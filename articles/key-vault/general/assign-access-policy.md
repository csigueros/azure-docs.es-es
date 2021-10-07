---
title: Asignación de una directiva de acceso de Azure Key Vault (CLI)
description: Cómo se usa la CLI de Azure para asignar una directiva de acceso de Key Vault a una identidad de aplicación o una entidad de seguridad.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: b194bec5f03d5985e282b7c8d220e268de1a763c
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837882"
---
# <a name="assign-a-key-vault-access-policy"></a>Asignación de una directiva de acceso de Key Vault

Una directiva de acceso de Key Vault determina si una entidad de seguridad concreta, es decir, un usuario, una aplicación o un grupo de usuarios, puede realizar distintas operaciones en los [secretos](../secrets/index.yml), las [claves](../keys/index.yml) y los [certificados](../certificates/index.yml) de Key Vault. Las directivas de acceso se pueden asignar mediante [Azure Portal](assign-access-policy-portal.md), la CLI de Azure o [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

# <a name="azure-portal"></a>[Azure Portal](#tab/azure-portal)

## <a name="assign-an-access-policy"></a>Asignación de directivas de acceso

1.  En [Azure Portal](https://portal.azure.com), vaya al recurso Key Vault. 

1.  En **Configuración**, seleccione **Directivas de acceso** y, después, **Agregar directiva de acceso**:

    ![Seleccionar directivas de acceso mediante la elección de Agregar asignación de roles](../media/authentication/assign-policy-portal-01.png)

1.  Seleccione los permisos que desee en **Permisos de los certificados**, **Permisos de las claves** y **Permisos de los secretos**. También puede seleccionar una plantilla que contenga combinaciones comunes de permisos:

    ![Especificación de permisos de la directiva de acceso](../media/authentication/assign-policy-portal-02.png)

1. En **Seleccionar la entidad de seguridad**, elija el vínculo **Ninguna seleccionada** para abrir el panel de selección **Entidad de seguridad**. Escriba el nombre del usuario, la aplicación o entidad de servicio en el campo de búsqueda, seleccione el resultado adecuado y elija **Seleccionar**.

    ![Selección de la entidad de seguridad para la directiva de acceso](../media/authentication/assign-policy-portal-03.png)

    Si usa una identidad administrada para la aplicación, busque y seleccione el nombre de la propia aplicación Para obtener más información sobre las entidades de seguridad, consulte [Autenticación de Key Vault](authentication.md).
 
1.  En el panel **Agregar directivas de acceso**, seleccione **Agregar** para guardar la directiva de acceso.

    ![Adición de la directiva de acceso con la entidad de seguridad asignada](../media/authentication/assign-policy-portal-04.png)

1. En la página **Directivas de acceso**, compruebe que la directiva de acceso aparece en **Directivas de acceso actuales** y seleccione **Guardar**. Las directivas de acceso no se aplican hasta que se guardan.

    ![Almacenamiento de los cambios en las directivas de acceso](../media/authentication/assign-policy-portal-05.png)

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para más información sobre cómo crear grupos en Azure Active Directory mediante la CLI de Azure, consulte [az ad group create](/cli/azure/ad/group#az_ad_group_create) y [az ad group member add](/cli/azure/ad/group/member#az_ad_group_member_add).

## <a name="configure-the-azure-cli-and-sign-in"></a>Configuración de la CLI de Azure e inicio de sesión en ella

1. Para ejecutar los comandos de la CLI de Azure en un entorno local, instale la [CLI de Azure](/cli/azure/install-azure-cli).
 
    Para ejecutar comandos directamente en la nube, use [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Solo la CLI local: inicie sesión en Azure mediante `az login`:

    ```bash
    az login
    ```

    El comando `az login` abre una ventana del explorador que recopila sus credenciales, en caso de que sea necesario.

## <a name="acquire-the-object-id"></a>Obtención del identificador del objeto

Determine el identificador del objeto de la aplicación, el grupo o el usuario al que desea asignar la directiva de acceso:

- Aplicaciones y otras entidades de servicio: use el comando [az ad sp list](/cli/azure/ad/sp#az_ad_sp_list) para recuperar las entidades de servicio. Examine la salida del comando para determinar el identificador de objeto de la entidad de seguridad a la que desea asignar la directiva de acceso.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Grupos: use el comando [az ad group list](/cli/azure/ad/group#az_ad_group_list) y filtre los resultados con el parámetro `--display-name`:

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Usuarios: use el comando [az ad user show](/cli/azure/ad/user#az_ad_user_show) y pase la dirección de correo electrónico del usuario en el parámetro `--id` :

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Asignación de una directiva de acceso
    
Use el comando [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) para asignar los permisos que desee:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Reemplace `<object-id>` por el identificador de objeto de la entidad de seguridad.

Solo necesita incluir `--secret-permissions`, `--key-permissions` y `--certificate-permissions` al asignar permisos a esos tipos concretos. Los valores que se permiten para `<secret-permissions>`, `<key-permissions>`y `<certificate-permissions>` se especifican en la documentación de [az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Para más información sobre cómo crear grupos en Azure Active Directory mediante Azure PowerShell, consulte [New-AzureADGroup](/powershell/module/azuread/new-azureadgroup) y [Add-AzADGroupMember](/powershell/module/az.resources/add-azadgroupmember).

## <a name="configure-powershell-and-sign-in"></a>Configuración de PowerShell e inicio de sesión

1. Para ejecutar los comandos localmente, instale [Azure PowerShell](/powershell/azure/) si todavía no lo ha hecho.

    Para ejecutar comandos directamente en la nube, use [Azure Cloud Shell](../../cloud-shell/overview.md).

1. Solo PowerShell local:

    1. Instale el [módulo PowerShell de Azure Active Directory](https://www.powershellgallery.com/packages/AzureAD).

    1. Inicie de sesión en Azure:

        ```azurepowershell-interactive
        Login-AzAccount
        ```
    
## <a name="acquire-the-object-id"></a>Obtención del identificador del objeto

Determine el identificador del objeto de la aplicación, el grupo o el usuario al que desea asignar la directiva de acceso:

- Aplicaciones y otras entidades de servicio: use el cmdlet [Get-AzADServicePrincipal](/powershell/module/az.resources/get-azadserviceprincipal) con el parámetro `-SearchString` para filtrar los resultados por el nombre de la entidad de servicio deseada:

    ```azurepowershell-interactive
    Get-AzADServicePrincipal -SearchString <search-string>
    ```

- Grupos: use el cmdlet [Get-AzADGroup](/powershell/module/az.resources/get-azadgroup) con el parámetro `-SearchString` para filtrar los resultados por el nombre del grupo deseado:

    ```azurepowershell-interactive
    Get-AzADGroup -SearchString <search-string>
    ```
    
    En la salida, el identificador de objeto aparece como `Id`.

- Usuarios: use el cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) y pase la dirección de correo electrónico del usuario al parámetro `-UserPrincipalName`.

    ```azurepowershell-interactive
     Get-AzAdUser -UserPrincipalName <email-address-of-user>
    ```

    En la salida, el identificador de objeto aparece como `Id`.

## <a name="assign-the-access-policy"></a>Asignación de una directiva de acceso

Use el cmdlet [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) para asignar la directiva de acceso:

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <key-vault-name> -ObjectId <Id> -PermissionsToSecrets <secrets-permissions> -PermissionsToKeys <keys-permissions> -PermissionsToCertificates <certificate-permissions    
```

Solo necesita incluir `-PermissionsToSecrets`, `-PermissionsToKeys` y `-PermissionsToCertificates` al asignar permisos a esos tipos concretos. Los valores permitidos para `<secret-permissions>`, `<key-permissions>` y `<certificate-permissions>` se proporcionan en la documentación de [Set-AzKeyVaultAccessPolicy: Parámetros](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy#parameters).

---

## <a name="next-steps"></a>Pasos siguientes

- [Seguridad de Azure Key Vault](security-features.md)
- [Guía del desarrollador de Azure Key Vault](developers-guide.md)
