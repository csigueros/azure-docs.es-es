---
title: Disponibilidad de SAP HANA entre regiones de Azure | Microsoft Docs
description: Información general de las consideraciones sobre disponibilidad que tener en cuenta cuando se ejecuta SAP HANA en máquinas virtuales de Azure en varias regiones de Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8bf4f1fbb5d984b052a9fdb84ed9d26da18db2f9
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114445819"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Disponibilidad de SAP HANA entre regiones de Azure

En este artículo se describen escenarios relacionados con la disponibilidad de SAP HANA en diferentes regiones de Azure. Debido a la distancia entre las regiones de Azure, configurar la disponibilidad de SAP HANA en varias regiones de Azure implica consideraciones especiales.

## <a name="why-deploy-across-multiple-azure-regions"></a>Motivos para la implementación en varias regiones de Azure

A menudo, las regiones de Azure están separadas por grandes distancias. Dependiendo de la región geopolítica, la distancia entre regiones de Azure puede ser de cientos de kilómetros o incluso de varios miles de kilómetros, como en Estados Unidos. Debido a la distancia, el tráfico de red entre recursos implementados en dos regiones de Azure diferentes tiene una latencia de ida y vuelta de red importante. La latencia es suficientemente importante como para excluir el intercambio sincrónico de datos entre dos instancias de SAP HANA en cargas de trabajo típicas de SAP. 

Por otro lado, las organizaciones suelen tener un requisito de distancia entre la ubicación del centro de datos principal y un centro de datos secundario. Un requisito de distancia ayuda a proporcionar disponibilidad si se produce un desastre natural en una ubicación geográfica más amplia. Por ejemplo, los huracanes que castigaron el área del Caribe y Florida en septiembre y octubre de 2017. Su organización podría tener al menos un requisito de distancia mínima. Para la mayoría de los clientes de Azure, la definición de una distancia mínima obliga a diseñar una disponibilidad que comprenda varias [regiones de Azure](https://azure.microsoft.com/regions/). Puesto que la distancia entre dos regiones de Azure es demasiado grande para usar el modo de replicación sincrónica de HANA, los requisitos de RTO y el RPO podrían obligarle a implementar configuraciones de disponibilidad dentro de una región, y complementarlas con implementaciones adicionales en una segunda región.

Otro aspecto que debe tenerse en cuenta en este escenario es la conmutación por error y la redirección de cliente. Se supone que una conmutación por error entre instancias de SAP HANA en dos regiones diferentes de Azure es siempre una conmutación por error manual. Puesto que la replicación del sistema de SAP HANA tiene el modo de replicación establecido en asincrónico, es posible que haya datos confirmados en la instancia principal de HANA que no hayan llegado aún a la instancia secundaria de HANA. Por lo tanto, la conmutación por error automática no es una opción para configuraciones en las que la replicación es asincrónica. Incluso con una conmutación por error controlada manualmente, como en un ejercicio de conmutación por error, es necesario tomar medidas para asegurarse de que todos los datos confirmados en el lado principal hayan llegado a la instancia secundaria, antes de pasar manualmente a la otra región de Azure.
 
Azure Virtual Network usa un intervalo de direcciones IP distinto. Las direcciones IP se implementan en la segunda región de Azure. Por lo tanto, tendrá que cambiar la configuración del cliente de SAP HANA o, preferiblemente, deberá crear pasos para cambiar la resolución de nombres. De esta manera, los clientes se redirigen a la nueva dirección IP del servidor del sitio secundario. Para más información, consulte el artículo de SAP sobre [recuperación de la conexión de cliente tras una adquisición](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Disponibilidad simple entre dos regiones de Azure

Puede decidir no contar con ninguna configuración de disponibilidad dentro de una única región, pero seguir teniendo la necesidad de tener la carga de trabajo atendida en caso de un desastre. Los sistemas que no son de producción son un caso clásico de este tipo. Aunque pueda ser admisible que el sistema permanezca inactivo durante medio día o incluso un día entero, no puede permitir que no esté disponible durante 48 horas o más. Para que la configuración sea menos costosa, ejecute otro sistema que sea aún menos importante en la máquina virtual. El otro sistema funciona como destino. También puede reducir el tamaño de la máquina virtual en la región secundaria y decidir no cargar previamente datos. Dado que la conmutación por error es manual y conlleva muchos pasos más para realizar también la conmutación por error de la pila de la aplicación completa, el tiempo adicional para apagar la VM, cambiar su tamaño y reiniciarla es aceptable.

Si usa el escenario de compartir el destino de recuperación ante desastres con un sistema de control de calidad en una VM, es preciso tener en cuenta estas consideraciones:

- Hay dos [modos de operación](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) con delta_datashipping y logreplay, que están disponibles para este escenario
- Ambos modos de operación tienen requisitos de memoria diferentes sin usar datos precargados
- Delta_datashipping podría requerir significativamente menos memoria sin la opción de precarga de lo que podría requerir logreplay. Consulte el capítulo 4.3 del documento de SAP [How To Perform System Replication for SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf) (Cómo realizar la replicación del sistema para SAP HANA).
- El requisito de memoria del modo de operación logreplay sin precarga no es determinista y depende de las estructuras cargadas del almacén de columnas. En casos extremos, es posible que necesite el 50 % de la memoria de la instancia principal. La memoria para el modo de operación logreplay es independiente de si elige establecer la carga previa de datos o no.


![Diagrama de dos máquinas virtuales en dos regiones](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> En esta configuración, no puede proporcionar un RPO = 0, dado que el modo de replicación del sistema de HANA es asincrónico. Si tiene que proporcionar un RPO = 0, esta configuración no es una opción.

Un pequeño cambio que puede hacerse en la configuración sería configurar datos como carga previa. De todas formas, dada la naturaleza manual de la conmutación por error y el hecho de que los niveles de aplicación tengan que moverse también a la segunda región, puede que la carga previa de los datos no tenga sentido. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Combinación de disponibilidad dentro de una región y entre regiones 

Una combinación de disponibilidad dentro de una región y entre regiones puede estar determinada por estos factores:

- Requisito de RPO = 0 dentro de una región de Azure.
- La organización no desea o no puede permitir que las operaciones globales se vean afectadas por una catástrofe natural importante que afecte a una región más grande. Este ha sido el caso de algunos huracanes cuyos efectos se han dejado sentir en el Caribe en los últimos años.
- Normativa que demanda distancias entre los sitios principal y secundario que se encuentran claramente por encima de las que las zonas de disponibilidad de Azure pueden proporcionar.

En tales casos, puede configurar lo que en SAP se llama una [SAP HANA multitier system replication configuration](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) (Replicación del sistema de varios niveles de SAP HANA) mediante la replicación del sistema de HANA. La arquitectura tendría este aspecto:

![Diagrama de tres máquinas virtuales en dos regiones](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

SAP introdujo la [replicación del sistema en varios destinos](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) con HANA 2.0 SPS3. La replicación del sistema en varios destinos aporta algunas ventajas en escenarios de actualización. Por ejemplo, el sitio de recuperación ante desastres (región 2) no se ve afectado cuando el sitio secundario de alta disponibilidad está inactivo por mantenimiento o actualizaciones. Puede encontrar más información acerca de la replicación del sistema en varios destinos de HANA en el [portal de ayuda de SAP](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Una arquitectura posible con la replicación en varios destinos tendría el siguiente aspecto:

![Diagrama de tres máquinas virtuales en dos regiones de varios destinos](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Si la organización tiene requisitos para la preparación de la alta disponibilidad en la segunda región de recuperación ante desastres de Azure, la arquitectura sería como la del siguiente ejemplo:

![Diagrama que muestra una organización que tiene requisitos para la preparación de la alta disponibilidad en la segunda región de recuperación ante desastres de Azure.](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


Con logreplay como modo de operación, esta configuración proporciona un RPO = 0, con un RTO bajo, dentro de la región principal. La configuración también proporciona un RPO aceptable en caso de que se necesite un traslado a la segunda región. Los tiempos de RTO en la segunda región dependen de si los datos se cargan previamente. Muchos clientes usan la máquina virtual en la región secundaria para ejecutar un sistema de prueba. En ese caso de uso, los datos no se pueden cargar previamente.

> [!IMPORTANT]
> Los modos de operación entre los diferentes niveles de deben ser homogéneos. **No** puede usar logreply como modo de operación entre el nivel 1 y el nivel 2, y delta_datashipping para proporcionar el nivel 3. Solo puede elegir entre un modo de operación u otro que deba ser coherente para todos los niveles. Puesto que delta_datashipping no es adecuado para ofrecerle un RPO=0, el único modo de operación razonable para este tipo de configuración de varios niveles sigue siendo logreplay. Para consultar detalles de los modos de operación y algunas restricciones, vea el artículo de SAP [Operation modes for SAP HANA system replication](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) (Modos de operación para la replicación de sistema de SAP HANA). 

## <a name="next-steps"></a>Pasos siguientes

Para obtener instrucciones paso a paso sobre cómo configurar estas configuraciones en Azure, vea:

- [Configuración de la replicación del sistema de SAP HANA en máquinas virtuales de Azure](sap-hana-high-availability.md)
- [High Availability for SAP HANA using System Replication](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/) (Alta disponibilidad en SAP HANA mediante la replicación del sistema)

 



 
  
