---
title: 'Tutorial: Creación de un cuaderno en Azure Cosmos DB para analizar y visualizar los datos'
description: 'Tutorial: Aprenda a usar cuadernos de Jupyter Notebook integrados para importar datos en Azure Cosmos DB, analizarlos y visualizar la salida.'
author: deborahc
ms.topic: tutorial
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 11/05/2019
ms.author: dech
ms.reviewer: sngun
ms.openlocfilehash: 18bd28fd9a987763397ba77affce897b11e58ddd
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123118146"
---
# <a name="tutorial-create-a-notebook-in-azure-cosmos-db-to-analyze-and-visualize-the-data"></a>Tutorial: Creación de un cuaderno en Azure Cosmos DB para analizar y visualizar los datos
[!INCLUDE[appliesto-sql-api](../includes/appliesto-sql-api.md)]

En este artículo se describe cómo usar cuadernos de Jupyter Notebook integrados para importar datos comerciales de ejemplo en Azure Cosmos DB. Verá cómo usar los comandos magic de SQL y Azure Cosmos DB para ejecutar consultas, analizar los datos y visualizar los resultados.

## <a name="prerequisites"></a>Requisitos previos

* [Habilitación de cuadernos en una cuenta de Azure Cosmos](enable-notebooks.md)

## <a name="create-the-resources-and-import-data"></a>Creación de los recursos e importación de datos
 
En esta sección creará la base de datos y el contenedor de Azure Cosmos e importará los datos comerciales en el contenedor.

1. Vaya a la cuenta de Azure Cosmos y abra el **Explorador de datos**.

1. Vaya a la pestaña **Notebooks** (Cuadernos), seleccione `…` junto a **My Notebooks** (Mis cuadernos) y elija **New Notebook** (Nuevo cuaderno). Seleccione **Python 3** como kernel predeterminado.

   :::image type="content" source="./media/create-notebook-visualize-data/create-new-notebook.png" alt-text="Creación de un cuaderno":::

1. Después de crear un nuevo cuaderno, puede cambiar su nombre a algo como **VisualizeRetailData.ipynb**.

1. A continuación, creará una base de datos denominada "RetailDemo" y un contenedor denominado "WebsiteData" para almacenar los datos comerciales. Puede usar /CartID como clave de partición. Copie y pegue el siguiente código en una nueva celda del cuaderno y ejecútelo:

   ```python
   import azure.cosmos
   from azure.cosmos.partition_key import PartitionKey

   database = cosmos_client.create_database_if_not_exists('RetailDemo')
   print('Database RetailDemo created')

   container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
   print('Container WebsiteData created')
   ```

   Para ejecutar una celda, seleccione `Shift + Enter` o seleccione la celda y elija la opción **Run Active Cell** (Ejecutar celda activa) en la barra de navegación del explorador de datos.

   :::image type="content" source="./media/create-notebook-visualize-data/run-active-cell.png" alt-text="Ejecución de la celda activa":::

   La base de datos y el contenedor se crean en la cuenta actual de Azure Cosmos. El contenedor se aprovisiona con 400 RU/s. Después de la creación de la base de datos y el contenedor, verá la siguiente salida. 

   ```console
    Database RetailDemo created
    Container WebsiteData created
   ```

   También puede actualizar la pestaña **Data** (Datos) y ver los recursos recién creados:

   :::image type="content" source="media/create-notebook-visualize-data/refresh-data-tab.png" alt-text="Actualización de la pestaña de datos para ver el nuevo contenedor":::

1. A continuación, importará los datos comerciales de ejemplo en el contenedor de Azure Cosmos. Este es el formato de un artículo de los datos comerciales:

   ```json
    {
       "CartID":5399,
       "Action":"Viewed",
       "Item":"Cosmos T-shirt",
       "Price":350,
       "UserName":"Demo.User10",
       "Country":"Iceland",
       "EventDate":"2015-06-25T00:00:00",
       "Year":2015,"Latitude":-66.8673,
       "Longitude":-29.8214,
       "Address":"852 Modesto Loop, Port Ola, Iceland",
       "id":"00ffd39c-7e98-4451-9b91-b2bcf2f9a32d"
    }
   ```

   Para este tutorial, los datos comerciales de ejemplo se almacenan en Azure Blob Storage. Puede importarlos en el contenedor de Azure Cosmos pegando el siguiente código en una nueva celda. Puede confirmar que los datos se importan correctamente mediante la ejecución de una consulta para seleccionar el número de artículos.

   ```python
    # Read data from storage
    import urllib.request, json

    with urllib.request.urlopen("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/websiteData.json") as url:
      data = json.loads(url.read().decode())

    print("Importing data. This will take a few minutes...\n")

    for event in data:
     try: 
        container.upsert_item(body=event)
     except errors.CosmosHttpResponseError as e:
        raise
        
    ## Run a query against the container to see number of documents
    query = 'SELECT VALUE COUNT(1) FROM c'
    result = list(container.query_items(query, enable_cross_partition_query=True))

    print('Container with id \'{0}\' contains \'{1}\' items'.format(container.id, result[0]))
   ```

   Al ejecutar la consulta anterior, se devuelve la siguiente salida:

   ```console
   Importing data. This will take a few minutes...

   Container with id 'WebsiteData' contains '2654' items
   ```

## <a name="get-your-data-into-a-dataframe"></a>Obtención de los datos en una estructura DataFrame

Antes de ejecutar consultas para analizar los datos, puede leer los datos del contenedor en una estructura [DataFrame de Pandas](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html) para su análisis. Use el siguiente comando magic de SQL para leer los datos en una estructura DataFrame:

```bash
%%sql --database {database_id} --container {container_id} --output outputDataframeVar 
{Query text}
```

Para más información, consulte el artículo [Comandos y características de cuadernos integrados en Azure Cosmos DB](use-python-notebook-features-and-commands.md). Ejecutará la consulta `SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c`. Los resultados se guardarán en una estructura DataFrame de Pandas denominada df_cosmos. Pegue el siguiente comando en una nueva celda del cuaderno y ejecútelo:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```

En una nueva celda del cuaderno, ejecute el siguiente código para leer los diez primeros artículos de la salida:

```python
# See a sample of the result
df_cosmos.head(10)
```

:::image type="content" source="./media/create-notebook-visualize-data/run-query-get-top10-items.png" alt-text="Ejecución de una consulta para obtener los diez primeros artículos":::

## <a name="run-queries-and-analyze-your-data"></a>Ejecución de consultas y análisis de los datos

En esta sección ejecutará algunas consultas en los datos recuperados.

* **Query1**: ejecute una consulta de tipo Agrupar por en la estructura DataFrame para obtener la suma de los ingresos totales por ventas en cada país y región, y mostrar cinco elementos de los resultados. En una nueva celda del cuaderno, ejecute el siguiente código:

   ```python
   df_revenue = df_cosmos.groupby("Country").sum().reset_index()
   display(df_revenue.head(5))
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/total-sales-revenue-output.png" alt-text="Salida de ingresos totales por ventas":::

* **Query2:** para obtener una lista de los cinco artículos más comprados, abra una nueva celda del cuaderno y ejecute el siguiente código:

   ```python
   import pandas as pd

   ## What are the top 5 purchased items?
   pd.DataFrame(df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False).head(5), columns=['Count'])
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/top5-purchased-items.png" alt-text="Los cinco artículos más comprados":::

## <a name="visualize-your-data"></a>Visualización de los datos  

1. Ahora que ya dispone de los datos sobre los ingresos procedentes del contenedor de Azure Cosmos, puede visualizar los datos con una biblioteca de visualización de su elección. En este tutorial se usará la biblioteca Bokeh. Abra una nueva celda del cuaderno y ejecute el siguiente código para instalar la biblioteca Bokeh. Una vez que se cumplan todos los requisitos, se instalará la biblioteca.

   ```python
   import sys
   !{sys.executable} -m pip install bokeh --user
   ```

1. A continuación, prepárese para trazar los datos en un mapa. Combine los datos de Azure Cosmos DB con la información de país ubicada en Azure Blob Storage y convierta el resultado al formato GeoJSON. Copie el siguiente código en una nueva celda del cuaderno y ejecútelo.

   ```python
   import urllib.request, json
   import geopandas as gpd

   # Load country/region information for mapping
   countries = gpd.read_file("https://cosmosnotebooksdata.blob.core.windows.net/notebookdata/countries.json")

   # Merge the countries/regions dataframe with our data in Azure Cosmos DB, joining on country/region code
   df_merged = countries.merge(df_revenue, left_on = 'admin', right_on = 'Country', how='left')

   # Convert to GeoJSON so bokeh can plot it
   merged_json = json.loads(df_merged.to_json())
   json_data = json.dumps(merged_json)
   ```

1. Visualice los ingresos por ventas en distintos países o regiones en un mapa mundial; para ello, ejecute el siguiente código en una nueva celda del cuaderno:

   ```python
   from bokeh.io import output_notebook, show
   from bokeh.plotting import figure
   from bokeh.models import GeoJSONDataSource, LinearColorMapper, ColorBar
   from bokeh.palettes import brewer
    
   #Input GeoJSON source that contains features for plotting.
   geosource = GeoJSONDataSource(geojson = json_data)
    
   #Choose our choropleth color palette: https://bokeh.pydata.org/en/latest/docs/reference/palettes.html
   palette = brewer['YlGn'][8]
    
   #Reverse color order so that dark green is highest revenue
   palette = palette[::-1]
    
   #Instantiate LinearColorMapper that linearly maps numbers in a range, into a sequence of colors.
   color_mapper = LinearColorMapper(palette = palette, low = 0, high = 1000)
    
   #Define custom tick labels for color bar.
   tick_labels = {'0': '$0', '250': '$250', '500':'$500', '750':'$750', '1000':'$1000', '1250':'$1250', '1500':'$1500','1750':'$1750', '2000': '>$2000'}
    
   #Create color bar. 
   color_bar = ColorBar(color_mapper=color_mapper, label_standoff=8,width = 500, height = 20,
   border_line_color=None,location = (0,0), orientation = 'horizontal', major_label_overrides = tick_labels)
    
   #Create figure object.
   p = figure(title = 'Sales revenue by country', plot_height = 600 , plot_width = 1150, toolbar_location = None)
   p.xgrid.grid_line_color = None
   p.ygrid.grid_line_color = None
    
   #Add patch renderer to figure. 
   p.patches('xs','ys', source = geosource,fill_color = {'field' :'ItemRevenue', 'transform' : color_mapper},
              line_color = 'black', line_width = 0.25, fill_alpha = 1)
    
   #Specify figure layout.
   p.add_layout(color_bar, 'below')
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
   
   #Display figure.
   show(p)
   ```

   La salida muestra el mapa mundial con distintos colores. Los colores más oscuros representan a los países con mayores ingresos y los más claros, a los de menores ingresos.

   :::image type="content" source="./media/create-notebook-visualize-data/countries-revenue-map-visualization.png" alt-text="Visualización del mapa de ingresos de países y regiones":::

1. Veamos otro caso de visualización de datos. El contenedor WebsiteData tiene un registro de usuarios que han visto un artículo, lo han agregado al carro y lo han adquirido. Vamos a trazar la tasa de conversión de los artículos comprados. Ejecute el siguiente código en una nueva celda para visualizar la tasa de conversión de cada artículo:

   ```python
   from bokeh.io import show, output_notebook
   from bokeh.plotting import figure
   from bokeh.palettes import Spectral3
   from bokeh.transform import factor_cmap
   from bokeh.models import ColumnDataSource, FactorRange
       
   # Get the top 10 items as an array
   top_10_items = df_cosmos[df_cosmos['Action']=='Purchased'].groupby('Item').size().sort_values(ascending=False)[:10].index.values.tolist()
    
   # Filter our data to only these 10 items
   df_top10 = df_cosmos[df_cosmos['Item'].isin(top_10_items)]
    
   # Group by Item and Action, sorting by event count
   df_top10_sorted = df_top10.groupby(['Item', 'Action']).count().rename(columns={'Country':'ResultCount'}, inplace=False).reset_index().sort_values(['Item', 'ResultCount'], ascending = False).set_index(['Item', 'Action'])
    
   # Get sorted X-axis values - this way, we can display the funnel of view -> add -> purchase
   x_axis_values = df_top10_sorted.index.values.tolist()
    
   group = df_top10_sorted.groupby(['Item', 'Action'])
    
   # Specifiy colors for X axis
   index_cmap = factor_cmap('Item_Action', palette=Spectral3, factors=sorted(df_top10.Action.unique()), start=1, end=2)
    
   # Create the plot
    
   p = figure(plot_width=1200, plot_height=500, title="Conversion rate of items from View -> Add to cart -> Purchase", x_range=FactorRange(*x_axis_values), toolbar_location=None, tooltips=[("Number of events", "@ResultCount_max"), ("Item, Action", "@Item_Action")])
    
   p.vbar(x='Item_Action', top='ItemRevenue_max', width=1, source=group,
           line_color="white", fill_color=index_cmap, )
    
   #Configure how the plot looks
   p.y_range.start = 0
   p.x_range.range_padding = 0.05
   p.xgrid.grid_line_color = None
   p.xaxis.major_label_orientation = 1.2
   p.outline_line_color = "black"
   p.xaxis.axis_label = "Item"
   p.yaxis.axis_label = "Count"
    
   #Display figure inline in Jupyter Notebook.
   output_notebook()
    
   #Display figure.
   show(p)
   ```

   :::image type="content" source="./media/create-notebook-visualize-data/visualize-purchase-conversion-rate.png" alt-text="Visualización de la tarifa de conversión de compra":::

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre los comandos de cuadernos de Python, consulte el artículo [Uso de comandos y características de cuadernos integrados en Azure Cosmos DB](use-python-notebook-features-and-commands.md).
