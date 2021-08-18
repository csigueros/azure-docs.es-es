---
title: 'Ejecución de tareas de ciencia de datos: proceso de ciencia de datos en equipo'
description: Describe cómo un científico de datos puede ejecutar un proyecto de ciencia de datos de forma controlada por versiones, de colaboración y cuyo seguimiento se puede realizar.
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 11/17/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 75166321eba4df5408fb6fc0fbb3119263d532ab
ms.sourcegitcommit: 30e3eaaa8852a2fe9c454c0dd1967d824e5d6f81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2021
ms.locfileid: "112464618"
---
# <a name="execute-data-science-tasks-exploration-modeling-and-deployment"></a>Ejecutar tareas de ciencia de datos: exploración, modelado e implementación

Entre las tareas de ciencia de datos habituales se incluyen la implementación, el modelado y la exploración de datos. En este artículo se muestra cómo usar las tareas para completar varias tareas comunes de ciencia de datos, como la exploración interactiva de los datos, el análisis de los datos, la creación de informes y la creación de modelos. Las opciones para implementar un modelo en un entorno de producción pueden incluir:

- [Azure Machine Learning](../index.yml)
- [SQL-Server con servicios de ML](/sql/advanced-analytics/r/r-services)
- [Azure Synapse Analytics](/azure/synapse-analytics/spark/apache-spark-machine-learning-mllib-notebook)


## <a name="1--exploration"></a>1. <a name='DataQualityReportUtility-1'></a> Exploración 

Un científico de datos puede realizar la exploración y los informes de diversas formas: mediante bibliotecas y paquetes disponibles para Python (matplotlib por ejemplo) o con R (ggplot o lattice por ejemplo). Los científicos de datos pueden personalizar dicho código para satisfacer las necesidades de exploración de datos para escenarios específicos. Las necesidades de lidiar con datos estructurados son distintas que en el caso de los datos no estructurados, como texto o imágenes. 

Productos como Azure Machine Learning también proporcionan una [preparación avanzada de datos](../how-to-create-register-datasets.md) para la exploración y la limpieza y transformación de datos, incluida la creación de características. El usuario debe decidir sobre las herramientas, las bibliotecas y los paquetes que mejor se adapten a sus necesidades. 

Al final de este fase debe entregarse un informe de exploración de datos. El informe debe proporcionar una vista bastante completa de los datos que se van a usar para el modelado y una evaluación de si los datos son adecuados para continuar con el paso de modelado. 

## <a name="2--modeling"></a>2. <a name='ModelingUtility-2'></a> Modelado

Hay diversos kits de herramientas y paquetes para entrenar modelos en diversos idiomas. Los científicos de datos no deben tener reparos en usar aquellos con los cuales se sientan cómodos, siempre que se cumplan las consideraciones de rendimiento relacionadas con la precisión y la latencia para los escenarios de producción y los casos de uso empresariales pertinentes.

### <a name="model-management"></a>Administración de modelos
Tras la creación de varios modelos, suele ser necesario tener un sistema para registrar y administrar los modelos. Normalmente, necesita una combinación de scripts o API y una base de datos back-end o un sistema de control de versiones. Algunas opciones que puede tener en cuenta para estas tareas de administración son:

1. [Azure Machine Learning: servicio Administración de modelos](../index.yml)
2. [ModelDB de MIT](https://people.csail.mit.edu/mvartak/papers/modeldb-hilda.pdf) 
3. [SQL Server como sistema de administración de modelos](https://blogs.technet.microsoft.com/dataplatforminsider/2016/10/17/sql-server-as-a-machine-learning-model-management-system/)

## <a name="3--deployment"></a>3. <a name='Deployment-3'></a> Implementación

La implementación de producción permite que un modelo desempeñe un rol activo en una empresa. Las predicciones a partir de un modelo implementado pueden usarse en las decisiones empresariales.

### <a name="production-platforms"></a>Plataformas de producción
Hay varios enfoques y plataformas para poner los modelos en producción. Estas son algunas opciones:


- [Implementación de modelos en Azure Machine Learning](../how-to-deploy-and-where.md)
- [Implementación de un modelo en SQL-Server](/sql/advanced-analytics/tutorials/sqldev-py6-operationalize-the-model)

> [!NOTE]
> Antes de la implementación, uno debe asegurarse de que la latencia de la puntuación de los modelos es lo suficientemente baja para usarse en producción.
>
>

Hay más ejemplos disponibles en tutoriales que muestran todos los pasos del proceso en **escenarios concretos**. Se enumeran y enlazan con descripciones en miniatura en los [tutoriales de ejemplo](walkthroughs.md). En ellos se ilustra cómo combinar herramientas y servicios locales y en la nube en un flujo de trabajo o canalización para crear una aplicación inteligente.

> [!NOTE]
> Para implementar mediante Azure Machine Learning Studio, consulte [Implementar un servicio web de Azure Machine Learning](../classic/deploy-a-machine-learning-web-service.md).
>
>

### <a name="ab-testing"></a>Pruebas A/B
Cuando hay varios modelos en producción, puede resultar útil realizar [pruebas A/B](https://en.wikipedia.org/wiki/A/B_testing) para comparar el rendimiento de los modelos. 

 
## <a name="next-steps"></a>Pasos siguientes

En [Track progress of data science projects](track-progress.md) (Realizar un seguimiento del progreso de los proyectos de ciencia de datos) se muestra cómo puede un científico de datos realizar un seguimiento del progreso de un proyecto de ciencia de datos.

[El modelo de operación y CI/CD](ci-cd-flask.md) muestra cómo se puede realizar CI/CD con modelos desarrollados.
