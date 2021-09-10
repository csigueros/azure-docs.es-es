---
title: Configuración de directivas de inmutabilidad para versiones de blobs (versión preliminar)
titleSuffix: Azure Storage
description: Aprenda a configurar una directiva de inmutabilidad limitada a un contenedor (versión preliminar). Las directivas de inmutabilidad proporcionan compatibilidad con WORM (escribir una vez, leer muchas) en Blob Storage, donde los datos se almacenan en un formato que no se puede borrar ni modificar.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 08/31/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 04e05f67787b285dd1286e0c6b7a6b251262ed0f
ms.sourcegitcommit: 7b6ceae1f3eab4cf5429e5d32df597640c55ba13
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/31/2021
ms.locfileid: "123272248"
---
# <a name="configure-immutability-policies-for-blob-versions-preview"></a>Configuración de directivas de inmutabilidad para versiones de blobs (versión preliminar)

El almacenamiento inmutable de Azure Blob Storage permite a los usuarios almacenar datos críticos para la empresa en estado WORM. Mientras los datos se encuentran en estado WORM, no se pueden modificar ni eliminar durante un intervalo especificado por el usuario. Con la configuración de directivas de inmutabilidad para los datos de blobs, puede impedir que sus datos se sobrescriban y eliminen. Las directivas de inmutabilidad incluyen directivas de retención de duración definida y suspensiones legales. Para más información sobre las directivas de inmutabilidad de Blob Storage, consulte [Almacenamiento de datos críticos para la empresa con almacenamiento inmutable](immutable-storage-overview.md).

Una directiva de inmutabilidad puede estar limitada a una determinada versión de blob (versión preliminar) o un contenedor. En este artículo se describe cómo configurar una directiva de inmutabilidad en el nivel de versión. Para aprender a configurar directivas de inmutabilidad en el nivel de contenedor, consulte [Configuración de directivas de inmutabilidad para contenedores](immutable-policy-configure-container-scope.md).

La configuración de una directiva de inmutabilidad en el nivel de versión es un proceso de dos pasos:

1. En primer lugar, habilite la compatibilidad con la inmutabilidad en el nivel de versión en un contenedor nuevo o existente. Para más información, consulte [Habilitación de la compatibilidad con la inmutabilidad en el nivel de versión de un contenedor](#enable-support-for-version-level-immutability-on-a-container).
1. A continuación, configure una directiva de retención con duración definida o una suspensión legal que se aplique a una o varias versiones de blob de ese contenedor.

> [!IMPORTANT]
> Las directivas de inmutabilidad en el nivel de versión están actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="prerequisites"></a>Requisitos previos

Para configurar directivas de retención con duración definida en el nivel de versión, el control de versiones de blob debe estar habilitado para la cuenta de almacenamiento. Para obtener información sobre cómo habilitar el control de versiones de blobs, consulte [Habilitación y administración del control de versiones de blobs](versioning-enable.md).

Para información sobre las configuraciones de cuentas de almacenamiento admitidas para las directivas de inmutabilidad en el nivel de versión, consulte [Configuraciones de cuenta admitidas](immutable-storage-overview.md#supported-account-configurations).

## <a name="enable-support-for-version-level-immutability-on-a-container"></a>Habilitación de la compatibilidad con la inmutabilidad en el nivel de versión de un contenedor

Para poder aplicar una directiva de retención con duración definida a una versión de blob, debe habilitar la compatibilidad con la inmutabilidad en el nivel de versión. Tanto los contenedores nuevos como los existentes se pueden configurar para admitir la inmutabilidad en el nivel de versión. Sin embargo, el contenedor existente debe someterse a un proceso de migración para habilitar la compatibilidad.

Tenga en cuenta que la habilitación de la compatibilidad con la inmutabilidad en el nivel de versión para un contenedor no convierte los datos de ese contenedor en inmutables. También debe configurar una directiva de inmutabilidad predeterminada para el contenedor o una directiva de inmutabilidad para una versión de blob específica.

### <a name="enable-version-level-immutability-for-a-new-container"></a>Habilitación de la inmutabilidad en el nivel de versión para un nuevo contenedor

Para usar una directiva de inmutabilidad en el nivel de versión, primero debe habilitar explícitamente la compatibilidad con WORM en el nivel de versión en el contenedor. Puede hacerlo al crear el contenedor o al agregar una directiva de inmutabilidad en el nivel de versión a un contenedor existente.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para crear un contenedor que admita la inmutabilidad en el nivel de versión en Azure Portal, siga estos pasos:

1. En Azure Portal, vaya a la página **Contenedores** de la cuenta de almacenamiento y seleccione **Agregar**.
1. En el cuadro de diálogo **Nuevo contenedor**, proporcione un nombre para el contenedor y expanda la sección **Avanzado**.
1. Seleccione **Enable version-level immutability support** (Habilitar compatibilidad con la inmutabilidad en el nivel de versión) para habilitar esta funcionalidad para el contenedor.

    :::image type="content" source="media/immutable-policy-configure-version-scope/create-container-version-level-immutability.png" alt-text="Captura de pantalla que muestra cómo crear un contenedor con la inmutabilidad en el nivel de versión habilitada":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para crear un contenedor que admita la inmutabilidad de nivel de versión con PowerShell, instale primero el [módulo Az.Storage](https://www.powershellgallery.com/packages/Az.Storage/3.10.1-preview), versión 3.10.1-preview.

Después, llame al comando **New-AzRmStorageContainer** con el parámetro `-EnableImmutableStorageWithVersioning`, como se muestra en el ejemplo siguiente. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurepowershell
# Create a container with version-level immutability support.
$container = New-AzRmStorageContainer -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -EnableImmutableStorageWithVersioning

# Verify that version-level immutability support is enabled for the container
$container.ImmutableStorageWithVersioning
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para crear un contenedor que admita la inmutabilidad de nivel de versión con la CLI de Azure, instale primero la versión 2.27 o posterior de la CLI de Azure. Para obtener más información sobre cómo instalar la CLI de Azure, vea [Procedimiento para instalar la CLI de Azure](/cli/azure/install-azure-cli).

Después, llame al comando [az storage container-rm create](/cli/azure/storage/container-rm#az_storage_container_rm_create) y especifique el parámetro `--enable-vlw`. No olvide reemplazar los valores de los marcadores de posición entre corchetes angulares por los suyos propios:

```azurecli
# Create a container with version-level immutability support.
az storage container-rm create \
    --name <container> \
    --storage-account <storage-account> \
    --resource-group <resource-group> \
    --enable-vlw

# Verify that version-level immutability support is enabled for the container
az storage container-rm show \
    --storage-account <storage-account> \
    --name <container> \
    --query '[immutableStorageWithVersioning.enabled]' \
    --output tsv
```

---

### <a name="migrate-an-existing-container-to-support-version-level-immutability"></a>Migración de un contenedor existente para admitir la inmutabilidad en el nivel de versión

Para configurar directivas de inmutabilidad en el nivel de versión para un contenedor existente, debe migrar el contenedor para que admita el almacenamiento inmutable en el nivel de versión. La migración del contenedor puede tardar algún tiempo y no se puede revertir.

Para migrar un contenedor existente a fin de admitir directivas de inmutabilidad de nivel de versión, el contenedor debe tener configurada una directiva de retención basada en el tiempo de nivel de contenedor. Se produce un error en la migración a menos que el contenedor tenga una directiva existente. El intervalo de retención de la directiva de nivel de contenedor se mantiene como intervalo de retención para la directiva de nivel de versión predeterminada en el contenedor.

Si el contenedor ya tiene una suspensión legal en el nivel de contenedor, no se puede migrar hasta que se retire esta.

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para migrar un contenedor para que admita el almacenamiento inmutable en el nivel de versión en Azure Portal, siga estos pasos:

1. Desplácese hasta el contenedor deseado.
1. Seleccione el botón **Más** a la derecha y, luego, **Directiva de acceso**.
1. En **Almacenamiento de blobs inmutable**, seleccione **Agregar directiva**.
1. En el campo **Tipo de directiva**, elija *Retención con duración definida* y especifique el período de retención.
1. Seleccione **Enable version-level immutability** (Habilitar inmutabilidad en el nivel de versión).
1. Seleccione **Aceptar** para crear una directiva de nivel de contenedor con el intervalo de retención especificado y, después, comience la migración para admitir la inmutabilidad de nivel de versión.

    :::image type="content" source="media/immutable-policy-configure-version-scope/migrate-existing-container.png" alt-text="Captura de pantalla que muestra cómo migrar un contenedor existente para admitir la inmutabilidad en el nivel de versión":::

Mientras la operación de migración está en curso, el ámbito de la directiva en el contenedor se muestra como *Contenedor*.

:::image type="content" source="media/immutable-policy-configure-version-scope/container-migration-in-process.png" alt-text="Captura de pantalla en la que se muestra la migración de contenedores en proceso":::

Una vez que se completa la migración, el ámbito de la directiva en el contenedor se muestra como *Versión*. La directiva que se muestra es una directiva predeterminada en el contenedor que se aplica de forma automática a todas las versiones de blob creadas posteriormente en el contenedor. La directiva predeterminada se puede invalidar en cualquier versión si se especifica una directiva personalizada para esa versión.

:::image type="content" source="media/immutable-policy-configure-version-scope/container-migration-complete.png" alt-text="Captura de pantalla en la que se muestra la migración de contenedores completada":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

A fin de migrar un contenedor para admitir el almacenamiento inmutable de nivel de versión con PowerShell, asegúrese primero de que existe una directiva de retención basada en el tiempo de nivel de contenedor para el contenedor. Para crear una, llame a [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy).

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
   -StorageAccountName <storage-account> `
   -ContainerName <container> `
   -ImmutabilityPeriod <retention-interval-in-days>
```

Después, llame al comando **Invoke-AzRmStorageContainerImmutableStorageWithVersioningMigration** para migrar el contenedor. Incluya el parámetro `-AsJob` para ejecutar el comando de forma asincrónica. Se recomienda ejecutar la operación de forma asincrónica, ya que la migración puede tardar algún tiempo en completarse.

```azurepowershell
$migrationOperation = Invoke-AzRmStorageContainerImmutableStorageWithVersioningMigration `
    -ResourceGroupName <resource-group> `
    -StorageAccountName <storage-account> `
    -Name <container> `
    -AsJob
```

Para comprobar el estado de la operación de larga duración, lea la propiedad **JobStateInfo.State** de la operación.

```azurepowershell
$migrationOperation.JobStateInfo.State
```

Si el contenedor no tiene una directiva de retención basada en el tiempo existente al intentar migrar a la inmutabilidad de nivel de versión, se produce un error en la operación. En el ejemplo siguiente se comprueba el valor de la propiedad **JobStateInfo.State** y se muestra el mensaje de error si se produce un error en la operación porque la directiva de nivel de contenedor no existe.

```azurepowershell
if ($migrationOperation.JobStateInfo.State -eq "Failed") {
Write-Host $migrationOperation.Error
}
The container <container-name> must have an immutability policy set as a default policy 
before initiating container migration to support object level immutability with versioning.
```

Una vez que se complete la migración, compruebe la propiedad **Output** de la operación para ver que está habilitada la compatibilidad con la inmutabilidad de nivel de versión.

```azurepowershell
$migrationOperation.Output
```

Para obtener más información sobre los trabajos de PowerShell, vea [Ejecución de cmdlets de Azure PowerShell en trabajos de PowerShell](/powershell/azure/using-psjobs).

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

A fin de migrar un contenedor para admitir el almacenamiento inmutable de nivel de versión con la CLI de Azure, asegúrese primero de que existe una directiva de retención basada en el tiempo de nivel de contenedor para el contenedor. Para crear una, llame a [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create).

```azurecli
az storage container immutability-policy create \
    --resource-group <resource-group> \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days>
```

Después, llame al comando [az storage container-rm migrate-vlw](/cli/azure/storage/container-rm#az_storage_container_rm_migrate_vlw) para migrar el contenedor. Incluya el parámetro `--no-wait` para ejecutar el comando de forma asincrónica. Se recomienda ejecutar la operación de forma asincrónica, ya que la migración puede tardar algún tiempo en completarse.

```azurecli
az storage container-rm migrate-vlw \
    --resource-group <resource-group> \
    --storage-account <storage-account> \
    --name <container> \
    --no-wait
```

Para comprobar el estado de la operación de larga duración, lea el valor de la propiedad **migrationState**.

```azurecli
az storage container-rm show \
    --storage-account <storage-account> \
    --name <container> \
    --query '[immutableStorageWithVersioning.migrationState]' \
    --output tsv
```

---

## <a name="configure-a-time-based-retention-policy-on-a-container"></a>Configuración de una directiva de retención con duración definida en un contenedor

Después de que un contenedor se ha habilitado para la inmutabilidad en el nivel de versión, puede especificar una directiva predeterminada de retención con duración definida en el nivel de versión para el contenedor. Después de especificar una directiva predeterminada para un contenedor, se aplica de forma predeterminada a todas las nuevas versiones de blob que se crean en el contenedor. Puede invalidar la directiva predeterminada para cualquier versión de blob individual del contenedor.

La directiva predeterminada no se aplica automáticamente a las versiones de blob que existían antes de configurar la directiva predeterminada.

Si ha migrado un contenedor existente para admitir la inmutabilidad de nivel de versión, la directiva de nivel de contenedor que estaba en vigor antes de la migración se migra a una directiva de nivel de versión predeterminada para el contenedor.

### <a name="configure-a-default-time-based-retention-policy-on-a-container"></a>Configuración de una directiva predeterminada de retención con duración definida en un contenedor

Para configurar una directiva de inmutabilidad de nivel de versión predeterminada para un contenedor, use Azure Portal, PowerShell, la CLI de Azure o uno de los SDK de Azure Storage. Asegúrese de que ha habilitado la compatibilidad con la inmutabilidad de nivel de versión para el contenedor, como se describe en [Habilitación de la compatibilidad con la inmutabilidad de nivel de versión en un contenedor](#enable-support-for-version-level-immutability-on-a-container).

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Para configurar una directiva de inmutabilidad predeterminada de nivel de versión para un contenedor en Azure Portal, siga estos pasos:

1. En Azure Portal, vaya a la página **Contenedores** y busque el contenedor al que quiere aplicar la directiva.
1. Seleccione el botón **Más** a la derecha del nombre del contenedor y elija **Directiva de acceso**.
1. En el cuadro de diálogo **Directiva de acceso**, en la sección **Almacenamiento de blobs inmutable**, elija **Agregar directiva**.
1. Seleccione **Time-based retention policy** (Directiva de retención con duración definida) y especifique el intervalo de retención.
1. Si lo desea, seleccione **Permitir anexiones protegidas adicionales** para permitir operaciones de escritura en blobs en anexos que estén protegidos por una directiva de inmutabilidad. Para más información, consulte [Permitir escrituras de blobs en anexos protegidos](immutable-time-based-retention-policy-overview.md#allow-protected-append-blobs-writes).
1. Seleccione **Aceptar** para aplicar la directiva predeterminada al contenedor.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-default-retention-policy-container.png" alt-text="Captura de pantalla que muestra cómo configurar una directiva predeterminada de retención en el nivel de versión para un contenedor":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para configurar una directiva de inmutabilidad de nivel de versión predeterminada para un contenedor con PowerShell, llame al comando [Set-AzRmStorageContainerImmutabilityPolicy](/powershell/module/az.storage/set-azrmstoragecontainerimmutabilitypolicy).

```azurepowershell
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName <resource-group> `
   -StorageAccountName <storage-account> `
   -ContainerName <container> `
   -ImmutabilityPeriod <retention-interval-in-days> `
   -AllowProtectedAppendWrite $true
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para configurar una directiva de inmutabilidad de nivel de versión predeterminada para un contenedor con la CLI de Azure, llame al comando [az storage container immutability-policy create](/cli/azure/storage/container/immutability-policy#az_storage_container_immutability_policy_create).

```azurecli
az storage container immutability-policy create \
    --account-name <storage-account> \
    --container-name <container> \
    --period <retention-interval-in-days> \
    --allow-protected-append-writes true
```

---

### <a name="determine-the-scope-of-a-retention-policy-on-a-container"></a>Determinación del ámbito de una directiva de retención en un contenedor

Para determinar el ámbito de una directiva de retención con duración definida en Azure Portal, siga estos pasos:

1. Desplácese hasta el contenedor deseado.
1. Seleccione el botón **Más** a la derecha y, luego, **Directiva de acceso**.
1. En **Almacenamiento de blobs inmutable**, busque el campo **Ámbito**. Si el contenedor está configurado con una directiva predeterminada de retención en el nivel de versión, el ámbito se establece en *Versión*, como se muestra en la siguiente imagen:

    :::image type="content" source="media/immutable-policy-configure-version-scope/version-scoped-retention-policy.png" alt-text="Captura de pantalla que muestra la directiva predeterminada de retención en el nivel de versión configurada para el contenedor":::

1. Si el contenedor está configurado con una directiva de retención en el nivel de versión, el ámbito se establece en *Contenedor*, como se muestra en la siguiente imagen:

    :::image type="content" source="media/immutable-policy-configure-version-scope/container-scoped-retention-policy.png" alt-text="Captura de pantalla que muestra la directiva predeterminada de retención en el nivel de contenedor configurada para el contenedor":::

## <a name="configure-a-time-based-retention-policy-on-an-existing-version"></a>Configuración de una directiva de retención con duración definida en una versión existente

Las directivas de retención con duración definida mantienen los datos de blob en un estado WORM durante un intervalo especificado. Para más información sobre las directivas de retención con duración definida, consulte [Directivas de retención con duración definida para datos de blobs inmutables](immutable-time-based-retention-policy-overview.md).

Tiene tres opciones para configurar una directiva de retención con duración definida para una versión de blob:

- Opción 1: Puede configurar una directiva predeterminada que esté limitada al contenedor y que se aplique a todos los objetos que contiene de forma predeterminada. Los objetos del contenedor heredarán la directiva predeterminada a menos que la invalide explícitamente mediante la configuración de una directiva para una versión de blob determinada. Para más información, consulte [Configuración de una directiva predeterminada de retención con duración definida en un contenedor](#configure-a-default-time-based-retention-policy-on-a-container).
- Opción 2: Puede configurar una directiva para la versión actual del blob. Esta directiva puede invalidar una directiva predeterminada configurada para el contenedor, si existe una y se desbloquea. De forma predeterminada, las versiones anteriores que se crean después de configurar la directiva heredarán la directiva de la versión actual del blob. Para más información, consulte [Configuración de una directiva de retención para la versión actual de un blob](#configure-a-retention-policy-on-the-current-version-of-a-blob).
- Opción 3: Puede configurar una directiva para una versión anterior de un blob. Esta directiva puede invalidar una directiva predeterminada configurada para la versión anterior, si existe una y se desbloquea. Para más información, consulte [Configuración de una directiva de retención para la versión anterior de un blob](#configure-a-retention-policy-on-a-previous-version-of-a-blob).

Para más información sobre el control de versiones de blobs, consulte [Control de versiones de blobs](versioning-overview.md).

### <a name="portal"></a>[Portal](#tab/azure-portal)

Al desplazarse hacia un contenedor, en Azure Portal se muestra una lista de blobs. Cada blob mostrado representa la versión actual del blob. Puede acceder a una lista de versiones anteriores si selecciona el botón **Más** de un blob y elige **Ver versiones anteriores**.  

### <a name="configure-a-retention-policy-on-the-current-version-of-a-blob"></a>Configuración de una directiva de retención para la versión actual de un blob

Para configurar una directiva de retención con duración definida para la versión actual de un blob, siga estos pasos:

1. Vaya al contenedor que contiene el blob de destino.
1. Seleccione el botón **Más** a la derecha del nombre del blob y elija **Directiva de acceso**. Si ya se ha configurado una directiva de retención con duración definida para la versión anterior, aparece en el cuadro de diálogo **Directiva de acceso**.
1. En el cuadro de diálogo **Directiva de acceso**, en la sección **Immutable blob versions** (Versiones de blob inmutables), elija **Agregar directiva**.
1. Seleccione **Time-based retention policy** (Directiva de retención con duración definida) y especifique el intervalo de retención.
1. Seleccione **Aceptar** para aplicar la directiva a la versión actual del blob.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-version.png" alt-text="Captura de pantalla que muestra cómo configurar una directiva de retención para la versión actual de un blob":::

Puede ver las propiedades de un blob para determinar si hay una directiva habilitada para la versión actual. Seleccione el blob y, luego, vaya a la pestaña **Información general** y busque la propiedad **Version-level immutability policy** (Directiva de inmutabilidad en el nivel de versión). Si hay una directiva habilitada, la propiedad **Período de retención** mostrará la fecha y hora de expiración de la directiva. Tenga en cuenta que una directiva puede estar configurada para la versión actual o puede heredarse del contenedor primario del blob si hay una directiva predeterminada vigente.

:::image type="content" source="media/immutable-policy-configure-version-scope/view-version-level-retention-policy-portal.png" alt-text="Captura de pantalla que muestra las propiedades de la directiva de inmutabilidad en la versión del blob en Azure Portal":::

### <a name="configure-a-retention-policy-on-a-previous-version-of-a-blob"></a>Configuración de una directiva de retención para una versión anterior de un blob

También puede configurar una directiva de retención con duración definida para una versión anterior de un blob. Una versión anterior siempre es inmutable, ya que no se puede modificar. Sin embargo, se puede eliminar una versión anterior. Una directiva de retención con duración definida protege contra la eliminación mientras está vigente.

Para configurar una directiva de retención con duración definida para una versión anterior de un blob, siga estos pasos:

1. Vaya al contenedor que contiene el blob de destino.
1. Seleccione el blob y, luego, vaya a la pestaña **Versiones**.
1. Busque la versión de destino y, luego, seleccione el botón **Más** y elija **Directiva de acceso**. Si ya se ha configurado una directiva de retención con duración definida para la versión anterior, aparece en el cuadro de diálogo **Directiva de acceso**.
1. En el cuadro de diálogo **Directiva de acceso**, en la sección **Immutable blob versions** (Versiones de blob inmutables), elija **Agregar directiva**.
1. Seleccione **Time-based retention policy** (Directiva de retención con duración definida) y especifique el intervalo de retención.
1. Seleccione **Aceptar** para aplicar la directiva a la versión actual del blob.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-previous-version.png" alt-text="Captura de pantalla que muestra cómo configurar una directiva de retención para una versión de blob anterior en Azure Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para configurar una directiva de retención basada en el tiempo en una versión de blob con PowerShell, llame al comando **Set-AzStorageBlobImmutabilityPolicy**.

```azurepowershell
# Get the storage account context
$ctx = (Get-AzStorageAccount `
        -ResourceGroupName <resource-group> `
        -Name <storage-account>).Context

Set-AzStorageBlobImmutabilityPolicy -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -ExpiresOn "2021-09-01T12:00:00Z" `
    -PolicyMode Unlocked
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

N/D

---

## <a name="configure-a-time-based-retention-policy-when-uploading-a-blob"></a>Configuración de una directiva de retención con duración definida al cargar un blob

Al usar Azure Portal para cargar un blob en un contenedor que admite la inmutabilidad en el nivel de versión, tiene varias opciones para configurar una directiva de retención con duración definida para el nuevo blob:

- Opción 1: Si hay una directiva de retención predeterminada configurada para el contenedor, puede cargar el blob con la directiva del contenedor. Esta opción se selecciona de forma predeterminada cuando hay una directiva de retención en el contenedor.
- Opción 2: Si hay una directiva de retención predeterminada configurada para el contenedor, puede optar por invalidarla; para ello, puede definir una directiva de retención personalizada para el nuevo blob o cargar el blob sin directiva.
- Opción 3: Si no hay ninguna directiva predeterminada configurada para el contenedor, puede cargar el blob con una directiva personalizada o sin directiva.

Para configurar una directiva de retención con duración definida al cargar un blob, siga estos pasos:

1. Vaya al contenedor deseado y seleccione **Cargar**.
1. En el cuadro de diálogo **Cargar blob**, expanda la sección **Avanzado**.
1. Configure la directiva de retención con duración definida para el nuevo blob en el campo **Directiva de retención**. Si hay una directiva predeterminada configurada para el contenedor, esa directiva se selecciona de forma predeterminada. También puede especificar una directiva personalizada para el blob.

    :::image type="content" source="media/immutable-policy-configure-version-scope/configure-retention-policy-blob-upload.png" alt-text="Captura de pantalla que muestra las opciones para configurar la directiva de retención para la carga de blobs en Azure Portal":::

## <a name="modify-or-delete-an-unlocked-retention-policy"></a>Modificación o eliminación de una directiva de retención desbloqueada

Puede modificar una directiva de retención con duración definida desbloqueada para acortar o alargar el intervalo de retención. Las directivas desbloqueadas también se pueden eliminar. La edición o eliminación de una directiva de retención con duración definida para una versión de blob no afecta a las directivas vigentes para cualquier otra versión. Si hay una directiva de retención con duración definida predeterminada vigente para el contenedor, la versión del blob con la directiva modificada o eliminada ya no se heredará del contenedor.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para modificar una directiva de retención de duración definida desbloqueada en Azure Portal, siga estos pasos:

1. Busque el contenedor o la versión de destino. Seleccione el botón **Más** y elija **Directiva de acceso**.
1. Busque la directiva de inmutabilidad desbloqueada existente. Seleccione el botón **Más** y, luego, **Editar** en el menú.

    :::image type="content" source="media/immutable-policy-configure-version-scope/edit-existing-version-policy.png" alt-text="Captura de pantalla que muestra cómo editar una directiva de retención con duración definida existente en Azure Portal":::

1. Proporcione la nueva fecha y hora de expiración de la directiva.

Para eliminar la directiva desbloqueada, seleccione **Eliminar** en el menú **Más**.

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para modificar una directiva de retención de basada en el tiempo desbloqueada con PowerShell, llame al comando **Set-AzStorageBlobImmutabilityPolicy** en la versión del blob con la nueva fecha y hora para la expiración de la directiva.

```azurepowershell
$containerName = "<container>"
$blobName = "<blob>"

# Get the previous blob version.
$blobVersion = Get-AzStorageBlob -Container $containerName `
    -Blob $blobName `
    -VersionId "2021-08-31T00:26:41.2273852Z" `
    -Context $ctx

# Extend the retention interval by five days.
$blobVersion = $blobVersion | 
    Set-AzStorageBlobImmutabilityPolicy -ExpiresOn (Get-Date).AddDays(5) `

# View the new policy parameters.
$blobVersion.BlobProperties.ImmutabilityPolicy
```

Para eliminar una directiva de retención desbloqueada, llame al comando **Remove-AzStorageBlobImmutabilityPolicy**.

```azurepowershell
$blobVersion = $blobVersion | Remove-AzStorageBlobImmutabilityPolicy
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

N/D

---

## <a name="lock-a-time-based-retention-policy"></a>Bloqueo de una directiva de retención de duración definida

Cuando haya terminado de probar una directiva de retención de duración definida, puede bloquearla. Una directiva bloqueada cumple los requisitos de SEC 17a-4(f) y de otras normas. Puede ampliar el intervalo de retención de una directiva bloqueada hasta cinco veces, pero no acortarlo.

Una vez bloqueada una directiva, no se puede eliminar. Sin embargo, puede eliminar el blob una vez expirado el intervalo de retención.

### <a name="portal"></a>[Portal](#tab/azure-portal)

Para bloquear una directiva con Azure Portal, siga estos pasos:

1. Busque el contenedor o la versión de destino. Seleccione el botón **Más** y elija **Directiva de acceso**.
1. En la sección **Versiones de blob inmutables**, busque la directiva desbloqueada ya existente. Seleccione el botón **Más** y, luego, **Bloquear directiva** en el menú.
1. Confirme que desea bloquear la directiva.

    :::image type="content" source="media/immutable-policy-configure-version-scope/lock-policy-portal.png" alt-text="Captura de pantalla que muestra cómo bloquear una directiva de retención con duración definida en Azure Portal":::

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para bloquear una directiva con PowerShell, llame al comando **Set-AzStorageBlobImmutabilityPolicy** y establezca el parámetro **PolicyMode** en *Locked*.

En el ejemplo siguiente se muestra cómo bloquear una directiva mediante la especificación del mismo intervalo de retención que estaba en vigor para la directiva desbloqueada. También puede cambiar la expiración en el momento de bloquear la directiva.

```azurepowershell
# Get the previous blob version.
$blobVersion = Get-AzStorageBlob -Container $containerName `
    -Blob $blobName `
    -VersionId "2021-08-31T00:26:41.2273852Z" `
    -Context $ctx

$blobVersion = $blobVersion | 
    Set-AzStorageBlobImmutabilityPolicy `
        -ExpiresOn $blobVersion.BlobProperties.ImmutabilityPolicy.ExpiresOn `
        -PolicyMode Locked
```

### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

N/D

---

## <a name="configure-or-clear-a-legal-hold"></a>Configuración o borrado de una suspensión legal

Una suspensión legal almacena datos inmutables hasta que se borra explícitamente. Para obtener más información sobre las directivas de suspensión legal, consulte [Suspensiones legales para datos de blobs inmutables](immutable-legal-hold-overview.md).

#### <a name="portal"></a>[Portal](#tab/azure-portal)

Siga estos pasos para configurar una suspensión legal en una versión de blob con Azure Portal:

1. Busque la versión de destino, que puede ser la versión actual o una versión anterior de un blob. Seleccione el botón **Más** y elija **Directiva de acceso**.
1. En la sección **Versiones de blob inmutables**, seleccione **Agregar directiva**.
1. Elija **Suspensión legal** como tipo de directiva y seleccione **Aceptar** para aplicarla.

En la siguiente imagen se muestra una versión actual de un blob que tiene configuradas una directiva de retención con duración definida y una suspensión legal.

:::image type="content" source="media/immutable-policy-configure-version-scope/configure-legal-hold-blob-version.png" alt-text="Captura de pantalla que muestra la suspensión legal configurada para la versión del blob":::

Para borrar una suspensión legal, vaya al cuadro de diálogo **Directiva de acceso**, seleccione el botón **Más** y elija **Eliminar**.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para configurar o borrar una suspensión legal en una versión de blob con PowerShell, llame al comando **Set-AzStorageBlobLegalHold**.

```azurepowershell
# Set a legal hold
Set-AzStorageBlobLegalHold -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -EnableLegalHold

# Clear a legal hold
Set-AzStorageBlobLegalHold -Container <container> `
    -Blob <blob-version> `
    -Context $ctx `
    -DisableLegalHold
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

N/D

---

## <a name="next-steps"></a>Pasos siguientes

- [Almacenamiento inmutable de los datos críticos para la empresa en Azure Blob Storage](immutable-storage-overview.md)
- [Directivas de retención de duración definida para datos de blobs inmutables](immutable-time-based-retention-policy-overview.md)
- [Retenciones legales para datos de blob inmutables](immutable-legal-hold-overview.md)
