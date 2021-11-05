---
title: Administración de incidentes de seguridad en Microsoft Defender for Cloud | Microsoft Docs
description: Este documento le ayuda a usar Microsoft Defender for Cloud para administrar incidentes de seguridad.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/17/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 3977d95358c20b98551170394bcd846d0b4e8358
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017465"
---
# <a name="manage-security-incidents-in-microsoft-defender-for-cloud"></a>Administración de incidentes de seguridad en Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

La clasificación e investigación de las alertas de seguridad puede ser un proceso largo incluso para los analistas de seguridad más cualificados. Para muchos, es difícil saber por dónde empezar. 

Defender for Cloud usa [análisis](./alerts-overview.md) para conectar la información entre [alertas de seguridad](managing-and-responding-alerts.md) diferentes. Con estas conexiones, Defender for Cloud puede proporcionar una vista única de una campaña de ataque y sus alertas relacionadas para ayudarle a comprender las acciones del atacante y los recursos afectados.

En esta página se proporciona información general sobre los incidentes en Defender for Cloud.

## <a name="what-is-a-security-incident"></a>¿Qué es un incidente de seguridad?

En Defender for Cloud, un incidente de seguridad es la suma de todas las alertas de un recurso que se alinean con patrones de [cadenas de eliminación](alerts-reference.md#intentions). Los incidentes aparecen en la página [Alertas de seguridad](managing-and-responding-alerts.md). Seleccione un incidente para ver las alertas relacionadas y obtener más información.

## <a name="managing-security-incidents"></a>Administración de incidentes de seguridad

1. En la página de alertas de Defender for Cloud, use el botón **Agregar filtro** para filtrar por nombre de alerta en el nombre de alerta **Incidente de seguridad detectado en varios recursos**. 

    :::image type="content" source="media/incidents/locating-incidents.png" alt-text="Buscar los incidentes en la página de alertas de Microsoft Defender for Cloud.":::

    La lista está ahora filtrada para que muestre solo los incidentes. Tenga en cuenta que los incidentes de seguridad tienen un icono diferente a las alertas de seguridad.

    :::image type="content" source="media/incidents/incidents-list.png" alt-text="Lista de incidentes en la página de alertas de Microsoft Defender for Cloud.":::

1. Para ver los detalles de un incidente, seleccione uno de la lista. Aparece un panel lateral con más detalles sobre el incidente.

    :::image type="content" source="media/incidents/incident-quick-peek.png" alt-text="Panel lateral que muestra los detalles del incidente.":::

1. Para ver más detalles, seleccione **Ver detalles completos**.

    [![Responder a incidentes de seguridad en Microsoft Defender for Cloud.](media/incidents/incident-details.png)](media/incidents/incident-details.png#lightbox)

    En el panel izquierdo de la página Incidente de seguridad se muestra información de alto nivel sobre el incidente de seguridad: título, gravedad, estado, tiempo de actividad, descripción y el recurso afectado. Junto al recurso afectado, puede ver las etiquetas relevantes de Azure. Use estas etiquetas para deducir el contexto de la organización del recurso al investigar la alerta.

    En el panel derecho se incluye la pestaña **Alertas** con las alertas de seguridad que se correlacionan como parte de este incidente. 

    >[!TIP]
    > Para obtener más información acerca de una alerta específica, selecciónela. 

    [![Pestaña Realizar acción del incidente](media/incidents/incident-take-action-tab.png)](media/incidents/incident-take-action-tab.png#lightbox)

    Para cambiar a la pestaña **Realizar acción**, seleccione la pestaña o el botón en la parte inferior del panel derecho. Use esta pestaña para realizar acciones adicionales, como:
    - *Mitigar las amenazas:* proporciona pasos de corrección manual para este incidente de seguridad.
    - *Evitar ataques futuros:* proporciona recomendaciones de seguridad para ayudar a reducir la superficie expuesta a ataques, aumentar la postura de seguridad y evitar futuros ataques.
    - *Desencadenar respuesta automatizada:* ofrece la opción de desencadenar una aplicación lógica como respuesta a este incidente de seguridad.
    - *Suprimir alertas similares:* ofrece la opción de suprimir las alertas futuras con características similares si la alerta no es relevante para su organización. 

   > [!NOTE]
   > La misma alerta puede existir como parte de un incidente y estar visible como alerta independiente.

1. Para corregir las amenazas del incidente, siga los pasos de corrección que se proporcionan con cada alerta.


## <a name="next-steps"></a>Pasos siguientes

En esta página se han explicado las capacidades de incidentes de seguridad de Defender for Cloud. Para obtener información relacionada, consulte las páginas siguientes:

- [Alertas de seguridad en Defender for Cloud](alerts-overview.md)
- [Administración de las alertas de seguridad y respuesta a ellas](managing-and-responding-alerts.md)
