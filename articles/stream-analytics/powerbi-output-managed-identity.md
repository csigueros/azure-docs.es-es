---
title: Uso de la identidad administrada para autenticar el trabajo de Azure Stream Analytics en la salida de Power BI
description: En este artículo se describe cómo usar las identidades administradas para autenticar el trabajo de Azure Stream Analytics en la salida de Power BI.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: how-to
ms.date: 05/30/2021
ms.openlocfilehash: f0dfc7c77ce0eeedc6a85760627988e3eddc838e
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110784925"
---
# <a name="use-managed-identity-to-authenticate-your-azure-stream-analytics-job-to-power-bi"></a>Uso de la identidad administrada para autenticar el trabajo de Azure Stream Analytics en Power BI

[Autenticación de identidad administrada](../active-directory/managed-identities-azure-resources/overview.md) para la salida a Power BI proporciona a los trabajos de Stream Analytics acceso directo a un área de trabajo dentro de la cuenta de Power BI. Esta característica permite que las implementaciones de los trabajos de Stream Analytics se automaticen de manera completa, dado que ya no es necesario que un usuario inicie sesión de forma interactiva en Power BI a través de Azure Portal. Además, los trabajos de larga duración que escriben en Power BI ahora se admiten mejor, ya que no será necesario volver a autorizar el trabajo de forma periódica.

En este artículo se muestra cómo habilitar la identidad administrada para las salidas a Power BI de un trabajo de Stream Analytics a través de Azure Portal y de una implementación de Azure Resource Manager.

## <a name="prerequisites"></a>Prerrequisitos

Para usar esta característica se requiere lo siguiente:

- Una cuenta de Power BI con una [licencia Pro](/power-bi/service-admin-purchasing-power-bi-pro).

- Un área de trabajo actualizada dentro de la cuenta de Power BI. Para más información, vea el [anuncio de Power BI](https://powerbi.microsoft.com/blog/announcing-new-workspace-experience-general-availability-ga/) de esta característica.

## <a name="create-a-stream-analytics-job-using-the-azure-portal"></a>Creación de un trabajo de Stream Analytics mediante Azure Portal

1. Cree un nuevo trabajo de Stream Analytics o abra un trabajo existente en Azure Portal. En la barra de menús situada a la izquierda de la pantalla, seleccione **Identidad administrada**, que se encuentra en **Configurar**. Asegúrese de que está seleccionada la opción "Usar la identidad administrada asignada por el sistema" y, después, seleccione el botón **Guardar** en la parte inferior de la pantalla.

   ![Configuración de la Identidad administrada de Stream Analytics](./media/common/stream-analytics-enable-managed-identity.png)

2. Antes de configurar la salida, debe asignar al trabajo de Stream Analytics acceso al área de trabajo de Power BI; para ello, siga las instrucciones de la sección [Concesión de acceso al trabajo de Stream Analytics al área de trabajo de Power BI](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) de este artículo.

3. Vaya a la sección **Salidas** del trabajo de Stream Analytics, seleccione **+ Agregar** y luego **Power BI**. Después, seleccione el botón **Autorizar** e inicie sesión con la cuenta de Power BI.

   ![Autorización con la cuenta de Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-authorize-powerbi.png)

4. Una vez que esté autorizado, se rellenará una lista desplegable con todas las áreas de trabajo a las que tenga acceso. Seleccione el área de trabajo que haya autorizado en el paso anterior. Después, seleccione **Identidad administrada** en la lista "Autenticación". Por último, seleccione el botón **Guardar**.

   ![Configuración de la salida de Power BI con la identidad administrada](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-configure-powerbi-with-managed-id.png)

## <a name="azure-resource-manager-deployment"></a>Implementación de Azure Resource Manager

Azure Resource Manager permite automatizar completamente la implementación del trabajo de Stream Analytics. Puede implementar las plantillas de Resource Manager mediante Azure PowerShell o la [CLI de Azure.](/cli/azure/) Los ejemplos siguientes usan la CLI de Azure.


1. Para crear un recurso **Microsoft.StreamAnalytics/streamingjobs** con una identidad administrada, puede incluir la siguiente propiedad en la sección de recursos de la plantilla de Resource Manager:

    ```json
    "identity": {
        "type": "SystemAssigned",
    }
    ```

   Esta propiedad indica a Azure Resource Manager que cree y administre la identidad del trabajo de Stream Analytics. A continuación, se muestra un ejemplo de una plantilla de Resource Manager que implementa un trabajo de Stream Analytics con la identidad administrada habilitada y un receptor de salida de Power BI que usa la identidad administrada:

    ```json
    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "resources": [
            {
                "apiVersion": "2017-04-01-preview",
                "name": "pbi_managed_id",
                "location": "[resourceGroup().location]",
                "type": "Microsoft.StreamAnalytics/StreamingJobs",
                "identity": {
                    "type": "systemAssigned"
                },
                "properties": {
                    "sku": {
                        "name": "standard"
                    },
                    "outputs":[
                        {
                            "name":"output",
                            "properties":{
                                "datasource":{
                                    "type":"PowerBI",
                                    "properties":{
                                        "dataset": "dataset_name",
                                        "table": "table_name",
                                        "groupId": "01234567-89ab-cdef-0123-456789abcdef",
                                        "authenticationMode": "Msi"
                                    }
                                }
                            }
                        }
                    ]
                }
            }
        ]
    }
    ```

    Implemente el trabajo anterior en el grupo de recursos **ExampleGroup** mediante el siguiente comando de la CLI de Azure:

    ```azurecli
    az deployment group create --resource-group ExampleGroup -template-file StreamingJob.json
    ```

2. Una vez creado el trabajo, use Azure Resource Manager para recuperar la definición completa del trabajo.

    ```azurecli
    az resource show --ids /subscriptions/<subsription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/StreamingJobs/<resource-name>
    ```

    El comando anterior devolverá una respuesta como la siguiente:

    ```json
    {
        "id": "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.StreamAnalytics/streamingjobs/<resource-name>",
        "identity": {
            "principalId": "<principal-id>",
            "tenantId": "<tenant-id>",
            "type": "SystemAssigned",
            "userAssignedIdentities": null
        },
        "kind": null,
        "location": "West US",
        "managedBy": null,
        "name": "<resource-name>",
        "plan": null,
        "properties": {
            "compatibilityLevel": "1.0",
            "createdDate": "2019-07-12T03:11:30.39Z",
            "dataLocale": "en-US",
            "eventsLateArrivalMaxDelayInSeconds": 5,
            "jobId": "<job-id>",
            "jobState": "Created",
            "jobStorageAccount": null,
            "jobType": "Cloud",
            "outputErrorPolicy": "Stop",
            "package": null,
            "provisioningState": "Succeeded",
            "sku": {
                "name": "Standard"
            }
        },
        "resourceGroup": "<resource-group>",
        "sku": null,
        "tags": null,
        "type": "Microsoft.StreamAnalytics/streamingjobs"
    }
    ```

    Si tiene previsto usar la API REST de Power BI para agregar el trabajo de Stream Analytics al área de trabajo de Power BI, anote el valor "principalId" devuelto.

3. Ahora que se ha creado el trabajo, continúe a la sección [Concesión de acceso al trabajo de Stream Analytics al área de trabajo de Power BI](#give-the-stream-analytics-job-access-to-your-power-bi-workspace) de este artículo.


## <a name="give-the-stream-analytics-job-access-to-your-power-bi-workspace"></a>Concesión de acceso al trabajo de Stream Analytics al área de trabajo de Power BI

Ahora que se ha creado el trabajo de Stream Analytics, se le puede conceder acceso a un área de trabajo de Power BI. Una vez que haya dado acceso a su trabajo, espere unos minutos a que se propague la identidad.

### <a name="use-the-power-bi-ui"></a>Uso de la interfaz de usuario de Power BI

   > [!Note]
   > Para agregar el trabajo de Stream Analytics al área de trabajo de Power BI mediante la interfaz de usuario, también debe habilitar el acceso de la entidad de servicio en **Configuración de desarrollador** en el portal de administración de Power BI. Vea [Introducción a una entidad de servicio](/power-bi/developer/embed-service-principal) para obtener más detalles.

1. Vaya a la configuración de acceso del área de trabajo. Vea este artículo para obtener más información: [Concesión de acceso al área de trabajo](/power-bi/service-create-the-new-workspaces#give-access-to-your-workspace).

2. Escriba el nombre del trabajo de Stream Analytics en el cuadro de texto y seleccione **Colaborador** como nivel de acceso.

3. Seleccione **Agregar** y cierre el panel.

   ![Adición del trabajo de Stream Analytics al área de trabajo de Power BI](./media/stream-analytics-powerbi-output-managed-identity/stream-analytics-add-job-to-powerbi-workspace.png)

### <a name="use-the-power-bi-powershell-cmdlets"></a>Uso de los cmdlets de PowerShell de Power BI

1. Instale los cmdlets de PowerShell `MicrosoftPowerBIMgmt` de Power BI.

   > [!Important]
   > Asegúrese de que usa la versión 1.0.821 o posterior de los cmdlets.

```powershell
Install-Module -Name MicrosoftPowerBIMgmt
```

2. Inicie sesión en Power BI.

```powershell
Login-PowerBI
```

3. Agregue el trabajo de Stream Analytics como colaborador en el área de trabajo.

```powershell
Add-PowerBIWorkspaceUser -WorkspaceId <group-id> -PrincipalId <principal-id> -PrincipalType App -AccessRight Contributor
```

### <a name="use-the-power-bi-rest-api"></a>Uso de la API REST de Power BI

El trabajo de Stream Analytics también se puede agregar como Colaborador al área de trabajo si se usa directamente la API REST "Agregar usuario de grupo". La documentación completa para esta API se puede encontrar aquí: [Grupos: agregar usuario de grupo](/rest/api/power-bi/groups/addgroupuser).

**Solicitud de ejemplo**
```http
POST https://api.powerbi.com/v1.0/myorg/groups/{groupId}/users
```
Cuerpo de la solicitud
```json
{
    "groupUserAccessRight": "Contributor",
    "identifier": "<principal-id>",
    "principalType": "App"
}
```

### <a name="use-a-service-principal-to-grant-permission-for-an-asa-jobs-managed-identity"></a>Uso de una entidad de servicio para conceder permiso para la identidad administrada de un trabajo de ASA

En el caso de las implementaciones automatizadas, no es posible usar un inicio de sesión interactivo para proporcionar a un trabajo de ASA acceso un área de trabajo de Power BI. Esto se puede hacer mediante una entidad de servicio para conceder permiso para la identidad administrada de un trabajo de ASA. Esto es posible mediante PowerShell:

Connect-PowerBIServiceAccount -ServicePrincipal -TenantId "<id-inquilino>" -CertificateThumbprint "<thumbprint>" -ApplicationId "<id-aplicación>" Add-PowerBIWorkspaceUser -WorkspaceId <id-grupo> -PrincipalId <id-entidad-seguridad> -PrincipalType App -AccessRight Contributor


## <a name="remove-managed-identity"></a>Eliminación de una identidad administrada

La identidad administrada creada para un trabajo de Stream Analytics se elimina solo cuando se elimina el trabajo. No hay ninguna manera de eliminar la identidad administrada sin eliminar el trabajo. Si ya no va a usar la identidad administrada, puede cambiar el método de autenticación de la salida. La identidad administrada seguirá existiendo hasta que se elimine el trabajo y se utilizará si decide usar de nuevo la autenticación de identidad administrada.

## <a name="limitations"></a>Limitaciones
A continuación se muestran las limitaciones de esta característica:

- No se admiten las áreas de trabajo clásicas de Power BI.

- Cuentas de Azure sin Azure Active Directory.

- No se admite el acceso multiinquilino. La entidad de servicio creada para un trabajo determinado de Stream Analytics debe residir en el mismo inquilino de Azure Active Directory en el que se creó el trabajo, y no se puede usar en un recurso que resida en un inquilino de Azure Active Directory diferente.

- La [identidad asignada por el usuario](../active-directory/managed-identities-azure-resources/overview.md) no se admite. Esto significa que no puede especificar una entidad de servicio propia para que la use el trabajo de Stream Analytics. Azure Stream Analytics debe generar la entidad de servicio.

## <a name="next-steps"></a>Pasos siguientes

* [Integración del panel de Power BI con Azure Stream Analytics](./stream-analytics-power-bi-dashboard.md)
* [Información sobre las salidas desde Azure Stream Analytics](./stream-analytics-define-outputs.md)
