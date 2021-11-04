---
title: ¿Qué es el reconocimiento de entidades con nombre (NER) personalizado en Azure Cognitive Service for Language (versión preliminar)?
titleSuffix: Azure Cognitive Services
description: Aprenda a usar Reconocimiento de entidades con nombre personalizado.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-service
ms.topic: overview
ms.date: 11/02/2021
ms.author: aahi
ms.custom: language-service-custom-ner, ignite-fall-2021
ms.openlocfilehash: d5644ecbc6b76ab2fc1ec44239fc14c52ab5fa73
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131093283"
---
# <a name="what-is-custom-named-entity-recognition-ner-preview"></a>¿Qué es Reconocimiento de entidades con nombre (NER) personalizado (versión preliminar)?

Reconocimiento de entidades con nombre personalizado se ofrece como parte de las características personalizadas de [Azure Cognitive Service for Language](../overview.md) y se crea sobre la tecnología de [Microsoft Turing](https://msturing.org/about). Esta característica permite a sus usuarios crear modelos de inteligencia artificial personalizados para extraer entidades específicas del dominio de texto no estructurado, como contratos o documentos financieros. Mediante la creación de un proyecto de Reconocimiento de entidades con nombre personalizado, los desarrolladores pueden etiquetar datos de forma iterativa, entrenar, evaluar y mejorar el rendimiento del modelo antes de que esté disponible para su consumo. 

Para simplificar la creación y personalización del modelo, el servicio ofrece un portal web personalizado al que se puede acceder a través de [Language Studio](https://aka.ms/languageStudio). Si sigue los pasos que se indican en este [inicio rápido](quickstart.md), le resultará fácil empezar a usar el servicio. 

Esta documentación contiene los siguientes tipos de artículos:

* Los [inicios rápidos](quickstart.md) son instrucciones de inicio que le guiarán a la hora de hacer solicitudes al servicio.
* Los [conceptos](concepts/evaluation-metrics.md) proporcionan explicaciones sobre la funcionalidad y las características del servicio.
* Las [guías de procedimientos](how-to/tag-data.md) contienen instrucciones para usar el servicio de una manera más específica o personalizada.

## <a name="example-usage-scenarios"></a>Escenarios de uso de ejemplo

### <a name="information-extraction"></a>Extracción de información

Muchas organizaciones financieras y legales extraen y normalizan datos de miles de textos no estructurados complejos, como extractos bancarios, contratos legales o formularios bancarios a diario. En lugar de procesar manualmente estos formularios, Reconocimiento de entidades con nombre personalizado puede ayudar a automatizar este proceso.

### <a name="knowledge-mining-to-enhanceenrich-semantic-search"></a>Minería del conocimiento para mejorar o enriquecer la búsqueda semántica

La búsqueda es fundamental para cualquier aplicación que represente contenido de texto para los usuarios, con escenarios comunes como la búsqueda de catálogos o documentos, la búsqueda de productos comerciales o la minería de conocimientos para la ciencia de datos.Muchas empresas de varios sectores están investigando la posibilidad de crear una mejor experiencia de búsqueda sobre contenido privado y heterogéneo que incluya documentos tanto estructurados como no estructurados. Como parte de su canalización, los desarrolladores pueden usar Reconocimiento de entidades con nombre personalizado para extraer entidades del texto que son relevantes para su sector. Estas entidades se podrían usar para enriquecer la indexación del archivo, con el fin de personalizar más la experiencia de búsqueda. 

### <a name="audit-and-compliance"></a>Auditoría y cumplimiento

En lugar de usar manualmente archivos de texto largos para auditar y aplicar directivas organizativas, se puede usar Reconocimiento de entidades con nombre personalizado para ayudar a crear soluciones automatizadas y aplicar las reglas de negocio necesarias. 

## <a name="application-development-lifecycle"></a>Ciclo de vida del desarrollo de aplicaciones

El uso de Reconocimiento de entidades con nombre personalizado suele implicar varios pasos diferentes. 

:::image type="content" source="../custom-classification/media/development-lifecycle.png" alt-text="El ciclo de vida del desarrollo" lightbox="../custom-classification/media/development-lifecycle.png":::

1. **Definir el esquema**: conozca los datos e identifique las entidades que desea extraer. Evita la ambigüedad.

2. **Etiquetar los datos**: el etiquetado de datos es un factor clave para determinar el rendimiento del modelo. Agregue las etiquetas de forma precisa, coherente y completa.
    1. **Etiquetar con precisión**: etiquete cada entidad en su tipo correcto siempre. Incluya solo lo que quiera extraer y evite datos innecesarios en la etiqueta.
    2. **Etiquetar de forma coherente**: la misma entidad debe tener la misma etiqueta en todos los archivos.
    3. **Etiquetar por completo**: etiquete todas las instancias de la entidad en todos los archivos.

3. **Entrenar el modelo**: el modelo empieza a aprender de los datos etiquetados.

4. **Ver los detalles de evaluación del modelo**: una vez completado el entrenamiento, vea los detalles de evaluación del modelo y su rendimiento.

5. **Mejorar el modelo**: después de examinar los detalles de evaluación del modelo, puede continuar y aprender a mejorar el modelo.

6. **Implementar el modelo**: la implementación de un modelo es para que esté disponible para su uso.

7. **Extraer entidades**: use los modelos personalizados para las tareas de extracción de entidades.

## <a name="next-steps"></a>Pasos siguientes

* Tras leer el [artículo de inicio rápido](quickstart.md) podrá empezar a usar la clasificación de texto personalizada.  

* Cuando avance por el ciclo de vida de desarrollo de aplicaciones, consulte el [glosario](glossary.md) para obtener más información sobre los términos que se usan en la documentación para esta característica. 

* No olvide ver los [límites del servicio](service-limits.md) para obtener información como la [disponibilidad regional](service-limits.md#regional-availability).
