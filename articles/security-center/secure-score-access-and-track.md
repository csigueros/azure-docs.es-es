---
title: Seguimiento de la puntuación segura en Microsoft Defender for Cloud
description: Obtenga información sobre las distintas maneras de acceder a su puntuación de seguridad y hacerle un seguimiento en Microsoft Defender for Cloud.
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: b4942dd35591839440814e5aa8862632faf1ce36
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131084047"
---
# <a name="access-and-track-your-secure-score"></a>Acceso y seguimiento de la puntuación segura

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Encontrará la puntuación de seguridad general, así como la puntuación por suscripción, mediante Azure Portal o mediante programación, tal como se describe en las secciones siguientes:

> [!TIP]
> Para obtener una explicación detallada de cómo se calculan las puntuaciones, consulte [Cálculos: Descripción de la puntuación](secure-score-security-controls.md#calculations---understanding-your-score).

## <a name="get-your-secure-score-from-the-portal"></a>Obtención de la puntuación de seguridad desde el portal

Defender for Cloud muestra la puntuación de forma destacada en el portal: es el primer icono principal de la página de información general de Defender for Cloud. Al seleccionar este icono, irá a la página dedicada de puntuación de seguridad, donde verá la puntuación dividida por suscripciones. Seleccione una suscripción única para ver la lista detallada de las recomendaciones prioritarias y del posible impacto que su corrección tendrá en la puntuación de la suscripción. 

En resumen, la puntuación de seguridad se muestra en las siguientes ubicaciones en las páginas del portal de Defender for Cloud.

- En un icono de **Información general** de Defender for Cloud (panel principal):

    :::image type="content" source="./media/secure-score-security-controls/score-on-main-dashboard.png" alt-text="Puntuación segura en el panel de Defender for Cloud":::

- En la página dedicada **Puntuación de seguridad**, puede ver la puntuación de seguridad de la suscripción y los grupos de administración:

    :::image type="content" source="./media/secure-score-security-controls/score-on-dedicated-dashboard.png" alt-text="Puntuación segura de las suscripciones en la página de puntuación segura de Defender for Cloud":::

    :::image type="content" source="./media/secure-score-security-controls/secure-score-management-groups.png" alt-text="Puntuación segura de los grupos de seguridad en la página de puntuación segura de Defender for Cloud":::

    > [!NOTE]
    > Los grupos de administración para los que no tenga permisos suficientes mostrarán su puntuación como "Restringido". 

- En la parte superior de la página **Recomendaciones**:

    :::image type="content" source="./media/secure-score-security-controls/score-on-recommendations-page.png" alt-text="La puntuación segura en la página de recomendaciones de Defender for Cloud":::

## <a name="get-your-secure-score-from-the-rest-api"></a>Obtención de la puntuación de seguridad desde la API de REST

Puede acceder a la puntuación a través de la API de puntuación segura. Los métodos de API proporcionan la flexibilidad necesaria para consultar los datos y crear su propio mecanismo de creación de informes de las puntuaciones seguras a lo largo del tiempo. Por ejemplo, puede usar [Secure Scores](/rest/api/securitycenter/securescores) API para obtener la puntuación de una suscripción específica. Además, puede usar [Secure Score Controls API](/rest/api/securitycenter/securescorecontrols) para mostrar los controles de seguridad y la puntuación actual de las suscripciones.

![Recuperación de una sola puntuación de seguridad por medio de la API.](media/secure-score-security-controls/single-secure-score-via-api.png)

Para ver ejemplos de herramientas basadas en la API de puntuación de seguridad, consulte el [área de puntuación de seguridad de la comunidad de GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score). 

## <a name="get-your-secure-score-from-azure-resource-graph"></a>Obtención de la puntuación de seguridad desde Azure Resource Graph

Azure Resource Graph proporciona acceso instantáneo a la información de los recursos en los entornos en la nube con funcionalidades sólidas para filtrar, agrupar y ordenar. Es una forma rápida y eficaz de consultar información en las suscripciones de Azure mediante programación o desde Azure Portal. [Más información sobre Azure Resource Graph](../governance/resource-graph/index.yml).

Para acceder a la puntuación de seguridad de varias suscripciones con Azure Resource Graph:

1. Desde Azure Portal, abra el **Explorador de Azure Resource Graph**.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Inicio de la página de recomendaciones de Azure Resource Graph Explorer**" :::

1. Escriba la consulta de Kusto (utilice los ejemplos siguientes como guía).

    - Esta consulta devuelve el identificador de la suscripción, la puntuación actual en puntos y como porcentaje, y la puntuación máxima de la suscripción. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores' 
        | extend current = properties.score.current, max = todouble(properties.score.max)
        | project subscriptionId, current, max, percentage = ((current / max)*100)
        ```

    - Esta consulta devuelve el estado de todos los controles de seguridad. Para cada control, obtendrá el número de recursos incorrectos, la puntuación actual y la puntuación máxima. 

        ```kusto
        SecurityResources 
        | where type == 'microsoft.security/securescores/securescorecontrols'
        | extend SecureControl = properties.displayName, unhealthy = properties.unhealthyResourceCount, currentscore = properties.score.current, maxscore = properties.score.max
        | project SecureControl , unhealthy, currentscore, maxscore
        ```

1. Seleccione **Ejecutar consulta**.


## <a name="tracking-your-secure-score-over-time"></a>Seguimiento de la puntuación de seguridad a lo largo del tiempo

### <a name="secure-score-over-time-report-in-workbooks-page"></a>Informe de puntuación de seguridad a lo largo del tiempo en la página de libros

La página de libros de Defender for Cloud incluye un informe ya preparado para realizar un seguimiento visual de las puntuaciones de las suscripciones, los controles de seguridad, etc. Obtenga más información en [Creación de informes completos e interactivos de datos de Defender for Cloud](custom-dashboards-azure-workbooks.md).

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Una sección del informe de puntuación segura a lo largo del tiempo de la galería de libros de Microsoft Defender for Cloud":::

### <a name="power-bi-pro-dashboards"></a>Paneles de Power BI Pro

Si es un usuario de Power BI con una cuenta Pro, puede usar el panel de Power BI de **puntuación de seguridad a lo largo del tiempo** para realizar un seguimiento de la puntuación de seguridad a lo largo del tiempo e investigar los cambios.

> [!TIP]
> Puede encontrar este panel, así como otras herramientas para trabajar mediante programación con la puntuación de seguridad, en el área dedicada de la comunidad de Microsoft Defender for Cloud panel en GitHub: https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score

El panel contiene los dos informes siguientes para ayudarle a analizar el estado de seguridad:

- **Resumen de los recursos**: proporciona datos resumidos sobre el estado de los recursos.
- **Resumen de la puntuación de seguridad**: proporciona datos resumidos sobre el progreso de la puntuación. Use el gráfico "Secure score over time per subscription" para ver los cambios en la puntuación. Si observa un cambio drástico en la puntuación, consulte la tabla "detected changes that may affect your secure score" para buscar posibles cambios que puedan haber producido la variación. En esta tabla se presentan recursos eliminados, recursos recién implementados o recursos que su estado de seguridad cambió para una de las recomendaciones.

:::image type="content" source="./media/secure-score-security-controls/power-bi-secure-score-dashboard.png" alt-text="Panel de Power BI de puntuación de seguridad opcional a lo largo del tiempo para realizar el seguimiento de la puntuación de seguridad en el tiempo e investigar los cambios.":::


## <a name="next-steps"></a>Pasos siguientes

En este artículo se describe cómo acceder a la puntuación de seguridad y realizar su seguimiento. Para obtener material relacionado, consulte los siguientes artículos:

- [Más información sobre los distintos elementos de una recomendación](review-security-recommendations.md)
- [Recomendaciones de corrección](implement-security-recommendations.md)
- [Ver las herramientas basadas en GitHub para trabajar mediante programación con la puntuación de seguridad](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score)
