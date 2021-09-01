---
title: Colección de direcciones IP de Azure Application Insights | Microsoft Docs
description: Descripción de la forma en que Application Insights controla las direcciones  IP y la geolocalización.
ms.topic: conceptual
ms.date: 09/23/2020
ms.custom: devx-track-js, devx-track-azurepowershell
ms.openlocfilehash: 28b3fb6242d496be7962961d36db3a85c6187d8e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745891"
---
# <a name="geolocation-and-ip-address-handling"></a>Administración de la ubicación geográfica y la dirección IP

En este artículo se explica cómo funcionan la búsqueda de geolocalización y el control de direcciones IP en Application Insights, además de cómo se modifica el comportamiento predeterminado.

## <a name="default-behavior"></a>Comportamiento predeterminado

De forma predeterminada, las direcciones IP se recopilan temporalmente en Application Insights, pero no se almacenan. El proceso básico es el siguiente:

Cuando los datos de telemetría se envían a Azure, Application Insights usa la dirección IP para realizar una búsqueda de geolocalización mediante [GeoLite2 de MaxMind](https://dev.maxmind.com/geoip/geoip2/geolite2/). Application Insights usa los resultados de esta búsqueda para rellenar los campos `client_City`, `client_StateOrProvince` y `client_CountryOrRegion`. Luego, la dirección se descarta y `0.0.0.0` se escribe en el campo `client_IP`. 

Estos son los tipos de telemetría:

* Telemetría del explorador: Application Insights recopila la dirección IP del emisor. El punto de conexión de ingesta calcula la dirección IP.
* Telemetría del servidor: el módulo de telemetría de Application Insights recopila temporalmente la dirección IP del cliente. La dirección IP no se recopila localmente cuando se establece el encabezado `X-Forwarded-For`. Cuando la lista entrante de direcciones IP tiene más de un elemento, se usa la última dirección IP para rellenar los campos de geolocalización.

Este comportamiento es así de forma predeterminada, ya que le ayudará a evitar la recopilación innecesaria de datos personales. Siempre que sea posible, se recomienda evitar la recopilación de datos personales. 

> [!NOTE]
> Aunque el valor predeterminado es no recopilar direcciones IP, este comportamiento se puede invalidar. Se recomienda comprobar que la recopilación no infringe ningún requisito de cumplimiento ni normativa local. 
>
> Para más información sobre cómo controlar datos personales en Application Insights, consulte la [guía de datos personales](../logs/personal-data-mgmt.md).


## <a name="storage-of-ip-address-data"></a>Almacenamiento de datos de dirección IP

Para habilitar la recopilación y el almacenamiento de direcciones IP, la propiedad `DisableIpMasking` del componente de Application Insights debe establecerse en `true`. Esta propiedad se puede establecer mediante las plantillas de Azure Resource Manager o llamando a la API REST. 

### <a name="azure-resource-manager-template"></a>Plantilla del Administrador de recursos de Azure

```json
{
       "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/microsoft.insights/components/<resource-name>",
       "name": "<resource-name>",
       "type": "microsoft.insights/components",
       "location": "westcentralus",
       "tags": {
              
       },
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "Flow_Type": "Redfield",
              "Request_Source": "IbizaAIExtension",
              // ...
              "DisableIpMasking": true
       }
}
```

### <a name="portal"></a>Portal

Si solo necesita modificar el comportamiento de un recurso de Application Insights, use Azure Portal. 

1. Vaya al recurso de Application Insights y seleccione **Automation** > **Exportar plantilla**. 

2. Seleccione **Implementar**.

    ![Captura de pantalla que muestra el botón Implementar resaltado en rojo.](media/ip-collection/deploy.png)

3. Seleccione **Editar plantilla**.

    ![Captura de pantalla que muestra el botón Editar resaltado en rojo, junto con una advertencia sobre el grupo de recursos.](media/ip-collection/edit-template.png)

    > [!NOTE]
    > Si aparece el siguiente error (como se muestra en la captura de pantalla), puede resolverlo: "El grupo de recursos está en una ubicación que no es compatible con uno o varios recursos de la plantilla. Elija otro grupo de recursos." Seleccione temporalmente otro grupo de recursos en la lista desplegable y, después, vuelva a seleccionar el grupo de recursos original.

4. En la plantilla JSON, busque `properties` dentro de `resources`, agregue una coma al último campo JSON y, después, agregue la siguiente línea nueva: `"DisableIpMasking": true`. Después, seleccione **Guardar**.

    ![Captura de pantalla que muestra la incorporación de una coma y una nueva línea después de la propiedad en el origen de la solicitud.](media/ip-collection/save.png)

5. Seleccione **Revisar y crear** > **Crear**.

    > [!NOTE]
    > Si ve "Error en la implementación", busque en los detalles de implementación el que tenga el tipo `microsoft.insights/components` y compruebe el estado. Si se ejecuta correctamente, significa que se han implementado los cambios realizados en `DisableIpMasking`.

6. Una vez finalizada la implementación, se registrarán nuevos datos de telemetría.

    Si vuelve a seleccionar y editar la plantilla, solo verá la plantilla predeterminada sin la propiedad recién agregada. Si no ve los datos de la dirección IP y quiere confirmar que `"DisableIpMasking": true` está establecido, ejecute los siguientes comandos de PowerShell:
    
    ```powershell
    # Replace `Fabrikam-dev` with the appropriate resource and resource group name.
    # If you aren't using Azure Cloud Shell, you need to connect to your Azure account
    # Connect-AzAccount 
    $AppInsights = Get-AzResource -Name 'Fabrikam-dev' -ResourceType 'microsoft.insights/components' -ResourceGroupName 'Fabrikam-dev'
    $AppInsights.Properties
    ```
    
    Como resultado, se devuelve una lista de propiedades. Una de las propiedades debe ser `DisableIpMasking: true`. Si ejecuta los comandos de PowerShell antes de implementar la nueva propiedad con Azure Resource Manager, la propiedad no existirá.

### <a name="rest-api"></a>API REST

La carga útil de la [API de Rest](/rest/api/azure/) para realizar las mismas modificaciones es la siguiente:

```
PATCH https://management.azure.com/subscriptions/<sub-id>/resourceGroups/<rg-name>/providers/microsoft.insights/components/<resource-name>?api-version=2018-05-01-preview HTTP/1.1
Host: management.azure.com
Authorization: AUTH_TOKEN
Content-Type: application/json
Content-Length: 54

{
       "location": "<resource location>",
       "kind": "web",
       "properties": {
              "Application_Type": "web",
              "DisableIpMasking": true
       }
}
```

## <a name="telemetry-initializer"></a>Inicializador de telemetría

Si necesita una alternativa más flexible que `DisableIpMasking`, puede usar un [inicializador de telemetría](./api-filtering-sampling.md#addmodify-properties-itelemetryinitializer) para copiar toda la dirección IP, o una parte de ella, en un campo personalizado. 

# <a name="net"></a>[.NET](#tab/net)

### <a name="aspnet-or-aspnet-core"></a>ASP.NET o ASP.NET Core

```csharp
using Microsoft.ApplicationInsights.Channel;
using Microsoft.ApplicationInsights.DataContracts;
using Microsoft.ApplicationInsights.Extensibility;

namespace MyWebApp
{
    public class CloneIPAddress : ITelemetryInitializer
    {
        public void Initialize(ITelemetry telemetry)
        {
            ISupportProperties propTelemetry = telemetry as ISupportProperties;

            if (propTelemetry !=null && !propTelemetry.Properties.ContainsKey("client-ip"))
            {
                string clientIPValue = telemetry.Context.Location.Ip;
                propTelemetry.Properties.Add("client-ip", clientIPValue);
            }
        }
    } 
}
```

> [!NOTE]
> Si no puede acceder a `ISupportProperties`, asegúrese de que ejecuta la versión estable más reciente del SDK de Application Insights. `ISupportProperties` está pensado para valores de cardinalidad alta. `GlobalProperties` es más adecuado para los valores de cardinalidad baja, como el nombre de la región y el nombre del entorno.

### <a name="enable-the-telemetry-initializer-for-aspnet"></a>Habilitación del inicializador de datos de telemetría para ASP.NET

```csharp
using Microsoft.ApplicationInsights.Extensibility;


namespace MyWebApp
{
    public class MvcApplication : System.Web.HttpApplication
    {
        protected void Application_Start()
        {
              //Enable your telemetry initializer:
              TelemetryConfiguration.Active.TelemetryInitializers.Add(new CloneIPAddress());
        }
    }
}

```

### <a name="enable-the-telemetry-initializer-for-aspnet-core"></a>Habilitación del inicializador de datos de telemetría para ASP.NET Core

Un inicializador de telemetría se puede crear de la misma forma en ASP.NET Core y en ASP.NET. Para habilitar el inicializador, use el ejemplo siguiente como referencia:

```csharp
 using Microsoft.ApplicationInsights.Extensibility;
 using CustomInitializer.Telemetry;
 public void ConfigureServices(IServiceCollection services)
{
    services.AddSingleton<ITelemetryInitializer, CloneIPAddress>();
}
```
# <a name="nodejs"></a>[Node.js](#tab/nodejs)

### <a name="nodejs"></a>Node.js

```javascript
appInsights.defaultClient.addTelemetryProcessor((envelope) => {
    const baseData = envelope.data.baseData;
    if (appInsights.Contracts.domainSupportsProperties(baseData)) {
        const ipAddress = envelope.tags[appInsights.defaultClient.context.keys.locationIp];
        if (ipAddress) {
            baseData.properties["client-ip"] = ipAddress;
        }
    }
});
```
# <a name="client-side-javascript"></a>[JavaScript del lado cliente](#tab/javascript)

### <a name="client-side-javascript"></a>JavaScript del lado cliente

A diferencia de los SDK del servidor, el SDK de JavaScript del cliente no calcula ninguna dirección IP. De forma predeterminada, el cálculo de la dirección IP para la telemetría del cliente se produce en el punto de conexión de ingesta de Azure. 

Si quiere calcular la dirección IP directamente en el cliente, necesitará agregar su propia lógica personalizada y usar el resultado para establecer la etiqueta `ai.location.ip`. Cuando se establece `ai.location.ip`, el punto de conexión de ingesta no realiza el cálculo de la dirección IP y la dirección IP proporcionada se usa para la búsqueda de geolocalización. En este escenario, la dirección IP sigue siendo cero de forma predeterminada. 

Para conservar la dirección IP completa calculada a partir de su lógica personalizada, se puede usar un inicializador de telemetría que copie los datos de la dirección IP que proporcionó en `ai.location.ip` en un campo personalizado independiente. Sin embargo, de nuevo, a diferencia de los SDK del servidor, el SDK de cliente no calculará automáticamente la dirección si no puede usar bibliotecas de terceros o su propia lógica personalizada.    


```javascript
appInsights.addTelemetryInitializer((item) => {
    const ipAddress = item.tags && item.tags["ai.location.ip"];
    if (ipAddress) {
        item.baseData.properties = {
            ...item.baseData.properties,
            "client-ip": ipAddress
        };
    }
});

```  

Si los datos del cliente atraviesan un proxy antes de reenviarse al punto de conexión de ingesta, el cálculo de la dirección IP podría mostrar la dirección IP del proxy, no la del cliente. 

---

### <a name="view-the-results-of-your-telemetry-initializer"></a>Ver los resultados del inicializador de telemetría

Si envía el tráfico nuevo al sitio y espera unos minutos, puede ejecutar una consulta para confirmar que la colección funciona:

```kusto
requests
| where timestamp > ago(1h) 
| project appName, operation_Name, url, resultCode, client_IP, customDimensions.["client-ip"]
```

Las direcciones IP recién recopiladas aparecerán en la columna `customDimensions_client-ip`. La columna `client-ip` predeterminada seguirá teniendo los cuatro octetos establecidos en cero. 

Si realiza la prueba desde localhost y el valor de `customDimensions_client-ip` es `::1`, este valor es el comportamiento esperado. El valor `::1` representa la dirección de bucle invertido en IPv6. Es equivalente a `127.0.0.1` en IPv4.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga más información sobre la [recopilación de datos personales](../logs/personal-data-mgmt.md) en Application Insights.

* Obtenga más información sobre cómo funciona la [recopilación de direcciones IP](https://apmtips.com/posts/2016-07-05-client-ip-address/) en Application Insights. Este artículo es una entrada de blog externa antigua que escribió uno de nuestros ingenieros. Es anterior al comportamiento predeterminado actual, donde la dirección IP se registra como `0.0.0.0`, pero profundiza en el inicializador de telemetría integrado.
