---
title: 'Adición de filas: referencia de componente'
titleSuffix: Azure Machine Learning
description: Aprenda a usar el componente Adición de filas del diseñador de Azure Machine Learning para concatenar dos conjuntos de datos.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 02/22/2020
ms.openlocfilehash: 856821f810397f6b14b383281d2e566b9c6e16d2
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566075"
---
# <a name="add-rows-component"></a>Componente Adición de filas

En este artículo se describe un componente del diseñador de Azure Machine Learning.

Use este componente para concatenar dos conjuntos de datos. En la concatenación, las filas del segundo conjunto de datos se agregan al final del primer conjunto de datos.  
  
La concatenación de filas es útil en escenarios como los siguientes:  
  
+ Se ha generado una serie de estadísticas de evaluación y desea combinarlas en una sola tabla para crear informes más fácilmente.  
  
+ Ha estado trabajando con diferentes conjuntos de datos y desea combinarlos para crear un conjunto de datos final.  

## <a name="how-to-use-add-rows"></a>Procedimiento para usar Adición de filas  

Para concatenar las filas de dos conjuntos de datos, las filas deben tener exactamente el mismo esquema. Esto significa, el mismo número de columnas y el mismo tipo de datos en las columnas.

1.  Arrastre el componente **Adición de filas** a la canalización. Puede encontrarlo en **Transformación de datos**.

2. Conecte los conjuntos de datos a los dos puertos de entrada. El conjunto de datos que desea anexar debe estar conectado al segundo puerto (derecho). 
  
3.  Envíe la canalización. El número de filas del conjunto de datos de salida debe ser igual a la suma de las filas de ambos conjuntos de datos de entrada.

    Si agrega el mismo conjunto de datos a ambas entradas del componente **Adición de filas**, se duplica el conjunto de datos. 

## <a name="next-steps"></a>Pasos siguientes

Vea el [conjunto de componentes disponibles](component-reference.md) para Azure Machine Learning. 