---
title: Guía del usuario del linaje de Data Catalog (versión preliminar)
description: En este artículo se proporciona información general sobre la característica de linaje del catálogo de Azure Purview.
author: chanuengg
ms.author: csugunan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 08/25/2021
ms.openlocfilehash: 38f150a99b2145968569e986ea158b1aa552593a
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122968608"
---
# <a name="azure-purview-data-catalog-lineage-user-guide"></a>Guía del usuario del linaje de Azure Purview Data Catalog

En este artículo se proporciona información general sobre las características del linaje de datos en Azure Purview Data Catalog.

## <a name="background"></a>Información previa

Una de las características de la plataforma de Azure Purview es la capacidad de mostrar el linaje entre conjuntos de datos que hayan creado procesos de datos. Sistemas como Data Factory, Data Share y Power BI capturan el linaje de los datos a medida que se transfieren. Los informes de linaje personalizados también se admiten a través de los enlaces de Atlas y la API de REST.

## <a name="lineage-collection"></a>Colección de linajes 
 Los metadatos recopilados en Azure Purview desde sistemas de datos empresariales se unen para mostrar un linaje de datos de un extremo a otro. Los sistemas de datos que recopilan el linaje en Purview se clasifican ampliamente en los tres tipos siguientes.

### <a name="data-processing-system"></a>Sistema de procesamiento de datos
La integración de datos y las herramientas de ETL pueden enviar linajes a Azure Purview en tiempo de ejecución. Herramientas como Data Factory, Data Share, Synapse o Azure Databricks entre otras, pertenecen a esta categoría de sistemas de datos. Los sistemas de procesamiento de datos hacen referencia a los conjuntos de datos que son el origen de diferentes bases de datos y soluciones de almacenamiento, para crear conjuntos de datos de destino. La lista de sistemas de procesamiento de datos que se integran actualmente con Purview para obtener el linaje se enumeran en la tabla siguiente.

| Sistema de procesamiento de datos | Ámbito admitido |
| ---------------------- | ------------|
| Azure Data Factory | [Actividad de copia](how-to-link-azure-data-factory.md#copy-activity-support) <br> [Actividad de los flujos de datos](how-to-link-azure-data-factory.md#data-flow-support) <br> [Ejecución de una actividad del paquete de SSIS](how-to-link-azure-data-factory.md#execute-ssis-package-support) |
| Azure Synapse Analytics | [Actividad de copia](how-to-lineage-azure-synapse-analytics.md#copy-activity-support) <br> [Actividad de los flujos de datos](how-to-lineage-azure-synapse-analytics.md#data-flow-support) |
| Azure Data Share | [Compartir instantánea](how-to-link-azure-data-share.md) |
 
### <a name="data-storage-systems"></a>Sistemas de almacenamiento de datos
Las bases de datos y las soluciones de almacenamiento como SQL Server, Teradata y SAP tienen motores de consulta para transformar datos mediante el lenguaje de scripting. El linaje de datos de los procedimientos almacenados se recopila en Purview y se une con el linaje de otros sistemas.

| Sistema de almacenamiento de datos | Ámbito admitido |
| ---------------------- | ------------|
| Teradata | Procedimientos almacenados

### <a name="data-analytics--reporting-systems"></a>Análisis de datos y sistemas de informes
Sistemas de datos como Azure ML y Power BI informan del linaje en Azure Purview. Estos sistemas usarán los conjuntos de datos de los sistemas de almacenamiento y el proceso a través de su metamodelo para crear un panel de BI y experimentos de Machine Learning, entre otras cosas.

| Análisis de datos y sistema de informes | Ámbito admitido |
| ---------------------- | ------------|
| Power BI | [Conjuntos de datos, flujos de datos, informes y paneles](register-scan-power-bi-tenant.md)

## <a name="get-started-with-lineage"></a>Introducción al linaje

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RWxTAK]

El linaje en Purview incluye conjuntos de datos y procesos. Los conjuntos de datos se conocen como nodos, mientras que los procesos también se pueden llamar bordes:

* **Conjunto de datos (nodo)** : un conjunto de datos (estructurado o no) se proporciona como entrada en un proceso. Por ejemplo, una tabla de SQL, un blob de Azure y archivos de tipo .csv y .xml, se consideran un conjuntos de datos. En la sección de linaje de Purview, los conjuntos de datos se representan mediante cuadros rectangulares.

* **Proceso (borde)** : una actividad o transformación realizada en un conjunto de datos se denomina proceso. Por ejemplo, una actividad de copia de ADF, una instantánea de Data Share, etc. En la sección de linaje de Purview, los procesos se representan mediante cuadros con bordes redondeados.

Para obtener acceso a la información de linaje de un recurso en Purview, siga estos pasos:

1. En Azure Portal, vaya a la [página de la cuenta de Purview](https://aka.ms/purviewportal).

1. Seleccione su cuenta de Azure Purview de la lista y, a continuación, seleccione **Launch purview account** (Iniciar cuenta de Purview) en la página de **Información general**.

1. En la **página principal** de Azure Purview, busque un nombre de conjunto de datos o de proceso, como la actividad de copia de ADF o de Data Flow. A continuación, presione Entrar.

1. En los resultados de la búsqueda, seleccione el recurso y seleccione su pestaña de **Linaje**.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-lineage-from-asset.png" alt-text="Captura de pantalla que muestra cómo seleccionar la pestaña de Linaje." border="true":::

## <a name="asset-level-lineage"></a>Linaje de nivel de recurso

Azure Purview admite el linaje de nivel de recurso de los conjuntos de datos y los procesos. Para ver el linaje de nivel de recurso, vaya a la pestaña **Linaje** del recurso actual en el catálogo. Seleccione el nodo de recursos del conjunto de datos actual. De forma predeterminada, la lista de columnas que pertenecen a los datos aparece en el panel izquierdo.

   :::image type="content" source="./media/catalog-lineage-user-guide/view-columns-from-lineage.png" alt-text="Captura de pantalla que muestra cómo seleccionar la opción para ver columnas en la página del linaje" border="true":::

## <a name="dataset-column-lineage"></a>Linaje de columna de conjunto de datos

Para ver el linaje de nivel de columna de un conjunto de datos, vaya a la pestaña **Linaje** del recurso actual del catálogo y siga los pasos que se indican a continuación:

1. Una vez que esté en la pestaña del linaje, en el panel izquierdo, active la casilla situada junto a cada columna que quiera mostrar en el linaje de datos.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png" alt-text="Captura de pantalla que muestra cómo seleccionar la opción para mostrar columnas en la página del linaje." lightbox="./media/catalog-lineage-user-guide/select-columns-to-show-in-lineage.png":::

2. Mantenga el mouse sobre una columna seleccionada en el panel izquierdo o en el conjunto de datos del panel de lienzo del linaje para ver la asignación de columnas. Se resaltarán todas las instancias de columna.

   :::image type="content" source="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png" alt-text="Captura de pantalla que muestra cómo pasar el mouse sobre un nombre de columna para resaltar el flujo de columna en una ruta de acceso del linaje de datos." lightbox="./media/catalog-lineage-user-guide/show-column-flow-in-lineage.png":::

3. Si el número de columnas es mayor que el que se puede mostrar en el panel izquierdo, use la opción de filtro para seleccionar una columna específica por nombre. Como alternativa, puede usar el mouse para desplazarse por la lista.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-columns-by-name.png" alt-text="Captura de pantalla que muestra cómo filtrar columnas por nombre en la página del linaje." lightbox="./media/catalog-lineage-user-guide/filter-columns-by-name.png":::

4. Si el panel de lienzo del linaje contiene más nodos y bordes, use el filtro para seleccionar los recursos de datos o procesar los nodos por nombre. Como alternativa, puede usar el mouse para desplazarse por la ventana del linaje.

   :::image type="content" source="./media/catalog-lineage-user-guide/filter-assets-by-name.png" alt-text="Captura de pantalla que muestra los nodos de recursos de datos ordenados por nombre en la página de linaje." lightbox="./media/catalog-lineage-user-guide/filter-assets-by-name.png":::

5. Use el control de alternancia en el panel izquierdo para resaltar la lista de conjuntos de datos en el panel de lienzo del linaje. Si desactiva el control de alternancia, se muestra cualquier recurso que contenga al menos una de las columnas seleccionadas. Si, por el contrario, activa el control de alternancia, solo se mostrarán los conjuntos de datos que contengan todas las columnas.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png" alt-text="Captura de pantalla que muestra cómo usar el control de alternancia para filtrar la lista de nodos en la página del linaje." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-filter-nodes.png":::

## <a name="process-column-lineage"></a>Linaje de columna de proceso
El proceso de datos puede tomar uno o más conjuntos de datos de entrada para generar una o más salidas. En Purview, el linaje de nivel de columna está disponible en nodos de proceso. 
1. Cambie entre los conjuntos de datos de entrada y salida de un menú desplegable del panel de columnas.
2. Seleccione las columnas de una o más tablas para ver el linaje que fluye desde el conjunto de datos de entrada hasta el conjunto de datos de salida correspondiente.

   :::image type="content" source="./media/catalog-lineage-user-guide/process-column-lineage.png" alt-text="Captura de pantalla que muestra el linaje de las columnas de un nodo de proceso." lightbox="./media/catalog-lineage-user-guide/process-column-lineage.png":::

## <a name="browse-assets-in-lineage"></a>Navegación por los recursos del linaje
1. Seleccione **Switch to asset** (Cambiar de recurso) en cualquier recurso, para ver sus metadatos en la vista de linaje. Si lo hace así, podrá examinar otro recurso del catálogo desde la vista de linaje.

   :::image type="content" source="./media/catalog-lineage-user-guide/select-switch-to-asset.png" alt-text="Captura de pantalla sobre cómo seleccionar la opción Cambiar de recurso en un recurso de datos de linaje." lightbox="./media/catalog-lineage-user-guide/select-switch-to-asset.png":::

2. El panel de lienzo del linaje podría ser complejo para conjuntos de datos populares. Para evitar confusiones, en la vista predeterminada solo se muestran los cinco niveles de linaje del recurso seleccionado. Para expandir el resto del linaje, haga clic en las burbujas del panel de lienzo del linaje. Los consumidores de datos también pueden ocultar los recursos del panel de lienzo que no sean de su interés. Para reducir aún más la complejidad de los datos, desactive el control de alternancia **More Lineage** (Más linaje) en la parte superior del panel de lienzo del linaje. Esta acción ocultará todas las burbujas en el panel de lienzo del linaje.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png" alt-text="Captura de pantalla que muestra cómo alternar más linaje." lightbox="./media/catalog-lineage-user-guide/use-toggle-to-hide-bubbles.png":::

3. Use los botones inteligentes del panel de lienzo del linaje para obtener una vista óptima del linaje. El diseño automático, el zoom para ajustar, las opciones para acercar o alejar, la pantalla completa y el mapa de navegación están disponibles para ofrecerle una experiencia de linaje envolvente en el catálogo.

   :::image type="content" source="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png" alt-text="Captura de pantalla que muestra cómo seleccionar los botones inteligentes del linaje." lightbox="./media/catalog-lineage-user-guide/use-lineage-smart-buttons.png":::

## <a name="next-steps"></a>Pasos siguientes

* [Vínculo a Azure Data Factory para linaje](how-to-link-azure-data-factory.md)
* [Vínculo a Azure Data Share para linaje](how-to-link-azure-data-share.md)