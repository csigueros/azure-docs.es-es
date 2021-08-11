---
title: 'Actualización mediante volcado y restauración: Azure Database for PostgreSQL'
description: Se describen los métodos de actualización sin conexión que usan bases de datos de volcado y restauración para la migración a una versión superior de Azure Database for PostgreSQL.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2021
ms.openlocfilehash: d528d75bd26bf17ca0da20447848d315e2dc9057
ms.sourcegitcommit: c385af80989f6555ef3dadc17117a78764f83963
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/04/2021
ms.locfileid: "111406884"
---
# <a name="upgrade-your-postgresql-database-using-dump-and-restore"></a>Actualización de una base de datos de PostgreSQL mediante volcado y restauración

>[!NOTE]
> Los conceptos que se explican en esta documentación son aplicables a Azure Database for PostgreSQL: servidor único y a Azure Database for PostgreSQL: servidor flexible (versión preliminar). 

Puede actualizar el servidor de PostgreSQL implementado en Azure Database for PostgreSQL mediante la migración de las bases de datos a una versión principal superior del servidor mediante los métodos siguientes.
* Método **sin conexión** mediante [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) y [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) de PostgreSQL que implica tiempo de inactividad para migrar los datos. En este documento se aborda este método de actualización/migración.
* Método **en línea** que usa [Database Migration Service](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) (DMS). Este método ofrece una migración con tiempo de inactividad reducido y mantiene sincronizada la base de datos de destino con el origen. Además, permite elegir el momento del traslado. No obstante, existen algunos requisitos previos y restricciones que se deben abordar para usar DMS. Para más información, consulte la [documentación de DMS](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md). 

 En la tabla siguiente se proporcionan algunas recomendaciones según el escenario y el tamaño de la base de datos.

| **Base de datos/escenario** | **Volcado y restauración (sin conexión)** | **DMS (en línea)** |
| ------ | :------: | :-----: |
| Tiene una base de datos pequeña y puede permitirse un tiempo de inactividad para realizar la actualización.  | X | |
| Bases de datos pequeñas (< 10 GB).  | X | X | 
| Bases de datos pequeñas a medianas (10 GB a 100 GB). | X | X |
| Bases de datos de gran tamaño (> 100 GB). |  | X |
| Puede permitirse un tiempo de inactividad para la actualización (independientemente del tamaño de la base de datos). | X |  |
| ¿Se puede encargar de los [requisitos previos](../dms/tutorial-azure-postgresql-to-azure-postgresql-online-portal.md#prerequisites) de DMS, incluido el reinicio? |  | X |
| ¿Se puede evitar el uso de DDL y las tablas no registradas durante el proceso de actualización? | |  X |

En esta guía se proporcionan algunas metodologías de migración sin conexión y ejemplos para mostrar cómo se puede migrar desde el servidor de origen al servidor de destino con una versión posterior de PostgreSQL.

> [!NOTE]
> El volcado y restauración de PostgreSQL puede realizarse de muchas maneras. Puede optar por migrar mediante uno de los métodos proporcionados en esta guía o elegir otras formas que se adapten a sus necesidades. Para obtener información detallada sobre la sintaxis de volcado y restauración con parámetros adicionales, consulte los artículos sobre [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) y [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html). 


## <a name="prerequisites-for-using-dump-and-restore-with-azure-database-for-postgresql"></a>Requisitos previos para el uso del volcado y la restauración con Azure Database for PostgreSQL
 
Para seguir esta guía, necesitará lo siguiente:

- Un servidor de bases de datos PostgreSQL de **origen** que ejecuta una versión inferior del motor que quiere actualizar.
- Un servidor de bases de datos PostgreSQL de **destino** con la versión principal deseada del [servidor de Azure Database for PostgreSQL: servidor único](quickstart-create-server-database-portal.md) o [Azure Database for PostgreSQL: servidor flexible](./flexible-server/quickstart-create-server-portal.md). 
- Un sistema cliente de PostgreSQL para ejecutar los comandos de volcado y restauración.
  - Puede ser un cliente Linux o Windows con PostgreSQL instalado y con las utilidades de línea de comandos [pg_dump](https://www.postgresql.org/docs/current/static/app-pgdump.html) y [pg_restore](https://www.postgresql.org/docs/current/static/app-pgrestore.html) instaladas. 
  - También puede usar [Azure Cloud Shell](https://shell.azure.com) o hacer clic en la opción Azure Cloud Shell de la barra de menús de la parte superior derecha de [Azure Portal](https://portal.azure.com). Tendrá que iniciar sesión en su cuenta con `az login` antes de ejecutar los comandos de volcado y restauración.
- El cliente de PostgreSQL preferentemente se ejecutará en la misma región que los servidores de origen y de destino. 


## <a name="additional-details-and-considerations"></a>Detalles y consideraciones adicionales
- Para encontrar la cadena de conexión a las bases de datos de origen y de destino, haga clic en "Cadenas de conexión" en el portal. 
- Es posible que se esté ejecutando más de una base de datos en el servidor. Para encontrar la lista de bases de datos, conéctese al servidor de origen y ejecute el comando `\l`.
- Cree las bases de datos correspondientes en el servidor de base de datos de destino.
- Puede omitir la actualización de `azure_maintenance` o de las bases de datos de plantilla.
- Consulte las tablas anteriores para determinar si la base de datos es adecuada para este modo de migración.
- Si quiere usar Azure Cloud Shell, recuerde que el tiempo de espera de la sesión se agota después de 20 minutos. Si el tamaño de la base de datos es menor que 10 GB, podrá completar la actualización sin que se agote el tiempo de espera de la sesión. De lo contrario, quizá tenga que mantener la sesión abierta por otros medios, como presionar la tecla <Enter> una vez cada 10 a 15 minutos. 


## <a name="example-database-used-in-this-guide"></a>Base de datos de ejemplo usada en esta guía

En esta guía se usan los siguientes servidores de origen y de destino, y esto se ilustra con ejemplos de nombres de base de datos.

 | **Descripción** | **Valor** |
 | ------- | ------- |
 | Servidor de origen (versión 9.5) | pg-95.postgres.database.azure.com |
 | Base de datos de origen | bench5gb |
 | Tamaño de la base de datos de origen | 5 GB |
 | Nombre de usuario del origen | pg@pg-95 |
 | Servidor de destino (versión 11) | pg-11.postgres.database.azure.com |
 | Base de datos de destino | bench5gb |
 | Nombre de usuario de destino | pg@pg-11 |

>[!NOTE]
> El servidor flexible admite PostgreSQL versión 11 en adelante. Además, el nombre de usuario del servidor flexible no requiere @<servername>.

## <a name="upgrade-your-databases-using-offline-migration-methods"></a>Actualización de las bases de datos mediante métodos de migración sin conexión
Puede optar por utilizar uno de los métodos descritos en esta sección para las actualizaciones. Puede usar las siguientes sugerencias mientras realiza las tareas.

- Si usa la misma contraseña para la base de datos de origen y destino, puede configurar la variable de entorno `PGPASSWORD=yourPassword`.  De este modo no será necesario especificar la contraseña cada vez que se ejecuten comandos como psql, pg_dump y pg_restore.  Del mismo modo, puede configurar variables adicionales como `PGUSER`, `PGSSLMODE`, entre otras. Consulte las [variables de entorno de PostgreSQL](https://www.postgresql.org/docs/11/libpq-envars.html).
  
- Si el servidor de PostgreSQL requiere conexiones TLS/SSL (que lo sucede de forma predeterminada en los servidores de Azure Database for PostgreSQL), establezca una variable de entorno `PGSSLMODE=require` para que la herramienta pg_restore se conecte con TLS. Sin TSL, el error podría ser `FATAL:  SSL connection is required. Please specify SSL options and retry.`

- En la línea de comandos de Windows, ejecute el comando `SET PGSSLMODE=require` antes de ejecutar el comando pg_restore. En Linux o Bash, ejecute el comando `export PGSSLMODE=require` antes de ejecutar el comando pg_restore.

### <a name="method-1-migrate-using-dump-file"></a>Método 1: Migración mediante un archivo de volcado

Este método consta de dos pasos. En primer lugar, se realiza un volcado desde el servidor de origen. El segundo paso consiste en restaurar el archivo de volcado en el servidor de destino. Para más información, consulte la documentación sobre la [Migración mediante el volcado y la restauración](howto-migrate-using-dump-and-restore.md). Este es el método recomendado si tiene bases de datos grandes y el sistema cliente tiene suficiente espacio de almacenamiento para almacenar el archivo de volcado.

### <a name="method-2-migrate-using-streaming-the-dump-data-to-the-target-database"></a>Método 2: Migración mediante transmisión de los datos de volcado a la base de datos de destino

Si no tiene un cliente de PostgreSQL o si desea usar Azure Cloud Shell, puede usar este método. El volcado de la base de datos se transmite directamente al servidor de bases de datos de destino y no se almacena el volcado en el cliente. Por lo tanto, se puede usar con un cliente que tenga almacenamiento limitado, e incluso se puede ejecutar desde Azure Cloud Shell. 

1. Asegúrese de que la base de datos existe en el servidor de destino mediante el comando `\l`. Si la base de datos no existe, créela.
   ```azurecli-interactive
    psql "host=myTargetServer port=5432 dbname=postgres user=myUser password=###### sslmode=mySSLmode"
    ```
    ```SQL
    postgres> \l   
    postgres> create database myTargetDB;
   ```

2. Ejecute el volcado y la restauración como una sola línea de comandos mediante una canalización. 
    ```azurecli-interactive
    pg_dump -Fc -v --mySourceServer --port=5432 --username=myUser --dbname=mySourceDB | pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB
    ```

    Por ejemplo,

    ```azurecli-interactive
    pg_dump -Fc -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb | pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb
    ```  
3. Una vez completado el proceso de actualización (migración), puede probar la aplicación con el servidor de destino. 
4. Repita el mismo proceso para todas las bases de datos del servidor.

 Como ejemplo, en la tabla siguiente se muestra el tiempo que se ha tardado en migrar mediante el método de transmisión del volcado. Los datos de ejemplo se rellenan mediante [pgbench](https://www.postgresql.org/docs/10/pgbench.html). Como la base de datos puede tener un número diferente de objetos con tamaños variados en comparación con las tablas e índices generados por pgbench, se recomienda que pruebe el volcado y la restauración de la base de datos para comprender el tiempo real que se tarda en actualizar la base de datos. 

| **Tamaño de la base de datos** | **Tiempo invertido aproximado** | 
| ----- | ------ |
| 1 GB  | 1 a 2 minutos |
| 5 GB | 8 a 10 minutos |
| 10 GB | 15-20 minutos |
| 50 GB | 1 a 1,5 horas |
| 100 GB | 2,5 a 3 horas|
   
### <a name="method-3-migrate-using-parallel-dump-and-restore"></a>Método 3: Migración mediante volcado y restauración paralelos 

Puede considerar este método si tiene pocas tablas de mayor tamaño en la base de datos y quiere paralelizar el proceso de volcado y restauración de esa base de datos. También necesitará almacenamiento suficiente en el sistema cliente para dar cabida a los volcados de copia de seguridad. Este proceso de volcado y restauración paralelo reduce el consumo de tiempo para completar toda la migración. Por ejemplo, la base de datos de pgbench de 50 GB que tardó 1 a 1,5 horas en migrar se completó con el método 1 y 2; con este método tardó menos de 30 minutos.

1. Para cada base de datos del servidor de origen, cree una base de datos correspondiente en el servidor de destino.

   ```bash
    psql "host=myTargetServer port=5432 dbname=postgres user=myuser password=###### sslmode=mySSLmode"
    postgresl> create database myDB;
   ```
   Por ejemplo,
    ```bash
    psql "host=pg-11.postgres.database.azure.com port=5432 dbname=postgres user=pg@pg-11 password=###### sslmode=require"

    postgres> create database bench5gb;
    postgres> \q
    ```

2. Ejecute el comando pg_dump en un formato de directorio con un número de trabajos igual que 4 (número de tablas en la base de datos). Con un nivel de proceso mayor y con más tablas, puede aumentarlo a un número mayor. Dicho comando pg_dump creará un directorio para almacenar los archivos comprimidos de cada trabajo.

    ```bash
    pg_dump -Fd -v --host=sourceServer --port=5432 --username=myUser --dbname=mySourceDB -j 4 -f myDumpDirectory
    ```
    Por ejemplo,
    ```bash
    pg_dump -Fd -v --host=pg-95.postgres.database.azure.com --port=5432 --username=pg@pg-95 --dbname=bench5gb -j 4 -f dump.dir
    ```

3. A continuación, restaure la copia de seguridad en el servidor de destino.
    ```bash
    $ pg_restore -v --no-owner --host=myTargetServer --port=5432 --username=myUser --dbname=myTargetDB -j 4 myDumpDir
    ```
    Por ejemplo,
    ```bash
    $ pg_restore -v --no-owner --host=pg-11.postgres.database.azure.com --port=5432 --username=pg@pg-11 --dbname=bench5gb -j 4 dump.dir
    ```

> [!TIP]
> El proceso mencionado en este documento también se puede usar para actualizar el servidor flexible de Azure Database for PostgreSQL, que se encuentra en versión preliminar. La diferencia principal es que la cadena de conexión para el destino del servidor flexible no tiene el parámetro `@dbName`.  Por ejemplo, si el nombre de usuario es `pg`, el nombre de usuario del servidor único en la cadena de conexión será `pg@pg-95`, mientras que con un servidor flexible, puede simplemente usar `pg`.

## <a name="next-steps"></a>Pasos siguientes

- Una vez que esté satisfecho con la función de base de datos de destino, puede quitar el servidor de bases de datos anterior. 
- En el caso de Azure Database for PostgreSQL: servidor único solo. Si quiere usar el mismo punto de conexión de base de datos que el servidor de origen, puede crear una réplica de lectura con el nombre de servidor de base de datos anterior después de eliminar el servidor de base de datos de origen anterior. Una vez alcanzado el estado de replicación estable, puede detener la réplica, lo que promoverá el servidor de réplicas para que sea un servidor independiente. Consulte [Replicación](./concepts-read-replicas.md) para obtener más detalles.
- No olvide probar y validar estos comandos en un entorno de prueba antes de usarlos en producción.