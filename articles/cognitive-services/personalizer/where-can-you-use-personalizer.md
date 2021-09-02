---
title: Dónde y cómo utiliza Personalizer
description: Personalizer se puede aplicar en cualquier situación en la que su aplicación pueda seleccionar el artículo, acción o producto correcto que se quiere mostrar, con el fin de mejorar la experiencia, lograr mejores resultados comerciales o mejorar la productividad.
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 02/18/2020
ms.openlocfilehash: bc8b0a09212c54ae1e0328e5d1dbfd6d8f213981
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122830194"
---
# <a name="where-and-how-to-use-personalizer"></a>Dónde y cómo utiliza Personalizer

Utilice Personalizer en cualquier situación en la que su aplicación necesite seleccionar la acción correcta (contenido) que se quiere mostrar, con el fin de mejorar la experiencia, lograr mejores resultados comerciales o mejorar la productividad.

Personalizer utiliza el aprendizaje de refuerzo para seleccionar qué acción (contenido) mostrar al usuario. La selección puede variar drásticamente según la cantidad, calidad y distribución de los datos enviados al servicio.

## <a name="example-use-cases-for-personalizer"></a>Ejemplos de casos de uso para Personalizer

* **Aclaración de intenciones y desambiguación**: para ayudar a los usuarios a tener una mejor experiencia cuando su intención no sea clara, brinda una opción que esté personalizada.
* **Sugerencias predeterminadas** para menús y opciones: haz que el bot sugiera el elemento más probable de una manera personalizada como primer paso, en lugar de presentar un menú impersonal o una lista de alternativas.
* **Rasgos y tono del bot**: para los bots que pueden variar el tono, el nivel de detalle y el estilo de escritura, piensa en la posibilidad de variar estos rasgos.
* **Contenido de notificaciones y alertas**: decide qué texto usar para las alertas con el fin de llamar más la atención de los usuarios.
* **Tiempos de notificaciones y alertas**: haz un aprendizaje personalizado de cuándo enviar notificaciones a los usuarios para atraerlos más.


## <a name="expectations-required-to-use-personalizer"></a>Expectativas necesarias para usar Personalizer

Puede aplicar Personalizer en situaciones en las que cumple o puede implementar las siguientes directrices.

|Directrices|Explicación|
|--|--|
|Objetivo empresarial|Tiene un objetivo de negocio o de usabilidad para la aplicación.|
|Contenido|Tiene un lugar en la aplicación donde tomar una decisión contextual sobre qué mostrar a los usuarios para mejorar ese objetivo.|
|Cantidad de contenido|Tiene menos de 50 acciones para clasificar por llamada.|
|Adición de datos|La mejor opción puede y debe aprenderse del comportamiento colectivo de los usuarios y de la puntuación total de recompensa.|
|Uso ético|El uso del aprendizaje automático para la personalización sigue las [directrices de uso responsable](ethics-responsible-use.md) y las opciones que se eligen.
|Mejor opción única|La decisión contextual puede expresarse como la clasificación de la mejor opción (acción) de un conjunto limitado de opciones.|
|Resultado puntuado|La eficacia de la opción de clasificación para la aplicación puede determinarse mediante la medición de algún aspecto del comportamiento del usuario y de su expresión en una _[puntuación de recompensa](concept-rewards.md)_ .|
|Tiempo relevante|La puntuación de recompensa no aporta demasiados factores de confusión o externos. La duración del experimento es lo suficientemente baja como para que la puntuación de recompensa pueda calcularse mientras sea pertinente.|
|Suficientes características de contexto|Puede expresar el contexto para la clasificación como una lista de al menos cinco [características](concepts-features.md) que, en su opinión, ayudarían a tomar la decisión correcta, y que no incluye información identificable específica del usuario.|
|Suficientes características de acción|Tiene información sobre cada opción de contenido, o _acción_, como una lista de al menos cinco [características](concepts-features.md) que, en su opinión, ayudarán a Personalizer a tomar la decisión correcta.|
|Datos diarios|Hay suficientes eventos para estar al tanto de la personalización óptima si el problema se desvía con el tiempo (como las preferencias en las noticias o la moda). Personalizer se adaptará al cambio continuo en el mundo real, pero los resultados no serán óptimos si no hay suficientes eventos y datos de los que aprender para detectar y establecer nuevos patrones. Debe elegir un caso de uso que ocurra con suficiente frecuencia. Considere la posibilidad de buscar casos de uso que ocurran al menos 500 veces al día.|
|Datos históricos|La aplicación puede retener datos durante el tiempo suficiente para acumular un historial de al menos 100 000 interacciones. Permite a Personalizer recopilar suficientes datos para realizar evaluaciones sin conexión y optimizar las directivas.|

**No utilice Personalizer** donde el comportamiento personalizado no sea algo que pueda detectarse en todos los usuarios. Por ejemplo, usar Personalizer para sugerir un primer pedido de pizza en una lista de 20 elementos de menú posibles es útil, pero a qué contacto llamar de la lista de contactos del usuario al pedir ayuda con el cuidado de los niños (por ejemplo, "Abuela") no es algo que pueda personalizarse a través de la base de usuarios.

## <a name="how-to-use-personalizer-in-a-web-application"></a>Cómo usar Personalizer en una aplicación web

Agregar un bucle a una aplicación web incluye:

* Decide qué experiencia personalizar, qué acciones y características tienes, qué características de contexto quieres usar y qué recompensas vas a definir.
* Agrega una referencia al SDK de Personalization en la aplicación.
* Llama a Rank API cuando estés listo para personalizar.
* Almacena el eventId. Enviarás una recompensa con Reward API más adelante.
1. Llamar a Activate para el evento una vez que estés seguro de que el usuario ha visto tu página personalizada.
1. Espera a que el usuario seleccione el contenido clasificado.
1. Llama a Reward API para especificar qué tan bien funcionó la salida de Rank API.

## <a name="how-to-use-personalizer-with-a-chat-bot"></a>Cómo usar Personalizer con un bot de chat

En este ejemplo, verás cómo usar Personalization para hacer una sugerencia predeterminada en lugar de enviar al usuario una serie de menús u opciones cada vez.

* Obtén el [código](https://github.com/Azure-Samples/cognitive-services-personalizer-samples/tree/master/samples/ChatbotExample) para este ejemplo.
* Configura la solución del bot. Asegúrate de publicar la aplicación de LUIS.
* Administrar las llamadas a Rank y Reward API para el bot.
    * Agrega código para administrar el procesamiento de intenciones de LUIS. Si se devuelve **None** como intención superior o la puntuación de la intención superior está por debajo de tu umbral de lógica de negocio, envía la lista de intenciones a Personalizer para clasificar las intenciones.
    * Muestra la lista de intención al usuario como vínculos seleccionables, donde la primera intención sea la intención de rango superior de la respuesta de Rank API.
    * Captura la selección del usuario y envíala en la llamada de Reward API.

### <a name="recommended-bot-patterns"></a>Patrones recomendados para bots

* Haz llamadas de Rank API de Personalizer cada vez que se necesite una desambiguación, en lugar de almacenar en caché los resultados para cada usuario. El resultado de la intención de desambiguación puede cambiar con el tiempo para una persona, por lo que permitir que Rank API explore variaciones acelerará el aprendizaje en general.
* Elige una interacción que sea común con muchos usuarios, de modo que tengas suficientes datos para personalizar. Por ejemplo, las preguntas introductorias pueden servir mejor que las aclaraciones más detalladas más adelante en el grafo de la conversación, al que solo es posible que lleguen unos pocos usuarios.
* Usa llamadas a Rank API para habilitar conversaciones "la primera sugerencia es correcta", donde al usuario se le pregunta "¿Quieres X?" o "¿Quisiste decir X?" y el usuario solo puede confirmar; a diferencia de proporcionar opciones al usuario donde este debe elegir entre un menú. Por ejemplo: Usuario: "Me gustaría pedir un café". Bot: "¿Quiere un expreso doble?". De este modo, la señal de recompensa también es fuerte, ya que pertenece directamente a una sugerencia.

## <a name="how-to-use-personalizer-with-a-recommendation-solution"></a>Cómo usar Personalizer con una solución de recomendaciones

Muchas empresas utilizan motores de recomendación, herramientas de marketing y campañas, segmentación y agrupación de audiencias, filtrado colaborativo y otros medios para recomendar productos de un gran catálogo a los clientes.

El [repositorio GitHub de Microsoft Recommenders](https://github.com/Microsoft/Recommenders) proporciona ejemplos y procedimientos recomendados para crear sistemas de recomendación, proporcionados como cuadernos de Jupyter Notebook. Proporciona ejemplos prácticos para la preparación de datos, la creación de modelos, la evaluación, el ajuste y la puesta en marcha de los motores de recomendación, para muchos enfoques comunes, entre ellos xDeepFM, SAR, ALS, RBM y DKN.

Personalizer puede trabajar con un motor de recomendación cuando está presente.

* Los motores de recomendación toman grandes cantidades de elementos (por ejemplo, 500 000) y recomiendan un subconjunto (como los 20 primeros) de cientos o miles de opciones.
* Personalizer toma un pequeño número de acciones con mucha información sobre ellas y las clasifica en tiempo real para un contexto rico determinado, mientras que la mayoría de los motores de recomendación solo usan unos pocos atributos sobre los usuarios, los productos y sus interacciones.
* Personalizer está diseñado para explorar de forma autónoma las preferencias del usuario todo el tiempo, lo que dará mejores resultados donde el contenido está cambiando rápidamente, como noticias, eventos en vivo, contenido de la comunidad en vivo, contenido con actualizaciones diarias o contenido estacional.

Un uso común es tomar la salida de un motor de recomendación (por ejemplo, los 20 mejores productos para un determinado cliente) y utilizarlo como las acciones de entrada para Personalizer.

## <a name="adding-content-safeguards-to-your-application"></a>Agregar a la aplicación medidas de seguridad para contenido

Si la aplicación permite grandes variaciones del contenido que se muestra a los usuarios, y algunos de estos contenidos pueden no ser seguros o apropiados para algunos usuarios, debes planear con antelación para asegurarte de poner en práctica las medidas de seguridad correctas a para evitar que los usuarios vean contenido inaceptable. El mejor patrón para implementar medidas de seguridad es el siguiente:
    * Obtén la lista de acciones para clasificar.
    * Filtra las que no sean viables para el público.
    * Clasifica solo estas acciones viables.
    * Muestra al usuario la acción con clasificación superior.

En algunas arquitecturas, la secuencia anterior puede ser difícil de implementar. En ese caso, existe un enfoque alternativo a la implementación de medidas de seguridad después de la clasificación, pero debe establecer salvedades para que las acciones que excluidas por la medida de seguridad no se usen para entrenar el modelo de Personalizer.

* Obtén la lista de acciones para clasificar, con aprendizaje desactivado.
* Clasifica las acciones.
* Comprueba si la acción superior es viable.
    * Si la acción superior es viable, activa el aprendizaje para esta clasificación y, luego, muéstrala al usuario.
    * Si la acción superior no es viable, no actives el aprendizaje para esta clasificación y decide mediante tu propia lógica o enfoques alternativos qué mostrar al usuario. Incluso si usas la segunda mejor opción clasificada, no actives el aprendizaje para esta clasificación.


## <a name="next-steps"></a>Pasos siguientes

[Ética y uso responsable](ethics-responsible-use.md).
