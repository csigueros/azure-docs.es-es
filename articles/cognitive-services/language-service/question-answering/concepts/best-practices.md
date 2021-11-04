---
title: 'Procedimientos recomendados: respuesta a preguntas'
description: Use estos procedimientos recomendados para mejorar el proyecto y ofrecer mejores resultados a los usuarios finales del bot de chat o de la aplicación.
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: conceptual
ms.date: 11/02/2021
ms.custom: language-service-question-answering, ignite-fall-2021
ms.openlocfilehash: 552e3d014ffecf5f43977420c9aa8c6bb1b59080
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093340"
---
# <a name="question-answering-best-practices"></a>Procedimientos recomendados de respuesta a preguntas

Siga estos procedimientos recomendados para mejorar la base de conocimiento y ofrecer mejores resultados a los usuarios finales del bot de chat o de la aplicación cliente.

## <a name="extraction"></a>Extracción

La respuesta a preguntas mejora continuamente los algoritmos que extraen pares de preguntas y respuestas del contenido y amplía la lista de formatos HTML y de archivos que admite. En general, las páginas de preguntas más frecuentes deben ser independientes y no combinarse con otra información. Los manuales de productos deben tener encabezados claros y, preferiblemente, una página de índice.

## <a name="creating-good-questions-and-answers"></a>Creación de buenas preguntas y respuestas

### <a name="good-questions"></a>Buenas preguntas

Las mejores preguntas son simples. Identifique la palabra o frase clave de cada pregunta y formule una pregunta sencilla sobre ella.

Agregue tantas preguntas alternativas como necesite, pero mantenga las variantes simples. Agregar más palabras o expresiones que no forman parte del objetivo principal de la pregunta no ayuda a que los algoritmos de respuesta a preguntas encuentren una coincidencia.

### <a name="add-relevant-alternative-questions"></a>Agregar preguntas alternativas pertinentes

El usuario puede escribir preguntas con un estilo de conversación de texto, `How do I add a toner cartridge to my printer?`, o bien con una búsqueda por palabra clave, como `toner cartridge`. El proyecto debe tener los dos estilos de preguntas para devolver correctamente la mejor respuesta. Si no está seguro de qué palabras clave escribe un cliente, use los datos de [Azure Monitor](../how-to/analytics.md) para analizar las consultas.

### <a name="good-answers"></a>Buenas respuestas

Las mejores respuestas son simples, pero no demasiado. No use respuestas como `yes` y `no`. Si la respuesta debe remitir a otros orígenes o incluir una experiencia enriquecida con vínculos y contenido multimedia, use el etiquetado de metadatos para distinguir las respuestas y, a continuación, envíe la consulta con las etiquetas de metadatos de la propiedad `strictFilters` para obtener la versión correcta de la respuesta.

|Respuesta|Avisos de seguimiento|
|--|--|
|Apague el dispositivo Surface Laptop con el botón de encendido del teclado.|* Combinaciones de teclas para suspender, apagar y reiniciar.<br>* Cómo arrancar en frío un dispositivo Surface Laptop.<br>* Cómo cambiar el BIOS de un dispositivo Surface Laptop.<br>* Diferencias entre suspender, apagar y reiniciar.|
|El servicio de atención al cliente está disponible por vía telefónica, Skype y mensaje de texto 24 horas al día.|* Información de contacto del equipo de ventas.<br> * Ubicación de las oficinas y tiendas, así como los horarios para visitas en persona.<br> * Accesorios para dispositivos Surface Laptop.|

## <a name="chit-chat"></a>Charla

Agregue una charla a su bot para que sea más conversacional y atractivo fácilmente. Puede agregar fácilmente orígenes de datos de charla de personalidades predefinidas al crear el proyecto y cambiarlos en cualquier momento. Obtenga información sobre cómo [agregar una charla a la base de conocimiento](../How-To/chit-chat.md).

La charla es compatible con [muchos idiomas](../how-to/chit-chat.md#language-support).

### <a name="choosing-a-personality"></a>Selección de una personalidad

La charla es compatible con varias personalidades predefinidas:

|Personalidad |Archivo de conjunto de datos de respuesta a preguntas |
|---------|-----|
|Profesional |[qna_chitchat_professional.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_professional.tsv) |
|Descriptiva |[qna_chitchat_friendly.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_friendly.tsv) |
|Ingenioso |[qna_chitchat_witty.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_witty.tsv) |
|Cariñoso |[qna_chitchat_caring.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_caring.tsv) |
|Entusiasta |[qna_chitchat_enthusiastic.tsv](https://qnamakerstore.blob.core.windows.net/qnamakerdata/editorial/qna_chitchat_enthusiastic.tsv) |

El rango de respuestas va de formal a informal e irreverente. Debe seleccionar la personalidad que sea más acorde con el tono que quiera para el bot. Puede ver los [conjuntos de datos](https://github.com/Microsoft/BotBuilder-PersonalityChat/tree/master/CSharp/Datasets), elegir uno que sirva como base para el bot y, a continuación, personalizar las respuestas.

### <a name="edit-bot-specific-questions"></a>Edición de preguntas específicas de bot

Hay algunas preguntas específicas de bot que forman parte del conjunto de datos de charla y que se han rellenado con respuestas genéricas. Cambie estas respuestas para reflejar mejor los detalles del bot.

Se recomienda hacer más específicos los siguientes pares de respuestas de preguntas de charla:

* ¿Quién es usted?
* ¿Qué puede hacer?
* ¿Cuántos años tiene?
* ¿Quién le ha creado?
* Hola

### <a name="adding-custom-chit-chat-with-a-metadata-tag"></a>Adición de una charla personalizado con una etiqueta de metadatos

Si agrega pares de respuestas y preguntas de charla propios, asegúrese de agregar metadatos para que se devuelvan estas respuestas. El par nombre-valor de metadatos es `editorial:chitchat`.

## <a name="searching-for-answers"></a>Búsqueda de respuestas

La API REST de respuesta a preguntas usa tanto las preguntas como la respuesta para buscar las mejores respuestas a la consulta de un usuario.

### <a name="searching-questions-only-when-answer-is-not-relevant"></a>Búsqueda de preguntas solo cuando la respuesta no es pertinente

Use [`RankerType=QuestionOnly`](#choosing-ranker-type) si no quiere buscar respuestas.

Un ejemplo es una base de conocimiento que es un catálogo con acrónimos como preguntas y su forma completa como respuesta. El valor de la respuesta no ayudará a buscar la respuesta adecuada.

## <a name="rankingscoring"></a>Clasificación y puntuación

Asegúrese de aprovechar al máximo las características de clasificación admitidas. Eso aumentará las probabilidades de que una determinada consulta de usuario se responda con una respuesta correcta.

### <a name="choosing-a-threshold"></a>Selección de un umbral

La [puntuación de confianza](confidence-score.md) predeterminada que se usa como umbral es 0, pero puede [cambiar el umbral](confidence-score.md#set-threshold) del proyecto en función de las necesidades. Como cada proyecto es diferente, conviene probar y elegir el umbral que mejor se adapte al proyecto.

### <a name="choosing-ranker-type"></a>Selección del tipo de clasificador

De manera predeterminada, la respuesta a preguntas busca entre las preguntas y respuestas. Si solo quiere buscar en las preguntas, para generar una respuesta, utilice el objeto `RankerType=QuestionOnly` del cuerpo de POST de la solicitud de la API REST.

### <a name="add-alternate-questions"></a>Agregar preguntas alternativas

Alterne las preguntas para aumentar las probabilidades de encontrar una coincidencia con una consulta de usuario. Las preguntas alternativas son útiles cuando hay varias maneras de plantear la misma pregunta. Esto puede incluir cambios en la estructura de la frase y el estilo de redacción.

|Consulta original|Consultas alternativas|Change|
|--|--|--|
|¿Hay estacionamiento disponible?|¿Dispone de estacionamiento?|estructura de la frase|
 |Hola|Buenas<br>¿Cómo estamos?|estilo de redacción o jerga|

### <a name="use-metadata-tags-to-filter-questions-and-answers"></a>Uso de etiquetas de metadatos para el filtrado de preguntas y respuestas

Los metadatos agregan la posibilidad de que una aplicación cliente pueda saber que no debe tomar todas las respuestas, sino restringir los resultados de una consulta de usuario en función de las etiquetas de metadatos. La respuesta del proyecto o base de conocimiento puede diferir según la etiqueta de metadatos, aunque la consulta sea la misma. Por ejemplo, *¿Dónde está ubicado el estacionamiento?* puede tener otra respuesta si la ubicación del restaurante es diferente; es decir, los metadatos son *ubicación: Seattle* frente a *ubicación: Redmond*.

### <a name="use-synonyms"></a>Usar sinónimos

Aunque hay cierta compatibilidad con los sinónimos en inglés, use [alteraciones de palabras](../tutorials/adding-synonyms.md) que no distingan mayúsculas de minúsculas para agregar sinónimos a las palabras clave que tengan una forma diferente.

|Palabra original|Sinónimos|
|--|--|
|comprar|compra<br>banca online<br>banca electrónica|

---

### <a name="use-distinct-words-to-differentiate-questions"></a>Usar diferentes palabras para diferenciar preguntas

El algoritmo de clasificación, que busca la correspondencia entre la consulta de un usuario y una pregunta del proyecto o base de conocimiento, funciona mejor si cada pregunta aborda una necesidad distinta. La repetición del mismo conjunto de palabras en diferentes preguntas reduce la probabilidad de que se seleccione la respuesta correcta para una consulta de usuario determinado con esas palabras.

Por ejemplo, puede tener dos pares de preguntas y respuestas independientes con las siguientes preguntas:

|Preguntas|
|--|
|dónde está *ubicado* el estacionamiento|
|dónde está *ubicado* el cajero automático|

Como estas dos preguntas se expresan con palabras muy similares, esta similitud podría provocar que las puntuaciones sean muy parecidas para muchas de las consultas de usuario que están expresadas como *"dónde está ubicado `<x>`"* . En su lugar, intente diferenciarlas claramente con consultas como *"dónde está el estacionamiento"* y *"dónde está el cajero automático"* , y evite palabras como "ubicado", que pueden estar en muchas preguntas del proyecto.

## <a name="collaborate"></a>Colaborar

La respuesta a preguntas permite a los usuarios colaborar en un proyecto o una base de conocimiento. Los usuarios necesitan acceso al grupo de recursos de Azure a fin de acceder al proyecto o la base de conocimiento. Algunas organizaciones quizás quieran externalizar la edición y el mantenimiento de la base de conocimiento, sin dejar de proteger el acceso a sus recursos de Azure. Para crear este modelo de editor-aprobador, se configuran dos recursos de idioma idénticos con proyectos de respuesta a preguntas idénticos en suscripciones diferentes y se selecciona uno para el ciclo de pruebas de edición. Una vez que finalizan las pruebas, el contenido del proyecto se exporta y transfiere con un proceso de [importación y exportación](../how-to/migrate-knowledge-base.md) al recurso de idioma del aprobador que, finalmente, implementará el proyecto y actualizará el punto de conexión.

## <a name="active-learning"></a>Aprendizaje activo

[Aprendizaje activo](../tutorials/active-learning.md) ofrece el mejor resultado al sugerir preguntas alternativas cuando tiene una amplia variedad de calidad y cantidad de consultas basadas en el usuario. Es importante permitir que las consultas de usuario de las aplicaciones cliente participen en el bucle de comentarios de aprendizaje activo sin censura. Una vez que se sugieren preguntas en el portal de Language Studio, puede revisar y aceptar o rechazar esas sugerencias.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Editar una base de conocimiento](../How-to/manage-knowledge-base.md)
