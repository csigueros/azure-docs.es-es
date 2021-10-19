---
title: Autenticación personalizada en Azure Static Web Apps
description: Aprenda a configurar la autenticación personalizada de Azure Static Web Apps.
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 10/08/2021
ms.openlocfilehash: 49921eba1a7f4c6c898eaadf1d8743d8d210057a
ms.sourcegitcommit: 216b6c593baa354b36b6f20a67b87956d2231c4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2021
ms.locfileid: "129729812"
---
# <a name="custom-authentication-in-azure-static-web-apps"></a>Autenticación personalizada en Azure Static Web Apps

Azure Static Web Apps proporciona [autenticación administrada](authentication-authorization.md) que usa registros de proveedor administrados por Azure. Para habilitar más flexibilidad sobre el registro, puede reemplazar los valores predeterminados por un registro personalizado.

- La autenticación personalizada también le permite [configurar proveedores personalizados](./authentication-custom.md?tabs=openid-connect#configure-a-custom-identity-provider) que admitan [OpenID Connect](https://openid.net/connect/). Esta configuración permite el registro de varios proveedores externos.

- El uso de registros personalizados deshabilita todos los proveedores preconfigurados.

> [!NOTE]
> La autenticación personalizada solo está disponible en el plan Estándar de Azure Static Web Apps.

## <a name="configure-a-custom-identity-provider"></a>Configuración de un proveedor de identidades personalizados

Los proveedores de identidad personalizados se configuran en la sección `auth` del [archivo de configuración](configuration.md).

Para evitar colocar secretos en el control de código fuente, la configuración busca en la [configuración de la aplicación](application-settings.md) un nombre coincidente en el archivo de configuración. También puede optar por almacenar los secretos en [Azure Key Vault](./key-vault-secrets.md).

# <a name="azure-active-directory"></a>[Azure Active Directory](#tab/aad)

Para crear el registro, empiece por crear la siguiente [configuración de la aplicación](application-settings.md):

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `AAD_CLIENT_ID` | Identificador de la aplicación (cliente) del registro de la aplicación de Azure AD |
| `AAD_CLIENT_SECRET` | Secreto de cliente del registro de la aplicación de Azure AD |

Use el ejemplo siguiente para configurar el proveedor en el [archivo de configuración](configuration.md).

Los proveedores de Azure Active Directory están disponibles en dos versiones diferentes. La versión 1 define explícitamente `userDetailsClaim`, que permite que la carga devuelva información del usuario. Por el contrario, la versión 2 devuelve información del usuario de forma predeterminada y la designa con `v2.0` en la dirección URL `openIdIssuer`.

### <a name="azure-active-directory-version-1"></a>Azure Active Directory versión 1

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>",
          "clientIdSettingName": "AAD_CLIENT_ID",
          "clientSecretSettingName": "AAD_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Recuerde reemplazar `<TENANT_ID>` por su identificador de inquilino de Azure Active Directory.

### <a name="azure-active-directory-version-2"></a>Azure Active Directory versión 2

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>/v2.0",
          "clientIdSettingName": "AAD_CLIENT_ID",
          "clientSecretSettingName": "AAD_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Recuerde reemplazar `<TENANT_ID>` por su identificador de inquilino de Azure Active Directory.

Para más información sobre cómo configurar Azure Active Directory, consulte la [documentación sobre autenticación y autorización de App Service](../app-service/configure-authentication-provider-aad.md#-option-2-use-an-existing-registration-created-separately) relacionada con el uso de un registro existente.

> [!NOTE]
> Aunque la sección de configuración de Azure Active Directory es `azureActiveDirectory`, la plataforma lo asocia a `aad` en las direcciones URL para iniciar sesión, cerrar sesión y purgar información del usuario. Consulte la sección de [autenticación y autorización](authentication-authorization.md) para obtener más información.

# <a name="apple"></a>[Apple](#tab/apple)

Para crear el registro, empiece por crear la siguiente [configuración de la aplicación](application-settings.md):

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `APPLE_CLIENT_ID` | Identificador de cliente de Apple |
| `APPLE_CLIENT_SECRET` | Secreto de cliente de Apple |

Use el ejemplo siguiente para configurar el proveedor en el [archivo de configuración](configuration.md).

```json
{
  "auth": {
    "identityProviders": {
      "apple": {
        "registration": {
          "clientIdSettingName": "APPLE_CLIENT_ID",
          "clientSecretSettingName": "APPLE_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Para más información sobre cómo configurar Apple como proveedor de autenticación, consulte la [documentación sobre autenticación y autorización de App Service](../app-service/configure-authentication-provider-apple.md).

# <a name="facebook"></a>[Facebook](#tab/facebook)

Para crear el registro, empiece por crear la siguiente [configuración de la aplicación](application-settings.md):

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `FACEBOOK_APP_ID` | Identificador de la aplicación Facebook |
| `FACEBOOK_APP_SECRET` | Secreto de la aplicación Facebook |

Use el ejemplo siguiente para configurar el proveedor en el [archivo de configuración](configuration.md).

```json
{
  "auth": {
    "identityProviders": {
      "facebook": {
        "registration": {
          "appIdSettingName": "FACEBOOK_APP_ID",
          "appSecretSettingName": "FACEBOOK_APP_SECRET"
        }
      }
    }
  }
}
```

Para más información sobre cómo configurar Facebook como proveedor de autenticación, consulte la [documentación sobre autenticación y autorización de App Service](../app-service/configure-authentication-provider-facebook.md).

# <a name="github"></a>[GitHub](#tab/github)


Para crear el registro, empiece por crear la siguiente [configuración de la aplicación](application-settings.md):

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `GITHUB_CLIENT_ID` | Identificador de cliente de GitHub |
| `GITHUB_CLIENT_SECRET` | Secreto de cliente de GitHub |

Use el ejemplo siguiente para configurar el proveedor en el [archivo de configuración](configuration.md).

```json
{
  "auth": {
    "identityProviders": {
      "github": {
        "registration": {
          "clientIdSettingName": "GITHUB_CLIENT_ID",
          "clientSecretSettingName": "GITHUB_CLIENT_SECRET"
        }
      }
    }
  }
}
```

# <a name="google"></a>[Google](#tab/google)


Para crear el registro, empiece por crear la siguiente [configuración de la aplicación](application-settings.md):

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `GOOGLE_CLIENT_ID` | Identificador de cliente de Google |
| `GOOGLE_CLIENT_SECRET` | Secreto de cliente de Google |

Use el ejemplo siguiente para configurar el proveedor en el [archivo de configuración](configuration.md).

```json
{
  "auth": {
    "identityProviders": {
      "google": {
        "registration": {
          "clientIdSettingName": "GOOGLE_CLIENT_ID",
          "clientSecretSettingName": "GOOGLE_CLIENT_SECRET"
        }
      }
    }
  }
}
```

Para más información sobre cómo configurar Google como proveedor de autenticación, consulte la [documentación sobre autenticación y autorización de App Service](../app-service/configure-authentication-provider-google.md).

# <a name="twitter"></a>[Twitter](#tab/twitter)

Para crear el registro, empiece por crear la siguiente [configuración de la aplicación](application-settings.md):

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `TWITTER_CONSUMER_KEY` | Clave de consumidor de Twitter |
| `TWITTER_CONSUMER_SECRET` | Secreto de consumidor de Twitter |

Use el ejemplo siguiente para configurar el proveedor en el [archivo de configuración](configuration.md).

```json
{
  "auth": {
    "identityProviders": {
      "twitter": {
        "registration": {
          "consumerKeySettingName": "TWITTER_CONSUMER_KEY",
          "consumerSecretSettingName": "TWITTER_CONSUMER_SECRET"
        }
      }
    }
  }
}
```

Para más información sobre cómo configurar Twitter como proveedor de autenticación, consulte la [documentación sobre autenticación y autorización de App Service](../app-service/configure-authentication-provider-twitter.md).

# <a name="openid-connect"></a>[OpenID Connect](#tab/openid-connect)

Puede configurar Azure Static Web Apps para usar un proveedor de autenticación personalizado que cumpla la [especificación OpenID Connect (OIDC)](https://openid.net/connect/). Los pasos siguientes son necesarios para usar un proveedor de OIDC personalizado.

- Se permiten uno o varios proveedores de OIDC.
- Cada proveedor debe tener un nombre único en la configuración.
- Solo un proveedor puede actuar como destino de redirección predeterminado.

### <a name="register-your-application-with-the-identity-provider"></a>Registro de la aplicación con el proveedor de identidades

Es necesario registrar los detalles de la aplicación con un proveedor de identidades. Consulte con el proveedor los pasos necesarios para generar un **identificador de cliente** y un **secreto de cliente** para la aplicación.

Una vez que se ha registrado la aplicación con el proveedor de identidades, cree los siguientes secretos de aplicación en la [configuración de la aplicación](application-settings.md) de la aplicación web estática:

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `MY_PROVIDER_CLIENT_ID` | Identificador de cliente generado por el proveedor de autenticación de la aplicación web estática |
| `MY_PROVIDER_CLIENT_SECRET` | Secreto de cliente generado por el registro personalizado del proveedor de autenticación de la aplicación web estática |

Si registra proveedores adicionales, cada uno de ellos necesita un identificador de cliente asociado y un almacén de secretos de cliente en la configuración de la aplicación.

> [!IMPORTANT]
> Los secretos de aplicación son credenciales de seguridad confidenciales. No comparta este secreto con nadie, distribúyalo dentro de una aplicación cliente o insértelo en el control de código fuente.

Una vez que tenga las credenciales de registro, siga estos pasos para crear un registro personalizado.

1. Necesita los metadatos de OpenID Connect para el proveedor. A menudo, esta información se expone a través de un [documento de metadatos de configuración](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), que es la _dirección URL del emisor_ del proveedor con el sufijo `/.well-known/openid-configuration`. Obtenga esta dirección URL de configuración.

1. Agregue una sección `auth` del [archivo de configuración](configuration.md) con un bloque de configuración para los proveedores de OIDC y la definición del proveedor.

   ```json
   {
     "auth": {
       "identityProviders": {
         "customOpenIdConnectProviders": {
           "myProvider": {
             "registration": {
               "clientIdSettingName": "MY_PROVIDER_CLIENT_ID",
               "clientCredential": {
                 "clientSecretSettingName": "MY_PROVIDER_CLIENT_SECRET"
               },
               "openIdConnectConfiguration": {
                 "wellKnownOpenIdConfiguration": "https://<PROVIDER_ISSUER_URL>/.well-known/openid-configuration"
               }
             },
             "login": {
               "nameClaimType": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name",
               "scopes": [],
               "loginParameterNames": []
             }
           }
         }
       }
     }
   }
   ```

  - El nombre del proveedor, `myProvider` en este ejemplo, es el identificador único que usa Azure Static Web Apps.
  - Recuerde reemplazar `<PROVIDER_ISSUER_URL>` por la ruta de acceso a la _URL del emisor_ del proveedor.
  - El objeto `login` le permite proporcionar valores para: ámbitos personalizados, parámetros de inicio de sesión o notificaciones personalizadas.

---

## <a name="authentication-callbacks"></a>Devoluciones de llamada de autenticación

Los proveedores de identidad personalizados requieren una dirección URL de redireccionamiento para completar la solicitud de inicio o cierre de sesión. La mayoría de los proveedores requieren que agregue las direcciones URL de devolución de llamada a una lista de permitidos. Los siguientes puntos de conexión están disponibles como destinos de redirección.

| Tipo   | Patrón de URL                                                 |
| ------ | ----------------------------------------------------------- |
| Iniciar sesión  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

Si usa Azure Active Directory, use `aad` como valor del marcador de posición `<PROVIDER_NAME_IN_CONFIG>`.

> [!Note]
> Estas direcciones URL las proporciona Azure Static Web Apps para recibir la respuesta del proveedor de autenticación, no es necesario que cree páginas en estas rutas.

## <a name="login-logout-and-purging-user-details"></a>Inicio de sesión, cierre de sesión y purga de los detalles del usuario

Para usar un proveedor de identidad personalizado, use los siguientes patrones de dirección URL.

| Acción             | Patrón                                  |
| ------------------ | ---------------------------------------- |
| Iniciar sesión              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| Purga de los detalles del usuario | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

Si usa Azure Active Directory, use `aad` como valor del marcador de posición `<PROVIDER_NAME_IN_CONFIG>`.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Protección de secretos de autenticación en Azure Key Vault](./key-vault-secrets.md)
