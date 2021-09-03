---
title: Migración de recursos de IoT de Azure desde Azure Alemania a Azure global
description: En este artículo se proporciona información sobre cómo migrar los recursos de IoT de Azure desde Azure Alemania a Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: 6e480ef9c12bdf425a41f567f1941d3f1a654b08
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/21/2020
ms.locfileid: "117029228"
---
# <a name="migrate-iot-resources-to-global-azure"></a>Migración de recursos de IoT a Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

En este artículo se proporciona información que puede ayudarle a migrar los recursos de IoT de Azure desde Azure Alemania a Azure global.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Puede usar la herramienta de migración de datos de Azure Cosmos DB para migrar los datos a Azure Cosmos DB. La herramienta de migración de datos Azure Cosmos DB es una solución de código abierto que importa datos a Azure Cosmos DB desde una variedad de orígenes.

La herramienta de migración de datos en Azure Cosmos DB está disponible como una herramienta de interfaz gráfica o como herramienta de línea de comandos. El código fuente está disponible en el repositorio de GitHub de la [herramienta de migración de datos de Azure Cosmos DB](https://github.com/azure/azure-documentdb-datamigrationtool). Una [versión compilada de la herramienta](https://www.microsoft.com/download/details.aspx?id=46436) está disponible en el Centro de descarga de Microsoft.

Para migrar recursos de Azure Cosmos DB, se recomienda que complete los pasos siguientes:

1. Revise los requisitos de tiempo de actividad de la aplicación y las configuraciones de cuentas para determinar el mejor plan de acción.
1. Clone las configuraciones de la cuenta de Azure Alemania a la nueva región mediante la ejecución de la herramienta de migración de datos.
1. Si es posible usar una ventana de mantenimiento, copie los datos desde el origen al destino mediante la ejecución de la herramienta de migración de datos.
1. Si no se puede usar una ventana de mantenimiento, copie los datos desde el origen al destino mediante la ejecución de la herramienta y, después, complete estos pasos:
   1. Utilice un enfoque basado en la configuración para realizar cambios de lectura o escritura en una aplicación.
   1. Complete la sincronización por primera vez.
   1. Configure una sincronización incremental y póngase al día con la fuente de cambios.
   1. Lea en la nueva cuenta y valide la aplicación.
   1. Detenga el proceso de escritura en la cuenta antigua, valide que la fuente de cambios esté al día y, después, escriba en la nueva cuenta.
   1. Detenga la herramienta y elimine la cuenta antigua.
1. Ejecute la herramienta para validar que los datos son coherentes entre las cuentas antiguas y nuevas.

Para obtener más información:

- Lea una [introducción a Azure Cosmos DB](../cosmos-db/introduction.md).
- Aprenda cómo [importar datos en masa a Azure Cosmos DB](../cosmos-db/import-data.md).

## <a name="functions"></a>Functions

En este momento no se admite la migración de los recursos de Azure Functions desde Azure Alemania a Azure global. Se recomienda exportar una plantilla de Resource Manager, cambiar la ubicación y, después, volver a implementarla en la región de destino.

> [!IMPORTANT]
> Cambie la ubicación, los secretos de Azure Key Vault, los certificados y otros GUID para que sea coherente con la nueva región.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Functions](../azure-functions/index.yml).
- Aprenda como [exportar plantillas de Resource Manager](../azure-resource-manager/templates/export-template-portal.md) o lea la introducción a [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Revise la [introducción a Azure Functions](../azure-functions/functions-overview.md).
- Lea una [introducción a las ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/).
- Obtenga información sobre cómo [volver a implementar una plantilla](../azure-resource-manager/templates/deploy-powershell.md).

## <a name="notification-hubs"></a>Notification Hubs

Para migrar la configuración de una instancia de Azure Notification Hubs a otra, exporte y después importe todas las etiquetas y los tokens de registro:

1. [Exporte los registros existentes del Centro de notificaciones](/previous-versions/azure/azure-services/dn790624(v=azure.100)) a un contenedor de almacenamiento de blobs de Azure.
1. Cree un nuevo Centro de notificaciones en el entorno de destino.
1. [Importe los tokens de registro](/previous-versions/azure/azure-services/dn790624(v=azure.100)) desde el almacenamiento de blobs al nuevo Centro de notificaciones.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Notification Hubs](../notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started.md).
- Revise la [introducción a Azure Notification Hubs](../notification-hubs/notification-hubs-push-notification-overview.md).

## <a name="iot-hub"></a>IoT Hub

Aunque puede migrar las instancias de Azure IoT Hub desde Azure Germany a Azure global, la migración no es perfecta.

> [!NOTE]
> Esta migración podría provocar un tiempo de inactividad y pérdida de datos en la aplicación de IoT de Azure. No se migran los mensajes de telemetría, los comandos C2D y la información relacionada con el trabajo (programas e historial). Debe volver a configurar los dispositivos y aplicaciones de back-end para empezar a usar las nuevas cadenas de conexión.

### <a name="step-1-re-create-the-iot-hub"></a>Paso 1: Recreación del centro de IoT

IoT Hub no admite la clonación de forma nativa. Sin embargo, puede usar la característica de Azure Resource Manager para [exportar un grupo de recursos como una plantilla](../azure-resource-manager/templates/export-template-portal.md) y exportar los metadatos de IoT Hub. Las rutas configuradas y demás configuraciones de IoT Hub se incluyen en los metadatos exportados. Después, vuelva a implementar la plantilla en Azure global. Le resultará más fácil volver a crear el centro de IoT en Azure Portal examinando los detalles en el JSON exportado.

### <a name="step-2-migrate-device-identities"></a>Paso 2: Migración de identidades del dispositivo

Para migrar las identidades de dispositivo:

1. En el inquilino de origen en Azure Alemania, utilice la API de Resource Manager [ExportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) para exportar todas las identidades de dispositivo, dispositivos gemelos y módulos gemelos (incluidas las claves) a un contenedor de almacenamiento. Puede usar un contenedor de almacenamiento en Azure Alemania o Azure global. Asegúrese de que el identificador URI de firma de acceso compartido generado tiene permisos suficientes. 
1. Ejecute la API [ImportDevices](../iot-hub/iot-hub-bulk-identity-mgmt.md) de Resource Manager para importar todas las identidades de dispositivo desde el contenedor de almacenamiento en el centro de IoT clonado en Azure global.
1. Vuelva a configurar los dispositivos y servicios de back-end para empezar a usar las nuevas cadenas de conexión. El nombre de host cambia de **\*.azure devices.de** a **\*.azure devices.com**.  

> [!NOTE]
> La entidad de certificación raíz es diferente en Azure Alemania y Azure global. Tenga esto en cuenta cuando vuelva a configurar los dispositivos y aplicaciones back-end que interactúan con la instancia de IoT Hub.

Para obtener más información:

- Aprenda a [exportar identidades de IoT Hub de forma masiva](../iot-hub/iot-hub-bulk-identity-mgmt.md#export-devices).
- Aprenda a [importar identidades de IoT Hub de forma masiva](../iot-hub/iot-hub-bulk-identity-mgmt.md#import-devices).
- Revise la [introducción a Azure IoT Hub](../iot-hub/about-iot-hub.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Proceso](./germany-migration-compute.md)
- [Redes](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de datos](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [Integración](./germany-migration-integration.md)
- [Identidad](./germany-migration-identity.md)
- [Seguridad](./germany-migration-security.md)
- [Herramientas de administración](./germany-migration-management-tools.md)
- [Elementos multimedia](./germany-migration-media.md)