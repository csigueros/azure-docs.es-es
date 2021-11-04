---
title: ¿Qué es la clasificación personalizada (versión preliminar) en Azure Cognitive Services for Language?
titleSuffix: Azure Cognitive Services
description: Aprenda a usar la clasificación de texto personalizada.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-classification, ignite-fall-2021
ms.openlocfilehash: c31a348376f41bf61a4632d991fc02f08b360be7
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131450255"
---
# <a name="what-is-custom-text-classification-preview"></a>¿Qué es la clasificación de texto personalizada (versión preliminar)?

La clasificación de texto personalizada es una de las características que ofrece [Azure Cognitive Service for Language](../overview.md). Se trata de un servicio de API basado en la nube que aplica inteligencia de aprendizaje automático para permitirle crear modelos personalizados para tareas de clasificación de texto. 

La clasificación de texto personalizada se ofrece como parte de las características personalizadas de Azure Cognitive for Language. Esta característica permite a sus usuarios crear modelos de IA personalizados para clasificar texto en categorías personalizadas predefinidas por el usuario. Mediante la creación de un proyecto de clasificación personalizada, los desarrolladores pueden etiquetar datos de forma iterativa, entrenar, evaluar y mejorar el rendimiento del modelo antes de que esté disponible para su consumo. La calidad de los datos etiquetados afecta considerablemente al rendimiento del modelo. Para simplificar la creación y personalización del modelo, el servicio ofrece un portal web personalizado al que se puede acceder a través de [Language Studio](https://aka.ms/languageStudio). Si sigue los pasos que se indican en este [inicio rápido](quickstart.md), le resultará fácil empezar a usar el servicio. 

La clasificación de texto personalizada admite dos tipos de proyectos: 

* **Clasificación mediante etiqueta única**: puede asignar una sola clase a cada archivo del conjunto de datos. Por ejemplo, el guión de una película solo se puede clasificar como "Acción" o "Suspense". 
* **Clasificación mediante varias etiquetas**: puede asignar varias clases a cada archivo del conjunto de datos. Por ejemplo, el guión de una película se puede clasificar como "Acción" o como "Acción y suspense".

Esta documentación contiene los siguientes tipos de artículos:

* Los [inicios rápidos](quickstart.md) son instrucciones de inicio que le guiarán a la hora de hacer solicitudes al servicio.
* Los [conceptos](concepts/evaluation.md) proporcionan explicaciones sobre la funcionalidad y las características del servicio.
* Las [guías de procedimientos](how-to/tag-data.md) contienen instrucciones para usar el servicio de una manera más específica o personalizada.

## <a name="example-usage-scenarios"></a>Escenarios de uso de ejemplo

### <a name="automatic-emailsticket-triage"></a>Evaluación automática de prioridades de correos electrónicos o vales

Los centros de soporte técnico de todos los tipos reciben entre miles y cientos de miles de correos electrónicos o vales que contienen texto no estructurado, de forma libre y datos adjuntos. La revisión, confirmación y enrutamiento oportunos a expertos en la materia dentro de los equipos internos es fundamental. Sin embargo, la evaluación de prioridades del correo electrónico a esta escala que implique el examen de personas y su enrutamiento a los departamentos adecuados exige invertir tiempo y valiosos recursos. La clasificación personalizada se puede usar para analizar la evaluación de prioridades del texto entrante y clasificar el contenido que se va a enrutar automáticamente al departamento correspondiente para realizar las acciones necesarias.

### <a name="knowledge-mining-to-enhanceenrich-semantic-search"></a>Minería del conocimiento para mejorar o enriquecer la búsqueda semántica

La búsqueda es la base de todas aquellas aplicaciones que muestran contenido de texto a los usuarios, con escenarios comunes como: la búsqueda en catálogos o documentos, la búsqueda en productos comerciales o la minería del conocimiento para la ciencia de datos.Muchas empresas de varios sectores están investigando la posibilidad de crear una mejor experiencia de búsqueda sobre contenido privado y heterogéneo que incluya documentos tanto estructurados como no estructurados. Como parte de su canalización, los desarrolladores pueden usar la clasificación personalizada para clasificar texto en clases que sean relevantes para su sector. Las clases predichas se podrían usar para enriquecer la indexación del archivo, con el fin de personalizar más la experiencia de búsqueda. 

## <a name="application-development-lifecycle"></a>Ciclo de vida del desarrollo de aplicaciones

La creación de una aplicación de clasificación personalizada suele implicar varios pasos. 

:::image type="content" source="media/development-lifecycle.png" alt-text="El ciclo de vida del desarrollo" lightbox="media/development-lifecycle.png":::

Siga estos pasos para sacar el máximo partido del modelo:

1. **Definir el esquema**: conozca los datos e identifique las clases entre las que quiere marcar diferencias, evite la ambigüedad.

2. **Etiquetar los datos**: la calidad del etiquetado de datos es un factor clave para determinar el rendimiento del modelo. Etiquete todos los archivos que desea incluir en el entrenamiento. Los archivos que pertenecen a la misma clase siempre deben tener la misma clase, si tiene un archivo que puede estar en dos clases, use proyectos de **clasificación de varias clases**. Evite la ambigüedad de clases, asegúrese de que las clases se pueden separar claramente unas de otras, especialmente en el caso de proyectos de clasificación de clase única.

3. **Entrenar el modelo**: el modelo empieza a aprender de los datos etiquetados.

4. **Ver los detalles de evaluación del modelo**: vea los detalles de evaluación del modelo para determinar su rendimiento cuando se introduce en datos nuevos.

5. **Mejorar el modelo**: trabaje en la mejora del rendimiento del modelo. Para ello, debe examinar las predicciones incorrectas del modelo y la distribución de los datos.

6. **Implementar el modelo**: la implementación de un modelo hace que esté disponible para su uso a través de [Analyze API](https://aka.ms/ct-runtime-swagger).

7. **Clasificar el texto**: use el modelado personalizado para las tareas de clasificación de texto.

## <a name="next-steps"></a>Pasos siguientes

* Tras leer el [artículo de inicio rápido](quickstart.md) podrá empezar a usar la clasificación de texto personalizada.  

* Cuando avance por el ciclo de vida de desarrollo de aplicaciones, consulte el [glosario](glossary.md) para obtener más información sobre los términos que se usan en la documentación para esta característica. 

* No olvide ver los [límites del servicio](service-limits.md) para obtener información como la [disponibilidad regional](service-limits.md#regional-availability).
