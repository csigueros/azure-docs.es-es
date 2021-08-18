---
title: 'Implementación de modelos en producción: proceso de ciencia de datos en equipos'
description: La implementación de modelos en producción les permite desempeñar un papel activo en la toma de decisiones empresariales.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 4bef5e76372959fb79686fd414c09e7d30431b52
ms.sourcegitcommit: 351279883100285f935d3ca9562e9a99d3744cbd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/19/2021
ms.locfileid: "112379499"
---
# <a name="deploy-models-to-production-to-play-an-active-role-in-making-business-decisions"></a>Implementación de modelos en producción para desempeñar un papel activo en la toma de decisiones empresariales

La implementación de producción permite que un modelo desempeñe un rol activo en una empresa. Las predicciones a partir de un modelo implementado pueden usarse en las decisiones empresariales.

## <a name="production-platforms"></a>Plataformas de producción

Hay varios enfoques y plataformas para poner los modelos en producción. Estas son algunas opciones:

- [Implementación de modelos con Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implementación de un modelo en SQL-Server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)
- [Azure Synapse Analytics](/azure/synapse-analytics/spark/apache-spark-machine-learning-mllib-notebook)

>[!NOTE]
>Antes de la implementación, uno debe asegurarse de que la latencia de la puntuación de los modelos es lo suficientemente baja para usarse en producción.
>

>[!NOTE]
>Para implementar mediante Azure Machine Learning Studio, consulte [Implementar un servicio web de Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>

## <a name="ab-testing"></a>Pruebas A/B

Si hay varios modelos en producción, se pueden usar [pruebas A/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar su rendimiento. 
 
## <a name="next-steps"></a>Pasos siguientes

También se proporcionan tutoriales que muestran todos los pasos del proceso en **escenarios concretos**. Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplo](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente.
