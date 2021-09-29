---
title: Traslado de un área de trabajo de Log Analytics a otra región de Azure mediante Azure Portal
description: Use una plantilla de Azure Resource Manager para trasladar un área de trabajo de Log Analytics de una región de Azure a otra mediante Azure Portal.
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: cecfa72493545f40c2e7326fece88cb85f99974b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128565420"
---
# <a name="move-a-log-analytics-workspace-to-another-region-by-using-the-azure-portal"></a>Traslado de un área de trabajo de Log Analytics a otra región mediante Azure Portal

Existen varios escenarios en los que puede que le interese trasladar su área de trabajo de Log Analytics existente de una región a otra. Por ejemplo, Log Analytics está disponible desde hace poco tiempo en una región que hospeda la mayoría de sus recursos y desea que el área de trabajo esté más cerca y ahorrarse los gastos de salida. También es posible que quiera mover su área de trabajo a una región recién agregada por requisito de la soberanía de datos.

Un área de trabajo de Log Analytics no se puede mover de una región a otra. Pero se puede usar una plantilla de Azure Resource Manager para exportar el recurso de área de trabajo y los recursos relacionados. Después, los recursos se pueden almacenar provisionalmente en otra región; para ello, es preciso exportar el área de trabajo a una plantilla, modificar los parámetros para que coincidan con los de la región de destino y, luego, implementar la plantilla en la nueva región. Para más información sobre Resource Manager y las plantillas, consulte [Inicio rápido: Creación e implementación de plantillas de Azure Resource Manager mediante Azure Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). 

El entorno del área de trabajo puede ser complejo e incluir orígenes conectados, soluciones administradas, servicios vinculados, alertas y paquetes de consultas. No todos los recursos se pueden exportar en una plantilla de Resource Manager, algunos de ellos requerirán una configuración independiente al trasladar a un área de trabajo.

## <a name="prerequisites"></a>Requisitos previos

- Para exportar la configuración del área de trabajo a una plantilla que se pueda implementar en otra región, necesita los roles [Colaborador de Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) o [Colaborador de supervisión](../../role-based-access-control/built-in-roles.md#monitoring-contributor), u otros roles superiores.

- Identifique todos los recursos que estén asociados actualmente al área de trabajo, por ejemplo:
  - *Agentes conectados*: escriba **Registros** en el área de trabajo y consulte la tabla de [latidos](../insights/solution-agenthealth.md#azure-monitor-log-records) para enumerar los agentes conectados.
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - *Configuración de diagnóstico*: los recursos pueden enviar registros a Azure Diagnostics o a tablas dedicadas del área de trabajo. Escriba **Registros** en el área de trabajo y ejecute esta consulta para los recursos que envían datos a la tabla `AzureDiagnostics`:

    ```kusto
    AzureDiagnostics
    | where TimeGenerated > ago(12h)
    | summarize by  ResourceProvider , ResourceType, Resource
    | sort by ResourceProvider, ResourceType
    ```

    Ejecute esta consulta para los recursos que envían datos a tablas dedicadas:

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | where isnotnull(_ResourceId)
    | extend ResourceProvider = split(_ResourceId, '/')[6]
    | where ResourceProvider !in ('microsoft.compute', 'microsoft.security')
    | extend ResourceType = split(_ResourceId, '/')[7]
    | extend Resource = split(_ResourceId, '/')[8]
    | summarize by tostring(ResourceProvider) , tostring(ResourceType), tostring(Resource)
    | sort by ResourceProvider, ResourceType
    ```

  - *Soluciones instaladas*: seleccione **Soluciones** en el panel de navegación del área de trabajo para ver una lista de las soluciones instaladas.
  - *Data Collector API*: los datos que llegan a través de [Data Collector API](../logs/data-collector-api.md) se almacenan en tablas de registro personalizadas. Para ver una lista de las tablas de registro personalizadas, seleccione **Registros** en el panel de navegación del área de trabajo y, después, seleccione **Registro personalizado** en el panel de esquema.
  - *Servicios vinculados*: las áreas de trabajo pueden tener servicios vinculados a recursos dependientes, como una cuenta de Azure Automation, una cuenta de almacenamiento o un clúster dedicado. Quite los servicios vinculados del área de trabajo. Vuelva a configurarlos manualmente en el área de trabajo de destino.
  - *Alertas*: para enumerar las alertas, seleccione **Alertas** en el panel de navegación del área de trabajo y, después, seleccione **Administrar reglas de alertas** en la barra de herramientas. Las alertas de las áreas de trabajo creadas después del 1 de junio de 2019 o de las áreas de trabajo que se [actualizaron desde Alert API de Log Analytics a scheduledQueryRules API](../alerts/alerts-log-api-switch.md) se pueden incluir en la plantilla. 
  
     Puede [comprobar si scheduledQueryRules API se usa para las alertas del área de trabajo](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace). Como alternativa, puede configurar las alertas manualmente en el área de trabajo de destino.
  - *Paquetes de consulta*: un área de trabajo se puede asociar a varios paquetes de consulta. Para identificar los paquetes de consulta de un área de trabajo, seleccione **Registros** en el panel de navegación del área de trabajo, seleccione **consultas** en el panel izquierdo y, después, seleccione los puntos suspensivos a la derecha del cuadro de búsqueda. Se abre un cuadro de diálogo con los paquetes de consulta seleccionados a la derecha. Si los paquetes de consulta están en el mismo grupo de recursos que el área de trabajo que se va a mover, puede incluirlo con esta migración.
- Compruebe que la suscripción de Azure le permite crear áreas de trabajo de Log Analytics en la región de destino.

## <a name="prepare-and-move"></a>Preparación y traslado
En los siguientes procedimientos se muestra cómo preparar el área de trabajo y los recursos para el traslado mediante una plantilla de Resource Manager y, después, trasladarlos a la región de destino mediante el portal. Siga los procedimientos en orden.

> [!NOTE]
> No todos los recursos se pueden exportar a través de una plantilla. Deberá configurarlos por separado después de crear el área de trabajo en la región de destino.

### <a name="select-resource-groups-and-edit-parameters"></a>Selección de grupos de recursos y parámetros de edición

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y después seleccione **Grupos de recursos**.
1. Busque el grupo de recursos que contiene el área de trabajo y selecciónelo.
1. Para ver los recursos de alerta, active la casilla **Mostrar tipos ocultos**.
1. Seleccione el filtro **Tipo**. Seleccione **Área de trabajo de Log Analytics**, **Solución**, **SavedSearches**, **microsoft.insights/scheduledqueryrules**, **defaultQueryPack** y otro recursos relacionados con el área de trabajo que tiene (como una cuenta de Automation). Luego, seleccione **Aplicar**.
1. Seleccione el área de trabajo, las soluciones, las consultas guardadas, las alertas, los paquetes de consultas y otros recursos relacionados con el área de trabajo que tiene (como una cuenta de Automation). Después, seleccione **Exportar plantilla** en la barra de herramientas.
    
    > [!NOTE]
    > Azure Sentinel no se puede exportar con una plantilla. Debe [incorporar Sentinel](../../sentinel/quickstart-onboard.md) a un área de trabajo de destino.
   
1. Seleccione **Implementar** en la barra de herramientas para editar y preparar la plantilla para la implementación.
1. Seleccione **Editar parámetros** en la barra de herramientas para abrir el archivo *parameters.json* en el editor en línea.
1. Para editar los parámetros, cambie la propiedad `value` en `parameters`. Este es un ejemplo:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "value": "my-workspace-name"
        },
        "workspaceResourceId": {
          "value": "/subscriptions/resource-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/workspaces/workspace-name"
        },
        "alertName": {
          "value": "my-alert-name"
        },
        "querypacks_name": {
          "value": "my-default-query-pack-name"
        }
      }
    }
    ```

1. Seleccione **Guardar** en el editor.

### <a name="edit-the-template"></a>Edición de la plantilla

1. Seleccione **Editar plantilla** en la barra de herramientas para abrir el archivo *template.json* en el editor en línea.
1. Para editar la región de destino en la que se implementará el área de trabajo de Log Analytics, cambie la propiedad `location` en `resources` en el editor en línea. 

   Para obtener los códigos de ubicación de la región, consulte [Residencia de datos Azure](https://azure.microsoft.com/global-infrastructure/locations/). El código de una región es el nombre de la región sin espacios. Por ejemplo, **Centro de EE. UU** debe ser `centralus`.
1. Quite los recursos de servicios vinculados (`microsoft.operationalinsights/workspaces/linkedservices`) si están presentes en la plantilla. Debe volver a configurar estos recursos manualmente en el área de trabajo de destino.

   La siguiente plantilla de ejemplo incluye el área de trabajo, la búsqueda guardada, las soluciones, las alertas y el paquete de consultas:

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "workspaces_name": {
          "type": "String"
        },
        "workspaceResourceId": {
          "type": "String"
        },
        "alertName": {
          "type": "String"
        },
        "querypacks_name": {
          "type": "String"
        }
      },
      "variables": {},
      "resources": [
        {
          "type": "microsoft.operationalinsights/workspaces",
          "apiVersion": "2020-08-01",
          "name": "[parameters('workspaces_name')]",
          "location": "france central",
          "properties": {
            "sku": {
              "name": "pergb2018"
            },
            "retentionInDays": 30,
            "features": {
              "enableLogAccessUsingOnlyResourcePermissions": true
            },
            "workspaceCapping": {
              "dailyQuotaGb": -1
            },
            "publicNetworkAccessForIngestion": "Enabled",
            "publicNetworkAccessForQuery": "Enabled"
          }
        },
        {
          "type": "Microsoft.OperationalInsights/workspaces/savedSearches",
          "apiVersion": "2020-08-01",
          "name": "[concat(parameters('workspaces_name'), '/2b5112ec-5ad0-5eda-80e9-ad98b51d4aba')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/workspaces', parameters('workspaces_name'))]"
          ],
          "properties": {
            "category": "VM Monitoring",
            "displayName": "List all versions of curl in use",
            "query": "VMProcess\n| where ExecutableName == \"curl\"\n| distinct ProductVersion",
            "tags": [],
            "version": 2
          }
        },
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('Updates(', parameters('workspaces_name'))]",
          "location": "france central",
          "dependsOn": [
            "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]"
          ],
          "plan": {
            "name": "[concat('Updates(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/Updates",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/Updates(', parameters('workspaces_name'), ')')]"
            ]
          }
        }
        {
          "type": "Microsoft.OperationsManagement/solutions",
          "apiVersion": "2015-11-01-preview",
          "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
          "location": "france central",
          "plan": {
            "name": "[concat('VMInsights(', parameters('workspaces_name'))]",
            "promotionCode": "",
            "product": "OMSGallery/VMInsights",
            "publisher": "Microsoft"
          },
          "properties": {
            "workspaceResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name'))]",
            "containedResources": [
              "[concat(resourceId('microsoft.operationalinsights/workspaces', parameters('workspaces_name')), '/views/VMInsights(', parameters('workspaces_name'), ')')]"
            ]
          }
        },
        {
          "type": "microsoft.insights/scheduledqueryrules",
          "apiVersion": "2021-02-01-preview",
          "name": "[parameters('alertName')]",
          "location": "france central",
          "properties": {
            "displayName": "[parameters('alertName')]",
            "severity": 3,
            "enabled": true,
            "evaluationFrequency": "PT5M",
            "scopes": [
              "[parameters('workspaceResourceId')]"
            ],
            "windowSize": "PT15M",
            "criteria": {
              "allOf": [
                {
                  "query": "Heartbeat | where computer == 'my computer name'",
                  "timeAggregation": "Count",
                  "operator": "LessThan",
                  "threshold": 14,
                  "failingPeriods": {
                    "numberOfEvaluationPeriods": 1,
                    "minFailingPeriodsToAlert": 1
                  }
                }
              ]
            },
            "autoMitigate": true,
            "actions": {}
          }
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks",
          "apiVersion": "2019-09-01-preview",
          "name": "[parameters('querypacks_name')]",
          "location": "francecentral",
          "properties": {}
        },
        {
          "type": "Microsoft.OperationalInsights/querypacks/queries",
          "apiVersion": "2019-09-01-preview",
          "name": "[concat(parameters('querypacks_name'), '/00000000-0000-0000-0000-000000000000')]",
          "dependsOn": [
            "[resourceId('Microsoft.OperationalInsights/querypacks', parameters('querypacks_name'))]"
          ],
          "properties": {
            "displayName": "my-query-name",
            "body": "my-query-text",
            "related": {
              "categories": [],
              "resourceTypes": [
                  "microsoft.operationalinsights/workspaces"
              ]
            },
            "tags": {
              "labels": []
            }
          }
        }
      ]
    }
    ```

1. Seleccione **Guardar** en el editor en línea.

### <a name="deploy-the-workspace"></a>Implemente el área de trabajo.

1. Seleccione **Suscripción** para elegir la suscripción en la que se implementará el área de trabajo de destino.
1. Seleccione **Grupo de recursos** para elegir el grupo de recursos donde se implementará el área de trabajo de destino. Puede seleccionar **Crear nuevo** para crear un grupo de recursos para el área de trabajo de destino.
1. Compruebe que en **Región** está seleccionada la ubicación de destino en la que quiere el grupo de seguridad de red.
1. Seleccione el botón **Revisar y crear** para comprobar la plantilla.
1. Seleccione **Crear** para implementar el área de trabajo y el recurso seleccionado en la región de destino.
1. El área de trabajo, incluidos los recursos seleccionados, ahora se implementa en la región de destino. Puede completar el resto de la configuración en el área de trabajo para emparejar la funcionalidad con el área de trabajo original.
   - *Conectar agentes*: use cualquiera de las opciones disponibles, como Reglas de recopilación de datos, para configurar los agentes necesarios en las máquinas virtuales y los conjuntos de escalado de máquinas virtuales y especifique la nueva área de trabajo de destino como destino.
   - *Configuración de diagnóstico*: actualice la configuración de diagnóstico en los recursos identificados, con el área de trabajo de destino como destino.
   - *Instalación de soluciones*: algunas soluciones, como [Azure Sentinel](../../sentinel/quickstart-onboard.md), requieren cierto procedimiento de incorporación y no se incluyeron en la plantilla. Debe incorporarlas por separado a la nueva área de trabajo.
   - *Configurar Data Collector API*: configure instancias de Data Collector API para enviar datos al área de trabajo de destino.
   - *Configurar reglas de alertas*: cuando las alertas no se exportan a la plantilla, debe configurarlas manualmente en el área de trabajo de destino.
1. Compruebe que los datos nuevos no se ingieren en el área de trabajo original. Ejecute la siguiente consulta en el área de trabajo original y observe que no hay ninguna ingesta después de la migración:

    ```kusto
    search *
    | where TimeGenerated > ago(12h)
    | summarize max(TimeGenerated) by Type
    ```

Una vez que los orígenes de datos se conectan al área de trabajo de destino, los datos ingeridos se almacenan en el área de trabajo de destino. Los datos más antiguos permanecen en el área de trabajo original y están sujetos a la directiva de retención. Puede realizar una [consulta entre áreas de trabajo](./cross-workspace-query.md#performing-a-query-across-multiple-resources). Si a ambas áreas de trabajo se les ha asignado el mismo nombre, use un nombre completo (*subscriptionName/resourceGroup/componentName*) en la referencia del área de trabajo.

Este es un ejemplo de una consulta en dos áreas de trabajo que tienen el mismo nombre:

```kusto
union 
  workspace('subscription-name1/<resource-group-name1/<original-workspace-name>')Update, 
  workspace('subscription-name2/<resource-group-name2/<target-workspace-name>').Update, 
| where TimeGenerated >= ago(1h)
| where UpdateState == "Needed"
| summarize dcount(Computer) by Classification
```

## <a name="discard"></a>Discard (Descartar)

Si desea descartar el área de trabajo de origen, elimine los recursos exportados o el grupo de recursos que los contiene:

1. Seleccione el grupo de recursos de destino en Azure Portal.
1. En la página **Información general**:
   
   - Si ha creado un nuevo grupo de recursos para esta implementación, seleccione **Eliminar grupo de recursos** en la barra de herramientas para eliminar el grupo de recursos.
   - Si la plantilla se implementó en un grupo de recursos existente, seleccione los recursos que se implementaron con la plantilla y, a continuación, seleccione **Eliminar** en la barra de herramientas para eliminar los recursos seleccionados.

## <a name="clean-up"></a>Limpieza

Mientras se ingieren nuevos datos en la nueva área de trabajo, los datos más antiguos del área de trabajo original siguen estando disponibles para la consulta y están sujetos a la directiva de retención definida en el área de trabajo. Se recomienda conservar el área de trabajo original mientras necesite datos más antiguos para realizar [consultas entre](./cross-workspace-query.md#performing-a-query-across-multiple-resources) áreas de trabajo. 

Si ya no necesita acceso a datos antiguos en el área de trabajo original:

1. Seleccione el grupo de recursos original en Azure Portal: 
1. Seleccione los recursos que quiera quitar y, a continuación, seleccione **Eliminar** en la barra de herramientas.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha trasladado un área de trabajo de Log Analytics de una región a otra y ha limpiado los recursos de origen. Para más información sobre el traslado de recursos entre regiones y la recuperación ante desastres en Azure, consulte:

- [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Traslado de máquinas virtuales de Azure a otra región](../../site-recovery/azure-to-azure-tutorial-migrate.md)
