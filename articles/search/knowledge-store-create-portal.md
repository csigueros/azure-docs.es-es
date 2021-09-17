---
title: 'Inicio rápido: Creación de un almacén de conocimiento en Azure Portal'
titleSuffix: Azure Cognitive Search
description: Use el Asistente para la importación de datos a fin de crear un almacén de información utilizado para conservar el contenido enriquecido. Conéctese a un almacén de información para el análisis desde otras aplicaciones o envíe contenido enriquecido a los procesos de nivel inferior.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 09/02/2021
ms.openlocfilehash: 4e23862e78fb6b3de9dd360ee54bb229c76bc8b4
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/07/2021
ms.locfileid: "123537986"
---
# <a name="quickstart-create-a-knowledge-store-in-the-azure-portal"></a>Inicio rápido: Creación de un almacén de conocimiento en Azure Portal

El almacén de conocimiento es una característica de Azure Cognitive Search que envía la salida de una [canalización de enriquecimiento con IA](cognitive-search-concept-intro.md) a Azure Storage para el análisis subsiguiente o el procesamiento posterior.

Una canalización de enriquecimiento acepta contenido de imágenes y texto no estructurado, aplica procesamiento con tecnología de IA mediante Cognitive Services, y genera nuevas estructuras e información que no existían anteriormente. Una de las estructuras de datos físicas creadas por una canalización es un [almacén de conocimiento](knowledge-store-concept-intro.md), al que se puede acceder mediante cualquier herramienta, aplicación o proceso que se conecten a Azure Storage.

En este artículo de inicio rápido, configurará los datos y, luego, ejecutará el Asistente para **importar datos** para crear una canalización de enriquecimiento que también genere un almacén de conocimiento. El almacén de conocimiento contendrá contenido de texto original que se extrajo del origen, además de contenido generado por IA que incluye una etiqueta de opinión, la extracción de frases clave, y la traducción de texto de comentarios de clientes que no están en inglés.

> [!NOTE]
> Este artículo de inicio rápido es la ruta más rápida hacia un almacén de conocimiento terminado en Azure Storage. Para una cobertura más detallada, consulte [Creación de un almacén de conocimiento mediante REST](knowledge-store-create-rest.md) en su lugar.

## <a name="prerequisites"></a>Requisitos previos

En este artículo de inicio rápido se usan los servicios siguientes:

+ Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/).

+ Un servicio de Azure Cognitive Search. [Cree un servicio](search-create-service-portal.md) o [busque uno existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) en su cuenta. Puede usar un servicio gratuito para este inicio rápido. 

+ Una cuenta de Azure Storage con [Blob Storage](../storage/blobs/index.yml).

En este inicio rápido también se usa [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) para la inteligencia artificial. Dado que la carga de trabajo es tan pequeña, Cognitive Services se aprovecha en segundo plano del procesamiento gratuito de hasta 20 transacciones. Esto significa que puede completar este ejercicio sin tener que crear un recurso de Cognitive Services adicional.

## <a name="set-up-your-data"></a>Configuración de los datos

En los pasos siguientes, configure un contenedor de blobs en Azure Storage para almacenar archivos de contenido heterogéneo.

1. [Descarga de HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Estos datos son las reseñas del hotel guardadas en un archivo CSV (procede de Kaggle.com) y contiene 19 fragmentos de comentarios de clientes sobre un solo hotel. 

1. [Cree una cuenta de Azure Storage](../storage/common/storage-account-create.md?tabs=azure-portal) o [busque una cuenta existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). Usará Azure Storage tanto para el contenido sin procesar que se va a importar como para el almacén de información que es el resultado final.

   Elija el tipo de cuenta **StorageV2 (uso general V2)** .

1. En el recurso de Azure Storage, use el **Explorador de Storage** para crear un contenedor de blobs denominado **hotel-reviews**.

1. Seleccione **Cargar** en la parte superior de la página para cargar el archivo **HotelReviews-Free.csv** que descargó del paso anterior.

   :::image type="content" source="media/knowledge-store-create-portal/blob-container-storage-explorer.png" alt-text="Captura de pantalla del Explorador de Storage con el archivo cargado y el panel de navegación izquierdo" border="true":::

1. Casi ha terminado con este recurso, pero antes de salir de estas páginas, seleccione **Claves de acceso** en el panel de navegación izquierdo para obtener una cadena de conexión para poder recuperar estos datos mediante el indexador.

1. En **Claves de acceso**, seleccione **Mostrar claves** en la parte superior de la página para mostrar las cadenas de conexión y, luego, copie la cadena de conexión para key1 o key2.

   Una cadena de conexión tiene el formato siguiente: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

Ahora ya está preparado para continuar con el Asistente para la **importación de datos**.

## <a name="start-the-wizard"></a>Inicio del asistente

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con su cuenta de Azure.

1. [Busque su servicio de búsqueda](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) y, en la página de información general, haga clic en **Importar datos** en la barra de comandos para crear un almacén de información en cuatro pasos.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Captura de pantalla del comando para importar datos" border="true":::

### <a name="step-1-create-a-data-source"></a>Paso 1: Creación de un origen de datos

Dado que los datos son varias filas en un archivo CSV, establezca el *modo de análisis* para obtener un documento de búsqueda para cada fila.

1. En **Conectarse a los datos propios**, elija **Azure Blob Storage** y seleccione la cuenta y el contenedor que creó. 

1. En **Nombre**, escriba `hotel-reviews-ds`.

1. En **Modo de análisis**, seleccione **Texto delimitado** y, a continuación, active la casilla **La primera línea contiene encabezado**. Asegúrese de que el **Carácter delimitador** es una coma (,).

1. En **Cadena de conexión**, pegue la cadena de conexión que copió de Azure Storage.

1. En **Contenedores**, escriba el nombre del contenedor de blobs que contiene los datos (`hotel-reviews`).

    La página debe tener un aspecto similar a la siguiente captura de pantalla.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ds.png" alt-text="Captura de pantalla de la definición del origen de datos" border="true":::

1. Continúe en la siguiente página.

### <a name="step-2-add-skills"></a>Paso 2: Adición de aptitudes

En este paso del asistente, agregue aptitudes para el enriquecimiento con IA. Los datos de origen se componen de reseñas de clientes en inglés y francés. Las aptitudes pertinentes para este conjunto de datos incluyen la extracción de frases clave, la detección de opiniones y la traducción de texto. En un paso posterior, estos enriquecimientos se "proyectarán" en un almacén de conocimiento como tablas de Azure.

1. Expanda **Adjuntar Cognitive Services**. De forma predeterminada, está seleccionada la opción **Gratis (enriquecimientos limitados)** . Puede usar este recurso porque el número de registros de HotelReviews-Free.csv es 19, y este recurso gratuito permite hasta 20 transacciones al día.

1. Expanda **Agregar enriquecimientos**.

1. En **Nombre del conjunto de aptitudes**, escriba `hotel-reviews-ss`.

1. En el campo **Datos de origen**, seleccione **reviews_text**.

1. En **Nivel de granularidad de enriquecimiento**, seleccione **Páginas (fragmentos de 5000 caracteres)** .

1. En **Conocimientos cognitivos de texto**, seleccione las siguientes aptitudes:

    + **Extracción de frases clave**
    + **Traducir texto**
    + **Detección de idioma**
    + **Detección de opiniones**

   La página debería ser similar a la siguiente captura de pantalla:

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ss.png" alt-text="Captura de pantalla de la definición del conjunto de aptitudes" border="true":::

1. Desplácese y expanda **Guardar enriquecimientos en el almacén de conocimiento**.

1. Seleccione las **Proyecciones de tabla de Azure** siguientes. El asistente siempre ofrece la proyección **Documentos**. Se ofrecerán otras proyecciones en función de las aptitudes que seleccione (como **Frases clave**) o de la granularidad de enriquecimiento (**Páginas**):

    + **Documentos**
    + **Páginas**
    + **Frases clave**

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ks.png" alt-text="Captura de pantalla de la definición del almacén de conocimiento" border="true":::

1. Escriba la **Cadena de conexión de la cuenta de almacenamiento** que guardó en un paso anterior.

1. Continúe en la siguiente página.

### <a name="step-3-configure-the-index"></a>Paso 3: Configuración del índice

En este paso del asistente, configure un índice para las consultas de búsqueda de texto completo opcionales. El asistente examinará el origen de datos para deducir los campos y los tipos de datos. Solo tiene que seleccionar los atributos para el comportamiento deseado. Por ejemplo, el atributo **Retrievable** permitirá que el servicio de búsqueda devuelva un valor de campo, mientras que **Searchable** habilitará la búsqueda de texto completo en el campo.

1. En **Nombre de índice**, escriba `hotel-reviews-idx`.

1. En el caso de los atributos, acepte las selecciones predeterminadas: **Retrievable** (Recuperable) y **Searchable** (Permite búsquedas) para los nuevos campos que crea la canalización.

    El índice debe tener un aspecto similar al de la siguiente imagen. Dado que la lista es larga, no todos los campos están visibles en la imagen.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-idx.png" alt-text="Captura de pantalla de la definición del índice" border="true":::

1. Continúe en la siguiente página.

### <a name="step-4-configure-and-run-the-indexer"></a>Paso 4: Configuración y ejecución del indexador

En este paso del asistente, configure un indexador que reunirá el origen de datos, las aptitudes y el índice que definió en los pasos anteriores del asistente.

1. En **Nombre**, escriba `hotel-reviews-idxr`.

1. En **Programación**, mantenga el valor predeterminado **Una vez**.

1. Seleccione **Enviar** para ejecutar el indexador. La extracción de datos, la indexación y la aplicación de aptitudes cognitivas se producen en este paso.

## <a name="check-status"></a>Comprobar estado

En la página **Información general**, abra la pestaña **Indexadores** en el medio de la página y, luego, seleccione **hotels-reviews-ixr**. En un minuto o dos, el estado debería avanzar de "En curso" a "Correcto" sin errores ni advertencias.

## <a name="check-tables-in-storage-explorer"></a>Comprobación de las tablas en el Explorador de Storage

En Azure Portal, cambie a la cuenta de Azure Storage y use el **Explorador de Storage** para ver las nuevas tablas. Debería ver tres tablas, una para cada proyección que se ofrecía en la sección "Guardar enriquecimientos" de la página "Agregar enriquecimientos".

+ La tabla de documentos contiene todos los nodos de primer nivel del árbol de enriquecimiento de un documento.

+ Se crea una tabla de páginas (o tabla de oraciones) si se especifica el nivel de granularidad en "páginas" u "oraciones". La salida de las aptitudes que se ejecutan a nivel de páginas u oraciones se proyectará en esta tabla.

+ La salida de las aptitudes que emiten colecciones (matrices), como frases clave y entidades, se obtendrá en una tabla independiente.

Todas las tablas del mismo grupo de proyección contienen información de referencia cruzada para admitir relaciones de tablas en otras herramientas y aplicaciones.

En este artículo de inicio rápido, la tabla debe ser similar a la captura de pantalla siguiente:

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="Captura de pantalla de las tablas generadas en el Explorador de Storage" border="true":::

Cada tabla se genera con los identificadores necesarios para la vinculación cruzada de las tablas en las consultas. Al abrir una tabla, desplácese más allá de estos campos para ver los campos de contenido que ha agregado la canalización.

| Tabla | Descripción |
|-------|-------------|
| hotelReviewssDocument | Contiene campos que trasladan desde el archivo CSV, como reviews_date y reviews_text. |
| hotelReviewssPages | Contiene campos enriquecidos creados por el conjunto de aptitudes, como la etiqueta de opinión y el texto traducido. |
| hotelReviewssKeyPhrases | Contiene una larga lista con solo las frases clave. |

## <a name="clean-up"></a>Limpieza

Al trabajar en una suscripción propia, se recomienda identificar al final de un proyecto si aún necesita los recursos creados. Los recursos que se dejan en ejecución pueden costarle mucho dinero. Puede eliminar los recursos de forma individual o eliminar el grupo de recursos para eliminar todo el conjunto de recursos.

Puede encontrar y administrar recursos en el portal, mediante el vínculo **Todos los recursos** o **Grupos de recursos** en el panel de navegación izquierdo.

Si está usando un servicio gratuito, recuerde que está limitado a tres índices, indexadores y orígenes de datos. Puede eliminar elementos individuales en el portal para mantenerse por debajo del límite.

> [!TIP]
> Si desea repetir este ejercicio o realizar otro tutorial de enriquecimiento de inteligencia artificial, elimine el indexador **hotel-reviews-idxr** y los objetos relacionados para volver a crearlos. La eliminación del indexador restablece el contador de transacciones diarias gratis a cero.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha visto una introducción a un almacén de conocimiento, puede echar un vistazo más en profundidad a cada paso; para ello, cambie al tutorial de la API REST. Las tareas que el asistente controla internamente se explican en el tutorial de REST.

> [!div class="nextstepaction"]
> [Creación de un almacén de conocimiento mediante REST y Postman](knowledge-store-create-rest.md)
