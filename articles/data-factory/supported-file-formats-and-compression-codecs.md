---
title: Formatos de archivo que admite la actividad de copia en Azure Data Factory
description: En este tema se describen los formatos de archivo y los códecs de compresión que admiten las actividades de copia en Azure Data Factory.
author: jianleishen
ms.service: data-factory
ms.topic: conceptual
ms.date: 07/16/2020
ms.author: jianleishen
ms.openlocfilehash: 7e468b44636662a0654e1ca7770e9df3094da0f6
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/19/2021
ms.locfileid: "110086471"
---
# <a name="supported-file-formats-and-compression-codecs-by-copy-activity-in-azure-data-factory"></a>Formatos de archivo y códecs de compresión que admite la actividad de copia en Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

*Este artículo se aplica a los siguientes conectores: [Amazon S3](connector-amazon-simple-storage-service.md), [Amazon S3 Compatible Storage](connector-amazon-s3-compatible-storage.md), [Azure Blob](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), [Azure File Storage](connector-azure-file-storage.md), [File System](connector-file-system.md), [FTP](connector-ftp.md), [Google Cloud Storage](connector-google-cloud-storage.md), [HDFS](connector-hdfs.md), [HTTP](connector-http.md), [Oracle Cloud Storage](connector-oracle-cloud-storage.md) y [SFTP](connector-sftp.md).*

[!INCLUDE [data-factory-v2-file-formats](includes/data-factory-v2-file-formats.md)] 

La [actividad de copia](copy-activity-overview.md) se puede usar para copiar archivos tal cual entre dos almacenes de datos basados en archivos, en cuyo caso los datos se copian de manera eficaz sin serialización ni deserialización. 

Además, también puede analizar o generar archivos con un formato determinado. Por ejemplo, puede hacer lo siguiente:

* Copiar datos de una base de datos de SQL Server y escribirlos en Azure Data Lake Storage Gen2 en formato Parquet.
* Copiar archivos en formato de texto (CSV) desde el sistema de archivos local y escribirlos en Azure Blob Storage en formato Avro
* Copiar archivos comprimidos del sistema de archivos local, descomprimirlos sobre la marcha y escribirlos en Azure Data Lake Storage Gen2.
* Copiar datos en formato de texto comprimido Gzip (CSV) de Azure Blob Storage y escribirlos en Azure SQL Database
* Muchas más actividades que requieren serialización y deserialización o compresión y descompresión.

## <a name="next-steps"></a>Pasos siguientes

Consulte los otros artículos de la actividad de copia:

- [Información general de la actividad de copia](copy-activity-overview.md)
- [Rendimiento de la actividad de copia](copy-activity-performance.md)
