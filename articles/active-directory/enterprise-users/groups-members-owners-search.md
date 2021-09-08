---
title: 'Búsqueda y filtrado de miembros y propietarios de grupos (versión preliminar): Azure Active Directory | Microsoft Docs'
description: Busque y filtre miembros y propietarios de grupos en Azure Portal.
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: krbain
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: e45ccf6a2d6a11e0acc8bf1c7c3a00a4867e0d2a
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433024"
---
# <a name="search-groups-and-members-preview-in-azure-active-directory"></a>Búsqueda de grupos y miembros (versión preliminar) en Azure Active Directory

En este artículo se explica cómo buscar miembros y propietarios de un grupo y cómo usar filtros de búsqueda como parte de la versión preliminar de mejora de grupos en el portal de Azure Active Directory (Azure AD). Hay una gran cantidad de mejoras en las experiencias de los grupos que le ayudarán a administrar los grupos, incluidos los miembros y propietarios, de forma rápida y sencilla. Para más información sobre las versiones preliminares, consulte [Términos de uso complementarios de las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Los cambios de esta versión preliminar incluyen:

- Nuevas funcionalidades de búsqueda de grupos, como la búsqueda de substring en nombres de grupos.
- Nuevas opciones de filtrado y ordenación en las listas de miembros y propietarios.
- Nuevas funcionalidades de búsqueda para listas de miembros y propietarios.
- Recuentos de grupos más precisos para grupos grandes.

## <a name="enabling-and-managing-the-preview"></a>Habilitación y administración de la versión preliminar

Hemos facilitado la unión a la versión preliminar:

  1. Inicie sesión en el [portal de Azure AD](https://portal.azure.com) y seleccione **Grupos**.
  2. En la página Grupos - Todos los grupos, seleccione el banner situado en la parte superior de la página para unirse a la versión preliminar.

También puede consultar las características y mejoras seleccionando **Información de versión preliminar** en la página **Todos los grupos**. Después de unirse a la versión preliminar, puede ver la etiqueta de versión preliminar en todas las páginas de grupos que tienen mejoras y que forman parte de dicha versión. No se han actualizado todas las páginas de grupos como parte de esta versión preliminar.

Si tiene algún problema, puede volver a la experiencia heredada seleccionando el banner situado en la parte superior de la página **Todos los grupos**. Agradecemos sus comentarios para que podamos mejorar nuestra experiencia.

## <a name="group-search-and-sorting"></a>Búsqueda y ordenación de grupos

La búsqueda de listas de grupos se ha mejorado de modo que, cuando escribe una cadena de búsqueda, esta realiza automáticamente una búsqueda de substring y `startswith` en la lista de nombres de grupos. La búsqueda de substring solo se realiza en palabras completas y no incluye caracteres especiales. En la búsqueda de substring se distingue entre mayúsculas y minúsculas.

![Nuevas búsquedas de substring en la página Todos los grupos](./media/groups-members-owners-search/groups-search-preview.png)

Por ejemplo, una búsqueda de "directiva" devolverá "Directiva de MDM: oeste" y "Grupo de directivas". Sin embargo, no se devolverá un grupo denominado "New_policy".

- También puede realizar la misma búsqueda en las listas de pertenencia a grupos.
- Ahora puede ordenar la lista de grupos por nombre mediante las flechas situadas a la derecha del encabezado de columna de nombre para ordenar la lista en orden ascendente o descendente.

## <a name="group-member-search-and-filtering"></a>Búsqueda y filtrado de miembros de grupos

### <a name="search-group-member-and-owner-lists"></a>Búsqueda de listas de miembros y propietarios de grupos

Ahora puede buscar los miembros de un grupo específico por nombre y realizar la misma búsqueda también en la lista de propietarios del grupo. En la nueva experiencia, si escribe una cadena en el cuadro de búsqueda, se realizará automáticamente una búsqueda StartsWith. Por ejemplo, la búsqueda de "Scott" devolverá Scott Wilkinson.

![Nuevas búsquedas de substring en las listas de miembros y propietarios del grupo](./media/groups-members-owners-search/members-list.png)

### <a name="filter-member-and-owners-list"></a>Filtrado de la lista de miembros y propietarios

Además de realizar búsquedas, ahora puede filtrar las listas de miembros y propietarios por tipo de usuario. Esta es la información que se encuentra en la columna de tipo de usuario de la lista. Por lo tanto, puede filtrar la lista por miembros e invitados para determinar si hay invitados en el grupo.

### <a name="view-and-manage-membership"></a>Visualización y administración de pertenencia

Además de ver los miembros directos de un grupo específico, ahora puede ver la lista de todos los miembros del grupo en la página Miembros. La lista de miembros incluye todos los miembros únicos del grupo, incluidos los miembros transitivos.

También puede filtrar la lista de miembros directos y la lista de todos los miembros, así como realizar búsquedas en ellas, individualmente. El filtrado de la lista de todos los miembros no afecta a los filtros que se aplican a la lista de miembros directos.

## <a name="improved-group-member-counts"></a>Recuentos de miembros del grupo mejorado

Hemos mejorado la página **Información general** de Grupos para proporcionar recuentos de miembros del grupo para grupos de todos los tamaños. Puede ver los recuentos de miembros incluso para grupos con más de 1000 miembros. Ahora puede ver el número total de miembros directos de un grupo y el número total de pertenencias (todos los miembros únicos del grupo, incluidos los miembros transitivos) en la página **Información general**.

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
