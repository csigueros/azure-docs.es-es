---
title: 'Administración del acceso con revisiones de acceso: Azure AD'
description: Obtenga información sobre cómo administrar el acceso de usuario e invitado como pertenencia a un grupo o asignación a una aplicación con revisiones de acceso de Azure Active Directory
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
ms.date: 08/20/2021
ms.author: ajburnle
ms.reviewer: mwahl
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cf6b15b68c864c3f98d3a4866931148cfcd6411
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093206"
---
# <a name="manage-user-and-guest-user-access-with-access-reviews"></a>Administración del acceso de usuarios y usuarios invitados con revisiones de acceso
 
Con Azure Active Directory (Azure AD), puede asegurarse fácilmente de que los usuarios o invitados tengan el acceso adecuado. Puede pedir a los propios usuarios o a quien decida en su lugar que participen en una revisión de acceso y vuelvan a certificar (o atestiguar) el acceso de los usuarios. Los revisores pueden dar su aprobación para cada necesidad de acceso continuado de los usuarios, en función de las sugerencias de Azure AD. Cuando una revisión de acceso haya terminado, es posible hacer cambios y retirar la concesión de acceso a los usuarios que ya no lo necesitan.
 
> [!NOTE]
> En este artículo se describe cómo realizar revisiones de acceso para usuarios y aplicaciones. Para ver información sobre cómo realizar una revisión de acceso para varios recursos en paquetes de acceso, consulte aquí [Revisión del acceso de un paquete de acceso en la administración de derechos de Azure AD](entitlement-management-access-reviews-review-access.md). Si desea revisar el acceso de usuario o entidad de servicio a Azure AD o roles de recursos de Azure, consulte [Inicio de una revisión de acceso en Azure AD Privileged Identity Management](../privileged-identity-management/pim-how-to-start-security-review.md).
 
## <a name="prerequisites"></a>Prerrequisitos
 
- Azure AD Premium P2
 
Para obtener más información, consulte [Requisitos de licencia](access-reviews-overview.md#license-requirements).
 
## <a name="create-and-perform-an-access-review-for-users"></a>Creación y realización de una revisión de acceso para los usuario
En primer lugar, debe estar asignado a alguno de los siguientes roles:
- Administrador global
- Administrador de usuarios
- Administrador de Identity Governance 
- Administrador de roles con privilegios (solo para revisiones de grupos a los que se pueden asignar roles)
- (Versión preliminar) Microsoft 365 o AAD propietario del grupo de seguridad del grupo que se va a revisar 

Después, vaya a la [página de Identity Governance](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/) para asegurarse de que las revisiones de acceso están listas para su organización.

Puede tener uno o más usuarios como revisores en una revisión de acceso.  
 
1. Seleccione un grupo de Azure AD con uno o más miembros. O bien, seleccione una aplicación conectada a Azure AD con uno o más usuarios asignados a ella. 
 
2. Decida si cada usuario revisará su propio acceso o bien si uno o más usuarios revisarán el acceso de todos.
 
3. En uno de los roles enumerados anteriormente, vaya a la [página Gobernanza de identidades](https://portal.azure.com/#blade/Microsoft_AAD_ERM/DashboardBlade/).
 
4. Cree la revisión de acceso. Para más información, consulte [Creación de una revisión de acceso de los grupos o las aplicaciones](create-access-review.md).
 
5. Cuando se inicie la revisión de acceso, pida a los revisores que proporcionen una entrada. De forma predeterminada, cada uno recibe un correo electrónico de Azure AD con un vínculo al panel de acceso con el que podrán [realizar la revisión de acceso de los grupos o las aplicaciones](self-access-review.md).
 
6. Si los revisores no han proporcionado información, puede pedir a Azure AD que les envíe un recordatorio. De forma predeterminada, Azure AD envía automáticamente un recordatorio a la mitad de la fecha de finalización a todos los revisores.
 
7. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para más información, consulte [Revisión de acceso de los grupos o las aplicaciones](complete-access-review.md).
 
## <a name="manage-guest-access-with-azure-ad-access-reviews"></a>Administración del acceso de los invitados con las revisiones de acceso de Azure AD
 
Con Azure Active Directory (Azure AD), puede habilitar fácilmente la colaboración entre distintas organizaciones mediante la [característica B2B de Azure AD](../external-identities/what-is-b2b.md). Los usuarios invitados de otros inquilinos pueden ser [invitados por los administradores](../external-identities/add-users-administrator.md) o por [otros usuarios](../external-identities/what-is-b2b.md). Esta capacidad también se aplica a las identidades sociales como las cuentas Microsoft.
 
 
 
## <a name="create-and-perform-an-access-review-for-guests"></a>Creación y realización de una revisión de acceso para invitados
 
Los mismos roles necesarios para crear una revisión de acceso para los usuarios también son necesarios para crear una revisión de acceso para los invitados. Para obtener más información, consulte [Crear y realizar una revisión de acceso para los usuarios](manage-access-review.md#create-and-perform-an-access-review-for-users).

Azure AD ofrece varios escenarios para revisar el acceso de los usuarios invitados.
 
Puede revisar:
 
 - Un grupo de Azure AD con uno o más invitados como miembros.
 - Una aplicación conectada a Azure AD con uno o más usuarios invitados asignados a ella. 
 
Al revisar el acceso de los usuarios invitados a los grupos de Microsoft 365, puede crear una revisión para cada grupo individualmente o activar las revisiones de acceso periódicas automáticas de los usuarios invitados en todos los grupos de Microsoft 365. En el vídeo siguiente se proporciona más información sobre las revisiones de acceso periódicas de los usuarios invitados: 
 
> [!VIDEO https://www.youtube.com/embed/3D2_YW2DwQ8]
 
A continuación, puede decidir si solicitar a cada invitado que revise su propio acceso o preguntar a uno o más usuarios que revise el acceso de cada invitado.
 
 Estos escenarios se tratan en las siguientes secciones.
 
### <a name="ask-guests-to-review-their-own-membership-in-a-group"></a>Se pide a los invitados que revisen su propia pertenencia a un grupo
 
Puede usar las revisiones de acceso para garantizar que los usuarios invitados y agregados a un grupo siguen necesitando acceso. Puede solicitar fácilmente a los invitados que revisen su propia pertenencia a ese grupo.
 
1. Para crear una revisión de acceso para el grupo, seleccione la revisión para incluir solo a los miembros que sean usuarios invitados y para que los miembros se revisen a sí mismos. Para más información, consulte [Creación de una revisión de acceso de los grupos o las aplicaciones](create-access-review.md).
 
2. Pida a cada invitado que revise su propia pertenencia. De forma predeterminada, cada invitado que haya aceptado una invitación recibirá un correo electrónico de Azure AD con un vínculo a la revisión de acceso. Azure AD tiene instrucciones para los invitados sobre cómo [revisar el acceso a grupos o aplicaciones](self-access-review.md).
 
3. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para más información, consulte [Revisión de acceso de los grupos o las aplicaciones](complete-access-review.md).
 
4.  Además de los usuarios que negaron su necesidad de seguir teniendo acceso, puede también quitar a los usuarios que no respondieron.
 
5. Si el grupo no se usó para la administración de acceso, también puede quitar los usuarios que no estuvieran seleccionados para participar en la revisión porque no aceptaran su invitación. La no aceptación podría indicar que la dirección de correo electrónico del usuario invitado tiene un error de escritura. Si se utiliza un grupo como una lista de distribución, quizás algunos usuarios invitados no se seleccionaron para la participación porque son objetos de contacto.
 
### <a name="ask-a-sponsor-to-review-a-guests-membership-in-a-group"></a>Se pide a los patrocinadores que revisen la pertenencia de un invitado a un grupo
 
Puede solicitar a un patrocinador, por ejemplo al propietario de un grupo, que revise la necesidad de un invitado de seguir perteneciendo a un grupo.
 
1. Para crear una revisión de acceso para el grupo, seleccione la revisión para incluir solo a los miembros que sean usuarios invitados. Luego especifique uno o más revisores. Para más información, consulte [Creación de una revisión de acceso de los grupos o las aplicaciones](create-access-review.md).
 
2. Pida a los revisores que proporcionen sus datos de entrada. De forma predeterminada, cada uno recibe un correo electrónico de Azure AD con un vínculo al panel de acceso con el que podrán [realizar la revisión de acceso de los grupos o las aplicaciones](self-access-review.md).
 
3. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para más información, consulte [Revisión de acceso de los grupos o las aplicaciones](complete-access-review.md).

> [!NOTE]
>  Puede impedir que las identidades externas inicien sesión en el inquilino y eliminar las identidades externas del inquilino después de 30 días. Durante este período, la configuración, los resultados, los revisores o los registros de auditoría de la revisión actual no se podrán ver ni configurar. Para obtener más información, vea [Deshabilitar y eliminar identidades externas con revisiones de acceso de Azure AD](access-reviews-external-users.md#disable-and-delete-external-identities-with-azure-ad-access-reviews).

 
### <a name="ask-guests-to-review-their-own-access-to-an-application"></a>Se pide a los invitados que revisen su propio acceso a una aplicación
 
Puede usar revisiones de acceso para asegurarse de que los usuarios que han sido invitados a una aplicación concreta siguen necesitando el acceso. Puede solicitarles de manera fácil que revisen su propia necesidad de acceso.
 
1. Para crear una revisión de acceso para la aplicación, seleccione la revisión para incluir solo a los invitados y para que los usuarios revisen su propio acceso. Para más información, consulte [Creación de una revisión de acceso de los grupos o las aplicaciones](create-access-review.md).
 
2. Se pide a cada invitado que revise su propio acceso a la aplicación. De forma predeterminada, cada invitado que haya aceptado una invitación recibirá un correo electrónico de Azure AD. Dicho correo electrónico tiene un vínculo a la revisión de acceso en el panel de acceso de su organización. Azure AD tiene instrucciones para los invitados sobre cómo [revisar el acceso a grupos o aplicaciones](self-access-review.md).
 
3. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para más información, consulte [Revisión de acceso de los grupos o las aplicaciones](complete-access-review.md).
 
4. Además de los usuarios que negaron su propia necesidad de acceso continuo, también puede eliminar a los usuarios invitados que no respondieron. También puede quitar los usuarios invitados que no se seleccionaron para participar, especialmente si no se han invitado recientemente. Esos usuarios no aceptaron su invitación y, por tanto, no tienen acceso a la aplicación. 
 
### <a name="ask-a-sponsor-to-review-a-guests-access-to-an-application"></a>Se pide a los patrocinadores que revisen su propio acceso a una aplicación
 
Puede solicitar a un patrocinador, por ejemplo, al propietario de una aplicación, que revise la necesidad de un invitado de seguir teniendo acceso a la aplicación.
 
1. Para crear una revisión de acceso para la aplicación, seleccione la revisión para incluir solo a los invitados. Luego especifique uno o más usuarios como revisores. Para más información, consulte [Creación de una revisión de acceso de los grupos o las aplicaciones](create-access-review.md).
 
2. Pida a los revisores que proporcionen sus datos de entrada. De forma predeterminada, cada uno recibe un correo electrónico de Azure AD con un vínculo al panel de acceso con el que podrán [realizar la revisión de acceso de los grupos o las aplicaciones](self-access-review.md).
 
3. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso y aplique los cambios. Para más información, consulte [Revisión de acceso de los grupos o las aplicaciones](complete-access-review.md).
 
### <a name="ask-guests-to-review-their-need-for-access-in-general"></a>Se pide a los invitados que revisen el acceso que requieren en general
 
En algunas organizaciones, los invitados pueden no ser conscientes de a qué grupos pertenecen.
 
 
1. Cree un grupo de seguridad en Azure AD con los invitados como miembros, si aún no existe un grupo adecuado. Por ejemplo, puede crear un grupo con la pertenencia mantenida de forma manual para los invitados. O bien, puede crear un grupo dinámico con un nombre como "Invitados de Contoso" para los usuarios del inquilino Contoso que tengan el valor Guest en el atributo UserType. Tenga en cuenta que un usuario invitado que sea miembro del grupo puede ver a los demás miembros del grupo.
 
2. Para crear una revisión de acceso para ese grupo, seleccione a los revisores para que sean los propios miembros. Para más información, consulte [Creación de una revisión de acceso de los grupos o las aplicaciones](create-access-review.md).
 
3. Pida a cada invitado que revise su propia pertenencia. De forma predeterminada, cada invitado que haya aceptado una invitación recibirá un correo electrónico de Azure AD con un vínculo a la revisión de acceso del panel de acceso de la organización. Azure AD tiene instrucciones para los invitados sobre cómo [revisar el acceso a grupos o aplicaciones](perform-access-review.md). 
 
4. Cuando los revisores hayan proporcionado la información, detenga la revisión de acceso. Para más información, consulte [Revisión de acceso de los grupos o las aplicaciones](complete-access-review.md).
 
5. Quite el acceso de invitado para los invitados cuyo acceso se denegara, no completaran la revisión o no hubieran aceptado su invitación previamente. Si algunos de los invitados son contactos que no fueron seleccionados para participar en la revisión o que previamente no habían aceptado una invitación, puede deshabilitar sus cuentas mediante Azure Portal o PowerShell. Si el invitado ya no necesita el acceso y no es un contacto, puede quitar su objeto de usuario desde su directorio mediante Azure Portal o PowerShell para eliminar el objeto de usuario invitado.

 
## <a name="next-steps"></a>Pasos siguientes
 
[Creación de una revisión de acceso de grupos o aplicaciones](create-access-review.md)


