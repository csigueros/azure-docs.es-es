---
title: Conclusiones del área de trabajo de Log Analytics
description: 'Información general sobre las conclusiones del área de trabajo de Log Analytics: ingesta, uso, estado, agentes y mucho más'
services: azure-monitor
ms.topic: conceptual
author: noakup
ms.author: noakuper
ms.date: 05/06/2021
ms.openlocfilehash: 450f4f8d93261412db4963579f8b8525c2028f46
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/26/2021
ms.locfileid: "110482971"
---
# <a name="log-analytics-workspace-insights-preview"></a>Conclusiones del área de trabajo de Log Analytics (versión preliminar)

Conclusiones del área de trabajo de Log Analytics (versión preliminar) proporciona una supervisión completa de las áreas de trabajo a través de una vista unificada del uso, el rendimiento, el estado, el agente, las consultas y el registro de cambios del área de trabajo. Este artículo le ayudará a saber cómo incorporar y usar Conclusiones del área de trabajo de Log Analytics (versión preliminar).

## <a name="overview-your-log-analytics-workspaces"></a>Información de las áreas de trabajo de Log Analytics

Al acceder a Conclusiones del área de trabajo de Log Analytics mediante Azure Monitor Insights, se muestra la perspectiva "A gran escala". Aquí puede ver cómo están repartidas las áreas de trabajo por todo el mundo, puede revisar sus detalles de retención, límites y licencias (codificados por colores) y también puede elegir un área de trabajo para ver sus conclusiones.


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-scale.png" alt-text="Captura de pantalla que muestra la lista de áreas de trabajo de Conclusiones del área de trabajo de Log Analytics" lightbox="media/log-analytics-workspace-insights-overview/at-scale-expanded.png":::


Para iniciar Conclusiones del área de trabajo de Log Analytics a gran escala, realice los pasos siguientes:

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).

2. Seleccione **Supervisión** en el panel izquierdo de Azure Portal y, en la sección Centro de información, seleccione **Conclusiones del área de trabajo de Log Analytics (versión preliminar)** .

## <a name="view-insights-for-a-log-analytics-workspace"></a>Visualización de las conclusiones de un área de trabajo de Log Analytics

Al iniciar las conclusiones en el contexto de un área de trabajo específica, se muestran datos enriquecidos y análisis del rendimiento, el uso, el estado, los agentes, las consultas y el registro de cambios del área de trabajo.


:::image type="content" source="media/log-analytics-workspace-insights-overview/at-resource.png" alt-text="Captura de pantalla que muestra la Información general de Conclusiones del área de trabajo de Log Analytics" lightbox="media/log-analytics-workspace-insights-overview/at-resource.png":::

Para acceder a Conclusiones del área de trabajo de Log Analytics:

1. Abra las Conclusiones del área de trabajo de Log Analytics desde Azure Monitor (como se explicó anteriormente).

2. Seleccione un área de trabajo en la que profundizar.

Or 

1. En Azure Portal, seleccione **Áreas de trabajo de Log Analytics**.

2. Elija un área de trabajo de Log Analytics.

3. Seleccione **Conclusiones** en el menú Área de trabajo (en Supervisión).

Los datos están organizados en pestañas y el intervalo de tiempo de la parte superior se aplica a todas las pestañas (el valor predeterminado es 24 horas). Algunos gráficos y tablas usan un intervalo de tiempo diferente, como se indica en sus títulos.


### <a name="overview-tab"></a>Pestaña Información general

En la pestaña **Información general** puede ver:

* Estadísticas y configuración principales.
    - El volumen de ingesta mensual del área de trabajo.
    - Número de máquinas que enviaron latidos; es decir, las máquinas que están conectadas a esta área de trabajo (en el intervalo de tiempo seleccionado).
    - Máquinas que no han enviado latidos en la última hora (en el intervalo de tiempo seleccionado).
    - El período de retención de datos configurado.
    - El límite diario configurado y la cantidad de datos que ya se han ingerido en el día más reciente.

* Cinco tablas principales: gráficos que analizan las cinco tablas más ingeridas en el último mes.
    - Volumen de datos ingeridos en cada tabla.
    - Ingesta diaria para cada una a fin de mostrar de forma visual los picos o caídas.
    - Anomalías de ingesta: una lista de picos y caídas identificados en la ingesta de estas tablas.


### <a name="usage-tab"></a>Pestaña Uso

#### <a name="usage-dashboard"></a>Panel de uso

Esta pestaña proporciona información sobre el uso del área de trabajo. La subpestaña del panel muestra los datos de ingesta de las tablas y tiene como valor predeterminado las cinco tablas más ingeridas en el intervalo de tiempo seleccionado (las mismas tablas que se muestran en la página Información general). Puede elegir las tablas que se mostrarán en la lista desplegable Tablas del área de trabajo. 

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage.png" alt-text="Captura de pantalla de la pestaña Uso del área de trabajo" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage.png":::

* Cuadrícula principal: aquí puede ver las tablas agrupadas por soluciones, así como información sobre cada tabla.
    - Cantidad de datos que se ingirieron en ella (durante el intervalo de tiempo seleccionado).
    - Porcentaje que esta tabla toma de todo el volumen de ingesta (durante el intervalo de tiempo seleccionado). Este dato ayuda a identificar las tablas que más afectan a la ingesta. En la captura de pantalla siguiente puede ver que AzureDiagnostics y ContainerLog representan más de dos tercios (64 %) de los datos ingeridos en esta área de trabajo.
    - Cuándo fue la última actualización de las estadísticas de uso con respecto a cada tabla; normalmente, se espera que las estadísticas de uso se actualicen cada hora. Dado que la actualización de las estadísticas de uso es una operación interna de servicio recurrente, un retraso en la actualización de los datos se señala con el único fin de que sepa cómo interpretar los datos correctamente. No es necesario que realice ninguna acción (como usuario).
    - Facturable: indica cuáles tablas se facturan y cuáles son gratuitas.

* Tabla: detalles específicos.

    En la parte inferior de la página, puede ver información detallada sobre la tabla seleccionada en la cuadrícula principal.
    - Volumen de ingesta: cuántos datos se ingieren en la tabla desde cada recurso y cómo se reparten a lo largo del tiempo. Los recursos que ingieren más del 30 % del volumen total enviado a esta tabla se marcan con un signo de advertencia para que los tenga en cuenta.
    - Latencia de ingesta: cuánto tiempo tardó la ingesta, analizado para los percentiles 50, 90 o 95 de las solicitudes enviadas a esta tabla. En el gráfico superior de esta área se muestra el tiempo total de ingesta de las solicitudes (para el percentil seleccionado) de un extremo a otro; es decir, desde el momento en que se produjo el evento y hasta que se ingirió en el área de trabajo.
    En el gráfico siguiente se muestran por separado la latencia del agente (el tiempo que tardó el agente en enviar el registro al área de trabajo) y la de la canalización (el tiempo que tardó el servicio en procesar los datos e insertarlos en el área de trabajo).
    :::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png" alt-text="Captura de pantalla de la subpestaña de latencia de ingestión en el uso del área de trabajo" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-ingestion-latency.png":::

#### <a name="additional-usage-queries"></a>Consultas de uso adicionales

La subpestaña Consultas adicionales expone las consultas que se ejecutan en todas las tablas del área de trabajo (en lugar de depender de los metadatos de uso, que se actualizan cada hora). Dado que sus consultas son mucho más amplias y menos eficaces, no se ejecutan automáticamente. Sin embargo, pueden obtener información interesante sobre qué recursos envían la mayoría de los registros al área de trabajo y, quizás, qué recursos afectan a la facturación.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png" alt-text="Captura de pantalla de la pestaña Consultas adicionales en el uso del área de trabajo" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-queries.png":::

Una consulta de este tipo puede ser "Qué recursos de Azure envían la mayoría de los registros a esta área de trabajo" (que muestra los 50 primeros registros).
En el demo de área de trabajo, puede ver claramente que tres clústeres de Kuberbetes envían muchos más datos que todos los demás recursos combinados, y uno en específico carga más el área de trabajo.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png" alt-text="Captura de pantalla de la pestaña Consultas adicionales en el uso del área de trabajo con los resultados de una consulta adicional" lightbox="media/log-analytics-workspace-insights-overview/workspace-usage-additional-query-run.png":::


### <a name="health-tab"></a>Pestaña Estado

Esta pestaña muestra el estado de mantenimiento del área de trabajo y cuándo se informó por última vez, así como los errores operativos y advertencias (recuperados de la tabla _LogOperation).

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-health.png" alt-text="Captura de pantalla de la pestaña Estado del área de trabajo" lightbox="media/log-analytics-workspace-insights-overview/workspace-health.png":::

### <a name="agents-tab"></a>Pestaña Agentes

Esta pestaña proporciona información sobre los agentes que envían registros a esta área de trabajo.
:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-agents.png" alt-text="Captura de pantalla de la pestaña Agentes del área de trabajo" lightbox="media/log-analytics-workspace-insights-overview/workspace-agents.png":::

* Errores y advertencias de operación: son errores y advertencias relacionados específicamente con los agentes. Están agrupados según el título del error o advertencia para ayudarle a obtener una vista más clara de los distintos problemas que pueden producirse, pero se pueden expandir para mostrar las horas exactas y los recursos a los que hacen referencia. Tenga en cuenta también que puede hacer clic en "Ejecutar consulta en registros" para consultar la tabla _LogOperation a través de la experiencia de registros, ver los datos sin procesar y analizarlos con más detalle.
* Agentes del área de trabajo: estos son los agentes que enviaron registros al área de trabajo durante el intervalo de tiempo seleccionado. Puede ver los tipos de agentes (directo, puerta de enlace, SCOM o servidores de administración de SCOM) y el estado de mantenimiento. Los agentes marcados como correctos no necesariamente funcionan de forma correcta; este valor solo indica que enviaron un latido durante la última hora. En la cuadrícula siguiente se desglosa un estado de mantenimiento más detallado.
* Actividad de agentes: esta cuadrícula muestra información sobre todos los agentes, ya sean correctos o incorrectos. En este caso, "Correcto" también indica únicamente que el agente envió un latido durante la última hora. Para comprender mejor su estado, revise la tendencia que se muestra en la cuadrícula, donde aparece cuántos latidos ha enviado este agente a lo largo del tiempo. El verdadero estado de mantenimiento solo se puede inferir si sabe cómo funciona el recurso supervisado; por ejemplo, si un equipo se apaga intencionadamente en momentos concretos, cabe esperar que los latidos del agente aparezcan de manera intermitente en un patrón coincidente.


### <a name="query-audit-tab"></a>Pestaña Auditoría de consulta

La auditoría de consultas crea registros sobre la ejecución de las consultas en el área de trabajo. Si están habilitados, estos datos son muy beneficiosos para comprender y mejorar el rendimiento, la eficacia y la carga de las consultas. Para habilitar la auditoría de consultas en el área de trabajo u obtener más información sobre ella, consulte [Auditoría de consultas en los registros de Azure Monitor](../logs/query-audit.md).

#### <a name="performance"></a>Rendimiento 
En esta pestaña se muestra:
* Duración de la consulta: la duración del percentil 95 y el percentil 50 (mediana) en ms, a lo largo del tiempo.
* Número de filas devueltas: percentil 95 y percentil 50 (mediana) de filas, a lo largo del tiempo.
* Volumen de datos procesados: percentil 95, percentil 50 y total de datos procesados en todas las solicitudes, a lo largo del tiempo.
* Códigos de respuesta: la distribución de los códigos de respuesta a todas las consultas del intervalo de tiempo seleccionado.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png" alt-text="Captura de pantalla de la pestaña Auditoría de consulta, subpestaña Rendimiento]" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-performance.png":::

#### <a name="slow-and-inefficient-queries"></a>Consultas lentas e ineficaces 
En esta pestaña se muestran dos cuadrículas que le ayudarán a identificar consultas lentas e ineficaces que quizá quiera volver a plantearse. Estas consultas no deben usarse en paneles o alertas, ya que crearán una carga crónica innecesaria en el área de trabajo.
* Consultas que consumen más recursos: las diez consultas con la mayor demanda de CPU, junto con el volumen de datos procesados (KB), el intervalo de tiempo y el texto de cada consulta.
* Consultas más lentas: las diez consultas más lentas, junto con el intervalo de tiempo y el texto de cada consulta.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png" alt-text="Captura de pantalla de la pestaña Auditoría de consultas, subpestaña Consultas lentas" lightbox="media/log-analytics-workspace-insights-overview/workspace-query-audit-slow-queries.png":::

#### <a name="query-users"></a>Consulta de usuarios 
En esta pestaña se muestra la actividad de los usuarios en esta área de trabajo:
* Consultas por usuario: cuántas consultas ejecutó cada usuario en el intervalo de tiempo seleccionado.
* Usuarios con limitaciones: usuarios que ejecutaron consultas que se limitaron (debido a un exceso de consultas en el área de trabajo).

### <a name="change-log-tab"></a>Pestaña Registro de cambios

En esta pestaña se muestran los cambios de configuración realizados en el área de trabajo durante los últimos 90 días (independientemente del intervalo de tiempo seleccionado) y quién los realizó.
Está diseñada para ayudarle a supervisar quién ha cambiado los valores de configuración importantes del área de trabajo, como el límite de datos o la licencia del área de trabajo.

:::image type="content" source="media/log-analytics-workspace-insights-overview/workspace-change-log.png" alt-text="Captura de pantalla de la pestaña Registro de cambios del área de trabajo" lightbox="media/log-analytics-workspace-insights-overview/workspace-change-log.png":::


## <a name="next-steps"></a>Pasos siguientes

Conozca los escenarios para los que están concebidos los libros, cómo crear informes y personalizar los ya existentes y otros muchos temas en el artículo [Crear informes interactivos con libros de Azure Monitor](../visualize/workbooks-overview.md).
