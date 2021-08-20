---
title: Versiones de la plataforma de Azure Industrial IoT
description: En este artículo se proporciona información general sobre la versión existente de la plataforma de Industrial IoT y su compatibilidad.
author: jehona-m
ms.author: jemorina
ms.service: industrial-iot
ms.topic: overview
ms.date: 03/08/2021
ms.openlocfilehash: 26d71a438638bbd681687a26c9ebdc1db8273ca7
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114467230"
---
# <a name="azure-industrial-iot-platform-v280-lts"></a>Soporte técnico a largo plazo de la versión 2.8.0 de la plataforma de Azure Industrial IoT

Nos complace anunciar la declaración de soporte técnico a largo plazo (LTS) de la versión 2.8.0. Aunque seguimos desarrollando y lanzando actualizaciones de nuestros proyectos en curso en GitHub, ahora también ofrecemos una rama que solo recibirá correcciones de errores críticos y actualizaciones de seguridad a partir de julio de 2021. Los clientes pueden confiar en un ciclo de vida de soporte técnico a largo plazo para estas compilaciones de LTS, lo que proporciona estabilidad y garantía para el planeamiento de horizontes temporales más largos que nuestros clientes necesitan. La rama LTS ofrece a los clientes la garantía de que se beneficiarán de las correcciones de errores críticos o de seguridad necesarias con un impacto mínimo en sus implementaciones e interacciones de módulos.  Al mismo tiempo, los clientes pueden acceder a las actualizaciones más recientes de la [rama principal](https://github.com/Azure/Industrial-IoT) para mantener el ritmo de los últimos desarrollos y el tiempo de ciclo más rápido para las actualizaciones del producto. 

## <a name="version-history"></a>Historial de versiones 

|Versión      |Tipo                   |Date         |Aspectos destacados                             |
|-------------|-----------------------|-------------|---------------------------------------|
|2.5.4        |Stable                 |Marzo de 2020   |La interfaz del método directo de IoT Hub controla desde la nube sin ningún microservicio adicional (modo independiente), mientras que la interfaz del servidor OPC UA usa la pila de OPC de OPC Foundation: [Notas de la versión](https://github.com/Azure/Industrial-IoT/releases/tag/2.5.4)|
|[2.7.206](https://github.com/Azure/Industrial-IoT/tree/release/2.7.206)      |Stable                 |Enero de 2021 |La configuración a través de la API de REST (modo orquestado) admite el formato de telemetría Samples, así como PubSub: [Notas de la versión](https://github.com/Azure/Industrial-IoT/releases/tag/2.7.206)|
|[2.8.0](https://github.com/Azure/Industrial-IoT/tree/release/2.8)        |Soporte técnico a largo plazo (LTS)|Julio de 2021    |Actualización de IoT Edge a 1.1 LTS, registro y seguimiento de la pila de OPC para obtener un mejor diagnóstico de OPC Publisher, correcciones de seguridad|

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [¿Qué es Azure Industrial IoT?](overview-what-is-industrial-iot.md)