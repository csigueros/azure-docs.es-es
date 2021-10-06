---
title: Copia de datos de Amazon RDS para Oracle
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo copiar datos de Amazon RDS para Oracle en almacenes receptores compatibles a través de canalizaciones de Data Factory o Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: jianleishen
ms.openlocfilehash: c6f14b2bb5129907aa5efac042f23217791d84d3
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594585"
---
# <a name="copy-data-from-amazon-rds-for-oracle-by-using-azure-data-factory-or-azure-synapse-analytics"></a>Copia de datos desde Amazon RDS para Oracle mediante Azure Data Factory o Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se resume el uso de la actividad de copia de Azure Data Factory para copiar datos desde una base de datos Amazon RDS para Oracle. Se basa en la [introducción a la actividad de copia](copy-activity-overview.md).

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Este conector de Amazon RDS para Oracle es compatible con las actividades siguientes:

- [Actividad de copia](copy-activity-overview.md) con [matriz de origen o receptor compatible](copy-activity-overview.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)

Puede copiar datos desde la base de datos de Amazon RDS para Oracle en cualquier almacén de datos receptor compatible. Consulte la tabla de [Almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como receptores.

En concreto, este conector de Amazon RDS para Oracle admite:

- Las siguientes versiones de una base de datos Amazon RDS para Oracle:
    - Amazon RDS para Oracle 19c R1 (19.1) y versiones posteriores
    - Amazon RDS para Oracle 18c R1 (18.1) y versiones posteriores
    - Amazon RDS para Oracle 12c R1 (12.1) y versiones posteriores
    - Amazon RDS para Oracle 11g R1 (11.1) y versiones posteriores
- Copia en paralelo desde un origen de Amazon RDS para Oracle. Consulte la sección [Copia en paralelo desde Amazon RDS para Oracle](#parallel-copy-from-amazon-rds-for-oracle) para obtener más detalles.

> [!Note]
> No se admite Amazon RDS para el servidor proxy de Oracle.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [data-factory-v2-integration-runtime-requirements](includes/data-factory-v2-integration-runtime-requirements.md)] 

El entorno de ejecución de integración proporciona un controlador de Amazon RDS para Oracle integrado. Por tanto, no es necesario instalar manualmente un controlador para copiar datos desde Amazon RDS para Oracle.

## <a name="get-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-amazon-rds-for-oracle-using-ui"></a>Creación de un servicio vinculado a Amazon RDS para Oracle mediante la interfaz de usuario

Siga estos pasos para crear un servicio vinculado a Amazon RDS para Oracle en la interfaz de usuario de Azure Portal.

1. Vaya a la pestaña Administrar del área de trabajo de Azure Data Factory o Synapse y seleccione Servicios vinculados; luego haga clic en Nuevo:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Captura de pantalla de la creación de un nuevo servicio vinculado con la interfaz de usuario de Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Captura de pantalla de la creación de un nuevo servicio vinculado con la interfaz de usuario de Azure Synapse.":::

1. Busque Amazon RDS para Oracle y seleccione el conector de Amazon RDS para Oracle.

    :::image type="content" source="media/connector-amazon-rds-for-oracle/amazon-rds-for-oracle-connector.png" alt-text="Captura de pantalla del conector de Amazon RDS para Oracle.":::    

1. Configure los detalles del servicio, pruebe la conexión y cree el nuevo servicio vinculado.

    :::image type="content" source="media/connector-amazon-rds-for-oracle/configure-amazon-rds-for-oracle-linked-service.png" alt-text="Captura de pantalla de la configuración del servicio vinculado de Amazon RDS para Oracle.":::

## <a name="connector-configuration-details"></a>Detalles de configuración del conector

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades específicas para el conector de Amazon RDS para Oracle.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado Amazon RDS para Oracle:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en **AmazonRdsForOracle**. | Sí |
| connectionString | Especifica la información necesaria para conectarse a la instancia de Amazon RDS para Oracle Database. <br/>También puede poner una contraseña en Azure Key Vault y extraer la configuración de `password` de la cadena de conexión. Consulte los siguientes ejemplos y el artículo [Almacenamiento de credenciales en Azure Key Vault](store-credentials-in-key-vault.md) con información detallada. <br><br>**Tipo de conexión que se admite**: para identificar su base de datos, puede usar el **SID de Amazon RDS para Oracle** o el **nombre de servicio de Amazon RDS para Oracle**:<br>- Si usa el SID: `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;`<br>- Si usa el nombre del servicio: `Host=<host>;Port=<port>;ServiceName=<servicename>;User Id=<username>;Password=<password>;`<br>En el caso de opciones avanzadas de conexión nativa de Amazon RDS para Oracle, puede optar por agregar una entrada en el archivo [TNSNAMES.ORA](http://www.orafaq.com/wiki/Tnsnames.ora) en el servidor de Amazon RDS para Oracle; por otro lado, en el servicio vinculado de Amazon RDS para Oracle, elija usar el tipo de conexión del nombre de servicio de Amazon RDS para Oracle y configure el nombre de servicio correspondiente. | Sí |
| connectVia | El [entorno de ejecución de integración](concepts-integration-runtime.md) que se usará para conectarse al almacén de datos. Obtenga más información en la sección [Requisitos previos](#prerequisites). Si no se especifica, se usa el valor predeterminado de Azure Integration Runtime. |No |

Si tiene varias instancias de Amazon RDS para Oracle para el escenario de conmutación por error, puede crear un servicio vinculado de Amazon RDS para Oracle y rellenar el host principal, el puerto, el nombre de usuario, la contraseña, entre otros, y agregar una nueva sección "**Propiedades de conexión adicionales**" con el nombre de propiedad `AlternateServers` y el valor `(HostName=<secondary host>:PortNumber=<secondary port>:ServiceName=<secondary service name>)`; no olvide los corchetes y ponga atención al uso de dos puntos (`:`) como separadores. Por ejemplo, el siguiente valor de servidores alternativos define dos servidores de bases de datos alternativos para la conmutación por error de conexiones: `(HostName=AccountingAmazonRdsForOracleServer:PortNumber=1521:SID=Accounting,HostName=255.201.11.24:PortNumber=1522:ServiceName=ABackup.NA.MyCompany)`.

Puede establecer más propiedades de conexión en la cadena de conexión, según su caso:

| Propiedad | Descripción | Valores permitidos |
|:--- |:--- |:--- |
| ArraySize |El número de bytes que el conector puede capturar en un solo recorrido de ida y vuelta de red. Por ejemplo, `ArraySize=‭10485760‬`.<br/><br/>Los valores mayores aumentan la capacidad de proceso al reducir el número de veces que se capturan los datos en la red. Los valores más pequeños aumentan el tiempo de respuesta, ya que hay menos retraso en la espera para que el servidor transmita datos. | Entero de 1 a 4294967296 (4 GB). El valor predeterminado es `60000`. El valor 1 no define el número de bytes, sino que indica que se asigna espacio para exactamente una fila de datos. |

Para habilitar el cifrado en la conexión de Amazon RDS para Oracle, tiene dos opciones:

-   Para usar **el cifrado Triple-DES (3DES) y el Estándar de cifrado avanzado (AES)** , en el lado servidor de Amazon RDS para Oracle, vaya a Oracle Advanced Security (OAS) y configure las opciones de cifrado. Para más información, consulte esta [documentación de Oracle](https://docs.oracle.com/cd/E11882_01/network.112/e40393/asointro.htm#i1008759). El conector de Amazon RDS para Oracle Application Development Framework (ADF) negocia automáticamente el método de cifrado para usar el que configura en OAS al establecer la conexión con Amazon RDS para Oracle.

-   Para usar **TLS**:

    1.  Obtenga la información del certificado TLS/SSL. Obtenga la información del certificado codificado con reglas de codificación distinguida (DER) de su certificado TLS/SSL y guarde la salida (----- Begin Certificate… End Certificate -----) como archivo de texto.

        ```
        openssl x509 -inform DER -in [Full Path to the DER Certificate including the name of the DER Certificate] -text
        ```

        **Ejemplo**: extraiga la información del certificado de DERcert.cer; luego, guarde la salida en cert.txt.

        ```
        openssl x509 -inform DER -in DERcert.cer -text
        Output:
        -----BEGIN CERTIFICATE-----
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
        XXXXXXXXX
        -----END CERTIFICATE-----
        ```
    
    2.  Compile `keystore` o `truststore`. El siguiente comando crea el archivo `truststore` con o sin contraseña en formato PKCS-12.

        ```
        openssl pkcs12 -in [Path to the file created in the previous step] -out [Path and name of TrustStore] -passout pass:[Keystore PWD] -nokeys -export
        ```

        **Ejemplo**: cree un archivo `truststore` PKCS12 denominado MyTrustStoreFile con una contraseña.

        ```
        openssl pkcs12 -in cert.txt -out MyTrustStoreFile -passout pass:ThePWD -nokeys -export  
        ```

    3.  Coloque el archivo `truststore` en la máquina del entorno de ejecución de integración autohospedado. Por ejemplo, coloque el archivo en C:\MyTrustStoreFile.
    4.  En el servicio, configure la cadena de conexión de Amazon RDS para Oracle con `EncryptionMethod=1` y el valor correspondiente `TrustStore`/`TrustStorePassword`. Por ejemplo, `Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;EncryptionMethod=1;TrustStore=C:\\MyTrustStoreFile;TrustStorePassword=<trust_store_password>`.

**Ejemplo**:

```json
{
    "name": "AmazonRdsForOracleLinkedService",
    "properties": {
        "type": "AmazonRdsForOracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;Password=<password>;"
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
    "name": "AmazonRdsForOracleLinkedService",
    "properties": {
        "type": "AmazonRdsForOracle",
        "typeProperties": {
            "connectionString": "Host=<host>;Port=<port>;Sid=<sid>;User Id=<username>;",
            "password": { 
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

En esta sección se proporciona una lista de las propiedades que admite el conjunto de datos de Amazon RDS for Oracle. Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte [Conjuntos de datos](concepts-datasets-linked-services.md). 

Para copiar datos desde Amazon RDS para Oracle, establezca la propiedad type del conjunto de datos en `AmazonRdsForOracleTable`. Se admiten las siguientes propiedades.

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en `AmazonRdsForOracleTable`. | Sí |
| esquema | Nombre del esquema. | No |
| table | Nombre de la tabla o vista. | No |
| tableName | Nombre de la tabla o vista con el esquema. Esta propiedad permite la compatibilidad con versiones anteriores. Para la nueva carga de trabajo use `schema` y `table`. | No |

**Ejemplo**:

```json
{
    "name": "AmazonRdsForOracleDataset",
    "properties":
    {
        "type": "AmazonRdsForOracleTable",
        "schema": [],
        "typeProperties": {
            "schema": "<schema_name>",
            "table": "<table_name>"
        },
        "linkedServiceName": {
            "referenceName": "<Amazon RDS for Oracle linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

En esta sección se proporciona una lista de las propiedades que admite el origen de Amazon RDS para Oracle. Para ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte [Canalizaciones](concepts-pipelines-activities.md). 

### <a name="amazon-rds-for-oracle-as-source"></a>Amazon RDS para Oracle como origen

>[!TIP]
>Para cargar datos desde Amazon RDS para Oracle de manera eficaz mediante la creación de particiones de datos, consulte [Copia en paralelo desde una Amazon RDS para Oracle](#parallel-copy-from-amazon-rds-for-oracle).

Para copiar datos desde Amazon RDS para Oracle, establezca el tipo de origen de la actividad de copia en `AmazonRdsForOracleSource`. En la sección **source** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en `AmazonRdsForOracleSource`. | Sí |
| oracleReaderQuery | Use la consulta SQL personalizada para leer los datos. Un ejemplo es `"SELECT * FROM MyTable"`.<br>Si habilita la carga con particiones, deberá enlazar todos los parámetros de partición integrados correspondientes en la consulta. Por ejemplo, consulte la sección [Copia en paralelo desde Amazon RDS para Oracle](#parallel-copy-from-amazon-rds-for-oracle). | No |
| partitionOptions | Especifica las opciones de creación de particiones de datos que se usan para cargar datos desde Amazon RDS para Oracle. <br>Los valores permitidos son: **None** (valor predeterminado), **PhysicalPartitionsOfTable** y **DynamicRange**.<br>Cuando se habilita una opción de partición (es decir, no `None`), el grado de paralelismo para cargar simultáneamente datos de una base de datos Amazon RDS para Oracle se controla mediante la configuración [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) en la actividad de copia. | No |
| partitionSettings | Especifique el grupo de configuración para la creación de particiones de datos. <br>Se aplica si la opción de partición no es `None`. | No |
| partitionNames | Lista de particiones físicas que deben copiarse. <br>Se aplica si la opción de partición es `PhysicalPartitionsOfTable`. Si usa una consulta para recuperar datos de origen, enlace `?AdfTabularPartitionName` en la cláusula WHERE. Por ejemplo, consulte la sección [Copia en paralelo desde Amazon RDS para Oracle](#parallel-copy-from-amazon-rds-for-oracle). | No |
| partitionColumnName | Especifique el nombre de la columna de origen **in integer type** que usará la creación de particiones por rangos para la copia en paralelo. Si no se especifica, se detectará automáticamente la clave principal de la tabla y se usará como columna de partición. <br>Se aplica si la opción de partición es `DynamicRange`. Si usa una consulta para recuperar datos de origen, enlace `?AdfRangePartitionColumnName` en la cláusula WHERE. Por ejemplo, consulte la sección [Copia en paralelo desde Amazon RDS para Oracle](#parallel-copy-from-amazon-rds-for-oracle). | No |
| partitionUpperBound | El valor máximo de la columna de partición para copiar datos. <br>Se aplica si la opción de partición es `DynamicRange`. Si usa una consulta para recuperar datos de origen, enlace `?AdfRangePartitionUpbound` en la cláusula WHERE. Por ejemplo, consulte la sección [Copia en paralelo desde Amazon RDS para Oracle](#parallel-copy-from-amazon-rds-for-oracle). | No |
| partitionLowerBound | El valor mínimo de la columna de partición para copiar datos. <br>Se aplica si la opción de partición es `DynamicRange`. Si usa una consulta para recuperar datos de origen, enlace `?AdfRangePartitionLowbound` en la cláusula WHERE. Por ejemplo, consulte la sección [Copia en paralelo desde Amazon RDS para Oracle](#parallel-copy-from-amazon-rds-for-oracle). | No |

**Ejemplo: copia de datos mediante una consulta básica sin partición**

```json
"activities":[
    {
        "name": "CopyFromAmazonRdsForOracle",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Amazon RDS for Oracle input dataset name>",
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
                "type": "AmazonRdsForOracleSource",
                "oracleReaderQuery": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="parallel-copy-from-amazon-rds-for-oracle"></a>Copia en paralelo desde Amazon RDS para Oracle

El conector de Amazon RDS para Oracle proporciona creación de particiones de datos integrada para copiar datos de Amazon RDS para Oracle en paralelo. Puede encontrar las opciones de creación de particiones de datos en la pestaña **Origen** de la actividad de copia.

:::image type="content" source="./media/connector-amazon-rds-for-oracle/connector-amazon-rds-for-oracle-partition-options.png" alt-text="Captura de pantalla de las opciones de partición.":::

Al habilitar la copia con particiones, el servicio ejecuta consultas en paralelo en el origen de Amazon RDS para Oracle para cargar los datos mediante particiones. El grado en paralelo se controla mediante el valor [`parallelCopies`](copy-activity-performance-features.md#parallel-copy) de la actividad de copia. Por ejemplo, si establece `parallelCopies` en cuatro, el servicio genera y ejecuta al mismo tiempo cuatro consultas de acuerdo con la configuración y la opción de partición que ha especificado, y cada consulta recupera una porción de datos de la base de datos de Amazon RDS para Oracle.

Se sugiere habilitar la copia en paralelo con la creación de particiones de datos, especialmente si se cargan grandes cantidades de datos de la base de datos Amazon RDS para Oracle. Estas son algunas configuraciones sugeridas para diferentes escenarios. Cuando se copian datos en un almacén de datos basado en archivos, se recomienda escribir en una carpeta como varios archivos (solo especifique el nombre de la carpeta), en cuyo caso el rendimiento es mejor que escribir en un único archivo.

| Escenario                                                     | Configuración sugerida                                           |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| Carga completa de una tabla grande con particiones físicas.          | **Opción de partición**: particiones físicas de la tabla. <br><br/>Durante la ejecución, el servicio detecta automáticamente las particiones físicas y copia los datos por particiones. |
| Carga completa de una tabla grande, sin particiones físicas, aunque con una columna de enteros para la creación de particiones de datos. | **Opciones de partición**: partición por rangos dinámica.<br>**Columna de partición**: especifique la columna usada para crear la partición de datos. Si no se especifica, se usa la columna de clave principal. |
| Cargue una gran cantidad de datos mediante una consulta personalizada con particiones físicas. | **Opción de partición**: particiones físicas de la tabla.<br>**Consulta**: `SELECT * FROM <TABLENAME> PARTITION("?AdfTabularPartitionName") WHERE <your_additional_where_clause>`.<br>**Nombre de la partición**: especifique los nombres de las particiones desde las que se copiarán los datos. Si no se especifican, el servicio detecta automáticamente las particiones físicas en la tabla que ha especificado en el conjunto de datos de Amazon RDS para Oracle.<br><br>Durante la ejecución, el servicio reemplaza `?AdfTabularPartitionName` por el nombre real de la partición y se lo envía a Amazon RDS para Oracle. |
| Carga de grandes cantidades de datos mediante una consulta personalizada, sin particiones físicas, aunque cuenta con una columna de enteros para la creación de particiones de datos. | **Opciones de partición**: partición por rangos dinámica.<br>**Consulta**: `SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>`.<br>**Columna de partición**: especifique la columna usada para crear la partición de datos. Puede crear particiones en la columna con un tipo de datos entero.<br>**Límite de partición superior** y **límite de partición inferior**: especifique si quiere filtrar en la columna de partición para recuperar solo los datos entre el intervalo inferior y el superior.<br><br>Durante la ejecución, el servicio reemplaza `?AdfRangePartitionColumnName`, `?AdfRangePartitionUpbound` y `?AdfRangePartitionLowbound` por el nombre real de la columna y los rangos de valores de cada partición y se los envía a Amazon RDS para Oracle. <br>Por ejemplo, si establece la columna de partición "ID" con un límite inferior de 1 y un límite superior de 80, con la copia en paralelo establecida en 4, el servicio recupera los datos de 4 particiones. Los identificadores están comprendidos entre [1, 20], [21, 40], [41, 60] y [61, 80] respectivamente. |

> [!TIP]
> Al copiar datos de una tabla sin particiones, puede usar la opción de partición "Dynamic range" (Intervalo dinámico) para crear particiones en una columna de enteros. Si los datos de origen no tienen este tipo de columna, puede aprovechar la función [ORA_HASH]( https://docs.oracle.com/database/121/SQLRF/functions136.htm) de la consulta de origen para generar una columna y usarla como columna de partición.

**Ejemplo: consulta con partición física**

```json
"source": {
    "type": "AmazonRdsForOracleSource",
    "query": "SELECT * FROM <TABLENAME> PARTITION(\"?AdfTabularPartitionName\") WHERE <your_additional_where_clause>",
    "partitionOption": "PhysicalPartitionsOfTable",
    "partitionSettings": {
        "partitionNames": [
            "<partitionA_name>",
            "<partitionB_name>"
        ]
    }
}
```

**Ejemplo: consulta con partición por rangos dinámica**

```json
"source": {
    "type": "AmazonRdsForOracleSource",
    "query": "SELECT * FROM <TABLENAME> WHERE ?AdfRangePartitionColumnName <= ?AdfRangePartitionUpbound AND ?AdfRangePartitionColumnName >= ?AdfRangePartitionLowbound AND <your_additional_where_clause>",
    "partitionOption": "DynamicRange",
    "partitionSettings": {
        "partitionColumnName": "<partition_column_name>",
        "partitionUpperBound": "<upper_value_of_partition_column>",
        "partitionLowerBound": "<lower_value_of_partition_column>"
    }
}
```

## <a name="lookup-activity-properties"></a>Propiedades de la actividad de búsqueda

Para obtener información detallada sobre las propiedades, consulte [Actividad de búsqueda](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Pasos siguientes
Para obtener una lista de almacenes de datos que la actividad de copia admite como orígenes y receptores, vea [Almacenes de datos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).
