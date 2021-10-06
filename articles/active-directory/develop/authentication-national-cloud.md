---
title: Autenticación en Azure AD y nubes nacionales | Azure
titleSuffix: Microsoft identity platform
description: Obtenga información acerca de los puntos de conexión para la autenticación y registro de aplicaciones en nubes nacionales.
services: active-directory
author: negoe
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/21/2021
ms.author: negoe
ms.reviewer: marsma, negoe,celested
ms.custom: aaddev,references_regions
ms.openlocfilehash: d920747b3af826a3009c602e81baa9157160eeb8
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128551531"
---
# <a name="national-clouds"></a>Nubes nacionales

Las nubes nacionales son instancias físicamente aisladas de Azure. Estas regiones de Azure se han diseñado para asegurarse de que cumplen los requisitos de residencia, soberanía y cumplimiento normativo de los datos dentro de las fronteras geográficas.

Incluida la nube global de Azure, Azure Active Directory (Azure AD) se implementa en las siguientes nubes nacionales:

- Azure Government
- Azure China 21Vianet
- Azure Alemania ([cierre el 29 de octubre de 2021](https://www.microsoft.com/cloud-platform/germany-cloud-regions)). Obtenga más información sobre la [migración de Azure Alemania](#azure-germany-microsoft-cloud-deutschland).

Cada _instancia_ de nube, las nubes nacionales individuales y la nube global de Azure, es un entorno independiente con sus propios puntos de conexión. Los puntos de conexión específicos de la nube incluyen puntos de conexión de solicitud de token de acceso de OAuth 2.0 y de token de identificador de OpenID Connect, y direcciones URL para la administración e implementación de aplicaciones, como Azure Portal.

A medida que desarrolle las aplicaciones, use los puntos de conexión de la instancia de nube donde implementará la aplicación.

## <a name="app-registration-endpoints"></a>Puntos de conexión de registro de aplicaciones

Hay una instancia de Azure Portal independiente para cada una de las nubes nacionales. Para integrar aplicaciones con la plataforma de identidad de Microsoft en una nube nacional, deberá registrar la aplicación por separado en cada instancia de Azure Portal específica del entorno.

En la tabla siguiente se enumeran las direcciones URL base de los puntos de conexión de Azure AD que se usan para registrar una aplicación en cada nube nacional.

| Nube nacional                          | Punto de conexión de Azure Portal      |
| --------------------------------------- | -------------------------- |
| Azure Portal para la Administración Pública de Estados Unidos          | `https://portal.azure.us`  |
| Azure Portal China, operado por 21Vianet | `https://portal.azure.cn`  |
| Azure Portal (servicio global)           | `https://portal.azure.com` |

## <a name="azure-ad-authentication-endpoints"></a>Puntos de conexión de autenticación de Azure AD

Todas las nubes nacionales autentican a los usuarios por separado en cada entorno y tienen puntos de conexión de autenticación independientes.

En la tabla siguiente se enumeran las direcciones URL base de los puntos de conexión de Azure AD que se usan para adquirir tokens en cada nube nacional.

| Nube nacional                      | Punto de conexión de autenticación de Azure AD           |
| ----------------------------------- | ------------------------------------------ |
| Azure AD para el US Gov          | `https://login.microsoftonline.us`         |
| Azure AD China, operado por 21Vianet | `https://login.partner.microsoftonline.cn` |
| Azure AD (servicio global)           | `https://login.microsoftonline.com`        |

Puede realizar solicitudes a los puntos de conexión de autorización o token de Azure AD mediante la dirección URL base específica de la región pertinente. Por ejemplo, para Azure global:

- El punto de conexión común de autorización es `https://login.microsoftonline.com/common/oauth2/v2.0/authorize`.
- El punto de conexión común de token es `https://login.microsoftonline.com/common/oauth2/v2.0/token`.

En el caso de las aplicaciones de inquilino único, reemplace "common" en las direcciones URL anteriores por el identificador o el nombre del inquilino. Un ejemplo es `https://login.microsoftonline.com/contoso.com`.

## <a name="azure-germany-microsoft-cloud-deutschland"></a>Azure Alemania (Microsoft Cloud Deutschland)

> [!WARNING]
> Azure Alemania (Microsoft Cloud Deutschland) se [cerrará el 29 de octubre de 2021](https://www.microsoft.com/cloud-platform/germany-cloud-regions). Los servicios y las aplicaciones que decida _no_ migrar a una región de Azure global antes de esa fecha serán inaccesibles.

Si no ha migrado la aplicación desde Azure Alemania, siga [Información de Azure Active Directory para la migración desde Azure Alemania](/microsoft-365/enterprise/ms-cloud-germany-transition-azure-ad) para empezar.

## <a name="microsoft-graph-api"></a>Microsoft Graph API

Para obtener información sobre cómo llamar a las API de Microsoft Graph en el entorno de nube nacional, vaya a [Microsoft Graph en implementaciones de nube nacional](/graph/deployments).

> [!IMPORTANT]
> Ciertos servicios y características que se encuentran en regiones específicas del servicio mundial pueden no estar disponibles en todas las nubes nacionales. Para saber qué servicios están disponibles, consulte [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=all&regions=usgov-non-regional,us-dod-central,us-dod-east,usgov-arizona,usgov-iowa,usgov-texas,usgov-virginia,china-non-regional,china-east,china-east-2,china-north,china-north-2,germany-non-regional,germany-central,germany-northeast).

Para obtener información sobre cómo compilar una aplicación mediante la Plataforma de identidad de Microsoft, siga el [tutorial Aplicación de página única (SPA) con flujo de código de autenticación](tutorial-v2-angular-auth-code.md). En concreto, esta aplicación iniciará la sesión de un usuario y obtendrá un token de acceso para llamar a la API de Microsoft Graph.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a usar la [Biblioteca de autenticación de Microsoft (MSAL) en un entorno de nube nacional](msal-national-cloud.md).

Documentación de la nube nacional:

- [Azure Government](../../azure-government/index.yml)
- [Azure China 21Vianet](/azure/china/)
- [Azure Alemania (cierre el 29 de octubre de 2021)](../../germany/index.yml)
