---
title: Copia de seguridad y restauración en el Servidor flexible de Azure Database for MySQL
description: Obtenga información sobre los conceptos de la copia de seguridad y restauración con el Servidor flexible de Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 09/21/2020
ms.openlocfilehash: 7564db786084964fa0aca38c6ce8b722b0f3a95a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780467"
---
# <a name="backup-and-restore-in-azure-database-for-mysql-flexible-server-preview"></a>Copia de seguridad y restauración en el Servidor flexible de Azure Database for MySQL (versión preliminar)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Actualmente, Azure Database for MySQL: Servidor flexible se encuentra en versión preliminar pública.

El Servidor flexible de Azure Database for MySQL crea automáticamente copias de seguridad del servidor y las almacena de forma segura en el almacenamiento con redundancia local en la región. Las copias de seguridad pueden utilizarse para restaurar el servidor a un momento dado. Las copias de seguridad y las restauraciones son una parte esencial de cualquier estrategia de continuidad del negocio, ya que protegen los datos frente a daños o eliminaciones accidentales.

## <a name="backup-overview"></a>Introducción a Backup

El Servidor flexible toma copias de seguridad de instantáneas de los archivos de datos y las almacena en un almacenamiento con redundancia local. El servidor también realiza copias de seguridad de registros de transacciones y los almacena en el almacenamiento con redundancia local. Estas copias de seguridad permiten restaurar un servidor a un momento dado dentro del período de retención de copias de seguridad configurado. El período de retención predeterminado es siete días. Opcionalmente, puede configurar la copia de seguridad de base de datos de 1 a 35 días. Todas las copias de seguridad se cifran mediante cifrado AES de 256 bits para los datos almacenados en reposo.

Estos archivos de copia de seguridad no se pueden exportar. Las copias de seguridad solo se pueden usar para operaciones de restauración en el Servidor flexible. También puede usar [mysqldump](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) desde un cliente MySQL para copiar una base de datos.

## <a name="backup-frequency"></a>Frecuencia de copia de seguridad

Las copias de seguridad en servidores flexibles se basan en instantáneas. La primera copia de seguridad de instantáneas se programa inmediatamente después de la creación de un servidor. Las copias de seguridad de instantáneas se realizan una vez al día. Las copias de seguridad del registro de transacciones tienen lugar cada cinco minutos.

## <a name="backup-retention"></a>Retención de copias de seguridad

Las copias de seguridad de base de datos se almacenan en un almacenamiento con redundancia local (LRS), que se almacena en tres copias en una región. Las copias de seguridad se conservan según el valor del período de retención de copia de seguridad en el servidor. Puede seleccionar un período de retención de 1 a 35 días con un período de retención predeterminado de siete días. Puede establecer el período de retención durante la creación del servidor o en otro momento actualizando la configuración de copia de seguridad mediante Azure Portal.

El período de retención de la copia de seguridad rige durante cuánto tiempo se puede realizar una restauración a un momento dado, porque se basa en las copias de seguridad disponibles. El período de retención de copia de seguridad también se puede tratar como un período de recuperación desde una perspectiva de restauración. Todas las copias de seguridad que se necesitan para realizar una restauración a un momento dado dentro del período de retención de copia de seguridad, se conservan en el almacenamiento de copia de seguridad. Por ejemplo, si el período de retención de la copia de seguridad se establece en siete días, la ventana de recuperación comprenderá los últimos siete días. En este escenario, se conservan todas las copias de seguridad necesarias para restaurar el servidor en los últimos siete días. Con una ventana de retención de copia de seguridad de siete días, las instantáneas de base de datos y las copias de seguridad del registro de transacciones se almacenan durante los últimos ocho días (1 día antes de la ventana).

## <a name="backup-storage-cost"></a>Costo del almacenamiento de copia de seguridad

El Servidor flexible proporciona hasta un 100 % del almacenamiento del servidor aprovisionado como almacenamiento de copia de seguridad, sin costos adicionales. El cargo de cualquier almacenamiento de copia de seguridad adicional que se use se realizará por GB/mes. Por ejemplo, si ha aprovisionado un servidor con 250 GB de almacenamiento, tiene 250 GB de almacenamiento disponible para las copias de seguridad del servidor sin ningún cargo adicional. Si la utilización de la copia de seguridad diaria es de 25 GB, puede tener disponible hasta 10 días de almacenamiento de copia de seguridad gratuito. El almacenamiento consumido para copias de seguridad que supere los 250 GB se cobra según el [modelo de precios](https://azure.microsoft.com/pricing/details/mysql/).

Puede usar la métrica [Almacenamiento de copia de seguridad utilizado](./concepts-monitoring.md) en Azure Monitor disponible en Azure Portal para supervisar el almacenamiento de copia de seguridad que usa un servidor. La métrica **Almacenamiento de copia de seguridad utilizado** representa la suma del almacenamiento que consumen todas las copias de seguridad de base de datos y las copias de seguridad de registros, que se conservan en función del período de retención de copia de seguridad establecido para el servidor. Una gran actividad transaccional en el servidor puede hacer que el uso del almacenamiento de copia de seguridad aumente, independientemente del tamaño total de la base de datos.

El medio principal para controlar el costo de almacenamiento de la copia de seguridad es establecer el período de retención de la copia de seguridad adecuado. Puede seleccionar un período de retención de 1 a 35 días.

> [!IMPORTANT]
> Las copias de seguridad de un servidor de base de datos configurado en una configuración de alta disponibilidad con redundancia de zona se producen desde el servidor de base de datos principal, ya que la sobrecarga es mínima con las copias de seguridad de instantáneas.

> [!IMPORTANT]
> Las copias de seguridad con redundancia geográfica no se admiten actualmente en el servidor flexible.

## <a name="point-in-time-restore"></a>Restauración a un momento dado

En el Servidor flexible de Azure Database for MySQL, al realizar una restauración a un momento dado, se crea un servidor nuevo a partir de las copias de seguridad del servidor flexible en la misma región que el servidor de origen. Se crea con la configuración del servidor original para el nivel de proceso, número de núcleos virtuales, tamaño de almacenamiento, período de retención de copia de seguridad y opción de redundancia de copia de seguridad. Además, las etiquetas y configuraciones como las de la red virtual y del firewall se heredan del servidor de origen. El nivel de proceso y la capa de almacenamiento del servidor restaurado, la configuración y los valores de seguridad se pueden cambiar una vez completada la restauración.

> [!NOTE]
> Hay dos parámetros de servidor que se restablecen a los valores predeterminados (y no se copian del servidor principal) después de la operación de restauración.
> *   time_zone: este valor se establece en DEFAULT value SYSTEM.
> *   event_scheduler: este parámetro se establece en OFF en el servidor restaurado.

La restauración a un momento dado es útil en diversos escenarios. Algunos de los casos de uso comunes incluyen los siguientes:
-   Cuando un usuario elimina accidentalmente datos de la base de datos.
-   El usuario quita una tabla o base de datos importante.
-   Una aplicación de usuario sobrescribe accidentalmente datos correctos por otros no válidos debido a un defecto en esta.

Puede elegir entre el último punto de restauración y un punto de restauración personalizado a través de [Azure Portal](how-to-restore-server-portal.md).

-   **Punto de restauración más reciente**: la opción de punto de restauración más reciente le ayuda a restaurar el servidor a la marca de tiempo cuando se desencadenó la operación de restauración. Esta opción es útil para restaurar rápidamente el servidor al estado más actualizado.
-   **Personalizar el punto de restauración**: esto le permitirá elegir cualquier momento dentro del período de retención definido para este servidor flexible. Esta opción es útil para restaurar el servidor en el momento preciso de recuperación de un error de usuario.

El tiempo estimado de recuperación depende de varios factores, entre los que se incluyen los tamaños de las bases de datos, el tamaño de la copia de seguridad del registro de transacciones, el tamaño de proceso de la SKU y la hora de la restauración. La recuperación del registro de transacciones es la parte más lenta del proceso de restauración. Si el momento de la restauración se elige más próximo a la programación de la copia de seguridad, las operación de restauración son más rápidas, ya que la aplicación del registro de transacciones es mínima. A fin de calcular el tiempo de recuperación preciso para el servidor, es muy recomendable probarla en el entorno, ya que tiene demasiadas variables específicas de entorno.

> [!IMPORTANT]
> Si va a restaurar un servidor flexible configurado con alta disponibilidad con redundancia de zona, el servidor restaurado se configurará en la misma región y zona que el servidor principal, y se implementará como un único servidor flexible en un modo sin alta disponibilidad. Consulte [alta disponibilidad con redundancia de zona](concepts-high-availability.md) para un servidor flexible.

> [!IMPORTANT]
> Los servidores eliminados **no se pueden** restaurar. Si elimina el servidor, todas las bases de datos que pertenecen al servidor también se eliminan y no se pueden recuperar. Para proteger los recursos del servidor, después de la implementación, de eliminaciones accidentales o cambios inesperados, los administradores pueden aprovechar los [bloqueos de administración](../../azure-resource-manager/management/lock-resources.md).

## <a name="perform-post-restore-tasks"></a>Tareas posteriores a la restauración

Cuando efectúe la restauración con el mecanismo de recuperación **último punto de restauración** o **punto de restauración personalizado**, deberá realizar las tareas siguientes para que los usuarios y las aplicaciones vuelvan a conectarse:

-   Si el servidor nuevo está destinado a reemplazar al servidor original, redirija a los clientes y las aplicaciones cliente al servidor nuevo.
-   Asegúrese de aplicar reglas de red virtual y de firewall de nivel de servidor adecuadas para que se conecten los usuarios.
-   No se olvide de emplear los permisos de nivel de base de datos y los inicios de sesión apropiados.
-   Configure las alertas según corresponda.

## <a name="frequently-asked-questions-faqs"></a>Preguntas más frecuentes (P+F)

### <a name="backup-related-questions"></a>Preguntas relacionadas con las copias de seguridad

- **¿Cómo hago una copia de seguridad de mi servidor?**
De forma predeterminada, Azure Database for MySQL habilita copias de seguridad automatizadas de todo el servidor (abarcando todas las bases de datos creadas) con un período de retención predeterminado de 7 días. La única manera de realizar manualmente una copia de seguridad es mediante herramientas de comunidad como mysqldump como se documenta [aquí](../concepts-migrate-dump-restore.md#dump-and-restore-using-mysqldump-utility) o mydumper como se documenta [aquí](../concepts-migrate-mydumper-myloader.md#create-a-backup-using-mydumper). Si desea hacer una copia de seguridad de Azure Database for MySQL en un almacenamiento de blobs, consulte el blog de nuestra comunidad tecnológica [Copia de seguridad de Azure Database for MySQL en un almacenamiento de blobs](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/backup-azure-database-for-mysql-to-a-blob-storage/ba-p/803830). 

- **¿Puedo configurar copias de seguridad automáticas para que se conserven a largo plazo?**
No, actualmente solo se admite un máximo de 35 días de retención de copia de seguridad automatizada. Puede realizar copias de seguridad manuales y usarlas para los requisitos de retención a largo plazo.

- **¿Cuáles son las ventanas de copia de seguridad de mi servidor? ¿Puedo personalizarlo?**
La primera copia de seguridad de instantáneas se programa inmediatamente después de la creación de un servidor. Las copias de seguridad de instantáneas se realizan una vez al día. Las copias de seguridad del registro de transacciones tienen lugar cada cinco minutos. Azure administra de forma inherente las ventanas de copia de seguridad y no se pueden personalizar.

- **¿Mis copias de seguridad están cifradas?**
Todos los datos, copias de seguridad y archivos temporales de Azure Database for MySQL creados durante la consultas se cifran mediante AES de 256 bits. El cifrado de almacenamiento siempre está activado y no se puede deshabilitar. 

- **¿Puedo restaurar una o varias bases de datos?**
No se admite la restauración de una o varias bases de datos o tablas. En caso de que desee restaurar bases de datos específicas, realice una restauración a un momento dado y, a continuación, extraiga las tablas o bases de datos necesarias.

- **¿Mi servidor está disponible durante la ventana de copia de seguridad?**
Sí. Las copias de seguridad son operaciones en línea y se basan en instantáneas. La operación de instantánea solo tarda unos segundos y no interfiere con las cargas de trabajo de producción que garantizan la alta disponibilidad del servidor.

- **Al configurar la ventana de mantenimiento para el servidor, ¿es necesario tener en cuenta la ventana de copia de seguridad?**
No, las copias de seguridad se desencadenan internamente como parte del servicio administrado y no tienen ninguna relación con la ventana de mantenimiento administrado.

- **¿Dónde se almacenan mis copias de seguridad automatizadas y cómo se administra su retención?**
Azure Database for MySQL crea automáticamente copias de seguridad del servidor y las almacena en el almacenamiento con redundancia local o con redundancia geográfica configurado por el usuario. Estos archivos de copia de seguridad no se pueden exportar. El período de retención predeterminado es siete días. Opcionalmente, puede configurar la copia de seguridad de base de datos de 1 a 35 días.

- **¿Cómo puedo validar mis copias de seguridad?**
La mejor manera de validar la disponibilidad de las copias de seguridad válidas es realizar restauraciones a un momento dado periódicas y garantizar que las copias de seguridad sean válidas y restaurables. Las operaciones o archivos de copia de seguridad no se exponen a los usuarios finales.

- **¿Dónde puedo ver el uso de la copia de seguridad?**
En la Azure Portal, en la pestaña Supervisión: sección Métricas, puede encontrar la métrica [almacenamiento de copia de seguridad usado](./concepts-monitoring.md) que puede ayudarle a supervisar el uso total de la copia de seguridad.

- **¿Qué ocurre con mis copias de seguridad si se elimina el servidor?**
Si elimina el servidor, todas las copias de seguridad que pertenecen al servidor también se eliminan y no se pueden recuperar. Para proteger los recursos del servidor, después de la implementación, de eliminaciones accidentales o cambios inesperados, los administradores pueden aprovechar los [bloqueos de administración](../../azure-resource-manager/management/lock-resources.md).

- **¿Cómo se me cobrará y facturará por el uso de copias de seguridad?**
El Servidor flexible proporciona hasta un 100 % del almacenamiento del servidor aprovisionado como almacenamiento de copia de seguridad, sin costos adicionales. El cargo de cualquier almacenamiento de copia de seguridad adicional que se use se realizará por GB/mes según el [modelo de precios](https://azure.microsoft.com/pricing/details/mysql/server/). La facturación del almacenamiento de copia de seguridad también se rige por el período de retención de copia de seguridad seleccionado y la opción de redundancia de copia de seguridad elegida aparte de la actividad transaccional en el servidor, lo que afecta al almacenamiento de copia de seguridad total usado directamente.

- **¿Cómo se conservan las copias de seguridad de los servidores detenidos?**
No se realizan nuevas copias de seguridad para los servidores detenidos. Todas las copias de seguridad anteriores (dentro de la ventana de retención) en el momento de detener el servidor se conservan hasta que se reinicia el servidor después de que la retención de la copia de seguridad para el servidor activo se rija por su ventana de retención de copia de seguridad.

- **¿Cómo se facturarán las copias de seguridad de un servidor detenido?**
Mientras se detiene la instancia del servidor, se le cobra por el almacenamiento aprovisionado (incluidas las IOPS aprovisionadas) y el almacenamiento de copia de seguridad (copias de seguridad almacenadas dentro de la ventana de retención especificada). El almacenamiento de copia de seguridad gratuito se limita al tamaño de la base de datos aprovisionada y solo se aplica a los servidores activos. 

### <a name="restore-related-questions"></a>Preguntas relacionadas con la restauración

- **Cómo restaurar mi servidor?**
Azure Portal admite la restauración a un momento dado (para todos los servidores), lo que permite a los usuarios restaurar al punto de restauración más reciente o personalizado. Para restaurar manualmente el servidor a partir de las copias de seguridad realizadas por mysqldump/myDumper, lea [Restauración de la base de datos mediante myLoader](../concepts-migrate-mydumper-myloader.md#restore-your-database-using-myloader).

- **¿Por qué mi restauración tarda tanto tiempo?**
El tiempo estimado para la recuperación del servidor depende de varios factores: 
   - El tamaño de las bases de datos. Como parte del proceso de recuperación, la base de datos debe hidratarse de la última copia de seguridad física y, por lo tanto, el tiempo necesario para recuperarse será proporcional al tamaño de la base de datos.
   - La parte activa de la actividad de la transacción que debe reproducirse para recuperarse. La recuperación puede tardar más en función de la actividad de transacción adicional del último punto de control correcto.
   - El ancho de banda de red si la restauración es a una región diferente 
   - El número de solicitudes de restauración simultáneas que se están procesando en la región de destino 
   - La presencia de la clave principal en las tablas de la base de datos. Para una recuperación más rápida, considere la posibilidad de agregar la clave principal para todas las tablas de la base de datos.  


## <a name="next-steps"></a>Pasos siguientes

-   Más información sobre la [continuidad empresarial](./concepts-business-continuity.md)
-   Obtenga información sobre la [alta disponibilidad con redundancia de zona](./concepts-high-availability.md)
-   Más información sobre [copia de seguridad y recuperación](./concepts-backup-restore.md)
