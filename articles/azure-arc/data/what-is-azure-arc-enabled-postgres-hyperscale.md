---
title: ¿Qué es Hiperescala de PostgreSQL habilitada para Azure Arc?
description: ¿Qué es Hiperescala de PostgreSQL habilitada para Azure Arc?
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: fa2a244a0a8e2181f5e3d396980c8239ab868584
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131552998"
---
# <a name="what-is-azure-arc-enabled-postgresql-hyperscale"></a>¿Qué es Hiperescala de PostgreSQL habilitada para Azure Arc?

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]


## <a name="what-is-azure-arc-vs-azure-arc-enabled-data-services-vs-azure-arc-enabled-postgresql-hyperscale"></a>¿Qué es Azure Arc frente a los servicios de datos habilitados para Azure Arc frente a la Hiperescala de PostgreSQL habilitada para Azure Arc?

**Azure Arc** es uno de los pilares de la familia híbrida de Azure: Azure Arc, Azure Stack y Azure IoT. Azure Arc ayuda a los clientes a administrar la complejidad de sus implementaciones híbridas simplificando la experiencia del cliente. Con Azure Stack, Microsoft o sus asociados proporcionan el hardware y el software (un dispositivo). Con Azure Arc, Microsoft solo proporciona el software. El cliente o sus asociados proporcionan la infraestructura de soporte y operan la solución. Azure Arc se admite en Azure Stack. Azure Arc le permite ejecutar servicios de Azure en infraestructuras que residen fuera de los centros de datos de Azure y le permite integrarse con otros servicios administrados de Azure si lo desea.

Los **servicios de datos habilitados para Azure Arc** forman parte de Azure Arc. Es un conjunto de productos y servicios que permite a los clientes administrar sus datos. Permite a los clientes realizar lo siguiente:

- Ejecutar servicios de datos de Azure en cualquier infraestructura física
- Optimizar las operaciones mediante la misma tecnología en la nube en todas partes
- Optimizar los desarrollos de aplicaciones con la misma tecnología subyacente independientemente de dónde se hospede la aplicación o la base de datos (en PaaS de Azure o en Azure Arc)
- Usar tecnologías en la nube en su propio centro de datos y, a la vez, cumplir con los requisitos normativos (residencia de datos y control de clientes). Es decir, "si no puede ir a la nube, la nube va a usted".

Algunos de los valores que los servicios de datos habilitados para Azure Arc proporcionan incluyen:
- Siempre actual
- Escalado elástico
- Aprovisionamiento de autoservicio
- Administración unificada
- Facturación en la nube
- Compatibilidad con escenarios conectados (a Azure) y conectados ocasionalmente (a Azure). (Modo de conectividad indirecta frente a indirecta).

**Hiperescala de PostgreSQL habilitada para Azure Arc** es uno de los motores de base de datos disponibles como parte de los servicios de datos habilitados para Azure Arc. 


## <a name="compare-postgres-solutions-provided-by-microsoft-in-azure"></a>Comparación de soluciones de Postgres proporcionadas por Microsoft en Azure

Microsoft ofrece servicios de base de datos Postgres en Azure de dos maneras:
- Como servicio administrado en PaaS (plataforma como servicio) de Azure
- Como un servicio semi administrado con Azure Arc ya que lo operan los clientes o sus asociados o proveedores.

### <a name="in-azure-paas"></a>En PaaS de Azure
**En [PaaS de Azure](https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer)** , Microsoft ofrece varias opciones de implementación para Postgres como servicio administrado:

:::row:::
    :::column:::
        Servidor único de Azure Database for Postgres y servidor flexible de Azure Database for Postgres. Estos servicios son un factor de forma Postgres de un solo nodo o instancia administrado por Microsoft. El servidor flexible de Azure Database for Postgres es la evolución más reciente de este servicio.
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/azure-database-for-postgresql-bigger.png" alt-text="Azure Database para PostgreSQL":::
    :::column-end:::
:::row-end:::
:::row:::
    :::column:::
        Hiperescala (Citus) de Azure Database for PostgreSQL. Este servicio es el factor de forma de Postgres para varios nodos o instancias administrado por Microsoft. Cuenta con la tecnología de la extensión Citus para Postgres que transforma Postgres de nodo único en un sistema de base de datos distribuida. A medida que escala horizontalmente, distribuye los datos y las consultas que potencialmente permiten que la carga de trabajo alcance niveles sin precedentes de escala y rendimiento. La aplicación ve una única instancia de Postgres, también conocida como grupo de servidores. Sin embargo, en realidad, este grupo de servidores está constituido por varias instancias de Postgres que funcionan conjuntamente. Al escalar horizontalmente, aumenta el número de instancias de Postgres dentro del grupo de servidores que potencialmente mejora el rendimiento y la escalabilidad de la carga de trabajo. Puede decidir, en función de sus necesidades y las características de la carga de trabajo, cuántas instancias de Postgres agrega al grupo de servidores. 
        Para obtener más información, vea los siguientes vídeos:
        - [DEMO: HTAP de alto rendimiento con Hiperescala (Citus) de Postgres:](https://youtu.be/W_3e07nGFxY )
        - [DEMO: Creación de aplicaciones de HTAP con Python y Postgres en Azure:](https://youtu.be/YDT8_riLLs0 )
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale.png" alt-text="Hiperescala (Citus) de Azure Database for PostgreSQL":::
    :::column-end:::
:::row-end:::



### <a name="with-azure-arc"></a>Con Azure Arc

:::row:::
    :::column:::
        **Con Azure Arc**, Microsoft ofrece **un único** producto o servicio: **Hiperescala de PostgreSQL habilitada para Azure Arc**. Con Azure Arc, hemos simplificado la definición del producto y la experiencia del cliente para Postgres en comparación con PaaS de Azure al proporcionar **un producto de Postgres** con capacidad para:
        - implementar Postgres de un solo nodo o instancia como el servidor único o flexible de Azure Database for Postgres,
        - implementar Postgres de varios nodos o instancias como la Hiperescala (Citus) de Azure Database for PostgreSQL,
        - ofrecer gran flexibilidad al permitir a los clientes transformar sus implementaciones de Postgres de un nodo a varios nodos de Postgres y viceversa si lo desean. Pueden hacerlo sin migración de datos y con una experiencia sencilla.
    :::column-end:::
    :::column:::
        :::image type="content" source="media/postgres-hyperscale/postgresql-hyperscale-arc.png" alt-text="Hiperescala de PostgreSQL habilitada para Azure Arc":::
    :::column-end:::
:::row-end:::

Igual que el elemento del mismo nivel en PaaS de Azure, en su forma de varios nodos o instancias, Postgres cuenta con la tecnología de la extensión Citus que transforma Postgres de un solo nodo en un sistema de base de datos distribuida. A medida que escala horizontalmente, distribuye los datos y las consultas que potencialmente permiten que la carga de trabajo alcance niveles sin precedentes de escala y rendimiento. La aplicación ve una única instancia de Postgres, también conocida como grupo de servidores. Sin embargo, en realidad, este grupo de servidores está constituido por varias instancias de Postgres que funcionan conjuntamente. Al escalar horizontalmente, aumenta el número de instancias de Postgres dentro del grupo de servidores que potencialmente mejora el rendimiento y la escalabilidad de la carga de trabajo. Puede decidir, en función de sus necesidades y las características de la carga de trabajo, cuántas instancias de Postgres agrega al grupo de servidores. Si lo desea, puede reducir el número de instancias de Postgres en el grupo de servidores a 1.  Con Azure Arc, todas las implementaciones de Postgres proporcionadas por Microsoft están listas para la Hiperescala.


Con el modo de conectividad directa que ofrecen los servicios de datos habilitados para Azure Arc, puede implementar la Hiperescala de PostgreSQL habilitada para Azure Arc desde Azure Portal. Si usa el modo de conexión indirecta, implementará la Hiperescala de PostgreSQL habilitada para Azure Arc desde la infraestructura que lo hospeda.

**Con la Hiperescala de PostgreSQL habilitada para Azure Arc, puede:**
- Administrar Postgres de manera sencilla
    - Aprovisionar o desaprovisionar instancias de Postgres con un comando
    - En cualquier escala: escalar verticalmente, reducir verticalmente, escalar horizontalmente o reducir horizontalmente 
- Simplificar la supervisión, la conmutación por error, la copia de seguridad, la aplicación de revisiones y la actualización, el control de acceso y más
- Compilar aplicaciones de Postgres a una escala y rendimiento sin precedentes
    - Escalar horizontalmente el proceso entre varias instancias de Postgres
    - Distribuir datos y consultas
    - Ejecutar la extensión Citus
    - Transformar PostgreSQL estándar en un sistema de base de datos distribuida
- Implementar Postgres en cualquier infraestructura
    - Local, en varias nubes (AWS, GCP, Azure), perimetral
- Integrar con Azure (opcional)
- Pagar por lo que usa (facturación por uso)
- Obtener soporte técnico de Microsoft sobre Postgres

**Consideraciones adicionales:**
- La Hiperescala de PostgreSQL habilitada para Azure Arc no es un nuevo motor de base de datos ni es una versión específica de un motor de base de datos existente. Es el mismo motor de base de datos que se ejecuta en PaaS de Azure. Recuerde, con Azure Arc, si no puede ir a la nube de Microsoft; la nube de Microsoft irá a usted. La innovación con Azure Arc reside en la forma en que Microsoft ofrece este motor de base de datos y en las experiencias que Microsoft proporciona en torno a este motor de base de datos. 

- La Hiperescala de PostgreSQL habilitada para Azure Arc tampoco es una solución de replicación de datos. Los datos empresariales permanecen en la implementación de Arc. No se replica en la nube de Azure. A menos que haya elegido configurar una característica del motor de base de datos, como la replicación de datos o las réplicas de lectura. En ese caso, los datos se pueden replicar fuera de la implementación de Postgres: no por Azure Arc, sino porque eligió configurar una característica de replicación de datos.

- No es necesario usar un controlador o proveedor específicos para que la carga de trabajo se ejecute en la Hiperescala de PostgreSQL habilitada para Azure Arc. Cualquier "aplicación de Postgres" debe poder ejecutarse en la Hiperescala de PostgreSQL habilitada para Azure Arc.

- Las operaciones de escalado horizontal y de reducción horizontal no son automáticas. Están controladas por los usuarios. Los usuarios pueden crear scripts para estas operaciones y automatizar la ejecución de esos scripts. No todas las cargas de trabajo pueden beneficiarse del escalado horizontal. Lea más detalles sobre este tema como se sugiere en la sección "Pasos siguientes".

**Para más información sobre estas funcionalidades, también puede ver este episodio de Data Exposed:**
> [!VIDEO https://channel9.msdn.com/Shows/Data-Exposed/What-is-Azure-Arc-Enabled-PostgreSQL-Hyperscale--Data-Exposed/player?format=ny]


## <a name="roles-and-responsibilities-azure-managed-services-platform-as-a-service-paas-_vs_-azure-arc-enabled-data-services"></a>Roles y responsabilidades: Servicios administrados de Azure (plataforma como servicio [PaaS]) _frente a_ los servicios de datos habilitados para Azure Arc
:::image type="content" source="media/postgres-hyperscale/rr-azure-paas-vs-azure-arc.png" alt-text="Roles y responsabilidades de PaaS de Azure frente a Azure Arc":::

## <a name="next-steps"></a>Pasos siguientes
- **Pruébelo.** Empiece a trabajar rápidamente con el [Inicio rápido de Azure Arc](https://github.com/microsoft/azure_arc#azure-arc-enabled-data-services) en Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o en una máquina virtual de Azure. 

- **Impleméntelo, cree el suyo propio.** Siga estos pasos para crear en su propio clúster de Kubernetes: 
   1. [Instalación de las herramientas de cliente](install-client-tools.md)
   2. [Creación del controlador de datos de Azure Arc](create-data-controller.md)
   3. [Creación de un grupo de servidores Hiperescala de Azure Database for PostgreSQL en Azure Arc](create-postgresql-hyperscale-server-group.md) 

- **Learn**
   - [Azure Arc](https://aka.ms/azurearc)
   - Servicios de datos habilitados para Azure Arc [aquí](https://azure.microsoft.com/services/azure-arc/hybrid-data-services) y [aquí](overview.md)
   - [Modos de conectividad y requisitos](connectivity.md)



- **Lea los conceptos y las guías paso a paso de Hiperescala de Azure Database for PostgreSQL para distribuir los datos entre varios nodos de Hiperescala de PostgreSQL y posiblemente beneficiarse de mejores rendimientos**:
    * [Nodos y tablas](../../postgresql/concepts-hyperscale-nodes.md)
    * [Determinar el tipo de aplicación](../../postgresql/concepts-hyperscale-app-type.md)
    * [Elección de una columna de distribución](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [Colocación de tabla](../../postgresql/concepts-hyperscale-colocation.md)
    * [Distribución y modificación de tablas](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [Diseño de una base de datos multiinquilino](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [Diseño de un panel de análisis en tiempo real](../../postgresql/tutorial-design-database-hyperscale-realtime.md)* 

