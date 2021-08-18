---
title: Niveles de rendimiento de discos administrados de Azure
description: Información sobre los niveles de rendimiento de discos administrados.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 06/29/2021
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: c45bc6fa0df5883f060458c3ce92680752e5875a
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113358533"
---
# <a name="performance-tiers-for-managed-disks"></a>Niveles de rendimiento de discos administrados

El rendimiento de un disco administrado de Azure se establece al crearlo, en forma de su nivel de rendimiento. El nivel de rendimiento determina la IOPS y el rendimiento del disco administrado. Al establecer el tamaño aprovisionado de un disco, se selecciona automáticamente un nivel de rendimiento. El nivel de rendimiento se puede cambiar tanto en la implementación como posteriormente sin tener que cambiar el tamaño del disco.

El cambio del nivel de rendimiento le permite prepararse para una mayor demanda y, por consiguiente cubrirla, sin tener que usar la funcionalidad de ráfaga del disco. Es posible que sea más rentable cambiar el nivel de rendimiento que usar dicha funcionalidad, dependerá del tiempo que sea preciso mantener el rendimiento adicional. Esto es perfecto para aquellos eventos que requieren temporalmente un nivel de rendimiento más alto de forma constante, como las compras navideñas, las pruebas de rendimiento o la ejecución de un entorno de aprendizaje. Para administrar estos eventos, puede usar un nivel de rendimiento superior mientras sea necesario. Después, puede volver al nivel original cuando ya no sea necesario el rendimiento adicional.

## <a name="how-it-works"></a>Cómo funciona

La primera vez que implementa o aprovisiona un disco, el nivel de rendimiento de línea de base para ese disco se establece en función del tamaño del disco aprovisionado. Puede usar un nivel de rendimiento superior al de la línea base original para satisfacer una mayor demanda. Cuando ya no necesite ese nivel de rendimiento, podrá volver al nivel de rendimiento de línea de base inicial.

La facturación cambia a medida que cambia el nivel de rendimiento. Por ejemplo, si aprovisiona un disco P10 (128 GiB), el nivel de rendimiento de línea de base se establece como P10 (500 IOPS y 100 Mbps). Se le facturará según la tarifa de P10. Puede actualizar el nivel para cumplir con el rendimiento de P50 (7500 IOPS y 250 Mbps) sin aumentar el tamaño del disco. En el momento de la actualización, se le facturará según la tarifa P50. Cuando ya no necesite el alto rendimiento, puede volver al nivel P10. El disco se facturará una vez más según la tarifa P10.

| Tamaño del disco | Rendimiento de línea de base | Se puede actualizar a |
|----------------|-----|-------------------------------------|
| 4 GiB | P1 | P2, P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 8 GiB | P2 | P3, P4, P6, P10, P15, P20, P30, P40, P50 |
| 16 GiB | P3 | P4, P6, P10, P15, P20, P30, P40, P50 | 
| 32 GiB | P4 | P6, P10, P15, P20, P30, P40, P50 |
| 64 GiB | P6 | P10, P15, P20, P30, P40, P50 |
| 128 GB | P10 | P15, P20, P30, P40, P50 |
| 256 GiB | P15 | P20, P30, P40, P50 |
| 512 GB | P20 | P30, P40, P50 |
| 1 TiB | P30 | P40, P50 |
| 2 TiB | P40 | P50 |
| 4 TiB | P50 | None |
| 8 TiB | P60 |  P70, P80 |
| 16 TiB | P70 | P80 |
| 32 TiB | P80 | None |

Para obtener información de facturación, consulte los [Precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).

## <a name="restrictions"></a>Restricciones

[!INCLUDE [virtual-machines-disks-performance-tiers-restrictions](../../includes/virtual-machines-disks-performance-tiers-restrictions.md)]

## <a name="next-steps"></a>Pasos siguientes

Para aprender a cambiar el nivel de rendimiento, consulte los artículos en los que se explica cómo se hace desde [Azure Portal](disks-performance-tiers-portal.md) o desde [PowerShell/CLI](disks-performance-tiers.md).

