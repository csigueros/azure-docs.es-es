---
title: Copia de datos de Google BigQuery
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo copiar datos de Google BigQuery en almacenes de datos receptores compatibles a través de una actividad de copia en una canalización de Azure Data Factory o Synapse Analytics.
ms.author: jianleishen
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: conceptual
ms.custom: synapse
ms.date: 09/09/2021
ms.openlocfilehash: aa59787675870483941f271778bbf5b907467668
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124815145"
---
# <a name="copy-data-from-google-bigquery-using-azure-data-factory-or-synapse-analytics"></a>Copia de datos de Google BigQuery con Azure Data Factory o Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se resume el uso de la actividad de copia en canalizaciones de Azure Data Factory y Synapse Analytics para copiar datos de Google BigQuery. El documento se basa en el artículo de [introducción a la actividad de copia](copy-activity-overview.md) que presenta información general de la actividad de copia.

## <a name="supported-capabilities"></a>Funcionalidades admitidas

Este conector de Google BigQuery es compatible con las actividades siguientes:

- [Actividad de copia](copy-activity-overview.md) con [matriz de origen o receptor compatible](copy-activity-overview.md)
- [Actividad de búsqueda](control-flow-lookup-activity.md)

Puede copiar datos de Google BigQuery en cualquier almacén de datos de receptor compatible. Consulte la tabla de [almacenes de datos compatibles](copy-activity-overview.md#supported-data-stores-and-formats) para ver una lista de almacenes de datos que la actividad de copia admite como orígenes o receptores.

El servicio proporciona un controlador integrado para permitir la conectividad. Por lo tanto, no es necesario instalar manualmente uno para usar este conector.

>[!NOTE]
>Este conector de Google BigQuery se basa en las API de BigQuery. Tenga en cuenta que BigQuery limita la velocidad máxima de las solicitudes entrantes e impone cuotas adecuadas según cada proyecto; consulte las [cuotas y límites de las solicitudes de API](https://cloud.google.com/bigquery/quotas#api_requests) para más información. Asegúrese de no desencadenar demasiadas solicitudes simultáneas a la cuenta.

## <a name="get-started"></a>Introducción

[!INCLUDE [data-factory-v2-connector-get-started](includes/data-factory-v2-connector-get-started.md)]

## <a name="create-a-linked-service-to-google-bigquery-using-ui"></a>Creación de un servicio vinculado en Google BigQuery mediante la interfaz de usuario

Siga estos pasos para crear un servicio vinculado en Google BigQuery en la interfaz de usuario de Azure Portal.

1. Vaya a la pestaña Administrar del área de trabajo de Azure Data Factory o Synapse y seleccione Servicios vinculados; luego haga clic en Nuevo:

    # <a name="azure-data-factory"></a>[Azure Data Factory](#tab/data-factory)

    :::image type="content" source="media/doc-common-process/new-linked-service.png" alt-text="Captura de pantalla de la creación de un nuevo servicio vinculado con la interfaz de usuario de Azure Data Factory.":::

    # <a name="azure-synapse"></a>[Azure Synapse](#tab/synapse-analytics)

    :::image type="content" source="media/doc-common-process/new-linked-service-synapse.png" alt-text="Captura de pantalla de la creación de un nuevo servicio vinculado con la interfaz de usuario de Azure Synapse.":::

2. Busque Google y seleccione el conector de Google BigQuery.

    :::image type="content" source="media/connector-google-bigquery/google-bigquery-connector.png" alt-text="Captura de pantalla del conector de Google BigQuery.":::    

1. Configure los detalles del servicio, pruebe la conexión y cree el nuevo servicio vinculado.

    :::image type="content" source="media/connector-google-bigquery/configure-google-bigquery-linked-service.png" alt-text="Captura de pantalla de la configuración del servicio vinculado en Google BigQuery.":::

## <a name="connector-configuration-details"></a>Detalles de configuración del conector

En las secciones siguientes se proporcionan detalles sobre las propiedades que se usan para definir entidades específicas del conector de Google BigQuery.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de Google BigQuery:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en **GoogleBigQuery**. | Sí |
| proyecto | Identificador del proyecto predeterminado de BigQuery para el que se realizarán consultas.  | Sí |
| additionalProjects | Lista separada por comas de identificadores de proyectos públicos de BigQuery para su acceso.  | No |
| requestGoogleDriveScope | Si desea solicitar acceso a Google Drive. Al permitir el acceso a Google Drive, se habilita la compatibilidad para las tablas federadas que combinan datos de BigQuery con datos de Google Drive. El valor predeterminado es **false**.  | No |
| authenticationType | Mecanismo de autenticación OAuth 2.0 que se usa para autenticar. ServiceAuthentication solo puede usarse en Integration Runtime autohospedado. <br/>Los valores permitidos son: **UserAuthentication** y **ServiceAuthentication**. Consulte en las secciones después de esta tabla más propiedades y ejemplos de JSON para esos tipos de autenticación respectivamente. | Sí |

### <a name="using-user-authentication"></a>Uso de la autenticación de usuarios

Establezca la propiedad "authenticationType" en **UserAuthentication** y especifique las siguientes propiedades junto con las propiedades genéricas descritas en la sección anterior:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| clientId | Identificador de la aplicación usada para generar el token de actualización. | No |
| clientSecret | Secreto de la aplicación usado para generar el token de actualización. Marque este campo como SecureString para almacenarlo de forma segura, o bien [haga referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | No |
| refreshToken | El token de actualización obtenido de Google usado para autorizar el acceso a BigQuery. Aprenda cómo obtener uno en [Obtaining OAuth 2.0 access tokens](https://developers.google.com/identity/protocols/OAuth2WebServer#obtainingaccesstokens) (Obtención de tokens de acceso de OAuth 2.0) y este [blog de la comunidad](https://jpd.ms/getting-your-bigquery-refresh-token-for-azure-datafactory-f884ff815a59). Marque este campo como SecureString para almacenarlo de forma segura, o bien [haga referencia a un secreto almacenado en Azure Key Vault](store-credentials-in-key-vault.md). | No |

**Ejemplo**:

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project ID>",
            "additionalProjects" : "<additional project IDs>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "UserAuthentication",
            "clientId": "<id of the application used to generate the refresh token>",
            "clientSecret": {
                "type": "SecureString",
                "value":"<secret of the application used to generate the refresh token>"
            },
            "refreshToken": {
                "type": "SecureString",
                "value": "<refresh token>"
            }
        }
    }
}
```

### <a name="using-service-authentication"></a>Uso de la autenticación de servicio

Establezca la propiedad "authenticationType" en **ServiceAuthentication** y especifique las siguientes propiedades junto con las propiedades genéricas descritas en la sección anterior. Este tipo de autenticación solo puede usarse en Integration Runtime autohospedado.

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| email | El identificador de correo electrónico de la cuenta de servicio que se usa para ServiceAuthentication. Solo se puede usar en Integration Runtime autohospedado.  | No |
| keyFilePath | La ruta de acceso completa al archivo de clave. p12 que se usa para autenticar la dirección de correo electrónico de la cuenta de servicio. | No |
| trustedCertPath | La ruta de acceso completa del archivo .pem que contiene certificados de entidad de certificación de confianza usados para comprobar el servidor cuando se conecta a través de TLS. Esta propiedad solo puede establecerse cuando se usa TLS en el entorno de ejecución de integración autohospedado. El valor predeterminado es el archivo cacerts.pem instalado con Integration Runtime.  | No |
| useSystemTrustStore | Especifica si se usa un certificado de entidad de certificación del almacén de confianza del sistema o de un archivo .pem especificado. El valor predeterminado es **false**.  | No |

**Ejemplo**:

```json
{
    "name": "GoogleBigQueryLinkedService",
    "properties": {
        "type": "GoogleBigQuery",
        "typeProperties": {
            "project" : "<project id>",
            "requestGoogleDriveScope" : true,
            "authenticationType" : "ServiceAuthentication",
            "email": "<email>",
            "keyFilePath": "<.p12 key path on the IR machine>"
        },
        "connectVia": {
            "referenceName": "<name of Self-hosted Integration Runtime>",
            "type": "IntegrationRuntimeReference"
        }
    }
}
```

## <a name="dataset-properties"></a>Propiedades del conjunto de datos

Si desea ver una lista completa de las secciones y propiedades disponibles para definir conjuntos de datos, consulte el artículo sobre [conjuntos de datos](concepts-datasets-linked-services.md). En esta sección se proporciona una lista de las propiedades compatibles con el conjunto de datos de Google BigQuery.

Para copiar datos de Google BigQuery, establezca la propiedad type del conjunto de datos en **GoogleBigQueryObject**. Se admiten las siguientes propiedades:

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del conjunto de datos debe establecerse en: **GoogleBigQueryObject** | Sí |
| dataset | Nombre del conjunto de datos de Google BigQuery. |No (si se especifica "query" en el origen de la actividad)  |
| table | Nombre de la tabla. |No (si se especifica "query" en el origen de la actividad)  |
| tableName | Nombre de la tabla. Esta propiedad permite la compatibilidad con versiones anteriores. Para la nueva carga de trabajo use `dataset` y `table`. | No (si se especifica "query" en el origen de la actividad) |

**Ejemplo**

```json
{
    "name": "GoogleBigQueryDataset",
    "properties": {
        "type": "GoogleBigQueryObject",
        "typeProperties": {},
        "schema": [],
        "linkedServiceName": {
            "referenceName": "<GoogleBigQuery linked service name>",
            "type": "LinkedServiceReference"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Propiedades de la actividad de copia

Si desea ver una lista completa de las secciones y propiedades disponibles para definir actividades, consulte el artículo sobre [canalizaciones](concepts-pipelines-activities.md). En esta sección se proporciona una lista de las propiedades compatibles con el tipo de origen de Google BigQuery.

### <a name="googlebigquerysource-as-a-source-type"></a>GoogleBigQuerySource como tipo de origen

Para copiar datos de Google BigQuery, establezca el tipo de origen de la actividad de copia en **GoogleBigQuerySource**. En la sección **source** de la actividad de copia se admiten las siguientes propiedades.

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type del origen de la actividad de copia debe establecerse en **GoogleBigQuerySource**. | Sí |
| Query | Use la consulta SQL personalizada para leer los datos. Un ejemplo es `"SELECT * FROM MyTable"`. | No (si se especifica "tableName" en el conjunto de datos) |

**Ejemplo**:

```json
"activities":[
    {
        "name": "CopyFromGoogleBigQuery",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<GoogleBigQuery input dataset name>",
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
                "type": "GoogleBigQuerySource",
                "query": "SELECT * FROM MyTable"
            },
            "sink": {
                "type": "<sink type>"
            }
        }
    }
]
```

## <a name="lookup-activity-properties"></a>Propiedades de la actividad de búsqueda

Para obtener información detallada sobre las propiedades, consulte [Actividad de búsqueda](control-flow-lookup-activity.md).

## <a name="next-steps"></a>Pasos siguientes
Para obtener una lista de almacenes de datos que la actividad de copia admite como orígenes y receptores, vea [Almacenes de datos que se admiten](copy-activity-overview.md#supported-data-stores-and-formats).
