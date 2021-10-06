---
title: Tarea de Azure Pipelines para Servidor flexible de Azure Database for MySQL
description: Cómo habilitar una tarea de la CLI para Servidor flexible de Azure Database for MySQL para usarla con Azure Pipelines
ms.topic: how-to
ms.service: mysql
ms.custom: seodec18, devx-track-azurecli
ms.author: sumuth
author: mksuni
ms.date: 08/09/2021
ms.openlocfilehash: cbb44e7a3b48ec1fae928ac4de14b30b939cf975
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129361721"
---
# <a name="azure-pipelines-for-azure-database-for-mysql-flexible-server"></a>Azure Pipelines para Servidor flexible de Azure Database for MySQL

Después de cada compilación correcta con **Azure Pipelines**, puede implementar automáticamente las actualizaciones de base de datos en Servidor flexible de Azure Database for MySQL.  Se puede usar una tarea de la CLI de Azure para actualizar la base de datos con un archivo SQL o un script SQL insertado en la base de datos. Esta tarea se puede ejecutar en agentes multiplataforma que se ejecutan en los sistemas operativos Linux, macOS o Windows.

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure. Si no tiene una, [obtenga la versión de evaluación gratuita](https://azure.microsoft.com/free/).

- Una [conexión de servicio de Azure Resource Manager](/azure/devops/pipelines/library/connect-to-azure) a la cuenta de Azure
- Los agentes hospedados por Microsoft tienen la CLI de Azure preinstalada. Si usa agentes privados en su lugar, [instale la CLI de Azure](/cli/azure/install-azure-cli) en los equipos donde se ejecute el agente de compilación y versión. Si ya hay un agente en el equipo donde la CLI de Azure está instalada, reinícielo para asegurarse de que se actualizan todas las variables de fase pertinentes.
  
En este tutorial rápido se usan como punto de partida los recursos creados en una de estas guías:
- Crear un Servidor flexible de Azure Database for MySQL mediante [Azure Portal](./quickstart-create-server-portal.md) o la [CLI de Azure](./quickstart-create-server-cli.md)


## <a name="use-sql-file"></a>Usar un archivo SQL

En el siguiente ejemplo, se muestra cómo pasar argumentos de base de datos y ejecutar el comando ```execute```.  

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

Puede ver la lista completa de todas las entradas de la tarea al usar una tarea de la CLI de Azure con Azure Pipelines. 

| Parámetro            | Descripción         | 
| :------------------- | :-------------------|
| azureSubscription| (Obligatorio) Proporcione la suscripción de Azure Resource Manager de la implementación. Este parámetro se muestra únicamente cuando la versión de la tarea seleccionada es 0.*, ya que la versión 1.0 de la tarea de la CLI de Azure solo admite suscripciones Azure Resource Manager. |
|scriptType| (Obligatorio) Proporcione el tipo de script. Los scripts admitidos son PowerShell, PowerShell Core, Bat, Shell y script. Cuando se ejecuta en un **agente de Linux**, seleccione una de las siguientes opciones: ```bash``` o ```pscore```. Cuando se ejecuta en un **agente de Windows**, seleccione una de las siguientes opciones: ```batch```, ```ps``` o ```pscore```. |
|sriptLocation| (Obligatorio) Proporcione la ruta de acceso al script, por ejemplo, la ruta de acceso del archivo real, o use ```Inline script``` al especificar scripts insertados. El valor predeterminado es ```scriptPath```. |
|scriptPath| (Obligatorio) Ruta de acceso completa del script (.ps1, .bat o .cmd si se usa un agente basado en Windows, o <code>.ps1 </code> o <code>.sh </code> si se usa un agente basado en Linux), o bien la ruta de acceso correspondiente al directorio de trabajo predeterminado. |
|inlineScript|(Obligatorio) Aquí se pueden escribir los scripts insertados. Si usa un agente de Windows, use PowerShell, PowerShell Core o scripts por lotes; si usa un agente basado en Linux, use en su lugar scripts de PowerShell Core o de shell. En los archivos por lotes, use el prefijo \"call\" antes de cada comando de Azure. También se pueden pasar variables predefinidas y personalizadas a este script mediante argumentos. <br/>Ejemplo para PowerShell, PowerShellCore y el shell: ``` az --version az account show``` <br/>Ejemplo para script por lotes: ``` call az --version call az account show``` |
| argumentos| (Opcional) Proporcione todos los argumentos pasados al script. Por ejemplo, ```-SERVERNAME mydemoserver```. |
|powerShellErrorActionPreference| (Opcional) Antepone la línea <b>$ErrorActionPreference = 'VALUE'</b> al inicio del script de PowerShell o PowerShell Core. El valor predeterminado es stop. Los valores admitidos son stop, continue y silentlyContinue. |
|addSpnToEnvironment|(Opcional) Agrega el identificador de la entidad de servicio y la clave del punto de conexión de Azure que haya elegido al entorno de ejecución del script. En el script se pueden usar estas variables: <b>$env:servicePrincipalId, $env:servicePrincipalKey y $env:tenantId</b>. Esto solo se respeta cuando el punto de conexión de Azure tiene un esquema de autenticación de entidad de servicio. El valor predeterminado es false.|
|useGlobalConfig|(Opcional) Si es false, esta tarea usará su propio <a href= "/cli/azure/azure-cli-configuration?preserve-view=true&view=azure-cli-latest#cli-configuration-file">directorio de configuración de la CLI de Azure</a> independiente. Se puede usar para ejecutar tareas de la CLI de Azure en versiones <b>paralelas</b>. <br/>Valor predeterminado: false</td>
|workingDirectory| (Opcional) Directorio de trabajo actual donde se ejecuta el script.  Si está vacío, es la raíz del repositorio (compilación) o de los artefactos (versión), que es $(System.DefaultWorkingDirectory). |
|failOnStandardError|(Opcional) Si es true, la tarea no se llevará a cabo cuando se escriban errores en la secuencia StandardError. Anule la selección de la casilla para omitir los errores estándar y basarse en los códigos de salida para determinar el estado. El valor predeterminado es false.|
|powerShellIgnoreLASTEXITCODE| (Opcional) Si es false, la línea <code>if ((Test-Path -LiteralPath variable:\\LASTEXITCODE)) { exit $LASTEXITCODE }</code> se anexa al final del script. Esto hará que el último código de salida de un comando externo se propague como el código de salida de PowerShell. De lo contrario, la línea no se anexa al final del script. El valor predeterminado es false. |


Si tiene problemas con la tarea de la CLI, consulte [Solución de problemas de ejecuciones de canalizaciones](/azure/devops/pipelines/troubleshooting/troubleshooting).

## <a name="next-steps"></a>Pasos siguientes 
Estas son algunas tareas relacionadas para implementar en Azure. 

- [Implementación de un grupo de recursos de Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
- [Tarea de implementación de Azure App Service](/azure/devops/pipelines/tasks/deploy/azure-rm-web-app-deployment)

