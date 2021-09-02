---
title: Cadenas de conexión en Azure Application Insights | Microsoft Docs
description: Uso de cadenas de conexión
ms.topic: conceptual
author: timothymothra
ms.author: tilee
ms.date: 01/17/2020
ms.custom: devx-track-js, devx-track-csharp
ms.reviewer: mbullwin
ms.openlocfilehash: 2b4a7ac88f92c830daecf943d6937946c4a89f82
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114297451"
---
# <a name="connection-strings"></a>Cadenas de conexión

## <a name="overview"></a>Información general

Las cadenas de conexión proporcionan a los usuarios de Application Insights una única opción de configuración, lo que elimina la necesidad de varias configuraciones de proxy. Resultan de gran utilidad para los servidores web de la intranet y los entornos de nube híbrida o soberana que buscan enviar datos al servicio de supervisión.

Los pares clave-valor proporcionan a los usuarios una manera fácil de definir una combinación de sufijo y prefijo para cada servicio o producto de Application Insights (AI).

> [!IMPORTANT]
> No se recomienda establecer la cadena de conexión y la clave de instrumentación. En caso de que un usuario establezca ambas, tendrá prioridad la que se haya establecido en último lugar. 

> [!TIP]
> Se recomienda el uso de cadenas de conexión, en lugar de claves de instrumentación.

## <a name="scenario-overview"></a>Información general de escenario 

Escenarios de cliente en los que se visualiza dónde tiene esto el mayor efecto:

- Excepciones de firewall o redirecciones de proxy 

    En los casos en los que es necesario supervisar el servidor web de la intranet, nuestra solución anterior solicitaba a los clientes que agregaran puntos de conexión de servicio individuales a la configuración. Para más información, consulte [esta página](../faq.yml#can-i-monitor-an-intranet-web-server-). 
    Las cadenas de conexión ofrecen una mejor alternativa, ya que reducen esta labor a una única configuración. Una corrección de sufijos y prefijos simple permite el rellenado y la redirección automáticos de todos los puntos de conexión a los servicios adecuados. 

- Entornos de nube soberana o híbrida

    Los usuarios pueden enviar datos a una [región de Azure Government](../../azure-government/compare-azure-government-global-azure.md#application-insights) definida.
    Las cadenas de conexión permiten definir la configuración de punto de conexión de los servidores de la intranet o la configuración de la nube híbrida. 

## <a name="getting-started"></a>Introducción

### <a name="finding-my-connection-string"></a>¿Dónde está la cadena de conexión?

La cadena de conexión se muestra en la hoja de información general del recurso de Application Insights.

![cadena de conexión en la hoja de información general](media/overview-dashboard/overview-connection-string.png)

### <a name="schema"></a>Schema

#### <a name="max-length"></a>Longitud máxima

La conexión tiene una longitud máxima admitida de 4096 caracteres.

#### <a name="key-value-pairs"></a>Pares de clave-valor

La cadena de conexión consta de una lista de valores de configuración representados como pares de clave-valor separados por punto y coma: `key1=value1;key2=value2;key3=value3`

#### <a name="syntax"></a>Sintaxis

- `InstrumentationKey` (por ejemplo: 00000000-0000-0000-0000-000000000000) La cadena de conexión es un campo **obligatorio**.
- `Authorization` (por ejemplo: ikey) (Este valor es opcional porque actualmente no se admite la autorización mediante ikey.)
- `EndpointSuffix` (por ejemplo: applicationinsights.azure.cn) Al establecer el sufijo del punto de conexión, se indicará al SDK a qué nube de Azure se conectará. El SDK ensamblará el resto del punto de conexión para los servicios individuales.
- Puntos de conexión explícitos.
  Cualquier servicio se puede invalidar explícitamente en la cadena de conexión.
   - `IngestionEndpoint` (por ejemplo: `https://dc.applicationinsights.azure.com`)
   - `LiveEndpoint` (por ejemplo: `https://live.applicationinsights.azure.com`)
   - `ProfilerEndpoint` (por ejemplo: `https://profiler.monitor.azure.com`)
   - `SnapshotEndpoint` (por ejemplo: `https://snapshot.monitor.azure.com`)

#### <a name="endpoint-schema"></a>Esquema del punto de conexión

`<prefix>.<suffix>`
- Prefijo: define un servicio. 
- Sufijo: define el nombre de dominio común.

##### <a name="valid-suffixes"></a>Sufijos válidos

Esta es una lista de sufijos válidos. 
- applicationinsights.azure.cn
- applicationinsights.us


Consulte también: [Regiones que requieren la modificación del punto de conexión](./custom-endpoints.md#regions-that-require-endpoint-modification)


##### <a name="valid-prefixes"></a>Prefijos válidos

- [Ingesta de telemetría](./app-insights-overview.md): `dc`
- [Live Metrics](./live-stream.md): `live`
- [Profiler](./profiler-overview.md): `profiler`
- [Instantánea](./snapshot-debugger.md): `snapshot`



## <a name="connection-string-examples"></a>Ejemplos de cadena de conexión


### <a name="minimal-valid-connection-string"></a>Cadena de conexión válida mínima

`InstrumentationKey=00000000-0000-0000-0000-000000000000;`

En este ejemplo, solo se ha establecido la clave de instrumentación.

- El esquema de autorización tiene como valor predeterminado "ikey". 
- Clave de instrumentación: 00000000-0000-0000-0000-000000000000
- Los identificadores URI de servicio regional se basan en los [valores predeterminados del SDK](https://github.com/microsoft/ApplicationInsights-dotnet/blob/develop/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/Endpoints/Constants.cs) y se conectarán a Azure público global:
   - Ingesta: `https://dc.services.visualstudio.com/`
   - Live Metrics: `https://rt.services.visualstudio.com/`
   - Profiler: `https://profiler.monitor.azure.com/`
   - Depurador: `https://snapshot.monitor.azure.com/`



### <a name="connection-string-with-endpoint-suffix"></a>Cadena de conexión con sufijo de punto de conexión

`InstrumentationKey=00000000-0000-0000-0000-000000000000;EndpointSuffix=ai.contoso.com;`

En este ejemplo, esta cadena de conexión especifica el sufijo de punto de conexión, y el SDK construirá los puntos de conexión de servicio.

- El esquema de autorización tiene como valor predeterminado "ikey". 
- Clave de instrumentación: 00000000-0000-0000-0000-000000000000
- Los identificadores URI de servicio regional se basan en el sufijo de punto de conexión proporcionado: 
   - Ingesta: `https://dc.ai.contoso.com`
   - Live Metrics: `https://live.ai.contoso.com`
   - Profiler: `https://profiler.ai.contoso.com`
   - Depurador: `https://snapshot.ai.contoso.com`  



### <a name="connection-string-with-explicit-endpoint-overrides"></a>Cadena de conexión con invalidaciones explícitas del punto de conexión 

`InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://custom.com:111/;LiveEndpoint=https://custom.com:222/;ProfilerEndpoint=https://custom.com:333/;SnapshotEndpoint=https://custom.com:444/;`

En este ejemplo, esta cadena de conexión especifica invalidaciones explícitas para cada servicio. El SDK usará los puntos de conexión exactos proporcionados sin modificaciones.

- El esquema de autorización tiene como valor predeterminado "ikey". 
- Clave de instrumentación: 00000000-0000-0000-0000-000000000000
- Los identificadores URI de servicio regional se basan en los valores de invalidación explícitos: 
   - Ingesta: `https://custom.com:111/`
   - Live Metrics: `https://custom.com:222/`
   - Profiler: `https://custom.com:333/`
   - Depurador: `https://custom.com:444/`  


## <a name="how-to-set-a-connection-string"></a>Cómo establecer una cadena de conexión

Se admiten cadenas de conexión en las siguientes versiones del SDK:
- .NET y .NET Core v2.12.0
- Java v2.5.1 y Java 3.0
- JavaScript v2.3.0
- NodeJS v1.5.0
- Python v1.0.0

Una cadena de conexión se puede establecer por el código, la variable de entorno o el archivo de configuración.



### <a name="environment-variable"></a>Variable de entorno

- Cadena de conexión: `APPLICATIONINSIGHTS_CONNECTION_STRING`

### <a name="code-samples"></a>Ejemplos de código

# <a name="netnetcore"></a>[.NET/.NetCore](#tab/net)

Establezca la propiedad [TelemetryConfiguration.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/add45ceed35a817dc7202ec07d3df1672d1f610d/BASE/src/Microsoft.ApplicationInsights/Extensibility/TelemetryConfiguration.cs#L271-L274) o la propiedad [ApplicationInsightsServiceOptions.ConnectionString](https://github.com/microsoft/ApplicationInsights-dotnet/blob/81288f26921df1e8e713d31e7e9c2187ac9e6590/NETCORE/src/Shared/Extensions/ApplicationInsightsServiceOptions.cs#L66-L69).

.NET establecido explícitamente:
```csharp
var configuration = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
};
```

Archivo de configuración .NET:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

NetCore establecido explícitamente:
```csharp
public void ConfigureServices(IServiceCollection services)
{
    var options = new ApplicationInsightsServiceOptions { ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;" };
    services.AddApplicationInsightsTelemetry(options: options);
}
```

NetCore config.json: 

```json
{
  "ApplicationInsights": {
    "ConnectionString" : "InstrumentationKey=00000000-0000-0000-0000-000000000000;"
    }
  }
```


# <a name="java"></a>[Java](#tab/java)

Puede establecer la cadena de conexión en el archivo de configuración `applicationinsights.json`:

```json
{
  "connectionString": "InstrumentationKey=00000000-0000-0000-0000-000000000000"
}
```

Consulte [Configuración de la cadena de conexión](./java-standalone-config.md#connection-string) para más información.

Para Application Insights de Java 2.x, puede establecer la cadena de conexión en el archivo de configuración `ApplicationInsights.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationInsights xmlns="http://schemas.microsoft.com/ApplicationInsights/2013/Settings">
    <ConnectionString>InstrumentationKey=00000000-0000-0000-0000-000000000000</ConnectionString>
</ApplicationInsights>
```

# <a name="javascript"></a>[JavaScript](#tab/js)

Importante: JavaScript no admite el uso de variables de entorno.

Uso del fragmento de código:

El fragmento de código actual que se muestra a continuación es la versión "5"; la versión se codifica en el fragmento de código como sv:"#", y la [versión actual también está disponible en GitHub](https://go.microsoft.com/fwlink/?linkid=2156318).

```html
<script type="text/javascript">
!function(T,l,y){var S=T.location,k="script",D="instrumentationKey",C="ingestionendpoint",I="disableExceptionTracking",E="ai.device.",b="toLowerCase",w="crossOrigin",N="POST",e="appInsightsSDK",t=y.name||"appInsights";(y.name||T[e])&&(T[e]=t);var n=T[t]||function(d){var g=!1,f=!1,m={initialize:!0,queue:[],sv:"5",version:2,config:d};function v(e,t){var n={},a="Browser";return n[E+"id"]=a[b](),n[E+"type"]=a,n["ai.operation.name"]=S&&S.pathname||"_unknown_",n["ai.internal.sdkVersion"]="javascript:snippet_"+(m.sv||m.version),{time:function(){var e=new Date;function t(e){var t=""+e;return 1===t.length&&(t="0"+t),t}return e.getUTCFullYear()+"-"+t(1+e.getUTCMonth())+"-"+t(e.getUTCDate())+"T"+t(e.getUTCHours())+":"+t(e.getUTCMinutes())+":"+t(e.getUTCSeconds())+"."+((e.getUTCMilliseconds()/1e3).toFixed(3)+"").slice(2,5)+"Z"}(),iKey:e,name:"Microsoft.ApplicationInsights."+e.replace(/-/g,"")+"."+t,sampleRate:100,tags:n,data:{baseData:{ver:2}}}}var h=d.url||y.src;if(h){function a(e){var t,n,a,i,r,o,s,c,u,p,l;g=!0,m.queue=[],f||(f=!0,t=h,s=function(){var e={},t=d.connectionString;if(t)for(var n=t.split(";"),a=0;a<n.length;a++){var i=n[a].split("=");2===i.length&&(e[i[0][b]()]=i[1])}if(!e[C]){var r=e.endpointsuffix,o=r?e.location:null;e[C]="https://"+(o?o+".":"")+"dc."+(r||"services.visualstudio.com")}return e}(),c=s[D]||d[D]||"",u=s[C],p=u?u+"/v2/track":d.endpointUrl,(l=[]).push((n="SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details)",a=t,i=p,(o=(r=v(c,"Exception")).data).baseType="ExceptionData",o.baseData.exceptions=[{typeName:"SDKLoadFailed",message:n.replace(/\./g,"-"),hasFullStack:!1,stack:n+"\nSnippet failed to load ["+a+"] -- Telemetry is disabled\nHelp Link: https://go.microsoft.com/fwlink/?linkid=2128109\nHost: "+(S&&S.pathname||"_unknown_")+"\nEndpoint: "+i,parsedStack:[]}],r)),l.push(function(e,t,n,a){var i=v(c,"Message"),r=i.data;r.baseType="MessageData";var o=r.baseData;return o.message='AI (Internal): 99 message:"'+("SDK LOAD Failure: Failed to load Application Insights SDK script (See stack for details) ("+n+")").replace(/\"/g,"")+'"',o.properties={endpoint:a},i}(0,0,t,p)),function(e,t){if(JSON){var n=T.fetch;if(n&&!y.useXhr)n(t,{method:N,body:JSON.stringify(e),mode:"cors"});else if(XMLHttpRequest){var a=new XMLHttpRequest;a.open(N,t),a.setRequestHeader("Content-type","application/json"),a.send(JSON.stringify(e))}}}(l,p))}function i(e,t){f||setTimeout(function(){!t&&m.core||a()},500)}var e=function(){var n=l.createElement(k);n.src=h;var e=y[w];return!e&&""!==e||"undefined"==n[w]||(n[w]=e),n.onload=i,n.onerror=a,n.onreadystatechange=function(e,t){"loaded"!==n.readyState&&"complete"!==n.readyState||i(0,t)},n}();y.ld<0?l.getElementsByTagName("head")[0].appendChild(e):setTimeout(function(){l.getElementsByTagName(k)[0].parentNode.appendChild(e)},y.ld||0)}try{m.cookie=l.cookie}catch(p){}function t(e){for(;e.length;)!function(t){m[t]=function(){var e=arguments;g||m.queue.push(function(){m[t].apply(m,e)})}}(e.pop())}var n="track",r="TrackPage",o="TrackEvent";t([n+"Event",n+"PageView",n+"Exception",n+"Trace",n+"DependencyData",n+"Metric",n+"PageViewPerformance","start"+r,"stop"+r,"start"+o,"stop"+o,"addTelemetryInitializer","setAuthenticatedUserContext","clearAuthenticatedUserContext","flush"]),m.SeverityLevel={Verbose:0,Information:1,Warning:2,Error:3,Critical:4};var s=(d.extensionConfig||{}).ApplicationInsightsAnalytics||{};if(!0!==d[I]&&!0!==s[I]){var c="onerror";t(["_"+c]);var u=T[c];T[c]=function(e,t,n,a,i){var r=u&&u(e,t,n,a,i);return!0!==r&&m["_"+c]({message:e,url:t,lineNumber:n,columnNumber:a,error:i}),r},d.autoExceptionInstrumented=!0}return m}(y.cfg);function a(){y.onInit&&y.onInit(n)}(T[t]=n).queue&&0===n.queue.length?(n.queue.push(a),n.trackPageView({})):a()}(window,document,{
src: "https://js.monitor.azure.com/scripts/b/ai.2.min.js", // The SDK URL Source
// name: "appInsights", // Global SDK Instance name defaults to "appInsights" when not supplied
// ld: 0, // Defines the load delay (in ms) before attempting to load the sdk. -1 = block page load and add to head. (default) = 0ms load after timeout,
// useXhr: 1, // Use XHR instead of fetch to report failures (if available),
crossOrigin: "anonymous", // When supplied this will add the provided value as the cross origin attribute on the script tag
// onInit: null, // Once the application insights instance has loaded and initialized this callback function will be called with 1 argument -- the sdk instance (DO NOT ADD anything to the sdk.queue -- As they won't get called)
cfg: { // Application Insights Configuration
  connectionString:"InstrumentationKey=00000000-0000-0000-0000-000000000000;"
}});
</script>
```

> [!NOTE]
> Para mejorar la legibilidad y reducir errores potenciales de JavaScript, todas las opciones de configuración posibles se muestran en una nueva línea en el código del fragmento de código anterior. Si no desea cambiar el valor de una línea comentada, se puede quitar.

Instalación manual:
```javascript
import { ApplicationInsights } from '@microsoft/applicationinsights-web'

const appInsights = new ApplicationInsights({ config: {
  connectionString: 'InstrumentationKey=00000000-0000-0000-0000-000000000000;'
  /* ...Other Configuration Options... */
} });
appInsights.loadAppInsights();
appInsights.trackPageView();
```

# <a name="nodejs"></a>[Node.js](#tab/nodejs)

```javascript
const appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;");
appInsights.start();
```

# <a name="python"></a>[Python](#tab/python)

Se recomienda que los usuarios establezcan la variable de entorno.

Para establecer explícitamente la cadena de conexión:

```python
from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tracer = Tracer(exporter=AzureExporter(connection_string='InstrumentationKey=00000000-0000-0000-0000-000000000000'), sampler=ProbabilitySampler(1.0))
```

---

## <a name="next-steps"></a>Pasos siguientes

Comience en el tiempo de ejecución con:

* [Aplicaciones hospedadas en IIS en máquina virtual de Azure y conjunto de escalado de máquinas virtuales de Azure](./azure-vm-vmss-apps.md)
* [Servidor IIS](./status-monitor-v2-overview.md)
* [Azure Web Apps](./azure-web-apps.md)

Comience en el tiempo de desarrollo con:

* [ASP.NET](./asp-net.md)
* [ASP.NET Core](./asp-net-core.md)
* [Java](./java-in-process-agent.md)
* [Node.js](./nodejs.md)
* [Python](./opencensus-python.md)