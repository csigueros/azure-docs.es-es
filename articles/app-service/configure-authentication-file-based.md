---
title: Configuración basada en archivos de AuthN/AuthZ
description: Configure la autenticación y autorización en App Service mediante un archivo de configuración para habilitar funcionalidades de versión preliminar concretas.
ms.topic: article
ms.date: 07/15/2021
ms.openlocfilehash: 54fa47055a1f0bba3075d6e77c4fa27d63caf2b6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730690"
---
# <a name="file-based-configuration-in-azure-app-service-authentication"></a>Configuración basada en archivos en la autenticación de Azure App Service

Con la [autenticación de App Service](overview-authentication-authorization.md), la configuración de autenticación se puede establecer con un archivo. Es posible que tenga que usar la configuración basada en archivos para utilizar determinadas funcionalidades de versión preliminar de autenticación o autorización de App Service antes de exponerlas por medio de las API de [Azure Resource Manager](../azure-resource-manager/management/overview.md).

> [!IMPORTANT]
> Recuerde que la carga de la aplicación y, por lo tanto, este archivo, pueden moverse entre entornos, al igual que sucede con los [espacios](./deploy-staging-slots.md). Es probable que quiera anclar un registro de aplicación diferente a cada espacio, por lo que, en estos casos, debería seguir usando el método de configuración estándar en lugar de usar el archivo de configuración.

## <a name="enabling-file-based-configuration"></a>Habilitación de la configuración basada en archivos

1. Cree un nuevo archivo JSON para la configuración en la raíz del proyecto (implementado en D:\home\site\wwwroot en la aplicación web o de funciones). Rellene la configuración deseada según la [referencia de configuración basada en archivos](#configuration-file-reference). Si modifica una configuración de Azure Resource Manager existente, asegúrese de traducir las propiedades capturadas en la colección de `authsettings` en el archivo de configuración.

2. Modifique la configuración existente, que se captura en las API de [Azure Resource Manager](../azure-resource-manager/management/overview.md) de `Microsoft.Web/sites/<siteName>/config/authsettingsV2`. Para modificarla, puede usar una [plantilla de Azure Resource Manager](../azure-resource-manager/templates/overview.md) o una herramienta como [Azure Resource Explorer](https://resources.azure.com/). En la colección authsettingsV2, tendrá que establecer tres propiedades (y posiblemente quitar otras):

    1. Establecer en `platform.enabled` en "true"
    2. Establecer `platform.configFilePath` en el nombre del archivo (por ejemplo, "auth.json")

> [!NOTE]
> El formato de `platform.configFilePath` varía entre las plataformas. En Windows, se admiten las rutas de acceso relativas y absolutas. Se recomienda la relativa. En el caso de Linux, actualmente solo se admiten rutas de acceso absolutas, por lo que el valor de configuración debe ser "/home/site/wwwroot/auth.json" o similar.

Una vez que haya realizado esta actualización de la configuración, el contenido del archivo se usará para definir el comportamiento de autenticación o autorización de App Service para ese sitio. Si alguna vez quiere regresar a la configuración de Azure Resource Manager, puede hacerlo si vuelve a establecer `platform.configFilePath` en NULL.

## <a name="configuration-file-reference"></a>Referencia del archivo de configuración

Los secretos a los que se hará referencia desde el archivo de configuración deben almacenarse como [configuración de la aplicación](./configure-common.md#configure-app-settings). Puede asignar el nombre que quiera a la configuración. Simplemente tiene que asegurarse de que las referencias del archivo de configuración usan las mismas claves.

El código siguiente agota las posibles opciones de configuración del archivo:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "httpSettings": {
        "requireHttps": <true|false>,
        "routes": {
            "apiPrefix": "<api prefix>"
        },
        "forwardProxy": {
            "convention": "NoProxy|Standard|Custom",
            "customHostHeaderName": "<host header value>",
            "customProtoHeaderName": "<proto header value>"
        }
    },
    "login": {
        "routes": {
            "logoutEndpoint": "<logout endpoint>"
        },
        "tokenStore": {
            "enabled": <true|false>,
            "tokenRefreshExtensionHours": "<double>",
            "fileSystem": {
                "directory": "<directory to store the tokens in if using a file system token store (default)>"
            },
            "azureBlobStorage": {
                "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
            }
        },
        "preserveUrlFragmentsForLogins": <true|false>,
        "allowedExternalRedirectUrls": [
            "https://uri1.azurewebsites.net/",
            "https://uri2.azurewebsites.net/",
            "url_scheme_of_your_app://easyauth.callback"
        ],
        "cookieExpiration": {
            "convention": "FixedTime|IdentityDerived",
            "timeToExpiration": "<timespan>"
        },
        "nonce": {
            "validateNonce": <true|false>,
            "nonceExpirationInterval": "<timespan>"
        }
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "public_profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "apple": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_APPLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "openIdConnectProviders": {
            "<providerName>": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "clientSecretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scopes": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        }
    }
}
```

## <a name="more-resources"></a>Más recursos

- [Tutorial: Autenticación y autorización de usuarios de un extremo a otro](tutorial-auth-aad.md)
- [Variables de entorno y configuración de la aplicación para la autenticación](reference-app-settings.md#authentication--authorization)