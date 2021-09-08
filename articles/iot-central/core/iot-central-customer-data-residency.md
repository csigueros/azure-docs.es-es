---
title: Residencia de datos del cliente en Azure IoT Central | Microsoft Docs
description: En este artículo se describe la residencia de datos del cliente en las aplicaciones de Azure IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 08/31/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 2086ab1e899b85f52391187425c85eeb86b21c0e
ms.sourcegitcommit: e8b229b3ef22068c5e7cd294785532e144b7a45a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2021
ms.locfileid: "123481171"
---
# <a name="azure-iot-central-customer-data-residency"></a>Residencia de datos del cliente de Azure IoT Central

IoT Central no almacena datos de clientes fuera de la geografía especificada por el cliente, excepto en los siguientes escenarios:

- Cuando se agrega un nuevo usuario a una aplicación de IoT Central existente, el identificador de correo electrónico del usuario se puede almacenar fuera de la geografía hasta que el usuario invitado accede a la aplicación por primera vez.

- Los mosaicos de mapa del panel de IoT Central usan [Azure Maps](../../azure-maps/about-azure-maps.md). Cuando se agrega un mosaico de mapa a una aplicación de IoT Central existente, los datos de ubicación se pueden procesar o almacenar de acuerdo con las reglas de geolocalización del servicio Azure Maps.
