---
title: Implementación de Azure Monitor para soluciones de SAP con Azure Portal
description: Aprenda a usar un método de explorador para implementar Azure Monitor para soluciones de SAP.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 3acbef6c8521022ae847925e48d3cd42e13dc56e
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122965404"
---
# <a name="deploy-azure-monitor-for-sap-solutions-by-using-the-azure-portal"></a>Implementación de Azure Monitor para soluciones de SAP con Azure Portal

En este artículo, se explicará cómo implementar Azure Monitor para soluciones de SAP desde [Azure Portal](https://azure.microsoft.com/features/azure-portal). Mediante la interfaz basada en explorador del portal, implementaremos los Azure Monitor para soluciones de SAP y configuraremos los proveedores.

## <a name="sign-in-to-the-portal"></a>Iniciar sesión en el portal

Inicie sesión en [Azure Portal](https://portal.azure.com).

## <a name="create-a-monitoring-resource"></a>Creación de un recurso de supervisión

1. En **Marketplace**, seleccione **Azure Monitor para soluciones de SAP**.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-1.png" alt-text="Captura de pantalla que muestra la selección de la oferta Azure Monitor para soluciones de SAP en Azure Marketplace." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-1.png":::

2. En la pestaña **Aspectos básicos**, proporcione los valores necesarios. Si corresponde, puede usar un área de trabajo de Log Analytics existente.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-2.png" alt-text="Captura de pantalla que muestra las opciones de configuración en la pestaña Aspectos básicos." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-2.png":::

   Al seleccionar una red virtual, asegúrese de que los sistemas que quiere supervisar son accesibles desde dentro de esa red virtual. 

   > [!IMPORTANT]
   > Al seleccionar **Compartir** para **Compartir datos con el Soporte técnico de Microsoft**, permite que nuestros equipos de soporte técnico le ayuden a solucionar problemas.

## <a name="configure-providers"></a>Configurar proveedores

### <a name="sap-netweaver-provider"></a>Proveedor de SAP NetWeaver

El servicio de inicio de SAP proporciona una serie de servicios, incluida la supervisión del sistema SAP. Usaremos SAPControl, que es una interfaz de servicio web SOAP que expone estas funcionalidades. La interfaz de servicio web de SAPControl diferencia entre los métodos de servicio web [protegidos y no protegidos](https://wiki.scn.sap.com/wiki/display/SI/Protected+web+methods+of+sapstartsrv). 

Para capturar métricas específicas, debe desproteger algunos métodos para la versión actual. Siga estos pasos para cada sistema SAP:

1. Abra una conexión de GUI de SAP al servidor SAP.
2. Inicie sesión con una cuenta administrativa.
3. Ejecute la transacción RZ10.
4. Seleccione el perfil adecuado (*DEFAULT.PFL*).
5. Seleccione **Mantenimiento extendido** > **Cambiar**. 
6. Seleccione el parámetro de perfil "service/protectedwebmethods" y modifíquelo para que tenga el siguiente valor y, a continuación, haga clic en Copiar:  
 
SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList 

7. Vuelva atrás y seleccione **Perfil** > **Guardar**.
8. Una vez guardados los cambios de este parámetro, reinicie el servicio SAPStartSRV en cada una de las instancias del sistema SAP. (Reiniciar los servicios no reiniciará el sistema SAP; solo reiniciará el servicio SAPStartSRV (en Windows) o el proceso de demonio (en Unix/Linux)) 8a. En Windows, esto se puede hacer en una sola ventana mediante SAP Microsoft Management Console (MMC)/SAP Management Console(MC).  Haga clic con el botón derecho en cada instancia y elija Todas las tareas -> Reiniciar servicio.
   ![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png) 8b. En sistemas Linux, use el comando sapcontrol -nr <NN> -function RestartService, donde NN es el número de instancia de SAP para reiniciar el host en el que se ha iniciado sesión.
9. Una vez reiniciado el servicio SAP, compruebe que se han aplicado las reglas de exclusión de protección de métodos web actualizadas para cada instancia mediante la ejecución del siguiente comando: sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user "<adminUser>" "<adminPassword>". La salida debe ser como :- ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png).
10. Para concluir y validar, se puede realizar una consulta de prueba en métodos web para validar la conexión iniciando sesión en cada instancia y ejecutando los siguientes comandos; para todas las instancias : sapcontrol -nr <NN> -function GetProcessList; para la instancia de ENQUE : sapcontrol -nr <NN> -function EnqGetStatistic; para instancias de ABAP: sapcontrol -nr <NN> -function ABAPGetWPTable; para instancias de ABAP/J2EE/JEE: sapcontrol -nr <NN> -function GetQueueStatistic.

>[!Important] 
>Es fundamental que el servicio sapstartsrv se reinicie en cada instancia del sistema SAP para que los métodos web de SAPControl se desprotejan.  Estas API SOAP de solo lectura son necesarias para que el proveedor de NetWeaver obtenga datos de métricas del sistema SAP y, si no se desprotegen estos métodos, se provocarán visualizaciones vacías o no se generará ninguna visualización en el libro de métricas de NetWeaver.
   
>[!Tip]
> Use una lista de control de acceso (ACL) para filtrar el acceso a un puerto de servidor. Para más información, consulte [esta nota de SAP](https://launchpad.support.sap.com/#/notes/1495075).

Para instalar el proveedor de NetWeaver en Azure Portal:

1. Asegúrese de que ha completado los pasos de requisitos previos anteriores y de que se ha reiniciado el servidor.
1. En Azure Portal, en **Azure Monitor para soluciones de SAP**, seleccione **Agregar proveedor** y, después:

   1. En **Tipo**, seleccione **SAP NetWeaver**.

   1. En **Nombre de host**, escriba el nombre de host del sistema SAP.

   1. En **Subdominio** escriba un subdominio si se aplica uno.

   1. En **Instance No** (Número de instancia), escriba el número de instancia que corresponde al nombre de host especificado. 

   1. En **SID**, escriba el identificador del sistema.
   
   ![Captura de pantalla que muestra las opciones de configuración para agregar un proveedor de SAP NetWeaver.](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.  Cuando termine, seleccione **Agregar proveedor**. Siga agregando proveedores según sea necesario o seleccione **Revisar y crear** para completar la implementación.

>[!Important]
>Si los servidores de aplicaciones SAP (es decir, las máquinas virtuales) forman parte de un dominio de red, como uno administrado por Azure Active Directory, es fundamental que el subdominio correspondiente se proporcione en el cuadro de texto Subdominio.  La máquina virtual del recopilador de Azure Monitor para SAP que existe dentro de la red virtual no está unido al dominio y, por tanto, no podrá resolver el nombre de host de las instancias dentro del sistema SAP, a menos que el nombre de host sea un nombre de dominio completo.  Si no se proporciona esto, no habrá visualizaciones o estarán incompletas en el libro de NetWeaver.
 
>Por ejemplo, si el nombre de host del sistema SAP tiene un nombre de dominio completo de "myhost.mycompany.global.corp", escriba un nombre de host de "myhost" y proporcione un subdominio de "mycompany.global.corp".  Cuando el proveedor de NetWeaver invoca la API GetSystemInstanceList en el sistema SAP, SAP devuelve los nombres de host de todas las instancias del sistema.  La máquina virtual del recopilador usará esta lista para realizar llamadas API adicionales para capturar métricas específicas de las características de cada instancia (por ejemplo, ABAP, J2EE, MESSAGESERVER, ENQUE, ENQREP, etc.). Si se especifica, la máquina virtual del recopilador usará el subdominio "mycompany.global.corp" para compilar el nombre de dominio completo de cada instancia del sistema SAP.  
 
>NO especifique una dirección IP para el campo de nombre de host si el sistema SAP forma parte del dominio de red.

   
### <a name="sap-hana-provider"></a>Proveedor de SAP HANA 

1. Seleccione la pestaña **Proveedores** para agregar los proveedores que quiere configurar. Puede agregar varios proveedores uno tras otro o agregarlos después de implementar el recurso de supervisión. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Captura de pantalla que muestra la pestaña donde agrega proveedores" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::.

1. Seleccione **Agregar proveedor** y, después:

   1. En **Tipo**, seleccione **SAP HANA**. 

      > [!IMPORTANT]
      > Asegúrese de que un proveedor de SAP HANA esté configurado para el nodo `master` de SAP HANA.

   1. Para la **Dirección IP**, escriba la dirección IP privada para el servidor HANA.

   1. Para **Inquilino de la base de datos**, escriba el nombre del inquilino que quiere usar. Puede elegir cualquier inquilino, pero se recomienda usar **SYSTEMDB** porque permite una matriz más amplia de áreas de supervisión. 

   1. Para **Puerto de SQL**, escriba el número de puerto de asociado con la base de datos HANA. Debe tener el formato *[3]*  +  *[instance#]*  +  *[13]* . Por ejemplo, **30013**. 

   1. Para **Nombre de usuario de la base de datos**, escriba el nombre de usuario que quiere usar. Asegúrese de que el usuario de la base de datos tiene asignados los roles de *supervisión* y *lectura de catálogo*.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Captura de pantalla que muestra las opciones de configuración para agregar un proveedor de SAP HANA" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::.

1. Cuando termine, seleccione **Agregar proveedor**. Siga agregando proveedores según sea necesario o seleccione **Revisar y crear** para completar la implementación.

   
### <a name="microsoft-sql-server-provider"></a>Proveedor de Microsoft SQL Server

1. Antes de agregar el proveedor de Microsoft SQL Server, ejecute el script siguiente en SQL Server Management Studio para crear un usuario con los permisos adecuados necesarios para configurar el proveedor.

   ```sql
   USE [<Database to monitor>]
   DROP USER [AMS]
   GO
   USE [master]
   DROP USER [AMS]
   DROP LOGIN [AMS]
   GO
   CREATE LOGIN [AMS] WITH PASSWORD=N'<password>', DEFAULT_DATABASE=[<Database to monitor>], DEFAULT_LANGUAGE=[us_english], CHECK_EXPIRATION=OFF, CHECK_POLICY=OFF
   CREATE USER AMS FOR LOGIN AMS
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GRANT CONNECT TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW SERVER STATE TO AMS
   GRANT VIEW ANY DEFINITION TO AMS
   GRANT EXEC ON xp_readerrorlog TO AMS
   GO
   USE [<Database to monitor>]
   CREATE USER [AMS] FOR LOGIN [AMS]
   ALTER ROLE [db_datareader] ADD MEMBER [AMS]
   ALTER ROLE [db_denydatawriter] ADD MEMBER [AMS]
   GO
   ``` 

1. Seleccione **Agregar proveedor** y, después:

   1. En **Tipo**, seleccione **Microsoft SQL Server**. 

   1. Rellene los campos restantes con la información asociada a su instancia de SQL Server. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-6.png" alt-text="Captura de pantalla que muestra las opciones de configuración para agregar un proveedor de Microsoft SQL Server" lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-6.png":::.

1. Cuando termine, seleccione **Agregar proveedor**. Siga agregando proveedores según sea necesario o seleccione **Revisar y crear** para completar la implementación.

### <a name="high-availability-cluster-pacemaker-provider"></a>Proveedor de clúster de alta disponibilidad (Pacemaker)

Antes de agregar proveedores para clústeres de alta disponibilidad (Pacemaker), instale el agente adecuado para su entorno.

En el caso de los clústeres basados en **SUSE**, asegúrese de que ha_cluster_provider está instalado en cada nodo. Vea cómo instalar el [exportador de clústeres de alta disponibilidad](https://github.com/ClusterLabs/ha_cluster_exporter#installation). Versiones de SUSE compatibles: SLES para SAP 12 SP3 y versiones posteriores.  
   
En el caso de los clústeres basados en **RHEL**, asegúrese de que el paquete copiloto de rendimiento (DAX) y el subpaquete pcp-pmda-hacluster está instalado en cada nodo. Vea cómo instalar el [agente de PCP HACLUSTER] (https://access.redhat.com/articles/6139852) ). Versiones compatibles de RHEL: 8.2, 8.4 y versiones posteriores.
 
Después de completar la instalación de los requisitos previos anterior, cree un proveedor para cada nodo de clúster.

1. Seleccione **Agregar proveedor** y, después:

1. En **Tipo**, seleccione **Clúster de alta disponibilidad (Pacemaker)** . 
   
1. Configure proveedores para cada nodo del clúster; para ello, escriba la dirección URL del punto de conexión en **HA Cluster Exporter Endpoint** (Punto de conexión del exportador de clústeres de alta disponibilidad). Para los clústeres basados en **SUSE**, escriba **http://<IP  address>:9664/metrics**. Para los clústeres basados en **RHEL**, escriba **http://<IP address>:44322/metrics?names=ha_cluster**.
 
1. Escriba el identificador del sistema, el nombre de host y el nombre del clúster en los cuadros correspondientes.
   
   > [!IMPORTANT]
   > El nombre de host hace referencia al nombre de host real de la máquina virtual. Use el comando "hostname -s" para los clústeres basados en SUSE y RHEL.  

1. Cuando termine, seleccione **Agregar proveedor**. Siga agregando proveedores según sea necesario o seleccione **Revisar y crear** para completar la implementación.

### <a name="os-linux-provider"></a>Proveedor del SO (Linux) 

1. Seleccione **Agregar proveedor** y, después:

   1. En **Tipo**, seleccione **Sistema operativo (Linux)** . 

      >[!IMPORTANT]
      > Para configurar el proveedor del sistema operativo (Linux), asegúrese de que la [última versión de node_exporter](https://prometheus.io/download/#node_exporter) está instalada en cada host (BareMetal o máquina virtual) que desee supervisar. [Más información](https://github.com/prometheus/node_exporter).

   1. En **Nombre**, escriba un nombre, que será el identificador de la instancia de BareMetal.

   1. En **Punto de conexión de Node Exporter**, escriba **http://IP:9100/metrics** .

      >[!IMPORTANT]
      >Use la dirección IP privada del host Linux. Asegúrese de que el host y Azure Monitor para el recurso de SAP se encuentran en la misma red virtual. 
      >
      >El puerto de firewall 9100 debe estar abierto en el host Linux. Si usa `firewall-cmd`, ejecute los comandos siguientes: 
      >
      >`firewall-cmd --permanent --add-port=9100/tcp`
      >
      >`firewall-cmd --reload`
      >
      >Si usa `ufw`, ejecute los comandos siguientes:
      >
      >`ufw allow 9100/tcp`
      >
      >`ufw reload`
      >
      > Si el host Linux es una máquina virtual (VM) de Azure, asegúrese de que todos los grupos de seguridad de red aplicables permitan el tráfico entrante en el puerto 9100 desde `VirtualNetwork` como origen.
 
1. Cuando termine, seleccione  **Agregar proveedor**. Continúe agregando proveedores según sea necesario o seleccione  **Revisar y crear**  para completar la implementación. 


## <a name="next-steps"></a>Pasos siguientes

Más información sobre Azure Monitor para soluciones de SAP.

> [!div class="nextstepaction"]
> [Supervisión de SAP en Azure](monitor-sap-on-azure.md)
