---
title: Cifrado de recursos seguros en Azure Automation
description: En este artículo se proporcionan conceptos para configurar la cuenta de Automation para usar el cifrado.
services: automation
ms.service: automation
ms.subservice: process-automation
author: snehithm
ms.author: snmuvva
ms.date: 07/27/2021
ms.topic: conceptual
manager: kmadnani
ms.openlocfilehash: 82f64ec617972b4e280e2b383c51829f648a3bef
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730593"
---
# <a name="encryption-of-secure-assets-in-azure-automation"></a>Cifrado de recursos seguros en Azure Automation

Los recursos protegidos en Azure Automation incluyen credenciales, certificados, conexiones y variables cifradas. Estos recursos se protegen en Azure Automation mediante varios niveles de cifrado. En función de la clave de nivel superior utilizada para el cifrado, hay dos modelos de cifrado:

- Usando claves administradas por Microsoft
- Uso de claves que administra

## <a name="microsoft-managed-keys"></a>Claves administradas por Microsoft

De forma predeterminada, la cuenta de Azure Automation usa claves de cifrado administradas por Microsoft.

Cada recurso seguro se cifra y se almacena en Azure Automation con una clave única(clave de cifrado de datos) que se genera para cada cuenta de Automation. Estas claves se cifran y almacenan en Azure Automation con otra clave única que se genera para cada cuenta, denominada clave de cifrado de cuenta (AEK). Estas claves de cifrado de cuenta se cifran y almacenan en Azure Automation mediante claves administradas por Microsoft.

## <a name="keys-that-you-manage-with-key-vault"></a>Claves que se administran con Key Vault

Puede administrar el cifrado de recursos seguros para su cuenta de Automation con sus propias claves. Cuando especifica una clave administrada por el cliente en el nivel de la cuenta de Automation, esa clave se usa para proteger y controlar el acceso a la clave de cifrado de cuenta para la cuenta de Automation. Esta, a su vez, se usa para cifrar y descifrar todos los recursos seguros. Las claves administradas por el cliente ofrecen más flexibilidad para crear, rotar, deshabilitar y revocar controles de acceso. También puede auditar las claves de cifrado que se usan para proteger los recursos seguros.

Use Azure Key Vault para almacenar las claves administradas por el cliente. Puede crear sus propias claves y almacenarlas en un almacén de claves, o puede usar las API de Azure Key Vault para generarlas. 

La habilitación de Azure Firewall en [Azure Key Vault](../key-vault/general/network-security.md) bloquea el acceso desde runbooks de Azure Automation para ese servicio. El acceso se bloqueará incluso cuando la excepción de firewall para permitir servicios de Microsoft de confianza esté habilitada, ya que Automation no forma parte de la lista de servicios de confianza. Con un firewall habilitado, el acceso solo se puede realizar mediante una instancia de Hybrid Runbook Worker y un [punto de conexión de servicio de red virtual](../key-vault/general/overview-vnet-service-endpoints.md).

Para obtener más información sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](../key-vault/general/overview.md)

## <a name="use-of-customer-managed-keys-for-an-automation-account"></a>Uso de las claves administradas por el cliente para una cuenta de Automation

Al usar el cifrado con claves administradas por el cliente para una cuenta de Automation, Azure Automation encapsula la clave de cifrado de la cuenta con la clave administrada por el cliente en el almacén de claves asociado. La habilitación de claves administradas por el cliente no afecta al rendimiento, y la cuenta se cifra con la nueva clave inmediatamente sin retraso alguno.

Las cuentas de Automation nuevas siempre se cifran mediante claves administradas por Microsoft. No es posible habilitar claves administradas por el cliente en el momento en que se crea la cuenta. Las claves administradas por el cliente se almacenan en Azure Key Vault, y el almacén de claves se debe aprovisionar con directivas de acceso que concedan permisos de clave a la identidad administrada que está asociada a la cuenta de Automation. La identidad administrada solo está disponible después de crear la cuenta de almacenamiento.

Al modificar la clave que se usa para el cifrado de recursos seguros de Azure Automation mediante la habilitación o deshabilitación de las claves administradas por el cliente, la actualización de la versión de la clave o la especificación de otra clave, el cifrado de la clave de cifrado de cuenta cambia, pero los recursos seguros de la cuenta de Azure Storage no tienen que volver a cifrarse.

> [!NOTE]
> Para habilitar las claves administradas por el cliente con llamadas API REST de Azure Automation, tendrá que usar la versión de API 2020-01-13-preview.

## <a name="prerequisites-for-using-customer-managed-keys-in-azure-automation"></a>Requisitos previos para el uso de claves administradas por el cliente en Azure Automation

Antes de habilitar las claves administradas por el cliente para una cuenta de Automation, debe asegurarse de que se cumplen los siguientes requisitos previos:

- Una instancia de [Azure Key Vault](../key-vault/general/basic-concepts.md) con las propiedades **Eliminación temporal** y **No purgar** habilitadas. Estas propiedades son necesarias para permitir la recuperación de claves en caso de eliminación accidental.
- Solo se admiten claves RSA con para el cifrado de Azure Automation. Para obtener más información acerca de las claves, consulte [Información acerca de claves, secretos y certificados de Azure Key Vault](../key-vault/general/about-keys-secrets-certificates.md).
- La cuenta de Automation y el almacén de claves pueden estar en distintas suscripciones, pero deben estar en el mismo inquilino de Azure Active Directory.
- Al usar PowerShell, compruebe que el [módulo Azure Az de PowerShell](/powershell/azure/new-azureps-module-az) está instalado. Para instalar o actualizar, vea [Instalación del módulo Azure Az de PowerShell](/powershell/azure/install-az-ps).

## <a name="generate-and-assign-a-new-system-assigned-identity-for-an-automation-account"></a>Generación y asignación de una nueva identidad asignada por el sistema para una cuenta de Automation

Para usar las claves administradas por el cliente con una cuenta de Automation, la cuenta de Automation debe autenticarse en el almacén de claves que contiene las claves administradas por el cliente. Azure Automation usa identidades administradas asignadas por el sistema para autenticar la cuenta con Azure Key Vault. Para más información sobre las identidades administradas, consulte el artículo sobre [Qué son las identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="using-powershell"></a>Usar PowerShell

Use el cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) de PowerShell para modificar una cuenta de Azure Automation existente. El parámetro `-AssignSystemIdentity` genera y asigna una nueva identidad asignada por el sistema para que la cuenta de Automation la use con otros servicios como Azure Key Vault. Para obtener más información, vea [¿Qué son las identidades administradas para recursos de Azure?](../active-directory/managed-identities-azure-resources/overview.md) y [Acerca de Azure Key Vault](../key-vault/general/overview.md). Ejecute el código siguiente:

```powershell
# Revise variables with your actual values.
$resourceGroup = "ResourceGroupName"
$automationAccount = "AutomationAccountName"
$vaultName = "KeyVaultName"
$keyName = "KeyName"

Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity
```

La salida debe tener una apariencia similar a la siguiente:

:::image type="content" source="./media/automation-secure-asset-encryption/set-azautomationaccount.png" alt-text="Salida del cmdlet Set-AzAutomationAccount.":::

Obtenga el valor `PrincipalId` para usarlo más adelante. Ejecute el código siguiente:

```powershell
$principalID = (Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Identity.PrincipalId

$principalID
```

### <a name="using-rest"></a>Uso de REST

Configure una identidad administrada asignada por el sistema para la cuenta de Automation mediante la siguiente llamada API REST:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Cuerpo de la solicitud:

```json
{ 
 "identity": 
 { 
  "type": "SystemAssigned" 
  } 
}
```

La identidad asignada por el sistema para la cuenta de Automation se devuelve en una respuesta similar a la siguiente:

```json
{
 "name": "automation-account-name",
 "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
 ..
 "identity": {
    "type": "SystemAssigned",
    "principalId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
    "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb"
 },
..
}
```

## <a name="configuration-of-the-key-vault-access-policy"></a>Configuración de la directiva de acceso de Key Vault

Una vez que se asigna una identidad administrada asignada por el sistema a la cuenta de Automation, se configura el acceso al almacén de claves que contiene las claves administradas por el cliente. Azure Automation necesita los permisos de operación **Get**, **Recover**, **WrapKey** y **UnwrapKey** para que la identidad acceda a las claves administradas por el cliente.

### <a name="using-powershell"></a>Usar PowerShell

Use el cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) de PowerShell para conceder los permisos necesarios. Después, use [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) para crear una clave en el almacén de claves. Ejecute el código siguiente:

```powershell
Set-AzKeyVaultAccessPolicy `
    -VaultName $vaultName `
    -ObjectId $principalID `
    -PermissionsToKeys Get, Recover, UnwrapKey, WrapKey

Add-AzKeyVaultKey `
    -VaultName $vaultName `
    -Name $keyName `
    -Destination 'Software'
```

La salida debe tener una apariencia similar a la siguiente:

:::image type="content" source="./media/automation-secure-asset-encryption/add-azkeyvaultkey.png" alt-text="Salida del cmdlet Add-AzKeyVaultKey.":::

### <a name="using-rest"></a>Uso de REST

La directiva de acceso se puede establecer mediante la siguiente llamada API REST:

```http
PUT https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/sample-group/providers/Microsoft.KeyVault/vaults/sample-vault/accessPolicies/add?api-version=2018-02-14
```

Cuerpo de la solicitud:

```json
{
  "properties": {
    "accessPolicies": [
      {
        "tenantId": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
        "objectId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
        "permissions": {
          "keys": [
            "get",
            "recover",
            "wrapKey",
            "unwrapKey"
          ],
          "secrets": [],
          "certificates": []
        }
      }
    ]
  }
}
```

> [!NOTE]
> Se deben proporcionar los campos **tenantId** y **objectId** con valores de **identity.tenantId** y **identity.principalId** respectivamente de la respuesta de la identidad administrada para la cuenta de Automation.

## <a name="reconfigure-automation-account-to-use-customer-managed-key"></a>Nueva configuración de la cuenta de Automation para usar la clave administrada por el cliente

Si quiere cambiar la cuenta de Automation de claves administradas por Microsoft a claves administradas por el cliente, puede hacerlo mediante Azure PowerShell o con una plantilla de Azure Resource Manager.

### <a name="using-powershell"></a>Usar PowerShell

Use el cmdlet [Set-AzAutomationAccount](/powershell/module/az.automation/set-azautomationaccount) de PowerShell para volver a configurar la cuenta de Automation para que use claves administradas por el cliente.

```powershell
$vaultURI = (Get-AzKeyVault -VaultName $vaultName).VaultUri
$keyVersion = (Get-AzKeyVaultKey -VaultName $vaultName -KeyName $keyName).Version

Set-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount `
    -AssignSystemIdentity `
    -KeyName $keyName `
    -KeyVaultUri $vaultURI `
    -KeyVersion $keyVersion `
    -KeyVaultEncryption 
```

Puede comprobar el cambio si ejecuta el siguiente comando:

```powershell
(Get-AzAutomationAccount `
    -ResourceGroupName $resourceGroup `
    -Name $automationAccount).Encryption `
    |  ConvertTo-Json 
```

La salida debe tener una apariencia similar a la siguiente:

:::image type="content" source="./media/automation-secure-asset-encryption/get-azautomationaccount.png" alt-text="Salida del cmdlet Get-AzAutomationAccount.":::

### <a name="using-rest"></a>Uso de REST

Use la siguiente llamada API REST:

```http
PATCH https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name?api-version=2020-01-13-preview
```

Cuerpo de la solicitud:

```json
{
    "identity": {
    "type": "SystemAssigned"
    },
    "properties": {
        "encryption": {
            "keySource": "Microsoft.Keyvault",
            "keyvaultProperties": {
                "keyName": "sample-vault-key",
                "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
                "keyVersion": "7c73556c521340209371eaf623cc099d"
            }
        }
    }
}
```

Respuesta de muestra

```json
{
  "name": "automation-account-name",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/resource-group-name/providers/Microsoft.Automation/automationAccounts/automation-account-name",
  ..
  "properties": {
    ..
    "encryption": {
      "keyvaultProperties": {
         "keyName": "sample-vault-key",
          "keyvaultUri": "https://sample-vault-key12.vault.azure.net",
          "keyVersion": "7c73556c521340209371eaf623cc099d"
      },
      "keySource": "Microsoft.Keyvault"
    },
    ..
  }
}
```

## <a name="rotation-of-a-customer-managed-key"></a>Rotación de una clave administrada por el cliente

Las claves administradas por el cliente se pueden rotar en Azure Key Vault según las directivas de cumplimiento. Cuando la clave rota, hay que actualizar la cuenta de Automation para usar el nuevo identificador URI de la clave.

El giro de la clave no desencadena un nuevo cifrado de los recursos seguros en la cuenta de Automation. No se necesita ninguna acción adicional.

## <a name="revocation-of-access-to-a-customer-managed-key"></a>Revocación del acceso de una clave administrada por el cliente

Para revocar el acceso a las claves administradas por el cliente, use PowerShell o la CLI de Azure. Para más información, consulte la referencia de [PowerShell para Azure Key Vault](/powershell/module/az.keyvault/) o la referencia de la [CLI para Azure Key Vault](/cli/azure/keyvault). La revocación del acceso bloquea de manera eficaz el acceso a todos los recursos seguros de la cuenta de Automation, ya que Azure Automation no puede acceder a la clave de cifrado.

## <a name="next-steps"></a>Pasos siguientes

- Para comprender Azure Key Vault, vea [¿Qué es Azure Key Vault?](../key-vault/general/overview.md).
- Para trabajar con certificados, vea [Administración de certificados en Azure Automation](shared-resources/certificates.md).
- Para manipular las credenciales, vea [Administración de credenciales en Azure Automation](shared-resources/credentials.md).
- Para usar variables de automatización, [Administración de variables en Azure Automation](shared-resources/variables.md).
- Para obtener ayuda al trabajar con conexiones, vea [Administración de conexiones en Azure Automation](automation-connections.md).
