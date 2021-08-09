---
title: Interoperabilidad de características con grupos de disponibilidad y cliente de escucha de DNN
description: 'Aprenda sobre las consideraciones adicionales que se requiere al trabajar con ciertas características de SQL Server y un cliente de escucha de nombre de red distribuida (DNN) con un grupos de disponibilidad Always On de SQL Server en máquinas virtuales de Azure. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: e7ff8eaaca03a2c977311c6469e06714c87ce53f
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572363"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>Interoperabilidad de características con grupos de disponibilidad y cliente de escucha de DNN 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Existen determinadas características de SQL Server que dependen de un nombre de red virtual (VNN) codificado de forma rígida. Por tanto, al usar el cliente de escucha de nombre de red distribuida (DNN) con su grupos de disponibilidad Always On y SQL Server en máquinas virtuales de Azure, puede haber algunas consideraciones adicionales. 

En este artículo se detallan las características de SQL Server y la interoperabilidad con el cliente de escucha de DNN del grupo de disponibilidad. 

## <a name="behavior-differences"></a>Diferencias de comportamiento

Hay algunas diferencias de comportamiento entre la funcionalidad del agente de escucha de VNN y el agente de escucha de DNN que son importantes para tenerlas en cuenta: 

- **Tiempo de conmutación por error**: el tiempo de conmutación por error es más rápido cuando se usa un agente de escucha de DNN, ya que no es necesario esperar a que el equilibrador de carga de red detecte el evento de error y cambie su enrutamiento. 
- **Conexiones existentes**: Las conexiones establecidas con una *base de datos específica* dentro de un grupo de disponibilidad de conmutación por error se cerrarán, pero otras conexiones a la réplica principal permanecerán abiertas, ya que el DNN permanece en línea durante el proceso de conmutación por error. Esto es diferente de un entorno VNN tradicional en el que todas las conexiones a la réplica principal normalmente se cierran cuando se conmuta por error el grupo de disponibilidad, el agente de escucha se queda sin conexión y la réplica principal cambia al rol secundario. Al usar un agente de escucha de DNN, es posible que tenga que ajustar las cadenas de conexión de la aplicación para asegurarse de que las conexiones se redirijan a la nueva réplica principal tras la conmutación por error.
- **Transacciones abiertas**: Las transacciones abiertas en una base de datos de un grupo de disponibilidad con conmutación por error se cerrarán y revertirán, y tendrá que volver a conectarse *manualmente*. Por ejemplo, en SQL Server Management Studio, cierre la ventana de consulta y abra otra nueva. 

## <a name="client-drivers"></a>Controladores cliente

Para los controladores ODBC, OLEDB, ADO.NET, JDBC, PHP y Node.js, los usuarios deben especificar explícitamente el puerto y el nombre del cliente de escucha de DNN como el nombre del servidor en la cadena de conexión. Para garantizar una conectividad rápida tras la conmutación por error, agregue `MultiSubnetFailover=True` a la cadena de conexión si el cliente SQL lo admite. 

## <a name="tools"></a>Herramientas

Los usuarios de [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is) y [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) deben especificar explícitamente el puerto y el nombre del cliente de escucha de DNN como el nombre del servidor en la cadena de conexión para conectarse con el cliente de escucha. 

Actualmente no se admite la creación del cliente de escucha de DNN a través de la GUI de SQL Server Management Studio (SSMS). 


## <a name="availability-groups-and-fci"></a>Grupos de disponibilidad y FCI

Puede configurar un grupo de disponibilidad Always On mediante una instancia de clúster de conmutación por error (FCI) como una de las réplicas. Para que esta configuración funcione con el cliente de escucha de DNN, la [instancia de clúster de conmutación por error también debe usar el DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md), ya que no hay forma de colocar la dirección IP virtual de la FCI en la lista de direcciones IP de DNN del grupo de disponibilidad. 

En esta configuración, la dirección URL del punto de conexión de creación de reflejo para la réplica de FCI debe utilizar el DNN de la FCI. Del mismo modo, si la FCI se usa como réplica de solo lectura, el enrutamiento de solo lectura a la réplica de FCI debe utilizar el DNN de la FCI. 

El formato del punto de conexión de creación de reflejo es: `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'`. 

Por ejemplo, si el nombre DNS de DNN de la FCI es `dnnlsnr` y `5022` es el puerto del punto de conexión de creación de reflejo de la FCI, el fragmento de código de Transact-SQL (T-SQL) para crear la dirección URL del punto de conexión tiene el aspecto siguiente: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Del mismo modo, el formato de la dirección URL de enrutamiento de solo lectura es: `TCP://<FCI DNN DNS name>:<SQL Server instance port>`. 

Por ejemplo, si el nombre DNS del DNN es `dnnlsnr` y `1444` es el puerto que usa la FCI de SQL Server de destino de solo lectura, el fragmento de código de T-SQL para crear la dirección URL de enrutamiento de solo lectura tiene el siguiente aspecto: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Puede omitir el puerto en la dirección URL si es el puerto predeterminado 1433. Para una instancia con nombre, configure un puerto estático para la instancia con nombre y especifíquelo en la dirección URL de enrutamiento de solo lectura.  

## <a name="distributed-availability-group"></a>Grupo de disponibilidad distribuido

Actualmente, los grupos de disponibilidad distribuidos no son compatibles con el cliente de escucha de DNN. 

## <a name="replication"></a>Replicación

La replicación transaccional, de mezcla y de instantáneas permiten reemplazar el cliente de escucha de VNN por el agente de escucha de DNN y el puerto en los objetos de replicación que se conectan al cliente de escucha. 

Para más información sobre cómo usar la replicación con los grupos de disponibilidad, consulte las secciones sobre [Publicador y grupos de disponibilidad](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server), [Suscriptor y grupos de disponibilidad](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server) y [Distribuidor y grupos de disponibilidad](/sql/relational-databases/replication/configure-distribution-availability-group).

## <a name="msdtc"></a>MSDTC

Se admite un Coordinador de transacciones distribuidas local y en clúster, pero el Coordinador de transacciones distribuidas usa un puerto dinámico que requiere una instancia de Azure Load Balancer estándar para configurar el puerto de alta disponibilidad. Como tal, la máquina virtual debe usar una reserva de IP estándar o no se puede exponer a Internet. 

Defina dos reglas, una para el puerto 135 del asignador de puntos de conexión de RPC y otro para el puerto del Coordinador de transacciones distribuidas real. Después de la conmutación por error, modifique la regla de LB al puerto nuevo del Coordinador de transacciones distribuidas después de que cambie en el nodo nuevo. 

Si el Coordinador de transacciones distribuidas es local, asegúrese de permitir la comunicación de salida. 

## <a name="distributed-query"></a>Consulta distribuida 

La consulta distribuida se basa en un servidor vinculado que se puede configurar mediante el uso del puerto y el cliente de escucha de DNN del grupo de disponibilidad. Si el puerto no es 1433, elija la opción **Usar otro origen de datos** en SQL Server Management Studio (SSMS) al configurar el servidor vinculado. 

## <a name="filestream"></a>Secuencia de archivos

La secuencia de archivos se admite, pero no en escenarios en los que los usuarios acceden al recurso compartido de archivos con ámbito mediante Windows File API. 

## <a name="filetable"></a>Tabla de archivos

La tabla de archivos se admite, pero no en escenarios en los que los usuarios acceden al recurso compartido de archivos con ámbito mediante Windows File API. 

## <a name="linked-servers"></a>Servidores vinculados

Configure el servidor vinculado con el puerto y el nombre del cliente de escucha de DNN del grupo de disponibilidad. Si el puerto no es 1433, elija la opción **Usar otro origen de datos** en SQL Server Management Studio (SSMS) al configurar el servidor vinculado. 


## <a name="frequently-asked-questions"></a>Preguntas más frecuentes


- ¿Qué versión de SQL Server incluye la compatibilidad con el cliente de escucha de DNN de grupo de disponibilidad? 

   SQL Server 2019 CU8 y versiones posteriores.

- ¿Qué tiempo de conmutación por error se espera cuando se usa el cliente de escucha de DNN?

   En el caso del cliente de escucha de DNN, el tiempo de conmutación por error será solo el tiempo de conmutación por error del grupo de disponibilidad, sin ningún tiempo adicional (como el tiempo de sondeo cuando se utiliza Azure Load Balancer).

- ¿Hay algún requisito de versión para que los clientes de SQL admitan DNN con OLEDB y ODBC?

   Se recomienda compatibilidad con la cadena de conexión `MultiSubnetFailover=True` para el cliente de escucha de DNN. Está disponible a partir de SQL Server 2012 (11.x).

- ¿Hay algún cambio de configuración de SQL Server que necesite para utilizar el cliente de escucha de DNN? 

   SQL Server no requiere ningún cambio en la configuración para utilizar el DNN, pero es posible que algunas características de SQL Server requieran más atención. 

- ¿Admite el DNN clústeres de varias subredes?

   Sí. El clúster enlaza el DNN en DNS con las direcciones IP físicas de todas las réplicas de la disponibilidad, independientemente de la subred. El cliente SQL prueba todas las direcciones IP del nombre DNS independientemente de la subred. 



## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte:

- [Grupos de disponibilidad Always On para SQL Server en Azure Virtual Machines](availability-group-overview.md)
- [Clúster de conmutación por error de Windows Server con SQL Server en máquinas virtuales de Azure](hadr-windows-server-failover-cluster-overview.md)
- [Introducción a los grupos de disponibilidad Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)
- [Configuración de alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](hadr-cluster-best-practices.md)

