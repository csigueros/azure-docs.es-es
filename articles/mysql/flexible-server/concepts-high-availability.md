---
title: Información general de alta disponibilidad con redundancia de zona con el servidor flexible de Azure Database for MySQL
description: Información sobre los conceptos de alta disponibilidad con redundancia de zona con el servidor flexible de Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 08/10/2021
ms.openlocfilehash: d78bb5aeb111411641508b38bd49a6f5bcbf5374
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122177851"
---
# <a name="high-availability-concepts-in-azure-database-for-mysql-flexible-server-preview"></a>Conceptos de alta disponibilidad en el servidor flexible de Azure Database for MySQL (versión preliminar)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT] 
> Actualmente, la opción Servidor flexible de Azure Database for MySQL se encuentra en versión preliminar pública.

Servidor flexible de Azure Database for MySQL (versión preliminar) permite configurar la alta disponibilidad con conmutación automática por error. Cuando se configura la alta disponibilidad, el servidor flexible aprovisiona y administra automáticamente una réplica en espera mediante dos opciones diferentes.

* **Alta disponibilidad con redundancia de zona**: esta opción es preferible para el aislamiento completo y la redundancia de la infraestructura en varias zonas de disponibilidad. Proporciona el máximo nivel de disponibilidad, pero es necesario configurar la redundancia de la aplicación entre las zonas. La alta disponibilidad con redundancia de zona es preferible cuando se quiere lograr el máximo nivel de disponibilidad frente a cualquier error de infraestructura en la zona de disponibilidad y donde la latencia en la zona de disponibilidad sea aceptable. La alta disponibilidad con redundancia de zona está disponible en un [subconjunto de regiones de Azure](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions) donde la región admite varias zonas de disponibilidad y la alta disponibilidad con redundancia de zona está disponible.

* **Alta disponibilidad en la misma zona**: esta opción es preferible para la redundancia de infraestructura con menor latencia de red, ya que el servidor principal y el servidor en espera estarán en la misma zona de disponibilidad. Proporciona alta disponibilidad sin configurar la redundancia de la aplicación entre zonas. Alta disponibilidad en la misma zona es preferible cuando se quiere lograr el mayor nivel de disponibilidad dentro de una sola zona de disponibilidad con la latencia de red más baja. Alta disponibilidad en la misma zona está disponible en todas las [regiones de Azure en las que está disponible el servidor flexible](https://docs.microsoft.com/azure/mysql/flexible-server/overview#azure-regions).  

## <a name="zone-redundant-high-availability"></a>Alta disponibilidad con redundancia de zona

Cuando se crea el servidor flexible con alta disponibilidad con redundancia de zona habilitada, los archivos de datos y de registro se hospedan en un [almacenamiento con redundancia de zona (ZRS)](../../storage/common/storage-redundancy.md#redundancy-in-the-primary-region). Con la replicación de nivel de almacenamiento disponible con ZRS, los archivos de datos y de registro se replican sincrónicamente en el servidor en espera para garantizar que no se pierdan datos. La conmutación por error es totalmente transparente desde la aplicación cliente y no requiere ninguna acción por parte del usuario. La recuperación del servidor en espera para conectarse durante la conmutación por error depende de la aplicación de registro binario en espera. Por tanto, se recomienda usar claves principales en todas las tablas para reducir el tiempo de conmutación por error. El servidor en espera no está disponible para las operaciones de lectura o escritura, pero es un estado de espera pasivo para habilitar la conmutación por error rápida. Los tiempos de conmutación por error suelen oscilar entre 60-120 segundos.

> [!Note]
> Es posible que la alta disponibilidad con redundancia de zona provoque una caída de la latencia del 5 al 10 % si la aplicación se conecta al servidor de bases de datos por medio de zonas de disponibilidad donde la latencia de red es relativamente mayor, de entre 2 a 4 ms. 

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Alta disponibilidad con redundancia de zona":::

### <a name="zone-redundancy-architecture"></a>Arquitectura de la redundancia de zona

El servidor principal se implementa en la región y en una zona de disponibilidad concreta. Cuando se elige la alta disponibilidad, se implementa de forma automática un servidor de réplica en espera con la misma configuración que el servidor principal en la "zona de disponibilidad especificada", incluidos el nivel y el tamaño de proceso, el tamaño de almacenamiento y la configuración de red. Los datos de registro se replican sincrónicamente en la réplica en espera para garantizar que no se produzca ninguna pérdida de datos en cualquier situación de error. Las copias de seguridad automáticas, tanto las de instantáneas como las de registros, se realizan en un almacenamiento con redundancia de zona desde el servidor de bases de datos principal.

### <a name="standby-zone-selection"></a>Selección de zona en espera
En el escenario de alta disponibilidad con redundancia de zona, puede elegir la ubicación de la zona de servidor en espera que prefiera. La colocación de los servidores de bases de datos y las aplicaciones en espera en la misma zona reduce las latencias y permite a los usuarios prepararse mejor para situaciones de recuperación ante desastres y escenarios de "zona fuera de servicio".

## <a name="same-zone-high-availability"></a>Alta disponibilidad en la misma zona

Cuando se crea el servidor flexible con alta disponibilidad en la misma zona habilitada, los archivos de datos y de registro se hospedan en un [almacenamiento con redundancia local (LRS)](https://docs.microsoft.com/azure/storage/common/storage-redundancy#locally-redundant-storage). Con la replicación de nivel de almacenamiento disponible con LRS, los archivos de datos y de registro se replican sincrónicamente en el servidor en espera para garantizar que no se pierdan datos. El servidor en espera ofrece redundancia de infraestructura con una máquina virtual independiente (proceso) que reduce el tiempo de conmutación por error y la latencia de red entre la aplicación de usuario y el servidor de bases de datos debido a la coubicación. La conmutación por error es totalmente transparente desde la aplicación cliente y no requiere ninguna acción por parte del usuario. La recuperación del servidor en espera para conectarse durante la conmutación por error depende de la aplicación de registro binario en espera. Por tanto, se recomienda usar claves principales en todas las tablas para reducir el tiempo de conmutación por error. El servidor en espera no está disponible para las operaciones de lectura o escritura, pero es un estado de espera pasivo para habilitar la conmutación por error rápida. Los tiempos de conmutación por error suelen oscilar entre 60-120 segundos.

La alta disponibilidad en la misma zona permite a los usuarios colocar un servidor en espera en la misma zona que el servidor principal, lo que reduce el retraso de replicación entre el servidor principal y el que está en espera. Esto también proporciona latencias inferiores entre el servidor de aplicaciones y el servidor de bases de datos si se colocan dentro de la misma zona de disponibilidad de Azure.

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Alta disponibilidad en la misma zona":::

## <a name="high-availability-monitoring"></a>Supervisión de la alta disponibilidad
El estado de la alta disponibilidad se supervisa y se registra de manera continuada en la página de información general. A continuación se enumeran los distintos estados de replicación:

| **Estado** | **Descripción** |
| :----- | :------ |
| <b>NotEnabled | La alta disponibilidad con redundancia de zona no está habilitada |
| <b>ReplicatingData | Una vez que se ha creado el servidor en espera, se pone al día con el servidor principal. |
| <b>FailingOver | El servidor de bases de datos está en proceso de conmutar por error desde el principal al de espera. |
| <b>Correcto | La alta disponibilidad con redundancia de zona está en estado estable y es correcta. |
| <b>RemovingStandby | En función de la acción del usuario, la réplica en espera se encuentra en proceso de eliminación.| 

## <a name="advantages"></a>Ventajas

Estas son algunas ventajas de usar la característica de alta disponibilidad con redundancia de zona: 

- La réplica en espera se implementa en una configuración de máquina virtual exacta como la principal, como núcleos virtuales, almacenamiento, configuración de red (VNET, firewall), etc.
- Se puede quitar la réplica en espera si se deshabilita la alta disponibilidad.
- Las copias de seguridad automáticas se basan en instantáneas, se realizan desde el servidor de bases de datos principal y se almacenan en un almacenamiento con redundancia de zona o con redundancia local, según la opción de alta disponibilidad.
- En caso de conmutación por error, el servidor flexible de Azure Database for MySQL conmuta automáticamente por error a la réplica en espera si la alta disponibilidad está habilitada. La configuración de alta disponibilidad supervisa el servidor principal y lo vuelve a poner en línea.
- Los clientes siempre se conectan al servidor de bases de datos principal.
- Si hay un bloqueo en la base de datos o un error de nodos, la máquina virtual del servidor flexible se reinicia en el mismo nodo. Al mismo tiempo, se desencadena una conmutación automática por error. Si el reinicio de la máquina virtual del servidor flexible se realiza correctamente antes de que finalice la conmutación por error, se cancelará la operación de conmutación por error.
- Capacidad de reiniciar el servidor para seleccionar cualquier cambio de parámetro de servidor estático.

## <a name="steady-state-operations"></a>Operaciones de estado estable

Las aplicaciones se conectan al servidor principal mediante el nombre del servidor de bases de datos. La información de la réplica en espera no se expone para el acceso directo. Las confirmaciones y escrituras se confirman después de vaciar los archivos de registro en el almacenamiento con redundancia de zona (ZRS) del servidor principal. Debido a la tecnología de replicación de sincronización que se usa en el almacenamiento de ZRS, las aplicaciones pueden esperar una ligera latencia para las escrituras y confirmaciones.

## <a name="failover-process"></a>Proceso de conmutación por error 
Para la continuidad empresarial, debe tener un proceso de conmutación por error para eventos planeados y no planeados. 

>[!NOTE]
> Use siempre el nombre de dominio completo (FQDN) para conectarse al servidor principal y evite usar la dirección IP para conectarse. En caso de conmutación por error, después de cambiar los roles de servidor principal y en espera, el registro de DNS A también podría cambiar, lo que impedirá que la aplicación se conecte al nuevo servidor principal si se usa la dirección IP en la cadena de conexión. 

### <a name="planned-events---forced-failover"></a>Eventos planeados: conmutación por error forzada

La conmutación por error forzada de Azure Database for MySQL le permite forzar manualmente una conmutación por error. Esto a su vez le permite probar la funcionalidad con los escenarios de aplicación y le ayuda a estar listo en caso de que se produzcan interrupciones. La conmutación por error forzada cambia el servidor en espera para que se convierta en el servidor principal mediante el desencadenamiento de una conmutación por error que activa la réplica en espera para que se convierta en el servidor principal con el mismo nombre de servidor de base de datos mediante la actualización del registro DNS. El servidor principal original se reiniciará y se cambiará a la réplica en espera. Las conexiones de cliente se desconectan y deben volver a conectarse para reanudar sus operaciones. Según la carga de trabajo actual y el último punto de control, se medirá el tiempo total de conmutación por error. En general, se espera que oscile entre 60 y 120 s. 

### <a name="failover-process---unplanned-events"></a>Proceso de conmutación por error: eventos no planeados
Los tiempos de inactividad del servicio no planeados incluyen errores de software o de infraestructura, como los de procesos, redes y almacenamiento, o bien interrupciones de energía que afectan a la disponibilidad de la base de datos. En caso de que la base de datos no esté disponible, la replicación en la réplica en espera se anula y la réplica en espera se activa para ser la base de datos principal. El DNS se actualiza y, después, los clientes se vuelven a conectar al servidor de bases de datos y reanudan sus operaciones. Se espera que el tiempo de conmutación por error global sea de 60-120 segundos. Pero en función de la actividad del servidor de bases de datos principal en el momento de la conmutación por error (como las transacciones de gran tamaño y el tiempo de recuperación) la conmutación por error puede tardar más.

## <a name="schedule-maintenance-window"></a>Programación de la ventana de mantenimiento 

Los servidores flexibles ofrecen una capacidad de programación de mantenimiento en la que puede elegir un período de 30 minutos en un día concreto durante el cual el mantenimiento de Azure funciona como las revisiones o las actualizaciones de versiones secundarias. En el caso de los servidores flexibles configurados con alta disponibilidad, estas actividades de mantenimiento se realizan primero en la réplica en espera. 

## <a name="point-in-time-restore"></a>Restauración a un momento dado 
Dado que el servidor flexible está configurado en alta disponibilidad y replica datos de forma sincrónica, el servidor en espera está actualizado con el principal. Los errores de usuario en el servidor principal, como la eliminación accidental de una tabla o las actualizaciones incorrectas, se replican fielmente en el servidor en espera. Por tanto, no puede usar el servidor en espera para recuperarse de estos errores lógicos. Para recuperarse de estos errores lógicos, tendrá que realizar una restauración a un momento dado justo antes de que se haya producido el error. Con la funcionalidad de restauración a un momento dado del servidor flexible, al restaurar la base de datos configurada con alta disponibilidad, se restaura un nuevo servidor de bases de datos como un nuevo servidor flexible con un nombre proporcionado por el usuario. Después, puede exportar el objeto desde el servidor de bases de datos e importarlo en el servidor de bases de datos de producción. Del mismo modo, si quiere clonar el servidor de bases de datos con fines de prueba y desarrollo, o bien realizar la restauración para cualquier otro fin, puede elegir el punto de restauración más reciente o uno personalizado. La operación de restauración crea un servidor flexible de una sola zona.

## <a name="mitigate-downtime"></a>Mitigación del tiempo de inactividad 
Cuando no use la característica de alta disponibilidad con redundancia de zona, todavía podrá mitigar el tiempo de inactividad de la aplicación. La planeación de tiempos de inactividad del servicio, como revisiones programadas, actualizaciones de versiones secundarias o las operaciones iniciadas por el usuario, se puede realizar como parte de ventanas de mantenimiento programadas. Los tiempos de inactividad del servicio planeados, como las revisiones programadas, las actualizaciones de versiones secundarias o las operaciones iniciadas por el usuario como las de escalado de proceso, incurren en tiempo de inactividad. Para mitigar el impacto de la aplicación en las tareas de mantenimiento iniciadas por Azure, puede optar por programarlas durante el día de la semana y la hora que menos afecten a la aplicación. 

Durante los eventos de tiempo de inactividad no planeados, como el bloqueo de la base de datos o el error del servidor, el servidor afectado se reinicia dentro de la misma zona. Aunque es poco frecuente, si toda la zona resulta afectada, puede restaurar la base de datos en otra zona dentro de la región. 

## <a name="things-to-know-with-zone-redundancy"></a>Aspectos que se deben conocer sobre la redundancia de zona 

Estas son algunas consideraciones que debe tener en cuenta al usar alta disponibilidad con redundancia de zona: 

- La alta disponibilidad **solo** se puede establecer durante el tiempo de creación del servidor flexible.
- La alta disponibilidad no se admite en el nivel de proceso flexible.
- Debido a la replicación sincrónica en otra zona de disponibilidad, el servidor de bases de datos principal puede experimentar una latencia elevada de escritura y confirmación.
- No se puede usar la réplica en espera para las consultas de solo lectura.
- En función de la actividad del servidor principal en el momento de la conmutación por error, la conmutación por error puede tardar hasta 60-120 segundos o más en completarse.
- Al reiniciar el servidor de bases de datos principal para aplicar los cambios de los parámetros estáticos también se reinicia la réplica en espera.
- No se admite la configuración de réplicas de lectura para servidores de alta disponibilidad con redundancia de zona.
- La configuración de las tareas de administración iniciadas por el cliente no se puede automatizar durante la ventana de mantenimiento administrado.
- Los eventos planeados, como la escala de proceso y las actualizaciones de versiones secundarias, se producen tanto en el servidor principal como en el servidor en espera al mismo tiempo. 


## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la [continuidad empresarial](./concepts-business-continuity.md)
- Obtenga información sobre la [alta disponibilidad con redundancia de zona](./concepts-high-availability.md)
- Más información sobre [copia de seguridad y recuperación](./concepts-backup-restore.md)