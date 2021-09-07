---
title: Arquitectura de referencia de la solución de supervisión de la calidad del agua creada con Azure IoT Central | Microsoft Docs
description: Conozca los conceptos de una solución de supervisión de la calidad del agua creada con Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 83906682a188a0d6abf859589a38311a0d726a05
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229819"
---
# <a name="water-quality-monitoring-reference-architecture"></a>Arquitectura de referencia de supervisión de calidad del agua 

Las soluciones de supervisión de la calidad del agua se pueden crear con la plantilla de aplicación de **Azure IoT Central** como una aplicación de IoT de inicio rápido. En este artículo se proporciona una guía de arquitectura de referencia de alto nivel sobre la creación de una solución de un extremo a otro. 

![Arquitectura de supervisión de la calidad del agua](./media/concepts-waterqualitymonitoring-architecture/concepts-waterqualitymonitoring-architecture1.png)

Conceptos:

1. Dispositivos y conectividad  
1. IoT Central
1. Extensibilidad e integraciones
1. Aplicaciones empresariales

En este artículo se describen los componentes clave que suelen desempeñar un papel en una solución de supervisión de la calidad del agua.

## <a name="devices-and-connectivity"></a>Dispositivos y conectividad

Las soluciones de administración de agua usan dispositivos de agua inteligentes, como medidores de flujo, monitores de calidad del agua, válvulas inteligentes y detectores de fugas.

Los dispositivos que se usan en las soluciones inteligentes para el agua normalmente se conectan mediante redes de área extensa de bajo consumo (LPWAN) mediante un operador de red de terceros. Para estos tipos de dispositivos, use el [Puente de dispositivos de Azure IoT Central](../core/howto-build-iotc-device-bridge.md) para enviar los datos del dispositivo a la aplicación de IoT en Azure IoT Central. También puede tener puertas de enlace de dispositivo que sean compatibles con IP y que puedan conectarse directamente a IoT Central.

## <a name="iot-central"></a>IoT Central

Azure IoT Central es una plataforma de aplicaciones de IoT que le ayuda a compilar e implementar rápidamente una solución de IoT. Puede utilizar su marca, personalizar e integrar la solución con servicios de terceros.

Al conectar los dispositivos de agua inteligentes a IoT Central, la aplicación proporciona el control, la supervisión y las alertas de los dispositivos, la interfaz de usuario con RBAC integrado, paneles de información configurables y opciones de extensibilidad.

## <a name="extensibility-and-integrations"></a>Extensibilidad e integraciones

Puede ampliar su aplicación de IoT en IoT Central y, si lo desea:

* Transformar e integrar los datos de IoT para el análisis avanzado, como el entrenamiento de modelos de aprendizaje automático, mediante la exportación continua de datos de la aplicación de IoT Central.
* Automatizar flujos de trabajo en otros sistemas al desencadenar acciones mediante Power Automate o webhooks desde la aplicación de IoT Central.
* Acceder mediante programación a la aplicación IoT de IoT Central mediante las API de IoT Central.

## <a name="business-applications"></a>Aplicaciones empresariales

Puede usar datos de IoT para crear varias aplicaciones empresariales dentro de una utilidad de agua. En la [aplicación de supervisión de consumo de agua de IoT Central](tutorial-water-consumption-monitoring.md) puede configurar reglas y acciones, y establecer que creen alertas en [Connected Field Service](/dynamics365/field-service/connected-field-service). Configure Power Automate en reglas de IoT Central para automatizar los flujos de trabajo entre aplicaciones y servicios. Además, en función de las actividades de los servicios de Connected Field Service, la información se puede devolver a Azure IoT Central.

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [crear una aplicación de IoT Central de supervisión de la calidad del agua](./tutorial-water-quality-monitoring.md).
* Más información acerca de las [plantillas de IoT Central Government](./overview-iot-central-government.md)
* Para más información acerca de IoT Central, consulte [Introducción a IoT Central](../core/overview-iot-central.md).
