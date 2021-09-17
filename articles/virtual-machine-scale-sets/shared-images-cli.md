---
title: Uso de imágenes de máquina virtual compartidas para crear un conjunto de escalado en la CLI de Azure
description: Obtenga información sobre cómo usar la CLI de Azure para crear imágenes de máquinas virtuales compartidas que se utilizarán para implementar conjuntos de escalado de máquina virtual en Azure.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: shared-image-gallery
ms.topic: conceptual
ms.date: 05/06/2019
ms.author: cynthn
ms.reviewer: mimckitt
ms.openlocfilehash: b3131d811b35cdbe28018a6939e514df7458cc95
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122693198"
---
# <a name="create-and-use-shared-images-for-virtual-machine-scale-sets-with-the-azure-cli-20"></a>Creación y uso de imágenes compartidas personalizada para conjuntos de escalado de máquinas virtuales con la CLI de Azure 2.0

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado uniformes

Al crear el conjunto de escalado, se especifica la imagen que se usará cuando se implementen las instancias de máquina virtual. Una [galería de imágenes compartidas](../virtual-machines/shared-image-galleries.md) simplifica el uso compartido de imágenes personalizadas en toda una organización. Las imágenes personalizadas son como las imágenes de Marketplace, pero las puede crear usted mismo. Las imágenes personalizadas pueden usarse para configuraciones de arranque como la carga previa de aplicaciones, configuraciones de aplicaciones y otras configuraciones del sistema operativo. 

Shared Image Gallery le permite compartir sus imágenes con otras personas. Elija las imágenes que desea compartir, qué regiones desea que estén disponibles en ellas y con quién desea compartirlas. 


[!INCLUDE [virtual-machines-common-shared-images-cli](../../includes/virtual-machines-common-shared-images-cli.md)]


## <a name="next-steps"></a>Pasos siguientes

Cree una versión de la imagen a partir de una [máquina virtual](../virtual-machines/image-version-vm-cli.md) o una [imagen administrada](../virtual-machines/image-version-managed-image-cli.md).

Para más información sobre las galerías de imágenes compartidas, consulte la [Introducción](../virtual-machines/shared-image-galleries.md). Si encuentra problemas, consulte [Solución de problemas de galerías de imágenes compartidas](../virtual-machines/troubleshooting-shared-images.md).
