---
title: 'Réplicas de lectura: Azure Database for MySQL: Servidor flexible'
description: 'Obtenga información sobre las réplicas de lectura en el Servidor flexible de Azure Database for MySQL: creación de réplicas, conexión a réplicas, supervisión de la replicación y detención de la replicación.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 06/17/2021
ms.openlocfilehash: 601b3659cd23cbd12d96f4e73c24a3a4a6c3fe55
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602268"
---
# <a name="read-replicas-in-azure-database-for-mysql---flexible-server"></a>Réplicas de lectura en Azure Database for MySQL: Servidor flexible

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Réplicas de lectura en Azure Database for MySQL: Servidor flexible está en versión preliminar.

MySQL es uno de los motores de base de datos populares para ejecutar aplicaciones web y móviles a escala de Internet. Muchos de nuestros clientes lo usan para sus servicios de educación en línea, servicios de streaming de vídeo, soluciones de pago digital, plataformas de comercio electrónico, servicios de juegos, portales de noticias, gobierno y sitios web de atención sanitaria. Estos servicios son necesarios para servir y escalar a medida que aumenta el tráfico en la aplicación web o móvil.

En el caso de las aplicaciones, la aplicación se desarrolla normalmente en Java o PHP y se migra para ejecutarse en conjuntos de escalado de máquinas virtuales de Azure o en instancias de Azure App Services, o bien se incluye en contenedores para ejecutarse en Azure Kubernetes Service (AKS). Con el conjunto de escalado de máquinas virtuales, App Service o AKS como infraestructura subyacente, el escalado de aplicaciones se simplifica al aprovisionar de forma instantánea nuevas máquinas virtuales y replicar los componentes sin estado de las aplicaciones para satisfacer las solicitudes. Sin embargo, la base de datos a menudo acaba siendo un cuello de botella como componente con estado centralizado.

La característica de réplica de lectura permite replicar datos de Azure Database for MySQL con servidor flexible en un servidor de solo lectura. Puede crear hasta **10** réplicas desde el servidor de origen. Las réplicas se actualizan asincrónicamente mediante la tecnología de replicación basada en la posición de los archivos de registros binarios nativos (binlog) del motor de MySQL. Para obtener más información acerca de la replicación de binlog, consulte la [Introducción a la replicación de binlog de MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Las réplicas son nuevos servidores que se administran de forma similar a los servidores flexibles de Azure Database for MySQL de origen. Se producirán cargos de facturación por cada réplica de lectura en función del proceso aprovisionado en núcleos virtuales y el almacenamiento aprovisionado en GB/mes. Para más información, consulte los [precios](./concepts-compute-storage.md#pricing).

Para más información sobre los problemas y las características de replicación de MySQL, consulte la [documentación de replicación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Este artículo contiene referencias al término *esclavo*, un término que Microsoft ya no usa. Cuando se elimine el término del software, se eliminará también de este artículo.

## <a name="common-use-cases-for-read-replica"></a>Casos de uso habituales de las réplicas de lectura

La finalidad de la característica de réplica de lectura es ayudar a mejorar el rendimiento y la escala de las cargas de trabajo que conllevan un gran número de operaciones de lectura. Las cargas de trabajo de lectura se pueden aislar en las réplicas, mientras que las cargas de trabajo de escritura se pueden dirigir al origen.

Los escenarios comunes son:

* Escalar las cargas de trabajo de lectura procedentes de la aplicación mediante un proxy de conexión ligero, como [ProxySQL](https://aka.ms/ProxySQLLoadBalanceReplica), o usar el patrón basado en microservicios para escalar horizontalmente las consultas de lectura procedentes de la aplicación para las réplicas de lectura.
* Las cargas de trabajo de informes analíticos o de BI pueden usar réplicas de lectura como origen de datos para la creación de informes.
* Para un escenario de IoT o de fabricación en el que la información de telemetría se ingiere en el motor de base de datos MySQL mientras se usan varias réplicas de lectura para la creación de informes de datos.

Dado que las réplicas son de solo lectura, no reducen directamente las cargas de capacidad de escritura en el origen. Esta característica no está destinada a cargas de trabajo intensivas de escritura.

Esta característica de réplica de lectura utiliza la replicación asincrónica de MySQL. La característica no está diseñada para escenarios de replicación sincrónica. Habrá un retraso medible entre el origen y la réplica. Los datos de la réplica se vuelven finalmente coherentes con los datos del origen. Use esta característica con cargas de trabajo que puedan admitir este retraso.

## <a name="create-a-replica"></a>Creación de una réplica

Si un servidor de origen no tiene ningún servidor de réplica existente, el origen se reiniciará primero a fin de prepararse para la replicación.

Cuando se inicia el flujo de trabajo de creación de la réplica, se crea un servidor Azure Database for MySQL en blanco. El nuevo servidor se rellena con los datos que estaban en el servidor de origen. El tiempo de creación depende de la cantidad de datos en el origen y del tiempo transcurrido desde la última copia de seguridad completa semanal. Puede oscilar desde unos minutos hasta varias horas.

> [!NOTE]
> Las réplicas de lectura se crean con la misma configuración de servidor que el origen. Una vez creado, se puede cambiar la configuración del servidor de réplica. El servidor de réplica siempre se crea en el mismo grupo de recursos, en la misma ubicación y en la misma suscripción que el servidor de origen. Si desea crear un servidor réplica en otro grupo de recursos o en una suscripción diferente, puede [mover el servidor réplica](../../azure-resource-manager/management/move-resource-group-and-subscription.md) después de la creación. Se recomienda mantener la configuración del servidor de réplica con valores iguales o mayores que el de origen para asegurarse de que la réplica funciona al mismo nivel que el servidor de origen.

Aprenda a [crear una réplica de lectura en Azure Portal](how-to-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conexión a una réplica

Durante la creación, una réplica hereda el método de conectividad del servidor de origen. Y no se puede cambiar el método de conectividad de la réplica. Por ejemplo, si el servidor de origen tiene **Acceso privado (integración con red virtual)** , la réplica no puede estar en **Acceso público (direcciones IP permitidas)** .

La réplica hereda la cuenta de administrador del servidor de origen. Todas las cuentas de usuario del servidor de origen se replican en las réplicas de lectura. Solo se puede conectar a una réplica de lectura utilizando las cuentas de usuario disponibles en el servidor de origen.

Puede conectarse a la réplica mediante su nombre de host y una cuenta de usuario válida, igual que haría en un servidor flexible de Azure Database for MySQL normal. En un servidor denominado **myreplica** con el nombre de usuario administrador **myadmin**, puede conectarse a la réplica mediante la CLI de mysql:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin -p
```

Cuando se le solicite, escriba la contraseña de la cuenta de usuario.

## <a name="monitor-replication"></a>Supervisión de la replicación

El servidor flexible de Azure Database for MySQL proporciona la métrica de **retraso de replicación en segundos** en Azure Monitor. Esta métrica está disponible únicamente para las réplicas. Esta métrica se calcula utilizando la métrica `seconds_behind_master` disponible en el comando `SHOW SLAVE STATUS` de MySQL. Establezca una alerta que le informe cuando el retraso de replicación alcance un valor que no sea aceptable para la carga de trabajo.

Si ve un mayor retraso en la replicación, consulte [Solución de problemas de latencia de replicación](./../howto-troubleshoot-replication-latency.md) para solucionar problemas y comprender las causas posibles.

## <a name="stop-replication"></a>Detención replicación

Puede detener la replicación entre un servidor de origen y una réplica. Una vez que se ha detenido la replicación entre un servidor de origen y una réplica de lectura, la réplica se convierte en un servidor independiente. Los datos del servidor independiente son los datos que estaban disponibles en la réplica en el momento en que se inició el comando para detener la replicación. El servidor independiente no alcanza al servidor de origen.

Si decide detener la replicación en una réplica, perderá todos los vínculos con su servidor de origen anterior y otras réplicas. Recuerde que no se produce ninguna conmutación por error automatizada entre un servidor de origen y su réplica.

> [!IMPORTANT]
>Este servidor independiente no puede volver a convertirse en una réplica.
> Asegúrese de que la réplica tenga todos los datos que necesita antes de detener la replicación en una réplica de lectura.

Aprenda a [detener la replicación en una réplica](how-to-read-replicas-portal.md).

## <a name="failover"></a>Conmutación por error

No se produce ninguna conmutación por error automatizada entre el servidor de origen y la réplica.

Las réplicas de lectura están pensadas para escalar cargas de trabajo intensivas de lectura y no están diseñadas para satisfacer las necesidades de alta disponibilidad de un servidor. No se produce ninguna conmutación por error automatizada entre el servidor de origen y la réplica. Detener la replicación en la réplica de lectura para ponerla en línea en el modo de lectura y escritura es el medio por el que se realiza esta conmutación por error manual.

Dado que la replicación es asincrónica, se produce un retraso entre el origen y la réplica. La cantidad de desfase puede verse afectada por muchos factores, como lo pesada que sea la carga de trabajo que se ejecuta en el servidor de origen y la latencia entre los centros de datos. En la mayoría de los casos, el retraso de la réplica oscila entre unos segundos y un par de minutos. Puede realizar un seguimiento del retraso real de la replicación mediante la métrica *Replica Lag* (Retraso de réplica), que está disponible para cada réplica. Esta métrica muestra el tiempo desde la última transacción reproducida. Se recomienda que observe el retraso de la réplica durante un período de tiempo para identificar el retraso medio. Puede establecer una alerta sobre el retraso de la réplica, para que si se sale del intervalo esperado, puede tomar medidas.

> [!Tip]
> Si realiza la conmutación por error a la réplica, el retraso en el momento de desvincular la réplica del origen indicará la cantidad de datos perdidos.

Después de haber decidido que quiere conmutar por error a una réplica, realice estos pasos:

1. Detenga la replicación en la réplica.<br/>
   Este paso es necesario para que el servidor de la réplica pueda aceptar escrituras. Como parte de este proceso, el servidor de réplica se desvinculará del origen. Una vez iniciada la detención de la replicación, el proceso de back-end suele tardar aproximadamente dos minutos en completarse. Consulte la sección [Detención de la replicación](#stop-replication) de este artículo para conocer las implicaciones de esta acción.

2. Haga que la replicación apunte a la réplica (anterior).<br/>
   Cada servidor tiene una cadena de conexión única. Actualice la aplicación para que apunte a la réplica (anterior) en lugar de al origen.

Una vez que la aplicación procesa correctamente las lecturas y las escrituras, ya está completa la conmutación por error. La cantidad de tiempo de inactividad que experimente su aplicación dependerá del momento en que se detecte una incidencia y se realicen los pasos 1 y 2 anteriores.

## <a name="global-transaction-identifier-gtid"></a>Identificador de transacción global (GTID)

El identificador de transacción global (GTID) es un identificador único que se crea con cada transacción confirmada en un servidor de origen y que está desactivado de forma predeterminada en el servidor flexible de Azure Database for MySQL. GTID es compatible con las versiones 5.7 y 8.0. Para más información sobre GTID y sobre cómo se usa en la replicación, consulte la documentación de la [replicación con GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) de MySQL.

Los siguientes parámetros de servidor se pueden usar para configurar GTID: 

|**Parámetros de servidor**|**Descripción**|**Valor predeterminado**|**Valores**|
|--|--|--|--|
|`gtid_mode`|Indica si se usan GTID para identificar transacciones. Los cambios de modo se deben realizar exclusivamente paso a paso y en orden ascendente (p. ej., `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF*`|`OFF`: Tanto las transacciones nuevas como las de replicación deben ser anónimas <br> `OFF_PERMISSIVE`: las transacciones nuevas son anónimas. Las transacciones replicadas pueden ser transacciones de GTID o anónimas. <br> `ON_PERMISSIVE`: las transacciones nuevas son transacciones de GTID. Las transacciones replicadas pueden ser transacciones de GTID o anónimas. <br> `ON`: tanto las transacciones nuevas como las replicadas deben ser transacciones de GTID.|
|`enforce_gtid_consistency`|Aplica la coherencia de GTID, ya que solo permite que se ejecuten las instrucciones que se pueden registrar de una manera transaccionalmente segura. Este valor debe establecerse en `ON` antes de habilitar la replicación de GTID. |`OFF*`|`OFF`: a todas las transacciones se les permite infringir la coherencia de GTID.  <br> `ON`: no se permite a ninguna transacción infringir la coherencia de GTID. <br> `WARN`: a todas las transacciones se les permite infringir la coherencia de GTID, pero se genera una advertencia. | 

**En el caso de los servidores flexibles de Azure Database for MySQL con la característica de alta disponibilidad habilitada, el valor predeterminado está establecido en `ON`* .
> [!NOTE]
>
> * Una vez habilitado GTID, no se puede volver a desactivar. Si necesita desactivar GTID, póngase en contacto con el servicio de soporte técnico. 
>
> * Si desea cambiar los GTID de un valor a otro, solo puede hacerlo un paso cada vez en orden ascendente de modos. Por ejemplo, si gtid_mode está establecido en OFF_PERMISSIVE, es posible cambiar a ON_PERMISSIVE, pero no a ON.
>
> * Para mantener la coherencia de la replicación, no se puede actualizar en el caso de un servidor maestro o de réplica.
>
> * Se recomienda establecer enforce_gtid_consistency en ON antes de establecer gtid_mode=ON.

Para habilitar GTID y configurar el comportamiento de la coherencia, actualice los parámetros de servidor `gtid_mode` y `enforce_gtid_consistency` mediante [Azure Portal](how-to-configure-server-parameters-portal.md) o la [CLI de Azure](how-to-configure-server-parameters-cli.md).

Si GTID está habilitado en un servidor de origen (`gtid_mode` = ON), las réplicas recién creadas también tendrán GTID habilitado y usarán la replicación de GTID. Para garantizar la coherencia de la replicación, `gtid_mode` no se puede cambiar una vez que se crean los servidores maestros o de réplica con GTID habilitado.

## <a name="considerations-and-limitations"></a>Consideraciones y limitaciones

| Escenario | Limitación/Consideración |
|:-|:-|
| Réplica en el servidor con alta disponibilidad con redundancia de zona habilitada | No compatible |
| Replicación de lectura entre regiones | No compatible |
| Precios | El costo de ejecutar el servidor de réplica se basa en la región en la que se ejecuta el servidor de réplica. |
| Reinicio del servidor de origen | Cuando se crea la réplica de origen que no tiene réplicas existentes, el origen se reiniciará primero a fin de prepararse para la replicación. Téngalo en cuenta y realice estas operaciones durante un período de poca actividad. |
| Nuevas réplicas | Las réplicas de lectura se crean como un nuevo servidor flexible de Azure Database for MySQL. Un servidor no puede volver a convertirse en una réplica. No se puede crear una réplica de otra réplica de lectura. |
| Configuración de réplicas | Las réplicas se crean con la misma configuración de servidor que el origen. Después de crear una réplica, se pueden cambiar varios valores independientemente del servidor de origen: generación de proceso, núcleos virtuales, almacenamiento y período de retención de copias de seguridad. El nivel de proceso también puede cambiarse de forma independiente.<br> <br> **IMPORTANTE**  <br> - Antes de actualizar la configuración de un servidor de origen con nuevos valores, actualice la configuración de las réplicas a valores iguales o mayores. Esta acción garantiza que la réplica puede hacer frente a los cambios realizados en el servidor de origen. <br/> El método de conectividad y la configuración de parámetros se heredan del servidor de origen y van a la réplica cuando esta se crea. Después, las reglas de la réplica son independientes. |
| Réplicas detenidas | Si detiene la replicación entre un servidor de origen y una réplica de lectura, la réplica detenida se convierte en un servidor independiente que acepta operaciones de lectura y escritura. Este servidor independiente no puede volver a convertirse en una réplica. |
| Servidores independientes y de origen eliminados | Al eliminar un servidor de origen, la replicación se detiene en todas las réplicas de lectura. Estas réplicas se convierten automáticamente en servidores independientes y pueden aceptar operaciones de lectura y de escritura. El propio servidor de origen se elimina. |
| Cuentas de usuario | Los usuarios del servidor de origen se replican en las réplicas de lectura. Solo se puede conectar a una réplica de lectura utilizando las cuentas de usuario disponibles en el servidor de origen. |
| Parámetros del servidor | Para evitar que los datos dejen de estar sincronizados y evitar posibles pérdidas o daños en los datos, se bloquean algunos parámetros del servidor para que no se actualicen mientras se usan réplicas de lectura. <br> Los siguientes parámetros de servidor están bloqueados tanto en el servidor de origen como en los de réplica:<br> - [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) <br> - [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators) <br> El parámetro [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) está bloqueado en los servidores de réplica. <br> Para actualizar uno de los parámetros anteriores en el servidor de origen, elimine los servidores de réplica, actualice el valor del parámetro en el origen y vuelva a crear las réplicas. 
<br> Al configurar parámetros de nivel de sesión como "foreign_keys_checks" en la réplica de lectura, asegúrese de que los valores de parámetro que se establecen en la réplica de lectura sean coherentes con los del servidor de origen.|
| Otros | - No permite crear réplicas de réplicas. <br> - Las tablas en memoria pueden provocar que las réplicas dejen de sincronizarse. Esto es una limitación de la tecnología de replicación de MySQL. Puede obtener más información en la [documentación de referencia de MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html). <br>- Asegúrese de que las tablas del servidor de origen tengan claves principales. La falta de claves principales puede generar una latencia en la replicación entre el origen y las réplicas.<br>- Consulte la lista completa de las limitaciones de la replicación de MySQL en la [documentación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html). |

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [crear y administrar réplicas de lectura mediante Azure Portal](how-to-read-replicas-portal.md).
* Obtenga información sobre cómo [crear y administrar réplicas de lectura mediante la CLI de Azure](how-to-read-replicas-cli.md).