---
title: 'Comandos personalizados: servicio de voz'
titleSuffix: Azure Cognitive Services
description: Información general sobre las características, funcionalidades y restricciones de Comandos personalizados, que es una solución para crear aplicaciones de voz.
services: cognitive-services
author: trrwilson
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: travisw
ms.openlocfilehash: db0f78f2f3aef3295837d921d7d70866ed8b8769
ms.sourcegitcommit: d01c2b2719e363178720003b67b968ac2a640204
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122455815"
---
# <a name="what-is-custom-commands"></a>¿Qué es Comandos personalizados?

Las aplicaciones de voz, como los [asistentes de voz](voice-assistants.md), escuchan a los usuarios y realizan una acción en consecuencia, lo que a menudo implica una respuesta. Utilizan [conversión de voz en texto](speech-to-text.md) para transcribir la voz del usuario y, a continuación, realizan una acción en función de su comprensión del lenguaje natural del texto. Esta acción suele incluir una respuesta hablada del asistente generada con la [conversión de texto en voz](text-to-speech.md). Los dispositivos se conectan con los asistentes mediante el objeto `DialogServiceConnector` del SDK de voz.

Los **comandos personalizados** facilitan la creación de aplicaciones de comandos de voz enriquecidas y optimizadas para las experiencias de interacción que priorizan la voz. Proporcionan una experiencia de creación unificada, un modelo de hospedaje automático y una complejidad relativamente inferior, lo que ayuda a centrarse en la creación de la mejor solución para sus escenarios de comandos de voz.

Comandos personalizados resulta más adecuado para la finalización de tareas o los escenarios de comando y control, y encaja bien con dispositivos de Internet de las cosas (IoT), así como con dispositivos de ambiente y sin periféricos. Entre los ejemplos se incluyen soluciones para los sectores hotelero, minorista y de la automoción, donde se quieren experiencias controladas por voz para huéspedes, la administración de inventarios en la tienda o la funcionalidad en el automóvil.

Si está interesado en crear aplicaciones de conversación complejas, se recomienda que pruebe Bot Framework con la [solución de asistente virtual](/azure/bot-service/bot-builder-enterprise-template-overview). Puede agregar voz a cualquier bot de Bot Framework mediante Direct Line Speech.

Los candidatos adecuados para Comandos personalizados tienen un vocabulario fijo con conjuntos de variables bien definidos. Por ejemplo, las tareas de automatización del hogar, como el control de un termostato, son ideales.

   ![Ejemplos de escenarios de finalización de tareas](media/voice-assistants/task-completion-examples.png "ejemplos de finalización de tareas")

## <a name="getting-started-with-custom-commands"></a>Introducción a Comandos personalizados

El objetivo de Comandos personalizados es reducir la carga cognitiva derivada de aprender las distintas tecnologías y centrarse en la creación de la aplicación de comandos de voz. El primer paso para usar Comandos personalizados es <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">crear un recurso de voz de Azure</a>. Puede crear la aplicación de Comandos personalizados en Speech Studio y publicarla, tras lo cual una aplicación en el dispositivo podrá comunicarse con ella mediante el SDK de Voz.

#### <a name="authoring-flow-for-custom-commands"></a>Flujo de creación de Comandos personalizados
   ![Flujo de creación de Comandos personalizados](media/voice-assistants/custom-commands-flow.png "Flujo de creación de Comandos personalizados")

Siga nuestra guía de inicio rápido para que su primera aplicación de Comandos personalizados ejecute código en menos de 10 minutos.

* [Creación de un asistente de voz mediante Comandos personalizados](quickstart-custom-commands-application.md)

Una vez que haya terminado con la guía de inicio rápido, examine nuestras guías paso a paso para obtener pasos detallados para diseñar, desarrollar, depurar, implementar e integrar una aplicación de Comandos personalizados.

## <a name="building-voice-assistants-with-custom-commands"></a>Creación de asistentes de voz con Comandos personalizados
> [!VIDEO https://www.youtube.com/embed/1zr0umHGFyc]

## <a name="next-steps"></a>Pasos siguientes

* [Obtenga una clave de suscripción gratuita a los servicios de Voz](overview.md#try-the-speech-service-for-free)
* [Consulte nuestro repositorio de asistentes de voz en GitHub para obtener ejemplos](https://aka.ms/speech/cc-samples)
* [Vaya a Speech Studio para probar Custom Commands](https://speech.microsoft.com/customcommands)
* [Obtención del SDK de voz](speech-sdk.md)