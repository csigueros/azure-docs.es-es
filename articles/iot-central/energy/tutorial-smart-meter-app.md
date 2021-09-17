---
title: 'Tutorial: Supervisión de medidores inteligentes de Azure IoT | Microsoft Docs'
description: En este tutorial, se muestra cómo implementar y usar la plantilla de aplicación de supervisión de medidores inteligentes para IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a332ab10ce4e7c38442288165c56d1161081cd9c
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179480"
---
# <a name="tutorial-deploy-and-walk-through-the-smart-meter-monitoring-app-template"></a>Tutorial: Implementación y recorrido por la plantilla de aplicación de supervisión de medidores inteligentes 

Use la plantilla de aplicación de *supervisión de medidores inteligentes* de IoT Central y las instrucciones de este artículo para desarrollar una solución de supervisión de medidores inteligentes completa.

  :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-app-architecture.png" alt-text="Arquitectura de medidores inteligentes.":::

Esta arquitectura consta de los siguientes componentes. Puede que algunas soluciones no requieran todos los componentes que se mencionan aquí.

### <a name="smart-meters-and-connectivity"></a>Medidores inteligentes y conectividad

Un medidor inteligente es uno de los dispositivos más importantes entre todos los recursos energéticos. Registra datos de consumo energético y los comunica a servicios públicos con fines de supervisión y otros casos de uso, como la respuesta a la demanda y la facturación. Según el tipo de medidor, puede conectarse a IoT Central mediante puertas de enlace u otros sistemas o dispositivos intermedios, tales como dispositivos perimetrales y sistemas de cabeceras. Cree un dispositivo de puente de IoT Central para conectar los dispositivos que no pueden hacerlo directamente. El puente de dispositivo de IoT Central es una solución de código abierto y puede encontrar los detalles completos [aquí](../core/howto-build-iotc-device-bridge.md). 

### <a name="iot-central-platform"></a>Plataforma de IoT Central

Azure IoT Central es una plataforma que simplifica la compilación de su solución de IoT y permite reducir la carga y los costos de la administración, las operaciones y el desarrollo de IoT. Con IoT Central, puede conectar, supervisar y administrar fácilmente sus recursos de Internet de las cosas (IoT) a escala. Después de conectar los medidores inteligentes a IoT Central, la plantilla de aplicación usa características integradas, como modelos de dispositivos, comandos y paneles. La plantilla de la aplicación también usa el almacenamiento de IoT Central para escenarios de ruta de acceso activa, como la supervisión, el análisis, las reglas y la visualización de datos de medición casi en tiempo real. 

### <a name="extensibility-options-to-build-with-iot-central"></a>Opciones de extensibilidad para la compilación con IoT Central

La plataforma de IoT Central proporciona dos opciones de extensibilidad: Exportación de datos continua (CDE) y API. Los clientes y asociados pueden elegir entre estas opciones en función de la personalización de sus soluciones para necesidades específicas. Por ejemplo, uno de nuestros asociados configuró CDE con Azure Data Lake Storage (ADLS). Usa ADLS para la retención de datos a largo plazo y otros escenarios de almacenamiento de la ruta de acceso pasiva, como el procesamiento por lotes, la auditoría e informes.

En este tutorial, aprenderá a:

- Instrucciones sobre cómo crear la aplicación de medidores inteligentes de manera gratuita
- Tutorial de la aplicación
- Limpieza de recursos

## <a name="prerequisites"></a>Requisitos previos

* No se necesitan requisitos previos específicos para implementar esta aplicación.
* Puede usar el plan gratuito o una suscripción de Azure.

## <a name="create-a-smart-meter-monitoring-application"></a>Creación de una aplicación de supervisión de medidores inteligentes

1. Navegue al sitio de [Compilación Azure IoT Central](https://aka.ms/iotcentral). Después, inicie sesión con una cuenta Microsoft personal, profesional o educativa. Seleccione **Crear** en la barra de navegación de la izquierda y, a continuación, seleccione la pestaña **Energía**:

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-build.png" alt-text="Plantilla de medidor inteligente":::

1. En **Supervisión de medidores inteligentes**, seleccione **Crear aplicación**.

Para más información, consulte [Creación de una aplicación de Azure IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Recorrido por la aplicación.

Las secciones siguientes le guiarán por las características clave de la aplicación:

### <a name="dashboard"></a>Panel

Una vez que implemente la plantilla de la aplicación, esta incluirá un dispositivo de ejemplo de medidores inteligentes, un modelo de dispositivo y un panel. 

Adatum es una compañía energética ficticia que supervisa y administra los medidores inteligentes. En el panel de supervisión de medidores inteligentes, verá propiedades, datos y comandos de ejemplo de los medidores inteligentes. Este permite que los operadores y los equipos de soporte técnico realicen de forma proactiva las actividades siguientes antes de que se produzcan incidentes de soporte técnico: 
* Revisar la información más reciente del medidor y la [ubicación](../core/howto-use-location-data.md) de su instalación en el mapa
* Comprobar de manera proactiva la red del medidor y el estado de la conexión 
* Supervisar las lecturas de voltaje mínimas y máximas para el estado de la red 
* Revisar las tendencias de energía, alimentación y voltaje para detectar cualquier patrón anómalo 
* Realizar un seguimiento del consumo energético total para fines de planificación y facturación
* Realizar operaciones con comandos y controles, como, por ejemplo, volver a conectar el medidor y actualizar la versión del firmware. En la plantilla, los botones de comando muestran las funcionalidades posibles y no envían comandos reales. 

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png" alt-text="Panel de supervisión de medidores inteligentes.":::

### <a name="devices"></a>Dispositivos

La aplicación incluye un dispositivo de ejemplo de medidores inteligentes. Para ver los detalles del dispositivo, haga clic en la pestaña **Dispositivos**.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-devices.png" alt-text="Dispositivos de medidores inteligentes.":::

Haga clic en el vínculo **SM0123456789** del dispositivo de ejemplo para ver los detalles del dispositivo. Puede actualizar las propiedades de escritura del dispositivo en la página **Actualizar propiedades** y visualizar los valores actualizados en el panel.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png" alt-text="Propiedades de los medidores inteligentes.":::

### <a name="device-template"></a>Plantilla de dispositivo

Haga clic en la pestaña **Plantillas de dispositivo** para ver el modelo de dispositivo de medidores inteligentes. El modelo tiene una interfaz predefinida para los datos, las propiedades, los comandos y las vistas.

:::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-device-template.png" alt-text="Plantillas de dispositivos de medidores inteligentes.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

Si decide no seguir usando esta aplicación, elimínela con los siguientes pasos:

1. En el panel izquierdo, abra la pestaña Administración.
1. Seleccione la configuración de la aplicación y haga clic en el botón Eliminar situado en la parte inferior de la página. 

    :::image type="content" source="media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png" alt-text="Eliminar aplicación.":::

## <a name="next-steps"></a>Pasos siguientes

> [Tutorial: Implementación y recorrido por una plantilla de aplicación de paneles solares](tutorial-solar-panel-app.md)

