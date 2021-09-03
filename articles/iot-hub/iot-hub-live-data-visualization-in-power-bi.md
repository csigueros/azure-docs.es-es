---
title: 'Visualización en tiempo real de datos de Azure IoT Hub: Power BI'
description: Use Power BI para visualizar datos de temperatura y humedad que se recopilan desde el sensor y se le envían a Azure IoT Hub.
author: robinsh
keywords: visualización de datos en tiempo real, visualización de datos en directo, visualización de datos de sensor
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 7/23/2021
ms.author: robinsh
ms.openlocfilehash: 4bfd0f761538516d771fd1bb00eb9dc625f46ce8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015609"
---
# <a name="tutorial-visualize-real-time-sensor-data-from-azure-iot-hub-using-power-bi"></a>Tutorial: Visualización de datos de sensor en tiempo real desde Azure IoT Hub mediante Power BI

Puede usar Microsoft Power BI para visualizar los datos de sensor en tiempo real que se reciben en el centro de Azure IoT. Para ello, configure un trabajo de Azure Stream Analytics a fin de consumir los datos de IoT Hub y enrutarlos a un conjunto de datos en Power BI. 

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/end-to-end-diagram.png" alt-text="Diagrama integral" border="false":::

 [Microsoft Power BI](https://powerbi.microsoft.com/) es una herramienta de visualización de datos que puede usar para realizar operaciones de autoservicio y de inteligencia empresarial (BI) en grandes conjuntos de datos. [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/#overview) es un servicio de análisis en tiempo real totalmente administrado diseñado para ayudarle a analizar y procesar flujos de datos rápidos que se pueden usar para obtener conclusiones, crear informes o desencadenar alertas y acciones. 

En este tutorial se realizan las siguientes tareas:

> [!div class="checklist"]
> * Crear un grupo de consumidores en el centro de IoT.
> * Crear y configurar un trabajo de Azure Stream Analytics para leer la telemetría de temperatura del grupo de consumidores y enviarla a Power BI.
> * Crear un informe de los datos de temperatura en Power BI y compartirlo con la web.

## <a name="prerequisites"></a>Requisitos previos

* Complete uno de los inicios rápidos de [Envío de telemetría](../iot-develop/quickstart-send-telemetry-iot-hub.md?pivots=programming-language-csharp) en el lenguaje de desarrollo que prefiera. Como alternativa, puede usar cualquier aplicación de dispositivo que envíe telemetría de temperatura;por ejemplo, el [simulador en línea de Raspberry Pi](iot-hub-raspberry-pi-web-simulator-get-started.md) o uno de los inicios rápidos de [Dispositivo insertado](../iot-develop/quickstart-devkit-mxchip-az3166.md). Estos artículos abarcan los requisitos siguientes:
  
  * Una suscripción de Azure activa.
  * Una instancia de Azure IoT Hub en la suscripción.
  * Una aplicación cliente que envíe mensajes al centro de Azure IoT.

* Una cuenta de Power BI ([pruebe Power BI de manera gratuita](https://powerbi.microsoft.com/)).

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Creación, configuración y ejecución de un trabajo de Stream Analytics

Comencemos creando un trabajo de Stream Analytics. Después de crear el trabajo, puede definir las entradas, las salidas y la consulta que se usa para recuperar los datos.

### <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics

1. En [Azure Portal](https://portal.azure.com), haga clic en **Crear un recurso**. Escriba *Trabajo de Stream Analytics* en el cuadro de búsqueda y selecciónelo en la lista desplegable. En la página de información general **Trabajo de Stream Analytics**, seleccione **Crear**.

2. Escriba la siguiente información para el trabajo.

   **Nombre del trabajo**: Nombre del trabajo. El nombre debe ser único globalmente.

   **Grupo de recursos**: use el mismo grupo de recursos que usa el centro de IoT.

   **Ubicación**: use la misma ubicación que el grupo de recursos.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/create-stream-analytics-job.png" alt-text="Creación de un trabajo de Stream Analytics en Azure":::

3. Seleccione **Crear**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Adición de una entrada al trabajo de Stream Analytics

1. Abra el trabajo de Stream Analytics.

2. En **Topología de trabajo**, seleccione **Entradas**.

3. En el panel **Entradas**, seleccione **Agregar entrada de flujo** y, a continuación, seleccione **IoT Hub** en la lista desplegable. En el panel de la entrada nueva, escriba la siguiente información:

   **Alias de entrada**: escriba un alias único para la entrada.

   **Seleccionar centro de IoT de entre las suscripciones**: seleccione este botón de radio.

   **Suscripción**: seleccione la suscripción de Azure que utiliza para este tutorial.

   **IoT Hub**: seleccione la instancia de IoT Hub que utiliza para este tutorial.

   **Punto de conexión**: seleccione **Mensajería**.

   **Nombre de directiva de acceso compartido**: seleccione el nombre de la directiva de acceso compartido que quiera que utilice el trabajo de Stream Analytics para su centro de IoT. Para este tutorial, puede seleccionar *service*. La directiva *service* se crea de forma predeterminada en los centros de IoT nuevos y concede permiso de envío y recepción para los puntos de conexión de la nube que expone el centro de IoT. Para obtener más información, consulte el tema sobre [control de acceso y permisos](iot-hub-dev-guide-sas.md#access-control-and-permissions).

   **Clave de directiva de acceso compartido**: este campo se rellena automáticamente en función del nombre de directiva de acceso compartido que seleccione.

   **Grupo de consumidores**: seleccione el grupo de consumidores que creó anteriormente.

   Deje todos los demás campos con sus valores predeterminados.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-input-to-stream-analytics-job.png" alt-text="Adición de una entrada a un trabajo de Stream Analytics en Azure":::

4. Seleccione **Guardar**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Adición de una salida al trabajo de Stream Analytics

1. En **Topología de trabajo**, seleccione **Salidas**.

2. En el panel **Salidas**, seleccione **Agregar** y después **Power BI** en la lista desplegable.

3. En el panel **Power BI: Nueva salida** panel, seleccione **Autorizar** y siga las indicaciones para iniciar sesión en su cuenta de Power BI.

4. Una vez que haya iniciado sesión en Power BI, escriba la siguiente información:

   **Alias de salida**: alias único para la salida.

   **Área de trabajo de grupo**: seleccione el área de trabajo de grupo de destino.

   **Nombre del conjunto de datos**: escriba un nombre para el conjunto de datos.

   **Nombre de la tabla**: Escriba un nombre de tabla.

   **Modo de autenticación**: Deje la opción predeterminada.

   :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-output-to-stream-analytics-job.png" alt-text="Adición de una salida a un trabajo de Stream Analytics en Azure":::

5. Seleccione **Guardar**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Configuración de la consulta del trabajo de Stream Analytics

1. En **Topología de trabajo**, seleccione **Consulta**.

2. Reemplace `[YourInputAlias]` por el alias de entrada del trabajo.

3. Reemplace `[YourOutputAlias]` por el alias de salida del trabajo.

1. Agregue la siguiente cláusula `WHERE` como última línea de la consulta. Esta línea garantiza que solo los mensajes con una propiedad **temperature** se reenviarán a Power BI.

    ```sql
    WHERE temperature IS NOT NULL
    ```
1. La consulta debe ser similar a la captura de pantalla siguiente. Seleccione **Guardar consulta**.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/add-query-to-stream-analytics-job.png" alt-text="Adición de una consulta a un trabajo de Stream Analytics":::

### <a name="run-the-stream-analytics-job"></a>Ejecución del trabajo de Stream Analytics

En el trabajo de Stream Analytics, seleccione **Información general** y, a continuación, elija **Iniciar** > **Ahora** > **Iniciar**. Una vez que el trabajo se inicia correctamente, su estado cambia de **Detenido** a **En ejecución**.

:::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/run-stream-analytics-job.png" alt-text="Ejecución de un trabajo de Stream Analytics en Azure":::

## <a name="create-and-publish-a-power-bi-report-to-visualize-the-data"></a>Creación y publicación de un informe de Power BI para visualizar los datos

En los pasos siguientes se muestra cómo crear y publicar un informe mediante el servicio Power BI. Puede seguir estos pasos, con algunas modificaciones, si desea utilizar el "nuevo aspecto" en Power BI. Para comprender las diferencias y cómo navegar dentro del "nuevo aspecto", consulte [Nuevo aspecto del servicio Power BI](/power-bi/fundamentals/desktop-latest-update).

1. Asegúrese de que la aplicación cliente se ejecuta en el dispositivo.

2. Inicie sesión en la cuenta de [Power BI](https://powerbi.microsoft.com/) y seleccione **Servicio Power BI** en el menú superior.

3. En el menú lateral, seleccione el área de trabajo que ha usado, **Mi área de trabajo**.

4. En las pestañas **Todo** o **Conjuntos de datos y flujos de datos** debería ver el conjunto de datos que ha especificado al crear la salida para el trabajo de Stream Analytics.

5. Mantenga el puntero sobre el conjunto de datos que ha creado, seleccione el menú **Más opciones** (los tres puntos a la derecha del nombre del conjunto de datos) y después **Crear informe**.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-create-report.png" alt-text="Creación de informe de Microsoft Power BI":::

6. Cree un gráfico de líneas para mostrar la temperatura en tiempo real en un período determinado.

   1. En el panel **Visualizaciones** de la página de creación de informes, seleccione el icono de gráfico de líneas para agregar un gráfico de líneas. Use las guías ubicadas en los lados y esquinas del gráfico para ajustar su tamaño y posición.

   2. En el panel **Campos**, expanda la tabla que especificó en el momento de crear la salida para el trabajo de Stream Analytics.

   3. Arrastre **EventEnqueuedUtcTime** (Hora UTC de evento en cola) al **Eje** en el panel **Visualizaciones**.

   4. Arrastre **temperature** (temperatura) a **Valores**.

      Se ha creado un gráfico de líneas. El eje x muestra la fecha y hora en la zona horaria UTC. El eje y muestra la temperatura del sensor.

      :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-add-temperature.png" alt-text="Adición de un gráfico de líneas de temperatura a un informe de Microsoft Power BI":::

     > [!NOTE]
     > En función del dispositivo o dispositivo simulado que use para enviar datos de telemetría, es posible que tenga una lista de campos ligeramente diferente.
     >

8. Seleccione **Guardar** para guardar el informe. Cuando se le solicite, escriba un nombre para el informe. Cuando se le solicite una etiqueta de confidencialidad, puede seleccionar **Público** y después **Guardar**.

10. Todavía en el panel del informe, seleccione **Archivo** > **Insertar informe** > **Sitio web o portal**.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-select-embed-report.png" alt-text="Selección del sitio web de inserción de informes para el informe de Microsoft Power BI":::

    > [!NOTE]
    > Si recibe una notificación para ponerse en contacto con el administrador para habilitar la creación de código para insertar, puede que tenga que ponerse en contacto con él. La creación del código para insertar tiene que estar habilitada para poder completar este paso.
    >
    > :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/contact-admin.png" alt-text="Notificación de contacto con el administrador":::


11. Se le ofrecerá el vínculo del informe, que puede compartir con cualquiera para concederle acceso a este, y un fragmento de código para que pueda integrarlo en un blog o sitio web. Copie el vínculo en la ventana **Código de inserción segura** y después ciérrela.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/copy-secure-embed-code.png" alt-text="Copia del vínculo para insertar el informe":::

12. Abra un explorador web y pegue el vínculo en la barra de dirección.

    :::image type="content" source="./media/iot-hub-live-data-visualization-in-power-bi/power-bi-web-output.png" alt-text="Publicación de informe de Microsoft Power BI":::

Microsoft también ofrece las [aplicaciones móviles de Power BI](https://powerbi.microsoft.com/documentation/powerbi-power-bi-apps-for-mobile-devices/) para ver e interactuar con los informes y paneles de Power BI desde su dispositivo móvil.

## <a name="cleanup-resources"></a>Limpieza de recursos

En este tutorial, ha creado un grupo de recursos, un centro de IoT, un trabajo de Stream Analytics y un conjunto de datos en Power BI. 

Si tiene pensado completar otros tutoriales, es posible que le interese conservar el centro de IoT y el grupo de recursos, y volver a usarlos más adelante. 

Si ya no necesita el centro de IoT o los demás recursos que ha creado, puede eliminar el grupo de recursos en el portal. Para ello, seleccione el grupo de recursos y, después, **Eliminar grupo de recursos**. Si quiere conservar el centro de IoT, puede eliminar otros recursos en el panel **Información general** del grupo de recursos. Para ello, haga clic con el botón derecho en el recurso, seleccione **Eliminar** en el menú contextual y siga las indicaciones. 

### <a name="use-the-azure-cli-to-clean-up-azure-resources"></a>Uso de la CLI de Azure para limpiar recursos de Azure

Para eliminar el grupo de recursos y todos sus recursos, use el comando [az group delete](/cli/azure/group#az_group_delete).

```azurecli-interactive
az group delete --name {your resource group}
```

### <a name="clean-up-power-bi-resources"></a>Limpieza de recursos de Power BI

Ha creado un conjunto de datos, **PowerBiVisualizationDataSet**, en Power BI. Para eliminarlo, inicie sesión en la cuenta de [Power BI](https://powerbi.microsoft.com/). En el menú de la izquierda, en **Áreas de trabajo**, seleccione **Mi área de trabajo**. En la lista de conjuntos de datos de la pestaña **Conjuntos de datos y flujos de datos**, mantenga el puntero sobre el conjunto de datos **PowerBiVisualizationDataSet**. Seleccione los tres puntos verticales que aparecen a la derecha del nombre del conjunto de datos para abrir el menú **Más opciones**, seleccione **Eliminar** y siga las indicaciones. Al quitar el conjunto de datos, se quita también el informe.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a usar Power BI para visualizar datos de sensor en tiempo real desde el centro de Azure IoT por medio de las siguientes tareas:

> [!div class="checklist"]
> * Crear un grupo de consumidores en el centro de IoT.
> * Crear y configurar un trabajo de Azure Stream Analytics para leer la telemetría de temperatura del grupo de consumidores y enviarla a Power BI.
> * Configurar un informe de los datos de temperatura en Power BI y compartirlo con la web.

Para obtener otra manera de visualizar datos desde Azure IoT Hub, vea el siguiente artículo.

> [!div class="nextstepaction"]
> [Uso de una aplicación web para visualizar datos de sensor en tiempo real desde Azure IoT Hub](iot-hub-live-data-visualization-in-web-apps.md).