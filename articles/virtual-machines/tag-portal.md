---
title: Etiquetado de una máquina virtual mediante Azure Portal
description: Más información sobre cómo etiquetar una máquina virtual mediante Azure Portal.
ms.topic: how-to
ms.workload: infrastructure-services
author: cynthn
ms.service: virtual-machines
ms.date: 11/11/2020
ms.author: cynthn
ms.openlocfilehash: 543317b2e1dcc0c55ac970212612f5266e5f84aa
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122695366"
---
# <a name="tagging-a-vm-using-the-portal"></a>Etiquetado de máquinas virtuales mediante el portal

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

En este artículo se describe cómo agregar etiquetas a una máquina virtual mediante el portal. Las etiquetas son pares clave-valor definidos por el usuario que se pueden colocar directamente en un recurso o un grupo de recursos. Actualmente, Azure admite un máximo de 50 etiquetas por recurso y grupo de recursos. Las etiquetas se pueden colocar en un recurso en el momento de su creación, o bien se pueden agregar a un recurso existente.


1. Vaya a su máquina virtual en el portal.
1. En **Essentials**, seleccione **Haga clic aquí para agregar etiquetas**.

    :::image type="content" source="media/tag/azure-portal-tag.png" alt-text="Captura de pantalla de la sección Essentials de la página de la máquina virtual.":::

1. Agregue un valor en **Nombre** y **Valor**, y seleccione **Guardar**.

    :::image type="content" source="media/tag/key-value.png" alt-text="Captura de pantalla de la página para agregar un par clave-valor como etiqueta.":::

### <a name="next-steps"></a>Pasos siguientes

- Para más información sobre el etiquetado de los recursos de Azure, consulte [Información general de Azure Resource Manager](../azure-resource-manager/management/overview.md) y [Uso de etiquetas para organizar los recursos de Azure](../azure-resource-manager/management/tag-resources.md).
- Para ver cómo las etiquetas pueden ayudarle a administrar el uso de recursos de Azure, consulte [Descripción de la factura de Microsoft Azure](../cost-management-billing/understand/review-individual-bill.md).
