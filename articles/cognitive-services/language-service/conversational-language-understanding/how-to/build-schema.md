---
title: Cómo compilar un esquema de proyecto de Conversational Language Understanding
titleSuffix: Azure Cognitive Services
description: Use este artículo para empezar a compilar un esquema de proyecto Conversational Language Understanding
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-clu, ignite-fall-2021
ms.openlocfilehash: 0a140457e67c24cc363d7c660f641a2c98a25939
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093760"
---
# <a name="how-to-build-your-project-schema"></a>Cómo compilar el esquema del proyecto
 
En Conversational Language Understanding, el *esquema* se define como la combinación de intenciones y entidades dentro del proyecto. El diseño del esquema es una parte fundamental del éxito del proyecto. Al crear un esquema, quiere pensar en qué intenciones y entidades se deben incluir en el proyecto.

## <a name="guidelines-and-recommendations"></a>Guías y recomendaciones

Tenga en cuenta las siguientes directrices al seleccionar intenciones para el proyecto:

  1. Crear intenciones distintas y separables. Una intención se describe mejor como la acción general deseada de un usuario. Piense en el proyecto que compila e identifique todas las distintas acciones que los usuarios pueden realizar al interactuar con el proyecto. El envío, la llamada y la cancelación son todas las acciones que se representan mejor como intenciones diferentes. "Cancelar una orden" y "cancelar una cita" son muy similares, con la distinción de lo *que* están cancelando. Estas dos acciones deben representarse con la misma intención.
  
  2. Cree entidades para extraer fragmentos de información pertinentes en el texto. Las entidades deben usarse para capturar la información pertinente necesaria para satisfacer la acción del usuario. Por ejemplo, la *orden* o la *cita* podrían ser diferentes cosas que un usuario está intentando cancelar y debe crear una entidad para capturar ese fragmento de información.

Puede *"enviar"* un *mensaje*, *"enviar"* un *correo electrónico* o *"enviar"* un paquete. La creación de una intención para capturar cada uno de esos requisitos no modificará la escala con el tiempo y debe usar entidades para identificar lo *que* el usuario estaba enviando. La combinación de intenciones y entidades debe determinar el flujo de conversación. 

Por ejemplo, considere una empresa en la que los desarrolladores de bots han identificado las tres acciones más comunes que los usuarios llevan a cabo al usar un calendario: 

* Configuración de reuniones nuevas 
* Respuesta a solicitudes de reunión 
* Cancelación de reuniones 

Pueden crear una intención para representar cada una de estas acciones. También pueden incluir entidades para ayudar a completar estas acciones, como:

* Reuniones con los asistentes
* Fecha
* Duración de la reunión


Solo puede crear intenciones para los proyectos de **flujo de trabajo de orquestación**. El proyecto de flujo de trabajo de orquestación está diseñado para enrutar a otros servicios de destino que se pueden habilitar con la extracción de entidades para completar el flujo de conversación. Puede agregar nuevas intenciones que estén conectadas a otros servicios _o_ crear intenciones que no estén conectadas a ningún servicio (una intención desconectada). 

Al agregar una intención desconectada, permite al orquestador enrutar a esa intención y volver sin llamar a un servicio adicional. Debe proporcionar ejemplos de entrenamiento para intenciones desconectadas. Solo puede conectarse a proyectos que pertenecen al mismo recurso de Azure. 

Siguiendo con el ejemplo anterior, los desarrolladores de un bot podrían darse cuenta de que, para cada aptitud de su bot (que incluye acciones de calendario, acciones de correo electrónico y preguntas más frecuentes de la empresa), necesitan una intención que se conecte a cada una de esas aptitudes.  

## <a name="build-project-schema-for-conversation-projects"></a>Compilación del esquema de proyecto para proyectos de conversación

Para compilar un esquema de proyecto para proyectos de conversación:

1. Seleccione la pestaña **intenciones** o **entidades** en la página Esquema de compilación y seleccione **Agregar**. Se le pedirá un nombre antes de completar la creación de la intención o entidad. 

2. Al hacer clic en una intención, se le llevará a la página [expresiones](tag-utterances.md) de etiqueta, donde puede agregar ejemplos de intenciones y etiquetar entidades de ejemplos.

    :::image type="content" source="../media/build-schema-page.png" alt-text="Una captura de pantalla que muestra la página de creación de esquemas para proyectos de conversación en Language Studio." lightbox="../media/build-schema-page.png":::
    
3. Después de crear una entidad, se le enrutará a la página de detalles de la entidad. Cada componente se define mediante varios componentes. Puede etiquetar ejemplos en la página expresiones de etiqueta para entrenar un componente aprendido, agregar una lista de valores con los que coincidir en el componente de lista o agregar un conjunto de componentes creados previamente desde la biblioteca disponible. Obtenga más información sobre los componentes [aquí](../concepts/entity-components.md)

    :::image type="content" source="../media/entity-details.png" alt-text="Una captura de pantalla que muestra la página de detalles de la entidad para los proyectos de conversación en Language Studio." lightbox="../media/entity-details.png":::

## <a name="build-project-schema-for-orchestration-workflow-projects"></a>Compilación del esquema de proyecto para proyectos de flujo de trabajo de orquestación

Para compilar un esquema de proyecto para proyectos de flujo de trabajo de orquestación: 

1. Seleccione **Agregar** en la página **Esquema de compilación**. Se le pedirá un nombre y que defina una conexión para la intención, si la hay. Si desea conectar una intención, debe proporcionar:
    1. **Tipo de servicio:** LUIS, Respuesta a preguntas personalizadas (QnA) o Conversational Language Understanding.
    2. **Nombre del proyecto**: el proyecto al que desea que se conecte la intención.
    3. **Versión para expresiones** (solo para LUIS): qué versión de LUIS debe usarse para entrenar el modelo de clasificación del orquestador.

    :::image type="content" source="../media/orchestration-intent.png" alt-text="Una captura de pantalla que muestra el modal de creación de intenciones para proyectos de orquestación en Language Studio." lightbox="../media/orchestration-intent.png":::

> [!IMPORTANT]
> * No se pueden seleccionar intenciones conectadas porque no se pueden agregar ejemplos de entrenamiento a una intención conectada, ya que ya usa los datos del proyecto de destino para entrenar su clasificación de intenciones.
> * Solo podrá conectarse a los servicios de destino que son propiedad del mismo recurso.

## <a name="next-steps"></a>Pasos siguientes
* [Expresiones de etiqueta](tag-utterances.md)
