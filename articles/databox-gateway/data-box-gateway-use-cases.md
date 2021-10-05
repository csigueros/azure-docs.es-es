---
title: Casos de uso de Microsoft Azure Data Box Gateway | Microsoft Docs
description: Describe los casos de uso de Azure Data Box Gateway, una solución de almacenamiento de aplicaciones virtuales que permite transferir datos a Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 09/28/2021
ms.author: alkohli
ms.openlocfilehash: eec1c595119e225468bdbbe76f506b5b330a1beb
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129212406"
---
# <a name="use-cases-for-azure-data-box-gateway"></a>Casos de uso de Azure Data Box Gateway

Azure Data Box Gateway es un dispositivo de puerta de enlace de almacenamiento en la nube que reside en el entorno local y que envía imágenes, elementos multimedia y otros datos a Azure. Esta puerta de enlace de almacenamiento en la nube es una máquina virtual aprovisionada en el hipervisor. Puede escribir datos en este dispositivo virtual mediante los protocolos NFS y SMB que este, posteriormente, envía a Azure. En este artículo se proporciona una descripción detallada de los escenarios en los que puede implementar este dispositivo.

Use Data Box Gateway para los escenarios siguientes:

- Para ingerir continuamente cantidades masivas de datos.
- Para el archivado de datos en la nube de forma segura y eficaz.
- Para la transferencia de datos incremental en la red después de realizar una transferencia masiva inicial con Data Box.

Cada uno de estos escenarios se describe en detalle en las secciones que aparecen a continuación.


## <a name="continuous-data-ingestion"></a>Ingesta de datos continua

Una de las principales ventajas de Data Box Gateway es la posibilidad de ingerir continuamente datos en el dispositivo para copiarlos en la nube, independientemente del tamaño de estos.

Como los datos se escriben en el dispositivo de puerta de enlace, el dispositivo carga los datos en Azure Storage. El dispositivo administra automáticamente el almacenamiento eliminando los archivos localmente mientras conserva los metadatos cuando alcanza un umbral determinado. Mantener una copia local de los metadatos permite que el dispositivo de puerta de enlace solo cargue los cambios cuando el archivo se actualiza. Los datos que se cargan en el dispositivo de puerta de enlace deben ser como lo indican las directrices de [Advertencias al cargar los datos](data-box-gateway-limits.md#data-upload-caveats).

Cuando el dispositivo se llena de datos, empieza a limitar la velocidad de entrada lo necesario para que se iguale a la velocidad a la que los datos se cargan en la nube. Para supervisar la ingesta continua en el dispositivo, puede usar alertas. Estas alertas se generan cuando se inicia la limitación y desaparecen cuando esta se detiene.

## <a name="cloud-archival-of-data"></a>Archivado de datos en la nube

Use Data Box Gateway cuando desee retener los datos a largo plazo en la nube. Puede usar el nivel de archivo del almacenamiento para la retención a largo plazo.

El nivel de archivo está optimizado para almacenar datos a los que raramente se accede durante al menos 180 días. El nivel de archivo ofrece los costos de almacenamiento más bajos pero los costos más altos de acceso. Para más información, vaya a [Nivel de acceso de archivo](../storage/blobs/storage-blob-storage-tiers.md#archive-access-tier).

### <a name="move-data-to-the-archive-tier"></a>Traslado de datos al nivel de archivo

Antes de comenzar, asegúrese de que tiene un dispositivo de Data Box Gateway en ejecución. Siga los pasos descritos en [Tutorial: Preparación de la implementación de Azure Data Box Gateway](data-box-gateway-deploy-prep.md) y vaya al siguiente tutorial hasta que tenga un dispositivo operativo.

- Use el dispositivo de Data Box Gateway para cargar datos en Azure mediante el procedimiento de transferencia habitual como se describe en [Transferencia de datos con Azure Data Box Gateway](data-box-gateway-deploy-add-shares.md).
- Después de cargar los datos, tendrá que moverlos al nivel de archivo. Puede establecer el nivel de blob de dos maneras: mediante un script de Azure PowerShell o una directiva de administración del ciclo de vida de Azure Storage.  
    - Si va a utilizar Azure PowerShell, siga estos [pasos](../databox/data-box-how-to-set-data-tier.md#use-azure-powershell-to-set-the-blob-tier) para mover los datos al nivel de archivo.
    - Si va a utilizar la administración del ciclo de vida de Azure, siga estos pasos para mover los datos al nivel de archivo.
        - Use el nivel de archivo a través del [servicio de administración del ciclo de vida de los blobs](../storage/blobs/lifecycle-management-overview.md).
        - Use la siguiente directiva para el [archivado de datos en la ingesta](../storage/blobs/lifecycle-management-overview.md#archive-data-after-ingest).
- Una vez que los blobs se marcan como pertenecientes al nivel de archivo, la puerta de enlace ya no puede modificarlos a menos que se trasladen a un nivel de acceso frecuente o a uno de acceso poco frecuente. Si el archivo está en el almacenamiento local, ningún cambio que se realice en la copia local (incluidas las eliminaciones) se cargará en el nivel de archivo.
- Para leer los datos del almacenamiento de archivos, debe rehidratar los datos cambiando el nivel de blob a un nivel de acceso frecuente o a otro de acceso poco frecuente. La [actualización del recurso compartido](data-box-gateway-manage-shares.md#refresh-shares) en la puerta de enlace no rehidratará el blob.

Para más información, consulte [Administración del ciclo de vida de Azure Blob Storage](../storage/blobs/lifecycle-management-overview.md).

## <a name="initial-bulk-transfer-followed-by-incremental-transfer"></a>Transferencia masiva inicial seguida de transferencia incremental

Use Data Box y Data Box Gateway cuando desee realizar una carga masiva de una gran cantidad de datos seguida de transferencias incrementales. Use Data Box para la transferencia masiva en un modo sin conexión (inicialización) y Data Box Gateway para las transferencias incrementales (fuente continua) a través de la red.

### <a name="seed-the-data-with-data-box"></a>Inicialización de los datos con Data Box

Siga estos pasos para copiar los datos en Data Box y cargarlos en Azure Storage.

1. [Pida su Data Box](../databox/data-box-deploy-ordered.md).
2. [Configúrelo](../databox/data-box-deploy-set-up.md).
3. [Copie los datos en Data Box mediante SMB](../databox/data-box-deploy-copy-data.md).
4. [Devuelva el Data Box y compruebe la carga de datos en Azure](../databox/data-box-deploy-picked-up.md).
5. Una vez completada la carga de datos en Azure, todos los datos deben estar en contenedores de almacenamiento de Azure. En la cuenta de almacenamiento de Data Box, vaya al contenedor de blobs (y archivos) para asegurarse de que se copian todos los datos. Anote el nombre del contenedor, ya que usará este nombre más adelante. Por ejemplo, en la siguiente captura de pantalla el contenedor `databox` se usará para la transferencia incremental.

    ![Contenedor con datos en Data Box](media/data-box-gateway-use-cases/data-container.png)

Esta transferencia masiva completa la fase de inicialización.

### <a name="ongoing-feed-with-data-box-gateway"></a>Fuente continua con Data Box Gateway

Siga estos pasos para la ingesta continua con Data Box Gateway. 

1. Cree un recurso compartido en la nube en Data Box Gateway. Este recurso compartido carga automáticamente los datos a la cuenta de Azure Storage. Vaya a **Recursos compartidos** en el recurso de Data Box Gateway y haga clic en **+ Agregar recurso compartido**.

    ![Hacer clic en +Agregar recurso compartido](media/data-box-gateway-use-cases/add-share.png)

2. Asegúrese de que este recurso compartido se asigna al contenedor que contiene los datos inicializados. En **Seleccionar el contenedor de blobs**, elija **Usar existente** y vaya al contenedor donde se han transferido los datos de Data Box.

    ![Configuración del recurso compartido](media/data-box-gateway-use-cases/share-settings-select-existing-container.png)

3. Una vez creado el recurso compartido, actualícelo. Esta operación actualiza el recurso compartido local con el contenido de Azure.

    ![Actualizar recurso compartido](media/data-box-gateway-use-cases/refresh-share.png)

    Cuando se sincronice el recurso compartido, Data Box Gateway cargará los cambios incrementales si se han modificado los archivos en el cliente.

## <a name="next-steps"></a>Pasos siguientes

- Revise [los requisitos del sistema Data Box Gateway](data-box-gateway-system-requirements.md).
- Información acerca de los [límites de Data Box Gateway](data-box-gateway-limits.md).
- Implemente [Azure Data Box Gateway](data-box-gateway-deploy-prep.md) en Azure Portal.
