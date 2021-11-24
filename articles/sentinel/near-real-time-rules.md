---
title: Detección rápida de amenazas con reglas de análisis casi en tiempo real (NRT) en Microsoft Sentinel | Microsoft Docs
description: En este artículo se explica cómo las nuevas reglas de análisis casi en tiempo real (NRT) pueden ayudarle a detectar amenazas rápidamente en Microsoft Sentinel.
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
ms.openlocfilehash: 1b46989647146d433f86b7e9698222900fb455dd
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132518857"
---
# <a name="detect-threats-quickly-with-near-real-time-nrt-analytics-rules-in-microsoft-sentinel"></a>Detección rápida de amenazas con reglas de análisis casi en tiempo real (NRT) en Microsoft Sentinel

> [!IMPORTANT]
>
> - Las reglas casi en tiempo real (NRT) se encuentran actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="what-are-near-real-time-nrt-analytics-rules"></a>¿Qué son las reglas de análisis casi en tiempo real (NRT)?

Cuando se enfrenta a amenazas de seguridad, el tiempo y la velocidad son fundamentales. Debe tener en cuenta las amenazas a medida que se materializan para que pueda analizarlas y reaccionar rápidamente a fin de contenerlas. Las reglas de análisis casi en tiempo real (NRT) de Microsoft Sentinel ofrecen una detección de amenazas rápida, más cercana a la de un SIEM local, y la capacidad de acortar los tiempos de respuesta en escenarios específicos.

Las [reglas de análisis casi en tiempo real](detect-threats-built-in.md#nrt) de Microsoft Sentinel ofrecen una detección de amenazas al minuto integrada. Este tipo de regla se diseñó para tener una gran capacidad de respuesta mediante la ejecución de su consulta a intervalos de un minuto de diferencia.

## <a name="how-do-they-work"></a>¿Cómo funcionan?

Las reglas NRT se codifican de forma rígida para que se ejecuten una vez cada minuto y capturen los eventos ingeridos en el minuto anterior, de modo que puedan proporcionarle información lo más al minuto posible.

A diferencia de las reglas programadas normales que se ejecutan con un retraso integrado de cinco minutos para tener en cuenta el retraso del tiempo de ingesta, las reglas NRT se ejecutan con un retraso de solo dos minutos, y resuelven el problema del retraso de ingesta consultando el tiempo de ingesta de los eventos en lugar de su tiempo de generación en el origen (el campo TimeGenerated). Como resultado, se producen mejoras de frecuencia y precisión en las detecciones. (Para comprender este problema de una forma más completa, consulte [Programación de consultas y umbral de alertas](detect-threats-custom.md#query-scheduling-and-alert-threshold) y [Control del retraso de ingesta en las reglas de análisis programadas](ingestion-delay.md).)

Las reglas NRT tienen muchas de las características y funcionalidades de las reglas de análisis programadas. Hay disponible el conjunto completo de funcionalidades de enriquecimiento de alertas: puede asignar entidades y exponer detalles personalizados, y puede configurar contenido dinámico para los detalles de las alertas. Puede elegir cómo se agrupan las alertas en incidentes, puede suprimir temporalmente la ejecución de una consulta después de generar un resultado y puede definir reglas de automatización y cuadernos de estrategias para que se ejecuten en respuesta a las alertas e incidentes generados a partir de la regla.

Por el momento, estas plantillas tienen una aplicación limitada como se describe a continuación, pero la tecnología está evolucionando y creciendo rápidamente.

## <a name="considerations"></a>Consideraciones
Actualmente, las siguientes limitaciones rigen el uso de reglas NRT:

1. En este momento, no se pueden definir más de 20 reglas por cliente.

1. Como se trata de un nuevo tipo de regla, su sintaxis está limitada actualmente, pero evolucionará gradualmente. Por lo tanto, en este momento están en vigor las restricciones siguientes:

    1. La consulta definida en una regla NRT solo puede hacer **referencia a una tabla**. Sin embargo, las consultas pueden hacer referencia a varias listas de reproducción y a fuentes de inteligencia sobre amenazas.

    1. No se pueden usar uniones ni combinaciones.

    1. Dado que este tipo de regla es casi en tiempo real, hemos reducido el retraso integrado al mínimo (dos minutos).

    1. Dado que las reglas NRT usan el tiempo de ingesta en lugar del tiempo de generación de eventos (representado por el campo TimeGenerated), puede omitir de forma segura el retraso del origen de datos y la latencia del tiempo de ingesta (consulte la información anterior).

    1. Las consultas solo se pueden ejecutar dentro de un área de trabajo única. No hay ninguna funcionalidad de ejecución en varias áreas de trabajo.

    1. No hay ninguna agrupación de eventos. Las reglas NRT generan una sola alerta que agrupa todos los eventos aplicables.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido cómo funcionan las reglas de análisis casi en tiempo real (NRT) en Microsoft Sentinel.

- Obtenga información sobre cómo [crear reglas NRT](create-nrt-rules.md).
- Obtenga información [sobre otros tipos de reglas de análisis.](detect-threats-built-in.md)
