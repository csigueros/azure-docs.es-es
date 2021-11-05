---
title: 'Búsqueda y filtrado de miembros y propietarios de grupos (versión preliminar): Azure Active Directory | Microsoft Docs'
description: Busque y filtre miembros y propietarios de grupos en Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 10/22/2021
ms.author: curtand
ms.reviewer: jodah
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8774bef1dd939daea4384a25bc35d22f3475f57a
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131049635"
---
# <a name="search-groups-and-members-in-azure-active-directory"></a>Búsqueda de grupos y miembros en Azure Active Directory

En este artículo se explica cómo buscar miembros y propietarios de un grupo y cómo usar filtros de búsqueda en el portal de Azure Active Directory (Azure AD). Las funciones de búsqueda para grupos incluyen:

- Funcionalidades de búsqueda de grupos, como la búsqueda de substring en nombres de grupos.
- Opciones de filtrado y ordenación en las listas de miembros y propietarios.
- Funcionalidades de búsqueda para listas de miembros y propietarios.

## <a name="group-search-and-sort"></a>Búsqueda y ordenación de grupos.

En la página **Todos los grupos**, al escribir una cadena de búsqueda, ahora puede alternar entre las búsquedas "contiene" y "empieza con" solo en la página **Todos los grupos**. La búsqueda de substring solo se realiza en palabras enteras y los caracteres especiales también se buscan como una búsqueda con AND. Por ejemplo, al buscar -Nombre, se inicia una búsqueda de la substring "Nombre" y una búsqueda de "-". En la búsqueda de substring se distingue entre mayúsculas y minúsculas. También se buscan las propiedades id. de objeto o mailNickname.

![Nuevas búsquedas de substring en la página Todos los grupos](./media/groups-members-owners-search/members-list.png)

Por ejemplo, una búsqueda de "directiva" devuelve "Directiva de MDM: oeste" y "Grupo de directivas". Sin embargo, no se devolverá un grupo denominado "New_policy". Puede ordenar la lista **Todos los grupos** por nombre en orden ascendente o descendente.

## <a name="group-member-search-and-filter"></a>Búsqueda y filtrado de miembros de grupos

### <a name="search-group-member-and-owner-lists"></a>Búsqueda de listas de miembros y propietarios de grupos

Puede buscar los miembros o propietarios de un grupo específico por nombre y, al escribir una cadena de búsqueda, se realiza automáticamente una búsqueda `contains`. Por ejemplo, una búsqueda de "Scott" devuelve tanto Scott Wilkinson como Maya Scott.

![Nuevas búsquedas de substring en las listas de miembros y propietarios del grupo](./media/groups-members-owners-search/groups-search-preview.png)

### <a name="filter-member-and-owner-lists"></a>Filtrado de las listas de miembros y propietarios

También puede filtrar las listas de miembros y propietarios del grupo por tipo de usuario. Esta información se encuentra en la columna **Tipo de usuario** de la lista de miembros o propietarios. Puede filtrar la lista para ver solo miembros o invitados.

La página **Miembros** incluye todos los miembros únicos del grupo, incluida cualquier persona que herede su pertenencia a grupos de otro grupo.

También puede buscar y filtrar las listas individualmente. El filtrado de la lista de todos los miembros no afecta a los filtros que se aplican a la lista de miembros directos.

## <a name="group-memberships"></a>Pertenencias a grupos

También puede ver las pertenencias a grupos de un grupo en la página **Pertenencias a grupos**. La página **Pertenencias a grupos** admite operaciones de búsqueda, ordenación y filtro similares a las otras páginas de grupos.

## <a name="group-member-counts"></a>Recuentos de miembros del grupo

La página **Información general** del grupo proporciona recuentos de miembros de los grupos. Puede ver el número total de miembros directos de un grupo y el número total de pertenencias (todos los miembros únicos del grupo, incluidas las pertenencias heredadas) en la página **Información general**.

![Mayor precisión en los recuentos de pertenencia a grupos](./media/groups-members-owners-search/member-numbers.png)

## <a name="next-steps"></a>Pasos siguientes

En estos artículos se proporciona información adicional sobre cómo trabajar con los grupos en Azure AD.

- [Visualización de grupos y miembros](../fundamentals/active-directory-groups-view-azure-portal.md)
- [Administración de pertenencia al grupo](../fundamentals/active-directory-groups-membership-azure-portal.md)
- [Administrar reglas dinámicas de los usuarios de un grupo](groups-create-rule.md)
- [Edición de la configuración de un grupo](../fundamentals/active-directory-groups-settings-azure-portal.md)
- [Administrar el acceso a los recursos mediante grupos](../fundamentals/active-directory-manage-groups.md)
- [Administrar el acceso a las aplicaciones SaaS mediante grupos](groups-saasapps.md)
- [Administrar grupos mediante los comandos de PowerShell](../enterprise-users/groups-settings-v2-cmdlets.md)
- [Incorporación de una suscripción de Azure a Azure Active Directory](../fundamentals/active-directory-how-subscriptions-associated-directory.md)
