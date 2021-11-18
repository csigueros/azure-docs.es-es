---
title: Concesión del consentimiento del administrador para todo el inquilino a una aplicación
titleSuffix: Azure AD
description: Obtenga información sobre cómo conceder consentimiento para todo el inquilino a una aplicación de modo que no se les solicite consentimiento a los usuarios finales al iniciar sesión en una aplicación.
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 10/23/2021
ms.author: davidmu
ms.reviewer: ergreenl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8845643a7ef3816cb4bb3b2e25d511467d2bfcbb
ms.sourcegitcommit: 05c8e50a5df87707b6c687c6d4a2133dc1af6583
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132546602"
---
# <a name="grant-tenant-wide-admin-consent-to-an-application"></a>Concesión del consentimiento del administrador para todo el inquilino a una aplicación

  En este artículo va a aprender a conceder consentimiento del administrador para todo el inquilino a una aplicación en Azure Active Directory (Azure AD).

Al conceder el consentimiento del administrador de todo el inquilino a una aplicación, todos los usuarios pueden iniciar sesión en la aplicación. Para restringir qué usuarios pueden iniciar sesión en una aplicación, configure esta de modo que requiera asignación de usuarios y luego asigne usuarios o grupos a la aplicación. Para más información, consulte [Métodos para asignar usuarios y grupos](./assign-user-or-group-access-portal.md).

El consentimiento del administrador para todo el inquilino a una aplicación concede acceso a la aplicación y al publicador de esta a los datos de la organización. Antes de conceder el consentimiento, repase cuidadosamente los permisos que solicita la aplicación. Para más información acerca de cómo dar consentimiento a las aplicaciones, consulte [Marco de consentimiento de Azure Active Directory](../develop/consent-framework.md).

La concesión del consentimiento del administrador para todo el inquilino puede revocar los permisos concedidos previamente para todo el inquilino. Los permisos concedidos previamente por los usuarios en su propio nombre no se verán afectados.

## <a name="prerequisites"></a>Prerrequisitos

La concesión del consentimiento del administrador para todo el inquilino requiere que inicie sesión como un usuario que esté autorizado para dar su consentimiento en nombre de la organización.

Para conceder consentimiento del administrador para todo el inquilino necesita:

- Una cuenta de Azure con uno de los siguientes roles: Administrador global, Administrador de roles con privilegios, Administrador de aplicaciones en la nube o Administrador de aplicaciones. También se puede autorizar a un usuario para conceder consentimiento para todo el inquilino si se le asigna un [rol de directorio personalizado](../roles/custom-create.md) que incluya el [permiso para conceder permisos a las aplicaciones](../roles/custom-consent-permissions.md).

## <a name="grant-tenant-wide-admin-consent-in-enterprise-apps"></a>Concesión de consentimiento del administrador para todo el inquilino en aplicaciones empresariales

Puede conceder el consentimiento del administrador para todo el inquilino a través de *Aplicaciones empresariales* si la aplicación ya se ha aprovisionado en el inquilino. Por ejemplo, se podría aprovisionar una aplicación en el inquilino si al menos un usuario ya ha dado su consentimiento a la aplicación. Para más información, vea [Cómo y por qué se agregan aplicaciones a Azure AD](../develop/active-directory-how-applications-are-added.md).

Para conceder el consentimiento del administrador para todo el inquilino a una de las aplicaciones mostradas en **Aplicaciones empresariales**:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con uno de los roles indicados en la sección de requisitos previos.
1. Seleccione **Azure Active Directory** y, luego, **Aplicaciones empresariales**.
1. Seleccione la aplicación a la que quiere conceder consentimiento del administrador para todo el inquilino y luego seleccione **Permisos**.
   :::image type="content" source="media/grant-tenant-wide-admin-consent/grant-tenant-wide-admin-consent.png" alt-text="Captura de pantalla que muestra cómo conceder consentimiento del administrador para todo el inquilino.":::

1. Repase cuidadosamente los permisos que requiere la aplicación. Si está de acuerdo con los permisos que requiere la aplicación, seleccione **Conceder consentimiento del administrador**.

## <a name="grant-admin-consent-in-app-registrations"></a>Concesión del consentimiento del administrador en Registros de aplicaciones

Para las aplicaciones desarrolladas por la organización, o bien las que se hayan registrado directamente en el inquilino de Azure AD, también puede conceder el consentimiento del administrador para todo el inquilino desde **Registros de aplicaciones** en Azure Portal.

Para conceder el consentimiento del administrador para todo el inquilino desde **Registros de aplicaciones**:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con uno de los roles indicados en la sección de requisitos previos.
1. Seleccione **Azure Active Directory** y luego **Registros de aplicaciones**.
1. Seleccione la aplicación a la que quiera conceder el consentimiento del administrador para todo el inquilino.
1. Seleccione **Permisos de API**.
1. Repase cuidadosamente los permisos que requiere la aplicación. Si está de acuerdo, seleccione **Conceder consentimiento del administrador**.

## <a name="construct-the-url-for-granting-tenant-wide-admin-consent"></a>Construcción de la dirección URL para conceder el consentimiento del administrador para todo el inquilino

Al conceder el consentimiento del administrador para todo el inquilino con cualquiera de los métodos descritos antes, se abre una ventana desde Azure Portal para solicitar el consentimiento del administrador para todo el inquilino. Si conoce el identificador de cliente (también conocido como id. de la aplicación) de la aplicación, puede crear la misma dirección URL para conceder consentimiento del administrador para todo el inquilino.

La dirección URL de consentimiento del administrador para todo el inquilino tiene el formato siguiente:

```http
https://login.microsoftonline.com/{tenant-id}/adminconsent?client_id={client-id}
```

donde:

- `{client-id}` es el identificador de cliente de la aplicación (también conocido como id. de la aplicación).
- `{tenant-id}` es el identificador de inquilino de la organización o cualquier nombre de dominio comprobado.

Como siempre, antes de conceder el consentimiento, revise con atención los permisos que solicita la aplicación.

## <a name="next-steps"></a>Pasos siguientes

[Configuración del consentimiento de los usuarios finales a las aplicaciones](configure-user-consent.md)

[Configuración del flujo de trabajo de consentimiento del administrador](configure-admin-consent-workflow.md)
