---
title: Configuración de mensajes AS2
description: Guía de referencia para la configuración de envío y recepción de AS2 en Azure Logic Apps con Enterprise Integration Pack
services: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, logicappspm
ms.topic: article
ms.date: 07/20/2021
ms.openlocfilehash: af13306598db75e89064507b9a2fad80d8d33542
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114453178"
---
# <a name="reference-for-as2-message-settings-in-azure-logic-apps-with-enterprise-integration-pack"></a>Guía de referencia para la configuración de envío y recepción de AS2 en Azure Logic Apps con Enterprise Integration Pack

Esta referencia describe las propiedades que puede establecer para especificar cómo un contrato de AS2 controla los mensajes enviados y recibidos entre asociados comerciales. Configure estas propiedades en función del contrato con el asociado con el que intercambia mensajes.

<a name="AS2-incoming-messages"></a>

## <a name="as2-receive-settings"></a>Configuración de recepción de AS2

![Seleccione "Configuración de recepción"](./media/logic-apps-enterprise-integration-as2-message-settings/receive-settings.png)

| Propiedad | Obligatorio | Descripción |
|----------|----------|-------------|
| **Override message properties** | No | Reemplaza las propiedades en los mensajes entrantes por los valores de sus propiedades. |
| **Debe firmarse el mensaje** | No | Especifica si todos los mensajes entrantes deben estar firmados digitalmente. Si requiere una firma, en la lista **Certificado**, seleccione un certificado público del asociado invitado existente para validar la firma de los mensajes. Si no tiene un certificado, obtenga más información sobre la [adición de certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Message should be encrypted** | No | Especifica si se deben cifrar todos los mensajes entrantes. Los mensajes sin cifrado se rechazan. Si requiere cifrado, desde la lista **Certificado**, seleccione un certificado privado del asociado del host existente para descifrar los mensajes entrantes. Si no tiene un certificado, obtenga más información sobre la [adición de certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Message should be compressed** | No | Especifica si se deben comprimir todos los mensajes entrantes. Los mensajes sin comprimir se rechazan. |
| **No permitir los id. de mensajes duplicados** | No | Especifica si se deben permitir que los mensajes con identificadores duplicados. Si prohíbe los identificadores duplicados, seleccione el número de días entre comprobaciones. También puede elegir si se deben suspender los duplicados. |
| **MDN Text (Texto de MDN)** | No | Especifica la notificación de disposición del mensaje (MDN) predeterminada que quiere que se envíe al remitente del mensaje. |
| **Send MDN (Enviar MDN)** | No | Especifica si se deben enviar MDN sincrónicas para los mensajes recibidos.  |
| **Send signed MDN (Enviar MDN firmada)** | No | Especifica si se deben enviar MDN firmadas para los mensajes recibidos. Si requiere inicio de sesión, desde la lista **Algoritmo MIC**, seleccione el algoritmo que se usará para firmar los mensajes. |
| **Send asynchronous MDN (Enviar MDN asincrónica)** | No | Especifica si se deben enviar MDN de forma asincrónica. Si selecciona enviar MDN asincrónicas, en el cuadro **URL**, especifique la dirección URL a la que se enviarán las MDN. |
||||

<a name="AS2-outgoing-messages"></a>

## <a name="as2-send-settings"></a>Configuración de envío de AS2

![Seleccione "Configuración de envío"](./media/logic-apps-enterprise-integration-as2-message-settings/send-settings.png)

| Propiedad | Obligatorio | Descripción |
|----------|----------|-------------|
| **Habilitar la firma de mensajes** | No | Especifica si todos los mensajes salientes deben estar firmados digitalmente. Si requiere inicio de sesión, seleccione estos valores: <p>- Desde la lista **Algoritmo de firma**, seleccione el algoritmo que se usará para firmar los mensajes. <br>- Desde la lista **Certificado**, seleccione un certificado privado del asociado del host existente para firmar los mensajes. Si no tiene un certificado, obtenga más información sobre la [adición de certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Habilitar el cifrado de mensajes** | No | Especifica si se deben cifrar todos los mensajes salientes. Si requiere cifrado, seleccione estos valores: <p>- Desde la lista **Algoritmo de cifrado**, seleccione el algoritmo del certificado público del asociado invitado que se usará para cifrar los mensajes. <br>- Desde la lista **Certificado**, seleccione un certificado público del asociado del host existente para cifrar los mensajes salientes. Si no tiene un certificado, obtenga más información sobre la [adición de certificados](../logic-apps/logic-apps-enterprise-integration-certificates.md). |
| **Habilitar la compresión de mensajes** | No | Especifica si se deben comprimir todos los mensajes salientes. |
| **Unfold HTTP headers (Expandir encabezados HTTP)** | No | Coloca el encabezado HTTP `content-type` en una sola línea. |
| **Transmitir el nombre de archivo en el encabezado MIME** | No | Especifica si se debe incluir el nombre de archivo en el encabezado MIME. |
| **Solicitar MDN** | No | Especifica si se deben recibir notificaciones de disposición de mensaje (MDN) para todos los mensajes salientes. |
| **Request signed MDN (Solicitar MDN firmada)** | No | Especifica si se deben recibir MDN firmadas para todos los mensajes salientes. Si requiere inicio de sesión, desde la lista **Algoritmo MIC**, seleccione el algoritmo que se usará para firmar los mensajes. |
| **Request asynchronous MDN (Solicitar MDN asíncrona)** | No | Especifica si se deben recibir MDN de forma asincrónica. Si selecciona enviar MDN asincrónicas, en el cuadro **URL**, especifique la dirección URL a la que se enviarán las MDN. |
| **Enable NRR (Habilitar NRR)** | No | Especifica si se debe requerir la recepción sin rechazo (NRR). Este atributo de comunicación prueba que se recibieron los datos según la dirección indicada. |
| **Formato del algoritmo SHA2** | No | Especifica el formato de algoritmo MIC que se usará para iniciar sesión en los encabezados de las MDN o los mensajes AS2 salientes. |
||||

## <a name="next-steps"></a>Pasos siguientes

[Intercambio de mensajes AS2](../logic-apps/logic-apps-enterprise-integration-as2.md)
