---
title: Funciones y variables del sistema de Data Factory
description: Proporciona una lista de funciones y variables del sistema de Azure Data Factory
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: fd2f85da574489f03aa536478366fea5a9027e46
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128611058"
---
# <a name="azure-data-factory---functions-and-system-variables"></a>Azure Data Factory: funciones y variables del sistema
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si utiliza la versión actual del servicio Data Factory, consulte [Variables del sistema en Data Factory V2](../control-flow-system-variables.md).

Este artículo proporciona información sobre las funciones y las variables compatibles con Azure Data Factory.

## <a name="data-factory-system-variables"></a>Variables del sistema de Data Factory

| Nombre de la variable | Descripción | Ámbito del objeto | Ámbito JSON y casos de uso |
| --- | --- | --- | --- |
| WindowStart |Inicio del intervalo de tiempo para la ventana de ejecución de la actividad actual |activity |<ol><li>Especifique las consultas de selección de datos. Consulte los artículos de conector indicados en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) .</li> |
| WindowEnd |Final del intervalo de tiempo para la ventana de ejecución de actividad actual |activity |igual que WindowStart. |
| SliceStart |Inicio del intervalo de tiempo para el segmento de datos que se está generando |activity<br/>dataset |<ol><li>Especifique rutas de acceso dinámicas a carpetas y nombres de archivo mientras trabaja con [Azure Blob](data-factory-azure-blob-connector.md) y [conjuntos de archivos del sistema de archivos](data-factory-onprem-file-system-connector.md).</li><li>Especifique las dependencias de entrada con funciones de factoría de datos en la colección de entradas de la actividad.</li></ol> |
| SliceEnd |Fin del intervalo de tiempo del segmento de datos actual. |activity<br/>dataset |igual que SliceStart. |

> [!NOTE]
> Actualmente, Data Factory requiere que la programación especificada en la actividad coincide exactamente con la especificada en la disponibilidad del conjunto de datos de salida. Por tanto, WindowStart, WindowEnd, SliceStart y SliceEnd siempre se asignan al mismo período y a un segmento de salida único.
> 

### <a name="example-for-using-a-system-variable"></a>Ejemplo de uso de una variable del sistema
En el siguiente ejemplo, el año, mes, día y hora de **SliceStart** se extraen en variables independientes que se usan en las propiedades **folderPath** y **fileName**.

```json
"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
"fileName": "{Hour}.csv",
"partitionedBy":
 [
    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } }
],
```

## <a name="data-factory-functions"></a>Funciones de Data Factory
Puede usar las funciones de Data Factory junto con las variables del sistema con los fines siguientes:

1. Especifique las consultas de selección de datos (consulte los artículos de conector mencionados en el artículo [Actividades de movimiento de datos](data-factory-data-movement-activities.md) .
   
   La sintaxis para invocar una función de Data Factory es: **$$\<function>** para las consultas de selección de datos y otras propiedades de la actividad y conjuntos de datos.  
2. Especifique las dependencias de entrada con funciones de factoría de datos en la colección de entradas de la actividad.
   
    $$ no es necesario para especificar expresiones de dependencia de entrada.     

En el ejemplo siguiente, la propiedad **sqlReaderQuery** de un archivo JSON se asigna a un valor devuelto por la función `Text.Format`. Este ejemplo también usa una variable de sistema denominada **WindowStart**, que representa la hora de inicio de la ventana de ejecución de la actividad.

```json
{
    "Type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('SELECT * FROM MyTable WHERE StartTime = \\'{0:yyyyMMdd-HH}\\'', WindowStart)"
}
```

Consulte el tema [Cadenas con formato de fecha y hora personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings), en el que se describen las diferentes opciones de formato que puede usar (por ejemplo: aa frente a aaaa). 

### <a name="functions"></a>Functions
Las tablas siguientes muestran todas las funciones de Azure Data Factory:

| Category | Función | Parámetros | Descripción |
| --- | --- | --- | --- |
| Time |AddHours(X,Y) |X: DateTime <br/><br/>Y: int |Agrega Y horas a la hora X determinada. <br/><br/>Ejemplo: `9/5/2013 12:00:00 PM + 2 hours = 9/5/2013 2:00:00 PM` |
| Time |AddMinutes(X,Y) |X: DateTime <br/><br/>Y: int |Agrega Y minutos a X.<br/><br/>Ejemplo: `9/15/2013 12: 00:00 PM + 15 minutes = 9/15/2013 12: 15:00 PM` |
| Time |StartOfHour(X) |X: Datetime |Obtiene la hora de inicio de la hora representada por el componente de hora de X. <br/><br/>Ejemplo: `StartOfHour of 9/15/2013 05: 10:23 PM is 9/15/2013 05: 00:00 PM` |
| Date |AddDays(X,Y) |X: DateTime<br/><br/>Y: int |Agrega Y días a X. <br/><br/>Ejemplo: 9/15/2013 12:00:00 PM + 2 días = 9/17/2013 12:00:00 PM.<br/><br/>También puede restar días especificando Y como un número negativo.<br/><br/>Ejemplo: `9/15/2013 12:00:00 PM - 2 days = 9/13/2013 12:00:00 PM`. |
| Date |AddMonths(X,Y) |X: DateTime<br/><br/>Y: int |Agrega Y meses a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 month = 10/15/2013 12:00:00 PM`.<br/><br/>También puede restar meses especificando Y como un número negativo.<br/><br/>Ejemplo: `9/15/2013 12:00:00 PM - 1 month = 8/15/2013 12:00:00 PM`.|
| Date |AddQuarters(X,Y) |X: DateTime <br/><br/>Y: int |Agrega Y * 3 meses a X.<br/><br/>Ejemplo: `9/15/2013 12:00:00 PM + 1 quarter = 12/15/2013 12:00:00 PM` |
| Date |AddWeeks(X,Y) |X: DateTime<br/><br/>Y: int |Agrega Y * 7 días a X.<br/><br/>Ejemplo: 9/15/2013 12:00:00 PM + 1 semana = 9/22/2013 12:00:00 PM<br/><br/>También puede restar semanas especificando Y como un número negativo.<br/><br/>Ejemplo: `9/15/2013 12:00:00 PM - 1 week = 9/7/2013 12:00:00 PM`. |
| Date |AddYears(X,Y) |X: DateTime<br/><br/>Y: int |Agrega Y años a X.<br/><br/>`Example: 9/15/2013 12:00:00 PM + 1 year = 9/15/2014 12:00:00 PM`<br/><br/>También puede restar años especificando Y como un número negativo.<br/><br/>Ejemplo: `9/15/2013 12:00:00 PM - 1 year = 9/15/2012 12:00:00 PM`. |
| Date |Day(X) |X: DateTime |Obtiene el componente de día de X.<br/><br/>Ejemplo: `Day of 9/15/2013 12:00:00 PM is 9`. |
| Date |DayOfWeek(X) |X: DateTime |Obtiene el día del componente de la semana de X.<br/><br/>Ejemplo: `DayOfWeek of 9/15/2013 12:00:00 PM is Sunday`. |
| Date |DayOfYear(X) |X: DateTime |Obtiene el día del año representado por el componente de año de X.<br/><br/>Ejemplos:<br/>`12/1/2015: day 335 of 2015`<br/>`12/31/2015: day 365 of 2015`<br/>`12/31/2016: day 366 of 2016 (Leap Year)` |
| Date |DaysInMonth(X) |X: DateTime |Obtiene los días del mes representado por el componente de mes del parámetro X.<br/><br/>Ejemplo: `DaysInMonth of 9/15/2013 are 30 since there are 30 days in the September month`. |
| Date |EndOfDay(X) |X: DateTime |Obtiene la fecha y hora que representa el final del día (componente de días) de X.<br/><br/>Ejemplo: `EndOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 11:59:59 PM`. |
| Date |EndOfMonth(X) |X: DateTime |Obtiene el final del mes representado por el componente de mes del parámetro X. <br/><br/>Ejemplo: `EndOfMonth of 9/15/2013 05:10:23 PM is 9/30/2013 11:59:59 PM` (fecha y hora que representan el final del mes de septiembre) |
| Date |StartOfDay(X) |X: DateTime |Obtiene el inicio del día representado por el componente de día del parámetro X.<br/><br/>Ejemplo: `StartOfDay of 9/15/2013 05:10:23 PM is 9/15/2013 12:00:00 AM`. |
| DateTime |From(X) |X: String |Analiza la cadena X en una fecha y hora. |
| DateTime |Ticks(X) |X: DateTime |Obtiene la propiedad ticks del parámetro X. Un ciclo es igual a 100 nanosegundos. El valor de esta propiedad representa el número de ciclos transcurridos desde la medianoche del 1 de enero de 0001. |
| Texto |Format(X) |X: Variable de cadena |Formatea el texto (use la combinación `\\'` para aplicar el escape del carácter `'`).|

> [!IMPORTANT]
> Cuando se usa una función dentro de otra función, no es necesario usar el prefijo **$$** para la función interna. Por ejemplo: $$Text.Format('PartitionKey eq \\'my_pkey_filter_value\\' y RowKey ge \\'{0: yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(SliceStart, -6)). En este ejemplo, observe que el prefijo **$$** no se usa para la función **Time.AddHours**. 

#### <a name="example"></a>Ejemplo
En el ejemplo siguiente, los parámetros de entrada y salida de la actividad de Hive se determinan mediante el uso de la función `Text.Format` y la variable del sistema SliceStart. 

```json  
{
    "name": "HiveActivitySamplePipeline",
        "properties": {
    "activities": [
            {
            "name": "HiveActivitySample",
            "type": "HDInsightHive",
            "inputs": [
                    {
                    "name": "HiveSampleIn"
                    }
            ],
            "outputs": [
                    {
                    "name": "HiveSampleOut"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
                    "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                }
            }
            }
    ]
    }
}
```

### <a name="example-2"></a>Ejemplo 2

En el ejemplo siguiente, el parámetro DateTime de la actividad de procedimiento almacenado se determina mediante el uso de la función Text. y la variable SliceStart. 

```json
{
    "name": "SprocActivitySamplePipeline",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
            "start": "2016-08-02T00:00:00Z",
            "end": "2016-08-02T05:00:00Z",
        "isPaused": false
    }
}
```

### <a name="example-3"></a>Ejemplo 3
Para leer los datos del día anterior en lugar del día representado por SliceStart, utilice la función AddDays como se muestra en el ejemplo siguiente: 

```json
{
    "name": "SamplePipeline",
    "properties": {
        "start": "2016-01-01T08:00:00",
        "end": "2017-01-01T11:00:00",
        "description": "hive activity",
        "activities": [
            {
                "name": "SampleHiveActivity",
                "inputs": [
                    {
                        "name": "MyAzureBlobInput",
                        "startTime": "Date.AddDays(SliceStart, -1)",
                        "endTime": "Date.AddDays(SliceEnd, -1)"
                    }
                ],
                "outputs": [
                    {
                        "name": "MyAzureBlobOutput"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adftutorial\\hivequery.hql",
                    "scriptLinkedService": "StorageLinkedService",
                    "defines": {
                        "Year": "$$Text.Format('{0:yyyy}',WindowsStart)",
                        "Month": "$$Text.Format('{0:MM}',WindowStart)",
                        "Day": "$$Text.Format('{0:dd}',WindowStart)"
                    }
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "policy": {
                    "concurrency": 1,
                    "executionPriorityOrder": "OldestFirst",
                    "retry": 2,
                    "timeout": "01:00:00"
                }
            }
        ]
    }
}
```

Consulte el tema [Cadenas con formato de fecha y hora personalizado](/dotnet/standard/base-types/custom-date-and-time-format-strings), en el que se describen las diferentes opciones de formato que puede usar (por ejemplo: aa frente a aaaa).