---
title: 'Información general de alta disponibilidad con redundancia de zona con Azure Database for PostgreSQL: Servidor flexible (versión preliminar)'
description: 'Obtenga información sobre los conceptos de alta disponibilidad con redundancia de zona con Azure Database for PostgreSQL: Servidor flexible'
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: c8108540f77d323c46cc88caa628764b40c59e74
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128597994"
---
# <a name="high-availability-concepts-in-azure-database-for-postgresql---flexible-server"></a>Conceptos de alta disponibilidad en Azure Database for PostgreSQL: Servidor flexible



Azure Database for PostgreSQL: Servidor flexible ofrece una configuración de alta disponibilidad con capacidad de conmutación automática por error mediante una implementación de servidor **con redundancia de zona**. Cuando se implementa en una configuración con redundancia de zona, el servidor flexible aprovisiona y administra de forma automática una réplica en espera en otra zona de disponibilidad. Mediante la replicación de streaming de PostgreSQL, los datos se replican en el servidor de réplica en espera en modo **sincrónico**. 

La configuración con redundancia de zona habilita la capacidad de conmutación por error automática sin pérdida de datos durante eventos planeados, como operaciones de proceso de escalado iniciadas por el usuario, y también durante eventos no planeados, como errores de hardware y software subyacentes, errores de red y errores de zona de disponibilidad. 

:::image type="content" source="./media/business-continuity/concepts-zone-redundant-high-availability-architecture.png" alt-text="Alta disponibilidad con redundancia de zona"::: 

## <a name="zone-redundant-high-availability-architecture"></a>Arquitectura de alta disponibilidad con redundancia de zona

La implementación con redundancia de zona permite que el servidor flexible sea de alta disponibilidad. Puede elegir la región y la zona de disponibilidad para implementar el servidor de bases de datos principal. Un servidor de réplica en espera se aprovisiona y administra **automáticamente** en una zona de disponibilidad diferente de la misma región con la misma configuración de proceso, almacenamiento y red que el servidor principal. Los archivos de datos y los archivos de registro de transacciones (registros de escritura previa, también conocidos como WAL) se almacenan en almacenamiento con redundancia local dentro de cada zona de disponibilidad que almacena automáticamente como tres copias de datos. Esto proporciona aislamiento físico de toda la pila entre los servidores principal y en espera.  

Cuando la aplicación realiza escrituras o confirmaciones, mediante la replicación de streaming de PostgreSQL, los registros de transacciones (registros de escritura previa, también conocidos como WAL) también se escriben en el disco local y también se replican en modo *sincrónico* en la réplica en espera. Una vez que los registros se conservan en la réplica en espera, la aplicación se confirma de las escrituras o confirmaciones. El servidor en espera estará en modo de recuperación, que sigue aplicando los registros, pero el servidor principal no espera a que se complete la aplicación.

Las copias de seguridad automáticas se realizan periódicamente desde el servidor de bases de datos principal, mientras que los registros de transacciones se archivan continuamente en el almacenamiento de copia de seguridad de la réplica en espera.

El estado de los servidores principales y en espera se supervisa continuamente y se toman las medidas adecuadas para corregir problemas, incluido el desencadenamiento de una conmutación por error en el servidor en espera. A continuación se enumeran los estados de alta disponibilidad con redundancia de zona:

| **Estado** | **Descripción** |
| ------- | ------ |
| <b> Inicializando | En proceso de crear un servidor en espera |
| <b> Replicando datos | Una vez que se ha creado el servidor en espera, se pone al día con el servidor principal. |
| <b> Correcto | La replicación está en estado estable y es correcta. |
| <b> Conmutando por error | El servidor de bases de datos está en proceso de conmutar por error al de espera. |
| <b> Quitando en espera | En proceso de eliminar el servidor en espera. | 
| <b> Sin habilitar | La alta disponibilidad con redundancia de zona no está habilitada.  |

>[!NOTE]
> Puede habilitar la alta disponibilidad durante la creación del servidor o también en un momento posterior. Si va a habilitar o deshabilitar la alta disponibilidad durante la fase posterior a la creación, se recomienda realizar la operación cuando la actividad del servidor principal sea baja.

## <a name="steady-state-operations"></a>Operaciones de estado estable

Las aplicaciones cliente de PostgreSQL se conectan al servidor principal mediante el nombre del servidor de bases de datos. Las lecturas de aplicación se sirven directamente desde el servidor principal, mientras que las confirmaciones y escrituras se confirman en la aplicación solo después de que los datos de registro se conserven en el servidor principal y en la réplica en espera. Debido a esta ida y vuelta adicional, las aplicaciones pueden esperar una latencia elevada para las operaciones de escritura y confirmación. Puede supervisar el estado de la alta disponibilidad en el portal.

:::image type="content" source="./media/business-continuity/concepts-high-availability-steady-state.png" alt-text="Alta disponibilidad con redundancia de zona: estado estable"::: 

1. Los clientes se conectan al servidor flexible y realizan operaciones de escritura.
2. Los cambios se replican en el sitio en espera.
3. El servidor principal recibe confirmación.
4. Las escrituras o confirmaciones se confirman.

## <a name="failover-process---planned-downtimes"></a>Proceso de conmutación por error: tiempos de inactividad planificados

Los eventos de tiempo de inactividad planificados incluyen actualizaciones de software frecuentes programadas de Azure y actualizaciones de versiones secundarias. Cuando se configuran en alta disponibilidad, estas operaciones se aplican en primer lugar a la réplica en espera mientras las aplicaciones continúan accediendo al servidor principal. Una vez que se actualiza la réplica en espera, se purgan las conexiones del servidor principal y se desencadena una conmutación por error que activa la réplica en espera para que sea la principal con el mismo nombre de servidor de bases de datos. Las aplicaciones cliente tendrán que volver a conectarse con el mismo nombre de servidor de bases de datos al servidor principal nuevo y podrán reanudar sus operaciones. Se establecerá un nuevo servidor en espera en la misma zona que el servidor principal anterior. 

Para otras operaciones iniciadas por el usuario, como el proceso de escalado o almacenamiento de escalado, los cambios se aplican en primer lugar en el modo en espera y después en el principal. Actualmente, el servicio no se conmuta por error en el servidor de espera y, por lo tanto, mientras la operación de escalado se lleva a cabo en el servidor principal, las aplicaciones encontrarán un breve tiempo de inactividad.

### <a name="reducing-planned-downtime-with-managed-maintenance-window"></a>Reducción del tiempo de inactividad planificado con la ventana de mantenimiento administrado

Con el servidor flexible, puede programar actividades de mantenimiento iniciadas por Azure eligiendo una ventana de 30 minutos en un día de su preferencia, donde se espera que las actividades de las bases de datos sean bajas. Las tareas de mantenimiento de Azure, como la revisión o las actualizaciones de versiones secundarias, se producirían durante esa ventana de mantenimiento. Si no elige una ventana personalizada, se elige una ventana de 1 hora asignada por el sistema entre las 23:00 y las 7:00 hora local para el servidor. 
 
En el caso de los servidores flexibles configurados con alta disponibilidad, estas actividades de mantenimiento se realizan primero en la réplica en espera y el servicio se conmuta por error en el servidor en espera al que las aplicaciones pueden volver a conectarse.

## <a name="failover-process---unplanned-downtimes"></a>Proceso de conmutación por error: tiempos de inactividad no planificados

Las interrupciones no planificadas incluyen errores de software o de componentes de infraestructura que afectan a la disponibilidad de la base de datos. En caso de que el servidor principal no esté disponible, el sistema de supervisión lo detecta e inicia un proceso de conmutación por error.  El proceso incluye unos segundos de tiempo de espera para asegurarse de que no es un falso positivo. La replicación en la réplica en espera se anula y la réplica en espera se activa para ser el servidor de bases de datos principal. Esto incluye el modo de espera para recuperar los archivos WAL residuales. Una vez que se haya recuperado por completo, el DNS para el mismo punto de conexión se actualiza con la dirección IP del servidor en espera. Los clientes pueden volver a intentar conectarse al servidor de bases de datos mediante la misma cadena de conexión y reanudar sus operaciones. 

>[!NOTE]
> Los servidores flexibles configurados con alta disponibilidad con redundancia de zona proporcionan un objetivo de punto de recuperación (RPO) de **cero** (sin pérdida de datos). Se espera que el objetivo de recuperación (RTO) sea **inferior a 120 s** en los casos típicos. Pero en función de la actividad del servidor de bases de datos principal en el momento de la conmutación por error, la conmutación por error puede tardar más. 

Después de la conmutación por error, mientras se aprovisiona un nuevo servidor en espera, las aplicaciones todavía pueden conectarse al servidor principal y continuar con sus operaciones de lectura y escritura. Una vez establecido el servidor en espera, empezará a recuperar los registros que se generaron después de la conmutación por error. 

:::image type="content" source="./media/business-continuity/concepts-high-availability-failover-state.png" alt-text="Alta disponibilidad con redundancia de zona: conmutación por error"::: 

1. El servidor de bases de datos principal está inactivo y los clientes pierden la conectividad de la base de datos. 
2. El servidor en espera se activa para convertirse en el servidor principal nuevo. El cliente se conecta al servidor principal nuevo con la misma cadena de conexión. Tener la aplicación cliente en la misma zona que el servidor de bases de datos principal reduce la latencia y mejora el rendimiento.
3. El servidor en espera se establece en la misma zona que el servidor principal anterior y se inicia la replicación de streaming. 
4. Una vez establecida la replicación de estado estable, las confirmaciones y escrituras de la aplicación cliente se confirman después de que los datos se conserven en ambos sitios.

## <a name="on-demand-failover"></a>Conmutación por error a petición

El servidor flexible proporciona dos métodos para realizar la conmutación por error a petición en el servidor en espera. Son útiles si desea probar el tiempo de conmutación por error y el impacto en el tiempo de inactividad de las aplicaciones y si desea conmutar por error a la zona de disponibilidad preferida. 

### <a name="forced-failover"></a>conmutación por error forzada

Puede usar esta característica para simular un escenario de interrupción no planeada mientras se ejecuta la carga de trabajo de producción y observar el tiempo de inactividad de la aplicación. Como alternativa, en raras ocasiones cuando el servidor principal deja de responder por cualquier motivo, puede usar esta característica. 

Esta característica activa el servidor principal e inicia el flujo de trabajo de conmutación por error en el que se realiza la operación de promoción en espera. Una vez que el modo de espera completa el proceso de recuperación hasta los últimos datos confirmados, se promueve para que sea el servidor principal. Los registros DNS se actualizan y la aplicación puede conectarse al servidor principal promocionado. La aplicación puede seguir escribiendo en el servidor principal mientras se establece un nuevo servidor en espera en segundo plano. Hay que seguir estos pasos:

  | **Step** | **Descripción** | **¿Se prevé algún tiempo de inactividad?** |
  | ------- | ------ | ----- |
  | 1 | El servidor principal se detiene poco después de recibir la solicitud de conmutación por error. | Sí |
  | 2 | La aplicación encuentra tiempo de inactividad cuando el servidor principal está inactivo. | Sí |
  | 3 | El sistema de supervisión interno detecta el error e inicia una conmutación por error en el servidor en espera. | Sí |
  | 4 | El servidor en espera entra en modo de recuperación antes de promocionarse por completo como un servidor independiente. | Sí |
  | 5 | El proceso de conmutación por error espera a que se complete la recuperación en espera. | Sí |
  | 6 | Una vez que el servidor está activo, el registro DNS se actualiza con el mismo nombre de host, pero con la dirección IP del servidor en espera. | Sí |
  | 7 | La aplicación puede volver a conectarse al nuevo servidor principal y reanudar la operación. | No |
  | 8 | Se establece un servidor en espera en la zona preferida. | No |
  | 9 | El servidor en espera comienza a recuperar los registros (del blob de Azure) que faltó durante su establecimiento. | No |
  | 10 | Se establece un estado estable entre el servidor principal y el servidor en espera. | No |
  | 11 | Se ha completado el proceso de conmutación por error forzada. | No |

Se espera que el tiempo de inactividad de la aplicación se inicie el paso 1 y persista hasta que se complete el paso 6. El resto de los pasos se suceden en segundo plano sin afectar a las escrituras y confirmaciones de la aplicación.

### <a name="planned-failover"></a>Conmutación por error planeada

Puede usar esta característica para conmutar por error en el servidor en espera con un tiempo de inactividad reducido. Por ejemplo, después de una conmutación por error no planeada, el servidor principal podría estar en una zona de disponibilidad diferente de la aplicación y desea devolver el servidor principal a la zona anterior para colocarlo con la aplicación.

Al ejecutar esta característica, el servidor en espera se prepara primero para asegurarse de que se encuentra al día con las transacciones recientes, lo que permite a la aplicación seguir realizando operaciones de lectura y escritura. A continuación, se promueve el servidor en modo de espera y se gravan las conexiones al principal. La aplicación puede seguir escribiendo en el servidor principal mientras se establece un nuevo servidor en espera en segundo plano. A continuación se indican los pasos necesarios para la conmutación por error planeada.

| **Step** | **Descripción** | **¿Se prevé algún tiempo de inactividad?** |
  | ------- | ------ | ----- |
  | 1 | Espere a que el servidor en espera se haya conectado con el servidor principal. | No |
  | 2 | El sistema de supervisión interno inicia el flujo de trabajo de conmutación por error. | No |
  | 3 | Las escrituras de aplicaciones se bloquean cuando el servidor en espera está cerca del número de secuencia de registro principal (LSN). | Sí |
  | 4 | El servidor en espera se promueve como un servidor independiente. | Sí |
  | 5 | El registro DNS se actualiza con la nueva dirección IP del servidor en espera. | Sí |
  | 6 | Aplicación para volver a conectarse y reanudar su lectura y escritura con el nuevo servidor principal. | No |
  | 7 | Se establece un nuevo servidor en espera en otra zona. | No |
  | 8 | El servidor en espera comienza a recuperar los registros (del blob de Azure) que faltó durante su establecimiento. | No |
  | 9 | Se establece un estado estable entre el servidor principal y el servidor en espera. | No |
  | 10 |  Se ha completado el proceso de conmutación por error planeado. | No |

El tiempo de inactividad de la aplicación comienza en el paso 3 y puede reanudar la operación después del paso 5. El resto de los pasos se suceden en segundo plano sin afectar a las escrituras y confirmaciones de la aplicación. 

### <a name="considerations-while-performing-on-demand-failovers"></a>Consideraciones al realizar las conmutaciones por error a petición

* El tiempo total de la operación de un extremo a otro puede ser mayor que el tiempo de inactividad real experimentado por la aplicación. **Observe el tiempo de inactividad desde la perspectiva de la aplicación**.
* No realice conmutaciones por error inmediatas consecutivas. Espere al menos entre 15 y 20 minutos entre una conmutación por error y otra, lo que permitirá que el nuevo servidor en espera esté totalmente establecido.
* Para la conmutación por error planeada con un tiempo de inactividad reducido, se recomienda realizar durante el período de actividad baja.

Consulte [esta guía](how-to-manage-high-availability-portal.md) para administrar la alta disponibilidad.


## <a name="point-in-time-restore-of-ha-servers"></a>Restauración a un momento dado de servidores de alta disponibilidad

Los servidores flexibles que están configurados con alta disponibilidad replican los datos de registro en tiempo real en el servidor en espera. Los errores de usuario en el servidor principal, como la eliminación accidental de una tabla o las actualizaciones de datos incorrectas, se replican en la réplica en espera también. Por tanto, no puede usar el servidor en espera para recuperarse de estos errores lógicos. Tiene que realizar una restauración a un momento dado de copias de seguridad para recuperarse de estos errores.  Con la capacidad de restauración a un momento dado del servidor flexible, puede restaurar hasta el momento antes de que se produjera el error. En el caso de las bases de datos configuradas con alta disponibilidad, un nuevo servidor de bases de datos se restaurará como un servidor flexible de una sola zona con un nuevo nombre proporcionado por el usuario. Puede usar el servidor restaurado para algunos casos de uso:

  1. Puede usar el servidor restaurado para el uso de producción y, opcionalmente, habilitar la alta disponibilidad con redundancia de zona. 
  2. Si solo desea restaurar un objeto, puede exportarlo desde el servidor de bases de datos restaurado e importarlo al servidor de base de datos de producción. 
  3. Si quiere clonar el servidor de bases de datos con fines de prueba y desarrollo, o bien realizar la restauración para cualquier otro fin, puede realizar una restauración a un momento dado.

## <a name="zone-redundant-high-availability---features"></a>Alta disponibilidad con redundancia de zona: características

* La réplica en espera se implementará en una configuración de máquina virtual exacta a la del servidor principal, incluidos los núcleos virtuales, el almacenamiento, la configuración de red (VNET, firewall), etc.

* Puede agregar alta disponibilidad a un servidor de bases de datos existente.

* Puede quitar la réplica en espera si se deshabilita la alta disponibilidad.

* Solo puede elegir la zona de disponibilidad para el servidor de bases de datos principal. La zona en espera se selecciona automáticamente.

* Las operaciones como detener, iniciar y reiniciar se realizan en los servidores de base de datos principal y en espera al mismo tiempo.

* Las copias de seguridad automáticas se realizan desde el servidor de bases de datos principal y se almacenan en un almacenamiento con redundancia de zona.

* Los clientes siempre se conectan al nombre de host final del servidor de bases de datos principal.

* Los cambios en los parámetros del servidor también se aplican a la réplica en espera.

* Capacidad de reiniciar el servidor para seleccionar cualquier cambio de parámetro de servidor estático.
  
* Las actividades de mantenimiento periódico, como las actualizaciones de versiones secundarias, se producen en primer lugar en el servidor en espera y el servicio se conmuta por error para reducir el tiempo de inactividad.  

## <a name="zone-redundant-high-availability---limitations"></a>Alta disponibilidad con redundancia de zona: limitaciones

* La alta disponibilidad no se admite en el nivel de proceso expandible.
* La alta disponibilidad solo se admite en regiones donde hay disponibles varias zonas.
* Debido a la replicación sincrónica en otra zona de disponibilidad, las aplicaciones pueden experimentar una latencia elevada de escritura y confirmación.

* No se puede usar la réplica en espera para las consultas de solo lectura.

* Según la carga de trabajo y la actividad en el servidor principal, el proceso de conmutación por error puede tardar más de 120 segundos debido a la recuperación implicada en la réplica en espera antes de que se pueda promover.

* Al reiniciar el servidor de bases de datos principal también se reinicia la réplica en espera. 

* No se admite la configuración de réplicas de lectura adicionales.

* La configuración de las tareas de administración iniciadas por el cliente no se puede programar durante la ventana de mantenimiento administrado.

* Los eventos planificados, como la escala de proceso y el almacenamiento de escalado, se producen primero en el servidor en espera y luego en el servidor principal. Actualmente, el servidor no realiza la conmutación por error para estas operaciones planeadas. 

* Si la descodificación lógica o la replicación lógica están configuradas con un servidor flexible configurado de alta disponibilidad, en el caso de una conmutación por error al servidor en espera, las ranuras de replicación lógica no se copian en el servidor en espera.  

## <a name="availability-without-zone-redundant-ha"></a>Disponibilidad sin alta disponibilidad con redundancia de zona

En el caso de los servidores flexibles configurados **sin** alta disponibilidad con redundancia de zona, el servicio sigue proporcionando disponibilidad integrada, redundancia de almacenamiento y resistencia para ayudar a recuperarse de los eventos de tiempo de inactividad planeados o no planeados.

### <a name="planned-downtime"></a>Tiempo de inactividad planificado 

Estos son algunos escenarios de mantenimiento planeado:

| **Escenario** | **Descripción**|
| ------------ | ----------- |
| <b>Escalado y reducción vertical de proceso | Cuando el usuario realiza una operación de escalado o reducción vertical de procesos, se aprovisiona un nuevo servidor de base de datos con la configuración de proceso escalado. En el servidor de base de datos anterior, se permite que se finalicen los puntos de comprobación activos, se purgan las conexiones de cliente, se cancelan las transacciones no confirmadas y, a continuación, se apaga el servidor. A continuación, el almacenamiento se desasocia del servidor de base de datos anterior y se conecta al nuevo servidor de base de datos. Estará preparado para aceptar las conexiones.|
| <b>Escalado vertical del almacenamiento | El escalado vertical del almacenamiento es actualmente una operación sin conexión que implica un breve tiempo de inactividad.|
| <b>Nueva implementación de software (Azure) | Las nuevas características de implementación o corrección de errores se producen automáticamente como parte del mantenimiento planeado del servicio. Para obtener más información, consulte la [documentación](./concepts-maintenance.md) y consulte también el [portal](https://aka.ms/servicehealthpm).|
| <b>Actualizaciones de versión secundarias | Azure Database for PostgreSQL revisa automáticamente los servidores de bases de datos según la versión secundaria determinada por Azure. Se produce como parte del mantenimiento planeado del servicio. Esto provocaría un breve tiempo de inactividad en términos de segundos y el servidor de base de datos se reiniciará automáticamente con la nueva versión secundaria. Para obtener más información, consulte la [documentación](./concepts-maintenance.md) y consulte también el [portal](https://aka.ms/servicehealthpm).|


###  <a name="unplanned-downtime"></a>Tiempo de inactividad no planeado 

Se puede producir un tiempo de inactividad no planeado como resultado de errores imprevistos, incluidos los errores subyacentes de hardware, los problemas de red y los errores de software. Si el servidor de base de datos deja de funcionar de forma inesperada, se aprovisiona automáticamente un nuevo servidor de base de datos en segundos. El almacenamiento remoto se asocia automáticamente al nuevo servidor de base de datos. El motor de PostgreSQL realiza la operación de recuperación mediante WAL y archivos de base de datos, y abre el servidor de base de datos para permitir que los clientes se conecten. Las transacciones no confirmadas se pierden y la aplicación debe volver a intentarlas. Aunque no es posible evitar un tiempo de inactividad no planeado, el servidor flexible lo reduce gracias a las operaciones de recuperación automáticas tanto en el servidor de bases de datos como en las capas de almacenamiento sin necesidad de intervención humana. 

Estos son algunos escenarios de error y cómo el servidor flexible se recupera automáticamente:

| **Escenario** | **Recuperación automática** |
| ---------- | ---------- |
| <B>Error de servidor de bases de datos | Si el servidor de base de datos está inactivo debido a un error de hardware subyacente, se quitan las conexiones activas y se anulan las transacciones inactivas. Se implementa automáticamente un nuevo servidor de base de datos y el almacenamiento de datos remotos se adjunta al nuevo servidor de base de datos. Una vez completada la recuperación de la base de datos, los clientes pueden conectarse al nuevo servidor de bases de datos con el mismo punto de conexión. <br /> <br /> El tiempo de recuperación (RTO) depende de varios factores, por ejemplo, la actividad en el momento del error, como una transacción de gran tamaño y la cantidad de recuperación que se va a realizar durante el proceso de inicio del servidor de bases de datos. <br /> <br /> Las aplicaciones que usan bases de datos de PostgreSQL se deben crear de forma que detecten y reintenten conexiones eliminadas y transacciones erróneas.  |
| <B>Error de almacenamiento | Las aplicaciones no verán ningún impacto por los problemas relacionados con el almacenamiento, como un error de disco o un daño de bloque físico. A medida que los datos se almacenan en tres copias, el almacenamiento sobreviviente proporciona la copia de los datos. Los daños en bloques se corrigen automáticamente. Si se pierde una copia de los datos, se crea automáticamente una nueva. |

Estos son algunos escenarios de error que requieren de la acciones del usuario para recuperarse:

| **Escenario** | **Plan de recuperación** |
| ---------- | ---------- |
| <b> Error de zona de disponibilidad | Si la región admite varias zonas de disponibilidad, las copias de seguridad se guardan automáticamente en el almacenamiento de copia de seguridad con redundancia de zona. Si se produce un error de zona, puede restaurar desde la copia de seguridad en otra zona de disponibilidad. Esto proporciona resistencia de nivel de zona. Sin embargo, supone un tiempo de restauración y recuperación. Podría haber una pérdida de datos, ya que puede que no se hubieran copiado todos los registros WAL en el almacenamiento de copia de seguridad. <br> <br> Si prefiere tener poco tiempo de inactividad y un tiempo de actividad elevado, se recomienda configurar el servidor con alta disponibilidad con redundancia de zona. |
| <b> Errores de usuario o lógicos | La recuperación de los errores de usuario, como las tablas eliminadas accidentalmente o los datos actualizados incorrectamente, implica la realización de una [recuperación a un momento dado](./concepts-backup-restore.md) (PITR), de modo que se restauran y recuperan los datos hasta el momento justo antes de que se produjera el error.<br> <br>  Si quiere restaurar únicamente un subconjunto de bases de datos o tablas específicas en lugar de todas las bases de datos del servidor de bases de datos, puede restaurar el servidor de base de datos en una nueva instancia, exportar las tablas a través de [pg_dump](https://www.postgresql.org/docs/13/app-pgdump.html) y, a continuación, usar [pg_restore](https://www.postgresql.org/docs/13/app-pgrestore.html) para restaurar esas tablas en la base de datos. |

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="ha-configuration-questions"></a>Preguntas sobre la configuración de la alta disponibilidad

* **¿Es necesario tener alta disponibilidad con redundancia de zona para proteger mi servidor frente a interrupciones no planeadas?** <br>
    No. El servidor flexible ofrece almacenamiento con redundancia local con 3 copias de datos, copia de seguridad con redundancia de zona (en las regiones donde se admite) y resistencia de servidor integrada para reiniciar automáticamente un servidor que se bloquea e incluso reubicar el servidor a otro nodo físico. La alta disponibilidad con redundancia de zona proporcionará un mayor tiempo de actividad mediante la conmutación automática por error a otro servidor en ejecución (en espera) de otra zona y, por tanto, proporciona alta disponibilidad con resistencia de zona sin pérdida de datos.

* **¿Hay una alta disponibilidad con redundancia de zona disponible en todas las regiones?** <br>
    La alta disponibilidad con redundancia de zona está disponible en las regiones que admiten varias zonas de disponibilidad en la región. Para obtener la compatibilidad con la región más reciente, consulte [esta documentación](overview.md#azure-regions). Estamos agregando continuamente más regiones y habilitando varias zonas de disponibilidad. 

* **¿Qué modo de replicación se encuentra entre los servidores principal y en espera?** <br>
    El modo sincrónico de replicación se establece entre el servidor principal y el servidor en espera. Las escrituras y confirmaciones de la aplicación solo se reconocen después de que los datos del registro de escritura previa (WAL) se conserven en el sitio en espera. Esto permite que no se pierdan datos en caso de que se produzca una conmutación por error.

* **El modo sincrónico incurre en latencia. ¿Qué tipo de impacto en el rendimiento puedo esperar para mi aplicación?** <br>
    La configuración en alta disponibilidad induce cierta latencia en las operaciones de escritura y confirmación. No afecta a las consultas de lectura. El impacto en el rendimiento varía en función de la carga de trabajo. Como norma general, el impacto de las escrituras y las confirmaciones puede ser alrededor del 20 o 30 % de impacto. 

* **¿La alta disponibilidad con redundancia de zona proporciona protección contra interrupciones planeadas y no planeadas?** <br>
    Sí. El propósito principal de la alta disponibilidad es ofrecer un mayor tiempo de actividad para mitigar las interrupciones. En el caso de una interrupción no planeada (incluido un error en la base de datos, la máquina virtual, el nodo físico, el centro de datos o en el nivel de la zona de disponibilidad), el sistema de supervisión conmuta por error automáticamente al servidor en modo de espera. Del mismo modo, durante las interrupciones planeadas, incluidas las actualizaciones de versiones secundarias o la aplicación de revisiones en la infraestructura que se producen durante la ventana de mantenimiento programada, las actualizaciones se aplican primero en servidor en espera y el servicio se conmuta por error mientras el principal anterior pasa por el proceso de actualización. Esto reduce el tiempo de inactividad global. 

* **¿Puedo habilitar o deshabilitar la alta disponibilidad en cualquier momento?** <br>

    Sí. Puede habilitar o deshabilitar la alta disponibilidad con redundancia de zona en cualquier momento, excepto cuando el servidor se encuentra en determinados estados como detenido, reiniciando o si ya se está en el proceso de conmutación por error. 

* **¿Puedo elegir la zona de disponibilidad para el modo de espera?** <br>
    No. Actualmente no puede elegir la zona de disponibilidad para el modo de espera. Tenemos previsto agregar esa funcionalidad en el futuro.

* **¿Puedo configurar la alta disponibilidad entre el acceso privado (VNET) y el público?** <br>
    No. Puede configurar la alta disponibilidad en una red virtual (distribuida a través de zonas de disponibilidad dentro de una región) o acceso público. 

* **¿Puedo configurar la alta disponibilidad entre regiones?** <br>
    No. La alta disponibilidad se configura dentro de una región, pero entre zonas de disponibilidad. En el futuro, estamos pensando ofrecer réplicas de lectura que se pueden configurar entre regiones para fines de recuperación ante desastres (DR). Se proporcionarán más detalles cuando la característica esté habilitada. 

* **¿Puedo usar la replicación lógica con servidores configurados de alta disponibilidad?** <br>
    Puede configurar la replicación lógica con alta disponibilidad. Sin embargo, después de una conmutación por error, los detalles de la ranura lógica no se copian en el modo de espera. Por lo tanto, actualmente hay compatibilidad limitada para esta configuración.

### <a name="replication-and-failover-related-questions"></a>Preguntas relacionadas con la replicación y la conmutación por error

* **¿Cómo proporciona el servidor flexible una alta disponibilidad en caso de error, como un error de zona de disponibilidad?** <br>
    Al habilitar el servidor con una alta disponibilidad con redundancia de zona, se implementa automáticamente una réplica de espera física con la misma configuración de proceso y almacenamiento que la principal en una zona de disponibilidad distinta de la principal. La replicación de transmisión de PostgreSQL se establece entre los servidores principal y en espera. 

* **¿Cuál es el proceso de conmutación por error típico durante una interrupción?** <br>
    Cuando el sistema de supervisión detecta el error, inicia un flujo de trabajo de conmutación por error que implica asegurarse de que el modo de espera haya aplicado todos los archivos de WAL residuales y que se haya detenido por completo antes de abrirlo para lectura y escritura. Después, el DNS se actualiza con la dirección P del modo de espera antes de que los clientes puedan volver a conectarse al servidor con el mismo punto de conexión (nombre de host). Se crea una instancia de un nuevo modo de espera para mantener la configuración en un modo de alta disponibilidad.

* **¿Cuál es el tiempo de conmutación por error típico y la pérdida de datos esperada durante una interrupción?** <br>
    En un caso típico, el tiempo de conmutación por error o el tiempo de inactividad experimentado por la perspectiva de la aplicación se encuentra entre 60  a 120 s. Esto puede ser mayor en los casos en los que la interrupción se ha producido durante las transacciones de larga duración, la creación de índices o durante las actividades de escritura pesadas, ya que el modo de espera puede tardar más tiempo en completar el proceso de recuperación.

    Dado que la replicación se produce en modo sincrónico, no se espera ninguna pérdida de datos.

* **¿Ofrece Acuerdos de Nivel de Servicio para el tiempo de conmutación por error?** <br>
    En el momento de la conmutación por error, se aportan instrucciones sobre cuánto tiempo tarda normalmente la operación. Se proporcionará el Acuerdo de Nivel de Servicio oficial para el tiempo de actividad global en el momento de la disponibilidad general del servicio. No se ofrece ningún Acuerdo de Nivel de Servicio durante la versión preliminar pública.

* **¿La aplicación se conecta automáticamente al servidor después de la conmutación por error?** <br>
    No. Las aplicaciones deben tener un mecanismo de reintento para volver a conectarse al mismo punto de conexión (nombre de host).

* **¿Cómo pruebo la conmutación por error?** <br>
    Puede usar la **conmutación por error forzada** o la característica **conmutación por error** planeada para probar la conmutación por error. Consulte la sección **Conmutación por error a petición** de este documento para obtener más información.

* **¿Cómo compruebo el estado de replicación de un blob?** <br>
    En el portal, en la página de información general del servidor, se muestra el estado de alta disponibilidad con redundancia de zona y el estado del servidor. También puede comprobar el estado y las zonas de disponibilidad de los servidores principales y en espera desde la hoja de alta disponibilidad del portal del servidor. 

    En psql, puede ejecutar `select * from pg_stat_replication;`, que muestra el estado de streaming entre otros detalles.

* **¿Se admiten consultas de lectura en la réplica en espera?** <br>
    No. No se admiten consultas de lectura en la réplica en espera.

* **Cuando realizo la recuperación a un momento dado (PITR), ¿configurará automáticamente el servidor restaurado en alta disponibilidad?** <br>
    No. El servidor de PITR se restaura como un servidor independiente. Si desea habilitar la alta disponibilidad, puede hacerlo una vez completada la restauración.


## <a name="next-steps"></a>Pasos siguientes

-   Más información sobre la [continuidad empresarial](./concepts-business-continuity.md)
-   Más información sobre [administración de la alta disponibilidad](./how-to-manage-high-availability-portal.md)
-   Más información sobre [copia de seguridad y recuperación](./concepts-backup-restore.md)
