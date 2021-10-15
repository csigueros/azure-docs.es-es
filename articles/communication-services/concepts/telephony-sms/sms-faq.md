---
title: Preguntas más frecuentes sobre SMS
titleSuffix: An Azure Communication Services concept document
description: Preguntas más frecuentes sobre SMS
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: 658e13aa888e448de723a97bf9cc89c162a774b5
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129356310"
---
# <a name="sms-faq"></a>Preguntas más frecuentes sobre SMS

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
## <a name="can-a-customer-use-azure-communication-services-for-emergency-purposes"></a>¿Puede un cliente usar Azure Communication Services para una emergencia?

Azure Communication Services no admite la funcionalidad de texto a 911 en los Estados Unidos, pero es posible que tenga la obligación de hacerlo en virtud de las reglas de la Comisión federal de comunicaciones (FCC) estadounidense.  Debe evaluar si las reglas de texto a 911 de la FCC se aplican a su servicio o aplicación. En la medida en que se le apliquen estas reglas, será responsable de enrutar los mensajes de texto 911 a los centros de llamadas de emergencia que los soliciten. Es libre de determinar su propio modelo de entrega de texto a 911, pero un enfoque aceptado por la FCC conlleva el inicio automático del marcador nativo en el dispositivo móvil del usuario para entregar textos 911 por medio del operador de telefonía móvil subyacente.

## <a name="are-there-any-limits-on-sending-messages"></a>¿Se aplica algún límite al envío de mensajes?

Para garantizar que se siga ofreciendo la alta calidad de servicio coherente con los Acuerdos de Nivel de Servicio, Azure Communication Services aplica límites de frecuencia (diferentes para cada primitivo). Los desarrolladores que llaman a las API más allá del límite reciben una respuesta de código de estado HTTP 429. Si la empresa tiene requisitos que superan el límite de frecuencia, envíe un correo electrónico a phone@microsoft.com.

Límites de frecuencia para SMS:

|Operación|Ámbito|Períodos de tiempo| Límite (número de solicitudes) | Unidades de mensaje por minuto|
|---------|-----|-------------|-------------------|-------------------------|
|Enviar mensaje|Por número|60|200|200|

## <a name="how-does-azure-communication-services-handle-opt-outs-for-toll-free-numbers"></a>¿Cómo controla Azure Communication Services la no participación en números de teléfono gratuitos?

Las cancelaciones de participación para números gratuitos de Estados Unidos son obligatorias y las aplican los operadores de Estados Unidos.
- STOP: Si un destinatario del mensaje de texto desea dejar de participar, puede enviar "STOP" al número de teléfono gratuito. El operador envía la siguiente respuesta predeterminada para STOP: "MENSAJE DE RED: Ha respondido con la palabra "stop", que bloquea todos los mensajes de texto enviados desde este número. Vuelva a responder "unstop" para recibir mensajes de nuevo".
- START o UNSTOP: si el destinatario desea volver a suscribirse a los mensajes de texto desde un número gratuito, puede enviar "START" o "UNSTOP" al número de teléfono gratuito. El operador envía la siguiente respuesta predeterminada para los mensajes START y UNSTOP: "MENSAJE DE RED: Ha respondido "unstop" y comenzará a recibir mensajes de nuevo desde este número".
- Azure Communication Services detectará el mensaje de detención y bloqueará todos los demás mensajes al destinatario. El informe de entrega indicará que se ha producido un error en la entrega con el mensaje de estado "Remitente bloqueado para el destinatario especificado".
- Los mensajes STOP, UNSTOP y START se le retransmitirán de vuelta. Azure Communication Services recomienda supervisar e implementar estas cancelaciones de participación para garantizar que no se envíen más mensajes a los destinatarios que hayan optado por no recibir las comunicaciones.

## <a name="how-can-i-receive-messages-using-azure-communication-services"></a>¿Cómo puedo recibir mensajes mediante Azure Communication Services?

Los clientes de Azure Communication Services pueden usar Azure Event Grid para recibir mensajes entrantes. Siga esta [guía de inicio rápido](../../quickstarts/telephony-sms/handle-sms-events.md) para configurar la cuadrícula de eventos a fin de recibir mensajes.

## <a name="what-is-the-sms-character-limit"></a>¿Cuál es el límite de caracteres en los SMS?
El límite de caracteres para un único SMS es de 160 caracteres. Al enviar mensajes, todos los mensajes de más de 160 caracteres se dividen en segmentos y se envían individualmente, segmentos que luego concatena el dispositivo del destinatario. Del mismo modo, para recibir mensajes largos, varios segmentos de un mensaje largo se concatenan y envían automáticamente como un solo mensaje en el punto de conexión especificado en Azure Event Grid. 

## <a name="can-i-sendreceive-long-messages-2048-chars"></a>¿Puedo recibir mensajes largos (más de 2048 caracteres)?

Azure Communication Services admite el envío y la recepción de mensajes largos a través de SMS. Sin embargo, algunos operadores o dispositivos inalámbricos pueden actuar de forma diferente al recibir mensajes largos.

## <a name="how-are-messages-sent-to-landline-numbers-treated"></a>¿Cómo se tratan los mensajes enviados a números de teléfono fijos?

En los Estados Unidos, Azure Communication Services no comprueba los números de teléfono para ver si pertenecen a un dispositivo fijo e intenta enviar los mensajes a los operadores para su entrega. A los clientes se les cobra por los mensajes enviados a números de teléfono fijos. 

## <a name="can-i-send-messages-to-multiple-recipients"></a>¿Puedo enviar mensajes a varios destinatarios?

Sí, puede hacer una solicitud con varios destinatarios. Siga esta [guía de inicio rápido](../../quickstarts/telephony-sms/send.md?pivots=programming-language-csharp) para enviar mensajes a varios destinatarios.

##  <a name="i-received-a-http-status-202-from-the-send-sms-api-but-the-sms-didnt-reach-my-phone-what-do-i-do-now"></a>Recibí un estado HTTP 202 desde la API Envío de SMS, pero mi teléfono no recibió el mensaje de texto. ¿Qué hago ahora?

El estado 202 devuelto por el servicio significa que el mensaje se ha puesto en cola para su envío, no para su entrega. Use este [inicio rápido](../../quickstarts/telephony-sms/handle-sms-events.md) para suscribirse a eventos de informe de entrega y solución de problemas. Una vez configurados los eventos, revise el campo "deliveryStatus" del informe de entrega a fin de comprobar si la entrega se realizó correctamente o con errores.
