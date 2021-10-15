---
title: 'Banco de pruebas de rendimiento de pruebas recomendadas: Azure NetApp Files'
description: Obtenga información sobre las recomendaciones de pruebas comparativas de rendimiento y métricas de volumen mediante Azure NetApp Files.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 09/29/2021
ms.openlocfilehash: e5d5104e0f2215e81b5539296ff18572375a8a28
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362401"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Banco de pruebas de rendimiento de recomendaciones de pruebas para Azure NetApp Files

En este artículo se entregan recomendaciones de pruebas comparativas de rendimiento y métricas de volumen mediante Azure NetApp Files.

## <a name="overview"></a>Información general

Para entender las características de rendimiento de un volumen de Azure NetApp Files, puede usar la herramienta de código abierto [FIO](https://github.com/axboe/fio) para ejecutar una serie de pruebas comparativas y simular diversas cargas de trabajo. FIO se puede instalar en los sistemas operativos Linux y Windows.  Es una herramienta excelente para obtener una instantánea rápida tanto del IOPS como del rendimiento de un volumen.

Azure NetApp Files *no* recomienda usar la utilidad `dd` como herramienta de pruebas comparativas de línea de base. Debe usar una carga de trabajo de aplicación real, una simulación de carga de trabajo y herramientas de pruebas comparativas y análisis (por ejemplo, Oracle AWR con Oracle o el equivalente de IBM para DB2) para establecer y analizar un rendimiento óptimo de la infraestructura. Herramientas como FIO, vdbench e iometer tienen su lugar en la determinación de las máquinas virtuales de los límites de almacenamiento, haciendo coincidir los parámetros de la prueba con las mezclas reales de cargas de trabajo de la aplicación para obtener resultados más útiles. Sin embargo, siempre es mejor probar con la aplicación del mundo real.  

### <a name="vm-instance-sizing"></a>Tamaño de instancia de la máquina virtual

Para obtener mejores resultados, asegúrese de usar una instancia de máquina virtual (VM) que tiene el tamaño adecuado para realizar las pruebas. En los ejemplos siguientes se usa una instancia Standard_D32s_v3. Para más información sobre los tamaños de las instancias de la máquina virtual, consulte [Tamaños de las máquinas virtuales Windows en Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) para las máquinas virtuales basadas en Windows y [Tamaños de las máquinas virtuales Linux en Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) para las máquinas virtuales basadas en Linux.

### <a name="azure-netapp-files-volume-sizing"></a>Ajuste del tamaño en Azure NetApp Files

Asegúrese de elegir el tamaño de cuota del volumen y el nivel de servicio correctos del nivel de rendimiento esperado. Para más información, consulte [Niveles de servicio para Azure NetApp Files](azure-netapp-files-service-levels.md).

### <a name="virtual-network-vnet-recommendations"></a>Recomendaciones de red virtual (VNet)

Debe realizar las pruebas comparativas en la misma red virtual en que se encuentra Azure NetApp Files. En el ejemplo siguiente se muestra la recomendación:

![Recomendaciones de red virtual](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Instalación de FIO

FIO está disponible en formato binario para Linux y Windows. Siga las instrucciones que aparecen en la sección sobre paquetes binarios en [FIO](https://github.com/axboe/fio) para instalar la versión para la plataforma que prefiera.

## <a name="fio-examples-for-iops"></a>Ejemplos de FIO para IOPS 

Los ejemplos de FIO de esta sección usan la configuración siguiente:
* Tamaño de instancia de máquina virtual: D32s_v3
* Tamaño y nivel de servicio del grupo de capacidad: Premium / 50 TiB
* Tamaño de cuota del volumen: 48 TiB

En los ejemplos siguientes se muestran las lecturas y escrituras aleatorias de FIO.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: lecturas 100 % aleatorias con tamaño de bloque de 8 k

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Salida: se muestra IOPS de lectura de 68 k

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: escrituras 100 % aleatorias con tamaño de bloque de 8 k

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Salida: se muestra IOPS de escritura de 73 k

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>Ejemplos de FIO para el ancho de banda

Los ejemplos de esta sección muestran las lecturas y escrituras secuenciales de FIO.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: lecturas 100 % secuenciales con tamaño de bloque de 64 k

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Salida: se muestra un rendimiento de 11,8 Gbit/s

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: escrituras 100 % secuenciales con tamaño de bloque de 64 k

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Salida: se muestra un rendimiento de 12,2 Gbit/s

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Métricas de volumen

Los datos de rendimiento de Azure NetApp Files están disponibles a través de los contadores de Azure Monitor. Los contadores está disponibles mediante Azure Portal y las solicitudes GET de API REST. 

Puede ver los datos históricos para obtener la información siguiente:
* Latencia de lectura media 
* Latencia de escritura media 
* IOPS de lectura (promedio)
* IOPS de escritura (promedio)
* Tamaño lógico del volumen (promedio)
* Tamaño de la instantánea de volumen (promedio)

### <a name="using-azure-monitor"></a>Uso de Azure Monitor 

Puede acceder a los contadores de Azure NetApp Files por cada volumen desde la página de métricas, como se muestra a continuación:

![Métricas de Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

También puede crear un panel en Azure Monitor para Azure NetApp Files si va a la página Métricas, filtra por NetApp y especifica los contadores de volumen que le interesan: 

![Panel de Azure Monitor](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Acceso a la API Azure Monitor

Puede acceder a los contadores de Azure NetApp Files si usa las llamadas de API REST. Consulte [Métricas compatibles con Azure Monitor: Microsoft.NetApp/netAppAccounts/capacityPools/Volumes](../azure-monitor/essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) para obtener los contadores para los volúmenes y los grupos de capacidad.

En el ejemplo siguiente se muestra una dirección URL de GET para ver el tamaño del volumen lógico:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Pasos siguientes

- [Niveles de servicio para Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Bancos de pruebas de rendimiento para Linux](performance-benchmarks-linux.md)
