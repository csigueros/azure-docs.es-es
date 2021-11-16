---
title: 'Planeamiento de la aplicación: respuesta a preguntas'
description: Descubra cómo planear la aplicación de respuesta a preguntas. Conozca cómo funciona la respuesta a preguntas y cómo interactúa con otros servicios de Azure, así como algunos conceptos sobre las bases de conocimiento.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 0dbd8e670f652ff598c9b65c07687ab38a097050
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478142"
---
# <a name="plan-your-question-answering-app"></a>Planeamiento de la aplicación de respuesta de preguntas

Para planear la aplicación de respuesta a preguntas, debe comprender cómo funciona esta característica y cómo interactúa con otros servicios de Azure. También debe tener una idea sólida de los conceptos de las bases de conocimiento.

## <a name="azure-resources"></a>Recursos de Azure

Cada [recurso de Azure](azure-resources.md#resource-purposes) creado con la respuesta a preguntas tiene una finalidad concreta. Cada recurso tiene su propia finalidad, límites y [plan de tarifa](azure-resources.md#pricing-tier-considerations). Es importante comprender la función de estos recursos para que pueda usar ese conocimiento en el proceso de planeamiento.

| Recurso | Propósito |
|--|--|
| [Recurso de idioma](azure-resources.md) | Creación, telemetría y punto de conexión de consulta de predicciones|
| Recurso de [Cognitive Search](azure-resources.md#azure-cognitive-search-resource) | Almacenamiento y búsqueda de datos |

### <a name="resource-planning"></a>Planeamiento de recursos

El rendimiento de la respuesta a preguntas está actualmente limitado a 10 transacciones por segundo tanto para las API de administración como para las de predicción. Con el fin de obtener un rendimiento de diez transacciones por segundo con su servicio, se recomienda la SKU S1 (1 instancia) de Azure Cognitive Search.

### <a name="language-resource"></a>Recurso de idioma

Un único recurso de idioma que tenga habilitada la característica de respuesta a preguntas personalizada puede hospedar más de un proyecto o base de conocimiento. El número de proyectos o de bases de conocimiento está determinado por la cantidad de índices admitidos en el plan de tarifa de Cognitive Search. Obtenga más información sobre la [relación de los índices con las bases de conocimiento](azure-resources.md#index-usage).

### <a name="knowledge-base-size-and-throughput"></a>Tamaño y rendimiento de la base de conocimiento

Cuando compile una aplicación real, planee recursos suficientes para el tamaño de la base de conocimiento y las solicitudes de predicción de consultas que espere.

El tamaño de la base de conocimiento lo controlan los siguientes elementos:
* Los límites del plan de tarifa del [recurso de Cognitive Search](../../../../search/search-limits-quotas-capacity.md)
* [Límites de la respuesta a preguntas](./limits.md)

La solicitud de predicción de consultas de la base de conocimiento se controla mediante el plan de la aplicación web y la propia aplicación web. Consulte la [configuración recomendada](azure-resources.md#recommended-settings) para decidir el plan de tarifa.

### <a name="understand-the-impact-of-resource-selection"></a>Descripción del impacto de la selección de recursos

Para seleccionar los recursos de manera adecuada, la base de conocimiento debe responder a las predicciones de consultas correctamente.

Si la base de conocimiento no funciona de forma correcta, el problema suele deberse a una administración de recursos inadecuada.

En el caso de una selección de recursos incorrecta, es necesario investigar para determinar qué [recurso debe cambiar](azure-resources.md#pricing-tier-considerations).

## <a name="project"></a>Project

Un proyecto o una base de conocimiento están asociados directamente a su recurso de idioma. Contiene los pares de preguntas y respuestas (PyR) que se usan para responder a las solicitudes de predicción de consultas.

### <a name="language-considerations"></a>Consideraciones de idioma

Ahora puede tener proyectos en distintos idiomas dentro del mismo recurso de idioma en la que esté habilitada la característica de respuesta a preguntas. Al crear el primer proyecto, puede elegir si quiere usar el recurso para proyectos o bases de conocimiento en un único idioma que se aplique a todos los proyectos posteriores o si prefiere seleccionar el idioma cada vez que se cree un proyecto.

### <a name="ingest-data-sources"></a>Ingesta de orígenes de datos

La respuesta a preguntas también admite contenido sin estructurar. Puede cargar un archivo con contenido de este tipo.

Actualmente no se admiten direcciones URL para contenido sin estructurar.

El proceso de ingesta convierte los tipos de contenido admitidos a Markdown. La edición posterior de la *respuesta* se realiza con Markdown. Después de crear una base de conocimiento, puede editar pares de preguntas y respuestas en el portal de Language Studio mediante la creación de texto enriquecido.

### <a name="data-format-considerations"></a>Consideraciones sobre el formato de los datos

Dado que el formato final de un par de pregunta y respuesta es Markdown, es importante entender la compatibilidad con Markdown.

### <a name="bot-personality"></a>Personalidad de bot

Agregue una personalidad de bot al proyecto o la base de conocimiento con [charla](../how-to/chit-chat.md). Esta personalidad incluye respuestas en un determinado tono de conversación, como *profesional* y *amistoso*. La función de charla se proporciona como un conjunto conversacional, que puede agregar, editar y quitar como le convenga.

Se recomienda una personalidad de bot si el bot se conecta a la base de conocimiento. Puede usar la charla en la base de conocimiento incluso si también se conecta a otros servicios, pero debe revisar cómo interactúa el servicio de bot para saber si ese es el diseño de arquitectura correcto para su uso.

### <a name="conversation-flow-with-a-project"></a>Flujo de conversación con un proyecto

Normalmente, el flujo de conversación empieza con un saludo por parte de un usuario, como `Hi` o `Hello`. La base de conocimiento puede responder de manera general, como `Hi, how can I help you`, y mostrar una serie de mensajes de seguimiento para continuar con la conversación.

Debe diseñar el flujo de conversación con un bucle en mente para que el usuario sepa cómo usar el bot y que este no lo abandone durante la conversación. Los [mensajes de seguimiento](../tutorials/guided-conversations.md) proporcionan vinculación entre los pares de preguntas y respuestas, que hacen posible el flujo de conversación.

### <a name="authoring-with-collaborators"></a>Creación con colaboradores

Los colaboradores podrían ser otros desarrolladores que comparten la pila de desarrollo completa de la aplicación de base de conocimiento, o tal vez se limiten a crear la base de conocimiento.

La creación de la base de conocimiento admite varios permisos de acceso basado en roles que se apliquen en Azure Portal para limitar el ámbito de las capacidades de un colaborador.

## <a name="integration-with-client-applications"></a>Integración con aplicaciones cliente

La integración con aplicaciones cliente se realiza enviando una consulta al punto de conexión del tiempo de ejecución de predicción. Se envía una consulta al proyecto o la base de conocimiento concretos con una solicitud basada en REST o en el SDK al punto de conexión de la aplicación web de respuesta a preguntas.

Para autenticar correctamente una solicitud de cliente, la aplicación cliente debe enviar las credenciales correctas y el identificador de la base de conocimiento. Si usa Azure Bot Service, defina esta configuración como parte de la configuración del bot en Azure Portal.

### <a name="conversation-flow-in-a-client-application"></a>Flujo de conversación en una aplicación cliente

El flujo de conversación en una aplicación cliente, como un bot de Azure, puede requerir alguna funcionalidad antes y después de interactuar con la base de conocimiento.

¿La aplicación cliente admite el flujo de conversación, ya sea proporcionando medios alternativos para controlar los mensajes de seguimiento o incluyendo una charla? Si es así, diséñelos antes y asegúrese de que la consulta de la aplicación cliente la controle correctamente otro servicio o cuando se envíe a la base de conocimiento.

### <a name="active-learning-from-a-client-application"></a>Aprendizaje activo desde una aplicación cliente

La respuesta a preguntas usa el _aprendizaje activo_ para mejorar la base de conocimiento mediante la sugerencia de preguntas alternativas a una respuesta. La aplicación cliente es responsable de una parte de este [aprendizaje activo](../tutorials/active-learning.md). A través de los mensajes de conversación, la aplicación cliente puede determinar que la base de conocimiento ha devuelto una respuesta que no es útil para el usuario y puede determinar una respuesta mejor. La aplicación cliente debe enviar esta información de nuevo a la base de conocimiento para mejorar la calidad de la predicción.

### <a name="providing-a-default-answer"></a>Aportación de una respuesta predeterminada

Si la base de conocimiento no encuentra una respuesta, devuelve la _respuesta predeterminada_. Esta respuesta se puede configurar en la página **Configuración**.

Esta respuesta predeterminada es diferente de la que ofrece el bot de Azure. La respuesta predeterminada para el bot de Azure se define en Azure Portal como parte de la configuración. Se devuelve cuando no se cumple el umbral de puntuación.

## <a name="prediction"></a>Predicción

La predicción es la respuesta de la base de conocimiento e incluye más información que simplemente la respuesta. Para obtener una respuesta de predicción de consulta, use la API de respuesta a preguntas.

### <a name="prediction-score-fluctuations"></a>Fluctuaciones de la puntuación de predicción

Una puntuación puede cambiar en función de varios factores:

* Número de respuestas solicitadas en la respuesta con la propiedad `top`
* La variedad de preguntas alternativas disponibles
* El filtrado de metadatos
* Consulta enviada al proyecto o la base de conocimiento `test` o `production`.

### <a name="analytics-with-azure-monitor"></a>Análisis con Azure Monitor

En la respuesta a preguntas, la telemetría se ofrece a través del [servicio Azure Monitor](../../../../azure-monitor/index.yml). Use nuestras [principales consultas](../how-to/analytics.md) para entender las métricas.

## <a name="development-lifecycle"></a>Ciclo de vida del desarrollo

El ciclo de vida del desarrollo de una base de conocimiento es constante, ya que consta de la edición, la prueba y la publicación de la base de conocimiento.

### <a name="knowledge-base-development-of-question-answer-pairs"></a>Desarrollo de la base de conocimiento de pares de preguntas y respuestas

Los pares de preguntas y respuestas deben diseñarse y desarrollarse en función del uso de la aplicación cliente.

Cada par puede contener:
* Metadatos: se pueden filtrar al realizar consultas para permitirle etiquetar los pares de preguntas y respuestas con información adicional sobre el origen, el contenido, el formato y la finalidad de los datos.
* Mensajes de seguimiento: ayudan a determinar una ruta en la base de conocimiento para que el usuario llegue a la respuesta correcta.
* Preguntas alternativas: importantes para permitir que la búsqueda haga coincidir la respuesta con otras formas de la pregunta. Las [sugerencias de aprendizaje activo](../tutorials/active-learning.md) se convierten en preguntas alternativas.

### <a name="devops-development"></a>Desarrollo de DevOps

El desarrollo de una knowledge base para insertarla en una canalización de DevOps requiere que la knowledge base esté aislada durante las pruebas por lotes.

Una base de conocimiento comparte el índice de Cognitive Search con todas las demás bases de conocimiento del recurso de idioma. Aunque la base de conocimiento está aislada por partición, si se comparte el índice, se puede producir una diferencia en la puntuación cuando se compara con la base de conocimiento publicada.

Para tener la _misma puntuación_ en las bases de conocimiento `test` y `production`, aísle un recurso de idioma en una única base de conocimiento. En esta arquitectura, el recurso solo necesita persistir tanto tiempo como la prueba por lotes aislada.

## <a name="next-steps"></a>Pasos siguientes

* [Recursos de Azure](./azure-resources.md)
