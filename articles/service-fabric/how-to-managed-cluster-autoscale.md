---
title: Configuración del escalado automático en nodos de clústeres administrados de Service Fabric
description: Aprenda a configurar directivas de escalado automático en un clúster administrado de Service Fabric.
ms.topic: how-to
ms.date: 10/25/2021
ms.openlocfilehash: 6c2dd14535b726d242d7076bcac94894e4c560dd
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093141"
---
# <a name="introduction-to-autoscaling-on-service-fabric-managed-clusters-preview"></a>Introducción al escalado automático en clústeres administrados de Service Fabric (versión preliminar)
El [escalado automático](../azure-monitor/autoscale/autoscale-overview.md) ofrece una gran flexibilidad y permite agregar o reducir nodos a petición en un tipo de nodo secundario. Este comportamiento automatizado y flexible reduce la sobrecarga de administración y el posible impacto empresarial mediante la supervisión y la optimización de la cantidad de nodos que prestan servicio a la carga de trabajo. Configure reglas para la carga de trabajo y deje que el escalado automático se ocupe del resto. Cuando se alcanzan esos umbrales definidos, las reglas de escalado automático actúan para ajustar la capacidad del tipo de nodo. El escalado automático se puede habilitar, deshabilitar o configurar en cualquier momento. En este artículo se proporciona una implementación de ejemplo y se explica cómo habilitar o deshabilitar el escalado automático y cómo configurar una directiva de escalado automático de ejemplo.


**Requisitos y métricas admitidas:**
* Para usar el escalado automático en clústeres administrados, debe tener la versión de API `2021-07-01-preview` o posterior.
* El SKU del clúster debe ser Estándar.
* Solo se puede configurar en un tipo de nodo secundario del clúster.
* Después de habilitar el escalado automático en un tipo de nodo, establezca la propiedad `vmInstanceCount` en `-1` al volver a implementar el recurso.
* Solo se admiten [métricas publicadas de Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

Un escenario común en el que el escalado automático resulta útil es cuando la carga de un servicio concreto varía con el tiempo. Por ejemplo, un servicio (como, una puerta de enlace) puede escalar en función de la cantidad de recursos necesarios para gestionar las solicitudes entrantes. Vamos a echar un vistazo a un ejemplo del aspecto que podrían tener esas reglas de escalado que vamos a usar más adelante en el artículo:
* Si todas las instancias de la puerta de enlace usan más del 70 % de media, agregue dos instancias más para escalar el servicio de puerta de enlace horizontalmente. Haga esto cada 30 minutos, pero nunca tenga más de veinte instancias en total.
* Si todas las instancias de la puerta de enlace usan menos del 40 % de núcleos de media, quite una instancia para reducir el servicio horizontalmente. Hágalo cada 30 minutos, pero no tenga nunca menos de tres instancias en total.

## <a name="example-autoscale-deployment"></a>Implementación de escalado automático de ejemplo

Este ejemplo le ayudará a: 
* Crear un clúster administrado de Service Fabric de SKU estándar con dos tipos de nodo, `NT1` y `NT2`, de manera predeterminada.
* Agregar reglas de escalado automático al tipo de nodo secundario, `NT2`.

>[!NOTE] 
> El escalado automático del tipo de nodo se realiza en función de las métricas de host de CPU de VMSS del clúster administrado. El recurso de VMSS se resuelve automáticamente en la plantilla. 


A continuación se le lleva paso a paso por la configuración de un clúster con el escalado automático configurado.

1) Cree un grupo de recursos en una región

   ```powershell 
   Login-AzAccount
   Select-AzSubscription -SubscriptionId $subscriptionid
   New-AzResourceGroup -Name $myresourcegroup -Location $location
   ```

2) Cree un recurso de clúster

   Descargue este [ejemplo de clúster administrado de Service Fabric de SKU estándar](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/azuredeploy.json). Ejecute este comando para implementar el recurso de clúster:

   ```powershell
   $parameters = @{ 
   clusterName = $clusterName
   adminPassword = $VmAdminPassword
   clientCertificateThumbprint = $clientCertificateThumbprint
   } 
   New-AzResourceGroupDeployment -Name "deploy_cluster" -ResourceGroupName $resourceGroupName -TemplateFile .\azuredeploy.json -TemplateParameterObject $parameters -Verbose
   ```

3) Configure y habilite reglas de escalado automático en un tipo de nodo secundario
 
   Descargue la [plantilla de ejemplo de escalado automático de clúster administrado](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/sfmc-deploy-autoscale.json) que va a usar para configurar el escalado automático con los siguientes comandos:

   ```powershell
   $parameters = @{ 
   clusterName = $clusterName
   }
   New-AzResourceGroupDeployment -Name "deploy_autoscale" -ResourceGroupName $resourceGroupName -TemplateFile .\sfmc-deploy-autoscale.json -TemplateParameterObject $parameters -Verbose 
   ```

>[!NOTE]
> Una vez completada esta implementación, las futuras implementaciones de recursos de clúster deben establecer la propiedad `vmInstanceCount` en `-1` en los tipos de nodo secundarios con reglas de escalado automático habilitadas. Esto garantiza que las implementaciones de clúster no entren en conflicto con el escalado automático.


## <a name="enable-or-disable-autoscaling-on-a-secondary-node-type"></a>Habilitación o deshabilitación del escalado automático en un tipo de nodo secundario

Los tipos de nodo implementados por un clúster administrado de Service Fabric no habilitan el escalado automático de manera predeterminada. El escalado automático se puede habilitar o deshabilitar en cualquier momento por tipo de nodo configurado y disponible.

Para habilitar esta característica, configure la propiedad `enabled` en el tipo `Microsoft.Insights/autoscaleSettings` de una plantilla de ARM, como se muestra a continuación:

```JSON
    "resources": [
            {
            "type": "Microsoft.Insights/autoscaleSettings",
            "apiVersion": "2015-04-01",
            "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
            "location": "[resourceGroup().location]",
            "properties": {
                "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
                "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'), '/nodetypes/', parameters('nodeType2Name'))]",
                "enabled": true,
            ...
```

Para deshabilitar el escalado automático, establezca el valor en `false`

## <a name="delete-autoscaling-rules"></a>Eliminación de reglas de escalado automático

Para eliminar cualquier configuración de directivas de escalado automático de un tipo de nodo, puede ejecutar el siguiente comando de PowerShell.

```PowerShell
Remove-AzResource -ResourceId "/subscriptions/$subscriptionId/resourceGroups/$resourceGroup/providers/microsoft.insights/autoscalesettings/$name" -Force
```

## <a name="set-policies-for-autoscaling"></a>Establecimiento de directivas para el escalado automático

 Un clúster administrado de Service Fabric no configura ninguna [directiva para el escalado automático](../azure-monitor/autoscale/autoscale-understanding-settings.md) de manera predeterminada. Las directivas de escalado automático deben configurarse para cualquier acción de escalado que se produzca en los recursos subyacentes.

En el ejemplo siguiente se establece una directiva para que `nodeType2Name` sea al menos 3 nodos, pero se permite escalar verticalmente hasta 20 nodos. El escalado vertical se desencadena cuando el uso de CPU medio es del 70 % en los últimos 30 minutos con una granularidad de 1 minuto. La reducción vertical se desencadena cuando el uso de CPU medio es inferior al 40 % en los últimos 30 minutos con una granularidad de 1 minuto.

```JSON
    "resources": [
            {
            "type": "Microsoft.Insights/autoscaleSettings",
            "apiVersion": "2015-04-01",
            "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
            "location": "[resourceGroup().location]",
            "properties": {
                "name": "[concat(parameters('clusterName'), '-', parameters('nodeType2Name'))]",
                "targetResourceUri": "[concat('/subscriptions/', subscription().subscriptionId, '/resourceGroups/',  resourceGroup().name, '/providers/Microsoft.ServiceFabric/managedclusters/', parameters('clusterName'), '/nodetypes/', parameters('nodeType2Name'))]",
                "enabled": "[parameters('enableAutoScale')]",
                "profiles": [
                    {
                        "name": "Autoscale by percentage based on CPU usage",
                        "capacity": {
                            "minimum": "3",
                            "maximum": "20",
                            "default": "3"
                        },
                        "rules": [
                            {
                                "metricTrigger": {
                                  "metricName": "Percentage CPU",
                                  "metricNamespace": "",
                                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/SFC_', reference(resourceId('Microsoft.ServiceFabric/managedClusters', parameters('clusterName')), '2021-07-01-preview').clusterId,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('nodeType2Name'))]",
                                  "timeGrain": "PT1M",
                                  "statistic": "Average",
                                  "timeWindow": "PT30M",
                                  "timeAggregation": "Average",
                                  "operator": "GreaterThan",
                                  "threshold": 70
                                },
                                "scaleAction": {
                                  "direction": "Increase",
                                  "type": "ChangeCount",
                                  "value": "5",
                                  "cooldown": "PT5M"
                                }
                            },
                            {
                                "metricTrigger": {
                                  "metricName": "Percentage CPU",
                                  "metricNamespace": "",
                                  "metricResourceUri": "[concat('/subscriptions/',subscription().subscriptionId,'/resourceGroups/SFC_', reference(resourceId('Microsoft.ServiceFabric/managedClusters', parameters('clusterName')), '2021-07-01-preview').clusterId,'/providers/Microsoft.Compute/virtualMachineScaleSets/',parameters('nodeType2Name'))]",
                                  "timeGrain": "PT1M",
                                  "statistic": "Average",
                                  "timeWindow": "PT30M",
                                  "timeAggregation": "Average",
                                  "operator": "LessThan",
                                  "threshold": 40
                                },
                                "scaleAction": {
                                  "direction": "Decrease",
                                  "type": "ChangeCount",
                                  "value": "1",
                                  "cooldown": "PT5M"
                                }
                            }
                            ]
                    }
                    ]
                }
            }
        
    ]                           
```

Puede descargar esta [plantilla de ARM para habilitar el escalado automático](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/SF-Managed-Standard-SKU-2-NT-Autoscale/sfmc-deploy-autoscale.json) que contiene el ejemplo anterior.


## <a name="view-configured-autoscale-definitions-of-your-managed-cluster-resource"></a>Visualización de las definiciones de escalado automático configuradas del recurso de clúster administrado

Puede ver la configuración de escalado automático establecida mediante [Azure Resource Explorer](https://resources.azure.com/).

1) Vaya a [Azure Resource Explorer](https://resources.azure.com/)

2) Vaya a `subscriptions` -> `SubscriptionName` -> `resource group` -> `microsoft.insights` -> `autoscalesettings` -> nombre de la directiva de escalado automático: por ejemplo, `sfmc01-NT2`. 

   Se ve algo parecido a esto en el árbol de navegación:

   ![Vista de árbol de ejemplo de Azure Resource Explorer][autoscale-are-tree]


3) En el lado derecho puede ver la definición completa de este valor de escalado automático. 

   En este ejemplo el escalado automático se configura con una regla de escalabilidad horizontal y de reducción horizontal basada en un porcentaje de CPU.

   ![Detalles de escalado automático del tipo de nodo de ejemplo de Azure Resource Explorer][autoscale-nt-details]



## <a name="troubleshooting"></a>Solución de problemas

Hay varias cosas que deben tenerse en cuenta: 

* Revise los eventos de escalado automático que se desencadenan en los tipos de nodo secundarios de los clústeres administrados

   1) Vaya al registro de actividad del clúster
   2) Revise el registro de actividad de la operación de escalado y reducción vertical completada

* ¿Cuántas máquinas virtuales hay configuradas para el tipo de nodo? ¿La carga de trabajo se produce en todas ellas o solo en algunas?

* ¿Son los umbrales de reducción y escalado horizontal suficientemente diferentes?

   Imagine que establece una regla de escalado horizontal cuando el promedio de uso de la CPU es mayor del 50 % durante más de cinco minutos y de reducción horizontal cuando el promedio de uso de la CPU es inferior al 50 %. Este valor puede provocar un problema de "oscilación" cuando el uso de la CPU está cerca del umbral, con acciones de escalado que aumentan y disminuyen constantemente el tamaño del conjunto. Debido a este valor, el servicio de escalado automático intenta evitar una "oscilación", lo que puede resultar en la no realización del escalado. Por tanto, asegúrese de que los umbrales de escalabilidad y reducción horizontal son lo suficiente diferentes como para permitir un cierto espacio entre el escalado.

* ¿Puede escalar o reducir horizontalmente un tipo de nodo?
   Ajuste el número de nodos en el nivel de tipo de nodo y asegúrese de que la operación se completa correctamente. [Escalado de un tipo de nodo en un clúster administrado](./how-to-managed-cluster-modify-node-type.md#scale-a-node-type-manually-with-a-template)

* Vea los recursos Microsoft.ServiceFabric/managedclusters/nodetypes y Microsoft.Insights en Azure Resource Explorer

   Azure Resource Explorer es una herramienta indispensable de solución de problemas que muestra el estado de los recursos de Azure Resource Manager. Haga clic en la suscripción y examine el grupo de recursos que se están diagnosticando. En el proveedor de recursos `ServiceFabric/managedclusters/clustername`, busque en `NodeTypes` los tipos de nodo que ha creado y compruebe las propiedades para validar que `provisioningState`es `Succeeded`. Luego vaya al proveedor de recursos Microsoft.Insights en `clustername` y compruebe que las reglas de escalado automático estén bien. 

* ¿Los valores de métrica emitidos son los esperados?
   Use el [módulo `Get-AzMetric` de PowerShell para obtener los valores de métrica de un recurso](/powershell/module/az.monitor/get-azmetric) y revise


Una vez que haya realizado estos pasos, si sigue teniendo problemas con el escalado automático, puede probar los recursos siguientes: [Registro de una solicitud de soporte técnico](./service-fabric-support.md#create-an-azure-support-request). Esté preparado para compartir la plantilla y una vista de los datos de rendimiento.


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Obtenga información sobre la compatibilidad de escalado automático de Azure Monitor](../azure-monitor/autoscale/autoscale-overview.md)
> [!div class="nextstepaction"]
> [Revise métricas en Azure Monitor](../azure-monitor/essentials/data-platform-metrics.md)
> [!div class="nextstepaction"]
> [Opciones de configuración del clúster administrado de Service Fabric](how-to-managed-cluster-configuration.md)

[autoscale-are-tree]: ./media/how-to-managed-cluster-autoscale/autoscale-are-tree.png
[autoscale-nt-details]: ./media/how-to-managed-cluster-autoscale/autoscale-nt-details.png


