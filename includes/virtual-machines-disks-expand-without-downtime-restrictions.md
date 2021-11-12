---
title: archivo de inclusión
description: Archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 11/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 5813ec99666e7e758dd1d58a639211d3d0ca0c94
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131448960"
---
- Actualmente, solo está disponible en la región Centro-oeste de EE. UU.
- Solo se admite para discos de datos.
- Los discos con una capacidad inferior a 4 TiB no se pueden ampliar a 4 TiB o más sin tiempo de inactividad.
    - Si aumenta el tamaño de un disco a 4 TiB o más, se puede ampliar sin tiempo de inactividad.
- Instale y use una de las opciones siguientes:
    - La [CLI de Azure más reciente](/cli/azure/install-azure-cli)
    - El [módulo de Azure PowerShell más reciente](/powershell/azure/install-az-ps)
    - Azure Portal si se accede a través de [https://aka.ms/iaasexp/DiskLiveResize](https://aka.ms/iaasexp/DiskLiveResize)
    - O bien una plantilla de Azure Resource Manager con una versión de API que sea 2021-04-01 o posterior.