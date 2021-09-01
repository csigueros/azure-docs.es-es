---
title: Niveles premium y estándar de Azure Service Bus
description: En este artículo se describen los niveles estándar y premium de Azure Service Bus. Compara estos niveles y proporciona diferencias técnicas.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: c6c520219c383a21d8d2e134d0798f3cb5058c2d
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114202428"
---
# <a name="service-bus-premium-and-standard-messaging-tiers"></a>Niveles de mensajería Premium y Estándar de Service Bus

La mensajería de Service Bus, que incluye entidades como colas y temas, combina las funcionalidades de la mensajería empresarial con una completa semántica de publicación-suscripción en la escala de nube. La mensajería de Service Bus se utiliza como la red troncal de comunicación para muchas soluciones sofisticadas en la nube.

El nivel *Premium* de la mensajería de Service Bus atiende solicitudes comunes de los clientes con relación a la escala, el rendimiento y la disponibilidad para aplicaciones fundamentales. El nivel Premium se recomienda para escenarios de producción. Aunque los conjuntos de características son prácticamente idénticos, estos dos niveles de mensajería de Service Bus están diseñados para usarse en distintas situaciones.

En la tabla siguiente, se resaltan algunas de las principales diferencias.

| Premium | Estándar |
| --- | --- |
| Capacidad de proceso elevada |Capacidad de proceso variable |
| Rendimiento predecible |Latencia variable |
| Precio fijo |Precios según la variante de pago por uso |
| Posibilidad de escalar y de reducir verticalmente la carga de trabajo |N/D |
| Tamaño de mensaje de hasta 1 MB. Actualmente existe [compatibilidad con cargas de mensajes de hasta 100 MB](#large-messages-support-preview) en versión preliminar. |Tamaño de mensaje de hasta 256 KB |

La **mensajería Premium de Service Bus** proporciona aislamiento de recursos en el nivel de CPU y memoria para que cada carga de trabajo de cliente se ejecute de forma aislada. Este contenedor de recursos se llama *unidad de mensajería*. A cada espacio de nombres premium se le asigna al menos una unidad de mensajería. Puede comprar 1, 2, 4, 8 o 16 unidades de mensajería para cada espacio de nombres Premium de Service Bus. Una sola carga de trabajo o entidad puede abarcar varias unidades de mensajería y el número de unidades de mensajería puede cambiarse a voluntad. El resultado es un rendimiento predecible y repetible para su solución basada en Service Bus.

Este rendimiento no es solo más predecible y presenta mayor disponibilidad, sino que también es más rápido. Con la mensajería Premium, obtener el máximo rendimiento es mucho más rápido que en el nivel Estándar.

> [!NOTE]
> El límite de tamaño del lote para la mensajería Premium es 1 MB.

## <a name="premium-messaging-technical-differences"></a>Diferencias técnicas de la mensajería Premium

En las secciones siguientes se describen algunas diferencias existentes entre los niveles de mensajería Estándar y Premium.

### <a name="partitioned-queues-and-topics"></a>Temas y colas con particiones

Los temas y colas con particiones no se admiten en Mensajería Premium. Para más información sobre las particiones, consulte [Temas y colas con particiones](service-bus-partitioning.md).

### <a name="express-entities"></a>Entidades exprés

Dado que la Mensajería Premium se ejecuta en un entorno de tiempo de ejecución aislado, no se admiten entidades rápidas en los espacios de nombres Premium. Una entidad express conserva un mensaje en la memoria temporalmente antes de escribirlo en el almacenamiento persistente. Si tiene código que se ejecuta en la mensajería estándar y desea trasladarlo al nivel Premium, asegúrese de que la característica de entidad express está deshabilitada.

## <a name="premium-messaging-resource-usage"></a>Uso de recursos de mensajería premium
En general, cualquier operación en una entidad puede causar uso de CPU y memoria. Estas son algunas de las operaciones: 

- Operaciones de administración como CRUD (creación recuperación, actualización y eliminación) en colas, temas y suscripciones.
- Operaciones en tiempo de ejecución (enviar y recibir mensajes).
- Operaciones de supervisión y alertas.

El uso de memoria y CPU adicional no tiene un costo adicional. Para el nivel de Mensajería Premium, hay un precio único por unidad de mensaje.

El uso de CPU y memoria se supervisa y se muestra por los motivos siguientes: 

- Proporcionar transparencia en los aspectos internos del sistema.
- Entender la capacidad de los recursos adquiridos.
- Permitir el planeamiento de capacidad que le ayuda a escalar o reducir verticalmente.

## <a name="messaging-unit---how-many-are-needed"></a>Unidad de mensajería: ¿cuántas se necesitan?

Al aprovisionar un espacio de nombres Premium de Azure Service Bus, debe especificarse el número de unidades de mensajería asignadas. Estas unidades de mensajería son recursos dedicados que se asignan al espacio de nombres.

El número de unidades de mensajería asignadas al espacio de nombres Premium de Service Bus se puede **ajustar dinámicamente** para factorizar el cambio (aumento o disminución) de las cargas de trabajo.

Hay varios factores que se deben tener en cuenta a la hora de decidir el número de unidades de mensajería que va a tener una arquitectura:

- Comience con ***1 o 2 unidades de mensajería*** asignadas al espacio de nombres.
- Estudie las métricas de uso de la CPU en [Métricas de uso de recursos](monitor-service-bus-reference.md#resource-usage-metrics) para el espacio de nombres.
    - Si el uso de CPU está por ***debajo del 20 %** , es posible que pueda *_reducir verticalmente_** el número de unidades de mensajería asignadas al espacio de nombres.
    - Si el uso de CPU está ***por encima del 70 %** , la aplicación se beneficiará del *_escalado vertical_** del número de unidades de mensajería asignadas al espacio de nombres.

Para obtener información sobre cómo configurar un espacio de nombres de Service Bus para escalar automáticamente (aumentar o disminuir las unidades de mensajería), consulte [Actualización automática de unidades de mensajería](automate-update-messaging-units.md).

> [!NOTE]
> El **escalado** de los recursos asignados al espacio de nombres puede ser preventivo o reactivo.
>
>  * **Preventivo**: Si se espera una carga de trabajo adicional (debido a la estacionalidad o a determinadas tendencias), puede seguir asignando más unidades de mensajería al espacio de nombres antes de que lleguen las cargas de trabajo.
>
>  * **Reactivo**: Si se identifican cargas de trabajo adicionales mediante el estudio de las métricas de uso de los recursos, se pueden asignar recursos adicionales al espacio de nombres para satisfacer una mayor demanda.
>
> Los medidores de facturación de Service Bus son por horas. En el caso del escalado vertical, solo paga por los recursos adicionales durante las horas en que se usaron.
>

## <a name="get-started-with-premium-messaging"></a>Introducción a la Mensajería premium

La introducción a la mensajería premium es muy sencilla y el proceso es similar al de la mensajería estándar. Comience por [crear un espacio de nombres](service-bus-create-namespace-portal.md) en [Azure Portal](https://portal.azure.com). Asegúrese de que selecciona **Premium** en **Plan de tarifa**. Haga clic en **Ver todos los detalles de los precios** para ver más información sobre cada nivel.

:::image type="content" source="./media/service-bus-premium-messaging/select-premium-tier.png" alt-text="Captura de pantalla que muestra la selección del nivel Premium al crear un espacio de nombres.":::

También puede crear un [espacios de nombres premium con plantillas de Azure Resource Manager](https://azure.microsoft.com/resources/templates/servicebus-pn-ar/).

## <a name="large-messages-support-preview"></a>Compatibilidad con mensajes grandes (versión preliminar)
Los espacios de nombres de nivel Prémium de Azure Service Bus admiten la capacidad de enviar cargas de mensajes grandes, de hasta 100 MB. Esta característica está destinada principalmente a cargas de trabajo heredadas que han usado cargas de mensajes mayores en otros agentes de mensajería empresarial y buscan migrar sin problemas a Azure Service Bus.

Estas son algunas consideraciones que deben tenerse en cuenta al enviar mensajes grandes en Azure Service Bus:
   * Solo se admite en espacios de nombres de nivel Prémium de Azure Service Bus.
   * Solo se admite cuando se usa el protocolo AMQP. No se admite cuando se usa el protocolo SBMP.
   * Se admite cuando se usa el [SDK de cliente de Java Message Service (JMS) 2.0](how-to-use-java-message-service-20.md) y otros SDK de cliente de lenguaje.
   * El envío de mensajes grandes dará lugar a una disminución del rendimiento y una mayor latencia.
   * Aunque se admiten cargas de mensajes de 100 MB, se recomienda que las cargas de mensajes sean lo más pequeñas posible, con el fin de garantizar un rendimiento confiable desde el espacio de nombres de Service Bus.
   * El tamaño máximo del mensaje solo se aplica a los mensajes enviados a la cola o al tema. El límite de tamaño no se aplica para la operación de recepción. Esto permite actualizar el tamaño máximo del mensaje para una cola (o tema) determinada.

### <a name="enabling-large-messages-support-for-a-new-queue-or-topic"></a>Habilitación de la compatibilidad con mensajes de gran tamaño en una nueva cola (o tema)

Para habilitar la compatibilidad con mensajes grandes, establezca el tamaño máximo del mensaje al crear una cola (o tema), como se muestra a continuación. 

:::image type="content" source="./media/service-bus-premium-messaging/large-message-preview.png" alt-text="Captura de pantalla que muestra cómo habilitar la compatibilidad con mensajes grandes en una cola existente.":::

### <a name="enabling-large-messages-support-for-an-existing-queue-or-topic"></a>Habilitación de la compatibilidad con mensajes de gran tamaño en una cola (o tema) existente

También puede habilitar la compatibilidad con mensajes grandes para colas existentes (o temas), actualizando el **tamaño máximo del mensaje** en la **_información general_** de esa cola (o tema) específica, como se muestra a continuación.

:::image type="content" source="./media/service-bus-premium-messaging/large-message-preview-update.png" alt-text="Captura de pantalla de la página Crear cola con compatibilidad con mensajes grandes habilitada.":::

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Mensajería de Service Bus, consulte los vínculos siguientes:

- [Actualización automática de unidades de mensajería](automate-update-messaging-units.md).
- [Introducción a la mensajería Premium de Azure Service Bus (entrada de blog)](https://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Introducción a la mensajería Premium de Azure Service Bus (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)


