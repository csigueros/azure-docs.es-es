---
title: 'Asignación de acceso de una identidad administrada a un recurso mediante Azure Portal: Azure AD'
description: Instrucciones paso a paso para asignar a una identidad administrada de un recurso acceso a otro recurso, mediante Azure Portal.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.custom: subject-rbac-steps
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 06/24/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: eeff666af9d680baa037415cedb19077de9c061f
ms.sourcegitcommit: cd8e78a9e64736e1a03fb1861d19b51c540444ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112966363"
---
# <a name="assign-a-managed-identity-access-to-a-resource-by-using-the-azure-portal"></a>Asignación de acceso de una identidad administrada a un recurso mediante Azure Portal

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Una vez que haya configurado un recurso de Azure con una identidad administrada, puede dar acceso de identidad administrada a otro recurso, al igual que cualquier entidad de seguridad. En este artículo se muestra cómo otorgar acceso a la identidad administrada de un conjunto de escalado de máquinas virtuales o una máquina virtual de Azure para una cuenta de Azure Storage mediante Azure Portal.

## <a name="prerequisites"></a>Requisitos previos

- Si no está familiarizado con las identidades administradas de los recursos de Azure, consulte la [sección de introducción](overview.md). **No olvide revisar la [diferencia entre una identidad administrada asignada por el sistema y una identidad administrada asignada por el usuario](overview.md#managed-identity-types)** .
- Si aún no tiene una cuenta de Azure, [regístrese para una cuenta gratuita](https://azure.microsoft.com/free/) antes de continuar.

## <a name="use-azure-rbac-to-assign-a-managed-identity-access-to-another-resource"></a>Uso de Azure RBAC para asignar el acceso de una identidad administrada a otro recurso

Después de habilitar la identidad administrada en un recurso de Azure, como una [máquina virtual de Azure](qs-configure-portal-windows-vm.md) o un [conjunto de escalado de máquinas virtuales de Azure](qs-configure-portal-windows-vmss.md):

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción de Azure en la que ha configurado la identidad administrada.

2. Vaya al recurso para el que desea modificar el control de acceso. En este ejemplo, vamos a dar a una máquina virtual de Azure acceso a una cuenta de almacenamiento, de modo que navegamos a la cuenta de almacenamiento.

1. Seleccione **Access Control (IAM)** .

1. Seleccione **Agregar** > **Agregar asignación de roles** para abrir la página Agregar asignación de roles.

1. Seleccione el rol y la identidad administrada. Para asignar roles, consulte [Asignación de roles de Azure mediante Azure Portal](../../role-based-access-control/role-assignments-portal.md).

    ![Página Agregar asignación de roles en Azure Portal.](../../../includes/role-based-access-control/media/add-role-assignment-page.png)
     
## <a name="next-steps"></a>Pasos siguientes

- [Identidades administradas de recursos de Azure](overview.md)
- Para habilitar la identidad administrada de una máquina virtual de Azure, consulte [Configuración de identidades administradas de recursos de Azure en una VM de Azure mediante Azure Portal](qs-configure-portal-windows-vm.md).
- Para habilitar la identidad administrada en un conjunto de escalado de máquinas virtuales de Azure, consulte [Configuración de identidades administradas de recursos de Azure en un conjunto de escalado de máquinas virtuales mediante Azure Portal](qs-configure-portal-windows-vmss.md).


