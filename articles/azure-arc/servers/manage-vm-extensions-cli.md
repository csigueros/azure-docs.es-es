---
title: Habilitación de la extensión de VM mediante la CLI de Azure
description: En este artículo se describe cómo implementar extensiones de máquina virtual en servidores habilitados para Azure Arc que se ejecutan en entornos de nube híbrida mediante la CLI de Azure.
ms.date: 10/28/2021
ms.topic: conceptual
ms.custom: devx-track-azurecli
ms.openlocfilehash: b7294285cd6e4fa7c2bbfac859c3fec8dfa3a474
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131440419"
---
# <a name="enable-azure-vm-extensions-using-the-azure-cli"></a>Habilitación de las extensiones de VM de Azure mediante la CLI de Azure

En este artículo se muestra cómo implementar, actualizar y desinstalar extensiones de VM, compatibles con servidores habilitados para Azure Arc, en una máquina híbrida Linux o Windows mediante la CLI de Azure.

> [!NOTE]
> Los servidores habilitados para Azure Arc no admiten la implementación y administración de extensiones de VM en máquinas virtuales de Azure. En el caso de las máquinas virtuales de Azure, consulte el siguiente artículo con [información general acerca de la extensión de máquina virtual](../../virtual-machines/extensions/overview.md).

[!INCLUDE [Azure CLI Prepare your environment](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="install-the-azure-cli-extension"></a>Instalación de la extensión de la CLI de Azure

Los comandos de ConnectedMachine no se incluyen como parte de la CLI de Azure. Antes de usar la CLI de Azure para conectarse a Azure y administrar extensiones de máquina virtual en el servidor híbrido administrado por los servidores habilitados para Azure Arc, debe cargar la extensión ConnectedMachine. Estas operaciones de administración se pueden realizar desde la estación de trabajo y no es necesario ejecutarlas en el servidor habilitado para Azure Arc.

Ejecute el siguiente comando para obtenerla:

```azurecli
az extension add --name connectedmachine
```

## <a name="enable-extension"></a>Habilitación de una extensión

Para habilitar una extensión de máquina virtual en el servidor habilitado para Azure Arc, use [az connectedmachine extension create](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_create) con los parámetros `--machine-name`, `--extension-name`, `--location`, `--type`, `settings` y `--publisher`.

En el ejemplo siguiente se habilita la extensión de máquina virtual de Log Analytics en un servidor habilitado para Azure Arc:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "OmsAgentForLinux or MicrosoftMonitoringAgent" --location "regionName" --settings '{\"workspaceId\":\"myWorkspaceId\"}' --protected-settings '{\"workspaceKey\":\"myWorkspaceKey\"}' --resource-group "myResourceGroup" --type-handler-version "1.13" --type "OmsAgentForLinux or MicrosoftMonitoringAgent" --publisher "Microsoft.EnterpriseCloud.Monitoring" 
```

En el ejemplo siguiente se habilita la extensión de script personalizado en un servidor habilitado para Azure Arc:

```azurecli
az connectedmachine extension create --machine-name "myMachineName" --name "CustomScriptExtension" --location "regionName" --type "CustomScriptExtension" --publisher "Microsoft.Compute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -gt 10000 }\\\"\"}" --type-handler-version "1.10" --resource-group "myResourceGroup"
```

En el ejemplo siguiente se habilita la extensión de máquina virtual de Key Vault en un servidor habilitado para Azure Arc:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.KeyVault" --type "KeyVaultForLinux or KeyVaultForWindows" --name "KeyVaultForLinux or KeyVaultForWindows" --settings '{"secretsManagementSettings": { "pollingIntervalInS": "60", "observedCertificates": ["observedCert1"] }, "authenticationSettings": { "msiEndpoint": "http://localhost:40342/metadata/identity" }}'
```

En el ejemplo anterior se habilita la extensión de Microsoft Antimalware en un servidor Windows habilitado para Azure Arc:

```azurecli
az connectedmachine extension create --resource-group "resourceGroupName" --machine-name "myMachineName" --location "regionName" --publisher "Microsoft.Azure.Security" --type "IaaSAntimalware" --name "IaaSAntimalware" --settings '{"AntimalwareEnabled": true}'
```

## <a name="list-extensions-installed"></a>Enumeración de extensiones instaladas

Para obtener una lista de las extensiones de máquina virtual en el servidor habilitado para Azure Arc, use [az connectedmachine extension list](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_list) con los parámetros `--machine-name` y `--resource-group`.

Ejemplo:

```azurecli
az connectedmachine extension list --machine-name "myMachineName" --resource-group "myResourceGroup"
```

De forma predeterminada, la salida de los comandos de la CLI de Azure está en JSON (notación de objetos JavaScript). Para cambiar la salida predeterminada a una lista o tabla, por ejemplo, use [az config set core.output=table](/cli/azure/reference-index). También puede agregar `--output` a cualquier comando para un cambio específico del formato de salida.

En el ejemplo siguiente se muestra la salida JSON parcial desde el comando `az connectedmachine extension -list`:

```json
[
  {
    "autoUpgradingMinorVersion": "false",
    "forceUpdateTag": null,
    "id": "/subscriptions/subscriptionId/resourceGroups/resourceGroupName/providers/Microsoft.HybridCompute/machines/SVR01/extensions/DependencyAgentWindows",
    "location": "regionName",
    "name": "DependencyAgentWindows",
    "namePropertiesInstanceViewName": "DependencyAgentWindows",
```

## <a name="update-extension-configuration"></a>Actualización de la configuración de la extensión

Algunas extensiones de máquina virtual requieren opciones de configuración para instalarlas en el servidor habilitado para Arc, como la extensión de script personalizado y la extensión de máquina virtual del agente de Log Analytics. Para actualizar la configuración de una extensión, use [az connectedmachine extension update](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_update).

En el ejemplo siguiente se muestra cómo configurar la extensión de script personalizado:

```azurecli
az connectedmachine extension update --name "CustomScriptExtension" --type "CustomScriptExtension" --publisher "Microsoft.HybridCompute" --settings "{\"commandToExecute\":\"powershell.exe -c \\\"Get-Process | Where-Object { $_.CPU -lt 100 }\\\"\"}" --type-handler-version "1.10" --machine-name "myMachine" --resource-group "myResourceGroup"
```

## <a name="upgrade-extensions"></a>Actualización de extensiones

Cuando se publica una nueva versión de una extensión de VM compatible, puede actualizarla a esa versión más reciente. Para actualizar una extensión de máquina virtual, use [az connectedmachine upgrade-extension](/cli/azure/connectedmachine) con los parámetros `--machine-name`, `--resource-group` y `--extension-targets`.

Para el parámetro `--extension-targets`, debe especificar la extensión y la versión más reciente disponible. Para averiguar cuál es la versión más reciente disponible, consulte la página **Extensiones** del servidor habilitado para Arc seleccionado en Azure Portal o ejecute [az vm extension image list](/cli/azure/vm/extension/image#az_vm_extension_image_list).

Para actualizar la extensión del agente de Log Analytics para Windows que tenga disponible una versión más reciente, ejecute el siguiente comando:

```azurecli
az connectedmachine upgrade-extension --machine-name "myMachineName" --resource-group "myResourceGroup --extension-targets  --extension-targets "{\"MicrosoftMonitoringAgent\":{\"targetVersion\":\"1.0.18053.0\"}}"" 
```

Puede revisar la versión de las extensiones de máquina virtual instaladas en cualquier momento ejecutando el comando [az connectedmachine extension list](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_list). El valor de propiedad `typeHandlerVersion` representa la versión de la extensión.

## <a name="remove-extensions"></a>Eliminación de extensiones

Para quitar una extensión de máquina virtual instalada en el servidor habilitado para Azure Arc, use [az connectedmachine extension delete](/cli/azure/connectedmachine/extension#az_connectedmachine_extension_delete) con los parámetros `--extension-name`, `--machine-name` y `--resource-group`.

Por ejemplo, para quitar la extensión de VM de Log Analytics para Linux, ejecute el siguiente comando:

```azurecli
az connectedmachine extension delete --machine-name "myMachineName" --name "OmsAgentForLinux" --resource-group "myResourceGroup"
```

## <a name="next-steps"></a>Pasos siguientes

- Puede implementar, administrar y quitar extensiones de VM con [Azure PowerShell](manage-vm-extensions-powershell.md), desde [Azure Portal](manage-vm-extensions-portal.md) o con [plantillas de Azure Resource Manager](manage-vm-extensions-template.md).

- Puede encontrar información de solución de problemas en la [guía de solución de problemas de las extensiones de máquina virtual](troubleshoot-vm-extensions.md).

- Consulte el artículo de [introducción](/cli/azure/connectedmachine/extension) a las extensiones de VM de la CLI de Azure para obtener más información sobre los comandos.
