---
title: Continuidad empresarial de Azure HDInsight
description: En este artículo se ofrece información general sobre los procedimientos recomendados, la disponibilidad en una sola región y las opciones de optimización para el planeamiento de la continuidad empresarial de Azure HDInsight.
keywords: hadoop alta disponibilidad
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: e3cbae5df6e9a4e5ff17bf1245fcaf190efbbf84
ms.sourcegitcommit: 4f185f97599da236cbed0b5daef27ec95a2bb85f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112371525"
---
# <a name="azure-hdinsight-business-continuity"></a>Continuidad empresarial de Azure HDInsight

Los clústeres de Azure HDInsight dependen de muchos servicios de Azure, como almacenamiento, bases de datos, Active Directory, Active Directory Domain Services, redes y Key Vault. Una aplicación de análisis bien diseñada, de alta disponibilidad y tolerante a errores debe diseñarse con suficiente redundancia para resistir las interrupciones regionales o locales en uno o varios de estos servicios. En este artículo se ofrece información general sobre los procedimientos recomendados, la disponibilidad en una sola región y las opciones de optimización para el planeamiento de la continuidad empresarial.

## <a name="general-best-practices"></a>Procedimientos recomendados generales

En esta sección se tratan algunos procedimientos recomendados que se deben tener en cuenta durante el planeamiento de la continuidad empresarial.

* Determine la funcionalidad de negocio mínima que necesitará si se produce un desastre y por qué. Por ejemplo, evalúe si necesita capacidades de conmutación por error para la capa de transformación de datos (que se muestra en amarillo) *y* la capa de servicio de datos (que se muestra en azul) o si solo necesita conmutación por error para la capa de servicio de datos.

   :::image type="content" source="media/hdinsight-business-continuity/data-layers.png" alt-text="capas de transformación de datos y servicio de datos":::

* Segmente los clústeres en función de la carga de trabajo, el ciclo de vida de desarrollo y los departamentos. El hecho de tener más clústeres reduce las posibilidades de que un único error grande afecte a varios procesos empresariales diferentes.

* Haga que sus regiones secundarias sean de solo lectura. Las regiones de conmutación por error con capacidades de lectura y escritura pueden conducir a arquitecturas complejas.

* Los clústeres transitorios son más fáciles de administrar cuando se produce un desastre. Diseñe sus cargas de trabajo de forma que los clústeres se puedan reciclar y no se mantenga ningún estado en ellos.

* A menudo, las cargas de trabajo se dejan sin terminar si se produce un desastre y es necesario reiniciar en la nueva región. Diseñe las cargas de trabajo para que sean idempotentes por naturaleza.

* Use la automatización durante las implementaciones de clústeres y asegúrese de que los valores de configuración del clúster se incluyen en el script en la medida de lo posible para garantizar una implementación rápida y totalmente automatizada si se produce un desastre.

* Use las herramientas de supervisión de Azure en HDInsight para detectar un comportamiento anómalo en el clúster y establecer las notificaciones de alerta correspondientes. Puede implementar las soluciones de administración específicas del clúster de HDInsight preconfiguradas que recopilan las métricas de rendimiento importantes del tipo de clúster específico. Para más información, consulte [Supervisión de Azure para HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).  

* Suscríbase a las alertas de estado de Azure para recibir notificaciones sobre problemas de servicio, mantenimiento planeado, avisos de estado y seguridad para una suscripción, un servicio o una región. Las notificaciones de estado que incluyen la causa del problema y la hora de llegada estimada resuelta le ayudan a ejecutar mejor la conmutación por error y la conmutación por recuperación. Para obtener más información, consulte [Documentación de Azure Service Health](../service-health/index.yml).

## <a name="single-region-availability"></a>Disponibilidad en una sola región

Un sistema de HDInsight básico tiene los siguientes componentes. Todos los componentes tienen sus propios mecanismos de tolerancia a errores de una sola región.

* Proceso (máquinas virtuales): Clúster de Azure HDInsight
* Tiendas de metadatos: Azure SQL Database
* Almacenamiento: Azure Data Lake Gen2 o Blob Storage
* Autenticación: Azure Active Directory, Azure Active Directory Domain Services y Enterprise Security Package
* Resolución de nombres de dominio: Azure DNS

Hay otros servicios opcionales que se pueden usar, como Azure Key Vault y Azure Data Factory.

:::image type="content" source="media/hdinsight-business-continuity/hdinsight-components.png" alt-text="Componentes de HDInsight":::

### <a name="azure-hdinsight-cluster-compute"></a>Clúster de Azure HDInsight (proceso)

HDInsight ofrece un Acuerdo de Nivel de Servicio de disponibilidad del 99,9 %. Para proporcionar alta disponibilidad en una sola implementación, HDInsight viene acompañado de muchos servicios que están en modo de alta disponibilidad de manera predeterminada. Los mecanismos de tolerancia a errores de HDInsight los proporcionan los servicios de alta disponibilidad de los ecosistemas de Microsoft y Apache OSS.

Los siguientes servicios están diseñados para tener una alta disponibilidad:

#### <a name="infrastructure"></a>Infraestructura

* Nodos principales activos y en espera
* Varios nodos de puerta de enlace
* Tres nodos de Quorum de Zookeeper
* Nodos de trabajo distribuidos por dominios de actualización y de error

#### <a name="service"></a>Servicio

* Servidor Apache Ambari
* Servidores de escala de tiempo de la aplicación para YARN
* Servidor de Historial de trabajos para MapReduce de Hadoop
* Apache Livy
* HDFS
* YARN Resource Manager
* HBase Master

Para obtener más información, consulte la documentación que aparece en [Servicios de alta disponibilidad admitidos en Azure HDInsight](./hdinsight-high-availability-components.md).

No siempre se toma un evento catastrófico que afecta a la funcionalidad empresarial. Los incidentes de servicio en uno o varios de los servicios siguientes en una sola región también pueden provocar la pérdida de la funcionalidad empresarial esperada.

### <a name="hdinsight-metastore"></a>Metastore de HDInsight

HDInsight usa [Azure SQL Database](https://azure.microsoft.com/support/legal/sla/azure-sql-database/v1_4/) como metastore, lo que proporciona un Acuerdo de Nivel de Servicio del 99,99 %. Se conservan tres réplicas de datos dentro de un centro de datos con replicación sincrónica. Si se produce una pérdida de réplica, se proporciona una réplica alternativa sin problemas. La [replicación geográfica activa](../azure-sql/database/active-geo-replication-overview.md) es compatible de forma integrada con un máximo de cuatro centros de datos. Cuando hay una conmutación por error, ya sea manual o del centro de datos, la primera réplica de la jerarquía se convierte automáticamente en compatible con lectura y escritura. Para más información, consulte [Continuidad empresarial de Azure SQL Database](../azure-sql/database/business-continuity-high-availability-disaster-recover-hadr-overview.md).

### <a name="hdinsight-storage"></a>Almacenamiento para HDInsight

HDInsight recomienda Azure Data Lake Storage Gen2 como capa de almacenamiento subyacente. [Azure Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/), incluido Azure Data Lake Storage Gen2, proporciona un Acuerdo de Nivel de Servicio del 99,9 %. HDInsight usa el servicio LRS en el que se conservan tres réplicas de datos dentro de un centro de datos y la replicación es sincrónica. Si se produce una pérdida de réplica, se proporciona una réplica sin problemas.

### <a name="azure-active-directory"></a>Azure Active Directory

[Azure Active Directory](https://azure.microsoft.com/support/legal/sla/active-directory/v1_0/) proporciona un Acuerdo de Nivel de Servicio del 99,9 %. Active Directory es un servicio global con varios niveles de redundancia interna y capacidad de recuperación automática. Para obtener más información, vea cómo [Microsoft está mejorando continuamente la confiabilidad de Azure Active Directory](https://azure.microsoft.com/blog/advancing-azure-active-directory-availability/).

### <a name="azure-active-directory-domain-services-ad-ds"></a>Azure Active Directory Domain Services (AD DS)

[Azure Active Directory Domain Services](https://azure.microsoft.com/support/legal/sla/active-directory-ds/v1_0/) proporciona un Acuerdo de Nivel de Servicio del 99,9 %. Azure AD DS es un servicio de alta disponibilidad que se hospeda en centros de datos distribuidos globalmente. Los conjuntos de réplicas son una característica en vista previa (GB) de Azure AD DS que permite la recuperación ante desastres geográfica si una región de Azure se queda sin conexión. Para más información, consulte [Conceptos y características de los conjuntos de réplicas de Azure Active Directory Domain Services](../active-directory-domain-services/concepts-replica-sets.md).  

### <a name="azure-dns"></a>Azure DNS

[Azure DNS](https://azure.microsoft.com/support/legal/sla/dns/v1_1/) proporciona un Acuerdo de Nivel de Servicio del 100 %. HDInsight usa Azure DNS en varios lugares para la resolución de nombres de dominio.

## <a name="multi-region-cost-and-complexity-optimizations"></a>Optimizaciones de costos y complejidad de varias regiones

La mejora de la continuidad empresarial mediante la recuperación ante desastres de alta disponibilidad entre regiones requiere diseños arquitectónicos de mayor complejidad y un costo más alto. En las siguientes tablas se detallan algunas áreas técnicas que pueden aumentar el costo total de propiedad.

### <a name="cost-optimizations"></a>Optimizaciones de costos

|Área|Causa de la escalación de costo|Estrategias de optimización|
|----|------------------------|-----------------------|
|Almacenamiento de datos|Duplicación de tablas o datos principales en una región secundaria.|Replique solo los datos mantenidos.|
|Salida de datos|Las transferencias de datos entre regiones salientes tienen un precio. Revise las directrices de precios de ancho de banda.|Replique solo los datos mantenidos para reducir la superficie de salida de la región.|
|Cálculo del clúster|Clústeres de HDInsight adicionales en la región secundaria.|Use scripts automatizados para implementar un proceso secundario después del error principal. Use el escalado automático para mantener el tamaño del clúster secundario al mínimo. Use SKU de máquinas virtuales más baratas. Cree los clústeres secundarios en las regiones donde se pueden descontar las SKU de máquinas virtuales.|
|Authentication |Los escenarios multiusuario en la región secundaria generarán configuraciones adicionales de Azure AD DS.|Evite las configuraciones multiusuario en la región secundaria.|

### <a name="complexity-optimizations"></a>Optimizaciones de complejidad

|Área|Causa de la escalación de complejidad|Estrategias de optimización|
|----|------------------------|-----------------------|
|Patrones de lectura y escritura |Requerir que los elementos primarios y secundarios estén habilitados para lectura y escritura. |Diseñe el elemento secundario para que sea de solo lectura.|
|RTO y RPO cero |Requerir pérdida de datos cero (RPO = 0) y tiempo de inactividad cero (RTO = 0). |Diseñe RPO y RTO de manera que se reduzca el número de componentes que deben conmutar por error.|
|Funcionalidad empresarial |Requerir toda la funcionalidad empresarial de la primaria en la secundaria. |Evalúe si puede realizar la ejecución con un subconjunto crítico mínimo básico de la funcionalidad empresarial en la secundaria.|
|Conectividad |Requerir que todos los sistemas ascendentes y descendentes de la conectividad primaria se conecten también a la secundaria.|Limite la conectividad secundaria a un subconjunto crítico mínimo básico.|

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los elementos que se describen en este artículo, consulte:

* [Arquitecturas de continuidad empresarial de Azure HDInsight](./hdinsight-business-continuity-architecture.md)
* [Caso práctico de arquitectura de solución de alta disponibilidad de Azure HDInsight](./hdinsight-high-availability-case-study.md)
* [¿Qué son Apache Hive y HiveQL en Azure HDInsight?](./hadoop/hdinsight-use-hive.md)
