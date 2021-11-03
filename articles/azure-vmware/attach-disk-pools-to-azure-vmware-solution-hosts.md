---
title: Conexión de grupos de discos a hosts de Azure VMware Solution (versión preliminar)
description: Obtenga información sobre cómo conectar un grupo de discos que se muestra a través de un destino iSCSI como el almacén de datos de VMware de una nube privada de Azure VMware Solution. Una vez configurado el almacén de datos, puede crear volúmenes en él y adjuntarlos a la instancia de VMware.
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f2719b135860f448732f5f36285ef2c33ff0115e
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131071665"
---
# <a name="attach-disk-pools-to-azure-vmware-solution-hosts-preview"></a>Conexión de grupos de discos a hosts de Azure VMware Solution (versión preliminar)

Los [grupos de discos de Azure](../virtual-machines/disks-pools.md) ofrecen almacenamiento en bloque persistente a aplicaciones y cargas de trabajo con el respaldo de Azure Disks. Puede usar discos como almacenamiento persistente para Azure VMware Solution con un coste y un rendimiento óptimos. Por ejemplo, puede escalar verticalmente mediante grupos de discos en lugar de escalar clústeres si hospeda cargas de trabajo de almacenamiento intensivo. También puede usar discos para replicar los datos de los entornos VMware locales o primarios al almacenamiento en disco para el sitio secundario. Para escalar el almacenamiento independientemente de los hosts de Azure VMware Solution, admitimos la utilización de [discos Ultra](../virtual-machines/disks-types.md#ultra-disks), [SSD premium](../virtual-machines/disks-types.md#premium-ssds) y [SSD estándar](../virtual-machines/disks-types.md#standard-ssds) como almacenes de datos.  

>[!IMPORTANT]
>Los grupos de discos de Azure VMware Solution (versión preliminar) se encuentran actualmente en versión preliminar pública.
>Esta versión preliminar se ofrece sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
>Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Los discos administrados de Azure se asocian a una máquina virtual controladora iSCSI implementada en el grupo de recursos de Azure VMware Solution. Los discos se implementan como destinos de almacenamiento en un grupo de discos y cada destino de almacenamiento se muestra como un LUN iSCSI en el destino iSCSI. Puede exponer un grupo de discos como un destino iSCSI conectado a hosts de Azure VMware Solution como un almacén de datos. Un grupo de discos se muestra como un único punto de conexión para todos los discos subyacentes agregados como destinos de almacenamiento. Cada grupo de discos solo puede tener una controladora iSCSI.

En el diagrama se muestra cómo funcionan los grupos de discos con hosts de Azure VMware Solution. Cada controladora iSCSI accede a un disco administrado mediante un protocolo estándar de Azure y los hosts de Azure VMware Solution pueden acceder a la controladora iSCSI a través de iSCSI.


:::image type="content" source="media/disk-pools/azure-disks-attached-to-managed-iscsi-controllers.png" alt-text="Diagrama que muestra cómo funcionan los grupos de discos con hosts de Azure VMware Solution." border="false":::


## <a name="supported-regions"></a>Regiones admitidas

Solo puede conectar el grupo de discos a una nube privada de Azure VMware Solution de la misma región. Para ver una lista de las regiones admitidas, consulte [Regional availability](../virtual-machines/disks-pools.md#regional-availability) (Disponibilidad regional).  Si la nube privada se implementa en una región no compatible, puede volver a implementarla en una región admitida. La nube privada de Azure VMware Solution y la ubicación del grupo de discos proporcionan el mejor rendimiento con una latencia de red mínima.


## <a name="prerequisites"></a>Requisitos previos

- Se identifican los requisitos de escalabilidad y rendimiento de las cargas de trabajo. Para obtener más información, consulte [Planning for Azure disk pools](../virtual-machines/disks-pools-planning.md) (Planificación para grupos de discos de Azure).

- [Nube privada de Azure VMware Solution](deploy-azure-vmware-solution.md) implementada con una [red virtual](deploy-azure-vmware-solution.md#connect-to-azure-virtual-network-with-expressroute). Para más información, consulte [Lista de comprobación de planeación de la red](tutorial-network-checklist.md) y [Configure networking for your VMware private cloud](tutorial-configure-networking.md) (Configuración de redes para la nube privada de VMware). 

   - Si selecciona discos Ultra, use una puerta de enlace de red virtual de ExpressRoute de ultrarrendimiento para la conexión de red del grupo de discos a la nube privada de Azure VMware Solution y, a continuación, [habilite FastPath de ExpressRoute](../expressroute/expressroute-howto-linkvnet-arm.md#configure-expressroute-fastpath).

   - Si selecciona SSD premium o estándar, use una puerta de enlace de red virtual de ExpressRoute estándar (1 Gbps) o de alto rendimiento (2 Gbps) para la conexión de red del grupo de discos a la nube privada de Azure VMware Solution.  

- Debe usar Estándar\_DS##\_v3 para hospedar iSCSI.  Si se encuentra con incidencias de cuota, solicite un aumento en los [límites de cuota de vCPU](../azure-portal/supportability/per-vm-quota-requests.md) por cada serie de máquinas virtuales de Azure para la serie Dsv3.

- Grupo de discos como almacenamiento de respaldo implementado y expuesto como destino iSCSI con cada disco como un LUN individual. Para más información, consulte [Deploy an Azure disk pool](../virtual-machines/disks-pools-deploy.md) (Implementar un grupo de discos de Azure).

   >[!IMPORTANT]
   > El grupo de discos debe implementarse en la misma suscripción que el clúster de VMware y debe estar conectado a la misma VNET que el clúster de VMware.

## <a name="add-a-disk-pool-to-your-private-cloud"></a>Incorporación de un grupo de discos a la nube privada
Puede conectar un grupo de discos que se muestra a través de un destino iSCSI, como el almacén de datos de VMware de una nube privada de Azure VMware Solution.

>[!IMPORTANT]
>Mientras se encuentre en la **Versión preliminar pública**, conecte solo un grupo de discos a un clúster de prueba o que no sea de producción.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Compruebe si la suscripción está registrada en `Microsoft.AVS`.

```azurecli
az provider show -n "Microsoft.AVS" --query registrationState
```

Si todavía no está registrada, regístrela.

```azurecli
az provider register -n "Microsoft.AVS"
```

Compruebe si la suscripción está registrada en `CloudSanExperience` AFEC en Microsoft.AVS.

```azurecli
az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS"
```

Si todavía no está registrada, regístrela.

```azurecli
az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
```

El registro puede tardar aproximadamente 15 minutos en completarse; puede usar el siguiente comando para comprobar el estado:

```azurecli
az feature show --name "CloudSanExperience" --namespace "Microsoft.AVS" --query properties.state
```

>[!TIP]
>Si el registro queda bloqueado en un estado intermedio durante más de 15 minutos antes de completarse, anule el registro y vuelva a registrar la marca.
>
>```azurecli
>az feature unregister --name "CloudSanExperience" --namespace "Microsoft.AVS"
>az feature register --name "CloudSanExperience" --namespace "Microsoft.AVS"
>```

Compruebe si la extensión `vmware ` está instalada. 

```azurecli
az extension show --name vmware
```

Si la extensión ya está instalada, compruebe si la versión es **3.0.0**. Si hay instalada una versión anterior, actualice la extensión.

```azurecli
az extension update --name vmware
```

Si todavía no está instalada, instálela.

```azurecli
az extension add --name vmware
```

### <a name="attach-the-iscsi-lun"></a>Conexión de LUN iSCSI

Cree y adjunte un almacén de datos iSCSI en el clúster de la nube privada de Azure VMware Solution mediante el destino iSCSI proporcionado `Microsoft.StoragePool`. El grupo de discos se conecta a una red virtual por medio de una subred delegada, que se crea con el proveedor de recursos Microsoft.StoragePool/diskPools.  Si la subred no está delegada, se produce un error en la implementación.

```bash
#Initialize input parameters
resourceGroupName='<yourRGName>'
name='<desiredDataStoreName>'
cluster='<desiredCluster>'
privateCloud='<privateCloud>'
lunName='<desiredLunName>'

az vmware datastore disk-pool-volume create --name $name --resource-group $resourceGroupName --cluster $cluster --private-cloud $privateCloud --target-id /subscriptions/11111111-1111-1111-1111-111111111111/resourceGroups/ResourceGroup1/providers/Microsoft.StoragePool/diskPools/mpio-diskpool/iscsiTargets/mpio-iscsi-target --lun-name $lunName
```

>[!TIP]
>Puede mostrar la ayuda en los almacenes de datos.
>
>   ```azurecli
>   az vmware datastore -h
>   ```


Para confirmar que la conexión se ha realizado correctamente, puede usar los siguientes comandos:

Muestre los detalles de un almacén de datos iSCSI en un clúster de nube privada.

```azurecli
az vmware datastore show --name MyCloudSANDatastore1 --resource-group MyResourceGroup --cluster -Cluster-1 --private-cloud MyPrivateCloud
```

Enumere todos los almacenes de datos de un clúster de nube privada.

```azurecli
az vmware datastore list --resource-group MyResourceGroup --cluster Cluster-1 --private-cloud MyPrivateCloud
```

# <a name="portal"></a>[Portal](#tab/azure-portal)

### <a name="preview-registration"></a>Registro para la versión preliminar

En primer lugar, registre su suscripción en Microsoft.AVS y CloudSanExperience.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Busque y seleccione **Suscripciones**.
1. Seleccione la suscripción que desea usar y seleccione **Proveedores de recursos** en **Configuración**.
1. Busque **Microsoft.AVS**, selecciónelo y haga clic en **Registrar**.
1. Seleccione **Características en vista previa** en **Configuración**.
1. Busque y registre **CloudSanExperience**.

### <a name="connect-your-disk-pool"></a>Conexión del grupo de discos

Ahora que la suscripción se ha registrado correctamente, puede conectar el grupo de discos al clúster de la nube privada de Azure VMware Solution.

> [!IMPORTANT]
> El grupo de discos se conecta a una red virtual por medio de una subred delegada, que se crea con el proveedor de recursos Microsoft.StoragePool. Si la subred no está delegada, se produce un error en la implementación. Consulte [Delegación del permiso de subred](../virtual-machines/disks-pools-deploy.md#delegate-subnet-permission) para obtener más información.

1. Vaya a su instancia de Azure VMware Solution.
1. Seleccione **Almacenamiento (versión preliminar)** en **Administrar**.
1. Seleccione **Conectar un grupo de discos**.
1. Seleccione la suscripción que quiere usar.
1. Seleccione el grupo de discos y el clúster de cliente al que le gustaría conectarlo.
1. Habilite los LUN (en caso de que los haya), proporcione un nombre de almacén de datos (de manera predeterminada, se usa el LUN) y seleccione **Conectar**.

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/connect-a-disk-pool-temp.png" alt-text="Captura de pantalla de la experiencia de conexión de un grupo de discos." lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/connect-a-disk-pool-temp.png":::

Cuando la conexión se realiza correctamente, verá los almacenes de datos agregados en vCenter.

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/vsphere-datastores.png" alt-text="Captura de pantalla de la experiencia de vSphere, los grupos de discos se han conectado como almacenes de datos." lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/vsphere-datastores.png":::

---

## <a name="disconnect-a-disk-pool-from-your-private-cloud"></a>Desconexión de un grupo de discos de la nube privada

Al desconectar un grupo de discos, sus recursos no se eliminan. No se necesita ninguna ventana de mantenimiento para esta operación. No obstante, tenga cuidado al hacerlo.

En primer lugar, apague las máquinas virtuales y quite todos los objetos asociados a los almacenes de datos del grupo de discos, lo que incluye:

   - Máquinas virtuales (quitar del inventario)

   - Plantillas

   - Instantáneas

A continuación, elimine el almacén de datos de nube privada.

1. Vaya a su instancia de Azure VMware Solution en Azure Portal.
1. Seleccione **Almacenamiento** en **Administrar**.
1. Seleccione el grupo de discos del que desea desconectarse y seleccione **Desconectar**.

:::image type="content" source="media/attach-disk-pools-to-azure-vmware-solution-hosts/disconnect-a-disk-pool.png" alt-text="Captura de pantalla de la página de almacenamiento de Azure VMware Solution, con la lista de grupos de discos conectados con la opción de desconexión resaltada." lightbox="media/attach-disk-pools-to-azure-vmware-solution-hosts/disconnect-a-disk-pool.png":::

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha conectado un grupo de discos a los hosts de Azure VMware Solution, puede que quiera obtener información sobre:

- [Administración de un grupo de discos de Azure](../virtual-machines/disks-pools-manage.md).  Una vez que haya implementado un grupo de discos, hay varias acciones de administración disponibles. Puede agregar o quitar un disco en un grupo de discos, actualizar la asignación de LUN iSCSI o agregar ACL.

- [Eliminación de un grupo de discos](../virtual-machines/disks-pools-deprovision.md#delete-a-disk-pool). Cuando se elimina un grupo de discos, también se eliminan todos los recursos del grupo de recursos administrados.

- [Desactivación de la compatibilidad con iSCSI en un disco](../virtual-machines/disks-pools-deprovision.md#disable-iscsi-support). Si deshabilita la compatibilidad con iSCSI en un grupo de discos, ya no podrá usar de forma eficaz un grupo de discos.

- [Traslado de un grupo de discos a otra suscripción](../virtual-machines/disks-pools-move-resource.md). Mueva un grupo de discos de Azure a otra suscripción, lo que implica mover el propio grupo de discos, los discos que este contiene, el grupo de recursos administrados y todos los recursos. 

- [Solución de problemas de grupos de discos](../virtual-machines/disks-pools-troubleshoot.md). Revise los códigos de error comunes relacionados con los grupos de discos de Azure (versión preliminar). También proporciona posibles resoluciones y ciertas aclaraciones sobre los estados del grupo de discos.
