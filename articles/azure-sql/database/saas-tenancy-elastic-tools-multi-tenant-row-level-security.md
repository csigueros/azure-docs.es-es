---
title: Aplicaciones multiinquilino con RLS y herramientas de bases de datos elásticas
description: Use herramientas de bases de datos elásticas con seguridad de nivel de fila para crear una aplicación con una capa de datos altamente escalable.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: VanMSFT
ms.author: vanto
ms.reviewer: mathoma
ms.date: 12/18/2018
ms.openlocfilehash: 5ea87afa5b66701a3d28f98a446c9307ccac0fc4
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738465"
---
# <a name="multi-tenant-applications-with-elastic-database-tools-and-row-level-security"></a>Aplicaciones de múltiples inquilinos con herramientas de bases de datos elásticas y seguridad de nivel de fila
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Las [herramientas de bases de datos elásticas](elastic-scale-get-started.md) y la [seguridad de nivel de fila (RLS)][rls] cooperan para permitir el escalado del nivel de datos de una aplicación multiinquilino con Azure SQL Database. Este conjunto de tecnologías le ayudan a crear una aplicación que tiene una capa de datos muy escalable. El nivel de datos admite particiones multiinquilino y usa **ADO.NET SqlClient** o **Entity Framework**. Para más información, consulte [Diseño de patrones para aplicaciones SaaS multiinquilino con Azure SQL Database](./saas-tenancy-app-design-patterns.md).

- Las **herramientas de bases de datos elásticas** permiten a los desarrolladores escalar horizontalmente el nivel de datos con prácticas de particionamiento estándar, mediante el uso de bibliotecas de .NET y plantillas de servicios de Azure. La administración de particiones mediante la [Biblioteca cliente de Elastic Database][s-d-elastic-database-client-library] le ayuda a automatizar y simplificar muchas de las tareas de infraestructura asociadas típicamente con el particionamiento.
- La **seguridad de nivel de fila** permite a los desarrolladores almacenar de forma segura los datos de varios inquilinos en la misma base de datos. Las directivas de seguridad RLS filtran y eliminan las filas que no pertenecen al inquilino que ejecuta una consulta. La centralización de la lógica de filtro en la propia base de datos simplifica el mantenimiento y reduce el riesgo de un error de seguridad. La alternativa de confiar en todo el código de cliente para reforzar la seguridad es arriesgada.

Si estas características se usan conjuntamente, una aplicación puede almacenar los datos para varios inquilinos en la misma base de datos de la partición. El costo por inquilino es menor cuando los inquilinos comparten una base de datos. La misma aplicación puede ofrecen también a sus inquilinos premium la opción de pagar su propia partición dedicada para un único inquilino. Una de las ventajas de este aislamiento es que aporta una mayor garantía de rendimiento. En una base de datos de un único inquilino, no hay otros inquilinos que compitan por los recursos.

El objetivo es usar las API de [enrutamiento dependiente de los datos](elastic-scale-data-dependent-routing.md) de la Biblioteca de cliente de Elastic Database para conectar automáticamente cada inquilino dado a la base de datos de la partición correcta. Solo una partición contiene el valor de TenantId concreto para el inquilino determinado. El valor de TenantId es la *clave de particionamiento*. Una vez establecida la conexión, una directiva de seguridad de RLS dentro de la base de datos garantiza que el inquilino dado puede acceder solo a las filas de datos que contienen su TenantId.

> [!NOTE]
> El identificador del inquilino puede constar de más de una columna. Por comodidad, vamos a asumir de manera informal que el valor de TenantId tiene una sola columna.

![Arquitectura de la aplicación de creación de blogs][1]

## <a name="download-the-sample-project"></a>Descarga del proyecto de ejemplo

### <a name="prerequisites"></a>Requisitos previos

- Uso de Visual Studio (2012 o posterior)
- Creación de tres bases de datos en Azure SQL Database
- Descargue el proyecto de ejemplo: [Herramientas de bases de datos elásticas para Azure SQL: Particiones multiinquilino](https://go.microsoft.com/?linkid=9888163)
  - Rellene la información para las bases de datos al comienzo de **Program.cs**

Este proyecto amplía el descrito en [Herramientas de bases de datos elásticas para SQL de Azure - Integración de Entity Framework](elastic-scale-use-entity-framework-applications-visual-studio.md) mediante la adición de compatibilidad con bases de datos de partición de varios inquilinos. El proyecto compila una sencilla aplicación de consola para la creación de blogs y publicaciones. El proyecto incluye cuatro inquilinos y dos bases de datos de la partición multiinquilino. Esta configuración se ilustra en el diagrama anterior.

Compile y ejecute la aplicación. Esta ejecución arranca el administrador de mapas de particiones de las herramientas de Elastic Database y realiza las siguientes pruebas:

1. Con Entity Framework y LINQ, cree un nuevo blog y muestre todos los blogs para cada inquilino.
2. Con ADO.NET SqlClient, muestre todos los blogs para un inquilino.
3. Intente insertar un blog del inquilino erróneo para comprobar que se produce un error.

Tenga en cuenta que como todavía no se ha habilitado RLS en las bases de datos de la partición, cada una de estas pruebas revela un problema: los inquilinos pueden consultar los blogs que no pertenecen a ellos y la aplicación no impide insertar un blog del inquilino incorrecto. El resto de este artículo describe cómo resolver estos problemas mediante la imposición de aislamiento de inquilinos con RLS. Hay dos pasos:

1. **Capa de aplicación**: modifique el código de la aplicación para establecer siempre el valor actual de TenantId en SESSION\_CONTEXT después de abrir una conexión. El proyecto de ejemplo ya establece el valor de TenantId de este modo.
2. **Capa de datos**: cree una directiva de seguridad de RLS en cada base de datos de la partición para filtrar las filas en función del valor de TenantId almacenado en SESSION\_CONTEXT. Cree una directiva para cada una de las bases de datos de la partición, ya que si no lo hace, las filas de las particiones multiinquilino no se van a filtrar.

## <a name="1-application-tier-set-tenantid-in-the-session_context"></a>1. Capa de aplicación: establezca TenantId en SESSION\_CONTEXT

En primer lugar, conéctese a una base de datos de la partición mediante la API de enrutamiento dependiente de los datos de la Biblioteca de cliente de Elastic Database. La aplicación debe indicar a la base de datos que TenantId utiliza la conexión. El valor de TenantId indica a la directiva de seguridad de RLS qué filas se deben filtrar y eliminar, ya que pertenecen a otros inquilinos. Almacenar el valor de TenantId actual en el [SESSION\_CONTEXT](/sql/t-sql/functions/session-context-transact-sql) de la conexión.

Una alternativa a SESSION\_CONTEXT es usar [CONTEXT\_INFO](/sql/t-sql/functions/context-info-transact-sql). Pero es mejor la opción de SESSION\_CONTEXT. SESSION\_CONTEXT es más fácil de usar, devuelve NULL de forma predeterminada y admite pares clave-valor.

### <a name="entity-framework"></a>Entity Framework

En el caso de las aplicaciones que usan Entity Framework, el enfoque más sencillo es establecer SESSION\_CONTEXT dentro de la invalidación de ElasticScaleContext descrita en [el enrutamiento dependiente de datos mediante DbContext de EF](elastic-scale-use-entity-framework-applications-visual-studio.md#data-dependent-routing-using-ef-dbcontext). Cree y ejecute un objeto SqlCommand que establezca el valor de TenantId en SESSION\_CONTEXT en la clave de particionamiento especificada para la conexión. A continuación, devuelva la conexión desacoplada a través de enrutamiento dependiente de los datos. De este modo, solo hay que escribir el código una vez para establecer SESSION\_CONTEXT.

```csharp
// ElasticScaleContext.cs
// Constructor for data-dependent routing.
// This call opens a validated connection that is routed to the
// proper shard by the shard map manager.
// Note that the base class constructor call fails for an open connection
// if migrations need to be done and SQL credentials are used.
// This is the reason for the separation of constructors.
// ...
public ElasticScaleContext(ShardMap shardMap, T shardingKey, string connectionStr)
    : base(
        OpenDDRConnection(shardMap, shardingKey, connectionStr),
        true)  // contextOwnsConnection
{
}

public static SqlConnection OpenDDRConnection(
    ShardMap shardMap,
    T shardingKey,
    string connectionStr)
{
    // No initialization.
    Database.SetInitializer<ElasticScaleContext<T>>(null);

    // Ask shard map to broker a validated connection for the given key.
    SqlConnection conn = null;
    try
    {
        conn = shardMap.OpenConnectionForKey(
            shardingKey,
            connectionStr,
            ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", shardingKey);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}
// ...
```

Ahora, SESSION\_CONTEXT se establece automáticamente con el valor de TenantId especificado cada vez que se invoca ElasticScaleContext:

```csharp
// Program.cs
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        var query = from b in db.Blogs
                    orderby b.Name
                    select b;

        Console.WriteLine("All blogs for TenantId {0}:", tenantId);
        foreach (var item in query)
        {
            Console.WriteLine(item.Name);
        }
    }
});
```

### <a name="adonet-sqlclient"></a>SqlClient de ADO.NET

En el caso de las aplicaciones que utilizan ADO.NET SqlClient, cree una función de contenedor alrededor del método ShardMap.OpenConnectionForKey. Establezca que el contenedor establezca TenantId automáticamente en SESSION\_CONTEXT en el valor actual de TenantId antes de devolver una conexión. Para asegurarse de que SESSION\_CONTEXT está siempre establecido, debe abrir conexiones únicamente con esta función de contenedor.

```csharp
// Program.cs
// Wrapper function for ShardMap.OpenConnectionForKey() that
// automatically sets SESSION_CONTEXT with the correct
// tenantId before returning a connection.
// As a best practice, you should only open connections using this method
// to ensure that SESSION_CONTEXT is always set before executing a query.
// ...
public static SqlConnection OpenConnectionForTenant(
    ShardMap shardMap, int tenantId, string connectionStr)
{
    SqlConnection conn = null;
    try
    {
        // Ask shard map to broker a validated connection for the given key.
        conn = shardMap.OpenConnectionForKey(
            tenantId, connectionStr, ConnectionOptions.Validate);

        // Set TenantId in SESSION_CONTEXT to shardingKey
        // to enable Row-Level Security filtering.
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText =
            @"exec sp_set_session_context
                @key=N'TenantId', @value=@shardingKey";
        cmd.Parameters.AddWithValue("@shardingKey", tenantId);
        cmd.ExecuteNonQuery();

        return conn;
    }
    catch (Exception)
    {
        if (conn != null)
        {
            conn.Dispose();
        }
        throw;
    }
}

// ...

// Example query via ADO.NET SqlClient.
// If row-level security is enabled, only Tenant 4's blogs are listed.
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (SqlConnection conn = OpenConnectionForTenant(
        sharding.ShardMap, tenantId4, connStrBldr.ConnectionString))
    {
        SqlCommand cmd = conn.CreateCommand();
        cmd.CommandText = @"SELECT * FROM Blogs";

        Console.WriteLine(@"--
All blogs for TenantId {0} (using ADO.NET SqlClient):", tenantId4);

        SqlDataReader reader = cmd.ExecuteReader();
        while (reader.Read())
        {
            Console.WriteLine("{0}", reader["Name"]);
        }
    }
});

```

## <a name="2-data-tier-create-row-level-security-policy"></a>2. Capa de datos: cree una directiva de seguridad de nivel de fila

### <a name="create-a-security-policy-to-filter-the-rows-each-tenant-can-access"></a>Creación de una directiva de seguridad para filtrar las filas a las que puede acceder cada inquilino

Ahora que la aplicación establece SESSION\_CONTEXT con el valor actual de TenantId antes de realizar consultas, una directiva de seguridad de RLS puede filtrar dichas consultas y excluir las filas que tengan otro valor de TenantId.

RLS se implementa en Transact-SQL. Una función definida por el usuario define la lógica de acceso y una directiva de seguridad enlaza dicha función a cualquier número de tablas. Para este proyecto:

1. La función simplemente comprueba que la aplicación está conectada a la base de datos y que el valor de TenantId almacenado en SESSION\_CONTEXT coincide con el valor de TenantId de una fila determinada.
    - La aplicación está conectada, en lugar de otro usuario de SQL.

2. Un predicado FILTER permite que las filas que pasen el filtro de TenantId atraviesen las consultas SELECT, UPDATE y DELETE.
    - Un predicado BLOCK impide que las filas que no pasan el filtro se inserten o actualicen con INSERT o UPDATE.
    - Si SESSION\_CONTEXT no se ha establecido, la función devuelve NULL y no hay filas visibles ni que puedan insertarse.

Para habilitar RLS en todas las particiones, ejecute la siguiente instrucción T-SQL con Visual Studio (SSDT), SSMS o el script de PowerShell incluido en el proyecto. O bien, si utiliza [trabajos de Elastic Database](./elastic-jobs-overview.md), puede automatizar la ejecución de esta instrucción T-SQL en todas las particiones.

```sql
CREATE SCHEMA rls; -- Separate schema to organize RLS objects.
GO

CREATE FUNCTION rls.fn_tenantAccessPredicate(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        -- Use the user in your application’s connection string.
        -- Here we use 'dbo' only for demo purposes!
        WHERE DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo')
        AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId;
GO

CREATE SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Blogs,
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.Posts;
GO
```

> [!TIP]
> En un proyecto complejo, es posible que deba agregar el predicado a cientos de tablas, lo que puede resultar muy tedioso. Hay un procedimiento almacenado auxiliar que genera una directiva de seguridad y agrega un predicado a todas las tablas de un esquema. Para más información, consulte la entrada de blog [Apply Row-Level Security to all tables – helper script (blog)](https://techcommunity.microsoft.com/t5/sql-server/apply-row-level-security-to-all-tables-helper-script/ba-p/384360)(Aplicación de la seguridad de nivel de fila a todas las tablas - script auxiliar).

Ahora, si vuelve a ejecutar la aplicación de ejemplo, los inquilinos ven solamente las filas que les pertenecen. Además, la aplicación no puede insertar filas que pertenezcan a inquilinos que no sea el que está conectado en ese momento a la base de datos de la partición ni puede actualizar el valor de TenantId en las filas visibles. Si la aplicación intenta realizar cualquiera de las dos acciones, se genera una excepción DbUpdateException.

Si agrega después una tabla nueva más adelante, modifique la directiva de seguridad y agregue predicados FILTER y BLOCK a la tabla nueva.

```sql
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ADD FILTER PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable,
    ADD BLOCK  PREDICATE rls.fn_tenantAccessPredicate(TenantId) ON dbo.MyNewTable;
GO
```

### <a name="add-default-constraints-to-automatically-populate-tenantid-for-inserts"></a>Adición de restricciones DEFAULT para rellenar automáticamente el TenantId para las inserciones

Puede colocar una restricción predeterminada en cada tabla para rellenar automáticamente TenantId con el valor almacenado actualmente en SESSION\_CONTEXT al insertar filas. A continuación, encontrará un ejemplo.

```sql
-- Create default constraints to auto-populate TenantId with the
-- value of SESSION_CONTEXT for inserts.
ALTER TABLE Blogs
    ADD CONSTRAINT df_TenantId_Blogs
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO

ALTER TABLE Posts
    ADD CONSTRAINT df_TenantId_Posts
    DEFAULT CAST(SESSION_CONTEXT(N'TenantId') AS int) FOR TenantId;
GO
```

Ahora la aplicación no necesita especificar un TenantId al insertar filas:

```csharp
SqlDatabaseUtils.SqlRetryPolicy.ExecuteAction(() =>
{
    using (var db = new ElasticScaleContext<int>(
        sharding.ShardMap, tenantId, connStrBldr.ConnectionString))
    {
        // The default constraint sets TenantId automatically!
        var blog = new Blog { Name = name };
        db.Blogs.Add(blog);
        db.SaveChanges();
    }
});
```

> [!NOTE]
> Si utiliza las restricciones predeterminadas para un proyecto de Entity Framework, es aconsejable que *NO* incluya la columna de TenantId en el modelo de datos EF. Esta recomendación se debe a que las consultas de Entity Framework proporcionan automáticamente los valores predeterminados que invalidan las restricciones predeterminadas creadas en T-SQL que usan SESSION\_CONTEXT.
> Para utilizar las restricciones DEFAULT en el proyecto de ejemplo, por ejemplo, debe quitar el TenantId de DataClasses.cs (y ejecutar Add-Migration en la consola de administrador de paquetes) y usar T-SQL para asegurarse de que el campo solo existe en las tablas de base de datos. De este modo, EF no proporciona automáticamente valores predeterminados incorrectos al insertar datos.

### <a name="optional-enable-a-superuser-to-access-all-rows"></a>(Opcional) Habilitar un *superusuario* para que acceda a todas las filas

Algunas aplicaciones pueden necesitar crear un *superusuario* que pueda acceder a todas las filas. Un superusuario puede habilitar la generación de informes en todos los inquilinos de todas las particiones. O bien, un superusuario puede realizar operaciones de división y combinación en particiones que implican mover filas de inquilinos entre bases de datos.

Para habilitar un superusuario, cree un nuevo usuario de SQL (`superuser` en este ejemplo) en cada base de datos de la partición. Luego, modifique la directiva de seguridad con una nueva función de predicado que permita a dicho usuario acceder a todas las filas. Dicha función se proporciona a continuación.

```sql
-- New predicate function that adds superuser logic.
CREATE FUNCTION rls.fn_tenantAccessPredicateWithSuperUser(@TenantId int)
    RETURNS TABLE
    WITH SCHEMABINDING
AS
    RETURN SELECT 1 AS fn_accessResult
        WHERE
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('dbo') -- Replace 'dbo'.
            AND CAST(SESSION_CONTEXT(N'TenantId') AS int) = @TenantId
        )
        OR
        (
            DATABASE_PRINCIPAL_ID() = DATABASE_PRINCIPAL_ID('superuser')
        );
GO

-- Atomically swap in the new predicate function on each table.
ALTER SECURITY POLICY rls.tenantAccessPolicy
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Blogs,
    ALTER FILTER PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts,
    ALTER BLOCK  PREDICATE rls.fn_tenantAccessPredicateWithSuperUser(TenantId) ON dbo.Posts;
GO
```


### <a name="maintenance"></a>Mantenimiento

- **Agregar particiones nuevas**: ejecute el script T-SQL para habilitar RLS en las nuevas particiones; en caso contrario, no se filtran las consultas en esas particiones.
- **Agregar tabla nuevas**: agregue un predicado FILTER y BLOCK a la directiva de seguridad en todas las particiones cada vez que se cree una tabla nueva. De lo contrario, no se filtran las consultas de la nueva tabla. Esta adición se puede automatizar mediante un desencadenador DDL, como se describe en [Apply Row-Level Security automatically to newly created tables (blog)](https://techcommunity.microsoft.com/t5/SQL-Server/Apply-Row-Level-Security-automatically-to-newly-created-tables/ba-p/384393)[Aplicación de la seguridad de nivel de fila a las tablas recientemente creadas (blog)].

## <a name="summary"></a>Resumen

Las herramientas de base de datos elásticas y la seguridad de nivel de fila pueden usarse juntas para escalar horizontalmente el nivel de datos de una aplicación con compatibilidad para particiones de un solo inquilino y de varios. Las particiones multiinquilino se pueden utilizar para almacenar datos de forma más eficaz. Esta eficacia es muy pronunciada cuando un gran número de inquilinos tienen pocas filas de datos. Las particiones de inquilino único pueden admitir inquilinos premium, que tienen unos requisitos de rendimiento y aislamiento más estrictos. Para obtener más información, consulte la [referencia sobre la seguridad de nivel de fila][rls].

## <a name="additional-resources"></a>Recursos adicionales

- [¿Qué es un grupo elástico de Azure?](elastic-pool-overview.md)
- [Escalado horizontal con Azure SQL Database](elastic-scale-introduction.md)
- [Modelos de diseño para las aplicaciones SaaS multiinquilino con Azure SQL Database](./saas-tenancy-app-design-patterns.md)
- [Autenticación en aplicaciones multiinquilino con Azure AD y OpenID Connect](/azure/architecture/multitenant-identity/authenticate)
- [Aplicación Tailspin Surveys](/azure/architecture/multitenant-identity/tailspin)

## <a name="questions-and-feature-requests"></a>Preguntas y solicitudes de características

Si tiene alguna pregunta, póngase en contacto con nosotros en la [Página de preguntas y respuestas de Microsoft sobre SQL Database](/answers/topics/azure-sql-database.html). Y agregue todas las solicitudes de característica al [foro de comentarios de SQL Database](https://feedback.azure.com/forums/217321-sql-database/).

<!--Image references-->
[1]: ./media/saas-tenancy-elastic-tools-multi-tenant-row-level-security/blogging-app.png
<!--anchors-->
[rls]: /sql/relational-databases/security/row-level-security
[s-d-elastic-database-client-library]:elastic-database-client-library.md