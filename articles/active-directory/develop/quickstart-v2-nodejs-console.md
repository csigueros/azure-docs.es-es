---
title: 'Inicio rápido: Llamada a Microsoft Graph desde una aplicación de consola de Node.js | Azure'
titleSuffix: Microsoft identity platform
description: En este inicio rápido, descargará y ejecutará un código de ejemplo que muestra de qué forma una aplicación de consola de Node.js puede obtener un token de acceso y llamar a una API protegida por un punto de conexión de una plataforma de identidad de Microsoft mediante la propia identidad de la aplicación
services: active-directory
author: derisen
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: quickstart
ms.date: 02/17/2021
ms.author: v-doeris
ms.openlocfilehash: ffe6f750604546a51a7d3d7852df5745ba07dd3a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128619143"
---
# <a name="quickstart-acquire-a-token-and-call-microsoft-graph-api-from-a-nodejs-console-app-using-apps-identity"></a>Inicio rápido: Adquisición de un token y llamada a Microsoft Graph API desde una aplicación de consola de Node.js mediante la identidad de la aplicación

En este inicio rápido descargará y ejecutará un código de ejemplo que muestra cómo puede obtener una aplicación de consola de Node.js un token de acceso mediante la identidad de la aplicación para llamar a Microsoft Graph API y mostrar una [lista de usuarios](/graph/api/user-list) en el directorio. En el ejemplo de código se muestra cómo se puede ejecutar un trabajo desatendido o un servicio de Windows con una identidad de aplicación, en lugar de la identidad de un usuario.

En este inicio rápido se usa la [biblioteca de autenticación de Microsoft para Node.js (MSAL Node)](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) con la [concesión de credenciales de cliente](v2-oauth2-client-creds-grant-flow.md).

## <a name="prerequisites"></a>Prerrequisitos

* [Node.js](https://nodejs.org/en/download/)
* [Visual Studio Code](https://code.visualstudio.com/download) u otro editor de código

> [!div renderon="docs"]
> ## <a name="register-and-download-the-sample-application"></a>Registro y descarga de la aplicación de ejemplo
>
> Para comenzar, siga estos pasos.
>
> [!div renderon="docs"]
> #### <a name="step-1-register-the-application"></a>Paso 1: Registro de la aplicación
> Para registrar la aplicación y agregar la información de registro de la aplicación a la solución de forma manual, siga estos pasos:
>
> 1. Inicie sesión en <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>.
> 1. Si tiene acceso a varios inquilinos, use el filtro **Directorios y suscripciones**:::image type="icon" source="./media/common/portal-directory-subscription-filter.png" border="false"::: del menú superior para cambiar al inquilino en el que quiere registrar la aplicación.
> 1. Busque y seleccione **Azure Active Directory**.
> 1. En **Administrar**, seleccione **Registros de aplicaciones** >  y, luego, **Nuevo registro**.
> 1. Escriba el **Nombre** de la aplicación, por ejemplo `msal-node-cli`. Los usuarios de la aplicación pueden ver este nombre, el cual se puede cambiar más tarde.
> 1. Seleccione **Registrar**.
> 1. En **Administrar**, seleccione **Certificados y secretos**.
> 1. En **Secretos de cliente**, seleccione **Nuevo secreto de cliente**, escriba un nombre y seleccione **Agregar**. Grabe el valor del secreto en una ubicación segura para usarlo en un paso posterior.
> 1. En **Administrar**, seleccione **Permisos de API** > **Add a permission** (Agregar un permiso). Seleccione **Microsoft Graph**.
> 1. Seleccione **Permisos de aplicación**.
> 1. En el nodo **Usuario**, seleccione **User.Read.All** y, luego, **Agregar permisos**.

> [!div class="sxs-lookup" renderon="portal"]
> ### <a name="download-and-configure-the-sample-app"></a>Descarga y configuración de la aplicación de ejemplo
>
> #### <a name="step-1-configure-the-application-in-azure-portal"></a>Paso 1: Configuración de la aplicación en Azure Portal
> Para que el ejemplo de código de esta guía de inicio rápido funcione, debe crear un secreto de cliente y agregar el permiso de aplicación **User.Read.All** de Graph API.
> > [!div renderon="portal" id="makechanges" class="nextstepaction"]
> > [Realizar estos cambios por mí]()
>
> > [!div id="appconfigured" class="alert alert-info"]
> > ![Ya configurada](media/quickstart-v2-netcore-daemon/green-check.png) La aplicación está configurada con estos atributos.

#### <a name="step-2-download-the-nodejs-sample-project"></a>Paso 2: Descarga del proyecto de ejemplo en Node.js

> [!div renderon="docs"]
> [Descargar el código de ejemplo](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div renderon="portal" id="autoupdate" class="sxs-lookup nextstepaction"]
> [Descargar el código de ejemplo](https://github.com/azure-samples/ms-identity-javascript-nodejs-console/archive/main.zip)

> [!div class="sxs-lookup" renderon="portal"]
> > [!NOTE]
> > `Enter_the_Supported_Account_Info_Here`

> [!div renderon="docs"]
> #### <a name="step-3-configure-the-nodejs-sample-project"></a>Paso 3: Configuración del proyecto de ejemplo en Node.js
>
> 1. Extraiga el archivo ZIP en una carpeta local próxima a la raíz del disco, por ejemplo, *C:\Azure-Samples*.
> 1. Edite *.env* y sustituya los valores de los campos `TENANT_ID`, `CLIENT_ID` y `CLIENT_SECRET` por el siguiente fragmento de código:
>
>    ```
>    "TENANT_ID": "Enter_the_Tenant_Id_Here",
>    "CLIENT_ID": "Enter_the_Application_Id_Here",
>    "CLIENT_SECRET": "Enter_the_Client_Secret_Here"
>    ```
>    Donde:
>    - `Enter_the_Application_Id_Here`: es el **id. de aplicación (cliente)** de la aplicación que registró anteriormente. Búsquelo en el panel **Información general** del registro de la aplicación en Azure Portal.
>    - `Enter_the_Tenant_Id_Here`: sustituya este valor por el **identificador de inquilino** o el **nombre de inquilino** (por ejemplo, contoso.microsoft.com).  Estos valores se pueden buscar en el panel **Información general** del registro de la aplicación en Azure Portal.
>    - `Enter_the_Client_Secret_Here`: sustituya este valor por el secreto de cliente que creó anteriormente. Para generar una nueva clave, use **Certificados y secretos** en la configuración del registro de aplicaciones en Azure Portal.
>
> > [!WARNING]
> > Cualquier secreto de texto no cifrado que haya en el código fuente supone un aumento del riesgo de seguridad. En este artículo se usa un secreto de cliente de texto no cifrado exclusivamente en aras de una mayor simplicidad. Use [credenciales de certificado](active-directory-certificate-credentials.md), en lugar de secretos de cliente, en las aplicaciones cliente confidenciales, especialmente en las que desee implementar en producción.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-3-admin-consent"></a>Paso 3: Consentimiento de administrador

> [!div renderon="docs"]
> #### <a name="step-4-admin-consent"></a>Paso 4: Consentimiento de administrador

Si intenta ejecutar la aplicación en este momento, recibirá un error *HTTP 403 - Prohibido*: `Insufficient privileges to complete the operation`. Este error sucede porque cualquier *permiso de solo aplicación* requiere el **consentimiento del administrador**: un administrador global del directorio debe otorgar su consentimiento a la aplicación. Seleccione una de las opciones siguientes según el rol:

##### <a name="global-tenant-administrator"></a>Administrador de inquilinos global

> [!div renderon="docs"]
> Si es administrador de inquilinos global, vaya a la página **Permisos de API** del registro de aplicación de Azure Portal y seleccione **Grant admin consent for {Tenant Name}** (Conceder consentimiento del administrador para {nombre de inquilino}), donde {nombre de inquilino} es el nombre del directorio.

> [!div renderon="portal" class="sxs-lookup"]
> Si es administrador global, vaya a la página **Permisos de API**, seleccione **Grant admin consent for Enter_the_Tenant_Name_Here** (Conceder consentimiento del administrador para _escribir_aquí_el_nombre_del_inquilino).
> > [!div id="apipermissionspage"]
> > [Ir a la página Permisos de API]()

##### <a name="standard-user"></a>Usuario estándar

Si es usuario estándar de su inquilino, debe pedir a un administrador global que conceda **consentimiento del administrador** para su aplicación. Para ello, proporcione la siguiente dirección URL a su administrador:

```url
https://login.microsoftonline.com/Enter_the_Tenant_Id_Here/adminconsent?client_id=Enter_the_Application_Id_Here
```

> [!div renderon="docs"]
>> Donde:
>> * `Enter_the_Tenant_Id_Here`: sustituya este valor por el **identificador de inquilino** o el **nombre de inquilino** (por ejemplo, contoso.microsoft.com).
>> * `Enter_the_Application_Id_Here`: es el **identificador de aplicación (cliente)** de la aplicación que registró.

> [!div class="sxs-lookup" renderon="portal"]
> #### <a name="step-4-run-the-application"></a>Paso 4: Ejecución de la aplicación

> [!div renderon="docs"]
> #### <a name="step-5-run-the-application"></a>Paso 5: Ejecución de la aplicación

Busque la carpeta raíz del ejemplo (donde `package.json` reside) en una consola o un símbolo del sistema. Deberá instalar las dependencias de este ejemplo una vez:

```console
npm install
```

A continuación, ejecute la aplicación a través del símbolo del sistema o la consola:

```console
node . --op getUsers
```

Debería ver en la salida de la consola algún fragmento de JSON que represente una lista de los usuarios del directorio de Azure AD.

## <a name="about-the-code"></a>Sobre el código

A continuación, se describen algunos de los aspectos importantes de la aplicación de ejemplo.

### <a name="msal-node"></a>MSAL Node

[MSAL Node](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-node) es la biblioteca que se usa para iniciar la sesión de los usuarios y solicitar los tokens que se usan para acceder a una API protegida por la plataforma de identidad de Microsoft. Como se ha descrito, en este inicio rápido se solicitan tokens por permisos de aplicación (mediante la identidad propia de la aplicación), en lugar de permisos delegados. El flujo de autenticación usado en este caso se conoce como [flujo de credenciales de cliente de OAuth 2.0](v2-oauth2-client-creds-grant-flow.md). Para más información sobre cómo usar MSAL Node con aplicaciones de demonio, consulte [Escenario: aplicación demonio](scenario-daemon-overview.md).

 Para instalar MSAL Node, ejecute el siguiente comando npm.

```console
npm install @azure/msal-node --save
```

### <a name="msal-initialization"></a>Inicialización de MSAL

Puede agregar la referencia de MSAL con el código siguiente:

```javascript
const msal = require('@azure/msal-node');
```

A continuación, realice la inicialización de MSAL con el siguiente código:

```javascript
const msalConfig = {
    auth: {
        clientId: "Enter_the_Application_Id_Here",
        authority: "https://login.microsoftonline.com/Enter_the_Tenant_Id_Here",
        clientSecret: "Enter_the_Client_Secret_Here",
   }
};
const cca = new msal.ConfidentialClientApplication(msalConfig);
```

> | Donde: |Descripción |
> |---------|---------|
> | `clientId` | Es el **Identificador de aplicación (cliente)** de la aplicación registrada en Azure Portal. Puede encontrar este valor en la página **Información general** de la aplicación en Azure Portal. |
> | `authority`    | El punto de conexión STS para el usuario que se autenticará. Normalmente `https://login.microsoftonline.com/{tenant}` en la nube pública, donde {tenant} es el nombre o el identificador del inquilino.|
> | `clientSecret` | Es el secreto de cliente creado para la aplicación en Azure Portal. |

Para más información, consulte la [documentación de referencia de `ConfidentialClientApplication`](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-node/docs/initialize-confidential-client-application.md).

### <a name="requesting-tokens"></a>Solicitud de tokens

Para solicitar un token mediante la identidad de la aplicación, use el método `acquireTokenByClientCredential`:

```javascript
const tokenRequest = {
    scopes: [ 'https://graph.microsoft.com/.default' ],
};

const tokenResponse = await cca.acquireTokenByClientCredential(tokenRequest);
```

> |Donde:| Descripción |
> |---------|---------|
> | `tokenRequest` | Contiene los ámbitos solicitados. Con clientes confidenciales, se debe usar el formato similar a `{Application ID URI}/.default` para indicar que los ámbitos que se solicitan son los definidos estáticamente en el objeto de aplicación establecido en Azure Portal (con Microsoft Graph, `{Application ID URI}` apunta a `https://graph.microsoft.com`). En el caso de las API web personalizadas, `{Application ID URI}` se define en la sección **Exponer una API** del registro de aplicación de Azure Portal. |
> | `tokenResponse` | La respuesta contiene un token de acceso para los ámbitos solicitados. |

[!INCLUDE [Help and support](../../../includes/active-directory-develop-help-support-include.md)]

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el desarrollo de aplicaciones de consola o demonio con MSAL Node, consulte el tutorial:

> [!div class="nextstepaction"]
> [Aplicación demonio que llama a las API web](tutorial-v2-nodejs-console.md)