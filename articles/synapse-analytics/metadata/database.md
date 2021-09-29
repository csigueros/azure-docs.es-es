---
title: Base de datos compartida
description: Azure Synapse Analytics proporciona un modelo de metadatos compartido en el que la creación de una base de datos en un grupo de Apache Spark sin servidor hará que sea accesible desde su grupo de SQL sin servidor y los motores del grupo de SQL.
services: synapse-analytics
author: MikeRys
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: metadata
ms.date: 05/01/2020
ms.author: mrys
ms.reviewer: jrasnick
ms.custom: devx-track-csharp
ms.openlocfilehash: 99b6aad8c6c916c91e97e3059e3dbf92e5dd7aa8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124804166"
---
# <a name="azure-synapse-analytics-shared-database"></a>Base de datos compartida de Azure Synapse Analytics

Azure Synapse Analytics permite que los diferentes motores de cálculo de áreas de trabajo compartan bases de datos y tablas. Actualmente, las bases de datos y las tablas de Parquet que se crean en los grupos de Apache Spark se comparten automáticamente con el motor del grupo de SQL sin servidor.

Una base de datos creada con un trabajo de Spark será visible con el mismo nombre en todos los grupos de Spark actuales y futuros del área de trabajo, incluido el motor del grupo de SQL sin servidor. No se pueden agregar objetos personalizados (tablas, vistas y procedimientos externos) directamente en esta base de datos replicada mediante el grupo de SQL sin servidor.

La base de datos predeterminada de Spark, llamada `default`, también estará visible en el contexto del grupo de SQL sin servidor como una base de datos llamada `default`. 

Dado que las bases de datos se sincronizan con el grupo de SQL sin servidor de forma asincrónica, se producirá un retraso hasta que aparezcan.

## <a name="manage-a-spark-created-database"></a>Administración de una base de datos creada con Spark

Use Spark para administrar las bases de datos creadas con Spark. Por ejemplo, elimínelas mediante un trabajo de grupo de Spark y cree tablas en ellas desde Spark.

Si crea objetos en una base de datos creada con Spark mediante el grupo de SQL sin servidor o intenta eliminar la base de datos, la operación se realizará correctamente. Pero la base de datos original de Spark no se cambiará.

## <a name="how-name-conflicts-are-handled"></a>Control de los conflictos de nombres

Si el nombre de una base de datos de Spark entra en conflicto con el nombre de una base de datos existente del grupo de SQL sin servidor, se anexa un sufijo a la base de datos de Spark en el grupo de SQL sin servidor. El sufijo del grupo de SQL sin servidor es `_<workspace name>-ondemand-DefaultSparkConnector`.

Por ejemplo, si se crea una base de datos de Spark llamada `mydb` en el área de trabajo `myws` de Azure Synapse y ya existe una base de datos del grupo de SQL sin servidor con ese nombre, se tendrá que hacer referencia a la base de datos de Spark en el grupo de SQL sin servidor con el nombre `mydb_myws-ondemand-DefaultSparkConnector`.

> [!CAUTION]
> Precaución: No debe tener una dependencia de este comportamiento.

## <a name="security-model"></a>Modelo de seguridad

Las tablas y bases de datos de Spark, junto con sus representaciones sincronizadas en el motor de SQL, están protegidas en el nivel de almacenamiento subyacente.

La entidad de seguridad que crea una base de datos se considera el propietario de dicha base de datos y tiene todos los derechos para la base de datos y sus objetos.

Para conceder a una entidad de seguridad, como un usuario o un grupo de seguridad, el acceso a una base de datos, proporcione los permisos de archivo y carpeta POSIX adecuados para las carpetas y los archivos subyacentes en el directorio `warehouse`. 

Por ejemplo, para que una entidad de seguridad pueda leer una tabla de una base de datos, todas las carpetas que comienzan en la carpeta de la base de datos del directorio `warehouse` tienen que tener asignados los permisos `X` y `R` a esa entidad de seguridad. Además, los archivos (como los archivos de datos subyacentes de la tabla) requieren permisos `R`. 

Si una entidad de seguridad requiere la capacidad de crear objetos o de anular objetos en una base de datos, se requieren permisos `W` adicionales en las carpetas y los archivos de la carpeta `warehouse`.

## <a name="examples"></a>Ejemplos

### <a name="create-and-connect-to-spark-database-with-serverless-sql-pool"></a>Creación y conexión a una base de datos de Spark con un grupo de SQL sin servidor

En primer lugar, cree una nueva base de datos de Spark denominada `mytestdb` con un clúster de Spark que ya haya creado en el área de trabajo. Puede lograrlo, por ejemplo, mediante un cuaderno C# de Spark con la siguiente instrucción .NET para Spark:

```csharp
spark.Sql("CREATE DATABASE mytestdb")
```

Después de un breve retraso, puede ver la base de datos del grupo de SQL sin servidor. Por ejemplo, ejecute la siguiente instrucción desde el grupo de SQL sin servidor.

```sql
SELECT * FROM sys.databases;
```

Compruebe que `mytestdb` se incluye en los resultados.

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre los metadatos compartidos de Azure Synapse Analytics](overview.md)
- [Más información sobre las tablas de metadatos compartidos de Azure Synapse Analytics](table.md)