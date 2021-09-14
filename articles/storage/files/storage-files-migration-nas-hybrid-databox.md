---
title: Migración de NAS local a Azure File Sync mediante Data Box
description: Obtenga información sobre cómo migrar archivos de una ubicación de almacenamiento conectado a la red (NAS) local a una implementación de nube híbrida con Azure File Sync mediante Azure Data Box.
author: fauhse
ms.service: storage
ms.topic: how-to
ms.date: 03/5/2021
ms.author: fauhse
ms.subservice: files
ms.openlocfilehash: fb7132e0a7b9dc59ac6b047d431acf0e740aba0a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123430835"
---
# <a name="use-data-box-to-migrate-from-network-attached-storage-nas-to-a-hybrid-cloud-deployment-by-using-azure-file-sync"></a>Uso de Data Box para migrar de un almacenamiento conectado a la red (NAS) a una implementación de nube híbrida con Azure File Sync

Este artículo de migración es uno de los que se aplican a las palabras clave NAS, Azure File Sync y Azure Data Box. Compruebe si este artículo se aplica a su escenario:

> [!div class="checklist"]
> * Origen de datos: almacenamiento conectado a la red (NAS)
> * Ruta de migración: NAS &rArr; Data Box &rArr; recurso compartido de archivos de Azure &rArr; sincronización con Windows Server
> * Almacenamiento en caché de archivos locales: sí, el objetivo final es una implementación de Azure File Sync

Si el escenario es diferente, examine la [tabla de guías de migración](storage-files-migration-overview.md#migration-guides).

Azure File Sync funciona en ubicaciones de almacenamiento de conexión directa (DAS). No admite la sincronización con ubicaciones de almacenamiento conectado a la red (NAS).
Por esta razón, debe migrar los archivos. Este artículo le guía a través del planeamiento y la implementación de esta migración.

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="migration-goals"></a>Objetivos de la migración

El objetivo es trasladar los recursos compartidos que tiene en el dispositivo NAS a Windows Server. Después, usará Azure File Sync para una implementación de nube híbrida. Esta migración se debe realizar de forma que garantice la integridad de los datos de producción y la disponibilidad durante la migración. Esta última requiere que el tiempo de inactividad sea mínimo, para cumplir o solo superar ligeramente las ventanas de mantenimiento regulares.

## <a name="migration-overview"></a>Información general sobre la migración

El proceso de migración consta de varias fases. Necesitará:
- Implementar cuentas de almacenamiento y recursos compartidos de archivos de Azure
- Implementar un equipo local que ejecute Windows Server 
- Configure Azure File Sync.
- Migrar archivos mediante Robocopy
- Realizar la transición

En las secciones siguientes se describen detalladamente las fases del proceso de migración.

> [!TIP]
> Si vuelve a este artículo, use la navegación del lado derecho de la pantalla para saltar a la fase de migración en la que se quedó.

## <a name="phase-1-determine-how-many-azure-file-shares-you-need"></a>Fase 1: Determinación del número de recursos compartidos de archivos de Azure que necesita

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

## <a name="phase-4-provision-a-suitable-windows-server-instance-on-premises"></a>Fase 4: Aprovisionamiento de una instancia de Windows Server adecuada en el entorno local

Mientras espera a que lleguen los dispositivos Azure Data Box, puede empezar a revisar las necesidades de una o más instancias de Windows Server que va a usar con Azure File Sync.

* Cree una instancia de Windows Server 2019 (como mínimo, Windows Server 2012 R2) como una máquina virtual o un servidor físico. También se admite un clúster de conmutación por error de Windows Server.
* Aprovisione o agregue almacenamiento de conexión directa (DAS, en lugar de NAS, que no se admite).

La configuración de recursos (de proceso y RAM) de la instancia de Windows Server que implemente depende principalmente del número de archivos y carpetas que se van a sincronizar. Se recomienda una configuración de rendimiento más alto si tiene problemas.

[Aprenda a cambiar el tamaño de una instancia de Windows Server según el número de elementos que necesite sincronizar.](../file-sync/file-sync-planning.md#recommended-system-resources)

> [!NOTE]
> El artículo vinculado anteriormente incluye una tabla con un intervalo de memoria del servidor (RAM). Puede usar los números del extremo inferior del intervalo para el servidor, pero probablemente la sincronización inicial tardará mucho más.

## <a name="phase-5-copy-files-onto-your-data-box"></a>Fase 5: Copia de archivos en el dispositivo Data Box

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


## <a name="phase-6-deploy-the-azure-file-sync-cloud-resource"></a>Fase 6: Implementación del recurso de nube de Azure File Sync

Antes de continuar con esta guía, espere hasta que todos los archivos hayan llegado a los recursos compartidos de archivos de Azure correctos. El proceso de envío e ingesta de datos de Data Box llevará tiempo.

[!INCLUDE [storage-files-migration-deploy-afs-sss](../../../includes/storage-files-migration-deploy-azure-file-sync-storage-sync-service.md)]

## <a name="phase-7-deploy-the-azure-file-sync-agent"></a>Fase 7: Implementación del agente de Azure File Sync

[!INCLUDE [storage-files-migration-deploy-afs-agent](../../../includes/storage-files-migration-deploy-azure-file-sync-agent.md)]

## <a name="phase-8-configure-azure-file-sync-on-the-windows-server-instance"></a>Fase 8: Configuración de Azure File Sync en la instancia de Windows Server

La instancia registrada de Windows Server local debe estar preparada y conectada a Internet para este proceso.

[!INCLUDE [storage-files-migration-configure-sync](../../../includes/storage-files-migration-configure-sync.md)]

Active la característica de nube por niveles y seleccione **Namespace only** (Solo espacio de nombres) en la sección de descarga inicial.

> [!IMPORTANT]
> La nube por niveles es la característica de Azure File Sync que permite al servidor local tener menos capacidad de almacenamiento de la que está almacenada en la nube pero disponer de todo el espacio de nombres. Los datos de interés local también se almacenan en caché para conseguir un rendimiento de acceso rápido. La nube por niveles es opcional. Puede establecerla individualmente para cada punto de conexión de Azure File Sync servidor. Debe usar esta característica si no tiene suficiente capacidad de disco local en una instancia de Windows Server para contener todos los datos en la nube y si quiere evitar la descarga de todos los datos de la nube.

Para todos los recursos compartidos de archivos de Azure o las ubicaciones de servidor que necesita configurar para la sincronización, repita los pasos para crear grupos de sincronización y agregar las carpetas de servidor correspondientes como puntos de conexión de servidor. Espere hasta que se complete la sincronización del espacio de nombres. En la sección siguiente se explica cómo puede asegurarse de que la sincronización se haya completado.

> [!NOTE]
> Después de crear un punto de conexión de servidor, la sincronización funciona. Aun así, la sincronización debe enumerar (detectar) los archivos y las carpetas que se movieron a través de Data Box al recurso compartido de archivos de Azure. Según el tamaño del espacio de nombres, puede pasar mucho tiempo antes de que el espacio de nombres de la nube aparezca en el servidor.

## <a name="phase-9-wait-for-the-namespace-to-fully-appear-on-the-server"></a>Fase 9: Espera para que el espacio de nombres aparezca por completo en el servidor

Antes de continuar con los pasos siguientes de la migración, espere a que el servidor haya descargado por completo el espacio de nombres del recurso compartido de nube. Si empieza a mover archivos demasiado pronto al servidor, corre el riesgo de que se realicen cargas innecesarias e incluso de que se produzcan conflictos de sincronización de archivos.

Para determinar si el servidor ha completado la sincronización inicial de la descarga, abra el Visor de eventos en la instancia de Windows Server que se está sincronizando y use el registro de eventos de telemetría de Azure File Sync.
El registro de eventos de telemetría se encuentra en el Visor de eventos en Applications and Services\Microsoft\FileSync\Agent.

Busque el evento 9102 más reciente. El identificador de evento 9102 se registra cuando finaliza una sesión de sincronización. En el texto del evento, hay un campo para la dirección de sincronización de la descarga. (`HResult` debe ser cero y los archivos deben descargarse).
 
Debería ver dos eventos consecutivos de este tipo y con este contenido para asegurarse de que el servidor haya terminado de descargar el espacio de nombres. No hay ningún problema si hay otros eventos entre los dos eventos 9102.

## <a name="phase-10-run-robocopy-from-your-nas"></a>Fase 10: Ejecución de Robocopy desde el NAS

Una vez que el servidor haya completado la sincronización inicial del espacio de nombres completo desde el recurso compartido en la nube, puede continuar con este paso. La sincronización inicial debe completarse antes de continuar con este paso. Consulte la sección anterior para obtener más información.

En este paso, ejecutará trabajos de Robocopy para sincronizar los recursos compartidos en la nube con los cambios más recientes del NAS que se produjeron desde el momento en que bifurcó los recursos compartidos en el dispositivo Data Box.
Esta ejecución de Robocopy puede finalizar rápidamente o tardar unos minutos, en función de la cantidad de abandonos que se hayan producido en los recursos compartidos de NAS.

> [!WARNING]
> Debido al comportamiento con regresión de Robocopy en Windows Server 2019, el modificador `/MIR` de Robocopy no es compatible con los directorios de destino en capas. No puede usar Windows Server 2019 ni un cliente Windows 10 para esta fase de la migración. Use Robocopy en una instancia intermedia de Windows Server 2016.

Este es el enfoque de migración básico:
 - Ejecute Robocopy desde el dispositivo NAS para sincronizar la instancia de Windows Server. 
 - Use Azure File Sync para sincronizar los recursos compartidos de archivos de Azure desde Windows Server.

Ejecute la primera copia local en la carpeta de destino de Windows Server:

1. Identifique la primera ubicación en el dispositivo NAS.
1. Identifique la carpeta coincidente en la instancia de Windows Server, que ya tiene Azure File Sync configurado.
1. Inicie la copia con Robocopy.

El comando de Robocopy siguiente solo copiará las diferencias (archivos y carpetas actualizados) desde el almacenamiento NAS a la carpeta de destino de la instancia de Windows Server. Después, la instancia de Windows Server los sincronizará con los recursos compartidos de archivos de Azure. 

[!INCLUDE [storage-files-migration-robocopy](../../../includes/storage-files-migration-robocopy.md)]

Si ha aprovisionado menos almacenamiento en la instancia de Windows Server que el que usan los archivos del dispositivo NAS, ha configurado la nube por niveles. A medida que el volumen local de Windows Server se llena, la [nube por niveles](../file-sync/file-sync-cloud-tiering-overview.md) se iniciará y organizará por niveles los archivos que ya se han sincronizado correctamente. La nube por niveles generará espacio suficiente para continuar con la copia desde el dispositivo NAS. La nube por niveles realiza comprobaciones cada hora para determinar qué se ha sincronizado y liberar espacio en disco a fin de alcanzar el 99 % de espacio libre del volumen.

Robocopy podría tener que trasladar más archivos de los que se pueden almacenar localmente en la instancia de Windows Server. Puede esperar que Robocopy realice el traslado más rápido de lo que Azure File Sync puede cargar los archivos y cambiarlos de nivel del volumen local. En esta situación, Robocopy producirá un error. Se recomienda trabajar con los recursos compartidos en una secuencia que impida esta situación. Por ejemplo, mueva solo los recursos compartidos que quepan en el espacio disponible en la instancia de Windows Server. Otra opción es evitar iniciar trabajos de Robocopy en todos los recursos compartidos al mismo tiempo. La buena noticia es que el modificador `/MIR` garantizará que solo se muevan los valores delta. Una vez que se haya movido un valor delta, un trabajo reiniciado no tendrá que volver a mover el archivo.

### <a name="do-the-cutover"></a>Realización de la transición

Al ejecutar por primera vez el comando Robocopy, los usuarios y las aplicaciones seguirán accediendo a los archivos en la ubicación del NAS y podrán modificarlos. Robocopy procesará un directorio y, luego, pasará al siguiente. A continuación, un usuario del NAS podría agregar, cambiar o eliminar un archivo del primer directorio que no se procesará durante la ejecución actual de Robocopy. Este comportamiento es normal.

La primera ejecución consiste en mover la mayor parte de los datos perdidos a la instancia de Windows Server y, después, a la nube mediante Azure File Sync. Esta primera copia puede tardar mucho tiempo, en función de lo siguiente:

* El ancho de banda de carga.
* La velocidad de la red local y lo óptimamente que los subprocesos de Robocopy coinciden con ella.
* El número de elementos (archivos y carpetas) que deben procesar Robocopy y Azure File Sync.

Una vez completada la ejecución inicial, vuelva a ejecutar el comando.

Robocopy finalizará más rápido la segunda vez que lo ejecute para un recurso compartido, ya que solo necesita transportar los cambios posteriores a la última ejecución. Puede ejecutar trabajos repetidos para el mismo recurso compartido.

Si considera que el tiempo de inactividad es aceptable, debe quitar el acceso de usuario a los recursos compartidos basados en NAS. Hágalo de cualquier forma que impida que los usuarios cambien el contenido y la estructura de archivos y carpetas. Por ejemplo, puede apuntar el espacio de nombres DFS a una ubicación que no existe o cambiar las ACL raíz en el recurso compartido.

Ejecute Robocopy una última vez. Recuperará todos los cambios que se hayan omitido.
El tiempo necesario para hacer este último paso depende de la velocidad del análisis de Robocopy. Para realizar un cálculo estimado del tiempo (que equivale al tiempo de inactividad), averigüe cuánto duró la ejecución anterior.

Cree un recurso compartido en la carpeta de Windows Server y, eventualmente, ajuste esta carpeta como destino de la implementación de DFS-N. Asegúrese de establecer los mismos permisos de nivel de recurso compartido que en el recurso compartido de SMB de NAS. Si tuviera un NAS unido a un dominio de clase empresarial, los SID de usuario coincidirían automáticamente porque los usuarios se encuentran en Active Directory y Robocopy copia los archivos y metadatos con total fidelidad. Si ha empleado usuarios locales en el NAS, debe hacer lo siguiente: 
- Vuelva a crear estos usuarios como usuarios locales de Windows Server. 
- Asigne los SID existentes que Robocopy ha migrado a su instancia de Windows Server a los SID de los usuarios locales nuevos de Windows Server.

Ha completado la migración de un recurso compartido o un grupo de recursos compartidos a una raíz o un volumen común (en función de la asignación de la fase 1).

Puede intentar ejecutar algunas de estas copias en paralelo. Se recomienda procesar el ámbito de un recurso compartido de archivos de Azure de cada vez.

## <a name="deprecated-option-offline-data-transfer"></a>Opción en desuso: "transferencia de datos sin conexión"

Antes de la publicación del agente de Azure File Sync versión 13, la integración con Data Box se realizaba mediante un proceso denominado "transferencia de datos sin conexión". Este proceso está en desuso. Con la versión 13 del agente, se ha reemplazado por los pasos mucho más sencillos y rápidos que se describen en este artículo. Si sabe que quiere usar la funcionalidad de "transferencia de datos sin conexión" en desuso, todavía puede hacerlo. Sigue disponible mediante un [módulo de PowerShell de AFS anterior](https://www.powershellgallery.com/packages/Az.StorageSync/1.4.0) concreto:

```powershell
Install-Module Az.StorageSync -RequiredVersion 1.4.0
Import-module Az.StorageSync -RequiredVersion 1.4.0
# Verify the specific version is loaded:
Get-module Az.StorageSync
```
Después, puede continuar con la creación de un punto de conexión de servidor con el mismo módulo de PowerShell y especificar un recurso compartido de almacenamiento provisional en el proceso.
Si tiene una migración en curso con el proceso de transferencia de datos sin conexión, la migración continuará según lo previsto y todavía tendrá que deshabilitar esta configuración una vez que se complete la migración.
La capacidad de iniciar nuevas migraciones con este proceso en desuso se quitará con una próxima versión del agente.

## <a name="troubleshooting"></a>Solución de problemas

El problema más común es que el comando Robocopy genere un error de tipo "Volumen lleno" en el lado de Windows Server. La nube por niveles actúa una vez cada hora para evacuar el contenido del disco local de Windows Server, que se ha sincronizado. Su objetivo es alcanzar el 99 % de espacio libre en el volumen.

Permita que el progreso de la sincronización y la nube por niveles liberen espacio en disco. Puede observarlo en el Explorador de archivos en la instancia de Windows Server.

Cuando la instancia de Windows Server tenga capacidad suficiente disponible, vuelva a ejecutar el comando para resolver el problema. Si se da esta situación, no se interrumpe nada y puede avanzar con plena confianza. La única consecuencia es el inconveniente de tener que volver a ejecutar el comando.

Para solucionar problemas de Azure File Sync, consulte el artículo que se indica en la sección siguiente.

## <a name="next-steps"></a>Pasos siguientes

Hay más información sobre los recursos compartidos de archivos de Azure y Azure File Sync. Los artículos siguientes lo ayudarán a comprender las opciones avanzadas y los procedimientos recomendados. También proporcionan ayuda para solucionar problemas. Estos artículos contienen vínculos a la [documentación del recurso compartido de archivos de Azure](storage-files-introduction.md) cuando corresponda.

* [Información general acerca de la migración](storage-files-migration-overview.md)
* [Planeamiento de una implementación de Azure File Sync](../file-sync/file-sync-planning.md)
* [Creación de un recurso compartido de archivos](storage-how-to-create-file-share.md)
* [Solución de problemas de Azure File Sync](../file-sync/file-sync-troubleshoot.md)