---
title: Migración de recursos de integración de Azure desde Azure Alemania a Azure global
description: En este artículo se proporciona información sobre cómo migrar los recursos de integración de Azure desde Azure Alemania a Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate, devx-track-azurepowershell
ms.openlocfilehash: 0a4ea6393741e15d8dce94869213c62c08537c7e
ms.sourcegitcommit: 20acb9ad4700559ca0d98c7c622770a0499dd7ba
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/29/2021
ms.locfileid: "117029058"
---
# <a name="migrate-integration-resources-to-global-azure"></a>Migración de recursos de integración a Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

En este artículo se proporciona información que puede ayudarle a migrar los recursos de integración de Azure desde Azure Alemania a Azure global.

## <a name="service-bus"></a>Azure Service Bus

Los servicios Azure Service Bus no tienen funcionalidades de exportación o importación de datos. Para migrar los recursos de Service Bus de Azure Alemania a Azure global, puede exportar los recursos [como una plantilla de Azure Resource Manager](../azure-resource-manager/templates/export-template-portal.md). Después, adapta la plantilla exportada para Azure global y vuelve a crear los recursos.

> [!NOTE]
> La exportación de una plantilla de Resource Manager no copia los datos (por ejemplo, los mensajes). Exportar una plantilla solo vuelve a crear los metadatos.

> [!IMPORTANT]
> Cambie la ubicación, los secretos de Azure Key Vault, los certificados y otros GUID para que sea coherente con la nueva región.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

### <a name="service-bus-metadata"></a>Metadatos de Service Bus

Los siguientes elementos de metadatos de Service Bus se vuelven a crear cuando se exporta una plantilla de Resource Manager:

- Espacios de nombres
- Colas
- Temas
- Suscripciones
- Reglas
- Reglas de autorización

### <a name="keys"></a>Claves

Los pasos anteriores para exportar y volver a crear no copian las claves de firma de acceso compartido que están asociadas con las reglas de autorización. Si debe conservar las claves de firma de acceso compartido, utilice el cmdlet `New-AzServiceBuskey` con el parámetro opcional `-Keyvalue` para aceptar la clave como una cadena. El cmdlet actualizado está disponible en el [módulo Az de Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="usage-example"></a>Ejemplo de uso

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Queue <queuename> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

```powershell
New-AzServiceBuskey -ResourceGroupName <resourcegroupname> -Namespace <namespace> -Topic <topicname> -Name <name of Authorization rule> -RegenerateKey <PrimaryKey/SecondaryKey> -KeyValue <string - key value>
```

> [!NOTE]
> Debe actualizar las aplicaciones para usar una nueva cadena de conexión, incluso si conserva las claves. Los nombres de host DNS son diferentes en Azure Alemania y Azure global.

### <a name="sample-connection-strings"></a>Cadena de conexión de ejemplo

**Azure Alemania**

```cmd
Endpoint=sb://myBFProdnamespaceName.**servicebus.cloudapi.de**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

**Azure global**

```cmd
Endpoint=sb://myProdnamespaceName.**servicebus.windows.net**/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXx=
```

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Service Bus](../service-bus-messaging/index.yml).
- Familiarícese con el procedimiento de [exportación de plantillas de Resource Manager](../azure-resource-manager/templates/export-template-portal.md) o lea la información general de [Azure Resource Manager](../azure-resource-manager/management/overview.md).
- Revise la [introducción a Service Bus](../service-bus-messaging/service-bus-messaging-overview.md).

## <a name="logic-apps"></a>Logic Apps

Azure Logic Apps no está disponible en Azure Alemania, pero en su lugar puede crear trabajos de programación mediante Logic Apps en Azure global. Aunque anteriormente estaba disponible en Azure Alemania, Azure Scheduler se va a retirar.

Para obtener más información:

- Para obtener más información, complete los [tutoriales de Azure Logic Apps](../logic-apps/tutorial-build-schedule-recurring-logic-app-workflow.md).
- Revise la [introducción a Azure Logic Apps](../logic-apps/logic-apps-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Proceso](./germany-migration-compute.md)
- [Redes](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de datos](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Identidad](./germany-migration-identity.md)
- [Seguridad](./germany-migration-security.md)
- [Herramientas de administración](./germany-migration-management-tools.md)
- [Elementos multimedia](./germany-migration-media.md)
