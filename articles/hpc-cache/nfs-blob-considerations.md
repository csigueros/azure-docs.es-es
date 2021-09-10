---
title: Uso de Blob Storage NFS con Azure HPC Cache
description: Se describen los procedimientos y las limitaciones al usar Blob Storage ADLS-NFS con Azure HPC Cache.
author: ekpgh
ms.service: hpc-cache
ms.topic: how-to
ms.date: 07/12/2021
ms.author: v-erkel
ms.openlocfilehash: 70b1dc3e2de6c70a6b59aa739a9bed254295a4f9
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114293439"
---
# <a name="use-nfs-mounted-blob-storage-with-azure-hpc-cache"></a>Uso de Blob Storage montado en NFS con Azure HPC Cache

Puede usar contenedores de blobs montados en NFS con Azure HPC Cache. Obtenga más información sobre la [compatibilidad de Azure Blob Storage con el protocolo NFS 3.0](../storage/blobs/network-file-system-protocol-support.md) en el sitio web de documentación de Blob Storage.

Azure HPC Cache almacenamiento de blobs compatible con NFS en su tipo de destino de almacenamiento ADLS-NFS. Estos destinos de almacenamiento son similares a los destinos de almacenamiento NFS normales, pero también hay cierta superposición con los destinos normales de Azure Blob Storage.

En este artículo se explican las estrategias y limitaciones que debe conocer al usar destinos de almacenamiento ADLS-NFS.

También debe leer la documentación sobre blobs NFS, en particular las siguientes secciones, en las que se describen escenarios compatibles e incompatibles:

* [Descripción de las características](../storage/blobs/network-file-system-protocol-support.md)
* [Consideraciones de rendimiento](../storage/blobs/network-file-system-protocol-support-performance.md)
* [Limitaciones y problemas conocidos](../storage/blobs/network-file-system-protocol-known-issues.md)

## <a name="understand-consistency-requirements"></a>Descripción de los requisitos de coherencia

HPC Cache exige una alta coherencia a los destinos de almacenamiento ADLS-NFS. De forma predeterminada, el almacenamiento de blobs compatible con NFS no actualiza estrictamente metadatos de archivos, lo que impide a HPC Cache comparar con precisión las versiones de archivo.

Para evitar este problema, Azure HPC Cache deshabilita automáticamente el almacenamiento en caché de atributos NFS en cualquier contenedor de blobs compatible con NFS que se utilice como destino de almacenamiento.

Esta configuración se conserva durante la vigencia del contenedor, aunque se elimine de la memoria caché.

## <a name="pre-load-data-with-nfs-protocol"></a>Precarga de datos con el protocolo NFS
<!-- cross-referenced from hpc-cache-ingest.md and here -->

En un contenedor de blobs compatible con NFS, *un archivo solo se puede editar mediante el mismo protocolo que se usó al crearse*. Es decir, si usa la API de REST de Azure para rellenar un contenedor, no puede usar NFS para actualizar los archivos. Dado que Azure HPC Cache solo usa NFS, no puede editar archivos creados con la API de REST de Azure. (Más información sobre [problemas conocidos con las API de almacenamiento de blobs](../storage/blobs/network-file-system-protocol-known-issues.md#blob-storage-apis))

El hecho de que el contenedor esté vacío o que los archivos se hayan creado mediante NFS no supone ningún problema para la caché.

Si los archivos de su contenedor se han creado con la API de REST de Azure Blob Storage en lugar de NFS, las acciones de Azure HPC Cache en los archivos originales están limitadas a las siguientes:

* Mostrar el archivo en un directorio.
* Leer el archivo (y mantenerlo en la caché para lecturas posteriores).
* Eliminar el archivo.
* Vaciar el archivo (truncarlo a 0).
* Guardar una copia del archivo. La copia se marca como archivo creado con NFS y se puede editar usando este protocolo.

**Azure HPC Cache no puede editar el contenido de un archivo creado mediante REST.** Por lo tanto, la caché no puede volver a guardar un archivo modificado desde un cliente en el destino de almacenamiento.

Es importante comprender esta limitación, ya que puede provocar problemas de integridad de datos si usa modelos de uso de almacenamiento en caché de lectura y escritura en archivos que no se hayan creado con NFS.

> [!TIP]
> Obtenga más información sobre el almacenamiento en caché de lectura y escritura en [Descripción de los modelos de uso de caché](cache-usage-models.md).

### <a name="write-caching-scenarios"></a>Escenarios de almacenamiento en caché de escritura

Estos modelos de uso de caché incluyen almacenamiento en caché de escritura:

* **Mayor que el 15 % de textos**
* **Mayor que el 15 % de las escrituras, comprobando si hay cambios en el servidor de copia de seguridad cada 30 segundos**
* **Mayor que el 15 % de las escrituras, comprobando si hay cambios en el servidor de copia de seguridad cada 60 segundos**
* **Mayor que el 15 % de las escrituras, reescribiendo en el servidor cada 30 segundos**

Los modelos de uso de almacenamiento en caché de escritura solo se deben usar en archivos creados con NFS.

Si intenta usar el almacenamiento en caché de escritura en archivos creados con REST, los cambios realizados en los archivos podrían perderse. El motivo es que la caché no intenta guardar inmediatamente los cambios de archivos en el contenedor de almacenamiento.

A continuación se explica cómo se ponen en riesgo los datos al intentar almacenar en caché escrituras en archivos creados con REST:

1. La caché acepta las modificaciones de clientes y devuelve un mensaje de confirmación con cada cambio.
1. La caché mantiene el archivo modificado en su almacenamiento y espera a que se realicen más cambios.
1. Transcurrido un tiempo, la caché intenta guardar el archivo cambiado en el contenedor de back-end. Llegado a este punto, recibirá un mensaje de error, ya que está intentando escribir con NFS en un archivo creado mediante REST.

   Ya es demasiado tarde para indicar al equipo cliente que sus cambios no se han aceptado, y la caché no tiene forma de actualizar el archivo original. Por lo tanto, se perderán los cambios de los clientes.

### <a name="read-caching-scenarios"></a>Escenarios de almacenamiento en caché de lectura

Los escenarios de almacenamiento en caché de lectura son adecuados para archivos creados con NFS o la API de REST de Azure Blob Storage.

Estos modelos de uso solo utilizan almacenamiento en caché de lectura:

* **Lectura de textos densos y poco frecuentes**
* **Los clientes escriben en el destino NFS omitiendo la caché**
* **Lectura intensiva, comprobación del servidor de copia de seguridad cada 3 horas**

Puede utilizar estos modelos de uso con archivos creados mediante la API de REST o NFS. Las escrituras con NFS que se envíen desde un cliente al contenedor de back-end seguirán generando errores, pero lo harán al instante y devolverán un mensaje de error al cliente.

Un flujo de trabajo de almacenamiento en caché de lectura puede seguir incluyendo cambios en archivos, siempre y cuando estos no se almacenen en caché. Por ejemplo, los clientes pueden acceder a archivos del contenedor y escribir sus cambios como un archivo nuevo, o bien guardar archivos modificados en otra ubicación.

## <a name="recognize-network-lock-manager-nlm-limitations"></a>Reconocimiento de las limitaciones de Network Lock Manager (NLM)

Los contenedores de blobs compatibles con NFS no admiten Network Lock Manager (NLM), un protocolo NFS que suele usarse para proteger archivos frente a conflictos.

Si el flujo de trabajo de NFS se ha escrito originalmente para sistemas de almacenamiento de hardware, sus aplicaciones cliente podrían incluir solicitudes NLM. Para evitar esta limitación al mover su proceso a almacenamiento de blobs compatible con NFS, asegúrese de que sus clientes deshabiliten NLM cuando monten la caché.

Para deshabilitar NLM, use la opción ``-o nolock`` en el comando ``mount`` de los clientes. Esta opción impide que los clientes soliciten bloqueos NLM y reciban errores como respuesta. La opción ``nolock`` se implementa de forma diferente en distintos sistemas operativos; compruebe la documentación del sistema operativo del cliente (man 5 nfs) para más información.

## <a name="streamline-writes-to-nfs-enabled-containers-with-hpc-cache"></a>Optimización de las escrituras en contenedores compatibles con NFS mediante HPC Cache

Azure HPC Cache puede contribuir a mejorar el rendimiento de una carga de trabajo que incluya la escritura de cambios en un destino de almacenamiento ADLS-NFS.

> [!NOTE]
> Si desea modificar los archivos de un contenedor de almacenamiento ADLS-NFS a través de Azure HPC Cache, debe usar NFS para rellenarlo.

Una de las limitaciones que se describen en el [artículo sobre consideraciones de rendimiento](../storage/blobs/network-file-system-protocol-support-performance.md) de blobs compatibles con NFS es que el almacenamiento ADLS-NFS no es muy eficaz a la hora de sobrescribir archivos ya existentes. Si usa Azure HPC Cache con blobs montados en NFS, la caché controla las reescrituras intermitentes a medida que los clientes modifican un archivo activo. La latencia de la escritura de un archivo en el contenedor de back-end se oculta a los clientes.

Tenga presente las limitaciones que se explicaron anteriormente en [Precarga de datos con el protocolo NFS](#pre-load-data-with-nfs-protocol).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre los [requisitos previos de los destinos de almacenamiento ADLS-NFS](hpc-cache-prerequisites.md#nfs-mounted-blob-adls-nfs-storage-requirements).
* Cree una [cuenta de almacenamiento de blobs compatible con NFS](../storage/blobs/network-file-system-protocol-support-how-to.md).
