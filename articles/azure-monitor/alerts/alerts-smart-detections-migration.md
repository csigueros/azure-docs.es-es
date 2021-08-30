---
title: Actualización de la detección inteligente de Azure Monitor Application Insights a las alertas (versión preliminar) | Microsoft Docs
description: Obtenga información sobre los pasos necesarios para actualizar la detección inteligente de Application Insights de Azure Monitor a reglas de alerta.
ms.topic: conceptual
ms.date: 05/30/2021
ms.openlocfilehash: d0995c05af707c5a4fb94305ca1211e38ca59e3a
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113587488"
---
# <a name="migrate-azure-monitor-application-insights-smart-detection-to-alerts-preview"></a>Migración de la detección inteligente de Azure Monitor Application Insights a las alertas (versión preliminar)

En este artículo se describe el proceso de migración de detección inteligente de Application Insights a alertas. La migración crea reglas de alerta para los distintos módulos de detección inteligente. Puede administrar y configurar dichas reglas como cualquier otra regla de alerta de Azure Monitor. También puede configurar grupos de acciones para estas reglas, lo que le brinda varios métodos de acciones o notificaciones para nuevas detecciones.

## <a name="benefits-of-migration-to-alerts"></a>Ventajas de la migración a alertas

Con la migración, la detección inteligente ahora le permite aprovechar todas las funcionalidades de las alertas de Azure Monitor, entre las que se incluyen:

- **Opciones de notificación enriquecidas para todos los detectores** - Los [grupos de acciones](../alerts/action-groups.md) permiten configurar varios tipos de notificaciones y acciones que se desencadenan cuando se activa una alerta. Puede configurar notificaciones por correo electrónico, SMS, llamadas de voz o notificaciones push; y acciones, como llamar a un webhook seguro, aplicación lógica, runbook de automatización, etc. Los grupos de acciones promueven la administración a gran escala, ya que le permiten configurar acciones una vez y usarlas en varias reglas de alerta.
- **Administración a gran escala** de alertas de detección inteligente mediante una API y la experiencia de alertas de Azure Monitor.
- **Supresión de notificaciones basada en reglas** - Las [reglas de acción](../alerts/alerts-action-rules.md) le ayudan a definir o suprimir acciones en cualquier ámbito de Azure Resource Manager (suscripción, grupo de recursos o recurso de destino de Azure). Disponen de varios filtros que le ayudan a reducir el subconjunto específico de instancias de alerta sobre las que quiere actuar. 

## <a name="migrated-smart-detection-capabilities"></a>Funcionalidades migradas de detección inteligente

Se crea un nuevo conjunto de reglas de alerta al migrar un recurso de Application Insights.  Se crea una regla para cada una de las funcionalidades de detección inteligente migradas. En la tabla siguiente se asignan las funcionalidades de detección inteligente anteriores de la migración a las reglas de alerta posteriores a la migración:

| Nombre de la regla de detección inteligente <sup>(1)</sup>          | Nombre de la regla de alerta <sup>(2)</sup> |
| -------------------------                          | ------------------------------ |
| Degradación del tiempo de respuesta del servidor                | Degradación de la latencia de respuesta: *\<Application Insights resource name\>*  |
| Reducción de la duración de la dependencia                 | Degradación de la latencia de dependencia: *\<Application Insights resource name\>*|
| Degradación en la relación de gravedad de seguimiento (versión preliminar)      | Degradación de la gravedad de seguimiento: *\<Application Insights resource name\>*|
| Aumento anormal del volumen de excepciones (versión preliminar)        | Anomalías de excepciones: *\<Application Insights resource name\>*|
| Detección de una posible fuga de memoria (versión preliminar)           | Posible fuga de memoria: *\<Application Insights resource name\>*|
| Carga lenta de página                                | *discontinuado* <sup>(3)</sup>  |
| Lentitud en el tiempo de respuesta del servidor                          | *discontinuado* <sup>(3)</sup>  |
| Duración de la dependencia prolongada                           | *discontinuado* <sup>(3)</sup>  |
| Detección de un posible problema de seguridad (versión preliminar)        | *discontinuado* <sup>(3)</sup>  |
| Aumento anómalo del volumen de datos diario (versión preliminar)       | *discontinuado* <sup>(3)</sup>  |

<sup>(1)</sup> Nombre de la regla tal como aparece en la hoja Configuración de detección inteligente  
<sup>(2)</sup> Nombre de la nueva regla de alertas después de la migración  
<sup>(3)</sup> Estas funcionalidades de detección inteligente no se convierten en alertas, debido al bajo uso y a la reevaluación de la eficacia de detección. Estos detectores ya no se admitirán para este recurso una vez que se complete su migración. 

La migración no cambia el diseño algorítmico ni el comportamiento de la detección inteligente. Se espera el mismo rendimiento de detección antes y después del cambio.

Tiene que aplicar la migración a cada recurso de Application Insights por separado. En el caso de los recursos que no se migran explícitamente, la detección inteligente seguirá funcionando como antes.

### <a name="action-group-configuration-for-the-new-smart-detection-alert-rules"></a>Configuración del grupo de acciones para las nuevas reglas de alerta de detección inteligente

Como parte de la migración, cada nueva regla de alerta se configura automáticamente con un grupo de acciones. La migración puede asignar un grupo de acciones predeterminado para cada regla. El grupo de acciones predeterminado se configura según la notificación de la regla antes de la migración:

- Si la **regla de detección inteligente tenía configurado el correo electrónico predeterminado o no tenía configurada ninguna notificación**, la nueva regla de alerta se configura con un grupo de acciones denominado "Application Insights Smart Detection" (Detección inteligente de Application Insights).
    - Si la herramienta de migración encuentra un grupo de acciones existente con ese nombre, vincula la nueva regla de alerta con ese grupo de acciones.  
    - De lo contrario, crea un nuevo grupo de acciones con ese nombre. El nuevo grupo se configura para las acciones "Rol de Azure Resource Manager de correo electrónico" y envía una notificación a los usuarios Colaborador de supervisión y Lector de supervisión de Azure Resource Manager.

- Si la **notificación por correo electrónico predeterminada se cambió** antes de la migración, se crea un grupo de acciones denominado "Application Insights Smart Detection \<n\>", con una acción de correo electrónico que envía notificaciones a las direcciones de correo electrónico configuradas anteriormente.

En lugar de usar el grupo de acciones predeterminado, seleccione un grupo de acciones existente que se configurará para todas las nuevas reglas de alerta.

## <a name="executing-smart-detection-migration-process"></a>Ejecución del proceso de migración de detección inteligente

### <a name="migrate-your-smart-detection-using-the-azure-portal"></a>Migración de detección inteligente mediante Azure Portal

Aplique la migración a un recurso específico de Application Insights a la vez.

Para migrar la detección inteligente en el recurso, siga los pasos a continuación:

1. Seleccione **Detección inteligente** en el encabezado **Investigar** del menú de la izquierda del recurso de Application Insights.

2. Haga clic en el banner que le pide **migrar la detección inteligente a alertas (versión preliminar)** . Se abre el cuadro de diálogo de migración.

   ![Banner de la fuente de detección inteligente](media/alerts-smart-detections-migration/smart-detection-feed-banner.png)

3. Seleccione el grupo de acciones que se va a configurar para las nuevas reglas de alerta. Puede elegir entre usar el grupo de acciones predeterminado (como se explicó anteriormente) o uno de los grupos de acciones existentes.

4. Seleccione **Migrar** para iniciar el proceso de migración.

   ![Cuadro de diálogo de migración de detección inteligente](media/alerts-smart-detections-migration/smart-detection-migration-dialog.png)

Después de la migración, se crean nuevas reglas de alerta para el recurso de Application Insights, como se explicó anteriormente.

### <a name="migrate-your-smart-detection-using-azure-cli"></a>Migración de detección inteligente mediante la CLI de Azure

Puede iniciar la migración de detección inteligente mediante el siguiente comando de la CLI de Azure. El comando desencadena el proceso de migración preconfigurado como se describió anteriormente.

```azurecli
az rest --method POST --uri /subscriptions/{subscriptionId}/providers/Microsoft.AlertsManagement/migrateFromSmartDetection?api-version=2021-01-01-preview --body @body.txt
```

Donde body.txt debe incluir:

```json
{
      "scope": [
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName} /providers/microsoft.insights/components/{resourceName} "
      ],
      "actionGroupCreationPolicy" : "{Auto/Custom}",
      "customActionGroupName" : "{actionGroupName}"           
}
```

**ActionGroupCreationPolicy** selecciona la directiva para migrar la configuración de correo electrónico de las reglas de detección inteligente a los grupos de acciones. Los valores permitidos son:

- **"Auto"** , que usa los grupos de acciones predeterminados, como se describe en este documento.
- **"Custom"** , que crea todas las reglas de alerta con el grupo de acciones especificado en **"customActionGroupName"** .
- *\<blank\>* : Si no se especifica **ActionGroupCreationPolicy**, se usa la directiva "Auto".

### <a name="migrate-your-smart-detection-using-azure-resource-manager-templates"></a>Migración de detección inteligente mediante plantillas de Azure Resource Manager

Puede desencadenar la migración de detección inteligente a alertas para un recurso específico de Application Insights mediante plantillas de Azure Resource Manager. Con este método, tendría que:

- Crear una regla de alerta de detección inteligente para cada uno de los detectores admitidos.
- Modificar las propiedades de Application Insights para indicar que se completó la migración.

Este método le permite controlar qué reglas de alerta crear, definir su propio nombre y descripción de las reglas de alertas, y seleccionar cualquier grupo de acciones que quiera para cada regla.

Las plantillas siguientes se deben usar para este propósito (edítelas según sea necesario para proporcionar el identificador de suscripción y el nombre del recurso de Application Insights).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "applicationInsightsResourceName": {
            "type": "string"
        },
        "actionGroupName": {
            "type": "string",
            "defaultValue": "Application Insights Smart Detection"
        },
        "actionGroupResourceGroup": {
            "type": "string",
            "defaultValue": "[resourceGroup().Name]"
        }
    },
    "variables": {
        "applicationInsightsResourceId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',resourceGroup().Name,'/providers/microsoft.insights/components/',parameters('applicationInsightsResourceName'))]",
        "actionGroupId": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/',parameters('actionGroupResourceGroup'),'/providers/microsoft.insights/ActionGroups/',parameters('actionGroupName'))]",
        "requestPerformanceDegradationDetectorRuleName": "[concat('Response Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "dependencyPerformanceDegradationDetectorRuleName": "[concat('Dependency Latency Degradation - ', parameters('applicationInsightsResourceName'))]",
        "traceSeverityDetectorRuleName": "[concat('Trace Severity Degradation - ', parameters('applicationInsightsResourceName'))]",
        "exceptionVolumeChangedDetectorRuleName": "[concat('Exception Anomalies - ', parameters('applicationInsightsResourceName'))]",
        "memoryLeakRuleName": "[concat('Potential Memory Leak - ', parameters('applicationInsightsResourceName'))]"
    },
    "resources": [
        {
            "name": "[variables('requestPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "RequestPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {   
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('dependencyPerformanceDegradationDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Dependency Latency Degradation notifies you of an unusual increase in response by a dependency your app is calling (e.g. REST API or database)",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "DependencyPerformanceDegradationDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('traceSeverityDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Trace Severity Degradation notifies you of an unusual increase in the severity of the traces generated by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "TraceSeverityDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('exceptionVolumeChangedDetectorRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Exception Anomalies notifies you of an unusual rise in the rate of exceptions thrown by your app.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "ExceptionVolumeChangedDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[variables('memoryLeakRuleName')]",
            "type": "Microsoft.AlertsManagement/smartdetectoralertrules",
            "location": "global",
            "apiVersion": "2019-03-01",
            "properties": {
                "description": "Potential Memory Leak notifies you of increased memory consumption pattern by your app which may indicate a potential memory leak.",
                "state": "Enabled",
                "severity": "Sev3",
                "frequency": "PT24H",
                "detector": {
                    "id": "MemoryLeakDetector"
                },
                "scope": [
                    "[variables('applicationInsightsResourceId')]"
                ],
                "actionGroups": {
                    "groupIds": [
                        "[variables('actionGroupId')]"
                    ]
                }
            }
        },
        {
            "name": "[concat(parameters('applicationInsightsResourceName'),'/migrationToAlertRulesCompleted')]",
            "type": "Microsoft.Insights/components/ProactiveDetectionConfigs",
            "location": "[resourceGroup().location]",
            "apiVersion": "2018-05-01-preview",
            "properties": {
                "name": "migrationToAlertRulesCompleted",
                "sendEmailsToSubscriptionOwners": false,
                "customEmails": [],
                "enabled": true
            },
            "dependsOn": [
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('requestPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('dependencyPerformanceDegradationDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('traceSeverityDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('exceptionVolumeChangedDetectorRuleName'))]",
                "[resourceId('Microsoft.AlertsManagement/smartdetectoralertrules', variables('memoryLeakRuleName'))]"
            ]
        }
    ]
}
```

## <a name="viewing-your-alerts-after-the-migration"></a>Visualización de las alertas después de la migración

Después del proceso de migración, para ver las alertas de detección inteligente, puede seleccionar la entrada Alertas en el menú de la izquierda del recurso de Application Insights. Seleccione **Tipo de señal** como **Detector inteligente** para filtrar y presentar solo las alertas de detección inteligente. Puede seleccionar una alerta para ver los detalles de la detección.

![Alertas de detección inteligente](media/alerts-smart-detections-migration/smart-detector-alerts.png)

También puede ver las detecciones disponibles en la fuente de detección inteligente del recurso de Application Insights.

![Fuente de detección inteligente](media/alerts-smart-detections-migration/smart-detection-feed.png)

## <a name="managing-smart-detection-alert-rules-settings-after-the-migration"></a>Administración de la configuración de las reglas de alerta de detección inteligente después de la migración

### <a name="managing-alert-rules-settings-using-the-azure-portal"></a>Administración de la configuración de las reglas de alerta mediante Azure Portal

Una vez que se complete la migración, accede a las nuevas reglas de alerta de detección inteligente de forma similar a las otras reglas de alerta definidas para el recurso:

1. Seleccione **Alertas** en el encabezado **Supervisión** en el menú de la izquierda del recurso de Application Insights.

   ![Menú Alertas](media/alerts-smart-detections-migration/application-insights-alerts.png)

2. Haga clic en **Administrar regla de alerta**.

   ![Administración de reglas de alerta](media/alerts-smart-detections-migration/manage-alert-rules.png)

3. Seleccione **Tipo de señal** como **Detector inteligente** para filtrar y presentar las reglas de alerta de detección inteligente.

   ![Reglas de Detector inteligente](media/alerts-smart-detections-migration/smart-detector-rules.png)

### <a name="enabling--disabling-smart-detection-alert-rules"></a>Habilitación o deshabilitación de reglas de alerta de detección inteligente 

Las reglas de alerta de detección inteligente se pueden habilitar o deshabilitar a través de la interfaz de usuario del portal o mediante programación, al igual que cualquier otra regla de alerta.

Si se deshabilitó una regla de detección inteligente específica antes de la migración, también se deshabilitará la nueva regla de alerta.

### <a name="configuring-action-group-for-your-alert-rules"></a>Configuración del grupo de acciones para las reglas de alerta

Puede crear y administrar grupos de acciones para las nuevas reglas de alerta de detección inteligente como para cualquier otra regla de alerta de Azure Monitor. 

### <a name="managing-alert-rule-settings-using-azure-resource-manager-templates"></a>Administración de la configuración de reglas de alerta mediante plantillas de Azure Resource Manager

Después de completar la migración, puede usar plantillas de Azure Resource Manager para configurar los valores de las reglas de alerta de detección inteligente.

> [!NOTE]
> Después de completar la migración, los valores de detección inteligente deben configurarse mediante plantillas de reglas de alerta de detección inteligente, y ya no se podrán configurar mediante la [plantilla de Resource Manager para Application Insights](../app/proactive-arm-config.md#smart-detection-rule-configuration).

En este ejemplo de plantilla de Azure Resource Manager se muestra cómo configurar una regla de alerta de **Degradación de la latencia de respuesta** en un estado **Habilitado** con una gravedad de 2.
* La detección inteligente es un servicio global, por lo que la ubicación de la regla se crea en la ubicación **global**.
* La propiedad "id" debe cambiar según el detector específico configurado. El valor tiene que ser uno de los siguientes:

  - **FailureAnomaliesDetector**
  - **RequestPerformanceDegradationDetector**
  - **DependencyPerformanceDegradationDetector**
  - **ExceptionVolumeChangedDetector**
  - **TraceSeverityDetector**  
  - **MemoryLeakDetector**
 
```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "microsoft.alertsmanagement/smartdetectoralertrules",
            "apiVersion": "2019-03-01",
            "name": "Response Latency Degradation - my-app",
            "location": "global", 
            "properties": {
                  "description": "Response Latency Degradation notifies you of an unusual increase in latency in your app response to requests.",
                  "state": "Enabled",
                  "severity": "2",
                  "frequency": "PT24H",
                  "detector": {
                  "id": "RequestPerformanceDegradationDetector"
                  },
                  "scope": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourceGroups/MyResourceGroup/providers/microsoft.insights/components/my-app"],
                  "actionGroups": {
                        "groupIds": ["/subscriptions/00000000-1111-2222-3333-444444444444/resourcegroups/MyResourceGroup/providers/microsoft.insights/actiongroups/MyActionGroup"]
                  }
            }
        }
    ]
}
```



## <a name="next-steps"></a>Pasos siguientes

- [Obtenga más información sobre alertas en Azure](./alerts-overview.md)
- [Más información sobre la detección inteligente en Application Insights](../app/proactive-diagnostics.md)
