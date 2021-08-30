---
title: Procedimientos recomendados de caché del sistema de archivos de Linux para Azure NetApp Files | Microsoft Docs
description: Describe los procedimientos recomendados de caché del sistema de archivos de Linux que se deben seguir para Azure NetApp Files.
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
ms.openlocfilehash: d0d097a13fa24fb62f70648a34edd27e3b5a0636
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203866"
---
# <a name="linux-filesystem-cache-best-practices-for-azure-netapp-files"></a>Procedimientos recomendados de caché del sistema de archivos de Linux para Azure NetApp Files

Este artículo le ayuda a comprender los procedimientos recomendados de caché del sistema de archivos para Azure NetApp Files.  

## <a name="filesystem-cache-tunables"></a>Elementos de caché del sistema de archivos

Debe comprender los siguientes factores sobre los elementos de caché del sistema de archivos:  

* El vaciado de un búfer desfasado deja los datos en un estado limpio que se puede usar para futuras lecturas hasta que la presión de memoria conduce a la expulsión.  
* Hay tres desencadenadores para una operación de vaciado asincrónica:
    * Basado en el tiempo: cuando un búfer alcanza la antigüedad definida por este elemento, debe marcarse para la limpieza (es decir, vaciado o escritura en el almacenamiento).
    * Presión de memoria: [`vm.dirty_ratio | vm.dirty_bytes`](#vmdirty_ratio--vmdirty_bytes) para obtener detalles.
    * Cerrar: cuando se cierra un identificador de archivo, todos los búferes desfasados se vacían asincrónicamente en el almacenamiento.

Cuatro elementos se encargan de controlar estos factores. Cada elemento se puede optimizar dinámica y persistentemente mediante `tuned` o `sysctl` en el archivo `/etc/sysctl.conf`. El ajuste de estas variables mejora el rendimiento de las aplicaciones.  

> [!NOTE]
> La información que se describe en este artículo se descubrió durante los ejercicios de validación de SAS GRID y SAS Viya. Como tal, los elementos se basan en lecciones aprendidas de los ejercicios de validación. De forma similar, muchas aplicaciones se beneficiarán del ajuste de estos parámetros.

### `vm.dirty_ratio | vm.dirty_bytes` 

Estos dos elementos definen la cantidad de RAM que se puede usar para los datos modificados, pero que aún no se han escrito en el almacenamiento estable.  Al establecerse uno de los elementos, el otro elemento se establece automáticamente en cero; RedHat recomienda que no se establezca manualmente ninguno de los dos elementos en cero.  RedHat establece la opción `vm.dirty_ratio` (el valor predeterminado de los dos) en el 20 % o el 30 % de la memoria física dependiendo del sistema operativo, que es una cantidad significativa teniendo en cuenta la superficie de memoria de los sistemas modernos. Se debe tener en cuenta el establecimiento de `vm.dirty_bytes` en lugar de `vm.dirty_ratio` para una experiencia más coherente independientemente del tamaño de la memoria.  Por ejemplo, el trabajo en curso con SAS GRID determinó 30 MiB como una configuración adecuada para el mejor rendimiento de la carga de trabajo mixta general. 

### `vm.dirty_background_ratio | vm.dirty_background_bytes` 

Estos elementos definen el punto de partida en el que el mecanismo de escritura diferida de Linux comienza a vaciar bloques desfasados en el almacenamiento estable. Redhat tiene como valor predeterminado el 10 % de la memoria física, que, en un sistema de memoria grande, es una cantidad significativa de datos para iniciar el vaciado. Usando SAS GRID como ejemplo, la recomendación siempre ha sido establecer `vm.dirty_background` en un tamaño de 1/5 de `vm.dirty_ratio` o `vm.dirty_bytes`. Teniendo en cuenta el grado de intensidad con el que se establece el valor `vm.dirty_bytes` para SAS GRID, no se establece ningún valor específico aquí.  

### `vm.dirty_expire_centisecs` 

Este elemento define la antigüedad que puede tener un búfer desfasado antes de que deba etiquetarse para la escritura asincrónica. Use la carga de trabajo CAS de SAS Viya como ejemplo. Una carga de trabajo de escritura dominante efímera detectó que establecer este valor en 300 centisegundos (3 segundos) era óptimo, siendo el valor predeterminado 3000 centisegundos (30 segundos).  

SAS Viya comparte datos de CAS en varios fragmentos pequeños de unos pocos megabytes cada uno.  En lugar de cerrar estos identificadores de archivo después de escribir datos en cada partición, los identificadores se dejan abiertos y la aplicación asigna la memoria a los búferes que hay dentro.  Sin un cierre, no habrá ningún vaciado hasta que hayan transcurrido la presión de memoria o 30 segundos. Se confirmó que esperar la presión de memoria era subóptimo, lo mismo que esperar a que expirara un temporizador largo. A diferencia de SAS GRID, que buscaba el mejor rendimiento general, SAS Viya buscaba optimizar el ancho de banda de escritura.  

### `vm.dirty_writeback_centisecs` 

El subproceso de vaciado de kernel es responsable de vaciar asincrónicamente búferes desfasados entre las suspensiones de cada subproceso de vaciado.  Este elemento define el importe gastado con la suspensión entre vaciados.  Teniendo en cuenta el valor `vm.dirty_expire_centisecs` de 3 segundos que usa SAS Viya, SAS estableció este elemento en 100 centisegundos (1 segundo) en lugar de los 500 centisegundos (5 segundos) predeterminados para encontrar el mejor rendimiento general.

## <a name="impact-of-an-untuned-filesystem-cache"></a>Impacto de una caché del sistema de archivos sin optimizar

Teniendo en cuenta los elementos de memoria virtual predeterminados y la cantidad de RAM de los sistemas modernos, es posible que la escritura diferida ralentice otras operaciones enlazadas a almacenamiento desde la perspectiva del cliente específico que impulsa esta carga de trabajo mixta.  Se pueden esperar los siguientes síntomas de una máquina Linux con mucha actividad de escritura, la caché llena y sin optimizar.  

* Las listas de directorios `ls` tardan lo suficiente para que parezca que no responden.
* El rendimiento de lectura en el sistema de archivos disminuye significativamente en comparación con el rendimiento de escritura.
* `nfsiostat` notifica latencias de escritura **en segundos o más tiempo**.

Es posible que solo experimente este comportamiento en *la máquina Linux* que realiza la carga de trabajo con mucha actividad de escritura mixta.  Además, la experiencia se degrada en todos los volúmenes NFS montados en un solo punto de conexión de almacenamiento.  Si los montajes proceden de dos o más puntos de conexión, solo los volúmenes que comparten un punto de conexión presentan este comportamiento.

Se ha mostrado el establecimiento de los parámetros de caché del sistema de archivos como se describe en esta sección para solucionar los problemas.

## <a name="monitoring-virtual-memory"></a>Supervisión de la memoria virtual

Para comprender lo que sucede con la memoria virtual y la escritura diferida, considere los siguientes fragmento de código y salida.  *Con modificaciones* representa la cantidad de memoria desfasada del sistema y la *escritura diferida* representa la cantidad de memoria que se escribe activamente en el almacenamiento.  

`# while true; do echo "###" ;date ; egrep "^Cached:|^Dirty:|^Writeback:|file" /proc/meminfo; sleep 5; done`

La siguiente salida procede de un experimento en el que `vm.dirty_ratio` y la relación `vm.dirty_background` se establecieron en el 2 % y el 1 % de la memoria física, respectivamente.  En este caso, el vaciado comenzó en 3,8 GiB, el 1 % del sistema de memoria de 384 GiB.  La escritura diferida se parecía mucho al rendimiento de escritura en NFS. 

```
Cons
Dirty:                                    1174836 kB
Writeback:                         4 kB
###
Dirty:                                    3319540 kB
Writeback:                         4 kB
###
Dirty:                                    3902916 kB        <-- Writes to stable storage begins here
Writeback:                         72232 kB   
###
Dirty:                                    3131480 kB
Writeback:                         1298772 kB   
``` 

## <a name="next-steps"></a>Pasos siguientes  

* [Procedimientos recomendados de E/S directa de Linux para Azure NetApp Files](performance-linux-direct-io.md)
* [Procedimientos recomendados de las opciones de montaje de NFS de Linux para Azure NetApp Files](performance-linux-mount-options.md)
* [Procedimientos recomendados de simultaneidad de Linux para Azure NetApp Files](performance-linux-concurrency-session-slots.md)
* [Procedimientos recomendados de lectura anticipada de NFS de Linux](performance-linux-nfs-read-ahead.md)
* [Procedimientos recomendados de SKU de máquinas virtuales de Azure](performance-virtual-machine-sku.md) 
* [Bancos de pruebas de rendimiento para Linux](performance-benchmarks-linux.md) 
