---
title: Base de referencia de rendimiento de la guía para la migración de MySQL en el entorno local a Azure Database for MySQL
description: El conocimiento de la carga de trabajo de MySQL existente es una de las mejores inversiones que se pueden realizar para garantizar una migración correcta.
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
author: arunkumarthiags
ms.author: arthiaga
ms.reviewer: maghan
ms.custom: ''
ms.date: 06/11/2021
ms.openlocfilehash: 9948eef759ff44468c8a9b70e790c8376b40ff16
ms.sourcegitcommit: 3bb9f8cee51e3b9c711679b460ab7b7363a62e6b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/14/2021
ms.locfileid: "112082950"
---
# <a name="mysql-on-premises-to-azure-database-for-mysql-migration-guide-performance-baselines"></a>Base de referencia de rendimiento de la guía para la migración de MySQL en el entorno local a Azure Database for MySQL

## <a name="prerequisites"></a>Prerrequisitos

[Test Plans](06-test-plans.md)

## <a name="overview"></a>Información general

El conocimiento de la carga de trabajo de MySQL existente es una de las mejores inversiones que se pueden realizar para garantizar una migración correcta. Un rendimiento excelente del sistema depende del hardware adecuado y de un buen diseño de las aplicaciones. A los elementos como CPU, memoria, disco y redes se les debe ajustar el tamaño y configurar correctamente para la carga prevista. El hardware y la configuración forman parte de la ecuación de rendimiento del sistema. El desarrollador debe conocer la carga de consultas de la base de datos y las consultas más caras que se van a ejecutar. Centrarse en las consultas más caras puede marcar una diferencia considerable en las métricas de rendimiento generales.

La creación de una base de referencia de rendimiento de consultas es fundamental para un proyecto de migración. La base de referencia de rendimiento se puede usar para comprobar la configuración de la zona de aterrizaje de Azure para las cargas de trabajo de datos migradas. La mayoría de los sistemas se ejecutan de manera ininterrumpida y tienen horas de carga máxima diferentes. Es importante capturar las cargas de trabajo máximas para la base de referencia. Las métricas se capturan varias veces. Más adelante en el documento, se analizarán los parámetros del servidor de origen y cómo estos son fundamentales para tener una perspectiva general de la base de referencia de rendimiento. Los parámetros del servidor no deben pasarse por alto durante un proyecto de migración.

## <a name="tools"></a>Herramientas

A continuación se muestran las herramientas que se usan para recopilar métricas del servidor e información de la carga de trabajo de la base de datos. Use las métricas capturadas para determinar el nivel de servicio adecuado de Azure Database for MySQL y las opciones de escalado asociadas.

  - [MySQL Enterprise Monitor:](https://www.mysql.com/products/enterprise/monitor.html) esta herramienta de pago, en versión empresarial, puede proporcionar una lista ordenada de las consultas más caras, las métricas del servidor, la E/S de archivos e información sobre la topología.

  - [Percona Monitoring and Management (PMM)](https://www.percona.com/software/database-tools/percona-monitoring-and-management): una solución avanzada de supervisión de bases de datos de código abierto. Ayuda a reducir la complejidad, optimizar el rendimiento y mejorar la seguridad de los entornos de base de datos críticos para la empresa, independientemente de la ubicación implementada.

## <a name="server-parameters"></a>Parámetros del servidor

Es posible que las configuraciones predeterminadas del servidor MySQL no admitan adecuadamente una carga de trabajo. Hay una gran cantidad de parámetros de servidor en MySQL, pero en la mayoría de los casos el equipo de migración debe centrarse solo en unos pocos. Los parámetros siguientes se deben evaluar en los entornos de **origen** y de **destino**. Las configuraciones incorrectas pueden afectar a la velocidad de la migración. Volveremos a repasar estos parámetros cuando se ejecuten los pasos de migración.

  - **innodb\_buffer\_pool\_size**: un valor grande garantizará que los recursos en memoria se usen primero antes de usar la E/S de disco. Los valores típicos oscilarán entre el 80 y el 90 % de la memoria disponible. Por ejemplo, un sistema con 8 GB de memoria debe asignar entre 5 y 6 GB para el tamaño del grupo.

  - **innodb\_log\_file\_size**: los registros de la fase de puesta al día se utilizan para garantizar escrituras rápidas y duraderas. Esta copia de seguridad transaccional es útil durante un bloqueo del sistema. A partir de innodb\_log\_file\_size = 512 M (lo que da 1 GB de registros de la fase de puesta al día) hay mucho espacio para las escrituras. Las aplicaciones con uso intensivo de escritura que usan MySQL 5.6 o posterior deben comenzar por un tamaño de innodb\_log\_file\_size = 4 G.

  - **max\_connections**: este parámetro puede ayudar a mitigar el error `Too many connections`. El valor predeterminado es de 151 conexiones. Se prefiere usar un grupo de conexiones en el nivel de aplicación, pero es posible que también sea necesario aumentar la configuración de conexión del servidor.

  - **innodb\_file\_per\_table**: este valor le indicará a InnoDB si debe almacenar datos e índices en el espacio de tablas compartido o en un archivo separate.ibd para cada tabla. Tener un archivo por tabla permite que el servidor recupere espacio cuando se descarten, trunquen o se vuelvan a crear las tablas. Las bases de datos que contienen un gran número de tablas no deben usar la configuración de una tabla por archivo. A partir de MySQL 5.6, el valor predeterminado es ON. Las versiones anteriores de la base de datos deben establecer la configuración en ON antes de cargar los datos. Esta configuración solo afecta a las tablas recién creadas.

  - **innodb\_flush\_log\_at\_trx\_commit**: el valor predeterminado de 1 significa que InnoDB es totalmente compatible con ACID. Esta configuración de transacción de menor riesgo puede experimentar una sobrecarga significativa en los sistemas con discos lentos porque se necesitan fsyncs adicionales para vaciar cada cambio en los registros de la fase de puesta al día. Establecer el parámetro en 2 es un poco menos confiable porque las transacciones confirmadas se vaciarán en los registros de la fase de puesta al día solo una vez por segundo. El riesgo puede ser aceptable en algunas situaciones concretas y, definitivamente, resulta un buen valor para una réplica. Un valor de 0 permite un mejor rendimiento del sistema, pero es más probable que el servidor de bases de datos pierda algunos datos durante un error. En la línea inferior, use el valor 0 solo para una réplica.

  - **innodb\_flush\_method**: esta configuración controla cómo se vacían los datos y registros en el disco. Use `O_DIRECT` cuando esté en presencia de un controlador RAID de hardware con una memoria caché de escritura diferida con protección de batería. Use `fdatasync` (valor predeterminado) para otros escenarios.

  - **innodb\_log\_buffer\_size**: este valor es el tamaño del búfer para las transacciones que aún no se han confirmado. El valor predeterminado (1 MB) suele ser adecuado. Las transacciones con campos de blob o texto grandes pueden llenar el búfer rápidamente y desencadenar una carga de E/S adicional. Mire la variable de estado `Innodb_log_waits` y, si no es 0, aumente `innodb_log_buffer_size`.

  - **query\_cache\_size**: la caché de consulta es un cuello de botella conocido que se puede observar durante una etapa de simultaneidad moderada. El valor inicial debe establecerse en 0 para deshabilitar la caché. Por ejemplo: `query_cache_size = 0`. Este es el valor predeterminado en MySQL 5.6 y versiones posteriores.

  - **log\_bin**: este valor habilitará el registro binario. La habilitación del registro binario es obligatoria si el servidor va a actuar como maestro de replicación.

  - **server\_id**: este valor será un valor único para los servidores de identidad de las topologías de replicación.

  - **expire\_logs\_days**: este valor controlará cuántos días se purgarán automáticamente los registros binarios.

  - **skip\_name\_resolve**: usuario que va a realizar la resolución del nombre de host del cliente. Si el DNS es lento, la conexión será lenta. Al deshabilitar la resolución de nombres, las instrucciones GRANT solo deben usar direcciones IP. Las instrucciones GRANT realizadas anteriormente tendrían que volver a crearse para usar la dirección IP.

Ejecute el siguiente comando para exportar los parámetros del servidor a un archivo para su revisión. Con un análisis sencillo, la salida se puede usar para volver a aplicar los mismos parámetros de servidor después de la migración, si procede, al servidor de Azure Database for MySQL. Consulte [Configuración de parámetros del servidor en Azure Database for MySQL mediante Azure Portal](../../howto-server-parameters.md)

`mysql -u root -p -A -e "SHOW GLOBAL VARIABLES;" > settings.txt`

Los parámetros de servidor instalados predeterminados de MySQL 5.5.60 se pueden encontrar en el [apéndice](15-appendix.md#default-server-parameters-mysql-55-and-azure-database-for-mysql).

Antes de comenzar la migración, exporte los valores de configuración de MySQL de origen. Compare esos valores con la configuración de la instancia de la zona de aterrizaje de Azure después de la migración. Si se ha modificado algún valor predeterminado en la instancia de la zona de aterrizaje de Azure de destino, asegúrese de que se hayan establecido de nuevo después de la migración. Además, el usuario de migración debe comprobar que los parámetros del servidor se pueden establecer antes de la migración.

Para obtener una lista de parámetros de servidor que no se pueden configurar, consulte [Parámetros no configurables del servidor](../../concepts-server-parameters.md#non-configurable-server-parameters).

### <a name="egress-and-ingress"></a>Entrada y salida

Para cada herramienta y ruta de acceso de migración de datos correspondiente, los parámetros de servidor de MySQL de origen y destino se deben modificar para que admitan una salida y entrada lo más rápidas posibles. En función de la herramienta, los parámetros podrían ser diferentes. Por ejemplo, una herramienta que realiza una migración en paralelo puede necesitar más conexiones en el origen y el destino en comparación con una herramienta sencilla de subprocesos.

Revise los parámetros de tiempo de espera que puedan verse afectados por los conjuntos de datos. Entre ellas se incluyen las siguientes:

  - [connect\_timeout](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_connect_timeout)

  - [wait\_timeout](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_wait_timeout)

Además, revise los parámetros que afectarán a los valores máximos:

  - [max\_allowed\_packet](https://dev.mysql.com/doc/refman/8.0/en/server-system-variables.html#sysvar_max_allowed_packet)

> [!NOTE]
> Un error habitual en la migración es `MySQL server has gone away`. Los parámetros que se mencionan aquí son los típicos responsables de la resolución de este error.

## <a name="wwi-scenario"></a>Escenario de WWI

WWI ha revisado la carga de trabajo de la base de datos Conference y ha determinado que tiene una carga muy pequeña. Aunque un servidor de nivel básico funcionaría para ellos, no quieren realizar el trabajo más adelante de migrar a otro nivel de servicio. El servidor que se implemente hospedará finalmente las demás cargas de trabajo de datos de MySQL y, por tanto, han elegido el nivel `General Performance`.

Al revisar la base de datos de MySQL, el servidor de MySQL 5.5 se ejecuta con los parámetros del servidor predeterminados que se establecen durante la instalación inicial.

> [!div class="nextstepaction"]
> [Migración de datos](./08-data-migration.md)
