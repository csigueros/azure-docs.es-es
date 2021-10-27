---
title: Habilitación de Azure Monitor OpenTelemetry para aplicaciones de .NET, Node.js y Python
description: Aquí se proporcionan instrucciones para habilitar Azure Monitor en aplicaciones mediante OpenTelemetry
ms.topic: conceptual
ms.date: 10/11/2021
author: mattmccleary
ms.author: mmcc
ms.openlocfilehash: 8f8daa67c22f8a505014ff326ca3961fa86f21f5
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130160834"
---
# <a name="enable-azure-monitor-opentelemetry-exporter-for-net-nodejs-and-python-applications-preview"></a>Habilitación de Azure Monitor OpenTelemetry Exporter para aplicaciones de .NET, Node.js y Python (versión preliminar)

En este artículo se explica cómo habilitar y configurar la oferta de versión preliminar de Azure Monitor basada en OpenTelemetry. Cuando termine de leer las instrucciones de este artículo podrá enviar seguimientos de OpenTelemetry a Azure Monitor Application Insights.

> [!IMPORTANT]
> Azure Monitor OpenTelemetry Exporter para aplicaciones de .NET, Node.js y Python se encuentra actualmente en VERSIÓN PRELIMINAR.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="limitations-of-preview-release"></a>Limitaciones de la versión preliminar

### <a name="net"></a>[.NET](#tab/net)

Medite detenidamente si esta versión preliminar es adecuada para usted. **Habilita únicamente el seguimiento distribuido** y _excluye_ lo siguiente:
 - API de métricas (métricas personalizadas, [métricas agregadas previamente)](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)
 - [Live Metrics](live-stream.md)
 - API de registro (registros de consola, bibliotecas de registro, etc.)
 - Captura automática de excepciones no controladas
 - [Generador de perfiles](profiler-overview.md)
 - [Depurador de instantáneas](snapshot-debugger.md)
 - Almacenamiento en disco sin conexión
 - [Autenticación de Azure AD](azure-ad-authentication.md)
 - [Muestreo](sampling.md)
 - Rellenado automático de Nombre del rol en la nube e Instancia de rol en la nube en entornos de Azure
 - Rellenado automático de Id. de usuario e Id. de usuario autenticado al usar el [SDK de JavaScript de Application Insights](javascript.md)
 - Rellenado automático de IP del usuario (para determinar los atributos de ubicación)
 - Capacidad de invalidar [Nombre de la operación](correlation.md#data-model-for-telemetry-correlation)
 - Capacidad de establecer manualmente Id. de usuario o Id. de usuario autenticado
 - Propagación de Nombre de la operación a Telemetría de dependencias
 - Propagación del contexto de seguimiento distribuido (bibliotecas de instrumentación) mediante Azure Functions Worker

Aquellos que requieran una experiencia con todas las características deben usar el SDK existente de [ASP.NET](asp-net.md) o [ASP.NET Core](asp-net-core.md) de Application Insights hasta que la oferta basada en OpenTelemetry madure.

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Medite detenidamente si esta versión preliminar es adecuada para usted. Habilita únicamente el seguimiento distribuido y _excluye_ lo siguiente:
 - API de métricas (métricas personalizadas, [métricas agregadas previamente)](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)
 - [Live Metrics](live-stream.md)
 - API de registro (registros de consola, bibliotecas de registro, etc.)
 - Captura automática de excepciones no controladas
 - [Autenticación de Azure AD](azure-ad-authentication.md)
 - [Muestreo](sampling.md)
 - Rellenado automático de Nombre del rol en la nube e Instancia de rol en la nube en entornos de Azure
 - Rellenado automático de Id. de usuario e Id. de usuario autenticado al usar el [SDK de JavaScript de Application Insights](javascript.md)
 - Rellenado automático de IP del usuario (para determinar los atributos de ubicación)
 - Capacidad de invalidar [Nombre de la operación](correlation.md#data-model-for-telemetry-correlation)
 - Capacidad de establecer manualmente Id. de usuario o Id. de usuario autenticado
 - Propagación de Nombre de la operación a Telemetría de dependencias

Aquellos que requieran una experiencia con todas las características deben usar el [SDK de Node.js de Application Insights](nodejs.md) existente hasta que la oferta basada en OpenTelemetry madure.

> [!WARNING] 
> En la actualidad, este exportador solo funciona en entornos de Node.js. Use el [SDK de JavaScript de Application Insights](javascript.md) en escenarios web o de explorador.

### <a name="python"></a>[Python](#tab/python)

Medite detenidamente si esta versión preliminar es adecuada para usted. **Habilita únicamente el seguimiento distribuido** y _excluye_ lo siguiente:
 - API de métricas (métricas personalizadas, [métricas agregadas previamente)](pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics)
 - [Live Metrics](live-stream.md)
 - API de registro (registros de consola, bibliotecas de registro, etc.)
 - Captura automática de excepciones no controladas
 - Almacenamiento en disco sin conexión
 - [Autenticación de Azure AD](azure-ad-authentication.md)
 - [Muestreo](sampling.md)
 - Rellenado automático de Nombre del rol en la nube e Instancia de rol en la nube en entornos de Azure
 - Rellenado automático de Id. de usuario e Id. de usuario autenticado al usar el [SDK de JavaScript de Application Insights](javascript.md)
 - Rellenado automático de IP del usuario (para determinar los atributos de ubicación)
 - Capacidad de invalidar [Nombre de la operación](correlation.md#data-model-for-telemetry-correlation)
 - Capacidad de establecer manualmente Id. de usuario o Id. de usuario autenticado
 - Propagación de Nombre de la operación a Telemetría de dependencias
 - Propagación del contexto de seguimiento distribuido (bibliotecas de instrumentación) mediante Azure Functions Worker

Aquellos que requieran una experiencia con todas las características deben usar el [SDK de Python-OpenCensus de Application Insights](opencensus-python.md) existente hasta que la oferta basada en OpenTelemetry madure.

---

## <a name="get-started"></a>Introducción

Siga los pasos de esta sección para instrumentar la aplicación con OpenTelemetry.

### <a name="prerequisites"></a>Prerrequisitos

- Una suscripción a Azure: [cree una de forma gratuita](https://azure.microsoft.com/free/).
- Recurso de Application Insights: [Creación de un recurso de Application Insights](create-workspace-resource.md#create-workspace-based-resource)

### <a name="net"></a>[.NET](#tab/net)

- Aplicación que usa una versión oficialmente compatible de [.NET Core](https://dotnet.microsoft.com/download/dotnet) o [.NET Framework](https://dotnet.microsoft.com/download/dotnet-framework) >= `.NET Framework 4.6.1`.


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- Aplicación que usa una [versión compatible](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments) oficialmente del runtime de Node.js.
  - [Runtimes compatibles de OpenTelemetry](https://github.com/open-telemetry/opentelemetry-js#supported-runtimes)
  - [Runtimes compatibles de Azure Monitor OpenTelemetry Exporter](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter#currently-supported-environments)

### <a name="python"></a>[Python](#tab/python)

- Aplicación de Python con la versión 3.6+


---

### <a name="install-the-client-libraries"></a>Instalación de las bibliotecas cliente

#### <a name="net"></a>[.NET](#tab/net)

Instale el paquete NuGet [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) más reciente:

```dotnetcli
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter 
```

Si recibe un error como "No hay ninguna versión disponible del paquete "Azure.Monitor.OpenTelemetry.Exporter", probablemente se deba a que falta el valor de los orígenes del paquete NuGet. Puede intentar especificar el origen con la opción `-s`:

```dotnetcli
# Install the latest package with NuGet package source specified
dotnet add package --prerelease Azure.Monitor.OpenTelemetry.Exporter -s https://api.nuget.org/v3/index.json
```

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Instale estos paquetes:

- [@opentelemetry/sdk-trace-base](https://www.npmjs.com/package/@opentelemetry/sdk-trace-base)
- [@opentelemetry/sdk-trace-node](https://www.npmjs.com/package/@opentelemetry/sdk-trace-node)
- [@azure/monitor-opentelemetry-exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter)

```sh
npm install @opentelemetry/sdk-trace-base
npm install @opentelemetry/sdk-trace-node
npm install @azure/monitor-opentelemetry-exporter
```

Los siguientes paquetes también se usan en algunos escenarios específicos que se describen más adelante en este artículo.

- [@opentelemetry/api](https://www.npmjs.com/package/@opentelemetry/api)
- [@opentelemetry/resources](https://www.npmjs.com/package/@opentelemetry/resources)
- [@opentelemetry/semantic-conventions](https://www.npmjs.com/package/@opentelemetry/semantic-conventions)
- [@opentelemetry/instrumentation-http](https://www.npmjs.com/package/@opentelemetry/instrumentation-http)

```sh
npm install @opentelemetry/api
npm install @opentelemetry/resources
npm install @opentelemetry/semantic-conventions
npm install @opentelemetry/instrumentation-http
```

#### <a name="python"></a>[Python](#tab/python)

Instale el paquete Pypi [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) más reciente.

```sh
pip install azure-monitor-opentelemetry-exporter 
```

---

### <a name="enable-azure-monitor-application-insights"></a>Habilitación de Azure Monitor Application Insights

#### <a name="add-opentelemetry-instrumentation-code"></a>Incorporación de código de instrumentación de OpenTelemetry

##### <a name="net"></a>[.NET](#tab/net)

> [!NOTE]
> En las siguientes instrucciones se muestra cómo habilitar Azure Monitor Application Insights para aplicaciones de consola de C#.
> 
> Vea los Léames de GitHub de OpenTelemetry para obtener instrucciones sobre otros tipos de aplicaciones:
> - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Instrumentation.AspNet/README.md)
> - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Instrumentation.AspNetCore/README.md)
> - [HttpClient and HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Instrumentation.Http/README.md)
> 
> El método de extensión `AddAzureMonitorTraceExporter` para enviar datos a Application Insights es aplicable a todos los tipos de aplicaciones indicados.
> 
> Para obtener recursos adicionales, vea [Ejemplos de OpenTelemetry en GitHub](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/examples). 

```csharp
using System.Diagnostics;
using Azure.Monitor.OpenTelemetry.Exporter;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class Program
{
    private static readonly ActivitySource MyActivitySource = new ActivitySource(
        "OTel.AzureMonitor.Demo");

    public static void Main()
    {
        using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddAzureMonitorTraceExporter(o =>
            {
                o.ConnectionString = "<Your Connection String>";
            })
            .Build();

        using (var activity = MyActivitySource.StartActivity("TestActivity"))
        {
            activity?.SetTag("CustomTag1", "Value1");
            activity?.SetTag("CustomTag2", "Value2");
        }

        System.Console.WriteLine("Press Enter key to exit.");
        System.Console.ReadLine();
    }
}
```

> [!NOTE]
> Las clases `Activity` y `ActivitySource` del espacio de nombres `System.Diagnostics` representan los conceptos de OpenTelemetry de `Span` y `Tracer` respectivamente. `ActivitySource` se crea directamente mediante su constructor en lugar de usar `TracerProvider` (cada [`ActivitySource`](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/docs/trace/customizing-the-sdk#activity-source) debe conectarse explícitamente a `TracerProvider` mediante `AddSource()`). Esto se debe a que elementos de la API de seguimiento de OpenTelemetry se incorporan directamente al runtime de .NET. [Más información](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/src/OpenTelemetry.Api/README.md#introduction-to-opentelemetry-net-tracing-api).

##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

En el código siguiente se muestra cómo habilitar OpenTelemetry en una aplicación de Node.js sencilla.

```typescript
const { AzureMonitorTraceExporter } = require("@azure/monitor-opentelemetry-exporter");
const { BatchSpanProcessor, Span } = require("@opentelemetry/sdk-trace-base");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
provider.register();

// Create an exporter instance
const exporter = new AzureMonitorTraceExporter({
  instrumentationKey: "<Your Connection String>"
});

// Add the exporter to the provider
provider.addSpanProcessor(
  new BatchSpanProcessor(exporter, {
    bufferTimeout: 15000,
    bufferSize: 1000
  })
);
// Create a span. A span must be closed.
const parentSpan = tracer.startSpan("main");
for (let i = 0; i < 10; i += 1) {
   doWork(parentSpan);
}
// Be sure to end the span.
parentSpan.end();

function doWork(parent: Span) {
  // Start another span. In this example, the main method already started a
  // span, so that'll be the parent span, and this will be a child span.
  const ctx = opentelemetry.trace.setSpan(opentelemetry.context.active(), parent);
  const span = tracer.startSpan("doWork", undefined, ctx);
  // simulate some random work.
  for (let i = 0; i <= Math.floor(Math.random() * 40000000); i += 1) {
    // empty
  }
  // Set attributes to the span.
  span.setAttribute("key", "value");
  // Annotate our span to capture metadata about our operation
  span.addEvent("invoking doWork");
  span.end();
}
```

##### <a name="python"></a>[Python](#tab/python)

En el código siguiente se muestra cómo habilitar OpenTelemetry en una aplicación de Python sencilla.

```python
import os
from opentelemetry import trace
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__)
span_processor = BatchSpanProcessor(exporter)
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("hello"):
    print("Hello, World!")

```

---

#### <a name="set-application-insights-connection-string"></a>Establecimiento de la cadena de conexión de Application Insights

Reemplace el marcador de posición `<Your Connection String>` del código anterior por la cadena de conexión de SU recurso de Application Insights.

:::image type="content" source="media/opentelemetry/connection-string.png" alt-text="Captura de pantalla de la cadena de conexión de Application Insights.":::

#### <a name="confirm-data-is-flowing"></a>Confirmación de que los datos fluyen

Ejecute la aplicación y abra la pestaña Recurso de Application Insights en Azure Portal. Los datos pueden tardar unos minutos en aparecer en el portal.

> [!NOTE]
> Si no puede ejecutar la aplicación o no obtiene datos según lo previsto, vaya a [Solución de problemas](#troubleshooting).

:::image type="content" source="media/opentelemetry/server-requests.png" alt-text="Captura de pantalla de la pestaña Información general de Application Insights con las solicitudes de servidor y el tiempo de respuesta del servidor resaltados.":::

> [!IMPORTANT]
> Si tiene dos o más servicios que emiten telemetría al mismo recurso de Application Insights, debe [establecer nombres de rol en la nube](#set-cloud-role-name-and-cloud-role-instance) para representarlos correctamente en el mapa de aplicación.

> [!NOTE]
> Como parte del uso de la instrumentación de Application Insights, se recopilan y envían datos de diagnóstico a Microsoft. Estos datos ayudan a ejecutar y mejorar Application Insights. Tiene la opción de deshabilitar la recopilación de datos no esenciales. [Más información](./statsbeat.md).

## <a name="set-cloud-role-name-and-cloud-role-instance"></a>Establecimiento de Nombre del rol en la nube e Instancia de rol en la nube

Puede establecer [Nombre del rol en la nube](app-map.md#understanding-cloud-role-name-within-the-context-of-the-application-map) e Instancia de rol en la nube por medio de los atributos de [Recurso](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/sdk.md#resource-sdk). Esto actualiza Nombre del rol en la nube e Instancia de rol en la nube de su valor predeterminado a algo que tenga sentido para el equipo. Aparece en el mapa de aplicación como el nombre debajo de un nodo. Nombre del rol en la nube usa los atributos `service.namespace` y `service.name`, aunque recurre a `service.name` si `service.namespace` no está establecido. Instancia de rol en la nube usa el valor del atributo `service.instance.id`.

### <a name="net"></a>[.NET](#tab/net)

```csharp
// Setting Role name and Role instance
var resourceAttributes = new Dictionary<string, object> {
    { "service.name", "my-service" },
    { "service.namespace", "my-namespace" },
    { "service.instance.id", "my-instance" }};
var resourceBuilder = ResourceBuilder.CreateDefault().AddAttributes(resourceAttributes);
// Done setting Role name and Role instance

// Set ResourceBuilder on the provider
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .SetResourceBuilder(resourceBuilder)
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>";
        })
        .Build();
```


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

```typescript
...
import { NodeTracerProvider, NodeTracerConfig } from "@opentelemetry/sdk-trace-node";
import { Resource } from "@opentelemetry/resources";
import { SemanticResourceAttributes } from "@opentelemetry/semantic-conventions";

// ----------------------------------------
// Setting Role name and role instance
// ----------------------------------------
const config: NodeTracerConfig = {
        resource: new Resource({
            [SemanticResourceAttributes.SERVICE_NAME]: "my-helloworld-service",
            [SemanticResourceAttributes.SERVICE_NAMESPACE]: "my-namespace",
            [SemanticResourceAttributes.SERVICE_INSTANCE_ID]: "my-instance",
        }),
    };
// ----------------------------------------
// Done setting Role name and role instance
// ----------------------------------------
const provider = new NodeTracerProvider(config);
...
```

### <a name="python"></a>[Python](#tab/python)

```python
...
from opentelemetry.sdk.resources import SERVICE_NAME, SERVICE_NAMESPACE, SERVICE_INSTANCE_ID, Resource
trace.set_tracer_provider(
    TracerProvider(
        resource=Resource.create(
            {
                SERVICE_NAME: "my-helloworld-service",
# ----------------------------------------
# Setting Role name and role instance
# ----------------------------------------
                SERVICE_NAMESPACE: "my-namespace",
                SERVICE_INSTANCE_ID: "my-instance",
# ----------------------------------------------
# Done setting Role name and role instance
# ----------------------------------------------
            }
        )
    )
)
...
```

---

Referencia: [Convenciones semánticas de recursos](https://github.com/open-telemetry/opentelemetry-specification/blob/main/specification/resource/semantic_conventions/README.md)

## <a name="sampling"></a>muestreo

Aunque el muestreo se admite en OpenTelemetry, no así en Azure Monitor OpenTelemetry Exporter en este momento.

> [!WARNING]
> Al habilitar el muestreo en OpenTelemetry, las métricas estándar y basadas en registros se vuelven extremadamente imprecisas, lo que afecta negativamente a todas las experiencias de Application Insights. Además, la habilitación del muestreo junto con los SDK de Application Insights existentes da lugar a seguimientos rotos.

## <a name="instrumentation-libraries"></a>Bibliotecas de instrumentación
<!-- Microsoft has tested and validated that the following instrumentation libraries will work with the **Preview** Release. -->
Las bibliotecas siguientes están validadas para funcionar con la versión preliminar:

> [!WARNING]
> Las bibliotecas de instrumentación se basan en especificaciones experimentales de OpenTelemetry. El compromiso de soporte técnico de la **versión preliminar** de Microsoft es garantizar que las bibliotecas enumeradas a continuación emitan datos a Azure Monitor Application Insights, aunque es posible que los cambios importantes o la asignación experimental bloqueen algunos elementos de datos.

### <a name="http"></a>HTTP

#### <a name="net"></a>[.NET](#tab/net)

- Versión de [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md): [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNet/1.0.0-rc7)
- Versión de [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md): [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.AspNetCore/1.0.0-rc7)
- Versión de [clientes HTTP](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md): [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.Http/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- Versión de [http/https](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http/README.md): [0.26.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-http/v/0.26.0)


#### <a name="python"></a>[Python](#tab/python)

- Versión de [Django](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-django/README.md): [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-django/0.24b0/)
- Versión de [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask/README.md): [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-flask/0.24b0/)
- Versión de [solicitudes](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-requests/README.md): [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-requests/0.24b0/)

---

### <a name="database"></a>Base de datos

#### <a name="net"></a>[.NET](#tab/net)

- Versión del [cliente de SQL](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.SqlClient/README.md): [1.0.0-rc7](https://www.nuget.org/packages/OpenTelemetry.Instrumentation.SqlClient/1.0.0-rc7)

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- Versión de [mysql](https://github.com/open-telemetry/opentelemetry-js-contrib/tree/main/plugins/node/opentelemetry-instrumentation-mysql): [0.25.0](https://www.npmjs.com/package/@opentelemetry/instrumentation-mysql/v/0.25.0)

#### <a name="python"></a>[Python](#tab/python)

- Versión de [Psycopg2](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-psycopg2): [0.24b0](https://pypi.org/project/opentelemetry-instrumentation-psycopg2/0.24b0/)

---

> [!NOTE]
> La oferta de **versión preliminar** solo incluye instrumentaciones que controlan solicitudes HTTP y de base de datos. Vea [Convenciones semánticas de OpenTelemetry](https://github.com/open-telemetry/opentelemetry-specification/tree/main/specification/trace/semantic_conventions) para obtener más información.

## <a name="modify-telemetry"></a>Modificación de la telemetría

### <a name="add-span-attributes"></a>Incorporación de atributos de intervalo

Los atributos de intervalo se pueden agregar de alguna de las dos maneras siguientes.
1. Uso de las opciones proporcionadas por las [bibliotecas de instrumentación](#instrumentation-libraries).
2. Incorporación de un procesador de intervalos personalizado.

Estos atributos pueden incluir la incorporación de una propiedad empresarial personalizada a la telemetría. También puede usar atributos para establecer campos opcionales en el esquema de Application Insights, como Id. de usuario o IP del cliente.

> [!TIP]
> La ventaja de usar "opciones proporcionadas por las bibliotecas de instrumentación" (cuando están disponibles) es que todo el contexto está disponible, lo que significa que los usuarios pueden optar por agregar o filtrar atributos adicionales. Por ejemplo, la opción de enriquecimiento de la biblioteca de instrumentación HttpClient incluye dar a los usuarios acceso al propio httpRequestMessage para seleccionar cualquier elemento de ahí y almacenarlo como un atributo.

#### <a name="add-custom-property"></a>Adición de una propiedad personalizada

Los [atributos](#add-span-attributes) que se agregan a la actividad o el intervalo se exportan como propiedades personalizadas. Rellenan el campo _customDimensions_ de las tablas de solicitudes o dependencias de Application Insights.

##### <a name="net"></a>[.NET](#tab/net)

1. Muchas bibliotecas de instrumentación proporcionan una opción de enriquecimiento. Vea el Léame de las bibliotecas de instrumentación correspondientes para obtener instrucciones.
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#enrich)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#enrich)
    - [HttpClient y HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#enrich)

2. Uso de procesador personalizado:

> [!TIP]
> Agregue el procesador que se muestra a continuación ANTES de Azure Monitor Exporter.

```csharp
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddProcessor(new ActivityEnrichingProcessor())
        .AddAzureMonitorTraceExporter(o =>
        {
                o.ConnectionString = "<Your Connection String>"
        })
        .Build();
```

Agregue `ActivityEnrichingProcessor.cs` al proyecto con el código siguiente.

```csharp
using System.Diagnostics;
using OpenTelemetry;
using OpenTelemetry.Trace;

public class ActivityEnrichingProcessor : BaseProcessor<Activity>
{
    public override void OnEnd(Activity activity)
    {
        // The updated activity will be available to all processors which are called after this processor.
        activity.DisplayName = "Updated-" + activity.DisplayName;
        activity.SetTag("CustomDimension1", "Value1");
        activity.SetTag("CustomDimension2", "Value2");
    }
}
```


##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Uso de procesador personalizado:

> [!TIP]
> Agregue el procesador que se muestra a continuación ANTES de Azure Monitor Exporter.

```typescript
import { AzureMonitorTraceExporter } from "@azure/monitor-opentelemetry-exporter";
import { NodeTracerProvider } from "@opentelemetry/sdk-trace-node";
import { ReadableSpan, SimpleSpanProcessor, Span, SpanProcessor } from "@opentelemetry/sdk-trace-base";

class SpanEnrichingProcessor implements SpanProcessor{
    forceFlush(): Promise<void>{
        return Promise.resolve();
    }
    shutdown(): Promise<void>{
        return Promise.resolve();
    }
    onStart(_span: Span): void{}
    onEnd(span: ReadableSpan){
        span.attributes["CustomDimension1"] = "value1";
        span.attributes["CustomDimension2"] = "value2";
    }
}

const provider = new NodeTracerProvider();
const azureExporter = new AzureMonitorTraceExporter();
provider.addSpanProcessor(new SpanEnrichingProcessor());
provider.addSpanProcessor(new SimpleSpanProcessor(azureExporter));

```

##### <a name="python"></a>[Python](#tab/python)

Uso de procesador personalizado:

> [!TIP]
> Agregue el procesador que se muestra a continuación ANTES de Azure Monitor Exporter.

```python
...
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

trace.set_tracer_provider(TracerProvider())
span_processor = BatchSpanProcessor(exporter)
span_enrich_processor = SpanEnrichingProcessor()
trace.get_tracer_provider().add_span_processor(span_enrich_processor)
trace.get_tracer_provider().add_span_processor(span_processor)
...
```

Agregue `SpanEnrichingProcessor.py` al proyecto con el código siguiente.

```python
from opentelemetry.sdk.trace import SpanProcessor

class SpanEnrichingProcessor(SpanProcessor):

    def on_end(self, span):
        span._name = "Updated-" + span.name
        span._attributes["CustomDimension1"] = "Value1"
        span._attributes["CustomDimension2"] = "Value2"

```
---

#### <a name="set-user-ip"></a>Establecimiento de IP del usuario

Puede rellenar el campo _client_IP_ de las solicitudes si establece el atributo `http.client_ip` en la actividad o el intervalo. Application Insights usa la dirección IP para generar atributos de ubicación del usuario y luego la [descarta de manera predeterminada](ip-collection.md#default-behavior).

##### <a name="net"></a>[.NET](#tab/net)

Use el [ejemplo de incorporación de propiedad personalizada](#add-custom-property), pero cambie las siguientes líneas de código de `ActivityEnrichingProcessor.cs`:

```C#
activity.SetTag("http.client_ip", "<IP Address>");
```

##### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Use el [ejemplo de incorporación de propiedad personalizada](#add-custom-property), pero cambie las siguientes líneas de código:

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.HTTP_CLIENT_IP] = "<IP Address>";
    }
}
```

##### <a name="python"></a>[Python](#tab/python)

Use el [ejemplo de incorporación de propiedad personalizada](#add-custom-property), pero cambie las siguientes líneas de código de `SpanEnrichingProcessor.py`:

```python
span._attributes["http.client_ip"] = "<IP Address>"
```

---
<!--

#### Set user ID or authenticated user ID

You can populate the _user_Id_ or _user_Authenticatedid_ field for requests by setting `xyz` or `xyz` attribute on activity/span. User ID is an anonymous user identifier and Authenticated User ID is a known user identifier.

> [!IMPORTANT]
> Consult applicable privacy laws before setting Authenticated User ID.

##### [.NET](#tab/net)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```C#
Placeholder
```

##### [Node.js](#tab/nodejs)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```typescript
...
import { SemanticAttributes } from "@opentelemetry/semantic-conventions";

class SpanEnrichingProcessor implements SpanProcessor{
    ...

    onEnd(span: ReadableSpan){
        span.attributes[SemanticAttributes.ENDUSER_ID] = "<User ID>";
    }
}
```

##### [Python](#tab/python)

Use the add [custom property example](#add-custom-property), except change out the following lines of code:

```python
span._attributes["enduser.id"] = "<User ID>"
```

---
-->

### <a name="filter-telemetry"></a>Telemetría de filtro

Puede usar las siguientes maneras de filtrar la telemetría antes de salir de la aplicación.

#### <a name="net"></a>[.NET](#tab/net)

1. Muchas bibliotecas de instrumentación proporcionan una opción de filtrado. Vea el Léame de las bibliotecas de instrumentación correspondientes para obtener instrucciones.
    - [ASP.NET](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNet/README.md#filter)
    - [ASP.NET Core](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.AspNetCore/README.md#filter)
    - [HttpClient y HttpWebRequest](https://github.com/open-telemetry/opentelemetry-dotnet/blob/1.0.0-rc7/src/OpenTelemetry.Instrumentation.Http/README.md#filter)

2. Uso de procesador personalizado:
    
    ```csharp
    using var tracerProvider = Sdk.CreateTracerProviderBuilder()
            .AddSource("OTel.AzureMonitor.Demo")
            .AddProcessor(new ActivityFilteringProcessor())
            .AddAzureMonitorTraceExporter(o =>
            {
                    o.ConnectionString = "<Your Connection String>"
            })
            .Build();
    ```
    
    Agregue `ActivityFilteringProcessor.cs` al proyecto con el código siguiente.
    
    ```csharp
    using System.Diagnostics;
    using OpenTelemetry;
    using OpenTelemetry.Trace;
    
    public class ActivityFilteringProcessor : BaseProcessor<Activity>
    {
        public override void OnStart(Activity activity)
        {
            // prevents all exporters from exporting internal activities
            if (activity.Kind == ActivityKind.Internal)
            {
                activity.IsAllDataRequested = false;
            }
        }
    }
    ```


3. Si un origen determinado no se agrega de manera explícita mediante `AddSource("ActivitySourceName")`, no se exporta ninguna de las actividades creadas con ese origen.
    
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```C#
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. Excluya la opción de dirección URL proporcionada por muchas bibliotecas de instrumentación http.

    A continuación se muestra un ejemplo de cómo excluir una dirección URL determinada del seguimiento mediante la [biblioteca de instrumentación http/https](https://github.com/open-telemetry/opentelemetry-js/tree/main/experimental/packages/opentelemetry-instrumentation-http).
    
    ```typescript
    ...
    import { HttpInstrumentation, HttpInstrumentationConfig } from "@opentelemetry/instrumentation-http";
    
    ...
    const httpInstrumentationConfig: HttpInstrumentationConfig = {
        ignoreIncomingPaths: [new RegExp(/dc.services.visualstudio.com/i)]
    };
    const httpInstrumentation = new HttpInstrumentation(httpInstrumentationConfig);
    provider.register();
    registerInstrumentations({
        instrumentations: [
            httpInstrumentation,
        ]
    });
    
    ```

2. Uso de procesador personalizado. Puede usar un procesador de intervalos personalizado para excluir determinados intervalos de la exportación. Para marcar los intervalos que no se van a exportar, establezca `TraceFlag` en `DEFAULT`.
Use el [ejemplo de incorporación de propiedad personalizada](#add-custom-property), pero cambie las siguientes líneas de código:

    ```typescript
    ...
    import { SpanKind, TraceFlags } from "@opentelemetry/api";
    
    class SpanEnrichingProcessor implements SpanProcessor{
        ...
    
        onEnd(span: ReadableSpan) {
            if(span.kind == SpanKind.INTERNAL){
                span.spanContext().traceFlags = TraceFlags.NONE;
            }
        }
    }
    ```

#### <a name="python"></a>[Python](#tab/python)

1. Excluya la opción de dirección URL proporcionada por muchas bibliotecas de instrumentación http.

    A continuación se muestra un ejemplo de cómo excluir una dirección URL determinada del seguimiento mediante la instrumentación de [Flask](https://github.com/open-telemetry/opentelemetry-python-contrib/tree/main/instrumentation/opentelemetry-instrumentation-flask).
    
    ```python
    ...
    import flask
    
    from opentelemetry.instrumentation.flask import FlaskInstrumentor
    
    # You may also populate OTEL_PYTHON_FLASK_EXCLUDED_URLS env variable
    # List will consist of comma delimited regexes representing which URLs to exclude
    excluded_urls = "client/.*/info,healthcheck"
    
    FlaskInstrumentor().instrument(excluded_urls=excluded_urls) # Do this before flask.Flask
    app = flask.Flask(__name__)
    ...
    ```

2. Uso de procesador personalizado. Puede usar un procesador de intervalos personalizado para excluir determinados intervalos de la exportación. Para marcar los intervalos que no se van a exportar, establezca `TraceFlag` en `DEFAULT`.
    
    ```python
    ...
    from opentelemetry.sdk.trace import TracerProvider
    from opentelemetry.sdk.trace.export import BatchSpanProcessor
    
    trace.set_tracer_provider(TracerProvider())
    span_processor = BatchSpanProcessor(exporter)
    span_filter_processor = SpanFilteringProcessor()
    trace.get_tracer_provider().add_span_processor(span_filter_processor)
    trace.get_tracer_provider().add_span_processor(span_processor)
    ...
    ```
    
    Agregue `SpanFilteringProcessor.py` al proyecto con el código siguiente.
    
    ```python
    from opentelemetry.trace import SpanContext, SpanKind, TraceFlags
    from opentelemetry.sdk.trace import SpanProcessor
    
    class SpanFilteringProcessor(SpanProcessor):
    
        # prevents exporting spans from internal activities
        def on_start(self, span):
            if span._kind is SpanKind.INTERNAL:
                span._context = SpanContext(
                    span.context.trace_id,
                    span.context.span_id,
                    span.context.is_remote,
                    TraceFlags.DEFAULT,
                    span.context.trace_state,
                )
    
    ```
    
    <!-- For more information, see [GitHub Repo](link). -->
    <!---
    ### Get Trace ID or Span ID
    You may use X or Y to get trace ID and/or span ID. Adding trace ID and/or span ID to existing logging telemetry enables better correlation when debugging and diagnosing issues.
    
    > [!NOTE]
    > If you are manually creating spans for log-based metrics and alerting, you will need to update them to use the metrics API (after it is released) to ensure accuracy.
    
    ```python
    Placeholder
    ```
    
    For more information, see [GitHub Repo](link).
    --->

---

## <a name="enable-otlp-exporter"></a>Habilitación del exportador de OTLP

Es posible que quiera habilitar el exportador de OpenTelemetry Protocol (OTLP) junto con Azure Monitor Exporter para enviar la telemetría a dos ubicaciones.

> [!NOTE]
> El exportador de OTLP solo se muestra por comodidad. Oficialmente, no se admite el exportador de OTLP ni ningún componente o experiencia de terceros de nivel inferior. Se recomienda abrir una incidencia con el [OpenTelemetry-Collector](https://github.com/open-telemetry/opentelemetry-collector) de problemas de OpenTelemetry fuera del límite de Soporte técnico de Azure.

#### <a name="net"></a>[.NET](#tab/net)

1. Instale el paquete [OpenTelemetry.Exporter.OpenTelemetryProtocol](https://www.nuget.org/packages/OpenTelemetry.Exporter.OpenTelemetryProtocol/) junto con [Azure.Monitor.OpenTelemetry.Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter) en el proyecto.

2. Agregue el siguiente fragmento de código. En este ejemplo se da por hecho que tiene un recopilador de OpenTelemetry con un receptor de OTLP en ejecución. Para obtener detalles, vea el ejemplo que aparece [aquí](https://github.com/open-telemetry/opentelemetry-dotnet/blob/main/examples/Console/TestOtlpExporter.cs).

```csharp
// sends data to Application Insights as well as OTLP
using var tracerProvider = Sdk.CreateTracerProviderBuilder()
        .AddSource("OTel.AzureMonitor.Demo")
        .AddAzureMonitorTraceExporter(o =>
        {
            o.ConnectionString = "<Your Connection String>"
        })
        .AddOtlpExporter()
        .Build();
```


#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

1. Instale el paquete [OpenTelemetry Collector Exporter](https://www.npmjs.com/package/@opentelemetry/exporter-otlp-http) junto con [Azure Monitor OpenTelemetry Exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter) en el proyecto.


```sh
    npm install @opentelemetry/exporter-otlp-http
    npm install @azure/monitor-opentelemetry-exporter
```

2. Agregue el siguiente fragmento de código. En este ejemplo se da por hecho que tiene un recopilador de OpenTelemetry con un receptor de OTLP en ejecución. Para obtener detalles, vea el ejemplo que aparece [aquí](https://github.com/open-telemetry/opentelemetry-js/tree/main/examples/otlp-exporter-node).

```typescript
const { BasicTracerProvider, SimpleSpanProcessor } = require('@opentelemetry/sdk-trace-base');
const { OTLPTraceExporter } = require('@opentelemetry/exporter-otlp-http');

const provider = new BasicTracerProvider();
const azureMonitorExporter = new AzureMonitorTraceExporter({
  instrumentationKey: os.environ["APPLICATIONINSIGHTS_CONNECTION_STRING"]
});
const otlpExporter = new OTLPTraceExporter();
provider.addSpanProcessor(new SimpleSpanProcessor(azureMonitorExporter));
provider.addSpanProcessor(new SimpleSpanProcessor(otlpExporter));
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

1. Instale los paquetes [azure-monitor-opentelemetry-exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/) y [opentelemetry-exporter-otlp](https://pypi.org/project/opentelemetry-exporter-otlp/).

2. Agregue el siguiente fragmento de código. En este ejemplo se da por hecho que tiene un recopilador de OpenTelemetry con un receptor de OTLP en ejecución. Para obtener detalles, vea este [LÉAME](https://github.com/Azure/azure-sdk-for-python/tree/main/sdk/monitor/azure-monitor-opentelemetry-exporter/samples/traces#collector).

```python
from opentelemetry import trace 

from opentelemetry.exporter.otlp.proto.grpc.trace_exporter import OTLPSpanExporter
from opentelemetry.sdk.trace import TracerProvider
from opentelemetry.sdk.trace.export import BatchSpanProcessor

from azure.monitor.opentelemetry.exporter import AzureMonitorTraceExporter

trace.set_tracer_provider(TracerProvider())
tracer = trace.get_tracer(__name__) 

exporter = AzureMonitorTraceExporter.from_connection_string(
    "<Your Connection String>"
)
otlp_exporter = OTLPSpanExporter(endpoint="http://localhost:4317")
span_processor = BatchSpanProcessor(otlp_exporter) 
trace.get_tracer_provider().add_span_processor(span_processor)

with tracer.start_as_current_span("test"):
    print("Hello world!")
```

---

## <a name="troubleshooting"></a>Solución de problemas

### <a name="enable-diagnostic-logging"></a>Activación del registro de diagnóstico

#### <a name="net"></a>[.NET](#tab/net)

Azure Monitor Exporter usa EventSource para su propio registro interno. Los registros del exportador están disponibles para cualquier elemento EventListener si se opta por el origen de nombre "OpenTelemetry-AzureMonitor-Exporter". Vea [Solución de problemas de OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet/tree/main/src/OpenTelemetry#troubleshooting) para obtener pasos de solución de problemas detallados.

#### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Azure Monitor Exporter usa OpenTelemetry API Logger para los registros internos; se puede habilitar mediante el código siguiente. 

```typescript
const { diag, DiagConsoleLogger, DiagLogLevel } = require("@opentelemetry/api");
const { NodeTracerProvider } = require("@opentelemetry/sdk-trace-node");

const provider = new NodeTracerProvider();
diag.setLogger(new DiagConsoleLogger(), DiagLogLevel.ALL);
provider.register();
```

#### <a name="python"></a>[Python](#tab/python)

Azure Monitor Exporter usa la [biblioteca](https://docs.python.org/3/library/logging.html) de registro estándar de Python para su propio registro interno. Los registros de la API de OpenTelemetry y Azure Monitor Exporter suelen registrarse en el nivel de gravedad ADVERTENCIA o ERROR de actividad irregular, e INFORMACIÓN de actividad normal o correcta. El valor predeterminado de la biblioteca de registro de Python es ADVERTENCIA, por lo que debe cambiar el nivel de gravedad para ver los registros de menor gravedad. A continuación se muestra un ejemplo de cómo generar registros de gravedad TODOS en la consola Y un archivo.

```python
...
import logging

logging.basicConfig(format="%(asctime)s:%(levelname)s:%(message)s", level=logging.DEBUG)

logger = logging.getLogger(__name__)
file = logging.FileHandler("example.log")
stream = logging.StreamHandler()
logger.addHandler(file)
logger.addHandler(stream)
...

```

---

### <a name="known-issues"></a>Problemas conocidos

Los problemas conocidos de Azure Monitor OpenTelemetry Exporter incluyen: 

- Falta el nombre de la operación en la telemetría de dependencias, lo que afecta negativamente a los errores y a la experiencia de la pestaña de rendimiento.
- Falta el modelo de dispositivo en las solicitudes y la telemetría de dependencias, lo que afecta negativamente al análisis de cohortes de dispositivos.
- El nombre del servidor de bases de datos se ha dejado fuera del nombre de la dependencia, lo que agrega incorrectamente tablas con el mismo nombre en servidores diferentes.

## <a name="support"></a>Soporte técnico

- Revise los pasos de solución de problemas de este artículo.
- En caso de problemas de Soporte técnico de Azure, abra una [incidencia de Soporte técnico de Azure](https://azure.microsoft.com/support/create-ticket/).

### <a name="net"></a>[.NET](#tab/net)

En caso de problemas de OpenTelemetry, póngase en contacto con la [comunidad de .NET de OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet) directamente.

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

En caso de problemas de OpenTelemetry, póngase en contacto con la [comunidad de JavaScript de OpenTelemetry](https://github.com/open-telemetry/opentelemetry-js) directamente.

### <a name="python"></a>[Python](#tab/python)

En caso de problemas de OpenTelemetry, póngase en contacto con la [comunidad de Python de OpenTelemetry](https://github.com/open-telemetry/opentelemetry-python) directamente.

---

## <a name="opentelemetry-feedback"></a>Comentarios de OpenTelemetry

- Rellene la [encuesta de comentarios de los clientes](https://docs.google.com/forms/d/e/1FAIpQLScUt4reClurLi60xyHwGozgM9ZAz8pNAfBHhbTZ4gFWaaXIRQ/viewform) de la comunidad de OpenTelemetry.
- Únase a la [comunidad de usuarios pioneros de OpenTelemetry](https://aka.ms/AzMonOTel/) y cuente a Microsoft un poco sobre sí mismo.
- Interactúe con otros usuarios de Azure Monitor en [Microsoft Tech Community](https://techcommunity.microsoft.com/t5/azure-monitor/bd-p/AzureMonitor).

## <a name="next-steps"></a>Pasos siguientes

### <a name="net"></a>[.NET](#tab/net)

- Revise el código fuente en el [repositorio de GitHub de Azure Monitor Exporter](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter).
- Para instalar el paquete NuGet, buscar actualizaciones o ver las notas de la versión, visite la página [Paquete NuGet de Azure Monitor Exporter](https://www.nuget.org/packages/Azure.Monitor.OpenTelemetry.Exporter/).
- Familiarícese con Azure Monitor Application Insights y OpenTelemetry con la [aplicación de ejemplo de Azure Monitor](https://github.com/Azure/azure-sdk-for-net/tree/main/sdk/monitor/Azure.Monitor.OpenTelemetry.Exporter/tests/Azure.Monitor.OpenTelemetry.Exporter.Tracing.Customization).
- Para obtener más información sobre OpenTelemetry y su comunidad, visite el [repositorio de GitHub de .NET de OpenTelemetry](https://github.com/open-telemetry/opentelemetry-dotnet).
- [Habilite la supervisión de usuarios web o de explorador](javascript.md) para habilitar experiencias de uso.


### <a name="nodejs"></a>[Node.js](#tab/nodejs)

- Revise el código fuente en el [repositorio de GitHub de Azure Monitor Exporter](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter).
- Para instalar el paquete NPM, buscar actualizaciones o ver las notas de la versión, visite la página [Paquete NPM de Azure Monitor Exporter](https://www.npmjs.com/package/@azure/monitor-opentelemetry-exporter).
- Familiarícese con Azure Monitor Application Insights y OpenTelemetry con la [aplicación de ejemplo de Azure Monitor](https://github.com/Azure/azure-sdk-for-js/tree/main/sdk/monitor/monitor-opentelemetry-exporter/samples).
- Para obtener más información sobre OpenTelemetry y su comunidad, visite el [repositorio de GitHub de JavaScript de OpenTelemetry](https://github.com/open-telemetry/opentelemetry-js).
- [Habilite la supervisión de usuarios web o de explorador](javascript.md) para habilitar experiencias de uso.

### <a name="python"></a>[Python](#tab/python)

- Revise el código fuente en el [repositorio de GitHub de Azure Monitor Exporter](https://github.com/Azure/azure-sdk-for-python/blob/main/sdk/monitor/azure-monitor-opentelemetry-exporter/README.md).
- Para instalar el paquete PyPI, buscar actualizaciones o ver las notas de la versión, visite la página [Paquete PyPI de Azure Monitor Exporter](https://pypi.org/project/azure-monitor-opentelemetry-exporter/).
-  Familiarícese con Azure Monitor Application Insights y OpenTelemetry con la [aplicación de ejemplo de Azure Monitor](https://github.com/Azure-Samples/azure-monitor-opentelemetry-python).
- Para obtener más información sobre OpenTelemetry y su comunidad, visite el [repositorio de GitHub de Python de OpenTelemetry](https://github.com/open-telemetry/opentelemetry-python).
- [Habilite la supervisión de usuarios web o de explorador](javascript.md) para habilitar experiencias de uso.

---
