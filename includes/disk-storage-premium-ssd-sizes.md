---
title: Archivo de inclusión
description: archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/24/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 759ce6ab44e7b50b88abc487cf2ab90041a06259
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112975325"
---
| Tamaños de SSD Premium | P1 | P2 | P3 | P4 | P6 | P10 | P15 | P20 | P30 | P40 | P50 | P60 | P70 | P80 |
|-------------------|----|----|----|----|----|-----|-----|-----|-----|-----|-----|------|------|------|
| Tamaño de disco en GiB | 4 | 8 | 16 | 32 | 64 | 128 | 256 | 512 | 1024 | 2 048 | 4 096 | 8192 | 16 384 | 32 767 |
| IOPS aprovisionadas por disco | 120 | 120 | 120 | 120 | 240 | 500 | 1 100 | 2,300 | 5\.000 | 7 500 | 7 500 | 16 000 | 18 000 | 20.000 |
| Capacidad de proceso aprovisionada por disco | 25 MB/s | 25 MB/s | 25 MB/s | 25 MB/s | 50 MB/s | 100 MB/s | 125 MB/s | 150 MB/s | 200 MB/s | 250 MB/s | 250 MB/s| 500 MB/s | 750 MB/s | 900 MB/s |
| Máximo de IOPS de ráfaga por disco | 3500 | 3500 | 3500 | 3500 | 3500 | 3500 | 3500 | 3500 | 30 000* | 30 000* | 30 000* | 30 000* | 30 000* | 30 000* |
| Capacidad de proceso máximo de ráfaga por disco | 170 MB/s | 170 MB/s | 170 MB/s | 170 MB/s | 170 MB/s | 170 MB/s | 170 MB/s | 170 MB/s | 1000 MB/s* | 1000 MB/s* | 1000 MB/s* | 1000 MB/s* | 1000 MB/s* | 1000 MB/s* |
| Duración máxima de ráfaga | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | 30 min  | Sin límite* | Sin límite* | Sin límite* | Sin límite* | Sin límite* | Sin límite* |
| Apto para reserva | No  | No  | No  | No  | No  | No  | No  | No  | Sí, hasta un año | Sí, hasta un año | Sí, hasta un año | Sí, hasta un año | Sí, hasta un año | Sí, hasta un año |

\*Solo se aplica a los discos con la expansión a petición habilitada.