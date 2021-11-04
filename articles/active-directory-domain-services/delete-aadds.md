---
title: Eliminar Azure Active Directory Domain Services | Microsoft Docs
description: Obtenga información para deshabilitar o eliminar Azure Active Directory Domain Services mediante Azure Portal
services: active-directory-ds
author: justinha
manager: daveba
ms.assetid: 89e407e1-e1e0-49d1-8b89-de11484eee46
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: how-to
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: fa0cc8c0ca548dbf5021d7531198802729afaf2f
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131427824"
---
# <a name="delete-an-azure-active-directory-domain-services-managed-domain-using-the-azure-portal"></a>Obtenga información para deshabilitar o eliminar Azure Active Directory Domain Services mediante Azure Portal

Si ya no necesita un dominio administrado, puede eliminar Azure Active Directory Domain Services (Azure AD DS). No hay ninguna opción para desactivar o deshabilitar temporalmente un dominio administrado de Azure AD DS. Al eliminar el dominio administrado no se elimina ni afecta negativamente al inquilino de Azure AD.

En este artículo se muestra cómo usar el Azure Portal para eliminar un dominio administrado.

> [!WARNING]
> **La eliminación es permanente y no se puede deshacer.**
> 
> Cuando se elimina un dominio administrado, se realizan los siguientes pasos:
>   * Los controladores del dominio administrado se desabastecerán y se quitarán de la red virtual.
>   * Los datos del dominio administrado se eliminarán permanentemente. Estos datos incluyen unidades organizativas personalizadas, GPO, registros DNS personalizados, entidades de servicio, GMSA y elementos similares que haya creado.
>   * Las máquinas unidas al dominio administrado perderán la relación de confianza con el dominio y deberán separarse de este.
>       * No podrá iniciar sesión en estas máquinas con credenciales corporativas de AD. En su lugar, use las credenciales de administrador local para la máquina.

## <a name="delete-the-managed-domain"></a>Eliminar el dominio administrado

Para eliminar un dominio administrado, realice los siguientes pasos:

1. En Azure Portal, busque y seleccione **Azure AD Domain Services**.
1. Seleccione el nombre del dominio administrado, como *aaddscontoso.com*.
1. En la página **Información general**, seleccione **Eliminar**. Para confirmar la eliminación, escriba de nuevo el nombre de dominio del dominio administrado y seleccione **Eliminar**.

La eliminación del dominio administrado puede tardar 15-20 minutos o más.

## <a name="next-steps"></a>Pasos siguientes

Puede de [compartir comentarios][feedback] de las características que le gustaría ver en Azure AD DS.

Si desea empezar a usar Azure AD DS de nuevo, consulte [Crear y configurar un dominio administrado de Azure Active Directory Domain Services][create-instance].

<!-- INTERNAL LINKS -->
[feedback]: https://feedback.azure.com/d365community/forum/22920db1-ad25-ec11-b6e6-000d3a4f0789?c=5d63b5b7-ae25-ec11-b6e6-000d3a4f0789
[create-instance]: tutorial-create-instance.md
