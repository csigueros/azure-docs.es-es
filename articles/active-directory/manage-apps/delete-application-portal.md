---
title: 'Inicio rápido: Eliminación de una aplicación del inquilino'
titleSuffix: Azure AD
description: Este inicio rápido utiliza Azure Portal para eliminar una aplicación del inquilino de Azure Active Directory (Azure AD).
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: quickstart
ms.workload: identity
ms.date: 07/23/2021
ms.author: davidmu
ms.reviewer: alamaral
ms.openlocfilehash: a29ac9aa0cecaefb3d9ada87b60d2bebb8c5269b
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123112428"
---
# <a name="quickstart-delete-an-application-from-your-tenant"></a>Inicio rápido: Eliminación de una aplicación del inquilino

Este inicio rápido utiliza Azure Portal para eliminar una aplicación que se agregó al inquilino de Azure Active Directory (Azure AD).

Para más información sobre el inicio de sesión único y Azure, consulte [¿Qué es el inicio de sesión único?](what-is-single-sign-on.md)

## <a name="prerequisites"></a>Requisitos previos

Para eliminar una aplicación del inquilino de Azure AD, necesita:

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Uno de los siguientes roles: Administrador global, Administrador de aplicaciones en la nube, Administrador de aplicaciones o Propietario de la entidad de servicio.
- Opcional: Haber finalizado el inicio rápido de [Visualización de las aplicaciones](view-applications-portal.md).
- Opcional: Haber finalizado el inicio rápido de [Incorporación de una aplicación](add-application-portal.md).
- Opcional: Haber finalizado el inicio rápido de [Configuración de una aplicación](add-application-portal-configure.md).
- Opcional: haber finalizado [Asignación de usuarios a una aplicación](add-application-portal-assign-users.md).
- Opcional: Haber finalizado el inicio rápido de [Configuración del inicio de sesión único](add-application-portal-setup-sso.md).

>[!IMPORTANT]
>Use un entorno que no sea de producción para probar los pasos de esta guía de inicio rápido.

> [!NOTE]
>Para eliminar una aplicación de Azure AD, se debe asignar a un usuario uno de los siguientes roles: administrador global, administrador de aplicaciones en la nube, administrador de aplicaciones o propietario de la entidad de servicio.

## <a name="delete-an-application-from-your-azure-ad-tenant"></a>Eliminación de una aplicación del inquilino de Azure AD

Para eliminar una aplicación del inquilino de Azure AD:

1. En el portal de Azure AD, seleccione **Aplicaciones empresariales**. Busque y seleccione la aplicación que desea eliminar. En este caso, queremos eliminar **360 Online**.
1. En la sección **Administrar** en el panel izquierdo, seleccione **Propiedades**.
1. Seleccione **Eliminar** y, a continuación, seleccione **Sí** para confirmar que quiere eliminar la aplicación de su inquilino de Azure AD.

:::image type="content" source="media/add-application-portal/delete-application.png" alt-text="Captura de la pantalla Propiedades que muestra cómo cambiar el logotipo.":::

> [!TIP]
> La administración de aplicaciones se puede automatizar mediante Graph API, consulte el artículo sobre la [automatización de la administración de aplicaciones con Microsoft Graph API](/graph/application-saml-sso-configure-api).

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando haya terminado con esta serie de inicios rápidos, considere la posibilidad de eliminar la aplicación para limpiar el inquilino de prueba. En este inicio rápido se ha indicado cómo eliminar la aplicación.

## <a name="next-steps"></a>Pasos siguientes

Ha completado la serie de inicios rápidos. A continuación, obtenga información acerca del inicio de sesión único (SSO), para lo que debe consultar [¿Qué es el inicio de sesión único (SSO)?](what-is-single-sign-on.md) O bien, lea los procedimientos recomendados en la administración de aplicaciones.
> [!div class="nextstepaction"]
> [Procedimientos recomendados de administración de aplicaciones](application-management-fundamentals.md)
