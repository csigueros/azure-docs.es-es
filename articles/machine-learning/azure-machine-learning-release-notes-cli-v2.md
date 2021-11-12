---
title: Notas de la versión de la CLI (v2)
titleSuffix: Azure Machine Learning
description: Obtenga información sobre las actualizaciones más recientes de la CLI de Azure Machine Learning (v2)
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.author: minxia
author: mx-iao
ms.date: 11/03/2021
ms.openlocfilehash: 9619b98ad824de757273cbe38567b5f9418686a3
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131566143"
---
# <a name="azure-machine-learning-cli-v2-release-notes"></a>Notas de la versión de la CLI de Azure Machine Learning (v2)

En este artículo va a obtener información sobre las versiones de la CLI de Azure Machine Learning (v2).

__Fuente RSS__: reciba notificaciones cuando esta página se actualice copiando y pegando la siguiente dirección URL en su lector de fuentes: `https://docs.microsoft.com/api/search/rss?search=%22Azure+machine+learning+release+notes-v2%22&locale=en-us`

## <a name="2021-10-04"></a>2021-10-04

### <a name="azure-machine-learning-cli-v2-v201a6"></a>CLI de Azure Machine Learning (v2) v2.0.1a6

- `az ml workspace`
  - Se ha actualizado el [Esquema YAML del área de trabajo](reference-yaml-workspace.md)
- `az ml compute`
  - Se han actualizado los esquemas YAML de [AmlCompute](reference-yaml-compute-aml.md) e [Instancia de proceso](reference-yaml-compute-instance.md)
  - Se ha eliminado la compatibilidad con la asociación de AKS heredada mediante `az ml compute attach`. La asociación de Kubernetes habilitada para Azure Arc se va a admitir en la próxima versión
- `az ml datastore`
  - Se han actualizado los esquemas YAML de los almacenes de datos de [Azure Blob](reference-yaml-datastore-blob.md), [Azure File](reference-yaml-datastore-files.md), [Azure Data Lake Gen1](reference-yaml-datastore-data-lake-gen1.md) y [Azure Data Lake Gen2](reference-yaml-datastore-data-lake-gen2.md)
  - Se ha agregado compatibilidad con la creación de almacenes de datos de Azure Data Lake Storage Gen1 y Gen2
- `az ml job`
  - Se han actualizado los esquemas YAML del [trabajo de comando](reference-yaml-job-command.md) y el [trabajo de barrido](reference-yaml-job-sweep.md)
  - Se ha agregado compatibilidad con la ejecución de trabajos de canalización ([esquema YAML de trabajo de canalización](reference-yaml-job-pipeline.md))
  - Se ha agregado compatibilidad con los literales de entrada de trabajo y los URI de datos de entrada de todos los tipos de trabajo
  - Se ha agregado compatibilidad con las salidas de trabajo de todos los tipos de trabajo
  - Se ha modificado la sintaxis de expresión de `{ <expression> }` a `${{ <expression> }}`. Para obtener más información, vea [Sintaxis de expresión para configurar trabajos de Azure ML](reference-yaml-core-syntax.md#expression-syntax-for-configuring-azure-ml-jobs-and-components)
- `az ml environment`
  - Se ha actualizado el [Esquema YAML del entorno](reference-yaml-environment.md)
  - Se ha agregado compatibilidad con la creación de entornos a partir del contexto de compilación de Docker
- `az ml model`
  - Se ha actualizado el [Esquema YAML del modelo](reference-yaml-model.md)
  - Se ha agregado una nueva propiedad `model_format` al modelo para escenarios de implementación sin código
- `az ml dataset`
  - Se ha cambiado el nombre del subgrupo `az ml data` a `az ml dataset`
  - Se ha actualizado el [Esquema YAML del conjunto de datos](reference-yaml-dataset.md)
- `az ml component`
  - Se han agregado comandos `az ml component` para administrar componentes de Azure ML
  - Se ha agregado compatibilidad con los componentes de comando ([esquema YAML del componente de comando](reference-yaml-component-command.md))
- `az ml online-endpoint`
  - El subgrupo `az ml endpoint` se ha dividido en dos grupos independientes: `az ml online-endpoint` y `az ml batch-endpoint`
  - Se ha actualizado el [Esquema de YAML de punto de conexión en línea](reference-yaml-endpoint-managed-online.md)
  - Se ha agregado compatibilidad con los puntos de conexión locales en escenarios de desarrollo y prueba
  - Se ha agregado compatibilidad interactiva de depuración de VSCode con los puntos de conexión locales (se ha agregado la marca `--vscode-debug` a `az ml batch-endpoint create/update`)
- `az ml online-deployment`
  - El subgrupo `az ml deployment` se ha dividido en dos grupos independientes: `az ml online-deployment` y `az ml batch-deployment`
  - Se ha actualizado el [Esquema YAML de implementación en línea administrada](reference-yaml-endpoint-managed-online.md)
  - Se ha agregado compatibilidad de escalabilidad automática mediante la integración con la escalabilidad automática de Azure Monitor
  - Se ha agregado compatibilidad con la actualización de varias propiedades de implementación en línea en la misma operación de actualización
  - Se ha agregado compatibilidad con la realización de operaciones simultáneas en implementaciones en el mismo punto de conexión
- `az ml batch-endpoint`
  - El subgrupo `az ml endpoint` se ha dividido en dos grupos independientes: `az ml online-endpoint` y `az ml batch-endpoint`
  - Se ha actualizado el [Esquema YAML de punto de conexión por lotes](reference-yaml-endpoint-batch.md)
  - Se ha quitado la propiedad `traffic`; se ha reemplazado por una propiedad de implementación predeterminada configurable
  - Se ha agregado compatibilidad con los URI de datos de entrada para `az ml batch-endpoint invoke`
  - Se ha agregado compatibilidad con la entrada de red virtual (vínculo privado)
- `az ml batch-deployment`
  - El subgrupo `az ml deployment` se ha dividido en dos grupos independientes: `az ml online-deployment` y `az ml batch-deployment`
  - Se ha actualizado el [Esquema YAML de implementación por lotes](reference-yaml-deployment-batch.md)

## <a name="2021-05-25"></a>25 de mayo de 2021

### <a name="announcing-the-cli-v2-preview-for-azure-machine-learning"></a>Anuncio de la CLI (v2) (versión preliminar) para Azure Machine Learning

La extensión `ml` de la CLI de Azure es la interfaz de próxima generación de Azure Machine Learning. Permite entrenar e implementar modelos desde la línea de comandos, e incluye características que aceleran el escalado vertical y horizontal de la ciencia de datos al tiempo que se hace un seguimiento del ciclo de vida del modelo. [Instalación e introducción](how-to-configure-cli.md).
