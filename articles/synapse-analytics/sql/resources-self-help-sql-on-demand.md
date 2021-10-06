---
title: 'Grupos de SQL sin servidor: autoayuda'
description: Esta sección contiene información que puede ayudarle a solucionar los problemas que surjan con grupos de SQL sin servidor.
services: synapse analytics
author: azaricstefan
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql
ms.date: 9/23/2021
ms.author: stefanazaric
ms.reviewer: jrasnick, wiassaf
ms.openlocfilehash: 35803ad7d63e107f71e71c6ce8292c5608740eec
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128555261"
---
# <a name="self-help-for-serverless-sql-pool"></a>Autoayuda para grupos de SQL sin servidor

Este artículo contiene información sobre cómo solucionar los problemas más frecuentes con grupos de SQL sin servidor en Azure Synapse Analytics.

## <a name="synapse-studio"></a>Synapse Studio

### <a name="serverless-sql-pool-is-grayed-out-in-synapse-studio"></a>El grupo de SQL sin servidor está atenuado en Synapse Studio

Si Synapse Studio no puede establecer la conexión con el grupo de SQL sin servidor, este aparecerá atenuado o con el estado "Sin conexión". Normalmente, este problema se da cuando se produce uno de los siguientes casos:

1) La red impide la comunicación con el back-end de Azure Synapse. El caso más frecuente es que el puerto 1443 está bloqueado. Para que el grupo de SQL sin servidor funcione, desbloquee este puerto. Hay otros problemas que pueden impedir el funcionamiento de un grupo de SQL sin servidor funcione. [Para más información, visite la guía para la solución de problemas](../troubleshoot/troubleshoot-synapse-studio.md).
2) No tiene permisos para iniciar sesión en un grupo de SQL sin servidor. Para obtener acceso, uno de los administradores del área de trabajo de Azure Synapse debe agregarle al administrador de áreas de trabajo o al rol de administrador de SQL. [Visite guía completa sobre el control de acceso para más información](../security/synapse-workspace-access-control-overview.md).

### <a name="query-fails-with-error-websocket-connection-was-closed-unexpectedly"></a>Se produce un error en la consulta: la conexión a Websocket se cerró inesperadamente.

Si se produce un error en la consulta que dice que la conexión a Websocket se cerró inesperadamente, significa que se interrumpió la conexión del explorador a Synapse Studio, por ejemplo, debido a un problema de red. 

Para resolver este problema, vuelva a ejecutar esta consulta. Si este mensaje se produce a menudo en su entorno, informe a su administrador de red, compruebe la configuración del firewall y consulte esta [guía de solución de problemas para más información](../troubleshoot/troubleshoot-synapse-studio.md). 

Si el problema continúa, cree una [incidencia de soporte técnico](../../azure-portal/supportability/how-to-create-azure-support-request.md) en Azure Portal e intente las mismas consultas con [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) o [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) en lugar de con Synapse Studio para investigar más a fondo el problema.

## <a name="query-execution"></a>Ejecución de consultas

### <a name="query-fails-because-file-cannot-be-opened"></a>Se produce un error en la consulta porque no se puede abrir el archivo

Si aparece en la consulta un error que indica que no se puede abrir el archivo porque no existe o porque lo está usando otro proceso, y está seguro de que el archivo existe y que no lo usa ningún otro proceso, significa que el grupo de SQL sin servidor no puede acceder al archivo. Este problema suele ocurrir porque la identidad de Azure Active Directory no tiene derechos para acceder al archivo o porque un firewall bloquea el acceso al archivo. De forma predeterminada, el grupo de SQL sin servidor intenta acceder al archivo mediante su identidad de Azure Active Directory. Para resolver este problema, debe tener los derechos adecuados para acceder al archivo. La manera más fácil es concederse el rol "Colaborador de datos de blobs de almacenamiento" en la cuenta de almacenamiento que está intentando consultar. 
- [Visite la guía completa sobre el control de acceso de Azure Active Directory para el almacenamiento para más información](../../storage/blobs/assign-azure-role-data-access.md). 
- [Visite Control del acceso a la cuenta de almacenamiento del grupo de SQL sin servidor en Azure Synapse Analytics](develop-storage-files-storage-access-control.md)

#### <a name="alternative-to-storage-blob-data-contributor-role"></a>Alternativa al rol Colaborador de datos de Storage Blob

En lugar de conceder el rol Colaborador de datos de Storage Blob, también puede conceder permisos más pormenorizados sobre un subconjunto de archivos. 

* Todos los usuarios que necesitan acceso a algunos datos de este contenedor también deben tener el permiso EXECUTE sobre todas las carpetas primarias hasta la raíz (el contenedor). Aprenda cómo [establecer listas ACL en Azure Data Lake Storage Gen2](../../storage/blobs/data-lake-storage-explorer-acl.md). 

> [!NOTE]
> El permiso de ejecución sobre el nivel de contenedor debe establecerse en Azure Data Lake Gen2.
> Los permisos sobre la carpeta se pueden establecer en Azure Synapse. 


Si quiere consultar data2.csv en este ejemplo, se necesitan los siguientes permisos: 
   - permiso de ejecución sobre el contenedor
   - permiso de ejecución sobre folder1 
   - permiso de lectura sobre data2.csv

![Dibujo que muestra la estructura de permisos en Data Lake.](./media/resources-self-help-sql-on-demand/folder-structure-data-lake.png)

* Inicie sesión en Azure Synapse con un usuario administrador que tenga permisos completos sobre los datos a los que quiere acceder.

* En el panel de datos, haga clic con el botón derecho en el archivo y seleccione ADMINISTRAR ACCESO.

![Captura de pantalla que muestra la interfaz de usuario para administrar el acceso.](./media/resources-self-help-sql-on-demand/manage-access.png)

* Elija al menos el permiso de "lectura", escriba el UPN de los usuarios o el identificador de objeto, por ejemplo, user@contoso.com y haga clic en Agregar.

* Conceda a este usuario permiso de lectura.
![Captura de pantalla que muestra la interfaz de usuario de concesión de permisos de lectura](./media/resources-self-help-sql-on-demand/grant-permission.png)

> [!NOTE]
> En el caso de los usuarios invitados, este paso debe hacerse directamente con el servicio Azure Data Lake, ya que no se puede hacer directamente mediante de Azure Synapse. 

### <a name="query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints"></a>Se produce un error en la consulta porque no se puede ejecutar debido a las restricciones de recursos actuales 

Si la consulta no se puede ejecutar y aparece un mensaje de error en el que se indica que la consulta no se puede ejecutar debido a las actuales restricciones de los recursos, significa que el grupo de SQL sin servidor no puede ejecutarla en este momento a causa de las restricciones de recursos: 

- Asegúrese de que se usan tipos de datos de tamaños razonables.  

- Si la consulta se dirige a archivos Parquet, considere la posibilidad de definir tipos explícitos para las columnas de cadena, ya que serán VARCHAR(8000) de forma predeterminada. [Comprobación de los tipos de datos inferidos](./best-practices-serverless-sql-pool.md#check-inferred-data-types)

- Si la consulta se dirige a archivos CSV, considere la posibilidad de [crear estadísticas](develop-tables-statistics.md#statistics-in-serverless-sql-pool). 

- Para optimizar la consulta, visite [los procedimientos recomendados para mejorar el rendimiento en los grupos de SQL sin servidor](./best-practices-serverless-sql-pool.md).  

### <a name="could-not-allocate-tempdb-space-while-transferring-data-from-one-distribution-to-another"></a>No se pudo asignar espacio de tempdb al transferir datos de una distribución a otra.

Este error es un caso especial de un [error en la consulta porque no se puede ejecutar debido a las restricciones de recursos actuales](#query-fails-because-it-cannot-be-executed-due-to-current-resource-constraints). Este error se devuelve cuando los recursos asignados a la base de datos `tempdb` no son suficientes para ejecutar la consulta. 

Aplique la misma mitigación y los procedimientos recomendados antes de presentar una incidencia de soporte técnico.

### <a name="query-fails-with-error-while-handling-an-external-file"></a>Se produce un error en la consulta al controlar un archivo externo. 

Si se produce un error en la consulta con un mensaje que dice que no se puede controlar el archivo externo y que se ha alcanzado el número máximo de errores, significa que hay un error de coincidencia de un tipo de columna especificado y es necesario cargar los datos. Para obtener más información sobre el error y las filas y columnas que se deben examinar, cambie la versión del analizador de "2.0" a "1.0". 

#### <a name="example"></a>Ejemplo
Si quisiera consultar el archivo "names.csv" con esta consulta 1, Azure Synapse SQL sin servidor devolvería este error. 

names.csv
```csv
Id,first name, 
1,Adam
2,Bob
3,Charles
4,David
5,Eva
```

Consulta 1:
```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='2.0',
       FIELDTERMINATOR =';',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] SMALLINT, 
    [Text] VARCHAR (1) COLLATE Latin1_General_BIN2 
)

    AS [result]
```
Causas:

`Error handling external file: ‘Max error count reached’. File/External table name: [filepath].`

En cuanto se cambia la versión del analizador de 2.0 a 1.0, los mensajes de error ayudan a identificar el problema. El nuevo mensaje de error es ahora: 

`Bulk load data conversion error (truncation) for row 1, column 2 (Text) in data file [filepath]`

El truncamiento nos indica que el tipo de columna es demasiado pequeño para nuestros datos. El nombre más largo de este archivo "names.csv" tiene siete caracteres. Por lo tanto, el tipo de datos correspondiente que se va a usar debe ser al menos VARCHAR(7). El error se debe a esta línea de código: 

```sql 
    [Text] VARCHAR (1) COLLATE Latin1_General_BIN2
```
Al cambiar la consulta como corresponde, se resuelve el error. Después de la depuración, cambie de nuevo la versión del analizador a 2.0 para lograr el máximo rendimiento. Lea más sobre cuándo usar la versión del analizador [aquí](develop-openrowset.md). 

```sql 
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='2.0',
        FIELDTERMINATOR =';',
        FIRSTROW = 2
    ) 
    WITH (
    [ID] SMALLINT, 
    [Text] VARCHAR (7) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

### <a name="query-fails-with-conversion-error"></a>La consulta produce un error de conversión
Si se produce un error en la consulta que dice que hay un error de conversión en la carga masiva de datos (errores de coincidencia de tipo o carácter no válido para la página de códigos especificada) en la fila n y la columna m [columnname] del archivo de datos [filepath], significa que los tipos de datos no coinciden con los datos reales en el número de fila n y la columna m. 

Por ejemplo, si solo espera enteros en los datos, pero en la fila n podría haber una cadena, este es el mensaje de error que recibirá. Para resolver este problema, inspeccione el archivo y los tipos de datos correspondientes que eligió. Compruebe también si la configuración del delimitador de filas y el terminador de campo es correcta. En el ejemplo siguiente se muestra cómo se puede realizar la inspección mediante VARCHAR como tipo de columna. Encuentre más información sobre terminadores de campo, delimitadores de filas y caracteres de comillas de escape [aquí](query-single-csv-file.md). 

#### <a name="example"></a>Ejemplo 
Si quisiera consultar el archivo "names.csv" con esta consulta 1, Azure Synapse SQL sin servidor devolvería este error. 

names.csv
```csv
Id, first name, 
1,Adam
2,Bob
3,Charles
4,David
five,Eva
```

Consulta 1:
```sql 
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =',',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] SMALLINT, 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

provoca este error: `Bulk load data conversion error (type mismatch or invalid character for the specified codepage) for row 6, column 1 (ID) in data file [filepath]`

Es necesario examinar los datos y tomar una decisión informada para controlar este problema. Para ver los datos que ocasionan este problema, primero es necesario cambiar el tipo de datos. En lugar de consultar la columna "ID" con el tipo de datos "SMALLINT", ahora se usa VARCHAR(100) para analizar este problema. Con esta consulta 2 ligeramente modificada, ahora se pueden procesar los datos y se muestra la lista de nombres. 

Consulta 2 
```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =',',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] VARCHAR(100), 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

names.csv
```csv
Id, first name, 
1,Adam
2,Bob
3,Charles
4,David
five,Eva
```

Parece que los datos tienen valores de ID inesperados en la quinta fila. En tales circunstancias, es importante trabajar con el propietario empresarial de los datos para acordar cómo se puede evitar dañar datos como estos. Si la prevención no es posible en el nivel de aplicación y es necesario trabajar con todo tipo de datos para el elemento de ID, la única opción posible podría ser ajustar VARCHAR a un tamaño razonable.

> [!Tip]
> Intente que VARCHAR() sea lo más corto posible. Evite VARCHAR(MAX) si es posible, ya que esta opción puede afectar al rendimiento. 

### <a name="the-result-table-does-not-look-like-expected-result-columns-are-empty-or-unexpected-loaded"></a>La tabla de resultados no tiene el aspecto esperado. Las columnas de resultados están vacías o se cargan de forma inesperada. 

Si la consulta no da error pero encuentra que la tabla de resultados no se carga según lo previsto, es probable que el delimitador de filas o el terminador de campo se hayan elegido incorrectamente. Para resolver este problema, es necesario examinar de nuevo los datos y cambiar esa configuración. Como se muestra una tabla de resultados, la depuración de esta consulta es fácil, como en el ejemplo siguiente. 

#### <a name="example"></a>Ejemplo
Si quisiera consultar el archivo "names.csv" con esta consulta 1, Azure Synapse SQL sin servidor devolvería esta tabla de resultados que parece extraña. 

names.csv
```csv
Id,first name, 
1,Adam
2,Bob
3,Charles
4,David
5,Eva
```

```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =';',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] VARCHAR(100), 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
```

provoca esta tabla de resultados

| ID            |   firstname   | 
| ------------- |-------------  | 
| 1,Adam        | NULL | 
| 2,Bob         | NULL | 
| 3,Charles     | NULL | 
| 4,David       | NULL | 
| 5,Eva         | NULL | 

Parece que no hay ningún valor en nuestra columna "firstname". En su lugar, todos los valores terminaron en la columna "ID". Esos valores van separados por una coma. El problema se debe a esta línea de código, ya que es necesario elegir la coma en lugar del símbolo de punto y coma como terminador de campo:

```sql
FIELDTERMINATOR =';',
```

El cambio de este carácter único resuelve el problema:

```sql
FIELDTERMINATOR =',',
```

La tabla de resultados creada por la consulta 2 tiene ahora el aspecto esperado. 

Consulta 2
```sql
SELECT
    TOP 100 *
FROM
    OPENROWSET(
        BULK '[FILE-PATH OF CSV FILE]',
        FORMAT = 'CSV',
        PARSER_VERSION='1.0',
       FIELDTERMINATOR =',',
       FIRSTROW = 2
    ) 
    WITH (
    [ID] VARCHAR(100), 
    [Firstname] VARCHAR (25) COLLATE Latin1_General_BIN2 
)

    AS [result]
``` 

devuelve

| ID            |   firstname   | 
| ------------- |-------------  | 
| 1        | Adam | 
| 2         | Bob | 
| 3     | Charles | 
| 4       | David | 
| 5         | Eva | 


### <a name="query-fails-with-error-column-column-name-of-type-type-name-is--not-compatible-with-external-data-type-external-data-type-name"></a>Se produce un error en la consulta que dice que la columna [column-name] de tipo [type-name] no es compatible con el tipo de datos externo [external-data-type-name] 

Si se produce un error en la consulta con el mensaje de error que dice que la columna [column-name] de tipo [type-name] no es compatible con tipos de datos externos [...], es probable que haya intentado asignar un tipo de datos de PARQUET al tipo de datos de SQL incorrecto. Por ejemplo, si el archivo parquet tiene un precio de columna con números flotantes (como 12,89) y ha intentado asignarlo a INT, este es el mensaje de error que recibirá. 

Para resolver este problema, inspeccione el archivo y los tipos de datos correspondientes que eligió. Esta [tabla de asignación](develop-openrowset.md#type-mapping-for-parquet) ayuda a elegir un tipo de datos SQL. Sugerencia de procedimiento recomendado: Especifique la asignación solo para las columnas que, si no, se resolverían en el tipo de datos VARCHAR. Evitar VARCHAR cuando sea posible conduce a un mejor rendimiento en las consultas. 

#### <a name="example"></a>Ejemplo
Si quisiera consultar el archivo "taxi-data.parquet" con esta consulta 1, Azure Synapse SQL sin servidor devolvería ese error.

taxi-data.parquet:

|PassengerCount |SumTripDistance|AvgTripDistance |
|---------------|---------------|----------------|
| 1 | 2635668.66000064 | 6.72731710678951 |
| 2 | 172174.330000005 | 2.97915543404919 |
| 3 | 296384.390000011 | 2.8991352022851  |
| 4 | 12544348.58999806| 6.30581582240281 |
| 5 | 13091570.2799993 | 111.065989028627 |

Consulta 1:
```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK '<filepath>taxi-data.parquet',
        FORMAT='PARQUET'
    )  WITh
        (
        PassengerCount INT, 
        SumTripDistance INT, 
        AVGTripDistance FLOAT
        )

    AS [result]
```

provoca este error: 

`Column 'SumTripDistance' of type 'INT' is not compatible with external data type 'Parquet physical type: DOUBLE', please try with 'FLOAT'. File/External table name: '<filepath>taxi-data.parquet'.`

Este mensaje de error nos indica que los tipos de datos no son compatibles y ya incluye la sugerencia de usar FLOAT en lugar de INT. Por lo tanto, el error se debe a esta línea de código: 

```sql
SumTripDistance INT, 
```

Con esta consulta 2 ligeramente modificada, ahora se pueden procesar los datos y se muestran las tres columnas. 

Consulta 2 
```sql
SELECT
    *
FROM
    OPENROWSET(
        BULK '<filepath>taxi-data.parquet',
        FORMAT='PARQUET'
    )  WITh
        (
        PassengerCount INT, 
        SumTripDistance FLOAT, 
        AVGTripDistance FLOAT
        )

    AS [result]
```

## <a name="configuration"></a>Configuración

### <a name="query-fails-with-please-create-a-master-key-in-the-database-or-open-the-master-key-in-the-session-before-performing-this-operation"></a>La consulta produce un error que le dice que cree una clave maestra en la base de datos o que abra la clave maestra en la sesión antes de realizar esta operación.

Si se produce un error en la consulta con el mensaje de error que le dice que cree una clave maestra en la base de datos o que abra la clave maestra en la sesión antes de realizar esta operación, significa que la base de datos de usuario no tiene acceso a una clave maestra en este momento. 

Lo más probable es que acabe de crear una base de datos de usuario y aún no haya creado una clave maestra. 

Para resolver este problema, cree una clave maestra con la siguiente consulta:

```sql
CREATE MASTER KEY [ ENCRYPTION BY PASSWORD ='password' ];
```

> [!NOTE]
> Reemplace aquí "password" por otro secreto. 

### <a name="create-statement-is-not-supported-in-master-database"></a>CREATE STATEMENT no se admite en la base de datos maestra

Si la consulta no se puede ejecutar y aparece el mensaje de error:

> "Failed to execute query. Error: CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT is not supported in master database" (No se pudo ejecutar la consulta. Error: CREATE EXTERNAL TABLE/DATA SOURCE/DATABASE SCOPED CREDENTIAL/FILE FORMAT no se admiten en la base de datos maestra). 

Esto significa que la base de datos maestra del grupo de SQL sin servidor no admite la creación de:
  - Tablas externas
  - Orígenes de datos externos
  - Credenciales cuyo ámbito es la base de datos
  - Formatos de archivo externos

Solución:

  1. Cree una base de datos de usuarios:

```sql
CREATE DATABASE <DATABASE_NAME>
```

  2. Ejecute la instrucción de creación en el contexto de <NOMBRE_BASE DE DATOS>, que produjo un error antes en la base de datos maestra. 
  
  Ejemplo de creación de formato de archivo externo:
    
```sql
USE <DATABASE_NAME>
CREATE EXTERNAL FILE FORMAT [SynapseParquetFormat] 
WITH ( FORMAT_TYPE = PARQUET)
```

### <a name="operation-is-not-allowed-for-a-replicated-database"></a>No se permite la operación para una base de datos replicada.
   
Si intenta crear algunos objetos o usuarios de SQL o cambiar permisos de una base de datos, es posible que reciba errores del tipo "No se permite la operación CREATE USER para la base de datos replicada". Este error se devuelve al intentar crear algunos objetos en una base de datos que se [comparte con el grupo de Spark](../metadata/database.md). Las bases de datos que se replican desde los grupos de Apache Spark son de solo lectura. No se pueden crear otros objetos en la base de datos replicada mediante T-SQL.

Cree una base de datos independiente y haga referencia a las [tablas](../metadata/table.md) sincronizadas mediante nombres de tres partes y consultas entre bases de datos.

## <a name="cosmos-db"></a>Cosmos DB

En la tabla siguiente se enumeran los posibles errores y las acciones para solucionar problemas.

| Error | Causa principal |
| --- | --- |
| Errores de sintaxis:<br/> - Sintaxis incorrecta cerca de `Openrowset`<br/> - `...` no es una opción de proveedor `BULK OPENROWSET` reconocida.<br/> - Sintaxis incorrecta cerca de `...` | Posibles causas principales:<br/> - No se usa CosmosDB como el primer parámetro.<br/> - Se usa un literal de cadena, en lugar de un identificador en el tercer parámetro.<br/> - No se especifica el tercer parámetro (nombre del contenedor). |
| Se produjo un error en la cadena de conexión de CosmosDB. | - No se ha especificado la cuenta, la base de datos ni la clave. <br/> - Hay una opción en una cadena de conexión que no se reconoce.<br/> - Un signo de punto y coma (`;`) se coloca al final de la cadena de conexión. |
| Error al resolver la ruta de acceso de CosmosDB: "Nombre de cuenta incorrecto" o "Nombre de base de datos incorrecto". | No se encuentra el nombre de cuenta, el nombre de la base de datos o el contenedor especificados, o bien no se han habilitado el almacenamiento analítico para la colección especificada.|
| Error al resolver la ruta de acceso de CosmosDB: "Valor de secreto incorrecto" o "El secreto es nulo o está vacío". | La clave de cuenta falta o no es válida. |
| La columna `column name` del tipo `type name` no es compatible con el tipo de datos externo `type name`. | El tipo de columna especificado en la cláusula `WITH` no coincide con el tipo del contenedor de Azure Cosmos DB. Intente cambiar el tipo de columna como se describe en la sección [Asignaciones de Azure Cosmos DB a tipos de SQL](query-cosmos-db-analytical-store.md#azure-cosmos-db-to-sql-type-mappings) o use el tipo `VARCHAR`. |
| La columna contiene valores `NULL` en todas las celdas. | Es posible que el nombre de columna o la expresión de la ruta de acceso sean incorrectos en la cláusula `WITH`. El nombre de columna, o la expresión de la ruta de acceso después del tipo de columna, de la cláusula `WITH` debe coincidir con el nombre de alguna propiedad de la colección de Azure Cosmos DB. La comparación *distingue mayúsculas de minúsculas*. Por ejemplo, `productCode` y `ProductCode` son propiedades diferentes. |

Puede informar sugerencias y problemas en la [página de comentarios de Azure Synapse Analytics](https://feedback.azure.com/forums/307516-azure-synapse-analytics?category_id=387862).

### <a name="utf-8-collation-warning-is-returned-while-reading-cosmosdb-string-types"></a>Se devuelve una advertencia de intercalación de UTF-8 al leer los tipos de cadena de CosmosDB

Un grupo de SQL sin servidor devolverá una advertencia en tiempo de compilación si la intercalación de columnas `OPENROWSET` no tiene codificación UTF-8. Puede cambiar fácilmente la intercalación predeterminada de todas las funciones `OPENROWSET` que se ejecutan en la base de datos actual mediante la instrucción T-SQL `alter database current collate Latin1_General_100_CI_AS_SC_UTF8`.

La [intercalación Latin1_General_100_BIN2_UTF8](best-practices-serverless-sql-pool.md#use-proper-collation-to-utilize-predicate-pushdown-for-character-columns) ofrece el mejor rendimiento al filtrar los datos mediante predicados de cadena.

### <a name="some-rows-are-not-returned"></a>No se devuelven algunas filas

- Hay un retraso en la sincronización entre el almacén transaccional y analítico. El documento que escribió en el almacén transaccional de Cosmos DB podría aparecer en el almacén analítico al cabo de 2 o 3 minutos.
- El documento podría infringir algunas [restricciones de esquema](../../cosmos-db/analytical-store-introduction.md#schema-constraints). 

### <a name="query-returns-null-values"></a>La consulta devuelve valores `NULL`

Azure Synapse SQL devolverá `NULL` en lugar de los valores que ve en el almacén de transacciones en los casos siguientes:
- Hay un retraso en la sincronización entre el almacén transaccional y analítico. El valor que escribió en el almacén transaccional de Cosmos DB podría aparecer en el almacén analítico al cabo de 2 o 3 minutos.
- Es posible que el nombre de columna o la expresión de la ruta de acceso sean incorrectos en la cláusula `WITH`. El nombre de columna (o la expresión de la ruta de acceso después del tipo de columna) de la cláusula `WITH` debe coincidir con los nombres de propiedad de un colección de Cosmos DB. La comparación distingue entre mayúsculas y minúsculas (por ejemplo, `productCode` y `ProductCode` son propiedades diferentes). Asegúrese de que los nombres de columna coinciden exactamente con los nombres de propiedad de Cosmos DB.
- La propiedad podría no moverse al almacenamiento analítico porque infringe algunas [restricciones de esquema](../../cosmos-db/analytical-store-introduction.md#schema-constraints), por ejemplo, más de 1000 propiedades o más de 127 niveles de anidamiento.
- Si usa una [representación de esquema](../../cosmos-db/analytical-store-introduction.md#schema-representation) bien definida, el valor del almacén transaccional podría tener un tipo incorrecto. Un esquema bien definido bloquea los tipos de cada propiedad mediante el muestreo de los documentos. Cualquier valor agregado en el almacén transaccional que no coincida con el tipo se trata como un valor incorrecto y no se migra al almacén analítico. 
- Si usa la [representación de esquema](../../cosmos-db/analytical-store-introduction.md#schema-representation) de máxima fidelidad, asegúrese de que agrega el sufijo de tipo después del nombre de propiedad, como `$.price.int64`. Si no ve un valor para la ruta de acceso a la que se hace referencia, quizás se almacene en una ruta de acceso de tipo diferente, por ejemplo, `$.price.float64`. Consulte [cómo consultar colecciones de Cosmos DB en el esquema de máxima fidelidad](query-cosmos-db-analytical-store.md#query-items-with-full-fidelity-schema).

### <a name="column-is-not-compatible-with-external-data-type"></a>La columna no es compatible con el tipo de datos externos

El valor especificado en la cláusula `WITH` no coincide con los tipos de Cosmos DB subyacentes en el almacenamiento analítico y no se puede convertir implícitamente. Use el tipo `VARCHAR` en el esquema.

### <a name="cosmosdb-performance-issues"></a>Incidencias de rendimiento de CosmosDB

Si experimenta algunos problemas de rendimiento inesperados, asegúrese de aplicar los procedimientos recomendados, como:
- Asegúrese de que ha colocado la aplicación cliente, el grupo sin servidor y el almacenamiento analítico de Cosmos DB en [la misma región](best-practices-serverless-sql-pool.md#colocate-your-cosmosdb-analytical-storage-and-serverless-sql-pool).
- Asegúrese de que usa la cláusula `WITH` con los [tipos de datos óptimos](best-practices-serverless-sql-pool.md#use-appropriate-data-types).
- Asegúrese de que usa la [intercalación Latin1_General_100_BIN2_UTF8](best-practices-serverless-sql-pool.md#use-proper-collation-to-utilize-predicate-pushdown-for-character-columns) al filtrar los datos mediante predicados de cadena.
- Si tiene consultas repetitivas que se podrían almacenar en caché, intente usar [CETAS para almacenar los resultados de la consulta en Azure Data Lake Storage](best-practices-serverless-sql-pool.md#use-cetas-to-enhance-query-performance-and-joins).

## <a name="delta-lake"></a>Delta Lake

La compatibilidad con Delta Lake se encuentra actualmente en versión preliminar pública en los grupos de SQL sin servidor. Hay algunos problemas conocidos que podría experimentar durante la versión preliminar.
- Asegúrese de que hace referencia a la carpeta raíz de Delta Lake en la función [OPENROWSET](./develop-openrowset.md) o en la ubicación de la tabla externa.
  - La carpeta raíz debe tener una subcarpeta denominada `_delta_log`. Se producirá un error en la consulta si no hay ninguna carpeta `_delta_log`. Si no ve esa carpeta, significa que hace referencia a archivos Parquet sin formato que se deben [convertir a Delta Lake](../spark/apache-spark-delta-lake-overview.md?pivots=programming-language-python#convert-parquet-to-delta) mediante grupos de Apache Spark.
  - No especifique caracteres comodín para describir el esquema de partición. La consulta de Delta Lake identificará automáticamente las particiones de Delta Lake. 
- Las tablas de Delta Lake creadas en los grupos de Apache Spark no están disponibles automáticamente en el grupo de SQL sin servidor. Para consultar estas tablas de Delta Lake mediante el lenguaje T-SQL, ejecute la instrucción [CREATE EXTERNAL TABLE](./create-use-external-tables.md#delta-lake-external-table) y especifique Delta como formato.
- Las tablas externas no admiten la creación de particiones. Use [vistas con particiones](create-use-views.md#delta-lake-partitioned-views) en la carpeta de Delta Lake para aprovechar la eliminación de particiones. Consulte a continuación los problemas conocidos y las soluciones alternativas.
- Los grupos de SQL sin servidor no admiten consultas de viaje en el tiempo. Puede votar por esta característica en el [sitio de comentarios de Azure](https://feedback.azure.com/forums/307516-azure-synapse-analytics/suggestions/43656111-add-time-travel-feature-in-delta-lake). Use grupos de Apache Spark en Azure Synapse Analytics para [leer datos históricos](../spark/apache-spark-delta-lake-overview.md?pivots=programming-language-python#read-older-versions-of-data-using-time-travel).
- Los grupos de SQL sin servidor no admiten la actualización de archivos de Delta Lake. Puede usar el grupo de SQL sin servidor para consultar la versión más reciente de Delta Lake. Use grupos de Apache Spark en Azure Synapse Analytics para [actualizar Delta Lake](../spark/apache-spark-delta-lake-overview.md?pivots=programming-language-python#update-table-data).
- Los grupos de SQL sin servidor en Azure Synapse Analytics no admiten conjuntos de datos con el [filtro BLOOM](/azure/databricks/delta/optimizations/bloom-filters).
- La compatibilidad con Delta Lake no está disponible en grupos de SQL dedicados. Asegúrese de que usa grupos sin servidor para consultar archivos de Delta Lake.

### <a name="content-of-directory-on-path-cannot-be-listed"></a>No se puede enumerar el contenido del directorio en la ruta de acceso

El siguiente error se devuelve cuando el grupo de SQL sin servidor no puede leer la carpeta del registro de transacciones de Delta Lake.

```
Msg 13807, Level 16, State 1, Line 6
Content of directory on path 'https://.....core.windows.net/.../_delta_log/*.json' cannot be listed.
```

Asegúrese de que existe esa carpeta `_delta_log` (quizás esté consultando archivos Parquet sin formato que no se convierten al formato Delta Lake).

Si la carpeta `_delta_log` existe, asegúrese de que tiene permisos de lectura y enumeración en las carpetas de Delta Lake subyacentes.
Intente leer los archivos \*.json directamente mediante FORMAT='CSV' (coloque el identificador URI en el parámetro BULK):

```sql
select top 10 * 
from openrowset(BULK 'https://.....core.windows.net/.../_delta_log/*.json', 
FORMAT='csv', FIELDQUOTE = '0x0b', FIELDTERMINATOR ='0x0b', ROWTERMINATOR = '0x0b') with (line varchar(max)) as logs
```

Si se produce un error en esta consulta, el autor de llamada no tiene permiso para leer los archivos del almacenamiento subyacente. 

La manera más fácil es concederse el rol "Colaborador de datos de blobs de almacenamiento" en la cuenta de almacenamiento que está intentando consultar. 
- [Visite la guía completa sobre el control de acceso de Azure Active Directory para el almacenamiento para más información](../../storage/blobs/assign-azure-role-data-access.md). 
- [Visite Control del acceso a la cuenta de almacenamiento del grupo de SQL sin servidor en Azure Synapse Analytics](develop-storage-files-storage-access-control.md)

### <a name="partitioning-column-returns-null-values"></a>La columna de creación de particiones devuelve valores NULL

**Estado**: resuelto

**Lanzamiento**: agosto de 2021

### <a name="query-failed-because-of-a-topology-change-or-compute-container-failure"></a>Error de consulta debido a un cambio de topología o a un error del contenedor de proceso

**Estado**: resuelto

**Lanzamiento**: agosto de 2021

### <a name="column-of-type-varchar-is-not-compatible-with-external-data-type-parquet-column-is-of-nested-type"></a>La columna de tipo "VARCHAR" no es compatible con el tipo de datos externo: "La columna Parquet es de tipo anidado"

Está intentando leer archivos de Delta Lake que contienen algunas columnas de tipo anidado sin especificar la cláusula WITH (mediante la inferencia automática de esquemas).

```sql
SELECT TOP 10 *
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/delta-lake/data-set-with-complex-type/',
    FORMAT = 'delta') as rows;
```

La inferencia automática de esquemas no funciona con las columnas anidadas en Delta Lake. Compruebe que la consulta devuelve algunos resultados si especifica FORMAT='parquet' y anexa ** a la ruta de acceso.

**Solución alternativa:** use la cláusula `WITH` y asigne explícitamente el tipo `VARCHAR` a las columnas anidadas. Tenga en cuenta que esto no funcionará si el conjunto de datos tiene particiones, debido a otro problema conocido en el que la cláusula `WITH` devuelve `NULL` para las columnas de partición. Actualmente no se admiten conjuntos de datos con particiones con columnas de tipo complejo.

### <a name="cannot-parse-field-type-in-json-object"></a>No se puede analizar el campo "type" en el objeto JSON

Está intentando leer archivos de Delta Lake que contienen algunas columnas de tipo anidado sin especificar la cláusula WITH (mediante la inferencia automática de esquemas). 

```sql
SELECT TOP 10 *
FROM OPENROWSET(
    BULK 'https://sqlondemandstorage.blob.core.windows.net/delta-lake/data-set-with-complex-type/',
    FORMAT = 'delta') as rows;
```

La inferencia automática de esquemas no funciona con las columnas anidadas en Delta Lake. Compruebe que la consulta devuelve algunos resultados si especifica FORMAT='parquet' y anexa ** a la ruta de acceso.

**Solución alternativa:** use la cláusula `WITH` y asigne explícitamente el tipo `VARCHAR` a las columnas anidadas. Tenga en cuenta que esto no funcionará si el conjunto de datos tiene particiones, debido a otro problema conocido en el que la cláusula `WITH` devuelve `NULL` para las columnas de partición. Actualmente no se admiten conjuntos de datos con particiones con columnas de tipo complejo.

### <a name="cannot-find-value-of-partitioning-column-in-file"></a>No se encuentra el valor de la columna de partición en el archivo 

Los conjuntos de datos de Delta Lake pueden tener valores `NULL` en las columnas de partición. Estas particiones se almacenan en la carpeta `HIVE_DEFAULT_PARTITION`. Este escenario no se admite actualmente en grupos de SQL sin servidor. En este caso, recibirá un error como este:

```
Resolving Delta logs on path 'https://....core.windows.net/.../' failed with error:
Cannot find value of partitioning column '<column name>' in file 
'https://......core.windows.net/...../<column name>=__HIVE_DEFAULT_PARTITION__/part-00042-2c0d5c0e-8e89-4ab8-b514-207dcfd6fe13.c000.snappy.parquet'.
```

**Solución alternativa:** pruebe a actualizar el conjunto de datos de Delta Lake mediante grupos de Apache Spark y use algún valor (cadena vacía o `"null"`) en lugar de `null` en la columna de creación de particiones.

### <a name="json-text-is-not-properly-formatted"></a>El texto JSON no tiene el formato correcto

Este error indica que el grupo de SQL sin servidor no puede leer el registro de transacciones de Delta Lake. Probablemente verá un error como el siguiente:

```
Msg 13609, Level 16, State 4, Line 1
JSON text is not properly formatted. Unexpected character '{' is found at position 263934.
Msg 16513, Level 16, State 0, Line 1
Error reading external metadata.
```
En primer lugar, asegúrese de que el conjunto de datos de Delta Lake no esté dañado.
- Compruebe que puede leer el contenido de la carpeta de Delta Lake mediante el grupo de Apache Spark en el clúster de Azure Synapse o Databricks. De este modo, se asegurará de que el archivo `_delta_log` no esté dañado.
- Compruebe que puede leer el contenido de los archivos de datos especificando `FORMAT='PARQUET'` y usando el carácter comodín recursivo `/**` al final de la ruta de acceso del URI. Si puede leer todos los archivos Parquet, el problema se encuentra en la carpeta del registro de transacciones `_delta_log`.

Errores comunes y soluciones alternativas:

- `JSON text is not properly formatted. Unexpected character '.'`: es posible que los archivos parquet subyacentes contengan algunos tipos de datos que no se admiten en el grupo de SQL sin servidor.

**Solución alternativa:** intente usar el esquema WITH que excluirá tipos no admitidos.

- `JSON text is not properly formatted. Unexpected character '{'`: es posible que use alguna intercalación de base de datos `_UTF8`. 

**Solución alternativa:** intente ejecutar una consulta en la base de datos `master` o en cualquier otra base de datos que tenga una intercalación que no sea UTF8. Si esta solución alternativa resuelve el problema, use una base de datos sin intercalación `_UTF8`. Especifique la intercalación `_UTF8` en la definición de columna de la cláusula `WITH`.

**Solución alternativa general**: intente crear un punto de control en el conjunto de datos de Delta Lake mediante un grupo de Apache Spark y vuelva a ejecutar la consulta. El punto de control agregará archivos de registro JSON transaccionales y podría resolver el problema.

Si el conjunto de datos es válido y la solución alternativa no sirve de ayuda, envíe una incidencia de soporte técnico y proporcione una reproducción a Soporte técnico de Azure:
- No realice ningún cambio, como agregar o quitar las columnas u optimizar la tabla, ya que esto podría cambiar el estado de los archivos de registro de transacciones de Delta Lake.
- Copie el contenido de la carpeta `_delta_log` en una nueva carpeta vacía. **NO** copie los archivos `.parquet data`.
- Intente leer el contenido que copió en la nueva carpeta y compruebe que recibe el mismo error.
- Ahora puede seguir usando la carpeta de Delta Lake con el grupo de Spark. Si tiene permiso para compartir esto, proporcionará los datos copiados al servicio de soporte técnico de Microsoft.
- Envíe el contenido del archivo copiado `_delta_log` a Soporte técnico de Azure.

El equipo de Azure investigará el contenido del archivo `delta_log` y proporcionará más información sobre los posibles errores y las soluciones alternativas.

### <a name="resolving-delta-log-on-path--failed-with-error-cannot-parse-json-object-from-log-file"></a>Error al resolver el registro Delta en la ruta de acceso ... (No se puede analizar el objeto JSON del archivo de registro)

Este error puede producirse debido a las siguientes razones o características no admitidas:
- [Filtro BLOOM](/azure/databricks/delta/optimizations/bloom-filters) en el conjunto de datos de Delta Lake. Los grupos de SQL sin servidor en Azure Synapse Analytics no admiten conjuntos de datos con el [filtro BLOOM](/azure/databricks/delta/optimizations/bloom-filters).
- Columna float en el conjunto de datos de Delta Lake con estadísticas.
- Conjunto de datos particionado en una columna float.

**Solución alternativa**: [quite el filtro BLOOM](/azure/databricks/delta/optimizations/bloom-filters#drop-a-bloom-filter-index) s quiere leer la carpeta Delta Lake con el grupo de SQL sin servidor. Si tiene columnas `float` que están causando el problema, deberá volver a particionar el conjunto de datos o quitar las estadísticas.

## <a name="performance"></a>Rendimiento

El grupo de SQL sin servidor asigna los recursos a las consultas en función del tamaño del conjunto de datos y la complejidad de las consultas. No puede afectar ni limitar los recursos que se proporcionan a las consultas. Hay algunos casos en los que puede experimentar degradaciones inesperadas del rendimiento de las consultas e identificar las causas principales.

### <a name="query-duration-is-very-long"></a>La duración de la consulta es muy larga 

Si usa Synapse Studio, pruebe a usar algún cliente de escritorio, como SQL Server Management Studio o Azure Data Studio. Synapse Studio es un cliente web que se conecta al grupo sin servidor mediante el protocolo HTTP, que suele ser más lento que las conexiones SQL nativas usadas en SQL Server Management Studio o Azure Data Studio.
Si tiene consultas con una duración de consulta superior a 30 minutos, esto indica que la devolución de resultados al cliente es lenta. El grupo de SQL sin servidor tiene un límite de ejecución de 30 minutos y el tiempo adicional se dedica a la transmisión de los resultados.
-   Asegúrese de que las aplicaciones cliente se coloquen con el punto de conexión del grupo de SQL sin servidor. La ejecución de una consulta en toda la región puede provocar una latencia adicional y una transmisión lenta del conjunto de resultados.
-   Asegúrese de que no tiene problemas de redes que puedan provocar una transmisión lenta del conjunto de resultados. 
-   Asegúrese de que la aplicación cliente tenga suficientes recursos (por ejemplo, no usa el 100 % de CPU). Consulte los procedimientos recomendados para [colocar los recursos](best-practices-serverless-sql-pool.md#client-applications-and-network-connections).

### <a name="high-variations-in-query-durations"></a>Altas variaciones en las duraciones de las consulta

Si ejecuta la misma consulta y observa variaciones en las duraciones de la consulta, puede haber varias razones que pueden provocar este comportamiento:  
- Compruebe si se trata de la primera ejecución de una consulta. La primera ejecución de una consulta recopila las estadísticas necesarias para crear un plan. Las estadísticas se recopilan mediante el examen de los archivos subyacentes y pueden aumentar la duración de la consulta. En Synapse Studio, verá consultas adicionales de "creación de estadísticas globales" en la lista de solicitudes SQL, que se ejecutan antes de la consulta.
- Las estadísticas pueden expirar después de un tiempo, por lo que periódicamente podría observar un impacto en el rendimiento porque el grupo sin servidor debe examinar y volver a crear las estadísticas. Es posible que observe consultas adicionales de "creación de estadísticas globales" en la lista de solicitudes SQL, que se ejecutan antes de la consulta.
- Compruebe si hay alguna carga de trabajo adicional que se ejecute en el mismo punto de conexión cuando se ejecutó la consulta con una duración mayor. El punto de conexión de SQL sin servidor asignará equitativamente los recursos a todas las consultas que se ejecutan en paralelo y es posible que la consulta se retrase.

## <a name="connections"></a>Conexiones

### <a name="sql-on-demand-is-currently-unavailable"></a>SQL a petición no está disponible actualmente

El punto de conexión del grupo de SQL sin servidor se desactiva automáticamente cuando no se usa. El punto de conexión se activa automáticamente cuando se recibe la siguiente solicitud SQL de cualquier cliente. En algunos casos, es posible que el punto de conexión no se inicie correctamente cuando se ejecute una primera consulta. En la mayoría de los casos como este, se trata de un error transitorio. Al reintentar la consulta, se activará la instancia.

Si ve este mensaje durante más tiempo, abra una incidencia de soporte técnico mediante Azure Portal.

### <a name="cannot-connect-from-synapse-studio"></a>No es posible conectarse desde Synapse Studio

Consulte la [sección sobre Synapse Studio](#synapse-studio).

## <a name="security"></a>Seguridad

### <a name="aad-service-principal-login-failures-when-spi-is-creating-a-role-assignment"></a>Errores de inicio de sesión de la entidad de servicio de AAD cuando SPI crea una asignación de roles
Si desea crear la asignación de roles para la aplicación de Identificador de entidad de servicio o AAD con otro SPI, o si ya ha creado uno y no puede iniciar sesión, es probable que reciba el siguiente error:
```
Login error: Login failed for user '<token-identified principal>'.
```
Para las entidades de servicio, el inicio de sesión debe crearse con el identificador de aplicación como SID (no con el identificador de objeto). Hay una limitación conocida para las entidades de servicio que impide que el servicio Azure Synapse recupere el identificador de aplicación de Azure AD Graph al crear la asignación de roles para otra aplicación o SPI.  

#### <a name="solution-1"></a>Solución 1
Vaya a Azure Portal > Synapse Studio > Administrar > Control de acceso y agregue manualmente Administrador de Synapse o Administrador de Synapse SQL para la entidad de servicio deseada.

#### <a name="solution-2"></a>Solución 2
Debe crear manualmente un inicio de sesión adecuado mediante código SQL:
```sql
use master
go
CREATE LOGIN [<service_principal_name>] FROM EXTERNAL PROVIDER;
go
ALTER SERVER ROLE sysadmin ADD MEMBER [<service_principal_name>];
go
```

#### <a name="solution-3"></a>Solución 3
También puede configurar la entidad de servicio Administrador de Synapse mediante PowerShell. Debe tener instalado el [módulo Az.Synapse](/powershell/module/az.synapse).
La solución es usar el cmdlet New-AzSynapseRoleAssignment con `-ObjectId "parameter"` y en ese campo de parámetro proporcionar el identificador de aplicación (en lugar del identificador de objeto) mediante las credenciales de la entidad de servicio de Azure del administrador del área de trabajo. Script de PowerShell:
```azurepowershell
$spAppId = "<app_id_which_is_already_an_admin_on_the_workspace>"
$SPPassword = "<application_secret>"
$tenantId = "<tenant_id>"
$secpasswd = ConvertTo-SecureString -String $SPPassword -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $spAppId, $secpasswd

Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant $tenantId

New-AzSynapseRoleAssignment -WorkspaceName "<workspaceName>" -RoleDefinitionName "Synapse Administrator" -ObjectId "<app_id_to_add_as_admin>" [-Debug]
```

#### <a name="validation"></a>Validación
Conéctese a un punto de conexión de SQL sin servidor y compruebe que se crea el inicio de sesión externo con SID `app_id_to_add_as_admin`:
```sql
select name, convert(uniqueidentifier, sid) as sid, create_date
from sys.server_principals where type in ('E', 'X')
```
o simplemente intente iniciar sesión en el punto de conexión de SQL sin servidor mediante la aplicación de administración que acaba de establecer.

## <a name="constraints"></a>Restricciones

Hay algunas restricciones generales del sistema que pueden afectar a la carga de trabajo:

| Propiedad | Limitación |
|---|---|
| Número máximo de áreas de trabajo de Synapse por suscripción | 20 |
| Número máximo de bases de datos por grupo sin servidor | 20 (sin incluir las bases de datos sincronizadas desde el grupo de Apache Spark) |
| Número máximo de bases de datos sincronizadas desde el grupo de Apache Spark | Sin límite |
| Número máximo de objetos de base de datos por base de datos | La suma del número de todos los objetos de una base de datos no puede superar 2 147 483 647 (consulte las [limitaciones del motor de base de datos de SQL Server](/sql/sql-server/maximum-capacity-specifications-for-sql-server#objects)). |
| Longitud máxima del identificador (en caracteres) | 128 (consulte las [limitaciones del motor de base de datos de SQL Server](/sql/sql-server/maximum-capacity-specifications-for-sql-server#objects))|
| Duración máxima de la consulta | 30 min |
| Tamaño máximo del conjunto de resultados | 80 GB (compartidos entre todas las consultas simultáneas que se ejecutan actualmente) |
| Simultaneidad máxima | No está limitada y depende de la complejidad de las consultas y la cantidad de datos analizados. Un grupo de SQL sin servidor puede controlar simultáneamente 1000 sesiones activas que ejecuten consultas ligeras, pero las cifras se reducen si las consultas son más complejas o examinan una mayor cantidad de datos. |

## <a name="next-steps"></a>Pasos siguientes

Consulte los artículos siguientes para aprender a usar un grupo de SQL sin servidor:

- [Consulta de archivos CSV](query-single-csv-file.md)

- [Consulta de carpetas y varios archivos .csv](query-folders-multiple-csv-files.md)

- [Consulta de archivos específicos](query-specific-files.md)

- [Consulta de archivos Parquet](query-parquet-files.md)

- [Consulta de tipos anidados de Parquet](query-parquet-nested-types.md)

- [Consulta de archivos JSON](query-json-files.md)

- [Creación y uso de vistas](create-use-views.md)

- [Creación y uso de tablas externas](create-use-external-tables.md)

- [Almacenamiento de los resultados de las consultas en Storage](create-external-table-as-select.md)
