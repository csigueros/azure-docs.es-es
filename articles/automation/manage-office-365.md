---
title: Administración de servicios de Office 365 mediante Azure Automation
description: En este artículo se indica cómo usar Azure Automation para administrar los servicios de suscripción de Office 365.
services: automation
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: f61149fd9c35cc55a3ed55fa83e6031d895fb073
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124737736"
---
# <a name="manage-office-365-services"></a>Administración de servicios de Office 365

Puede usar Azure Automation para la administración de los servicios de suscripción de Office 365, en productos como Microsoft Word y Microsoft Outlook. Las interacciones con Office 365 se habilitan mediante [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md). Consulte [Uso de Azure AD en Automation para autenticarse en Azure](automation-use-azure-ad.md).

## <a name="prerequisites"></a>Prerrequisitos

Necesita los siguientes elementos para administrar los servicios de suscripción de Office 365 en Azure Automation.

* Suscripción a Azure. Consulte [Guía de decisiones de suscripción](/azure/cloud-adoption-framework/decision-guides/subscriptions/).
* Un objeto de Automation en Azure que contenga las credenciales y los runbooks de la cuenta de usuario. Consulte [Introducción a Azure Automation](./automation-intro.md).
* Azure AD. Consulte [Uso de Azure AD en Automation para autenticarse en Azure](automation-use-azure-ad.md).
* Un inquilino de Office 365, con una cuenta. Consulte [Configurar el espacio empresarial de Office 365](/sharepoint/dev/spfx/set-up-your-developer-tenant).

## <a name="install-the-msonline-and-msonlineext-modules"></a>Instalación de los módulos MSOnline y MSOnlineExt

El uso de Office 365 en Azure Automation requiere Microsoft Azure Active Directory para Windows PowerShell (módulo `MSOnline`). También necesitará el módulo [`MSOnlineExt`](https://www.powershellgallery.com/packages/MSOnlineExt/1.0.35), que simplifica la administración de Azure AD en entornos de uno y de varios inquilinos. Instale los módulos tal como se describe en [Uso de Azure AD en Automation para autenticarse en Azure](automation-use-azure-ad.md).

>[!NOTE]
>Para usar MSOnline PowerShell, debe ser miembro de Azure AD. Los usuarios invitados no pueden usar el módulo.

## <a name="create-an-azure-automation-account"></a>Creación de una cuenta de Azure Automation

Para completar los pasos de este artículo, necesitará una cuenta en Azure Automation. Consulte [Creación de una cuenta de Azure Automation](./quickstarts/create-account-portal.md).
 
## <a name="add-msonline-and-msonlineext-as-assets"></a>Adición de MSOnline y MSOnlineExt como recursos

Agregue ahora los módulos MSOnline y MSOnlineExt instalados para habilitar la funcionalidad de Office 365. Consulte [Administración de módulos en Azure Automation](shared-resources/modules.md).

1. En Azure Portal, seleccione **Cuentas de Automation**.
2. Seleccione la cuenta de Automation.
3. Seleccione **Galería de módulos** en **Recursos compartidos**.
4. Busque MSOnline.
5. Seleccione el módulo `MSOnline` de PowerShell y haga clic en **Importar** para importar el módulo como un recurso.
6. Repita los pasos 4 y 5 para buscar e importar el módulo `MSOnlineExt`.

## <a name="create-a-credential-asset-optional"></a>Creación de un recurso de credencial (opcional)

La creación de un recurso de credencial para el usuario administrativo de Office 365 que tiene permisos para ejecutar el script es opcional; sin embargo, puede ser útil para evitar la exposición de nombres de usuario y contraseñas dentro de scripts de PowerShell. Para obtener instrucciones, vea [Creación de un recurso de credencial](automation-use-azure-ad.md#create-a-credential-asset).

## <a name="create-an-office-365-service-account"></a>Creación de una cuenta de servicio de Office 365

Para ejecutar los servicios de suscripción de Office 365, necesita una cuenta de servicio de Office 365 con permisos para hacer lo que desee. Puede usar una cuenta de administrador global, una cuenta por servicio o contar con una función o un script que se ejecuten. En cualquier caso, la cuenta de servicio requiere una contraseña compleja y segura. Consulte [Configurar Office 365 para empresas](/microsoft-365/admin/setup/setup).

## <a name="connect-to-the-azure-ad-online-service"></a>Conexión al servicio en línea de Azure AD

>[!NOTE]
>Para usar los cmdlets de módulo MSOnline, debe ejecutarlos desde Windows PowerShell. PowerShell Core no es compatible con estos cmdlets.

Puede usar el módulo MSOnline para conectarse a Azure AD desde la suscripción de Office 365. La conexión usa un nombre de usuario y una contraseña de Office 365 o la autenticación multifactor (MFA). Puede conectarse mediante Azure Portal o un símbolo del sistema de Windows PowerShell (no tiene que ser elevado).

A continuación se muestra el ejemplo de PowerShell. El cmdlet [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) solicita las credenciales y las almacena en la variable `Msolcred`. A continuación, el cmdlet [Connect-MsolService](/powershell/module/msonline/connect-msolservice) usa las credenciales para conectarse al servicio en línea de Azure AD. Si desea conectarse a un entorno específico de Azure, use el parámetro `AzureEnvironment`.

```powershell
$Msolcred = Get-Credential
Connect-MsolService -Credential $MsolCred -AzureEnvironment "AzureCloud"
```

Si no recibe ningún error, se ha conectado correctamente. Una prueba rápida consiste en ejecutar un cmdlet de Office 365, por ejemplo, `Get-MsolUser`, y ver los resultados. Si recibe errores, tenga en cuenta que un problema habitual es indicar una contraseña incorrecta.

>[!NOTE]
>También puede usar los módulos AzureRM o Az para conectarse a Azure AD desde la suscripción de Office 365. El cmdlet de conexión principal es [Connect-AzureAD](/powershell/module/azuread/connect-azuread). Este cmdlet admite el parámetro `AzureEnvironmentName` para entornos específicos de Office 365.

## <a name="create-a-powershell-runbook-from-an-existing-script"></a>Creación de un runbook de PowerShell a partir de un script existente

Puede acceder a la funcionalidad de Office 365 desde un script de PowerShell. A continuación se muestra un ejemplo de un script para una credencial denominada `Office-Credentials` con el nombre de usuario `admin@TenantOne.com`. Utiliza `Get-AutomationPSCredential` para importar la credencial de Office 365.

```powershell
$emailFromAddress = "admin@TenantOne.com"
$emailToAddress = "servicedesk@TenantOne.com"
$emailSMTPServer = "outlook.office365.com"
$emailSubject = "Office 365 License Report"

$credObject = Get-AutomationPSCredential -Name "Office-Credentials"
Connect-MsolService -Credential $credObject

$O365Licenses = Get-MsolAccountSku | Out-String
Send-MailMessage -Credential $credObject -From $emailFromAddress -To $emailToAddress -Subject $emailSubject -Body $O365Licenses -SmtpServer $emailSMTPServer -UseSSL
```

## <a name="run-the-script-in-a-runbook"></a>Ejecución del script en un runbook

Puede usar el script en un runbook de Azure Automation. A modo de ejemplo, usaremos el tipo de runbook de PowerShell.

1. Cree un runbook de PowerShell. Consulte [Creación de un runbook de Azure Automation](./learn/powershell-runbook-managed-identity.md).
2. En la cuenta de Automation, seleccione **Runbooks** en **Automatización de procesos**.
3. Seleccione el nuevo runbook y haga clic en **Editar**.
4. Copie el script y péguelo en el editor de texto para el runbook.
5. Seleccione **ASSETS** (Recursos), expanda **Credentials** (Credenciales) y compruebe que la credencial de Office 365 está incluida.
6. Haga clic en **Save**(Guardar).
7. Seleccione **Panel de prueba** y, a continuación, haga clic en **Iniciar** para empezar a probar el runbook. Consulte [Administración de runbooks en Azure Automation](./manage-runbooks.md).
8. Una vez finalizada la prueba, salga del panel prueba.

## <a name="publish-and-schedule-the-runbook"></a>Publicación y programación del runbook

Para publicar y programar el runbook, consulte [Administración de runbooks en Azure Automation](./manage-runbooks.md).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener más información sobre el uso de credenciales, vea [Administración de credenciales en Azure Automation](shared-resources/credentials.md).
* Para obtener más información sobre los módulos, vea [Administración de módulos en Azure Automation](shared-resources/modules.md).
* Si necesita iniciar un runbook, vea [Inicio de un runbook en Azure Automation](start-runbooks.md).
* Para obtener información sobre PowerShell, vea [Documentación de PowerShell](/powershell/scripting/overview).