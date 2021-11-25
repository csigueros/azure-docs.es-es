---
title: Grupos de selección de ubicación de proximidad de Azure para aplicaciones SAP | Microsoft Docs
description: Se describen los escenarios de implementación de SAP con grupos de selección de ubicación de proximidad de Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/14/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: bccbced3ce40856f858fa7e0028a27c1e1c59460
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484703"
---
# <a name="azure-proximity-placement-groups-for-optimal-network-latency-with-sap-applications"></a>Grupos de selección de ubicación de proximidad de Azure para una latencia de red óptima con aplicaciones SAP

> [!IMPORTANT]
> En noviembre de 2021, hemos realizado cambios significativos en la forma en que se deben usar los grupos con ubicación por proximidad con la carga de trabajo de SAP en implementaciones zonales. 

Las aplicaciones SAP basadas en la arquitectura SAP NetWeaver o SAP S/4HANA son sensibles a la latencia de red entre la capa de aplicación de SAP y el nivel de base de datos de SAP. Esta sensibilidad es el resultado de la mayor parte de la lógica de negocios que se ejecuta en el nivel de aplicación. Dado que el nivel de aplicación de SAP ejecuta la lógica de negocios, emite consultas al nivel de base de datos con una elevada frecuencia a una velocidad de miles o decenas de miles de consultas por segundo. En la mayoría de los casos, la naturaleza de estas consultas es sencilla. A menudo, se pueden ejecutar en el nivel de base de datos en 500 microsegundos o menos.

El tiempo invertido en la red para enviar una consulta de este tipo desde la capa de aplicación al nivel de base de datos y recibir el conjunto de resultados influye considerablemente en el tiempo que se tarda en ejecutar procesos empresariales. Esta sensibilidad a la latencia de red es la razón por la que se debe lograr una latencia de red mínima en proyectos de implementación de SAP. Consulte la [Nota de SAP 1100926 - Preguntas más frecuentes: rendimiento de red](https://launchpad.support.sap.com/#/notes/1100926/E) para obtener directrices para clasificar la latencia de red.

En muchas regiones de Azure, el número de centros de datos ha crecido. Al mismo tiempo, los clientes, especialmente para sistemas SAP de gama alta, usan familias de máquinas virtuales más especiales, como la familia M o Mv2, o en raras ocasiones HANA (instancias grandes). Son tipos de máquinas virtuales de Azure que no siempre están disponibles en cada uno de los centros de datos que se recopilan en una región de Azure. Estos hechos pueden crear oportunidades para optimizar la latencia de red entre la capa de aplicación de SAP y la capa de DBMS de SAP.

Para ofrecer la posibilidad de optimizar la latencia de red, Azure ofrece [grupos con ubicación por proximidad](../../co-location.md). Los grupos con ubicación por proximidad se pueden usar para forzar la agrupación de diferentes tipos de máquinas virtuales en una sola red que proporciona una latencia de red baja suficiente entre estos distintos tipos de máquina virtual en los que todavía no se han proporcionado. En el proceso de implementación de la primera máquina virtual en un grupo con ubicación por proximidad, la máquina virtual se enlaza a un tronco de red específico. Como todas las demás máquinas virtuales que se van a implementar en el mismo grupo con ubicación por proximidad, esas máquinas virtuales se agrupan en la misma zona de red. Aunque esta posibilidad sea muy atractiva, el uso de la construcción incluye también algunas restricciones y dificultades:

- No se puede suponer que todos los tipos de máquinas virtuales de Azure están disponibles en todos los centros de datos de Azure o en todos y cada uno de los troncos de red. Como resultado, se puede restringir la combinación de diferentes tipos de máquinas virtuales dentro de un grupo con ubicación por proximidad. Estas restricciones se producen porque es posible que el hardware de host necesario para ejecutar un tipo de máquina virtual determinado no esté presente en el centro de datos o en la zona de red a la que se asignó el grupo con ubicación por proximidad.
- Al cambiar el tamaño de las partes de las máquinas virtuales que se encuentran dentro de un grupo con ubicación por proximidad, no se puede suponer automáticamente que, en todos los casos, el nuevo tipo de máquina virtual está disponible en el mismo centro de datos o en la zona de red a la que se asignó el grupo con ubicación por proximidad.
- A medida que Azure retira hardware, puede forzar que determinadas máquinas virtuales de un grupo con ubicación por proximidad se muevan a otro centro de datos de Azure o a otro tronco de red. Para más información sobre este caso, consulte el documento [Grupos con ubicación por proximidad](../../co-location.md#planned-maintenance-and-proximity-placement-groups).  

> [!IMPORTANT]
> Como resultado de las posibles restricciones, solo se deben usar grupos con ubicación por proximidad:
>
> - Cuando sea necesario en determinados escenarios (consulte más adelante)
> - Cuando la latencia de red entre la capa de aplicación y la capa de DBMS es demasiado alta y afecta a la carga de trabajo
> - Solo en la granularidad de un único sistema SAP y no en todo el entorno del sistema o en un entorno completo de SAP
> - Para mantener al mínimo los distintos tipos de máquina virtual y el número de máquinas virtuales dentro de un grupo con ubicación por proximidad


Los escenarios en los que usó grupos con ubicación por proximidad hasta ahora eran:

- Implementación de la carga de trabajo de SAP con conjuntos de disponibilidad. Donde el nivel de base de datos de SAP, el nivel de aplicación de SAP y las máquinas virtuales de ASCS/SCS se agrupaban en tres conjuntos de disponibilidad diferentes. En tal caso, quería asegurarse de que los conjuntos de disponibilidad no se repartían entre toda la región de Azure, ya que esto podría, dependiendo de la región de Azure, provocar una latencia de red que podría afectar negativamente a la carga de trabajo de SAP.
- Quería implementar los recursos críticos de la carga de trabajo de SAP en diferentes zonas de disponibilidad y, por otro lado, quería asegurarse de que las máquinas virtuales del nivel de aplicación de cada una de las zonas se distribuirían entre distintos dominios de error mediante conjuntos de disponibilidad. En este caso, como se describe más adelante en el documento, los grupos con ubicación por proximidad son la adherencia necesaria.
- Ha usado grupos con ubicación por proximidad para agrupar máquinas virtuales con el fin de lograr una latencia de red óptima entre los servicios hospedados en las máquinas virtuales.

En cuanto al escenario de implementación n.º 1, en muchas regiones, especialmente en las regiones sin zonas de disponibilidad y la mayoría de las regiones con zonas de disponibilidad, la latencia de red independientemente de dónde se aterrizó las máquinas virtuales es aceptable. Aunque hay algunas regiones de Azure que no pueden proporcionar una experiencia suficientemente buena sin colocar los tres conjuntos de disponibilidad diferentes con el uso de conjuntos de disponibilidad. A partir del escenario de implementación n.º 2, vamos a recomendar una manera diferente de usar grupos con ubicación por proximidad en las secciones siguientes de este documento.



## <a name="what-are-proximity-placement-groups"></a>¿Qué son los grupos de selección de ubicación de proximidad? 
Un grupo de selección de ubicación de proximidad de Azure es una construcción lógica. Cuando se define un grupo con ubicación por proximidad, se enlaza a una región de Azure y a un grupo de recursos de Azure. Cuando se implementan VM, las siguientes hacen referencia a un grupo de selección de ubicación de proximidad:

- La primera máquina virtual de Azure implementada en una zona de red con muchas unidades de proceso de Azure y baja latencia de red. Este tipo de zona de red a menudo coincide con un único centro de datos de Azure. Puede pensar en la primera máquina virtual como una "máquina virtual de ámbito" que se implementa en una unidad de escalado de proceso basada en algoritmos de asignación de Azure que finalmente se combinan con parámetros de implementación.
- Todas las máquinas virtuales posteriores implementadas que hacen referencia al grupo con ubicación por proximidad se implementarán en la misma red que la primera máquina virtual.

> [!NOTE]
> Si no hay ningún hardware de host implementado que pueda ejecutar un tipo de máquina virtual específica en el mismo tronco de red en el que se colocó la primera máquina virtual, la implementación del tipo de máquina virtual solicitada no se realizará correctamente. Verá un mensaje de error de asignación que indica que la máquina virtual no se puede admite dentro del perímetro del grupo con ubicación por proximidad.

Un único [grupo de recursos de Azure](../../../azure-resource-manager/management/manage-resources-portal.md) puede tener varios grupos de selección de ubicación de proximidad asignados. Sin embargo, un grupo de selección de ubicación de proximidad solo se puede asignar a un grupo de recursos de Azure.


## <a name="proximity-placement-groups-with-sap-systems-that-use-only-azure-vms"></a>Grupos de selección de ubicación de proximidad con sistemas SAP que solo usan VM de Azure
En esta sección, se van a seguir las arquitecturas de implementación usadas hasta ahora y las nuevas recomendaciones.

### <a name="proximity-placement-groups-with-zonal-deployments"></a>Grupos con ubicación por proximidad con implementaciones zonales
En el caso de las implementaciones que no usan HANA (instancias grandes), es importante proporcionar una latencia de red razonablemente baja entre el nivel de aplicación de SAP y el nivel de DBMS. Para habilitar una latencia de red razonablemente baja para un conjunto limitado de escenarios, se puede definir un grupo con ubicación por proximidad de Azure para este tipo de sistema SAP.

Evite la agrupación de varios sistemas de producción o de no producción de SAP en un solo grupo con ubicación por proximidad. Evite las agrupaciones de sistemas SAP porque cuantos más sistemas se agrupen en un grupo con ubicación por proximidad, mayor será la probabilidad de lo siguiente:

- Que necesite un tipo de máquina virtual que no esté disponible en la zona de red a la que se asignó el grupo con ubicación por proximidad.
- Esos recursos de máquinas virtuales no estándar, como las máquinas virtuales de la serie M, podrían acabar sin cumplirse cuando necesite expandir el número de máquinas virtuales en un grupo con ubicación por proximidad con el tiempo.

El uso del grupo con ubicación por proximidad que se ha recomendado hasta ahora es similar al de este gráfico.

![Grupos con ubicación por proximidad antiguos con zonas](./media/sap-proximity-placement-scenarios/vm-ppg-zone-old.png)

Ha creado un grupo con ubicación por proximidad en cada una de las dos zonas de disponibilidad en las que implementó el sistema SAP. Todas las máquinas virtuales de una zona determinada forman parte del grupo con ubicación por proximidad individual de esa zona concreta. Ha empezado en cada zona con la implementación de la máquina virtual de DBMS para el ámbito del grupo con ubicación por proximidad y, a continuación, ha implementado la máquina virtual de ASCS en la misma zona y grupo con ubicación por proximidad. En un tercer paso, creó un conjunto de disponibilidad de Azure, asignó el conjunto de disponibilidad al grupo con ubicación por proximidad con ámbito e implementó la capa de aplicación de SAP en él. La ventaja de esta configuración era que todos los componentes estaban bien alineados debajo de la misma red. La gran desventaja es que la flexibilidad para cambiar el tamaño de las máquinas virtuales puede ser limitada.


En función de muchas mejoras implementadas por Microsoft en las regiones de Azure para reducir la latencia de red dentro de una zona de disponibilidad de Azure, la nueva guía de implementación para implementaciones zonales tiene el siguiente aspecto:

![Grupos con ubicación por proximidad nuevos con zonas](./media/sap-proximity-placement-scenarios/vm-ppg-zone.png)

La diferencia con la recomendación dada hasta ahora es que las máquinas virtuales de base de datos de las dos zonas ya no forman parte de los grupos con ubicación por proximidad. Los grupos con ubicación por proximidad por zona ahora están limitados con la implementación de la máquina virtual que ejecuta las instancias de ASCS/SCS de SAP. Esto también significa que para las regiones en las que varios centros de datos recopilan zonas de disponibilidad, la instancia de ASCS/SCS y el nivel de aplicación podrían ejecutarse en una zona de red y las máquinas virtuales de base de datos podrían ejecutarse en otra zona de red. Aunque con las mejoras de red realizadas, la latencia de red entre el nivel de aplicación de SAP y el nivel de DBMS todavía debe ser suficiente para un rendimiento y un procesamiento suficientemente buenos. La ventaja de esta nueva configuración es que tiene más flexibilidad para cambiar el tamaño de las máquinas virtuales o cambiar a nuevos tipos de máquina virtual con la capa de DBMS o la capa de aplicación del sistema SAP. 


### <a name="proximity-placement-groups-with-availability-set-deployments"></a>Grupos con ubicación por proximidad con implementaciones de conjuntos de zonas de disponibilidad
En este caso, el propósito es usar grupos con ubicación por proximidad para colocar las máquinas virtuales que se implementan a través de diferentes conjuntos de disponibilidad. En este escenario de uso, no se utiliza una implementación controlada en diferentes zonas de disponibilidad en una región. En su lugar, quiere implementar el sistema SAP mediante conjuntos de disponibilidad. Como resultado, tiene al menos un conjunto de disponibilidad para las máquinas virtuales de DBMS, las máquinas virtuales de ASCS/SCS y las máquinas virtuales de nivel de aplicación. Dado que, en el momento de la implementación una máquina virtual, no puede especificar un conjunto de disponibilidad Y una zona de disponibilidad, no puede controlar dónde se van a asignar las máquinas virtuales de los distintos conjuntos de disponibilidad. Esto podría dar lugar a algunas regiones de Azure en las que la latencia de red entre diferentes máquinas virtuales podría ser demasiado alta para proporcionar una experiencia de rendimiento lo suficientemente buena. Por lo tanto, la arquitectura resultante tendría el siguiente aspecto:


![Grupos con ubicación por proximidad con AvSets](./media/sap-proximity-placement-scenarios/vm-ppg-avsets.png)

En este gráfico, se asignaría un único grupo con ubicación por proximidad a un único sistema SAP. Este grupo con ubicación por proximidad se asigna a los tres conjuntos de disponibilidad. A continuación, el ámbito del grupo con ubicación por proximidad se establece mediante la implementación de las primeras máquinas virtuales de nivel de base de datos en el conjunto de disponibilidad de DBMS. Esta recomendación de arquitectura colocará todas las máquinas virtuales en el mismo tronco de red. Se presentan las restricciones mencionadas anteriormente en este artículo. Por lo tanto, la arquitectura del grupo con ubicación por proximidad debe usarse con poca frecuencia.


## <a name="proximity-placement-groups-and-hana-large-instances"></a>Grupos de selección de ubicación de proximidad y HANA (instancias grandes)
Si algunos de los sistemas SAP se basan en [HANA (instancias grandes)](./hana-overview-architecture.md) para el nivel de aplicación, se pueden experimentar mejoras considerables en la latencia de red entre la unidad de HANA (instancias grandes) y las VM de Azure al usar unidades de HANA (instancias grandes) que se han implementado en [sellos o filas de la revisión 4](./hana-network-architecture.md#networking-architecture-for-hana-large-instance). Una de las mejoras es que cuando se implementan unidades de HANA (instancias grandes), lo hacen con un grupo de selección de ubicación de proximidad. Puede usar ese grupo de selección de ubicación de proximidad para implementar las máquinas virtuales del nivel de aplicación. Como resultado, esas VM se implementarán en el mismo centro de datos que hospeda la unidad de HANA (instancias grandes).

Para determinar si la unidad de HANA (instancias grandes) está implementada en un sello o fila de la revisión 4, consulte el artículo [Control de instancias grandes de HANA en Azure mediante Azure Portal](./hana-li-portal.md#look-at-attributes-of-single-hli-unit). En la información general de los atributos de la unidad de HANA (instancias grandes), también puede determinar el nombre del grupo de selección de ubicación de proximidad porque se creó cuando se implementó la unidad de HANA (instancias grandes). El nombre que aparece en la información general de los atributos es el nombre del grupo de selección de ubicación de proximidad en el que debe implementar las VM del nivel de aplicación.

En comparación con los sistemas SAP que usan solo máquinas virtuales de Azure, cuando se usan instancias grandes de HANA, tiene menos flexibilidad a la hora de decidir el número de [grupos de recursos de Azure](../../../azure-resource-manager/management/manage-resources-portal.md) que se van a usar. Todas las unidades de HANA (instancias grandes) de un [inquilino de HANA (instancias grandes)](./hana-know-terms.md) se agrupan en un único grupo de recursos, tal como se describe en [este artículo](./hana-li-portal.md#display-of-hana-large-instance-units-in-the-azure-portal). A menos que realice una implementación en distintos inquilinos para separar, por ejemplo, sistemas de producción y de no producción u otros sistemas, todas las unidades de HANA (instancias grandes) se implementarán en un mismo inquilino de HANA (instancias grandes). Este inquilino tiene una relación uno a uno con un grupo de recursos. Sin embargo, se definirá un grupo de selección de ubicación de proximidad independiente para cada una de las unidades.

Como resultado, las relaciones entre grupos de recursos de Azure y grupos de selección de ubicación de proximidad para un solo inquilino tendrán el siguiente aspecto:

![Grupos de selección de ubicación de proximidad y HANA (instancias grandes)](./media/sap-proximity-placement-scenarios/ppg-for-hana-large-instance-units.png)

## <a name="example-of-deployment-with-proximity-placement-groups"></a>Ejemplo de implementación con grupos de selección de ubicación de proximidad
A continuación, se muestran algunos comandos de PowerShell que puede usar para implementar las VM con grupos de selección de ubicación de proximidad de Azure.

El primer paso, después de iniciar sesión en [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/), consiste en comprobar si se encuentra en la suscripción de Azure que quiere usar para la implementación:

<pre><code>
Get-AzureRmContext
</code></pre>

Si necesita cambiar a una suscripción diferente, puede ejecutar este comando para hacerlo:

<pre><code>
Set-AzureRmContext -Subscription "PPG test subscription"
</code></pre>

Para crear un nuevo grupo de recursos de Azure, ejecute este comando:

<pre><code>
New-AzResourceGroup -Name "ppgexercise" -Location "westus2"
</code></pre>

Para crear el nuevo grupo de selección de ubicación de proximidad, ejecute este comando:

<pre><code>
New-AzProximityPlacementGroup -ResourceGroupName "ppgexercise" -Name "collocate" -Location "westus2"
</code></pre>

Implemente su primera VM en el grupo de selección de ubicación de proximidad con un comando como este:

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "ppgscopevm" -Location "westus2" -OpenPorts 80,3389 -ProximityPlacementGroup "collocate" -Size "Standard_E16s_v4"
</code></pre>

El comando anterior implementa una VM basada en Windows. Después de que la implementación de la máquina virtual se realice correctamente, el ámbito del tronco de red del grupo con ubicación por proximidad se define en la región de Azure. Todas las implementaciones de máquina virtual posteriores que hagan referencia al grupo con ubicación por proximidad, como se muestra en el comando anterior, se implementarán en el mismo tronco de red, siempre que el tipo de máquina virtual se pueda hospedar en el hardware colocado en ese tronco de red y haya disponible capacidad para ese tipo de máquina virtual.

## <a name="combine-availability-sets-and-availability-zones-with-proximity-placement-groups"></a>Combinación de conjuntos de disponibilidad y Availability Zones con grupos de selección de ubicación de proximidad
Uno de los problemas de usar zonas de disponibilidad para implementaciones de sistemas SAP es que no se puede implementar la capa de aplicación de SAP mediante conjuntos de disponibilidad dentro de la zona de disponibilidad específica. Quiere que la capa de aplicación de SAP se implemente en las mismas zonas que las máquinas virtuales ASCS/SCS. En este momento no es posible hacer referencia a una zona de disponibilidad y a un conjunto de disponibilidad al implementar una sola máquina virtual. Pero simplemente implementando una máquina virtual que indica una zona de disponibilidad, perderá la capacidad de asegurarse de que las máquinas virtuales de la capa de aplicación se repartan entre distintos dominios de actualización y error.

Mediante los grupos de selección de ubicación de proximidad, puede evitar esta restricción. A continuación, se muestra la secuencia de implementación:

- Cree un grupo de selección de ubicación de proximidad.
- Implemente la máquina virtual delimitadora, que se recomienda que sea la máquina virtual de ASCS/SCS, haciendo referencia a una zona de disponibilidad.
- Cree un conjunto de disponibilidad que haga referencia al grupo de proximidad de Azure. (Vea el comando más adelante en este artículo).
- Implemente las VM del nivel de aplicación haciendo referencia al conjunto de disponibilidad y al grupo de selección de ubicación de proximidad.

En lugar de implementar la primera VM como se mostró en la sección anterior, haga referencia a una zona de disponibilidad y al grupo de selección de ubicación de proximidad al implementar la VM:

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "centralserviceszone1" -Location "westus2" -OpenPorts 80,3389 -Zone "1" -ProximityPlacementGroup "collocate" -Size "Standard_E8s_v4"
</code></pre>

Una implementación correcta de esta máquina virtual hospedaría la instancia de ASCS/SCS del sistema SAP en una zona de disponibilidad. El ámbito del grupo con ubicación por proximidad se fija en uno de los troncos de red de la zona de disponibilidad que ha definido.

En el paso siguiente, debe crear los conjuntos de disponibilidad que quiere usar para el nivel de aplicación de su sistema SAP.

Defina y cree el grupo con ubicación por proximidad. El comando para crear el conjunto de disponibilidad requiere una referencia adicional al identificador del grupo de selección de ubicación de proximidad (no al nombre). Puede obtener el identificador del grupo de selección de ubicación de proximidad mediante este comando:

<pre><code>
Get-AzProximityPlacementGroup -ResourceGroupName "ppgexercise" -Name "collocate"
</code></pre>

Al crear el conjunto de disponibilidad, debe tener en cuenta parámetros adicionales al usar discos administrados (valores predeterminados, a menos que se especifique lo contrario) y grupos de selección de ubicación de proximidad:

<pre><code>
New-AzAvailabilitySet -ResourceGroupName "ppgexercise" -Name "ppgavset" -Location "westus2" -ProximityPlacementGroupId "/subscriptions/my very long ppg id string" -sku "aligned" -PlatformUpdateDomainCount 3 -PlatformFaultDomainCount 2 
</code></pre>

Idealmente, debe utilizar tres dominios de error. Sin embargo, el número de dominios de error admitidos puede variar de una región a otra. En este caso, el número máximo de dominios de error posibles para las regiones específicas es dos. Para implementar las VM del nivel de aplicación, debe agregar una referencia al nombre del conjunto de disponibilidad y el nombre del grupo de selección de ubicación de proximidad, como se muestra aquí:

<pre><code>
New-AzVm -ResourceGroupName "ppgexercise" -Name "appinstance1" -Location "westus2" -OpenPorts 80,3389 -AvailabilitySetName "myppgavset" -ProximityPlacementGroup "collocate" -Size "Standard_E16s_v4"
</code></pre>

El resultado de esta implementación es:
- Unos servicios centrales para el sistema SAP que se encuentran en una zona de disponibilidad específica o en las zonas de disponibilidad.
- Una capa de aplicación de SAP que se encuentra a través de conjuntos de disponibilidad en el mismo tronco de red que la máquina virtual o las máquinas virtuales de servicios centrales de SAP (ASCS/SCS).

> [!NOTE]
> Dado que implementa una máquina virtual de DBMS y ASCS/SCS en una zona y una segunda máquina virtual de DBMS y ASCS/SCS en otra zona para crear una configuración de alta disponibilidad, será necesario disponer de grupos con ubicación por proximidad diferentes para cada una de las zonas. Lo mismo se aplica a cualquier conjunto de disponibilidad que use.

## <a name="change-proximity-placement-group-configurations-of-an-existing-system"></a>Cambio de las configuraciones de grupos con ubicación por proximidad de un sistema existente
Si ha implementado grupos con ubicación por proximidad según las recomendaciones dadas hasta ahora y desea ajustarse a la nueva configuración, puede hacerlo con los métodos descritos en estos artículos:

- [Implementación de máquinas virtuales en grupos de selección de ubicación de proximidad con la CLI de Azure](../../linux/proximity-placement-groups.md)
- [Implementación de máquinas virtuales en grupos de selección de ubicación de proximidad con PowerShell](../../windows/proximity-placement-groups.md) 

También puede usar estos comandos para los casos en los que se producen errores de asignación en los casos en los que no se pueda mover a un nuevo tipo de máquina virtual con una máquina virtual existente en el grupo con ubicación por proximidad.

## <a name="next-steps"></a>Pasos siguientes
Consulte la documentación:

- [Lista de comprobación de planeamiento e implementación de cargas de trabajo de SAP en Azure](./sap-deployment-checklist.md)
- [Implementación de máquinas virtuales en grupos de selección de ubicación de proximidad con la CLI de Azure](../../linux/proximity-placement-groups.md)
- [Implementación de máquinas virtuales en grupos de selección de ubicación de proximidad con PowerShell](../../windows/proximity-placement-groups.md)
- [Consideraciones para la implementación de DBMS de Azure Virtual Machines para la carga de trabajo de SAP](./dbms_guide_general.md)