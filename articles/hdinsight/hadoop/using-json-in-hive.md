---
title: 'Análisis y proceso de JSON con Apache Hive: Azure HDInsight'
description: Aprenda a utilizar documentos JSON y a analizarlos mediante Apache Hive en Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 0c1067b2e2002992d75311497b1ba8f984c421b8
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112370408"
---
# <a name="process-and-analyze-json-documents-by-using-apache-hive-in-azure-hdinsight"></a>Procesamiento y análisis de documentos JSON mediante Apache Hive en Azure HDInsight

Aprenda a procesar y analizar los archivos JavaScript Object Notation (JSON) con Apache Hive en Azure HDInsight. En este artículo se usa el siguiente documento JSON:

```json
{
  "StudentId": "trgfg-5454-fdfdg-4346",
  "Grade": 7,
  "StudentDetails": [
    {
      "FirstName": "Peggy",
      "LastName": "Williams",
      "YearJoined": 2012
    }
  ],
  "StudentClassCollection": [
    {
      "ClassId": "89084343",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "High",
      "Score": 93,
      "PerformedActivity": false
    },
    {
      "ClassId": "78547522",
      "ClassParticipation": "NotSatisfied",
      "ClassParticipationRank": "None",
      "Score": 74,
      "PerformedActivity": false
    },
    {
      "ClassId": "78675563",
      "ClassParticipation": "Satisfied",
      "ClassParticipationRank": "Low",
      "Score": 83,
      "PerformedActivity": true
    }
  ]
}
```

El archivo se encuentra en `wasb://processjson@hditutorialdata.blob.core.windows.net/`. Para obtener más información sobre cómo usar Azure Blob Storage con HDInsight, consulte el artículo sobre el [uso de Azure Blob Storage compatible con HDFS con Apache Hadoop en HDInsight](../hdinsight-hadoop-use-blob-storage.md). Puede copiar el archivo en el contenedor predeterminado del clúster.

En este artículo se usa la consola de Apache Hive. Para instrucciones sobre cómo abrir la consola de Hive, consulte [Uso de Apache Ambari Hive View con Apache Hadoop en HDInsight](apache-hadoop-use-hive-ambari-view.md).

> [!NOTE]  
> Hive View ya no está disponible en HDInsight 4.0.

## <a name="flatten-json-documents"></a>Acoplamiento de documentos JSON

Los métodos enumerados en la siguiente sección requieren que el documento JSON conste de una sola fila. Por lo tanto, debe acoplar el documento JSON en una cadena. Si el documento JSON ya está acoplado, puede omitir este paso e ir directamente a la sección siguiente sobre el análisis de los datos JSON. Para acoplar el documento JSON, ejecute el script siguiente:

```sql
DROP TABLE IF EXISTS StudentsRaw;
CREATE EXTERNAL TABLE StudentsRaw (textcol string) STORED AS TEXTFILE LOCATION "wasb://processjson@hditutorialdata.blob.core.windows.net/";

DROP TABLE IF EXISTS StudentsOneLine;
CREATE EXTERNAL TABLE StudentsOneLine
(
  json_body string
)
STORED AS TEXTFILE LOCATION '/json/students';

INSERT OVERWRITE TABLE StudentsOneLine
SELECT CONCAT_WS(' ',COLLECT_LIST(textcol)) AS singlelineJSON
      FROM (SELECT INPUT__FILE__NAME,BLOCK__OFFSET__INSIDE__FILE, textcol FROM StudentsRaw DISTRIBUTE BY INPUT__FILE__NAME SORT BY BLOCK__OFFSET__INSIDE__FILE) x
      GROUP BY INPUT__FILE__NAME;

SELECT * FROM StudentsOneLine
```

El archivo JSON sin formato se encuentra en `wasb://processjson@hditutorialdata.blob.core.windows.net/`. La tabla de Hive **StudentsRaw** apunta al documento JSON sin formato que no está acoplado.

La tabla de Hive **StudentsOneLine** almacena los datos en el sistema de archivos predeterminado de HDInsight en la ruta de acceso **/json/students/** .

La instrucción **INSERT** rellena la tabla **StudentOneLine** con los datos de JSON acoplados.

La instrucción **SELECT** solo devuelve una fila.

Este es el resultado de la instrucción **SELECT**:

:::image type="content" source="./media/using-json-in-hive/hdinsight-flatten-json.png" alt-text="Acoplamiento de HDInsight del documento JSON" border="true":::

## <a name="analyze-json-documents-in-hive"></a>Análisis de documentos JSON en Hive

Hive proporciona tres mecanismos distintos para ejecutar consultas en documentos JSON, pero también puede escribir las suyas propias:

* Use la función definida por el usuario get_json_object.
* Use la función definida por el usuario json-tuple.
* Use el serializador/deserializador (SerDe) personalizado.
* Escriba su propia función definida por el usuario con Python u otros lenguajes. Para más información sobre cómo ejecutar su propio código Python con Hive, consulte [Funciones definidas por el usuario (UDF) de Python con Apache Hive y Apache Pig](./python-udf-hdinsight.md).

### <a name="use-the-get_json_object-udf"></a>Uso de la función definida por el usuario get_json_object

Hive proporciona una función definida por el usuario integrada llamada [get_json_object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object) que consulta el código JSON en tiempo de ejecución. Este método toma dos argumentos: el nombre de la tabla y el nombre del método. El nombre del método tiene el documento JSON plano y el campo JSON que debe analizarse. Veamos un ejemplo para ver cómo funciona esta UDF.

La siguiente consulta devuelve el nombre y el apellido de cada estudiante:

```sql
SELECT
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.FirstName'),
  GET_JSON_OBJECT(StudentsOneLine.json_body,'$.StudentDetails.LastName')
FROM StudentsOneLine;
```

Este es el resultado cuando se ejecuta esta consulta en la ventana de la consola:

:::image type="content" source="./media/using-json-in-hive/hdinsight-get-json-object.png" alt-text="Apache Hive obtiene la UDF de objeto JSON." border="true":::

La función definida por el usuario get-json_object tiene algunas limitaciones:

* Como cada campo de la consulta requiere volver a analizar la consulta, esto afecta al rendimiento.
* **GET\_JSON_OBJECT()** devuelve la representación de cadena de una matriz. Para convertir esta matriz en una de Hive, tiene que utilizar expresiones regulares para reemplazar los corchetes "[" y "]" y llamar también a split para obtener la matriz.

Esta conversión es el motivo por el que el sitio wiki de Hive recomienda **json_tuple**.  

### <a name="use-the-json_tuple-udf"></a>Uso de la función definida por el usuario json_tuple

Otra función definida por el usuario proporcionada por Hive se denomina [json_tuple](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-json_tuple), que es más eficaz que [get_ json _object](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF#LanguageManualUDF-get_json_object). Este método toma un conjunto de claves y una cadena JSON. A continuación, devuelve una tupla de valores. La siguiente consulta devuelve el identificador y el curso del estudiante del documento JSON:

```sql
SELECT q1.StudentId, q1.Grade
FROM StudentsOneLine jt
LATERAL VIEW JSON_TUPLE(jt.json_body, 'StudentId', 'Grade') q1
  AS StudentId, Grade;
```

Salida de este script en la consola de Hive:

:::image type="content" source="./media/using-json-in-hive/hdinsight-json-tuple.png" alt-text="Resultados de la consulta JSON de Apache Hive" border="true":::

La UDF `json_tuple` usa la sintaxis de [lateral view](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+LateralView) de Hive, que permite que json\_tuple cree una tabla virtual mediante la aplicación de la función de UDT a cada fila de tabla original. Los JSON complejos se vuelven demasiado difíciles de manejar debido al uso repetido de **LATERAL VIEW**. Además, **JSON_TUPLE** no puede controlar los documentos JSON anidados.

### <a name="use-a-custom-serde"></a>Uso de un SerDe personalizado

Un SerDe es la mejor opción para analizar documentos JSON anidados. Le permite definir el esquema JSON y utilizarlo para analizar los documentos. Para instrucciones, consulte [How to use a custom JSON SerDe with Microsoft Azure HDInsight](/archive/blogs/bigdatasupport/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight) (Uso de un SerDe personalizado para JSON con Microsoft Azure HDInsight).

## <a name="summary"></a>Resumen

El tipo de operador JSON en Hive que elija depende de su escenario. Con un documento JSON simple y un campo para buscar, elija la UDF de Hive **get_json_object**. Si tiene varias claves por las que buscar, puede usar **json_tuple**. En el caso de los documentos anidados, use **JSON SerDe**.

## <a name="next-steps"></a>Pasos siguientes

Para artículos relacionados, consulte:

* [Uso de Apache Hive y HiveQL con Apache Hadoop en HDInsight para analizar un archivo de Apache Log4j de muestra](./hdinsight-use-hive.md)
* [Análisis de datos de retraso de vuelos con Interactive Query en HDInsight](../interactive-query/interactive-query-tutorial-analyze-flight-data.md)
