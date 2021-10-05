---
title: Copia de datos de una tabla web
description: Obtenga información sobre el conector de tabla web que permite copiar datos desde una tabla web a almacenes de datos compatibles como receptores con Azure Data Factory y Synapse Analytics.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: af6423b58cb2eba4fca0d902270afee03a733637
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124782691"
---
# <a name="copy-data-from-web-table-by-using-azure-data-factory-or-synapse-analytics"></a>Copia de datos de tabla web con Azure Data Factory o Synapse Analytics
> [!div class="op_single_selector" title1="Seleccione la versión del servicio Data Factory que usa:"]
> * [Versión 1](v1/data-factory-web-table-connector.md)
> * [Versión actual](connector-web-table.md)
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se describe el uso de la actividad de copia en una canalización de Azure Data Factory o Synapse Analytics para copiar datos de tabla web. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que describe información general de la actividad de copia.

Las diferencias entre este conector de tabla web, el [conector REST](connector-rest.md) y el [conector HTTP](connector-http.md) son:

- El **conector de tabla web** extrae contenido de la tabla de una página web HTML.
- El **conector REST** admite específicamente la copia de datos desde API RESTful.
- El **conector HTTP** es genérico y puede recuperar datos desde cualquier punto de conexión HTTP, por ejemplo, para descargar archivos. 

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Este conector de tabla web es compatible con las actividades siguientes:

- [Actividad de copia](copy-activity-overview.md) con [matriz de origen o receptor compatible](copy-activity-overview.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)

Puede copiar datos desde la base de datos de la tabla web en cualquier almacén de datos receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

En concreto, este conector de tabla web permite **extraer contenido de tablas de una página HTML**.

## <a name="prerequisites"></a>Prerrequisitos

Para usar este conector de tabla web, tiene que configurar una instancia de Integration Runtime autohospedada. Consulte el artículo sobre [Integration Runtime autohospedado](create-self-hosted-integration-runtime.md) para más información.

## <a name="getting-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-web-table-using-ui"></a>Creación de un servicio vinculado a una Web Table mediante la interfaz de usuario

Siga estos pasos para crear un servicio vinculado a Web Table en la interfaz de usuario de Azure Portal.

1. Vaya a la pestaña Administrar del área de trabajo de Azure Data Factory o Synapse y seleccione Servicios vinculados; luego haga clic en Nuevo:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Creación de un servicio vinculado con la interfaz de usuario de Azure Data Factory":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Creación de un nuevo servicio vinculado con la interfaz de usuario de Azure Synapse.":::

2. Busque Web y seleccione el conector de Web Table.

   :::image type="content" source="media/connector-web-table/web-table-connector.png" alt-text="Seleccione el conector de Web Table.":::    


1. Configure los detalles del servicio, pruebe la conexión y cree el nuevo servicio vinculado.

   :::image type="content" source="media/connector-web-table/configure-web-table-linked-service.png" alt-text="Configuración de un servicio vinculado en Web Table.":::

## <a name="connector-configuration-details"></a>Detalles de configuración del conector

Las secciones siguientes proporcionan detalles sobre las propiedades que se usan para definir entidades de Data Factory específicas del conector de tabla web.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de tabla web:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en: **Web** |Sí |
| url | Dirección URL para el origen de Web |Sí |
| authenticationType | El valor permitido es: **Anonymous**. |Sí |
| connectVia | El entorno [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Tal y como se mencionó en los [requisitos previos](#prerequisites), se requiere un entorno Integration Runtime autohospedado. |Sí |

**Ejemplo**:

```json
{
    "name": "WebLinkedService",
    "properties": {
        "type": "Web",
        "typeProperties": {
            "url" : "https://en.wikipedia.org/wiki/",
            "authenticationType": "Anonymous"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de tabla web.

Para copiar datos desde una tabla web, establezca la propiedad type del conjunto de datos en **WebTable**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **WebTable** | Sí |
| path |Dirección URL relativa al recurso que contiene la tabla. |No. Cuando no se especifica la ruta de acceso, se solo se usa la dirección URL especificada en la definición de servicio vinculado. |
| índice |Índice de la tabla en el recurso. Consulte la sección [Obtención de índice de una tabla en una página HTML](#get-index-of-a-table-in-an-html-page) para saber los pasos necesarios para obtener el índice de una tabla en una página HTML. |Sí |

**Ejemplo**:

```json
{
    "name": "WebTableInput",
    "properties": {
        "type": "WebTable",
        "typeProperties": {
            "index": 1,
            "path": "AFI's_100_Years...100_Movies"
        },
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<Web linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades que admite el origen de datos de tabla web.

### <a name="web-table-as-source"></a>Tabla web como origen

Para copiar datos desde una tabla web, establezca el tipo de origen de la actividad de copia en **WebSource**. No se admite ninguna otra propiedad.

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromWebTable",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Web table input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "WebSource"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="get-index-of-a-table-in-an-html-page"></a>Obtención de índice de una tabla en una página HTML

Para obtener el índice de una tabla que necesita configurar en [propiedades del conjunto de datos ](#dataset-properties), puede utilizar, por ejemplo, Excel 2016 como herramienta, como se indica a continuación:

1. Inicie **Excel 2016** y cambie a la pestaña **Datos**.
2. Haga clic en **Nueva consulta** en la barra de herramientas, elija **De otros orígenes** y haga clic en **Desde Web**.

    :::image type="content" source="./media/copy-data-from-web-table/PowerQuery-Menu.png" alt-text="Menú de Power Query":::
3. En el cuadro de diálogo **Desde Web**, escriba la **dirección URL** que usaría en el objeto JSON del servicio vinculado (por ejemplo: https://en.wikipedia.org/wiki/) ) junto con la ruta de acceso que especificaría para el conjunto de datos (por ejemplo: AFI%27s_100_Years...100_Movies) y haga clic en **Aceptar**.

    :::image type="content" source="./media/copy-data-from-web-table/FromWeb-DialogBox.png" alt-text="Cuadro de diálogo Desde Web":::

    Dirección URL que se usa en este ejemplo: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies
4. Si ve el cuadro de diálogo **Acceso a contenido web**, seleccione la **dirección URL** correcta, la **autenticación** y haga clic en **Conectar**.

   :::image type="content" source="./media/copy-data-from-web-table/AccessWebContentDialog.png" alt-text="Cuadro de diálogo Acceso a contenido web":::
5. Haga clic en un elemento de **tabla** en la vista de árbol para ver el contenido de la tabla y después en el botón **Editar** ubicado en la parte inferior.  

   :::image type="content" source="./media/copy-data-from-web-table/Navigator-DialogBox.png" alt-text="Cuadro de diálogo Navegador":::
6. En la ventana **Editor de consultas**, haga clic en el botón **Editor avanzado** de la barra de herramientas.

    :::image type="content" source="./media/copy-data-from-web-table/QueryEditor-AdvancedEditorButton.png" alt-text="Botón Editor avanzado":::
7. En el cuadro de diálogo Editor avanzado, el número que aparece junto a "Origen" es el índice.

    :::image type="content" source="./media/copy-data-from-web-table/AdvancedEditor-Index.png" alt-text="Editor avanzado - Índice":::

Si usa Excel 2013, use [Microsoft Power Query para Excel](https://www.microsoft.com/download/details.aspx?id=39379) para obtener el índice. Consulte el artículo [Conectarse a una página web](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8) para más información. Los pasos son similares si usa [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/).


## <a name="lookup-activity-properties"></a>Propiedades de la actividad de búsqueda

Para obtener información detallada sobre las propiedades, consulte [Actividad de búsqueda](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Pasos siguientes
Para obtener una lista de almacenes de datos que la actividad de copia admite como orígenes y receptores, vea [Almacenes de datos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).
