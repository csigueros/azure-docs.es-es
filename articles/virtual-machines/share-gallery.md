---
title: Uso compartido de una galería mediante RBAC
description: Aprenda a compartir una galería mediante el control de acceso basado en rol (RBAC).
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/31/2021
ms.author: cynthn
ms.custom: template-how-to
ms.openlocfilehash: 01c98ba8d619573c241e5a8fdfa2f774b4aed22d
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123452347"
---
# <a name="use-rbac-to-share-image-gallery-resources"></a>Uso de RBAC para compartir recursos de la galería de imágenes

Dado que Shared Image Gallery, la definición de imagen y la versión de imagen son recursos, pueden compartirse con los controles RBAC integrados nativos de Azure. Con Azure RBAC puede compartir estos recursos con otros usuarios, entidades de servicio y grupos. Incluso se puede compartir el acceso a personas ajenas al inquilino en el que se crearon. Cuando el usuario tiene acceso a la versión de las imágenes compartidas, puede implementar una máquina virtual o un conjunto de escalado de máquinas virtuales.  

Se recomienda el uso compartido en el nivel de la galería para una mejor experiencia. No se recomienda compartir las versiones individuales de la imagen. Para obtener más información sobre Azure RBAC, consulte [Asignación de roles de Azure](../role-based-access-control/role-assignments-portal.md).

Si el usuario está fuera de su organización, recibirá una invitación por correo electrónico para unirse a ella. Debe aceptarla para ver la galería y todas las definiciones y versiones de imágenes de su lista de recursos.

## <a name="share-a-gallery"></a>Uso compartido de una galería

### <a name="portal"></a>[Portal](#tab/portal)

Si el usuario está fuera de su organización, recibirá una invitación por correo electrónico para unirse a ella. Debe aceptarla para ver la galería y todas las definiciones y versiones de imágenes de su lista de recursos.

1. En la página de la galería de imágenes, en el menú de la izquierda, seleccione **Control de acceso (IAM)** . 
1. En **Agregar una asignación de roles**, seleccione **Agregar**. Se abre el panel **Agregar una asignación de roles**. 
1. En **Rol**, seleccione **Lector**.
1. En **Asignar acceso a**, deje el valor predeterminado de **Usuario, grupo o entidad de servicio de Azure AD**.
1. En **Seleccionar**, escriba la dirección de correo electrónico de la persona a la que quiere invitar.
1. Si el usuario está fuera de su organización, verá el mensaje **This user will be sent an email that enables them to collaborate with Microsoft** (A este usuario se le enviará un correo electrónico que le permite colaborar con Microsoft). Seleccione el usuario con la dirección de correo electrónico y, luego, haga clic en **Guardar**.


### <a name="cli"></a>[CLI](#tab/cli)

Para obtener el identificador de objeto de la galería, use [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Use el identificador de objeto como ámbito, junto con una dirección de correo electrónico y [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) para dar a un usuario acceso a la galería de imágenes compartidas. Reemplace `<email-address>` y `<gallery iD>` por su propia información.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Para más información sobre cómo compartir recursos con RBAC, consulte [Administración del acceso mediante RBAC y la CLI de Azure](../role-based-access-control/role-assignments-cli.md).

### <a name="powershell"></a>[PowerShell](#tab/powershell)

Use una dirección de correo electrónico y el cmdlet [Get-AzADUser](/powershell/module/az.resources/get-azaduser) para obtener el identificador de objeto del usuario y, después, utilice [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) para concederle acceso a la galería. Reemplace el correo electrónico alinne_montes@contoso.com de este ejemplo por su propia información.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName

```

---


## <a name="next-steps"></a>Pasos siguientes

Cree una [definición de imagen y una versión de imagen](image-version.md).

[Azure Image Builder (versión preliminar)](./image-builder-overview.md) puede ayudar a automatizar la creación de versiones de la imagen, incluso se puede usar para actualizar y [crear una nueva versión de la imagen a partir de una versión de imagen existente](./windows/image-builder-gallery-update-image-version.md). 

Puede crear también recursos de galería de imágenes compartidas con plantillas. Hay varias plantillas de Inicio rápido de Azure disponibles: 

- [Creación de una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-create/)
- [Creación de una definición de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-image-definition-create/)
- [Creación de una versión de imagen en una galería de imágenes compartidas](https://azure.microsoft.com/resources/templates/sig-image-version-create/)
