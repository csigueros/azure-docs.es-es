---
title: Introducción a la recuperación del HSM administrado de Azure Key Vault | Microsoft Docs
description: Las características de recuperación del HSM administrado están diseñadas para evitar la eliminación accidental o malintencionada del recurso y las claves del HSM.
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: how-to
ms.author: mbaldwin
author: mbaldwin
ms.date: 06/01/2021
ms.openlocfilehash: c0688d40e0ccb71b98e598da7d5ff8100b735229
ms.sourcegitcommit: 86ca8301fdd00ff300e87f04126b636bae62ca8a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/16/2021
ms.locfileid: "122195152"
---
# <a name="managed-hsm-soft-delete-and-purge-protection"></a>Eliminación temporal y protección de purga del HSM administrado

En este artículo se describen dos características de recuperación del HSM administrado: la eliminación temporal y la protección de purga. Se proporciona información general sobre estas características y se muestra cómo administrarlas mediante la CLI de Azure y Azure PowerShell.

Para más información, consulte [¿Qué es HSM administrado de Azure Key Vault?](overview.md)

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. [cree una de forma gratuita](https://azure.microsoft.com/free/dotnet).
* El [módulo de PowerShell](/powershell/azure/install-az-ps).
* La CLI de Azure 2.25.0 o posterior. Ejecute `az --version` para determinar qué versión tiene. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).
* Un HSM administrado. Puede crear uno mediante la [CLI de Azure](./quick-create-cli.md) o [Azure PowerShell](./quick-create-powershell.md).
* Los usuarios necesitarán los siguientes permisos para realizar operaciones en los HSM o las claves eliminados temporalmente:

  | Asignación de roles | Descripción |
  |---|---|
  |[Colaborador de HSM administrado](../../role-based-access-control/built-in-roles.md#managed-hsm-contributor)|Enumeración, recuperación y purga de HSM eliminados temporalmente|
  |[Managed HSM Crypto User](./built-in-roles.md)|Enumeración de claves eliminadas temporalmente|
  |[Managed HSM Crypto Officer](./built-in-roles.md)|Purga y recuperación de claves eliminadas temporalmente|



## <a name="what-are-soft-delete-and-purge-protection"></a>¿Qué son la eliminación temporal y la protección de purga?

La [eliminación temporal](soft-delete-overview.md) y la protección de purga son características de recuperación.


La *eliminación temporal* está diseñada para evitar la eliminación accidental del HSM y las claves. Esta eliminación funciona como una papelera de reciclaje. Cuando se elimina un HSM o una clave, se puede recuperar durante el período de retención que se haya configurado o bien durante un período predeterminado de 90 días. Los HSM y las claves con estado de eliminación temporal también se pueden *purgar*, lo que significa que se eliminan de forma permanente. La purga permite volver a crear los HSM y las claves con el mismo nombre que el elemento purgado. Tanto la recuperación como la eliminación de HSM y claves requieren asignaciones específicas de rol. La eliminación temporal no se puede deshabilitar.

> [!NOTE]
> Dado que los recursos subyacentes permanecen asignados al HSM incluso cuando se encuentra en un estado eliminado, el recurso de HSM seguirá acumulando cargos por hora mientras se encuentre en ese estado.

Los nombres de los HSM administrados son únicos globalmente en cada entorno de nube. Por lo tanto, no puede crear un HSM administrado con el mismo nombre que el que existe en un estado de eliminación temporal. De forma similar, los nombres de las claves son únicos dentro de un HSM. Por lo tanto, no puede crear una clave con el mismo nombre que el que existe en un estado de eliminación temporal.

Para más información, consulte [Visión general de la eliminación temporal de HSM administrado](soft-delete-overview.md).

La *protección de purga* está diseñada para evitar la eliminación de los HSM y las claves por parte de un agente interno malintencionado. Es como una papelera de reciclaje con un bloqueo de duración definida. Puede recuperar los elementos en cualquier momento durante el período de retención que haya configurado. No podrá purgar ni eliminar de forma permanente un HSM o una clave hasta que transcurra el período de retención. Cuando finalice el período de retención, el HSM o la clave se purgarán automáticamente.

> [!NOTE]
> No existe ningún rol ni permiso de administrador que pueda invalidar, deshabilitar o eludir la protección de purga. *Si se habilita la protección de purga, ningún usuario ni Microsoft podrán deshabilitarla o invalidarla.* Por lo tanto, debe recuperar un HSM eliminado o esperar a que finalice el período de retención para poder reutilizar el nombre del HSM.

## <a name="manage-keys-and-managed-hsms"></a>Administración de claves y HSM administrados

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

### <a name="managed-hsms-cli"></a>HSM administrados (CLI) 

* Para comprobar el estado de la eliminación temporal y la protección de purga de un HSM administrado:

    ```azurecli
    az keyvault show --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```

* Para eliminar un HSM:

    ```azurecli
    az keyvault delete --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME}
    ```
    
  Esta acción se puede recuperar porque la eliminación temporal está activada de manera predeterminada.

* Para enumerar todos los HSM eliminados temporalmente:

    ```azurecli
    az keyvault list-deleted --subscription {SUBSCRIPTION ID} --resource-type hsm
    ```

* Para recuperar un HSM eliminado temporalmente:

    ```azurecli
    az keyvault recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```


* Para purgar un HSM eliminado temporalmente:

    ```azurecli
    az keyvault purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --location {LOCATION}
    ```
    > [!WARNING] 
    > Esta operación eliminará de forma permanente el HSM.

* Para habilitar la protección de purga en un HSM:

    ```azurecli
    az keyvault update-hsm --subscription {SUBSCRIPTION ID} -g {RESOURCE GROUP} --hsm-name {HSM NAME} --enable-purge-protection true
    ```

### <a name="keys-cli"></a>Claves (CLI)

* Para eliminar una clave:

    ```azurecli
    az keyvault key delete --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* Para enumerar las claves eliminadas:

    ```azurecli
    az keyvault key list-deleted --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME}
    ```

* Para recuperar una clave eliminada:

    ```azurecli
    az keyvault key recover --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```

* Para purgar una clave eliminada temporalmente: 

    ```azurecli
    az keyvault key purge --subscription {SUBSCRIPTION ID} --hsm-name {HSM NAME} --name {KEY NAME}
    ```
    > [!WARNING] 
    > Esta operación eliminará de forma permanente la clave.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

### <a name="managed-hsms-powershell"></a>HSM administrados (PowerShell)

* Para comprobar el estado de la eliminación temporal y la protección de purga de un HSM administrado:

    ```powershell
    Get-AzKeyVaultManagedHsm -Name "ContosoHSM"
    ```

* Para eliminar un HSM:

    ```powershell
    Remove-AzKeyVaultManagedHsm -Name 'ContosoHSM'
    ```
  Esta acción se puede recuperar porque la eliminación temporal está activada de manera predeterminada.

### <a name="keys-powershell"></a>Claves (PowerShell)

* Para eliminar una clave:

  ```powershell
  Remove-AzKeyVaultKey -HsmName ContosoHSM -Name 'MyKey'
  ```

* Para enumerar todas las claves eliminadas: 

  ```powershell
  Get-AzKeyVaultKey -HsmName ContosoHSM -InRemovedState
  ```

* Para recuperar una clave eliminada temporalmente:

    ```powershell
    Undo-AzKeyVaultKeyRemoval -HsmName ContosoHSM -Name ContosoFirstKey
    ```

* Para purgar una clave eliminada temporalmente:

    ```powershell
    Remove-AzKeyVaultKey -HsmName ContosoHSM -Name ContosoFirstKey -InRemovedState
    ```
    > [!WARNING] 
    > Esta operación eliminará de forma permanente la clave.
    
---

## <a name="next-steps"></a>Pasos siguientes

- [Az.KeyVault](/powershell/module/az.keyvault)
- [Comandos de la CLI de Azure para Key Vault](/cli/azure/keyvault)
- [Copia de seguridad completa y restauración](backup-restore.md)
- [Registro de Managed HSM](logging.md)
