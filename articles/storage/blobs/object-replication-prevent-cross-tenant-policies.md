---
title: Impedir la replicación de objetos entre inquilinos de Azure Active Directory (versión preliminar)
titleSuffix: Azure Storage
description: Impedir la replicación de objetos entre inquilinos
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: tamram
ms.subservice: blobs
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: 2fe98c0a15b1ec07ff1608e00aa030fd18360547
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128599198"
---
# <a name="prevent-object-replication-across-azure-active-directory-tenants-preview"></a>Impedir la replicación de objetos entre inquilinos de Azure Active Directory (versión preliminar)

La replicación de objetos copia asincrónicamente blobs en bloque de un contenedor de una cuenta de almacenamiento a un contenedor de otra cuenta de almacenamiento. Al configurar una directiva de replicación de objetos, especifique la cuenta de origen y el contenedor, así como la cuenta de destino y el contenedor. Una vez configurada la directiva, Azure Storage copia automáticamente los resultados de las operaciones de creación, actualización y eliminación de un objeto de origen en el de destino. Para obtener más información sobre la replicación de objetos en Azure Storage, vea [Replicación de objetos para blobs en bloques](object-replication-overview.md).

De forma predeterminada, un usuario autorizado puede configurar una directiva de replicación de objetos en la que la cuenta de origen se encuentra en un inquilino de Azure Active Directory (Azure AD) y la cuenta de destino, en otro diferente. Si las directivas de seguridad requieren que restrinja la replicación de objetos a las cuentas de almacenamiento que residen solo en el mismo inquilino, puede no permitir la creación de directivas en las que las cuentas de origen y destino se encuentren en inquilinos diferentes (versión preliminar). De forma predeterminada, la replicación de objetos entre inquilinos está habilitada para una cuenta de almacenamiento, a menos que no lo permita explícitamente.

En este artículo se describe cómo corregir la replicación de objetos entre inquilinos para las cuentas de almacenamiento. También se describe cómo crear directivas para aplicar una prohibición en la replicación de objetos entre inquilinos para cuentas de almacenamiento nuevas y existentes.

Para más información sobre cómo configurar directivas de replicación de objetos, incluidas las directivas entre inquilinos, consulte [Configuración de la replicación de objetos para blobs en bloques](object-replication-configure.md).

## <a name="remediate-cross-tenant-object-replication"></a>Corrección de la replicación de objetos entre inquilinos

Para evitar la replicación de objetos entre inquilinos de Azure AD, establezca la propiedad **AllowCrossTenantReplication** de la cuenta de almacenamiento en **false**. Si una cuenta de almacenamiento no participa actualmente en ninguna directiva de replicación de objetos entre inquilinos, establecer la propiedad **AllowCrossTenantReplication** en *false* impide la configuración futura de directivas de replicación de objetos entre inquilinos con esta cuenta de almacenamiento como origen o destino. Sin embargo, si una cuenta de almacenamiento participa actualmente en una o varias directivas de replicación de objetos entre inquilinos, no se permite establecer la propiedad **AllowCrossTenantReplication** en *false* hasta que elimine las directivas entre inquilinos existentes.

Las directivas entre inquilinos se permiten de forma predeterminada para una cuenta de almacenamiento. Sin embargo, la propiedad **AllowCrossTenantReplication** no se establece de forma predeterminada para una cuenta de almacenamiento nueva o existente y no devuelve ningún valor hasta que se establece explícitamente. La cuenta de almacenamiento puede participar en directivas de replicación de objetos entre inquilinos cuando el valor de la propiedad es **null** o **true**.

### <a name="remediate-cross-tenant-replication-for-a-new-account"></a>Corrección de la replicación entre inquilinos para una nueva cuenta

Para no permitir la replicación entre inquilinos para una nueva cuenta de almacenamiento, use Azure Portal, PowerShell o la CLI de Azure.

#### <a name="portal"></a>[Portal](#tab/portal)

Para no permitir la replicación de objetos entre inquilinos para una nueva cuenta de almacenamiento, siga estos pasos:

1. En Azure Portal, vaya a la página **Cuentas de almacenamiento** y seleccione **Crear**.
1. Rellene la pestaña **Aspectos básicos** para la nueva cuenta de almacenamiento.
1. En la pestaña **Opciones avanzadas,** en la sección **Blob Storage**, busque la opción **Permitir replicación entre inquilinos** y desactive la casilla.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-create-account.png" alt-text="Captura de pantalla que muestra cómo no permitir la replicación de objetos entre inquilinos para una nueva cuenta de almacenamiento":::

1. Complete el proceso de creación de la cuenta.

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para no permitir la replicación de objetos entre inquilinos para una nueva cuenta de almacenamiento, llame al comando [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) e incluya el parámetro `AllowCrossTenantReplication` con un valor *$false*.

```azurepowershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"
$location = "<location>"

# Create a storage account and disallow cross-tenant replication.
New-AzStorageAccount -ResourceGroupName $rgName `
    -Name $accountName `
    -Location $location `
    -SkuName Standard_LRS
    -AllowCrossTenantReplication $false

# Read the property for the new storage account
(Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName).AllowCrossTenantReplication
```

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para no permitir la replicación de objetos entre inquilinos para una nueva cuenta de almacenamiento, llame al comando [az storage account create](/cli/azure/storage/account#az_storage_account_create) e incluya el parámetro `allow-cross-tenant-replication` con un valor *$false*.

```azurecli
# Create a storage account with cross-tenant replication disallowed.
az storage account create \
    --name <storage-account> \
    --resource-group <resource-group> \
    --location <location> \
    --sku Standard_LRS
    --allow-cross-tenant-replication false

# Read the property for the new storage account
az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query allowCrossTenantReplication \
    --output tsv
```

---

### <a name="remediate-cross-tenant-replication-for-an-existing-account"></a>Corrección de la replicación entre inquilinos para una cuenta existente

Para no permitir la replicación entre inquilinos para una cuenta de almacenamiento existente, use Azure Portal, PowerShell o la CLI de Azure.

#### <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Para no permitir la replicación de objetos entre inquilinos para una cuenta de almacenamiento existente que no participa actualmente en ninguna directiva entre inquilinos, siga estos pasos:

1. Vaya a la cuenta de almacenamiento en Azure Portal.
1. En **Administración de datos**, seleccione **Replicación de objetos**.
1. Seleccione **Configuración avanzada**.
1. Desactive **Permitir replicación entre inquilinos**. De forma predeterminada, esta casilla está activada, porque se permite la replicación de objetos entre inquilinos para una cuenta de almacenamiento a menos que no se permita explícitamente.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-object-replication-portal-update-account.png" alt-text="Captura de pantalla que muestra cómo no permitir la replicación de objetos entre inquilinos para una cuenta de almacenamiento existente":::

1. Seleccione **Aceptar** para guardar los cambios.

Si la cuenta de almacenamiento participa actualmente en una o varias directivas de replicación entre inquilinos, no podrá permitir la replicación de objetos entre inquilinos hasta que elimine esas directivas. En este escenario, la configuración no está disponible en Azure Portal, tal como se muestra en la siguiente imagen.

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/cross-tenant-object-replication-policies-in-effect-portal.png" alt-text="Instantánea":::

#### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para no permitir la replicación de objetos entre inquilinos para una cuenta de almacenamiento existente que no participa actualmente en ninguna directiva entre inquilinos, primero instale la versión 3.7.0 o posterior del [módulo de PowerShell Az.Storage](https://www.powershellgallery.com/packages/Az.Storage)\,. A continuación, configure la propiedad **AllowCrossTenantReplication** para la cuenta de almacenamiento.

En el siguiente ejemplo se muestra cómo denegar la replicación de objetos entre inquilinos en una cuenta de almacenamiento existente con PowerShell. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```powershell
$rgName = "<resource-group>"
$accountName = "<storage-account>"

Set-AzStorageAccount -ResourceGroupName $rgName `
    -AccountName $accountName `
    -AllowCrossTenantReplication $false
```

Si la cuenta de almacenamiento participa actualmente en una o varias directivas de replicación entre inquilinos, no podrá permitir la replicación de objetos entre inquilinos hasta que elimine esas directivas. PowerShell proporciona un error que indica que hubo un problema en la operación debido a las directivas de replicación entre inquilinos existentes.

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para no permitir la replicación de objetos entre inquilinos para una cuenta de almacenamiento existente que no participa actualmente en ninguna directiva entre inquilinos, primero instale la versión 2.24.0 o posterior de la CLI de Azure. Para más información, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli). A continuación, configure la propiedad **AllowCrossTenantReplication** para una cuenta de almacenamiento nueva o existente.

En el siguiente ejemplo se muestra cómo denegar la replicación de objetos entre inquilinos en una cuenta de almacenamiento existente con la CLI de Azure. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-cross-tenant-replication false
```

Si la cuenta de almacenamiento participa actualmente en una o varias directivas de replicación entre inquilinos, no podrá permitir la replicación de objetos entre inquilinos hasta que elimine esas directivas. La CLI de Azure proporciona un error que indica que hubo un problema en la operación debido a las directivas de replicación entre inquilinos existentes.

---

Después de no permitir la replicación entre inquilinos, se produce un error al intentar configurar una directiva entre inquilinos con la cuenta de almacenamiento como origen o destino. Azure Storage devuelve un error que indica que no se permite la replicación de objetos entre inquilinos para la cuenta de almacenamiento.

Cuando no se permite la replicación de objetos entre inquilinos para una cuenta de almacenamiento, las nuevas directivas de replicación de objetos que cree con esa cuenta deben incluir los identificadores de Azure Resource Manager completos para la cuenta de origen y de destino. Azure Storage requiere el identificador de recurso completo para comprobar si las cuentas de origen y destino residen en el mismo inquilino. Para obtener más información, vea [Especificar identificadores de recursos completos para las cuentas de origen y de destino](object-replication-overview.md#specify-full-resource-ids-for-the-source-and-destination-accounts).

La propiedad **AllowCrossTenantReplication** se admite en cuentas de almacenamiento que usan únicamente el modelo de implementación de Azure Resource Manager. Para información sobre qué cuentas de almacenamiento usan el modelo de implementación de Azure Resource Manager, consulte [Tipos de cuentas de almacenamiento](../common/storage-account-overview.md#types-of-storage-accounts).

## <a name="permissions-for-allowing-or-disallowing-cross-tenant-replication"></a>Permisos para permitir o no permitir la replicación entre inquilinos

Para establecer la propiedad **AllowCrossTenantReplication** para una cuenta de almacenamiento, un usuario debe tener permisos para crear y administrar cuentas de almacenamiento. Los roles de control de acceso basado en rol de Azure (Azure RBAC) que proporcionan estos permisos incluyen la acción **Microsoft.Storage/storageAccounts/write** o **Microsoft.Storage/storageAccounts/\*** . Los roles integrados con esta acción incluyen:

- El rol [Propietario](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager
- El rol [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) de Azure Resource Manager
- El rol [Colaborador de la cuenta de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Estos roles no proporcionan acceso a los datos de una cuenta de almacenamiento a través de Azure Active Directory (Azure AD). Sin embargo, incluyen **Microsoft.Storage/storageAccounts/listkeys/action**, que concede acceso a las claves de acceso de la cuenta. Con este permiso, un usuario puede usar las claves de acceso de la cuenta para acceder a todos los datos de una cuenta de almacenamiento.

Las asignaciones de roles deben tener el ámbito del nivel de la cuenta de almacenamiento o superior para permitir que un usuario permita o deniegue la replicación de objetos entre inquilinos para la cuenta de almacenamiento. Para obtener más información sobre el ámbito de los roles, vea [Comprensión del ámbito para RBAC de Azure](../../role-based-access-control/scope-overview.md).

Tenga cuidado de restringir la asignación de estos roles solo a aquellos usuarios que requieran la capacidad de crear una cuenta de almacenamiento o actualizar sus propiedades. Use el principio de privilegios mínimos para asegurarse de que los usuarios tienen los permisos mínimos que necesitan para realizar sus tareas. Para más información sobre la administración del acceso con RBAC de Azure, consulte [Procedimientos recomendados para RBAC de Azure](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Los roles clásicos de administrador de suscripciones Administrador del servicio y Coadministrador equivalen al rol [Propietario](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager. El rol **Propietario** incluye todas las acciones, por lo que un usuario con uno de estos roles administrativos también puede crear y administrar cuentas de almacenamiento. Para más información, consulte [Roles de administrador de suscripciones clásico, de Azure y de administrador de Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="use-azure-policy-to-audit-for-compliance"></a>Uso de Azure Policy para auditar el cumplimiento

Si tiene un gran número de cuentas de almacenamiento, tal vez quiera realizar una auditoría para asegurarse de que esas cuentas están configuradas para impedir la replicación de objetos entre inquilinos. Para auditar un conjunto de cuentas de almacenamiento para su cumplimiento, use Azure Policy. Azure Policy es un servicio que puede usar para crear, asignar y administrar directivas que aplican reglas a los recursos de Azure. Azure Policy le permite mantener esos recursos conforme a lo establecido en los estándares corporativos y los contratos de nivel de servicio. Para más información, consulte la [Introducción a Azure Policy](../../governance/policy/overview.md).

### <a name="create-a-policy-with-an-audit-effect"></a>Creación de una directiva con un efecto de auditoría

Azure Policy admite efectos que determinan lo que sucede cuando una regla de directiva se evalúa con respecto a un recurso. El efecto de auditoría crea una advertencia cuando un recurso no cumple los requisitos, pero no detiene la solicitud. Para más información, consulte [Comprender los efectos de Azure Policy](../../governance/policy/concepts/effects.md).

Para crear una directiva con un efecto de auditoría para la configuración de replicación de objetos entre inquilinos de una cuenta de almacenamiento con Azure Portal, siga estos pasos:

1. En Azure Portal, vaya al servicio Azure Policy.
1. Seleccione **Definiciones** en la sección **Creación**.
1. Seleccione **Agregar definición de directiva** para crear una nueva definición de directiva.
1. En el campo donde se indica la **ubicación de la definición**, seleccione el botón **Más** para especificar dónde se encuentra el recurso de directiva de auditoría.
1. Escriba un nombre para la directiva. Si lo desea, puede escribir también una descripción y la categoría.
1. En **Regla de directivas**, agregue la siguiente definición de directiva a la sección **policyRule**.

    ```json
    {
      "if": {
        "allOf": [
          {
            "field": "type",
            "equals": "Microsoft.Storage/storageAccounts"
          },
          {
            "not": {
              "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
              "equals": "false"
            }
          }
        ]
      },
      "then": {
        "effect": "audit"
      }
    }
    ```

1. Guarde la directiva.

### <a name="assign-the-policy"></a>Asignación de la directiva

A continuación, asigne la directiva a un recurso. El ámbito de la directiva corresponde a ese recurso y a todos los recursos que hay debajo del mismo. Para más información sobre la asignación de directivas, consulte [Estructura de asignaciones de Azure Policy](../../governance/policy/concepts/assignment-structure.md).

Para asignar la directiva con Azure Portal, haga lo siguiente:

1. En Azure Portal, vaya al servicio Azure Policy.
1. Seleccione **Asignaciones** en la sección **Creación**.
1. Seleccione **Asignar directiva** para crear una nueva asignación de directiva.
1. En el campo **Ámbito**, seleccione el ámbito de la asignación de directiva.
1. En el campo **Definición de directiva**, seleccione el botón **Más** y, a continuación, seleccione la directiva que definió en la sección anterior de la lista.
1. Escriba un nombre para la asignación de directiva. La descripción es opcional.
1. Deje la opción **Cumplimiento de directivas** como *Habilitada*. Esta configuración no tiene ningún efecto en la directiva de auditoría.
1. Seleccione **Revisar y crear** para crear la asignación.

### <a name="view-compliance-report"></a>Ver el informe de cumplimiento

Una vez asignada la directiva, puede ver el informe de cumplimiento. El informe de cumplimiento de una directiva de auditoría proporciona información sobre qué cuentas de almacenamiento siguen permitiendo directivas de replicación de objetos entre inquilinos. Para más información, consulte [Obtención de datos de cumplimiento de directiva](../../governance/policy/how-to/get-compliance-data.md).

El informe de cumplimiento puede tardar varios minutos en estar disponible después de que se cree la asignación de directiva.

Para ver el informe de cumplimiento en Azure Portal, siga estos pasos:

1. En Azure Portal, vaya al servicio Azure Policy.
1. Seleccione **Cumplimiento**.
1. Filtre los resultados por el nombre de la asignación de directiva que creó en el paso anterior. El informe muestra los recursos que no cumplen la directiva.
1. Puede explorar en profundidad el informe para obtener más detalles, incluida una lista de cuentas de almacenamiento que no cumplen los requisitos.

    :::image type="content" source="media/object-replication-prevent-cross-tenant-policies/compliance-report-cross-tenant-audit-effect-policy.png" alt-text="Captura de pantalla que muestra el informe de cumplimiento de la directiva de auditoría para la replicación de objetos entre inquilinos de blobs":::

## <a name="use-azure-policy-to-enforce-same-tenant-replication-policies"></a>Usar Azure Policy para aplicar las directivas de replicación en el mismo inquilino

Azure Policy admite la gobernanza en la nube, asegurándose así de que los recursos de Azure cumplen los requisitos y los estándares establecidos. Para asegurarse de que las cuentas de almacenamiento de su organización no permitan la replicación entre clientes, puede crear una directiva que impida la creación de una nueva cuenta de almacenamiento que admita directivas de replicación de objetos entre inquilinos. La directiva de cumplimiento usa el efecto de denegación para evitar una solicitud que podría crear o modificar una cuenta de almacenamiento para permitir la replicación de objetos entre inquilinos. La directiva de denegación también impedirá todos los cambios de configuración en una cuenta existente si la configuración de la replicación de objetos entre inquilinos no es compatible con la directiva. Para más información sobre los efecto de la denegación, consulte [Comprender los efectos de Azure Policy](../../governance/policy/concepts/effects.md).

Para crear una directiva con un efecto de denegación para la replicación de objetos entre inquilinos, siga los mismos pasos que se indican en [Uso de Azure Policy para auditar el cumplimiento](#use-azure-policy-to-audit-for-compliance), pero proporcione el siguiente código JSON en la sección **policyRule** de la definición de directivas:

```json
{
  "if": {
    "allOf": [
      {
        "field": "type",
        "equals": "Microsoft.Storage/storageAccounts"
      },
      {
        "not": {
          "field":"Microsoft.Storage/storageAccounts/allowCrossTenantReplication",
          "equals": "false"
        }
      }
    ]
  },
  "then": {
    "effect": "deny"
  }
}
```

Después de crear la directiva con el efecto de denegación y asignarla a un ámbito, un usuario no puede crear una cuenta de almacenamiento que permita la replicación de objetos entre inquilinos. Asimismo, los usuarios tampoco pueden realizar cambios de configuración en una cuenta de almacenamiento existente que actualmente permita la replicación de objetos entre inquilinos. Si intentan hacerlo, se producirá un error. La propiedad **AllowCrossTenantReplication** de la cuenta de almacenamiento debe establecerse en **false** para continuar con la creación o las actualizaciones de configuración de la cuenta, de acuerdo con la directiva.

En la siguiente imagen se muestra el error que se produce si se intenta crear una cuenta de almacenamiento que permita la replicación de objetos entre inquilinos (el valor predeterminado de una nueva cuenta) cuando una directiva con un efecto de denegación requiere que no se permita la replicación de objetos entre inquilinos.

:::image type="content" source="media/object-replication-prevent-cross-tenant-policies/disallow-cross-tenant-replication-deny-policy-error-portal.png" alt-text="Captura de pantalla que muestra el error que se produce al crear una cuenta de almacenamiento que infringe la directiva":::

## <a name="see-also"></a>Consulte también

- [Replicación de objetos para blobs en bloques](object-replication-overview.md)
- [Configuración de la replicación de objetos para blobs en bloques](object-replication-configure.md)
