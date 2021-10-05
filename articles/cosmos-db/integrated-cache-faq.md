---
title: Preguntas más frecuentes sobre la caché integrada de Azure Cosmos DB
description: Preguntas más frecuentes sobre la caché integrada de Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/20/2021
ms.author: tisande
ms.openlocfilehash: a8d1dc69ef64726f55dffd20221a19c3f3114964
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128595149"
---
# <a name="azure-cosmos-db-integrated-cache-frequently-asked-questions"></a>Preguntas más frecuentes sobre la caché integrada de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

La caché integrada de Azure Cosmos DB es una caché en memoria integrada para Azure Cosmos DB. En este artículo se responden las preguntas más frecuentes sobre la caché integrada de Azure Cosmos DB.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="why-does-the-integrated-cache-require-a-dedicated-gateway"></a>¿Por qué la caché integrada requiere una puerta de enlace dedicada?

Si se ha conectado a Azure Cosmos DB mediante el modo de puerta de enlace, ha usado la puerta de enlace estándar. Aunque el back-end de Azure Cosmos DB (su rendimiento y almacenamiento aprovisionados) tiene capacidad dedicada por contenedor, la puerta de enlace estándar se comparte entre muchos clientes. Resulta práctico para muchos clientes compartir una puerta de enlace estándar, ya que los recursos de proceso que consume cada cliente individual son mínimos. Dado que la memoria caché integrada es específica de la cuenta de Azure Cosmos DB y requiere una CPU y memoria significativas, requiere un nodo de puerta de enlace dedicada.

### <a name="what-is-a-dedicated-gateway"></a>¿Qué es una puerta de enlace dedicada?

Una [puerta de enlace dedicada](dedicated-gateway.md) es un proceso del lado servidor que actúa como front-end para los datos en la cuenta de Azure Cosmos DB. Cuando se conecta al punto de conexión de puerta de enlace dedicada, la aplicación envía una solicitud a la puerta de enlace dedicada, que luego enruta la solicitud a distintas particiones back-end.

### <a name="does-using-the-dedicated-gateway-offer-any-other-performance-benefits-over-using-the-standard-gateway"></a>¿El uso de la puerta de enlace dedicada ofrece otras ventajas de rendimiento con respecto al uso de la puerta de enlace estándar?

En general, las solicitudes enrutadas por la puerta de enlace dedicada tendrán una latencia ligeramente menor y más coherente que las solicitudes enrutadas por la puerta de enlace estándar. Incluso las solicitudes que no usan la memoria caché integrada seguirán teniendo una latencia ligeramente menor que la puerta de enlace estándar.

### <a name="what-kind-of-latency-should-i-expect-from-the-integrated-cache"></a>¿Qué tipo de latencia debo esperar de la caché integrada?

Una solicitud atendida por la caché integrada es más rápida porque los datos en caché se almacenan en memoria en la puerta de enlace dedicada, en lugar de en el back-end. Para las lecturas puntuales almacenadas en caché, debe esperar una latencia de 2 a 4 ms.

Para las consultas almacenadas en caché, la latencia depende de la consulta. La caché de consulta funciona almacenando en caché la respuesta del motor de consultas para una consulta determinada. A continuación, esta respuesta se envía de vuelta del lado cliente al SDK para su procesamiento. Para las consultas simples, se requiere un trabajo mínimo en el SDK y las latencias de 2 a 4 ms son típicas. Sin embargo, las consultas más complejas con `GROUP BY` o `DISTINCT` requieren más procesamiento en el SDK, por lo que la latencia puede ser mayor, incluso con la caché de consulta.

Si anteriormente se conectaba a Azure Cosmos DB con el modo directo y cambiaba a la conexión con la puerta de enlace dedicada, puede observar un ligero aumento de la latencia para algunas solicitudes. El uso del modo de puerta de enlace requiere que se envíe una solicitud a la puerta de enlace (en este caso, la puerta de enlace dedicada) y, a continuación, se enruta correctamente al back-end. El modo directo, como sugiere el nombre, permite al cliente comunicarse directamente con el back-end, quitando un salto adicional. 

Si la aplicación usó anteriormente el modo directo, las ventajas de latencia de la caché integrada serán significativas solo en los escenarios siguientes:

- Latencia de lectura puntual para elementos grandes (> 16 KB)
- Consultas complejas o RU elevadas

Si la aplicación usaba anteriormente el modo de puerta de enlace con la puerta de enlace estándar, la caché integrada ofrecerá reducciones de latencia en casi todos los escenarios. 

### <a name="does-the-azure-cosmos-db-availability-sla-extend-to-the-dedicated-gateway-and-integrated-cache"></a>¿Se extiende el Acuerdo de Nivel de Servicio de disponibilidad de Azure Cosmos DB a la puerta de enlace dedicada y a la caché integrada?

Tendremos un Acuerdo de Nivel de Servicio o un SLO de disponibilidad en la puerta de enlace dedicada (y, por tanto, la caché integrada) una vez que la característica esté disponible con carácter general. En escenarios que requieren alta disponibilidad, debe aprovisionar tres veces el número de instancias necesarias de puerta de enlace dedicada. Por ejemplo, si se necesita un nodo de puerta de enlace dedicada en producción, debe aprovisionar dos nodos de puerta de enlace dedicada adicionales para tener en cuenta los posibles tiempos de inactividad o interrupciones.

### <a name="the-integrated-cache-is-only-available-for-sql-core-api-right-now-are-you-planning-on-releasing-it-for-other-apis-as-well"></a>La memoria caché integrada solo está disponible para SQL (Core) API en este momento. ¿Tiene previsto publicarla también para otras API?

La expansión de la caché integrada más allá de SQL API está prevista en el plan de desarrollo a largo plazo, pero más allá de la versión preliminar pública inicial de la caché integrada.

### <a name="what-consistency-does-the-integrated-cache-support"></a>¿Qué coherencia admite la caché integrada?

La caché integrada admite la coherencia final y de sesión. También puede configurar el valor opcional [MaxIntegratedCacheStaleness](integrated-cache.md#maxintegratedcachestaleness), que establece un límite superior en los datos en caché.

## <a name="next-steps"></a>Pasos siguientes

- [Caché integrada](integrated-cache.md)
- [Configuración de la memoria caché integrada](how-to-configure-integrated-cache.md)
- [Puerta de enlace dedicada](dedicated-gateway.md)
- ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Para ello, puede usar información sobre el clúster de bases de datos existente.
    - Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
    - Si conoce las velocidades de solicitud típicas de la carga de trabajo de base de datos actual, lea sobre el [cálculo de las unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).