---
title: 'Tutorial: Migración de Azure Database for MySQL con servidor único a Azure Database for MySQL con servidor flexible con un tiempo de inactividad mínimo'
description: En este artículo se describe cómo realizar una migración con un tiempo de inactividad mínimo de Azure Database for MySQL con servidor único a Azure Database for MySQL con servidor flexible.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: how-to
ms.date: 06/18/2021
ms.openlocfilehash: cbabc94f1aa8a2dcd8d8022ab81b7d772006b574
ms.sourcegitcommit: abf31d2627316575e076e5f3445ce3259de32dac
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/15/2021
ms.locfileid: "114203481"
---
# <a name="tutorial-migrate-azure-database-for-mysql--single-server-to-azure-database-for-mysql--flexible-server-with-minimal-downtime"></a>Tutorial: Migración de Azure Database for MySQL con servidor único a Azure Database for MySQL con servidor flexible con un tiempo de inactividad mínimo

Puede migrar una instancia de Azure Database for MySQL con servidor único a Azure Database for MySQL con servidor flexible con un tiempo de inactividad mínimo en las aplicaciones mediante una combinación de herramientas de código abierto como mydumper/myloader y la replicación de datos de entrada.

> [!NOTE]
> Este artículo contiene referencias al término _esclavo_, un término que Microsoft ya no usa. Cuando se elimine el término del software, se eliminará también de este artículo.

La replicación de datos de entrada es una técnica que replica los cambios de datos del servidor de origen en el servidor de destino en función del método de posición del archivo de registro binario. En este escenario, la instancia de MySQL que funciona como origen (en la que se originan los cambios de la base de datos) escribe las actualizaciones y los cambios como "eventos" en el registro binario. La información del registro binario se almacena en diferentes formatos de registro según los cambios de la base de datos que se registran. Las réplicas están configuradas para leer el registro binario del origen y ejecutar los eventos del registro binario en la base de datos local de la réplica.

Si configura la replicación de datos de entrada para sincronizar datos de una instancia de Azure Database for MySQL a otra, puede realizar una transferencia selectiva de las aplicaciones desde la base de datos principal (o base de datos de origen) a la réplica (o base de datos de destino).

En este tutorial, usará mydumper/myloader y la replicación de datos de entrada para migrar una base de datos de ejemplo ([classicmodels](https://www.mysqltutorial.org/mysql-sample-database.aspx)) de una instancia de Azure Database for MySQL con servidor único a una instancia de Azure Database for MySQL con servidor flexible y, a continuación, sincronizará los datos.

En este tutorial, aprenderá a:

* Configurar la red para la replicación de datos de entrada en distintos escenarios.
* Configurar la replicación de datos de entrada entre el servidor principal y el servidor de réplica.
* Probar la replicación.
* Realizar la migración total.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

* Una instancia de Azure Database for MySQL con servidor único que ejecute la versión 5.7 u 8.0.
    > [!Note]
    > Si ejecuta la versión 5.6 de Azure Database for MySQL con servidor único, actualice la instancia a la versión 5.7 y después configure los datos en la replicación. Para más información, consulte [Actualización de versión principal de Azure Database for MySQL con un servidor único](how-to-major-version-upgrade.md).
* Una instancia de Azure Database for MySQL con servidor flexible. Para obtener más información, consulte el artículo [Inicio rápido: Uso de Azure Portal para crear un servidor flexible de Azure Database for MySQL](./flexible-server/quickstart-create-server-portal.md).
    > [!Note]
    > No se admite la configuración de la replicación de datos de entrada para servidores de alta disponibilidad con redundancia de zona. Si desea tener alta disponibilidad con redundancia de zona en el servidor de destino, siga estos pasos:
    >
    > 1. Cree el servidor con la opción Disponibilidad alta con redundancia de zona habilitada.
    > 2. Deshabilite la opción de alta disponibilidad.
    > 3. Siga el artículo para configurar la replicación de datos de entrada.
    > 4. Tras la migración total, eliminación la configuración de la replicación de datos de entrada.
    > 5. Habilite la opción de alta disponibilidad.
    >
    > *Asegúrese de que **[GTID_Mode](./concepts-read-replicas.md#global-transaction-identifier-gtid)** tiene el mismo valor en los servidores de origen y de destino.*

* Conectarse y crear una base de datos mediante MySQL Workbench. Para más información, consulte el artículo [Inicio rápido: Use MySQL Workbench para conectarse a Azure Database for MySQL con la opción Servidor flexible (versión preliminar) y consultar datos en este servicio](./flexible-server/connect-workbench.md).
* Asegurarse de que tiene una máquina virtual de Azure que ejecuta Linux en la misma región (o en la misma red virtual, con acceso privado) que hospeda las bases de datos de origen y de destino.
* Instalar el cliente mysql o MySQL Workbench (las herramientas de cliente) en la máquina virtual de Azure. Asegúrese de que puede conectarse al servidor principal y al servidor de réplica. Para los fines de este artículo, se instala el cliente mysql.
* Instalar mydumper/myloader en la máquina virtual de Azure. Para más información, consulte el artículo [Migración de bases de datos grandes a Azure Database for MySQL mediante mydumper/myloader](concepts-migrate-mydumper-myloader.md).
* Descargar y ejecutar el script de base de datos de ejemplo en la base de datos [classicmodels](https://www.mysqltutorial.org/wp-content/uploads/2018/03/mysqlsampledatabase.zip) del servidor de origen.

## <a name="configure-networking-requirements"></a>Configuración de los requisitos de red

Para configurar la replicación de datos de entrada, debe asegurarse de que el destino puede conectarse al origen por medio del puerto 3306. En función del tipo de punto de conexión configurado en el origen, realice los pasos siguientes correspondientes.

* Si se ha habilitado un punto de conexión público en el origen, asegúrese de que el destino pueda conectarse al origen habilitando "Permitir el acceso a servicios de Azure" en la regla de firewall. Para más información, consulte [Reglas de firewall: Azure Database for MySQL](./concepts-firewall-rules.md#connecting-from-azure). 
* Si se ha habilitado un punto de conexión privado y "[Denegación del acceso público](concepts-data-access-security-private-link.md#deny-public-access-for-azure-database-for-mysql)" en el origen, instale el vínculo privado en la misma red virtual que hospeda el destino. Para más información, consulte [Private Link: Azure Database for MySQL](concepts-data-access-security-private-link.md).

## <a name="configure-data-in-replication"></a>Configuración de la replicación de datos de entrada

Para configurar la replicación de datos de entrada, lleve a cabo los siguientes pasos:

1. Inicie sesión en la máquina virtual de Azure en la que ha instalado la herramienta de cliente mysql.

2. Conéctese al origen y al destino mediante la herramienta de cliente mysql.

3. Use la herramienta de cliente mysql para determinar si log_bin está habilitado en el origen con la ejecución del comando siguiente:

    ```sql
    SHOW VARIABLES LIKE 'log_bin';
    ```

    > [!Note]
    > En Azure Database for MySQL con servidor único y con el almacenamiento de gran tamaño, que admite hasta 16 TB, esta opción está habilitada de forma predeterminada.

    > [!Tip]
    > En Azure Database for MySQL con servidor único, que admite hasta 4 TB, esta opción no está habilitada de forma predeterminada. Sin embargo, si promueve una [réplica de lectura](howto-read-replicas-portal.md) para el servidor de origen y luego la elimina, el parámetro se establecerá en ON.

4. En función del cumplimiento de SSL para el servidor de origen, cree un usuario en el servidor de origen con permiso de replicación mediante la ejecución del comando correspondiente.

    Si usa SSL, ejecute el siguiente comando:

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
    ```

    Si no usa SSL, ejecute el siguiente comando:

    ```sql
    CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
    GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
    ```

5. Para realizar una copia de seguridad de la base de datos mediante mydumper, ejecute el siguiente comando en la máquina virtual de Azure donde se ha instalado mydumper\myloader:

    ```bash
    $ mydumper --host=<primary_server>.mysql.database.azure.com --user=<username>@<primary_server> --password=<Password> --outputdir=./backup --rows=100 -G -E -R -z --trx-consistency-only --compress --build-empty-files --threads=16 --compress-protocol --ssl  --regex '^(classicmodels\.)' -L mydumper-logs.txt
    ```

    > [!Tip]
    > La opción **--trx-consistency-only** se necesita para asegurar la coherencia transaccional mientras se hace la copia de seguridad.
    >
    > * Equivalente en mydumper de --single-transaction en mysqldump.
    > * Resulta útil si todas las tablas son InnoDB.
    > * El subproceso "principal" solo necesita mantener el bloqueo global hasta que los subprocesos de "volcado" puedan iniciar una transacción.
    > * Ofrece la duración más corta del bloqueo global.

    El subproceso "principal" solo necesita mantener el bloqueo global hasta que los subprocesos de "volcado" puedan iniciar una transacción.

    A continuación se explican las variables de este comando:

    * **--host**: nombre del servidor principal.
    * **--user**: nombre de un usuario (con el formato username@servername dado que el servidor principal ejecuta Azure Database for MySQL con servidor único). Puede usar el administrador del servidor o un usuario que tenga permisos SELECT y RELOAD.
    * **--Password**: la contraseña del usuario anterior.

    Para más información sobre el uso de mydumper, consulte [mydumper/myloader](concepts-migrate-mydumper-myloader.md).

6. Lea el archivo de metadatos para determinar el nombre y el desplazamiento del archivo de registro binario mediante la ejecución del comando siguiente:

    ```bash
    $ cat ./backup/metadata 
    ```

    En este comando, **./backup** hace referencia al directorio de salida utilizado en el comando del paso anterior.

    Los resultados deben aparecer como se muestra en la siguiente imagen:

    :::image type="content" source="./media/howto-migrate-single-flexible-minimum-downtime/metadata.png" alt-text="Sincronización continua con Azure Database Migration Service":::

    Asegúrese de anotar el nombre del archivo binario, pues se utilizará en pasos posteriores.

7. Restaure la base de datos con myloader ejecutando el siguiente comando:

    ```bash
    $ myloader --host=<servername>.mysql.database.azure.com --user=<username> --password=<Password> --directory=./backup --queries-per-transaction=100 --threads=16 --compress-protocol --ssl --verbose=3 -e 2>myloader-logs.txt
    ```

    A continuación se explican las variables de este comando:

    * **--host**: el nombre del servidor de réplica.
    * **--user**: el nombre de un usuario. Puede usar el administrador del servidor o un usuario con permiso de lectura y escritura capaz de restaurar los esquemas y los datos en la base de datos.
    * **--Password**: contraseña del usuario anterior.

8. En función del cumplimiento de SSL en el servidor principal, conéctese al servidor de réplica mediante la herramienta de cliente mysql y lleve a cabo los pasos siguientes.

    * Si el cumplimiento de SSL está habilitado, haga lo siguiente:

        i. Descargue [aquí](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) el certificado necesario para comunicarse a través de SSL con el servidor de Azure Database for MySQL.

        ii. Abra el archivo en el Bloc de notas y pegue el contenido en la sección "PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE".

        ```sql
        SET @cert = '-----BEGIN CERTIFICATE-----
        PLACE YOUR PUBLIC KEY CERTIFICATE'S CONTEXT HERE 
        -----END CERTIFICATE-----'
        ```

        iii. Para configurar la replicación de datos de entrada, ejecute el siguiente comando:

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '=<username>@<primary_server>', '<Password>, 3306, '<File_Name>', <Position>, @cert);
        ```

        > [!Note]
        > Determine la posición y el nombre de archivo a partir de la información obtenida en el paso 6.

    * Si el cumplimiento de SSL no está habilitado, ejecute el siguiente comando:

        ```sql
        CALL mysql.az_replication_change_master('<Primary_server>.mysql.database.azure.com', '=<username>@<primary_server>', '<Password>, 3306, '<File_Name>', <Position>, ‘’);
        ```

9. Para iniciar la replicación desde el servidor de réplica, llame al procedimiento almacenado siguiente.

    ```sql
    call  mysql.az_replication_start;
    ```

10. Para comprobar el estado de replicación, en el servidor de réplica, ejecute el siguiente comando:

     ```sql
     show slave status \G; 
     ```

    > [!Note]
    > Si usa MySQL Workbench, no necesita el modificador \G.

    Si el estado de *Slave_IO_Running* y *Slave_SQL_Running* es Yes (Sí) y el valor de *Seconds_Behind_Master* es 0, la replicación funciona correctamente. Seconds_Behind_Master indica el retraso de la réplica. Si el valor es distinto de 0, la réplica está procesando actualizaciones.

## <a name="testing-the-replication-optional"></a>Prueba de la replicación (opcional)

Para confirmar que la replicación de datos de entrada funciona correctamente, puede comprobar si los cambios en las tablas del servidor principal aparecen en la réplica.

1. Identifique la tabla que se va a usar para realizar pruebas (por ejemplo, la tabla Customers) y, a continuación, confirme que el número de entradas que contiene es el mismo en los servidores principal y de réplica mediante la ejecución del siguiente comando en cada uno de ellos:

    ```
    select count(*) from customers;
    ```

2. Tome nota del número de entradas para la comparación posterior.

    Para probar la replicación, agregaremos algunos datos a la tabla de clientes en el servidor principal y comprobaremos si los nuevos datos se replican. En este caso, agregará dos filas a una tabla en el servidor principal y, a continuación, confirmará que aparecen en el servidor de réplica.

3. En la tabla Customers del servidor principal, ejecute el siguiente comando para insertar filas:

    ```sql
    insert  into `customers`(`customerNumber`,`customerName`,`contactLastName`,`contactFirstName`,`phone`,`addressLine1`,`addressLine2`,`city`,`state`,`postalCode`,`country`,`salesRepEmployeeNumber`,`creditLimit`) values 
    (<ID>,'name1','name2','name3 ','11.22.5555','54, Add',NULL,'Add1',NULL,'44000','country',1370,'21000.00');
    ```

4. Para comprobar el estado de replicación, llame a *show slave status \G* para confirmar si la replicación funciona según lo previsto.

5. Para confirmar que el recuento es el mismo, en el servidor de réplica, ejecute el siguiente comando:

    ```sql
    select count(*) from customers;
    ```

## <a name="ensure-a-successful-cutover"></a>Garantía de migración total correcta

Para asegurarse de que la migración total se realiza correctamente, realice las siguientes tareas:

1. Configure las reglas de firewall y de red virtual de nivel de servidor adecuadas para conectarse al servidor de destino. Puede comparar las reglas de firewall para el [origen](howto-manage-firewall-using-portal.md) y el [destino](./flexible-server/how-to-manage-firewall-portal.md#create-a-firewall-rule-after-server-is-created) en el portal.
2. Configure los inicios de sesión y los permisos de nivel de base de datos adecuados en el servidor de destino. Puede ejecutar *SELECT * FROM mysql.user;* en los servidores de origen y de destino para comparar.
3. Asegúrese de que se detienen todas las conexiones entrantes en Azure Database for MySQL con servidor único.
    > [!Tip]
    > Puede establecer la configuración de Azure Database for MySQL con servidor único en solo lectura.
4. Asegúrese de que el servidor de réplica está al día con el servidor principal; para ello, ejecute *show slave status \G* y confirme que el valor del parámetro *Seconds_Behind_Master* es 0.
5. Redirija los clientes y las aplicaciones cliente a la instancia de destino de Azure Database for MySQL con servidor flexible.
6. Realice la migración total final mediante la ejecución del procedimiento almacenado mysql.az_replication_stop, que detendrá la replicación desde el servidor de réplica.
7. Llame a *mysql.az_replication_remove_master* para quitar la configuración de replicación de datos de entrada.

En este momento, las aplicaciones están conectadas al nuevo servidor flexible de Azure Database for MySQL y los cambios en el origen ya no se replican en el destino.

## <a name="next-steps"></a>Pasos siguientes

* Más información sobre la replicación de datos de entrada en [Replicación de datos en el servidor flexible de Azure Database for MySQL (versión preliminar)](flexible-server/concepts-data-in-replication.md) y [Configuración de la replicación de datos de entrada del servidor flexible de Azure Database for MySQL](./flexible-server/how-to-data-in-replication.md)
* Más información sobre la [solución de errores comunes en Azure Database for MySQL](howto-troubleshoot-common-errors.md)
* Más información sobre la [migración de MySQL a Azure Database for MySQL sin conexión con Azure Database Migration Service](../dms/tutorial-mysql-azure-mysql-offline-portal.md)
