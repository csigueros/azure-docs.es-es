---
title: Tablas de configuración de las SKU de Dedicated Host
description: Especificaciones para el empaquetado de máquinas virtuales de las SKU de ADH.
author: brittanyrowe
ms.author: brittanyrowe
ms.reviewer: mimckitt
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: conceptual
ms.date: 10/01/2021
ms.openlocfilehash: 67b17324b550196728da62cb0e5b306d6387fa25
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478262"
---
# <a name="azure-dedicated-host-sku-configuration-tables"></a>Tablas de configuración de las SKU de Azure Dedicated Host
Las SKU de Azure Dedicated Host son la combinación de una familia de máquinas virtuales y una especificación de hardware determinada. Solo puede implementar máquinas virtuales de la serie que especifique la SKU de Dedicated Host. Por ejemplo, en Dsv3 de tipo 3, solo puede aprovisionar máquinas virtuales de la [serie Dsv3](dv3-dsv3-series.md#dsv3-series). 

En este documento, se revisan las especificaciones de hardware y los empaquetados de máquinas virtuales para todas las SKU de Dedicated Host.

## <a name="dasv4"></a>Dasv4
### <a name="dasv4-type1"></a>Dasv4 de tipo 1
Dasv4 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador AMD EPYC™ 7452 de 2,35 GHz. Ofrece 64 núcleos físicos, 96 vCPU y 672 GiB de memoria RAM. Dasv4 de tipo 1 ejecuta máquinas virtuales de la [serie Dasv4](dav4-dasv4-series.md#dasv4-series). Consulte la documentación de este tamaño de máquina virtual para conocer mejor la información específica sobre el rendimiento de las máquinas.

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Dasv4 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM  | N.º de máquinas virtuales |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 672 GiB       | D2as v4  | 32    |
|                |                 |               | D4as v4  | 24    |
|                |                 |               | D8as v4  | 12    |
|                |                 |               | D16as v4 | 6     |
|                |                 |               | D32as v4 | 3     |
|                |                 |               | D48as v4 | 2     |
|                |                 |               | D64as v4 | 1     |
|                |                 |               | D96as v4 | 1     |

También puede combinar varios tamaños de máquina virtual en la SKU Dasv4 de tipo 1. Las siguientes son combinaciones de ejemplo de empaquetados de máquinas virtuales en la SKU Dasv4 de tipo 1:
- 1 D48asv4 + 3 D16asv4
- 1 D32asv4 + 2 D16asv4 + 8 D4asv4
- 20 D4asv4 + 8 D2asv4

## <a name="ddsv4"></a>Ddsv4
### <a name="ddsv4-type1"></a>Ddsv4 de tipo 1
Ddsv4 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8272CL). Ofrece 52 núcleos físicos, 68 vCPU y 504 GiB de memoria RAM. Ddsv4 de tipo 1 ejecuta máquinas virtuales de la [serie Ddsv4](ddv4-ddsv4-series.md#ddsv4-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Ddsv4 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM  | N.º de máquinas virtuales |
|----------------|-----------------|---------------|----------|-------|
| 52             | 68              | 504 GiB       | D2ds v4  | 32    |
|                |                 |               | D4ds v4  | 17    |
|                |                 |               | D8ds v4  | 8     |
|                |                 |               | D16ds v4 | 4     |
|                |                 |               | D32ds v4 | 1     |
|                |                 |               | D48ds v4 | 1     |
|                |                 |               | D64ds v4 | 1     |

También puede combinar varios tamaños de máquina virtual en la SKU Ddsv4 de tipo 1. Las siguientes son combinaciones de ejemplo de empaquetados de máquinas virtuales en la SKU Ddsv4 de tipo 1:
- 1 D48dsv4 + 4 D4dsv4 + 2 D2dsv4
- 1 D32dsv4 + 2 D16dsv4 + 1 D4dsv4
- 10 D4dsv4 + 14 D2dsv4

## <a name="dsv4"></a>Dsv4
### <a name="dsv4-type1"></a>Dsv4 de tipo 1

Dsv4 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8272CL). Ofrece 52 núcleos físicos, 80 vCPU y 504 GiB de memoria RAM. Dsv4 de tipo 1 ejecuta máquinas virtuales de la [serie Dsv4](dv4-dsv4-series.md#dsv4-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Dsv4 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | D2s v4  | 32    |
|                |                 |               | D4s v4  | 20    |
|                |                 |               | D8s v4  | 10    |
|                |                 |               | D16s v4 | 5     |
|                |                 |               | D32s v4 | 2     |
|                |                 |               | D48s v4 | 1     |
|                |                 |               | D64s v4 | 1     |

También puede combinar varios tamaños de máquina virtual en la SKU Dsv4 de tipo 1. Las siguientes son combinaciones de ejemplo de empaquetados de máquinas virtuales en la SKU Dsv4 de tipo 1:
- 1 D64sv4 + 1 D16sv4
- 1 D32sv4 + 2 D16dsv4 + 2 D8sv4
- 10 D4sv4 + 20 D2sv4

## <a name="dsv3"></a>Dsv3
### <a name="dsv3-type1"></a>Dsv3 de tipo 1

Dsv3 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Broadwell (Xeon® E5-2673 v4 de 2,3 GHz). Ofrece 40 núcleos físicos, 64 vCPU y 256 GiB de memoria RAM. Dsv3 de tipo 1 ejecuta máquinas virtuales de la [serie Dsv3](dv3-dsv3-series.md#dsv3-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Dsv3 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 40             | 64              | 256 GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 16    |
|                |                 |               | D8s v3  | 8     |
|                |                 |               | D16s v3 | 4     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

También puede combinar varios tamaños de máquina virtual en la SKU Dsv3 de tipo 1. Las siguientes son combinaciones de ejemplo de empaquetados de máquinas virtuales en la SKU Dsv3 de tipo 1:
- 1 D32sv3 + 1 D16sv3 + 1 D8sv3
- 1 D48sv3 + 3 D4sv3 + 2 D2sv3
- 10 D4sv3 + 12 D2sv3

### <a name="dsv3-type2"></a>Dsv3 de tipo 2

Dsv3 de tipo 2 es una SKU de Dedicated Host que utiliza el procesador Intel® Skylake (Xeon® Platinum 8171M de 2,1 GHz). Ofrece 48 núcleos físicos, 76 vCPU y 504 GiB de memoria RAM. Dsv3 de tipo 2 ejecuta máquinas virtuales de la [serie Dsv3](dv3-dsv3-series.md#dsv3-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Dsv3 de tipo 2.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 48             | 76              | 504 GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 18    |
|                |                 |               | D8s v3  | 9     |
|                |                 |               | D16s v3 | 4     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

También puede combinar varios tamaños de máquina virtual en la SKU Dsv3 de tipo 2. Las siguientes son combinaciones de ejemplo de empaquetados de máquinas virtuales en la SKU Dsv3 de tipo 2:
- 1 D64sv3 + 2 D4vs3 + 2 D2sv3
- 1 D48sv3 + 4 D4sv3 + 6 D2sv3
- 12 D4sv3 + 14 D2sv3

### <a name="dsv3-type3"></a>Dsv3 de tipo 3

Dsv3 de tipo 3 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8272CL). Ofrece 52 núcleos físicos, 80 vCPU y 504 GiB de memoria RAM. Dsv3 de tipo 3 ejecuta máquinas virtuales de la [serie Dsv3](dv3-dsv3-series.md#dsv3-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Dsv3 de tipo 3.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | D2s v3  | 32    |
|                |                 |               | D4s v3  | 20    |
|                |                 |               | D8s v3  | 10    |
|                |                 |               | D16s v3 | 5     |
|                |                 |               | D32s v3 | 2     |
|                |                 |               | D48s v3 | 1     |
|                |                 |               | D64s v3 | 1     |

También puede combinar varios tamaños de máquina virtual en la SKU Dsv3 de tipo 3. Las siguientes son combinaciones de ejemplo de empaquetados de máquinas virtuales en la SKU Dsv3 de tipo 3:
- 1 D64sv3 + 1 D8sv3 + 2 D4sv3
- 1 D48sv3 + 1 D16sv3 + 4 D4sv3
- 15 D4sv3 +  10 D2sv3

## <a name="dcsv2"></a>DCsv2
### <a name="dcsv2-type1"></a>DCsv2 de tipo 1

DCsv2 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Coffee Lake (Xeon® E-2288G con tecnología SGX). Ofrece 8 núcleos físicos, 8 vCPU y 64 GiB de memoria RAM. DCsv2 de tipo 1 ejecuta máquinas virtuales de la [serie DCsv2](dcv2-series.md).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host DCsv2 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 8              | 8               | 64 GiB        | DC8s v2 | 1     |

## <a name="easv4"></a>Easv4
### <a name="easv4-type1"></a>Easv4 de tipo 1

Easv4 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador AMD EPYC™ 7452 de 2,35 GHz. Ofrece 64 núcleos físicos, 96 vCPU y 672 GiB de memoria RAM. Easv4 de tipo 1 ejecuta máquinas virtuales de la [serie Easv4](eav4-easv4-series.md#easv4-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Easv4 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM  | N.º de máquinas virtuales |
|----------------|-----------------|---------------|----------|-------|
| 64             | 96              | 672 GiB       | E2as v4  | 32    |
|                |                 |               | E4as v4  | 21    |
|                |                 |               | E8as v4  | 10    |
|                |                 |               | E16as v4 | 5     |
|                |                 |               | E20as v4 | 4     |
|                |                 |               | E32as v4 | 2     |
|                |                 |               | E48as v4 | 1     |
|                |                 |               | E64as v4 | 1     |
|                |                 |               | E96as v4 | 1     |

## <a name="edsv4"></a>Edsv4
### <a name="edsv4-type1"></a>Edsv4 de tipo 1

Edsv4 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8272CL). Ofrece 52 núcleos físicos, 64 vCPU y 504 GiB de memoria RAM. Edsv4 de tipo 1 ejecuta máquinas virtuales de la [serie Edsv4](edv4-edsv4-series.md#edsv4-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Edsv4 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM  | N.º de máquinas virtuales |
|----------------|-----------------|---------------|----------|-------|
| 52             | 64              | 504 GiB       | E2ds v4  | 31    |
|                |                 |               | E4ds v4  | 15    |
|                |                 |               | E8ds v4  | 7     |
|                |                 |               | E16ds v4 | 3     |
|                |                 |               | E20ds v4 | 3     |
|                |                 |               | E32ds v4 | 1     |
|                |                 |               | E48ds v4 | 1     |
|                |                 |               | E64ds v4 | 1     |

## <a name="esv4"></a>Esv4
### <a name="esv4-type1"></a>Esv4 de tipo 1

Esv4 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8272CL). Ofrece 52 núcleos físicos, 80 vCPU y 504 GiB de memoria RAM. Esv4 de tipo 1 ejecuta máquinas virtuales de la [serie Esv4](ev4-esv4-series.md#esv4-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Esv4 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | E2s v4  | 31    |
|                |                 |               | E4s v4  | 15    |
|                |                 |               | E8s v4  | 7     |
|                |                 |               | E16s v4 | 3     |
|                |                 |               | E20s v4 | 3     |
|                |                 |               | E32s v4 | 1     |
|                |                 |               | E48s v4 | 1     |
|                |                 |               | E64s v4 | 1     |

## <a name="esv3"></a>Esv3
### <a name="esv3-type1"></a>Esv3 de tipo 1

Esv3 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Broadwell (Xeon® E5-2673 v4 de 2,3 GHz). Ofrece 40 núcleos físicos, 64 vCPU y 448 GiB de memoria RAM. Esv3 de tipo 1 ejecuta máquinas virtuales de la [serie Esv3](ev3-esv3-series.md#ev3-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Esv3 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 40             | 64              | 448 GiB       | E2s v3  | 28    |
|                |                 |               | E4s v3  | 14    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 2     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type2"></a>Esv3 de tipo 2

Esv3 de tipo 2 es una SKU de Dedicated Host que utiliza el procesador Intel® Skylake (Xeon® 8171M). Ofrece 48 núcleos físicos, 78 vCPU y 504 GiB de memoria RAM. Esv3 de tipo 2 ejecuta máquinas virtuales de la [serie Esv3](ev3-esv3-series.md#ev3-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Esv3 de tipo 2.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 48             | 76              | 504 GiB       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

### <a name="esv3-type3"></a>Esv3 de tipo 3

Esv3 de tipo 3 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8272CL). Ofrece 52 núcleos físicos, 80 vCPU y 504 GiB de memoria RAM. Esv3 de tipo 3 ejecuta máquinas virtuales de la [serie Esv3](ev3-esv3-series.md#ev3-series).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Esv3 de tipo 3.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 52             | 80              | 504 GiB       | E2s v3  | 31    |
|                |                 |               | E4s v3  | 15    |
|                |                 |               | E8s v3  | 7     |
|                |                 |               | E16s v3 | 3     |
|                |                 |               | E20s v3 | 3     |
|                |                 |               | E32s v3 | 1     |
|                |                 |               | E48s v3 | 1     |
|                |                 |               | E64s v3 | 1     |

## <a name="fsv2"></a>Fsv2
### <a name="fsv2-type2"></a>Fsv2 de tipo 2

Fsv2 de tipo 2 es una SKU de Dedicated Host que utiliza el procesador Intel® Skylake (Xeon® Platinum 8168). Ofrece 48 núcleos físicos, 72 vCPU y 144 GiB de memoria RAM. Fsv2 de tipo 2 ejecuta máquinas virtuales de la [serie Fsv2](fsv2-series.md).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Fsv2 de tipo 2.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 48             | 72              | 144 GiB       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 18    |
|                |                 |               | F8s v2  | 9     |
|                |                 |               | F16s v2 | 4     |
|                |                 |               | F32s v2 | 2     |
|                |                 |               | F48s v2 | 1     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

### <a name="fsv2-type3"></a>Fsv2 de tipo 3

Fsv2 de tipo 3 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8272CL). Ofrece 52 núcleos físicos, 86 vCPU y 504 GiB de memoria RAM. Fsv2 de tipo 3 ejecuta máquinas virtuales de la [serie Fsv2](fsv2-series.md).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Fsv2 de tipo 3.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 52             | 86              | 504 GiB       | F2s v2  | 32    |
|                |                 |               | F4s v2  | 21    |
|                |                 |               | F8s v2  | 10    |
|                |                 |               | F16s v2 | 5     |
|                |                 |               | F32s v2 | 2     |
|                |                 |               | F48s v2 | 1     |
|                |                 |               | F64s v2 | 1     |
|                |                 |               | F72s v2 | 1     |

## <a name="fxmds"></a>FXmds
### <a name="fxmds-type1"></a>FXmds de tipo 1

FXmds de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Gold 6246R). Ofrece 32 núcleos físicos, 48 vCPU y 1152 GiB de memoria RAM. FXmds de tipo 1 ejecuta máquinas virtuales de la [serie FX](fx-series.md).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host FXmds de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 32             | 48              | 1152 GiB     | FX4mds  | 12    |
|                |                 |               | FX12mds | 4     |
|                |                 |               | FX24mds | 2     |
|                |                 |               | FX36mds | 1     |
|                |                 |               | FX48mds | 1     |

## <a name="lsv2"></a>Lsv2
### <a name="lsv2-type1"></a>Lsv2 de tipo 1

Lsv2 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Gold 6246R). Ofrece 64 núcleos físicos, 80 vCPU y 640 GiB de memoria RAM. Lsv2 de tipo 1 ejecuta máquinas virtuales de la [serie Lsv2](lsv2-series.md).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Lsv2 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 64             | 80              | 640 GiB       | L8s v2  | 10    |
|                |                 |               | L16s v2 | 5     |
|                |                 |               | L32s v2 | 2     |
|                |                 |               | L48s v2 | 1     |
|                |                 |               | L64s v2 | 1     |
|                |                 |               | L80s v2 | 1     |

## <a name="m"></a>M
### <a name="ms-type1"></a>Ms de tipo 1

Ms de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8280). Ofrece 112 núcleos físicos, 128 vCPU y 2048 GiB de memoria RAM. Ms de tipo 1 ejecuta máquinas virtuales de la [serie M](m-series.md), incluidas las máquinas de las series M, Mls, Ms y Mts.

La siguiente configuración describe el empaquetado máximo de las máquinas virtuales uniformes que puede colocar en un host Ms de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------|-------|
| 112            | 128             | 2048 GiB     | M32ls   | 4     |
|                |                 |               | M32ts   | 4     |
|                |                 |               | M64     | 2     | 
|                |                 |               | M64s    | 2     |
|                |                 |               | M64ls   | 2     |
|                |                 |               | M128    | 1     | 
|                |                 |               | M128s   | 1     |

### <a name="msm-type1"></a>Msm de tipo 1

Msm de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8280). Ofrece 112 núcleos físicos, 128 vCPU y 3892 GiB de memoria RAM. Msm de tipo 1 ejecuta máquinas virtuales de la [serie M](m-series.md), incluidas las máquinas de las series Ms, Mms, Mts y Mls.

La siguiente configuración describe el empaquetado máximo de las máquinas virtuales uniformes que puede colocar en un host Msm de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM   | N.º de máquinas virtuales |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 128             | 3892 GiB     | M8ms      | 16    |
|                |                 |               | M8-2ms    | 16    |
|                |                 |               | M8-4ms    | 16    |
|                |                 |               | M16ms     | 8     |
|                |                 |               | M16-4ms   | 8     |
|                |                 |               | M16-8ms   | 8     |
|                |                 |               | M32ts     | 4     |
|                |                 |               | M32ls     | 4     |
|                |                 |               | M32ms     | 4     |
|                |                 |               | M32-8ms   | 4     |
|                |                 |               | M32-16ms  | 4     |
|                |                 |               | M64ms     | 2     |
|                |                 |               | M64       | 2     | 
|                |                 |               | M64s      | 2     |
|                |                 |               | M64m      | 2     |
|                |                 |               | M64ls     | 2     |
|                |                 |               | M64-16ms  | 2     |
|                |                 |               | M64-32ms  | 2     |
|                |                 |               | M128ms    | 1     |
|                |                 |               | M128s     | 1     |
|                |                 |               | M128m     | 1     |
|                |                 |               | M128      | 1     | 
|                |                 |               | M128-32ms | 1     |
|                |                 |               | M128-64ms | 1     |

## <a name="mv2"></a>Mv2
### <a name="msmv2-type1"></a>Msmv2 de tipo 1

Msm de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Skylake (Xeon® Platinum 8180M). Ofrece 224 núcleos físicos, 416 vCPU y 11 400 GiB de memoria RAM. Msmv2 de tipo 1 ejecuta máquinas virtuales de la [serie Mv2](mv2-series.md), incluidas las máquinas de las series Msv2 y Mmsv2.

La siguiente configuración describe el empaquetado máximo de las máquinas virtuales uniformes que puede colocar en un host Msm de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM       | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------------|-------|
| 224            | 416             | 11 400 GiB    | M208ms v2     | 2     |
|                |                 |               | M208s v2     | 2     |
|                |                 |               | M416-208ms v2 | 1     | 
|                |                 |               | M416-208s v2  | 1     | 
|                |                 |               | M416ms v2     | 1     |  
|                |                 |               | M416s v2      | 1     |  

### <a name="msv2-type1"></a>Msv2 de tipo 1

Msv2 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Skylake (Xeon® Platinum 8180M). Ofrece 224 núcleos físicos, 416 vCPU y 5700 GiB de memoria RAM. Msv2 de tipo 1 ejecuta máquinas virtuales de la [serie Mv2](mv2-series.md), incluidas las máquinas de las series Msv2 y Mmsv2.

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host Msv2 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM       | N.º de máquinas virtuales |
|----------------|-----------------|---------------|---------------|-------|
| 224            | 416             | 5700 GiB     | M208ms v2     | 2     |
|                |                 |               | M208s v2      | 1     |
|                |                 |               | M416-208s v2  | 1     |
|                |                 |               | M416s v2      | 1     |

## <a name="msv2"></a>Msv2
### <a name="mmsv2medmem-type1"></a>Mmsmv2MedMem de tipo 1
Mmsv2MedMem de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8280). Ofrece 112 núcleos físicos, 192 vCPU y 4096 GiB de memoria RAM. Mmsv2MedMem de tipo 1 ejecuta máquinas virtuales de la [serie Msv2](msv2-mdsv2-series.md), incluidas las máquinas de las series Msv2 y Mmsv2.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM   | N.º de máquinas virtuales |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 192             | 4096 GiB     | M32ms v2  | 4     |
|                |                 |               | M64s v2   | 3     |
|                |                 |               | M64ms v2  | 2     |
|                |                 |               | M128ms v2 | 1     |
|                |                 |               | M128s v2  | 1     |

### <a name="msv2medmem-type1"></a>Msv2MedMem de tipo 1
Msv2MedMem de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8280). Ofrece 112 núcleos físicos, 192 vCPU y 2048 GiB de memoria RAM. Msv2MedMem de tipo 1 ejecuta máquinas virtuales de la [serie Msv2](msv2-mdsv2-series.md), incluidas las máquinas de las series Msv2 y Mmsv2.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM    | N.º de máquinas virtuales |
|----------------|-----------------|---------------|------------|-------|
| 112            | 192             | 2048 GiB     | M32ms v2   | 2     |
|                |                 |               | M64s v2    | 2     |
|                |                 |               | M64ms v2   | 1     |
|                |                 |               | M128s v2   | 1     |

## <a name="mdsv2"></a>Mdsv2
### <a name="mdmsv2medmem-type1"></a>Mdmsv2MedMem de tipo 1
Mdmsv2MedMem de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8280). Ofrece 112 núcleos físicos, 192 vCPU y 4096 GiB de memoria RAM. Mdmsv2MedMem de tipo 1 ejecuta máquinas virtuales de la [serie Msv2](msv2-mdsv2-series.md), incluidas las máquinas de las series Mdsv2 y Mdmsv2.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM    | N.º de máquinas virtuales |
|----------------|-----------------|---------------|------------|-------|
| 112            | 192             | 4096 GiB     | M32dms v2  | 4     |
|                |                 |               | M64ds v2   | 2     |
|                |                 |               | M64dms v2  | 2     |
|                |                 |               | M128ds v2  | 1     |
|                |                 |               | M128dms v2 | 1     |

### <a name="mdsv2medmem-type1"></a>Mdsv2MedMem de tipo 1
Mdsv2MedMem de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Cascade Lake (Xeon® Platinum 8280). Ofrece 112 núcleos físicos, 192 vCPU y 2048 GiB de memoria RAM. Mdsv2MedMem de tipo 1 ejecuta máquinas virtuales de la [serie Msv2](msv2-mdsv2-series.md), incluidas las máquinas de las series Mdsv2 y Mdmsv2.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM   | N.º de máquinas virtuales |
|----------------|-----------------|---------------|-----------|-------|
| 112            | 192             | 2048 GiB     | M32dms v2 | 2     |
|                |                 |               | M64ds v2  | 2     |
|                |                 |               | M64dms v2 | 1     |
|                |                 |               | M128ds v2 | 1     |

## <a name="nvasv4"></a>NVasv4
### <a name="nvasv4-type1"></a>NVasv4 de tipo 1

NVasv4 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador AMD® Rome (EPYC™ 7V12) con GPU AMD Radeon Instinct MI25. Ofrece 128 núcleos físicos, 128 vCPU y 448 GiB de memoria RAM. NVasv4 de tipo 1 ejecuta máquinas virtuales de la [serie NVsv4](nvv4-series.md).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host NVasv4 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM   | N.º de máquinas virtuales |
|----------------|-----------------|---------------|-----------|-------|
| 128            | 128             | 448 GiB       | NV4as v4  | 30    |
|                |                 |               | NV8as v4  | 15    |
|                |                 |               | NV16as v4 | 7     |
|                |                 |               | NV32as v4 | 3     |

## <a name="nvsv3"></a>NVsv3
### <a name="nvsv3-type1"></a>NVsv3 de tipo 1

NVsv3 de tipo 1 es una SKU de Dedicated Host que utiliza el procesador Intel® Broadwell (E5-2690 v4) con GPU NVDIDIA Tesla M60 y tecnología NVIDIA GRID. Ofrece 28 núcleos físicos, 48 vCPU y 448 GiB de memoria RAM. NVsv3 de tipo 1 ejecuta máquinas virtuales de la [serie NVv3](nvv3-series.md).

La siguiente configuración describe el empaquetado máximo de máquinas virtuales uniformes que puede poner en un host NVsv3 de tipo 1.

| Núcleos físicos | vCPU disponibles | RAM disponible | Tamaño de VM  | N.º de máquinas virtuales |
|----------------|-----------------|---------------|----------|-------|
| 28             | 48              | 448 GiB       | NV12s v3 | 4     |
|                |                 |               | NV24s v3 | 2     |
|                |                 |               | NV48s v3 | 1     | 


## <a name="next-steps"></a>Pasos siguientes

- Para obtener más detalles, consulte la introducción a los [hosts dedicados](dedicated-hosts.md).

- Hay una plantilla de ejemplo, disponible en las [plantillas de inicio rápido de Azure](https://github.com/Azure/azure-quickstart-templates/blob/master/quickstarts/microsoft.compute/vm-dedicated-hosts/README.md), en la que se usan zonas y dominios de error para obtener la máxima resistencia en una región.