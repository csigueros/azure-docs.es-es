---
title: Implementación de una máquina virtual con inicio seguro
description: Implemente una máquina virtual que use el inicio seguro.
author: khyewei
ms.author: khwei
ms.reviewer: cynthn
ms.service: virtual-machines
ms.subservice: trusted-launch
ms.topic: how-to
ms.date: 10/25/2021
ms.custom: template-how-to
ms.openlocfilehash: 0212c21a90caa533003d2304cfded72eb2370b49
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286058"
---
# <a name="deploy-a-vm-with-trusted-launch-enabled"></a>Implementación de una máquina virtual con el inicio seguro habilitado

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles

El [inicio seguro](trusted-launch.md) es una manera de mejorar la seguridad de las máquinas virtuales de [generación 2](generation-2.md). Protege frente a técnicas de ataque persistentes y avanzadas, gracias a la combinación de tecnologías de infraestructura como vTPM y el arranque seguro.

## <a name="prerequisites"></a>Requisitos previos 

- Debe [incorporar su suscripción a Microsoft Defender for Cloud](https://azure.microsoft.com/services/security-center/?&ef_id=CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE:G:s&OCID=AID2200277_SEM_CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE:G:s&gclid=CjwKCAjwwsmLBhACEiwANq-tXHeKhV--teH6kIijnBTmP-PgktfvGr5zW9TAx00SR7xsGUc3sTj5sBoCkEoQAvD_BwE#overview) si aún no lo está. Microsoft Defender for Cloud tiene un nivel gratuito, que ofrece información muy útil para varios recursos de Azure e híbridos. El inicio seguro aprovecha Defender for Cloud para mostrar varias recomendaciones sobre el estado de las máquinas virtuales. 

- Asigne iniciativas de directivas de Azure a su suscripción. Estas iniciativas de directiva solo deben asignarse una vez por suscripción. Esto instalará automáticamente todas las extensiones necesarias en todas las máquinas virtuales compatibles. 
    - Configurar los requisitos previos para habilitar la atestación de invitado en máquinas virtuales habilitadas para el inicio seguro 

    - Configurar las máquinas para instalar automáticamente los agentes de Azure Monitor y de seguridad de Azure en máquinas virtuales 

 
## <a name="deploy-a-trusted-vm"></a>Implementación de una máquina virtual de confianza
Cree una máquina virtual con el inicio seguro habilitado. Elija una de las siguientes opciones:

### <a name="portal"></a>[Portal](#tab/portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com).
2. Busque **Máquinas virtuales**.
3. En **Servicios**, seleccione **Máquinas virtuales**.
4. En la página **Máquinas virtuales**, seleccione **Agregar** y, después, **Máquina virtual**.
5. En **Detalles del proyecto**, asegúrese de que está seleccionada la suscripción correcta.
6. En **Grupo de recursos**, seleccione **Crear nuevo** y especifique un nombre para el grupo de recursos, o bien seleccione un grupo de recursos existente en la lista desplegable.
7. En **Detalles de la instancia**, escriba un nombre para la máquina virtual y elija una región que admita el [inicio seguro](trusted-launch.md#limitations).
1. En **Tipo de seguridad,** seleccione **Máquinas virtuales de inicio seguro**. Esto hará que aparezcan dos opciones más: **Arranque seguro** y **vTPM**. Seleccione las opciones adecuadas para su implementación.
    :::image type="content" source="media/trusted-launch/security.png" alt-text="Captura de pantalla que muestra las opciones de inicio seguro.":::
3. En **Imagen,** seleccione una imagen de las **imágenes recomendadas de Gen 2 compatibles con el inicio seguro**. Para obtener una lista, vea las [imágenes que admiten el inicio seguro](trusted-launch.md#limitations). 
   > [!TIP]
   > Si no ve la versión de segunda generación de la imagen que desea en la lista desplegable, seleccione **Ver todas las imágenes** y cambie el filtro **Tipo de seguridad** a **Inicio seguro**.
13. Seleccione un tamaño de máquina virtual que admita el inicio seguro. Consulte la lista de [tamaños compatibles](trusted-launch.md#limitations).
14. Rellene la información de **Cuenta de administrador** y **Reglas del puerto de entrada**.
15. En la parte inferior de la página, seleccione **Revisar y crear**.
16. En la página **Crear una máquina virtual** verá los detalles de la máquina virtual que va a implementar. Una vez que se supere la validación, seleccione **Crear**.

    :::image type="content" source="media/trusted-launch/validation.png" alt-text="Captura de pantalla de la página de validación, donde se muestran las opciones de inicio seguro incluidas.":::


La implementación de la máquina virtual tardará unos minutos.

### <a name="cli"></a>[CLI](#tab/cli)

Asegúrese de usar la versión más reciente de la CLI de Azure. 

Inicie sesión en Azure con `az login`.  

```azurecli-interactive
az login 
```

Cree una máquina virtual con el inicio seguro. 

```azurecli-interactive
az group create -n myresourceGroup -l eastus 
az vm create \ 
   --resource-group myResourceGroup \ 
   --name myVM \ 
   --image UbuntuLTS \ 
   --admin-username azureuser \ 
   --generate-ssh-keys \ 
   --SecurityType trustedLaunch \ 
   --EnableSecureBoot true \  
   --EnableVtpm true \
```
 
En el caso de las máquinas virtuales existentes, puede habilitar o deshabilitar la configuración de arranque seguro y vTPM. La actualización de la máquina virtual con la configuración de arranque seguro y vTPM desencadenará el reinicio automático.

```azurecli-interactive
az vm update \  
   --resource-group myResourceGroup \ 
   --name myVM \ 
   --EnableSecureBoot \  
   --EnableVtpm 
```  

### <a name="powershell"></a>[PowerShell](#tab/powershell)


Para aprovisionar una máquina virtual con inicio seguro, primero debe habilitarse con `TrustedLaunch` mediante el cmdlet `Set-AzVmSecurityType`. A continuación, puede usar el cmdlet Set-AzVmUefi para establecer la configuración de vTPM y SecureBoot. Use el fragmento de código siguiente como inicio rápido y recuerde reemplazar los valores de este ejemplo por los suyos propios. 

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$location = "West US"
$vmName = "myTrustedVM"
$vmSize = Standard_B2s
$publisher = "MicrosoftWindowsServer"
$offer = "WindowsServer"
$sku = "2019-Datacenter"
$version = latest
$cred = Get-Credential `
   -Message "Enter a username and password for the virtual machine."

$vm = New-AzVMConfig -VMName $vmName -VMSize $vmSize 

$vm = Set-AzVMOperatingSystem `
   -VM $vm -Windows `
   -ComputerName $vmName `
   -Credential $cred `
   -ProvisionVMAgent `
   -EnableAutoUpdate 

$vm = Add-AzVMNetworkInterface -VM $vm `
   -Id $NIC.Id 

$vm = Set-AzVMSourceImage -VM $vm `
   -PublisherName $publisher `
   -Offer $offer `
   -Skus $sku `
   -Version $version 

$vm = Set-AzVMOSDisk -VM $vm `
   -StorageAccountType "StandardSSD_LRS" `
   -CreateOption "FromImage" 

$vm = Set-AzVmSecurityType -VM $vm `
   -SecurityType "TrustedLaunch" 

$vm = Set-AzVmUefi -VM $vm `
   -EnableVtpm $true `
   -EnableSecureBoot $true 

New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vm 
```
 


### <a name="template"></a>[Plantilla](#tab/template)

Puede implementar máquinas virtuales con inicio seguro mediante una plantilla de inicio rápido:

**Linux**

[![Implementar en Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-linux%2FcreateUiDefinition.json)

**Windows**

[![Implementar en Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2Fazuredeploy.json/createUIDefinitionUri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fquickstarts%2Fmicrosoft.compute%2Fvm-trustedlaunch-windows%2FcreateUiDefinition.json)

---

## <a name="verify-or-update-your-settings"></a>Comprobación o actualización de la configuración

Puede ver la configuración del inicio seguro de una máquina virtual existente en su página  **Información general**  en el portal. La pestaña **Propiedades** mostrará el estado de las características de inicio seguro:

:::image type="content" source="media/trusted-launch/overview-properties.png" alt-text="Captura de pantalla de las propiedades de inicio seguro de la máquina virtual.":::

Para cambiar la configuración del inicio seguro, en el menú de la izquierda, seleccione  **Configuración**  en la sección  **Configuración** . Puede habilitar o deshabilitar el arranque seguro y vTPM desde la sección del tipo de seguridad del inicio seguro. Cuando haya terminado, seleccione Guardar en la parte superior de la página. 

:::image type="content" source="media/trusted-launch/update.png" alt-text="Captura de pantalla que muestra las casillas para cambiar la configuración del inicio seguro.":::

Si la máquina virtual se está ejecutando, recibirá un mensaje que indica que se reiniciará la máquina virtual. Seleccione  **Sí**  y espere a que se reinicie la máquina virtual para que los cambios surtan efecto. 


## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre las máquinas virtuales con [inicio seguro](trusted-launch.md) y de [generación 2](generation-2.md).
