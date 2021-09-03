---
title: Administración de claves de acceso de cuentas
titleSuffix: Azure Storage
description: Obtenga información sobre cómo ver, administrar y rotar las claves de acceso de la cuenta de almacenamiento.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: tamram
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 600c651601e4281b717c1c8fa7808f3663be4af6
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113093950"
---
# <a name="manage-storage-account-access-keys"></a>Administración de las claves de acceso de la cuenta de almacenamiento

Cuando se crea una cuenta de almacenamiento, Azure genera dos claves de acceso de cuenta de almacenamiento de 512 bits. Estas claves se pueden usar para autorizar el acceso a los datos de su cuenta de almacenamiento mediante la autorización con clave compartida.

Microsoft recomienda usar Azure Key Vault para administrar las claves de acceso, así como rotar y volver a generar las claves de forma periódica. El uso de Azure Key Vault facilita la rotación de las claves sin ocasionar interrupciones en las aplicaciones. También puede rotar las claves de forma manual.

[!INCLUDE [storage-account-key-note-include](../../../includes/storage-account-key-note-include.md)]

## <a name="view-account-access-keys"></a>Visualización de claves de acceso de cuenta

Puede ver y copiar las claves de acceso a la cuenta con Azure Portal, PowerShell o la CLI de Azure. Azure Portal también proporciona una cadena de conexión para la cuenta de almacenamiento que puede copiar.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para ver y copiar la cadena de conexión o las claves de acceso de la cuenta de almacenamiento desde Azure Portal:

1. Vaya a la cuenta de almacenamiento en [Azure Portal](https://portal.azure.com).

2. En **Configuración**, seleccione **Claves de acceso**. Aparecen las claves de acceso de la cuenta, así como la cadena de conexión completa de cada clave.

3. Busque el valor de **Clave** en **key1** y haga clic en el botón **Copiar** para copiar la clave de cuenta.

4. Como alternativa, puede copiar la cadena de conexión completa. Busque el valor de **Cadena de conexión** en **key1** y haga clic en el botón **Copiar** para copiar la cadena de conexión.

    :::image type="content" source="media/storage-account-keys-manage/portal-connection-string.png" alt-text="Captura de pantalla que muestra cómo ver las claves de acceso en Azure Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para recuperar las claves de acceso a la cuenta con PowerShell, llame al comando [Get-AzStorageAccountKey](/powershell/module/az.Storage/Get-azStorageAccountKey).

En el ejemplo siguiente se recupera la primera clave. Para recuperar la segunda clave, use `Value[1]` en lugar de `Value[0]`. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores.

```powershell
$storageAccountKey = `
    (Get-AzStorageAccountKey `
    -ResourceGroupName <resource-group> `
    -Name <storage-account>).Value[0]
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para enumerar las claves de acceso a la cuenta con la CLI de Azure, llame al comando [az storage account keys list](/cli/azure/storage/account/keys#az_storage_account_keys_list), tal como se muestra en el ejemplo siguiente. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores. 

```azurecli-interactive
az storage account keys list \
  --resource-group <resource-group> \
  --account-name <storage-account>
```

---

Puede utilizar cualquiera de las dos claves para acceder a Azure Storage, pero en general es recomendable usar la primera clave y reservar el uso de la segunda clave para cuando se roten las claves.

Para ver o leer las claves de acceso de una cuenta, el usuario debe ser un administrador de servicios o debe tener asignado un rol de Azure que incluya **Microsoft.Storage/storageAccounts/listkeys/action**. Algunos de los roles integrados de Azure que incluyen esta acción son **Propietario**, **Colaborador** y **Rol de servicio del operador de claves de cuentas de almacenamiento**. Para obtener más información sobre el rol de administrador de servicios, consulte [Roles de administrador de suscripciones clásico, de Azure y de Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Para obtener información detallada sobre los roles integrados de RBAC para Azure Storage, consulte la sección **Almacenamiento** en [Roles integrados de Azure para RBAC de Azure](../../role-based-access-control/built-in-roles.md#storage).

## <a name="use-azure-key-vault-to-manage-your-access-keys"></a>Uso de Azure Key Vault para administrar las claves de acceso

Microsoft recomienda usar Azure Key Vault para administrar y rotar las claves de acceso. La aplicación puede acceder de forma segura a las claves en Key Vault, de modo que puede evitar almacenarlas en el código de la aplicación. Para obtener más información sobre el uso de Key Vault en la administración de claves, consulte los siguientes artículos:

- [Administración de claves de cuenta de almacenamiento con Azure Key Vault y Azure PowerShell](../../key-vault/secrets/overview-storage-keys-powershell.md)
- [Administración de claves de cuenta de almacenamiento con Azure Key Vault y la CLI de Azure](../../key-vault/secrets/overview-storage-keys.md)

## <a name="manually-rotate-access-keys"></a>Rotación manual de las claves de acceso

Microsoft recomienda rotar las claves de acceso periódicamente para ayudar a proteger la cuenta de almacenamiento. De ser posible, use Azure Key Vault para administrar las claves de acceso. Si no está usando Key Vault, deberá rotar las claves manualmente.

Para poder rotar las claves, se asignan dos claves de acceso. De este modo, se garantiza que la aplicación mantiene el acceso a Azure Storage a lo largo del proceso.

> [!WARNING]
> La regeneración de las claves de acceso puede afectar a aplicaciones o servicios de Azure que dependen de la clave de cuenta de almacenamiento. Los clientes que usan la clave de cuenta para acceder a la cuenta de almacenamiento se deben actualizar para usar la nueva clave, lo que incluye los servicios multimedia, las aplicaciones de nube, escritorio y móviles y las aplicaciones de interfaz gráfica de usuario de Azure Storage, como el [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/).

Si tiene previsto girar manualmente las claves de acceso, Microsoft recomienda establecer una directiva de expiración de claves y, después, usar consultas en Azure Monitor para determinar cuándo es el momento de girar una clave de acceso.

### <a name="create-a-key-expiration-policy"></a>Creación de una directiva de expiración de clave

#### <a name="portal"></a>[Portal](#tab/azure-portal)

La capacidad de establecer una directiva de expiración de claves mediante Azure Portal todavía no está disponible. Puede usar la CLI de Azure o PowerShell.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para crear una directiva de expiración de claves, use el comando [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) y establezca el parámetro `-KeyExpirationPeriodInDay` en el número de días que una clave de acceso puede estar activa antes de que se deba girar. 

```powershell
$account = Set-AzStorageAccount -ResourceGroupName <resource-group> -Name `
    <storage-account-name>  -KeyExpirationPeriodInDay <period-in-days> 
```

> [!TIP]
> También puede establecer la directiva de expiración de claves al crear una cuenta de almacenamiento si establece el parámetro `-KeyExpirationPeriodInDay` del comando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

Para comprobar que se ha aplicado la directiva, use la propiedad `KeyPolicy` de [PSStorageAccount](/dotnet/api/microsoft.azure.commands.management.storage.models.psstorageaccount) devuelta a la variable `$account` en el comando anterior. 
  
```powershell
$account.KeyPolicy
``` 

El período de expiración de la clave aparece en la salida de la consola.

> [!div class="mx-imgBorder"]
> ![Período de expiración de la clave de acceso](./media/storage-account-keys-manage/key-policy-powershell.png)

Es posible que quiera girar las claves existentes si han estado activas durante más tiempo que el período de expiración. Para averiguar cuándo se ha creado una clave, use la propiedad `KeyCreationTime`. 
  
```powershell
$account.KeyCreationTime
``` 

La hora de creación de la clave de acceso para las dos claves de acceso aparece en la salida de la consola.

> [!div class="mx-imgBorder"]
> ![Horas de creación de las claves de acceso](./media/storage-account-keys-manage/key-creation-time-powershell.png)


#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para crear una directiva de expiración de claves en cuentas de almacenamiento existentes, use el comando [az storage account update](/cli/azure/storage/account#az_storage_account_update) y establezca el parámetro `--key-exp-days` en el número de días que una clave de acceso puede estar activa antes de que se deba girar. 

```azurecli-interactive
az storage account update \
  -n <storage-account-name> \
  -g <resource-group> --key-exp-days <period-in-days>
```

> [!TIP]
> También puede establecer la directiva de expiración de claves al crear una cuenta de almacenamiento si establece el parámetro `-KeyExpirationPeriodInDay` del comando [az storage account create](/cli/azure/storage/account#az_storage_account_create).

Para comprobar que se ha aplicado la directiva, llame al comando [az storage account show](/cli/azure/storage/account#az_storage_account_show) y use la cadena `{KeyPolicy:keyPolicy}` para el parámetro `-query`.
  
```azurecli-interactive
az storage account show \
  -n <storage-account-name> \
  -g <resource-group-name> \
  --query "{KeyPolicy:keyPolicy}"
```

El período de expiración de la clave aparece en la salida de la consola.

```json
{
  "KeyPolicy": {
    "keyExpirationPeriodInDays": 5
  }
}
```


Es posible que quiera girar las claves existentes si han estado activas durante más tiempo que el período de expiración. Para averiguar cuándo se ha creado una clave, use el comando [az storage account show](/cli/azure/storage/account#az_storage_account_show) y después la cadena `keyCreationTime` para el parámetro -query.
  
```azurecli-interactive
az storage account show \
  -n <storage-account-name> \
  -g <resource-group-name> \
  --query "keyCreationTime"
```

---

### <a name="query-for-policy-violations"></a>Consulta de infracciones de la directiva

Si crea una configuración de diagnóstico que [envía registros al área de trabajo de Azure Log Analytics](../blobs/monitor-blob-storage.md#send-logs-to-azure-log-analytics), puede usar una consulta de registro de Azure Monitor para determinar si una clave ha expirado. 

Para determinar si una clave ha expirado, escriba la siguiente consulta en la barra de **búsqueda de registros**.

```Kusto
StorageBlobLogs | where KeyExpiryStatus startsWith "Policy Violated". 
```

También puede crear una consulta que le ayude a determinar si una consulta está a punto de expirar. En la consulta siguiente se proporciona esta información.

```Kusto
resources  
| where type =~ 'microsoft.storage/storageAccounts' 
| extend days = datetime_diff('day', now(), todatetime(parse_json(properties).keyCreationTime)) 
| extend KeyExpiryStatus = iff(days > 180, "Policy Violated", "") 
| project name, days, KeyExpiryStatus  
```

### <a name="rotate-access-keys"></a>Giro de claves de acceso

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para rotar las claves de acceso a la cuenta de almacenamiento en Azure Portal:

1. Actualice las cadenas de conexión en el código de su aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de almacenamiento.

2. Vaya a la cuenta de almacenamiento en [Azure Portal](https://portal.azure.com).

3. En **Configuración**, seleccione **Claves de acceso**.

4. Para regenerar la clave de acceso principal de la cuenta de almacenamiento, seleccione el botón **Regenerar** que se encuentra junto a la clave de acceso principal.

5. Actualice las cadenas de conexión en su código para hacer referencia a la nueva clave de acceso primaria.

6. Vuelva a generar la clave de acceso secundaria de la misma forma.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para rotar las claves de acceso a la cuenta de almacenamiento con PowerShell:

1. Actualice las cadenas de conexión en el código de su aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de almacenamiento.

2. Llame al comando [New-AzStorageAccountKey](/powershell/module/az.storage/new-azstorageaccountkey) para regenerar la clave de acceso principal, tal como se muestra en este ejemplo:

    ```powershell
    New-AzStorageAccountKey -ResourceGroupName <resource-group> `
      -Name <storage-account> `
      -KeyName key1
    ```

3. Actualice las cadenas de conexión en su código para hacer referencia a la nueva clave de acceso primaria.

4. Vuelva a generar la clave de acceso secundaria de la misma forma. Para regenerar la clave secundaria, use `key2` como el nombre de clave en lugar de `key1`.

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para rotar las claves de acceso a la cuenta de almacenamiento con la CLI de Azure:

1. Actualice las cadenas de conexión en el código de su aplicación para hacer referencia a la clave de acceso secundaria de la cuenta de almacenamiento.

2. Llame al comando [az storage account keys renew](/cli/azure/storage/account/keys#az_storage_account_keys_renew) para regenerar la clave de cuenta principal, tal como se muestra en este ejemplo:

    ```azurecli-interactive
    az storage account keys renew \
      --resource-group <resource-group> \
      --account-name <storage-account>
      --key primary
    ```

1. Actualice las cadenas de conexión en su código para hacer referencia a la nueva clave de acceso primaria.

2. Vuelva a generar la clave de acceso secundaria de la misma forma. Para regenerar la clave secundaria, use `key2` como el nombre de clave en lugar de `key1`.

---

> [!NOTE]
> Microsoft recomienda usar solo una de las claves en todas las aplicaciones al mismo tiempo. Si utiliza la Clave 1 en algunos lugares y la Clave 2 en otros, no podrá rotar las claves sin que alguna aplicación pierda el acceso.

Para rotar las claves de acceso de una cuenta, el usuario debe ser un administrador de servicios o debe tener asignado un rol de Azure que incluya **Microsoft.Storage/storageAccounts/regeneratekey/Action**. Algunos de los roles integrados de Azure que incluyen esta acción son **Propietario**, **Colaborador** y **Rol de servicio del operador de claves de cuentas de almacenamiento**. Para obtener más información sobre el rol de administrador de servicios, consulte [Roles de administrador de suscripciones clásico, de Azure y de Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md). Para obtener información detallada sobre los roles integrados de Azure para Azure Storage, consulte la sección **Almacenamiento** en [Roles integrados de Azure para Azure RBAC](../../role-based-access-control/built-in-roles.md#storage).

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a las cuentas de Azure Storage](storage-account-overview.md)
- [Cree una cuenta de almacenamiento](storage-account-create.md)
