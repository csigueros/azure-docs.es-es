---
title: Introducción a la integración de Azure Service Bus en Event Grid | Microsoft Docs
description: En este artículo se proporciona una descripción de cómo se integra la mensajería de Azure Service Bus con Azure Event Grid.
documentationcenter: .net
author: spelluru
ms.topic: conceptual
ms.date: 02/11/2021
ms.author: spelluru
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 999bfb9278f3c355e2b431c0fe3ca13648aa42c9
ms.sourcegitcommit: 5163ebd8257281e7e724c072f169d4165441c326
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2021
ms.locfileid: "112413014"
---
# <a name="azure-service-bus-to-event-grid-integration-overview"></a>Introducción a la integración de Azure Service Bus en Event Grid
Service Bus puede ahora emitir eventos a Event Grid cuando haya mensajes en una cola o suscripción y no haya ningún receptor presente. Puede crear suscripciones de Event Grid para los espacios de nombres de Service Bus, escuchar estos eventos y reaccionar a ellos iniciando un receptor. Con esta característica, puede usar Service Bus en modelos de programación reactiva. El principal escenario de esta característica es que aquellas colas o suscripciones de Service Bus que tengan un bajo volumen de mensajes, no tienen que tener un sondeo de receptor de mensajes en todo momento. 

Para habilitar la característica necesita los siguientes elementos:

* Un espacio de nombres Premium de Service Bus con al menos una cola de Service Bus o un tema de Service Bus que tenga como mínimo una suscripción.
* Acceso de colaborador al espacio de nombres de Service Bus. Vaya al espacio de nombres de Service Bus en Azure Portal, seleccione **Control de acceso (IAM)** y, a continuación, seleccione la pestaña **Asignaciones de rol**. Compruebe que dispone de acceso de colaborador al espacio de nombres. 
* Además, se necesita una suscripción a Event Grid para el espacio de nombres de Service Bus. Esta suscripción recibe una notificación de Event Grid que indica que hay mensajes que se deben recopilar. Los suscriptores típicos pueden ser la característica Logic Apps de Azure App Service, Azure Functions, o un webhook que se ponga en contacto con una aplicación web. El suscriptor, a continuación, procesa los mensajes. 

![19][]

[!INCLUDE [event-grid-service-bus.md](./includes/event-grid-service-bus.md)]

## <a name="event-grid-subscriptions-for-service-bus-namespaces"></a>Suscripciones de Event Grid para espacios de nombres de Service Bus
Puede crear suscripciones de Event Grid para los espacios de nombres de Service Bus de tres formas diferentes:

- Azure Portal. Vea los siguientes tutoriales para aprender a usar Azure Portal con el fin de crear suscripciones de Event Grid para eventos de Service Bus con Azure Logic Apps y Azure Functions como controladores. 
    - [Azure Logic Apps](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
    - [Funciones de Azure](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)
* CLI de Azure. En el siguiente ejemplo de la CLI se muestra cómo crear una suscripción de Azure Functions para un [tema del sistema](../event-grid/system-topics.md) creado por un espacio de nombres de Service Bus.

     ```azurecli-interactive
    namespaceid=$(az resource show --namespace Microsoft.ServiceBus --resource-type namespaces --name "<service bus namespace>" --resource-group "<resource group that contains the service bus namespace>" --query id --output tsv
    
    az eventgrid event-subscription create --resource-id $namespaceid --name "<YOUR EVENT GRID SUBSCRIPTION NAME>" --endpoint "<your_endpoint_url>" --subject-ends-with "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
- PowerShell. Este es un ejemplo:
    ```powershell-interactive
    $namespaceID = (Get-AzServiceBusNamespace -ResourceGroupName "<YOUR RESOURCE GROUP NAME>" -NamespaceName "<YOUR NAMESPACE NAME>").Id
    
    New-AzEVentGridSubscription -EventSubscriptionName "<YOUR EVENT GRID SUBSCRIPTION NAME>" -ResourceId $namespaceID -Endpoint "<YOUR ENDPOINT URL>” -SubjectEndsWith "<YOUR SERVICE BUS SUBSCRIPTION NAME>"
    ```
### <a name="how-many-events-are-emitted-and-how-often"></a>¿Cuál es la frecuencia y la cantidad de eventos que se emiten?

Si tiene varias colas y temas o suscripciones en el espacio de nombres, obtendrá al menos un evento por cola y uno por suscripción. Los eventos se emiten inmediatamente si no hay ningún mensaje en la entidad de Service Bus y llega un mensaje nuevo. O bien, los eventos se emiten cada dos minutos, a menos que Service Bus detecte un receptor activo. El examen de los mensajes no interrumpe los eventos.

De forma predeterminada, Service Bus emite eventos para todas las entidades del espacio de nombres. Si desea obtener eventos solo para entidades concretas, consulte la siguiente sección.

### <a name="use-filters-to-limit-where-you-get-events-from"></a>Use filtros para limitar de dónde se obtienen los eventos

Por ejemplo, si desea obtener eventos solo para una cola o suscripción del espacio de nombres, puede utilizar los filtros *Begins with* (Comienza por) o *Ends with* (Termina con) que proporciona Event Grid. En algunas interfaces, los filtros se denominan *Prefijo* y *Sufijo*. Si desea obtener eventos para varias colas y suscripciones (pero no para todas), puede crear varias suscripciones de Event Grid y proporcionar un filtro para cada una.

## <a name="next-steps"></a>Pasos siguientes
Vea los siguientes tutoriales: 
- [Azure Logic Apps para controlar mensajes de Service Bus recibidos a través de Event Grid](service-bus-to-event-grid-integration-example.md#receive-messages-by-using-logic-apps)
- [Azure Functions para controlar mensajes de Service Bus recibidos a través de Event Grid](service-bus-to-event-grid-integration-function.md#connect-the-function-and-namespace-via-event-grid)

[1]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgrid1.png
[19]: ./media/service-bus-to-event-grid-integration-concept/sbtoeventgriddiagram.png
[8]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid8.png
[9]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgrid9.png
[20]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal.png
[21]: ./media/service-bus-to-event-grid-integration-example/sbtoeventgridportal2.png
