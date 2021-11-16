---
title: Información de Azure Communication Services (versión preliminar)
titleSuffix: An Azure Communication Services concept document
description: Descripciones de las visualizaciones de datos disponibles para Communications Services mediante Libros.
author: timmitchell
services: azure-communication-services
ms.author: timmitchell
ms.date: 10/25/2021
ms.topic: conceptual
ms.service: azure-communication-services
ms.subservice: data
ms.openlocfilehash: 1a11cc68d197d53ab2348c7b6ae625c6659a6884
ms.sourcegitcommit: 838413a8fc8cd53581973472b7832d87c58e3d5f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/10/2021
ms.locfileid: "132138605"
---
# <a name="communications-services-insights-preview"></a>Información de Communications Services (versión preliminar)

## <a name="overview"></a>Información general
Dentro del recurso Communications Services, hemos proporcionado una característica **Información** en versión preliminar que muestra una serie de visualizaciones de datos que transmiten información detallada de los registros y métricas de Azure Monitor supervisados para Communication Services. Las visualizaciones en esta característica son posibles a través de [Libros de Azure Monitor](/azure/azure-monitor/visualize/workbooks-overview). Para aprovechar las ventajas de la herramienta Libros, siga las instrucciones descritas en [Habilitación de Azure Monitor en Configuración de diagnóstico](enable-logging.md) y, para habilitar Libros, deberá enviar los registros a un destino del [área de trabajo de Log Analytics](/azure/azure-monitor/logs/log-analytics-overview). 

:::image type="content" source="media\workbooks\insights-overview-2.png" alt-text="Información de Communication Services":::

## <a name="accessing-azure-insights-for-communication-services"></a>Acceso a Información de Azure para Communication Services

1. En la página principal de **Azure Portal**, seleccione el recurso **Communication Services**:

    :::image type="content" source="media\workbooks\azure-portal-home-browser.png" alt-text="Inicio de Azure Portal":::

2. Una vez que esté dentro del recurso, desplácese hacia abajo en la barra de navegación izquierda hasta la categoría **Supervisión** y haga clic en la pestaña **Información**:

    :::image type="content" source="media\workbooks\acs-insights-nav.png" alt-text="Navegación por Información":::

3. Se mostrará el panel **Información** del recurso Communication Services:

    :::image type="content" source="media\workbooks\acs-insights-tab.png" alt-text="Pestaña Información de Communication Services":::

## <a name="insights-dashboard-navigation"></a>Navegación por el panel Información

Los paneles de información de Communication Services proporcionan a los usuarios una manera clara e intuitiva de navegar por los datos de registro de su recurso. La sección **Información general** proporciona una vista de todas las modalidades, para que el usuario pueda ver las distintas formas en que se ha usado su recurso en un intervalo de tiempo específico:

:::image type="content" source="media\workbooks\overview.png" alt-text="Introducción a Conclusiones":::

Los usuarios pueden controlar el intervalo de tiempo y la granularidad de tiempo que aparecerán con los parámetros en la parte superior:

:::image type="content" source="media\workbooks\time-range-param.png" alt-text="Parámetro de intervalo de tiempo":::

Estos parámetros son globales, lo que significa que actualizarán los datos mostrados en todo el panel.

La sección **Información general** contiene un parámetro adicional para controlar el tipo de visualización que se muestra:

:::image type="content" source="media\workbooks\plot-type-param.png" alt-text="Parámetro de tipo de trazado":::

Este parámetro es local, lo que significa que solo afecta a los trazados de esta sección.

El resto de las pestañas muestra los datos de registro relacionados con una modalidad específica.

:::image type="content" source="media\workbooks\main-nav.png" alt-text="Navegación principal":::

Puesto que los registros de **voz y vídeo** son los más complejos por naturaleza, esta modalidad se divide en cuatro subsecciones:

:::image type="content" source="media\workbooks\voice-and-video-nav.png" alt-text="Navegación por voz y vídeo":::

La pestaña **Resumen** contiene información general sobre el uso de voz y vídeo, incluidos los tipos de secuencias multimedia compartidas, los tipos de puntos de conexión que participan en una llamada (por ejemplo, VoIP, Bot, Aplicación, RTC o Servidor), el uso del sistema operativo y los motivos de finalización del participante:

:::image type="content" source="media\workbooks\voice-and-video-summary.png" alt-text="Resumen de voz y vídeo":::

La pestaña **Volumen** de la modalidad **Voice and video** (Voz y vídeo) muestra el número de llamadas y el número de participantes en un período de tiempo específico (parámetro **Intervalo de tiempo**), subdividido en intervalos de tiempo (parámetro **Granularidad de tiempo**):

:::image type="content" source="media\workbooks\voice-and-video-volume.png" alt-text="Volumen de voz y vídeo":::

La pestaña **Volumen** contiene un parámetro **Agrupación**, que ayuda a visualizar el número de llamadas y participantes segmentados por Tipo de llamada (llamadas P2P frente a llamadas de grupo) y Llamadas de interoperabilidad (Azure Communication Services puro [ACS] frente a Interoperabilidad de Teams):

:::image type="content" source="media\workbooks\voice-and-video-volume-grouping.png" alt-text="Agrupación de volúmenes de voz y vídeo":::

La pestaña **Calidad** de **Voice and video** (Voz y vídeo) permite a los usuarios inspeccionar la distribución de calidad de las llamadas, donde la calidad se define en tres niveles en este panel:

- La proporción de secuencias multimedia de baja calidad (trazado de **calidad de secuencia**), donde la calidad de una secuencia se clasifica como baja cuando tiene al menos un valor de telemetría incorrecto, donde los intervalos incorrectos se definen como:
  - Vibración > 30 milisegundos
  - Velocidad de pérdida de paquetes > 10 %
  - Tiempo de ida y vuelta > 500 milisegundos

- La proporción de **llamadas afectadas**, donde una llamada afectada se define como una llamada que tiene al menos una secuencia de calidad baja.

- Las **razones de finalización del participante**, que mantienen un seguimiento del motivo por el que un participante abandonó una llamada. Las razones de finalización son [códigos SIP](https://en.wikipedia.org/wiki/List_of_SIP_response_codes), que son códigos numéricos que describen el estado específico de una solicitud de señalización. Los códigos SIP se pueden agrupar en seis categorías: *Correcto*, *Error del cliente*, *Error del servidor*, *Error global*, *Redirección* y *Provisional*. La distribución de las categorías de códigos SIP se muestra en el gráfico circular del lado izquierdo, mientras que en el lado derecho se proporciona una lista de los códigos SIP específicos de los motivos de finalización del participante.

:::image type="content" source="media\workbooks\voice-and-video-quality.png" alt-text="Calidad de voz y vídeo":::

La calidad también se puede filtrar por los tipos de secuencias multimedia (parámetro **Tipo de elemento multimedia**) que se usan en la llamada, por ejemplo, para obtener solo las llamadas afectadas en términos de calidad de la secuencia de vídeo:

:::image type="content" source="media\workbooks\voice-and-video-quality-params.png" alt-text="Parámetro de tipo de elemento multimedia de calidad de voz y vídeo":::

Además, también se pueden filtrar por los tipos de punto de conexión (parámetro **Tipo de punto de conexión**), por ejemplo, obtener los motivos de finalización del participante en el caso de los participantes de RTC. Estos filtros permiten varias selecciones:

:::image type="content" source="media\workbooks\voice-and-video-params-2.png" alt-text="Parámetro de tipo de punto de conexión de calidad de voz y vídeo":::

La pestaña **Detalles** ofrece una manera rápida de navegar por las llamadas de **voz y vídeo** realizadas en un intervalo de tiempo al agrupar las llamadas por fechas y mostrar los detalles de cada llamada realizada en términos de los participantes en esa llamada y las secuencias salientes por participante, junto con los valores de duración y telemetría de estas:

:::image type="content" source="media\workbooks\voice-and-video-details.png" alt-text="Detalles de voz y vídeo":::

Los detalles están inicialmente ocultos. Después de hacer clic en una llamada, se muestra una lista de los participantes:

:::image type="content" source="media\workbooks\voice-and-video-details-participants.png" alt-text="Detalles de los participantes de voz y vídeo":::

Al hacer clic en un participante, se muestra una lista de las secuencias salientes para ese participante, junto con su duración (proporcional a la duración completa de la llamada) y los valores de telemetría, donde los valores incorrectos se muestran en rojo:

:::image type="content" source="media\workbooks\voice-and-video-details-streams.png" alt-text="Detalles de la secuencia de voz y vídeo":::

La pestaña **Autenticación** muestra los registros de autenticación, que se crean mediante operaciones como emitir un token de acceso o crear una identidad. Los datos mostrados incluyen los tipos de operaciones realizadas y los resultados de esas operaciones:

:::image type="content" source="media\workbooks\auth.png" alt-text="Pestaña Autenticación":::

La pestaña **Chat** muestra los datos de todas las operaciones relacionadas con el chat y sus tipos de resultado:

:::image type="content" source="media\workbooks\chat.png" alt-text="Pestaña Chat":::

La pestaña **SMS** muestra las operaciones y los resultados del uso de SMS mediante un recurso de ACS (actualmente no tenemos ningún dato para esta modalidad):

:::image type="content" source="media\workbooks\sms.png" alt-text="Pestaña SMS":::

## <a name="editing-dashboards"></a>Edición de paneles

Los paneles de **Información** que se proporcionan con el recurso **Communication Services** se pueden personalizar haciendo clic en el botón **Editar** de la barra de navegación superior:

:::image type="content" source="media\workbooks\dashboard-editing.png" alt-text="Edición de paneles":::

La edición de estos paneles no modifica la pestaña **Información**, sino que crea un libro independiente al que se puede acceder en la pestaña Libros del recurso:

:::image type="content" source="media\workbooks\workbooks-tab.png" alt-text="Pestaña Libros":::

Para ver una descripción detallada de los libros, consulte la documentación de [Libros de Azure Monitor](/azure/azure-monitor/visualize/workbooks-overview).