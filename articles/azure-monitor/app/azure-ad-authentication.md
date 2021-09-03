---
title: Autenticación de Azure Active Directory para Application Insights (versión preliminar)
description: Aprenda a habilitar la autenticación de Azure Active Directory (Azure AD) para asegurarse de que solo se ingiera telemetría autenticada en los recursos de Application Insights.
ms.topic: conceptual
ms.date: 08/02/2021
ms.openlocfilehash: 3b50948d5dcc408595ccc8434d7fb29c07c68ab0
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747686"
---
# <a name="azure-ad-authentication-for-application-insights-preview"></a>Autenticación de Azure Active Directory para Application Insights (versión preliminar)
Application Insights ahora admite la autenticación de Azure Active Directory (Azure AD). Mediante Azure AD, ahora puede asegurarse de que solo se ingiere la telemetría autenticada en los recursos de Application Insights. 

Normalmente, el uso de varios sistemas de autenticación puede ser complicado y suponer un riesgo, ya que es difícil administrar las credenciales a gran escala. Ahora puede optar por excluirse de la autenticación local y asegurarse de que solo la telemetría que se autentica exclusivamente mediante [identidades administradas](../../active-directory/managed-identities-azure-resources/overview.md) y [Azure Active Directory](../../active-directory/fundamentals/active-directory-whatis.md) se ingiera en el recurso de Application Insights. Esta característica es un paso para mejorar la seguridad y fiabilidad de la telemetría que se usa para tomar decisiones empresariales y operativas críticas (alertas, escalado automático, etc.).

> [!IMPORTANT]
> Actualmente, la autenticación de Azure AD se encuentra en VERSIÓN PRELIMINAR.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

A continuación se muestran los SDK o escenarios que no se admiten en la versión preliminar pública:
- [SDK de Java 2.x para Application Insights](java-2x-agent.md): la autenticación de Azure AD solo está disponible para el agente de Java, versión 3.2.0 como mínimo, para Application Insights . 
- [SDK web de JavaScript para Application Insights](javascript.md). 
- [SDK de Python de OpenCensus para Application Insights](opencensus-python.md) con Python versión 3.4 y 3.5.
- Los [certificados o secretos basados en Azure AD](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md) no se recomiendan. En lugar de ello, use las identidades administradas. 
- De forma predeterminada, la supervisión sin código (para idiomas) para App Service, los conjuntos de escalado de máquinas virtuales o máquinas virtuales, Azure Functions etc.
- [Pruebas de disponibilidad](availability-overview.md).
- [Profiler](profiler-overview.md).

## <a name="prerequisites-to-enable-azure-ad-authentication-ingestion"></a>Requisitos previos para habilitar la ingesta de autenticación de Azure AD

- Conocimientos de:
    - [Identidad administrada](../../active-directory/managed-identities-azure-resources/overview.md). 
    - [Entidad de servicio](../../active-directory/develop/howto-create-service-principal-portal.md).
    - [Asignación de roles de Azure](../../role-based-access-control/role-assignments-portal.md). 
- Tiene un rol "Propietario" en el grupo de recursos para conceder acceso mediante [roles integrados de Azure](../../role-based-access-control/built-in-roles.md).

## <a name="configuring-and-enabling-azure-ad-based-authentication"></a>Configuración y habilitación de la autenticación basada en Azure AD 

1. Cree una identidad, si aún no la tiene, mediante una identidad administrada o una entidad de servicio:

    1. Mediante una identidad administrada (recomendado):

        [Configure una identidad administrada para el servicio de Azure](../../active-directory/managed-identities-azure-resources/services-support-managed-identities.md) (VM, App Service etc.).

    1. Mediante una entidad de servicio (no recomendado):

        Para obtener más información sobre cómo crear una entidad de servicio y una aplicación de Azure AD que puedan acceder a los recursos, consulte [Creación de una entidad de servicio](../../active-directory/develop/howto-create-service-principal-portal.md).

1. Asignación de un rol al servicio de Azure. 

    Siga los pasos descritos en [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md) para agregar el rol "Supervisión del publicador de métricas" desde el recurso de destino de Application Insights al recurso de Azure desde el que se envía la telemetría. 

    > [!NOTE]
    > Aunque el rol "Supervisión del publicador de métricas" indica métricas, publicará toda la telemetría en el recurso de Application Insights.

1. Siga las instrucciones de configuración por idioma a continuación.

### <a name="aspnet-and-net"></a>[ASP.NET y .NET](#tab/net)

> [!NOTE]
> La compatibilidad con Azure AD en el SDK de .NET de Application Insights se incluye a partir de la [versión 2.18-Beta3](https://www.nuget.org/packages/Microsoft.ApplicationInsights/2.18.0-beta3).

El SDK de .NET de Application Insights admite las clases de credenciales proporcionadas por [Azure Identity](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/identity/Azure.Identity#credential-classes).

- Se recomienda `DefaultAzureCredential` para el desarrollo local.
- Se recomienda `ManagedIdentityCredential` para identidades administradas asignadas por el sistema y asignadas por el usuario.
    - En el caso de las asignadas por el sistema, use el constructor predeterminado sin parámetros.
    - En el caso de los asignados por el usuario, proporcione el valor de clientId al constructor.
- Se recomienda `ClientSecretCredential` para las entidades de servicio. 
    - Proporcione los valores de tenantId, clientId y clientSecret al constructor.

A continuación se muestra un ejemplo de creación y configuración manual de `TelemetryConfiguration` mediante .NET:

```csharp
var config = new TelemetryConfiguration
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
}
var credential = new DefaultAzureCredential();
config.SetAzureTokenCredential(credential);

```

A continuación se muestra un ejemplo de cómo configurar `TelemetryConfiguration` mediante ASP.NET Core:
```csharp
services.Configure<TelemetryConfiguration>(config =>
{
       var credential = new DefaultAzureCredential();
       config.SetAzureTokenCredential(credential);
});
services.AddApplicationInsightsTelemetry(new ApplicationInsightsServiceOptions
{
    ConnectionString = "InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/"
});
```
### <a name="nodejs"></a>[Node.js](#tab/nodejs)
 
> [!NOTE]
> La compatibilidad con Azure AD en Node-JS de Application Insights se incluye a partir de la [versión 2.1.0-beta.1](https://www.npmjs.com/package/applicationinsights/v/2.1.0-beta.1).

Node.JS de Application Insights admite las clases de credenciales proporcionadas por [Azure Identity](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/identity/identity#credential-classes).

#### <a name="defaultazurecredential"></a>DefaultAzureCredential

```javascript
let appInsights = require("applicationinsights");
import { DefaultAzureCredential } from "@azure/identity"; 
 
const credential = new DefaultAzureCredential();
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

#### <a name="clientsecretcredential"></a>ClientSecretCredential

```javascript
let appInsights = require("applicationinsights");
import { ClientSecretCredential } from "@azure/identity"; 
 
const credential = new ClientSecretCredential(
    "<YOUR_TENANT_ID>",
    "<YOUR_CLIENT_ID>",
    "<YOUR_CLIENT_SECRET>"
  );
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").start();
appInsights.defaultClient.aadTokenCredential = credential;

```

### <a name="java"></a>[Java](#tab/java)

> [!NOTE]
> La compatibilidad con Azure AD en el agente de Java de Application Insights se incluye a partir de la version [Java 3.2.0-BETA](https://github.com/microsoft/ApplicationInsights-Java/releases/tag/3.2.0-BETA). 

1. [Configure la aplicación con el agente de Java.](java-in-process-agent.md#quickstart)

    > [!IMPORTANT]
    > Use la cadena de conexión completa que incluye “IngestionEndpoint” al configurar la aplicación con el agente de Java. Por ejemplo: `InstrumentationKey=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX;IngestionEndpoint=https://XXXX.applicationinsights.azure.com/`.

    > [!NOTE]
    >  Para obtener más información sobre cómo migrar desde el SDK 2.X al agente de Java 3.X, consulte [Actualización del SDK de Java de Application Insights 2.x](java-standalone-upgrade-from-2x.md).

1. Agregue la configuración json al archivo de configuración ApplicationInsights.json dependiendo de la autenticación que utilice. Se recomienda que los usuarios usen identidades administradas.

#### <a name="system-assigned-managed-identity"></a>Identidad administrada asignada por el sistema

A continuación se muestra un ejemplo sobre cómo configurar el agente de Java para usar la identidad administrada asignada por el sistema para la autenticación con Azure AD.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "SAMI" 
    } 
  } 
} 
```

#### <a name="user-assigned-managed-identity"></a>Identidad administrada asignada por el usuario

A continuación se muestra un ejemplo sobre cómo configurar el agente de Java para usar la identidad administrada asignada por el usuario para la autenticación con Azure AD.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint", 
  "preview": { 
    "authentication": { 
      "enabled": true, 
      "type": "UAMI", 
      "clientId":"<USER-ASSIGNED MANAGED IDENTITY CLIENT ID>" 
    } 
  }  
} 
```
:::image type="content" source="media/azure-ad-authentication/user-assigned-managed-identity.png" alt-text="Captura de pantalla de la identidad administrada asignada por el usuario." lightbox="media/azure-ad-authentication/user-assigned-managed-identity.png":::

#### <a name="client-secret"></a>Secreto del cliente

A continuación se muestra un ejemplo sobre cómo configurar el agente de Java para usar la entidad de servicio para la autenticación con Azure AD. Se recomienda a los usuarios que usen este tipo de autenticación solo durante el desarrollo. El objetivo final de agregar la característica de autenticación es eliminar los secretos.

```JSON
{ 
  "connectionString": "App Insights Connection String with IngestionEndpoint",
   "preview": { 
        "authentication": { 
        "enabled": true, 
        "type": "CLIENTSECRET", 
        "clientId":"<YOUR CLIENT ID>", 
        "clientSecret":"<YOUR CLIENT SECRET>", 
        "tenantId":"<YOUR TENANT ID>" 
    } 
  } 
} 
```
:::image type="content" source="media/azure-ad-authentication/client-secret-tenant-id.png" alt-text="Captura de pantalla del secreto de cliente con tenantID y ClientID." lightbox="media/azure-ad-authentication/client-secret-tenant-id.png":::

:::image type="content" source="media/azure-ad-authentication/client-secret-cs.png" alt-text="Captura de pantalla del secreto de cliente con el secreto de cliente." lightbox="media/azure-ad-authentication/client-secret-cs.png":::

### <a name="python"></a>[Python](#tab/python)

> [!NOTE]
> La autenticación de Azure AD solo está disponible para Python v2.7, v3.6 y v3.7. La compatibilidad con Azure AD en el SDK de Python de Opencensus para Application Insights se incluye a partir de la versión beta [opencensus-ext-azure 1.1b0](https://pypi.org/project/opencensus-ext-azure/1.1b0/).


Construya las [credenciales](/python/api/overview/azure/identity-readme?view=azure-python#credentials) adecuadas y páselas al constructor del exportador de Azure Monitor. Asegúrese de que la cadena de conexión está configurada con la clave de instrumentación y el punto de conexión de ingesta del recurso.

A continuación se muestran los siguientes tipos de autenticación que los exportadores de Azure Monitor para Opencensus admiten. Se recomienda usar identidades administradas en entornos de producción.


#### <a name="system-assigned-managed-identity"></a>Identidad administrada asignada por el sistema

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential()
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="user-assigned-managed-identity"></a>Identidad administrada asignada por el usuario

```python
from azure.identity import ManagedIdentityCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

credential = ManagedIdentityCredential(client_id="<client-id>")
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...

```

#### <a name="client-secret"></a>Secreto del cliente

```python
from azure.identity import ClientSecretCredential

from opencensus.ext.azure.trace_exporter import AzureExporter
from opencensus.trace.samplers import ProbabilitySampler
from opencensus.trace.tracer import Tracer

tenant_id = "<tenant-id>"
client_id = "<client-id"
client_secret = "<client-secret>"

credential = ClientSecretCredential(tenant_id=tenant_id, client_id=client_id, client_secret=client_secret)
tracer = Tracer(
    exporter=AzureExporter(credential=credential, connection_string="InstrumentationKey=<your-instrumentation-key>;IngestionEndpoint=<your-ingestion-endpoint>"),
    sampler=ProbabilitySampler(1.0)
)
...
```
---

## <a name="disable-local-authentication"></a>Deshabilitación de la autenticación local

Después de habilitar la autenticación de Azure AD, puede optar por deshabilitar la autenticación local. Esto le permitirá ingerir telemetría autenticada exclusivamente mediante Azure AD y afectará al acceso a los datos (por ejemplo, a través de claves de API). 

Puede deshabilitar la autenticación local mediante Azure Portal, la directiva de Azure o mediante programación.

### <a name="azure-portal"></a>Azure portal

1.  En el recurso de Application Insights, seleccione **Propiedades** en el título *Configurar* del menú de la izquierda. A continuación, seleccione **Habilitado (haga clic para cambiar)** si la autenticación local está habilitada. 

    :::image type="content" source="./media/azure-ad-authentication/enabled.png" alt-text="Captura de pantalla de Propiedades con el título *Configurar* seleccionado y el botón de autenticación local Habilitado (haga clic para cambiar).":::

1. Seleccione **Deshabilitado** y aplique los cambios. 

    :::image type="content" source="./media/azure-ad-authentication/disable.png" alt-text="Captura de pantalla de la autenticación local con el botón habilitado o deshabilitado resaltado.":::

1. Una vez que el recurso haya deshabilitado la autenticación local, verá la información correspondiente en el panel **Información general**.

    :::image type="content" source="./media/azure-ad-authentication/overview.png" alt-text="Captura de pantalla de la pestaña Información general con Deshabilitado (haga clic para cambiar) resaltado.":::

### <a name="azure-policy"></a>Azure Policy 

La directiva de Azure para "DisableLocalAuth" denegará a los usuarios la creación de un nuevo recurso de Application Insights sin esta configuración de propiedad en "true". El nombre de la directiva es "Los componentes de Application Insights deben bloquear la ingesta de autenticación que no es de ADD".

Para aplicar esta directiva a la suscripción, [cree una nueva asignación de directiva y asigne la directiva](../..//governance/policy/assign-policy-portal.md).

A continuación se muestra la definición de la plantilla de directiva:
```JSON
{
    "properties": {
        "displayName": "Application Insights components should block non-AAD auth ingestion",
        "policyType": "BuiltIn",
        "mode": "Indexed",
        "description": "Improve Application Insights security by disabling log ingestion that are not AAD-based.",
        "metadata": {
            "version": "1.0.0",
            "category": "Monitoring"
        },
        "parameters": {
            "effect": {
                "type": "String",
                "metadata": {
                    "displayName": "Effect",
                    "description": "The effect determines what happens when the policy rule is evaluated to match"
                },
                "allowedValues": [
                    "audit",
                    "deny",
                    "disabled"
                ],
                "defaultValue": "audit"
            }
        },
        "policyRule": {
            "if": {
                "allOf": [
                    {
                        "field": "type",
                        "equals": "Microsoft.Insights/components"
                    },
                    {
                        "field": "Microsoft.Insights/components/DisableLocalAuth",
                        "notEquals": "true"                        
                    }
                ]
            },
            "then": {
                "effect": "[parameters('effect')]"
            }
        }
    }
}
```

### <a name="programmatic-enablement"></a>Habilitación mediante programación 

La propiedad `DisableLocalAuth` se usa para deshabilitar cualquier autenticación local en el recurso de Application Insights. Cuando se establece en `true`, esta propiedad exige que la autenticación de Azure AD se use para todos los accesos.

A continuación se muestra una plantilla de Azure Resource Manager de ejemplo que puede usar para crear un recurso de Application Insights basado en área de trabajo con la autenticación local deshabilitada.

```JSON 
{
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "name": {
            "type": "string"
        },
        "type": {
            "type": "string"
        },
        "regionId": {
            "type": "string"
        },
        "tagsArray": {
            "type": "object"
        },
        "requestSource": {
            "type": "string"
        },
        "workspaceResourceId": {
            "type": "string"
        },
        "disableLocalAuth": {
            "type": "bool"
        }
     
    },
    "resources": [
        {
        "name": "[parameters('name')]",
        "type": "microsoft.insights/components",
        "location": "[parameters('regionId')]",
        "tags": "[parameters('tagsArray')]",
        "apiVersion": "2020-02-02-preview",
        "dependsOn": [],
        "properties": {
            "Application_Type": "[parameters('type')]",
            "Flow_Type": "Redfield",
            "Request_Source": "[parameters('requestSource')]",
            "WorkspaceResourceId": "[parameters('workspaceResourceId')]",
            "DisableLocalAuth": "[parameters('disableLocalAuth')]"
            }
    }
 ]
}

```

## <a name="troubleshooting"></a>Solución de problemas

En esta sección se proporcionan distintos escenarios de solución de problemas y pasos que los usuarios pueden seguir para resolver cualquier problema antes de generar una incidencia de soporte técnico. 

### <a name="ingestion-http-errors"></a>Errores HTTP de ingesta

El servicio de ingesta devolverá errores específicos, independientemente del lenguaje del SDK. El tráfico de red se puede recopilar mediante una herramienta como Fiddler. Debe filtrar el tráfico al punto de conexión de ingesta establecido en la cadena de conexión.

#### <a name="http11-400-authentication-not-support"></a>No se admite la autenticación HTTP/1.1 400 

Esto indica que el recurso de Application Insights se ha configurado solo para Azure AD, pero el SDK no se ha configurado correctamente y envía a la API incorrecta.

> [!NOTE]
>  "v2/track" no admite Azure AD. Cuando el SDK esté configurado correctamente, la telemetría se enviará a "v2.1/track".

Los siguientes pasos deben ser revisar la configuración del SDK.

#### <a name="http11-401-authorization-required"></a>Se requiere autorización de HTTP/1.1 401

Esto indica que el SDK se ha configurado correctamente, pero no pudo adquirir un token válido. Esto puede indicar un problema con Azure Active Directory.

Los pasos siguientes deben consistir en identificar las excepciones en los registros del SDK o los errores de red de la identidad de Azure.

#### <a name="http11-403-unauthorized"></a>HTTP/1.1 403 no autorizado 

Esto indica que el SDK se ha configurado con credenciales que no tienen permiso para el recurso o la suscripción de Application Insights.

Los pasos siguientes deben consistir en revisar el control de acceso del recurso de Application Insights. El SDK debe configurarse con una credencial a la que se le haya concedido el rol "Supervisión del publicador de métricas".

### <a name="language-specific-troubleshooting"></a>Solución de problemas específicos del lenguaje

### <a name="aspnet-and-net"></a>[ASP.NET y .NET](#tab/net)

#### <a name="event-source"></a>Origen de eventos

El SDK de .NET de Application Insights emite registros de errores mediante el origen del evento. Para obtener más información sobre la recopilación de registros de origen del evento, visite Solución de problemas de [Solución de problemas cuando no hay datos: Application Insights para .NET/.NET Core](asp-net-troubleshoot-no-data.md#PerfView).

Si el SDK no puede obtener un token, el mensaje de excepción se registra como: “No se pudo obtener el token de AAD. Mensaje de error: ”

### <a name="nodejs"></a>[Node.js](#tab/nodejs)

Los registros internos podrían activarse mediante la siguiente configuración. Una vez habilitados, los registros de errores se mostrarán en la consola, incluido cualquier error relacionado con la integración de Azure AD. Por ejemplo, si no se genera el token cuando se proporcionan credenciales incorrectas o se producen errores cuando el punto de conexión de ingesta no se puede autenticar con las credenciales proporcionadas.

```javascript
let appInsights = require("applicationinsights");
appInsights.setup("InstrumentationKey=00000000-0000-0000-0000-000000000000;IngestionEndpoint=https://xxxx.applicationinsights.azure.com/").setInternalLogging(true, true);
```

### <a name="java"></a>[Java](#tab/java)

#### <a name="http-traffic"></a>Tráfico HTTP

Puede inspeccionar el tráfico de red mediante una herramienta como Fiddler. Para habilitar el tráfico para tunelización a través de Fiddler, agregue la siguiente configuración de proxy en el archivo de configuración:

```JSON
"proxy": {
"host": "localhost",
"port": 8888
}
```

O bien, agregue los siguientes argumentos de JVM al ejecutar la aplicación:`-Djava.net.useSystemProxies=true -Dhttps.proxyHost=localhost -Dhttps.proxyPort=8888`

Si Azure AD está habilitado en el agente, el tráfico saliente incluirá el encabezado HTTP “Autorización”.


#### <a name="401-unauthorized"></a>401 No autorizado 

Si se aparece el siguiente mensaje WARN en el archivo de registro `WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 401, please check your credentials`, indica que el agente no estaba enviando datos de telemetría correctamente. Probablemente no haya habilitado la autenticación de Azure AD en el agente, pero el recurso de Application Insights está configurado con `DisableLocalAuth: true`. Asegúrese de que va a pasar una credencial válida y de que tiene permiso para acceder al recurso de Application Insights.


Si usa Fiddler, es posible que vea el siguiente encabezado de respuesta: `HTTP/1.1 401 Unauthorized - please provide the valid authorization token`.


#### <a name="credentialunavailableexception"></a>CredentialUnavailableException

Si se ve la siguiente excepción en el archivo de registro `com.azure.identity.CredentialUnavailableException: ManagedIdentityCredential authentication unavailable. Connection to IMDS endpoint cannot be established`, indica que el agente no estaba adquiriendo correctamente el token de acceso y el motivo probable podría ser que ha proporcionado un `clientId` no válido en la configuración de la identidad administrada asignada por el usuario.


#### <a name="failed-to-send-telemetry"></a>No se pudo enviar telemetría

Si aparece el siguiente mensaje WARN en el archivo de registro, `WARN c.m.a.TelemetryChannel - Failed to send telemetry with status code: 403, please check your credentials`, indica que el agente no estaba enviando datos de telemetría correctamente. Esto puede deberse a que las credenciales proporcionadas no conceden el acceso para ingerir la telemetría en el componente.

Si usa Fiddler, es posible que vea el siguiente encabezado de respuesta: `HTTP/1.1 403 Forbidden - provided credentials do not grant the access to ingest the telemetry into the component`.

La causa principal podría ser una de las siguientes:
- Ha creado el recurso con la identidad administrada asignada por el sistema habilitada o puede haber asociado la identidad asignada por el usuario al recurso, pero olvidó agregar el rol `Monitoring Metrics Publisher` al recurso (si usa SAMI) o a la identidad asignada por el usuario (si usa UAMI).
- Ha proporcionado las credenciales adecuadas para obtener los tokens de acceso, pero las credenciales no pertenecen al recurso de Application Insights. Asegúrese de que ve el recurso (máquina virtual, App Service, etc.) o la identidad asignada por el usuario con roles `Monitoring Metrics Publisher` en el recurso de Application Insights.

#### <a name="invalid-tenantid"></a>TenantId no válido

Si se ve la siguiente excepción en el archivo de registro `com.microsoft.aad.msal4j.MsalServiceException: Specified tenant identifier <TENANT-ID> is neither a valid DNS name, nor a valid external domain.`, indica que el agente no estaba adquiriendo correctamente el token de acceso y el motivo probable podría ser que ha proporcionado un `tenantId` no válido o erróneo en la configuración del secreto de cliente.

#### <a name="invalid-client-secret"></a>Secreto de cliente no válido

Si se ve la siguiente excepción en el archivo de registro `com.microsoft.aad.msal4j.MsalServiceException: Invalid client secret is provided`, indica que el agente no estaba adquiriendo correctamente el token de acceso y el motivo probable podría ser que ha proporcionado un `clientSecret` no válido en la configuración del secreto de cliente.


#### <a name="invalid-clientid"></a>ClientId no válido

Si se ve la siguiente excepción en el archivo de registro `com.microsoft.aad.msal4j.MsalServiceException: Application with identifier <CLIENT_ID> was not found in the directory`, indica que el agente no estaba adquiriendo correctamente el token de acceso y el motivo probable podría ser que ha proporcionado un “clientId” no válido o erróneo en la configuración del secreto de cliente.

 Esto puede pasar si el administrador del inquilino no es el que ha instalado el administrador del inquilino o no ha recibido el consentimiento de ningún usuario del inquilino. Es posible que haya enviado la solicitud de autenticación al inquilino incorrecto.

### <a name="python"></a>[Python](#tab/python)

#### <a name="error-starts-with-credential-error-with-no-status-code"></a>El error comienza por “error de credencial” (sin código de estado)

Hay algo incorrecto relacionado con la credencial que está usando y el cliente no puede obtener un token para la autorización. Este problema normalmente se debe a la falta de los datos necesarios para el estado. Un ejemplo sería pasar un elemento ManagedIdentityCredential del sistema, pero el recurso no está configurado para usar la identidad administrada por el sistema.

#### <a name="error-starts-with-authentication-error-with-no-status-code"></a>El error comienza por “error de autenticación” (sin código de estado)

El cliente no pudo autenticarse con la credencial dada. Normalmente se produce cuando la credencial usada no tiene asignaciones de roles correctas.

#### <a name="im-getting-a-status-code-400-in-my-error-logs"></a>Obtengo un código de estado 400 en los registros de errores

Probablemente falte una credencial o la credencial esté establecida en `None`, pero el recurso de Application Insights está configurado con `DisableLocalAuth: true`. Asegúrese de que va a pasar una credencial válida y de que tiene permiso para acceder al recurso de Application Insights.

#### <a name="im-getting-a-status-code-403-in-my-error-logs"></a>Obtengo un código de estado 403 en los registros de errores

Normalmente se produce cuando las credenciales proporcionadas no conceden acceso a la telemetría de ingesta para el recurso de Application Insights. Asegúrese de que el recurso de IA tiene las asignaciones de roles correctas.

---
## <a name="next-steps"></a>Pasos siguientes
* [Supervisión de la telemetría en el portal](overview-dashboard.md).
* [Diagnósticos con Live Metrics Stream](live-stream.md).
