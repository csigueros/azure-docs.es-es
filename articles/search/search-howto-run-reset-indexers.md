---
title: Ejecución o restablecimiento de indexadores
titleSuffix: Azure Cognitive Search
description: Restablezca un indizador, aptitudes o documentos individuales para actualizar todo o una parte, y el índice o el almacén de conocimiento.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/09/2021
ms.openlocfilehash: de17fface1a99e7e3832cf815eadda6915a707fd
ms.sourcegitcommit: 54d8b979b7de84aa979327bdf251daf9a3b72964
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/24/2021
ms.locfileid: "112583156"
---
# <a name="how-to-run-or-reset-indexers-skills-or-documents"></a>Cómo ejecutar o restablecer indizadores, aptitudes o documentos

La ejecución del indizador puede producirse cuando se crea por primera vez el [indizador](search-indexer-overview.md), cuando se ejecuta un indizador a petición o cuando se establece un indizador según una programación. Después de la ejecución inicial, un indizador realiza un seguimiento de los documentos de búsqueda que se han indexado a través de una "marca de límite superior" interna. El marcador nunca se expone en la API, pero internamente el indizador sabe dónde se detuvo la indexación para que pueda recoger dónde se quedó en la siguiente ejecución.

Puede borrar la marca de límite superior restableciendo el indizador si desea volver a procesar desde cero. Las API de restablecimiento están disponibles en niveles decrecientes en la jerarquía de objetos:

+ Toda la búsqueda corpus (usar los [indizadores de restablecimiento](#reset-indexers))
+ Un documento o una lista de documentos específicos (usar [Restablecimiento de documentos (versión preliminar)](#reset-docs))
+ Una habilidad o enriquecimiento específico en un documento (usar [Restablecer aptitudes (versión preliminar)](#reset-skills))

Las API de restablecimiento se usan para actualizar el contenido almacenado en caché (aplicable en escenarios de [enriquecimiento con IA](cognitive-search-concept-intro.md)) o para borrar la marca de límite superior y volver a generar el índice.

El restablecimiento, seguido de la ejecución, puede volver a procesar documentos nuevos y ya existentes, pero no quita los documentos de búsqueda huérfanos del índice de búsqueda que se crearon en ejecuciones anteriores. Para obtener más información sobre la eliminación, consulte [Adición, actualización o eliminación de documentos](/rest/api/searchservice/addupdate-or-delete-documents).

## <a name="run-indexers"></a>Ejecución de indizadores

[Crear indizador](/rest/api/searchservice/create-indexer) crea y ejecuta el indizador a menos que lo cree en un estado deshabilitado ("disabled": true). La primera ejecución tarda un poco más, ya que también cubre la creación de objetos.

[Ejecutar indizador](/rest/api/searchservice/run-indexer) detectará y procesará solo aquello que sea necesario para sincronizar el índice de búsqueda con el origen de datos. Blob Storage tiene una detección de cambios integrada. Otros orígenes de datos, como Azure SQL o Cosmos DB, deben configurarse para la detección de cambios antes de que el indizador pueda leer solo las filas nuevas y actualizadas.

Puede ejecutar un indizador con cualquiera de estos enfoques:

+ Azure Portal, mediante el comando **Ejecutar** de la página del indizador
+ [Ejecutar indizador (REST)](/rest/api/searchservice/run-indexer)
+ [Método RunIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.runindexer) en el SDK de Azure para .NET (o usando el método equivalente RunIndexer en otro SDK)

La ejecución del indizador está sujeta a los límites siguientes:

+ El número máximo de trabajos de indizador es 1 por réplica sin trabajos simultáneos.

  Si la ejecución del indizador ya ha alcanzado su capacidad máxima, recibirá esta notificación: "Failed to run indexer '<indexer-name>' (No se pudo ejecutar el indizador '<indexer-name>'), error: "Another indexer invocation is currently in progress; concurrent invocations are not allowed" (Otra invocación del indizador está actualmente en curso; no están permitidas las invocaciones simultáneas).

+ El tiempo máximo de ejecución es de 2 horas si se usa un conjunto de aptitudes y 24 horas si no. 

  Puede extender el procesamiento colocando el indizador según una programación. El nivel Gratis tiene límites de tiempo de ejecución inferiores. Para obtener la lista completa, consulte los [límites del indizador](search-limits-quotas-capacity.md#indexer-limits)

<a name="reset-indexers"></a>

## <a name="reset-an-indexer"></a>Restablecimiento de un indizador

El restablecimiento de un indizador abarca todo. En el índice de búsqueda, cualquier documento de búsqueda rellenado originalmente por el indizador se marca para su procesamiento completo. Cualquier documento nuevo encontrado en el origen subyacente se agregará al índice como documento de búsqueda. Si el indizador está configurado para usar un conjunto de aptitudes y [almacenamiento en caché](search-howto-incremental-index.md), el conjunto de aptitudes se vuelve a ejecutar y se actualiza la memoria caché.

Puede restablecer un indizador con cualquiera de estos enfoques, seguido de un indizador ejecutado mediante uno de los métodos descritos anteriormente.

+ Azure Portal, mediante el comando **Restablecer** de la página del indizador
+ [Retablecer indizador (REST)](/rest/api/searchservice/reset-indexer)
+ [Método ResetIndexers](/dotnet/api/azure.search.documents.indexes.searchindexerclient.resetindexer) en el SDK de Azure para .NET (o usando el método equivalente RunIndexer en otro SDK)

Una vez finalizada la ejecución, se borra una marca de restablecimiento. Cualquier lógica de detección de cambios normal que funcione para el origen de datos se reanudará en la siguiente ejecución, seleccionándose otros valores nuevos o actualizados en el resto del conjunto de datos.

> [!NOTE]
> Una solicitud de restablecimiento determina lo que se vuelve a procesar (indizador, aptitud o documento), pero no afecta de otra manera al comportamiento en tiempo de ejecución del indizador. Si el indizador tiene parámetros de tiempo de ejecución, asignaciones de campos, almacenamiento en caché, opciones de lote, etc., todos esos valores de configuración se activarán cuando ejecute un indizador después de su restablecimiento.

<a name="reset-skills"></a>

## <a name="reset-skills-preview"></a>Restablecer aptitudes (versión preliminar)

> [!IMPORTANT] 
> [Restablecer aptitudes](/rest/api/searchservice/preview-api/reset-skills) está en versión preliminar pública en los [términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). La [API REST de versión preliminar](/rest/api/searchservice/index-preview) admite esta característica.

En el caso de los indizadores que tienen conjuntos de aptitudes, puede restablecer aptitudes específicas para forzar el procesamiento de esa aptitud y cualquier aptitud de nivel inferior que dependa de su salida. Los [enriquecimientos en caché](search-howto-incremental-index.md) también se actualizan. Al restablecer aptitudes se invalidan los resultados de las aptitudes en caché, lo que resulta útil cuando se implementa una nueva versión de una aptitud y se desea que el indizador vuelva a ejecutar esa aptitud para todos los documentos. 

[Restablecer aptitudes](/rest/api/searchservice/preview-api/reset-skills) está disponible a través de REST **`api-version=2020-06-30-Preview`** .

```http
POST https://[service name].search.windows.net/skillsets/[skillset name]/resetskills?api-version=2020-06-30-Preview
{
    "skillNames" : [
        "#1",
        "#5",
        "#6"
    ]
}
```

Puede especificar aptitudes individuales, como se indica en el ejemplo anterior, pero si alguna de esas aptitudes requiere la salida de aptitudes que no están en la lista (n.º 2 a n.º 4), se ejecutarán estas últimas a menos que la memoria caché pueda proporcionar la información necesaria. Para que esto sea cierto, los enriquecimientos en caché para las aptitudes comprendidas entre la n.º 2 y la n.º 4 no deben depender de la n.º 1 (incluidas en la lista para el restablecimiento).

Si no se especifica ninguna aptitud, se ejecutará todo el conjunto de aptitudes y, si el almacenamiento en caché está habilitado, también se actualizará la memoria caché.

<a name="reset-docs"></a>

## <a name="reset-docs-preview"></a>Restablecimiento de documentos (versión preliminar)

> [!IMPORTANT] 
> [Restablecimiento de documentos](/rest/api/searchservice/preview-api/reset-documents) está en versión preliminar pública, disponible solo a través de la API de REST en versión preliminar. Las características en vista previa (GB) se ofrecen tal cual, en [Términos de uso complementarios](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

La [API de restablecimiento de documentos](/rest/api/searchservice/preview-api/reset-documents) acepta una lista de claves de documento para que pueda actualizar documentos específicos. Si se especifica, los parámetros de restablecimiento se convierten en el único factor determinante de lo que se procesa, independientemente de otros cambios en los datos subyacentes. Por ejemplo, si se agregaron o actualizaron 20 blobs desde la última vez que se ejecutó el indizador, pero solo se restablece un documento, solo se procesará dicho documento.

En cada documento, todos los campos de ese documento de búsqueda se actualizan con los valores del origen de datos. No se pueden elegir los campos que se van a actualizar. 

Si el documento se enriquece a través de un conjunto de aptitudes y tiene datos almacenados en caché, el conjunto de aptitudes se invocará solo para los documentos especificados y los datos en caché se actualizarán para los documentos que se han vuelto a procesar.

Al probar esta API por primera vez, las siguientes API le ayudarán a validar y probar los comportamientos:

+ [Obtención del estado del indizador](/rest/api/searchservice/get-indexer-status) con la versión de API `2020-06-30-Preview`, para comprobar los estados del restablecimiento y la ejecución. Encontrará información sobre la solicitud de restablecimiento al final de la respuesta de estado.
+ [Restablecimiento de documentos](/rest/api/searchservice/preview-api/reset-documents) con la versión de API `2020-06-30-Preview`, para especificar los documentos que deben procesarse.
+ [Ejecutar indizador](/rest/api/searchservice/run-indexer), para ejecutar el indizador (cualquier versión de API).
+ [Buscar en documentos](/rest/api/searchservice/search-documents), para comprobar si hay valores actualizados y también para devolver las claves de documento si no está seguro del valor. Use `"select": "<field names>"` si desea limitar los campos que aparecerán en la respuesta.

### <a name="formulate-and-send-the-reset-request"></a>Formulación y envío de la solicitud de restablecimiento

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "1001",
        "4452"
    ]
}
```

Las claves de documento proporcionadas en la solicitud son valores del índice de búsqueda, que pueden ser diferentes de los campos correspondientes del origen de datos. Si no está seguro del valor de clave, [envíe una consulta](search-query-create.md) para devolver el valor. Puede usar `select` para devolver solo el campo de clave de documento.

En el caso de los blobs que se analizan en varios documentos de búsqueda (por ejemplo, si usó [jsonLines o jsonArrays](search-howto-index-json-blobs.md) o [delimitedText](search-howto-index-csv-blobs.md)) como modo de análisis, el indizador genera la clave de documento y es posible que no la conozca. En esta situación, una consulta de la clave de documento será fundamental para proporcionar el valor correcto.

La llamada a la API varias veces con diferentes claves anexa las nuevas claves a la lista de claves de documento restablecidas. La llamada a la API con el parámetro **`overwrite`** establecido en true sobrescribirá la lista actual de claves de documento que se van a restablecer con la carga útil de la solicitud:

```http
POST https://[service name].search.windows.net/indexers/[indexer name]/resetdocs?api-version=2020-06-30-Preview
{
    "documentKeys" : [
        "200",
        "630"
    ],
    "overwrite": true
}
```

## <a name="check-reset-status"></a>Comprobación del estado de restablecimiento

Para comprobar el estado de un restablecimiento y ver qué claves de documento se ponen en cola para su procesamiento, use [Obtención del estado del indizador](/rest/api/searchservice/get-indexer-status) con **`api-version=06-30-2020-Preview`** . La API de vista previa devolverá la sección **`currentState`** , que encontrará al final de la respuesta de Obtención del estado del indexador.

El "modo" será **`indexingAllDocs`** en Restablecer aptitudes (dado que posiblemente se vean afectados todos los documentos, en los campos que se rellenan a través del enriquecimiento con IA).

En Restablecimiento de documentos, el modo se establece en **`indexingResetDocs`** . El indizador conserva este estado hasta que se procesan todas las claves de documento proporcionadas en la llamada de restablecimiento de documentos y no se ejecutará ningún otro trabajo de indizador mientras la operación progresa. Para buscar todos los documentos en la lista de claves de documento, es necesario descifrar cada documento para buscar y coincidir en la clave, lo que puede tardar unos minutos si el conjunto de datos es grande. Si un contenedor de blobs contiene centenares de blobs y los documentos que desea restablecer están al final, el indizador no encontrará los blobs coincidentes hasta que todos los demás se hayan comprobado primero.

```json
"currentState": {
    "mode": "indexingResetDocs",
    "allDocsInitialTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "allDocsFinalTrackingState": "{\"LastFullEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"LastAttemptedEnumerationStartTime\":\"2021-02-06T19:02:07.0323764+00:00\",\"NameHighWaterMark\":null}",
    "resetDocsInitialTrackingState": null,
    "resetDocsFinalTrackingState": null,
    "resetDocumentKeys": [
        "200",
        "630"
    ]
}
```

Una vez que se vuelvan a procesar los documentos, el indizador volverá al modo **`indexingAllDocs`** y procesará cualquier otro documento nuevo o actualizado en la siguiente ejecución.

## <a name="next-steps"></a>Pasos siguientes

Las API de restablecimiento se usan para determinar el ámbito de la siguiente ejecución del indizador. Para el procesamiento real, deberá invocar una ejecución de indizador a petición o permitir que un trabajo programado complete el trabajo. Una vez finalizada la ejecución, el indizador vuelve al procesamiento normal, ya sea según una programación o un procesamiento a petición.

Después de restablecer y volver a ejecutar los trabajos de indizador, puede supervisar el estado del servicio de búsqueda u obtener información detallada a través del registro de diagnóstico.

+ [Operaciones de indizador (REST)](/rest/api/searchservice/indexer-operations)
+ [Supervisión del estado del indexador de búsqueda](search-howto-monitor-indexers.md)
+ [Recopilación y análisis de datos de registro](search-monitor-logs.md)
+ [Programación de un indexador](search-howto-schedule-indexers.md)