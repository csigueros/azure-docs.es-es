---
title: 'Inicio rápido: Creación de un almacén de conocimiento en Azure Portal'
titleSuffix: Azure Cognitive Search
description: Use el Asistente para la importación de datos a fin de crear un almacén de información utilizado para conservar el contenido enriquecido. Conéctese a un almacén de información para el análisis desde otras aplicaciones o envíe contenido enriquecido a los procesos de nivel inferior.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: quickstart
ms.date: 10/28/2021
ms.openlocfilehash: 6e9c09cb5407747c325b696570cd3eabdeeb4ed7
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131563727"
---
# <a name="quickstart-create-a-knowledge-store-in-the-azure-portal"></a>Inicio rápido: Creación de un almacén de conocimiento en Azure Portal

El [almacén de conocimiento](knowledge-store-concept-intro.md) es una característica de Azure Cognitive Search que acepta la salida de una [canalización de enriquecimiento con IA](cognitive-search-concept-intro.md) y hace que esté disponible en Azure Storage para las aplicaciones y cargas de trabajo de nivel inferior. Los enriquecimientos creados por la canalización, como texto traducido, texto OCR, imágenes etiquetadas y entidades reconocidas, se proyectan en tablas o blobs, a los que cualquier aplicación o carga de trabajo que se conecte a Azure Storage puede acceder.

En este artículo de inicio rápido, configurará los datos y, luego, ejecutará el Asistente para **importar datos** para crear una canalización de enriquecimiento que también genere un almacén de conocimiento. El almacén de conocimiento contendrá contenido de texto original que se extrajo del origen (reseñas de clientes sobre un hotel), además de contenido generado por IA que incluye una etiqueta de opinión, la extracción de frases clave, y la traducción de texto de comentarios de clientes que no están en inglés.

> [!NOTE]
> Este inicio rápido le indica el camino más corto hacia un almacén de conocimiento terminado en Azure Storage. Para obtener explicaciones más detalladas de cada paso, consulte [Creación de un almacén de conocimiento mediante REST](knowledge-store-create-rest.md) en su lugar.

## <a name="prerequisites"></a>Requisitos previos

En este artículo de inicio rápido se usan los servicios siguientes:

+ Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/).

+ Azure Cognitive Search. [Cree un servicio](search-create-service-portal.md) o [busque uno existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) en su cuenta. Puede usar un servicio gratuito para este inicio rápido. 

+ Azure Storage. [Cree una cuenta](../storage/common/storage-account-create.md) o [busque una cuenta ya existente](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/). El tipo de cuenta debe ser **StorageV2 (uso general V2)** .

+ Datos de ejemplo. En este inicio rápido se usan las reseñas del hotel guardadas en un archivo CSV (procede de Kaggle.com) y contiene 19 fragmentos de comentarios de clientes sobre un solo hotel.

  [Descargue HotelReviews_Free.csv](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D) y [cárguelo en un contenedor de blobs](../storage/blobs/storage-quickstart-blobs-portal.md) en Azure Storage.

En este inicio rápido también se usa [Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) para el enriquecimiento con IA. Dado que la carga de trabajo es tan pequeña, Cognitive Services se aprovecha en segundo plano del procesamiento gratuito de hasta 20 transacciones. Esto significa que puede completar este ejercicio sin tener que crear un recurso de Cognitive Services adicional.

## <a name="start-the-wizard"></a>Inicio del asistente

1. Inicie sesión en [Azure Portal](https://portal.azure.com/) con su cuenta de Azure.

1. [Busque su servicio de búsqueda](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) y, en la página de información general, haga clic en **Importar datos** en la barra de comandos para crear un almacén de información en cuatro pasos.

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="Captura de pantalla del comando para importar datos" border="true":::

### <a name="step-1-create-a-data-source"></a>Paso 1: Creación de un origen de datos

Dado que los datos son varias filas en un archivo CSV, establezca el *modo de análisis* para obtener un documento de búsqueda para cada fila.

1. En **Conectarse a los datos propios**, elija **Azure Blob Storage** y seleccione la cuenta y el contenedor que creó. 

1. En **Nombre**, escriba "hotel-reviews-ds".

1. En **Modo de análisis**, seleccione **Texto delimitado** y, a continuación, active la casilla **La primera línea contiene encabezado**. Asegúrese de que el **Carácter delimitador** es una coma (,).

1. En **Cadena de conexión**, pegue la cadena de conexión en la cuenta de Azure Storage. 

   Una cadena de conexión tiene el formato siguiente: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. En **Contenedores**, escriba el nombre del contenedor de blobs que contiene los datos ("hotel-reviews").

    La página debe tener un aspecto similar a la siguiente captura de pantalla.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ds.png" alt-text="Captura de pantalla de la definición del origen de datos" border="true":::

1. Continúe en la siguiente página.

### <a name="step-2-add-skills"></a>Paso 2: Adición de aptitudes

En este paso del asistente, agregue aptitudes para el enriquecimiento con IA. Los datos de origen se componen de reseñas de clientes en inglés y francés. Las aptitudes pertinentes para este conjunto de datos incluyen la extracción de frases clave, la detección de opiniones y la traducción de texto. En un paso posterior, estos enriquecimientos se "proyectarán" en un almacén de conocimiento como tablas de Azure.

1. Expanda **Adjuntar Cognitive Services**. De forma predeterminada, está seleccionada la opción **Gratis (enriquecimientos limitados)** . Puede usar este recurso porque el número de registros de HotelReviews-Free.csv es 19, y este recurso gratuito permite hasta 20 transacciones al día.

1. Expanda **Agregar enriquecimientos**.

1. En **Nombre del conjunto de aptitudes**, escriba "hotel-reviews-ss".

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

1. Seleccione **Elegir una conexión existente** y, después, seleccione una cuenta de Azure Storage. Aparecerá la página Contenedores para que pueda crear un contenedor para las proyecciones. Se recomienda adoptar una convención de nomenclatura de prefijos, como "kstore-hotel-reviews" para distinguir entre el contenido de origen y el contenido del almacén de conocimiento.

1. Al volver al Asistente para la importación de datos, seleccione las siguientes **proyecciones de tabla de Azure**. El asistente siempre ofrece la proyección **Documentos**. Se ofrecerán otras proyecciones en función de las aptitudes que seleccione (como **Frases clave**) o de la granularidad de enriquecimiento (**Páginas**):

    + **Documentos**
    + **Páginas**
    + **Frases clave**

   En la captura de pantalla siguiente se muestran las selecciones de proyecciones de tablas del asistente.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-ks.png" alt-text="Captura de pantalla de la definición del almacén de conocimiento" border="true":::

1. Continúe en la siguiente página.

### <a name="step-3-configure-the-index"></a>Paso 3: Configuración del índice

En este paso del asistente, configure un índice para las consultas de búsqueda de texto completo opcionales. El asistente examinará el origen de datos para deducir los campos y los tipos de datos. Solo tiene que seleccionar los atributos para el comportamiento deseado. Por ejemplo, el atributo **Retrievable** permitirá que el servicio de búsqueda devuelva un valor de campo, mientras que **Searchable** habilitará la búsqueda de texto completo en el campo.

1. En **Nombre del índice**, escriba "hotel-reviews-idx".

1. En el caso de los atributos, acepte las selecciones predeterminadas: **Retrievable** (Recuperable) y **Searchable** (Permite búsquedas) para los nuevos campos que crea la canalización.

    El índice debe tener un aspecto similar al de la siguiente imagen. Dado que la lista es larga, no todos los campos están visibles en la imagen.

   :::image type="content" source="media/knowledge-store-create-portal/hotel-reviews-idx.png" alt-text="Captura de pantalla de la definición del índice" border="true":::

1. Continúe en la siguiente página.

### <a name="step-4-configure-and-run-the-indexer"></a>Paso 4: Configuración y ejecución del indexador

En este paso del asistente, configure un indexador que reunirá el origen de datos, las aptitudes y el índice que definió en los pasos anteriores del asistente.

1. En **Nombre**, escriba "hotel-reviews-idxr".

1. En **Programación**, mantenga el valor predeterminado **Una vez**.

1. Seleccione **Enviar** para ejecutar el indexador. La extracción de datos, la indexación y la aplicación de aptitudes cognitivas se producen en este paso.

### <a name="step-5-check-status"></a>Paso 5: Comprobación del estado

En la página **Información general**, abra la pestaña **Indexadores** en el medio de la página y, luego, seleccione **hotels-reviews-idxr**. En un minuto o dos, el estado debería avanzar de "En curso" a "Correcto" sin errores ni advertencias.

## <a name="check-tables-in-storage-browser"></a>Comprobación de las tablas del explorador de almacenamiento

En Azure Portal, cambie a la cuenta de Azure Storage y use el **explorador de almacenamiento** para ver las nuevas tablas. Debería ver tres tablas, una para cada proyección que se ofrecía en la sección "Guardar enriquecimientos" de la página "Agregar enriquecimientos".

+ "hotelReviewssDocuments" contiene todos los nodos de primer nivel del árbol de enriquecimiento de un documento que no son colecciones. 

+ "hotelReviewssKeyPhrases" contiene una larga lista con solo las frases clave extraídas de todas las reseñas. La salida de las aptitudes que emiten colecciones (matrices), como frases clave y entidades, se obtendrá en una tabla independiente.

+ "hotelReviewssPages" contiene campos enriquecidos creados en cada página que se ha dividido del documento. En este conjunto de aptitudes y origen de datos, los enriquecimientos en el nivel de página constan de etiquetas de opinión y texto traducido. Se crea una tabla de páginas (o una tabla de oraciones si especifica ese nivel concreto de granularidad) al elegir la granularidad de "páginas" en la definición del conjunto de aptitudes. 

Todas estas tablas contienen columnas de identificador para admitir relaciones de tabla en otras herramientas y aplicaciones. Al abrir una tabla, desplácese más allá de estos campos para ver los campos de contenido que ha agregado la canalización.

En este inicio rápido, la tabla de "hotelReviewssPages" debe ser similar a la captura de pantalla siguiente:

   :::image type="content" source="media/knowledge-store-create-portal/azure-table-hotel-reviews.png" alt-text="Captura de pantalla de las tablas generadas en el explorador de almacenamiento" border="true":::

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
