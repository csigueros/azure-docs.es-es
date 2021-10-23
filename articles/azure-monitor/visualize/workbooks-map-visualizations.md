---
title: Visualizaciones de mapa del libro de Azure Monitor
description: Obtenga más información sobre las visualizaciones de mapa del libro de Azure Monitor.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 11/25/2020
ms.openlocfilehash: a90723754440191bcc92c6e01341cecee7869520
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130132348"
---
# <a name="map-visualization"></a>Visualización de mapa

La visualización de mapa le permite identificar los problemas de regiones específicas y le muestra vistas agregadas de alto nivel de los datos de supervisión; para ello, le proporciona la capacidad de agregar todos los datos asignados a cada ubicación, país o región.

En la captura de pantalla siguiente se muestran las transacciones totales y la latencia de un extremo a otro de distintas cuentas de almacenamiento. Aquí el tamaño viene determinado por el número total de transacciones, y las métricas de colores que aparecen debajo del mapa muestran la latencia de un extremo a otro. A primera vista, el número de transacciones de la región del **Oeste de EE. UU.** es pequeño en comparación con la región del **Este de EE. UU.** , pero la latencia de un extremo a otro de la región del **Oeste de EE. UU.** es mayor que la de la región del **Este de EE. UU.** . Esto le proporciona una visión inicial de que algo es incorrecto en la región del **Oeste de EE. UU.** .

![Captura de pantalla del mapa de ubicación de Azure](./media/workbooks-map-visualizations/map-performance-example.png)

## <a name="adding-a-map"></a>Agregar un mapa

Un mapa se puede visualizar si los datos y las métricas subyacentes tienen información de la latitud y la longitud, de los recursos de Azure, de la ubicación de Azure o del país o región, del nombre o del código del país o la región.

### <a name="using-azure-location"></a>Uso de la ubicación de Azure

1. Para cambiar el libro al modo de edición, seleccione el elemento de la barra de herramientas **Editar**.
2. Seleccione **Agregar** y, luego, *Agregar consulta*.
3. Cambie el *Origen de datos* a `Azure Resource Graph` y seleccione cualquier suscripción que tenga una cuenta de almacenamiento.
4. Escriba la consulta siguiente para realizar el análisis y haga clic en **Ejecutar consulta**.

    ```kusto
    where  type =~ 'microsoft.storage/storageaccounts'
    | summarize count() by location
    ```

5. Establezca el *Tamaño* en `Large`.
6. Establezca la *Visualización* en `Map`.
7. Todos los valores de configuración se rellenarán automáticamente. En la configuración personalizada, seleccione el botón **Configuración del mapa** para abrir el panel de configuración.
8. A continuación, se muestra una captura de pantalla de la visualización de mapa que muestra las cuentas de almacenamiento de cada región de Azure para la suscripción seleccionada.

![Captura de pantalla del mapa de ubicación de Azure con la consulta anterior](./media/workbooks-map-visualizations/map-azure-location-example.png)

### <a name="using-azure-resource"></a>Uso de los recursos de Azure

1. Para cambiar el libro al modo de edición, seleccione el elemento de la barra de herramientas **Editar**.
2. Seleccione **Agregar** y, a continuación, *Agregar métrica*.
3. Use una suscripción que tenga cuentas de almacenamiento.
4. Cambie el *Tipo de recurso* a `storage account`, y en la opción *Recursos*, seleccione varias cuentas de almacenamiento.
5. Seleccione **Agregar métrica** y agregue una métrica de transacción.
    1. Espacio de nombres: `Account`
    2. Métrica: `Transactions`
    3. Agregación: `Sum`
    
    ![Captura de pantalla de la métrica de transacción](./media/workbooks-map-visualizations/map-transaction-metric.png)
1. Seleccione **Agregar métrica** y agregue la métrica Success E2E Latency.
    1. Espacio de nombres: `Account`
    1. Métrica: `Success E2E Latency`
    1. Agregación: `Average`
    
    ![Captura de pantalla de la métrica de latencia de un extremo a otro correcta](./media/workbooks-map-visualizations/map-e2e-latency-metric.png)
1. Establezca el *Tamaño* en `Large`.
1. Establezca el tamaño de la *visualización* en `Map`.
1. En la **Configuración del mapa**, establezca lo siguiente:
    1. Información de ubicación mediante: `Azure Resource`
    1. Campo de recursos de Azure: `Name`
    1. Tamaño por: `microsoft.storage/storageaccounts-Transaction-Transactions`
    1. Agregación de la ubicación: `Sum of values`
    1. Tipo de coloreado: `Heatmap`
    1. Color por: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agregación del color: `Sum of values`
    1. Paleta de colores: `Green to Red`
    1. Valor mínimo: `0`
    1. Valor de métrica: `microsoft.storage/storageaccounts-Transaction-SuccessE2ELatency`
    1. Agregue otras métricas por: `Sum of values`
    1. Seleccione el cuadro de **formato personalizado**
    1. Unidad: `Milliseconds`
    1. Estilo: `Decimal`
    1. Máximo de dígitos fraccionarios: `2`

### <a name="using-countryregion"></a>Uso del país o región

1. Para cambiar el libro al modo de edición, seleccione el elemento de la barra de herramientas **Editar**.
2. Seleccione **Agregar* y, a continuación, *Agregar consulta*.
3. Cambie el *Origen de datos* a `Log`.
4. Seleccione el *Tipo de recurso* como `Application Insights` y, a continuación, elija cualquier recurso de Application Insights que tenga datos de pageViews.
5. Use el editor de consultas para especificar el KQL para el análisis y seleccione **Ejecutar consulta**.

    ```kusto
    pageViews
    | project duration, itemCount, client_CountryOrRegion
    | limit 20
    ```

6. Establezca los valores de tamaño en `Large`.
7. Establezca la visualización en `Map`.
8. Todos los valores de configuración se rellenarán automáticamente. En la configuración personalizada, seleccione **Configuración de mapa**.

### <a name="using-latitudelocation"></a>Uso de la latitud y la ubicación

1. Para cambiar el libro al modo de edición, seleccione el elemento de la barra de herramientas **Editar**.
2. Seleccione **Agregar* y, a continuación, *Agregar consulta*.
3. Cambie el *Origen de datos* a `JSON`.
1. Escriba los datos JSON que se muestran a continuación en el editor de consultas y seleccione **Ejecutar consulta**.
1. Establezca los valores de *Tamaño* en `Large`.
1. Establezca la *Visualización* en `Map`.
1. En la opción de **Configuración de mapa** que está en la "Configuración de métricas", establezca la *Etiqueta de métrica* en `displayName` y, a continuación, seleccione **Guardar y cerrar**.

En la visualización de mapa siguiente se muestran los usuarios de cada ubicación de latitud y longitud con la etiqueta seleccionada para las métricas.

![Captura de pantalla de la visualización de mapa que muestra los usuarios de cada ubicación de latitud y longitud con la etiqueta seleccionada para las métricas.](./media/workbooks-map-visualizations/map-latitude-longitude-example.png)

## <a name="map-settings"></a>Configuración del mapa

### <a name="layout-settings"></a>Configuración del diseño

| Configuración | Explicación |
|:-------------|:-------------|
| `Location info using` | Seleccione una manera de obtener la ubicación de los elementos que se muestran en el mapa. <br> **Latitud y longitud**: Seleccione esta opción si hay columnas con información de la latitud y la longitud. Cada fila con datos de latitud y longitud se mostrará como un elemento distinto en el mapa. <br> **Ubicación de Azure**: seleccione esta opción si hay una columna que tenga información sobre la ubicación de Azure (este, westeurope, centralindia, etc.). Especifique esa columna y capturará la latitud y la longitud correspondientes a cada ubicación de Azure; a continuación, agrupe las mismas filas de ubicación (según la agregación especificada) para mostrar las ubicaciones en el mapa. <br> **Recurso de Azure**: seleccione esta opción si hay una columna con información de recursos de Azure (cuenta de almacenamiento, cuenta de cosmosdb, etc.). Especifique esa columna y capturará la latitud y la longitud correspondientes a cada recurso de Azure; a continuación, agrupe las mismas filas de ubicación (ubicación de Azure) según la agregación especificada, para mostrar las ubicaciones en el mapa. <br> **País o región**: seleccione esta opción si hay una columna con información sobre el nombre o el código del país o región (US, Estados Unidos, IN, India, CN, China). Especifique esa columna y capturará la latitud y la longitud correspondientes a cada país, región o código; a continuación, agrupe las filas con el mismo nombre de código de país o región o con el mismo nombre de país o región para mostrar las ubicaciones en el mapa. El nombre y el código del país no se agruparán como una única entidad en el mapa.
| `Latitude/Longitude` | Estas dos opciones estarán visibles si el valor del campo de información de ubicación es: latitud y longitud. Seleccione la columna que tiene la latitud en el campo latitud y la longitud en el campo longitud, respectivamente. |
| `Azure location field` | Esta opción estará visible si el valor del campo de información de ubicación es: ubicación de Azure. Seleccione la columna que contiene la información de ubicación de Azure. |
| `Azure resource field` | Esta opción estará visible si el valor del campo de información de ubicación es: Recurso de Azure. Seleccione la columna que contiene la información de recursos de Azure. |
| `Country/Region field` | Esta opción estará visible si el valor del campo de información de ubicación es: país o región. Seleccione la columna que contiene la información del país o región. |
| `Size by` | Esta opción controla el tamaño de los elementos que se muestran en el mapa. El tamaño depende del valor de la columna que especificó el usuario. Actualmente, el radio del círculo es directamente proporcional a la raíz cuadrada del valor de la columna. Si se selecciona el valor "None...", todos los círculos mostrarán el tamaño predeterminado de la región.|
| `Aggregation for location` | Este campo especifica cómo agregar las columnas de tipo **Tamaño por** que tengan la misma ubicación de Azure, los mismos recursos de Azure o el mismo país o región. |
| `Minimum region size` | Este campo especifica qué es el radio mínimo del elemento que se muestra en el mapa. Se usa cuando hay una diferencia significativa entre el tamaño de los valores de la columna, por lo que los elementos más pequeños apenas son visibles en el mapa. |
| `Maximum region size` | Este campo especifica qué es el radio máximo del elemento que se muestra en el mapa. Se usa cuando los valores de la columna que indica el tamaño son muy grandes y cubren una gran área del mapa.|
| `Default region size` | Este campo especifica qué es el radio predeterminado del elemento que se muestra en el mapa. El radio predeterminado se usa cuando la columna de tipo Tamaño por es "None..." o el valor es 0.|
| `Minimum value` | Este es el valor mínimo que se usa para calcular el tamaño de la región. Si no se especifica, el valor mínimo será el valor más pequeño después de la agregación. |
| `Maximum value` | Este es el valor máximo que se usa para calcular el tamaño de la región. Si no se especifica, el valor máximo será el valor más grande después de la agregación.|
| `Opacity of items on Map` | Este campo especifica la transparencia de los elementos que se muestran en el mapa. Una opacidad de 1 significa que no hay transparencia, y una opacidad de 0 significa que los elementos no estarán visibles en el mapa. Si hay demasiados elementos en el mapa, la opacidad se puede establecer en un valor bajo para que todos los elementos superpuestos sean visibles.|

### <a name="color-settings"></a>Configuración del color

| Tipo de coloreado | Explicación |
|:------------- |:-------------|
| `None` | Todos los nodos tienen el mismo color. |
| `Thresholds` | En este tipo, los colores de las celdas se establecen mediante reglas de umbral (por ejemplo, _CPU > 90 % => rojo, 60 % > CPU > 90 % => amarillo, CPU < 60 % => verde_). <ul><li> **Color por**: el valor de esta columna lo usará la lógica de umbrales o de mapa térmico.</li> <li>**Agregación del color**: este campo especifica cómo agregar las columnas de tipo **Color por** que tengan la misma ubicación de Azure, los mismos recursos de Azure o el mismo país o región. </li> <ul> |
| `Heatmap` | En este tipo, las celdas se colorean en función de la paleta y el campo Color por. Asimismo, esta opción también tendrá las mismas opciones de tipo **Color por** y **Agregación de color** como umbrales. |

### <a name="metric-settings"></a>Configuración de métricas
| Configuración | Explicación |
|:------------- |:-------------|
| `Metric Label` | Esta opción estará visible si el valor del campo de información de ubicación es: latitud y longitud. Con esta característica, el usuario puede elegir la etiqueta que se va a mostrar en las métricas que se muestran debajo del mapa. |
| `Metric Value` | Este campo especifica el valor de métrica que se va a mostrar debajo del mapa. |
| `Create 'Others' group after` | Este campo especifica el límite antes de que se cree un grupo "Otros". |
| `Aggregate 'Others' metrics by` | Este campo especifica la agregación que se usa en el grupo "Otros", si este se muestra. |
| `Custom formatting` | Use este campo para establecer las unidades, el estilo y las opciones de formato de los valores numéricos. Estas opción es la misma que el [formato personalizado de la cuadrícula](../visualize/workbooks-grid-visualizations.md#custom-formatting).|

## <a name="next-steps"></a>Pasos siguientes

- Aprenda a crear [visualizaciones de tipo panal en los libros](../visualize/workbooks-honey-comb.md).