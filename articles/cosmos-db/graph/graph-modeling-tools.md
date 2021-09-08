---
title: Herramientas de modelado de datos de terceros para datos de grafos de Azure Cosmos DB
description: En este artículo, se describen varias herramientas para diseñar el modelo de datos de grafos.
author: mansha
ms.author: mansha
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/25/2021
ms.reviewer: sngun
ms.openlocfilehash: eacebb55cbf4d6254d6688a95dba8ae638838cf3
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780018"
---
# <a name="third-party-data-modeling-tools-for-azure-cosmos-db-graph-data"></a>Herramientas de modelado de datos de terceros para datos de grafos de Azure Cosmos DB

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

Es importante diseñar el modelo de datos y, además, es importante mantenerlo. A continuación, se muestra un conjunto de herramientas de diseño visual de terceros que ayudan a diseñar y mantener el modelo de datos de grafos.

> [!IMPORTANT] 
> Las soluciones mencionadas en este artículo tienen solo carácter informativo; la propiedad es del propietario de cada solución individual. Se recomienda a los usuarios que realicen una evaluación exhaustiva y, después, seleccionen la opción más adecuada.

## <a name="hackolade"></a>Hackolade

Hackolade es una herramienta de modelado de datos y diseño de esquemas para bases de datos NoSQL. Tiene un estudio de modelado de datos, que ayuda en la administración de esquemas para datos en reposo y datos en movimiento.

### <a name="how-it-works"></a>Funcionamiento
Esta herramienta proporciona el modelado de datos de vértices y bordes, y sus propiedades respectivas.  Admite varios casos de uso, algunos de ellos son:
-   Comience desde una página en blanco y piense en las distintas opciones para crear gráficamente el modelo de Gremlin de Cosmos DB.  A continuación, avance el diseño del modelo a la instancia de Azure para evaluar el resultado y continuar con la evolución.  Todo esto sin escribir una sola línea de código.
-   Cree un diseño inverso de un grafo existente en Azure para comprender claramente su estructura, de modo que también pueda consultar el gráfico de forma eficaz.  A continuación, enriquezca el modelo de datos con descripciones, metadatos y restricciones para generar documentación. Admite formato HTML, Markdown o PDF, y alimenta sistemas de diccionario o de gobernanza de datos corporativos.
-   Realice la migración de una base de datos relacional a NoSQL mediante la desnormalización de las estructuras de datos.
-   Integración con una canalización de CI/CD mediante la interfaz de la línea de comandos
-   Colaboración y control de versiones mediante Git
-   Y mucho más...

### <a name="sample"></a>Muestra

La animación de la figura 2 proporciona una demostración de ingeniería inversa, extracción de entidades del RDBMS y, a continuación, Hackolade detectará relaciones a partir de relaciones de clave externa y, después, modificaciones.

DDL de ejemplo para un origen como SQL Server disponible [aquí](https://github.com/Azure-Samples/northwind-ddl-sample/nw.sql)   


:::image type="content" source="./media/graph-modeling-tools/hackolade-screenshot.jpg" alt-text="Diagrama del grafo":::
**Figura-1:** diagrama del grafo (se ha extraído el modelo de datos del grafo)

Después de modificar el modelo de datos, la herramienta puede generar el script de Gremlin, que puede incluir un script de índice de Cosmos DB personalizado para asegurarse de que se creen los índices óptimos. Consulte la figura 2 para obtener el flujo completo.

En la imagen siguiente, se muestra la ingeniería inversa de RDBMS y Hackolade en acción: :::image type="content" source="./media/graph-modeling-tools/cosmos-db-gremlin-hackolade.gif" alt-text="Hackolade en acción":::

**Figura-2:** Hackolade en acción (se muestra la conversión del modelo de datos de SQL a Gremlin)
### <a name="useful-links"></a>Vínculos útiles 
-   [Descarga de una evaluación gratuita de 14 días](https://hackolade.com/download.html)
-   [Programación de una demostración](https://c.x.ai/pdesmarets)
-  [Obtenga más modelos de datos](https://hackolade.com/samplemodels.html#cosmosdb).
-  [Documentación de Hackolade](https://hackolade.com/help/CosmosDBGremlin.html)

## <a name="next-steps"></a>Pasos siguientes
- [Visualización de los datos](/graph-visualization)
