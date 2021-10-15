---
title: Migración de RDS de Amazon para MySQL a Azure Database for MySQL mediante Replicación de datos de entrada
description: En este artículo se describe cómo migrar RDS de Amazon para MySQL a Azure Database for MySQL mediante Replicación de datos de entrada.
author: SudheeshGH
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 09/24/2021
ms.openlocfilehash: 9d425ce352472755729b34b750a19ce0c3e48c0c
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362196"
---
# <a name="migrate-amazon-rds-for-mysql-to-azure-database-for-mysql-using-data-in-replication"></a>Migración de RDS de Amazon para MySQL a Azure Database for MySQL mediante Replicación de datos de entrada

[!INCLUDE[applies-to-mysql-single-flexible-server](includes/applies-to-mysql-single-flexible-server.md)]

> [!NOTE]
> Este artículo contiene referencias al término *esclavo*, un término que Microsoft ya no usa. Cuando se elimine el término del software, se eliminará también de este artículo.

Puede usar métodos como el volcado y la restauración de MySQL, exportación e importación de MySQL Workbench o Azure Database Migration Service para migrar las bases de datos MySQL a Azure Database for MySQL. Al usar una combinación de herramientas de código abierto como mysqldump o mydumper y myloader con Replicación de datos de entrada, puede migrar las cargas de trabajo con un tiempo de inactividad mínimo.

La Replicación de datos de entrada es una técnica que replica los cambios de datos del servidor de origen en el servidor de destino en función del método de posición del archivo de registro binario. En este escenario, la instancia de MySQL que funciona como origen (en la que se originan los cambios de la base de datos) escribe las actualizaciones y los cambios como *eventos* en el registro binario. La información del registro binario se almacena en diferentes formatos de registro según los cambios de la base de datos que se registran. Las réplicas están configuradas para leer el registro binario del origen y ejecutar los eventos del registro binario en la base de datos local de la réplica.

Si configura [Replicación de datos de entrada](../mysql/flexible-server/concepts-data-in-replication.md) para sincronizar datos desde un servidor MySQL de origen en uno de destino, puede realizar una migración selectiva de las aplicaciones desde la base de datos principal (o de origen) a la réplica (o base de datos de destino).

En este tutorial, aprenderá a configurar la Replicación de datos de entrada entre un servidor de origen en el que se ejecuta el servicio de base de datos relacional (RDS) de Amazon para MySQL y un servidor de destino en el que se ejecuta Azure Database for MySQL.

## <a name="performance-considerations"></a>Consideraciones de rendimiento

Antes de comenzar este tutorial, tenga en cuenta las implicaciones de rendimiento de la ubicación y la capacidad del equipo cliente que usará para realizar la operación.

### <a name="client-location"></a>Ubicación del cliente

Realice operaciones de volcado o restauración desde un equipo cliente que se inicia en la misma ubicación que el servidor de bases de datos:

- Para servidores de Azure Database for MySQL, la máquina cliente debe estar en la misma red virtual y zona de disponibilidad que el servidor de bases de datos de destino.
- Para las instancias de base de datos de RDS de Amazon de origen, la instancia de cliente debe existir en la misma nube privada virtual de Amazon y zona de disponibilidad que el servidor de bases de datos de origen.
En el caso anterior, puede mover archivos de volcado entre máquinas cliente mediante protocolos de transferencia de archivos (como FTP o SFTP), o cargarlos en Azure Blob Storage. Para reducir el tiempo total de migración, comprima los archivos antes de transferirlos.

### <a name="client-capacity"></a>Capacidad del cliente

Con independencia de dónde se encuentre el equipo cliente, necesita la capacidad de proceso, E/S y red adecuadas para realizar las operaciones solicitadas. Estas son las recomendaciones generales:

- Si el volcado o la restauración implican el procesamiento en tiempo real de datos, por ejemplo, compresión o descompresión, elija una clase de instancia con al menos un núcleo de CPU por subproceso de volcado o restauración.
- Asegúrese de que haya suficiente ancho de banda de red disponible para la instancia de cliente. Use tipos de instancia que admitan la característica de redes aceleradas. Para obtener más información, vea la sección "Redes aceleradas" de la [Guía de redes de máquinas virtuales de Azure](../virtual-network/create-vm-accelerated-networking-cli.md).
- Asegúrese de que la capa de almacenamiento de la máquina cliente proporciona la capacidad de lectura y escritura esperada. Se recomienda usar una máquina virtual de Azure con almacenamiento SSD prémium.

## <a name="prerequisites"></a>Requisitos previos

Para completar este tutorial, necesita:

- Instale [mysqlclient](https://dev.mysql.com/downloads/) en el equipo cliente para crear un volcado y realizar una operación de restauración en el servidor de Azure Database for MySQL de destino.
- Para bases de datos más grandes, instale [mydumper y myloader](https://centminmod.com/mydumper.html) para el volcado y la restauración en paralelo de bases de datos.

    > [!NOTE]
    > Mydumper solo se puede ejecutar en distribuciones de Linux. Para más información, consulte [Instalación de mydumper](https://github.com/maxbube/mydumper#how-to-install-mydumpermyloader).

- Cree una instancia del servidor de Azure Database for MySQL que ejecute la versión 5.7 u 8.0.

    > [!IMPORTANT]
    > Si el destino es Servidor flexible de Azure Database for MySQL con alta disponibilidad con redundancia de zona, tenga en cuenta que la Replicación de datos de entrada no se admite para esta configuración. Como solución alternativa, durante la creación del servidor, configure la alta disponibilidad con redundancia de zona:
    >
    > 1. Cree el servidor con la alta disponibilidad con redundancia de zona habilitada.
    > 1. Deshabilite la alta disponibilidad.
    > 1. Siga el artículo para configurar la Replicación de datos de entrada.
    > 1. Tras la migración total, elimine la configuración de la Replicación de datos de entrada.
    > 1. Habilite la alta disponibilidad.

Asegúrese de que varios parámetros y características están configurados correctamente, como se describe a continuación:

- Por motivos de compatibilidad, tenga los servidores de bases de datos de origen y de destino en la misma versión de MySQL.
- Cada tabla debe tener una clave principal. La falta de claves principales en las tablas puede ralentizar el proceso de replicación.
- Asegúrese de que el juego de caracteres de las bases de datos de origen y de destino sea el mismo.
- Establezca el parámetro `wait_timeout` en un tiempo razonable. El tiempo depende de la cantidad de datos o la carga de trabajo que quiera importar o migrar.
- Compruebe que todas las tablas usan InnoDB. El servidor de Azure Database for MySQL solo admite el motor de almacenamiento InnoDB.
- Para las tablas con muchos índices secundarios o para tablas de gran tamaño, los efectos de la sobrecarga de rendimiento son visibles durante la restauración. Modifique los archivos de volcado de memoria para que las instrucciones `CREATE TABLE` no incluyan definiciones de claves secundarias. Después de importar los datos, vuelva a crear índices secundarios para evitar la penalización del rendimiento durante el proceso de restauración.

Por último, a fin de prepararse para la Replicación de datos de entrada:

- Compruebe que el servidor de Azure Database for MySQL de destino se puede conectar al servidor de RDS de Amazon para MySQL de origen mediante el puerto 3306.
- Asegúrese de que el servidor de RDS de Amazon para MySQL de origen permite el tráfico entrante y saliente en el puerto 3306.
- Asegúrese de proporcionar [conectividad de sitio a sitio](../vpn-gateway/tutorial-site-to-site-portal.md) al servidor de origen mediante [Azure ExpressRoute](../expressroute/expressroute-introduction.md) o [Azure VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md). Para más información acerca de cómo crear una red virtual, consulte la [documentación de Azure Virtual Network](/azure/virtual-network/). Consulte también los artículos de inicio rápido con detalles paso a paso.
- Configure los grupos de seguridad de red del servidor de bases de datos de origen para permitir la dirección IP del servidor de Azure Database for MySQL de destino.

> [!IMPORTANT]
> Si la instancia de RDS de Amazon para MySQL de origen tiene GTID_mode establecido en ON, la instancia de destino de Servidor flexible de Azure Database for MySQL también debe tener GTID_mode establecido en ON.

## <a name="configure-the-target-instance-of-azure-database-for-mysql"></a>Configuración de la instancia de destino de Azure Database for MySQL

Para configurar la instancia de destino de Azure Database for MySQL, que es el destino para la Replicación de datos de entrada:

1. Establezca el valor del parámetro `max_allowed_packet` en el máximo de **1073741824**, que es 1 GB. Este valor evita cualquier problema de desbordamiento relacionado con filas largas.
1. Establezca los parámetros `slow_query_log`, `general_log`, `audit_log_enabled` y `query_store_capture_mode` en **OFF** durante la migración para ayudar a eliminar cualquier sobrecarga relacionada con el registro de consultas.
1. Escale verticalmente el tamaño de proceso del servidor de Azure Database for MySQL de destino hasta un máximo de 64 núcleos virtuales. Este tamaño proporciona más recursos de proceso al restaurar el volcado de base de datos desde el servidor de origen.

    Siempre puede volver a modificar la escala del proceso para satisfacer las demandas de la aplicación una vez que se complete la migración.

1. Escale verticalmente el tamaño de almacenamiento para obtener más IOPS durante la migración, o bien aumente el número máximo de IOPS para la migración.

   > [!NOTE]
   > Las IOPS máximas disponibles están determinadas por el tamaño de proceso. Para obtener más información, vea la sección IOPS en [Opciones de proceso y almacenamiento en Azure Database for MySQL - Servidor flexible](../mysql/flexible-server/concepts-compute-storage.md#iops).

## <a name="configure-the-source-amazon-rds-for-mysql-server"></a>Configuración del servidor de origen de RDS de Amazon para MySQL

Para preparar y configurar el servidor MySQL hospedado en RDS de Amazon, que es el *origen* para la Replicación de datos de entrada:

1. Confirme que el registro binario está habilitado en el servidor de RDS de Amazon para MySQL de origen. Compruebe que las copias de seguridad automatizadas están habilitadas o asegúrese de que existe una réplica de lectura para el servidor de RDS de Amazon para MySQL de origen.

1. Asegúrese de que los archivos de registro binarios del servidor de origen se conservan hasta que se hayan aplicado los cambios en la instancia de destino de Azure Database for MySQL.

    Con la Replicación de datos de entrada, Azure Database for MySQL no administra el proceso de replicación.

1. Para comprobar la retención de registros binarios en el servidor de RDS de Amazon de origen a fin de determinar el número de horas que se conservan, llame al procedimiento almacenado `mysql.rds_show_configuration`:

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
1. Para configurar el período de retención de registros binarios, ejecute el procedimiento almacenado `rds_set_configuration` a fin de asegurarse de que los registros binarios se conservan en el servidor de origen durante el período de tiempo deseado. Por ejemplo:

    ```
    Mysql> Call mysql.rds_set_configuration(‘binlog retention hours', 96);
    ```

    Si va a crear un volcado y, después, restaurarlo, el comando anterior le ayuda a ponerse al día rápidamente con los cambios diferenciales.

   > [!NOTE]
   > Asegúrese de que haya suficiente espacio en disco para almacenar los registros binarios en el servidor de origen en función del período de retención definido.

Hay dos maneras de capturar un volcado de datos desde el servidor de RDS de Amazon para MySQL de origen. Un enfoque implica capturar un volcado de datos directamente desde el servidor de origen. El otro enfoque implica capturar un volcado de memoria de una réplica de lectura de RDS de Amazon para MySQL.

- Para capturar un volcado de datos directamente desde el servidor de origen:

    1. Asegúrese de detener las escrituras de la aplicación durante unos minutos para obtener un volcado de datos transaccionalmente coherente.

       También puede establecer temporalmente el parámetro `read_only` en un valor de **1** para que las escrituras no se procesen al capturar un volcado de datos.

    1. Después de detener las escrituras en el servidor de origen, recopile el nombre del archivo de registro binario y el desplazamiento mediante la ejecución del comando `Mysql> Show master status;`.
    1. Guarde estos valores para iniciar la replicación desde el servidor de Azure Database for MySQL.
    1. Para crear un volcado de los datos, ejecute `mysqldump` y, después, el comando siguiente:

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

- Si no es posible detener las escrituras en el servidor de origen o el rendimiento del volcado de datos no es aceptable en el servidor de origen, capture un volcado en un servidor de réplica:

    1. Cree una réplica de lectura de Amazon MySQL con la misma configuración que el servidor de origen. A continuación, cree el volcado allí.
    1. Deje que la réplica de lectura de RDS de Amazon para MySQL se ponga al día con el servidor de RDS de Amazon para MySQL de origen.
    1. Cuando el retraso de réplica alcance **0** en la réplica de lectura, llame al procedimiento almacenado `mysql.rds_stop_replication` para detener la replicación.

        ```
        Mysql> call mysql.rds_stop_replication;
        ```

    1. Con la replicación detenida, conéctese a la réplica. Después, ejecute el comando `SHOW SLAVE STATUS` para recuperar el nombre del archivo de registro binario actual del campo **Relay_Master_Log_File** y la posición del archivo de registro del campo **Exec_Master_Log_Pos**.
    1. Guarde estos valores para iniciar la replicación desde el servidor de Azure Database for MySQL.
    1. Para crear un volcado de los datos desde la réplica de lectura de RDS de Amazon para MySQL, ejecute `mysqldump` mediante la ejecución del comando siguiente:

        ```
        $ mysqldump -h hostname -u username -p –single-transaction –databases dbnames –order-by-primary> dumpname.sql
        ```

    > [!NOTE]
    > También puede usar mydumper para capturar un volcado en paralelo de los datos de la base de datos de RDS de Amazon para MySQL de origen. Para obtener más información, vea [Migración de bases de datos grandes a Azure Database for MySQL mediante mydumper/myloader](../mysql/concepts-migrate-mydumper-myloader.md).

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Vinculación de los servidores de origen y de réplica para iniciar la Replicación de datos de entrada

1. Para restaurar la base de datos mediante la restauración nativa de mysql, ejecute el comando siguiente:

    ```
    $ mysql -h <target_server> -u <targetuser> -p < dumpname.sql
    ```

   > [!NOTE]
   > Si en su lugar usa myloader, vea [Migración de bases de datos grandes a Azure Database for MySQL mediante mydumper/myloader](../mysql/concepts-migrate-mydumper-myloader.md).

1. Inicie sesión en el servidor de RDS de Amazon para MySQL de origen y configure un usuario de replicación. A continuación, conceda los privilegios necesarios a este usuario.

    - Si usa SSL, ejecute los comandos siguientes:

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%' REQUIRE SSL; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    - Si no usa SSL, ejecute los comandos siguientes:

        ```
        Mysql> CREATE USER 'syncuser'@'%' IDENTIFIED BY 'userpassword';
        Mysql> GRANT REPLICATION SLAVE, REPLICATION CLIENT on *.* to 'syncuser'@'%'; 
        Mysql> SHOW GRANTS FOR syncuser@'%';
        ```

    Todas las funciones de la replicación de datos internos se realizan mediante los procedimientos almacenados. Para obtener información sobre todos los procedimientos, vea [Procedimientos almacenados de Replicación de datos de entrada](../mysql/reference-stored-procedures.md#data-in-replication-stored-procedures). Puede ejecutar estos procedimientos almacenados en el shell de MySQL o en MySQL Workbench.

1. Para vincular el servidor de RDS de Amazon for MySQL de origen y el servidor de Azure Database for MySQL de destino, inicie sesión en el servidor de Azure Database for MySQL de destino. Establezca el servidor de RDS de Amazon para MySQL como servidor de origen mediante la ejecución del siguiente comando:

    ```
    CALL mysql.az_replication_change_master('source_server','replication_user_name','replication_user_password',3306,'<master_bin_log_file>',master_bin_log_position,'<master_ssl_ca>');
    ```

1. Para iniciar la replicación entre el servidor de RDS de Amazon para MySQL de origen y el servidor de Azure Database for MySQL de destino, ejecute el comando siguiente:

    ```
    Mysql> CALL mysql.az_replication_start;
    ```

1. Para comprobar el estado de replicación, ejecute el comando siguiente en el servidor de réplica:

    ```
    Mysql> show slave status\G
    ```

    Si el estado de los parámetros `Slave_IO_Running` y `Slave_SQL_Running` es **Yes** (Sí), la replicación se ha iniciado y se encuentra en estado de ejecución.

1. Compruebe el valor del parámetro `Seconds_Behind_Master` para determinar el retraso del servidor de destino.

    Si el valor es **0**, el destino ha procesado todas las actualizaciones del servidor de origen. Si el valor es distinto de **0**, el servidor de destino sigue procesando las actualizaciones.

## <a name="ensure-a-successful-cutover"></a>Garantía de migración total correcta

Para asegurar una migración total correcta:

1. Configure los inicios de sesión y los permisos de nivel de base de datos adecuados en el servidor de Azure Database for MySQL de destino.
1. Detenga las escrituras en el servidor de RDS de Amazon para MySQL de origen.
1. Asegúrese de que el servidor de Azure Database for MySQL de destino se ha puesto al día con el servidor de origen y que el valor `Seconds_Behind_Master` es **0** desde `show slave status`.
1. Llame al procedimiento almacenado `mysql.az_replication_stop` para detener la replicación, ya que todos los cambios se han replicado en el servidor de Azure Database for MySQL de destino.
1. Llame a `mysql.az_replication_remove_master` para eliminar la configuración de la Replicación de datos de entrada.
1. Redirija los clientes y las aplicaciones cliente al servidor de Azure Database for MySQL de destino.

En este momento, la migración se ha completado. Las aplicaciones están conectadas al servidor que ejecuta Azure Database for MySQL.

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo migrar bases de datos a Azure Database for MySQL, consulte la [Guía de migración de base de datos](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide).
- Vea el vídeo [Migración fácil de aplicaciones de MySQL/PostgreSQL al servicio administrado de Azure](https://medius.studios.ms/Embed/Video/THR2201?sid=THR2201). Contiene una demostración que muestra cómo migrar aplicaciones de MySQL a Azure Database for MySQL.
