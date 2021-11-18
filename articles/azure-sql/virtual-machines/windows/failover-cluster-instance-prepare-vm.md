---
title: Preparación de máquinas virtuales para una FCI
description: Prepare las máquinas virtuales de Azure para utilizarlas con una instancia de clúster de conmutación por error (FCI) y SQL Server en máquinas virtuales de Azure.
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: ac7b5617cf81b2845701360cf1d7af3fbc4858c0
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132156089"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>Preparación de máquinas virtuales para una FCI (SQL Server en máquinas virtuales de Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

En este artículo se describe cómo preparar máquinas virtuales (VM) de Azure para utilizarlas con una instancia de clúster de conmutación por error (FCI) de SQL Server. La configuración varía en función de la solución de almacenamiento de una FCI, por lo que debe asegurarse de que elige la configuración correcta para su entorno y su empresa. 

Para más información, consulte la información general de [FCI con SQL Server en VM de Azure](failover-cluster-instance-overview.md) y [Procedimientos recomendados de clúster](hadr-cluster-best-practices.md). 

> [!NOTE]
> Ahora es posible migrar mediante lift and shift la solución de instancia de clúster de conmutación por error a SQL Server en máquinas virtuales de Azure mediante Azure Migrate. Consulte [Migración de una instancia de clúster de conmutación por error](../../migration-guides/virtual-machines/sql-server-failover-cluster-instance-to-sql-on-azure-vm.md) para más información. 

## <a name="prerequisites"></a>Requisitos previos 

- Una suscripción de Microsoft Azure. Comience a utilizarla [gratis](https://azure.microsoft.com/free/). 
- Un dominio de Windows en máquinas virtuales de Azure o un directorio activo local extendido a Azure con emparejamiento de red virtual.
- Una cuenta que tenga permisos para crear objetos en máquinas virtuales de Azure y en Active Directory.
- Una red virtual de Azure y una o varias subredes con espacio de direcciones IP suficiente para estos componentes:
   - Ambas máquinas virtuales
   - Una dirección IP para el clúster de conmutación por error de Windows
   - Una dirección IP para cada FCI
- DNS configurado en la red de Azure que señala a los controladores de dominio.



## <a name="choose-an-fci-storage-option"></a>Elección de una opción de almacenamiento de FCI

La configuración de la máquina virtual varía en función de la opción de almacenamiento que piensa utilizar para la instancia de clúster de conmutación por error de SQL Server. Antes de preparar la máquina virtual, revise las [opciones de almacenamiento de FCI disponibles](failover-cluster-instance-overview.md#storage), y elija la opción que mejor se adapte a sus necesidades empresariales y de entorno. Después, seleccione cuidadosamente las opciones de configuración de máquinas virtuales adecuadas en este artículo según la selección de almacenamiento. 

## <a name="choose-vm-availability"></a>Elección de la disponibilidad de la máquina virtual 

La característica de clúster de conmutación por error requiere que las máquinas virtuales se coloquen en un [conjunto de disponibilidad](../../../virtual-machines/linux/tutorial-availability-sets.md) o en una [zona de disponibilidad](../../../availability-zones/az-overview.md#availability-zones).

Seleccione cuidadosamente la opción de disponibilidad de la máquina virtual que coincida con la configuración de clúster prevista: 

- **Discos compartidos de Azure**: la opción de disponibilidad varía si usa discos SSD prémium o UltraDisk:
   - El **almacenamiento con redundancia de zona de los discos SSD prémium (ZRS)** admite [zonas de disponibilidad](../../../availability-zones/az-overview.md#availability-zones) en diferentes zonas. [ZRS de SSD prémium](../../../virtual-machines/disks-redundancy.md#zone-redundant-storage-for-managed-disks) replica el disco administrado de Azure de forma sincrónica en tres zonas de disponibilidad de Azure en la región seleccionada. Las máquinas virtuales que forman parte del clúster de conmutación por error se pueden colocar en distintas zonas de disponibilidad, lo que ayuda a lograr una instancia de clúster de conmutación por error (FCI) de SQL Server con redundancia de zona que proporciona un Acuerdo de Nivel de Servicio de disponibilidad de máquina virtual del 99,99 %. La latencia de disco para ZRS es mayor debido a la copia de datos entre zonas.
   - **Almacenamiento con redundancia local (LRS) de SSD prémium**: [Conjunto de disponibilidad](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) en dominios de error o actualización diferentes para [LRS de SSD prémium](../../../virtual-machines/disks-redundancy.md#locally-redundant-storage-for-managed-disks). También puede colocar las máquinas virtuales dentro de un [grupo con ubicación por proximidad](../../../virtual-machines/windows/proximity-placement-groups-portal.md) para ubicarlas más cerca entre sí. La combinación del conjunto de disponibilidad y el grupo con ubicación por proximidad proporciona la latencia más baja para los discos compartidos, ya que los datos se replican localmente en un centro de datos y proporciona un Acuerdo de Nivel de Servicio de disponibilidad de la máquina virtual del 99,95 %.
   - **Almacenamiento con redundancia local en disco Ultra**: [Zona de disponibilidad](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address) pero las máquinas virtuales deben estar situadas en la misma zona de disponibilidad. Los [discos Ultra](../../../virtual-machines/disks-enable-ultra-ssd.md) ofrecen la latencia de disco más baja y es la mejor opción para cargas de trabajo con uso intensivo de E/S. Puesto que todas las máquinas virtuales que forman parte de la FCI están en la misma zona de disponibilidad, la disponibilidad de la máquina virtual es solo del 99,9 %. 
- **Recursos compartidos de archivos prémium**: [Conjunto de disponibilidad](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set) o [Zona de disponibilidad](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address).
- **Espacios de almacenamiento directo**: [Conjunto de disponibilidad](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set).

> [!IMPORTANT]
> Una vez creada una máquina virtual el conjunto de disponibilidad no se puede establecer o cambiar.

## <a name="subnets"></a>Subredes 

En el caso de SQL Server en máquinas virtuales de Azure, tiene la opción de implementar las VM con SQL Server en una sola subred o en varias. 

La implementación de las máquinas virtuales en varias subredes saca provecho del clúster o la dependencia de las direcciones IP y se corresponde con la experiencia local de conexión a la instancia de clúster de conmutación por error. El enfoque de varias subredes se recomienda para SQL Server en máquinas virtuales de Azure para una administración más sencilla y tiempos de conmutación por error más rápidos. 

La implementación de las máquinas virtuales en una sola subred requiere una dependencia adicional en una instancia de Azure Load Balancer o un nombre de red distribuida (DNN) para enrutar el tráfico hacia la instancia de clúster de conmutación por error. 

Si implementa las VM con SQL Server en varias subredes, siga los pasos de esta sección para crear las redes virtuales con subredes adicionales y, después, una vez creadas las VM con SQL Server, [asigne direcciones IP secundarias](#assign-secondary-ip-addresses) dentro de esas subredes a las máquinas virtuales. La implementación de VM con SQL Server en una sola subred no requiere ninguna configuración de red adicional. 

# <a name="single-subnet"></a>[Subred única](#tab/single-subnet)

Coloque ambas máquinas virtuales en una sola subred que tenga suficientes direcciones IP tanto para las máquinas virtuales como para todas las FCI que podría instalar finalmente en el clúster. Este enfoque requiere un componente adicional para enrutar las conexiones a la FCI, como una instancia de Azure Load Balancer o un nombre de red distribuida (DNN). 

Si decide implementar las VM con SQL Server en una sola subred, [revise las diferencias entre las opciones de conectividad de Azure Load Balancer y DNN](hadr-windows-server-failover-cluster-overview.md#distributed-network-name-dnn) y decida qué opción funciona mejor antes de preparar el resto del entorno para la FCI.

La implementación de VM con SQL Server en una sola subred no requiere ninguna configuración de red adicional. 

# <a name="multi-subnet"></a>[Varias subredes](#tab/multi-subnet)

Si desea enrutar las conexiones directamente a la instancia de clúster de conmutación por error de SQL Server, coloque ambas máquinas virtuales en subredes independientes dentro de una red virtual. Asigne una dirección IP secundaria a la VM con SQL Server para la instancia del clúster de conmutación por error y, si se encuentra en Windows Server 2016 o versiones inferiores, asigne también una dirección IP secundaria adicional para el clúster de conmutación por error de Windows Server. Windows Server 2019 y las versiones posteriores usan un nombre de red distribuida (DNN) para el nombre del clúster, por lo que no es necesaria una dirección IP secundaria para el clúster. 

Este enfoque elimina la necesidad de una instancia de Azure Load Balancer o un nombre de red distribuida (DNN) al conectarse a la FCI de SQL Server. 

Si decide implementar las VM con SQL Server en varias subredes, primero deberá crear la red virtual con dos subredes adicionales y, una vez creadas las VM con SQL Server, [asigne direcciones IP secundarias a la máquina virtual](#assign-secondary-ip-addresses).  Para más información, consulte [¿Qué es Azure Virtual Network?](../../../virtual-network/virtual-networks-overview.md) Los nombres de subred y las direcciones IP de esta sección se proporcionan solo como un ejemplo y pueden variar en su entorno. Para crear la red virtual en Azure Portal, siga estos pasos:

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione **+ Crear**.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="Creación de un nuevo recurso en el grupo de recursos":::

1. Busque **red virtual** en el cuadro de búsqueda de **Marketplace** y elija el icono **Red virtual** de Microsoft. En la página **Red virtual**, seleccione **Crear**.  
1. En la pestaña **Básico** de la página **Crear una red virtual**, escriba la siguiente información: 
   1. En **Detalles del proyecto**, elija la **suscripción** de Azure adecuada y el **grupo de recursos** en los que tiene pensado implementar las VM con SQL Server. 
   1. En **Detalles de la instancia**, proporcione un nombre para la red virtual y elija la misma región que la del grupo de recursos en la lista desplegable.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/03-create-vnet-basics.png" alt-text="Elija el grupo de recursos que creó anteriormente y, a continuación, proporcione un nombre para la red virtual":::.

1. En la pestaña **Direcciones IP**, seleccione **+ Agregar subred** para agregar una subred adicional para la primera VM con SQL Server y rellene los valores siguientes: 
   1. Proporcione un valor para **Nombre de subred**, como **SQL-subnet-1**. 
   1. Proporcione un intervalo de direcciones de subred único dentro del espacio de direcciones de la red virtual. Por ejemplo, puede iterar el tercer octeto del intervalo de direcciones de DC-subnet en 1. 
      - Por ejemplo, si el intervalo **predeterminado** es *10.38.0.0/24*, escriba el intervalo de direcciones IP `10.38.1.0/24` para **SQL-subnet-1**. 
      - Del mismo modo, si el intervalo IP **predeterminado** es *10.5.0.0/24*, escriba `10.5.1.0/24` para la nueva subred. 
   1. Seleccione **Agregar** para agregar la nueva subred. 

     :::image type="content" source="./media/failover-cluster-instance-prepare-vm/05-create-vnet-ip-address-add-sql-subnet-1.png" alt-text="Asigne un nombre a la primera subred, por ejemplo, sql-subnet-1, y, a continuación, itere el tercer octeto en 1, de modo que si la dirección IP de DC-subnet es 10.5.0.0, la nueva subred debe ser 10.5.1.0.":::

1. Repita el paso anterior para agregar un intervalo de subred único adicional para la segunda máquina virtual de SQL Server con un nombre como **SQL-subnet-2**. Puede iterar el tercer octeto en uno de nuevo. 
   - Por ejemplo, si el intervalo IP **predeterminado** es *10.38.0.0/24* y **SQL-subnet-1** es *10.38.1.0/24*, escriba `10.38.2.0/24` para la nueva subred.
   - Del mismo modo, si el intervalo IP **predeterminado** es *10.5.0.0/24* y **SQL-subnet-1** es *10.5.1.0/24*, escriba el intervalo de direcciones IP `10.5.2.0/24` para **SQL-subnet-2**. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/06-create-vnet-ip-address-add-sql-subnet-2.png" alt-text="Asigne un nombre a la segunda subred, por ejemplo, sql-subnet-2, y, a continuación, itere el tercer octeto en 2, de modo que si la dirección IP de DC-subnet es 10.38.0.0/24, la nueva subred debe ser 10.38.2.0/24.":::

1. Después de agregar la segunda subred, revise los nombres e intervalos de subred (los intervalos de direcciones IP pueden diferir de la imagen). Si todo parece correcto, seleccione **Revisar y crear** y, a continuación, **Crear** para crear la nueva red virtual. 

   :::image type="content" source="./media/failover-cluster-instance-prepare-vm/07-create-vnet-ip-address.png" alt-text="Después de agregar la segunda subred, revise los nombres e intervalos de subred, como en el ejemplo de la imagen (aunque las direcciones IP pueden ser diferentes). Si todo parece correcto, seleccione Revisar y crear y, a continuación, Crear para crear la nueva red virtual.":::

   Azure le devuelve al panel del portal y le informa cuando se cree la red.

---

## <a name="configure-dns"></a>Configurar el DNS

Configure la red virtual para que use el servidor DNS. En primer lugar, identifique la dirección IP del sistema de nombres de dominio (DNS) y, a continuación, agréguela a la red virtual. 

### <a name="identify-dns-ip-address"></a>Identificación de la dirección IP de DNS

Identifique la dirección IP del servidor DNS y agréguela a la configuración de red virtual. En esta sección se muestra cómo identificar la dirección IP de DNS si el servidor DNS está en una máquina virtual de Azure. 

Para identificar la dirección IP de la máquina virtual del servidor DNS en Azure Portal, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione la máquina virtual del servidor DNS. 
1. En la página de la máquina virtual, elija **Redes** en el panel **Configuración**. 
1. Anote la dirección **IP privada de NIC**, ya que esta es la dirección IP del servidor DNS. En la imagen de ejemplo, la dirección IP privada es **10.38.0.4**. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-dc-vm-1-private-ip.png" alt-text="En la página DC-VM-1, elija Redes en el panel Configuración y, a continuación, anote la dirección IP privada de la NIC. Use esta dirección IP como servidor DNS.":::

### <a name="configure-virtual-network-dns"></a>Configuración de DNS de la red virtual

Configure la red virtual para que use esta dirección IP del servidor DNS. 

Para configurar la red virtual para DNS, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione la red virtual. 
1. Seleccione **Servidores DNS** en el panel **Configuración** y, a continuación, seleccione **Personalizado**. 
1. Escriba la dirección IP privada que identificó anteriormente en el campo **Dirección IP**, por ejemplo, `10.38.0.4`, o proporcione la dirección IP interna del servidor DNS interno. 
1. Seleccione **Guardar**. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-identify-dns-ip-address.png" alt-text="Seleccione Servidores DNS en el panel Configuración y, a continuación, seleccione Personalizado. Escriba la dirección IP privada que identificó anteriormente en el campo Dirección IP, por ejemplo, 10.38.0.4.":::

## <a name="create-the-virtual-machines"></a>Creación de las máquinas virtuales

Después de configurar la red virtual de la máquina virtual y elegir la disponibilidad de la máquina virtual, está preparado para crear máquinas virtuales. Puede optar por utilizar una imagen de Azure Marketplace, ya tenga o no SQL Server instalado. Sin embargo, si elige una imagen para SQL Server en máquinas virtuales de Azure, deberá desinstalar SQL Server de la máquina virtual antes de configurar la instancia de clúster de conmutación por error. 

### <a name="nic-considerations"></a>Consideraciones relacionadas con la NIC

En un clúster de conmutación por error invitado de máquinas virtuales de Azure, se recomienda una sola NIC por servidor (nodo de clúster). La red de Azure tiene redundancia física, que hace que las NIC adicionales sean innecesarias en un clúster invitado de máquina virtual de IaaS de Azure. Aunque el informe de validación del clúster emita una advertencia acerca de que los nodos solo son accesibles en una única red, esta advertencia puede omitirse en los clústeres de conmutación por error invitados de VM de IaaS de Azure.

Coloque ambas máquinas virtuales:

- En el mismo grupo de recursos de Azure en el que se encuentra su conjunto de disponibilidad, si está utilizando este tipo de conjuntos.
- En la misma red virtual que el controlador de dominio y el servidor DNS, o en una red virtual que tenga la conectividad adecuada con el controlador de dominio.
- En el conjunto de disponibilidad o la zona de disponibilidad de Azure.

Puede crear una máquina virtual de Azure mediante una imagen [con](sql-vm-create-portal-quickstart.md) o [sin](../../../virtual-machines/windows/quick-create-portal.md) SQL Server preinstalado. Si elige la imagen con SQL Server, deberá desinstalar manualmente la instancia de SQL Server antes de instalar la instancia de clúster de conmutación por error.  

### <a name="assign-secondary-ip-addresses"></a>Asignación de direcciones IP secundarias

Si implementó las VM con SQL Server en una sola subred, omita este paso. Si implementó las VM con SQL Server en varias subredes para mejorar la conectividad con la FCI, debe asignar las direcciones IP secundarias a cada máquina virtual. 

Asigne direcciones IP secundarias a cada VM con SQL Server para usarlas para el nombre de red de la instancia de clúster de conmutación por error y, para Windows Server 2016 y versiones anteriores, asigne también direcciones IP secundarias a cada VM con SQL Server para el nombre de red del clúster. Esto anula la necesidad de una instancia de Azure Load Balancer, como es el requisito en un entorno de una sola subred.  

En Windows Server 2016 y versiones anteriores, debe asignar una dirección IP secundaria adicional a cada máquina virtual de SQL Server para usarla para la dirección IP del clúster de Windows, ya que el clúster usa el **nombre de red del clúster** en lugar del nombre de red distribuida (DNN) predeterminado introducido en Windows Server 2019. Con un DNN, el objeto de nombre de clúster (CNO) se registra automáticamente con las direcciones IP de todos los nodos del clúster, lo que elimina la necesidad de una dirección IP dedicada del clúster de Windows.

Si está en Windows Server 2016 y versiones anteriores, siga los pasos descritos en esta sección para asignar una dirección IP secundaria a cada máquina virtual de SQL Server *tanto para* el nombre de red de FCI *como para* el clúster. 

Si está en Windows Server 2019 o posterior, asigne solo una dirección IP secundaria para el nombre de red de FCI y omita los pasos para asignar una dirección IP del clúster de Windows, a menos que planee configurar el clúster con un nombre de red virtual (VNN), en cuyo caso asigne ambas direcciones IP a cada máquina virtual de SQL Server como lo haría para Windows Server 2016. 

Para asignar direcciones IP secundarias adicionales a las máquinas virtuales, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com/) y seleccione la primera máquina virtual de SQL Server. 
1. Seleccione **Redes** en el panel **Configuración** y, a continuación, seleccione la **interfaz de red**: 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/19-sql-vm-network-interface.png" alt-text="Seleccione Redes en el panel Configuración y, a continuación, seleccione la interfaz de red.":::

1. En la página **Interfaz de red**, seleccione **Configuraciones IP** en el panel **Configuración** y, a continuación, elija **+ Agregar** para agregar una dirección IP adicional: 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/20-ip-configurations-add.png" alt-text="Configuraciones IP":::

1. En la página **Agregar configuración IP**, realice lo siguiente: 
   1. Especifique el valor de **Nombre** como la dirección IP del clúster de Windows, por ejemplo, **windows-cluster-ip** para Windows 2016 y versiones anteriores. Omita este paso si está en Windows Server 2019 o posterior. 
   1. Establezca **Asignación** en **Estática**.
   1. Escriba una **dirección IP** sin usar de la misma subred (**SQL-subnet-1**) que la VM con SQL Server, por ejemplo, `10.38.1.10`. 
   1. Deje el campo **Dirección IP pública** en el valor predeterminado de **Desasociar**. 
   1. Seleccione **Aceptar** para terminar de agregar la configuración IP. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/21-add-ip-windows-cluster.png" alt-text="Para agregar la dirección IP del clúster, escriba una dirección IP usada en la subred de la primera máquina virtual de SQL Server.":::

1. Seleccione **+ Agregar** de nuevo para configurar una dirección IP adicional para el nombre de red de FCI (con un nombre como **FCI-network-name**), especificando de nuevo una dirección IP sin usar de **SQL-subnet-1**, por ejemplo, `10.38.1.11`: 

   :::image type="content" source="./media/failover-cluster-instance-prepare-vm/22-add-fci-ip-address.png" alt-text="Seleccione + Agregar de nuevo para configurar una dirección IP adicional para el cliente de escucha del grupo de disponibilidad (con un nombre como availability-group-listener),especificando de nuevo una dirección IP sin usar de SQL-subnet-1, por ejemplo, 10.31.1.11":::

1. Repita estos pasos de nuevo para la segunda VM con SQL Server. Asigne dos direcciones IP secundarias no utilizadas de **SQL-subnet-2**. Use los valores de la tabla siguiente para agregar la configuración de IP (aunque las direcciones IP son solo ejemplos, las suyas pueden variar): 

   
    | **Campo** | Entrada | Entrada | 
    | --- | --- | --- |
    | **Nombre** |windows-cluster-ip | FCI-network-name |
    | **Asignación** | estática | estática |
    | **Dirección IP** | 10.38.2.10 | 10.38.2.11 | 
    |  |  |  |



## <a name="uninstall-sql-server"></a>Desinstalar SQL Server

Como parte del proceso de creación de la FCI, instalará SQL Server como una instancia en clúster en el clúster de conmutación por error. *Si ha implementado una máquina virtual con una imagen de Azure Marketplace sin SQL Server, puede omitir este paso.* Si ha implementado una imagen con SQL Server preinstalado, tendrá que anular el registro de la máquina virtual con SQL Server en la extensión Agente de IaaS de SQL y, a continuación, desinstalar SQL Server. 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>Anulación del registro de la extensión Agente de IaaS de SQL

Las imágenes de máquinas virtuales con SQL Server de Azure Marketplace se registran automáticamente con la extensión Agente de IaaS de SQL. Antes de desinstalar la instancia con SQL Server preinstalado, debe [anular el registro de cada máquina virtual con SQL Server en la extensión Agente de IaaS de SQL](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension). 

### <a name="uninstall-sql-server"></a>Desinstalar SQL Server

Después de anular el registro dela extensión, puede desinstalar SQL Server. Siga estos pasos en cada máquina virtual: 

1. Conéctese a la máquina virtual mediante RDP. La primera vez que se conecte a una máquina virtual con RDP, un mensaje le pregunta si desea permitir que el equipo se pueda detectar en la red. Seleccione **Sí**.
1. Abra **Programas y características** en el **Panel de control**. 
1. En **Programas y características**, haga clic con el botón derecho en **Microsoft SQL Server 201_ (64 bits)** y seleccione **Desinstalar o cambiar**.
1. Seleccione **Quitar**.
1. Seleccione la instancia predeterminada.
1. Elimine todas las características de **Database Engine Services**, **Analysis Services** y **Reporting Services - Native**. No quite nada de **SharedFeatures**. Verá algo parecido a la siguiente captura de pantalla: ![Seleccionar funcionalidades](./media/failover-cluster-instance-prepare-vm/remove-features-updated.png)
1. Seleccione **Siguiente** y después **Quitar**.
1. Una vez quitada correctamente la instancia, reinicie la máquina virtual. 

## <a name="open-the-firewall"></a>Apertura del firewall 

En cada máquina virtual, abra el puerto TCP de Firewall de Windows que SQL Server utiliza. De forma predeterminada SQL Server usa el puerto 1433, pero si lo ha cambiado en su entorno, abra el puerto que ha configurado para que la instancia de SQL Server lo use. El puerto 1433 se abre automáticamente en las imágenes de SQL Server implementadas desde Azure Marketplace. 

Si utiliza un [equilibrador de carga](failover-cluster-instance-vnn-azure-load-balancer-configure.md) para el escenario con una sola subred, también deberá abrir el puerto que usa el sondeo de estado. De forma predeterminada, el sondeo de estado usa el puerto 59999, pero puede ser cualquier puerto TCP que especifique al crear el equilibrador de carga. 

En esta tabla se detallan los puertos que es posible que necesite abrir, en función de la configuración de la FCI: 

   | Propósito | Port | Notas
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | Puerto normal para las instancias predeterminadas de SQL Server. Si usó una imagen de la galería, este puerto se abre automáticamente. </br> </br> **Utilizado por**: Todas las configuraciones de FCI. |
   | Sondeo de mantenimiento | TCP 59999 | Cualquier puerto TCP abierto. Configure el [sondeo de estado](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe) del equilibrador de carga y el clúster para utilizar este puerto. </br> </br> **Usado por**: FCI con equilibrador de carga en un escenario de una sola subred. |
   | Recurso compartido de archivos | UDP 445 | Puerto que el servicio del recurso compartido de archivos utiliza. </br> </br> **Utilizado por**: FCI con recurso compartido de archivos Premium. |

## <a name="join-the-domain"></a>Unir al dominio

También debe unir las máquinas virtuales al dominio. Puede hacerlo mediante una [plantilla de inicio rápido](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain). 

## <a name="review-storage-configuration"></a>Revisión de la configuración de almacenamiento

Las máquinas virtuales creadas en Azure Marketplace vienen con un almacenamiento asociado. Si planea configurar el almacenamiento de FCI mediante recursos compartidos de archivos Premium o discos compartidos de Azure, puede quitar el almacenamiento asociado para ahorrar en los costos porque el almacenamiento local no se utiliza para la instancia de clúster de conmutación por error. Sin embargo, es posible utilizar el almacenamiento asociado para las soluciones de FCI de espacios de almacenamiento directo, así que eliminarlos podría ser inútil en este caso. Revise la solución de almacenamiento de FCI para determinar si la eliminación del almacenamiento asociado ayuda a ahorrar costos. 


## <a name="next-steps"></a>Pasos siguientes

Ahora que ha preparado el entorno de la máquina virtual, está preparado para configurar la instancia de clúster de conmutación por error. 

Elija una de las siguientes guías para configurar el entorno de FCI que sea adecuado para su empresa: 
- [Configuración de una FCI con discos compartidos de Azure](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Configuración de una FCI con un recurso compartido Premium](failover-cluster-instance-premium-file-share-manually-configure.md)
- [Configuración de una FCI con espacios de almacenamiento directo](failover-cluster-instance-storage-spaces-direct-manually-configure.md)


Para obtener más información, consulte:

- [Clúster de conmutación por error de Windows Server con SQL Server en máquinas virtuales de Azure](hadr-windows-server-failover-cluster-overview.md)
- [Instancias de clúster de conmutación por error con SQL Server en Azure Virtual Machines](failover-cluster-instance-overview.md)
- [Información general de las instancias de clúster de conmutación por error](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)
- [Configuración de alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](hadr-cluster-best-practices.md)
