---
title: Configuración de un cliente de escucha de DNN para un grupo de disponibilidad
description: Obtenga información sobre cómo configurar un cliente de escucha de nombre de red distribuida (DNN) para reemplazar el cliente de escucha del nombre de red virtual (VNN) y enrutar el tráfico al grupo de disponibilidad Always On en VM con SQL Server en Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: jroth
tags: azure-resource-manager
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/07/2020
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: d7d637a98a0f250936fa8c9024ae1302d3aaa536
ms.sourcegitcommit: ff1aa951f5d81381811246ac2380bcddc7e0c2b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/07/2021
ms.locfileid: "111572376"
---
# <a name="configure-a-dnn-listener-for-an-availability-group"></a>Configuración de un cliente de escucha de DNN para un grupo de disponibilidad
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Con VM con SQL Server en Azure, el nombre de red distribuida (DNN) enruta el tráfico al recurso en clústeres adecuado. Proporciona una manera más fácil de conectarse a un grupos de disponibilidad (AG) Always On que el cliente de escucha de nombre de red virtual (VNN), sin necesidad de una instancia de Azure Load Balancer.

En este artículo aprenderá a configurar un cliente de escucha de DNN para reemplazar al cliente de escucha de VNN, y a enrutar el tráfico a un grupo de disponibilidad con VM con SQL Server en Azure para lograr una alta disponibilidad y recuperación ante desastres (HADR).

La característica del cliente de escucha de DNN actualmente solo está disponible a partir de SQL Server 2019 CU8 en Windows Server 2016 y versiones posteriores.

En el caso de una opción de conectividad alternativa, considere la posibilidad de usar un [cliente de escucha de VNN y Azure Load Balancer](availability-group-vnn-azure-load-balancer-configure.md) en su lugar.

## <a name="overview"></a>Información general

Un cliente de escucha de nombre de red distribuida (DNN) reemplaza al cliente de escucha de grupo de disponibilidad de nombre de red virtual (VNN) tradicional cuando se usa con [grupos de disponibilidad Always On en VM con SQL Server](availability-group-overview.md). Esto evita la necesidad de que una instancia de Azure Load Balancer enrute el tráfico, lo que simplifica la implementación y el mantenimiento, y mejora la conmutación por error.

Use el cliente de escucha de DNN para reemplazar un cliente de escucha de VNN existente o, como alternativa, úselo junto con un agente de escucha de VNN existente para que el grupo de disponibilidad tenga dos puntos de conexión distintos: uno que use el nombre del cliente de escucha de VNN (y el puerto si no es el predeterminado), y otro que use el nombre y el puerto del cliente de escucha de DNN.

> [!CAUTION]
> El comportamiento de enrutamiento cuando se usa un DNN es distinto de cuando se usa un VNN. No use el puerto 1433. Para más información, consulte la sección [Consideraciones sobre los puertos](#port-considerations) más adelante en este artículo.

## <a name="prerequisites"></a>Requisitos previos

Antes de completar los pasos de este artículo, ya debe tener:

- SQL Server 2019 en CU8 o posterior, en Windows Server 2016 y versiones posteriores
- Decidió que el nombre de red distribuida es la [opción de conectividad adecuada para su solución HADR](hadr-cluster-best-practices.md#connectivity).
- Configurado el [grupo de disponibilidad Always On](availability-group-overview.md). 
- Instaló la versión más reciente de [Azure PowerShell](/powershell/azure/install-az-ps). 
- Identificó el puerto único que usará con el cliente de escucha de DNN. El puerto usado con un cliente de escucha de DNN debe ser único en todas las réplicas del grupo de disponibilidad o de la instancia de clúster de conmutación por error.  Ninguna otra conexión puede compartir el mismo puerto.
- El cliente que se conecta al agente de escucha de DNN debe admitir el parámetro `MultiSubnetFailover=True` en la cadena de conexión. 



## <a name="create-script"></a>Creación del script

Use PowerShell para crear el recurso de nombre de red distribuida (DNN) y asociarlo con el grupo de disponibilidad.

Para ello, siga estos pasos:

1. Abra un editor de texto, como el Bloc de notas.
1. Copie y pegue el siguiente script:

   ```powershell
   param (
      [Parameter(Mandatory=$true)][string]$Ag,
      [Parameter(Mandatory=$true)][string]$Dns,
      [Parameter(Mandatory=$true)][string]$Port
   )
   
   Write-Host "Add a DNN listener for availability group $Ag with DNS name $Dns and port $Port"
   
   $ErrorActionPreference = "Stop"
   
   # create the DNN resource with the port as the resource name
   Add-ClusterResource -Name $Port -ResourceType "Distributed Network Name" -Group $Ag 
   
   # set the DNS name of the DNN resource
   Get-ClusterResource -Name $Port | Set-ClusterParameter -Name DnsName -Value $Dns 
   
   # start the DNN resource
   Start-ClusterResource -Name $Port
   
   
   $Dep = Get-ClusterResourceDependency -Resource $Ag
   if ( $Dep.DependencyExpression -match '\s*\((.*)\)\s*' )
   {
   $DepStr = "$($Matches.1) or [$Port]"
   }
   else
   {
   $DepStr = "[$Port]"
   }
   
   Write-Host "$DepStr"
   
   # add the Dependency from availability group resource to the DNN resource
   Set-ClusterResourceDependency -Resource $Ag -Dependency "$DepStr"
   
   
   #bounce the AG resource
   Stop-ClusterResource -Name $Ag
   Start-ClusterResource -Name $Ag
   ```

1. Guarde el script como archivo `.ps1`, por ejemplo, `add_dnn_listener.ps1`.

## <a name="execute-script"></a>Ejecución del script

Para crear el cliente de escucha de DNN, ejecute el script pasando los parámetros para el nombre del grupo de disponibilidad, el nombre del cliente de escucha y el puerto.

Por ejemplo, suponiendo que el nombre de un grupo de disponibilidad sea `ag1`, el nombre del agente de escucha sea `dnnlsnr`, y el puerto del cliente de escucha sea `6789`, siga estos pasos:

1. Abra una herramienta de la interfaz de la línea de comandos, como el símbolo del sistema o PowerShell.
1. Vaya a la ubicación en la que guardó el script `.ps1`, como C:\Documentos.
1. Ejecute el script: ```add_dnn_listener.ps1 <ag name> <listener-name> <listener port>```. Por ejemplo:

   ```console
   c:\Documents> add_dnn_listener.ps1 ag1 dnnlsnr 6789
   ```

## <a name="verify-listener"></a>Comprobación del cliente de escucha

Use SQL Server Management Studio o Transact-SQL para confirmar que el cliente de escucha de DNN se haya creado correctamente.

### <a name="sql-server-management-studio"></a>SQL Server Management Studio

Expanda **Agentes de escucha del grupo de disponibilidad** en [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) para ver el cliente de escucha de DNN:

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-in-ssms.png" alt-text="Vea el cliente de escucha de DNN en Agentes de escucha del grupo de disponibilidad en SQL Server Management Studio (SSMS)":::

### <a name="transact-sql"></a>Transact-SQL

Use Transact-SQL para ver el estado del cliente de escucha de DNN:

```sql
SELECT * FROM SYS.AVAILABILITY_GROUP_LISTENERS
```

Un valor de `1` para `is_distributed_network_name` indica que el cliente de escucha es un cliente de escucha de nombre de red distribuida (DNN):

:::image type="content" source="media/availability-group-distributed-network-name-dnn-listener-configure/dnn-listener-tsql.png" alt-text="Use sys.availability_group_listeners para identificar los clientes de escucha de DNN que tienen un valor de 1 en is_distributed_network_name":::

## <a name="update-connection-string"></a>Actualización de la cadena de conexión

Actualice las cadenas de conexión de las aplicaciones para que se conecten al cliente de escucha de DNN. Las cadenas de conexión de los clientes de escucha de DNN deben proporcionar el número de puerto de DNN. Para garantizar una conectividad rápida tras la conmutación por error, agregue `MultiSubnetFailover=True` a la cadena de conexión si el cliente SQL lo admite.

## <a name="test-failover"></a>Conmutación por error de prueba

Pruebe la conmutación por error del grupo de disponibilidad para asegurar la funcionalidad.

Para probar la conmutación por error, siga estos pasos:

1. Conéctese al cliente de escucha de DNN o a una de las réplicas mediante [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).
1. Expanda **Always On Availability Group** (Grupo de disponibilidad Always On) en el **Explorador de objetos**.
1. Haga clic con el botón derecho en el grupo de disponibilidad y elija **Conmutación por error** para abrir el **Asistente para la conmutación por error**.
1. Siga las notificaciones para elegir un destino de conmutación por error y conmutar por error el grupo de disponibilidad en una réplica secundaria.
1. Confirme que la base de datos se encuentre en un estado sincronizado en la nueva réplica principal.
1. (Opcional) Conmute por recuperación en la réplica principal o en otra réplica secundaria.

## <a name="test-connectivity"></a>Comprobación de la conectividad

Pruebe la conectividad con el cliente de escucha de DNN con estos pasos:

1. Abra [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
1. Conéctese al cliente de escucha de DNN.
1. Abra una nueva ventana de consulta y compruebe a qué réplica está conectado mediante la ejecución de `SELECT @@SERVERNAME`.
1. Conmute por error el grupo de disponibilidad a otra réplica.
1. Después de una cantidad de tiempo razonable, ejecute `SELECT @@SERVERNAME` para confirmar que el grupo de disponibilidad se hospeda ahora en otra réplica.

## <a name="limitations"></a>Limitaciones

- Actualmente, un cliente de escucha de DNN para un grupo de disponibilidad solo se admite para SQL Server 2019 CU8 y versiones posteriores en Windows Server 2016 y versiones posteriores. 
- Los clientes de escucha de DNN **SE DEBEN** configurar con un puerto único.  No se puede compartir el puerto con ninguna otra conexión en ninguna réplica.
- Puede haber consideraciones adicionales al trabajar con otras características de SQL Server y un grupo de disponibilidad con un DNN. Para obtener más información, consulte [Interoperabilidad de AG con DNN](availability-group-dnn-interoperability.md). 

## <a name="port-considerations"></a>Consideraciones sobre los puertos

Los clientes de escucha de DNN están diseñados para escuchar en todas las direcciones IP, pero en un puerto único específico. La entrada DNS para el nombre del cliente de escucha debe resolverse en las direcciones de todas las réplicas del grupo de disponibilidad. Esta acción se realiza automáticamente con el script de PowerShell que se proporciona en la sección [Creación de un script](#create-script). Dado que los clientes de escucha de DNN aceptan conexiones en todas las direcciones IP, es fundamental que el puerto de escucha sea único y que no esté en uso por ninguna otra réplica del grupo de disponibilidad. Puesto que SQL Server siempre escucha en el puerto 1433, ya sea directamente o a través del servicio SQL Browser, este puerto no se puede usar con ningún cliente de escucha de DNN.

## <a name="next-steps"></a>Pasos siguientes

Una vez que se haya implementado el grupo de disponibilidad, puede optimizar la [configuración de alta disponibilidad y recuperación ante desastres para SQL Server en máquinas virtuales de Azure](hadr-cluster-best-practices.md). 


Para obtener más información, consulte:

- [Clúster de conmutación por error de Windows Server con SQL Server en máquinas virtuales de Azure](hadr-windows-server-failover-cluster-overview.md)
- [Grupos de disponibilidad Always On para SQL Server en Azure Virtual Machines](availability-group-overview.md)
- [Introducción a los grupos de disponibilidad Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

