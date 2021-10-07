---
title: Habilitación de la autenticación en una API web mediante Azure Active Directory B2C
description: En este artículo se describe cómo usar Azure Active Directory B2C para proteger una API web.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: a67cdd9ba92e3c78c5cb29a827cf537ba2e372ae
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124740200"
---
# <a name="enable-authentication-in-your-own-web-api-by-using-azure-ad-b2c"></a>Habilitación de la autenticación en su propia API web mediante Azure Active Directory B2C

Para autorizar el acceso a una API web, atienda solo las solicitudes que incluyen un token de acceso válido emitido por Azure Active Directory B2C (Azure AD B2C). En este artículo se muestra cómo habilitar la autorización de Azure AD B2C para la API web. Después de completar los pasos de este artículo, solo los usuarios que obtengan un token de acceso válido estarán autorizados para llamar a los puntos de conexión de la API web.  

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, lea uno de los siguientes artículos, en los que se explica cómo configurar la autenticación para aplicaciones que llaman a las API web. A continuación, siga los pasos descritos en este artículo para reemplazar la API web de ejemplo por su propia API web.   

- [Configuración de la autenticación en una aplicación de ASP.NET Core de ejemplo](configure-authentication-sample-web-app-with-api.md)
- [Configuración de la autenticación en una aplicación de página única (SPA) de ejemplo](configure-authentication-sample-spa-app.md)

## <a name="overview"></a>Información general

La autenticación basada en tokens garantiza que las solicitudes a una API web van acompañadas de un token de acceso válido. 

La aplicación hace lo siguiente:

1. Para autenticar a los usuarios con Azure AD B2C.
1. Adquiere un token de acceso con los permisos necesarios (ámbitos) para el punto de conexión de API web.
1. Pasa el token de acceso como token de portador en el encabezado de autorización de la solicitud HTTP con este formato: 

    ```http
    Authorization: Bearer <token>
    ```    

La API web hace lo siguiente:

1. Lee el token de portador del encabezado de autorización en la solicitud HTTP.

1. Valida el token. 
1. Valida los permisos (ámbitos) en el token.
1. Lee las notificaciones codificadas en el token (opcional).
1. Responde a la solicitud HTTP. 

### <a name="app-registration-overview"></a>Introducción al registro de aplicaciones

Para que la aplicación pueda iniciar sesión con Azure AD B2C y llame a una API web, debe registrar dos aplicaciones en el directorio de Azure AD B2C.  

- El registro de la *aplicación web, móvil o SPA* permite que la aplicación inicie sesión con Azure AD B2C. El proceso de registro de la aplicación genera un *identificador de aplicación*, también conocido como *id. de cliente*, que permite identificar de forma exclusiva la aplicación (por ejemplo, *id. de aplicación: 1*).

- El registro de la *API web* permite que la aplicación llame a una API web protegida. El registro expone los permisos de la API web (ámbitos). El proceso de registro de la aplicación genera un *identificador de aplicación*, que identifica de forma exclusiva la API web (por ejemplo, *id. de aplicación: 2*). Conceda a la aplicación (id. de aplicación: 1) permisos para los ámbitos de la API web (id. de aplicación: 2). 

Los registros y la arquitectura de la aplicación se describen en el diagrama siguiente:

![Diagrama de los registros y la arquitectura de la aplicación para una aplicación con API web.](./media/enable-authentication-web-api/app-with-api-architecture.png) 

## <a name="prepare-your-development-environment"></a>Preparación del entorno de desarrollo  

En las secciones siguientes, creará un nuevo proyecto de API web. Seleccione el lenguaje de programación, ASP.NET Core o Node.js. Asegúrese de que tiene un equipo que ejecuta cualquiera de las siguientes opciones: 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

* [Visual Studio Code](https://code.visualstudio.com/download)
* [C# para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) (versión más reciente)
* [SDK de .NET 5.0](https://dotnet.microsoft.com/download/dotnet)

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

* [Visual Studio Code](https://code.visualstudio.com/) u otro editor de código
* [Entorno de ejecución de Node.js](https://nodejs.org/en/download/)

---

## <a name="step-1-create-a-protected-web-api"></a>Paso 1: Creación de una API web protegida

Cree un proyecto de API web. En primer lugar, seleccione el lenguaje de programación que desea usar, **ASP.NET Core** o **Node.js**.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Use el comando [`dotnet new`](/dotnet/core/tools/dotnet-new). El comando `dotnet new` crea una carpeta denominada *TodoList* con los recursos del proyecto de API web. Abra el directorio y, a continuación, abra [Visual Studio Code](https://code.visualstudio.com/). 

```dotnetcli
dotnet new webapi -o TodoList
cd TodoList
code . 
```

Cuando se le pida que "agregue los recursos necesarios al proyecto", seleccione **Sí**.

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Use [Express](https://expressjs.com/) para [Node.js](https://nodejs.org/) para crear una API web. Para crear una API web, haga lo siguiente:

1. Cree una carpeta con el nombre *TodoList*. 
1. En la carpeta *TodoList*, cree un archivo denominado *app.js*.
1. En un shell de comandos, ejecute `npm init -y`. Este comando crea un archivo *package.json* predeterminado para el proyecto de Node.js.
1. En el shell de comandos, ejecute `npm install express`. Este comando instala el marco Express.

--- 

## <a name="step-2-install-the-dependencies"></a>Paso 2: Instalación de las dependencias.

Agregue la biblioteca de autenticación al proyecto de la API web. La biblioteca de autenticación analiza el encabezado de autenticación HTTP, valida el token y extrae las notificaciones. Para obtener más información, revise la documentación de la biblioteca.


# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Para agregar la biblioteca de autenticación, instale el paquete mediante la ejecución del siguiente comando:

```dotnetcli
dotnet add package Microsoft.Identity.Web
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Para agregar la biblioteca de autenticación, instale los paquetes mediante la ejecución del siguiente comando:

```
npm install passport
npm install passport-azure-ad
npm install morgan
```
 
El [paquete morgen](https://www.npmjs.com/package/morgan) es middleware de registrador de solicitudes HTTP para Node.js.

---

## <a name="step-3-initiate-the-authentication-library"></a>Paso 3: Inicio de la biblioteca de autenticación

Agregue el código necesario para iniciar la biblioteca de autenticación.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Abra el archivo *Startup.cs* y, a continuación, agregue las siguientes declaraciones `using` al principio de la clase:

```csharp
using Microsoft.AspNetCore.Authentication.JwtBearer;
using Microsoft.Identity.Web;
```

Busque la función `ConfigureServices(IServiceCollection services)`. A continuación, antes de la línea de código `services.AddControllers();`, agregue el siguiente fragmento de código:


```csharp
public void ConfigureServices(IServiceCollection services)
{
    // Adds Microsoft Identity platform (Azure AD B2C) support to protect this Api
    services.AddAuthentication(JwtBearerDefaults.AuthenticationScheme)
            .AddMicrosoftIdentityWebApi(options =>
    {
        Configuration.Bind("AzureAdB2C", options);

        options.TokenValidationParameters.NameClaimType = "name";
    },
    options => { Configuration.Bind("AzureAdB2C", options); });
    // End of the Microsoft Identity platform block    

    services.AddControllers();
}
```

Busque la función `Configure`. A continuación, inmediatamente después de la línea de código `app.UseRouting();`, agregue el siguiente fragmento de código:


```csharp
app.UseAuthentication();
```

Después del cambio, el código debe ser parecido al siguiente fragmento de código:

```csharp
public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
{
    if (env.IsDevelopment())
    {
        app.UseDeveloperExceptionPage();
    }

    app.UseHttpsRedirection();

    app.UseRouting();
    
    // Add the following line 
    app.UseAuthentication();
    // End of the block you add
    
    app.UseAuthorization();

    app.UseEndpoints(endpoints =>
    {
        endpoints.MapControllers();
    });
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Agregue el siguiente código JavaScript al archivo *app.js*.

```javascript
// Import the required libraries
const express = require('express');
const morgan = require('morgan');
const passport = require('passport');
const config = require('./config.json');

// Import the passport Azure AD library
const BearerStrategy = require('passport-azure-ad').BearerStrategy;

// Set the Azure AD B2C options
const options = {
    identityMetadata: `https://${config.credentials.tenantName}.b2clogin.com/${config.credentials.tenantName}.onmicrosoft.com/${config.policies.policyName}/${config.metadata.version}/${config.metadata.discovery}`,
    clientID: config.credentials.clientID,
    audience: config.credentials.clientID,
    issuer: config.credentials.issuer,
    policyName: config.policies.policyName,
    isB2C: config.settings.isB2C,
    scope: config.resource.scope,
    validateIssuer: config.settings.validateIssuer,
    loggingLevel: config.settings.loggingLevel,
    passReqToCallback: config.settings.passReqToCallback
}

// Instantiate the passport Azure AD library with the Azure AD B2C options
const bearerStrategy = new BearerStrategy(options, (token, done) => {
        // Send user info using the second argument
        done(null, { }, token);
    }
);

// Use the required libraries
const app = express();

app.use(morgan('dev'));

app.use(passport.initialize());

passport.use(bearerStrategy);

//enable CORS (for testing only -remove in production/deployment)
app.use((req, res, next) => {
    res.header('Access-Control-Allow-Origin', '*');
    res.header('Access-Control-Allow-Headers', 'Authorization, Origin, X-Requested-With, Content-Type, Accept');
    next();
});
```
--- 

## <a name="step-4-add-the-endpoints"></a>Paso 4: Adición de los puntos de conexión

Agregue dos puntos de conexión a la API web:

- Punto de conexión `/public` anónimo. Este punto de conexión devuelve la fecha y hora actuales. Úselo para depurar la API web con llamadas anónimas.
- Punto de conexión `/hello` protegido. Este punto de conexión devuelve el valor de la notificación `name` dentro del token de acceso.

**Para agregar el punto de conexión anónimo:**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

En la carpeta */Controllers*, agregue un archivo *PublicController.cs* y, a continuación, agréguele el fragmento de código siguiente:

```csharp
using System;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;

namespace TodoList.Controllers
{
    [ApiController]
    [Route("[controller]")]
    public class PublicController : ControllerBase
    {
        private readonly ILogger<PublicController> _logger;

        public PublicController(ILogger<PublicController> logger)
        {
            _logger = logger;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new {date = DateTime.UtcNow.ToString()});
        }
    }
}
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

En el archivo *app.js*, agregue el código JavaScript siguiente:


```javascript
// API anonymous endpoint
app.get('/public', (req, res) => res.send( {'date': new Date() } ));
```

--- 

**Para agregar el punto de conexión protegido:**

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

En la carpeta */Controllers*, agregue un archivo *HelloController.cs* y, a continuación, agréguele el código siguiente:

```csharp
using Microsoft.AspNetCore.Authorization;
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Extensions.Logging;
using Microsoft.Identity.Web.Resource;

namespace TodoList.Controllers
{
    [Authorize]
    [RequiredScope("tasks.read")]
    [ApiController]
    [Route("[controller]")]
    public class HelloController : ControllerBase
    {

        private readonly ILogger<HelloController> _logger;
        private readonly IHttpContextAccessor _contextAccessor;

        public HelloController(ILogger<HelloController> logger, IHttpContextAccessor contextAccessor)
        {
            _logger = logger;
            _contextAccessor = contextAccessor;
        }

        [HttpGet]
        public ActionResult Get()
        {
            return Ok( new { name = User.Identity.Name});
        }
    }
}
```

El controlador `HelloController` está decorado con [AuthorizeAttribute](/aspnet/core/security/authorization/simple), que limita el acceso solo a los usuarios autenticados. 

El controlador también está decorado con `[RequiredScope("tasks.read")]`. El elemento [RequiredScopeAttribute](/dotnet/api/microsoft.identity.web.resource.requiredscopeattribute.-ctor) comprueba que se llama a la API web con los ámbitos adecuados, `tasks.read`. 

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

En el archivo *app.js*, agregue el código JavaScript siguiente: 

```javascript
// API protected endpoint
app.get('/hello',
    passport.authenticate('oauth-bearer', {session: false}),
    (req, res) => {
        console.log('Validated claims: ', req.authInfo);
        
        // Service relies on the name claim.  
        res.status(200).json({'name': req.authInfo['name']});
    }
);
```

El punto de conexión `/hello` primero llama a la función `passport.authenticate()`. La función de autenticación limita el acceso solo a los usuarios autenticados. 

La función de autenticación también comprueba que se llama a la API web con los ámbitos adecuados. Los ámbitos permitidos se encuentran en el [archivo de configuración](#step-6-configure-the-web-api). 

--- 

## <a name="step-5-configure-the-web-server"></a>Paso 5: Configuración del servidor web

En un entorno de desarrollo, establezca la API web para que escuche en el número de puerto de solicitudes HTTP entrantes. En este ejemplo, use el puerto HTTP 6000. El URI base de la API web será: <'http://localhost:6000 '>

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

Agregue el fragmento de código JSON siguiente al archivo *appsettings.json*. 

```json
"Kestrel": {
    "EndPoints": {
      "Http": {
        "Url": "http://localhost:6000"
      }
    }
  }
```

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

Agregue el siguiente código JavaScript al archivo *app.js*. 

```javascript
// Starts listening on port 6000
const port = process.env.PORT || 6000;

app.listen(port, () => {
    console.log('Listening on port ' + port);
});
```

---

## <a name="step-6-configure-the-web-api"></a>Paso 6: Configuración de la API web

Agregue configuraciones a un archivo de configuración. El archivo contiene información sobre el proveedor de identidades de Azure AD B2C. La aplicación de API web usa esta información para validar el token de acceso que la aplicación web pasa como token de portador.

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

En la carpeta raíz del proyecto, abra el archivo *appsettings.json* y, a continuación, agregue la siguiente configuración:

```json
{
  "AzureAdB2C": {
    "Instance": "https://contoso.b2clogin.com",
    "Domain": "contoso.onmicrosoft.com",
    "ClientId": "<web-api-app-application-id>",
    "SignedOutCallbackPath": "/signout/<your-sign-up-in-policy>",
    "SignUpSignInPolicyId": "<your-sign-up-in-policy>"
  },
  // More settings here
}
```

En el archivo *appsettings.json*, actualice las propiedades siguientes: 

|Sección  |Key  |Valor  |
|---------|---------|---------|
|AzureAdB2C|Instancia| Primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, `https://contoso.b2clogin.com`).|
|AzureAdB2C|Domain| [Nombre de inquilino](tenant-management.md#get-your-tenant-name) completo de Azure AD B2C (por ejemplo, `contoso.onmicrosoft.com`).|
|AzureAdB2C|ClientId| Id. de aplicación de la API web. En el [diagrama anterior](#app-registration-overview), es la aplicación con *id. de aplicación: 2*. Para obtener información sobre cómo obtener el identificador de registro de la aplicación de API web, consulte [Requisitos previos](#prerequisites). |
|AzureAdB2C|SignUpSignInPolicyId|Flujos de usuario o directiva personalizada. Para obtener información sobre cómo obtener el flujo de usuario o la directiva, consulte [Requisitos previos](#prerequisites).  |

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

En la carpeta raíz del proyecto, cree un archivo *config.json* y, a continuación, agréguele el siguiente fragmento de código JSON:  

```json
{
    "credentials": {
        "tenantName": "<your-tenant-name>",
        "clientID": "<your-webapi-application-ID>",
        "issuer": "https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/"
    },
    "policies": {
        "policyName": "b2c_1_susi"
    },
    "resource": {
        "scope": ["tasks.read"]
    },
    "metadata": {
        "discovery": ".well-known/openid-configuration",
        "version": "v2.0"
    },
    "settings": {
        "isB2C": true,
        "validateIssuer": true,
        "passReqToCallback": false,
        "loggingLevel": "info"
    }
}
```

En el archivo *config.json*, actualice las propiedades siguientes:

|Sección  |Key  |Valor  |
|---------|---------|---------|
| credentials | tenantName | Primera parte del [nombre de inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C (por ejemplo, `contoso`).|
| credentials |clientID | Id. de aplicación de la API web. En el [diagrama anterior](#app-registration-overview), es la aplicación con *id. de aplicación: 2*. Para obtener información sobre cómo obtener el identificador de registro de la aplicación de API web, consulte [Requisitos previos](#prerequisites). |
| credentials | issuer| Valor de notificación `iss` del emisor del token. De forma predeterminada, Azure AD B2C devuelve el token en el formato siguiente: `https://<your-tenant-name>.b2clogin.com/<your-tenant-ID>/v2.0/`. Reemplace `<your-tenant-name>` por la primera parte del [nombre del inquilino](tenant-management.md#get-your-tenant-name) de Azure AD B2C. Reemplace `<your-tenant-ID>` por el [id. del inquilino de Azure AD B2C](tenant-management.md#get-your-tenant-id). |
| directivas | policyName | Flujos de usuario o directiva personalizada. Para obtener información sobre cómo obtener el flujo de usuario o la directiva, consulte [Requisitos previos](#prerequisites).|
| resource | scope | Ámbitos del registro de la aplicación de API web. Para obtener información sobre cómo obtener el ámbito de la API web, consulte [Requisitos previos](#prerequisites).|

---

## <a name="step-7-run-and-test-the-web-api"></a>Paso 7: Ejecución y prueba de la API web

Por último, ejecute la API web con la configuración del entorno de Azure AD B2C. 

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)

En el shell de comandos, ejecute el siguiente comando para iniciar la aplicación web:

```bush
 dotnet run
```

Debería ver la siguiente salida, que significa que la aplicación está en funcionamiento y lista para recibir solicitudes.

```
Now listening on: http://localhost:6000
```

Para detener el programa, en el shell de comandos, seleccione Ctrl+C. Puede volver a ejecutar la aplicación mediante el comando `node app.js`.

> [!TIP]
> Como alternativa, para ejecutar el comando `dotnet run`, puede usar el [depurador de Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging). El depurador integrado de Visual Studio Code ayuda a acelerar el bucle de edición, compilación y depuración.

Abra un explorador y vaya a `http://localhost:6000/public`. En la ventana del explorador, debería ver el texto siguiente, junto con la fecha y hora actuales.

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)

En el shell de comandos, ejecute el siguiente comando para iniciar la aplicación web:

```bush
node app.js
```

Debería ver la siguiente salida, que significa que la aplicación está en funcionamiento y lista para recibir solicitudes.

```
Example app listening on port 6000!
```

Para detener el programa, en el shell de comandos, seleccione Ctrl+C. Puede volver a ejecutar la aplicación mediante el comando `node app.js`.

> [!TIP]
> Como alternativa, para ejecutar el comando `node app.js`, use el [depurador de Visual Studio Code](https://code.visualstudio.com/docs/editor/debugging). El depurador integrado de Visual Studio Code ayuda a acelerar el bucle de edición, compilación y depuración.

Abra un explorador y vaya a `http://localhost:6000/public`. En la ventana del explorador, debería ver el texto siguiente, junto con la fecha y hora actuales.

---

## <a name="step-8-call-the-web-api-from-your-app"></a>Paso 8: Llamada a la API web desde la aplicación

Intente llamar al punto de conexión de API web protegido sin un token de acceso. Abra un explorador y vaya a `http://localhost:6000/hello`. La API devolverá un mensaje de error HTTP no autorizado, que confirmará que la API web está protegida con un token de portador.

Siga configurando la aplicación para llamar a la API web. Para obtener instrucciones, consulte la sección [Requisitos previos](#prerequisites).

Vea este vídeo para obtener información sobre algunos procedimientos recomendados al integrar Azure AD B2C con una API.

>[!Video https://www.youtube.com/embed/wuUu71RcsIo]

## <a name="next-steps"></a>Pasos siguientes

Obtenga el ejemplo completo en GitHub:

# <a name="aspnet-core"></a>[ASP.NET Core](#tab/csharpclient)
* Obtenga la API web mediante la [biblioteca de identidades de Microsoft](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/4-WebApp-your-API/4-2-B2C/TodoListService).

# <a name="nodejs"></a>[Node.js](#tab/nodejsgeneric)
* Obtenga la API web mediante la [biblioteca Passport.js](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi).
