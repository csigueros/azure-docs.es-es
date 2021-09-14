---
title: Azure AD acceso híbrido seguro | Microsoft Docs
description: En este artículo se describen las soluciones de asociados para integrar las aplicaciones locales, de nube pública o de nube privada heredadas con Azure AD.
services: active-directory
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 8/17/2021
ms.author: gasinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 321b7ed1ee6cfc2d37547a88f3aef5be6d594e10
ms.sourcegitcommit: ddac53ddc870643585f4a1f6dc24e13db25a6ed6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122397133"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Acceso híbrido seguro: Protección de aplicaciones heredadas con Azure Active Directory

Ahora puede proteger sus aplicaciones de autenticación heredadas locales y en la nube conectándolas a Azure Active Directory (AD) con:

- [Azure AD Application Proxy](#secure-hybrid-access-through-azure-ad-application-proxy)

- [Asociados de acceso híbrido seguros](#secure-hybrid-access-through-azure-ad-partner-integrations)

Puede salvar la brecha y fortalecer su postura de seguridad en todas las aplicaciones con funcionalidades de Azure AD, como [Acceso condicional de Azure AD](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) y [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/identity-protection/overview-identity-protection). Al tener Azure AD como proveedor de identidades (IDP), puede usar métodos de autenticación y autorización modernos, como el [inicio de sesión único](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) y la [autenticación multifactor](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks) para proteger las aplicaciones heredadas locales.

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>Acceso híbrido seguro a través de Azure AD Application Proxy
  
Con [Application Proxy](https://docs.microsoft.com/azure/active-directory/app-proxy/what-is-application-proxy), puede proporcionar [acceso remoto seguro](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-add-on-premises-application) a sus aplicaciones web locales. Los usuarios no necesitan usar una VPN. Los usuarios se benefician al conectarse fácilmente a sus aplicaciones desde cualquier dispositivo después de un [inicio de sesión único](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-config-sso-how-to#how-to-configure-single-sign-on). Application Proxy proporciona acceso remoto como servicio y permite [publicar fácilmente sus aplicaciones locales](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-add-on-premises-application) a usuarios ajenos a la red corporativa. Ayuda a escalar la administración del acceso a la nube sin necesidad de modificar las aplicaciones locales. [Planee una implementación de Azure AD Application Proxy](https://docs.microsoft.com/azure/active-directory/app-proxy/application-proxy-deployment-plan) como el siguiente paso.

## <a name="secure-hybrid-access-through-azure-ad-partner-integrations"></a>Protección del acceso híbrido a través de integraciones de asociados de Azure AD  

Además de recurrir a [Azure AD Application Proxy](https://aka.ms/whyappproxy), Microsoft se asocia con proveedores de terceros para habilitar el acceso seguro a las aplicaciones locales y las aplicaciones que usan la autenticación heredada.

![La imagen muestra el acceso híbrido seguro con el proxy de la aplicación y los asociados.](./media/secure-hybrid-access/secure-hybrid-access.png)

Los siguientes asociados ofrecen soluciones pre compiladas para admitir directivas de acceso condicional por aplicación y proporcionan instrucciones detalladas para la integración con Azure AD. 

- [Controlador de entrega de aplicaciones (ADC) de Citrix](https://docs.microsoft.com/azure/active-directory/saas-apps/citrix-netscaler-tutorial)  

- [Datawiza Access Broker](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [F5 Big-IP APM ADC](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-integration)

- [F5 Big-IP APM VPN](https://docs.microsoft.com/azure/active-directory/manage-apps/f5-aad-password-less-vpn)

- [Kemp](https://docs.microsoft.com/azure/active-directory/saas-apps/kemp-tutorial)

- [Perimeter 81](https://docs.microsoft.com/azure/active-directory/saas-apps/perimeter-81-tutorial)

- [Silverfort Authentication Platform](https://docs.microsoft.com/azure/active-directory/manage-apps/add-application-portal-setup-oidc-sso)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

Los siguientes asociados ofrecen soluciones predefinidas e instrucciones detalladas para la integración con Azure AD. 

- [Akamai Enterprise Application Access](https://docs.microsoft.com/azure/active-directory/saas-apps/akamai-tutorial)

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [Palo Alto Networks Global Protect](https://docs.microsoft.com/azure/active-directory/saas-apps/paloaltoadmin-tutorial)

- [Pulse Secure Pulse Connect Secure (PCS)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-pcs-tutorial)

- [Pulse Secure Virtual Traffic Manager (VTM)](https://docs.microsoft.com/azure/active-directory/saas-apps/pulse-secure-virtual-traffic-manager-tutorial)

- [Zscaler Private Access (ZPA)](https://docs.microsoft.com/azure/active-directory/saas-apps/zscalerprivateaccess-tutorial)
