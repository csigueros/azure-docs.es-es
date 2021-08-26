---
title: Introducción a las consultas elásticas
description: La consulta elástica permite ejecutar una consulta de Transact-SQL que abarca varias bases de datos.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: overview
author: MladjoA
ms.author: mlandzic
ms.reviewer: mathoma
ms.date: 12/05/2019
ms.openlocfilehash: 21e6fec69b1ac02d1ffb37b0aa0f20f54d3c1a05
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121727098"
---
# <a name="azure-sql-database-elastic-query-overview-preview"></a>Información general sobre las consultas elásticas de Azure SQL Database (versión preliminar)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

La característica de consulta elástica (en versión preliminar) le permite ejecutar una consulta de Transact-SQL que abarca varias bases de datos en Azure SQL Database. Permite realizar consultas entre bases de datos para acceder a tablas remotas, así como conectar herramientas de Microsoft y de terceros (Excel, Power BI, Tableau, etc.) para realizar consultas en capas de datos con varias bases de datos. Con esta característica, puede escalar consultas horizontalmente a capas de datos de gran tamaño y visualizar los resultados en informes de inteligencia empresarial (BI).

## <a name="why-use-elastic-queries"></a>Razones para usar consultas elásticas

### <a name="azure-sql-database"></a>Azure SQL Database

Realice consultas entre bases de datos en Azure SQL Database completamente en T-SQL. Esto permite realizar consultas de solo lectura en bases de datos remotas y proporciona una opción para que los clientes actuales de SQL Server migren las aplicaciones que usan nombres de tres y cuatro partes, o bien un servidor vinculado a la base de datos SQL.

### <a name="available-on-standard-tier"></a>Disponible en el nivel Estándar

La consulta elástica se admite en los niveles de servicio Estándar y Premium. Consulte más adelante la sección Limitaciones de la versión preliminar, que trata sobre las limitaciones del rendimiento para los niveles de servicio inferiores.

### <a name="push-parameters-to-remote-databases"></a>Inserción de parámetros en bases de datos remotas

Ahora las consultas elásticas pueden insertar parámetros SQL en las bases de datos remotas para su ejecución.

### <a name="stored-procedure-execution"></a>Ejecución de un procedimiento almacenado

Ejecute llamadas a procedimientos almacenados remotos o funciones remotas mediante [sp\_execute\_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database).

### <a name="flexibility"></a>Flexibilidad

Las tablas externas con consulta elástica pueden hacer referencia a tablas remotas con un nombre de tabla o un esquema diferente.

## <a name="elastic-query-scenarios"></a>Escenarios de consulta elástica

El objetivo es facilitar escenarios de consulta en los que varias bases de datos aportan filas a un único resultado global. El usuario o la aplicación pueden componer la consulta directamente, o también se puede conseguir de forma indirecta mediante las herramientas que están conectadas a la base de datos. Esto resulta especialmente útil cuando se crean informes mediante herramientas de integración de datos o de BI comerciales, o bien cualquier aplicación que no se pueda cambiar. Con una consulta elástica, puede consultar varias bases de datos por medio de la conocida experiencia de conectividad de SQL Server en herramientas como Excel, Power BI, Tableau o Cognos.
Una consulta elástica facilita el acceso a toda una colección de bases de datos a través de las consultas emitidas por SQL Server Management Studio o Visual Studio. Asimismo, permite las consultas entre bases de datos desde Entity Framework u otros entornos de ORM. En la Figura 1 se muestra un escenario donde una aplicación en la nube existente (que usa la [biblioteca de cliente de bases de datos elásticas](elastic-database-client-library.md)) se compila en una capa de datos escalada horizontalmente y se usa una consulta elástica para informes entre bases de datos.

**Figura 1** Consulta usada en la capa de datos de escala horizontal

![Consulta usada en la capa de datos de escala horizontal][1]

Los escenarios de clientes para la consulta elástica se caracterizan por las siguientes topologías:

* **Particionamiento vertical: consultas entre bases de datos** (Topología 1): los datos se particionan en vertical entre varias bases de datos en una capa de datos. Normalmente, los distintos conjuntos de tablas residen en bases de datos diferentes. Esto significa que el esquema es diferente en las distintas bases de datos. Por ejemplo, todas las tablas de inventario se encuentran en una base de datos mientras que todas las relacionadas con la contabilidad se encuentran en otra. En los casos de uso habituales con esta topología, se requiere uno para realizar una consulta o compilar informes en todas las tablas de varias bases de datos.
* **Creación de partición horizontal: particionamiento** (Topología 2): los datos se particionan en horizontal para distribuir las filas en una capa de datos de escala horizontal. Con este enfoque, el esquema es idéntico en todas las bases de datos participantes. Este enfoque también se denomina "particionamiento". El particionamiento se puede realizar y administrar mediante 1) las bibliotecas de herramientas de bases de datos elásticas o 2) el particionamiento automático. Se usa una consulta elástica para realizar consultas o compilar informes en muchas particiones. Las particiones suelen ser bases de datos dentro de un grupo elástico. Puede considerar la consulta elástica como una forma eficaz de consultar todas las bases de datos de un grupo elástico a la vez, siempre que las bases de datos compartan el esquema común.

> [!NOTE]
> La consulta elástica funciona mejor en escenarios de informes en los que la mayor parte del procesamiento (filtrado, agregación) se puede realizar en el origen externo. No es adecuada para las operaciones de ETL en las que se transfieren grandes cantidades de datos desde bases de datos remotas. Para grandes cargas de trabajo de informes o escenarios de almacenamiento de datos con consultas más complejas, considere también usar [Azure Synapse Analytics](https://azure.microsoft.com/services/synapse-analytics).
>  

## <a name="vertical-partitioning---cross-database-queries"></a>Particionamiento vertical: consultas entre bases de datos

Para empezar a codificar, consulte la [introducción a la consulta entre bases de datos (creación de particiones verticales)](elastic-query-getting-started-vertical.md).

Se puede usar una consulta elástica para que los datos de una base de datos de SQL Database estén disponibles para otras bases. Esto permite que las consultas desde una base de datos hagan referencia a las tablas de cualquier otra base de datos remota en SQL Database. El primer paso consiste en definir un origen de datos externo para cada base de datos remota. El origen de datos externo se define en la base de datos local desde la que desee obtener acceso a las tablas que residen en la base de datos remota. No es necesario realizar cambios en la base de datos remota. En los escenarios típicos de particionamiento vertical en los que las diferentes bases de datos tienen esquemas distintos, se pueden usar consultas elásticas para implementar casos de uso habituales, como el acceso a datos de referencia y la consulta entre bases de datos.

> [!IMPORTANT]
> Se debe poseer el permiso ALTER ANY EXTERNAL DATA SOURCE. Este permiso está incluido en el permiso ALTER DATABASE. Se necesitan permisos ALTER ANY EXTERNAL DATA SOURCE para hacer referencia al origen de datos subyacente.
>

**Datos de referencia**: la topología se usa para la administración de datos de referencia. En la ilustración siguiente, se mantienen dos tablas (T1 y T2) con datos de referencia en una base de datos dedicada. Con una consulta elástica, ahora puede acceder a las tablas T1 y T2 de forma remota desde otras bases de datos, como se muestra en la ilustración. Use la topología 1 si las tablas de referencia son pequeñas o si las consultas remotas en la tabla de referencia tienen predicados selectivos.

**Ilustración 2** Particionamiento vertical: usar una consulta elástica para consultar datos de referencia

![Particionamiento vertical: usar una consulta elástica para consultar datos de referencia][3]

**Consultas entre bases de datos**: las consultas elásticas hacen posibles los casos de uso que requieren realizar consultas entre varias bases de datos en SQL Database. En la figura 3 se muestran cuatro bases de datos distintas: CRM, Inventory, HR y Products. Las consultas realizadas en una de las bases de datos también necesitan acceder a una de las otras bases de datos o a todas ellas. Mediante una consulta elástica, puede configurar la base de datos para este caso ejecutando unas pocas instrucciones DDL sencillas en cada una de las cuatro bases de datos. Después de esta configuración única, el acceso a una tabla remota es tan sencillo como hacer referencia a una tabla local desde las consultas T-SQL o las herramientas de BI. Se recomienda este enfoque si las consultas remotas no devuelven resultados de gran tamaño.

**Ilustración 3** Particionamiento vertical: usar una consulta elástica para consultar en varias bases de datos

![Particionamiento vertical: usar una consulta elástica para consultar en varias bases de datos][4]

Con los siguientes pasos se configuran consultas de bases de datos elásticas para escenarios de particionamiento vertical que requieren acceso a una tabla ubicada en bases de datos remotas en SQL Database con el mismo esquema:

* [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql) miClaveMaestra
* [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) miCredencial
* [CREATE/DROP EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) miOrigenDeDatos de tipo **RDBMS**
* [CREATE/DROP EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) miTabla

Después de ejecutar las instrucciones DDL, puede acceder a la tabla remota "miTabla" como si fuera una tabla local. Azure SQL Database abre automáticamente una conexión con la base de datos remota, procesa la solicitud en la base de datos remota y devuelve los resultados.

## <a name="horizontal-partitioning---sharding"></a>Particiones horizontales (particionamiento)

El uso de una consulta elástica para realizar tareas de informes en una capa de datos particionada, es decir, con particiones horizontales, requiere un [mapa de particiones de bases de datos elásticas](elastic-scale-shard-map-management.md) para representar las bases de datos de la capa de datos. Normalmente, se usa un solo mapa de particiones en este escenario y una base de datos dedicada con funcionalidades de consulta elástica (nodo principal) sirve de punto de entrada para las consultas de informes. Esta base de datos dedicada es la única que necesita acceder al mapa de particiones. La ilustración 4 muestra esta topología y su configuración con la base de datos de consulta elástica y el mapa de particiones. Para obtener más información acerca de la biblioteca de cliente de bases de datos elásticas y crear mapas de particiones, consulte [Administración de mapas de particiones](elastic-scale-shard-map-management.md).

**Ilustración 4** Particionamiento horizontal: usar una consulta elástica para informes en capas de datos particionadas

![Particionamiento horizontal: usar una consulta elástica para informes en capas de datos particionadas][5]

> [!NOTE]
> La base de datos de consulta elástica (nodo principal) puede ser una independiente o la misma que hospeda el mapa de particiones.
> Sea cual sea la configuración que elija, asegúrese de que ese nivel de servicio y el tamaño de proceso de esa base de datos es suficientemente alto como para controlar la cantidad de solicitudes de inicio de sesión/consulta esperada.

Con los siguientes pasos, se configuran consultas de bases de datos elásticas para escenarios de particionamiento horizontal que requieren acceso a un conjunto de tablas ubicadas, por lo general, en varias bases de datos remotas en SQL Database:

* [CREATE MASTER KEY](/sql/t-sql/statements/create-master-key-transact-sql) miClaveMaestra
* [CREATE DATABASE SCOPED CREDENTIAL](/sql/t-sql/statements/create-database-scoped-credential-transact-sql) miCredencial
* Cree un [mapa de particiones](elastic-scale-shard-map-management.md) que represente su capa de datos mediante la biblioteca de cliente de bases de datos elásticas.
* [CREATE/DROP EXTERNAL DATA SOURCE](/sql/t-sql/statements/create-external-data-source-transact-sql) miOrigenDeDatos de tipo **SHARD_MAP_MANAGER**
* [CREATE/DROP EXTERNAL TABLE](/sql/t-sql/statements/create-external-table-transact-sql) miTabla

Una vez que haya realizado estos pasos, puede acceder a la tabla con particiones horizontales "miTabla" como si fuera una tabla local. Azure SQL Database abre automáticamente varias conexiones paralelas con las bases de datos remotas donde se almacenan las tablas, procesa las solicitudes en las bases de datos remotas y devuelve los resultados.
Obtenga más información sobre los pasos necesarios para el escenario de particionamiento horizontal en [Consulta de bases de datos elásticas para particionamiento horizontal](elastic-query-horizontal-partitioning.md).

Para empezar a codificar, consulte [Introducción a las consultas elásticas para particionamiento horizontal (particionamiento)](elastic-query-getting-started.md).

> [!IMPORTANT]
> La ejecución correcta de una consulta elástica en un conjunto grande de bases de datos depende en gran medida de la disponibilidad de cada una de las bases de datos durante la ejecución de la consulta. Si una de las bases de datos no está disponible, se producirá un error en toda la consulta. Si tiene previsto consultar cientos o miles de bases de datos a la vez, asegúrese de que la aplicación cliente tiene insertada lógica de reintento, o considere la posibilidad de aprovechar los [trabajos de Elastic Database](./job-automation-overview.md) (versión preliminar) y consultar subconjuntos más pequeños de bases de datos, consolidando los resultados de cada consulta en un único destino.

## <a name="t-sql-querying"></a>Consultas T-SQL

Una vez que defina los orígenes de datos externos y las tablas externas, puede usar cadenas de conexión de SQL Server normales para conectarse a las bases de datos en las que definió las tablas externas. A continuación, puede ejecutar instrucciones T-SQL en las tablas externas de esa conexión, con las limitaciones que se describen a continuación. Puede encontrar más información y ejemplos de consultas de T-SQL en los temas de documentación sobre [particiones horizontales](elastic-query-horizontal-partitioning.md) y [particiones verticales](elastic-query-vertical-partitioning.md).

## <a name="connectivity-for-tools"></a>Conectividad para herramientas

Puede usar cadenas de conexión de SQL Server normales para conectar sus aplicaciones y herramientas de integración de datos o de BI a bases de datos con tablas externas. Asegúrese de que SQL Server se admite como origen de datos para la herramienta. Una vez conectadas, consulte la base de datos de consulta elástica y las tablas externas de esa base de datos como haría con cualquier otra base de datos de SQL Server a la que se conecte con su herramienta.

> [!IMPORTANT]
> Actualmente no se admite la autenticación usando Azure Active Directory con consultas elásticas.

## <a name="cost"></a>Coste

La consulta elástica se incluye en el costo de Azure SQL Database. Tenga en cuenta que se admiten las topologías en las que las bases de datos remotas se encuentran en un centro de datos diferente al punto de conexión de la consulta elástica, pero la salida de datos de las bases de datos remotas se cobra a [tarifas de Azure](https://azure.microsoft.com/pricing/details/data-transfers/) normales.

## <a name="preview-limitations"></a>Limitaciones de vista previa

* Ejecutar una consulta elástica por primera vez puede tardar unos minutos en el nivel de servicio Estándar. Esta vez es necesario cargar la funcionalidad de consulta elástica; el rendimiento de carga mejora con niveles de servicio y tamaños de proceso más altos.
* Aún no se admite el scripting de orígenes de datos externos o tablas externas desde SSMS o SSDT.
* Import/Export para SQL Database todavía no admite orígenes de datos externos ni tablas externas. Si necesita usar Importación/Exportación, quite estos objetos antes de exportar y después vuelva a crearlos después de importar.
* Actualmente, la consulta elástica solo es compatible con el acceso de solo lectura a tablas externas. Sin embargo, puede usar toda la funcionalidad de T-SQL en la base de datos donde se define la tabla externa. Esto puede ser útil, por ejemplo, para conservar resultados temporales mediante por ejemplo SELECT <column_list> INTO <local_table>, o para definir procedimientos almacenados en la base de datos de consulta elástica que hacen referencia a tablas externas.
* A excepción de nvarchar(max), los tipos LOB (tipos espaciales incluidos) no se admiten en las definiciones de tabla externa. Como alternativa, cree una vista en la base de datos remota que convierta el tipo LOB en nvarchar(max), defina la tabla externa en la vista en lugar de la tabla base y después conviértala en el tipo LOB original en las consultas.
* Las columnas del tipo de datos nvarchar (max) que hay en el conjunto de resultados deshabilitan las técnicas de procesamiento por lotes avanzadas usadas en la implementación de la consulta elástica y pueden afectar al rendimiento de consulta para un orden de magnitud, o incluso dos órdenes de magnitud en casos de uso no canónicos en los que grandes cantidades de datos no agregados se transfieren como resultado de la consulta.
* Actualmente no se admiten estadísticas de columna con tablas externas. Se admiten las estadísticas de las tablas, pero se deben crear manualmente.
* Las consultas elásticas solo funcionan con Azure SQL Database. No se puede usar para consultar una instancia de SQL Server.

## <a name="share-your-feedback"></a>Comparta sus comentarios

Escriba más abajo sus comentarios sobre su experiencia con las consultas elásticas, en los foros de MSDN o en Stack Overflow. Estamos interesados en todo tipo de comentarios sobre el servicio (defectos, particularidades, deficiencias de característica, etc.).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener un tutorial sobre la creación de particiones verticales, consulte [Introducción a las consultas entre bases de datos (particiones verticales)](elastic-query-getting-started-vertical.md).
* Para ver la sintaxis y consultas de ejemplo para los datos con particionamiento vertical, consulte [Consulta de datos particionados verticalmente](elastic-query-vertical-partitioning.md)
* Para obtener un tutorial sobre la creación de particiones horizontales (particionamiento), consulte [Introducción a las consultas elásticas para las particiones horizontales (particionamiento)](elastic-query-getting-started.md).
* Para ver la sintaxis y consultas de ejemplo para los datos con particionamiento horizontal, consulte [Consulta de datos particionados horizontalmente.](elastic-query-horizontal-partitioning.md)
* Consulte [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) para ver un procedimiento almacenado que ejecuta una instrucción de Transact-SQL en una sola instancia remota de Azure SQL Database o un conjunto de bases de datos que actúan como particiones en un esquema de particiones horizontales.

<!--Image references-->
[1]: ./media/elastic-query-overview/overview.png
[2]: ./media/elastic-query-overview/topology1.png
[3]: ./media/elastic-query-overview/vertpartrrefdata.png
[4]: ./media/elastic-query-overview/verticalpartitioning.png
[5]: ./media/elastic-query-overview/horizontalpartitioning.png

<!--anchors-->