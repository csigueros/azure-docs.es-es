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
ms.openlocfilehash: bc8d5d0b62f75d73bbe8879739069f1d28ce15bb
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128642612"
---
# <a name="secure-hybrid-access-secure-legacy-apps-with-azure-active-directory"></a>Acceso híbrido seguro: Protección de aplicaciones heredadas con Azure Active Directory

Ahora puede proteger sus aplicaciones de autenticación heredadas locales y en la nube conectándolas a Azure Active Directory (AD) con:

- [Azure AD Application Proxy](#secure-hybrid-access-through-azure-ad-application-proxy)

- [Asociados de acceso híbrido seguros](#secure-hybrid-access-through-azure-ad-partner-integrations)

Puede salvar la brecha y fortalecer su postura de seguridad en todas las aplicaciones con funcionalidades de Azure AD, como [Acceso condicional de Azure AD](../conditional-access/overview.md) y [Azure AD Identity Protection](../identity-protection/overview-identity-protection.md). Al tener Azure AD como proveedor de identidades (IDP), puede usar métodos de autenticación y autorización modernos, como el [inicio de sesión único](what-is-single-sign-on.md) y la [autenticación multifactor](../authentication/concept-mfa-howitworks.md) para proteger las aplicaciones heredadas locales.

## <a name="secure-hybrid-access-through-azure-ad-application-proxy"></a>Acceso híbrido seguro a través de Azure AD Application Proxy
  
Con [Application Proxy](../app-proxy/what-is-application-proxy.md), puede proporcionar [acceso remoto seguro](../app-proxy/application-proxy-add-on-premises-application.md) a sus aplicaciones web locales. Los usuarios no necesitan usar una VPN. Los usuarios se benefician al conectarse fácilmente a sus aplicaciones desde cualquier dispositivo después de un [inicio de sesión único](../app-proxy/application-proxy-config-sso-how-to.md#how-to-configure-single-sign-on). Application Proxy proporciona acceso remoto como servicio y permite [publicar fácilmente sus aplicaciones locales](../app-proxy/application-proxy-add-on-premises-application.md) a usuarios ajenos a la red corporativa. Ayuda a escalar la administración del acceso a la nube sin necesidad de modificar las aplicaciones locales. [Planee una implementación de Azure AD Application Proxy](../app-proxy/application-proxy-deployment-plan.md) como el siguiente paso.

## <a name="secure-hybrid-access-through-azure-ad-partner-integrations"></a>Protección del acceso híbrido a través de integraciones de asociados de Azure AD  

Además de recurrir a [Azure AD Application Proxy](../app-proxy/what-is-application-proxy.md), Microsoft se asocia con proveedores de terceros para habilitar el acceso seguro a las aplicaciones locales y las aplicaciones que usan la autenticación heredada.

![La imagen muestra el acceso híbrido seguro con el proxy de la aplicación y los asociados.](./media/secure-hybrid-access/secure-hybrid-access.png)

Los siguientes asociados ofrecen soluciones pre compiladas para admitir **directivas de acceso condicional por aplicación** y proporcionan instrucciones detalladas para la integración con Azure AD. 

- [Akamai Enterprise Application Access](../saas-apps/akamai-tutorial.md)

- [Controlador de entrega de aplicaciones (ADC) de Citrix](../saas-apps/citrix-netscaler-tutorial.md)  

- [Datawiza Access Broker](../manage-apps/datawiza-with-azure-ad.md)

- [F5 Big-IP APM ADC](../manage-apps/f5-aad-integration.md)

- [F5 Big-IP APM VPN](../manage-apps/f5-aad-password-less-vpn.md)

- [Kemp](../saas-apps/kemp-tutorial.md)

- [Perimeter 81](../saas-apps/perimeter-81-tutorial.md)

- [Silverfort Authentication Platform](../manage-apps/silverfort-azure-ad-integration.md)

- [Strata](https://docs.microsoft.com/azure/active-directory/saas-apps/maverics-identity-orchestrator-saml-connector-tutorial)

Los siguientes asociados ofrecen soluciones predefinidas e instrucciones detalladas para la integración con Azure AD.

- [Cisco AnyConnect](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-anyconnect)

- [Fortinet](https://docs.microsoft.com/azure/active-directory/saas-apps/fortigate-ssl-vpn-tutorial)

- [Palo Alto Networks Global Protect](../saas-apps/paloaltoadmin-tutorial.md)

- [Pulse Secure Pulse Connect Secure (PCS)](../saas-apps/pulse-secure-pcs-tutorial.md)

- [Pulse Secure Virtual Traffic Manager (VTM)](../saas-apps/pulse-secure-virtual-traffic-manager-tutorial.md)

- [Zscaler Private Access (ZPA)](../saas-apps/zscalerprivateaccess-tutorial.md)
