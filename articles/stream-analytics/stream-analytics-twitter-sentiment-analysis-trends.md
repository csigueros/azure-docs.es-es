---
title: Análisis de redes sociales mediante Azure Stream Analytics
description: En este artículo se describe cómo usar Stream Analytics para analizar las redes sociales mediante la API de cliente de Twitter. Instrucciones paso a paso desde la generación de eventos a los datos en un panel dinámico.
services: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: a2459966b364e2f69b0c0dded6ef5dc1de941dd3
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039113"
---
# <a name="social-media-analysis-with-azure-stream-analytics"></a>Análisis de redes sociales mediante Azure Stream Analytics

En este artículo se enseña a crear una solución de análisis de sentimiento para análisis de redes sociales, para lo cual se ponen eventos de Twitter en tiempo real en Azure Event Hubs. Escriba una consulta de Azure Stream Analytics para analizar los datos y almacenar los resultados para usarlos más tarde, o bien cree un panel de [Power BI](https://powerbi.com/) para ofrecer información en tiempo real.

Las herramientas de análisis de las redes sociales ayudan a las organizaciones a comprender temas populares. Los temas populares son cuestiones y actitudes que tienen un gran volumen de entradas en las redes sociales. Por su parte, los análisis de sentimiento, denominados también *minería de opinión*, usan las herramientas de análisis de las redes sociales para determinar las actitudes hacia un producto o una idea. 

El análisis de tendencias de Twitter en tiempo real es un buen ejemplo de herramienta de análisis, porque el modelo de suscripción por hashtags permite escuchar palabras clave específicas (hashtags) y desarrollar el análisis de sentimiento de la fuente.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Escenario: Análisis de sentimiento de redes sociales en tiempo real

Una empresa que tiene un sitio web de medios de comunicación está interesada en obtener una ventaja sobre sus competidores ofreciendo contenido del sitio que sea inmediatamente relevante para sus lectores. La empresa usa el análisis de las redes sociales en temas relevantes para sus lectores mediante la elaboración del análisis de sentimiento en tiempo real de los datos de Twitter.

Para identificar los temas populares en tiempo real en Twitter, la empresa necesita el análisis en tiempo real del volumen de tweets y la opinión sobre los temas clave.

## <a name="prerequisites"></a>Requisitos previos

En esta guía paso a paso va a utilizar una aplicación cliente que se conecta a Twitter y busca tweets con ciertos hashtags (que se pueden establecer). Para ejecutar la aplicación y analizar los tweets mediante Azure Stream Analytics, debe tener lo siguiente:

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/).

* Una cuenta de [Twitter](https://twitter.com).

* La aplicación TwitterClientCore, que lee la fuente de Twitter. Para obtener esta aplicación, descargue [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Instale la [CLI de .NET Core](/dotnet/core/tools/?tabs=netcore2x) versión 2.1.0.

A continuación se muestra la arquitectura de la solución que va a implementar.

   ![Diagrama que muestra diferentes partes de servicios y aplicaciones que se usan para compilar la solución.](./media/stream-analytics-twitter-sentiment-analysis-trends/solution-diagram.png "Diagrama de la solución")

## <a name="create-an-event-hub-for-streaming-input"></a>Creación de un centro de eventos para la entrada de streaming

La aplicación de ejemplo genera eventos y los inserta en un centro de eventos de Azure. Azure Event Hubs es el método preferido de ingesta de eventos para Stream Analytics. Para más información, consulte la [documentación de Azure Event Hubs](../event-hubs/event-hubs-about.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Creación de un espacio de nombres del centro de eventos y un centro de eventos
En esta sección, se crea un espacio de nombres del centro de eventos y, luego, se agrega un centro de eventos al espacio de nombres. Los espacios de nombres del centro de eventos se usan para agrupar lógicamente instancias de bus de eventos relacionados. 

1. Inicie sesión en Azure Portal y seleccione **Crear un recurso**. Luego, busque **Event Hubs** y seleccione **Crear**.

2. En la página **Crear espacio de nombres**, escriba el nombre del espacio de nombres. Puede utilizar cualquier nombre, pero debe ser válido para una dirección URL y ser único en Azure. 
    
3. Seleccione un plan de tarifa y una suscripción, y cree o elija un grupo de recursos. Luego, elija una ubicación y seleccione **Crear**. 
 
4. Cuando haya finalizado la implementación del espacio de nombres, vaya al grupo de recursos y busque el espacio de nombres del centro de eventos en la lista de recursos de Azure. 

5. En el nuevo espacio de nombres, seleccione **+&nbsp;Centro de eventos**. 

6. Asigne al nuevo centro de eventos el nombre *socialtwitter-eh*. Puede usar otro nombre. Si lo hace, tome nota de él, porque más adelante se necesita el nombre. No es preciso establecer otras opciones del centro de eventos.
 
7. Seleccione **Crear**.

### <a name="grant-access-to-the-event-hub"></a>Concesión de acceso al centro de eventos

Para que un proceso pueda enviar datos a un centro de eventos, este necesita una directiva que permita el acceso. La directiva de acceso genera una cadena de conexión que incluye la información de autorización.

1.  En la barra de navegación del lado izquierdo del espacio de nombres de Event Hubs, seleccione **Event Hubs**, que se encuentra en la sección **Entidades**. Luego, seleccione el centro de eventos que acaba de crear.

2.  En la barra de navegación de la izquierda, seleccione **Directivas de acceso compartido**, que se encuentra en **Configuración**.

    >[!NOTE]
    >Hay una opción Directivas de acceso compartido debajo para el espacio de nombres del centro de eventos y para el centro de eventos. Asegúrese de que usa el contexto de su centro de eventos, no el espacio de nombres del centro de eventos general.

3.  En la página de la directiva de acceso, seleccione **+ Agregar**. Luego, escriba *socialtwitter-access* en **Nombre de directiva** y active la casilla **Administrar**.
 
4.  Seleccione **Crear**.

5.  Una vez que se haya implementado la directiva, selecciónela en la lista de directivas de acceso compartido.

6.  Busque el cuadro **Cadena de conexión: clave principal** y haga clic en el botón para copiar que se encuentra junto a la cadena de conexión.
 
7.  Pegue la cadena de conexión en un editor de texto. Esta cadena de conexión se necesita en la siguiente sección, después de hacer algunas modificaciones pequeñas.

   La cadena de conexión tiene el siguiente aspecto:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Observe que la cadena de conexión contiene varios pares clave-valor, separados por punto y coma: `Endpoint`, `SharedAccessKeyName`, `SharedAccessKey` y `EntityPath`.  

   > [!NOTE]
   > Por seguridad, se han quitado partes de la cadena de conexión del ejemplo.

## <a name="configure-and-start-the-twitter-client-application"></a>Configuración e inicio de la aplicación cliente de Twitter

La aplicación cliente obtiene los eventos Tweet directamente de Twitter. Para ello, necesita permiso para llamar a las API de streaming de Twitter. Para configurar ese permiso, debe crear una aplicación de Twitter, que genera credenciales exclusivas (por ejemplo, un token de OAuth). Luego puede configurar la aplicación cliente para que use estas credenciales cuando realiza llamadas de API. 

### <a name="create-a-twitter-application"></a>Creación de una aplicación de Twitter
Si no dispone de una aplicación de Twitter que pueda usar para esta guía paso a paso, puede crear una. Ya debe tener una cuenta de Twitter.

> [!NOTE]
> Puede que el proceso exacto en Twitter para crear una aplicación y obtener claves, secretos y tokens cambie. Si estas instrucciones no coinciden con lo que se ve en el sitio de Twitter, consulte la documentación para desarrolladores de Twitter.

1. En un explorador web, vaya a [Twitter For Developers](https://developer.twitter.com/en/apps), cree una cuenta de desarrollador y seleccione **Create an app** (Crear una aplicación). Es posible que vea un mensaje en el que se le indique que ha de solicitar una cuenta de desarrollador de Twitter. Hágalo y, cuando se haya aprobado la solicitud, verá un correo electrónico de confirmación. Tenga en cuenta que una cuenta de desarrollador puede tardar varios días en aprobarse.

   ![Captura de pantalla que muestra el botón Crear una aplicación.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Detalles de la aplicación Twitter")

2. En la página **Create an application** (Crear una aplicación), proporcione los detalles de la nueva aplicación y luego seleccione **Create your Twitter application** (Crear su aplicación de Twitter).

   ![Captura de pantalla que muestra el panel Detalles de la aplicación en el que puede escribir valores para la aplicación.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Detalles de la aplicación Twitter")

3. En la página de la aplicación, seleccione la pestaña **Keys and Tokens** (Claves y tokens de acceso) y copie los valores de **Consumer API Key** (Clave de API de consumidor) y **Consumer API Secret Key** (Clave secreta de API de consumidor). Además, seleccione **Create** (Crear) en **Access Token and Access Token Secret** (Token de acceso y secreto de token de acceso) para generar los tokens de acceso. Copie los valores de **Access Token** (Token de acceso) y **Access Token Secret** (Secreto del token de acceso).

   Guarde los valores que ha recuperado de la aplicación de Twitter. Los necesitará más adelante.

> [!NOTE]
> Las claves y los secretos de la aplicación de Twitter proporcionan acceso a su cuenta de Twitter. Considere esta información como confidencial, al igual que la contraseña de Twitter. Por ejemplo, no inserte esta información en una aplicación que proporcione a otros usuarios. 

### <a name="configure-the-client-application"></a>Configuración de la aplicación cliente

Hemos creado una aplicación cliente que se conecta a los datos de Twitter mediante las [API de streaming de Twitter](https://dev.twitter.com/streaming/overview) para recopilar eventos de tweets sobre un conjunto de temas específicos.

Antes de que se ejecute la aplicación, requiere cierta información del usuario, como las claves de Twitter y la cadena de conexión del centro de eventos.

1. Asegúrese de que ha descargado la aplicación [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore), tal y como se muestra en los requisitos previos.

2. Use un editor de texto para abrir el archivo *App.config*. Realice los siguientes cambios en el elemento `<appSettings>`:

   * Establezca `oauth_consumer_key` en la clave de consumidor de Twitter (clave de API). 
   * Establezca `oauth_consumer_secret` en el secreto de consumidor de Twitter (clave de secreto de API).
   * Establezca `oauth_token` en el token de acceso de Twitter.
   * Establezca `oauth_token_secret` en el secreto del token de acceso de Twitter.
   * Establezca `EventHubNameConnectionString` en la cadena de conexión.
   * Establezca `EventHubName` en el nombre del centro de eventos (es decir, el valor de la ruta de acceso de la entidad).

3. Abra la línea de comandos y vaya al directorio en el que se encuentre la aplicación TwitterClientCore. Use el comando `dotnet build` para compilar el proyecto. Luego, use el comando `dotnet run` para ejecutar la aplicación. Esta envía tweets a su centro de eventos.

## <a name="create-a-stream-analytics-job"></a>Creación de un trabajo de Stream Analytics

Ahora que los eventos Tweet se transmiten en tiempo real desde Twitter, se puede configurar un trabajo de Stream Analytics para analizar estos eventos en tiempo real.

1. En Azure Portal, vaya a su grupo de recursos y seleccione **+ Agregar**. Luego, busque **Trabajo de Stream Analytics** y seleccione **Crear**.

2. Asigne al trabajo el nombre `socialtwitter-sa-job` y especifique una suscripción, un grupo de recursos y una ubicación.

    Es conveniente colocar el trabajo y el centro de eventos en la misma región para obtener el mejor rendimiento y no pagar por transferir datos de una región a otra.

3. Seleccione **Crear**. Luego, vaya al trabajo cuando finalice la implementación.

## <a name="specify-the-job-input"></a>Especificación de la entrada del trabajo

1. En el trabajo de Stream Analytics, seleccione **Entradas** en el menú izquierdo, en **Topología de trabajo**.

2. Seleccione **+&nbsp;Agregar entrada de flujo** > **Centro de eventos**. Rellene el formulario **Nueva entrada** con la siguiente información:

   |**Configuración**  |**Valor sugerido**  |**Descripción**  |
   |---------|---------|---------|
   |Alias de entrada| *TwitterStream* | Escriba un alias para la entrada. |
   |Suscripción  | \<Your subscription\> |  Seleccione la suscripción de Azure que quiera usar. |
   |Espacio de nombres del centro de eventos | *asa-twitter-eventhub* |
   |Nombre del centro de eventos | *socialtwitter-eh* | Elija *Usar existente*. Luego, seleccione el centro de eventos que ha creado.|
   |Tipo de compresión de eventos| GZip | El tipo de compresión de datos.|

   Deje los restantes valores predeterminados y seleccione **Guardar**.

## <a name="specify-the-job-query"></a>Especificación de la consulta del trabajo

Stream Analytics admite un modelo de consulta declarativa simple que describe las transformaciones. Para más información sobre el lenguaje, consulte la [Referencia de lenguaje de consulta de Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference). Esta guía paso a paso le ayudará a crear y probar varias consultas sobre datos de Twitter.

Para comparar el número de menciones entre temas, se usa una [ventana de saltos de tamaño constante](/stream-analytics-query/tumbling-window-azure-stream-analytics) para obtener el recuento de menciones por tema cada cinco segundos.

1. En la **Información general** del trabajo, seleccione **Editar consulta**, cerca de la parte superior derecha del cuadro Consulta. Azure enumera las entradas y salidas que están configuradas para el trabajo, y permite crear una consulta para transformar el flujo de entrada a medida que se envía a la salida.

2. En el editor de código, cambie la consulta por lo siguiente:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Los datos de eventos de los mensajes deberían aparecer en la ventana **Vista previa de entrada**, debajo de la consulta. Asegúrese de que en **Vista** se ha seleccionado **JSON**. Si no ve datos, asegúrese de que el generador de datos está enviando eventos al centro de eventos y que ha seleccionado **GZip** como tipo de compresión para la entrada.

4. Seleccione **Consulta de prueba** y fíjese en los resultados que aparecen en la ventana **Resultados de prueba**, que está debajo de la consulta.

5. En el editor de código, cambie la consulta por la siguiente y seleccione **Consulta de prueba**:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Esta consulta devuelve todos los tweets que incluyen la palabra clave *Azure*.

## <a name="create-an-output-sink"></a>Creación de un receptor de salida

Se ha definido un flujo de eventos, una entrada de centro de eventos para la ingesta de eventos y una consulta para realizar una transformación en el flujo de datos. El último paso consiste en definir un receptor de salida para el trabajo.  

En esta guía paso a paso va a escribir los eventos Tweet agregados de nuestra consulta de trabajo en Azure Blob Storage.  También puede insertar los resultados en Azure SQL Database, Azure Table Storage, Event Hubs o Power BI, según las necesidades específicas de su aplicación.

## <a name="specify-the-job-output"></a>Especificación de la salida del trabajo

1. En la sección **Topología de trabajo** del menú de navegación de la izquierda, seleccione **Salidas**. 

2. En la página **Salidas**, haga clic en **+&nbsp;Agregar** y **Blob Storage/Data Lake Storage Gen2**:

   * **Alias de salida**: Use el nombre `TwitterStream-Output`. 
   * **Opciones de importación**: Seleccione **Seleccionar almacenamiento de las suscripciones**.
   * **Cuenta de almacenamiento**. Seleccione la cuenta de almacenamiento.
   * **Contenedor**. Seleccione **Crear nuevo** y escriba `socialtwitter`.
   
4. Seleccione **Guardar**.   

## <a name="start-the-job"></a>Inicio del trabajo

Se especifican una entrada de trabajo, la consulta y la salida. Está listo para iniciar el trabajo de Stream Analytics.

1. Asegúrese de que se está ejecutando la aplicación TwitterClientCore. 

2. En la información general del trabajo, seleccione **Iniciar**.

3. En la página **Iniciar trabajo**, en **Hora de inicio de la salida del trabajo**, seleccione **Ahora** y, después, **Iniciar**.

## <a name="get-support"></a>Obtención de soporte técnico
Para más ayuda, pruebe nuestra [Página de preguntas y respuestas de Microsoft sobre Azure Stream Analytics](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Pasos siguientes
* [Introducción a Azure Stream Analytics](stream-analytics-introduction.md)
* [Introducción al uso de Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Escalación de trabajos de Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Referencia del lenguaje de consulta de Azure Stream Analytics](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referencia de API de REST de administración de Azure Stream Analytics](/rest/api/streamanalytics/)
