---
title: Implementación de Azure Monitor para soluciones de SAP con Azure Portal
description: Aprenda a usar un método de explorador para implementar Azure Monitor para soluciones de SAP.
author: sameeksha91
ms.author: sakhare
ms.topic: how-to
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.date: 07/08/2021
ms.openlocfilehash: 85cfe6887ded3844e2143754c31a3c6efee5e132
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128579467"
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
6. Seleccione el parámetro de perfil "service/protectedwebmethods", modifíquelo para que tenga el siguiente valor y, después, haga clic en Copiar:  

   ```service/protectedwebmethods instruction
      SDEFAULT -GetQueueStatistic -ABAPGetWPTable -EnqGetStatistic -GetProcessList```

7. Go back and select **Profile** > **Save**.
8. After saving the changes for this parameter, please restart the SAPStartSRV service on each of the instances in the SAP system. (Restarting the services will not restart the SAP system; it will only restart the SAPStartSRV service (in Windows) or daemon process (in Unix/Linux))
   8a. On Windows systems, this can be done in a single window using the SAP Microsoft Management Console (MMC) / SAP Management Console(MC).  Right-click on each instance and choose All Tasks -> Restart Service.
![MMC](https://user-images.githubusercontent.com/75772258/126453939-daf1cf6b-a940-41f6-98b5-3abb69883520.png)

   8b. On Linux systems, use the below command where NN is the SAP instance number to restart the host which is logged into.
   
   ```RestartService
   sapcontrol -nr <NN> -function RestartService```
   
9. Once the SAP service is restarted, please check to ensure the updated web method protection exclusion rules have been applied for each instance by running the following command: 

**Logged as \<sidadm\>** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods`

**Logged as different user** 
   `sapcontrol -nr <NN> -function ParameterValue service/protectedwebmethods -user "<adminUser>" "<adminPassword>"`

   The output should look like :-
   ![SS](https://user-images.githubusercontent.com/75772258/126454265-d73858c3-c32d-4afe-980c-8aba96a0b2a4.png)

10. To conclude and validate, a test query can be done against web methods to validate the connection by logging into each instance and running the following commands:

    - For all instances : `sapcontrol -nr <NN> -function GetProcessList`
    - For the ENQUE instance : `sapcontrol -nr <NN> -function EnqGetStatistic`
    - For ABAP instances : `sapcontrol -nr <NN> -function ABAPGetWPTable`
    - For ABAP/J2EE/JEE instances : `sapcontrol -nr <NN> -function GetQueueStatistic`

>[!Important] 
>It is critical that the sapstartsrv service is restarted on each instance of the SAP system for the SAPControl web methods to be unprotected.  These read-only SOAP API are required for the NetWeaver provider to fetch metric data from the SAP System and failure to unprotect these methods will lead to empty or missing visualizations on the NetWeaver metric workbook.
   
>[!Tip]
> Use an access control list (ACL) to filter the access to a server port. For more information, see [this SAP note](https://launchpad.support.sap.com/#/notes/1495075).

To install the NetWeaver provider on the Azure portal:

1. Make sure you've completed the earlier prerequisite steps and that the server has been restarted.
1. On the Azure portal, under **Azure Monitor for SAP Solutions**, select **Add provider**, and then:

   1. For **Type**, select **SAP NetWeaver**.

   1. For **Hostname**, enter the host name of the SAP system.

   1. For **Subdomain**, enter a subdomain if one applies.

   1. For **Instance No**, enter the instance number that corresponds to the host name you entered. 

   1. For **SID**, enter the system ID.
   
   ![Screenshot showing the configuration options for adding a SAP NetWeaver provider.](https://user-images.githubusercontent.com/75772258/114583569-5c777d80-9c9f-11eb-99a2-8c60987700c2.png)

1.    When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

>[!Important]
>If the SAP application servers (ie. virtual machines) are part of a network domain, such as one managed by Azure Active Directory, then it is critical that the corresponding subdomain is provided in the Subdomain text box.  The Azure Monitor for SAP collector VM that exists inside the Virtual Network is not joined to the domain and as such will not be able to resolve the hostname of instances inside the SAP system unless the hostname is a fully qualified domain name.  Failure to provide this will result in missing / incomplete visualizations in the NetWeaver workbook.
 
>For example, if the hostname of the SAP system has a fully qualified domain name of "myhost.mycompany.global.corp" then please enter a Hostname of "myhost" and provide a Subdomain of "mycompany.global.corp".  When the NetWeaver provider invokes the GetSystemInstanceList API on the SAP system, SAP returns the hostnames of all instances in the system.  The collector VM will use this list to make additional API calls to fetch metrics specific to each instance's features (e.g.  ABAP, J2EE, MESSAGESERVER, ENQUE, ENQREP, etc…). If specified, the collector VM will then use the subdomain  "mycompany.global.corp" to build the fully qualified domain name of each instance in the SAP system.  
 
>Please DO NOT specify an IP Address for the hostname field if the SAP system is a part of network domain.

   
### SAP HANA provider 

1. Select the **Providers** tab to add the providers you want to configure. You can add multiple providers one after another, or add them after you deploy the monitoring resource. 

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-3.png" alt-text="Screenshot showing the tab where you add providers." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-3.png":::

1. Select **Add provider**, and then:

   1. For **Type**, select **SAP HANA**. 

      > [!IMPORTANT]
      > Ensure that a SAP HANA provider is configured for the SAP HANA `master` node.

   1. For **IP address**, enter the private IP address for the HANA server.

   1. For **Database tenant**, enter the name of the tenant you want to use. You can choose any tenant, but we recommend using **SYSTEMDB** because it enables a wider array of monitoring areas. 

   1. For **SQL port**, enter the port number associated with your HANA database. It should be in the format of *[3]* + *[instance#]* + *[13]*. An example is **30013**. 

   1. For **Database username**, enter the username you want to use. Ensure the database user has the *monitoring* and *catalog read* roles assigned.

   :::image type="content" source="./media/azure-monitor-sap/azure-monitor-quickstart-4.png" alt-text="Screenshot showing configuration options for adding an SAP HANA provider." lightbox="./media/azure-monitor-sap/azure-monitor-quickstart-4.png":::

1. When you're finished, select **Add provider**. Continue to add providers as needed, or select **Review + create** to complete the deployment.

   
### Microsoft SQL Server provider

1. Before you add the Microsoft SQL Server provider, run the following script in SQL Server Management Studio to create a user with the appropriate permissions for configuring the provider.

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
   
1. Configure proveedores para cada nodo del clúster; para ello, escriba la dirección URL del punto de conexión en **HA Cluster Exporter Endpoint** (Punto de conexión del exportador de clústeres de alta disponibilidad). Para los clústeres basados en **SUSE**, escriba **http://\<IP  address\>:9664/metrics**. Para los clústeres basados en **RHEL**, escriba **http://\<IP address\>:44322/metrics?names=ha_cluster**.
 
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
