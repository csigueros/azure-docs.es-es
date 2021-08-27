---
title: Migración de datos existentes a una cuenta de Table API en Azure Cosmos DB
description: Aprenda a migrar o importar datos locales o en la nube a una cuenta de Table API de Azure en Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 12/07/2017
ms.author: sngun
ms.custom: seodec18
ms.openlocfilehash: fa40f5d77fc9aa993f510510eb6396e011bbf3fb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121786447"
---
# <a name="migrate-your-data-to-an-azure-cosmos-db-table-api-account"></a>Migración de los datos a una cuenta de Table API de Azure Cosmos DB
[!INCLUDE[appliesto-table-api](../includes/appliesto-table-api.md)]

En este tutorial se proporcionan instrucciones sobre cómo importar datos para su uso con [Table API](introduction.md) de Azure Cosmos DB. Si tiene datos almacenados en Azure Table Storage, puede usar la Herramienta de migración de datos o AzCopy para importar los datos en Table API de Azure Cosmos DB. 

En este tutorial se describen las tareas siguientes:

> [!div class="checklist"]
> * Importación de datos con la herramienta de migración de datos
> * Importación de datos con AzCopy

## <a name="prerequisites"></a>Requisitos previos

* **Aumento del rendimiento:** la duración de la migración de datos depende de la cantidad de rendimiento configurado para un solo contenedor o un conjunto de contenedores. Asegúrese de aumentar el rendimiento para migraciones de datos más grandes. Después de haber completado la migración, reduzca el rendimiento para ahorrar costos.

* **Cree recursos de Azure Cosmos DB**: antes de comenzar la migración de datos, cree todas las tablas desde Azure Portal. Si va a migrar a una cuenta de Azure Cosmos DB con rendimiento de nivel de base de datos, asegúrese de proporcionar una clave de partición al crear las tablas de Azure Cosmos DB.

## <a name="data-migration-tool"></a>Herramienta de migración de datos

Puede usar la herramienta de migración de datos de la línea de comandos (dt.exe) en Azure Cosmos DB para importar los datos existentes de Azure Table Storage en una cuenta de Table API. 

Para migrar datos de tabla:

1. Descargue la herramienta de migración de [GitHub](https://github.com/azure/azure-documentdb-datamigrationtool).
2. Ejecute `dt.exe` con los argumentos de la línea de comandos para su escenario. `dt.exe` toma un comando con el formato siguiente:

   ```bash
    dt.exe [/<option>:<value>] /s:<source-name> [/s.<source-option>:<value>] /t:<target-name> [/t.<target-option>:<value>] 
   ```

Las opciones admitidas para este comando son:

* **/ErrorLog:** Opcional. Nombre del archivo CSV al que se redirigirán los errores de transferencia de datos.
* **/OverwriteErrorLog:** Opcional. Sobrescribe el archivo de registro de errores.
* **/ProgressUpdateInterval:** opcional, el valor predeterminado es `00:00:01`. Tiempo necesario para actualizar el progreso de la transferencia de datos en pantalla.
* **/ErrorDetails:** opcional, el valor predeterminado es `None`. Especifica que se debe mostrar información de error detallada para los siguientes errores: `None`, `Critical` o `All`.
* **/EnableCosmosTableLog:** Opcional. Dirija el registro a una cuenta de Table de Azure Cosmos DB. Si está establecido, el valor predeterminado es la cadena de conexión de la cuenta de destino, a menos que también se proporcione `/CosmosTableLogConnectionString`. Esto resulta útil si se ejecutan varias instancias de la herramienta simultáneamente.
* **/CosmosTableLogConnectionString:** Opcional. La cadena de conexión para dirigir el registro a una cuenta de Table de Azure Cosmos DB remota.

### <a name="command-line-source-settings"></a>Configuración del origen de la línea de comandos

Utilice las siguientes opciones de origen al definir Azure Table Storage como origen de la migración.

* **/s:AzureTable:** lee los datos de Azure Table Storage.
* **/s.ConnectionString:** Cadena de conexión para el punto de conexión de tabla. Puede recuperarlo de Azure Portal.
* **/s.LocationMode:** opcional, el valor predeterminado es `PrimaryOnly`. Especifica el modo de ubicación que se va a usar al conectarse a Table Storage: `PrimaryOnly`, `PrimaryThenSecondary`, `SecondaryOnly` o `SecondaryThenPrimary`.
* **/s.Table:** nombre de la tabla de Azure.
* **/s.InternalFields:** se establece en `All` para la migración de tablas, ya que `RowKey` y `PartitionKey` son campos necesarios para la importación.
* **/s.Filter:** Opcional. Cadena de filtro que se va a aplicar.
* **/s.Projection:** Opcional. Lista de columnas que se va a seleccionar.

Para recuperar la cadena de conexión de origen al importar desde Table Storage, abra Azure Portal. Seleccione **Cuentas de almacenamiento** > **Cuenta** > **Claves de acceso** y copie la **cadena de conexión**.

:::image type="content" source="./media/table-import/storage-table-access-key.png" alt-text="Captura de pantalla que muestra las opciones Cuentas de almacenamiento > Cuenta > Claves de acceso y resalta el botón Copiar.":::

### <a name="command-line-target-settings"></a>Configuración del destino de la línea de comandos

Utilice las siguientes opciones de destino al definir Table API de Azure Cosmos DB como destino de la migración.

* **/t: TableAPIBulk:** carga los datos por lotes en Table API de Azure Cosmos DB.
* **/t.ConnectionString:** cadena de conexión para el punto de conexión de tabla.
* **/t.TableName:** especifica el nombre de la tabla en la que se va a escribir.
* **/t.Overwrite:** opcional, el valor predeterminado es `false`. Especifica si se deben sobrescribir los valores existentes.
* **/t.MaxInputBufferSize:** opcional, el valor predeterminado es `1GB`. Estimación aproximada de bytes de entrada que se almacenarán en búfer antes de vaciar los datos en el receptor.
* **/t.Throughput:** Opcional, los valores predeterminados de servicio si no se especifican. Especifica el rendimiento que se va a configurar para la tabla.
* **/t.MaxBatchSize:** opcional, el valor predeterminado es `2MB`. Especifica el tamaño del lote en bytes.

### <a name="sample-command-source-is-table-storage"></a>Comando de ejemplo: el origen es Table Storage.

A continuación se muestra un ejemplo de línea de comandos para importar desde Table Storage a Table API:

```bash
dt /s:AzureTable /s.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Table storage account name>;AccountKey=<Account Key>;EndpointSuffix=core.windows.net /s.Table:<Table name> /t:TableAPIBulk /t.ConnectionString:DefaultEndpointsProtocol=https;AccountName=<Azure Cosmos DB account name>;AccountKey=<Azure Cosmos DB account key>;TableEndpoint=https://<Account name>.table.cosmosdb.azure.com:443 /t.TableName:<Table name> /t.Overwrite
```

## <a name="migrate-data-by-using-azcopy"></a>Migración de datos mediante AzCopy

También puede usar la utilidad de la línea de comandos AzCopy para migrar los datos de Table Storage a Table API de Azure Cosmos DB. Para usar AzCopy, primero se exportan los datos como se describe en [Exportación de datos desde Table Storage](/previous-versions/azure/storage/storage-use-azcopy#export-data-from-table-storage). Después, se importan los datos en Azure Cosmos DB como se describe en [Table API de Azure Cosmos DB](/previous-versions/azure/storage/storage-use-azcopy#import-data-into-table-storage).

Consulte el siguiente ejemplo cuando vaya a importar en Azure Cosmos DB. Recuerde que el valor `/Dest` usa `cosmosdb` y no `core`.

Comando de importación de ejemplo:

```bash
AzCopy /Source:C:\myfolder\ /Dest:https://myaccount.table.cosmosdb.windows.net/mytable1/ /DestKey:key /Manifest:"myaccount_mytable_20140103T112020.manifest" /EntityOperation:InsertOrReplace
```

## <a name="next-steps"></a>Pasos siguientes

Aprenda a consultar datos con Table API de Azure Cosmos DB. 

> [!div class="nextstepaction"]
>[Consulta de datos](tutorial-query-table.md)




