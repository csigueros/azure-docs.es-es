---
title: Solución de problemas de Event Grid
description: En este artículo se proporcionan diferentes formas de solucionar problemas de Azure Event Grid.
ms.topic: conceptual
ms.date: 06/10/2021
ms.openlocfilehash: 94370af8a3325d1798c3e2bcb65c2ccb3e54a43b
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546355"
---
# <a name="troubleshoot-azure-event-grid-issues"></a>Solución de problemas de Azure Event Grid
En este artículo se proporciona información que lo ayuda a solucionar problemas de Azure Event Grid. 

## <a name="diagnostic-logs"></a>Registros de diagnóstico
Habilite la configuración de diagnóstico para temas o dominios de Event Grid para capturar y ver los registros de errores de publicación y entrega. Para obtener más información, consulte [Registros de diagnóstico](enable-diagnostic-logs-topic.md).

## <a name="metrics"></a>Métricas
Puede ver las métricas de los temas y las suscripciones de Event Grid y crear alertas sobre ellos. Para más información, consulte [Métricas de Event Grid](monitor-event-delivery.md).

## <a name="alerts"></a>Alertas
Cree alertas sobre métricas y operaciones de registro de actividad de Azure Event Grid. Para obtener más información, vea [Alertas de métricas y registros de actividad de Event Grid](set-alerts.md).

## <a name="subscription-validation-issues"></a>Problemas de validación de suscripciones
Durante la creación de la suscripción a eventos, es posible que reciba un mensaje de error que indica que no se pudo completar la validación del punto de conexión proporcionado. Para solucionar problemas de validación de suscripciones, consulte [Solución de problemas de validación de suscripciones de Azure Event Grid](troubleshoot-subscription-validation.md). 

## <a name="network-connectivity-issues"></a>Problemas de conectividad de red
Hay varias razones por las que las aplicaciones cliente no pueden conectarse a un tema o dominio de Event Grid. Los problemas de conectividad que experimenta pueden ser permanentes o transitorios. Para obtener información sobre cómo resolver estos problemas, consulte [Solución de problemas de conectividad](troubleshoot-network-connectivity.md).

## <a name="error-codes"></a>Códigos de error
Si recibe mensajes de error con códigos de error como 400, 409 y 403, consulte [Solución de problemas de Azure Event Grid](troubleshoot-errors.md). 

## <a name="distributed-tracing"></a>Seguimiento distribuido
Las bibliotecas de Event Grid en .NET, Java, Python y JavaScript admiten el seguimiento distribuido. Para adherirse a la [Guía de la especificación de CloudEvents](https://github.com/cloudevents/spec/blob/v1.0.1/extensions/distributed-tracing.md) sobre el seguimiento distribuido, la biblioteca establece los atributos `traceparent` y `tracestate` de una extensión `CloudEvent` cuando el seguimiento distribuido está habilitado.

Para obtener más información sobre cómo habilitar el seguimiento distribuido en la aplicación, consulte la documentación de seguimiento distribuido de Azure SDK:

- [.NET](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/core/Azure.Core/samples/Diagnostics.md#Distributed-tracing)
- [Java](/azure/developer/java/sdk/tracing)
- [Python](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/core/azure-core-tracing-opentelemetry)
- [JavaScript](https://github.com/Azure/azure-sdk-for-js/blob/main/sdk/core/README.md#tracing)

Para habilitar el seguimiento de un extremo a otro para una suscripción a Event Grid de [Azure Event Hubs](handler-event-hubs.md) o [Azure Service Bus](handler-service-bus.md), configure las [propiedades de entrega personalizadas](delivery-properties.md) para reenviar el atributo de extensión CloudEvent `traceparent` a la propiedad de la aplicación AMQP `Diagnostic-Id`. 

Este es un ejemplo de una suscripción que tiene propiedades de entrega de seguimiento configuradas para Event Hubs:

```azurecli
az eventgrid event-subscription create --name <event-grid-subscription-name> \
    --source-resource-id <event-grid-resource-id>
    --endpoint-type eventhub \
    --endpoint <event-hubs-endpoint> \
    --delivery-attribute-mapping Diagnostic-Id dynamic traceparent
```

### <a name="sample"></a>Muestra
Vea el [ejemplo de contador de líneas](/samples/azure/azure-sdk-for-net/line-counter/). En esta aplicación de ejemplo se muestra el uso de los clientes Storage, Event Hubs, y Event Grid junto con la integración de ASP.NET Core, el seguimiento distribuido y los servicios hospedados. Permite a los usuarios cargar un archivo en un blob, lo que desencadena un evento de Event Hubs que contiene el nombre de archivo. El procesador de Event Hubs recibe el evento y, a continuación, la aplicación descarga el blob y cuenta el número de líneas del archivo. La aplicación muestra un vínculo a una página que contiene el recuento de líneas. Cuando se hace clic en el vínculo, se publica un evento CloudEvent que contiene el nombre del archivo mediante Event Grid.

## <a name="next-steps"></a>Pasos siguientes
Si necesita más ayuda, publique su problema en el [foro de Stack Overflow](https://stackoverflow.com/questions/tagged/azure-eventgrid) o abra una [incidencia de soporte técnico](https://azure.microsoft.com/support/options/). 
