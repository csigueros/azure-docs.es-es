---
title: Creación y uso de tablas de libro de contabilidad de solo anexión
description: Aprenda a crear y usar tablas de libro de contabilidad de solo anexión en Azure SQL Database.
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: f433e56140a199cdb872bc733343a8cf88c818cb
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2021
ms.locfileid: "112076409"
---
# <a name="create-and-use-append-only-ledger-tables"></a>Creación y uso de tablas de libro de contabilidad de solo anexión

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Actualmente, el libro de contabilidad de Azure SQL Database está en versión preliminar pública y está disponible en el Centro-oeste de EE. UU.

En este artículo se muestra cómo crear una [tabla de libro de contabilidad de solo anexión](ledger-append-only-ledger-tables.md) en Azure SQL Database. Luego, insertará valores en la tabla de libro de contabilidad de solo anexión e intentará realizar actualizaciones de los datos. Por último, verá los resultados mediante la vista de libro de contabilidad. Se usará un ejemplo de un sistema de acceso con clave de tarjeta de una instalación, que es un modelo de sistema de solo anexión. Este ejemplo le proporcionará una visión práctica de la relación entre la tabla de libro de contabilidad de solo anexión y su vista de libro de contabilidad correspondiente.

Para más información, consulte [Tablas de libro de contabilidad de solo anexión](ledger-append-only-ledger-tables.md).

## <a name="prerequisites"></a>Prerrequisitos

- Azure SQL Database con el libro de contabilidad habilitado. Si aún no ha creado una base de datos en SQL Database, consulte [Inicio rápido: Creación de una base de datos en Azure SQL Database con el libro de contabilidad habilitado](ledger-create-a-single-database-with-ledger-enabled.md).
- [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

## <a name="create-an-append-only-ledger-table"></a>Creación de tablas de libro de contabilidad de solo anexión

Crearemos una tabla `KeyCardEvents` con el esquema siguiente.

| Nombre de la columna | Tipo de datos | Descripción |
|--|--|--|
| EmployeeID | int | Identificador único del empleado que accede al edificio. |
| AccessOperationDescription | nvarchar (MAX) | Operación de acceso del empleado. |
| Timestamp | datetime2 | Fecha y hora en que el empleado ha accedido al edificio. |

> [!IMPORTANT]
> Para crear tablas de libro de contabilidad de solo anexión se necesita el permiso **ENABLE LEDGER**. Para más información sobre los permisos relacionados con las tablas de libro de contabilidad, consulte [Permisos](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

1. Use [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio) para crear un esquema y una tabla denominados `[AccessControl].[KeyCardEvents]`.

   ```sql
   CREATE SCHEMA [AccessControl] 
   CREATE TABLE [AccessControl].[KeyCardEvents]
       (
           [EmployeeID] INT NOT NULL,
           [AccessOperationDescription] NVARCHAR (MAX) NOT NULL,
           [Timestamp] Datetime2 NOT NULL
       )
       WITH (
          LEDGER = ON (
                       APPEND_ONLY = ON
                       )
         );
   ```

1. Agregue un nuevo evento de acceso al edificio en la tabla `[AccessControl].[KeyCardEvents]` con los valores siguientes.

   ```sql
   INSERT INTO [AccessControl].[KeyCardEvents]
   VALUES ('43869', 'Building42', '2020-05-02T19:58:47.1234567')
   ```

1. Vea el contenido de la tabla KeyCardEvents y especifique las columnas [GENERATED ALWAYS](/sql/t-sql/statements/create-table-transact-sql#generate-always-columns) que se agregan a la [tabla de libro de contabilidad de solo anexión](ledger-append-only-ledger-tables.md).

   ```sql
   SELECT *
        ,[ledger_start_transaction_id]
        ,[ledger_start_sequence_number]
   FROM [AccessControl].[KeyCardEvents]
   ```

   :::image type="content" source="media/ledger/append-only-how-to-keycardevent-table.png" alt-text="Captura de pantalla que muestra los resultados de consultar la tabla KeyCardEvents.":::

1. Intente actualizar la tabla `KeyCardEvents` cambiando `EmployeeID` de `43869` a `34184.`.

   ```sql
   UPDATE [AccessControl].[KeyCardEvents] SET [EmployeeID] = 34184
   ```

   Recibirá un mensaje de error que indica que no están permitidas las actualizaciones para la tabla de libro de contabilidad de solo anexión.

   :::image type="content" source="media/ledger/append-only-how-to-1.png" alt-text="Captura de pantalla que muestra el mensaje de error de solo anexión.":::

## <a name="next-steps"></a>Pasos siguientes

- [Libro de contabilidad de base de datos](ledger-database-ledger.md) 
- [Administración de resúmenes y comprobación de la base de datos](ledger-digest-management-and-database-verification.md)
- [Tablas de libro de contabilidad de solo anexión](ledger-append-only-ledger-tables.md) 
- [Tablas de libro de contabilidad actualizables](ledger-updatable-ledger-tables.md)
- [Creación y uso de tablas de libro de contabilidad actualizables](ledger-how-to-updatable-ledger-tables.md)
- [Acceso a los resúmenes almacenados en Azure Confidential Ledger (ACL)](ledger-how-to-access-acl-digest.md)
- [Comprobación de una tabla de libro de contabilidad para detectar alteraciones](ledger-verify-database.md)
