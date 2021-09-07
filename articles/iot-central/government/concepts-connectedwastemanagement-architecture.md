---
title: Arquitectura de referencia de la solución de administración de residuos conectada creada con Azure IoT Central | Microsoft Docs
description: Conozca los conceptos de una solución de administración de residuos conectada creada con Azure IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 07/15/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 60fe5b51ffa04de79598f3f4744a8bf2bb57966a
ms.sourcegitcommit: 92dd25772f209d7d3f34582ccb8985e1a099fe62
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114229906"
---
# <a name="connected-waste-monitoring-reference-architecture"></a>Arquitectura de referencia de supervisión de residuos conectada 

Se puede crear una solución de administración de residuos conectada con la **plantilla de aplicación de Azure IoT Central** como una aplicación de IoT de inicio. En este artículo se proporciona una guía de arquitectura de referencia de alto nivel sobre la creación de una solución completa.

![Arquitectura de la administración de residuos conectada](./media/concepts-connectedwastemanagement-architecture/concepts-connectedwastemanagement-architecture1.png)

Conceptos:

1. Dispositivos y conectividad  
1. IoT Central
1. Extensibilidad e integraciones
1. Aplicaciones empresariales

En este artículo se describen los componentes clave que suelen desempeñar un papel en una solución de administración de residuos.

## <a name="devices-and-connectivity"></a>Dispositivos y conectividad

Los dispositivos, como los contenedores de residuos, que se usan en entornos abiertos, normalmente se conectan mediante redes de área extensa de bajo consumo (LPWAN) o a través de un operador de red de terceros. Para estos tipos de dispositivos, use el [Puente de dispositivos de Azure IoT Central](../core/howto-build-iotc-device-bridge.md) para enviar los datos del dispositivo a la aplicación de IoT en Azure IoT Central. También puede tener puertas de enlace de dispositivo que sean compatibles con IP y que puedan conectarse directamente a IoT Central.

## <a name="iot-central"></a>IoT Central

Azure IoT Central es una plataforma de aplicaciones de IoT que le ayuda a compilar e implementar rápidamente una solución de IoT. Puede utilizar su marca, personalizar e integrar la solución con servicios de terceros.

Al conectar los dispositivos de residuos a IoT Central, la aplicación proporciona el control, la supervisión y las alertas de los dispositivos, la interfaz de usuario con RBAC integrado, paneles de información configurables y opciones de extensibilidad.

## <a name="extensibility-and-integrations"></a>Extensibilidad e integraciones

Puede ampliar su aplicación de IoT en IoT Central y, si lo desea:

* Transformar e integrar los datos de IoT para el análisis avanzado, como el entrenamiento de modelos de aprendizaje automático, mediante la exportación continua de datos de la aplicación de IoT Central.
* Automatizar flujos de trabajo en otros sistemas al desencadenar acciones mediante Power Automate o webhooks desde la aplicación de IoT Central.
* Acceder mediante programación a la aplicación IoT de IoT Central mediante las API de IoT Central.

## <a name="business-applications"></a>Aplicaciones empresariales

Puede usar datos de IoT para crear varias aplicaciones empresariales dentro de una utilidad de residuos. Por ejemplo, en una solución de administración de residuos conectada, puede optimizar el envío de camiones de recogida de basura. La optimización se puede realizar en función de los datos de los sensores de IoT de los contenedores de residuos conectados. En la [aplicación de administración de residuos conectada a IoT Central](./tutorial-connected-waste-management.md), puede configurar reglas y acciones y establecerlas para crear alertas en el [servicio de campo conectado](/dynamics365/field-service/connected-field-service). Configure Power Automate en reglas de IoT Central para automatizar los flujos de trabajo entre aplicaciones y servicios. Además, en función de las actividades de los servicios de Connected Field Service, la información se puede devolver a Azure IoT Central.

Puede configurar fácilmente los siguientes procesos de integración con IoT Central y el servicio de campo conectado:

* Azure IoT Central puede enviar información sobre las anomalías del dispositivo al servicio de campo conectado para el diagnóstico.
* El servicio de campo conectado puede crear casos o pedidos desencadenados a partir de las anomalías del dispositivo.
* El servicio de campo conectado puede programar técnicos para que realicen las comprobaciones pertinentes, con el fin de evitar incidentes que provoquen que haya un tiempo de inactividad.
* El panel de dispositivos de Azure IoT Central se puede actualizar con información relevante acerca de la programación y los servicios.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [crear una aplicación de administración de residuos conectada](./tutorial-connected-waste-management.md) de IoT Central.
* Más información acerca de las [plantillas de IoT Central Government](./overview-iot-central-government.md)
* Para más información acerca de IoT Central, consulte [Introducción a IoT Central](../core/overview-iot-central.md).
