---
title: Supervisión de SAP HANA en Azure (instancias grandes) | Microsoft Docs
description: Obtenga información sobre la supervisión de SAP HANA en Azure (instancias grandes).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/23/2021
ms.author: madhukan
ms.custom:
- H1Hack27Feb2017
- contperf-fy21q4
ms.openlocfilehash: 3c77fe23b7ce058f31a216c5991b7129c3ac99fe
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112579835"
---
# <a name="monitor-sap-hana-large-instances-on-azure"></a>Supervisión de SAP HANA (instancias grandes) en Azure

En este artículo, se describirá la supervisión de SAP HANA (instancias grandes) en Azure (también conocida como Infraestructura BareMetal).

SAP HANA en Azure (instancias grandes) no es diferente de otras implementación de IaaS. Es importante supervisar el sistema operativo y la aplicación. Le interesará saber cómo se consumen los siguientes recursos en las aplicaciones:

- CPU
- Memoria
- Ancho de banda de red
- Espacio en disco

Supervise SAP HANA (instancias grandes) para ver si los recursos anteriores son suficientes o si se están agotando. En las secciones siguientes se proporciona más información sobre cada uno de estos recursos.

## <a name="cpu-resource-consumption"></a>Consumo de recursos de CPU

SAP define un umbral máximo de uso de CPU para la carga de trabajo de SAP HANA. Permanecer dentro de este umbral garantiza que tiene suficientes recursos de CPU para trabajar con los datos almacenados en memoria. Se puede producir un consumo elevado de CPU cuando los servicios de SAP HANA ejecutan consultas debido a índices que faltan o problemas similares. Por tanto, es fundamental supervisar el consumo de las instancias grandes de HANA y el consumo de CPU de servicios específicos de HANA.

## <a name="memory-consumption"></a>Consumo de memoria 

Es importante supervisar el consumo de memoria dentro de y fuera de HANA en SAP HANA (instancias grandes). Supervise cómo los datos consumen memoria asignada por HANA para que pueda mantenerse dentro de las instrucciones de tamaño de SAP. Supervise el consumo de memoria en la instancia grande para asegurarse de que el software que no es de HANA no consuma demasiada memoria. No quiere que el software que no sea de HANA compita con HANA para usar la memoria.

## <a name="network-bandwidth"></a>Ancho de banda de red 

El ancho de banda de la puerta de enlace de Azure Virtual Network (VNet) es limitado. Solo se puede mover una cantidad de datos concreta a la red virtual de Azure. Supervise los datos recibidos por todas las máquinas virtuales de Azure dentro de una red virtual. De este modo, sabrá cuándo se acerca a los límites de la SKU de puerta de enlace de Azure que ha seleccionado. También es recomendable supervisar el tráfico de red entrante y saliente en la instancia grande de HANA para realizar el seguimiento de los volúmenes controlados en el tiempo.

## <a name="disk-space"></a>Espacio en disco

normalmente su uso aumenta a lo largo del tiempo. Las causas más comunes son:
- El volumen de datos aumenta con el tiempo
- Ejecución de copias de seguridad del registro de transacciones
- Almacenamiento de archivos de seguimiento
- Realización de instantáneas de almacenamiento 

Por tanto, es importante supervisar el uso del espacio en disco y administrar el espacio en disco asociado a HANA (instancias grandes).

## <a name="preloaded-system-diagnostic-tools"></a>Herramientas de diagnóstico del sistema precargadas

En las **SKU de tipo II** de las instancias grandes de HANA, el servidor viene ya con las herramientas de diagnóstico del sistema cargadas. Puede usar estas herramientas de diagnóstico para realizar la comprobación de estado del sistema.
 
Ejecute el comando siguiente para generar el archivo de registro de comprobación de estado en /var/log/health_check.

```
/opt/sgi/health_check/microsoft_tdi.sh
```
Cuando trabaje con el equipo de Soporte técnico de Microsoft para solucionar un problema, es posible que se le pida que proporcione los archivos de registro mediante estas herramientas de diagnóstico. Puede comprimir el archivo con este comando:

```
tar  -czvf health_check_logs.tar.gz /var/log/health_check
```

## <a name="azure-monitor-for-sap-solutions"></a>Azure Monitor para soluciones de SAP

Puede usar Azure Monitor para soluciones de SAP a fin de supervisar todos los recursos enumerados anteriormente y muchos más. Azure Monitor para soluciones de SAP es nativo de Azure. Permite recopilar datos de la infraestructura y las bases de datos de Azure en una sola ubicación y poner en correlación visualmente los datos para acelerar la solución de problemas. Para obtener más información, vea [Azure Monitor para soluciones de SAP](../../../virtual-machines/workloads/sap/azure-monitor-overview.md).

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo supervisar y solucionar problemas desde dentro de SAP HANA.

> [!div class="nextstepaction"]
> [Supervisión y solución de problemas en el lado de HANA](hana-monitor-troubleshoot.md)
