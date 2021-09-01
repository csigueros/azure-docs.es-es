---
title: Análisis de la retención de usuarios de aplicaciones web con Application Insights
description: ¿Cuántos usuarios regresan a la aplicación?
ms.topic: conceptual
author: lgayhardt
ms.author: lagayhar
ms.date: 07/30/2021
ms.openlocfilehash: de83c898d7a471892f61fa299cdf2091a1f228e7
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739796"
---
# <a name="user-retention-analysis-for-web-applications-with-application-insights"></a>Análisis de retención de usuarios para aplicaciones web con Application Insights

La característica de retención de [Application Insights](./app-insights-overview.md) le ayuda a analizar cuántos usuarios regresan a la aplicación, y con qué frecuencia realizan determinadas tareas o logran objetivos. Por ejemplo, si ejecuta un sitio de juegos, puede comparar el número de usuarios que regresan al sitio después de perder una partida con la cantidad que vuelven tras ganar. Esta información puede ayudarlo a mejorar la experiencia de los usuarios y la estrategia empresarial.

## <a name="get-started"></a>Introducción

Si aún no ve los datos en la herramienta Retención del portal de Application Insights, [obtenga información sobre cómo empezar a trabajar con las herramientas de uso](usage-overview.md).

## <a name="the-retention-workbook"></a>Libro de retención

Para usar el libro de análisis de la retención, en la aplicación Application Insights vaya a **Usage** > **Retention** (Uso > Retención) y seleccione **Retention Analysis Workbook** (Libro Análisis de la retención). O bien, en la pestaña **Workbooks** (Libros), seleccione **Public Templates** (Plantillas públicas) y, luego, seleccione **User Retention Analysis** (Análisis de la retención de usuario) en *Usage* (Uso).

:::image type="content" source="./media/usage-retention/workbooks-gallery.png" alt-text="Captura de pantalla de la galería de libros en plantillas públicas." lightbox="./media/usage-retention/workbooks-gallery.png":::



### <a name="using-the-workbook"></a>Uso del libro

:::image type="content" source="./media/usage-retention/retention.png" alt-text="Captura de pantalla del libro de retención que muestra un gráfico de líneas." lightbox="./media/usage-retention/retention.png":::

- De forma predeterminada, la herramienta de retención muestra todos los usuarios que hicieron algo y que , posteriormente, regresaron e hicieron algo más a lo largo de un período. Puede seleccionar una combinación diferente de eventos para centrar el enfoque en las actividades específicas del usuario.
- Para agregar uno o varios filtros en las propiedades, seleccione **Agregar filtros**. Por ejemplo, puede centrarse en los usuarios de un país o una región determinados. 
- El gráfico de retención general muestra un resumen de la retención del usuario durante un período de tiempo seleccionado. 
- La cuadrícula muestra el número de usuarios retenidos. Cada fila representa una cohorte de usuarios que llevó a cabo cualquier evento en el periodo mostrado. Cada celda de la fila muestra cuántos de esa cohorte regresaron al menos una vez en un periodo posterior. Algunos usuarios pueden regresar en más de un periodo. 
- Las tarjetas de información muestran los cinco eventos de inicio más importantes y los cinco eventos devueltos principales para proporcionar a los usuarios una mejor comprensión de su informe de retención. 

    :::image type="content" source="./media/usage-retention/retention-2.png" alt-text="Captura de pantalla del libro de retención, en la que se muestra la vuelta del usuario después del gráfico de número de semanas." lightbox="./media/usage-retention/retention-2.png":::

## <a name="use-business-events-to-track-retention"></a>Uso de eventos empresariales para realizar un seguimiento de la retención

Para obtener el análisis de retención más útil, mida los eventos que representan actividades empresariales importantes. 

Por ejemplo, muchos usuarios podrían abrir una página de la aplicación sin jugar al juego que se muestra. Al realizar solo el seguimiento de las vistas de página, se proporcionaría una estimación inexacta de cuántas personas vuelven a reproducir el juego después de haberlo jugado anteriormente. Para obtener una visión clara de los jugadores que regresan, la aplicación debe enviar un evento personalizado cuando un usuario juega realmente.  

Se recomienda codificar los eventos personalizados que representan las acciones clave del negocio y usarlos para realizar el análisis de retención. Para obtener el resultado del juego, tiene que escribir una línea de código con el fin enviar un evento personalizado a Application Insights. Si se escribe en el código de la página web o en Node.JS, se vería similar a esto:

```JavaScript
    appinsights.trackEvent("won game");
```

O en el código del servidor ASP.NET:

```csharp
   telemetry.TrackEvent("won game");
```

[Obtenga más información sobre la creación de eventos personalizados](./api-custom-events-metrics.md#trackevent).


## <a name="next-steps"></a>Pasos siguientes
- - Para más información sobre los libros, visite [la introducción a los libros](../visualize/workbooks-overview.md).
- Para habilitar las experiencias de uso, empiece por enviar [eventos personalizados](./api-custom-events-metrics.md#trackevent) o [vistas de páginas](./api-custom-events-metrics.md#page-views).
- Si ya ha enviado eventos personalizados o vistas de página, explore las herramientas de uso para obtener información sobre cómo los usuarios utilizan el servicio.
    - [Usuarios, sesiones, eventos](usage-segmentation.md)
    - [Embudos](usage-funnels.md)
    - [Flujos de usuario](usage-flows.md)
    - [Libros](../visualize/workbooks-overview.md)
    - [Adición de contexto de usuario](./usage-overview.md)