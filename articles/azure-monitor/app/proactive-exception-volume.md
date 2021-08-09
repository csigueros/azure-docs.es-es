---
title: 'Aumento anómalo del volumen de excepciones: Azure Application Insights'
description: Supervise las excepciones de las aplicaciones con la detección inteligente en Azure Application Insights para detectar patrones poco habituales del volumen de excepciones.
ms.topic: conceptual
ms.date: 12/08/2017
ms.openlocfilehash: 4220415a0ab907c2cdf9268a59a4e85400cad86c
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111536684"
---
# <a name="abnormal-rise-in-exception-volume-preview"></a>Aumento anormal del volumen de excepciones (versión preliminar)

>[!NOTE]
>Puede migrar los recursos de Application Insights a la detección inteligente basada en alertas (versión preliminar). La migración crea reglas de alerta para los distintos módulos de detección inteligente. Una vez creadas, puede administrar y configurar dichas reglas como cualquier otra regla de Azure Monitor. También puede configurar grupos de acciones para estas reglas, lo que habilita varios métodos para realizar acciones o desencadenar notificaciones en nuevas detecciones.
>
> Para obtener más información, consulte el artículo sobre la [migración de alertas de detección inteligente](../alerts/alerts-smart-detections-migration.md).

La detección inteligente analiza de forma automática las excepciones generadas en la aplicación y puede avisarle sobre patrones inusuales en la telemetría de excepciones.

Esta característica no requiere ninguna configuración especial, excepto la [configuración de los informes de excepciones](./asp-net-exceptions.md#set-up-exception-reporting) de la aplicación. Se activa cuando la aplicación genere suficiente telemetría de excepciones.

## <a name="when-would-i-get-this-type-of-smart-detection-notification"></a>¿Cuándo recibiré este tipo de notificación de detección inteligente?
Este tipo de notificación se obtiene si la aplicación muestra un aumento anómalo del número de excepciones de un tipo específico, durante un día. Este número se compara con una línea base que se ha calculado durante los siete días anteriores.
Se usan algoritmos de aprendizaje automático para detectar el aumento en el número de excepciones, a la vez que se tiene en cuenta un crecimiento natural en el uso de la aplicación.

## <a name="does-my-app-definitely-have-a-problem"></a>Entonces, ¿mi aplicación tiene un problema?
No, una notificación no significa que la aplicación tenga un problema. Aunque un número excesivo de excepciones suele indicar un problema de la aplicación, es posible que estas sean benignas y que la aplicación las controle correctamente.

## <a name="how-do-i-fix-it"></a>¿Cómo puedo corregirlo?
Las notificaciones incluyen información de diagnóstico para facilitar el proceso de diagnóstico:
1. **Evaluación de prioridades**. La notificación muestra el número de usuarios o solicitudes afectados. Esta información puede ayudarle a asignar una prioridad al problema.
2. **Ámbito.** ¿El problema afecta a todo el tráfico o solo a alguna operación? Esta información puede obtenerse de la notificación.
3. **Diagnóstico.** La detección contiene información sobre el método desde el que se generó la excepción y el tipo de excepción. También puede usar los elementos relacionados y los vínculos de informes para obtener información que puede ayudarle a efectuar un diagnóstico más exhaustivo del problema.
