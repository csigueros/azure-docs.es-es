---
title: 'Asignación de propietarios de aplicaciones empresariales: Azure AD | Microsoft Docs'
description: Asignación de propietarios en aplicaciones en Azure Active Directory
services: active-directory
documentationcenter: ''
author: davidmu1
manager: celesteDG
ms.service: active-directory
ms.workload: identity
ms.subservice: app-mgmt
ms.topic: how-to
ms.date: 08/03/2021
ms.author: davidmu
ms.openlocfilehash: 18649f5c597be73e9f14abf00a2df584c56df5d4
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446722"
---
# <a name="assign-enterprise-application-owners"></a>Asignación de propietarios de aplicaciones empresariales

La asignación de propietarios es una manera sencilla de conceder la posibilidad de administrar todos los aspectos de la configuración de Azure AD para un registro de aplicación o aplicación empresarial específicos. Como propietario, un usuario puede administrar la configuración de la aplicación empresarial que es específica de la organización, como la configuración del inicio de sesión único, el aprovisionamiento y las asignaciones de usuarios. Un propietario también puede agregar o quitar otros propietarios. A diferencia de los administradores globales, los propietarios solo pueden administrar las aplicaciones empresariales que poseen.

Solo los usuarios pueden ser propietarios de aplicaciones empresariales. No se pueden asignar grupos como propietarios. Los propietarios pueden agregar credenciales a una aplicación y usarlas para suplantar la identidad de la aplicación. La aplicación puede tener más permisos que el propietario y, por tanto, se trataría de una elevación de privilegios con respecto a lo que el propietario tiene acceso como usuario o entidad de servicio. 

Un propietario de aplicación podría crear o actualizar usuarios u otros objetos durante la suplantación de la aplicación, según los permisos de la aplicación. Los propietarios de aplicaciones tienen los mismos permisos que los administradores de aplicaciones con ámbito en una aplicación individual. Para más información, consulte [Roles integrados en Azure](../roles/permissions-reference.md#application-administrator). 

## <a name="assign-an-owner"></a>Asignación de un propietario

Para asignar un propietario a una aplicación empresarial:

1. Inicie sesión en su [organización de Azure AD](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) con una cuenta que sea válida para el rol de **administrador de aplicaciones** o el rol de **administrador de aplicaciones en la nube** de la organización.
2. Seleccione **Aplicaciones empresariales** y, después, seleccione la aplicación a la que quiere agregar un propietario.
3. Seleccione **Propietarios** y, a continuación, seleccione **Agregar** para obtener una lista de cuentas de usuario entre las que puede elegir un propietario.
4. Busque y seleccione la cuenta de usuario que desea que sea propietaria de la aplicación.
5. Haga clic en **Seleccionar** para agregar la cuenta de usuario que desea que sea propietaria de la aplicación.

> [!NOTE]
> Si la opción de configuración del usuario **Restringir el acceso al portal de administración de Azure AD** está establecida en `Yes`, los usuarios que no sean administradores no podrán usar Azure Portal para administrar las aplicaciones de su propiedad. Para obtener más información sobre las acciones que se pueden realizar en aplicaciones empresariales que se poseen, vea [Aplicaciones empresariales que se poseen](../fundamentals/users-default-permissions.md#owned-enterprise-applications). 

## <a name="next-steps"></a>Pasos siguientes

- [Delegación de permisos de registro de aplicaciones en Azure Active Directory](../roles/delegate-app-roles.md)
