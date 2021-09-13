---
title: Información general de alta disponibilidad con redundancia de zona con el servidor flexible de Azure Database for MySQL
description: Información sobre los conceptos de alta disponibilidad con redundancia de zona con el servidor flexible de Azure Database for MySQL
author: SudheeshGH
ms.author: sunaray
ms.service: mysql
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: 4a8f7d5bd10c21b14c8935bf0a44040501ebeb90
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123109194"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Conceptos de alta disponibilidad en el servidor flexible de Azure Database for MySQL (versión preliminar)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT] 
> Actualmente, la opción Servidor flexible de Azure Database for MySQL se encuentra en versión preliminar pública.

Servidor flexible de Azure Database for MySQL (versión preliminar) permite configurar la alta disponibilidad con conmutación automática por error. La solución de alta disponibilidad está diseñada para garantizar que los datos confirmados nunca se pierdan debido a errores y que la base de datos no sea un único punto de error de la arquitectura de software. Cuando se configura la alta disponibilidad, el servidor flexible aprovisiona y administra automáticamente una réplica en espera. Hay dos modelos arquitectónicos de alta disponibilidad:

* **Alta disponibilidad con redundancia de zona**: esta opción es preferible para lograr el aislamiento completo y la redundancia de la infraestructura en varias zonas de disponibilidad. Proporciona el máximo nivel de disponibilidad, pero exige configurar la redundancia de aplicaciones entre zonas. La alta disponibilidad con redundancia de zona es preferible cuando se quiere lograr el máximo nivel de disponibilidad frente a cualquier error de infraestructura en la zona de disponibilidad y donde la latencia en la zona de disponibilidad sea aceptable. La alta disponibilidad con redundancia de zona solo se puede habilitar en el momento de la creación del servidor. La alta disponibilidad con redundancia de zona está disponible en un [subconjunto de regiones de Azure](./overview.md#azure-regions) que admiten varias [zonas de disponibilidad](../../availability-zones/az-overview.md) y donde hay disponibles [recursos compartidos de archivos premium con redundancia de zona](../..//storage/common/storage-redundancy.md#zone-redundant-storage).

* **Alta disponibilidad en la misma zona**: esta opción es preferible para la redundancia de infraestructura con menor latencia de red, ya que el servidor principal y el servidor en espera están en la misma zona de disponibilidad. Proporciona alta disponibilidad sin configurar la redundancia de la aplicación entre zonas. La alta disponibilidad en la misma zona se prefiere cuando se quiere lograr el máximo nivel de disponibilidad dentro de una sola zona de disponibilidad con la mínima latencia de red. La alta disponibilidad en la misma zona está disponible en [todas las regiones de Azure](./overview.md#azure-regions) donde es posible crear servidores flexibles de Azure Database for MySQL.  

## <a name="zone-redundancy-high-availability-architecture"></a>Arquitectura de alta disponibilidad con redundancia de zona
 
Al implementar un servidor con la opción de alta disponibilidad con redundancia de zona, se dispone de un servidor principal creado en una zona de disponibilidad y un servidor de réplica en espera con la misma configuración que el servidor principal (nivel de proceso, tamaño de proceso, tamaño de almacenamiento y configuración de red) en otra zona de disponibilidad de la misma región de Azure. Puede especificar la zona de disponibilidad que prefiera para la réplica principal y en espera. La colocación de los servidores de bases de datos y las aplicaciones en espera en la misma zona reduce las latencias y permite a los usuarios prepararse mejor para situaciones de recuperación ante desastres y escenarios de "zona fuera de servicio".

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Alta disponibilidad con redundancia de zona":::

Los datos y los archivos de registro se hospedan en un [almacenamiento con redundancia de zona (ZRS)](../../storage/common/storage-redundancy.md#redundancy-in-the-primary-region). Con la replicación de nivel de almacenamiento disponible con ZRS, los datos y los archivos de registro se replican en el servidor en espera. Si se produce una conmutación por error, se activa la réplica en espera y los archivos de registro binarios del servidor principal se siguen aplicando en el servidor en espera para conectarlo a la última transacción confirmada en el servidor principal. Gracias a los registros de ZRS, los registros son accesibles incluso en aquellos casos en que el servidor principal no está disponible, lo que ayuda a garantizar que no se pierdan datos. Una vez que se activa la réplica en espera y se aplican los registros binarios, el servidor de réplica en espera actual adopta los roles del servidor principal. DNS se actualiza para que las conexiones del cliente se dirijan a la nueva réplica principal cuando el cliente se vuelva a conectar. La conmutación por error es totalmente transparente desde la aplicación cliente y no requiere ninguna acción por parte del usuario. Luego, en cuanto es posible, la solución de alta disponibilidad reactiva el servidor principal antiguo y lo coloca como servidor en espera.
 
Las aplicaciones se conectan al servidor principal mediante el nombre del servidor de bases de datos. La información de la réplica en espera no se expone para el acceso directo. Las confirmaciones y escrituras se confirman después de vaciar los archivos de registro en el almacenamiento con redundancia de zona (ZRS) del servidor principal. Debido a la tecnología de replicación de sincronización que se usa en el almacenamiento de ZRS, las aplicaciones pueden esperar un aumento de entre el 5 % y el 10 % de latencia en las escrituras y confirmaciones.
 
Las copias de seguridad automáticas, tanto las de instantáneas como las de registros, se realizan en un almacenamiento con redundancia de zona desde el servidor de bases de datos principal.

## <a name="same-zone-high-availability-architecture"></a>Arquitectura de alta disponibilidad en la misma zona
 
Al implementar un servidor con la opción de alta disponibilidad en la misma zona, se dispone de un servidor principal y un servidor de réplica en espera creados en la misma zona con la misma configuración que el servidor principal (nivel de proceso, tamaño de proceso, tamaño de almacenamiento y configuración de red). El servidor en espera ofrece redundancia de infraestructura con una máquina virtual independiente (proceso) que reduce el tiempo de conmutación por error y la latencia de red entre la aplicación de usuario y el servidor de bases de datos debido a la coubicación.

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Alta disponibilidad en la misma zona":::

Los datos y los archivos de registro se hospedan en un [almacenamiento con redundancia local (LRS)](../../storage/common/storage-redundancy.md#locally-redundant-storage). Con la replicación de nivel de almacenamiento disponible con LRS, los datos y los archivos de registro se replican en el servidor en espera.

Si se produce una conmutación por error, se activa la réplica en espera y los archivos de registro binarios del servidor principal se siguen aplicando en el servidor en espera para conectarlo a la última transacción confirmada en el servidor principal. Gracias a los registros de LRS, los registros son accesibles incluso en aquellos casos en que el servidor principal no está disponible, lo que ayuda a garantizar que no se pierdan datos. Una vez que se activa la réplica en espera y se aplican los registros binarios, la réplica en espera actual adopta los roles del servidor principal. DNS se actualiza para redirigir las conexiones a la nueva réplica principal cuando el cliente se vuelve a conectar. La conmutación por error es totalmente transparente desde la aplicación cliente y no requiere ninguna acción por parte del usuario. Luego, en cuanto es posible, la solución de alta disponibilidad reactiva el servidor principal antiguo y lo coloca como servidor en espera.
 
Las aplicaciones se conectan al servidor principal mediante el nombre del servidor de bases de datos. La información de la réplica en espera no se expone para el acceso directo. Las confirmaciones y escrituras se confirman después de vaciar los archivos de registro en el almacenamiento con redundancia de zona local (LRS) del servidor principal. Como la réplica principal y en espera están en la misma zona, el retraso de replicación es menor y proporciona latencias menores entre el servidor de aplicaciones y el servidor de bases de datos si están colocados dentro de la misma zona de disponibilidad de Azure. La configuración de misma zona no proporciona alta disponibilidad en el escenario en que las infraestructuras dependientes quedan sin servicio en la zona de disponibilidad en cuestión.   Hay un tiempo de inactividad hasta que todos los servicios dependientes vuelven a estar en línea en esa zona de disponibilidad.
 
Las copias de seguridad automáticas, tanto las instantáneas como las copias de seguridad de registros, se realizan en un almacenamiento con redundancia local desde el servidor de bases de datos principal.

>[!Note]
>En el caso de la alta disponibilidad con redundancia de zona y en la misma zona,
>* si hay un error, el tiempo necesario para que la réplica en espera adopte el rol de principal depende de la aplicación de registro binario en la réplica en espera. Por tanto, se recomienda usar claves principales en todas las tablas para reducir el tiempo de conmutación por error. Los tiempos de conmutación por error suelen oscilar entre 60-120 segundos. 
>* El servidor en espera no está disponible para las operaciones de lectura o escritura, pero es un estado de espera pasivo para habilitar la conmutación por error rápida.
>* Use siempre el nombre de dominio completo (FQDN) para conectarse al servidor principal y evite usar la dirección IP para conectarse. En caso de conmutación por error, después de cambiar los roles de servidor principal y en espera, el registro de DNS A también podría cambiar, lo que impedirá que la aplicación se conecte al nuevo servidor principal si se usa la dirección IP en la cadena de conexión.

## <a name="failover-process"></a>Proceso de conmutación por error 
 
### <a name="planned---forced-failover"></a>Conmutación por error forzada planeada
 
La conmutación por error forzada de un servidor flexible de Azure Database for MySQL permite forzar manualmente una conmutación por error. Esto, a su vez, permite probar la funcionalidad con los escenarios de aplicación y ayuda a estar listo ante interrupciones. La conmutación por error forzada cambia el servidor en espera a servidor principal mediante el desencadenamiento de una conmutación por error que activa la réplica en espera para que se convierta en el servidor principal con el mismo nombre de servidor de bases de datos al actualizar el registro DNS. El servidor principal original se reiniciará y se cambiará a la réplica en espera. Las conexiones de cliente se desconectan y deben volver a conectarse para reanudar sus operaciones. Según la carga de trabajo actual y el último punto de control, se medirá el tiempo total de conmutación por error. En general, se espera que oscile entre 60 y 120 segundos.
 
### <a name="unplanned--automatic-failover"></a>Conmutación automática por error no planeada 
 
Los tiempos de inactividad no planeados del servicio incluyen errores de software o fallos de infraestructura, como errores de proceso, red y almacenamiento, o bien cortes de energía que afectan a la disponibilidad de la base de datos. En caso de que la base de datos no esté disponible, la replicación en la réplica en espera se anula y la réplica en espera se activa para ser la base de datos principal. El DNS se actualiza y, después, los clientes se vuelven a conectar al servidor de bases de datos y reanudan sus operaciones. Se espera que el tiempo de conmutación por error global sea de 60-120 segundos. Pero en función de la actividad del servidor de bases de datos principal en el momento de la conmutación por error (como las transacciones de gran tamaño y el tiempo de recuperación) la conmutación por error puede tardar más.

## <a name="high-availability-monitoring"></a>Supervisión de la alta disponibilidad
El estado de la alta disponibilidad se supervisa y se registra de manera continuada en la página de información general. A continuación se enumeran los distintos estados de replicación:

| **Estado** | **Descripción** |
| :----- | :------ |
| <b>NotEnabled | La alta disponibilidad con redundancia de zona no está habilitada |
| <b>ReplicatingData | Una vez que se ha creado el servidor en espera, se pone al día con el servidor principal. |
| <b>FailingOver | El servidor de bases de datos está en proceso de conmutar por error desde el principal al de espera. |
| <b>Correcto | La alta disponibilidad con redundancia de zona está en estado estable y es correcta. |
| <b>RemovingStandby | En función de la acción del usuario, la réplica en espera se encuentra en proceso de eliminación.| 

##  <a name="limitations"></a>Limitaciones 
 
Estas son algunas consideraciones que se deben tener en cuenta al usar la alta disponibilidad:
* La alta disponibilidad con redundancia de zona solo se puede establecer durante la creación del servidor flexible.
* La alta disponibilidad no se admite en el nivel de proceso flexible.
* Al reiniciar el servidor de bases de datos principal para aplicar los cambios de los parámetros estáticos también se reinicia la réplica en espera.
* No se admite la configuración de réplicas de lectura para servidores de alta disponibilidad con redundancia de zona.
* No se admite la replicación de datos de entrada para servidores de alta disponibilidad. 
* El modo GTID se establece en ON, ya que la solución de alta disponibilidad usa GTID. Confirme si la carga de trabajo tiene [restricciones o limitaciones en cuanto a la replicación con GTID](https://dev.mysql.com/doc/refman/5.7/en/replication-gtids-restrictions.html).  
 
## <a name="frequently-asked-questions-faqs"></a>Preguntas más frecuentes (P+F)
 
**¿Puedo usar la réplica en espera para operaciones de lectura o escritura?** </br>
El servidor en espera no está disponible para las operaciones de lectura o escritura, pero es un estado de espera pasivo para habilitar la conmutación por error rápida.</br>
**¿Se produce alguna pérdida de datos en caso de conmutación por error?**</br>
Gracias a los registros de ZRS, los registros son accesibles incluso en aquellos casos en que el servidor principal no está disponible, lo que ayuda a garantizar que no se pierdan datos. Una vez que se activa la réplica en espera y se aplican los registros binarios, adopta los roles del servidor principal. </br>
**¿Es necesario que los usuarios realicen alguna acción después de la conmutación por error?**</br>
La conmutación por error es totalmente transparente desde la aplicación cliente y no requiere ninguna acción por parte del usuario. La aplicación solo debe implementar la lógica de reintento para sus conexiones. </br>
**¿Qué ocurre si no selecciono una zona específica para mi réplica en espera? ¿Puedo cambiarla más adelante?**</br>
Si no se selecciona ninguna zona, se selecciona aleatoriamente una que no sea la que se usa para el servidor principal. Para cambiar la zona más adelante, puede establecer Modo de alta disponibilidad en Deshabilitado y luego volver a establecerlo en Redundancia de zona con la zona que prefiera en la hoja Alta disponibilidad.</br>
**¿La replicación entre la réplica principal y la réplica en espera es sincrónica?**</br>
 La solución de replicación entre las réplicas principal y en espera se puede considerar similar al [modo semisincrónico](https://dev.mysql.com/doc/refman/5.7/en/replication-semisync.html) de MySQL. Si se confirma una transacción, no es necesario hacerlo en la réplica en espera.  Pero si la réplica principal no está disponible, sería bueno asegurarse de que la réplica en espera puede replicar todos los cambios de datos que tenía la principal y asegurarse de no experimentar ninguna pérdida de datos.</br> 
 **¿Se conmuta por error en la réplica en espera en caso de errores no planeados?**</br>
Si hay un bloqueo en la base de datos o un error de nodos, la máquina virtual del servidor flexible se reinicia en el mismo nodo. Al mismo tiempo, se desencadena una conmutación automática por error. Si el reinicio de la máquina virtual del servidor flexible se realiza correctamente antes de que finalice la conmutación por error, se cancelará la operación de conmutación por error. Lo opción que lleve menos tiempo decide cuál actúa como réplica principal frente a réplica en espera.</br>
**¿Hay algún impacto sobre el rendimiento al usar la solución de alta disponibilidad?**</br>
La alta disponibilidad con redundancia de zona puede ocasionar una bajada de latencia de entre el 5 y el 10 % en la aplicación que se conecta al servidor de bases de datos en zonas de disponibilidad donde la latencia de red es</br> relativamente superior, del orden de entre 2 y 4 ms. En la alta disponibilidad en la misma zona, como la réplica principal y en espera están en la misma zona, el retraso de replicación es menor y proporciona latencias menores entre el servidor de aplicaciones y el servidor de bases de datos si están colocados dentro de la misma zona de disponibilidad de Azure.</br>
**¿Cómo se produce el mantenimiento de mi servidor de alta disponibilidad?**</br>
Los eventos planeados, como la escala de proceso y las actualizaciones de versiones secundarias, se producen tanto en el servidor principal como en el servidor en espera al mismo tiempo. La [ventana de mantenimiento programado](./concepts-maintenance.md) del servidor de alta disponibilidad se puede establecer igual que para los servidores flexibles. Tendría la misma cantidad de tiempo de inactividad que para el servidor flexible de Azure Database for MySQL con la alta capacidad deshabilitada. El uso del mecanismo de conmutación por error para reducir el tiempo de inactividad de los servidores de alta disponibilidad está en la hoja de ruta y la característica pronto estará disponible. </br>
**¿Puedo realizar una restauración a un momento dado (PITR) de mi servidor de alta disponibilidad?**</br>
Puede hacer una [PITR](./concepts-backup-restore.md#point-in-time-restore) de un servidor flexible de Azure Database for MySQL con la alta disponibilidad habilitada en un nuevo servidor flexible de Azure Database for MySQL con la alta disponibilidad deshabilitada. Si el servidor de origen se ha creado con alta disponibilidad con redundancia de zona, puede habilitar la alta disponibilidad con redundancia de zona o en la misma zona en el servidor restaurado más adelante. Si el servidor de origen se ha creado con la alta disponibilidad en la misma zona, solo puede habilitar la misma zona en el servidor restaurado.</br>
**¿Puedo habilitar la alta disponibilidad para un servidor después de su creación?**</br>
Puede habilitar la alta disponibilidad en la misma zona después de la creación del servidor, pero la opción de alta disponibilidad con redundancia de zona solo se puede seleccionar en el momento de la creación.</br> 
 **¿Puedo deshabilitar la alta disponibilidad para un servidor después de su creación?** </br>
Después de la creación del servidor puede deshabilitar la alta disponibilidad, y la facturación se detiene inmediatamente.  </br>
**¿Cómo puedo mitigar el tiempo de inactividad?**</br>
Aunque no use la característica de alta disponibilidad, sigue necesitando mitigar el tiempo de inactividad de la aplicación. La planeación de los tiempos de inactividad del servicio, como las revisiones programadas, las actualizaciones de versiones secundarias, o las operaciones iniciadas por el usuario, como un cálculo a gran escala, se pueden realizar como parte de las ventanas de mantenimiento programadas, ya que conllevan tiempo de inactividad. Para mitigar el impacto de la aplicación en las tareas de mantenimiento iniciadas por Azure, puede optar por programarlas durante el día de la semana y la hora que menos afecten a la aplicación.</br>
**¿Se puede tener una réplica de lectura para un servidor habilitado para alta disponibilidad?**</br>
Actualmente, un servidor habilitado para alta disponibilidad no admite réplica de lectura. Pero la réplica de lectura de los servidores de alta disponibilidad está en la hoja de ruta y se está trabajando para que esta característica esté disponible pronto.</br>
**¿Puedo usar la replicación de datos de entrada para los servidores de alta disponibilidad?**</br>
En este momento no se admite la replicación de datos de entrada para los servidores de alta disponibilidad. Pero la replicación de datos de entrada de los servidores de alta disponibilidad está en la hoja de ruta y se está trabajando para que esta característica esté disponible pronto. Por ahora, si quiere usar la característica de replicación de datos de entrada para la migración, puede seguir estos pasos:
* Cree el servidor con la alta disponibilidad con redundancia de zona habilitada.
* Deshabilite la alta disponibilidad.
* Siga el artículo sobre la [configuración de la replicación de datos de entrada](./concepts-data-in-replication.md). (Asegúrese de que GTID_Mode tiene el mismo valor en los servidores de origen y destino).
* Tras la migración, elimine la configuración de la replicación de datos de entrada.
* Habilite la alta disponibilidad.

**¿Puedo realizar la conmutación por error al servidor en espera durante los reinicios del servidor o al escalar o reducir verticalmente para reducir el tiempo de inactividad?** </br>
Actualmente, cuando se realiza una operación de escalado o reducción vertical, los servidores en espera y principal se escalan al mismo tiempo, por lo que la conmutación por error no ayuda, pero permitir primero el escalado vertical en el servidor en espera, seguido de la conmutación por error y el escalado vertical del servidor principal está en el mapa de ruta, aunque aún no se admite.</br>


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [continuidad empresarial](./concepts-business-continuity.md)
- Obtenga información sobre la [alta disponibilidad con redundancia de zona](./concepts-high-availability.md)
- Más información sobre [copia de seguridad y recuperación](./concepts-backup-restore.md)