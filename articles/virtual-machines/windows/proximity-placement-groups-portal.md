---
title: Cree un grupo de selección de ubicación de proximidad con el portal
description: Aprenda cómo crear un grupo de selección de ubicación de proximidad con el Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 3/8/2021
ms.author: cynthn
ms.openlocfilehash: 37e959615ea9e5d483a081c7059556d36b5fc8ff
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122694871"
---
# <a name="create-a-proximity-placement-group-using-the-azure-portal"></a>Creación de un grupo de selección de ubicación de proximidad mediante Azure Portal

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows 

Para acercar las máquinas virtuales lo máximo posible con la menor latencia, debe implementarlas dentro de un [grupo de selección de ubicación de proximidad](../co-location.md#proximity-placement-groups).

Un grupo de selección de ubicación de proximidad es una agrupación lógica que se usa para asegurarse de que los recursos de proceso de Azure se encuentran físicamente cercanos entre sí. Los grupos de selección de ubicación de proximidad son útiles para las cargas de trabajo en las que la latencia baja es un requisito.

> [!NOTE]
> No se pueden usar grupos de selección de ubicación de proximidad con hosts dedicados.
>
> Si desea usar zonas de disponibilidad junto con grupos de selección de ubicación, debe asegurarse de que las máquinas virtuales del grupo de selección de ubicación también están en la misma zona de disponibilidad.
>

## <a name="create-the-proximity-placement-group"></a>Creación de un grupo de selección de ubicación de proximidad

1. Escriba **proximity placement group** 'grupo de selección de ubicación de proximidad' en el cuadro de búsqueda.
1. En **Servicios** en los resultados de la búsqueda, seleccione **Proximity placement groups (Grupos de selección de ubicación de proximidad)**.
1. En la página **Grupos de selección de ubicación de proximidad**, seleccione **Agregar**.
1. En la pestaña **Datos básicos**, en **Detalles del proyecto**, asegúrese de que está seleccionada la suscripción correcta.
1. En **Grupo de recursos** seleccione **Crear nuevo** para crear un nuevo grupo o seleccione un grupo de recursos vacío existente de la lista desplegable. 
1. En **Región** seleccione la ubicación en la que desea crear el grupo de selección de ubicación de proximidad.
1. En **Nombre de grupo de ubicación de proximidad** escriba un nombre y luego seleccione **Revisar + crear**.
1. Una vez que se haya superado la validación, seleccione **Crear** para crear el grupo de selección de ubicación de proximidad.

    ![Captura de pantalla de creación de un grupo de selección de ubicación de proximidad](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Crear una VM

1. Al crear una máquina virtual en el portal, vaya a la pestaña **Advanced**. 
1. En la selección **Grupo de selección de ubicación de proximidad**, elija el grupo de ubicación correcto. 

    ![Captura de pantalla de la sección del grupo de selección de ubicación de proximidad al crear una nueva máquina virtual en el portal](./media/ppg/vm-ppg.png)

1. Cuando haya terminado de realizar todas las demás selecciones necesarias, seleccione **Revisar + crear**.
1. Después de pasar la validación, seleccione **Crear** para implementar la máquina virtual en el grupo de ubicación.


## <a name="add-vms-in-an-availability-set-to-a-proximity-placement-group"></a>Adición de máquinas virtuales en un conjunto de disponibilidad a un grupo con ubicación por proximidad

Si la máquina virtual forma parte del conjunto de disponibilidad, debe agregar el conjunto de disponibilidad en el grupo de selección de ubicación antes de agregar las máquinas virtuales.

1. En el [portal](https://portal.azure.com), busque *Conjuntos de disponibilidad* y seleccione el conjunto de disponibilidad en los resultados.
1. Detenga/desasigne cada máquina virtual del conjunto de disponibilidad. Para ello, seleccione la máquina virtual, seleccione **Detener** en la página de la máquina virtual y, a continuación, seleccione **Aceptar** para detener la máquina virtual.
1. En la página del conjunto de disponibilidad, asegúrese de que el **Estado** de todas las máquinas virtuales sea **Detenido (desasignado)** .
1. En el menú de la izquierda, seleccione **Configuración**.
1. En **Grupo con ubicación por proximidad**, seleccione un grupo de selección de ubicación en la lista desplegable y, a continuación, seleccione **Guardar**.
1. Seleccione **Información general** en el menú de la izquierda para volver a ver la lista de máquinas virtuales. 
1. Seleccione cada una de las máquinas virtuales del conjunto de disponibilidad y, a continuación, seleccione **Iniciar** en la página de cada máquina virtual. 


## <a name="add-existing-vm-to-placement-group"></a>Adición de una máquina virtual existente a un grupo de selección de ubicación 


1. En la página de la máquina virtual, seleccione **Detener**.
1. Una vez que el estado de la máquina virtual se muestra como **Detenido (desasignado)** , seleccione **Configuración** en el menú de la izquierda.
1. En **Grupo con ubicación por proximidad**, seleccione un grupo de selección de ubicación en la lista desplegable y, a continuación, seleccione **Guardar**.
1. Seleccione **Información general** en el menú de la izquierda y, a continuación, seleccione **Iniciar** para reiniciar la máquina virtual.

 

## <a name="next-steps"></a>Pasos siguientes

También puede usar [Azure PowerShell](proximity-placement-groups.md) para crear grupos de selección de ubicación de proximidad.