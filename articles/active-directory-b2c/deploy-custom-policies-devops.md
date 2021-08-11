---
title: Implementar directivas personalizadas con Azure Pipelines
titleSuffix: Azure AD B2C
description: Aprenda a implementar directivas personalizadas de Azure AD B2C en una canalización de CI/CD mediante Azure Pipelines.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 06/01/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 470fcebf33e995d4c81d916970d80015b8f7c8f6
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110783757"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Implementar directivas personalizadas con Azure Pipelines

[Azure Pipelines](/azure/devops/pipelines) admite la integración continua (CI) y la entrega continua (CD) para probar y compilar un código de forma constante y coherente, y enviarlo a cualquier destino. En este artículo se describe cómo automatizar el proceso de implementación de las [directivas personalizadas](user-flow-overview.md) de Azure Active Directory B2C (Azure AD B2C) mediante Azure Pipelines.

> [!IMPORTANT]
> La administración de las directivas personalizadas de Azure AD B2C con Azure Pipelines usa actualmente la **vista previa** de las operaciones de disponibles en el punto de conexión `/beta` de Microsoft Graph API. No se admite su uso en aplicaciones de producción. Para obtener más información, consulte [Referencia del punto de conexión de la API de REST Microsoft Graph beta](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true).

## <a name="prerequisites"></a>Prerrequisitos

* Siga los pasos de [Introducción a las directivas personalizadas en Active Directory B2C](tutorial-create-user-flows.md).
* Si no ha creado una organización de DevOps, hágalo mediante las instrucciones de [Registrarse e iniciar sesión en Azure DevOps](/azure/devops/user-guide/sign-up-invite-teammates).  

## <a name="register-an-application-for-management-tasks"></a>Registrar una aplicación para tareas de administración

Puede usar el script de PowerShell para implementar las directivas de Azure AD B2C. Para que el script de PowerShell pueda interactuar con [Microsoft Graph API](microsoft-graph-operations.md), cree un registro de aplicación en el inquilino de Azure AD B2C. Si aún no lo ha hecho, [registre una aplicación de Microsoft Graph](microsoft-graph-get-started.md).

Para que el script de PowerShell tenga acceso a los datos de Microsoft Graph, conceda a la aplicación registrada los [permisos de aplicación](/graph/permissions-reference) pertinentes. Se ha concedido el permiso **Microsoft Graph** > **Policy** > **Policy.ReadWrite.TrustFramework** dentro de los **permisos de API** del registro de la aplicación.

## <a name="configure-an-azure-repo"></a>Configurar un repositorio de Azure

Una vez registrada la aplicación de Microsoft Graph, está listo para configurar un repositorio para los archivos de directivas.

1. Inicie sesión en su [organización de Azure DevOps](https://azure.microsoft.com/services/devops/).
1. [Cree un nuevo proyecto][devops-create-project] o seleccione uno existente.
1. En el proyecto, vaya a **Repos** y seleccione **Archivos**. 
1. Seleccione un repositorio existente o cree uno.
1. En el directorio raíz del repositorio, cree una carpeta denominada `B2CAssets`. Agregue los archivos de la directiva personalizada de Azure AD B2C a la carpeta *B2CAssets*.
1. En el directorio raíz del repositorio, cree una carpeta denominada `Scripts`. Cree un archivo de PowerShell denominado *DeployToB2C.ps1*. Pegue el siguiente script de PowerShell en *DeployToB2C.ps1*. 
1. **Confirme** e **inserte** los cambios.

El siguiente script adquiere un token de acceso de Azure AD. Con el token, el script llama a Microsoft Graph API para cargar las directivas en la carpeta *B2CAssets*. También puede cambiar el contenido de la directiva antes de cargarla. Por ejemplo, reemplace `tenant-name.onmicrosoft.com` por el nombre del inquilino.

```PowerShell
[Cmdletbinding()]
Param(
    [Parameter(Mandatory = $true)][string]$ClientID,
    [Parameter(Mandatory = $true)][string]$ClientSecret,
    [Parameter(Mandatory = $true)][string]$TenantId,
    [Parameter(Mandatory = $true)][string]$PolicyId,
    [Parameter(Mandatory = $true)][string]$PathToFile
)

try {
    $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

    $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
    $token = $response.access_token

    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("Content-Type", 'application/xml')
    $headers.Add("Authorization", 'Bearer ' + $token)

    $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
    $policycontent = Get-Content $PathToFile

    # Optional: Change the content of the policy. For example, replace the tenant-name with your tenant name.
    # $policycontent = $policycontent.Replace("your-tenant.onmicrosoft.com", "contoso.onmicrosoft.com")     

    $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

    Write-Host "Policy" $PolicyId "uploaded successfully."
}
catch {
    Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

    $_

    $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
    $streamReader.BaseStream.Position = 0
    $streamReader.DiscardBufferedData()
    $errResp = $streamReader.ReadToEnd()
    $streamReader.Close()

    $ErrResp

    exit 1
}

exit 0
```

## <a name="configure-azure-pipelines"></a>Configuración de Azure Pipelines

Con el repositorio inicializado y rellenado con los archivos de directivas personalizadas, está listo para configurar la canalización de versión. Para crear una canalización, siga estos pasos:

1. En el proyecto, seleccione **Canalizaciones** > **Versiones** > **Nueva canalización**.
1. En **Seleccionar una plantilla**, seleccione **Fase vacía** y, a continuación, seleccione **Aplicar**.
1. Escriba un **Nombre de fase**, por ejemplo, *DeployCustomPolicies*, y luego cierre el panel.
1. Seleccione **Agregar un artefacto** y, en **Tipo de origen**, seleccione **Azure Repository** (Repositorio de Azure).
    1. En **Proyecto**, seleccione el proyecto.
    1. Seleccione el **origen (repositorio)** que contiene la carpeta *Scripts*.
    1. Seleccione una **rama predeterminada**, por ejemplo, *master*.
    1. Deje la configuración de **Versión predeterminada** en *Más reciente desde la rama predeterminada*.
    1. Escriba un **Alias de origen** para el repositorio. Por ejemplo, *policyRepo*. 
1. Seleccione **Agregar**.
1. Cambie el nombre de la canalización para que refleje su intención. Por ejemplo, *Implementar la canalización de directivas personalizadas*.
1. Para guardar la configuración de canalización, seleccione **Guardar**.

### <a name="configure-pipeline-variables"></a>Configurar variables de canalización

Las variables de la canalización proporcionan una manera cómoda de obtener los bits clave de los datos en varias partes de la canalización. Las siguientes variables proporcionan información sobre el entorno de Azure AD B2C.

| Nombre | Value |
| ---- | ----- |
| `clientId` | **Id. de aplicación (cliente)** de la aplicación que registró anteriormente. |
| `clientSecret` | El valor del **secreto de cliente** que creó anteriormente. <br /> Cambie el tipo de variable a **secreto** (seleccione el icono de candado). |
| `tenantId` | `your-b2c-tenant.onmicrosoft.com`, donde *your-b2c-tenant* es el nombre del inquilino de Azure AD B2C. |

Para agregar canalizaciones, siga estos pasos:

1. En la canalización, seleccione la pestaña **Variables**.
1. En **Variables de canalización**, agregue la variable anterior con sus valores.
1. Seleccione **Guardar** para guardar las variables.

### <a name="add-pipeline-tasks"></a>Agregar tareas de canalización

Una tarea de canalización es un script empaquetado previamente que realiza una acción. Agregue una tarea que llame al script *DeployToB2C.ps1* de PowerShell.

1. En la canalización que creó, seleccione la pestaña **Tareas**.
1. Seleccione **Trabajo de agente** y, a continuación, seleccione el signo más ( **+** ) para agregar una tarea al trabajo del agente.
1. Busque y seleccione **PowerShell**. No seleccione "Azure PowerShell", "PowerShell en equipos de destino" ni otra entrada de PowerShell.
1. Seleccione la tarea **Script de PowerShell** recién agregada.
1. Escriba los siguientes valores para la tarea Script de PowerShell:
    * **Versión de la tarea**: 2.*
    * **Nombre para mostrar**: El nombre de la directiva que debe cargar esta tarea. Por ejemplo, *B2C_1A_TrustFrameworkBase*.
    * **Tipo**: Ruta del archivo
    * **Ruta de acceso del script**: Seleccione los puntos suspensivos (**_..._* _), vaya a la carpeta _Scripts* y, a continuación, seleccione el archivo *DeployToB2C.ps1*.
    * **Argumentos**:

        Escriba los siguientes valores para **Argumentos**. Reemplace `{alias-name}` por el alias que especificó en la sección anterior. Reemplace `{policy-id}` por el nombre de la directiva. Reemplace `{policy-file-name}` por el nombre de archivo de la directiva.

        La primera directiva en cargarse debe ser *TrustFrameworkBase.xml*.

        ```PowerShell
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId {policy-id} -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/{policy-file-name}
        ```

        `PolicyId` es un valor que se encuentra al principio de un archivo de directiva XML dentro del nodo TrustFrameworkPolicy. Por ejemplo, `PolicyId` en el siguiente XML de directiva es *B2C_1A_TrustFrameworkBase*:

        ```xml
        <TrustFrameworkPolicy
        xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xmlns:xsd="http://www.w3.org/2001/XMLSchema"
        xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
        PolicySchemaVersion="0.3.0.0"
        TenantId="your-tenant.onmicrosoft.com"
        PolicyId= "B2C_1A_TrustFrameworkBase"
        PublicPolicyUri="http://your-tenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
        ```

        Los argumentos finales deben tener un aspecto similar al del siguiente ejemplo:

        ```PowerShell
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Seleccione **Guardar** para guardar el trabajo de agente.

## <a name="test-your-pipeline"></a>Prueba de la canalización

Para probar la canalización de versión:

1. Seleccione **Canalizaciones** y luego **Versiones**.
1. Seleccione la canalización que creó anteriormente, por ejemplo *DeployCustomPolicies*.
1. Seleccione **Crear versión** y, a continuación, seleccione **Crear** para poner en cola la versión.

Debería ver un banner de notificación que indica que una versión se ha puesto en cola. Para ver su estado, seleccione el vínculo en el banner de notificación o selecciónelo en la lista de la pestaña **Versiones**.

## <a name="add-more-pipeline-tasks"></a>Incorporación de tareas de canalización adicionales

Para implementar el resto de las directivas, repita los [pasos anteriores](#add-pipeline-tasks) para cada uno de los archivos de directiva personalizados.

Al ejecutar los agentes y cargar los archivos de directivas, asegúrese de que se cargan en el orden correcto:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

## <a name="next-steps"></a>Pasos siguientes

Más información sobre:

* [Llamadas entre servicios mediante las credenciales del cliente](../active-directory/develop/v2-oauth2-client-creds-grant-flow.md)
* [Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
