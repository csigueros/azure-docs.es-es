---
title: Preparación de datos y definición de un esquema
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre la selección de datos, la preparación y la creación de un esquema para proyectos de clasificación personalizados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: 243139219259c3b4be95010df988cf30256ca204
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132484110"
---
# <a name="how-to-prepare-data-and-define-a-schema"></a>Preparación de datos y definición de un esquema

Para crear un modelo de clasificación personalizado, necesitará datos de calidad para entrenarlo. En este artículo se explica cómo debe abordar la selección y preparación de los datos, junto con la definición de un esquema. Un esquema define las clases en las que necesita que el modelo clasifique el texto en tiempo de ejecución y es el primer paso para desarrollar una [aplicación de clasificación personalizada](../overview.md#project-development-lifecycle).


## <a name="data-selection"></a>Selección de datos

La calidad de los datos con los que entrena el modelo afecta enormemente al rendimiento del modelo.

* Use datos de la vida real que reflejen el espacio de problemas del dominio para entrenar de forma eficaz el modelo. Puede usar datos sintéticos para acelerar el proceso de entrenamiento del modelo inicial, pero probablemente serán diferentes de los datos reales y harán que el modelo sea menos eficaz cuando se use.

* Equilibra la distribución de datos tanto como sea posible sin desviarse de la distribución en la vida real.

* Use diversos datos siempre que sea posible para evitar el sobreajuste del modelo. Una menor diversidad en los datos de entrenamiento puede dar lugar a correlaciones falsas del aprendizaje del modelo que pueden no existir en los datos de la vida real. 
 
* Evite los archivos duplicados en los datos. Los datos duplicados tienen un efecto negativo en el proceso de entrenamiento, las métricas del modelo y el rendimiento del modelo. 

* Considere de dónde proceden los datos. Si va a recopilar datos de una persona, departamento o parte de su escenario, es probable que falte la diversidad que puede ser importante para que su modelo conozca. 

> [!NOTE]
> Si los archivos están en varios idiomas, seleccione la opción de **varios idiomas** durante la [creación del proyecto](../quickstart.md) y establezca la opción de **idioma** en el idioma de la mayoría de los archivos.

## <a name="data-preparation"></a>Preparación de datos

Como requisito previo para crear un proyecto de clasificación personalizada de texto, los datos de entrenamiento se deben cargar en un contenedor de blobs de la cuenta de almacenamiento. Puede crear y cargar archivos de entrenamiento desde Azure directamente o mediante la herramienta Explorador de Azure Storage. La herramienta Explorador de Azure Storage permite cargar más datos rápidamente.  

* [Creación y carga de archivos desde Azure](/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container)
* [Creación y carga de archivos mediante el Explorador de Azure Storage](/azure/vs-azure-tools-storage-explorer-blobs)

Solo puede usar archivos `.txt`. para texto personalizado. Si los datos están en otro formato, puede usar el comando de análisis [CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) para cambiar el formato del archivo.

 Puede cargar un conjunto de datos anotado o cargar uno sin anotar y [etiquetar los datos](../how-to/tag-data.md) en Language Studio. 
 
## <a name="schema-design"></a>Diseño del esquema

El esquema define las clases en las que necesita que el modelo clasifique el texto en tiempo de ejecución.

* **Revisión e identificación**: revise los archivos del conjunto de datos para familiarizarse con su estructura y contenido y, a continuación, identifique cómo desea clasificar los datos. 

    Por ejemplo, si va a clasificar incidencias de soporte técnico, es posible que necesite las siguientes clases:*problema de inicio de sesión*, *incidencia de hardware*, *incidencia de conectividad* y *solicitud de nuevo equipo*.

* **Evite la ambigüedad en las clases**: la ambigüedad surge cuando las clases especificadas comparten un significado similar entre sí. Cuanto más ambiguo sea el esquema, más datos etiquetados necesitará para entrenar el modelo.  

    Por ejemplo, si va a clasificar recetas de alimentos, pueden ser similares a una extensión. Para diferenciar entre *la receta de ingredientes* y *la receta de receta principal*, es posible que tenga que etiquetar más ejemplos para ayudar al modelo a distinguir entre las dos clases. Evitar la ambigüedad ahorra tiempo y produce mejores resultados. 

* **Datos fuera del ámbito**: al usar el modelo en producción, considere la posibilidad de agregar una clase *fuera del ámbito* al esquema si espera archivos que no pertenecen a ninguna de las clases. A continuación, agregue algunos archivos al conjunto de datos para etiquetarse como *fuera del ámbito*. El modelo puede aprender a reconocer archivos irrelevantes y predecir sus etiquetas en consecuencia.

## <a name="next-steps"></a>Pasos siguientes

Si aún no lo ha hecho, cree un proyecto de clasificación personalizado. Si es la primera vez que usa la clasificación personalizada, considere la posibilidad de seguir el [inicio rápido](../quickstart.md) para crear un proyecto como ejemplo. También puede ver los [requisitos del proyecto](../how-to/create-project.md) para obtener más detalles sobre lo que necesita para crear un proyecto. 
