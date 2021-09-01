---
title: Metadatos y linaje de SAP S/4HANA
description: En este artículo se describe la extracción de linaje de datos del origen de SAP S/4HANA.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 52ae0f629bc863f604133e3c0f0e3c6256556a89
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014950"
---
# <a name="how-to-get-lineage-from-sap-s4hana-into-azure-purview"></a>Extracción de linaje de SAP S/4HANA a Azure Purview

En este artículo se detallan los aspectos del linaje de datos del origen de SAP S/4HANA en Azure Purview. El requisito previo para ver el linaje de datos en Purview para SAP S/4HANA es [examinar su instancia de SAP S/4HANA.](../purview/register-scan-saps4hana-source.md) 

## <a name="lineage-of-sap-s4hana-artifacts-in-azure-purview"></a>Linaje de artefactos de SAP S/4HANA en Azure Purview

Los usuarios pueden buscar artefactos de SAP S/4HANA por el nombre, la descripción u otros detalles para ver los resultados pertinentes. En la pestaña de información general y propiedades de los recursos, se muestran los detalles básicos, como la descripción, las propiedades y otra información. En la pestaña Linaje, se muestran las relaciones de recursos entre las tablas y vistas de SAP S/4HANA. Por lo tanto, las vistas de SAP S/4HANA tendrán información de linaje de las tablas. 

El linaje derivado también está disponible en el nivel de columnas.

## <a name="next-steps"></a>Pasos siguientes

- [Obtener información sobre el linaje de datos en Azure Purview](catalog-lineage-user-guide.md)
- Si va a mover datos a Azure desde SAP S/4HANA mediante ADF, podemos realizar un seguimiento del linaje como parte del tiempo de ejecución de movimiento de datos: [vincular Azure Data Factory para insertar linaje automatizado](how-to-link-azure-data-factory.md).
