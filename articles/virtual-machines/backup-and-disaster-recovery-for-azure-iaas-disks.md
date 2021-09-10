---
title: Copia de seguridad y recuperación ante desastres de discos administrados en máquinas virtuales de Azure
description: En este artículo se explica cómo planear la copia de seguridad y la recuperación ante desastres de discos administrados y máquinas virtuales IaaS en Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 08/26/2021
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: a2c060362a74400a1356d96fb85a4e62d20cac57
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123101805"
---
# <a name="backup-and-disaster-recovery-for-azure-managed-disks"></a>Copia de seguridad y recuperación ante desastres de discos administrados de Azure

**Se aplica a:** :heavy_check_mark: Máquinas virtuales Linux :heavy_check_mark: Máquinas virtuales Windows :heavy_check_mark: Conjuntos de escalado flexibles

En este artículo se explica cómo planear la copia de seguridad y la recuperación ante desastres de discos administrados de Azure.

En primer lugar se habla de las capacidades de tolerancia a errores integradas en la plataforma Azure que protegen frente a errores locales. Luego, analizaremos los escenarios de desastre que las funcionalidades integradas no cubren completamente. También se muestran varios ejemplos de escenarios de carga de trabajo a los que se pueden aplicar distintas consideraciones de copia de seguridad y recuperación ante desastres. Además se tratan algunas soluciones de recuperación ante desastres para discos administrados.

## <a name="introduction"></a>Introducción

Azure usa diversos métodos de redundancia y tolerancia a errores para proteger a los clientes frente a errores de hardware localizados. Los errores locales pueden incluir problemas con una máquina de servidor de Azure Storage en la que se almacenan parte de los datos de un disco virtual, o errores de unidades de estado sólido (SSD) o disco duro (HDD) en ese servidor. Los errores de componentes de hardware aislados pueden producirse durante las operaciones normales.

Azure está diseñado para ser resistente a estos errores. Los desastres de gran envergadura pueden provocar errores o la inaccesibilidad de muchos servidores de almacenamiento o, incluso, de un centro de datos completo. Si bien las máquinas virtuales (VM) y los discos normalmente están protegidos frente a errores localizados, es preciso seguir otros pasos para proteger la carga de trabajo frente a errores catastróficos en toda la región, como un desastre de gran envergadura, que pueden afectar a las máquinas virtuales y los discos.

Además de la posibilidad de errores de la plataforma, pueden producirse problemas con los datos o la aplicación de un cliente. Por ejemplo, es posible que una versión nueva de la aplicación realice inadvertidamente un cambio en los datos que produzca alguna interrupción. En ese caso, tal vez convenga revertir la aplicación y los datos a una versión anterior que contenga el último buen estado conocido. Para ello, es necesario haber realizado copias de seguridad periódicas.

En el caso de la recuperación ante desastres regional, debe realizar una copia de seguridad de los discos de las máquinas virtuales de infraestructura como servicio (IaaS) en otra región. 

Antes de examinar las opciones de copia de seguridad y recuperación ante desastres, vamos a repasar algunos métodos disponibles para controlar errores localizados.

### <a name="azure-iaas-resiliency"></a>Resistencia de IaaS de Azure

Resistencia se refiere a la tolerancia a errores normales que se producen en los componentes de hardware. Resistencia es la capacidad de recuperarse de los errores y seguir funcionando. No se trata de evitar los errores, sino de responder a ellos de manera que se evite el tiempo de inactividad o la pérdida de datos. El objetivo de la resistencia es devolver la aplicación a un estado plenamente operativo después de un error. Las máquinas virtuales y los discos administrados de Azure están diseñados para ser resistentes a errores de hardware comunes. Echemos un vistazo a cómo la plataforma Azure IaaS proporciona esta resistencia.

Una máquina virtual se compone principalmente de dos partes: un servidor de proceso y los discos persistentes. Ambos afectan a la tolerancia a errores de una máquina virtual.

Si el servidor host de proceso de Azure que hospeda la máquina virtual experimenta un error de hardware, lo que es poco frecuente, Azure está diseñado para restaurar automáticamente la máquina virtual en otro servidor. En este escenario, el equipo se reinicia y la máquina virtual vuelve a activarse después de un tiempo. Azure detecta automáticamente estos errores de hardware y comienza la recuperación para ayudar a garantizar que la máquina virtual del cliente esté disponible cuanto antes.

Con respecto a los discos administrados, la durabilidad de los datos es fundamental en una plataforma de almacenamiento persistente. Los clientes de Azure tienen aplicaciones empresariales importantes que se ejecutan en IaaS y dependen de la persistencia de los datos. Azure diseña la protección de estos discos IaaS con tres copias redundantes de datos almacenados localmente. Estas copian ofrecen gran durabilidad contra errores locales. Si se produce un error en uno de los componentes de hardware que contiene el disco, la máquina virtual no se ve afectada porque hay dos copias adicionales que admiten las solicitudes de disco. Funciona bien, aunque dos componentes de hardware distintos que admiten un disco tengan errores al mismo tiempo (lo que es muy poco frecuente). 

Para garantizar que siempre haya tres réplicas, Azure crea automáticamente una nueva copia de los datos en segundo plano si alguna de las tres copias deja de estar disponible. Por consiguiente, no debe ser necesario utilizar usar RAID con discos de Azure para tolerancia a errores. Una configuración simple de RAID 0 debe ser suficiente para seccionar los discos si es necesario para crear volúmenes más grandes.

Gracias a esta arquitectura, Azure ha ofrecido de manera constante durabilidad de nivel empresarial para discos IaaS, con una [tasa de error anualizada](https://en.wikipedia.org/wiki/Annualized_failure_rate) de cero por cierto líder en el sector.

Los errores de hardware localizados en el host de proceso o en la plataforma de almacenamiento a veces pueden traducirse en falta de disponibilidad temporal de la máquina virtual cuya disponibilidad está cubierta por el [contrato de nivel de servicio de Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Azure ofrece también un Acuerdo de Nivel de Servicio (SLA) líder del sector para instancias únicas de máquina virtual que usan discos SSD premium de Azure.

Para proteger las cargas de trabajo de aplicación de tiempo de inactividad debido a la falta temporal de disponibilidad de un disco o una máquina virtual, los clientes pueden usar los [conjuntos de disponibilidad](./availability.md). Dos máquinas virtuales o más de un conjunto de disponibilidad proporcionan redundancia para la aplicación. Azure luego crea estas máquinas virtuales y discos en dominios de error independientes con distintos componentes de alimentación, red y servidor.

Debido a estos dominios de error independientes, los errores de hardware localizados normalmente no afectan a varias máquinas virtuales del conjunto al mismo tiempo. Tener dominios de error independientes proporciona alta disponibilidad de la aplicación. Se recomienda usar conjuntos de disponibilidad cuando se requiere alta disponibilidad.

### <a name="backup-and-disaster-recovery"></a>Copia de seguridad y recuperación ante desastres

La recuperación ante desastres es la capacidad de recuperarse de incidentes poco frecuentes, pero de gran envergadura. Estos incidentes incluyen errores no transitorios de gran escala, como una interrupción del servicio que afecta a toda una región. La recuperación ante desastres incluye la copia de seguridad y el archivado de datos, y puede incluir la intervención manual, como la restauración manual de una base de datos a partir de una copia de seguridad.

Puede que la protección integrada frente a errores localizados de la plataforma de Azure no proteja completamente la máquina virtual y los discos si un desastre de gran envergadura provoca interrupciones a gran escala. Estas incluyen eventos catastróficos, como que un centro de datos se vea afectado por un huracán, un terremoto, un incendio, o si hay errores de unidades de hardware a gran escala. Además, se pueden producir errores debido a problemas de datos o aplicaciones.

Para contribuir a proteger de interrupciones las cargas de trabajo de IaaS, debe planear redundancia y tener copias de seguridad para habilitar la recuperación. Para la recuperación ante desastres, debe crear una copia de seguridad en una ubicación geográfica distinta fuera del sitio principal. Así se ayuda a garantizar que la copia de seguridad no se ve afectada por el mismo evento que originalmente afectó la máquina virtual o los discos. Para más información, consulte [Recuperación ante desastres para aplicaciones de Azure](/azure/architecture/resiliency/disaster-recovery-azure-applications).

Las consideraciones de recuperación ante desastres pueden incluir los aspectos siguientes:

- Alta disponibilidad: la capacidad de la aplicación de seguir ejecutándose en un estado correcto, sin tiempo de inactividad relevante. Por estado correcto, queremos decir que la aplicación responde y los usuarios pueden conectarse e interactuar con ella. Ciertas bases de datos y aplicaciones críticas pueden tener que estar disponibles siempre, aunque existan errores en la plataforma. Para estas cargas de trabajo, puede que necesite planear la redundancia para la aplicación, así como para los datos.

- Durabilidad de los datos: en ciertos casos, la consideración más importante es garantizar que los datos se conserven si se produce un desastre. Por consiguiente, puede que tenga que realizar una copia de seguridad de los datos en otro sitio. Para estas cargas de trabajo, es posible que no se requiera una redundancia completa para la aplicación, sino solo una copia de seguridad periódica de los discos.

## <a name="backup-and-disaster-recovery-scenarios"></a>Escenarios de copia de seguridad y recuperación ante desastres

Veamos algunos ejemplos típicos de escenarios de carga de trabajo de aplicación y las consideraciones sobre la planeación de recuperación ante desastres.

### <a name="scenario-1-major-database-solutions"></a>Escenario 1: Soluciones de bases de datos principales

Considere la posibilidad de usar un servidor de base de datos de producción, como SQL Server u Oracle, que pueda admitir alta disponibilidad. Los usuarios y las aplicaciones de producción críticas dependen de esta base de datos. Es posible que el plan de recuperación ante desastres para este sistema tenga que admitir los requisitos siguientes:

- Los datos deben estar protegidos y ser recuperables.
- El servidor debe estar disponible para su uso.

El plan de recuperación ante desastres puede requerir que se mantenga una réplica de la base de datos en una región distinta como copia de seguridad. Según los requisitos para la recuperación de datos y la disponibilidad del servidor, la solución podría variar de un sitio de réplica activa-pasiva o activa-activa para copias de seguridad periódicas sin conexión de los datos. Las bases de datos relacionales, como SQL Server y Oracle, ofrecen varias opciones de replicación. Con SQL Server, use los [Grupos de disponibilidad AlwaysOn de SQL Server](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) para alta disponibilidad.

Las bases de datos NoSQL, como MongoDB, también admiten [réplicas](https://docs.mongodb.com/manual/replication/) para conseguir redundancia. Se usan las réplicas de caché para conseguir alta disponibilidad.

### <a name="scenario-2-a-cluster-of-redundant-vms"></a>Escenario 2: Un clúster de máquinas virtuales redundantes

Considere la posibilidad de una carga de trabajo controlada por un clúster de máquinas virtuales que proporcionan redundancia y equilibrio de carga. Un ejemplo es un clúster Cassandra implementado en una región. Este tipo de arquitectura ya proporciona un alto nivel de redundancia en esa región. Pero, para proteger la carga de trabajo de un error de nivel regional, debe plantearse la posibilidad de propagar el clúster en dos regiones o realizar copias de seguridad periódicas en otra región.

### <a name="scenario-3-iaas-application-workload"></a>Escenario 3: Carga de trabajo de la aplicación IaaS

Analicemos la carga de trabajo de la aplicación IaaS. Por ejemplo, podría tratarse de una carga de trabajo de producción típica que se ejecuta en una máquina virtual de Azure. Podría ser un servidor web o servidor de archivos que contiene el contenido y otros recursos de un sitio. También podría ser una aplicación empresarial personalizada que se ejecuta en una máquina virtual que almacena los datos, los recursos y el estado de la aplicación en los discos de máquina virtual. En este caso, es importante asegurarse de realizar copias de seguridad periódicamente. La frecuencia de copia de seguridad debe basarse en la naturaleza de la carga de trabajo de máquina virtual. Por ejemplo, si la aplicación se ejecuta todos los días y modifica los datos, la copia de seguridad debe realizarse cada hora.

Otro ejemplo es un servidor de informes que extrae datos de otros orígenes y genera informes agregados. La pérdida de esta máquina virtual o estos discos se podría traducir en la pérdida de los informes. Pero, tal vez sea posible volver a ejecutar el proceso de creación de informes y volver a generar la salida. En ese caso, en realidad no se tiene una pérdida de datos, incluso si un desastre azota el servidor de informes. Como resultado, podría tener un nivel mayor de tolerancia a perder parte de los datos en el servidor de informes. En ese caso, realizar copias de seguridad con menor frecuencia es una posibilidad para reducir los costos.

### <a name="scenario-4-iaas-application-data-issues"></a>Escenario 4: Problemas de datos de la aplicación IaaS

Los problemas de datos de la aplicación IaaS son otra posibilidad. Considere una aplicación que calcula, mantiene y sirve de datos comerciales críticos, como información sobre los precios. Una nueva versión de la aplicación tenía un error de software que calculó incorrectamente el precio y dañó los datos comerciales existentes servidos por la plataforma. En este caso, la mejor medida es revertir a la versión anterior de la aplicación y los datos. Para habilitarla, realice copias de seguridad periódicas del sistema.

## <a name="disaster-recovery-solution-azure-disk-backup"></a>Solución de recuperación ante desastres: Azure Disk Backup 

Azure Disk Backup es una solución de copia de seguridad nativa y basada en la nube que protege los datos en los discos administrados. Se trata de una solución sencilla, segura y rentable que le permite configurar la protección de los discos administrados en unos pocos pasos. Asimismo, le garantiza la recuperación de los datos en caso de que se produzca un desastre.

Azure Disk Backup ofrece una solución de tipo "llave en mano" que le proporciona la oportunidad de administrar el ciclo de vida de las instantáneas para los discos administrados; para ello, solo debe automatizar la creación periódica de instantáneas y guardarlas durante la duración configurada mediante la directiva de copia de seguridad. Puede administrar las instantáneas de disco sin costos de infraestructura, sin necesidad de realizar un scripting personalizado y sin sufrir ninguna sobrecarga de administración. Esta solución de copia de seguridad coherente con los bloqueos realiza en un momento dado una copia de seguridad de un disco administrado mediante instantáneas incrementales, y admite la realización de varias copias de seguridad al día. También es una solución sin agente que no afecta al rendimiento de las aplicaciones de producción. Admite la copia de seguridad y la restauración de los discos de datos y del sistema operativo (incluidos los discos compartidos), tanto si están conectados actualmente a una máquina virtual de Azure en ejecución como si no.

Para obtener más detalles sobre Azure Disk Backup, vea [Introducción a Azure Disk Backup](../backup/disk-backup-overview.md).

## <a name="alternative-solution-consistent-snapshots"></a>Solución alternativa: instantáneas coherentes

Si no puede usar Azure Backup, puede implementar su propio mecanismo de copia de seguridad con instantáneas. Resulta complicado crear las instantáneas coherentes para todos los discos usados que usa una máquina virtual y luego replicar esas instantáneas en otra región. Por esta razón, Azure considera mejor opción usar el servicio Backup que compilar una solución personalizada.

Si se usa el almacenamiento con redundancia local para los discos, el usuario debe replicar los datos por sí mismo.

Una instantánea es una representación de un objeto en un momento dado. Una instantánea se factura según el tamaño incremental de los datos que contiene. Para obtener más información, vea [Creación de una instantánea incremental para discos administrados](disks-incremental-snapshots.md).

### <a name="create-snapshots-while-the-vm-is-running"></a>Creación de instantáneas mientras la máquina virtual está en ejecución

Aunque puede tomar una instantánea en cualquier momento, si la máquina virtual se está ejecutando, todavía hay datos que se transmiten a los discos. Las instantáneas pueden contener operaciones parciales que estaban en marcha. Además, si intervienen varios discos, es posible que se hayan producido instantáneas de distintos discos en distintos momentos. Estos escenarios pueden provocar que las instantáneas se descoordinen. Esta falta de coordinación es especialmente problemática para los volúmenes seccionados cuyos archivos pueden resultar dañados si se realizan cambios durante la copia de seguridad.

Para evitar esta situación, el proceso de copia de seguridad debe implementar los siguientes pasos:

1.  Inmovilice todos los discos.

1.  Vacíe todas las escrituras pendientes.

1.  [Cree una instantánea incremental para discos administrados](disks-incremental-snapshots.md) de todos los discos.

Algunas aplicaciones de Windows como SQL Server ofrecen un mecanismo de copia de seguridad coordinado a través de un servicio de instantáneas de volumen para crear copias de seguridad coherentes con la aplicación. En Linux, puede usar una herramienta como *fsfreeze* para coordinar los discos. Esta herramienta brinda copias de seguridad coherentes con el archivo, pero no instantáneas coherentes con la aplicación. Este proceso es complejo, por lo que debe plantarse el uso de [Azure Disk Backup](../backup/disk-backup-overview.md) o de una solución de copia de seguridad de terceros que ya lo implementen.

El proceso anterior da como resultado una colección de instantáneas coordinadas de todos los discos de máquina virtual, que representa una vista de un momento dado de la máquina virtual. Se trata de un punto de restauración de copia de seguridad para la máquina virtual. Puede repetir el proceso a intervalos programados para crear copias de seguridad periódicas. Vea [Copia de las instantáneas en otra región](#copy-the-snapshots-to-another-region) para obtener los pasos para copiar las instantáneas en otra región para la recuperación ante desastres.

### <a name="create-snapshots-while-the-vm-is-offline"></a>Creación de instantáneas mientras la máquina virtual está sin conexión

Otra opción para crear copias de seguridad coherentes es apagar la máquina virtual y tomar instantáneas de cada disco. Tomar instantáneas sin conexión resulta más sencillo que coordinar instantáneas de una máquina virtual en ejecución, pero requiere algunos minutos de inactividad.

### <a name="copy-the-snapshots-to-another-region"></a>Copia de las instantáneas en otra región

La creación de las instantáneas por sí sola puede no ser suficiente para la recuperación ante desastres. También debe copiar las instantáneas en otra región. Revise las instrucciones de [Copia de copias de seguridad de discos administrados de Azure en otra región con capacidad diferencial de instantáneas incrementales](https://github.com/Azure-Samples/managed-disks-dotnet-backup-with-incremental-snapshots).

## <a name="other-options"></a>Otras opciones

### <a name="sql-server"></a>SQL Server

SQL Server en una máquina virtual tiene su propia funcionalidad integrada para realizar copias de seguridad de la base de datos de SQL Server en Azure Blob Storage o en un recurso compartido de archivos. Para más información, consulte [Copias de seguridad y restauración para SQL Server en máquinas virtuales de Azure](../azure-sql/virtual-machines/windows/azure-storage-sql-server-backup-restore-use.md). Además de la copia de seguridad y restauración, los [grupos de disponibilidad AlwaysOn de SQL Server](../azure-sql/virtual-machines/windows/business-continuity-high-availability-disaster-recovery-hadr-overview.md) pueden conservar réplicas secundarias de las bases de datos. Esta posibilidad reduce considerablemente el tiempo de recuperación ante desastres.

## <a name="next-steps"></a>Pasos siguientes

Consulte [Copia de seguridad de discos de máquina virtual no administrados de Azure con instantáneas incrementales](linux/incremental-snapshots.md).

[1]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-1.png
[2]: ./media/virtual-machines-common-backup-and-disaster-recovery-for-azure-iaas-disks/backup-and-disaster-recovery-for-azure-iaas-disks-2.png