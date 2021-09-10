---
title: Autenticación de Azure AD para los registros
description: Aprenda a habilitar la autenticación de Azure Active Directory (Azure AD) para Log Analytics en Azure Monitor.
ms.topic: conceptual
ms.date: 08/24/2021
ms.openlocfilehash: 526312df5c1bbf5e21a4017177752fb4f476ae5c
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122868710"
---
# <a name="azure-ad-authentication-for-logs"></a>Autenticación de Azure AD para los registros

Azure Monitor puede [recopilar datos en registros de varios orígenes](data-platform-logs.md#data-collection), incluidos agentes en máquinas virtuales, Application Insights, configuración de diagnóstico para recursos de Azure y API de recopilador de datos.

Los agentes de Log Analytics usan una clave de área de trabajo como clave de inscripción para comprobar el acceso inicial y aprovisionar un certificado que se usa para establecer una conexión segura entre el agente y Azure Monitor. Para obtener más información, consulte [Envío de datos desde agentes](data-security.md#2-send-data-from-agents). API de recopilador de datos usa la misma clave de área de trabajo para [autorizar el acceso](data-collector-api.md#authorization).

Estas opciones pueden ser complicadas y suponer un riesgo, ya que es difícil administrar credenciales específicamente, claves de área de trabajo a gran escala. Puede optar por no participar en la autenticación local y asegurarse de que solo los datos de telemetría que se autentican exclusivamente mediante identidades administradas y Azure Active Directory se ingieren en Azure Monitor. Esta característica mejora la seguridad y confiabilidad de la telemetría utilizada para tomar decisiones operativas y empresariales críticas.

Siga estos pasos para habilitar la integración de Azure Active Directory para los registros de Azure Monitor y quitar la dependencia de estos secretos compartidos.

1. El agente de Azure Monitor (AMA) no requiere ninguna clave, pero en su lugar [requiere una identidad administrada asignada por el sistema](../agents/azure-monitor-agent-overview.md#security). [Migre al agente de Azure Monitor](../agents/azure-monitor-agent-migration.md) desde los agentes de Log Analytics.
2. [Deshabilite la autenticación local para las áreas de trabajo de Log Analytics](#disable-local-authentication-for-log-analytics).
3. Asegúrese de que solo se ingiera telemetría autenticada en los recursos de Application Insights mediante la [Autenticación de Azure AD para Application Insights (versión preliminar)](../app/azure-ad-authentication.md).

## <a name="disable-local-authentication-for-log-analytics"></a>Deshabilitación de la autenticación local para Log Analytics

Después de quitar su dependencia del agente de Log Analytics, puede optar por deshabilitar la autenticación local para las áreas de trabajo de Log Analytics. Esto le permitirá ingerir y consultar la telemetría autenticada exclusivamente por Azure AD.

Deshabilitar la autenticación local puede limitar algunas funcionalidades disponibles, en concreto:

- Los agentes de Log Analytics existentes dejarán de funcionar, solo se admite el agente de Azure Monitor (AMA). El agente de Azure Monitor no tiene algunas funcionalidades disponibles a través del agente de Log Analytics (por ejemplo, la recopilación de registros personalizada o la recopilación de registros de IIS).
- API de recopilador de datos (versión preliminar) no admite la autenticación de Azure AD y no estará disponible para la ingesta de datos.

Puede deshabilitar la autenticación local mediante Azure Policy o mediante programación a través de la plantilla de Azure Resource Manager, PowerShell o la CLI.

### <a name="azure-policy"></a>Azure Policy

Azure Policy para "DisableLocalAuth" denegará a los usuarios la creación de un área de trabajo de Log Analytics sin esta configuración de propiedad en "true". El nombre de la directiva es `Log Analytics Workspaces should block non-Azure Active Directory based ingestion`. Para aplicar esta definición de directiva a la suscripción, [cree una nueva asignación de directiva y asigne la directiva](../../governance/policy/assign-policy-portal.md). 

A continuación se muestra la definición de la plantilla de directiva:

```json
{
  "properties": {
    "displayName": "Log Analytics Workspaces should block non-Azure Active Directory based ingestion.",
    "policyType": "BuiltIn",
    "mode": "Indexed",
    "description": "Enforcing log ingestion to require Azure Active Directory authentication prevents unauthenticated logs from an attacker which could lead to incorrect status, false alerts, and incorrect logs stored in the system.",
    "metadata": {
      "version": "1.0.0",
      "category": "Monitoring"
    },
    "parameters": {
      "effect": {
        "type": "String",
        "metadata": {
          "displayName": "Effect",
          "description": "Enable or disable the execution of the policy"
        },
        "allowedValues": [
          "Deny",
          "Audit",
          "Disabled"
        ],
        "defaultValue": "Audit"
      }
    },
    "policyRule": {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.OperationalInsights/workspaces"
          },
          {
            "field": "Microsoft.OperationalInsights/workspaces/features.disableLocalAuth",
            "notEquals": "true"
          }
        ]
      },
      "then": {
        "effect": "[parameters('effect')]"
      }
    }
  },
  "id": "/providers/Microsoft.Authorization/policyDefinitions/e15effd4-2278-4c65-a0da-4d6f6d1890e2",
  "type": "Microsoft.Authorization/policyDefinitions",
  "name": "e15effd4-2278-4c65-a0da-4d6f6d1890e2"
}
```

### <a name="azure-resource-manager"></a>Azure Resource Manager

La propiedad `DisableLocalAuth` se usa para deshabilitar cualquier autenticación local en el área de trabajo de Log Analytics. Cuando se establece en true, esta propiedad exige que la autenticación de Azure AD se use para todos los accesos. 

A continuación se muestra una plantilla de Azure Resource Manager de ejemplo que puede usar para deshabilitar la autenticación local:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "workspaces_name": {
            "defaultValue": "workspace-name",
            "type": "string"
        },
        "workspace_location": {
          "defaultValue": "region-name",
          "type": "string"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.OperationalInsights/workspaces",
            "apiVersion": "2020-08-01",
            "name": "[parameters('workspaces_name')]",
            "location": "[parameters('workspace_location')]",
            "properties": {
                 "sku": {
                    "name": "PerGB2018"
                },
                "retentionInDays": 30,
                "features": {
                    "disableLocalAuth": false,
                    "enableLogAccessUsingOnlyResourcePermissions": true
                }
            }
        }
    ]
}

```


### <a name="cli"></a>CLI

La propiedad `DisableLocalAuth` se usa para deshabilitar cualquier autenticación local en el área de trabajo de Log Analytics. Cuando se establece en true, esta propiedad exige que la autenticación de Azure AD se use para todos los accesos. 

A continuación se muestra un ejemplo de comandos de la CLI que puede usar para deshabilitar la autenticación local:

```azurecli
    az resource update --ids "/subscriptions/[Your subscription ID]/resourcegroups/[Your resource group]/providers/microsoft.operationalinsights/workspaces/[Your workspace name]--api-version "2021-06-01" --set properties.features.disableLocalAuth=True
```

### <a name="powershell"></a>PowerShell

La propiedad `DisableLocalAuth` se usa para deshabilitar cualquier autenticación local en el área de trabajo de Log Analytics. Cuando se establece en true, esta propiedad exige que la autenticación de Azure AD se use para todos los accesos. 

A continuación se muestra un ejemplo de comandos de PowerShell que puede usar para deshabilitar la autenticación local:

```powershell
    $workspaceSubscriptionId = "[You subscription ID]"
    $workspaceResourceGroup = "[You resource group]"
    $workspaceName = "[Your workspace name]"
    $disableLocalAuth = $false
    
    # login
    Connect-AzAccount
    
    # select subscription
    Select-AzSubscription -SubscriptionId $workspaceSubscriptionId
    
    # get private link workspace resource
    $workspace = Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ResourceGroupName $workspaceResourceGroup -ResourceName $workspaceName -ApiVersion "2021-06-01"
    
    # set DisableLocalAuth
    $workspace.Properties.Features | Add-Member -MemberType NoteProperty -Name DisableLocalAuth -Value $disableLocalAuth -Force
    $workspace | Set-AzResource -Force
```

## <a name="next-steps"></a>Pasos siguientes
* [Autenticación de Azure Active Directory para Application Insights (versión preliminar)](../app/azure-ad-authentication.md)