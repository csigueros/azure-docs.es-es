---
title: Lista de SKU de máquina virtual de puntos de conexión en línea administrados (versión preliminar)
titleSuffix: Azure Machine Learning
description: Listas con las SKU de máquina virtual que se pueden usar para los puntos de conexión en línea administrados (versión preliminar) de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: laobri
ms.author: seramasu
author: rsethur
ms.custom: devplatv2
ms.date: 05/10/2021
ms.openlocfilehash: 79711c803decdb2397701de7fd064639799e11aa
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122445379"
---
# <a name="managed-online-endpoints-sku-list-preview"></a>Lista de SKU de puntos de conexión en línea administrados (versión preliminar)

[!INCLUDE [preview disclaimer](../../includes/machine-learning-preview-generic-disclaimer.md)]

En esta tabla se muestran las SKU de máquina virtual que se admiten para los puntos de conexión en línea administrados (versión preliminar) de Azure Machine Learning.

* El atributo `instance_type` utilizado para la implementación debe especificarse con el formato "Standard_F4s_v2". En la tabla siguiente se enumeran los nombres de instancia, por ejemplo, F2s v2. Estos nombres deben colocarse en el formato especificado (`Standard_{name}`) para que las solicitudes de la CLI de Azure o de las plantillas de Azure Resource Manager creen y actualicen las implementaciones. 

* Para más información sobre los detalles de configuración, como CPU y RAM, consulte [Precios de Azure Machine Learning](https://azure.microsoft.com/pricing/details/machine-learning/).

| Size | Uso general | Optimizada para proceso |  GPU |
| --- | --- | --- | --- | --- | 
| V.Small | DS2 v2 | F2s v2 | NC4as_T4_v3 |
| Pequeña | DS3_v2 | F4s v2 | NC6s v2 <br/> NC6s v3 <br/> NC8as_T4_v3 |
| Media | DS4 v2 | F8s v2 | NC12s v2 <br/> NC12s v3 <br/> NC16as_T4_v3 |
| grande | DS5 v2 | F16s v2 | NC24s v2 <br/> NC24s v3 <br/> NC64as_T4_v3 |
| Mas grande| - | F32s v2 <br/> F48s v2 <br/> F64s v2 <br/> F72s v2 | - |


