---
title: Modelos de diseño de divulgación
titleSuffix: Azure Cognitive Services
description: Modelos de diseño y procedimientos recomendados para la divulgación.
services: cognitive-services
author: sharonlo101
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: shlo
ms.openlocfilehash: 7a0bf694aca369e5ed774a8bb704973d49683afb
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131507992"
---
# <a name="disclosure-design-patterns"></a>Modelos de diseño de divulgación
Tras determinar el [nivel de divulgación](concepts-disclosure-guidelines.md#disclosure-assessment) adecuado para su experiencia de voz sintética, es un buen momento para explorar posibles modelos de diseño.
## <a name="overview"></a>Información general
Hay un abanico de modelos de diseño de divulgación que puede aplicar a su experiencia de voz sintética. Si el resultado de la evaluación de la divulgación fue "nivel alto de divulgación", se recomienda la [**divulgación explícita**](#explicit-disclosure); es decir, comunicar los orígenes de la voz sintética directamente. La [**divulgación implícita**](#implicit-disclosure) incluye guías y modelos de interacción que benefician a las experiencias de voz, independientemente de si los niveles de divulgación necesarios son altos o bajos.
![Espectro de modelos de divulgación](media/responsible-ai/disclosure-patterns/affordances.png)






| Modelos de divulgación explícitos                                                                                                                                                                                    | Modelos de divulgación implícitos                                                                 |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------|
|[Introducción transparente](#transparent-introduction)<br> [Introducción transparente verbal](#verbal-transparent-introduction)<br>  [Firma explícita](#explicit-byline)<br>  [Personalización y calibración](#customization-and-calibration)<br> [Divulgación parental](#parental-disclosure)<br> [Oportunidades para obtener más información sobre cómo se realizó la voz](#providing-opportunities-to-learn-more-about-how-the-voice-was-made) | [Divulgación de funcionalidad](#capability-disclosure)<br>[Indicaciones implícitas y retroalimentación](#implicit-cues--feedback)<br> [Transparencia de conversación](#conversational-transparency) |



Use el siguiente gráfico para hacer referencia directamente a los modelos que se aplican a la voz sintética. Algunas de las demás condiciones de este gráfico también pueden aplicarse a su escenario:<br/>



| Si su experiencia de voz sintética... | Recomendaciones | Patrones de diseño |
| --- | --- | --- |
| Requiere un nivel de divulgación alto  | Use al menos un modelo explícito e indicaciones implícitas por adelantado para ayudar a los usuarios a crear asociaciones. |[Divulgación explícita](#explicit-disclosure)<br>[Divulgación implícita](#implicit-disclosure)  |
| Requiere un nivel de divulgación bajo | La divulgación puede ser mínima o innecesaria, pero podría beneficiarse de algunos modelos implícitos. | [Divulgación de funcionalidad](#capability-disclosure)<br>[Transparencia de conversación](#conversational-transparency)  |
| Tiene un alto nivel de interacción | Compile a largo plazo y ofrezca varios puntos de entrada para la divulgación a lo largo del recorrido del usuario. Se recomienda encarecidamente tener una experiencia de incorporación. | [Introducción transparente](#transparent-introduction)<br>[Personalización y calibración](#customization-and-calibration)<br>[Divulgación de funcionalidad](#capability-disclosure) |
| Incluye a los niños como el público principal previsto | Diríjase a los padres como público de divulgación principal y asegúrese de que pueden comunicarse de forma eficaz con los niños.  | [Divulgación parental](#parental-disclosure)<br>[Introducción transparente verbal](#verbal-transparent-introduction)<br> [Divulgación implícita](#implicit-disclosure)<br> [Transparencia de conversación](#conversational-transparency)  |
| Incluye a usuarios ciegos o personas con poca visión como el público principal previsto  | Debe incluir a todos los usuarios y asegurarse de que cualquier forma de divulgación visual tenga un texto alternativo o efectos sonoros asociados. Cumpla los estándares de accesibilidad para la relación de contraste y el tamaño de la pantalla. Utilice indicaciones auditivas para comunicar la divulgación.  | [Introducción transparente verbal](#verbal-transparent-introduction) <br>[Indicaciones auditivas](#implicit-cues--feedback)<br>[Indicaciones hápticas](#implicit-cues--feedback)<br>[Transparencia de conversación](#conversational-transparency)<br>[Estándares de accesibilidad](https://www.microsoft.com/accessibility) |
| No tiene pantalla, no tiene dispositivo o usa la voz como el modo principal o único de interacción. | Utilice indicaciones auditivas para comunicar la divulgación. | [Introducción transparente verbal](#verbal-transparent-introduction) <br> [Indicaciones auditivas](#implicit-cues--feedback)  |
| Posiblemente incluye varios usuarios o agentes de escucha (por ejemplo, asistente personal en varios hogares).  | Tenga en cuentan los distintos contextos de usuario y niveles de comprensión y ofrezca varias oportunidades para la divulgación en el recorrido del usuario.  | [Introducción transparente (usuario habitual)](#transparent-introduction)<br> [Oportunidades para obtener más información sobre cómo se realizó la voz](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)<br> [Transparencia de conversación](#conversational-transparency)  |



## <a name="explicit-disclosure"></a>Divulgación explícita
Si la experiencia de voz sintética requiere un nivel de divulgación elevado, es mejor usar al menos uno de los siguientes modelos explícitos para indicar claramente la naturaleza sintética de la voz.
### <a name="transparent-introduction"></a>Introducción transparente

Antes de que comience la experiencia de voz, introduzca el asistente digital; sea totalmente transparente sobre los orígenes de la voz y sus funcionalidades. El momento óptimo para usar este modelo es cuando se incorpora un nuevo usuario o cuando se introducen nuevas características para un usuario habitual. La implementación de indicaciones implícitas durante una introducción ayuda a los usuarios a formar un modelo mental sobre la naturaleza sintética del agente digital.

#### <a name="first-time-user-experience"></a>Experiencia de usuario nuevo

![Introducción transparente durante la primera experiencia de ejecución](media/responsible-ai/disclosure-patterns/transparent-intro-first.png) <br>
*La voz sintética se introduce durante la incorporación de un nuevo usuario.*

Recomendaciones
- Describa que la voz es artificial (por ejemplo, &quot;digital&quot;)
- Describa lo que el agente es capaz de hacer.
- Indique explícitamente los orígenes de la voz.
- Ofrezca un punto de entrada para obtener más información acerca de la voz sintética.

#### <a name="returning-user-experience"></a>Experiencia del usuario habitual

Si un usuario omite la experiencia de incorporación, siga ofreciendo puntos de entrada a la experiencia de introducción transparente hasta que el usuario active la voz por primera vez.
<br/>

![Introducción transparente durante la experiencia del usuario habitual](media/responsible-ai/disclosure-patterns/transparent-intro-return.png)<br/>
*Proporcione un punto de entrada coherente con la experiencia de voz sintética. Permita que el usuario vuelva a la experiencia de incorporación cuando desencadene la voz por primera vez en cualquier momento del recorrido del usuario.*


### <a name="verbal-transparent-introduction"></a>Introducción transparente verbal

Un aviso hablado que indica los orígenes de la voz del asistente digital es lo suficientemente explícito como para lograr la divulgación. Este modelo es el mejor para escenarios con un nivel de divulgación elevado, donde la voz es el único modo de interacción disponible.
<br/>

![Introducción transparente hablada](media/responsible-ai/disclosure-patterns/spoken-prompt-1.png)
<br/>*Use una introducción transparente cuando haya momentos en la experiencia del usuario en los que ya pueda introducir o atribuir la voz de una persona.*


![Introducción transparente hablada en primera persona](media/responsible-ai/disclosure-patterns/spoken-prompt-2.png)<br/>
*Para una transparencia adicional, el actor de voz puede revelar los orígenes de la voz sintética en primera persona.*

### <a name="explicit-byline"></a>Firma explícita

Use este modelo si el usuario va a interactuar con un reproductor de audio o un componente interactivo para desencadenar la voz.


![Firma explícita en un escenario de noticias en los medios de comunicación](media/responsible-ai/disclosure-patterns/explicit-byline.png) <br/>
*Una firma explícita es la atribución de la procedencia de la voz.*

Recomendaciones

- Ofrezca un punto de entrada para obtener más información acerca de la voz sintética.

### <a name="customization-and-calibration"></a>Personalización y calibración

Proporcione a los usuarios control sobre el modo en que el asistente digital responde a ellos (es decir, cómo suena la voz).  Cuando un usuario interactúa con un sistema en sus propios términos y con objetivos específicos en mente, por definición, ya ha entendido que no se trata de una persona real.

#### <a name="user-control"></a>Control de usuario

Ofrezca opciones que tengan un impacto significativo y perceptible en la experiencia de voz sintética.

![Preferencias de usuario](media/responsible-ai/disclosure-patterns/customization-user-control.png)<br/>
*Las preferencias de usuario permiten a los usuarios personalizar y mejorar su experiencia.*

Recomendaciones

- Permita a los usuarios personalizar la voz (por ejemplo, seleccionar el idioma y el tipo de voz).
- Proporcione a los usuarios una forma de enseñar al sistema a responder a su voz única (por ejemplo, calibración de voz, comandos personalizados).
- Optimice las interacciones contextuales o generadas por el usuario (por ejemplo, recordatorios).

#### <a name="persona-customization"></a>Personalización de roles

Ofrezca maneras de personalizar la voz del asistente digital. Si la voz está basada en una persona famosa o en ampliamente reconocible, considere la posibilidad de usar las introducciones visuales y orales cuando los usuarios obtengan una vista previa de la voz.

![Personalización de la voz](media/responsible-ai/disclosure-patterns/customization-voice-type.png)<br/>
*Ofrecer la posibilidad de seleccionar entre un conjunto de voces ayuda a transmitir la naturaleza artificial.*

Recomendaciones
- Permita a los usuarios obtener una vista previa del sonido de cada voz.
- Use una introducción auténtica para cada voz.
- Ofrezca puntos de entrada para obtener más información acerca de la voz sintética.

### <a name="parental-disclosure"></a>Divulgación parental

Además de cumplir con las regulaciones de COPPA, proporcione divulgación a los padres si el público previsto principal es joven y el nivel de exposición es alto. En el caso de los usos confidenciales, considere la posibilidad de restringir el acceso a la experiencia hasta que un adulto haya confirmado el uso de la voz sintética. Anime a los padres a que comuniquen el mensaje a sus hijos.

![Divulgación para padres](media/responsible-ai/disclosure-patterns/parental-disclosure.png)<br/>
*Una introducción transparente optimizada para los padres garantiza que un adulto sea consciente de la naturaleza sintética de la voz antes de que un niño interactúe con ella.*

Recomendaciones

- Diríjase a los padres como público principal para la divulgación.
- Anime a los padres a comunicar la divulgación a sus hijos.
- Ofrezca puntos de entrada para obtener más información acerca de la voz sintética.
- Controle el acceso a la experiencia al solicitar a los padres una sencilla pregunta &quot;de seguridad&quot; para mostrar que han leído la divulgación.

### <a name="providing-opportunities-to-learn-more-about-how-the-voice-was-made"></a>Oportunidades para obtener más información sobre cómo se realizó la voz

Ofrezca puntos de entrada contextuales a una página, un elemento emergente o un sitio externo que proporcione más información acerca de la tecnología de voz sintética. Por ejemplo, podría mostrar un vínculo para obtener más información durante la incorporación o cuando el usuario solicite más información durante la conversación.

![Punto de entrada para obtener más información](media/responsible-ai/disclosure-patterns/learn-more-entry-point.png)<br/>
*Ejemplo de un punto de entrada para ofrecer la oportunidad de obtener más información sobre la voz sintetizada.*

Una vez que un usuario solicita más información sobre la voz sintética, el objetivo principal es educarlo sobre los orígenes de la voz sintética y ser transparente sobre la tecnología.

![Proporcionar a los usuarios más información acerca de la voz sintética](media/responsible-ai/disclosure-patterns/learn-more.png)<br/>
*Se puede ofrecer más información en el sitio de ayuda de un sitio externo.*

Recomendaciones

- Simplifique los conceptos complejos y evite el uso de jerga jurídica y técnica.
- No oculte este contenido en declaraciones de privacidad y condiciones de uso.
- Mantenga el contenido conciso y use imágenes cuando estén disponibles.

## <a name="implicit-disclosure"></a>Divulgación implícita

La coherencia es la clave para lograr la divulgación implícitamente a lo largo del recorrido del usuario. El uso coherente de indicaciones visuales y auditivas en los dispositivos y modos de interacción puede ayudar a crear asociaciones entre los modelos implícitos y la divulgación explícita.

![Coherencia de las indicaciones implícitas](media/responsible-ai/disclosure-patterns/consistency.png)

### <a name="implicit-cues--feedback"></a>Indicaciones implícitas y comentarios

El antropomorfismo puede manifestarse de maneras diferentes, desde la representación visual real del agente hasta la voz, los sonidos, los patrones de luz, el rebote de formas o incluso la vibración de un dispositivo. Al definir el rol, aproveche las indicaciones implícitas y los patrones de retroalimentación, en lugar de tener como objetivo un avatar de apariencia muy humana. Se trata de una manera de minimizar la necesidad de una divulgación más explícita.

![Indicaciones visuales y retroalimentación](media/responsible-ai/disclosure-patterns/visual-affordances.png)<br/>
*Estas guías ayudan a antropomorfizar al agente sin volverlo demasiado humano. También pueden convertirse en mecanismos de divulgación eficaces por su cuenta cuando se usan de forma coherente con el tiempo.*

Tenga en cuenta los diferentes modos de interacciones de su experiencia al incorporar los siguientes tipos de indicaciones:

| Indicaciones visuales                                                                                                                                                               | Indicaciones auditivas                                                      | Indicaciones hápticas |
|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------|-------------|
|  Avatar <br>Indicaciones dinámicas en tiempo real (por ejemplo, animaciones).<br> Indicaciones que no están en pantalla (por ejemplo, luces y patrones en un dispositivo).<br>  | Sonicon (por ejemplo, un breve sonido distintivo, una serie de notas musicales). | Vibración   |

### <a name="capability-disclosure"></a>Divulgación de funcionalidad

La divulgación se puede lograr de forma implícita al establecer expectativas precisas de lo que el asistente digital es capaz de usar. Proporcione comandos de ejemplo para que los usuarios puedan aprender cómo interactuar con el asistente digital y ofrezca ayuda contextual para obtener más información sobre la voz sintética durante las primeras fases de la experiencia.

![Ejemplo de respuestas predeterminadas a una conversación que puede crear.](media/responsible-ai/disclosure-patterns/capability-disclosure.png)<br/>

### <a name="conversational-transparency"></a>Transparencia de conversación

Cuando las conversaciones se encuentren en rutas de acceso inesperadas, considere la posibilidad de crear respuestas predeterminadas que puedan ayudar a restablecer las expectativas, reforzar la transparencia y dirigir a los usuarios hacia las rutas correctas. También hay oportunidades para usar la divulgación explícita en la conversación.

![Control de rutas de acceso inesperadas](media/responsible-ai/disclosure-patterns/conversational-transparency-1.png)<br/>

<br/>
Las preguntas que no están relacionadas o son &quot;personales&quot; dirigidas al agente son un buen momento para recordar a los usuarios la naturaleza sintética del agente y dirigirlos para que interactúen con ella adecuadamente o para transferirlos con una persona real.

![Control de preguntas no relacionadas](media/responsible-ai/disclosure-patterns/conversational-transparency-2.png)<br/>

## <a name="when-to-disclose"></a>Momento de divulgación

Hay muchas oportunidades de divulgación a lo largo del recorrido del usuario. Diseñe para el primer uso, el segundo, el número n..., pero también acepte los &quot;errores&quot; para resaltar la transparencia. Por ejemplo, cuando el sistema comete un error o cuando el usuario detecta una limitación en las funcionalidades del agente.

![Oportunidades de divulgación durante el recorrido del usuario](media/responsible-ai/disclosure-patterns/touchpoints.png)<br/>

Ejemplo de un recorrido de usuario estándar de asistente digital que resalta varias oportunidades de divulgación.

### <a name="up-front"></a>Por adelantado

El momento óptimo para la divulgación es la primera vez que una persona interactúa con la voz sintética.  En un escenario de asistente de voz personal, esto ocurriría durante la incorporación o la primera vez que el usuario prácticamente desempaqueta la experiencia. En otros escenarios, podría ser la primera vez que una voz sintética lee contenido en un sitio web o la primera vez que un usuario interactúa con un personaje virtual.

- [Introducción transparente](#transparent-introduction)
- [Divulgación de funcionalidad](#capability-disclosure)
- [Personalización y calibración](#customization-and-calibration)
- [Indicaciones implícitas](#implicit-cues--feedback)

### <a name="upon-request"></a>A petición

Los usuarios deben poder acceder fácilmente a información adicional, controlar las preferencias y recibir comunicación transparente en cualquier punto del recorrido del usuario cuando se solicite.

- [Oportunidades para obtener más información sobre cómo se realizó la voz](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Personalización y calibración](#customization-and-calibration)
- [Transparencia de conversación](#conversational-transparency)

### <a name="continuously"></a>Continuamente

Use los modelos de diseño implícitos que mejoran la experiencia del usuario continuamente.

- [Divulgación de funcionalidad](#capability-disclosure)
- [Indicaciones implícitas](#implicit-cues--feedback)

### <a name="when-the-system-fails"></a>Cuando se produce un error en el sistema

Use la divulgación como una oportunidad para que se produzcan errores con gracia.

- [Transparencia de conversación](#conversational-transparency)
- [Oportunidades para obtener más información sobre cómo se realizó la voz](#providing-opportunities-to-learn-more-about-how-the-voice-was-made)
- [Entrega a personas](#conversational-transparency)

## <a name="additional-resources"></a>Recursos adicionales
- [Directrices de bot de Microsoft](https://www.microsoft.com/research/uploads/prod/2018/11/Bot_Guidelines_Nov_2018.pdf)
- [Directrices sobre el diseño de Cortana](/cortana/voice-commands/voicecommand-design-guidelines)
- [Directrices para el diseño de voz de UWP de Microsoft Windows](/windows/uwp/design/input/speech-interactions)
- [Directrices de comandos de voz de Microsoft Windows Mixed Reality](/windows/mixed-reality/voice-design#top-things-users-should-know-about-speech-in-mixed-reality)

## <a name="see-also"></a>Consulte también

* [Divulgación de talento de voz](/legal/cognitive-services/speech-service/disclosure-voice-talent?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)
* [Directrices para la implementación responsable de la tecnología de voz sintética](concepts-guidelines-responsible-deployment-synthetic.md)
* [Procedimiento de divulgación](concepts-disclosure-guidelines.md)
