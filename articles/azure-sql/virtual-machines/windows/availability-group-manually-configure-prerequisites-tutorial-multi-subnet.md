---
title: 'Tutorial: Requisitos previos del grupo de disponibilidad en varias subredes'
description: 'En este tutorial, se muestra cómo configurar los requisitos previos para crear un grupo de disponibilidad AlwaysOn en varias subredes para SQL Server en Azure Virtual Machines. '
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
ms.openlocfilehash: 4d1ec223a745f49518a7936bc85d40f470739c80
ms.sourcegitcommit: 512e6048e9c5a8c9648be6cffe1f3482d6895f24
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132159408"
---
# <a name="tutorial-prerequisites-for-availability-groups-in-multiple-subnets-sql-server-on-azure-vms"></a>Tutorial: Requisitos previos para grupos de disponibilidad en varias subredes (SQL Server en máquinas virtuales de Azure) 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

> [!TIP]
> Elimine la necesidad de una instancia de Azure Load Balancer o un nombre de red distribuida (DNN) para el grupo de disponibilidad Always On mediante la creación de las máquinas virtuales de SQL Server en varias subredes dentro de la misma red virtual de Azure.

En este tutorial, se muestra cómo completar los requisitos previos para crear un [grupo de disponibilidad AlwaysOn para SQL Server en Azure Virtual Machines en varias subredes](availability-group-manually-configure-tutorial-multi-subnet.md). Al final de este tutorial, tendrá un controlador de dominio en dos máquinas virtuales de Azure, dos máquinas virtuales de SQL Server en varias subredes y una cuenta de almacenamiento en un único grupo de recursos. 

**Estimación de tiempo**: este tutorial crea varios recursos en Azure y puede tardar hasta 30 minutos en completarse. 

En el diagrama siguiente, se muestran los recursos que se implementan en este tutorial: 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/multi-subnet-availability-group-diagram.png" alt-text="En el diagrama siguiente, se muestran los recursos que se implementan en este tutorial.":::


## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesitará lo siguiente:

- Suscripción a Azure. Puede [abrir una cuenta gratuita de Azure](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic) o [activar las ventajas que disfrutan los suscriptores de Visual Studio](/visualstudio/subscriptions/subscriber-benefits).
- Conocimientos básicos y familiaridad con los [grupos de disponibilidad Always On en SQL Server](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). 


## <a name="create-resource-group"></a>Creación de un grupo de recursos

Para crear el grupo de recursos en Azure Portal, siga estos pasos: 

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
1. Seleccione **+ Crear un recurso** para crear un nuevo recurso en el portal.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-portal-plus.png" alt-text="Seleccione +Crear un recurso para crear un nuevo recurso en el portal.":::

1. Busque **grupo de recursos** en el cuadro de búsqueda de **Marketplace** y elija el icono **Grupo de recursos** de Microsoft. Seleccione **Crear** en la página **Grupo de recursos**. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-resource-group-search.png" alt-text="Busque el grupo de recursos en Marketplace y, a continuación, elija crear el grupo de recursos.":::

1. En la página **Crear un grupo de recursos**, rellene los valores para crear el grupo de recursos:
   1. Elija la suscripción de Azure adecuada en la lista desplegable. 
   1. Proporcione un nombre para el grupo de recursos, como **SQL-HA-RG**.
   1. Elija una región en la lista desplegable, como **Oeste de EE. UU. 2**. Asegúrese de implementar también todos los recursos posteriores en esta ubicación. 
   1. Seleccione **Revisar y crear** para revisar los parámetros del recurso y después seleccione **Crear** para crear el grupo de recursos.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/01-resource-group-create-complete.png" alt-text="Rellene los valores para crear el grupo de recursos en Azure Portal.":::


## <a name="create-network-and-subnets"></a>Creación de redes y subredes

A continuación, cree la red virtual y tres subredes. Para más información, consulte [¿Qué es Azure Virtual Network?](../../../virtual-network/virtual-networks-overview.md) 

Para crear la red virtual en Azure Portal, siga estos pasos:

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione **+ Crear**.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="Creación de un nuevo recurso en el grupo de recursos":::

1. Busque **red virtual** en el cuadro de búsqueda de **Marketplace** y elija el icono **Red virtual** de Microsoft. En la página **Red virtual**, seleccione **Crear**.  
1. En la pestaña **Básico** de la página **Crear una red virtual**, escriba la siguiente información: 
   1. En **Detalles del proyecto**, elija la **suscripción** de Azure adecuada y el **grupo de recursos** que creó anteriormente, por ejemplo, **SQL-HA-RG**. 
   1. En **Detalles de la instancia**, proporcione un nombre para la red virtual, como **SQLHAVNET**, y elija la misma región que el grupo de recursos en la lista desplegable.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/03-create-vnet-basics.png" alt-text="Elija el grupo de recursos que creó anteriormente y, a continuación, proporcione un nombre para la red virtual, como SQLHAVNET.":::

1. En la pestaña **Direcciones IP**, seleccione la subred **predeterminada** para abrir la página **Editar subred**. Cambie el nombre a **DC-subnet** para usarla para la subred del controlador de dominio. Seleccione **Guardar**.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/04-create-vnet-ip-address-rename-default-subnet.png" alt-text="En la pestaña Direcciones IP, seleccione la subred predeterminada para abrir la página Editar subred. Cambie el nombre a DC-subnet para usarla para la subred del controlador de dominio. Seleccione Guardar.":::

1. Seleccione **+ Agregar subred** para agregar una subred adicional para la primera máquina virtual de SQL Server y rellene los valores siguientes: 
   1. Proporcione un valor para **Nombre de subred**, como **SQL-subnet-1**. 
   1. Proporcione un intervalo de direcciones de subred único dentro del espacio de direcciones de la red virtual. Por ejemplo, puede iterar el tercer octeto del intervalo de direcciones de DC-subnet en 1. 
      - Por ejemplo, si el intervalo de **DC-subnet** es *10.38.0.0/24*, escriba el intervalo de direcciones IP `10.38.1.0/24` para **SQL-subnet-1**. 
      - Del mismo modo, si el intervalo IP de **DC-subnet** es *10.5.0.0/24*, escriba `10.5.1.0/24` para la nueva subred. 
   1. Seleccione **Agregar** para agregar la nueva subred. 

     :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/05-create-vnet-ip-address-add-sql-subnet-1.png" alt-text="Asigne un nombre a la primera subred, por ejemplo, sql-subnet-1, y, a continuación, itere el tercer octeto en 1, de modo que si la dirección IP de DC-subnet es 10.5.0.0, la nueva subred debe ser 10.5.1.0.":::

1. Repita el paso anterior para agregar un intervalo de subred único adicional para la segunda máquina virtual de SQL Server con un nombre como **SQL-subnet-2**. Puede iterar el tercer octeto en uno de nuevo. 
   - Por ejemplo, si el intervalo IP de **DC-subnet** es *10.38.0.0/24* y **SQL-subnet-1** es *10.38.1.0/24*,escriba `10.38.2.0/24` para la nueva subred.
   - Del mismo modo, si el intervalo IP de **DC-subnet** es *10.5.0.0/24* y **SQL-subnet-1** es *10.5.1.0/24*, escriba el intervalo de direcciones IP `10.5.2.0/24` para **SQL-subnet-2**. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/06-create-vnet-ip-address-add-sql-subnet-2.png" alt-text="Asigne un nombre a la segunda subred, por ejemplo, sql-subnet-2, y, a continuación, itere el tercer octeto en 2, de modo que si la dirección IP de DC-subnet es 10.38.0.0/24, la nueva subred debe ser 10.38.2.0/24.":::

1. Después de agregar la segunda subred, revise los nombres e intervalos de subred (los intervalos de direcciones IP pueden diferir de la imagen). Si todo parece correcto, seleccione **Revisar y crear** y, a continuación, **Crear** para crear la nueva red virtual. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/07-create-vnet-ip-address.png" alt-text="Después de agregar la segunda subred, revise los nombres e intervalos de subred, como en el ejemplo de la imagen (aunque las direcciones IP pueden ser diferentes). Si todo parece correcto, seleccione Revisar y crear y, a continuación, Crear para crear la nueva red virtual.":::

   Azure le devuelve al panel del portal y le informa cuando se cree la red.


## <a name="create-domain-controllers"></a>Creación de controladores de dominio

Una vez que la red y las subredes estén listas, cree una máquina virtual (o dos opcionalmente, para la alta disponibilidad) y configúrela como controlador de dominio. 

### <a name="create-dc-virtual-machines"></a>Creación de máquinas virtuales del controlador de dominio

Para crear las máquinas virtuales del controlador de dominio (DC) en Azure Portal, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione **+ Crear**.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/02-create-resource-rg.png" alt-text="Creación de un nuevo recurso en el grupo de recursos":::

1. Busque **Windows Server** en el cuadro de búsqueda de **Marketplace**. 
1. En el icono de **Windows Server** de Microsoft, seleccione la lista desplegable **Crear** y elija la imagen **Windows Server 2016 Datacenter**. 
1. Rellene los valores de la página **Crear una máquina virtual** para crear la máquina virtual del controlador de dominio, por ejemplo, **DC-VM-1**. Opcionalmente, cree una máquina virtual adicional, como **DC-VM-2**, para proporcionar alta disponibilidad para Active Directory Domain Services. Use los valores de la siguiente tabla para crear las máquinas virtuales:

    | **Campo** | Value |
    | --- | --- |
    | **Suscripción** |*Su suscripción* |
    | **Grupos de recursos** |SQL-HA-RG |
    | **Nombre de la máquina virtual** |Primer controlador de dominio: *DC-VM-1*.</br>Segundo controlador de dominio: *DC-VM-2*. |
    | **Región** |*Ubicación en la que implementó el grupo de recursos y la red virtual.* |
    | **Opciones de disponibilidad** |Zona de disponibilidad </br> *En el caso de las regiones de Azure que no admiten zonas de disponibilidad, use conjuntos de disponibilidad en su lugar. Cree un nuevo conjunto de disponibilidad y coloque todas las máquinas virtuales creadas en este tutorial dentro del conjunto de disponibilidad.* |
    | **Zona de disponibilidad** |Especifique 1 para DC-VM-1. </br> Especifique 2 para DC-VM-2. |
    | **Tamaño** |D2s_v3 (2 vCPU, 8 GB de RAM) |
    | **Nombre de usuario** |DomainAdmin |
    | **Contraseña** |Contoso!0000 |
    | **Puertos de entrada públicos** | *Permitir los puertos seleccionados* |
    | **Selección de puertos de entrada** | *RDP (3389)* |
    | **Tipo de disco del sistema operativo** | SSD prémium (almacenamiento con redundancia local) |
    | **Red virtual** |SQLHAVNET |
    | **Subred** |DC-subnet |
    | **Dirección IP pública** |*El mismo nombre que la máquina virtual, por ejemplo, DC-VM-1 o DC-VM-2* |
    | **Grupo de seguridad de red de NIC**| Básico |
    | **Puertos de entrada públicos**| Permitir los puertos seleccionados |
    | **Selección de puertos de entrada**| RDP (3389) |
    | **Diagnósticos de arranque** |Habilitar con la cuenta de almacenamiento administrada (recomendado). |

    Azure le notifica cuando se crean las máquinas virtuales y están listas para usarse. 


### <a name="configure-the-domain-controller"></a>Configuración del controlador de dominio

Una vez que las máquinas virtuales del controlador de dominio estén listas, configure el controlador de dominio para corp.contoso.com.

Para configurar **DC-VM-1** como controlador de dominio, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione la máquina **DC-VM-1**. 
1. En la página **DC-VM-1**, seleccione **Conectar** para descargar un archivo RDP para el acceso a Escritorio remoto y, a continuación, abra el archivo.  

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/08-dc-vm-1-rdp-connect.png" alt-text="Conexión a una máquina virtual":::

1. Conéctese a la sesión RDP con la cuenta de administrador configurada (**BUILTIN\DomainAdmin**) y la contraseña (**Contoso!0000**).
1. Abra el panel **Administrador del servidor** (que se puede abrir de manera predeterminada) y elija **Agregar roles y características**. 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="Administrador del servidor: Agregar roles":::

1. Seleccione **Siguiente** hasta llegar a la sección **Roles de servidor**.
1. Seleccione los roles **Active Directory Domain Services** y **Servidor DNS**. Cuando se le pida, agregue características adicionales que son necesarias para estos roles.

   > [!NOTE]
   > Windows le advierte de que no hay ninguna dirección IP estática. Si está probando la configuración, seleccione **Continuar**. En los escenarios de producción, establezca la dirección IP en estática en Azure Portal o [use PowerShell para establecer la dirección IP estática de la máquina del controlador de dominio](/azure/virtual-network/virtual-networks-static-private-ip-arm-ps).
   >

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/10-add-roles.png" alt-text="Cuadro de diálogo Agregar roles":::

1. Seleccione **Siguiente** hasta llegar a la sección **Confirmación**. Active la casilla **Reiniciar automáticamente el servidor de destino en caso necesario** .
1. Seleccione **Instalar**.
1. Cuando terminen de instalarse las características, vuelva al panel **Administrador del servidor** .
1. Seleccione la nueva opción **AD DS** en el panel izquierdo.
1. Seleccione el vínculo **Más** en la barra de advertencia amarilla.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/11-ad-ds-more.png" alt-text="Cuadro de diálogo AD DS en la máquina virtual del servidor DNS":::
    
1. En la columna **Acción** del cuadro de diálogo **Todos los detalles de la tarea del servidor**, seleccione **Promover este servidor a controlador de dominio**.
1. En el **Asistente de configuración de Active Directory Domain Services**, use los siguientes valores:

    | **Page** | Configuración |
    | --- | --- |
    | **Configuración de la implementación** |**Incorporación de nuevos bosques**<br/> **Nombre del dominio raíz** = corp.contoso.com |
    | **Opciones del controlador de dominio** |**Contraseña de DSRM** = Contoso!0000<br/>**Confirmar contraseña** = Contoso!0000 |

1. Seleccione **Siguiente** para avanzar por las otras páginas del asistente. En la página **Comprobación de requisitos previos**, compruebe que ve el mensaje siguiente: **Todas las comprobaciones de requisitos previos se realizaron correctamente**. Puede revisar todos los mensajes de advertencia aplicables, pero es posible continuar con la instalación.
1. Seleccione **Instalar**. La máquina virtual **DC-VM-1** se reinicia automáticamente.

### <a name="identify-dns-ip-address"></a>Identificación de la dirección IP de DNS

Utilice el controlador de dominio principal para DNS. Para ello, identifique la dirección IP privada de la máquina virtual que se usa para el controlador de dominio principal. 

Para identificar la dirección IP privada de la máquina virtual en Azure Portal, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione el controlador de dominio principal, **DC-VM-1**. 
1. En la página **DC-VM-1**, elija **Redes** en el panel **Configuración**. 
1. Anote la dirección **IP privada de la NIC**. Use esta dirección IP como servidor DNS para las demás máquinas virtuales.  En la imagen de ejemplo, la dirección IP privada es **10.38.0.4**. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-dc-vm-1-private-ip.png" alt-text="En la página DC-VM-1, elija Redes en el panel Configuración y, a continuación, anote la dirección IP privada de la NIC. Use esta dirección IP como servidor DNS.":::

### <a name="configure-virtual-network-dns"></a>Configuración de DNS de la red virtual

Después de crear el primer controlador de dominio y habilitar DNS, configure la red virtual para usar esta máquina virtual para DNS.

Para configurar la red virtual para DNS, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione la red virtual, por ejemplo, **SQLHAVNET**. 
1. Seleccione **Servidores DNS** en el panel **Configuración** y, a continuación, seleccione **Personalizado**. 
1. Escriba la dirección IP privada que identificó anteriormente en el campo **Dirección IP**, por ejemplo, `10.38.0.4`. 
1. Seleccione **Guardar**. 

:::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/12-identify-dns-ip-address.png" alt-text="Seleccione Servidores DNS en el panel Configuración y, a continuación, seleccione Personalizado. Escriba la dirección IP privada que identificó anteriormente en el campo Dirección IP, por ejemplo, 10.38.0.4.":::


### <a name="configure-second-domain-controller"></a>Configuración del segundo controlador de dominio

Después de reiniciar el controlador de dominio principal, opcionalmente puede configurar el segundo controlador de dominio para la alta disponibilidad. Si no desea configurar un segundo controlador de dominio, omita este paso. Sin embargo, tenga en cuenta que se recomienda un segundo controlador de dominio en entornos de producción. 

Establezca la dirección del servidor DNS preferido, únase al dominio y, a continuación, configure el controlador de dominio secundario. 

#### <a name="set-preferred-dns-server-address"></a>Establecimiento de la dirección del servidor DNS preferido

En primer lugar, cambie la dirección del servidor DNS preferido. Para ello, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione la máquina del segundo controlador de dominio, por ejemplo, **DC-VM-2**. 
1. En la página **DC-VM-2**, seleccione **Conectar** para descargar el archivo RDP para el acceso a Escritorio remoto y, a continuación, abra el archivo. 
1. Conéctese a la sesión RDP con la cuenta de administrador configurada (**BUILTIN\DomainAdmin**) y la contraseña (**Contoso!0000**).
1. Abra el **Centro de redes y recursos compartidos** y seleccione la interfaz de red. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/13-network-interface.png" alt-text="Interfaz de red":::

1. Abra la página **Propiedades**. 
1. Seleccione **Protocolo de Internet versión 4 (TCP/IPv4)** y, luego, **Propiedades**.
1. Seleccione **Usar las siguientes direcciones de servidor DNS** y especifique la dirección IP privada del controlador de dominio principal en **Servidor DNS preferido**, por ejemplo, `10.38.0.4`. 
1. Seleccione **Aceptar** y **Cerrar** para confirmar los cambios. Si pierde la conexión a Escritorio remoto después de cambiar la dirección IP de DNS, vaya a la máquina virtual en [Azure Portal](https://portal.azure.com) y reinicie la máquina virtual. 

### <a name="join-the-domain"></a>Unir al dominio 

A continuación, únase al dominio **corp.contoso.com**. Para ello, siga estos pasos: 

1. Conéctese de forma remota a la máquina virtual con la cuenta **BUILTIN\DomainAdmin**.
1. Abra **Administrador del servidor** y seleccione **Servidor local**.
1. Seleccione **WORKGROUP**.
1. En la sección **Nombre de equipo**, seleccione **Cambiar**.
1. Seleccione la casilla **Dominio** y escriba **corp.contoso.com** en el cuadro de texto. Seleccione **Aceptar**.
1. En el cuadro de diálogo emergente **Seguridad de Windows**, especifique las credenciales de la cuenta de administrador del dominio predeterminado (**CORP\DomainAdmin**) y la contraseña (**Contoso!0000**).
1. Cuando vea el mensaje "Bienvenida al dominio corp.contoso.com", seleccione **Aceptar**.
1. Seleccione **Cerrar** y luego **Reiniciar ahora** en el cuadro de diálogo emergente.


#### <a name="configure-domain-controller"></a>Configuración del controlador de dominio 

Una vez que el servidor se haya unido al dominio, puede configurarlo como el segundo controlador de dominio. Para ello, siga estos pasos: 

1. Si aún no está conectado, abra una sesión RDP en el controlador de dominio secundario y abra el panel **Administrador del servidor** (que puede estar abierto de manera predeterminada).
1. Seleccione el vínculo **Agregar roles y características** en el panel.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/09-add-features.png" alt-text="Administrador del servidor: Agregar roles":::

1. Seleccione **Siguiente** hasta llegar a la sección **Roles de servidor**.
1. Seleccione los roles **Active Directory Domain Services** y **Servidor DNS**. Cuando se le pida, agregue características adicionales que son necesarias para estos roles.
1. Cuando terminen de instalarse las características, vuelva al panel **Administrador del servidor** .
1. Seleccione la nueva opción **AD DS** en el panel izquierdo.
1. Seleccione el vínculo **Más** en la barra de advertencia amarilla.
1. En la columna **Acción** del cuadro de diálogo **Todos los detalles de la tarea del servidor**, seleccione **Promover este servidor a controlador de dominio**.
1. En **Configuración de implementación**, seleccione **Agregar un controlador de dominio a un dominio existente**.
1. Haga clic en **Seleccionar**.
1. Conéctese con la cuenta de administrador (**CORP.CONTOSO.COM\domainadmin**) y la contraseña (**Contoso!0000**).
1. En **Seleccionar un dominio del bosque**, elija el dominio y, después, seleccione **Aceptar**.
1. En **Opciones del controlador de dominio**, utilice los valores predeterminados y establezca una contraseña de DSRM.

    >[!NOTE]
    >La página **Opciones de DNS** puede avisarle de que no se puede crear una delegación de este servidor DNS. Puede pasar por alto esta advertencia en entornos de no producción.
    >

1. Seleccione **Siguiente** hasta que se muestre la casilla **Requisitos previos**. A continuación, seleccione **Instalar**.

Después de que el servidor complete los cambios de configuración, reinícielo.

### <a name="add-second-dc-ip-address-to-dns"></a>Adición de la segunda dirección IP del controlador de dominio al sistema DNS

Después de configurar el segundo controlador de dominio, siga los mismos pasos que antes para [identificar la dirección IP privada de la máquina virtual](#identify-dns-ip-address) y [agregue la dirección IP privada como un servidor DNS personalizado secundario](#configure-virtual-network-dns) en la red virtual del grupo de recursos. Agregar el servidor DNS secundario en Azure Portal permite la redundancia del servicio DNS. 



## <a name="configure-domain-accounts"></a>Configuración de cuentas de dominio

Una vez configurados los controladores de dominio y configurados los servidores DNS en Azure Portal, cree cuentas de dominio para el usuario que va a instalar SQL Server y para la cuenta de servicio de SQL Server. 

Configure tres cuentas en total, una cuenta de instalación para ambas máquinas virtuales de SQL Server y, a continuación, una cuenta de servicio para cada máquina virtual de SQL Server. Por ejemplo, use los valores de la tabla siguiente para las cuentas:

|Los puntos  | máquina virtual  |Nombre de dominio completo  |Descripción   |
|---------|---------|---------|---------|
|Instalar    |Ambos| Corp\Install        |Inicie sesión en cualquiera de las máquinas virtuales con esta cuenta para configurar el clúster y el grupo de disponibilidad. |
|SQLSvc1     |SQL-VM-1 |Corp\SQLSvc1 | Use esta cuenta para el servicio SQL Server de la primera máquina virtual de SQL Server. |
|SQLSvc2     |SQL-VM2 |Corp\SQLSvc2| Use esta cuenta para el servicio SQL Server de la segunda máquina virtual de SQL Server.|

Siga estos pasos para crear cada cuenta: 

1. Conéctese a la máquina del controlador de dominio principal, por ejemplo, **DC-VM-1**. .
1. En **Administrador del servidor**, seleccione **Herramientas** y, después, **Centro de administración de Active Directory**.
1. Seleccione **corp (local)** en el panel izquierdo.
1. En el panel **Tareas** de la derecha, seleccione **Nuevo** y, después, **Usuario**. 
1. Escriba la nueva cuenta de usuario y establezca una contraseña compleja. Para entornos de no producción, establezca que la cuenta de usuario no expire nunca.

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/14-ad-dc-new-user.png" alt-text="Centro de administración de Active Directory":::

1. Seleccione **Aceptar** para crear el usuario.
1. Repita estos pasos para crear las tres cuentas. 

### <a name="grant-installation-account-permissions"></a>Concesión de permisos de la cuenta de instalación

Una vez creadas las cuentas, conceda los permisos de dominio necesarios a la cuenta de instalación para que la cuenta pueda crear objetos en AD. 

Para conceder los permisos a la cuenta de instalación, siga estos pasos: 

1. Abra el **Centro de administración de Active Directory** desde el **Administrador del servidor**, si aún no está abierto. 
1. Seleccione **corp (local)** en el panel izquierdo. 
1. En el panel **Tareas** de la derecha, compruebe que aparezca **corp (local)** en la lista desplegable y, a continuación, seleccione **Propiedades** debajo.

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/15-ad-dc-properties.png" alt-text="Propiedades de usuario CORP":::

1. Seleccione **Extensiones** y, luego, el botón **Avanzadas** en la pestaña **Seguridad**.
1. En el cuadro de diálogo **Configuración de seguridad avanzada de corp**, seleccione **Agregar**.
1. Elija **Seleccionar una entidad de seguridad**, busque **CORP\Install** y, a continuación, seleccione **Aceptar**.
1. Seleccione las casillas situadas junto a **Leer todas las propiedades** y **Crear objetos de equipo**. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/16-add-permissions.png" alt-text="Permisos de usuario Corp":::

1. Seleccione **Aceptar** y, después, otra vez **Aceptar**. Cierre la ventana de propiedades **corp**.

Ahora que ha terminado de configurar Active Directory y los objetos de usuario, está listo para crear las máquinas virtuales de SQL Server. 

## <a name="create-sql-server-vms"></a>Creación de las máquinas virtuales con SQL Server

Una vez configuradas las cuentas de usuario, DNS y AD, está listo para crear las máquinas virtuales de SQL Server. Para simplificar, use el las imágenes de máquina virtual de SQL Server de Marketplace. 

Sin embargo, antes de crear las máquinas virtuales de SQL Server, tenga en cuenta las siguientes decisiones de diseño: 

**Disponibilidad: Availability Zones**   

Para obtener el máximo nivel de redundancia, resistencia y disponibilidad, implemente las máquinas virtuales dentro de zonas de disponibilidad independientes. Las zonas de disponibilidad son ubicaciones físicas exclusivas dentro de una región de Azure. Cada zona de disponibilidad consta de uno o varios centros de datos con alimentación, refrigeración y redes independientes. En el caso de las regiones de Azure que aún no admiten Availability Zones, use conjuntos de disponibilidad en su lugar. Coloque todas las máquinas virtuales en el mismo conjunto de disponibilidad. 

**Almacenamiento: Azure Managed Disks**   

Para el almacenamiento de máquina virtual, use Azure Managed Disks. Microsoft recomienda Managed Disks para las máquinas virtuales de SQL Server, ya que controlan el almacenamiento en segundo plano. Para más información, consulte [Introducción a Azure Managed Disks](../../../virtual-machines/managed-disks-overview.md). 

**Red: direcciones IP privadas en producción**   

Para las máquinas virtuales, este tutorial usa direcciones IP públicas. Una dirección IP pública permite conectarse de forma remota directamente a la máquina virtual a través de Internet y facilita los pasos de configuración. En entornos de producción, Microsoft recomienda usar únicamente direcciones IP privadas con el fin de reducir el grado de vulnerabilidad del recurso de máquina virtual de la instancia de SQL Server.

**Red: NIC única por servidor**   

Use una sola NIC por servidor (nodo de clúster). Las redes de Azure tienen redundancia física, lo que hace que las NIC adicionales no sean necesarias en un clúster de conmutación por error implementado en una máquina virtual de Azure. El informe de validación de clúster le avisará de que solo se puede tener acceso a los nodos en una sola red. Puede omitir esta advertencia cuando el clúster de conmutación por error está en máquinas virtuales de Azure. 

Para crear las máquinas virtuales, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com) y seleccione **+ Crear**.
1. Busque **Azure SQL** y seleccione el icono de **Azure SQL** de Microsoft. 
1. En la página **Azure SQL**, seleccione **Crear** y, a continuación, elija la imagen **SQL Server 2016 SP2 Enterprise en Windows Server 2016** en la lista desplegable. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/18-select-sql-vm-image.png" alt-text="En la página Azure SQL del portal, seleccione Crear y, a continuación, elija la imagen SQL Server 2016 SP2 Enterprise en Windows Server 2016 en la lista desplegable.":::

Use la tabla siguiente para rellenar los valores de la página **Crear una máquina virtual** para crear ambas máquinas virtuales de SQL Server, por ejemplo, **SQL-VM-1** y **SQL-VM-2** (las direcciones IP pueden diferir de los ejemplos de la tabla): 

| Configuración | SQL-VM-1 | SQL-VM-2 |
| --- | --- | --- |
| Imagen de la Galería |**SQL Server 2016 SP2 Enterprise en Windows Server 2016** |**SQL Server 2016 SP2 Enterprise en Windows Server 2016** |
| **Aspectos básicos de la máquina virtual** |**Nombre** = SQL-VM-1<br/>**Nombre de usuario** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Suscripción** = su suscripción<br/>**Grupo de recursos** = SQL-HA-GR<br/>**Ubicación** = Su ubicación de Azure |**Nombre** = SQL-VM-2<br/>**Nombre de usuario** = DomainAdmin<br/>**Password** = Contoso!0000<br/>**Suscripción** = su suscripción<br/>**Grupo de recursos** = SQL-HA-GR<br/>**Ubicación** = Su ubicación de Azure |
| **Tamaño de VM** |**TAMAÑO** = E2ds_v4 (2 vCPUs, 16 GB de RAM)</br> |**TAMAÑO** = E2ds_v4 (2 vCPUs, 16 GB de RAM) |
| **Configuración de la máquina virtual** |**Opciones de disponibilidad** = Zona de disponibilidad<br/>**Zona de disponibilidad** = 1<br/>**Puertos de entrada públicos** = Permitir puertos seleccionados<br/>**Selección de puertos de entrada** = RDP (3389)<br/>**Tipo de disco del sistema operativo** = SSD prémium (almacenamiento con redundancia local)<br/>**Red virtual** = SQLHAVNET<br/>**Subred** = SQL-subnet-1(10.38.1.0/24)<br/>**Dirección IP pública** = generada automáticamente.<br/>**Grupo de seguridad de red de la NIC** = Básico<br/>**Puertos de entrada públicos** = Permitir puertos seleccionados <br/> **Selección de puertos de entrada** = RDP (3389)<br/>**Diagnósticos de arranque** = Habilitar con la cuenta de almacenamiento administrada (recomendado)<br/>|**Opciones de disponibilidad** = Zona de disponibilidad<br/>**Zona de disponibilidad** = 2<br/>**Puertos de entrada públicos** = Permitir puertos seleccionados<br/>**Selección de puertos de entrada** = RDP (3389)<br/>**Tipo de disco del sistema operativo** = SSD prémium (almacenamiento con redundancia local)<br/>**Red virtual** = SQLHAVNET<br/>**Subred** = SQL-subnet-2(10.38.2.0/24)<br/>**Dirección IP pública** = generada automáticamente.<br/>**Grupo de seguridad de red de la NIC** = Básico<br/>**Puertos de entrada públicos** = Permitir puertos seleccionados <br/> **Selección de puertos de entrada** = RDP (3389)<br/>**Diagnósticos de arranque** = Habilitar con la cuenta de almacenamiento administrada (recomendado)<br/> |
| **Configuración de SQL Server** |**Conectividad de SQL** = privado (dentro de la red virtual)<br/>**Puerto** = 1433<br/>**Autenticación SQL** = deshabilitar<br/>**Integración de Azure Key Vault** = Deshabilitada <br/>**Optimización del almacenamiento** = Procesamiento transaccional<br/>**Datos de SQL** = 1024 GiB, 5000 IOPS, 200 MB/s<br/>**Registro de SQL** = 1024 GiB, 5000 IOPS, 200 MB/s<br/>**TempDb de SQL** = Usar unidad SSD local<br/>**Aplicación de revisiones automatizada** = el domingo a las 2:00<br/>**Copia de seguridad automatizada** = Deshabilitar |**Conectividad de SQL** = privado (dentro de la red virtual)<br/>**Puerto** = 1433<br/>**Autenticación SQL** = deshabilitar<br/>**Integración de Azure Key Vault** = Deshabilitada <br/>**Optimización del almacenamiento** = Procesamiento transaccional<br/>**Datos de SQL** = 1024 GiB, 5000 IOPS, 200 MB/s<br/>**Registro de SQL** = 1024 GiB, 5000 IOPS, 200 MB/s<br/>**TempDb de SQL** = Usar unidad SSD local<br/>**Aplicación de revisiones automatizada** = el domingo a las 2:00<br/>**Copia de seguridad automatizada** = Deshabilitar |

<br/>

> [!NOTE]
> Estos tamaños de máquina sugeridos solo están pensados para probar los grupos de disponibilidad en Azure Virtual Machines. Para cargas de trabajo de producción optimizadas, consulte las recomendaciones de [Lista de comprobación: Procedimientos recomendados de SQL Server en máquinas virtuales de Azure](./performance-guidelines-best-practices-checklist.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>

## <a name="configure-sql-server-vms"></a>Configuración de las máquinas virtuales de SQL Server

Una vez completada la creación de la máquina virtual, configure las máquinas virtuales de SQL Server mediante la adición de una dirección IP secundaria a cada máquina virtual y su unión al dominio. 


### <a name="add-secondary-ips-to-sql-server-vms"></a>Adición de direcciones IP secundarias a las máquinas virtuales de SQL Server

En el entorno de varias subredes, asigne direcciones IP secundarias a cada máquina virtual de SQL Server para usarlas para el cliente de escucha del grupo de disponibilidad y, para Windows Server 2016 y versiones anteriores, asigne también direcciones IP secundarias a cada máquina virtual de SQL Server para la dirección IP del clúster. Esto anula la necesidad de una instancia de Azure Load Balancer, como es el requisito en un entorno de una sola subred.  

En Windows Server 2016 y versiones anteriores, debe asignar una dirección IP secundaria adicional a cada máquina virtual de SQL Server para usarla para la dirección IP del clúster de Windows, ya que el clúster usa el **nombre de red del clúster** en lugar del nombre de red distribuido (DNN) predeterminado introducido en Windows Server 2019. Con un DNN, el objeto de nombre de clúster (CNO) se registra automáticamente con las direcciones IP de todos los nodos del clúster, lo que elimina la necesidad de una dirección IP dedicada del clúster de Windows.

Si está en Windows Server 2016 y versiones anteriores, siga los pasos descritos en esta sección para asignar una dirección IP secundaria a cada máquina virtual de SQL Server *tanto para* el cliente de escucha del grupo de disponibilidad *como para* el clúster. 

Si está en Windows Server 2019 o posterior, asigne solo una dirección IP secundaria para el cliente de escucha del grupo de disponibilidad y omita los pasos para asignar una dirección IP del clúster de Windows, a menos que planee configurar el clúster con un nombre de red virtual (VNN), en cuyo caso asigne ambas direcciones IP a cada máquina virtual de SQL Server como lo haría para Windows Server 2016. 

Para asignar direcciones IP secundarias adicionales a las máquinas virtuales, siga estos pasos: 

1. Vaya al grupo de recursos en [Azure Portal](https://portal.azure.com/) y seleccione la primera máquina virtual de SQL Server, por ejemplo, **DC-VM-1**. 
1. Seleccione **Redes** en el panel **Configuración** y, a continuación, seleccione la **interfaz de red**: 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/19-sql-vm-network-interface.png" alt-text="Seleccione Redes en el panel Configuración y, a continuación, seleccione la interfaz de red.":::

1. En la página **Interfaz de red**, seleccione **Configuraciones IP** en el panel **Configuración** y, a continuación, elija **+ Agregar** para agregar una dirección IP adicional: 

    :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/20-ip-configurations-add.png" alt-text="Configuraciones IP":::

1. En la página **Agregar configuración IP**, realice lo siguiente: 
   1. Especifique el valor de **Nombre** como la dirección IP del clúster de Windows, por ejemplo, **windows-cluster-ip** para Windows 2016 y versiones anteriores. Omita este paso si está en Windows Server 2019 o posterior.  
   1. Establezca **Asignación** en **Estática**.
   1. Escriba una **dirección IP** sin usar de la misma subred (**SQL-subnet-1**) que la máquina virtual de SQL Server (**SQL-VM-1**), por ejemplo, `10.38.1.10`. 
   1. Deje el campo **Dirección IP pública** en el valor predeterminado de **Desasociar**. 
   1. Seleccione **Aceptar** para terminar de agregar la configuración IP. 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/21-add-ip-windows-cluster.png" alt-text="Para agregar la dirección IP del clúster, escriba una dirección IP usada en la subred de la primera máquina virtual de SQL Server.":::

1. Seleccione **+ Agregar** de nuevo para configurar una dirección IP adicional para el cliente de escucha del grupo de disponibilidad (con un nombre como **availability-group-listener**),especificando de nuevo una dirección IP sin usar de **SQL-subnet-1**, por ejemplo, `10.38.1.11`: 

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/22-add-ip-ag-listener.png" alt-text="Seleccione + Agregar de nuevo para configurar una dirección IP adicional para el cliente de escucha del grupo de disponibilidad (con un nombre como availability-group-listener),especificando de nuevo una dirección IP sin usar de SQL-subnet-1, por ejemplo, 10.31.1.11":::

1. Repita estos pasos de nuevo para la segunda máquina virtual de SQL Server, por ejemplo, **SQL-VM-2**. Asigne dos direcciones IP secundarias no utilizadas de **SQL-subnet-2**. Use los valores de la tabla siguiente para agregar la configuración IP: 

   
    | **Campo** | Entrada | Entrada | 
    | --- | --- | --- |
    | **Nombre** |windows-cluster-ip | availability-group-listener |
    | **Asignación** | estática | estática |
    | **Dirección IP** | 10.38.2.10 | 10.38.2.11 | 
    |  |  |  |

Ahora está listo para unirse a **corp.contoso.com**. 

### <a name="join-the-servers-to-the-domain"></a>Unión de los servidores al dominio

Una vez que se hayan asignado las dos direcciones IP secundarias a ambas máquinas virtuales de SQL Server, una cada máquina virtual de SQL Server al dominio **corp.contoso.com**. 

Para unirse al dominio corp.contoso.com, siga los mismos pasos para la máquina virtual de SQL Server que siguió al [unirse al dominio](#join-the-domain) con el controlador de dominio secundario. 

Espere a que se reinicie cada máquina virtual de SQL Server y, a continuación, puede agregar las cuentas. 


## <a name="add-accounts"></a>Adición de cuentas

Agregue la cuenta de instalación como administrador en cada VM, conceda permiso a la cuenta de instalación y a las cuentas locales en SQL Server y actualice la cuenta de servicio de SQL Server. 

### <a name="add-install-account"></a>Adición de la cuenta de instalación

Una vez que las máquinas virtuales de SQL Server se hayan unido al dominio, agregue **CORP\Install** como miembro del grupo de administradores locales.

>[!TIP]
> Asegúrese de iniciar sesión con la cuenta de administrador del *dominio*. En los pasos anteriores, hemos utilizado la cuenta de administrador **BUILTIN**. Ahora que el servidor forma parte del dominio, use la cuenta de dominio. En la sesión RDP, especifique *DOMINIO*\\*nombre de usuario*, por ejemplo, **CORP\DomainAdmin**.

Para agregar la cuenta como administrador, siga estos pasos: 

1. Espere hasta que se reinicie la máquina virtual y, después, inicie el archivo RDP de nuevo desde la primera máquina virtual de SQL Server para iniciar sesión en **SQL-VM-1** con la cuenta **CORP\DomainAdmin**.
1. En **Administrador del servidor**, seleccione **Herramientas** y, después, **Administración de equipos**.
1. En la ventana **Administración de equipos**, expanda **Usuarios y grupos locales** y luego seleccione **Grupos**.
1. Haga doble clic en el grupo **Administradores** .
1. En el cuadro de diálogo **Propiedades de administradores**, seleccione el botón **Agregar**.
1. Especifique el usuario **CORP\Install** y seleccione **Aceptar**.
1. Seleccione **Aceptar** para cerrar el cuadro de diálogo **Propiedades de administradores**.
1. Repita estos pasos en **SQL-VM-2**. 

### <a name="add-account-to-sysadmin"></a>Adición de la cuenta a sysadmin 

La cuenta de instalación (CORP\install) usada para configurar el grupo de disponibilidad debe formar parte del rol fijo de servidor **sysadmin** en cada máquina virtual de SQL Server. 

Para conceder los derechos de **sysadmin** a la cuenta de instalación, siga estos pasos: 

1. Conéctese al servidor mediante el Protocolo de escritorio remoto (RDP) con la cuenta *\<MachineName\>\AdministradorDeDominio*, por ejemplo, `SQL-VM-1\DomainAdmin`.
1. Abra SQL Server Management Studio y conéctese a la instancia local de SQL Server.
1. En el **Explorador de objetos**, seleccione **Seguridad**.
1. Haga clic con el botón derecho en **Inicios de sesión**. Seleccione **Nuevo inicio de sesión**.
1. En **Inicio de sesión: Nuevo**, seleccione **Buscar**.
1. Seleccione **Ubicaciones**.
1. Escriba las credenciales del administrador de dominio.
1. Utilice la cuenta de instalación (CORP\install).
1. Establezca el inicio de sesión como un miembro del rol de servidor fijo **sysadmin**.
1. Seleccione **Aceptar**.
1. Repita estos pasos en la segunda máquina virtual de SQL Server, por ejemplo, **SQL-VM-2**, conectándose con la cuenta del nombre de máquina correspondiente, como `SQL-VM-2\DomainAdmin`. 


### <a name="add-system-account"></a>Adición de una cuenta del sistema 

En las últimas versiones de SQL Server, la cuenta [NT AUTHORITY\SYSTEM] no tiene permisos para SQL Server de manera predeterminada y se deben conceder manualmente. 

Para agregar [NT AUTHORITY\SYSTEM] y conceder los permisos adecuados, siga estos pasos: 

1. Conéctese a la primera máquina virtual de SQL Server mediante el Protocolo de escritorio remoto (RDP) con la cuenta *\<MachineName\>\AdministradorDeDominio*, por ejemplo, `SQL-VM-1\DomainAdmin`.
1. Abra SQL Server Management Studio y conéctese a la instancia local de SQL Server.
1. Cree una cuenta para `[NT AUTHORITY\SYSTEM]` en cada instancia de SQL Server mediante el siguiente comando Transact-SQL (T-SQL): 

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. Conceda los permisos siguientes a `[NT AUTHORITY\SYSTEM]` en cada instancia de SQL Server:

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   Para conceder estos permisos, use el siguiente comando Transact-SQL (T-SQL): 

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

1. Repita estos pasos en la segunda máquina virtual de SQL Server, por ejemplo, **SQL-VM-2**, conectándose con la cuenta del nombre de máquina correspondiente, como `SQL-VM-2\DomainAdmin`. 

### <a name="set-the-sql-server-service-accounts"></a>Configuración de las cuentas de servicio de SQL Server

El servicio SQL Server de cada máquina virtual debe usar una cuenta de dominio dedicada.  Use las cuentas de dominio que creó anteriormente: **Corp\SQLSvc1** para **SQL-VM-1** y **Corp\SQLSvc2** para **SQL-VM-2**. 

Para establecer la cuenta de servicio, siga estos pasos: 

1. Conéctese a la primera máquina virtual de SQL Server mediante el Protocolo de escritorio remoto (RDP) con la cuenta *\<MachineName\>\AdministradorDeDominio*, por ejemplo, `SQL-VM-1\DomainAdmin`.
1. Abra el **Administrador de configuración de SQL Server**.
1. Haga clic con el botón derecho en el servicio SQL Server y seleccione **Propiedades**.
1. Proporcione la cuenta (**Corp\SQLSvc1**) y la contraseña.
1. Seleccione **Aplicar** para confirmar el cambio y reiniciar el servicio SQL Server. 
1. Repita estos pasos en la otra máquina virtual de SQL Server (SQL-VM-1), inicie sesión con la cuenta de dominio del equipo, por ejemplo, `SQL-VM-2\DomainAdmin`, y proporcione la segunda cuenta de servicio (**Corp\SQLSvc2**). 


## <a name="create-azure-storage-account"></a>Creación de una cuenta de Azure Storage

Para implementar un clúster de conmutación por error de dos nodos con Windows Server, se necesita un tercer miembro para establecer el cuórum. En las máquinas virtuales de Azure, el testigo en la nube es la opción de cuórum recomendada. Para configurar un testigo en la nube, necesita una cuenta de Azure Storage. Para más información, consulte [Implementación de un testigo en la nube para un clúster de conmutación por error](/windows-server/failover-clustering/deploy-cloud-witness).

Para crear la cuenta de Azure Storage en el portal:

1. En el portal, abra el grupo de recursos **SQL-HA-RG** y seleccione **+ Crear**.
1. Busque **cuenta de almacenamiento**.
1. Seleccione **Cuenta de almacenamiento** y seleccione **Crear**, configurándola con los siguientes valores: 

    1. Seleccione la suscripción y seleccione el grupo de recursos **SQL-HA-RG**.
    1. Escriba un **Nombre de cuenta de almacenamiento** para la cuenta de almacenamiento.
       Los nombres de las cuentas de almacenamiento deben tener entre 3 y 24 caracteres, y solo pueden incluir números y letras en minúscula. El nombre de la cuenta de almacenamiento también debe ser único dentro de Azure.    
    1. Seleccione la **Región**.
    1. En **Rendimiento**, seleccione **Estándar: recomendado para la mayoría de los escenarios (cuenta de uso general v2)** . No se admite Azure Premium Storage para un testigo en la nube.
    1. En **Redundancia**, seleccione **Almacenamiento con redundancia local (LRS)** .
       Los clústeres de conmutación por error usan el archivo de blob como punto de arbitraje, lo cual requiere algunas garantías de coherencia al leer los datos. Por tanto, debe seleccionar Almacenamiento con redundancia local para Tipo de Replicación. 
    1. Seleccionar **Revisar y crear**.

## <a name="configure-the-firewall"></a>Configuración del firewall

La característica de grupo de disponibilidad se basa en el tráfico en los siguientes puertos TCP: 

- **Máquina virtual con SQL Server**: Puerto 1433 para una instancia predeterminada de SQL Server.
- **Punto de conexión de reflejo de la base de datos:** Cualquier puerto disponible. A menudo, los ejemplos utilizan el 5022.

Abra estos puertos del firewall en ambas máquinas virtuales de SQL Server. El método para abrir los puertos depende de la solución de firewall que use y puede variar con respecto al ejemplo de Firewall de Windows que se proporciona en esta sección. 

Para abrir estos puertos en un Firewall de Windows, siga estos pasos: 

1. En la pantalla **Inicio** del primer servidor SQL Server, abra **Firewall de Windows con seguridad avanzada**.
1. En el panel izquierdo, seleccione **Reglas de entrada**. En el panel derecho, seleccione **Nueva regla**.
1. En **Tipo de regla**, elija **Puerto**.
1. Para el puerto, especifique **TCP** y escriba los números de puerto adecuados. Observe el ejemplo siguiente:

   :::image type="content" source="./media/availability-group-manually-configure-prerequisites-tutorial-multi-subnet/17-firewall-tcp-ports.png" alt-text="Firewall de SQL":::

1. Seleccione **Next** (Siguiente).
1. En la página **Acción**, seleccione **Permitir la conexión** y, a continuación, **Siguiente**.
1. En la página **Perfiles**, acepte la configuración predeterminada y luego seleccione **Siguiente**.
1. En la página **Nombre**, especifique el nombre de la regla, como **SQL de entrada** en el cuadro de texto **Nombre** y, a continuación, seleccione **Finalizar**.
1. Repita estos pasos en la segunda máquina virtual con SQL Server.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha configurado los requisitos previos, comience con la [configuración del grupo de disponibilidad](availability-group-manually-configure-tutorial-multi-subnet.md) en varias subredes. 

Para obtener más información, consulte:

- [Clúster de conmutación por error de Windows Server con SQL Server en máquinas virtuales de Azure](hadr-windows-server-failover-cluster-overview.md)
- [Grupos de disponibilidad Always On para SQL Server en Azure Virtual Machines](availability-group-overview.md)
- [Introducción a los grupos de disponibilidad Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Configuración de alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](hadr-cluster-best-practices.md)