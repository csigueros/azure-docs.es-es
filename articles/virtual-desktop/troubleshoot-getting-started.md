---
title: Solución de problemas de la característica de introducción en Azure Virtual Desktop
description: Cómo solucionar de problemas con la característica de introducción de Azure Virtual Desktop.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 08/06/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: 21cdccf5a025db4516d53c506c070fcf6f580fb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780680"
---
# <a name="troubleshoot-the-azure-virtual-desktop-getting-started-feature"></a>Solución de problemas de la característica de introducción a Azure Virtual Desktop

La característica de introducción de Azure Virtual Desktop usa plantillas anidadas para implementar recursos de Azure para la validación y automatización en Azure Virtual Desktop. La característica de introducción crea dos o tres grupos de recursos en función de si la suscripción en la que se ejecuta tiene o no Active Directory Domain Services (AD DS) o Azure Active Directory Domain Services (Azure AD DS). Todos los grupos de recursos comienzan con el mismo prefijo definido por el usuario.

Al ejecutar las plantillas anidadas, crean tres grupos de recursos y una plantilla que aprovisiona recursos de Azure Resource Manager. En las listas siguientes se muestra cada grupo de recursos y las plantillas que ejecutan.

El grupo de recursos que termina en "-deployment" ejecuta estas plantillas:

- easy-button-roleassignment-job-linked-template
- easy-button-prerequisitecompletion-job-linked-template
- easy-button-prerequisite-job-linked-template
- easy-button-inputvalidation-job-linked-template
- easy-button-deploymentResources-linked-template
- easy-button-prerequisite-user-setup-linked-template

>[!NOTE]
>La plantilla easy-button-prerequisite-user-setup-linked-template es opcional y solo aparecerá si creó un usuario de validación.

El grupo de recursos que termina en "-wvd" ejecuta estas plantillas:

- NSG-linkedTemplate
- vmCreation-linkedTemplate
- Workspace-linkedTemplate
- wvd-resources-linked-template
- easy-button-wvdsetup-linked-template

El grupo de recursos que termina en "-prerequisite" ejecuta estas plantillas:

- easy-button-prerequisite-resources-linked-template

>[!NOTE]
>Este grupo de recursos es opcional y solo aparecerá si la suscripción no tiene Azure AD DS o AD DS.

## <a name="no-subscriptions"></a>No hay suscripciones.

En este problema, verá un mensaje de error que indica "no hay suscripciones" al abrir la característica de introducción. Esto sucede cuando se intenta abrir la característica sin una suscripción de Azure activa.

Para corregir este problema, compruebe si su suscripción o el usuario afectado tiene una suscripción de Azure activa. Si no es así, asigne al usuario el rol Propietario del control de acceso basado en rol en su suscripción.

## <a name="you-dont-have-permissions"></a>No tiene permisos.

Este problema se produce cuando abre la característica de introducción y recibe un mensaje de error que indica "no tiene permisos". Este mensaje aparece cuando el usuario que ejecuta la característica no tiene permisos de propietario en su suscripción activa de Azure.

Para corregir este problema, inicie sesión con una cuenta de Azure que tenga permisos de propietario y, a continuación, asigne el rol de propietario del control de acceso basado en rol a la cuenta afectada.

## <a name="fields-under-virtual-machine-tab-are-grayed-out"></a>Los campos de la pestaña Máquina virtual están atenuados

Este problema se produce cuando se abre la pestaña **Máquina virtual** y ve que los campos de "¿Desea que los usuarios compartan esta máquina?" están atenuados. Este problema le impide cambiar el tipo de imagen, seleccionar la imagen que se va a usar o cambiar el tamaño de la máquina virtual.

Este problema se produce cuando ejecuta la característica con un prefijo que ya se usó para iniciar una implementación. Cuando la característica crea una implementación, crea un objeto para representar la implementación en Azure. Ciertos valores del objeto, como la imagen, se adjuntan a ese objeto para evitar que varios objetos utilicen las mismas imágenes.

Para corregir este problema, puede eliminar todos los grupos de recursos con el prefijo existente o usar un nuevo prefijo.

## <a name="username-must-not-include-reserved-words"></a>El nombre de usuario no debe incluir palabras reservadas.

Este problema se produce cuando la característica de introducción no aceptará el nuevo nombre de usuario que escriba en el campo.

Este mensaje de error aparece porque Azure no permite determinadas palabras en nombres de usuario para puntos de conexión públicos. Para ver una lista completa de palabras bloqueadas, consulte [Resolución de errores en los nombres de recursos reservados](../azure-resource-manager/templates/error-reserved-resource-name.md).

Para resolver este problema, pruebe una palabra nueva o agregue letras a la palabra bloqueada para que sea única. Por ejemplo, si la palabra "admin" está bloqueada, pruebe a usar "AVDadmin" en su lugar.

## <a name="the-value-must-be-between-12-and-72-characters-long"></a>El valor debe tener entre 12 y 72 caracteres de longitud.

Este mensaje de error aparece al escribir una contraseña demasiado larga o demasiado corta en relación con el requisito de longitud de caracteres. Los requisitos de longitud y complejidad de la contraseña de Azure se aplican incluso a los campos que más adelante se usan en Windows, que tiene requisitos menos estrictos.

Para resolver este problema, asegúrese de usar una cuenta que siga las [directrices de contraseña de Microsoft](https://www.microsoft.com/research/publication/password-guidance) o use la [protección con contraseña de Azure AD](../active-directory/authentication/concept-password-ban-bad.md).

## <a name="error-messages-for-easy-button-prerequisite-user-setup-linked-template"></a>Mensajes de error para easy-button-prerequisite-user-setup-linked-template

Si la máquina virtual con AD DS que usa ya tiene asociada una extensión denominada Microsoft.Powershell.DSC, verá un mensaje de error similar al siguiente:

```azure
"error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'Microsoft.Powershell.DSC'. Error message: \\\"DSC Configuration 'AddADDSUser' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: Some error occurred in DSC CreateUser SetScript: \\r\\n\\r\\nException             : Microsoft.ActiveDirectory.Management.ADIdentityNotFoundException: Cannot find an object with \\r\\n                        identity: 'Adam S' under: 'DC=GT090617,DC=onmicrosoft,DC=com'.\\r\\n                           at Microsoft.ActiveDirectory.Management.Commands.ADFactoryUtil.GetObjectFromIdentitySearcher(\\r\\n                        ADObjectSearcher searcher, ADEntity identityObj, String searchRoot, AttributeSetRequest attrs, \\r\\n                        CmdletSessionInfo cmdletSessionInfo, String[]& warningMessages)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.ADFactory`1.GetDirectoryObjectFromIdentity(T \\r\\n                        identityObj, String searchRoot, Boolean showDeleted)\\r\\n                           at \\r\\n                        Microsoft.ActiveDirectory.Management.Commands.SetADGroupMember`1.ValidateMembersParameter()\\r\\nTargetObject          : Adam S\\r\\nCategoryInfo          : ObjectNotFound: (Adam S:ADPrincipal) [Add-ADGroupMember], ADIdentityNotFoundException\\r\\nFullyQualifiedErrorId : SetADGroupMember.ValidateMembersParameter,Microsoft.ActiveDirectory.Management.Commands.AddADGro\\r\\n                        upMember\\r\\nErrorDetails          : \\r\\nInvocationInfo        : System.Management.Automation.InvocationInfo\\r\\nScriptStackTrace      : at <ScriptBlock>, C:\\\\Packages\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.83.1.0\\\\DSCWork\\\\DSCADUserCreatio\\r\\n                        nScripts_2020-04-28.2\\\\Script-CreateADDSUser.ps1: line 98\\r\\n                        at <ScriptBlock>, <No file>: line 8\\r\\n                        at ScriptExecutionHelper, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfi\\r\\n                        guration\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 270\\r\\n                        at Set-TargetResource, C:\\\\Windows\\\\system32\\\\WindowsPowerShell\\\\v1.0\\\\Modules\\\\PSDesiredStateConfigur\\r\\n                        ation\\\\DscResources\\\\MSFT_ScriptResource\\\\MSFT_ScriptResource.psm1: line 144\\r\\nPipelineIterationInfo : {}\\r\\nPSMessageDetails      : \\r\\n\\r\\n\\r\\n\\r\\n  The SendConfigurationApply function did not succeed.\\\"\\r\\n\\r\\nMore information on troubleshooting is available at https://aka.ms/VMExtensionDSCWindowsTroubleshoot \"\r\n      }\r\n    ]\r\n  }\r\n}"
            }
        ]
    }

```

Para resolver este problema, desinstale la extensión Microsoft.Powershell.DSC y vuelva a ejecutar la característica de introducción.

## <a name="error-messages-for-easy-button-prerequisite-job-linked-template"></a>Mensajes de error para easy-button-prerequisite-job-linked-template

Si ve un mensaje de error como este, significa que la operación de recursos de la plantilla easy-button-prerequisite-job-linked-template no se ha completado correctamente:

```azure
{
    "status": "Failed",
    "error": {
        "code": "DeploymentFailed",
        "message": "At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.",
        "details": [
            {
                "code": "Conflict",
                "message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\"\r\n  }\r\n}"
            }
        ]
    }
}

```

Para asegurarse de que este es el problema con el que está tratando:

1. Seleccione **easy-button-prerequisite-job-linked-template** y, a continuación, seleccione **Aceptar** en la ventana del mensaje de error que aparece.

2. Vaya al **grupo de recursos \<prefix\>-deployment** y seleccione **resourceSetupRunbook**.

3. Seleccione el estado, que debe decir **Error**.

4. Seleccione la pestaña **Excepción**. Debería ver un mensaje de error similar al siguiente:
   
   ```azure
   The running command stopped because the preference variable "ErrorActionPreference" or common parameter is set to Stop: Error while creating and adding validation user <your-username-here> to group <your-resource-group-here>
   ```

Actualmente no hay ninguna manera de corregir este problema de forma permanente. Como solución alternativa, vuelva a ejecutar la característica de introducción de Azure Virtual Desktop, pero esta vez no cree un usuario de validación. Después, cree los nuevos usuarios solo con el proceso manual.

### <a name="validate-that-the-domain-administrator-upn-exists-for-a-new-profile"></a>Valide que el UPN del administrador de dominio existe para un nuevo perfil.

Para comprobar si la dirección UPN está causando el problema con la plantilla:

1. Seleccione **easy-button-prerequisite-job-linked-template** y, a continuación, el paso con el error. Confirme el mensaje de error.

2. Vaya al **grupo de recursos \<prefix\>-deployment** y haga clic en **resourceSetupRunbook**.

3. Seleccione el estado, que debe decir **Error**.

4. Seleccione la pestaña **Salida**.

Si el UPN existe en la nueva suscripción, hay dos causas posibles del problema:

- La característica de introducción no ha creado el perfil de administrador de dominio, porque el usuario ya existe. Para resolverlo, vuelva a ejecutar la característica de introducción, pero esta vez escriba un nombre de usuario que aún no exista en el proveedor de identidades.
- La característica de introducción no ha creado el perfil de usuario de validación. Para resolver este problema, vuelva a ejecutar la característica de introducción, pero esta vez no cree ningún usuario de validación. Después, cree los nuevos usuarios solo con el proceso manual.

## <a name="error-messages-for-easy-button-inputvalidation-job-linked-template"></a>Mensajes de error para easy-button-inputvalidation-job-linked-template

Si hay un problema con la plantilla easy-button-inputvalidation-job-linked-template, verá un mensaje de error similar al siguiente:

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state 'Failed'."
    }
}
```

Para asegurarse de que este es el problema que ha encontrado:

1. Abra el grupo de recursos \<prefix\>-deployment y busque **inputValidationRunbook**.

2. En los trabajos recientes, habrá un trabajo con estado de error. Haga clic en **Error**.

3. En la ventana de **detalles del trabajo**, seleccione **Excepción**.

Este error se produce cuando el UPN de administrador de Azure especificado no es correcto. Para resolver este problema, asegúrese de que escribe correctamente el nombre de usuario y la contraseña y vuelva a intentarlo.

## <a name="multiple-vmextensions-per-handler-not-supported"></a>No se admiten varias instancias de VMExtension por controlador.

Al ejecutar la característica de introducción en una suscripción que tiene Azure AD DS o AD DS, la característica usará una extensión Microsoft.Powershell.DSC para crear usuarios de validación y configurar FSLogix. Sin embargo, las máquinas virtuales de Windows en Azure no pueden ejecutar más de uno de los mismos tipos de extensión al mismo tiempo.

Si intenta ejecutar varias versiones de Microsoft.Powershell.DSC, verá un mensaje de error similar al siguiente:

```azure
{
    "status": "Failed",
    "error": {
        "code": "BadRequest",
        "message": "Multiple VMExtensions per handler not supported for OS type 'Windows'. VMExtension 'Microsoft.Powershell.DSC' with handler 'Microsoft.Powershell.DSC' already added or specified in input."
    }
}
```

Para resolver este problema, antes de ejecutar la característica de introducción, asegúrese de quitar cualquier instancia en ejecución de Microsoft.Powershell.DSC de la máquina virtual del controlador de dominio.

## <a name="failure-in-easy-button-prerequisitecompletion-job-linked-template"></a>Error en easy-button-prerequisitecompletion-job-linked-template

El grupo de usuarios para los usuarios de validación se encuentra en el contenedor "USERS". Sin embargo, el grupo de usuarios debe sincronizarse con Azure AD para que funcione correctamente. Si no es así, verá un mensaje de error similar al siguiente:

```azure
{
    "status": "Failed",
    "error": {
        "code": "ResourceDeploymentFailure",
        "message": "The resource operation completed with terminal provisioning state ‘Failed’."
    }
}
```

Para asegurarse de que el problema se debe a que el grupo de usuarios de validación no se sincroniza, abra el grupo de recursos \<prefix\>-prerequisites y busque un archivo denominado **prerequisiteSetupCompletionRunbook**. Seleccione el runbook y, a continuación, seleccione **Todos los registros**.

Para solucionar este problema:

1. Habilite la sincronización con Azure AD para el contenedor "USERS".

2. Cree el grupo AVDValidationUsers en una unidad organizativa que se esté sincronizando con Azure.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la característica de introducción en [Implementación de Azure Virtual Desktop con la característica de introducción](getting-started-feature.md).
