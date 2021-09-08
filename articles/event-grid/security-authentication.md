---
title: Autenticación de la entrega de eventos en los controladores de eventos (Azure Event Grid).
description: En este artículo se describen diferentes formas de autenticar los controladores de eventos en Azure Event Grid.
ms.topic: conceptual
ms.date: 06/28/2021
ms.openlocfilehash: 01383809e6aab895ff4ed42763c57004a6ee02a8
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003240"
---
# <a name="authenticate-event-delivery-to-event-handlers-azure-event-grid"></a>Autenticación de la entrega de eventos en los controladores de eventos (Azure Event Grid).
En este artículo se proporciona información sobre cómo autenticar la entrega de eventos a los controladores de eventos. 

## <a name="overview"></a>Información general
Azure Event Grid utiliza diferentes métodos de autenticación para entregar eventos a controladores de eventos. `

| Método de autenticación | Controladores admitidos | Descripción  |
|--|--|--|
Clave de acceso | <p>Event Hubs</p><p>Azure Service Bus</p><p>Colas de almacenamiento</p><p>Retransmisión de conexiones híbridas</p><p>Azure Functions</p><p>Storage Blobs (Deadletter)&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;</p> | Las claves de acceso se capturan con las credenciales de la entidad de servicio de Event Grid. Los permisos se conceden a Event Grid al registrar el proveedor de recursos de Event Grid en su suscripción de Azure. |  
Identidad de sistema administrado <br/>&<br/> Control de acceso basado en rol | <p>Event Hubs</p><p>Azure Service Bus</p><p>Colas de almacenamiento</p><p>Storage Blobs (Deadletter)</p></li></ul> | Habilite la identidad del sistema administrado para el tema y agréguela al rol adecuado en el destino. Para obtener más información, consulte [Usar identidades asignadas por el sistema para la entrega de eventos](#use-system-assigned-identities-for-event-delivery).  |
|Autenticación de token de portador con webhook protegido de Azure AD | webhook | Consulte la sección [Autenticación de la entrega de eventos a los puntos de conexión de webhook](#authenticate-event-delivery-to-webhook-endpoints) para obtener más información. |
Secreto de cliente como parámetro de consulta | webhook | Consulte la sección [Uso del secreto de cliente como parámetro de consulta](#using-client-secret-as-a-query-parameter) para obtener más información. |

## <a name="use-system-assigned-identities-for-event-delivery"></a>Usar identidades asignadas por el sistema para la entrega de eventos.
Puede habilitar una identidad administrada asignada por el sistema para un tema o dominio y usar la identidad para reenviar eventos a destinos compatibles, como colas y temas de Service Bus, centros de eventos y cuentas de almacenamiento.

He aquí los pasos: 

1. Cree un tema o un dominio con una identidad asignada por el sistema, o bien actualice un tema o dominio existente para habilitar la identidad. Para obtener más información, consulte [Habilitación de la identidad administrada para un tema del sistema](enable-identity-system-topics.md) o [Habilitación de la identidad administrada para un tema personalizado o un dominio](enable-identity-custom-topics-domains.md)
1. Agregue la identidad a un rol adecuado (por ejemplo, Remitente de los datos de Service Bus) en el destino (por ejemplo, una cola de Service Bus). Para obtener más información, consulte [Otorgar a la identidad el acceso al destino de Event Grid](add-identity-roles.md)
1. Al crear suscripciones de eventos, habilite el uso de la identidad para enviar eventos al destino. Para obtener más información, consulte [Creación de una suscripción de eventos que usa la identidad](managed-service-identity.md). 

Para obtener instrucciones paso a paso detalladas, consulte [Entrega de eventos con una identidad administrada](managed-service-identity.md).


## <a name="authenticate-event-delivery-to-webhook-endpoints"></a>Autenticación de la entrega de eventos a los puntos de conexión de webhook
En las secciones siguientes se describe cómo autenticar la entrega de eventos a los puntos de conexión de webhook. Use un mecanismo de protocolo de enlace de validación con independencia del método que use. Consulte [Entrega de eventos de webhook](webhook-event-delivery.md) para detalles. 


### <a name="using-azure-active-directory-azure-ad"></a>Uso de Azure Active Directory (Azure AD)
Puede proteger el punto de conexión de webhook que se usa para recibir eventos de Event Grid mediante Azure AD. Tendrá que crear una aplicación de Azure AD, crear un rol y una entidad de servicio en la aplicación que autorice a Event Grid, y configurar la suscripción de eventos para usar la aplicación de Azure AD. Obtenga información sobre cómo [configurar Azure Active Directory con Event Grid](secure-webhook-delivery.md).

### <a name="using-client-secret-as-a-query-parameter"></a>Uso del secreto de cliente como parámetro de consulta
Además, puede proteger el punto de conexión del webhook mediante la incorporación de parámetros de consulta a la dirección URL de destino del webhook, especificada como parte de la creación de una suscripción de eventos. Establezca uno de los parámetros de consulta para que sea un secreto de cliente, como un [token de acceso](https://en.wikipedia.org/wiki/Access_token) o un secreto compartido. El servicio Event Grid incluye todos los parámetros de consulta en cada solicitud de entrega de eventos al webhook. El servicio de webhook puede recuperar y validar el secreto. Si se actualiza el secreto de cliente, también debe actualizarse la suscripción de eventos. Para evitar errores de entrega durante la rotación del secreto, haga que el webhook acepte tanto los secretos antiguos como los nuevos durante un plazo limitado antes de actualizar la suscripción de eventos con el nuevo secreto. 

Como los parámetros de consulta pueden contener secretos de cliente, se controlan con más cuidado. Se almacenan como cifrados y no son accesibles para los operadores del servicio. No se registran como parte de los registros o seguimientos de servicio. Al recuperar las propiedades de la suscripción de eventos, los parámetros de la consulta de destino no se devuelven de forma predeterminada. Por ejemplo: el parámetro [--include-full-endpoint-url](/cli/azure/eventgrid/event-subscription#az_eventgrid_event_subscription_show) se usará en la [CLI](/cli/azure) de Azure.

Para más información sobre cómo entregar eventos a webhooks, consulte [Entrega de eventos de webhook](webhook-event-delivery.md).

> [!IMPORTANT]
> Azure Event Grid solo admite puntos de conexión de webhook **HTTPS**. 

## <a name="endpoint-validation-with-cloudevents-v10"></a>Validación de puntos de conexión con CloudEvents v1.0
Si ya está familiarizado con Event Grid, es posible que conozca el protocolo de enlace de validación de punto de conexión para evitar el uso inapropiado. CloudEvents v1.0 implementa su propia [semántica de protección contra abusos](webhook-event-delivery.md) mediante el método **HTTP OPTIONS**. Para más información al respecto, consulte [Webhooks de HTTP 1.1 para la entrega de eventos: versión 1.0](https://github.com/cloudevents/spec/blob/v1.0/http-webhook.md#4-abuse-protection). Cuando usa el esquema de CloudEvents para la salida, Event Grid usa la protección contra abusos de CloudEvents v1.0 en lugar del mecanismo de eventos de validación de Event Grid. Para obtener más información, vea [Uso del esquema CloudEvents v1.0 con Event Grid](cloudevents-schema.md). 


## <a name="next-steps"></a>Pasos siguientes
Consulte [autenticación de publicación de clientes](security-authenticate-publishing-clients.md) para obtener información sobre la autenticación de clientes que publican eventos en temas o dominios. 
