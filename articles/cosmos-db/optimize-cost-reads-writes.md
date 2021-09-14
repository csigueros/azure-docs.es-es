---
title: Optimización del costo de las solicitudes en Azure Cosmos DB
description: En este artículo se explica cómo optimizar los costos al emitir solicitudes en Azure Cosmos DB.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/26/2021
ms.openlocfilehash: db6973d19fd8bd4fc4d36903b3205e6c8e01314d
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2021
ms.locfileid: "123029157"
---
# <a name="optimize-request-cost-in-azure-cosmos-db"></a>Optimización del costo de las solicitudes en Azure Cosmos DB
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

En este artículo se describe cómo se traducen las solicitudes de lectura y escritura en [unidades de solicitud](request-units.md) y cómo optimizar el costo de estas solicitudes. Las operaciones de lectura incluyen lecturas y consultas puntuales. Las operaciones de escritura incluyen la inserción, el reemplazo, la eliminación y la inserción de elementos.

Azure Cosmos DB ofrece un amplio conjunto de operaciones de base de datos que se realizan en los elementos de un contenedor. El costo asociado a cada una de estas operaciones variará en función de la CPU, la E/S y la memoria necesarias para completar la operación. En lugar de administrar y pensar sobre los recursos de hardware, puede pensar en una unidad de solicitud (RU) como una medida única para los recursos necesarios para realizar varias operaciones de la base de datos y dar servicio a una solicitud.

## <a name="measuring-the-ru-charge-of-a-request"></a>Medición del cargo de una unidad de solicitud

Es importante medir el cargo de RU de las solicitudes para comprender el costo real y evaluar también la eficacia de las optimizaciones. Puede obtener este costo con Azure Portal o por medio de la inspección de la respuesta enviada desde Azure Cosmos DB mediante uno de los SDK. Consulte [Búsqueda del cargo de unidad de solicitud en Azure Cosmos DB](find-request-unit-charge.md) para obtener instrucciones detalladas sobre cómo lograrlo.

## <a name="reading-data-point-reads-and-queries"></a>Lectura de datos: lecturas y consultas puntuales

Las operaciones de lectura en Azure Cosmos DB se suelen ordenar de la más rápida o eficiente a la más lenta o menos eficiente en cuanto a consumo de RU, de la siguiente manera:  

* Lecturas puntuales (búsqueda de clave y valor en un único id. de elemento y clave de partición).
* Consulta con una cláusula de filtro en una única clave de partición.
* Consulta sin una cláusula de filtro de igualdad o intervalo en cualquier propiedad.
* Consulta sin filtros.

### <a name="role-of-the-consistency-level"></a>Rol del nivel de coherencia

Cuando se usan los [niveles de coherencia](consistency-levels.md) **fuerte** u **obsolescencia limitada**, se duplica el costo de RU de cualquier operación de lectura (lectura o consulta puntual).

### <a name="point-reads"></a>Lecturas puntuales

El único factor que afecta al cargo de RU de una lectura puntual (además del nivel de coherencia usado) es el tamaño del elemento recuperado. En la tabla siguiente se muestra el costo en RU de lecturas puntuales para los elementos con un tamaño de 1 KB y de 100 KB.

| **Tamaño del elemento** | **Costo de una lectura puntual** |
| --- | --- |
| 1 KB | 1 RU |
| 100 KB | 10 RU |

Dado que las lecturas puntuales (búsquedas de clave/valor en el identificador de elemento) son el tipo de lectura más eficaz, debe asegurarse de que el identificador de elemento tenga un valor significativo para que pueda capturar los elementos con una lectura puntual (en lugar de una consulta) cuando sea posible.

### <a name="queries"></a>Consultas

Las unidades de solicitud para las consultas dependen de distintos factores. Por ejemplo, detalles sobre el número de elementos de Azure Cosmos cargado y devueltos, el número de búsquedas en el índice, el tiempo de compilación de consulta, etcétera. Azure Cosmos DB garantiza que la misma consulta de los mismos datos consumirá siempre el mismo número de unidades de solicitud, aunque se repitan las ejecuciones. El perfil de la consulta con métricas de ejecución de consultas ofrece una buena idea de cómo se invierten las unidades de solicitud.  

En algunos casos podría ver una secuencia de 200 y 429 respuestas y unidades de solicitud variables en la ejecución paginada de las consultas; esto se debe a que estas se ejecutan lo más rápido posible en función de las RU disponibles. Quizá vea una ejecución de consulta dividida en varias páginas o en recorrido de ida y vuelta entre el cliente y servidor. Por ejemplo, 10 000 elementos pueden devolverse como varias páginas y que cada una se cargue en función del cálculo realizado para esa página. Al sumar estas páginas debe obtener el mismo número de unidades de solicitud que obtendría para la consulta completa.

#### <a name="metrics-for-troubleshooting-queries"></a>Métricas para la solución de problemas de consultas

El rendimiento y la capacidad de proceso consumida por las consultas (incluidas las funciones definidas por el usuario) dependen principalmente del cuerpo de la función. La manera más sencilla de averiguar cuánto tiempo se dedica a la ejecución de consultas en la función definida por el usuario y el número de unidades de solicitud consumidas es habilitar las métricas de consulta. Si usa el SDK de .NET, estas son las métricas de consulta de ejemplo que este devuelve:

```bash
Retrieved Document Count                 :               1              
Retrieved Document Size                  :           9,963 bytes        
Output Document Count                    :               1              
Output Document Size                     :          10,012 bytes        
Index Utilization                        :          100.00 %            
Total Query Execution Time               :            0.48 milliseconds 
  Query Preparation Times 
    Query Compilation Time               :            0.07 milliseconds 
    Logical Plan Build Time              :            0.03 milliseconds 
    Physical Plan Build Time             :            0.05 milliseconds 
    Query Optimization Time              :            0.00 milliseconds 
  Index Lookup Time                      :            0.06 milliseconds 
  Document Load Time                     :            0.03 milliseconds 
  Runtime Execution Times 
    Query Engine Execution Time          :            0.03 milliseconds 
    System Function Execution Time       :            0.00 milliseconds 
    User-defined Function Execution Time :            0.00 milliseconds 
  Document Write Time                    :            0.00 milliseconds 
  Client Side Metrics 
    Retry Count                          :               1              
    Request Charge                       :            3.19 RUs  
```

#### <a name="best-practices-to-cost-optimize-queries"></a>Procedimientos recomendados para optimizar el costo de las consultas 

Al optimizar el costo de las consultas, considere los siguientes procedimientos recomendados:

* **Colocación de varios tipos de entidad**

   Intente colocar varios tipos de entidad dentro de un único contenedor o en el menor número de ellos posible. Este método produce beneficios no solo en lo relativo a los precios, sino también respecto a las transacciones y la ejecución de las consultas. Las consultas se limitan a un único contenedor y las transacciones atómicas con varios registros a través de procedimientos almacenados o desencadenadores, a una clave de partición de un único contenedor. Colocar las entidades en el mismo contenedor puede reducir el número de recorridos de ida y vuelta en la red para resolver las relaciones entre registros. Por lo tanto, aumenta el rendimiento de un extremo a otro, permite las transacciones atómicas con varios registros para un mayor conjunto de datos y, como resultado, reduce los costos. Si la colocación de varios tipos de entidad en un único contenedor o en un número reducido de estos resulta difícil en su caso, normalmente porque va a migrar una aplicación existente y no quiere realizar cambios de código, debe considerar el rendimiento del aprovisionamiento a nivel de la base de datos.  

* **Medición y optimización del uso menor de unidades de solicitud por segundo**

   La complejidad de una consulta afecta a la cantidad de unidades de solicitud (RU) consumidas para una operación. El número de predicados, la naturaleza de estos, el número de UDF y el tamaño del conjunto de datos de origen son factores que influyen en el costo de las operaciones de consulta. 

Azure Cosmos DB proporciona un rendimiento predecible en cuanto a rendimiento y latencia con un modelo de rendimiento aprovisionado. El rendimiento aprovisionado se representa en términos de [unidades de solicitud](request-units.md) por segundo o RU/s. Una unidad de solicitud (RU) es una abstracción lógica a través de los recursos de proceso, como CPU, memoria, E/S, etc., que son necesarios para realizar una solicitud. El rendimiento aprovisionado (RU) se reserva y está dedicado a su contenedor o base de datos para ofrecer un rendimiento y latencia predecibles. El rendimiento aprovisionado permite a Azure Cosmos DB ofrecer un rendimiento coherente y predecible, baja latencia garantizada y alta disponibilidad a cualquier escala. Las unidades de solicitud representan la moneda normalizada que simplifica el razonamiento sobre cuántos recursos necesita una aplicación.

El cargo de la solicitud devuelto en el encabezado de solicitud indica el costo de una consulta determinada. Por ejemplo, si una consulta devuelve 1000 elementos de 1 KB, el costo de la operación es 1000. Por lo tanto, al cabo de un segundo, el servidor atenderá solo dos de estas solicitudes antes de limitar la velocidad de las solicitudes posteriores. Para más información, consulte el artículo sobre las [unidades de solicitud](request-units.md) y la calculadora de unidades de solicitud.

## <a name="writing-data"></a>Escritura de datos

El costo de RU de escribir un elemento depende de:

- El tamaño del elemento.
- El número de propiedades que se incluyen en la [directiva de indexación](index-policy.md) y que es necesario indexar.

La inserción de un elemento de 1 KB sin indexación cuesta en torno a 5,5 RU. El reemplazo un elemento cuesta el doble que la inserción del mismo elemento.

### <a name="optimizing-writes"></a>Optimización de escrituras

La mejor manera de optimizar el costo de RU de las operaciones de escritura es adoptar un tamaño adecuado para los elementos y el número de propiedades que se indexan.

- Almacenar elementos muy grandes en Azure Cosmos DB da como resultado altos cargos de RU y se puede considerar un antipatrón. En concreto, no almacene contenido binario o fragmentos grandes de texto que no necesite consultar. Un procedimiento recomendado es poner este tipo de datos en [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md) y almacenar una referencia (o vínculo) al blob en el elemento que se escribe en Azure Cosmos DB.
- La optimización de la directiva de indexación para indizar solo las propiedades por las que se filtran las consultas puede suponer una gran diferencia en la RU consumida por las operaciones de escritura. Al crear un nuevo contenedor, la directiva de indexación predeterminada indexa cada una de las propiedades que se encuentran en los elementos. Aunque se trata de un buen valor predeterminado para las actividades de desarrollo, se recomienda volver a evaluar y [personalizar la directiva de indexación](how-to-manage-indexing-policy.md) cuando pase a producción o cuando la carga de trabajo empiece a recibir un tráfico significativo.

Al realizar la ingesta masiva de datos, también se recomienda usar la [biblioteca BulkExecutor de Azure Cosmos DB](bulk-executor-overview.md), ya que está diseñada para optimizar el consumo de RU de tales operaciones. Opcionalmente, también puede usar [Azure Data Factory](../data-factory/introduction.md), que se basa en esa misma biblioteca.

## <a name="next-steps"></a>Pasos siguientes

A continuación, puede seguir obteniendo más información sobre la optimización de costos en Azure Cosmos DB con los siguientes artículos:

* Obtenga más información sobre la [optimización para desarrollo y pruebas](optimize-dev-test.md).
* Obtenga más información sobre [la factura de Azure Cosmos DB](understand-your-bill.md).
* Obtenga más información sobre la [optimización del costo de la capacidad del rendimiento](optimize-cost-throughput.md).
* Obtenga más información sobre la [optimización del costo del almacenamiento](optimize-cost-storage.md).
* Obtenga más información sobre la [optimización del costo de las cuentas de Azure Cosmos de varias regiones](optimize-cost-regions.md).
* Sobre la [capacidad reservada de Azure Cosmos DB](cosmos-db-reserved-capacity.md)
* ¿Intenta planear la capacidad de una migración a Azure Cosmos DB? Para ello, puede usar información sobre el clúster de bases de datos existente.
    * Si lo único que sabe es el número de núcleos virtuales y servidores del clúster de bases de datos existente, lea sobre el [cálculo de unidades de solicitud mediante núcleos o CPU virtuales](convert-vcore-to-request-unit.md). 
    * Si conoce las tasas de solicitudes típicas de la carga de trabajo de la base de datos actual, obtenga información sobre el [cálculo de unidades de solicitud mediante la herramienta de planeamiento de capacidad de Azure Cosmos DB](estimate-ru-with-capacity-planner.md).
