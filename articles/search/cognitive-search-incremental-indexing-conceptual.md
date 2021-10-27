---
title: Conceptos de enriquecimiento incremental (versión preliminar)
titleSuffix: Azure Cognitive Search
description: Almacene en caché contenido intermedio y cambios incrementales de la canalización de enriquecimiento con IA en Azure Storage para conservar las inversiones en los documentos procesados existentes. Esta característica actualmente está en su versión preliminar pública.
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/17/2021
ms.openlocfilehash: cb04bbea71588ea8d9fa1f1c4734b10e3d0b6792
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130179210"
---
# <a name="incremental-enrichment-and-caching-in-azure-cognitive-search"></a>Enriquecimiento incremental y almacenamiento en caché en Azure Cognitive Search

> [!IMPORTANT] 
> Esta característica se encuentra en versión preliminar pública en los [términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). La [API REST de versión preliminar](/rest/api/searchservice/index-preview) admite esta característica.

El *enriquecimiento incremental* hace referencia al uso de enriquecimientos en caché durante la [ejecución de conjuntos de aptitudes](cognitive-search-working-with-skillsets.md) para que solo se realice el procesamiento de IA en las aptitudes y documentos que hayan experimentado cambios. La memoria caché contiene la salida del [descifrado de documentos](search-indexer-overview.md#document-cracking) más las salidas de todas las aptitudes de todos los documentos. Dado que la extracción de imágenes y el procesamiento de IA son [eventos facturables](search-sku-manage-costs.md#billable-events), habilitar la memoria caché reducirá el costo, así como el tiempo de procesamiento, del enriquecimiento con IA. 

Al habilitar el almacenamiento en caché, el indizador evalúa las actualizaciones para determinar si los enriquecimientos existentes se pueden extraer de la memoria caché. Es probable que el contenido de imagen y texto de la fase de descifrado de documentos, además de las salidas de aptitudes que son ascendentes u ortogonales para las ediciones, sean reutilizables.

Después de realizar los enriquecimientos incrementales indicados por la actualización del conjunto de aptitudes, los resultados actualizados se escriben de nuevo en la memoria caché y también en el índice de búsqueda o el almacén de conocimiento.

## <a name="cache-configuration"></a>Configuración de la caché

Físicamente, la caché se almacena en un contenedor de blobs en la cuenta de Azure Storage, uno por indizador. A cada indizador se le asigna un identificador de caché único e inmutable correspondiente al contenedor que usa.

La memoria caché se crea al especificar la propiedad "cache" y ejecutar el indizador. Solo se puede almacenar en caché el contenido enriquecido. Si el indizador no tiene ningún conjunto de aptitudes asociado, no se aplica el almacenamiento en caché. 

En el ejemplo siguiente se muestra un indexador con el almacenamiento en caché habilitado. Para obtener instrucciones completas, consulte [Habilitación del almacenamiento en caché del enriquecimiento](search-howto-incremental-index.md). Tenga en cuenta que, al agregar la propiedad de caché, debe usar la versión preliminar de la API, 2020-06-30-Preview o posterior, en la solicitud.

```json
POST https://[search service name].search.windows.net/indexers?api-version=2020-06-30-Preview
    {
        "name": "myIndexerName",
        "targetIndexName": "myIndex",
        "dataSourceName": "myDatasource",
        "skillsetName": "mySkillset",
        "cache" : {
            "storageConnectionString" : "<Your storage account connection string>",
            "enableReprocessing": true
        },
        "fieldMappings" : [],
        "outputFieldMappings": [],
        "parameters": []
    }
```

## <a name="cache-management"></a>Administración de la memoria caché

El indexador administra el ciclo de vida de la caché. Si se elimina un indexador, también se elimina la memoria caché correspondiente. Si la propiedad "cache" del indizador está establecida en NULL o la cadena de conexión ha cambiado, se eliminará la caché existente en la siguiente ejecución del indizador. 

Aunque el enriquecimiento incremental está diseñado para detectar y responder a los cambios sin intervención por su parte, existen parámetros que puede usar para invocar comportamientos específicos:

+ [Priorizar nuevos documentos](#Prioritize-new-documents)
+ [Omitir comprobaciones de conjuntos de aptitudes](#Bypass-skillset-checks)
+ [Omitir comprobaciones de origen de datos](#Bypass-data-source-check)
+ [Forzar la evaluación de conjuntos de aptitudes](#Force-skillset-evaluation)

<a name="Prioritize-new-documents"></a>

### <a name="prioritize-new-documents"></a>Priorizar nuevos documentos

La propiedad "cache" incluye un parámetro "enableReprocessing". Se usa para controlar el procesamiento de los documentos entrantes ya representados en la caché. Cuando se define como true (valor predeterminado), los documentos que ya están en la caché se vuelven a procesar cuando se vuelve a ejecutar el indizador, siempre que la actualización de las aptitudes afecte a ese documento. 

Cuando se define como false, los documentos existentes no se vuelven a procesar, con lo que se prioriza de forma eficaz el contenido nuevo y entrante sobre el existente. Solo debe definir "enableReprocessing" como false de forma temporal. Tener "enableReprocessing" definido como true la mayor parte del tiempo garantiza que todos los documentos, tanto los nuevos como los existentes, sean válidos según la definición del conjunto de aptitudes actual.

<a name="Bypass-skillset-checks"></a>

### <a name="bypass-skillset-evaluation"></a>Omitir la evaluación del conjunto de aptitudes

La modificación y el reprocesamiento de una aptitud suelen ir de la mano. Pero algunos cambios en una aptitud no deben dar como resultado el reprocesamiento (por ejemplo, la implementación de una aptitud personalizada en una ubicación nueva o con una clave de acceso nueva). Lo más probable es que se trate de modificaciones periféricas, sin un impacto real en la base de la propia salida de la aptitud. 

Si sabe que un cambio en la aptitud es superficial, debe invalidar la evaluación de la aptitud. Para hacerlo, defina el parámetro "disableCacheReprocessingChangeDetection" como true:

1. Llame a [Update Skillset](/rest/api/searchservice/update-skillset) y modifique la definición del conjunto de aptitudes.
1. Anexe el parámetro "disableCacheReprocessingChangeDetection=true" en la solicitud.
1. Envíe el cambio.

Al establecer este parámetro se garantiza que solo se confirman las actualizaciones de la definición del conjunto de aptitudes y que el cambio no se evalúa para los efectos en la caché existente. Use una versión preliminar de la API: 2020-06-30-Preview o posterior.

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30-Preview
    {
        "disableCacheReprocessingChangeDetection" : true
    }
```

<a name="Bypass-data-source-check"></a>

### <a name="bypass-data-source-validation-checks"></a>Omitir comprobaciones de validación de origen de datos

La mayoría de los cambios en una definición de origen de datos invalidarán la caché. Pero para los escenarios en los que sabe que un cambio no debe invalidar la caché, como cambiar una cadena de conexión o rotar la clave en la cuenta de almacenamiento, anexe el parámetro "ignoreResetRequirement" en la actualización del origen de datos. Establecer este parámetro en true permite que se realice la confirmación, sin desencadenar una condición de restablecimiento que daría lugar a que todos los objetos se volvieran a generar y se rellenaran desde el principio.

```http
PUT https://[search service].search.windows.net/datasources/[data source name]?api-version=2020-06-30-Preview
    {
        "ignoreResetRequirement" : true
    }
```

<a name="Force-skillset-evaluation"></a>

### <a name="force-skillset-evaluation"></a>Forzar la evaluación de conjuntos de aptitudes

El propósito de la caché es evitar procesamientos innecesarios, pero imagine que realiza un cambio en una aptitud que el indizador no detecta (por ejemplo, cambiar un elemento del código externo, como una aptitud personalizada).

En este caso, puede usar [Reset Skills](/rest/api/searchservice/preview-api/reset-skills) para forzar el reprocesamiento de una aptitud determinada, incluidos los conocimientos de nivel inferior que tengan una dependencia en la salida de esa aptitud. Esta API acepta una solicitud POST con una lista de aptitudes que se deben invalidar y marcar para volver a procesarse. Después de restablecer las aptitudes, siga con una solicitud para [ejecutar el indizador](/rest/api/searchservice/run-indexer) para invocar el procesamiento de la canalización.

## <a name="re-cache-specific-documents"></a>Volver a almacenar en caché documentos específicos

Al [restablecer un indizador](/rest/api/searchservice/reset-indexer), se volverán a procesar todos los documentos del corpus de búsqueda. En aquellos escenarios en los que solo haya que volver a procesar unos pocos documentos, use el [Restablecimiento de documentos (versión preliminar)](/rest/api/searchservice/preview-api/reset-documents) para forzar el reprocesamiento de determinados documentos. Cuando se restablece un documento, el indizador invalida la caché del documento que, a continuación, se vuelve a procesar mediante su lectura desde el origen de datos. Para más información, consulte el artículo en el que se explican los [procedimientos para ejecutar o restablecer indexadores, aptitudes y documentos](search-howto-run-reset-indexers.md).

Para restablecer documentos específicos, la solicitud proporciona una lista de claves de documento que se leen desde el índice de búsqueda. Si la clave se asigna a un campo del origen de datos externo, el valor que proporcione debe ser el que se usa en el índice de búsqueda.

En función de cómo se llame a la API, la solicitud anexará la lista de claves, la sobrescribirá o la pondrá en cola:

+ La llamada a la API varias veces con diferentes claves anexa las nuevas claves a la lista de claves de documento restablecidas. 

+ La llamada a la API con el parámetro de cadena de consulta "overwrite" definido como true sobrescribirá la lista actual de claves de documento que se van a restablecer con la carga útil de la solicitud.

+ La llamada a la API solo da como resultado que las claves de documento se agreguen a la cola de tareas que realiza el indexador. La próxima vez que se invoque al indexador, ya sea de forma programada o a petición, se dará prioridad al procesamiento de las claves de restablecimiento de documento antes que a cualquier otro cambio del origen de datos.

En el ejemplo siguiente se muestra una solicitud de restablecimiento de documento:

```http
POST https://[search service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
    {
        "documentKeys" : [
            "key1",
            "key2",
            "key3"
        ]
    }
```

## <a name="changes-that-invalidate-the-cache"></a>Cambios que invalidan la caché

Una vez que se ha habilitado la caché, el indizador evalúa los cambios en la composición de la canalización para determinar qué contenido se puede reusar y cuál se debe volver a procesar. En esta sección se enumeran los cambios que invalidan la caché directamente, seguidos de los que desencadenan el procesamiento incremental. 

Un cambio invalidador es aquél en el que la caché completa deja de ser válida. Un ejemplo de cambio invalidador es en el que se actualiza el origen de datos. Esta es la lista completa de cambios en cualquier parte de la canalización del indexador que invalidaría la memoria caché:

+ Cambio del tipo del origen de datos.
+ Cambio de contenedor del origen de datos
+ Cambio de credenciales del origen de datos
+ Cambio de directiva de detección de cambios en el origen de datos
+ Cambio de directiva de detección de eliminación de orígenes de datos
+ Cambio de asignaciones de campos del indizador
+ Cambio de parámetros del indizador:
  + Modo de análisis
  + Extensiones de nombre de archivo excluidas
  + Extensiones de nombre de archivo indexadas
  + Metadatos de almacenamiento de índice solo para documentos de gran tamaño
  + Encabezados de texto delimitado
  + Delimitador de texto delimitado
  + Raíz del documento
  + Acción de imagen (cambios en la forma en que se extraen las imágenes)

## <a name="changes-that-trigger-incremental-processing"></a>Cambios que desencadenan el procesamiento incremental

El procesamiento incremental evalúa la definición del conjunto de aptitudes y determina qué aptitudes se deben volver a ejecutar, con lo que se actualizan selectivamente las partes afectadas del árbol del documento. Esta es la lista completa de cambios que dan como resultado el enriquecimiento incremental:

+ Cambio de tipo de aptitud (se actualiza el tipo OData de la aptitud)
+ Se actualizan los parámetros específicos de la aptitud; p. ej. la dirección URL, los valores predeterminados u otros parámetros
+ Cambios en la salida de la aptitud: la aptitud devuelve salidas adicionales o diferentes
+ Cambios en la entrada de la aptitud que generan una ascendencia distinta: el encadenamiento de aptitudes ha cambiado
+ Cualquier invalidación de aptitudes ascendente, si se actualiza una aptitud que proporcione una entrada a esta aptitud
+ Actualizaciones en la ubicación de la proyección del almacén de información, lo que provoca que se vuelvan a proyectar los documentos
+ Cambios en las proyecciones del almacén de información, lo que provoca que se vuelvan a proyectar los documentos
+ Las asignaciones de campos de salida cambiadas en un indizador provocan que se vuelvan a proyectar los documentos en el índice

## <a name="apis-used-for-caching"></a>API usadas para el almacenamiento en caché

La versión de la API REST `2020-06-30-Preview` o posterior proporciona enriquecimiento incremental a través de propiedades adicionales en indizadores. Los conjuntos de aptitudes y los orígenes de datos pueden usar la versión disponible con carácter general. Además de la documentación de referencia, consulte [Configuración del almacenamiento en caché para el enriquecimiento incremental](search-howto-incremental-index.md) a fin de obtener más información sobre el orden de las operaciones.

+ [Creación o actualización del indizador (api-version=2020-06-30-Preview)](/rest/api/searchservice/preview-api/create-or-update-indexer) 

+ [Actualizar conjunto de aptitudes (api-version=2020-06-30)](/rest/api/searchservice/update-skillset) (nuevo parámetro URI en la solicitud)

+ [Restablecer aptitudes (api-version=2020-06-30)](/rest/api/searchservice/preview-api/reset-skills)

+ La opción [Actualizar origen de datos](/rest/api/searchservice/update-data-source), cuando se llama con una versión preliminar de la API, proporciona un nuevo parámetro denominado "ignoreResetRequirement", que debe establecerse en true cuando la acción de actualización no debe invalidar la memoria caché. Use "ignoreResetRequirement" con moderación, ya que puede dar lugar a incoherencias no intencionadas en los datos que no se detectarán fácilmente.

## <a name="next-steps"></a>Pasos siguientes

El enriquecimiento incremental es una característica eficaz que extiende el seguimiento de cambios en conjuntos de aptitudes y el enriquecimiento con IA. El enriquecimiento incremental permite la reutilización del contenido procesado existente mientras se realiza la iteración en el diseño del conjunto de aptitudes. Como paso siguiente, habilite el almacenamiento en caché en los indizadores.

> [!div class="nextstepaction"]
> [Habilitación del almacenamiento en caché para el enriquecimiento incremental](search-howto-incremental-index.md)