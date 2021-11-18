---
title: Trabajar con reglas de análisis de detección casi en tiempo real (NRT) en Microsoft Sentinel | Microsoft Docs
description: En este artículo se explica cómo ver y crear reglas de análisis de detección casi en tiempo real (NRT) en Microsoft Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9e807363a1a1cc058d6b54c009f9ff90d6a5aa93
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132277892"
---
# <a name="work-with-near-real-time-nrt-detection-analytics-rules-in-microsoft-sentinel"></a>Trabajar con reglas de análisis de detección casi en tiempo real (NRT) en Microsoft Sentinel

> [!IMPORTANT]
>
> - Las reglas casi en tiempo real (NRT) se encuentran actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

Las [reglas de análisis casi en tiempo real](near-real-time-rules.md) de Microsoft Sentinel ofrecen una detección de amenazas al minuto integrada. Este tipo de regla se diseñó para tener una gran capacidad de respuesta mediante la ejecución de su consulta a intervalos de solo un minuto de diferencia.

Por el momento, estas plantillas tienen una aplicación limitada, como se describe a continuación, pero la tecnología está evolucionando y creciendo rápidamente.

## <a name="view-near-real-time-nrt-rules"></a>Visualización de reglas casi en tiempo real (NRT)

1. En el menú de navegación de Microsoft Sentinel, seleccione **Análisis**.

1. En la pestaña **Reglas activas** de la hoja **Analytics**, filtre la lista de plantillas de **NRT**:

    1. Haga clic en el filtro **Tipo de regla** y, a continuación, en la lista desplegable que aparece a continuación.

    1. Desmarque **Seleccionar todo** y, posteriormente, seleccione **NRT**.

    1. Si es necesario, haga clic en la parte superior de la lista desplegable para retirarla y, a continuación, haga clic en **Aceptar**.

## <a name="create-nrt-rules"></a>Crear reglas de NRT

Las reglas de NRT se crean de la misma manera que se crean reglas de [análisis de consultas programadas normales](detect-threats-custom.md):

1. En el menú de navegación de Microsoft Sentinel, seleccione **Análisis**.

1. Seleccione **Crear** en la barra de botones y, posteriormente, la **regla de consulta NRT** en la lista desplegable.

    :::image type="content" source="media/create-nrt-rules/create-nrt-rule.png" alt-text="Cree una nueva regla de NRT.":::

1. Siga las instrucciones del [**asistente para reglas de análisis**](detect-threats-custom.md).

    La configuración de las reglas de NRT es, en la mayoría de los casos, la misma que la de las reglas de análisis programadas. 

    - Puede hacer referencia a [**listas de control**](watchlists.md) y [**fuentes de inteligencia sobre amenazas**](understand-threat-intelligence.md) en la lógica de consulta.

    - Puede usar todos los métodos de enriquecimiento de alertas: [**asignación de entidades**](map-data-fields-to-entities.md), [**detalles personalizados**](surface-custom-details-in-alerts.md) y [**detalles de alerta**](customize-alert-details.md).

    - Puede elegir cómo agrupar alertas en incidentes y suprimir una consulta cuando se ha generado un resultado determinado.

    - Puede automatizar las respuestas a alertas e incidentes.

    Sin embargo, debido a la [**naturaleza y las limitaciones de las reglas de NRT**](near-real-time-rules.md#considerations), las siguientes características de las reglas de análisis programadas *no estarán disponibles* en el asistente:

    - La **programación de consultas** no es configurable, ya que las consultas se programan automáticamente para ejecutarse una vez por minuto con un período de retroceso de un minuto. 
    - El **umbral de alerta** es irrelevante, ya que siempre se genera una alerta.
    - La **configuración de agrupación** de eventos no está disponible, ya que los eventos siempre se agrupan en la alerta creada por la regla que captura los eventos. Las reglas de NRT no pueden generar una alerta para cada evento.

    Además, la propia consulta tiene los siguientes requisitos:

    - La propia consulta solo puede hacer referencia a una tabla y no puede contener uniones ni combinaciones.

    - No se puede ejecutar la consulta entre áreas de trabajo.

    - Debido a los límites de tamaño de las alertas, la consulta debe usar instrucciones para incluir solo los `project`campos necesarios de la tabla. De lo contrario, la información que quiere mostrar puede acabar truncándose.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a crear reglas de análisis casi en tiempo real (NRT) en Microsoft Sentinel.

- Obtenga más información sobre las [reglas de análisis casi en tiempo real (NRT) en Microsoft Sentinel](near-real-time-rules.md).
- Explore otros [tipos de reglas de análisis](detect-threats-built-in.md).
