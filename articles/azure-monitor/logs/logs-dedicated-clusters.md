---
title: Clústeres dedicados de registros de Azure Monitor
description: Los clientes que ingieren más de 1 TB al día de datos de supervisión pueden usar clústeres dedicados en lugar de compartidos.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 07/29/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: e9385787dd48e3354d3359cf7991492b8f813f0c
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445211"
---
# <a name="azure-monitor-logs-dedicated-clusters"></a>Clústeres dedicados de registros de Azure Monitor

Los clústeres dedicados de registros de Azure Monitor son una opción de implementación que habilita las funcionalidades avanzadas para los clientes de registros de Azure Monitor. Los clientes pueden seleccionar cuáles de sus áreas de trabajo de Log Analytics deben hospedarse en clústeres dedicados.

Los clústeres dedicados requieren que los clientes confirmen el uso de una capacidad de al menos 1 TB de ingesta de datos al día. Puede migrar un área de trabajo existente a un clúster dedicado sin pérdida de datos ni interrupción del servicio. 

Las funcionalidades que requieren clústeres dedicados son las siguientes:

- **[Claves administradas por el cliente](../logs/customer-managed-keys.md)** : cifre los datos del clúster mediante las claves proporcionadas y controladas por el cliente.
- **[Caja de seguridad](../logs/customer-managed-keys.md#customer-lockbox-preview)** : permite controlar las solicitudes de acceso a sus datos de los ingenieros de soporte técnico de Microsoft.
- **[Cifrado doble](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption)** : protege en caso de que uno de los algoritmos o claves de cifrado puedan estar en peligro. En este caso, la capa adicional de cifrado también protege los datos.
- **[Zonas de disponibilidad](../../availability-zones/az-overview.md)** : proteja los datos de los errores del centro de datos con Availability Zones en un clúster dedicado. Las zonas de disponibilidad son centros de datos en ubicaciones físicas independientes y están equipadas con alimentación, refrigeración y redes independientes. Esta infraestructura independiente y la separación física de zonas hace que un incidente sea mucho menos probable, ya que el área de trabajo puede depender de los recursos de cualquiera de las zonas.
- **[Varias áreas de trabajo](../logs/cross-workspace-query.md)** : si un cliente usa más de un área de trabajo para producción, puede que tenga sentido usar un clúster dedicado. Las consultas entre áreas de trabajo se ejecutarán más rápido si todas las áreas de trabajo están en el mismo clúster. Puede ser más rentable también usar un clúster dedicado, ya que los niveles de compromiso asignados tienen en cuenta toda la ingesta del clúster y se aplican a todas sus áreas de trabajo, incluso si algunas de ellas son pequeñas y no son válidas para el descuento de nivel de compromiso.



## <a name="management"></a>Administración 

Los clústeres dedicados se administran con un recurso de Azure que representa clústeres de registro de Azure Monitor. Todas las operaciones se realizan en este recurso mediante PowerShell o la API de REST.

Una vez que se crea el clúster, se puede configurar y se le pueden vincular áreas de trabajo. Cuando un área de trabajo está vinculada a un clúster, los nuevos datos que se envían al área de trabajo residen en el clúster. Solo se pueden vincular al clúster las áreas de trabajo que se encuentran en la misma región que el clúster. Las áreas de trabajo se pueden desvincular de un clúster con algunas limitaciones. En este artículo se incluyen más detalles sobre estas limitaciones. 

Los datos ingeridos en clústeres dedicados se cifran dos veces, una vez en el nivel de servicio mediante claves administradas por Microsoft o [claves administradas por el cliente](../logs/customer-managed-keys.md), y una vez en el nivel de infraestructura mediante dos algoritmos de cifrado diferentes y dos claves diferentes. El [doble cifrado](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) sirve de protección en caso de que uno de los algoritmos o claves de cifrado puedan estar en peligro. En este caso, la capa adicional de cifrado también protege los datos. El clúster dedicado también le permite proteger los datos con un control de [caja de seguridad](../logs/customer-managed-keys.md#customer-lockbox-preview).

Todas las operaciones en el nivel de clúster requieren el permiso de acción `Microsoft.OperationalInsights/clusters/write` en el clúster. Este permiso se puede conceder a través del rol Propietario o Colaborador que contiene la acción `*/write` o a través del rol Colaborador de Log Analytics que contiene la acción `Microsoft.OperationalInsights/*`. Para obtener más información sobre los permisos de Log Analytics, consulte [Administración del acceso a los datos de registro y las áreas de trabajo en Azure Monitor](./manage-access.md). 


## <a name="cluster-pricing-model"></a>Modelo de precios de clúster

Los clústeres dedicados de Log Analytics usan un modelo de precios en un nivel de compromiso de al menos 500 GB/día. Cualquier uso por encima del nivel se facturará a una tarifa efectiva por GB de ese nivel de compromiso.  La información de precios del plan de compromiso está disponible en la [página de precios de Azure Monitor]( https://azure.microsoft.com/pricing/details/monitor/).  

El nivel de compromiso del clúster se configura mediante programación con Azure Resource Manager usando el parámetro `Capacity` en `Sku`. `Capacity` se especifica en unidades de GB y puede tener valores de 500, 1000, 2000 o 5000 GB/día.

Hay dos modos de facturación para el uso en un clúster. Estos pueden especificarse mediante el parámetro `billingType` al configurar el clúster. 

1. **Clúster (predeterminado)** : la facturación de los datos ingeridos se realiza en el nivel de clúster. Las cantidades de datos ingeridas desde cada área de trabajo asociada a un clúster se suman para calcular la factura diaria del clúster. 

2. **Áreas de trabajo**: los costos del nivel de compromiso del clúster se atribuyen proporcionalmente a las áreas de trabajo del clúster, según el volumen de ingesta de datos de cada área de trabajo (después de tener en cuenta las asignaciones por nodo de [Azure Security Center](../../security-center/index.yml) para cada área de trabajo). Estos detalles completos de este modelo de precios se explican [aquí](./manage-cost-storage.md#log-analytics-dedicated-clusters). 

Si el área de trabajo usa el plan de tarifa heredado por nodo, cuando esté vinculado a un clúster se le facturará en función de los datos ingeridos en relación con el nivel de compromiso en lugar de por nodo. Se seguirán aplicando las asignaciones de datos por nodo de Azure Security Center.

Encontrará más detalles sobre la facturación de los clústeres dedicados de Log Analytics [aquí](./manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="asynchronous-operations-and-status-check"></a>Operaciones asincrónicas y comprobación de estado

Algunos de los pasos de configuración se ejecutan de forma asincrónica porque no se pueden completar rápidamente. El estado de la respuesta puede ser uno de los siguientes: *EnCurso*, *Actualizando*, *Eliminando*, *Correcto* o *Incorrecto*, incluido el código de error. Cuando se usa REST, la respuesta devuelve inicialmente un código de estado HTTP 202 (Aceptado) y un encabezado con la propiedad Azure-AsyncOperation:

```JSON
"Azure-AsyncOperation": "https://management.azure.com/subscriptions/subscription-id/providers/Microsoft.OperationalInsights/locations/region-name/operationStatuses/operation-id?api-version=2021-06-01"
```

Puede obtener el estado de la operación asincrónica si envía una solicitud GET al valor del encabezado Azure-AsyncOperation:

```rest
GET https://management.azure.com/subscriptions/subscription-id/providers/microsoft.operationalInsights/locations/region-name/operationstatuses/operation-id?api-version=2021-06-01
Authorization: Bearer <token>
```

## <a name="create-a-dedicated-cluster"></a>Creación de un clúster dedicado

Debe especificar las siguientes propiedades al crear un nuevo clúster dedicado:

- **Nombre del clúster**: Se usa con fines administrativos. Los usuarios no se exponen a este nombre.
- **ResourceGroupName**: grupo de recursos para el clúster dedicado. Debe usar un grupo de recursos de TI central, ya que los clústeres suelen compartirse entre muchos equipos dentro de una organización. Para más información sobre el diseño, consulte [Diseño de la implementación de registros de Azure Monitor](../logs/design-logs-deployment.md).
- **Ubicación**: Un clúster se encuentra en una región de Azure específica. Solo las áreas de trabajo que se encuentran en esta región se pueden vincular a este clúster.
- **SkuCapacity**: debe especificar el nivel de compromiso (SKU) al crear un recurso de clúster. El nivel de compromiso se puede establecer en 500, 1000, 2000 o 5000 GB/día. Para obtener más información sobre los costos del clúster, consulte [Administración de costos de clústeres de Log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters). 
 

> [!NOTE]
> Los niveles de compromiso se denominaban anteriormente "reservas de capacidad". 

Después de crear el recurso cluster, puede editar propiedades adicionales, como *sku*, *keyVaultProperties o *billingType*. Vea más detalles a continuación.

Puede tener hasta dos clústeres activos por suscripción por región. Si se elimina el clúster, se sigue reservando durante 14 días. Puede tener hasta cuatro clústeres reservados por suscripción por región (activos o eliminados recientemente).

> [!WARNING]
> La creación del clúster desencadena la asignación y el aprovisionamiento de recursos. Esta operación puede tardar varias horas en completarse. Se recomienda ejecutarla de forma asincrónica.

La cuenta de usuario que crea los clústeres debe tener el permiso de creación de recursos de Azure estándar (`Microsoft.Resources/deployments/*`) y el permiso de escritura de clúster (`Microsoft.OperationalInsights/clusters/write`) teniendo en sus asignaciones de roles esta acción específica, `Microsoft.OperationalInsights/*` o `*/write`.

**PowerShell**

```powershell
New-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name} -Location {region-name} -SkuCapacity {daily-ingestion-gigabyte} -AsJob

# Check when the job is done
Get-Job -Command "New-AzOperationalInsightsCluster*" | Format-List -Property *
```

**REST API**

*Call* 

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
Content-type: application/json

{
  "identity": {
    "type": "systemAssigned"
    },
  "sku": {
    "name": "capacityReservation",
    "Capacity": 500
    },
  "properties": {
    "billingType": "Cluster",
    },
  "location": "<region>",
}
```

*Respuesta*

Debe ser 202 (Aceptado) y un encabezado.



### <a name="check-cluster-provisioning-status"></a>Comprobación del estado de aprovisionamiento del clúster

El aprovisionamiento del clúster de Log Analytics tarda un tiempo en completarse. Use uno de los métodos siguientes para comprobar el estado de aprovisionamiento:

**PowerShell**

Ejecute *Get-AzOperationalInsightsCluster* con el nombre del grupo de recursos y compruebe la propiedad *ProvisioningState*. El valor es *ProvisioningAccount* mientras se realiza el aprovisionamiento y *Succeeded* una vez que se completa. Copie el valor de la dirección URL de Azure-AsyncOperation de la respuesta y siga la comprobación del estado de operaciones asincrónicas.


  ```powershell
  Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} 
  ```

 
**REST API**

Envíe una solicitud GET en el recurso de clúster y examine el valor de *provisioningState*. El valor es *ProvisioningAccount* mientras se realiza el aprovisionamiento y *Succeeded* una vez que se completa.

  ```rest
  GET https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
  Authorization: Bearer <token>
  ```

  **Respuesta**

  ```json
  {
    "identity": {
      "type": "SystemAssigned",
      "tenantId": "tenant-id",
      "principalId": "principal-id"
    },
    "sku": {
      "name": "capacityreservation",
      "capacity": 500
    },
    "properties": {
      "provisioningState": "ProvisioningAccount",
      "clusterId": "cluster-id",
      "billingType": "Cluster",
      "lastModifiedDate": "last-modified-date",
      "createdDate": "created-date",
      "isDoubleEncryptionEnabled": false,
      "isAvailabilityZonesEnabled": false,
      "capacityReservationProperties": {
        "lastSkuUpdate": "last-sku-modified-date",
        "minCapacity": 500
      }
    },
    "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
    "name": "cluster-name",
    "type": "Microsoft.OperationalInsights/clusters",
    "location": "cluster-region"
  }
  ```

El GUID *principalId* se genera desde el servicio de identidad administrada al crear el clúster.

---



## <a name="link-a-workspace-to-a-cluster"></a>Vinculación del área de trabajo a un clúster

Cuando un área de trabajo de Log Analytics está vinculada a un clúster dedicado, los nuevos datos que se ingieren en el área de trabajo se enrutan al nuevo clúster, mientras que los datos existentes permanecen en el clúster existente. Si el clúster dedicado se cifra mediante claves administradas por el cliente (CMK), solo se cifrarán los datos nuevos con la clave. El sistema deduce esta diferencia, por lo que puede consultar el área de trabajo como de costumbre mientras el sistema realiza consultas entre clústeres en segundo plano.

Un clúster puede vincularse con hasta 1000 áreas de trabajo. Las áreas de trabajo vinculadas se encuentran en la misma región que el clúster. Para proteger el back-end del sistema y evitar la fragmentación de los datos, un área de trabajo no se puede vincular a un clúster más de dos veces al mes.

Para realizar la operación de vinculación, debe tener permisos de "escritura" tanto en el área de trabajo como en el recurso cluster:

- En el área de trabajo: *Microsoft.OperationalInsights/workspaces/write*
- En el recurso cluster: *Microsoft.OperationalInsights/clusters/write*

Además de los aspectos de facturación, el área de trabajo vinculada mantiene su propia configuración, como la duración de la retención de datos.

El área de trabajo y el clúster pueden estar en distintas suscripciones. Es posible que el área de trabajo y el clúster estén en inquilinos diferentes si se usa Azure Lighthouse para asignar ambos a un único inquilino.

Vincular un área de trabajo puede realizarse una vez completado el aprovisionamiento del clúster de Log Analytics.

> [!WARNING]
> Vincular un área de trabajo a un clúster requiere sincronizar varios componentes de back-end y garantizar la hidratación de la memoria caché. Dado que esta operación puede tardar hasta dos horas en completarse, debe ejecutarla de forma asincrónica.


**PowerShell**

Use el comando de PowerShell siguiente para vincular un clúster:

```powershell
# Find cluster resource ID
$clusterResourceId = (Get-AzOperationalInsightsCluster -ResourceGroupName {resource-group-name} -ClusterName {cluster-name}).id

# Link the workspace to the cluster
Set-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster -WriteAccessResourceId $clusterResourceId -AsJob

# Check when the job is done
Get-Job -Command "Set-AzOperationalInsightsLinkedService" | Format-List -Property *
```

**REST API**

Use la llamada de REST siguiente para vincular un clúster:

*Envío*

```rest
PUT https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>/linkedservices/cluster?api-version=2021-06-01 
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "WriteAccessResourceId": "/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/clusters/<cluster-name>"
    }
}
```

*Respuesta*

202 (Aceptado) y encabezado.

---

### <a name="check-workspace-link-status"></a>Comprobación del estado de vinculación del área de trabajo
  
Cuando un clúster se configura con claves administradas por el cliente, los datos ingeridos en las áreas de trabajo después de finalizar la operación de vinculación se almacenan cifrados con la clave administrada. La operación de vínculo del área de trabajo puede tardar hasta 90 minutos en completarse. Puede comprobar el estado de la operación de dos maneras:

- Copie el valor de la dirección URL de Azure-AsyncOperation de la respuesta y siga la comprobación del estado de operaciones asincrónicas.
- Realice una operación Get en el área de trabajo y observe si la propiedad *clusterResourceId* presente en la respuesta en la sección *features*.

**CLI**
```azurecli
az monitor log-analytics workspace show --resource-group "resource-group-name" --workspace-name "workspace-name"
```
**PowerShell**

```powershell
Get-AzOperationalInsightsWorkspace -ResourceGroupName "resource-group-name" -Name "workspace-name"
```

**REST API**

*Call*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>?api-version=2021-06-01
Authorization: Bearer <token>
```

*Respuesta*

```json
{
  "properties": {
    "source": "Azure",
    "customerId": "workspace-name",
    "provisioningState": "Succeeded",
    "sku": {
      "name": "pricing-tier-name",
      "lastSkuUpdate": "Tue, 28 Jan 2020 12:26:30 GMT"
    },
    "retentionInDays": 31,
    "features": {
      "legacy": 0,
      "searchVersion": 1,
      "enableLogAccessUsingOnlyResourcePermissions": true,
      "clusterResourceId": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name"
    },
    "workspaceCapping": {
      "dailyQuotaGb": -1.0,
      "quotaNextResetTime": "Tue, 28 Jan 2020 14:00:00 GMT",
      "dataIngestionStatus": "RespectQuota"
    }
  },
  "id": "/subscriptions/subscription-id/resourcegroups/resource-group-name/providers/microsoft.operationalinsights/workspaces/workspace-name",
  "name": "workspace-name",
  "type": "Microsoft.OperationalInsights/workspaces",
  "location": "region"
}
```

---


## <a name="change-cluster-properties"></a>Comprobación de las propiedades del clúster

Después de crear el recurso de clúster y de que esté totalmente aprovisionado, puede editar propiedades adicionales en el nivel de clúster mediante PowerShell o la API REST. Las propiedades adicionales que se pueden establecer después de aprovisionar el clúster son las siguientes:

- **keyVaultProperties**: contiene la clave de Azure Key Vault con los parámetros siguientes: *KeyVaultUri*, *KeyName*, *KeyVersion*. Consulte [Actualización del clúster con detalles del identificador de clave](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details).
- **Identidad**: identidad que se usa para autenticarse en la instancia de Key Vault. Puede ser asignada por el sistema o por el usuario.
- **billingType**: atribución de facturación para el recurso de clúster y sus datos. Incluye los valores siguientes:
  - **Cluster (predeterminado)** : los costos para el clúster se atribuyen al recurso de clúster.
  - **Workspaces**: los costos del nivel de compromiso para el clúster se atribuyen proporcionalmente a las áreas de trabajo del clúster. En este caso, se factura una parte del uso del recurso de clúster si el total de datos ingeridos del día está por debajo del nivel de compromiso. Consulte [Clústeres dedicados de Log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters) para más información sobre el modelo de precios del clúster.


>[!IMPORTANT]
>La actualización del clúster no debe incluir los detalles de identidad y de identificador de clave en la misma operación. Si necesita actualizar ambos valores, la actualización debe realizarse en dos operaciones consecutivas.

> [!NOTE]
> La propiedad *billingType* no se admite en PowerShell.

## <a name="get-all-clusters-in-resource-group"></a>Obtención de todos los clústeres de un grupo de recursos

**CLI**

```azurecli
az monitor log-analytics cluster list --resource-group "resource-group-name"
```
**PowerShell**

```powershell
Get-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name"
```

**REST API**

*Call*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters?api-version=2021-06-01
Authorization: Bearer <token>
```

*Respuesta*
  
```json
{
  "value": [
    {
      "identity": {
        "type": "SystemAssigned",
        "tenantId": "tenant-id",
        "principalId": "principal-id"
      },
      "sku": {
        "name": "capacityreservation",
        "capacity": 500
      },
      "properties": {
        "provisioningState": "Succeeded",
        "clusterId": "cluster-id",
        "billingType": "Cluster",
        "lastModifiedDate": "last-modified-date",
        "createdDate": "created-date",
        "isDoubleEncryptionEnabled": false,
        "isAvailabilityZonesEnabled": false,
        "capacityReservationProperties": {
          "lastSkuUpdate": "last-sku-modified-date",
          "minCapacity": 500
        }
      },
      "id": "/subscriptions/subscription-id/resourceGroups/resource-group-name/providers/Microsoft.OperationalInsights/clusters/cluster-name",
      "name": "cluster-name",
      "type": "Microsoft.OperationalInsights/clusters",
      "location": "cluster-region"
    }
  ]
}
```

---



## <a name="get-all-clusters-in-subscription"></a>Obtención de todos los clústeres de una suscripción

**CLI**

```azurecli
az monitor log-analytics cluster list
```

**PowerShell**

```powershell
Get-AzOperationalInsightsCluster
```
**REST API**

*Call*

```rest
GET https://management.azure.com/subscriptions/<subscription-id>/providers/Microsoft.OperationalInsights/clusters?api-version=2021-06-01
Authorization: Bearer <token>
```
    
*Respuesta*
    
Lo mismo que para el "clúster de un grupo de recursos", pero en el ámbito de la suscripción.

---


## <a name="update-commitment-tier-in-cluster"></a>Actualización del nivel de compromiso en el clúster

Cuando cambie el volumen de datos en las áreas de trabajo vinculadas con el tiempo y desee actualizar el nivel de compromiso adecuadamente. El nivel se especifica en unidades de GB y puede tener valores de 500, 1000, 2000 o 5000 GB/día. Tenga en cuenta que no tiene que proporcionar el cuerpo completo de la solicitud de REST, pero debe incluir la SKU.

**CLI**


```azurecli
az monitor log-analytics cluster update --name "cluster-name" --resource-group "resource-group-name" --sku-capacity 500
```

### <a name="powershell"></a>PowerShell

```powershell
Update-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name" -SkuCapacity 500
```

### <a name="rest-api"></a>API DE REST

*Call*

```rest
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
Content-type: application/json

{
  "sku": {
    "name": "capacityReservation",
    "Capacity": 2000
  }
}
```

---



### <a name="update-billingtype-in-cluster"></a>Actualización de billingType en el clúster

La propiedad *billingType* determina la atribución de facturación para el clúster y sus datos:
- *Clúster* (valor predeterminado): la facturación se atribuye al recurso de clúster.
- *Áreas de trabajo*: la facturación se atribuye a las áreas de trabajo vinculadas de forma proporcional. Cuando el volumen de datos de todas las áreas de trabajo está por debajo del nivel de compromiso, el volumen restante se atribuye al clúster.

**REST**

*Call*

```rest
PATCH https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
Content-type: application/json

{
  "properties": {
    "billingType": "Workspaces",
    }  
}
```

### <a name="unlink-a-workspace-from-cluster"></a>Desvinculación de un área de trabajo de un clúster

Puede desvincular un área de trabajo de un clúster. Después de desvincular un área de trabajo del clúster, los nuevos datos asociados a esta área de trabajo no se envían al clúster dedicado. Además, la facturación del área de trabajo ya no se realiza a través del clúster. Los datos antiguos del área de trabajo desvinculada podrían quedar en el clúster. Si estos datos están cifrados mediante claves administradas por el cliente (CMK), se conservan los secretos de Key Vault. El sistema abstrae este cambio de los usuarios de Log Analytics. Los usuarios pueden simplemente consultar el área de trabajo como de costumbre. El sistema realiza consultas entre clústeres en el back-end según sea necesario sin indicarlo a los usuarios.  

> [!WARNING] 
> Hay un límite de dos operaciones de vinculación para un área de trabajo específica dentro de un mes. Dedique tiempo a analizar y planear las acciones de desvinculación en consecuencia.

**CLI**

```azurecli
az monitor log-analytics workspace linked-service delete --resource-group "resource-group-name" --workspace-name "workspace-name" --name cluster
```

**PowerShell**

Use el comando de PowerShell siguiente para desvincular un área de trabajo de un clúster:

```powershell
# Unlink a workspace from cluster
Remove-AzOperationalInsightsLinkedService -ResourceGroupName {resource-group-name} -WorkspaceName {workspace-name} -LinkedServiceName cluster
```

---




## <a name="delete-cluster"></a>Eliminación de clúster

Debe desvincular todas las áreas de trabajo de un clúster dedicado antes de eliminarlo. Esto requiere permisos de *escritura* en el recurso de clúster para realizar esta operación. 

Una vez que se elimina el recurso de clúster, el clúster físico entra en un proceso de purga y eliminación. La eliminación de un clúster elimina todos los datos que se han almacenado en el clúster. Los datos podrían ser de áreas de trabajo que se vincularon al clúster en el pasado.

Un recurso de clúster que se eliminó durante los últimos 14 días se encuentra en estado de eliminación temporal y se puede recuperar. Dado que todas las áreas de trabajo se han desasociado del recurso de clúster con eliminación del recurso de clúster, debe volver a asociar las áreas de trabajo después de la recuperación. El usuario no puede realizar la operación de recuperación. Póngase en contacto con el canal de Microsoft o con soporte técnico si tiene solicitudes de recuperación.

Dentro de los 14 días posteriores a la eliminación, el nombre del recurso de clúster está reservado y otros recursos no pueden usarlo.

> [!WARNING] 
> Hay un límite de tres clústeres por suscripción. Dentro de este recuento se incluyen los clústeres activos y los eliminados temporalmente. Los clientes no deben crear procedimientos recurrentes que creen y eliminen clústeres. Esta acción tiene un impacto significativo en los sistemas de back-end de Log Analytics.

**PowerShell**

Use el comando de PowerShell siguiente para eliminar un clúster:

```powershell
Remove-AzOperationalInsightsCluster -ResourceGroupName "resource-group-name" -ClusterName "cluster-name"
```

**REST API**

Use la llamada de REST siguiente para eliminar un clúster:

```rest
DELETE https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.OperationalInsights/clusters/<cluster-name>?api-version=2021-06-01
Authorization: Bearer <token>
```

  **Respuesta**

  200 OK

---



## <a name="limits-and-constraints"></a>Límites y restricciones

- Se puede crear un máximo de dos clústeres activos en cada región y suscripción.

- Se puede crear un número máximo de cuatro clústeres reservados (activos o eliminados recientemente) en cada región y suscripción.

- Se pueden vincular un máximo de 1000 áreas de trabajo de Log Analytics a un clúster.

- Se permite un máximo de dos operaciones de vinculación de áreas de trabajo en un área de trabajo determinada en un período de 30 días.

- Actualmente no se admite el traslado de un clúster a otro grupo de recursos o a otra suscripción.

- La actualización del clúster no debe incluir los detalles de identidad y de identificador de clave en la misma operación. En caso de que deba actualizar ambos valores, la actualización debe realizarse en dos operaciones consecutivas.

- La caja de seguridad no está disponible actualmente en China. 

- El [cifrado doble](../../storage/common/storage-service-encryption.md#doubly-encrypt-data-with-infrastructure-encryption) se configura automáticamente para los clústeres creados a partir de octubre de 2020 en las regiones compatibles. Puede comprobar si el clúster está configurado para el cifrado doble mediante el envío de una solicitud GET en el clúster y observando que el valor `isDoubleEncryptionEnabled` sea `true` para los clústeres con el cifrado doble habilitado. 
  - Si crea un clúster y recibe un error que indica que la región no admite el cifrado doble para clústeres, puede crear el clúster sin cifrado doble agregando `"properties": {"isDoubleEncryptionEnabled": false}` en el cuerpo de la solicitud REST.
  - La configuración de cifrado doble no se puede cambiar después de crear el clúster.

## <a name="troubleshooting"></a>Solución de problemas

- Si se produce un error de conflicto al crear un clúster, es posible que haya eliminado el clúster en los últimos 14 días y que esté en un estado de eliminación temporal. El nombre del clúster permanece reservado durante el período de eliminación temporal y no se puede crear un nuevo clúster con ese nombre. El nombre se libera después del período de eliminación temporal cuando el clúster se elimina de forma permanente.

- Si actualiza el clúster mientras este está en el estado de aprovisionamiento o actualización, se producirá un error en la actualización.

- Algunas operaciones son prolongadas y pueden tardar un tiempo en completarse. Estas son: *cluster create*, *cluster key update* y *cluster delete*. Puede comprobar el estado de la operación de dos maneras:
  - Cuando utilice REST, copie el valor de la dirección URL de Azure-AsyncOperation de la respuesta y siga la [comprobación del estado de operaciones asincrónicas](#asynchronous-operations-and-status-check).
  - Envíe una solicitud GET al clúster o al área de trabajo y observe la respuesta. Por ejemplo, un área de trabajo desvinculada no tendrá el elemento *clusterResourceId* en la sección *features*.

- Se producirá un error en la vinculación del área de trabajo al clúster si está vinculada a otro clúster.

## <a name="error-messages"></a>Mensajes de error
  
### <a name="cluster-create"></a>Creación de un clúster

-  400: El nombre del clúster no es válido. El nombre del clúster puede contener los caracteres a-z, A-Z, 0-9 y una longitud de 3 a 63.
-  400: El cuerpo de la solicitud es NULL o tiene un formato incorrecto.
-  400: El nombre de SKU no es válido. Establezca el nombre de SKU en capacityReservation.
-  400: Se proporcionó Capacity, pero la SKU no es capacityReservation. Establezca el nombre de la SKU en capacityReservation.
-  400: Falta Capacity en la SKU. Establezca el valor de Capacity en 500, 1000, 2000 o 5000 GB/día.
-  400: Capacity está bloqueado durante 30 días. Se permite la reducción de la capacidad 30 días después de la actualización.
-  400: No se estableció ninguna SKU. Establezca el nombre de la SKU en capacityReservation y el valor de Capacity en 500, 1000, 2000 o 5000 GB/día.
-  400: Identity es NULL o está vacío. Establezca Identity en el tipo systemAssigned.
-  400: KeyVaultProperties se estableció en la creación. Actualice KeyVaultProperties después de la creación del clúster.
-  400: No se puede ejecutar la operación ahora. La operación asincrónica está en un estado distinto del correcto. El clúster debe completar su operación antes de realizar cualquier operación de actualización.

### <a name="cluster-update"></a>Actualización del clúster:

-  400: El clúster está en estado de eliminación. La operación asincrónica está en curso. El clúster debe completar su operación antes de realizar cualquier operación de actualización.
-  400: KeyVaultProperties no está vacío, pero tiene un formato incorrecto. Consulte [actualización de identificador de clave](../logs/customer-managed-keys.md#update-cluster-with-key-identifier-details).
-  400: No se pudo validar la clave en Key Vault. Podría deberse a la falta de permisos o a que la clave no existe. Verifique que [estableció la clave y la directiva de acceso](../logs/customer-managed-keys.md#grant-key-vault-permissions) en Key Vault.
-  400: No se puede recuperar la clave. Key Vault debe establecerse para la eliminación temporal y protección de purga. Consulte la [documentación de Key Vault](../../key-vault/general/soft-delete-overview.md).
-  400: No se puede ejecutar la operación ahora. Espere a que se complete la operación asincrónica e inténtelo de nuevo.
-  400: El clúster está en estado de eliminación. Espere a que se complete la operación asincrónica e inténtelo de nuevo.

### <a name="cluster-get"></a>Obtención del clúster

 -  404: No se encontró el clúster, es posible que se haya eliminado. Si intenta crear un clúster con ese nombre y obtiene un conflicto, el clúster se encuentra en eliminación temporal durante 14 días. Puede ponerse en contacto con soporte técnico para recuperarlo o usar otro nombre para crear un nuevo clúster. 

### <a name="cluster-delete"></a>Eliminación del clúster:

 -  409: No se puede eliminar un clúster mientras está en estado de aprovisionamiento. Espere a que se complete la operación asincrónica e inténtelo de nuevo.

### <a name="workspace-link"></a>Vinculación del área de trabajo

-  404: No se encuentra el área de trabajo. El área de trabajo que especificó no existe o se eliminó.
-  409: Operación en curso de vinculación o desvinculación del área de trabajo.
-  400: No se encontró el clúster, el clúster que especificó no existe o se eliminó. Si intenta crear un clúster con ese nombre y obtiene un conflicto, el clúster se encuentra en eliminación temporal durante 14 días. Puede ponerse en contacto con el soporte técnico para recuperarlo.

### <a name="workspace-unlink"></a>Desvinculación del área de trabajo
-  404: No se encuentra el área de trabajo. El área de trabajo que especificó no existe o se eliminó.
-  409: Operación en curso de vinculación o desvinculación del área de trabajo.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [facturación de clústeres dedicados de Log Analytics](./manage-cost-storage.md#log-analytics-dedicated-clusters)
- Obtenga más información sobre el [diseño adecuado de áreas de trabajo de Log Analytics](../logs/design-logs-deployment.md)
