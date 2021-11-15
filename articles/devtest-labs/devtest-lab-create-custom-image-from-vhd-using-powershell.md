---
title: Creación de una imagen personalizada a partir de un archivo VHD mediante Azure PowerShell
description: Automatice la creación de una imagen personalizada en Azure DevTest Labs a partir de un archivo VHD mediante PowerShell.
ms.topic: how-to
ms.date: 10/24/2021
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 2185309194b04d1b76ca84daea19e92e7c017463
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131469068"
---
# <a name="create-a-custom-image-from-a-vhd-file-with-powershell"></a>Creación de una imagen personalizada a partir de un archivo VHD con PowerShell

[!INCLUDE [devtest-lab-create-custom-image-from-vhd-selector](../../includes/devtest-lab-create-custom-image-from-vhd-selector.md)]

[!INCLUDE [devtest-lab-custom-image-definition](../../includes/devtest-lab-custom-image-definition.md)]

[!INCLUDE [devtest-lab-upload-vhd-options](../../includes/devtest-lab-upload-vhd-options.md)]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="powershell-steps"></a>pasos de PowerShell

Los siguientes pasos le guían en la creación de una imagen personalizada a partir de un archivo VHD mediante Azure PowerShell:

1. En el símbolo del sistema de PowerShell, inicie sesión en su cuenta de Azure con el cmdlet **Connect-AzAccount**:

   ```powershell
   Connect-AzAccount
   ```

1. Seleccione la suscripción de Azure con el cmdlet **Select-AzSubscription**. Reemplace \<subscription ID> por el GUID del identificador de la suscripción.

   ```powershell
   $subscriptionId = '<subscription ID>'
   Select-AzSubscription -SubscriptionId $subscriptionId
   ```

1. Obtenga el objeto de laboratorio mediante la llamada al cmdlet **Get-AzResource**. Reemplace los marcadores de posición \<lab resource group name> y \<lab name> por su propio grupo de recursos y nombres de laboratorio.

   ```powershell
   $labRg = '<lab resource group name>'
   $labName = '<lab name>'
   $lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)
   ```

1. Reemplace el marcador de posición de la variable **$vhdUri** por el identificador URI del archivo VHD cargado. Puede obtener el URI del archivo VHD desde su página de blobs en la cuenta de almacenamiento del laboratorio en Azure Portal. Un URI de VHD de ejemplo es: `https://acontosolab1234.blob.core.windows.net/uploads/myvhd.vhd`.

   ```powershell
   $vhdUri = '<VHD URI>'
   ```

1. Cree la imagen personalizada mediante el cmdlet **New-AzResourceGroupDeployment**. Reemplace los marcadores de posición \<custom image name> y \<custom image description> por el nombre y la descripción que desee.

   ```powershell
   $customImageName = '<custom image name>'
   $customImageDescription = '<custom image description>'

   $parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

   New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
   ```

## <a name="complete-powershell-script"></a>Script completo de PowerShell

La combinación de los pasos anteriores genera el siguiente script de PowerShell que crea una imagen personalizada a partir de un archivo VHD. Para usar el script, reemplace los siguientes marcadores de posición por sus propios valores:

- \<subscription ID>
- \<lab resource group name>
- \<lab name>
- \<VHD URI>
- \<custom image name>
- \<custom image description>

```powershell
# Log in to your Azure account.
Connect-AzAccount

# Select the desired Azure subscription.
$subscriptionId = '<subscription ID>'
Select-AzSubscription -SubscriptionId $subscriptionId

# Get the lab object.
$labRg = '<lab resource group name>'
$labName = '<lab name>'
$lab = Get-AzResource -ResourceId ('/subscriptions/' + $subscriptionId + '/resourceGroups/' + $labRg + '/providers/Microsoft.DevTestLab/labs/' + $labName)

# Set the URI of the VHD file.
$vhdUri = '<VHD URI>'

# Set the custom image name and description values.
$customImageName = '<custom image name>'
$customImageDescription = '<custom image description>'

# Set up the parameters object.
$parameters = @{existingLabName="$($lab.Name)"; existingVhdUri=$vhdUri; imageOsType='windows'; isVhdSysPrepped=$false; imageName=$customImageName; imageDescription=$customImageDescription}

# Create the custom image.
New-AzResourceGroupDeployment -ResourceGroupName $lab.ResourceGroupName -Name CreateCustomImage -TemplateUri 'https://raw.githubusercontent.com/Azure/azure-devtestlab/master/samples/DevTestLabs/QuickStartTemplates/201-dtl-create-customimage-from-vhd/azuredeploy.json' -TemplateParameterObject $parameters
```

## <a name="next-steps"></a>Pasos siguientes

- [Comparación de imágenes personalizadas y fórmulas en DevTest Labs](devtest-lab-comparing-vm-base-image-types.md)
- [Copying Custom Images between Azure DevTest Labs (Copiar imágenes personalizadas entre instancias de Azure DevTest Labs)](https://www.visualstudiogeeks.com/blog/DevOps/How-To-Move-CustomImages-VHD-Between-AzureDevTestLabs#copying-custom-images-between-azure-devtest-labs)
