---
title: 'Tutorial: Supervisión de paneles solares de Azure IoT | Microsoft Docs'
description: En este tutorial se muestra cómo implementar y usar la plantilla de aplicación de supervisión de paneles solares para IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 59650af808a5af947cbb2fd6df1f203692fb7034
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122182784"
---
# <a name="tutorial-deploy-and-walk-through-the-solar-panel-monitoring-app-template"></a>Tutorial: Implementación y tutorial de la plantilla de aplicación de supervisión de paneles solares 

Use la plantilla de aplicación de *supervisión de paneles solares* de IoT Central y las instrucciones de este artículo para desarrollar una solución de supervisión de paneles solares de un extremo a otro.

  :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-app-architecture.png" alt-text="Arquitectura de paneles solares":::

Esta arquitectura consta de los siguientes componentes. Puede que algunas aplicaciones no requieran todos los componentes que se mencionan aquí.

### <a name="solar-panels-and-connectivity"></a>Paneles solares y conectividad

Los paneles solares son una fuente significativa de energía renovable. En función del tipo de panel solar y de la configuración, puede conectarlo mediante puertas de enlace u otros dispositivos intermedios y sistemas de propiedad. Puede que necesite crear un dispositivo de puente de IoT Central para conectar los dispositivos que no pueden hacerlo directamente. El puente de dispositivo de IoT Central es una solución de código abierto y puede encontrar los detalles completos [aquí](../core/howto-build-iotc-device-bridge.md). 

### <a name="iot-central-platform"></a>Plataforma de IoT Central

Azure IoT Central es una plataforma que simplifica la compilación de su solución de IoT y permite reducir la carga y los costos de la administración, las operaciones y el desarrollo de IoT. Con IoT Central, puede conectar, supervisar y administrar fácilmente sus recursos de Internet de las cosas (IoT) a escala. Después de conectar los paneles solares a IoT Central, la plantilla de aplicación usa características integradas, como modelos de dispositivos, comandos y paneles. La plantilla de la aplicación también usa el almacenamiento de IoT Central para escenarios de ruta de acceso activa, como la supervisión, el análisis, las reglas y la visualización de datos de medición casi en tiempo real.

### <a name="extensibility-options-to-build-with-iot-central"></a>Opciones de extensibilidad para la compilación con IoT Central

La plataforma de IoT Central proporciona dos opciones de extensibilidad: Exportación de datos continua (CDE) y API. Los clientes y asociados pueden elegir entre estas opciones en función de la personalización de sus soluciones para necesidades específicas. Por ejemplo, uno de nuestros asociados configuró CDE con Azure Data Lake Storage (ADLS). Usa ADLS para la retención de datos a largo plazo y otros escenarios de almacenamiento de la ruta de acceso pasiva, como el procesamiento por lotes, la auditoría e informes. 


En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Crear una aplicación de paneles solares gratis
> * Recorrido por la aplicación.
> * Limpieza de recursos


## <a name="prerequisites"></a>Requisitos previos

* No se necesitan requisitos previos específicos para implementar esta aplicación.
* Puede usar el plan gratuito o una suscripción de Azure.


## <a name="create-a-solar-panel-monitoring-application"></a>Creación de una aplicación de supervisión de paneles solares


1. Navegue al sitio de [Compilación Azure IoT Central](https://aka.ms/iotcentral). Después, inicie sesión con una cuenta Microsoft personal, profesional o educativa. Seleccione **Compilar** en la barra de navegación de la izquierda y la pestaña **Energía**:

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-build.png" alt-text="Plantilla de medidor inteligente":::

1. En **Supervisión de paneles solares**, seleccione **Crear aplicación**.

Para más información, consulte [Creación de una aplicación de Azure IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Recorrido por la aplicación.

Las secciones siguientes le guiarán por las características clave de la aplicación:

### <a name="dashboard"></a>Panel

Después de implementar la plantilla de la aplicación, querrá explorar la aplicación un poco más. Observe que se incluyen el dispositivo medidor inteligente de ejemplo, el modelo de dispositivo y el panel.

Adatum es una compañía energética ficticia que supervisa y administra paneles solares. En el panel de supervisión de paneles solares, verá propiedades, datos y comandos de ejemplo de los paneles solares. Este panel permite al usuario o al equipo de soporte técnico realizar las siguientes actividades de forma proactiva, antes de que los problemas requieran soporte técnico adicional:
* Revisar la información más reciente del panel y la [ubicación](../core/howto-use-location-data.md) de su instalación en el mapa.
* Comprobar el estado del panel y el estado de la conexión.
* Revisar las tendencias de generación de energía y temperatura para detectar cualquier patrón anómalo.
* Realizar un seguimiento de la generación total de energía para fines de planeamiento y facturación.
* Activar un panel y actualizar la versión del firmware, si es necesario. En la plantilla, los botones de comando muestran las funcionalidades posibles y no envían comandos reales.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png" alt-text="Captura de pantalla del panel de la plantilla de supervisión de paneles solares.":::

### <a name="devices"></a>Dispositivos

La aplicación incluye un dispositivo de ejemplo de paneles solares. Para ver los detalles del dispositivo, seleccione **Devices** (Dispositivos).

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device.png" alt-text="Captura de pantalla de los dispositivos de la plantilla de supervisión de paneles solares.":::

Seleccione el dispositivo de ejemplo, **SP0123456789**. En la pestaña **Update Properties** (Actualizar propiedades), puede actualizar las propiedades de escritura del dispositivo y visualizar los valores actualizados en el panel. 

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png" alt-text="Captura de pantalla de la pestaña para actualizar propiedades de la plantilla de supervisión de paneles solares.":::


### <a name="device-template"></a>Plantilla de dispositivo

Para ver el modelo de dispositivo del panel solar, seleccione la pestaña **Device templates** (Plantillas de dispositivo). El modelo tiene interfaces predefinidas para datos, propiedades, comandos y vistas.

:::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png" alt-text="Captura de pantalla de las plantillas de dispositivo de la plantilla de supervisión de paneles solares.":::


## <a name="clean-up-resources"></a>Limpieza de recursos

Si decide no seguir usando esta aplicación, elimínela con los siguientes pasos:

1. En el panel izquierdo, seleccione **Administration** (Administración).
1. Seleccione **Application settings** > **Delete** (Configuración de la aplicación > Eliminar). 

    :::image type="content" source="media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png" alt-text="Captura de pantalla de la opción de administración de la plantilla de supervisión de paneles solares.":::
