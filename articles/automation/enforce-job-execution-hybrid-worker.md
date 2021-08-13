---
title: Control de la ejecución de trabajos en Azure Automation Hybrid Runbook Worker
description: En este artículo se explica cómo usar una definición de Azure Policy personalizada para exigir la ejecución de trabajos en una instancia de Azure Automation Hybrid Runbook Worker.
services: automation
ms.subservice: process-automation
ms.date: 05/24/2021
ms.topic: conceptual
ms.openlocfilehash: 36ead3f16a04055e7056c702b0600265dd8e61ce
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110483587"
---
# <a name="use-azure-policy-to-enforce-job-execution-on-hybrid-runbook-worker"></a>Uso de Azure Policy para exigir la ejecución de trabajos en Hybrid Runbook Worker

Al iniciar un runbook en una instancia de Hybrid Runbook Worker se usa la opción **Ejecutar en** que permite especificar el nombre de un grupo de Hybrid Runbook Worker al iniciarse desde Azure Portal, con Azure PowerShell o la API REST. Si se especifica un grupo, uno de los roles de trabajo del grupo recupera y ejecuta el runbook. Si el runbook no especifica esta opción, Azure Automation ejecuta el runbook en el espacio aislado de Azure. 

Cualquier persona de su organización que sea miembro del [Operador de trabajos de Automation](automation-role-based-access-control.md#automation-job-operator) o superior puede crear trabajos de runbook. Para administrar la ejecución de runbooks dirigida a un grupo de Hybrid Runbook Worker en la cuenta de Automation, puede usar [Azure Policy](../governance/policy/overview.md). Esto ayuda a aplicar los estándares organizativos y a garantizar que los trabajos de automatización están controlados y administrados por las personas designadas. Además, se asegura de que nadie pueda ejecutar un runbook en un espacio aislado de Azure, solo en Hybrid Runbook Worker. 

En este artículo se incluye una definición de Azure Policy personalizada para ayudarle a controlar estas actividades mediante las siguientes operaciones de la API REST de Automation. Concretamente:

* [Crear trabajo](/rest/api/automation/job/create)
* [Create job schedule](/rest/api/automation/jobschedule/create)
* [Create webhook](/rest/api/automation/webhook/createorupdate)

Esta directiva se basa en la propiedad `runOn`. La directiva valida el valor de la propiedad, que debe contener el nombre de un grupo de Hybrid Runbook Worker existente. Si el valor es NULL, se interpreta como si la solicitud de creación del trabajo, programación del trabajo o webhook estuviera dirigida al espacio aislado de Azure y se deniega la solicitud.

## <a name="permissions-required"></a>Permisos necesarios

Debe ser miembro del rol [Propietario](../role-based-access-control/built-in-roles.md#owner) en el nivel de suscripción a fin de tener los permisos para acceder a recursos de Azure Policy.

## <a name="create-and-assign-the-policy-definition"></a>Creación y asignación de la definición de directiva

En este punto se crea la regla de directiva y, a continuación, se asigna a un grupo de administración o una suscripción y, opcionalmente, se especifica un grupo de recursos en la suscripción. Si aún no está familiarizado con el lenguaje de directiva, consulte la [estructura de definición de directivas](../governance/policy/concepts/definition-structure.md) para saber cómo estructurar la definición de directiva.

1. Utilice el siguiente fragmento de código JSON para crear un archivo JSON con el nombre AuditAutomationHRWJobExecution.json.

    ```json
    {
        "properties": {
            "displayName": "Enforce job execution on Automation Hybrid Runbook Worker",
            "description": "Enforce job execution on Hybrid Runbook Workers in your Automation account.",
            "mode": "all",
            "parameters": {
                "effectType": {
                    "type": "string",
                    "defaultValue": "Deny",
                    "allowedValues": [
                       "Deny",
                       "Disabled"
                    ],
                    "metadata": {
                        "displayName": "Effect",
                        "description": "Enable or disable execution of the policy"
                    }
                }
            },
            "policyRule": {
          "if": {
            "anyOf": [
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobs"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/webhooks"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/webhooks/runOn'))]",
                    "less": 1
                  }
                ]
              },
              {
                "allOf": [
                  {
                    "field": "type",
                    "equals": "Microsoft.Automation/automationAccounts/jobSchedules"
                  },
                  {
                    "value": "[length(field('Microsoft.Automation/automationAccounts/jobSchedules/runOn'))]",
                    "less": 1
                  }
                ]
              }
            ]
          },
          "then": {
            "effect": "[parameters('effectType')]"
          }
        }
      }
    }
    ```

2. Ejecute el siguiente comando de Azure PowerShell o la CLI de Azure para crear una definición de directiva mediante el archivo AuditAutomationHRWJobExecution.json.

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

    ```azurecli
    az policy definition create --name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' --display-name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' --description 'This policy enforces job execution on Automation account user Hybrid Runbook Workers.' --rules 'AuditAutomationHRWJobExecution.json' --mode All
    ```

    El comando crea una definición de directiva denominada **Audit Enforce Jobs on Automation Hybrid Runbook Workers** (auditar la aplicación de trabajos en Automation Hybrid Runbook Workers). Para más información acerca de otros parámetros que puede utilizar, consulte [creación de la definición de directivas az](/cli/azure/policy/definition#az_policy_definition_create).

    Cuando se llama sin parámetros de ubicación, `az policy definition create` elige de forma predeterminada guardar la definición de directiva en la suscripción seleccionada del contexto de sesiones. Para guardar la definición en una ubicación diferente, use los siguientes parámetros:

    * **subscription**: se guarda en otra suscripción. Requiere un valor de *GUID* como identificador de suscripción o un valor de *cadena* como nombre.
    * **management-group**: se guarda en un grupo de administración. Requiere un valor de *cadena*.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -DisplayName 'Audit Enforce Jobs on Automation Hybrid Runbook Workers' -Policy 'AuditAutomationHRWJobExecution.json'
    ```

    El comando crea una definición de directiva denominada **Audit Enforce Jobs on Automation Hybrid Runbook Workers** (auditar la aplicación de trabajos en Automation Hybrid Runbook Workers). Para más información acerca de otros parámetros que puede utilizar, consulte [New-AzPolicyDefinition](/powershell/module/az.resources/new-azpolicydefinition).

    Cuando se llama sin parámetros de ubicación, `New-AzPolicyDefinition` elige de forma predeterminada guardar la definición de directiva en la suscripción seleccionada del contexto de sesiones. Para guardar la definición en una ubicación diferente, use los siguientes parámetros:

    * **SubscriptionId**: se guarda en una suscripción diferente. Requiere un valor de *GUID*.
    * **ManagementGroupName**: se guarda en un grupo de administración. Requiere un valor de *cadena*.

    ---

3. Después de crear la definición de directiva, puede crear una asignación de directiva mediante la ejecución de los siguientes comandos:

    # <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

    ```azurecli
   az policy assignment create --name '<name>' --scope '<scope>' --policy '<policy definition ID>'
   ```

   El parámetro **scope** de `az policy assignment create` funciona con un grupo de administración, una suscripción, un grupo de recursos o un único recurso. El parámetro utiliza una ruta de acceso de recurso completa. El patrón de **scope** para cada contenedor es el siguiente. Reemplace `{rName}`, `{rgName}`, `{subId}` y `{mgName}` por el nombre del recurso, el nombre del grupo de recursos, el identificador de suscripción y el nombre del grupo de administración, respectivamente. `{rType}` se reemplazaría por el **tipo de recurso** del recurso como, por ejemplo, `Microsoft.Compute/virtualMachines` para una máquina virtual.

   - Recurso `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
   - Grupo de recursos `/subscriptions/{subID}/resourceGroups/{rgName}`
   - Suscripción `/subscriptions/{subID}`
   - Grupo de administración `/providers/Microsoft.Management/managementGroups/{mgName}`

    Puede obtener el id. de definición de Azure Policy si usa PowerShell con el comando siguiente:
    
    ```azurecli
    az policy definition show --name 'Audit Enforce Jobs on Automation Hybrid Runbook Workers'
    ```
    
    El identificador de definición de directiva para la definición de directiva que ha creado debe parecerse al ejemplo siguiente:
    
    ```output
    "/subscription/<subscriptionId>/providers/Microsoft.Authorization/policyDefinitions/Audit Enforce Jobs on Automation Hybrid Runbook Workers"
    ```

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    ```azurepowershell
    $rgName = Get-AzResourceGroup -Name 'ContosoRG'
    $Policy = Get-AzPolicyDefinition -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers'
    New-AzPolicyAssignment -Name 'audit-enforce-jobs-on-automation-hybrid-runbook-workers' -PolicyDefinition $Policy -Scope $rg.ResourceId
    ```

    Reemplace _ContosoRG_ por el nombre del grupo de recursos que desee.

    El parámetro **Scope** de `New-AzPolicyAssignment` funciona con un grupo de administración, una suscripción, un grupo de recursos o un único recurso. El parámetro utiliza una ruta de acceso de recurso completo, que devuelve la propiedad **ResourceId** en `Get-AzResourceGroup`. El patrón de **Scope** para cada contenedor es como sigue. Reemplace `{rName}`, `{rgName}`, `{subId}` y `{mgName}` por el nombre del recurso, el nombre del grupo de recursos, el identificador de suscripción y el nombre del grupo de administración, respectivamente.
    `{rType}` se reemplazaría por el **tipo de recurso** del recurso como, por ejemplo, `Microsoft.Compute/virtualMachines` para una máquina virtual.

    - Recurso `/subscriptions/{subID}/resourceGroups/{rgName}/providers/{rType}/{rName}`
    - Grupo de recursos `/subscriptions/{subId}/resourceGroups/{rgName}`
    - Suscripción `/subscriptions/{subId}`
    - Grupo de administración `/providers/Microsoft.Management/managementGroups/{mgName}`

    ---

4. Inicie sesión en [Azure Portal](https://portal.azure.com).
5. Inicie el servicio Azure Policy en Azure Portal. Para ello, seleccione **Todos los servicios** y, a continuación, busque y seleccione **Directiva**.
6. Seleccione **Cumplimiento** en el panel izquierdo de la página. A continuación, busque la asignación de directiva que creó.

   :::image type="content" source="./media/enforce-job-execution-hybrid-worker/azure-policy-dashboard-policy-status.png" alt-text="Captura de pantalla del panel de información de Azure Policy.":::

Cuando una de las operaciones REST de Automation se ejecuta sin hacer referencia a una instancia de Hybrid Runbook Worker en el cuerpo de la solicitud, se devuelve un código de respuesta 403 con un error similar al ejemplo siguiente que indica que la operación intentó ejecutarse en un espacio aislado de Azure:

```rest
{
  "error": {
    "code": "RequestDisallowedByPolicy",
    "target": "Start_VMS",
    "message": "Resource 'Start_VMS' was disallowed by policy. Policy identifiers: '[{\"policyAssignment\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917\"},\"policyDefinition\":{\"name\":\"Enforce Jobs on Automation Hybrid Runbook Workers\",\"id\":\"/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc\"}}]'.",
    "additionalInfo": [
      {
        "type": "PolicyViolation",
        "info": {
          "policyDefinitionDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "evaluationDetails": {
            "evaluatedExpressions": [
              {
                "result": "True",
                "expressionKind": "Field",
                "expression": "type",
                "path": "type",
                "expressionValue": "Microsoft.Automation/automationAccounts/jobs",
                "targetValue": "Microsoft.Automation/automationAccounts/jobs",
                "operator": "Equals"
              },
              {
                "result": "True",
                "expressionKind": "Value",
                "expression": "[length(field('Microsoft.Automation/automationAccounts/jobs/runOn'))]",
                "expressionValue": 0,
                "targetValue": 1,
                "operator": "Less"
              }
            ]
          },
          "policyDefinitionId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/providers/Microsoft.Authorization/policyDefinitions/4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionName": "4fdffd35-fd9f-458e-9779-94fe33401bfc",
          "policyDefinitionEffect": "Deny",
          "policyAssignmentId": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG/providers/Microsoft.Authorization/policyAssignments/fd5e2cb3842d4eefbc857917",
          "policyAssignmentName": "fd5e2cb3842d4eefbc857917",
          "policyAssignmentDisplayName": "Enforce Jobs on Automation Hybrid Runbook Workers",
          "policyAssignmentScope": "/subscriptions/75475e1e-9643-4f3d-859e-055f4c31b458/resourceGroups/MAIC-RG",
          "policyAssignmentParameters": {}
        }
      }
    ]
  }
}
```

La operación que se intentó realizar también se agrega en el registro de actividad de la cuenta de Automation, de manera similar al ejemplo siguiente.

:::image type="content" source="./media/enforce-job-execution-hybrid-worker/failed-job-activity-log-example.png" alt-text="Ejemplo de registro de actividad para la ejecución de trabajos con errores.":::

## <a name="next-steps"></a>Pasos siguientes

Para trabajar con runbooks, consulte [Administración de runbooks en Azure Automation](manage-runbooks.md).