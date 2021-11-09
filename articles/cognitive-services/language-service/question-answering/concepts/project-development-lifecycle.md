---
title: 'Ciclo de vida del proyecto: respuesta a preguntas'
description: Respuesta a preguntas aprende mejor en un ciclo iterativo de cambios de modelo, ejemplos de expresiones, implementación y recopilación de datos de consultas de punto de conexión.
ms.service: cognitive-services
ms.subservice: language-service
author: mrbullwinkle
ms.author: mbullwin
ms.topic: conceptual
ms.date: 11/02/2021
ms.openlocfilehash: 193d01adac85681c39554ec1f6cab24dce9082f0
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478474"
---
# <a name="question-answering-project-lifecycle"></a>Ciclo de vida del proyecto de respuesta a preguntas

Respuesta a preguntas aprende mejor en un ciclo iterativo de cambios de modelo, ejemplos de expresiones, implementación y recopilación de datos de consultas de punto de conexión.

## <a name="creating-a-project"></a>Creación de un proyecto

Los proyectos o knowledge bases de respuesta a preguntas proporcionan la mejor respuesta a una consulta de usuario en función del contenido del proyecto. La creación de un proyecto es una acción que se realiza una sola vez para configurar un repositorio de contenido de preguntas, respuestas y metadatos asociados. Se puede crear un proyecto mediante el rastreo de contenido ya existente, como los orígenes siguientes:

- Páginas de preguntas más frecuentes
- Manuales de productos
- Pares de preguntas y respuestas

Aprenda cómo [crear una base de conocimiento](../how-to/create-test-deploy.md).

## <a name="testing-and-updating-your-project"></a>Prueba y actualización del proyecto

El proyecto está listo para las pruebas una vez que se llena con el contenido, bien mediante edición o por medio de extracción automática. Las pruebas interactivas se pueden realizar en el portal de Language Studio, en el menú de respuesta a preguntas personalizado, mediante el panel **Probar**. Escriba consultas comunes de los usuarios. A continuación, compruebe que las respuestas se devuelvan con la respuesta correcta y una puntuación de confianza suficiente.

* **Para corregir las puntuaciones de confianza baja**: agregue preguntas alternativas.
* **Cuando una consulta devuelve incorrectamente la [respuesta predeterminada](../How-to/change-default-answer.md)**: agregue nuevas respuestas a la pregunta correcta.

Este bucle ajustado de prueba-actualización continúa hasta que esté satisfecho con los resultados.

## <a name="deploy-your-project"></a>Implementación del proyecto

Una vez que se ha terminado de probar el proyecto, se puede implementar en producción. La implementación inserta la versión más reciente del proyecto probado en un índice dedicado de Azure Cognitive Search que representa el proyecto o knowledge base **publicado**. También se crea un punto de conexión al que puede llamar en su aplicación o bot de chat.

Debido a la acción de implementación, los cambios adicionales realizados en la versión de prueba de knowledge base no afectan a la versión publicada. La versión publicada puede estar activa en una aplicación de producción.

Cada uno de estos proyectos o knowledge bases se puede probar por separado.

## <a name="monitor-usage"></a>Supervisión de uso

Para poder registrar los registros de chat del servicio y obtener análisis adicionales, debería habilitar [registros de diagnóstico de Azure Monitor](../how-to/analytics.md) después de crear el recurso de lenguaje.

En función de lo aprendido de los análisis, realice las actualizaciones adecuadas en el proyecto.

## <a name="version-control-for-data-in-your-knowledge-base"></a>Control de versiones para datos en su base de conocimiento

El control de versiones de los datos se proporciona por medio de las características de importación y exportación de la página del proyecto, en la sección de respuesta a preguntas del portal de Language Studio.

Puede realizar una copia de seguridad de un proyecto o knowledge base si lo exporta, ya sea en formato `.tsv` o `.xls`. Una vez exportado, incluya este archivo en la comprobación de control de código fuente normal.

Cuando necesite volver a una versión específica, debe importar ese archivo desde el sistema local. Una exportación solo se **debe** usar mediante la importación en la página del proyecto. No se puede usar como origen de datos de un archivo o URL de documento. De este modo, reemplazará las preguntas y respuestas que se encuentran actualmente en la base de conocimiento con el contenido del archivo importado.

## <a name="test-and-production-project"></a>Proyecto de prueba y producción

Un proyecto o knowledge base es el repositorio de conjuntos de preguntas y respuestas que se crea, se mantiene y se usa por medio de la respuesta a preguntas. Cada recurso de lenguaje puede contener varios proyectos o knowledge bases.

Un proyecto o knowledge base tiene dos estados: *prueba* y *publicado*.

### <a name="test-projectknowledge-base"></a>Proyecto o knowledge base de prueba

La *knowledge base de prueba* es la versión editada y guardada actualmente. La versión de prueba se ha probado para comprobar la exactitud y la integridad de las respuestas. Los cambios realizados en la base de conocimientos de prueba no afectan al usuario final de la aplicación o bot de chat. La base de conocimiento de prueba se conoce como `test` en la solicitud HTTP. La knowledge base `test` está disponible en el panel interactivo **Probar** de Language Studio.

### <a name="production-projectknowledge-base"></a>Proyecto o knowledge base de producción

La *base de conocimiento publicada* es la versión que se utiliza en la aplicación o en el bot de chat. Al publicar una knowledge base, el contenido de la versión de prueba se coloca en la versión publicada. La knowledge base publicada es la versión que utiliza la aplicación o el bot de chat. Asegúrese de que el contenido sea correcto y se haya probado. La base de conocimiento publicada se conoce como `prod` en la solicitud HTTP.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Sugerencias de aprendizaje activo](../tutorials/active-learning.md)