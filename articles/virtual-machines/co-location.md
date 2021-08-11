---
title: Grupos de selección de ubicación de proximidad
description: Obtenga información sobre el uso de grupos con ubicación por proximidad en Azure.
author: cynthn
ms.author: cynthn
ms.service: virtual-machines
ms.subservice: proximity-placement-groups
ms.topic: conceptual
ms.date: 3/07/2021
ms.reviewer: zivr
ms.openlocfilehash: 15da7300018d563ed9919c145ca3e7f08a07f619
ms.sourcegitcommit: a9f131fb59ac8dc2f7b5774de7aae9279d960d74
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110190640"
---
# <a name="proximity-placement-groups"></a>Grupos de selección de ubicación de proximidad

Si coloca las máquinas virtuales en una sola región, reducirá la distancia física entre las instancias. Además, si las coloca en una sola zona de disponibilidad, estarán todavía más cercanas físicamente. Aun así, a medida que la superficie de Azure crece, una sola zona de disponibilidad puede abarcar varios centros de datos físicos, lo que es posible que provoque una latencia de red que puede afectar al rendimiento de la aplicación. 

Para acercar las máquinas virtuales lo más posible con la menor latencia posible, debe implementarlas dentro de un grupo de selección de ubicación de proximidad.

Un grupo de selección de ubicación de proximidad es una agrupación lógica que se usa para asegurarse de que los recursos de proceso de Azure se encuentran físicamente cercanos entre sí. Los grupos de selección de ubicación de proximidad son útiles para las cargas de trabajo en las que la latencia baja es un requisito.

- Baja latencia entre máquinas virtuales independientes.
- Baja latencia entre máquinas virtuales en un único conjunto de disponibilidad o conjunto de escalado de máquinas virtuales. 
- Baja latencia entre máquinas virtuales independientes, máquinas virtuales en varios conjuntos de disponibilidad o varios conjuntos de escalado. Puede tener varios recursos de proceso en un solo grupo de selección de ubicación para reunir una aplicación de varias capas. 
- Baja latencia entre varias capas de aplicación con diferentes tipos de hardware. Por ejemplo, la ejecución del back-end con la serie M en un conjunto de disponibilidad y el front-end en una instancia de la serie D, en un conjunto de escalado, en un solo grupo de selección de ubicación de proximidad.

![Gráfico para grupos de selección de ubicación de proximidad](./media/virtual-machines-common-ppg/ppg.png)

## <a name="using-proximity-placement-groups"></a>Uso de los grupos de selección de ubicación de proximidad 

Un grupo con ubicación por proximidad es un recurso de Azure. Debe crear uno para poder usarlo con otros recursos. Una vez creado, podría usarse con máquinas virtuales, conjuntos de disponibilidad o conjuntos de escalado de máquinas virtuales. Un grupo de selección de ubicación de proximidad se especifica al crear recursos de proceso que proporcionan el identificador de grupo de selección de ubicación de proximidad. 

También puede mover un recurso existente a un grupo de selección de ubicación de proximidad. Al hacerlo, en primer lugar debe detener (desasignar) el recurso, ya que es posible que se vuelva a implementar posiblemente en otro centro de datos de la región para satisfacer la restricción de coubicación. 

En el caso de conjuntos de disponibilidad y conjuntos de escalado de máquinas virtuales, debe establecer el grupo de selección de ubicación de proximidad en el nivel de recursos, en lugar de en las máquinas virtuales individuales. 

Un grupo de selección de ubicación de proximidad es una restricción de colocación y no un mecanismo de anclaje. Se ancla a un centro de datos específico con la implementación del primer recurso para usarlo. Una vez que todos los recursos que usan el grupo de selección de ubicación de proximidad se han detenido (desasignado) o eliminado, ya no se anclan. Por lo tanto, cuando se usa un grupo de selección de ubicación de proximidad con varias series de máquinas virtuales, es importante especificar todos los tipos necesarios al principio en una plantilla cuando sea posible o seguir una secuencia de implementación, lo que mejorará las oportunidades de una implementación correcta. Si se produce un error en la implementación, reinicie esta con el tamaño de máquina virtual que ha dado error como el primer tamaño que se va a implementar.

## <a name="what-to-expect-when-using-proximity-placement-groups"></a>Qué esperar al usar los grupos con ubicación por proximidad 
Los grupos con ubicación por proximidad ofrecen coubicación en el mismo centro de datos. Sin embargo, dado que los grupos con ubicación por proximidad representan una restricción de implementación adicional, pueden producirse errores de asignación. Hay pocos casos de uso en los que puede ver errores de asignación al usar grupos con ubicación por proximidad:

- Al solicitar la primera máquina virtual en el grupo con ubicación por proximidad, el centro de datos se selecciona automáticamente. En algunos casos, se puede generar un error en la segunda solicitud de una SKU de máquina virtual diferente si no existe en ese centro de datos. En este caso, se devuelve un error **OverconstrainedAllocationRequest**. Para evitar esto, intente cambiar el orden en el que implementa las SKU o haga que ambos recursos se implementen mediante una sola plantilla de ARM.
-   En el caso de las cargas de trabajo elásticas, en las que se agregan y quitan instancias de máquina virtual, es posible que tener una restricción de grupo con ubicación por proximidad en la implementación produzca un error al realizar la solicitud, lo que da como resultado un error **AllocationFailure**. 
- Otra manera de lograr la elasticidad consiste en detener (desasignar) e iniciar las máquinas virtuales según sea necesario. Dado que la capacidad no se conserva una vez que se detiene (desasigna) una máquina virtual, si se vuelve a iniciar, puede producirse un error **AllocationFailure**.

## <a name="planned-maintenance-and-proximity-placement-groups"></a>Grupos de mantenimiento planeado y con ubicación por proximidad

Los eventos de mantenimiento planeado, como la retirada de hardware en un centro de datos Azure, podrían afectar a la alineación de los recursos en los grupos con ubicación por proximidad. Los recursos se pueden migrar a un centro de datos diferente, con lo que se interrumpirán las expectativas de colocación y latencia asociadas al grupo con ubicación por proximidad.

### <a name="check-the-alignment-status"></a>Comprobación del estado de alineación

Para comprobar el estado de alineación de los grupos con ubicación por proximidad, puede hacer lo siguiente.

- El estado de colocación del grupo de ubicación por proximidad se puede ver mediante el portal, la CLI y PowerShell.

    -   Al usar PowerShell, se puede obtener el estado de colocación con el cmdlet Get-AzProximityPlacementGroup incluyendo el parámetro opcional "-ColocationStatus".

    -   Cuando se usa la CLI, el estado de colocación puede obtenerse con `az ppg show` incluyendo el parámetro opcional "--include-colocation-status".

- En cada grupo con ubicación por proximidad, una propiedad de **estado de coubicación** proporciona el resumen actual de estado de alineación de los recursos agrupados. 

    - **Alineado**: el recurso está dentro de la misma envoltura de latencia del grupo de ubicación por proximidad.

    - **Desconocido**: se desasigna al menos uno de los recursos de máquina virtual. Una vez que se vuelva a iniciar correctamente, el estado debería ser de nuevo **Alineado**.

    - **No alineado**: al menos un recurso de máquina virtual no está alineado con el grupo con ubicación por proximidad. Los recursos específicos que no están alineados también se seleccionarán por separado en la sección de pertenencia

- En el caso de los conjuntos de disponibilidad, puede ver información sobre la alineación de máquinas virtuales individuales en la página de información general del conjunto de disponibilidad.

- En el caso de los conjuntos de escalado, la información sobre la alineación de instancias individuales puede verse en la pestaña **Instancias** de la página **Información general** del conjunto de escalado. 

### <a name="re-align-resources"></a>Nueva alineación de los recursos 

Si el estado de un grupo con ubicación por proximidad es `Not Aligned`, puede detenerlo o desasignarlo y, luego, reiniciar los recursos afectados. Si la máquina virtual está en un conjunto de disponibilidad o en un conjunto de escalado, todas las máquinas virtuales de esos conjuntos se deben detener o desasignar primero antes de reiniciarlas.

Si se produce un error de asignación debido a las restricciones de implementación, es posible que tenga que detener o desasignar primero todos los recursos del grupo con ubicación por proximidad afectado (incluidos los recursos alineados) y, luego, reiniciarlos para restaurar la alineación.

## <a name="best-practices"></a>Procedimientos recomendados 
- Para la latencia más baja, use grupos de selección de ubicación de proximidad junto con redes aceleradas. Para obtener más información, consulte [Creación de una máquina virtual Linux con redes aceleradas](../virtual-network/create-vm-accelerated-networking-cli.md) o [Creación de una máquina virtual Windows con redes aceleradas](../virtual-network/create-vm-accelerated-networking-powershell.md).
- Implemente todos los tamaños de máquina virtual en una sola plantilla. Para evitar llegar a hardware que no admite todas las SKU y tamaños de máquina virtual que necesita, incluya todas las capas de aplicación en una sola plantilla de modo que se implementen todas al mismo tiempo.
- Si va a crear scripts de la implementación mediante PowerShell, la CLI o el SDK, puede producirse un error de asignación `OverconstrainedAllocationRequest`. En este caso, debe detener o desasignar todas las máquinas virtuales existentes y cambiar la secuencia en el script de implementación para que empiece con la SKU o los tamaños de máquina virtual en los que se produjo el error. 
- Al reutilizar un grupo de selección de ubicación existente del que se han eliminado máquinas virtuales, espere a que la eliminación se complete antes de agregarle máquinas virtuales.
- Si su prioridad es la latencia, coloque las máquinas virtuales en un grupo de selección de ubicación de proximidad y toda la solución en una zona de disponibilidad. En cambio, si su prioridad es la resistencia, distribuya las instancias por varias zonas de disponibilidad (un solo grupo de selección de ubicación de proximidad no puede abarcar zonas).

## <a name="next-steps"></a>Pasos siguientes

- Implemente una máquina virtual en un grupo con ubicación por proximidad mediante la [CLI de Azure](./linux/proximity-placement-groups.md) o [PowerShell](./windows/proximity-placement-groups.md).
- Aprenda a [probar la latencia de red](../virtual-network/virtual-network-test-latency.md).
- Aprenda a [optimizar el rendimiento de la red](../virtual-network/virtual-network-optimize-network-bandwidth.md).
- Aprenda a [usar grupos de selección de ubicación de proximidad con las aplicaciones de SAP](./workloads/sap/sap-proximity-placement-scenarios.md).
