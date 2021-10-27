---
title: 'Adición de un proveedor de identidades: Azure Active Directory B2C'
description: Aprenda a agregar un proveedor de identidades en su inquilino de Active Directory B2C.
services: active-directory-b2c
author: kengaderdus
manager: CelesteDG
ms.author: kengaderdus
ms.date: 04/29/2021
ms.custom: mvc
ms.topic: how-to
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 92cbc796d4372c6127922ac6cac11d803a29b293
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130039987"
---
# <a name="add-an-identity-provider-to-your-azure-active-directory-b2c-tenant"></a>Incorporación de un proveedor de identidades en su inquilino de Azure Active Directory B2C

Puede configurar Azure AD B2C para permitir que los usuarios inicien sesión en su aplicación con las credenciales de proveedores de identidades (IdP) externos de redes sociales o de empresa. Azure AD B2C admite proveedores de identidades externos, como Facebook, cuenta Microsoft, Google, Twitter y cualquier otro proveedor de identidades compatible con los protocolos OAuth 1.0, OAuth 2.0, OpenID Connect y SAML.

Con la federación de proveedores de identidades externos, puede ofrecer a sus consumidores la posibilidad de iniciar sesión con sus cuentas de redes sociales o de empresa existentes, sin tener que crear una cuenta solo para la aplicación.

En la página de registro o inicio de sesión, Azure AD B2C presenta una lista de proveedores de identidades externos que el usuario puede elegir para iniciar sesión. Después de seleccionar uno de los proveedores de identidades externos, se le redirige al sitio web del proveedor seleccionado para completar el proceso de inicio de sesión. Una vez que el usuario inicia sesión correctamente, se le devuelve a Azure AD B2C para autenticar la cuenta en la aplicación.

![Ejemplo de inicio de sesión móvil con una cuenta de redes sociales (Facebook)](media/add-identity-provider/external-idp.png)

Puede agregar proveedores de identidades compatibles con Azure Active Directory B2C (Azure AD B2C) a los [flujos de usuario](user-flow-overview.md) mediante Azure Portal. También puede agregar proveedores de identidades a las [directivas personalizadas](user-flow-overview.md).

## <a name="select-an-identity-provider"></a>Seleccione un proveedor de identidad

Por lo general, solo se usa un proveedor de identidades en las aplicaciones, pero existe la opción de agregar más. En los artículos de procedimientos siguientes se muestra cómo crear la aplicación del proveedor de identidades, cómo agregarla al inquilino y cómo agregar el proveedor de identidades a su flujo de usuario o a la directiva personalizada.

* [AD FS](identity-provider-adfs.md)
* [Amazon](identity-provider-amazon.md)
* [Apple](identity-provider-apple-id.md)
* [Azure AD (inquilino único)](identity-provider-azure-ad-single-tenant.md)
* [Azure AD (multiinquilino)](identity-provider-azure-ad-multi-tenant.md)
* [Azure AD B2C](identity-provider-azure-ad-b2c.md)
* [eBay](identity-provider-ebay.md)
* [Facebook](identity-provider-facebook.md)
* [Proveedores de identidades genéricas](identity-provider-generic-openid-connect.md)
* [GitHub](identity-provider-github.md)
* [ID.me](identity-provider-id-me.md)
* [Google](identity-provider-google.md)
* [LinkedIn](identity-provider-linkedin.md)
* [Cuenta Microsoft](identity-provider-microsoft-account.md)
* [QQ](identity-provider-qq.md)
* [Salesforce](identity-provider-salesforce.md)
* [Salesforce (protocolo SAML)](identity-provider-salesforce-saml.md)
* [Twitter](identity-provider-twitter.md)
* [WeChat](identity-provider-wechat.md)
* [Weibo](identity-provider-weibo.md)
