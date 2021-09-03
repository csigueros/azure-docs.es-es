---
title: Migración de recursos web de Azure desde Azure Alemania a Azure global
description: En este artículo se proporciona información sobre cómo migrar los recursos web de Azure desde Azure Alemania a Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: d67a4aadbe22b5447aa73063e48c0ca43fb45d04
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029208"
---
# <a name="migrate-web-resources-to-global-azure"></a>Migración de recursos web a Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

En este artículo se proporciona información que puede ayudarle a migrar los recursos web de Azure desde Azure Alemania a Azure global.

## <a name="web-apps"></a>Web Apps

En este momento, no se admite la migración de aplicaciones que ha creado con la característica Web Apps de Azure App Service de Azure Alemania a Azure global. Se recomienda exportar la aplicación web como una plantilla de Azure Resource Manager. Después, vuelva a realizar la implementación después de cambiar la propiedad de ubicación a la nueva región de destino.

> [!IMPORTANT]
> Cambie la ubicación, los secretos de Azure Key Vault, los certificados y otros GUID para que sea coherente con la nueva región.

### <a name="migrate-web-app-resource"></a>Migración del recurso de aplicación web

1. [Exporte la aplicación web y el plan de App Service como plantilla](../azure-resource-manager/templates/export-template-portal.md) desde la suscripción de Azure Alemania. Seleccione los recursos que quiera migrar en el grupo de recursos de la aplicación web y expórtelos como una plantilla.
1. Descargue la plantilla como un archivo ZIP. 
1. Cambie la propiedad location del archivo **template.json** por la región global de Azure de destino. Por ejemplo, el siguiente archivo JSON tiene una ubicación de destino de *Oeste de EE. UU.* .

    ```json
        "resources": [
        {
            "type": "Microsoft.Web/serverfarms",
            "apiVersion": "2018-02-01",
            "name": "[parameters('serverfarms_myappservice_name')]",
            "location": "West US",

    ```
1. Implemente la plantilla modificada en Azure global. Por ejemplo, puede usar PowerShell para la implementación.

    ```powershell
    az deployment group create --name "<web app name>" \
        --resource-group "<resource group name>" \
        --template-file "<path of your template.json file>"
    ```

### <a name="migrate-web-app-content"></a>Migración del contenido de la aplicación web

1. En el portal de Azure Alemania, seleccione la aplicación web.
1. Seleccione **Herramientas de desarrollo > Herramientas avanzadas**.
1. En el menú superior, seleccione **Consola de depuración** y después **PowerShell**.
1. Seleccione **sitio**.
1. Seleccione el **icono de descarga** situado junto a la carpeta **wwwroot**. El archivo ZIP descargado contiene el código fuente de la aplicación web.
1. Implemente la raíz web en la aplicación web global de Azure migrada. Por ejemplo, puede usar el siguiente script de PowerShell.

    ``` powershell
    az webapp deployment source config-zip \
        --resource-group "<resource group name>" \
        --name "<web App name>" \
        --src "path to webroot folder zip file"
    ```

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de App Service](../app-service/tutorial-dotnetcore-sqldb-app.md).
- Obtenga información acerca de cómo [exportar las plantillas de Azure Resource Manager](../azure-resource-manager/templates/export-template-portal.md).
- Revise la [introducción a Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Revise la [introducción a App Service](../app-service/overview.md).
- Consulte una [introducción a las ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Obtenga información sobre cómo [volver a implementar una plantilla](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="notification-hubs"></a>Notification Hubs

Para migrar la configuración de una instancia de Azure Notification Hubs a otra, exporte e importe todos los tokens de registro con sus etiquetas:

1. [Exporte los registros existentes del Centro de notificaciones](/previous-versions/azure/azure-services/dn790624(v=azure.100)) a un contenedor de almacenamiento de blobs de Azure.
1. Cree un nuevo Centro de notificaciones en el entorno de destino.
1. [Importe los tokens de registro](/previous-versions/azure/azure-services/dn790624(v=azure.100)) desde el almacenamiento de blobs al nuevo Centro de notificaciones.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).
- Revise la [introducción a Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="event-hubs"></a>Event Hubs

Para migrar un centro de eventos de Azure, exporte la plantilla de recursos del centro de eventos desde Azure Alemania y, después, implemente la plantilla en Azure global.

1. [Exporte el centro de eventos como plantilla](../azure-resource-manager/templates/export-template-portal.md) desde la suscripción de Azure Alemania.
1. [Implemente la plantilla del centro de eventos como una plantilla personalizada](../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) en la suscripción global de Azure. Cargue e implemente la plantilla que ha exportado desde la suscripción de Azure Alemania.

Para obtener más información:

- Revise la [introducción a Event Hubs](../event-hubs/event-hubs-about.md).
- Revise la [introducción a Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Obtenga información acerca de cómo [exportar las plantillas de Azure Resource Manager](../azure-resource-manager/templates/export-template-portal.md).
- Obtenga información sobre cómo [volver a implementar una plantilla](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Proceso](./germany-migration-compute.md)
- [Redes](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Bases de datos](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integración](./germany-migration-integration.md)
- [Identidad](./germany-migration-identity.md)
- [Seguridad](./germany-migration-security.md)
- [Herramientas de administración](./germany-migration-management-tools.md)
- [Elementos multimedia](./germany-migration-media.md)