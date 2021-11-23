---
title: 'Visualización del registro de métricas del conector de IoT: API de Azure Healthcare'
description: En este artículo se explica cómo mostrar las métricas del conector de IoT
services: healthcare-apis
author: msjasteppe
ms.service: healthcare-apis
ms.subservice: iomt
ms.topic: how-to
ms.date: 11/22/2021
ms.author: jasteppe
ms.openlocfilehash: 88e5310a53bfd5fed79ae0a9a6efc06d91fdb0cf
ms.sourcegitcommit: 3d04177023a3136832adb561da831ccc8e9910c7
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/23/2021
ms.locfileid: "132933958"
---
# <a name="how-to-display-iot-connector-metrics"></a>Visualización de las métricas del conector de IoT

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, aprenderá a mostrar las métricas del conector de IoT en el Azure Portal. 

## <a name="display-metrics"></a>Mostrar métricas

1. En el área de trabajo de Azure Healthcare API, seleccione **Conectores de IoT.** 

     :::image type="content" source="media\iot-metrics\iot-workspace-displayed-with-connectors-button.png" alt-text="Seleccione el botón Conectores de IoT." lightbox="media\iot-metrics\iot-connectors-button.png"::: 

2. Seleccione el conector de IoT para el que desea mostrar las métricas.

    :::image type="content" source="media\iot-metrics\iot-connector-select.png" alt-text="Seleccione Conector de IoT para el que desea mostrar métricas." lightbox="media\iot-metrics\iot-connector-select.png":::
    
3. Seleccione **Métricas** en la página conector de IoT.

   :::image type="content" source="media\iot-metrics\iot-select-metrics.png" alt-text="Seleccione el botón Metrics (Métricas)." lightbox="media\iot-metrics\iot-metrics-button.png"::: 

4. En la página Métricas, puede crear las métricas que desea mostrar para el conector de IoT. En este ejemplo, elegiremos las siguientes selecciones:

    * **Ámbito** = Nombre del conector de IoT **(valor predeterminado)**
    * **Espacio de nombres de** métrica = Métricas estándar **(valor predeterminado)** 
    * **Métrica** = Métricas del conector de IoT que desea mostrar. En este ejemplo, elegiremos **Number of Incoming Messages (Número de mensajes entrantes).**
    * **Agregación** = ¿Cómo desea mostrar las métricas? En este ejemplo, elegiremos **Recuento**. 

    :::image type="content" source="media\iot-metrics\iot-select-metrics-to-display.png" alt-text="Seleccione Métricas para mostrar." lightbox="media\iot-metrics\iot-metrics-selection-close-up.png"::: 

5. Ahora podemos ver las métricas del conector de IoT **para el número de** mensajes entrantes que se muestran en el Azure Portal.

    > [!TIP]
    > Para agregar métricas adicionales, seleccione el **botón Agregar métrica** y elija sus opciones.

    :::image type="content" source="media\iot-metrics\iot-metrics-add-button.png" alt-text="Seleccione el botón Agregar métrica para agregar más métricas." lightbox="media\iot-metrics\iot-add-metric-button.png":::

    > [!IMPORTANT]
    > Si sale de la página Métricas, la configuración de métricas se pierde y tendrá que volver a crearse. Si desea guardar las métricas del conector de IoT para su visualización futura, puede anclarlas a un panel de Azure como un icono.

## <a name="pinning-metrics-tile-on-azure-portal-dashboard"></a>Icono Anclar métricas en Azure Portal panel

1. Para anclar el icono Métricas a Azure Portal panel, seleccione el **botón Anclar al** panel:

    :::image type="content" source="media\iot-metrics\iot-metrics-select-add-pin-to-dashboard.png" alt-text="Seleccione el botón Anclar al panel." lightbox="media\iot-metrics\iot-pin-to-dashboard-button.png":::

2. Seleccione el panel en el que desea mostrar las métricas del conector de IoT. En este ejemplo, usaremos un panel privado denominado `IoT connector Metrics` . Seleccione **Anclar** para agregar el icono Métricas al panel.

    :::image type="content" source="media\iot-metrics\iot-select-pin-to-dashboard.png" alt-text="Seleccione el panel y el botón Anclar para completar el proceso de anclado del panel." lightbox="media\iot-metrics\iot-select-pin-to-dashboard.png":::

3. Recibirá una confirmación de que el icono Métricas se agregó correctamente al panel.

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-pinned-successful.png" alt-text="Icono de métricas anclado correctamente al panel." lightbox="media\iot-metrics\iot-select-dashboard-pinned-successful.png":::

4. Una vez que haya recibido una confirmación correcta, seleccione **Panel**.

    :::image type="content" source="media\iot-metrics\iot-select-dashboard-with-metrics-tile.png" alt-text="Seleccione el botón Panel." lightbox="media\iot-metrics\iot-dashboard-button.png":::

5. Seleccione el panel al que ha anclado el icono Métricas. En este ejemplo, el panel es `IoT connector Metrics` . El panel mostrará el icono Métricas del conector de IoT que creó en los pasos anteriores.

    :::image type="content" source="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png" alt-text="Panel con el icono Métricas del conector de IoT anclado." lightbox="media\iot-metrics\iot-dashboard-with-metrics-tile-displayed.png":::

> [!TIP]
> Consulte la Guía de solución [de problemas del conector de](./iot-troubleshoot-guide.md) IoT para obtener ayuda para solucionar errores y problemas comunes.

## <a name="conclusion"></a>Conclusión 

Tener acceso a las métricas es esencial para la supervisión y la solución de problemas.  El conector de IoT le ayuda a realizar estas acciones a través de métricas. 

## <a name="next-steps"></a>Pasos siguientes

Consulte las preguntas más frecuentes sobre el conector de IoT.

>[!div class="nextstepaction"]
>[Preguntas más frecuentes sobre el conector de IoT](iot-connector-faqs.md)

(FHIR&#174;) es una marca registrada de HL7 y se usa con el permiso HL7.
