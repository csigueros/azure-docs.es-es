---
title: Procedimientos recomendados de E/S directa de Linux para Azure NetApp Files | Microsoft Docs
description: Describe la E/S directa de Linux y los procedimientos recomendados que se deben seguir para Azure NetApp Files.
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
ms.openlocfilehash: 6497d91169c7530251a473e8e06c26ce411e0e6a
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233517"
---
# <a name="linux-direct-io-best-practices-for-azure-netapp-files"></a>Procedimientos recomendados de E/S directa de Linux para Azure NetApp Files

Este artículo le ayuda a comprender los procedimientos recomendados de E/S directa para Azure NetApp Files.  

## <a name="direct-io"></a>E/S directa

 El parámetro más común que se usa en los puntos de referencia de rendimiento del almacenamiento es la E/S directa. Es compatible con FIO y Vdbench. DISKSPD ofrece compatibilidad con una construcción similar de E/S asignada a memoria. Con la E/S directa, se omite la caché del sistema de archivos, se evitan las operaciones para la copia de acceso directo a memoria y las pruebas de almacenamiento se realizan de forma rápida y sencilla.  

El uso del parámetro de E/S directa facilita las pruebas de almacenamiento. No se lee ningún dato de la memoria caché del sistema de archivos en el cliente. Por lo tanto, la prueba está realmente probando el esfuerzo del protocolo de almacenamiento y el propio servicio, en lugar de las velocidades de acceso a la memoria. Además, sin las copias de memoria de DMA, las operaciones de lectura y escritura son eficaces desde una perspectiva de procesamiento. 

Tomemos el comando de Linux `dd` como carga de trabajo de ejemplo. Sin la marca `odirect` opcional, todas las operaciones de E/S generadas por `dd` se sirven desde la caché del búfer de Linux. Las lecturas cuyos bloques ya están en memoria no se recuperan del almacenamiento. Las lecturas que dan lugar a una pérdida de caché del búfer terminan siendo leídas desde el almacenamiento mediante la lectura por adelantado de NFS con resultados variables, en función de factores que se pueden ajustar como el valor de `rsize` del montaje y la lectura adelantada del cliente. Cuando las escrituras se envían mediante la caché del búfer, usan un mecanismo de escritura subyacente que no se ha ajustado y usa una cantidad significativa de paralelismo para enviar los datos al dispositivo de almacenamiento. Puede intentar ejecutar dos secuencias independientes de E/S, una ejecución de `dd` para las lecturas y otra ejecución de `dd` para las escrituras. Pero de hecho, el sistema operativo, sin ajustar, favorece las escrituras sobre las lecturas y usa más paralelismo en ello.

Aparte de la base de datos, pocas aplicaciones usan las E/S directa. En su lugar, optan por aprovechar las ventajas de una caché de memoria grande para lecturas repetidas y una memoria caché de escritura subyacente para escrituras asincrónicas. En resumen, el uso de la E/S directa convierte la prueba en un micropunto de referencia *si* la aplicación que se sintetiza usa la memoria caché del sistema de archivos.  

Estas son algunas bases de datos que admiten la E/S directa: 

* Oracle 
* SAP HANA
* MySQL (motor de almacenamiento InnoDB)
* RocksDB
* PostgreSQL
* Teradata

## <a name="best-practices"></a>Procedimientos recomendados 

Probar con `directio` es una excelente manera de comprender los límites del servicio de almacenamiento y el cliente. Para comprender mejor cómo se comportará la propia aplicación (si la aplicación no usa `directio`), también debe ejecutar pruebas en la memoria caché del sistema de archivos.

## <a name="next-steps"></a>Pasos siguientes  

* [Procedimientos recomendados de caché del sistema de archivos de Linux para Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Procedimientos recomendados de las opciones de montaje de NFS de Linux para Azure NetApp Files](performance-linux-mount-options.md)
* [Procedimientos recomendados de simultaneidad de Linux para Azure NetApp Files](performance-linux-concurrency-session-slots.md)
* [Procedimientos recomendados de lectura anticipada de NFS de Linux](performance-linux-nfs-read-ahead.md)
* [Procedimientos recomendados de SKU de máquinas virtuales de Azure](performance-virtual-machine-sku.md) 
* [Bancos de pruebas de rendimiento para Linux](performance-benchmarks-linux.md) 
