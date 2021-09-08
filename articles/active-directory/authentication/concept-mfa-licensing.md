---
title: Versiones y planes de consumo de Azure AD Multi-Factor Authentication
description: Obtenga información sobre el cliente de Azure AD Multi-Factor Authentication, así como los distintos métodos y versiones disponibles.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b2a3528e30546acf0e8f652de7f8e46226fd188
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451491"
---
# <a name="features-and-licenses-for-azure-ad-multi-factor-authentication"></a>Características y licencias de Azure AD Multi-Factor Authentication

Para proteger las cuentas de usuario de la organización, se debe usar la autenticación multifactor. Esta característica resulta especialmente importante en las cuentas que tienen acceso con privilegios a los recursos. Los administradores globales de Microsoft 365 y Azure Active Directory (Azure AD) disponen de las características básicas de la autenticación multifactor sin cargo adicional. Si quiere actualizar las características de los administradores o extender la autenticación multifactor al resto de los usuarios, puede adquirir Azure AD Multi-Factor Authentication de varias maneras.

> [!IMPORTANT]
> En este artículo se detallan las diferentes licencias y formas de uso de Azure AD Multi-Factor Authentication. Para obtener información específica sobre precios y facturación, consulte [Página de precios de Azure AD](https://www.microsoft.com/en-us/security/business/identity-access-management/azure-ad-pricing).

## <a name="available-versions-of-azure-ad-multi-factor-authentication"></a>Versiones disponibles de Azure AD Multi-Factor Authentication

Existen varias licencias distintas de Azure AD Multi-Factor Authentication, y formas de usarlo, en función de las necesidades de la organización. Es posible que ya pueda usar Azure AD Multi-Factor Authentication conforme a la licencia de Azure AD, EMS o Microsoft 365 que tenga actualmente. Por ejemplo, los primeros 50 000 usuarios activos mensuales de Azure AD External Identities pueden usar MFA y otras características de Premium P1 o P2 de forma gratuita. Para obtener más información, consulte [Precios de Azure Active Directory for External Identities](https://azure.microsoft.com/pricing/details/active-directory/external-identities/).

En la tabla siguiente se detallan las diferentes formas de obtener Azure AD Multi-Factor Authentication y algunas de las características y los casos de uso de cada una de ellas.

| Si es usuario de | Funcionalidades y casos de uso |
| --- | --- |
| [Microsoft 365 Business Premium](https://www.microsoft.com/microsoft-365/business) y [EMS](https://www.microsoft.com/security/business/enterprise-mobility-security) o [Microsoft 365 E3 y E5](https://www.microsoft.com/microsoft-365/enterprise/compare-office-365-plans) | EMS E3, Microsoft 365 E3 y Microsoft 365 Empresa Premium incluye Azure AD Premium P1. EMS E5 o Microsoft 365 E5 incluyen Azure AD Premium P2. Puede usar las mismas características de acceso condicional que se indican en las siguientes secciones para proporcionar la autenticación multifactor a los usuarios. |
| [Azure AD Premium P1](../fundamentals/active-directory-get-started-premium.md) | Puede usar el [acceso condicional de Azure AD](../conditional-access/howto-conditional-access-policy-all-users-mfa.md) para solicitar a los usuarios la autenticación multifactor en determinados escenarios o eventos y adaptarse así a los requisitos empresariales. |
| [Azure AD Premium P2](../fundamentals/active-directory-get-started-premium.md) | Proporciona la opción de seguridad más potente y una experiencia de usuario mejorada. Agrega [acceso condicional basado en riesgos](../conditional-access/howto-conditional-access-policy-risk.md) a las características de Azure AD Premium P1; se adapta a los patrones del usuario y reduce el número de solicitudes de la autenticación multifactor. |
| [Todos los planes de Microsoft 365](https://www.microsoft.com/microsoft-365/compare-microsoft-365-enterprise-plans) | Azure AD Multi-Factor Authentication puede habilitarse para todos los usuarios con los [valores predeterminados de seguridad](../fundamentals/concept-fundamentals-security-defaults.md). La administración de Azure AD Multi-Factor Authentication se realiza en el portal de Microsoft 365. Para mejorar la experiencia del usuario, actualice a Azure AD Premium P1 o P2 y use el acceso condicional. Para obtener más información, consulte el artículo sobre [la protección de los recursos de Microsoft 365 con la autenticación multifactor](/microsoft-365/admin/security-and-compliance/set-up-multi-factor-authentication). MFA también puede [habilitarse por usuario](howto-mfa-userstates.md). |
| [Office 365 gratis](https://www.microsoft.com/microsoft-365/enterprise/compare-office-365-plans)<br>[Azure AD Gratis](../verifiable-credentials/how-to-create-a-free-developer-account.md) | Puede usar los [valores predeterminados de seguridad](../fundamentals/concept-fundamentals-security-defaults.md) para solicitar a los usuarios la autenticación multifactor según sea necesario y, aunque no tiene un control pormenorizado de los usuarios o escenarios habilitados, sí se proporciona ese paso de seguridad adicional.<br /> Incluso cuando no se usan los valores predeterminados de seguridad para habilitar la autenticación multifactor para todos los usuarios, se puede configurar el rol de *administrador global de Azure AD* para usar la autenticación multifactor. Esta característica del nivel de servicio gratuito garantiza que las cuentas de administrador críticas están protegidas por la autenticación multifactor. |

## <a name="feature-comparison-of-versions"></a>Comparación de características de las versiones

En la tabla siguiente se proporciona una lista de las características que están disponibles en las distintas versiones de Azure AD Multi-Factor Authentication. Planee sus necesidades de protección para la autenticación de usuarios y, a continuación, determine qué enfoque cumple esos requisitos. Por ejemplo, aunque Azure AD Free aporta valores predeterminados de seguridad que proporcionan Azure AD Multi-Factor Authentication, en la solicitud de autenticación solo se puede usar la aplicación de autenticación para dispositivos móviles, no una llamada telefónica ni un SMS. Este enfoque puede ser una limitación si no se puede garantizar que la aplicación de autenticación para dispositivos móviles esté instalada en el dispositivo personal de un usuario. Consulte [Nivel Azure AD Free](#azure-ad-free-tier) más adelante en este tema para obtener más información. 

| Característica | Azure AD Free: valores predeterminados de seguridad (habilitados para todos los usuarios) | Azure AD Free: solo administradores globales | Office 365 | Azure AD Premium (P1 o P2) |
| --- |:---:|:---:|:---:|:---:|
| Protección de cuentas de administración de inquilinos de Azure AD con MFA | ● | ● (solo cuentas de *administrador global de Azure AD*) | ● | ● |
| Aplicación móvil como segundo factor | ● | ● | ● | ● |
| Llamada de teléfono como segundo factor | | ● | ● | ● |
| SMS como segundo factor | | ● | ● | ● |
| Control administrativo sobre métodos de comprobación | | ● | ● | ● |
| Alerta de fraude | | | | ● |
| Informes de MFA | | | | ● |
| Saludos personalizados para las llamadas de teléfono | | | | ● |
| Identificador de llamada personalizado para llamadas telefónicas | | | | ● |
| IP de confianza | | | | ● |
| Recordar MFA para dispositivos de confianza | | ● | ● | ● |
| MFA para aplicaciones locales | | | | ● |

## <a name="purchase-and-enable-azure-ad-multi-factor-authentication"></a>Compra y habilitación de Azure AD Multi-Factor Authentication

Para usar Azure AD Multi-Factor Authentication, regístrese o compre un nivel de Azure AD válido. Azure AD está disponible en cuatro ediciones: Free, Office 365, Premium P1 y Premium P2.

La edición Free se incluye con una suscripción de Azure. Consulte la [sección siguiente](#azure-ad-free-tier) para obtener información sobre cómo usar los valores predeterminados de seguridad o cómo proteger las cuentas con el rol de *administrador global de Azure AD*.

Las ediciones de Azure AD Premium están disponibles a través del representante de Microsoft, el [programas de licencias por volumen Open](https://www.microsoft.com/licensing/licensing-programs/open-license.aspx) y el [programa de proveedores de soluciones en la nube](https://go.microsoft.com/fwlink/?LinkId=614968&clcid=0x409). Los suscriptores de Azure y Microsoft 365 también pueden comprar Azure Active Directory Premium P1 y P2 en línea. [Inicie sesión](https://portal.office.com/Commerce/Catalog.aspx) para comprar.

Una vez que haya adquirido el nivel de Azure AD necesario, [planee e implemente Azure AD Multi-Factor Authentication](howto-mfa-getstarted.md).

### <a name="azure-ad-free-tier"></a>Nivel Azure AD Free

Todos los usuarios de un inquilino de Azure AD Free pueden usar Azure AD Multi-Factor Authentication mediante el uso de los valores predeterminados de seguridad. Cuando se emplean los valores predeterminados de seguridad de Azure AD Free, el único método que se puede usar para Azure AD Multi-Factor Authentication es la aplicación de autenticación para dispositivos móviles.

* [Más información sobre los valores predeterminados de seguridad de Azure AD](../fundamentals/concept-fundamentals-security-defaults.md)
* [Habilitación de los valores predeterminados de seguridad para los usuarios de Azure AD Free](../fundamentals/concept-fundamentals-security-defaults.md#enabling-security-defaults)

Si no quiere habilitar Azure AD Multi-Factor Authentication para todos los usuarios, puede optar por proteger solo las cuentas de usuario con el rol de *administrador global de Azure AD*. Este enfoque proporciona más solicitudes de autenticación para las cuentas de administrador críticas. Puede habilitar Azure AD Multi-Factor Authentication de una de las siguientes maneras, según el tipo de cuenta que use:

* Si usa una cuenta Microsoft, [regístrese para la autenticación multifactor](https://support.microsoft.com/help/12408/microsoft-account-about-two-step-verification).
* Si no usa una cuenta Microsoft, [active la autenticación multifactor para un usuario o grupo en Azure AD](howto-mfa-userstates.md).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre los costes, consulte [Precios de Azure AD](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing).
* [¿Qué es el acceso condicional?](../conditional-access/overview.md)

