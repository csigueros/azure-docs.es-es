---
title: Confirmaciones MDN AS2
description: Obtenga información sobre las confirmaciones de notificación de disposición de mensajes (MDN) para los mensajes AS2 en Azure Logic Apps.
services: logic-apps
ms.suite: integration
author: S-Jindal
ms.author: shivamjindal
ms.reviewer: estfan, azla
ms.topic: reference
ms.date: 08/12/2021
ms.openlocfilehash: 510be5ca1343f115bcdf13c2d67b598b4d410564
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862844"
---
# <a name="mdn-acknowledgments-for-as2-messages-in-azure-logic-apps"></a>Confirmaciones MDN para los mensajes AS2 en Azure Logic Apps

En Azure Logic Apps, puede crear flujos de trabajo que controlen los mensajes AS2 para la comunicación del intercambio electrónico de datos (EDI) al usar las operaciones **AS2**. En la mensajería EDI, las confirmaciones proporcionan el estado del procesamiento de un intercambio EDI. Al recibir un intercambio, la [acción **Descodificación AS2**](logic-apps-enterprise-integration-as2.md#decode) puede devolver una notificación de disposición del mensaje (MDN) o una confirmación para el emisor. Una MDN comprueba si ha ocurrido lo siguiente:

* El asociado receptor ha recibido correctamente el mensaje original.

  El asociado emisor compara el `MessageID` para el mensaje que se envió originalmente con el campo `original-message-id` que el receptor incluye en la MDN.

* El asociado receptor ha comprobado la integridad de los datos intercambiados.

  Se calcula una comprobación de integridad del mensaje (MIC) o un resumen de MIC a partir de la carga del mensaje enviado originalmente. El asociado emisor compara esta MIC con la MIC que el receptor ha calculado a partir de la carga en el mensaje recibido y ha incluido en el campo `Received-content-MIC` de la MDN si está firmada.

  > [!NOTE]
  > Una MDN se puede firmar, pero no cifrar ni comprimir.

* Sin rechazo de recepción

  El asociado emisor compara la MDN con la clave pública del socio receptor y comprueba que el valor MIC devuelto en la MDN sea el mismo que la MIC de la carga del mensaje original almacenada en la base de datos sin rechazo.

> [!NOTE]
> Si habilita el envío de una MDN como respuesta, la aplicación lógica intenta devolver una MDN para notificar el estado del procesamiento de los mensajes AS2, incluso aunque se produzca un error durante el procesamiento. La transmisión de AS2 no se completa hasta que el emisor recibe y comprueba la MDN.
> Una MDN sincrónica sirve como respuesta HTTP, por ejemplo, un estado `200 OK`.

Este tema incluye una breve introducción sobre la confirmación de la MDN de AS2, incluidas las propiedades que se usan para generar la confirmación, los encabezados de MDN que se usarán y la MIC. Para obtener información relacionada, revise la siguiente documentación:

* [Intercambio de mensajes AS2 para la integración empresarial B2B en Azure Logic Apps](logic-apps-enterprise-integration-as2.md)
* [Configuración de mensajes AS2](logic-apps-enterprise-integration-as2-message-settings.md)
* [¿Qué es Azure Logic Apps?](logic-apps-overview.md)

## <a name="mdn-generation"></a>Generación de una MDN

La acción Descodificación AS2 genera una MDN a partir de las propiedades de un contrato AS2 con una entidad si la **Configuración de recepción** tiene la opción **Enviar MDN** seleccionada. En esta instancia, la propiedad **AS2-From** del encabezado del mensaje se usa para generar la MDN, pero las otras propiedades y sus valores se toman de la configuración del contrato AS2 del asociado.

De forma predeterminada, los encabezados de mensaje AS2 entrantes se usan para la validación y la generación de la MDN. Para utilizar la validación del contrato y la configuración de la MDN en su lugar, en la **Configuración de recepción** del contrato, seleccione **Reemplazar propiedades del mensaje**. De lo contrario, si esta opción no está seleccionada o un contrato no está disponible, la acción Descodificación AS2 usará los encabezados de mensaje entrantes en su lugar.

## <a name="mdn-headers"></a>Encabezados de MDN

Para correlacionar una MDN con el mensaje AS2 como respuesta, se utilizan el encabezado `AS2-From`, el `AS2-To` y la propiedad de contexto `MessageID`. En la MDN, el encabezado `Original-Message-ID` proviene del encabezado `Message-ID` del mensaje AS2 para el cual la MDN es la respuesta. Una MDN contiene los encabezados siguientes:

| encabezados | Descripción |
|---------|-------------|
| HTTP y AS2 | Para obtener más información, revise la [configuración de mensajes AS2](logic-apps-enterprise-integration-as2-message-settings.md).
| Capa de transferencia | Este encabezado incluye el encabezado `Content-Type` que contiene el mensaje con varias partes firmado, el algoritmo para la MIC, el protocolo de formato de firma y encabezados exteriores secundarios de límites de varias partes. |
| Primera parte | La primera parte del mensaje firmado de varias partes es la MDN insertada. Esta parte es legible para el ser humano. |
| Segunda parte | La segunda parte del mensaje firmado de varias partes contiene la firma digital, una referencia al mensaje original, el estado y tipo de disposición y el valor MIC. Esta parte es legible para la máquina. |
|||

## <a name="mic-digest"></a>Resumen de MIC

El resumen de MIC o MIC comprueba que una MDN se correlacione con la carga del mensaje enviado originalmente. Esta MIC se incluye en la segunda parte del mensaje MDN de varias partes firmado en el campo de extensión `Received-Content-MIC`.

El MIC está codificado en base64 y se determina a partir de la propiedad **Algoritmo MIC**, que se habilita cuando están seleccionadas las propiedades **Enviar MDN** y **Enviar MDN firmado** en la página **Configuración de recepción** del contrato AS2. Para la generación de la MIC, puede elegir entre los siguientes algoritmos hash admitidos:

* SHA1
* MD5
* SHA2-256
* SHA2-384
* SHA2-512

Por ejemplo, en la siguiente captura de pantalla se muestran las propiedades de la MDN en la página **Configuración de recepción** del contrato AS2.

![Captura de pantalla en la que se muestra Azure Portal con la opción "Configuración de recepción" del contrato AS2 con la configuración de la confirmación de MDN.](./media/logic-apps-enterprise-integration-as2-mdn-acknowledgment/mdn-ack-settings.png)

## <a name="next-steps"></a>Pasos siguientes

* [Configuración de mensajes AS2](logic-apps-enterprise-integration-as2-message-settings.md)
