---
title: Métricas de disco
description: Ejemplos de métricas de expansión de disco
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 0d0330996629ec087022e592cc862a428bc02807
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114454585"
---
# <a name="disk-performance-metrics"></a>Métricas de rendimiento de discos
Azure dispone de métricas en Azure Portal que proporcionan información sobre el funcionamiento de las máquinas virtuales (VM) y los discos. Estas métricas también se pueden recuperar con una llamada API. Este artículo está estructurado en tres secciones:

- **Métricas de entrada y salida de disco, rendimiento y profundidad de cola**: estas métricas le permiten ver el rendimiento del almacenamiento desde la perspectiva de un disco y una máquina virtual.
- **Métricas de expansión de disco**: estas métricas le permiten analizar la característica de [expansión](disk-bursting.md) de los discos prémium.
- **Métricas de uso de entrada y salida del almacenamiento**: estas métricas ayudan a diagnosticar cuellos de botella en el rendimiento del almacenamiento en los discos. 

Todas las métricas se emiten cada minuto, excepto la métrica de porcentaje de crédito de expansión, que se emite cada 5 minutos.

## <a name="disk-io-throughput-and-queue-depth-metrics"></a>Métricas de entrada y salida, de rendimiento y de profundidad de cola
Las siguientes métricas permiten extraer conclusiones sobre las operaciones de entrada y salida, el rendimiento y la profundidad de cola de la máquina virtual y el disco:

- **Profundidad de la cola del disco del SO**: el número actual de solicitudes de E/S pendientes que esperan a que se lean desde el SO o se escriban en este.
- **Bytes de lectura de disco de SO por segundo**: el número de bytes que se leen en un segundo desde el disco del sistema operativo.
- **Operaciones de lectura en discos de SO por segundo**: el número de operaciones de entrada que se leen en un segundo desde el disco del sistema operativo.
- **Bytes de escritura en disco del SO por segundo**: el número de bytes que se escriben en un segundo desde el disco del sistema operativo.
- **Operaciones de escritura en disco de sistema operativo por segundo**: el número de operaciones de salida que se escriben en un segundo desde el disco del sistema operativo.
- **Profundidad de la cola de disco de datos**: el número actual de solicitudes de E/S pendientes que esperan a que se lean desde los discos de datos o se escriban en estos.
- **Bytes de lectura de discos de datos por segundo**: el número de bytes que se leen en un segundo desde los discos de datos.
- **Operaciones de lectura de disco de datos por segundo**: el número de operaciones de entrada que se leen en un segundo desde los discos de datos.
- **Bytes de escritura de discos de datos por segundo**: el número de bytes que se escriben en un segundo desde el disco de datos.
- **Operaciones de escritura de discos de datos por segundo**: el número de operaciones de salida que se escriben en un segundo desde los discos de datos.
- **Bytes de lectura de disco por segundo**: el número de bytes totales que se leen en un segundo desde los discos conectados a una máquina virtual.
- **Operaciones de lectura de disco por segundo**: el número de operaciones de entrada que se leen en un segundo desde todos los discos conectados a una máquina virtual.
- **Bytes de escritura en disco por segundo**: el número de bytes que se escriben en un segundo desde todos los discos conectados a una máquina virtual.
- **Operaciones de escritura por segundo en disco**: el número de operaciones de salida que se escriben en un segundo desde todos los discos conectados a una máquina virtual.

## <a name="bursting-metrics"></a>Métricas de expansión
Las siguientes métricas mejoran la observabilidad de la característica de [expansión](disk-bursting.md) de los discos prémium:

- **Ancho de banda de expansión máxima del disco de datos**: límite de rendimiento máximo hasta el que se pueden expandir los discos de datos.
- **Ancho de banda de expansión máxima del disco del sistema operativo**: límite de rendimiento máximo hasta el que se puede expandir el disco del sistema operativo.
- **IOPS de expansión máxima del disco de datos**: límite de IOPS hasta el que se pueden expandir los discos de datos.
- **IOPS de expansión máxima del disco del sistema operativo**: límite de IOPS hasta el que se puede expandir el disco del sistema operativo.
- **Ancho de banda de destino del disco de datos**: límite de rendimiento que el disco de datos puede alcanzar sin expandirse.
- **Ancho de banda de destino del disco del sistema operativo**: límite de rendimiento que el disco del sistema operativo puede alcanzar sin expandirse.
- **IOPS de destino del disco de datos**: límite de IOPS que el disco de datos puede alcanzar sin expandirse.
- **IOPS de destino del disco del sistema operativo**: límite de IOPS que el disco de datos puede alcanzar sin expandirse.
- **Porcentaje de créditos de BPS de expansión utilizados en el disco de datos**: porcentaje acumulado de expansión del rendimiento utilizado en los discos de datos. Se emite en intervalos de 5 minutos.
- **Porcentaje de créditos de BPS de expansión utilizados en el disco del sistema operativo**: porcentaje acumulado de expansión del rendimiento utilizado en el disco del sistema operativo. Se emite en intervalos de 5 minutos.
- **Porcentaje de créditos de entrada y salida de expansión utilizados en el disco del sistema operativo**: porcentaje acumulado de expansión de IOPS utilizado en el disco de datos. Se emite en intervalos de 5 minutos.
- **Porcentaje de créditos de entrada y salida de expansión utilizados en el disco del sistema operativo**: porcentaje acumulado de expansión de IOPS utilizado en el disco del sistema operativo. Se emite en intervalos de 5 minutos.

## <a name="storage-io-utilization-metrics"></a>Métricas de uso de E/S de almacenamiento
Las siguientes métricas ayudan a diagnosticar cuellos de botella en la combinación de discos y máquinas virtuales. Estas métricas solo están disponibles con la configuración siguiente:
- Solo está disponible en las series de máquinas virtuales que admiten Premium Storage.
- No disponible para discos Ultra, todos los demás tipos de disco de estas series de máquinas virtuales pueden usar estas métricas.

Métricas que ayudan a diagnosticar el límite de E/S de disco:

- **Porcentaje de consumo de IOPS de disco de datos**: el porcentaje calculado por la IOPS de disco de datos completada con respecto a la IOPS del disco de datos aprovisionado. Si esta cantidad es del 100 %, se restringe la E/S de la aplicación en ejecución respecto al límite de IOPS del disco de datos.
- **Porcentaje de ancho de banda consumido del disco de datos**: el porcentaje calculado por el rendimiento de disco de datos completado con respecto al rendimiento del disco de datos aprovisionado. Si esta cantidad es del 100 %, se restringe la E/S de la aplicación en ejecución respecto al límite de ancho de banda del disco de datos.
- **Porcentaje de consumo de IOPS de disco del sistema operativo**: el porcentaje calculado por la IOPS de disco del sistema operativo completada con respecto a la IOPS del sistema operativo aprovisionado. Si esta cantidad es del 100 %, se restringe la E/S de la aplicación en ejecución respecto al límite de IOPS del sistema operativo.
- **Porcentaje de ancho de banda consumido del sistema operativo**: el porcentaje calculado por el rendimiento del sistema operativo completado con respecto al rendimiento del sistema operativo aprovisionado. Si esta cantidad es del 100 %, se restringe la E/S de la aplicación en ejecución respecto al límite de ancho de banda del sistema operativo.

Métricas que ayudan a diagnosticar el límite de E/S de máquinas virtuales:

- **Porcentaje de consumo de IOPS en caché de la máquina virtual**: el porcentaje calculado por la IOPS total completado con respecto al límite máximo de IOPS de máquinas virtuales en caché. Si esta cantidad es del 100 %, se restringe la E/S de la aplicación en ejecución con respecto al límite de IOPS de la máquina virtual en caché.
- **Porcentaje de consumo de ancho de banda en caché de máquinas virtuales**: el porcentaje calculado por el rendimiento total de disco completado en el límite máximo del rendimiento de las máquinas virtuales almacenadas en caché. Si esta cantidad es del 100 %, se restringe la E/S de la aplicación en ejecución respecto al límite de ancho de banda del ancho de banda de la máquina virtual en caché.
- **Porcentaje de consumo de IOPS que no se encuentra almacenado en caché de máquinas virtuales**: el porcentaje calculado por la IOPS total de una máquina virtual completado en el límite máximo de IOPS de máquinas virtuales que no se encuentran almacenadas en caché. Si esta cantidad es del 100 %, se restringe la E/S de la aplicación en ejecución con respecto al límite de IOPS de la máquina virtual que no se encuentra almacenada en caché.
- **Porcentaje de consumo de ancho de banda que no está almacenado en caché de máquinas virtuales**: el porcentaje calculado por el rendimiento total de disco de una máquina virtual completado en el límite máximo del rendimiento de las máquinas virtuales aprovisionadas. Si esta cantidad es del 100 %, se restringe la E/S de la aplicación en ejecución respecto al límite de ancho de banda del ancho de banda de la máquina virtual que no está en caché.

## <a name="storage-io-metrics-example"></a>Ejemplo de métricas de entrada y salida de almacenamiento

Veamos un ejemplo de cómo utilizar estas nuevas métricas de uso de E/S de almacenamiento para depurar la ubicación de un cuello de botella en nuestro sistema. La configuración del sistema es la misma que la del ejemplo anterior, con la excepción de que el disco del sistema operativo conectado *no* se almacena en caché.

**Configuración:**

- Standard_D8s_v3
  - IOPS en caché: 16 000
  - IOPS no almacenadas en caché: 12.800
- Disco de SO P30
  - IOPS: 5.000
  - Almacenamiento en caché de host: **Disabled** (Deshabilitado)
- Dos discos de datos P30 × 2
  - IOPS: 5.000
  - Almacenamiento en caché de host: **Lectura/escritura**
- Dos discos de datos P30 × 2
  - IOPS: 5.000
  - Almacenamiento en caché de host: **Disabled** (Deshabilitado)

Se ejecutará una prueba comparativa en esta combinación de máquina virtual y disco que crea la actividad de E/S. Para obtener información sobre cómo realizar pruebas comparativas de E/S de almacenamiento en Azure, vea [Pruebas comparativas de la aplicación en Azure Disk Storage](disks-benchmarks.md). Desde la herramienta de pruebas comparativas, puede ver que la combinación de discos y máquinas virtuales es capaz de lograr 22 800 IOPS:

![Captura de pantalla de la salida de FIO que muestra r=22.8k resaltado.](media/disks-metrics/utilization-metrics-example/fio-output.jpg)



Standard_D8s_v3 puede alcanzar un total de 28 600 IOPS. Con las métricas, vamos a investigar lo que está ocurriendo e identificar nuestro cuello de botella de E/S de almacenamiento. En el panel izquierdo, seleccione **Métricas**:

![Captura de pantalla que muestra la opción Métricas resaltada en el panel izquierdo.](media/disks-metrics/utilization-metrics-example/metrics-menu.jpg)

Primero de todo, echemos un vistazo a nuestra métrica del **porcentaje de consumo de IOPS en caché de la máquina virtual**:

![Captura de pantalla que muestra el porcentaje consumido de IOPS en caché de la VM.](media/disks-metrics/utilization-metrics-example/vm-cached.jpg)

Esta métrica nos indica que se está usando el 61 % de los 16 000 IOPS asignados a los IOPS almacenados en la memoria caché de la máquina virtual. Este porcentaje significa que el cuello de botella de E/S de almacenamiento no se produce con los discos que están almacenados en la memoria caché, porque no está al 100 %. Ahora echemos un vistazo a nuestra métrica del **porcentaje de consumo de IOPS que no están almacenados en el caché de la máquina virtual**:

![Captura de pantalla que muestra el porcentaje consumido de IOPS no almacenados en la memoria caché de la VM.](media/disks-metrics/utilization-metrics-example/vm-uncached.jpg)

Esta métrica está al 100 %. Esto indica que se están utilizando los 12 800 IOPS asignados a los IOPS no almacenados en caché de la máquina virtual. Una manera de corregir esto es cambiar el tamaño de la máquina virtual a un tamaño mayor que pueda administrar la E/S adicional. Pero antes de hacerlo, echemos un vistazo al disco conectado para averiguar cuántos IOPS están viendo. Comprobemos el disco del sistema operativo examinando el **porcentaje de consumo de IOPS de disco del sistema operativo**:

![Captura de pantalla que muestra el porcentaje consumido de IOPS del disco del sistema operativo.](media/disks-metrics/utilization-metrics-example/os-disk.jpg)

Esta métrica nos indica que se está usando aproximadamente el 90 % de los 5000 IOPS aprovisionados para este disco del sistema operativo P30. Este porcentaje significa que no hay cuellos de botella en el disco del sistema operativo. Ahora, echemos un vistazo a los discos de datos adjuntos a la máquina virtual examinando el **porcentaje de consumo de IOPS del disco de datos**:

![Captura de pantalla que muestra el porcentaje consumido de IOPS del disco de datos.](media/disks-metrics/utilization-metrics-example/data-disks-no-splitting.jpg)

Esta métrica nos indica que el porcentaje de consumo promedio de IOPS en todos los discos adjuntos es aproximadamente del 42 %. Este porcentaje se calcula en función de las IOPS que se utilizan en los discos y que no se atienden desde la memoria caché del host. Profundicemos en esta métrica aplicando la *división* en estas métricas y la división por el valor del LUN:

![Captura de pantalla que muestra el porcentaje consumido de los IOPS del disco de datos con división.](media/disks-metrics/utilization-metrics-example/data-disks-splitting.jpg)

Esta métrica nos indica que los discos de datos adjuntos a los LUN 3 y 2 utilizan alrededor del 85 % de los IOPS aprovisionados. Este es un diagrama del aspecto de la E/S de la arquitectura de discos y máquinas virtuales:

![Diagrama del ejemplo de métricas de E/S de almacenamiento.](media/disks-metrics/utilization-metrics-example/metrics-diagram.jpg)

## <a name="next-steps"></a>Pasos siguientes

- [Información general sobre las métricas en Microsoft Azure](../azure-monitor/essentials/data-platform-metrics.md)
- [Explicación de la agregación de métricas](../azure-monitor/essentials/metrics-aggregation-explained.md)
- [Creación, visualización y administración de alertas de métricas mediante Azure Monitor](../azure-monitor/alerts/alerts-metric.md)