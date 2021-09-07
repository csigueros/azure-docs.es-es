---
title: Configuración de directivas de inmutabilidad para contenedores
titleSuffix: Azure Storage
description: Obtenga información sobre cómo configurar una directiva de inmutabilidad con ámbito en un contenedor. Las directivas de inmutabilidad proporcionan compatibilidad WORM (escribir una vez, leer muchas) con Blob Storage almacenando datos que no se pueden borrar ni modificar.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/16/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1cdc40b8aebe2d80553a23deec3990d4349ebd79
ms.sourcegitcommit: 2eac9bd319fb8b3a1080518c73ee337123286fa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123255620"
---
# <a name="configure-immutability-policies-for-containers"></a>Configuración de directivas de inmutabilidad para contenedores

El almacenamiento inmutable para Azure Blob Storage permite a los usuarios almacenar datos críticos para la empresa en un estado WORM (escribir una vez, leer muchas). Mientras los datos se encuentran en un estado WORM, no se pueden modificar ni eliminar durante un intervalo especificado por el usuario. Configurando directivas de inmutabilidad para los datos de blobs, puede impedir que sus datos se sobrescriban o eliminen. Las directivas de inmutabilidad incluyen directivas de retención de duración definida y suspensiones legales. Para obtener más información sobre las directivas de inmutabilidad de Blob Storage, consulte [Almacenamiento de datos críticos para la empresa con almacenamiento inmutable](immutable-storage-overview.md).

Una directiva de inmutabilidad puede tener como ámbito una determinada versión de blob (versión preliminar) o un contenedor. En este artículo se describe cómo configurar una directiva de inmutabilidad para contenedores. Para obtener información sobre cómo configurar directivas de inmutabilidad para versiones, consulte [Configuración de directivas de inmutabilidad para versiones de blob (versión preliminar)](immutable-policy-configure-version-scope.md).

## <a name="configure-a-retention-policy-on-a-container"></a>Configuración de una directiva de retención en un contenedor

Para configurar una directiva de retención de duración definida en un contenedor, use Azure Portal, PowerShell o la CLI de Azure. Puede configurar una directiva de retención para contenedores de entre 1 y 146 000 días.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para configurar una directiva de retención de duración definida en un contenedor con Azure Portal, siga los pasos que figuran a continuación:

1. Desplácese hasta el contenedor deseado.
1. Seleccione el botón **Más** a la derecha y, a continuación, **Directiva de acceso**.
1. En la sección **Almacenamiento de blobs inmutable**, seleccione **Agregar directiva**.
1. En el campo **Tipo de directiva**, seleccione **Retención de duración definida** y especifique el período de retención en días.
1. Para crear una directiva con ámbito de contenedor, no active la casilla **Habilitar inmutabilidad de nivel de versión**.
1. Si lo desea, seleccione **Permitir anexos protegidos adicionales** para permitir que se escriba en blobs en anexos que estén protegidos por una directiva de inmutabilidad. Para obtener más información, consulte [Permitir escrituras de blobs en anexos protegidos](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).

    :::image type="content" source="media/immutable-policy-configure-container-scope/configure-retention-policy-container-scope.png" alt-text="Captura de pantalla en la que se muestra cómo configurar una directiva de inmutabilidad que está en el ámbito del contenedor":::

Una vez configurada la directiva de inmutabilidad, verá que está en el ámbito del contenedor:

:::image type="content" source="media/immutable-policy-configure-container-scope/view-retention-policy-container-scope.png" alt-text="Captura de pantalla en la que se muestra una directiva de inmutabilidad existente que está en el ámbito del contenedor":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para configurar una directiva de retención de duración definida en un contenedor con PowerShell, llame al comando [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) y proporcione el intervalo de retención en días. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 10
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para configurar una directiva de retención de duración definida en un contenedor con la CLI de Azure, llame al comando [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create) y proporcione el intervalo de retención en días. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az storage container immutability-policy \
    --resource-group <resource-group>
    --account-name <storage-account> \
    --container-name <container> \
    --period 10
```

---

## <a name="modify-an-unlocked-retention-policy"></a>Modificación de una directiva de retención desbloqueada

Puede modificar una directiva de retención de duración definida desbloqueada para reducir o ampliar el intervalo de retención y permitir escrituras adicionales en blobs en anexos del contenedor. Las directivas desbloqueadas también se pueden eliminar.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para modificar una directiva de retención de duración definida desbloqueada en Azure Portal, siga estos pasos:

1. Desplácese hasta el contenedor deseado.
1. Seleccione el botón **Más** y elija **Directiva de acceso**.
1. En la sección **Versiones de blob inmutables**, localice la directiva desbloqueada ya existente. Seleccione el botón **Más** y, a continuación **Editar** en el menú.
1. Proporcione un nuevo intervalo de retención para la directiva. También puede seleccionar **Permitir anexos protegidos adicionales** para permitir que se escriba en blobs en anexos que estén protegidos.

    :::image type="content" source="media/immutable-policy-configure-container-scope/modify-retention-policy-container-scope.png" alt-text="Captura de pantalla en la que se muestra cómo modificar una directiva de retención de duración definida desbloqueada":::

Para eliminar una directiva desbloqueada, seleccione el botón **Más** y, a continuación, **Eliminar**.

> [!NOTE]
> Si desea habilitar directivas de inmutabilidad para versiones (versión preliminar), active la casilla "Habilitar inmutabilidad de nivel de versión". Para obtener información sobre cómo habilitar directivas de inmutabilidad para versiones, consulte [Configuración de directivas de inmutabilidad para versiones de blob (versión preliminar)](immutable-policy-configure-version-scope.md).

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para modificar una directiva desbloqueada, primero recupérela llamando al comando [Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy). A continuación, llame al comando [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy) para actualizar la directiva. Incluya el nuevo intervalo de retención en días y el parámetro `-ExtendPolicy`. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -ContainerName <container> `
    -ImmutabilityPeriod 21 `
    -AllowProtectedAppendWrite true `
    -Etag $policy.Etag `
    -ExtendPolicy
```

Para eliminar una directiva desbloqueada, llame al comando [Remove-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/remove-azrmstoragecontainerimmutabilitypolicy).

```azurepowershell
Remove-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container> 
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para modificar una directiva de retención de duración definida desbloqueada con la CLI de Azure, llame al comando [az storage container immutability-policy extend](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_extend) y proporcione el nuevo intervalo de retención en días. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
$etag=$(az storage container immutability-policy show /
        --account-name <storage-account> /
        --container-name <container> /
        --query etag /
        --output tsv) 

az storage container immutability-policy \
    --resource-group <resource-group>
    --account-name <storage-account> \
    --container-name <container> \
    --period 21 \
    --if-match $etag \
    --allow-protected-append-writes true 
```

Para eliminar una directiva desbloqueada, llame al comando [az storage container immutability-policy delete](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_delete).

---

## <a name="lock-a-time-based-retention-policy"></a>Bloqueo de una directiva de retención de duración definida

Cuando haya terminado de probar una directiva de retención de duración definida, puede bloquearla. Una directiva bloqueada cumple los requisitos de SEC 17a-4(f) y de otras normas. Puede ampliar el intervalo de retención de una directiva bloqueada hasta cinco veces, pero no reducirlo.

Una vez bloqueada una directiva, no se puede eliminar. Sin embargo, puede eliminar el blob una vez expirado el intervalo de retención.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para asignar una directiva con Azure Portal, siga los pasos que figuran a continuación:

1. Vaya a un contenedor con una directiva desbloqueada.
1. En la sección **Versiones de blob inmutables**, localice la directiva desbloqueada ya existente. Seleccione el botón **Más** y, a continuación **Bloquear directiva** en el menú.
1. Confirme que desea bloquear la directiva.

:::image type="content" source="media/immutable-policy-configure-container-scope/lock-retention-policy.png" alt-text="Captura de pantalla en la que se muestra cómo bloquear una directiva de retención de duración definida en Azure Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para bloquear una directiva con PowerShell, primero llame al comando [Get-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/get-azrmstoragecontainerimmutabilitypolicy) para recuperar la etiqueta de entidad de la directiva. A continuación, llame al comando [Lock-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/lock-azrmstoragecontainerimmutabilitypolicy) y pase el valor de la etiqueta de entidad para bloquear la directiva. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurepowershell
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container>

Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -ContainerName <container> `
    -Etag $policy.Etag
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para bloquear una directiva con la CLI de Azure, primero llame al comando [az storage container immutability-policy show](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_show) para recuperar la etiqueta de entidad de la directiva. A continuación, llame al comando [az storage container immutability-policy lock](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_lock) y pase el valor de la etiqueta de entidad para bloquear la directiva. No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
$etag=$(az storage container immutability-policy show /
        --account-name <storage-account> /
        --container-name <container> /
        --query etag /
        --output tsv) 

az storage container immutability-policy lock /
    --resource-group <resource-group> /
    --account-name <storage-account> /
    --container-name <container> /
    --if-match $etag
```

---

## <a name="configure-or-clear-a-legal-hold"></a>Configuración o borrado de una suspensión legal

Una suspensión legal almacena datos inmutables hasta que se borra explícitamente. Para obtener más información sobre las directivas de suspensión legal, consulte [Suspensiones legales para datos de blobs inmutables](immutable-legal-hold-overview.md).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estos pasos para configurar la suspensión legal de un contenedor con Azure Portal:

1. Desplácese hasta el contenedor deseado.
1. Seleccione el botón **Más** y elija **Directiva de acceso**.
1. En la sección **Versiones de blob inmutables**, seleccione **Agregar directiva**.
1. Elija **Suspensión legal** como tipo de directiva y seleccione **Aceptar** para aplicarla.

En la siguiente imagen se muestra un contenedor con una directiva de retención de duración definida y una suspensión legal configurada.

:::image type="content" source="media/immutable-policy-configure-container-scope/retention-policy-legal-hold-container-scope.png" alt-text="Captura de pantalla en la que se muestra un contenedor con una directiva de retención de duración definida y una suspensión legal configurada":::

Para borrar una suspensión legal, acceda al diálogo **Directiva de acceso**, seleccione el botón **Más** y elija **Eliminar**.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para configurar una suspensión legal en un contenedor con PowerShell, llame al comando [Add-AzRmStorageContainerLegalHold](/powershell/module/az.storage/add-azrmstoragecontainerlegalhold). No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurepowershell
Add-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -Tag <tag1>,<tag2>,...
```

Para borrar una suspensión legal, llame al comando [Remove-AzRmStorageContainerLegalHold](/powershell/module/az.storage/remove-azrmstoragecontainerlegalhold):

```azurepowershell
Remove-AzRmStorageContainerLegalHold -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> ` 
    -Tag <tag1>,<tag2>,...
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para configurar una suspensión legal en un contenedor con la CLI de Azure, llame al comando [az storage container legal-hold set](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_set). No olvide reemplazar los valores del marcador de posición entre corchetes angulares por sus propios valores:

```azurecli
az storage container legal-hold set /
    --tags tag1 tag2 /
    --container-name <container> /
    --account-name <storage-account> /
    --resource-group <resource-group>
```

Para borrar una suspensión legal, llame al comando [az storage container legal-hold clear](/cli/azure/storage/container/legal-hold#az_storage_container_legal_hold_clear):

```azurecli
az storage container legal-hold clear /
    --tags tag1 tag2 /
    --container-name <container> /
    --account-name <storage-account> /
    --resource-group <resource-group>
```

---

## <a name="next-steps"></a>Pasos siguientes

- [Almacenamiento inmutable de los datos críticos para la empresa en Azure Blob Storage](immutable-storage-overview.md)
- [Directivas de retención de duración definida para datos de blobs inmutables](immutable-time-based-retention-policy-overview.md)
- [Retenciones legales para datos de blob inmutables](immutable-legal-hold-overview.md)
- [Configuración de directivas de inmutabilidad para versiones de blobs (versión preliminar)](immutable-policy-configure-version-scope.md)
