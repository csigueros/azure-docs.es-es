---
title: 'Acceso delegado en Azure Virtual Desktop (clásico): Azure'
description: Se describe cómo delegar funcionalidades administrativas en una implementación de Azure Virtual Desktop (clásico) y se incluyen ejemplos.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9db8ad454ad38f24f32e05bf2f72d67ef7db1971
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111752052"
---
# <a name="delegated-access-in-azure-virtual-desktop-classic"></a>Acceso delegado en Azure Virtual Desktop (clásico)

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager. Si está intentando administrar objetos de Azure Virtual Desktop para Azure Resource Manager, consulte [este artículo](../delegated-access-virtual-desktop.md).

Azure Virtual Desktop presenta un modelo de acceso delegado que permite definir la cantidad de acceso que puede tener un usuario determinado mediante la asignación de un rol. Una asignación de roles consta de tres componentes: entidad de seguridad, definición de rol y ámbito. El modelo de acceso delegado de Azure Virtual Desktop se basa en el modelo de Azure RBAC. Para más información sobre asignaciones de roles específicas y sus componentes, consulte la [información general sobre el control de acceso basado en roles de Azure](../../role-based-access-control/built-in-roles.md).

El acceso delegado de Azure Virtual Desktop admite los siguientes valores para cada elemento de la asignación de roles:

* Entidad de seguridad
    * Usuarios
    * Entidades de servicio
* Definición de roles
    * Roles integrados
* Ámbito
    * Grupos de inquilinos
    * Inquilinos
    * Grupos de host
    * Grupos de aplicaciones

## <a name="built-in-roles"></a>Roles integrados

El acceso delegado en Azure Virtual Desktop tiene varias definiciones de roles integradas que se pueden asignar a los usuarios y a las entidades de servicio.

* Un propietario de RDS puede administrarlo todo, incluido el acceso a los recursos.
* Un colaborador de RDS puede administrar todo, pero no puede acceder a los recursos.
* Un lector de RDS puede verlo todo, pero no puede realizar cambios.
* Un operador de RDS puede ver las actividades de diagnóstico.

## <a name="powershell-cmdlets-for-role-assignments"></a>Cmdlets de PowerShell para asignaciones de roles

Puede ejecutar los siguientes cmdlets para crear, ver y eliminar asignaciones de roles:

* **Get-RdsRoleAssignment** muestra una lista de asignaciones de roles.
* **New-RdsRoleAssignment** crea una asignación de roles.
* **Remove-RdsRoleAssignment** elimina las asignaciones de roles.

### <a name="accepted-parameters"></a>Parámetros aceptados

Puede modificar los tres cmdlets básicos con los siguientes parámetros:

* **AadTenantId**: especifica el identificador del inquilino de Azure Active Directory del que la entidad de servicio es miembro.
* **AppGroupName**: nombre del grupo de aplicaciones de Escritorio remoto.
* **Diagnostics**: indica el ámbito de diagnóstico. (Debe estar emparejado con los parámetros **Infrastructure** o **Tenant**).
* **HostPoolName**: nombre del grupo de host de Escritorio remoto.
* **Infrastructure**: indica el ámbito de la infraestructura.
* **RoleDefinitionName**: nombre del rol de control de acceso basado en roles de los Servicios de Escritorio remoto asignado al usuario, el grupo o la aplicación. (Por ejemplo, propietario de los Servicios de Escritorio remoto, lector de los Servicios de Escritorio remoto, etc.)
* **ServerPrincipleName**: nombre de la aplicación de Azure Active Directory.
* **SignInName**: dirección de correo electrónico del usuario o nombre principal de usuario.
* **TenantName**: nombre del inquilino de Escritorio remoto.

## <a name="next-steps"></a>Pasos siguientes

Para una lista más completa de cmdlets de PowerShell que puede usar cada rol, consulte la [referencia de PowerShell](/powershell/windows-virtual-desktop/overview).

Para tener una guía para configurar un entorno de Azure Virtual Desktop, consulte [Entorno de Azure Virtual Desktop](environment-setup-2019.md).
