---
title: 'Diseño de aplicaciones iterativo: LUIS'
titleSuffix: Azure Cognitive Services
description: LUIS aprende mejor en un ciclo iterativo de cambios en el modelo, ejemplos de expresiones, publicación y recopilación de datos de las consultas de punto de conexión.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/06/2020
ms.openlocfilehash: 92991ef4b9889b0be2e887884ed46086b87ff9f3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128588891"
---
# <a name="iterative-app-design-for-luis"></a>Diseño iterativo de aplicaciones para LUIS

Una aplicación de Language Understanding (LUIS) aprende y funciona de forma más eficaz con la iteración. Este es un ciclo de iteración típico:

* Creación de una nueva versión
* Edición del esquema de aplicación de LUIS Esto incluye:
    * Intenciones con expresiones de ejemplo
    * Entidades
    * Características
* Entrenamiento, prueba y publicación
    * Prueba en el punto de conexión de predicción para el aprendizaje activo
* Recopilación de datos de consultas del punto de conexión

![Ciclo de creación](./media/luis-concept-app-iteration/iteration.png)

## <a name="building-a-luis-schema"></a>Crear un modelo de LUIS

El esquema de la aplicación define qué pide el usuario (la _intención_ _)_ y qué partes de la intención aportan datos (llamados _entidades_) que se usan para ayudar a determinar la respuesta.

El esquema de la aplicación tiene que ser específico de los dominios de aplicación para poder determinar palabras y frases que sean apropiadas, así como el orden habitual de las palabras.

Los expresiones de ejemplo representan entradas de usuario, como texto o voz reconocidos, que la aplicación espera en tiempo de ejecución.

El esquema requiere intenciones y _debe tener_ entidades.

### <a name="example-schema-of-intents"></a>Esquema de ejemplo de intenciones

El esquema más común es un esquema de intenciones organizado con intenciones. Este tipo de esquema usa LUIS para determinar la intención de un usuario.

Este tipo de esquema de intención puede tener entidades si ayuda a LUIS a determinar la intención del usuario. Por ejemplo, una entidad de envío (como una _característica de aprendizaje automático_ para una intención) ayuda a LUIS a determinar una intención de envío.

### <a name="example-schema-of-entities"></a>Esquema de ejemplo de entidades

Un esquema de entidad se centra en las entidades, que son los datos que se extraen de las expresiones del usuario. Por ejemplo, si un usuario quiere decir "Me gustaría pedir tres pizzas". Hay dos entidades que se pueden extraer: _tres_ y _pizzas_. Se usan para ayudar a satisfacer la intención, que era realizar un pedido.

Para un esquema de entidad, la intención de la expresión es menos importante para la aplicación cliente.

### <a name="example-of-a-mixed-schema"></a>Ejemplo de un esquema mixto

El esquema más eficaz y maduro es un esquema de intenciones con una gama completa de entidades y características. Este esquema puede comenzar como un esquema de intenciones o entidades y crecer para incluir los conceptos de ambos, ya que la aplicación cliente necesita esos fragmentos de información.

## <a name="add-example-utterances-to-intents"></a>Incorporación de expresiones de ejemplo a las intenciones

LUIS necesita algunas expresiones de ejemplo en cada **intención**. Las expresiones de ejemplo necesitan una variación amplia de palabras y del orden de estas para poder determinar qué intención tiene la expresión.

> [!CAUTION]
> No agregue expresiones de ejemplo de forma masiva. Comience con entre quince y treinta ejemplos específicos y variados.

Cada expresión de ejemplo necesita tener todos los **datos necesarios que se desean extraer** etiquetados y diseñados como **entidades**.

|Elemento clave|Propósito|
|--|--|
|Intención|**Clasifique** las expresiones de usuario en una sola intención o acción. Algunos ejemplos son `BookFlight` y `GetWeather`.|
|Entidad|**Extraiga** los datos de la expresión necesaria para completar la intención. Entre los ejemplos se incluyen la fecha y la hora de viaje, así como la ubicación.|

Se puede diseñar una aplicación de LUIS para que omita las expresiones que no sean pertinentes para el dominio de la aplicación. Para ello, debe asignar la expresión a la intención **None**.

## <a name="test-and-train-your-app"></a>Prueba y entrenamiento de la aplicación

Después de tener entre quince y treinta expresiones de ejemplo diferentes en cada intención, con las entidades necesarias etiquetadas, tendrá que probar y [entrenar](luis-how-to-train.md) la aplicación de LUIS.

## <a name="publish-to-a-prediction-endpoint"></a>Publicación en un punto de conexión de la predicción

La aplicación de LUIS debe publicarse para que esté disponible en la lista de [regiones del punto de conexión de la predicción](luis-reference-regions.md).

## <a name="test-your-published-app"></a>Prueba de la aplicación publicada

Puede probar la aplicación de LUIS publicada desde el punto de conexión de predicción HTTPS. La realización de pruebas desde el punto de conexión de predicción permite a LUIS elegir cualquier expresión con una confianza baja para la [revisión](luis-how-to-review-endpoint-utterances.md).

## <a name="create-a-new-version-for-each-cycle"></a>Creación de una nueva versión para cada ciclo

Cada versión es una instantánea en el tiempo de la aplicación de LUIS. Antes de realizar cambios en la aplicación, cree una nueva versión. Es más fácil volver a una versión anterior y, luego, intentar quitar las intenciones y expresiones de un estado anterior.

El identificador de la versión está formado por caracteres, dígitos o ".", y no puede tener más de 10 caracteres.

La versión inicial (0.1) es la versión activa predeterminada.

### <a name="begin-by-cloning-an-existing-version"></a>Comienzo con la clonación de una versión existente

Clone una versión existente para usarla como punto inicial de la nueva versión. Después de clonar una versión, la versión nueva se convertirá en la versión **activa**.

### <a name="publishing-slots"></a>Ranuras de publicación

Puede publicar en los espacios de ensayo o de producción. Cada espacio puede tener otra versión o la misma. Esto es útil para comprobar los cambios antes de publicar para producción, que está disponible para bots u otras aplicaciones de llamadas de LUIS.

Las versiones entrenadas no están disponibles automáticamente en el [punto de conexión](luis-glossary.md#endpoint) de la aplicación de LUIS. Debe [publicar](luis-how-to-publish-app.md) o volver a publicar una versión para que esté disponible en el punto de conexión de la aplicación de LUIS. Puede publicar en la fase de **ensayo** o de **producción**, lo que le ofrece dos versiones de la aplicación disponibles en el punto de conexión. Si tienen que estar disponibles más versiones de la aplicación en un punto de conexión, debe exportar la versión y volver a importarla a una aplicación nueva. La nueva aplicación tiene un identificador de aplicación diferente.

### <a name="import-a-version"></a>Importación de una versión

Una versión se puede **importar** como una nueva:
* Aplicación, con un id. de la aplicación nuevo
* Versión de una aplicación existente

Esa versión se convertirá en la versión activa y usará el identificador de versión en la propiedad `versionId` del archivo de la aplicación.

### <a name="export-a-version"></a>Exportación de una versión

Una versión se puede **exportar** desde el portal de LUIS en el nivel de aplicación o de versión:

* Nivel de aplicación: seleccione la aplicación en la página **Mis aplicaciones** y, después, seleccione **Exportar**.
* Nivel de versión: seleccione el vínculo de la aplicación en la página **Mis aplicaciones**, después **Administrar** y luego **Versiones**.

La única diferencia es que, en el nivel de aplicación, la versión exportada es la versión activa, mientras que en el nivel de versión puede elegir en la página **[Configuración](luis-how-to-manage-versions.md)** cualquier versión para exportarla.

El archivo exportado **no** contiene:

* información de aprendizaje automático, puesto que la aplicación se vuelve a entrenar después de importarse.
* Información del colaborador.

Para realizar una copia de seguridad del esquema de la aplicación de LUIS, exporte una versión desde el [portal de LUIS](https://www.luis.ai/applications).

## <a name="manage-contributor-changes-with-versions-and-contributors"></a>Administración de los cambios del colaborador con versiones y colaboradores

LUIS usa el concepto de colaboradores en la aplicación al proporcionar permisos de nivel de recurso de Azure. Combine este concepto con el control de versiones para proporcionar colaboración dirigida.

Use las técnicas siguientes para administrar los cambios del colaborador en la aplicación.

### <a name="manage-multiple-versions-inside-the-same-app"></a>Administrar varias versiones dentro de la misma aplicación

Comience con [clonación](luis-how-to-manage-versions.md#clone-a-version), desde una versión base, para cada autor.

Cada autor efectúa cambios en su propia versión de la aplicación. Cuando esté satisfecho con el modelo, debe exportar las versiones nuevas a archivos JSON.

Se pueden comparar los cambios de las aplicaciones exportadas y los archivos `.json` o `.lu`. Combine los archivos para crear un único archivo de la nueva versión. Cambie la propiedad `versionId` para indicar la nueva versión combinada. Importe esa versión en la aplicación original.

Este método permite tener una versión activa, una versión de ensayo y una versión publicada. Puede comparar los resultados de la versión activa con una versión publicada (fase o producción) en el [panel de pruebas interactivas](luis-interactive-test.md).

### <a name="manage-multiple-versions-as-apps"></a>Administrar varias versiones como aplicaciones

[Exporte](luis-how-to-manage-versions.md#export-version) la versión base. Cada autor importa la versión. La persona que importa la aplicación es el propietario de la versión. Cuando acabe de modificar la aplicación, deberá exportar la versión.

Las aplicaciones exportadas son archivos con formato JSON, que se pueden comparar con la exportación base para efectuar cambios. Combine los archivos para crear un único archivo JSON de la nueva versión. Cambie la propiedad **versionId** del archivo JSON para indicar la nueva versión combinada. Importe esa versión en la aplicación original.

Obtenga más información sobre la creación de contribuciones de los [colaboradores](luis-how-to-collaborate.md).

## <a name="review-endpoint-utterances-to-begin-the-new-iterative-cycle"></a>Revisión de las expresiones de punto de conexión para comenzar el nuevo ciclo iterativo

Cuando haya terminado con un ciclo de iteración, puede repetir el proceso. Empiece [revisando las expresiones del punto de conexión de predicción](luis-how-to-review-endpoint-utterances.md) que LUIS haya marcado con una confianza baja. Compruebe estas expresiones tanto para la intención predicha correcta como para la entidad correcta y completa extraída. Después de revisar y aceptar los cambios, la lista de revisión debería estar vacía.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre conceptos relativos a la [colaboración](luis-how-to-azure-subscription.md).
