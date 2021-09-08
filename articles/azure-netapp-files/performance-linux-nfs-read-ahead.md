---
title: 'Procedimientos recomendados de lectura anticipada de NFS de Linux para Azure NetApp Files: ranuras de sesión y entradas de tabla de ranuras | Microsoft Docs'
description: Se describen los procedimientos recomendados de lectura anticipada de la caché del sistema de archivos y NFS de Linux para Azure NetApp Files.
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
ms.openlocfilehash: f87d2742ab34cdf5b6509e88b403240d388fa373
ms.sourcegitcommit: 285d5c48a03fcda7c27828236edb079f39aaaebf
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/02/2021
ms.locfileid: "113233516"
---
# <a name="linux-nfs-read-ahead-best-practices-for-azure-netapp-files"></a>Procedimientos recomendados de lectura anticipada de NFS de Linux para Azure NetApp Files

Este artículo le ayuda a comprender los procedimientos recomendados de caché del sistema de archivos para Azure NetApp Files.  

La lectura anticipada de NFS solicita de forma predictiva bloques de un archivo antes de las solicitudes de E/S de la aplicación. Está diseñado para mejorar el rendimiento de lectura secuencial del cliente.  Hasta hace poco, en todas las distribuciones modernas de Linux se establecía la lectura anticipada en un valor equivalente a 15 veces el de los sistemas de archivos montados `rsize`.  

En la tabla siguiente se muestran los valores de lectura anticipada predeterminados para cada opción de montaje `rsize` determinada.

| Sistema de archivos montado `rsize` | Bloquea la lectura anticipada |
|-|-|
| 64 KiB | 960 KiB |
| 256 KiB | 3840 KiB |
| 1024 KiB | 15 360 KiB |

En RHEL 8.3 y Ubuntu 18.04 se han introducido cambios que podrían afectar negativamente al rendimiento de lectura secuencial del cliente.  A diferencia de las versiones anteriores, en estas distribuciones se establece la lectura anticipada en un valor predeterminado de 128 KiB, independientemente de la opción de montaje `rsize` que se use. La actualización desde versiones con el valor de lectura anticipada mayor a las que tienen el valor predeterminado de 128 KiB ha experimentado una disminución en el rendimiento de lectura secuencial. Pero los valores de lectura anticipada se pueden ajustar hacia arriba de forma dinámica y persistente.  Por ejemplo, las pruebas con SAS GRID han demostrado que el valor de lectura de 15 360 KiB es óptimo en comparación con 3840 KiB, 960 KiB y 128 KiB.  No se han ejecutado pruebas suficientes más allá de 15 360 KiB para determinar el impacto positivo o negativo.

En la tabla siguiente se muestran los valores de lectura anticipada predeterminados para cada opción distribución disponible actualmente.

|     Distribución    |     Release    |     Bloquea la lectura anticipada    |
|-|-|-|
|     RHEL    |     8.3    |     128 KiB    |
|     RHEL    |     7.X, 8.0, 8.1, 8.2    |     15 X `rsize`    |
|     SLES    |     12.X: al menos 15SP2    |     15 X `rsize`    |
|     Ubuntu    |     18.04: al menos 20.04    |     128 KiB    |
|     Ubuntu    |     16.04    |     15 X `rsize`    |
|     Debian    |     Hasta 10 como mínimo    |     15 x `rsize`    |


## <a name="how-to-work-with-per-nfs-filesystem-read-ahead"></a>Procedimiento para trabajar con la lectura anticipada por sistema de archivos NFS   

La lectura anticipada de NFS se define en el punto de montaje para un sistema de archivos NFS. La configuración predeterminada se puede ver y establecer de forma dinámica y persistente.  Para mayor comodidad, se ha proporcionado el siguiente script de Bash escrito por Red Hat a fin de ver o configurar de forma dinámica la lectura anticipada por cada sistema de archivos NFS montado.

La lectura anticipada se puede definir dinámicamente por montaje de NFS mediante el siguiente script, o bien de forma persistente mediante reglas `udev`, como se muestra en esta sección.  A fin de mostrar o establecer la lectura anticipada para un sistema de archivos NFS montado, puede guardar el siguiente script como un archivo de Bash, modificar los permisos del archivo para convertirlo en ejecutable (`chmod 544 readahead.sh`) y realizar la ejecución como se muestra. 

## <a name="how-to-show-or-set-read-ahead-values"></a>Procedimiento para mostrar o establecer valores de lectura anticipada   

Para mostrar el valor de lectura anticipada actual (el valor devuelto está en KiB), ejecute el comando siguiente:  

`$ ./readahead.sh  show <mount-point>`   

Para establecer un nuevo valor para la lectura anticipada, ejecute el siguiente comando:   

`$ ./readahead.sh  show <mount-point> [read-ahead-kb]`
 
### <a name="example"></a>Ejemplo   

```
#!/bin/bash
# set | show readahead for a specific mount point
# Useful for things like NFS and if you do not know / care about the backing device
#
# To the extent possible under law, Red Hat, Inc. has dedicated all copyright
# to this software to the public domain worldwide, pursuant to the
# CC0 Public Domain Dedication. This software is distributed without any warranty.
# See <http://creativecommons.org/publicdomain/zero/1.0/>.
#

E_BADARGS=22
function myusage() {
echo "Usage: `basename $0` set|show <mount-point> [read-ahead-kb]"
}

if [ $# -gt 3 -o $# -lt 2 ]; then
   myusage
   exit $E_BADARGS
fi

MNT=${2%/}
BDEV=$(grep $MNT /proc/self/mountinfo | awk '{ print $3 }')

if [ $# -eq 3 -a $1 == "set" ]; then
   echo $3 > /sys/class/bdi/$BDEV/read_ahead_kb
elif [ $# -eq 2 -a $1 == "show" ]; then
   echo "$MNT $BDEV /sys/class/bdi/$BDEV/read_ahead_kb = "$(cat /sys/class/bdi/$BDEV/read_ahead_kb)
else
   myusage
   exit $E_BADARGS
fi
```

## <a name="how-to-persistently-set-read-ahead-for-nfs-mounts"></a>Procedimiento para establecer de forma persistente la lectura anticipada para montajes NFS

A fin de establecer de forma persistente la lectura anticipada para montajes NFS, se pueden escribir reglas `udev` de la siguiente manera:    

1. Cree y pruebe `/etc/udev/rules.d/99-nfs.rules`:

    `SUBSYSTEM=="bdi", ACTION=="add", PROGRAM="/bin/awk -v bdi=$kernel 'BEGIN{ret=1} {if ($4 == bdi) {ret=0}} END{exit ret}' /proc/fs/nfsfs/volumes", ATTR{read_ahead_kb}="15380"`

2. Aplique la regla `udev`:   

    `$udevadm control --reload`

## <a name="next-steps"></a>Pasos siguientes  

* [Procedimientos recomendados de E/S directa de Linux para Azure NetApp Files](performance-linux-direct-io.md)
* [Procedimientos recomendados de caché del sistema de archivos de Linux para Azure NetApp Files](performance-linux-filesystem-cache.md)
* [Procedimientos recomendados de las opciones de montaje de NFS de Linux para Azure NetApp Files](performance-linux-mount-options.md)
* [Procedimientos recomendados de simultaneidad de Linux](performance-linux-concurrency-session-slots.md)
* [Procedimientos recomendados de SKU de máquinas virtuales de Azure](performance-virtual-machine-sku.md) 
* [Bancos de pruebas de rendimiento para Linux](performance-benchmarks-linux.md) 
