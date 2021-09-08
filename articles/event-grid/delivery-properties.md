---
title: 'Azure Event Grid: Establecimiento de encabezados personalizados en eventos entregados'
description: Describe el establecimiento de encabezados personalizados (o propiedades de entrega) en eventos entregados.
ms.topic: conceptual
ms.date: 08/13/2021
ms.openlocfilehash: 3600d74d91ad218f3fcab99002762d605fba3139
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831355"
---
# <a name="custom-delivery-properties"></a>Propiedades de entrega personalizadas
Las suscripciones a eventos permiten configurar encabezados HTTP que se incluyen en los eventos entregados. Esta capacidad permite establecer encabezados personalizados que un destino requiere. Puede configurar hasta 10 encabezados al crear una suscripción de eventos. Cada valor de encabezado no debe ser mayor que 4 096 (4 K) bytes.

Puede establecer encabezados personalizados en los eventos que se entregan a los destinos siguientes:

- webhooks
- Temas y colas de Azure Service Bus
- Azure Event Hubs
- Retransmisión de conexiones híbridas

Al crear una suscripción de eventos en Azure Portal, puede usar la pestaña **Propiedades de entrega** para establecer encabezados HTTP personalizados. Esta página le permite establecer valores de encabezado fijos y dinámicos.

## <a name="setting-static-header-values"></a>Configuración de valores de encabezado estáticos
Para establecer los encabezados con un valor fijo, proporcione el nombre del encabezado y su valor en los campos correspondientes:

:::image type="content" source="./media/delivery-properties/static-header-property.png" alt-text="Propiedades de entrega: estáticas":::

Es posible que desee seleccionar la opción **¿Es secreto?** al proporcionar información confidencial. Los datos confidenciales no se mostrarán en Azure Portal. 

## <a name="setting-dynamic-header-values"></a>Configuración de valores de encabezado dinámicos
Puede establecer el valor de un encabezado basándose en una propiedad de un evento de entrada. Use la sintaxis JsonPath para hacer referencia al valor de propiedad de un evento entrante que se va a usar como valor de un encabezado en las solicitudes salientes. Por ejemplo, para establecer el valor de un encabezado denominado **Channel** mediante el valor de la propiedad del evento entrante **system** de los datos de evento, configure la suscripción a eventos de la siguiente manera:

:::image type="content" source="./media/delivery-properties/dynamic-header-property.png" alt-text="Propiedades de entrega: dinámicas":::

## <a name="use-azure-cli"></a>Uso de CLI de Azure
Use el parámetro `--delivery-attribute-mapping` al crear una suscripción mediante el comando `az eventgrid event-subscription create`. Este es un ejemplo:

```azurecli
az eventgrid event-subscription create -n es1 \
    --source-resource-id /subscriptions/{SubID}/resourceGroups/{RG}/providers/Microsoft.EventGrid/topics/topic1
    --endpoint-type storagequeue \
    --endpoint /subscriptions/{SubID}/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/sa1/queueservices/default/queues/q1 \
    --enable-advanced-filtering-on-arrays true
    --delivery-attribute-mapping staticproperty1 static somestaticvalue2 true 
    --delivery-attribute-mapping staticproperty2 static somestaticvalue3 false 
    --delivery-attribute-mapping dynamicproperty1 dynamic data.key1
```

## <a name="examples"></a>Ejemplos
En esta sección se proporcionan algunos ejemplos del uso de propiedades de entrega.

### <a name="setting-the-authorization-header-with-a-bearer-token-non-normative-example"></a>Configuración del encabezado de autorización con un token de portador (ejemplo no normativo)

Establezca un valor en un encabezado de autorización para identificar la solicitud con el controlador de webhook. Se puede establecer un encabezado de autorización si no va [a proteger el webhook con Azure Active Directory](secure-webhook-delivery.md).

| Nombre de encabezado   | Tipo de encabezado | Valor de encabezado |
| :--           | :--         | :--            |
|`Authorization` | estática | `BEARER SlAV32hkKG...`|

Ahora, las solicitudes salientes deben contener el encabezado establecido en la suscripción a eventos:

```console
POST /home.html HTTP/1.1
Host: acme.com

Authorization: BEARER SlAV32hkKG...
```

> [!NOTE]
> La definición de los encabezados de autorización es una opción razonable cuando el destino es un webhook. No debe usarse para las [funciones suscritas con un identificador de recurso](/rest/api/eventgrid/version2020-06-01/eventsubscriptions/createorupdate#azurefunctioneventsubscriptiondestination), Service Bus, Event Hubs y Conexiones híbridas ya que esos destinos admiten sus propios esquemas de autenticación cuando se usan con Event Grid.

### <a name="service-bus-example"></a>Ejemplo de Service Bus
Azure Service Bus permite el uso de las propiedades de mensaje siguientes al enviar mensajes individuales. 

| Nombre de encabezado | Tipo de encabezado |
| :-- | :-- |
| `MessageId` | Dinámica |  
| `PartitionKey` | Estático o dinámico |
| `SessionId` | Estático o dinámico |
| `CorrelationId` | Estático o dinámico |
| `Label` | Estático o dinámico |
| `ReplyTo` | Estático o dinámico | 
| `ReplyToSessionId` | Estático o dinámico |
| `To` |Estático o dinámico |
| `ViaPartitionKey` | Estático o dinámico |

> [!NOTE]
> - El valor predeterminado de `MessageId` es el identificador interno del evento de Event Grid. Puede invalidarlo. Por ejemplo, `data.field`.
> - Solo puede establecer `SessionId` o `MessageId`. 

### <a name="event-hubs-example"></a>Ejemplo de Event Hubs

Si necesita publicar eventos en una partición concreta de un centro de eventos, establezca la propiedad `ParitionKey` en la suscripción a eventos para especificar la clave de partición que identifica la partición del centro de eventos de destino.

| Nombre de encabezado | Tipo de encabezado |
| :-- | :-- |
|`PartitionKey` | estática |


### <a name="configure-time-to-live-on-outgoing-events-to-azure-storage-queues"></a>Configuración del período de vida de los eventos salientes de las colas de Azure Storage
En el caso del destino de las colas de Azure Storage, solo puede configurar el período de vida que tendrá el mensaje saliente una vez que se haya entregado a una cola de Azure Storage. Si no se proporciona ningún período, el período de vida predeterminado del mensaje es de 7 días. También puede establecer el evento para que no expire nunca.

:::image type="content" source="./media/delivery-properties/delivery-properties-storage-queue.png" alt-text="Propiedades de entrega: cola de Storage":::

## <a name="next-steps"></a>Pasos siguientes
Para más información acerca de la entrega de eventos, consulte los siguientes artículos:

- [Entrega y nuevo intento](delivery-and-retry.md)
- [Entrega de eventos de webhook](webhook-event-delivery.md)
- [Filtro de evento](event-filtering.md)
