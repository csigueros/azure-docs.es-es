---
title: Administración de alertas de seguridad en Azure Security Center | Microsoft Docs
description: Este documento le ayuda a usar las capacidades del Centro de seguridad de Azure para administrar y responder a las alertas de seguridad.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: b88a8df7-6979-479b-8039-04da1b8737a7
ms.service: security-center
ms.topic: how-to
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/17/2021
ms.author: memildin
ms.openlocfilehash: dbed9883fbddc2efdaaa8ad252578afa928167bd
ms.sourcegitcommit: f3b930eeacdaebe5a5f25471bc10014a36e52e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2021
ms.locfileid: "112237261"
---
# <a name="manage-and-respond-to-security-alerts-in-azure-security-center"></a>Administración y respuesta a alertas de seguridad en Azure Security Center

En este tema se muestra cómo ver y procesar las alertas de Security Center y cómo proteger los recursos.

Las detecciones avanzadas que desencadenan las alertas de seguridad solo están disponibles con Azure Defender. Hay una evaluación gratuita disponible. Para actualizar, consulte [Habilitación de Azure Defender](enable-azure-defender.md).

## <a name="what-are-security-alerts"></a>¿Qué son las alertas de seguridad?
Security Center recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión, para detectar amenazas reales y reducir los falsos positivos. En Security Center, se muestra una lista de alertas de seguridad prioritarias, junto con la información que necesita para investigar rápidamente, y los pasos que debe seguir para corregir un ataque.

Para más información sobre los distintos tipos de alertas, consulte [Alertas de seguridad: una guía de referencia](alerts-reference.md).

Para obtener información general sobre la forma en que Security Center genera alertas, consulte [¿Cómo detecta Security Center las amenazas?](security-center-alerts-overview.md)


## <a name="manage-your-security-alerts"></a>Administración de las alertas de seguridad

1. En la página de información general de Security Center, seleccione el icono **Alertas de seguridad** en la parte superior de la página, o el vínculo de la barra lateral.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Ir a la página de alertas de seguridad desde la página de información general de Azure Security Center":::

    Se abre la página de alertas de seguridad.

    :::image type="content" source="media/security-center-managing-and-responding-alerts/alerts-page.png" alt-text="Lista de alertas de seguridad de Security Center":::

1. Para filtrar la lista de alertas, seleccione cualquiera de los filtros pertinentes. Opcionalmente, puede agregar más filtros con la opción **Agregar filtro**.

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Adición de filtros a la vista de alertas." lightbox="./media/security-center-managing-and-responding-alerts/alerts-adding-filters-large.png":::

    La lista se actualiza según las opciones de filtro que haya seleccionado. El filtrado puede resultar muy útil. Por ejemplo, podría comprobar las alertas de seguridad que se produjeron en las 24 horas anteriores, ya que se está investigando una posible brecha en el sistema.


## <a name="respond-to-security-alerts"></a>Responder a alertas de seguridad

1. En la lista **Alertas de seguridad**, seleccione una alerta. Se abre un panel lateral en el que se muestra una descripción de la alerta y de todos los recursos afectados. 

    :::image type="content" source="./media/security-center-managing-and-responding-alerts/alerts-details-pane.png" alt-text="Vista mínima de detalles de una alerta de seguridad.":::

    > [!TIP]
    > Con este panel lateral abierto, puede revisar rápidamente la lista de alertas con las flechas arriba y abajo del teclado.

1. Para más información, seleccione **Ver detalles completos**.

    En el panel izquierdo de la página alerta de seguridad se muestra información de alto nivel sobre la alerta de seguridad: título, gravedad, estado, tiempo de actividad, descripción de la actividad sospechosa y el recurso afectado. Junto con el recurso afectado se encuentran las etiquetas de Azure relevantes para el recurso. Úselas para deducir el contexto de la organización del recurso al investigar la alerta.

    En el panel derecho se incluye la pestaña **Detalles de alerta** que contiene más detalles de la alerta para ayudarle a investigar el problema: Direcciones IP, archivos, procesos, etc.
     
    ![Sugerencias sobre qué hacer con las alertas de seguridad.](./media/security-center-managing-and-responding-alerts/security-center-alert-remediate.png)

    Además, en el panel derecho se encuentra la pestaña **Realizar acción**. Use esta pestaña para realizar acciones adicionales con respecto a la alerta de seguridad. Acciones como:
    - *Mitigar las amenazas:* proporciona pasos de corrección manual para esta alerta de seguridad
    - *Evitar ataques futuros:* proporciona recomendaciones de seguridad para ayudar a reducir la superficie expuesta a ataques, aumentar la postura de seguridad y así evitar futuros ataques
    - *Desencadenar respuesta automatizada:* ofrece la opción de desencadenar una aplicación lógica como respuesta a esta alerta de seguridad
    - *Suprimir alertas similares:* ofrece la opción de suprimir las alertas futuras con características similares si la alerta no es relevante para su organización

    ![Pestaña Realizar acción.](./media/security-center-managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Consulte también

En este documento, aprendió cómo ver alertas de seguridad. Vea las páginas siguientes para obtener material relacionado:

- [Configuración de reglas de eliminación de alertas](alerts-suppression-rules.md)
- [Creación de respuestas automáticas a alertas y recomendaciones con automatización del flujo de trabajo](workflow-automation.md)
- [Alertas de seguridad: una guía de referencia](alerts-reference.md)
