---
title: Métricas en Azure Monitor | Microsoft Docs
description: Conozca las métricas de Azure Monitor, que son datos de supervisión ligeros capaces de admitir escenarios casi en tiempo real.
documentationcenter: ''
author: bwren
manager: carmonm
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/27/2021
ms.author: bwren
ms.openlocfilehash: 3986e59965842166ce675cd778d04984896d671d
ms.sourcegitcommit: 557ed4e74f0629b6d2a543e1228f65a3e01bf3ac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129458536"
---
# <a name="azure-monitor-metrics-overview"></a>Información general sobre las métricas en Microsoft Azure
Las métricas en Azure Monitor son una característica de Azure Monitor que recopila datos numéricos de [recursos supervisados](../monitor-reference.md) en una base de datos de serie temporal. Las métricas son valores numéricos que se recopilan a intervalos regulares y describen algún aspecto de un sistema en un momento determinado. 

Las métricas de Azure Monitor son ligeras y capaces de admitir escenarios casi en tiempo real, por lo que son útiles para la creación de alertas y la detección rápida de incidencias. Puede analizarlas de forma interactiva con el Explorador de métricas, recibir una notificación anticipada con una alerta cuando un valor cruce un umbral o visualizarlas en un libro o panel.

> [!NOTE]
> Las métricas de Azure Monitor son la mitad de la plataforma de datos que admite Azure Monitor. La otra son los [registros de Azure Monitor](../logs/data-platform-logs.md), que recopilan y organizan los datos de registro y de rendimiento, y permiten que se analicen con un lenguaje de consulta enriquecido. 
>
> La característica Métricas solo puede almacenar datos numéricos en una estructura determinada, mientras que la característica Registros puede almacenar una variedad de tipos de datos (cada uno con su propia estructura). También se pueden realizar análisis complejos sobre los datos de registros mediante consultas de registros, que no se pueden usar para analizar los datos de métricas.

## <a name="what-can-you-do-with-azure-monitor-metrics"></a>¿Qué puede hacer con las métricas de Azure Monitor?
En la tabla siguiente se enumeran las distintas maneras en que se puede usar la característica Métricas de Azure Monitor.

|  | Descripción |
|:---|:---|
| **Análisis** | Use el [Explorador de métricas](metrics-charts.md) para analizar las métricas recopiladas en un gráfico y comparar las métricas de varios recursos. |
| **Alerta** | Configure una [regla de alertas de métricas](../alerts/alerts-metric.md) que envíe una notificación o realice una [acción automatizada](../alerts/action-groups.md) cuando el valor de la métrica cruce un umbral. |
| **Visualizar** | Ancle un gráfico del Explorador de métricas a un [panel de Azure](../app/tutorial-app-dashboards.md).<br>Cree un [libro](../visualize/workbooks-overview.md) para combinar con varios conjuntos de datos en un informe interactivo. Exporte los resultados de una consulta a [Grafana](../visualize/grafana-plugin.md) para utilizar sus paneles y combinar con otros orígenes de datos. |
| **Automatización** |  Use la [escalabilidad automática](../autoscale/autoscale-overview.md) para aumentar o disminuir los recursos según un valor de métrica que cruce un umbral. |
| **Recuperar** | Acceda a los valores de métricas desde una línea de comandos mediante [cmdlets de PowerShell](/powershell/module/az.monitor).<br>Acceda a los valores de métricas desde una aplicación personalizada con la [API REST](./rest-api-walkthrough.md).<br>Acceda a los valores de métricas desde una línea de comandos mediante la [CLI de Azure](/cli/azure/monitor/metrics). |
| **Exportarar** | [Enrute las métricas a los registros](./resource-logs.md#send-to-azure-storage) para analizar los datos de las métricas y de los registros de Azure Monitor y para almacenar los valores de métricas durante un período superior a 93 días.<br>Transmita las métricas a un [centro de eventos](./stream-monitoring-data-event-hubs.md) para enrutarlas a sistemas externos. |
| **Archivar** | [Archivar](./platform-logs-overview.md) el historial de rendimiento o estado del recurso para fines de cumplimiento, auditoría o creación de informes sin conexión. |

![Diagrama que muestra los orígenes y los usos de las métricas.](media/data-platform-metrics/metrics-overview.png)

## <a name="data-collection"></a>datos, recopilación
Azure Monitor recopila métricas de los siguientes orígenes. Una vez que estas métricas se recopilan en la base de datos de métricas de Azure Monitor, se pueden evaluar juntas independientemente de su origen.

- **Recursos de Azure**. Las métricas de plataforma se crean mediante los recursos de Azure y brindan visibilidad sobre su estado y rendimiento. Cada tipo de recurso crea un [conjunto distinto de métricas](./metrics-supported.md) sin ninguna configuración necesaria. Las métricas de plataforma se recopilan de los recursos de Azure con una frecuencia de un minuto, a menos que se especifique lo contrario en la definición de la métrica. 

- **Aplicaciones**. Las métricas se crean mediante Application Insights para las aplicaciones supervisadas con el fin de ayudarle a detectar problemas de rendimiento y a realizar un seguimiento de las tendencias de uso de la aplicación. Los valores incluyen _Tiempo de respuesta del servidor_ y _Excepciones del explorador_.

- **Agentes de máquina virtual**. Las métricas se recopilan desde el sistema operativo invitado de una máquina virtual. Se pueden habilitar las métricas del sistema operativo invitado para máquinas virtuales Windows con la [extensión de diagnóstico de Windows](../agents/diagnostics-extension-overview.md) y para las máquinas virtuales Linux con el [agente de Telegraf de InfluxData](https://www.influxdata.com/time-series-platform/telegraf/).

- **Métricas personalizadas**. Puede definir métricas además de las métricas estándar que están disponibles automáticamente. También se pueden [definir métricas personalizadas en la aplicación](../app/api-custom-events-metrics.md) que se supervisa mediante Application Insights o crear métricas personalizadas para un servicio de Azure con la [API de métricas personalizadas](./metrics-store-custom-rest-api.md).

Para ver una lista completa de los orígenes de datos que pueden enviar datos a métricas de Azure Monitor, consulte [¿Qué supervisa Azure Monitor?](../monitor-reference.md)

## <a name="metrics-explorer"></a>Explorador de métricas
Use el [Explorador de métricas](metrics-charts.md) para analizar de forma interactiva los datos en la base de datos de métricas y representar los valores de varias métricas con el tiempo. Puede anclar los gráficos a un panel para verlos con otras visualizaciones. También puede recuperar las métricas con la [API REST de supervisión de Azure](./rest-api-walkthrough.md).

![Captura de pantalla de un grafo de ejemplo en el Explorador de métricas que muestra las solicitudes de servidor, el tiempo de respuesta del servidor y las solicitudes con error.](media/data-platform-metrics/metrics-explorer.png)

Para más información, consulte [Introducción al Explorador de métricas de Azure Monitor](./metrics-getting-started.md).

## <a name="data-structure"></a>Estructura de los datos
Los datos que recopilan las métricas de Azure Monitor se almacenan en una base de datos de serie temporal que está optimizada para analizar los datos con marca de tiempo. Cada conjunto de valores de métricas es una serie temporal con las siguientes propiedades:

* Hora en que se recopiló el valor.
* El recurso con el que el valor está asociado.
* Espacio de nombres que actúa como categoría para la métrica.
* Un nombre para la métrica.
* El propio valor.
* [Varias dimensiones](#multi-dimensional-metrics) cuando están presentes. Tenga en cuenta que las métricas personalizadas están limitadas a 10 dimensiones.

## <a name="multi-dimensional-metrics"></a>Métricas multidimensionales
Uno de los desafíos de los datos de métricas es que a menudo tienen información limitada para proporcionar contexto para los valores recopilados. Azure Monitor aborda este desafío con métricas multidimensionales. 

Las dimensiones de una métrica son pares nombre-valor que transportan datos adicionales para describir el valor de la métrica. Por ejemplo, una métrica llamada _Espacio en disco disponible_ podría tener una dimensión llamada _Unidad_ con los valores _C:_ y _D:_ . Esa dimensión permitiría ver el espacio en disco disponible en todas las unidades o en cada una de forma individual.

En el ejemplo siguiente se muestran dos conjuntos de datos para una métrica hipotética denominada _Rendimiento de la red_. El primer conjunto de datos no tiene ninguna dimensión. El segundo conjunto de datos muestra los valores con dos dimensiones: _IP_ y _Dirección_.

### <a name="network-throughput"></a>Capacidad de proceso de la red

| Timestamp     | Valor de métrica |
| ------------- |:-------------|
| 9/8/2017 8:14 | 1331,8 Kbps |
| 9/8/2017 8:15 | 1141,4 Kbps |
| 9/8/2017 8:16 | 1110,2 Kbps |

Esta métrica no dimensional solo puede responder a una pregunta básica, como: "¿cuál era el rendimiento de la red en un momento dado?".

### <a name="network-throughput-and-two-dimensions-ip-and-direction"></a>Rendimiento de la red y dos dimensiones ("IP" y "Dirección")

| Timestamp     | Dimensión “IP”   | Dimensión “Dirección” | Valor de métrica|
| ------------- |:-----------------|:------------------- |:-----------|
| 9/8/2017 8:14 | IP="192.168.5.2" | Dirección="Envío"    | 646,5 Kbps |
| 9/8/2017 8:14 | IP="192.168.5.2" | Dirección="Recepción" | 420,1 Kbps |
| 9/8/2017 8:14 | IP="10.24.2.15"  | Dirección="Envío"    | 150,0 Kbps |
| 9/8/2017 8:14 | IP="10.24.2.15"  | Dirección="Recepción" | 115,2 Kbps |
| 9/8/2017 8:15 | IP="192.168.5.2" | Dirección="Envío"    | 515,2 Kbps |
| 9/8/2017 8:15 | IP="192.168.5.2" | Dirección="Recepción" | 371,1 Kbps |
| 9/8/2017 8:15 | IP="10.24.2.15"  | Dirección="Envío"    | 155,0 Kbps |
| 9/8/2017 8:15 | IP="10.24.2.15"  | Dirección="Recepción" | 100,1 Kbps |

Esta métrica puede responder a preguntas como: "¿cuál era el rendimiento de la red en cada dirección IP?" y "¿cuántos datos fueron los enviados frente a los recibidos?" Las métricas multidimensionales llevan valores de análisis y diagnóstico adicionales en comparación con las métricas no dimensionales.

### <a name="viewing-multi-dimensional-performance-counter-metrics-in-metrics-explorer"></a>Visualización de métricas de contador de rendimiento multidimensionales en el Explorador de métricas 
No es posible enviar métricas de contador de rendimiento que contengan un asterisco (\*) a Azure Monitor a través de la API de métricas clásicas de invitado. Esta API no puede mostrar métricas que contengan un asterisco porque indica que es una métrica multidimensional, y las métricas clásicas no las admiten.

Para configurar y ver las métricas multidimensionales de contador de rendimiento del sistema operativo invitado mediante la extensión de diagnósticos de Azure, siga estos pasos:

1. Vaya a la página **Configuración de diagnóstico** de la máquina virtual.
2. Seleccione la pestaña **Contadores de rendimiento**. 
3. Seleccione **Personalizado** para configurar los contadores de rendimiento que quiere recopilar.

   ![Captura de pantalla de la sección de contadores de rendimiento de la página de configuración de diagnóstico.](media/data-platform-metrics/azure-monitor-perf-counter.png)

4. Seleccione **Receptores**. A continuación, seleccione **Habilitado** para enviar los datos a Azure Monitor.

   ![Captura de pantalla de la sección de receptores de la página de configuración de diagnóstico.](media/data-platform-metrics/azure-monitor-sink.png)

5. Para ver la métrica en Azure Monitor, seleccione **Invitado de la máquina virtual** en la lista desplegable **Espacio de nombres de métricas**.

   ![Captura de pantalla del espacio de nombres de métricas.](media/data-platform-metrics/vm-guest-namespace.png)

6. Seleccione **Apply splitting** (Aplicar separación) y rellene los detalles para dividir la métrica por instancia. A continuación, puede descomponer la métrica en cada uno de los valores posibles representados por el asterisco en la configuración. En este ejemplo, el asterisco representa los volúmenes de disco lógico más el total.

   ![Captura de pantalla de la división de métricas por instancia.](media/data-platform-metrics/split-by-instance.png)

## <a name="retention-of-metrics"></a>Retención de métricas
En la mayoría de los recursos de Azure, las métricas de plataforma se almacenan durante 93 días. Hay algunas excepciones:

- **Métricas clásicas del sistema operativo invitado**: son contadores de rendimiento que recopila la [extensión de diagnósticos de Windows](../agents/diagnostics-extension-overview.md) o la [extensión de diagnósticos de Linux](../../virtual-machines/extensions/diagnostics-linux.md) y se enrutan a una cuenta de almacenamiento de Azure. La retención garantizada de estas métricas es de al menos 14 días, aunque no se escribe ninguna fecha de expiración en la cuenta de almacenamiento. 
  
  Por motivos de rendimiento, el portal limita la cantidad de datos que muestra en función del volumen. Por lo tanto, el número real de días que el portal recupera puede ser superior a 14 si el volumen de datos que se escribe no es muy grande. 

- **Métricas del sistema operativo invitado enviadas a las métricas de Azure Monitor**: son contadores de rendimiento que recopila la [extensión de diagnósticos de Windows](../agents/diagnostics-extension-overview.md) y se envían al [receptor de datos de Azure Monitor](../agents/diagnostics-extension-overview.md#data-destinations), al [agente de Telegraf de InfluxData](https://www.influxdata.com/time-series-platform/telegraf/) en máquinas Linux o al reciente [agente de Azure Monitor](../agents/azure-monitor-agent-overview.md) mediante reglas de recopilación de datos. La retención de estas métricas es de 93 días.

- **Métricas del sistema operativo invitado recopiladas por el agente de Log Analytics**: son contadores de rendimiento recopilados por el agente de Log Analytics y enviados a un área de trabajo de Log Analytics. La retención de estas métricas es de 31 días y se puede ampliar hasta a 2 años.

- **Métricas basadas en registros de Application Insights**. En segundo plano, las [métricas basadas en registros](../app/pre-aggregated-metrics-log-metrics.md) se traducen en consultas de registros. Su retención es variable y coincide con la de eventos de registros subyacentes (entre 31 días y 2 años). Para los recursos de Application Insights, los registros se almacenan durante 90 días.

> [!NOTE]
> También puede [enviar métricas de plataforma de recursos de Azure Monitor a un área de trabajo de Log Analytics](./resource-logs.md#send-to-azure-storage) si quiere conocer las tendencias a largo plazo.

> [!NOTE]
> Como se ha mencionado anteriormente, en la mayoría de los recursos de Azure, las métricas de plataforma se almacenan durante 93 días. Sin embargo, solo puede realizar consultas (en el icono de **Métricas**) durante no más de 30 días en un único gráfico. Esta limitación no se aplica a las métricas basadas en registros. 
>
> Si ve un gráfico en blanco o el gráfico solo muestra parte de los datos de métricas, compruebe que la diferencia entre las fechas de inicio y finalización en el selector de tiempo no supere el intervalo de 30 días. Después de que haya seleccionado un intervalo de 30 días, puede [desplazarse de forma lateral](./metrics-charts.md#pan) por el gráfico para ver el período de retención completo.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [plataforma de datos de Azure Monitor](../data-platform.md).
- Más información sobre los [datos de registro en Azure Monitor](../logs/data-platform-logs.md).
- Obtenga información sobre los [Orígenes de datos de supervisión para Azure Monitor](../agents/data-sources.md) disponibles para diferentes recursos de Azure.