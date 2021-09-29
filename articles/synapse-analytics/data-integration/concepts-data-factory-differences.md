---
title: Diferencias con respecto a Azure Data Factory
description: Aprenda cómo las funcionalidades de integración de datos de Azure Synapse Analytics difieren de las de Azure Data Factory
services: synapse-analytics
author: kromerm
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.date: 09/07/2021
ms.author: makromer
ms.reviewer: jrasnick
ms.openlocfilehash: d8320c6356f310584d24c51a384446dfdcb22afa
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128608173"
---
# <a name="data-integration-in-azure-synapse-analytics-versus-azure-data-factory"></a>Integración de datos en Azure Synapse Analytics frente a Azure Data Factory

En Azure Synapse Analytics, las funcionalidades de integración de datos, como los flujos de datos y las canalizaciones de Synapse, se basan en las de Azure Data Factory. Para más información, vea [¿Qué es Azure Data Factory?](../../data-factory/introduction.md).


## <a name="available-features-in-adf--azure-synapse-analytics"></a>Características disponibles en ADF y Azure Synapse Analytics

Consulte la tabla siguiente para ver la disponibilidad de las características:

| Category                 | Característica    |  Azure Data Factory  | Azure Synapse Analytics |
| ------------------------ | ---------- | :------------------: | :---------------------: |
| **Integration Runtime**  | Uso de SSIS y SSIS Integration Runtime | ✓ | ✗ |
|                          | Compatibilidad con Integration Runtime entre regiones (flujos de datos) | ✓ | ✗ |
|                          | Uso compartido de Integration Runtime | ✓<br><small>*Puede compartirse entre diferentes factorías de datos* | ✗ |
| **Actividades de canalizaciones** | Actividad del paquete de SSIS | ✓ | ✗ |
|                          | Compatibilidad con la actividad de Power Query | ✓ | ✗ |
| **Galería de plantillas y Centro de conocimientos** | Plantillas de solución | ✓<br><small>*Galería de plantillas de Azure Data Factory* | ✓<br><small>*Centro de conocimientos del área de trabajo de Synapse* |
| **Integración del repositorio GIT** | Integración de GIT | ✓ | ✓ |
| **Supervisión**           | Supervisión de trabajos de Spark para el flujo de datos | ✗ | ✓<br><small>*Aproveche los grupos de Spark en Synapse* |

## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con la integración de datos en el área de trabajo de Synapse, aprenda cómo [ingerir datos en una cuenta de Azure Data Lake Storage Gen2](data-integration-data-lake.md).
