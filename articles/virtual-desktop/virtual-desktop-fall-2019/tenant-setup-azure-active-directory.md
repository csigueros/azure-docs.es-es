---
title: 'Creación de un inquilino en Azure Virtual Desktop (clásico): Azure'
description: Se describe cómo configurar inquilinos de Azure Virtual Desktop (clásico) en Azure Active Directory.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: f828ce5c246103462ca29066779468c7526e63ee
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111751818"
---
# <a name="tutorial-create-a-tenant-in-azure-virtual-desktop-classic"></a>Tutorial: Creación de un inquilino en Azure Virtual Desktop (clásico)

>[!IMPORTANT]
>Este contenido se aplica a Azure Virtual Desktop (clásico), que no admite objetos de Azure Resource Manager.

La creación de un inquilino en Azure Virtual Desktop es el primer paso hacia la compilación de la primera solución de virtualización de escritorio. Un inquilino es un grupo de uno o varios grupos host. Cada grupo host se compone de varios hosts de sesión, que se ejecutan como máquinas virtuales en Azure y que están registrados en el servicio Azure Virtual Desktop. Cada grupo host consta también de uno o varios grupos de aplicaciones que se usan para publicar recursos del escritorio remoto y de la aplicación remota para los usuarios. Con un inquilino, puede crear grupos host, crear grupos de aplicaciones, asignar usuarios y realizar conexiones en el servicio.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Conceder permisos de Azure Active Directory al servicio Azure Virtual Desktop.
> * Asignar el rol de aplicación TenantCreator a un usuario en el inquilino de Azure Active Directory.
> * Crear un inquilino de Azure Virtual Desktop.

## <a name="what-you-need-to-set-up-a-tenant"></a>Lo que necesita para configurar un inquilino

Antes de empezar a configurar el inquilino de Azure Virtual Desktop, asegúrese de que tiene lo siguiente:

* El identificador de inquilino de [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para los usuarios de Azure Virtual Desktop.
* Una cuenta de administrador global en el inquilino de Azure Active Directory.
   * Esto también es aplicable a las organizaciones de proveedores de soluciones en la nube (CSP) que crean un inquilino de Azure Virtual Desktop para sus clientes. Si este es su caso, debe poder iniciar sesión como administrador global de la instancia de Azure Active Directory del cliente.
   * La cuenta de administrador se debe originar en el inquilino de Azure Active Directory en el que se intenta crear el inquilino de Azure Virtual Desktop. Este proceso no es compatible con las cuentas de Azure Active Directory B2B (invitado).
   * La cuenta de administrador debe ser una cuenta profesional o educativa.
* Suscripción a Azure.

Debe tener el identificador de inquilino, la cuenta de administrador global y la suscripción de Azure listos para que el proceso descrito en este tutorial pueda funcionar correctamente.

## <a name="grant-permissions-to-azure-virtual-desktop"></a>Concesión de permisos para Azure Virtual Desktop

Si ya ha concedido permisos a Azure Virtual Desktop para esta instancia de Azure Active Directory, omita esta sección.

La concesión de permisos al servicio Azure Virtual Desktop le permite consultar Azure Active Directory para buscar tareas administrativas y de usuario final.

Para conceder los permisos de servicio:

1. Abra un explorador e inicie el flujo de consentimiento del administrador a la [aplicación de servidor de Azure Virtual Desktop](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Si administra un cliente y necesita conceder el consentimiento del administrador para el directorio del cliente, escriba la siguiente dirección URL en el explorador y reemplace {tenant} por el nombre de dominio de Azure AD del cliente. Por ejemplo, si la organización del cliente ha registrado el nombre de dominio de Azure AD contoso.onmicrosoft.com, reemplace {tenant} por contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Inicie sesión en la página de consentimiento de Azure Virtual Desktop con una cuenta de administrador global. Por ejemplo, si pertenecía a la organización Contoso, su cuenta podría ser admin@contoso.com o admin@contoso.onmicrosoft.com.
3. Seleccione **Aceptar**.
4. Espere un minuto para que Azure AD pueda registrar el consentimiento.
5. Abra un explorador e inicie el flujo de consentimiento del administrador en la [aplicación cliente de Azure Virtual Desktop](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Si administra un cliente y necesita conceder el consentimiento del administrador para el directorio del cliente, escriba la siguiente dirección URL en el explorador y reemplace {tenant} por el nombre de dominio de Azure AD del cliente. Por ejemplo, si la organización del cliente ha registrado el nombre de dominio de Azure AD contoso.onmicrosoft.com, reemplace {tenant} por contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Inicie sesión en la página de consentimiento de Azure Virtual Desktop como administrador global, como ha hecho en el paso 2 anterior.
7. Seleccione **Aceptar**.

## <a name="assign-the-tenantcreator-application-role"></a>Asignación del rol de aplicación TenantCreator

La asignación a un usuario de Azure Active Directory del rol de aplicación TenantCreator permite a ese usuario crear un inquilino de Azure Virtual Desktop asociado con la instancia de Azure Active Directory. Deberá usar su cuenta de administrador global para asignar el rol TenantCreator.

Para asignar el rol de aplicación TenantCreator:

1. Vaya a [Azure Portal](https://portal.azure.com) para administrar el rol de aplicación TenantCreator. Busque **Aplicaciones empresariales** y selecciónelo. Si está trabajando con varios inquilinos de Azure Active Directory, es recomendable abrir una sesión privada del explorador y copiar y pegar las direcciones URL en la barra de direcciones.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla de la búsqueda de aplicaciones empresariales en Azure Portal](../media/azure-portal-enterprise-applications.png)

2. En **Aplicaciones empresariales**, busque **Azure Virtual Desktop**. Verá las dos aplicaciones para las que ha dado su consentimiento en la sección anterior. De estas dos aplicaciones, seleccione **Azure Virtual Desktop**.

3. Seleccione **Usuarios y grupos**. Es posible que vea que el administrador que concedió consentimiento a la aplicación ya aparece con el rol **Acceso predeterminado** asignado. Esto no es suficiente para crear un inquilino de Azure Virtual Desktop. Siga con estas instrucciones para agregar el rol **TenantCreator** a un usuario.

4. Seleccione **+ Agregar usuario** y, después, **Usuarios y grupos** en la pestaña **Agregar asignación**.
5. Busque una cuenta de usuario que cree el inquilino de Azure Virtual Desktop. Para mayor sencillez, esta puede ser la cuenta de administrador global.
   - Si usa un proveedor de identidades de Microsoft como contosoadmin@live.com o contosoadmin@outlook.com, es posible que no pueda iniciar sesión en Azure Virtual Desktop. Se recomienda usar una cuenta específica de dominio como admin@contoso.com o admin@contoso.onmicrosoft.com en su lugar.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla de la selección de un usuario para agregar como "TenantCreator"](../media/tenant-assign-user.png).

   > [!NOTE]
   > Debe seleccionar un usuario (o un grupo que contiene un usuario) que tenga su origen en esta instancia de Azure Active Directory. No puede elegir un usuario invitado (B2B) o una entidad de servicio.

6. Seleccione la cuenta de usuario, haga clic en el botón **Seleccionar** y, a continuación, seleccione **Asignar**.
7. En la página **Azure Virtual Desktop - Usuarios y grupos**, compruebe que ve una nueva entrada con el rol **TenantCreator** asignado al usuario que va a crear el inquilino de Azure Virtual Desktop.

Antes de continuar para crear el inquilino de Azure Virtual Desktop, necesita dos datos más:

   - Su identificador de inquilino de Azure Active Directory (o **identificador de directorio**)
   - El identificador de suscripción a Azure

Para buscar su identificador de inquilino de Azure Active Directory (o **identificador de directorio**):
1. En la misma sesión de [Azure Portal](https://portal.azure.com), busque y seleccione **Azure Active Directory**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla de los resultados de búsqueda para "Azure Active Directory" en Azure Portal. El resultado de búsqueda en "Servicios" aparece resaltado.](../media/tenant-search-azure-active-directory.png)

2. Desplácese hacia abajo hasta que encuentre **Propiedades** y, a continuación, selecciónelo.
3. Busque el **identificador de directorio** y seleccione luego el icono del Portapapeles. Péguelo en un una ubicación práctica para que pueda usarlo más adelante como **AadTenantId**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla de las propiedades de Azure Active Directory. El puntero se mantiene sobre el icono del Portapapeles para copiar y pegar el identificador de directorio.](../media/tenant-directory-id.png)

Para buscar el identificador de suscripción de Azure:
1. En la misma sesión de [Azure Portal](https://portal.azure.com), busque y seleccione **Suscripciones**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla de los resultados de búsqueda para "Azure Active Directory" en Azure Portal. El resultado de búsqueda de "Servicios" aparece resaltado.](../media/tenant-search-subscription.png)

2. Seleccione la suscripción de Azure que quiere usar para recibir las notificaciones del servicio Azure Virtual Desktop.
3. Busque el **identificador de suscripción** y luego mantenga el puntero sobre el valor hasta que aparezca el icono del Portapapeles. Seleccione el icono del Portapapeles y péguelo en una ubicación práctica para que pueda usarlo más adelante como el valor de **AzureSubscriptionId**.

   > [!div class="mx-imgBorder"]
   > ![Captura de pantalla de las propiedades de la suscripción de Azure. El puntero se mantiene sobre el icono del Portapapeles para copiar y pegar el identificador de suscripción.](../media/tenant-subscription-id.png)

## <a name="create-a-azure-virtual-desktop-tenant"></a>Creación de un inquilino de Azure Virtual Desktop

Ahora que ha concedido los permisos al servicio Azure Virtual Desktop para consultar Azure Active Directory y ha asignado el rol TenantCreator a una cuenta de usuario, puede crear un inquilino de Azure Virtual Desktop.

En primer lugar y, si todavía no lo ha hecho, [descargue e importe el módulo de Azure Virtual Desktop](/powershell/windows-virtual-desktop/overview/) que se usará en la sesión de PowerShell.

Inicie sesión en Azure Virtual Desktop mediante la cuenta de usuario de TenantCreator con este cmdlet:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Después, cree un inquilino de Azure Virtual Desktop asociado al inquilino de Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Reemplace los valores entre corchetes por los valores pertinentes para la organización y el inquilino. El nombre que elija para el nuevo inquilino de Azure Virtual Desktop debe ser único globalmente. Por ejemplo, imagine que tiene el rol TenantCreator en Azure Virtual Desktop para la organización Contoso. El cmdlet que ejecutaría tendría este aspecto:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Es una buena idea asignar acceso administrativo a un segundo usuario por si alguna vez tiene el acceso bloqueado a su cuenta o por si se va de vacaciones y necesita que alguien actúe como administrador del inquilino en su ausencia. Para asignar acceso de administrador a un segundo usuario, ejecute el siguiente cmdlet y reemplace `<TenantName>` y `<Upn>` por el nombre del inquilino y el UPN del segundo usuario.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Pasos siguientes
Una vez que haya creado su inquilino, tendrá que crear una entidad de servicio en Azure Active Directory y asignarle un rol dentro de Azure Virtual Desktop. La entidad de servicio le permitirá implementar correctamente la oferta de Azure Marketplace de Azure Virtual Desktop para crear un grupo host. Para más información acerca de los grupos host, continúe con el tutorial de creación de un grupo host en Azure Virtual Desktop.

> [!div class="nextstepaction"]
> [Creación de entidades de servicio y asignaciones de roles con PowerShell](create-service-principal-role-powershell.md)
