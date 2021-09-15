---
title: 'Realice una revisión de acceso de los recursos de Azure y los roles de Azure AD en PIM: Azure AD | Microsoft Docs'
description: Aprenda a revisar el acceso a los recursos de Azure y los roles de Azure AD en Azure Active Directory Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: KarenH444
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: pim
ms.date: 9/3/2021
ms.author: curtand
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fc90d7305ced649e0ea754431f9c8ce54ddc57b
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452412"
---
# <a name="perform-an-access-review-of-azure-resource-and-azure-ad-roles-in-pim"></a>Realice una revisión de acceso de los recursos de Azure y los roles de Azure AD en PIM

Privileged Identity Management (PIM) simplifica el modo en que las empresas administran el acceso con privilegios a los recursos de Azure Active Directory (AD) y a otros servicios en línea de Microsoft como Microsoft 365 o Microsoft Intune. Siga los pasos de este artículo para realizar revisiones del acceso a los roles.

Si se le ha asignado un rol administrativo, el administrador de roles con privilegios de su organización le puede pedir que revise y confirme periódicamente que aún necesita ese rol para su trabajo. Puede que reciba un correo electrónico con un vínculo o puede ir directamente a [Azure Portal](https://portal.azure.com).

Si es un administrador de roles con privilegios o un administrador global y está interesado en las revisiones de acceso, puede obtener más información en [How to start an access review](pim-create-azure-ad-roles-and-resource-roles-review.md) (Cómo iniciar una revisión de acceso).

## <a name="approve-or-deny-access"></a>Aprobación o denegación de acceso

Puede aprobar o denegar el acceso en función de si el usuario todavía necesita acceso al rol. Elija **Aprobar** si desea que permanezcan en el rol o **Denegar** si no van a volver a necesitar el acceso. El estado de asignación de los usuarios no cambiará hasta que se cierre la revisión y el administrador aplique los resultados. Entre los escenarios comunes en los que determinados usuarios denegados no pueden tener resultados aplicados a ellos se pueden incluir los siguientes:

- **Revisión de los miembros de un grupo de Windows AD en el entorno local sincronizado**: si el grupo se sincroniza desde una instancia local de Windows AD, no se puede administrar en Azure AD y, por tanto, no se puede cambiar la pertenencia.
- **Revisión de un rol con grupos anidados asignados**: para los usuarios que tienen pertenencia a través de un grupo anidado, la revisión de acceso no quitará su pertenencia al grupo anidado y, por tanto, conservarán el acceso al rol que se está revisando.
- **Usuario no encontrado u otros errores**: estos también pueden dar lugar a que no se admita un resultado de aplicación.

Para buscar y completar la revisión de acceso, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Seleccione **Azure Active Directory** y abra **Privileged Identity Management**.
1. Seleccione **Revisar acceso**. Si tiene revisiones de acceso pendientes, aparecerán en la página de revisiones de acceso.

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png" alt-text="Captura de pantalla de la aplicación Privileged Identity Management, con la hoja Revisar acceso seleccionada para los roles de Azure AD" lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-complete.png":::.

1. Seleccione la revisión que quiere completar.
1. Elija **Aprobar** o **Denegar**. En el cuadro **Proporcionar un motivo**, escriba una justificación comercial para su decisión en caso de ser necesario.

    :::image type="content" source="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png" alt-text="Captura de pantalla de la aplicación Privileged Identity Management, con la revisión de acceso seleccionada para los roles de Azure AD" lightbox="media/pim-perform-azure-ad-roles-and-resource-roles-review/rbac-access-review-azure-ad-completed.png":::.

## <a name="next-steps"></a>Pasos siguientes

- [Cree una revisión de acceso de los recursos de Azure y los roles de Azure AD en PIM](pim-create-azure-ad-roles-and-resource-roles-review.md)
- [Complete una revisión de acceso de los recursos de Azure y los roles de Azure AD en PIM](pim-complete-azure-ad-roles-and-resource-roles-review.md)
