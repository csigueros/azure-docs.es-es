---
title: 'Tutorial: Migración de PostgreSQL a Azure Database for PostgreSQL en línea a través de la CLI de Azure'
titleSuffix: Azure Database Migration Service
description: Aprenda a realizar una migración en línea de PostgreSQL local a Azure Database for PostgreSQL mediante Azure Database Migration Service a través de la CLI.
services: dms
author: arunkumarthiags
ms.author: arthiaga
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019, devx-track-azurecli
ms.topic: tutorial
ms.date: 04/11/2020
ms.openlocfilehash: 52d5c7a500652b0090cf9b21400a9c45f2bf54e7
ms.sourcegitcommit: b044915306a6275c2211f143aa2daf9299d0c574
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113033738"
---
# <a name="tutorial-migrate-postgresql-to-azure-db-for-postgresql-online-using-dms-via-the-azure-cli"></a>Tutorial: Migración de PostgreSQL a Azure DB for PostgreSQL en línea mediante DMS a través de la CLI de Azure

Puede usar Azure Database Migration Service para migrar las bases de datos de una instancia de PostgreSQL local a [Azure Database for PostgreSQL](../postgresql/index.yml) con un tiempo de inactividad mínimo. En otras palabras, la migración se puede completar con un tiempo de inactividad mínimo para la aplicación. En este tutorial, va a migrar la base de datos de ejemplo **DVD Rental** de una instancia local de PostgreSQL 9.6 a Azure Database for PostgreSQL mediante la actividad de migración en línea de Azure Database Migration Service.

En este tutorial, aprenderá a:
> [!div class="checklist"]
>
> * Migre el esquema de ejemplo mediante la utilidad pg_dump.
> * Crear una instancia de Azure Database Migration Service.
> * Crear un proyecto de migración mediante Azure Database Migration Service.
> * Ejecutar la migración.
> * Supervisar la migración

> [!NOTE]
> El uso de Azure Database Migration Service para realizar una migración en línea requiere la creación de una instancia basada en el plan de tarifa Premium. El disco se cifra para impedir el robo de datos durante el proceso de migración.

> [!IMPORTANT]
> Para disfrutar de una experiencia de migración óptima, Microsoft recomienda crear una instancia de Azure Database Migration Service en la misma región de Azure que la base de datos de destino. Si los datos se transfieren entre diferentes regiones o ubicaciones geográficas, el proceso de migración puede verse afectado y pueden producirse errores.

## <a name="prerequisites"></a>Prerrequisitos

Para completar este tutorial, necesita:

* Descargue e instale [PostgreSQL community edition](https://www.postgresql.org/download/) 9.5, 9.6 o 10. La versión del servidor PostgreSQL Server de origen debe ser la 9.5.11, 9.6.7, 10 o una posterior. Para más información, consulte el artículo acerca de las [versiones de base de datos admitidas de PostgreSQL](../postgresql/concepts-supported-versions.md).

    Tenga en cuenta también que la versión de Azure Database for PostgreSQL de destino debe ser igual o posterior a la versión local de PostgreSQL. Por ejemplo, PostgreSQL 9.6 solo puede migrarse a Azure Database for PostgreSQL 9.6, 10 u 11, pero no a Azure Database for PostgreSQL 9.5.

* [Cree una instancia de Azure Database for PostgreSQL](../postgresql/quickstart-create-server-database-portal.md) o [cree una instancia de Azure Database for PostgreSQL: servidor de Hiperescala (Citus)](../postgresql/quickstart-create-hyperscale-portal.md).
* Cree una instancia de Azure Virtual Network para Azure Database Migration Service mediante el modelo de implementación de Azure Resource Manager, que proporciona conectividad de sitio a sitio a los servidores de origen local mediante [ExpressRoute](../expressroute/expressroute-introduction.md) o [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para más información sobre la creación de una red virtual, consulte la documentación de [Virtual Network](../virtual-network/index.yml)y, especialmente, los artículos de inicio rápido con detalles paso a paso.

    > [!NOTE]
    > Durante la configuración de la red virtual, si usa ExpressRoute con emparejamiento de red a Microsoft, agregue los siguientes [puntos de conexión](../virtual-network/virtual-network-service-endpoints-overview.md) de servicio a la subred en la que se aprovisionará el servicio:
    >
    > * Punto de conexión de base de datos de destino (por ejemplo, punto de conexión de SQL, punto de conexión de Cosmos DB, etc.)
    > * Punto de conexión de Storage
    > * Punto de conexión de Service Bus
    >
    > Esta configuración es necesaria porque Azure Database Migration Service no tiene conexión a Internet.

* Asegúrese de que las reglas del grupo de seguridad de red (NSG) de la red virtual no bloqueen el puerto 443 de salida de ServiceTag para ServiceBus, Storage y AzureMonitor. Para más información sobre el filtrado del tráfico con grupos de seguridad de red para redes virtuales, vea el artículo [Filtrado del tráfico de red con grupos de seguridad de red](../virtual-network/virtual-network-vnet-plan-design-arm.md).
* Configurar su [Firewall de Windows para acceder al motor de base de datos](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access).
* Abra el Firewall de Windows para permitir que Azure Database Migration Service tenga acceso al servidor PostgreSQL de origen que, de manera predeterminada, es el puerto TCP 5432.
* Cuando se usa un dispositivo de firewall frente a las bases de datos de origen, puede que sea necesario agregar reglas de firewall para permitir que Azure Database Migration Service acceda a las bases de datos de origen para realizar la migración.
* Cree una [regla de firewall](../postgresql/concepts-firewall-rules.md) en el nivel de servidor para que Azure Database for PostgreSQL permita a Azure Database Migration Service tener acceso a las bases de datos de destino. Proporcione el rango de subred de la red virtual que se usa para Azure Database Migration Service.
* Hay dos métodos para invocar la CLI:

  * En la esquina superior derecha de Azure Portal, seleccione el botón Cloud Shell:

       ![Botón Cloud Shell en Azure Portal](media/tutorial-postgresql-to-azure-postgresql-online/cloud-shell-button.png)

  * Instale y ejecute la CLI localmente. Se necesita la versión 2.18 o posterior de la CLI de la herramienta de línea de comandos para poder administrar los recursos de Azure necesarios para esta migración.

       Para descargar la CLI, siga las instrucciones del artículo [Instalación de la CLI de Azure](/cli/azure/install-azure-cli). En el artículo también se enumeran las plataformas que admiten la CLI de Azure.

       Para configurar el subsistema Windows para Linux (WSL), siga las instrucciones de la [guía de instalación de Windows 10](/windows/wsl/install-win10)

* Para habilitar la replicación lógica en el servidor de origen, edite el archivo postgresql.config y defina los parámetros siguientes:

  * wal_level = **logical**
  * max_replication_slots = [número de ranuras], se recomienda establecer en **cinco ranuras**
  * max_wal_senders = [número de tareas simultáneas]; el parámetro max_wal_senders establece el número de tareas simultáneas que puede ejecutar; se recomienda establecerlo en **10 tareas**

## <a name="migrate-the-sample-schema"></a>Migración del esquema de ejemplo

Para completar todos los objetos de base de datos como esquemas de tabla, índices y procedimientos almacenados, se debe extraer el esquema de la base de datos de origen y aplicarlo a la base de datos.

1. Use el comando pg_dump -s para crear un archivo de volcado de esquema para una base de datos. 

    ```
    pg_dump -o -h hostname -U db_username -d db_name -s > your_schema.sql
    ```

    Por ejemplo, para volcar una base de datos dvdrental de archivo de esquema:
    ```
    pg_dump -o -h localhost -U postgres -d dvdrental -s  > dvdrentalSchema.sql
    ```

    Para más información sobre el uso de la utilidad pg_dump, vea los ejemplos del tutorial [pg volcado](https://www.postgresql.org/docs/9.6/static/app-pgdump.html#PG-DUMP-EXAMPLES).

2. Cree una base de datos vacía en el entorno de destino, que es Azure Database for PostgreSQL.

    Para más información sobre cómo crear una base de datos y conectarse a ella, consulte el artículo [Inicio rápido: Creación de un servidor de Azure Database for PostgreSQL en Azure Portal](../postgresql/quickstart-create-server-database-portal.md) o [Inicio rápido: Creación de una instancia de Hiperescala (Citus) de Azure Database for PostgreSQL en Azure Portal](../postgresql/quickstart-create-hyperscale-portal.md).

3. Importe el esquema en la base de datos de destino que creó restaurando el archivo de volcado de esquema.

    ```
    psql -h hostname -U db_username -d db_name < your_schema.sql 
    ```

    Por ejemplo:

    ```
    psql -h mypgserver-20170401.postgres.database.azure.com  -U postgres -d dvdrental < dvdrentalSchema.sql
    ```

  > [!NOTE]
   > El servicio de migración controla internamente la habilitación o deshabilitación de claves externas y desencadenadores para garantizar una migración de datos confiable y sólida. Como resultado, no tiene que preocuparse por realizar modificaciones en el esquema de la base de datos de destino.

## <a name="provisioning-an-instance-of-dms-using-the-azure-cli"></a>Aprovisionamiento de una instancia de DMS mediante la CLI de Azure

1. Instale la extensión de sincronización de dms:
   * Inicie sesión en Azure ejecutando el siguiente comando:
       ```azurecli
       az login
       ```

   * Cuando se le pida, abra un explorador web y escriba un código para autenticar el dispositivo. Siga las instrucciones según se indican.

   * La migración en línea de PostgreSQL ahora está disponible en el paquete normal de la CLI (versión 2.18.0 y posterior) sin necesidad de la extensión `dms-preview`. Si ya ha instalado la extensión, puede quitarla mediante los pasos siguientes:
        * Para comprobar si ya tiene instalada la extensión `dms-preview`, ejecute el siguiente comando:
        
            ```azurecli
            az extension list -o table
            ```

        * Si la extensión `dms-preview` está instalada, ejecute el siguiente comando para desinstalarla:
        
            ```azurecli
            az extension remove --name dms-preview
            ```

        * Para comprobar que ha desinstalado la extensión `dms-preview` correctamente, ejecute el siguiente comando y ya no debería ver la extensión `dms-preview` en la lista:

            ```azurecli
            az extension list -o table
            ```

      > [!IMPORTANT]
      > La extensión `dms-preview` todavía puede ser necesaria para otras rutas de migración admitidas por Azure DMS. Compruebe la documentación de la ruta de migración específica para decidir si la extensión es necesaria. En esta documentación se trata el requisito de extensión, específico de PostgreSQL para Azure Database for PostgreSQL en línea.

   * En cualquier momento, vea todos los comandos admitidos en DMS ejecutando:

       ```azurecli
       az dms -h
       ```

   * Si tiene varias suscripciones de Azure, ejecute el siguiente comando para establecer la suscripción que desea usar para aprovisionar una instancia del servicio DMS.

        ```azurecli
       az account set -s 97181df2-909d-420b-ab93-1bff15acb6b7
        ```

2. Aprovisionar una instancia de DMS, ejecute el comando siguiente:

   ```azurecli
   az dms create -l <location> -n <newServiceName> -g <yourResourceGroupName> --sku-name Premium_4vCores --subnet/subscriptions/{vnet subscription id}/resourceGroups/{vnet resource group}/providers/Microsoft.Network/virtualNetworks/{vnet name}/subnets/{subnet name} –tags tagName1=tagValue1 tagWithNoValue
   ```

   Por ejemplo, el comando siguiente creará un servicio en:

   * Ubicación: Este de EE. UU. 2
   * Suscripción: 97181df2-909d-420b-ab93-1bff15acb6b7
   * Nombre del grupo de recursos: PostgresDemo
   * Nombre del servicio DMS: PostgresCLI

   ```azurecli
   az dms create -l eastus2 -g PostgresDemo -n PostgresCLI --subnet /subscriptions/97181df2-909d-420b-ab93-1bff15acb6b7/resourceGroups/ERNetwork/providers/Microsoft.Network/virtualNetworks/AzureDMS-CORP-USC-VNET-5044/subnets/Subnet-1 --sku-name Premium_4vCores
   ```

   La instancia del servicio DMS tarda en crearse entre 10 y 12 minutos aproximadamente.

3. Para identificar la dirección IP del agente DMS para que se pueda agregar al archivo pg_hba.conf de Postgres, ejecute el siguiente comando:

    ```azurecli
    az network nic list -g <ResourceGroupName>--query '[].ipConfigurations | [].privateIpAddress'
    ```

    Por ejemplo:

    ```azurecli
    az network nic list -g PostgresDemo --query '[].ipConfigurations | [].privateIpAddress'
    ```

    Debe obtener un resultado similar a la siguiente dirección: 

    ```output
    [
      "172.16.136.18"
    ]
    ```

4. Agregue la dirección IP del agente DMS al archivo pg_hba.conf de Postgres.

    * Anote la dirección IP de DMS cuando termine el aprovisionamiento en DMS.
    * Agregue la dirección IP al archivo pg_hba.conf en el origen, de forma similar a la entrada siguiente:

        ```
        host     all            all        172.16.136.18/10    md5
        host     replication    postgres   172.16.136.18/10    md5
        ```

5. A continuación, cree un proyecto de migración de PostgreSQL ejecutando el comando siguiente:
    
    ```azurecli
    az dms project create -l <location> -g <ResourceGroupName> --service-name <yourServiceName> --source-platform PostgreSQL --target-platform AzureDbforPostgreSQL -n <newProjectName>
    ```

    Por ejemplo, el comando siguiente crea un proyecto mediante estos parámetros:

    * Ubicación: Centro-Oeste de EE. UU.
    * Nombre del grupo de recursos: PostgresDemo
    * Nombre del servicio: PostgresCLI
    * Nombre del proyecto: PGMigration
    * Plataforma de origen: PostgreSQL
    * Plataforma de destino: AzureDbForPostgreSql   

     ```azurecli
     az dms project create -l westcentralus -n PGMigration -g PostgresDemo --service-name PostgresCLI --source-platform PostgreSQL --target-platform AzureDbForPostgreSql
     ```

6. Cree una tarea de migración de PostgreSQL mediante los siguientes pasos.

    Este paso incluye el uso de la dirección IP de origen, el identificador de usuario y la contraseña, la dirección IP de destino, el identificador de usuario, la contraseña y el tipo de tarea para establecer la conectividad.

    * Para ver una lista completa de opciones, ejecute el comando:

        ```azurecli
        az dms project task create -h
        ```

        Para la conexión de origen y destino, el parámetro de entrada hace referencia a un archivo json que tiene la lista de objetos.

        El formato del objeto JSON de conexión para las conexiones de PostgreSQL.
        
        ```json
        {
            // if this is missing or null, you will be prompted
            "userName": "user name",
            // if this is missing or null (highly recommended) you will  be prompted  
            "password": null,
            "serverName": "server name",
            // if this is missing, it will default to the 'postgres' database
            "databaseName": "database name",
            // if this is missing, it will default to 5432 
            "port": 5432                
        }
        ```

        También hay un archivo json de opción de base de datos que enumera los objetos json. Para PostgreSQL, el formato del objeto JSON de las opciones de base de datos se muestra a continuación:

        ```json
        [
            {
                "name": "source database",
                "target_database_name": "target database",
                "selectedTables": [
                    "schemaName1.tableName1",
                    ...n
                ]
            },
            ...n
        ]
        ```

    * Para crear el JSON de conexión de origen, abra Bloc de notas, copie el siguiente código JSON y péguelo en el archivo. Guarde el archivo en C:\DMS\source.json después de modificarlo según el servidor de origen.

        ```json
        {
            "userName": "postgres",    
            "password": null,
            "serverName": "13.51.14.222",
            "databaseName": "dvdrental", 
            "port": 5432                
        }
        ```

    * Para crear el JSON de conexión de destino, abra Bloc de notas, copie el siguiente código JSON y péguelo en el archivo. Guarde el archivo en C:\DMS\target.json después de modificarlo según el servidor de destino.
    
        ```json
        {
            "userName": " dms@builddemotarget",    
            "password": null,           
            "serverName": " builddemotarget.postgres.database.azure.com",
            "databaseName": "inventory", 
            "port": 5432                
        }
        ```

    * Cree un archivo JSON de opciones de base de datos que enumere el inventario y la asignación de las bases de datos para migrar:

        * Cree una lista de tablas que se van a migrar, o bien puede usar una consulta SQL para generar la lista a partir de la base de datos de origen. A continuación se proporciona una consulta de ejemplo para generar la lista de tablas. Si usa esta consulta, no olvide quitar la última coma al final del nombre de la última tabla para convertirla en una matriz JSON válida. 
        
            ```sql
            SELECT
                FORMAT('%s,', REPLACE(FORMAT('%I.%I', schemaname, tablename), '"', '\"')) AS SelectedTables
            FROM 
                pg_tables
            WHERE 
                schemaname NOT IN ('pg_catalog', 'information_schema');
            ```

        * Cree el archivo JSON de opciones de base de datos con una entrada para cada base de datos con los nombres de base de datos de origen y destino, y la lista de tablas seleccionadas que se van a migrar. Puede usar la salida de la consulta SQL anterior para rellenar la matriz *"selectedTables"* . **Tenga en cuenta que si la lista de tablas seleccionadas está vacía, el servicio incluirá en la migración todas las tablas que tengan nombres de tabla y esquema coincidentes.**
        
            ```json
            [
                {
                    "name": "dvdrental",
                    "target_database_name": "dvdrental",
                    "selectedTables": [
                        "schemaName1.tableName1",
                        "schemaName1.tableName2",                    
                        ...
                        "schemaNameN.tableNameM"
                    ]
                },
                ... n
            ]
            ```

    * Ejecute el siguiente comando, que toma la conexión de origen, la conexión de destino y los archivos JSON de opciones de base de datos.

        ```azurecli
        az dms project task create -g PostgresDemo --project-name PGMigration --source-connection-json c:\DMS\source.json --database-options-json C:\DMS\option.json --service-name PostgresCLI --target-connection-json c:\DMS\target.json --task-type OnlineMigration -n runnowtask    
        ```

    Llegados a este punto, ha enviado correctamente una tarea de migración.

7. Para mostrar el progreso de la tarea, ejecute el siguiente comando:

    * Para ver el estado general de la tarea en formato conciso:
        ```azurecli
        az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
        ```

    * Para ver el estado detallado de la tarea, incluida la información del progreso de la migración:

        ```azurecli
        az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask --expand output
        ```

    * También puede usar el formato de consulta [JMESPATH](https://jmespath.org/) para extraer solo migrationState de la salida expandida:

        ```azurecli
        az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask --expand output --query 'properties.output[].migrationState'
        ```

        En la salida, hay varios parámetros que indican el progreso de los distintos pasos de la migración. Por ejemplo, vea la salida a continuación:

        ```json
        {
            "output": [
                // Database Level
                {
                    "appliedChanges": 0, // Total incremental sync applied after full load
                    "cdcDeleteCounter": 0, // Total delete operation  applied after full load
                    "cdcInsertCounter": 0, // Total insert operation applied after full load
                    "cdcUpdateCounter": 0, // Total update operation applied after full load
                    "databaseName": "inventory",
                    "endedOn": null,
                    "fullLoadCompletedTables": 2, //Number of tables completed full load
                    "fullLoadErroredTables": 0, //Number of tables that contain migration error
                    "fullLoadLoadingTables": 0, //Number of tables that are in loading status
                    "fullLoadQueuedTables": 0, //Number of tables that are in queued status
                    "id": "db|inventory",
                    "incomingChanges": 0, //Number of changes after full load
                    "initializationCompleted": true,
                    "latency": 0,
                    //Status of migration task
                    "migrationState": "READY_TO_COMPLETE", //READY_TO_COMPLETE => the database is ready for cutover
                    "resultType": "DatabaseLevelOutput",
                    "startedOn": "2018-07-05T23:36:02.27839+00:00"
                }, {
                    "databaseCount": 1,
                    "endedOn": null,
                    "id": "dd27aa3a-ed71-4bff-ab34-77db4261101c",
                    "resultType": "MigrationLevelOutput",
                    "sourceServer": "138.91.123.10",
                    "sourceVersion": "PostgreSQL",
                    "startedOn": "2018-07-05T23:36:02.27839+00:00",
                    "state": "PENDING",
                    "targetServer": "builddemotarget.postgres.database.azure.com",
                    "targetVersion": "Azure Database for PostgreSQL"
                },
                // Table 1
                {
                    "cdcDeleteCounter": 0,
                    "cdcInsertCounter": 0,
                    "cdcUpdateCounter": 0,
                    "dataErrorsCount": 0,
                    "databaseName": "inventory",
                    "fullLoadEndedOn": "2018-07-05T23:36:20.740701+00:00", //Full load completed time
                    "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
                    "fullLoadStartedOn": "2018-07-05T23:36:15.864552+00:00", //Full load started time
                    "fullLoadTotalRows": 10, //Number of rows loaded in full load
                    "fullLoadTotalVolumeBytes": 7056, //Volume in Bytes in full load
                    "id": "or|inventory|public|actor",
                    "lastModifiedTime": "2018-07-05T23:36:16.880174+00:00",
                    "resultType": "TableLevelOutput",
                    "state": "COMPLETED", //State of migration for this table
                    "tableName": "public.catalog", //Table name
                    "totalChangesApplied": 0 //Total sync changes that applied after full load
                },
                //Table 2
                {
                    "cdcDeleteCounter": 0,
                    "cdcInsertCounter": 50,
                    "cdcUpdateCounter": 0,
                    "dataErrorsCount": 0,
                    "databaseName": "inventory",
                    "fullLoadEndedOn": "2018-07-05T23:36:23.963138+00:00",
                    "fullLoadEstFinishTime": "1970-01-01T00:00:00+00:00",
                    "fullLoadStartedOn": "2018-07-05T23:36:19.302013+00:00",
                    "fullLoadTotalRows": 112,
                    "fullLoadTotalVolumeBytes": 46592,
                    "id": "or|inventory|public|address",
                    "lastModifiedTime": "2018-07-05T23:36:20.308646+00:00",
                    "resultType": "TableLevelOutput",
                    "state": "COMPLETED",
                    "tableName": "public.orders",
                    "totalChangesApplied": 0
                }
            ],
            // DMS migration task state
            "state": "Running", //Running => service is still listening to any changes that might come in
            "taskType": null
        }
        ```

## <a name="cutover-migration-task"></a>Tarea de migración total

La base de datos está lista para la migración total cuando se complete la carga total. Dependiendo de lo ocupado que esté el servidor de origen con las nuevas transacciones, la tarea DMS podría seguir aplicando cambios una vez completada la carga total.

Para garantizar que todos los datos estén al día, valide los recuentos de filas entre las bases de datos de origen y destino. Por ejemplo, puede validar los siguientes detalles de la salida de estado:

```
Database Level
"migrationState": "READY_TO_COMPLETE" => Status of migration task. READY_TO_COMPLETE means database is ready for cutover
"incomingChanges": 0 => Check for a period of 5-10 minutes to ensure no new incoming changes need to be applied to the target server

Table Level (for each table)
"fullLoadTotalRows": 10    => The row count matches the initial row count of the table
"cdcDeleteCounter": 0      => Number of deletes after the full load
"cdcInsertCounter": 50     => Number of inserts after the full load
"cdcUpdateCounter": 0      => Number of updates after the full load
```

1. Realice la tarea de migración de base de datos de transición mediante el comando siguiente:

    ```azurecli
    az dms project task cutover -h
    ```

    Por ejemplo, el siguiente comando iniciará la transición para la base de datos "Inventory":

    ```azurecli
    az dms project task cutover --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask  --object-name Inventory
    ```

2. Para supervisar el progreso de transición, ejecute el comando siguiente:

    ```azurecli
    az dms project task show --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
    ```
3. Cuando aparezca el estado **Completado** de la migración de base de datos, [recree las secuencias](https://wiki.postgresql.org/wiki/Fixing_Sequences) (si procede) y conecte las aplicaciones a la nueva instancia de destino de Azure Database for PostgreSQL.

## <a name="service-project-task-cleanup"></a>Limpieza de servicios, proyectos y tareas

Si necesita cancelar o eliminar cualquier tarea, proyecto o servicio de DMS, realice la cancelación en la siguiente secuencia:

* Cancelar todas las tareas en ejecución
* Eliminar la tarea
* Eliminar el proyecto
* Eliminar el servicio DMS

1. Para cancelar una tarea en ejecución, utilice el siguiente comando:

    ```azurecli
    az dms project task cancel --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
     ```

2. Para eliminar una tarea en ejecución, utilice el siguiente comando:
    ```azurecli
    az dms project task delete --service-name PostgresCLI --project-name PGMigration --resource-group PostgresDemo --name runnowtask
    ```

3. Para cancelar un proyecto en ejecución, utilice el siguiente comando:
     ```azurecli
    az dms project task cancel -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
     ```

4. Para eliminar un proyecto en ejecución, utilice el siguiente comando:
    ```azurecli
    az dms project task delete -n runnowtask --project-name PGMigration -g PostgresDemo --service-name PostgresCLI
    ```

5. Para eliminar un servicio DMS, utilice el siguiente comando:

     ```azurecli
    az dms delete -g ProgresDemo -n PostgresCLI
     ```

## <a name="next-steps"></a>Pasos siguientes

* Para información sobre problemas y limitaciones conocidos al realizar migraciones en línea a Azure Database for PostgreSQL, consulte el artículo [Known issues and workarounds with Azure Database for PostgreSQL online migrations](known-issues-azure-postgresql-online.md) (Problemas conocidos y soluciones alternativas con migraciones en línea de Azure Database for PostgreSQL).
* Para más información sobre Azure Database Migration Service, consulte el artículo [¿Qué es Azure Database Migration Service?](./dms-overview.md)
* Para más información sobre Azure Database for PostgreSQL, consulte el artículo [¿Qué es Azure Database for PostgreSQL?](../postgresql/overview.md)