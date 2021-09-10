---
title: Investigación de la recomendación de puntos de referencia de CIS
description: Realice investigaciones básicas y avanzadas en función de las recomendaciones de línea base del sistema operativo.
ms.date: 08/11/2021
ms.topic: how-to
ms.openlocfilehash: 295345d43c48736f9f4572c8845f96560ce4f184
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738095"
---
# <a name="investigate-os-baseline-based-on-cis-benchmark-recommendation"></a>Investigación de la recomendación de línea base del sistema operativo (basada en el punto de referencia CIS)

Realice investigaciones básicas y avanzadas en función de las recomendaciones de línea base del sistema operativo.

## <a name="basic-os-baseline-security-recommendation-investigation"></a>Investigación básica de la recomendación de seguridad del sistema operativo  

Para investigar las recomendaciones de línea base del sistema operativo, vaya al portal de Azure Defender para IoT, en **IoT Hub**. Para más información, consulte cómo [investigar las recomendaciones de seguridad](quickstart-investigate-security-recommendations.md).

## <a name="advanced-os-baseline-security-recommendation-investigation"></a>Investigación de las recomendaciones avanzadas de seguridad del sistema operativo  

En esta sección se describe cómo conocer mejor los resultados de las pruebas de la línea base del sistema operativo y consultar eventos en Azure Log Analytics.  

La investigación de las recomendaciones avanzadas de seguridad de línea base del sistema operativo solo se admite mediante Log Analytics. Conecte defender para IoT a un área de trabajo de Log Analytics antes de continuar. Para más información sobre las recomendaciones avanzadas de seguridad de línea base del sistema operativo, consulte cómo [configurar una solución basada en agente de Azure Defender para IoT](how-to-configure-agent-based-solution.md).

Para consultar las alertas de los eventos de seguridad de IoT en Log Analytics:

1. Vaya a la página **Alertas**.

1. Seleccione **Investigate recommendations in Log Analytics workspace** (Investigar recomendaciones en el área de trabajo de Log Analytics).

Para consultar las recomendaciones de los eventos de seguridad de IoT en Log Analytics:

1. Vaya a la página **Recomendaciones**.

1. Seleccione **Investigate recommendations in Log Analytics workspace** (Investigar recomendaciones en el área de trabajo de Log Analytics).

1. Para ver los detalles de un dispositivo concreto, seleccione **Show Operation system (OS) baseline rules details** [Mostrar los detalles de las reglas de línea base del sistema operativo (SO)] en la página de vista rápida **Recommendation details** (Detalles de la recomendación).

   :::image type="content" source="media/how-to-investigate-cis-benchmark/recommendation-details.png" alt-text="Vea los detalles de un dispositivo específico.":::

Para consultar los eventos de seguridad de IoT en el área de trabajo de Log Analytics directamente:

1. Vaya a la página **Registros**.

    :::image type="content" source="media/how-to-investigate-cis-benchmark/logs.png" alt-text="Seleccione los registros en el panel izquierdo.":::

1. Seleccione **Investigate the alerts** (Investigar las alertas) o bien, seleccione la opción **Investigate the alerts in Log Analytics** (Investigar las alertas en Log Analytics) de cualquier recomendación de seguridad o alerta.

## <a name="useful-queries-to-investigate-the-os-baseline-resources"></a>Consultas útiles para investigar los recursos de línea base del sistema operativo

> [!Note]
> Asegúrese de reemplazar `<device-id>` por los nombres que asignó al dispositivo en cada una de las siguientes consultas.

### <a name="retrieve-the-latest-information"></a>Recuperación de la información más reciente

- **Error de flota de dispositivos**: ejecute la siguiente consulta para recuperar la información más reciente sobre las comprobaciones que produjeron error en la flota de dispositivos:

    ```kusto
    let lastDates = SecurityIoTRawEvent |
    where RawEventName == "Baseline" |
    summarize TimeStamp=max(TimeStamp) by DeviceId;
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId |
    extend event = parse_json(EventDetails) |
    where event.BaselineCheckResult == "FAIL" |
    project DeviceId, event.BaselineCheckId, event.BaselineCheckDescription
    ```

- **Error de dispositivo específico**: ejecute la siguiente consulta para recuperar la información más reciente sobre las comprobaciones que generaron error en un dispositivo concreto:  

    ```kusto
    let id = SecurityIoTRawEvent | 
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)) |
    where TimeGenerated <= now() |
    where RawEventName == "Baseline" |
    where DeviceId == "<device-id>" |
    summarize arg_max(TimeGenerated, IoTRawEventId) |
    project IoTRawEventId;
    SecurityIoTRawEvent |
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)), extraDetails = todynamic(EventDetails) |
    where IoTRawEventId == toscalar(id) |
    where extraDetails.BaselineCheckResult == "FAIL" |
    project DeviceId, CceId = extraDetails.BaselineCheckId, Description = extraDetails.BaselineCheckDescription
    ```

- **Error de dispositivo específico**: ejecute la siguiente consulta para recuperar la información más reciente sobre las comprobaciones que generaron error en un dispositivo concreto:

    ```kusto
    let id = SecurityIoTRawEvent |
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)) |
    where TimeGenerated <= now() |
    where RawEventName == "Baseline" |
    where DeviceId == "<device-id>" |
    summarize arg_max(TimeGenerated, IoTRawEventId) |
    project IoTRawEventId;
    SecurityIoTRawEvent |
    extend IoTRawEventId = extractjson("$.EventId", EventDetails, typeof(string)), extraDetails = todynamic(EventDetails) |
    where IoTRawEventId == toscalar(id) |
    where extraDetails.BaselineCheckResult == "ERROR" |
    project DeviceId, CceId = extraDetails.BaselineCheckId, Description = extraDetails.BaselineCheckDescription
    ```

- **Actualizar la lista de dispositivos de la flota de dispositivos que no superó una comprobación concreta**: ejecute esta consulta para recuperar la lista actualizada de dispositivos (en la flota del dispositivo) que no pudieron realizar una comprobación específica:  

    ```kusto
    let lastDates = SecurityIoTRawEvent |
    where RawEventName == "Baseline" |
    summarize TimeStamp=max(TimeStamp) by DeviceId;
    lastDates | join kind=inner (SecurityIoTRawEvent) on TimeStamp, DeviceId |
    extend event = parse_json(EventDetails) |
    where event.BaselineCheckResult == "FAIL" |
    where event.BaselineCheckId contains "6.2.8" |
    project DeviceId;
    ```

## <a name="next-steps"></a>Pasos siguientes

[Investigación de recomendaciones de seguridad](quickstart-investigate-security-recommendations.md).
