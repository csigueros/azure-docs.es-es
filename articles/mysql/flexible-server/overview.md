---
title: Información general sobre el servidor flexible de Azure Database for MySQL
description: Obtenga más información acerca del servidor flexible de Azure Database for MySQL, que es un servicio de base de datos relacional de la nube de Microsoft basado en MySQL Community Edition.
author: savjani
ms.author: pariks
ms.service: mysql
ms.custom: mvc, references_regions
ms.topic: overview
ms.date: 08/10/2021
ms.openlocfilehash: 863281f85eac1d467e7935f47a90aacf1b3134dd
ms.sourcegitcommit: df2a8281cfdec8e042959339ebe314a0714cdd5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129153451"
---
# <a name="azure-database-for-mysql---flexible-server-preview"></a>Servidor flexible (versión preliminar) de Azure Database for MySQL

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]


Azure Database for MySQL, con tecnología de MySQL Community Edition, está disponible en dos modos de implementación:

- Servidor único
- Servidor flexible (versión preliminar)

En este artículo proporcionaremos información general y una introducción a los conceptos básicos del modelo de implementación del servidor flexible. Para información sobre cómo decidir qué opción de implementación es adecuada para la carga de trabajo, consulte [Elección de la opción correcta de Servidor MySQL en Azure](./../select-right-deployment-type.md).

## <a name="overview"></a>Información general

Servidor flexible de Azure Database for MySQL es un servicio de base de datos totalmente administrado diseñado para proporcionar un control más granular y una mayor flexibilidad sobre las funciones de administración de bases de datos y las opciones de configuración. En general, el servicio proporciona mayor flexibilidad y personalizaciones de la configuración del servidor en función de los requisitos del usuario. La arquitectura de servidor flexible permite a los usuarios optar por la alta disponibilidad dentro de una única zona de disponibilidad o entre varias. Los servidores flexibles también proporcionan mejores controles de optimización de costos con la capacidad de iniciar o detener el servidor, y SKU ampliables, idóneas para cargas de trabajo que no necesitan una capacidad de proceso completa de forma continuada. El servicio admite actualmente la versiones de la comunidad de MySQL 5.7 y 8.0. El servicio está disponible actualmente en versión preliminar en muchas [regiones de Azure](https://azure.microsoft.com/global-infrastructure/services/).

Los servidores flexibles son idóneos para:

- Desarrollo de aplicaciones que requieren un control mejorado y personalizaciones.
- Alta disponibilidad con redundancia de zona
- Ventanas de mantenimiento administradas

Para obtener las actualizaciones más recientes en servidor flexible, consulte [Novedades de Azure Database for MySQL: servidor flexible](whats-new.md).

![Diagrama conceptual del servidor flexible](media/overview/1-flexible-server-conceptual-diagram.png) 

## <a name="free-12-month-offer"></a>Oferta de 12 meses gratis

Con una [cuenta gratuita de Azure](https://azure.microsoft.com/free/), puede usar Servidor flexible de forma gratuita durante 12 meses con límites mensuales de hasta:
* **750 horas de una instancia de B1MS ampliable**, horas suficientes para ejecutar una instancia de base de datos continuamente cada mes.
* **32 GB** de almacenamiento y **32 GB** de almacenamiento de copia de seguridad. 

Puede aprovechar esta oferta para desarrollar e implementar aplicaciones que usen Azure Database for MySQL: servidor flexible. Para aprender a crear y usar un servidor flexible de forma gratuita mediante una cuenta gratuita de Azure, consulte [este tutorial](how-to-deploy-on-azure-free-account.md). 

## <a name="high-availability-within-and-across-availability-zones"></a>Alta disponibilidad dentro y entre zonas de disponibilidad

Servidor flexible de Azure Database for MySQL (versión preliminar) permite configurar la alta disponibilidad con conmutación automática por error. La solución de alta disponibilidad está diseñada para garantizar que los datos confirmados nunca se pierdan debido a errores y mejorar el tiempo de actividad general de la aplicación.Cuando se configura la alta disponibilidad, el servidor flexible aprovisiona y administra automáticamente una réplica en espera. Hay dos modelos arquitectónicos de alta disponibilidad: 

- **Alta disponibilidad con redundancia de zona**: esta opción es preferible para lograr el aislamiento completo y la redundancia de la infraestructura en varias zonas de disponibilidad. Proporciona el máximo nivel de disponibilidad, pero exige configurar la redundancia de aplicaciones entre zonas. La alta disponibilidad con redundancia de zona es preferible cuando se quiere lograr el máximo nivel de disponibilidad frente a cualquier error de infraestructura en la zona de disponibilidad y donde la latencia en la zona de disponibilidad sea aceptable. La alta disponibilidad con redundancia de zona está disponible en un  [subconjunto de regiones de Azure](overview.md#azure-regions)  en el que la región admite varias zonas de disponibilidad y en la que hay disponibles recurso compartido de archivos Prémium con redundancia de zona. 

:::image type="content" source="./media/concepts-high-availability/1-flexible-server-overview-zone-redundant-ha.png" alt-text="Alta disponibilidad con redundancia de zona":::

- **Alta disponibilidad en la misma zona:** esta opción es preferible para la redundancia de infraestructura con menor latencia de red, ya que el servidor principal y el servidor en espera estarán en la misma zona de disponibilidad. Proporciona alta disponibilidad sin configurar la redundancia de la aplicación entre zonas. La alta disponibilidad en la misma zona se prefiere cuando se quiere lograr el mayor nivel de disponibilidad dentro de una sola zona de disponibilidad con la menor latencia de red. La alta disponibilidad en la misma zona está disponible en [todas las regiones de Azure](overview.md#azure-regions) en las que podemos crear Azure Database for MySQL con la opción Servidor flexible. 

:::image type="content" source="./media/concepts-high-availability/flexible-server-overview-same-zone-ha.png" alt-text="Alta disponibilidad en la misma zona":::

Para más información, consulte los [conceptos sobre la alta disponibilidad](concepts-high-availability.md).

## <a name="automated-patching-with-managed-maintenance-window"></a>Aplicación de revisiones automatizada con la ventana de mantenimiento administrado

El servicio aplica automáticamente revisiones al hardware, sistema operativo y motor de base de datos subyacentes. La aplicación de revisión incluye actualizaciones de seguridad y software. En el motor de MySQL, las actualizaciones de versión secundaria también se incluyen como parte de la versión de mantenimiento planeado. Los usuarios pueden configurar la programación de la aplicación de revisión para que la administre el sistema o definir su programación personalizada. Durante la programación de mantenimiento, se aplica la revisión y es posible que el servidor requiera un reinicio como parte del proceso de aplicación de revisiones para completar la actualización. Con la programación personalizada, los usuarios pueden hacer que el ciclo de aplicación de revisiones sea predecible y elegir una ventana de mantenimiento con un impacto mínimo en el negocio. En general, el servicio sigue una programación mensual de versiones como parte de la integración y publicación continuas.

Consulte [Mantenimiento programado](concepts-maintenance.md) para más detalles. 

## <a name="automatic-backups"></a>Copias de seguridad automáticas

El servicio de servidor flexible crea automáticamente copias de seguridad del servidor y las almacena en el almacenamiento con redundancia local o con redundancia geográfica configurado por el usuario. Las copias de seguridad se pueden usar para restaurar el servidor a un momento dado dentro del período de retención de la copia de seguridad. El período de retención predeterminado es siete días. La retención se puede configurar para un total de 35 días. Todas las copias de seguridad se cifran mediante cifrado AES de 256 bits.

Consulte los [conceptos de copia de seguridad](concepts-backup-restore.md) para obtener más información.

## <a name="network-isolation"></a>Aislamiento de red

Tiene dos opciones de red para conectar su instancia de Servidor flexible de Azure Database for MySQL. Las opciones son **acceso privado (integración con red virtual)** y **acceso público (direcciones IP permitidas)** . 

- **Acceso privado (Integración con red virtual)** : puede implementar el servidor flexible en la instancia de [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Las redes virtuales de Azure proporcionan una comunicación de red privada y segura. Los recursos de una red virtual se pueden comunicar mediante direcciones IP privadas.

  Elija la opción Integración con red virtual si quiere las funcionalidades siguientes:

  - Conexión desde recursos de Azure en la misma red virtual a un servidor flexible mediante direcciones IP privadas
  - Uso de VPN o ExpressRoute para conectarse desde recursos que no son de Azure al servidor flexible
  - Ningún punto de conexión público

- **Acceso público (direcciones IP permitidas)** : puede implementar el servidor flexible a con un punto de conexión público. El punto de conexión público es una dirección DNS que se puede resolver públicamente. La frase "direcciones IP permitidas" hace referencia a un intervalo de direcciones IP a las que decida conceder permiso para acceder al servidor. Estos permisos se denominan **reglas de firewall**.

Consulte los [conceptos de redes](concepts-networking.md) para obtener más información.

## <a name="adjust-performance-and-scale-within-seconds"></a>Ajustar el rendimiento y la escala en cuestión de segundos

El servicio de servidor flexible está disponible en tres niveles de SKU: flexible, de uso general y optimizado para memoria. El nivel Ampliable es el más adecuado para las cargas de trabajo de desarrollo de bajo costo y simultaneidad baja que no necesitan una capacidad de proceso completa continuamente. Los niveles De uso general y Optimizado para memoria son los más adecuados para las cargas de trabajo de producción que requieren un alto nivel de simultaneidad, escala y rendimiento predecible. Puede compilar su primera aplicación en una base de datos pequeña por poco dinero al mes y, después, ajustar fácilmente la escala para satisfacer las necesidades de la solución. El escalado de almacenamiento se realiza en línea y admite el crecimiento automático del almacenamiento. Servidor flexible permite aprovisionar IOPS adicionales, hasta 20 000 por encima del límite de IOPS gratuitos, independientemente del almacenamiento. Con esta característica puede aumentar o disminuir el número de IOPS aprovisionadas en función de los requisitos de la carga de trabajo en cualquier momento. La escalabilidad dinámica permite a la base de datos responder de manera transparente a los cambiantes requisitos de recursos. Solo paga por los recursos que consume. 

Consulte [Conceptos de proceso y almacenamiento](concepts-compute-storage.md) para obtener más información.

## <a name="scale-out-your-read-workload-with-up-to-10-read-replicas"></a>Escalabilidad horizontal de la carga de trabajo de lectura con hasta 10 réplicas de lectura

MySQL es uno de los motores de base de datos populares para ejecutar aplicaciones web y móviles a escala de Internet. Muchos de nuestros clientes lo usan para sus servicios de educación en línea, servicios de streaming de vídeo, soluciones de pago digital, plataformas de comercio electrónico, servicios de juegos, portales de noticias, gobierno y sitios web de atención sanitaria. Estos servicios son necesarios para servir y escalar a medida que aumenta el tráfico en la aplicación web o móvil.

En el caso de las aplicaciones, la aplicación se desarrolla normalmente en Java o PHP y se migra para ejecutarse en  [conjuntos de escalado de máquinas virtuales de Azure](../../virtual-machine-scale-sets/overview.md)  o en instancias de  [Azure App Services](../../app-service/overview.md) , o bien se incluye en contenedores para ejecutarse en  [Azure Kubernetes Service (AKS)](../../aks/intro-kubernetes.md). Con el conjunto de escalado de máquinas virtuales, App Service o AKS como infraestructura subyacente, el escalado de aplicaciones se simplifica al aprovisionar de forma instantánea nuevas máquinas virtuales y replicar los componentes sin estado de las aplicaciones para satisfacer las solicitudes. Sin embargo, la base de datos a menudo acaba siendo un cuello de botella como componente con estado centralizado.

La característica de réplica de lectura permite replicar datos de Azure Database for MySQL con servidor flexible en un servidor de solo lectura. Puede replicar desde el servidor de origen en **hasta 10 réplicas**. Las réplicas se actualizan asincrónicamente mediante la [tecnología de replicación basada en la posición de los archivos de registros binarios (binlog)](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html) nativa del motor de MySQL. Puede usar una solución de proxy de equilibrador de carga como [ProxySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/load-balance-read-replicas-using-proxysql-in-azure-database-for/ba-p/880042) para escalar horizontalmente la carga de trabajo de la aplicación de manera perfecta para leer réplicas sin ningún costo de refactorización de la aplicación. 

Para más información, consulte los [conceptos sobre las réplicas de lectura](concepts-read-replicas.md).

## <a name="setup-hybrid-or-multi-cloud-data-synchronization-with-data-in-replication"></a>Configuración de la sincronización de datos híbridos o en varias nubes con replicación de los datos de entrada

La replicación de datos de entrada permite sincronizar datos de una instancia externa de MySQL Server con el servicio flexible de Azure Database for MySQL. El servidor externo puede ser local, de máquinas virtuales, servidor único de Azure Database for MySQL o un servicio de base de datos hospedado por otros proveedores de nube. La replicación de datos de entrada se basa en la posición del archivo de registro binario (binlog). Los escenarios principales que se deben tener en cuenta para usar la replicación de datos de entrada son los siguientes:
* Sincronización de datos híbridos
* Sincronización en varias nubes
* [Migración al servidor flexible con tiempo de inactividad mínimo](../../mysql/howto-migrate-single-flexible-minimum-downtime.md)

Para más información, consulte los [conceptos sobre la replicación de los datos de entrada](concepts-data-in-replication.md).


## <a name="stopstart-server-to-optimize-cost"></a>Detención o inicio del servidor para optimizar el costo

El servicio de servidor flexible le permite detener e iniciar el servidor a petición para optimizar los costos. La facturación del nivel de proceso se detiene inmediatamente al detener el servidor. De este modo puede lograr un importante ahorro en el costo durante el desarrollo, las pruebas y las cargas de trabajo de producción predecibles enlazadas con límites de tiempo. El servidor permanece en estado detenido durante siete días, a menos que se reinicie antes.

Para más información, consulte los [conceptos sobre el servidor](concept-servers.md).

## <a name="enterprise-grade-security-and-privacy"></a>Privacidad y seguridad de calidad empresarial

El servicio de servidor flexible usa el módulo criptográfico con validación FIPS 140-2 para el cifrado del almacenamiento de los datos en reposo. Se cifran los datos, incluidas las copias de seguridad, y los archivos temporales creados mientras se ejecutan las consultas. El servicio usa el cifrado AES de 256 bits que se incluye en el cifrado de Azure Storage y las claves las puede administrar el sistema (valor predeterminado).

El servicio cifra los datos en movimiento con la seguridad de la capa de transporte que se aplica de manera predeterminada. De manera predeterminada Servidor flexible admite conexiones cifradas mediante la Seguridad de la capa de transporte (TLS 1.2) y denegará todas las conexiones entrantes con TLS 1.0 y TLS 1.1. Para deshabilitar el cumplimiento de SSL, hay que establecer el parámetro de servidor require_secure_transport y se puede establecer el valor mínimo de tls_version para el servidor.

Para más información, consulte sobre el [uso de conexiones cifradas para servidores flexibles](how-to-connect-tls-ssl.md).

Los servidores flexibles permiten el acceso privado completo a los servidores mediante la integración con la [red virtual de Azure](../../virtual-network/virtual-networks-overview.md). Solo puede acceder a los servidores de la red virtual de Azure y conectarse con ellos mediante las direcciones IP privadas. Con la integración con red virtual, se deniega el acceso público y no se puede acceder a los servidores mediante puntos de conexión públicos.

Para más información, consulte [Conceptos de redes](concepts-networking.md).

## <a name="monitoring-and-alerting"></a>Supervisión y alertas

El servicio de servidor flexible está equipado con características integradas de alertas y supervisión de rendimiento. Todas las métricas de Azure tienen una frecuencia de un minuto y cada métrica proporciona 30 días de historial. Puede configurar alertas en las métricas. El servicio expone las métricas del servidor host para supervisar el uso de los recursos y permite configurar registros de consultas lentas. Con estas herramientas, puede optimizar rápidamente sus cargas de trabajo y configurar el servidor para lograr el mejor rendimiento. Además, puede usarlas e integrarlas con herramientas de supervisión de la comunidad como [Percona Monitoring and Management con su servidor flexible de MySQL](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/monitor-azure-database-for-mysql-using-percona-monitoring-and/ba-p/2568545). 

Para más información, consulte los [conceptos sobre la supervisión](concepts-monitoring.md).

## <a name="migration"></a>Migración

El servicio ejecuta la versión Community de MySQL. Esto ofrece una compatibilidad total con las aplicaciones y requiere un costo mínimo de refactorización para migrar las aplicaciones existentes desarrolladas en el motor de MySQL al servidor flexible. La migración al servidor flexible se puede realizar con la siguiente opción:

### <a name="offline-migrations"></a>Migraciones sin conexión
*   Uso de Azure Data Migration Service cuando el ancho de banda de red entre el origen y Azure es bueno (por ejemplo: ExpressRoute de alta velocidad). Más información con instrucciones paso a paso: [Migración de MySQL a Azure Database for MySQL sin conexión mediante DMS - Azure Database Migration Service](../../dms/tutorial-mysql-azure-mysql-offline-portal.md)
*   Use mydumper/myloader para aprovechar la configuración de compresión para mover datos de forma eficaz a través de redes de baja velocidad (como Internet público). Más información con las instrucciones paso a paso de [Migración de bases de datos grandes a Azure Database for MySQL mediante mydumper/myloader](../../mysql/concepts-migrate-mydumper-myloader.md).

### <a name="online-or-minimal-downtime-migrations"></a>Migraciones con tiempo de inactividad mínimo o en línea
Use la replicación de datos de entrada con la copia de seguridad y restauración coherentes de mydumper/myloader para la primera inicialización. Más información con instrucciones paso a paso: [Tutorial: Migración con tiempo de inactividad mínimo de Azure Database for MySQL (servidor único) a Azure Database for MySQL (servidor flexible)](../../mysql/howto-migrate-single-flexible-minimum-downtime.md).

Para migrar de Azure Database for MySQL: servidor único a servidor flexible en cinco sencillos pasos sencillos, consulte [este blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/migrate-from-azure-database-for-mysql-single-server-to-flexible/ba-p/2674057).

Para más información, consulte [Guía de migración para Azure Database for MySQL](../../mysql/migrate/mysql-on-premises-azure-db/01-mysql-migration-guide-intro.md).

## <a name="azure-regions"></a>Regiones de Azure

Una ventaja de ejecutar una carga de trabajo en Azure es su alcance global. El servidor flexible para Azure Database for MySQL está disponible actualmente en las siguientes regiones de Azure:

| Region (Región) | Disponibilidad | Alta disponibilidad en la misma zona | Alta disponibilidad con redundancia de zona |
| --- | --- | --- | --- |
| Este de Australia | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Sudeste de Australia | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Sur de Brasil | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Centro de Canadá | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Este de Canadá | :heavy_check_mark: | :x: | :x: |
| Centro de la India | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Centro de EE. UU. | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Asia Oriental (Hong Kong) | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Este de EE. UU. | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Este de EE. UU. 2 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Centro de Francia | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Centro-oeste de Alemania | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Japón Oriental | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Japón Occidental | :heavy_check_mark: | :x: | :x: |
| Centro de Corea del Sur | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Corea del Sur | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Norte de Europa | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Este de Noruega | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Sudeste de Asia | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Centro-sur de EE. UU. | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Norte de Sudáfrica | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Norte de Suiza | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Sur de Reino Unido | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oeste de Reino Unido | :heavy_check_mark: | :x: | :x: |
| Norte de Emiratos Árabes Unidos | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Oeste de EE. UU. | :heavy_check_mark: | :heavy_check_mark: | :x: |
| Oeste de EE. UU. 2 | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Oeste de Europa | :heavy_check_mark: | :heavy_check_mark: | :heavy_check_mark: |
| Centro-Oeste de EE. UU. | :heavy_check_mark: | :heavy_check_mark: | :x: |

## <a name="contacts"></a>Contactos

Para cualquier pregunta o sugerencia que pueda tener sobre el servidor flexible de Azure Database for MySQL, envíe un correo electrónico al equipo de Azure Database for MySQL ([@Ask Azure DB for MySQL](mailto:AskAzureDBforMySQL@service.microsoft.com)). Esta dirección de correo electrónico no es un alias de soporte técnico.

Además, tenga en cuenta los siguientes puntos de contacto según corresponda:

- Para ponerse en contacto con el servicio de soporte técnico de Azure, [presente una incidencia de soporte técnico en Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Para corregir un problema con la cuenta, envíe una [solicitud de soporte técnico](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) en Azure Portal.
- Para proporcionar comentarios o solicitar nuevas características, cree una entrada mediante [UserVoice](https://feedback.azure.com/forums/597982-azure-database-for-mysql).

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha leído la introducción al modo de implementación del servidor único de Azure Database for MySQL, ya está listo para lo siguiente:

- Creación del primer servidor.
  - [Creación de un servidor flexible de Azure Database for MySQL mediante Azure Portal](quickstart-create-server-portal.md).
  - [Creación de un servidor flexible de Azure Database for MySQL mediante la CLI de Azure](quickstart-create-server-cli.md)
  - [Administración de un servidor flexible de Azure Database for MySQL mediante la CLI de Azure](how-to-manage-server-portal.md)

- Cree su primera aplicación con su lenguaje preferido:
  - [Python](connect-python.md)
  - [PHP](connect-php.md)
