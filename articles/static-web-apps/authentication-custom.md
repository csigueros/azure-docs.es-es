---
title: Autenticación personalizada en Azure Static Web Apps
description: Aprenda a configurar la autenticación personalizada de Azure Static Web Apps.
services: static-web-apps
author: aaronpowell
ms.author: aapowell
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/07/2021
ms.openlocfilehash: e4583c6474872cc1de909d86d812aa9ac9630536
ms.sourcegitcommit: 67cdbe905eb67e969d7d0e211d87bc174b9b8dc0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111854583"
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

Las tablas siguientes contienen las distintas opciones de configuración de cada proveedor.

# <a name="azure-active-directory"></a>[Azure Active Directory](#tab/aad)

| Ruta de acceso de campo                             | Descripción                                                                                                               |
| -------------------------------------- | ------------------------------------------------------------------------------------------------------------- |
| `registration.openIdIssuer`            | Punto de conexión para la configuración de OpenID del inquilino de AAD.                                                  |
| `registration.clientIdSettingName`     | Nombre de la configuración de la aplicación que contiene el identificador de aplicación (cliente) para el registro de aplicaciones de Azure AD. |
| `registration.clientSecretSettingName` | Nombre de la configuración de la aplicación que contiene el secreto de cliente para el registro de aplicaciones de Azure AD.           |

```json
{
  "auth": {
    "identityProviders": {
      "azureActiveDirectory": {
        "registration": {
          "openIdIssuer": "https://login.microsoftonline.com/<TENANT_ID>",
          "clientIdSettingName": "<AAD_CLIENT_ID>",
          "clientSecretSettingName": "<AAD_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Azure Active Directory ofrece puntos de conexión con versiones que afectan a cómo se configura el registro. Si usa AAD v1 (el punto de conexión del emisor no termina en "/v2.0"), deberá agregar la siguiente entrada `userDetailsClaim` a la configuración en el objeto `"azureActiveDirectory"`.

```json
"azureActiveDirectory": {
  "registration": { ... },
  "userDetailsClaim": "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" 
}
```

Para más información sobre cómo configurar Azure Active Directory, consulte la [documentación sobre autenticación y autorización de App Service](../app-service/configure-authentication-provider-aad.md).

# <a name="apple"></a>[Apple](#tab/apple)

| Ruta de acceso de campo                             | Descripción                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | Nombre de la configuración de la aplicación que contiene el identificador de cliente.                                       |
| `registration.clientSecretSettingName` | Nombre de la configuración de la aplicación que contiene el secreto de cliente.                                   |

```json
{
  "auth": {
    "identityProviders": {
      "apple": {
        "registration": {
          "clientIdSettingName": "<APPLE_CLIENT_ID>",
          "clientSecretSettingName": "<APPLE_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Para más información sobre cómo configurar Apple como proveedor de autenticación, consulte la [documentación sobre autenticación y autorización de App Service](../app-service/configure-authentication-provider-apple.md).

# <a name="facebook"></a>[Facebook](#tab/facebook)

| Ruta de acceso de campo                          | Descripción                                                                            |
| ----------------------------------- | -------------------------------------------------------------------------------------- |
| `registration.appIdSettingName`     | Nombre de la configuración de la aplicación que contiene el identificador de aplicación.                             |
| `registration.appSecretSettingName` | Nombre de la configuración de la aplicación que contiene el secreto de aplicación.                         |

```json
{
  "auth": {
    "identityProviders": {
      "facebook": {
        "registration": {
          "appIdSettingName": "<FACEBOOK_APP_ID>",
          "appSecretSettingName": "<FACEBOOK_APP_SECRET>"
        }
      }
    }
  }
}
```

Para más información sobre cómo configurar Facebook como proveedor de autenticación, consulte la [documentación sobre autenticación y autorización de App Service](../app-service/configure-authentication-provider-facebook.md).

# <a name="github"></a>[GitHub](#tab/github)

| Ruta de acceso de campo                             | Descripción                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | Nombre de la configuración de la aplicación que contiene el identificador de cliente.                                |
| `registration.clientSecretSettingName` | Nombre de la configuración de la aplicación que contiene el secreto de cliente.                            |

```json
{
  "auth": {
    "identityProviders": {
      "github": {
        "registration": {
          "clientIdSettingName": "<GITHUB_CLIENT_ID>",
          "clientSecretSettingName": "<GITHUB_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

# <a name="google"></a>[Google](#tab/google)

| Ruta de acceso de campo                             | Descripción                                                                                  |
| -------------------------------------- | -------------------------------------------------------------------------------------------- |
| `registration.clientIdSettingName`     | Nombre de la configuración de la aplicación que contiene el identificador de cliente.                                |
| `registration.clientSecretSettingName` | Nombre de la configuración de la aplicación que contiene el secreto de cliente.                            |

```json
{
  "auth": {
    "identityProviders": {
      "google": {
        "registration": {
          "clientIdSettingName": "<GOOGLE_CLIENT_ID>",
          "clientSecretSettingName": "<GOOGLE_CLIENT_SECRET>"
        }
      }
    }
  }
}
```

Para más información sobre cómo configurar Google como proveedor de autenticación, consulte la [documentación sobre autenticación y autorización de App Service](../app-service/configure-authentication-provider-google.md).

# <a name="twitter"></a>[Twitter](#tab/twitter)

| Ruta de acceso de campo                               | Descripción                                                                                        |
| ---------------------------------------- | -------------------------------------------------------------------------------------------------- |
| `registration.consumerKeySettingName`    | Nombre de la configuración de la aplicación que contiene la clave de consumidor.                                   |
| `registration.consumerSecretSettingName` | Nombre de la configuración de la aplicación que contiene el secreto de consumidor.                                |

```json
{
  "auth": {
    "identityProviders": {
      "twitter": {
        "registration": {
          "consumerKeySettingName": "<TWITTER_CONSUMER_KEY>",
          "consumerSecretSettingName": "<TWITTER_CONSUMER_SECRET>"
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

> [!IMPORTANT]
> Los secretos de aplicación son credenciales de seguridad confidenciales. No comparta este secreto con nadie, distribúyalo dentro de una aplicación cliente o insértelo en el control de código fuente.

Una vez que tenga las credenciales de registro, siga estos pasos para crear un registro personalizado.

1. Agregue el identificador y el secreto de cliente como [configuración de la aplicación](application-settings.md) mediante los nombres de configuración que prefiera. Tome nota de estos nombres para más adelante. Como alternativa, el identificador de cliente se puede incluir en el archivo de configuración.

1. Necesita los metadatos de OpenID Connect para el proveedor. A menudo, esta información se expone a través de un [documento de metadatos de configuración](https://openid.net/specs/openid-connect-discovery-1_0.html#ProviderConfig), que es la _dirección URL del emisor_ del proveedor con el sufijo `/.well-known/openid-configuration`. Obtenga esta dirección URL de configuración.

1. Agregue una sección `auth` del [archivo de configuración](configuration.md) con un bloque de configuración para los proveedores de OIDC y la definición del proveedor.

   ```json
   {
     "auth": {
       "identityProviders": {
         "customOpenIdConnectProviders": {
           "myProvider": {
             "registration": {
               "clientIdSettingName": "<MY_PROVIDER_CLIENT_ID_SETTING_NAME>",
               "clientCredential": {
                 "clientSecretSettingName": "<MY_PROVIDER_CLIENT_SECRET_SETTING_NAME>"
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

  Cambie los siguientes tokens de reemplazo en el código por sus valores.

  | Reemplace esto... | por esto... |
  | --- | --- |
  | `<MY_PROVIDER_CLIENT_ID_SETTING_NAME>` | Nombre de la configuración de la aplicación asociado al identificador de cliente generado a partir del registro personalizado. |
  | `<MY_PROVIDER_CLIENT_SECRET_SETTING_NAME>` | Nombre de la configuración de la aplicación asociado al secreto de cliente generado a partir del registro personalizado. |
  | `<PROVIDER_ISSUER_URL>` | Ruta de acceso a la _dirección URL del emisor_ del proveedor. |

- El nombre del proveedor, `myProvider` en este ejemplo, es el identificador único que usa Azure Static Web Apps.
- El objeto `login` le permite proporcionar valores para: ámbitos personalizados, parámetros de inicio de sesión o notificaciones personalizadas.

### <a name="login-logout-and-purging-user-details"></a>Inicio de sesión, cierre de sesión y purga de los detalles del usuario

Para usar un proveedor de OIDC personalizado, use los siguientes patrones de dirección URL.

| Acción             | Patrón                                  |
| ------------------ | ---------------------------------------- |
| Iniciar sesión              | `/.auth/login/<PROVIDER_NAME_IN_CONFIG>` |
| Logout             | `/.auth/logout`                          |
| Purga de los detalles del usuario | `/.auth/purge/<PROVIDER_NAME_IN_CONFIG>` |

### <a name="authentication-callbacks"></a>Devoluciones de llamada de autenticación

Los proveedores de autenticación requieren la URL de redireccionamiento para completar la solicitud de inicio o cierre de sesión. Los siguientes puntos de conexión están disponibles como destinos de redirección.

| Tipo   | Patrón de URL                                                 |
| ------ | ----------------------------------------------------------- |
| Iniciar sesión  | `https://<YOUR_SITE>/.auth/login/<PROVIDER_NAME_IN_CONFIG>/callback`  |
| Logout | `https://<YOUR_SITE>/.auth/logout/<PROVIDER_NAME_IN_CONFIG>/callback` |

> [!Note]
> Estas direcciones URL las proporciona Azure Static Web Apps para recibir la respuesta del proveedor de autenticación, no es necesario que cree páginas en estas rutas.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Protección de secretos de autenticación en Azure Key Vault](./key-vault-secrets.md)
