---
title: Configuración de grupo de disponibilidad Always On cuando DH2i DxEnterprise se ejecuta en Azure Virtual Machines basado en Linux
description: Use DH2i DxEnterprise como administrador de clústeres para lograr alta disponibilidad con un grupo de disponibilidad en SQL Server en Azure Virtual Machines Linux
ms.date: 03/04/2021
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: tutorial
author: amvin87
ms.author: amitkh
ms.reviewer: vanto
ms.openlocfilehash: 8791d845b822f365e47482b0215f1ecac9fa0da9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732021"
---
# <a name="tutorial---setup-a-three-node-always-on-availability-group-with-dh2i-dxenterprise-running-on-linux-based-azure-virtual-machines"></a>Tutorial: Configuración de grupo de disponibilidad Always On de tres nodos cuando DH2i DxEnterprise se ejecuta en Azure Virtual Machines basado en Linux

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

En este tutorial se explica cómo configurar un grupo de disponibilidad Always On de SQL Server en el que DH2i DxEnterprise se ejecuta en Azure Virtual Machines basadas en Linux. 

Para más información sobre DxEnterprise, consulte [DH2i DxEnterprise](https://dh2i.com/dxenterprise-availability-groups/).

> [!NOTE]
> Microsoft admite el movimiento de datos, el grupo de disponibilidad y los componentes de SQL Server. Póngase en contacto con DH2i si necesita soporte técnico relacionado con la documentación del clúster de DH2i DxEnterprise para la administración del clúster y del cuórum.
 

Este tutorial se compone de los siguientes pasos:

> [!div class="checklist"]
> * Instalación de SQL Server en las máquinas virtuales de Azure que formarán parte del grupo de disponibilidad.
> * Instalación de DxEnterprise en todas las máquinas virtuales y configuración del clúster de DxEnterprise.
> * Creación de los hosts virtuales para proporcionar compatibilidad con la conmutación por error y alta disponibilidad, e incorporación de un grupo de disponibilidad y una base de datos al grupo de disponibilidad.
> * Creación de una instancia interna de Azure Load Balancer para el agente de escucha del grupo de disponibilidad (opcional).
> * Realización de una conmutación por error manual o automática.

En este tutorial, vamos a configurar un clúster de DxEnterprise mediante la [interfaz de usuario del cliente de DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/). El clúster también se puede configurar mediante la interfaz de línea de comandos de [DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/). En este ejemplo, hemos usado cuatro máquinas virtuales, y tres de ellas utilizan Ubuntu 18.04 y forman parte del clúster de tres nodos. La cuarta usa Windows 10 con la herramienta DxAdmin para administrar y configurar el clúster.

## <a name="prerequisites"></a>Requisitos previos

- Cree cuatro máquinas virtuales en Azure. Siga las indicaciones que se proporcionan en el artículo [Inicio rápido: Creación de una máquina virtual Linux en Azure Portal](../../../virtual-machines/linux/quick-create-portal.md) para crear máquinas virtuales basadas en Linux. Del mismo modo, para crear la máquina virtual basada en Windows, siga las directrices del artículo [Inicio rápido: Creación de una máquina virtual Windows en Azure Portal](../../../virtual-machines/windows/quick-create-portal.md).
- Instale .NET 3.1 en todas las máquinas virtuales basadas en Linux que van a formar parte del clúster. Siga las instrucciones que encontrará [aquí](/dotnet/core/install/linux) en función del sistema operativo Linux que elija.
- Se necesitará una licencia de DxEnterprise válida con las características de administración de grupos de disponibilidad habilitadas. Para más información, consulte en la página de la [evaluación gratuita de DxEnterprise](https://dh2i.com/trial/) cómo obtener una evaluación gratuita.

## <a name="install-sql-server-on-all-the-azure-vms-that-will-be-part-of-the-availability-group"></a>Instalación de SQL Server en todas las máquinas virtuales de Azure que formarán parte del grupo de disponibilidad

En este tutorial, vamos a configurar un clúster basado en Linux de tres nodos en el que se ejecuta el grupo de disponibilidad. Consulte la documentación relativa a la [instalación de SQL Server en Linux](/sql/linux/sql-server-linux-overview#install) de la plataforma Linux que prefiera. Para completar este tutorial también es aconsejable instalar las [herramientas de SQL Server](/sql/linux/sql-server-linux-setup-tools).
 
> [!NOTE]
> Asegúrese de que el sistema operativo Linux que elija sea una distribución común compatible tanto con [DH2i DxEnterprise (consulte la sección de requisitos mínimos del sistema)](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) como con [Microsoft SQL Server](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms).
>
> En este ejemplo, se va a usar Ubuntu 18.04, que es compatible con DH2i DxEnterprise y Microsoft SQL Server.

Para este tutorial, no se va a instalar SQL Server en la máquina virtual Windows, ya que este nodo no va a formar parte del clúster y solo se usa para administrar el clúster mediante DxAdmin.

Después de completar este paso, tanto SQL Server como las [herramientas de SQL Server](/sql/linux/sql-server-linux-setup-tools) (opcionalmente) deberían estar instalados en las tres máquinas virtuales Linux que van a participar en el grupo de disponibilidad.
 
## <a name="install-dxenterprise-on-all-the-vms-and-configure-the-cluster"></a>Instalación de DxEnterprise en todas las máquinas virtuales y configuración del clúster

En este paso, se va a instalar DH2i DxEnterprise para Linux en las tres máquinas virtuales Linux. En la tabla siguiente se describe el rol que desempeña cada servidor en el clúster:

| Número de VM | Rol de DH2i DxEnterprise | Rol de réplica de grupo de disponibilidad de Microsoft SQL Server |
|--|--|--|
| 1 | Nodo de clúster: basado en Linux | Principal |
| 1 | Nodo de clúster: basado en Linux | Secundario: confirmación sincrónica |
| 1 | Nodo de clúster: basado en Linux | Secundario: confirmación sincrónica |
| 1 | Cliente de DxAdmin | N/D |


Para instalar DxEnterprise en los tres nodos basados en Linux, siga la documentación de DH2i DxEnterprise para el sistema operativo Linux que elija. Utilice cualquiera de los métodos que se enumeran a continuación para instalar DxEnterprise.

- **Ubuntu**
    - [Guía de inicio rápido para la instalación del repositorio](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-ubuntu-installation-quick-start-guide/)
    - [Guía de inicio rápido de la extensión](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Guía de inicio rápido de la imagen de Marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)
- **RHEL**
    - [Guía de inicio rápido para la instalación del repositorio](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-rhel-centos-installation-quick-start-guide/)
    - [Guía de inicio rápido de la extensión](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-extension-quick-start-guide/)
    - [Guía de inicio rápido de la imagen de Marketplace](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-marketplace-image-for-linux-quick-start-guide/)

Para instalar solo la herramienta de cliente DxAdmin en la máquina virtual Windows, consulte la [guía de inicio rápido de la interfaz de usuario del cliente de DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-dxadmin-client-ui-quick-start-guide/).

Después de este paso, el clúster de DxEnterprise se debe haber creado en las máquinas virtuales Linux y el cliente DxAdmin debe estar instalado en la máquina cliente de Windows. 

> [!NOTE]
> También puede crear un clúster de tres nodos, en el que uno de ellos se agrega como *modo de solo configuración*, como se describe [aquí](/sql/database-engine/availability-groups/windows/availability-modes-always-on-availability-groups#SupportedAvModes) para habilitar la conmutación por error automática. 

## <a name="create-the-virtual-hosts-to-provide-failover-support-and-high-availability"></a>Creación de los hosts virtuales para proporcionar compatibilidad con la conmutación por error y alta disponibilidad

En este paso, se va a crear un host virtual, un grupo de disponibilidad y, luego, se va a agregar una base de datos, todo ello desde la interfaz de usuario de DxAdmin.   

> [!NOTE]
> En este paso también se reiniciarán las instancias de SQL Server para habilitar Always On. 

Conéctese a la máquina cliente de Windows que ejecuta DxAdmin para conectarse al clúster creado en el paso anterior. Siga los pasos que encontrará en el artículo sobre [grupos de disponibilidad de MSSQL con DxAdmin](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-mssql-availability-groups-with-dxadmin-quick-start-guide/) para habilitar Always On y crear tanto el host virtual como el grupo de disponibilidad. 

> [!TIP]
> Antes de agregar las bases de datos, asegúrese de que se ha creado la base de datos y de que se ha realizado una copia de seguridad de la misma en la instancia principal de SQL Server.  

## <a name="create-the-internal-azure-load-balancer-for-listener-optional"></a>Creación de una instancia interna de Azure Load Balancer para el agente de escucha (opcional)

En este paso opcional, se puede crear y configurar una instancia de Azure Load Balancer que contiene las direcciones IP de los agentes de escucha del grupo de disponibilidad. Para más información sobre Azure Load Balancer, consulte [¿Qué es Azure Load Balancer?](../../../load-balancer/load-balancer-overview.md) Para configurar Azure Load Balancer y el agente de escucha del grupo de disponibilidad mediante DxAdmin, siga las indicaciones de la [guía de inicio rápido de Azure Load Balancer](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-0-software-azure-load-balancer-quick-start-guide/)de DxEnterprise.

Después de este paso, se debe tener un agente de escucha del grupo de disponibilidad creado y asignado a Azure Load Balancer.

## <a name="test-manual-or-automatic-failover"></a>Prueba de la conmutación por error manual o automática

Para la prueba de conmutación por error automática, puede desactivar la réplica principal (apagar la máquina virtual desde Azure Portal). De esta forma se replica la falta de disponibilidad repentina del nodo principal. Éste es el comportamiento esperado:
- El administrador de clústeres promueve a principal una de las réplicas secundarias del grupo de disponibilidad.
- La réplica principal con error se combina automáticamente con el clúster después de que se realice la copia de seguridad. El administrador de clústeres la promueve a una réplica secundaria.

 
También puede realizar una conmutación por error manual. Para ello, debe seguir los pasos que se indican a continuación:

1. Conéctese al clúster a través de DxAdmin.   
1. Expanda el host virtual para el grupo de disponibilidad.
1. Haga clic con el botón derecho en el nodo de destino o la réplica secundaria, y seleccione **Start Hosting on Member** (Iniciar hospedaje en el miembro) para iniciar la conmutación por error. 

Para obtener información sobre otras operaciones en DxEnterprise, acceda a la [guía de administración de DxEnterprise](https://dh2i.com/wp-content/uploads/DxEnterprise-v20-Admin-Guide.pdf) y a la [guía de DxEnterprise DxCLI](https://dh2i.com/docs/20-0/dxenterprise/dh2i-dxenterprise-20-software-dxcli-guide/)

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre los [grupos de disponibilidad en Linux](/sql/linux/sql-server-linux-availability-group-overview)
- [Inicio rápido: Creación de una máquina virtual Linux en Azure Portal](../../../virtual-machines/linux/quick-create-portal.md)
- [Inicio rápido: Creación de una máquina virtual de Windows en Azure Portal](../../../virtual-machines/windows/quick-create-portal.md)
- [Plataformas compatibles con SQL Server 2019 en Linux](/sql/linux/sql-server-linux-release-notes-2019#supported-platforms)