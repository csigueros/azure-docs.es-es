---
title: Solución de problemas de SQL Insights (versión preliminar)
description: Obtenga información sobre cómo solucionar problemas de SQL Insights en Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/04/2021
ms.openlocfilehash: b76e08103b7af7591e7f71d3fe40e1e018a45665
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610662"
---
# <a name="troubleshoot-sql-insights-preview"></a>Solución de problemas de SQL Insights (versión preliminar)
Para solucionar los problemas de recopilación de datos en SQL Insights, compruebe el estado de la máquina de supervisión en la pestaña **Administrar perfil**. Los estados que verá son los siguientes:

- **Recopilando** 
- **No se está recopilando** 
- **Recopilando con errores** 
 
Seleccione el estado para ver los registros y más detalles que puedan ayudarle a resolver el problema. 

:::image type="content" source="media/sql-insights-enable/monitoring-machine-status.png" alt-text="Captura de pantalla que muestra un estado de la máquina de supervisión.":::

## <a name="status-not-collecting"></a>Estado: no se está recopilando 
La máquina de supervisión muestra el estado **No se está recopilando** si no hay ningún dato en *InsightsMetrics* para SQL en los últimos 10 minutos. 

> [!NOTE]
> Compruebe que está recopilando los datos de una [versión compatible de SQL](sql-insights-overview.md#supported-versions). Por ejemplo, si intenta recopilar datos con un perfil y una cadena de conexión válidos, pero desde una versión no admitida de Azure SQL Database dará como resultado un estado de tipo **No está recopilando**.

SQL Insights usa la siguiente consulta para recuperar esta información:

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(10m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

Compruebe si algún registro de Telegraf le permite identificar la causa raíz del problema. Si hay entradas de registro, puede hacer clic en **No se está recopilando** y consultar los registros y la información para solucionar problemas comunes. 

Si no hay entradas de registro, debe comprobar los registros de la máquina virtual de supervisión para buscar los siguientes servicios que hayan instalado dos extensiones de máquina virtual:

- Microsoft.Azure.Monitor.AzureMonitorLinuxAgent 
  - Servicio: mdsd 
- Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension 
  - Servicio: wli 
  - Servicio: ms-telegraf 
  - Servicio: td-agent-bit-wli 
  - Registro de extensión para comprobar los errores de instalación: /var/log/azure/Microsoft.Azure.Monitor.Workloads.Workload.WLILinuxExtension/wlilogs.log 

### <a name="wli-service-logs"></a>Registros del servicio wli 

Registros del servicio: `/var/log/wli.log`

Para ver los registros recientes: `tail -n 100 -f /var/log/wli.log`
 
Si ve el siguiente registro de errores, indica que hay un problema con el servicio mdsd.

```
2021-01-27T06:09:28Z [Error] Failed to get config data. Error message: dial unix /var/run/mdsd/default_fluent.socket: connect: no such file or directory 
```

### <a name="telegraf-service-logs"></a>Registros del servicio telegraf 

Registros del servicio: `/var/log/ms-telegraf/telegraf.log`

Para ver los registros recientes: `tail -n 100 -f /var/log/ms-telegraf/telegraf.log`

Para ver los registros de errores y las advertencias recientes: `tail -n 1000 /var/log/ms-telegraf/telegraf.log | grep "E\!\|W!"`

La configuración que usa Telegraf la genera el servicio wli y se ubica en: `/etc/ms-telegraf/telegraf.d/wli`
 
Si se genera una configuración incorrecta, es posible que no se inicie el servicio ms-telegraf. Compruebe si el servicio ms-telegraf se está ejecutando con el comando: `service ms-telegraf status`.

Para ver los mensajes de error del servicio Telegraf, ejecútelo manualmente con el siguiente comando: 

```
/usr/bin/ms-telegraf --config /etc/ms-telegraf/telegraf.conf --config-directory /etc/ms-telegraf/telegraf.d/wli --test 
```

### <a name="mdsd-service-logs"></a>Registros del servicio mdsd 

Consulte [Requisitos previos](../agents/azure-monitor-agent-install.md#prerequisites) para el Agente de Azure Monitor. 


Registros del servicio:  
- `/var/log/mdsd.err`
- `/var/log/mdsd.warn`
- `/var/log/mdsd.info`

Para ver los errores recientes: `tail -n 100 -f /var/log/mdsd.err`

Recopile la información siguiente si necesita ponerse en contacto con nuestro personal de soporte técnico: 

- Registros en `/var/log/azure/Microsoft.Azure.Monitor.AzureMonitorLinuxAgent/` 
- Registro en `/var/log/waagent.log` 
- Registros en `/var/log/mdsd*`
- Archivos en `/etc/mdsd.d/`
- File `/etc/default/mdsd`

### <a name="invalid-monitoring-virtual-machine-configuration"></a>Configuración de máquina virtual de supervisión no válida

Una de las causas del estado **No está recopilando** es una configuración no válida para la máquina virtual de supervisión. Esta es la configuración predeterminada:

```json
{
    "version": 1,
    "secrets": {
        "telegrafPassword": {
            "keyvault": "https://mykeyvault.vault.azure.net/",
            "name": "sqlPassword"
        }
    },
    "parameters": {
        "sqlAzureConnections": [
            "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=telegraf;Password=$telegrafPassword;"
        ],
        "sqlVmConnections": [
        ],
        "sqlManagedInstanceConnections": [
        ]
    }
}
```

Esta configuración especifica los tokens de reemplazo que se usarán en la configuración del perfil en la máquina virtual de supervisión. También le permite hacer referencia a secretos desde Azure Key Vault y evitar así tener valores de secreto en ninguna configuración (esto se recomienda encarecidamente).

#### <a name="secrets"></a>Secretos
Los secretos son tokens cuyos valores se recuperan en tiempo de ejecución desde Azure Key Vault. Un secreto se define mediante un par compuesto por una referencia de Key Vault y un nombre de secreto. Esta definición permite a Azure Monitor obtener el valor dinámico del secreto y usarlo como referencias de configuración de bajada.

Puede definir tantos secretos como sean necesarios en la configuración, incluidos los secretos guardados en almacenes independientes de Key Vault.

```json
   "secrets": {
        "<secret-token-name-1>": {
            "keyvault": "<key-vault-uri>",
            "name": "<key-vault-secret-name>"
        },
        "<secret-token-name-2>": {
            "keyvault": "<key-vault-uri-2>",
            "name": "<key-vault-secret-name-2>"
        }
    }
```

Los permisos para tener acceso al almacén de claves se proporcionan a una identidad administrada en la máquina virtual de supervisión. Azure Monitor espera que el almacén de claves proporcione al menos el permiso de obtención de secretos a la máquina virtual. Puede habilitarlo desde Azure Portal, PowerShell, la CLI de Azure o una plantilla de Azure Resource Manager.

#### <a name="parameters"></a>Parámetros
Los parámetros son tokens a los que se puede hacer referencia en la configuración del perfil a través de plantillas JSON. Los parámetros tienen un nombre y un valor. Los valores pueden ser cualquier tipo JSON, incluidos objetos y matrices. Se hace referencia a un parámetro en la configuración del perfil mediante su nombre y con esta convención: `.Parameters.<name>`.

Los parámetros pueden hacer referencia a secretos en Key Vault mediante la misma convención. Por ejemplo, `sqlAzureConnections` hace referencia al secreto `telegrafPassword` mediante la convención `$telegrafPassword`.

En tiempo de ejecución, todos los parámetros y secretos se resolverán y combinarán con la configuración del perfil para construir la configuración real que se va a usar en la máquina.

> [!NOTE]
> Los nombres de los parámetros de `sqlAzureConnections`, `sqlVmConnections` y `sqlManagedInstanceConnections` son obligatorios en la configuración, incluso si no proporciona cadenas de conexión para algunos de ellos.


## <a name="status-collecting-with-errors"></a>Estado: Recopilando con errores
La máquina de supervisión mostrará el estado **Recopilando con errores** si hay al menos un registro de *InsightsMetrics*, pero si también hay errores en la tabla de *operaciones*.

SQL Insights usa las siguientes consultas para recuperar esta información:

```kusto
InsightsMetrics 
    | extend Tags = todynamic(Tags) 
    | extend SqlInstance = tostring(Tags.sql_instance) 
    | where TimeGenerated > ago(240m) and isnotempty(SqlInstance) and Namespace == 'sqlserver_server_properties' and Name == 'uptime' 
```

```kusto
WorkloadDiagnosticLogs
| summarize Errors = countif(Status == 'Error')
```

> [!NOTE]
> Si no ve ningún dato en el tipo de datos `WorkloadDiagnosticLogs`, es posible que deba actualizar el perfil de supervisión para almacenar estos datos.  Desde la experiencia de usuario de SQL Insights, seleccione **Administrar perfil** > **Editar perfil** > **Actualizar el perfil de supervisión**.

En casos comunes, se proporciona información de solución de problemas en nuestra vista de registros: 

:::image type="content" source="media/sql-insights-enable/troubleshooting-logs-view.png" alt-text="Vista de registros de solución de problemas.":::

## <a name="next-steps"></a>Pasos siguientes

- Obtenga detalles sobre [Cómo habilitar SQL Insights](sql-insights-enable.md).
