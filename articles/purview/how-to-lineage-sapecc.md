---
title: Metadatos y linaje de SAP ECC
description: En este artículo se describe la extracción de linaje de datos del origen de SAP ECC.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 4c1e18f3f7484aec5bd99efbc4f9dea5b0e84ef8
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014576"
---
# <a name="how-to-get-lineage-from-sap-ecc-into-azure-purview"></a>Extracción de linaje de SAP ECC a Azure Purview

En este artículo se detallan los aspectos del linaje de datos del origen de SAP ECC en Azure Purview. El requisito previo para ver el linaje de datos en Purview para SAP ECC es [examinar su instancia de SAP ECC.](../purview/register-scan-sapecc-source.md) 

## <a name="lineage-of-sap-ecc-artifacts-in-azure-purview"></a>Linaje de artefactos de SAP ECC en Azure Purview

Los usuarios pueden buscar artefactos de SAP ECC por el nombre, la descripción u otros detalles para ver los resultados pertinentes. En la pestaña de información general y propiedades de los recursos, se muestran los detalles básicos, como la descripción, las propiedades y otra información. En la pestaña de linaje, se muestran las relaciones de recursos entre tablas y vistas de SAP ECC. Por lo tanto, las vistas de SAP ECC tendrán información de linaje de las tablas. 

El linaje derivado también está disponible en el nivel de columnas.

:::image type="content" source="./media/how-to-lineage-sapecc/lineage.png" alt-text="Captura de pantalla que muestra cómo se representa el linaje para SAP ECC." lightbox="./media/how-to-lineage-sapecc/lineage.png":::


## <a name="next-steps"></a>Pasos siguientes

- [Obtener información sobre el linaje de datos en Azure Purview](catalog-lineage-user-guide.md)
- Si va a mover datos a Azure desde SAP ECC mediante ADF, podemos realizar un seguimiento del linaje como parte del tiempo de ejecución de movimiento de datos: [vincular Azure Data Factory para insertar linaje automatizado](how-to-link-azure-data-factory.md).
