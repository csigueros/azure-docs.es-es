---
title: Código de búfer de anillo de XEvent
description: Proporciona un ejemplo de código de Transact-SQL más fácil y rápido mediante el uso del destino de Búfer de anillo, en Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: mathoma
ms.date: 12/19/2018
ms.openlocfilehash: 0a74c6851c8dc3e2c2e4808f324fe711e31fd603
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741199"
---
# <a name="ring-buffer-target-code-for-extended-events-in-azure-sql-database"></a>Código de destino de búfer en anillo para eventos extendidos en Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Desea tener un ejemplo de código completo de la manera más rápida y fácil para capturar y notificar información de un evento extendido durante una prueba. El destino más fácil para los datos de eventos extendidos es el [destino de búfer de anillo](/previous-versions/sql/sql-server-2016/bb630339(v=sql.130)).

En este tema, se presenta un ejemplo de código de Transact-SQL que:

1. Crea una tabla con datos con los cuales demostrarse.
2. Crea una sesión para un evento extendido existente, es decir, **sqlserver.sql_statement_starting**.

   * El evento se limita a las instrucciones SQL que contienen una cadena Update determinada: **statement LIKE '%UPDATE tabEmployee%'** .
   * Elige enviar la salida del evento a un destino de tipo búfer en anillo, es decir, **package0.ring_buffer**.
3. Inicia la sesión de eventos.
4. Emite un par de instrucciones SQL UPDATE simple.
5. Emite una instrucción SQL SELECT para recuperar la salida de evento del búfer en anillo.

   * Se unen **sys.dm_xe_database_session_targets** y otras vistas de administración dinámica (DMV).
6. Detiene la sesión de eventos.
7. Anula el destino de Búfer de anillo para liberar sus recursos.
8. Anula la sesión de eventos y la tabla de demostración.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta y una suscripción de Azure. Puede registrarse para obtener una [evaluación gratuita](https://azure.microsoft.com/pricing/free-trial/).
* Cualquier base de datos donde pueda crear una tabla.

  * De manera opcional, puede [crear una base de datos de **AdventureWorksLT** de demostración](single-database-create-quickstart.md) en cuestión de minutos.
* SQL Server Management Studio (ssms.exe), idealmente la versión de actualización mensual más reciente.
  Puede descargar la versión más reciente de ssms.exe desde:

  * El tema titulado [Descarga de SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
  * [Un vínculo directo a la descarga.](https://go.microsoft.com/fwlink/?linkid=616025)

## <a name="code-sample"></a>Código de ejemplo

Con modificaciones muy pequeñas, el siguiente ejemplo de código de Búfer de anillo se puede ejecutar en Azure SQL Database o en Microsoft SQL Server. La diferencia es la presencia del nodo "_database" en el nombre de algunas vistas de administración dinámica (DMV), como se usa en la cláusula FROM del paso 5. Por ejemplo:

* sys.dm_xe<strong>_database</strong>_session_targets
* sys.dm_xe_session_targets

&nbsp;

```sql
GO
----  Transact-SQL.
---- Step set 1.

SET NOCOUNT ON;
GO


IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'tabEmployee')
BEGIN
    DROP TABLE tabEmployee;
END
GO


CREATE TABLE tabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO


INSERT INTO tabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

---- Step set 2.


IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'eventsession_gm_azuresqldb51')
BEGIN
    DROP EVENT SESSION eventsession_gm_azuresqldb51
        ON DATABASE;
END
GO


CREATE
    EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE '%UPDATE tabEmployee%'
            )
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
GO

---- Step set 3.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = START;
GO

---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
GO

---- Step set 5.


SELECT
    se.name                      AS [session-name],
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source,
    st.target_data,
    CAST(st.target_data AS XML)  AS [target_data_XML]
FROM
               sys.dm_xe_database_session_event_actions  AS ac

    INNER JOIN sys.dm_xe_database_session_events         AS ev  ON ev.event_name = ac.event_name
        AND CAST(ev.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_object_columns AS oc
         ON CAST(oc.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_session_targets        AS st
         ON CAST(st.event_session_address AS BINARY(8)) = CAST(ac.event_session_address AS BINARY(8))

    INNER JOIN sys.dm_xe_database_sessions               AS se
         ON CAST(ac.event_session_address AS BINARY(8)) = CAST(se.address AS BINARY(8))
WHERE
        oc.column_name = 'occurrence_number'
    AND
        se.name        = 'eventsession_gm_azuresqldb51'
    AND
        ac.action_name = 'sql_text'
ORDER BY
    se.name,
    ev.event_name,
    ac.action_name,
    st.target_name,
    se.session_source
;
GO

---- Step set 6.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    STATE = STOP;
GO

---- Step set 7.


ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO

---- Step set 8.


DROP EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE;
GO

DROP TABLE tabEmployee;
GO
```

&nbsp;

## <a name="ring-buffer-contents"></a>Contenido del Búfer de anillo

Se usa `ssms.exe` para ejecutar el ejemplo de código.

Para ver los resultados, hemos hecho clic en la celda bajo el encabezado de columna **target_data_XML**.

Luego, en el panel de resultados, hemos hecho clic en la celda bajo el encabezado de columna **target_data_XML**. Al hace este clic, se creó otra pestaña de archivo en ssms.exe donde se mostró el contenido de la celda de resultado, como XML.

El resultado se muestra en el bloque siguiente. Parece largo, pero son solo dos elementos **\<event>** .

&nbsp;

```xml
<RingBufferTarget truncated="0" processingTime="0" totalEventsProcessed="2" eventCount="2" droppedCount="0" memoryUsed="1728">
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.317Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>7</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>184</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>328</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
  <event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T15:29:31.327Z">
    <data name="state">
      <type name="statement_starting_state" package="sqlserver" />
      <value>0</value>
      <text>Normal</text>
    </data>
    <data name="line_number">
      <type name="int32" package="package0" />
      <value>10</value>
    </data>
    <data name="offset">
      <type name="int32" package="package0" />
      <value>340</value>
    </data>
    <data name="offset_end">
      <type name="int32" package="package0" />
      <value>486</value>
    </data>
    <data name="statement">
      <type name="unicode_string" package="package0" />
      <value>UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015</value>
    </data>
    <action name="sql_text" package="sqlserver">
      <type name="unicode_string" package="package0" />
      <value>
---- Step set 4.


SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM tabEmployee;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 102;

UPDATE tabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 1015;

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM tabEmployee;
</value>
    </action>
  </event>
</RingBufferTarget>
```

### <a name="release-resources-held-by-your-ring-buffer"></a>Liberar los recursos contenidos en el Búfer de anillo

Cuando termine de usar el búfer en anillo, puede quitarlo y liberar sus recursos. Para ello, emita un comando **ALTER** como el siguiente:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    DROP TARGET package0.ring_buffer;
GO
```

La definición de la sesión de eventos está actualizada, pero no se quita. Más adelante, puede agregar otra instancia del Búfer de anillo a la sesión de eventos:

```sql
ALTER EVENT SESSION eventsession_gm_azuresqldb51
    ON DATABASE
    ADD TARGET
        package0.ring_buffer
            (SET
                max_memory = 500   -- Units of KB.
            );
```

## <a name="more-information"></a>Más información

El tema principal de los eventos extendidos en Azure SQL Database es:

* [Consideraciones de eventos extendidos en Azure SQL Database](xevent-db-diff-from-svr.md), que compara algunos aspectos de los eventos extendidos que son distintos entre Azure SQL Database y Microsoft SQL Server.

Hay otros temas de ejemplo de código para eventos extendidos disponibles en los siguientes vínculos. De todas formas, debe comprobar siempre cualquier ejemplo para ver si está destinado a Microsoft SQL Server frente a Azure SQL Database. A continuación, puede decidir si es necesario algún pequeño cambio para ejecutar el ejemplo.

* Ejemplo de código para Azure SQL Database: [Código de destino del archivo de evento para eventos extendidos en Azure SQL Database](xevent-code-event-file.md)

<!--
('lock_acquired' event.)

- Code sample for SQL Server: [Determine Which Queries Are Holding Locks](/sql/relational-databases/extended-events/determine-which-queries-are-holding-locks)
- Code sample for SQL Server: [Find the Objects That Have the Most Locks Taken on Them](/sql/relational-databases/extended-events/find-the-objects-that-have-the-most-locks-taken-on-them)
-->