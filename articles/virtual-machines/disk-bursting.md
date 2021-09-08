---
title: Seguridad de disco administrado
description: Obtenga información sobre la expansión de disco para discos de Azure y para máquinas virtuales de Azure
author: roygara
ms.author: rogarana
ms.date: 06/29/2021
ms.topic: conceptual
ms.service: storage
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 7cd3c1d4a0da5ca0741f6d7f05a1cf082d2e922e
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696544"
---
# <a name="managed-disk-bursting"></a>Seguridad de disco administrado

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles :heavy_check_mark: Conjuntos de escalado uniformes

Azure ofrece la posibilidad de aumentar las IOPS de almacenamiento en disco y el rendimiento de MB/s, que se conoce como "expansión", tanto en las máquinas virtuales como en los discos. Puede usar la expansión de máquinas virtuales y discos para mejorar el rendimiento de la expansión tanto de las máquinas virtuales como de los discos.

La expansión de los recursos de disco y las máquinas virtuales de Azure no es interdependiente. No es necesario tener una máquina virtual expandible para que un disco expandible se expanda. Del mismo modo, no es necesario tener un disco expandible conectado a la máquina virtual expandible para que esta se expanda.

## <a name="common-scenarios"></a>Escenarios frecuentes
Los siguientes escenarios pueden beneficiarse en gran medida de la expansión:
- **Mejora de los tiempos de arranque**: con la expansión, la instancia se iniciará a una velocidad más rápida. Por ejemplo, el disco de SO predeterminado para las VM habilitadas para Premium es el P4, con un rendimiento aprovisionado de hasta 120 IOPS y 25 MB/s. Con la expansión, el disco P4 puede llegar hasta 3500 IOPS y 170 MB/s, lo que permite multiplicar por 6 la aceleración del arranque.
- **Controlar trabajos por lotes**: algunas cargas de trabajo de aplicaciones son cíclicas por naturaleza. Requieren un rendimiento de línea base la mayor parte del tiempo y un rendimiento mayor durante breves períodos de tiempo. Un ejemplo de esto es un programa de contabilidad que procesa transacciones diariamente y que requiere una pequeña cantidad de tráfico de disco. Al finalizar el mes, el programa finalizará la conciliación de los informes que requieren una cantidad mucho mayor de tráfico de disco.
- **Picos de tráfico**: tanto los servidores web como sus aplicaciones pueden experimentar picos de tráfico en cualquier momento. Si el servidor web está respaldado por máquinas virtuales o discos que usan la expansión, los servidores estarán mejor equipados para controlar los picos de tráfico. 

## <a name="disk-level-bursting"></a>Expansión en el nivel de disco

Actualmente, hay dos tipos de discos administrados que pueden expandirse: los [SSD prémium](disks-types.md#premium-ssd) y los [SSD estándar](disks-types.md#standard-ssd). La expansión no se aplica a otros tipos de disco actualmente. Hay dos modelos de expansión para los discos:

- Un modelo de expansión a petición (versión preliminar), en el que el disco se expande cada vez que sus necesidades superan su capacidad actual. Este modelo incurre en cargos adicionales cada vez que el disco se expande. La expansión bajo demanda solo está disponible para los SSD premium de más de 512 GiB.
- Un modelo basado en el crédito, en el que el disco solo se expandirá si tiene créditos de expansión acumulados en su cubo de crédito. Este modelo no incurrirá en cargos adicionales cuando el disco se expanda. La expansión basada en crédito solo está disponible para los SSD premium y estándar de 512 GiB e inferior.

Los discos [SSD prémium](disks-types.md#premium-ssd) de Azure pueden usar cualquier modelo de expansión, pero los de tipo [SSD estándar](disks-types.md#standard-ssd) solo ofrecen la expansión basada en crédito actualmente.

Además, el [nivel de rendimiento de los discos administrados puede cambiar](disks-change-performance.md), lo que podría ser ideal si la carga de trabajo se ejecutara en la expansión.

|  |Expansión basada en crédito  |Expansión a petición  |Cambio del nivel de rendimiento  |
|---------|---------|---------|---------|
| **Escenarios**|Ideal para el escalado a corto plazo (30 minutos o menos).|Ideal para el escalado a corto plazo (sin restricción de tiempo).|Ideal si la carga de trabajo se ejecutaría continuamente en la expansión.|
|**Costee**     |Gratuito         |El costo es variable; para más información, consulte la sección de [facturación](#billing).        |El costo de cada nivel de rendimiento es fijo, para más información, consulte [Precios de Managed Disks](https://azure.microsoft.com/pricing/details/managed-disks/).         |
|**Disponibilidad**     |Solo está disponible para SSD premium y estándar con un tamaño de 512 GiB, y menores.         |Solo está disponible para SSD Prémium de más dé que 512 GiB.         |Disponible para todos los tamaños de SSD Prémium.         |
|**Introducción: autenticación de Active Directory Domain Services local en SMB para recursos compartidos de archivos de Azure**     |Habilitado de forma predeterminada en los discos aptos.         |Debe habilitarlo el usuario.         |El usuario debe cambiar su nivel de forma manual.         |

[!INCLUDE [managed-disks-bursting](../../includes/managed-disks-bursting-2.md)]

## <a name="next-steps"></a>Pasos siguientes

- Para habilitar la expansión a petición, consulte [Habilitación de la expansión a petición](disks-enable-bursting.md).
- Para aprender a obtener información sobre los recursos de expansión, consulte [Métricas de expansión de disco](disks-metrics.md).
- Para ver exactamente cuál es la ráfaga de cada tamaño de disco aplicable, vea [Objetivos de escalabilidad y rendimiento para discos de máquinas virtuales](disks-scalability-targets.md).