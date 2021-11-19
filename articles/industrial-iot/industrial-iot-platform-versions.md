---
title: Versiones de la plataforma de Azure Industrial IoT
description: En este artículo se proporciona información general sobre la versión existente de la plataforma de Industrial IoT y su compatibilidad.
author: monikavar
ms.author: movarshn
ms.service: industrial-iot
ms.topic: overview
ms.date: 11/10/2021
ms.openlocfilehash: 248cf06093ae206f68553cd3a6c81dff8fbb8e75
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494398"
---
# <a name="azure-industrial-iot-platform-release-281"></a>Plataforma de Azure Industrial IoT, versión 2.8.1
Nos complace anunciar el lanzamiento de la versión 2.8.1 de los componentes de la plataforma de Industrial IoT. Esta es la actualización de la primera revisión de la versión 2.8 del Soporte técnico a largo plazo (LTS). Contiene actualizaciones de seguridad importantes, correcciones de errores y optimizaciones del rendimiento.

## <a name="azure-industrial-iot-platform-release-28"></a>Plataforma de Azure Industrial IoT, versión 2.8

Nos complace anunciar la declaración del Soporte técnico a largo plazo (LTS) de la versión 2.8. Aunque seguimos desarrollando y lanzando actualizaciones de nuestros proyectos en curso en GitHub, ahora también ofrecemos una rama que solo recibirá correcciones de errores críticos y actualizaciones de seguridad a partir de julio de 2021. Los clientes pueden confiar en un ciclo de vida de soporte técnico a largo plazo para estas compilaciones de LTS, lo que proporciona estabilidad y garantía para el planeamiento de horizontes temporales más largos que nuestros clientes necesitan. La rama LTS ofrece a los clientes la garantía de que se beneficiarán de las correcciones de errores críticos o de seguridad necesarias con un impacto mínimo en sus implementaciones e interacciones de módulos.  Al mismo tiempo, los clientes pueden acceder a las actualizaciones más recientes de la [rama principal](https://github.com/Azure/Industrial-IoT) para mantener el ritmo de los últimos desarrollos y el tiempo de ciclo más rápido para las actualizaciones del producto. 

## <a name="version-history"></a>Historial de versiones 

|Versión      |Tipo                   |Date         |Aspectos destacados                             |
|-------------|-----------------------|-------------|---------------------------------------|
|2.5.4        |Stable                 |Marzo de 2020   |La interfaz del método directo de IoT Hub controla desde la nube sin ningún microservicio adicional (modo independiente), mientras que la interfaz del servidor OPC UA usa la pila de OPC de OPC Foundation: [Notas de la versión](https://github.com/Azure/Industrial-IoT/releases/tag/2.5.4)|
|[2.7.206](https://github.com/Azure/Industrial-IoT/tree/release/2.7.206)      |Stable                 |Enero de 2021 |La configuración a través de la API de REST (modo orquestado) admite el formato de telemetría Samples, así como PubSub: [Notas de la versión](https://github.com/Azure/Industrial-IoT/releases/tag/2.7.206)|
|[2.8](https://github.com/Azure/Industrial-IoT/tree/2.8.0)        |Soporte técnico a largo plazo (LTS)|Julio de 2021    |Actualización de IoT Edge a 1.1 LTS, registro y seguimiento de la pila de OPC para obtener un mejor diagnóstico de OPC Publisher, correcciones de seguridad: [Notas de la versión](https://github.com/Azure/Industrial-IoT/releases/tag/2.8.0)|
|[2.8.1](https://github.com/Azure/Industrial-IoT/tree/2.8.1)        |Versión de revisión para LTS 2.8|Noviembre de 2021    |Correcciones de errores críticos, actualizaciones de seguridad y optimizaciones de rendimiento para la versión 2.8 de LTS|

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [¿Qué es Azure Industrial IoT?](overview-what-is-industrial-iot.md)
