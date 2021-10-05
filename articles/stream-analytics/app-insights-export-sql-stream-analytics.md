---
title: Exportación a SQL desde Azure Application Insights | Microsoft Docs
description: Exportación continua de datos de Application Insights a mediante el Stream Analytics
ms.topic: conceptual
ms.date: 09/11/2017
ms.service: stream-analytics
ms.openlocfilehash: bcf3bc5995a6d9c8c37ab92e303b7d39b0b132fe
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594622"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Tutorial: exportación a SQL desde Application Insights mediante Stream Analytics
En este artículo se muestra cómo trasladar los datos de telemetría desde [Azure Application Insights][start] a Azure SQL Database mediante la [Exportación continua][export] y [Azure Stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

La Exportación continua traslada los datos de telemetría a Azure Storage en formato JSON. Analizaremos los objetos JSON mediante Azure Stream Analytics y crearemos filas en una tabla de base de datos.

(De manera más general, la Exportación continua es la forma de realizar su propio análisis de la telemetría que las aplicaciones envían a Application Insights. Se puede adaptar este ejemplo de código para realizar otras operaciones con la telemetría exportada, como la adición de datos).

Comenzaremos con la suposición de que ya dispone de la aplicación que desea supervisar.

En este ejemplo, vamos a usar los datos de la vista de página, pero se puede ampliar fácilmente el mismo patrón a otros tipos de datos, como eventos y excepciones personalizados. 

> [!IMPORTANT]
> La exportación continua está en desuso y solo se admite para los recursos clásicos de Application Insights. [Migre a un recurso de Application Insights basado en áreas de trabajo](../azure-monitor/app/convert-classic-resource.md) para usar la [configuración de diagnóstico](../azure-monitor/app/export-telemetry.md#diagnostic-settings-based-export) para la exportación de telemetría.

## <a name="add-application-insights-to-your-application"></a>Agregar Application Insights a una aplicación 
Primeros pasos:

1. [Configure Application Insights para sus páginas web](../azure-monitor/app/javascript.md). 
   
    (En este ejemplo, nos centraremos en el procesamiento de datos de vistas de página de los exploradores cliente, pero también puede configurar Application Insights para el lado servidor de una aplicación [Java](../azure-monitor/app/java-in-process-agent.md) o [ASP.NET](../azure-monitor/app/asp-net.md) y procesar peticiones, dependencias y otra telemetría de servidor).
2. Publique la aplicación y vea los datos de telemetría que aparecen en el recurso de Application Insights.

## <a name="create-storage-in-azure"></a>Creación de almacenamiento en Azure
La exportación continua siempre envía los datos a una cuenta de Azure Storage, por lo que necesitará crear primero el almacenamiento.

1. Cree una cuenta de almacenamiento en su suscripción en el [portal de Azure][portal].
   
    ![Captura de pantalla de Azure Portal; elija Nuevo, Datos, Almacenamiento y, a continuación, seleccione Clásico, Crear y proporcione un nombre para la cuenta de almacenamiento.](./media/app-insights-export-sql-stream-analytics/040-store.png)
2. Creación de un contenedor
   
    ![Captura de pantalla del nuevo almacenamiento, seleccione Contenedores y, después, el icono Contenedores y Agregar.](./media/app-insights-export-sql-stream-analytics/050-container.png)
3. Copie la clave de acceso de almacenamiento.
   
    La necesitará en seguida para configurar la entrada para el servicio de análisis de transmisiones.
   
    ![ Captura de pantalla del almacenamiento, abra Configuración, Claves y realice una copia de la clave de acceso principal.](./media/app-insights-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Inicio de la exportación continua al almacenamiento de Azure
1. En el portal de Azure, busque el recurso de Application Insights que ha creado para la aplicación.
   
    ![Captura de pantalla de Azure Portal; elija Examinar, Application Insights y su aplicación.](./media/app-insights-export-sql-stream-analytics/060-browse.png)
2. Cree una exportación continua.
   
    ![Captura de pantalla del recurso de Application Insights; elija Configuración, Exportación continua y Agregar.](./media/app-insights-export-sql-stream-analytics/070-export.png)

    Seleccione la cuenta de almacenamiento que creó anteriormente:

    ![Captura de pantalla de exportación continua y, a continuación, seleccione Agregar.](./media/app-insights-export-sql-stream-analytics/080-add.png)

    Configure los tipos de eventos que desea ver:

    ![Captura de pantalla de la adición de la exportación continua; a continuación, elija los tipos de eventos.](./media/app-insights-export-sql-stream-analytics/085-types.png)


1. Permita que se acumulen algunos datos. Póngase cómo y deje que los usuarios usen su aplicación durante un tiempo. Así, aparecerá la telemetría y verá gráficos estadísticos en el [explorador de métricas](../azure-monitor/essentials/metrics-charts.md) y eventos individuales en la [búsqueda de diagnóstico](../azure-monitor/app/diagnostic-search.md). 
   
    Y, además, exportará los datos en el almacenamiento. 
2. Inspeccione los datos exportados, ya sea en el portal (seleccione **Examinar**, seleccione la cuenta de almacenamiento y, después, **Contenedores**) o bien en Visual Studio. En Visual Studio, elija **Ver/Cloud Explorer** y abra Azure/Almacenamiento. (Si no tiene esta opción de menú, deberá instalar Azure SDK: abra el cuadro de diálogo Nuevo proyecto y Visual C#/Nube/Obtener Microsoft Azure SDK para. NET).
   
    ![Captura de pantalla de Visual Studio; abra el Explorador de servidores, Azure, Storage.](./media/app-insights-export-sql-stream-analytics/087-explorer.png)
   
    Tome nota de la parte común del nombre de la ruta de acceso, que se deriva del nombre de la aplicación y de la clave de instrumentación. 

Los eventos se escriben en archivos de blob en formato JSON. Cada archivo puede contener uno o varios eventos. Así, es probable que queramos leer los datos de eventos y filtrar por los campos que deseemos. Se pueden realizar multitud de acciones con los datos, pero nuestro plan de hoy consiste en usar Stream Analytics para trasladar los datos a SQL Database. De este modo, será más sencillo ejecutar muchas consultas interesantes.

## <a name="create-an-azure-sql-database"></a>Creación de una base de datos de Azure SQL
De nuevo, empiece desde su suscripción en [Azure Portal][portal], cree la base de datos (y un servidor, a menos que ya tenga uno) donde escribirá los datos.

![Captura de pantalla de Azure Portal, Crear, Datos y almacenamiento y SQL Database.](./media/app-insights-export-sql-stream-analytics/090-sql.png)

Asegúrese de que el servidor permite el acceso a los servicios de Azure:

![Captura de pantalla de servidores SQL en Azure Portal y configuración del firewall.](./media/app-insights-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Creación de una tabla en Azure SQL Database
Conéctese a la base de datos creada en la sección anterior con su herramienta preferida de administración. En este tutorial, usaremos [Herramientas de administración de SQL Server](/sql/ssms/sql-server-management-studio-ssms) (SSMS).

![Captura de pantalla de la conexión a Azure SQL Database.](./media/app-insights-export-sql-stream-analytics/31-sql-table.png)

Cree una nueva consulta y ejecute la siguiente instrucción T-SQL:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![Captura de pantalla de la creación de PageViewsTable en SQL Server Management Studio.](./media/app-insights-export-sql-stream-analytics/34-create-table.png)

En este ejemplo, usamos datos de vistas de página. Para ver los demás datos disponibles, observe el resultado de JSON y consulte el [modelo de exportación de datos](../azure-monitor/app/export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Creación de una instancia de Azure Stream Analytics
Desde [Azure Portal](https://portal.azure.com/), seleccione el servicio Azure Stream Analytics y cree un nuevo trabajo de Stream Analytics:

![Captura de pantalla que muestra la página del trabajo de Stream Analytics con el botón Crear resaltado.](./media/app-insights-export-sql-stream-analytics/001.png)

![Captura de pantalla de Nuevo trabajo de Stream Analytics](./media/app-insights-export-sql-stream-analytics/002.png)

Cuando se cree el nuevo trabajo, seleccione **Ir al recurso**.

![Captura de pantalla que muestra el mensaje Implementación correcta y el botón Ir al recurso.](./media/app-insights-export-sql-stream-analytics/003.png)

#### <a name="add-a-new-input"></a>Incorporación de una nueva entrada

![Captura de pantalla que muestra la página Entradas con el botón Agregar seleccionado.](./media/app-insights-export-sql-stream-analytics/004.png)

Configúrelo de manera que tome los datos del blob de Exportación continua:

![Captura de pantalla que muestra la ventana Nueva entrada con las opciones del menú desplegable Alias de entrada, Origen y Cuenta de almacenamiento seleccionadas.](./media/app-insights-export-sql-stream-analytics/0005.png)

Ahora, necesitará la clave de acceso principal de la cuenta de almacenamiento, que anotó anteriormente. Establézcala como la clave de cuenta de almacenamiento.

#### <a name="set-path-prefix-pattern"></a>Establecimiento del patrón del prefijo de la ruta de acceso

**Asegúrese de establecer el formato de fecha como AAAA-MM-DD (con guiones).**

El patrón del prefijo de la ruta de acceso especifica cómo busca el Stream Analytics los archivos de entrada en el almacenamiento. Deberá establecerlo para que coincida con el modo en que la Exportación continua almacena los datos. Configúrelo como este caso que se muestra a continuación:

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

En este ejemplo:

* `webapplication27` es el nombre del recurso de Application Insights, **todo en minúsculas**. 
* `1234...` es la clave de instrumentación del recurso de Application Insights **con los guiones quitados**. 
* `PageViews` es el tipo de datos que se van a analizar. Los tipos disponibles dependen del filtro definido en la Exportación continua. Examine los datos exportados para ver los demás tipos disponibles y vea el [modelo de exportación de datos](../azure-monitor/app/export-data-model.md).
* `/{date}/{time}` es un patrón escrito literalmente.

Para obtener el nombre y el valor iKey del recurso de Application Insights, abra Essentials en su página de información general o bien abra la Configuración.

> [!TIP]
> Use la función Sample para comprobar que estableció correctamente la ruta de acceso de entrada. Si se produce un error: compruebe que hay datos en el almacenamiento para el intervalo de tiempo de muestreo que eligió. Modifique la definición de entrada y compruebe que establece correctamente la cuenta de almacenamiento, el prefijo de ruta de acceso y el formato de fecha.

 
## <a name="set-query"></a>Establecimiento de la consulta
Abra la sección de consulta:

Reemplace la consulta predeterminada por lo siguiente:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

Observe que las primeras propiedades son específicas de los datos de la vista de página. Las exportaciones de otros tipos de telemetría tendrán diferentes propiedades. Vea la [referencia detallada del modelo de datos para los tipos y valores de propiedad](../azure-monitor/app/export-data-model.md)

## <a name="set-up-output-to-database"></a>Configuración de la salida a la base de datos
Seleccione SQL como salida.

![Captura de pantalla de Stream Analytics con salidas seleccionadas.](./media/app-insights-export-sql-stream-analytics/006.png)

Especifique la base de datos.

![Captura de pantalla de la nueva salida con detalles de la base de datos.](./media/app-insights-export-sql-stream-analytics/007.png)

Cierre el asistente y espere una notificación que indica que se ha configurado la salida.

## <a name="start-processing"></a>Inicio del procesamiento
Inicie el trabajo desde la barra de acción:

![Captura de pantalla de Stream Analytics con Inicio seleccionado.](./media/app-insights-export-sql-stream-analytics/008.png)

Puede elegir si desea iniciar el procesamiento de los datos a partir de ahora o bien empezar con datos anteriores. Lo segundo resulta útil si la Exportación continua ya se ha estado ejecutando durante un tiempo.

Después de unos minutos, vuelva a las herramientas de administración de SQL Server y consulte los datos que están entrando. Por ejemplo, utilice una consulta como esta:

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="next-steps"></a>Pasos siguientes
* [Exportación a PowerBI mediante Stream Analytics](../azure-monitor/app/export-power-bi.md)
* [Referencia detallada del modelo de datos para los tipos y valores de propiedad](../azure-monitor/app/export-data-model.md)
* [Exportación continua en Application Insights](../azure-monitor/app/export-telemetry.md)

<!--Link references-->

[diagnostic]: ../azure-monitor/app/diagnostic-search.md
[export]: ../azure-monitor/app/export-telemetry.md
[metrics]: ../azure-monitor/essentials/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ../azure-monitor/app/app-insights-overview.md

