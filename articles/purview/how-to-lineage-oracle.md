---
title: Metadatos y linaje de Oracle
description: En este artículo se describe la extracción de linaje de datos del origen de Oracle.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: cf415250868c74aa95e22b12d8980987f0c9dc48
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862835"
---
# <a name="how-to-get-lineage-from-oracle-into-azure-purview"></a>Obtención de linaje de Oracle a Azure Purview

En este artículo se detallan los aspectos del linaje de datos del origen de Oracle en Azure Purview. El requisito previo para ver el linaje de datos en Purview para Oracle es [examinar su Oracle.](../purview/register-scan-oracle-source.md) 

## <a name="lineage-of-oracle-artifacts-in-azure-purview"></a>Linaje de artefactos de Oracle en Azure Purview

Los usuarios pueden buscar artefactos de Oracle por el nombre, la descripción u otros detalles para ver los resultados pertinentes. En la pestaña de información general y propiedades de los recursos se muestran los detalles básicos, como la descripción, la clasificación y otra información. En la pestaña linaje, se muestran las relaciones de recursos entre tablas de Oracle y procedimientos almacenados, vistas y funciones. 

Por lo tanto, hoy en día las vistas de Oracle tendrán información de linaje de las tablas, mientras que las funciones y los procedimientos almacenados producirán linaje entre tabla y vista con parameter_dataset y stored_procedure_result_set. El linaje derivado también está disponible en el nivel de columnas.

:::image type="content" source="./media/how-to-lineage-oracle/lineage.png" alt-text="Captura de pantalla que muestra cómo se representa el linaje para Oracle." lightbox="./media/how-to-lineage-oracle/lineage.png":::


## <a name="next-steps"></a>Pasos siguientes

- [Obtener información sobre el linaje de datos en Azure Purview](catalog-lineage-user-guide.md)
- [Vinculación de Azure Data Factory para insertar linaje automatizado](how-to-link-azure-data-factory.md)
