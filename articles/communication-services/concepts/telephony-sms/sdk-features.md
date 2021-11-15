---
title: Introducción al SDK de SMS de Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Proporciona información general sobre el SDK de SMS y sus ofertas.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: prakulka
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: sms
ms.openlocfilehash: 3be8101834eaa9f023c12b61d37c3b572d7a6ac6
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131434130"
---
# <a name="sms-sdk-overview"></a>Introducción al SDK de SMS

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Los SDK de SMS de Azure Communication Services se pueden usar para agregar mensajería SMS a las aplicaciones.

## <a name="sms-sdk-capabilities"></a>Funcionalidades del SDK de SMS

En la lista siguiente se presenta el conjunto de características que están disponibles actualmente en nuestros SDK.

| Grupo de características | Capacidad                                                                            | JS  | Java | .NET | Python |
| ----------------- | ------------------------------------------------------------------------------------- | --- | ---- | ---- | ------ |
| Funcionalidades principales | Enviar y recibir mensajes SMS                                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Habilitar informes de entrega para los mensajes enviados                                             | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Todos los juegos de caracteres (compatibilidad con lenguaje/Unicode)                                         | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Compatibilidad con mensajes largos (hasta 2048 bytes)                                          | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Concatenación automática de mensajes largos                                                   | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Envío de mensajes a varios destinatarios a la vez                                        | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Compatibilidad con la idempotencia                                                               | ✔️   | ✔️    | ✔️    | ✔️      |
|                   | Etiquetas personalizadas para los mensajes.                                                             | ✔️   | ✔️    | ✔️    | ✔️      |
| Events            | Usar Event Grid para configurar webhooks para recibir mensajes entrantes e informes de entrega | ✔️   | ✔️    | ✔️    | ✔️      |
| Número de teléfono      | Números gratuitos, códigos cortos (*Próximamente*)                                        | ✔️   | ✔️    | ✔️    | ✔️      |
| Llamadas RTC      | Agregar funcionalidades de llamadas RTC al número de teléfono gratuito habilitado para SMS                    | ✔️   | ✔️    | ✔️    | ✔️      |

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción al envío de SMS](../../quickstarts/telephony-sms/send.md)

Puede que los siguientes documentos le resulten interesantes:

- Familiarización con los [conceptos de SMS](../telephony-sms/concepts.md) generales
- Obtención de un [número de teléfono](../../quickstarts/telephony-sms/get-phone-number.md) compatible con SMS
- [Tipos de número de teléfono en Azure Communication Services](../telephony-sms/plan-solution.md)
