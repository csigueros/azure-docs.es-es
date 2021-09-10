---
title: Directiva de mensajería
titleSuffix: An Azure Communication Services concept document
description: Obtenga información sobre las directivas de mensajería SMS.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 2281dac86281d01ec6f033062b87236a780d3bc7
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123259753"
---
# <a name="azure-communication-services-messaging-policy"></a>Directiva de mensajería de Azure Communication Services

Azure Communication Services está transformando la forma en la que nuestros clientes interactúan con sus clientes mediante la creación de experiencias de comunicación personalizadas enriquecidas que aprovechan los mismos servicios de clase empresarial que respaldan a Microsoft Teams y Skype. Integre la funcionalidad de mensajería SMS en sus soluciones de comunicaciones para llegar a sus clientes en cualquier momento y en cualquier lugar en el que necesiten asistencia. Solo tiene que tener en cuenta algunos requisitos de mensajería y normativas del sector para comenzar.

Sabemos que los requisitos de mensajería pueden parecer complicados de aprender, pero es tan sencillo como recordar la abreviatura "COMS":

- C: consentimiento
- O: opción de no participar
- M: contenido del Mensaje
- S: suplantación de identidad

Hemos desarrollado esta directiva de mensajería para ayudarle a cumplir los requisitos normativos y a alinearse con los procedimientos recomendados. 

[!INCLUDE [Notice](../../includes/messaging-policy-include.md)]

## <a name="consent"></a>Consentir 

### <a name="what-is-consent"></a>¿Qué es el consentimiento?

El consentimiento es un contrato entre usted y el destinatario del mensaje que le permite enviarle mensajes automatizados. Debe obtener su consentimiento antes de enviar el primer mensaje y debe dejar claro al destinatario que acepta recibir mensajes suyos. Este procedimiento se conoce como recepción de "consentimiento expreso por anticipado" de la persona a la que pretende enviar mensajes.

Los mensajes que envíe deben ser del mismo tipo de mensaje que el destinatario haya acordado recibir y solo se deben enviar al número que el destinatario le haya proporcionado. Si desea enviar mensajes informativos, como recordatorios de citas o alertas, el consentimiento puede ser oral o escrito. Si desea enviar mensajes promocionales, como mensajes de ventas o de marketing que promocionan un producto o servicio, el consentimiento debe ser por escrito.

### <a name="how-do-you-obtain-consent"></a>¿Cómo se obtiene el consentimiento?

El consentimiento se puede obtener de varias maneras, por ejemplo:

- Cuando un usuario escribe su número de teléfono en un sitio web 
- Cuando un usuario inicia un intercambio de mensajes de texto
- Cuando un usuario envía una palabra clave de registro a su número de teléfono 
 
Independientemente de cómo se obtenga el consentimiento, usted y sus clientes deben asegurarse de que el consentimiento no sea ambiguo. El ámbito del consentimiento debe ser claro para el destinatario.


### <a name="consent-requirements"></a>Requisitos del consentimiento:

- Proporcione una "llamada a la acción" antes de obtener el consentimiento. Usted y sus clientes deben proporcionar a los posibles destinatarios de los mensajes una "llamada a la acción" que les invite a participar en el programa de mensajería. La llamada a la acción debe incluir, como mínimo: (1) la identidad del remitente del mensaje, (2) unas instrucciones de participación claras, (3) instrucciones para dejar de participar y (4) cualquier cuota de mensajería asociada.
- El consentimiento no es transferible ni asignable. Cualquier consentimiento que le proporcione una persona no se puede transferir ni vender a terceros no afiliados. Si recopila el consentimiento de una persona para un tercero, debe identificar claramente el tercero a la persona. También debe indicar que el consentimiento obtenido se aplica únicamente a las comunicaciones del tercero.
- El consentimiento está limitado en su propósito. Una persona que proporciona su número para un propósito determinado da su consentimiento para recibir comunicaciones solo para ese propósito específico y desde ese remitente del mensaje específico. Antes de obtener el consentimiento, debe notificar claramente al destinatario previsto del mensaje si va a enviar mensajes periódicos o mensajes de un afiliado.

### <a name="consent-best-practices"></a>Procedimientos recomendados para el consentimiento:

Además de los requisitos de mensajería descritos anteriormente, es posible que desee implementar varios procedimientos recomendados comunes, entre los que se incluyen: 

- Información detallada sobre la "llamada a la acción". Para asegurarse de que obtiene el consentimiento adecuado, proporcione
  - el nombre o la descripción de su programa de mensajería o producto
  - los números desde los que los destinatarios recibirán mensajes, y 
  - los términos y condiciones aplicables antes de que una persona opte por recibir mensajes suyos.
- Registros precisos de consentimiento. Debe conservar los registros de cualquier consentimiento que le proporcione una persona durante al menos cuatro años. Los registros de consentimiento pueden incluir:
  - marcas de tiempo
  - medio por el que se obtuvo el consentimiento
  - campaña específica para la que se obtuvo el consentimiento
  - capturas de pantalla
  - identificador de sesión o dirección IP de la persona que da su consentimiento.
- Directivas de privacidad y seguridad. Se recomienda a los desarrolladores que proporcionen directivas de privacidad sencillas que los destinatarios del mensaje puedan revisar antes de que se obtenga su consentimiento. También se recomienda mantener controles de seguridad proactivos para proteger la información privada de los usuarios.


## <a name="double-opt-in-consent"></a>Doble consentimiento de participación:

Azure Communication Services recomienda usar un doble consentimiento de participación para todas las campañas de mensajería. El doble consentimiento de participación es un proceso de dos pasos en el que una persona primero da su consentimiento para recibir determinados tipos de mensajes. Después, se le envía un mensaje de participación de seguimiento para confirmar su consentimiento. Solo debe enviar más mensajes una vez que el destinatario del mensaje confirme su consentimiento.

El mensaje de confirmación inicial que envíe debe incluir su identidad, la opción de no participar en futuros mensajes (como el uso de un comando "STOP"), un número gratuito o un comando "HELP" para obtener información adicional, una notificación de que la persona está inscrita en un programa de mensajes periódico, una breve descripción del programa, la frecuencia con la que pretende enviar mensajes periódicos y las tarifas asociadas. 

### <a name="does-azure-communication-services-ever-require-double-opt-in-consent"></a>¿Azure Communication Services requiere el doble consentimiento de participación?
Sí, aunque siempre se recomienda el doble consentimiento de participación, Azure Communication Services requiere que se use el doble consentimiento de participación en algunos tipos de campañas de mensajería debido a su uso frecuente en esquemas de suplantación de identidad o a su tendencia a producir quejas de los consumidores. Estas campañas incluyen:
- Mensajes de garantía automática
- Planes de seguros de salud a corto plazo
- Mensajes de refinanciación de deudas o de reducción de tasa de interés, si no los realiza una institución financiera
- Mensajes de generación de clientes potenciales
- Sorteos, concursos y regalos
- Ofertas de trabajo desde casa

Las campañas para las que se requiere el doble consentimiento de participación están sujetas a cambios a discreción de Azure Communication Services.

### <a name="exceptions-to-traditional-consent-rules"></a>Excepciones a las reglas de consentimiento tradicionales:
Aunque normalmente se requiere el consentimiento expreso antes de enviar un mensaje, existen dos situaciones en las que se implica el consentimiento de una persona al mensaje.

- El destinatario inicia una comunicación. Si una persona inicia una comunicación mediante el envío de un mensaje, puede proporcionar la información correspondiente en respuesta a una consulta o solicitud específica contenida en el mensaje. Sin embargo, el consentimiento implícito que la persona ha proporcionado está limitado a la conversación que inició dicha persona a menos que obtenga consentimiento para otras comunicaciones.
- Exenciones para servicios específicos. Hay varios servicios específicos para los que puede tener el consentimiento implícito para iniciar un mensaje. Los más comunes son: 
- mensajes de entrega de paquetes
- mensajes de una institución financiera relativos a temas en los que el tiempo es importante (como transacciones potencialmente fraudulentas o brechas de datos)
- mensajes de un proveedor de asistencia sanitaria que incluyen información en la que el tiempo es importante y tienen un propósito de tratamiento (como recordatorios de citas o exámenes, resultados de laboratorio y notificaciones de recetas). 
 
Ninguno de estos mensajes puede incluir convocatorias ni anuncios.


## <a name="opt-out"></a>Dejar de participar

Los destinatarios del mensaje pueden revocar el consentimiento y dejar de recibir mensajes futuros mediante cualquier medio razonable. No puede designar un medio exclusivo para que los destinatarios del mensaje revoquen el consentimiento. 

### <a name="opt-out-requirements"></a>Requisitos para la opción de dejar de participar:

Asegúrese de que los destinatarios del mensaje pueden dejar de participar en mensajes futuros en cualquier momento. También debe ofrecer varias opciones para dejar de participar. Después de que el destinatario del mensaje opte por dejar de participar, no debe enviar mensajes adicionales a menos que la persona proporcione su consentimiento renovado.

Uno de los mecanismos para dejar de participar más comunes consiste en incluir la palabra clave "STOP" en el mensaje inicial de cada nueva conversación. Debe estar preparado para quitar a los clientes que respondan con "stop" en minúsculas o con otras palabras clave comunes, como "cancelar suscripción" o "cancelar". Después de que una persona haya revocado el consentimiento, se le debe quitar de todas las campañas de mensajería periódicas, a menos que explícitamente opten por seguir recibiendo mensajes de un programa determinado.

### <a name="opt-out-best-practices"></a>Procedimientos recomendados para dejar de participar:

Además de las palabras clave, otros mecanismos comunes para dejar de participar incluyen proporcionar a los clientes una dirección de correo electrónico de cancelación designada, el número de teléfono del personal de soporte al cliente o un vínculo para cancelar la suscripción en la página web. 


### <a name="how-we-handle-opt-out-requests"></a>Cómo se controlan las solicitudes para dejar de participar:

Si una persona solicita dejar de participar en mensajes futuros de un número gratuito de Azure Communication Services, todo el tráfico adicional procedente de ese número se detendrá automáticamente. Sin embargo, debe asegurarse de que no envía mensajes adicionales para esa campaña de mensajería desde otros números o números nuevos. Si ha obtenido el consentimiento expreso por separado para otra campaña de mensajería, puede continuar enviando mensajes desde otro número para esa campaña. Consulte nuestra sección de preguntas más frecuentes para más información sobre el [control de la cancelación de la participación](https://github.com/Prakulka/azure-docs-pr/blob/master/articles/communication-services/concepts/telephony-sms/sms-faq.md#how-can-i-receive-messages-using-azure-communication-services).

## <a name="message-content"></a>Contenido del mensaje

### <a name="adult-content"></a>Contenido para adultos:

El contenido del mensaje que incluya elementos de sexo, odio, alcohol, armas de fuego, tabaco, apuestas o sorteos y concursos puede desencadenar requisitos adicionales. Este contenido está expresamente prohibido en algunas jurisdicciones. Si envía un mensaje que incluya este contenido, es su deber cumplir todas las leyes aplicables en las jurisdicciones en las que se reciben las comunicaciones. A petición de las autoridades judiciales o de Azure Communication Services, debe estar preparado para proporcionar una prueba de consentimiento con las leyes locales que regulan el contenido para adultos.

Incluso en los casos en los que dicho contenido no sea ilícito, debe incluir un mecanismo de comprobación de edad en el momento de optar por participar para filtrar por edad el destinatario previsto del mensaje con contenido para adultos. En Estados Unidos se aplican requisitos legales adicionales a las comunicaciones de marketing dirigidas a niños menores de 13 años. 

### <a name="prohibited-practices"></a>Prácticas prohibidas:

Tanto usted como sus clientes tienen prohibido usar Azure Communication Services para eludir solicitudes de cancelación razonables. Además, ni usted ni sus clientes pueden eludir las medidas implementadas por Azure Communication Services o un proveedor de servicios de comunicaciones para garantizar el cumplimiento de los requisitos de mensajería y los estándares del sector.

Azure Communication Services prohíbe ciertos contenidos de los mensajes independientemente del consentimiento. El contenido prohibido incluye:
- Contenido que promueve actividades ilícitas (por ejemplo, fraude de impuestos o crueldad hacia los animales en Estados Unidos)
- Discursos de incitación al odio, difamación, acoso o cualquier otro discurso que se haya determinado como claramente ofensivo
- Contenido pornográfico
- Contenido obsceno o vulgar
- Intimidación y amenazas
- Contenido que intente defraudar, engañar, causar daños o obtener de forma ilícita cualquier cosa de valor 
- Contenido que incite daños, discriminación o violencia
- Contenido que distribuya malware
- Contenido que intente eludir los requisitos de filtro por edad

Nos reservamos el derecho a modificar la lista de contenido de mensajes prohibido en cualquier momento.

## <a name="spoofing"></a>Suplantación de identidad

La suplantación de identidad es el acto de hacer que se muestre en el dispositivo del destinatario de un mensaje un número de origen erróneo o inexacto. No se recomienda el uso de mensajes suplantados por su parte ni la de cualquier proveedor de servicios que use. La suplantación oculta la identidad del remitente del mensaje y evita que los destinatarios del mensaje puedan optar fácilmente por no participar en comunicaciones no deseadas. También es necesario cumplir todas las leyes de suplantación de identidad aplicables.

## <a name="final-thoughts"></a>Consideraciones finales

### <a name="legal-responsibility"></a>Responsabilidad legal:

Esta directiva de mensajería no constituye consejo legal y nos reservamos el derecho a modificar la directiva en cualquier momento. Azure Communication Services no es responsable de garantizar que el contenido, los tiempos o los destinatarios de los mensajes de nuestros clientes cumplan todos los requisitos legales aplicables. 

Nuestros clientes son responsables de todos los requisitos de mensajería. Si es una plataforma o un proveedor de software que usa Azure Communication Services con fines de mensajería, debe requerir que sus clientes también cumplan todos los requisitos descritos en esta directiva de mensajería. Si necesita más información, los [principios de mensajería y los procedimientos recomendados](https://api.ctia.org/wp-content/uploads/2019/07/190719-CTIA-Messaging-Principles-and-Best-Practices-FINAL.pdf) de CTIA pueden serle útil para conocer los estándares del sector pertinentes.

### <a name="penalties"></a>Penalizaciones:

Animamos a nuestros clientes a desarrollar e implementar directivas y procedimientos diseñados para garantizar el cumplimiento de todos los requisitos de mensajería. Las infracciones de los requisitos de mensajería pueden implicar multas importantes de tramitación rápida. Es de su propio interés conocer y cumplir todos los requisitos de mensajería aplicables y desarrollar medidas de seguridad de mitigación efectivas que contengan y eliminen las infracciones antes de su propagación. Si infringe nuestra directiva de mensajería u otros requisitos legales, colaboraremos con usted para garantizar el cumplimiento futuro. Sin embargo, nos reservamos el derecho de eliminar de la plataforma de Azure Communication Services a cualquier cliente que muestre un patrón de incumplimiento con nuestra directiva de mensajería o los requisitos legales.
