---
title: Actualización de clústeres administrados de Azure Service Fabric
description: Más información sobre las opciones para actualizar el clúster administrado de Azure Service Fabric
ms.topic: how-to
ms.date: 08/23/2021
ms.openlocfilehash: 3374a67d2b404234feafd4bc621fd5fef745b3f4
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546704"
---
# <a name="manage-service-fabric-managed-cluster-upgrades"></a>Administración de actualizaciones del clúster administrado de Service Fabric

Un clúster de Azure Service Fabric es un recurso de su propiedad, pero que está parcialmente administrado por Microsoft. Aquí se muestra cuándo y cómo Microsoft actualiza el entorno de ejecución de clúster administrado de Azure Service Fabric.

## <a name="set-upgrade-mode"></a>Establecimiento del modo de actualización

Los clústeres administrados de Azure Service Fabric se han configurado de manera predeterminada para recibir las actualizaciones automáticas de Service Fabric a medida que Microsoft las publica mediante una estrategia de [implementación por oleadas](#wave-deployment-for-automatic-upgrades). Como alternativa, puede configurar las actualizaciones en modo manual y elegir entre una lista de las versiones compatibles actualmente. Puede configurar estas opciones mediante el control *Actualizaciones de Fabric* de Azure Portal o mediante la opción `ClusterUpgradeMode` de la plantilla de implementación del clúster.

## <a name="wave-deployment-for-automatic-upgrades"></a>Implementación por oleadas para actualizaciones automáticas

Con la implementación por oleadas, puede crear una canalización para actualizar los clústeres de prueba, ensayo y producción en secuencia, separados por el "tiempo de preparación" integrado para validar las próximas versiones de Service Fabric antes de que se actualicen los clústeres de producción.

>[!NOTE]
>De manera predeterminada, los clústeres se establecerán en la oleada 0.

Para seleccionar una implementación por oleadas para la actualización automática, determine primero la oleada que asignará al clúster:

* **Oleada 0** (`Wave0`): los clústeres se actualizan en cuanto se publica una nueva compilación de Service fabric.
* **Oleada 1** (`Wave1`): los clústeres se actualizan después de la oleada 0 para permitir un tiempo de simulación mediante "bake". La oleada 1 se produce al menos 7 días después de la oleada 0.
* **Oleada 2** (`Wave2`): los clústeres se actualizan en último lugar para permitir un mayor tiempo de simulación mediante "bake". La oleada 2 se produce al menos 14 días después de la oleada 0.

## <a name="set-the-wave-for-your-cluster"></a>Configuración de las oleadas del clúster

Puede configurar el clúster en una de las oleadas disponibles mediante el control *Actualizaciones de Fabric* de Azure Portal o mediante la opción `ClusterUpgradeMode` de la plantilla de implementación del clúster.

### <a name="azure-portal"></a>Portal de Azure

Con Azure Portal, podrá elegir entre las oleadas automáticas disponibles al crear un clúster de Service Fabric.

:::image type="content" source="./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png" alt-text="Elección entre las diferentes oleadas disponibles al crear un clúster en Azure Portal a partir de las opciones avanzadas":::

También puede alternar entre las diferentes oleadas automáticas disponibles en la sección **Actualizaciones de Fabric** de un recurso de clúster existente.

:::image type="content" source="./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png" alt-text="Selección entre las diferentes oleadas automáticas de la sección &quot;Actualizaciones de Fabric&quot; del recurso de clúster en Azure Portal":::

### <a name="resource-manager-template"></a>Plantilla de Resource Manager

Para cambiar el modo de actualización del clúster mediante una plantilla de Resource Manager, especifique *Automática* o *Manual* en la propiedad `ClusterUpgradeMode` de la definición del recurso *Microsoft.ServiceFabric/clusters*. Si elige actualizaciones manuales, establezca también `clusterCodeVersion` en una [versión de tejido compatible](#query-for-supported-cluster-versions) actualmente.

#### <a name="manual-upgrade"></a>Actualización manual

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedClusters",
  "properties": {
    "clusterUpgradeMode": "Manual",
    "clusterCodeVersion": "8.0.514.9590"
  }
}
```

Después de una implementación correcta de la plantilla, se aplicarán los cambios al modo de actualización del clúster. Si el clúster está en modo manual, la actualización del clúster se iniciará automáticamente.

Las directivas de mantenimiento del clúster (una combinación del estado del nodo y el mantenimiento de todas las aplicaciones que se ejecutan en el clúster) se cumplen mientras dura la actualización. Si no se cumplen las directivas de mantenimiento del clúster, la actualización se revierte.

Si se produce una reversión, debe corregir los problemas que provocaron la reversión e iniciar la actualización de nuevo, siguiendo los mismos pasos que antes.

#### <a name="automatic-upgrade-with-wave-deployment"></a>Actualización automática con implementación por oleadas

Para configurar las actualizaciones automáticas y la implementación por oleadas solo tiene que agregar o comprobar que `ClusterUpgradeMode` está establecido en `Automatic` y que la propiedad `clusterUpgradeCadence` está definida con uno de los valores de oleada enumerados anteriormente en la plantilla de Resource Manager.

```json
{
  "apiVersion": "2021-05-01",
  "type": "Microsoft.ServiceFabric/managedClusters",
  "properties": {
    "clusterUpgradeMode": "Automatic",
    "clusterUpgradeCadence": "Wave1"
  }
}
```

Una vez que implemente la plantilla actualizada, el clúster se inscribirá en la oleada especificada para actualizaciones automáticas.

## <a name="query-for-supported-cluster-versions"></a>Consulta de las versiones de clúster compatibles

Puede usar la [API REST de Azure](/rest/api/azure/) para enumerar todas las versiones del entorno de ejecución de Service Fabric ([clusterVersions](/rest/api/servicefabric/sfrp-api-clusterversions_list)) disponibles para la ubicación y la suscripción especificadas.

También puede consultar las [versiones de Service Fabric](service-fabric-versions.md) para más información sobre las versiones y los sistemas operativos compatibles.

```REST
GET https://<endpoint>/subscriptions/{{subscriptionId}}/providers/Microsoft.ServiceFabric/locations/{{location}}/managedclusterVersions?api-version=2021-05-01

"value": [
  {
    "id": "subscriptions/eec8e14e-b47d-40d9-8bd9-23ff5c381b40/providers/Microsoft.ServiceFabric/locations/eastus2/environments/Windows/managedClusterVersions/7.2.477.9590",
    "name": "7.2.477.9590",
    "type": "Microsoft.ServiceFabric/locations/environments/managedClusterVersions",
    "properties": {
      "supportExpiryUtc": "2021-11-30T00:00:00",
      "osType": "Windows",
      "clusterCodeVersion": "7.2.477.9590"
    }
  },
  {
    "id": "subscriptions/########-####-####-####-############/providers/Microsoft.ServiceFabric/locations/eastus2/environments/Windows/managedClusterVersions/8.0.514.9590",
    "name": "8.0.514.9590",
    "type": "Microsoft.ServiceFabric/locations/environments/managedClusterVersions",
    "properties": {
      "supportExpiryUtc": "9999-12-31T23:59:59.9999999",
      "osType": "Windows",
      "clusterCodeVersion": "8.0.514.9590"
    }
  }
]

```

`supportExpiryUtc` en la salida informa de cuando una versión determinada expira o ha expirado. La última versión no tiene una fecha válida, sino un valor de *9999-12-31T23:59:59.9999999*, lo que significa simplemente que la fecha de expiración no se ha establecido aún.

## <a name="next-steps"></a>Pasos siguientes

* [Personalización de la configuración del clúster administrado de Service Fabric](how-to-managed-cluster-configuration.md)
* Obtenga información sobre [actualizaciones de aplicaciones](service-fabric-application-upgrade.md)

<!--Image references-->
[Upgrade-Wave-Settings]: ./media/how-to-managed-cluster-upgrades/manage-upgrade-wave-settings.png
[New-Cluster-Wave-Settings]: ./media/how-to-managed-cluster-upgrades/portal-new-cluster-upgrade-waves-setting.png
