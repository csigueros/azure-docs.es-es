---
title: Uso de anomalías de SOC-ML para detectar amenazas en Azure Sentinel | Microsoft Docs
description: En este artículo se explica cómo usar las nuevas funcionalidades de detección de anomalías de SOC-ML en Azure Sentinel.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/28/2021
ms.author: yelevin
ms.openlocfilehash: 51bc9923ce01116137b4ff447b94d08262b0c758
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121734783"
---
# <a name="use-soc-ml-anomalies-to-detect-threats-in-azure-sentinel"></a>Uso de anomalías de SOC-ML para detectar amenazas en Azure Sentinel

> [!IMPORTANT]
>
> - Las anomalías de SOC-ML se encuentran actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

## <a name="what-are-soc-ml-anomalies"></a>¿Qué son las anomalías de SOC-ML?

En el campo de juego de la ciberseguridad se libra una batalla permanente entre atacantes y defensores por tomar ventaja, donde los atacantes siempre encuentran maneras de evadir la detección. Sin embargo, es inevitable que los ataques provoquen un comportamiento inusual en los sistemas atacados. Las anomalías basadas en el aprendizaje automático de SOC-ML de Azure Sentinel pueden identificar este comportamiento con plantillas de reglas analíticas que pueden ponerse a funcionar directamente, sin necesidad de configuraciones adicionales. Aunque las anomalías no indican necesariamente un comportamiento malintencionado o incluso sospechoso por sí mismas, se pueden usar para mejorar las detecciones, las investigaciones y la búsqueda de amenazas.

- **Señales adicionales para mejorar la detección**: los analistas de seguridad pueden usar las anomalías para detectar nuevas amenazas y hacer más eficaces las detecciones existentes. Una sola anomalía no es una señal sólida de comportamiento malintencionado, pero cuando se combina con varias anomalías que ocurren en distintos puntos de la cadena de eliminación, su efecto acumulativo es mucho más fuerte. Los analistas de seguridad también pueden mejorar las detecciones existentes haciendo que el comportamiento inusual identificado por las anomalías sea una condición para que se desencadenen las alertas.

- **Evidencia durante las investigaciones**: los analistas de seguridad también pueden usar anomalías durante las investigaciones para ayudar a confirmar una vulneración, buscar nuevas rutas para investigarla y evaluar su posible impacto. Estas eficiencias reducen el tiempo que los analistas de seguridad dedican a las investigaciones.

- **El inicio de las búsquedas proactivas de amenazas**: los buscadores de amenazas pueden usar anomalías como contexto para ayudar a determinar si sus consultas han detectado un comportamiento sospechoso. Cuando el comportamiento es sospechoso, las anomalías también apuntan a posibles rutas para profundizar en ello. Estas pistas proporcionadas por las anomalías reducen tanto el tiempo para detectar una amenaza como su posibilidad de causar daños.

Las anomalías pueden ser herramientas eficaces, pero son sin duda muy ruidosas. Normalmente requieren una gran cantidad de tediosos ajustes para entornos específicos o un complejo procesamiento posterior. Las plantillas de anomalías de SOC-ML de Azure Sentinel están ajustadas por nuestro equipo de ciencia de datos para proporcionar un valor inmediato, pero si necesita ajustarlas más, el proceso es sencillo y no requiere conocimientos de aprendizaje automático. Los umbrales y parámetros de muchas de las anomalías se pueden configurar y ajustar a través de la ya conocida interfaz de usuario de las reglas de análisis. El rendimiento del umbral y de los parámetros originales puede compararse con los nuevos dentro de la interfaz y ajustarse aún más si es necesario durante una fase de prueba o piloto. Una vez que la anomalía cumple los objetivos de rendimiento, la anomalía con el nuevo umbral o los nuevos parámetros se puede promover a producción con solo hacer clic en un botón. Las anomalías de SOC-ML de Azure Sentinel le permiten obtener el beneficio de las anomalías dejando de lado el trabajo tedioso.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido cómo SOC-ML le ayuda a detectar anomalías en Azure Sentinel.

- Obtenga información sobre cómo [ver, crear, administrar y ajustar reglas de anomalías](work-with-anomaly-rules.md).
- Obtenga información [sobre otros tipos de reglas de análisis.](detect-threats-built-in.md)
