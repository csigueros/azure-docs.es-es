---
title: Incorporación de análisis a Bing Web Search API
titleSuffix: Azure Cognitive Services
description: Las estadísticas de Bing proporcionan análisis para Bing Image Search API. El análisis incluye el volumen de llamadas, las cadenas de consulta principales, la distribución geográfica y mucho más.
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 07/17/2019
ms.custom: seodec2018
ms.openlocfilehash: 878a6fd49a46b47c3243b168469a15909102a019
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128587069"
---
# <a name="add-analytics-to-the-bing-search-apis"></a>Incorporación de análisis a Bing Search APIs

> [!WARNING]
> Bing Search APIs se mueve de Cognitive Services a Bing Search Services. A partir del **30 de octubre de 2020**, las nuevas instancias de Bing Search deben aprovisionarse siguiendo el proceso documentado [aquí](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> El aprovisionamiento de Bing Search APIs con Cognitive Services será posible durante los próximos tres años o hasta que finalice el Contrato Enterprise, lo que suceda primero.
> Puede encontrar instrucciones sobre la migración en [Bing Search Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

Estadísticas de Bing proporcionan análisis para Bing Search APIs. Este análisis incluye el volumen de llamadas, las cadenas de consulta principales, la distribución geográfica y mucho más. Para habilitar Estadísticas de Bing en [Azure Portal](https://ms.portal.azure.com), vaya a su recurso de Azure y haga clic en **Enable Bing Statistics** (Habilitar estadísticas de Bing).

> [!IMPORTANT]
> * Estadísticas de Bing no están disponibles con los recursos en el plan de tarifa `F0` gratis.
> * No se puede usar ningún dato disponible a través del panel de Estadísticas de Bing para crear aplicaciones y distribuirlas a terceros.
> * Si habilita Estadísticas de Bing, la tasa de suscripción se incrementa ligeramente. Consulte [Precios](https://aka.ms/bingstatisticspricing) para obtener detalles.


En la siguiente imagen se muestra el análisis disponible para cada punto de conexión de Bing Search API.

![Distribución mediante la matriz de compatibilidad de punto de conexión](./media/bing-statistics/bing-statistics-matrix.png)

## <a name="access-your-analytics"></a>Acceder a su análisis

Bing actualiza los datos de análisis cada 24 horas y mantiene un historial de hasta 13 meses, al que puede acceder desde el [panel de análisis](https://bingapistatistics.com). Asegúrese de haber iniciado sesión con la misma cuenta de Microsoft (MSA) que usó registrarse en Estadísticas de Bing.

> [!NOTE]  
> * Las métricas pueden tardar hasta 24 horas en aparecer en el panel. El panel muestra la fecha y hora en la que los datos se actualizaron por última vez.  
> * Las métricas están disponibles desde el momento en que se habilita el complemento de Estadísticas de Bing.

## <a name="filter-the-data"></a>Filtrar los datos

De manera predeterminada, los diagramas y gráficos muestran todas las métricas y los datos a los que tiene acceso. Para filtrar los datos que se muestran en los diagramas y gráficos, seleccione los recursos, los mercados, los puntos de conexión y el período de informe que le interesa. Puede cambiar los filtros siguientes:

- **Identificador de recurso**: identificador de recurso único que identifica la suscripción de Azure. La lista contiene varios id. si se suscribe a más de un nivel de Bing Search APIs. De forma predeterminada, se seleccionan todos los recursos.  
  
- **Mercados**: mercados de los que proceden los resultados. Por ejemplo, en-us (inglés de los Estados Unidos). De forma predeterminada, se seleccionan todos los mercados. El mercado `en-WW` es el que Bing usa si la llamada no especifica un mercado y Bing no puede determinar el mercado del usuario.  
  
- **Puntos de conexión**: puntos de conexión de Bing Search APIs. La lista contiene todos los puntos de conexión para los cuales tiene una suscripción de pago. De forma predeterminada, se seleccionan todos los puntos de conexión.  

- **Período de tiempo**: período de notificación. Puede especificar:
  - **Todos**: Incluye datos de hasta 13 meses.  
  - **Últimas 24 horas**: Incluye el análisis de las últimas 24 horas.  
  - **Última semana**: Incluye el análisis de los 7 días anteriores.  
  - **Último mes**: Incluye el análisis de los 30 días anteriores.  
  - **Un intervalo de fechas personalizado**: Incluye el análisis del intervalo de fechas especificado, si está disponible.  

## <a name="charts-and-graphs"></a>Tablas y gráficos

El panel muestra las tablas y gráficos de las métricas disponibles para el punto de conexión seleccionado. No todas las métricas están disponibles para todos los puntos de conexión. Los diagramas y gráficos para cada punto de conexión son estáticos (no se pueden seleccionar los diagramas y gráficos que se muestran). El panel muestra solo los diagramas y gráficos para los que existen datos.

<!--
For example, if you don't include the User-Agent header in your calls, the dashboard will not include device-related graphs.
-->

A continuación se indican las métricas posibles y las restricciones del punto de conexión.

- **Volumen de llamadas**: muestra el número de llamadas realizadas durante el período de notificación. Si el período de informe es de un día, el gráfico muestra el número de llamadas realizadas por hora. En caso contrario, el gráfico muestra el número de llamadas realizadas por día del período de informe.  
  
  > [!NOTE]
  > El volumen de llamada puede diferir de los informes de facturación, que generalmente incluye solo las llamadas correctas.

- **Principales consultas**: muestra las consultas principales y el número de repeticiones de cada consulta durante el período de notificación. Se puede configurar el número de consultas que se muestran. Por ejemplo, puede mostrar las 25, 50 o 75 consultas principales. Las consultas principales no están disponibles para los siguientes punto de conexión:  

  - /images/trending
  - /images/details
  - /images/visualsearch
  - /videos/trending
  - /videos/details
  - /news
  - /news/trendingtopics
  - /suggestions  
  
  > [!NOTE]  
  > Algunos términos de consulta se pueden suprimir para quitar información confidencial, como el correo electrónico, los números de teléfono, el número de seguridad social, etcétera.

- **Distribución geográfica**: Los mercados en los que se originan los resultados de la búsqueda. Por ejemplo, `en-us` (inglés de los Estados Unidos). Bing usa el parámetro de consulta `mkt` para determinar el mercado, si se especifica. En caso contrario, Bing usa señales como la dirección IP del llamador para determinar el mercado.

- **Distribución de códigos de respuesta**: códigos de estado HTTP de todas las llamadas durante el período de notificación.

- **Distribución del origen de llamada**: tipos de exploradores utilizados por los usuarios. Por ejemplo, Microsoft Edge, Chrome, Safari y FireFox. Las llamadas realizadas desde fuera de un explorador (como bots, Postman o mediante cURL desde una aplicación de consola) se agrupan en Bibliotecas. El origen se determina mediante el valor del encabezado User-Agent de la solicitud. Si la solicitud no incluye el encabezado User-Agent, Bing intenta derivar el origen de otras señales.  

- **Distribución de búsqueda segura**: distribución de valores de búsqueda segura. Por ejemplo, desactivado, moderado o estricto. El parámetro de consulta `safeSearch` contiene el valor, si se especifica. En caso contrario, Bing establece el valor de forma predeterminada en moderado.  

- **Distribución de respuestas solicitadas**: Web Search API responde a lo que solicitó en el parámetro de consulta `responseFilter`.  

- **Distribución de respuestas devueltas**: respuestas que devuelve Web Search API en los resultados.

- **Distribución del servidor de respuestas**: servidor de la aplicación que atiende las solicitudes de API. Los valores posibles son Bing.com (para el tráfico procedente de los dispositivos de escritorio y portátiles) y Bing.com-mobile (para el tráfico procedente de los dispositivos móviles). El servidor se determina mediante el valor del encabezado User-Agent de la solicitud. Si la solicitud no incluye el encabezado User-Agent, Bing intenta derivar al servidor de otras señales.

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué son las versiones de Bing Search API?](bing-api-comparison.md)
* [Requisitos de uso y visualización de Bing Search API](use-display-requirements.md)