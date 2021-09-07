---
title: Metadatos y linaje de Erwin
description: En este artículo se describe la extracción de linaje de datos del origen de Erwin.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/11/2021
ms.openlocfilehash: d1821ca94d8f5329ca4c08f65438a3d0ba6e4d48
ms.sourcegitcommit: 6c6b8ba688a7cc699b68615c92adb550fbd0610f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121862805"
---
# <a name="how-to-get-lineage-from-erwin-into-azure-purview"></a>Extracción de linaje de Erwin en Azure Purview

En este artículo se detallan los aspectos del linaje de datos del origen de Erwin en Azure Purview. El requisito previo para ver el linaje de datos en Purview para Erwin es [examinar su Erwin.](../purview/register-scan-erwin-source.md) 

## <a name="lineage-of-erwin-artifacts-in-azure-purview"></a>Linaje de artefactos de Erwin en Azure Purview

Los usuarios pueden buscar artefactos de Erwin por el nombre, la descripción u otros detalles para ver los resultados pertinentes. En la pestaña de información general y propiedades de los recursos, se muestran los detalles básicos, como la descripción, las propiedades y otra información. En la pestaña de linaje, se muestran las relaciones de recursos entre tablas y vistas de Erwin. Por lo tanto, las vistas de Erwin tendrán información de linaje de las tablas. 

:::image type="content" source="./media/how-to-lineage-erwin/lineage.png" alt-text="Captura de pantalla que muestra cómo se representa el linaje para Erwin." lightbox="./media/how-to-lineage-erwin/lineage.png":::


## <a name="next-steps"></a>Pasos siguientes

- [Obtener información sobre el linaje de datos en Azure Purview](catalog-lineage-user-guide.md)
- [Vinculación de Azure Data Factory para insertar linaje automatizado](how-to-link-azure-data-factory.md)
