---
title: Alta disponibilidad con redundancia de zona de la opción Servidor flexible de Azure Database for MySQL
description: Obtenga una introducción conceptual sobre la alta disponibilidad con redundancia de zona de la opción Servidor flexible de Azure Database for MySQL.
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: e95119f65e088fe9b9b6ace71b6fee98f679f5a4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426101"
---
# <a name="high-availability-in-azure-database-for-mysql---flexible-server-preview"></a>Alta disponibilidad en la opción Servidor flexible de Azure Database for MySQL (versión preliminar)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT] 
> Actualmente, Azure Database for MySQL: Servidor flexible se encuentra en versión preliminar pública.

La versión preliminar de la opción Servidor flexible de Azure Database for MySQL permite configurar la alta disponibilidad con conmutación automática por error. La solución de alta disponibilidad está diseñada para garantizar que los datos confirmados nunca se pierdan debido a errores y que la base de datos no sea un único punto de error de la arquitectura de software. Cuando se configura la alta disponibilidad, la opción Servidor flexible aprovisiona y administra automáticamente una réplica en espera. Hay dos modelos arquitectónicos de alta disponibilidad:

* **Alta disponibilidad con redundancia de zona**. Esta opción es preferible para el aislamiento completo y la redundancia de la infraestructura en varias zonas de disponibilidad. Proporciona el máximo nivel de disponibilidad, pero exige configurar la redundancia de las aplicaciones entre zonas. La alta disponibilidad con redundancia de zona es preferible cuando se quiere lograr el máximo nivel de disponibilidad frente a cualquier error de infraestructura en la zona de disponibilidad y cuando la latencia entre las zonas de disponibilidad es aceptable. Solo se puede habilitar cuando se crea el servidor. La alta disponibilidad con redundancia de zona está disponible en un [subconjunto de regiones de Azure](./overview.md#azure-regions) que admiten varias [zonas de disponibilidad](../../availability-zones/az-overview.md) y donde hay disponibles [recursos compartidos de archivos Premium con redundancia de zona](../..//storage/common/storage-redundancy.md#zone-redundant-storage).

* **Alta disponibilidad en la misma zona**. Esta opción es preferible para la redundancia de infraestructura con menor latencia de red, ya que el servidor principal y el servidor en espera estarán en la misma zona de disponibilidad. Proporciona alta disponibilidad sin necesidad de configurar la redundancia de las aplicaciones entre zonas. La alta disponibilidad en la misma zona se prefiere cuando se quiere lograr el máximo nivel de disponibilidad dentro de una sola zona de disponibilidad con la mínima latencia de red. La alta disponibilidad en la misma zona está disponible en todas las [regiones de Azure](./overview.md#azure-regions) donde es posible utilizar la opción Servidor flexible de Azure Database for MySQL.  

## <a name="zone-redundant-ha-architecture"></a>Arquitectura de alta disponibilidad con redundancia de zona
 
Al implementar un servidor con alta disponibilidad con redundancia de zona, se crearán dos servidores: 
- Un servidor principal en una zona de disponibilidad
- Un servidor de réplica en espera que tiene la misma configuración que el servidor principal (nivel y tamaño de proceso, tamaño de almacenamiento y configuración de red) en otra zona de disponibilidad de la misma región de Azure.
 
Puede elegir la zona de disponibilidad para el servidor principal y la réplica en espera. La colocación de los servidores de bases de datos en espera y las aplicaciones en espera en la misma zona reduce la latencia. También permite prepararse mejor para situaciones de recuperación ante desastres y escenarios de "zona fuera de servicio".

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Diagrama que muestra la arquitectura de la alta disponibilidad con redundancia de zona.":::

Los datos y los archivos de registro se hospedan en un [almacenamiento con redundancia de zona (ZRS)](../../storage/common/storage-redundancy.md#redundancy-in-the-primary-region). Estos archivos se replican en el servidor en espera mediante la replicación de nivel de almacenamiento disponible con ZRS. Si se produce una conmutación por error: 
- Se activa la réplica en espera. 
- Los archivos de registro binarios del servidor principal se siguen aplicando al servidor en espera para conectarlo a la última transacción confirmada en el servidor principal. 

En ZRS, los registros son accesibles incluso cuando el servidor principal no está disponible. Esta disponibilidad ayuda a garantizar que no se pierdan datos. Una vez que se activa la réplica en espera y se aplican los registros binarios, el servidor de réplica en espera actual toma el rol del servidor principal. Se actualiza el DNS para que las conexiones del cliente se dirijan a la nueva réplica principal cuando el cliente se vuelva a conectar. La conmutación por error es totalmente transparente desde la aplicación cliente y no requiere ninguna acción por su parte. A continuación, en cuanto es posible, la solución de alta disponibilidad reactiva el servidor principal antiguo y lo coloca como servidor en espera.
 
Se utiliza el nombre del servidor de bases de datos para conectar las aplicaciones al servidor principal. La información de la réplica en espera no se expone para el acceso directo. Las confirmaciones y escrituras se confirman una vez que los archivos de registro se vacíen en el almacenamiento ZRS del servidor principal. Debido a la tecnología de replicación de sincronización que se usa en el almacenamiento ZRS, las aplicaciones pueden esperar un aumento de entre el 5 % y el 10 % de la latencia en las escrituras y confirmaciones.
 
Las copias de seguridad automáticas, tanto las de instantáneas como las de registros, se realizan en un almacenamiento con redundancia de zona desde el servidor de bases de datos principal.

## <a name="same-zone-ha-architecture"></a>Arquitectura de alta disponibilidad en la misma zona
 
Al implementar un servidor con alta disponibilidad en la misma zona, se crearán dos servidores en la misma zona: 
- Un servidor principal
- Un servidor de réplica en espera que tiene la misma configuración que el servidor principal (nivel y tamaño de proceso, tamaño de almacenamiento y configuración de red). 

El servidor en espera ofrece redundancia de infraestructura con una máquina virtual independiente (proceso). Esta redundancia reduce el tiempo de conmutación por error y la latencia de red entre la aplicación y el servidor de bases de datos debido a la coubicación.

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Diagrama que muestra la arquitectura de la alta disponibilidad en la misma zona.":::

Los datos y los archivos de registro se hospedan en un [almacenamiento con redundancia local (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage). Estos archivos se replican en el servidor en espera mediante la replicación de nivel de almacenamiento disponible con LRS.

Si se produce una conmutación por error: 
- Se activa la réplica en espera. 
- Los archivos de registro binarios del servidor principal se siguen aplicando al servidor en espera para conectarlo a la última transacción confirmada en el servidor principal. 

En LRS, los registros son accesibles incluso cuando el servidor principal no está disponible. Esta disponibilidad ayuda a garantizar que no se pierdan datos. Una vez que se activa la réplica en espera y se aplican los registros binarios, la réplica en espera actual toma el rol del servidor principal. Se actualiza el DNS para redirigir las conexiones a la nueva réplica principal cuando el cliente se vuelve a conectar. La conmutación por error es totalmente transparente desde la aplicación cliente y no requiere ninguna acción por su parte. A continuación, en cuanto es posible, la solución de alta disponibilidad reactiva el servidor principal antiguo y lo coloca como servidor en espera.
 
Se utiliza el nombre del servidor de bases de datos para conectar las aplicaciones al servidor principal. La información de la réplica en espera no se expone para el acceso directo. Las confirmaciones y escrituras se confirman una vez que los archivos de registro se vacíen en el almacenamiento LRS del servidor principal. Dado que la réplica principal y la réplica en espera están en la misma zona, hay menos retraso de replicación y menor latencia entre el servidor de aplicaciones y el servidor de bases de datos. La configuración en la misma zona no proporciona alta disponibilidad en el escenario en que las infraestructuras dependientes quedan sin servicio en la zona de disponibilidad en cuestión. Habrá un tiempo de inactividad hasta que todos los servicios dependientes vuelvan a estar en línea en esa zona de disponibilidad.
 
Las copias de seguridad automáticas, tanto las de instantáneas como las de registros, se realizan en un almacenamiento con redundancia local desde el servidor de bases de datos principal.

>[!Note]
>Para la alta disponibilidad, tanto con redundancia de zona como en la misma zona:
>* Si hay un error, el tiempo necesario para que la réplica en espera adopte el rol de principal depende de la aplicación de registro binario en la réplica en espera. Por lo tanto, se recomienda usar claves principales en todas las tablas para reducir el tiempo de conmutación por error. Los tiempos de conmutación por error suelen oscilar entre 60 y 120 segundos. 
>* El servidor en espera no está disponible para operaciones de lectura o escritura. Es un modo de espera pasivo para permitir una conmutación por error rápida.
>* Use siempre un nombre de dominio completo (FQDN) para conectarse al servidor principal. Evite usar una dirección IP para conectarse. Si se produce una conmutación por error, después de intercambiar los roles del servidor principal y en espera, podría cambiar el registro A de DNS. Ese cambio impedirá que la aplicación se conecte al nuevo servidor principal si se usa la dirección IP en la cadena de conexión.

## <a name="failover-process"></a>Proceso de conmutación por error 
 
### <a name="planned-forced-failover"></a>Planeado: conmutación por error forzada
 
La conmutación por error forzada de la opción Servidor flexible de Azure Database for MySQL permite forzar manualmente una conmutación por error. Esto le permite probar la funcionalidad con los escenarios de la aplicación y le ayuda a prepararse para las interrupciones. 

La conmutación por error forzada desencadena una conmutación por error que activa la réplica en espera para que se convierta en el servidor principal con el mismo nombre de servidor de bases de datos al actualizar el registro de DNS. El servidor principal original se reinicia y se cambia a réplica en espera. Las conexiones de cliente se desconectan y deben volver a conectarse para reanudar sus operaciones. 

El tiempo total de la conmutación por error depende de la carga de trabajo actual y del último punto de control. En general, se espera que tarde entre 60 y 120 segundos.
 
### <a name="unplanned-automatic-failover"></a>No planeado: conmutación automática por error 
 
El tiempo de inactividad no planeado del servicio puede deberse a errores de software o errores de infraestructura, como errores de proceso, red o almacenamiento, o a interrupciones de energía que afectan a la disponibilidad de la base de datos. Si la base de datos deja de estar disponible, la replicación en la réplica en espera se anula y la réplica en espera se activa para ser la base de datos principal. Se actualiza el DNS y los clientes se vuelven a conectar al servidor de bases de datos y reanudan sus operaciones. 

Se espera que el tiempo total de la conmutación por error esté entre 60 y 120 segundos. Pero en función de la actividad del servidor de bases de datos principal en el momento de la conmutación por error (como las transacciones de gran tamaño y el tiempo de recuperación), la conmutación por error puede tardar más.

## <a name="monitoring-for-high-availability"></a>Supervisión de la alta disponibilidad
El estado de la alta disponibilidad se supervisa y se informa de manera continuada en la página de información general. Estos son los estados de replicación:

| **Estado** | **Descripción** |
| :----- | :------ |
| **NotEnabled** | La alta disponibilidad con redundancia de zona no está habilitada. |
| **ReplicatingData** | Una vez que se ha creado el servidor en espera, se pone al día con el servidor principal. |
| **FailingOver** | El servidor de bases de datos está en proceso de conmutar por error desde el principal al de espera. |
| **Healthy** | La alta disponibilidad con redundancia de zona está en estado estable y es correcta. |
| **RemovingStandby** | Un usuario ha eliminado la réplica en espera y la eliminación está en proceso.| 

##  <a name="limitations"></a>Limitaciones 
 
Estas son algunas consideraciones que se deben tener en cuenta al usar la alta disponibilidad:
* La alta disponibilidad con redundancia de zona solo se puede establecer cuando se crea el servidor flexible.
* La alta disponibilidad no se admite en el nivel de proceso ampliable.
* Al reiniciar el servidor de bases de datos principal para elegir los cambios de los parámetros estáticos también se reinicia la réplica en espera.
* No se admiten réplicas de lectura para servidores de alta disponibilidad con redundancia de zona.
* No se admite la replicación de datos de entrada para servidores de alta disponibilidad. 
* Se activará el modo GTID, ya que la solución de alta disponibilidad usa GTID. Compruebe si la carga de trabajo tiene [restricciones o limitaciones en cuanto a la replicación con GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids-restrictions.html).  
 
## <a name="frequently-asked-questions-faq"></a>Preguntas más frecuentes
 
**¿Puedo usar la réplica en espera para operaciones de lectura o escritura?** </br>
El servidor en espera no está disponible para operaciones de lectura o escritura. Es un modo de espera pasivo para permitir una conmutación por error rápida.</br>
**¿Se produce alguna pérdida de datos en caso de conmutación por error?**</br>
En ZRS, los registros son accesibles incluso cuando el servidor principal no está disponible. Esta disponibilidad ayuda a garantizar que no se pierdan datos. Una vez que se activa la réplica en espera y se aplican los registros binarios, esta adopta los roles del servidor principal. </br>
**¿Es necesario realizar alguna acción después de una conmutación por error?**</br>
Las conmutaciones por error son totalmente transparentes desde la aplicación cliente. No tiene que realizar ninguna acción. La aplicación solo debe utilizar una lógica de reintento para sus conexiones. </br>
**¿Qué ocurre si no selecciono una zona específica para mi réplica en espera? ¿Puedo cambiar la zona más adelante?**</br>
Si no elige una zona, se seleccionará una aleatoriamente. No será la que se usa para el servidor principal. Para cambiar la zona más adelante, puede establecer **Modo de alta disponibilidad** en **Deshabilitado** en el panel **Alta disponibilidad** y luego volver a establecerlo en **Redundancia de zona** y elegir una zona.</br>
**¿La replicación entre la réplica principal y la réplica en espera es sincrónica?**</br>
 La replicación entre las réplicas principal y en espera es similar al [modo semisincrónico](https://dev.mysql.com/doc/refman/5.7/en/replication-semisync.html) de MySQL. Cuando se confirma una transacción, no se confirma necesariamente en la réplica en espera. Pero cuando la principal no está disponible, la réplica en espera replica todos los cambios de datos de la principal para asegurarse de que no haya pérdida de datos.</br> 
 **¿Se conmuta por error en la réplica en espera en caso de errores no planeados?**</br>
Si hay un bloqueo en la base de datos o un error del nodo, la máquina virtual del servidor flexible se reinicia en el mismo nodo. Al mismo tiempo, se desencadena una conmutación automática por error. Si el reinicio de la máquina virtual del servidor flexible se realiza correctamente antes de que finalice la conmutación por error, se cancelará la operación de conmutación por error. La determinación del servidor que se usará como réplica principal depende del proceso que finalice primero.</br>
**¿Hay un impacto en el rendimiento cuando se usa la alta disponibilidad?**</br>
Es posible que la alta disponibilidad con redundancia de zona provoque una caída de la latencia del 5 al 10 % si la aplicación se conecta al servidor de bases de datos por medio de zonas de disponibilidad donde la latencia de red es relativamente mayor (de 2 a 4 ms). En el caso de la alta disponibilidad en la misma zona, dado que la réplica principal y la réplica en espera están en la misma zona, el retraso de replicación es menor. Hay menos latencia entre el servidor de aplicaciones y el servidor de bases de datos cuando están en la misma zona de disponibilidad de Azure.</br>
**¿Cómo se lleva a cabo el mantenimiento de mi servidor de alta disponibilidad?**</br>
Los eventos planeados, como el escalado de proceso y las actualizaciones de versiones secundarias, se producen en el servidor principal y en espera al mismo tiempo. La [ventana de mantenimiento programado](./concepts-maintenance.md) de los servidores de alta disponibilidad se puede establecer igual que para los servidores flexibles. El tiempo de inactividad será el mismo que en la opción Servidor flexible de Azure Database for MySQL cuando la alta disponibilidad está deshabilitada. El uso del mecanismo de conmutación por error para reducir el tiempo de inactividad de los servidores de alta disponibilidad está en nuestra hoja de ruta y estará disponible pronto. </br>
**¿Puedo realizar una restauración a un momento dado (PITR) de mi servidor de alta disponibilidad?**</br>
Puede hacer una [PITR](./concepts-backup-restore.md#point-in-time-restore) de un servidor flexible de Azure Database for MySQL con la alta disponibilidad habilitada en un nuevo servidor flexible de Azure Database for MySQL con la alta disponibilidad deshabilitada. Si el servidor de origen se ha creado con alta disponibilidad con redundancia de zona, puede habilitar la alta disponibilidad con redundancia de zona o en la misma zona en el servidor restaurado más adelante. Si el servidor de origen se ha creado con alta disponibilidad en la misma zona, solo puede habilitar la alta disponibilidad en la misma zona en el servidor restaurado.</br>
**¿Puedo habilitar la alta disponibilidad en un servidor después de crear el servidor?**</br>
Después de crear el servidor, puede habilitar la alta disponibilidad en la misma zona. La alta disponibilidad con redundancia de zona se debe habilitar cuando se crea el servidor.</br> 
 **¿Puedo deshabilitar la alta disponibilidad en un servidor después de crearlo?** </br>
Puede deshabilitar la alta disponibilidad en un servidor después de crearlo. La facturación se detiene inmediatamente.  </br>
**¿Cómo puedo mitigar el tiempo de inactividad?**</br>
Aunque no use la alta disponibilidad, debe poder mitigar el tiempo de inactividad de la aplicación. El tiempo de inactividad del servicio, como las revisiones programadas, las actualizaciones de versiones secundarias o las operaciones iniciadas por el cliente, como el escalado de proceso, se pueden realizar durante las ventanas de mantenimiento programado. Para mitigar el impacto en la aplicación de las tareas de mantenimiento iniciadas por Azure, puede optar por programarlas durante el día de la semana y la hora que menos afecten a la aplicación.</br>
**¿Puedo usar una réplica de lectura para un servidor habilitado para la alta disponibilidad?**</br>
No se admiten réplicas de lectura para los servidores de alta disponibilidad. Esta característica está en nuestra hoja de ruta y estamos trabajando para que esté disponible pronto.</br>
**¿Puedo usar la replicación de datos de entrada para los servidores de alta disponibilidad?**</br>
No se admite la replicación de datos de entrada para servidores de alta disponibilidad. Pero la replicación de datos de entrada para servidores de alta disponibilidad está en nuestra hoja de ruta y estará disponible pronto. Por ahora, si desea usar la replicación de datos de entrada para la migración, puede seguir estos pasos:
1. Cree el servidor con la alta disponibilidad con redundancia de zona habilitada.
1. Deshabilite la alta disponibilidad.
1. Lleve a cabo los pasos para [configurar la replicación de datos de entrada](./concepts-data-in-replication.md).  (Asegúrese de que `gtid_mode` tenga el mismo valor en los servidores de origen y de destino).
1. Tras la migración, elimine la configuración de la replicación de datos de entrada.
1. Habilite la alta disponibilidad.

**¿Puedo realizar la conmutación por error al servidor en espera durante los reinicios del servidor o al escalar o reducir verticalmente para reducir el tiempo de inactividad?** </br>
Actualmente, cuando se realiza una operación de escalado vertical o de reducción vertical, el servidor en espera y el principal se escalan al mismo tiempo. Por lo tanto, la conmutación por error no ayuda. Permitir primero el escalado vertical del servidor en espera, seguido de la conmutación por error y, a continuación, el escalado vertical del servidor principal está en nuestra hoja de ruta, pero aún no se admite.</br>


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [continuidad empresarial](./concepts-business-continuity.md).
- Más información sobre la [alta disponibilidad con redundancia de zona](./concepts-high-availability.md).
- Más información sobre la [copia de seguridad y recuperación](./concepts-backup-restore.md).
