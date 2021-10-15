---
title: Métricas y alertas de Data Factory
description: Obtenga información sobre las métricas disponibles para la supervisión de Azure Data Factory.
author: minhe-msft
ms.author: hemin
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: monitoring
ms.topic: conceptual
ms.date: 09/02/2021
ms.openlocfilehash: 53f12eed1e39eb035f5ec69c825ab5658b207b03
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129400522"
---
# <a name="data-factory-metrics-and-alerts"></a>Métricas y alertas de Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Factory proporciona las siguientes métricas y alertas para habilitar la supervisión del servicio.

## <a name="data-factory-metrics"></a>Métricas de Data Factory

Con Azure Monitor, puede obtener visibilidad sobre el rendimiento y el estado de las cargas de trabajo de Azure. El tipo más importante de datos de Monitor es la métrica, que también se denomina contador de rendimiento. La mayoría de los recursos de Azure emiten métricas. Monitor proporciona varias maneras de configurar y consumir estas métricas para supervisar y solucionar problemas.

Estas son algunas de las métricas emitidas por Azure Data Factory, versión 2.

| **Métrica**                           | **Nombre de métrica para mostrar**                  | **Unidad** | **Tipo de agregación** | **Descripción**                |
|--------------------------------------|------------------------------------------|----------|----------------------|--------------------------------|
| ActivityCancelledRuns                 | Métricas de ejecuciones de actividad canceladas           | Count    | Total                | El número total de ejecuciones de actividad canceladas en un período de minutos. |
| ActivityFailedRuns                   | Métricas de ejecuciones de actividad erróneas             | Count    | Total                | El número total de ejecuciones de actividad en las que se produjo un error en período de minutos. |
| ActivitySucceededRuns                | Métricas de ejecución de actividad realizadas correctamente          | Count    | Total                | El número total de ejecuciones de actividad realizadas correctamente dentro de un período de minutos. |
| PipelineCancelledRuns                 | Métricas de ejecuciones de canalización canceladas           | Count    | Total                | El número total de ejecuciones de canalización canceladas en un período de minutos. |
| PipelineFailedRuns                   | Métricas de ejecuciones de canalización erróneas             | Count    | Total                | El número total de ejecuciones de canalización con error dentro de una período de minutos. |
| PipelineSucceededRuns                | Las métricas de ejecuciones de canalización se realizaron correctamente          | Count    | Total                | El número total de ejecuciones de canalización realizadas correctamente dentro de un período de minutos. |
| TriggerCancelledRuns                  | Métricas de ejecuciones de desencadenador canceladas            | Count    | Total                | El número total de ejecuciones de desencadenador canceladas en un período de minutos. |
| TriggerFailedRuns                    | Métricas de ejecuciones de desencadenador erróneas              | Count    | Total                | El número total de ejecuciones de desencadenador en las que se produjo un error dentro de un período de minutos. |
| TriggerSucceededRuns                 | Métricas de ejecuciones de desencadenador realizadas correctamente           | Count    | Total                | El número total de ejecuciones de desencadenador realizadas correctamente dentro de un período de minutos. |
| SSISIntegrationRuntimeStartCancelled  | Métricas de inicio de SSIS Integration Runtime cancelado           | Count    | Total                | Número total de inicios de SSIS Integration Runtime cancelados dentro de un período de un minuto. |
| SSISIntegrationRuntimeStartFailed    | Métricas de inicio de SSIS Integration Runtime con errores             | Count    | Total                | El número total de inicios de SSIS Integration Runtime con errores dentro de un período de un minuto. |
| SSISIntegrationRuntimeStartSucceeded | Métricas de inicio de SSIS Integration Runtime correcto          | Count    | Total                | El número total de inicios de SSIS Integration Runtime correctos dentro de un período de un minuto. |
| SSISIntegrationRuntimeStopStuck      | Métricas de detención de SSIS Integration Runtime bloqueada               | Count    | Total                | El número total de detenciones de SSIS Integration Runtime bloqueadas dentro de un período de un minuto. |
| SSISIntegrationRuntimeStopSucceeded  | Métricas de detención de SSIS Integration Runtime correcta           | Count    | Total                | El número total de detenciones de SSIS IR correctas dentro de un período de un minuto. |
| SSISPackageExecutionCancelled         | Métricas de ejecución de paquetes SSIS cancelada  | Count    | Total                | El número total de ejecuciones de paquetes SSIS canceladas en un período de minutos. |
| SSISPackageExecutionFailed           | Métricas de ejecución de paquetes SSIS con errores    | Count    | Total                | El número total de ejecuciones de paquetes SSIS con errores en un período de minutos. |
| SSISPackageExecutionSucceeded        | Métricas de ejecución de paquetes SSIS correcta | Count    | Total                | El número total de ejecuciones de paquetes SSIS correctas en un período de minutos. |
| PipelineElapsedTimeRuns | Métricas de ejecuciones de canalización con tiempo transcurrido | Count | Total | Número de veces, en un período de minutos, que una canalización se ejecuta más tiempo que la duración esperada definida por el usuario. [(Más información).](tutorial-operationalize-pipelines.md) |

Para acceder a las métricas, complete las instrucciones que aparecen en la [plataforma de datos de Azure Monitor](../azure-monitor/data-platform.md).

> [!NOTE]
> A excepción de _PipelineElapsedTimeRuns_, solo se emiten eventos de las ejecuciones de actividades y canalizaciones desencadenadas y completadas. Las ejecuciones de depuración y en curso *no* se emiten. Por otro lado, se emiten eventos de *todas* las ejecuciones de paquetes SSIS, incluidas las que se completan y están en curso, independientemente de sus métodos de invocación. Por ejemplo, puede invocar las ejecuciones de paquetes en SQL Server Data Tools (SSDT) habilitado para Azure mediante T-SQL en SQL Server Management Studio, el Agente SQL Server u otras herramientas designadas, y como ejecuciones desencadenadas o de depuración de las actividades de ejecución de paquetes SSIS en canalizaciones de Data Factory.

## <a name="data-factory-alerts"></a>Alertas de Data Factory

Inicie sesión en Azure Portal y haga clic en **Monitor** > **Alertas** para crear alertas.

:::image type="content" source="media/monitor-using-azure-monitor/alerts_image3.png" alt-text="Captura de pantalla en la que se muestran las alertas en el menú del portal.":::

### <a name="create-alerts"></a>Creación de alertas

1. Seleccione **+ Nueva regla de alertas** para crear una nueva alerta.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image4.png" alt-text="Captura de pantalla que muestra la creación de una nueva regla de alerta.":::

1. Defina la condición de la alerta.

    > [!NOTE]
    > Asegúrese de seleccionar **Todo** en la lista desplegable **Filtrar por tipo de recurso**.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image5.png" alt-text="Captura de pantalla que muestra las selecciones para abrir el panel para elegir un recurso.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image6.png" alt-text="Captura de pantalla que muestra las selecciones para abrir el panel para configurar una lógica de señal.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image7.png" alt-text="Captura de pantalla que muestra la configuración de la lógica de señal.":::

1. Defina los detalles de la alerta.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image8.png" alt-text="Captura de pantalla que muestra los detalles de la alerta.":::

1. Defina el grupo de acciones.

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image9.png" alt-text="Captura de pantalla que muestra la creación de una regla, con nuevo grupo de acciones resaltado.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image10.png" alt-text="Captura de pantalla que muestra la creación de un nuevo grupo de acción.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image11.png" alt-text="Captura de pantalla que muestra la configuración del correo electrónico, los SMS, el envío de cambios y la voz.":::

    :::image type="content" source="media/monitor-using-azure-monitor/alerts_image12.png" alt-text="Captura de pantalla en la que se muestra cómo definir un grupo de acciones.":::

## <a name="next-steps"></a>Pasos siguientes

[Configuración de diagnósticos y del área de trabajo](monitor-configure-diagnostics.md)
