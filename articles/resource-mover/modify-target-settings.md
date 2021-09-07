---
title: Modificación de la configuración de destino al mover VM de Azure entre regiones con Azure Resource Mover
description: Obtenga información sobre la modificación de la configuración de destino al mover VM de Azure entre regiones con Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 02/08/2021
ms.author: raynew
ms.openlocfilehash: 7044414d47f685062331bc4aceb1b538d6f9a062
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112461606"
---
# <a name="modify-destination-settings"></a>Modificación de la configuración de destino

En este artículo se describe cómo modificar la configuración de destino cuando se mueven recursos entre regiones de Azure con [Azure Resource Mover](overview.md).


## <a name="modify-vm-settings"></a>Modificación de la configuración de máquina virtual

Puede modificar la configuración de destino al mover las máquinas virtuales de Azure y los recursos asociados. Es recomendable que:

- Cambie la configuración de destino únicamente después de validar la colección de transferencia de recursos. Pero:
    - Si va a mover el recurso de origen, normalmente puede modificar dicha configuración hasta que inicie el proceso de movimiento.
    - Si asigna un recurso existente en la región de origen, puede modificar la configuración de destino hasta que se completa la confirmación del movimiento.
- Modifique la configuración antes de preparar los recursos, ya que es posible que algunas propiedades de destino no estén disponibles para su edición una vez completada la preparación.

### <a name="settings-you-can-modify"></a>Opciones de configuración que se pueden modificar

Las opciones de configuración que se pueden modificar se resumen en la tabla.

**Recurso** | **Opciones** 
--- | --- 
**Nombre de la máquina virtual** | Opciones de destino:<br/><br/> - Crear una nueva VM con el mismo nombre en la región de destino.<br/><br/> - Crear una nueva VM con un nombre diferente en la región de destino.<br/><br/> - Usar una VM existente en la región de destino.<br/><br/> Si crea una nueva VM, con la excepción de los valores que modifique, a la nueva VM de destino se le asignará la misma configuración que la de la máquina virtual de origen.
**Zona de disponibilidad de máquina virtual** | Zona de disponibilidad en la que se colocará la VM de destino. Seleccione **No aplicable** si no quiere cambiar la configuración de origen o si no quiere colocar la VM en una zona de disponibilidad.
**SKU de la máquina virtual** | [Tipo de VM](https://azure.microsoft.com/pricing/details/virtual-machines/series/) (disponible en la región de destino) que se usará para la VM de destino.<br/><br/> La VM de destino seleccionada no debe ser más pequeña que la VM de origen.
**Conjunto de disponibilidad de VM** | Conjunto de disponibilidad en el que se colocará la VM de destino. Seleccione **No aplicable** si no quiere cambiar la configuración de origen o si no quiere colocar la VM en un conjunto de disponibilidad.
**Almacén de claves de VM** | Almacén de claves asociado al habilitar el cifrado de discos de Azure en una VM.
**Conjunto de cifrado de disco** | Conjunto de cifrado de disco asociado si la VM usa una clave administrada por el cliente para el cifrado del lado servidor.
**Grupos de recursos** | Grupo de recursos en el que se colocará la VM de destino.
**Recursos de redes** | Opciones para interfaces de red, redes virtuales (VNets/) y grupos de seguridad de red/interfaces de red:<br/><br/> - Crear un nuevo recurso con el mismo nombre en la región de destino.<br/><br/> - Crear un nuevo recurso con un nombre diferente en la región de destino.<br/><br/> - Usar un recurso de red existente en la región de destino.<br/><br/> Si crea un nuevo recurso de destino, con la excepción de los valores que modifique, se le asignará la misma configuración que la del recurso de origen.
**Nombre, SKU y zona de la dirección IP pública** | Especifica la [SKU](../virtual-network/public-ip-addresses.md#sku) y la [zona](../virtual-network/public-ip-addresses.md#standard) de las direcciones IP públicas estándar.<br/><br/> Si desea que tenga redundancia de zona, especifíquela como **Con redundancia de zona**.
**Nombre, SKU y zona del equilibrador de carga** | Especifica el nombre, la SKU (nivel Básico o Estándar) y la zona del equilibrador de carga.<br/><br/> Se recomienda usar la SKU Estándar.<br/><br/> Si quiere que tenga redundancia de zona, especifique **Con redundancia de zona**.
**Dependencias de recursos** | Opciones para cada dependencia:<br/><br/>- El recurso usa recursos dependientes del origen que se moverán a la región de destino.<br/><br/> - El recurso usa distintos recursos dependientes ubicados en la región de destino. En este caso, puede elegir entre cualquier recurso similar de la región de destino.

### <a name="edit-vm-destination-settings"></a>Modificación de la configuración de destino de la VM

Si no desea mover los recursos dependientes de la región de origen al destino, tiene un par de opciones más:

- Cree un recurso en la región de destino. A menos que especifique una configuración diferente, el nuevo recurso tendrá la misma configuración que el recurso de origen.
- Usar un recurso existente en la región de destino.

El comportamiento exacto depende del tipo de recurso. [Obtenga más información](modify-target-settings.md) sobre cómo modificar la configuración de destino.

Puede modificar la configuración de destino de un recurso mediante la entrada **Configuración de destino** en la colección de transferencia de recursos. 

Para modificar un valor de configuración: 

1. En la página **Entre regiones** > columna **Destination configuration** (Configuración de destino), haga clic en el vínculo de la entrada del recurso.
2. En **Opciones de configuración**, puede crear una nueva VM en la región de destino.
3. Asigne una nueva zona de disponibilidad, un conjunto de disponibilidad o una SKU a la VM de destino. **Zona de disponibilidad** y **SKU**.

Solo se realizan cambios para el recurso que se edita. Los recursos dependientes deben actualizarse por separado.


## <a name="modify-sql-settings"></a>Modificación de la configuración de SQL

Al mover recursos de Azure SQL Database, puede modificar la configuración de destino para el traslado. 

- Para una base de datos SQL:
    - Se recomienda modificar la configuración de destino antes de prepararla para el traslado.
    - Puede modificar la configuración de la base de datos de destino y la redundancia de zona para la base de datos.
- Para grupos elásticos:
    -  Puede modificar la configuración de destino en cualquier momento antes de iniciar el traslado.
    - Puede modificar el grupo elástico de destino y la redundancia de zona para el grupo. 

### <a name="sql-settings-you-can-modify"></a>Opciones de SQL que puede modificar

**Configuración** | **SQL database** | **Grupo elástico**
--- | --- | ---
**Nombre** | Cree una nueva base de datos con el mismo nombre en la región de destino.<br/><br/> Cree una nueva base de datos con un nombre diferente en la región de destino.<br/><br/> Use una base de datos existente en la región de destino. | Cree un nuevo grupo elástico con el mismo nombre en la región de destino.<br/><br/> Cree un nuevo grupo elástico con un nombre diferente en la región de destino.<br/><br/> Use un grupo elástico existente en la región de destino.
**Redundancia de zona** | Para mover de una región que admite la redundancia de zona a una región que no la admite, escriba **Deshabilitar** en la configuración de zona.<br/><br/> Para mover de una región que no admite la redundancia de zona a una región que sí la admite, escriba **Habilitar** en la configuración de zona. | Para mover de una región que admite la redundancia de zona a una región que no la admite, escriba **Deshabilitar** en la configuración de zona.<br/><br/> Para mover de una región que no admite la redundancia de zona a una región que sí la admite, escriba **Habilitar** en la configuración de zona.

### <a name="edit-sql-destination-settings"></a>Modificación de la configuración de destino de SQL

Modifique la configuración de destino de un recurso de Azure SQL Database como se indica a continuación: 

1. En **Across regions** (Entre regiones), para el recurso que desea modificar, haga clic en la entrada **Destination configuration** (Configuración de destino).
2. En **Opciones de configuración**, especifique la configuración de destino resumida en la tabla anterior.


## <a name="modify-settings-in-powershell"></a>Modificación de la configuración en PowerShell

Puede modificar la configuración en PowerShell.

1)  Recupere el recurso de movimiento para el que desea editar las propiedades. Por ejemplo, para recuperar una ejecución de máquina virtual:

    ```azurepowershell
    $moveResourceObj = Get-AzResourceMoverMoveResource -MoveCollectionName "PS-centralus-westcentralus-demoRMS1" -ResourceGroupName "RG-MoveCollection-demoRMS" -Name "PSDemoVM"
    ```
2)  Copie la configuración del recurso en un objeto de configuración de recurso de destino.

    ```azurepowershell
    $TargetResourceSettingObj = $moveResourceObj.ResourceSetting
    ```

3)  Establezca el parámetro en el objeto de configuración del recurso de destino. Por ejemplo, para cambiar el nombre de la máquina virtual de destino:

    ```azurepowershell
    $TargetResourceSettingObj.TargetResourceName="PSDemoVM-target"
    ```

4)  Actualice la configuración de destino del recurso de movimiento. En este ejemplo, se cambia el nombre de la máquina virtual de *PSDemoVM* a *PSDemoVMTarget*.

    ```azurepowershell
    Update-AzResourceMoverMoveResource -ResourceGroupName "RG-MoveCollection-demoRMS" -MoveCollectionName "PS-centralus-westcentralus-demoRMS" -SourceId "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/PSDemoRM/providers/Microsoft.Compute/virtualMachines/PSDemoVM" -Name "PSDemoVM" -ResourceSetting $TargetResourceSettingObj
    ```
    **Salida**
    ![Texto de salida después de modificar la configuración de destino](./media/modify-target-settings/update-settings.png)


## <a name="next-steps"></a>Pasos siguientes

[Traslade una máquina virtual de Azure](tutorial-move-region-virtual-machines.md) a otra región.
