---
title: Insights para Azure Load Balancer
description: Use la información del equilibrador de carga para lograr una localización rápida de errores y tomar decisiones de diseño informadas.
services: load-balancer
documentationcenter: na
author: KumudD
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/27/2020
ms.author: kumud
ms.openlocfilehash: 7f87862cd922451113403b382930379f63e471e4
ms.sourcegitcommit: 82d82642daa5c452a39c3b3d57cd849c06df21b0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/07/2021
ms.locfileid: "113357902"
---
# <a name="using-insights-to-monitor-and-configure-your-azure-load-balancer"></a>Uso de la información para supervisar y configurar Azure Load Balancer

A través de Azure Monitor para redes, se le proporcionan visualizaciones de dependencia funcional y el panel de métricas preconfigurado para las instancias de Load Balancer. Estos objetos visuales le ayudarán a tomar decisiones de diseño informadas y a localizar, diagnosticar y resolver errores rápidamente.

>[!NOTE] 
>Tenga en cuenta que esta característica está en versión preliminar y que la vista de dependencia funcional y el panel preconfigurado pueden cambiar para mejorar esta experiencia.

>[!IMPORTANT]
>Standard Load Balancer es necesario para ver las métricas del espacio de nombres de Load Balancer en el panel de métricas preconfigurado. Podrá seguir viendo las métricas de los espacios de nombres de la máquina virtual, el conjunto de escalado de máquinas virtuales y Connection Monitor. No obstante, se recomienda la [actualización a Standard](./upgrade-basic-standard.md) para que las cargas de trabajo de producción aprovechen el sólido conjunto de métricas de Load Balancer.

## <a name="functional-dependency-view"></a>Vista de dependencia funcional

La vista de dependencia funcional le permitirá ilustrar incluso las configuraciones más complejas del equilibrador de carga. Con comentarios visuales sobre la configuración de Load Balancer más reciente, puede realizar actualizaciones mientras mantiene la configuración en mente.

Para acceder a esta vista, visite la hoja Insights de su recurso de Load Balancer en Azure.

:::image type="content" source="./media/load-balancer-insights/load-balancer-functional-dependency-visual.png" alt-text="Representación de la vista de dependencia funcional. El front-end del equilibrador de carga puede verse al conectarse a los miembros del grupo de back-end a través de las reglas configuradas. Para Standard Load Balancer, las líneas de las reglas de equilibrio de carga para las instancias del grupo de back-end están codificadas por colores según el estado de sondeo de estado." border="true":::

En el caso de Standard Load Balancer, los recursos del grupo de back-end están codificados por colores con el estado de sondeo de estado que indica la disponibilidad actual del grupo de back-end para atender el tráfico. Junto a la topología anterior, se le presenta un gráfico de estado de mantenimiento, que ofrece una vista de instantánea del estado de la aplicación.

## <a name="metrics-dashboard"></a>Panel de métricas

En la hoja Insights de Load Balancer, puede seleccionar Métricas más detalladas para ver un [libro de Azure Monitor](../azure-monitor/visualize/workbooks-overview.md) preconfigurado que contiene objetos visuales de métricas relevantes para aspectos específicos de Load Balancer. Este panel mostrará el estado Load Balancer y los vínculos a la documentación correspondiente en la parte superior de la página.

Al principio, se mostrará la pestaña Información general. Puede desplazarse por las pestañas disponibles, cada una de las cuales contiene objetos visuales relevantes para un aspecto específico de Load Balancer. Las instrucciones explícitas para cada una de ellas están disponibles en el panel en la parte inferior de cada pestaña.

Las pestañas del panel disponibles actualmente son:
* Información general
* Disponibilidad de front-end y back-end
* Rendimiento de los datos
* Distribución del flujo
* Monitores de conexión
* Definiciones de métricas 

### <a name="overview-tab"></a>Pestaña Información general
La pestaña Información general contiene una cuadrícula en la que se pueden realizar búsquedas con el estado de sondeo de estado y la disponibilidad de la ruta de acceso a datos general para cada una de las direcciones IP de front-end asociadas a su instancia de Load Balancer. Estas métricas indican si la dirección IP de front-end tiene capacidad de respuesta y las instancias de proceso del grupo de back-end están respondiendo individualmente a las conexiones entrantes.

También puede ver el rendimiento general de los datos de cada dirección IP de front-end en esta página para tener una idea de si está generando y recibiendo los niveles de tráfico esperados. Las instrucciones que aparecen en la parte inferior de la página le dirigirán a la pestaña adecuada en caso de que vea valores irregulares.

### <a name="frontend-and-backend-availability-tab"></a>Pestaña Disponibilidad de front-end y back-end
Las pestañas de disponibilidad de front-end y back-end muestran las métricas de rendimiento de la ruta de acceso a datos y de estado de sondeo de estado presentadas en algunas vistas útiles. En el primer gráfico se muestra el valor agregado para que pueda determinar si hay un problema. En el resto de los gráficos se muestran estas métricas divididas por varias dimensiones para que pueda solucionar e identificar los orígenes de cualquier problema de disponibilidad entrante.

En la parte inferior de la página se proporciona un flujo de trabajo para ver estos gráficos con causas comunes para varios síntomas. 

### <a name="data-throughput-tab"></a>Pestaña Rendimiento de los datos
La pestaña Rendimiento de los datos permite revisar el rendimiento de entrada y salida para identificar si los patrones de tráfico son los esperados. Se mostrará la división del rendimiento de los datos entrantes y salientes por la dirección IP de front-end y el puerto de front-end para que pueda identificar si los servicios que se ejecutan se están realizando de forma individual.

### <a name="flow-distribution"></a>Distribución del flujo
La pestaña Distribución del flujo le ayudará a visualizar y administrar el número de flujos que las instancias de back-end van a recibir y producir. Muestra la velocidad de creación de flujos y el número de flujos para el tráfico entrante y saliente, así como el tráfico de red que recibe cada máquina virtual y cada instancia del conjunto de escalado de máquinas virtuales. 

Estas vistas pueden proporcionar comentarios sobre si la configuración de Load Balancer o los patrones de tráfico conducen a un tráfico desequilibrado. Por ejemplo, si tiene configurada la afinidad de la sesión y un solo cliente está realizando un número desproporcionado de solicitudes. También le indicará si se está aproximando al [límite de flujo por máquina virtual](../virtual-network/virtual-machine-network-throughput.md#flow-limits-and-active-connections-recommendations) para el tamaño de la máquina.

### <a name="connection-monitors"></a>Monitores de conexión
La pestaña de instancias Connection Monitor le mostrará la latencia de recorrido de ida y vuelta en un mapa global para todas las instancias de [Connection Monitor](../network-watcher/connection-monitor.md) que ha configurado. Estos objetos visuales proporcionan información útil para los servicios con requisitos de latencia estrictos. Para satisfacer sus necesidades, puede que necesite agregar implementaciones regionales adicionales o cambiar a un modelo de [equilibrio de carga entre regiones](./cross-region-overview.md).

### <a name="metric-definitions"></a>Definiciones de métricas
La pestaña Definiciones de métricas contiene toda la información que se muestra en el [artículo de métricas multidimensionales](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics).

## <a name="next-steps"></a>Pasos siguientes
* Revise el panel y proporcione comentarios mediante el siguiente vínculo si hay algo que se pueda mejorar.
* [Revise la documentación de las métricas para asegurarse de que comprende cómo se calcula cada métrica](./load-balancer-standard-diagnostics.md#multi-dimensional-metrics).
* [Cree instancias de Connection Monitor para Load Balancer](../network-watcher/connection-monitor.md).
* [Cree sus propios libros](../azure-monitor/visualize/workbooks-overview.md). Para ello, puede hacer clic en el botón Editar en el panel de métricas detalladas para inspirarse.
