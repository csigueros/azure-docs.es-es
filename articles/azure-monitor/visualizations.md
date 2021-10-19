---
title: Visualización de datos de Azure Monitor | Microsoft Docs
description: Aquí obtendrá un resumen de los métodos disponibles para visualizar los datos de métricas y de registro almacenados en Azure Monitor.
ms.topic: conceptual
author: rboucher
ms.author: robb
ms.date: 07/28/2021
ms.openlocfilehash: 257ca33952283ba3989ce11a2d933af3feceb8ac
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "129612358"
---
# <a name="visualize-data-from-azure-monitor"></a>Visualización de datos de Azure Monitor
Este artículo le ofrece un resumen de los métodos disponibles para visualizar los datos de métricas y de registro almacenados en Azure Monitor.

Visualizaciones como gráficos y diagramas le ayudarán a analizar los datos de supervisión para explorar en profundidad los problemas e identificar patrones. Según la herramienta que use, es posible que también tenga la opción de compartir visualizaciones con otros usuarios dentro y fuera de la organización.

## <a name="workbooks"></a>Workbooks
Los [libros](./visualize/workbooks-overview.md) son documentos interactivos que proporcionan información detallada sobre los datos, la investigación y colaboración en el equipo. Los libros son especialmente útiles para las guías de solución de problemas y las memorias posteriores a incidentes.

![Diagrama que muestra capturas de pantalla de tres páginas de un libro, entre las que se incluyen el Análisis de vistas de página, el Uso y el Tiempo invertido en la página.](media/visualizations/workbook.png)

Las ventajas de un libro incluyen:

- Compatibilidad con métricas y registros.
- Admite parámetros que habilitan los informes interactivos en los que, al seleccionar un elemento en una tabla, se actualizarán dinámicamente los gráficos y las visualizaciones asociados.
- Flujo similar al de los documentos.
- Opciones de libros personales o compartidos.
- Experiencia de creación sencilla y colaborativa.
- Plantillas que admiten la galería de plantillas públicas basadas en GitHub.


## <a name="azure-dashboards"></a>Paneles de Azure
Los [paneles de Azure](../azure-portal/azure-portal-dashboards.md) son la tecnología de panel principal de Azure. Son especialmente útiles para proporcionar una hoja de cristal sobre la infraestructura y los servicios de Azure, lo que le permite identificar rápidamente problemas importantes.

![Captura de pantalla que muestra un ejemplo de un panel de Azure con información personalizable.](media/visualizations/dashboard.png)

Aquí tiene un vídeo con un tutorial sobre la creación de paneles:

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AslH]

Las ventajas de un panel incluyen:

- Integración profunda en Azure. Las visualizaciones se pueden anclar a paneles desde varias páginas de Azure, que incluyen el [Explorador de métricas](essentials/metrics-charts.md), [Log Analytics](logs/log-analytics-overview.md) y [Application Insights](app/app-insights-overview.md).
- Compatibilidad con métricas y registros.
- La capacidad de combinar datos de varios orígenes, incluidos los resultados del [Explorador de métricas](essentials/metrics-charts.md), las [consultas del registro](logs/log-query-overview.md) y los [mapas](app/app-map.md) y la disponibilidad de [Application Insights](app/app-insights-overview.md).
- Opciones para paneles personales o compartidos a través de la integración con el [control de acceso basado en roles de Azure (RBAC de Azure)](../role-based-access-control/overview.md).
- Actualización automática. Las métricas se actualizan según el intervalo de tiempo con un mínimo de cinco minutos. Los registros se actualizan cada hora. Puede actualizar manualmente a petición si selecciona el icono **Actualizar** en una visualización o actualizando el panel completo.
- Paneles de métricas parametrizadas con marca de tiempo y parámetros personalizados.
- Opciones de diseño flexibles.
- Modo de pantalla completa.


Entre estas limitaciones se incluyen:

- Control limitado sobre las visualizaciones de registros sin compatibilidad con tablas de datos. Número total de series de datos que se limita a 50 con otras series de datos agrupadas en _otro_ depósito.
- Los gráficos de registro no son compatibles con los parámetros personalizados.
- Un período limitado para los gráficos de registros (últimos 30 días).
- Requisito de que los gráficos de registro se deben anclar a paneles compartidos.
- No hay interactividad con los datos del panel.
- Exploración en profundidad contextual limitada.

## <a name="grafana"></a>Grafana
[Grafana](https://grafana.com/) es una plataforma abierta que destaca en la creación de paneles operativos y en la visualización de datos de rendimiento y disponibilidad. Resulta útil para detectar, aislar y priorizar los incidentes operativos. Grafana incluye la compatibilidad integrada con Azure Monitor, así como el [complemento de Azure Monitor](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/) integrado. También admite la visualización de datos de tres servicios de Azure:
- Métricas de Azure Monitor, para obtener datos numéricos de series temporales de recursos de Azure. 
- Registros de Azure Monitor, para obtener registros y datos de rendimiento de recursos de Azure, con los que se pueden realizar consultas mediante el potente lenguaje de consulta Kusto (KQL).
- Azure Resource Graph, para consultar e identificar rápidamente recursos de Azure en suscripciones.

Grafana funciona con una amplia gama de orígenes de datos y permite crear paneles que combinan datos de supervisión de recursos de Azure, locales u otras nubes. 

![Captura de pantalla que muestra las visualizaciones de Grafana.](media/visualizations/grafana.png)

> [!IMPORTANT]
> Tenga en cuenta que Internet Explorer 11 solo es compatible con versiones de Grafana anteriores a la versión 6.0. Consulte [Exploradores admitidos para Grafana](https://grafana.com/docs/grafana/latest/installation/requirements/#supported-web-browsers).

Estas son las ventajas de Grafana, entre otras:

- Visualizaciones enriquecidas.
- Un ecosistema enriquecido de orígenes de datos, en el que se incluye la compatibilidad con orígenes de datos que no son de Azure.
- Interactividad de datos que incluye la función de acercamiento.
- Anotaciones que permiten la integración de datos de eventos en gráficos.
- Compatibilidad con parámetros, incluidas las [variables de Azure Monitor](https://grafana.com/docs/grafana/latest/datasources/azuremonitor/template-variables/).

Entre estas limitaciones se incluyen:

- No puede administrar los paneles y los modelos a través de Azure Resource Manager.
- El costo para admitir una infraestructura Grafana adicional o el costo adicional de Grafana Cloud.
## <a name="power-bi"></a>Power BI
[Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/) es particularmente útil para crear informes y paneles centrados en los negocios, así como informes de análisis de tendencias de KPI a largo plazo. Puede [importar los resultados de una consulta de registro](visualize/powerbi.md) en un conjunto de datos de Power BI para poder beneficiarse de sus características, como la combinación de datos de diferentes orígenes y el uso compartido de informes en la web y los dispositivos móviles.

![Captura de pantalla que muestra un informe de Power BI de ejemplo para las operaciones de TI.](media/visualizations/power-bi.png)

Las ventajas de Power BI incluyen:

- Visualizaciones enriquecidas.
- Amplia interactividad, incluidas las funciones de acercamiento y filtrado cruzado.
- Facilidad de uso compartido en toda la organización.
- Integración con otros datos desde varios orígenes de datos.
- Mejor rendimiento con los resultados almacenados en caché en un cubo.

Entre estas limitaciones se incluyen:

- Compatibilidad con registros, pero no con métricas.
- No hay integración de Azure. No puede administrar los paneles y los modelos a través de Azure Resource Manager.
- La necesidad de que los resultados de la consulta se importen en un modelo de Power BI para la configuración. 
- Limitación de tamaño de los resultados y de la actualización.
- La actualización de datos se limita a ocho veces al día.


## <a name="azure-monitor-partners"></a>Asociados de Azure Monitor
Algunos asociados de Azure Monitor proporcionan la funcionalidad de visualización. Para obtener una lista de asociados que Microsoft haya evaluado, consulte [Integraciones de los asociados de Azure Monitor](./partners.md). 

Un asociado de Azure Monitor puede proporcionarle visualizaciones rápidas para ahorrar tiempo. 

Entre estas limitaciones se incluyen:

- Posibles costos adicionales.
- La necesidad de tener tiempo para investigar y evaluar las ofertas de los asociados.

## <a name="your-own-custom-application"></a>Su propia aplicación personalizada
Puede acceder a los datos de registro y las métricas en Azure Monitor a través de una API mediante cualquier cliente de REST. A continuación, puede crear sus propios sitios web y aplicaciones personalizados.

Entre las ventajas de crear una aplicación personalizada se incluyen:

- Flexibilidad completa en cuanto a interfaz de usuario, visualización, interactividad y características.
- La capacidad de combinar datos de métricas y de registro con otros orígenes de datos.

Una desventaja importante es que requiere realizar un esfuerzo de ingeniería.

## <a name="azure-monitor-views"></a>Vistas de Azure Monitor

> [!IMPORTANT]
> Las vistas están en proceso de quedar en desuso. Para obtener instrucciones sobre cómo convertir las vistas en libros, consulte la [guía de transición](visualize/view-designer-conversion-overview.md).

Las [vistas de Azure Monitor](visualize/view-designer.md) le permiten crear visualizaciones personalizadas mediante los datos de registro. Las [soluciones de supervisión](insights/solutions.md) utilizan vistas para presentar los datos que recopilan.


![Captura de pantalla que muestra un icono de Solución de supervisión de contenedores y la vista detallada de Azure Monitor que se abre al seleccionarlo.](media/visualizations/view.png)

Entre las ventajas de las vistas se incluyen:

- Visualizaciones enriquecidas para datos de registro.
- La capacidad de exportar e importar las vistas para transferirlas a otros grupos de recursos y suscripciones.
- La integración en el modelo de administración de Azure Monitor con áreas de trabajo y soluciones de supervisión.
- [Filtran](visualize/view-designer-filters.md) los parámetros personalizados.
- Interactividad, con compatibilidad para la obtención de detalles de varios niveles (una vista que se profundiza en otra vista).

Entre estas limitaciones se incluyen:

- Compatibilidad con registros, pero no con métricas.
- No hay vistas personales. Una vista está disponible para todos los usuarios con acceso al área de trabajo.
- No hay actualizaciones automáticas.
- Opciones de diseño limitadas.
- No se admiten las consultas a través de varias áreas de trabajo o aplicaciones de Application Insights.
- Tamaño limitado de respuesta de consulta (8 MB) y tiempo de ejecución de consulta (110 segundos).

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información sobre los [datos que Azure Monitor recopila](data-platform.md).
- Obtenga información sobre los [paneles de Azure](../azure-portal/azure-portal-dashboards.md).
- Obtenga información acerca de el [Explorador de métricas](essentials/metrics-getting-started.md).
- Obtenga información sobre los [libros](./visualize/workbooks-overview.md).
- Obtenga información sobre la [importación de datos de registro en Power BI](./visualize/powerbi.md).
- Obtenga información sobre el [complemento de origen de datos de Azure Monitor para Grafana](./visualize/grafana-plugin.md).
- Obtenga información sobre las [vistas de Azure Monitor](visualize/view-designer.md).
