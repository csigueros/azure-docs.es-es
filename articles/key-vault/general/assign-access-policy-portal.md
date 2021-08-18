---
title: Asignación de una directiva de acceso de Azure Key Vault (Azure Portal)
description: Cómo se usa Azure Portal para asignar una directiva de acceso de Key Vault a una identidad de aplicación o una entidad de seguridad.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 6258de3e6f81ef25c4f515c956662be13eb5f1e2
ms.sourcegitcommit: 8942cdce0108372d6fc5819c71f7f3cf2f02dc60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113136497"
---
# <a name="assign-a-key-vault-access-policy-using-the-azure-portal"></a>Asignación de una directiva de acceso de Key Vault mediante Azure Portal

Las directivas de acceso de Key Vault determinan si una entidad de seguridad concreta, es decir, un usuario, una aplicación o un grupo de usuarios, puede realizar distintas operaciones en los [secretos](../secrets/index.yml), las [claves](../keys/index.yml) y los [certificados](../certificates/index.yml) de Key Vault. Las directivas de acceso se pueden asignar mediante Azure Portal (este artículo), la [CLI de Azure](assign-access-policy-cli.md)o [Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Para más información sobre cómo crear grupos en Azure Active Directory mediante Azure Portal, consulte [Creación de un grupo básico e incorporación de miembros](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

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


## <a name="next-steps"></a>Pasos siguientes

- [Seguridad de Azure Key Vault](security-features.md)
- [Guía del desarrollador de Azure Key Vault](developers-guide.md)
