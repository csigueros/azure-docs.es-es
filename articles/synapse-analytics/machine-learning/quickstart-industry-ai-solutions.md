---
title: Soluciones de inteligencia artificial del sector
description: Soluciones de inteligencia artificial del sector de Azure Synapse Analytics
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: overview
ms.reviewer: garye
ms.date: 11/02/2021
author: nelgson
ms.author: negust
ms.custom: ignite-fall-2021
ms.openlocfilehash: 0a4476b1324938acd2c753368f26bc7aafbed62b
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091216"
---
# <a name="industry-ai-solutions"></a>Soluciones de inteligencia artificial del sector

Cada sector tiene problemas comunes específicos que resolver. En este artículo se tratan las soluciones del sector disponibles en las áreas de trabajo de Azure Synapse para empezar a solucionar rápidamente problemas comunes. En este momento, la solución de inteligencia artificial que se ofrece es una solución comercial para la recomendación de productos. A continuación, puede encontrar más detalles sobre cómo los distribuidores pueden usar esta solución para empezar a trabajar.

## <a name="target-user"></a>Usuario de destino

La solución de recomendación está dirigida a científicos de datos existentes y emergentes que están familiarizados con el código y con los conceptos de aprendizaje automático. Esta solución está pensada para resolver problemas específicos del dominio minorista y así acelerar la productividad de estos usuarios.

## <a name="retail-product-recommendation-solution"></a>Solución de recomendaciones de productos minoristas

La solución de **Retail - Product recommendations** (Venta al por menor: recomendaciones de productos) proporciona un motor de recomendaciones sólido y escalable para el desarrollo integrado en Synapse. Con el uso de esta solución, recibirá un cuaderno para entrenar un modelo de aprendizaje automático para la recomendación de productos.

Es posible que tenga que personalizar el cuaderno preconfigurado para asegurarse de que cumple sus requisitos empresariales únicos.

Esta solución de recomendaciones comerciales se puede implementar en dos modos diferentes. Puede probarla con datos de ejemplo o usar una base de datos modelada mediante la plantilla de base de datos para el comercio minorista de Synapse.

La solución **Retail - Product recommendations** (Venta al por menor: recomendaciones de productos) proporciona una canalización de recomendaciones para recomendaciones con filtrado basado en contenido. La canalización de filtrado basado en contenido usa el algoritmo LightGBM para entrenar un modelo para predecir las preferencias del usuario en función de las características de usuarios y elementos. Las características pueden ser características estáticas como perfil de usuario y perfil de elemento, así como características dinámicas como los patrones de comportamiento de usuario agregados. El sistema de recomendaciones con tipo de filtrado basado en contenido se suele usar para recomendaciones como "recomendación personalizada" o "nuevos productos que podrían gustar".

## <a name="get-started"></a>Primeros pasos

1. Apertura del área de trabajo de Synapse
1. En la pantalla de inicio, seleccione **Centro de conocimientos** en la sección **Descubrir más**.
1. En el Centro de conocimientos, seleccione **Examinar la galería**.
1. En la galería, seleccione la pestaña **Plantillas de base de datos**, desplácese hacia abajo hasta la sección **AI solutions** (Soluciones de IA) y seleccione la solución "Retail - Product recommendations" (Venta al por menor: recomendaciones de productos). Haga clic en **Continuar**.
1. Puede elegir entre dos opciones:
   * "Use sample data" (Usar datos de ejemplo) 
   * "Use my own data from a workspace database" (Usar mis propios datos de una base de datos de área de trabajo) Esta opción puede ser, por ejemplo, una base de datos modelada con la plantilla de base de datos de venta al por menor.
   
    Al hacer clic en **Implementar**, se abrirá un cuaderno en el área de trabajo de Synapse.

1. El cuaderno se abrirá ahora en su área de trabajo. Puede asociar este cuaderno a un grupo de Spark y empezar a explorarlo. Tenga en cuenta que este cuaderno está pensado para personalizarse según sus necesidades específicas.

> [!NOTE]
> Si elige su propia base de datos, debe personalizar el cuaderno para usar sus propios nombres de tabla y columna.

## <a name="next-steps"></a>Pasos siguientes

* [Introducción a Azure Synapse Analytics](../get-started.md)
* [Creación de un área de trabajo](../get-started-create-workspace.md)
