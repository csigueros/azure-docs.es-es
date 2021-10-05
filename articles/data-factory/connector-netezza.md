---
title: Copia de datos de Netezza
description: Aprenda a copiar datos de Netezza en almacenes de datos receptores compatibles con una actividad de copia de una canalización de Azure Data Factory o Synapse Analytics.
titleSuffix: Azure Data Factory & Azure Synapse
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/09/2021
ms.author: jianleishen
ms.openlocfilehash: 588700126d87361e4530c073cdcaf1a5a9339511
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124828177"
---
# <a name="copy-data-from-netezza-by-using-azure-data-factory-or-synapse-analytics"></a>Copia de datos de Netezza con Azure Data Factory o Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se describe el uso de la actividad de copia en canalizaciones de Azure Data Factory y Azure Synapse Analytics para copiar datos de Netezza. El artículo se basa en [Actividad de copia](copy-activity-overview.md), en el que se ofrece información general acerca de la actividad de copia.

>[!TIP]
>Para información sobre el escenario de migración de datos de Netezza a Azure, consulte [Migración de datos del servidor local de Netezza a Azure](data-migration-guidance-netezza-azure-sqldw.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Este conector de Netezza es compatible con las actividades siguientes:

- [Actividad de copia](copy-activity-overview.md) con [matriz de origen o receptor compatible](copy-activity-overview.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)


Puede copiar datos de Netezza en cualquier almacén de datos de receptor admitido. Para obtener una lista de almacenes de datos que la actividad de copia admite como orígenes y receptores, consulte [Almacenes de datos y formatos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).

El conector de Netezza admite la copia en paralelo desde el origen. Consulte la sección [Copia en paralelo desde Netezza](#parallel-copy-from-netezza) para obtener más detalles.

El servicio proporciona un controlador integrado para permitir la conectividad. No es necesario instalar manualmente uno para usar este conector.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)]

## <a name="get-started"></a>Introducción

Puede crear una canalización que use una actividad de copia mediante el SDK de .NET, el SDK de Python, Azure PowerShell, la API de REST o una plantilla de Azure Resource Manager. Vea el [tutorial de la actividad de copia](quickstart-create-data-factory-dot-net.md) para obtener instrucciones paso a paso para crear una canalización con una actividad de copia.

## <a name="create-a-linked-service-to-netezza-using-ui"></a>Creación de un servicio vinculado en Netezza mediante la interfaz de usuario

Siga estos pasos para crear un servicio vinculado en Netezza en la interfaz de usuario de Azure Portal.

1. Vaya a la pestaña Administrar del área de trabajo de Azure Data Factory o Synapse y seleccione Servicios vinculados; luego haga clic en Nuevo:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Captura de pantalla de la creación de un nuevo servicio vinculado con la interfaz de usuario de Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Captura de pantalla de la creación de un nuevo servicio vinculado con la interfaz de usuario de Azure Synapse.":::

2. Busque Netezza y seleccione el conector de Netezza.

   :::image type="content" source="media/connector-netezza/netezza-connector.png" alt-text="Captura de pantalla del conector de Netezza.":::    


1. Configure los detalles del servicio, pruebe la conexión y cree el nuevo servicio vinculado.

   :::image type="content" source="media/connector-netezza/configure-netezza-linked-service.png" alt-text="Captura de pantalla de la configuración del servicio vinculado en Netezza.":::

## <a name="connector-configuration-details"></a>Detalles de configuración del conector

En las siguientes secciones se proporcionan detalles sobre las propiedades que puede usar para definir entidades específicas del conector de Netezza.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Netezza:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** debe establecerse en **Netezza**. | Sí |
| connectionString | Cadena de conexión de ODBC para conectarse a Netezza. <br/>También puede colocar la contraseña en Azure Key Vault y extraer la configuración de `pwd` de la cadena de conexión. Consulte los siguientes ejemplos y el artículo [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md) con información detallada. | Sí |
| connectVia | Instancia de [Integration Runtime](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Obtenga más información en la sección [Requisitos previos](#prerequisites). Si no se especifica, se usa el valor predeterminado de Azure Integration Runtime. |No |

Una cadena de conexión típica es `Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>`. En la tabla siguiente se describen más propiedades que puede establecer:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| SecurityLevel | Nivel de seguridad que usa el controlador para la conexión con el almacén de datos. El controlador admite conexiones SSL con autenticación unidireccional mediante la versión 3 de SSL. <br>Ejemplo: `SecurityLevel=preferredSecured`. Los valores admitidos son:<br/>- **Solo sin protección** (**onlyUnSecured**): el controlador no usa SSL.<br/>- **Sin protección preferido (preferredUnSecured) (valor predeterminado)** : si el servidor proporciona una opción, el controlador no usa SSL. <br/>- **Con protección preferido (preferredSecured)** : si el servidor proporciona una opción, el controlador usa SSL. <br/>- **Solo con protección (onlySecured)** : el controlador no se conectará a menos que haya una conexión SSL disponible. | No |
| CaCertFile | La ruta de acceso completa al certificado SSL que usa el servidor. Ejemplo: `CaCertFile=<cert path>;`| Sí, si se ha habilitado SSL |

**Ejemplo**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;PWD=<password>"
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

**Ejemplo: Almacenamiento de la contraseña en Azure Key Vault**

```json
{
    "name": "NetezzaLinkedService",
    "properties": {
        "type": "Netezza",
        "typeProperties": {
            "connectionString": "Server=<server>;Port=<port>;Database=<database>;UID=<user name>;",
            "pwd": { 
                "type": "AzureKeyVaultSecret", 
                "store": { 
                    "referenceName": "<Azure Key Vault linked service name>", 
                    "type": "LinkedServiceReference" 
                }, 
                "secretName": "<secretName>" 
            }
        },
        "connectVia": {
            "referenceName": "<name of Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Netezza.

Para ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte [Conjuntos de datos](concepts-datasets-linked-services.md).

Para copiar datos desde Netezza, establezca la propiedad **type** del conjunto de datos en **NetezzaTable**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **NetezzaTable** | Sí |
| esquema | Nombre del esquema. |No (si se especifica "query" en el origen de la actividad)  |
| table | Nombre de la tabla. |No (si se especifica "query" en el origen de la actividad)  |
| tableName | Nombre de la tabla con el esquema. Esta propiedad permite la compatibilidad con versiones anteriores. Use `schema` y `table` para la carga de trabajo nueva. | No (si se especifica "query" en el origen de la actividad) |

**Ejemplo**

```json
{
    "name": "NetezzaDataset",
    "properties": {
        "type": "NetezzaTable",
        "linkedServiceName": {
            "referenceName": "<Netezza linked service name>",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {}
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

En esta sección se proporciona una lista de las propiedades que admite el origen de Netezza.

Para ver una lista completa de las secciones y propiedades que hay disponibles para definir actividades, consulte [Canalizaciones](concepts-pipelines-activities.md).

### <a name="netezza-as-source"></a>Netezza como origen

>[!TIP]
>Para cargar datos desde Netezza de manera eficaz con la creación de particiones de datos, obtenga más información en la sección [Copia en paralelo desde Netezza](#parallel-copy-from-netezza).

Para copiar datos desde Netezza, establezca el tipo **source** en la actividad de copia en **NetezzaSource**. La sección **source** de la actividad de copia admite las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad **type** del origen de la actividad de copia debe establecerse en **NetezzaSource**. | Sí |
| Query | Use la consulta SQL personalizada para leer los datos. Ejemplo: `"SELECT * FROM MyTable"` | No (si se especifica "tableName" en el conjunto de datos) |
| partitionOptions | Especifica los opciones de creación de particiones de datos que se usan para cargar datos desde Netezza. <br>Los valores permitidos son los siguientes: **None** (valor predeterminado), **DataSlice** y **DynamicRange**.<br>Cuando se habilita una opción de partición (es decir, no `None`), el grado de paralelismo para cargar simultáneamente datos de una base de datos Netezza se controla mediante la configuración [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) en la actividad de copia. | No |
| partitionSettings | Especifique el grupo de configuración para la creación de particiones de datos. <br>Se aplica cuando la opción de partición no es `None`. | No |
| partitionColumnName | Especifique el nombre de la columna de origen **in integer type** que usará la creación de particiones por rangos para la copia en paralelo. Si no se especifica, se detectará automáticamente la clave principal de la tabla y se usará como columna de partición. <br>Se aplica si la opción de partición es `DynamicRange`. Si usa una consulta para recuperar datos de origen, enlace `?AdfRangePartitionColumnName` en la cláusula WHERE. Consulte un ejemplo en la sección [Copia en paralelo desde Netezza](#parallel-copy-from-netezza). | No |
| partitionUpperBound | El valor máximo de la columna de partición para copiar datos. <br>Se aplica cuando la opción de partición es `DynamicRange`. Si usa la consulta para recuperar datos de origen, enlace `?AdfRangePartitionUpbound` en la cláusula WHERE. Consulte la sección [Copia en paralelo desde Netezza](#parallel-copy-from-netezza) para ver un ejemplo. | No |
| partitionLowerBound | El valor mínimo de la columna de partición para copiar datos. <br>Se aplica si la opción de partición es `DynamicRange`. Si usa una consulta para recuperar datos de origen, enlace `?AdfRangePartitionLowbound` en la cláusula WHERE. Consulte la sección [Copia en paralelo desde Netezza](#parallel-copy-from-netezza) para ver un ejemplo. | No |

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromNetezza",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Netezza input dataset name>",
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
                "type": "NetezzaSource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-netezza"></a>Copia en paralelo desde Netezza

El conector de Netezza de Data Factory proporciona la creación de particiones de datos integrados para copiar datos de Netezza en paralelo. Puede encontrar las opciones de creación de particiones de datos en la pestaña **Origen** de la actividad de copia.

:::image type="content" source="./media/connector-netezza/connector-netezza-partition-options.png" alt-text="Captura de pantalla de las opciones de partición":::

Al habilitar la copia con particiones, el servicio ejecuta consultas en paralelo en el origen de Netezza para cargar los datos mediante particiones. El grado en paralelo se controla mediante el valor [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) de la actividad de copia. Por ejemplo, si establece `parallelCopies` en cuatro, el servicio genera y ejecuta al mismo tiempo cuatro consultas de acuerdo con la configuración y la opción de partición que ha especificado, y cada consulta recupera una porción de datos de la base de datos de Netezza.

Se le sugiere que habilite la copia en paralelo con la creación de particiones de datos, especialmente si carga grandes cantidades de datos de la base de datos de Netezza. Estas son algunas configuraciones sugeridas para diferentes escenarios. Cuando se copian datos en un almacén de datos basado en archivos, se recomienda escribir en una carpeta como varios archivos (solo especifique el nombre de la carpeta), en cuyo caso el rendimiento es mejor que escribir en un único archivo.

| Escenario                                                     | Configuración sugerida                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de una tabla grande.                                   | **Opción de partición**: Segmento de datos. <br><br/>Durante la ejecución, el servicio particiona automáticamente los datos en función de los [segmentos de datos integrados de Netezza](https://www.ibm.com/support/knowledgecenter/en/SSULQD_7.2.1/com.ibm.nz.adm.doc/c_sysadm_data_slices_parts_disks.html) y copia los datos por particiones. |
| Cargue grandes cantidades de datos mediante una consulta personalizada.                 | **Opción de partición**: Segmento de datos.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>`.<br>Durante la ejecución, el servicio reemplaza `?AdfPartitionCount` (por el número de copia en paralelo establecido en la actividad de copia) y `?AdfDataSliceCondition` por la lógica de partición del segmento de datos, y lo envía a Netezza. |
| Carga de grandes cantidades de datos mediante una consulta personalizada, con una columna de enteros con valor distribuido uniformemente para la creación de particiones por rangos. | **Opciones de partición**: partición por rangos dinámica.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Columna de partición**: especifique la columna usada para crear la partición de datos. Puede crear particiones en la columna con un tipo de datos entero.<br>**Límite de partición superior** y **límite de partición inferior**: especifique si quiere filtrar en la columna de partición para recuperar solo los datos entre el intervalo inferior y el superior.<br><br>Durante la ejecución, el servicio reemplaza `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` y `?AdfRangePartitionLowbound` por el nombre real de la columna y los intervalos de valores de cada partición y se los envía a Netezza. <br>Por ejemplo, si establece la columna de partición "ID" con un límite inferior de 1 y un límite superior de 80, con la copia en paralelo establecida en 4, el servicio recupera los datos de 4 particiones. Los identificadores están comprendidos entre [1, 20], [21, 40], [41, 60] y [61, 80] respectivamente. |

**Ejemplo: consulta con partición de segmento de datos**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE mod(datasliceid, ?AdfPartitionCount) = ?AdfDataSliceCondition AND <your_additional_where_clause>",
    "partitionOption": "DataSlice"
}
```

**Ejemplo: consulta con partición por rangos dinámica**

```json
"source": {
    "type": "NetezzaSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<dynamic_range_partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Propiedades de la actividad de búsqueda

Para obtener información detallada sobre las propiedades, consulte [Actividad de búsqueda](control-flow-lookup-activity.md).


## <a name="next-steps"></a>Pasos siguientes

Para obtener una lista de almacenes de datos que la actividad de copia admite como orígenes y receptores, consulte [Almacenes de datos y formatos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).
