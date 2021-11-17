---
title: 'Tutorial: Configuración de un grupo de disponibilidad en varias subredes'
description: 'Este tutorial muestra cómo crear un grupo de disponibilidad AlwaysOn para SQL Server en Azure Virtual Machines. '
services: virtual-machines
documentationCenter: na
author: rajeshsetlem
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 11/10/2021
ms.author: rsetlem
ms.reviewer: mathoma
ms.openlocfilehash: aeef6f689e9a27b6a813b53e73ceb11ff982af97
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159279"
---
# <a name="tutorial-configure-an-availability-group-in-multiple-subnets-sql-server-on-azure-vms"></a>Tutorial: Configuración de un grupo de disponibilidad en varias subredes (SQL Server en máquinas virtuales de Azure)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Elimine la necesidad de una instancia de Azure Load Balancer para el grupo de disponibilidad Always On mediante la creación de las máquinas virtuales de SQL Server en varias subredes dentro de la misma red virtual de Azure.

Este tutorial muestra cómo crear un grupo de disponibilidad AlwaysOn para SQL Server en Azure Virtual Machines dentro de varias subredes. En el tutorial completo, se crea un clúster de conmutación por error con Windows Server y un grupo de disponibilidad con dos réplicas de SQL Server y un agente de escucha.

 
**Estimación de tiempo**: suponiendo que se hayan completado los [requisitos previos](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md), este tutorial debe tardar unos 30 minutos en completarse. 


## <a name="prerequisites"></a>Requisitos previos

En la tabla siguiente, se enumeran los [requisitos previos](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) que debe completar antes de iniciar este tutorial:

| Requisito | Descripción |
|----- |----- |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **Dos instancias de SQL Server** | - Cada máquina virtual en dos zonas de disponibilidad de Azure diferentes o en el mismo conjunto de disponibilidad <br/> - En subredes independientes dentro de una red virtual de Azure <br/> - Con dos direcciones IP secundarias asignadas a cada máquina virtual <br/> - En un solo dominio <br/> |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **Cuenta de servicio de SQL Server** | Una cuenta de dominio utilizada por el servicio SQL Server para cada máquina | 
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::   **Abra los puertos de firewall.** | - SQL Server: **1433** para instancia predeterminada <br/> - Punto de conexión de creación de reflejo de la base de datos: **5022** o cualquier puerto disponible <br/> |
|:::image type="icon" source="./media/availability-group-manually-configure-tutorial-multi-subnet/square.png" border="false":::**Cuenta de instalación de dominio** | - Administrador local en cada servidor SQL Server <br/> - Miembro del rol fijo del servidor sysadmin de SQL Server para cada instancia de SQL Server  |

En el tutorial, se da por supuesto que tiene conocimientos básicos sobre los [grupos de disponibilidad AlwaysOn de SQL Server](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). 

<a name="CreateCluster"></a>

## <a name="create-the-cluster"></a>Creación del clúster

El grupo de disponibilidad Always On reside sobre la infraestructura del clúster de conmutación por error con Windows Server, por lo que antes de implementar el grupo de disponibilidad, primero debe configurar el clúster de conmutación por error con Windows Server, lo que incluye agregar la característica, crear el clúster y establecer la dirección IP del clúster. 

### <a name="add-failover-cluster-feature"></a>Adición de la característica de clúster de conmutación por error

Agregue la característica de clúster de conmutación por error a ambas máquinas virtuales de SQL Server. Para ello, siga estos pasos: 

1. Conéctese a la máquina virtual de SQL Server mediante el Protocolo de escritorio remoto (RDP) con una cuenta de dominio que tenga permisos para crear objetos en AD, como la cuenta de dominio **CORP\Install** creada en el [artículo de requisitos previos](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md). 
1. Abra el **panel Administrador de servidores**.
1. Seleccione el vínculo **Agregar roles y características** en el panel.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="Selección del vínculo Agregar roles y características en el panel.":::

1. Seleccione **Siguiente** hasta llegar a la sección **Características de servidor**.
1. En **Características**, seleccione **Clúster de conmutación por error**.
1. Agregue más características necesarias.
1. Seleccione **Instalar** para agregar las características.
1. Repita los pasos en la otra máquina virtual con SQL Server. 


### <a name="create-cluster"></a>Crear clúster

Una vez agregada la característica de clúster a cada máquina virtual de SQL Server, está listo para crear el clúster de conmutación por error con Windows Server. 

Para crear el clúster, siga estos pasos: 

1. Use el Protocolo de escritorio remoto (RDP) para conectarse a la primera máquina virtual de SQL Server (por ejemplo, **SQL-VM-1**) con una cuenta de dominio que tenga permisos para crear objetos en AD, como la cuenta de dominio **CORP\Install** creada en el [artículo de requisitos previos](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md). 
2. En el panel **Administrador del servidor**, seleccione **Herramientas** y, después, **Administrador de clústeres de conmutación por error**.
3. En el panel izquierdo, haga clic con el botón derecho en **Administrador de clústeres de conmutación por error** y, a continuación, seleccione **Crear un clúster**.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/01-create-cluster.png" alt-text="Crear clúster":::

4. En el **Asistente para crear clúster**, cree un clúster de dos nodos avanzando por las páginas con la configuración que se proporciona en la tabla siguiente:

   | Página | Configuración |
   | --- | --- |
   | Antes de empezar |Usar predeterminados. |
   | Seleccionar servidores |Escriba el nombre de la primera instancia de SQL Server (por ejemplo, **SQL-VM-1**) en **Escriba el nombre del servidor** y seleccione **Agregar**.<br/>Escriba el nombre de la segunda instancia de SQL Server (por ejemplo, **SQL-VM-2**) en **Escriba el nombre del servidor** y seleccione **Agregar**. |
   | Advertencia de validación |Seleccione **Sí. Ejecutar las pruebas de validación de configuración y volver al proceso de creación del clúster al hacer clic en Siguiente**. |
   | Introducción | Seleccione Siguiente. |
   | Opciones de las pruebas | Elija **Run only the tests I select** (Ejecutar solo las pruebas que seleccione). |
   | Selección de pruebas | Desactive Almacenamiento. Asegúrese de que estén seleccionados **Inventario**, **Red** y **Configuración del sistema**. 
   | Confirmación | Seleccione Siguiente.<br/>Espere a que se complete la validación.<br/>Seleccione **Ver informe** para revisar el informe. Puede omitir de forma segura la advertencia sobre las máquinas virtuales a las que se puede acceder en una sola interfaz de red. La infraestructura de Azure tiene redundancia física y, por tanto, no es necesario agregar interfaces de red adicionales.<br/> Seleccione **Finalizar**.|
   | Punto de acceso para administrar el clúster |Escriba un nombre de clúster, por ejemplo **SQLAGCluster1** en **Nombre del clúster**.|
   | Confirmación | Desactive la opción **Agregar todo el almacenamiento apto al clúster** y seleccione **Siguiente**. |
   | Resumen | Seleccione **Finalizar**. | 

   >[!WARNING]
   >Si no desactiva la opción **Agregar todo el almacenamiento apto al clúster**, Windows desasocia los discos virtuales durante el proceso de agrupación en clústeres. Como resultado, no aparecen en el Administrador de discos ni en el Explorador hasta que se quita el almacenamiento del clúster y se vuelve a asociar mediante PowerShell. 
   > 
   

### <a name="set-the-failover-cluster-ip-address"></a>Establecimiento de la dirección IP del clúster de conmutación por error

Normalmente, la dirección IP asignada al clúster es la misma dirección IP asignada a la máquina virtual, lo que significa que, en Azure, la dirección IP del clúster estará en estado de error y no se puede poner en línea. Cambie la dirección IP del clúster para poner en línea el recurso de dirección IP. 

Durante los requisitos previos, debe haber [asignado direcciones IP secundarias](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#add-secondary-ips-to-sql-server-vms) a cada máquina virtual de SQL Server, como se muestra en la tabla de ejemplo siguiente (las direcciones IP específicas pueden variar): 

   | Nombre de la máquina virtual | Nombre de subred | Intervalo de direcciones de subred | Nombre de la dirección IP secundaria | Dirección IP secundaria |
   | --- | --- | --- | --- | --- |
   | SQL-VM-1 | SQL-subnet-1 | 10.38.1.0/24 | windows-cluster-ip | 10.38.1.10 |
   | SQL-VM-2 | SQL-subnet-2 | 10.38.2.0/24 | windows-cluster-ip | 10.38.2.10

Asigne estas direcciones IP como direcciones IP del clúster para cada subred pertinente. 

> [!NOTE]
> En Windows Server 2019, el clúster crea un **nombre de servidor distribuido** en lugar del **nombre de red del clúster** y el objeto de nombre del clúster (CNO) se registra automáticamente con las direcciones IP para todos los nodos del clúster, lo que elimina la necesidad de una dirección IP dedicada del clúster con Windows. Si está en Windows Server 2019, omita esta sección y cualquier otro paso que haga referencia a los **recursos básicos del clúster** o cree un clúster basado en el nombre de red virtual (VNN) mediante [PowerShell](failover-cluster-instance-storage-spaces-direct-manually-configure.md#create-windows-failover-cluster). Consulte el blog [Clúster de conmutación por error: objeto de red del clúster](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) para más información. 


Para cambiar la dirección IP del clúster, siga estos pasos: 

1. En **Administrador de clústeres de conmutación por error**, desplácese hacia abajo hasta **Recursos principales de clúster** y expanda los detalles del clúster. Debería de ver el **Nombre** y dos recursos de **Dirección IP** de cada subred en estado **Con errores**.
1. Haga clic con el botón derecho en el primer recurso de **Dirección IP** con errores y, después, seleccione **Propiedades**.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/02-failed-ip-address.png" alt-text="Propiedades del clúster":::

1. Seleccione **Dirección IP estática** y actualice la dirección IP a la [dirección IP dedicada del clúster con Windows](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#add-secondary-ips-to-sql-server-vms) en la subred que asignó a la primera máquina virtual de SQL Server (por ejemplo, **SQL-VM-1**). Seleccione **Aceptar**.
   
    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/03-first-static-ip-address.png" alt-text="Selección de **Dirección IP estática** y actualización de la dirección IP a la dirección IP dedicada del clúster con Windows en la misma subred que asignó a la máquina virtual de SQL Server en el artículo de requisitos previos":::

1. Repita los pasos para el segundo recurso de **dirección IP** con errores mediante la [dirección IP dedicada del clúster con Windows](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#add-secondary-ips-to-sql-server-vms) para la subred de la segunda máquina virtual de SQL Server (por ejemplo, **SQL-VM-2**). 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/04-second-static-ip-address.png" alt-text="Repetición de los pasos para el segundo recurso de **dirección IP** con errores mediante la dirección IP dedicada del clúster con Windows para la subred de la otra máquina virtual de SQL Server.":::

1. En la sección **Recursos principales de clúster**, haga clic con el botón derecho en el nombre del clúster y seleccione **Poner en línea**. Espere hasta que el nombre y uno de los recursos de dirección IP estén en línea. 

Puesto que las máquinas virtuales de SQL Server están en subredes diferentes, el clúster tendrá una dependencia OR en las dos direcciones IP dedicadas del clúster con Windows. Cuando el recurso de nombre de clúster está en línea, actualiza el servidor de controlador de dominio (DC) con una cuenta de equipo de Active Directory (AD). Si los recursos básicos del clúster mueven nodos, una dirección IP se queda sin conexión, mientras que la otra vuelve a estar en línea, actualizando el servidor del controlador de dominio con la nueva asociación de direcciones IP.  

>[!TIP]
> Al ejecutar el clúster en máquinas virtuales de Azure en un entorno de producción, cambie la configuración del clúster a un estado de supervisión más relajado para mejorar la estabilidad y confiabilidad del clúster en un entorno en la nube. Para más información, consulte [Procedimientos recomendados para la configuración de HADR (SQL Server en Azure Virtual Machines)](hadr-cluster-best-practices.md#checklist).

## <a name="configure-quorum"></a>Configuración de un cuórum

En un clúster de dos nodos, se necesita un dispositivo de cuórum para la confiabilidad y estabilidad del clúster. En las máquinas virtuales de Azure, el testigo en la nube es la configuración de cuórum recomendada, aunque hay [otras opciones disponibles](hadr-cluster-quorum-configure-how-to.md). Los pasos de esta sección configuran un testigo en la nube para el cuórum. Identifique las claves de acceso de la cuenta de almacenamiento y, a continuación, configure el testigo en la nube. 

## <a name="get-access-keys-for-storage-account"></a>Obtención de las claves de acceso de la cuenta de almacenamiento 

Cuando se crea una cuenta de Microsoft Azure Storage, esta se asocia a dos claves de acceso que se generan automáticamente: la clave de acceso primaria y la secundaria. Utilice la clave de acceso primaria la primera vez que cree el testigo en la nube, pero posteriormente no hay ninguna restricción sobre qué clave usar para el testigo en la nube. 

Utilice Azure Portal para ver y copiar las claves de acceso de almacenamiento de la cuenta de Azure Storage creada en el [artículo de requisitos previos](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#create-azure-storage-account). 


Para ver y copiar las claves de acceso de almacenamiento, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione la cuenta de almacenamiento que ha creado. 
1. En **Seguridad y redes**, seleccione **Claves de acceso**. 
1. Seleccione **Mostrar claves** y copie la clave. 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/05-storage-account-keys.png" alt-text="Selección de **Mostrar claves** y copia de la clave":::

### <a name="configure-cloud-witness"></a>Configuración del testigo en la nube

Una vez copiada la clave de acceso, cree el testigo en la nube para el cuórum del clúster. 

Para crear el testigo en la nube, siga estos pasos: 

1. Conéctese a la primera máquina virtual de SQL Server, **SQL-VM-1**, mediante el escritorio remoto.
1. Abra **Windows PowerShell** en modo de administrador.
1. Ejecute el script de PowerShell para establecer el valor de TLS (Seguridad de la capa de transporte) de la conexión en 1.2:

    ```powershell
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    ```
    
1. Use PowerShell para configurar el testigo en la nube. Reemplace los valores del nombre de la cuenta de almacenamiento y la clave de acceso por la información específica:  

    ```powershell
    Set-ClusterQuorum -CloudWitness -AccountName "Storage_Account_Name&quot; -AccessKey &quot;Storage_Account_Access_Key"
    ```
    
1. La salida del ejemplo siguiente indica que se ha realizado correctamente:  

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/06-configure-quorum.png" alt-text="La salida debe mostrar el testigo en la nube como este ejemplo.":::

Los recursos básicos del clúster están configurados con un testigo en la nube.

## <a name="enable-ag-feature"></a>Habilitación de la característica de grupos de disponibilidad 

La característica de grupos de disponibilidad AlwaysOn está deshabilitada de manera predeterminada. Use el **Administrador de configuración de SQL Server** para habilitar la característica en ambas instancias de SQL Server. 

Para habilitar la característica de grupos de disponibilidad, siga estos pasos: 

1. Inicie el archivo RDP en la primera máquina virtual de SQL Server (por ejemplo, **SQL-VM-1**) con una cuenta de dominio que sea miembro del rol fijo de servidor sysadmin, como la cuenta de dominio **CORP\Install** creada en el [documento de requisitos previos](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md).
1. Desde la pantalla **Inicio** de una de las máquinas virtuales de SQL Server, inicie el **Administrador de configuración de SQL Server**.
1. En el árbol del explorador, resalte **Servicios de SQL Server**, haga clic con el botón derecho en el servicio **SQL Server (MSSQLSERVER)** y seleccione **Propiedades**.
1. Seleccione la pestaña **Alta disponibilidad AlwaysOn** y, a continuación, active la casilla para **Habilitar los grupos de disponibilidad de AlwaysOn**:

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/08-enable-always-on.png" alt-text="Habilitar los grupos de disponibilidad de AlwaysOn":::

1. Seleccione **Aplicar**. Seleccione **Aceptar** en el cuadro de diálogo emergente.
1. Reinicie el servicio SQL Server.
1. Repita estos pasos para la otra instancia de SQL Server.


## <a name="create-database"></a>Crear base de datos

Para la base de datos, puede seguir los pasos descritos en esta sección para crear una nueva base de datos o restaurar una [base de datos AdventureWorks](/sql/samples/sql-samples-where-are). También debe hacer una copia de seguridad de la base de datos para inicializar la cadena de registros. Las bases de datos de las que no se ha realizado una copia de seguridad no cumplen los requisitos previos para un grupo de disponibilidad. 

Para crear una base de datos, siga estos pasos: 

1. Inicie el archivo RDP en la primera máquina virtual de SQL Server (por ejemplo, **SQL-VM-1**) con una cuenta de dominio que sea miembro del rol fijo de servidor sysadmin, como la cuenta de dominio **CORP\Install** creada en el [documento de requisitos previos](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md).
1. Abra **SQL Server Management Studio** y conéctese a la instancia de SQL Server.
1. En el **Explorador de objetos**, haga clic con el botón derecho en **Bases de datos** y seleccione **Nueva base de datos**.
1. En **Nombre de la base de datos**, escriba **MyDB1**. 
1. Seleccione la página **Opciones** y elija **Completo** en la lista desplegable **Modelo de recuperación**, si no está relleno de manera predeterminada. La base de datos debe estar en modo de recuperación completa para cumplir los requisitos previos para participar en un grupo de disponibilidad. 
1. Seleccione **Aceptar** para cerrar la página **Nueva base de datos** y crear la nueva base de datos. 


Para hacer una copia de seguridad de la base de datos, siga estos pasos: 

1. En el **Explorador de objetos**, haga clic con el botón derecho en la base de datos, resalte **Tareas** y, a continuación, seleccione **Hacer copia de seguridad...** .

1. Seleccione **Aceptar** para hacer una copia de seguridad completa de la base de datos en la ubicación de copia de seguridad predeterminada.

## <a name="create-file-share"></a>Creación de un recurso compartido de archivos

Cree un recurso compartido de archivos de copia de seguridad al que las máquinas virtuales de SQL Server y sus cuentas de servicio tengan acceso. 

Para crear el recurso compartido de archivos de copia de seguridad, siga estos pasos: 


1. En la primera máquina virtual de SQL Server, en **Administrador del servidor**, seleccione **Herramientas**. Abra **Administración de equipos**.

2. Seleccione **Carpetas compartidas**.

3. Haga clic con el botón derecho en **Recursos compartidos**, seleccione **Nuevo recurso compartido...** y, a continuación, use el **Asistente para crear una carpeta compartida** para crear un recurso compartido.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/09-new-share.png" alt-text="Seleccione Nuevo recurso compartido":::

4. En **Ruta de acceso a la carpeta**, seleccione **Examinar** y busque o cree una ruta de acceso para la carpeta compartida de copia de seguridad de base de datos, por ejemplo, `C:\Backup`. Seleccione **Next** (Siguiente).

5. En **Nombre, descripción y configuración**, compruebe el nombre del recurso compartido y la ruta de acceso. Seleccione **Next** (Siguiente).

6. En **Permisos de la carpeta compartida**, establezca **Personalizar permisos**. Seleccione **Personalizado...** .

7. En **Personalizar permisos**, seleccione **Agregar...**

8. Active **Control total** para conceder acceso completo al recurso compartido a *ambas* cuentas de servicio de SQL Server (`Corp\SQLSvc1` y `Corp\SQLSvc2`):  

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/10-backup-share-permission.png" alt-text="Asegurarse de que las cuentas de servicio de SQL Server de ambos servidores tengan control total.":::

9. Seleccione **Aceptar**.

10. En **Permisos de la carpeta compartida**, seleccione **Finalizar**. Seleccione **Finalizar** otra vez.  

## <a name="create-availability-group"></a>Crear grupo de disponibilidad 

Una vez realizada la copia de seguridad de la base de datos, está listo para crear el grupo de disponibilidad, que toma automáticamente una copia de seguridad completa y una copia de seguridad del registro de transacciones de la réplica principal de SQL Server y la restaura en la instancia secundaria de SQL Server con la opción **NORECOVERY**. 

Para crear el grupo de disponibilidad, siga estos pasos. 

1. En el **Explorador de objetos** de SQL Server Management Studio (SSMS), en la primera máquina virtual de SQL Server (por ejemplo, **SQL-VM-1**), haga clic con el botón derecho en **Alta disponibilidad AlwaysOn** y seleccione **Asistente para nuevo grupo de disponibilidad**.

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/11-new-ag-wizard.png" alt-text="Iniciar el Asistente para nuevo grupo de disponibilidad":::

1. En la página **Introducción**, seleccione **Siguiente**. En la página **Especificar nombre de grupo de disponibilidad**, escriba el nombre del grupo de disponibilidad en **Nombre del grupo de disponibilidad**; por ejemplo, **AG1**. Seleccione **Next** (Siguiente).

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/12-new-ag-name.png" alt-text="Asistente para nuevo grupo de disponibilidad: Especificar nombre de grupo de disponibilidad":::

1. En la página **Seleccionar bases de datos**, seleccione la base de datos y seleccione **Siguiente**. Si la base de datos no cumple los requisitos previos, asegúrese de que esté en modo de recuperación completa y [realice una copia de seguridad](#create-database): 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/13-new-ag-select-database.png" alt-text="Asistente para nuevo grupo de disponibilidad: Seleccionar bases de datos":::

1. En la página **Especificar réplicas**, seleccione **Agregar réplica**.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/14-new-ag-add-replica.png" alt-text="Asistente para nuevo grupo de disponibilidad: Especificar réplicas":::

1. Se abre el cuadro de diálogo **Conectar con el servidor** . Escriba el nombre del segundo servidor en **Nombre del servidor**, por ejemplo, **SQL-VM-2**. Seleccione **Conectar**.
1. En la página **Especificar réplicas**, active las casillas **Conmutación automática por error** y elija **Confirmación sincrónica** para el modo de disponibilidad en la lista desplegable:

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/15-new-ag-replica.png" alt-text=" En la página **Especificar réplicas**, active las casillas Conmutación automática por error y elija Confirmación sincrónica para el modo de disponibilidad.":::

1. Seleccione la pestaña **Puntos de conexión** para confirmar que los puertos usados para el punto de conexión de creación de reflejo de la base de datos son los que [abrió en el firewall](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md#configure-the-firewall): 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/16-endpoint.png" alt-text="Asistente para nuevo grupo de disponibilidad: Seleccionar sincronización de datos iniciales":::

1. Seleccione la pestaña **Cliente de escucha** y elija **Crear un cliente de escucha del grupo de disponibilidad** con los siguientes valores para el cliente de escucha: 

   |Campo | Value |
   | --- | --- |
   | Nombre DNS del cliente de escucha: | AG1-Listener |
   | Port | Use el puerto predeterminado de SQL Server. 1433 |
   | Modo de red: | IP estática |

1. Seleccione **Agregar** para proporcionar la dirección IP dedicada secundaria del cliente de escucha para ambas máquinas virtuales de SQL Server. 

   En la tabla siguiente, se muestran las direcciones IP de ejemplo creadas para el cliente de escucha a partir del [documento de requisitos previos](availability-group-manually-configure-prerequisites-tutorial-multi-subnet.md) (aunque las direcciones IP específicas pueden variar):
   
   | Nombre de la máquina virtual | Nombre de subred | Intervalo de direcciones de subred | Nombre de la dirección IP secundaria | Dirección IP secundaria |
   | --- | --- | --- | --- | --- |
   | SQL-VM-1 | SQL-subnet-1 | 10.38.1.0/24 | availability-group-listener | 10.38.1.11 |
   | SQL-VM-2 | SQL-subnet-2 | 10.38.2.0/24 | availability-group-listener | 10.38.2.11

1. Elija la primera subred (por ejemplo, 10.38.1.0/24) en la lista desplegable del cuadro de diálogo **Agregar dirección IP** y, a continuación, proporcione la **dirección IPv4** del cliente de escucha dedicado secundario, por ejemplo, `10.38.1.11`. Seleccione **Aceptar**. 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/18-add-listener-ip-subnet-1.png" alt-text="Elija la primera subred (por ejemplo, 10.38.1.0/24) en la lista desplegable del cuadro de diálogo Agregar dirección IP y, a continuación, proporcione la dirección IPv4 del cliente de escucha dedicado secundario, por ejemplo, 10.38.1.11.":::

1. Repita este paso de nuevo, pero elija la otra subred de la lista desplegable (por ejemplo, 10.38.2.0/24) y proporcione la **dirección IPv4** del cliente de escucha dedicado secundario de la otra máquina virtual de SQL Server, por ejemplo, `10.38.2.11`. Seleccione **Aceptar**. 
    

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/19-add-listener-ip-subnet-2.png" alt-text="Adición de la dirección IP del cliente de escucha":::    

1. Después de revisar los valores en la página **Cliente de escucha**, seleccione **Siguiente**: 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/20-listener.png" alt-text="Después de revisar los valores en la página Cliente de escucha, seleccione Siguiente:":::

1. En la página **Seleccionar sincronización de datos iniciales**, elija **Copia de seguridad completa de registros y bases de datos** y proporcione la [ubicación del recurso compartido de red que creó anteriormente](#create-file-share), por ejemplo, `\\SQL-VM-1\Backup`. 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/21-full-data-sync.png" alt-text="Elección de la sincronización de datos completa":::

   > [!NOTE]
   > La sincronización completa realiza una copia de seguridad completa de la base de datos en la primera instancia de SQL Server y la restaura en la segunda instancia. Para bases de datos grandes, no se recomienda la sincronización completa porque puede llevar mucho tiempo. Puede reducir este tiempo realizando manualmente una copia de seguridad de la base de datos y restaurándola con `NO RECOVERY`. Si ya se ha restaurado la base de datos con `NO RECOVERY` en la segunda instancia de SQL Server antes de configurar el grupo de disponibilidad, elija **Solo unirse**. Si desea realizar la copia de seguridad después de configurar el grupo de disponibilidad, elija **Omitir la sincronización de datos iniciales**.
   

1. En la página **Validación**, confirme que se han superado todas las comprobaciones de validación y, a continuación, elija **Siguiente**: 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/22-validation.png" alt-text="Asistente para nuevo grupo de disponibilidad: Validación":::

   
1. En la página **Resumen**, seleccione **Finalizar** y espere hasta que el asistente configure el nuevo grupo de disponibilidad. Elija **Más detalles** en la página **Progreso** para ver el progreso detallado. Cuando vea que el **asistente se haya completado correctamente** en la página **Resultados**, inspeccione el resumen para comprobar que el grupo de disponibilidad y el cliente de escucha se hayan creado correctamente. 

     :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/23-results.png" alt-text="Asistente para nuevo grupo de disponibilidad: Resultados":::

1. Seleccione **Cerrar** para salir del asistente.

## <a name="check-availability-group"></a>Comprobación del grupo de disponibilidad

Puede comprobar el estado del grupo de disponibilidad mediante **SQL Server Management Studio** y el **Administrador de clústeres de conmutación por error**.  

Para comprobar el estado del grupo de disponibilidad, siga estos pasos: 

1. En el **Explorador de objetos**, expanda **Alta disponibilidad AlwaysOn** y, a continuación, expanda **Grupos de disponibilidad**. Ahora debería de ver el nuevo grupo de disponibilidad en este contenedor. Haga clic con el botón derecho en el grupo de disponibilidad y seleccione **Mostrar el panel**.

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/24-show-dashboard.png" alt-text="Visualización del panel del grupo de disponibilidad":::

   El panel del grupo de disponibilidad muestra la réplica, el modo de conmutación por error de cada réplica y el estado de sincronización, como en el ejemplo siguiente: 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/25-ag-dashboard.png" alt-text="Panel del grupo de disponibilidad":::


2. Abra el **Administrador de clústeres de conmutación por error**, seleccione el clúster y elija **Roles** para ver el rol del grupo de disponibilidad que ha creado en el clúster. Elija el rol **AG1** y seleccione la pestaña **Recursos** para ver el cliente de escucha y las direcciones IP asociadas, como en el ejemplo siguiente: 

   :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/26-cluster-manager.png" alt-text="Grupo de disponibilidad en el administrador de clústeres de conmutación por error":::

En este momento, tiene un grupo de disponibilidad con réplicas en dos instancias de SQL Server y un cliente de escucha del grupo de disponibilidad correspondiente. Puede conectarse mediante el cliente de escucha y puede mover el grupo de disponibilidad entre instancias mediante **SQL Server Management Studio**. 

> [!WARNING]
> No trate de realizar una conmutación por error del grupo de disponibilidad mediante el Administrador de clústeres de conmutación por error. Todas las operaciones de conmutación por error se deben realizar desde dentro de **SQL Server Management Studio**, como mediante el **panel Always On** o Transact-SQL (T-SQL). Para más información, consulte [Clústeres de conmutación por error y grupos de disponibilidad AlwaysOn (SQL Server)](/sql/database-engine/availability-groups/windows/failover-clustering-and-always-on-availability-groups-sql-server).



## <a name="test-listener-connection"></a>Prueba de la conexión del cliente de escucha

Una vez que el grupo de disponibilidad esté listo y el cliente de escucha se haya configurado con las direcciones IP secundarias adecuadas, pruebe la conexión con el cliente de escucha.  

Para probar la conexión, siga estos pasos: 

1. Use RDP para conectarse a una instancia de SQL Server que se encuentre en la misma red virtual, pero que no posee la réplica, como la otra instancia de SQL Server dentro del clúster, o cualquier otra máquina virtual con **SQL Server Management Studio** instalado en ella.  

2. Abra **SQL Server Management Studio** y, en el cuadro de diálogo **Conectar con el servidor**, escriba el nombre del cliente de escucha (por ejemplo, **AG1-Listener**) en **Nombre del servidor** y, a continuación, seleccione **Opciones**: 

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/27-ssms-listener-connect.png" alt-text="Abra SQL Server Management Studio y, en Nombre del servidor, escriba el nombre del cliente de escucha, por ejemplo, AG1-Listener.":::

3. Escriba `MultiSubnetFailover=True` en la ventana **Parámetros de conexión adicionales** y, a continuación, elija **Conectar** para conectarse automáticamente a la instancia que hospeda la réplica principal de SQL Server:  

    :::image type="content" source="./media/availability-group-manually-configure-tutorial-multi-subnet/28-ssms-connection-parameters.png" alt-text="Conexión de SSMS":::

> [!NOTE]
> - Al conectarse al grupo de disponibilidad en distintas subredes, la configuración `MultiSubnetFailover=true` proporciona una detección y conexión más rápidas a la réplica principal actual. Consulte [Conexión con MultiSubnetFailover](/dotnet/framework/data/adonet/sql/sqlclient-support-for-high-availability-disaster-recovery#connecting-with-multisubnetfailover).
> - La configuración `MultiSubnetFailover=True` no es necesaria con .NET Framework 4.6.1 o versiones posteriores.

## <a name="next-steps"></a>Pasos siguientes


Para obtener más información, consulte:

- [Clúster de conmutación por error de Windows Server con SQL Server en máquinas virtuales de Azure](hadr-windows-server-failover-cluster-overview.md)
- [Grupos de disponibilidad Always On para SQL Server en Azure Virtual Machines](availability-group-overview.md)
- [Introducción a los grupos de disponibilidad Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Configuración de alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](hadr-cluster-best-practices.md)