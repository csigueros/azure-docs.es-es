---
title: Autorización de solicitudes a recursos de SignalR con Azure AD desde aplicaciones de Azure
description: En este artículo se proporciona información sobre la autorización de solicitudes a recursos de SignalR con Azure AD desde aplicaciones de Azure
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: 8297a715d66206bbad2721faaca89c3616744ce4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478287"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-azure-applications"></a>Autorización de solicitudes a recursos de SignalR con Azure AD desde aplicaciones de Azure

Azure SignalR Service admite solicitudes de autorización de Azure Active Directory (Azure AD) procedentes de [aplicaciones de Azure](../active-directory/develop/app-objects-and-service-principals.md).

En este artículo se muestra cómo configurar el recurso y los códigos de SignalR para autorizar la solicitud a un recurso de SignalR desde una aplicación de Azure.

## <a name="register-an-application"></a>Registro de una aplicación

El primer paso consiste en registrar una aplicación de Azure.

1. En [Azure Portal](https://portal.azure.com/), busque y seleccione **Azure Active Directory**.
2. En la sección **Administrar**, seleccione **Registros de aplicaciones**.
3. Haga clic en **Nuevo registro**.

    ![Captura de pantalla del registro de una aplicación](./media/authenticate/register-an-application.png)

4. Escriba un **Nombre** para mostrar para la aplicación.
5. Haga clic en **Registrar** para confirmar el registro.

Cuando haya registrado la aplicación, puede encontrar el **identificador de aplicación (cliente)** y el **identificador de directorio (inquilino)** en su página de información general. Estos GUID pueden ser útiles en los pasos siguientes.

![Captura de pantalla de una aplicación](./media/authenticate/application-overview.png)

Para más información sobre el registro de una aplicación, vea
- [Inicio rápido: Registro de una aplicación con la plataforma de identidad de Microsoft](../active-directory/develop/quickstart-register-app.md).


## <a name="add-credentials"></a>Adición de credenciales

Puede agregar certificados y secretos de cliente (una cadena) como credenciales al registro de la aplicación cliente confidencial.

### <a name="client-secret"></a>Secreto del cliente

La aplicación necesita un secreto de cliente para demostrar su identidad al solicitar un token. Para agregar un secreto de cliente, siga estos pasos.

1. En la sección **Administrar**, seleccione **Certificados y secretos**.
1. En la pestaña **Secretos de cliente**, haga clic en **Nuevo secreto de cliente**.
![Captura de pantalla de la creación de un secreto de cliente](./media/authenticate/new-client-secret.png)
1. Escriba una **descripción** para el secreto de cliente y elija una **hora de expiración**.
1. Copie el valor del **secreto de cliente** y péguelo en una ubicación segura. 
    > [!NOTE]
    > El secreto solo se mostrará una vez.

### <a name="certificate"></a>Certificado

También puede cargar una certificación en lugar de crear un secreto de cliente.

![Captura de pantalla de la carga de una certificación](./media/authenticate/upload-certificate.png)

Para más información sobre cómo agregar credenciales, vea

- [Adición de credenciales](../active-directory/develop/quickstart-register-app.md#add-credentials)

## <a name="add-role-assignments-on-azure-portal"></a>Adición de asignaciones de roles en Azure Portal

En este ejemplo se muestra cómo asignar un rol `SignalR App Server` a una entidad de servicio (aplicación) mediante un recurso de SignalR. 

> [!Note]
> Un rol se puede asignar a cualquier ámbito, por ejemplo, al grupo de administración, la suscripción, el grupo de recursos o un único recurso. Para más información sobre el ámbito, vea [Descripción del ámbito de RBAC de Azure](../role-based-access-control/scope-overview.md).

1. En [Azure Portal](https://portal.azure.com/), vaya al recurso de SignalR.

1. Haga clic en **Control de acceso (IAM)** para mostrar la configuración del control de acceso de Azure SignalR.

1. Haga clic en la pestaña **Asignaciones de roles** para ver todas las asignaciones de roles en este ámbito.

   En la captura de pantalla siguiente se muestra un ejemplo de la página Control de acceso (IAM) de un recurso de SignalR.

   ![Captura de pantalla del control de acceso](./media/authenticate/access-control.png)

1. Haga clic en **Agregar > Agregar asignación de roles**.

1. En la pestaña **Roles**, seleccione `SignalR App Server`.

1. Haga clic en **Siguiente**.
    
   ![Captura de pantalla de la adición de la asignación de roles](./media/authenticate/add-role-assignment.png)

1. En la pestaña **Miembros**, en la sección **Asignar acceso a**, seleccione **Usuario, grupo o entidad de servicio**.

1. Haga clic en **Seleccionar miembros**.

3. Busque y seleccione la aplicación a la que quiere asignar el rol.

1. Haga clic en **Seleccionar** para confirmar la selección.

4. Haga clic en **Siguiente**.
    
   ![Captura de pantalla de la asignación de un rol a entidades de servicio](./media/authenticate/assign-role-to-service-principals.png)

5. Haga clic en **Revisar y asignar** para confirmar el cambio.

> [!IMPORTANT]
> Las asignaciones de roles de Azure pueden tardar hasta 30 minutos en propagarse.

Para más información sobre cómo asignar y administrar asignaciones de roles de Azure, vea estos artículos:
- [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md)
- [Asignación de roles de Azure mediante la API REST](../role-based-access-control/role-assignments-rest.md)
- [Asignación de roles de Azure mediante Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Asignación de roles de Azure mediante la CLI de Azure](../role-based-access-control/role-assignments-cli.md)
- [Asignación de roles de Azure mediante plantillas de Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

## <a name="configure-you-app"></a>Configuración de la aplicación

### <a name="app-server"></a>Servidor de aplicaciones

El procedimiento recomendado consiste en configurar la identidad y las credenciales en las variables de entorno:

| Variable  | Descripción |
|------|------
| `AZURE_TENANT_ID` | Identificador de inquilino (directorio) de Azure Active Directory. |
| `AZURE_CLIENT_ID` | Identificador de cliente (aplicación) de un registro de aplicación en el inquilino. |
| `AZURE_CLIENT_SECRET` | Secreto de cliente generado para el registro de aplicación. |
| `AZURE_CLIENT_CERTIFICATE_PATH` | Ruta al par de certificado y clave privada en formato PEM o PFX, que puede autenticar el registro de aplicación. |
| `AZURE_USERNAME`  | Nombre de usuario, también conocido como UPN, de una cuenta de usuario de Azure Active Directory. |
| `AZURE_PASSWORD`  | Contraseña de la cuenta de usuario de Azure Active Directory. Tenga en cuenta que no se admiten cuentas con MFA habilitada. |

Si lo hace, puede usar [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) o [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) para configurar los puntos de conexión de SignalR.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new DefaultAzureCredential())
    };
});
```

O bien usar `EnvironmentalCredential` directamente.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource-name>.service.signalr.net"), new EnvironmentCredential())
    };
});
```

Para saber cómo funciona `DefaultAzureCredential`, vea [DefaultAzureCredential (clase)](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

#### <a name="use-different-credentials-while-using-multiple-endpoints"></a>Utilice otras credenciales mientras usa varios puntos de conexión.

Por alguna razón, es posible que quiera usar otras credenciales para otros puntos de conexión.

En este escenario, puede usar [ClientSecretCredential](/dotnet/api/azure.identity.clientsecretcredential) o [ClientCertificateCredential](/dotnet/api/azure.identity.clientcertificatecredential).

```csharp
services.AddSignalR().AddAzureSignalR(option =>
{
    var credential1 = new ClientSecretCredential("tenantId", "clientId", "clientSecret");
    var credential2 = new ClientCertificateCredential("tenantId", "clientId", "pathToCert");

    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), credential1),
        new ServiceEndpoint(new Uri("https://<resource2>.service.signalr.net"), credential2),
    };
});
```

### <a name="azure-functions-signalr-bindings"></a>Enlaces de SignalR de Azure Functions

> [!WARNING]
> El enlace de desencadenador de SignalR todavía no admite la conexión basada en identidades y las cadenas de conexión siguen siendo necesarias.

Los enlaces de SignalR de Azure Functions usan la [configuración de aplicación](/azure/azure-functions/functions-how-to-use-azure-function-app-settings) en el portal o [`local.settings.json`](/azure/azure-functions/functions-develop-local#local-settings-file) en el entorno local a fin de configurar identidades de aplicación de Azure para acceder a los recursos de SignalR.

En primer lugar, debe especificar el URI de servicio de SignalR Service, cuya clave es `serviceUri`, comienza con un **prefijo de nombre de conexión** (el valor predeterminado es `AzureSignalRConnectionString`) y un separador (`__` en Azure Portal y `:`en el archivo local.settings.json). El nombre de conexión se puede personalizar con la propiedad de enlace [`ConnectionStringSetting`](/azure/azure-functions/functions-bindings-signalr-service). Siga leyendo para encontrar el ejemplo.

Después, elija configurar la identidad de la aplicación de Azure en [variables de entorno predefinidas](#configure-identity-in-pre-defined-environment-variables) o en [variables especificadas de SignalR](#configure-identity-in-signalr-specified-variables).

#### <a name="configure-identity-in-pre-defined-environment-variables"></a>Configuración de la identidad en variables de entorno predefinidas

Vea [Variables de entorno](/dotnet/api/overview/azure/identity-readme#environment-variables) para obtener la lista de variables de entorno predefinidas. Cuando hay varios servicios, se recomienda incluir SignalR para que dependa de la misma identidad de aplicación de Azure, a fin de que no sea necesario configurar la identidad para cada servicio. Según su configuración, otros servicios también pueden usar estas variables de entorno.

Por ejemplo, para usar credenciales de secreto de cliente, configure como se muestra a continuación en el archivo `local.settings.json`.
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net",
    "AZURE_CLIENT_ID":"...",
    "AZURE_CLIENT_SECRET":"...",
    "AZURE_TENANT_ID":"..."
  }
}
```
En Azure Portal, agregue la configuración siguiente:
```
 <CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
AZURE_CLIENT_ID = ...
AZURE_TENANT_ID = ...
AZURE_CLIENT_SECRET = ...
 ```

#### <a name="configure-identity-in-signalr-specified-variables"></a>Configuración de la identidad en variables especificadas de SignalR

Las variables especificadas de SignalR comparten el mismo prefijo de clave con la clave `serviceUri`. Esta es la lista de variables que puede usar:
* clientId
* clientSecret
* tenantId

Estos son los ejemplos para usar las credenciales de secreto de cliente:

En el archivo `local.settings.json`:
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net",
    "<CONNECTION_NAME_PREFIX>:clientId": "...",
    "<CONNECTION_NAME_PREFIX>:clientSecret": "...",
    "<CONNECTION_NAME_PREFIX>:tenantId": "..."
  }
}
```

En Azure Portal, agregue la configuración siguiente:
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__clientId = ...
<CONNECTION_NAME_PREFIX>__clientSecret = ...
<CONNECTION_NAME_PREFIX>__tenantId = ...
```
## <a name="next-steps"></a>Pasos siguientes

Consulte los artículos relacionados siguientes:
- [Introducción a Azure AD para SignalR](signalr-concept-authorize-azure-active-directory.md)
- [Autorización de solicitudes a recursos de SignalR con Azure AD desde identidades administradas](signalr-howto-authorize-managed-identity.md)