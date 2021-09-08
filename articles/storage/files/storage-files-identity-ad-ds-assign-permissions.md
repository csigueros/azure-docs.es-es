---
title: Control del acceso a los recursos compartidos de archivos de Azure - Autenticación local de AD DS
description: Obtenga información sobre cómo asignar permisos a una identidad de Active Directory Domain Services que representa la cuenta de almacenamiento. Esto le permite controlar el acceso con la autenticación basada en identidades.
author: roygara
ms.service: storage
ms.subservice: files
ms.topic: how-to
ms.date: 07/13/2021
ms.author: rogarana
ms.custom: devx-track-azurepowershell, subject-rbac-steps
ms.openlocfilehash: 4c69a8bcd3acb559de3674dd7012220f4c7868e4
ms.sourcegitcommit: 6f4378f2afa31eddab91d84f7b33a58e3e7e78c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/13/2021
ms.locfileid: "113687162"
---
# <a name="part-two-assign-share-level-permissions-to-an-identity"></a>Parte 2: Asignación de permisos de nivel de recurso compartido a una identidad

Antes de comenzar este artículo, asegúrese de que ha completado el artículo anterior [Habilitación de la autenticación de AD DS para la cuenta](storage-files-identity-ad-ds-enable.md).

Una vez habilitada la autenticación de Active Directory Domain Services (AD DS) en la cuenta de almacenamiento, debe configurar los permisos de nivel de recurso compartido para poder acceder a los recursos compartidos de archivos. Hay dos maneras de asignar permisos de nivel de recurso compartido. Puede asignarlos a usuarios o grupos de usuarios específicos de Azure AD y puede asignarlos a todas las identidades autenticadas como permiso de nivel de recurso compartido predeterminado.

> [!IMPORTANT]
> El control administrativo total de un recurso compartido de archivos, incluida la capacidad de tomar posición de un archivo, requiere usar la clave de la cuenta de almacenamiento. El control administrativo no puede realizarse con credenciales de Azure AD.

## <a name="applies-to"></a>Se aplica a
| Tipo de recurso compartido de archivos | SMB | NFS |
|-|:-:|:-:|
| Recursos compartidos de archivos Estándar (GPv2), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Estándar (GPv2), GRS/GZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |
| Recursos compartidos de archivos Premium (FileStorage), LRS/ZRS | ![Sí](../media/icons/yes-icon.png) | ![No](../media/icons/no-icon.png) |

## <a name="which-configuration-should-you-use"></a>Qué configuración debe usar

La mayoría de los usuarios deben asignar permisos de nivel de recurso compartido a usuarios o grupos específicos de Azure AD y, después, usar listas de control de acceso de Windows para el control de acceso pormenorizado en el nivel de directorio y archivo. Esta es la configuración más estricta y segura.

Hay tres escenarios, sin embargo, en los que se recomienda usar permisos de nivel de recurso compartido predeterminados asignados a todas las identidades autenticadas:

- Si no puede sincronizar el entorno local de AD DS con Azure AD, puede usar un permiso de nivel de recurso compartido predeterminado. La asignación de este permiso le permite evitar el requisito de sincronización, ya que no es necesario especificar el permiso para las identidades en Azure AD. Después puede usar listas de control de acceso de Windows para la aplicación de permisos pormenorizados en los archivos y directorios.
- El entorno local de AD DS que está usando se sincroniza con una instancia de Azure AD diferente de la instancia en la que se implementa el recurso compartido.
    - Esto es habitual cuando se administran entornos multiinquilino. El uso del permiso de nivel de recurso compartido predeterminado le permite omitir el requisito de una identidad híbrida de Azure AD. Podrá seguir usando las listas de control de acceso de Windows en los archivos y directorios para la aplicación de permisos pormenorizados.
- Prefiere aplicar la autenticación solo mediante listas de control de acceso de Windows en el nivel de archivo y directorio. 

## <a name="share-level-permissions"></a>Asignación de permisos de nivel de recurso compartido

En la tabla siguiente se enumeran los permisos de nivel de recurso compartido y cómo se corresponden con los roles RBAC integrados:

|Roles integrados admitidos  |Descripción  |
|---------|---------|
|[Lector de recursos compartidos de SMB de datos de archivos de Storage](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-reader)     |Permite el acceso de lectura a los archivos y directorios de los recursos compartidos de Azure. Este rol es análogo a una ACL de recurso compartido de lectura en los servidores de archivos de Windows. [Más información](storage-files-identity-auth-active-directory-enable.md).         |
|[Colaborador de recursos compartidos de SMB de datos de archivos de Storage](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-contributor)     |Permite el acceso de lectura, escritura y eliminación a los archivos y directorios de los recursos compartidos de Azure. [Más información](storage-files-identity-auth-active-directory-enable.md).         |
|[Colaborador elevado de recursos compartidos de SMB de datos de archivos de Storage](../../role-based-access-control/built-in-roles.md#storage-file-data-smb-share-elevated-contributor)     |Permite el acceso de lectura, escritura, eliminación y modificación de ACL en los archivos y directorios de los recursos compartidos de Azure. Este rol es análogo a una ACL de recurso compartido de cambio en los servidores de archivos de Windows. [Más información](storage-files-identity-auth-active-directory-enable.md).         |


## <a name="share-level-permissions-for-specific-azure-ad-users-or-groups"></a>Permisos de nivel de recurso compartido para usuarios o grupos específicos de Azure AD

Si tiene previsto usar un usuario o grupo específico de Azure AD para acceder a recursos compartidos de archivos de Azure, esa identidad debe ser una identidad híbrida que exista tanto en el entorno local de AD DS como en el de Azure AD. Por ejemplo, supongamos que tiene un usuario en su instancia de AD, user1@onprem.contoso.com, que se ha sincronizado con Azure AD como user1@contoso.com con la sincronización de Azure AD Connect. Para que este usuario acceda a Azure Files, debe asignar los permisos de nivel de recurso compartido a user1@contoso.com. El mismo concepto se aplica a los grupos o entidades de servicio. Por este motivo, debe sincronizar los usuarios y grupos de su instancia de AD con Azure AD mediante la sincronización de Azure AD Connect. 

Los permisos de nivel de recurso compartido deben asignarse a la identidad de Azure AD que representa el mismo usuario o grupo en AD DS para admitir la autenticación de AD DS en el recurso compartido de archivos de Azure. La autenticación y autorización con identidades que solo existen en Azure AD, como las identidades administradas de Azure (MSI), no se admiten con la autenticación de AD DS.

Puede usar Azure Portal, el módulo Azure PowerShell o la CLI de Azure para asignar los roles integrados a la identidad de Azure AD de un usuario a fin de conceder permisos de nivel de recurso compartido.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Para asignar un rol de Azure a una identidad de Azure AD mediante [Azure Portal](https://portal.azure.com), siga estos pasos:

1. En Azure Portal, vaya al recurso compartido de archivos o [cree uno](storage-how-to-create-file-share.md).
1. Seleccione **Access Control (IAM)** .
1. Seleccione **Agregar una asignación de roles**.
1. En la hoja **Agregar asignación de roles**, seleccione el [rol integrado apropiado](#share-level-permissions) en la lista **Rol**.
    1. Lector de recursos compartidos de SMB de datos de archivos de almacenamiento
    1. Colaborador de recursos compartidos de SMB de datos de archivos de almacenamiento
    1. Colaborador con privilegios elevados de recursos compartidos de SMB de datos de archivos de almacenamiento
1.  Mantenga la opción **Asignar acceso a** en la configuración predeterminada: **Usuario, grupo o entidad de servicio de Azure AD**. Seleccione la identidad de Azure AD de destino por nombre o dirección de correo electrónico. **La identidad de Azure AD seleccionada debe ser una identidad híbrida y no puede ser una identidad solo en la nube.** Esto significa que la misma identidad también se representa en AD DS.
1. Seleccione **Guardar** para completar la operación de asignación de roles.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

El siguiente ejemplo de PowerShell muestra cómo asignar un rol de Azure a una identidad de Azure AD, según el nombre de inicio de sesión. Para más información sobre la asignación de roles de Azure mediante PowerShell, consulte [Incorporación o eliminación de asignaciones de roles de Azure con Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

Antes de ejecutar el siguiente script de ejemplo, reemplace los valores de marcador de posición, incluidos los corchetes, por los suyos.

```powershell
#Get the name of the custom role
$FileShareContributorRole = Get-AzRoleDefinition "<role-name>" #Use one of the built-in roles: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
#Constrain the scope to the target file share
$scope = "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
#Assign the custom role to the target identity with the specified scope.
New-AzRoleAssignment -SignInName <user-principal-name> -RoleDefinitionName $FileShareContributorRole.Name -Scope $scope
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)
  
El siguiente comando de la CLI 2.0 asigna un rol de Azure a una identidad de Azure AD, según el nombre de inicio de sesión. Para más información sobre la asignación de roles de Azure mediante la CLI de Azure, consulte [Incorporación o eliminación de asignaciones de roles mediante la CLI de Azure](../../role-based-access-control/role-assignments-cli.md). 

Antes de ejecutar el siguiente script de ejemplo, no olvide reemplazar los valores de marcador de posición, incluidos los corchetes, por los suyos propios.

```azurecli-interactive
#Assign the built-in role to the target identity: Storage File Data SMB Share Reader, Storage File Data SMB Share Contributor, Storage File Data SMB Share Elevated Contributor
az role assignment create --role "<role-name>" --assignee <user-principal-name> --scope "/subscriptions/<subscription-id>/resourceGroups/<resource-group>/providers/Microsoft.Storage/storageAccounts/<storage-account>/fileServices/default/fileshares/<share-name>"
```
---

## <a name="share-level-permissions-for-all-authenticated-identities"></a>Permisos de nivel de recurso compartido para todas las identidades autenticadas

Puede agregar un permiso de nivel de recurso compartido predeterminado en la cuenta de almacenamiento, en lugar de configurar permisos de nivel de recurso compartido para usuarios o grupos de Azure AD. Un permiso de nivel de recurso compartido predeterminado asignado a la cuenta de almacenamiento se aplica a todos los recursos compartidos contenidos en la cuenta de almacenamiento. 

Al establecer un permiso de nivel de recurso compartido predeterminado, todos los usuarios y grupos autenticados tendrán el mismo permiso. Los usuarios o grupos autenticados se identifican, ya que la identidad se puede autenticar en el entorno local de AD DS al que está asociado la cuenta de almacenamiento. El permiso de nivel de recurso compartido predeterminado se establece en **Ninguno** durante la inicialización, lo que implica que no se permite el acceso a los archivos y directorios en el recurso compartido de Azure.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Actualmente no se pueden asignar permisos a la cuenta de almacenamiento con Azure Portal. Use el módulo Azure PowerShell o la CLI de Azure.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

Puede usar el siguiente script para configurar los permisos predeterminados de nivel de recurso compartido en la cuenta de almacenamiento. Solo puede habilitar el permiso de nivel de recurso compartido predeterminado en las cuentas de almacenamiento asociadas a un servicio de directorio para la autenticación de archivos. 

Antes de ejecutar el siguiente script, asegúrese de que el módulo Az.Storage corresponde a la versión 3.7.0 o posterior.

```azurepowershell
$defaultPermission = "None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice

$account = Set-AzStorageAccount -ResourceGroupName "<resource-group-name-here>" -AccountName "<storage-account-name-here>" -DefaultSharePermission $defaultPermission

$account.AzureFilesIdentityBasedAuth
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Puede usar el siguiente script para configurar los permisos predeterminados de nivel de recurso compartido en la cuenta de almacenamiento. Solo puede habilitar el permiso de nivel de recurso compartido predeterminado en las cuentas de almacenamiento asociadas a un servicio de directorio para la autenticación de archivos. 

Antes de ejecutar el siguiente script, asegúrese de que la CLI de Azure corresponde a la versión 2.24.1 o posterior.

```azurecli
# Declare variables
storageAccountName="YourStorageAccountName"
resourceGroupName="YourResourceGroupName"
defaultPermission="None|StorageFileDataSmbShareContributor|StorageFileDataSmbShareReader|StorageFileDataSmbShareElevatedContributor" # Set the default permission of your choice


az storage account update --name $storageAccountName --resource-group $resourceGroupName --default-share-permission $defaultPermission
```
---

## <a name="what-happens-if-you-use-both-configurations"></a>Qué ocurre si usa ambas configuraciones

También puede asignar permisos a todos los usuarios autenticados de Azure AD, y a usuarios y grupos específicos de Azure AD. Con esta configuración, un usuario o grupo específico tendría el superconjunto de permisos que combina el permiso de nivel de recurso compartido predeterminado y la asignación de RBAC. Para ayudarle a entender cómo funciona este caso, veamos el siguiente ejemplo: supongamos que ha concedido a un usuario el rol **Lector de recursos compartidos de SMB de datos de archivos de almacenamiento** en el recurso compartido de destino. También ha concedido el permiso de nivel de recurso compartido predeterminado **Colaborador con privilegios elevados de recursos compartidos de SMB de datos de archivos de almacenamiento** a todos los usuarios autenticados. Con esta configuración, ese usuario concreto tendrá un nivel de acceso **Colaborador con privilegios elevados de recursos compartidos de SMB de datos de archivos de almacenamiento** al recurso compartido. Los permisos de nivel superior siempre tienen prioridad.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha asignado los permisos de nivel de recurso compartido, debe configurar los permisos de nivel de archivo y de directorio. Continúe con el artículo siguiente.

[Parte tres: Configuración de permisos de nivel de directorio y de archivo en SMB](storage-files-identity-ad-ds-configure-permissions.md)
