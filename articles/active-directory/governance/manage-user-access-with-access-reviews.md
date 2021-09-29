---
title: Administración del acceso de los usuarios con las revisiones de acceso - Azure AD
description: Aprenda a administrar el acceso de los usuarios, como la pertenencia a un grupo o la asignación a una aplicación con revisiones de acceso de Azure Active Directory
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
editor: markwahl-msft
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 06/21/2018
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0107546f47363d0af51f1b8da5e7b01a9c773ec
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124791714"
---
# <a name="manage-user-access-with-azure-ad-access-reviews"></a>Administración del acceso de los usuarios con las revisiones de acceso de Azure AD

Con Azure Active Directory (Azure AD), puede asegurarse de que los usuarios tienen el acceso adecuado. Puede pedir a los propios usuarios o a quien decida en su lugar que participen en una revisión de acceso y vuelvan a certificar (o atestiguar) el acceso de los usuarios. Los revisores pueden dar su aprobación para cada necesidad de acceso continuado de los usuarios, en función de las sugerencias de Azure AD. Cuando una revisión de acceso haya terminado, es posible hacer cambios y retirar la concesión de acceso a los usuarios que ya no lo necesitan.

> [!NOTE]
> Si desea revisar solo el acceso de los usuarios invitados y no el de todos los tipos de usuarios, consulte [Administración del acceso de los invitados con las revisiones de acceso de Azure AD](manage-guest-access-with-access-reviews.md). Si desea revisar la pertenencia de los usuarios a roles administrativos tales como administrador global, consulte [Inicio de una revisión de acceso en Azure AD Privileged Identity Management](../privileged-identity-management/pim-create-azure-ad-roles-and-resource-roles-review.md).

## <a name="prerequisites"></a>Prerrequisitos

- Azure AD Premium P2

Para obtener más información, consulte [Requisitos de licencia](access-reviews-overview.md#license-requirements).

## <a name="create-and-perform-an-access-review"></a>Creación y realización de una revisión de acceso

Puede tener uno o más usuarios como revisores en una revisión de acceso.  

1. Seleccione un grupo de Azure AD con uno o más miembros. O bien, seleccione una aplicación conectada a Azure AD con uno o más usuarios asignados a ella. 

2. Decida si cada usuario revisará su propio acceso o bien si uno o más usuarios revisarán el acceso de todos.

3. En uno de los siguientes roles: un administrador global, un administrador de usuarios o un propietario del grupo de seguridad de M365 o AAD (versión preliminar) del grupo que se va a revisar, vaya a la [página de Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).

4. Cree la revisión de acceso. Para más información, consulte [Creación de una revisión de acceso de los grupos o las aplicaciones](create-access-review.md).

5. Cuando se inicie la revisión de acceso, pida a los revisores que proporcionen una entrada. De forma predeterminada, cada uno recibe un correo electrónico de Azure AD con un vínculo al panel de acceso con el que podrán [realizar la revisión de acceso de los grupos o las aplicaciones](perform-access-review.md).

6. Si los revisores no han proporcionado información, puede pedir a Azure AD que les envíe un recordatorio. De forma predeterminada, Azure AD envía automáticamente un recordatorio hacia la mitad del plazo de finalización a los revisores que no hayan respondido.

7. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para más información, consulte [Revisión de acceso de los grupos o las aplicaciones](complete-access-review.md).


## <a name="next-steps"></a>Pasos siguientes

[Creación de una revisión de acceso de grupos o aplicaciones](create-access-review.md)