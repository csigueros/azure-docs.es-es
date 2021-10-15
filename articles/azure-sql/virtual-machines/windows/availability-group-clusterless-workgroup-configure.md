---
title: Configuración de un grupo de disponibilidad de grupo de trabajo independiente del dominio
description: Aprenda a configurar un grupo de disponibilidad Always On de grupo de trabajo independiente del dominio de Active Directory en una máquina virtual de SQL Server en Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: 53981f7e-8370-4979-b26a-93a5988d905f
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/29/2020
ms.author: mathoma
ms.openlocfilehash: 86714655c05968a32e16c83b714705929baf1ca7
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233680"
---
# <a name="configure-a-workgroup-availability-group"></a>Configuración de un grupo de disponibilidad de grupo de trabajo 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

En este artículo se explican los pasos necesarios para crear un clúster independiente del dominio de Active Directory con un grupo de disponibilidad Always On, que también se conoce como un clúster de grupo de trabajo. Este artículo se centra en los pasos relevantes para preparar y configurar el grupo de trabajo y el grupo de disponibilidad, y trata por encima los pasos descritos en otros artículos, como la creación del clúster o la implementación del grupo de disponibilidad. 


## <a name="prerequisites"></a>Requisitos previos

Para configurar un grupo de disponibilidad de grupo de trabajo, necesita lo siguiente:
- Al menos dos máquinas virtuales con Windows Server 2016 (o superior) que ejecuten SQL Server 2016 (o superior), implementadas en el mismo conjunto de disponibilidad o en zonas de disponibilidad diferentes, mediante direcciones IP estáticas. 
- Una red local con un mínimo de 4 direcciones IP libres en la subred. 
- Una cuenta en cada máquina del grupo de administradores que también tenga derechos de administrador del sistema en SQL Server. 
- Puertos abiertos: TCP 1433, TCP 5022 y TCP 59999. 

Como referencia, se usan los parámetros siguientes en este artículo, pero se pueden modificar según sea necesario: 

| **Nombre** | **Parámetro** |
| :------ | :---------------------------------- |
| **Node1**   | AGNode1 (10.0.0.4) |
| **Node2**   | AGNode2 (10.0.0.5) |
| **Nombre del clúster** | AGWGAG (10.0.0.6) |
| **Agente de escucha** | AGListener (10.0.0.7) | 
| **Sufijo DNS** | ag.wgcluster.example.com | 
| **Nombre del grupo de trabajo** | AGWorkgroup | 
| &nbsp; | &nbsp; |

## <a name="set-a-dns-suffix"></a>Definición de un sufijo DNS 

En este paso, configure el sufijo DNS para ambos servidores. Por ejemplo, `ag.wgcluster.example.com`. Esto le permite usar el nombre del objeto al que desea conectarse como una dirección completa dentro de la red, como `AGNode1.ag.wgcluster.example.com`. 

Para configurar el sufijo DNS, siga estos pasos:

1. Habilite RDP en el primer nodo y abra el Administrador del servidor. 
1. Seleccione **Servidor local** y, a continuación, seleccione el nombre de la máquina virtual en **Nombre del equipo**. 
1. Seleccione **Cambiar** en **Para cambiar el nombre de este equipo**. 
1. Cambie el nombre del grupo de trabajo para que tenga sentido, como `AGWORKGROUP`: 

   ![Cambiar el nombre del grupo de trabajo](./media/availability-group-clusterless-workgroup-configure/1-change-workgroup-name.png)

1. Seleccione **Más** para abrir el cuadro de diálogo **Sufijo DNS y nombre NetBIOS del equipo**. 
1. Escriba el nombre del sufijo DNS en **Sufijo DNS principal de este equipo** (por ejemplo, `ag.wgcluster.example.com`) y, a continuación, seleccione **Aceptar**: 

   ![Captura de pantalla que muestra el sufijo DNS y el cuadro de diálogo Nombre NetBIOS del equipo en el que puede escribir el valor.](./media/availability-group-clusterless-workgroup-configure/2-add-dns-suffix.png)

1. Confirme que **Nombre completo del equipo** ahora muestra el sufijo DNS y, a continuación, seleccione **Aceptar** para guardar los cambios: 

   ![Captura de pantalla que muestra dónde ver el nombre completo del equipo.](./media/availability-group-clusterless-workgroup-configure/3-confirm-full-computer-name.png)

1. Reinicie el servidor cuando se le pida que lo haga. 
1. Repita estos pasos en todos los demás nodos que se van a usar para el grupo de disponibilidad. 

## <a name="edit-a-host-file"></a>Edición de un archivo host

Al no haber ninguna instancia de Active Directory, no hay forma de autenticar las conexiones de Windows. Por tanto, para asignar la confianza, edite el archivo host con un editor de texto. 

Para editar el archivo host, siga estos pasos:

1. Habilite RDP en la máquina virtual. 
1. Use el **Explorador de archivos** para ir a `c:\windows\system32\drivers\etc`. 
1. Haga clic con el botón derecho en el archivo **hosts** y abra el archivo con **Bloc de notas** (o cualquier otro editor de texto).
1. Al final del archivo, agregue una entrada para cada nodo, el grupo de disponibilidad y el agente de escucha con el formato `IP Address, DNS Suffix #comment`, como se muestra a continuación: 

   ```
   10.0.0.4 AGNode1.ag.wgcluster.example.com #Availability group node
   10.0.0.5 AGNode2.ag.wgcluster.example.com #Availability group node
   10.0.0.6 AGWGAG.ag.wgcluster.example.com #Cluster IP
   10.0.0.7 AGListener.ag.wgcluster.example.com #Listener IP
   ```
 
   ![Agregar entradas para la dirección IP, el clúster y el agente de escucha al archivo host](./media/availability-group-clusterless-workgroup-configure/4-host-file.png)

## <a name="set-permissions"></a>Establecimiento de permisos

Puesto que no hay ninguna instancia de Active Directory para administrar los permisos, debe permitir manualmente una cuenta de administrador local no integrada para crear el clúster. 

Para ello, ejecute el siguiente cmdlet de PowerShell en una sesión de PowerShell administrativa en cada nodo: 

```PowerShell

new-itemproperty -path HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System -Name LocalAccountTokenFilterPolicy -Value 1
```

## <a name="create-the-failover-cluster"></a>Creación del clúster de conmutación por error

En este paso, creará el clúster de conmutación por error siguiente. Si no está familiarizado con estos pasos, puede seguirlos desde el [tutorial sobre los clústeres de conmutación por error](failover-cluster-instance-storage-spaces-direct-manually-configure.md).

Diferencias importantes entre el tutorial y lo que se debe hacer para un clúster de grupo de trabajo:
- Desactive **Almacenamiento** y **Espacios de almacenamiento directo** cuando ejecute la validación del clúster. 
- Al agregar los nodos al clúster, agregue el nombre completo, como:
   - `AGNode1.ag.wgcluster.example.com`
   - `AGNode2.ag.wgcluster.example.com`
- Desactive **Agregar todo el almacenamiento apto al clúster**. 

Una vez creado el clúster, asigne una dirección IP de clúster estática. Para hacerlo, siga estos pasos:

1. En uno de los nodos, abra **Administrador de clústeres de conmutación por error**, seleccione el clúster, haga clic con el botón derecho en **Nombre: \<ClusterNam>** en **Recursos principales de clúster** y, luego, seleccione **Propiedades**. 

   ![Abrir propiedades del nombre del clúster](./media/availability-group-clusterless-workgroup-configure/5-launch-cluster-name-properties.png)

1. Seleccione la dirección IP en **Direcciones IP** y seleccione **Editar**. 
1. Seleccione **Usar estática**, proporcione la dirección IP del clúster y, después, seleccione **Aceptar**: 

   ![Proporcionar una dirección IP estática para el clúster](./media/availability-group-clusterless-workgroup-configure/6-provide-static-ip-for-cluster.png)

1. Compruebe que la configuración sea correcta y, a continuación, seleccione **Aceptar** para guardarla:

   ![Comprobar las propiedades del clúster](./media/availability-group-clusterless-workgroup-configure/7-verify-cluster-properties.png)

## <a name="create-a-cloud-witness"></a>Creación de un testigo en la nube 

En este paso, configure un testigo de recurso compartido en la nube. Si no está familiarizado con los pasos, consulte [Implementación de un testigo en la nube para un clúster de conmutación por error](/windows-server/failover-clustering/deploy-cloud-witness). 

## <a name="enable-the-availability-group-feature"></a>Habilitación de la característica de grupos de disponibilidad 

En este paso, habilitará la característica de grupo de disponibilidad. Si no está familiarizado con los pasos, consulte el [tutorial sobre los grupos de disponibilidad](availability-group-manually-configure-tutorial.md#enable-availability-groups). 

## <a name="create-keys-and-certificates"></a>Creación de claves y certificados

En este paso, cree los certificados que se usan en un inicio de sesión de SQL en el punto de conexión cifrado. Cree una carpeta en cada nodo para almacenar las copias de seguridad de los certificados, como `c:\certs`. 

Para configurar el primer nodo, siga estos pasos: 

1. Abra **SQL Server Management Studio** y conéctese al primer nodo, como `AGNode1`. 
1. Abra una ventana **Nueva consulta** y ejecute la siguiente instrucción Transact-SQL (T-SQL) después de actualizar a una contraseña compleja y segura:

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO

   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode1Cert   
      WITH SUBJECT = 'AGNode1 Certificate';  
   GO  

   --Backup the cert and transfer it to AGNode2
   BACKUP CERTIFICATE AGNode1Cert TO FILE = 'C:\certs\AGNode1Cert.crt';  
   GO  
   ```

1. A continuación, cree el punto de conexión HADR y use el certificado de autenticación mediante la ejecución de esta instrucción Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode1Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Use el **Explorador de archivos** para ir a la ubicación del archivo donde se encuentra su certificado, como `c:\certs`. 
1. Cree manualmente una copia del certificado, como `AGNode1Cert.crt`, desde el primer nodo y transfiérala a la misma ubicación en el segundo nodo. 

Para configurar el segundo nodo, siga estos pasos: 

1. Conéctese al segundo nodo con **SQL Server Management Studio**, como `AGNode2`. 
1. En una ventana **Nueva consulta**, ejecute la siguiente instrucción Transact-SQL (T-SQL) después de actualizar a una contraseña compleja y segura: 

   ```sql
   USE master;  
   CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'PassWOrd123!';  
   GO 
   
   --create a cert from the master key
   USE master;  
   CREATE CERTIFICATE AGNode2Cert   
      WITH SUBJECT = 'AGNode2 Certificate';  
   GO  
   --Backup the cert and transfer it to AGNode1
   BACKUP CERTIFICATE AGNode2Cert TO FILE = 'C:\certs\AGNode2Cert.crt';  
   GO
   ```

1. A continuación, cree el punto de conexión HADR y use el certificado de autenticación mediante la ejecución de esta instrucción Transact-SQL (T-SQL):

   ```sql
   --CREATE or ALTER the mirroring endpoint
   CREATE ENDPOINT hadr_endpoint  
      STATE = STARTED  
      AS TCP (  
         LISTENER_PORT=5022  
         , LISTENER_IP = ALL  
      )   
      FOR DATABASE_MIRRORING (   
         AUTHENTICATION = CERTIFICATE AGNode2Cert  
         , ENCRYPTION = REQUIRED ALGORITHM AES  
         , ROLE = ALL  
      );  
   GO  
   ```

1. Use el **Explorador de archivos** para ir a la ubicación del archivo donde se encuentra su certificado, como `c:\certs`. 
1. Cree manualmente una copia del certificado, como `AGNode2Cert.crt`, desde el segundo nodo y transfiérala a la misma ubicación en el primer nodo. 

Si hay otros nodos en el clúster, repita los mismos pasos en estos con los nombres de certificado respectivos. 

## <a name="create-logins"></a>Creación de inicios de sesión

La autenticación de certificados se usa para sincronizar los datos entre nodos. Para permitirlo, cree un inicio de sesión para el otro nodo, cree un usuario para el inicio de sesión, cree un certificado para que el inicio de sesión use el certificado de copia de seguridad y, a continuación, permita la conexión en el punto de conexión de creación de reflejo. 

Para ello, primero ejecute la siguiente consulta Transact-SQL (T-SQL) en el primer nodo, como `AGNode1`: 

```sql
--create a login for the AGNode2
USE master;  
CREATE LOGIN AGNode2_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode2_User FOR LOGIN AGNode2_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode2Cert  
   AUTHORIZATION AGNode2_User  
   FROM FILE = 'C:\certs\AGNode2Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode2_login];  
GO
```

A continuación, ejecute la siguiente consulta Transact-SQL (T-SQL) en el segundo nodo, como `AGNode2`: 

```sql
--create a login for the AGNode1
USE master;  
CREATE LOGIN AGNode1_Login WITH PASSWORD = 'PassWord123!';  
GO  

--create a user from the login
CREATE USER AGNode1_User FOR LOGIN AGNode1_Login;  
GO  

--create a certificate that the login uses for authentication
CREATE CERTIFICATE AGNode1Cert  
   AUTHORIZATION AGNode1_User  
   FROM FILE = 'C:\certs\AGNode1Cert.crt'  
GO 

--grant connect for login
GRANT CONNECT ON ENDPOINT::hadr_endpoint TO [AGNode1_login];  
GO
```

Si hay otros nodos en el clúster, repita los mismos pasos en estos con los nombres de certificado y usuario respectivos. 

## <a name="configure-an-availability-group"></a>Configuración de un grupo de disponibilidad

En este paso, configure el grupo de disponibilidad y agréguele sus bases de datos. No cree un agente de escucha en este momento. Si no está familiarizado con los pasos, consulte el [tutorial sobre los grupos de disponibilidad](availability-group-manually-configure-tutorial.md#create-the-availability-group). Asegúrese de iniciar una conmutación por error y una conmutación por recuperación para comprobar que todo funciona correctamente. 

   > [!NOTE]
   > Si se produce un error durante el proceso de sincronización, puede que tenga que conceder derechos de administrador del sistema `NT AUTHORITY\SYSTEM` para crear recursos de clúster en el primer nodo, como `AGNode1`, temporalmente. 

## <a name="configure-a-load-balancer"></a>Configuración de un equilibrador de carga

En este paso final, configure el equilibrador de carga mediante [Azure Portal](availability-group-load-balancer-portal-configure.md) o [PowerShell](availability-group-listener-powershell-configure.md).


## <a name="next-steps"></a>Pasos siguientes

Una vez que se haya implementado el grupo de disponibilidad, puede optimizar la [configuración de alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](hadr-cluster-best-practices.md). 


Para obtener más información, consulte:

- [Clúster de conmutación por error de Windows Server con SQL Server en máquinas virtuales de Azure](hadr-windows-server-failover-cluster-overview.md)
- [Grupos de disponibilidad Always On para SQL Server en Azure Virtual Machines](availability-group-overview.md)
- [Introducción a los grupos de disponibilidad Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
