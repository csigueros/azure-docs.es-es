---
title: 'Tutorial: Análisis de los datos de llamadas fraudulentas mediante Azure Stream Analytics y visualización de los resultados en el panel de Power BI'
description: En este tutorial se proporciona una demostración de un extremo a otro completa de cómo usar Azure Stream Analytics para analizar las llamadas fraudulentas en una secuencia de llamadas de teléfono.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: eb13fa39452cac26fac2476b6f102248478b18a5
ms.sourcegitcommit: 63f3fc5791f9393f8f242e2fb4cce9faf78f4f07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/26/2021
ms.locfileid: "114689754"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Tutorial: Análisis de los datos de llamadas fraudulentas mediante Stream Analytics y visualización de los resultados en el panel de Power BI

En este tutorial se muestra cómo analizar los datos de llamadas de teléfono mediante Azure Stream Analytics. Los datos de llamadas de teléfono, generadas por una aplicación cliente, contienen llamadas fraudulentas que el trabajo de Stream Analytics filtrará. Puede usar las técnicas de este tutorial con otros tipos de detección de fraudes, como los fraudes asociados a tarjetas de crédito o el robo de identidad.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Generar datos de llamadas de teléfono de ejemplo y enviarlos a Azure Event Hubs.
> * Cree un trabajo de Stream Analytics.
> * Configurar la entrada y salida del trabajo.
> * Definir consultas para filtrar las llamadas fraudulentas.
> * Probar e iniciar el trabajo.
> * Visualizar los resultados en Power BI.

## <a name="prerequisites"></a>Prerrequisitos

Antes de empezar, asegúrese de seguir estos pasos:

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/).
* Descargue la aplicación de generación de eventos de llamada telefónica [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) del Centro de descarga de Microsoft u obtenga el código fuente de [GitHub](https://aka.ms/azure-stream-analytics-telcogenerator).
* Necesitará la cuenta de Power BI.

## <a name="sign-in-to-azure"></a>Inicio de sesión en Azure

Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-an-azure-event-hub"></a>Crear un centro de eventos de Azure

Para que Stream Analytics pueda analizar el flujo de datos de las llamadas fraudulentas, los datos se deben enviar a Azure. En este tutorial, enviará los datos a Azure mediante [Azure Event Hubs](../event-hubs/event-hubs-about.md).

Siga estos pasos para crear una instancia de Event Hub y enviar datos de la llamada a dicha instancia:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
2. Seleccione **Crear un recurso** > **Internet de las cosas** > **Event Hubs**.

   ![Creación de un centro de eventos de Azure mediante Azure Portal](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. Rellene el panel **Crear espacio de nombres** con los siguientes valores:

   |**Configuración**  |**Valor sugerido** |**Descripción**  |
   |---------|---------|---------|
   |Nombre     | asaTutorialEventHub        |  Un nombre único para identificar el espacio de nombres del centro de eventos.       |
   |Subscription     |   \<Your subscription\>      |   Seleccione una suscripción de Azure donde quiere crear el centro de eventos.      |
   |Resource group     |   MyASADemoRG      |  Seleccione **Crear nuevo** y escriba un nuevo nombre de grupo de recursos para la cuenta.       |
   |Location     |   Oeste de EE. UU. 2      |    Ubicación donde se puede implementar el espacio de nombres del centro de eventos.     |

4. Use las opciones predeterminadas en el resto de los valores y seleccione **Revisar y crear**. Seleccione **Crear** para iniciar la implementación.

   ![Creación de un espacio de nombres del centro de eventos en Azure Portal](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. Cuando se haya terminado de implementar el espacio de nombres, vaya a **Todos los recursos** y busque *asaTutorialEventHub* en la lista de recursos de Azure. Seleccione *asaTutorialEventHub* para abrirlo.

6. Luego, seleccione **+ Centro de eventos** y en el campo **Nombre**, escriba el nombre del centro de eventos. En **Recuento de particiones**, seleccione el valor 2.  Use las opciones predeterminadas en el resto de los valores y seleccione **Crear**. Espere a que la implementación se realice correctamente.

   ![Configuración del centro de eventos en Azure Portal](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Concesión de acceso al centro de eventos y obtención de una cadena de conexión

Para que una aplicación pueda enviar datos a Azure Event Hubs, el centro de eventos debe tener una directiva que permita el acceso. La directiva de acceso genera una cadena de conexión que incluye la información de autorización.

1. Vaya al centro de eventos que creó en el paso anterior, *MyEventHub*. Seleccione **Directivas de acceso compartido** en **Configuración** y, luego, seleccione **+Agregar**.

2. Asigne a la directiva el nombre **MyPolicy** y asegúrese de que la opción **Administrar** está seleccionada. Seleccione **Crear**.

   ![Creación de una directiva de acceso compartido para el centro de eventos](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. Una vez creada la directiva, haga clic en el nombre para abrirla. Busque **Cadena de conexión: clave principal**. Seleccione el botón de **copia** que hay al lado de la cadena de conexión.

   ![Guardar la cadena de conexión de la directiva de acceso compartido](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. Pegue la cadena de conexión en un editor de texto. Necesitará esta cadena de conexión en la sección siguiente.

   La cadena de conexión tiene el siguiente aspecto:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Observe que la cadena de conexión contiene varios pares clave-valor, separados por punto y coma: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** y **EntityPath**.

## <a name="start-the-event-generator-application"></a>Inicio de la aplicación de generación de eventos

Antes de iniciar la aplicación TelcoGenerator, debe configurarla para enviar datos a los centros de eventos de Azure que creó anteriormente.

1. Extraiga el contenido del archivo [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip).
2. Abra el archivo `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` en el editor de texto de su elección. Hay más de un archivo `.config`, por lo que debe asegurarse de abrir el correcto.

3. Actualice el elemento `<appSettings>` en el archivo de configuración con los detalles siguientes:

   * Como valor de la clave *EventHubName* use el valor de EntityPath en la cadena de conexión.
   * Como valor de la clave *Microsoft.ServiceBus.ConnectionString* use la cadena de conexión sin el valor de EntityPath. No olvide eliminar el punto y coma que precede al valor EntityPath.

4. Guarde el archivo.

5. A continuación abra una ventana de comandos y cambie a la carpeta en la que ha descomprimido la aplicación TelcoGenerator. Después, escriba el comando siguiente:

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   Este comando toma los parámetros siguientes:
   * Número de registros de datos de llamada por hora.
   * Porcentaje de probabilidad de fraude, que es la frecuencia con la que la aplicación debe simular una llamada fraudulenta. El valor 0,2 indica que aproximadamente el 20 % de los registros de llamada tendrán un aspecto fraudulento.
   * Duración en horas, que es el número de horas que debe ejecutarse la aplicación. La aplicación se puede detener en cualquier momento. Para ello, solo hay que finalizar el proceso (**Ctrl+C**) en la línea de comandos.

   Después de unos segundos, la aplicación comienza a mostrar los registros de llamada telefónica en la pantalla tal y como los envía al centro de eventos. Los datos de llamada de teléfono contienen los siguientes campos:

   |**Registro**  |**Definición**  |
   |---------|---------|
   |CallrecTime    |  Marca de tiempo para la hora de inicio de la llamada.       |
   |SwitchNum     |  Conmutador de teléfono que se usa para conectar la llamada. En este ejemplo, los conmutadores son cadenas que representan el país o la región de origen (Estados Unidos, China, Reino Unido, Alemania o Australia).       |
   |CallingNum     |  Número de teléfono del autor de la llamada.       |
   |CallingIMSI     |  Identidad del suscriptor móvil internacional (IMSI). Identificador único del autor de la llamada.       |
   |CalledNum     |   Número de teléfono del destinatario de la llamada.      |
   |CalledIMSI     |  Identidad del suscriptor móvil internacional (IMSI). Identificador único del destinatario de la llamada.       |

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics

Ahora que tiene un flujo de eventos de llamada, puede crear un trabajo de Stream Analytics que lea los datos desde el centro de eventos.

1. Para crear un trabajo de Stream Analytics, vaya a [Azure Portal](https://portal.azure.com/).

2. Seleccione **Crear un recurso** y busque **Trabajo de Stream Analytics**. Seleccione el icono **Trabajo de Stream Analytics** y haga clic en **Crear**.

3. Rellene el formulario **Nuevo trabajo de Stream Analytics** con los valores siguientes:

   |**Configuración**  |**Valor sugerido**  |**Descripción**  |
   |---------|---------|---------|
   |Nombre del trabajo     |  ASATutorial       |   Un nombre único para identificar el espacio de nombres del centro de eventos.      |
   |Suscripción    |  \<Your subscription\>   |   Seleccione una suscripción de Azure donde desea crear el trabajo.       |
   |Resource group   |   MyASADemoRG      |   Seleccione **Use existing** (Usar existente) y, después, escriba un nuevo nombre de grupo de recursos para la cuenta.      |
   |Location   |    Oeste de EE. UU. 2     |      Ubicación donde se puede implementar el trabajo. Es conveniente colocar el trabajo y el centro de eventos en la misma región para obtener el mejor rendimiento y no pagar por transferir datos de una región a otra.      |
   |Entorno de hospedaje    | Nube        |     Los trabajos de Stream Analytics se pueden implementar en la nube o en dispositivos perimetrales. Si elige la nube, podrá implementar en la nube de Azure y, si elige implementar en un dispositivo perimetral, podrá hacerlo en un dispositivo de IoT Edge.    |
   |Unidades de streaming     |    1       |      Las unidades de streaming representan los recursos informáticos que se necesitan para ejecutar un trabajo. De forma predeterminada, este valor se establece en 1. Para información sobre el escalado de unidades de streaming, consulte el artículo [Descripción y ajuste de las unidades de streaming](stream-analytics-streaming-unit-consumption.md).      |

4. Use las opciones predeterminadas en el resto de la configuración, seleccione **Crear** y espere a que la implementación se realice correctamente.

   ![Creación de un trabajo de Azure Stream Analytics](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Configuración de la entrada del trabajo

El siguiente paso es definir un origen de entrada para que el trabajo lea los datos mediante el centro de eventos que creó en la sección anterior.

1. En Azure Portal, abra la página **Todos los recursos** y busque el trabajo de Stream Analytics *ASATutorial*.

2. En la sección **Topología de trabajo** del trabajo de Stream Analytics, seleccione **Entradas**.

3. Haga clic en **Agregar entrada de flujo** y seleccione **Event Hub** (Centro de eventos). Rellene el formulario de entrada con los siguientes valores:

   |**Configuración**  |**Valor sugerido**  |**Descripción**  |
   |---------|---------|---------|
   |Alias de entrada     |  CallStream       |  Proporcione un nombre descriptivo para identificar la entrada. El alias de entrada debe tener una longitud de entre 3 y 63 caracteres, y solo puede incluir caracteres alfanuméricos, guiones y guiones bajos.       |
   |Suscripción    |   \<Your subscription\>      |   Seleccione la suscripción de Azure en la que creó el centro de eventos. El centro de eventos puede estar en la misma suscripción que el trabajo de Stream Analytics, o en otra diferente.       |
   |Espacio de nombres del centro de eventos    |  asaTutorialEventHub       |  Seleccione el espacio de nombres del centro de eventos que creó en la sección anterior. Todos los espacios de nombres del centro de eventos disponibles en su suscripción actual se muestran en la lista desplegable.       |
   |Nombre del centro de eventos    |   MyEventHub      |  Seleccione el centro de eventos que creó en la sección anterior. Todos los centros de eventos disponibles en la suscripción actual se muestran en la lista desplegable.       |
   |Nombre de la directiva del centro de eventos   |  MyPolicy       |  Seleccione la directiva de acceso compartido del centro de eventos que creó en la sección anterior. Todas las directivas de los centros de eventos disponibles en la suscripción actual se muestran en la lista desplegable.       |

4. Use las opciones predeterminadas en el resto de los valores y seleccione **Guardar**.

   ![Configuración de la entrada de Azure Stream Analytics](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Configuración de la salida del trabajo

El último paso consiste en definir un receptor de salida en el que el trabajo pueda escribir los datos transformados. En este tutorial, los datos se generan y se visualizan con Power BI.

1. En Azure Portal, abra **Todos los recursos** y seleccione el trabajo de Stream Analytics *ASATutorial*.

2. En la sección **Topología de trabajo** del trabajo de Stream Analytics, seleccione la opción **Salidas**.

3. Seleccione **+ Agregar** > **Power BI**. Luego, seleccione **Autorizar** y siga las indicaciones para autenticar Power BI.

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="Botón de autorizar para Power BI":::

4. Rellene el formulario con los siguientes detalles y seleccione **Guardar**:

   |**Configuración**  |**Valor sugerido**  |
   |---------|---------|
   |Alias de salida  |  MyPBIoutput  |
   |Área de trabajo de grupo| Mi área de trabajo |
   |Nombre del conjunto de datos  |   ASAdataset  |
   |Nombre de la tabla |  ASATable  |
   | Modo de autenticación | Token de usuario |

   ![Configuración de la salida de Stream Analytics](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   En este tutorial se utiliza el modo de autenticación *Token de usuario*. Para utilizar la identidad administrada, consulte [Uso de la identidad administrada para autenticar el trabajo de Azure Stream Analytics en Power BI](powerbi-output-managed-identity.md).

## <a name="create-queries-to-transform-real-time-data"></a>Creación de consultas para transformar datos en tiempo real

En este momento, tiene un trabajo de Stream Analytics configurado para leer un flujo de datos entrantes. El siguiente paso consiste en crear una consulta que analice los datos en tiempo real. Las consultas usan un lenguaje de tipo SQL que tiene algunas extensiones específicas para Stream Analytics.

En esta sección del tutorial, crea y prueba varias consultas para aprender algunas formas en las que puede transformar un flujo de entrada para su análisis. 

Las consultas que se creen aquí solo mostrarán los datos transformados en la pantalla. En una sección posterior, escribirá los datos transformados en Power BI.

Para más información sobre el lenguaje, consulte la [Referencia de lenguaje de consulta de Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="test-using-a-pass-through-query"></a>Prueba con una consulta de paso a través

Si quiere archivar todos los eventos, puede usar una consulta de paso a través para leer todos los campos de la carga útil del evento.

1. Acceda al trabajo de Stream Analytics en Azure Portal y seleccione **Consulta** en *Topología de trabajo*. 

2. En la ventana Consulta, escriba la consulta siguiente:

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Al igual que con SQL, las palabras clave no distinguen entre mayúsculas y minúsculas y un espacio en blanco no es significativo.

    En esta consulta, `CallStream` es el alias que especificó al crear la entrada. Si ha usado otro alias, use ese nombre en su lugar.

3. Seleccione **Consulta de prueba**.

    El trabajo de Stream Analytics ejecuta la consulta en los datos de muestra de la entrada y presenta la salida en la parte inferior de la ventana. El conjunto de resultados indica que tanto el centro de eventos como el trabajo de Stream Analytics están configurados correctamente.

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="Salida de ejemplo de la consulta de prueba":::

    El número exacto de registros que verá variará en función de los registros que se hayan capturado durante el muestreo.

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Reducción del número de campos mediante una proyección de columna

En muchos casos, el análisis no necesita todas las columnas del flujo de entrada. Puede usar una consulta que proyecte un conjunto de campos devueltos más pequeño que la consulta de paso a través.

Ejecute la consulta siguiente y observe la salida.

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Recuento de llamadas entrantes por región: Ventana de saltos de tamaño constante con agregación

Suponga que quiere saber el número de llamadas entrantes por región. En los datos de streaming, si desea ejecutar funciones de agregado, como obtener el recuento, tiene que segmentar el flujo en unidades temporales, ya que el flujo de datos es ilimitado. Para ello, use una [función de ventana](stream-analytics-window-functions.md) de Stream Analytics. Luego puede trabajar con los datos contenidos en esa ventana como unidad.

Para esta transformación, se busca una secuencia de ventanas temporales que no se superpongan; cada ventana tendrá un conjunto discreto de datos que se pueden agrupar y agregar. Este tipo de ventana se denomina *ventana de saltos de tamaño constante*. En la ventana de saltos de tamaño constante, puede ver el número de llamadas entrantes agrupadas por `SwitchNum`, que representa el país o la región en los que se realizó la llamada.

1. Pegue la siguiente consulta en el editor:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    En esta consulta se usa la palabra clave `Timestamp By` en la cláusula `FROM` para especificar el campo de marca de tiempo del flujo de entrada que se usa para definir la ventana de saltos de tamaño constante. En este caso, la ventana divide los datos en segmentos por el campo `CallRecTime` de cada registro. Si no se ha especificado este campo, la operación de ventana usa la hora en la que cada evento llega al centro de eventos. Vea "Tiempo de llegada frente a tiempo de aplicación" en [Referencia de lenguaje de consulta de Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). 

    La proyección incluye `System.Timestamp`, que devuelve una marca de tiempo para el final de cada ventana. 

    Para especificar que desea usar una ventana de saltos de tamaño constante, use la función [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) en la cláusula `GROUP BY`. En la función, especifique una unidad de tiempo (entre un microsegundo y un día) y un tamaño de ventana (el número de unidades). En este ejemplo, la ventana de saltos de tamaño constante se compone de intervalos de cinco segundos, por lo que se obtendrá un recuento por país o región de las llamadas correspondientes a cada cinco segundos.

2. Seleccione **Consulta de prueba**. En los resultados, observe que las marcas de tiempo que aparecen en **WindowEnd** reflejan incrementos de cinco segundos.

### <a name="detect-sim-fraud-using-a-self-join"></a>Detección de fraudes de SIM mediante una autocombinación

Para este ejemplo, se considera uso fraudulento aquellas llamadas cuyo origen sea un solo usuario, pero que se realicen en ubicaciones diferentes en intervalos inferiores a cinco segundos entre una y otra. Por ejemplo, el mismo usuario no puede legítimamente realizar una llamada desde Estados Unidos y Australia al mismo tiempo.

Para comprobar estos casos, puede usar una autocombinación de los datos de streaming que combine el flujo consigo mismo en función del valor `CallRecTime`. Luego puede buscar los registros de llamada en los que el valor `CallingIMSI` (el número donde se origina) sea el mismo, pero el valor `SwitchNum` (país o región de origen) no lo sea.

Cuando use una combinación con datos de streaming, la combinación debe especificar algunos límites en la diferencia de tiempo que puede haber entre filas coincidentes. Como ya se ha señalado, los datos de streaming son de ilimitados. Los límites de tiempo de la relación se especifican en la cláusula `ON` de la combinación, con la función `DATEDIFF`. En este caso, la combinación se basa en un intervalo de cinco segundos entre datos de llamada.

1. Pegue la siguiente consulta en el editor:

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    Esta consulta es similar a cualquier instrucción SQL JOIN, salvo por la función `DATEDIFF` en la combinación. Esta versión de `DATEDIFF` es específica de Stream Analytics y debe aparecer en la cláusula `ON...BETWEEN`. Los parámetros son una unidad de tiempo (segundos en este ejemplo) y los alias de los dos orígenes de la combinación. Esta función no es la misma que la función `DATEDIFF` de SQL estándar.

    La cláusula `WHERE` incluye la condición que marca la llamada fraudulenta: los conmutadores de origen no son iguales.

2. Seleccione **Consulta de prueba**. Revise la salida y, a continuación, seleccione **Guardar consulta**.

## <a name="start-the-job-and-visualize-output"></a>Inicio del trabajo y visualización de la salida

1. Para iniciar el trabajo, vaya a **Información general** del trabajo y seleccione **Iniciar**.

2. Seleccione **Ahora** como la hora de inicio de la salida del trabajo y seleccione **Iniciar**. El estado del trabajo se puede ver en la barra de notificación.

3. Una vez que el trabajo se inicia correctamente, vaya a [Power BI](https://powerbi.com/) e inicie sesión con su cuenta profesional o educativa. Si la consulta del trabajo de Stream Analytics genera resultados, el conjunto de datos *ASAdataset* que creó existe en la pestaña **Conjuntos de datos**.

4. En el área de trabajo de Power BI, seleccione **+ Crear** para crear un panel denominado *Fraudulent Calls* (Llamadas fraudulentas).

5. En la parte superior de la ventana, seleccione **Editar** y **Agregar icono**. Luego, seleccione **Datos de transmisión personalizados** y **Siguiente**. Elija **ASAdataset** en **Sus conjuntos de datos**. Seleccione **Tarjeta** en la lista desplegable **Tipo de visualización** y agregue **fraudulentcalls** a **Campos**. Seleccione **Siguiente** para escribir el nombre del icono y, después, seleccione **Aplicar** para crear el icono.

   ![Creación de iconos de paneles de Power BI](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. Siga de nuevo el paso 5, con las siguientes opciones:
   * Cuando llegue a Tipo de visualización, elija Gráfico de líneas.
   * Agregue un eje y seleccione **windowend**.
   * Agregue un valor y seleccione **fraudulentcalls**.
   * En **Período de tiempo para mostrar**, seleccione los últimos 10 minutos.

7. El panel debe ser similar al del ejemplo siguiente una vez que se agreguen ambos iconos. Observe que, si se ejecutan la aplicación del remitente del centro de eventos y la aplicación de Stream Analytics, el panel de Power BI se actualiza periódicamente cuando llegan nuevos datos.

   ![Ver los resultados en el panel de Power BI](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>Inserción del panel de Power BI en una aplicación web

En esta parte del tutorial, usará una aplicación web [ASP.NET](https://asp.net/) de ejemplo que ha creado el equipo de Power BI para insertar el panel. Para más información sobre cómo insertar paneles, consulte el artículo [Inserción con Power BI](/power-bi/developer/embedding).

Para configurar la aplicación, vaya al repositorio de GitHub [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) y siga las instrucciones de la sección **User Owns Data** (El usuario es propietario de los datos) (use las direcciones URL de redireccionamiento y de la página principal en el subapartado **integrate-web-app**). Como se va a usar el ejemplo del panel, utilice el código de ejemplo **integrate-web-app** que se encuentra en el [repositorio de GitHub](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/).
Cuando esté satisfecho con la aplicación que se ejecuta en el explorador, siga estos pasos para insertar el panel que creó anteriormente en la página web:

1. Seleccione **Sign in to Power BI** (Iniciar sesión en Power BI). Mediante esta acción se concede a la aplicación acceso a los paneles de la cuenta de Power BI.

2. Seleccione el botón **Get Dashboards** (Obtener paneles), que muestra los paneles de su cuenta en una tabla. Busque el nombre del panel que creó anteriormente **powerbi-embedded-dashboard** y copie el valor de **EmbedUrl** correspondiente.

3. Por último, pegue el valor de **EmbedUrl** en el campo de texto correspondiente y seleccione **Embed Dashboard** (Insertar panel). Ahora puede ver el mismo panel insertado en una aplicación web.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha creado un trabajo sencillo de Stream Analytics, ha analizado los datos de entrada y ha presentado los resultados en un panel de Power BI. Para más información sobre los trabajos de Stream Analytics, continúe con el tutorial siguiente:

> [!div class="nextstepaction"]
> [Ejecución de Azure Functions dentro de trabajos de Stream Analytics](stream-analytics-with-azure-functions.md)
