---
title: '¿Qué se puede hacer con Azure Machine Learning Studio (clásico): Azure?'
description: Machine Learning Studio (clásico) es una herramienta de arrastrar y colocar que permite crear rápidamente modelos desde una biblioteca de algoritmos y módulos listos para utilizarse.
services: machine-learning
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.assetid: e65c8fe1-7991-4a2a-86ef-fd80a7a06269
ms.service: machine-learning
ms.subservice: studio-classic
ms.topic: overview
ms.date: 08/19/2020
ms.openlocfilehash: ae20a9b0a62212b558d0613ac821ae8a6baa626b
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122696651"
---
# <a name="what-can-i-do-with-machine-learning-studio-classic"></a>¿Qué se puede hacer con Azure Machine Learning Studio (clásico)?

**SE APLICA A:**  ![esta es una marca de verificación, lo que significa que este artículo se aplica a Machine Learning Studio (clásico).](../../../includes/media/aml-applies-to-skus/yes.png)Machine Learning Studio (clásico)   ![Esta es una X, lo que significa que este artículo se aplica a Azure Machine Learning.](../../../includes/media/aml-applies-to-skus/no.png)[Azure Machine Learning](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)

[!INCLUDE [ML Studio (classic) retirement](../../../includes/machine-learning-studio-classic-deprecation.md)]

Machine Learning Studio (clásico) es una herramienta del tipo arrastrar y colocar que le permite crear, probar e implementar modelos de Machine Learning. Studio (clásico) publica modelos como servicios web que pueden consumir fácilmente en aplicaciones personalizadas o herramientas de inteligencia empresarial como Excel.


## <a name="studio-classic--interactive-workspace"></a>Área de trabajo interactiva de Studio (clásico)

Para desarrollar un modelo de análisis predictivo, normalmente se utilizan datos de una o varias fuentes, se transforman y analizan los datos a través de diversas funciones estadísticas y de manipulación de datos y se genera un conjunto de resultados. Desarrollar un modelo como este es un proceso iterativo: a medida que se modifican las diversas funciones y sus parámetros, sus resultados convergen hasta que esté satisfecho con un modelo entrenado y efectivo.

Machine Learning Studio (clásico) le proporciona un área de trabajo visual e interactiva para crear, probar e iterar fácilmente en un modelo de análisis predictivo. Arrastre y coloque ***conjuntos de datos** y _*_módulos_*_ de análisis en un lienzo interactivo, conéctelos para formar un _*_experimento_*_ que se ejecuta en Machine Learning Studio (clásico). Para iterar su diseño de modelo, se puede editar el experimento, guardar una copia si así se desea y ejecutarlo de nuevo. Cuando esté listo, puede convertir el _*_experimento de entrenamiento_*_ en un _*_experimento predictivo_*_ y luego publicarlo como *_servicio web_** para que otros usuarios puedan acceder al modelo.

No se requiere programación; conecte visualmente conjuntos de datos y módulos para construir el modelo de análisis predictivo.

![Diagrama de Machine Learning Studio (clásico): crear experimentos, leer datos de muchos orígenes, escribir datos puntuados, modelos de escritura.](./media/what-is-ml-studio/azure-ml-studio-diagram.jpg)

## <a name="download-the-ml-studio-classic-overview-diagram"></a>Descarga del diagrama de información general de Machine Learning Studio (clásico)
Descargue el diagrama **Microsoft Azure Machine Learning Studio (classic) Capabilities Overview** [Información general acerca de las funcionalidades de Machine Learning Studio (clásico)]. Para tenerlo a mano, puede imprimir el diagrama en tamaño tabloide (279 x 432 mm).

**Descargue aquí el diagrama: [Microsoft Machine Learning Studio (classic) Capabilities Overview](https://download.microsoft.com/download/C/4/6/C4606116-522F-428A-BE04-B6D3213E9E52/ml_studio_overview_v1.1.pdf)** 
![Microsoft Machine Learning Studio (classic) Capabilities Overview](./media/what-is-ml-studio/ml_studio_overview_v1.1.png) (Introducción a las funcionalidades de Microsoft Machine Learning Studio [clásico])


## <a name="components-of-a-studio-classic--experiment"></a>Componentes de un experimento de Studio (clásico)
Un experimento consta de conjuntos de datos que proporcionan datos a módulos analíticos, que se conectan en conjunto para construir un modelo de análisis predictivo. En concreto, un experimento válido tiene estas características:

* Tiene al menos un conjunto de datos y un módulo.
* Los conjuntos de datos pueden estar solo conectados a módulos.
* Los módulos pueden conectarse a conjuntos de datos o a otros módulos.
* Todos los puertos de entrada de los módulos deben tener alguna conexión al flujo de datos.
* Deben establecerse todos los parámetros necesarios para cada módulo.

Puede crear un experimento desde cero, o puede usar un experimento de ejemplo existente como plantilla. Para más información, consulte [Copia de experimentos de ejemplo para crear nuevos experimentos de aprendizaje automático](sample-experiments.md).

Para ver un ejemplo en el que se crea un experimento, consulte [Creación de un experimento en Machine Learning Studio (clásico)](create-experiment.md).

Para obtener un tutorial más completo de la creación de una solución de análisis predictivos, consulte [Desarrollo de una solución predictiva con Machine Learning Studio (clásico)](tutorial-part1-credit-risk.md).

### <a name="datasets"></a>Conjuntos de datos
Un conjunto de datos son los datos que se han cargado en Machine Learning Studio (clásico) para utilizarse en el proceso de modelado. Machine Learning Studio (clásico) incluye varios conjuntos de datos de ejemplo con los que puede experimentar, y puede cargar más a medida que los necesite. A continuación se muestran algunos ejemplos de los conjuntos de datos incluidos:

* **Datos sobre consumo de combustible por distancia recorrida para varios automóviles** : valores sobre consumo de combustible por distancia recorrida para automóviles identificados por el número de cilindros, los caballos de potencia, etc.
* **Datos sobre cáncer de mama** : datos de diagnóstico de cáncer de mama.
* **Datos de incendios forestales** : magnitud de los incendios forestales en el noreste de Portugal.

Cuando crea un experimento, puede elegir un conjunto de datos en la lista de conjuntos de datos disponibles a la izquierda del lienzo.

Para obtener una lista de los conjuntos de datos de ejemplo que se incluyen en Machine Learning Studio (clásico), vea [Uso de conjuntos de datos de ejemplo en Machine Learning Studio (clásico)](use-sample-datasets.md).

### <a name="modules"></a>Módulos
Un módulo es un algoritmo que puede aplicar sobre sus datos. Machine Learning Studio (clásico) cuenta con diversos módulos, que van desde las funciones de entrada de datos hasta procesos de entrenamiento, puntuación y validación. A continuación se muestran algunos ejemplos de los módulos incluidos:

* [Convertir a ARFF][convert-to-arff]: convierte un conjunto de datos serializados de .NET a Formato de archivo atributo-relación (ARFF).
* [Estadísticas elementales de procesos][elementary-statistics]: calcula estadísticas elementales como la media, la desviación estándar, etc.
* [Regresión lineal][linear-regression]: crea un modelo de regresión lineal basado en un descenso de gradiente en línea.
* [Puntuar modelo][score-model]: puntúa un modelo entrenado de clasificación o regresión.

Cuando crea un experimento, puede elegir un módulo en la lista de módulos disponibles a la izquierda del lienzo.

Un módulo puede tener un conjunto de parámetros que puede utilizar para configurar los algoritmos internos del módulo. Al seleccionar un módulo en el lienzo, los parámetros del módulo se muestran en el panel **Propiedades** a la derecha del lienzo. Puede modificar los parámetros en ese panel para ajustar su modelo.

Si necesita ayuda para desplazarse por la gran biblioteca de algoritmos de aprendizaje automático, consulte [Elección de algoritmos para Microsoft Machine Learning Studio (clásico)](../how-to-select-algorithms.md).

## <a name="deploying-a-predictive-analytics-web-service"></a>Implementación del servicio web de análisis predictivo
Una vez que el modelo de análisis predictivo esté listo, puede implementarlo como servicio web desde Machine Learning Studio (clásico). Para más información, consulte [Implementación de un servicio web de Machine Learning](deploy-a-machine-learning-web-service.md).

## <a name="next-steps"></a>Pasos siguientes
Puede aprender lo básico del análisis predictivo y de Machine Learning mediante un [inicio rápido paso a paso](create-experiment.md) y el [uso de ejemplos](sample-experiments.md).

<!-- Module References -->
[convert-to-arff]: /azure/machine-learning/studio-module-reference/convert-to-arff
[elementary-statistics]: /azure/machine-learning/studio-module-reference/compute-elementary-statistics
[linear-regression]: /azure/machine-learning/studio-module-reference/linear-regression
[score-model]: /azure/machine-learning/studio-module-reference/score-model