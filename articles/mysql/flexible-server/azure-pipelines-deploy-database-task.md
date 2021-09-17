---
title: Tarea de Azure Pipelines para Servidor flexible de Azure Database for MySQL
description: Cómo habilitar una tarea de la CLI para Servidor flexible de Azure Database for MySQL para usarla con Azure Pipelines
ms.topic: how-to
ms.service: mysql
ms.custom: seodec18, devx-track-azurecli
ms.author: sumuth
author: mksuni
ms.date: 08/09/2021
ms.openlocfilehash: db8d8e5faaca0941e8f009a2193301cd2207669a
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515845"
---
# <a name="azure-pipelines-for-azure-database-for-mysql-flexible-server"></a>Azure Pipelines para Servidor flexible de Azure Database for MySQL

Después de cada compilación correcta con **Azure Pipelines**, puede implementar automáticamente las actualizaciones de base de datos en Servidor flexible de Azure Database for MySQL.  Se puede usar una tarea de la CLI de Azure para actualizar la base de datos con un archivo SQL o un script SQL insertado en la base de datos. Esta tarea se puede realizar en agentes multiplataforma que se ejecutan en sistemas operativos Linux, macOS o Windows.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure. Si no tiene una, [obtenga la versión de evaluación gratuita](https://azure.microsoft.com/free/).

- Una [conexión de servicio de Azure Resource Manager](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) a su cuenta de Azure
- Agentes hospedados por Microsoft con la CLI de Azure preinstalada. Si usa agentes privados en su lugar, [instale la CLI de Azure](/cli/azure/install-azure-cli) en los equipos donde se ejecute el agente de compilación y versión. Si ya hay un agente en el equipo donde la CLI de Azure está instalada, reinícielo para asegurarse de que se actualizan todas las variables de fase pertinentes.
  
En este tutorial rápido se usan como punto de partida los recursos creados en una de estas guías:
- Crear un Servidor flexible de Azure Database for MySQL mediante [Azure Portal](./quickstart-create-server-portal.md) o la [CLI de Azure](./quickstart-create-server-cli.md)


## <a name="use-sql-file"></a>Usar un archivo SQL

En el siguiente ejemplo se muestra cómo pasar argumentos de base de datos y ejecutar el comando ```execute```.  

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
    inlineScript: |
      az login --allow-no-subscription
      az mysql flexible-server execute --name $(SERVERNAME) \
      --admin-user $(DBUSER) --admin-password '$(DBPASSWORD)' \
      --database-name $(DBNAME) \
      --file-path /code/sql/db-schema-update.sql
```

## <a name="use-inline-sql-script"></a>Usar scripts SQL insertados

En el siguiente ejemplo se muestra cómo ejecutar un script SQL insertado con el comando ```execute```. 

```yaml
- task: AzureCLI@2
  displayName: Azure CLI
  inputs:
    azureSubscription: <Name of the Azure Resource Manager service connection>
    scriptLocation: inlineScript
    arguments:
      -SERVERNAME mydemoserver `
      -DBNAME pollsdb `
      -DBUSER pollsdbuser`
      -DBPASSWORD pollsdbpassword
      -INLINESCRIPT 
    inlineScript: |
      az login --allow-no-subscription
      az mysql flexible-server execute --name $(SERVERNAME) \
      --admin-user $(DBUSER) --admin-password '$(DBPASSWORD)' \
      --database-name $(DBNAME) \
      --query-text "UPDATE items SET items.retail = items.retail * 0.9 WHERE items.id =100;" 
```

## <a name="task-inputs"></a>Entradas de la tarea

Aquí se incluye una lista completa de todas las entradas de tarea al usar una tarea de la CLI de Azure con Azure Pipelines. 

| Parámetro            | Descripción         | 
| :------------------- | :-------------------|
| azureSubscription| (Obligatorio) Indique la suscripción de Azure Resource Manager de la implementación. Este parámetro se muestra únicamente cuando la versión de la tarea seleccionada es 0.*, ya que la versión 1.0 de la tarea de la CLI de Azure solo admite suscripciones Azure Resource Manager. |
|scriptType| (Obligatorio) Indique el tipo de script; los scripts admitidos son PowerShell, PowerShell Core, Bat, Shell y script. Si se usa un **agente de Linux**, seleccione una de las siguientes opciones: ```bash``` o ```pscore```. Si se usa un **agente de Windows**, seleccione una de las siguientes opciones: ```batch```, ```ps``` y ```pscore```. |
|sriptLocation| (Obligatorio) Indique la ruta de acceso al script, por ejemplo, la ruta de acceso de archivo real, o use ```Inline script``` al especificar scripts insertados. El valor predeterminado es ```scriptPath```. |
|scriptPath| (Obligatorio) Ruta de acceso completa del script (.ps1, .bat o .cmd si se usa un agente basado en Windows, o <code>.ps1 </code> o <code>.sh </code> si se usa un agente basado en Linux), o bien ruta de acceso correspondiente al directorio de trabajo predeterminado. |
|inlineScript|(Obligatorio) Aquí se pueden escribir los scripts insertados. Si usa un agente de Windows, use PowerShell, PowerShell Core o scripts por lotes; si usa un agente basado en Linux, use en su lugar scripts de PowerShell Core o de shell. En los archivos por lotes, use el prefijo \"call\" antes de cada comando de Azure. También se pueden pasar variables predefinidas y personalizadas a este script mediante argumentos. <br/>Ejemplo de script de PowerShell/PowerShellCore/shell: ``` az --version az account show``` <br/>Ejemplo de script por lotes: ``` call az --version call az account show``` |
| argumentos| (Opcional) Indique todos los argumentos pasados al script, por ejemplo: ```-SERVERNAME mydemoserver```. |
|powerShellErrorActionPreference| (Opcional) Antepone la línea <b>$ErrorActionPreference = 'VALUE'</b> al inicio del script de PowerShell/PowerShell Core. El valor predeterminado es "stop". Los valores admitidos son "stop", "continue" y "silentlyContinue". |
|addSpnToEnvironment|(Opcional) Agrega el identificador de entidad de servicio y la clave del punto de conexión de Azure escogidos al entorno de ejecución del script. En el script se pueden usar estas variables: <b>$env:servicePrincipalId, $env:servicePrincipalKey y $env:tenantId</b>. Esto solo se respeta cuando el punto de conexión de Azure tiene un esquema de autenticación de entidad de servicio. El valor predeterminado es false.|
|useGlobalConfig|(Opcional) Si es false, esta tarea usará su propio <a href= "/cli/azure/azure-cli-configuration?preserve-view=true&view=azure-cli-latest#cli-configuration-file">directorio de configuración de CLI de Azure</a>. Se puede usar para ejecutar tareas de la CLI de Azure en versiones <b>paralelas</b>. <br/>Valor predeterminado: false</td>
|workingDirectory| (Opcional) Directorio de trabajo actual donde el script se ejecuta.  La raíz del repositorio (compilación) o del artefacto (versión), que es $(System.DefaultWorkingDirectory), está vacía. |
|failOnStandardError|(Opcional) Si es true, la tarea no se llevará a cabo cuando se escriban errores en la secuencia StandardError. Anule la selección de la casilla para omitir los errores estándar y basarse en los códigos de salida para determinar el estado. El valor predeterminado es false.|
|powerShellIgnoreLASTEXITCODE| (Opcional) Si es false, la línea <code>if ((Test-Path -LiteralPath variable:\\LASTEXITCODE)) { exit $LASTEXITCODE }</code> se anexa al final del script, lo que hará que el último código de salida de un comando externo se propague como el código de salida de PowerShell. De lo contrario, la línea no se anexa al final del script. El valor predeterminado es false. |


Si tiene problemas con la tarea de la CLI, consulte [cómo solucionar problemas de compilación y versión](/azure/devops/pipelines/troubleshooting/troubleshooting?view=azure-devops).

## <a name="next-steps"></a>Pasos siguientes 
Estas son algunas tareas relacionadas para implementar en Azure. 

- [Implementación de un grupo de recursos de Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops)
- [Implementación de Azure Web Apps](/azure/devops/pipelines/tasks/deploy/azure-rm-web-app-deployment?view=azure-devops)

