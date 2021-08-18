---
title: Implementación de la plataforma de inteligencia empresarial SAP BusinessObjects en Azure | Microsoft Docs
description: Planifique, implemente y configure la plataforma de inteligencia empresarial SAP BusinessObjects en Azure
services: virtual-machines-windows,virtual-network,storage,azure-netapp-files,azure-files
documentationcenter: saponazure
author: dennispadia
manager: juergent
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/05/2020
ms.author: depadia
ms.openlocfilehash: a489b8fd416c750071fa5e292bf916f23c83dab5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747943"
---
# <a name="sap-businessobjects-bi-platform-planning-and-implementation-guide-on-azure"></a>Guía de planificación e implementación de la plataforma de inteligencia empresarial SAP BusinessObjects en Azure

## <a name="overview"></a>Información general

El objetivo de esta guía es proporcionar directrices para planificar, implementar y configurar la plataforma SAP BusinessObjects BI, también conocida como plataforma SAP BOBI, en Azure. Esta guía está destinada a cubrir los servicios y características de Azure comunes que son relevantes para la plataforma SAP BOBI. Esta guía no es una lista exhaustiva de todas las opciones de configuración posibles. Trata soluciones comunes para escenarios de implementación típicos.

Esta guía no pretende reemplazar las guías estándar de instalación y administración de la plataforma SAP BOBI, el sistema operativo o cualquier documentación de la base de datos.

## <a name="plan-and-implement-sap-businessobjects-bi-platform-on-azure"></a>Planificación e implementación de la plataforma SAP BusinessObjects BI en Azure

Microsoft Azure ofrece una amplia gama de servicios, entre los que se incluyen proceso, almacenamiento, redes y muchos otros para que las empresas puedan crear sus aplicaciones sin largos ciclos de adquisición. Las máquinas virtuales (VM) de Azure ayudan a las empresas a implementar recursos informáticos a petición y escalables para diferentes aplicaciones SAP, como aplicaciones basadas en SAP NetWeaver, SAP Hybris o la plataforma SAP BusinessObjects BI, según sus necesidades comerciales. Azure también admite la conectividad entre sitios locales, que permite a las empresas integrar máquinas virtuales de Azure en sus dominios locales, nubes privadas e infraestructura del sistema SAP.

En este documento se proporcionan instrucciones sobre la planificación e implementación de la plataforma SAP BusinessObjects BI en Azure. Complementa la documentación de instalación de SAP y las Notas de SAP, que representan los recursos principales para las instalaciones e implementaciones de SAP BOBI.

### <a name="architecture-overview"></a>Información general sobre la arquitectura

La plataforma SAP BusinessObjects BI es un sistema independiente que puede existir en una sola máquina virtual de Azure o se puede escalar en un clúster de muchas máquinas virtuales de Azure que controlan diferentes componentes. La plataforma SAP BOBI consta de seis niveles conceptuales: nivel de cliente, nivel de web, nivel de administración, nivel de almacenamiento, nivel de procesamiento y nivel de datos. (Para obtener más información sobre cada nivel, consulte la guía del administrador en el portal de ayuda de la [plataforma de inteligencia empresarial SAP BusinessObjects](https://help.sap.com/viewer/product/SAP_BUSINESSOBJECTS_BUSINESS_INTELLIGENCE_PLATFORM/4.3/en-US)). A continuación se indican los detalles generales de cada nivel:

- **Nivel de cliente:** contiene todas las aplicaciones cliente de escritorio que interactúan con la plataforma de inteligencia empresarial para ofrecer distintas funcionalidades de informes, análisis y administración.
- **Nivel web:** contiene las aplicaciones web implementadas en los servidores de aplicaciones web de JAVA. Estas proporcionan funcionalidades de la plataforma de inteligencia empresarial a los usuarios finales a través de un explorador web.
- **Nivel de administración:** coordina y controla todos los componentes que conforman a la plataforma de inteligencia empresarial. Incluye el servidor de administración central (CMS), el servidor de eventos y los servicios asociados.
- **Nivel de almacenamiento:** se encarga del control de archivos, como documentos e informes. También controla el almacenamiento en caché de informes para ahorrar recursos del sistema cuando el usuario accede a los informes.
- **Nivel de procesamiento:** analiza los datos y genera informes y otros tipos de salidas. Es el único nivel que tiene acceso a las bases de datos con datos de informes.
- **Nivel de datos:** consta de los servidores de bases de datos que alojan las bases de datos del sistema CMS y el almacén de datos de auditoría.

La plataforma de inteligencia empresarial de SAP está conformada por una colección de servidores que se ejecutan en uno o más hosts. Es fundamental que elija la estrategia de implementación correcta en función del tamaño, las necesidades comerciales y el tipo de entorno. Para una instalación pequeña, como una de desarrollo o pruebas, puede usar una sola máquina virtual de Azure para el servidor de aplicaciones web, el servidor de base de datos y todos los servidores de la plataforma de inteligencia empresarial. En caso de que use la oferta de base de datos como servicio (DBaaS) de Azure, el servidor de base de datos se ejecutará de manera independiente de otros componentes. En el caso de una instalación de tamaño mediano o grande, puede tener servidores que se ejecuten en varias máquinas virtuales de Azure.

En la siguiente ilustración, se muestra la arquitectura de una implementación a gran escala de la plataforma SAP BOBI en máquinas virtuales de Azure, de manera que cada componente se distribuye y se coloca en conjuntos de disponibilidad que puedan realizar una conmutación por error si se produce una interrupción del servicio.

![Arquitectura de la plataforma SAP BusinessObjects BI en Azure](./media/businessobjects-deployment-guide/businessobjects-architecture-on-azure.png)

#### <a name="architecture-details"></a>Detalles de la arquitectura

- Equilibrador de carga

  En la implementación de varias instancias de SAP BOBI, los servidores de aplicaciones web (o el nivel web) se ejecutan en dos o más hosts. Para distribuir la carga de usuarios uniformemente entre los servidores web, puede usar un equilibrador de carga entre los usuarios finales y los servidores web. En Azure, puede usar [Azure Load Balancer](../../../load-balancer/load-balancer-overview.md) o [Azure Application Gateway](../../../application-gateway/overview.md) para administrar el tráfico a los servidores web.

- Servidores de aplicaciones web

  El servidor web hospeda las aplicaciones web de la plataforma SAP BOBI, como CMC y el panel de inicio de inteligencia empresarial. Para lograr una alta disponibilidad en el servidor web, debe implementar al menos dos servidores de aplicaciones web para administrar la redundancia y el equilibrio de cargas. En Azure, estos servidores de aplicaciones web se pueden colocar en conjuntos de disponibilidad o en zonas de disponibilidad para mejorar su disponibilidad.

  Tomcat es la aplicación web predeterminada para la plataforma de inteligencia empresarial de SAP. Para lograr una alta disponibilidad para Tomcat, habilite la replicación de la sesión mediante el [Interceptor de pertenencia estática](https://tomcat.apache.org/tomcat-9.0-doc/config/cluster-membership.html#Static_Membership_Attributes) en Azure. Este garantiza que el usuario puede acceder a la aplicación web de inteligencia empresarial de SAP incluso cuando se interrumpe el servicio Tomcat.

  > [!Important]
  > De manera predeterminada, Tomcat usa la dirección IP y el puerto de multidifusión para la agrupación en clústeres, que es un método admitido en Azure (Nota de SAP [2764907](https://launchpad.support.sap.com/#/notes/2764907)).

- Servidores de la plataforma de inteligencia empresarial

  Los servidores de la plataforma de inteligencia empresarial incluyen todos los servicios que forman parte de la aplicación SAP BOBI (nivel de administración, nivel de procesamiento y nivel de almacenamiento). Cuando un servidor web recibe una solicitud, detecta cada servidor de la plataforma de inteligencia empresarial (en concreto, todos los servidores CMS de un clúster) y equilibra automáticamente la carga de sus solicitudes. En caso de que se produzca un error en alguno de los hosts de la plataforma de inteligencia empresarial, el servidor web enviará automáticamente las solicitudes a otro host.

  Para lograr una alta disponibilidad o redundancia para la plataforma de inteligencia empresarial, debe implementar la aplicación en al menos dos máquinas virtuales de Azure. En función del tamaño, puede escalar la plataforma de inteligencia empresarial para que se ejecute en más máquinas virtuales de Azure.

- Servidor de repositorio de archivos (FRS)

  El servidor de repositorio de archivos contiene todos los informes y otros documentos de inteligencia empresarial que se han creado. En una implementación de varias instancias, los servidores de la plataforma de inteligencia empresarial se ejecutan en varias máquinas virtuales, y cada una debe tener acceso a estos informes y a otros documentos de inteligencia empresarial. Por lo tanto, un sistema de archivos debe compartirse en todos los servidores de la plataforma de inteligencia empresarial.

  En Azure, puede usar [Azure Files Premium](../../../storage/files/storage-files-introduction.md) o [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) como servidor de repositorio de archivos. Ambos servicios de Azure incluyen redundancia integrada.

  > [!Important]
  > El protocolo SMB para Azure Files está disponible con carácter general, pero la compatibilidad con el protocolo NFS para Azure Files se encuentra actualmente en versión preliminar. Para obtener más información, consulte [La compatibilidad de NFS 4.1 con Azure Files ya se encuentra en versión preliminar](https://azure.microsoft.com/blog/nfs-41-support-for-azure-files-is-now-in-preview/).

- Base de datos de auditoría y CMS
  
  La plataforma SAP BOBI requiere de una base de datos para almacenar los datos del sistema, que se conoce como base de datos CMS. Se usa para almacenar información de plataforma de inteligencia empresarial, como usuarios, servidores, carpetas, documentos, configuraciones y datos de autenticación.

  Las ofertas de Azure [base de datos MySQL](https://azure.microsoft.com/services/mysql/) y [base de datos de Azure SQL](https://azure.microsoft.com/services/sql-database/) de tipo base de datos como servicio (DBaaS) se pueden usar para la base de datos CMS y la base de datos de auditoría. Como se trata de una oferta de PaaS, los clientes no tienen que preocuparse de la operación, disponibilidad y mantenimiento de las bases de datos. El cliente también puede elegir su propia base de datos para CMS, así como un repositorio de auditoría, en función de sus necesidades comerciales.

## <a name="support-matrix"></a>Matrices compatibles

En esta sección se describe la compatibilidad de diferentes componentes de SAP BOBI, como la versión de la plataforma de inteligencia empresarial de SAP BusinessObjects, el sistema operativo y las bases de datos de Azure.

### <a name="sap-businessobjects-bi-platform"></a>Plataforma SAP BusinessObjects BI

La infraestructura como servicio (IaaS) de Azure le permite implementar y configurar la plataforma SAP BusinessObjects BI en Azure Compute. Es compatible con las siguientes versiones de la plataforma SAP BOBI:

- Plataforma SAP BusinessObjects BI 4.3
- Plataforma SAP BusinessObjects BI 4.2 SP04 y posteriores
- Plataforma SAP BusinessObjects BI 4.1 SP05 y posteriores

La plataforma de inteligencia empresarial de SAP se ejecuta en distintas bases de datos y sistemas operativos. La compatibilidad de la plataforma SAP BOBI entre el sistema operativo y la versión de la base de datos puede consultarse en la [matriz de disponibilidad de productos](https://apps.support.sap.com/sap/support/pam) para SAP BOBI.

### <a name="operating-system"></a>Sistema operativo

Azure admite los siguientes sistemas operativos para la implementación de la plataforma SAP BusinessObjects BI.

- Microsoft Windows Server
- SUSE Linux Enterprise Server (SLES)
- Red Hat Enterprise Linux (RHEL)
- Oracle Linux (OL)

Se admite la versión del sistema operativo que aparece en la [matriz de disponibilidad de productos (PAM) para la plataforma SAP BusinessObjects BI](https://support.sap.com/pam), siempre y cuando pueda ejecutarse en la infraestructura de Azure.

### <a name="databases"></a>Bases de datos

La plataforma de inteligencia empresarial necesita una base de datos para CMS y para un almacén de datos de auditoría, que se puede instalar en cualquiera de las bases de datos compatibles que se enumeran en la [matriz de disponibilidad de productos de SAP](https://support.sap.com/pam), incluidas las siguientes:

- Microsoft SQL Server

- [Azure SQL Database](https://azure.microsoft.com/services/sql-database/) (base de datos compatible solo con la plataforma SAP BOBI en Windows)

  Se trata de un motor de base de datos de SQL Server totalmente administrado, basado en la última versión de Enterprise Edition estable de SQL Server. La base de datos de Azure SQL se encarga de la mayoría de las funciones de administración de bases de datos, como actualizar, aplicar revisiones y supervisar sin intervención del usuario. Con Azure SQL Database, puede crear una capa de almacenamiento de datos de gran rendimiento y disponibilidad para las aplicaciones y las soluciones de Azure. Para obtener más información, consulte la documentación sobre [Azure SQL Database](../../../azure-sql/azure-sql-iaas-vs-paas-what-is-overview.md).

- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql/) (siga las mismas instrucciones de compatibilidad que se mencionan para MySQL AB en SAP PAM).

  Es un servicio de base de datos relacional con tecnología de MySQL Community Edition. Al tratarse de una base de datos como servicio (DBaaS) totalmente administrada, puede manejar cargas de trabajo críticas con un rendimiento predecible y escalabilidad dinámica. Cuenta con alta disponibilidad integrada, copias de seguridad automáticas, revisiones de software, detección automática de errores y restauración a un momento dado durante un máximo de 35 días, lo que reduce considerablemente las tareas operativas. Para obtener más información, consulte la documentación sobre [Azure Database for MySQL](../../../mysql/overview.md).

- SAP HANA

- ASE de SAP

- IBM DB2

- Oracle (para conocer las versiones y restricciones, consulte la Nota de SAP [2039619](https://launchpad.support.sap.com/#/notes/2039619))

- MaxDB

En este documento se muestran las instrucciones para implementar la **plataforma SAP BOBI en Windows con Azure SQL Database** y la **plataforma SAP BOBI en Linux con Azure Database for MySQL**. También recomendamos este enfoque para ejecutar la plataforma SAP BusinessObjects BI en Azure.

## <a name="sizing"></a>Ajuste de tamaño

El ajuste de tamaño es un proceso que consiste en determinar los requisitos de hardware para ejecutar la aplicación de manera eficaz. En el caso de la plataforma SAP BOBI, el ajuste de tamaño debe realizarse con la herramienta de ajuste de tamaño de SAP llamada [Quick Sizer](https://www.sap.com/about/benchmark/sizing.quick-sizer.html#quick-sizer). Esta herramienta proporciona los estándares SAPS en función de la entrada, que debe asignarse a los tipos máquinas virtuales de Azure certificadas para SAP. La Nota de SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) especifica la lista de productos de SAP admitidos y los tipos de máquinas virtuales de Azure, junto con los SAPS. Para obtener más información sobre el ajuste de tamaño, consulte la [guía de ajuste de tamaño de SAP BI](https://wiki.scn.sap.com/wiki/display/BOBJ/Sizing+and+Deploying+SAP+BusinessObjects+BI+4.x+Platform+and+Add-Ons).

Para las necesidades de almacenamiento de la plataforma SAP BOBI, Azure ofrece diferentes tipos de [discos administrados](../../managed-disks-overview.md). Se recomienda usar el disco administrado Premium para el directorio de instalación de SAP BOBI y para la base de datos que se ejecuta en máquinas virtuales; siga las instrucciones incluidas en [Implementación de DBMS para la carga de trabajo de SAP](dbms_guide_general.md).

Azure admite dos ofertas DBaaS para el nivel de datos de la plataforma SAP BOBI: [Azure SQL Database](https://azure.microsoft.com/services/sql-database) (aplicación de inteligencia empresarial que se ejecuta en Windows) y [Azure Database for MySQL](https://azure.microsoft.com/services/mysql) (aplicación de inteligencia empresarial que se ejecuta en Linux y Windows). Por lo tanto, en función del resultado de ajuste de tamaño, puede elegir el modelo de compra que mejor se adapte a sus necesidades.

> [!Tip]
> Para obtener una referencia rápida del ajuste de tamaño, considere que 800 SAPS = 1 vCPU mientras asigna el resultado en SAPS del nivel de base de datos de la plataforma SAP BOBI a la base de datos como servicio de Azure (Azure SQL Database o Azure Database for MySQL).

### <a name="sizing-models-for-azure-sql-database"></a>Modelos de ajuste de amaño para la base de datos de Azure SQL

Azure SQL Database ofrece los siguientes tres modelos de compra:

- Basado en núcleos virtuales

  Permite elegir el número de núcleos virtuales, la cantidad de memoria y la cantidad y velocidad del almacenamiento. El modelo de compra basado en núcleo virtual también le permite usar la [Ventaja híbrida de Azure para SQL Server](https://azure.microsoft.com/pricing/hybrid-benefit/) para ahorrar en los costos. Este modelo se ajusta a aquellos clientes que valoran la flexibilidad, el control y la transparencia.

  Se ofrecen tres [opciones de nivel de servicio](../../../azure-sql/database/service-tiers-vcore.md#service-tiers) del modelo de núcleo virtual, incluidas Uso general, Crítico para la empresa e Hiperescala. El nivel de servicio define la arquitectura de almacenamiento, el espacio, los límites de E/S y las opciones de continuidad empresarial relacionadas con la disponibilidad y la recuperación ante desastres. A continuación se indican los detalles generales de cada opción de nivel de servicio:

  1. El nivel de servicio de **uso general** es más adecuado para las cargas de trabajo empresariales. Ofrece opciones de proceso y almacenamiento equilibradas y escalables pensando en el presupuesto. Para más información, consulte las [opciones y límites de recursos](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---provisioned-compute---gen5).
  2. El nivel de servicio **Crítico para la empresa** ofrece a las aplicaciones empresariales la mayor resistencia a los errores mediante el uso de varias réplicas aisladas y proporciona el mayor rendimiento de E/S por réplica de base de datos. Para más información, consulte las [opciones y límites de recursos](../../../azure-sql/database/resource-limits-vcore-single-databases.md#business-critical---provisioned-compute---gen5).
  3. El nivel de servicio de **Hiperescala** resulta adecuado para las cargas de trabajo empresariales que tengan requisitos altamente escalables de almacenamiento y escalado de lectura. Ofrece mayor resistencia a los errores al permitir la configuración de más de una réplica de base de datos aislada. Para más información, consulte las [opciones y límites de recursos](../../../azure-sql/database/resource-limits-vcore-single-databases.md#hyperscale---provisioned-compute---gen5).

- Basado en DTU

  El modelo de compra basado en DTU ofrece una combinación de recursos de proceso, memoria y E/S en tres niveles de servicio, para admitir cargas de trabajo de base de datos de ligeras y pesadas. Los tamaños de proceso de cada nivel ofrecen una combinación diferente de estos recursos, a los que puede agregar recursos de almacenamiento adicionales. Es más adecuado para los clientes que buscan opciones de recursos simples y configuradas previamente.

  Los [niveles de servicio](../../../azure-sql/database/service-tiers-dtu.md#compare-the-dtu-based-service-tiers) en el modelo de compra basado en DTU se diferencian por una variedad de tamaños de proceso con una cantidad fija de almacenamiento incluido, un período de retención fijo de las copias de seguridad y un precio fijo.

- Sin servidor

  El modelo sin servidor escala automáticamente el proceso en función de la demanda de la carga de trabajo y se factura según la cantidad de proceso usado por segundo. El nivel de proceso sin servidor detiene automáticamente las bases de datos durante períodos de inactividad cuando solo se factura el almacenamiento, y reactiva automáticamente las bases de datos cuando se reanuda la actividad. Para más información, consulte las [opciones y límites de recursos](../../../azure-sql/database/resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).
  
  Resulta más adecuado para uso intermitente e impredecible con un menor uso promedio de proceso a lo largo del tiempo. Por lo tanto, este modelo se puede usar para la implementación de SAP BOBI cuando no sea de producción.

> [!Note]
> En el caso de SAP BOBI, se recomienda usar el modelo basado en núcleos virtuales y elegir el nivel de servicio De uso general o Crítico para la empresa en función de las necesidades comerciales.

### <a name="sizing-models-for-azure-database-for-mysql"></a>Modelos de ajuste de tamaño para Azure Database for MySQL

Azure Database for MySQL incluye tres planes de tarifa distintos. Estos se diferencian por la cantidad de proceso en núcleos virtuales, la cantidad de memoria por núcleo virtual y la tecnología de almacenamiento usada para almacenar los datos. A continuación se indican los detalles generales de las opciones. Para obtener más información sobre los distintos atributos, consulte el [Plan de tarifa](../../../mysql/concepts-pricing-tiers.md) para Azure Database for MySQL.

- Básico

  Se usa para las cargas de trabajo de destino que requieren proceso y rendimiento de E/S ligeros.

- De uso general

  Es adecuado para la mayoría de las cargas de trabajo de empresa que requieren un equilibrio entre proceso y memoria con rendimiento de E/S escalable.

- Memoria optimizada

  Para las cargas de trabajo de base de datos de alto rendimiento que requieren rendimiento en memoria para un procesamiento de transacciones más rápido y una mayor simultaneidad.

> [!Note]
> En el caso de SAP BOBI, se recomienda usar el plan de tarifa De uso general o el Optimizada para memoria en función de la carga de trabajo empresarial.

## <a name="azure-resources"></a>Recursos de Azure

### <a name="choosing-regions"></a>Elección de regiones

Una región de Azure es un centro de datos, o una colección de estos, que contiene la infraestructura para ejecutar y hospedar diferentes servicios de Azure. Esta infraestructura incluye un gran número de nodos que funcionan como nodos de proceso o de almacenamiento, o bien que ejecutan funcionalidades de red. No todas las regiones ofrecen los mismos servicios.

La plataforma de inteligencia empresarial de SAP contiene distintos componentes que pueden requerir de tipos de máquinas virtuales específicos, almacenamiento como el de Azure Files o Azure NetApp Files, o bien una base de datos como servicio (DBaaS) para el nivel de datos que podría no estar disponible en determinadas regiones. Puede averiguar la información exacta sobre los tipos de máquina virtual, los tipos de almacenamiento de Azure Storage u otros servicios de Azure en el sitio de [productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/). Si ya está ejecutando sus sistemas SAP en Azure, probablemente ya tiene identificada su región. En ese caso, primero debe investigar si los servicios necesarios están disponibles en esas regiones, para determinar la arquitectura de la plataforma de inteligencia empresarial de SAP.

### <a name="availability-zones"></a>Zonas de disponibilidad

Las zonas de disponibilidad son ubicaciones separadas físicamente dentro de una región de Azure. Cada zona de disponibilidad consta de uno o varios centros de datos equipados con alimentación, refrigeración y redes independientes.

Para lograr una alta disponibilidad en cada nivel de la plataforma de inteligencia empresarial de SAP, puede distribuir las máquinas virtuales en zonas de disponibilidad distintas mediante la implementación de un marco de alta disponibilidad, que puede proporcionar el mejor Acuerdo de Nivel de Servicio en Azure. Para saber más sobre el SLA en Azure, consulte la versión más reciente de los [SLA para máquinas virtuales](https://azure.microsoft.com/support/legal/sla/virtual-machines/).

En el caso del nivel de datos, el servicio de base de datos como servicio (DBaaS) de Azure proporciona un marco de alta disponibilidad de manera predeterminada. Solo es necesario seleccionar las funcionalidades de alta disponibilidad, redundancia y resistencia intrínsecos de la región y servicio para mitigar el tiempo de inactividad de la base de datos frente a interrupciones planeadas y no planeadas, sin necesidad de configurar ningún componente adicional. Para obtener más información del Acuerdo de Nivel de Servicio de la oferta de DBaaS admitida en Azure, consulte [Alta disponibilidad en Azure Database for MySQL](../../../mysql/concepts-high-availability.md) y [Alta disponibilidad para Azure SQL Database](../../../azure-sql/database/high-availability-sla.md).

### <a name="availability-sets"></a>Conjuntos de disponibilidad

Un conjunto de disponibilidad es una funcionalidad de agrupación lógica para aislar los recursos de una máquina virtual (VM) entre sí al implementarlos. Azure se asegura de que las máquinas virtuales colocadas en un conjunto de disponibilidad se ejecuten en varios servidores físicos, grupos de proceso, unidades de almacenamiento y conmutadores de red. Si se produce un error de hardware o software, solo un subconjunto de las máquinas virtuales se ve afectado y la solución general permanece operativa. Por lo tanto, cuando las máquinas virtuales se colocan en conjuntos de disponibilidad, el controlador de tejido de Microsoft Azure distribuye las máquinas virtuales en diferentes dominios de [error](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#fault-domains) y [actualización](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#upgrade-domains) para evitar que todas las máquinas virtuales tengan bloqueado el acceso debido al mantenimiento de la infraestructura o a un error en un dominio de error.

La plataforma de inteligencia empresarial de SAP contiene muchos componentes diferentes y, al diseñar la arquitectura, debe asegurarse de que cada uno de estos componentes sea resistente a cualquier tipo de interrupción. Esto se puede lograr al colocar una máquina virtual de Azure de cada componente en conjuntos de disponibilidad. Tenga en cuenta que al combinar máquinas virtuales de distintas familias de máquinas virtuales en un mismo conjunto de disponibilidad, podría experimentar problemas que impidan incluir un determinado tipo de máquina virtual en dicho conjunto de disponibilidad. Por lo tanto, se recomienda contar con un conjunto de disponibilidad independiente para la aplicación web y para la aplicación de inteligencia empresarial para la plataforma de inteligencia empresarial de SAP, como se resalta en la información general sobre la arquitectura.

Además, el número de dominios de error y actualización que puede utilizar un conjunto de disponibilidad de Azure dentro de una unidad de escalado de Azure no es infinito. Por lo tanto, si agrega continuamente máquinas virtuales a un único conjunto de disponibilidad, llegará un momento en el que dos o más máquinas virtuales coincidan en el mismo dominio de error o actualización. Para obtener más información, consulte la sección [Conjuntos de disponibilidad de Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#azure-availability-sets) del documento sobre implementación y planeamiento de Azure Virtual Machines para SAP.

Para comprender el concepto de conjuntos de disponibilidad de Azure y la relación entre los conjuntos de disponibilidad y los dominios de error y actualización, consulte el artículo sobre [administración de la disponibilidad](../../availability.md).

> [!Important]
> Los conceptos de zonas de disponibilidad de Azure y de conjuntos de disponibilidad de Azure son mutuamente excluyentes. Esto significa que se pueden implementar dos o varias máquinas virtuales en una zona de disponibilidad específica o en un conjunto de disponibilidad de Azure, pero no en ambos.

### <a name="virtual-machines"></a>Máquinas virtuales

Las máquinas virtuales de Azure son una oferta de servicio que permite implementar imágenes personalizadas en Azure como instancias de la infraestructura como servicio (IaaS). Simplifica el mantenimiento y funcionamiento de aplicaciones proporcionando procesos y almacenamiento a petición para hospedar, escalar y administrar aplicaciones web y aplicaciones conectadas.

Azure ofrece varias gamas de máquinas virtuales para todas las necesidades de la aplicación. No obstante, para las cargas de trabajo de SAP, Azure ha reducido la selección a distintas familias de máquinas virtuales que son adecuadas para las cargas de trabajo de SAP y de SAP HANA de un modo más específico. Para obtener más conclusiones, consulte [Qué software de SAP es compatible para su implementación en Azure](sap-supported-product-on-azure.md).

Según el tamaño de la plataforma de inteligencia empresarial de SAP, deberá asignar sus requisitos a una instancia de Azure Virtual Machines, que sea compatible en Azure con el producto de SAP. La Nota de SAP [1928533](https://launchpad.support.sap.com/#/notes/1928533) es un buen punto de partida que enumera los tipos de máquina virtual de Azure admitidos para los productos de SAP en Windows y Linux. Además, debe tener en cuenta que no solo debe seleccionar los tipos de VM que son compatibles, también hay que comprobar si están disponibles en una región específica. Puede comprobar la disponibilidad en la página de [productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/). Para elegir el modelo de precios, puede consultar [Máquinas virtuales de Azure para la carga de trabajo de SAP](planning-guide.md#azure-virtual-machines-for-sap-workload).

### <a name="storage"></a>Storage

Azure Storage es un servicio en la nube administrado por Azure que proporciona almacenamiento altamente disponible, seguro, duradero, escalable y redundante. Algunos de los tipos de almacenamiento tienen un uso limitado para escenarios de SAP. No obstante, hay varios tipos de almacenamiento de Azure Storage que son idóneos para escenarios específicos de carga de trabajo de SAP, o bien están optimizados para ello. Para obtener más información, consulte la guía de [Tipos de Azure Storage para una carga de trabajo de SAP](planning-guide-storage.md), ya que resalta las distintas opciones de almacenamiento que son adecuadas para SAP.

Azure Storage tiene diferentes tipos de almacenamiento disponibles para los clientes, y los detalles de los mismos se pueden leer en el artículo [¿Qué tipos de disco están disponibles en Azure?](../../disks-types.md). La plataforma SAP BOBI usa los siguientes opciones de Azure Storage para compilar la aplicación:

- Discos administrados por Azure

  Es un volumen de almacenamiento de nivel de bloque que Azure administra. Puede usar los discos para las bases de datos y servidores de aplicaciones de la plataforma SAP BOBI, cuando se instalan en máquinas virtuales de Azure. Hay diferentes tipos de discos de [Azure Managed Disks](../../managed-disks-overview.md) disponibles, pero se recomienda usar los [SSD Premium](../../disks-types.md#premium-ssd) para la aplicación y base de datos de la plataforma SAP BOBI.

  En el ejemplo siguiente, se usan los SSD Premium para el directorio de instalación de la plataforma BOBI. Para la base de datos instalada en una máquina virtual, puede usar discos administrados para el volumen de datos y de registro, según lo indican las instrucciones. Las bases de datos CMS y de auditoría suelen ser pequeñas y no tienen los mismos requisitos de rendimiento de almacenamiento que las de otras bases de datos OLAP/OLTP de SAP.

- Azure Files Premium o Azure NetApp Files

  En la plataforma SAP BOBI, el servidor de repositorio de archivos (FRS) hace referencia a los directorios del disco donde se almacena contenido como informes, universos y conexiones que todos los servidores de aplicaciones de ese sistema utilizan. Se puede usar el almacenamiento de [Azure Files Premium](../../../storage/files/storage-files-introduction.md) o [Azure NetApp Files](../../../azure-netapp-files/azure-netapp-files-introduction.md) como sistema de archivos compartidos para el FRS de las aplicaciones de SAP BOBI. Como esta oferta de almacenamiento no está disponible en todas las regiones, consulte el sitio de [Productos disponibles por región](https://azure.microsoft.com/global-infrastructure/services/) para buscar información actualizada.

  Si el servicio no está disponible en su región, puede crear un servidor NFS desde el que pueda compartir el sistema de archivos con la aplicación SAP BOBI. Sin embargo, también debe tener en cuenta su alta disponibilidad.

![Diseño de almacenamiento de la plataforma SAP BusinessObjects BI en Azure](media/businessobjects-deployment-guide/businessobjects-storage-layout.png)

### <a name="networking"></a>Redes

SAP BOBI es una plataforma de informe para presentación de informes y análisis que no contiene datos empresariales. Por lo tanto, el sistema se conecta a otros servidores de bases de datos desde los que obtiene todos los datos y proporciona conclusiones a los usuarios. Azure proporciona una infraestructura de red que permite asignar todos los escenarios que se pueden llevar a cabo con la plataforma de inteligencia empresarial de SAP, como la conexión a un sistema local, a sistemas de diferentes redes virtuales y otros. Para más información, consulte [Redes de Microsoft Azure para cargas de trabajo de SAP](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/virtual-machines/workloads/sap/planning-guide.md#microsoft-azure-networking).

En el caso de la oferta de base de datos como servicio, cualquier base de datos recién creada (ya sea Azure SQL Database o Azure Database for MySQL) tiene un firewall que bloquea todas las conexiones externas. Para permitir el acceso al servicio DBaaS desde máquinas virtuales de la plataforma de inteligencia empresarial, debe especificar una o más reglas de firewall en el nivel de servidor para permitir el acceso a su servidor de DBaaS. Para obtener más información, consulte la sección [Reglas de firewall](../../../mysql/concepts-firewall-rules.md) para Azure Database for MySQL y la sección [Controles de acceso a la red](../../../azure-sql/database/network-access-controls-overview.md) de Azure SQL Database.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de la plataforma de inteligencia empresarial SAP BusinessObjects en Linux](businessobjects-deployment-guide-linux.md)
- [Planeamiento e implementación de Azure Virtual Machines para SAP](planning-guide.md)
- [Implementación de Azure Virtual Machines para SAP](deployment-guide.md)
- [Implementación de DBMS de Azure Virtual Machines para SAP](./dbms_guide_general.md)