---
title: Tutorial de Power BI para el conector de Azure Cosmos DB
description: Use este tutorial de Power BI para importar JSON, crear informes muy precisos y visualizar datos mediante el conector de Azure Cosmos DB y Power BI.
author: Rodrigossz
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/21/2019
ms.author: rosouz
ms.openlocfilehash: ccdef9784e96d55615caf640675f98946fcca43d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111959118"
---
# <a name="visualize-azure-cosmos-db-data-by-using-the-power-bi-connector"></a>Visualizar datos de Azure Cosmos DB mediante el conector de Power BI
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[Power BI](https://powerbi.microsoft.com/) es un servicio en línea donde puede crear y compartir paneles e informes. Power BI Desktop es una herramienta de creación de informes con la que puede recuperar datos desde diversos orígenes de datos. Azure Cosmos DB es uno de los orígenes de datos que puede usar con Power BI Desktop. Puede conectar Power BI Desktop a la cuenta de Azure Cosmos DB mediante el conector Azure Cosmos DB para Power BI.  Después de importar los datos de Azure Cosmos DB a Power BI, puede transformarlos, crear informes y publicarlos en Power BI.

Otra opción es crear informes casi en tiempo real mediante [Azure Synapse Link para Azure Cosmos DB](synapse-link.md). Con Azure Synapse Link, puede conectarse a Power BI para analizar los datos de Azure Cosmos DB, sin ningún impacto en el rendimiento ni en el costo de las cargas de trabajo transaccionales y sin canalizaciones de ETL. Puede usar los modos [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) o [importación](/power-bi/connect-data/service-dataset-modes-understand#import-mode). Para más información, haga clic [aquí](synapse-link-power-bi.md).


En este artículo se describen los pasos necesarios para conectar la cuenta de Azure Cosmos DB a Power BI Desktop. Después de conectarla, vaya a una colección, extraiga los datos, transforme los datos JSON en formato tabular y publique un informe en Power BI.

> [!NOTE]
> El conector de Power BI para Azure Cosmos DB se conecta a Power BI Desktop. Los informes que se crean con Power BI Desktop se pueden publicar en PowerBI.com. No se puede realizar la extracción directa de datos de Azure Cosmos DB desde PowerBI.com. 

> [!NOTE]
> Actualmente, la conexión a Azure Cosmos DB con el conector de Power BI solo se admite en las cuentas de Azure Cosmos DB SQL API y de Gremlin API.

> [!NOTE]
> La creación de paneles de Power BI casi en tiempo real mediante Azure Synapse Link se admite actualmente para Azure Cosmos DB SQL API y Azure Cosmos DB API para MongoDB.

## <a name="prerequisites"></a>Requisitos previos
Antes de seguir las instrucciones de este tutorial de Power BI, asegúrese de que tiene acceso a los siguientes recursos:

* [Descargue la versión más reciente de Power BI Desktop](https://powerbi.microsoft.com/desktop).

* Descargue los [datos de volcanes de ejemplo](https://github.com/Azure-Samples/azure-cosmos-db-sample-data/blob/main/SampleData/VolcanoData.json) de GitHub.

* [Cree una cuenta de base de datos de Azure Cosmos](create-cosmosdb-resources-portal.md#create-an-azure-cosmos-db-account) e importe los datos de volcanes mediante la [herramienta de migración de datos de Azure Cosmos DB](import-data.md). Al importar datos, tenga en cuenta las opciones siguientes para el origen y los destinos en la herramienta de migración de datos:

   * **Parámetros de origen** 

       * **Importar desde:** Archivo JSON

   * **Parámetros de destino** 

      * **Cadena de conexión:** `AccountEndpoint=<Your_account_endpoint>;AccountKey=<Your_primary_or_secondary_key>;Database= <Your_database_name>` 

      * **Clave de partición:** /País 

      * **Rendimiento de la colección:** 1000 

Para compartir los informes en PowerBI.com, debe tener una cuenta en PowerBI.com.  Para más información sobre Power BI y Power BI Pro, visite [https://powerbi.microsoft.com/pricing](https://powerbi.microsoft.com/pricing).

## <a name="lets-get-started"></a>Comencemos.
En este tutorial, imaginemos que usted es un geólogo que estudia los volcanes de todo el mundo. Los datos de los volcanes se almacenan en una cuenta de Azure Cosmos DB y el formato de documento JSON tiene este aspecto:

```json
{
    "Volcano Name": "Rainier",
        "Country": "United States",
        "Region": "US-Washington",
        "Location": {
          "type": "Point",
          "coordinates": [
            -121.758,
            46.87
          ]
        },
        "Elevation": 4392,
        "Type": "Stratovolcano",
        "Status": "Dendrochronology",
        "Last Known Eruption": "Last known eruption from 1800-1899, inclusive"
}
```

Recuperará los datos de los volcanes de la cuenta de Azure Cosmos DB y visualizará los datos en un informe de Power BI interactivo.

1. Ejecute Power BI Desktop.

2. También puede **obtener datos**, consultar **orígenes recientes** o **abrir otros informes** directamente desde la pantalla de bienvenida. Haga clic en la X de la esquina superior derecha para cerrar la pantalla. Se muestra la vista **Informe** de Power BI Desktop.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview.png" alt-text="Vista de informes de Power BI Desktop: conector de Power BI":::

3. Seleccione la cinta de opciones **Inicio** y haga clic en **Obtener datos**.  Se muestra la pantalla **Obtener datos** .

4. Haga clic en **Azure**, seleccione **Azure Cosmos DB (beta)** y, a continuación, haga clic en **Conectar**. 

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbigetdata.png" alt-text="Obtención de datos de Power BI Desktop: conector de Power BI":::

5. En la página **Vista previa del conector**, haga clic en **Continuar**. Aparece la ventana de **Azure Cosmos DB**.

6. Especifique la dirección URL del punto de conexión de la cuenta de Azure Cosmos DB de la que desea recuperar los datos, tal como se muestra a continuación, y haga clic en **Aceptar**. Para usar su propia cuenta, puede recuperar la dirección URL del cuadro te texto Identificador URI de la hoja **Claves** de Azure Portal. Si lo prefiere, puede indicar el nombre de la base de datos, el nombre de la colección o usar el navegador para seleccionar la base de datos y la colección para identificar la procedencia de los datos.
   
7. Si se conecta a este punto de conexión por primera vez, se le pedirá la clave de cuenta. Para usar su propia cuenta, recupere la clave del cuadro **Clave principal** de la hoja **Claves de solo lectura** de Azure Portal. Escriba la clave adecuada y, a continuación, haga clic en **Conectar**.
   
   Se recomienda usar la clave de solo lectura al generar informes. De esta forma se evita una exposición innecesaria de la clave principal a posibles riesgos de seguridad. La clave de solo lectura está disponible desde la hoja **Claves** de Azure Portal. 
    
8. Con la cuenta conectada correctamente, aparece el panel **Navegador**. El panel **Navegador** muestra la lista de bases de datos que hay en la cuenta.

9. Haga clic en la base de datos de donde proceden los datos del informe y expándala; seleccione ``volcanodb`` (el nombre de la base de datos puede ser diferente).   

10. Seleccione una colección que contenga los datos que se van a recuperar, seleccione **volcano1** (el nombre de la colección puede ser diferente).
    
    El panel de vista previa muestra una lista de elementos **Registro** .  Un documento se representa como un tipo **Registro** en Power BI. De forma similar, un bloque JSON anidado dentro de un documento también es un **Registro**.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbinavigator.png" alt-text="Tutorial de Power BI para conector de Power BI de Azure Cosmos DB: ventana del navegador":::

12. Haga clic en **Editar** para iniciar el Editor de consultas en una nueva ventana para transformar los datos.

## <a name="flattening-and-transforming-json-documents"></a>Eliminación de formato y transformación de documentos JSON
1. Cambie a la ventana del Editor de consultas de Power BI, donde debería ver la columna **Documento** en el panel central.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditor.png" alt-text="Editor de consultas de Power BI Desktop":::

1. Haga clic en el botón de expansión en el lado derecho del encabezado de columna **Documento** .  Se muestra el menú contextual con una lista de campos.  Seleccione los campos necesarios para el informe, por ejemplo, nombre de volcán, país, región, ubicación, elevación, tipo, estado y última erupción conocida. Anule la selección del cuadro **Use original column name as prefix** (Usar nombre de columna original como prefijo) y haga clic en **Aceptar**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiqueryeditorexpander.png" alt-text="Tutorial de Power BI para conector de Power BI de Azure Cosmos DB: expandir documentos":::

1. El panel central muestra una vista previa del resultado con los campos seleccionados.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflatten.png" alt-text="Tutorial de Power BI para conector de Power BI de Azure Cosmos DB: aplanar resultados":::

1. En nuestro ejemplo, la propiedad Ubicación es un bloque de GeoJSON en un documento.  Como puede ver, la ubicación se representa como un tipo **Registro** en Power BI Desktop.  

1. Haga clic en el botón de expansión en el lado derecho del encabezado de columna Document.Location.  Aparece el menú contextual con los campos de tipo y coordenadas.  Vamos a seleccionar el campo de coordenadas, asegúrese de que **Use original column name as prefix** (Usar nombre de columna original como prefijo) no esté seleccionado y haga clic en **Aceptar**.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbilocationrecord.png" alt-text="Tutorial de Power BI para conector de Power BI de Azure Cosmos DB: registro de ubicación":::

1. El panel central muestra ahora una columna de coordenadas de tipo **lista** .  Como se ha indicado al comienzo del tutorial, los datos de GeoJSON son de tipo Point con los valores de latitud y longitud registrados en la matriz de coordenadas.
   
   El elemento coordinates[0] representa la longitud, mientras que el elemento coordinates[1] representa la latitud.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbiresultflattenlist.png" alt-text="Tutorial de Power BI para conector de Power BI de Azure Cosmos DB - Lista de coordenadas":::

1. Para eliminar el formato de la matriz de coordenadas, cree una **columna personalizada** llamada LatLong.  Seleccione la cinta de opciones **Agregar columna** y haga clic en **Columna personalizada**.  Aparece la ventana de **Columna personalizada**.

1. Dele un nombre a la nueva columna, por ejemplo, LatLong.

1. Después, especifique la fórmula personalizada para la nueva columna.  En nuestro ejemplo, concatenaremos los valores de latitud y longitud separados por comas, como se muestra a continuación mediante la fórmula siguiente: `Text.From([coordinates]{1})&","&Text.From([coordinates]{0})`. Haga clic en **OK**.
   
   Para más información sobre las expresiones de análisis de datos (DAX), incluidas las funciones DAX, visite [Aspectos básicos de DAX en Power BI Desktop](/power-bi/desktop-quickstart-learn-dax-basics).
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicustomlatlong.png" alt-text="Tutorial de Power BI para conector de Power BI de Azure Cosmos DB: agregar columna personalizada":::

1. Ahora, el panel central muestra las nuevas columnas LatLong rellenadas con los valores.
    
    :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicolumnlatlong.png" alt-text="Tutorial de Power BI para conector de Power BI de Azure Cosmos DB: columna LatLong personalizada":::
    
    Si recibe un error en la columna nueva, asegúrese de que los pasos aplicados en Configuración la consulta coinciden con la siguiente figura:
    
    :::image type="content" source="./media/powerbi-visualize/power-bi-applied-steps.png" alt-text="Los pasos aplicados deben ser Source, Navigation, Expanded Document, Expanded Document.Location, Added Custom.":::
    
    Si los pasos son distintos, elimine los pasos adicionales e intente agregar nuevamente la columna personalizada. 

1. Haga clic en **Cerrar y aplicar** para guardar el modelo de datos.

   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbicloseapply.png" alt-text="Tutorial de Power BI para conector de Power BI de Azure Cosmos DB: cerrar y aplicar":::

<a id="build-the-reports"></a>
## <a name="build-the-reports"></a>Generación de informes

La vista de informe de Power BI Desktop es donde puede empezar a crear informes para visualizar datos.  Puede crear informes arrastrando y soltando campos en el lienzo del **informe** .

:::image type="content" source="./media/powerbi-visualize/power_bi_connector_pbireportview2.png" alt-text="Vista de informe de Power BI Desktop: arrastrar y colocar los campos obligatorios":::

En la vista de informe, debe buscar:

1. El panel **Campos** es donde verá una lista de los modelos de datos con campos que puede usar para los informes.
1. El panel **Visualizaciones** . Un informe puede contener una o varias visualizaciones.  Seleccione los tipos visuales que se ajusten a sus necesidades en el panel **Visualizaciones** .
1. El lienzo **Informe** es donde se compilan los objetos visuales para el informe.
1. La página **Informe** . Puede agregar varias páginas de informes en Power BI Desktop.

A continuación se muestran los pasos básicos para crear un sencillo informe de la vista de mapa interactivo.

1. En nuestro ejemplo, crearemos una vista del mapa que muestra la ubicación de cada volcán.  En el panel **Visualizaciones** , haga clic en el tipo de mapa visual que aparece destacado en la captura de pantalla anterior.  Debería ver el tipo de mapa visual pintado en el lienzo **Informe** .  El panel de **visualizaciones** también debe mostrar un conjunto de propiedades relacionadas con el tipo de mapa visual.
1. Ahora, arrastre y coloque el campo LatLong desde el panel de **campos** a la **bicación** del panel de **visualizaciones**.
1. A continuación, arrastre y coloque el campo del nombre del volcán en la propiedad **Leyenda** .  
1. Luego, arrastre y coloque el campo Elevación en la propiedad **Tamaño** .  
1. Ahora debería ver el mapa visual que muestra un conjunto de burbujas que indican la ubicación de cada volcán, con el tamaño de la burbuja correlacionado con la elevación del volcán.
1. Ahora ha creado un informe básico.  Puede personalizar aún más el informe agregando más visualizaciones.  En nuestro caso, hemos agregado una segmentación del tipo de volcán para que el informe sea interactivo.  
   
1. En el menú Archivo, haga clic en **Guardar** y guarde el archivo como PowerBITutorial.pbix.

## <a name="publish-and-share-your-report"></a>Publicación y uso compartido del informe
Para compartir el informe, debe tener una cuenta en PowerBI.com.

1. En Power BI Desktop, haga clic en la cinta de opciones de **inicio** .
1. Haga clic en **Publicar**.  Se le pedirá que escriba el nombre de usuario y la contraseña de su cuenta de PowerBI.com.
1. Una vez que se autentique la credencial, el informe se publicará en el destino que seleccione.
1. Haga clic en **Abrir 'PowerBITutorial.pbix' en Power BI** para ver y compartir su informe en PowerBI.com.
   
   :::image type="content" source="./media/powerbi-visualize/power_bi_connector_open_in_powerbi.png" alt-text="La publicación en Power BI se realizó correctamente Abrir tutorial de Power BI":::

## <a name="create-a-dashboard-in-powerbicom"></a>Creación de un panel en PowerBi.com
Ahora que tiene un informe, vamos a compartirlo en PowerBi.com

Cuando publica su informe de Power BI Desktop en PowerBI.com, se genera un **informe** y un **conjunto de datos** en su inquilino de PowerBI.com. Por ejemplo, después de que publique un informe llamado **PowerBITutorial** en PowerBI.com, podrá ver PowerBITutorial tanto en la sección de **informes** como en la sección de **onjuntos de datos** en PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/powerbi-reports-datasets.png" alt-text="Captura de pantalla del nuevo informe y conjunto de datos en PowerBI.com":::

Para crear un panel que se puede compartir, haga clic en el botón de la **página Anclar elemento activo** del informe PowerBI.com.

   :::image type="content" source="./media/powerbi-visualize/power-bi-pin-live-tile.png" alt-text="Captura de pantalla sobre cómo anclar un informe a PowerBI.com":::

Luego, siga las instrucciones que aparecen en [Anclar un icono de un informe](https://powerbi.microsoft.com/documentation/powerbi-service-pin-a-tile-to-a-dashboard-from-a-report/#pin-a-tile-from-a-report) para crear un panel nuevo. 

También puede realizar modificaciones ad hoc en el informe antes de crear un panel. Sin embargo, se recomienda que use Power BI Desktop para realizar las modificaciones y vuelva a publicar el informe en PowerBI.com.

## <a name="refresh-data-in-powerbicom"></a>Actualización de datos en PowerBI.com
Hay dos formas de actualizar los datos: actualización ad hoc y actualización programada.

Para una actualización ad hoc, simplemente haga clic en **Actualizar ahora** para actualizar los datos.

Para realizar una actualización programada, haga lo siguiente.

1. Vaya a **Configuración** y abra la pestaña **Conjuntos de datos**.

2. Haga clic en **Actualización programada** y establezca la programación.


## <a name="next-steps"></a>Pasos siguientes
* Para más información sobre Power BI, consulte [Introducción a Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/).
* Para más información sobre Azure Cosmos DB, consulte la [página de aterrizaje de la documentación de Azure Cosmos DB](https://azure.microsoft.com/documentation/services/cosmos-db/).