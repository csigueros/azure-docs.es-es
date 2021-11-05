---
title: Panel de protección de cargas de trabajo de Microsoft Defender for Cloud y sus características
description: Obtenga información sobre las características del panel de protección de cargas de trabajo de Microsoft Defender for Cloud.
author: memildin
ms.author: memildin
ms.date: 11/02/2021
ms.topic: how-to
ms.service: security-center
manager: rkarlin
ms.custom: ignite-fall-2021
ms.openlocfilehash: db2f01d390cc9503abd0b0a26650e2861dc059c1
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131017354"
---
# <a name="the-workload-protections-dashboard"></a>Panel de protecciones de cargas de trabajo

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Este panel proporciona:

- Visibilidad de la cobertura de Microsoft Defender en los diferentes tipos de recursos
- Vínculos para configurar las funcionalidades avanzadas de protección contra amenazas
- Estado de incorporación e instalación del agente
- Alertas de detección de amenazas 

Para acceder al panel de protecciones de cargas de trabajo, seleccione **Protecciones de cargas de trabajo** en la sección "Seguridad en la nube" del menú de Defender for Cloud.

## <a name="whats-shown-on-the-dashboard"></a>¿Qué se muestra en el panel?

:::image type="content" source="./media/workload-protections-dashboard/sample-defender-dashboard-numbered.png" alt-text="Un ejemplo del panel de protecciones de cargas de trabajo de Defender for Cloud." lightbox="./media/workload-protections-dashboard/sample-defender-dashboard-numbered.png":::

El panel incluye las secciones siguientes:

1. **Cobertura de Microsoft Defender**: aquí puede ver los tipos de recursos que se encuentran en su suscripción y que pueden protegerse mediante Defender for Cloud. Siempre que proceda, tendrá también la opción de actualizar. Si desea actualizar todos los recursos válidos posibles, seleccione **Actualizar todo**.

2. **Alertas de seguridad**: cuando Defender for Cloud detecta una amenaza en cualquiera de las áreas del entorno, genera una alerta. Estas alertas describen los detalles de los recursos afectados, los pasos de corrección sugeridos y, en algunos casos, una opción para desencadenar una aplicación lógica como respuesta. Al seleccionar cualquier parte de este gráfico, se abre la **página de alertas de seguridad**.

3. **Protección avanzada**: Defender for Cloud incluye muchas capacidades avanzadas de protección contra amenazas para máquinas virtuales, bases de datos SQL, contenedores, aplicaciones web, la red y mucho más. En esta sección de protección avanzada, puede ver el estado de los recursos de las suscripciones seleccionadas para cada una de estas protecciones. Seleccione cualquiera de ellas para ir directamente al área de configuración de ese tipo de protección.

4. **Información detallada**: este panel continuo de noticias, lecturas sugeridas y alertas de alta prioridad aporta a Defender for Cloud información sobre aspectos de seguridad apremiantes que son pertinentes para usted y su suscripción. Ya sea que se trate de una lista de vulnerabilidades y exposiciones comunes de alta gravedad descubiertas en sus máquinas virtuales por una herramienta de análisis de vulnerabilidades, o bien de una nueva publicación de blog de un miembro del equipo de Defender for Cloud, lo encontrará en el panel de información detallada.




## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido información sobre el panel de protecciones de cargas de trabajo. 

> [!div class="nextstepaction"]
> [Habilitación de protecciones mejoradas](enable-enhanced-security.md)

Para obtener más información sobre las protecciones avanzadas de Microsoft Defender, consulte la [introducción a Microsoft Defender for Cloud](defender-for-cloud-introduction.md).
