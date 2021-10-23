---
title: Creación de máquinas virtuales en un conjunto de escalado flexible con Azure Portal
description: Información sobre cómo crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible a través de Azure Portal.
author: fitzgeraldsteele
ms.author: fisteele
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: flexible-scale-sets
ms.date: 08/05/2021
ms.reviewer: jushiman
ms.custom: mimckitt, devx-track-azurecli, vmss-flex
ms.openlocfilehash: 9804b8cd773eec6df8a8b7a5b06e61ee110987b3
ms.sourcegitcommit: 01dcf169b71589228d615e3cb49ae284e3e058cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130163562"
---
# <a name="create-virtual-machines-in-a-flexible-scale-set-using-azure-portal"></a>Creación de máquinas virtuales en un conjunto de escalado flexible con Azure Portal

**Se aplica a:** :heavy_check_mark: Conjuntos de escalado flexibles

En este artículo se describe el uso de Azure Portal para crear un conjunto de escalado de máquinas virtuales en modo de orquestación flexible. Para obtener más información sobre los conjuntos de escalado flexibles, vea [Modo de orquestación flexible para conjuntos de escalado de máquinas virtuales](flexible-virtual-machine-scale-sets.md). 


> [!CAUTION]
> El modo de orquestación se define al crear el conjunto de escalado y no se puede cambiar ni actualizar más adelante.


## <a name="get-started-with-flexible-orchestration-mode"></a>Introducción al modo de orquestación flexible

### <a name="create-a-virtual-machine-scale-set-in-flexible-orchestration-mode-through-the-azure-portal"></a>Cree un conjunto de escalado de máquinas virtuales en modo de orquestación Flexible mediante Azure Portal.

1. Inicie sesión en Azure Portal en https://portal.azure.com.
1. En la barra de búsqueda, busque y seleccione **Conjuntos de escalado de máquinas virtuales**.
1. Seleccione **Crear** en la página **Conjuntos de escalado de máquinas virtuales**.
1. En la página **Crear un conjunto de escalado de máquinas virtuales**, vea la sección **Orquestación**.
1. En **Orchestration mode** (Modo de orquestación), seleccione la opción **Flexible**.
1. Indique un valor en **Número de dominios de error**.
1. Termine de crear el conjunto de escalado. Para más información sobre cómo crear un conjunto de escalado, consulte [Creación de un conjunto de escalado en Azure Portal](../virtual-machine-scale-sets/quick-create-portal.md#create-virtual-machine-scale-set).


### <a name="optional-add-a-virtual-machine-to-the-scale-set-in-flexible-orchestration-mode"></a>(Opcional) Agregue una máquina virtual al conjunto de escalado en el modo de orquestación flexible.

1. En la barra de búsqueda, busque y seleccione **Máquinas virtuales**.
1. En la página **Máquinas virtuales**, seleccione **Agregar**.
1. En la pestaña **Aspectos básicos**, vea la sección **Detalles de instancia**.
1. Agregue la máquina virtual al conjunto de escalado en el modo de orquestación flexible, para lo que debe seleccionar el conjunto de escalado en las **opciones de disponibilidad**. Puede agregar la máquina virtual a un conjunto de escalado en la misma región, zona y grupo de recursos.
1. Vaya a la pestaña **Redes** y defina explícitamente la conectividad saliente.

    > [!IMPORTANT]
    > La conectividad saliente definida explícitamente es necesaria en los conjuntos de escalado de máquinas virtuales con orquestación flexible. Consulte la [Conectividad de red saliente explícita](flexible-virtual-machine-scale-sets-migration-resources.md#explicit-network-outbound-connectivity-required) para obtener más información.

1. Termine de crear la máquina virtual.


## <a name="next-steps"></a>Pasos siguientes
> [!div class="nextstepaction"]
> [Más información sobre cómo crear un conjunto de escalado flexible con la CLI de Azure](flexible-virtual-machine-scale-sets-cli.md)