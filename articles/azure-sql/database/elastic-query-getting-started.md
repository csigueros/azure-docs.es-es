---
title: Informes de bases de datos escaladas horizontalmente en la nube
description: Uso de consultas de bases de datos entre bases de datos para informes a través de varias bases de datos.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: mathoma
ms.date: 10/10/2019
ms.openlocfilehash: 4e396a616e4bc89e7e68d6553b5e7516eeb04594
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121730500"
---
# <a name="report-across-scaled-out-cloud-databases-preview"></a>Informes de bases de datos escaladas horizontalmente en la nube (versión preliminar)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Puede crear informes de varias bases de datos desde un único punto de conexión mediante una [consulta elástica](elastic-query-overview.md). Las bases de datos deben tener particiones horizontales (también conocidas como "particiones").

Si tiene una base de datos, consulte [Conversión de bases de datos existentes para usar herramientas para bases de datos elásticas](elastic-convert-to-use-elastic-tools.md).

Para comprender los objetos SQL necesarios para realizar consultas, consulte [Informes de bases de datos escaladas horizontalmente en la nube (versión preliminar)](elastic-query-horizontal-partitioning.md).

## <a name="prerequisites"></a>Requisitos previos

Descargue [Introducción al ejemplo de herramientas de Elastic Database](elastic-scale-get-started.md).

## <a name="create-a-shard-map-manager-using-the-sample-app"></a>Creación de un administrador de mapas de particiones con la aplicación de ejemplo
Aquí se creará un administrador de mapas de particiones junto con varias particiones, seguido de la inserción de datos en las particiones. Si resulta que ya dispone de la configuración de particiones con almacenes de datos en ellas, puede omitir los pasos siguientes y pasar a la sección siguiente.

1. Compile y ejecute la aplicación de ejemplo **Introducción a las herramientas de Elastic Database** siguiendo los pasos descritos en la sección del artículo [Descarga y ejecución de la aplicación de ejemplo](elastic-scale-get-started.md#download-and-run-the-sample-app-1). Una vez finalizados todos los pasos, verá el siguiente símbolo del sistema:

    ![símbolo del sistema][1]
2. En la ventana de comandos, escriba "1" y pulse **Entrar**. De esta forma, se creará el administrador de mapas de particiones y se agregarán dos particiones al servidor. A continuación, escriba "3" y pulse **Entrar**; repita la acción cuatro veces. De esta forma, se insertan las filas de datos de ejemplo en sus particiones.
3. [Azure Portal](https://portal.azure.com) debería mostrar tres nuevas bases de datos en el servidor:

   ![Confirmación de Visual Studio][2]

   En este momento, se admiten las consultas entre bases de datos a través de las bibliotecas de cliente de Elastic Database. Por ejemplo, use la opción 4 en la ventana de comandos. Los resultados de una consulta de varias particiones son siempre una **UNIÓN DE TODOS** los resultados de todas las particiones.

   En la siguiente sección, crearemos un extremo de la base de datos de ejemplo que admite las consultas más completas de los datos entre las particiones.

## <a name="create-an-elastic-query-database"></a>Creación una base de datos de consulta elástica

1. Abra [Azure Portal](https://portal.azure.com) e inicie sesión.
2. Cree una nueva base de datos de Azure SQL Database en el mismo servidor que la configuración de la partición. Utilice el nombre "ElasticDBQuery" para la base de datos.

    ![Portal de Azure y nivel de precios][3]

    > [!NOTE]
    > puede usar una base de datos existente. Si puede hacerlo, no debe ser una de las particiones en las que desee ejecutar las consultas. Esta base de datos se usará para crear los objetos de metadatos para una consulta de base de datos elástica.
    >

## <a name="create-database-objects"></a>Creación de objetos de base de datos
### <a name="database-scoped-master-key-and-credentials"></a>Clave maestra y credenciales de ámbito de base de datos
Se usan para conectarse al administrador de mapas de particiones y particiones:

1. Abra SQL Server Management Studio o SQL Server Data Tools en Visual Studio.
2. Conéctese a la base de datos ElasticDBQuery y ejecute los siguientes comandos de T-SQL:

    ```tsql
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '<master_key_password>';

    CREATE DATABASE SCOPED CREDENTIAL ElasticDBQueryCred
    WITH IDENTITY = '<username>',
    SECRET = '<password>';
    ```

    El nombre de usuario y la contraseña deben coincidir con la información de inicio de sesión del paso 3 de la sección [Descarga y ejecución de la aplicación de ejemplo](elastic-scale-get-started.md#download-and-run-the-sample-app) en el artículo **Introducción a las herramientas de base de datos elástica**.

### <a name="external-data-sources"></a>Orígenes de datos externos
Para crear un origen de datos externo, ejecute el siguiente comando en la base de datos ElasticDBQuery:

```tsql
CREATE EXTERNAL DATA SOURCE MyElasticDBQueryDataSrc WITH
    (TYPE = SHARD_MAP_MANAGER,
    LOCATION = '<server_name>.database.windows.net',
    DATABASE_NAME = 'ElasticScaleStarterKit_ShardMapManagerDb',
    CREDENTIAL = ElasticDBQueryCred,
    SHARD_MAP_NAME = 'CustomerIDShardMap'
) ;
```    

 "CustomerIDShardMap" es el nombre del mapa de particiones, si ha creado el administrador de mapa de particiones y el mapa de particiones con el ejemplo de herramientas de base de datos elástica. Sin embargo, si usó la configuración personalizada para este ejemplo, debe ser el nombre de mapa de particiones que eligió en la aplicación.

### <a name="external-tables"></a>Tablas externas
Cree una tabla externa que coincida con la tabla Clientes en las particiones ejecutando el siguiente comando en la base de datos ElasticDBQuery:

```tsql
CREATE EXTERNAL TABLE [dbo].[Customers]
( [CustomerId] [int] NOT NULL,
    [Name] [nvarchar](256) NOT NULL,
    [RegionId] [int] NOT NULL)
WITH
( DATA_SOURCE = MyElasticDBQueryDataSrc,
    DISTRIBUTION = SHARDED([CustomerId])
) ;
```

## <a name="execute-a-sample-elastic-database-t-sql-query"></a>Ejecución de la consulta de T-SQL de la base de datos elástica de ejemplo
Una vez que haya definido el origen de datos externo y las tablas externas, puede usar el T-SQL completo en las tablas externas.

Ejecute esta consulta en la base de datos ElasticDBQuery:

```tsql
select count(CustomerId) from [dbo].[Customers]
```

Observará que la consulta agrega resultados de todas las particiones y proporciona el siguiente resultado:

![Detalles de salida][4]

## <a name="import-elastic-database-query-results-to-excel"></a>Importación de los resultados de la consulta de base de datos elástica a Excel
 Puede importar los resultados de una consulta a un archivo Excel.

1. Inicie Excel 2013.
2. Diríjase a la barra de herramientas **Datos** .
3. Haga clic en **Desde otros orígenes** y luego en **Desde SQL Server**.

   ![Importación de Excel desde otros orígenes][5]
4. En el **Asistente de conexión de datos** , escriba el nombre del servidor y las credenciales de inicio de sesión. A continuación, haga clic en **Siguiente**.
5. En el cuadro de diálogo **Seleccione la base de datos que contiene la información que desea**, seleccione la base de datos **ElasticDBQuery**.
6. Seleccione la tabla **Customers** de la vista de lista y haga clic en **Siguiente**. Haga clic en **Finalizar**.
7. En el formulario **Importar datos**, en **Seleccione cómo desea ver estos datos en el libro**, seleccione **Tabla** y haga clic en **Aceptar**.

Todas las filas de la tabla **Clientes** , almacenadas en distintas particiones, completan la hoja de Excel.

Ahora puede usar las funciones de visualización de datos decisivas de Excel. Puede usar la cadena de conexión con el nombre de servidor, el nombre de base de datos y las credenciales para conectar su BI y las herramientas de integración de datos a la base de datos de consulta elástica. Asegúrese de que SQL Server se admite como origen de datos para la herramienta. Puede consultar la base de datos de consulta elástica y las tablas externas como cualquier otra base de datos SQL Server y las tablas de SQL Server que quiera conectar con la herramienta.

### <a name="cost"></a>Coste
No hay ningún cargo adicional por usar la característica de consulta de Elastic Database.

Para obtener información sobre los precios, consulte [Detalles de precios de SQL Database](https://azure.microsoft.com/pricing/details/sql-database/).

## <a name="next-steps"></a>Pasos siguientes

* Para obtener información general sobre las consultas elásticas, consulte [Información general sobre las consultas elásticas](elastic-query-overview.md).
* Para obtener un tutorial sobre la creación de particiones verticales, consulte [Introducción a las consultas entre bases de datos (particiones verticales)](elastic-query-getting-started-vertical.md).
* Para ver la sintaxis y consultas de ejemplo para los datos con particionamiento vertical, consulte [Consulta de datos particionados verticalmente](elastic-query-vertical-partitioning.md)
* Para ver la sintaxis y consultas de ejemplo para los datos con particionamiento horizontal, consulte [Consulta de datos particionados horizontalmente.](elastic-query-horizontal-partitioning.md)
* Consulte [sp\_execute \_remote](/sql/relational-databases/system-stored-procedures/sp-execute-remote-azure-sql-database) para ver un procedimiento almacenado que ejecuta una instrucción de Transact-SQL en una sola instancia remota de Azure SQL Database o un conjunto de bases de datos que actúan como particiones en un esquema de particiones horizontales.


<!--Image references-->
[1]: ./media/elastic-query-getting-started/cmd-prompt.png
[2]: ./media/elastic-query-getting-started/portal.png
[3]: ./media/elastic-query-getting-started/tiers.png
[4]: ./media/elastic-query-getting-started/details.png
[5]: ./media/elastic-query-getting-started/exel-sources.png
<!--anchors-->