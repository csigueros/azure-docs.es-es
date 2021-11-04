---
title: Directrices de diseño de divulgación de información
titleSuffix: Azure Cognitive Services
description: Introducción a las directrices de diseño de divulgación y evaluación del nivel de divulgación.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 17dc58adf6ea6b1ab285a2ec4d93d7067448d0b9
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131510751"
---
# <a name="disclosure-design-guidelines"></a>Directrices de diseño de divulgación de información
Aprenda a generar y mantener la confianza con los clientes al ser transparentes sobre la naturaleza sintética de su experiencia de voz.

## <a name="what-is-disclosure"></a>¿Qué es la divulgación?

La divulgación es un medio para que los usuarios sepan que la voz que están escuchando o con la que están interactuando está generada de forma sintética.

## <a name="why-is-disclosure-necessary"></a>¿Por qué es necesaria la divulgación?

La necesidad de divulgar los orígenes sintéticos de una voz generada por ordenador es relativamente nueva. En el pasado, las voces generadas por ordenador eran obvias, ya que nadie las confundiría con una persona real. Sin embargo, todos los días el realismo de las voces sintéticas mejora y se vuelve cada vez más indistinguible de las voces humanas.

## <a name="goals"></a>Objetivos
Estos son los principios a tener en cuenta al diseñar experiencias de voz sintéticas:

**Reforzar la confianza**
<br>Diseñe con la intención de que se produzca un error en la prueba de Turing sin deteriorar la experiencia. Avise a los usuarios que están interactuando con una voz sintética mientras les permite interactuar sin problemas con la experiencia.

**Adaptarse al contexto de uso**
<br>Comprenda cuándo, dónde y cómo interactuarán los usuarios con la voz sintética para proporcionar el tipo correcto de divulgación en el momento adecuado.

**Establecer expectativas claras**
<br>Permita a los usuarios detectar y comprender fácilmente las funcionalidades del agente. Ofrezca oportunidades para obtener más información acerca de la tecnología de voz sintética a petición.

**Aceptar los errores**
<br>Utilice los errores para reforzar las funcionalidades del agente.

## <a name="how-to-use-this-guide"></a>Cómo utilizar esta guía

Esta guía le ayuda a determinar qué patrones de divulgación son los que mejor se adaptan a su experiencia de voz sintética. A continuación, se ofrecen ejemplos de cómo y cuándo utilizarlos. Cada uno de estos modelos está diseñado para maximizar la transparencia con los usuarios sobre la síntesis de voz, al mismo tiempo que se mantiene el diseño humano.

Teniendo en cuenta la gran cantidad de guías de diseño sobre experiencias de voz, nos centramos aquí en lo siguiente:

1. [**Evaluación de la divulgación**](#disclosure-assessment): un proceso para determinar el tipo de divulgación recomendado para su experiencia de voz sintética

2. [**Procedimiento de divulgación**](concepts-disclosure-patterns.md): ejemplos de patrones de divulgación que pueden aplicarse a su experiencia de voz sintética.

3. [**Momento de divulgación**](concepts-disclosure-patterns.md#when-to-disclose): momentos óptimos para divulgar información a lo largo del recorrido del usuario.

## <a name="disclosure-assessment"></a>Evaluación de la divulgación
Tenga en cuenta las expectativas de los usuarios sobre una interacción y el contexto en el que experimentarán la voz. Si el contexto deja claro que una voz sintética está &quot;hablando&quot;, la divulgación puede ser mínima, puntual o incluso innecesaria. Los principales tipos de contexto que afectan a la divulgación incluyen el tipo de rol, el tipo de escenario y el nivel de exposición. También resulta útil considerar quién puede estar escuchando.

### <a name="understand-context"></a>Comprensión del contexto

Utilice esta hoja de cálculo para determinar el contexto de su experiencia de voz sintética. La aplicará en el paso siguiente, donde determinará el nivel de divulgación.

|                                    | Contexto de uso                                                                                                                                                                                                                                                                                                                                                       | Posibles riesgos y desafíos                                                                                                                                                                                                                                                                                                                                                                       |
|------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **1. Tipo de rol**               | **Si se cumple alguna de las siguientes condiciones, el rol se ajusta a la categoría "rol similar al humano":**<br><br><ul><li> El rol personifica a una persona real, ya sea una representación ficticia o si no (por ejemplo, fotografía o representación generada por ordenador de una persona real).<br><br><li> La voz sintética se basa en la voz de una persona real ampliamente reconocible (por ejemplo, una persona famosa o una figura política). | Cuanto más representaciones similares al humano proporcione su rol, más probable será que un usuario lo asocie a una persona real o que crea que el contenido es de una persona real en lugar de generado por un ordenador. </ul>                                                                                                                                                                      |
| **2. Tipo de escenario**            | **Si se cumple alguna de las siguientes condiciones, su experiencia de voz se ajusta en la categoría "confidencial":**<br><br><ul><li> Obtiene o muestra información personal del usuario. <br><br> <li> Difunde noticias u información confidencial (por ejemplo, una alerta de emergencia).<br><br><li> Pretende ayudar a los usuarios reales a comunicarse entre sí (por ejemplo, lee correos electrónicos o mensajes de texto personales).<br><br> <li> Proporciona asistencia médica o sanitaria. </ul>            | Es posible que el uso de voz sintética no resulte adecuado o confiable para las personas que la usan cuando los temas están relacionados con asuntos confidenciales, personales o urgentes. También es posible que esperen el mismo nivel de empatía y conciencia del contexto que con un usuario real. |
| **3. Nivel de exposición** |**Lo más probable es que la experiencia de voz se ajuste a la categoría "nivel alto" si:** <br><br><ul><li>El usuario oirá o interactuará con la voz sintética con frecuencia o durante un largo período de tiempo. </ul>                                                                                                                                                                             | La importancia de ser transparente y de generar confianza en los usuarios es incluso mayor cuando se establecen relaciones a largo plazo.                                                                                                                                                                                                                                                                      |

### <a name="determine-disclosure-level"></a>Determinación del nivel de divulgación

Use el siguiente diagrama para determinar si su experiencia de voz sintética requiere un nivel de divulgación alto o bajo en función del contexto de uso.

  ![Diagrama de evaluación de la divulgación](media/responsible-ai/disclosure-guidelines/flowchart.png)

## <a name="see-also"></a>Consulte también

* [Modelos de diseño de divulgación](concepts-disclosure-patterns.md)
* [Divulgación de talento de voz](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Directrices para la implementación responsable de la tecnología de voz sintética](concepts-guidelines-responsible-deployment-synthetic.md)
