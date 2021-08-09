---
title: Ventajas de usar Azure NetApp Files para la implementación de SQL Server | Microsoft Docs
description: Muestra las ventajas en cuanto a rendimiento de un análisis detallado de los costos del uso de Azure NetApp Files para la implementación de SQL Server.
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
ms.date: 05/19/2021
ms.author: b-juche
ms.openlocfilehash: cecf3d0da1b987d7c389b8fe9ab8f0600fed5dba
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110190518"
---
#  <a name="benefits-of-using-azure-netapp-files-for-sql-server-deployment"></a>Ventajas de usar Azure NetApp Files para la implementación de SQL Server

Azure NetApp Files reduce el costo total de propiedad de SQL Server, en comparación con las soluciones de almacenamiento en bloque.  Con el almacenamiento en bloque, se han impuesto a las máquinas virtuales límites relativos a la E/S y al ancho de banda en las operaciones de disco. Solo se aplican límites de ancho de banda de red a Azure NetApp Files y únicamente en la salida.  En otras palabras, no se aplican límites de entrada/salida en el nivel de máquina virtual a Azure NetApp Files. Sin estos límites de entrada/salida, si SQL Server se ejecuta en máquinas virtuales más pequeñas conectadas a Azure NetApp Files, puede funcionar tan bien como si se ejecutara en máquinas virtuales mucho mayores. La reducción del tamaño de las instancias como tal reduce el costo de proceso al 25 % del precio anterior.  *Puede reducir los costos de proceso con Azure NetApp Files.*  

Sin embargo, los costos de proceso son pequeños en comparación con los costos de las licencias de SQL Server.  La [concesión de licencias](https://download.microsoft.com/download/B/C/0/BC0B2EA7-D99D-42FB-9439-2C56880CAFF4/SQL_Server_2017_Licensing_Datasheet.pdf) de Microsoft SQL Server está ligada al número de núcleos físicos. Por lo tanto, la reducción del tamaño de la instancia supone un ahorro de costos aún mayor para las licencias de software. *Con Azure NetApp Files puede reducir los costos de las licencias de software.*

En este artículo se muestra un análisis de costos detallado y las ventajas en cuanto a rendimiento de usar Azure NetApp Files para la implementación de SQL Server. Las instancias más pequeñas no solo tienen suficiente CPU para hacer trabajos de base de datos que solo eran posibles con bloques en instancias de mayor tamaño, sino que, *en muchos casos, el rendimiento de las instancias más pequeñas es mayor que el de sus homólogas mayores basadas en disco gracias a Azure NetApp Files.* 

## <a name="detailed-cost-analysis"></a>Análisis detallado del costo 

Los dos conjuntos de gráficos de esta sección muestran el ejemplo de costo total de propiedad.  Se han seleccionado el número y el tipo de discos administrados, el nivel de servicio de Azure NetApp Files y la capacidad de cada escenario para lograr la mejor relación entre rendimiento, capacidad y precio.  Cada gráfico se compone de máquinas agrupadas (por ejemplo, D16 con Azure NetApp Files, en comparación con D64 con disco administrado) y los precios se desglosan por cada tipo de máquina.  

El primer conjunto de gráficos muestra el costo general de la solución con un tamaño de base de datos de 1 TiB, y se comparan D16s_v4 con D64, D8 con D32 y D4 con D16. La IOPS proyectada para cada configuración se indica mediante una línea verde o amarilla, y corresponde al eje Y del lado derecho.

[ ![Gráfico que muestra el costo general de la solución con un tamaño de base de datos de 1 TIB. ](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-1-tib.png#lightbox)


El segundo conjunto de gráficos muestra el costo global si se usa una base de datos de 50 TiB. Las comparaciones son las mismas: D16 en comparación con Azure NetApp Files frente a D64 con bloque, por ejemplo. 

[ ![Gráfico que muestra el costo general si se usa una base de datos de 50 TIB. ](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png)](../media/azure-netapp-files/solution-sql-server-cost-50-tib.png#lightbox)
 
## <a name="performance-and-lots-of-it"></a>Mucho rendimiento  

Para obtener una reducción de costos significativa, se necesita un gran rendimiento (por ejemplo, las instancias más grandes del inventario general de Azure admiten 80 000 IOPS de disco). Un solo volumen de Azure NetApp Files puede lograr 80 000 IOPS de base de datos, e instancias como D16 pueden consumir lo mismo. El tamaño D16, que normalmente puede alcanzar 25 600 IOPS de disco, es un 25 % de D64.  D64s_v4 puede realizar 80 000 IOPS de disco y, como tal, es un excelente punto de comparación en el nivel superior.

D16s_v4 puede lograr que un volumen de Azure NetApp Files realice hasta 80 000 IOPS en una base de datos. Tal y como demostró la herramienta de pruebas comparativas SQL Storage Benchmark (SSB), la instancia de D16 logró una carga de trabajo un 125 % superior a la que se alcanza en el disco con la instancia de D64.  Para más información sobre la herramienta, consulte la sección sobre la [herramienta de pruebas SSB](#ssb-testing-tool).

Se ha utilizado un tamaño de espacio de trabajo de 1 TiB y una carga de trabajo de SQL Server de 80 % de lectura y un 20 % de actualización, se miden las funcionalidades de rendimiento de la mayoría de las instancias de la clase de instancia D; la mayoría de los casos, no todos, ya que las instancias de D2 y D64 se excluyeron de las pruebas. La primera se quedó fuera porque no admite las redes aceleradas y la última, porque es el punto de comparación. Vea el siguiente gráfico para conocer los límites de D4s_v4, D8s_v4, D16s_v4 y D32s_v4, respectivamente.  Las pruebas de almacenamiento en disco administrado no se muestran en el gráfico. Los valores de comparación se extraen directamente de la [tabla de límites de Azure Virtual Machines](../virtual-machines/dv3-dsv3-series.md) para el tipo de instancia de clase D.

Con Azure NetApp Files, cada una de las instancias de la clase D puede cumplir o superar las funcionalidades de rendimiento de disco de instancias dos veces más grandes.  *Con Azure NetApp Files puede reducir considerablemente los costos de las licencias de software.*  

* D4, con un 75 % de uso de la CPU, coincidía con las funcionalidades de disco de D16.  
    * D16 tiene la velocidad limitada a 25 600 IOPS de disco.  
* D8, con un 75 % de uso de la CPU, coincidía con las funcionalidades de disco de D32.  
    * D32 tiene la velocidad limitada a 51 200 IOPS de disco.  
* D16, con un 55 % de uso de la CPU, coincidía con las funcionalidades de disco de D64.  
    * D64 tiene la velocidad limitada a 80 000 IOPS de disco.  
* D32, con un 15 % de uso de la CPU, también coincidía con las funcionalidades de disco de D64.  
    * D64, como ya se ha indicado, tiene la velocidad limitada a 80 000 IOPS de disco.  

### <a name="s3b-cpu-limits-test--performance-versus-processing-power"></a>Prueba de límites de CPU de S3B: rendimiento frente a potencia de procesamiento

En el diagrama siguiente se resume la prueba de límites de CPU de S3B:

![Diagrama que muestra el porcentaje medio de la CPU para SQL Server de instancia única en Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-average-cpu.png)

La escalabilidad es solo una parte de la historia. La otra parte es la latencia.  Una cosa es que las máquinas virtuales más pequeñas tengan la capacidad de impulsar mayores velocidades de E/S, y otra es hacerlo con latencias bajas, como se muestra a continuación.  

* D4 realizó 26 000 IOPS en Azure NetApp Files con una latencia de 2,3 ms.  
* D8 realizó 51 000 IOPS en Azure NetApp Files con una latencia de 2,0 ms.  
* D16 realizó 88 000 IOPS en Azure NetApp Files con una latencia de 2,8 ms.
* D32 realizó 80 000 IOPS en Azure NetApp Files con una latencia de 2,4 ms.  

### <a name="s3b-per-instance-type-latency-results"></a>Resultados de latencia del tipo S3B por instancia

En el diagrama siguiente se muestra la latencia de SQL Server de una sola instancia en Azure NetApp Files:

![Diagrama que muestra la latencia de SQL Server de una sola instancia en Azure NetApp Files.](../media/azure-netapp-files/solution-sql-server-single-instance-latency.png)

## <a name="ssb-testing-tool"></a>Herramienta de prueba de SSB 
 
Por diseño, la herramienta de pruebas comparativas [TPC-E](http://www.tpc.org/tpce/) hace hincapié en el *proceso* en lugar del *almacenamiento*. Los resultados de las pruebas que se muestran en esta sección se basan en una herramienta de prueba de esfuerzo llamada SQL Storage Benchmark (SSB).  SQL Server SQL Storage Benchmark puede impulsar la ejecución de SQL a escala masiva en una base de datos SQL Server para simular una carga de trabajo de OLTP, algo parecido a lo que hace la [herramienta de pruebas comparativas SLOB2 Oracle](https://kevinclosson.net/slob/). 

La herramienta SSB genera una carga de trabajo controlada por SELECT y UPDATE que emite las instrucciones indicadas directamente a la base de datos de SQL Server que se ejecuta en la máquina virtual de Azure.  Para este proyecto, las cargas de trabajo de SSB han aumentado de 1 a 100 usuarios de SQL Server, con 10 o 12 puntos intermedios a 15 minutos por número de usuarios.  Todas las métricas de rendimiento de estas ejecuciones provienen del punto de vista del rendimiento y SSB se ejecutó tres veces por escenario. 

Las pruebas se configuraron como 80 % de la instrucción SELECT y un 20 % de UPDATE, por tanto, un 90 % de lectura aleatoria.  El tamaño de la propia base de datos, creada por SSB, era de 1000 GB. Consta de 15 tablas de usuario y cada una de estas tablas tiene 9 millones de filas y 8192 bytes por fila. 

SSB es una herramienta de código abierto.  Está disponible de forma gratuita en la página de [SQL Storage Benchmark de GitHub](https://github.com/NetApp/SQL_Storage_Benchmark.git).  


## <a name="in-summary"></a>En resumen  

Con Azure NetApp Files, se puede aumentar el rendimiento de SQL Server y, al mismo tiempo, reducir considerablemente el costo total de propiedad. 

## <a name="next-steps"></a>Pasos siguientes

* [Cree un volumen SMB para Azure NetApp Files](azure-netapp-files-create-volumes-smb.md) 
* [Arquitecturas de soluciones mediante Azure NetApp Files: SQL Server](azure-netapp-files-solution-architectures.md#sql-server) 

