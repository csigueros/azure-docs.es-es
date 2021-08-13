---
title: 'Réplicas de lectura en Azure Database for PostgreSQL: servidor único'
description: En este artículo se describe la característica de réplica de lectura de Azure Database for PostgreSQL de servidor único.
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/29/2021
ms.openlocfilehash: 635a90b70c044e1f8c49518c42b49c521dbb317e
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "110781894"
---
# <a name="read-replicas-in-azure-database-for-postgresql---single-server"></a>Réplicas de lectura en Azure Database for PostgreSQL: servidor único

La característica de réplica de lectura permite replicar datos de un servidor Azure Database for PostgreSQL en un servidor de solo lectura. Las réplicas se actualizan de **manera asincrónica** con la tecnología de replicación física nativa del motor de PostgreSQL. Puede crear hasta cinco réplicas desde el servidor principal.

Las réplicas son nuevos servidores que se administran de forma similar a los servidores de Azure Database for PostgreSQL normales. En cada réplica de lectura, se le cobra por el proceso aprovisionado en núcleos virtuales y el almacenamiento aprovisionado en GB/mes.

Vea cómo [crear y administrar réplicas](howto-read-replicas-portal.md).

## <a name="when-to-use-a-read-replica"></a>Casos en los que utilizar las réplicas de lectura
La finalidad de la característica de réplica de lectura es ayudar a mejorar el rendimiento y la escala de las cargas de trabajo que conllevan un gran número de operaciones de lectura. Las cargas de trabajo de lectura se pueden aislar en las réplicas, mientras que las cargas de trabajo de escritura se pueden dirigir al servidor principal. Las réplicas de lectura también se pueden implementar en otra región y se pueden promover a servidor de lectura/escritura en caso de una recuperación ante desastres.

Un caso habitual es que las cargas de trabajo de análisis e inteligencia empresarial utilicen la réplica de lectura como origen de datos para los informes.

Dado que las réplicas son de solo lectura, no reducen directamente las cargas de capacidad de escritura en el servidor principal.

### <a name="considerations"></a>Consideraciones
La característica está pensada para escenarios donde el retraso es aceptable y para la descarga de consultas. No está diseñada para escenarios de replicación sincrónica en los que se espera que los datos de réplica estén actualizados. Habrá un retraso medible entre el servidor principal y la réplica. Puede ser de minutos o incluso horas, según la carga de trabajo y la latencia entre el servidor principal y la réplica. Los datos de la réplica se vuelven finalmente coherentes con los datos del servidor principal. Use esta característica con cargas de trabajo que puedan admitir este retraso. 

> [!NOTE]
> Con la mayoría de las cargas de trabajo, las réplicas de lectura ofrecen actualizaciones casi en tiempo real desde el servidor principal. Sin embargo, con cargas de trabajo principales continuas con numerosas operaciones de escritura, el retraso en la replicación puede seguir creciendo y que no se pueda alcanzar nunca al servidor principal. Como consecuencia, también puede aumentar el uso de almacenamiento en el servidor principal, ya que los archivos WAL no se eliminan hasta que se reciben en la réplica. Si esta situación persiste, la opción para devolver la réplica a un buen estado con respecto al retraso es eliminar y volver a crear la réplica de lectura después de que se completen las cargas de trabajo con numerosas operaciones de escritura.
> Las réplicas de lectura asincrónicas no son adecuadas para esas cargas de trabajo con tantas operaciones de escritura. Al evaluar las réplicas de lectura de la aplicación, supervise el retraso en la réplica durante un ciclo completo de carga de trabajo de la aplicación en sus horas punta y fuera de las horas punta para determinar el posible retraso y el RTO/RPO esperado en diversos puntos del ciclo de carga de trabajo.

> [!NOTE]
> Las copias de seguridad automáticas se realizan para servidores de réplica configurados con hasta 4 TB de almacenamiento.

## <a name="cross-region-replication"></a>Replicación entre regiones
Puede crear una réplica de lectura en una región distinta del servidor principal. La replicación entre regiones puede ser útil para escenarios como el planeamiento de la recuperación ante desastres o la incorporación de datos más cerca de los usuarios.

>[!NOTE]
> Los servidores de nivel básico solo admiten la replicación de la misma región.

Puede tener un servidor principal en cualquier [región de Azure Database for PostgreSQL](https://azure.microsoft.com/global-infrastructure/services/?products=postgresql). Un servidor principal puede tener una réplica emparejada en su región o en las regiones de la réplica universal. En la imagen siguiente se muestran las regiones de réplica disponibles en función de la región primaria.

[ :::image type="content" source="media/concepts-read-replica/read-replica-regions.png" alt-text="Regiones de réplica de lectura":::](media/concepts-read-replica/read-replica-regions.png#lightbox)

### <a name="universal-replica-regions"></a>Regiones de réplica universal
Siempre puede crear una réplica de lectura en cualquiera de las siguientes regiones, con independencia de dónde se encuentre el servidor principal. Estas son las regiones de réplica universal:

Este de Australia, Sudeste de Australia, Sur de Brasil, Centro de Canadá, Este de Canadá, Centro de EE. UU., Este de Asia, Este de EE. UU. 2, Este de Japón, Oeste de Japón, Centro de Corea del Sur, Sur de Corea del Sur, Centro y norte de EE. UU., Norte de Europa, Centro-sur de EE. UU., Sudeste de Asia, Sur de Reino Unido, Oeste de Reino Unido, Oeste de Europa, Oeste de EE. UU., Oeste de EE. UU. 2, Centro-oeste de EE. UU.

### <a name="paired-regions"></a>Regiones emparejadas
Además de las regiones de réplica universal, puede crear una réplica de lectura en la región emparejada de Azure del servidor principal. Si no conoce el par de la región, puede obtener más información en el [artículo sobre regiones emparejadas de Azure](../best-practices-availability-paired-regions.md).

Si usa réplicas entre regiones para planear la recuperación ante desastres, se recomienda que cree la réplica en la región emparejada en lugar de en una de las otras regiones. Las regiones emparejadas evitan actualizaciones simultáneas y priorizan el aislamiento físico y la residencia de datos.  

Existen limitaciones que deben considerarse: 
    
* Pares unidireccionales: Algunas regiones de Azure se emparejan solo en una dirección. Estas regiones incluyen Oeste de la India y Sur de Brasil. 
   Esto significa que un servidor principal de Oeste de la India puede crear una réplica en Sur de la India. En cambio, un servidor principal de Sur de la India no puede crear una réplica en Oeste de la India. Esto es debido a que la región secundaria del Oeste de la India es Sur de la India, pero la región secundaria de esta última no es Oeste de la India.


## <a name="create-a-replica"></a>Creación de una réplica
Cuando se inicia el flujo de trabajo de creación de la réplica, se crea un servidor Azure Database for PostgreSQL en blanco. El nuevo servidor se rellena con los datos que estaban en el servidor principal. El tiempo que se tarda en crear la réplica depende de la cantidad de datos en el servidor principal y del tiempo que ha transcurrido desde la última copia de seguridad completa semanal. Puede oscilar desde unos minutos hasta varias horas.

Cada réplica se habilita para el [crecimiento automático](concepts-pricing-tiers.md#storage-auto-grow) del almacenamiento. La característica de crecimiento automático permite a la réplica mantenerse al día con los datos replicados en ella y evitar una interrupción en la replicación causada por errores de almacenamiento insuficiente.

La característica de réplica de lectura usa la replicación física de PostgreSQL (replicación no lógica). El modo de funcionamiento predeterminado es la transmisión de la replicación mediante espacios de replicación. Cuando es necesario, se usa el trasvase de registros para actualizarse.

Aprenda a [crear una réplica de lectura en Azure Portal](howto-read-replicas-portal.md).

Si el servidor PostgreSQL de origen está cifrado con claves administradas por el cliente, consulte la [documentación](concepts-data-encryption-postgresql.md) para obtener más información.

## <a name="connect-to-a-replica"></a>Conexión a una réplica
Al crear una réplica, no se heredan las reglas de firewall o el punto de conexión de servicio de red virtual del servidor principal. Estas reglas se deben configurar de forma independiente para la réplica.

La réplica hereda su cuenta de administrador del servidor principal. Todas las cuentas de usuario existentes en el servidor principal se replican en las réplicas de lectura. Solo se puede conectar a una réplica de lectura a través de las cuentas de usuario disponibles en el servidor principal.

Puede conectarse a la réplica mediante su nombre de host y una cuenta de usuario válida, igual que haría en un servidor Azure Database for PostgreSQL normal. En un servidor denominado **myreplica** con el nombre de usuario administrador **myadmin**, puede conectarse a la réplica mediante psql:

```bash
psql -h myreplica.postgres.database.azure.com -U myadmin@myreplica -d postgres
```

Cuando se le solicite, escriba la contraseña de la cuenta de usuario.

## <a name="monitor-replication"></a>Supervisión de la replicación
Azure Database for PostgreSQL proporciona dos métricas para supervisar la replicación. Las dos métricas son **Max Lag Across Replicas** (Retraso máximo entre réplicas) y **Replica Lag** (Retraso entre réplicas). Para obtener información sobre cómo ver estas métricas, consulte la sección **Supervisión de una réplica** del [artículo de procedimientos de réplica de lectura](howto-read-replicas-portal.md).

La métrica **Retraso máximo entre réplicas** muestra el retardo en bytes entre la réplica con mayor retardo y el servidor principal. Esta métrica solo es aplicable y está disponible en el servidor principal, y solo lo estará si al menos una de las réplicas de lectura está conectada a la principal y la principal está en modo de replicación de transmisión. La información del retraso no muestra detalles de cuando la réplica está en proceso de alcanzar al servidor principal mediante los registros archivados de este en modo de replicación de envío de archivos.

La métrica **Replica Lag** muestra el tiempo desde la última transacción reproducida. Si no se produce ninguna transacción en el servidor principal, la métrica refleja este retardo de tiempo. Esta métrica solo se aplica y está disponible para los servidores de réplica. El retraso entre réplicas se calcula desde la vista `pg_stat_wal_receiver`:

```SQL
SELECT EXTRACT (EPOCH FROM now() - pg_last_xact_replay_timestamp());
```

Establezca una alerta que le informe cuando el retardo de la réplica alcance un valor que no sea aceptable para la carga de trabajo. 

Para obtener más información, consulte el servidor principal directamente para obtener el retardo de replicación en bytes en todas las réplicas.

> [!NOTE]
> Si se reinicia un servidor principal o una réplica de lectura, el tiempo que tarda en reiniciarse y ponerse al día se reflejará en la métrica de retardo de la réplica.

## <a name="stop-replication--promote-replica"></a>Detención de la replicación y promoción de la réplica
En cualquier momento, puede detener la replicación entre un servidor principal y una réplica. La acción de detención hace que la réplica se reinicie y se promueva como servidor independiente de lectura y escritura. Los datos del servidor independiente son los datos que estaban disponibles en el servidor de réplica en el momento en que se inició la replicación. Las actualizaciones posteriores en el servidor principal no se propagan a la réplica. Sin embargo, es posible que el servidor de réplica haya acumulado registros que aún no se hayan aplicado. Como parte del proceso de reinicio, la réplica aplica todos los registros pendientes antes de aceptar conexiones de cliente.  

### <a name="considerations"></a>Consideraciones
- Antes de detener la replicación en una réplica de lectura, busque el retraso de la replicación para asegurarse de que la réplica tenga todos los datos que necesita. 
- Como la réplica de lectura tiene que aplicar todos los registros pendientes antes de que se pueda convertir en un servidor independiente, el RTO puede ser más alto con cargas de trabajo con numerosas operaciones de escritura cuando se detiene la replicación, ya que podría haber un retraso considerable en la réplica. A la hora de planear la promoción de una réplica, preste atención a este hecho.
- El servidor de réplica promocionado no se puede volver a convertir en una réplica.
- Si promueve una réplica como servidor principal, no podrá volver a establecer la replicación en el servidor principal anterior. Si quiere volver a la región principal anterior, puede establecer un nuevo servidor de réplica con un nuevo nombre (o) eliminar el servidor principal anterior y crear una réplica con el nombre de este.
- Si tiene varias réplicas de lectura y promueve una de ellas como servidor principal, otros servidores de réplica seguirán conectados al servidor principal anterior. Es posible que tenga que volver a crear las réplicas a partir del nuevo servidor promocionado.

Al detener la replicación, la réplica pierde todos los vínculos con su servidor principal anterior y otras réplicas.

Aprenda a [detener la replicación en una réplica](howto-read-replicas-portal.md).

## <a name="failover-to-replica"></a>Conmutación por error a una réplica

En caso de error del servidor principal, **no** conmute por error automáticamente a la réplica de lectura. 

Dado que la replicación es asincrónica, habrá un retraso considerable entre el servidor principal y la réplica. La cantidad de retraso se ve afectada por diversos factores, como el tipo de carga de trabajo que se ejecuta en el servidor principal y la latencia entre el servidor principal y el servidor de réplica. En casos típicos con una carga de trabajo de escritura nominal, cabe esperar un retraso en la réplica de entre unos segundos y algunos minutos. Sin embargo, en los casos en los que el servidor principal ejecuta cargas de trabajo con numerosas operaciones de escritura y la réplica no se pone al día lo bastante rápido, el retraso puede ser mucho mayor. Para realizar un seguimiento del retraso de replicación de cada réplica, use la métrica *Replica Lag* (Retraso entre réplicas). Esta métrica muestra el tiempo desde la última transacción reproducida en la réplica. Se recomienda que observe el retraso de la réplica durante un período de tiempo para identificar el retraso medio. Puede establecer una alerta sobre el retraso de la réplica, de forma que, si se sale del intervalo esperado, se le notifique para que pueda tomar medidas.

> [!Tip]
> Si realiza la conmutación por error a la réplica, el retraso en el momento de desvincular la réplica del servidor principal indicará cuántos datos se han perdido.

Cuando haya decidido que quiere conmutar por error a una réplica, realice estos pasos: 

1. Detenga la replicación en la réplica.<br/>
   Este paso es necesario para que el servidor de réplica se convierta en servidor independiente y pueda aceptar operaciones de escritura. Como parte de este proceso, el servidor de réplica se reiniciará y se desvinculará del servidor principal. Una vez iniciada la detención de la replicación, el proceso de back-end suele tardar unos minutos en aplicar los registros residuales que todavía no se han aplicado y en abrir la base de datos como servidor de lectura y escritura. Consulte la sección [Detención de la replicación](#stop-replication--promote-replica) de este artículo para conocer las implicaciones de esta acción.
    
2. Haga que la replicación apunte a la réplica (anterior).<br/>
   Cada servidor tiene una cadena de conexión única. Actualice la cadena de conexión de la aplicación para que apunte a la réplica (anterior), en lugar de al servidor principal.
    
Una vez que la aplicación procesa correctamente las lecturas y las escrituras, ya está completa la conmutación por error. La cantidad de tiempo de inactividad que experimente su aplicación dependerá del momento en que se detecte una incidencia y se realicen los pasos 1 y 2 anteriores.

### <a name="disaster-recovery"></a>Recuperación ante desastres

Cuando se produce un desastre importante, como errores regionales o de nivel de zona de disponibilidad, puede realizar una operación de recuperación ante desastres mediante la promoción de la réplica de lectura. En el portal de la interfaz de usuario, puede navegar al servidor de réplica de lectura. Después, haga clic en la pestaña de replicación. Desde aquí puede detener la réplica para promoverla de modo que se convierta en un servidor independiente. Otra opción consiste en usar la [CLI de Azure](/cli/azure/postgres/server/replica#az_postgres_server_replica_stop) para detener y promover el servidor de réplica.

## <a name="considerations"></a>Consideraciones

En esta sección se resumen las consideraciones sobre la característica de réplica de lectura.

### <a name="prerequisites"></a>Requisitos previos
Tanto las réplicas de lectura como la [descodificación lógica](concepts-logical.md) dependen del registro de escritura previa (WAL) Postgres para obtener información. Estas dos características necesitan diferentes niveles de registro de Postgres. La descodificación lógica requiere un mayor nivel de registro que las réplicas de lectura.

Para configurar el nivel de registro adecuado, use el parámetro de soporte de replicación de Azure. El soporte de la replicación de Azure tiene tres opciones de valor:

* **Desactivado**: coloca la más mínima información en el WAL. Este valor no está disponible en la mayoría de los servidores Azure Database for PostgreSQL.  
* **Réplica**: más detallado que **Off**. Este es el nivel mínimo de registro necesario para que [las réplicas de lectura](concepts-read-replicas.md) funcionen. Esta es la configuración predeterminada en la mayoría de los servidores.
* **Lógico**: más detallado que **Réplica**. Este es el nivel mínimo de registro para que funcione la descodificación lógica. Las réplicas de lectura también funcionan con este valor.


### <a name="new-replicas"></a>Nuevas réplicas
Las réplicas de lectura se crean como nuevos servidores Azure Database for PostgreSQL. Un servidor no puede volver a convertirse en una réplica. No se puede crear una réplica de otra réplica de lectura.

### <a name="replica-configuration"></a>Configuración de réplicas
Una réplica se crea con la misma configuración de proceso y almacenamiento que el servidor principal. Una vez creada una réplica, se pueden cambiar varias configuraciones, incluido el período de retención de almacenamiento y copia de seguridad.

La réplica no hereda las reglas de firewall, las reglas de red virtual ni la configuración de parámetros del servidor principal cuando se crea o con posterioridad.

### <a name="scaling"></a>Ampliación
Escalado de núcleos virtuales o entre Uso general y Memoria optimizada:
* PostgreSQL requiere que `max_connections` la configuración en un servidor secundario sea [mayor o igual que el valor de la principal](https://www.postgresql.org/docs/current/hot-standby.html), de lo contrario, la réplica secundaria no se iniciará.
* En Azure Database for PostgreSQL, las conexiones máximas permitidas para cada servidor se corrigen en la SKU de proceso, ya que las conexiones ocupan memoria. Puede obtener más información sobre la [asignación entre max_connections y las SKU de proceso](concepts-limits.md).
* **Escalado**: En primer lugar, escale el proceso de una réplica y, a continuación, escale la principal. Esta orden impedirá que los errores no cumplan `max_connections` el requisito.
* **Reducción vertical**: En primer lugar, reduzca verticalmente el proceso principal y, a continuación, reduzca verticalmente la réplica. Si intenta escalar la réplica por debajo de la principal, se producirá un error, ya que esto infringe el`max_connections` requisito.

Escalado de almacenamiento:
* Todas las réplicas tienen habilitado el crecimiento automático de almacenamiento para evitar problemas de replicación de una réplica de almacenamiento completo. Esta configuración no se puede deshabilitar.
* También puede escalar verticalmente el almacenamiento de forma manual, como haría en cualquier otro servidor


### <a name="basic-tier"></a>Nivel Basic
Los servidores de nivel básico solo admiten la replicación de la misma región.

### <a name="max_prepared_transactions"></a>max_prepared_transactions
[PostgreSQL requiere](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PREPARED-TRANSACTIONS) que el valor del parámetro `max_prepared_transactions` en la réplica de lectura sea mayor o igual que el valor principal; en caso contrario, no se iniciará la réplica. Si quiere cambiar `max_prepared_transactions` en el servidor principal, primero cámbielo en las réplicas.

### <a name="stopped-replicas"></a>Réplicas detenidas
Si decide detener la replicación entre un servidor principal y una réplica de lectura, esta se reiniciará para aplicar el cambio. La réplica detenida se convierte en un servidor independiente que acepta las lecturas y escrituras. Este servidor independiente no puede volver a convertirse en una réplica.

### <a name="deleted-primary-and-standalone-servers"></a>Servidores principal e independiente eliminados
Cuando se elimina un servidor principal, todas sus réplicas de lectura se convierten en servidores independientes. Las réplicas se reiniciarán para reflejar este cambio.

## <a name="next-steps"></a>Pasos siguientes
* Aprenda a [crear y administrar réplicas de lectura en Azure Portal](howto-read-replicas-portal.md).
* Aprenda a [crear y administrar réplicas de lectura en la CLI de Azure y con API REST](howto-read-replicas-cli.md).