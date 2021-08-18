---
title: Solución de problemas del error 404001 DeviceNotFound de Azure IoT Hub
description: Corrección del error 404001 DeviceNotFound
author: jlian
ms.service: iot-hub
services: iot-hub
ms.topic: troubleshooting
ms.date: 07/07/2021
ms.author: jlian
ms.openlocfilehash: 127f4546d674eb425440a8e0f395ca8ff0a6b6f4
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515527"
---
# <a name="404001-devicenotfound"></a>404001 DeviceNotFound

En este artículo se describen las causas y las soluciones de los errores **404001 DeviceNotFound**.

## <a name="symptoms"></a>Síntomas

Durante una comunicación de la nube al dispositivo (C2D), como el mensaje C2D, la actualización gemela o el método directo, la operación produce un error **404001 DeviceNotFound**.

## <a name="cause"></a>Causa

No se pudo realizar la operación porque IoT Hub no encuentra el dispositivo. El dispositivo no está registrado o está deshabilitado.

## <a name="solution"></a>Solución

Registre el identificador de dispositivo que usó e inténtelo de nuevo.