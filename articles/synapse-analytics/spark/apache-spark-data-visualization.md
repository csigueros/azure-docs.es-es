---
title: Visualizaciones
description: Uso de los cuadernos de Azure Synapse Notebook para visualizar los datos
author: midesa
ms.author: midesa
ms.reviewer: euang
services: synapse-analytics
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: spark
ms.date: 09/13/2020
ms.openlocfilehash: b52f599dd3430f963b03b5fdba41f71abf11ee43
ms.sourcegitcommit: 3ef5a4eed1c98ce76739cfcd114d492ff284305b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128707676"
---
# <a name="visualize-data"></a>Visualización de datos
Azure Synapse es un servicio de análisis integrado que acelera el tiempo necesario para obtener información de los sistemas de almacenamiento de datos y de análisis de macrodatos. La visualización de datos es un componente clave para poder obtener información sobre los datos. Igualmente, facilita la comprensión de los macrodatos y otros datos de pequeño tamaño a los usuarios. También facilita la detección de patrones, tendencias y valores atípicos en grupos de datos. 

Al usar Apache Spark en Azure Synapse Analytics, existen varias opciones integradas que le permitirán visualizar los datos, incluidas las opciones de gráfico del cuaderno de Synapse, el acceso a bibliotecas populares de código abierto y la integración con Synapse SQL y Power BI.

## <a name="notebook-chart-options"></a>Opciones de gráfico del cuaderno
Al usar un cuaderno de Azure Synapse, puede convertir la vista de resultados tabulares en un gráfico personalizado mediante las opciones de gráfico. Aquí puede visualizar los datos sin tener que escribir ningún código. 

### <a name="displaydf-function"></a>Función display(df)
La función ```display``` permite activar las consultas de SQL, los dataframe de Apache Spark y los RDD en visualizaciones de datos enriquecidas. Igualmente, la función ```display``` se puede usar en dataframes o RDD creados en PySpark, Scala, Java y .NET.

Para obtener acceso a las opciones del gráfico:
1. De forma predeterminada, la salida de los comandos magic ```%%sql``` aparece en la vista de tabla representada. También puede llamar a ```display(df)``` en DataFrames de Spark o a una función de Resilient Distributed Datasets (RDD) para generar la vista tabular representada. 
   
2. Una vez que tenga una vista de tabla representada, cambie a la vista de gráfico.
   ![gráficos integrados](./media/apache-spark-development-using-notebooks/synapse-built-in-charts.png#lightbox)

3. Puede personalizar la visualización especificando los valores siguientes:

   | Configuración | Descripción |
   |--|--| 
   | Tipo de gráfico | La función ```display``` admite una amplia gama de tipos de gráficos, como gráficos de barras, gráficos de dispersión, gráficos de líneas, etc. |
   | Clave | Especificar el rango de valores para el eje x.|
   | Value | Especificar el rango de valores para los valores del eje y. |
   | Grupo de series | Se usa para determinar los grupos de la agregación. | 
   | Agregación | Método para agregar datos en la visualización.| 
   
   
    > [!NOTE]
    > De forma predeterminada, la función ```display(df)``` solo tomará las primeras 1000 filas de los datos para representar los gráficos. Marque **Aggregation over all results** (Agregación de todos los resultados) y haga clic en el botón **Aplicar**; el gráfico se creará a partir de todo el conjunto de datos. Se desencadenará un trabajo de Spark cuando cambie la configuración del gráfico. Tenga en cuenta que la operación para completar el cálculo y representar el gráfico puede tardar varios minutos.
   
4. Una vez hecho esto, puede ver la visualización final e interactuar con ella.

### <a name="displaydf-statistic-details"></a>Detalles de estadística de display(df)
Puede usar <code>display(df, summary = true)</code> para comprobar el resumen de las estadísticas de una instancia de DataFrame de Apache Spark determinada que incluya el nombre de columna, el tipo de columna, los valores únicos y los valores que faltan en cada columna. También puede seleccionar una columna específica para ver el valor mínimo, el valor máximo, el valor medio y la desviación estándar.
    ![built-in-charts-summary](./media/apache-spark-development-using-notebooks/synapse-built-in-charts-summary.png#lightbox)
   
### <a name="displayhtml-option"></a>displayHTML() option
Los cuadernos de Azure Synapse Analytics admiten gráficos HTML gracias a la función ```displayHTML```.

La imagen siguiente es un ejemplo de creación de visualizaciones mediante [D3.js](https://d3js.org/).

   ![d3-js-example](./media/apache-spark-data-viz/d3-boxplot.png#lightbox)

Ejecute el código siguiente para crear la visualización anterior.

```python
displayHTML("""<!DOCTYPE html>
<meta charset="utf-8">

<!-- Load d3.js -->
<script src="https://d3js.org/d3.v4.js"></script>

<!-- Create a div where the graph will take place -->
<div id="my_dataviz"></div>
<script>

// set the dimensions and margins of the graph
var margin = {top: 10, right: 30, bottom: 30, left: 40},
  width = 400 - margin.left - margin.right,
  height = 400 - margin.top - margin.bottom;

// append the svg object to the body of the page
var svg = d3.select("#my_dataviz")
.append("svg")
  .attr("width", width + margin.left + margin.right)
  .attr("height", height + margin.top + margin.bottom)
.append("g")
  .attr("transform",
        "translate(" + margin.left + "," + margin.top + ")");

// Create Data
var data = [12,19,11,13,12,22,13,4,15,16,18,19,20,12,11,9]

// Compute summary statistics used for the box:
var data_sorted = data.sort(d3.ascending)
var q1 = d3.quantile(data_sorted, .25)
var median = d3.quantile(data_sorted, .5)
var q3 = d3.quantile(data_sorted, .75)
var interQuantileRange = q3 - q1
var min = q1 - 1.5 * interQuantileRange
var max = q1 + 1.5 * interQuantileRange

// Show the Y scale
var y = d3.scaleLinear()
  .domain([0,24])
  .range([height, 0]);
svg.call(d3.axisLeft(y))

// a few features for the box
var center = 200
var width = 100

// Show the main vertical line
svg
.append("line")
  .attr("x1", center)
  .attr("x2", center)
  .attr("y1", y(min) )
  .attr("y2", y(max) )
  .attr("stroke", "black")

// Show the box
svg
.append("rect")
  .attr("x", center - width/2)
  .attr("y", y(q3) )
  .attr("height", (y(q1)-y(q3)) )
  .attr("width", width )
  .attr("stroke", "black")
  .style("fill", "#69b3a2")

// show median, min and max horizontal lines
svg
.selectAll("toto")
.data([min, median, max])
.enter()
.append("line")
  .attr("x1", center-width/2)
  .attr("x2", center+width/2)
  .attr("y1", function(d){ return(y(d))} )
  .attr("y2", function(d){ return(y(d))} )
  .attr("stroke", "black")
</script>

"""
)

```

## <a name="popular-libraries"></a>Bibliotecas populares
Cuando se trata de la visualización de datos, Python ofrece varias bibliotecas de gráficos equipadas con varias características diferentes. De forma predeterminada, cada grupo de Apache Spark en Azure Synapse Analytics contiene un conjunto seleccionado de bibliotecas de código abierto conocidas. También puede agregar o administrar bibliotecas adicionales y versiones mediante las funcionalidades de administración de la biblioteca de Azure Synapse Analytics. 

### <a name="matplotlib"></a>Matplotlib
Puede representar bibliotecas de trazado estándar, como Matplotlib, mediante las funciones de representación integradas para cada biblioteca.

En la imagen siguiente se muestra un ejemplo de cómo crear un gráfico de barras mediante **Matplotlib**.
   ![Ejemplo de gráfico de líneas.](./media/apache-spark-data-viz/matplotlib-example.png#lightbox)

Ejecute el siguiente código de ejemplo para dibujar la imagen anterior.

```python
# Bar chart

import matplotlib.pyplot as plt

x1 = [1, 3, 4, 5, 6, 7, 9]
y1 = [4, 7, 2, 4, 7, 8, 3]

x2 = [2, 4, 6, 8, 10]
y2 = [5, 6, 2, 6, 2]

plt.bar(x1, y1, label="Blue Bar", color='b')
plt.bar(x2, y2, label="Green Bar", color='g')
plt.plot()

plt.xlabel("bar number")
plt.ylabel("bar height")
plt.title("Bar Chart Example")
plt.legend()
plt.show()
```


### <a name="bokeh"></a>Bokeh
Puede representar bibliotecas HTML o interactivas, como **bokeh**, mediante ```displayHTML(df)``` . 

La imagen siguiente es un ejemplo del trazado de glifos en un mapa mediante **bokeh**.

   ![bokeh-example](./media/apache-spark-development-using-notebooks/synapse-bokeh-image.png#lightbox)

Ejecute el siguiente código de ejemplo para dibujar la imagen anterior.

```python
from bokeh.plotting import figure, output_file
from bokeh.tile_providers import get_provider, Vendors
from bokeh.embed import file_html
from bokeh.resources import CDN
from bokeh.models import ColumnDataSource

tile_provider = get_provider(Vendors.CARTODBPOSITRON)

# range bounds supplied in web mercator coordinates
p = figure(x_range=(-9000000,-8000000), y_range=(4000000,5000000),
           x_axis_type="mercator", y_axis_type="mercator")
p.add_tile(tile_provider)

# plot datapoints on the map
source = ColumnDataSource(
    data=dict(x=[ -8800000, -8500000 , -8800000],
              y=[4200000, 4500000, 4900000])
)

p.circle(x="x", y="y", size=15, fill_color="blue", fill_alpha=0.8, source=source)

# create an html document that embeds the Bokeh plot
html = file_html(p, CDN, "my plot1")

# display this html
displayHTML(html)
```


### <a name="plotly"></a>Plotly
Puede representar bibliotecas HTML o interactivas, como **Plotly**, mediante **displayHTML()** .

Ejecute el siguiente código de ejemplo para dibujar la imagen siguiente.

   ![ejemplo de Plotly](./media/apache-spark-development-using-notebooks/synapse-plotly-image.png#lightbox)


```python
from urllib.request import urlopen
import json
with urlopen('https://raw.githubusercontent.com/plotly/datasets/master/geojson-counties-fips.json') as response:
    counties = json.load(response)

import pandas as pd
df = pd.read_csv("https://raw.githubusercontent.com/plotly/datasets/master/fips-unemp-16.csv",
                   dtype={"fips": str})

import plotly
import plotly.express as px

fig = px.choropleth(df, geojson=counties, locations='fips', color='unemp',
                           color_continuous_scale="Viridis",
                           range_color=(0, 12),
                           scope="usa",
                           labels={'unemp':'unemployment rate'}
                          )
fig.update_layout(margin={"r":0,"t":0,"l":0,"b":0})

# create an html document that embeds the Plotly plot
h = plotly.offline.plot(fig, output_type='div')

# display this html
displayHTML(h)
```
### <a name="pandas"></a>Pandas

Puede ver la salida HTML del dataframe de Pandas como la salida predeterminada. El cuaderno mostrará automáticamente el contenido HTML con estilo. 

   ![Ejemplo de gráfico de Pandas.](./media/apache-spark-data-viz/support-panda.png#lightbox)

```python
import pandas as pd 
import numpy as np 

df = pd.DataFrame([[38.0, 2.0, 18.0, 22.0, 21, np.nan],[19, 439, 6, 452, 226,232]], 

                  index=pd.Index(['Tumour (Positive)', 'Non-Tumour (Negative)'], name='Actual Label:'), 

                  columns=pd.MultiIndex.from_product([['Decision Tree', 'Regression', 'Random'],['Tumour', 'Non-Tumour']], names=['Model:', 'Predicted:'])) 

df 
```


### <a name="additional-libraries"></a>Bibliotecas adicionales 
Además de estas bibliotecas, el entorno de tiempo de ejecución de Azure Synapse Analytics también incluye el siguiente conjunto de bibliotecas que se suelen usar para la visualización de datos:

- [Seaborn](https://seaborn.pydata.org/) 

Puede visitar la [documentación](./spark/../apache-spark-version-support.md) del entorno de tiempo de ejecución de Azure Synapse Analytics para obtener la información más actualizada acerca de las bibliotecas y las versiones disponibles.

## <a name="connect-to-power-bi-using-apache-spark--sql-on-demand"></a>Conexión a Power BI mediante Apache Spark y SQL a petición
Azure Synapse Analytics se integra profundamente con Power BI para permitir a los ingenieros de datos crear soluciones de análisis.

Azure Synapse Analytics permite que los diferentes motores de cálculo de áreas de trabajo compartan bases de datos y tablas entre sus grupos de Spark y el grupo de SQL sin servidor. Mediante el [modelo de metadatos compartido](../metadata/overview.md), puede consultar las tablas de Apache Spark con SQL a petición. Una vez hecho esto, puede conectar el punto de conexión a petición de SQL a Power BI para consultar fácilmente las tablas de Spark sincronizadas.


## <a name="next-steps"></a>Pasos siguientes
- Para obtener más información sobre cómo configurar el conector de SQL DW para Spark: [Conector de Synapse SQL](./spark/../synapse-spark-sql-pool-import-export.md)
- Visualización de las bibliotecas predeterminadas: [Runtime de Azure Synapse Analytics](../spark/apache-spark-version-support.md)