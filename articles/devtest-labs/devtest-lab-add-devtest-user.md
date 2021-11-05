---
title: Adición de usuarios y propietarios
description: Agregar propietarios y usuarios en Azure DevTest Labs mediante Azure Portal o PowerShell
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6edf548658d02ae3427fe5ac448dcc3e38a6b4e2
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131068767"
---
# <a name="add-owners-and-users-in-azure-devtest-labs"></a>Adición de propietarios y usuarios en Azure DevTest Labs
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/How-to-set-security-in-your-DevTest-Lab/player]
> 
> 

El acceso a Azure DevTest Labs se controla mediante el [control de acceso basado en rol de Azure (RBAC de Azure)](../role-based-access-control/overview.md). Mediante Azure RBAC, puede repartir las tareas del equipo en *roles* y conceder a los usuarios únicamente el nivel de acceso que necesitan para realizar su trabajo. Tres de estos roles de Azure son *Propietario*, *Usuario de DevTest Labs* y *Colaborador*. En este artículo, aprenderá qué acciones se pueden realizar en cada uno de los tres roles principales de Azure. A partir de ahí, aprenderá cómo agregar usuarios a un laboratorio a través del portal o a través de un script de PowerShell y cómo agregar los usuarios en el nivel de suscripción.

## <a name="actions-that-can-be-performed-in-each-role"></a>Acciones que se pueden realizar en cada rol
Hay tres roles principales que puede asignar a un usuario:

* Propietario
* Usuario de DevTest Labs
* Colaborador

En la tabla siguiente se muestran las acciones que pueden realizar los usuarios pertenecientes a cada uno de estos roles:

| **Acciones que pueden realizar los usuarios de este rol** | **Usuario de DevTest Labs** | **Propietario** | **Colaborador** |
| --- | --- | --- | --- |
| **Tareas de laboratorio** | | | |
| Agregar usuarios a un laboratorio |No |Sí |No |
| Actualizar la configuración de costo |No |Sí |Sí |
| **Tareas base de máquina virtual** | | | |
| Agregar y quitar imágenes personalizadas |No |Sí |Sí |
| Agregar, actualizar y eliminar las fórmulas |Sí |Sí |Sí |
| Habilitar imágenes de Marketplace |No |Sí |Sí |
| **Tareas de la máquina virtual** | | | |
| Creación de máquinas virtuales |Sí |Sí |Sí |
| Iniciar, detener y eliminar máquinas virtuales |Solo las máquinas virtuales creadas por el usuario |Sí |Sí |
| Actualizar directivas de máquinas virtuales |No |Sí |Sí |
| Agregar discos de datos o quitarlos en máquinas virtuales |Solo las máquinas virtuales creadas por el usuario |Sí |Sí |
| **Tareas de artefacto** | | | |
| Agregar y quitar repositorios de artefacto |No |Sí |Sí |
| Aplicar artefactos |Sí |Sí |Sí |

> [!NOTE]
> Cuando un usuario crea una máquina virtual, a ese usuario se le asigna automáticamente el rol **Propietario** de la máquina virtual creada.
> 
> 

## <a name="add-an-owner-or-user-at-the-lab-level"></a>Agregar un propietario o usuario en el nivel de laboratorio
Los propietarios y los usuarios se pueden agregar en el nivel de laboratorio a través de Azure Portal. Un usuario puede ser un usuario externo con una [cuenta Microsoft (MSA)](./devtest-lab-faq.yml)válida.
Los siguientes pasos le guiarán a través del proceso de agregación de un propietario o usuario a un laboratorio de Azure DevTest Labs:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como [Administrador de acceso de usuario](../role-based-access-control/built-in-roles.md#user-access-administrator) o [Propietario](../role-based-access-control/built-in-roles.md#owner).

1. Abra el grupo de recursos deseado y seleccione **DevTest Labs**.

1. En el menú de navegación, seleccione **Control de acceso (IAM)** .

1. Seleccione **Agregar** > **Agregar asignación de roles**.

    ![Página Control de acceso (IAM) con el menú Agregar asignación de roles abierto.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. En la pestaña **Rol**, seleccione el rol **PROPIETARIO** o **USUARIO**.

    ![Página Agregar asignación de roles con la pestaña Rol seleccionada.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. En la pestaña **Miembros**, seleccione el usuario al que quiere asignar el rol deseado.

1. En la pestaña **Revisión y asignación**, seleccione **Revisión y asignación** para asignar el rol.


## <a name="add-an-external-user-to-a-lab-using-powershell"></a>Incorporación de un usuario externo a un laboratorio mediante PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Además de agregar usuarios en Azure Portal, puede agregar un usuario externo al laboratorio mediante un script de PowerShell. En el ejemplo siguiente, basta con modificar los valores de parámetro en el comentario **Values to change** (Valores para cambiar).
Puede recuperar los valores `subscriptionId`, `labResourceGroup` y `labName` de la hoja de laboratorio en Azure Portal.

> [!NOTE]
> El script de ejemplo supone que el usuario especificado se ha agregado como invitado a Active Directory y se producirá un error si este no es el caso. Para agregar un usuario que no está en Active Directory a un laboratorio, use Azure Portal para asignar el usuario a un rol, tal como se muestra en la sección [Agregar un propietario o usuario en el nivel de laboratorio](#add-an-owner-or-user-at-the-lab-level).   
> 
> 

```azurepowershell
# Add an external user in DevTest Labs user role to a lab
# Ensure that guest users can be added to the Azure Active directory:
# https://azure.microsoft.com/documentation/articles/active-directory-create-users/#set-guest-user-access-policies

# Values to change
$subscriptionId = "<Enter Azure subscription ID here>"
$labResourceGroup = "<Enter lab's resource name here>"
$labName = "<Enter lab name here>"
$userDisplayName = "<Enter user's display name here>"

# Log into your Azure account
Connect-AzAccount

# Select the Azure subscription that contains the lab. 
# This step is optional if you have only one subscription.
Select-AzSubscription -SubscriptionId $subscriptionId

# Retrieve the user object
$adObject = Get-AzADUser -SearchString $userDisplayName

# Create the role assignment. 
$labId = ('subscriptions/' + $subscriptionId + '/resourceGroups/' + $labResourceGroup + '/providers/Microsoft.DevTestLab/labs/' + $labName)
New-AzRoleAssignment -ObjectId $adObject.Id -RoleDefinitionName 'DevTest Labs User' -Scope $labId
```

## <a name="add-an-owner-or-user-at-the-subscription-level"></a>Agregar un propietario o usuario en el nivel de suscripción
Los permisos de Azure se propagan desde el ámbito primario al secundario en Azure. Por lo tanto, los propietarios de una suscripción de Azure que contiene laboratorios, automáticamente son propietarios de estos. También serán propietarios de las máquinas virtuales y de los demás recursos creados por los usuarios del laboratorio y el servicio Azure DevTest Labs. 

Puede agregar propietarios adicionales a un laboratorio a través de la hoja del laboratorio en [Azure Portal](https://go.microsoft.com/fwlink/p/?LinkID=525040). Sin embargo, el ámbito de administración del propietario agregado será más limitado que el ámbito del propietario de la suscripción. Por ejemplo, los propietarios agregados no tienen acceso completo a algunos de los recursos que el servicio DevTest Labs crea en la suscripción. 

Para agregar un propietario a una suscripción de Azure, siga estos pasos:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) como [Administrador de acceso de usuario](../role-based-access-control/built-in-roles.md#user-access-administrator) o [Propietario](../role-based-access-control/built-in-roles.md#owner).

1. Abra el grupo de suscripciones deseado.

1. En el menú de navegación, seleccione **Control de acceso (IAM)** .

1. Seleccione **Agregar** > **Agregar asignación de roles**.

    ![Página Control de acceso (IAM) con el menú Agregar asignación de roles abierto.](../../includes/role-based-access-control/media/add-role-assignment-menu-generic.png)

1. En la pestaña **Rol**, seleccione el rol **PROPIETARIO**.

    ![Página Agregar asignación de roles con la pestaña Rol seleccionada.](../../includes/role-based-access-control/media/add-role-assignment-role-generic.png)

1. En la pestaña **Miembros**, seleccione el usuario al que quiere asignar el rol de propietario.

1. En la pestaña **Revisión y asignación**, seleccione **Revisión y asignación** para asignar el rol.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]
