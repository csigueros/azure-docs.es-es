---
title: Migración de RDS de Amazon para MySQL a Azure Database for MySQL mediante Replicación de datos de entrada
description: En este artículo se describe cómo migrar RDS de Amazon para MySQL a Azure Database for MySQL mediante Replicación de datos de entrada.
author: SudheeshGH
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: d05806b9d69543fdd4f5584661a6b8b74ac1f01e
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128647164"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-data-in-replication"></a>Migración de RDS de Amazon para MySQL a Azure Database for MySQL mediante Replicación de datos de entrada

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

> [!NOTE]
> Este artículo contiene referencias al término *esclavo*, un término que Microsoft ya no usa. Cuando el término se elimine del software, se eliminará también de este artículo.

Puede usar varios métodos, como el volcado y la restauración de MySQL, exportación o importación de MySQL Workbench o Azure Database Migration Service (DMS) para migrar las bases de datos MySQL a Azure Database for MySQL. Pero al usar una combinación de herramientas de código abierto como volcado y restauración de MySQL, o mydumper/myloader con Replicación de datos de entrada, puede migrar las cargas de trabajo con un tiempo de inactividad mínimo.

La replicación de datos de entrada es una técnica que replica los cambios de datos del servidor de origen en el servidor de destino en función del método de posición del archivo de registro binario. En este escenario, la instancia de MySQL que funciona como origen (en la que se originan los cambios de la base de datos) escribe las actualizaciones y los cambios como "eventos" en el registro binario. La información del registro binario se almacena en diferentes formatos de registro según los cambios de la base de datos que se registran. Las réplicas están configuradas para leer el registro binario del origen y ejecutar los eventos del registro binario en la base de datos local de la réplica.

Si configura [Replicación de datos de entrada](/azure/mysql/flexible-server/concepts-data-in-replication) para sincronizar datos desde un servidor MySQL de origen en uno de destino, puede realizar una migración selectiva de las aplicaciones desde la base de datos principal (o de origen) a la réplica (o base de datos de destino).

En este tutorial, aprenderá a configurar la replicación de datos de entrada entre un servidor de origen en el que se ejecuta RDS de Amazon para MySQL y un servidor de destino en el que se ejecuta Azure Database for MySQL.

## <a name="performance-considerations"></a>Consideraciones de rendimiento

Antes de comenzar este tutorial, asegúrese de tener en cuenta las implicaciones de rendimiento de la ubicación y la capacidad del equipo cliente que usará para realizar la operación.

### <a name="client-location"></a>Ubicación del cliente

Realice operaciones de volcado o restauración desde un equipo cliente que se inicia en la misma ubicación que el servidor de bases de datos:

- Para servidores de Azure Database for MySQL, la máquina cliente debe estar en la misma red virtual y zona de disponibilidad que el servidor de bases de datos de destino.
- Para las instancias de base de datos de RDS de Amazon de origen, la instancia de cliente debe existir en la misma nube privada virtual de Amazon (Amazon VPC) y zona de disponibilidad que el servidor de bases de datos de origen.
En el caso anterior, puede mover archivos de volcado entre máquinas cliente mediante protocolos de transferencia de archivos (como FTP o SFTP), o cargarlos en Azure Blob Storage. Para reducir el tiempo total de migración, puede comprimir los archivos antes de transferirlos.

### <a name="client-capacity"></a>Capacidad del cliente

Con independencia de dónde se encuentre el equipo cliente, necesita la capacidad de proceso, E/S y red adecuadas para realizar las operaciones solicitadas. Las recomendaciones generales son las siguientes:

- Si el volcado o la restauración implican el procesamiento en tiempo real de datos, por ejemplo, compresión o descompresión, elija una clase de instancia con al menos un núcleo de CPU por subproceso de volcado o restauración.
- Asegúrese de que haya suficiente ancho de banda de red disponible para la instancia de cliente. Se recomienda usar tipos de instancia que admitan la característica de redes aceleradas. Para obtener más información, vea la sección Redes aceleradas de la [Guía de redes de máquinas virtuales de Azure](/azure/virtual-network/create-vm-accelerated-networking-cli).
- Asegúrese de que la capa de almacenamiento de la máquina cliente proporciona la capacidad de lectura y escritura esperada; se recomienda una máquina virtual de Azure con almacenamiento SSD prémium.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

- Instale [mysqlclient](https://dev.mysql.com/downloads/) en el equipo cliente para crear un volcado y realizar una operación de restauración en el servidor de Azure Database for MySQL de destino.
- Para bases de datos más grandes, instale [mydumper/myloader](https://centminmod.com/mydumper.html) para el volcado y la restauración en paralelo de bases de datos.

    > [!NOTE]
    > mydumper solo se puede ejecutar en distribuciones de Linux; vea [Instalación de mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader).

- Cree una instancia del servidor de Azure Database for MySQL que ejecute la versión 5.7 u 8.0.

    > [!IMPORTANT]
    > Si el destino es Servidor flexible de Azure Database for MySQL con alta disponibilidad con redundancia de zona, tenga en cuenta que la replicación de datos de entrada no se admite para esta configuración. Como alternativa, durante la creación del servidor, configure la alta disponibilidad con redundancia de zona mediante los pasos siguientes:
    >
    > 1. Cree el servidor con la alta disponibilidad con redundancia de zona habilitada.
    > 2. Deshabilite la alta disponibilidad.
    > 3. Siga el artículo para configurar la replicación de datos de entrada.
    > 4. Tras la migración total, elimine la configuración de la replicación de datos de entrada.
    > 5. Habilite la alta disponibilidad.

También se recomienda asegurarse de que varios parámetros y características están configurados correctamente, como se describe a continuación.

- Por motivos de compatibilidad, se recomienda tener los servidores de bases de datos de origen y de destino en la misma versión de MySQL.
- Cada tabla debe tener una clave principal. La falta de claves principales en las tablas puede ralentizar el proceso de replicación.
- Asegúrese de que el juego de caracteres de las bases de datos de origen y de destino sea el mismo.
- Establezca el parámetro `wait_timeout` en un tiempo razonable, en función de la cantidad de datos o la carga de trabajo que quiera importar o migrar.
- Compruebe que todas las tablas usan InnoDB, ya que el servidor de Azure Database for MySQL solo admite el motor de almacenamiento de InnoDB.
- En el caso de las tablas con muchos índices secundarios o las que son muy grandes, los efectos de la sobrecarga de rendimiento son especialmente visibles durante la restauración; modifique los archivos de volcado de memoria para que las instrucciones `CREATE TABLE` no incluyan definiciones de clave secundaria y, después de importar los datos, vuelva a crear los índices secundarios para evitar la penalización del rendimiento durante el proceso de restauración.

Por último, a fin de prepararse para la replicación de datos de entrada:

- Compruebe que el servidor de Azure Database for MySQL de destino se puede conectar al servidor de RDS de Amazon para MySQL de origen mediante el puerto 3306.
- Asegúrese de que el servidor de RDS de Amazon para MySQL de origen permite el tráfico entrante y saliente en el puerto 3306.
- Asegúrese de proporcionar [conectividad de sitio a sitio](/azure/vpn-gateway/tutorial-site-to-site-portal) al servidor de origen mediante [ExpressRoute](/azure/expressroute/expressroute-introduction) o [VPN Gateway](/azure/vpn-gateway/vpn-gateway-about-vpngateways). Para obtener más información sobre la creación de una red virtual, vea la [documentación de Virtual Network](/azure/virtual-network/) y, especialmente, los artículos de inicio rápido con detalles paso a paso.
- Configure los grupos de seguridad de red del servidor de bases de datos de origen para permitir la dirección IP del servidor de Azure Database for MySQL de destino.

> [!IMPORTANT]
> Si la instancia de RDS de Amazon para MySQL de origen tiene GTID_mode establecido en ON, la instancia de destino de Azure Database for MySQL - Servidor flexible también debe tener GTID_mode establecido en ON.

## <a name="configure-the-target-instance-of-azure-database-for-mysql"></a>Configuración de la instancia de destino de Azure Database for MySQL

Para configurar la instancia de destino de Azure Database for MySQL (el destino para la replicación de datos de entrada), siga estos pasos:

1. Establezca el valor del parámetro `max_allowed_packet` en el máximo de **1073741824** (es decir, 1 GB), lo que evitará cualquier problema de desbordamiento relacionado con filas largas.
2. Establezca los parámetros `slow_query_log`, `general_log`, `audit_log_enabled` y `query_store_capture_mode` en **OFF** durante la migración para ayudar a eliminar cualquier sobrecarga relacionada con el registro de consultas.
3. Escale verticalmente el tamaño de proceso del servidor de Azure Database for MySQL de destino hasta un máximo de 64 núcleos virtuales para proporcionar recursos de proceso adicionales al restaurar el volcado de base de datos desde el servidor de origen.

    Siempre puede volver a modificar la escala del proceso para satisfacer las demandas de la aplicación una vez que se complete la migración.

4. Escale verticalmente el tamaño de almacenamiento para obtener más IOPS durante la migración, o bien aumente el número máximo de IOPS para la migración.

   > [!NOTE]
   > Las IOPS máximas disponibles están determinadas por el tamaño de proceso. Para obtener más información, vea la sección IOPS en [Opciones de proceso y almacenamiento en Azure Database for MySQL - Servidor flexible](/azure/mysql/flexible-server/concepts-compute-storage#iops).

## <a name="configure-the-source-amazon-rds-for-mysql-server"></a>Configuración del servidor de origen de RDS de Amazon para MySQL

Para preparar y configurar el servidor MySQL hospedado en RDS de Amazon (el "origen" para la replicación de datos de entrada), siga estos pasos.

1. Confirme que el registro binario está habilitado en el servidor de RDS de Amazon para MySQL de origen; para ello, compruebe que las copias de seguridad automatizadas están habilitadas o asegúrese de que existe una réplica de lectura para el servidor de RDS de Amazon para MySQL de origen.

2. Asegúrese de que los archivos de registro binarios del servidor de origen se conservan hasta que se hayan aplicado los cambios en la instancia de destino de Azure Database for MySQL.

    Con la replicación de datos de entrada, Azure Database for MySQL no administra el proceso de replicación.

3. Para comprobar la retención de registros binarios en el servidor de RDS de Amazon de origen a fin de determinar el número de horas que se conservan, llame al procedimiento almacenado "mysql.rds_show_configuration":

    ```
    mysql> call mysql.rds_show_configuration;
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | name                   | value | description                                                                                               |
    +------------------------+-------+-----------------------------------------------------------------------------------------------------------+
    | binlog retention hours | 24    | binlog retention hours specifies the duration in hours before binary logs are automatically deleted.      |
    | source delay           | 0     | source delay specifies replication delay in seconds between current instance and its master.              |
    | target delay           | 0     | target delay specifies replication delay in seconds between current instance and its future read-replica. |
    +------------------------+-------            +-----------------------------------------------------------------------------------------------------------+
    3 rows in set (0.00 sec)
    ```
4. Para configurar el período de retención de registros binarios, ejecute el procedimiento almacenado `rds_set_configuration` a fin de asegurarse de que los registros binarios se conservan en el servidor de origen durante el período de tiempo deseado, por ejemplo:

    ```
    Mysql> Call mysql.rds_set_configuration(‘binlog retention hours’, 96);
    ```

    Esto resulta útil si va a crear un volcado y, después, restaurarlo, para ponerse al día rápidamente con los cambios diferenciales.

   > [!NOTE]
   > Asegúrese de que haya suficiente espacio en disco para almacenar los registros binarios en el servidor de origen, en función del período de retención definido.

Hay dos maneras de realizar un volcado de datos desde el servidor de RDS de Amazon para MySQL de origen. Un enfoque implica realizar un volcado de datos directamente desde el servidor de origen, mientras que el otro implica capturar un volcado de memoria de una réplica de lectura de RDS de Amazon para MySQL.

- Si va a capturar un volcado de datos directamente desde el servidor de origen, siga estos pasos:

    1. Asegúrese de detener las escrituras de la aplicación durante unos minutos para obtener un volcado de datos transaccionalmente coherente.

       También puede establecer temporalmente el parámetro `read_only` en un valor de **1** para que las escrituras no se procesen al capturar un volcado de datos.

    2. Después de detener las escrituras en el servidor de origen, recopile el nombre del archivo de registro binario y el desplazamiento mediante la ejecución del comando `Mysql> Show master status;`.
    3. Guarde estos valores para iniciar la replicación desde el servidor de Azure Database for MySQL.
    4. Para crear un volcado de los datos, ejecute mysqldump y, después, el comando siguiente:

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

- Si no es posible detener las escrituras en el servidor de origen o el rendimiento del volcado de datos no es aceptable en el servidor de origen, capture un volcado en un servidor de réplica mediante los pasos siguientes:

    1. Cree una réplica de lectura de Amazon MySQL con la misma configuración que el servidor de origen y, después, cree el volcado allí.
    2. Deje que la réplica de lectura de RDS de Amazon para MySQL se ponga al día con el servidor de RDS de Amazon para MySQL de origen.
    3. Cuando el retraso de réplica alcance **0** en la réplica de lectura, llame al procedimiento almacenado `mysql.rds_stop_replication` para detener la replicación.

        ```
        Mysql> call mysql.rds_stop_replication;
        ```

    4. Una vez que se detenga la replicación, conéctese a la réplica y, después, ejecute el comando `SHOW SLAVE STATUS` para recuperar el nombre del archivo de registro binario actual del campo **Relay_Master_Log_File** y la posición del archivo de registro del campo **Exec_Master_Log_Pos**.
    5. Guarde estos valores para iniciar la replicación desde el servidor de Azure Database for MySQL.
    6. Para crear un volcado de los datos desde la réplica de lectura de RDS de Amazon para MySQL, ejecute mysqldump mediante la ejecución del comando siguiente:

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

    > [!NOTE]
    > También puede usar mydumper para realizar un volcado en paralelo de los datos de la base de datos de RDS de Amazon para MySQL de origen. Para obtener más información, vea [Migración de una base de datos grande a Azure Database for MySQL mediante mydumper/myloader](/azure/mysql/concepts-migrate-mydumper-myloader).

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Vinculación de los servidores de origen y de réplica para iniciar la replicación de datos de entrada

1. Para restaurar la base de datos mediante la restauración nativa de mysql, ejecute el comando siguiente:

    ```
    $ mysql -h <target_server> -u <targetuser> -p < dumpname.sql
    ```

   > [!NOTE]
   > Si en su lugar usa myloader, vea [Migración de una base de datos grande a Azure Database for MySQL mediante mydumper/myloader](/azure/mysql/concepts-migrate-mydumper-myloader).

2. Inicie sesión en el servidor de RDS de Amazon para MySQL de origen, configure un usuario de replicación y, después, conceda los privilegios necesarios a este usuario.

    - Si usa SSL, ejecute los comandos siguientes:

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%' REQUIRE SSL; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    - Si no usa SSL, ejecute el siguiente comando:

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%'; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    Todas las funciones de la replicación de datos de entrada se realizan mediante los procedimientos almacenados. Para obtener información sobre todos los procedimientos, vea [Procedimientos almacenados de replicación de datos de entrada](/azure/mysql/reference-stored-procedures#data-in-replication-stored-procedures). Puede ejecutar estos procedimientos almacenados en el shell de MySQL o en MySQL Workbench.

3. Para vincular el servidor de origen de RDS de Amazon para MySQL y el servidor de destino de Azure Database for MySQL, inicie sesión en el servidor de Azure Database for MySQL de destino y establezca el servidor de RDS de Amazon para MySQL como servidor de origen mediante la ejecución del comando siguiente:

    ```
    CALL mysql.az_replication_change_master('source_server','replication_user_name','replication_user_password',3306,'<master_bin_log_file>',master_bin_log_position,'<master_ssl_ca>');
    ```

4. Para iniciar la replicación entre el servidor de RDS de Amazon para MySQL de origen y el servidor de Azure Database for MySQL de destino, ejecute el comando siguiente:

    ```
    Mysql> CALL mysql.az_replication_start;
    ```

5. Para comprobar el estado de replicación, ejecute el comando siguiente en el servidor de réplica:

    ```
    Mysql> show slave status\G
    ```

    Si el estado de los parámetros `Slave_IO_Running` y `Slave_SQL_Running` es **Yes** (Sí), la replicación se ha iniciado y se encuentra en estado de ejecución.

6. Compruebe el valor del parámetro `Seconds_Behind_Master` para determinar el retraso del servidor de destino.

    Si el valor es **0**, el destino ha procesado todas las actualizaciones del servidor de origen. Si el valor es distinto de **0**, el servidor de destino sigue procesando las actualizaciones.

## <a name="ensure-a-successful-cutover"></a>Garantía de migración total correcta

Para asegurarse de que la migración total se realiza correctamente, realice las siguientes tareas:

1. Configure los inicios de sesión y los permisos de nivel de base de datos adecuados en el servidor de Azure Database for MySQL de destino.
2. Detenga las escrituras en el servidor de RDS de Amazon para MySQL de origen.
3. Asegúrese de que el servidor de Azure Database for MySQL de destino se ha puesto al día con el servidor de origen y que el valor `Seconds_Behind_Master` es **0** desde `show slave status`.
4. Llame al procedimiento almacenado `mysql.az_replication_stop` para detener la replicación, ya que todos los cambios se han replicado en el servidor de Azure Database for MySQL de destino.
5. Llame a `mysql.az_replication_remove_master` para eliminar la configuración de la replicación de datos de entrada.
6. Redirija los clientes y las aplicaciones cliente al servidor de Azure Database for MySQL de destino.

En este momento, la migración se ha completado y las aplicaciones están conectadas al servidor que ejecuta Azure Database for MySQL.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo migrar bases de datos a Azure Database for MySQL, consulte la [Guía de migración de base de datos](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Vea el vídeo sobre [migración fácil de aplicaciones de MySQL/PostgreSQL a Azure Managed Service](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201), que contiene una demostración que muestra cómo migrar aplicaciones de MySQL a Azure Database for MySQL.
