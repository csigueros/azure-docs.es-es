---
title: Preparación de datos y diseño de un esquema para NER personalizado
titleSuffix: Azure Cognitive Services
description: Obtenga información sobre cómo seleccionar y preparar los datos para crear correctamente proyectos NER personalizados.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: 04a01833498154a1446e7f00aafb505b37e9e335
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705025"
---
# <a name="how-to-prepare-data-and-define-a-schema-for-custom-ner"></a>Preparación de datos y definición de un esquema para NER personalizado

Para crear un modelo NER personalizado, necesitará datos de calidad para entrenarlo. En este artículo se explica cómo debe abordar la selección y preparación de los datos, junto con la definición de un esquema. El esquema define los tipos o categorías de entidad que necesita que el modelo extraiga del texto en runtime y es el primer paso para [desarrollar un modelo de extracción de entidades](../overview.md#application-development-lifecycle).

## <a name="schema-design"></a>Diseño de esquema

El esquema define los tipos o categorías de entidad que necesita que el modelo extraiga del texto en runtime.

* Revise los archivos del conjunto de datos para familiarizarse con su formato y estructura.

* Identifique las [entidades](../glossary.md#entity) que desea extraer de los datos.

    Por ejemplo, si va a extraer entidades de correos electrónicos de soporte técnico, es posible que tenga que extraer "Nombre del cliente", "Nombre del producto", "Problema del cliente", "Fecha de solicitud" e "Información de contacto".

* Evite la ambigüedad de los tipos de entidad.

    **La ambigüedad** se produce cuando los tipos seleccionados son similares entre sí. Cuanto más ambiguo sea el esquema, más datos etiquetados entrenará el modelo.

    Por ejemplo, si va a extraer datos de un contrato legal, para extraer "Nombre de la primera entidad" y "Nombre de la segunda entidad", deberá agregar más ejemplos para superar la ambigüedad, ya que los nombres de ambas partes tienen un aspecto similar. Evitar la ambigüedad ahorra tiempo, esfuerzo y produce mejores resultados.

* Evite entidades complejas. Las entidades complejas pueden ser difíciles de seleccionar con precisión del texto; considere la posibilidad de dividirla en varias entidades.

    Por ejemplo, el modelo tendría dificultades para extraer "Dirección" si no se dividiera en entidades más pequeñas. Hay tantas variaciones en la forma en que aparecen las direcciones, que se tardaría un gran número de entidades etiquetadas en enseñar al modelo a extraer una dirección, en su conjunto, sin desglosarla. Sin embargo, si reemplaza "Dirección" con "Nombre de la calle", "Apartado postal", "Ciudad", "Estado" y "Código postal", el modelo requerirá menos etiquetas por entidad.

## <a name="data-selection"></a>Selección de datos

La calidad de los datos con los que entrena el modelo afecta enormemente al rendimiento del modelo.

* Use datos de la vida real que reflejen el espacio de problemas del dominio para entrenar de forma eficaz el modelo. Puede usar datos sintéticos para acelerar el proceso de entrenamiento del modelo inicial, pero probablemente serán diferentes de los datos reales y harán que el modelo sea menos eficaz cuando se use.

* Equilibra la distribución de datos tanto como sea posible sin desviarse de la distribución en la vida real. Por ejemplo, si está entrenando el modelo para extraer entidades de documentos legales que pueden llegar en muchos formatos e idiomas diferentes, debe proporcionar ejemplos que ejemplifiquen la diversidad como se esperaría ver en la vida real.

* Use datos diversos siempre que sea posible para evitar el sobreajuste del modelo. Una menor diversidad en los datos de entrenamiento puede dar lugar a correlaciones falsas del aprendizaje del modelo que pueden no existir en los datos de la vida real. 
 
* Evite los archivos duplicados en los datos. Los datos duplicados tienen un efecto negativo en el proceso de entrenamiento, las métricas del modelo y el rendimiento del modelo. 

* Considere de dónde proceden los datos. Si va a recopilar datos de una persona, departamento o parte de su escenario, es probable que falte la diversidad que puede ser importante para que su modelo conozca. 

> [!NOTE]
> Si los archivos están en varios idiomas, seleccione la opción de **varios idiomas** durante la [creación del proyecto](../quickstart.md) y establezca la opción de **idioma** en el idioma de la mayoría de los archivos.

## <a name="data-preparation"></a>Preparación de datos

Como requisito previo para crear un proyecto, los datos de entrenamiento deben cargarse en un contenedor de blobs de la cuenta de almacenamiento. Puede crear y cargar archivos de entrenamiento desde Azure directamente o mediante la herramienta Explorador de Azure Storage. La herramienta Explorador de Azure Storage permite cargar más datos rápidamente.  

* [Creación y carga de archivos desde Azure](../../../../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
* [Creación y carga de archivos mediante el Explorador de Azure Storage](../../../../vs-azure-tools-storage-explorer-blobs.md)

Solo puede usar archivos `.txt`. Si los datos están en otro formato, puede usar el [comando de análisis CLUtils](https://github.com/microsoft/CognitiveServicesLanguageUtilities/blob/main/CustomTextAnalytics.CLUtils/Solution/CogSLanguageUtilities.ViewLayer.CliCommands/Commands/ParseCommand/README.md) para cambiar el formato del archivo.

 Puede cargar un conjunto de datos anotado o cargar uno sin anotar y [etiquetar los datos](../how-to/tag-data.md) en Language Studio. 
 
## <a name="next-steps"></a>Pasos siguientes

Si aún no lo ha hecho, cree un proyecto NER personalizado. Si es la primera vez que usa NER personalizado, considere la posibilidad de seguir el [inicio rápido](../quickstart.md) para crear un proyecto de ejemplo. También puede ver el [tutorial](../how-to/create-project.md) para obtener más detalles sobre lo que necesita para crear un proyecto.