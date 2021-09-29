---
title: Configurar una directiva de administración del ciclo de vida
titleSuffix: Azure Storage
description: Configure una directiva de administración del ciclo de vida para mover datos automáticamente entre los niveles de acceso frecuente, esporádico y de archivo durante su ciclo de vida.
author: tamram
ms.author: tamram
ms.date: 08/18/2021
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 507077913a672da6f9572a283367c5713a9d5e39
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128603512"
---
# <a name="configure-a-lifecycle-management-policy"></a>Configurar una directiva de administración del ciclo de vida

La administración del ciclo de vida de Azure Storage ofrece una directiva basada en reglas que se puede usar para trasladar los datos de blob al nivel de acceso adecuado y para hacer que los datos expiren cuando finalice su ciclo de vida. Una directiva de ciclo de vida actúa en un blob base y, opcionalmente, en las versiones o instantáneas del blob. Para obtener más información sobre las directivas de administración del ciclo de vida, consulte [Optimizar los costes mediante la administración automática del ciclo de vida de los datos](lifecycle-management-overview.md).

Una directiva de administración del ciclo de vida consta de una o varias reglas que definen el conjunto de acciones que deben realizarse en función de si una condición se cumple o no. En un blob base, se puede comprobar una de estas dos condiciones:

- Número de días desde que el blob se modificó por última vez.
- Número de días desde que se accedió al blob por última vez. Para usar esta condición en una acción, antes hay que [habilitar opcionalmente el seguimiento de la hora de acceso](#optionally-enable-access-time-tracking).

Si la condición seleccionada se cumple, la directiva de administración realiza la acción especificada. Por ejemplo, si se ha definido una acción para mover un blob de nivel de acceso frecuente al nivel de acceso esporádico si no se ha modificado durante 30 días, la directiva de administración del ciclo de vida moverá el blob 30 días después de la última operación de escritura en ese blob.

En una instantánea o versión de blob, la condición que se comprueba es el número de días desde que se creó la instantánea o la versión.

## <a name="optionally-enable-access-time-tracking"></a>Habilitar el seguimiento de hora de acceso opcionalmente

Antes de configurar una directiva de administración del ciclo de vida, puede optar por habilitar el seguimiento de la hora de acceso a blobs. Cuando el seguimiento de la hora de acceso se habilita, una directiva de administración del ciclo de vida puede incluir una acción basada en el momento en que se tuvo acceso por última vez al blob por motivo de una operación de lectura o escritura.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Haga lo siguiente para habilitar el seguimiento de la hora del último acceso con Azure Portal:

1. Vaya a la cuenta de almacenamiento en Azure Portal.
1. En la sección **Administración de datos**, seleccione **Enable access tracking** (Habilitar seguimiento de acceso).

    :::image type="content" source="media/lifecycle-management-policy-configure/last-access-tracking-enable.png" alt-text="Captura de pantalla que muestra cómo habilitar el seguimiento de la hora del último acceso en Azure Portal":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para habilitar el seguimiento de la hora del último acceso con PowerShell, llame al comando [Enable-AzStorageBlobLastAccessTimeTracking](/powershell/module/az.storage/enable-azstoragebloblastaccesstimetracking), como se muestra en el siguiente ejemplo. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```powershell
# Initialize these variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Enable-AzStorageBlobLastAccessTimeTracking  -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -PassThru
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para habilitar el seguimiento de la hora del último acceso con la CLI de Azure, llame al comando [az storage account blob-service-properties update](/cli/azure/storage/account/blob-service-properties#az_storage_account_blob_service_properties_update), como se muestra en el siguiente ejemplo. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az storage account blob-service-properties update \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --enable-last-access-tracking true
```

# <a name="template"></a>[Plantilla](#tab/template)

Para habilitar el seguimiento de la hora del último acceso en una cuenta de almacenamiento nueva o existente con una plantilla de Azure Resource Manager, incluya el objeto **lastAccessTimeTrackingPolicy** en la definición de la plantilla. Para obtener más información, consulte [Microsoft.Storage/storageAccounts/blobServices 2021-02-01 - Referencia de plantillas de ARM y Bicep](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/blobservices?tabs=json). El objeto **lastAccessTimeTrackingPolicy** está disponible en la API de REST del proveedor de recursos de Azure Storage en sus versiones 2019-06-01 y posteriores.

---

Use la propiedad **daysAfterLastAccessTimeGreaterThan** para especificar el número de días transcurridos desde el último acceso a partir del cual se debe realizar una acción en un blob.

## <a name="create-or-manage-a-policy"></a>Crear administrar una directiva

Una directiva de administración del ciclo de vida se puede agregar, editar o quitar con Azure Portal, PowerShell, la CLI de Azure o una plantilla de Azure Resource Manager.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Hay dos formas de agregar una directiva en Azure Portal.

- [Vista de lista](#list-view)
- [Vista de código](#code-view)

#### <a name="list-view"></a>Vista de lista

1. En Azure Portal, vaya a la cuenta de almacenamiento.
1. En **Administración de datos**, seleccione **Administración del ciclo de vida** para ver o cambiar las directivas de administración del ciclo de vida.
1. Seleccione la pestaña **Vista de lista**.

1. Seleccione **Agregar una regla** y asigne un nombre a la regla en el formulario **Detalles**. También puede establecer valores en **Ámbito de la regla**, **Tipo de blob** y **Subtipo de blob**. En el ejemplo siguiente se establece el ámbito para filtrar los blobs. Esto hace que se agregue la pestaña **Conjunto de filtros**.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-details.png" alt-text="Página de detalles de agregar una regla en la administración del ciclo de vida de Azure Portal":::

1. Seleccione **Base blobs** (Blobs base) para establecer las condiciones de la regla. En el siguiente ejemplo, los blobs se mueven al almacenamiento esporádico si no se han modificado durante 30 días.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-base-blobs.png" alt-text="Página de blobs base de administración del ciclo de vida en Azure Portal":::

   La opción **Último acceso** solamente está disponible si se ha habilitado el seguimiento de la hora de acceso. Para obtener información sobre cómo habilitar el seguimiento del acceso, consulte [Habilitar el seguimiento de hora de acceso opcionalmente](#optionally-enable-access-time-tracking).

1. Si seleccionó **Limitar blobs con filtros** en la página **Detalles**, seleccione **Conjunto de filtros** para agregar un filtro opcional. En el siguiente ejemplo se filtran los blobs cuyos nombres comienzan por *log* en un contenedor denominado *sample-container*.

   :::image type="content" source="media/lifecycle-management-policy-configure/lifecycle-management-filter-set.png" alt-text="Página del conjunto de filtros de administración del ciclo de vida en Azure Portal":::

1. Seleccione **Agregar** para agregar la nueva directiva.

#### <a name="code-view"></a>vista Código

1. En Azure Portal, vaya a la cuenta de almacenamiento.
1. En **Administración de datos**, seleccione **Administración del ciclo de vida** para ver o cambiar las directivas de administración del ciclo de vida.
1. Seleccione la pestaña **Vista Código**. En esta pestaña se puede definir una directiva de administración del ciclo de vida en JSON.

El siguiente JSON de ejemplo define una directiva de administración del ciclo de vida que traslada un blob en bloques cuyo nombre comienza por *log* al nivel de acceso esporádico si han transcurrido más de 30 días desde que el blob se modificó.

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "sample-container/log"
             ]
           }
         }
       }
     ]
   }
   ```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Use estos comandos para agregar una directiva de administración del ciclo de vida con PowerShell:

- Llame al comando [Add-AzStorageAccountManagementPolicyAction](/powershell/module/az.storage/add-azstorageaccountmanagementpolicyaction) para definir las acciones que componen una regla.
- Llame al comando [New-AzStorageAccountManagementPolicyFilter](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyfilter) para especificar uno o varios filtros de una regla.
- Llame al comando [New-AzStorageAccountManagementPolicyRule](/powershell/module/az.storage/new-azstorageaccountmanagementpolicyrule) para crear una regla de directiva que incluya acciones y filtros opcionales.
- Llame al comando [Set-AzStorageAccountManagementPolicy](/powershell/module/az.storage/set-azstorageaccountmanagementpolicy) para crear la directiva en la cuenta de almacenamiento.

En el siguiente ejemplo se muestra cómo usar cada uno de estos comandos para crear una directiva de administración del ciclo de vida. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```powershell
# Initialize the following variables with your values.
$rgName = "<resource-group>"
$accountName = "<storage-account>"

# Create a new action object.
$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete `
    -daysAfterModificationGreaterThan 180
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToArchive `
    -daysAfterModificationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BaseBlobAction TierToCool `
    -daysAfterModificationGreaterThan 30
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -SnapshotAction Delete `
    -daysAfterCreationGreaterThan 90
Add-AzStorageAccountManagementPolicyAction -InputObject $action `
    -BlobVersionAction TierToArchive `
    -daysAfterCreationGreaterThan 90

# Create a new filter object.
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd `
    -BlobType blockBlob

# Create a new rule object.
$rule1 = New-AzStorageAccountManagementPolicyRule -Name sample-rule `
    -Action $action `
    -Filter $filter

# Create the policy.
Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgName `
    -StorageAccountName $accountName `
    -Rule $rule1
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para agregar una directiva de administración del ciclo de vida con la CLI de Azure, escriba la directiva en un archivo JSON y llame al comando [az storage account management-policy create](/cli/azure/storage/account/management-policy#az_storage_account_management_policy_create) para crear la directiva.

En el siguiente ejemplo se muestra cómo usar cada uno de estos comandos para crear una directiva de administración del ciclo de vida. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az storage account management-policy create \
    --account-name <storage-account> \
    --policy @policy.json \
    --resource-group <resource-group>
```

# <a name="template"></a>[Plantilla](#tab/template)

Para definir una directiva de administración del ciclo de vida con una plantilla de Azure Resource Manager, incluya el objeto **Microsoft.Storage/storageAccounts/managementPolicies**. Para obtener información detallada sobre la configuración, consulte [Microsoft.Storage/storageAccounts/managementPolicies 2021-02-01 - Referencia de plantillas de ARM y Bicep](/azure/templates/microsoft.storage/2021-02-01/storageaccounts/managementpolicies?tabs=json). El objeto **Microsoft.Storage/storageAccounts/managementPolicies** está disponible en la API de REST del proveedor de recursos de Azure Storage en sus versiones 2018-11-01 y posteriores.

---

Una directiva de administración del ciclo de vida se debe leer o escribir completamente. No se admiten las actualizaciones parciales.

> [!NOTE]
> Si habilita reglas de firewall para la cuenta de almacenamiento, puede que se bloqueen las solicitudes de administración del ciclo de vida. Puede desbloquear estas solicitudes proporcionando excepciones para los servicios de confianza de Microsoft. Para obtener más información, consulte la sección **Excepciones** de [Configuración de redes virtuales y firewalls de Azure Storage](../common/storage-network-security.md#exceptions).

## <a name="see-also"></a>Consulte también

- [Optimizar los costes mediante la administración automática del ciclo de vida de los datos](lifecycle-management-overview.md)
- [Niveles de acceso de Azure Blob Storage: frecuente, esporádico y archivo](storage-blob-storage-tiers.md)
