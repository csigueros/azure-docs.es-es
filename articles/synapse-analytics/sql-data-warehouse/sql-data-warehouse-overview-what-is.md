---
title: ¿Qué es un grupo de SQL dedicado (anteriormente SQL DW)?
description: Un grupo de SQL dedicado (anteriormente SQL DW) en Azure Synapse Analytics es la funcionalidad de almacenamiento de datos empresariales de Azure Synapse Analytics.
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: sql-dw
ms.date: 01/22/2021
ms.author: jrasnick
ms.reviewer: igorstan
ms.openlocfilehash: 4323f41cbc7440c79b2842bb3f128705a22d1dd4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123435373"
---
# <a name="what-is-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>¿Qué es un grupo de SQL dedicado (anteriormente SQL DW) en Azure Synapse Analytics?

Azure Synapse Analytics es un servicio de análisis que engloba el almacenamiento de datos empresariales y el análisis de macrodatos. Un grupo de SQL dedicado (anteriormente SQL DW) hace referencia a las características de almacenamiento de datos empresariales que están disponibles en Azure Synapse Analytics.



![Un grupo de SQL dedicado (anteriormente SQL DW) en relación con Azure Synapse](./media/sql-data-warehouse-overview-what-is/dedicated-sql-pool.png)



Un grupo de SQL dedicado (anteriormente SQL DW) representa una colección de recursos de análisis que se aprovisionan al usar Synapse SQL. El tamaño de un grupo de SQL dedicado (anteriormente SQL DW) se determina mediante las unidades de almacenamiento de datos (DWU).

Una vez que se crea un grupo de SQL dedicado, puede importar macrodatos con consultas T-SQL de [PolyBase](/sql/relational-databases/polybase/polybase-guide?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) simples y, después, usar la potencia del motor de consultas distribuidas para realizar un análisis de alto rendimiento. Al realizar la integración y el análisis de los datos, el grupo de SQL dedicado (anteriormente SQL DW) pasará a ser la versión única de certeza con la que puede contar su empresa para obtener información.

> [!NOTE]
>Explore la [documentación de Azure Synapse Analytics](../overview-what-is.md).
> 

## <a name="key-component-of-a-big-data-solution"></a>Componente clave de una solución de macrodatos

El almacenamiento de datos un componente clave de una solución de macrodatos de un extremo a otro en la nube.

![Solución de almacenamiento de datos](./media/sql-data-warehouse-overview-what-is/data-warehouse-solution.png)

En una solución de datos en la nube, los datos provenientes de varios orígenes se ingieren en almacenes de macrodatos. Una vez que están en un almacén de macrodatos, Hadoop, y Spark y los algoritmos de aprendizaje automático preparan y entrenan los datos. Cuando los datos están listos para el análisis complejo, el grupo de SQL dedicado usa PolyBase para consultar los almacenes de macrodatos. PolyBase usa las consultas T-SQL estándar para llevar los datos a tablas del grupo de SQL dedicado (anteriormente SQL DW).

El grupo de SQL dedicado (anteriormente SQL DW) almacena los datos en tablas relacionales con almacenamiento en columnas. Este formato reduce considerablemente los costos de almacenamiento de datos y mejora el rendimiento de las consultas. Una vez que los datos están almacenados, se pueden realizar análisis a gran escala. En comparación con los sistemas de bases de datos tradicionales, las consultas de análisis finalizan en segundos, en lugar de minutos, o en horas, en lugar de días.

Los resultados del análisis pueden ir a aplicaciones o bases de datos de informes mundiales. En ellas, los analistas empresariales pueden obtener información útil para tomar decisiones empresariales bien informadas.

## <a name="next-steps"></a>Pasos siguientes

- Exploración de la [arquitectura de Azure Synapse](massively-parallel-processing-mpp-architecture.md)
- [Creación de un grupo de SQL dedicado](create-data-warehouse-portal.md) de forma rápida
- [Cargue datos de ejemplo](./load-data-from-azure-blob-storage-using-copy.md).
- Explore [vídeos](https://azure.microsoft.com/documentation/videos/index/?services=sql-data-warehouse).

O bien, examine algunos de estos otros recursos de Azure Synapse.

- Busque información en [Blogs](https://azure.microsoft.com/blog/tag/azure-sql-data-warehouse/).
- Envíe una [solicitud de característica](https://feedback.azure.com/forums/307516-sql-data-warehouse).
- [Creación de una incidencia de soporte técnico](sql-data-warehouse-get-started-create-support-ticket.md)
- Buscar en la [página de preguntas y respuestas de Microsoft](/answers/topics/azure-synapse-analytics.html)
- Busque en el [foro Stack Overflow](https://stackoverflow.com/questions/tagged/azure-sqldw).