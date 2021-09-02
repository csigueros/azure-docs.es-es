---
title: Solución de problemas del error 409001 DeviceAlreadyExists de Azure IoT Hub
description: Corrección del error 409001 DeviceAlreadyExists
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: f6ef32537da22324ac2d4991fac9df785374e16f
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122829204"
---
# <a name="409001-devicealreadyexists"></a>409001 DeviceAlreadyExists

En este artículo se describen las causas y las soluciones de los errores **409001 DeviceAlreadyExists**.

## <a name="symptoms"></a>Síntomas

Al intentar registrar un dispositivo en IoT Hub, la solicitud genera un error **409001 DeviceAlreadyExists**.

## <a name="cause"></a>Causa

Ya existe un dispositivo con el mismo identificador de dispositivo en el centro de IoT. 

## <a name="solution"></a>Solución

Use un identificador de dispositivo distinto e inténtelo de nuevo.