---
title: Movimiento de datos de tabla web mediante Azure Data Factory
description: Obtenga información sobre cómo mover datos de una tabla de una página web mediante Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/05/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 0e3472393587fe117e342bbc1dce5d990520760d
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128651225"
---
# <a name="move-data-from-a-web-table-source-using-azure-data-factory"></a>Movimiento de datos de un origen de tabla web mediante Azure Data Factory
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](data-factory-web-table-connector.md)
> * [Versión 2 (versión actual)](../connector-web-table.md)

> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte el artículo sobre el [conector de tabla web en V2](../connector-web-table.md).

En este artículo, se describe el uso de la actividad de copia en Azure Data Factory para mover datos de una tabla de una página web a un almacén de datos receptor compatible. Este artículo se basa en el artículo sobre las [actividades de movimiento de datos](data-factory-data-movement-activities.md), que presenta una introducción general del movimiento de datos con la actividad de copia la lista de almacenes de datos compatibles como orígenes/receptores.

Factoría de datos solo admite actualmente el movimiento de datos desde una tabla web a otros almacenes de datos, pero no de otros almacenes de datos a un destino de tabla web.

> [!IMPORTANT]
> Actualmente, este conector web solo permite extraer contenido de tablas de una página HTML. Para recuperar datos de un punto de conexión HTTP/s, utilice el [conector HTTP](data-factory-http-connector.md) en su lugar.

## <a name="prerequisites"></a>Requisitos previos

Para utilizar este conector de tabla web, debe configurar Integration Runtime autohospedado (también conocido como Data Management Gateway) y configurar la propiedad `gatewayName` en el servicio vinculado del receptor. Por ejemplo, para copiar de la tabla web a Azure Blob Storage, configure el servicio vinculado de Azure Storage de la siguiente manera:

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

## <a name="getting-started"></a>Introducción
Puede crear una canalización con una actividad de copia que mueva los datos desde un almacén de datos Cassandra local mediante el uso de diferentes herramientas o API. 

- La manera más fácil de crear una canalización es usar el **Asistente para copiar**. Consulte [Tutorial: Creación de una canalización mediante el Asistente para copia](data-factory-copy-data-wizard-tutorial.md) para ver un tutorial rápido sobre la creación de una canalización utilizando el Asistente para copia de datos. 
- Puede usar las siguientes herramientas para crear una canalización: **Visual Studio**, **Azure PowerShell**, una **plantilla de Azure Resource Manager**, la **API de .NET** y **API REST**. Consulte el [tutorial de actividad de copia](data-factory-copy-data-from-azure-blob-storage-to-sql-database.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia. 

Tanto si usa las herramientas como las API, realice los pasos siguientes para crear una canalización que mueva datos de un almacén de datos de origen a un almacén de datos receptor:

1. Cree **servicios vinculados** para vincular almacenes de datos de entrada y salida a la factoría de datos.
2. Cree **conjuntos de datos** con el fin de representar los datos de entrada y salida para la operación de copia. 
3. Cree una **canalización** con una actividad de copia que tome como entrada un conjunto de datos y un conjunto de datos como salida. 

Cuando se usa el Asistente, se crean automáticamente definiciones de JSON para estas entidades de Data Factory (servicios vinculados, conjuntos de datos y la canalización). Al usar herramientas o API (excepto la API de .NET), se definen estas entidades de Data Factory con el formato JSON.  Para ver un ejemplo con definiciones de JSON para entidades de Data Factory que se emplean para copiar datos de una tabla web, consulte la sección [Ejemplo con definiciones de JSON: copia de datos de una tabla web a un blob de Azure](#json-example-copy-data-from-web-table-to-azure-blob) de este artículo. 

En las secciones siguientes, se proporcionan detalles sobre las propiedades JSON que se usan para definir entidades de Data Factory específicas de una tabla web:

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado
En la tabla siguiente se proporciona la descripción de los elementos JSON específicos del servicio vinculado de Web.

| Propiedad | Descripción | Obligatorio |
| --- | --- | --- |
| type |La propiedad type debe establecerse en: **Web** |Sí |
| Url |Dirección URL para el origen de Web |Sí |
| authenticationType |Anonymous. |Sí |

### <a name="using-anonymous-authentication"></a>Uso de autenticación anónima

```json
{
    "name": "web",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos
Para una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). Las secciones como structure, availability y policy del código JSON del conjunto de datos son similares para todos los tipos de conjunto de datos (SQL Azure, blob de Azure, tabla de Azure, etc.).

La sección **typeProperties** es diferente en cada tipo de conjunto de datos y proporciona información acerca de la ubicación de los datos en el almacén de datos. La sección typeProperties del conjunto de datos de tipo **WebTable** tiene las propiedades siguientes:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type |Tipo del conjunto de datos. Debe establecerse en **WebTable** |Sí |
| path |Dirección URL relativa al recurso que contiene la tabla. |No. Cuando no se especifica la ruta de acceso, se solo se usa la dirección URL especificada en la definición de servicio vinculado. |
| índice |Índice de la tabla en el recurso. Consulte la sección [Obtención de índice de una tabla en una página HTML](#get-index-of-a-table-in-an-html-page) para saber los pasos necesarios para obtener el índice de una tabla en una página HTML. |Sí |

**Ejemplo**:

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia
Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo [Creación de canalizaciones](data-factory-create-pipelines.md). Las propiedades (como nombre, descripción, tablas de entrada y salida, y directivas) están disponibles para todos los tipos de actividades.

Por otra parte, las propiedades disponibles en la sección typeProperties de la actividad varían con cada tipo de actividad. Para la actividad de copia, varían en función de los tipos de orígenes y receptores.

En este momento, cuando el origen de la actividad de copia es de tipo **WebSource**, no se admite ninguna propiedad adicional.


## <a name="json-example-copy-data-from-web-table-to-azure-blob"></a>Ejemplo con definiciones de JSON: copia de datos de una tabla web a un blob de Azure
El ejemplo siguiente muestra:

1. Un servicio vinculado de tipo [Web](#linked-service-properties).
2. Un servicio vinculado de tipo [AzureStorage](data-factory-azure-blob-connector.md#linked-service-properties)
3. Un [conjunto de datos](data-factory-create-datasets.md) de entrada de tipo [WebTable](#dataset-properties).
4. Un [conjunto de datos](data-factory-create-datasets.md) de salida de tipo [AzureBlob](data-factory-azure-blob-connector.md#dataset-properties).
5. Una [canalización](data-factory-create-pipelines.md) con la actividad de copia que usa [WebSource](#copy-activity-properties) y [BlobSink](data-factory-azure-blob-connector.md#copy-activity-properties).

El ejemplo copia los datos de una tabla web a un blob de Azure cada hora. Las propiedades JSON usadas en estos ejemplos se describen en las secciones que aparecen después de los ejemplos.

En el ejemplo siguiente se muestra cómo copiar datos de una tabla web a un blob de Azure. Sin embargo, los datos se pueden copiar directamente en cualquiera de los receptores indicados en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) mediante la actividad de copia de Azure Data Factory.

**Servicio vinculado de tipo Web** : este ejemplo utiliza el servicio vinculado de tipo Web con la autenticación anónima. Consulte la sección [Propiedades del servicio vinculado de Web](#linked-service-properties) para conocer los diferentes tipos de autenticación que se pueden usar.

```json
{
    "name": "WebLinkedService",
    "properties":
    {
        "type": "Web",
        "typeProperties":
        {
            "authenticationType": "Anonymous",
            "url" : "https://en.wikipedia.org/wiki/"
        }
    }
}
```

**Servicio vinculado de Azure Storage**

```json
{
  "name": "AzureStorageLinkedService",
  "properties": {
    "type": "AzureStorage",
    "typeProperties": {
      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>",
      "gatewayName": "<gateway name>"
    }
  }
}
```

**Conjunto de datos de entrada WebTable**: si se establece **external** en **true**, se informa al servicio Data Factory que la tabla es externa a la factoría de datos y no se produce por ninguna actividad de la misma.

> [!NOTE]
> Consulte la sección [Obtención de índice de una tabla en una página HTML](#get-index-of-a-table-in-an-html-page) para saber los pasos necesarios para obtener el índice de una tabla en una página HTML.  
>
>

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "linkedServiceName": "WebLinkedService",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "external": true,
        "availability": {
            "frequency": "Hour",
            "interval":  1
        }
    }
}
```


**Conjunto de datos de salida de blob de Azure**

Los datos se escriben en un nuevo blob cada hora (frecuencia: hora, intervalo: 1).

```json
{
    "name": "AzureBlobOutput",
    "properties":
    {
        "type": "AzureBlob",
        "linkedServiceName": "AzureStorageLinkedService",
        "typeProperties":
        {
            "folderPath": "adfgetstarted/Movies"
        },
        "availability":
        {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```



**Canalización con actividad de copia**

La canalización contiene una actividad de copia que está configurada para usar los conjuntos de datos de entrada y de salida y está programada para ejecutarse cada hora. En la definición del JSON de la canalización, el tipo **source** se establece en **WebSource** y el tipo **sink**, en **BlobSink**.

Consulte Propiedades de tipo WebSource para obtener la lista de propiedades que admite WebSource.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2014-06-01T18:00:00",
    "end":"2014-06-01T19:00:00",
    "description":"pipeline with copy activity",
    "activities":[  
      {
        "name": "WebTableToAzureBlob",
        "description": "Copy from a Web table to an Azure blob",
        "type": "Copy",
        "inputs": [
          {
            "name": "WebTableInput"
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutput"
          }
        ],
        "typeProperties": {
          "source": {
            "type": "WebSource"
          },
          "sink": {
            "type": "BlobSink"
          }
        },
       "scheduler": {
          "frequency": "Hour",
          "interval": 1
        },
        "policy": {
          "concurrency": 1,
          "executionPriorityOrder": "OldestFirst",
          "retry": 0,
          "timeout": "01:00:00"
        }
      }
      ]
   }
}
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Obtención de índice de una tabla en una página HTML
1. Inicie **Excel 2016** y cambie a la pestaña **Datos**.  
2. Haga clic en **Nueva consulta** en la barra de herramientas, elija **De otros orígenes** y haga clic en **Desde Web**.

    :::image type="content" source="./media/data-factory-web-table-connector/PowerQuery-Menu.png" alt-text="Menú de Power Query":::
3. En el cuadro de diálogo **Desde Web**, escriba la **dirección URL** que usaría en el objeto JSON del servicio vinculado (por ejemplo: https://en.wikipedia.org/wiki/) ) junto con la ruta de acceso que especificaría para el conjunto de datos (por ejemplo: AFI%27s_100_Years...100_Movies) y haga clic en **Aceptar**.

    :::image type="content" source="./media/data-factory-web-table-connector/FromWeb-DialogBox.png" alt-text="Cuadro de diálogo Desde Web":::

    Dirección URL que se usa en este ejemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Si ve el cuadro de diálogo **Acceso a contenido web**, seleccione la **dirección URL** correcta, la **autenticación** y haga clic en **Conectar**.

   :::image type="content" source="./media/data-factory-web-table-connector/AccessWebContentDialog.png" alt-text="Cuadro de diálogo Acceso a contenido web":::
5. Haga clic en un elemento de **tabla** en la vista de árbol para ver el contenido de la tabla y después en el botón **Editar** ubicado en la parte inferior.  

   :::image type="content" source="./media/data-factory-web-table-connector/Navigator-DialogBox.png" alt-text="Cuadro de diálogo Navegador":::
6. En la ventana **Editor de consultas**, haga clic en el botón **Editor avanzado** de la barra de herramientas.

    :::image type="content" source="./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png" alt-text="Botón Editor avanzado":::
7. En el cuadro de diálogo Editor avanzado, el número que aparece junto a "Origen" es el índice.

    :::image type="content" source="./media/data-factory-web-table-connector/AdvancedEditor-Index.png" alt-text="Editor avanzado - Índice":::

Si usa Excel 2013, use [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) para obtener el índice. Consulte el artículo [Conectarse a una página web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) para más información. Los pasos son similares si usa [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/).

> [!NOTE]
> Para asignar columnas del conjunto de datos de origen a columnas del conjunto de datos del receptor, consulte [Mapping dataset columns in Azure Data Factory](data-factory-map-columns.md) (Asignación de columnas de conjunto de datos en Azure Data Factory).

## <a name="performance-and-tuning"></a>Rendimiento y optimización
Consulte [Guía de optimización y rendimiento de la actividad de copia](data-factory-copy-activity-performance.md) para más información sobre los factores clave que afectan al rendimiento del movimiento de datos (actividad de copia) en Azure Data Factory y las diversas formas de optimizarlo.
