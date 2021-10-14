---
title: Permisos para ver y administrar reservas de Azure
description: Aprenda a ver y a administrar las reservas de Azure en Azure Portal.
author: bandersmsft
ms.reviewer: primittal
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: how-to
ms.date: 10/05/2021
ms.author: banders
ms.openlocfilehash: 797aff6fed0cf2eda46bcf5371e57e18df466f00
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546969"
---
# <a name="permissions-to-view-and-manage-azure-reservations"></a>Permisos para ver y administrar reservas de Azure

En este artículo se explica cómo funcionan los permisos de reserva y cómo los usuarios pueden ver y administrar las reservas de Azure en Azure Portal y con Azure PowerShell.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="who-can-manage-a-reservation-by-default"></a>Quiénes pueden administrar una reservas de manera predeterminada

De forma predeterminada, estos son los usuarios que pueden ver y administrar reservas:

- Tanto la persona que compra una reserva como el administrador de cuenta de la suscripción de facturación que se ha usado para comprar la reserva se agregan al pedido de reserva.
- Administradores de facturación del Contrato Enterprise y del Contrato de cliente de Microsoft.
- Usuarios con privilegios de acceso elevados para administrar todas las suscripciones y los grupos de administración de Azure.
- Un administrador de reservas, en el caso de las reservas de su inquilino (directorio) de Azure Active Directory (Azure AD)
- Un lector de reservas tiene acceso de solo lectura a las reservas de su inquilino (directorio) de Azure Active Directory.

Actualmente, los roles de administrador y lector de reservas solo están disponibles para asignarse mediante PowerShell. No se pueden ver ni asignar en Azure Portal. Para más información, consulte [Concesión de acceso con PowerShell](#grant-access-with-powershell).

El ciclo de vida de la reserva no depende de una suscripción de Azure, por lo que la reserva no es un recurso de la suscripción de Azure. En su lugar, es un recurso de nivel de inquilino con su propio permiso de RBAC de Azure independiente de las suscripciones. Las reservas no heredan los permisos de las suscripciones después de la compra.

## <a name="view-and-manage-reservations"></a>Vista y administración de reservas

Si es un administrador de facturación, siga estos pasos para ver y administrar todas las reservas y las transacciones de reserva en Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a **Cost Management + Billing**.
    - Los administradores de EA pueden seleccionar **Ámbitos de facturación** en el menú de la izquierda y, después, seleccionar uno de los ámbitos de la lista.
    - Los propietarios de un perfil de facturación de Contrato de cliente de Microsoft, deben seleccionar **Perfiles de facturación** en el menú de la izquierda. En la lista de perfiles de facturación, seleccione uno.
1. En el menú de la izquierda, seleccione **Productos y servicios** > **Reservas**.
1. Se muestra la lista completa de reservas de su perfil de facturación o inscripción de EA.
1. Los administradores de facturación pueden tomar posesión de una reserva mediante la selección de una o varias reservas, haciendo clic en **Conceder acceso** y seleccionando **Conceder acceso** en la ventana que aparece.

### <a name="add-billing-administrators"></a>Incorporación de administradores de facturación

Agregue un usuario como administrador de facturación a un Contrato Enterprise o un Contrato de cliente de Microsoft en Azure Portal.

- En el caso de un Contrato Enterprise, agregue usuarios con el rol de _Administrador de empresa_ para ver y administrar todos los pedidos de reserva que se aplican al Contrato Enterprise. Los administradores de empresa pueden ver y administrar las reservas en **Administración de costos + facturación**.
    - Los usuarios con el rol de _Administrador de empresa (solo lectura)_ solo pueden ver la reserva en **Administración de costos + facturación**. 
    - Ni los administradores de departamento ni los propietarios de cuentas no pueden ver reservas, _salvo que_ se les agreguen mediante el Control de acceso (IAM). Para más información, consulte [Administración de roles de Azure Enterprise](../manage/understand-ea-roles.md).
- En el caso de un Contrato de cliente de Microsoft, los usuarios con el rol Propietario de perfil de facturación o el rol Colaborador de perfil de facturación pueden administrar todas las compras de reserva realizadas mediante el perfil de facturación. Los lectores del perfil de facturación y los administradores de facturas pueden ver todas las reservas que se pagan con el perfil de facturación. Sin embargo, no pueden realizar cambios en las reservas.
    Para más información, consulte [Tareas y roles del perfil de facturación](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="view-reservations-with-azure-rbac-access"></a>Visualización de reservas con acceso de RBAC de Azure

Si ha adquirido la reserva o se le ha agregado a una, siga estos pasos para ver y administrar las reservas en Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione **Todos los servicios** > **Reservas** para obtener una lista de las reservas a las que tiene acceso.

## <a name="manage-subscriptions-and-management-groups-with-elevated-access"></a>Administración de suscripciones y grupos de administración con acceso elevado

Puede elevar el privilegio de [acceso para administrar todas las suscripciones y los grupos de administración de Azure](../../role-based-access-control/elevate-access-global-admin.md?toc=/azure/cost-management-billing/reservations/toc.json).

Con acceso con privilegios elevados:

1. Vaya a **Todos los servicios** > **Reserva** para ver todas las reservas del inquilino.
1. Para realizar modificaciones en la reserva, agréguese como propietario del pedido de reserva mediante el control de acceso (IAM).

## <a name="grant-access-to-individual-reservations"></a>Concesión de acceso a reservas individuales

Los usuarios con acceso de propietario en las reservas y los administradores de facturación pueden delegar la administración de acceso con relación a un pedido de reserva individual en Azure Portal.

Para que otras personas puedan administrar las reservas, tiene dos opciones:

- Delegue la administración del acceso para un pedido de reserva individual mediante la asignación del rol Propietario a un usuario en el ámbito de recursos del pedido de reserva. Si desea otorgar un acceso limitado, seleccione otro rol.  
     Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).

- Agregue un usuario como administrador de facturación a un Contrato Enterprise o un Contrato de cliente de Microsoft:
    - En el caso de un Contrato Enterprise, agregue usuarios con el rol de _Administrador de empresa_ para ver y administrar todos los pedidos de reserva que se aplican al Contrato Enterprise. Los usuarios con el rol de _Administrador de empresa (solo lectura)_ solo pueden ver la reserva. Ni los administradores de departamento ni los propietarios de cuentas no pueden ver reservas, _salvo que_ se les agreguen mediante el Control de acceso (IAM). Para más información, consulte [Administración de roles de Azure Enterprise](../manage/understand-ea-roles.md).

        _Los administradores de empresa pueden tomar posesión de un pedido de reserva y pueden agregar otros usuarios a una reserva mediante el Control de acceso (IAM)._
    - En el caso de un Contrato de cliente de Microsoft, los usuarios con el rol Propietario de perfil de facturación o el rol Colaborador de perfil de facturación pueden administrar todas las compras de reserva realizadas mediante el perfil de facturación. Los lectores del perfil de facturación y los administradores de facturas pueden ver todas las reservas que se pagan con el perfil de facturación. Sin embargo, no pueden realizar cambios en las reservas.
    Para más información, consulte [Tareas y roles del perfil de facturación](../manage/understand-mca-roles.md#billing-profile-roles-and-tasks).

## <a name="grant-access-with-powershell"></a>Concesión de acceso con PowerShell

Los usuarios con acceso de propietario para los pedidos de reservas, los usuarios con acceso elevado y los [administradores de acceso de usuario](../../role-based-access-control/built-in-roles.md#user-access-administrator) pueden delegar la administración de acceso con relación a todos los pedidos de reserva a los que tienen acceso.

## <a name="assign-the-owner-role-for-all-reservations"></a>Asignación del rol "Propietario" para todas las reservas

Use el siguiente script de Azure PowerShell para proporcionar a un usuario acceso de Azure RBAC a todos los pedidos de reservas en su inquilino (directorio) de Azure AD.

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources
 
Connect-AzAccount -Tenant <TenantId>
 
$response = Invoke-AzRestMethod -Path /providers/Microsoft.Capacity/reservations?api-version=2020-06-01 -Method GET
 
$responseJSON = $response.Content | ConvertFrom-JSON
 
$reservationObjects = $responseJSON.value
 
foreach ($reservation in $reservationObjects)
{
  $reservationOrderId = $reservation.id.substring(0, 84)
  Write-Host "Assiging Owner role assignment to "$reservationOrderId
  New-AzRoleAssignment -Scope $reservationOrderId -ObjectId <ObjectId> -RoleDefinitionName Owner
}
```

### <a name="parameters"></a>Parámetros

**-ObjectId:** Id. de objeto de Azure AD del usuario, del grupo o de la entidad de servicio.
- Escriba:  String
- Alias: Id o PrincipalId
- Posición: con nombre
- Valor predeterminado: ninguno
- Aceptar la entrada de la canalización: true
- Aceptar caracteres comodín: false

**-TenantId:** identificador único de inquilino.
- Escriba:  String
- Posición: 5
- Valor predeterminado: ninguno
- Aceptar la entrada de la canalización: false
- Aceptar caracteres comodín: false

Los [administradores de acceso de usuario](../../role-based-access-control/built-in-roles.md#user-access-administrator) pueden agregar los usuarios a los roles "Administrador de reservas" y "Lector de reservas".

## <a name="add-a-reservation-administrator-role-at-the-tenant-level"></a>Incorporación de un rol "Administrador de reservas" en el nivel de inquilino

Use el siguiente script de Azure PowerShell para agregar un rol "Administrador de reservas" en el nivel de inquilino con PowerShell.

```azurepowershell
Import-Module Az.Accounts
Import-Module Az.Resources
Connect-AzAccount -Tenant <TenantId>
New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Administrator"
```

### <a name="parameters"></a>Parámetros

**-ObjectId:** Id. de objeto de Azure AD del usuario, del grupo o de la entidad de servicio.
- Escriba:  String
- Alias: Id o PrincipalId
- Posición: con nombre
- Valor predeterminado: ninguno
- Aceptar la entrada de la canalización: true
- Aceptar caracteres comodín: false

**-TenantId:** identificador único de inquilino.
- Escriba:  String
- Posición: 5
- Valor predeterminado: ninguno
- Aceptar la entrada de la canalización: false
- Aceptar caracteres comodín: false

## <a name="assign-a-reservation-reader-role-at-the-tenant-level"></a>Asignación de un rol "Lector de reservas" en el nivel de inquilino

Use el siguiente script de Azure PowerShell para asignar un rol "Lector de reservas" en el nivel de inquilino con PowerShell.

```azurepowershell

Import-Module Az.Accounts
Import-Module Az.Resources

Connect-AzAccount -Tenant <TenantId>

New-AzRoleAssignment -Scope "/providers/Microsoft.Capacity" -PrincipalId <ObjectId> -RoleDefinitionName "Reservations Reader"
```

### <a name="parameters"></a>Parámetros

**-ObjectId:** Id. de objeto de Azure AD del usuario, del grupo o de la entidad de servicio.
- Escriba:  String
- Alias: Id o PrincipalId
- Posición: con nombre
- Valor predeterminado: ninguno
- Aceptar la entrada de la canalización: true
- Aceptar caracteres comodín: false

**-TenantId:** identificador único de inquilino.
- Escriba:  String
- Posición: 5
- Valor predeterminado: ninguno
- Aceptar la entrada de la canalización: false
- Aceptar caracteres comodín: false


## <a name="next-steps"></a>Pasos siguientes

- [Administración de reservas de Azure](manage-reserved-vm-instance.md)
