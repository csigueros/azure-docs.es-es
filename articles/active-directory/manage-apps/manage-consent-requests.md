---
title: Administración del consentimiento en las aplicaciones y evaluación de las solicitudes de consentimiento
description: Aprenda a administrar las solicitudes de consentimiento cuando el consentimiento del usuario está deshabilitado o restringido, y a evaluar una solicitud de consentimiento del administrador para todo el inquilino a una aplicación en Azure Active Directory.
titleSuffix: Azure AD
services: active-directory
author: davidmu1
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2021
ms.author: davidmu
ms.reviewer: phsignor
ms.openlocfilehash: 039461797d9b451703959f43977234278e87827d
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131424066"
---
# <a name="manage-consent-to-applications-and-evaluate-consent-requests-in-azure-active-directory"></a>Administración del consentimiento en las aplicaciones y evaluación de las solicitudes de consentimiento en Azure Active Directory

Microsoft recomienda [restringir el consentimiento del usuario](../../active-directory/manage-apps/configure-user-consent.md) para permitir que los usuarios solo den su consentimiento para las aplicaciones de publicadores comprobados y únicamente para los permisos que seleccione. En el caso de las aplicaciones que no cumplan esta directiva, el proceso de toma de decisiones se centralizará en el equipo administrador de identidades y seguridad de la organización.

Después de deshabilitar o restringir el consentimiento del usuario final, hay varias consideraciones importantes que se deben tener en cuenta para asegurarse de que la organización se mantiene segura y, al mismo tiempo, se permite el uso de aplicaciones críticas para la empresa. Estos pasos son cruciales para minimizar el impacto en el equipo de soporte técnico y los administradores de TI de la organización, a la vez que se evita el uso de cuentas no administradas en aplicaciones de terceros.

## <a name="process-changes-and-education"></a>Cambios de proceso y educación

 1. Considere la posibilidad de habilitar el [flujo de trabajo de consentimiento del administrador](configure-admin-consent-workflow.md) para permitir que los usuarios soliciten la aprobación del administrador directamente desde la pantalla de consentimiento.

 2. Asegúrese de que todos los administradores entienden el [marco de permisos y consentimiento](../develop/consent-framework.md), cómo funciona la [solicitud de consentimiento](../develop/application-consent-experience.md) y cómo se [evalúa una solicitud de consentimiento del administrador para todo el inquilino](#evaluating-a-request-for-tenant-wide-admin-consent).
 3. Revise los procesos existentes en la organización para que los usuarios soliciten la aprobación del administrador para una aplicación y actualícelos si es necesario. Si los procesos cambian:
    * Actualice la documentación pertinente, la supervisión, la automatización, etc.
    * Comunique los cambios en los procesos a todos los usuarios afectados, desarrolladores, equipos de soporte técnico y administradores de TI.

## <a name="auditing-and-monitoring"></a>Auditoría y supervisión

1. Realice una [auditoría de las aplicaciones y los permisos concedidos](../../security/fundamentals/steps-secure-identity.md#audit-apps-and-consented-permissions) en la organización, para asegurarse de que no se ha concedido acceso a los datos a ninguna aplicación no justificada o sospechosa.

2. Revise el artículo [Detectar y solucionar la concesión de consentimiento ilegal en Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants) para conocer otros procedimientos recomendados y medidas de seguridad contra aplicaciones sospechosas que solicitan consentimiento de OAuth.

3. Si la organización tiene la licencia correspondiente:

    * Use otras [características de auditoría de aplicaciones de OAuth en Microsoft Cloud App Security](/cloud-app-security/investigate-risky-oauth).
    * Utilice [libros de Azure Monitor para supervisar la actividad relacionada con los permisos y el consentimiento](../reports-monitoring/howto-use-azure-monitor-workbooks.md). El libro *Consent Insights* proporciona una vista de las aplicaciones por número de solicitudes de consentimiento con error. Esta información puede resultar útil para clasificar las aplicaciones por orden de prioridad y que los administradores las revisen y decidan si se les concede el consentimiento del administrador.

### <a name="additional-considerations-for-reducing-friction"></a>Consideraciones adicionales para reducir la fricción

Para minimizar el impacto en las aplicaciones de confianza críticas para la empresa que ya están en uso, considere la posibilidad de conceder de forma proactiva el consentimiento del administrador a las aplicaciones que tienen un gran número de concesiones de consentimiento del usuario:

1. Realice un inventario de las aplicaciones ya agregadas a la organización que muestran un uso elevado, basado en registros de inicio de sesión o en la actividad de concesión de consentimiento. Se puede usar un [script](https://gist.github.com/psignoret/41793f8c6211d2df5051d77ca3728c09) de PowerShell para detectar de forma rápida y sencilla las aplicaciones con un gran número de concesiones de consentimiento del usuario.

2. Evalúe las principales aplicaciones a las que aún no se les ha concedido consentimiento del administrador.

   > [!IMPORTANT]
   > Evalúe cuidadosamente una aplicación antes de conceder consentimiento del administrador para todo el inquilino, incluso si muchos usuarios de la organización ya han dado su consentimiento.

3. Para cada aplicación que se apruebe, conceda el consentimiento del administrador para todo el inquilino mediante uno de los métodos que se describen a continuación.

4. Para cada aplicación aprobada, considere la posibilidad de [restringir el acceso del usuario](configure-user-consent.md).

## <a name="evaluating-a-request-for-tenant-wide-admin-consent"></a>Evaluación de una solicitud de consentimiento del administrador para todo el inquilino

La concesión del consentimiento del administrador para todo el inquilino es una operación delicada.  Los permisos se concederán en nombre de toda la organización y pueden incluir permisos para intentar realizar operaciones con privilegios elevados. Por ejemplo, administración de roles, acceso completo a todos los buzones de correo o todos los sitios y suplantación total de los usuarios.

Antes de conceder el consentimiento del administrador para todo el inquilino, debe asegurarse de que confía en la aplicación y en el editor de la aplicación para el nivel de acceso que va a conceder. Si no sabe con seguridad quién controla la aplicación y por qué la aplicación está solicitando los permisos, *no conceda el consentimiento*.

En la siguiente lista se proporcionan algunas recomendaciones que tener en cuenta a la hora de evaluar una solicitud para conceder el consentimiento del administrador.

* **Comprenda el [marco de permisos y consentimiento](../develop/consent-framework.md) de la plataforma de identidad de Microsoft.**

* **Comprenda la diferencia entre los [permisos delegados y los permisos de aplicación](../develop/v2-permissions-and-consent.md#permission-types).**

   Los permisos de aplicación permiten que esta acceda a los datos de toda la organización, sin interacción de ningún usuario. Los permisos delegados permiten que la aplicación actúe en nombre de un usuario que en algún momento inició sesión en la aplicación.

* **Comprenda los permisos que se solicitan.**

   Los permisos solicitados por la aplicación se enumeran en la [petición de consentimiento](../develop/application-consent-experience.md). Cuando se expande el título del permiso, se muestra su descripción. La descripción de los permisos de aplicación suele acabar con "sin que inicie sesión ningún usuario". La descripción de los permisos delegados suele acabar con "en nombre del usuario que inició sesión". Los permisos para Microsoft Graph API se describen en [Referencia de permisos de Microsoft Graph](/graph/permissions-reference). Consulte la documentación de otras API para comprender los permisos que exponen.

   Si no entiende el permiso que se solicita, *no conceda el consentimiento*.

* **Comprenda qué aplicación solicita permisos y quién la publicó.**

   Tenga cuidado con las aplicaciones malintencionadas que intentan parecer otras aplicaciones.

   Si duda de la legitimidad de una aplicación o su editor, *no conceda el consentimiento*. En su lugar, busque confirmación adicional (por ejemplo, directamente del editor de la aplicación).

* **Asegúrese de que los permisos solicitados se corresponden con las características que espera de la aplicación.**

   Por ejemplo, una aplicación que ofrezca administración de sitios de SharePoint puede requerir acceso delegado para leer todas las colecciones de sitios, pero no precisaría necesariamente acceso completo a todos los buzones ni privilegios de suplantación total en el directorio.

   Si sospecha que la aplicación solicita más permisos de los que necesita, *no conceda el consentimiento*. Póngase en contacto con el editor de la aplicación para obtener más detalles.

## <a name="granting-consent-as-an-administrator"></a>Concesión del consentimiento como administrador

### <a name="granting-tenant-wide-admin-consent"></a>Concesión del consentimiento del administrador para todo el inquilino

Consulte [Concesión del consentimiento del administrador para todo el inquilino a una aplicación](grant-admin-consent.md) para obtener instrucciones paso a paso de esta operación desde Azure Portal, mediante Azure AD PowerShell o desde la propia petición de consentimiento.

### <a name="granting-consent-on-behalf-of-a-specific-user"></a>Concesión de consentimiento en nombre de un usuario específico

En lugar de conceder el consentimiento para toda la organización, un administrador también puede usar [Microsoft Graph API](/graph/use-the-api) para conceder consentimiento a los permisos delegados en nombre de un solo usuario. Para obtener un ejemplo detallado del uso de Microsoft Graph PowerShell, consulte [Concesión de consentimiento en nombre de un solo usuario mediante PowerShell](#grant-consent-on-behalf-of-a-single-user-using-powershell).

## <a name="limiting-user-access-to-applications"></a>Limitación del acceso de los usuarios a las aplicaciones

Aunque se haya concedido consentimiento del administrador para todo el inquilino, todavía puede limitarse el acceso de los usuarios a las aplicaciones. Para más información sobre cómo requerir la asignación de usuarios a una aplicación, consulte los [métodos para asignar usuarios y grupos](./assign-user-or-group-access-portal.md).

Para una visión general más amplia sobre cómo tratar otros escenarios complejos, consulte cómo [usar Azure AD para la administración del acceso a las aplicaciones](what-is-access-management.md).

## <a name="disable-all-future-user-consent-operations-to-any-application"></a>Deshabilitación de todas las operaciones de consentimiento de usuario futuras para todas las aplicaciones

Deshabilitar el consentimiento del usuario para todo el directorio impide que los usuarios finales den consentimiento a cualquier aplicación. A pesar de ello, los administradores podrán seguir dando el consentimiento en nombre de los usuarios. Para más información sobre el consentimiento de aplicación y los motivos por los que es posible que quiera o no quiera hacer esto, lea el artículo [Descripción del consentimiento de usuario y administrador](../develop/howto-convert-app-to-be-multi-tenant.md).

Para deshabilitar todas las operaciones de consentimiento de usuario futuras en todo el directorio, siga estos pasos:

1. Abra [**Azure Portal**](https://portal.azure.com/) e inicie sesión como **administrador global.**
2. Abra la **extensión de Azure Active Directory** haciendo clic en **Todos los servicios** en la parte superior del menú de navegación izquierdo principal.
3. Escriba **"Azure Active Directory**" en el cuadro de búsqueda de filtrado y seleccione el elemento **Azure Active Directory**.
4. Seleccione **Aplicaciones empresariales** y después **Configuración de usuario** en la sección **Administrar**.
:::image type="content" source="media/manage-consent-requests/disable-user-consent-operations.png" alt-text="Deshabilitación de las operaciones de consentimiento del usuario para todas las aplicaciones.":::
5. Deshabilite todas las operaciones de consentimiento de usuario futuras; para ello, establezca la opción **Users can consent to apps accessing company data on their behalf** (Los usuarios pueden permitir que las aplicaciones accedan a los datos de la compañía en su nombre) en **No** y haga clic en el botón **Guardar**.

## <a name="grant-consent-on-behalf-of-a-single-user-using-powershell"></a>Concesión de consentimiento en nombre de un solo usuario mediante PowerShell

Cuando un usuario concede consentimiento en nombre propio, ocurre lo siguiente:

1. Se crea una entidad de servicio para la aplicación cliente, si aún no existe. Una entidad de servicio es la instancia de una aplicación o un servicio, en el inquilino de Azure AD. El acceso concedido a la aplicación o servicio está asociado a este objeto de entidad de servicio.
1. Para cada API a la que la aplicación requiere acceso, se crea una concesión de permisos delegados para los permisos que necesita la aplicación para esa API, para el acceso en nombre del usuario. Una concesión de permisos delegados autoriza a una aplicación a acceder a una API en nombre de un usuario, cuando ese usuario ha iniciado sesión.
1. Al usuario se le asigna la aplicación cliente. La asignación de la aplicación al usuario garantiza que la aplicación aparezca en el portal [Aplicaciones](my-apps-deployment-plan.md) de ese usuario, lo que le permite revisar y revocar el acceso concedido en su nombre.

Para realizar manualmente los pasos equivalentes a la concesión de consentimiento a una aplicación en nombre de un usuario, necesitará los siguientes detalles:

* El id. de aplicación de la aplicación a la que va a conceder consentimiento (la llamaremos "aplicación cliente").
* Los permisos de API requeridos por la aplicación cliente. Deberá conocer el id. de la aplicación de la API y los id. de permiso o los valores de notificación.
* El nombre de usuario o el id. de objeto del usuario en nombre del que se concederá acceso.

En el ejemplo siguiente, usaremos [Microsoft Graph PowerShell](/graph/powershell/get-started) para realizar los tres pasos indicados anteriormente para conceder consentimiento en nombre de un solo usuario. En este ejemplo, la aplicación cliente será [Microsoft Graph Explorer](https://aka.ms/ge) y se concederá acceso a Microsoft Graph API.

```powershell
# The app for which consent is being granted. In this example, we're granting access
# to Microsoft Graph Explorer, an application published by Microsoft.
$clientAppId = "de8bc8b5-d9f9-48b1-a8ad-b748da725064" # Microsoft Graph Explorer

# The API to which access will be granted. Microsoft Graph Explorer makes API 
# requests to the Microsoft Graph API, so we'll use that here.
$resourceAppId = "00000003-0000-0000-c000-000000000000" # Microsoft Graph API

# The permissions to grant. Here we're including "openid", "profile", "User.Read"
# and "offline_access" (for basic sign-in), as well as "User.ReadBasic.All" (for 
# reading other users' basic profile).
$permissions = @("openid", "profile", "offline_access", "User.Read", "User.ReadBasic.All")

# The user on behalf of who access will be granted. The app will be able to access 
# the API on behalf of this user.
$userUpnOrId = "user@example.com"

# Step 0. Connect to Microsoft Graph PowerShell. We need User.ReadBasic.All to get
#    users' IDs, Application.ReadWrite.All to list and create service principals, 
#    DelegatedPermissionGrant.ReadWrite.All to create delegated permission grants, 
#    and AppRoleAssignment.ReadWrite.All to assign an app role.
#    WARNING: These are high-privilege permissions!
Connect-MgGraph -Scopes ("User.ReadBasic.All Application.ReadWrite.All " `
                        + "DelegatedPermissionGrant.ReadWrite.All " `
                        + "AppRoleAssignment.ReadWrite.All")

# Step 1. Check if a service principal exists for the client application. 
#     If one does not exist, create it.
$clientSp = Get-MgServicePrincipal -Filter "appId eq '$($clientAppId)'"
if (-not $clientSp) {
   $clientSp = New-MgServicePrincipal -AppId $clientAppId
}

# Step 2. Create a delegated permission grant granting the client app access to the
#     API, on behalf of the user. (This example assumes that an existing delegated 
#     permission grant does not already exist, in which case it would be necessary 
#     to update the existing grant, rather than create a new one.)
$user = Get-MgUser -UserId $userUpnOrId
$resourceSp = Get-MgServicePrincipal -Filter "appId eq '$($resourceAppId)'"
$scopeToGrant = $permissions -join " "
$grant = New-MgOauth2PermissionGrant -ResourceId $resourceSp.Id `
                                     -Scope $scopeToGrant `
                                     -ClientId $clientSp.Id `
                                     -ConsentType "Principal" `
                                     -PrincipalId $user.Id

# Step 3. Assign the app to the user. This ensure the user can sign in if assignment
#     is required, and ensures the app shows up under the user's My Apps.
if ($clientSp.AppRoles | ? { $_.AllowedMemberTypes -contains "User" }) {
    Write-Warning ("A default app role assignment cannot be created because the " `
                 + "client application exposes user-assignable app roles. You must " `
                 + "assign the user a specific app role for the app to be listed " `
                 + "in the user's My Apps access panel.")
} else {
    # The app role ID 00000000-0000-0000-0000-000000000000 is the default app role
    # indicating that the app is assigned to the user, but not for any specific 
    # app role.
    $assignment = New-MgServicePrincipalAppRoleAssignedTo `
          -ServicePrincipalId $clientSp.Id `
          -ResourceId $clientSp.Id `
          -PrincipalId $user.Id `
          -AppRoleId "00000000-0000-0000-0000-000000000000"
}
```

## <a name="next-steps"></a>Pasos siguientes

* [Cinco pasos para asegurar su infraestructura de identidad](../../security/fundamentals/steps-secure-identity.md#before-you-begin-protect-privileged-accounts-with-mfa)
* [Configuración del flujo de trabajo de consentimiento del administrador](configure-admin-consent-workflow.md)
* [Configuración del consentimiento de los usuarios finales a las aplicaciones](configure-user-consent.md)
* [Permisos y consentimiento en la plataforma de identidad de Microsoft](../develop/v2-permissions-and-consent.md)
