---
title: Conexión a un almacén de conocimiento con Power BI
titleSuffix: Azure Cognitive Search
description: Conecte un almacén de conocimiento de Azure Cognitive Search con Power BI para su análisis y exploración.
author: HeidiSteen
ms.author: heidist
manager: nitinme
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/18/2021
ms.openlocfilehash: 14a6cd5ec07d9c4c5ee584312e9ba20b2251d0ef
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178310"
---
# <a name="connect-a-knowledge-store-with-power-bi"></a>Conexión a un almacén de conocimiento con Power BI

En este artículo, aprenderá a conectarse a un almacén de conocimiento y a consultarlo mediante Power Query en la aplicación Power BI Desktop. Puede empezar a trabajar más rápido con plantillas o crear un panel personalizado desde cero.

Un almacén de conocimiento que se compone de tablas de Azure Storage funcionan mejor en Power BI. Si las tablas contienen proyecciones del mismo conjunto de aptitudes y grupo de proyección, puede "unirlas" fácilmente para crear visualizaciones de tablas que incluyan campos de tablas relacionadas.

Siga los pasos que se describen en este artículo mediante datos de ejemplo y un almacén de conocimiento como el que se [creó en este inicio rápido del portal](knowledge-store-create-portal.md) o mediante [Postman y API REST](knowledge-store-create-rest.md). 

## <a name="connect-to-azure-storage"></a>Conectar a Azure Storage

1. Inicie [Power BI Desktop](https://powerbi.microsoft.com/downloads/) y seleccione **Obtener datos**. 

1. En la ventana **Obtener datos**, seleccione **Azure** y después **Azure Table Storage**.

1. Seleccione **Conectar**.

1. En **URL o nombre de cuenta**, escriba el nombre de la cuenta de Azure Storage (la dirección URL completa se creará automáticamente).

1. Si se le solicita, escriba la clave de la cuenta de almacenamiento.

## <a name="set-up-tables"></a>Configuración de las tablas

1. Seleccione la casilla situada junto a todas las tablas que se crearon a partir del mismo conjunto de aptitudes y, a continuación, seleccione **Cargar**.

   ![Carga de tablas](media/knowledge-store-connect-power-bi/power-bi-load-tables.png "Carga de tablas")

1. En la cinta de opciones superior, seleccione **Transformar datos** para abrir el **Editor de Power Query**.

   ![Abrir Power Query](media/knowledge-store-connect-power-bi/powerbi-edit-queries.png "Abrir Power Query")

1. Abra *hotelReviewsDocument* y quite las columnas *PartitionKey*, *RowKey* y *Timestamp*. Esas columnas se usan para las relaciones de tabla en Azure Table Storage. Power BI no las necesita. Debería quedar una columna denominada "Content" con el texto *Record* en cada fila. 

   ![Editar tablas](media/knowledge-store-connect-power-bi/powerbi-edit-table.png "Editar tablas")

1. Haga clic en el icono con flechas opuestas en el lado superior derecho de la tabla para expandir el *contenido*. Cuando aparezca la lista de columnas, selecciónelas todas. Borre las columnas que empiezan por "metadata". Haga clic en **Aceptar** para incluir las columnas seleccionadas.

   ![Expandir contenido](media/knowledge-store-connect-power-bi/powerbi-expand-content-table.png "Expandir contenido")

1. Para cambiar el tipo de datos de las columnas siguientes, haga clic en el icono ABC-123 situado en la parte superior izquierda de la columna.

   + Para *content.latitude* y *Content.longitude*, seleccione **Número decimal**.
   + En *Content.reviews_date* y *Content.reviews_dateAdded*, seleccione **Fecha y hora**.

   ![Cambiar tipos de datos](media/knowledge-store-connect-power-bi/powerbi-change-type.png "Cambiar tipos de datos")

1. Abra *hotelReviewsSsPages* y repita los pasos de eliminación de columnas. Para seleccionar columnas para los registros, expanda la columna *Content*. No hay modificaciones de tipo de datos para esta tabla.

1. Abra *hotelReviewsSsKeyPhrases* y repita los pasos de eliminación de columnas. Para seleccionar columnas para los registros, expanda la columna *Content*. No hay modificaciones de tipo de datos para esta tabla.

1. En la barra de comandos, haga clic en **Cerrar y aplicar**.

## <a name="check-table-relationships"></a>Comprobación de las relaciones de tablas

1. Haga clic en el icono Modelo en el panel de navegación izquierdo y compruebe que Power BI muestra relaciones entre las tres tablas.

   ![Validar relaciones](media/knowledge-store-connect-power-bi/powerbi-relationships.png "Validar relaciones")

1. Haga doble clic en cada relación y asegúrese de que la **dirección de filtro cruzado** está establecida en **Ambas**.  Esto permite que los objetos visuales se actualicen cuando se aplica un filtro.

## <a name="build-a-report"></a>Generación de un informe

1. Haga clic en el icono de informe en el panel de navegación izquierdo para explorar los datos mediante visualizaciones. En el caso de los campos de texto, las tablas y las tarjetas son visualizaciones útiles.

1. Elija campos de cada una de las tres tablas para rellenar la tabla o tarjeta.

   ![Compilación de un informe de tabla](media/knowledge-store-connect-power-bi/power-bi-table-report.png "Compilación de un informe de tabla")

## <a name="sample-power-bi-template---azure-portal-only"></a>Plantilla de ejemplo de Power BI: solo Azure Portal

Al crear un [almacén de conocimiento con Azure Portal](knowledge-store-create-portal.md), tiene la opción de descargar una [plantilla de Power BI](https://github.com/Azure-Samples/cognitive-search-templates) en la segunda página del **Asistente para la importación de datos**. Esta plantilla proporciona varias visualizaciones, como WordCloud y Network Navigator, para el contenido basado en texto. 

Haga clic en **Obtener plantilla de Power BI** en la página **Agregar conocimientos cognitivos** para recuperar y descargar la plantilla desde su ubicación pública de GitHub. El asistente modifica la plantilla para acomodar la forma de los datos capturados en las proyecciones del almacén de conocimiento especificadas en dicho asistente. Por esta razón, la plantilla que descargue variará cada vez que ejecute el asistente, suponiendo que haya diferentes entradas de datos y selecciones de aptitudes.

![Plantilla de ejemplo de Power BI en Azure Cognitive Search](media/knowledge-store-connect-power-bi/powerbi-sample-template-portal-only.png "Plantilla de ejemplo de Power BI")

> [!NOTE]
> La plantilla se descarga mientras el asistente está a mitad del vuelo. Tendrá que esperar a que el almacén de conocimiento se cree realmente en Azure Table Storage antes de poder usarlo.

## <a name="video-introduction"></a>Vídeo de introducción

Para ver una demostración del uso de Power BI con un almacén de conocimiento, vea el vídeo siguiente.

> [!VIDEO https://www.youtube.com/embed/XWzLBP8iWqg?version=3]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Tablas en informes y paneles de Power BI](/power-bi/visuals/power-bi-visualization-tables)