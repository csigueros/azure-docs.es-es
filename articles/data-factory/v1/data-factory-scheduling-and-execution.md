---
title: Programación y ejecución con Data Factory
description: Obtenga información sobre los aspectos de programación y ejecución del modelo de aplicación de Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9cce6dfcda5c506f27c3527c5a417cbe712ebcca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661058"
---
# <a name="data-factory-scheduling-and-execution"></a>Programación y ejecución de Data Factory
> [!NOTE]
> Este artículo se aplica a la versión 1 de Data Factory. Si usa la versión actual del servicio Data Factory, vea [pipeline execution and triggers](../concepts-pipeline-execution-triggers.md) (ejecución de la canalización y desencadenadores).

En este artículo se explican los aspectos de programación y ejecución del modelo de aplicación de Azure Data Factory. En este artículo se presupone que comprende los conceptos básicos del modelo de aplicación de Data Factory, como la actividad, las canalizaciones, los servicios vinculados y los conjuntos de datos. Para los conceptos básicos de Azure Data Factory, consulte los artículos siguientes:

* [Introducción al servicio Factoría de datos de Azure](data-factory-introduction.md)
* [Canalizaciones](data-factory-create-pipelines.md)
* [Conjuntos de datos](data-factory-create-datasets.md) 

## <a name="start-and-end-times-of-pipeline"></a>Horas de inicio y finalización de la canalización
Una canalización solo está activa entre su hora de **inicio** y de **finalización**. No se ejecuta antes de la hora de inicio ni después de la hora de finalización. Si la canalización está en pausa, no se ejecuta, independientemente de su hora de inicio y de finalización. Para que se ejecute una canalización, no debe estar en pausa. Puede encontrar esta configuración (inicio, finalización y pausa) en la definición de la canalización: 

```json
"start": "2017-04-01T08:00:00Z",
"end": "2017-04-01T11:00:00Z"
"isPaused": false
```

Para más información sobre estas propiedades, vea el artículo sobre la [creación de canalizaciones](data-factory-create-pipelines.md). 


## <a name="specify-schedule-for-an-activity"></a>Definición de la programación de una actividad
La canalización en sí no se ejecuta. Son las actividades que incluye las que se ejecutan en el contexto general de la canalización. Puede especificar una programación recurrente para una actividad; para ello, use la sección **scheduler** de la actividad JSON. Por ejemplo, puede programar una actividad para que se ejecute cada hora de la manera siguiente:  

```json
"scheduler": {
    "frequency": "Hour",
    "interval": 1
},
```

Como se muestra en el diagrama siguiente, al especificar una programación de una actividad se crea una ventana de saltos de tamaño constante con las horas de inicio y finalización de la canalización. Las ventanas de saltos de tamaño constante son series de intervalos de tiempo de tamaño fijo, no superpuestos y contiguos. Estas ventanas de saltos lógicas de tamaño constante para la actividad se denominan **ventanas de actividad**.

:::image type="content" source="media/data-factory-scheduling-and-execution/scheduler-example.png" alt-text="Ejemplo de programador de actividades":::

La propiedad **scheduler** de una actividad es opcional. Si especifica esta propiedad, debe coincidir con la cadencia que indique en la definición del conjunto de datos de salida de la actividad. Actualmente, el conjunto de datos de salida es lo que impulsa la programación. Por tanto, debe crear un conjunto de datos de salida incluso si la actividad no produce ninguna salida. 

## <a name="specify-schedule-for-a-dataset"></a>Definición de la programación de un conjunto de datos
Una actividad de una canalización de Data Factory puede tomar diversos **conjuntos de datos** de entrada, o ninguno, y generar uno o varios conjuntos de datos de salida. Para una actividad, puede especificar la cadencia con que se encuentran disponibles los datos de entrada o con que se producen los datos de salida en la sección **availability** de las definiciones de los conjuntos de datos. 

La propiedad **frecuency** de la sección **availability** especifica la unidad de tiempo. Los valores permitidos para frequency son: Minute, Hour, Day, Week y Month. La propiedad **interval** de la sección availability especifica un multiplicador para la frecuencia. Por ejemplo, si la propiedad frecuency se establece en Day y la propiedad interval, en 1 para un conjunto de datos de salida, los datos de salida se generan diariamente. Si especifica frequency como Minute, se recomienda establecer interval en no menos de 15. 

En el ejemplo siguiente, los datos de entrada están disponibles cada hora y los datos de salida se generan cada hora (`"frequency": "Hour", "interval": 1`). 

**Conjunto de datos de entrada:** 

```json
{
    "name": "AzureSqlInput",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "MyTable"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}
```


**Conjunto de datos de salida**

```json
{
    "name": "AzureBlobOutput",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "mypath/{Year}/{Month}/{Day}/{Hour}",
            "format": {
                "type": "TextFormat"
            },
            "partitionedBy": [
                { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
                { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } },
                { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } },
                { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "HH" }}
            ]
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

Actualmente, **el conjunto de datos de salida impulsa la programación**. En otras palabras, la programación especificada para el conjunto de datos de salida se usa para ejecutar una actividad en tiempo de ejecución. Por tanto, debe crear un conjunto de datos de salida incluso si la actividad no produce ninguna salida. Si la actividad no toma ninguna entrada, puede omitir la creación del conjunto de datos de entrada. 

En la siguiente definición de la canalización, la propiedad **scheduler** se utiliza para especificar la programación de la actividad. Esta propiedad es opcional. Actualmente, la programación de la actividad debe coincidir con la programación especificada para el conjunto de datos de salida.
 
```json
{
    "name": "SamplePipeline",
    "properties": {
        "description": "copy activity",
        "activities": [
            {
                "type": "Copy",
                "name": "AzureSQLtoBlob",
                "description": "copy activity",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 100000,
                        "writeBatchTimeout": "00:05:00"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureSQLInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }
        ],
        "start": "2017-04-01T08:00:00Z",
        "end": "2017-04-01T11:00:00Z"
    }
}
```

En este ejemplo, la actividad se ejecuta cada hora entre las horas de inicio y finalización de la canalización. Los datos de salida se generan cada hora durante ventanas de tres horas (8:00 a 9:00, 9:00 a 10:00 y 10:00 a 11:00). 

Cada unidad de datos consumida o producida por la ejecución de una actividad se denomina **segmento de datos**. El diagrama siguiente muestra un ejemplo de una actividad con un conjunto de datos de entrada y uno de salida: 

:::image type="content" source="./media/data-factory-scheduling-and-execution/availability-scheduler.png" alt-text="Programador de disponibilidad":::

En el diagrama se muestran los segmentos de datos por hora para el conjunto de datos de entrada y salida. El diagrama muestra tres segmentos de entrada que están listos para su procesamiento. La actividad 10-11 AM está en curso, produciendo el segmento de salida 10-11 AM. 

Puede acceder al intervalo de tiempo asociado al segmento actual en el JSON del conjunto de datos con las variables [SliceStart](data-factory-functions-variables.md#data-factory-system-variables) y [SliceEnd](data-factory-functions-variables.md#data-factory-system-variables). De forma similar, puede acceder al intervalo de tiempo asociado con una ventana de actividad con las variables WindowStart y WindowEnd. La programación de una actividad debe coincidir con la programación del conjunto de datos de salida de la actividad. Por lo tanto, los valores de SliceStart y SliceEnd son los mismos que los valores de WindowStart y WindowEnd, respectivamente. Para más información sobre estas variables, vea el artículo [Funciones y variables del sistema](data-factory-functions-variables.md#data-factory-system-variables).  

Puede usar estas variables para distintos fines en el JSON de actividad. Por ejemplo, puede usarlas para seleccionar datos de conjuntos de datos de entrada y salida que representen datos de serie temporal (por ejemplo, de 8:00 a. m. a 9:00 a. m.). En el ejemplo también se usan **WindowStart** y **WindowEnd** para seleccionar datos pertinentes para la ejecución de una actividad y copiarlos en un blob con el valor de **folderPath** adecuado. **folderPath** se parametriza para tener una carpeta independiente para cada hora.  

En el ejemplo anterior, la programación especificada para los conjuntos de entrada y salida es la misma (hora). Si el conjunto de datos de entrada para la actividad está disponible en una frecuencia distinta, digamos cada 15 minutos, la actividad que genera este conjunto de datos de salida sigue ejecutándose una vez cada hora, ya que el conjunto de datos de salida es el que impulsa la programación de la actividad. Para más información, vea [Modelado de conjuntos de datos con distintas frecuencias](#model-datasets-with-different-frequencies).

## <a name="dataset-availability-and-policies"></a>Directivas y disponibilidad del conjunto de datos
Se ha analizado el uso de las propiedades frequency e interval en la sección availability de la definición del conjunto de datos. Hay otras propiedades que afectan a la programación y ejecución de una actividad. 

### <a name="dataset-availability"></a>Conjunto de datos availability 
La tabla siguiente describe las propiedades que puede utilizar en la sección de **disponibilidad**:

| Propiedad | Descripción | Obligatorio | Valor predeterminado |
| --- | --- | --- | --- |
| frequency |Especifica la unidad de tiempo para la producción de segmentos del conjunto de datos.<br/><br/><b>Frecuencia admitida</b>: Minute, Hour, Day, Week, Month |Sí |N/D |
| interval |Especifica un multiplicador para frecuencia<br/><br/>”Frequency x interval” determina la frecuencia con la que se produce el segmento.<br/><br/>Si necesita segmentar el conjunto de datos cada hora, establezca <b>frequency</b> en <b>hour</b> e <b>interval</b> en <b>1</b>.<br/><br/><b>Nota</b>: Si especifica Frequency como Minute, se recomienda establecer interval en no menos de 15 |Sí |N/D |
| style |Especifica si el segmento debe producirse al principio o al final del intervalo.<ul><li>StartOfInterval</li><li>EndOfInterval</li></ul><br/><br/>Si frequency se establece en Month y style se establece en EndOfInterval, el segmento se produce el último día del mes. Si style se establece en StartOfInterval, el segmento se produce el primer día del mes.<br/><br/>Si frequency se establece en Day y style se establece en EndOfInterval, el segmento se produce la última hora del día.<br/><br/>Si frequency se establece en Hour y style se establece en EndOfInterval, el segmento se produce al final de la hora. Por ejemplo, para un segmento para el período de 1 p.m. – 2 p.m., el segmento se producirá a las 2 p.m. |No |EndOfInterval |
| anchorDateTime |Define la posición absoluta en el tiempo usada por el programador para calcular los límites del segmento de conjunto de datos. <br/><br/><b>Nota</b>: Si AnchorDateTime tiene partes de fecha más pormenorizadas que la frecuencia, estas se omitirán. <br/><br/>Por ejemplo, si el valor de <b>interval</b> es <b>hourly</b> (frecuencia: hora e intervalo: 1) y <b>AnchorDateTime</b> contiene <b>minutes and seconds</b>, las partes <b>minutes and seconds</b> de AnchorDateTime no se tienen en cuenta. |No |01/01/0001 |
| offset |Intervalo de tiempo en función del cual se desplazan el inicio y el final de todos los segmentos del conjunto de datos. <br/><br/><b>Nota</b>: Si se especifican anchorDateTime y offset, el resultado es el desplazamiento combinado. |No |N/D |

### <a name="offset-example"></a>Ejemplo de offset
De forma predeterminada, cada día (`"frequency": "Day", "interval": 1`) se inician los segmentos a las 24:00 UTC (medianoche). Si desea que, en su lugar, la hora de inicio sea a las 6:00 UTC, defina el desplazamiento como se muestra en el siguiente fragmento de código: 

```json
"availability":
{
    "frequency": "Day",
    "interval": 1,
    "offset": "06:00:00"
}
```
### <a name="anchordatetime-example"></a>Ejemplo de anchorDateTime
En el ejemplo siguiente, el conjunto de datos se produce una vez cada 23 horas. El primer segmento se inicia a la hora especificada en anchorDateTime, que se define en `2017-04-19T08:00:00` (hora UTC).

```json
"availability":    
{    
    "frequency": "Hour",        
    "interval": 23,    
    "anchorDateTime":"2017-04-19T08:00:00"    
}
```

### <a name="offsetstyle-example"></a>Ejemplo de desplazamiento y estilo
El siguiente conjunto de datos es un conjunto de datos mensual y se produce el tercer día de cada mes a las 8:00 (`3.08:00:00`):

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "offset": "3.08:00:00", 
    "style": "StartOfInterval"
}
```

### <a name="dataset-policy"></a>Directiva del conjunto de datos
Un conjunto de datos puede tener una directiva de validación definida que especifique cómo se pueden validar los datos generados por la ejecución de un segmento antes de que esté listo para su uso. En estos casos, cuando el segmento ha terminado de ejecutarse, el estado del mismo cambia a **En espera** con un subestado de **Validación**. Una vez validados los segmentos, el estado del segmento cambia a **Listo**. Si se ha generado un segmento de datos, pero no ha pasado la validación, no se procesan las ejecuciones de actividad de los segmentos de nivel inferior que dependen de ese segmento. [Supervisión y administración de canalizaciones](data-factory-monitor-manage-pipelines.md) se tratan los diversos estados de los segmentos de datos en Data Factory.

En la sección **policy** de la definición del conjunto de datos se definen los criterios o condiciones que deben cumplir los segmentos del conjunto de datos. La tabla siguiente describe las propiedades que puede utilizar en la sección **policy**:

| Nombre de la directiva | Descripción | Aplicado a | Obligatorio | Valor predeterminado |
| --- | --- | --- | --- | --- |
| minimumSizeMB | Valida que los datos de un **blob de Azure** cumplen los requisitos de tamaño mínimo (en megabytes). |Blob de Azure |No |N/D |
| minimumRows | Comprueba que los datos de **Azure SQL Database** o de una **tabla de Azure** contengan el número mínimo de filas. |<ul><li>Azure SQL Database</li><li>tabla de Azure</li></ul> |No |N/D |

#### <a name="examples"></a>Ejemplos
**minimumSizeMB:**

```json
"policy":

{
    "validation":
    {
        "minimumSizeMB": 10.0
    }
}
```

**minimumRows**

```json
"policy":
{
    "validation":
    {
        "minimumRows": 100
    }
}
```

Para consultar ejemplos y más información sobre estas propiedades, vea el artículo [Creación de conjuntos de datos](data-factory-create-datasets.md). 

## <a name="activity-policies"></a>Directivas de la actividad
Las directivas afectan al comportamiento en tiempo de ejecución de una actividad, concretamente cuando se procesa el segmento de una tabla. En la tabla siguiente se proporciona los detalles.

| Propiedad | Valores permitidos | Valor predeterminado | Descripción |
| --- | --- | --- | --- |
| simultaneidad |Entero <br/><br/>Valor máximo: 10 |1 |Número de ejecuciones simultáneas de la actividad.<br/><br/>Determina el número de ejecuciones paralelas de la actividad que pueden tener lugar en distintos segmentos. Por ejemplo, si una actividad tiene que recorrer un gran conjunto de datos disponibles, tener un valor mayor de simultaneidad acelera el procesamiento de datos. |
| executionPriorityOrder |NewestFirst<br/><br/>OldestFirst |OldestFirst |Determina el orden de los segmentos de datos que se están procesando.<br/><br/>Por ejemplo, si tiene 2 segmentos (que tienen lugar uno a las 4 p.m. y el otro a las 5 p.m.) y ambos están pendientes de ejecución. Si establece que executionPriorityOrder sea NewestFirst, se procesará primero el segmento de las 5 p.m. De forma similar, si establece que executionPriorityORder sea OldestFIrst, se procesará el segmento de las 4 p.m. |
| retry |Entero<br/><br/>El valor máximo permitido es 10 |0 |Número de reintentos antes de que el procesamiento de datos del segmento se marque como error. La ejecución de la actividad de un segmento de datos se vuelve a intentar hasta el número de reintentos especificado. El reintento se realiza tan pronto como sea posible después del error. |
| timeout |TimeSpan |00:00:00 |Tiempo de espera para la actividad. Ejemplo: 00:10:00 (implica un tiempo de espera de 10 minutos)<br/><br/>Si un valor no se especifica o es 0, el tiempo de espera es infinito.<br/><br/>Si el tiempo de procesamiento de los datos en un segmento supera el valor de tiempo de espera, se cancela y el sistema vuelve a intentar el procesamiento. El número de reintentos depende de la propiedad retry. Si se excede el tiempo de espera, el estado será TimedOut. |
| delay |TimeSpan |00:00:00 |Especifica el retraso antes de iniciar el procesamiento de los datos del segmento.<br/><br/>La ejecución de la actividad de un segmento de datos se inicia una vez que transcurra el retraso más allá del tiempo de ejecución esperado.<br/><br/>Ejemplo: 00:10:00 (implica un retraso de 10 minutos) |
| longRetry |Entero<br/><br/>Valor máximo: 10 |1 |Número de reintentos largos antes de que la ejecución de los segmentos produzca error.<br/><br/>Los intentos de longRetry se espacian de acuerdo a longRetryInterval. Por tanto, si necesita especificar un tiempo entre reintentos, utilice longRetry. Si se especifican Retry y longRetry, cada intento de longRetry incluirá el número de intentos de Retry y el número máximo de intentos será Retry * longRetry.<br/><br/>Por ejemplo, si tenemos la siguiente configuración en la directiva de la actividad:<br/>Reintento: 3<br/>longRetry: 2<br/>longRetryInterval: 01:00:00<br/><br/>Se supone que existe un solo segmento para ejecutar (el estado es En espera) y la ejecución de la actividad no se puede realizar nunca. Inicialmente habría tres intentos consecutivos de ejecución. Después de cada intento, el estado del segmento sería Retry. Después de los 3 primeros intentos, el estado del segmento sería LongRetry.<br/><br/>Después de una hora (es decir, el valor de longRetryInteval), se produciría otro conjunto de 3 intentos consecutivos de ejecución. Después de eso, el estado del segmento sería Failed y ya no se realizarían más intentos. Por tanto, en total se realizaron 6 intentos.<br/><br/>Si una ejecución se realiza correctamente, el estado del segmento sería Ready y no se realizaría ningún otro reintento.<br/><br/>longRetry puede usarse en situaciones donde llegan datos dependientes a horas no deterministas o el entorno general en el que se produce el procesamiento de datos es poco confiable. En esos casos es posible que realizar reintentos uno tras otro no ayude, mientras que hacerlo después de un intervalo de tiempo puede generar el resultado deseado.<br/><br/>Advertencia: No establezca valores altos para longRetry o longRetryInterval. Normalmente, los valores más altos implican otros problemas sistémicos. |
| longRetryInterval |TimeSpan |00:00:00 |El retraso entre reintentos largos |

Para más información, vea el artículo [Canalizaciones](data-factory-create-pipelines.md). 

## <a name="parallel-processing-of-data-slices"></a>Procesamiento en paralelo de segmentos de datos
Puede establecer la fecha de inicio de la canalización en el pasado. Al hacerlo, Data Factory calcula automáticamente (relleno de fondo) todos los segmentos de datos en el pasado y empieza a procesarlos. Por ejemplo, si crea una canalización con la fecha de inicio 01-04-2017 y la fecha actual es 10-04-2017. Si la cadencia del conjunto de datos de salida es diaria, Data Factory empieza a procesar todos los segmentos desde el 01-04-2017 hasta el 09-04-2017 inmediatamente, porque la fecha de inicio se ha fijado en el pasado. El segmento del 10-04-2017 no se procesa todavía porque el valor de la propiedad style de la sección availability es EndOfInterval de forma predeterminada. El segmento más antiguo se procesa en primer lugar, ya que el valor predeterminado de executionPriorityOrder es OldestFirst. Para obtener una descripción de la propiedad style, vea la sección [Disponibilidad del conjunto de datos](#dataset-availability). Para obtener una descripción de la sección executionPriorityOrder, vea la sección [Directivas de la actividad](#activity-policies). 

Puede configurar segmentos de datos de relleno de fondo para que se procesen en paralelo estableciendo la propiedad **concurrency** en la sección **policy** de la actividad JSON. Esta propiedad determina el número de ejecuciones paralelas de la actividad que pueden tener lugar en distintos segmentos. El valor predeterminado de la propiedad concurrency es 1. Por tanto, se procesa un segmento a la vez de forma predeterminada. El valor máximo es 10. Si una actividad tiene que recorrer un gran conjunto de datos disponibles, tener un valor mayor de simultaneidad acelera el procesamiento de datos. 

## <a name="rerun-a-failed-data-slice"></a>Volver a ejecutar un segmento de datos con errores
Cuando se produce un error durante el procesamiento de un segmento de datos, puede averiguar la causa en las hojas de Azure Portal o con la Aplicación de supervisión y administración. Consulte [Supervisión y administración de canalizaciones mediante hojas de Azure Portal](data-factory-monitor-manage-pipelines.md) o [Aplicación de supervisión y administración](data-factory-monitor-manage-app.md) para más información.

Observe el ejemplo siguiente, que muestra dos actividades. Activity1 y Activity2. Activity1 consume un segmento de Dataset1 y genera un segmento de Dataset2, que Activity2 consume como entrada para generar un segmento de Final Dataset.

:::image type="content" source="./media/data-factory-scheduling-and-execution/failed-slice.png" alt-text="Segmento con errores":::

El diagrama muestra que en tres segmentos recientes se produjo un error al generar el segmento 9-10 AM para Dataset2. Data Factory realiza automáticamente el seguimiento de la dependencia para el conjunto de datos de series temporales. Como resultado, no se inicia la ejecución de actividad para el segmento 9-10 AM de bajada.

Las herramientas de administración y supervisión de Data Factory permiten profundizar en los registros de diagnóstico del segmento con error para encontrar fácilmente la causa principal del problema y solucionarlo. Una vez solucionado el problema, puede iniciar fácilmente la ejecución de la actividad para generar el segmento con error. Para más información sobre cómo repetir la ejecución y entender las transiciones de estado para segmentos de datos, vea [Supervisión y administración de canalizaciones en las hojas de Azure Portal](data-factory-monitor-manage-pipelines.md) o [Aplicación de supervisión y administración](data-factory-monitor-manage-app.md).

Tras haber repetido la ejecución del segmento 9-10 AM para **Dataset2**, Data Factory inicia la ejecución del segmento dependiente de 9-10 AM en el conjunto de datos final.

:::image type="content" source="./media/data-factory-scheduling-and-execution/rerun-failed-slice.png" alt-text="Repetición de ejecución de un segmento con errores":::

## <a name="multiple-activities-in-a-pipeline"></a>Varias actividades en una canalización
pero se pueden tener más de una actividad en una canalización. Si tiene varias actividades en una canalización y la salida de una actividad no es la entrada de otra actividad, las actividades se pueden ejecutar en paralelo si los segmentos de datos de entrada para las actividades están listos.

Puede encadenar dos actividades (ejecutar una después de otra) haciendo que el conjunto de datos de salida de una actividad sea el conjunto de datos de entrada de la otra actividad. Las actividades pueden estar en la misma canalización o en canalizaciones diferentes. La segunda actividad se ejecuta solo cuando la primera de ellas se completa correctamente.

Por ejemplo, observe el siguiente caso en el que una canalización tiene dos actividades:

1. La actividad A1 que requiere el conjunto de datos de entrada externo D1 y genera el conjunto de datos de salida D2.
2. La actividad A2 que requiere una entrada del conjunto de datos D2 y genera el conjunto de datos de salida D3.

En este escenario, las actividades A1 y A2 se encuentran en la misma canalización. La actividad A1 se ejecuta cuando los datos externos están disponibles y se alcanza la frecuencia de disponibilidad programada. La actividad A2 se ejecuta cuando están disponibles los segmentos programados de D2 y se alcanza la frecuencia de disponibilidad programada. Si se produce un error en uno de los segmentos del conjunto de datos D2, A2 no se ejecuta para ese segmento hasta que está disponible.

La vista Diagrama con ambas actividades en la misma canalización tendría un aspecto similar al siguiente diagrama:

:::image type="content" source="./media/data-factory-scheduling-and-execution/chaining-one-pipeline.png" alt-text="Encadenamiento de las actividades de la misma canalización":::

Tal y como se ha mencionado anteriormente, las actividades pueden estar en distintas canalizaciones. En tal escenario, la vista de diagrama tendría un aspecto similar al siguiente diagrama:

:::image type="content" source="./media/data-factory-scheduling-and-execution/chaining-two-pipelines.png" alt-text="Encadenamiento de las actividades de dos canalizaciones":::

Consulte la sección Copia secuencial del anexo para obtener un ejemplo.

## <a name="model-datasets-with-different-frequencies"></a>Modelado de conjuntos de datos con distintas frecuencias
En los ejemplos, las frecuencias de los conjuntos de datos de entrada y salida y de la ventana de programación de actividad eran las mismas. Algunos escenarios requieren que se puedan producir resultados a una frecuencia diferente de las frecuencias de una o más entradas. Data Factory admite el modelado de estos escenarios.

### <a name="sample-1-produce-a-daily-output-report-for-input-data-that-is-available-every-hour"></a>Muestra 1: Generación de un informe de salida diario para los datos de entrada que esté disponible cada hora
Considere un escenario en el que tiene datos de medida de entrada de sensores disponibles cada hora en Azure Blob Storage. Quiere generar un informe agregado diario con estadísticas como media, máximo y mínimo para el día con la [actividad de Hive de Data Factory](data-factory-hive-activity.md).

A continuación, se muestra cómo puede modelar este escenario con Data Factory:

**Conjunto de datos de entrada**

Se quitan los archivos de entrada de cada hora en la carpeta para el día especificado. La disponibilidad para la entrada se establece en **Hour** (frecuency: hour, interval: 1).

```json
{
  "name": "AzureBlobInput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Hour",
      "interval": 1
    }
  }
}
```
**Conjunto de datos de salida**

Cada día se crea un archivo de salida en la carpeta del día. La disponibilidad para la salida se establece en **Day** (frecuency: Day e interval: 1).

```json
{
  "name": "AzureBlobOutput",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Actividad: actividad de Hive en una canalización**

El script de Hive recibe la información adecuada de *DateTime* como parámetros que usan la variable **WindowStart** como se muestra a continuación. El script de Hive usa esta variable para cargar los datos de la carpeta correcta para el día y ejecutar la agregación para generar la salida.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
        {
            "name": "SampleHiveActivity",
            "inputs": [
                {
                    "name": "AzureBlobInput"
                }
            ],
            "outputs": [
                {
                    "name": "AzureBlobOutput"
                }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "type": "HDInsightHive",
            "typeProperties": {
                "scriptPath": "adftutorial\\hivequery.hql",
                "scriptLinkedService": "StorageLinkedService",
                "defines": {
                    "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
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

El diagrama siguiente muestra el escenario desde el punto de vista de la dependencia de datos.

:::image type="content" source="./media/data-factory-scheduling-and-execution/data-dependency.png" alt-text="Dependencia de los datos":::

El segmento de salida para cada día depende de 24 segmentos por hora del conjunto de datos de entrada. Data Factory calcula automáticamente estas dependencias al determinar los segmentos de datos de entrada que se encuentran en el mismo período de tiempo que el segmento de salida que se va a producir. Si cualquiera de los 24 segmentos de entrada no está disponible, Data Factory espera a que el segmento de entrada esté listo antes de empezar la ejecución de la actividad diaria.

### <a name="sample-2-specify-dependency-with-expressions-and-data-factory-functions"></a>Ejemplo 2: Especificación de la dependencia con expresiones y funciones de Data Factory
Consideremos otro escenario. Suponga que tiene una actividad de Hive que procesa dos conjuntos de datos de entrada. Uno de ellos tiene nuevos datos diariamente, pero otro obtiene datos nuevos cada semana. Supongamos que desea combinar las dos entradas y producir una salida cada día.

El enfoque sencillo, en el que Data Factory determina automáticamente los segmentos de entrada correctos que se van a procesar al alinearlos con el período de los segmentos de datos de salida, no funciona.

Debe especificar que para cada ejecución de actividad, Data Factory debe usar el segmento de datos de la semana pasada para el conjunto de datos de entrada semanal. Utilice funciones de Azure Data Factory, como se muestra en el siguiente fragmento para implementar este comportamiento.

**Input1: Azure blob**

La primera entrada es el blob de Azure que se actualiza diariamente.

```json
{
  "name": "AzureBlobInputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Input2: Azure blob**

Input2 es el blob de Azure que se actualiza semanalmente.

```json
{
  "name": "AzureBlobInputWeekly",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "external": true,
    "availability": {
      "frequency": "Day",
      "interval": 7
    }
  }
}
```

**Output: Azure blob**

Cada día se crea un archivo de salida en la carpeta del día. La disponibilidad para la salida se establece en **Day** (frecuency: Day, interval: 1).

```json
{
  "name": "AzureBlobOutputDaily",
  "properties": {
    "type": "AzureBlob",
    "linkedServiceName": "StorageLinkedService",
    "typeProperties": {
      "folderPath": "mycontainer/myfolder/{Year}/{Month}/{Day}/",
      "partitionedBy": [
        { "name": "Year", "value": {"type": "DateTime","date": "SliceStart","format": "yyyy"}},
        { "name": "Month","value": {"type": "DateTime","date": "SliceStart","format": "MM"}},
        { "name": "Day","value": {"type": "DateTime","date": "SliceStart","format": "dd"}}
      ],
      "format": {
        "type": "TextFormat"
      }
    },
    "availability": {
      "frequency": "Day",
      "interval": 1
    }
  }
}
```

**Actividad: actividad de Hive en una canalización**

La actividad de Hive toma las dos entradas y genera un segmento de salida cada día. Puede especificar de la forma siguiente el segmento de salida de cada día para que dependa de segmento de entrada de la semana pasada para la entrada semanal.

```json
{  
    "name":"SamplePipeline",
    "properties":{  
    "start":"2015-01-01T08:00:00",
    "end":"2015-01-01T11:00:00",
    "description":"hive activity",
    "activities": [
      {
        "name": "SampleHiveActivity",
        "inputs": [
          {
            "name": "AzureBlobInputDaily"
          },
          {
            "name": "AzureBlobInputWeekly",
            "startTime": "Date.AddDays(SliceStart, - Date.DayOfWeek(SliceStart))",
            "endTime": "Date.AddDays(SliceEnd,  -Date.DayOfWeek(SliceEnd))"  
          }
        ],
        "outputs": [
          {
            "name": "AzureBlobOutputDaily"
          }
        ],
        "linkedServiceName": "HDInsightLinkedService",
        "type": "HDInsightHive",
        "typeProperties": {
          "scriptPath": "adftutorial\\hivequery.hql",
          "scriptLinkedService": "StorageLinkedService",
          "defines": {
            "Year": "$$Text.Format('{0:yyyy}',WindowStart)",
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

Para conocer la lista de funciones y variables del sistema que admite Data Factory, consulte el artículo [Azure Data Factory: funciones y variables del sistema](data-factory-functions-variables.md) .

## <a name="appendix"></a>Apéndice

### <a name="example-copy-sequentially"></a>Ejemplo: copia secuencial
Es posible ejecutar varias operaciones de copia sucesivas de manera secuencial y ordenada. Por ejemplo, podría tener dos actividades de copia en una canalización (CopyActivity1 y CopyActivity2) con los siguientes conjuntos de datos de entrada y salida:   

CopyActivity1

Entrada: Dataset. Salida: Dataset2.

CopyActivity2

Entrada: Dataset2.  Salida: Dataset3.

ActividadCopia2 solo se ejecutaría si ActividadCopia1 se hubiera ejecutado correctamente y ConjuntoDatos2 estuviera disponible.

Esta es el JSON de canalización de ejemplo:

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob1ToBlob2",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset3"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob2ToBlob3",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2016-08-25T01:00:00Z",
        "end": "2016-08-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observe que en el ejemplo, el conjunto de datos de salida de la primera actividad de copia (ConjuntoDatos2) se especifica como entrada para la segunda. Por lo tanto, la segunda actividad solo se ejecuta cuando el conjunto de datos de salida de la primera está listo.  

En el ejemplo, CopyActivity2 puede tener una entrada distinta, como Dataset3, pero especifica Dataset2 como una entrada de CopyActivity2 para que la actividad no se ejecute hasta que se haya completado CopyActivity1. Por ejemplo:

CopyActivity1

Entrada: Dataset1. Salida: Dataset2.

CopyActivity2

Entradas: Dataset3, Dataset2. Salida: Dataset4.

```json
{
    "name": "ChainActivities",
    "properties": {
        "description": "Run activities in sequence",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "copyBehavior": "PreserveHierarchy",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset1"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset2"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlobToBlob",
                "description": "Copy data from a blob to another"
            },
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [
                    {
                        "name": "Dataset3"
                    },
                    {
                        "name": "Dataset2"
                    }
                ],
                "outputs": [
                    {
                        "name": "Dataset4"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00"
                },
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "CopyFromBlob3ToBlob4",
                "description": "Copy data from a blob to another"
            }
        ],
        "start": "2017-04-25T01:00:00Z",
        "end": "2017-04-25T01:00:00Z",
        "isPaused": false
    }
}
```

Observe que en el ejemplo, hay dos conjuntos de datos de entrada especificados para la segunda actividad de copia. Cuando se especifican varias entradas, solo se usa el primer conjunto de datos de entrada para copiar los datos. Sin embargo, los demás conjuntos de datos se usan como dependencias. CopyActivity2 empezaría solo después de que se cumplen las condiciones siguientes:

* ActividadCopia1 se ha completado correctamente y ConjuntoDatos2 está disponible. Este conjunto de datos no se usa al copiar datos en Dataset4. Solo actúa como una dependencia de programación de ActividadCopia2.   
* ConjuntoDatos3 está disponible. Este conjunto de datos representa los datos que se copian en el destino. 
