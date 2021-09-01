---
title: 'Impacto del uso de Application Insights: Azure Monitor'
description: Analice cómo las diferentes propiedades pueden afectar a las tasas de conversión de partes de sus aplicaciones.
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: 3e484cb0f083292ba22c7a30c79202d01d1b10eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734371"
---
# <a name="impact-analysis-with-application-insights"></a>Análisis de impacto con Application Insights

La herramienta Impacto analiza cómo influyen los tiempos de carga y otras propiedades en las tasas de conversión para las distintas partes de su aplicación. Para ser más exactos, detecta cómo **cualquier dimensión** de una **vista de página**, **evento personalizado** o **solicitud** afecta el uso de una diferente **vista de página** o **evento personalizado**. 

## <a name="still-not-sure-what-impact-does"></a>¿Sigue sin tener claro la función de la herramienta Impacto?

La herramienta Impacto puede considerarse la herramienta por excelencia para establecer argumentos con alguien de su equipo acerca de cómo la lentitud de algún aspecto de su sitio está afectando a que los usuarios no se queden. Aunque los usuarios pueden tolerar una cierta lentitud, la herramienta Impacto proporciona una visión general de la mejor manera de equilibrar la optimización y el rendimiento para maximizar la conversión de usuario.

Pero analizar el rendimiento es solo un subconjunto de las capacidades de Impacto. Dado que la herramienta Impacto es compatible con dimensiones y eventos personalizados, la respuesta a preguntas acerca de cómo afecta la elección de explorador del usuario con las distintas tasas de conversión está a tan solo unos clics.

> [!NOTE]
> El recurso de Application Insights debe contener vistas de páginas o eventos personalizados para poder utilizar el libro Análisis del impacto. [Aprenda a configurar la aplicación para que recopile vistas de página automáticamente con el SDK de JavaScript de Application Insights](./javascript.md). También es importante tener en cuenta que, puesto que va a analizar correlaciones, el tamaño del ejemplo importa.

## <a name="impact-analytics-workbook"></a>Libro Análisis del impacto 

Para usar el libro Análisis del impacto, en la aplicación Application Insights vaya a **Uso** > **Impacto** y seleccione **Impact Analysis Workbook** (Libro Análisis del impacto). O bien, en la pestaña **Workbooks** (Libros), seleccione **Public Templates** (Plantillas públicas) y, luego, seleccione **User Impact Analysis** (Análisis del impacto en el usuario) en *Usage* (Uso).

:::image type="content" source="./media/usage-impact/workbooks-gallery.png" alt-text="Captura de pantalla de la galería de libros en plantillas públicas." lightbox="./media/usage-impact/workbooks-gallery.png":::


### <a name="using-the-workbook"></a>Uso del libro

:::image type="content" source="./media/usage-impact/selected-event.png" alt-text="Captura de pantalla en la que se muestra dónde elegir una vista de página inicial, un evento personalizado o una solicitud." lightbox="./media/usage-impact/selected-event.png":::

1. Seleccione un evento en la lista desplegable **Selected event** (Evento seleccionado).
2. Seleccione una métrica en la lista desplegable **analyze how its** (Analizar su).
3. Seleccione en evento en la lista desplegable **impacting event** (Evento con impacto). 
1. Si le ha gustado agregar un filtro, hágalo en **Add selected event filters** (Agregar filtros de eventos seleccionados) o en **Add impacting event filters** (Agregar filtros de eventos con impacto).


## <a name="is-page-load-time-impacting-how-many-people-convert-on-my-page"></a>¿El tiempo de carga de página está afectando a cuánta gente se convierte en mi página?

Para empezar a responder a preguntas con el libro Impacto, elija una vista de página inicial, un evento personalizado o una solicitud.

1. Seleccione un evento en la lista desplegable **Selected event** (Evento seleccionado).
2. Deje la lista desplegable **analyze how its** (Analizar su) en la selección predeterminada de **Duración** (en este contexto **Duración** es un alias para **Tiempo de carga de la página**.)
3. Para la lista desplegable **impacting event** (evento con impacto), seleccione un evento personalizado. Este evento debe corresponder a un elemento de la interfaz de usuario en la vista de página que seleccionó en el paso 1.


:::image type="content" source="./media/usage-impact/impact.png" alt-text="La captura de pantalla muestra un ejemplo con el evento seleccionado como Página principal analizado por duración." lightbox="./media/usage-impact/impact.png":::

## <a name="what-if-im-tracking-page-views-or-load-times-in-custom-ways"></a>¿Qué ocurre si sigo las vistas de página y los tiempos de carga de manera personalizada?

Impacto admite propiedades y medidas personalizadas y estándar, así que puede usar lo que prefiera. En lugar de duración, use filtros en los eventos principales y secundarios para obtener datos más específicos.

## <a name="do-users-from-different-countries-or-regions-convert-at-different-rates"></a>¿Las tasas de conversión de los usuarios varían en función de los países o regiones?

1. Seleccione un evento en la lista desplegable **Selected Event** (Evento seleccionado).
2. Elija "País o región" en la lista desplegable **analyze how its** (Analizar su).
3. En la lista desplegable **impacting event** (evento con impacto), seleccione un evento personalizado que corresponda a un elemento de la interfaz de usuario en la vista de página que eligió en el paso 1.

:::image type="content" source="./media/usage-impact/regions.png" alt-text="La captura de pantalla que muestra un ejemplo con el evento seleccionado como GET analizado por país y región." lightbox="./media/usage-impact/regions.png":::

## <a name="how-does-the-impact-analysis-workbook-calculate-these-conversion-rates"></a>¿Cómo calcula el libro Análisis de impacto estos tipos de conversión?

En segundo plano, el libro Análisis de impacto utiliza el [coeficiente de correlación de Pearson](https://en.wikipedia.org/wiki/Pearson_correlation_coefficient). Los resultados se calculan entre -1 y 1, donde -1 representa una correlación lineal negativa y 1 representa una correlación lineal positiva.

El análisis básico de cómo funciona Análisis de impacto es el siguiente:

_A_ = la vista de página/evento personalizado/solicitud principal que seleccionó en la primera lista desplegable (**evento seleccionado**).

_B_ = la vista de página/evento personalizado secundario que seleccionó (**afecta al uso de**).

Impacto examina una muestra de todas las sesiones de los usuarios en el intervalo de tiempo seleccionado. Para cada sesión, busca cada aparición de _A_.

A continuación las sesiones se dividen en dos tipos diferentes de _subsesiones_ basándose en una de estas dos condiciones:

- Una subsesión convertida se compone de una sesión que termina con un evento _B_ y abarca todos los eventos _A_ que se producen antes _B_.
- Se produce un subsesión no convertida cuando todos los eventos _A_ ocurren sin un terminal _B_.

La manera en la que se calcula el impacto varía en función de si estamos realizando el análisis por métricas o dimensiones. En el caso de las métricas, se calcula un promedio de todos los eventos _A_ en una subsesión se promedian. Mientras que para las dimensiones, el valor de cada _A_ contribuye _1/N_ al valor asignado a _B_ donde _N_ es el número de eventos _A_ en la subsesión.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre los libros, visite [la introducción a los libros](../visualize/workbooks-overview.md).
- Para habilitar las experiencias de uso, empiece por enviar [eventos personalizados](./api-custom-events-metrics.md#trackevent) o [vistas de páginas](./api-custom-events-metrics.md#page-views).
- Si ya ha enviado eventos personalizados o vistas de página, explore las herramientas de uso para obtener información sobre cómo los usuarios utilizan el servicio.
    - [Embudos](usage-funnels.md)
    - [Retención](usage-retention.md)
    - [Flujos de usuario](usage-flows.md)
    - [Libros](../visualize/workbooks-overview.md)
    - [Adición de contexto de usuario](./usage-overview.md)