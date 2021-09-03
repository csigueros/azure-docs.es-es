---
title: Administración mediante programación de espacios de nombres y entidades de Azure Service Bus
description: En este artículo se explica cómo aprovisionar dinámicamente o mediante programación las entidades y los espacios de nombres de Service Bus.
ms.topic: article
ms.date: 08/06/2021
ms.openlocfilehash: b053ff5cce51fbcd0ce56e2bdbfaff39dddb6394
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178922"
---
# <a name="dynamically-provision-service-bus-namespaces-and-entities"></a>Aprovisionamiento dinámico de espacios de nombres y entidades de Service Bus 
Azure Service Bus proporciona bibliotecas para facilitar el aprovisionamiento dinámico de las entidades y los espacios de nombres de Service Bus. Esto posibilita escenarios complejos de implementación y mensajería, y permite determinar mediante programación qué entidades aprovisionar.

## <a name="overview"></a>Información general
Puede seguir dos enfoques para administrar recursos de Azure Service Bus mediante programación. El primero consiste en usar las bibliotecas basadas en [Azure Resource Manager](../azure-resource-manager/management/overview.md), que permiten administrar espacios de nombres, colas, temas, suscripciones, reglas y directivas SAS. Las bibliotecas basadas en Azure Resource Manager tienen compatibilidad para la autenticación mediante Azure Active Directory, pero no mediante cadenas de conexión. El segundo enfoque consiste en aprovechar las mismas bibliotecas cliente de Service Bus que se usan para enviar y recibir mensajes. Las bibliotecas cliente también proporcionan API para ayudarle a administrar colas, temas, suscripciones y reglas en un espacio de nombres *existente*. Admiten la autenticación con cadenas de conexión. Al decidir el enfoque que adoptar, tenga en cuenta lo siguiente. 

Las bibliotecas basadas en Azure Resource Manager ofrecen la misma funcionalidad que Azure Portal, la CLI y PowerShell cuando se trata de administrar espacios de nombres y entidades de Service Bus como colas, temas, suscripciones, etc. Si ha usado Azure Portal, la CLI o PowerShell para las operaciones de administración y quiere una forma dinámica de hacerlo, estas bibliotecas podrían ser una mejor opción. 

Pero si ya usa una biblioteca cliente de Service Bus para operaciones específicas del servicio como enviar y recibir mensajes, y también necesita administrar entidades de Service Bus, es posible que el uso de la misma biblioteca le resulte más cómodo. Las bibliotecas cliente tienen un elemento `ServiceBusAdministrationClient` (denominado `ServiceBusManagementClient` en las bibliotecas anteriores) que ofrece un subconjunto de las características de administración proporcionadas por las bibliotecas basadas en Azure Resource Manager. Debe destacarse que, aunque las bibliotecas basadas en Azure Resource Manager permiten administrar espacios de nombres y entidades de Service Bus, las bibliotecas cliente solo permiten administrar entidades en un espacio de nombres existente, pero *no* el propio espacio de nombres.

## <a name="manage-using-azure-resource-manager-based-libraries"></a>Administración mediante bibliotecas basadas en Azure Resource Manager

Las bibliotecas basadas en Azure Resource Manager permiten administrar espacios de nombres, colas, temas, suscripciones, reglas y directivas SAS.  *Solo* admiten la autenticación con Azure Active Directory (Azure AD); no admiten cadenas de conexión. 

| Idioma | Paquete | Documentación | Ejemplos|
|-|-|-|-|
|.NET | [Microsoft.Azure.Management.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus/) |[Referencia de API para Microsoft.Azure.Management.ServiceBus](/dotnet/api/microsoft.azure.management.servicebus)|[.NET](https://github.com/Azure-Samples/service-bus-dotnet-management/tree/a55185bef30d1763c1a8182a3361dbb548bad436) |
| Java | [azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[Referencia de API para com.azure.resourcemanager.servicebus](/java/api/com.azure.resourcemanager.servicebus)|[Java](https://github.com/Azure-Samples/service-bus-java-manage-publish-subscribe-with-basic-features/tree/e4718a825e8fcfe58e5921770ff8084da67ccd89)|
| JavaScript |[@azure/arm-servicebus](https://www.npmjs.com/package/@azure/arm-servicebus)|[Referencia de API para @azure/arm-servicebus](/javascript/api/@azure/arm-servicebus/)||
|Python|[azure-mgmt-servicebus](https://pypi.org/project/azure-mgmt-servicebus/)|[Referencia de API para azure-mgmt-servicebus](/python/api/azure-mgmt-servicebus/azure.mgmt.servicebus)||


### <a name="fluent-net-and-java-libraries"></a>Bibliotecas de Fluent en .NET y Java
Existe una versión de Fluent de las bibliotecas basadas en Azure Resource Manager. 

|Idioma|Paquete|Documentación|
|-|-|-|
|.NET|[Microsoft.Azure.Management.ServiceBus.Fluent](https://www.nuget.org/packages/Microsoft.Azure.Management.ServiceBus.Fluent/) |[Referencia de API para Microsoft.Azure.Management.ServiceBus.Fluent](/dotnet/api/microsoft.azure.management.servicebus.fluent) |
| Java|[azure-resourcemanager-servicebus](https://search.maven.org/artifact/com.azure.resourcemanager/azure-resourcemanager-servicebus)|[Referencia de API para com.azure.resourcemanager.servicebus.fluent](/java/api/com.azure.resourcemanager.servicebus.fluent) |

## <a name="manage-using-service-bus-client-libraries"></a>Administración mediante bibliotecas cliente de Service Bus 

La bibliotecas cliente de Service Bus que se usan para operaciones como enviar y recibir mensajes también se pueden utilizar para administrar colas, temas, suscripciones y reglas en un espacio de nombres de Service Bus *existente*. Esta característica está disponible por medio de `ServiceBusAdministrationClient` en las bibliotecas más recientes y con `ServiceBusManagementClient` en las bibliotecas anteriores. Se recomienda encarecidamente usar las bibliotecas más recientes.

### <a name="latest-service-bus-libraries"></a>Bibliotecas de Service Bus más recientes
|Idioma|Paquete|Documentación|Ejemplos|
|-|-|-|-|
|.NET|  [Azure.Messaging.ServiceBus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus)|[ServiceBusAdministrationClient](/dotnet/api/azure.messaging.servicebus.administration.servicebusadministrationclient)|[.NET](/samples/azure/azure-sdk-for-net/azuremessagingservicebus-samples/)|
|Java|[azure-messaging-servicebus](https://search.maven.org/artifact/com.azure/azure-messaging-servicebus)|[ServiceBusAdministrationAsyncClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationasyncclient), [ServiceBusAdministrationClient](/java/api/com.azure.messaging.servicebus.administration.servicebusadministrationclient)| [Java](/samples/azure/azure-sdk-for-java/servicebus-samples/)|
|JavaScript|[@azure/service-bus](https://www.npmjs.com/package/@azure/service-bus)|[ServiceBusAdministrationClient](/javascript/api/@azure/service-bus/servicebusadministrationclient)|[JavaScript](/samples/azure/azure-sdk-for-js/service-bus-javascript/)/[TypeScript](/samples/azure/azure-sdk-for-js/service-bus-typescript/)|
|Python|[azure-servicebus](https://pypi.org/project/azure-servicebus/)|[ServiceBusAdministrationClient](/python/api/azure-servicebus/azure.servicebus.management.servicebusadministrationclient)|[Python](/samples/azure/azure-sdk-for-python/servicebus-samples/)|

### <a name="legacy-service-bus-libraries"></a>Bibliotecas de Service Bus heredadas
|Idioma|Paquete|Documentación|Ejemplos|
|-|-|-|-|
|.NET|[Microsoft.Azure.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/)|[ManagementClient](/dotnet/api/microsoft.azure.servicebus.management.managementclient)|[.NET](https://github.com/Azure/azure-service-bus/tree/master/samples/DotNet/Microsoft.Azure.ServiceBus)|
|Java|[azure-mgmt-servicebus](https://search.maven.org/artifact/com.microsoft.azure/azure-mgmt-servicebus)|[ManagementClientAsync](/java/api/com.microsoft.azure.servicebus.management.managementclientasync), [ManagementClient](/java/api/com.microsoft.azure.servicebus.management.managementclient)|[Java](https://github.com/Azure/azure-service-bus/tree/master/samples/Java)|


## <a name="next-steps"></a>Pasos siguientes
- Envío y recepción de mensajes desde la cola mediante la biblioteca de Service Bus más reciente: [.NET](./service-bus-dotnet-get-started-with-queues.md#send-messages), [Java](./service-bus-java-how-to-use-queues.md), [JavaScript](./service-bus-nodejs-how-to-use-queues.md), [Python](./service-bus-python-how-to-use-queues.md)
- Envío de mensajes a temas y recepción de mensajes desde suscripciones mediante la biblioteca de Service Bus más reciente: .[NET](./service-bus-dotnet-how-to-use-topics-subscriptions.md),  [Java](./service-bus-java-how-to-use-topics-subscriptions.md), [JavaScript](./service-bus-nodejs-how-to-use-topics-subscriptions.md), [Python](./service-bus-python-how-to-use-topics-subscriptions.md)