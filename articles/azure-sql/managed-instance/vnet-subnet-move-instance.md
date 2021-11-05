---
title: Traslado de una instancia administrada a otra subred
titleSuffix: Azure SQL Managed Instance
description: Aprenda cómo mover una instancia de Azure SQL Managed Instance a otra subred con solo un breve tiempo de inactividad durante la conmutación por error ( normalmente de hasta 10 segundos).
services: sql-database
ms.service: sql-managed-instance
ms.subservice: deployment-configuration
ms.devlang: ''
ms.topic: how-to
author: urosmil
ms.author: urmilano
ms.reviewer: mathoma, bonova, srbozovi, wiassaf
ms.date: 09/30/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: f7adb9886bdeebc8aad7f8c200b21523e53651a0
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091392"
---
# <a name="move-azure-sql-managed-instance-across-subnets"></a>Traslado de una instancia de Azure SQL Managed Instance entre subredes
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

La instancia de Azure SQL Managed Instance debe implementarse dentro de una subred dedicada de una [red virtual](../../virtual-network/virtual-networks-overview.md) de Azure. El número de instancias administradas que se puede implementar en la subred depende del tamaño de la subred (intervalo de subred).

En este artículo se explica cómo mover la instancia administrada de una subred a otra, de forma similar al escalado de núcleos virtuales o al cambio del nivel de servicio de la instancia. La instancia de SQL Managed Instance está disponible durante el traslado, excepto durante un breve tiempo de inactividad causado por una conmutación por error al final de la actualización, que normalmente dura hasta 10 segundos, incluso si se interrumpen las transacciones de larga ejecución. 

Mover la instancia a otra subred desencadena las siguientes operaciones de clúster virtual:
- La subred de destino compila el clúster virtual o cambia su tamaño.
- El clúster virtual se quita o desfragmenta en la subred de origen. 

Antes de mover la instancia a otra subred, considere la posibilidad de familiarizarse con los siguientes conceptos: 
- [Determine el tamaño e intervalo de subred necesarios para Azure SQL Managed Instance](vnet-subnet-determine-size.md).
- Elija entre mover la instancia a una [subred nueva](virtual-network-subnet-create-arm-template.md) o [usar una subred existente](vnet-existing-add-subnet.md).
- Use las [operaciones de administración](management-operations-overview.md) para implementar automáticamente nuevas instancias administradas, actualizar las propiedades de una instancia o eliminar instancias. Es posible [supervisar](management-operations-monitor.md) estas operaciones de administración. 



## <a name="requirements-and-limitations"></a>Limitaciones y requisitos 

Para implementar una instancia administrada o moverla a otra subred, la subred de destino debe cumplir determinados [requisitos de red](connectivity-architecture-overview.md#service-aided-subnet-configuration).

### <a name="subnet-readiness"></a>Preparación de la subred 

Antes de mover la instancia administrada, confirme que la subred está marcada como **Listo para Instancia administrada**. 

En la interfaz de usuario de **red virtual** de Azure Portal, las redes virtuales que cumplen los requisitos previos para una instancia administrada se clasifican como **Listo para Instancia administrada**. Las redes virtuales que tienen subredes con instancias administradas ya implementadas en ellas muestran un icono antes del nombre de la red virtual. Las subredes vacías que están listas para una instancia administrada no tienen ningún icono. 

Las subredes marcadas como **Otros** están vacías y se pueden usar para una instancia administrada, pero primero debe cumplir los [requisitos de red](connectivity-architecture-overview.md#service-aided-subnet-configuration). Esto incluye:

- delegar al proveedor de recursos Microsoft.Sql/managedInstances
- adjuntar una tabla de rutas
- adjuntar un grupo de seguridad de red

Una vez que se cumplen todos los requisitos, la subred pasa de la categoría **Otros** a la categoría **Listo para Instancia administrada** y se puede usar para una instancia administrada. 

Las subredes marcadas como **No válidas** no se pueden usar para instancias administradas nuevas o existentes, ya sea porque ya están en uso (las instancias usadas para implementaciones de instancias no pueden contener otros recursos) o porque la subred tiene una zona DNS diferente (una limitación de movimiento de instancia entre subredes). 

> [!div class="mx-imgBorder"]
> ![Captura de pantalla de la lista desplegable de subredes de Azure SQL Managed Instance.](./media/vnet-subnet-move-instance/subnet-grouping-per-state.png)


Según el estado y la designación de la subred, se pueden realizar los siguientes ajustes en la subred de destino: 

- **Listo para Instancia administrada (contiene la instancia de SQL Managed Instance existente):** no se realiza ningún ajuste. Estas subredes ya contienen instancias administradas, y realizar cualquier cambio en la subred podría afectar a las instancias existentes. 
- **Listo para Instancia administrada (vacío):** el flujo de trabajo valida todas las reglas necesarias en el grupo de seguridad de red y la tabla de rutas, y agrega las reglas necesarias pero que faltan. <sup>1</sup>

> [!Note]
> <sup>1</sup> Las reglas personalizadas agregadas a la configuración de la subred de origen no se copian a la subred de destino. Cualquier personalización de la configuración de la subred de origen se debe replicar manualmente en la subred de destino. Una manera de lograrlo es usar la misma tabla de rutas y el mismo grupo de seguridad de red para las subredes de origen y de destino.


### <a name="destination-subnet-limitations"></a>Limitaciones de la subred de destino 

Tenga en cuenta las siguientes limitaciones al elegir una subred de destino para una instancia existente: 

- La subred de destino debe estar en la misma red virtual que la subred de origen. 
- La zona DNS de la subred de destino debe coincidir con la zona DNS de la subred de origen, ya que actualmente no se admite el cambio de la zona DNS de una instancia administrada. 
- Las instancias que se ejecutan en hardware Gen4 deben actualizarse a una generación de hardware más reciente, ya que Gen4 está en desuso. La actualización de la generación de hardware y el traslado a otra subred se pueden realizar en una operación. 


## <a name="operation-steps"></a>Pasos de la operación

En la tabla siguiente se detallan los pasos de la operación que se producen durante la operación de traslado de la instancia: 

|Nombre del paso  |Descripción del paso  |
|----|---------|
|Validación de solicitudes |Valida los parámetros enviados. Si se detecta un error de configuración, se produce un error en la operación. |
|Cambio de tamaño o creación de un clúster virtual |Según el estado de la subred de destino, el clúster virtual se crea o cambia de tamaño.  |
|Inicio de la nueva instancia |El proceso de SQL se inicia en el clúster virtual implementado en la subred de destino. |
|Inicializar o asociar archivos de base de datos |Según el nivel de servicio, se inicializa la base de datos o se adjuntan los archivos de base de datos. |
|Preparación de la conmutación por error y conmutación por error |Una vez que los datos se han inicializado o se han vuelto a adjuntar los archivos de base de datos, el sistema se prepara para la conmutación por error. Cuando todo está listo, el sistema realiza una conmutación por error **con un breve tiempo de inactividad**, normalmente de menos de 10 segundos.  |
|Limpieza de instancias de SQL antiguas |Quita el proceso de SQL anterior del clúster virtual de origen.  |
|Eliminación de un clúster virtual |Si es la última instancia dentro de la subred de origen, el paso final elimina el clúster virtual de forma sincrónica. De lo contrario, el clúster virtual se desfragmenta de forma asincrónica.  |

Puede encontrar una explicación detallada de los pasos de la operación en la [introducción a las operaciones de administración de Azure SQL Managed Instance](management-operations-overview.md#management-operations-steps).

## <a name="move-the-instance"></a>Traslado de la instancia

El traslado de la instancia entre subredes forma parte de la operación de actualización de la instancia. La API de actualización de instancias existente, Azure PowerShell y los comandos de la CLI de Azure se han mejorado con una propiedad de id. de subred. 

En Azure Portal, use el campo de subred de la hoja **Redes** para mover la instancia a la subred de destino. Si usa Azure PowerShell o la CLI de Azure, proporcione un id. de subred diferente en el comando de actualización para mover la instancia de una subred existente a la subred de destino. 

Para obtener una referencia completa de los comandos de administración de instancias, consulte [Referencia de la API de administración Para Azure SQL Managed Instance](api-references-create-manage-instance.md). 

# <a name="portal"></a>[Portal](#tab/azure-portal)

La opción para elegir la subred de la instancia se encuentra en la hoja **Redes** de Azure Portal. La operación de traslado de la instancia se inicia al seleccionar una subred y guardar los cambios. 

El primer paso de la operación de traslado es preparar la subred de destino para la implementación, lo que puede tardar varios minutos. Una vez que la subred está lista, la operación de administración de traslado de instancias se inicia y se vuelve visible en Azure Portal. 


> [!div class="mx-imgBorder"]
> ![Selección de la subred en la hoja Redes de SQL Managed Instance](./media/vnet-subnet-move-instance/how-to-select-subnet.png)


Supervise las operaciones de traslado de instancias desde la hoja **Información general** de Azure Portal. Seleccione la notificación para abrir una hoja adicional con información sobre el paso actual, el total de pasos y un botón para cancelar la operación. 

> [!div class="mx-imgBorder"]
> ![Supervisión de la operación de traslado de instancias](./media/vnet-subnet-move-instance/monitor-subnet-move-operation.png)


# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use el comando [Set-AzSqlInstance](/powershell/module/az.sql/set-azsqlinstance) de Azure PowerShell para mover una instancia después de crear la subred en la misma red virtual que la subred de destino. Si quiere usar una subred existente, proporcione ese nombre de subred en el comando de PowerShell.

Los comandos de PowerShell de ejemplo de esta sección preparan la subred de destino para la implementación de la instancia y mueven la instancia administrada. 


Use el siguiente comando de PowerShell para especificar los parámetros: 

```powershell-interactive
### PART 1 - DEFINE PARAMETERS

#Generating basic parameters
$currentSubscriptionID = 'subscription-id'
$sqlMIResourceGroupName = 'resource-group-name-of-sql-mi'
$sqlMIName = 'sql-mi-name'
$sqlMIResourceVnetName = 'vnet-name-of-sql-mi'
$destinationSubnetName = 'name-of-the-destination-subnet-for-sql-mi'
```

Omita este comando si la subred ya tiene instancias implementadas. Si usa una subred nueva, use el siguiente comando de Azure PowerShell para prepararla: 

```powershell-interactive
### PART 2 - PREPARE DESTINATION SUBNET

#Loading the url of script used for preparing the subnet for SQL MI deployment
$scriptUrlBase = 'https://raw.githubusercontent.com/Microsoft/sql-server-samples/master/samples/manage/azure-sql-db-managed-instance/delegate-subnet'

#Generating destination subnet parameters
$parameters = @{
    subscriptionId = $currentSubscriptionID
    resourceGroupName = $sqlMIResourceGroupName
    virtualNetworkName = $sqlMIResourceVnetName
    subnetName = $destinationSubnetName
}

#Initiating subnet prepartion script
Invoke-Command -ScriptBlock ([Scriptblock]::Create((iwr ($scriptUrlBase+'/delegateSubnet.ps1?t='+ [DateTime]::Now.Ticks)).Content)) -ArgumentList $parameters
```

> [!Note]
> Para obtener más información sobre el script que prepara la subred, consulte [Configuración de una red virtual existente para Azure SQL Managed Instance](vnet-existing-add-subnet.md). 

El comando de Azure PowerShell siguiente mueve la instancia a la subred de origen: 

```powershell-interactive
### PART 3 - MOVE INSTANCE TO THE NEW SUBNET

Set-AzSqlInstance -Name $sqlMIName -ResourceGroupName $sqlMIResourceGroupName `
-SubnetId "/subscriptions/$currentSubscriptionID/resourceGroups/$sqlMIResourceGroupName/providers/Microsoft.Network/virtualNetworks/$sqlMIResourceVnetName/subnets/$destinationSubnetName"
```

El siguiente comando de Azure PowerShell mueve la instancia y, además, proporciona una manera de supervisar el progreso: 

```powershell-interactive
###PART 3 EXTENDED - MOVE INSTANCE AND MONITOR PROGRESS

# Extend the Set-AzSqlInstance command with -AsJob -Force parameters to be able to monitor the progress or proceed with script execution as moving the instance to another subnet is long running operation 
Set-AzSqlInstance -Name $sqlMIName -ResourceGroupName $sqlMIResourceGroupName `
-SubnetId "/subscriptions/$currentSubscriptionID/resourceGroups/$sqlMIResourceGroupName/providers/Microsoft.Network/virtualNetworks/$sqlMIResourceVnetName/subnets/$destinationSubnetName" -AsJob -Force

$operationProgress = Get-AzSqlInstanceOperation -ManagedInstanceName $sqlMIName -ResourceGroupName $sqlMIResourceGroupName
#checking the operation step status
Write-Host "Checking the ongoing step" -ForegroundColor Yellow
$operationProgress.OperationSteps.StepsList
```


# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Use el comando [az sql mi update](/cli/azure/sql/mi#az_sql_mi_update) de la CLI de Azure para mover la instancia a otra subred. 

Proporcione el destino especificando el id. de subred como la propiedad `--subnet` o especificando el nombre de red virtual como la propiedad `--vnet-name`, y el nombre de subred como la propiedad `--subnet`. 

En el ejemplo siguiente se mueve la instancia administrada a otra subred especificando el id. de subred: 


```azurecli-interactive
az sql mi update -g myResourceGroup -n mySqlManagedInstance --subnet /subscriptions/xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup/providers/Microsoft.Network/virtualNetworks/myVirtualNetworkName/subnets/destinationSubnetName
```

En el ejemplo siguiente se mueve la instancia administrada a otra subred especificando el nombre de red virtual y el nombre de subred:

```azurecli-interactive
az sql mi update -g myResourceGroup -n mySqlManagedInstance --vnet-name myVirtualNetworkName --subnet destinationSubnetName
```

Use el siguiente comando para supervisar el progreso de la operación de administración: 

```azurecli-interactive
az sql mi op list -g myResourceGroup --mi mySqlManagedInstance
```
---

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo crear su primera instancia administrada, vea la [Guía de inicio rápido](instance-create-quickstart.md).
- Para obtener una lista de características y una comparación, consulte [Características comunes de SQL](../database/features-comparison.md).
- Para obtener más información sobre la configuración de redes virtuales, vea [Configuración de una red virtual de Instancia administrada de SQL](connectivity-architecture-overview.md).
- Para ver un inicio rápido en el que se crea una instancia administrada y se restaura una base de datos desde un archivo de copia de seguridad, consulte [Creación de una instancia administrada](instance-create-quickstart.md).
- Para consultar un tutorial sobre el uso de Azure Database Migration Service para la migración, consulte [Migración de Instancia administrada de SQL mediante Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).
