---
title: Introducción a la automatización en Microsoft Sentinel | Microsoft Docs
description: 'En este artículo se presentan las capacidades de orquestación de seguridad, automatización y respuesta (SOAR) de Microsoft Sentinel y se describen sus componentes de SOAR: reglas de automatización y cuadernos de estrategias.'
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.service: microsoft-sentinel
ms.subservice: microsoft-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 16a7c66302bcb4b6b4b3268fe7ed55d950053b6b
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524823"
---
# <a name="security-orchestration-automation-and-response-soar-in-microsoft-sentinel"></a>Orquestación de seguridad, automatización y respuesta (SOAR) en Microsoft Sentinel

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En este artículo se describen las capacidades de orquestación de seguridad, automatización y respuesta (SOAR) de Microsoft Sentinel y se muestra cómo el uso de reglas de automatización y cuadernos de estrategias en respuesta a las amenazas de seguridad aumenta la eficacia de su centro de operaciones de seguridad (SOC) y le ahorra tiempo y recursos.

## <a name="microsoft-sentinel-as-a-soar-solution"></a>Microsoft Sentinel como solución de SOAR

### <a name="the-problem"></a>El problema.

Los equipos de SIEM y SOC normalmente se sobrecargan con alertas e incidentes de seguridad de forma regular, en volúmenes tan grandes que el personal disponible está abrumado. Esto genera, con demasiada frecuencia, situaciones en las que se ignoran muchas alertas y no se pueden investigar muchos incidentes, lo que hace que la organización sea vulnerable a ataques que pasan desapercibidos.

### <a name="the-solution"></a>La solución

Microsoft Sentinel, además de ser un sistema de administración de eventos e información de seguridad (SIEM), también es una plataforma para orquestación de seguridad, automatización y respuesta (SOAR). Uno de sus objetivos principales es automatizar las tareas de enriquecimiento, respuesta y análisis recurrentes y predecibles que sean responsabilidad del centro de operaciones de seguridad y el personal (SOC/SecOps), liberando tiempo y recursos para realizar una investigación más detallada de las amenazas avanzadas y para buscarlas. La automatización adopta diferentes formas en Microsoft Sentinel, desde reglas de automatización que administran de forma centralizada la automatización del control de incidentes y la respuesta que se les ofrece, hasta cuadernos de estrategias que ejecutan secuencias predeterminadas de acciones para proporcionar una automatización avanzada potente y flexible para sus tareas de respuesta a amenazas.

## <a name="automation-rules"></a>Regalas de automatización

Las reglas de automatización son un nuevo concepto de Microsoft Sentinel. Esta característica permite a los usuarios administrar de forma centralizada la automatización del control de incidentes. Además de permitirle asignar cuadernos de estrategias a incidentes (no solo a las alertas, como hasta ahora), las reglas de automatización también permiten automatizar las respuestas de varias reglas de análisis a la vez, etiquetar, asignar o cerrar incidentes automáticamente sin necesidad de cuadernos de estrategias y controlar el orden de las acciones que se ejecutan. Las reglas de automatización agilizarán el uso de la automatización en Microsoft Sentinel y le permitirán simplificar flujos de trabajo complejos de los procesos de orquestación de incidentes.

Consulte esta [explicación completa de las reglas de automatización](automate-incident-handling-with-automation-rules.md) para obtener más información.

> [!IMPORTANT]
>
> - La característica de **reglas de automatización** se encuentra actualmente en **versión preliminar**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="playbooks"></a>Playbooks

Un cuaderno de estrategias es una colección de acciones y lógica de respuesta y corrección que se puede ejecutar desde Microsoft Sentinel de forma rutinaria. Un cuaderno de estrategias puede ayudarle a automatizar y organizar la respuesta a las amenazas, se puede integrar con otros sistemas de manera interna y externa y se puede configurar para ejecutarse automáticamente en respuesta a alertas o incidentes específicos, cuando se desencadena mediante una regla de análisis o una regla de automatización, respectivamente. También se puede ejecutar manualmente a petición, en respuesta a las alertas, desde la página de incidentes.

Los cuadernos de estrategias de Microsoft Sentinel se basan en flujos de trabajo integrados en [Azure Logic Apps](../logic-apps/logic-apps-overview.md), un servicio en la nube que le ayuda a programar, automatizar y organizar tareas y flujos de trabajo en los sistemas de toda la empresa. Esto significa que los cuadernos de estrategias pueden aprovechar toda la eficacia y la personalización de las funciones de integración y orquestación de Logic Apps y las herramientas de diseño fáciles de usar, así como el grado de escalabilidad, confiabilidad y servicio del nivel 1 de Azure.

Consulte esta [explicación completa de los cuadernos de estrategias](automate-responses-with-playbooks.md) para obtener más información.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido cómo Microsoft Sentinel usa la automatización para ayudar a su SOC a trabajar de forma más eficiente y eficaz.

- Para obtener información sobre la automatización del control de incidentes, consulte [Automatización del control de incidentes en Microsoft Sentinel](automate-incident-handling-with-automation-rules.md).
- Para obtener más información sobre las opciones avanzadas de automatización, consulte [Automatización de la respuesta a amenazas con cuadernos de estrategias de Microsoft Sentinel](automate-responses-with-playbooks.md).
- Para obtener ayuda con la implementación de reglas de automatización y cuadernos de estrategias, consulte [Tutorial: Uso de cuadernos de estrategias para automatizar las respuestas a amenazas en Microsoft Sentinel](tutorial-respond-threats-playbook.md).
