---
title: Configuración de características en vista previa en una suscripción de Azure
description: Se describe cómo ver y registrar características en vista previa (así como anular su registro) de un proveedor de recursos en su suscripción de Azure.
ms.topic: how-to
ms.date: 08/18/2021
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 9d7b705e4db7c6556eea4b9fd3cbe1916ec257a4
ms.sourcegitcommit: 1deb51bc3de58afdd9871bc7d2558ee5916a3e89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122429860"
---
# <a name="set-up-preview-features-in-azure-subscription"></a>Configuración de características en vista previa en una suscripción de Azure

En este artículo se explica cómo administrar las características en vista previa de la suscripción de Azure. Las características en vista previa permiten participar en nuevas funcionalidades antes de su lanzamiento. Algunas características en vista previa están disponibles para cualquier persona que quiera participar en ellas, mientras que otras requieren la aprobación del equipo del producto.

El control de exposición de características de Azure está disponible a través del espacio de nombres [Microsoft.Features](/rest/api/resources/features). El identificador de recurso de las características en vista previa tienen el siguiente formato:

`Microsoft.Features/providers/{resourceProviderNamespace}/features/{featureName}`

## <a name="list-preview-features"></a>Mostrar las características en vista previa

Puede ver todas las características en vista previa de una suscripción de Azure y sus estados de registro.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Azure Portal solo muestra una característica en vista previa cuando el servicio propietario de la característica ha optado expresamente por la experiencia de administración de características en vista previa.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. En el cuadro de búsqueda, escriba _suscripciones_ y seleccione **Suscripciones**.

    :::image type="content" source="./media/preview-features/search.png" alt-text="Búsqueda en Azure Portal":::

1. Seleccione el vínculo del nombre de la suscripción.

    :::image type="content" source="./media/preview-features/subscriptions.png" alt-text="Selección de una suscripción a Azure":::

1. En el menú de la izquierda, en **Configuración**, seleccione **Características en vista previa**.

    :::image type="content" source="./media/preview-features/preview-features-menu.png" alt-text="Menú de características en vista previa de Azure":::

1. Verá una lista de las características en vista previa disponibles y su estado de registro actual.

    :::image type="content" source="./media/preview-features/preview-features-list.png" alt-text="Lista de características en vista previa en Azure Portal":::

1. En **Características en vista previa**, la lista se puede filtrar por **nombre** **estado** o **tipo**:

    - **Filtrar por nombre**: debe contener texto del nombre de una característica en vista previa, no el **nombre para mostrar**.
    - **Estado**: seleccione el menú desplegable y elija un estado. Azure Portal no filtra por **No registrado**.
    - **Tipo**: seleccione el menú desplegable y elija un tipo.

    :::image type="content" source="./media/preview-features/filter.png" alt-text="Filtrado de características en vista previa en Azure Portal":::

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para ver todas las características en vista previa de la suscripción, use el comando [az feature list](/cli/azure/feature#az_feature_list).

La salida predeterminada de la CLI de Azure es JSON. Para obtener más información sobre otros formatos de salida, consulte [Formatos de salida de los comandos de la CLI de Azure](/cli/azure/format-output-azure-cli).

```azurecli-interactive
az feature list
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "NotRegistered"
  },
  "type": "Microsoft.Features/providers/features"
}
```

Para filtrar la salida de un proveedor de recursos específico, use el parámetro `namespace`. En este ejemplo, el parámetro `output` especifica un formato de tabla.

```azurecli-interactive
az feature list --namespace Microsoft.Compute --output table
```

```Output
Name                                                RegistrationState
-------------------------------------------------   -------------------
Microsoft.Compute/AHUB                              Unregistered
Microsoft.Compute/AllowManagedDisksReplaceOSDisk    Registered
Microsoft.Compute/AllowPreReleaseRegions            Pending
Microsoft.Compute/InGuestPatchVMPreview             NotRegistered
```

Para filtrar la salida de una característica en vista previa concreta, use el comando [az feature show](/cli/azure/feature#az_feature_show).

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  NotRegistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para ver todas las características en vista previa de la suscripción, use el cmdlet [Get-AzProviderFeature](/powershell/module/az.resources/get-azproviderfeature).

```azurepowershell-interactive
Get-AzProviderFeature -ListAvailable
```

```Output
FeatureName      ProviderName     RegistrationState
-----------      ------------     -----------------
betaAccess       Microsoft.AAD    NotRegistered
previewAccess    Microsoft.AAD    Registered
tipAccess        Microsoft.AAD    Pending
testAccess       Microsoft.AAD    Unregistered
```

Para filtrar la salida de un proveedor de recursos específico, use el parámetro `ProviderNamespace`. La salida predeterminada muestra solo las características registradas. Para mostrar todas las características en vista previa de un proveedor de recursos, use el parámetro `ListAvailable` con el parámetro `ProviderNamespace`.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace "Microsoft.Compute" -ListAvailable
```

```Output
FeatureName                          ProviderName        RegistrationState
-----------                          ------------        -----------------
AHUB                                 Microsoft.Compute   Unregistered
AllowManagedDisksReplaceOSDisk       Microsoft.Compute   Registered
AllowPreReleaseRegions               Microsoft.Compute   Pending
InGuestPatchVMPreview                Microsoft.Compute   NotRegistered
```

La salida de una característica en vista previa concreta se puede filtrar mediante el parámetro `FeatureName`.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   NotRegistered
```

---

## <a name="register-preview-feature"></a>Registrar la característica en vista previa

Registre una característica en vista previa de su suscripción de Azure para exponer más funcionalidades de un proveedor de recursos. Algunas características en vista previa requieren aprobación.

Una vez que la característica en vista previa está registrada en la suscripción, verá uno de los dos estados siguientes: **Registrado** o **Pendiente**.

- El estado de una característica en vista previa que no requiere aprobación es **Registrado**.
- Si la característica en vista previa requiere aprobación, el estado de registro será **Pendiente**.
  - Para solicitar una aprobación, envíe una [solicitud de soporte técnico de Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).
  - Una vez aprobado el registro, el estado de la característica en vista previa cambia a **Registrado**.

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. En el cuadro de búsqueda, escriba _suscripciones_ y seleccione **Suscripciones**.
1. Seleccione el vínculo del nombre de la suscripción.
1. En el menú de la izquierda, en **Configuración**, seleccione **Características en vista previa**.
1. Seleccione el vínculo de la característica en vista previa que quiera registrar.
1. Seleccione **Registrar**.

    :::image type="content" source="./media/preview-features/register.png" alt-text="Registro de una característica en vista previa en Azure Portal":::

1. Seleccione **Aceptar**.

La pantalla **Características en vista previa** se actualiza para mostrar el estado **Registrado** de la característica en vista previa.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para registrar una característica en vista previa, use el comando [az feature register](/cli/azure/feature#az_feature_register).

```azurecli-interactive
az feature register --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Registering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

Para ver el estado del registro, use el comando `az feature show`.

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Registered
```

> [!NOTE]
> Cuando el comando de registro se ejecuta, aparece un mensaje que indica que, una vez registrada la característica, se ejecute `az provider register --namespace <provider-name>` para propagar los cambios.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para registrar una característica en vista previa, use el cmdlet [Register-AzProviderFeature](/powershell/module/az.resources/register-azproviderfeature).

```azurepowershell-interactive
Register-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registering
```

Para ver el estado del registro, use el cmdlet `Get-AzProviderFeature`.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Registered
```

---

## <a name="unregister-preview-feature"></a>Anular el registro de una característica en vista previa

Cuando haya terminado de usar una característica en versión preliminar, anule su registro de la suscripción de Azure. Después de anular el registro de la característica, verá dos posibles estados diferentes. Si el registro se anula a través de Azure Portal, el estado se establece en **No registrado**. Si el registro se anula a través de la CLI de Azure, PowerShell o la API de REST, el estado se establece en **Sin registrar**. El estado es diferente porque Azure Portal elimina el registro de la característica, mientras los comandos lo anulan. En ambos casos, la característica ya no estará disponible en la suscripción. Igualmente en ambos casos, puede optar por participar en la característica volviendo a registrarla.

# <a name="portal"></a>[Portal](#tab/azure-portal)

El registro de características en vista previa se puede anular en **Características en vista previa**. El **estado** cambiará a **No registrado**.

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. En el cuadro de búsqueda, escriba _suscripciones_ y seleccione **Suscripciones**.
1. Seleccione el vínculo del nombre de la suscripción.
1. En el menú de la izquierda, en **Configuración**, seleccione **Características en vista previa**.
1. Seleccione el vínculo de la característica en vista previa cuyo registro quiera anular.
1. Seleccione **Anular registro**.

    :::image type="content" source="./media/preview-features/unregister.png" alt-text="Anulación del registro de una característica en vista previa en Azure Portal":::

1. Seleccione **Aceptar**.

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para anular el registro de una característica en vista previa, use en comando [az feature unregister](/cli/azure/feature#az_feature_unregister). El estado `RegistrationState` cambiará a **Sin registrar**.

```azurecli-interactive
az feature unregister --name InGuestPatchVMPreview --namespace Microsoft.Compute
```

```json
{
  "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Features/providers/
    Microsoft.Compute/features/InGuestPatchVMPreview",
  "name": "Microsoft.Compute/InGuestPatchVMPreview",
  "properties": {
    "state": "Unregistering"
  },
  "type": "Microsoft.Features/providers/features"
}
```

Para ver el estado de una anulación de registro, use el comando `az feature show`.

```azurecli-interactive
az feature show --name InGuestPatchVMPreview --namespace Microsoft.Compute --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

> [!NOTE]
> Cuando el comando de anulación del registro se ejecuta, aparece un mensaje que indica que, una vez anulado el registro de la característica, se ejecute `az provider register --namespace <provider-name>` para propagar los cambios.

Use el siguiente comando para encontrar características en vista previa con un estado **Sin registrar**. Reemplace `<ResourceProvider.Name>` por un nombre de proveedor, como `Microsoft.Compute`.

En el siguiente ejemplo se muestra una característica en vista previa con un estado **Sin registrar** del proveedor de recursos `Microsoft.Compute`.

```azurecli-interactive
az feature list --namespace <ResourceProvider.Name> --query "[?properties.state=='Unregistered'].{Name:name, RegistrationState:properties.state}" --output table
```

```Output
Name                                     RegistrationState
---------------------------------------  -------------------
Microsoft.Compute/InGuestPatchVMPreview  Unregistered
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para anular el registro de una característica en vista previa, use el cmdlet [Unregister-AzProviderFeature](/powershell/module/az.resources/unregister-azproviderfeature). El estado `RegistrationState` cambiará a **Sin registrar**.

```azurepowershell-interactive
Unregister-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistering
```

Para ver el estado de una anulación de registro, use el cmdlet `Get-AzProviderFeature`.

```azurepowershell-interactive
Get-AzProviderFeature -FeatureName "InGuestPatchVMPreview" -ProviderNamespace "Microsoft.Compute"
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

En el siguiente ejemplo se muestra una característica en vista previa con un estado **Sin registrar** del proveedor de recursos `Microsoft.Compute`.

```azurepowershell-interactive
Get-AzProviderFeature  -ProviderNamespace "Microsoft.Compute" -ListAvailable | Where-Object { $_.RegistrationState -eq "Unregistered" }
```

```Output
FeatureName             ProviderName        RegistrationState
-----------             ------------        -----------------
InGuestPatchVMPreview   Microsoft.Compute   Unregistered
```

---

## <a name="next-steps"></a>Pasos siguientes

- Para usar llamadas a la API de REST y ver o registrar características en vista previa (así como anular su registro), consulte la documentación sobre [Características](/rest/api/resources/features).
- Para obtener más información sobre cómo registrar un proveedor de recursos, consulte [Tipos y proveedores de recursos de Azure](resource-providers-and-types.md).
- Para obtener una lista que asigna proveedores de recursos con servicios de Azure, consulte [Proveedores de recursos para servicios de Azure](azure-services-resource-providers.md).
