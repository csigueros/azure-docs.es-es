---
title: 'Procedimientos recomendados de Azure Monitor: análisis y visualizaciones'
description: Instrucciones y recomendaciones para personalizar las visualizaciones más allá de las características de análisis estándar en Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/18/2021
ms.openlocfilehash: c9f46404e1984c7f9eb4e8cea231229f31ffa8ad
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130231673"
---
# <a name="azure-monitor-best-practices---analyze-and-visualize-data"></a>Procedimientos recomendados de Azure Monitor: análisis y visualización de los datos
Este artículo forma parte del escenario de [recomendaciones para configurar Azure Monitor](best-practices.md). Describe las características integradas de Azure Monitor para analizar los datos recopilados y las opciones para crear visualizaciones personalizadas a fin de satisfacer los requisitos de los distintos usuarios de la organización. Visualizaciones como gráficos y diagramas le ayudarán a analizar los datos de supervisión para explorar en profundidad los problemas e identificar patrones.


## <a name="builtin-analysis-features"></a>Características de análisis integradas
En las secciones siguientes se describen las características de Azure Monitor que proporcionan un análisis de los datos recopilados sin ninguna configuración.
### <a name="overview-page"></a>Página de información general
La mayoría de los servicios de Azure tendrán una página de **información general** en Azure Portal en la que se incluya una sección **Supervisión** donde se muestren los gráficos recientes de las métricas críticas. Su finalidad es que los propietarios de servicios individuales evalúen rápidamente el rendimiento del recurso. La página se basa en métricas de plataforma que se recopilan de forma automática, por lo que no se requiere ninguna configuración para esta característica.

### <a name="metrics-explorer"></a>Explorador de métricas
El Explorador de métricas permite a los usuarios trabajar de forma interactiva con los datos de las métricas y crear las alertas correspondientes. La mayoría de los usuarios requerirá un entrenamiento mínimo para poder usar este explorador, pero deben estar familiarizados con las métricas que quieran analizar. No se requiere configuración alguna para esta característica una vez configurada la recopilación de datos. Las métricas de plataforma para los recursos de Azure estarán disponibles automáticamente. Las métricas de invitado para las máquinas virtuales estarán disponibles cuando se haya implementado en estas el agente de Azure Monitor y las métricas de aplicación estarán disponibles cuando se haya configurado Application Insights.


### <a name="log-analytics"></a>Log Analytics
Log Analytics permite que los usuarios puedan crear tanto consultas de registro, para trabajar interactivamente con los datos de este, como alertas de consulta de registros. Se requiere cierto entrenamiento para que los usuarios se familiaricen con el lenguaje de consulta, aunque pueden usar consultas precompiladas para los requisitos comunes. También puede agregar [paquetes de consulta](logs/query-packs.md) con consultas exclusivas para su organización. Esto permite que los usuarios familiarizados con el lenguaje de consulta puedan crear consultas para otros usuarios de la organización.


## <a name="workbooks"></a>Workbooks
Los [libros](./visualize/workbooks-overview.md) son la plataforma de visualización elegida para Azure que proporciona un lienzo flexible para el análisis de datos y la creación de informes visuales enriquecidos. Permiten acceder a varios orígenes de datos desde Azure y combinarlos en experiencias interactivas unificadas. Son especialmente útiles para preparar vistas de supervisión de E2E entre varios recursos de Azure.

Insights usa libros precompilados para presentar a los usuarios información crítica sobre el estado y el rendimiento de un servicio determinado. Puede acceder a una galería de libros adicionales en la pestaña **Libros** del menú Azure Monitor y crear libros personalizados para satisfacer los requisitos de los distintos usuarios.

![Diagrama que muestra capturas de pantalla de tres páginas de un libro, entre las que se incluyen el Análisis de vistas de página, el Uso y el Tiempo invertido en la página.](media/visualizations/workbook.png)

Entre los escenarios habituales para los libros se incluyen los siguientes:

- Creación de un informe interactivo con parámetros en el que, al seleccionar un elemento en una tabla, se actualizarán dinámicamente las visualizaciones y los gráficos asociados.
- Uso compartido de un informe con otros usuarios de la organización.
- Colaboración con otros autores de libros de la organización mediante una galería de plantillas públicas basadas en GitHub.



## <a name="azure-dashboards"></a>Paneles de Azure
Los [paneles de Azure](../azure-portal/azure-portal-dashboards.md) son especialmente útiles a la hora de proporcionar un único panel para la infraestructura y los servicios de Azure. Si bien un libro proporciona una funcionalidad más completa, un panel puede combinar datos de Azure Monitor con datos de otros servicios de Azure.

![Captura de pantalla que muestra un ejemplo de un panel de Azure con información personalizable.](media/visualizations/dashboard.png)

Aquí tiene un vídeo con un tutorial sobre la creación de paneles:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

Entre los escenarios habituales para los paneles se incluyen los siguientes:

- Creación de un panel que combine un gráfico de métricas y los resultados de una consulta de registro con datos operativos para servicios relacionados.
- Uso compartido de un panel con los propietarios de servicios a través de la integración con el [control de acceso basado en roles de Azure (RBAC de Azure)](../role-based-access-control/overview.md).
  

Consulte [Creación y uso compartido de paneles de datos de Log Analytics](visualize/tutorial-logs-dashboards.md) para obtener más información sobre cómo crear un panel que incluya datos de registros de Azure Monitor. Consulte [Creación de paneles de indicadores clave de rendimiento (KPI) personalizados con Azure Application Insights](app/tutorial-app-dashboards.md) para más información sobre cómo crear un panel que incluya datos de Application Insights. 



## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) es particularmente útil para crear informes y paneles centrados en los negocios, así como informes de análisis de tendencias de KPI a largo plazo. Puede [importar los resultados de una consulta de registro](./logs/log-powerbi.md) en un conjunto de datos de Power BI para poder beneficiarse de sus características, como la combinación de datos de diferentes orígenes y el uso compartido de informes en la web y los dispositivos móviles.

![Captura de pantalla que muestra un informe de Power BI de ejemplo para las operaciones de TI.](media/visualizations/power-bi.png)

Entre los escenarios comunes de Power BI se incluyen los siguientes:

- Visualizaciones enriquecidas.
- Amplia interactividad, incluidas las funciones de acercamiento y filtrado cruzado.
- Facilidad de uso compartido en toda la organización.
- Integración con otros datos desde varios orígenes de datos.
- Mejor rendimiento con los resultados almacenados en caché en un cubo.



## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) es una plataforma abierta que sobresale en paneles operativos. Es útil para detectar, aislar y evaluar las prioridades de los incidentes operativos, para lo que combina visualizaciones de orígenes de datos tanto de Azure como distintos, incluidas herramientas locales y de terceros, así como almacenes de datos de otras nubes. Grafana tiene complementos y plantillas de paneles populares para herramientas de APM como Dynatrace, New Relic y App Dynamics, lo que permite a los usuarios visualizar datos de la plataforma Azure junto con otras métricas de niveles superiores de la pila recopiladas por otras herramientas. También tiene complementos de AWS CloudWatch y GCP BigQuery para la supervisión de varias nubes en un único panel.




Puede agregar el [complemento de origen de datos de Azure Monitor en Grafana](visualize/grafana-plugin.md) a su suscripción de Azure para que visualice los datos de métricas de Azure.

![Captura de pantalla que muestra las visualizaciones de Grafana.](media/visualizations/grafana.png)


Entre los escenarios habituales para Grafana se incluyen los siguientes:

- Combinación de datos de eventos y de series temporales en un solo panel de visualización.
- Creación de un panel dinámico basado en la selección del usuario de variables dinámicas.
- Creación de un panel a partir de una plantilla creada y admitida por una comunidad.
- Creación de un escenario de BCDR independiente del proveedor que se ejecute en cualquier proveedor de nube o local.

## <a name="azure-monitor-partners"></a>Asociados de Azure Monitor
Algunos asociados de Azure Monitor proporcionan la funcionalidad de visualización. Para obtener una lista de asociados que Microsoft haya evaluado, consulte [Integraciones de los asociados de Azure Monitor](./partners.md). Un asociado de Azure Monitor puede proporcionar visualizaciones listas para usar a fin de ahorrar tiempo, aunque estas soluciones pueden tener un costo adicional.


## <a name="custom-application"></a>Aplicación personalizada
Después, puede compilar sus aplicaciones y sitios web personalizados propios con los datos de registro y de las métricas en Azure Monitor, a los que se accede a través de una API REST. Esto le proporciona flexibilidad completa en cuanto a interfaz de usuario, visualización, interactividad y características.


## <a name="next-steps"></a>Pasos siguientes
- Consulte [Alertas y acciones automatizadas](best-practices-alerts.md) para definir alertas y acciones automatizadas a partir de los datos de Azure Monitor.