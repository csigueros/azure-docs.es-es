---
title: 'Usar un grupo para administrar el acceso a aplicaciones SaaS: Azure AD | Microsoft Docs'
description: Cómo usar los grupos en Azure Active Directory para proporcionar acceso a aplicaciones SaaS integradas con Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 06/30/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f5284ca0669331e507ec39e4aa1feb261380a6c
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113128631"
---
# <a name="using-a-group-to-manage-access-to-saas-applications"></a>Uso de un grupo para administrar el acceso a las aplicaciones SaaS

Con Azure Active Directory (Azure AD) y un plan de licencias de Azure AD Premium, puede usar grupos para asignar el acceso a una aplicación SaaS integrada con Azure AD. Por ejemplo, si desea asignar acceso al departamento de marketing para utilizar cinco aplicaciones SaaS diferentes, puede crear un grupo de Office 365 o un grupo de seguridad que contenga a los usuarios del departamento de marketing y, a continuación, asignar ese grupo a las cinco aplicaciones SaaS requeridas por el departamento de marketing. De este modo, ahorrará tiempo administrando la pertenencia a grupos del departamento de marketing en un solo lugar. A continuación, los usuarios se asignan a la aplicación cuando se agregan como miembros del grupo de marketing, y se eliminan sus asignaciones de la aplicación cuando se eliminan del grupo de marketing. Esta función se puede utilizar con cientos de aplicaciones que puede agregar desde la Galería de aplicaciones de Azure AD.

> [!IMPORTANT]
> Solo puede utilizar esta característica después de iniciar una evaluación de Azure AD Premium o de adquirir el plan de licencias Azure AD Premium.
> La asignación basada en grupo solo se admite en grupos de seguridad.
> No se admiten las pertenencias a grupos anidadas para la asignación basada en grupos a aplicaciones en este momento.

## <a name="to-assign-access-for-a-user-or-group-to-a-saas-application"></a>Para asignar acceso para un usuario o un grupo a una aplicación SaaS

1. En el [centro de administración de Azure AD](https://aad.portal.azure.com), seleccione **Aplicaciones empresariales**.
2. Seleccione una aplicación que haya agregado desde la Galería de aplicaciones para abrirla.
3. Seleccione **Usuarios y grupos** y, luego, **Agregar usuario**.
4. En **Agregar asignación**, seleccione **Usuarios y grupos** para abrir la lista de selección **Usuarios y grupos**.
6. Seleccione todos los grupos o usuarios que desee y, a continuación, haga clic o puntee en **Seleccionar** para agregarlos a la lista **Agregar asignación**. También puede asignar un rol a un usuario en esta fase.
7. Seleccione **Asignar** para asignar los usuarios o los grupos a la aplicación de empresa seleccionada.

## <a name="next-steps"></a>Pasos siguientes
Estos artículos proporcionan información adicional sobre Azure Active Directory.

* [Administración del acceso a los recursos con grupos de Azure Active Directory](../fundamentals/active-directory-manage-groups.md)
* [Administración de aplicaciones en Azure Active Directory](../manage-apps/what-is-application-management.md)
* [Cmdlets de Azure Active Directory para configurar las opciones de grupo](../enterprise-users/groups-settings-cmdlets.md)
* [¿Qué es Azure Active Directory?](../fundamentals/active-directory-whatis.md)
* [Integración de las identidades locales con Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
