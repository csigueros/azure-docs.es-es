---
title: Creación de una relación de confianza entre una aplicación y un proveedor de identidades externo
titleSuffix: Microsoft identity platform
description: Configure una relación de confianza entre una aplicación de Azure AD un proveedor de identidades externo.  Esto permite que una carga de trabajo de software fuera de Azure acceda a los recursos protegidos de Azure AD sin usar secretos ni certificados.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/25/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: keyam, udayh, vakarand
ms.openlocfilehash: 1d2c4631b663a57a8b1b2465c8cba6d6f81d803b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091748"
---
# <a name="configure-an-app-to-trust-an-external-identity-provider-preview"></a>Configuración de una aplicación para confiar en un proveedor de identidades externo (versión preliminar)

En este artículo se describe cómo crear una relación de confianza entre una aplicación de Azure Active Directory (Azure AD) y un proveedor de identidades externo (IdP).  A continuación, puede configurar una carga de trabajo de software externo para intercambiar un token desde el IdP externo por un token de acceso desde Plataforma de identidad de Microsoft. La carga de trabajo externa puede acceder a los recursos protegidos de Azure AD sin necesidad de administrar secretos (en escenarios admitidos).  Para más información sobre el flujo de trabajo de intercambio de tokens, lea sobre la [federación de identidades de carga de trabajo](workload-identity-federation.md).  Para establecer la relación de confianza, configure una credencial de identidad federada en el registro de la aplicación mediante Microsoft Graph.

Cualquier persona con permisos para crear un registro de aplicación y agregar un secreto o certificado puede agregar una credencial de identidad federada.  Si los **usuarios pueden registrar aplicaciones**, el interruptor en la hoja [Configuración de usuario](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) está establecido en **No**, sin embargo, no podrá crear un registro de aplicación o configurar la credencial de identidad federada.  Busque un administrador para configurar la credencial de identidad federada en su nombre.  Cualquier usuario con los roles Administrador de aplicaciones o Propietario de la aplicación puede hacerlo.

Después de configurar la aplicación para que confíe en un IdP externo, configure la carga de trabajo de software para obtener un token de acceso del proveedor de identidades de Microsoft y acceder a los recursos protegidos Azure AD.

## <a name="prerequisites"></a>Prerrequisitos
[Cree un registro de aplicación](quickstart-register-app.md) en Azure AD.  Conceda a su aplicación acceso a los recursos de Azure destinados a la carga de trabajo de software externo.  

Busque el id. de objeto de la aplicación (no el id. de aplicación (cliente)) que necesita en los pasos siguientes.  Puede encontrar el id. de objeto de la aplicación en el Azure Portal.  Vaya a la lista de [aplicaciones registradas](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) en el Azure Portal y seleccione el registro de la aplicación.  En **Información general**->**Essentials,** busque el **id. de objeto**.

Obtenga la información del IdP externo y la carga de trabajo de software, que necesita en los pasos siguientes.

## <a name="configure-a-federated-identity-credential-using-microsoft-graph"></a>Configuración de una credencial de identidad federada mediante Microsoft Graph

Inicie [Azure Cloud Shell](https://portal.azure.com/#cloudshell/) e inicie sesión en el inquilino.

### <a name="create-a-federated-identity-credential"></a>Creación de una credencial de identidad federada

Ejecute el siguiente comando para [crear una nueva credencial de identidad federada](/graph/api/application-post-federatedidentitycredentials?view=graph-rest-beta&preserve-view=true) en la aplicación (especificada por el id. de objeto de la aplicación).  

*issuer* y el *sujeto* son los elementos clave de información necesarios para configurar la relación de confianza. *issuer* es la dirección URL del proveedor de identidades externo y debe coincidir con la notificación `issuer` del token externo que se intercambia.  *subject* es el identificador de la carga de trabajo de software externo y debe coincidir con la notificación `sub` (`subject`) del token externo que se intercambia. *subject* no tiene ningún formato fijo, ya que cada IdP usa uno propio: a veces un GUID, a veces un identificador delimitado por dos puntos y, a veces, cadenas arbitrarias. La combinación de `issuer` y `subject` debe ser única en la aplicación.  Cuando la carga de trabajo de software externo solicita una plataforma de identidad de Microsoft para intercambiar el token externo por un token de acceso, los valores de *emisor* y *sujeto* de la credencial de identidad federada se comprueban con las notificaciones `issuer` y `subject` proporcionadas en el token externo. Si se supera la comprobación de validación, la plataforma de identidad de Microsoft emite un token de acceso a la carga de trabajo de software externo.

*audiences* enumera las audiencias que pueden aparecer en el token externo.  Este campo es obligatorio y el valor predeterminado es "api://AzureADTokenExchange". Indica qué plataforma de identidad de Microsoft debe aceptar en la notificación `aud` en el token entrante.  Este valor representa Azure AD en el proveedor de identidades externo y no tiene ningún valor fijo entre los proveedores de identidades: es posible que tenga que crear un nuevo registro de aplicación en el IdP para que sirva como audiencia de este token.

*name* es el identificador único de la credencial de identidad federada, que tiene un límite de caracteres de 120 caracteres y debe ser descriptivo para direcciones URL. Una vez creado, es inmutable.

*description* es la descripción no validada proporcionada por el usuario de la credencial de identidad federada. 

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

> [!IMPORTANT]
> Si agrega accidentalmente la información de carga de trabajo externa incorrecta en la configuración del *subject*, la credencial de identidad federada se crea correctamente sin errores.  El error no se hace evidente hasta que se produce un error en el intercambio de tokens.

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
az rest -m DELETE  -u 'https://graph.microsoft.com/beta/applications/f6475511-fd81-4965-a00e-41e7792b7b9c/federatedIdentityCredentials/51ecf9c3-35fc-4519-a28a-8c27c6178bca' 

```

## <a name="next-steps"></a>Pasos siguientes
Para más información, lea sobre cómo Azure AD usa la [concesión de credenciales de cliente de OAuth 2.0](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) y una aserción de cliente emitida por otro IdP para obtener un token.