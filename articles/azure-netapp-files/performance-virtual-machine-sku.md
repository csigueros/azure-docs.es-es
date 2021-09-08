---
title: Procedimientos recomendados de SKU de máquinas virtuales de Azure para Azure NetApp Files | Microsoft Docs
description: Describe los procedimientos recomendados de Azure NetApp Files sobre las SKU de máquinas virtuales de Azure, incluidas las diferencias dentro y entre las SKU.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/02/2021
ms.author: b-juche
ms.openlocfilehash: 6644a52ee094ce0076293b37fa79db28568c9c67
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233511"
---
# <a name="azure-virtual-machine-skus-best-practices-for-azure-netapp-files"></a>Procedimientos recomendados de SKU de máquinas virtuales de Azure para Azure NetApp Files

Este artículo describe los procedimientos recomendados de Azure NetApp Files sobre las SKU de máquinas virtuales de Azure, incluidas las diferencias dentro y entre las SKU.   

## <a name="sku-selection-considerations"></a>Consideraciones de la selección de SKU

El rendimiento de almacenamiento implica más que la velocidad del propio almacenamiento. La velocidad del procesador y la arquitectura tienen mucho que ver con la experiencia general de cualquier nodo de proceso determinado. Como parte del proceso de selección de una SKU determinada, debe tener en cuenta los siguientes factores:

* AMD o Intel: por ejemplo, SAS usa una biblioteca de kernel matemáticos diseñada específicamente para procesadores Intel.  En este caso, se prefieren las SKU de Intel a las SKU de AMD.
* Los tipos de máquina F2, E_v3 y D_v3 se basan en más de un conjunto de chips.  Al usar Azure Dedicated Hosts, puede seleccionar modelos específicos (Broadwell, Cascade Lake o Skylake al seleccionar el tipo E, por ejemplo). De lo contrario, la selección del conjunto de chips no es determinista.  Si va a implementar un clúster de HPC y es necesaria una experiencia coherente en el inventario, puede considerar la posibilidad de usar Azure Dedicated Hosts únicos o usar SKU de un solo conjunto de chips, como E_v4 o D_v4.
* La variabilidad del rendimiento con el almacenamiento conectado a la red (NAS) se ha observado en las pruebas con las SKU basadas en Intel Broadwell y las SKU basadas en AMD EPYC™ 7551. Se han observado dos problemas:
    * Cuando la interfaz de red acelerada se asigna de forma inapropiada a un nodo NUMA no óptimo, el rendimiento de lectura disminuye significativamente.   Aunque la asignación de la interfaz de red acelerada a un nodo NUMA específico es beneficiosa para las SKU más recientes, debe considerarse un requisito en las SKU con estos conjuntos de chips (Lv2| E_v3| D_v3).
    * Las máquinas virtuales que se ejecutan en Lv2, E_v3 o D_v3, o que se ejecutan en un conjunto de chips de Broadwell son más susceptibles a la contención de recursos que cuando se ejecutan en otras SKU.  Al probar con varias máquinas virtuales que se ejecutan dentro de un único Azure Dedicated Host, se ha observado que la ejecución de una carga de trabajo de almacenamiento basada en red desde una máquina virtual reduce el rendimiento de las cargas de trabajo de almacenamiento basadas en red que se ejecutan desde una segunda máquina virtual. La disminución es más pronunciada cuando no se ha acelerado la interfaz de red o no se ha asignado correctamente el nodo NUMA de alguna de las máquinas virtuales.  Tenga en cuenta que los E_v3 y D_V3 pueden llegar a Haswell, Broadwell, Cascade Lake o Skylake. 

Para obtener un rendimiento más coherente al seleccionar máquinas virtuales, seleccione entre las SKU con un único tipo de conjunto de chips: se prefieren las SKU más recientes antes que los modelos más antiguos cuando estén disponibles.  Tenga en cuenta que, además de usar un host dedicado, es poco probable predecir correctamente el tipo de hardware al que llegan las máquinas virtuales E_v3 o D_v3.  Al usar la SKU E_v3 o D_v3:

* Cuando se desactiva y desasigna una máquina virtual y luego se activa de nuevo, es probable que la máquina virtual cambie los hosts y, del mismo modo, los modelos de hardware.
* Cuando las aplicaciones se implementan en varias máquinas virtuales, se espera que las máquinas virtuales se ejecuten en hardware heterogéneo.

## <a name="differences-within-and-between-skus"></a>Diferencias dentro y entre las SKU
 
En la tabla siguiente se resaltan las diferencias dentro y entre las SKU.  Tenga en cuenta, por ejemplo, que el conjunto de chips de E_v3 y D_v3 subyacentes varían entre Broadwell, Cascade Lake, Skylake y también en el caso de D_v3.  

|     Familia    |     Versión    |   Descripción     |     Frecuencia (GHz)    |
|-|-|-|-|
|     E    |     V3    |     Intel® Xeon® E5-2673 v4 (Broadwell)    |     2.3 (3.6)    |
|     E    |     V3    |     Intel® Xeon® Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     E    |     V3    |     Intel® Xeon® Platinum 8171M (Skylake)    |     2.1 (3.8)    |
|     E    |     V4    |     Intel® Xeon® Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     Ea    |     V4    |     AMD EPYC™ 7452    |     2.35 (3.35)    |
|     D    |     V3    |     Intel® Xeon® E5-2673 v4 (Broadwell)    |     2.3 (3.6)    |
|     D    |     V3    |     Intel® Xeon® E5-2673 v3 (Haswell)    |     2.3 (2.3)    |
|     D    |     V3    |     Intel® Xeon® Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     D    |     V3    |     Intel® Xeon® Platinum 8171M (Skylake)    |     2.1 (3.8)    |
|     D    |     V4    |     Intel® Xeon® Platinum 8272CL (Cascade Lake)    |     2.6 (3.7)    |
|     Da    |     V4    |     AMD EPYC™ 7452    |     2.35 (3.35)    |
|     L    |     V2    |     AMD EPYC™ 7551    |     2.0 (3.2)    |
|     F    |     1    |     Intel Xeon® E5-2673 v3 (Haswell)     |     2.3 (2.3)    |
|     F    |     2    |     Intel® Xeon® Platinum 8168M (Cascade Lake)    |     2.7 (3.7)    |
|     F    |     2    |     Gen 2 Intel® Xeon® Platinum 8272CL (Skylake)    |     2.1 (3.8)   |

Al preparar un entorno de SAS GRID de varios nodos para producción, es posible que observe una varianza repetible de una hora y quince minutos entre las ejecuciones de análisis sin otra diferencia que el hardware subyacente.  

|     Plataforma de SKU y hardware    |     Tiempos de ejecución del trabajo    |
|-|-|
|     E32-8_v3 (Broadwell)    |     5,5 horas    |
|     E32-8_v3 (Cascade Lake)    |     4,25 horas    |

En ambos conjuntos de pruebas, se seleccionó una SKU E32-8_v3 y se utilizó RHEL 8.3 junto con la opción de montaje `nconnect=8`.

## <a name="best-practices"></a>Procedimientos recomendados 

* Siempre que sea posible, seleccione E_v4, D_v4 o más recientes en lugar de las SKU E_v3 o D_v3.  
* Siempre que sea posible, seleccione Ed_v4, Dd_v4 o más recientes en lugar de las SKU L2.

## <a name="next-steps"></a>Pasos siguientes  

* [Procedimientos recomendados de E/S directa de Linux para Azure NetApp Files](performance-linux-direct-io.md)
* [Procedimientos recomendados de caché del sistema de archivos de Linux para Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Procedimientos recomendados de las opciones de montaje de NFS de Linux para Azure NetApp Files](performance-linux-mount-options.md)
* [Procedimientos recomendados de simultaneidad de Linux](performance-linux-concurrency-session-slots.md)
* [Procedimientos recomendados de lectura anticipada de NFS de Linux](performance-linux-nfs-read-ahead.md)
* [Bancos de pruebas de rendimiento para Linux](performance-benchmarks-linux.md) 
