---
title: Crear una relación de confianza entre una aplicación y GitHub
titleSuffix: Microsoft identity platform
description: Establezca una relación de confianza entre una aplicación de Azure AD y un repositorio de GitHub.  Esto permite que un flujo de trabajo de GitHub acceda a los recursos protegidos de Azure AD sin usar secretos ni certificados.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/18/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: keyam, udayh, vakarand
ms.openlocfilehash: 3e1e3d4857555b648b841a544e02346e6144bb2b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091749"
---
# <a name="configure-an-app-to-trust-a-github-repo-preview"></a>Configuración de una aplicación para confiar en un repositorio de GitHub (versión preliminar)

En este artículo se describe cómo crear una relación de confianza entre una aplicación de Azure Active Directory (Azure AD) y un repositorio de GitHub.  A continuación, puede configurar un flujo de trabajo para acciones de GitHub a fin de intercambiar un token de GitHub por un token de acceso desde la Plataforma de identidad de Microsoft y acceder a los recursos protegidos de Azure AD sin necesidad de administrar secretos.  Para más información sobre el flujo de trabajo de intercambio de tokens, lea sobre la [federación de identidades de carga de trabajo](workload-identity-federation.md).  Para establecer la relación de confianza, configure una credencial de identidad federada en el registro de la aplicación mediante Azure Portal o Microsoft Graph.

Cualquier persona con permisos para crear un registro de aplicación y agregar un secreto o certificado puede agregar una credencial de identidad federada.  Si los **usuarios pueden registrar aplicaciones**, el interruptor en la hoja [Configuración de usuario](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) está establecido en **No**, sin embargo, no podrá crear un registro de aplicación o configurar la credencial de identidad federada.  Busque un administrador para configurar la credencial de identidad federada en su nombre.  Cualquier usuario con los roles Administrador de aplicaciones o Propietario de la aplicación puede hacerlo.

Después de configurar la aplicación para que confíe en un repositorio de GitHub, [configure la carga de trabajo de acciones de GitHub](/azure/developer/github/connect-from-azure) para obtener un token de acceso del proveedor de identidades de Microsoft y acceder a los recursos protegidos Azure AD.

## <a name="prerequisites"></a>Prerrequisitos
[Cree un registro de aplicación](quickstart-register-app.md) en Azure AD.  Conceda a su aplicación acceso a los recursos de Azure destinados a la carga de trabajo de GitHub.  

Busque el id. de objeto de la aplicación (no el id. de aplicación (cliente)) que necesita en los pasos siguientes.  Puede encontrar el id. de objeto de la aplicación en el Azure Portal.  Vaya a la lista de [aplicaciones registradas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) en el Azure Portal y seleccione el registro de la aplicación.  En **Información general**->**Essentials,** busque el **id. de objeto**.

Obtenga la información de organización, repositorio y entorno del repositorio GitHub que necesita en los pasos siguientes.

## <a name="configure-a-federated-identity-credential"></a>Configuración de una credencial de identidad federada

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Inicie sesión en [Azure Portal](https://portal.azure.com/).  Vaya a **Registros de aplicaciones** y abra la aplicación que desea configurar.

Vaya a **Certificados y secretos**.  En la pestaña **Credenciales federadas**, seleccione **Agregar credencial**.  Se abre la hoja de **Agregar una credencial**.

En el cuadro desplegable **Escenario de credenciales federadas**, seleccione **Acciones de GitHub que implementan recursos de Azure**.

Especifique la **organización** y el **repositorio** para el flujo de trabajo de las acciones de GitHub.  

En **Tipo de entidad**, seleccione **Entorno**, **Rama**, **Solicitud de incorporación de cambios** o **Etiqueta** y especifique el valor.

Agregue un **Nombre** a la credencial federada.

Los campos de **Emisor**, **Audiencias** e **Identificador de sujeto** se rellenarán automáticamente en función de los valores especificados.

Haga clic en **Agregar** para configurar la credencial federada.

:::image type="content" source="media/workload-identity-federation-create-trust-github/add-credential.png" alt-text="Captura de pantalla de la ventana Agregar una credencial, en la que se muestran los valores de ejemplo." :::

> [!NOTE]
> Si configura accidentalmente el repositorio de GitHub de otra persona en la configuración del *asunto* (escriba un error tipográfico que coincida con otro repositorio), puede crear correctamente la credencial de identidad federada.  Sin embargo, en la configuración de GitHub se producirá un error porque no se puede acceder al repositorio de otra persona.

> [!IMPORTANT]
> Los valores de configuración de la **Organización**, **Repositorio** y **Tipo de entidad** deben coincidir exactamente con la configuración del flujo de trabajo de GitHub. De lo contrario, la plataforma de identidad de Microsoft examinará el token externo entrante y rechazará el intercambio por un token de acceso.  No se producirá un error, se producirá un error en el intercambio sin errores.

# <a name="microsoft-graph"></a>[Microsoft Graph](#tab/microsoft-graph)
Inicie [Azure Cloud Shell](https://portal.azure.com/#cloudshell/) e inicie sesión en el inquilino.

### <a name="create-a-federated-identity-credential"></a>Creación de una credencial de identidad federada

Ejecute el siguiente comando para [crear una nueva credencial de identidad federada](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) en la aplicación (especificada por el id. de objeto de la aplicación).  El *issuer* identifica a GitHub como el emisor del token externo.  *subject* identifica la organización GitHub, el repositorio y el entorno del flujo de trabajo GitHub Actions.  Cuando el flujo de trabajo de Acciones de GitHub solicita a la plataforma de identidad de Microsoft intercambiar un token de GitHub por un token de acceso, los valores de la credencial de identidad federada se comprueban con el token de GitHub proporcionado.

```azurecli
az rest --method POST --uri 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' --body '{"name":"Testing","issuer":"https://token.actions.githubusercontent.com/","subject":"repo:octo-org/octo-repo:environment:Production","description":"Testing","audiences":["api://AzureADTokenExchange"]}' 
```

Y obtiene la respuesta:
```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials/$entity",
  "audiences": [
    "api://AzureADTokenExchange"
  ],
  "description": "Testing",
  "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
  "issuer": "https://token.actions.githubusercontent.com/",
  "name": "Testing",
  "subject": "repo:octo-org/octo-repo:environment:Production"
}
```

*name*: el nombre de la aplicación de Azure.

*issuer*: ruta de acceso al proveedor OIDC de GitHub: `https://token.actions.githubusercontent.com/`. La aplicación de Azure confiará en este emisor.

*subject*: antes de que Azure conceda un token de acceso, la solicitud debe coincidir con las condiciones definidas aquí.
- Para trabajos vinculados a un entorno: `repo:< Organization/Repository >:environment:< Name >`
- En el caso de los trabajos no vinculados a un entorno, incluya la ruta de acceso de referencia para una rama o etiqueta en función de la ruta de acceso de referencia usada para desencadenar el flujo de trabajo: `repo:< Organization/Repository >:ref:< ref path>`.  Por ejemplo, `repo:n-username/ node_express:ref:refs/heads/my-branch` o `repo:n-username/ node_express:ref:refs/tags/my-tag`.
- En el caso de los flujos de trabajo desencadenados por un evento de solicitud de incorporación de cambios: `repo:< Organization/Repository >:pull-request`.

*audiences*: `api://AzureADTokenExchange` es el valor requerido.

> [!NOTE]
> Si configura accidentalmente el repositorio de GitHub de otra persona en la configuración del *asunto* (escriba un error tipográfico que coincida con otro repositorio), puede crear correctamente la credencial de identidad federada.  Sin embargo, en la configuración de GitHub se producirá un error porque no se puede acceder al repositorio de otra persona.

> [!IMPORTANT]
> Los valores de configuración del *asunto* deben coincidir exactamente con la configuración del flujo de trabajo de GitHub.  De lo contrario, la plataforma de identidad de Microsoft examinará el token externo entrante y rechazará el intercambio por un token de acceso.  No se producirá un error, se producirá un error en el intercambio sin errores.

### <a name="list-federated-identity-credentials-on-an-app"></a>Enumeración de credenciales de identidad federada en una aplicación

Ejecute el siguiente comando para [enumerar las credenciales de identidad federada](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) de una aplicación (especificadas por el id. de objeto de la aplicación):

```azurecli
az rest -m GET -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials' 
```

Y obtiene una respuesta similar a la siguiente:

```azurecli
{
  "@odata.context": "https://graph.microsoft.com/beta/$metadata#applications('f6475511-fd81-4965-a00e-41e7792b7b9c')/federatedIdentityCredentials",
  "value": [
    {
      "audiences": [
        "api://AzureADTokenExchange"
      ],
      "description": "Testing",
      "id": "1aa3e6a7-464c-4cd2-88d3-90db98132755",
      "issuer": "https://token.actions.githubusercontent.com/",
      "name": "Testing",
      "subject": "repo:octo-org/octo-repo:environment:Production"
    }
  ]
}
```

### <a name="delete-a-federated-identity-credential"></a>Eliminación de una credencial de identidad federada

Ejecute el siguiente comando para [eliminar una credencial de identidad federada](/graph/api/application-list-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) de una aplicación (especificada por el id. de objeto de la aplicación):

```azurecli
az rest -m DELETE  -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials/1aa3e6a7-464c-4cd2-88d3-90db98132755' 
```
---

## <a name="get-the-application-client-id-and-tenant-id-from-the-azure-portal"></a>Obtenga el identificador de la aplicación (cliente) y el identificador de inquilino de Azure Portal

Antes de configurar el flujo de trabajo de las Acciones de GitHub, obtenga los valores *tenant-id* y *client-id* del registro de la aplicación.  También puede encontrar estos valores en Azure Portal. Vaya a la lista de [aplicaciones registradas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) y seleccione el registro de la aplicación.  En la página de **Información general**->**Essentials**, encuentre el **identificador de la aplicación (cliente)** y **el identificador de directorio (inquilino)** . Establezca estos valores en el entorno de GitHub que se usará en la acción de inicio de sesión de Azure para el flujo de trabajo.  

## <a name="next-steps"></a>Pasos siguientes
[Configure un flujo de trabajo de Acciones de GitHub](/azure/developer/github/connect-from-azure) para obtener un token de acceso del proveedor de identidades de Microsoft y acceder a los recursos de Azure.

Lea la [documentación de Acciones de GitHub](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure) para obtener más información sobre cómo configurar el flujo de trabajo de Acciones de GitHub para obtener un token de acceso del proveedor de identidades de Microsoft y acceder a los recursos de Azure.