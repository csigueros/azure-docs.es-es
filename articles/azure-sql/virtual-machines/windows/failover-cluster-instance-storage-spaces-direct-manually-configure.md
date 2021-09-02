---
title: Creación de una FCI con Espacios de almacenamiento directo
description: Use Espacios de almacenamiento directo para crear una instancia de clúster de conmutación por error (FCI) con SQL Server en máquinas virtuales de Azure.
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.custom: na, devx-track-azurepowershell
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/18/2020
ms.author: mathoma
ms.openlocfilehash: 7c15dad5d82f538f6c6e81b0b99fed23d9bb0ae9
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123226768"
---
# <a name="create-an-fci-with-storage-spaces-direct-sql-server-on-azure-vms"></a>Creación de una FCI con Espacios de almacenamiento directo (SQL Server en Azure VM)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

En este artículo se explica cómo crear una instancia de clúster de conmutación por error (FCI) mediante [Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) con SQL Server en Azure Virtual Machines (VM). Espacios de almacenamiento directo actúa como una red de área de almacenamiento virtual (VSAN) basada en software que sincroniza el almacenamiento (discos de datos) entre los nodos (máquinas virtuales de Azure) en un clúster de Windows. 

Para más información, consulte la información general de [FCI con SQL Server en VM de Azure](failover-cluster-instance-overview.md) y los [procedimientos recomendados de clúster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Ahora es posible migrar mediante lift and shift la solución de instancia de clúster de conmutación por error a SQL Server en máquinas virtuales de Azure mediante Azure Migrate. Consulte [Migración de una instancia de clúster de conmutación por error](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) para más información. 


## <a name="overview"></a>Información general 

[Espacios de almacenamiento directo (S2D)](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) admite dos tipos de arquitecturas: convergidas e hiperconvergidas. Una infraestructura hiperconvergida coloca el almacenamiento en los mismos servidores que hospedan la aplicación en clúster, de modo que el almacenamiento se encuentra en cada nodo de FCI de SQL Server. 

En el diagrama siguiente se muestra la solución completa, que usa Espacios de almacenamiento directo hiperconvergidos con SQL Server en VM de Azure: 

![Diagrama de la solución completa, que usa Espacios de almacenamiento directo hiperconvergidos](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/00-sql-fci-s2d-complete-solution.png)

En el diagrama anterior se muestran los siguientes recursos en el mismo grupo de recursos:

- Dos máquinas virtuales en un clúster de conmutación por error de Windows Server. Cuando una máquina virtual está en un clúster de conmutación por error también se denomina un *nodo de clúster* o *nodos*.
- Cada máquina virtual tiene dos, o más, discos de datos.
- Espacios de almacenamiento directo sincroniza los datos del disco de datos y presenta el almacenamiento sincronizado como grupo de almacenamiento.
- El grupo de almacenamiento presenta un volumen compartido de clúster (CSV) al clúster de conmutación por error.
- El rol de clúster de FCI de SQL Server usa el CSV para las unidades de datos.
- Un equilibrador de carga de Azure que almacene la dirección IP para la FCI de SQL Server.
- Un conjunto de disponibilidad de Azure contiene todos los recursos.

   > [!NOTE]
   > Puede crear toda la solución en Azure a partir de una plantilla. Un ejemplo de plantilla está disponible en la página de [plantillas de inicio rápido de Azure](https://github.com/MSBrett/azure-quickstart-templates/tree/master/sql-server-2016-fci-existing-vnet-and-ad) de GitHub. Este ejemplo no se ha diseñado para ninguna carga de trabajo específica ni se ha probado para ella. Puede ejecutar la plantilla para crear una FCI de SQL Server con almacenamiento de Espacios de almacenamiento directo conectado a su dominio. Puede evaluar la plantilla y modificarla para adecuarla a sus fines.


## <a name="prerequisites"></a>Requisitos previos

Antes de completar las instrucciones de este artículo, ya debe tener:

- Suscripción a Azure. Comience de forma [gratuita](https://azure.microsoft.com/free/). 
- [Dos o más máquinas virtuales de Windows Azure preparadas](failover-cluster-instance-prepare-vm.md) en un [conjunto de disponibilidad](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).
- Una cuenta que tenga permisos para crear objetos en máquinas virtuales de Azure y en Active Directory.
- La versión más reciente de [PowerShell](/powershell/azure/install-az-ps). 


## <a name="add-the-windows-cluster-feature"></a>Incorporación de la característica de clúster de Windows

1. Conéctese a la primera máquina virtual con Protocolo de escritorio remoto (RDP) mediante una cuenta de dominio que sea miembro de los administradores locales y tenga permisos para crear objetos en Active Directory. Utilice esta cuenta para el resto de la configuración.

1. Agregue clústeres de conmutación por error a todas las máquinas virtuales.

   Para instalar clústeres de conmutación por error desde la interfaz de usuario, realice los pasos siguientes en las dos máquinas virtuales.

   1. En el **Administrador del servidor**, seleccione **Administrar** y, a continuación, seleccione **Agregar roles y características**.
   1. En el Asistente para **agregar roles y características**, seleccione **Siguiente** hasta llegar a **Seleccionar características**.
   1. En **Seleccionar características**, seleccione **Clúster de conmutación por error**. Incluya todas las características y herramientas de administración requeridas. 
   1. Seleccione **Agregar características**.
   1. Seleccione **Siguiente** y, después, **Finalizar** para instalar las características.

   Para instalar clústeres de conmutación por error con PowerShell, ejecute el siguiente script en una sesión de PowerShell de administrador en una de las máquinas virtuales:

   ```powershell
   $nodes = ("<node1>","<node2>")
   Invoke-Command  $nodes {Install-WindowsFeature Failover-Clustering -IncludeAllSubFeature -IncludeManagementTools}
   ```

Para más información acerca de los pasos siguientes, consulte las instrucciones de la sección "Paso 3: Configuración de Espacios de almacenamiento directo" en [Solución hiperconvergida con Espacios de almacenamiento directo en Windows Server 2016](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-3-configure-storage-spaces-direct).

## <a name="create-failover-cluster"></a>Creación de un clúster de conmutación por error

Para crear el clúster de conmutación por error, necesita:

- Los nombres de las máquinas virtuales que se convertirán en nodos del clúster.
- Un nombre para el clúster de conmutación por error.
- Una dirección IP para el clúster de conmutación por error. Puede usar una dirección IP que no se utilice en la misma red virtual de Azure y subred como nodos del clúster.


# <a name="windows-server-2012---2016"></a>[Windows Server 2012 a 2016](#tab/windows2012)

El siguiente script de PowerShell crea un clúster de conmutación por error para Windows Server 2012 a través de Windows Server 2016. Actualice el script con los nombres de los nodos (los nombres de las máquinas virtuales) y una dirección IP disponible desde la red virtual de Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage
```   

# <a name="windows-server-2019"></a>[Windows Server 2019](#tab/windows2019)

El siguiente script de PowerShell crea un clúster de conmutación por error para Windows Server 2019.  Actualice el script con los nombres de los nodos (los nombres de las máquinas virtuales) y una dirección IP disponible desde la red virtual de Azure.

```powershell
New-Cluster -Name <FailoverCluster-Name> -Node ("<node1>","<node2>") –StaticAddress <n.n.n.n> -NoStorage -ManagementPointNetworkType Singleton 
```

Para más información, consulte [Clúster de conmutación por error: objeto de red en clúster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97).

---


## <a name="configure-quorum"></a>Configuración de un cuórum

Aunque el testigo de disco es la opción de cuórum más resistente, no se admite para las instancias de clúster de conmutación por error configuradas con Espacios de almacenamiento directo. Por lo tanto, la opción de testigo en la nube es la solución de cuórum recomendada para este tipo de configuración del clúster de SQL Server en máquinas virtuales de Azure. En caso contrario, configure un testigo de recurso compartido de archivos. 

Si tiene un número par de votos en el clúster, configure la [solución de cuórum](hadr-cluster-quorum-configure-how-to.md) que mejor se adapte a sus necesidades empresariales. Para más información, consulte [Cuórum con VM con SQL Server](hadr-windows-server-failover-cluster-overview.md#quorum). 

## <a name="validate-the-cluster"></a>Validación del clúster

Valide el clúster en la interfaz de usuario o con PowerShell.

Para validar el clúster con la interfaz de usuario, realice los pasos siguientes en una de las máquinas virtuales:

1. En **Administrador del servidor**, seleccione **Herramientas** y, después, seleccione **Administrador de clústeres de conmutación por error**.
1. En **Administrador de clústeres de conmutación por error**, seleccione **Acción** y, a continuación, seleccione **Validar configuración**.
1. Seleccione **Next** (Siguiente).
1. En **Seleccionar servidores o un clúster**, escriba el nombre de ambas máquinas virtuales.
1. En **Opciones de pruebas**, seleccione **Ejecutar solo las pruebas que seleccione**. 
1. Seleccione **Next** (Siguiente).
1. En **Selección de pruebas**, seleccione todas las pruebas excepto **Almacenamiento**, como se muestra aquí:

   ![Seleccionar pruebas de validación de clústeres](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/10-validate-cluster-test.png)

1. Seleccione **Next** (Siguiente).
1. En **Confirmación**, seleccione **Siguiente**.

    El Asistente para **validar una configuración** ejecuta las pruebas de validación.

Para validar el clúster con PowerShell, ejecute el siguiente script en una sesión de PowerShell de administrador de una de las máquinas virtuales:

   ```powershell
   Test-Cluster –Node ("<node1>","<node2>") –Include "Storage Spaces Direct", "Inventory", "Network", "System Configuration"
   ```


## <a name="add-storage"></a>Agregue almacenamiento

Los discos Premium para Espacios de almacenamiento directo deben estar vacíos. No pueden contener particiones u otros datos. Para limpiar los discos, siga las instrucciones de [Implementación de Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-31-clean-drives).

1. [Habilitación de Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-35-enable-storage-spaces-direct).

   El siguiente script de PowerShell habilita los Espacios de almacenamiento directo:  

   ```powershell
   Enable-ClusterS2D
   ```

   En **Administrador de clústeres de conmutación por error**, puede ver el grupo de almacenamiento.

1. [Cree un volumen](/windows-server/storage/storage-spaces/deploy-storage-spaces-direct#step-36-create-volumes).

   Espacios de almacenamiento directo crea automáticamente un grupo de almacenamiento cuando se habilita. Ya está listo para crear un volumen. El cmdlet de PowerShell `New-Volume` automatiza el proceso de creación de volúmenes. Este proceso incluye el formato, la incorporación del volumen al clúster y la creación de un Volumen compartido de clúster (CSV). En el ejemplo se crea un CSV de 800 gigabytes (GB):

   ```powershell
   New-Volume -StoragePoolFriendlyName S2D* -FriendlyName VDisk01 -FileSystem CSVFS_REFS -Size 800GB
   ```   

   Después de ejecutar el comando anterior, se monta un volumen de 800 GB como un recurso de clúster. El volumen está en `C:\ClusterStorage\Volume1\`.

   En esta captura de pantalla se muestra un Volumen compartido de clúster con Espacios de almacenamiento directo:

   ![Captura de pantalla de un Volumen compartido de clúster con Espacios de almacenamiento directo](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/15-cluster-shared-volume.png)



## <a name="test-cluster-failover"></a>Conmutación por error del clúster de prueba

Pruebe la conmutación por error del clúster. En **Administrador de clústeres de conmutación por error**, haga clic con el botón derecho en el clúster y seleccione **Más acciones** > **Mover principales recursos de clúster** > **Seleccionar nodo** y, después, seleccione el otro nodo del clúster. Mueva el recurso de clúster principal a cada nodo del clúster y, después, devuélvalo al nodo principal. Si puede mover correctamente el clúster a cada nodo, está listo para instalar SQL Server.  

:::image type="content" source="media/failover-cluster-instance-premium-file-share-manually-configure/test-cluster-failover.png" alt-text="Prueba de la conmutación por error del clúster moviendo el recurso principal a los demás nodos":::

## <a name="create-sql-server-fci"></a>Crear la FCI de SQL Server

Después de haber configurado el clúster de conmutación por error y todos los componentes del clúster, incluido el almacenamiento, puede crear la FCI de SQL Server.

1. Conéctese a la primera máquina virtual con RDP.

1. En **Administrador de clústeres de conmutación por error**, asegúrese de que todos los recursos principales de clúster estén en la primera máquina virtual. Si es necesario, mueva todos los recursos a esa máquina virtual.

1. Localice los medios de instalación. Si la máquina virtual usa una de las imágenes de Azure Marketplace, los medios se encuentran en `C:\SQLServer_<version number>_Full`. Seleccione **Setup** (Configuración).

1. En **Centro de instalación de SQL Server**, seleccione **Instalación**.

1. Seleccione **Nueva instalación de clúster de conmutación por error de SQL Server**. Siga las instrucciones del asistente para instalar la FCI de SQL Server.

   Es preciso que los directorios de datos de FCI estén en el almacenamiento en clúster. Con Espacios de almacenamiento directo, no es un disco compartido, sino un punto de montaje para un volumen en cada servidor. Espacios de almacenamiento directo sincroniza el volumen entre ambos nodos. El volumen se presenta al clúster como un CSV. Utilice el punto de montaje de CSV para los directorios de datos.

   ![Directorios de datos](./media/failover-cluster-instance-storage-spaces-direct-manually-configure/20-data-dicrectories.png)

1. Después de completar las instrucciones en el asistente, el programa de instalación instalará una FCI de SQL Server en el primer nodo.

1. Una vez que el programa de instalación instale la FCI en el primer nodo, conéctese al segundo nodo con RDP.

1. Abra **Centro de instalación de SQL Server**. Seleccione **Instalación**.

1. Seleccione **Agregar nodo a clúster de conmutación por error de SQL Server**. Siga las instrucciones del asistente para instalar el servidor de SQL Server y agregarlo a la FCI.

   >[!NOTE]
   >Si usó una imagen de la galería de Azure Marketplace con SQL Server, las herramientas de SQL Server estaban incluidas en la imagen. Si no usó alguna de estas imágenes, instale las herramientas de SQL Server por separado. Para más información, consulte [Descargar SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
   >


## <a name="register-with-the-sql-vm-rp"></a>Registro con el proveedor de recursos de máquina virtual de SQL

Para administrar la VM con SQL Server desde el portal, regístrela con la extensión Agente de IaaS de SQL en [modo de administración ligera](sql-agent-extension-manually-register-single-vm.md#lightweight-mode); actualmente, es el único modo que se admite con FCI y SQL Server en las VM de Azure. 


Registre una máquina virtual con SQL Server en modo ligero con PowerShell:  

```powershell-interactive
# Get the existing compute VM
$vm = Get-AzVM -Name <vm_name> -ResourceGroupName <resource_group_name>
         
# Register SQL VM with 'Lightweight' SQL IaaS agent
New-AzSqlVM -Name $vm.Name -ResourceGroupName $vm.ResourceGroupName -Location $vm.Location `
   -LicenseType PAYG -SqlManagementType LightWeight  
```

## <a name="configure-connectivity"></a>Configuración de la conectividad 

Puede configurar un nombre de red virtual o un nombre de red distribuida para una instancia de clúster de conmutación por error. [Revise las diferencias entre los dos](hadr-windows-server-failover-cluster-overview.md#virtual-network-name-vnn) y, a continuación, implemente un [nombre de red distribuida](failover-cluster-instance-distributed-network-name-dnn-configure.md) o un [nombre de red virtual](failover-cluster-instance-vnn-azure-load-balancer-configure.md) para la instancia de clúster de conmutación por error.  

## <a name="limitations"></a>Limitaciones

- Azure Virtual Machines admite el Coordinador de transacciones distribuidas de Microsoft (MSDTC) en Windows Server 2019 con almacenamiento en volúmenes compartidos en clúster (CSV) y un [equilibrador de carga estándar](../../../load-balancer/load-balancer-overview.md).
- Los discos que se han conectado como discos con formato NTFS solo se pueden usar con Espacios de almacenamiento directo si la opción de elegibilidad del disco está deshabilitada o desactivada, cuando se agrega el almacenamiento al clúster. 
- Solo se admite el registro con la extensión Agente de IaaS de SQL en [modo de administración ligero](sql-server-iaas-agent-extension-automate-management.md#management-modes).
- Las instancias de clúster de conmutación por error que usan Espacios de almacenamiento directo como almacenamiento compartido no admiten el uso de un testigo de disco para el cuórum del clúster. Use un testigo en la nube en su lugar. 

## <a name="next-steps"></a>Pasos siguientes

Si aún no lo ha hecho, configure la conectividad a su FCI con un [nombre de red virtual y un equilibrador de carga de Azure](failover-cluster-instance-vnn-azure-load-balancer-configure.md) o un [nombre de red distribuida (DNN)](failover-cluster-instance-distributed-network-name-dnn-configure.md). 

Si Espacios de almacenamiento directo no es la solución de almacenamiento de FCI adecuada en su caso, considere la posibilidad de crear su FCI mediante [discos compartidos de Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md) o [recursos compartidos de archivos Premium](failover-cluster-instance-premium-file-share-manually-configure.md) en su lugar. 

Para obtener más información, consulte:

- [Clúster de conmutación por error de Windows Server con SQL Server en máquinas virtuales de Azure](hadr-windows-server-failover-cluster-overview.md)
- [Instancias de clúster de conmutación por error con SQL Server en Azure Virtual Machines](failover-cluster-instance-overview.md)
- [Información general de las instancias de clúster de conmutación por error](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Configuración de alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](hadr-cluster-best-practices.md)
