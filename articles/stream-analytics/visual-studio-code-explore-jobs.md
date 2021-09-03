---
title: Exploración de trabajos de Azure Stream Analytics con Visual Studio Code
description: Este artículo se muestra cómo exportar un trabajo de Azure Stream Analytics a un proyecto local, enumerar los trabajos y ver las entidades de Job.
ms.service: stream-analytics
author: su-jie
ms.author: sujie
ms.date: 07/21/2021
ms.topic: how-to
ms.openlocfilehash: 85b6dece608f47818f2115d0462dd4a56752581f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750599"
---
# <a name="explore-azure-stream-analytics-with-visual-studio-code-preview"></a>Exploración de Azure Stream Analytics con Visual Studio Code (versión preliminar)

Azure Stream Analytics para la extensión de Visual Studio Code ofrece a los desarrolladores una experiencia ligera para administrar los trabajos de Stream Analytics. Se puede usar en Windows, Mac y Linux. Con la extensión de Azure Stream Analytics puede:

- [Crear](quick-create-visual-studio-code.md), iniciar y detener trabajos
- Exportar los trabajos existentes a un proyecto local
- Enumerar los trabajos y ver las entidades job
- Visualización del diagrama de trabajo y depuración en el Monitor de trabajo

## <a name="export-a-job-to-a-local-project"></a>Exportar un trabajo a un proyecto local

Para exportar un trabajo a un proyecto local, busque el trabajo que desea exportar en el **explorador de Stream Analytics** en Visual Studio Code. Después seleccione una carpeta para el proyecto. El proyecto se exporta a la carpeta que seleccione, y puede seguir administrando el trabajo desde Visual Studio Code. Para más información sobre el uso de Visual Studio Code para administrar los trabajos de Stream Analytics, consulte el [inicio rápido](quick-create-visual-studio-code.md) de Visual Studio Code.

![Exportación de un trabajo de ASA en Visual Studio Code](./media/vscode-explore-jobs/export-job.png)

## <a name="list-job-and-view-job-entities"></a>Enumeración de trabajos y visualización de las entidades job

Puede usar la vista de trabajos para interactuar con trabajos de Azure Stream Analytics desde Visual Studio.


1. Haga clic en el icono de **Azure** en la barra de actividades de código de Visual Studio Code y, a continuación, expanda **nodo de Stream Analytics**. Los trabajos deben aparecer en las suscripciones.

   ![Apertura del explorador de Stream Analytics](./media/vscode-explore-jobs/open-explorer.png)

2. Expanda el nodo de trabajos, puede abrir y ver la consulta del trabajo, configuración, entradas, salidas y funciones. 

3. Haga clic con el botón derecho en el nodo de trabajo y elija el nodo **Open Job View in Portal** (Abrir vista de trabajo en el portal) para abrir la vista de trabajo en Azure Portal.

   ![Apertura de vista de trabajo en el portal](./media/vscode-explore-jobs/open-job-view.png)

## <a name="view-job-diagram-and-debug-in-job-monitor"></a>Visualización del diagrama de trabajo y depuración en el Monitor de trabajo

Puede usar el monitor de trabajo en Visual Studio Code para ver y solucionar problemas de los trabajos de Azure Stream Analytics.

### <a name="view-job-diagram-and-job-summary"></a>Visualización del diagrama de trabajo y el resumen del trabajo
1. Seleccione **Monitor de trabajo**. Debería aparecer el Monitor de trabajo y el diagrama de trabajos se debería cargar de forma automática.
   
   ![Apertura del Monitor de trabajo](./media/vscode-explore-jobs/open-job-monitor.png)

2.  Puede ver el diagrama del trabajo y hacer clic en **Resumen del trabajo** para ver propiedades e información del trabajo. 

      ![Visualización del resumen del trabajo](./media/vscode-explore-jobs/view-jobs-summary.png)

3.  Puede hacer clic en el botón **Probar la conexión** para probar la conexión a la entrada y la salida.

      ![Probar la conexión](./media/vscode-explore-jobs/test-connection.png)

4.  También puede hacer clic en el botón **Locate Script** (Localizar script) para ver la consulta.
   
      ![Visualización de la consulta](./media/vscode-explore-jobs/view-query.png)

### <a name="monitor-and-debug-with-metrics"></a>Supervisión y depuración con métricas

1.  Haga clic en el botón de flecha para abrir el panel Métricas.

      ![Apertura del panel Métricas](./media/vscode-explore-jobs/open-metrics-panel.png)

2.  Puede interactuar con él y analizar el trabajo con métricas clave que se muestran en el gráfico. Puede elegir ver métricas de nivel de trabajo o de nivel de nodos. Y también puede decidir qué métricas quiere que se muestren en el gráfico.

      ![Visualización de métricas](./media/vscode-explore-jobs/view-metrics.png)

### <a name="debug-with-diagnostic-logs-and-activity-logs"></a>Depuración con registros de diagnóstico y registros de actividad

Puede ver los registros de diagnóstico y los registros de actividad del trabajo para solucionar problemas.

1. Seleccione la pestaña **Registros de diagnóstico**.

   ![Visualización de los registros de diagnóstico](./media/vscode-explore-jobs/view-diagnostic-log.png)

2. Seleccione la pestaña **Registros de actividad**. 

   ![Visualización de los registros de actividad](./media/vscode-explore-jobs/view-activity-logs.png)

## <a name="next-steps"></a>Pasos siguientes

* [Creación de un trabajo en la nube de Azure Stream Analytics en Visual Studio Code (versión preliminar)](quick-create-visual-studio-code.md)
