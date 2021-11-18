---
title: Creación de una FCI con discos compartidos de Azure
description: Use discos compartidos de Azure para crear una instancia de clúster de conmutación por error (FCI) con SQL Server en Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na, devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: 142c84fa2006e6a5a99fc2997d62f79376758339
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132158604"
---
# <a name="create-an-fci-with-azure-shared-disks-sql-server-on-azure-vms"></a>Creación de una FCI con discos compartidos de Azure (SQL Server en VM de Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Elimine la necesidad de una instancia de Azure Load Balancer o un nombre de red distribuida (DNN) para la instancia de clúster de conmutación por error mediante la creación de las máquinas virtuales de SQL Server en [varias subredes](failover-cluster-instance-prepare-vm.md#subnets) dentro de la misma red virtual de Azure.

En este artículo se explica cómo crear una instancia de clúster de conmutación por error (FCI) mediante discos compartidos de Azure con SQL Server en Azure Virtual Machines (VM). 

Para más información, consulte la información general de [FCI con SQL Server en VM de Azure](failover-cluster-instance-overview.md) y [Procedimientos recomendados de clúster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Ahora es posible migrar mediante lift and shift la solución de instancia de clúster de conmutación por error a SQL Server en máquinas virtuales de Azure mediante Azure Migrate. Consulte [Migración de una instancia de clúster de conmutación por error](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) para más información. 

## <a name="prerequisites"></a>Requisitos previos 

Antes de completar las instrucciones de este artículo, ya debe tener:

- Suscripción a Azure. Comience a usarla [gratis](https://azure.microsoft.com/free/). 
- [Dos o más máquinas virtuales de Microsoft Azure](failover-cluster-instance-prepare-vm.md) en un conjunto de disponibilidad o en zonas de disponibilidad. 
- Una cuenta que tenga permisos para crear objetos en máquinas virtuales de Azure y en Active Directory.
- La versión más reciente de [Azure PowerShell](/powershell/azure/install-az-ps). 

## <a name="add-azure-shared-disk"></a>Adición de un disco compartido de Azure

[Implemente un disco SSD prémium administrado con la característica de disco compartido habilitada](../../../virtual-machines/disks-shared-enable.md#deploy-a-premium-ssd-as-a-shared-disk). Establezca `maxShares` para que se **alinee con el número de nodos de clúster** a fin de que el disco pueda compartirse en todos los nodos de FCI. 

## <a name="attach-shared-disk-to-vms"></a>Conexión de un disco compartido a máquinas virtuales

Una vez que haya implementado un disco compartido con maxShares > 1, puede montar el disco en las máquinas virtuales que van a participar como nodos en el clúster. 

Para conectar el disco compartido a las máquinas virtuales con SQL Server, siga estos pasos: 

1. Seleccione en Azure Portal la máquina virtual a la que conectará el disco compartido. 
1. Seleccione **Discos** en el panel **Configuración**. 
1. Seleccione **Attach existing disks** (Conectar discos existentes) para asociar el disco compartido a la máquina virtual. 
1. Elija el disco compartido en la lista desplegable **Nombre del disco**. 
1. Seleccione **Guardar**.
1. Repita estos pasos para cada máquina virtual con SQL Server del nodo de clúster. 

Transcurridos unos instantes, el disco de datos se conecta a la máquina virtual y aparece en la lista de discos de datos de esa máquina virtual.

## <a name="initialize-shared-disk"></a>Inicialización de un disco compartido

Una vez que el disco compartido se conecte a todas las máquinas virtuales, puede inicializar los discos de las máquinas virtuales que participarán como nodos en el clúster. Inicialice los discos en **todas** las máquinas virtuales. 


Para inicializar los discos de la máquina virtual con SQL Server, siga estos pasos: 
 
1. Realice la conexión a una de las máquinas virtuales.
2. Desde dentro de la máquina virtual, abra el menú **Inicio** y escriba **diskmgmt.msc** en el cuadro de búsqueda para abrir la consola de **Administración de discos**.
3. Administración de discos reconocerá que hay un nuevo disco sin inicializar y se abrirá la ventana **Inicializar disco**.
4. Asegúrese de que el nuevo disco está seleccionado y seleccione **Aceptar** para inicializarlo.
5. El nuevo disco aparecerá como **sin asignar**. Haga clic con el botón derecho en cualquier lugar del disco y seleccione **Nuevo volumen simple**. Se abre la ventana **Asistente para nuevo volumen simple**.
6. Siga los pasos del asistente, conserve todos los valores predeterminados y, cuando haya terminado, seleccione **Finalizar**.
7. Cierre **Administración de discos**.
8. Se abre una ventana emergente que indica que es necesario dar formato al nuevo disco para poder usarlo. Seleccione **Dar formato al disco**.
9. En la ventana **Dar formato al nuevo disco**, active las opciones y seleccione **Iniciar**.
10. Recibirá una advertencia que indica que al dar formato a los discos se borrarán todos los datos. Seleccione **Aceptar**.
11. Cuando se complete el formato, seleccione **Aceptar**.
12. Repita estos pasos en cada máquina virtual con SQL Server que participará en el FCI. 

## <a name="create-windows-failover-cluster"></a>Creación de un clúster de conmutación por error de Windows

Los pasos necesarios para crear un clúster de conmutación por error de Windows Server varían en función de si se han implementado las máquinas virtuales con SQL Server en una sola subred o en varias. Para crear el clúster, siga los pasos del tutorial de un [escenario de varias subredes](availability-group-manually-configure-tutorial-multi-subnet.md#add-failover-cluster-feature) o un [escenario de una sola subred](availability-group-manually-configure-tutorial-single-subnet.md#create-the-cluster). Aunque estos tutoriales son para crear un grupo de disponibilidad, los pasos para crear el clúster son los mismos. 

## <a name="configure-quorum"></a>Configuración de un cuórum

Dado que el testigo de disco es la opción de cuórum más resistente y la solución FCI usa discos compartidos de Azure, se recomienda configurar un testigo de disco como solución de cuórum. 

Si tiene un número par de votos en el clúster, configure la [solución de cuórum](hadr-cluster-quorum-configure-how-to.md) que mejor se adapte a sus necesidades empresariales. Para más información, consulte [Cuórum con VM SQL Server](hadr-windows-server-failover-cluster-overview.md#quorum). 

## <a name="validate-cluster"></a>Validar el clúster

Valide el clúster en una de las máquinas virtuales mediante la interfaz de usuario de Administrador de clústeres de conmutación por error o PowerShell. 

Para validar el clúster mediante la interfaz de usuario, siga estos pasos: 

1. En **Administrador del servidor**, seleccione **Herramientas** y, después, seleccione **Administrador de clústeres de conmutación por error**.
1. En **Administrador de clústeres de conmutación por error**, seleccione **Acción** y, a continuación, seleccione **Validar configuración**.
1. Seleccione **Next** (Siguiente).
1. En **Seleccionar servidores o un clúster**, escriba el nombre de ambas máquinas virtuales.
1. En **Opciones de pruebas**, seleccione **Ejecutar solo las pruebas que seleccione**. 
1. Seleccione **Next** (Siguiente).
1. En **Selección de pruebas**, seleccione todas las pruebas, *excepto* **Almacenamiento**.
1. Seleccione **Next** (Siguiente).
1. En **Confirmación**, seleccione **Siguiente**.  El Asistente para **validar una configuración** ejecuta las pruebas de validación.


Para validar el clúster con PowerShell, ejecute el siguiente script en una sesión de PowerShell de administrador de una de las máquinas virtuales:

```powershell
Test-Cluster –Node ("<node1>","<node2>") –Include "Inventory", "Network", "System Configuration"
```

## <a name="test-cluster-failover"></a>Conmutación por error del clúster de prueba

Pruebe la conmutación por error del clúster. En **Administrador de clústeres de conmutación por error**, haga clic con el botón derecho en el clúster y seleccione **Más acciones** > **Mover principales recursos de clúster** > **Seleccionar nodo** y, después, seleccione el otro nodo del clúster. Mueva el recurso de clúster principal a cada nodo del clúster y, después, devuélvalo al nodo principal. Asegúrese de que puede mover correctamente el clúster a todos los nodos antes de instalar SQL Server.

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Prueba de la conmutación por error del clúster moviendo el recurso principal a los demás nodos":::

## <a name="add-shared-disks-to-cluster"></a>Adición de discos compartidos a un clúster

Use el Administrador de clústeres de conmutación por error para agregar al clúster los discos compartidos de Azure conectados. 

Para agregar discos a un clúster, siga estos pasos: 

1. En el panel **Administrador del servidor**, seleccione **Herramientas** y, después, **Administrador de clústeres de conmutación por error**.
1. Seleccione el clúster y expándalo en el panel de navegación. 
1. Seleccione **Almacenamiento** y, después, **Discos**. 
1. Haga clic con el botón derecho en **Discos** y seleccione **Agregar disco**: 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-add-disk.png" alt-text="Agregar disco":::

1. Elija el disco compartido de Azure en la ventana **Agregar discos a un clúster**.  Seleccione **Aceptar**.
 
    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-select-shared-disk.png" alt-text="Seleccionar disco":::

1. Después de agregar el disco compartido al clúster, lo verá en la Administrador de clústeres de conmutación por error.

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/cluster-shared-disk.png" alt-text="Disco de clúster":::



## <a name="create-sql-server-fci"></a>Crear la FCI de SQL Server

Después de haber configurado el clúster de conmutación por error y todos los componentes del clúster, incluido el almacenamiento, puede crear la FCI de SQL Server.

1. Establezca la conexión a la primera máquina virtual mediante el Protocolo de escritorio remoto (RDP).

1. En **Administrador de clústeres de conmutación por error**, asegúrese de que todos los recursos principales de clúster estén en la primera máquina virtual. Si es necesario, mueva los discos a esa máquina virtual.

1. Localice los medios de instalación. Si la máquina virtual usa una de las imágenes de Azure Marketplace, los medios se encuentran en `C:\SQLServer_<version number>_Full`. 

1. Seleccione **Setup** (Configuración).

1. En **Centro de instalación de SQL Server**, seleccione **Instalación**.

1. Seleccione **Nueva instalación de clúster de conmutación por error de SQL Server**. Siga las instrucciones del asistente para instalar la FCI de SQL Server.

1. En la página **Selección de disco de clúster**, seleccione todos los discos compartidos que se conectaron a la máquina virtual. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-disk-selection.png" alt-text="Selección de disco en clúster":::

1. En la página **Configuración de red de clúster**, la IP que proporcione varía en función de si las máquinas virtuales con SQL Server se implementaron en una sola subred o en varias. 

   1. En el caso de un **entorno de una sola subred**, especifique la dirección IP que planea agregar a [Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md)
   1. En el caso de un **entorno de varias subredes**, especifique la dirección IP secundaria en la subred de la _primera_ máquina virtual con SQL Server que designó anteriormente como la [dirección IP del nombre de red de la instancia de clúster de conmutación por error](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses):

   :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-1.png" alt-text="especifique la dirección IP secundaria en la subred de la primera máquina virtual con SQL Server que designó anteriormente como la dirección IP del nombre de red de la instancia de clúster de conmutación por error":::

1. En la página **Configuración del Motor de base de datos**, asegúrese de que los directorios de base de datos están en los discos compartidos de Azure. 

1. Después de completar las instrucciones del asistente, el programa de instalación instala la instancia de clúster de conmutación por error de SQL Server en el primer nodo.

1. Tras completar correctamente la instalación de instancia de clúster de conmutación por error en el primer nodo, conéctese al segundo nodo mediante RDP.

1. Abra el **Centro de instalación de SQL Server** y, a continuación, seleccione **Instalación**.

1. Seleccione **Agregar nodo a clúster de conmutación por error de SQL Server**. Siga las instrucciones del asistente para instalar SQL Server y agregar el nodo a la instancia de clúster de conmutación por error.

1. En el caso de un escenario de varias subredes, en **Configuración de red en clúster**, especifique la dirección IP secundaria en la subred de la _segunda_ subred de máquina virtual con SQL Server que designó anteriormente como la dirección IP del [nombre de red de la instancia de clúster de conmutación por error](failover-cluster-instance-prepare-vm.md#assign-secondary-ip-addresses)

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-cluster-network-secondary-ip-vm-2.png" alt-text="escriba la dirección IP secundaria en la subred de la segunda subred de máquina virtual con SQL Server que designó anteriormente como la dirección IP del nombre de red de la instancia de clúster de conmutación por error":::

    Después de seleccionar **Siguiente** en **Configuración de red de clúster**, el programa de instalación muestra un cuadro de diálogo que indica que SQL Server el programa de instalación detectó varias subredes como en la imagen de ejemplo.  Seleccione **Sí** para confirmar la acción. 

    :::image type="content" source="./media/failover-cluster-instance-azure-shared-disk-manually-configure/sql-install-multi-subnet-confirmation.png" alt-text="Confirmación de varias subredes":::

1. Después de completar las instrucciones del asistente, el programa de instalación agrega el segundo nodo FCI de SQL Server. 

1. Repita estos pasos en cualquier otra máquina virtual con SQL Server que desee que participe en la instancia de clúster de conmutación por error con SQL Server. 


>[!NOTE]
> Las imágenes de la galería de Azure Marketplace vienen con SQL Server Management Studio instalado. Si no ha usado una imagen de Marketplace, [descargue SQL Server Management Studio (SSMS)](/sql/ssms/ownload-sql-server-management-studio-ssms).


## <a name="register-with-sql-iaas-extension"></a>Registro mediante la extensión de IaaS de SQL 

Para administrar una máquina virtual con SQL Server desde el portal, regístrela con la extensión Agente de IaaS de SQL en [modo de administración ligera](sql-agent-extension-manually-register-single-vm.md#lightweight-mode); actualmente, es el único modo que se admite con FCI y SQL Server en las máquinas virtuales de Azure. 

Registre una máquina virtual con SQL Server en modo ligero con PowerShell ((-LicenseType puede ser `PAYG` o `AHUB`):

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>

# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configuración de la conectividad 

Si implementó las máquinas virtuales de SQL Server en varias subredes, omita este paso. Si implementó las máquinas virtuales de SQL Server en una sola subred, deberá configurar un componente adicional para enrutar el tráfico a la instancia de clúster de conmutación por error. Puede configurar un nombre de red virtual (VNN) con Azure Load Balancer o un nombre de red distribuida para una instancia de clúster de conmutación por error. [Revise las diferencias entre los dos](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn) y, luego, implemente un [nombre de red distribuida](failover-cluster-instance-distributed-network-name-dnn-configure.md) o un [nombre de red virtual y Azure Load Balancer](failover-cluster-instance-vnn-azure-load-balancer-configure.md) para la instancia de clúster de conmutación por error.  

## <a name="limitations"></a>Limitaciones

- Azure Virtual Machines admite el Coordinador de transacciones distribuidas de Microsoft (MSDTC) en Windows Server 2019 con almacenamiento en volúmenes compartidos en clúster (CSV) y un [equilibrador de carga estándar](../../../load-balancer/load-balancer-overview.md). Coordinador de transacciones distribuidas no es compatible con Windows Server 2016, ni con las versiones anteriores. 
- Solo se admite el registro con la extensión Agente de IaaS de SQL en [modo de administración ligero](sql-server-iaas-agent-extension-automate-management.md#management-modes).

## <a name="next-steps"></a>Pasos siguientes

Si los discos compartidos de Azure no son la solución de almacenamiento de la FCI adecuada para usted, considere la posibilidad de crear la FCI mediante [recursos compartidos de archivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md) o [Espacios de almacenamiento directo](failover-cluster-instance-storage-spaces-direct-manually-configure.md) en su lugar. 


Para obtener más información, consulte:

- [Clúster de conmutación por error de Windows Server con SQL Server en máquinas virtuales de Azure](hadr-windows-server-failover-cluster-overview.md)
- [Instancias de clúster de conmutación por error con SQL Server en Azure Virtual Machines](failover-cluster-instance-overview.md)
- [Información general de las instancias de clúster de conmutación por error](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Configuración de alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](hadr-cluster-best-practices.md)
