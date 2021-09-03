---
title: 'Copia de seguridad y restauración en Azure Database for PostgreSQL: servidor flexible'
description: 'Obtenga información sobre los conceptos de la copia de seguridad y restauración con Azure Database for PostgreSQL: servidor flexible.'
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/30/2021
ms.openlocfilehash: 8c6f3ebaa72457d93e4b3e491656f494511bd994
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121745152"
---
# <a name="backup-and-restore-in-azure-database-for-postgresql---flexible-server"></a>Copia de seguridad y restauración en Azure Database for PostgreSQL: servidor flexible

> [!IMPORTANT]
> Azure Database for PostgreSQL: servidor flexible está en versión preliminar.

Las copias de seguridad forman una parte esencial de cualquier estrategia de continuidad empresarial. Ayudan a proteger los datos en caso de daños o eliminaciones accidentales. Azure Database for PostgreSQL: servidor flexible realiza automáticamente una copia de seguridad del servidor y conserva las copias de seguridad durante un período de hasta 35 días. Durante la restauración, puede especificar la fecha y la hora en las que quiera realizar la restauración en el período de retención. El tiempo total de restauración y recuperación depende del tamaño de los archivos de la base de datos y de las dimensiones de la recuperación que se va a realizar. 

### <a name="backup-process-in-flexible-server"></a>Proceso de copia de seguridad en un servidor flexible
La primera copia de seguridad de instantáneas completa se programa inmediatamente después de la creación del servidor flexible. Posteriormente, se realiza una copia de seguridad diaria de las instantáneas de los archivos de datos. Las copias de seguridad se almacenan en un almacenamiento con redundancia de zona dentro de una región. Los registros de transacciones (registros de escritura previa) también se archivan continuamente, por lo que podrá restaurar a la última transacción confirmada. Estas copias de seguridad de registros y datos permiten restaurar un servidor a un momento dado dentro del período de retención de copias de seguridad configurado. Todas las copias de seguridad se cifran mediante cifrado AES de 256 bits.

Si la base de datos se configura con alta disponibilidad, las instantáneas diarias se realizarán desde el servidor principal, y las copias de seguridad continuas del registro, desde el modo de espera.

> [!IMPORTANT]
>En los servidores detenidos no se realizan copias de seguridad. Sin embargo, las copias de seguridad se reanudan cuando la base de datos se inicia automáticamente transcurridos siete días o cuando lo hace el usuario.

Las copias de seguridad solo se pueden usar para operaciones de restauración en el servidor flexible. Si quiere exportar o importar datos al servidor flexible, use la metodología de [volcado y restauración](../howto-migrate-using-dump-and-restore.md).


### <a name="backup-retention"></a>Retención de copia de seguridad

Las copias de seguridad se conservan según el valor del período de retención de copia de seguridad en el servidor. Puede seleccionar un período de retención de entre 7 y 35 días. El período de retención predeterminado es de siete días. Puede establecer el período de retención durante la creación del servidor, o bien puede actualizarlo más adelante. Las copias de seguridad de los servidores detenidos también se conservan.

El período de retención de copia de seguridad rige durante cuánto tiempo se puede realizar una restauración a un momento dado, porque se basa en las copias de seguridad disponibles. El período de retención de copia de seguridad también se puede tratar como un período de recuperación desde una perspectiva de restauración. Todas las copias de seguridad que se necesitan para realizar una restauración a un momento dado dentro del período de retención de copia de seguridad se conservan en el almacenamiento de copia de seguridad. Por ejemplo, si el período de retención de la copia de seguridad se establece en siete días, el período de recuperación comprendería los últimos siete días. En este escenario, se conservan todos los datos y los registros necesarios para restaurar y recuperar el servidor en los últimos siete días. 


### <a name="backup-storage-cost"></a>Costo del almacenamiento de copia de seguridad

El Servidor flexible proporciona hasta un 100 % del almacenamiento del servidor aprovisionado como almacenamiento de copia de seguridad, sin costos adicionales. El cargo de cualquier almacenamiento de copia de seguridad adicional que se use se realizará por GB/mes. Por ejemplo, si ha aprovisionado un servidor con 250 GiB de almacenamiento, tendrá 250 GiB de capacidad para almacenar copias de seguridad sin costos adicionales. Si el uso de la copia de seguridad diaria es de 25 GiB, puede tener hasta diez días de almacenamiento de copia de seguridad. El consumo de almacenamiento de copia de seguridad que supere los 250 GiB se cobrará según el [modelo de precios](https://azure.microsoft.com/pricing/details/postgresql/).

Puede usar la métrica [Almacenamiento de copia de seguridad utilizado](../concepts-monitoring.md) en Azure Portal para supervisar el almacenamiento de copia de seguridad que usa un servidor. La métrica Almacenamiento de copia de seguridad utilizado representa la suma del almacenamiento consumido por todas las copias de seguridad de base de datos y copias de seguridad de registros, que se conservan durante el período de retención de copia de seguridad establecido para el servidor.  Una gran actividad transaccional en el servidor puede hacer que el uso del almacenamiento de copia de seguridad aumente, independientemente del tamaño total de la base de datos.

El medio principal para controlar el costo de almacenamiento de copia de seguridad es establecer el período de retención apropiado y elegir las opciones adecuadas de redundancia de copia de seguridad para satisfacer los objetivos de recuperación deseados.

> [!IMPORTANT]
> Las copias de seguridad con redundancia geográfica no se admiten actualmente en el servidor flexible.

## <a name="point-in-time-restore-overview"></a>Información general sobre la restauración a un momento dado

En un servidor flexible, al realizar una restauración a un momento dado, se crea un nuevo servidor a partir de las copias de seguridad del servidor flexible en la misma región que el de origen. Se crea con la configuración del servidor de origen relativa al plan de tarifa, generación de procesos, número de núcleos virtuales, tamaño de almacenamiento, período de retención de copia de seguridad y opción de redundancia de copia de seguridad. Además, las etiquetas y configuraciones como la configuración de la red virtual y del firewall se heredan del servidor de origen. 

 > [!IMPORTANT]
> Si va a restaurar un servidor flexible configurado con alta disponibilidad y redundancia geográfica, el servidor restaurado se configurará sin alta disponibilidad y en la misma región que el principal. 

 ### <a name="restore-process"></a>Proceso de restauración

Los archivos físicos de base de datos se restauran primero a partir de las copias de seguridad de instantáneas en la ubicación de datos del servidor. Se elige y restaura automáticamente la copia de seguridad pertinente, realizada antes del momento indicado. A continuación, se inicia un proceso de recuperación con archivos WAL para llevar la base de datos en un estado coherente. 

 Por ejemplo, supongamos que las copias de seguridad se realizan todas las noches a las 23:00. Si el punto de restauración es el 15 de agosto de 2020 a las 10:00, se restaurará la copia de seguridad diaria del 14 de agosto de 2020. Se recuperará la base de datos hasta las 10:00 del 25 de agosto de 2020 con la copia de seguridad del registro de transacciones entre el 24 de agosto a las 23:00 y el 25 de agosto a las 10:00. 

 Siga [estos pasos](./how-to-restore-server-portal.md) para restaurar el servidor de base de datos.

> [!IMPORTANT]
> Las operaciones de restauración en un servidor flexible crean un nuevo servidor de base de datos y no sobrescriben el servidor de base de datos existente.

La restauración a un momento dado es útil en diversos escenarios. Por ejemplo, cuando un usuario elimina accidentalmente los datos, elimina una tabla importante o la base de datos, o si una aplicación sobrescribe accidentalmente los datos correctos con datos incorrectos debido a un defecto de la aplicación. Gracias a la copia de seguridad continua de los registros de transacciones, podrá restaurar el servidor a la última transacción.

Puede elegir entre el punto de restauración más reciente y uno personalizado.

-   **Punto de restauración más reciente (ahora)** : esta es la opción predeterminada que permite restaurar el servidor al último momento dado. 

-   **Punto de restauración personalizado**: esta opción le permite elegir cualquier momento dentro del período de retención definido para este servidor flexible. De forma predeterminada, se selecciona automáticamente la última hora UTC, que resulta útil para realizar la restauración a la última transacción confirmada con fines de prueba. También puede elegir otros días y horas. 

El tiempo estimado de recuperación depende de varios factores, como el tamaño de la base de datos, el volumen de los registros de transacciones que se van a procesar, el ancho de banda de red y el número total de bases de datos que se están recuperando en la misma región al mismo tiempo. Normalmente, el tiempo de recuperación general tarda entre unos minutos y unas horas.

Si ha configurado el servidor dentro de una red virtual, puede restaurar a la misma red virtual o a otra. Sin embargo, no se puede restaurar a un acceso público. De forma similar, si ha configurado el servidor con acceso público, no podrá restaurar a un acceso privado.


> [!IMPORTANT]
> Los servidores eliminados **no se pueden** restaurar. Si elimina el servidor, todas las bases de datos que pertenecen al servidor también se eliminan y no se pueden recuperar. Para proteger los recursos del servidor, después de la implementación, de eliminaciones accidentales o cambios inesperados, los administradores pueden aprovechar los [bloqueos de administración](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Tareas posteriores a la restauración

Después de restaurar la base de datos, puede realizar las siguientes tareas para que los usuarios y las aplicaciones vuelvan a conectarse:

-   Si el nuevo servidor está destinado a reemplazar al original, redirija a los clientes y las aplicaciones cliente al nuevo servidor.

-   Asegúrese de aplicar reglas de red virtual y de firewall de nivel de servidor adecuadas para que se conecten los usuarios. Estas reglas no se copian desde el servidor original.
  
-   El proceso del servidor restaurado se puede escalar vertical u horizontalmente según sea necesario.

-   No se olvide de emplear los permisos de nivel de base de datos y los inicios de sesión apropiados.

-   Configure las alertas según corresponda.
  
-  Si ha restaurado la base de datos con alta disponibilidad y quiere configurar el servidor restaurado con alta disponibilidad, siga [estos pasos](./how-to-manage-high-availability-portal.md).

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="backup-related-questions"></a>Preguntas relacionadas con la copia de seguridad

* **¿Cómo controla Azure la copia de seguridad de mi servidor?**
 
    De forma predeterminada, Azure Database for PostgreSQL habilita copias de seguridad automatizadas de todo el servidor (abarcando todas las bases de datos creadas) con un período de retención predeterminado de siete días. Se realiza una instantánea incremental diaria de la base de datos. Los archivos de registros (WAL) se archivan continuamente en el blob de Azure.

* **¿Puedo configurar esas copias de seguridad automáticas para que se conserven a largo plazo?**
  
    No. Actualmente solo se admite un máximo de 35 días de retención. Puede hacer copias de seguridad manuales y usarlas para los requisitos de retención a largo plazo.

* **¿Cómo hago una copia de seguridad manual de mis servidores de Postgres?**
  
    Puede realizar manualmente una copia de seguridad mediante la herramienta pg_dump de PostgreSQL como se documenta [aquí](https://www.postgresql.org/docs/current/app-pgdump.html). Para ver ejemplos, puede consultar [esta documentación de actualización o migración](../howto-migrate-using-dump-and-restore.md) que también puede usar para las copias de seguridad. Si desea hacer una copia de seguridad de Azure Database for PostgreSQL en un almacenamiento de blobs, consulte el blog de nuestra comunidad tecnológica [Copia de seguridad de Azure Database for PostgreSQL en un almacenamiento de blobs](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/backup-azure-database-for-postgresql-to-a-blob-storage/ba-p/803343). 

* **¿Cuáles son las ventanas de copia de seguridad de mi servidor? ¿Puedo personalizarlo?**
  
    Azure administra de forma inherente las ventanas de copia de seguridad y no se pueden personalizar. La primera copia de seguridad de instantáneas completa, se programa inmediatamente después de la creación del servidor. Las copias de seguridad de instantáneas posteriores son copias de seguridad incrementales que se producen una vez al día.

* **¿Mis copias de seguridad están cifradas?**
  
    Sí. Todos los datos, copias de seguridad y archivos temporales de Azure Database for PostgreSQL creados durante la consultas se cifran mediante AES de 256 bits. El cifrado de almacenamiento siempre está activado y no se puede deshabilitar. 

* **¿Puedo restaurar una o varias bases de datos en un servidor?**
  
    No se admite directamente la restauración de una o varias bases de datos o tablas. Sin embargo, debe restaurar todo el servidor en un servidor nuevo y, después, extraer las tablas o bases de datos necesarias e importarlas en el servidor.

* **¿Mi servidor está disponible mientras la copia de seguridad está en curso?**
    Sí. Las copias de seguridad son operaciones en línea que usan instantáneas. La operación de instantánea solo tarda unos segundos y no interfiere con las cargas de trabajo de producción que garantizan la alta disponibilidad del servidor. 

* **Al configurar la ventana de mantenimiento para el servidor, ¿es necesario tener en cuenta la ventana de copia de seguridad?**
  
    No. Las copias de seguridad se desencadenan internamente como parte del servicio administrado y no tienen ninguna relación con la ventana de mantenimiento administrado.

* **¿Dónde se almacenan mis copias de seguridad automatizadas y cómo se administra su retención?**
  
    Azure Database for PostgreSQL crea automáticamente copias de seguridad del servidor y las almacena automáticamente en almacenamiento con redundancia de zona en regiones donde se admiten varias zonas o en almacenamiento con redundancia local en regiones que aún no admiten varias zonas. Estos archivos de copia de seguridad no se pueden exportar. Puede utilizar copias de seguridad solo para restaurar el servidor a un momento dado. El período de retención predeterminado es siete días. Opcionalmente, puede configurar la retención de copia de seguridad hasta 35 días.

* **¿Cómo se realizan las copias de seguridad en servidores habilitados para alta disponibilidad?**
  
    Se hace una copia de seguridad de los volúmenes de datos del servidor flexible mediante instantáneas incrementales de disco administrado desde el servidor principal. La copia de seguridad de WAL se realiza desde el servidor principal o el servidor en espera.

* **¿Cómo puedo validar que las copias de seguridad se realizan en mi servidor?**

    La mejor manera de validar la disponibilidad de las copias de seguridad válidas es realizar restauraciones a un momento dado periódicas y garantizar que las copias de seguridad sean válidas y restaurables. Las operaciones o archivos de copia de seguridad no se exponen a los usuarios finales.

* **¿Dónde puedo ver el uso de la copia de seguridad?**
  
    En Azure Portal, en Supervisión, haga clic en Métricas, puede encontrar "Métrica de uso de copia de seguridad" en la que puede supervisar el uso total de la copia de seguridad.

* **¿Qué ocurre con mis copias de seguridad si se elimina el servidor?**
  
    Si elimina el servidor, todas las copias de seguridad que pertenecen al servidor también se eliminan y no se pueden recuperar. Para proteger los recursos del servidor, después de la implementación, de eliminaciones accidentales o cambios inesperados, los administradores pueden aprovechar los bloqueos de administración.

* **¿Cómo se conservan las copias de seguridad de los servidores detenidos?**

    No se realizan nuevas copias de seguridad para los servidores detenidos. Todas las copias de seguridad anteriores (dentro de la ventana de retención) en el momento de detener el servidor se conservan hasta que se reinicia el servidor después de que la retención de la copia de seguridad para el servidor activo se rija por su ventana de retención de copia de seguridad.

* **¿Cómo se me cobrará y facturará por las copias de seguridad?**
  
    El Servidor flexible proporciona hasta un 100 % del almacenamiento del servidor aprovisionado como almacenamiento de copia de seguridad, sin costos adicionales. El cargo de cualquier almacenamiento de copia de seguridad adicional que se use se realizará por GB/mes según el modelo de precios. La facturación del almacenamiento de copia de seguridad también se rige por el período de retención de copia de seguridad seleccionado y la opción de redundancia de copia de seguridad elegida aparte de la actividad transaccional en el servidor, lo que afecta al almacenamiento de copia de seguridad total usado directamente.

* **¿Cómo se me facturará por un servidor detenido?**
  
    Mientras se detiene la instancia del servidor, no se realizan nuevas copias de seguridad. Se le cobra por el almacenamiento aprovisionado y el almacenamiento de copia de seguridad (copias de seguridad almacenadas dentro de la ventana de retención especificada). El almacenamiento de copia de seguridad gratuito se limita al tamaño de la base de datos aprovisionada y los datos de copia de seguridad excesivos se cobrarán según el precio de la copia de seguridad.

* **He configurado mi servidor con alta disponibilidad con redundancia de zona. ¿Realizará dos copias de seguridad y se me cobrará dos veces?**
  
    No. Independientemente de los servidores de alta disponibilidad o que no sean de alta disponibilidad, solo se mantiene un conjunto de copia de seguridad y solo se le cobrará una vez.

### <a name="restore-related-questions"></a>Preguntas relacionadas con la restauración

* **Cómo restaurar mi servidor?**

    Azure admite la restauración a un momento dado (para todos los servidores), lo que permite a los usuarios restaurar al punto de restauración más reciente o personalizado mediante Azure Portal, la CLI de Azure y la API. 

    Para restaurar el servidor a partir de las copias de seguridad realizadas manualmente mediante herramientas como pg_dump, primero puede crear un servidor flexible y restaurar las bases de datos en el servidor mediante [pg_restore](https://www.postgresql.org/docs/current/app-pgrestore.html).

* **¿Puedo restaurar a otra zona de disponibilidad dentro de la misma región?**
  
    Sí. Si la región admite varias zonas de disponibilidad, la copia de seguridad se almacena en la cuenta ZRS y le permite restaurar a otra zona. 

* **¿Cuánto tiempo se tarda en realizar una restauración a un momento dado? ¿Por qué mi restauración tarda tanto tiempo?**
  
    La operación de restauración de datos de la instantánea no depende del tamaño de los datos, pero el tiempo del proceso de recuperación que aplica los registros (actividades de transacción para reproducir) puede variar en función de la copia de seguridad anterior de la fecha y hora solicitada y la cantidad de registros que se procesarán. Esto es aplicable tanto a la restauración dentro de la misma zona como a una zona diferente. 
 
* **Si restauro mi servidor habilitado para alta disponibilidad, ¿el servidor de restauración se configura automáticamente con alta disponibilidad?**
  
    No. El servidor se restaura como un servidor flexible de instancia única. Una vez completada la restauración, puede configurar opcionalmente el servidor con alta disponibilidad.

* **He configurado mi servidor dentro de una red virtual. ¿Puedo restaurar a otra red virtual?**
  
    Sí. En el momento de la restauración, elija otra red virtual para restaurar.

* **¿Puedo restaurar mi servidor de acceso público en una red virtual o viceversa?**

    No. Actualmente no se admite la restauración de servidores en el acceso público y privado.

* **Cómo realizar un seguimiento de mi operación de restauración?**
  
    Actualmente no hay ninguna manera de realizar un seguimiento de la operación de restauración. Puede supervisar el registro de actividad para ver si la operación está en curso o completa.


## <a name="next-steps"></a>Pasos siguientes

-   Más información sobre la [continuidad empresarial](./concepts-business-continuity.md)
-   Obtenga información sobre la [alta disponibilidad con redundancia de zona](./concepts-high-availability.md)
-   Obtenga información sobre [cómo llevar a cabo una restauración](./how-to-restore-server-portal.md).