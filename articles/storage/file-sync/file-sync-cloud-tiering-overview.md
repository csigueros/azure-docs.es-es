---
title: Descripción de la nube por niveles de Azure File Sync | Microsoft Docs
description: Conozca la nube por niveles, una característica opcional de Azure File Sync. Los archivos a los que se accede con frecuencia se almacenan en caché localmente en el servidor; otros están organizados por niveles en Azure Files.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 1f0ae8b5a4ac2572719340020695a5bcd6b0d8a2
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128601356"
---
# <a name="cloud-tiering-overview"></a>Información general de nube por niveles

La nube por niveles, una característica opcional de Azure File Sync, reduce la cantidad de almacenamiento local necesario al tiempo que mantiene el rendimiento de un servidor de archivos local.

Cuando está habilitada, esta característica solo almacena los archivos de acceso frecuente (activos) en el servidor local. Los archivos de acceso poco frecuente (pasivos) se reparten entre el espacio de nombres (estructura de archivos y carpetas) y en el contenido del archivo. El espacio de nombres se almacena localmente y el contenido del archivo se almacena en un recurso compartido de archivos de Azure en la nube.

Cuando un usuario abre un archivo almacenado por niveles, Azure File Sync recupera completamente los datos del archivo del recurso compartido de archivos de Azure.

## <a name="how-cloud-tiering-works"></a>Funcionamiento de la nube por niveles

### <a name="cloud-tiering-policies"></a>Directivas de nube por niveles

Cuando se habilita la nube por niveles, hay dos directivas que se pueden configurar para informar a Azure File Sync sobre cuándo se deben almacenar por niveles los archivos pasivos: la **directiva de espacio disponible del volumen** y la **directiva de fecha**.

#### <a name="volume-free-space-policy"></a>Directiva de espacio disponible del volumen

La **directiva de espacio disponible del volumen** indica a Azure File Sync que organice por niveles los archivos pasivos en la nube cuando se ocupa una determinada cantidad de espacio en el disco local.

Por ejemplo, si la capacidad del disco local es de 200 GiB y quiere que al menos 40 permanezcan siempre libres, debe establecer la directiva de espacio disponible del volumen en un 20 %. El espacio disponible en el volumen se aplica con respecto al volumen, y no a los directorios individuales o a los puntos de conexión de servidor.

#### <a name="date-policy"></a>Directiva de fecha

Con la **directiva de fecha**, los archivos pasivos se organizan por niveles en la nube si no se ha tenido acceso a ellos (es decir, no se han leído ni se ha escrito en ellos) durante un número de días x. Por ejemplo, si observa que los archivos a los que no se ha accedido hace más de 15 días son normalmente archivos de archivado, debe establecer la directiva de fecha en 15 días.

Para ver más ejemplos de cómo funcionan conjuntamente la directiva de fecha y la directiva de espacio disponible del volumen, consulte [Selección de las directivas de nube por niveles de Azure File Sync](file-sync-choose-cloud-tiering-policies.md).

### <a name="windows-server-data-deduplication"></a>Desduplicación de datos de Windows Server

La desduplicación de datos se admite en volúmenes que tienen habilitada la nube por niveles a partir de Windows Server 2016. Para más información, consulte [Planeamiento de una implementación de Azure File Sync](file-sync-planning.md#data-deduplication).

### <a name="cloud-tiering-heatmap"></a>Mapa término de la nube por niveles

Azure File Sync supervisa el acceso a archivos (operaciones de lectura y escritura) a lo largo del tiempo y, en función de lo reciente y frecuente que sea, asigna una puntuación térmica a cada archivo. Estas puntuaciones se usan para crear un "mapa térmico" del espacio de nombres en cada punto de conexión de servidor. Este mapa térmico es una lista de todos los archivos de sincronización en una ubicación que tiene habilitada la nube por niveles, ordenados por su puntuación térmica. Los archivos a los que se accede con frecuencia se consideran activos, mientras que los archivos que apenas se han modificado y a los que no se ha tenido acceso durante algún tiempo se consideran pasivos.

Para determinar la posición relativa de un archivo en ese mapa térmico, el sistema usa el valor máximo de sus marcas de tiempo, en el orden siguiente: MAX(Hora del último acceso, Hora de última modificación, Hora de creación).

Normalmente, la hora del último acceso está registrada y disponible. Sin embargo, cuando se crea un punto de conexión de servidor y está habilitada la nube por niveles, no ha pasado tiempo suficiente para observar el acceso al archivo. Si no existe una hora de último acceso válida, se usa la hora de la última modificación para evaluar la posición relativa en el mapa térmico.

La directiva de fecha funciona de la misma manera. Sin una hora de último acceso, la directiva de fecha actúa en función de la hora de la última modificación. Si esa hora no estuviera disponible, se pasa a la hora de creación del archivo. Con el tiempo, el sistema observa más solicitudes de acceso al archivo y empieza a usar automáticamente la hora de último acceso que ha registrado.

> [!NOTE]
> La nube por niveles no depende de la característica NTFS para el registro de la hora del último acceso. Esta característica NTFS está desactivada de forma predeterminada y, debido a consideraciones de rendimiento, no se recomienda habilitarla manualmente. La nube por niveles registra la hora del último acceso por separado.

### <a name="proactive-recalling"></a>Recuperación proactiva

Cuando se crea o se modifica un archivo, puede recuperarlo de forma proactiva en los servidores que especifique. La recuperación proactiva permite que el archivo nuevo o modificado esté fácilmente disponible para su consumo en cada servidor especificado.

Por ejemplo, una empresa distribuida por todo el mundo tiene sucursales en Estados Unidos y en la India. Por la mañana (hora de EE. UU.), los trabajadores de la información crean una carpeta y los archivos de un nuevo proyecto en el que trabajan todo el día. Azure File Sync sincronizará la carpeta y los archivos con el recurso compartido de archivos de Azure (punto de conexión de nube). Los trabajadores de la información de la India seguirán trabajando en el proyecto en su zona horaria. Cuando lleguen por la mañana, el servidor local de Azure File Sync habilitado en la India debe tener disponibles estos nuevos archivos a nivel local, de modo que el equipo de la India pueda trabajar eficazmente fuera de una caché local. Al habilitar este modo, se evita que el acceso inicial al archivo sea más lento debido a la recuperación a petición y permite que el servidor recupere los archivos de forma proactiva en cuanto se han modificado o creado en el recurso compartido de archivos de Azure.

Si los archivos recuperados en el servidor no son necesarios localmente, la recuperación innecesaria puede aumentar el tráfico de salida y los costos. Por tanto, habilite la recuperación proactiva solo cuando sepa que rellenar previamente la caché de un servidor con los cambios recientes en la nube tendrá un efecto positivo en los usuarios o las aplicaciones que usan los archivos de dicho servidor.

Habilitar la recuperación proactiva también puede aumentar el uso de ancho de banda en el servidor y puede hacer que otro contenido relativamente nuevo en el servidor local se organice por niveles a la fuerza debido al aumento de los archivos que se recuperan. A su vez, la organización por niveles puede dar lugar a más recuperaciones si los servidores consideran que los archivos almacenados por niveles son activos.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Imagen que muestra el comportamiento de descarga del recurso compartido de archivos de Azure para un punto de conexión de servidor actualmente en vigor y un botón para abrir un menú que permite cambiarlo.":::

Para más información sobre la recuperación proactiva, consulte [Implementación de Azure File Sync](file-sync-deployment-guide.md#proactively-recall-new-and-changed-files-from-an-azure-file-share).

## <a name="tiered-vs-locally-cached-file-behavior"></a>Comportamiento de los archivos almacenados en la caché local o por niveles

La nube por niveles es la separación entre el espacio de nombres (la jerarquía de archivos y carpetas, así como las propiedades de archivo) y el contenido del archivo.

#### <a name="tiered-file"></a>Archivos almacenados por niveles

En el caso de los archivos almacenados por niveles, el tamaño en disco es cero, ya que el contenido del archivo propiamente dicho no se almacena localmente. Cuando un archivo está almacenado por niveles, el filtro del sistema de archivos de Azure File Sync (StorageSync.sys) sustituye al archivo localmente por un puntero (punto de repetición de análisis). El punto de repetición de análisis representa una dirección URL al archivo en el recurso compartido de archivos de Azure. Un archivo con niveles tiene los atributos “sin conexión” y FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS establecidos en NTFS para que las aplicaciones de terceros puedan identificar de forma segura archivos con niveles.

![Captura de pantalla de las propiedades de un archivo cuando está almacenado por niveles (solo el espacio de nombres).](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-2.png)

#### <a name="locally-cached-file"></a>Archivo almacenado en la caché local

Por otro lado, en el caso de un archivo almacenado en un servidor de archivos local, el tamaño en disco es aproximadamente igual al tamaño lógico del archivo, ya que todo el archivo (atributos + contenido del archivo) se almacena localmente.

![Captura de pantalla de las propiedades de un archivo cuando no está almacenado por niveles (espacio de nombres + contenido del archivo).](media/storage-sync-cloud-tiering-overview/cloud-tiering-overview-1.png)

También es posible que un archivo se encuentre parcialmente organizado en niveles (o parcialmente recuperado). En el caso de un archivo parcialmente organizado en niveles, solo parte del archivo se almacena en el disco. Es posible que tenga archivos parcialmente recuperados en el volumen si las aplicaciones que admiten el acceso de streaming a los archivos los leen parcialmente. Algunos ejemplos son reproductores multimedia y utilidades zip. Azure File Sync es eficaz y solo recupera la información solicitada del recurso compartido de archivos de Azure conectado.

> [!NOTE]
> El tamaño representa el tamaño lógico del archivo. El tamaño en disco representa el tamaño físico del flujo de archivos que se almacena en el disco.

## <a name="next-steps"></a>Pasos siguientes

- [Selección de directivas de nube por niveles de Azure File Sync](file-sync-choose-cloud-tiering-policies.md)
- [Planeamiento de una implementación de Azure File Sync](file-sync-planning.md)
