---
title: Autorización de solicitudes a recursos de SignalR con Azure AD desde identidades administradas
description: En este artículo se proporciona información sobre la autorización de solicitudes a recursos de SignalR con Azure AD desde identidades administradas.
author: terencefan
ms.author: tefa
ms.date: 09/06/2021
ms.service: signalr
ms.topic: conceptual
ms.openlocfilehash: b65dc12c3854a1730f74bf679a724e651985883b
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716913"
---
# <a name="authorize-request-to-signalr-resources-with-azure-ad-from-managed-identities"></a>Autorización de solicitudes a recursos de SignalR con Azure AD desde identidades administradas
Azure SignalR Service admite solicitudes de autorización de Azure Active Directory (Azure AD) desde [identidades administradas de recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).

En este artículo se muestra cómo configurar el recurso y los códigos de SignalR para autorizar la solicitud a un recurso de SignalR desde una identidad administrada.

## <a name="configure-managed-identities"></a>Configuración de identidades administradas

El primer paso es configurar las identidades administradas.

Aquí hay un ejemplo para configurar `System-assigned managed identity` en `Virtual Machine` mediante Azure Portal.

1. Abra [Azure Portal](https://portal.azure.com/) y busque y seleccione una máquina virtual.
1. En el menú **Configuración**, seleccione **Identidad**.
1. En la pestaña **Asignado por el sistema**, cambie **Estado** a **Activado**.
   ![Captura de pantalla de una aplicación](./media/authenticate/identity-virtual-machine.png)
1. Haga clic en el botón **Guardar** para confirmar el cambio.


Para obtener información sobre cómo crear identidades administradas asignadas por el usuario, consulte este artículo:
- [Creación de una identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md#create-a-user-assigned-managed-identity)

Para obtener más información sobre la configuración de identidades administradas, consulte uno de estos artículos:

- [Configurar identidades administradas para recursos de Azure en una VM mediante Azure Portal](../active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm.md)
- [Configuración de identidades administradas de recursos de Azure en una VM de Azure mediante PowerShell](../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md)
- [Configuración de identidades administradas para recursos de Azure en una VM de Azure mediante la CLI de Azure](../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md)
- [Configuración de identidades administradas para recursos de Azure en una máquina virtual de Azure mediante plantillas](../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md)
- [Configuración de una máquina virtual con identidades administradas de recursos de Azure mediante un SDK de Azure](../active-directory/managed-identities-azure-resources/qs-configure-sdk-windows-vm.md)

### <a name="for-app-service-and-azure-functions"></a>Para App Service y Azure Functions

Consulte [Cómo usar identidades administradas para App Service y Azure Functions](../app-service/overview-managed-identity.md).

## <a name="add-role-assignments-on-azure-portal"></a>Adición de asignaciones de roles en Azure Portal

En este ejemplo se muestra cómo asignar un rol `SignalR App Server` a una identidad asignada por el sistema mediante un recurso de SignalR. 

> [!Note]
> Un rol se puede asignar a cualquier ámbito, por ejemplo, al grupo de administración, la suscripción, el grupo de recursos o un único recurso. Para obtener más información sobre el ámbito, consulte [Descripción del ámbito de RBAC de Azure](../role-based-access-control/scope-overview.md).

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al recurso de SignalR.

1. Haga clic en **Control de acceso (IAM)** para mostrar la configuración del control de acceso de Azure SignalR.

   A continuación se muestra un ejemplo de la página Control de acceso (IAM) de un grupo de recursos.

1. Haga clic en la pestaña **Asignaciones de roles** para ver todas las asignaciones de roles en este ámbito.

   En la captura de pantalla siguiente se muestra un ejemplo de la página Control de acceso (IAM) de un recurso de SignalR.

   ![Captura de pantalla del control de acceso](./media/authenticate/access-control.png)

1. Haga clic en **Agregar > Agregar asignación de roles**.

1. En la pestaña **Roles**, seleccione `SignalR App Server`.

1. Haga clic en **Siguiente**.

   ![Captura de pantalla de la adición de la asignación de roles](./media/authenticate/add-role-assignment.png)

1. En la pestaña **Miembros**, en la sección **Asignar acceso a**, seleccione **Identidad administrada**.

1. Haga clic en **Seleccionar miembros**.

1. En el panel **Selección de identidades administradas**, elija **Identidad administrada asignada por el sistema > Máquina virtual**.

1. Busque y seleccione la máquina virtual a la que quiere asignar el rol.

1. Haga clic en **Seleccionar** para confirmar la selección.

2. Haga clic en **Siguiente**.

   ![Captura de pantalla de la asignación de roles a identidades administradas](./media/authenticate/assign-role-to-managed-identities.png)

3. Haga clic en **Revisar y asignar** para confirmar el cambio.

> [!IMPORTANT]
> Las asignaciones de roles de Azure pueden tardar hasta 30 minutos en propagarse.

Para obtener más información sobre cómo asignar y administrar asignaciones de roles de Azure, consulte estos artículos:
- [Asignación de roles de Azure mediante Azure Portal](../role-based-access-control/role-assignments-portal.md)
- [Asignación de roles de Azure mediante la API REST](../role-based-access-control/role-assignments-rest.md)
- [Asignación de roles de Azure mediante Azure PowerShell](../role-based-access-control/role-assignments-powershell.md)
- [Asignación de roles de Azure mediante la CLI de Azure](../role-based-access-control/role-assignments-cli.md)
- [Asignación de roles de Azure mediante plantillas de Azure Resource Manager](../role-based-access-control/role-assignments-template.md)

## <a name="configure-your-app"></a>Configurar la aplicación

### <a name="app-server"></a>App Server

#### <a name="using-system-assigned-identity"></a>Uso de la identidad asignada por el sistema

Puede usar [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential) o [ManagedIdentityCredential](/dotnet/api/azure.identity.managedidentitycredential) para configurar los puntos de conexión de SignalR.


Pero el procedimiento recomendado es usar `ManagedIdentityCredential` directamente.

La identidad administrada asignada por el sistema se usará de forma predeterminada, pero **asegúrese de que no configura ninguna variable de entorno** conservada por [EnvironmentCredential](/dotnet/api/azure.identity.environmentcredential) si usa `DefaultAzureCredential`. De lo contrario, se recurrirá a usar `EnvironmentCredential` para realizar la solicitud y esto dará como resultado una respuesta `Unauthorized` en la mayoría de los casos.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential()),
    };
});
```

#### <a name="using-user-assigned-identity"></a>Uso de la identidad asignada por el usuario

Proporcione el valor de `ClientId` al crear el objeto `ManagedIdentityCredential`.

> [!IMPORTANT]
> Use el **identificador de cliente**, no el identificador de objeto (entidad de seguridad), aunque ambos sean GUID.

```C#
services.AddSignalR().AddAzureSignalR(option =>
{
    option.Endpoints = new ServiceEndpoint[]
    {
        var clientId = "<your identity client id>";
        new ServiceEndpoint(new Uri("https://<resource1>.service.signalr.net"), new ManagedIdentityCredential(clientId)),
    };
```

### <a name="azure-functions-signalr-bindings"></a>Enlaces de SignalR de Azure Functions

> [!WARNING]
> El enlace de desencadenador de SignalR todavía no admite la conexión basada en identidades y las cadenas de conexión siguen siendo necesarias.

Los enlaces de SignalR de Azure Functions usan la [configuración de la aplicación](../azure-functions/functions-how-to-use-azure-function-app-settings.md) en el portal o [`local.settings.json`](../azure-functions/functions-develop-local.md#local-settings-file) en el entorno local a fin de configurar una identidad administrada para acceder a los recursos de SignalR.

Es posible que necesite un grupo de pares clave-valor para configurar una identidad. Las claves de todos los pares clave-valor deben comenzar con un **prefijo de nombre de conexión** (el valor predeterminado es `AzureSignalRConnectionString`) y un separador (`__` en el portal y `:` en el entorno local). El prefijo se puede personalizar con la propiedad de enlace [`ConnectionStringSetting`](../azure-functions/functions-bindings-signalr-service.md).

#### <a name="using-system-assigned-identity"></a>Uso de la identidad asignada por el sistema

Si solo configura el URI de servicio, se usa `DefaultAzureCredential`. Esto resulta útil si quiere compartir la misma configuración en Azure y en el entorno de desarrollo local. Para conocer cómo funciona `DefaultAzureCredential`, vea [DefaultAzureCredential](/dotnet/api/overview/azure/identity-readme#defaultazurecredential).

En Azure Portal, establezca lo siguiente para configurar una credencial `DefaultAzureCredential`. Si se asegura de no configurar ninguna de las [variables de entorno enumeradas aquí](/dotnet/api/overview/azure/identity-readme#environment-variables), se usará la identidad asignada por el sistema para la autenticación.
```
<CONNECTION_NAME_PREFIX>__serviceUri=https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
```

Este es un ejemplo de configuración de `DefaultAzureCredential` en el archivo `local.settings.json`. Tenga en cuenta que en el entorno local no hay ninguna identidad administrada, por lo que se intentará la autenticación mediante Visual Studio, la CLI de Azure y Azure PowerShell, en este orden.
```json
{
  "Values": {
    "<CONNECTION_NAME_PREFIX>:serviceUri": "https://<SIGNALR_RESOURCE_NAME>.service.signalr.net"
  }
}
```

Si quiere usar la identidad asignada por el sistema de forma independiente y sin la influencia de [otras variables de entorno](/dotnet/api/overview/azure/identity-readme#environment-variables), debe establecer la clave `credential` con el prefijo de nombre de conexión en `managedidentity`. Este es un ejemplo de configuración de la aplicación:

```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
```

#### <a name="using-user-assigned-identity"></a>Uso de la identidad asignada por el usuario

Si quiere usar la identidad asignada por el usuario, debe asignar una clave `clientId` adicional con el prefijo de nombre de conexión en comparación con la identidad asignada por el sistema. Este es un ejemplo de configuración de la aplicación:
```
<CONNECTION_NAME_PREFIX>__serviceUri = https://<SIGNALR_RESOURCE_NAME>.service.signalr.net
<CONNECTION_NAME_PREFIX>__credential = managedidentity
<CONNECTION_NAME_PREFIX>__clientId = <CLIENT_ID>
```
## <a name="next-steps"></a>Pasos siguientes

Consulte los artículos relacionados siguientes:
- [Introducción a Azure AD para SignalR](signalr-concept-authorize-azure-active-directory.md)
- [Autorización de solicitudes a recursos de SignalR con Azure AD desde aplicaciones de Azure](signalr-howto-authorize-application.md)