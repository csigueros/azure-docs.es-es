---
title: Uso de la IA para enriquecer el contenido de los blobs
titleSuffix: Azure Cognitive Search
description: Obtenga información sobre las funcionalidades de lenguaje natural y análisis de imágenes en Azure Cognitive Search, y sobre cómo se aplican esos procesos al contenido almacenado en blobs de Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 083a568f6fad664c59073e2f6858900302e9d971
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729759"
---
# <a name="use-ai-to-process-and-analyze-blob-content-in-azure-cognitive-search"></a>Uso de la IA para procesar y analizar el contenido de los blobs en Azure Cognitive Search

El contenido de Azure Blob Storage que se compone de imágenes o texto largo no diferenciado puede someterse a análisis de aprendizaje profundo para mostrar y extraer información valiosa útil para las aplicaciones de nivel inferior. Mediante el [enriquecimiento con IA](cognitive-search-concept-intro.md), puede hacer lo siguiente:

+ Extracción de texto de imágenes mediante reconocimiento óptico de caracteres (OCR)
+ Generación de una descripción de la escena o etiquetas de una foto
+ Detección de idioma y traducción de texto en diferentes idiomas
+ Deducción de la estructura a través del reconocimiento de entidades mediante la búsqueda de referencias a personas, fechas, lugares u organizaciones

Aunque es posible que necesite solo una de estas funcionalidades de inteligencia artificial, es habitual combinar varias de ellas en la misma canalización (por ejemplo, extraer texto de una imagen digitalizada y buscar después todas las fechas y los lugares a los que se hace referencia en él). También es común incluir el procesamiento del aprendizaje automático o la inteligencia artificial personalizada en forma de paquetes externos de vanguardia o modelos internos adaptados a sus datos y sus requisitos.

Aunque puede aplicar el enriquecimiento con IA a cualquier origen de datos admitido por un indizador de búsqueda, los blobs son las estructuras que se usan con más frecuencia en una canalización de enriquecimiento. Los resultados se extraen en un índice de búsqueda par una búsqueda de texto completo, o bien se devuelven a Azure Storage para permitir nuevas experiencias de aplicación, como la exploración de datos para escenarios de detección o análisis. 

En este artículo, vemos el enriquecimiento con inteligencia artificial a través de una lente amplia para que pueda captar rápidamente todo el proceso, desde la transformación de los datos sin procesar almacenados en los blobs hasta la información consultable en un índice de búsqueda o en un almacén de información.

## <a name="what-it-means-to-enrich-blob-data-with-ai"></a>Qué significa "enriquecer" los datos de los blobs con inteligencia artificial

El *enriquecimiento con IA* forma parte de la arquitectura de indexación de Azure Cognitive Search que integra modelos de Machine Learning de Microsoft o modelos de aprendizaje personalizado proporcionados por el usuario. Ayuda a implementar escenarios de un extremo a otro en los que es necesario procesar los blobs (tanto los existentes como los nuevos a medida que se incluyen o se actualizan), descifrar todos los formatos de archivo para extraer imágenes y texto, extraer la información deseada con diversas funcionalidades de IA e indexarlos en un índice de búsqueda para operaciones rápidas de búsqueda, recuperación y exploración. 

Las entradas son los blobs, en un solo contenedor, de Azure Blob Storage. Los blobs pueden ser casi cualquier tipo de datos de texto o de imagen. 

La salida siempre es un índice de búsqueda, que se usa para la búsqueda rápida, la recuperación y la exploración de texto en aplicaciones cliente. Además, la salida también puede ser un [*almacén de conocimiento*](knowledge-store-concept-intro.md) que proyecta documentos enriquecidos en blobs o tablas de Azure para el análisis descendente en herramientas como Power BI o en cargas de trabajo de ciencia de datos.

En medio, está la propia arquitectura de la canalización. La canalización se basa en los [*indexadores*](search-indexer-overview.md), a los que puede asignar un [*conjunto de aptitudes*](cognitive-search-working-with-skillsets.md) compuesto por una o varias *aptitudes* que proporciona la IA. El propósito de la canalización es generar *documentos enriquecidos* que entren en la canalización como contenido sin procesar y que vaya incorporando una estructura, un contexto e información adicionales mientras se desplaza por la canalización. Los documentos enriquecidos se consumen durante la indexación para crear índices invertidos y otras estructuras que se usan en la búsqueda de texto completo o en la exploración y el análisis.

## <a name="required-resources"></a>Recursos necesarios

Aparte de Azure Blob Storage y Azure Cognitive Search, necesita un tercer servicio o mecanismo que proporcione la IA:

+ En el caso de la inteligencia artificial integrada, Cognitive Search se integra con las API de procesamiento de lenguaje natural y Azure Cognitive Services. Puede [incorporar un recurso de Cognitive Services](cognitive-search-attach-cognitive-services.md) para agregar reconocimiento óptico de caracteres (OCR), análisis de imágenes o procesamiento de lenguaje natural (detección de lenguaje, traducción de texto, reconocimiento de entidades y extracción de frases clave). 

+ En el caso de la inteligencia artificial que usa recursos de Azure, puede definir una aptitud personalizada que encapsule la función o el modelo externos que desee usar. Las [aptitudes personalizadas](cognitive-search-custom-skill-interface.md) pueden usar el código que proporcionan Azure Functions, Azure Machine Learning, Azure Form Recognizer u otro recurso al que se pueda acceder a través de HTTPS.

+ En el caso de la inteligencia artificial que no sea de Azure, es preciso que un indexador pueda acceder tanto a su modelo como a su módulo a través de HTTP.

Si no tiene todos los servicios disponibles, comience directamente en la página del portal de la cuenta de almacenamiento. En la página de navegación izquierda, en **Blob service** haga clic en **Agregar Azure Cognitive Search** para crear un nuevo servicio o seleccionar uno existente. 

Una vez que agregue Azure Cognitive Search a su cuenta de almacenamiento, puede seguir el proceso estándar para enriquecer los datos de cualquier origen de datos de Azure. Se recomienda usar el asistente para la **importación de datos** de Azure Cognitive Search como introducción inicial al enriquecimiento con IA. Puede incorporar un recurso de Cognitive Services durante el flujo de trabajo. Esta guía de inicio rápido le guiará por los pasos siguientes: [Cree una canalización de enriquecimiento con IA en el portal](cognitive-search-quickstart-blob.md). 

En las secciones siguientes se examinan más detenidamente los componentes y el flujo de trabajo.

## <a name="use-a-blob-indexer"></a>Uso de un indexador de blobs

El enriquecimiento con IA es un complemento de una canalización de indexación y, en Azure Cognitive Search, esas canalizaciones se basan en un *indexador*. Un indexador es un subservicio con reconocimiento del origen de datos y equipado con lógica interna para el muestreo de datos, la lectura de datos de los metadatos, la recuperación de datos y la serialización de datos procedentes de formatos nativos en documentos JSON para su posterior importación. Los indexadores suelen usarse por sí mismos para la importación, aparte de la IA, pero si desea compilar una canalización de enriquecimiento con inteligencia artificial, necesitará acompañarla de un indexador y aptitudes. En esta sección se resalta el indexador; la siguiente sección se centra en las aptitudes.

Para indexar los blobs de Azure Storage se utiliza el [indexador de blobs](search-howto-indexing-azure-blob-storage.md), que se puede invocar mediante el Asistente para **importar datos**, una API REST o un SDK. Cuando el origen de datos que utiliza el indexador es un contenedor de blobs de Azure, se invoca a un indexador de blobs. Para indexar un subconjunto de blobs, cree un directorio virtual, que luego puede pasar como parámetro, o filtre por una extensión de tipo de archivo.

Un indizador [realiza el "descifrado de documentos"](search-indexer-overview.md#document-cracking) y abre un blob para inspeccionar el contenido. Después de conectarse al origen de datos, es el primer paso de la canalización. En el caso de los datos de blob, aquí es donde se detectan los archivos PDF, los documentos de Office, las imágenes y otros tipos de contenido. El descifrado de documentos con extracción de texto no tiene ningún cargo. El descifrado de documentos con extracción de imágenes se cobra aplicando las tarifas que puede encontrar en la [página de precios](https://azure.microsoft.com/pricing/details/search/).

Aunque se descifrarán todos los documentos, el enriquecimiento solo se produce si se proporcionan explícitamente las aptitudes para hacerlo. Por ejemplo, si la canalización consiste exclusivamente en el análisis de imágenes, se omite el texto del contenedor o de los documentos.

El indexador de blobs incluye parámetros de configuración y admite el seguimiento de cambios si los datos subyacentes proporcionan suficiente información. Para más información, consulte [Indexación de documentos en Azure Blob Storage con Azure Cognitive Search](search-howto-indexing-azure-blob-storage.md).

## <a name="add-ai-components"></a>Adición de componentes de IA

El enriquecimiento con IA hace referencia a módulos que buscan patrones o características y, después, realiza la operación correspondiente. El reconocimiento facial en fotos, las descripciones de texto de fotos, la detección de frases clave en un documento y OCR (o reconocimiento de texto impreso o manuscrito en archivos binarios) son algunos ejemplos.

En Azure Cognitive Search, las *aptitudes* son los componentes individuales del procesamiento de la IA que puede usar de forma independiente o en combinación con otras aptitudes. 

+ Las aptitudes integradas cuentan con el respaldo de Cognitive Services, con análisis de imágenes basado en Computer Vision y procesamiento de lenguaje natural basado en Text Analytics. Para obtener la lista completa, consulte las [aptitudes integradas para el enriquecimiento de contenido](cognitive-search-predefined-skills.md).

+ Las aptitudes personalizadas son código personalizado, incluido en una [definición de interfaz](cognitive-search-custom-skill-interface.md) que permite su integración en la canalización. En las soluciones de los clientes, es una práctica común usar ambos, con aptitudes personalizadas que proporcionan módulos de IA de código abierto, de terceros o propios.

Un *conjunto de aptitudes* es la colección de aptitudes que se usa en una canalización y se invoca después de que la fase de descifrado de documentos haga que el contenido esté disponible. Un indexador puede consumir exactamente un conjunto de aptitudes, pero ese conjunto existe independientemente de un indexador, por lo que puede volver a usarlo en otros escenarios.

Las aptitudes personalizadas pueden parecer complejas, pero pueden ser sencillas y directas en términos de implementación. Si ya tiene paquetes que proporcionan coincidencia de patrones o modelos de clasificación, el contenido que extraiga de los blobs podría pasarse a estos modelos para su procesamiento. Dado que el enriquecimiento con IA se basado en Azure, el modelo debe estar también en Azure. Algunas metodologías de hospedaje comunes son [funciones](cognitive-search-create-custom-skill-example.md) o [contenedores](https://github.com/Microsoft/SkillsExtractorCognitiveSearch) de Azure.

Las aptitudes integradas que usan la tecnología de Cognitive Services requieren una clave de suscripción integral de [Cognitive Services](cognitive-search-attach-cognitive-services.md) que proporcione acceso al recurso. Las claves integrales ofrecen análisis de imágenes, detección de idioma, traducción de texto y análisis de texto. Otras aptitudes integradas son características de Azure Cognitive Search y no requieren ninguna clave o servicio adicionales. El conformador, divisor y combinador son ejemplos de aptitudes auxiliares que a veces se necesitan para diseñar la canalización.

Si solo usa aptitudes personalizadas y aptitudes de utilidad integradas, no hay ninguna dependencia ni costos asociados a Cognitive Services.

## <a name="consume-ai-enriched-output-in-downstream-solutions"></a>Consumo de la salida enriquecida con IA en soluciones de nivel inferior

La salida del enriquecimiento con IA es un índice de búsqueda en Azure Cognitive Search o un [almacén de conocimiento](knowledge-store-concept-intro.md) en Azure Storage.

En Azure Cognitive Search, un índice de búsqueda se usa para la exploración interactiva mediante consultas de texto libre y filtradas en una aplicación cliente. A los documentos enriquecidos creados mediante IA se les aplica el formato JSON y se indexan de la misma manera que todos los documentos en Azure Cognitive Search, aprovechando todas las ventajas que proporciona un indexador. Por ejemplo, durante la indexación, el indexador de blobs hace referencia a los parámetros y los valores de configuración para utilizar las asignaciones de campos o la lógica de detección de cambios. Estos valores están totalmente disponibles para la indexación normal y las cargas de trabajo enriquecidas con IA. Después de la indexación, cuando el contenido se almacena en Azure Cognitive Search, puede crear consultas enriquecidas y expresiones de filtro para comprender el contenido.

En Azure Storage, un almacén de conocimiento tiene dos manifestaciones: un contenedor de blobs o tablas en Table Storage. 

+ Un contenedor de blobs captura documentos enriquecidos en su totalidad, lo que resulta útil si desea utilizarlos en otros procesos. 

+ En cambio, Table Storage puede incluir proyecciones físicas de documentos enriquecidos. Puede crear segmentos o capas de documentos enriquecidos que incluyan o excluyan partes específicas. Para su análisis en Power BI, las tablas de Azure Table Storage se convierten en el origen de datos para una posterior visualización y exploración.

El documento enriquecido al final de la canalización difiere de la versión de entrada original por la presencia de campos adicionales que contienen información nueva que se ha extraído o generado durante el enriquecimiento. Como tal, puede trabajar con una combinación de contenido original y creado, con independencia de la estructura de salida que use.

## <a name="next-steps"></a>Pasos siguientes

Hay muchas más cosas que puede hacer con el enriquecimiento con inteligencia artificial para sacar el máximo partido de los datos en Azure Storage, incluida la combinación de servicios de Cognitive Services de distintas maneras y la creación de aptitudes personalizadas para los casos en los que no haya ningún servicio de Cognitive Services en el escenario. Para más información, siga los vínculos que aparecen a continuación.

+ [Carga, descarga y enumeración de blobs con Azure Portal (Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [Configuración de un indexador de blobs (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md) 
+ [Información general sobre el enriquecimiento de AI (Azure Cognitive Search)](cognitive-search-concept-intro.md) 
+ [Crear un conjunto de aptitudes (Azure Cognitive Search)](cognitive-search-defining-skillset.md)
+ [Asignar nodos en un árbol de anotaciones (Azure Cognitive Search)](cognitive-search-output-field-mapping.md)
