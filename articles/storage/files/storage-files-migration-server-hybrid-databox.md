---
title: Migración de datos a Azure File Sync con Azure Data Box
description: Migre datos masivos sin conexión que sean compatibles con Azure File Sync. Evite conflictos de archivos y realice la puesta al día del recurso compartido de archivos con los cambios más recientes en el servidor para una migración a la nube sin tiempo de inactividad.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 06/01/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: 5b888ca7655f4de267ed89229559e4b0b1785f6c
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799495"
---
# <a name="migrate-data-offline-to-azure-file-sync-with-azure-data-box"></a>Migración de datos sin conexión a Azure File Sync con Azure Data Box

Este artículo de migración es uno de los varios que se aplican a las palabras clave Azure File Sync y Azure Data Box. Compruebe si este artículo se aplica a su escenario:

:::row:::
    :::column:::
        :::image type="content" source="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept.png" alt-text="Una presentación de tres pasos secuenciales descritos en esta guía de migración. La columna situada junto a la imagen los describe con detalle." lightbox="media/storage-files-migration-server-hybrid-databox/file-sync-data-box-concept-expanded.png":::
    :::column-end:::
    :::column:::
        > [!div class="checklist"]
        > * Origen de datos: Windows Server 2012 R2 o posterior donde Azure File Sync se instalará y apuntará al conjunto original de archivos.
        > * Ruta de migración: Windows Server 2012 R2 o posterior &rArr; Data Box &rArr; recurso compartido de archivos de Azure &rArr; sincronizar con la ubicación del archivo original de Windows Server
        > * Almacenamiento en caché de archivos en local: sí, el objetivo final es una implementación de Azure File Sync que sincronice los archivos desde donde están ahora. 
        :::column-end:::
:::row-end:::

Azure Data Box es una ruta de acceso viable para mover la mayor parte de los datos de Windows Server local a recursos compartidos de archivos de Azure independientes y luego, opcionalmente, agregar Azure File Sync en el servidor de origen original.

Hay diferentes rutas de migración disponibles; es importante seguir la correcta:

* Los datos se encuentran en Windows Server 2012 R2 o posterior y tiene previsto instalar AFS en ese servidor y sincronizar la ubicación original. En este escenario, no quiere cargar todos los archivos y usar Data Box en su lugar y, a continuación, usar la sincronización de archivos para los cambios en curso. Si este es su escenario, en este artículo se describe la ruta de migración.
* Tiene datos en un origen en el que *no* instalará o no puede instalar AFS. Un NAS (almacenamiento conectado a la red) por ejemplo o un servidor diferente. En su lugar, creará un nuevo servidor vacío y usará Azure File Sync en ese servidor. Si ese es su escenario, esta no es la guía de migración adecuada para usted. En su lugar, consulte: [Migración desde NAS a través de Data Box a Azure File Sync](../files/storage-files-migration-nas-hybrid-databox.md) o busque la mejor guía para su escenario en la página de [información general de la migración](../files/storage-files-migration-overview.md).
* Para todos los demás escenarios, consulte la [tabla de guías de migración de recursos compartidos de archivos de Azure](../files/storage-files-migration-overview.md). Esta página de información general proporciona un buen punto de partida para todos los escenarios de migración.
 
## <a name="migration-overview"></a>Información general sobre la migración

El proceso de migración consta de varias fases. Necesitará:
- Implemente cuentas de almacenamiento y recursos compartidos de archivos.
- Implemente uno o más dispositivos de Azure Data Box para mover los datos desde Windows Server 2012 R2 o posterior. 
- Configure Azure File Sync con carga autoritativa.

En las secciones siguientes se describen detalladamente las fases del proceso de migración.

> [!TIP]
> Si vuelve a este artículo, use la navegación del lado derecho de la pantalla para saltar a la fase de migración en la que se quedó.

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>Fase 1: Determinación del número de recursos compartidos de archivos de Azure que necesita

Con esta guía de migración, debe seguir usando el almacenamiento de conexión directa (DAS) local que contiene los archivos. Data Box se alimentará desde esa ubicación y Azure File Sync también se configurará en esa ubicación. NAS (Network Attached Storage) no funciona con esta ruta de acceso de migración.

Usted determina qué se sincroniza configurando *grupos de sincronización* de Azure File Sync, cada uno de los cuales determina dónde se sincroniza un conjunto de archivos. Cada grupo de sincronización tiene al menos una ubicación de servidor, denominada *punto de conexión de servidor*, y un recurso compartido de archivos de Azure, llamado *punto de conexión de nube*. 

Puede sincronizar rutas de acceso secundarias de un conjunto de archivos con cada uno de sus propios recursos compartidos de archivos de Azure. Esto significa configurar varios grupos de sincronización para cubrir completamente un conjunto de archivos. En el resto de la sección se describen las opciones. Si necesita reestructurar los datos, debe hacerlo como primer paso. A antes de continuar con esta guía, solicite una instancia de Data Box o sincronice la configuración. 

> [!CAUTION]
> Es imperativo que la estructura de sus archivos y carpetas sea como desea que sea a largo plazo, antes de comenzar la migración. Evite cualquier reestructuración innecesaria de carpetas durante la migración. Esto reducirá los efectos positivos del uso de Azure Data Box para el transporte masivo inicial de archivos a Azure.

[!INCLUDE [storage-files-migration-namespace-mapping](../../../includes/storage-files-migration-namespace-mapping.md)]

## <a name="phase-2-deploy-azure-storage-resources"></a>Fase 2: Implementación de recursos de almacenamiento de Azure

En esta fase, consulte la tabla de asignación de la fase 1 y úsela para aprovisionar el número correcto de cuentas de almacenamiento de Azure y recursos compartidos de archivos que contienen.

[!INCLUDE [storage-files-migration-provision-azfs](../../../includes/storage-files-migration-provision-azure-file-share.md)]

## <a name="phase-3-determine-how-many-azure-data-box-appliances-you-need"></a>Fase 3: Determinación del número de dispositivos de Azure Data Box que necesita

Inicie este paso solo después de haber finalizado la fase anterior. Los recursos de almacenamiento de Azure Storage (cuentas de almacenamiento y recursos compartidos de archivos) se deben crear en este momento. Al solicitar su Data Box, debe especificar las cuentas de almacenamiento a las que Data Box se mueve.

En esta fase, debe asignar los resultados del plan de migración de la fase anterior a los límites de las opciones de Data Box disponibles. Estas consideraciones le ayudarán a crear un plan para las opciones de Data Box que se van a elegir, así como cuántas necesitará para mover los recursos compartidos de NAS a recursos compartidos de archivos de Azure.

Para determinar el número de dispositivos que necesita y sus tipos, tenga en cuenta estos límites importantes:

* Cualquier dispositivo de Azure Data Box puede mover datos a un máximo de 10 cuentas de almacenamiento. 
* Cada opción de Data Box viene con su propia capacidad utilizable. Consulte [opciones de Data Box](#data-box-options).

Consulte el plan de migración para encontrar el número de cuentas de almacenamiento que ha decidido crear y los recursos compartidos en cada una de ellas. A continuación, examine el tamaño de cada uno de los recursos compartidos de NAS. La combinación de esta información le permitirá optimizar y decidir qué dispositivo debe enviar datos a las cuentas de almacenamiento. Dos dispositivos de Data Box pueden trasladar los archivos a la misma cuenta de almacenamiento, pero no divida el contenido de un solo recurso compartido de archivos en dos instancias de Data Box.

### <a name="data-box-options"></a>Opciones de Data Box

Para una migración estándar, elija una combinación de estas opciones de Data Box: 

* **Data Box Disk**.
  Data Box Disks: Microsoft le enviará entre uno y cinco discos SSD con una capacidad de 8 TiB cada uno, con un total de 40 TiB como máximo. La capacidad utilizable es aproximadamente un 20 % menor debido al cifrado y la sobrecarga del sistema de archivos. Para más información, consulte la [documentación de Data Box Disk](../../databox/data-box-disk-overview.md).
* **Data Box**.
  Esta opción es la más común. Microsoft le enviará un dispositivo de Data Box resistente que funciona de forma similar a un NAS. Tiene una capacidad utilizable de 80 TiB. Para más información, consulte la [documentación de Data Box](../../databox/data-box-overview.md).
* **Data Box Heavy**.
  Esta opción presenta un dispositivo Data Box resistente en ruedas, que funciona de forma similar a un NAS. Tiene una capacidad de 1 PiB. La capacidad utilizable es aproximadamente un 20 % menor debido al cifrado y la sobrecarga del sistema de archivos. Para más información, consulte la [documentación de Data Box Heavy](../../databox/data-box-heavy-overview.md).

## <a name="phase-4-copy-files-onto-your-data-box"></a>Fase 4: Copia de archivos en el dispositivo Data Box

Cuando llegue el dispositivo Data Box, debe colocarlo en la línea de visión del dispositivo NAS. Siga la documentación de establecimiento del tipo de Data Box que solicitó:

* [Configuración de Data Box](../../databox/data-box-quickstart-portal.md).
* [Configuración de Data Box Disk](../../databox/data-box-disk-quickstart-portal.md).
* [Configuración de Data Box Heavy](../../databox/data-box-heavy-quickstart-portal.md).

Dependiendo del tipo de Data Box, las herramientas de copia de Data Box podrían estar disponibles. En este punto, no se recomiendan para las migraciones a recursos compartidos de archivos de Azure porque no copian los archivos en el dispositivo Data Box con total fidelidad. En su lugar, use RoboCopy.

Cuando llegue el dispositivo Data Box, tendrá recursos compartidos de SMB aprovisionados previamente disponibles para cada cuenta de almacenamiento que especificó cuando lo solicitó.

* Si los archivos entran en un recurso compartido de archivos prémium de Azure, habrá un recurso compartido de SMB por cuenta de almacenamiento de "almacenamiento de archivos" prémium.
* Si los archivos entran en una cuenta de almacenamiento estándar, habrá tres recursos compartidos de SMB por cuenta de almacenamiento estándar (GPv1 y GPv2). Solo los recursos compartidos de archivos que terminan con `_AzFiles` son pertinentes para la migración. Omita los recursos compartidos de blob en bloques y en páginas.

Siga los pasos descritos en la documentación de Azure Data Box:

1. [Conexión a un dispositivo Data Box](../../databox/data-box-deploy-copy-data.md).
1. Copia de datos a un dispositivo Data Box.
1. [Preparación del dispositivo Data Box para cargarlo en Azure.](../../databox/data-box-deploy-picked-up.md)

La documentación vinculada de Data Box especifica un comando Robocopy. Ese comando no es adecuado para conservar la fidelidad completa de archivos y carpetas. En su lugar, use este comando:

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

## <a name="phase-5-deploy-the-azure-file-sync-cloud-resource"></a>Fase 5: Implementación del recurso de nube de Azure File Sync

Antes de continuar con esta guía, espere hasta que todos los archivos hayan llegado a los recursos compartidos de archivos de Azure correctos. El proceso de envío e ingesta de datos de Data Box llevará tiempo.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-6-deploy-the-azure-file-sync-agent"></a>Fase 6: Implementación del agente de Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-7-configure-azure-file-sync-on-the-existing-windows-server"></a>Fase 7: Configuración de Azure File Sync en el servidor de Windows Server existente

La instancia registrada de Windows Server local debe estar preparada y conectada a Internet para este proceso.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

:::row:::
    :::column:::
        [ ![Se muestra una sección de Azure Portal del asistente Crear un punto de conexión de servidor. Se resalta una casilla que corresponde al escenario de inicialización del recurso compartido de archivos de Azure con datos. Active esta casilla si conecta AFS a la misma ubicación local desde donde copió en Data Box antes.](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox.png)](media/storage-files-migration-server-hybrid-databox/enable-authoritative-upload-top-checkbox-expanded.png#lightbox)
    :::column-end:::
    :::column:::
        Una vez que se encuentra en el asistente **Crear un punto de conexión de servidor**, use la casilla proporcionada debajo de la ruta de acceso de la carpeta. Realice esta selección solamente si ha escrito una ruta de acceso que apunta a la misma estructura de archivos y carpetas que se puede encontrar en el recurso compartido de archivos de Azure (donde el dispositivo Data Box ha movido los archivos y las carpetas para este espacio de nombres). </br> </br> Si hay un error de coincidencia en la jerarquía de carpetas, eso se presentará como diferencias que no se pueden resolver automáticamente. Evitar desajustes o cualquier inversión en el proceso de Data Box se traducirá en un beneficio cero para usted. Todos los datos se eliminarán en el recurso compartido de archivos de Azure. Todos los datos tendrán que cargarse desde el servidor local. Las estructuras de directorio deben coincidir para obtener las ventajas de una migración masiva con Azure Data Box y una actualización sin problemas del recurso compartido en la nube con los cambios más recientes del servidor.
:::column-end:::
:::row-end:::

> [!NOTE]
> Al habilitar esta casilla se establecerá el modo **Sincronización inicial** en *Authoritatively overwrite files and folders in the Azure file share with content in this server’s path* (Sobrescribir de manera autorizada archivos y carpetas en el recurso compartido de archivos de Azure con contenido en la ruta de acceso de este servidor). Esta opción solo está disponible para el primer punto de conexión de servidor de un grupo de sincronización.

Una vez configurada la carga autoritativa para este nuevo punto de conexión de servidor, puede habilitar la nube por niveles si así lo desea.

La nube por niveles es la característica de Azure File Sync que permite al servidor local tener menos capacidad de almacenamiento de la que está almacenada en la nube pero disponer de todo el espacio de nombres. Los datos de interés local también se almacenan en caché para conseguir un rendimiento de acceso rápido. La nube por niveles es opcional. Puede establecerla individualmente para cada punto de conexión de Azure File Sync servidor. Use esta característica para lograr una superficie de almacenamiento fija en el entorno local, pero sin dejar de proporcionar a los usuarios una memoria caché de rendimiento local y almacenar datos más fríos en la nube.

Obtenga más información consultando la [información general de la nube por niveles](../file-sync/file-sync-cloud-tiering-overview.md) o eche un vistazo más de cerca a las diferentes [directivas de la nube por niveles](../file-sync/file-sync-cloud-tiering-policy.md) que puede usar para ajustar lo que se almacena en caché en niveles en el servidor local.

## <a name="complete-your-migration"></a>Completar la migración

Después de crear un punto de conexión de servidor, la sincronización funciona. Sin embargo, la sincronización debe enumerar (detectar) los archivos y las carpetas que se movieron a través de Azure Data Box al recurso compartido de archivos de Azure. Según el tamaño del espacio de nombres, puede pasar mucho tiempo antes de que los cambios más recientes del servidor se sincronicen en la nube. Los usuarios no se verán afectados y podrán seguir trabajando con los datos en el servidor. Esta estrategia logra una migración a la nube sin tiempo de inactividad.

Para todos los recursos compartidos de archivos de Azure o las ubicaciones de servidor que necesita configurar para la sincronización, repita los pasos para crear grupos de sincronización y agregar las carpetas de servidor correspondientes como puntos de conexión de servidor. Ha usado Azure Data Box para mover los archivos a varios recursos compartidos de archivos de Azure. La migración se completa una vez creados todos los puntos de conexión de servidor que conectan los datos locales a estos recursos compartidos de archivos de Azure.

## <a name="next-steps"></a>Pasos siguientes

Hay más información sobre los recursos compartidos de archivos de Azure y Azure File Sync. Los artículos siguientes lo ayudarán a comprender las opciones avanzadas y los procedimientos recomendados. También proporcionan ayuda para solucionar problemas. Estos artículos contienen vínculos a la [documentación del recurso compartido de archivos de Azure](storage-files-introduction.md) cuando corresponda.

* [Información general acerca de la migración](storage-files-migration-overview.md)
* [Planeamiento de una implementación de Azure File Sync](../file-sync/file-sync-planning.md)
* [Creación de un recurso compartido de archivos](storage-how-to-create-file-share.md)
* [Solución de problemas de Azure File Sync](../file-sync/file-sync-troubleshoot.md)
