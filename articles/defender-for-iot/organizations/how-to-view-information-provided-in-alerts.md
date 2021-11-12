---
title: Acerca de los mensajes de alerta
description: Seleccione una alerta en la ventana Alertas para revisar los detalles.
ms.date: 11/09/2021
ms.topic: how-to
ms.openlocfilehash: d76b10943932825388075b7aef44f0f5b94c1d53
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132278576"
---
# <a name="about-alert-messages"></a>Acerca de los mensajes de alerta

Seleccione una alerta en la ventana **Alertas** para revisar los detalles de la alerta. Los detalles incluyen la información siguiente:

- Metadatos de alerta

- Información sobre el tráfico, los dispositivos y el evento

- Vínculos a dispositivos conectados en el mapa de dispositivos

- Comentarios definidos por los analistas y administradores de seguridad

- Recomendaciones para investigar el evento

## <a name="alert-metadata"></a>Metadatos de alerta

Los detalles de la alerta incluyen los siguientes metadatos de la alerta:

  - Id. de alerta

  - Motor de directivas que desencadenó la alerta

  - Fecha y hora en que se generó la alerta.

:::image type="content" source="media/how-to-work-with-alerts-sensor/internet-connectivity-detection-unauthorized.png" alt-text="Se ha detectado una conexión a Internet no autorizada.":::

## <a name="information-about-devices-traffic-and-the-event"></a>Información sobre los dispositivos, el tráfico y el evento

El mensaje de alerta proporciona información sobre:

  - Los dispositivos detectados

  - El tráfico detectado entre los dispositivos, como protocolos y códigos de función

  - Información sobre las implicaciones del evento.

Esta información se puede usar al decidir cómo administrar el evento de alerta.

## <a name="links-to-connected-devices-in-the-device-map"></a>Vínculos a dispositivos conectados en el mapa de dispositivos

Para más información sobre los dispositivos conectados a los dispositivos detectados, puede seleccionar una imagen de dispositivo en la alerta y ver los dispositivos conectados en el mapa.

:::image type="content" source="media/how-to-work-with-alerts-sensor/rcp-operation-failed.png" alt-text="Error en la operación de RCP.":::

:::image type="content" source="media/how-to-work-with-alerts-sensor/devices-screen-populated.png" alt-text="Captura de pantalla de los dispositivos.":::

El mapa filtra por el dispositivo que seleccionó y otros dispositivos conectados a él. El mapa también muestra el cuadro de diálogo **Quick Properties** (Propiedades rápidas) de los dispositivos detectados en las alertas.

## <a name="comments-defined-by-security-analysts-and-administrators"></a>Comentarios definidos por los analistas y administradores de seguridad 

Las alertas pueden incluir una lista de comentarios predefinidos. Por ejemplo, los comentarios pueden ser instrucciones sobre las acciones de mitigación que se deben realizar, o nombres de los usuarios con los que ponerse en contacto con respecto al evento.

Cuando vaya a administrar un evento de alerta, puede elegir el comentario o los comentarios que mejor reflejen el estado del evento o los pasos que ha tomado para investigar la alerta.

Los comentarios seleccionados se guardan en el mensaje de alerta. Trabajar con comentarios mejora la comunicación entre los usuarios y los equipos durante la investigación de un evento de alerta. Por consiguiente, los comentarios pueden acelerar el tiempo de respuesta a los incidentes.

Los administradores o los analistas de seguridad predefinen los comentarios. Los comentarios seleccionados no se reenvían a los sistemas asociados definidos en las reglas de reenvío.

Después de revisar la información de una alerta, puede llevar a cabo diversos pasos forenses como guía para la administración del evento de alerta. Por ejemplo:

- Analizar la actividad reciente del dispositivo (informe de minería de datos). 

- Analizar otros eventos que se produjeron al mismo tiempo (escala de tiempo del evento). 

- Analizar el tráfico completo de eventos (archivo PCAP).

## <a name="pcap-files"></a>Archivos PCAP

En algunos casos, puede tener acceso a un archivo PCAP desde el mensaje de alerta. Esto puede resultar útil si quiere obtener información más detallada sobre el tráfico de red asociado.

Para descargar un archivo PCAP, seleccione :::image type="content" source="media/how-to-work-with-alerts-sensor/download-pcap.png" alt-text="icono de descarga."::: en la esquina superior derecha del cuadro de diálogo **Detalles de alertas**.

## <a name="recommendations-for-investigating-an-event"></a>Recomendaciones para investigar un evento 

El área **Recomendación** de una alerta muestra información que puede ayudarle a entender mejor un evento. Revise esta información antes de administrar el evento de alerta o de realizar una acción sobre el dispositivo o la red.

## <a name="see-also"></a>Consulte también

[Aceleración de los flujos de trabajo de alertas](how-to-accelerate-alert-incident-response.md)

[Administración de los eventos de alerta](how-to-manage-the-alert-event.md)
