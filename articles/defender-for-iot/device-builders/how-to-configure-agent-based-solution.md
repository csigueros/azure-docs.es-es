---
title: Configuración de una solución basada en agente de Azure Defender para IoT
description: Aprenda a configurar la recopilación de datos en una solución basada en agente de Azure Defender para IoT
ms.date: 05/26/2021
ms.topic: how-to
ms.openlocfilehash: 94f8548895da57efe450342a25bd5de086af0998
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018154"
---
# <a name="configure-azure-defender-for-iot-agent-based-solution"></a>Configuración de una solución basada en agente de Azure Defender para IoT  

En este artículo se describe cómo configurar la recopilación de datos en una solución basada en agente de Azure Defender para IoT.

## <a name="configure-data-collection"></a>Configuración de la recopilación de datos

Para configurar la recopilación de datos en una solución basada en agente de Azure Defender para IoT: 

1. Vaya a Azure Portal y seleccione la instancia de IoT Hub al que Defender para IoT está asociado. 

1. En el menú  **Seguridad** , seleccione **Configuración**. 

1. Seleccione  **Recopilación de datos**. 

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-collection.png" alt-text="Seleccionar Recopilación de datos en la opción Configuración del menú Seguridad.":::

## <a name="geolocation-and-ip-address-handling"></a>Administración de la ubicación geográfica y la dirección IP 

Para proteger una solución de IoT, se recopilan las direcciones IP de las conexiones entrantes y salientes de los dispositivos IoT, IoT Edge e IoT Hub, y se almacenan de forma predeterminada. Esta información es fundamental y se usa para detectar una conectividad anómala desde direcciones IP de origen sospechosas. Por ejemplo, cuando se realizan intentos de establecer conexiones desde una dirección IP de origen de una red de robots (botnet) conocida o desde una dirección IP de origen que se encuentre fuera de su geolocalización. El servicio Defender para IoT ofrece la flexibilidad necesaria para habilitar y deshabilitar la recopilación de los datos de la dirección IP en cualquier momento. 

Para habilitar o deshabilitar la recopilación de datos de direcciones IP: 

1. Abra IoT Hub y seleccione  **Configuración**  en el menú  **Seguridad** . 

1. Seleccione la pantalla  **Recopilación de datos**  y modifique la geolocalización y la configuración de control de direcciones IP para adaptarlas a sus necesidades. 

## <a name="log-analytics-creation"></a>Creación de Log Analytics 

Defender para IoT permite almacenar alertas de seguridad, recomendaciones y datos de seguridad sin procesar en el área de trabajo de Log Analytics. La ingesta de Log Analytics en IoT Hub está establecida en **desactivada** de forma predeterminada en la solución Defender para IoT. Es posible asociar Defender para IoT a un área de trabajo de Log Analytics, así como almacenar los datos de seguridad. 

De forma predeterminada, Defender para IoT almacena dos tipos de información en el área de trabajo de Log Analytics:
 
- Alertas de seguridad.

- Recomendaciones. 

Puede elegir agregar el almacenamiento de un tipo de información adicional, como `raw events`. 

> [!Note] 
> El almacenamiento de `raw events` en Log Analytics conlleva costos de almacenamiento adicionales. 

Para habilitar Log Analytics para trabajar con un microagente: 

1. Vaya a **Configuración del área de trabajo** > **Recopilación de datos** y cambie el control de alternancia a  **Activado**. 

1. Cree un área de trabajo de Log Analytics o adjunte uno existente. 

1. Compruebe que la opción  **Access to raw security data**  (Acceder a los datos de seguridad sin procesar) está seleccionada.  

    :::image type="content" source="media/how-to-configure-agent-based-solution/data-settings.png" alt-text="Asegúrese de que Access to raw security data (Acceder a los datos de seguridad sin procesar) está seleccionada.":::

1. Seleccione  **Guardar**.

Todos los meses los 5 primeros gigabytes de datos ingeridos por cliente en el servicio Azure Log Analytics son gratuitos. Cada gigabyte de datos ingeridos en el área de trabajo de Azure Log Analytics se retiene sin cargo adicional los 31 primeros días. Para más información sobre los precios, consulte [Precios de Log Analytics](https://azure.microsoft.com/pricing/details/monitor/). 

Para cambiar la configuración del área de trabajo de Log Analytics: 

1. En IoT Hub, en el menú **Seguridad**, seleccione  **Configuración**. 

1. Seleccione la pantalla  **Recopilación de datos**  y modifique la configuración del área de trabajo de Log Analytics que se ajuste a sus necesidades. 

Para acceder a las alertas del área de trabajo de Log Analytics después de la configuración:

1. Seleccione una alerta en Defender para IoT.

1. Seleccione **Investigate alerts in Log Analytics workspace** (Investigar alertas en el área de trabajo de Log Analytics).

Para acceder a las alertas del área de trabajo de Log Analytics después de la configuración:

1. Seleccione una recomendación en Defender para IoT.

1. Seleccione **Investigate recommendations in Log Analytics workspace** (Investigar recomendaciones en el área de trabajo de Log Analytics). 
 
Para obtener más información sobre cómo consultar datos de Log Analytics, vea  [Introducción a las consultas de registro en Azure Monitor](../../azure-monitor/logs/get-started-queries.md). 

## <a name="turn-off-defender-for-iot"></a>Desactivación de Defender para IoT 

Para activar o desactivar el servicio Defender para IoT en una instancia de IoT Hub concreta: 

1. En IoT Hub, en el menú **Seguridad**, seleccione  **Configuración**.

1. Seleccione la pantalla  **Recopilación de datos**  y modifique la configuración del área de trabajo de Log Analytics que se ajuste a sus necesidades.

## <a name="next-steps"></a>Pasos siguientes 

Pase al siguiente artículo para [configurar una solución](quickstart-configure-your-solution.md).