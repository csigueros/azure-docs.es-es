---
author: peterclu
ms.service: machine-learning
ms.topic: include
ms.date: 03/08/2021
ms.author: peterlu
ms.openlocfilehash: d6d73754a22a3a0870a6ea141e4a21e7f2ccff09
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2021
ms.locfileid: "122688775"
---
En la tabla siguiente se resumen las principales diferencias entre Machine Learning Studio (clásico) y Azure Machine Learning.

| Característica | ML Studio (clásico) | Azure Machine Learning |
|---| --- | --- |
| Interfaz que permite arrastrar y colocar | Experiencia clásica | Experiencia actualizada: [diseñador de Azure Machine Learning](../articles/machine-learning/concept-designer.md)| 
| SDK de código | No compatible | Completamente integrado con los SDK de [Azure Machine Learning Python](/python/api/overview/azure/ml/) y [R](https://github.com/Azure/azureml-sdk-for-r) |
| Experimento | Escalable (límite de datos de entrenamiento, 10 GB) | Escalar con destino de proceso |
| Entrenamiento de destinos de proceso | Destino de proceso propietario, solo admite CPU | Amplia gama de [destinos de proceso de entrenamiento](../articles/machine-learning/concept-compute-target.md#train) personalizables. Incluye compatibilidad con GPU y CPU | 
| Destinos de proceso de implementación | Formato del servicio web propietario, no personalizable | Amplia gama de [destinos de proceso de implementación](../articles/machine-learning/concept-compute-target.md#deploy) personalizables. Incluye compatibilidad con GPU y CPU |
| Canalización de Machine Learning | No compatible | Creación de [canalizaciones](../articles/machine-learning/concept-ml-pipelines.md) flexibles y modulares para automatizar flujos de trabajo |
| MLOps | Implementación y administración básicas de modelos; implementaciones exclusivas de CPU | Control de versiones de entidades (modelo, datos, flujos de trabajo), automatización de flujos de trabajo, integración con herramientas de CICD, implementaciones de CPU y GPU, [y más](../articles/machine-learning/concept-model-management-and-deployment.md) |
| Formato del modelo | Formato propietario, solo Studio (clásico) | Se admiten varios formatos en función del tipo de trabajo de entrenamiento |
| Entrenamiento de modelos y optimización de hiperparámetros automatizados |  No compatible | [Admitido](../articles/machine-learning/concept-automated-ml.md). Opciones de los tipos código primero y sin código. | 
| Detección de desfase de datos | No compatible | [Compatible](../articles/machine-learning/how-to-monitor-datasets.md) |
| Proyecto de etiquetado de datos | No compatible | [Compatible](../articles/machine-learning/how-to-create-labeling-projects.md) |
| Control de acceso basado en rol (RBAC) | Solo rol de colaborador y propietario | [Definición de roles flexibles y control RBAC](../articles/machine-learning/how-to-assign-roles.md) |
| AI Gallery | Compatible ([https://gallery.azure.ai/](https://gallery.azure.ai/)) | No compatible <br><br> Obtenga información con los [cuadernos de SDK de Python de ejemplo](https://github.com/Azure/MachineLearningNotebooks). |