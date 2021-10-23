---
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: include
ms.date: 05/21/2019
ms.author: alkohli
ms.openlocfilehash: 2978fff57cfc7ae3dd4629450f573758978058d5
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130077851"
---
Estos son los tamaños de los objetos de Azure que se pueden escribir. Asegúrese de que todos los archivos que se cargan se ajustan a estos límites.

| Tipo de objeto de Azure | Límite predeterminado                                             |
|-------------------|-----------------------------------------------------------|
| Blob en bloques        | ~4,75 TiB                                                 |
| Blob en páginas         | 8 TiB <br> Todos los archivos cargados en formato de blob en páginas deben tener 512 bytes alineados (un múltiplo entero), de lo contrario, se produce un error en la carga. <br> VHD y VHDX tienen 512 bytes alineados. |
| Azure Files        | 1 TiB                                                      |
| Discos administrados     | 4 TiB <br> Para más información sobre el tamaño y los límites, consulte: <li>[Objetivos de escalabilidad de SSD estándar](../articles/virtual-machines/disks-types.md#standard-ssds)</li><li>[Objetivos de escalabilidad de SSD Premium](../articles/virtual-machines/disks-types.md#standard-hdds)</li><li>[Objetivos de escalabilidad de unidades de disco duro estándar](../articles/virtual-machines/disks-types.md#premium-ssds)</li><li>[Descripción de precios y facturación de discos administrados](../articles/virtual-machines/disks-types.md#billing)</li>  
