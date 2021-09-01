---
title: Descripción de las diferencias entre las bases de datos relacionales y NoSQL de Azure Cosmos DB
description: En este artículo se enumeran las diferencias entre las bases de datos NoSQL y relacionales.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/16/2019
ms.reviewer: sngun
ms.openlocfilehash: 7926994766646f89a2ea853df3cc60715005b814
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123097794"
---
# <a name="understanding-the-differences-between-nosql-and-relational-databases"></a>Descripción de las diferencias entre las bases de datos relacionales y NoSQL
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

En este artículo se enumeran algunas de las ventajas principales de las bases de datos NoSQL frente a las bases de datos relacionales. También se analizan algunos de los desafíos de trabajar con NoSQL. Para obtener información detallada de los distintos almacenes de datos que existen, consulte nuestro artículo sobre [cómo elegir el almacén de datos correcto](/azure/architecture/guide/technology-choices/data-store-overview).

## <a name="high-throughput"></a>Capacidad de proceso elevada

Uno de los desafíos más obvios a la hora de mantener un sistema de bases de datos relacionales es que la mayoría de los motores relacionales usan bloqueos y bloqueos temporales para aplicar una [semántica estricta de ACID](https://en.wikipedia.org/wiki/ACID). Este método tiene sus ventajas en cuanto a garantizar un estado de datos coherente en la base de datos. Sin embargo, hay grandes inconvenientes con respecto a la simultaneidad, la latencia y la disponibilidad. Debido a estas restricciones básicas de la arquitectura, los volúmenes transaccionales altos pueden tener como resultado que los datos se deban particionar manualmente. La implementación manual de las particiones puede ser un proceso lento y tedioso.

En estos casos, las [bases de datos distribuidas](https://en.wikipedia.org/wiki/Distributed_database) pueden ofrecer una solución más escalable. Sin embargo, el mantenimiento puede seguir siendo costoso y lento. Es posible que los administradores tengan que realizar trabajo adicional para asegurarse de que la naturaleza distribuida del sistema sea transparente. También es posible que tengan que tener en cuenta la naturaleza "desconectada" de la base de datos.

[Azure Cosmos DB](./introduction.md) simplifica estos desafíos, ya que se implementa en todo el mundo y en todas las regiones de Azure. Los intervalos de partición se pueden subdividir dinámicamente para aumentar sin problemas el tamaño de la base de datos a la par de la aplicación, al tiempo que se mantiene una alta disponibilidad. El gobierno de recursos en la nube multiinquilino, específico y controlado de forma estricta facilita [sorprendentes garantías de latencia](./consistency-levels.md#consistency-levels-and-latency) y un rendimiento predecible. La creación de particiones está totalmente administrada, por lo que los administradores no tienen que escribir código ni administrar las particiones.

Si los volúmenes transaccionales alcanzan niveles extremos (por ejemplo, varios miles de transacciones por segundo) considere la posibilidad de usar una base de datos NoSQL distribuida. Plantéese la posibilidad de usar Azure Cosmos DB para obtener la máxima eficacia, facilitar el mantenimiento y reducir el costo total de propiedad.

:::image type="content" source="./media/relational-or-nosql/backend-scaled.png" alt-text="Back-end" border="false":::

## <a name="hierarchical-data"></a>Datos jerárquicos

Hay un número significativo de casos de uso en los que las transacciones de la base de datos pueden contener muchas relaciones entre elementos primarios y secundarios. Estas relaciones pueden aumentar significativamente con el tiempo y resultan difíciles de administrar. Durante los años ochenta, surgieron varias formas de [bases de datos jerárquicas](https://en.wikipedia.org/wiki/Hierarchical_database_model), pero no fueron populares debido a la ineficacia del almacenamiento. También perdieron terreno cuando el [modelo relacional de Ted Codd](https://en.wikipedia.org/wiki/Relational_model) se convirtió en el estándar de facto que usaban prácticamente todos los sistemas de administración de bases de datos convencionales.

Sin embargo, hoy en día, la popularidad de las bases de datos con estilo de documento ha crecido significativamente. Estas bases de datos podrían pensarse como una actualización del paradigma jerárquico de la base de datos, que ahora no se ve obstaculizada por el costo de almacenar los datos en el disco. Como resultado, el mantenimiento de muchas relaciones entidades complejas entre elementos primarios y secundarios en una base de datos relacional se puede considerar ahora un antipatrón en comparación con los métodos modernos orientados a documentos.

La aparición del [diseño orientado a objetos](https://en.wikipedia.org/wiki/Object-oriented_design) y la [adaptación de impedancias](https://en.wikipedia.org/wiki/Object-relational_impedance_mismatch) que surge al combinarlo con modelos relacionales, también resalta un antipatrón en las bases de datos relacionales para casos de uso concretos. Como resultado, pueden surgir costos de mantenimiento ocultos, aunque a menudo significativos. A pesar de que los [métodos de mapeo objeto-relacional](https://en.wikipedia.org/wiki/Object-relational_mapping) han evolucionado para mitigar parcialmente esta situación, las bases de datos orientadas a documentos se combinan mucho mejor con los métodos orientados a objetos. Con este método, los desarrolladores no se ven obligados a comprometerse con los controladores de mapeo objeto-relacional, ni con [motores de base de datos orientados a objetos](https://en.wikipedia.org/wiki/Object_database) personalizados que son específicos de un lenguaje. Si los datos contienen muchas relaciones de elementos primarios y secundarios y jerarquías internas, quizá quiera considerar la posibilidad de usar una base de datos de documentos NoSQL como la [API de SQL de Azure Cosmos DB](./introduction.md).

:::image type="content" source="./media/relational-or-nosql/order-orderdetails.jpg" alt-text="OrderDetails":::

## <a name="complex-networks-and-relationships"></a>Redes y relaciones complejas

Paradójicamente dado su nombre, las bases de datos relacionales presentan una solución poco óptima para el modelado de relaciones profundas y complejas. Esto se debe a que las relaciones entre entidades no existen realmente en una base de datos relacional. Deben calcularse en tiempo de ejecución, aunque las relaciones complejas requieren combinaciones cartesianas para permitir la asignación mediante consultas. Como resultado, las operaciones de cálculo se vuelven más costosas de forma exponencial a medida que aumentan las relaciones. En algunos casos, una base de datos relacional que intenta administrar tales entidades se vuelve inutilizable.

En la época en la que surgieron las bases de datos relacionales, también surgieron varias formas de bases de datos de "red", pero al igual que las bases de datos jerárquicas, estos sistemas han tenido dificultades para ganar popularidad. La adopción lenta se debía a la falta de casos de uso en la época, así como a la ineficacia del almacenamiento. En la actualidad, los motores de base de datos de grafos podrían considerarse como la reaparición del paradigma de la base de datos de red. La principal ventaja de estos sistemas es que las relaciones se almacenan como "ciudadanos de primera categoría" en la base de datos. Por lo tanto, las relaciones se pueden recorrer en intervalos de tiempo constantes, en lugar de que la complejidad temporal aumente con cada nueva combinación o producto cruzado.

Si va a mantener una red compleja de relaciones en la base de datos, debería plantearse la posibilidad de usar una base de datos de grafos como la [API de Gremlin de Azure Cosmos DB](./graph-introduction.md) para administrar los datos.

:::image type="content" source="./media/relational-or-nosql/graph.png" alt-text="Diagrama de base de datos que muestra varios empleados y departamentos conectados entre sí.":::

Azure Cosmos DB es un servicio de base de datos con varios modelos, que ofrece una proyección de API para todos los tipos de modelos NoSQL principales: familia de columnas, documentos, grafos y pares clave-valor. Las capas de la API de documentos de [Gremlin (grafo)](./gremlin-support.md) y SQL (Core) son completamente interoperables. Esto tiene ventajas para cambiar entre distintos modelos en el nivel de programación. Los almacenes de grafos se pueden consultar en términos de recorridos de red complejos, así como de transacciones modeladas como registros de documento en el mismo almacén.

## <a name="fluid-schema"></a>Esquema fluido

Otra característica concreta de las bases de datos relacionales es que los esquemas se deben definir durante el tiempo de diseño. Esto tiene ventajas en cuanto a la integridad referencial y la conformidad de los datos. Sin embargo, también puede resultar restrictivo a medida que crece la aplicación. Responder a los cambios en el esquema en modelos independientes de forma lógica que comparten la misma definición de base de datos o de tabla puede volverse una tarea compleja con el tiempo. Estos casos de uso a menudo se benefician de que el esquema se transfiera a la aplicación para administrarse por registro. Para ello, la base de datos debe ser independiente del esquema y permitir que los registros sean "autodescriptivos" en cuanto a los datos contenidos en ellos.

Si va a administrar datos cuyas estructuras cambian constantemente a velocidades altas, en especial si las transacciones pueden provenir de orígenes externos en los que es difícil lograr la conformidad en toda la base de datos, debería considerar un método más independiente del esquema con un servicio de base de datos NoSQL administrado, como Azure Cosmos DB.

## <a name="microservices"></a>Microservicios

El patrón de [microservicios](https://en.wikipedia.org/wiki/Microservices) ha crecido significativamente en los últimos años. Este patrón tiene sus orígenes en la [arquitectura orientada a servicios](https://en.wikipedia.org/wiki/Service-oriented_architecture). El estándar de facto para la transmisión de datos en estas arquitecturas de microservicios modernas es [JSON](https://en.wikipedia.org/wiki/JSON), que también es el medio de almacenamiento para la inmensa mayoría de las bases de datos NoSQL orientadas a documentos. Esto hace que los almacenes de documentos NoSQL sean opciones mucho mejores para la persistencia y la sincronización (mediante [patrones de abastecimiento de eventos](https://en.wikipedia.org/wiki/Event-driven_architecture)) en implementaciones de microservicios complejas. Las bases de datos relacionales más tradicionales pueden tener un mantenimiento mucho más complejo en estas arquitecturas. Esto se debe a que se necesita una mayor cantidad de transformaciones para los estados y la sincronización entre las API. En concreto, Azure Cosmos DB tiene una serie de características que lo convierten en una opción aún mejor para las arquitecturas de microservicios basadas en JSON que muchas bases de datos NoSQL:

* selección de tipos de datos JSON puros;
* un motor de JavaScript y una [API de consulta](sql/javascript-query-api.md) integradas en la base de datos;
* una [fuente de cambios](./change-feed.md) vanguardista a la que los clientes pueden suscribirse para recibir notificaciones sobre las modificaciones realizadas en un contenedor.

## <a name="some-challenges-with-nosql-databases"></a>Algunos desafíos de las bases de datos NoSQL

Aunque la implementación de bases de datos NoSQL presenta algunas claras ventajas, también existen algunos desafíos que se deben tener en cuenta. Es posible que estos no estén presentes en el mismo grado al trabajar con el modelo relacional:

* transacciones con varias relaciones que apuntan a la misma entidad.
* transacciones que necesitan una gran coherencia en todo el conjunto de datos.

En el primer desafío, la regla general de las bases de datos NoSQL suele ser la desnormalización que, como se mencionó anteriormente, genera lecturas más eficaces en un sistema distribuido. Sin embargo, hay algunos desafíos de diseño que entran en juego con este método. El siguiente es un ejemplo de un producto relacionado con una categoría y varias etiquetas:

:::image type="content" source="./media/relational-or-nosql/many-joins.png" alt-text="Combinaciones":::

Un método recomendado en una base de datos de documentos NoSQL sería desnormalizar el nombre de la categoría y los nombres de las etiquetas directamente en un "documento de producto". Sin embargo, para mantener sincronizadas las categorías, las etiquetas y los productos, las opciones de diseño que facilitan esta tarea han agregado complejidad al mantenimiento, ya que los datos se duplican en varios registros del producto, en lugar de ser una sencilla actualización de una relación de "uno a varios" y una combinación para recuperar los datos. 

A cambio, las lecturas son más eficaces en el registro desnormalizado y se vuelven cada vez más eficaces a medida que aumenta el número de entidades conectadas conceptualmente. Sin embargo, a medida que la eficacia de lectura aumenta con mayores números de entidades conectadas en un registro de desnormalización, también aumenta la dificultad de mantener sincronizadas las entidades. Una forma de solucionar este dilema es crear un [modelo de datos híbrido](./modeling-data.md#hybrid-data-models).

Aunque hay más flexibilidad disponible en las bases de datos NoSQL para solucionar estos dilemas, una mayor flexibilidad también puede ocasionar más decisiones de diseño. Consulte nuestro artículo sobre [cómo modelar y crear particiones de datos en Azure Cosmos DB con un ejemplo real](./how-to-model-partition-example.md), que incluye un método para mantener [los datos de usuario desnormalizados sincronizados](./how-to-model-partition-example.md#denormalizing-usernames) cuando los usuarios no solo se encuentran en distintas particiones, sino también en diferentes contenedores.

En relación con el alto nivel de coherencia, es poco habitual que sea necesaria en todo el conjunto de datos. Sin embargo, en los casos en los que se necesita, puede ser un desafío en las bases de datos distribuidas. Para garantizar una gran coherencia, se deben sincronizar los datos en todas las réplicas y regiones antes de permitir que los clientes los lean. Esto puede aumentar la latencia de las lecturas.

De nuevo, Azure Cosmos DB ofrece más flexibilidad que las bases de datos relacionales para los distintos inconvenientes que son pertinentes en este caso, aunque para las implementaciones a pequeña escala, este enfoque puede agregar más consideraciones de diseño. Consulte nuestro artículo sobre [Inconvenientes de la coherencia, disponibilidad y rendimiento](./consistency-levels.md) para obtener más información sobre este tema.

## <a name="next-steps"></a>Pasos siguientes

Sepa cómo administrar su cuenta de Azure Cosmos y aprenda otros conceptos:

* [Administración de la cuenta de Azure Cosmos](how-to-manage-database-account.md)
* [Distribución global](distribute-data-globally.md)
* [Niveles de coherencia](consistency-levels.md)
* [Uso de contenedores y elementos de Azure Cosmos](account-databases-containers-items.md)
* [Punto de conexión de servicio de red virtual para la cuenta de Azure Cosmos](how-to-configure-vnet-service-endpoint.md)
* [Firewall para direcciones IP para la cuenta de Azure Cosmos](how-to-configure-firewall.md)
* [Como agregar y quitar regiones de Azure en la cuenta de Azure Cosmos](how-to-manage-database-account.md).
* [Contratos de nivel de servicio para Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)