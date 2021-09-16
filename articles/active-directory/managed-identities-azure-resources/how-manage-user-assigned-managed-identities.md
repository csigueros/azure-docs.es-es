---
title: 'Administración de identidades administradas asignadas por el usuario: Azure AD'
description: Cree identidades administradas asignadas por el usuario.
services: active-directory
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: how-to
ms.workload: identity
ms.date: 06/08/2021
ms.author: barclayn
zone_pivot_groups: identity-mi-methods
ms.openlocfilehash: 83f47dbbdd0968a845871b106be9142fd67f9967
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "122651622"
---
# <a name="manage-user-assigned-managed-identities"></a>Administración de identidades administradas asignadas por el usuario



Las identidades administradas para los recursos de Azure eliminan la necesidad de administrar las credenciales en el código. Puede usarlas para obtener un token de Azure Active Directory (Azure AD), que sus aplicaciones podrán usar al acceder a recursos que admitan autenticación de este servicio. Azure administra la identidad para que usted no tenga que hacerlo.

Hay dos tipos de identidades administradas: asignadas por el sistema y asignadas por el usuario. La diferencia principal entre los dos tipos es que las identidades administradas asignadas por el sistema tienen un ciclo de vida vinculado al recurso donde se usan. Las identidades administradas asignadas por el usuario se pueden usar en varios recursos. Para obtener más información sobre las identidades administradas, consulte [¿Qué son las identidades administradas para los recursos de Azure?](overview.md).

::: zone pivot="identity-mi-methods-azp"
En este artículo obtendrá información sobre cómo usar Azure Portal para crear y eliminar una identidad administrada asignada por el usuario, obtener una lista de sus identidades, y concederles un rol.

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con las identidades administradas de los recursos de Azure, consulte la [sección de introducción](overview.md). No olvide revisar la [diferencia entre una identidad administrada asignada por el sistema y una identidad administrada asignada por el usuario](overview.md#managed-identity-types) .
- Si aún no tiene una, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de continuar.


## <a name="create-a-user-assigned-managed-identity"></a>Crear una identidad administrada asignada por el usuario

Para crear una identidad administrada asignada por el usuario, la cuenta requiere la asignación del rol [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure para crear la identidad administrada asignada por el usuario.
1. En el cuadro de búsqueda, escriba **Identidades administradas**. En **Servicios**, seleccione **Identidades administradas**.
1. Seleccione **Agregar** y escriba valores en los siguientes cuadros del panel **Crear identidad administrada asignada por el usuario**:
    - **Suscripción**: elija la suscripción donde crear la identidad administrada asignada por el usuario.
    - **Grupo de recursos:** elija un grupo de recursos en el que crear la identidad administrada asignada por el usuario, o bien seleccione **Crear nuevo** para crear un grupo.
    - **Región:** elija una región para implementar la identidad administrada asignada por el usuario, por ejemplo, **Oeste de EE. UU.**
    - **Nombre:** especifique el nombre de la identidad administrada asignada por el usuario, por ejemplo, "UAI1".
  
[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]
  
   ![Captura de pantalla en la que se muestra el panel "Crear identidad administrada asignada por el usuario"](media/how-to-manage-ua-identity-portal/create-user-assigned-managed-identity-portal.png)
1. Seleccione **Revisar y crear** para revisar los cambios.
1. Seleccione **Crear**.

## <a name="list-user-assigned-managed-identities"></a>Enumerar identidades administradas asignadas por el usuario

Para visualizar o leer una identidad administrada asignada por el usuario, es necesario que se asignen a su cuenta los roles [Operador de identidades administradas ](../../role-based-access-control/built-in-roles.md#managed-identity-operator) o [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure para obtener una lista de las identidades administradas asignadas por el usuario.
1. En el cuadro de búsqueda, escriba **Identidades administradas**. En **Servicios**, seleccione **Identidades administradas**.
1. Se devuelve una lista de las identidades administradas asignadas por el usuario de la suscripción. Para ver los detalles de una identidad administrada asignada por el usuario, seleccione el nombre.

   ![Captura de pantalla en la que se muestra la lista de identidades administradas asignadas por el usuario](media/how-to-manage-ua-identity-portal/list-user-assigned-managed-identity-portal.png)

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar una identidad administrada asignada por el usuario

Para eliminar una identidad administrada asignada por el usuario, la cuenta requiere la asignación del rol [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Al eliminar una identidad asignada por el usuario, no se elimina de ninguna VM ni ningún recurso a los que estuviera asignada. Para eliminar la identidad asignada por el usuario de una VM, consulte [Eliminación de una identidad administrada asignada por el usuario de una VM](qs-configure-portal-windows-vm.md#remove-a-user-assigned-managed-identity-from-a-vm).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure para eliminar una identidad administrada asignada por el usuario.
1. Seleccione la identidad administrada asignada por el usuario y haga clic en **Eliminar**.
1. En el cuadro de confirmación, seleccione **SÍ**.

   ![Captura de pantalla en la que se muestra la eliminación de identidades administradas asignadas por el usuario](media/how-to-manage-ua-identity-portal/delete-user-assigned-managed-identity-portal.png)

## <a name="assign-a-role-to-a-user-assigned-managed-identity"></a>Asignación de un rol a una identidad administrada asignada por el usuario 

Para asignar un rol a una identidad administrada asignada por el usuario, la cuenta requiere la asignación del rol [Administrador de acceso de usuario](../../role-based-access-control/built-in-roles.md#user-access-administrator).

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure para obtener una lista de las identidades administradas asignadas por el usuario.
1. En el cuadro de búsqueda, escriba **Identidades administradas**. En **Servicios**, seleccione **Identidades administradas**.
1. Se devuelve una lista de las identidades administradas asignadas por el usuario de la suscripción. Seleccione la identidad administrada asignada por el usuario a la que desea asignar un rol.
1. Seleccione **Control de acceso (IAM)** y después **Agregar asignación de rol**.

   ![Captura de pantalla en la que se muestra el inicio de una identidad administrada asignada por el usuario](media/how-to-manage-ua-identity-portal/assign-role-screenshot1.png)

1. En el panel **Agregar asignación de roles**, configure los siguientes valores y, a continuación, seleccione **Guardar**:
   - **Rol:** : rol que se va a asignar.
   - **Asignar acceso a:** recurso al que se va a asignar la identidad administrada asignada por el usuario.
   - **Seleccionar:** miembro al que se va a conceder acceso.
   
   ![Captura de pantalla en la que se muestra la IAM de identidades administradas asignadas por el usuario](media/how-to-manage-ua-identity-portal/assign-role-screenshot2.png)



::: zone-end



::: zone pivot="identity-mi-methods-azcli"

En este artículo obtendrá información sobre cómo usar la CLI de Azure para crear y eliminar una identidad administrada asignada por el usuario, obtener una lista de sus identidades, y concederles un rol.

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con las identidades administradas de los recursos de Azure, consulte la [sección de introducción](overview.md). *No olvide revisar la [diferencia entre una identidad administrada asignada por el sistema y una identidad administrada asignada por el usuario](overview.md#managed-identity-types)* .
- Si aún no tiene una, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de continuar.


[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!IMPORTANT]  
> Con el fin de modificar los permisos de usuario al usar una entidad de servicio de aplicación mediante la CLI, debe proporcionar más permisos a la entidad de servicio en Graph API de Azure Active Directory, ya que partes de la CLI realizan solicitudes GET a Graph API. De lo contrario, puede acabar recibiendo el mensaje "No tiene privilegios suficientes para completar la operación". Para completar este paso, vaya al **registro de aplicaciones** en Azure AD, seleccione **Permisos de API**, desplácese hacia abajo y seleccione **Azure Active Directory Graph**. Allí, seleccione **Permisos de la aplicación** y, a continuación, agregue los permisos correspondientes. 

## <a name="create-a-user-assigned-managed-identity"></a>Crear una identidad administrada asignada por el usuario 

Para crear una identidad administrada asignada por el usuario, la cuenta requiere la asignación del rol [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Use el comando [az identity create](/cli/azure/identity#az_identity_create) para crear una identidad administrada asignada por el usuario. El parámetro `-g` especifica el grupo de recursos donde se debe crear la identidad administrada asignada por el usuario. El parámetro `-n` especifica su nombre. Reemplace los valores de parámetro `<RESOURCE GROUP>` y `<USER ASSIGNED IDENTITY NAME>` por sus propios valores.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurecli-interactive
az identity create -g <RESOURCE GROUP> -n <USER ASSIGNED IDENTITY NAME>
```
## <a name="list-user-assigned-managed-identities"></a>Enumerar identidades administradas asignadas por el usuario

Para visualizar o leer una identidad administrada asignada por el usuario, es necesario que se asignen a su cuenta los roles [Operador de identidades administradas ](../../role-based-access-control/built-in-roles.md#managed-identity-operator) o [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para enumerar las identidades administradas asignadas por el usuario, use el comando [az identity list](/cli/azure/identity#az_identity_list). Reemplace el valor `<RESOURCE GROUP>` por su propio valor.

```azurecli-interactive
az identity list -g <RESOURCE GROUP>
```

En la respuesta JSON, las identidades administradas asignadas por el usuario obtienen el valor `"Microsoft.ManagedIdentity/userAssignedIdentities"` para la clave `type`.

`"type": "Microsoft.ManagedIdentity/userAssignedIdentities"`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar una identidad administrada asignada por el usuario

Para eliminar una identidad administrada asignada por el usuario, la cuenta requiere la asignación del rol [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para eliminar una identidad administrada asignada por el usuario, use el comando [az identity delete](/cli/azure/identity#az_identity_delete). El parámetro -n especifica su nombre. El parámetro -g especifica el grupo de recursos donde se creó la identidad administrada asignada por el usuario. Reemplace los valores de parámetro `<USER ASSIGNED IDENTITY NAME>` y `<RESOURCE GROUP>` por sus propios valores.

```azurecli-interactive
az identity delete -n <USER ASSIGNED IDENTITY NAME> -g <RESOURCE GROUP>
```
> [!NOTE]
> Al eliminar una identidad administrada asignada por el usuario, no se eliminará la referencia de ningún recurso al que se haya asignado. Para eliminarlos de una máquina virtual o un conjunto de escalado de máquinas virtuales, use el comando `az vm/vmss identity remove`.

## <a name="next-steps"></a>Pasos siguientes

Para obtener una lista completa de comandos de identidad de la CLI de Azure, consulte [az identity](/cli/azure/identity).

Para obtener información sobre cómo asignar una identidad administrada asignada por el usuario a una VM de Azure, consulte [Configuración de identidades administradas para recursos de Azure en una VM de Azure mediante la CLI de Azure](qs-configure-cli-windows-vm.md#user-assigned-managed-identity).


::: zone-end

::: zone pivot="identity-mi-methods-powershell"

En este artículo obtendrá información sobre cómo usar PowerShell para crear y eliminar una identidad administrada asignada por el usuario, obtener una lista de sus identidades, y concederles un rol.

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con las identidades administradas de los recursos de Azure, consulte la [sección de introducción](overview.md). *No olvide revisar la [diferencia entre una identidad administrada asignada por el sistema y una identidad administrada asignada por el usuario](overview.md#managed-identity-types)* .
- Si aún no tiene una, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de continuar.
- Para ejecutar los scripts de ejemplo, tiene dos opciones:
    - Use [Azure Cloud Shell](../../cloud-shell/overview.md), que puede abrir mediante el botón **Probar**, ubicado en la esquina superior derecha de los bloques de código.
    - Ejecute los scripts localmente con Azure PowerShell, tal como se describe en la sección siguiente.

En este artículo obtendrá información sobre cómo usar PowerShell para crear y eliminar una identidad administrada asignada por el usuario, y obtener una lista de sus identidades.

### <a name="configure-azure-powershell-locally"></a>Configuración de Azure PowerShell de forma local

Para usar Azure PowerShell localmente con este artículo en lugar de usar Cloud Shell:

1. Instale [la versión más reciente de Azure PowerShell](/powershell/azure/install-az-ps) si aún no lo ha hecho.

1. Inicie sesión en Azure.

    ```azurepowershell
    Connect-AzAccount
    ```

1. Instalar la [versión más reciente de PowerShellGet](/powershell/scripting/gallery/installing-psget#for-systems-with-powershell-50-or-newer-you-can-install-the-latest-powershellget).

    ```azurepowershell
    Install-Module -Name PowerShellGet -AllowPrerelease
    ```

    Es posible que necesite `Exit` fuera de la sesión de PowerShell actual después de ejecutar este comando para el siguiente paso.

1. Instale la versión preliminar del módulo `Az.ManagedServiceIdentity` con el fin de realizar las operaciones con identidades administradas asignadas por el usuario que se indican en este artículo.

    ```azurepowershell
    Install-Module -Name Az.ManagedServiceIdentity -AllowPrerelease
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Crear una identidad administrada asignada por el usuario

Para crear una identidad administrada asignada por el usuario, la cuenta requiere la asignación del rol [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para crear una identidad administrada asignada por el usuario, use el comando `New-AzUserAssignedIdentity`. El parámetro `ResourceGroupName` especifica el grupo de recursos donde se debe crear la identidad administrada asignada por el usuario. El parámetro `-Name` especifica su nombre. Reemplace los valores de parámetro `<RESOURCE GROUP>` y `<USER ASSIGNED IDENTITY NAME>` por sus propios valores.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName <RESOURCEGROUP> -Name <USER ASSIGNED IDENTITY NAME>
```

## <a name="list-user-assigned-managed-identities"></a>Enumerar identidades administradas asignadas por el usuario

Para visualizar o leer una identidad administrada asignada por el usuario, es necesario que se asignen a su cuenta los roles [Operador de identidades administradas ](../../role-based-access-control/built-in-roles.md#managed-identity-operator) o [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para enumerar las identidades administradas asignadas por el usuario, use el comando [Get-AzUserAssigned]. El parámetro `-ResourceGroupName` especifica el grupo de recursos donde se creó la identidad administrada asignada por el usuario. Reemplace el valor `<RESOURCE GROUP>` por su propio valor.

```azurepowershell-interactive
Get-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP>
```

En la respuesta, las identidades administradas asignadas por el usuario obtienen el valor `"Microsoft.ManagedIdentity/userAssignedIdentities"` para la clave `Type`.

`Type :Microsoft.ManagedIdentity/userAssignedIdentities`

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar una identidad administrada asignada por el usuario

Para eliminar una identidad administrada asignada por el usuario, la cuenta requiere la asignación del rol [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para eliminar una identidad administrada asignada por el usuario, use el comando `Remove-AzUserAssignedIdentity`. El parámetro `-ResourceGroupName` especifica el grupo de recursos donde se creó la identidad asignada por el usuario. El parámetro `-Name` especifica su nombre. Reemplace los valores de parámetro `<RESOURCE GROUP>` y `<USER ASSIGNED IDENTITY NAME>` por sus propios valores.

```azurepowershell-interactive
Remove-AzUserAssignedIdentity -ResourceGroupName <RESOURCE GROUP> -Name <USER ASSIGNED IDENTITY NAME>
```

> [!NOTE]
> Al eliminar una identidad administrada asignada por el usuario, no se eliminará la referencia de ningún recurso al que se haya asignado. Las asignaciones de identidad deben eliminarse por separado.

## <a name="next-steps"></a>Pasos siguientes

Para obtener una lista completa y más detalles sobre las identidades administradas de Azure PowerShell para los comandos de los recursos de Azure, consulte [Az.ManagedServiceIdentity](/powershell/module/az.managedserviceidentity#managed_service_identity).


::: zone-end


::: zone pivot="identity-mi-methods-arm"

En este artículo va a crear una identidad administrada asignada por el usuario mediante Azure Resource Manager.

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con las identidades administradas de los recursos de Azure, consulte la [sección de introducción](overview.md). *No olvide revisar la [diferencia entre una identidad administrada asignada por el sistema y una identidad administrada asignada por el usuario](overview.md#managed-identity-types)* .
- Si aún no tiene una, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de continuar.

No se puede visualizar ni eliminar una identidad administrada asignada por el usuario mediante una plantilla de Resource Manager. Consulte los artículos siguientes para crear y enumerar una identidad administrada asignada por el usuario:

- [Enumeración de identidades administradas asignadas por el usuario](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Eliminación de identidades administradas asignadas por el usuario](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)

## <a name="template-creation-and-editing"></a>Creación y edición de una plantilla

Al igual que con Azure Portal y los scripts, las plantillas de Azure Resource Manager proporcionan la capacidad de implementar recursos nuevos o modificados que estén definidos por un grupo de recursos de Azure. Hay disponibles varias opciones para editar e implementar plantillas, tanto locales como basadas en el portal. Puede:

- Usar una [plantilla personalizada de Azure Marketplace](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template) para crear otra desde cero o basarla en una plantilla común ya existente o [de inicio rápido](https://azure.microsoft.com/resources/templates/).
- Obtenerla a partir de un grupo de recursos existente, exportando una plantilla de [la implementación original](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates) o del [estado actual de la implementación](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates).
- Usar un [editor de JSON (por ejemplo, VS Code)](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md) localmente y, a continuación, cargarla e implementarla con PowerShell o la CLI de Azure.
- Usar el [proyecto del grupo de recursos de Azure](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) de Visual Studio para crear e implementar una plantilla. 

## <a name="create-a-user-assigned-managed-identity"></a>Crear una identidad administrada asignada por el usuario 

Para crear una identidad administrada asignada por el usuario, la cuenta requiere la asignación del rol [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

Para crear una identidad administrada asignada por el usuario, use la siguiente plantilla. Reemplace el valor de `<USER ASSIGNED IDENTITY NAME>` por sus propios valores.

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo asignar una identidad administrada asignada por el usuario a una VM de Azure mediante una plantilla de Resource Manager, consulte [Configuración de identidades administradas para recursos de Azure en una VM de Azure mediante una plantilla](qs-configure-template-windows-vm.md).




::: zone-end


::: zone pivot="identity-mi-methods-rest"

En este artículo obtendrá información sobre cómo usar REST para crear y eliminar una identidad administrada asignada por el usuario, y obtener una lista de sus identidades.


## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con las identidades administradas de los recursos de Azure, consulte la [sección de introducción](overview.md). *No olvide revisar la [diferencia entre una identidad administrada asignada por el sistema y una identidad administrada asignada por el usuario](overview.md#managed-identity-types)* .
- Si aún no tiene una, [regístrese para obtener una cuenta gratuita de Azure](https://azure.microsoft.com/free/) antes de continuar.
- Puede ejecutar todos los comandos de este artículo, ya sea en la nube o localmente:
    - Para ejecutarlos en la nube, use [Azure Cloud Shell](../../cloud-shell/overview.md).
    - Para ejecutar localmente, instale [curl](https://curl.haxx.se/download.html) y la [CLI de Azure](/cli/azure/install-azure-cli).


En este artículo obtendrá información sobre cómo usar CURL para realizar llamadas API, con el fin de crear y eliminar una identidad administrada asignada por el usuario, y obtener una lista de sus identidades.

## <a name="obtain-a-bearer-access-token"></a>Obtención de un token de acceso de portador

1. Si se ejecuta localmente, inicie sesión en Azure a través de la CLI.

    ```
    az login
    ```

1. Para obtener un token de acceso, use [az account get-access-token](/cli/azure/account#az_account_get_access_token).

    ```azurecli-interactive
    az account get-access-token
    ```

## <a name="create-a-user-assigned-managed-identity"></a>Crear una identidad administrada asignada por el usuario 

Para crear una identidad administrada asignada por el usuario, la cuenta requiere la asignación del rol [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X PUT -d '{"loc
ation": "<LOCATION>"}' -H "Content-Type: application/json" -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
PUT https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```

**Encabezados de solicitud**

|Encabezado de solicitud  |Descripción  |
|---------|---------|
|*Content-Type*     | Necesario. Establézcalo en `application/json`.        |
|*Autorización*     | Necesario. Establézcalo en un token de acceso `Bearer` válido.        |

**Cuerpo de la solicitud**

|Nombre  |Descripción  |
|---------|---------|
|Location     | Necesario. Ubicación del recurso        |

## <a name="list-user-assigned-managed-identities"></a>Enumerar identidades administradas asignadas por el usuario

Para visualizar o leer una identidad administrada asignada por el usuario, es necesario que se asignen a su cuenta los roles [Operador de identidades administradas ](../../role-based-access-control/built-in-roles.md#managed-identity-operator) o [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview' -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
GET https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities?api-version=2015-08-31-preview HTTP/1.1
```

|Encabezado de solicitud  |Descripción  |
|---------|---------|
|*Content-Type*     | Necesario. Establézcalo en `application/json`.        |
|*Autorización*     | Necesario. Establézcalo en un token de acceso `Bearer` válido.        |

## <a name="delete-a-user-assigned-managed-identity"></a>Eliminar una identidad administrada asignada por el usuario

Para eliminar una identidad administrada asignada por el usuario, la cuenta requiere la asignación del rol [Colaborador de identidades administradas](../../role-based-access-control/built-in-roles.md#managed-identity-contributor).

> [!NOTE]
> Al eliminar una identidad administrada asignada por el usuario, no se eliminará la referencia de ningún recurso al que se haya asignado. Para eliminar una identidad administrada asignada por el usuario de una máquina virtual mediante CURL, consulte [Eliminación de una identidad asignada por el usuario de una máquina virtual de Azure](qs-configure-rest-vm.md#remove-a-user-assigned-managed-identity-from-an-azure-vm).

```bash
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroup
s/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview' -X DELETE -H "Authorization: Bearer <ACCESS TOKEN>"
```

```HTTP
DELETE https://management.azure.com/subscriptions/80c696ff-5efa-4909-a64d-f1b616f423ca/resourceGroups/TestRG/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER ASSIGNED IDENTITY NAME>?api-version=2015-08-31-preview HTTP/1.1
```
|Encabezado de solicitud  |Descripción  |
|---------|---------|
|*Content-Type*     | Necesario. Establézcalo en `application/json`.        |
|*Autorización*     | Necesario. Establézcalo en un token de acceso `Bearer` válido.        |

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo usar CURL para asignar una identidad administrada asignada por el usuario a una máquina virtual de Azure o un conjunto de escalado de máquinas virtuales, consulte los siguientes artículos:
- [Configuración de identidades administradas de recursos de Azure en una VM de Azure mediante llamadas a la API de REST](qs-configure-rest-vm.md#user-assigned-managed-identity) 
- [Configuración de identidades administradas de recursos de Azure en un conjunto de escalado de máquinas virtuales mediante llamadas a la API REST](qs-configure-rest-vmss.md#user-assigned-managed-identity)

::: zone-end


