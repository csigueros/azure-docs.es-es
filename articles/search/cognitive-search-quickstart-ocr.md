---
title: 'Inicio rápido: OCR y análisis de imágenes'
titleSuffix: Azure Cognitive Search
description: Use el Asistente para la importación de datos y las aptitudes cognitivas de inteligencia artificial para aplicar OCR y análisis de imágenes a fin de identificar y extraer de los archivos de imagen textos que permitan búsquedas.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/07/2021
ms.openlocfilehash: 68fc76ab80320a41cd9c9f0884dec8dc7771bccc
ms.sourcegitcommit: 4abfec23f50a164ab4dd9db446eb778b61e22578
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130065281"
---
# <a name="quickstart-apply-ocr-and-image-analysis-using-the-import-data-wizard"></a>Inicio rápido: Aplicación de OCR y análisis de imágenes mediante el Asistente para importación de datos

Más información sobre cómo el enriquecimiento con inteligencia artificial en Azure Cognitive Search agrega el reconocimiento óptico de caracteres (OCR) y el análisis de imágenes para crear contenido que permita búsquedas a partir de archivos de imagen. 

En este inicio rápido, ejecutará el Asistente para la **importación de datos** a fin de analizar el contenido visual en archivos JPG. El contenido incluye fotografías de señales. La salida es un índice que permite búsquedas y contiene leyendas, etiquetas y texto identificados mediante OCR, consultables en el portal con el [Explorador de búsqueda](search-explorer.md). 

Para prepararse, creará algunos recursos y cargará archivos de ejemplo antes de ejecutar el asistente.

¿Prefiere empezar con el código? Pruebe en su lugar el [tutorial de .NET](cognitive-search-tutorial-blob-dotnet.md), el [tutorial de Python](cognitive-search-tutorial-blob-python.md) o el [tutorial de REST](cognitive-search-tutorial-blob-dotnet.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, tiene que cumplir los siguientes requisitos previos:

+ Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/).

+ Servicio Azure Cognitive Search. [Cree un servicio](search-create-service-portal.md) o [busque uno existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) en su suscripción actual. Puede usar un servicio gratuito para este inicio rápido. 

+ Una cuenta de Azure Storage con Blob Storage. [Cree una cuenta de almacenamiento](../storage/common/storage-account-create.md?tabs=azure-portal) o [busque una cuenta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). 

  + Elija la misma suscripción si desea que el asistente busque la cuenta de almacenamiento y configure la conexión.
  + Elija la misma región de Azure Cognitive Search para evitar cargos de ancho de banda.
  + Elija el tipo de cuenta StorageV2 (de uso general V2).

> [!NOTE]
> En este inicio rápido también se usa [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) para la inteligencia artificial. Dado que la carga de trabajo es tan pequeña, Cognitive Services se aprovecha en segundo plano del procesamiento gratuito de hasta 20 transacciones. Esto significa que puede completar este ejercicio sin tener que crear un recurso de Cognitive Services adicional.

## <a name="set-up-your-data"></a>Configuración de los datos

En los pasos siguientes, configure un contenedor de blobs en Azure Storage para almacenar archivos de contenido heterogéneo.

1. [Descargue los datos de ejemplo](https://github.com/Azure-Samples/azure-search-sample-data) de GitHub. Hay varios conjuntos de datos. Use los archivos de la carpeta **unsplash-images\jpg-signs** para este inicio rápido.

1. Carga de datos de ejemplo en un contenedor de blobs.

   1. Inicie sesión en [Azure Portal](https://portal.azure.com/) y busque la cuenta de almacenamiento.
   1. En el panel de navegación izquierdo, seleccione **Contenedores**.
   1. [Cree un contenedor](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container) denominado "signs" (señales). Use el nivel de acceso público predeterminado.
   1. En el contenedor "signs", seleccione **Cargar** para cargar los archivos desde la carpeta local **unsplash-images\jpg-signs**.

Debería tener 10 archivos que contengan fotografías de señales.

Hay una segunda subcarpeta que incluye edificios de referencia. Si desea [adjuntar una clave de Cognitive Services](cognitive-search-attach-cognitive-services.md), también puede incluir estos archivos para ver cómo funciona el análisis de imágenes en archivos de imagen que no incluyen texto incrustado. La clave es necesaria para los trabajos que superan la asignación gratuita.

Ahora ya está preparado para continuar con el Asistente para la importación de datos.

## <a name="run-the-import-data-wizard"></a>Ejecutar el Asistente para la importación de datos

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con su cuenta de Azure.

1. [Busque su servicio de búsquedas](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) y, en la página de información general, haga clic en **Importar datos** en la barra de comandos para configurar el enriquecimiento cognitivo en cuatro pasos.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Captura de pantalla del comando para importar datos" border="true":::

### <a name="step-1---create-a-data-source"></a>Paso 1: Creación de un origen de datos

1. En **Conectarse a los datos propios**, seleccione **Azure Blob Storage**. Elija una conexión existente a la cuenta de almacenamiento y al contenedor que ha creado. Asigne un nombre al origen de datos y use los valores predeterminados para el resto. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/connect-to-signs.png" alt-text="Configuración de blobs de Azure" border="true":::

### <a name="step-2---add-cognitive-skills"></a>Paso 2: Adición de aptitudes cognitivas

A continuación, configure el enriquecimiento con inteligencia artificial para invocar OCR y el análisis de imágenes. 

1. En este inicio rápido, se usará el recurso **Free** de Cognitive Services. Los datos de ejemplo se componen de 19 archivos, por lo que la cobertura diaria por indexador de 20 transacciones gratis de Cognitive Services es suficiente para este inicio rápido. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/free-enrichments.png" alt-text="Asociación del procesamiento gratuito de Cognitive Services" border="true":::

1. En la misma página, expanda **Agregar enriquecimientos** y realice tres selecciones:

   Habilitar OCR y combinar todo el texto en el campo merged_content.

   Elija "Generar etiquetas a partir de imágenes" y "Generar leyendas a partir de imágenes".

   :::image type="content" source="media/cognitive-search-quickstart-blob/select-ocr-image-enrichments.png" alt-text="Adjuntar determinados servicios de Attach Cognitive Services para el conjunto de aptitudes" border="true":::

   Para el análisis de imágenes, las imágenes se separan del texto durante el descifrado de documentos. El campo "merged_content" vuelve a asociar el texto y las imágenes en la canalización de enriquecimiento con inteligencia artificial.

### <a name="step-3---configure-the-index"></a>Paso 3: Configuración del índice

Un índice incluye el contenido en el que se pueden realizar búsquedas y el Asistente para la **importación de datos** puede inferir normalmente el esquema mediante el muestreo de los datos. En este paso, repase el esquema generado y revise la configuración. A continuación se ofrece el esquema predeterminado que se creó para el conjunto de datos de demostración.

En esta guía de inicio rápido, el asistente realiza un trabajo remarcable a la hora de configurar valores predeterminados razonables:  

+ Los campos predeterminados se basan en las propiedades de los blobs existentes, además de los nuevos campos que contienen la salida de enriquecimiento (por ejemplo, `text`, `layoutText`, `imageCaption`). Los tipos de datos se deducen de los metadatos y del muestreo de datos.

+ La clave de documento predeterminada es *metadata_storage_path* (se ha seleccionado porque el campo contiene valores únicos).

+ Los atributos predeterminados son **Retrievable** (Recuperable) y **Searchable** (Permite búsquedas). El atributo **Searchable** (Permite búsqueda) permite buscar texto completo en un campo. El atributo **Retrievable** (Recuperable) indica que un valor puede aparecer en los resultados. El asistente da por supuesto que desea que estos campos se puedan recuperar y permitan búsquedas porque los creó a través de un conjunto de aptitudes.

  :::image type="content" source="media/cognitive-search-quickstart-blob/index-fields-ocr-images.png" alt-text="Campos de índice" border="true":::

Marcar un campo como **Retrievable** no significa que el campo *debe* esté presente en los resultados de búsqueda. Puede controlar con precisión la composición de los resultados de búsqueda si usa el parámetro de consulta **$select** para especificar qué campos desea incluir. En el caso de campos con mucho texto como `content`, el parámetro **$select** es la solución para moldear resultados de búsqueda fáciles de administrar para los usuarios de la aplicación, mientras se garantiza que el código de cliente tenga acceso a toda la información que necesita a través del atributo **Recuperable**.

### <a name="step-4---configure-the-indexer"></a>Paso 4: Configuración del indexador

El indexador es un recurso de alto nivel que controla el proceso de indexación. Asimismo, especifica el nombre del origen de datos, un índice de destino y la frecuencia de ejecución. El Asistente para la **importación de datos** crea varios objetos y entre ellos hay siempre un indexador que se puede ejecutar repetidamente.

1. En la página **Indizador**, puede aceptar el nombre predeterminado y hacer clic en la opción de programación **Una vez** para ejecutarlo inmediatamente. 

   :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-signs.png" alt-text="Definición del indexador" border="true":::

1. Haga clic en **Enviar** para crear y ejecutar simultáneamente el indexador.

## <a name="monitor-status"></a>Supervisión de estado

La indexación cognitiva de aptitudes tarda más en completarse que la indexación típica basada en texto. Para supervisar el progreso, vaya a la página de información general y seleccione la pestaña **Indexadores** en medio de la página.

  :::image type="content" source="media/cognitive-search-quickstart-blob/indexer-status-signs.png" alt-text="Estado del indexador" border="true":::

Para comprobar los detalles sobre el estado de ejecución, seleccione un indexador de la lista.

## <a name="query-in-search-explorer"></a>Consulta en el Explorador de búsqueda

Después de crear un índice, puede ejecutar consultas para devolver los resultados. En el portal, use el **Explorador de búsqueda** del portal. 

1. En la página del panel del servicio de búsqueda, haga clic en el **Explorador de búsqueda** en la barra de comandos.

1. Seleccione **Cambiar índice** en la parte superior para seleccionar el índice que haya creado.

1. En Cadena de consulta, escriba una cadena de búsqueda para consultar el índice, como `search=sign&searchFields=imageTags&$select=text,imageCaption,imageTags&$count=true` y, a continuación, seleccione **Buscar**.

   :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-query-string-signs.png" alt-text="Cadena de consulta en el Explorador de búsqueda" border="true":::

Los resultados se devuelven en formato JSON, que suele ser detallado y difícil de leer, especialmente si se trata de documentos de gran tamaño que se crean en los blobs de Azure. Algunas sugerencias para buscar en esta herramienta incluyen las siguientes técnicas:

+ Agregue `$select` para especificar qué campos quiere incluir en los resultados de búsqueda.
+ Anexe `searchField` para que la búsqueda de texto completo abarque campos específicos.
+ Use CTRL-F para buscar en el JSON las propiedades o los términos específicos.

  :::image type="content" source="media/cognitive-search-quickstart-blob/search-explorer-results-signs.png" alt-text="Ejemplo del Explorador de búsquedas" border="true":::

Las cadenas de consulta distinguen mayúsculas de minúsculas, por lo que si obtiene un mensaje de "campo desconocido", compruebe los **campos** o la **definición de índice (JSON)** para comprobar el nombre y el caso. 

## <a name="clean-up-resources"></a>Limpieza de recursos

Cuando trabaje con su propia suscripción, es una buena idea al final de un proyecto identificar si todavía se necesitan los recursos que ha creado. Los recursos que se dejan en ejecución pueden costarle mucho dinero. Puede eliminar los recursos de forma individual o eliminar el grupo de recursos para eliminar todo el conjunto de recursos.

Puede encontrar y administrar recursos en el portal, mediante el vínculo **Todos los recursos** o **Grupos de recursos** en el panel de navegación izquierdo.

Si está usando un servicio gratuito, recuerde que está limitado a tres índices, indexadores y orígenes de datos. Puede eliminar elementos individuales en el portal para mantenerse por debajo del límite. 

## <a name="next-steps"></a>Pasos siguientes

Cognitive Search tiene otras aptitudes integradas que se pueden ejercer en el Asistente para la importación de datos. En el siguiente inicio rápido se utiliza el reconocimiento de entidades, la detección del idioma y la traducción de texto. 

> [!div class="nextstepaction"]
> [Inicio rápido: Traducción de texto y reconocimiento de entidades mediante el Asistente para la importación de datos](cognitive-search-quickstart-blob.md)