---
title: Procesamiento de datos de Azure Event Hubs con Stream Analytics | Microsoft Docs
description: En este artículo, se explica cómo se procesan los datos de un centro de eventos de Azure utilizando un trabajo de Azure Stream Analytics.
ms.date: 09/15/2021
ms.topic: article
ms.openlocfilehash: 31f8e6b7c5e45006b359c5e9545703f454dbacad
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128679813"
---
# <a name="process-data-from-your-event-hub-using-azure-stream-analytics"></a>Procesamiento de datos de un centro de eventos con Azure Stream Analytics 
El servicio Azure Stream Analytics permite ingerir, procesar y analizar fácilmente los datos de streaming procedentes de Azure Event Hubs, lo que proporciona valiosa información para emprender acciones en tiempo real. Esta integración le permite crear rápidamente una canalización de análisis de la ruta de acceso activa. Puede usar Azure Portal para visualizar los datos de entrada y escribir una consulta de Stream Analytics. Cuando la consulta esté lista, podrá transferirla a producción con solo unos pocos clics. 

## <a name="key-benefits"></a>Ventajas principales
Estas son las principales ventajas de la integración de Azure Event Hubs y Azure Stream Analytics: 
- **Vista previa de datos**: puede obtener una vista previa de los datos de entrada de un centro de eventos en Azure Portal.
- **Comprobación de la consulta**: prepare una consulta de transformación y pruébela directamente en Azure Portal. Si necesita información sobre la sintaxis del lenguaje de consulta, consulte la documentación sobre el [lenguaje de consulta de Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics).
- **Implementación de la consulta en producción**: puede implementar la consulta en producción creando e iniciando un trabajo de Azure Stream Analytics.

## <a name="end-to-end-flow"></a>Flujo integral

1. Inicie sesión en [Azure Portal](https://portal.azure.com). 
1. Vaya al **espacio de nombres de Event Hubs** y acceda al **centro de eventos** que contiene los datos de entrada. 
1. Seleccione **Procesar datos** en la página del centro de eventos.  

    ![Icono de Procesar datos](./media/process-data-azure-stream-analytics/process-data-tile.png)
1. En el icono **Enable real-time insights from events** (Extraer información en tiempo real de los eventos), seleccione **Explorar**. 

    ![Seleccionar el Stream Analytics](./media/process-data-azure-stream-analytics/process-data-page-explore-stream-analytics.png)
1. Aparecerá una página de consulta con los valores ya establecidos en los campos siguientes:
    1. El **centro de eventos**, como entrada de la consulta.
    1. Una **consulta SQL** de ejemplo con la instrucción SELECT. 
    1. Un alias de **salida** que hace referencia a los resultados de la prueba de consulta. 

        ![Editor de consultas](./media/process-data-azure-stream-analytics/query-editor.png)
        
        > [!NOTE]
        >  Si usa esta característica por primera vez, esta página le pedirá permiso para crear un grupo de consumidores y una directiva del centro de eventos para obtener una vista previa de los datos de entrada.
1. Seleccione **Crear** en el panel **Vista previa de la entrada**, tal como se muestra en la imagen anterior. 
1. Aparecerá inmediatamente una instantánea de los últimos datos de entrada en esta pestaña.
    - El tipo de serialización de los datos se detecta automáticamente (JSON o CSV). Puede cambiarlo manualmente a JSON, CSV o AVRO.
    - Puede obtener una vista previa de los datos de entrada en formato de tabla o en formato sin procesar. 
    - Si los datos que aparecen no están actualizados, seleccione **Actualizar** para ver los eventos más recientes. 

        A continuación, se incluye un ejemplo de datos en **formato de tabla**: ![Resultados en el formato de tabla](./media/process-data-azure-stream-analytics/snapshot-results.png)

        Este es un ejemplo de los datos con **formato sin procesar**: 

        ![Resultados en formato sin procesar](./media/process-data-azure-stream-analytics/snapshot-results-raw-format.png)
1. Seleccione **Test query** (Consulta de prueba) para ver una instantánea de los resultados de prueba de la consulta en la pestaña **Test results** (Resultados de la prueba). También puede descargar los resultados.

    ![Resultados de la consulta de prueba](./media/process-data-azure-stream-analytics/test-results.png)
1. Escriba su propia consulta para transformar los datos. Consulte [Stream Analytics Query Language reference](/stream-analytics-query/stream-analytics-query-language-reference) (Referencia del lenguaje de consulta de Stream Analytics).///
1. Cuando haya probado la consulta y desee moverla a producción, seleccione **Deploy query** (Implementar consulta). Para implementar la consulta, cree un trabajo de Azure Stream Analytics, donde podrá configurar una salida del trabajo e iniciarlo. Para crear un trabajo de Stream Analytics, especifique un nombre para el trabajo y seleccione **Create** (Crear).

      ![Creación de un trabajo de Azure Stream Analytics](./media/process-data-azure-stream-analytics/create-stream-analytics-job.png)

      > [!NOTE] 
      >  Le recomendamos que cree un grupo de consumidores y una directiva para cada nuevo trabajo de Azure Stream Analytics que cree en la página de Event Hubs. Los grupos de consumidores solo permiten cinco lectores simultáneos, por lo que, si se proporciona un grupo de consumidores dedicado para cada trabajo, se evitarán los errores que podrían surgir en caso de que se superara ese límite. Las directivas dedicadas le permiten cambiar la clave o revocar permisos sin afectar a otros recursos. 
1. El trabajo de Stream Analytics se crea en el mismo lugar de la consulta que coincide con la consulta probada; el centro de eventos se utiliza como entrada. 

9.  Para completar la canalización, establezca la **salida** de la consulta y seleccione **Start** (Iniciar) para iniciar el trabajo.

    > [!NOTE]
    > Antes de iniciar el trabajo, no olvide reemplazar el alias de salida por el nombre de salida que creó en Azure Stream Analytics.

      ![Definición de la salida e inicio del trabajo](./media/process-data-azure-stream-analytics/set-output-start-job.png)


## <a name="known-limitations"></a>Restricciones conocidas
Cuando se prueba la consulta, los resultados tardan aproximadamente seis segundos en cargarse. Estamos trabajando para mejorar el rendimiento de las pruebas. Sin embargo, cuando se implementa en producción, la latencia de Azure Stream Analytics es inferior a un segundo.

## <a name="streaming-units"></a>Unidades de streaming
El valor de Azure Stream Analytics se establece de forma predeterminada en tres unidades de streaming (SU). Para ajustar esta configuración, seleccione **Escala** en el menú izquierdo de la página **Trabajo de Stream Analytics** de Azure Portal. Para más información sobre las unidades de streaming, consulte [Descripción y ajuste de las unidades de streaming](../stream-analytics/stream-analytics-streaming-unit-consumption.md).

![Escalado de las unidades de streaming](./media/process-data-azure-stream-analytics/scale.png)

## <a name="next-steps"></a>Pasos siguientes
Para más información sobre de las consultas de Stream Analytics, consulte este artículo sobre el [lenguaje de consulta de Stream Analytics](/stream-analytics-query/built-in-functions-azure-stream-analytics)
