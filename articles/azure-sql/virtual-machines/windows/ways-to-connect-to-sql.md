---
title: Conexión a una máquina virtual de SQL Server (Resource Manager) | Microsoft Docs
description: Obtenga información acerca de cómo conectarse a la máquina virtual de SQL Server en Azure. En este tema se usa el modelo de implementación clásica. Los escenarios varían según la configuración de red y la ubicación del cliente.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 11764fffd3323efed9ddb0aebba05cb68076244a
ms.sourcegitcommit: beff1803eeb28b60482560eee8967122653bc19c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113435369"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Conexión a una máquina virtual de SQL Server en Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Información general

En este tema se describe cómo conectarse a SQL en una máquina virtual (VM) de Azure. Trata algunos [escenarios de conectividad general](#connection-scenarios) y proporciona los [pasos en el portal para cambiar la configuración de conectividad](#change). Si necesita solucionar problemas o configurar la conectividad fuera del portal, consulte la [configuración manual](#manual) al final de este tema. 

Si prefiere tener un tutorial completo sobre aprovisionamiento y conectividad, vea [Aprovisionamiento de una máquina virtual de SQL Server en Azure](create-sql-vm-portal.md).

## <a name="connection-scenarios"></a>Escenarios de conexión

La forma en la que un cliente se conecta a una VM con SQL Server varía en función de la ubicación del cliente y la configuración de la red.

Si se aprovisiona una VM con SQL Server en Azure Portal, tiene la opción de especificar el tipo de **conectividad SQL**.

![Opción de conectividad SQL pública durante el aprovisionamiento](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

Estas son algunas de las opciones de conectividad:

| Opción | Descripción |
|---|---|
| **Pública** | Conexión a SQL Server a través de Internet. |
| **Privada** | Conexión a SQL Server en la misma red virtual. |
| **Local** | Conexión a una instancia local de SQL Server en la misma máquina virtual. | 

En las siguientes secciones se explican las opciones **Pública** y **Privada** con más detalle.

## <a name="connect-to-sql-server-over-the-internet"></a>Conexión a SQL Server a través de Internet

Si quiere conectarse al motor de base de datos de SQL Server desde Internet, en el portal, seleccione **Pública** para el tipo de **conectividad SQL** durante el aprovisionamiento. El portal realiza automáticamente los siguientes pasos:

* Habilita el protocolo TCP/IP para SQL Server.
* Configura una regla de firewall para abrir el puerto TCP de SQL Server (predeterminado: 1433).
* Habilita la autenticación de SQL Server, que es necesaria para el acceso público.
* Configura el grupo de seguridad de red en la máquina virtual para todo el tráfico TCP del puerto de SQL Server.

> [!IMPORTANT]
> Las imágenes de máquina virtual de SQL Server Developer Edition y SQL Server Express Edition no habilitan automáticamente el protocolo TCP/IP. Para estas dos ediciones, debe usar el Administrador de configuración de SQL Server con el fin de [habilitar manualmente el protocolo TCP/IP](#manualtcp) después de crear la máquina virtual.

Cualquier cliente con acceso a Internet podrá conectarse a la instancia de SQL Server especificando la dirección IP pública de la máquina virtual o la etiqueta DNS asignada a esa dirección IP. Si el puerto de SQL Server es 1433, no es necesario que lo especifique en la cadena de conexión. La cadena de conexión siguiente se conecta a una VM con SQL con una etiqueta DNS de `sqlvmlabel.eastus.cloudapp.azure.com` mediante la autenticación de SQL (también puede usar la IP pública).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

Aunque esta cadena permite a los clientes conectarse a través de Internet, esto no implica que cualquier usuario pueda conectarse a la instancia de SQL Server. Los clientes externos necesitan usar el nombre de usuario y la contraseña correctos. Sin embargo, para obtener más seguridad, puede evitar utilizar el conocido puerto 1433. Por ejemplo, si fuera a configurar SQL Server para escuchar en el puerto 1500 y establecer reglas adecuadas de firewall y de grupo de seguridad de red, podría conectarse si anexa el número de puerto al nombre del servidor. En el ejemplo siguiente se modifica lo anterior agregando un número de puerto personalizado, **1500**, al nombre del servidor:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Cuando se realizan consultas de SQL Server en una máquina virtual a través de Internet, todos los datos salientes del centro de datos de Azure están sujetos a [precios de transferencia de datos salientes](https://azure.microsoft.com/pricing/details/data-transfers/) normales.

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Conexión a SQL Server en una red virtual

Al elegir **Privada** para el tipo de **conectividad SQL** en el portal, Azure configura la mayoría de los valores para que sean idénticos a los de **Público**. La única diferencia es que no hay ninguna regla de grupo de seguridad de red para permitir el tráfico externo en el puerto de SQL Server (predeterminado: 1433).

> [!IMPORTANT]
> Las imágenes de máquina virtual de SQL Server Developer Edition y SQL Server Express Edition no habilitan automáticamente el protocolo TCP/IP. Para estas dos ediciones, debe usar el Administrador de configuración de SQL Server con el fin de [habilitar manualmente el protocolo TCP/IP](#manualtcp) después de crear la máquina virtual.

La conectividad privada se suele utilizar junto con una [red virtual](../../../virtual-network/virtual-networks-overview.md), lo que permite varios escenarios. Puede conectar las máquinas virtuales en la misma red virtual, incluso si esas máquinas virtuales existen en distintos grupos de recursos. Asimismo, con una [VPN de sitio a sitio](../../../vpn-gateway/tutorial-site-to-site-portal.md), puede crear una arquitectura híbrida que conecta las máquinas virtuales con redes y máquinas locales.

Las redes virtuales también permiten unir las máquinas virtuales de Azure a un dominio. Esta es la única forma de usar la autenticación de Windows para SQL Server. Los demás escenarios de conexión requieren la autenticación de SQL con nombres de usuario y contraseñas.

Si configuró DNS en la red virtual, puede conectarse a la instancia de SQL Server si especifica el nombre de equipo de la máquina virtual de SQL Server en la cadena de conexión. En el siguiente ejemplo, también se da por hecho que se ha configurado la autenticación de Windows y que se ha concedido al usuario acceso a la instancia de SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> Cambio de la configuración de conectividad SQL

Puede cambiar la configuración de conectividad de la máquina virtual de SQL Server en Azure Portal.

1. En Azure Portal, seleccione **Máquinas virtuales SQL**.

2. Seleccione la VM con SQL Server.

3. En **Configuración**, seleccione **Seguridad**.

4. Cambie el **nivel de conectividad SQL** a la configuración necesaria. De forma opcional, también se puede usar esta área para cambiar el puerto de SQL Server o la configuración de autenticación de SQL.

   ![Cambio de conectividad de SQL](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Espere unos minutos hasta que se complete la actualización.

   ![Notificación de actualización de la VM con SQL](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> Habilitación de TCP/IP para las ediciones Developer y Express

Al cambiar la configuración de conectividad de SQL Server, Azure no habilita automáticamente el protocolo TCP/IP para las ediciones Developer y Express de SQL Server. En los pasos siguientes se explica cómo habilitar manualmente TCP/IP para que pueda conectarse de forma remota mediante una dirección IP.

En primer lugar, conéctese a la máquina virtual SQL Server con Escritorio remoto.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Después, habilite el protocolo TCP/IP con el **Administrador de configuración de SQL Server**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Conectarse con SSMS

En los pasos siguientes se muestra cómo crear una etiqueta opcional de DNS para la máquina virtual de Azure y, luego, conectarla con SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> Configuración manual y solución de problemas

Aunque el portal proporciona opciones para configurar automáticamente la conectividad, es útil saber cómo configurarla manualmente. Comprender los requisitos también puede ayudar a solucionar problemas.

En la tabla siguiente se enumeran los requisitos para conectarse a SQL Server en la máquina virtual de Azure.

| Requisito | Descripción |
|---|---|
| [Habilitación del modo de autenticación de SQL Server](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | Para la conexión remota a la máquina virtual se necesita autenticación de SQL Server, a menos que se haya configurado Active Directory en una red virtual. |
| [Creación de un inicio de sesión de SQL](/sql/relational-databases/security/authentication-access/create-a-login) | Si usa la autenticación de SQL, necesita un inicio de sesión de SQL con nombre de usuario y contraseña que también tenga permisos para la base de datos de destino. |
| [Habilitación del protocolo TCP/IP](#manualtcp) | SQL Server debe permitir conexiones a través de TCP. |
| [Habilitación de la regla de firewall para el puerto de SQL Server](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | El firewall de la máquina virtual debe permitir el tráfico entrante en el puerto de SQL Server (predeterminado: 1433). |
| [Creación de una regla del grupo de seguridad de red para el puerto 1433 de TCP](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Se debe permitir que la máquina virtual reciba tráfico en el puerto de SQL Server (predeterminado: 1433) si desea conectarse a través de internet. Las conexiones locales y las exclusivas de la red virtual no lo necesitan. Este es el único paso necesario en Azure Portal. |

> [!TIP]
> Los pasos descritos en la tabla anterior se realizan automáticamente al configurar la conectividad en el portal. Solo debe seguir estos pasos para confirmar la configuración o configurar manualmente la conectividad de SQL Server.

## <a name="next-steps"></a>Pasos siguientes

Para ver las instrucciones de aprovisionamiento junto con estos pasos de conectividad, vea [Aprovisionamiento de una máquina virtual de SQL Server en Azure](create-sql-vm-portal.md).

Para consultar otros temas relacionados con la ejecución de SQL Server en máquinas virtuales de Azure, vea [SQL Server en máquinas virtuales de Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).