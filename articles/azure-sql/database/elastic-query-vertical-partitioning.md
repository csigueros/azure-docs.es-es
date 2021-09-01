---
title: Consulta de bases de datos en la nube con esquemas diferentes
description: cómo configurar consultas entre bases de datos en particiones verticales
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: mathoma
ms.date: 01/25/2019
ms.openlocfilehash: 84e7618232c38f8686e7b21e4a0660d812d9638f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737085"
---
# <a name="query-across-cloud-databases-with-different-schemas-preview"></a>Consulta de bases de datos elásticas para consultas entre bases de datos (particionamiento vertical)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

![Consultas entre tablas de bases de datos diferentes][1]

Las bases de datos con particiones verticales usan distintos conjuntos de tablas en bases de datos diferentes. Esto significa que el esquema es diferente en las distintas bases de datos. Por ejemplo, todas las tablas de inventario se encuentran en una base de datos mientras que todas las relacionadas con la contabilidad se encuentran en otra.

## <a name="prerequisites"></a>Requisitos previos

* El usuario debe poseer el permiso ALTER ANY EXTERNAL DATA SOURCE. Este permiso está incluido en el permiso ALTER DATABASE.
* Se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE para hacer referencia al origen de datos subyacente.

## <a name="overview"></a>Información general

> [!NOTE]
> A diferencia del particionamiento horizontal, estas instrucciones DDL no dependen de la definición de una capa de datos con un mapa de particiones mediante la biblioteca de cliente de bases de datos elásticas.
>

1. [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql)
2. [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql)
3. [CREATE EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql)
4. [CREATE EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql)

## <a name="create-database-scoped-master-key-and-credentials"></a>Creación de clave maestra y credenciales con ámbito de base de datos

La credencial utiliza la consulta elástica para conectarse a las bases de datos remotas.  

```sql
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'master_key_password';
CREATE DATABASE SCOPED CREDENTIAL [<credential_name>]  WITH IDENTITY = '<username>',  
SECRET = '<password>';
```

> [!NOTE]
> Asegúrese de que `<username>` no incluye ningún sufijo **"\@nombreDeServidor"** .

## <a name="create-external-data-sources"></a>Creación de orígenes de datos externos

Sintaxis:

```syntaxsql
<External_Data_Source> ::=
CREATE EXTERNAL DATA SOURCE <data_source_name> WITH
    (TYPE = RDBMS,
    LOCATION = ’<fully_qualified_server_name>’,
    DATABASE_NAME = ‘<remote_database_name>’,  
    CREDENTIAL = <credential_name>
    ) [;]
```
> [!IMPORTANT]
> El parámetro TYPE debe establecerse en **RDBMS**.

### <a name="example"></a>Ejemplo

En el ejemplo siguiente se ilustra el uso de la instrucción CREATE para orígenes de datos externos.

```sql
CREATE EXTERNAL DATA SOURCE RemoteReferenceData
   WITH
      (
         TYPE=RDBMS,
         LOCATION='myserver.database.windows.net',
         DATABASE_NAME='ReferenceData',
         CREDENTIAL= SqlUser
      );
```

Para recuperar la lista de orígenes de datos externos actuales:

```sql
select * from sys.external_data_sources;
```

### <a name="external-tables"></a>Tablas externas

Sintaxis:

```syntaxsql
CREATE EXTERNAL TABLE [ database_name . [ schema_name ] . | schema_name . ] table_name  
    ( { <column_definition> } [ ,...n ])
    { WITH ( <rdbms_external_table_options> ) }
    )[;]

<rdbms_external_table_options> ::=
    DATA_SOURCE = <External_Data_Source>,
    [ SCHEMA_NAME = N'nonescaped_schema_name',]
    [ OBJECT_NAME = N'nonescaped_object_name',]
```

### <a name="example"></a>Ejemplo

```sql
CREATE EXTERNAL TABLE [dbo].[customer]
   (
      [c_id] int NOT NULL,
      [c_firstname] nvarchar(256) NULL,
      [c_lastname] nvarchar(256) NOT NULL,
      [street] nvarchar(256) NOT NULL,
      [city] nvarchar(256) NOT NULL,
      [state] nvarchar(20) NULL,
      DATA_SOURCE = RemoteReferenceData
   );
```

En el ejemplo siguiente se muestra cómo recuperar la lista de tablas externas de la base de datos actual:

```sql
select * from sys.external_tables;
```

### <a name="remarks"></a>Observaciones

La consulta elástica amplía la sintaxis de la tabla externa existente para que incluya la definición de tablas externas que usan orígenes de datos externos de tipo RDBMS. La definición de tabla externa para el particionamiento vertical abarca los siguientes aspectos:

* **Esquema**: el DDL de tabla externa define un esquema que las consultas pueden usar. El esquema proporcionado en la definición de tabla externa debe coincidir con el de las tablas de la base de datos remota donde se almacenan los datos en sí.
* **Referencia a base de datos remota**: el DDL de tabla externa hace referencia a un origen de datos externo. El origen de datos externo especifica el nombre del servidor y el nombre de la base de datos remota donde se almacenan los datos de la tabla.

Usando un origen de datos externo como se describe en la anterior sección, la sintaxis para crear tablas externas es la siguiente:

La cláusula DATA_SOURCE define el origen de datos externo (por ejemplo, la base de datos remota en el caso del particionamiento vertical) que se usa para la tabla externa.  

Las cláusulas SCHEMA_NAME y OBJECT_NAME permiten asignar la definición de la tabla externa a una tabla de un esquema diferente de la base de datos remota, o bien a una tabla con un nombre diferente, respectivamente. Esta asignación resulta útil si desea definir una tabla externa en una vista de catálogo o DMV en la base de datos remota, o en cualquier otra situación en que el nombre de la tabla remota ya se use localmente.  

La siguiente instrucción DDL quita una definición de tabla externa existente del catálogo local. No afecta a la base de datos remota.

```sql
DROP EXTERNAL TABLE [ [ schema_name ] . | schema_name. ] table_name[;]  
```

**Permisos para CREATE/DROP EXTERNAL TABLE**: se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE para el DDL de tabla externa, que también se necesita para hacer referencia al origen de datos subyacente.  

## <a name="security-considerations"></a>Consideraciones sobre la seguridad

Los usuarios con acceso a la tabla externa obtienen automáticamente acceso a las tablas remotas subyacentes con la credencial proporcionada en la definición del origen de datos externo. Administre con cuidado el acceso a la tabla externa para evitar la elevación de privilegios no deseada por medio de la credencial del origen de datos externo. Se pueden usar permisos SQL normales para conceder o revocar el acceso a una tabla externa como si fuera una tabla normal.  

## <a name="example-querying-vertically-partitioned-databases"></a>Ejemplo: consulta de bases de datos con particiones verticales

La consulta siguiente realiza una combinación tridireccional entre las dos tablas locales de pedidos y líneas de pedido, y la tabla remota de clientes. Es un ejemplo del caso de uso de datos de referencia para una consulta elástica:

```sql
    SELECT
     c_id as customer,
     c_lastname as customer_name,
     count(*) as cnt_orderline,
     max(ol_quantity) as max_quantity,
     avg(ol_amount) as avg_amount,
     min(ol_delivery_d) as min_deliv_date
    FROM customer
    JOIN orders
    ON c_id = o_c_id
    JOIN  order_line
    ON o_id = ol_o_id and o_c_id = ol_c_id
    WHERE c_id = 100
```

## <a name="stored-procedure-for-remote-t-sql-execution-sp_execute_remote"></a>Procedimiento almacenado para la ejecución remota de T-SQL: sp\_execute_remote

La consulta elástica también incluye un procedimiento almacenado que proporciona acceso directo a la base de datos remota. El procedimiento almacenado se denomina [sp\_execute\_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) y sirve para ejecutar procedimientos almacenados remotos o código T-SQL en la base de datos remota. Toma los parámetros siguientes:

* Nombre de origen de datos (nvarchar): nombre del origen de datos externo de tipo RDBMS.
* Consulta (nvarchar): la consulta T-SQL que se va a ejecutar en la base de datos remota.
* Declaración de parámetro (nvarchar) - opcional: cadena con definiciones de tipos de datos de los parámetros usados en el parámetro Query (como sp_executesql).
* Lista de valores de los parámetros (opcional): lista separada por comas de valores de los parámetros (por ejemplo, sp_executesql).

sp\_execute\_remote utiliza el origen de datos externo proporcionado en los parámetros de invocación para ejecutar la instrucción T-SQL determinada en la base de datos remota. Utiliza la credencial del origen de datos externo para conectarse a la base de datos remota.  

Ejemplo:

```sql
    EXEC sp_execute_remote
        N'MyExtSrc',
        N'select count(w_id) as foo from warehouse'
```

## <a name="connectivity-for-tools"></a>Conectividad para herramientas

Puede usar cadenas de conexión de SQL Server normales para conectar sus herramientas de integración de datos y de BI con bases de datos en el servidor en que se habilitó la consulta elástica y se definieron las tablas externas. Asegúrese de que SQL Server se admite como origen de datos para la herramienta. Después, consulte la base de datos de consulta elástica y sus tablas externas como cualquier otra base de datos de SQL Server a la que se conectaría con su herramienta.

## <a name="best-practices"></a>Procedimientos recomendados

* Asegúrese de que se conceda a la base de datos de punto de conexión de consulta elástica acceso a la base de datos remota habilitando el acceso a los Servicios de Azure en su configuración de firewall de Azure SQL Database. Compruebe también que la credencial proporcionada en la definición del origen de datos externo pueda iniciar sesión correctamente en la base de datos remota y tenga los permisos para acceder a la tabla remota.  
* Una consulta elástica funciona mejor para aquellas consultas en que la mayor parte del cálculo se puede realizar en las bases de datos remotas. Normalmente, el máximo rendimiento de las consultas se obtiene con predicados de filtro selectivos que se pueden evaluar en las bases de datos remotas o combinaciones que se pueden realizar totalmente en la base de datos remota. Es posible que otros patrones de consulta necesiten cargar grandes cantidades de datos desde la base de datos remota y pueden experimentar un rendimiento deficiente.

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información general sobre las consultas elásticas, consulte [Información general sobre las consultas elásticas](elastic-query-overview.md).
* Para obtener un tutorial sobre la creación de particiones verticales, consulte [Introducción a las consultas entre bases de datos (particiones verticales)](elastic-query-getting-started-vertical.md).
* Para obtener un tutorial sobre la creación de particiones horizontales (particionamiento), consulte [Introducción a las consultas elásticas para las particiones horizontales (particionamiento)](elastic-query-getting-started.md).
* Para ver la sintaxis y consultas de ejemplo para los datos con particionamiento horizontal, consulte [Consulta de datos particionados horizontalmente.](elastic-query-horizontal-partitioning.md)
* Consulte [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) para ver un procedimiento almacenado que ejecuta una instrucción de Transact-SQL en una sola instancia remota de Azure SQL Database o un conjunto de bases de datos que actúan como particiones en un esquema de particiones horizontales.

<!--Image references-->
[1]: ./media/elastic-query-vertical-partitioning/verticalpartitioning.png

<!--anchors-->