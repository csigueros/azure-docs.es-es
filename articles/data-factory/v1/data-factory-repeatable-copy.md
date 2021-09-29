---
title: Copia repetible en Azure Data Factory
description: Aprenda a evitar duplicados aunque un segmento que copia datos se ejecute más de una vez.
author: linda33wj
ms.service: data-factory
ms.subservice: v1
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: jingwang
robots: noindex
ms.openlocfilehash: 9dabe0767954d3ca19ea4b392c70641aa2f12c79
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128612802"
---
# <a name="repeatable-copy-in-azure-data-factory"></a>Copia repetible en Azure Data Factory

## <a name="repeatable-read-from-relational-sources"></a>Lectura repetible de orígenes relacionales
Cuando se copian datos desde almacenes de datos relacionales, hay que tener presente la repetibilidad para evitar resultados imprevistos. En Azure Data Factory, puede volver a ejecutar un segmento manualmente. También puede configurar la directiva de reintentos para un conjunto de datos con el fin de que un segmento se vuelva a ejecutar cuando se produce un error. Cuando se vuelve a ejecutar un segmento, debe asegurarse de que los mismos datos se lean sin importar el número de ejecuciones.  
 
> [!NOTE]
> Los ejemplos siguientes son para SQL Azure, pero son aplicables a cualquier almacén de datos que admita conjuntos de datos rectangulares. Puede que deba ajustar el **tipo** de origen y la propiedad de la **consulta** (por ejemplo, consulta en lugar de sqlReaderQuery) del almacén de datos.   

Normalmente, al leer desde almacenes relacionales, lo que quiere es leer únicamente los datos correspondientes a ese segmento. Una manera de hacerlo sería usar las variables WindowStart y WindowEnd disponibles en Azure Data Factory. Lea sobre las variables y funciones de Azure Data Factory aquí en el artículo [Azure Data Factory: funciones y variables del sistema](data-factory-functions-variables.md) . Ejemplo: 

```json
"source": {
    "type": "SqlSource",
    "sqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm\\'', WindowStart, WindowEnd)"
},
```
Esta consulta lee los datos que se encuentran en el intervalo de duración del segmento (WindowStart-> WindowEnd) de la tabla MyTable. Una segunda ejecución de este segmento también garantizará que se leen los mismos datos. 

En otros casos, puede que desee leer toda la tabla y puede definir el valor de sqlReaderQuery de la manera siguiente:

```json
"source": 
{            
    "type": "SqlSource",
    "sqlReaderQuery": "select * from MyTable"
},
```

## <a name="repeatable-write-to-sqlsink"></a>Escritura repetible en SqlSink
Cuando se copian datos en **Azure SQL o SQL Server desde** otros orígenes de datos, es necesario tener en mente la repetibilidad para evitar resultados imprevistos. 

Cuando se copien datos en Azure SQL o SQL Server Database, la actividad de copia anexa datos a la tabla del receptor de forma predeterminada. Supongamos que va a copiar datos de un archivo CSV (valores separados por comas) que contiene dos registros en la siguiente tabla en una instancia de Azure SQL o SQL Server Database. Cuando se ejecuta un segmento, se copian los dos registros en la tabla SQL. 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
```

Supongamos que encontró errores en el archivo de origen y actualizó la cantidad de Down Tube de 2 a 4. Si vuelve a ejecutar el segmento de datos durante ese período, encontrará dos nuevos registros anexados a Azure SQL o SQL Server Database. En este ejemplo se asume que ninguna de las columnas de la tabla tiene la restricción de clave principal.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    2            2015-05-01 00:00:00
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Para evitar este comportamiento, debe especificar una semántica UPSERT mediante uno de los dos mecanismos siguientes:

### <a name="mechanism-1-using-sqlwritercleanupscript"></a>Mecanismo 1: Uso de sqlWriterCleanupScript
Puede usar la propiedad **sqlWriterCleanupScript** para limpiar los datos de la tabla del receptor antes de insertar los datos cuando se ejecuta un segmento. 

```json
"sink":  
{ 
  "type": "SqlSink", 
  "sqlWriterCleanupScript": "$$Text.Format('DELETE FROM table WHERE ModifiedDate >= \\'{0:yyyy-MM-dd HH:mm}\\' AND ModifiedDate < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
}
```

Cuando se ejecuta un segmento, el script de limpieza se ejecuta primero para eliminar los datos que corresponden al segmento de la tabla de SQL. La actividad de copia inserta entonces datos en la tabla de SQL. Si se vuelve a ejecutar el segmento, la cantidad se actualiza según de la forma deseada.

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
6    Flat Washer    3            2015-05-01 00:00:00
7     Down Tube    4            2015-05-01 00:00:00
```

Supongamos que se quita el registro Flat Washer desde el archivo csv original. Después, si vuelve a ejecutar el segmento, se producirá el siguiente resultado: 

```
ID    Product        Quantity    ModifiedDate
...    ...            ...            ...
7     Down Tube    4            2015-05-01 00:00:00
```

La actividad de copia ejecutó el script de limpieza para eliminar los datos correspondientes para ese segmento. Luego leyó la entrada del archivo csv (que entonces solo contenía un registro) y la insertó en la tabla. 

### <a name="mechanism-2-using-sliceidentifiercolumnname"></a>Mecanismo 2: Uso de sliceIdentifierColumnName
> [!IMPORTANT]
> Actualmente, sliceIdentifierColumnName no se admite para Azure Synapse Analytics. 

El segundo mecanismo para lograr la repetibilidad es tener una columna dedicada (sliceIdentifierColumnName) en la tabla de destino. Esta columna debe usarAzure Data Factory para asegurarse de que el origen y el destino estén sincronizados. Este enfoque funciona cuando hay flexibilidad para cambiar o definir el esquema de la tabla SQL de destino. 

Azure Data Factory usa esta columna con fines de repetibilidad y, en el proceso, Azure Data Factory no realiza ningún cambio de esquema en la tabla. Forma de usar este enfoque:

1. Defina una columna de tipo **binario (32)** en la tabla DE SQL de destino. No debería haber ninguna restricción en esta columna. En este ejemplo, llamaremos a esta columna AdfSliceIdentifier.


    Tabla de origen:

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL
    )
    ```

    Tabla de destino: 

    ```sql
    CREATE TABLE [dbo].[Student](
       [Id] [varchar](32) NOT NULL,
       [Name] [nvarchar](256) NOT NULL,
       [AdfSliceIdentifier] [binary](32) NULL
    )
    ```

1. Úselo en la actividad de copia de la forma siguiente:
   
    ```json
    "sink":  
    { 
   
        "type": "SqlSink", 
        "sliceIdentifierColumnName": "AdfSliceIdentifier"
    }
    ```

Azure Data Factory rellena esta columna según sus necesidades para asegurarse de que el origen y el destino permanecen sincronizados. Los valores de esta columna no deben usarse fuera de este contexto. 

Similar al mecanismo de 1, la actividad de copia limpia automáticamente los datos del segmento especificado de la tabla de SQL de destino. Luego, inserta datos del origen en la tabla de destino. 

## <a name="next-steps"></a>Pasos siguientes
Revise los siguientes artículos de conectores para ver ejemplos completos de JSON: 

- [Azure SQL Database](data-factory-azure-sql-connector.md)
- [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md)
- [SQL Server](data-factory-sqlserver-connector.md)
