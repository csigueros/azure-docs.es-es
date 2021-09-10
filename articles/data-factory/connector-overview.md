---
title: Introducción a los conectores
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre los conectores admitidos en las canalizaciones de Azure Synapse Analytics y Azure Data Factory.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 08/30/2021
ms.author: jianleishen
ms.openlocfilehash: be49d5563a1e267b6f9be7ad5b4fa4534a56f22c
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225622"
---
# <a name="azure-data-factory-and-azure-synapse-analytics-connector-overview"></a>Información general sobre los conectores de Azure Data Factory y Azure Synapse Analytics

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Las canalizaciones de Azure Synapse Analytics y Azure Data Factory admiten los siguientes almacenes de datos y formatos a través de las actividades de copia, flujo de datos, búsqueda, obtención de metadatos y eliminación. Haga clic en cada almacén de datos para obtener información detallada sobre las funcionalidades admitidas y las configuraciones correspondientes.

## <a name="supported-data-stores"></a>Almacenes de datos compatibles

[!INCLUDE [Connector overview](includes/data-factory-v2-connector-overview.md)]

## <a name="integrate-with-more-data-stores"></a>Integración con más almacenes de datos

Las canalizaciones de Synapse y Azure Data Factory pueden llegar a un conjunto más amplio de almacenes de datos que la lista mencionada anteriormente. Si necesita mover datos hacia o desde un almacén de datos que no está en la lista de conectores integrados del servicio, estas son algunas opciones extensibles:
- En el caso de bases de datos y almacenamiento de datos, normalmente puede encontrar un controlador ODBC correspondiente, con el que puede usar un [conector ODBC genérico](connector-odbc.md).
- Para las aplicaciones SaaS:
    - Si proporciona API de RESTful, puede usar un [conector de REST genérico](connector-rest.md).
    - Si tiene fuentes de OData, puede usar un [conector OData genérico](connector-odata.md).
    - Si proporciona API de SOAP, puede usar un [conector HTTP genérico](connector-http.md).
    - Si tiene un controlador ODBC, puede usar un [conector ODBC genérico](connector-odbc.md).
- Para otras opciones, compruebe si puede cargar o exponer datos como con cualquier almacén de datos compatible; por ejemplo, blobs de Azure, archivos, FTP, SFTP, etc. A continuación, deje que el servicio se recoja desde allí. Puede invocar un mecanismo de carga de datos personalizado a través de [funciones de Azure Functions](control-flow-azure-function-activity.md), [actividades personalizadas](transform-data-using-dotnet-custom-activity.md), [Databricks](transform-data-databricks-notebook.md)/[HDInsight](transform-data-using-hadoop-hive.md), [actividades web](control-flow-web-activity.md), etc.

## <a name="supported-file-formats"></a>Formatos de archivos admitidos

Se admiten los formatos de archivo siguientes. Consulte los artículos para conocer la configuración basada en el formato.

- [Formato Avro](format-avro.md)
- [Formato binario](format-binary.md)
- [Formato Common Data Model](format-common-data-model.md)
- [Formato de texto delimitado](format-delimited-text.md)
- [Formato Delta](format-delta.md)
- [Formato Excel](format-excel.md)
- [Formato JSON](format-json.md)
- [Formato ORC](format-orc.md)
- [Formato Parquet](format-parquet.md)
- [Formato XML](format-xml.md)

## <a name="next-steps"></a>Pasos siguientes

- [Actividad de copia](copy-activity-overview.md)
- [Asignación de Data Flow](concepts-data-flow-overview.md)
- [Actividad Lookup](control-flow-lookup-activity.md)
- [Actividad GetMetadata](control-flow-get-metadata-activity.md)
- [Eliminar actividad](delete-activity.md)
