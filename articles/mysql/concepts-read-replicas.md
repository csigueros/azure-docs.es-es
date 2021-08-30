---
title: 'Réplicas de lectura: Azure Database for MySQL'
description: 'Obtenga información sobre las réplicas de lectura en Azure Database for MySQL: elección de regiones, creación de réplicas, conexión a réplicas, supervisión de la replicación y detención de la replicación.'
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 06/17/2021
ms.custom: references_regions
ms.openlocfilehash: 89cb9122da21887165b2330f75dd316c184de823
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781076"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Réplicas de lectura en Azure Database for MySQL

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

La característica de réplica de lectura permite replicar datos de un servidor Azure Database for MySQL en un servidor de solo lectura. Puede crear hasta cinco réplicas desde el servidor de origen. Las réplicas se actualizan asincrónicamente mediante la tecnología de replicación basada en la posición de los archivos de registros binarios nativos (binlog) del motor de MySQL. Para obtener más información acerca de la replicación de binlog, consulte la [Introducción a la replicación de binlog de MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Las réplicas son nuevos servidores que se administran de forma similar a los servidores de Azure Database for MySQL normales. En cada réplica de lectura, se le cobra por el proceso aprovisionado en núcleos virtuales y el almacenamiento aprovisionado en GB/mes.

Para más información sobre los problemas y las características de replicación de MySQL, consulte la [documentación de replicación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

> [!NOTE]
> Este artículo contiene referencias al término _esclavo_, un término que Microsoft ya no usa. Cuando se elimine el término del software, se eliminará también de este artículo.
>

## <a name="when-to-use-a-read-replica"></a>Casos en los que utilizar las réplicas de lectura

La finalidad de la característica de réplica de lectura es ayudar a mejorar el rendimiento y la escala de las cargas de trabajo que conllevan un gran número de operaciones de lectura. Las cargas de trabajo de lectura se pueden aislar en las réplicas, mientras que las cargas de trabajo de escritura se pueden dirigir al origen.

Un caso habitual es que las cargas de trabajo de análisis e inteligencia empresarial utilicen la réplica de lectura como origen de datos para los informes.

Dado que las réplicas son de solo lectura, no reducen directamente las cargas de capacidad de escritura en el origen. Esta característica no está destinada a cargas de trabajo intensivas de escritura.

Esta característica de réplica de lectura utiliza la replicación asincrónica de MySQL. La característica no está diseñada para escenarios de replicación sincrónica. Habrá un retraso medible entre el origen y la réplica. Los datos de la réplica se vuelven finalmente coherentes con los datos del origen. Use esta característica con cargas de trabajo que puedan admitir este retraso.

## <a name="cross-region-replication"></a>Replicación entre regiones

Puede crear una réplica de lectura en una región distinta a la del servidor de origen. La replicación entre regiones puede ser útil para escenarios como el planeamiento de la recuperación ante desastres o la incorporación de datos más cerca de los usuarios.

Puede tener un servidor de origen en cualquier [región de Azure Database for MySQL](https://azure.microsoft.com/global-infrastructure/services/?products=mysql).  Un servidor de origen puede tener una réplica en su región emparejada o en las regiones de réplica universales. En la imagen siguiente se muestran las regiones de réplica disponibles en función de la región de origen.

[ :::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Regiones de réplica de lectura":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiones de réplica universal

Puede crear una réplica de lectura en cualquiera de las siguientes regiones, con independencia de dónde se encuentre el servidor de origen. Entre las regiones de réplica universales admitidas se incluyen:

| Region | Disponibilidad de la réplica | 
| --- | --- | 
| Este de Australia | :heavy_check_mark: | 
| Sudeste de Australia | :heavy_check_mark: | 
| Sur de Brasil | :heavy_check_mark: | 
| Centro de Canadá | :heavy_check_mark: |
| Este de Canadá | :heavy_check_mark: |
| Centro de EE. UU. | :heavy_check_mark: | 
| Este de EE. UU. | :heavy_check_mark: | 
| Este de EE. UU. 2 | :heavy_check_mark: |
| Este de Asia | :heavy_check_mark: | 
| Japón Oriental | :heavy_check_mark: | 
| Japón Occidental | :heavy_check_mark: | 
| Centro de Corea del Sur | :heavy_check_mark: |
| Corea del Sur | :heavy_check_mark: |
| Norte de Europa | :heavy_check_mark: | 
| Centro-Norte de EE. UU | :heavy_check_mark: | 
| Centro-sur de EE. UU. | :heavy_check_mark: | 
| Sudeste de Asia | :heavy_check_mark: | 
| Sur de Reino Unido | :heavy_check_mark: | 
| Oeste de Reino Unido | :heavy_check_mark: | 
| Centro-Oeste de EE. UU. | :heavy_check_mark: | 
| Oeste de EE. UU. | :heavy_check_mark: | 
| Oeste de EE. UU. 2 | :heavy_check_mark: | 
| Oeste de Europa | :heavy_check_mark: | 
| Centro de la India* | :heavy_check_mark: | 
| Centro de Francia* | :heavy_check_mark: | 
| Norte de Emiratos Árabes Unidos* | :heavy_check_mark: | 
| Norte de Sudáfrica* | :heavy_check_mark: |

> [!Note] 
> *Regiones donde Azure Database for MySQL tiene Almacenamiento de uso general v2 en la versión preliminar pública.  <br /> *Para estas regiones de Azure, tendrá la opción de crear un servidor en Almacenamiento de uso general v1 y v2. En el caso de los servidores creados con Almacenamiento de uso general v2 en versión preliminar pública, solo podrá crear un servidor de réplica solo en las regiones de Azure que admiten dicha versión.

### <a name="paired-regions"></a>Regiones emparejadas

Además de las regiones de réplica universales, puede crear una réplica de lectura en la región emparejada de Azure del servidor de origen. Si no conoce el par de la región, puede obtener más información en el [artículo sobre regiones emparejadas de Azure](../best-practices-availability-paired-regions.md).

Si usa réplicas entre regiones para planear la recuperación ante desastres, se recomienda que cree la réplica en la región emparejada en lugar de en una de las otras regiones. Las regiones emparejadas evitan actualizaciones simultáneas y priorizan el aislamiento físico y la residencia de datos.  

Sin embargo, existen limitaciones que deben considerarse: 

* Disponibilidad regional: Azure Database for MySQL está disponible en el Centro de Francia, Norte de Emiratos Árabes Unidos y Centro de Alemania. Sin embargo, sus regiones emparejadas no están disponibles.

* Pares unidireccionales: Algunas regiones de Azure se emparejan solo en una dirección. Estas regiones incluyen Oeste de la India, Sur de Brasil y US Gov Virginia.
   Esto significa que un servidor de origen del Oeste de la India puede crear una réplica en el Sur de la India. Sin embargo, un servidor de origen del Sur de la India no puede crear una réplica en el Oeste de la India. Esto es debido a que la región secundaria del Oeste de la India es Sur de la India, pero la región secundaria de esta última no es Oeste de la India.

## <a name="create-a-replica"></a>Creación de una réplica

> [!IMPORTANT]
> La característica de réplica de lectura solo está disponible para servidores de Azure Database for MySQL en los planes de tarifa De uso general u Optimizada para memoria. Asegúrese de que el servidor de origen esté en uno de estos planes de tarifa.

Si un servidor de origen no tiene ningún servidor de réplica existente, el origen se reiniciará primero a fin de prepararse para la replicación.

Cuando se inicia el flujo de trabajo de creación de la réplica, se crea un servidor Azure Database for MySQL en blanco. El nuevo servidor se rellena con los datos que estaban en el servidor de origen. El tiempo de creación depende de la cantidad de datos en el origen y del tiempo transcurrido desde la última copia de seguridad completa semanal. Puede oscilar desde unos minutos hasta varias horas. El servidor de réplica siempre se crea en el mismo grupo de recursos y en la misma suscripción que el servidor de origen. Si desea crear un servidor réplica en otro grupo de recursos o en una suscripción diferente, puede [mover el servidor réplica](../azure-resource-manager/management/move-resource-group-and-subscription.md) después de la creación.

Cada réplica se habilita para el [crecimiento automático](concepts-pricing-tiers.md#storage-auto-grow) del almacenamiento. La característica de crecimiento automático permite a la réplica mantenerse al día con los datos replicados en ella y evitar una interrupción en la replicación causada por errores de almacenamiento insuficiente.

Aprenda a [crear una réplica de lectura en Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conexión a una réplica

Durante la creación, una réplica hereda las reglas de firewall del servidor de origen. Posteriormente, estas reglas son independientes del servidor de origen.

La réplica hereda la cuenta de administrador del servidor de origen. Todas las cuentas de usuario del servidor de origen se replican en las réplicas de lectura. Solo se puede conectar a una réplica de lectura utilizando las cuentas de usuario disponibles en el servidor de origen.

Puede conectarse a la réplica mediante su nombre de host y una cuenta de usuario válida, igual que haría en un servidor Azure Database for MySQL normal. En un servidor denominado **myreplica** con el nombre de usuario administrador **myadmin**, puede conectarse a la réplica mediante la CLI de mysql:

```bash
mysql -h myreplica.mysql.database.azure.com -u myadmin@myreplica -p
```

Cuando se le solicite, escriba la contraseña de la cuenta de usuario.

## <a name="monitor-replication"></a>Supervisión de la replicación

Azure Database for MySQL proporciona la métrica de **retraso de replicación en segundos** en Azure Monitor. Esta métrica está disponible únicamente para las réplicas. Esta métrica se calcula utilizando la métrica `seconds_behind_master` disponible en el comando `SHOW SLAVE STATUS` de MySQL. Establezca una alerta que le informe cuando el retraso de replicación alcance un valor que no sea aceptable para la carga de trabajo.

Si ve un mayor retraso en la replicación, consulte [Solución de problemas de latencia de replicación](howto-troubleshoot-replication-latency.md) para solucionar problemas y comprender las causas posibles.

## <a name="stop-replication"></a>Detención replicación

Puede detener la replicación entre un servidor de origen y una réplica. Una vez que se ha detenido la replicación entre un servidor de origen y una réplica de lectura, la réplica se convierte en un servidor independiente. Los datos del servidor independiente son los datos que estaban disponibles en la réplica en el momento en que se inició el comando para detener la replicación. El servidor independiente no alcanza al servidor de origen.

Si decide detener la replicación en una réplica, perderá todos los vínculos con su servidor de origen anterior y otras réplicas. No se produce ninguna conmutación por error automatizada entre un origen y su réplica.

> [!IMPORTANT]
> Este servidor independiente no puede volver a convertirse en una réplica.
> Asegúrese de que la réplica tenga todos los datos que necesita antes de detener la replicación en una réplica de lectura.

Aprenda a [detener la replicación en una réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Conmutación por error

No se produce ninguna conmutación por error automatizada entre el origen y los servidores de réplica.

Dado que la replicación es asincrónica, se produce un desfase entre el origen y la réplica. La cantidad de desfase puede verse afectada por muchos factores, como lo pesada que sea la carga de trabajo que se ejecuta en el servidor de origen y la latencia entre los centros de datos. En la mayoría de los casos, el retraso de la réplica oscila entre unos segundos y un par de minutos. Puede realizar un seguimiento del retraso real de la replicación mediante la métrica *Replica Lag* (Retraso de réplica), que está disponible para cada réplica. Esta métrica muestra el tiempo desde la última transacción reproducida. Se recomienda que observe el retraso de la réplica durante un período de tiempo para identificar el retraso medio. Puede establecer una alerta sobre el retraso de la réplica, para que si se sale del intervalo esperado, puede tomar medidas.

> [!Tip]
> Si realiza la conmutación por error a la réplica, el retraso en el momento de desvincular la réplica del origen indicará la cantidad de datos perdidos.

Cuando haya decidido que quiere conmutar por error a una réplica, realice estos pasos:

1. Detenga la replicación en la réplica.<br/>
   Este paso es necesario para que el servidor de la réplica pueda aceptar escrituras. Como parte de este proceso, el servidor de réplica se desvinculará del origen. Una vez iniciada la detención de la replicación, el proceso de back-end suele tardar aproximadamente dos minutos en completarse. Consulte la sección [Detención de la replicación](#stop-replication) de este artículo para conocer las implicaciones de esta acción.

2. Haga que la replicación apunte a la réplica (anterior).<br/>
   Cada servidor tiene una cadena de conexión única. Actualice la aplicación para que apunte a la réplica (anterior) en lugar de al origen.

Una vez que la aplicación procesa correctamente las lecturas y las escrituras, ya está completa la conmutación por error. La cantidad de tiempo de inactividad que experimente su aplicación dependerá del momento en que se detecte una incidencia y se realicen los pasos 1 y 2 indicados anteriormente.

## <a name="global-transaction-identifier-gtid"></a>Identificador de transacción global (GTID)

El identificador de transacción global (GTID) es un identificador único que se crea con cada transacción confirmada en un servidor de origen y que está desactivado de forma predeterminada en Azure Database for MySQL. GTID se admite en las versiones 5.7 y 8.0, y solo en los servidores que admiten almacenamiento de hasta 16 TB. Para más información sobre GTID y sobre cómo se usa en la replicación, consulte la documentación de la [replicación con GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids.html) de MySQL.

MySQL admite dos tipos de transacciones: Transacciones de GTID (identificadas con GTID) y transacciones anónimas (no tienen ningún GTID asignado)

Los siguientes parámetros de servidor se pueden usar para configurar GTID: 

|**Parámetros de servidor**|**Descripción**|**Valor predeterminado**|**Valores**|
|--|--|--|--|
|`gtid_mode`|Indica si se usan GTID para identificar transacciones. Los cambios de modo se deben realizar exclusivamente paso a paso y en orden ascendente (p. ej., `OFF` -> `OFF_PERMISSIVE` -> `ON_PERMISSIVE` -> `ON`)|`OFF`|`OFF`: Tanto las transacciones nuevas como las de replicación deben ser anónimas <br> `OFF_PERMISSIVE`: las transacciones nuevas son anónimas. Las transacciones replicadas pueden ser transacciones de GTID o anónimas. <br> `ON_PERMISSIVE`: las transacciones nuevas son transacciones de GTID. Las transacciones replicadas pueden ser transacciones de GTID o anónimas. <br> `ON`: tanto las transacciones nuevas como las replicadas deben ser transacciones de GTID.|
|`enforce_gtid_consistency`|Aplica la coherencia de GTID, ya que solo permite que se ejecuten las instrucciones que se pueden registrar de una manera transaccionalmente segura. Este valor debe establecerse en `ON` antes de habilitar la replicación de GTID. |`OFF`|`OFF`: a todas las transacciones se les permite infringir la coherencia de GTID.  <br> `ON`: no se permite a ninguna transacción infringir la coherencia de GTID. <br> `WARN`: a todas las transacciones se les permite infringir la coherencia de GTID, pero se genera una advertencia. | 

> [!NOTE]
> * Una vez habilitado GTID, no se puede volver a desactivar. Si necesita desactivar GTID, póngase en contacto con el servicio de soporte técnico. 
>
> * Si desea cambiar los GTID de un valor a otro, solo puede hacerlo un paso cada vez en orden ascendente de modos. Por ejemplo, si gtid_mode está establecido en OFF_PERMISSIVE, es posible cambiar a ON_PERMISSIVE, pero no a ON.
>
> * Para mantener la coherencia de la replicación, no se puede actualizar en el caso de un servidor maestro o de réplica.
>
> * Se recomienda establecer enforce_gtid_consistency en ON antes de establecer gtid_mode=ON.


Para habilitar GTID y configurar el comportamiento de la coherencia, actualice los parámetros de servidor `gtid_mode` y `enforce_gtid_consistency` mediante [Azure Portal](howto-server-parameters.md), la [CLI de Azure](howto-configure-server-parameters-using-cli.md) o [ PowerShell](howto-configure-server-parameters-using-powershell.md).

Si GTID está habilitado en un servidor de origen (`gtid_mode` = ON), las réplicas recién creadas también tendrán GTID habilitado y usarán la replicación de GTID. Para garantizar la coherencia de la replicación, `gtid_mode` no se puede cambiar una vez que se crean los servidores maestros o de réplica con GTID habilitado. 

## <a name="considerations-and-limitations"></a>Consideraciones y limitaciones

### <a name="pricing-tiers"></a>Planes de tarifa

Actualmente, las réplicas de lectura solo están disponibles en los planes de tarifa de uso general y optimizados para memoria.

> [!NOTE]
> El costo de ejecutar el servidor de réplica se basa en la región en la que se ejecuta el servidor de réplica.

### <a name="source-server-restart"></a>Reinicio del servidor de origen

Cuando se crea la réplica de origen que no tiene réplicas existentes, el origen se reiniciará primero a fin de prepararse para la replicación. Téngalo en cuenta y realice estas operaciones durante un período de poca actividad.

### <a name="new-replicas"></a>Nuevas réplicas

Las réplicas de lectura se crean como nuevos servidores Azure Database for MySQL. Un servidor no puede volver a convertirse en una réplica. No se puede crear una réplica de otra réplica de lectura.

### <a name="replica-configuration"></a>Configuración de réplicas

Las réplicas se crean con la misma configuración de servidor que el origen. Después de crear una réplica, se pueden cambiar varios valores independientemente del servidor de origen: generación de proceso, núcleos virtuales, almacenamiento y período de retención de copias de seguridad. El plan de tarifa también se puede cambiar de forma independiente, excepto si es con origen o destino en el nivel Básico.

> [!IMPORTANT]
> Antes de actualizar la configuración de un servidor de origen con nuevos valores, actualice la configuración de las réplicas a valores iguales o mayores. Esta acción garantiza que la réplica puede hacer frente a los cambios realizados en el servidor de origen.

Las reglas de firewall y la configuración de parámetros se heredan del servidor de origen y van a la réplica cuando esta se crea. Después, las reglas de la réplica son independientes.

### <a name="stopped-replicas"></a>Réplicas detenidas

Si detiene la replicación entre un servidor de origen y una réplica de lectura, la réplica detenida se convierte en un servidor independiente que acepta operaciones de lectura y escritura. Este servidor independiente no puede volver a convertirse en una réplica.

### <a name="deleted-source-and-standalone-servers"></a>Servidores independientes y de origen eliminados

Al eliminar un servidor de origen, la replicación se detiene en todas las réplicas de lectura. Estas réplicas se convierten automáticamente en servidores independientes y pueden aceptar operaciones de lectura y de escritura. El propio servidor de origen se elimina.

### <a name="user-accounts"></a>Cuentas de usuario

Los usuarios del servidor de origen se replican en las réplicas de lectura. Solo se puede conectar a una réplica de lectura utilizando las cuentas de usuario disponibles en el servidor de origen.

### <a name="server-parameters"></a>Parámetros del servidor

Para evitar que los datos dejen de estar sincronizados y evitar posibles pérdidas o daños en los datos, se bloquean algunos parámetros del servidor para que no se actualicen mientras se usan réplicas de lectura.

Los siguientes parámetros de servidor están bloqueados tanto en el servidor de origen como en los de réplica:

* [`innodb_file_per_table`](https://dev.mysql.com/doc/refman/8.0/en/innodb-file-per-table-tablespaces.html) 
* [`log_bin_trust_function_creators`](https://dev.mysql.com/doc/refman/5.7/en/replication-options-binary-log.html#sysvar_log_bin_trust_function_creators)

El parámetro [`event_scheduler`](https://dev.mysql.com/doc/refman/5.7/en/server-system-variables.html#sysvar_event_scheduler) está bloqueado en los servidores de réplica.

Para actualizar uno de los parámetros anteriores en el servidor de origen, elimine los servidores de réplica, actualice el valor del parámetro en el origen y vuelva a crear las réplicas.

### <a name="gtid"></a>GTID

GTID se admite en:

* Las versiones 5.7 y 8.0 de MySQL.
* Servidores que admiten almacenamiento de hasta 16 TB. Consulte el artículo sobre el [plan de tarifa](concepts-pricing-tiers.md#storage) para ver la lista completa de las regiones que admiten el almacenamiento de 16 TB.

GTID está desactivado de forma predeterminada, Una vez habilitado GTID, no se puede volver a desactivar. Si tiene que desactivar GTID, póngase en contacto con el servicio de soporte técnico.

Si GTID está habilitado en un servidor de origen, las réplicas recién creadas también tendrán GTID habilitado y usarán la replicación de GTID. Para que la replicación se realice de forma consistente, no puede actualizar `gtid_mode` en los servidores de origen ni de réplica.

### <a name="other"></a>Otros

* No permite crear réplicas de réplicas.
* Las tablas en memoria pueden provocar que las réplicas dejen de sincronizarse. Esto es una limitación de la tecnología de replicación de MySQL. Puede obtener más información en la [documentación de referencia de MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html).
* Asegúrese de que las tablas del servidor de origen tienen claves principales. La falta de claves principales puede generar una latencia en la replicación entre el origen y las réplicas.
* Consulte la lista completa de las limitaciones de la replicación de MySQL en la [documentación de MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [crear y administrar réplicas de lectura mediante Azure Portal](howto-read-replicas-portal.md).
* Aprenda a [crear y administrar réplicas de lectura mediante la CLI de Azure y API REST](howto-read-replicas-cli.md).