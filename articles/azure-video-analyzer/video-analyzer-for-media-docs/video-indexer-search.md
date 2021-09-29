---
title: Búsqueda de momentos exactos en vídeos con Azure Video Analyzer for Media (anteriormente, Video Indexer)
description: Obtenga información sobre cómo buscar momentos exactos en vídeos con Azure Video Analyzer for Media (anteriormente, Video Indexer).
ms.topic: how-to
ms.date: 11/23/2019
ms.author: juliako
ms.openlocfilehash: 454c5a8033e30a10183d03a1689b6e606bcb77ba
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128607527"
---
# <a name="search-for-exact-moments-in-videos-with-video-analyzer-for-media"></a>Búsqueda de momentos exactos en vídeos con Video Analyzer for Media

En este tema se muestra cómo usar el sitio web de Azure Video Analyzer for Media (anteriormente, Video Indexer) para buscar momentos exactos en los vídeos.

1. Vaya al sitio web de [Video Analyzer for Media](https://www.videoindexer.ai/) e inicie sesión.
1. Especifique las palabras clave de búsqueda; se realizará la búsqueda entre todos los vídeos de la biblioteca de su cuenta. 

    Para filtrar la búsqueda, seleccione **Filtros**. En el ejemplo siguiente, se busca "Microsoft" que aparece solo como texto en pantalla (OCR).

    :::image type="content" source="./media/video-indexer-search/filter.png" alt-text="Filtro, solo texto":::
1. Presione **Buscar** para ver el resultado.

    :::image type="content" source="./media/video-indexer-search/results.png" alt-text="Resultado de la búsqueda en vídeos":::

    Si se selecciona uno de los resultados, el reproductor le llevará a ese momento exacto en el vídeo.
1. Para ver y buscar las conclusiones resumidas del vídeo, haga clic en **Reproducir** en el vídeo o seleccione uno de los resultados de la búsqueda original. 

    Puede ver, buscar y editar las **conclusiones**. Si selecciona una de las conclusiones, el reproductor le llevará a ese momento exacto en el vídeo.  

    :::image type="content" source="./media/video-indexer-search/insights.png" alt-text="Vista, búsqueda y edición de las conclusiones del vídeo":::

    Si inserta el vídeo a través de widgets de Video Analyzer for Media, puede conseguir la vista del reproductor o las conclusiones y la sincronización de la aplicación. Para más información, consulte [Inserción de widgets de Video Analyzer for Media en las aplicaciones](video-indexer-embed-widgets.md).
1. Para ver, buscar y editar las transcripciones, haga clic en la pestaña **Escala de tiempo**. 

    :::image type="content" source="./media/video-indexer-search/timeline.png" alt-text="Vista, búsqueda y edición de transcripciones del vídeo":::

    Para editar el texto, seleccione **Editar** en la esquina superior derecha y cambie el texto según sea necesario. 

    También puede traducir y descargar las transcripciones seleccionando la opción adecuada en la esquina superior derecha. 

## <a name="embed-download-create-projects"></a>Inserción, descarga y creación de proyectos

Para insertar el vídeo, seleccione **</> Insertar** en el vídeo. Para más información, consulte [Inserción de widgets visuales en la aplicación](video-indexer-embed-widgets.md).

Puede descargar el vídeo de origen, las conclusiones del vídeo o las transcripciones haciendo clic en **Descargar** debajo del vídeo.

Puede crear un clip de líneas y momentos específicos del vídeo haciendo clic en **Abrir en el editor**. Después, edite el vídeo y guarde el proyecto. Para más información, consulte [Uso de las conclusiones detalladas de los vídeos](use-editor-create-project.md).

:::image type="content" source="./media/video-indexer-search/embed-download-create-projects.png" alt-text="Inserción, descarga y creación de proyectos del vídeo":::

## <a name="next-steps"></a>Pasos siguientes

[Procesamiento del contenido con la API REST de Video Analyzer for Media](video-indexer-use-apis.md)
