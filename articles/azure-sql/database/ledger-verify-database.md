---
title: Comprobación de una tabla de libro de contabilidad para detectar alteraciones
description: En este artículo se describe cómo comprobar si se ha alterado una tabla de Azure SQL Database.
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: how-to
author: JasonMAnderson
ms.author: janders
ms.reviewer: vanto
ms.date: 07/23/2021
ms.custom: references_regions
ms.openlocfilehash: 7684cc6ecff0d4e36fec0d7df54edd8602aa145f
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/24/2021
ms.locfileid: "114665501"
---
# <a name="verify-a-ledger-table-to-detect-tampering"></a>Comprobación de una tabla de libro de contabilidad para detectar alteraciones

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Actualmente, el libro de contabilidad de Azure SQL Database se encuentra en versión preliminar pública y está disponible en el Oeste de Europa, Sur de Brasil y Centro-oeste de EE. UU.

En este artículo, comprobará la integridad de los datos de las tablas de libro de contabilidad de Azure SQL Database. Si ha activado **Enable automatic digest storage** (Habilitar el almacenamiento de resumen automático) al [crear la instancia de SQL Database](ledger-create-a-single-database-with-ledger-enabled.md), siga las instrucciones de Azure Portal para generar automáticamente el script de Transact-SQL (T-SQL) necesario para comprobar el libro de contabilidad de la base de datos en el [Editor de consultas](connect-query-portal.md). De lo contrario, siga las instrucciones de T-SQL mediante [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

## <a name="prerequisites"></a>Prerrequisitos

- Tener una suscripción de Azure activa. En caso de no tener ninguna, [cree una cuenta gratuita](https://azure.microsoft.com/free/).
- [Creación de una base de datos de SQL Database con el libro de contabilidad habilitado](ledger-create-a-single-database-with-ledger-enabled.md)
- [Cree y use tablas de libro de contabilidad actualizables](ledger-how-to-updatable-ledger-tables.md) o [cree y use tablas de libro de contabilidad de solo anexión](ledger-how-to-append-only-ledger-tables.md).

## <a name="run-ledger-verification-for-sql-database"></a>Ejecución de la comprobación del libro de contabilidad para SQL Database

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Abra [Azure Portal](https://portal.azure.com/), seleccione **Todos los recursos** y busque la base de datos que quiere comprobar. Seleccione esa base de datos en SQL Database.

     :::image type="content" source="media/ledger/ledger-portal-all-resources.png" alt-text="Captura de pantalla que muestra Azure Portal con la pestaña Todos los recursos seleccionada.":::

1. En **Seguridad**, seleccione la opción **Libro de contabilidad**.

   :::image type="content" source="media/ledger/ledger-portal-manage-ledger.png" alt-text="Captura de pantalla que muestra Azure Portal con la pestaña Security Ledger (Libro de contabilidad de seguridad) seleccionada.":::

1. En el panel **Libro de contabilidad**, seleccione **</> Verify database** (Comprobar base de datos) y seleccione el icono de **copia** en el texto rellenado previamente en la ventana.

     :::image type="content" source="media/ledger/ledger-portal-verify.png" alt-text="Botón para comprobar base de datos en Azure Portal":::

   > > [!IMPORTANT]
   > Si no ha configurado el almacenamiento de resúmenes automático para los resúmenes de la base de datos y, en su lugar, los administra manualmente, no copie este script. Continúe con el paso 6.

1. Abra el **Editor de consultas** en el menú de la izquierda.

     :::image type="content" source="media/ledger/ledger-portal-open-query-editor.png" alt-text="Captura de pantalla que muestra la opción de menú Editor de consultas de Azure Portal.":::

1. En el editor de consultas, pegue el script de T-SQL que copió en el paso 3 y seleccione **Ejecutar**. Continúe con el paso 8.

   :::image type="content" source="media/ledger/ledger-portal-run-query-editor.png" alt-text="Captura de pantalla que muestra la opción Ejecutar del editor de consultas de Azure Portal para comprobar la base de datos.":::

1. Si usa el almacenamiento de resúmenes manual, escriba el siguiente código T-SQL en el editor de consultas para recuperar el resumen más reciente de la base de datos. Copie el resumen de los resultados devueltos para el paso siguiente.

   ```sql
   EXECUTE sp_generate_database_ledger_digest
   ```
   
1. En el editor de consultas, pegue el siguiente script de T-SQL, reemplazando `<database_digest>` por el resumen que copió en el paso 6 y seleccione **Ejecutar**.

   ```sql
   EXECUTE sp_verify_database_ledger N'<database_digest>'
   ```

1. La comprobación devuelve los mensajes siguientes en la ventana **Resultados**.

   - Si no se ha alterado la base de datos, el mensaje será el siguiente:

       ```output
       Ledger verification successful
       ```

   - Si se ha producido una alteración en la base de datos, aparecerá el siguiente error en la ventana **Mensajes**.
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger does not match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-automatic-digest-storage"></a>[T-SQL que usa el almacenamiento de resúmenes automático](#tab/t-sql-automatic)

1. Conéctese a la base de datos mediante [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

1. Cree una consulta con la siguiente instrucción T-SQL:

   ```sql
   DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);SELECT @digest_locations as digest_locations;
   BEGIN TRY
       EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
       SELECT 'Ledger verification succeeded.' AS Result;
   END TRY
   BEGIN CATCH
       THROW;
   END CATCH
   ```

1. Ejecutar la consulta. Verá que **digest_locations** devuelve la ubicación actual en la que se almacenan los resúmenes de la base de datos y las ubicaciones anteriores. **Resultado** devuelve el éxito o el error de la comprobación del libro de contabilidad.

   :::image type="content" source="media/ledger/verification_script_exectution.png" alt-text="Captura de pantalla de la ejecución de la comprobación del libro de contabilidad mediante Azure Data Studio.":::

1. Abra el conjunto de resultados de **digest_locations** para ver las ubicaciones de los resúmenes. En el ejemplo siguiente se muestran dos ubicaciones de almacenamiento de resúmenes para esta base de datos: 

   - **path** indica la ubicación de los resúmenes.
   - **last_digest_block_id** indica el identificador de bloque del último resumen almacenado en la ubicación **path**.
   - **is_current** indica si la ubicación de **path** es la actual (true) o la anterior (false).

       ```json
       [
        {
            "path": "https:\/\/digest1.blob.core.windows.net\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 10016,
            "is_current": true
        },
        {
            "path": "https:\/\/jandersneweracl.confidential-ledger.azure.com\/sqldbledgerdigests\/janderstestportal2server\/jandersnewdb\/2021-05-20T04:39:47.6570000",
            "last_digest_block_id": 1704,
            "is_current": false
        }
       ]
       ```

   > [!IMPORTANT]
   > Al ejecutar la comprobación del libro de contabilidad, inspeccione la ubicación de **digest_locations** para asegurarse de que los resúmenes usados en la comprobación se recuperan de las ubicaciones que espera. Quiere asegurarse de que un usuario con privilegios no haya cambiado las ubicaciones del almacenamiento de resúmenes a una ubicación de almacenamiento sin protección, como Azure Storage, sin una directiva de inmutabilidad configurada y bloqueada.

1. La comprobación devuelve el mensaje siguiente en la ventana **Resultados**.

   - Si no se ha alterado la base de datos, el mensaje será el siguiente:

       ```output
       Ledger verification successful
       ```

   - Si se ha producido una alteración en la base de datos, aparecerá el siguiente error en la ventana **Mensajes**:
  
       ```output
       Failed to execute query. Error: The hash of block xxxx in the database ledger doesn't match the hash provided in the digest for this block.
       ```

# <a name="t-sql-using-manual-digest-storage"></a>[T-SQL que usa el almacenamiento de resúmenes manual](#tab/t-sql-manual)

1. Conéctese a la base de datos mediante [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).
1. Cree una consulta con la siguiente instrucción T-SQL:

   ```sql
   /****** This will retrieve the latest digest file  ******/
   EXECUTE sp_generate_database_ledger_digest
   ```

1. Ejecutar la consulta. Los resultados contienen el resumen de la base de datos más reciente y representan el hash de la base de datos en el momento actual. Copie el contenido de los resultados para su uso en el siguiente paso.

   :::image type="content" source="media/ledger/ledger-retrieve-digest.png" alt-text="Captura de pantalla que muestra la recuperación de resultados de resumen mediante Azure Data Studio.":::

1. Cree una consulta con la siguiente instrucción T-SQL. Reemplace `<YOUR DATABASE DIGEST>` por el resumen que copió en el paso anterior.

   ```
   /****** Verifies the integrity of the ledger using the referenced digest  ******/
   EXECUTE sp_verify_database_ledger N'
   <YOUR DATABASE DIGEST>
   '
   ```

1. Ejecutar la consulta. La ventana **Mensajes** contiene el siguiente mensaje de finalización correcta.

   :::image type="content" source="media/ledger/ledger-verify-message.png" alt-text="Captura de pantalla que muestra el mensaje después de ejecutar la consulta T-SQL para la comprobación del libro de contabilidad mediante Azure Data Studio.":::

   > [!TIP]
   > La ejecución de la comprobación del libro de contabilidad con el resumen más reciente solo comprobará la base de datos desde el momento en el que se generó el resumen hasta el momento en el que se ejecutó la comprobación. Para comprobar que no se han manipulado los datos históricos de la base de datos, ejecute la comprobación con varios archivos de resumen de base de datos. Comience por el momento dado en el que quiere comprobar la base de datos. Un ejemplo de una comprobación sobre varios resúmenes sería similar a la consulta siguiente.

   ```
   EXECUTE sp_verify_database_ledger N'
   [
       {
           "database_name":  "ledgerdb",
           "block_id":  0,
           "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
           "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
           "digest_time":  "2020-11-12T18:39:27.7385724"
       },
       {
           "database_name":  "ledgerdb",
           "block_id":  1,
           "hash":  "0xE5BE97FDFFA4A16ADF7301C8B2BEBC4BAE5895CD76785D699B815ED2653D9EF8",
           "last_transaction_commit_time":  "2020-11-12T18:39:35.6633333",
           "digest_time":  "2020-11-12T18:43:30.4701575"
       }
   ]
   ```

---

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al libro de contabilidad de Azure SQL Database](ledger-overview.md)
- [Libro de contabilidad de SQL Database](ledger-database-ledger.md)
- [Administración de resúmenes y comprobación de la base de datos](ledger-digest-management-and-database-verification.md)
- [Tablas de solo adición del libro de contabilidad](ledger-append-only-ledger-tables.md)
- [Tablas actualizables del libro de contabilidad](ledger-updatable-ledger-tables.md)
- [Acceso a los resúmenes almacenados en Azure Confidential Ledger](ledger-how-to-access-acl-digest.md)
