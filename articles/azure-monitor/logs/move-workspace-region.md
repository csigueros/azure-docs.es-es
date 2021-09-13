---
title: Traslado de un área de trabajo de Log Analytics a otra región de Azure mediante Azure Portal
description: Use una plantilla de Azure Resource Manager para trasladar un área de trabajo de Log Analytics de una región de Azure a otra mediante Azure Portal.
author: yossiy
ms.topic: how-to
ms.date: 08/17/2021
ms.author: yossiy
ms.openlocfilehash: 0292559afefd983127980c036f4936433831f1e5
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326546"
---
# <a name="move-log-analytics-workspace-to-another-region-using-the-azure-portal"></a>Traslado del área de trabajo de Log Analytics a otra región mediante Azure Portal

Existen varios escenarios en los que puede que le interese trasladar su área de trabajo de Log Analytics existente de una región a otra. Por ejemplo, Log Analytics está disponible desde hace poco en una región que hospeda la mayoría de sus recursos y quiere que el área de trabajo de trabajo esté más cerca y ahorrarse los gastos de salida. También es posible que quiera mover su área de trabajo a una región recién agregada por requisito de la soberanía de datos.

El área de trabajo de Log Analytics no se puede mover de una región a otra. Sin embargo, puede usar una plantilla de Azure Resource Manager para exportar el recurso de área de trabajo y los recursos relacionados. Después, puede almacenar provisionalmente los recursos en otra región; para ello, exporte el área de trabajo a una plantilla y modifique los parámetros para que coincidan con los de la región de destino; a continuación, implemente la plantilla en la nueva región. Para más información sobre Resource Manager y las plantillas, consulte [Inicio rápido: Creación e implementación de plantillas de Azure Resource Manager mediante Azure Portal](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md). El entorno del área de trabajo puede ser complejo e incluir orígenes conectados, soluciones administradas, servicios vinculados, alertas y paquetes de consultas. No todos los recursos se pueden exportar en una plantilla de Resource Manager y algunos requerirán una configuración independiente al trasladarse a un área de trabajo.

## <a name="prerequisites"></a>Requisitos previos

- Para exportar la configuración del área de trabajo a una plantilla que se pueda implementar en otra región, necesita los roles [Colaborador de Log Analytics](../../role-based-access-control/built-in-roles.md#log-analytics-contributor) o [Colaborador de supervisión](../../role-based-access-control/built-in-roles.md#monitoring-contributor) u otros superiores.

- Identifique todos los recursos asociados actualmente al área de trabajo, por ejemplo:
  - Agentes conectados: escriba *Registros* en el área de trabajo y consulte la tabla [Latido](../insights/solution-agenthealth.md#heartbeat-records) para enumerar los agentes conectados.
    ```kusto
    Heartbeat
    | summarize by Computer, Category, OSType, _ResourceId
    ```
  - Soluciones instaladas: haga clic en **Soluciones** en el panel de navegación del área de trabajo para obtener una lista de las soluciones instaladas.
  - Data Collector API: los datos que llegan a través de [Data Collector API](../logs/data-collector-api.md) se almacenan en tablas de registro personalizadas. Haga clic en ***Registros** en el panel de navegación del área de trabajo y, luego, en *Registro personalizado** en el panel de esquema para obtener una lista de tablas de registro personalizadas.
  - Servicios vinculados: el área de trabajo puede tener servicios vinculados a recursos dependientes, como la cuenta de Automation, la cuenta de almacenamiento o un clúster dedicado. Quite los servicios vinculados del área de trabajo. Estos se deben volver a configurar manualmente en el área de trabajo de destino.
  - Alertas: haga clic en **Alertas** en el panel de navegación del área de trabajo y, luego, en **Administrar reglas de alertas** en la barra de herramientas para enumerar las alertas. Las alertas de áreas de trabajo creadas después del 1 de junio de 2019 o de áreas de trabajo que se [actualizaron de la API heredada de alertas de Log Analytics a scheduledQueryRules API](../alerts/alerts-log-api-switch.md) se pueden incluir en la plantilla. Puede [comprobar si scheduledQueryRules API se usa para las alertas del área de trabajo](../alerts/alerts-log-api-switch.md#check-switching-status-of-workspace). Como alternativa, puede configurar alertas manualmente en el área de trabajo de destino.
  - Paquetes de consulta: un área de trabajo se puede asociar a varios paquetes de consulta. Para identificar los paquetes de consulta del área de trabajo, haga clic en **Registros** en el panel de navegación del área de trabajo, en **Consultas** en el panel izquierdo y, luego, en los puntos suspensivos a la derecha del cuadro de búsqueda para obtener más opciones de configuración: se abrirá un cuadro de diálogo con el paquete de consulta seleccionado a la derecha. Si los paquetes de consulta están en el mismo grupo de recursos que el área de trabajo que se va a mover, puede incluirlo con esta migración.
- Compruebe que la suscripción de Azure le permita crear áreas de trabajo de Log Analytics en la región de destino.

## <a name="prepare-and-move"></a>Preparación y traslado
En los pasos siguientes se muestra cómo preparar el área de trabajo y los recursos para trasladarlos mediante una plantilla de Resource Manager y cómo trasladarlos a la región de destino con el portal. No todos los recursos se pueden exportar mediante una plantilla y se deben configurar por separado una vez creada el área de trabajo en la región de destino.

### <a name="export-the-template-and-deploy-from-the-portal"></a>Exportación de la plantilla e implementación desde el portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com)**Grupos de recursos**.
2. Busque el grupo de recursos que contiene el área de trabajo y haga clic en él.
3. Para ver los recursos de alerta, active la casilla **Mostrar tipos ocultos**.
4. Haga clic en el filtro "Tipo" y seleccione **Área de trabajo de Log Analytics**, **Solución**, **SavedSearches**, **microsoft.insights/scheduledqueryrules** y **defaultQueryPack** según corresponda, y haga clic en Aplicar.
5. Seleccione el área de trabajo, las soluciones, las alertas, las búsquedas guardadas y los paquetes de consulta que quiere mover y, luego, haga clic en **Exportar plantilla** en la barra de herramientas.
    
    > [!NOTE]
    > Sentinel no se puede exportar con la plantilla y debe [incorporar Sentinel](../../sentinel/quickstart-onboard.md) al área de trabajo de destino.
   
6. Haga clic en **Implementar** en la barra de herramientas para editar y preparar la plantilla para la implementación.
7. Haga clic en **Editar parámetros** en la barra de herramientas para abrir el archivo **parameters.json** en el editor en línea.
8. Para editar los parámetros, cambie la propiedad **value** en **parameters**:

Archivo de parámetros de ejemplo:

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

9. Haga clic en **Guardar** en el editor.
10. Haga clic en **Editar plantilla** en la barra de herramientas para abrir el archivo **template.json** en el editor en línea.
11. Para editar la región de destino en la que se implementará el área de trabajo de Log Analytics, cambie la propiedad **location** en **resources** en el editor en línea. Para obtener los códigos de ubicación de la región, consulte [Ubicaciones de Azure](https://azure.microsoft.com/global-infrastructure/locations/). El código de una región es el nombre de la región sin espacios, **Centro de EE. UU.**  = **centralus**.
12. Quite los recursos de servicios vinculados `microsoft.operationalinsights/workspaces/linkedservices` si están presentes en la plantilla. Estos se deben volver a configurar manualmente en el área de trabajo de destino.

Plantilla de ejemplo que incluye el área de trabajo, la búsqueda guardada, las soluciones, las alertas y el paquete de consulta:

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

13. Haga clic en **Guardar** en el editor en línea.
14. Haga clic en **Suscripción** para elegir la suscripción en la que se implementará el área de trabajo de destino.
16. Haga clic en **Grupo de recursos** para elegir el grupo de recursos donde se implementará el área de trabajo de destino. Puede hacer clic en **Crear nuevo** para crear un grupo de recursos para el área de trabajo de destino.
17. Compruebe que **Región** está establecida en la ubicación de destino en la que quiere implementar el área de trabajo.
18. Haga clic en el botón **Revisar y crear** para comprobar la plantilla.
19. Haga clic en **Crear** para implementar el área de trabajo y el recurso seleccionado en la región de destino.
20. El área de trabajo, incluidos los recursos seleccionados, ahora está implementada en la región de destino y puede completar el resto de la configuración en el área de trabajo para emparejar la funcionalidad con el área de trabajo original.
    - Conexión de agentes: use cualquiera de las opciones disponibles, por ejemplo, DCR, para configurar los agentes necesarios en las máquinas virtuales y los conjuntos de escalado de máquinas virtuales y especifique la nueva área de trabajo de destino como destino.
    - Instalación de soluciones: algunas soluciones, como [Azure Sentinel](../../sentinel/quickstart-onboard.md), requieren cierto procedimiento de incorporación y no se incluyeron en la plantilla. Debe incorporarlas por separado a la nueva área de trabajo.
    - Data Collector API: configure instancias de Data Collector API para enviar datos al área de trabajo de destino.
    - Reglas de alertas: cuando las alertas no se exportan a la plantilla, debe configurarlas manualmente en el área de trabajo de destino.

## <a name="discard"></a>Discard (Descartar)

Si quiere descartar el área de trabajo de origen, elimine los recursos exportados o el grupo de recursos que los contiene. Para ello, seleccione el grupo de recursos de destino en Azure Portal: si ha creado un grupo de recursos para esta implementación, haga clic en **Eliminar grupo de recursos** en la barra de herramientas de la página Información general. Si la plantilla se implementó en un grupo de recursos existente, seleccione los recursos que se implementaron con la plantilla y haga clic en **Eliminar** en la barra de herramientas.

## <a name="clean-up"></a>Limpieza

Se recomienda que siga siendo el grupo de recursos original, junto con el área de trabajo, durante el período de comprobación, hasta que se alcance la funcionalidad del área de trabajo de destino. Si quiere descartar recursos de la ubicación original, seleccione el grupo de recursos original en Azure Portal, elija los recursos que quiere quitar y haga clic en **Eliminar** en la barra de herramientas.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha trasladado un área de trabajo de Log Analytics de una región a otra y ha limpiado los recursos de origen. Para obtener más información sobre cómo trasladar recursos entre regiones y la recuperación ante desastres en Azure, consulte:

- [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
- [Traslado de máquinas virtuales de Azure a otra región](../../site-recovery/azure-to-azure-tutorial-migrate.md)