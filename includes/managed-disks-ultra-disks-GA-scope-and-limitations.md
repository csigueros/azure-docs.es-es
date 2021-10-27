---
title: Archivo de inclusión
description: Archivo de inclusión
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 08/17/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 0e0d3c131f5cd53684580d0035c8836a2537d780
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130070836"
---
Las únicas opciones de redundancia de infraestructura disponibles actualmente para Ultra Disks son las zonas de disponibilidad. Las máquinas virtuales que usan cualquier otra opción de redundancia no pueden conectar discos Ultra Disks.

En la tabla siguiente se describen las regiones en las que los discos Ultra están disponibles, así como las opciones de disponibilidad correspondientes.

> [!NOTE]
> Si una región de la lista siguiente no tiene ninguna zona de disponibilidad compatible con discos Ultra, una máquina virtual de esa región debe implementarse sin redundancia de infraestructura para poder conectar un disco Ultra.

| Opciones de redundancia | Regions |
|--------------------|---------|
| **Máquinas virtuales únicas** | Centro de Australia<br/>Sur de Brasil<br/>Centro de la India<br/>Este de Asia<br/>Centro-oeste de Alemania<br/>Centro de Corea del Sur<br/>Centro-norte de EE. UU., Centro-sur de EE. UU. y Oeste de EE. UU.<br/>US Gov Arizona, US Gov Texas, US Gov Virginia<br/> |
| **Dos zonas de disponibilidad** | Este de Australia<br/>Centro de Canadá<br/>Norte de Europa y Oeste de Europa<br/>Japón Oriental<br/>Sudeste de Asia<br/>Sur de Reino Unido 2<br/>Centro de EE. UU., Este de EE. UU., Este de EE. UU. 2, Oeste de EE. UU. 2|
| **Tres zonas de disponibilidad** | Centro de Francia |

No todos los tamaños de máquina virtual están disponibles en todas las regiones admitidas con discos Ultra. En la tabla siguiente se enumeran las series de máquinas virtuales que son compatibles con discos Ultra.

|Tipo de máquina virtual     |Tamaños    |Descripción  |
|------------|---------|-------------|
| Uso general|[Serie DSv3](../articles/virtual-machines/dv3-dsv3-series.md#dsv3-series), [serie Ddsv4](../articles/virtual-machines/ddv4-ddsv4-series.md#ddsv4-series), [serie Dsv4](../articles/virtual-machines/dv4-dsv4-series.md#dsv4-series), [serie Dasv4](../articles/virtual-machines/dav4-dasv4-series.md#dasv4-series)| Uso equilibrado de la CPU en proporción de memoria. Ideal para desarrollo y pruebas, bases de datos pequeñas o medianas, y servidores web de tráfico bajo o medio.|
| Proceso optimizado|[Serie FSv2](../articles/virtual-machines/fsv2-series.md)| Uso elevado de la CPU en proporción de memoria. Bueno para servidores web de tráfico medio, aplicaciones de red, procesos por lotes y servidores de aplicaciones.|
| Memoria optimizada|[Serie ESv3](../articles/virtual-machines/ev3-esv3-series.md#esv3-series), [serie Easv4](../articles/virtual-machines/eav4-easv4-series.md#easv4-series), [serie Edsv4](../articles/virtual-machines/edv4-edsv4-series.md#edsv4-series), [serie Esv4](../articles/virtual-machines/ev4-esv4-series.md#esv4-series), [serie M](../articles/virtual-machines/m-series.md), [serie Mv2](../articles/virtual-machines/mv2-series.md)|Memoria alta en proporción de CPU. Excelente para servidores de bases de datos relacionales, memorias caché de capacidad media o grande y análisis en memoria.
| Almacenamiento optimizado|[Serie LSv2](../articles/virtual-machines/lsv2-series.md)|Alto rendimiento de disco y de E/S ideales para macrodatos, bases de datos SQL y NoSQL, almacenamiento de datos y bases de datos transaccionales grandes.|
| GPU optimizada|[Serie NCv2](../articles/virtual-machines/ncv2-series.md), [serie NCv3](../articles/virtual-machines/ncv3-series.md), [serie NCasT4_v3](../articles/virtual-machines/nct4-v3-series.md), [serie ND](../articles/virtual-machines/nd-series.md), [serie NDv2](../articles/virtual-machines/ndv2-series.md), [serie NVv3](../articles/virtual-machines/nvv3-series.md), [serie NVv4](../articles/virtual-machines/nvv4-series.md)| Máquinas virtuales especializadas específicas para la representación de gráficos pesados y la edición de vídeo, así como para el entrenamiento e inferencia de modelos (ND) con aprendizaje profundo. Están disponibles con uno o varios GPU.|
| <nobr>Optimizados para rendimiento</nobr> |[Serie HB](../articles/virtual-machines/hb-series.md), [serie HC](../articles/virtual-machines/hc-series.md), [serie HBv2](../articles/virtual-machines/hbv2-series.md)|Las máquinas virtuales de CPU más rápidas y eficaces con interfaces de red de alto rendimiento (RDMA) opcionales.|

Los discos Ultra no se pueden usar con algunas características y funcionalidades, como instantáneas de disco, exportación de discos, cambio de tipo de disco, imágenes de máquina virtual, conjuntos de disponibilidad, instancias de Azure Dedicated Host o cifrado de discos de Azure. Azure Backup y Azure Site Recovery no admiten discos Ultra. Además, solo se admiten lecturas sin almacenar en caché y escrituras sin almacenar en caché.

Los discos Ultra admiten un tamaño de sector físico de 4 k de forma predeterminada. Se puede encontrar un tamaño de sector de 512E como oferta disponible con carácter general sin necesidad de registrarse. Si bien la mayoría de las aplicaciones son compatibles con los tamaños de sector de 4 k, algunas requieren tamaños de sector de 512 bytes. Oracle Database, por ejemplo, requiere la versión 12.2 o posterior para admitir los discos nativos de 4 k. En las versiones anteriores de Oracle DB, se requiere un tamaño de sector de 512 bytes.
