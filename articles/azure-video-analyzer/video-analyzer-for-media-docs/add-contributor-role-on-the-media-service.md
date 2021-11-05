---
title: Adición del rol Colaborador en Media Services
description: En este tema se explica cómo agregar un rol Colaborador en Media Services.
ms.author: itnorman
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 294587e9f502a7f927bb905fbd354ab588955a85
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093377"
---
# <a name="add-contributor-role-on-the-media-services"></a>Adición del rol Colaborador en Media Services

En este artículo se describe cómo asignar el rol Colaborador en Media Services.

> [!NOTE]
> Si va a crear Azure Video Analyzer for Media a través de la interfaz de usuario de Azure Portal, la identidad administrada seleccionada se asignará automáticamente con un permiso de colaborador en la cuenta de Media Service seleccionada.

## <a name="prerequisites"></a>Prerrequisitos

1. Azure Media Services (AMS)
2. Identidad administrada asignada por el usuario
> [!NOTE]
> Necesitará una suscripción a Azure en la que tenga acceso tanto al rol [Colaborador][docs-role-contributor] como al rol [Administrador de acceso de usuario][docs-role-administrator] para Azure Media Services y la identidad administrada asignada por el usuario. Si no tiene los permisos adecuados, pida al administrador de la cuenta que se los conceda. La instancia de Azure Media Services asociada debe estar en la misma región que la cuenta de Video Analyzer for Media.


## <a name="add-contributor-role-on-the-media-services"></a>Adición del rol Colaborador en Media Services
### <a name="azure-portal"></a>[Azure Portal](#tab/portal/)

### <a name="add-contributor-role-on-the-media-services-in-the-azure-portal"></a>Adición del rol Colaborador en Media Services en Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
    * En la barra de búsqueda de la parte superior, escriba **Media Services**.
    * Busque y seleccione el recurso de Media Service.
1. En el menú de la izquierda, haga clic en **Control de acceso (IAM)** .
    * Haga clic en **Agregar** > **Agregar asignación de roles**. Si no tiene permisos para asignar roles, la opción **Agregar asignación de roles** se deshabilitará.
1. En la lista de roles, seleccione el rol [Colaborador][docs-role-contributor] y haga clic en **Siguiente**.
1. En **Asignar acceso a**, seleccione el botón de radio *Identidad administrada*.
    * Haga clic en el botón **+Seleccionar miembros** y aparecerá el panel **Select managed identities** (Seleccionar identidades administradas).
1. **Seleccione** lo siguiente:
    * En **Suscripción**, la suscripción donde se encuentra la identidad administrada.
    * En **Identidad administrada**, seleccione *Identidad administrada asignada por el usuario*.
    * En la sección **Seleccionar**, busque la identidad administrada a la que le gustaría conceder permisos de colaborador en el recurso de Media Services.    
1. Una vez que haya encontrado la entidad de seguridad, haga clic en esta para seleccionarla.
1. Para asignar el rol, haga clic en **Review + assign** (Revisar y asignar).

<!-- links -->
[docs-role-contributor]: ../../role-based-access-control/built-in-roles.md#contributor
[docs-role-administrator]: ../../role-based-access-control/built-in-roles.md#user-access-administrator
