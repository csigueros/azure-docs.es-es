---
title: Administración de resúmenes y comprobación de la base de datos
description: En este artículo se proporciona información sobre la administración de resúmenes y la verificación de la base de datos de una base de datos de libro de contabilidad en Azure SQL Database.
ms.custom: ''
ms.date: 05/25/2021
ms.service: sql-database
ms.subservice: security
ms.reviewer: vanto
ms.topic: conceptual
author: JasonMAnderson
ms.author: janders
ms.openlocfilehash: e133ee1c8492bf63cbfd4702e795743009abfdc7
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2021
ms.locfileid: "112080100"
---
# <a name="digest-management-and-database-verification"></a>Administración de resúmenes y comprobación de la base de datos

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

> [!NOTE]
> Actualmente, el libro de contabilidad de Azure SQL Database está en versión preliminar pública y está disponible en el Centro-oeste de EE. UU.

El libro de contabilidad de Azure SQL Database proporciona un formulario de integridad de datos denominado *integridad hacia delante*, que proporciona pruebas de alteración de los datos de las tablas de su libro de contabilidad. Por ejemplo, si se produce una transacción bancaria en una tabla del libro de contabilidad en la que un saldo se ha actualizado al valor `x` y un atacante posteriormente modifica los datos cambiando el saldo de `x` a `y`, la verificación de la base de datos detectará esta actividad de alteración.  

El proceso de verificación de la base de datos toma como entrada uno o más resúmenes de base de datos generados previamente. Luego, vuelve a calcular los valores hash almacenados en el libro de contabilidad de la base de datos en función del estado actual de las tablas del libro de contabilidad. Si los hashes calculados no coinciden con los resúmenes de entrada, se produce un error en la verificación. El error indica que los datos se han alterado. El proceso de verificación notifica todas las incoherencias que detecta.

## <a name="database-digests"></a>Resúmenes de base de datos

El hash del bloque más reciente del libro de contabilidad de base de datos se denomina *resumen de la base de datos*. Representa el estado de todas las tablas del libro de contabilidad de la base de datos en el momento en que se generó el bloque. Generar un resumen de base de datos es eficaz, ya que solo implica calcular los hash de los bloques que se anexaron recientemente. 

El sistema puede generar los resúmenes de la base de datos automáticamente o bien el usuario puede hacerlo manualmente. Puede usarlos más adelante para verificar la integridad de la base de datos. 

Los resúmenes de la base de datos se generan como documento JSON que contiene el hash del bloque más reciente, junto con los metadatos del identificador de bloque. Los metadatos presentan el momento en que se generó el resumen y la marca de tiempo de confirmación de la última transacción de este bloque.

El proceso de verificación y la integridad de la base de datos dependen de la integridad de los resúmenes de entrada. Para ello, los resúmenes de base de datos que se extraen de la base de datos deben residir en almacenamientos de confianza que los usuarios con muchos privilegios o los atacantes del servidor de Azure SQL Database no puedan manipular.

### <a name="automatic-generation-and-storage-of-database-digests"></a>Generación automática y almacenamiento de resúmenes de base de datos

El libro de contabilidad de Azure SQL Database se integra con la [característica de almacenamiento inmutable de Azure Blob Storage](../../storage/blobs/storage-blob-immutable-storage.md) y [Azure Confidential Ledger](../../confidential-ledger/index.yml). Esta integración proporciona servicios de almacenamiento seguros en Azure para ayudar a proteger los resúmenes de la base de datos frente a posibles alteraciones. Esta integración proporciona una manera sencilla y rentable para que los usuarios automaticen la administración de los resúmenes sin tener que preocuparse por su disponibilidad y replicación geográfica. 

Puede configurar la generación y el almacenamiento automáticos de resúmenes de bases de datos a través de Azure Portal, PowerShell o la CLI de Azure. Al configurar la generación y el almacenamiento automáticos, los resúmenes de base de datos se generan en un intervalo predefinido de 30 segundos y se cargan en el servicio de almacenamiento seleccionado. Si no se producen transacciones en el sistema en el intervalo de 30 segundos, no se generará ni cargará ningún resumen de la base de datos. Este mecanismo garantiza que los resúmenes de la base de datos solo se generan cuando los datos se han actualizado en la base de datos.

:::image type="content" source="media/ledger/automatic-digest-management.png" alt-text="Captura de pantalla que muestra las selecciones para habilitar el almacenamiento de resumen."::: 

> [!IMPORTANT]
> Configure una [directiva de inmutabilidad](../../storage/blobs/storage-blob-immutability-policies-manage.md) en el contenedor después del aprovisionamiento para asegurarse de que los resúmenes de la base de datos están protegidos frente a alteraciones.

### <a name="manual-generation-and-storage-of-database-digests"></a>Generación y almacenamiento manual de resúmenes de base de datos

También puede usar el libro de contabilidad de Azure SQL Database para generar un resumen de la base de datos bajo demanda, de modo que pueda almacenarlo manualmente en cualquier servicio o dispositivo que considere un destino de almacenamiento de confianza. Por ejemplo, puede elegir un dispositivo write once, read many (WORM) como destino. Para generar manualmente un resumen de base de datos, ejecute el procedimiento almacenado [sys.sp_generate_database_ledger_digest](/sql/relational-databases/system-stored-procedures/sys-sp-generate-database-ledger-digest-transact-sql) en [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) o [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio).

> [!IMPORTANT]
> La generación de resúmenes de base de datos requiere el permiso **GENERATE LEDGER DIGEST**. Para obtener más información sobre los permisos relacionados con las tablas de libro de contabilidad, consulte [Permisos](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

```sql
EXECUTE sp_generate_database_ledger_digest
```

El conjunto de resultados devuelto es una sola fila de datos. Debe guardarse en la ubicación de almacenamiento de confianza como un documento JSON de la siguiente manera:

```json
    {
        "database_name":  "ledgerdb",
        "block_id":  0,
        "hash":  "0xDC160697D823C51377F97020796486A59047EBDBF77C3E8F94EEE0FFF7B38A6A",
        "last_transaction_commit_time":  "2020-11-12T18:01:56.6200000",
        "digest_time":  "2020-11-12T18:39:27.7385724"
    }
```

## <a name="database-verification"></a>Comprobación de la base de datos

El proceso de verificación examina todas las tablas del libro de contabilidad e historial. Vuelve a compilar los hash SHA-256 de sus filas y los compara con los archivos de resumen de la base de datos pasados al procedimiento almacenado de verificación. 

Para las tablas de gran tamaño del libro de contabilidad, la verificación de la base de datos puede ser un proceso que consume muchos recursos. Debe usarla solo cuando necesite verificar la integridad de una base de datos. 

El proceso de verificación se puede ejecutar cada hora o diariamente para los casos en los que es necesario supervisar con frecuencia la integridad de la base de datos. O bien solo se puede ejecutar cuando la organización que hospeda los datos supera una auditoría y necesita proporcionar evidencia criptográfica sobre la integridad de los datos. Para reducir el costo de la verificación, el libro de contabilidad expone las opciones para verificar las tablas del libro de contabilidad individuales o solo un subconjunto de las tablas del libro de contabilidad. 

Para realizar la verificación de la base de datos, se utilizan dos procedimientos almacenados, en función de si [usa el almacenamiento automático de resúmenes](#database-verification-that-uses-automatic-digest-storage) o [administra manualmente los resúmenes](#database-verification-that-uses-manual-digest-storage).

> [!IMPORTANT]
> La verificación de la base de datos requiere el permiso *View Ledger Content* (Ver contenido del libro de contabilidad). Para más información sobre los permisos relacionados con las tablas de libro de contabilidad, consulte [Permisos](/sql/relational-databases/security/permissions-database-engine#asdbpermissions). 

### <a name="database-verification-that-uses-automatic-digest-storage"></a>Verificación de la base de datos mediante almacenamiento de resumen automático

Cuando usa el almacenamiento de resumen automático para generar y almacenar resúmenes de bases de datos, la ubicación del almacenamiento de resumen se encuentra en la vista de catálogo del sistema [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) como objetos JSON. La ejecución de la verificación de la base de datos consiste en ejecutar el procedimiento almacenado del sistema [sp_verify_database_ledger_from_digest_storage](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-from-digest-storage-transact-sql). Especifique los objetos JSON de la vista del catálogo del sistema [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql) en la que se configuran los resúmenes de las bases de datos para su almacenamiento. 

Cuando usa el almacenamiento de resumen automático, puede cambiar las ubicaciones de almacenamiento a lo largo del ciclo de vida de las tablas del libro de contabilidad.  Por ejemplo, si empieza por usar el almacenamiento inmutable de Azure para almacenar sus archivos de resumen, pero más adelante quiere usar Azure Confidential Ledger en su lugar, puede hacerlo. Este cambio de ubicación se almacena en [sys.database_ledger_digest_locations](/sql/relational-databases/system-catalog-views/sys-database-ledger-digest-locations-transact-sql). 

Para simplificar la ejecución de la verificación cuando se usan varias ubicaciones de almacenamiento de resumen, el siguiente script capturará las ubicaciones de los resúmenes y ejecutará la verificación con esas ubicaciones.

```sql
DECLARE @digest_locations NVARCHAR(MAX) = (SELECT * FROM sys.database_ledger_digest_locations FOR JSON AUTO, INCLUDE_NULL_VALUES);
SELECT @digest_locations as digest_locations;
BEGIN TRY
    EXEC sys.sp_verify_database_ledger_from_digest_storage @digest_locations;
    SELECT 'Ledger verification succeeded.' AS Result;
END TRY
BEGIN CATCH
    THROW;
END CATCH
```

### <a name="database-verification-that-uses-manual-digest-storage"></a>Verificación de la base de datos mediante almacenamiento de resumen manual

Cuando utiliza el almacenamiento de resumen manual para generar y almacenar resúmenes de base de datos, se usa el siguiente procedimiento almacenado para verificar la base de datos del libro de contabilidad. El contenido JSON del resumen se anexa en el procedimiento almacenado. Al ejecutar la verificación de la base de datos, puede elegir verificar todas las tablas de la base de datos o tablas específicas. 

A continuación, se muestra la sintaxis del procedimiento almacenado [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql):

```sql
sp_verify_database_ledger <JSON_document_containing_digests>, <table_name> 
```

El código siguiente es un ejemplo de ejecución del procedimiento almacenado [sp_verify_database_ledger](/sql/relational-databases/system-stored-procedures/sys-sp-verify-database-ledger-transact-sql) pasando dos resúmenes para la verificación: 

```sql
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

Los códigos de devolución para `sp_verify_database_ledger` y `sp_verify_database_ledger_from_digest_storage` son `0` (correcto) o `1` (error).

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al libro de contabilidad de Azure SQL Database](ledger-overview.md)
- [Tablas de libro de contabilidad actualizables](ledger-updatable-ledger-tables.md)   
- [Tablas de libro de contabilidad de solo anexión](ledger-append-only-ledger-tables.md)   
- [Libro de contabilidad de base de datos](ledger-database-ledger.md)