---
title: Traslado de recursos a otra región con Azure Resource Mover
description: Aprenda a trasladar recursos de un grupo de recursos a otra región mediante Azure Resource Mover.
manager: evansma
author: rayne-wiselman
ms.service: resource-move
ms.topic: how-to
ms.date: 09/08/2020
ms.author: raynew
ms.openlocfilehash: 8eb4dc8ee58d9ced141c7c4e4404596ace727f0c
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112459173"
---
# <a name="move-resources-across-regions-from-resource-group"></a>Traslado de recursos entre regiones (desde un grupo de recursos)

En este artículo, aprenderá a trasladar recursos de un grupo de recursos específico a otra región de Azure. En el grupo de recursos, seleccione los recursos que desea trasladar. Después, trasládelos con [Azure Resource Mover](overview.md).

## <a name="prerequisites"></a>Requisitos previos

- Necesita el acceso de *propietario* en la suscripción en la que se encuentran los recursos que se van a trasladar.
    - La primera vez que agregue un recurso de una asignación de origen y destino específicas a una suscripción de Azure, Azure Resource Mover creará una [identidad administrada asignada por el sistema](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types), anteriormente llamada identidad de servicio administrado (MSI), en la que confiará la suscripción.
    - Para crear la identidad y asignarle el rol requerido (Colaborador o Administrador de acceso de usuario en la suscripción de origen), la cuenta que utilice para agregar recursos necesita permisos de *propietario* para la suscripción. [Obtenga más información](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles) sobre los roles de Azure.
- La suscripción necesita tener cuota suficiente para crear los recursos de origen de la región de destino. De lo contrario, hay que solicitar límites adicionales. [Más información](../azure-resource-manager/management/azure-subscription-service-limits.md).
- Compruebe los precios y los cargos asociados con la región de destino a la que va a trasladar las máquinas virtuales. El uso de la [calculadora de precios](https://azure.microsoft.com/pricing/calculator/) le resultará útil.
- Compruebe que los recursos que desea trasladar son compatibles con Resource Mover:
    - Máquinas virtuales de Azure y discos asociados
    - NIC
    - Conjuntos de disponibilidad
    - Redes virtuales de Azure
    - Direcciones IP públicas
    - Grupos de seguridad de red (NSG)
    - Equilibradores de carga internos y públicos
    - Grupos elásticos y bases de datos de Azure SQL


## <a name="check-vm-requirements"></a>Comprobación de los requisitos de máquina virtual

1. Asegúrese de que las máquinas virtuales que desea trasladar están admitidas.

    - [Compruebe](support-matrix-move-region-azure-vm.md#windows-vm-support) las máquinas virtuales Windows admitidas.
    - [Compruebe](support-matrix-move-region-azure-vm.md#linux-vm-support) las máquinas virtuales Linux y las versiones de kernel admitidas.
    - Compruebe la configuración admitida de [proceso](support-matrix-move-region-azure-vm.md#supported-vm-compute-settings), [almacenamiento](support-matrix-move-region-azure-vm.md#supported-vm-storage-settings) y [redes](support-matrix-move-region-azure-vm.md#supported-vm-networking-settings).
2. Asegúrese de que las máquinas virtuales dispongan de los certificados raíz de confianza más recientes, así como de una lista actualizada de revocación de certificados (CRL). 
    - En las máquinas virtuales de Azure que ejecuten Windows, instale las actualizaciones de Windows más recientes.
    - En las máquinas virtuales que ejecuten Linux, siga las instrucciones del distribuidor de Linux para asegurarse de que la máquina tenga los certificados y la CRL más recientes. 
3. Permita la conectividad saliente desde las máquinas virtuales:
    - Si utiliza un servidor proxy de firewall basado en direcciones URL para controlar la conectividad de salida, debe facilitar el acceso a estas [URL](support-matrix-move-region-azure-vm.md#url-access).
    - Si utiliza reglas de grupo de seguridad de red para determinar la conectividad saliente, cree estas [reglas de etiquetas de servicio](support-matrix-move-region-azure-vm.md#nsg-rules).

## <a name="select-resources-to-move"></a>Selección de los recursos que se van a trasladar

Seleccione los recursos que desea trasladar. Los recursos se trasladarán a una región de destino de la suscripción de la región de origen. Si desea cambiar la suscripción, puede hacerlo después de trasladar los recursos.

> [!NOTE]
>  No seleccione discos asociados o se producirá un error en la operación. Los discos asociados se incluyen automáticamente en la transferencia de una máquina virtual.

1. En Azure Portal, abra el grupo de recursos correspondiente.
2. En la página del grupo de recursos, seleccione los recursos que desea trasladar.
3. Seleccione **Mover** > **Mover a otra región**.

    ![Selección de recursos para moverlos a otra región](./media/move-region-within-resource-group/select-move-region.png)
    
4. En **Origen + Destino**, seleccione la región de destino a la que desea trasladar los recursos. Luego, seleccione **Siguiente**.


    ![Página Origen + Destino para seleccionar la región de destino](./media/move-region-within-resource-group/source-target.png)


7. En **Recursos que hay que mover**, seleccione **Siguiente**.  
8. En **Seleccionar recursos**, seleccione el recurso que desea trasladar. Solo es posible agregar recursos que se puedan mover. A continuación, seleccione **Done** (Listo).
9. En **Mover recursos**, seleccione **Siguiente**. 
10. En **Revisar + agregar**, compruebe los detalles del origen y el destino.
11. Confirme que entiende que los metadatos sobre los recursos que se van a mover se almacenarán en un grupo de recursos creado para este propósito y que permite a Resource Mover crear una identidad administrada por el sistema para acceder a los recursos de la suscripción.
1. Seleccione **Continuar** para empezar a agregar recursos.

    ![Página Resumen para comprobar los detalles y continuar con el traslado](./media/move-region-within-resource-group/summary.png)    

11. Se inicia la operación para agregar recursos. Una vez completada la operación, las notificaciones muestran que se agregaron los recursos y que la implementación se realizó correctamente.
13. En las notificaciones, seleccione **Adding resources for move** (Agregar recursos para mover).

    ![Mensaje mostrado en las notificaciones](./media/move-region-within-resource-group/notification.png)    


14. Después de seleccionar la notificación, los recursos seleccionados se agregan a una colección de movimiento en el centro de Azure Resource Mover.  Resource Mover le ayuda a comprobar las dependencias y, a continuación, comienza a mover los recursos a la región de destino.

## <a name="resolve-dependencies"></a>Resolución de dependencias

Los recursos que va a mover aparecen en la página **Entre regiones**, con el estado *Prepare pending* (Preparación pendiente). Inicie la validación de la siguiente manera:

1. Si los recursos muestran el mensaje *Validar dependencias* en la columna **Problemas**, seleccione el botón **Validar dependencias**. Se iniciará el proceso de validación.

    ![Botón para validar dependencias](./media/move-region-within-resource-group/validate-dependencies.png)

2. Si se encuentran dependencias, seleccione **Agregar dependencias**. 
3. En **Agregar dependencias**, seleccione los recursos dependientes > **Agregar dependencias**. Compruebe el progreso en el área de notificaciones.

    ![Botón para agregar dependencias](./media/move-region-within-resource-group/add-dependencies.png)

3. Agregue más dependencias si es necesario y valídelas según proceda. Seleccione **Actualizar** para asegurarse de que los recursos muestran un estado actualizado.

4. En la página **Entre regiones**, compruebe que los recursos tienen ahora el estado *Prepare pending* (Preparación pendiente) y que no presentan problemas.

    ![Página para mostrar el estado de preparación pendiente de todos los recursos](./media/move-region-within-resource-group/prepare-pending.png)

## <a name="move-the-source-resource-group"></a>Traslado del grupo de recursos de origen 

Antes de poder preparar y trasladar los recursos, el grupo de recursos de origen debe existir en la región de destino. 

### <a name="prepare-to-move-the-source-resource-group"></a>Preparación del grupo de recursos de origen para el traslado

Lleve a cabo los siguientes preparativos:

1. En **Entre regiones**, seleccione el grupo de recursos de origen > **Preparar**.
2. En **Preparar recursos**, seleccione **Preparar**.

    ![Botón para preparar el grupo de recursos de origen](./media/move-region-within-resource-group/prepare-source-resource-group.png)

    Durante el proceso de preparación, Resource Mover genera plantillas de Azure Resource Manager mediante la configuración del grupo de recursos. Los recursos que pertenecen al grupo no se ven afectados.
    
> [!NOTE]
>  Una vez preparado el grupo de recursos, tendrá el estado *Initiate move pending* (Iniciar movimiento pendiente). Actualice para mostrar el estado más reciente.

![Estado que muestra el estado de inicio pendiente](./media/move-region-within-resource-group/initiate-resource-group-pending.png)

### <a name="move-the-source-resource-group"></a>Traslado del grupo de recursos de origen

Empiece a trasladar el grupo de recursos como se indica a continuación:

1. En **Entre regiones**, seleccione el grupo de recursos > **Iniciar movimiento**.
2. En **Mover recursos**, seleccione **Iniciar movimiento**. El grupo de recursos pasará al estado *Initiate move in progress* (Iniciar movimiento en curso).
3. Una vez iniciado el traslado, se creará el grupo de recursos de destino, basado en la plantilla de Resource Manager generada. El grupo de recursos de origen pasa al estado *Commit move Pending* (Confirmar movimiento pendiente).

    ![Estado que muestra la confirmación de movimiento](./media/move-region-availability-zone/commit-move-pending.png)

Para confirmar y finalizar el proceso de traslado:

1. En **Entre regiones**, seleccione el grupo de recursos > **Commit move** (Confirmar movimiento).
2. En **Mover recursos**, seleccione **Confirmar**.

> [!NOTE]
> Una vez confirmado el traslado, el grupo de recursos de origen tendrá el estado *Delete source pending* (Eliminar origen pendiente).

## <a name="modify-target-settings"></a>Modificación de la configuración de destino

Si no desea trasladar un recurso de origen, puede realizar una de las siguientes acciones:

- Cree un recurso en la región de destino con el mismo nombre y la misma configuración que el recurso de la región de origen.
- Cree un recurso equivalente en la región de destino. A excepción de la configuración que especifique, el recurso de destino se crea con la misma configuración que el origen.
- Use un recurso existente en la región de destino.

Modifique la configuración de la siguiente manera:

1. Para modificar un valor de configuración, seleccione la entrada en la columna **Destination configuration** (Configuración de destino) del recurso.
2. En la página **Destination configuration** (Configuración de destino), especifique la configuración de destino que desea usar.
    Solo se realizan cambios para el recurso que se edita. Los recursos dependientes deben actualizarse por separado.   
    
La configuración exacta que modifique dependerá del tipo de recurso. [Obtenga más información](modify-target-settings.md) sobre cómo modificar la configuración de destino.

## <a name="prepare-resources-to-move"></a>Preparación de los recursos que se van a trasladar

Ahora que el grupo de recursos de origen se ha trasladado, puede preparar el traslado de los demás recursos.

1. En **Entre regiones**, seleccione los recursos que desea preparar. 

    ![Página para seleccionar la preparación de otros recursos](./media/move-region-availability-zone/prepare-other.png)

2. Seleccione **Preparar**.

> [!NOTE]
> - Durante el proceso de preparación, el agente de Azure Site Recovery Mobility se instala en las máquinas virtuales para la replicación.
> - Los datos de las máquinas virtuales se replican periódicamente en la región de destino. Esto no afecta a la máquina virtual de origen.
> - Resource Mover genera plantillas de Resource Manager para los demás recursos de origen.
> - Una vez preparados los recursos, pasarán al estado *Initiate move pending* (Iniciar movimiento pendiente).

![Página que muestra los recursos que tienen el estado de iniciar movimiento pendiente](./media/move-region-availability-zone/initiate-move-pending.png)

## <a name="initiate-the-move"></a>Inicio de la migración

Con los recursos ya preparados, puede iniciar el traslado.

1. En **Entre regiones**, seleccione los recursos que tengan el estado *Initiate move pending* (Iniciar movimiento pendiente). Después, seleccione **Iniciar movimiento**.
2. En **Mover recursos**, seleccione **Iniciar movimiento**.

    ![Seleccionar el botón Iniciar movimiento](./media/move-region-within-resource-group/initiate-move.png)

3. Haga un seguimiento del progreso del traslado en la barra de notificaciones.


> [!NOTE]
> - En el caso de las máquinas virtuales, se crearán réplicas en la región de destino. La máquina virtual de origen se apagará y se producirá cierto tiempo de inactividad (normalmente es cuestión de minutos).<br/>
> - Resource Mover recreará otros recursos mediante las plantillas de Resource Manager preparadas. Normalmente, no se produce tiempo de inactividad.<br/> 
> - En el caso de los equilibradores de carga, las reglas NAT no se copian. Créelas en la región de destino después de confirmar el movimiento.
> - En el caso de las direcciones IP públicas, la etiqueta de nombre DNS no se copia. Vuelva a crear la etiqueta después de confirmar el movimiento.
> - Después de preparar los recursos, tendrán el estado *Commit move pending* (Confirmar movimiento pendiente).


## <a name="discard-or-commit"></a>¿Descartar o confirmar?

Tras la operación inicial de traslado, puede decidir si desea confirmarla o descartarla. 

- **Descartar**: puede descartar un traslado cuando esté haciendo pruebas y no quiera trasladar verdaderamente el recurso de origen. Si el traslado se descarta, el recurso volverá a tener el estado *Initiate move pending* (Iniciar movimiento pendiente).
- **Confirmación**: la confirmación completa la operación de traslado a la región de destino. Tras la confirmación, un recurso de origen tendrá el estado *Delete source pending* (Eliminar origen pendiente) y el usuario puede decidir si desea eliminarlo.


## <a name="discard-the-move"></a>Descartar la operación de traslado 

Para descartar la operación de traslado, siga estos pasos:

1. En **Entre regiones**, seleccione los recursos que tengan el estado *Commit move pending* (Confirmar movimiento pendiente) y seleccione **Discard move** (Descartar movimiento).
2. En **Discard move** (Descartar movimiento), seleccione **Descartar**.
3. Haga un seguimiento del progreso del traslado en la barra de notificaciones.
4. Cuando las notificaciones indiquen que el traslado se ha realizado correctamente, seleccione **Actualizar**. 

> [!NOTE]
> Después de haber descartado recursos, las máquinas virtuales tendrán el estado *Initiate move pending* (Iniciar movimiento pendiente).

## <a name="commit-the-move"></a>Confirmación de la operación de traslado

Si desea completar la operación de traslado, confírmela. 


1. En **Entre regiones**, seleccione los recursos que tengan el estado *Commit move pending* (Confirmar movimiento pendiente) y seleccione **Confirmar movimiento**.
2. En **Commit resources** (Confirmar recursos), seleccione **Confirmar**.

    ![Página para confirmar los recursos y completar la operación de traslado](./media/move-region-within-resource-group/commit-resources.png)

3. Haga un seguimiento del progreso de la operación de confirmación en la barra de notificaciones.

> [!NOTE]
> - Tras confirmar el traslado, las máquinas virtuales dejarán de replicarse. La confirmación no afecta a la máquina virtual de origen.
> - La confirmación tampoco afecta a los recursos de redes de origen.
> - Una vez confirmado el traslado, los recursos tendrán el estado *Delete source pending* (Eliminar origen pendiente).

## <a name="configure-settings-after-the-move"></a>Configuración de parámetros tras el traslado

1. Puesto que las etiquetas de nombre DNS no se copian para las direcciones IP públicas, una vez finalizado el movimiento, vaya a los recursos de destino y actualice la etiqueta. 
2. En el caso de los equilibradores de carga internos, dado que las reglas NAT no se copian, vaya a los recursos creados en la región de destino y actualice las reglas NAT.
3. El servicio Mobility no se desinstala automáticamente de las máquinas virtuales. Puede desinstalarlo manualmente, o bien mantenerlo si tiene previsto trasladar el servidor de nuevo.
## <a name="delete-source-resources-after-commit"></a>Eliminación de los recursos de origen después de la confirmación

Después del traslado, puede eliminar los recursos de la región de origen si lo desea. 

1. En **Entre regiones**, seleccione el nombre de los recursos de origen que desee eliminar.
2. En la página Propiedades de cada recurso, seleccione **Eliminar**.

## <a name="delete-additional-resources-created-for-move"></a>Eliminación de recursos adicionales creados para el traslado

Después del traslado, puede eliminar manualmente la colección de traslado, junto con los recursos de Site Recovery que se crearon.

- La colección de traslado está oculta de forma predeterminada. Para verla, debe activar los recursos ocultos.
- El almacenamiento en caché tiene un bloqueo que debe eliminarse antes de que se pueda eliminar la colección.

Realice la eliminación de la siguiente manera: 

1. Identifique los recursos pertenecientes al grupo ```RegionMoveRG-<sourceregion>-<target-region>```, en la región de origen.
2. Compruebe que la máquina virtual y todos los demás recursos de origen de la colección de movimientos se han movido o eliminado. De este modo, se garantiza que no haya recursos pendientes que los utilicen.
2. Eliminación de los recursos:

    - El nombre de la colección de traslado es ```movecollection-<sourceregion>-<target-region>```.
    - El nombre de la cuenta de almacenamiento en caché es ```resmovecache<guid>```.
    - El nombre del almacén es ```ResourceMove-<sourceregion>-<target-region>-GUID```.

## <a name="next-steps"></a>Pasos siguientes


[Más información](about-move-process.md) acerca del proceso de traslado.