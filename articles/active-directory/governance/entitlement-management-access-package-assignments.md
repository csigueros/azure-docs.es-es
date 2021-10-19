---
title: 'Consulta, incorporación y eliminación de asignaciones para un paquete de acceso en la administración de derechos de Azure AD: Azure Active Directory'
description: Aprenda a consultar, agregar y eliminar asignaciones de un paquete de acceso en la administración de derechos de Azure Active Directory.
services: active-directory
documentationCenter: ''
author: ajburnle
manager: daveba
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.subservice: compliance
ms.date: 10/05/2021
ms.author: ajburnle
ms.reviewer: ''
ms.collection: M365-identity-device-management
ms.openlocfilehash: f944caecae6d35e680f5c5beb1a6e23fc422e698
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129618104"
---
# <a name="view-add-and-remove-assignments-for-an-access-package-in-azure-ad-entitlement-management"></a>Consulta, incorporación y eliminación de asignaciones para un paquete de acceso en la administración de derechos de Azure AD

En la administración de derechos de Azure AD, puede ver a quién se le han asignado los paquetes de acceso, su directiva y su estado. Si un paquete de acceso tiene una directiva adecuada, también puede asignar directamente al usuario un paquete de acceso. En este artículo se explica cómo consultar, agregar y eliminar asignaciones en paquetes de acceso.

## <a name="prerequisites"></a>Prerrequisitos

Para usar la administración de derechos de Azure AD y asignar usuario a los paquetes de acceso, debe tener una de las licencias siguientes:

- Azure AD Premium P2
- Licencia de Enterprise Mobility + Security (EMS) E5

## <a name="view-who-has-an-assignment"></a>Ver quién tiene una asignación

**Requisitos previos de rol:** Administrador global, Administrador de Identity Governance, Administrador de usuarios, Propietario del catálogo, Administrador de paquetes de acceso o Administrador de asignaciones de paquetes de acceso.

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso.

1. Haga clic en **Asignaciones** para ver una lista de las asignaciones activas.

    ![Lista de asignaciones a un paquete de acceso](./media/entitlement-management-access-package-assignments/assignments-list.png)

1. Haga clic en una asignación específica para ver detalles adicionales.

1. Para ver una lista de asignaciones que no tenían aprovisionados correctamente todos los roles de recursos, haga clic en el filtro de estado y seleccione **Entrega**.

    Puede ver detalles adicionales sobre los errores de entrega localizando la solicitud del usuario correspondiente en la página **Solicitudes**.

1. Para ver las asignaciones expiradas, haga clic en el filtro de estado y seleccione **Expiradas**.

1. Para descargar un archivo CSV de la lista filtrada, haga clic en **Descargar**.

## <a name="view-assignments-programmatically"></a>Visualización de asignaciones mediante programación
### <a name="view-assignments-with-microsoft-graph"></a>Visualización de asignaciones con Microsoft Graph
También puede recuperar las asignaciones de un paquete de acceso mediante Microsoft Graph.  Un usuario de un rol adecuado con una aplicación con el permiso delegado `EntitlementManagement.Read.All` o `EntitlementManagement.ReadWrite.All` puede llamar a la API para [enumerar elementos accessPackageAssignments](/graph/api/accesspackageassignment-list?view=graph-rest-beta&preserve-view=true). Aunque un administrador de gobernanza de identidades puede recuperar paquetes de acceso de varios catálogos, si el usuario solo se asigna a roles administrativos delegados específicos del catálogo, la solicitud debe proporcionar un filtro para indicar un paquete de acceso específico, como `$filter=accessPackage/id eq 'a914b616-e04e-476b-aa37-91038f0b165b'`. Una aplicación que tenga el permiso de aplicación `EntitlementManagement.Read.All` o `EntitlementManagement.ReadWrite.All` también puede usar esta API.

### <a name="view-assignments-with-powershell"></a>Visualización de asignaciones con PowerShell

Puede realizar esta consulta en PowerShell con el cmdlet `Get-MgEntitlementManagementAccessPackageAssignment` de los [cmdlets de Microsoft Graph PowerShell para el módulo Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) versión 1.6.0 o posterior. Este cmdlet toma como parámetro el id. del paquete de acceso, que se incluye en la respuesta del cmdlet `Get-MgEntitlementManagementAccessPackage`.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.Read.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -ExpandProperty target -All -ErrorAction Stop
$assignments | ft Id,AssignmentState,TargetId,{$_.Target.DisplayName}
```

## <a name="directly-assign-a-user"></a>Asignar directamente un usuario 

En algunos casos, es posible que quiera asignar directamente usuarios específicos a un paquete de acceso para que los usuarios no tengan que pasar por el proceso de solicitar el paquete de acceso. Para asignar directamente usuarios, el paquete de acceso debe tener una directiva que permita las asignaciones directas de administrador.

**Rol necesario:** Administrador global, Administrador de usuarios, Propietario del catálogo, Administrador de paquetes de acceso o Administrador de asignaciones de paquetes de acceso

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso.

1. En el menú de la izquierda, haga clic en **Asignaciones**.

1. Haga clic en **Nueva asignación** para abrir Add user to access package (Agregar usuario al paquete de acceso).

    ![Asignaciones - Add user to access package (Agregar usuario al paquete de acceso)](./media/entitlement-management-access-package-assignments/assignments-add-user.png)

1.  En la lista **Seleccionar directiva**, seleccione una directiva por la que se regirán y se realizará un seguimiento de las solicitudes y el ciclo de vida futuros de los usuarios. Si desea que los usuarios seleccionados tengan una configuración de la directiva diferente, puede hacer clic en **Crear nueva directiva** para agregar una nueva directiva.

1.  Una vez que seleccione una directiva, podrá agregar usuarios para seleccionar aquellos a los que quiere asignar este paquete de acceso, en la directiva elegida.

    > [!NOTE]
    > Si selecciona una directiva con preguntas, solo puede asignar un usuario a la vez.

1. Establezca la fecha y hora en la que quiere que comience y finalice la asignación de los usuarios seleccionados. Si no se proporciona una fecha de finalización, se utilizará la configuración del ciclo de vida de la directiva.

1.  Opcionalmente, proporcione una justificación para la asignación directa para mantener un registro.

1.  Si la directiva seleccionada incluye información adicional del solicitante, haga clic en **Ver preguntas** para responderlas en nombre de los usuarios y, a continuación, haga clic en **Guardar**.  

     ![Asignaciones: hacer clic en ver preguntas](./media/entitlement-management-access-package-assignments/assignments-view-questions.png)

    ![Asignaciones: panel de preguntas](./media/entitlement-management-access-package-assignments/assignments-questions-pane.png)

1. Haga clic en **Agregar** para asignar directamente los usuarios seleccionados al paquete acceso.

    Transcurridos unos instantes, haga clic en **Actualizar** para ver los usuarios en la lista Asignaciones.
    
> [!NOTE]
> Al asignar usuarios a un paquete de acceso, los administradores tendrán que comprobar que los usuarios cumplen los requisitos de la directiva existente para ese paquete de acceso. De lo contrario, los usuarios no se podrán asignar correctamente al paquete de acceso. Si el paquete de acceso contiene una directiva que requiere que la aprobación de las solicitudes de usuario, no se podrán asignar usuarios directamente al paquete sin las aprobaciones necesarias de los aprobadores designados.

## <a name="directly-assign-any-user-preview"></a>Asignación directa de cualquier usuario (versión preliminar)
La administración de derechos de Azure AD también permite asignar directamente usuarios externos a un paquete de acceso a fin de facilitar la colaboración con los asociados. Para ello, el paquete de acceso debe tener una directiva que permita a los usuarios que aún no están en el directorio solicitar acceso.

**Rol necesario:** Administrador global, Administrador de usuarios, Propietario del catálogo, Administrador de paquetes de acceso o Administrador de asignaciones de paquetes de acceso

1.  En Azure Portal, seleccione **Azure Active Directory** y, luego, **Gobierno de identidades**.

1.  En el menú de la izquierda, haga clic en **Paquetes de acceso** y abra el paquete de acceso en el que desea agregar un usuario.

1.  En el menú de la izquierda, haga clic en **Asignaciones**.

1.  Seleccione **Nueva asignación** para abrir **Agregar usuario al paquete de acceso**.

1.  En la lista **Seleccionar directiva**, seleccione una directiva que permita que se establezca en **Para los usuarios que no están en el directorio**.

1. Seleccione **Cualquier usuario**. Podrá especificar qué usuarios desea asignar a este paquete de acceso.
    ![Asignaciones - Agregar cualquier usuario al paquete de acceso](./media/entitlement-management-access-package-assignments/assignments-add-any-user.png)

1. Escriba el **nombre** (opcional) y la **dirección de correo electrónico** (obligatoria) del usuario.

    > [!NOTE]
    > - El usuario que desea agregar debe estar dentro del ámbito de la directiva. Por ejemplo, si la directiva está establecida en **Organizaciones conectadas específicas**, la dirección de correo electrónico del usuario debe ser de los dominios de las organizaciones seleccionadas. Si la dirección de correo electrónico del usuario que intenta agrega es jen@*foo.com*, pero el dominio de la organización seleccionada es *bar.com*, no podrá agregar ese usuario al paquete de acceso.
    > - Del mismo modo, si estableció la directiva para incluir **Todas las organizaciones conectadas que están configuradas**, la dirección de correo electrónico del usuario debe provenir de una de las organizaciones conectadas configuradas. De lo contrario, el usuario no se agregará al paquete de acceso.
    > - Si desea agregar cualquier usuario al paquete de acceso, deberá asegurarse de activar la opción **Todos los usuarios (todas las organizaciones conectadas + cualquier usuario externo)** al configurar la directiva.

1.  Establezca la fecha y hora en la que quiere que comience y finalice la asignación de los usuarios seleccionados. Si no se proporciona una fecha de finalización, se utilizará la configuración del ciclo de vida de la directiva.
1.  Haga clic en **Agregar** para asignar directamente los usuarios seleccionados al paquete acceso.
1.  Transcurridos unos instantes, haga clic en **Actualizar** para ver los usuarios en la lista Asignaciones.

## <a name="directly-assigning-users-programmatically"></a>Asignación de usuarios directamente mediante programación
### <a name="assign-a-user-to-an-access-package-with-microsoft-graph"></a>Asignación de un usuario a un paquete de acceso con Microsoft Graph
También puede asignar un usuario directamente a un paquete de acceso mediante Microsoft Graph.  Un usuario de un rol adecuado con una aplicación con el permiso `EntitlementManagement.ReadWrite.All`, o una aplicación con ese permiso de aplicación, puede llamar a la API para [crear un elemento accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true). En esta solicitud, el valor de la propiedad `requestType` debe ser `AdminAdd`, y la propiedad `accessPackageAssignment` es una estructura que contiene el `targetId` del usuario que se está asignando.

### <a name="assign-a-user-to-an-access-package-with-powershell"></a>Asignación de un usuario a un paquete de acceso con PowerShell

Puede asignar un usuario a un paquete de acceso en PowerShell con el cmdlet `New-MgEntitlementManagementAccessPackageAssignmentRequest` de los [cmdlets de Microsoft Graph PowerShell para el módulo Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) versión 1.6.0 o posterior. Este cmdlet toma como parámetros
* el id. del paquete de acceso, que se incluye en la respuesta del cmdlet `Get-MgEntitlementManagementAccessPackage`,
* el id. de la directiva de asignación del paquete de acceso, que se incluye en la respuesta del cmdlet `Get-MgEntitlementManagementAccessPackageAssignmentPolicy`,
* el id. de objeto del usuario de destino.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -TargetId "a43ee6df-3cc5-491a-ad9d-ea964ef8e464"
```

Puede también asignar múltiples usuarios a un paquete de acceso en PowerShell con el cmdlet `New-MgEntitlementManagementAccessPackageAssignment` de los [cmdlets de Microsoft Graph PowerShell para el módulo Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) versión 1.6.1 o posterior. Este cmdlet toma como parámetros
* el id. del paquete de acceso, que se incluye en la respuesta del cmdlet `Get-MgEntitlementManagementAccessPackage`,
* el id. de la directiva de asignación del paquete de acceso, que se incluye en la respuesta del cmdlet `Get-MgEntitlementManagementAccessPackageAssignmentPolicy`,
* los id. de objeto de los usuarios de destino, ya sea como una matriz de cadenas, o como una lista de miembros de usuario devueltos por el cmdlet `Get-MgGroupMember`.

Por ejemplo, si desea asegurarse de que todos los usuarios que actualmente son miembros de un grupo también tienen asignaciones a un paquete de acceso, puede usar este cmdlet para crear solicitudes para los usuarios que no tienen asignaciones actualmente.  Tenga en cuenta que este cmdlet no quita las asignaciones, solamente las crea.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All,Directory.Read.All"
Select-MgProfile -Name "beta"
$members = Get-MgGroupMember -GroupId "a34abd69-6bf8-4abd-ab6b-78218b77dc15"
$accesspackage = Get-MgEntitlementManagementAccessPackage -DisplayNameEq "Marketing Campaign" -ExpandProperty "accessPackageAssignmentPolicies"
$policy = $accesspackage.AccessPackageAssignmentPolicies[0]
$req = New-MgEntitlementManagementAccessPackageAssignment -AccessPackageId $accesspackage.Id -AssignmentPolicyId $policy.Id -RequiredGroupMember $members
```

## <a name="remove-an-assignment"></a>Eliminación de una asignación

**Rol necesario:** Administrador global, Administrador de usuarios, Propietario del catálogo, Administrador de paquetes de acceso o Administrador de asignaciones de paquetes de acceso

1. En Azure Portal, haga clic en **Azure Active Directory** y, luego, en **Gobernanza de identidades**.

1. En el menú de la izquierda, haga clic en **Paquetes de acceso** y luego abra el paquete de acceso.

1. En el menú de la izquierda, haga clic en **Asignaciones**.
 
1. Haga clic en la casilla que hay al lado del usuario cuya asignación desea quitar del paquete de acceso. 

1. Haga clic en el botón **Quitar** situado cerca de la parte superior del panel izquierdo. 
 
    ![Asignaciones: quitar usuario del paquete de acceso](./media/entitlement-management-access-package-assignments/remove-assignment-select-remove-assignment.png)

    Aparecerá una notificación que le informará de que se ha quitado la asignación. 

## <a name="remove-an-assignment-programmatically"></a>Quitar una asignación mediante programación
### <a name="remove-an-assignment-with-microsoft-graph"></a>Quitar una asignación con Microsoft Graph
También puede quitar una asignación de un usuario a un paquete de acceso mediante Microsoft Graph.  Un usuario de un rol adecuado con una aplicación con el permiso `EntitlementManagement.ReadWrite.All`, o una aplicación con ese permiso de aplicación, puede llamar a la API para [crear un elemento accessPackageAssignmentRequest](/graph/api/accesspackageassignmentrequest-post?view=graph-rest-beta&preserve-view=true).  En esta solicitud, el valor de la propiedad `requestType` debe ser `AdminRemove`, y la propiedad `accessPackageAssignment` es una estructura que contiene la propiedad `id` que identifica el `accessPackageAssignment` que se está quitando.

### <a name="remove-an-assignment-with-powershell"></a>Quitar una asignación con PowerShell

Puede quitar la asignación a un usuario en PowerShell con el cmdlet `New-MgEntitlementManagementAccessPackageAssignmentRequest` de los [cmdlets de Microsoft Graph PowerShell para el módulo Identity Governance](https://www.powershellgallery.com/packages/Microsoft.Graph.Identity.Governance/) versión 1.6.0 o posterior.

```powershell
Connect-MgGraph -Scopes "EntitlementManagement.ReadWrite.All"
Select-MgProfile -Name "beta"
$assignments = Get-MgEntitlementManagementAccessPackageAssignment -Filter "accessPackageId eq '9f573551-f8e2-48f4-bf48-06efbb37c7b8' and assignmentState eq 'Delivered'" -All -ErrorAction Stop
$toRemove = $assignments | Where-Object {$_.targetId -eq '76fd6e6a-c390-42f0-879e-93ca093321e7'}
$req = New-MgEntitlementManagementAccessPackageAssignmentRequest -AccessPackageAssignmentId $toRemove.Id -RequestType "AdminRemove"
```

## <a name="next-steps"></a>Pasos siguientes

- [Modificar la configuración de solicitud de un paquete de acceso](entitlement-management-access-package-request-policy.md)
- [Visualización de informes y registros](entitlement-management-reports.md)
