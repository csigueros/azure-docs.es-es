---
title: Requisitos de red de Device Update para IoT Hub | Microsoft Docs
description: Device Update para IoT Hub usa varios puertos de red para propósitos diferentes.
author: vimeht
ms.author: vimeht
ms.date: 1/11/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: aeeffa55a4f7f90742616e137328948f3ffa7a5c
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862019"
---
# <a name="ports-used-with-device-update-for-iot-hub"></a>Puertos utilizados con Device Update para IoT Hub
ADU usa varios puertos de red para propósitos diferentes.

## <a name="default-ports"></a>Puertos predeterminados

Propósito|Número de puerto |
---|---
Descarga desde redes y redes CDN  | 80 (protocolo HTTP)
Descarga desde MCC y redes CDN | 80 (protocolo HTTP)
Conexión de ADU Agent a Azure IoT Hub  | 8883 (protocolo MQTT)

## <a name="use-azure-iot-hub-supported-protocols"></a>Uso de protocolos admitidos por Azure IoT Hub
ADU Agent se puede modificar para usar cualquiera de los protocolos admitidos por Azure IoT Hub.

[Más información](../iot-hub/iot-hub-devguide-protocols.md) sobre la lista actual de protocolos admitidos.
