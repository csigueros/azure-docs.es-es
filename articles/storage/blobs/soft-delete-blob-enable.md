---
title: Habilitación de la eliminación temporal para blobs
titleSuffix: Azure Storage
description: Habilite la eliminación temporal de blobs para evitar que los datos de los blobs se eliminen o sobrescriban accidentalmente.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 5d5dbb4b3be5eec500a7a0845a9dbc568a113ca3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599114"
---
# <a name="enable-soft-delete-for-blobs"></a>Habilitación de la eliminación temporal para blobs

La eliminación temporal de blobs protege a cada uno de los blobs y sus versiones, instantáneas y metadatos de errores accidentales al borrar o sobrescribir los datos, ya que conserva en el sistema los datos eliminados durante el período de tiempo que se especifique. Durante este período de retención, los blobs pueden restaurarse a su estado original. Una vez vencido el período de retención especificado, el blob se elimina permanentemente. Para más información sobre la eliminación temporal, consulte este artículo sobre la [eliminación temporal de blobs](soft-delete-blob-overview.md).

La eliminación temporal de blobs forma parte de una exhaustiva estrategia para proteger los datos de los blobs. Si necesita más información sobre las recomendaciones de Microsoft para proteger los datos, consulte [Información general sobre la protección de datos](data-protection-overview.md).

> [!NOTE]
> La eliminación temporal de blobs también puede proteger los blobs y directorios de las cuentas que tienen habilitada la característica de espacio de nombres jerárquico. La eliminación temporal de blobs en las cuentas que tienen habilitada la característica de espacio de nombres jerárquico está actualmente en versión preliminar pública y está disponible globalmente en todas las regiones de Azure.

De forma predeterminada, en las nuevas cuentas de almacenamiento, la eliminación temporal de blobs está deshabilitada. Sin embargo, puede habilitarla o deshabilitarla en cualquier momento mediante Azure Portal, PowerShell o la CLI de Azure.

## <a name="enable-blob-soft-delete"></a>Habilitación de la eliminación temporal de blobs

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para habilitar la eliminación temporal de blobs en la cuenta de almacenamiento mediante Azure Portal, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com/), vaya a la cuenta de almacenamiento.
1. Busque la opción **Protección de datos** en **Blob service**.
1. En la sección **Recuperación**, seleccione **Turn on soft delete for blobs** (Activar eliminación temporal de blobs).
1. Especifique un período de retención de entre 1 y 365 días. Microsoft recomienda un período de retención mínimo de siete días.
1. Guarde los cambios.

:::image type="content" source="media/soft-delete-blob-enable/blob-soft-delete-configuration-portal.png" alt-text="Instantánea en la que se muestra cómo habilitar la eliminación temporal en Azure Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para habilitar la eliminación temporal de blobs con PowerShell, llame al comando [Enable-AzStorageBlobDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstorageblobdeleteretentionpolicy) especificando el período de retención en días.

En el ejemplo siguiente, se habilita la eliminación temporal de blobs y se establece un período de retención de siete días. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurepowershell
Enable-AzStorageBlobDeleteRetentionPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -RetentionDays 7
```

Para comprobar la configuración actual de la eliminación temporal de blobs, llame al comando [Get-AzStorageBlobServiceProperty](/powershell/module/az.storage/get-azstorageblobserviceproperty):

```azurepowershell
$properties = Get-AzStorageBlobServiceProperty -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account>
$properties.DeleteRetentionPolicy.Enabled
$properties.DeleteRetentionPolicy.Days
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-CLI)

Para habilitar la eliminación temporal de blobs con la CLI de Azure, llame al comando [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update) y especifique el período de retención en días.

En el ejemplo siguiente, se habilita la eliminación temporal de blobs y se establece un período de retención de siete días. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli-interactive
az storage account blob-service-properties update --account-name <storage-account> \
    --resource-group <resource-group> \
    --enable-delete-retention true \
    --delete-retention-days 7
```

Para comprobar la configuración actual de la eliminación temporal de blobs, llame al comando [az storage account blob-service-properties show](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_show):

```azurecli-interactive
az storage account blob-service-properties show --account-name <storage-account> \
    --resource-group <resource-group>
```

---

## <a name="enable-blob-soft-delete-hierarchical-namespace"></a>Habilitación de la eliminación temporal de blobs (espacio de nombres jerárquico)

La eliminación temporal de blobs también puede proteger los blobs y directorios de las cuentas que tienen habilitada la característica de espacio de nombres jerárquico en ellos.

> [!IMPORTANT]
> La eliminación temporal en las cuentas que tienen habilitada la característica de espacio de nombres jerárquico está actualmente en versión preliminar y está disponible globalmente en todas las regiones de Azure.
> Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>
>
> Para inscribirse en la versión preliminar, visite [este formulario](https://forms.office.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbR4mEEwKhLjlBjU3ziDwLH-pUOVRVOUpDRUtHVUtDUUtMVTZUR0tUMjZWNy4u).

<a id="enable-blob-soft-delete-hierarchical-namespace"></a>

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para habilitar la eliminación temporal de blobs en la cuenta de almacenamiento mediante Azure Portal, siga estos pasos:

1. En [Azure Portal](https://portal.azure.com/), vaya a la cuenta de almacenamiento.
1. Busque la opción **Protección de datos** en **Administración de datos**.
1. En la sección **Recuperación**, seleccione **Enable soft delete for blobs** (Habilitar la eliminación temporal de blobs).
1. Especifique un período de retención de entre 1 y 365 días. Microsoft recomienda un período de retención mínimo de siete días.
1. Guarde los cambios.

> [!div class="mx-imgBorder"]
> ![Captura de pantalla en la que se muestra cómo habilitar la eliminación temporal en Azure Portal en las cuentas que tienen un espacio de nombres jerárquico.](./media/soft-delete-blob-enable/blob-soft-delete-configuration-portal-hierarchical-namespace.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Instale el módulo de **PowershellGet** más reciente. Luego, cierre la consola de PowerShell y vuelva a abrirla.

    ```powershell
    install-Module PowerShellGet -Repository PSGallery -Force
    ```

2. Instale el módulo de versión preliminar **Az.Storage**.

    ```powershell
    Install-Module Az.Storage -Repository PsGallery -RequiredVersion 3.7.1-preview -AllowClobber -AllowPrerelease -Force
    ```

    Para más información sobre cómo instalar módulos de PowerShell, vea [Instalación del módulo de Azure PowerShell](/powershell/azure/install-az-ps).

3. Obtenga la autorización de la cuenta de almacenamiento mediante una clave de cuenta de almacenamiento, una cadena de conexión o Azure Active Directory (Azure AD). Para obtener más información, vea [Conexión a la cuenta](data-lake-storage-directory-file-acl-powershell.md#connect-to-the-account).

   En el ejemplo siguiente, se obtiene la autorización mediante una clave de cuenta de almacenamiento.

   ```powershell
   $ctx = New-AzStorageContext -StorageAccountName '<storage-account-name>' -StorageAccountKey '<storage-account-key>'
   ```

4. Para habilitar la eliminación temporal de blobs con PowerShell, use el comando [Enable-AzStorageDeleteRetentionPolicy](/powershell/module/az.storage/enable-azstoragedeleteretentionpolicy) y especifique el período de retención en días.

   En el ejemplo siguiente, se habilita la eliminación temporal de una cuenta y se establece un período de retención de cuatro días.

   ```powershell
   Enable-AzStorageDeleteRetentionPolicy -RetentionDays 4  -Context $ctx
   ```

5. Para comprobar la configuración actual de la eliminación de blobs, use el comando `Get-AzStorageServiceProperty`:

   ```powershell
    Get-AzStorageServiceProperty -ServiceType Blob -Context $ctx
   ```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-CLI)

1. Abra [Azure Cloud Shell](../../cloud-shell/overview.md) o, si ha [instalado](/cli/azure/install-azure-cli) la CLI de Azure localmente, abra una aplicación de consola de comandos como Windows PowerShell.

2. Instale la extensión `storage-preview`.

   ```azurecli
   az extension add -n storage-preview
   ```

3. Conecte a su cuenta de almacenamiento. Para obtener más información, vea [Conexión a la cuenta](data-lake-storage-directory-file-acl-cli.md#connect-to-the-account).

   > [!NOTE]
   > En el ejemplo que se presenta en este artículo se muestra la autorización de Azure Active Directory (Azure AD). Para obtener más información sobre los métodos de autorización, consulte [Autorización del acceso a los datos de blobs o colas con la CLI de Azure](./authorize-data-operations-cli.md).

4. Para habilitar la eliminación temporal con la CLI de Azure, llame al comando `az storage fs service-properties update` y especifique el período de retención en días.

   En el ejemplo siguiente, se habilita la eliminación temporal de blobs y directorios, y se establece un período de retención de cinco días.

   ```azurecli
   az storage fs service-properties update --delete-retention --delete-retention-period 5 --auth-mode login
   ```

5. Para comprobar la configuración actual de la eliminación de blobs, llame al comando `az storage fs service-properties update`:

   ```azurecli
   az storage fs service-properties update --delete-retention false --connection-string $con
   ```

---

## <a name="next-steps"></a>Pasos siguientes

- [Eliminación temporal para blobs](soft-delete-blob-overview.md)
- [Administración y restauración de blobs eliminados temporalmente](soft-delete-blob-manage.md)
