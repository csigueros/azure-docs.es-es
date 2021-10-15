---
title: Solución de problemas del conector de formato Parquet
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo solucionar problemas con el conector de formato Parquet en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: 64a49592609b2cb7fd262264bb9802de58db5f04
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390449"
---
# <a name="troubleshoot-the-parquet-format-connector-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas del conector de formato Parquet en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con el conector de formato Parquet en Azure Data Factory y Azure Synapse.

## <a name="error-code-parquetjavainvocationexception"></a>Código de error: ParquetJavaInvocationException

- **Mensaje**: `An error occurred when invoking java, message: %javaException;.`

- **Causas y recomendaciones**: diversas causas pueden provocar este error. Busque en la lista siguiente el análisis de las posibles causas y la recomendación relacionada.

    | Análisis de las causas                                               | Recomendación                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Si el mensaje de error contiene las cadenas "java.lang.OutOfMemory", "Java heap space" y "doubleCapacity", suele tratarse de un problema de administración de memoria en la versión anterior de Integration Runtime. | Si usa el entorno de ejecución de integración autohospedado y la versión es anterior a 3.20.7159.1, se recomienda actualizar a la versión más reciente. |
    | Si el mensaje de error contiene la cadena "java.lang.OutOfMemory", significa que el entorno de ejecución de integración no tiene suficientes recursos para procesar los archivos. | limite las ejecuciones simultáneas en Integration Runtime. En el caso del entorno de ejecución de integración autohospedado, escale verticalmente a una máquina eficaz con una memoria igual o superior a 8 GB. |
    | Cuando el mensaje de error contiene la cadena "NullPointerReference", podría tratarse de un error transitorio. | Vuelva a intentar la operación. Si el problema persiste, póngase en contacto con el soporte técnico. |

## <a name="error-code-parquetinvalidfile"></a>Código de error: ParquetInvalidFile

- **Mensaje**: `File is not a valid Parquet file.`

- **Causa**: Se trata de un problema con el archivo Parquet.

- **Recomendación:**  Compruebe que la entrada sea un archivo Parquet válido.

## <a name="error-code-parquetnotsupportedtype"></a>Código de error: ParquetNotSupportedType

- **Mensaje**: `Unsupported Parquet type. PrimitiveType: %primitiveType; OriginalType: %originalType;.`

- **Causa**: el formato Parquet no se admite en las canalizaciones de Azure Data Factory y Synapse.

- **Recomendación**: vuelva a comprobar los datos de origen en [Formatos de archivo y códecs de compresión que admite la actividad de copia](./supported-file-formats-and-compression-codecs.md).

## <a name="error-code-parquetmisseddecimalprecisionscale"></a>Código de error: ParquetMissedDecimalPrecisionScale

- **Mensaje**: `Decimal Precision or Scale information is not found in schema for column: %column;.`

- **Causa**: La precisión y la escala del número se analizaron, pero no se proporcionó dicha información.

- **Recomendación:**  El origen no devuelve la información de precisión y escala correcta. Consulte la información en la columna con el problema.

## <a name="error-code-parquetinvaliddecimalprecisionscale"></a>Código de error: ParquetInvalidDecimalPrecisionScale

- **Mensaje**: `Invalid Decimal Precision or Scale. Precision: %precision; Scale: %scale;.`

- **Causa**: el esquema no es válido.

- **Recomendación:**  Compruebe la precisión y la escala de la columna con el problema.

## <a name="error-code-parquetcolumnnotfound"></a>Código de error: ParquetColumnNotFound

- **Mensaje**: `Column %column; does not exist in Parquet file.`

- **Causa**: El esquema de origen no coincide con el esquema de receptor.

- **Recomendación:**  Compruebe las asignaciones de la actividad. Asegúrese de que la columna de origen se pueda asignar a la columna de receptor correcta.

## <a name="error-code-parquetinvaliddataformat"></a>Código de error: ParquetInvalidDataFormat

- **Mensaje**: `Incorrect format of %srcValue; for converting to %dstType;.`

- **Causa**: Los datos no se pueden convertir al tipo especificado en mappings.source.

- **Recomendación:**  Vuelva a comprobar los datos de origen o especifique el tipo de datos correcto para esta columna en la asignación de columnas de la actividad de copia. Para obtener más información, vea [Formatos de archivo y códecs de compresión que admite la actividad de copia](./supported-file-formats-and-compression-codecs.md).

## <a name="error-code-parquetdatacountnotmatchcolumncount"></a>Código de error: ParquetDataCountNotMatchColumnCount

- **Mensaje**: `The data count in a row '%sourceColumnCount;' does not match the column count '%sinkColumnCount;' in given schema.`

- **Causa**:  Una desigualdad entre el número de columnas de origen y el número de columnas de receptor.

- **Recomendación:**  Vuelva a comprobar que el número de columnas de origen sea igual al número de columnas de receptor en "mapping".

## <a name="error-code-parquetdatatypenotmatchcolumntype"></a>Código de error: ParquetDataTypeNotMatchColumnType

- **Mensaje**: `The data type %srcType; is not match given column type %dstType; at column '%columnIndex;'.`

- **Causa**: Los datos del origen no se pueden convertir al tipo que se define en el receptor.

- **Recomendación:**  especifique un tipo correcto en mapping.sink.

## <a name="error-code-parquetbridgeinvaliddata"></a>Código de error: ParquetBridgeInvalidData

- **Mensaje**: `%message;`

- **Causa**: El valor de los datos ha superado el límite.

- **Recomendación:**  Vuelva a intentar la operación. Si el problema persiste, póngase en contacto con nosotros.

## <a name="error-code-parquetunsupportedinterpretation"></a>Código de error: ParquetUnsupportedInterpretation

- **Mensaje**: `The given interpretation '%interpretation;' of Parquet format is not supported.`

- **Causa**: Este escenario no se admite.

- **Recomendación:**  el valor de "ParquetInterpretFor" no debe ser "sparkSql".

## <a name="error-code-parquetunsupportfilelevelcompressionoption"></a>Código de error: ParquetUnsupportFileLevelCompressionOption

- **Mensaje**: `File level compression is not supported for Parquet.`

- **Causa**: Este escenario no se admite.

- **Recomendación:**  Quite "CompressionType" en la carga.

## <a name="error-code-usererrorjniexception"></a>Código de error: UserErrorJniException

- **Mensaje**: `Cannot create JVM: JNI return code [-6][JNI call failed: Invalid arguments.]`

- **Causa**: No se puede crear una Máquina virtual Java (JVM) porque se han establecido algunos argumentos no válidos (globales).

- **Recomendación:**  Inicie sesión en la máquina que hospeda *cada nodo* del entorno de ejecución de integración autohospedado. Asegúrese de que la variable del sistema esté configurada correctamente, como se indica a continuación: `_JAVA_OPTIONS "-Xms256m -Xmx16g" with memory bigger than 8 G`. Reinicie todos los nodos del entorno de ejecución de integración y vuelva a ejecutar la canalización.

## <a name="arithmetic-overflow"></a>Desbordamiento aritmético

- **Síntomas**: Ha aparecido un mensaje de error al copiar los archivos de Parquet: `Message = Arithmetic Overflow., Source = Microsoft.DataTransfer.Common`

- **Causa**: Actualmente solo se admite el decimal de precisión <= 38 y una longitud de la parte entera <= 20 al copiar archivos de Oracle en Parquet. 

- **Solución:** Como solución alternativa, puede convertir en VARCHAR2 cualquier columna que tenga este problema.

## <a name="no-enum-constant"></a>Ninguna constante de enumeración

- **Síntomas**: Ha aparecido un mensaje de error al copiar datos en formato Parquet: `java.lang.IllegalArgumentException:field ended by &apos;;&apos;` o: `java.lang.IllegalArgumentException:No enum constant org.apache.parquet.schema.OriginalType.test`.

- **Causa**: 

    El problema puede deberse a espacios en blanco o caracteres especiales no admitidos (como ,;{}()\n\t=) en el nombre de la columna, ya que Parquet no admite este formato. 

    Por ejemplo, un nombre de columna como *contoso(test)* analizará el tipo entre corchetes a partir del [código](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/MessageTypeParser.java) `Tokenizer st = new Tokenizer(schemaString, " ;{}()\n\t");`. El error se produce porque no hay ningún tipo "test".

    Para comprobar los tipos admitidos, vaya al [sitio apache/parquet-mr](https://github.com/apache/parquet-mr/blob/master/parquet-column/src/main/java/org/apache/parquet/schema/OriginalType.java) de GitHub.

- **Solución:** 

    Vuelva a comprobar que:
    - Haya espacios en blanco en el nombre de la columna de receptor.
    - La primera fila con espacios en blanco se use como nombre de columna.
    - Se admita el tipo OriginalType. Intente evitar el uso de estos caracteres especiales: `,;{}()\n\t=`. 

## <a name="error-code-parquetdatetimeexceedlimit"></a>Código de error: ParquetDateTimeExceedLimit

- **Mensaje**: `The Ticks value '%ticks;' for the datetime column must be between valid datetime ticks range -621355968000000000 and 2534022144000000000.`

- **Causa**: Si el valor datetime es "0001-01-01 00:00:00"', podría deberse a la diferencia entre el calendario Juliano y el calendario Gregoriano. Para obtener más detalles, vea [Diferencia entre los calendarios juliano y gregoriano proléptico](https://en.wikipedia.org/wiki/Proleptic_Gregorian_calendar#Difference_between_Julian_and_proleptic_Gregorian_calendar_dates).

- **Resolución**: compruebe el valor de los tics y evite usar el valor datetime "0001-01-01 00:00:00".

## <a name="error-code-parquetinvalidcolumnname"></a>Código de error: ParquetInvalidColumnName

- **Mensaje**: `The column name is invalid. Column name cannot contain these character:[,;{}()\n\t=]`

- **Causa**: el nombre de columna contiene caracteres no válidos.

- **Resolución**: agregue o modifique la asignación de columnas para que el nombre de la columna de receptor sea válido.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
