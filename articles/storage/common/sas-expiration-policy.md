---
title: Creación de una directiva de expiración para firmas de acceso compartido
titleSuffix: Azure Storage
description: Creación de una directiva en la cuenta de almacenamiento que defina el período de tiempo durante el que una firma de acceso compartido (SAS) debe ser válida. Aprenda a supervisar las infracciones de directivas para corregir los riesgos de seguridad.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2021
ms.author: tamram
ms.reviewer: dineshm
ms.subservice: common
ms.openlocfilehash: 8d9a381e80c829acc2f87aa6a856a651b19315ae
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128709063"
---
# <a name="create-an-expiration-policy-for-shared-access-signatures"></a>Creación de una directiva de expiración para firmas de acceso compartido

Puede usar una firma de acceso compartido (SAS) para delegar el acceso a los recursos a su cuenta de Azure Storage. Un token de SAS incluye el recurso de destino, los permisos concedidos y el intervalo durante el cual se permite el acceso. Los procedimientos recomendados sugieren limitar el intervalo de una SAS en caso de que se vea comprometida. Al establecer una directiva de expiración de SAS para las cuentas de almacenamiento, puede proporcionar un límite de expiración superior recomendado cuando un usuario crea una SAS.

Una directiva de expiración de SAS no impide que un usuario cree una SAS con una expiración que supere el límite recomendado por la directiva. Cuando un usuario crea una SAS que infringe la directiva, verá una advertencia junto con el intervalo máximo recomendado. Si ha configurado una configuración de diagnóstico para el registro con Azure Monitor, Azure Storage escribe en una propiedad en los registros cada vez que un usuario crea una SAS que expira después del intervalo recomendado.

Para obtener más información sobre las firmas de acceso compartido, consulte [Otorgar acceso limitado a recursos de Azure Storage con firmas de acceso compartido (SAS)](storage-sas-overview.md).

## <a name="create-a-sas-expiration-policy"></a>Creación de una directiva de expiración de SAS

Al crear una directiva de expiración de SAS en una cuenta de almacenamiento, la directiva se aplica a cada tipo de SAS que se puede crear en esa cuenta de almacenamiento, incluida una SAS de servicio, una SAS de delegación de usuarios o una SAS de cuenta.

Para configurar una directiva de expiración de SAS para una cuenta de almacenamiento, use Azure Portal, PowerShell o la CLI de Azure.

### <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

Para crear una directiva de expiración de SAS en Azure Portal, siga estos pasos:

1. Vaya a la cuenta de almacenamiento en Azure Portal.
1. En **Configuración**, seleccione **Configuración**.
1. Busque la configuración **Permitir límite superior recomendado para el intervalo de expiración de firma de acceso compartido (SAS)** y establézcalo como **Habilitado**.
1. Especifique el intervalo recomendado para las nuevas firmas de acceso compartido que se crean en los recursos de esta cuenta de almacenamiento.

    :::image type="content" source="media/sas-expiration-policy/configure-sas-expiration-policy-portal.png" alt-text="Captura de pantalla que muestra cómo configurar una directiva de expiración de SAS en el Azure Portal":::

1. Seleccione el botón **Save** (Guardar) para guardar los cambios.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para crear una directiva de expiración de SAS, use el comando [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) y, a continuación, establezca el parámetro `-SasExpirationPeriod` en el número de días, horas, minutos y segundos que un token de SAS puede estar activo desde el momento en que se firma una SAS. La cadena que proporciona el parámetro `-SasExpirationPeriod` usa el formato siguiente: `<days>.<hours>:<minutes>:<seconds>`. Por ejemplo, si quisiera que la SAS expirase 1 día, 12 horas, 5 minutos y 6 segundos después de que se firmara, usaría la cadena `1.12:05:06`.

```powershell
$account = Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account-name> `
    -SasExpirationPeriod <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> También puede establecer la directiva de expiración de SAS al crear una cuenta de almacenamiento si establece el parámetro `-SasExpirationPeriod` del comando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount).

Para comprobar que se ha aplicado la directiva, use la propiedad `SasPolicy` de [PSStorageAccount](/dotnet/api/microsoft.azure.commands.management.storage.models.psstorageaccount) devuelta a la variable `$account` en el comando anterior. 
  
```powershell
$account.SasPolicy
```

El período de expiración de SAS aparece en la salida de la consola.

> [!div class="mx-imgBorder"]
> ![Período de expiración de SAS](./media/storage-sas-expiration-policy/sas-policy-console-output.png)

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para crear una directiva de expiración de SAS, use el comando [az storage account update](/cli/azure/storage/account#az_storage_account_update) y, a continuación, establezca el parámetro `--key-exp-days` en el número de días, horas, minutos y segundos que un token de SAS puede estar activo desde el momento en que se firma una SAS. La cadena que proporciona el parámetro `--key-exp-days` usa el formato siguiente: `<days>.<hours>:<minutes>:<seconds>`. Por ejemplo, si quisiera que la SAS expirase 1 día, 12 horas, 5 minutos y 6 segundos después de que se firmara, usaría la cadena `1.12:05:06`.

```azurecli-interactive
az storage account update \
  --name <storage-account> \
  --resource-group <resource-group> \
  --sas-exp <days>.<hours>:<minutes>:<seconds>
```

> [!TIP]
> También puede establecer la directiva de expiración de SAS al crear una cuenta de almacenamiento si establece el parámetro `--key-exp-days` del comando [az storage account create](/cli/azure/storage/account#az_storage_account_create).

Para comprobar que se ha aplicado la directiva, llame al comando [az storage account show](/cli/azure/storage/account#az_storage_account_show) y use la cadena `{SasPolicy:sasPolicy}` para el parámetro `-query`.
  
```azurecli-interactive
az storage account show \
  --name <storage-account> \
  --resource-group <resource-group> \
  --query "{SasPolicy:sasPolicy}"
```

El período de expiración de SAS aparece en la salida de la consola.

```json
{
  "SasPolicy": {
    "expirationAction": "Log",
    "sasExpirationPeriod": "1.12:05:06"
  }
}
```

---

## <a name="query-logs-for-policy-violations"></a>Consulta de registros de infracciones de la directiva

Para registrar la creación de una SAS que sea válida durante un intervalo más largo del que recomienda la directiva de expiración de SAS, cree primero una configuración de diagnóstico que envíe registros a un área de trabajo de Azure Log Analytics. Para más información, consulte [Enviar registros a Azure Log Analytics](../blobs/monitor-blob-storage.md#send-logs-to-azure-log-analytics).

A continuación, use una consulta de registro de Azure Monitor para determinar si una SAS ha expirado. Cree una nueva consulta en el área de trabajo de Log Analytics, agregue el siguiente texto de consulta y presione **Ejecutar**.

```kusto
StorageBlobLogs | where SasExpiryStatus startswith "Policy Violated" 
```

## <a name="see-also"></a>Consulte también

- [Grant limited access to Azure Storage resources using shared access signatures (SAS)](storage-sas-overview.md) (Otorgar acceso limitado a recursos de Azure Storage con firmas de acceso compartido [SAS])
- [Create a service SAS](/rest/api/storageservices/create-service-sas) (Creación de una SAS de servicio)
- [Creación de una SAS de delegación de usuario](/rest/api/storageservices/create-user-delegation-sas)
- [Creación de una SAS de cuenta](/rest/api/storageservices/create-account-sas)