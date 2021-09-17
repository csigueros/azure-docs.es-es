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
ms.openlocfilehash: 1ad7d21907e8be6de4c28881719cc31f31b02c7a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745970"
---
# <a name="quickstart-monitor-your-first-metric-by-using-the-web-portal"></a>Inicio rápido: Supervisión de la primera métrica con el portal web

Al aprovisionar una instancia de Azure Metrics Advisor, puede usar las API y el área de trabajo basada en web para trabajar con el servicio. El área de trabajo basada en web se puede usar para empezar a trabajar fácil y rápidamente con el servicio. También proporciona una forma visual de configurar valores, personalizar el modelo y realizar análisis de la causa principal. 

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. [cree una de forma gratuita](https://azure.microsoft.com/free/cognitive-services).
* Una vez que tenga la suscripción de Azure, <a href="https://go.microsoft.com/fwlink/?linkid=2142156"  title="Creación de un recurso de Metrics Advisor"  target="_blank">cree un recurso de Metrics Advisor </a> en Azure Portal para implementar la instancia de Metrics Advisor.  

    
> [!TIP]
> * Puede que el recurso de Metrics Advisor tarde entre 10 y 30 minutos en implementarse. Una vez que se implemente correctamente, seleccione **Ir al recurso**.
> * Si quiere usar la API REST para interactuar con el servicio, necesitará la clave y el punto de conexión del recurso que cree. Puede encontrarlos en la pestaña **Keys and endpoints** (Claves y puntos de conexión) del recurso creado.


En este documento se usa una instancia de SQL Database como ejemplo para crear el primer monitor.

## <a name="sign-in-to-your-workspace"></a>Inicio de sesión en su área de trabajo

Después de crear el recurso, inicie sesión en el [portal de Metrics Advisor](https://go.microsoft.com/fwlink/?linkid=2143774) con su cuenta de Active Directory. En la página de aterrizaje, seleccione el **directorio**, la **suscripción** y el **área de trabajo** que acaba de crear y, después, elija **Get started** (Comenzar). Para incorporar datos de serie temporal, seleccione **Add data feed** (Agregar fuente de distribución de datos) en el menú de la izquierda.

 
Actualmente, puede crear un recurso de Metrics Advisor en cada una de las regiones disponibles. Las áreas de trabajo se pueden cambiar en el portal de Metrics Advisor en cualquier momento.


## <a name="time-series-data"></a>Datos de serie temporal

Metrics Advisor proporciona conectores para diferentes orígenes de datos, por ejemplo, SQL Database, Azure Data Explorer y Azure Table Storage. Los pasos para conectar los datos son similares en los diferentes conectores, aunque algunos parámetros de configuración pueden variar. Para más información, consulte [Conexión con distintos orígenes de datos](../data-feeds-from-different-sources.md).

En este inicio rápido se usa una instancia de SQL Database como ejemplo. También puede ingerir sus propios datos siguiendo los mismos pasos.


### <a name="data-schema-requirements-and-configuration"></a>Configuración y requisitos del esquema de datos

[!INCLUDE [data schema requirements](../includes/data-schema-requirements.md)]

### <a name="configure-connection-settings-and-query"></a>Configuración de los valores de conexión y consulta

[Agregue las fuentes de distribución de datos](../how-tos/onboard-your-data.md), para lo que debe conectarse al origen de datos de su serie temporal. Para empezar, seleccione los parámetros siguientes:

* **Source Type** (Tipo de origen): tipo de origen de datos donde se almacenan los datos de serie temporal.
* **Granularidad**: intervalo entre puntos de datos consecutivos de los datos de serie temporal (por ejemplo, anualmente, mensualmente o diariamente). El intervalo más corto admitido es de 60 segundos.
* **Ingest data since (UTC)** (Ingesta de datos desde [UTC]): hora de inicio de la primera marca de tiempo que se va a ingerir. 


:::image type="content" source="../media/connection-settings.png" alt-text="Captura de pantalla que muestra la configuración de conexión." lightbox="../media/connection-settings.png":::


### <a name="load-data"></a>Cargar datos

Después de introducir las cadenas de conexión y consulta, seleccione **Load data** (Cargar datos). Metrics Advisor comprueba la conexión y el permiso para cargar datos, los parámetros necesarios usados en la consulta y el nombre de columna del origen de datos. 

Si se produce un error en este paso:
1. Compruebe si la cadena de conexión es válida. 
1. Confirme que hay permisos suficientes y que se concede acceso a la dirección IP del trabajo de ingesta.
1. Compruebe si se usan los parámetros necesarios (`@IntervalStart` y `@IntervalEnd`) en la consulta. 

### <a name="schema-configuration"></a>Configuración del esquema

Una vez que se carguen los datos mediante la ejecución de la consulta, seleccione los campos adecuados.


|Número de selección  |Descripción  |Notas  |
|---------|---------|---------|
|**Timestamp**     | La marca de tiempo de un punto de datos. Si se omite, Metrics Advisor la usa cuando se ingiere el punto de datos. Para cada fuente de distribución de datos, puede especificar como máximo una columna como una marca de tiempo.        | Opcional. Debe especificarse con una columna como máximo.       |
|**Medida**     |  Los valores numéricos de la fuente de distribución de datos. Para cada fuente de distribución de datos, puede especificar varias medidas, pero al menos una de ellas debe ser una columna.        | Debe especificarse con al menos una columna.        |
|**Dimensión**     | Los valores de las categorías. Una combinación de valores diferentes identifica una serie temporal determinada de una única dimensión. Algunos ejemplos son el país, el idioma y el inquilino. Puede seleccionar un número arbitrario de columnas como dimensiones o ningún valor. Si selecciona una columna que no es de cadena como dimensión, tenga cuidado con la explosión de dimensiones. | Opcional.        |
|**Omitir**     | Omite la columna seleccionada.        | Opcional. En el caso de los orígenes de datos que admiten el uso de una consulta para obtener datos, no hay ninguna opción de omisión.       |


:::image type="content" source="../media/schema-configuration.png" alt-text="Captura de pantalla que muestra la configuración de esquema." lightbox="../media/schema-configuration.png":::

Después de configurar el esquema, seleccione **Verify schema** (Comprobar esquema). Metrics Advisor realiza las siguientes comprobaciones:
- Que la marca de tiempo de los datos consultados esté en un solo intervalo. 
- Que no se hayan devuelto valores duplicados para la misma combinación de dimensiones dentro de un solo intervalo de métricas.  

### <a name="automatic-roll-up-settings"></a>Configuración de la acumulación automática

> [!IMPORTANT]
> Si quiere habilitar el análisis de la causa principal y otras funcionalidades de diagnóstico, configure las opciones de acumulación automática. Después de habilitar el análisis, no puede cambiar la configuración.

Metrics Advisor puede realizar automáticamente la agregación en cada dimensión durante la ingesta. Luego, el servicio crea una jerarquía que puede usar en el análisis de la causa principal y otras características de diagnóstico. Para más información, consulte [Configuración de la acumulación automática](../how-tos/onboard-your-data.md#automatic-roll-up-settings).

Proporcione un nombre personalizado para la fuente de distribución de datos (se mostrará en el área de trabajo). Seleccione **Submit** (Enviar). 

## <a name="tune-detection-configuration"></a>Ajuste de la configuración de detección

Una vez agregada la fuente de distribución de datos, Metrics Advisor intenta ingerir datos de métricas desde la fecha de inicio especificada. Los datos tardarán un tiempo en ingerirse por completo y para ver el estado de la ingesta es preciso seleccionar **Ingestion progress** (Progreso de la ingesta) en la parte superior de la página de la fuente de distribución de datos. Si se ingieren datos, Metrics Advisor aplicará la detección y continuará supervisando el origen para detectar nuevos datos.

Cuando se aplique la detección, seleccione una de las métricas que aparecen en la fuente de distribución de datos para buscar la **página de detalles de la métrica**. Aquí, puede hacer lo siguiente: 
- Ver los sectores de todas las series temporales de esta métrica.
- Actualizar la configuración de detección para cumplir los resultados esperados.
- Configurar la notificación de anomalías detectadas.

:::image type="content" source="../media/metric-details.png" alt-text="Captura de pantalla que muestra los detalles de las métricas." lightbox="../media/metric-details.png":::

## <a name="view-diagnostic-insights"></a>Visualización de la Información de diagnóstico

Después de ajustar la configuración de detección, las anomalías encontradas deben reflejar las anomalías reales de los datos. Metrics Advisor realiza análisis de métricas multidimensionales para encontrar la causa principal para una dimensión específica. El servicio también realiza análisis entre métricas mediante la característica de grafo de métricas. 

Para ver la información de diagnóstico, seleccione los puntos rojos de las visualizaciones de series temporales. Estos puntos rojos representan anomalías detectadas. Aparecerá una ventana con un vínculo a la página de análisis de incidentes. 

:::image type="content" source="../media/incident-link.png" alt-text="Captura de pantalla que muestra un vínculo a un incidente." lightbox="../media/incident-link.png":::

En la página de análisis de incidentes, verá un grupo de anomalías relacionadas y la información de diagnóstico. Las secciones siguientes cubren los pasos principales para diagnosticar un incidente.

### <a name="check-the-summary-of-the-current-incident"></a>Comprobación del resumen del incidente actual

Puede encontrar el resumen en la parte superior de la página de análisis. Este resumen incluye información básica, acciones y seguimientos, y una de las causas principales analizadas. La información básica incluye la serie más afectada con un diagrama, la hora de inicio y finalización del impacto, la gravedad y el número total de anomalías incluidas.

La causa principal analizada es un resultado que se analiza automáticamente. Metrics Advisor analiza todas las anomalías que se capturan en una serie temporal, dentro de una métrica con valores de dimensión diferentes con la misma marca de tiempo. Luego, el servicio realiza la correlación, agrupa las anomalías relacionadas con el grupo y genera consejos sobre las causas principales.

:::image type="content" source="../media/diagnostics/incident-summary.png" alt-text="Captura de pantalla que muestra un resumen de diagnóstico de incidentes." lightbox="../media/diagnostics/incident-summary.png":::

Basándose en estos consejos, puede obtener una visión clara no solo del estado anómalo actual, sino también del impacto del incidente y de la posible causa principal. Así, puede tomar medidas inmediatas para resolver el incidente. 

### <a name="view-cross-dimension-diagnostic-insights"></a>Ver la información del diagnóstico entre dimensiones

También puede obtener información más detallada sobre el estado anómalo de otras dimensiones dentro de la misma métrica de forma holística, mediante la característica de árbol de diagnóstico.

En el caso de las métricas con varias dimensiones, Metrics Advisor clasifica la serie temporal en una jerarquía (llamada árbol de diagnóstico). Por ejemplo, dos dimensiones supervisan una métrica de ingresos: región y categoría. Debe tener un valor de dimensión agregado, como `SUM`. Luego, la serie temporal de `region = SUM` y `category = SUM` se clasifica como el nodo raíz dentro del árbol. Cada vez que se captura una anomalía en la dimensión `SUM`, puede analizarla para encontrar qué valor de dimensión específico ha sido el que más ha contribuido a la anomalía del nodo principal. Seleccione cada nodo para expandirlo y ver información detallada.

:::image type="content" source="../media/diagnostics/cross-dimension-diagnostic.png" alt-text="Captura de pantalla que muestra la vista entre dimensiones de diagnóstico de incidentes." lightbox="../media/diagnostics/cross-dimension-diagnostic.png":::

### <a name="view-cross-metrics-diagnostic-insights"></a>Visualización de la información de diagnóstico entre métricas

A veces, es difícil analizar un problema mediante la comprobación del estado anómalo de una sola métrica, es necesario poner en correlación varias métricas. Para ello, configure un grafo de métricas que indique las relaciones entre ellas. 

Mediante el resultado de diagnóstico entre dimensiones descrito en la sección anterior, puede identificar que la causa principal se limita a un valor de dimensión específico. Luego, puede usar el grafo de métricas y filtrar por la dimensión de la causa principal analizada para comprobar el estado de la anomalía en otras métricas.

:::image type="content" source="../media/diagnostics/cross-metrics-analysis.png" alt-text="Captura de pantalla que muestra el análisis entre métricas de diagnóstico de incidentes." lightbox="../media/diagnostics/cross-metrics-analysis.png":::

También puede usar otras características para crear tablas dinámicas con más datos de diagnóstico. Estas características le ayudan a explorar en profundidad las dimensiones de las anomalías, ver anomalías similares y realizar comparaciones entre métricas. Para más información, consulte [Diagnóstico de incidentes](../how-tos/diagnose-an-incident.md). 

## <a name="get-notified-when-new-anomalies-are-found"></a>Notificación cuando se encuentren nuevas anomalías

Si quiere recibir una alerta cuando se detecte una anomalía en los datos, puede crear una suscripción a una o varias de sus métricas. Metrics Advisor usa enlaces para enviar alertas. Se admiten tres tipos de enlaces: enlace de correo electrónico, webhook y Azure DevOps. Aquí se usará un webhook como ejemplo. 

### <a name="create-a-web-hook"></a>Creación de un webhook

En Metrics Advisor, puede usar un webhook para mostrar una anomalía mediante programación. El servicio llama a una API proporcionada por el usuario cuando se desencadena una alerta. Para más información, consulte [Creación de un enlace](../how-tos/alerts.md#create-a-hook). 

### <a name="configure-alert-settings"></a>Configurar alertas

Después de crear un enlace, una configuración de alerta determina cómo y qué notificaciones de alerta deben enviarse. Puede establecer varias configuraciones de alerta para cada métrica. Dos configuraciones importantes son **Alert for** (Alerta para), que especifica las anomalías que se van a incluir, y **Filter anomaly options** (Filtrar opciones de anomalías), que define las anomalías que se incluirán en la alerta. Para más información, consulte [Adición o edición de configuraciones de alertas](../how-tos/alerts.md#add-or-edit-alert-settings).


## <a name="next-steps"></a>Pasos siguientes

- [Adición de los datos de métricas a Metrics Advisor](../how-tos/onboard-your-data.md)
    - [Administración de las fuentes de distribución de datos](../how-tos/manage-data-feeds.md)
    - [Conexión con distintos orígenes de datos](../data-feeds-from-different-sources.md)
- [Uso de las API REST o las bibliotecas cliente para personalizar una solución](./rest-api-and-client-library.md)
- [Configuración de métricas y ajuste de la configuración de la detección](../how-tos/configure-metrics.md)
