---
title: Habilitación de SMB multicanal
description: Aprenda a habilitar SMB multicanal en recursos compartidos de archivos Premium de Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/17/2021
ms.author: rogarana
ms.subservice: files
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 7bbfba257126b741751d169af9390615bec6917c
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114459685"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>Habilitación de SMB multicanal en una cuenta de FileStorage (versión preliminar) 

Las cuentas de Azure FileStorage admiten SMB multicanal (versión preliminar), lo que aumenta el rendimiento de los clientes de SMB 3.x mediante el establecimiento de varias conexiones de red a los recursos compartidos de archivos Premium. En este artículo se proporcionan una guía paso a paso para habilitar SMB multicanal en una cuenta de Storage existente. Para más información sobre SMB multicanal de Azure Files, consulte el artículo sobre el  [rendimiento de SMB multicanal](storage-files-smb-multichannel-performance.md).

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![No](../media/icons/no-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="limitations"></a>Limitaciones

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Disponibilidad regional

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Requisitos previos

- [Cree una cuenta de FileStorage](./storage-how-to-create-file-share.md).

## <a name="getting-started"></a>Introducción

Abra una ventana de PowerShell e inicie sesión en su suscripción de Azure. Desde ella puede registrarse para la versión preliminar de SMB multicanal con los siguientes comandos.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> El registro puede tardar hasta una hora en completarse.

### <a name="verify-that-feature-registration-is-complete"></a>Comprobación de que se ha completado el registro de características

Dado que la característica puede tardar hasta una hora en habilitarse en la cuenta de Storage, puede usar el siguiente comando para validar que está registrada para su suscripción:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>Habilitar SMB multicanal 
Una vez que haya creado una cuenta de FileStorage, puede seguir las instrucciones para actualizar la configuración de SMB multicanal en la cuenta de almacenamiento.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Inicie sesión en Azure Portal y vaya la cuenta de almacenamiento de FileStorage en la que desea configurar SMB multicanal.
1. Seleccione **Recursos compartidos de archivos** en **File service** y, después, seleccione **Configuración del recurso compartido de archivos**.
1. En **SMB multicanal** , seleccione **activado** (o **desactivado** para deshabilitarlo) y seleccione **Guardar**.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Captura de pantalla de la cuenta de almacenamiento, SMB multicanal está activado."  lightbox="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png":::

Si la opción SMB multicanal no se ve en **Configuración del recurso compartido de archivos** o si se produce un error de configuración que no permite realizar la actualización, asegúrese de que la suscripción está registrada y de que la cuenta se encuentra en una de las [regiones admitidas](#regional-availability) con el tipo de cuenta y la replicación admitidos.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Establezca las variables `$resourceGroupName` y `$storageAccountName` en el grupo de recursos y en la cuenta de Storage antes de ejecutar estos comandos de PowerShell.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
La CLI de Azure aún no admite la configuración de SMB multicanal. Para configurar SMB multicanal en una cuenta de Storage, vea las instrucciones del portal.

---

> [!NOTE]
> Todos los cambios que se realicen en la configuración se aplicarán a todos los recursos compartidos de archivos de la cuenta de Storage. Sin embargo, tendrá que volver a montar el recurso compartido en el cliente para que los cambios surtan efecto.


## <a name="next-steps"></a>Pasos siguientes 

- [Vuelva a montar el recurso compartido de archivos](storage-how-to-use-files-windows.md) para aprovechar SMB multicanal.
- [Solucione los problemas relacionados con SMB multicanal](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- Para más información sobre las mejoras, consulte el artículo sobre el [rendimiento de SMB multicanal](storage-files-smb-multichannel-performance.md)
- Para obtener más información acerca de la característica SMB multicanal de Windows, consulte [Administración de SMB multicanal](/azure-stack/hci/manage/manage-smb-multichannel).
