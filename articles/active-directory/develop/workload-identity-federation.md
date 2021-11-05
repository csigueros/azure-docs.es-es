---
title: Federación de identidades de carga de trabajo
titleSuffix: Microsoft identity platform
description: Use la federación de identidades de carga de trabajo para conceder a las cargas de trabajo que se ejecutan fuera de Azure acceso a recursos protegidos de Azure AD sin usar secretos ni certificados. Esto elimina la necesidad de que los desarrolladores almacenen y mantengan secretos o certificados de larga duración fuera de Azure.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: ryanwi
ms.reviewer: keyam, udayh, vakarand
ms.custom: aaddev
ms.openlocfilehash: ff1161e41965c92b469372038c4ba0cd602ee074
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091745"
---
# <a name="workload-identity-federation-preview"></a>Federación de identidades de carga de trabajo (versión preliminar)
En este artículo se proporciona información general sobre la federación de identidades de carga de trabajo para cargas de trabajo de software. El uso de la federación de identidades de carga de trabajo le permite acceder a recursos protegidos de Azure Active Directory (Azure AD) sin necesidad de administrar secretos (para los escenarios admitidos).

Puede usar la federación de identidades de carga de trabajo en escenarios como Acciones de GitHub.

## <a name="why-use-workload-identity-federation"></a>¿Por qué usar la federación de identidades de carga de trabajo?

Normalmente, una carga de trabajo de software (como una aplicación, un servicio, un script o una aplicación basada en contenedores) necesita una identidad para autenticarse y acceder a los recursos o comunicarse con otros servicios.  Cuando estas cargas de trabajo se ejecutan en Azure, puede usar identidades administradas y la plataforma Azure administra las credenciales.  Para una carga de trabajo de software que se ejecute fuera de Azure, debe usar las credenciales de la aplicación (un secreto o un certificado) para acceder a recursos protegidos de Azure AD (como Azure, Microsoft Graph, Microsoft 365 o recursos de terceros).  Estas credenciales suponen un riesgo para la seguridad y se deben almacenar de forma segura y cambiar periódicamente. También corre el riesgo de tiempo de inactividad del servicio si las credenciales expiran.

Use la federación de identidades de carga de trabajo para configurar un registro de aplicación de Azure AD para confiar en los tokens de un proveedor de identidades externo (IdP), como GitHub.  Una vez creada esa relación de confianza, la carga de trabajo de software puede intercambiar tokens de confianza desde el IdP externo por tokens de acceso de la plataforma de identidad de Microsoft.  A continuación, la carga de trabajo de software utiliza ese token de acceso para acceder a los recursos protegidos de Azure AD a los que se ha concedido acceso a la carga de trabajo. Esto elimina la carga de mantenimiento de administrar manualmente las credenciales y elimina el riesgo de filtración de secretos o de que los certificados expiren.

## <a name="supported-scenarios"></a>Escenarios admitidos

Se admiten los siguientes escenarios para acceder a recursos protegidos de Azure AD mediante la federación de identidades de carga de trabajo:

- Acciones de GitHub. En primer lugar, [configure una relación de confianza](workload-identity-federation-create-trust-github.md) entre la aplicación en Azure AD y un repositorio de GitHub en Azure Portal o mediante Microsoft Graph. A continuación, [configure un flujo de trabajo de Acciones de GitHub](/azure/developer/github/connect-from-azure) para obtener un token de acceso del proveedor de identidades de Microsoft y acceder a los recursos de Azure.
- Cargas de trabajo que se ejecutan en plataformas de proceso fuera de Azure. [Configure una relación de confianza](workload-identity-federation-create-trust.md) entre el registro de aplicación de Azure AD y el IdP externo para la plataforma de proceso. Puede usar tokens emitidos por esa plataforma para autenticarse con la plataforma de identidad de Microsoft y llamar a las API del ecosistema de Microsoft. Use el [flujo de credenciales de cliente](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) para obtener un token de acceso de la plataforma de identidad de Microsoft y enviar el JWT del proveedor de identidades en lugar de crear uno propio mediante un certificado almacenado.

## <a name="how-it-works"></a>Cómo funciona
Cree una relación de confianza entre el IdP externo y una aplicación de Azure AD configurando una [credencial de identidad federada](/graph/api/resources/federatedidentitycredentials-overview?view=graph-rest-beta&preserve-view=true). La credencial de identidad federada se usa para indicar qué token del IdP externo debe ser de confianza para la aplicación. Puede configurar la credencial de identidad federada en el registro de la aplicación en Azure Portal o a través de Microsoft Graph.  Los pasos para configurar la relación de confianza variarán en función del escenario y del IdP externo.

Sin embargo, el flujo de trabajo para intercambiar un token externo por un token de acceso es el mismo para todos los escenarios. En el diagrama siguiente se muestra el flujo de trabajo general de una carga de trabajo que intercambia un token externo por un token de acceso y, a continuación, accede a recursos protegidos de Azure AD.

:::image type="content" source="media/workload-identity-federation/workflow.svg" alt-text="Muestra el intercambio de un token externo por un token de acceso y el acceso a Azure" border="false":::

1. La carga de trabajo externa (por ejemplo, un flujo de trabajo de Acciones de GitHub) solicita un token del IdP externo (por ejemplo, GitHub).
1. El IdP externo emite un token a la carga de trabajo externa.
1. La carga de trabajo externa (la acción de inicio de sesión en un flujo de trabajo de GitHub, por ejemplo) [envía el token a plataforma de identidad de Microsoft](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) y solicita un token de acceso.
1. La plataforma de identidad de Microsoft comprueba la [relación de confianza](workload-identity-federation-create-trust.md) en el registro de la aplicación y valida el token externo con la dirección URL del emisor de Open ID Connect (OIDC) en el IdP externo.
1. Cuando se cumplen las comprobaciones, la plataforma de identidad de Microsoft emite un token de acceso a la carga de trabajo externa.
1. La carga de trabajo externa accede a los recursos protegidos de Azure AD mediante el token de acceso de la plataforma de identidad de Microsoft. Un flujo de trabajo de Acciones de GitHub, por ejemplo, usa el token de acceso para publicar una aplicación web a Azure App Service.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre cómo funciona la federación de identidades de carga de trabajo:
- Cómo Azure AD usa la [concesión de credenciales de cliente de OAuth 2.0](v2-oauth2-client-creds-grant-flow.md#third-case-access-token-request-with-a-federated-credential) y una aserción de cliente emitida por otro IdP para obtener un token.
- Cómo crear, eliminar, obtener o actualizar [credenciales de identidad federada](/graph/api/resources/federatedidentitycredentials-overview?view=graph-rest-beta&preserve-view=true) en un registro de aplicación mediante Microsoft Graph.
- Lea la [documentación de Acciones de GitHub](https://docs.github.com/actions/deployment/security-hardening-your-deployments/configuring-openid-connect-in-azure) para obtener más información sobre cómo configurar el flujo de trabajo de Acciones de GitHub para obtener un token de acceso del proveedor de identidades de Microsoft y acceder a los recursos de Azure.
