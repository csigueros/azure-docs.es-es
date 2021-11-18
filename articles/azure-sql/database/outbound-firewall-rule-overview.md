---
title: Reglas del firewall de salida (versión preliminar)
description: Información general sobre la característica de reglas del firewall de salida.
author: rohitnayakmsft
ms.author: rohitna
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.service: sql-database
ms.subservice: security
ms.topic: conceptual
ms.reviewer: vanto
ms.date: 11/10/2021
ms.openlocfilehash: d69536d8ed72dbd3793dd2481897d4534dbc2fdd
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132349992"
---
# <a name="outbound-firewall-rules-for-azure-sql-database-and-azure-synapse-analytics-preview"></a>Reglas del firewall de salida para Azure SQL Database y Azure Synapse Analytics (versión preliminar)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa-formerly-sqldw.md)] 

Las reglas del firewall de salida limitan el tráfico de red desde el servidor lógico de Azure SQL a una lista definida por el usuario de cuentas de Azure Storage y servidores lógicos de Azure SQL. Se deniega cualquier intento de acceder a las cuentas de almacenamiento o instancias de SQL Database que no están en esta lista. Las siguientes características de Azure SQL Database admiten esta característica:

- [Auditoría](auditing-overview.md)
- [Evaluación de vulnerabilidades](sql-vulnerability-assessment.md)
- [Servicio de importación/exportación](database-import-export-azure-services-off.md)
- OPENROWSET
- Inserción masiva
- [Consulta elástica](elastic-query-overview.md)

> [!IMPORTANT]
> Este artículo se aplica tanto a Azure SQL Database como al [grupo de SQL dedicado (antes SQL DW)](../../synapse-analytics\sql-data-warehouse\sql-data-warehouse-overview-what-is.md) en Azure Synapse Analytics. Estos valores se aplican a todas las bases de datos de SQL Database y de grupo de SQL dedicado (anteriormente, SQL DW) asociadas al servidor. Para simplificar, el término "base de datos" hace referencia a las bases de datos de Azure SQL Database y a las de Azure Synapse Analytics. Del mismo modo, todas las referencias a "servidor" indican el [servidor de SQL Server lógico](logical-servers.md) que hospeda Azure SQL Database y el grupo de SQL dedicado (anteriormente SQL DW) en Azure Synapse Analytics. Este artículo *no* se aplica a Azure SQL Managed Instance o a grupos de SQL dedicados de áreas de trabajo de Azure Synapse Analytics.

## <a name="set-outbound-firewall-rules-in-the-azure-portal"></a>Establecimiento de reglas de firewall de salida en Azure Portal

1. Vaya a la sección **Outbound networking** (Redes de salida) en la hoja **Firewalls y redes virtuales** de su base de datos Azure SQL Database y seleccione **Configure outbound networking restrictions** (Configurar restricciones de red de salida).

   ![Captura de pantalla de la sección de redes de salida][1]  

   Se abrirá la hoja siguiente en el lado derecho:

   ![Captura de pantalla de la hoja de redes de salida sin nada seleccionado][2]  

1. Active la casilla titulada **Restrict outbound networking** (Restringir red de salida) y, a continuación, agregue el FQDN para las cuentas de almacenamiento (o instancias de SQL Database) con el botón **Agregar dominio**.

   ![Captura de pantalla de la hoja de redes de salida que muestra cómo agregar FQDN][3]  

1. Cuando haya terminado, debería ver una pantalla similar a la siguiente. Seleccione **Aceptar** para aplicar la configuración.

   ![Captura de pantalla de la hoja de redes de salida después de agregar FQDN][4]  

## <a name="set-outbound-firewall-rules-using-powershell"></a>Establecimiento de reglas de firewall de salida mediante PowerShell

> [!IMPORTANT]
> Azure SQL Database todavía es compatible con el módulo de Azure Resource Manager para PowerShell, pero todo el desarrollo futuro se realizará para el módulo Az.Sql. Para estos cmdlets, consulte [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Los argumentos para los comandos del módulo Az y los módulos AzureRm son esencialmente idénticos. El script siguiente requiere el [módulo de Azure PowerShell](/powershell/azure/install-az-ps).

El siguiente script de PowerShell muestra cómo cambiar la configuración de red de salida (mediante la propiedad **RestrictOutboundNetworkAccess**):

```powershell
# Get current settings for Outbound Networking
(Get-AzSqlServer -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName>).RestrictOutboundNetworkAccess

# Update setting for Outbound Networking
$SecureString = ConvertTo-SecureString "<ServerAdminPassword>" -AsPlainText -Force

Set-AzSqlServer -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -SqlAdministratorPassword $SecureString  -RestrictOutboundNetworkAccess "Enabled"
```

Use estos cmdlets de PowerShell para configurar reglas de firewall de salida.

```powershell
# List all Outbound Firewall Rules
Get-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName>

# Add an Outbound Firewall Rule
New-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN testOBFR1

# List a specific Outbound Firewall Rule
Get-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN <StorageAccountFQDN>

#Delete an Outbound Firewall Rule
Remove-AzSqlServerOutboundFirewallRule -ServerName <SqlServerName> -ResourceGroupName <ResourceGroupName> -AllowedFQDN <StorageAccountFQDN>
```

## <a name="set-outbound-firewall-rules-using-the-azure-cli"></a>Establecimiento de reglas de firewall de salida mediante la CLI de Azure

> [!IMPORTANT]
> Todos los scripts de esta sección requieren la [CLI de Azure](/cli/azure/install-azure-cli).

### <a name="azure-cli-in-a-bash-shell"></a>CLI de Azure en un shell de Bash

El siguiente script de la CLI muestra cómo cambiar la configuración de red de salida (mediante la propiedad **RestrictOutboundNetworkAccess**) en un shell de Bash:

```azurecli-interactive
# Get current setting for Outbound Networking 
az sql server show -n sql-server-name -g sql-server-group --query "RestrictOutboundNetworkAccess"

# Update setting for Outbound Networking
az sql server update -n sql-server-name -g sql-server-group --set RestrictOutboundNetworkAccess="Enabled"
```

Use estos comandos de la CLI para configurar reglas de firewall de salida.

```azurecli-interactive
# List a server's outbound firewall rules.
az sql server outbound-firewall-rule list -g sql-server-group -s sql-server-name

# Create a new outbound firewall rule
az sql server outbound-firewall-rule create -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN

# Show the details for an outbound firewall rule.
az sql server outbound-firewall-rule show -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN

# Delete the outbound firewall rule.
az sql server outbound-firewall-rule delete -g sql-server-group -s sql-server-name --outbound-rule-fqdn allowedFQDN
```

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general sobre la seguridad de Azure SQL Database, vea [Protección de bases de datos SQL](security-overview.md).
- Para obtener información general sobre la conectividad de Azure SQL Database, consulte [Arquitectura de conectividad de Azure SQL](connectivity-architecture.md)

<!--Image references-->
[1]: media/outbound-firewall-rules/Step1.jpg
[2]: media/outbound-firewall-rules/Step2.jpg
[3]: media/outbound-firewall-rules/Step3.jpg
[4]: media/outbound-firewall-rules/Step4.jpg
