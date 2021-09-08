---
title: 'Migración de una instancia local de MySQL a Azure Database for MySQL: migración de datos'
description: Como paso prudente antes de actualizar o migrar datos, exporte la base de datos mediante MySQL Workbench o manualmente mediante el comando mysqldump.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/21/2021
ms.openlocfilehash: 9e53243424f4cb0b289c0ebba9c82cb9fb4853cc
ms.sourcegitcommit: 8b38eff08c8743a095635a1765c9c44358340aa8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113084953"
---
# <a name="migrate-mysql-on-premises-to-azure-database-for-mysql-data-migration"></a>Migración de una instancia local de MySQL a Azure Database for MySQL: migración de datos

[!INCLUDE[applies-to-mysql-single-flexible-server](../../includes/applies-to-mysql-single-flexible-server.md)]

## <a name="prerequisites"></a>Prerrequisitos

[Líneas de base de rendimiento](07-performance-baselines.md)

## <a name="back-up-the-database"></a>Copia de seguridad de la base de datos

Como paso prudente antes de actualizar o migrar datos, exporte la base de datos mediante MySQL Workbench o manualmente mediante el comando `mysqldump`.

## <a name="offline-vs-online"></a>Sin conexión frente a en línea

Antes de seleccionar una herramienta de migración, se debe determinar si la migración debe realizarse en línea o sin conexión.

  - Las **migraciones sin conexión** hacen que el sistema esté fuera de servicio mientras se realiza la migración. Esta opción garantiza que no se produzca ninguna transacción y que el estado de los datos sea exactamente el esperado cuando se restaure en Azure.

  - Las **migraciones en línea** migran los datos casi en tiempo real. Esta opción es adecuada cuando hay poco tiempo de inactividad para los usuarios o aplicaciones que consumen la carga de trabajo de datos. El proceso implica la replicación de los datos mediante un método de replicación como `binlog` o similar.

En el caso de WWI, su entorno tiene algunos requisitos complejos de redes y seguridad que no permiten que se apliquen los cambios adecuados para la conectividad entrante y saliente en el período de tiempo de migración de destino. Básicamente, estas complejidades y requisitos excluyen el enfoque en línea de ser tomado en cuenta.

> [!NOTE]
> Revise las secciones de planeación y evaluación para más detalles sobre la migración sin conexión frente a la migración en línea.

## <a name="data-drift"></a>Desfase de datos

Las estrategias de migración sin conexión pueden producir un desfase de datos. El desfase de datos se produce cuando los datos de origen recién modificados no están sincronizados con los datos migrados. Cuando sucede esto, se necesita una exportación completa o una exportación diferencial. Puede mitigar este problema si detiene todo el tráfico a la base de datos y, a continuación, realiza la exportación. Si no es posible detener todo el tráfico de modificación de datos, debe tener en cuenta el desfase.

Determinar los cambios puede complicarse si las tablas de base de datos no tienen columnas, como las claves principales basadas en valores numéricos, o algún tipo de fecha de modificación y creación en todas las tablas que deben migrarse.

Por ejemplo, si hay una clave principal basada en valores numéricos y la migración se importa con criterio de ordenación, es relativamente sencillo determinar dónde se detuvo la importación y reiniciarla desde esa posición. Si no hay ninguna clave basada en valores numéricos, podría ser posible usar la fecha de modificación y creación y, de nuevo, importar de forma ordenada para poder reiniciar la migración desde la última fecha detectada en el destino.

## <a name="performance-recommendations"></a>Recomendaciones de rendimiento

### <a name="exporting"></a>Exportación

  - Use una herramienta de exportación que se pueda ejecutar en un modo multiproceso, como mydumper.

  - Al usar MySQL 8.0, use [tablas con particiones](https://dev.mysql.com/doc/refman/8.0/en/partitioning-overview.html) cuando sea necesario para aumentar la velocidad de las exportaciones.

### <a name="importing"></a>Importación

  - Cree los índices agrupados y las claves principales antes de cargar los datos. Cargue los datos en el orden de la clave principal u otro si la clave principal contiene alguna columna de fecha (por ejemplo, la fecha de modificación o la fecha de creación) con criterio de ordenación.

  - Retrase la creación de índices secundarios hasta después de que se hayan cargado los datos. Cree todos los índices secundarios después de la carga.

  - Deshabilite las restricciones de las claves externas antes de la carga. El hecho de deshabilitar las comprobaciones de las claves externas favorece un aumento significativo del rendimiento. Habilite las restricciones y compruebe los datos después de la carga para garantizar la integridad referencial.

  - Cargue los datos en paralelo. Evite demasiado paralelismo que podría provocar la contención de recursos, y supervise los recursos mediante las métricas disponibles en Azure Portal.

## <a name="perform-the-migration"></a>Realización de la migración

  - Copia de seguridad de la base de datos

  - Creación y comprobación de la zona de aterrizaje de Azure

  - Configuración de parámetros del servidor de origen

  - Configuración de parámetros del servidor de destino

  - Exportación de los objetos de base de datos (esquema, usuarios, etc.)

  - Exportar los datos

  - Importación de los objetos de base de datos

  - Importación de los datos

  - Validación

  - Restablecimiento de parámetros del servidor de destino

  - Migración de las aplicaciones

## <a name="common-steps"></a>Pasos comunes

A pesar de la ruta de acceso que se siga, deben realizarse algunos pasos comunes:

  - Actualización a una versión compatible de Azure MySQL

  - Inventario de los objetos de base de datos

  - Exportación de usuarios y permisos

## <a name="migrate-to-latest-mysql-version"></a>Migración a la versión más reciente de MySQL

Puesto que se ejecuta la versión 5.5 de la base de datos de WWI Conference, es necesario realizar una actualización. El CIO ha pedido que actualicen a la versión más reciente de MySQL (actualmente 8.0).

Hay dos formas de actualizar a 8.0:

  - In situ

  - Exportación e importación

Al decidir realizar una actualización, es importante que ejecute la herramienta de **comprobación de actualizaciones** para determinar si hay conflictos. Por ejemplo, al actualizar a MySQL Server 8.0, la herramienta comprueba los conflictos siguientes:

  - Nombres de objetos de base de datos que entren en conflicto con palabras reservadas en MySQL 8.0

  - Uso del juego de caracteres utf8mb3

  - Uso de los atributos de tipo ZEROFILL/mostrar longitud

  - Nombres de tabla que entran en conflicto con las tablas de la versión 8.0

  - Uso de tipo temporal

  - Nombres de restricciones de clave externa de más de 64 caracteres

Si la comprobación de actualizaciones no informa de ningún problema, es seguro realizar una actualización local reemplazando los archivos binarios de MySQL. Las bases de datos con problemas tienen que exportarse y se deben solucionar los problemas.

## <a name="wwi-scenario"></a>Escenario de WWI

Después de migrar correctamente la instancia de MySQL a la versión 8.0, el equipo de migración de WWI se dio cuenta de que la ruta de migración original de [Database Migration Service (DMS)](https://datamigration.microsoft.com/scenario/mysql-to-azuremysql?step=1) ya no se podía usar, ya que la herramienta DMS solo admite actualmente las versiones 5.6 y 5.7. DMS requería acceso a la red. El equipo de migración de WWI no estaba listo para solucionar sus incidencias complejas de red. Estas incidencias de entorno limitaron la elección de la herramienta de migración a MySQL Workbench.

## <a name="database-objects"></a>Objetos de base de datos

Como se describe en la sección Test Plans, se debe realizar un inventario de los objetos de base de datos antes y después de la migración para asegurarse de que ha migrado todo.

Si quiere ejecutar un procedimiento almacenado para generar esta información, puede usar algo similar a lo siguiente:

```
DELIMITER // 
CREATE PROCEDURE `Migration_PerformInventory`(IN schemaName CHAR(64)) 
BEGIN 

        DECLARE finished INTEGER DEFAULT 0; 
          DECLARE tableName varchar(100) DEFAULT ""; 

        #get all tables 
            DECLARE curTableNames 
                CURSOR FOR 
                    SELECT TABLE_NAME FROM information_schema.tables where TABL
E_SCHEMA = schemaName; 
        
            -- declare NOT FOUND handler 
            DECLARE CONTINUE HANDLER 
                FOR NOT FOUND SET finished = 1; 
        
            DROP TABLE IF EXISTS MIG_INVENTORY; 

                CREATE TABLE MIG_INVENTORY 
                ( 
                      REPORT_TYPE VARCHAR(1000), 
                      OBJECT_NAME VARCHAR(1000), 
                  PARENT_OBJECT_NAME VARCHAR (1000), 
                      OBJECT_TYPE VARCHAR(1000), 
                      COUNT INT
                ) 
                ROW_FORMAT=DYNAMIC, 
                ENGINE='InnoDB';
              INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT
                     'OBJECTCOUNT', 'TABLES', 'TABLES', COUNT(*)
              FROM 
                     information_schema.tables 
                where 
                     TABLE_SCHEMA = schemaName;
                #### Constraints
              INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'STATISTICS', 'STATISTICS', COUNT(*) 
                FROM 
                      information_schema.STATISTICS 
                WHERE 
                      TABLE_SCHEMA = schemaName; 
                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'VIEWS', 'VIEWS', COUNT(*) 
                FROM 
                      information_schema.VIEWS 
                WHERE 
                      ROUTINE_TYPE = 'FUNCTION' and 
                      ROUTINE_SCHEMA = schemaName;

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                      'OBJECTCOUNT', 'PROCEDURES', 'PROCEDURES', COUNT(*) 
                FROM 
                      information_schema.ROUTINES 
                WHERE 
                      ROUTINE_TYPE = 'PROCEDURE' and 
                      ROUTINE_SCHEMA = schemaName; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                       'OBJECTCOUNT', 'EVENTS', 'EVENTS', COUNT(*) 
                FROM 
                       information_schema.EVENTS 
                WHERE 
                       EVENT_SCHEMA = schemaName; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                       'OBJECTCOUNT', 'USER DEFINED FUNCTIONS', 'USER DEFINED FUNCTIONS'
        , COUNT(*) 
                FROM 
                        mysql.func; 

                INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
                SELECT 
                        'OBJECTCOUNT', 'USERS', 'USERS', COUNT(*) 
                FROM 
                        mysql.user 
                WHERE 
                        user <> '' order by user; 

                OPEN curTableNames; 
        
                getTableName: LOOP 
                        FETCH curTableNames INTO tableName; 
                        IF finished = 1 THEN 
                              LEAVE getTableName; 
                        END IF; 

                   SET @s = CONCAT('SELECT COUNT(*) into @TableCount FROM ', schemaName, 
'.', tableName); 
        #SELECT @s; 
            PREPARE stmt FROM @s; 
        EXECUTE stmt;
        INSERT INTO MIG_INVENTORY (REPORT_TYPE,OBJECT_NAME, OBJECT_TYPE, COUNT) 
            
                SELECT 
                    'TABLECOUNT', tableName, 'TABLECOUNT', @TableCount; 
        DEALLOCATE PREPARE stmt; 
        
     END LOOP getTableName; 
     CLOSE curTableNames; 
          
   SELECT * FROM MIG_INVENTORY; 
END // 

DELIMITER ; 

CALL Migration_PerformInventory('reg_app');
```

  - La llamada a este procedimiento en la base de datos de origen revela lo siguiente (salida truncada):

![salida truncada](./media/image4.jpg)

  - El resultado del procedimiento de la base de datos de destino debe ser similar a la imagen siguiente después de completar la migración. Observe que no hay ninguna función en la base de datos. Las funciones se eliminaron antes de la migración.

![Funciones de base de datos](./media/image5.jpg)

## <a name="users-and-permissions"></a>Usuarios y permisos

Una migración correcta requiere migrar los usuarios y permisos asociados al entorno de destino.

Exporte todos los usuarios y sus concesiones mediante el siguiente script de PowerShell:

```
$username = "yourusername"; 
$password = "yourpassword"; 
mysql -u$username -p$password --skip-column-names -A -e"SELECT CONCAT('SHOW G
RANTS FOR ''',user,'''@''',host,''';') FROM mysql.user WHERE user<>''" > Show
Grants.sql; 

$lines = get-content "ShowGrants.sql" 

foreach ($line in $lines) 
{ 
mysql -u$username -p$password --skip-column-names -A -e"$line" >> AllGrants.sql 
}
```

  - Creación de un script de PowerShell con PowerShell ISE (consulte el documento de instalación)

  - Establezca **your username** en raíz y **your password** en la contraseña del usuario raíz

A continuación, ejecute el script `AllGrants.sql` destinado a la nueva instancia de Azure Database for MySQL:

```
$username = "yourusername"; 
$password = "yourpassword"; 
$server = "serverDNSname"; 
$lines = get-content "AllGrants.sql" 

foreach ($line in $lines)
{ 
mysql -u$username -p$password -h$server --ssl-ca=c:\temp\BaltimoreCyberTrus
tRoot.crt.cer --skip-column-names -A -e"$line" 
}
```

También puede crear usuarios en Azure Database for MySQL con PowerShell: /en-us/azure/mysql/howto-create-users

## <a name="execute-migration"></a>Ejecución de la migración

Ahora que tiene los componentes de migración básicos en su lugar, puede continuar con la migración de datos. Anteriormente se introdujeron varias herramientas y métodos. Para WWI, van a usar la ruta de acceso de MySQL Workbench para exportar los datos y, a continuación, importarlos a Azure Database for MySQL.

## <a name="data-migration-checklist"></a>Lista de comprobación para la migración de datos

  - Comprenda la complejidad del entorno y si es factible un enfoque en línea.

  - Tenga en cuenta el desfase de datos. La detención del servicio de base de datos puede eliminar el posible desfase de datos.

  - Configure los parámetros de origen para la exportación rápida.

  - Configure los parámetros de destino para la importación rápida.

  - Pruebe las migraciones que tengan una versión de origen diferente a la del destino.

  - Migre los objetos no basados en datos, como los nombres de usuario y los privilegios.

  - Asegúrese de que todas las tareas están documentadas y desactivadas a medida que se ejecuta la migración.  


## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Migración de datos con MySQL Workbench](./09-data-migration-with-mySQL-workbench.md)