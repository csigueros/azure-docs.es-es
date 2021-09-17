---
title: Guía de rendimiento del servicio Azure Web PubSub
description: Información general sobre el rendimiento y la prueba comparativa del servicio Azure Web PubSub. Métricas clave que se deben tener en cuenta al planear la capacidad.
ms.service: azure-web-pubsub
ms.topic: conceptual
ms.date: 5/12/2021
ms.author: biqian
author: bjqian
ms.openlocfilehash: b27fa03f999e8f50f4f14c4ce1debd330c305d75
ms.sourcegitcommit: 5d605bb65ad2933e03b605e794cbf7cb3d1145f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122598317"
---
# <a name="performance-guide-for-azure-web-pubsub-service"></a>Guía de rendimiento del servicio Azure Web PubSub

Una de las principales ventajas de usar el servicio Azure Web PubSub es la facilidad de escalado de las aplicaciones ascendentes de Web PubSub. En un escenario a gran escala, el rendimiento es un factor importante. 

En esta guía, presentaremos los factores que afectan al rendimiento de las aplicaciones ascendentes de Web PubSub. Vamos a describir un rendimiento típico en distintos escenarios de casos de uso. 

## <a name="term-definitions"></a>Definiciones de términos

*Entrante*: el mensaje entrante al servicio Azure Web PubSub.

*Saliente*: el mensaje saliente del servicio Azure Web PubSub.

*Ancho de banda*: el tamaño total de todos los mensajes en 1 segundo.

## <a name="overview"></a>Información general

El servicio Azure Web PubSub define siete niveles estándar para distintas capacidades de rendimiento. En esta guía se responden las preguntas siguientes:

- ¿Cuál es el rendimiento típico del servicio Azure Web PubSub para cada nivel?

- ¿El servicio Azure Web PubSub cumple mis requisitos de rendimiento de mensajes (por ejemplo, envío de 100 000 mensajes por segundo)?

- Para mi escenario concreto, ¿qué nivel es adecuado para mí? O bien, ¿cómo puedo seleccionar el nivel adecuado?

Para responder estas preguntas, en esta guía primero se proporciona una explicación detallada de los factores que afectan al rendimiento. Luego, se muestra el número máximo de mensajes entrantes y salientes de cada nivel para casos de uso típicos: **envío a grupos a través del subprotocolo de Web PubSub**, **ascendente** y **API REST**.

En esta guía no se pueden tratar todos los escenarios (ni casos de uso, tamaños de mensaje, patrones de envío de mensajes, etc. diferentes). Pero proporciona información básica para comprender las limitaciones de rendimiento.

## <a name="performance-insight"></a>Conclusión sobre el rendimiento

En esta sección se describen las metodologías de evaluación del rendimiento y luego se enumeran todos los factores que afectan al rendimiento. Al final, se proporcionan métodos para ayudarle a evaluar los requisitos de rendimiento.

### <a name="methodology"></a>Metodología

El *rendimiento* y la *latencia* son dos aspectos típicos de la comprobación del rendimiento. Para el servicio Azure Web PubSub, cada nivel de SKU tiene su propia directiva de limitación de rendimiento. La directiva define *el rendimiento máximo permitido (ancho de banda entrante y saliente)* como el rendimiento máximo obtenido cuando el 99 % de los mensajes tiene una latencia menor que 1 segundo.

### <a name="performance-factors"></a>Factores de rendimiento

En teoría, la capacidad del servicio Azure Web PubSub se ve limitada por los recursos de cálculo: CPU, memoria y red. Por ejemplo, un mayor número de conexiones al servicio Azure Web PubSub hace que el servicio use más memoria. Para mayor tráfico de mensajes (por ejemplo, cada mensaje es mayor que 2048 bytes), el servicio Azure Web PubSub debe dedicar más ciclos de CPU para procesar el tráfico.

El costo de enrutamiento de mensajes también limita el rendimiento. El servicio Azure Web PubSub desempeña un papel como agente de mensajes, que enruta el mensaje entre un conjunto de clientes. Un escenario o API diferente requiere una directiva de enrutamiento diferente. 

Para **echo**, el cliente envía un mensaje al servidor ascendente, y este devuelve el mensaje al cliente. Este patrón tiene el menor costo de enrutamiento. Sin embargo, para **difusión**, **enviar al grupo** y **enviar a conexión**, el servicio Azure Web PubSub debe buscar las conexiones de destino a través de la estructura de datos internos distribuidos. Este procesamiento adicional usa más recursos de la CPU, memoria y ancho de banda de red. Como resultado, el rendimiento es más lento.

En resumen, los siguientes factores afectan a la capacidad de entrada y salida:

-   Nivel de SKU (CPU y memoria)

-   Número de conexiones

-   Tamaño del mensaje

-   Velocidad de envío de mensajes

-   Escenario de caso de uso (costo de enrutamiento)


### <a name="finding-a-proper-sku"></a>Encontrar una SKU adecuada

¿Cómo se puede evaluar la capacidad de entrada y salida o buscar el nivel adecuado para un caso de uso específico?

Cada nivel tiene su propio ancho de banda entrante y saliente máximo. No se garantiza una experiencia de usuario agilizada cuando el tráfico de entrada o salida supera el límite. 

```
  inboundBandwidth = inboundConnections * messageSize / sendInterval
  outboundBandwidth = outboundConnections * messageSize / sendInterval
```

- *inboundConnections*: el número de conexiones que envían el mensaje.
- *outboundConnections*: el número de conexiones que reciben el mensaje.
- *messageSize*: el tamaño de un único mensaje (valor medio). Un mensaje pequeño de menos de 1024 bytes tiene un impacto sobre el rendimiento que es similar a un mensaje de 1024 bytes.
- *sendInterval:* el intervalo para enviar mensajes. Por ejemplo, 1 segundo significa que se envía un mensaje cada segundo. Un intervalo más pequeño significa enviar más mensajes en un período de tiempo. Por ejemplo, 0,5 segundos significa enviar dos mensajes cada segundo.
- *Connections*: el umbral máximo confirmado del servicio Azure Web PubSub para cada nivel. Se limitan las conexiones que superan el umbral.

Suponga que el servidor ascendente es lo suficientemente eficaz y no es el cuello de botella del rendimiento. Luego, compruebe el ancho de banda entrante y saliente máximo para cada nivel.

## <a name="case-study"></a>Caso práctico

Las secciones siguientes pasan por tres casos de uso típicos: **envío a grupos a través del subprotocolo de Web PubSub**, **desencadenamiento de CloudEvent** y **llamada API REST**. Para cada escenario, en la sección se muestra la capacidad actual de entrada y salida para el servicio Azure Web PubSub. También se explican los principales factores que afectan al rendimiento.

En todos los casos de uso, el tamaño predeterminado de mensaje es 2048 bytes y el intervalo de envío de mensajes es 1 segundo.

### <a name="send-to-groups-through-web-pubsub-subprotocol"></a>Envío a grupos a través del subprotocolo Web PubSub
El servicio admite un subprotocolo específico denominado `json.webpubsub.azure.v1`, que permite a los clientes publicar o suscribirse directamente en lugar de realizar un recorrido de ida y vuelta al servidor ascendente. Este escenario es eficaz, ya que no hay ningún servidor involucrado, y todo el tráfico pasa a través de la conexión WebSocket del servicio cliente.

![Diagrama que muestra el flujo de trabajo Enviar al grupo.](./media/concept-performance/group.png)

Los miembros de grupo y el número de grupos son dos factores que afectan al rendimiento. Para simplificar el análisis, se definen dos tipos de grupos:

- **Grupo grande**: el número de grupos es siempre 10. El número de miembros del grupo es igual a (número máximo de conexiones) / 10. Por ejemplo, para Unidad1, si hay 1000 números de conexión, cada grupo tiene 1000 / 10 = 100 miembros.
- **Grupo pequeño**: cada grupo tiene 10 conexiones. El número de grupos es igual a (número máximo de conexiones) / 10. Por ejemplo, para Unidad1, si hay 1000 números de conexión, tenemos 1000 / 10 = 100 grupos.

**Enviar al grupo** agrega un costo de enrutamiento al servicio Azure Web PubSub porque debe buscar las conexiones de destino a través de una estructura de datos distribuidos. A medida que aumentan las conexiones de envío, aumenta el coste.

##### <a name="big-group"></a>Grupo grande

Para **enviar a grupo grande**, el ancho de banda saliente se convierte en el cuello de botella antes de alcanzar el límite de coste de enrutamiento. En la tabla siguiente se enumera el ancho de banda de salida máximo.

|   Enviar a grupo grande       | Unidad1 | Unidad2 | Unidad5  | Unidad10 | Unidad20  | Unidad50 | Unidad100 |
|---------------------------|-------|-------|--------|--------|------- |--------|---------|
| Conexiones               | 1,000 | 2\.000 | 5\.000  | 10 000 | 20.000 | 50.000  | 100 000|
| Número de miembros del grupo        | 100   | 200   | 500    | 1,000  | 2\.000  | 5\.000   | 10 000 |
| Número de grupos               | 10    | 10    | 10     | 10     | 10     | 10      | 10|
| Mensajes entrantes por segundo  | 30      | 30      | 30      | 30    | 30    | 30   | 30     |
| Ancho de banda entrante  | 60 kbps | 60 kbps  | 60 kbps   | 60 kbps  | 60 kbps   | 60 kbps    | 60 kbps    |
| Mensajes salientes por segundo | 3,000 | 6,000 | 15,000 | 30,000 | 60 000 | 150 000 | 300 000 |
| Ancho de banda saliente | **6 Mbps** | **12 Mbps** | **30 Mbps** | **60 Mbps** | **120 Mbps** | **300 MBps** | **600 MBps** |

##### <a name="small-group"></a>Grupo pequeño

El coste de enrutamiento es importante para enviar mensajes a muchos grupos pequeños. Actualmente, la implementación del servicio Azure Web PubSub alcanza el límite de costos de enrutamiento en Unidad50. La adición de más CPU y memoria no ayuda, por lo que, por diseño, Unidad100 no puede mejorar más. Si necesita más ancho de banda entrante, póngase en contacto con el soporte técnico.

|   Enviar a un grupo pequeño     | Unidad1 | Unidad2 | Unidad5  | Unidad10 | Unidad20  | Unidad50 | Unidad100 |
|---------------------------|-------|-------|--------|--------|--------|--------|---------|
| Conexiones               | 1,000 | 2\.000 | 5\.000  | 10 000 | 20.000 | 50.000 | 100 000 |
| Número de miembros del grupo        | 10    | 10    | 10     | 10     | 10     | 10     | 10 |
| Número de grupos               | 100   | 200   | 500    | 1,000  | 2\.000  | 5\.000  | 10 000 |
| Mensajes entrantes por segundo  | 400 | 800  | 2\.000 | 4\.000 | 8,000 | 15,000 | 15,000 |
| Ancho de banda entrante  | 800 Kbps | 1,6 Mbps | 4 MBps    | 8 MBps    | 16 MBps   | 30 Mbps  | 30 Mbps   |
| Mensajes salientes por segundo | 4\.000 | 8,000 | 20.000 | 40.000 | 80 000 | 150 000 | 150 000 |
| Ancho de banda saliente | **8 MBps** | **16 MBps** | **40 MBps** | **80 MBps** | **160 Mbps** | **300 MBps** | **300 MBps** |

### <a name="triggering-cloud-event"></a>Desencadenamiento de eventos en la nube 
El servicio entrega eventos de cliente al webhook ascendente mediante el [protocolo HTTP CloudEvents](./reference-cloud-events.md).

![El webhook ascendente](./media/concept-performance/upstream.png)

Para cada evento, formula una solicitud HTTP POST al servidor ascendente registrado y espera una respuesta HTTP. 

> [!NOTE]
> Web PubSub también admite HTTP 2.0 para la entrega de eventos ascendentes. El resultado siguiente se prueba con HTTP 1.1. Si su servidor de aplicaciones admite HTTP 2.0, el rendimiento será mejor.

#### <a name="echo"></a>Eco

En este caso, el servidor de aplicaciones vuelve a escribir el mensaje original en la respuesta HTTP. El comportamiento de **eco** determina que el ancho de banda entrante máximo es igual que el ancho de banda saliente máximo. Consulte la tabla siguiente para más detalles:

|       Eco                        | Unidad1 | Unidad2 | Unidad5 | Unidad10 | Unidad20 | Unidad50 | Unidad100 |
|-----------------------------------|-------|-------|-------|--------|--------|--------|---------|
| Conexiones                       | 1,000 | 2\.000 | 5\.000 | 10 000 | 20.000 | 50.000 | 100 000 |
| Mensajes entrantes y salientes por segundo | 500 | 1,000 | 2,500 | 5\.000 | 10 000 | 25 000 | 50.000 |
| Ancho de banda entrante y saliente | **1 Mbps** | **2 MBps** | **5 Mbps** | **10 MBps** | **20 MBps** | **50 Mbps** | **100 MBps** |



### <a name="rest-api"></a>API de REST

Azure Web PubSub brinda [API eficaces](/rest/api/webpubsub/) para administrar clientes y entregar mensajes en tiempo real.

![Diagrama que muestra el flujo de trabajo general del servicio Web PubSub mediante las API REST.](./media/concept-performance/rest-api.png)

#### <a name="send-to-user-through-rest-api"></a>Enviar al usuario a través de la API de REST
El banco de pruebas asigna nombres de usuario a todos los clientes antes de estos empiecen a conectarse al servicio Azure Web PubSub. 

|   Enviar al usuario a través de la API de REST | Unidad1 | Unidad2 | Unidad5  | Unidad10 | Unidad20 | Unidad50  | Unidad100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Conexiones               | 1,000 | 2\.000 | 5\.000  | 10 000 | 20.000 | 50.000  | 100 000 |
| Mensajes entrantes y salientes por segundo | 180 | 360 | 900    | 1800 | 3,600 | 9000 | 18 000  |
| Ancho de banda entrante y saliente           | **360 kbps** | **720 kbps** | **1,8 MBps** | **3,6 Mbps** | **7,2 Mbps** | **18 Mbps** | **36 MBps** |

#### <a name="broadcast-through-rest-api"></a>Difusión a través de la API de REST
El límite de ancho de banda es el mismo que para **enviar a grupo grande**.

|   Difusión a través de la API de REST     | Unidad1 | Unidad2 | Unidad5  | Unidad10 | Unidad20 | Unidad50  | Unidad100 |
|---------------------------|-------|-------|--------|--------|--------|---------|---------|
| Conexiones               | 1,000 | 2\.000 | 5\.000  | 10 000 | 20.000 | 50.000  | 100 000 |
| Mensajes entrantes por segundo  | 3    | 3    | 3     | 3     | 3     | 3      | 3      |
| Mensajes salientes por segundo | 3,000 | 6,000 | 15,000 | 30,000 | 60 000 | 150 000 | 300 000 |
| Ancho de banda entrante  | 6 kbps   | 6 kbps   | 6 kbps    | 6 kbps    | 6 kbps    | 6 kbps     | 6 kbps     |
| Ancho de banda saliente | **6 Mbps** | **12 Mbps** | **30 Mbps** | **60 Mbps** | **120 Mbps** | **300 MBps** | **600 MBps** |

## <a name="next-steps"></a>Pasos siguientes

[!INCLUDE [next step](includes/include-next-step.md)]