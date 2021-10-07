---
title: Adición de una aplicación de inicio de sesión único basado en OpenID Connect
description: Aprenda a agregar una aplicación de inicio de sesión único basado en OpenID Connect en Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 09/22/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.openlocfilehash: 24d30dc81b2b2a3da2ac5f172dabe77cf5b01f9c
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129058283"
---
# <a name="add-an-openid-connect-based-single-sign-on-application-in-azure-active-directory"></a>Adición de una aplicación de inicio de sesión único basado en OpenID Connect en Azure Active Directory

Agregue una aplicación que admita el inicio de sesión único (SSO) basado en [OpenID Connect (OIDC)](../develop/active-directory-v2-protocols.md) al inquilino de Azure Active Directory (Azure AD).

Para probar los pasos de esta página, se recomienda usar un entorno que no sea de producción.

## <a name="prerequisites"></a>Requisitos previos

Para configurar el inicio de sesión único basado en OIDC, necesita:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.

## <a name="add-the-application"></a>Adición de la aplicación

Al agregar una aplicación que usa el estándar OIDC para el inicio de sesión único, seleccionará un botón de configuración. Al seleccionar el botón, completará el proceso de registro de la aplicación.

Para configurar el inicio de sesión único basado en OIDC para una aplicación:

1. Vaya al [centro de administración de Azure Active Directory](https://aad.portal.azure.com) e inicie sesión con uno de los roles enumerados en los requisitos previos.
1. En el menú de la izquierda, seleccione **Aplicaciones empresariales**. Se abre el panel **Todas las aplicaciones**, en el que se ve una lista de las aplicaciones que hay en su inquilino de Azure AD. 
1. En el panel **Aplicaciones empresariales**, seleccione **Nueva aplicación**.
1. Se abre el panel **Examinar la Galería de Azure AD**, que muestra iconos para plataformas en la nube, aplicaciones locales y aplicaciones destacadas. Las aplicaciones que aparecen en la sección **Aplicaciones destacadas** incluyen iconos que indican si admiten el inicio de sesión único federado y el aprovisionamiento. Busque y seleccione la aplicación. En este ejemplo, se usa **SmartSheet**.
1. Seleccione **Suscribirse**. Inicie sesión con las credenciales de la cuenta de usuario de Azure Active Directory. Si ya tiene una suscripción para la aplicación, se validan los detalles del usuario y la información del inquilino. Si la aplicación no puede comprobar el usuario, se le redirigirá a registrarse en el servicio de la aplicación.

    :::image type="content" source="media/add-application-portal-setup-oidc-sso/oidc-sso-configuration.png" alt-text="Completar la pantalla de consentimiento de una aplicación.":::

1. Seleccione **Consentimiento en nombre de la organización** y, luego, **Aceptar**. La aplicación se agrega al inquilino y la página principal de la aplicación aparece. Para más información sobre el consentimiento del usuario y del administrador, consulte [Consentimiento del usuario y del administrador](../develop/howto-convert-app-to-be-multi-tenant.md#understand-user-and-admin-consent).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo planear una implementación de inicio de sesión único.
> [!div class="nextstepaction"]
> [Planeamiento de la implementación del inicio de sesión único](plan-sso-deployment.md)
