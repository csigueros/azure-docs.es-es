---
title: Normalización de recursos
description: Más información sobre cómo Azure Purview evita los recursos duplicados en el mapa de datos mediante la normalización de recursos
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 07/23/2021
ms.openlocfilehash: 68092e55cfe53ecde6e6b8146608267e39df0557
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2021
ms.locfileid: "114670064"
---
# <a name="asset-normalization"></a>Normalización de recursos

Al ingerir recursos en el mapa de datos de Azure Purview, los distintos orígenes que actualizan el mismo recurso de datos pueden enviar nombres completos similares, pero ligeramente distintos. Aunque estos nombres completos representan el mismo recurso, pequeñas diferencias como un carácter adicional o un uso distinto de mayúsculas y minúsculas pueden hacer que estos recursos parezcan diferentes en la superficie. Para evitar almacenar entradas duplicadas y provocar confusión al consumir el catálogo de datos, Azure Purview aplica la normalización durante la ingesta para asegurarse de que todos los nombres completos del mismo tipo de entidad tienen el mismo formato.

Por ejemplo, se analiza en un blob de Azure con el nombre completo `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet`. Este blob también lo consume una canalización de Azure Data Factory que, después, agregará información de linaje al recurso. La canalización de ADF se puede configurar para leer el archivo como `https://myAccount.file.core.windows.net//myshare/folderA/folderB/my-file.parquet`. Aunque el nombre completo es diferente, esta canalización de ADF consume el mismo fragmento de datos. La normalización garantiza que todos los metadatos de Azure Blob Storage y Azure Data Factory son visibles en un solo recurso, `https://myaccount.file.core.windows.net/myshare/folderA/folderB/my-file.parquet`.

## <a name="normalization-rules"></a>Reglas de normalización

A continuación se muestran las reglas de normalización aplicadas por Azure Purview.

### <a name="encode-curly-brackets"></a>Codificación de llaves
Se aplica a: Todos los recursos

Antes: `https://myaccount.file.core.windows.net/myshare/{folderA}/folder{B/`

Después: `https://myaccount.file.core.windows.net/myshare/%7BfolderA%7D/folder%7BB/`

### <a name="trim-section-spaces"></a>Recorte de espacios de sección
Se aplica a: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Data Factory, Azure SQL Database, Instancia administrada de Azure SQL Database, grupo de Azure SQL, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Azure Data Share, Amazon S3

Antes: `https://myaccount.file.core.windows.net/myshare/  folder A/folderB   /`

Después: `https://myaccount.file.core.windows.net/myshare/folder A/folderB/`

### <a name="remove-hostname-spaces"></a>Eliminación de espacios de nombre de host
Se aplica a: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Instancia administrada de Azur SQL Database, grupo de Azure SQL, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Azure Data Share, Amazon S3

Antes: `https://myaccount .file. core.win dows. net/myshare/folderA/folderB/`

Después: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="remove-square-brackets"></a>Eliminación de corchetes 
Se aplica a: Azure SQL Database, Instancia administrada de Azure SQL Database, grupo de Azure SQL

Antes: `mssql://foo.database.windows.net/[bar]/dbo/[foo bar]`

Después: `mssql://foo.database.windows.net/bar/dbo/foo%20bar`

> [!NOTE]
> Se codificarán los espacios entre dos corchetes

### <a name="lowercase-scheme"></a>Esquema de minúsculas
Se aplica a: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Instancia administrada de Azure SQL Database, grupo de Azure SQL, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Amazon S3

Antes: `HTTPS://myaccount.file.core.windows.net/myshare/folderA/folderB/`

Después: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-hostname"></a>Nombre de host en minúsculas
Se aplica a: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure SQL Database, Instancia administrada de Azure SQL Database, grupo de Azure SQL, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Amazon S3

Antes: `https://myAccount.file.Core.Windows.net/myshare/folderA/folderB/`

Después: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-file-extension"></a>Extensión de archivo en minúsculas
Se aplica a: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Amazon S3

Antes: `https://myAccount.file.core.windows.net/myshare/folderA/data.TXT`

Después: `https://myaccount.file.core.windows.net/myshare/folderA/data.txt`

### <a name="remove-duplicate-slash"></a>Eliminación de filas duplicadas
Se aplica a: Azure Blob, Azure Files, Azure Data Lake Storage Gen1, Azure Data Lake Storage Gen2, Azure Data Factory, Azure SQL Database, Instancia administrada de Azure SQL Database, grupo de Azure SQL, Azure Cosmos DB, Azure Cognitive Search, Azure Data Explorer, Azure Data Share, Amazon S3

Antes: `https://myAccount.file.core.windows.net//myshare/folderA////folderB/`

Después: `https://myaccount.file.core.windows.net/myshare/folderA/folderB/`

### <a name="lowercase-adf-sections"></a>Secciones de ADF en minúsculas
Se aplica a: Azure Data Factory

Antes: `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/fooBar/providers/Microsoft.DataFactory/factories/fooFactory/pipelines/barPipeline/activities/barFoo`

Después: `/subscriptions/01234567-abcd-9876-0000-ba9876543210/resourceGroups/foobar/providers/microsoft.datafactory/factories/foofactory/pipelines/barpipeline/activities/barfoo`

### <a name="convert-to-adl-scheme"></a>Conversión a esquema ADL
Se aplica a: Azure Data Lake Storage Gen1

Antes: `https://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

Después: `adl://mystore.azuredatalakestore.net/folderA/folderB/abc.csv`

## <a name="next-steps"></a>Pasos siguientes

[Examine una cuenta de Azure Blob Storage](register-scan-azure-blob-storage-source.md) en el mapa de datos de Azure Purview. 