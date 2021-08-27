---
title: Conexión a GitHub
titleSuffix: Azure Data Factory & Azure Synapse
description: Uso de GitHub para especificar referencias de entidad de Common Data Model
author: linda33wj
ms.service: data-factory
ms.subservice: data-movement
ms.custom: synapse
ms.topic: conceptual
ms.date: 06/03/2020
ms.author: jingwang
ms.openlocfilehash: 659b0883e1b4085c9a8ad61f3512909a9a738a66
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122637982"
---
# <a name="use-github-to-read-common-data-model-entity-references"></a>Uso de GitHub para leer referencias de entidad de Common Data Model

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

El conector de GitHub en Azure Data Factory sirve únicamente para recibir el esquema de referencia de entidades para el formato de [Common Data Model](format-common-data-model.md) en el flujo de datos de asignación.

## <a name="linked-service-properties"></a>Propiedades del servicio vinculado

Las siguientes propiedades son compatibles con el servicio vinculado de GitHub.

| Propiedad | Descripción | Obligatorio |
|:--- |:--- |:--- |
| type | La propiedad type debe establecerse en **GitHub**. | sí
| userName | Nombre de usuario de GitHub | sí |
| password | Contraseña de GitHub | sí |

## <a name="next-steps"></a>Pasos siguientes

Cree un [conjunto de datos de origen](data-flow-source.md) en el flujo de datos de asignación.