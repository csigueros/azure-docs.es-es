---
title: Solución de problemas de los conectores de Azure Synapse Analytics, Azure SQL Database y SQL Server
titleSuffix: Azure Data Factory & Azure Synapse
description: Aprenda a solucionar problemas con los conectores de Azure Synapse Analytics, Azure SQL Database y SQL Server en Azure Data Factory y Azure Synapse Analytics.
author: jianleishen
ms.service: data-factory
ms.subservice: data-movement
ms.topic: troubleshooting
ms.date: 10/01/2021
ms.author: jianleishen
ms.custom: has-adal-ref, synapse
ms.openlocfilehash: f258ba3b8c62d0d290785bbf6a66cc7651286162
ms.sourcegitcommit: 7bd48cdf50509174714ecb69848a222314e06ef6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2021
ms.locfileid: "129390437"
---
# <a name="troubleshoot-the-azure-synapse-analytics-azure-sql-database-and-sql-server-connectors-in-azure-data-factory-and-azure-synapse"></a>Solución de problemas de los conectores de Azure Synapse Analytics, Azure SQL Database y SQL Server en Azure Data Factory y Azure Synapse

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

En este artículo se proporcionan sugerencias para solucionar problemas comunes con los conectores de Azure Synapse Analytics, Azure SQL Database y SQL Server en Azure Data Factory y Azure Synapse.

## <a name="error-code-sqlfailedtoconnect"></a>Código de error: SqlFailedToConnect

- **Mensaje**: `Cannot connect to SQL Database: '%server;', Database: '%database;', User: '%user;'. Check the linked service configuration is correct, and make sure the SQL Database firewall allows the integration runtime to access.`
- **Causas y recomendaciones**: diversas causas pueden provocar este error. Busque en la lista siguiente el análisis de las posibles causas y la recomendación relacionada.

    | Análisis de las causas                                               | Recomendación                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | En Azure SQL, si el mensaje de error contiene la cadena "SqlErrorNumber=47073", significa que se deniega el acceso a la red pública en la configuración de conectividad. | En el firewall de Azure SQL, establezca la opción **Deny public network access** (Denegar acceso a red pública) en *No*. Para más información, consulte [Configuración de conectividad de Azure SQL](../azure-sql/database/connectivity-settings.md#deny-public-network-access). |
    | En Azure SQL, si el mensaje de error contiene un código de error SQL como "SqlErrorNumber=[errorcode]", consulte la guía de solución de problemas de Azure SQL. | Puede encontrar algunas recomendaciones en [Solución de problemas de conectividad y otros errores con Azure SQL Database y Azure SQL Managed Instance](../azure-sql/database/troubleshoot-common-errors-issues.md). |
    | Compruebe si el puerto 1433 está en la lista de permitidos del firewall. | Para más información, consulte [Puertos que usa SQL Server](/sql/sql-server/install/configure-the-windows-firewall-to-allow-sql-server-access#ports-used-by-). |
    | Si el mensaje de error contiene la cadena "SqlException", SQL Database genera el error que indica que se produjo un problema en una operación específica. | Para más información, busque por el código de error de SQL en [Errores del motor de base de datos](/sql/relational-databases/errors-events/database-engine-events-and-errors). Si necesita más ayuda, póngase en contacto con el soporte técnico de Azure SQL. |
    | Si se trata de un problema transitorio (por ejemplo, una conexión de red inestable), agregue reintentos en la directiva de actividad para mitigarlo. | Para obtener más información, consulte [Canalizaciones y actividades](./concepts-pipelines-activities.md#activity-policy). |
    | Si el mensaje de error contiene la cadena "El cliente con la dirección IP "..." no tiene permitido acceder" y está intentando conectarse a Azure SQL Database, normalmente se debe a un problema con el firewall de Azure SQL Database. | En la configuración del firewall de Azure SQL Server, habilite la opción **Permitir que los servicios y recursos de Azure accedan a este servidor**. Para más información, consulte [Reglas de firewall de IP de Azure SQL Database y Azure Synapse](../azure-sql/database/firewall-configure.md). |
    
## <a name="error-code-sqloperationfailed"></a>Código de error: SqlOperationFailed

- **Mensaje**: `A database operation failed. Please search error to get more details.`

- **Causas y recomendaciones**: diversas causas pueden provocar este error. Busque en la lista siguiente el análisis de las posibles causas y la recomendación relacionada.

    | Análisis de las causas                                               | Recomendación                                               |
    | :----------------------------------------------------------- | :----------------------------------------------------------- |
    | Si el mensaje de error contiene la cadena "SqlException", SQL Database genera un error que indica que se produjo un problema en una operación concreta. | Si el error de SQL no está claro, intente modificar la base de datos al nivel de compatibilidad más reciente de "150". Esta acción puede producir los errores de la versión de SQL más reciente. Para más información, consulte la [documentación](/sql/t-sql/statements/alter-database-transact-sql-compatibility-level#backwardCompat). <br/> Para más información sobre la solución de problemas de SQL, busque por el código de error de SQL en [Errores del motor de base de datos](/sql/relational-databases/errors-events/database-engine-events-and-errors). Si necesita más ayuda, póngase en contacto con el soporte técnico de Azure SQL. |
    | Si el mensaje de error contiene la cadena "PdwManagedToNativeInteropException", normalmente se debe a una falta de coincidencia entre los tamaños de columna de origen y receptor. | Compruebe el tamaño de las columnas de origen y receptor. Si necesita más ayuda, póngase en contacto con el soporte técnico de Azure SQL. |
    | Si el mensaje de error contiene la cadena "InvalidOperationException", normalmente se debe a que los datos de entrada no son válidos. | Para saber en qué fila se encuentra el problema, habilite la característica de tolerancia a errores en la actividad de copia, que puede redirigir las filas problemáticas al almacenamiento para investigarlas más a fondo. Para obtener más información, consulte [Tolerancia a errores de la actividad de copia](./copy-activity-fault-tolerance.md). |


## <a name="error-code-sqlunauthorizedaccess"></a>Código de error: SqlUnauthorizedAccess

- **Mensaje**: `Cannot connect to '%connectorName;'. Detail Message: '%message;'`

- **Causa**: Las credenciales son incorrectas o la cuenta de inicio de sesión no puede acceder a SQL Database.

- **Recomendación:**  Compruebe que la cuenta de inicio de sesión tenga permisos suficientes para acceder a SQL Database.


## <a name="error-code-sqlopenconnectiontimeout"></a>Código de error: SqlOpenConnectionTimeout

- **Mensaje**: `Open connection to database timeout after '%timeoutValue;' seconds.`

- **Causa**: El problema podría ser un error transitorio de SQL Database.

- **Recomendación:**  Vuelva a intentar la operación para actualizar la cadena de conexión del servicio vinculado con un valor de tiempo de espera de conexión mayor.


## <a name="error-code-sqlautocreatetabletypemapfailed"></a>Código de error: SqlAutoCreateTableTypeMapFailed

- **Mensaje**: `Type '%dataType;' in source side cannot be mapped to a type that supported by sink side(column name:'%columnName;') in autocreate table.`

- **Causa**: La tabla de creación automática no puede cumplir el requisito de origen.

- **Recomendación:**  Actualice el tipo de columna en *mappings* o cree manualmente la tabla del receptor en el servidor de destino.


## <a name="error-code-sqldatatypenotsupported"></a>Código de error: SqlDataTypeNotSupported

- **Mensaje**: `A database operation failed. Check the SQL errors.`

- **Causa**: Si el problema se produce en el origen de SQL y el error está relacionado con el desbordamiento de SqlDateTime, el valor de datos está por encima del intervalo de tipos de lógica (1/1/1753 12:00:00 AM - 12/31/9999 11:59:59 PM).

- **Recomendación:**  Convierta el tipo en una cadena en la consulta SQL de origen o, en la asignación de columnas de la actividad de copia, cambie el tipo de columna a *String*.

- **Causa**: Si el problema se produce en el receptor de SQL y el error está relacionado con el desbordamiento de SqlDateTime, el valor de los datos estará por encima del intervalo permitido en la tabla del receptor.

- **Recomendación:**  Actualice el tipo de columna correspondiente al tipo *datetime2* en la tabla del receptor.


## <a name="error-code-sqlinvaliddbstoredprocedure"></a>Código de error: SqlInvalidDbStoredProcedure

- **Mensaje**: `The specified Stored Procedure is not valid. It could be caused by that the stored procedure doesn't return any data. Invalid Stored Procedure script: '%scriptName;'.`

- **Causa**: El procedimiento almacenado especificado no es válido. La causa podría ser que el procedimiento almacenado no devuelva ningún dato.

- **Recomendación:**  Valide el procedimiento almacenado con las herramientas de SQL. Asegúrese de que el procedimiento almacenado pueda devolver datos.


## <a name="error-code-sqlinvaliddbquerystring"></a>Código de error: SqlInvalidDbQueryString

- **Mensaje**: `The specified SQL Query is not valid. It could be caused by that the query doesn't return any data. Invalid query: '%query;'`

- **Causa**: La consulta SQL especificada no es válida. La causa podría ser que la consulta no devuelva ningún dato.

- **Recomendación:**  Valide la consulta SQL con las herramientas de SQL. Asegúrese de que la consulta pueda devolver datos.


## <a name="error-code-sqlinvalidcolumnname"></a>Código de error: SqlInvalidColumnName

- **Mensaje**: `Column '%column;' does not exist in the table '%tableName;', ServerName: '%serverName;', DatabaseName: '%dbName;'.`

- **Causa**: No se puede encontrar la columna porque es posible que la configuración no sea correcta.

- **Recomendación:**  Compruebe la columna de la consulta, el valor *structure* en el conjunto de datos y el valor *mappings* en la actividad.


## <a name="error-code-sqlbatchwritetimeout"></a>Código de error: SqlBatchWriteTimeout

- **Mensaje**: `Timeouts in SQL write operation.`

- **Causa**: El problema podría ser un error transitorio de SQL Database.

- **Recomendación:**  Vuelva a intentar la operación. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Azure SQL.


## <a name="error-code-sqlbatchwritetransactionfailed"></a>Código de error: SqlBatchWriteTransactionFailed

- **Mensaje**: `SQL transaction commits failed.`

- **Causa**: Si los detalles de la excepción indican constantemente el agotamiento del tiempo de espera de la transacción, significa que la latencia de red entre el entorno de ejecución de integración y la base de datos supera el umbral predeterminado de 30 segundos.

- **Recomendación:**  Actualice la cadena de conexión del servicio vinculado de SQL con un valor de *tiempo de espera de la conexión* igual a 120 o superior y vuelva a ejecutar la actividad.

- **Causa**: Si los detalles de la excepción indican de forma intermitente que la conexión SQL se ha interrumpido, podría tratarse de un error de red transitorio o de un problema de SQL Database.

- **Recomendación:**  Vuelva a intentar la actividad y revise las métricas de SQL Database.


## <a name="error-code-sqlbulkcopyinvalidcolumnlength"></a>Código de error: SqlBulkCopyInvalidColumnLength

- **Mensaje**: `SQL Bulk Copy failed due to receive an invalid column length from the bcp client.`

- **Causa**: Error en la copia masiva de SQL, ya que se ha recibido una longitud de columna no válida del cliente de la utilidad del programa de copia masiva (bcp).

- **Recomendación:**  Para identificar en qué fila se ha encontrado el problema, habilite la característica de tolerancia a errores en la actividad de copia. Esta acción puede redirigir las filas problemáticas al almacenamiento para investigarlas más a fondo. Para obtener más información, consulte [Tolerancia a errores de la actividad de copia](./copy-activity-fault-tolerance.md).


## <a name="error-code-sqlconnectionisclosed"></a>Código de error: SqlConnectionIsClosed

- **Mensaje**: `The connection is closed by SQL Database.`

- **Causa**: SQL Database cierra la conexión SQL cuando la ejecución simultánea excesiva y el servidor cierran la conexión.

- **Recomendación:**  Vuelva a intentar la conexión. Si el problema persiste, póngase en contacto con el servicio de soporte técnico de Azure SQL.

## <a name="error-code-sqlserverinvalidlinkedservicecredentialmissing"></a>Código de error: SqlServerInvalidLinkedServiceCredentialMissing

- **Mensaje**: `The SQL Server linked service is invalid with its credential being missing.`

- **Causa**: el servicio vinculado no se ha configurado correctamente.

- **Recomendación**: valide y corrija el servicio vinculado de SQL Server. 

## <a name="error-code-sqlparallelfailedtodetectpartitioncolumn"></a>Código de error: SqlParallelFailedToDetectPartitionColumn

- **Mensaje**: `Failed to detect the partition column with command '%command;', %message;.`

- **Causa**: no hay ninguna clave principal ni única en la tabla.

- **Recomendación**: compruebe la tabla para asegurarse de que se haya creado una clave principal o un índice único. 

## <a name="error-code-sqlparallelfailedtodetectphysicalpartitions"></a>Código de error: SqlParallelFailedToDetectPhysicalPartitions

- **Mensaje**: `Failed to detect the physical partitions with command '%command;', %message;.`

- **Causa**: no se han creado particiones físicas para la tabla. Compruebe la base de datos.

- **Recomendación**: consulte [Crear tablas e índices con particiones](/sql/relational-databases/partitions/create-partitioned-tables-and-indexes?view=sql-server-ver15&preserve-view=true) para resolver este problema.

## <a name="error-code-sqlparallelfailedtogetpartitionrangesynapse"></a>Código de error: SqlParallelFailedToGetPartitionRangeSynapse

- **Mensaje**: `Failed to get the partitions for azure synapse with command '%command;', %message;.`

- **Causa**: no se han creado particiones físicas para la tabla. Compruebe la base de datos.

- **Recomendación**: consulte [Creación de particiones de tablas en el grupo de SQL dedicado](../synapse-analytics/sql-data-warehouse/sql-data-warehouse-tables-partition.md) para resolver este problema.

## <a name="error-message-conversion-failed-when-converting-from-a-character-string-to-uniqueidentifier"></a>Mensaje de error: Error de conversión al convertir de una cadena de caracteres a uniqueidentifier

- **Síntomas**: Al copiar datos desde un origen de datos tabulares (como SQL Server) en Azure Synapse Analytics mediante copias almacenadas provisionalmente y PolyBase, recibe el siguiente error:

   `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Conversion failed when converting from a character string to uniqueidentifier...`

- **Causa**: PolyBase de Azure Synapse Analytics no puede convertir una cadena vacía en un GUID.

- **Solución:** En el receptor de la actividad de copia, en la configuración de PolyBase, establezca la opción **Use Type default** (Usar tipo predeterminado) en *false*.


## <a name="error-message-expected-data-type-decimalxx-offending-value"></a>Mensaje de error: Expected data type: DECIMAL(x,x), Offending value (Tipo de datos esperado: DECIMAL(x,x), valor incorrecto)

- **Síntomas**: Al copiar datos desde un origen de datos tabulares (como SQL Server) en Azure Synapse Analytics mediante copias almacenadas provisionalmente y PolyBase, recibe el siguiente error:

    `ErrorCode=FailedDbOperation,Type=Microsoft.DataTransfer.Common.Shared.HybridDeliveryException, 
    Message=Error happened when loading data into Azure Synapse Analytics., 
    Source=Microsoft.DataTransfer.ClientLibrary,Type=System.Data.SqlClient.SqlException, 
    Message=Query aborted-- the maximum reject threshold (0 rows) was reached while reading from an external source: 1 rows rejected out of total 415 rows processed. (/file_name.txt)  
    Column ordinal: 18, Expected data type: DECIMAL(x,x), Offending value:..`

- **Causa**: PolyBase de Azure Synapse Analytics no puede insertar una cadena vacía (valor null) en una columna decimal.

- **Solución:** En el receptor de la actividad de copia, en la configuración de PolyBase, establezca la opción **Use Type default** (Usar tipo predeterminado) en false.


## <a name="error-message-java-exception-message-hdfsbridgecreaterecordreader"></a>Mensaje de error: Mensaje de excepción de Java: HdfsBridge::CreateRecordReader

- **Síntomas**: Al copiar datos en Azure Synapse Analytics mediante PolyBase, recibe el siguiente error:

    `Message=110802;An internal DMS error occurred that caused this operation to fail.  
    Details: Exception: Microsoft.SqlServer.DataWarehouse.DataMovement.Common.ExternalAccess.HdfsAccessException, 
    Message: Java exception raised on call to HdfsBridge_CreateRecordReader.  
    Java exception message:HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.: Error [HdfsBridge::CreateRecordReader - Unexpected error encountered creating the record reader.] occurred while accessing external file.....`

- **Causa**: La causa podría ser que el esquema (ancho total de columna) sea demasiado grande (más de 1 MB). Compruebe el esquema de la tabla de Azure Synapse Analytics de destino sumando el tamaño de todas las columnas:

    - Int = 4 bytes
    - Bigint = 8 bytes
    - Varchar(n), char(n), binary(n), varbinary(n) = n bytes
    - Nvarchar(n), nchar(n) = n*2 bytes
    - Date = 6 bytes
    - Datetime/(2), smalldatetime = 16 bytes
    - Datetimeoffset = 20 bytes
    - Decimal = 19 bytes
    - Float = 8 bytes
    - Money = 8 bytes
    - Smallmoney = 4 bytes
    - Real = 4 bytes
    - Smallint = 2 bytes
    - Time = 12 bytes
    - Tinyint = 1 byte

- **Solución:** 
    - Reduzca el ancho de columna a menos de 1 MB.
    - También puede deshabilitar PolyBase para usar un enfoque de inserción masiva.


## <a name="error-message-the-condition-specified-using-http-conditional-headers-is-not-met"></a>Mensaje de error: The condition specified using HTTP conditional header(s) is not met (No se cumple la condición especificada mediante encabezados condicionales HTTP)

- **Síntomas**: Al utilizar una consulta SQL para extraer datos de Azure Synapse Analytics, recibe el siguiente error:

    `...StorageException: The condition specified using HTTP conditional header(s) is not met...`

- **Causa**: Se ha encontrado un problema en Azure Synapse Analytics al consultar la tabla externa en Azure Storage.

- **Solución:** Ejecute la misma consulta en SQL Server Management Studio (SSMS) y compruebe si obtiene el mismo resultado. En caso afirmativo, abra una incidencia de soporte técnico en Azure Synapse Analytics y proporcione el nombre del servidor y de la base de datos de Azure Synapse Analytics.


## <a name="performance-tier-is-low-and-leads-to-copy-failure"></a>El nivel de rendimiento es bajo y genera un error de copia

- **Síntomas**: Al copiar datos en Azure Cosmos DB, recibe el siguiente error: `Database operation failed. Error message from database execution : ExecuteNonQuery requires an open and available Connection. The connection's current state is closed.`.

- **Causa**: Azure SQL Database s1 ha alcanzado los límites de entrada/salida (E/S).

- **Solución:** actualice el nivel de rendimiento de Azure SQL Database para corregir el problema. 


## <a name="sql-table-cant-be-found"></a>No se encuentra la tabla SQL 

- **Síntomas**: Al copiar datos de un entorno híbrido en una tabla de SQL Server local, recibe el siguiente error:`Cannot find the object "dbo.Contoso" because it does not exist or you do not have permissions.`

- **Causa**: La cuenta de SQL actual no tiene permisos suficientes para ejecutar las solicitudes emitidas por SqlBulkCopy.WriteToServer de .NET.

- **Solución:** Cambie a una cuenta de SQL con más privilegios.


## <a name="error-message-string-or-binary-data-is-truncated"></a>Mensaje de error: Los datos binarios o de tipo cadena se truncan

- **Síntomas**: Se produce un error al copiar datos en una tabla de Azure SQL Server local. 

- **Causa**: La definición del esquema de la tabla SQL Cx tiene una o más columnas con menos longitud que la esperada.

- **Solución:** Para resolver la incidencia, intente lo siguiente:

    1. Para detectar las filas que tienen el problema, aplique [tolerancia a errores](./copy-activity-fault-tolerance.md) al receptor SQL, especialmente "redirectIncompatibleRowSettings".

        > [!NOTE]
        > La tolerancia a errores podría requerir tiempo de ejecución adicional, lo que podría conllevar costos mayores.

    2. Vuelva a comprobar los datos redirigidos con la longitud de la columna del esquema de la tabla SQL para ver qué columnas deben actualizarse.

    3. Actualice el esquema de tabla como corresponda.

## <a name="error-code-faileddboperation"></a>Código de error: FailedDbOperation

- **Mensaje**: `User does not have permission to perform this action.`

- **Recomendación**: asegúrese de que el usuario configurado en el conector de Azure Synapse Analytics deba tener el permiso "CONTROL" en la base de datos de destino mientras usa PolyBase para cargar datos. Para obtener más detalles, consulte [este documento](./connector-azure-sql-data-warehouse.md#required-database-permission).

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda para solucionar problemas, pruebe estos recursos:

- [Solución de problemas de conectores en Azure Data Factory](connector-troubleshoot-guide.md)
- [Blog de Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
- [Solicitud de características de Data Factory](/answers/topics/azure-data-factory.html)
- [Vídeos de Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
- [Página de preguntas y respuestas de Microsoft](/answers/topics/azure-data-factory.html)
- [Foro de Stack Overflow para Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
- [Información de Twitter sobre Data Factory](https://twitter.com/hashtag/DataFactory)
