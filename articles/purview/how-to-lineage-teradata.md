---
title: Metadatos y linaje de Teradata
description: En este artículo se describe la extracción de linaje de datos del origen de Teradata.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 08/12/2021
ms.openlocfilehash: 39965089cdd838e12ac324737e977dbeee17b40b
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122014941"
---
# <a name="how-to-get-lineage-from-teradata-into-azure-purview"></a>Extracción de linaje de Teradata a Azure Purview

En este artículo se detallan los aspectos del linaje de datos del origen de Teradata en Azure Purview. El requisito previo para ver el linaje de datos en Purview para Teradata es [examinar su Teradata.](../purview/register-scan-teradata-source.md) 

## <a name="lineage-of-teradata-artifacts-in-azure-purview"></a>Linaje de artefactos de Teradata en Azure Purview

Los usuarios pueden buscar artefactos de Teradata por el nombre, la descripción u otros detalles para ver los resultados pertinentes. En la pestaña de información general y propiedades de los recursos, se muestran los detalles básicos, como la descripción, las propiedades y otra información. En la pestaña Linaje, se muestran las relaciones de recursos entre tablas y procedimientos almacenados de Teradata, y entre tablas y vistas de Teradata. 

Por lo tanto, se admite el linaje para las transformaciones de datos que se están produciendo en procedimientos almacenados y transformaciones en vistas de tablas de Teradata. El linaje derivado también está disponible en el nivel de columnas.

:::image type="content" source="./media/how-to-lineage-teradata/lineage.png" alt-text="Captura de pantalla que muestra cómo se representa el linaje para Teradata." lightbox="./media/how-to-lineage-teradata/lineage.png":::

En la captura de pantalla anterior, **customerView** es una vista de Teradata creada a partir de una tabla de Teradata denominada **test_customer**. Además, **Return_DataSet** es un procedimiento almacenado que usa la tabla de Teradata **test_customer**.

## <a name="next-steps"></a>Pasos siguientes

- [Obtener información sobre el linaje de datos en Azure Purview](catalog-lineage-user-guide.md)
- Si va a mover datos a Azure desde Teradata mediante ADF, podemos realizar un seguimiento del linaje como parte del tiempo de ejecución de movimiento de datos: [vincular Azure Data Factory para insertar linaje automatizado](how-to-link-azure-data-factory.md).
