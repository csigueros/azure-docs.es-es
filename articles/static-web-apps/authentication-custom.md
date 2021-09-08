---
title: Autenticación personalizada en Azure Static Web Apps
description: Aprenda a configurar la autenticación personalizada de Azure Static Web Apps.
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: b09d1f6d6cdd5838f4c43e7cb05f63d8efd3e7f9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121723406"
---
# <a name="custom-authentication-in-azure-static-web-apps"></a>Autenticación personalizada en Azure Static Web Apps

Azure Static Web Apps proporciona [autenticación administrada](authentication-authorization.md) que usa registros de proveedor administrados por Azure. Para habilitar más flexibilidad sobre el registro, puede reemplazar los valores predeterminados por un registro personalizado.

- La autenticación personalizada también le permite [configurar proveedores personalizados](#configure-a-custom-openid-connect-provider) que admitan [OpenID Connect](https://openid.net/connect/). Esta configuración permite el registro de varios proveedores externos.

- El uso de registros personalizados deshabilita todos los proveedores preconfigurados.

- Concretamente, para los registros de Azure Active Directory, tiene la opción de proporcionar un inquilino, lo que le permite omitir el [flujo de invitación](./authentication-authorization.md#role-management) de la administración de grupos.

> [!NOTE]
> La autenticación personalizada solo está disponible en el plan Estándar de Azure Static Web Apps.

## <a name="override-pre-configured-provider"></a>Invalidación del proveedor preconfigurado

Los valores que se usan para invalidar un proveedor se configuran en la sección `auth` del [archivo de configuración](configuration.md).

Para evitar colocar secretos en el control de código fuente, la configuración busca en la [configuración de la aplicación](application-settings.md) un nombre coincidente en el archivo de configuración. También puede optar por almacenar los secretos en [Azure Key Vault](./key-vault-secrets.md).

### <a name="configuration"></a>Configuración

Para configurar la autenticación personalizada, debe hacer referencia a algunos secretos almacenados como [configuración de la aplicación](./application-settings.md). 

# <a name="azure-active-directory"></a>[Azure Active Directory](#tab/aad)

Los proveedores de Azure Active Directory están disponibles en dos versiones diferentes. La versión 1 define explícitamente `userDetailsClaim`, que permite que la carga devuelva información del usuario. Por el contrario, la versión 2 devuelve información del usuario de forma predeterminada y la designa con `v2.0` en la dirección URL `openIdIssuer`.

Para crear el registro, empiece por crear la siguiente configuración de la aplicación:

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `AAD_CLIENT_ID` | Identificador de la aplicación (cliente) del registro de la aplicación de Azure AD |
| `AAD_CLIENT_SECRET` | Secreto de cliente del registro de la aplicación de Azure AD |

#### <a name="azure-active-directory-version-1"></a>Azure Active Directory versión 1

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

#### <a name="azure-active-directory-version-2"></a>Azure Active Directory versión 2

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

Para más información sobre cómo configurar Azure Active Directory, consulte la [documentación sobre autenticación y autorización de App Service](../app-service/configure-authentication-provider-aad.md).

> [!NOTE]
> Aunque la sección de configuración de Azure Active Directory es `azureActiveDirectory`, la plataforma lo asocia a `aad` en las direcciones URL para iniciar sesión, cerrar sesión y purgar información del usuario. Consulte [Autenticación y autorización](authentication-authorization.md) para obtener más información.

# <a name="apple"></a>[Apple](#tab/apple)

Para crear el registro, empiece por crear la siguiente configuración de la aplicación:

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `APPLE_CLIENT_ID` | Identificador de cliente de Apple |
| `APPLE_CLIENT_SECRET` | Secreto de cliente de Apple |

Use el ejemplo siguiente para configurar el proveedor.

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

Para crear el registro, empiece por crear la siguiente configuración de la aplicación:

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `FACEBOOK_APP_ID` | Identificador de la aplicación Facebook |
| `FACEBOOK_APP_SECRET` | Secreto de la aplicación Facebook |

Use el ejemplo siguiente para configurar el proveedor.

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


Para crear el registro, empiece por crear la siguiente configuración de la aplicación:

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `GITHUB_CLIENT_ID` | Identificador de cliente de GitHub |
| `GITHUB_CLIENT_SECRET` | Secreto de cliente de GitHub |

Use el ejemplo siguiente para configurar el proveedor.

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


Para crear el registro, empiece por crear la siguiente configuración de la aplicación:

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `GOOGLE_CLIENT_ID` | Identificador de cliente de Google |
| `GOOGLE_CLIENT_SECRET` | Secreto de cliente de Google |

Use el ejemplo siguiente para configurar el proveedor.

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

Para crear el registro, empiece por crear la siguiente configuración de la aplicación:

| Nombre de la opción de configuración | Valor |
| --- | --- |
| `TWITTER_CONSUMER_KEY` | Clave de consumidor de Twitter |
| `TWITTER_CONSUMER_SECRET` | Secreto de consumidor de Twitter |

Use el ejemplo siguiente para configurar el proveedor.

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

---

## <a name="configure-a-custom-openid-connect-provider"></a>Configuración de un proveedor de OpenID Connect personalizado

En esta sección se muestra cómo configurar Azure Static Web Apps para usar un proveedor de autenticación personalizado que cumpla la [especificación OpenID Connect (OIDC)](https://openid.net/connect/). Los pasos siguientes son necesarios para usar un proveedor de OIDC personalizado.

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

### <a name="login-logout-and-purging-user-details"></a>Inicio de sesión, cierre de sesión y purga de los detalles del usuario

Para usar un proveedor de OIDC personalizado, use los siguientes patrones de dirección URL.

| Acción             | Patrón                                  |
| ------------------ | ---------------------------------------- |
| Iniciar sesión              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| Purga de los detalles del usuario | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

Si usa Azure Active Directory, use `aad` como valor del marcador de posición `<AUTHENTICATION_PROVIDER_NAME>`.

### <a name="authentication-callbacks"></a>Devoluciones de llamada de autenticación

Los proveedores de OIDC personalizados requieren la URL de redireccionamiento para completar la solicitud de inicio o cierre de sesión. Los siguientes puntos de conexión están disponibles como destinos de redirección.

| Tipo   | Patrón de URL                                                 |
| ------ | ----------------------------------------------------------- |
| Iniciar sesión  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

Si usa Azure Active Directory, use `aad` como valor del marcador de posición `<AUTHENTICATION_PROVIDER_NAME>`.

> [!Note]
> Estas direcciones URL las proporciona Azure Static Web Apps para recibir la respuesta del proveedor de autenticación, no es necesario que cree páginas en estas rutas.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Protección de secretos de autenticación en Azure Key Vault](./key-vault-secrets.md)
