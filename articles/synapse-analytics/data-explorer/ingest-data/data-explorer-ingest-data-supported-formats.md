---
title: Formatos de datos compatibles con el Explorador de datos de Azure Synapse para la ingesta
description: Obtenga información sobre los diversos formatos de datos y compresión compatibles con el Explorador de datos de Azure Synapse para la ingesta.
ms.topic: conceptual
ms.date: 11/02/2021
author: shsagir
ms.author: shsagir
ms.reviewer: tzgitlin
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: data-explorer
ms.openlocfilehash: f38325e37b548c7f8a99d38eacae26aa24756250
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478099"
---
# <a name="data-formats-supported-by-azure-synapse-data-explorer-for-ingestion-preview"></a>Formatos de datos compatibles con el Explorador de datos de Azure Synapse para la ingesta (versión preliminar)

La ingesta de datos es el proceso por el que se agregan datos a una tabla y se ponen a disposición para su consulta en el Explorador de datos. En el caso de los métodos de ingesta que no sean la ingesta desde consulta, los datos deben tener uno de los formatos admitidos. En la siguiente tabla se enumeran y se describen los formatos que admite el Explorador de datos para la ingesta de datos.

> [!NOTE]
> Antes de ingerir datos, asegúrese de que tengan el formato correcto y de que definan los campos esperados. Se recomienda usar el validador de su elección para confirmar que el formato sea válido. Por ejemplo, puede encontrar útiles los siguientes validadores para comprobar archivos CSV y JSON:
>
> * CSV: http://csvlint.io/
> * JSON: https://jsonlint.com/
>
> Para obtener más información sobre por qué podría producirse un error en la ingesta, consulte [Errores de ingesta](/azure/data-explorer/kusto/management/ingestionfailures?context=/azure/synapse-analytics/context/context) y [Códigos de error de ingesta en el Explorador de datos](/azure/data-explorer/error-codes?context=/azure/synapse-analytics/context/context).

|Formato   |Extensión   |Descripción|
|---------|------------|-----------|
|ApacheAvro|`.avro`    |Formato [Avro](https://avro.apache.org/docs/current/) compatible con [tipos lógicos](https://avro.apache.org/docs/current/spec.html#Logical+Types). Se admiten los siguientes códecs de compresión: `null`, `deflate` y `snappy`. La implementación del lector del formato `apacheavro` se basa en la [biblioteca oficial de Apache Avro](https://github.com/apache/avro).|
|Avro     |`.avro`     |Una implementación heredada del formato [AVRO](https://avro.apache.org/docs/current/) basada en la [biblioteca de .NET](https://www.nuget.org/packages/Microsoft.Hadoop.Avro). Se admiten los siguientes codecs de compresión: `null` y `deflate` (para `snappy`, utilice el formato de datos `ApacheAvro`).|
|CSV      |`.csv`      |Un archivo de texto con valores separados por comas (`,`). Consulte [RFC 4180: _Formato común y tipo MIME para archivos de valores separados por comas (CSV)_](https://www.ietf.org/rfc/rfc4180.txt).|
|JSON     |`.json`     |Un archivo de texto con objetos JSON delimitados por `\n` o `\r\n`. Consulte [JSON Lines (JSONL)](http://jsonlines.org/).|
|MultiJSON|`.multijson`|Un archivo de texto con una matriz JSON de contenedores de propiedades (cada uno de los cuales representa un registro) o cualquier número de contenedores de propiedades delimitados por espacios en blanco, `\n` o `\r\n`. Cada contenedor de propiedades se puede distribuir en varias líneas. Este formato se prefiere a `JSON`, salvo que los datos sean contenedores sin propiedades.|
|ORC      |`.orc`      |Un [archivo ORC](https://en.wikipedia.org/wiki/Apache_ORC).|
|Parquet  |`.parquet`  |Un [archivo Parquet](https://en.wikipedia.org/wiki/Apache_Parquet). |
|PSV      |`.psv`      |Un archivo de texto con valores separados por barras verticales (<code>&#124;</code>).|
|RAW      |`.raw`      |Un archivo de texto cuyo contenido completo es un valor de cadena único.|
|SCsv     |`.scsv`     |Un archivo de texto con valores separados por punto y coma (`;`).|
|SOHsv    |`.sohsv`    |Un archivo de texto con valores separados por SOH. (SOH es un punto de código ASCII 1; este formato lo usa Hive en HDInsight).|
|TSV      |`.tsv`      |Un archivo de texto con valores separados por tabulaciones (`\t`).|
|TSVE     |`.tsv`      |Un archivo de texto con valores separados por tabulaciones (`\t`). Se utiliza un carácter de barra diagonal inversa (`\`) para el escape.|
|TXT      |`.txt`      |Un archivo de texto con líneas delimitadas por `\n`. Se omiten las líneas vacías.|
|W3CLOGFILE |`.log`    |Formato de [archivo de registro web](https://www.w3.org/TR/WD-logfile.html) normalizado por W3C. |

## <a name="supported-data-compression-formats"></a>Formatos de compresión de datos admitidos

Tanto los blobs como los archivos se pueden comprimir con cualquiera de los siguientes algoritmos de compresión:

|Compresión|Extensión|
|-----------|---------|
|GZip       |.gz      |
|Zip        |.zip     |

Indique la compresión anexando la extensión al nombre del blob o archivo.

Por ejemplo:

* `MyData.csv.zip` indica un blob o archivo con formato CSV, comprimido con ZIP (varios archivos o uno archivo).
* `MyData.json.gz` indica un blob o un archivo con formato JSON, comprimido con GZip.

Los nombres de blobs o archivos que no incluyen extensiones de formato y solo incluyen la compresión (por ejemplo, `MyData.zip`) también se admiten. En este caso, el formato de archivo se debe especificar como una propiedad de la ingesta ya que no se puede deducir.

> [!NOTE]
> * Algunos formatos de compresión mantienen un seguimiento de la extensión de archivo original como parte del flujo de compresión. Normalmente, esta extensión se omite para determinar el formato de archivo. Si este no se puede determinar a partir del nombre de archivo o blob (comprimido), debe especificarse mediante la propiedad de ingesta `format`.
> * No se debe confundir con el códec de compresión interno (nivel de fragmento) utilizado por los formatos `Parquet`, `AVRO` y `ORC`. El nombre de la compresión interna normalmente se agrega al nombre de archivo antes de la extensión de formato de archivo, por ejemplo: `file1.gz.parquet`, `file1.snappy.avro`, etc.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [ingesta de datos](data-explorer-ingest-data-overview.md)
- Más información sobre las [propiedades de la ingesta de datos](data-explorer-ingest-data-properties.md)
