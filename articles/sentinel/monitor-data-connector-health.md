---
title: Seguimiento del estado de los conectores de datos con este libro de Microsoft Sentinel | Microsoft Docs
description: Use el libro de supervisión de estado para realizar un seguimiento de la conectividad y el rendimiento de los conectores de datos.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.devlang: na
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.openlocfilehash: 087ee5a8fd0b3f6337306532525c19db46a2c203
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524443"
---
# <a name="monitor-the-health-of-your-data-connectors-with-this-microsoft-sentinel-workbook"></a>Seguimiento del estado de los conectores de datos con este libro de Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Con el **libro de seguimiento de estado de conectores de datos** puede realizar un seguimiento del estado, la conectividad y el rendimiento de los conectores de datos desde Microsoft Sentinel. El libro proporciona supervisiones adicionales, detecta anomalías y proporciona información sobre el estado de ingesta de datos del área de trabajo. Puede usar la lógica del libro para supervisar el estado general de los datos ingeridos y crear vistas personalizadas y alertas basadas en reglas.

## <a name="use-the-health-monitoring-workbook"></a>Uso del libro de supervisión de estado

1. En el portal de Microsoft Sentinel, seleccione **Libros** en el menú **Administración de amenazas**.

1. En la galería **Libros**, escriba *Estado* en la barra de búsqueda y seleccione **Data collection health monitoring** (Supervisión del estado de conectores de datos) entre los resultados.

1. Seleccione **Ver plantilla** para usar el libro tal cual, o bien seleccione **Guardar** para crear una copia modificable del libro. Cuando se cree la copia, seleccione **Ver libro guardado**.

1. Una vez dentro del libro, seleccione primero la **suscripción** y el **área de trabajo** que desea ver y, a continuación, defina el **intervalo de tiempo** para filtrar los datos según sus necesidades. Use el conmutador de alternancia **Mostrar ayuda** para mostrar la explicación en contexto del libro.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-1.png" alt-text="página de aterrizaje del libro de supervisión de estado de conectores de datos" lightbox="media/monitor-data-connector-health/data-health-workbook-1.png":::

Hay tres secciones con pestañas en este libro:

1. En la pestaña **Información general** se muestra el estado general de la ingesta de datos en el área de trabajo seleccionada: medidas de volumen, tasas de EPS y hora de recepción del último registro.

1. La pestaña **Data collection anomalies** (Anomalías de recopilación de datos) lo ayudará a detectar anomalías en el proceso de recopilación de datos, por tabla y origen de datos. Cada pestaña presenta anomalías para una tabla determinada (la pestaña **General** incluye una colección de tablas). Las anomalías se calculan mediante la función **series_decompose_anomalies()** , que devuelve una **puntuación de las anomalías**. [Más información sobre esta función](/azure/data-explorer/kusto/query/series-decompose-anomaliesfunction?WT.mc_id=Portal-fx). Establezca los siguientes parámetros para la función que se va a evaluar:

    - **AnomaliesTimeRange**: este selector de tiempo se aplica solo a la vista de las anomalías de la recopilación de datos.
    - **SampleInterval**: el intervalo de tiempo para el cual se muestrean los datos. La puntuación de las anomalías se calcula solo en los datos del último intervalo.
    - **PositiveAlertThreshold**: este valor define el umbral de puntuación de anomalías positiva. Acepta valores decimales.
    - **NegativeAlertThreshold**: este valor define el umbral de puntuación de anomalías negativa. Acepta valores decimales.

        :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-2.png" alt-text="Página de anomalías del libro de supervisión de estado de conectores de datos" lightbox="media/monitor-data-connector-health/data-health-workbook-2.png":::

1. En la pestaña **Agent info** (Información del agente) se muestra información sobre el estado de los agentes de Log Analytics instalados en las distintas máquinas, ya sea una máquina virtual de Azure, otra máquina virtual en la nube o una máquina virtual local o física. Puede supervisar lo siguiente:

   - Ubicación del sistema

   - Latencia y estado de latido

   - Memoria disponible y espacio en disco

   - Operaciones del agente

    En esta sección, debe seleccionar la pestaña que describe el entorno de las máquinas: elija la pestaña **Azure-managed machines** (Máquinas administradas por Azure) si quiere ver solo las máquinas administradas por Azure Arc; elija la pestaña **All machines** (Todas las máquinas) para ver las máquinas administradas y que no son de Azure con el agente de Log Analytics instalado.

    :::image type="content" source="media/monitor-data-connector-health/data-health-workbook-3.png" alt-text="Página de información del agente del libro de supervisión de estado de conectores de datos" lightbox="media/monitor-data-connector-health/data-health-workbook-3.png":::

## <a name="next-steps"></a>Pasos siguientes
Descubra cómo [incorporar sus datos en Microsoft Sentinel](quickstart-onboard.md), [conectar orígenes de datos](connect-data-sources.md) y [tener visibilidad de datos y posibles amenazas](get-visibility.md).
