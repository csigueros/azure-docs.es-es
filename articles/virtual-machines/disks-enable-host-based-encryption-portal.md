---
title: Habilitación del cifrado de un extremo a otro mediante el cifrado en host con discos administrados - Azure Portal
description: Use el cifrado en el host para habilitar el cifrado de un extremo a otro en los discos administrados de Azure mediante Azure Portal.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/22/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1fd010e645370389a657f70aeb2e821b69477ef1
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122687574"
---
# <a name="use-the-azure-portal-to-enable-end-to-end-encryption-using-encryption-at-host"></a>Use Azure Portal para habilitar el cifrado de un extremo a otro mediante el cifrado en host

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows

Cuando se habilita el cifrado en el host, los datos almacenados en el host de máquina virtual se cifran en reposo y se transmiten cifrados al servido Storage. Para obtener información conceptual sobre el cifrado en el host y otros tipos de cifrado de disco administrado, consulte la sección [Cifrado en el host: cifrado de un extremo a otro de los datos de la máquina virtual](./disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

Los discos temporales y los discos de SO efímeros se cifran en reposo con claves administradas por la plataforma al habilitar el cifrado de un extremo a otro. Las cachés del disco de datos y del sistema operativo se cifran en reposo con claves administradas por el cliente o por la plataforma, en función del tipo de cifrado de disco que se seleccione. Por ejemplo, si un disco se cifra con claves administradas por el cliente, la caché del disco se cifra con claves administradas por el cliente, y si un disco se cifra con claves administradas por la plataforma, la caché del disco se cifra con claves administradas por la plataforma.

## <a name="restrictions"></a>Restricciones

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]


### <a name="supported-vm-sizes"></a>Tamaños de máquinas virtuales que se admiten

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="prerequisites"></a>Requisitos previos

Debe habilitar la característica para su suscripción antes de usar la propiedad EncryptionAtHost para su VM/VMSS. Siga los pasos que se indican a continuación para habilitar la característica para su suscripción:

1. **Portal de Azure**: Seleccione el icono de Cloud Shell en [Azure Portal](https://portal.azure.com):

    ![Icono para iniciar Cloud Shell desde Azure Portal](../Cloud-Shell/media/overview/portal-launch-icon.png)
    
1.  Ejecute el siguiente comando para registrar la característica para su suscripción

    ```powershell
     Register-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute" 
    ```

1.  Compruebe que el estado de registro es **Registrado** (tarda unos minutos) mediante el comando siguiente antes de probar la característica.

    ```powershell
     Get-AzProviderFeature -FeatureName "EncryptionAtHost" -ProviderNamespace "Microsoft.Compute"  
    ```


Inicie sesión en Azure Portal con el [vínculo proporcionado](https://aka.ms/diskencryptionupdates).

> [!IMPORTANT]
> Debe usar el [vínculo proporcionado](https://aka.ms/diskencryptionupdates) para tener acceso a Azure Portal. El cifrado en el host no está visible actualmente en Azure Portal público sin usar el vínculo.

## <a name="deploy-a-vm-with-platform-managed-keys"></a>Implementación de una máquina virtual con claves administradas por la plataforma

1. Inicie sesión en [Azure Portal](https://aka.ms/diskencryptionupdates).
1. Busque **Máquinas virtuales** y seleccione **+Agregar** para crear una VM.
1. Cree una máquina virtual nueva, seleccione una región adecuada y un tamaño de máquina virtual compatible.
1. Rellene los restantes valores del panel **Datos básicos** y vaya al panel **Discos**.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Captura de pantalla del panel Datos básicos de creación de la máquina virtual en la que se resaltan los campos Región y Tamaño de la máquina virtual.":::

1. En el panel **Discos**, seleccione **Encryption at host** (Cifrado en el host).
1. Realice las selecciones restantes como desee.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/host-based-encryption-platform-keys.png" alt-text="Captura de pantalla del panel Discos de la creación de máquinas virtuales en la que se resalta el cifrado en el host.":::

1. Finalice el proceso de implementación de la máquina virtual y realice las selecciones que se adapten a su entorno.

Ha implementado una máquina virtual con el cifrado en el host habilitado y la caché del disco se cifra mediante claves administradas por la plataforma.

## <a name="deploy-a-vm-with-customer-managed-keys"></a>Implementación de una máquina virtual con claves administradas por el cliente

Como alternativa, puede usar claves administradas por el cliente para cifrar las cachés de disco.

### <a name="create-an-azure-key-vault-and-disk-encryption-set"></a>Creación de un almacén de Azure Key Vault y conjunto de cifrado de disco

Una vez habilitada la característica, deberá configurar un almacén de Azure Key Vault y un conjunto de cifrado de disco, si no lo ha hecho aún.

[!INCLUDE [virtual-machines-disks-encryption-create-key-vault-portal](../../includes/virtual-machines-disks-encryption-create-key-vault-portal.md)]

## <a name="deploy-a-vm"></a>Implementación de una máquina virtual

Ahora que ha configurado una instancia de Azure Key Vault y un conjunto de cifrado de disco, puede implementar una máquina virtual y usará el cifrado en el host.

1. Inicie sesión en [Azure Portal](https://aka.ms/diskencryptionupdates).
1. Busque **Máquinas virtuales** y seleccione **+Agregar** para crear una VM.
1. Cree una máquina virtual nueva, seleccione una región adecuada y un tamaño de máquina virtual compatible.
1. Rellene los restantes valores del panel **Datos básicos** y vaya al panel **Discos**.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-at-host-basic-blade.png" alt-text="Captura de pantalla del panel Datos básicos de creación de la máquina virtual en la que se resaltan los campos Región y Tamaño de la máquina virtual.":::

1. En el panel **Discos**, seleccione **Encryption at-rest for customer-managed key** (Cifrado en reposo con una clave administrada por el cliente) en **SSE encryption type** (Tipo de cifrado de SSE) y seleccione el conjunto de cifrado de disco.
1. Seleccione **Encryption at host** (Cifrado en host).
1. Realice las selecciones restantes como desee.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-host-based-encryption-customer-managed-keys.png" alt-text="Captura de pantalla del panel Discos de la creación de la máquina virtual en el que está resaltada la opción Encryption at host (Cifrado en host) y están seleccionadas las claves administradas por el cliente.":::

1. Finalice el proceso de implementación de la máquina virtual y realice las selecciones que se adapten a su entorno.

Ha implementado una máquina virtual con el cifrado en host habilitado.

## <a name="disable-host-based-encryption"></a>Deshabilitación del cifrado basado en host

En primer lugar, asegúrese de que la máquina virtual está desasignada, ya que el cifrado en el host no puede deshabilitar hasta que la máquina virtual esté desasignada.

1. Seleccione **Discos** y, después, seleccione **Configuración adicional**.

    :::image type="content" source="media/virtual-machines-disks-encryption-at-host-portal/disks-encryption-host-based-encryption-additional-settings.png" alt-text="Captura de pantalla del panel Discos en una máquina virtual en la que está resaltada la opción Configuración adicional.":::

1. Seleccione **No**, en **Encryption at host** (Cifrado en host) y, después, seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

[Ejemplos de plantillas de Azure Resource Manager](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)
