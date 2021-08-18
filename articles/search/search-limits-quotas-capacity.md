---
title: Límites de servicio para niveles y SKU
titleSuffix: Azure Cognitive Search
description: Límites de servicio usados en el planeamiento de la capacidad y límites máximos sobre solicitudes y respuestas de Azure Cognitive Search.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 9034d493e0ab10d8c548170cac99238d5edc4e22
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113003879"
---
# <a name="service-limits-in-azure-cognitive-search"></a>Límites de servicio en Azure Cognitive Search

Los límites máximos sobre el almacenamiento, las cargas de trabajo, las cantidades de índices y otros objetos dependen de si se [aprovisiona Azure Cognitive Search](search-create-service-portal.md) conforme a un plan de tarifa **Gratis**, **Básico**, **Estándar** u **Optimizado para almacenamiento**.

+ **Gratis** es un servicio multiinquilino compartido incluido en su suscripción de Azure. 

+ **Básico** proporciona recursos informáticos dedicados para cargas de trabajo de producción en una escala menor, pero comparte algunas infraestructuras de red con otros inquilinos.

+ **Estándar** se ejecuta en máquinas dedicadas, con más almacenamiento y capacidad de procesamiento en cada nivel. Estándar incluye cuatro niveles: S1, S2, S3 y S3 HD. S3 High Density (S3 HD) está diseñado para [multiinquilinato](search-modeling-multitenant-saas-applications.md) y grandes cantidades de índices pequeños (tres mil índices por servicio). S3 HD no proporciona la [característica de indexador](search-indexer-overview.md) y la ingesta de datos debe aprovechar las API que envían datos del origen al índice. 

+ El plan de tarifa **Almacenamiento optimizado** se ejecuta en máquinas dedicadas con más almacenamiento total, ancho de banda de almacenamiento y memoria que el plan **Estándar**. Este nivel tiene como objetivo índices grandes que cambian con lentitud. Dicho plan se ofrece en dos niveles: L1 y L2.

## <a name="subscription-limits"></a>Límites de suscripción
[!INCLUDE [azure-search-limits-per-subscription](../../includes/azure-search-limits-per-subscription.md)]

## <a name="storage-limits"></a>Límites de Storage
[!INCLUDE [azure-search-limits-per-service](../../includes/azure-search-limits-per-service.md)]

<a name="index-limits"></a>

## <a name="index-limits"></a>Límites de índice

| Resource | Gratuito | Básico&nbsp;<sup>1</sup>  | S1 | S2 | S3 | S3&nbsp;HD | L1 | L2 |
| -------- | ---- | ------------------- | --- | --- | --- | --- | --- | --- |
| Índices máximos |3 |5 o 15 |50 |200 |200 |1000 GB por partición o 3000 por servicio |10 |10 |
| Campos simples máximos por índice |1000 |100 |1000 |1000 |1000 |1000 |1000 |1000 |
| Campos de colección complejos máximos por índice |40 |40 |40 |40 |40 |40 |40 |40 |
| Número máximo de elementos en todas las colecciones complejas por documento&nbsp;<sup>2</sup> |3000 |3000 |3000 |3000 |3000 |3000 |3000 |3000 |
| Profundidad máxima de campos complejos |10 |10 |10 |10 |10 |10 |10 |10 |
| [Proveedores de sugerencias](/rest/api/searchservice/suggesters) máximos por índice |1 |1 |1 |1 |1 |1 |1 |1 |
| [Perfiles de puntuación](/rest/api/searchservice/add-scoring-profiles-to-a-search-index) máximos por índice |100 |100 |100 |100 |100 |100 |100 |100 |
| Funciones máximas por perfil |8 |8 |8 |8 |8 |8 |8 |8 |

<sup>1</sup> Los servicios básicos creados antes de diciembre de 2017 tienen límites inferiores (5 en lugar de 15) en los índices. El nivel básico es la única SKU con un límite inferior de 100 campos por índice.

<sup>2</sup> Existe un límite superior para los elementos porque tener un gran número de ellos aumenta significativamente el almacenamiento necesario para el índice. Un elemento de una colección compleja se define como un miembro de esa colección. Por ejemplo, supongamos [un documento de hotel con una colección de habitaciones compleja](search-howto-complex-data-types.md#indexing-complex-types), donde cada habitación de dicha colección se considera un elemento. Durante la indexación, el motor de indexación puede procesar de forma segura un máximo de 3000 elementos en el documento en su totalidad. [Este límite](search-api-migration.md#upgrade-to-2019-05-06) se presentó en `api-version=2019-05-06` y se aplica solo a colecciones complejas, no a colecciones de cadenas ni a campos complejos.

<a name="document-limits"></a>

## <a name="document-limits"></a>Límites de documento 

A partir de octubre de 2018, ya no existen límites de documentos para ningún nuevo servicio creado en ningún nivel facturable (Básico, S1, S2, S3, S3 HD) en ninguna región. Los servicios antiguos creados antes de octubre del 2018 todavía pueden estar sujetos a límites en el número de documentos.

Para determinar si su servicio tiene límites de documento, use la [API de REST de estadísticas del servicio GET](/rest/api/searchservice/get-service-statistics). Los límites de documento se reflejan en la respuesta, con `null` indicando que no hay límites.

> [!NOTE]
> Aunque no hay ningún límite de documentos impuesto por el servicio, hay un límite de particiones de aproximadamente 24 mil millones de documentos por índice en los servicios de búsqueda Básico, S1, S2 y S3. En el caso de S3 HD, el límite de particiones es de 2 mil millones de documentos por índice. Cada elemento de una colección compleja cuenta como un único documento en lo que respecta a los límites de las particiones.

### <a name="document-size-limits-per-api-call"></a>Límites de tamaño de documento por llamada API

El tamaño máximo del documento cuando se llama a una API de índice es de aproximadamente 16 megabytes.

El tamaño del documento es de hecho un límite en el tamaño del cuerpo de la solicitud de la API de índice. Dado que se puede pasar un lote de varios documentos a la API de índice de una vez, el límite de tamaño depende en realidad de cuántos documentos estén en el lote. Para un lote con un único documento, el tamaño máximo del documento es de 16 MB de JSON.

Al calcular el tamaño del documento, recuerde tener en cuenta solo los campos que puede usar un servicio de búsqueda. Los datos binarios o de imagen de los documentos de origen deben omitirse en los cálculos.  

## <a name="indexer-limits"></a>Límites de indexador

Los tiempos de ejecución máximos existen para proporcionar equilibrio y estabilidad al servicio como un todo, pero es posible que los conjuntos de datos más grandes necesiten más tiempo de indexación que el que permite el máximo. Si un trabajo de indexación no se puede completar en el tiempo máximo permitido, intente ejecutarlo en una programación. El programador realiza un seguimiento del estado de la indexación. Si un trabajo programado de indexación se interrumpe por cualquier motivo, el indizador puede reanudarlo donde se quedó en la siguiente ejecución programada.


| Resource | Gratis&nbsp;<sup>1</sup> | Básico&nbsp;<sup>2</sup>| S1 | S2 | S3 | S3&nbsp;HD&nbsp;<sup>3</sup>|L1 |L2 |
| -------- | ----------------- | ----------------- | --- | --- | --- | --- | --- | --- |
| Indexadores máximos |3 |5 o 15|50 |200 |200 |N/D |10 |10 |
| Orígenes de datos máximos |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Conjuntos de habilidades máximos <sup>4</sup> |3 |5 o 15 |50 |200 |200 |N/D |10 |10 |
| Carga máxima de indexación por invocación |10 000 documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |Limitado solamente por el número máximo de documentos |N/D |Sin límite |Sin límite |
| Programación mínima | 5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos |5 minutos | 5 minutos |
| Tiempo de ejecución máximo| 1-3 minutos |24 horas |24 horas |24 horas |24 horas |N/D  |24 horas |24 horas |
| Tiempo máximo de ejecución de los indexadores con un aptitudes <sup>5</sup> | 3-10 minutos |2 horas |2 horas |2 horas |2 horas |N/D  |2 horas |2 horas |
| Indexador de blobs: tamaño máximo de blob, MB |16 |16 |128 |256 |256 |N/D  |256 |256 |
| Indexador de blobs: número máximo de caracteres del contenido extraído de un blob |32 000 |64 000 |4&nbsp;millones |8&nbsp;millones |16&nbsp;millones |N/D |4&nbsp;millones |4&nbsp;millones |

<sup>1</sup> Los servicios gratuitos tienen un tiempo de ejecución máximo del indexador de 3 minutos para servicios de blob y de 1 minuto para todos los demás orígenes de datos. Para la indexación de IA que llama a Cognitive Services, los servicios gratuitos se limitan a 20 transacciones gratuitas por día, donde una transacción se define como un documento que pasa correctamente a través de la canalización enriquecimiento.

<sup>2</sup> Los servicios básicos creados antes de diciembre de 2017 tienen límites inferiores (5 en lugar de 15) en los indexadores, los orígenes de datos y los conjuntos de aptitudes.

<sup>3</sup> Los servicios S3 HD no incluyen compatibilidad con indexador.

<sup>4</sup> Máximo de 30 habilidades por conjunto de habilidades.

<sup>5</sup> El análisis de imágenes y el enriquecimiento con IA consumen muchos recursos informáticos y una cantidad desproporcionada de la potencia de procesamiento disponible. El tiempo de ejecución de estas cargas de trabajo se ha abreviado para que otros trabajos de la cola tengan más posibilidades de ejecutarse.

> [!NOTE]
> Como se indica en el apartado [Límites de índice](#index-limits), los indexadores también aplicarán el límite superior de 3000 elementos en todas las colecciones complejas por documento a partir de la versión de la API de disponibilidad general más reciente que admita tipos complejos (`2019-05-06`) en adelante, lo que significa que si ha creado un indexador con una versión anterior de la API, no estará sujeto a ese límite. Para conservar la máxima compatibilidad, los indexadores que se crearon con una versión anterior de la API y, después, se actualizaron con una versión de la API `2019-05-06`, o posterior, seguirá estando **excluido** de los límites. Los clientes deben ser conscientes del impacto negativo de tener colecciones muy complejas (como se ha indicado anteriormente) y recomendamos encarecidamente que se creen todos los indexadores con la versión de la API de disponibilidad general más reciente.

## <a name="shared-private-link-resource-limits"></a>Límites de recursos compartidos de Private Link

Los indexadores pueden acceder a otros recursos de Azure mediante [puntos de conexión privados](search-indexer-howto-access-private.md) administrados con la [API de recursos compartidos de Private Link](/rest/api/searchmanagement/2020-08-01/shared-private-link-resources). En esta sección se describen los límites asociados a esta funcionalidad.

| Resource | Gratuito | Básica | S1 | S2 | S3 | S3 HD | L1 | L2
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Compatibilidad con indexador de puntos de conexión privados | No | Sí | Sí | Sí | Sí | No | Sí | Sí |
| Compatibilidad con puntos de conexión privados para indexadores con un conjunto de aptitudes<sup>1</sup> | No | No | No | Sí | Sí | No | Sí | Sí |
| Número máximo de puntos de conexión privados | N/D | 10 o 30 | 100 | 400 | 400 | N/D | 20 | 20 |
| Número máximo de tipos de recursos distintos<sup>2</sup> | N/D | 4 | 7 | 15 | 15 | N/D | 4 | 4 |

<sup>1</sup> El análisis de imágenes y el enriquecimiento con IA consumen muchos recursos informáticos y una cantidad desproporcionada de la potencia de procesamiento disponible. Por este motivo, las conexiones privadas están deshabilitadas en los niveles inferiores para evitar un impacto adverso en el rendimiento y la estabilidad del propio servicio de búsqueda.

<sup>2</sup> El número de tipos de recursos distintos se calcula como el número de valores de `groupId` únicos utilizados en todos los recursos compartidos de Private Link para un servicio de búsqueda determinado, independientemente del estado del recurso.

## <a name="synonym-limits"></a>Límites de sinónimos

El número máximo de asignaciones de sinónimos varía según el nivel. Cada regla puede tener hasta 20 expansiones, donde una expansión es un término equivalente. Por ejemplo, dado el término "gato", la asociación con "gatito", "felino" y "felis" (el género de gatos) contaría como 3 expansiones.

| Resource | Gratuito | Básica | S1 | S2 | S3 | S3-HD |L1 | L2 |
| -------- | -----|------ |----|----|----|-------|---|----|
| Asignaciones máximas de sinónimos |3 |3|5 |10 |20 |20 | 10 | 10 |
| Número máximo de reglas por asignación |5000 |20000|20000 |20000 |20000 |20000 | 20000 | 20000  |

## <a name="data-limits-ai-enrichment"></a>Límites de datos (enriquecimiento de inteligencia artificial)

Una [canalización de enriquecimiento con IA](cognitive-search-concept-intro.md) que realiza llamadas a un recurso de Text Analytics para el [reconocimiento de entidades](cognitive-search-skill-entity-recognition-v3.md), la [vinculación de entidad](cognitive-search-skill-entity-linking-v3.md), la [extracción de frases clave](cognitive-search-skill-keyphrases.md), el [análisis de sentimiento](cognitive-search-skill-sentiment-v3.md), la [detección de idioma](cognitive-search-skill-language-detection.md) y la [detección de información personal](cognitive-search-skill-pii-detection.md) está sujeta a límites de datos. El tamaño máximo de un registro debe tener menos de 50 000 caracteres según la medición de [`String.Length`](/dotnet/api/system.string.length). Si tiene que dividir los datos antes de enviarlos al analizador de opiniones, use la [aptitud División de texto](cognitive-search-skill-textsplit.md).

## <a name="throttling-limits"></a>Limitaciones

Las solicitudes de API se limitan a medida que el sistema se aproxima a la capacidad máxima. La limitación se comporta de forma diferente para las distintas API. Las API de consulta (Search/Suggest/Autocomplete) y las de indexación se limitan dinámicamente en función de la carga del servicio. Las API de índice y las API de operaciones de servicio tienen límites estáticos de frecuencia de solicitud. 

Límites estáticos de frecuencia de solicitudes para las operaciones relacionadas con un índice:

+ Enumerar índices (GET /indexes): 3 por segundo por unidad de búsqueda
+ Obtener índice (GET /indexes/myindex): 10 por segundo por unidad de búsqueda
+ Crear índice (POST /indexes): 12 por minuto por unidad de búsqueda
+ Crear o actualizar índice (PUT /indexes/myindex): 6 por segundo por unidad de búsqueda
+ Eliminar índice (DELETE /indexes/myindex): 12 por minuto por unidad de búsqueda 

Límites estáticos de frecuencia de solicitudes para las operaciones relacionadas con un servicio:

+ Estadísticas del servicio (GET /servicestats): 4 por segundo por unidad de búsqueda

## <a name="api-request-limits"></a>Límites de solicitud de API
* Máximo de 16 MB por solicitud <sup>1</sup>
* Longitud máxima de dirección URL de 8 KB
* Máximo de 1000 documentos por lote del índice de cargas de índices, combinaciones o eliminaciones
* Máximo de 32 campos en cláusula $orderby
* El tamaño máximo del término de búsqueda es de 32 766 bytes (32 KB menos 2 bytes) de texto con codificación UTF-8

<sup>1</sup> En Azure Cognitive Search, el cuerpo de una solicitud está sujeto a un límite superior de 16 MB, lo que impone un límite práctico sobre el contenido de campos individuales o colecciones que no están restringidos de algún modo por límites teóricos (consulte [Tipos de datos admitidos](/rest/api/searchservice/supported-data-types) para más información sobre composición de campos y restricciones).

## <a name="api-response-limits"></a>Límites de respuesta de API
* Máximo de 1000 documentos devueltos por página de resultados de búsqueda
* Máximo de 100 sugerencias devueltas por solicitud de Sugerir API

## <a name="api-key-limits"></a>Límites de clave de API
Las claves de API se usan para la autenticación del servicio. Hay dos tipos. Las claves de administración se especifican en el encabezado de solicitud y conceden acceso completo de lectura y escritura al servicio. Las claves de consulta son de solo lectura, se especifican en la dirección URL y normalmente se distribuyen a las aplicaciones cliente.

* Máximo de 2 claves de administración por servicio
* Máximo de 50 claves de consultas por servicio
