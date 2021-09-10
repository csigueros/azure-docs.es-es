---
title: Habilitación de las opciones de aplicaciones web de Python mediante Azure Active Directory B2C
description: En este artículo se muestra cómo habilitar el uso de opciones de aplicación web de Python.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 07/05/2021
ms.author: mimart
ms.subservice: B2C
ms.custom: b2c-support
ms.openlocfilehash: 97f1cb1f13ab990a6590576ae93ba86a4c935300
ms.sourcegitcommit: ef448159e4a9a95231b75a8203ca6734746cd861
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123220364"
---
# <a name="enable-authentication-options-in-a-python-web-app-by-using-azure-ad-b2c"></a>Habilitación de las opciones de autenticación en una aplicación web de Python mediante Azure AD B2C 

En este artículo se describe cómo habilitar, personalizar y mejorar la experiencia de autenticación de Azure Active Directory B2C (Azure AD B2C) para una aplicación web de Python. 

Antes de empezar, es importante que se familiarice con cómo [configurar la autenticación en una aplicación web de Python de ejemplo mediante Azure AD B2C](configure-authentication-sample-python-web-app.md).

[!INCLUDE [active-directory-b2c-app-integration-custom-domain](../../includes/active-directory-b2c-app-integration-custom-domain.md)]

Para usar un dominio personalizado y el id. de inquilino en la URL de autenticación: 

1. Siga las instrucciones de [Habilitación de dominios personalizados](custom-domain.md).
1. En el archivo *app_config.py*, actualice el miembro de clase `authority_template` con el dominio personalizado.

En el siguiente código de Python se muestra la configuración de la aplicación antes del cambio:

```python
authority_template = "https://{tenant}.b2clogin.com/{tenant}.onmicrosoft.com/{user_flow}"
```

En el siguiente código de Python se muestra la configuración de la aplicación después del cambio:

```python
authority_template = "https://custom.domain.com/00000000-0000-0000-0000-000000000000/{user_flow}" 
```

[!INCLUDE [active-directory-b2c-app-integration-login-hint](../../includes/active-directory-b2c-app-integration-login-hint.md)]

1. Si usa una directiva personalizada, agregue la notificación de entrada necesaria como se describe en [Configuración del inicio de sesión directo](direct-signin.md#prepopulate-the-sign-in-name). 
1. Busque el método `initiate_auth_code_flow` y, después, agregue el parámetro `login_hint` con el nombre de dominio del proveedor de identidades (por ejemplo, *facebook.com*).

```python
def _build_auth_code_flow(authority=None, scopes=None):
    return _build_msal_app(authority=authority).initiate_auth_code_flow(
        scopes or [],
        redirect_uri=url_for("authorized", _external=True),
        login_hint="bob@contoso.com")
```

[!INCLUDE [active-directory-b2c-app-integration-domain-hint](../../includes/active-directory-b2c-app-integration-domain-hint.md)]

1. Compruebe el nombre de dominio del proveedor de identidades externo. Para más información, consulte [Redirección del inicio de sesión a un proveedor social](direct-signin.md#redirect-sign-in-to-a-social-provider). 
1. Busque el método `initiate_auth_code_flow` y, después, agregue el parámetro `domain_hint` con la sugerencia de inicio de sesión.

    ```python
    def _build_auth_code_flow(authority=None, scopes=None):
        return _build_msal_app(authority=authority).initiate_auth_code_flow(
            scopes or [],
            redirect_uri=url_for("authorized", _external=True),
            domain_hint="facebook.com")
    ```


## <a name="next-steps"></a>Pasos siguientes

- Para obtener más información, vea [Opciones de configuración de MSAL para Python](https://github.com/AzureAD/microsoft-authentication-library-for-python/wiki).
