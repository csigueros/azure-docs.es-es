---
title: 'Réplicas de lectura: Azure Database for MariaDB'
description: 'Obtenga información sobre las réplicas de lectura en Azure Database for MariaDB: elección de regiones, creación de réplicas, conexión a réplicas, supervisión de la replicación y detención de la replicación.'
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 01/18/2021
ms.custom: references_regions
ms.openlocfilehash: 3b7306555e59759f79ffad8e984f0688820247de
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128641301"
---
# <a name="read-replicas-in-azure-database-for-mariadb"></a>Réplicas de lectura en Azure Database for MariaDB

La característica de réplica de lectura permite replicar datos de un servidor Azure Database for MariaDB en un servidor de solo lectura. Puede crear hasta cinco réplicas desde el servidor de origen. Las réplicas se actualizan asincrónicamente mediante la tecnología de replicación basada en la posición de los archivos del registro binario (binlog) del motor de MariaDB. Para obtener más información sobre la replicación de binlog, consulte la [introducción a la replicación de binlog](https://mariadb.com/kb/en/library/replication-overview/).

Las réplicas son nuevos servidores que se administran de forma similar a los servidores de Azure Database for MariaDB normales. En cada réplica de lectura, se le cobra por el proceso aprovisionado en núcleos virtuales y el almacenamiento aprovisionado en GB/mes.

Para más información acerca de la replicación de GTID, consulte la [documentación de replicación de MariaDB](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Este artículo contiene referencias al término *esclavo*, un término que Microsoft ya no usa. Cuando se elimine el término del software, se eliminará también de este artículo.

## <a name="when-to-use-a-read-replica"></a>Casos en los que utilizar las réplicas de lectura

La finalidad de la característica de réplica de lectura es ayudar a mejorar el rendimiento y la escala de las cargas de trabajo que conllevan un gran número de operaciones de lectura. Las cargas de trabajo de lectura se pueden aislar en las réplicas, mientras que las cargas de trabajo de escritura se pueden dirigir al servidor principal.

Un caso habitual es que las cargas de trabajo de análisis e inteligencia empresarial utilicen la réplica de lectura como origen de datos para los informes.

Dado que las réplicas son de solo lectura, no reducen directamente las cargas de capacidad de escritura en el servidor principal. Esta característica no está destinada a cargas de trabajo intensivas de escritura.

Esta característica de réplica de lectura utiliza la replicación asincrónica nativa. La característica no está diseñada para escenarios de replicación sincrónica. Habrá un retraso medible entre el origen y la réplica. Los datos de la réplica se vuelven finalmente coherentes con los datos del servidor principal. Use esta característica con cargas de trabajo que puedan admitir este retraso.

## <a name="cross-region-replication"></a>Replicación entre regiones

Puede crear una réplica de lectura en una región distinta a la del servidor de origen. La replicación entre regiones puede ser útil para escenarios como el planeamiento de la recuperación ante desastres o la incorporación de datos más cerca de los usuarios.

Puede tener un servidor de origen en cualquier [región de Azure Database for MariaDB](https://azure.microsoft.com/global-infrastructure/services/?products=mariadb).  Un servidor de origen puede tener una réplica en su región emparejada o en las regiones de réplica universales. En la imagen siguiente se muestran las regiones de réplica disponibles en función de la región de origen.

[![Regiones de réplica de lectura](media/concepts-read-replica/read-replica-regions.png)](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiones de réplica universal

Puede crear una réplica de lectura en cualquiera de las siguientes regiones, con independencia de dónde se encuentre el servidor de origen. Entre las regiones de réplica universales admitidas se incluyen:

Este de Australia, Sudeste de Australia, Sur de Brasil, Centro de Canadá, Este de Canadá, Centro de EE. UU., Este de Asia, Este de EE. UU. 2, Este de Japón, Oeste de Japón, Centro de Corea del Sur, Sur de Corea del Sur, Centro y norte de EE. UU., Norte de Europa, Centro-sur de EE. UU., Sudeste de Asia, Sur de Reino Unido, Oeste de Reino Unido, Oeste de Europa, Oeste de EE. UU., Oeste de EE. UU. 2, Centro-oeste de EE. UU.

### <a name="paired-regions"></a>Regiones emparejadas

Además de las regiones de réplica universales, puede crear una réplica de lectura en la región emparejada de Azure del servidor de origen. Si no conoce el par de la región, puede obtener más información en el [artículo sobre regiones emparejadas de Azure](../best-practices-availability-paired-regions.md).

Si usa réplicas entre regiones para planear la recuperación ante desastres, se recomienda que cree la réplica en la región emparejada en lugar de en una de las otras regiones. Las regiones emparejadas evitan actualizaciones simultáneas y priorizan el aislamiento físico y la residencia de datos.  

Sin embargo, existen limitaciones que deben considerarse: 

* Disponibilidad regional: Azure Database for MariaDB está disponible en el Centro de Francia, Norte de Emiratos Árabes Unidos y Centro de Alemania. Sin embargo, sus regiones emparejadas no están disponibles.

* Pares unidireccionales: Algunas regiones de Azure se emparejan solo en una dirección. Estas regiones incluyen Oeste de la India, Sur de Brasil y US Gov Virginia. 
   Esto significa que un servidor de origen del Oeste de la India puede crear una réplica en el Sur de la India. Sin embargo, un servidor de origen del Sur de la India no puede crear una réplica en el Oeste de la India. Esto es debido a que la región secundaria del Oeste de la India es Sur de la India, pero la región secundaria de esta última no es Oeste de la India.

## <a name="create-a-replica"></a>Creación de una réplica

> [!IMPORTANT]
> La característica de réplica de lectura solo está disponible para servidores de Azure Database for MariaDB en los planes de tarifa De uso general u Optimizado para memoria. Asegúrese de que el servidor de origen esté en uno de estos planes de tarifa.

Si un servidor de origen no tiene ningún servidor de réplica existente, el origen se reiniciará primero a fin de prepararse para la replicación.

Cuando se inicia el flujo de trabajo de creación de la réplica, se crea un servidor de Azure Database for MariaDB en blanco. El nuevo servidor se rellena con los datos que estaban en el servidor de origen. El tiempo de creación depende de la cantidad de datos en el origen y del tiempo transcurrido desde la última copia de seguridad completa semanal. Puede oscilar desde unos minutos hasta varias horas.

> [!NOTE]
> Si no tiene alertas de almacenamiento en sus servidores, se recomienda que lo haga. La alerta le informa cuando un servidor está alcanzando el límite de almacenamiento, lo que afectará a la replicación.

Aprenda a [crear una réplica de lectura en Azure Portal](howto-read-replicas-portal.md).

## <a name="connect-to-a-replica"></a>Conexión a una réplica

Durante la creación, una réplica hereda las reglas de firewall del servidor de origen. Posteriormente, estas reglas son independientes del servidor de origen.

La réplica hereda la cuenta de administrador del servidor de origen. Todas las cuentas de usuario del servidor de origen se replican en las réplicas de lectura. Solo se puede conectar a una réplica de lectura utilizando las cuentas de usuario disponibles en el servidor de origen.

Puede conectarse a la réplica mediante su nombre de host y una cuenta de usuario válida, igual que haría en un servidor Azure Database for MariaDB normal. En un servidor denominado **myreplica** con el nombre de usuario administrador **myadmin**, puede conectarse a la réplica mediante la CLI de mysql:

```bash
mysql -h myreplica.mariadb.database.azure.com -u myadmin@myreplica -p
```

Cuando se le solicite, escriba la contraseña de la cuenta de usuario.

## <a name="monitor-replication"></a>Supervisión de la replicación

Azure Database for MariaDB proporciona la métrica de **retraso de replicación en segundos** en Azure Monitor. Esta métrica está disponible únicamente para las réplicas.

Esta métrica se calcula utilizando la métrica `seconds_behind_master` disponible en el comando `SHOW SLAVE STATUS` de MariaDB.

Establezca una alerta que le informe cuando el retraso de replicación alcance un valor que no sea aceptable para la carga de trabajo.

## <a name="stop-replication"></a>Detención replicación

Puede detener la replicación entre un servidor de origen y una réplica. Una vez que se ha detenido la replicación entre un servidor de origen y una réplica de lectura, la réplica se convierte en un servidor independiente. Los datos del servidor independiente son los datos que estaban disponibles en la réplica en el momento en que se inició el comando para detener la replicación. El servidor independiente no alcanza al servidor de origen.

Si decide detener la replicación en una réplica, perderá todos los vínculos con su servidor de origen anterior y otras réplicas. Recuerde que no se produce ninguna conmutación por error automatizada entre un servidor de origen y su réplica.

> [!IMPORTANT]
> Este servidor independiente no puede volver a convertirse en una réplica.
> Asegúrese de que la réplica tenga todos los datos que necesita antes de detener la replicación en una réplica de lectura.

Aprenda a [detener la replicación en una réplica](howto-read-replicas-portal.md).

## <a name="failover"></a>Conmutación por error

No se produce ninguna conmutación por error automatizada entre el servidor de origen y la réplica.

Dado que la replicación es asincrónica, se produce un retraso entre el origen y la réplica. La cantidad de retraso puede verse afectada por varios factores, como lo pesada que sea la carga de trabajo que se ejecuta en el servidor de origen y la latencia entre los centros de datos. En la mayoría de los casos, el retraso de la réplica oscila entre unos segundos y un par de minutos. Puede realizar un seguimiento del retraso real de la replicación mediante la métrica *Replica Lag* (Retraso de réplica), que está disponible para cada réplica. Esta métrica muestra el tiempo desde la última transacción reproducida. Se recomienda que observe el retraso de la réplica durante un período de tiempo para identificar el retraso medio. Puede establecer una alerta sobre el retraso de la réplica, para que si se sale del intervalo esperado, puede tomar medidas.

> [!Tip]
> Si realiza la conmutación por error a la réplica, el retraso en el momento de desvincular la réplica del origen indicará la cantidad de datos perdidos.

Después de haber decidido que quiere conmutar por error a una réplica, realice estos pasos:

1. Detenga la replicación en la réplica.

   Este paso es necesario para que el servidor de la réplica pueda aceptar escrituras. Como parte de este proceso, el servidor de réplica se desvinculará del principal. Una vez iniciada la detención de la replicación, el proceso de back-end suele tardar aproximadamente 2 minutos en completarse. Consulte la sección [Detención de la replicación](#stop-replication) de este artículo para conocer las implicaciones de esta acción.

2. Haga que la aplicación apunte a la réplica (anterior).

   Cada servidor tiene una cadena de conexión única. Actualice la aplicación para que apunte a la réplica (anterior), en lugar de al servidor principal.

Una vez que la aplicación procesa correctamente las lecturas y las escrituras, ya está completa la conmutación por error. La cantidad de tiempo de inactividad que experimente su aplicación dependerá del momento en que se detecte una incidencia y se realicen los pasos 1 y 2 anteriores.

## <a name="considerations-and-limitations"></a>Consideraciones y limitaciones

### <a name="pricing-tiers"></a>Planes de tarifa

Actualmente, las réplicas de lectura solo están disponibles en los planes de tarifa de uso general y optimizados para memoria.

> [!NOTE]
> El costo de ejecutar el servidor de réplica se basa en la región en la que se ejecuta el servidor de réplica.

### <a name="source-server-restart"></a>Reinicio del servidor de origen

Cuando se crea la réplica de origen que no tiene réplicas existentes, el origen se reiniciará primero a fin de prepararse para la replicación. Téngalo en cuenta y realice estas operaciones durante un período de poca actividad.

### <a name="new-replicas"></a>Nuevas réplicas

Las réplicas de lectura se crean como nuevos servidores Azure Database for MariaDB. Un servidor no puede volver a convertirse en una réplica. No se puede crear una réplica de otra réplica de lectura.

### <a name="replica-configuration"></a>Configuración de réplicas

Las réplicas se crean con la misma configuración de servidor que el servidor principal. Después de crear una réplica, se pueden cambiar varias configuraciones con independencia del servidor de origen: generación de proceso, núcleos virtuales, almacenamiento, período de retención de copia de seguridad y versión del motor de MariaDB. El plan de tarifa también se puede cambiar de forma independiente, excepto si es con origen o destino en el nivel Básico.

> [!IMPORTANT]
> Antes de actualizar la configuración de un servidor de origen con nuevos valores, actualice la configuración de las réplicas a valores iguales o mayores. Esta acción garantiza que la réplica puede hacer frente a los cambios realizados en el servidor principal.

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

* [`innodb_file_per_table`](https://mariadb.com/kb/en/library/innodb-system-variables/#innodb_file_per_table) 
* [`log_bin_trust_function_creators`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#log_bin_trust_function_creators)

El parámetro [`event_scheduler`](https://mariadb.com/kb/en/library/server-system-variables/#event_scheduler) está bloqueado en los servidores de réplica.

Para actualizar uno de los parámetros anteriores en el servidor de origen, elimine los servidores de réplica, actualice el valor del parámetro en el servidor principal y vuelva a crear las réplicas.

### <a name="other"></a>Otros

* No permite crear réplicas de réplicas.
* Las tablas en memoria pueden provocar que las réplicas dejen de sincronizarse. Esto es una limitación de la tecnología de replicación de MariaDB.
* Asegúrese de que las tablas del servidor de origen tienen claves principales. La falta de claves principales puede generar una latencia en la replicación entre el origen y las réplicas.

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [crear y administrar réplicas de lectura mediante Azure Portal](howto-read-replicas-portal.md).
* Aprenda a [crear y administrar réplicas de lectura mediante la CLI de Azure y API REST](howto-read-replicas-cli.md).
