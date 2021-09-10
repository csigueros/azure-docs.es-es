---
title: Costo total de propiedad (TCO) con Azure Cosmos DB
description: En este artículo se compara el costo total de propiedad de Azure Cosmos DB con bases de datos IaaS y locales.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.reviewer: sngun
ms.openlocfilehash: b594d04c05b5cb05b45bc76f9de256d97aa436a1
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123033634"
---
# <a name="total-cost-of-ownership-tco-with-azure-cosmos-db"></a>Costo total de propiedad (TCO) con Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Azure Cosmos DB está diseñado con la gobernanza de recursos y multiempresa específico. Este diseño permite que Azure Cosmos DB funcione con un costo significativamente menor y ayuda a los usuarios a ahorrar. Actualmente, Azure Cosmos DB admite más de 280 cargas de trabajo de cliente en un solo equipo con la densidad continuamente en aumento, y miles de cargas de trabajo del cliente dentro de un clúster. Equilibra la carga de las réplicas de las cargas de trabajo de los clientes en las distintas máquinas en un clúster y en varios clústeres dentro de un centro de datos. Para más información, consulte [Azure Cosmos DB: ampliación de la frontera de las bases de datos distribuidas globalmente](https://azure.microsoft.com/blog/azure-cosmos-db-pushing-the-frontier-of-globally-distributed-databases/). Debido a la regulación de recursos, la multiempresa y la integración nativa con el resto de la infraestructura de Azure, Azure Cosmos DB es de 4 a 6 veces más económico que MongoDB, Cassandra u otra instancia de NoSQL de OSS que se ejecuta en IaaS, y hasta 10 veces más económico que los motores de base de datos que se ejecutan localmente. Consulte el documento [The total cost of (non) ownership of a NoSQL database cloud service](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf) (Costo total de [no] propiedad de un servicio en la nube de bases de datos NoSQL).

Las soluciones de base de datos NoSQL de OSS, como Apache Cassandra, MongoDB, HBase, se diseñaron para entornos locales. Cuando se ofreces como servicio administrado, son equivalentes a una plantilla de Resource Manager con una base de datos de inquilino para administrar los clústeres aprovisionados y supervisar el soporte técnico. Las arquitecturas NoSQL de OSS requieren una sobrecarga operativa considerable, y la experiencia puede ser difícil y cara de encontrar. Por otro lado, Azure Cosmos DB es un servicio en la nube totalmente administrado que permite a los desarrolladores centrarse en la innovación comercial, en lugar de en la administración y el mantenimiento de la infraestructura de la base de datos.

A diferencia de Azure Cosmos DB, un servicio de base de datos nativo de la nube, los motores de base de datos NoSQL de OSS no se han diseñado y creado teniendo en cuenta la gobernanza de recursos ni la multiempresa específica como principios arquitectónicos fundamentales. Los motores de base de datos NoSQL de OSS, como Cassandra y MongoDB, hacen una suposición fundamental de que todos los recursos de la máquina virtual en la que se ejecutan están disponibles para su uso. Muchos de estos motores de base de datos no pueden funcionar si la cantidad de recursos se sitúa por debajo de un umbral determinado. Por ejemplo, para instancias pequeñas de VM, están disponibles con configuraciones recomendadas por el proveedor, que sugieren normalmente VM a gran escala con un costo mayor. Por lo tanto, no es posible hospedar un motor NoSQL de OSS o cualquier otro motor de base de datos local, y que esté disponible mediante un modelo de cargos basado en consumo, como solicitudes por segundo o almacenamiento consumido.

## <a name="total-cost-of-ownership-of-azure-cosmos-db"></a>Costo total de propiedad de Azure Cosmos DB

El modelo de aprovisionamiento sin servidor de Azure Cosmos DB elimina la necesidad de aprovisionar en exceso la infraestructura de base de datos. Los recursos de Azure Cosmos DB se proporcionan sin necesidad de configuraciones especializadas ni licencias. Como resultado, las aplicaciones con respaldo de Azure Cosmos DB pueden ejecutarse con un ahorro de hasta el 70 % del costo total de propiedad en comparación con las bases de datos NoSQL de OSS. Para ver algunos ejemplos en tiempo real, consulte los [casos de uso de clientes](https://customers.microsoft.com/en-us/search?sq=Cosmos%20DB&ff=&p=0&so=story_publish_date%20desc). Entre otras ventajas del modelo de precios de Azure Cosmos DB se incluyen:

* **Gran economía de precios:** Los asociados, clientes y analistas de mercado han confirmado un valor mayor de todas las características que ofrece Azure Cosmos DB a un precio mucho más bajo en comparación con lo que los clientes pueden obtener al implementar estas soluciones por sí solas o a través de otros proveedores. Las características de bases de datos, como la distribución global, las escrituras multirregión, los modelos de coherencia bien definidos e intuitivos y la indexación automática, se han simplificado enormemente con Azure Cosmos DB sin ninguna complejidad, sobrecarga ni tiempo de inactividad.

* **No se requiere ninguna administración de DevOps de NoSQL:** Con Azure Cosmos DB, no es necesario emplear DevOps para administrar las implementaciones, llevar a cabo el mantenimiento, el escalado ni las revisiones. Puede ejecutar todas las cargas de trabajo que ejecutaría con el clúster NoSQL de OSS hospedado en el entorno local o en una infraestructura de nube.

:::image type="content" source="./media/total-cost-ownership/tco.png" alt-text="Costo de propiedad de Azure Cosmos DB" border="false":::

* **Capacidad de escalar elásticamente:** El rendimiento de Azure Cosmos DB se puede escalar o reducir verticalmente, lo que permite reducir el costo de propiedad durante las horas de poca actividad. Los clústeres NoSQL de OSS implementados en la infraestructura de nube ofrecen elasticidad limitada, y las implementaciones locales no son elásticas por definición. En Azure Cosmos DB, si se aprovisiona un mayor rendimiento, se garantiza que el rendimiento se escalará linealmente. Esta garantía está respaldada SLA financieros y en el percentil 99 a cualquier escala.

* **Economías de escala:** Un servicio administrado como Azure Cosmos DB funciona con un gran número de nodos, integrados con redes, almacenamiento y procesos de manera nativa. Debido a la estandarización a gran escala de Azure Cosmos DB, puede ahorrar costos.

* **Optimizado para la nube:** Azure Cosmos DB está diseñado desde el principio con aislamiento de rendimiento y multiempresa específica. Esto permite ubicar, ejecutar y equilibrar de manera óptima miles de inquilinos y sus cargas de trabajo en los clústeres y centros de datos. En cambio, la generación actual de bases de datos NoSQL de OSS funcionan de forma local, donde se supone que toda la máquina virtual ejecuta una carga de trabajo de un solo inquilino. Además, estas bases de datos no están diseñadas para aprovechar al máximo la infraestructura y el hardware de un proveedor de nube. Por ejemplo, un motor de bases de datos NoSQL de OSS no tiene en cuenta las diferencias entre una máquina virtual frente a una actualización de rutina de una imagen, ni el hecho de que los discos premium ya cuentan con triple replicación. No puede aprovechar estas ventajas y pasa las ventajas y el ahorro a los clientes.

* **Paga por hora:** Las cargas de trabajo a gran escala que tienen que escalarse en cualquier momento solo se cobran por hora. Las cargas de trabajo en una aplicación normalmente varían en función del momento del año y de los datos que se consultan. Con Azure Cosmos DB, puede escalar o reducir verticalmente según sus necesidades y pagar solo lo que necesita. Con los sistemas locales u hospedados en IaaS, no puede competir con este modelo, ya que no hay una manera de retirar el hardware cada hora. En tales casos, con Azure Cosmos DB puede ahorrar entre 10 y 14 veces en promedio.

* **Obtiene diversas características de forma gratuita:** En Azure Cosmos DB, escribir las cargas de trabajo es considerablemente más barato en comparación con los servicios de base de datos alternativos. Además, Azure Cosmos DB ofrece características como [indexación automática](index-policy.md), [período de vida (TTL)](time-to-live.md), [fuente de cambios](change-feed.md) y otras, sin ningún cargo adicional, algo que suelen cobran otros servicios de base de datos.

* **Usa moneda unificada para diversas cargas de trabajo:** A diferencia de las ofertas alternativas, en Azure Cosmos DB no deberá segmentar las cargas de trabajo, por ejemplo, en lecturas y escrituras. Tampoco aprovisionar el rendimiento por tipo de carga de trabajo, es decir, rendimiento de lectura frente a rendimiento de escritura. En Azure Cosmos DB, el rendimiento aprovisionado está reservado mediante una moneda unificada y normalizada en cuanto a unidades de solicitud o RU/s. Azure Cosmos DB no le obliga a asignar una prioridad a las cargas de trabajo, realizar el planeamiento de capacidad ni pagar por cada tipo de capacidad por separado. Un enfoque tal permite intercambiar fácilmente las mismas RU/s entre varias operaciones y tipos de cargas de trabajo.

* **No requiere el aprovisionamiento de VM para escalar:** La mayoría de las bases de datos operativas requieren que use máquinas virtuales grandes para evitar a vecinos ruidosos y para la gobernanza de recursos dinámicos, si quiere escalar. Esto deja en los clientes la carga y el compromiso inicial. Con Azure Cosmos DB, puede comenzar en pequeño y crecer hasta tamaños de carga de trabajo de gran escala sin problemas y sin tiempos de inactividad ni afectar la disponibilidad de los datos.

* **Puede usar el rendimiento aprovisionado hasta un límite máximo:** En virtud de la multiplexación subnúcleo en Azure Cosmos DB, puede saturar el rendimiento aprovisionado para un nivel mucho mayor que las opciones hospedadas en IaaS u ofertas de terceros. Este método ahorra mucho más que las soluciones alternativas.

* **Integración profunda de Azure Cosmos DB en otros servicios de Azure.** Azure Cosmos DB tiene una integración nativa en Redes, Proceso, Azure Functions (sin servidor), Azure IoT y otros servicios de Azure. Con esta integración, obtendrá el mejor rendimiento y velocidad de replicación de datos en todo el mundo con las garantías sólidas. Las soluciones de terceros no podrán ofrecer tanto, o cobrarán una prima para ofrecen dichas características.

* **Obtiene automáticamente alta disponibilidad, con al menos 10 a 20 dominios de error de forma predeterminada:** Azure Cosmos DB admite la distribución de cargas de trabajo entre dominios de error, una característica que es crítica para la alta disponibilidad. Ofrece una alta disponibilidad del 99,999 para las lecturas y escrituras en el percentil 99 en cualquier lugar del mundo. El costo de implementar algo parecido a esto por sí mismo o a través de la solución de un tercero sería alto.

* **Obtiene automáticamente todas las funcionalidades empresariales, sin costo adicional.** Azure Cosmos DB ofrece el conjunto más completo de certificaciones de cumplimiento, seguridad y cifrado en reposo y en movimiento sin costo adicional (en comparación con la competencia). Obtiene automáticamente disponibilidad regional en cualquier lugar del mundo. Puede expandir su base de datos a lo largo de cualquier número de regiones de Azure y agregar o quitar regiones en cualquier momento.

* **Puede guardar hasta un 65 % de los costos con capacidad reservada:** La [capacidad reservada](cosmos-db-reserved-capacity.md) de Azure Cosmos DB le ayuda a ahorrar dinero gracias al pago anticipado de los recursos de Azure Cosmos DB durante uno o tres años. Con los compromisos de un año o tres por adelantado, puede reducir los costos considerablemente y ahorrar de un 20 a un 65 % de descuento en comparación con los precios normales. Puede obtener los mejores SLA en cuanto a capacidad de aprovisionamiento en las cargas de trabajo críticas.

## <a name="capacity-planning"></a>Planificación de capacidad

Como ayuda para calcular el TCO, puede ser útil empezar con el planeamiento de la capacidad. Si planea una migración a Azure Cosmos DB desde un clúster de bases de datos existente, puede usar información sobre el clúster de base de datos existente para el planeamiento de la capacidad.
* Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
* Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).

## <a name="next-steps"></a>Pasos siguientes

* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Puede usar información sobre el clúster de bases de datos existente para planear la capacidad.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).
* Más información sobre la [rentabilidad del modelo de precios de Azure Cosmos DB para los clientes](total-cost-ownership.md)
* Obtenga más información sobre la [optimización para desarrollo y pruebas](optimize-dev-test.md).
* Obtenga más información sobre la [optimización del costo de la capacidad del rendimiento](optimize-cost-throughput.md).
* Obtenga más información sobre la [optimización del costo del almacenamiento](optimize-cost-storage.md).
* Obtenga más información sobre la [optimización del costo de la lectura y la escritura](optimize-cost-reads-writes.md).
* Obtenga más información sobre la [optimización del costo de las consultas](./optimize-cost-reads-writes.md).
* Más información sobre la [optimización del costo de las cuentas de Cosmos de varias regiones](optimize-cost-regions.md)
* Más información sobre el [costo total de (no) propiedad de un servicio en la nube de bases de datos NoSQL](https://documentdbportalstorage.blob.core.windows.net/papers/11.15.2017/NoSQL%20TCO%20paper.pdf)
