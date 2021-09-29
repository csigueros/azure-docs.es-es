---
title: Control del mantenimiento de máquinas virtuales de Azure mediante Azure Portal
description: Aprenda a controlar cuándo se aplica el mantenimiento a las máquinas virtuales de Azure mediante el control de mantenimiento y Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: maintenance
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/22/2020
ms.author: cynthn
ms.openlocfilehash: 6ac4be6b1c0654ea46bbbfe587a72839c3f2896b
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129216638"
---
# <a name="control-updates-with-maintenance-control-and-the-azure-portal"></a>Control de las actualizaciones con el control de mantenimiento y Azure Portal

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

El control de mantenimiento permite decidir cuándo se aplican las actualizaciones tanto a las máquinas virtuales aisladas como a los hosts dedicados de Azure. En este tema se tratan las opciones de Azure Portal para el control de mantenimiento. Para más información sobre las ventajas de usar el control de mantenimiento, sus limitaciones y otras opciones de administración, consulte el artículo acerca de la [administración de las actualizaciones de las distintas plataformas con control de mantenimiento](maintenance-control.md).

## <a name="create-a-maintenance-configuration"></a>Creación de una configuración de mantenimiento

1. Inicie sesión en Azure Portal.

1. Busque **Configuraciones de mantenimiento**.
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-search-bar.png" alt-text="Captura de pantalla en la que se muestra cómo abrir Configuraciones de mantenimiento":::

1. Haga clic en **Agregar**.

    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-2.png" alt-text="Captura de pantalla en la que se muestra cómo agregar una configuración de mantenimiento":::

1. En la pestaña Aspectos básicos, elija una suscripción y un grupo de recursos, escriba un nombre para la configuración, elija una región y seleccione *Host* para el ámbito. Haga clic en **Next**.
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-basics-tab.png" alt-text="Captura de pantalla en la que se muestran los elementos básicos de las configuraciones de mantenimiento":::

1. En la pestaña Programar, declare una ventana programada cuando Azure vaya a aplicar las actualizaciones en los recursos. Establezca una fecha de inicio, una ventana de mantenimiento y una periodicidad. Una vez que cree una ventana programada, ya no tendrá que aplicar las actualizaciones manualmente. Haga clic en **Next**. 

    > [!IMPORTANT]
    > La **duración** de la ventana de mantenimiento debe ser de *2 horas* o más. La **periodicidad** de mantenimiento debe establecerse para repetirse al menos una vez cada 35 días. 

    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-schedule-tab.png" alt-text="Captura de pantalla en la que se muestra la programación de la configuración de mantenimiento":::

1. En la pestaña Asignación, asigne recursos ahora u omita este paso y asigne recursos más tarde, después de la implementación de la configuración de mantenimiento. Haga clic en **Next**.

1. Agregue las etiquetas y los valores. Haga clic en **Next**.
    
    :::image type="content" source="media/virtual-machines-maintenance-control-portal/maintenance-configurations-tags-tab.png" alt-text="Captura de pantalla en la que se muestra cómo agregar etiquetas a una configuración de mantenimiento":::

1. Revise el resumen. Haga clic en **Crear**.

1. Cuando termine la implementación, haga clic en **Ir al recurso**.


## <a name="assign-the-configuration"></a>Asignación de la configuración

En la página de detalles de la configuración de mantenimiento, haga clic en Asignaciones y, después, en **Asignar recurso**. 

![Captura de pantalla en la que muestra cómo asignar un recurso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-add-assignment.png)

Seleccione los recursos a los que desea asignar la configuración de mantenimiento y haga clic en **Aceptar**. En la columna **Tipo** se muestra si el recurso es una máquina virtual aislada o un host dedicado de Azure. Para asignar la configuración, es preciso que la máquina virtual esté en ejecución. Si se intenta asignar una configuración a una máquina virtual que está detenida, se produce un error. 

<!---Shantanu to add details about the error case--->

![Captura de pantalla en la que muestra cómo seleccionar un recurso](media/virtual-machines-maintenance-control-portal/maintenance-configurations-select-resource.png)

## <a name="check-configuration"></a>Comprobación de configuración

Desde **Configuraciones de mantenimiento** no solo se puede comprobar si la configuración se ha aplicado correctamente, sino que también se pueden ver todas las configuraciones de mantenimiento que está asignadas actualmente. En la columna **Tipo** se muestra si la configuración está asignada a una máquina virtual aislada o a un host dedicado de Azure. 

![Captura de pantalla en la que se muestra cómo comprobar una configuración de mantenimiento](media/virtual-machines-maintenance-control-portal/maintenance-configurations-host-type.png)

También puede comprobar la configuración de una máquina virtual específica en su página de propiedades. Haga clic en **Mantenimiento** para ver la configuración asignada a esa máquina virtual.

![Captura de pantalla en la que se muestra cómo comprobar el mantenimiento de un host](media/virtual-machines-maintenance-control-portal/maintenance-configurations-check-config.png)

## <a name="check-for-pending-updates"></a>Búsqueda de actualizaciones pendientes

También hay dos maneras de comprobar si las actualizaciones tienen una configuración de mantenimiento pendiente. En **Configuraciones de mantenimiento**, en los detalles de la configuración, haga clic en **Asignaciones** y vea el valor de **Estado de mantenimiento**.

![Captura de pantalla en la que se muestra cómo comprobar las actualizaciones pendientes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending.png)

También puede comprobar un host específico mediante **Virtual Machines** o las propiedades del host dedicado. 

![Captura de pantalla en la que aparece el estado de mantenimiento resaltado.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-pending-vm.png)

## <a name="apply-updates"></a>Aplicación de actualizaciones

Las actualizaciones pendientes se pueden aplicar a petición. En los detalles de la máquina virtual o del host dedicado de Azure, haga clic en **Mantenimiento** y, después, en **Aplicar mantenimiento ahora**. La aplicación de llamadas de actualización puede tardar hasta 2 horas en completarse.

![Captura de pantalla en la que se muestra cómo aplicar las actualizaciones pendientes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-apply-updates-now.png)

## <a name="check-the-status-of-applying-updates"></a>Comprobación del estado de aplicación de las actualizaciones 

El progreso de las actualizaciones de una configuración se puede consultar en **Configuraciones de mantenimiento** o en **Virtual Machines**. En los detalles de la máquina virtual, haga clic en **Mantenimiento**. En el ejemplo siguiente, **Estado de mantenimiento**  muestra que una actualización está en estado **Pendiente**.

![Captura de pantalla en la que muestra cómo comprobar el estado de las actualizaciones pendientes](media/virtual-machines-maintenance-control-portal/maintenance-configurations-status.png)

## <a name="delete-a-maintenance-configuration"></a>Eliminación de una configuración de mantenimiento

Para eliminar una configuración, abra los detalles de la misma y haga clic en **Eliminar**.

![Captura de pantalla que muestra cómo eliminar una configuración.](media/virtual-machines-maintenance-control-portal/maintenance-configurations-delete.png)


## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte [Mantenimiento y actualizaciones](maintenance-and-updates.md).
