---
title: 'Inicio rápido: Portal web de Metrics Advisor'
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo empezar a usar el portal web de Metrics Advisor.
services: cognitive-services
author: mrbullwinkle
ms.author: mbullwin
manager: nitinme
ms.date: 09/30/2020
ms.topic: quickstart
ms.service: applied-ai-services
ms.subservice: metrics-advisor
ms.custom:
- mode-portal
ms.openlocfilehash: 324b84255bd5939940a7819c521a452c1c49c8e8
ms.sourcegitcommit: 192444210a0bd040008ef01babd140b23a95541b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114342489"
---
# <a name="quickstart-monitor-your-first-metric-using-the-web-portal"></a>Inicio rápido: Supervisión de su primera métrica con el portal web

Al aprovisionar una instancia de Metrics Advisor, puede usar las API y el área de trabajo basada en web para trabajar con el servicio. El área de trabajo basada en web se puede usar para empezar a trabajar fácil y rápidamente con el servicio. También proporciona una forma visual de configurar valores, personalizar el modelo y realizar análisis de la causa principal. 

* Incorporación de los datos de métricas
* Vista de métricas y visualizaciones
* Ajuste preciso de las configuraciones de detección
* Exploración de información de diagnóstico
* Creación y suscripción a alertas de anomalías

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción a Azure: [cree una cuenta gratuita](https://azure.microsoft.com/free/cognitive-services)
* Una vez que tenga la suscripción de Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="cree un recurso de Metrics Advisor"  target="_blank">create a Metrics Advisor resource </a> en Azure Portal para implementar la instancia de Metrics Advisor.  

    
> [!TIP]
> * Puede que se tarde entre 10 y 30 minutos en implementar el recurso de Metrics Advisor. Una vez que se implemente correctamente, seleccione **Ir al recurso**.
> * Si desea usar la API REST para interactuar con el servicio, necesitará la clave y el punto de conexión del recurso que cree. Puede encontrarlos en la pestaña **Keys and endpoints** (Claves y puntos de conexión) del recurso creado.


En este documento se usa una instancia de SQL Database como ejemplo para crear el primer monitor.

## <a name="sign-in-to-your-workspace"></a>Inicio de sesión en su área de trabajo

Tras crear el recurso, inicie sesión en el [portal de Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2143774) con una cuenta de Active Directory. En la página de aterrizaje, seleccione el **directorio**, la **suscripción** y el **área de trabajo** que acaba de crear y, después, seleccione **Introducción**. Para incorporar datos de una serie temporal, seleccione **Add data feed** (Agregar fuente de distribución de datos) datos en el menú de la izquierda.

 
Actualmente, puede crear un recurso de Metrics Advisor en cada una de las regiones disponibles. Las áreas de trabajo se pueden cambiar en el portal de Metrics Advisor en cualquier momento.


## <a name="onboard-time-series-data"></a>Incorporación de datos de serie temporal

Metrics Advisor proporciona conectores para diferentes orígenes de datos, como SQL Database, Azure Data Explorer y Azure Table Storage. Los pasos para la conexión de datos son similares para los diferentes conectores, aunque algunos parámetros de configuración pueden variar. Para ver distintas configuraciones de una conexión de datos, consulte [Conexión de datos de diferentes fuentes de distribución de datos](../data-feeds-from-different-sources.md).

En este inicio rápido se usa una instancia de SQL Database como ejemplo. También puede introducir sus propios datos siguiendo los mismos pasos.


### <a name="data-schema-requirements-and-configuration"></a>Configuración y requisitos del esquema de datos

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings-and-query"></a>Configuración de los valores de conexión y consulta

[Agregue las fuentes de distribución de datos](../how-tos/onboard-your-data.md), para lo que debe conectarse al origen de datos de su serie temporal. Para empezar, seleccione los parámetros siguientes:

* **Source Type** (Tipo de origen): tipo de origen de datos donde se almacenan los datos de serie temporal.
* **Granularity** (Granularidad): intervalo entre los puntos de datos consecutivos de los datos de serie temporal, por ejemplo, anualmente, mensualmente o diariamente. La personalización más baja del intervalo admitida es de 60 segundos.
* **Ingest data since (UTC)** (Ingesta de datos desde [UTC]): hora de inicio de la primera marca de tiempo que se va a ingerir. 


<!-- Next, specify the **Connection string** with the credentials for your data source, and a custom **Query**, see [how to write a valid query](../tutorials/write-a-valid-query.md) for more information. -->

:::image type="content" source="../media/connection-settings.png" alt-text="Configuración de conexión" lightbox="../media/connection-settings.png":::


### <a name="load-data"></a>Cargar datos

Después de especificar la cadena de conexión y la cadena de consulta, seleccione **Cargar datos**. Dentro de esta operación, Metrics Advisor comprobará la conexión y el permiso para cargar datos, los parámetros necesarios (@IntervalStart y @IntervalEnd) que deben usarse en la consulta y el nombre de columna del origen de datos. 

Si se produce un error en este paso:
1. En primer lugar, compruebe si la cadena de conexión es válida. 
2. Luego, confirme que hay permisos suficientes y que se concede acceso a la dirección IP del trabajo de ingesta.
3. Después, compruebe si se usan los parámetros necesarios (@IntervalStart y @IntervalEnd) en la consulta. 

### <a name="schema-configuration"></a>Configuración del esquema

Una vez que se carguen los datos, para lo que debe ejecutarse la consulta, y se muestren como se ve a continuación, seleccione los campos correspondientes.


|Número de selección  |Descripción  |Notas  |
|---------|---------|---------|
|**Timestamp**     | La marca de tiempo de un punto de datos. Si se omite, Metrics Advisor usará la marca de tiempo cuando se ingiera el punto de datos. Para cada fuente de distribución de datos, puede especificar como máximo una columna como una marca de tiempo.        | Opcional. Debe especificarse con una columna como máximo.       |
|**Medida**     |  Los valores numéricos de la fuente de distribución de datos. Para cada fuente de distribución de datos, puede especificar varias medidas, pero debe seleccionar al menos una columna como medida.        | Debe especificarse con al menos una columna.        |
|**Dimensión**     | Los valores de las categorías. Una combinación de valores diferentes identifica una serie temporal de una única dimensión determinada; por ejemplo: Country (País), Language (Idioma) y Tenant (Inquilino). Puede seleccionar un número arbitrario de columnas como dimensiones o ningún valor. Nota: Si selecciona una columna que no es de cadena como dimensión, tenga cuidado con la explosión de dimensiones. | Opcional.        |
|**Omitir**     | Omite la columna seleccionada.        | Opcional. Para que los orígenes de datos admitan el uso de una consulta para obtener datos, no hay ninguna opción "Omitir".       |


:::image type="content" source="../media/schema-configuration.png" alt-text="Configuración del esquema" lightbox="../media/schema-configuration.png":::

Después de configurar el esquema, seleccione **Verify schema** (Comprobar esquema). En esta operación, Metrics Advisor realizará las siguientes comprobaciones:
- Si la marca de tiempo de los datos consultados está en un solo intervalo. 
- Si se han devuelto valores duplicados para la misma combinación de dimensiones dentro de un solo intervalo de métricas.  

### <a name="automatic-roll-up-settings"></a>Configuración de la acumulación automática

> [!IMPORTANT]
> Si desea habilitar el **análisis de la causa principal** y otras funcionalidades de diagnóstico, es necesario configurar los valores de acumulación automática. Una vez habilitado, no se puede cambiar la configuración de la acumulación automática.

Metrics Advisor puede realizar automáticamente la agregación (SUM, MAX, MIN...) en cada dimensión durante la ingesta y, después, crea una jerarquía, que se usará en el análisis de la causa principal y en otras características del diagnóstico. Consulte cómo [configurar la acumulación automática](../how-tos/onboard-your-data.md#automatic-roll-up-settings) para obtener más información.

Proporcione un nombre personalizado a la fuente de distribución de datos, que se mostrará en el área de trabajo. Seleccione **Submit** (Enviar). 

## <a name="tune-detection-configuration"></a>Ajuste de la configuración de detección

Una vez agregada la fuente de distribución de datos, Metrics Advisor intentará ingerir datos de métricas desde la fecha de inicio especificada. Los datos tardarán un tiempo en ingerirse por completo y para ver el estado de la ingesta es preciso seleccionar **Ingestion progress** (Progreso de la ingesta) en la parte superior de la página de la fuente de distribución de datos. Si se ingieren datos, Metrics Advisor aplicará la detección y continuará supervisando el origen para detectar nuevos datos.

Cuando se aplique la detección, seleccione una de las métricas que aparecen en la fuente de distribución de datos para buscar la **página de detalles de la métrica** para: 
- Ver los sectores de todas las series temporales en esta métrica.
- Actualizar la configuración de la detección para cumplir los resultados esperados.
- Configuración de la notificación de anomalías detectadas

:::image type="content" source="../media/metric-details.png" alt-text="Detalles de la métrica" lightbox="../media/metric-details.png":::

## <a name="view-the-diagnostic-insights"></a>Visualización de la información de diagnóstico

Después de ajustar la configuración de detección, las anomalías encontradas deben reflejar las anomalías reales de los datos. Metrics Advisor realiza un análisis de las métricas multidimensionales para buscar la causa principal en una dimensión concreta y también el análisis de métricas cruzadas mediante "Gráfico de métricas". 

Para ver la información de diagnóstico, seleccione los puntos rojos de las visualizaciones de las series temporales, que representan las anomalías detectadas. Aparecerá una ventana con un vínculo a la página de análisis de incidentes. 

:::image type="content" source="../media/incident-link.png" alt-text="Vínculo del incidente" lightbox="../media/incident-link.png":::

Después de seleccionar el vínculo, pasará a la página de análisis de incidentes, en la que se realiza un análisis de un grupo de anomalías relacionadas con abundante información de diagnóstico. Hay tres pasos principales para diagnosticar un incidente:

### <a name="check-summary-of-current-incident"></a>Comprobar el resumen del incidente actual

En la parte superior, habrá un resumen, que incluye información básica, acciones y seguimientos, y una causa principal analizada. La información básica incluye la "serie más afectada" con un diagrama, "la hora de inicio y finalización del impacto", "la gravedad del incidente" y "el número total de anomalías incluidas".

La causa principal analizada es un resultado analizado automático. Metrics Advisor realiza análisis de todas las anomalías capturadas en series temporales dentro de una métrica con valores de dimensión diferentes en la misma marca de tiempo. Luego, realiza la correlación, agrupación en clústeres para agrupar anomalías relacionadas y genera un consejo de causa principal.

:::image type="content" source="../media/diagnostics/incident-summary.png" alt-text="Resumen de diagnóstico de incidentes" lightbox="../media/diagnostics/incident-summary.png":::

Sobre esta base, ya puede obtener una vista clara no solo del estado anómalo actual, sino también del impacto del incidente y de la potencial causa principal. De este modo, podrá tomar medidas inmediatas para resolver el incidente lo antes posible. 

### <a name="view-cross-dimension-diagnostic-insights"></a>Ver la información del diagnóstico entre dimensiones

Después de obtener información básica y una conclusión del análisis automático, puede obtener información más detallada sobre el estado anómalo en otras dimensiones dentro de la misma métrica de forma holística mediante el **"Árbol de diagnóstico"** .

En el caso de las métricas con varias dimensiones, Metrics Advisor clasifica la serie temporal en una jerarquía, que se denomina "Árbol de diagnóstico". Por ejemplo, dos dimensiones supervisan una métrica "revenue": "region" y "category". A pesar de los valores de dimensión concretos, es preciso tener un valor de dimensión **agregado**, como **"SUM"** . Luego, la serie temporal de "region" = **"SUM"** y de "category" = **"SUM"** se clasificará como el nodo principal dentro del árbol. Cada vez que se captura una anomalía en la dimensión **"SUM"** , se puede explorar en profundidad y analizar para localizar qué valor de dimensión específico ha sido el que más ha contribuido a la anomalía del nodo primario. Haga clic en cada nodo para expandir la información detallada.

:::image type="content" source="../media/diagnostics/cross-dimension-diagnostic.png" alt-text="Vista de dimensiones cruzadas de diagnóstico de incidentes" lightbox="../media/diagnostics/cross-dimension-diagnostic.png":::

### <a name="view-cross-metrics-diagnostic-insights-using-metrics-graph"></a>Visualización de la información de diagnóstico entre métricas mediante un "gráfico de métricas"

A veces, es difícil analizar un problema mediante la comprobación del estado anómalo de una sola métrica, es necesario poner en correlación varias métricas. Los clientes pueden configurar un "gráfico de métricas" que indica las relaciones entre las métricas. Al aprovechar el resultado de diagnóstico entre dimensiones anterior, la causa principal se limita a un valor de dimensión concreto. A continuación, use el "gráfico de métricas" y filtre por la dimensión de la causa principal analizada para comprobar el estado de la anomalía en otras métricas.
Después de hacer clic en el vínculo, se mostrará la página de análisis de incidentes, en la que se analiza la anomalía correspondiente con abundante información de diagnóstico. Hay tres secciones en la página de detalles del incidente que corresponden a tres pasos principales para diagnosticar un incidente. 

:::image type="content" source="../media/diagnostics/cross-metrics-analysis.png" alt-text="Análisis entre métricas de diagnóstico de incidentes" lightbox="../media/diagnostics/cross-metrics-analysis.png":::

También puede desplazarse por distinta información de diagnóstico con características adicionales para explorar en profundidad las anomalías por dimensión, ver anomalías similares y realizar una comparación entre las métricas. Encontrará más información en [Cómo diagnosticar un incidente](../how-tos/diagnose-an-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Notificación cuando se encuentren nuevas anomalías

Si desea recibir una alerta cuando se detecte una anomalía en los datos, puede crear una suscripción para una o varias de sus métricas. Metrics Advisor usa enlaces para enviar alertas. Se admiten tres tipos de enlaces: enlace de correo electrónico, webhook y Azure DevOps. Aquí se usará un webhook como ejemplo. 

### <a name="create-a-web-hook"></a>Creación de un webhook

Un webhook es el punto de entrada para notificar anomalías mediante programación desde el servicio Metrics Advisor, que llama a una API proporcionada por el usuario cuando se desencadena una alerta. Para obtener información sobre cómo crear un enlace, consulte la sección sobre la **creación de enlaces** del artículo sobre el [procedimiento para configurar alertas y obtener notificaciones mediante enlaces](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Configurar alertas

Después de crear un enlace, una configuración de alerta determina cómo y qué notificaciones de alerta deben enviarse. Puede establecer varias configuraciones de alerta para cada métrica. Dos configuraciones importantes son **Alert for** (Alerta para), que especifica las anomalías que se van a incluir, y **Filter anomaly options** (Filtrar opciones de anomalías), que define las anomalías que se incluirán en la alerta. Vea la sección **Adición o edición de la configuración de alertas** en [Procedimiento: Configuración de alertas y obtención de notificaciones mediante un enlace](../how-tos/alerts.md#add-or-edit-alert-settings) para obtener más información.


## <a name="next-steps"></a>Pasos siguientes

- [Incorporación de las fuentes de distribución de datos](../how-tos/onboard-your-data.md)
    - [Administración de las fuentes de distribución de datos](../how-tos/manage-data-feeds.md)
    - [Configuraciones para distintos orígenes de datos](../data-feeds-from-different-sources.md)
- [Uso de la API REST o las bibliotecas de cliente](./rest-api-and-client-library.md)
- [Configuración de métricas y ajuste de la configuración de la detección](../how-tos/configure-metrics.md)
