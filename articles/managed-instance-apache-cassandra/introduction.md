---
title: Introducción a Azure Managed Instance for Apache Cassandra
description: Conozca Azure Managed Instance for Apache Cassandra. Este servicio administra la implementación y el escalado de instancias nativas de código abierto de Apache Cassandra en Azure.
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: overview
ms.date: 11/02/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 91af4ca87c2363220081fe5e4912a26a764e85d1
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131844398"
---
# <a name="what-is-azure-managed-instance-for-apache-cassandra"></a>¿Qué es Azure Managed Instance for Apache Cassandra?

El servicio Azure Managed Instance for Apache Cassandra proporciona operaciones automatizadas de implementación y escalado para centros de datos administrados de código abierto de Apache Cassandra. Este servicio le ayuda a acelerar los escenarios híbridos y a reducir el mantenimiento continuo.

:::image type="content" source="./media/introduction/icon.gif" alt-text="Azure Managed Instance for Apache Cassandra es un servicio administrado para Apache Cassandra." border="false":::

## <a name="key-benefits"></a>Ventajas principales

### <a name="hybrid-deployments"></a>Implementaciones híbridas

Puede usar este servicio para colocar fácilmente instancias administradas de centros de datos de Apache Cassandra, que se implementan automáticamente como conjuntos de escalado de máquinas virtuales, en una instancia de Azure Virtual Network nueva o existente. Estos centros de datos se pueden agregar al anillo de Apache Cassandra que se ejecuta de forma local a través de [Azure ExpressRoute](/azure/architecture/reference-architectures/hybrid-networking/expressroute) en Azure, o en otro entorno de nube. Esto se logra mediante la [configuración híbrida](configure-hybrid-cluster.md).

- **Implementación simplificada:** una vez establecida la conectividad híbrida, la implementación es fácil a través del protocolo de chismes.
- **Métricas hospedadas:** las métricas se hospedan en [Prometheus](https://prometheus.io/docs/introduction/overview/) para supervisar la actividad en el clúster. El servicio también se integra con [Azure Monitor para métricas y registros de diagnóstico](monitor-clusters.md).

### <a name="simplified-scaling"></a>Escalado simplificado

En la instancia administrada, el escalado vertical y horizontal de los nodos en un centro de datos está totalmente administrado. Solo tiene que especificar el número de nodos que necesita, y el orquestador de escalado se encarga de establecer su operación dentro del anillo de Cassandra.

### <a name="managed-and-cost-effective"></a>Totalmente administrado y rentable

El servicio proporciona operaciones de administración para las siguientes tareas comunes de Apache Cassandra:

- Aprovisionamiento de un clúster
- Aprovisionamiento de un centro de datos
- Escalado de un centro de datos
- Eliminación de un centro de datos
- Cambio de la configuración de un centro de datos
- Configuración de copias de seguridad

El modelo de precios es flexible, a petición, basado en instancias y no tiene tarifas de licencia. Este modelo de precios le permite ajustarse a sus necesidades de carga de trabajo específicas. Puede elegir la cantidad de núcleos, el tipo de SKU de la máquina virtual, el tamaño de la memoria y el número de discos P30 por nodo.

## <a name="next-steps"></a>Pasos siguientes

Comience con uno de nuestros inicios rápidos:

* [Creación de un clúster de instancia administrada desde Azure Portal](create-cluster-portal.md)
* [Implementación de un clúster de Apache Spark administrado con Azure Databricks](deploy-cluster-databricks.md)
* [Administración de recursos de Azure Managed Instance for Apache Cassandra mediante la CLI de Azure](manage-resources-cli.md)
