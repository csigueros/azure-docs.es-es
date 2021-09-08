---
title: Funcionalidades de varios modelos
description: Microsoft Azure SQL permite trabajar con varios modelos de datos en la misma base de datos.
services: sql-database
ms.service: sql-db-mi
ms.subservice: service-overview
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: mathoma, urmilano
ms.date: 12/17/2018
ms.openlocfilehash: 93005f665f816f0f6dcde3a1c06748622f4bf271
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113585832"
---
# <a name="multi-model-capabilities-of-azure-sql-database-and-sql-managed-instance"></a>Funcionalidades de varios modelos de Azure SQL Database y SQL Managed Instance
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Las bases de datos de varios modelos permiten almacenar datos en varios formatos como datos relacionales, grafos, documentos JSON o XML, datos espaciales y pares clave-valor, así como trabajar con dichos datos.

La [familia de productos de Azure SQL](azure-sql-iaas-vs-paas-what-is-overview.md) utiliza el modelo relacional que proporciona el mejor rendimiento para diversas aplicaciones de uso general. Sin embargo, los productos de Azure SQL como Azure SQL Database y SQL Managed Instance no se limitan a los datos relacionales. Permiten usar formatos no relacionales que están estrechamente integrados en el modelo relacional.

Considere la posibilidad de usar funcionalidades de varios modelos de Azure SQL en los casos siguientes:

- Tiene cierta información o estructuras que se ajustan mejor a los modelos de NoSQL y no quiere usar una base de datos de NoSQL independiente.
- La mayoría de los datos es adecuada para un modelo relacional y necesita modelar algunas partes de los datos en un estilo de NoSQL.
- Quiere usar el lenguaje Transact-SQL para consultar y analizar datos relacionales y NoSQL, y, a continuación, integrar esos datos con una variedad de herramientas y aplicaciones que pueden usar el lenguaje SQL.
- Quiere aplicar características de base de datos como [tecnologías en memoria](in-memory-oltp-overview.md) para mejorar el rendimiento de los análisis o el procesamiento de las estructuras de datos NoSQL. Puede usar la [replicación transaccional](managed-instance/replication-transactional-overview.md) o las [réplicas legibles](database/read-scale-out.md) para crear copias de los datos y descargar algunas cargas de trabajo analíticas de la base de datos principal.

En las secciones siguientes se describen las funcionalidades de varios modelos más importantes de Azure SQL.

> [!Note]
> Puede usar expresiones de ruta de acceso JSON, expresiones XQuery y XPath, funciones espaciales y expresiones de consulta de grafos en la misma consulta de Transact-SQL para acceder a los datos almacenados en la base de datos. Cualquier herramienta o lenguaje de programación que puede ejecutar consultas de Transact-SQL, también puede usar esa interfaz de consulta para obtener acceso a datos de varios modelos. Esta es la diferencia clave de las bases de datos de varios modelos como [Azure Cosmos DB](../cosmos-db/index.yml) que proporcionan una API especializada para modelos de datos.

## <a name="graph-features"></a>Características de grafos

Los productos de Azure SQL ofrecen funcionalidades de base de datos de grafos para modelar las relaciones de varios a varios en la base de datos. Un grafo es una colección de nodos (o vértices) y bordes (o relaciones). Un nodo representa una entidad (por ejemplo, una persona o una organización). Un borde representa una relación entre los dos nodos a los que se conecta (por ejemplo, me gusta o amigos). 

Estas son algunas características que hacen que una base de datos de grafos sea única:

- Los bordes son entidades de primera clase en una base de datos de grafos. Pueden tener atributos o propiedades asociados.
- Un solo borde puede conectar flexiblemente varios nodos en una base de datos de grafos.
- Puede expresar fácilmente coincidencias de patrones y consultas de navegación en saltos múltiples.
- Puede expresar fácilmente consultas polimórficas y cierres transitivos.

Las [relaciones de grafos y funcionalidades de consulta de grafos](/sql/relational-databases/graphs/sql-graph-overview) están integradas en Transact-SQL y reciben las ventajas de usar el motor de base de datos de SQL Server como sistema fundamental de administración de bases de datos. Las características de Graph usan consultas estándar de Transact-SQL mejoradas con el operador de grafos `MATCH` para consultar los datos del grafo.

Una base de datos relacional puede lograr lo mismo que una base de datos de grafos. Sin embargo, una base de datos de grafos puede facilitar la expresión de determinadas consultas. La decisión para elegir una u otra puede basarse en los siguientes factores:

- Debe modelar datos jerárquicos donde un nodo puede tener varios elementos primarios, por lo que no puede usar [el tipo de datos hierarchyId](/sql/t-sql/data-types/hierarchyid-data-type-method-reference).
- La aplicación tiene relaciones complejas de varios a varios. A medida que la aplicación evoluciona, se agregan nuevas relaciones.
- Necesita analizar las relaciones y los datos interconectados.
- Quiere usar condiciones de búsqueda de T-SQL específicas del grafo, como [SHORTEST_PATH](/sql/relational-databases/graphs/sql-graph-shortest-path).

## <a name="json-features"></a>Característica de JSON

Con los productos de Azure SQL puede analizar y consultar datos representados en formato de [notación de objetos JavaScript (JSON)](https://www.json.org/), y exportar los datos relacionales como texto JSON. [JSON](/sql/relational-databases/json/json-data-sql-server) es una característica principal del motor de base de datos de SQL Server.

Las características JSON permiten colocar documentos JSON en tablas, transformar datos relacionales en documentos JSON y transformar documentos JSON en datos relacionales. Puede usar el lenguaje Transact-SQL estándar mejorado con funciones JSON para analizar documentos. También puede usar índices no agrupados, índices de almacén de columnas o tablas optimizadas para memoria para optimizar las consultas.

JSON es un formato de datos conocido para intercambiar datos en aplicaciones web y móviles modernas. JSON también se utiliza para almacenar datos semiestructurados en archivos de registro o en bases de datos NoSQL. Muchos servicios web REST devuelven resultados con formato de texto JSON o bien aceptan datos con este formato. 

La mayoría de servicios de Azure tienen puntos de conexión de REST que devuelven o consumen JSON. Estos servicios incluyen [Azure Cognitive Search](https://azure.microsoft.com/services/search/), [Azure Storage](https://azure.microsoft.com/services/storage/)y [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Si tiene texto JSON, puede extraer datos de JSON o comprobar que el formato JSON sea correcto con las funciones integradas [JSON_VALUE](/sql/t-sql/functions/json-value-transact-sql), [JSON_QUERY](/sql/t-sql/functions/json-query-transact-sql) y [ISJSON](/sql/t-sql/functions/isjson-transact-sql). Las otras funciones son:

- [JSON_MODIFY](/sql/t-sql/functions/json-modify-transact-sql): le permite actualizar valores dentro del texto JSON. 
- [OPENJSON](/sql/t-sql/functions/openjson-transact-sql): puede transformar una matriz de objetos JSON en un conjunto de filas, para consultas y análisis más avanzados. Se puede ejecutar cualquier consulta SQL en el conjunto de resultados devuelto. 
- [FOR JSON](/sql/relational-databases/json/format-query-results-as-json-with-for-json-sql-server): le permite dar formato de texto JSON a los datos almacenados en las tablas relacionales.

![Diagrama que muestra las funciones JSON.](./media/multi-model-features/image_1.png)

Para más información, consulte [Cómo trabajar con datos JSON](database/json-features.md). 

Puede utilizar modelos de documento en lugar de los modelos relacionales en algunos escenarios concretos:

- La alta normalización del esquema no ofrece ventajas significativas, ya que accede a todos los campos de objetos a la vez o nunca actualiza las partes normalizadas de los objetos. Sin embargo, el modelo normalizado aumenta la complejidad de las consultas porque debe combinar un gran número de tablas para obtener los datos.
- Está trabajando con aplicaciones que usan de forma nativa los documentos de JSON para la comunicación o como modelos de datos y no desea introducir más capas que transforman los datos relacionales en JSON y viceversa.
- Se necesita simplificar el modelo de datos anulando la normalización de las tablas secundarias o los patrones de valor de objeto de entidad.
- Deberá cargar o exportar los datos almacenados en formato JSON sin ninguna herramienta adicional que analice los datos.

## <a name="xml-features"></a>Características de XML

Las características XML permiten almacenar e indexar datos XML en la base de datos y utilizar operaciones XQuery y XPath nativas para trabajar con datos XML. Los productos de Azure SQL tienen un tipo de datos XML especializado e integrado y funciones de consulta que procesan datos XML.

El motor de base de datos de SQL Server proporciona una plataforma eficaz para desarrollar aplicaciones para la administración de datos semiestructurados. La [compatibilidad con XML](/sql/relational-databases/xml/xml-data-sql-server) está integrada en todos los componentes del motor de base de datos e incluye:

- La capacidad de almacenar valores XML de forma nativa en una columna de tipo de datos XML cuyo tipo se puede determinar según una colección de esquemas XML, o se puede dejar sin tipo. Puede indexar la columna XML.
- La capacidad de especificar una consulta con datos XQuery almacenados en columnas y variables del tipo XML. Puede usar las funcionalidades de XQuery en cualquier consulta de Transact-SQL que tiene acceso a un modelo de datos que se utiliza en la base de datos.
- Indexación automática de todos los elementos de documentos XML mediante el [índice XML principal](/sql/relational-databases/xml/xml-indexes-sql-server#primary-xml-index). O bien, puede especificar las rutas de acceso exactas que se deben indexar mediante el [índice XML secundario](/sql/relational-databases/xml/xml-indexes-sql-server#secondary-xml-indexes).
- `OPENROWSET`, que permite la carga masiva de datos XML.
- La capacidad de transformar datos relacionales en formato XML.

Puede utilizar modelos de documento en lugar de los modelos relacionales en algunos escenarios concretos:

- La alta normalización del esquema no ofrece ventajas significativas, ya que accede a todos los campos de objetos a la vez o nunca actualiza las partes normalizadas de los objetos. Sin embargo, el modelo normalizado aumenta la complejidad de las consultas porque debe combinar un gran número de tablas para obtener los datos.
- Está trabajando con aplicaciones que usan de forma nativa los documentos de XML para la comunicación o como modelos de datos y no desea introducir más capas que transforman los datos relacionales en JSON y viceversa.
- Se necesita simplificar el modelo de datos anulando la normalización de las tablas secundarias o los patrones de valor de objeto de entidad.
- Deberá cargar o exportar los datos almacenados en formato XML sin ninguna herramienta adicional que analice los datos.

## <a name="spatial-features"></a>Características espaciales

Los datos espaciales representan información sobre la ubicación física y la forma de objetos. Estos objetos pueden ser ubicaciones de punto u objetos más complejos, como países o regiones, carreteras o lagos.

Azure SQL admite dos tipos de datos espaciales: 

- El tipo geométrico representa los datos en un sistema de coordenadas euclidiano (plano).
- El tipo geográfico representa los datos en un sistema de coordenadas elipsoidales.

Las características espaciales de Azure SQL le permiten almacenar datos geométricos y geográficos. Puede usar objetos espaciales en Azure SQL para analizar y consultar datos representados en formato JSON y exportar los datos relacionales como texto JSON. Estos objetos espaciales incluyen [Point](/sql/relational-databases/spatial/point), [LineString](/sql/relational-databases/spatial/linestring) y [Polygon](/sql/relational-databases/spatial/polygon). Azure SQL proporciona también [índices espaciales](/sql/relational-databases/spatial/spatial-indexes-overview) especializados que puede usar para mejorar el rendimiento de las consultas espaciales.

La [compatibilidad espacial](/sql/relational-databases/spatial/spatial-data-sql-server) es una característica fundamental del motor de base de datos de SQL Server.

## <a name="key-value-pairs"></a>Pares clave-valor

Los productos de Azure SQL no tienen tipos o estructuras especializadas que admiten pares de clave-valor, porque las estructuras clave-valor pueden representarse de forma nativa como tablas relacionales estándar:

```sql
CREATE TABLE Collection (
  Id int identity primary key,
  Data nvarchar(max)
)
```

Puede personalizar esta estructura clave-valor para satisfacer sus necesidades sin restricciones. Por ejemplo, el valor puede ser un documento XML en lugar del tipo `nvarchar(max)`. Si el valor es un documento JSON, puede usar una restricción `CHECK` que compruebe la validez del contenido JSON. Puede colocar cualquier número de valores relacionados con una clave en las columnas adicionales. Por ejemplo:

- Agregue columnas calculadas e índices para simplificar y optimizar el acceso a los datos.
- Defina la tabla como una tabla optimizada para memoria y de solo esquema para obtener un mejor rendimiento.

Para obtener un ejemplo de cómo un modelo relacional se puede usar eficazmente como una solución de par clave-valor en la práctica, consulte [Cómo bwin usa SQL Server 2016 In-Memory OLTP para lograr un rendimiento y una escala sin precedentes](/archive/blogs/sqlcat/how-bwin-is-using-sql-server-2016-in-memory-oltp-to-achieve-unprecedented-performance-and-scale). En este caso práctico, bwin usó un modelo relacional para su solución almacenamiento en caché de ASP.NET para lograr 1,2 millones de lotes por segundo.

## <a name="next-steps"></a>Pasos siguientes

Las funcionalidades de varios modelos son características de motor de base de datos de SQL Server fundamentales que se comparten entre productos de Azure SQL. Para más información acerca de estas características, consulte estos artículos:

- [Gráfico de procesamiento con SQL Server y Azure SQL Database](/sql/relational-databases/graphs/sql-graph-overview)
- [Datos JSON en SQL Server](/sql/relational-databases/json/json-data-sql-server)
- [Datos espaciales de SQL Server](/sql/relational-databases/spatial/spatial-data-sql-server)
- [Datos XML en SQL Server](/sql/relational-databases/xml/xml-data-sql-server)
- [Rendimiento del almacén de clave-valor en Azure SQL Database](https://devblogs.microsoft.com/azure-sql/azure-sql-database-as-a-key-value-store/)
