---
title: Tutorial para configurar Azure Active Directory B2C con el firewall de aplicaciones web de Akamai
titleSuffix: Azure AD B2C
description: Tutorial para configurar el firewall de aplicaciones web de Akamai con Azure AD B2C
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 07/15/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 09f35e9621f6704fb33720a43afb38fd99e9eec6
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123213900"
---
# <a name="tutorial-configure-akamai-with-azure-active-directory-b2c"></a>Tutorial: Configuración de Akamai con Azure Active Directory B2C

En este tutorial de ejemplo, aprenderá a habilitar la solución de [firewall de aplicaciones web de Akamai](https://www.akamai.com/us/en/resources/web-application-firewall.jsp) para el inquilino de Azure Active Directory (AD) B2C mediante dominios personalizados. El firewall de aplicaciones web de Akamai ayuda a la organización a proteger sus aplicaciones web frente a ataques malintencionados que pretenden aprovechar vulnerabilidades como la inserción de SQL y el scripting entre sitios.

>[!NOTE]
>Esta característica está en versión preliminar pública.

Ventajas del uso de la solución de firewall de aplicaciones web de Akamai:

- Una plataforma perimetral que permite la administración del tráfico a los servicios.

- Se puede configurar delante de su inquilino de Azure AD B2C.

- Permite la manipulación detallada del tráfico para proteger la infraestructura de identidad.

Este tutorial de ejemplo se aplica a las soluciones de firewall de aplicaciones web [Web Application Protector (WAP)](https://www.akamai.com/us/en/products/security/web-application-protector-enterprise-waf-firewall-ddos-protection.jsp) y [Kona Site Defender (KSD)](https://www.akamai.com/us/en/products/security/kona-site-defender.jsp) que ofrece Akamai.

## <a name="prerequisites"></a>Requisitos previos

Para empezar, necesitará lo siguiente:

- Suscripción a Azure. Si no tiene una suscripción, puede crear una [cuenta gratuita](https://azure.microsoft.com/free/).

- [Un inquilino de Azure AD B2C](tutorial-create-tenant.md) vinculado a la suscripción de Azure.

- Una cuenta del [firewall de aplicaciones web de Akamai](https://www.akamai.com/us/en/akamai-free-trials.jsp).
 
## <a name="scenario-description"></a>Descripción del escenario

La integración de la solución de firewall de aplicaciones web de Akamai incluye los siguientes componentes:

- **Inquilino de Azure AD B2C**: el servidor de autorización, responsable de comprobar las credenciales del usuario en función de las directivas personalizadas definidas en el inquilino.  También se conoce como proveedor de identidades.

- [**Azure Front Door:**](../frontdoor/front-door-overview.md) es el responsable de habilitar dominios personalizados para el inquilino de Azure B2C. Todo el tráfico de Cloudflare WAF se enrutará a Azure Front Door antes de llegar al inquilino de Azure AD B2C.

- [**Firewall de aplicaciones web de Akamai**](https://www.akamai.com/us/en/resources/waf.jsp): es el firewall de aplicaciones web, que administra todo el tráfico que se envía al servidor de autorización.

## <a name="integrate-with-azure-ad-b2c"></a>Integración con Azure AD B2C

1. Para usar dominios personalizados en Azure AD B2C, es necesario usar la característica de dominio personalizado que proporciona Azure Front Door. Obtenga información sobre la [habilitación de dominios personalizados de Azure AD B2C](./custom-domain.md?pivots=b2c-user-flow).  

2. Una vez que el dominio de Azure AD B2C personalizado se haya configurado correctamente mediante Azure Front Door, [pruebe el dominio personalizado](./custom-domain.md?pivots=b2c-custom-policy#test-your-custom-domain) antes de continuar.  

## <a name="onboard-with-akamai"></a>Incorporación con Akamai

[Regístrese](https://www.akamai.com) y cree una cuenta de Akamai.

### <a name="create-and-configure-property"></a>Creación y configuración de una propiedad 

1. [Cree una propiedad](https://control.akamai.com/wh/CUSTOMER/AKAMAI/en-US/WEBHELP/property-manager/property-manager-help/GUID-14BB87F2-282F-4C4A-8043-B422344884E6.html).

2. Configure la configuración de la propiedad como:  

| Propiedad | Value |
|:---------------|:---------------|
|Versión de la propiedad | Seleccione TLS estándar o mejorado (opción preferida) |
|Nombres de host de la propiedad | Agregue un nombre de host de la propiedad. Este es el nombre del dominio personalizado, por ejemplo: login.domain.com. <BR> Cree o modifique un certificado con la configuración adecuada para el nombre de dominio personalizado. Para obtener más información, vea [this](https://learn.akamai.com/en-us/webhelp/property-manager/https-delivery-with-property-manager/GUID-9EE0EB6A-E62B-4F5F-9340-60CBD093A429.html). |

3. Establezca los valores de configuración de la propiedad del servidor de origen como:

|Propiedad| Value |
|:-----------|:-----------|
| Tipo de origen | Su origen |
| Nombre de host del servidor de origen | yourafddomain.azurefd.net |
| Encabezado host de reenvío | Encabezado de host de entrante |
| Nombre de host de la clave de caché| Encabezado de host de entrante  |

### <a name="configure-dns"></a>Configurar el DNS

Cree un registro CNAME en el DNS, como login.domain.com, que apunte al nombre de host de Edge en el campo Nombre de host de propiedad.

### <a name="configure-akamai-waf"></a>Configuración del firewall de aplicaciones web de Akamai

1. [Configure el firewall de aplicaciones web de Akamai](https://learn.akamai.com/en-us/webhelp/kona-site-defender/kona-site-defender-quick-start/GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A.html#GUID-6294B96C-AE8B-4D99-8F43-11B886E6C39A).

2. Asegúrese de que la opción **Acciones de regla** de todos los elementos que aparecen en **Attack Group** (Grupo de ataque) estén establecidos en **Denegar**.

![La imagen muestra la acción de regla establecida en Denegar](./media/partner-akamai/rule-action-deny.png)

Obtenga más información sobre [cómo funciona el control y las opciones de configuración](https://control.akamai.com/dl/security/GUID-81C0214B-602A-4663-839D-68BCBFF41292.html).

### <a name="test-the-settings"></a>Prueba de la configuración

Compruebe lo siguiente para asegurarse de que todo el tráfico hacia Azure AD B2C está pasando por el dominio personalizado:

- Asegúrese de que todas las solicitudes entrantes en el dominio personalizado de Azure AD B2C se enrutan a través del firewall de aplicaciones web de Akamai y mediante una conexión TLS válida.
- Asegúrese de que Azure AD B2C establece correctamente todas las cookies para el dominio personalizado.
- El panel del firewall de aplicaciones web de Akamai disponible en la consola de Security Center muestra gráficos de todo el tráfico que pasa a través del firewall de aplicaciones web junto con cualquier tráfico de ataque.

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de un dominio personalizado en Azure AD B2C](./custom-domain.md?pivots=b2c-user-flow)

- [Introducción a las directivas personalizadas en Azure AD B2C](./tutorial-create-user-flows.md?pivots=b2c-custom-policy&tabs=applications)
