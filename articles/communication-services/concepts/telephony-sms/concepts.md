---
title: Conceptos de SMS en Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Obtenga información sobre los conceptos de SMS de Communication Services.
author: probableprime
manager: chpalm
services: azure-communication-services
ms.author: rifox
ms.date: 06/30/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.openlocfilehash: 036a3de91d39337353eebf7c3d46b1c659fe7cd5
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255317"
---
# <a name="sms-concepts"></a>Conceptos de SMS

[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Azure Communication Services le permite enviar y recibir mensajes de texto SMS mediante los SDK de SMS de Communication Services. Estos SDK se pueden usar para posibilitar escenarios de servicio al cliente, recordatorios de citas, autenticación en dos fases y otros requisitos de comunicación en tiempo real. Los SMS de Communication Services permiten enviar mensajes de manera confiable a la vez que exponen las métricas de entrega y respuesta.

Entre las principales características de los SDK de SMS de Azure Communication Services se incluyen:

-  Experiencia de instalación **sencilla** para agregar la capacidad de SMS a las aplicaciones.
- Compatibilidad con mensajes de **alta velocidad** mediante números gratuitos para los casos de uso de A2P (aplicación a persona) en Estados Unidos.
- Se admite la **mensajería masiva** para permitir el envío de mensajes a varios destinatarios a la vez.
- Conversaciones **bidireccionales** para admitir escenarios como la asistencia al cliente, las alertas y los recordatorios de citas.
- **Entrega confiable** con informes de entrega en tiempo real para los mensajes que se envían desde la aplicación.
- **Análisis** para realizar un seguimiento de los patrones de uso de SMS.
- Compatibilidad con la administración de **cancelaciones** para detectar automáticamente y respetar las cancelaciones de números gratuitos. Las cancelaciones de participación para números gratuitos de Estados Unidos son obligatorias y las aplican los operadores de Estados Unidos.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Introducción al envío de SMS](../../quickstarts/telephony-sms/send.md)

Puede que los siguientes documentos le resulten interesantes:

- Familiarícese con el [SDK de SMS](../telephony-sms/sdk-features.md).
- Obtención de un [número de teléfono](../../quickstarts/telephony-sms/get-phone-number.md) compatible con SMS
- [Tipos de número de teléfono en Azure Communication Services](../telephony-sms/plan-solution.md)
