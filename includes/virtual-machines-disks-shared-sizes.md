---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/03/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 6d7d9389e00b3b0f23d590563a242ae0c12e2463
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121740096"
---
De momento, solo los discos Ultra Disks y SSD Premium pueden habilitar discos compartidos. Los distintos tamaños de disco pueden tener un límite de `maxShares` diferente, que no se puede superar al establecer el valor de `maxShares`. En el caso de los discos SSD Premium, los tamaños de disco que admiten el uso compartido de discos son P15 y superiores.

Para cada disco, puede definir un valor de `maxShares` que represente el número máximo de nodos que puede compartir el disco simultáneamente. Por ejemplo, si planea configurar un clúster de conmutación por error de dos nodos, debe establecer `maxShares=2`. El valor máximo es un límite superior. Los nodos pueden unirse al clúster, o salir de él (montaje o desmontaje del disco) siempre que el número de nodos sea inferior al valor de `maxShares` especificado.

> [!NOTE]
> El valor de `maxShares` solo se puede establecer o editar cuando el disco se desasocia de todos los nodos.

### <a name="premium-ssd-ranges"></a>Rangos de discos SSD Premium

En la tabla siguiente se muestran los valores máximos permitidos para `maxShares` por tamaños de discos SSD Premium:

|Tamaños de disco  |Límite de maxShares  |
|---------|---------|
|P1,P2,P3,P4,P6,P10,P15,P20     |3         |
|P30, P40, P50     |5         |
|P60, P70, P80     |10         |

Los límites de IOPS y ancho de banda de un disco no se ven afectados por el valor de `maxShares`. Por ejemplo, el número máximo de IOPS de un disco P15 es 1100 si maxShares = 1 o maxShares > 1.

### <a name="standard-ssd-ranges"></a>Intervalos de SSD estándar

En la tabla siguiente se muestran los valores máximos permitidos para `maxShares` por tamaños de discos estándar:

|Tamaños de disco  |Límite de maxShares  |
|---------|---------|
|E1,E2,E3,E4,E6,E10,E15,E20     |3         |
|E30, E40, E50     |5         |
|E60, E70, E80     |10         |

Los límites de IOPS y ancho de banda de un disco no se ven afectados por el valor de `maxShares`. Por ejemplo, el número máximo de IOPS de un disco E15 es 500 si maxShares = 1 o maxShares > 1.

### <a name="ultra-disk-ranges"></a>Rangos de discos Ultra Disks

El valor mínimo de `maxShares` es de 1, mientras que el valor máximo de `maxShares` es de 5. No hay restricciones de tamaño en los discos Ultra Disks, cualquier disco de tamaño ultra puede usar cualquier valor para `maxShares`, hasta el valor máximo incluido.