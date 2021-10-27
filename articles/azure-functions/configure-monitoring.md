---
title: Configuración de la supervisión para Azure Functions
description: Aprenda a conectar la aplicación de funciones a Application Insights para su supervisión y a configurar la recopilación de datos.
ms.date: 8/31/2020
ms.topic: how-to
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3afe10184ba2b3f0eba02111b98b31e86b26e075
ms.sourcegitcommit: 611b35ce0f667913105ab82b23aab05a67e89fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2021
ms.locfileid: "130006548"
---
# <a name="how-to-configure-monitoring-for-azure-functions"></a>Configuración de la supervisión para Azure Functions

Azure Functions se integra con Application Insights para permitirle supervisar mejor las aplicaciones de funciones. Application Insights, una característica de Azure Monitor, es un servicio de Application Performance Management (APM) extensible que recopila los datos generados por la aplicación de funciones, incluida la información que la aplicación escribe en los registros. La integración de Application Insights se suele habilitar al crear la aplicación de funciones. Si la aplicación no tiene establecida la clave de instrumentación, antes debe [habilitar la integración de Application Insights](#enable-application-insights-integration). 

Puede usar Application Insights sin ninguna configuración personalizada. La configuración predeterminada puede dar lugar a grandes volúmenes de datos. Si usa una suscripción de Visual Studio Azure, es posible que alcance su límite de datos para Application Insights. Para obtener más información sobre los costos de Application Insights, consulte [Administración del uso y los costos de Application Insights](../azure-monitor/app/pricing.md). Para más información, consulte [Soluciones con un gran volumen de telemetría](#solutions-with-high-volume-of-telemetry).

Más adelante en este artículo verá cómo configurar y personalizar los datos que envían las funciones a Application Insights. Para una aplicación de función, el registro se configura en el archivo [host.json]. 

> [!NOTE]
> Puede usar una configuración de la aplicación especialmente creada para representar valores específicos en un archivo host.json para un entorno específico. Esto le permitirá, de hecho, cambiar la configuración de host.json sin tener que volver a publicar este archivo en el proyecto. Para más información, consulte [Invalidación de valores de host.json](functions-host-json.md#override-hostjson-values).

## <a name="configure-categories"></a>Configuración de categorías

El registrador de Azure Functions incluye una *categoría* para cada registro. La categoría indica qué parte del código de tiempo de ejecución o del código de la función escribió el registro. Las categorías difieren entre la versión 1. x y las versiones posteriores. En el siguiente gráfico se describen las categorías principales de registros que crea el runtime. 

# <a name="v2x"></a>[v2.x y posteriores](#tab/v2)

| Category | Tabla | Descripción |
| ----- | ----- | ----- |
| **`Function.<YOUR_FUNCTION_NAME>`** | **dependencies**| Los datos de dependencia se recopilan automáticamente para algunos servicios. En el caso de ejecuciones correctas, estos registros son de nivel `Information`. Para obtener más información, consulte [Dependencias](functions-monitoring.md#dependencies). Las excepciones se registran en el nivel `Error`. El entorno de ejecución también crea registros de nivel `Warning`; por ejemplo, cuando los mensajes en cola se envían a la [cola de dudosos](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>`** | **customMetrics**<br/>**customEvents** | Los SDK de C# y JavaScript permiten recopilar métricas personalizadas y registrar eventos personalizados. Para más información, consulte [Datos de telemetría personalizados](functions-monitoring.md#custom-telemetry-data).|
| **`Function.<YOUR_FUNCTION_NAME>`** | **traces**| Incluye registros de funciones iniciadas y completadas para ejecuciones de función específicas. En el caso de ejecuciones correctas, estos registros son de nivel `Information`. Las excepciones se registran en el nivel `Error`. El entorno de ejecución también crea registros de nivel `Warning`; por ejemplo, cuando los mensajes en cola se envían a la [cola de dudosos](functions-bindings-storage-queue-trigger.md#poison-messages). | 
| **`Function.<YOUR_FUNCTION_NAME>.User`** | **traces**| Registros generados por el usuario, que pueden integrarse en cualquier nivel de registro. Para obtener más información sobre cómo escribir en los registros desde las funciones, consulte [Escritura en los registros](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Estos registros generados en tiempo de ejecución proporcionan los recuentos y promedios de las invocaciones de función en un período de tiempo [configurable](#configure-the-aggregator). El período predeterminado es 30 segundos o 1000 resultados, lo que ocurra primero. Algunos ejemplos son el número de ejecuciones, la tasa de éxito y la duración. Todos estos registros se escriben en el nivel `Information`. Si filtra por `Warning` o superior, no verá ninguno de estos datos. |
| **`Host.Results`** | **requests** | Estos registros generados en tiempo de ejecución indican si una función se ha realizado correctamente o no. Todos estos registros se escriben en el nivel `Information`. Si filtra por `Warning` o superior, no verá ninguno de estos datos. |
| **`Microsoft`** | **traces** | Categoría de registro completa que refleja un componente de .NET en tiempo de ejecución invocado por el host.  |
| **`Worker`** | **traces** | Registros generados por el proceso de trabajo de los lenguajes que no son .NET. Estos registros también se pueden crear en una categoría `Microsoft.*`, como `Microsoft.Azure.WebJobs.Script.Workers.Rpc.RpcFunctionInvocationDispatcher`. Se escriben en el nivel `Information`.|

> [!NOTE]
> En el caso de las funciones de la biblioteca de clases de .NET, estas categorías suponen que usa `ILogger` y no `ILogger<T>`. Para más información, consulte la [documentación de ILogger de Functions](functions-dotnet-class-library.md#ilogger). 

# <a name="v1x"></a>[v1.x](#tab/v1)

| Category | Tabla | Descripción |
| ----- | ----- | ----- |
| **`Function`** | **traces**| Registros generados por el usuario, que pueden integrarse en cualquier nivel de registro. Para obtener más información sobre cómo escribir en los registros desde las funciones, consulte [Escritura en los registros](functions-monitoring.md#writing-to-logs). | 
| **`Host.Aggregator`** | **customMetrics** | Estos registros generados en tiempo de ejecución proporcionan los recuentos y promedios de las invocaciones de función en un período de tiempo [configurable](#configure-the-aggregator). El período predeterminado es 30 segundos o 1000 resultados, lo que ocurra primero. Algunos ejemplos son el número de ejecuciones, la tasa de éxito y la duración. Todos estos registros se escriben en el nivel `Information`. Si filtra por `Warning` o superior, no verá ninguno de estos datos. |
| **`Host.Executor`** | **traces** | Incluye registros de **función iniciada** y **función completada** para ejecuciones de función específicas. Para las ejecuciones correctas, estos registros son de nivel `Information`. Las excepciones se registran en el nivel `Error`. El entorno de ejecución también crea registros de nivel `Warning`; por ejemplo, cuando los mensajes en cola se envían a la [cola de dudosos](functions-bindings-storage-queue-trigger.md#poison-messages).  |
| **`Host.Results`** | **requests** | Estos registros generados en tiempo de ejecución indican si una función se ha realizado correctamente o no. Todos estos registros se escriben en el nivel `Information`. Si filtra por `Warning` o superior, no verá ninguno de estos datos. |

---

En la columna **Tabla** se indica en qué tabla de Application Insights se escribe el registro. 

## <a name="configure-log-levels"></a>Configuración de los niveles de registro

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

Para cada categoría, debe indicar el nivel de registro mínimo para enviar. La configuración de host.json varía en función de la [versión del entorno de ejecución de Functions](functions-versions.md). 

En el ejemplo siguiente se define el registro en función de las siguientes reglas:

+ En el caso de los registros de las categorías `Host.Results` o `Function`, solo se deben registrar los eventos de nivel `Error` o de niveles superiores. 
+ En el caso de los registros de la categoría `Host.Aggregator`, se deben registrar todas las métricas generadas (`Trace`).
+ En el resto de los registros, incluidos los registros de usuario, solo se deben registrar los eventos de nivel `Information` y de niveles superiores.

# <a name="v2x"></a>[v2.x y posteriores](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host.Results": "Error",
      "Function": "Error",
      "Host.Aggregator": "Trace"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host.Results": "Error",
        "Function": "Error",
        "Host.Aggregator": "Trace"
      }
    }
  }
}
```

---

Si [host.json] incluye varios registros que comienzan con la misma cadena, los más definidos se asignan primero. Considere el ejemplo siguiente que registra todo en el entorno de ejecución, excepto la categoría `Host.Aggregator`, en el nivel `Error`:

# <a name="v2x"></a>[v2.x y posteriores](#tab/v2)

```json
{
  "logging": {
    "fileLoggingMode": "always",
    "logLevel": {
      "default": "Information",
      "Host": "Error",
      "Function": "Error",
      "Host.Aggregator": "Information"
    }
  }
}
```

# <a name="v1x"></a>[v1.x](#tab/v1) 

```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Information",
      "categoryLevels": {
        "Host": "Error",
        "Function": "Error",
        "Host.Aggregator": "Information"
      }
    }
  }
}
```

---

Puede usar un valor de nivel de registro de `None` para evitar que se escriban registros en una categoría. 

> [!CAUTION]
> Azure Functions se integra con Application Insights mediante el almacenamiento de eventos de telemetría en tablas de Application Insights; el establecimiento de un nivel de registro de categoría en cualquier valor diferente de `Information` impedirá que la telemetría fluya a esas tablas; como resultado, no podrá ver los datos relacionados en Application Insights o en la pestaña Supervisión de la función.
>
> A partir de los ejemplos anteriores:
> * Si la categoría `Host.Results` se establece en el nivel de registro `Error`, solo se recopilarán los eventos de telemetría de ejecución de host en la tabla `requests` correspondientes a las ejecuciones de funciones con error, lo que impide mostrar los detalles de ejecución del host de las ejecuciones correctas en Application Insights y en la pestaña Supervisión de la función.
> * Si la categoría `Function` se establece en el nivel de registro `Error`, se dejarán de recopilar datos de telemetría de funciones relacionados con `dependencies`, `customMetrics` y `customEvents` para todas las funciones, lo que impide ver cualquiera de estos datos en Application Insights. Solo se recopilarán los objetos `traces` registrados con el nivel `Error`. 
>
> En ambos casos, seguirá recopilando datos de errores y excepciones en Application Insights y en la pestaña Supervisión de la función. Para más información, consulte [Soluciones con gran volumen de telemetría](#solutions-with-high-volume-of-telemetry).


## <a name="configure-the-aggregator"></a>Configurar el agregador

Como se indicó en la sección anterior, el tiempo de ejecución agrega datos acerca de las ejecuciones de la función durante un período de tiempo. El período predeterminado es 30 segundos o 1000 ejecuciones, lo que ocurra primero. Puede configurar este valor en el archivo [host.json].  Este es un ejemplo:

```json
{
    "aggregator": {
      "batchSize": 1000,
      "flushTimeout": "00:00:30"
    }
}
```

## <a name="configure-sampling"></a>Configurar el muestreo

Application Insights tiene una característica de [muestreo](../azure-monitor/app/sampling.md) que le puede ayudar a impedir que se recopilen demasiados datos de telemetría sobre las ejecuciones completadas en los momentos de picos de carga. Cuando tasa de ejecuciones entrantes supera un umbral especificado, Application Insights empieza a omitir aleatoriamente algunas de las ejecuciones entrantes. La configuración predeterminada para el número máximo de ejecuciones por segundo es de 20 (cinco en la versión 1.x). Puede configurar el muestreo en [host.json](./functions-host-json.md#applicationinsights).  Este es un ejemplo:

# <a name="v2x"></a>[v2.x y posteriores](#tab/v2)

```json
{
  "logging": {
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond" : 20,
        "excludedTypes": "Request;Exception"
      }
    }
  }
}
```


Puede excluir determinados tipos de datos de telemetría del muestreo. En este ejemplo, se excluyen del muestreo los datos de tipo `Request` y `Exception`. Esto garantiza que se registran *todas* las ejecuciones (solicitudes) y excepciones de las funciones, en tanto que otros tipos de datos de telemetría siguen sujetos al muestreo. 

# <a name="v1x"></a>[v1.x](#tab/v1)  

```json
{
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Para obtener más información, consulte [Muestreo en Application Insights](../azure-monitor/app/sampling.md).

## <a name="configure-scale-controller-logs"></a>Configuración de los registros del controlador de escala

_Esta característica se encuentra en su versión preliminar._ 

Puede hacer que el [controlador de escala de Azure Functions](./event-driven-scaling.md#runtime-scaling) emita registros en Application Insights o en Blob Storage para comprender mejor las decisiones que este controlador está tomando para la aplicación de funciones.

Para habilitar esta característica, agregue una configuración de aplicación denominada `SCALE_CONTROLLER_LOGGING_ENABLED` a los valores de la aplicación de funciones. El valor de esta configuración debe tener el formato `<DESTINATION>:<VERBOSITY>`, en función de lo siguiente:

[!INCLUDE [functions-scale-controller-logging](../../includes/functions-scale-controller-logging.md)]

Por ejemplo, el siguiente comando de la CLI de Azure activa el registro detallado del controlador de escala para Application Insights:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:Verbose
```

En este ejemplo, reemplace `<FUNCTION_APP_NAME>` y `<RESOURCE_GROUP_NAME>` por el nombre de la aplicación de funciones y el nombre del grupo de recursos, respectivamente. 

El siguiente comando de la CLI de Azure deshabilita el registro estableciendo el nivel de detalle en `None`:

```azurecli-interactive
az functionapp config appsettings set --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--settings SCALE_CONTROLLER_LOGGING_ENABLED=AppInsights:None
```

También puede deshabilitar el registro si quita la configuración de `SCALE_CONTROLLER_LOGGING_ENABLED` con el siguiente comando de la CLI de Azure:

```azurecli-interactive
az functionapp config appsettings delete --name <FUNCTION_APP_NAME> \
--resource-group <RESOURCE_GROUP_NAME> \
--setting-names SCALE_CONTROLLER_LOGGING_ENABLED
```

Con el registro del controlador de escala habilitado, ahora puede [consultar los registros del controlador de escala](analyze-telemetry-data.md#query-scale-controller-logs). 

## <a name="enable-application-insights-integration"></a>Habilitación de la integración de Application Insights

Para que una aplicación de función envíe datos a Application Insights tiene que conocer la clave de instrumentación de un recurso de Application Insights. La clave tiene que estar en un valor de configuración de la aplicación llamado **APPINSIGHTS_INSTRUMENTATIONKEY**.

Al crear la función de aplicaciones [en Azure Portal](./functions-get-started.md), desde la línea de comandos mediante [Azure Functions Core Tools](./create-first-function-cli-csharp.md) o [Visual Studio Code](./create-first-function-vs-code-csharp.md), la integración de Application Insights está habilitada de forma predeterminada. El recurso de Application Insights tiene el mismo nombre que la aplicación de función y se crea en la misma región o en la región más cercana.

### <a name="new-function-app-in-the-portal"></a>Nueva aplicación de función en el portal

Para revisar el recurso de Application Insights que se está creando, selecciónelo para expandir la ventana **Application Insights**. Puede cambiar el valor de **Nuevo nombre de recurso**  o elegir otro valor en **Ubicación** en la [ubicación geográfica de Azure](https://azure.microsoft.com/global-infrastructure/geographies/) donde quiera almacenar los datos.

![Habilitar Application Insights al crear una aplicación de función](media/functions-monitoring/enable-ai-new-function-app.png)

Cuando elige **Crear**, se crea un recurso de Application Insights con la aplicación de función, que tiene `APPINSIGHTS_INSTRUMENTATIONKEY` establecido en la configuración de la aplicación. Todo está listo para funcionar.

<a id="manually-connect-an-app-insights-resource"></a>
### <a name="add-to-an-existing-function-app"></a>Adición a una aplicación de función existente 

Si no se creó ningún recurso de Application Insights con la aplicación de funciones, siga los pasos que se describen a continuación para crearlo. A continuación, puede agregar la clave de instrumentación de ese recurso como una [configuración de la aplicación](functions-how-to-use-azure-function-app-settings.md#settings) en la aplicación de funciones.

1. En [Azure Portal](https://portal.azure.com), busque y seleccione **aplicación de funciones** y, a continuación, elija la aplicación de funciones. 

1. Seleccione el banner **Application Insights no está configurado.** en la parte superior de la ventana. Si no ve el banner, es posible que la aplicación ya tenga Application Insights habilitado.

    :::image type="content" source="media/configure-monitoring/enable-application-insights.png" alt-text="Habilitación de Application Insights desde el portal":::

1. Expanda **Cambie su recurso** y cree un recurso de Application Insights, para lo que deberá usar los valores que se especifican en la siguiente tabla.  

    | Configuración      | Valor sugerido  | Descripción                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nuevo nombre de recurso** | Nombre de aplicación único | Lo más fácil es usar el mismo nombre que usa para la aplicación de función, que debe ser único en su suscripción. | 
    | **Ubicación** | Oeste de Europa | Si es posible, use la misma [región](https://azure.microsoft.com/regions/) que la aplicación de función o una que esté cerca de esa región. |

    :::image type="content" source="media/configure-monitoring/ai-general.png" alt-text="Creación de recursos en Application Insights":::

1. Seleccione **Aplicar**. 

   El recurso de Application Insights se crea en el mismo grupo de recursos y suscripción que su aplicación de función. Después de crear el recurso, cierre la ventana Application Insights.

1. En la aplicación de funciones, seleccione **Configuración** en **Configuración** y, después, seleccione **Configuración de la aplicación**. Si ve una configuración denominada `APPINSIGHTS_INSTRUMENTATIONKEY`, significa que la integración de Application Insights está habilitada para la aplicación de función que se ejecuta en Azure. Si, por alguna razón, esta configuración no existe, agréguela utilizando la clave de instrumentación de Application Insights como valor.

> [!NOTE]
> Las primeras versiones de Functions usaban la supervisión integrada, que ya no se recomienda. Al habilitar la integración de Application Insights para una aplicación de función tal, también debe [deshabilitar el registro integrado](#disable-built-in-logging).  

## <a name="disable-built-in-logging"></a>Deshabilitar el registro integrado

Cuando habilite Application Insights, deshabilite el registro integrado que usa Azure Storage. El registro integrado es útil para realizar pruebas con cargas de trabajo ligeras, pero no está diseñado para su uso en producción de alta carga. Para la supervisión de producción, se recomienda Application Insights. Si el registro integrado se usa en producción, el registro resultante podría estar incompleto debido a la limitación de Azure Storage.

Para deshabilitar el registro integrado, elimine la configuración de la aplicación `AzureWebJobsDashboard`. Para obtener información acerca de cómo eliminar la configuración de la aplicación en Azure Portal, consulte la sección **Application settings** (Configuración de la aplicación) en [How to manage a function app](functions-how-to-use-azure-function-app-settings.md#settings) (Cómo administrar una aplicación de función). Antes de eliminar el valor de configuración de la aplicación, asegúrese de que no haya ninguna función existente en la misma aplicación de función que utilice dicho valor para los desencadenadores o enlaces de Azure Storage.

## <a name="solutions-with-high-volume-of-telemetry"></a>Soluciones con un gran volumen de telemetría 

Las aplicaciones de funciones pueden ser una parte esencial de las soluciones que, por naturaleza, provocan grandes volúmenes de telemetría (soluciones de IoT, soluciones basadas en eventos, sistemas financieros de carga alta, sistemas de integración...). En este caso, debe considerar la realización de configuración adicional para reducir los costos y mantener la observabilidad.

En función de cómo se vaya a consumir la telemetría generada, y según los paneles en tiempo real, las alertas o los diagnósticos detallados, entre otros, deberá definir una estrategia para reducir el volumen de datos generados. Esa estrategia le permitirá supervisar, operar y diagnosticar correctamente las aplicaciones de funciones en producción. Puede considerar las opciones siguientes:

* **Usar muestreo**: como se mencionó [anteriormente](#configure-sampling), ayudará a reducir drásticamente el volumen de eventos de telemetría ingeridos al tiempo que se mantiene un análisis estadísticamente correcto. Podría ocurrir que, incluso con el muestreo, obtuviera un gran volumen de telemetría. Inspeccione las opciones que le proporciona el [muestreo adaptable](../azure-monitor/app/sampling.md#configuring-adaptive-sampling-for-aspnet-applications); por ejemplo, establezca `maxTelemetryItemsPerSecond` en un valor que equilibre el volumen generado con sus necesidades de supervisión. Tenga en cuenta que el muestreo de telemetría se aplica por host que ejecuta la aplicación de funciones. 

* **Nivel de registro predeterminado**: use `Warning` o `Error` como valor predeterminado para todas las categorías de telemetría. Ahora puede decidir en qué [categorías](#configure-categories) quiere establecer `Information` para poder supervisar y diagnosticar las funciones correctamente.

* **Ajuste de la telemetría de las funciones**: con el nivel de registro predeterminado establecido en `Error` o `Warning`, no se recopilará información detallada de cada función (dependencias, métricas personalizadas, eventos personalizados y seguimientos). En el caso de aquellas funciones que son clave para la supervisión en producción, defina una entrada explícita para la categoría `Function.<YOUR_FUNCTION_NAME>` y establézcala en `Information`, para que pueda recopilar información detallada. En este punto, para evitar la recopilación de [registros generados por el usuario](functions-monitoring.md#writing-to-logs) en el nivel `Information`, establezca la categoría `Function.<YOUR_FUNCTION_NAME>.User` en el nivel de registro `Error` o `Warning`.

* **Categoría Host.Aggregator**: como se describe en [Configuración de categorías](#configure-categories), esta categoría proporciona información agregada de las invocaciones de función. La información de esta categoría se recopila en la tabla `customMetrics` de Application Insights y se muestra en la pestaña Información general de la función en Azure Portal. En función de cómo se configure el agregador, tenga en cuenta que habrá un retraso, determinado por el objeto `flushTimeout`, en la telemetría recopilada. Si establece esta categoría en otro valor distinto de `Information`, dejará de recopilar los datos de la tabla `customMetrics` y no se mostrarán las métricas en la pestaña Información general de la función.

  En la captura de pantalla siguiente se muestran los datos de telemetría de Host.Aggregator en la pestaña Información general de la función. :::image type="content" source="media/configure-monitoring/host-aggregator-function-overview.png" alt-text="Captura de pantalla de telemetría de Host.Aggregator mostrada en la pestaña Información general de la función" lightbox="media/configure-monitoring/host-aggregator-function-overview-big.png":::.

  En la captura de pantalla siguiente se muestran los datos de telemetría de Host.Aggregator en la tabla customMetrics de Application Insights.
  :::image type="content" source="media/configure-monitoring/host-aggregator-custom-metrics.png" alt-text="Captura de pantalla de la telemetría de Host.Aggregator en la tabla customMetrics de Application Insights." lightbox="media/configure-monitoring/host-aggregator-custom-metrics-big.png":::

* **Categoría Host.Results**: como se describe en [Configuración de categorías](#configure-categories), esta categoría proporciona los registros generados en tiempo de ejecución que indican el éxito o error de una invocación de función. La información de esta categoría se recopila en la tabla `requests` de Application Insights y se muestra en la pestaña Supervisión de la función y en distintos paneles de Application Insights (rendimiento, errores...). Si establece esta categoría en otro valor distinto de `Information`, solo recopilará los datos de telemetría generados en el nivel de registro definido (o superior); por ejemplo, si se establece en `error`, se realizará el seguimiento de los datos de solicitudes solo para las ejecuciones con error. 

  En la captura de pantalla siguiente se muestran los datos de telemetría de Host.Results en la pestaña Supervisión de la función. :::image type="content" source="media/configure-monitoring/host-results-function-monitor.png" alt-text="Captura de pantalla de la telemetría de Host.Results en la pestaña Supervisión de la función." lightbox="media/configure-monitoring/host-results-function-monitor-big.png":::

  En la captura de pantalla siguiente se muestran los datos de telemetría de Host.Results en el panel Rendimiento de Application Insights.
  :::image type="content" source="media/configure-monitoring/host-results-application-insights.png" alt-text="Captura de pantalla de la telemetría de Host.Results en el panel Rendimiento de Application Insights." lightbox="media/configure-monitoring/host-results-application-insights-big.png":::

* **Host.Aggregator frente a Host.Results**: ambas categorías proporcionan buena información sobre las ejecuciones de funciones; si es necesario, puede quitar la información detallada de una de estas categorías, por lo que puede usar la otra para la supervisión y las alertas.
Aquí tiene un ejemplo:
# <a name="v2x"></a>[v2.x y posteriores](#tab/v2)

``` json
{
  "version": "2.0",  
  "logging": {
    "logLevel": {
      "default": "Warning",
      "Function": "Error",
      "Host.Aggregator": "Error",
      "Host.Results": "Information", 
      "Function.Function1": "Information",
      "Function.Function1.User": "Error"
    },
    "applicationInsights": {
      "samplingSettings": {
        "isEnabled": true,
        "maxTelemetryItemsPerSecond": 1,
        "excludedTypes": "Exception"
      }
    }
  }
} 
```
# <a name="v1x"></a>[v1.x](#tab/v1) 
```json
{
  "logger": {
    "categoryFilter": {
      "defaultLevel": "Warning",
      "categoryLevels": {
        "Function": "Error",
        "Host.Aggregator": "Error",
        "Host.Results": "Information",
        "Host.Executor": "Warning"
      }
    }
  },
  "applicationInsights": {
    "sampling": {
      "isEnabled": true,
      "maxTelemetryItemsPerSecond" : 5
    }
  }
}
```
---

Con esta configuración, tendrá lo siguiente:

* El valor predeterminado de todas las funciones y categorías de telemetría se establece en `Warning` (incluidas las categorías Microsoft y Worker), por lo que, de forma predeterminada, se recopilan todos los errores y advertencias generados por el tiempo de ejecución y el registro personalizado. 

* El nivel de registro de categoría `Function` se establece en `Error`, por lo que para todas las funciones, de forma predeterminada, solo se recopilarán excepciones y registros de errores (se omitirán las dependencias y los eventos y las métricas generados por el usuario).

* En el caso de la categoría `Host.Aggregator`, como se establece en el nivel de registro `Error`, no se recopilará información agregada de las invocaciones de función en la tabla `customMetrics` de Application Insights y no se mostrará información sobre los recuentos de ejecuciones (total, correcto, error...) en el panel de información general de la función.

* Para la categoría `Host.Results`, toda la información de ejecución del host se recopila en la tabla `requests` de Application Insights. Todos los resultados de las invocaciones se mostrarán en el panel Supervisión de la función y en los paneles de Application Insights.

* Para la función llamada `Function1`, hemos establecido el nivel de registro en `Information`, por lo que, para esta función concreta, se recopila toda la telemetría (dependencias, eventos y métricas personalizados). Para la misma función, la categoría `Function1.User` (seguimientos generados por el usuario) se establece en `Error`, por lo que solo se recopilará el registro de errores personalizado. Tenga en cuenta que la configuración por función no se admite en v1.x. 

* El muestreo está configurado para enviar un elemento de telemetría por segundo por tipo, exceptuando las excepciones. Este muestreo se realizará para cada host de servidor que ejecute nuestra aplicación de funciones, por lo que si tenemos cuatro instancias, esta configuración emitirá cuatro elementos de telemetría por segundo por tipo y todas las excepciones que puedan producirse. Observe que, los recuentos de métricas, como la tasa de solicitudes y de excepciones, se ajustan para compensar la frecuencia de muestreo, de modo que exhiban valores aproximadamente correctos en el Explorador de métricas.  

> [!TIP]
> Experimente con diferentes configuraciones para asegurarse de que cubre sus requisitos de registro, supervisión y alertas. Asegúrese de que tiene diagnósticos detallados en caso de errores inesperados o funcionamiento incorrecto.

### <a name="overriding-monitoring-configuration-at-runtime"></a>Invalidación de la configuración de supervisión en tiempo de ejecución
Por último, puede haber situaciones en las que necesite cambiar rápidamente el comportamiento de registro de una determinada categoría en producción y no quiera realizar una implementación completa solo por un cambio en el archivo `host.json`. En tales casos, puede invalidar los [valores de host.json](functions-host-json.md#override-hostjson-values).


Para configurar estos valores a nivel de configuración de la aplicación (y evitar una nueva implementación solo por los cambios en host.json), debe invalidar valores específicos de `host.json` mediante la creación de un valor equivalente como configuración de la aplicación. Cuando el entorno de ejecución encuentra una configuración de aplicación en el formato `AzureFunctionsJobHost__path__to__setting`, invalida la configuración de `host.json` equivalente que se encuentra en `path.to.setting` en el archivo JSON. Cuando se expresa como una configuración de aplicación, el punto (`.`), que se utilizaba para indicar la jerarquía JSON, se reemplaza por un carácter de subrayado doble (`__`). Por ejemplo, puede usar la siguiente configuración de la aplicación para configurar los niveles de registro de funciones individuales como en `host.json` en el ejemplo anterior.


| Ruta de acceso de Host.json | Configuración de aplicación |
|----------------|-------------|
| logging.logLevel.default  | AzureFunctionsJobHost__logging__logLevel__default  |
| logging.logLeve.Host.Aggregator | AzureFunctionsJobHost__logging__logLevel__Host__Aggregator |
| logging.logLevel.Function | AzureFunctionsJobHost__logging__logLevel__Function |
| logging.logLevel.Function.Function1 | AzureFunctionsJobHost__logging__logLevel__Function1 |
| logging.logLevel.Function.Function1.User | AzureFunctionsJobHost__logging__logLevel__Function1.User |


Puede invalidar la configuración directamente en la hoja Configuración de la aplicación de funciones de Azure Portal o mediante un script de la CLI de Azure o de PowerShell.

# <a name="az-cli"></a>[az cli](#tab/v2)
```azurecli-interactive
az functionapp config appsettings set --name MyFunctionApp --resource-group MyResourceGroup --settings "AzureFunctionsJobHost__logging__logLevel__Host__Aggregator=Information"
```
# <a name="powershell"></a>[PowerShell](#tab/v1) 
```powershell
Update-AzFunctionAppSetting -Name MyAppName -ResourceGroupName MyResourceGroupName -AppSetting @{"AzureFunctionsJobHost__logging__logLevel__Host__Aggregator" = "Information"}
```
---

> [!NOTE]
> Al invalidar `host.json` mediante el cambio de la configuración de la aplicación, se reiniciará la aplicación de funciones.
 
## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de la supervisión, consulte:

+ [Monitor Azure Functions](functions-monitoring.md)
+ [Análisis de datos de telemetría de Azure Functions en Application Insights](analyze-telemetry-data.md)
+ [Application Insights](/azure/application-insights/)


[host.json]: functions-host-json.md
