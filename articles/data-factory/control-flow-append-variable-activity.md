---
title: Anexar actividad de variable
titleSuffix: Azure Data Factory & Azure Synapse
description: Obtenga información sobre cómo configurar la actividad de anexión de variables para agregar un valor a una variable de matriz existente definida en una canalización de Data Factory o Synapse Analytics.
ms.service: data-factory
ms.subservice: orchestration
ms.custom: synapse
ms.topic: conceptual
author: chez-charlie
ms.author: chez
ms.reviewer: jburchel
ms.date: 09/09/2021
ms.openlocfilehash: 9d2a081535f571b139a5cb15cdb85b37e2c3af97
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124811713"
---
# <a name="append-variable-activity-in-azure-data-factory-and-synapse-analytics"></a>Actividad de anexión de variables de Azure Data Factory y Synapse Analytics
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]
Use la actividad de anexión de variables para agregar un valor a una variable de matriz existente definida en una canalización de Data Factory o Synapse Analytics.

## <a name="type-properties"></a>Propiedades de tipo

Propiedad | Descripción | Obligatorio
-------- | ----------- | --------
name | Nombre de la actividad en la canalización | Sí
description | Texto que describe para qué se usa la actividad | no
type | Tipo de actividad es AppendVariable | sí
value | Cadena literal o valor del objeto de expresión usado para anexar a la variable especificada | sí
variableName | Nombre de la variable que se va a modificar mediante la actividad; la variable debe ser de tipo 'Array' | sí

## <a name="next-steps"></a>Pasos siguientes
Obtenga información sobre una actividad de flujo de control relacionada: 

- [Establecimiento de la actividad de variable](control-flow-set-variable-activity.md)
