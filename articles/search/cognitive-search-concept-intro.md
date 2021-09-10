---
title: Conceptos de enriquecimiento con IA
titleSuffix: Azure Cognitive Search
description: La extracción de contenido, el procesamiento de lenguaje natural (NLP) y el procesamiento de imágenes se usan para crear contenido en el que se puedan realizar búsquedas en índices de Azure Cognitive Search mediante aptitudes cognitivas predefinidas y algoritmos de inteligencia artificial personalizados.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/10/2021
ms.custom: references_regions
ms.openlocfilehash: 73a1ae771ba3e3e5876b2efd3914b9e38ab0e426
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123222355"
---
# <a name="ai-enrichment-in-azure-cognitive-search"></a>Enriquecimiento con IA en Azure Cognitive Search

En Azure Cognitive Search, el enriquecimiento con IA hace referencia a aptitudes cognitivas integradas y otras personalizadas que agregan transformación y generación de contenido durante la indexación. Los enriquecimientos crean información nueva donde no existía anteriormente: extracción de información de imágenes, detección de la opinión, frases clave y entidades del texto, por nombrar algunas. Los enriquecimientos también agregan estructura a texto no diferenciado. Todos estos procesos producen documentos que mejoran la eficacia de la búsqueda de texto completo. En muchos casos, los documentos enriquecidos son útiles para escenarios que no son de búsqueda, como, por ejemplo, para la minería de datos de conocimiento.

El enriquecimiento se define mediante un [conjunto de aptitudes](cognitive-search-working-with-skillsets.md) asociado a un [indexador](search-indexer-overview.md). El indexador extraerá y configurará el contenido, mientras que el conjunto de aptitudes identifica, analiza y crea información y estructuras a partir de imágenes, blobs y otros orígenes de datos no estructurados. La salida de una canalización de enriquecimiento es un [índice de búsqueda](search-what-is-an-index.md) o un [almacén de conocimiento](knowledge-store-concept-intro.md).

Un conjunto de aptitudes puede contener aptitudes integradas de Cognitive Search o insertar el procesamiento externo que se proporciona en una [*aptitud personalizada*](cognitive-search-create-custom-skill-example.md). Algunos ejemplos de aptitudes personalizadas pueden ser un módulo de entidad personalizado o un clasificador de documentos que tiene como destino un dominio específico, como finanzas, publicaciones científicas o medicina.

Las aptitudes integradas se encuadran en estas categorías:

+ Las aptitudes de **procesamiento de lenguaje natural** incluyen [reconocimiento de entidades](cognitive-search-skill-entity-recognition-v3.md), [detección de idioma](cognitive-search-skill-language-detection.md), [extracción de frases clave](cognitive-search-skill-keyphrases.md), manipulación de texto, [detección de opiniones (incluida minería de opiniones)](cognitive-search-skill-sentiment-v3.md) y [detección de información de identificación personal](cognitive-search-skill-pii-detection.md). Con estas aptitudes, un texto no estructurado se asigna como campos en los que se pueden realizar búsquedas y aplicar filtros en un índice.

+ Las aptitudes de **procesamiento de imágenes** incluyen [reconocimiento óptico de caracteres (OCR)](cognitive-search-skill-ocr.md) e identificación de [características visuales](cognitive-search-skill-image-analysis.md), como detección facial, interpretación de imágenes, reconocimiento de imágenes (personas famosas y puntos de referencia) o atributos como orientación de la imagen. Estas aptitudes crean representaciones de texto del contenido de las imágenes, lo que permite la realización de búsquedas en dicho contenido mediante las funcionalidades de consulta de Azure Cognitive Search.

![Diagrama de una canalización de enriquecimiento](./media/cognitive-search-intro/cogsearch-architecture.png "introducción a la canalización de enriquecimiento")

Las aptitudes integradas de Azure Cognitive Search se basan en los modelos de aprendizaje automático previamente entrenados de Cognitive Services APIs: [Computer Vision](../cognitive-services/computer-vision/index.yml) y [Text Analytics](../cognitive-services/text-analytics/overview.md). Puede adjuntar un recurso de Cognitive Services si desea aprovechar estos recursos durante el procesamiento de contenido.

Tanto el lenguaje natural como el procesamiento de imágenes se aplican durante la fase de ingesta de datos, con resultados que forman parte de la composición de un documento en un índice en el que se pueden realizar búsquedas en Azure Cognitive Search. Los datos se obtienen como un conjunto de datos de Azure y luego se insertan a través de una canalización de indexación mediante las [aptitudes integradas](cognitive-search-predefined-skills.md) que necesite.  

## <a name="feature-availability"></a>Disponibilidad de características

El enriquecimiento con IA está disponible en las regiones en las que también lo está Azure Cognitive Services. Puede comprobar la disponibilidad actual del enriquecimiento con IA en la página [Productos de Azure disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=search). El enriquecimiento con IA está disponible en todas las regiones admitidas excepto:

+ Sudeste de Australia
+ Norte de China 2
+ Este de Noruega
+ Centro-oeste de Alemania

Si el servicio de búsqueda se encuentra en una de estas regiones, no puede crear y usar el conjunto de aptitudes, pero el resto de la funcionalidad del servicio de búsqueda está disponible y es totalmente compatible.

## <a name="when-to-use-ai-enrichment"></a>Cuándo usar el enriquecimiento con IA

Considere la posibilidad de usar el enriquecimiento si su contenido sin procesar es texto no estructurado, contenido de imagen o contenido que requiere detección y traducción de idiomas. La aplicación de inteligencia artificial a través de las aptitudes cognitivos integradas puede desbloquear este contenido, lo que aumenta su valor y utilidad en las aplicaciones de ciencia de datos y búsqueda. 

Además, puede considerar la posibilidad de agregar una aptitud personalizada si tiene código de código abierto, de terceros o de otro fabricante que le gustaría integrar en la canalización. Los modelos de clasificación que identifican las características destacadas de varios tipos de documento se encuentran en esta categoría, pero se puede usar cualquier paquete que agregue valor al contenido.

### <a name="use-cases-for-built-in-skills"></a>Casos de uso de las aptitudes integradas

Los [conjuntos de aptitudes](cognitive-search-defining-skillset.md) que se ensamblan mediante aptitudes integradas son apropiados para los siguientes escenarios de aplicación:

+ El [reconocimiento óptico de caracteres (OCR)](cognitive-search-skill-ocr.md) que reconoce el tipo de letra y el texto manuscrito en documentos digitalizados (JPEG) es quizás la aptitud más usada. Si se adjunta la aptitud de OCR, identificará, extraerá e ingerirá texto de los archivos JPEG.

+ La [traducción de texto](cognitive-search-skill-text-translation.md) de contenido multilingüe es otra aptitud de uso frecuente. Aunque está integrada en la traducción de texto, la [detección de idioma](cognitive-search-skill-language-detection.md) también se puede ejecutar de manera independiente si solo quiere los códigos de idioma del contenido de su corpus.

+ PDF con combinación de imagen y texto. El texto de los archivos PDF se puede extraer durante la indexación sin el uso de los pasos del enriquecimiento, pero la adición del procesamiento de imágenes y del lenguaje natural a menudo puede producir un mejor resultado que el de una indexación estándar.

+ Documentos no estructurados o semiestructurados cuyo contenido tenga un significado o contexto inherentes que está oculto en el documento mayor. 

  Los blobs a menudo contienen un cuerpo de contenido grande que se empaqueta en un "campo" único. Al adjuntar aptitudes de procesamiento de imágenes y de lenguaje natural a un indizador, puede crear información que exista en el contenido sin procesar, pero que no se expone como campos distintos. Algunas aptitudes cognitivas integradas y listas para usar que pueden ser de ayuda: [extracción de frases clave](cognitive-search-skill-keyphrases.md) y [reconocimiento de entidades](cognitive-search-skill-entity-recognition-v3.md) (personas, organizaciones y ubicaciones, por citas varias).

  Además, las aptitudes integradas también se pueden usar para reestructurar el contenido mediante operaciones de división de texto, combinación y modelado de forma.

### <a name="use-cases-for-custom-skills"></a>Casos de uso de las aptitudes personalizadas

Las aptitudes personalizadas pueden admitir escenarios más complejos, como el reconocimiento de formularios o la detección de entidades personalizadas mediante un modelo que se proporciona y se encapsula en la [interfaz web de aptitudes personalizadas](cognitive-search-custom-skill-interface.md). Entre los ejemplos de aptitudes personalizadas se incluyen [Form Recognizer](../cognitive-services/form-recognizer/overview.md), la integración de [Bing Entity Search API](./cognitive-search-create-custom-skill-example.md) y el [reconocimiento de entidades personalizadas](https://github.com/Microsoft/SkillsExtractorCognitiveSearch).

## <a name="enrichment-steps"></a>Pasos de enriquecimiento <a name="enrichment-steps"></a>

Una canalización de enriquecimiento consta de [*indexadores*](search-indexer-overview.md) que tienen [*conjuntos de aptitudes*](cognitive-search-working-with-skillsets.md). Un conjunto de aptitudes define los pasos de enriquecimiento, mientras que el indexador impulsa el conjunto de aptitudes. Al configurar un indizador, puede incluir propiedades, como asignaciones de campos de salida que envían contenido enriquecido a un [índice de búsqueda](search-what-is-an-index.md) o proyecciones que definen estructuras de datos en un [almacén de conocimiento](knowledge-store-concept-intro.md).

Una vez que se indexa, puede acceder al contenido mediante solicitudes de búsqueda a través de todos los [tipos de consulta compatibles con Azure Cognitive Search](search-query-overview.md).

### <a name="step-1-connection-and-document-cracking-phase"></a>Paso 1: Fase de conexión y descifrado de documentos

Los indexadores se conectan a orígenes externos mediante la información que proporciona en un origen de datos de indexador. Cuando el indexador se conecta al recurso, ["descifrará" los documentos](search-indexer-overview.md#document-cracking) para extraer texto e imágenes. El contenido de imagen se puede redirigir a aptitudes que llevan a cabo procesamiento de imágenes, mientras que el contenido de texto se pone en cola del procesamiento de texto. 

![Fase de descifrado de documentos](./media/cognitive-search-intro/document-cracking-phase-blowup.png "descifrado de documentos")

En este paso se ensambla todo el contenido inicial o sin procesar que se someterá al enriquecimiento con IA. Para cada documento, se crea un árbol de enriquecimiento. Aunque, inicialmente, el árbol es solo una representación de nodo raíz, crecerá y ganará estructura durante la ejecución del conjunto de aptitudes.

### <a name="step-2-skillset-enrichment-phase"></a>Paso 2: Fase de enriquecimiento del conjunto de aptitudes

Un conjunto de aptitudes define las operaciones atómicas que se realizan en cada documento. Por ejemplo, en el caso de texto e imágenes que se hayan extraído de un PDF, un conjunto de aptitudes puede aplicar el reconocimiento de entidades, la detección de idioma o la extracción de frases clave, para generar en su índice campos nuevos que no están disponibles de manera nativa en el origen. 

![Fase de enriquecimiento](./media/cognitive-search-intro/enrichment-phase-blowup.png "fase de enriquecimiento")

 Un conjunto de aptitudes puede ser mínimo o sumamente complejo y determina no solo el tipo de procesamiento, sino también el orden de las operaciones. La mayoría de los conjuntos contienen entre tres y cinco aptitudes.

Un conjunto de aptitudes, más las [asignaciones de campos de salida](cognitive-search-output-field-mapping.md) que se definen como parte de un indexador, especifica por completo la canalización de enriquecimiento. Para más información sobre cómo unir todos estos elementos, consulte el artículo sobre cómo [definir un conjunto de aptitudes](cognitive-search-defining-skillset.md).

Internamente, la canalización genera una colección de documentos enriquecidos. Puede decidir qué elementos de los documentos enriquecidos se deben asignar a los campos del índice de búsqueda que se pueden indexar. Por ejemplo, si aplicara las aptitudes de reconocimiento de entidades y de extracción de frases clave, esos campos nuevos se volverían parte del documento enriquecido y se podrían asignar a los campos del índice. Consulte las [anotaciones](cognitive-search-concept-annotations-syntax.md) para más información sobre las formaciones de entrada/salida.

### <a name="step-3-indexing"></a>Paso 3: Indexación

La indexación es el proceso en el que el contenido sin procesar y enriquecido se ingiere como campos en un índice de búsqueda, y como [proyecciones](knowledge-store-projection-overview.md) si también se crea un almacén de conocimiento. El mismo contenido enriquecido puede aparecer en ambos, usando asignaciones de campos implícitas o explícitas para enviarlo a los campos correctos.

El contenido enriquecido se genera durante la ejecución del conjunto de aptitudes y es temporal a menos que lo guarde. Para que el contenido enriquecido aparezca en un índice de búsqueda, el indexador debe tener información de asignación para poder enviar el contenido a un campo de un índice de búsqueda. Las [asignaciones de campos de salida](cognitive-search-output-field-mapping.md) establecen estas asociaciones.

## <a name="saving-enriched-output"></a>Guardado de la salida enriquecida

En Azure Cognitive Search, un indizador guarda el resultado que crea.

Una de las salidas que siempre crea un indexador es un [índice que permite búsquedas](search-what-is-an-index.md). La especificación de un índice es un requisito del indexador. Cuando se adjunta un conjunto de aptitudes, la salida del conjunto, además de los campos que se importan directamente desde el origen, se usan para rellenar el índice. Normalmente, las salidas de aptitudes específicas, como frases clave o puntuaciones de opinión, se ingieren en el índice en campos creados para ese fin.

Un [almacén de conocimiento](knowledge-store-concept-intro.md) es una salida opcional, que se usa para aplicaciones de nivel inferior, como la minería de conocimiento. Los almacenes de conocimiento se definen dentro de conjuntos de aptitudes. Su definición determina si los documentos enriquecidos se proyectan como tablas u objetos (archivos o blobs). Las proyecciones tabulares son adecuadas para análisis interactivos en herramientas como Power BI, mientras que los archivos y los blobs se usan normalmente en ciencia de datos o en procesos similares.

Por último, un indexador puede [almacenar en caché documentos enriquecidos](cognitive-search-incremental-indexing-conceptual.md) en Azure Blob Storage para su posible reutilización en ejecuciones posteriores de conjuntos de aptitudes. Los enriquecimientos almacenados en caché puede consumirlos el mismo conjunto de aptitudes que se vuelve a ejecutar en una fecha posterior. El almacenamiento en caché es útil si el conjunto de aptitudes incluye análisis de imágenes u OCR, y usted desea ahorrarse el tiempo y los gastos derivados de tener que procesar de nuevo los archivos de imagen.

Los índices y los almacenes de conocimiento son totalmente independientes entre sí. Aunque debe adjuntar un índice para satisfacer los requisitos del indexador, si su único objetivo es un almacén de conocimiento, puede omitir el índice una vez rellenado. No obstante, procure no eliminarlo. Si desea volver a ejecutar el indexador y el conjunto de aptitudes, necesitará el índice para que se ejecute el indexador.

## <a name="using-enriched-content"></a>Uso de contenido enriquecido

Una vez finalizado el procesamiento, obtendrá un [índice de búsqueda](search-what-is-an-index.md) que consta de documentos enriquecidos y texto totalmente apto para búsquedas en Azure Cognitive Search. Para acceder al contenido enriquecido que la canalización genera, los desarrolladores y usuarios [consultan el índice](search-query-overview.md). El índice es similar a cualquier otro que pueda crear para Azure Cognitive Search: puede complementar análisis de texto con analizadores personalizados, invocar consultas de búsqueda aproximada, agregar filtros o experimentar con perfiles de puntuación para ajustar la pertinencia de la búsqueda.

También puede tener un [almacén de conocimiento](knowledge-store-concept-intro.md). El almacén de conocimiento contiene datos que se pueden consumir en escenarios de minería de conocimiento, como análisis o aprendizaje automático. Puede usar [Explorador de Storage](knowledge-store-view-storage-explorer.md), [Power BI](knowledge-store-connect-power-bi.md) o cualquier aplicación que se conecte a Azure Storage.

## <a name="checklist-a-typical-workflow"></a>Lista de comprobación: Un flujo de trabajo típico

1. Al iniciar un proyecto, resulta útil trabajar con un subconjunto de datos. El diseño del indexador y del conjunto de aptitudes es un proceso iterativo, e iterará más rápidamente si trabaja con un pequeño conjunto de datos representativo.

1. Cree un [origen de datos](/rest/api/searchservice/create-data-source) que especifique una conexión con sus datos.

1. Cree un [conjunto de aptitudes](/rest/api/searchservice/create-skillset) para agregar enriquecimiento.

1. Cree un [esquema de índice](/rest/api/searchservice/create-index) que defina un índice de búsqueda.

1. Cree un [indexador](/rest/api/searchservice/create-indexer) para reunir todos los componentes anteriores. Al crear o ejecutar el indexador, se recuperan los datos, se ejecuta el conjunto de aptitudes y se carga el índice.

1. Ejecute consultas para evaluar los resultados y modifique el código para actualizar la configuración del indexador, el esquema o los conjuntos de aptitudes.

Para iterar con los pasos anteriores, [restablezca el indexador](search-howto-reindex.md) antes de volver a generar la canalización, o bien elimine y vuelva a crear los objetos en cada ejecución (opción recomendada si usa el nivel Gratis). También debe [habilitar el almacenamiento en caché de enriquecimiento](cognitive-search-incremental-indexing-conceptual.md) para reutilizar los enriquecimientos existentes siempre que sea posible.

## <a name="next-steps"></a>Pasos siguientes

+ [Inicio rápido: Tutorial de la prueba del enriquecimiento con IA en un portal](cognitive-search-quickstart-blob.md)
+ [Tutorial: Información sobre las API de REST de enriquecimiento con IA](cognitive-search-tutorial-blob.md)
+ [Conceptos de conjunto de aptitudes](cognitive-search-working-with-skillsets.md)
+ [Conceptos del almacén de conocimiento](knowledge-store-concept-intro.md)
+ [Creación de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Crear un almacén de conocimientos](knowledge-store-create-rest.md)
