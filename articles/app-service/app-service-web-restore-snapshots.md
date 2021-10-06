---
title: Restauración de una aplicación a partir de una instantánea
description: Obtenga información sobre cómo restaurar la aplicación desde una instantánea. Realice la recuperación de una pérdida de datos inesperada en el nivel Premium con las copias automáticas de las propiedades reemplazadas.
ms.assetid: 4164f9b5-f735-41c6-a2bb-71f15cdda417
ms.topic: article
ms.date: 09/02/2021
ms.reviewer: nicking
ms.custom: seodec18
ms.openlocfilehash: 48b3d859a73a2d63644e1d5881c3505cee93f9d7
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679510"
---
# <a name="restore-an-app-in-azure-from-a-snapshot"></a>Restauración de una aplicación en Azure desde una instantánea
Este artículo muestra cómo restaurar una aplicación en [Azure App Service](../app-service/overview.md) desde una instantánea. Puede restaurar la aplicación a un estado anterior basado en una de las instantáneas de la aplicación. No es necesario habilitar las instantáneas, la plataforma guarda automáticamente una instantánea de todas las aplicaciones para fines de recuperación de datos.

Las instantáneas son instantáneas incrementales de la aplicación de App Service. Cuando la aplicación está en el nivel Premium o un nivel superior, App Service toma instantáneas periódicas del contenido de la aplicación y su configuración. Ofrecen varias ventajas con respecto a las [copias de seguridad estándar](manage-backup.md):

- No hay errores de copia de archivos debido a bloqueos de archivo.
- Mayor tamaño máximo de instantánea (30 GB).
- No se requiere ninguna configuración para los planes de tarifa admitidos.
- Las instantáneas se pueden restaurar en una nueva aplicación de App Service en cualquier región de Azure.

La restauración desde instantáneas está disponible para las aplicaciones que se ejecutan en el nivel **Premium** o superior. Para obtener más información sobre la escalación de su aplicación, vea [Escalación de una aplicación web en el Servicio de aplicaciones de Azure](manage-scale-up.md).

## <a name="limitations"></a>Limitaciones

- El tamaño máximo admitido para la restauración de instantáneas es de 30 GB. Se produce un error en la restauración de instantáneas si el tamaño de almacenamiento es superior a 30 GB. Para reducir el tamaño de almacenamiento, considere la posibilidad de mover archivos como registros, imágenes, audios y vídeos a [Azure Storage](../storage/index.yml), por ejemplo.
- Las bases de datos conectadas que admitan o monten una [copia de seguridad estándar](manage-backup.md#what-gets-backed-up) de [Azure Storage](configure-connect-to-azure-storage.md?pivots=container-windows) *no* se incluyen en la instantánea. Considere la posibilidad de usar las funcionalidades de copia de seguridad nativas del servicio de Azure conectado (por ejemplo, [SQL Database](../azure-sql/database/automated-backups-overview.md) y [Azure Files](../storage/files/storage-snapshots-files.md)).
- App Service detiene la aplicación de destino o la ranura de destino mientras se realiza la restauración de una instantánea. Para minimizar el tiempo de inactividad de la aplicación de producción, restaure primero la instantánea en un [espacio de ensayo](deploy-staging-slots.md) y, a continuación, cambie a producción.
- Están disponibles las instantáneas de los últimos 30 días. El período de retención y la frecuencia de las instantáneas no son configurables.
- App Services en una instancia de App Service Environment no admite instantáneas.

## <a name="restore-an-app-from-a-snapshot"></a>Restauración de una aplicación desde una instantánea

1. En la página **Configuración** de la aplicación en [Azure Portal](https://portal.azure.com), haga clic en **Copias de seguridad** para mostrar la página **Copias de seguridad**. A continuación, haga clic en **Restaurar** en la sección **Instantánea (Versión preliminar)** .
   
    ![Captura de pantalla que muestra cómo restaurar una aplicación a partir de una instantánea.](./media/app-service-web-restore-snapshots/1.png)

2. En la página **Restaurar**, seleccione la instantánea para restaurar.
   
    ![Captura de pantalla que muestra cómo seleccionar la instantánea que se va a restaurar. ](./media/app-service-web-restore-snapshots/2.png)
   
3. Especifique el destino de la restauración de la aplicación en **Destino de restauración**.
   
    ![Captura de pantalla que muestra cómo especificar el destino de la restauración.](./media/app-service-web-restore-snapshots/3.png)
   
   > [!WARNING]
   > Como procedimiento recomendado, se recomienda realizar la restauración en una ranura nueva y, después, realizar un intercambio. Si elige **Sobrescribir**, se borrarán y sobrescribirán todos los datos existentes en el sistema de archivos actual de la aplicación. Antes de hacer clic en **Aceptar**, asegúrese de que es lo que desea hacer.
   > 
   > 
      
   > [!Note]
   > Debido a limitaciones técnicas actuales, solo puede restaurar a aplicaciones de la misma unidad de escala. Esta limitación se eliminará en una próxima versión.
   > 
   > 
   
    Puede seleccionar **Aplicación existente** para restaurar a una ranura. Antes de usar esta opción, debe haber creado una ranura en la aplicación.

4. Puede elegir restaurar la configuración del sitio.
   
    ![Captura de pantalla que muestra cómo restaurar la configuración del sitio.](./media/app-service-web-restore-snapshots/4.png)

5. Haga clic en **OK**.
