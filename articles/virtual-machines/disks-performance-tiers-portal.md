---
title: Cambio del rendimiento de los discos administrados de Azure mediante Azure Portal
description: Aprenda a cambiar los niveles de rendimiento de los discos administrados nuevos y existentes desde Azure Portal.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/02/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 1320ec212a94245e42c63f583d37b33eaa76224e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433303"
---
# <a name="change-your-performance-tier-using-the-azure-portal"></a>Cambio del nivel de rendimiento mediante Azure Portal

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

[!INCLUDE [virtual-machines-disks-performance-tiers-intro](../../includes/virtual-machines-disks-performance-tiers-intro.md)]

## <a name="restrictions"></a>Restricciones

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="getting-started"></a>Introducción

### <a name="new-disks"></a>Discos nuevos

En los pasos siguientes se muestra cómo cambiar el nivel de rendimiento de un disco al crearlo:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. Vaya a la máquina virtual para la que desea crear un disco.
1. Al seleccionar el disco nuevo, en primer lugar elija el tamaño que necesita.
1. Una vez que haya seleccionado el tamaño, seleccione un nivel de rendimiento diferente para cambiar su rendimiento.
1. Seleccione **Aceptar** para crear el disco.

:::image type="content" source="media/disks-performance-tiers-portal/new-disk-change-performance-tier.png" alt-text="Captura de pantalla de la hoja de creación de discos, en la que tanto un disco como la lista desplegable de niveles de rendimiento están resaltados." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::


### <a name="change-the-performance-tier-of-an-existing-disk-without-downtime"></a>Cambio del nivel de rendimiento de un disco existente sin que haya tiempo de inactividad

También puede cambiar el nivel de rendimiento sin tiempo de inactividad, por lo que no es preciso desasignar la máquina virtual ni desasociar el disco para cambiar el nivel.

### <a name="prerequisites"></a>Requisitos previos

Debe habilitar la característica en su suscripción para poder cambiar el nivel de rendimiento de un disco sin tiempo de inactividad. Siga los pasos que se indican a continuación para habilitar la característica para su suscripción:

1.  Ejecute el siguiente comando para registrar la característica para su suscripción

    ```azurecli
    az feature register --namespace Microsoft.Compute --name LiveTierChange
    ```
 
1.  Compruebe que el estado de registro es **Registrado** (puede tardar unos minutos) mediante el comando siguiente antes de probar la característica.

    ```azurecli
    az feature show --namespace Microsoft.Compute --name LiveTierChange
    ```

### <a name="change-performance-tier"></a>Cambio del nivel de rendimiento

Ahora que se ha registrado la característica, puede cambiar los niveles de rendimiento del disco aplicables sin tiempo de inactividad.

1. Inicie sesión en Azure Portal desde el vínculo siguiente: [https://aka.ms/diskPerfTiersPreview](https://aka.ms/diskPerfTiersPreview).
1. Vaya a la máquina virtual que contiene el disco que desea cambiar.
1. Seleccione el disco.
1. Seleccione **Tamaño y rendimiento**.
1. En la lista desplegable **Nivel de rendimiento**, seleccione un nivel distinto del nivel de rendimiento actual del disco.
1. Seleccione **Cambiar tamaño**.

:::image type="content" source="media/disks-performance-tiers-portal/change-tier-existing-disk.png" alt-text="Captura de pantalla de la hoja de tamaño y rendimiento, en la que hay un nivel de rendimiento resaltado." lightbox="media/disks-performance-tiers-portal/performance-tier-settings.png":::

## <a name="next-steps"></a>Pasos siguientes

Si necesita cambiar el tamaño de un disco para aprovechar las ventajas de los niveles de rendimiento superiores, consulte estos artículos:

- [Expansión de discos duros virtuales en una VM Linux con la CLI de Azure](linux/expand-disks.md)
- [Expansión de un disco administrado asociado a una máquina virtual de Windows](windows/expand-os-disk.md)
