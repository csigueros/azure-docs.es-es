---
title: Visualización en tiempo real de eventos de inferencia de IA con Power BI
description: Puede usar Azure Video Analyzer para la grabación continua de vídeo o la grabación basada en eventos. Este tutorial le guía por los pasos para visualizar en tiempo real eventos de inferencia de inteligencia artificial de IoT Hub en Microsoft Power BI.
ms.service: azure-video-analyzer
ms.topic: tutorial
ms.date: 09/08/2021
ms.openlocfilehash: 68eb62e126065a6c39b5fd6648afb4b96accbd2d
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401928"
---
# <a name="tutorial-real-time-visualization-of-ai-inference-events-with-power-bi"></a>Tutorial: Visualización en tiempo real de eventos de inferencia de IA con Power BI

Azure Video Analyzer proporciona la funcionalidad de capturar, grabar y analizar vídeo en directo junto con la publicación de los resultados del análisis de vídeo en forma de eventos de inferencia de IA en el [centro de IoT Edge](../../iot-edge/iot-edge-runtime.md?view=iotedge-2020-11&preserve-view=true#iot-edge-hub). Estos eventos de inferencia de IA se pueden enrutar a otros destinos, incluidos Visual Studio Code y servicios de Azure como Time Series Insights y Event Hubs.

Los paneles son una manera esclarecedora de supervisar la empresa y visualizar todas las métricas más importantes de un vistazo. Puede visualizar eventos de inferencia de inteligencia artificial generados por Video Analyzer con [Microsoft Power BI](https://powerbi.microsoft.com/) mediante [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) para obtener información y compartir paneles rápidamente con los compañeros de la organización.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/tutorial-block-diagram.png" alt-text="Diagrama de bloques para conectar Azure Video Analyzer a Microsoft Power BI mediante Azure Stream Analytics.":::

En este tutorial va a:

- Crear y ejecutar un trabajo de Stream Analytics para recuperar los datos necesarios de IoT Hub y enviarlos a Power BI
- Ejecutar una canalización en directo que genera eventos de inferencia
- Crear un panel de Power BI para visualizar las inferencias de inteligencia artificial

## <a name="suggested-pre-reading"></a>Sugerencias antes de la lectura

- [Supervisión y registro](monitor-log-edge.md) en Video Analyzer
- Lectura de [mensajes del dispositivo a la nube desde puntos de conexión integrados de IoT Hub](../../iot-hub/iot-hub-devguide-messages-read-builtin.md)
- Introducción a los [paneles de Power BI](/power-bi/create-reports/service-dashboards)

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), en caso de que aún no lo haya hecho.
- Este tutorial se basa en el uso del [ejemplo de cruce de una línea](use-line-crossing.md) para detectar cuándo los objetos cruzan una línea virtual en una fuente de vídeo en directo. Puede optar por crear la visualización para otras canalizaciones: **se requiere una canalización con un receptor de mensajes de IoT Hub**. Asegúrese de crear la canalización en directo, pero actívela solo después de crear un trabajo de Stream Analytics.

  > [!TIP]
  >
  > - El [ejemplo de cruce de una línea](use-line-crossing.md) usa una grabación de vídeo de 5 minutos. Para obtener mejores resultados en la visualización, use la grabación de 60 minutos de vehículos en una autovía disponible en [Otros conjuntos de datos](https://github.com/Azure/video-analyzer/tree/main/media#other-dataset).
  > - Consulte la sección de configuración e implementación de [Preguntas más frecuentes](faq-edge.yml) sobre cómo agregar archivos de vídeo de ejemplo al simulador RTSP. Una vez agregado, edite el valor de `rtspUrl` para que apunte al nuevo archivo de vídeo.
  > - Si ha seguido el ejemplo de cruce de una línea y usa el [repositorio de ejemplo de C# de AVA](https://github.com/Azure-Samples/video-analyzer-iot-edge-csharp), edite el archivo operations.json en propiedades -> parámetros y establezca el valor en `"rtsp://rtspsim:554/media/camera-3600s.mkv"` para cambiar el origen de vídeo a la grabación de 60 minutos.

- Una cuenta de [Power BI](https://powerbi.microsoft.com/).

## <a name="create-and-run-a-stream-analytics-job"></a>Creación y ejecución de un trabajo de Stream Analytics

[Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) es un servicio de análisis en tiempo real totalmente administrado diseñado para ayudarle a analizar y procesar flujos de datos en rápido movimiento. En esta sección, creará un trabajo de Stream Analytics y definirá las entradas, las salidas y la consulta usada para recuperar los datos necesarios.

### <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics

1. En [Azure Portal](https://portal.azure.com/), haga clic en **Crear un recurso**. Escriba _Trabajo de Stream Analytics_ en el cuadro de búsqueda y selecciónelo en la lista desplegable. En la página de información general **Trabajo de Stream Analytics**, seleccione **Crear**.
2. Escriba la siguiente información del trabajo:

   - **Nombre del trabajo**: Nombre del trabajo. El nombre debe ser único globalmente.
   - **Suscripción**: elija la misma suscripción que se usó para configurar el ejemplo de cruce de una línea.
   - **Grupo de recursos**: use el mismo grupo de recursos que usa el centro de IoT Edge como parte de la configuración del ejemplo de cruce de una línea.
   - **Ubicación**: use la misma ubicación que el grupo de recursos.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-asa-job.png" alt-text="Captura de pantalla de la creación de un trabajo de Stream Analytics.":::

3. Seleccione **Crear**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adición de una entrada al trabajo de Stream Analytics

1. Abra el trabajo de Stream Analytics.
2. En Topología de trabajo, seleccione **Entradas**.
3. En el panel Entradas, seleccione **Agregar entrada de flujo** y, a continuación, seleccione **IoT Hub** en la lista desplegable. En el panel de la entrada nueva, escriba la siguiente información:

   - **Alias de entrada**: escriba un alias único para la entrada, como "iothubinput".
   - **Seleccionar centro de IoT de entre las suscripciones**: seleccione este botón de radio.
   - **Suscripción**: seleccione la suscripción de Azure que utiliza para este artículo.
   - **Centro de IoT**: seleccione el centro de IoT que se utilizó para configurar el ejemplo de cruce de una línea.
   - **Nombre de directiva de acceso compartido**: seleccione el nombre de la directiva de acceso compartido que quiera que utilice el trabajo de Stream Analytics para su centro de IoT. Para este tutorial, puede seleccionar iothubowner. Para obtener más información, consulte el tema sobre [control de acceso y permisos](../../iot-hub/iot-hub-dev-guide-sas.md#access-control-and-permissions).
   - **Clave de directiva de acceso compartido**: este campo se rellena automáticamente en función del nombre de directiva de acceso compartido que seleccione.

   Deje todos los demás campos establecidos en sus valores predeterminados.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-iothub-input.png" alt-text="Captura de pantalla para agregar la entrada de IoT Hub al trabajo de Stream Analytics.":::

4. Seleccione **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adición de una salida al trabajo de Stream Analytics

1. En Topología de trabajo, seleccione **Salidas**.
2. En el panel Salidas, seleccione **Agregar** y después **Power BI** en la lista desplegable.
3. En Power BI, Nuevo panel de salida:

   - **Alias de salida**: escriba un alias único para la salida, como "powerbioutput".
   - **Área de trabajo de grupo**: seleccione el área de trabajo de grupo de destino.
   - **Modo de autenticación**: deje el valor predeterminado "Token de usuario" con fines de prueba.

   > [!NOTE]
   > Para trabajos de producción, se recomienda [usar una identidad administrada para autenticar el trabajo de Stream Analytics en Power BI](../../stream-analytics/powerbi-output-managed-identity.md).

   - **Nombre del conjunto de datos**: escriba un nombre para el conjunto de datos.
   - **Nombre de la tabla**: Escriba un nombre de tabla.
   - **Autorizar**: seleccione Autorizar y siga las indicaciones para iniciar sesión en la cuenta de Power BI (el token es válido durante 90 días).

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/add-pbi-output.png" alt-text="Captura de pantalla para agregar la salida de Power BI al trabajo de Stream Analytics.":::

4. Seleccione **Guardar**.

   > [!NOTE]
   > Para más información sobre el uso de Power BI como salida para un trabajo de Stream Analytics, haga clic [aquí](../../stream-analytics/power-bi-output.md). Más información sobre la [renovación de la autenticación](../../stream-analytics/stream-analytics-power-bi-dashboard.md#renew-authorization) para la cuenta de Power BI.

### <a name="configure-the-query-for-stream-analytics-job"></a>Configuración de la consulta del trabajo de Stream Analytics

1. En Topología de trabajo, seleccione **Consulta**.
2. Realice los siguientes cambios en la consulta:

   ```SQL
   SELECT
       CAST(InferenceRecords.ArrayValue.event.properties.total AS bigint) as EventTotal,
       CAST(i.EventProcessedUtcTime AS datetime) as EventProcessedUtcTime
   INTO [YourOutputAlias]
   FROM [YourInputAlias] i
   CROSS APPLY GetArrayElements(inferences) AS InferenceRecords
   WHERE InferenceRecords.ArrayValue.subType = 'lineCrossing'
   ```

   > [!NOTE]
   >
   > - En la consulta anterior, la letra _i_ de la cláusula FROM es necesaria sintácticamente para capturar el valor de EventProcessedUtcTime, que no está anidado en la matriz _inferences_.
   >   La consulta anterior está personalizada para obtener inferencias de IA para el [tutorial de cruce de una línea](use-line-crossing.md).
   > - Si está ejecutando otra canalización, asegúrese de personalizar la consulta según el esquema de inferencia de IA de la canalización. Más información sobre [Análisis de datos JSON y AVRO en Azure Stream Analytics](../../stream-analytics/stream-analytics-parsing-json.md).

3. Reemplace [YourOutputAlias] por el alias de salida usado en el paso Adición de una salida al trabajo de Stream Analytics, como "powerbioutput". Tenga en cuenta la secuencia correcta de alias de entrada y salida.
4. Reemplace [YourInputAlias] por el alias de entrada usado en el paso para agregar una entrada al trabajo de Stream Analytics, como "iothubinput".
5. La consulta debe ser similar a la captura de pantalla siguiente. Haga clic en **Consulta de prueba** para comprobar los resultados de la prueba como una tabla de los valores de EventTotal y EventProcessedUtcTime correspondientes.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/asa-query.png" alt-text="Captura de pantalla para probar y guardar la consulta del trabajo de Stream Analytics.":::

6. Seleccione **Guardar consulta**.

### <a name="run-the-stream-analytics-job"></a>Ejecución del trabajo de Stream Analytics

En el trabajo de Stream Analytics, seleccione Información general y, a continuación, elija **Iniciar** > **Ahora** > **Iniciar**. Una vez que el trabajo se inicia correctamente, su estado cambia de Creado a **En ejecución**.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/start-asa.png" alt-text="Captura de pantalla para iniciar y ejecutar un trabajo de Stream Analytics.":::

> [!TIP]
> IoT Hub permite la retención de datos en el servicio Event Hubs integrado para 1 día y durante un máximo de 7 días. Puede establecer el tiempo de retención durante la creación del servicio IoT Hub. El tiempo de retención de datos en IoT Hub depende del nivel y el tipo de unidad elegidos. Haga clic [aquí](../../iot-hub/iot-hub-devguide-messages-read-builtin.md) para más información. Para una retención de datos más larga, use [Azure Storage como salida](../../stream-analytics/blob-storage-azure-data-lake-gen2-output.md) y, a continuación, conecte Power BI a los archivos de la cuenta de almacenamiento.

## <a name="run-a-sample-pipeline"></a>Ejecución de una canalización de ejemplo

Cuando el trabajo de Stream Analytics creado en el paso anterior esté en estado **En ejecución**, vaya a la sección [Ejecución del programa de ejemplo](use-line-crossing.md#run-the-sample-program) del tutorial de cruce de una línea y active la canalización en directo. La canalización en directo comenzará a enviar los resultados de inferencia de IA a IoT Hub y, a continuación, el trabajo de Stream Analytics los recogerá.

## <a name="create-a-power-bi-dashboard-to-visualize-ai-events"></a>Creación de un panel de Power BI para visualizar los eventos de inteligencia artificial

En Power BI, puede visualizar los datos de streaming de dos maneras:

1. Creación de informes a partir de una tabla creada para el conjunto de datos de streaming y su anclaje al panel
2. Un icono de panel con un conjunto de datos de streaming personalizado

   > [!NOTE]
   > En este artículo, usaremos el primer método para crear informes y, a continuación, anclarlos al panel. Este método conserva los datos del objeto visual durante más tiempo y los agrega automáticamente en función de los datos entrantes. Para más información sobre el segundo método, consulte [Configurar el conjunto de datos de transmisión en tiempo real en Power BI](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi).

### <a name="create-and-publish-a-power-bi-report"></a>Creación y publicación de un informe de Power BI

En los pasos siguientes se muestra cómo crear y publicar un informe mediante el servicio Power BI.

1. Asegúrese de que la canalización de ejemplo esté activada en el dispositivo (iniciada en el paso anterior para ejecutar una canalización).
2. Inicie sesión en la [cuenta de Power BI](https://powerbi.microsoft.com/) y seleccione **Servicio Power BI** en el menú superior.
3. En el menú lateral, seleccione el área de trabajo que ha utilizado.
4. En las pestañas **Todo** o **Conjuntos de datos y flujos de datos**, debería ver el conjunto de datos que ha especificado en el paso _Adición de una salida al trabajo de Stream Analytics_.
5. Mantenga el puntero sobre el conjunto de datos que proporcionó al crear la salida de Stream Analytics, seleccione el menú **Más opciones** (los tres puntos a la derecha del nombre del conjunto de datos) y después **Crear informe**.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/create-report.png" alt-text="Captura de pantalla para crear un informe en Power BI.":::

6. Cree un gráfico de líneas para mostrar los eventos de cruce de una línea en tiempo real.

   - En el panel **Visualizaciones** de la página de creación de informes, seleccione el icono **Gráfico de líneas** para agregar un gráfico de líneas.
   - En el panel **Campos**, expanda la tabla que especificó en el momento de crear la salida para el trabajo de Stream Analytics.
   - Arrastre **EventProcessedUtcTime** al **Eje** en el panel **Visualizaciones**.
   - Arrastre **EventTotal** a **Valores**.
   - Se ha creado un gráfico de líneas. El eje x muestra la fecha y hora en la zona horaria UTC. El eje Y muestra valores resumidos automáticamente para la **Suma** de EventTotal desde la canalización en directo. Cambie los valores de EventTotal a **Máximo** para obtener el valor más reciente de EventTotal. Puede cambiar la función de agregación para mostrar la media, el recuento (distinto), etc.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/power-bi/powerbi-report.png" alt-text="Captura de pantalla del informe de cruce de una línea en Power BI.":::

7. Haga clic en **Guardar** en la parte superior derecha para guardar el informe.

### <a name="pin-visual-to-a-dashboard"></a>Anclaje del objeto visual a un panel

Haga clic en **Anclar a un panel** en la parte superior derecha y seleccione dónde desea anclar este objeto visual: en un panel existente o en un nuevo panel y, a continuación, siga las indicaciones en consecuencia.

Opcionalmente, también puede [insertar un widget de reproductor](embed-player-in-power-bi.md) para ver la reproducción de vídeo en paralelo con el informe.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/power-bi/pinned-dashboard.png" alt-text="Panel de Power BI con un informe anclado y un widget de reproductor agregado en un icono.":::

> [!NOTE]
> La reproducción de vídeo y la creación de informes no estarán sincronizadas debido al método utilizado para generar informes. Para obtener una experiencia más cercana al tiempo real, consulte [Configurar el conjunto de datos de transmisión en tiempo real en Power BI](/power-bi/connect-data/service-real-time-streaming#set-up-your-real-time-streaming-dataset-in-power-bi).

## <a name="interpret-the-dashboard"></a>Interpretación del panel

El nodo procesador del cruce de una línea detecta cuándo un objeto cruza una línea especificada en la topología mediante el parámetro lineCoordinates. Cuando los objetos cruzan estas coordenadas, se desencadena un evento con:

- EventTotal: número total de cruces de línea por parte de cualquier objeto en cualquier dirección (en el sentido de las agujas del reloj o en el sentido contrario a las agujas del reloj) hasta ahora desde el principio del vídeo. Más información sobre [Eventos de cruce de líneas](use-line-crossing.md#line-crossing-events).
- Hora UTC de procesamiento del evento: hora a la que el trabajo de Stream Analytics procesó cada evento. Como alternativa, puede personalizar la consulta de Stream Analytics para capturar EventEnqueuedUtcTime, que indica la hora a la que llegó el evento al centro de IoT listo para su posterior procesamiento.

En el panel anterior, verá un número creciente de **EventTotal** a lo largo del tiempo a medida que cada vez más objetos cruzan la línea virtual. Esta visualización le permite identificar rápidamente que los vehículos que pasan por la autovía tienen una alta frecuencia entre las 3:52:00 y las 3:53:30 p. m., por ejemplo. A continuación, puede usar estas conclusiones para restringir el análisis sobre los motivos de la congestión del tráfico en determinados momentos en la autopista.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere probar otros artículos de inicio rápidos o tutoriales, conserve los recursos que creó. En caso contrario, vaya a Azure Portal, vaya a los grupos de recursos, seleccione el grupo de recursos en el que ejecutó los requisitos previos de este artículo y creó el trabajo de Stream Analytics y, a continuación, seleccione **Eliminar grupo de recursos**.

Creó el conjunto de datos **LineCrossingDataset** en Power BI. Para eliminarlo, inicie sesión en la cuenta de [Power BI](https://powerbi.microsoft.com/). En el menú de la izquierda, en **Áreas de trabajo**, seleccione el área de trabajo que utilizó. En la lista de conjuntos de datos de la pestaña **Conjuntos de datos y flujos de datos**, mantenga el puntero sobre el conjunto de datos. Seleccione los tres puntos verticales que aparecen a la derecha del nombre del conjunto de datos para abrir el menú **Más opciones**, seleccione **Eliminar** y siga las indicaciones. Al quitar el conjunto de datos, se quita también el informe.

## <a name="next-steps"></a>Pasos siguientes

- Utilice la [Inserción del widget de reproductor en Power BI](embed-player-in-power-bi.md) para combinar la reproducción de vídeo con los datos de telemetría.
- Más información en [Supervisión y registro en IoT Edge](monitor-log-edge.md).
