---
title: Recomendaciones de seguridad en Microsoft Defender for Cloud
description: Este documento explica cómo las recomendaciones de Microsoft Defender for Cloud ayudan a proteger los recursos de Azure y a cumplir con las directivas de seguridad.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 10/20/2021
ms.author: memildin
ms.openlocfilehash: 0ff3c4ff330dbb1c2d92fa9f585812de791b3f16
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131422172"
---
# <a name="review-your-security-recommendations"></a>Examen de las recomendaciones de seguridad

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En este tema se explica cómo ver y entender las recomendaciones de Microsoft Defender for Cloud para ayudar a proteger los recursos de Azure.

## <a name="monitor-recommendations"></a>Supervisión de recomendaciones<a name="monitor-recommendations"></a>

Defender for Cloud analiza el estado de seguridad de los recursos para identificar posibles vulnerabilidades. 

1. En el menú de Defender for Cloud, abra la página **Recomendaciones** para ver las recomendaciones aplicables a su entorno. Las recomendaciones se agrupan en controles de seguridad.

    :::image type="content" source="./media/review-security-recommendations/view-recommendations.png" alt-text="Recomendaciones agrupadas por control de seguridad." lightbox="./media/review-security-recommendations/view-recommendations.png":::

1. Para encontrar recomendaciones específicas para el tipo de recurso, la gravedad, el entorno u otros criterios que sean importantes para usted, utilice los filtros opcionales que se encuentran encima de la lista de recomendaciones.

    :::image type="content" source="media/review-security-recommendations/recommendation-list-filters.png" alt-text="Filtros para refinar la lista de recomendaciones de Microsoft Defender for Cloud.":::

1. Expanda un control y seleccione una recomendación concreta para ver la página de detalles de las recomendaciones.

    :::image type="content" source="./media/review-security-recommendations/recommendation-details-page.png" alt-text="Página de detalles de recomendación" lightbox="./media/review-security-recommendations/recommendation-details-page.png":::

    La página incluye:

    1. En el caso de las recomendaciones admitidas, en la barra de navegación superior se muestran algunos o todos los botones siguientes:
        - **Aplicar** y **Denegar** (vea [Evitar errores de configuración con las recomendaciones Aplicar o Denegar](prevent-misconfigurations.md)).
        - **View policy definition** (Ver definición de directiva) para ir directamente a la entrada de Azure Policy de la directiva subyacente.
        - **Abrir consulta**: todas las recomendaciones tienen la opción de ver la información detallada sobre los recursos afectados mediante Azure Resource Graph Explorer.
    1. **Indicador de gravedad**.
    1. **Intervalo de actualización** (si procede).
    1. **Recuento de recursos exentos**: si existen exenciones para esta recomendación, se muestra el número de recursos exentos.
    1. **Descripción**: breve descripción del problema de seguridad.
    1. Si procede, la página de detalles también incluye una tabla de **recomendaciones relacionadas**:

        Los tipos de relaciones son:

        - **Requisito previo**: una recomendación que debe aplicarse antes de la recomendación seleccionada.
        - **Alternativa**: una recomendación distinta que proporciona otra manera de lograr los objetivos de la recomendación seleccionada.
        - **Dependiente**: una recomendación para la cual la recomendación seleccionada es un requisito previo.

        Para cada recomendación relacionada, el número de recursos incorrectos se muestra en la columna "Recursos afectados".

        > [!TIP]
        > Si una recomendación relacionada está atenuada, su dependencia aún no se ha completado y, por tanto, no está disponible.

    1. **Pasos de corrección**: una descripción de los pasos manuales necesarios para corregir el problema de seguridad en los recursos afectados Para encontrar recomendaciones con la opción **Corregir**, puede seleccionar **Ver lógica de corrección** antes de aplicar la corrección sugerida a los recursos.

    1. **Recursos afectados**: los recursos se agrupan en pestañas:
        - **Recursos con estado correcto**: recursos relevantes que no se ven afectados o en los que ya se ha corregido el problema.
        - **Recursos con estado incorrecto**: recursos que todavía se ven afectados por el problema identificado.
        - **Quitado**: recursos que se eliminaron entre ciclos de evaluación (tal y como se define en el intervalo de actualización).
        - **Recursos no aplicables**: recursos para los que la recomendación no puede dar una respuesta definitiva. La pestaña no aplicable también incluye las razones de cada recurso. 

            :::image type="content" source="./media/review-security-recommendations/recommendations-not-applicable-reasons.png" alt-text="Los recursos no aplicables con motivos":::.
    1. Botones de acción para corregir la recomendación o desencadenar una aplicación lógica.

## <a name="review-recommendation-data-in-azure-resource-graph-explorer-arg"></a>Revisión de los datos de recomendación en Azure Resource Graph Explorer (ARG)

La barra de herramientas de la página de detalles de recomendación incluye un botón **Abrir consulta** para explorar los detalles de [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml), un servicio de Azure que ofrece la posibilidad de consultar, en varias suscripciones, los datos de postura de seguridad de Defender for Cloud.

ARG está diseñado para proporcionar una exploración de recursos eficaz con la posibilidad de realizar consultas a gran escala en los entornos en la nube con sólidas funcionalidades de filtrado, agrupación y ordenación. Es una forma rápida y eficaz de consultar información en las suscripciones de Azure mediante programación o desde Azure Portal.

Con el [lenguaje de consulta Kusto (KQL)](/azure/data-explorer/kusto/query/), puede hacer referencia cruzada a los datos de Defender for Cloud con otras propiedades de recursos.

Por ejemplo, esta página de detalles de recomendación muestra quince recursos afectados:

:::image type="content" source="./media/review-security-recommendations/open-query.png" alt-text="Botón **Abrir consulta** en la página de detalles de recomendación.":::

Al abrir la consulta subyacente y ejecutarla, Azure Resource Graph Explorer devuelve los mismos quince recursos y su estado de mantenimiento en esta recomendación: 

:::image type="content" source="./media/review-security-recommendations/run-query.png" alt-text="Azure Resource Graph Explorer que muestra los resultados de la recomendación que aparecían en la captura de pantalla anterior.":::

## <a name="preview-recommendations"></a>Recomendaciones de la versión preliminar

Las recomendaciones marcadas como **Versión preliminar** no se incluyen en los cálculos de la puntuación de seguridad.

Aun así, deben corregirse siempre que sea posible, de tal forma que, cuando finalice el período de versión preliminar, contribuyan a la puntuación.

Un ejemplo de una recomendación en versión preliminar:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Recomendación con la marca de versión preliminar.":::
 
## <a name="next-steps"></a>Pasos siguientes

En este documento, se han presentando las recomendaciones de seguridad de Defender for Cloud. Para obtener información relacionada, visite:

- [Recomendaciones de corrección ](implement-security-recommendations.md): aprenda a configurar directivas de seguridad para las suscripciones y los grupos de recursos de Azure.
- [Evitar errores de configuración con las recomendaciones Aplicar o Denegar](prevent-misconfigurations.md).
- [Creación de respuestas automáticas a desencadenares de Defender for Cloud](workflow-automation.md): automatice las respuestas a las recomendaciones
- [Exclusión de un recurso de una recomendación](exempt-resource.md)
- [Guía de referencia sobre las recomendaciones de seguridad](recommendations-reference.md)
