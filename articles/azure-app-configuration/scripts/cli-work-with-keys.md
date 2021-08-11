---
title: 'Ejemplo de script de la CLI de Azure: trabajo con pares clave-valor en un almacén de Azure App Configuration'
titleSuffix: Azure App Configuration
description: Uso de un script de la CLI de Azure para crear, ver, actualizar y eliminar valores de clave en un almacén de App Configuration
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: azurecli
ms.topic: sample
ms.date: 02/19/2020
ms.author: alkemper
ms.custom: devx-track-azurecli
ms.openlocfilehash: 144b324dcde0c0bdcbaf0a64840b56c6c618a19e
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114441592"
---
# <a name="work-with-key-values-in-an-azure-app-configuration-store"></a>Uso de pares clave-valor en un almacén de Azure App Configuration

En este ejemplo de script se muestra cómo realizar lo siguiente:
* Crear un nuevo par clave-valor.
* Enumerar todos los pares clave-valor existentes.
* Actualizar el valor de una clave recién creada.
* Eliminar el nuevo par clave-valor.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Este tutorial requiere la versión 2.0 de la CLI de Azure o cualquier versión posterior. Si usa Azure Cloud Shell, ya está instalada la versión más reciente.
## <a name="sample-script"></a>Script de ejemplo

```azurecli-interactive
#!/bin/bash

appConfigName=myTestAppConfigStore
newKey="TestKey"
refKey="KeyVaultReferenceTestKey"
uri="[URL to value stored in Key Vault]"
uri2="[URL to another value stored in Key Vault]"

# Create a new key-value 
az appconfig kv set --name $appConfigName --key $newKey --value "Value 1"

# List current key-values
az appconfig kv list --name $appConfigName

# Update new key's value
az appconfig kv set --name $appConfigName --key $newKey --value "Value 2"

# List current key-values
az appconfig kv list --name $appConfigName

# Create a new key-value referencing a value stored in Azure Key Vault
az appconfig kv set-keyvault  --name $appConfigName --key $refKey --secret-identifier $uri

# List current key-values
az appconfig kv list --name $appConfigName

# Update Key Vault reference
az appconfig kv set-keyvault --name $appConfigName --key $refKey --secret-identifier $uri2

# List current key-values
az appconfig kv list --name $appConfigName

# Delete new key
az appconfig kv delete  --name $appConfigName --key $newKey

# Delete Key Vault reference
az appconfig kv delete --name $appConfigName --key $refKey

# List current key-values
az appconfig kv list --name $appConfigName
```

[!INCLUDE [cli-script-cleanup](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Explicación del script

En esta tabla se enumeran los comandos que se usan en el script de ejemplo. 

| Get-Help | Notas |
|---|---|
| [az appconfig kv set](/cli/azure/appconfig/kv#az_appconfig_kv_set) | Crea o actualiza un par clave-valor. |
| [az appconfig kv list](/cli/azure/appconfig/kv#az_appconfig_kv_list) | Enumera los pares clave-valor de un almacén de App Configuration. |
| [az appconfig kv delete](/cli/azure/appconfig/kv#az_appconfig_kv_delete) | Elimina un par clave-valor. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la CLI de Azure, consulte la [documentación de la CLI de Azure](/cli/azure).

Puede encontrar ejemplos de script adicionales de la CLI y App Configuration en los [ejemplos de la CLI y Azure App Configuration](../cli-samples.md).
