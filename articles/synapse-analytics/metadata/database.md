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
ms.openlocfilehash: 7b671246d3ee199719dbb3da6bf7820a876791cf
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123428297"
---
# <a name="azure-synapse-analytics-shared-database"></a>Base de datos compartida de Azure Synapse Analytics

Azure Synapse Analytics permite que los diferentes motores de cálculo de áreas de trabajo compartan bases de datos y tablas. Actualmente, las bases de datos y las tablas (basadas en Parquet o CSV) que se crean en los grupos de Apache Spark se comparten automáticamente con el motor del grupo de SQL sin servidor.

Una base de datos creada con un trabajo de Spark será visible con el mismo nombre en todos los grupos de Spark actuales y futuros del área de trabajo, incluido el motor del grupo de SQL sin servidor. No se pueden agregar objetos personalizados (tablas, vistas y procedimientos externos) directamente en esta base de datos sincronizada mediante el grupo de SQL sin servidor.

La base de datos predeterminada de Spark, llamada `default`, también estará visible en el contexto del grupo de SQL sin servidor como una base de datos llamada `default`. No se puede crear una base de datos en Spark y, después, crear otra base de datos con el mismo nombre en el grupo de SQL sin servidor.

Dado que las bases de datos se sincronizan con el grupo de SQL sin servidor de forma asincrónica, se producirá un retraso hasta que aparezcan.

## <a name="manage-a-spark-created-database"></a>Administración de una base de datos creada con Spark

Para administrar las bases de datos creadas por Spark, debe usar grupos de Apache Spark. Por ejemplo, debe crearlas o eliminarlas con un trabajo de grupo de Spark.

Los objetos de las bases de datos sincronizadas no se pueden modificar desde un grupo de SQL sin servidor.

>[!NOTE]
>No se pueden crear varias bases de datos con el mismo nombre de grupos diferentes. Si se crea una base datos de grupos de SQL sin servidor, no se podrá crear una base de datos de Spark con el mismo nombre. Del mismo modo, si la base de datos se crea en Spark, no se podrá crear una base de datos de grupos de SQL sin servidor con el mismo nombre.

## <a name="security-model"></a>Modelo de seguridad

Las tablas y bases de datos de Spark, junto con sus representaciones sincronizadas en el motor de SQL, están protegidas en el nivel de almacenamiento subyacente.

La entidad de seguridad que crea una base de datos se considera el propietario de dicha base de datos y tiene todos los derechos para la base de datos y sus objetos. El administrador de Synapse y el administrador de Synapse SQL también tendrán todos los permisos en los objetos sincronizados del grupo de SQL sin servidor de forma predeterminada. No se permite la creación de objetos personalizados (incluidos los usuarios) en bases de datos SQL sincronizadas. 

Para conceder a una entidad de seguridad, como un usuario, una aplicación de Azure AD o un grupo de seguridad, acceso a los datos subyacentes usados para las tablas externas, debe concederles permisos `read (R)` en los archivos (como los archivos de datos subyacentes de la tabla) y `execute (X)` en la carpeta donde se almacenan los archivos, además de en cada carpeta principal hasta la raíz. Puede consultar más información sobre estos permisos en la página sobre [listas de control de acceso (ACL)](/azure/storage/blobs/data-lake-storage-access-control). 

Por ejemplo, en `https://<storage-name>.dfs.core.windows.net/<fs>/synapse/workspaces/<synapse_ws>/warehouse/mytestdb.db/myparquettable/`, las entidades de seguridad deben tener permisos `X` en todas las carpetas a partir de `<fs>` hasta `myparquettable` y permisos `R` en `myparquettable` y los archivos dentro de esa carpeta, para poder leer una tabla de una base de datos (sincronizada o original).

Si una entidad de seguridad requiere la capacidad de crear objetos o de anular objetos en una base de datos, se requieren permisos `W` adicionales en las carpetas y los archivos de la carpeta `warehouse`. No se pueden modificar objetos en una base de datos desde un grupo de SQL sin servidor, solo desde Spark.

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
