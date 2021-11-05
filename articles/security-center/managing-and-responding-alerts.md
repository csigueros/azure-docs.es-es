---
title: Administración de incidentes de seguridad en Microsoft Defender for Cloud | Microsoft Docs
description: Este documento le ayuda a utilizar las capacidades de Microsoft Defender for Cloud para administrar y responder a las alertas de seguridad.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: fcf92ce079d4e51d448ba199a505706f9e0d5b1d
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093460"
---
# <a name="manage-and-respond-to-security-alerts-in-microsoft-defender-for-cloud"></a>Administración de alertas de seguridad y respuesta a ellas en Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En este tema se muestra cómo ver y procesar las alertas de Defender for Cloud y cómo proteger los recursos.

Las detecciones avanzadas que desencadenan alertas de seguridad solo están disponibles con las características de seguridad mejoradas de Microsoft Defender for Cloud habilitadas. Hay una evaluación gratuita disponible. Para efectuar la actualización, consulte [Habilitación de las protecciones mejoradas](enable-enhanced-security.md).

## <a name="what-are-security-alerts"></a>¿Qué son las alertas de seguridad?
Defender for Cloud recopila, analiza e integra automáticamente los datos de registro de los recursos de Azure, la red y las soluciones de asociados conectados, como firewalls y soluciones de protección de puntos de conexión, para detectar amenazas reales y reducir los falsos positivos. En Defender for Cloud se muestra una lista de alertas de seguridad prioritarias junto con la información necesaria para investigar rápidamente el problema y los pasos que se deben seguir para corregir un ataque.

Para más información sobre los distintos tipos de alertas, consulte [Alertas de seguridad: una guía de referencia](alerts-reference.md).

Para obtener información general sobre cómo Defender for Cloud genera alertas, consulte [How Microsoft Defender for Cloud detects and responds to threats](alerts-overview.md)(Cómo Microsoft Defender for Cloud y responde a las amenazas).


## <a name="manage-your-security-alerts"></a>Administración de las alertas de seguridad

1. En la página de información general de Defender for Cloud, seleccione el mosaico **Alertas de seguridad** en la parte superior de la página o el vínculo de la barra lateral.

    :::image type="content" source="media/managing-and-responding-alerts/overview-page-alerts-links.png" alt-text="Acceso a la página de alertas de seguridad desde la página de información general de Microsoft Defender for Cloud":::

    Se abre la página de alertas de seguridad.

    :::image type="content" source="media/managing-and-responding-alerts/alerts-page.png" alt-text="Alertas de seguridad en Microsoft Defender for Cloud":::

1. Para filtrar la lista de alertas, seleccione cualquiera de los filtros pertinentes. Opcionalmente, puede agregar más filtros con la opción **Agregar filtro**.

    :::image type="content" source="./media/managing-and-responding-alerts/alerts-adding-filters-small.png" alt-text="Adición de filtros a la vista de alertas." lightbox="./media/managing-and-responding-alerts/alerts-adding-filters-large.png":::

    La lista se actualiza según las opciones de filtro que haya seleccionado. El filtrado puede resultar muy útil. Por ejemplo, podría comprobar las alertas de seguridad que se produjeron en las 24 horas anteriores, ya que se está investigando una posible brecha en el sistema.


## <a name="respond-to-security-alerts"></a>Responder a alertas de seguridad

1. En la lista **Alertas de seguridad**, seleccione una alerta. Se abre un panel lateral en el que se muestra una descripción de la alerta y de todos los recursos afectados. 

    :::image type="content" source="./media/managing-and-responding-alerts/alerts-details-pane.png" alt-text="Vista mínima de detalles de una alerta de seguridad.":::

    > [!TIP]
    > Con este panel lateral abierto, puede revisar rápidamente la lista de alertas con las flechas arriba y abajo del teclado.

1. Para más información, seleccione **Ver detalles completos**.

    En el panel izquierdo de la página alerta de seguridad se muestra información de alto nivel sobre la alerta de seguridad: título, gravedad, estado, tiempo de actividad, descripción de la actividad sospechosa y el recurso afectado. Junto con el recurso afectado se encuentran las etiquetas de Azure relevantes para el recurso. Úselas para deducir el contexto de la organización del recurso al investigar la alerta.

    En el panel derecho se incluye la pestaña **Detalles de alerta** que contiene más detalles de la alerta para ayudarle a investigar el problema: Direcciones IP, archivos, procesos, etc.
     
    ![Sugerencias sobre qué hacer con las alertas de seguridad.](./media/managing-and-responding-alerts/security-center-alert-remediate.png)

    Además, en el panel derecho se encuentra la pestaña **Realizar acción**. Use esta pestaña para realizar acciones adicionales con respecto a la alerta de seguridad. Acciones como:
    - *Mitigar las amenazas:* proporciona pasos de corrección manual para esta alerta de seguridad
    - *Evitar ataques futuros:* proporciona recomendaciones de seguridad para ayudar a reducir la superficie expuesta a ataques, aumentar la postura de seguridad y así evitar futuros ataques
    - *Desencadenar respuesta automatizada:* ofrece la opción de desencadenar una aplicación lógica como respuesta a esta alerta de seguridad
    - *Suprimir alertas similares:* ofrece la opción de suprimir las alertas futuras con características similares si la alerta no es relevante para su organización

    ![Pestaña Realizar acción.](./media/managing-and-responding-alerts/alert-take-action.png)




## <a name="see-also"></a>Consulte también

En este documento, aprendió cómo ver alertas de seguridad. Vea las páginas siguientes para obtener material relacionado:

- [Configuración de reglas de eliminación de alertas](alerts-suppression-rules.md)
- [Automatización de respuestas a desencadenadores de Defender for Cloud](workflow-automation.md)
- [Alertas de seguridad: una guía de referencia](alerts-reference.md)
