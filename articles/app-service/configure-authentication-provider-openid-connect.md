---
title: Configuración de un proveedor de OpenID Connect (versión preliminar)
description: Aprenda a configurar un proveedor de OpenID Connect como proveedor de identidades para una aplicación de App Service o Azure Functions.
ms.topic: article
ms.date: 10/20/2021
ms.reviewer: mahender
ms.openlocfilehash: 0536d28ec91ada939a1ee0b3b7ecf6bf5e621a7a
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130236507"
---
# <a name="configure-your-app-service-or-azure-functions-app-to-login-using-an-openid-connect-provider-preview"></a>Configuración de la aplicación de App Service o Azure Functions para iniciar sesión mediante un proveedor de OpenID Connect (versión preliminar)

[!INCLUDE [app-service-mobile-selector-authentication](../../includes/app-service-mobile-selector-authentication.md)]

En este artículo se muestra cómo configurar Azure App Service o Azure Functions para usar un proveedor de autenticación personalizado que cumpla con las [especificaciones de OpenID Connect](https://openid.net/connect/). OpenID Connect (OIDC) es un estándar del sector que usan muchos proveedores de identidades (IDP). No es necesario comprender los detalles de las especificaciones para configurar la aplicación para que use un IDP que cumpla dichas especificaciones.

Puede configurar la aplicación para que use uno o varios proveedores de OIDC. Cada uno debe tener un nombre alfanumérico único en la configuración y solo uno puede actuar como destino de redirección predeterminado.

## <a name="register-your-application-with-the-identity-provider"></a><a name="register"> </a>Registro de la aplicación con el proveedor de identidades

El proveedor le solicitará que registre con él los detalles de la aplicación. Uno de estos pasos implica especificar un URI de redirección. Este URI de redirección tendrá el formato `<app-url>/.auth/login/<provider-name>/callback`. Cada proveedor de identidades debe proporcionar más instrucciones sobre cómo completar estos pasos.

> [!NOTE]
> Algunos proveedores pueden requerir pasos adicionales para su configuración y para usar los valores que proporcionan. Por ejemplo, Apple proporciona una clave privada que no se usa como secreto de cliente de OIDC y, en su lugar, se debe usar para crear un token JWT que se trata como el secreto que se proporciona en la configuración de la aplicación (consulte la sección "Creación del secreto de cliente" de la [documentación para iniciar sesión con Apple](https://developer.apple.com/documentation/sign_in_with_apple/generate_and_validate_tokens))
>

Deberá recopilar **id. de cliente** y un **secreto de cliente** para la aplicación.

> [!IMPORTANT]
> El secreto de cliente es una credencial de seguridad importante, No comparta este secreto con nadie ni lo distribuya en una aplicación cliente.
>

Además, necesitará los metadatos de OpenID Connect para el proveedor. A menudo se expone a través de un [documento de metadatos de configuración](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), que es la dirección URL del emisor del proveedor con el sufijo `/.well-known/openid-configuration`. Obtenga esta dirección URL de configuración.

Si no puede usar un documento de metadatos de configuración, tendrá que recopilar los siguientes valores por separado:

- La URL del emisor (a veces se muestra como `issuer`)
- El [punto de conexión de autorización de OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-3.1) (a veces se muestra como `authorization_endpoint`)
- El [punto de conexión de token de OAuth 2.0](https://tools.ietf.org/html/rfc6749#section-3.2) (a veces se muestra como `token_endpoint`)
- La URL del documento de [conjunto de claves web JSON de OAuth 2.0](https://tools.ietf.org/html/rfc8414#section-2) (a veces se muestra como `jwks_uri`)

## <a name="add-provider-information-to-your-application"></a><a name="configure"> </a>Adición de información de un proveedor a su aplicación

1. Inicie sesión en [Azure Portal] y vaya a la aplicación.
1. Seleccione **Autenticación** en el menú de la izquierda. Haga clic en **Agregar proveedor de identidades**.
1. Seleccione **OpenID Connect** en el menú desplegable del proveedor de identidades.
1. Proporcione el nombre alfanumérico único seleccionado anteriormente como **nombre del proveedor de OpenID**.
1. Si tiene la dirección URL del **documento de metadatos** del proveedor de identidades, proporcione ese valor en **URL de metadatos**. De lo contrario, seleccione la opción **Provide endpoints separately** (Proporcionar puntos de conexión por separado) y coloque cada dirección URL recopilada del proveedor de identidades en el campo adecuado.
1. Proporcione los valores de **Id. de cliente** y **Secreto de cliente** recopilados anteriormente en los campos adecuados.
1. Especifique un nombre de configuración de aplicación para el secreto de cliente. El secreto de cliente se almacenará como una configuración de aplicación para confirmar que los secretos se almacenan de forma segura. Puede actualizar esa configuración más adelante para usar [referencias de Key Vault](./app-service-key-vault-references.md) si desea administrar el secreto en Azure Key Vault.
1. Presione el botón **Agregar** para terminar de configurar el proveedor de identidades. 

## <a name="next-steps"></a><a name="related-content"> </a>Pasos siguientes

[!INCLUDE [app-service-mobile-related-content-get-started-users](../../includes/app-service-mobile-related-content-get-started-users.md)]
