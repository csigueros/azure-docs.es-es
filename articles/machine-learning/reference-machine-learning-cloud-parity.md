---
title: Disponibilidad de características entre regiones en la nube
titleSuffix: Azure Machine Learning
description: En este artículo, se explican las diferencias en cuanto a la disponibilidad de características entre la nube pública y las regiones Azure Government, Azure Alemania y Azure China 21Vianet.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 08/24/2021
ms.custom: references_regions
ms.openlocfilehash: 8c05b77a77bbcbfc977dadb22151f0086001b859
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359448"
---
# <a name="azure-machine-learning-feature-availability-across-clouds-regions"></a>Disponibilidad de características de Azure Machine Learning entre regiones de nubes en la nube

Obtenga información sobre qué características de Azure Machine Learning están disponibles en las regiones Azure Government, Azure Alemania y Azure China 21Vianet. 

En la lista de regiones globales de Azure, hay varias regiones que sirven a mercados específicos, además de las regiones de la nube pública. Por ejemplo, las regiones Azure Government y Azure China 21Vianet. Azure Machine Learning se implementa en las siguientes regiones, además de en las regiones de la nube pública:

* **EE. UU.: Arizona** y **EE. UU.: Virginia** de la región Azure Government.
* **Este de China 2** de la región Azure China 21Vianet.

La información del resto de este documento proporciona información sobre qué características de Azure Machine Learning están disponibles en estas regiones, junto con información específica de la región sobre el uso de estas características.
## <a name="azure-government"></a>Azure Government 

| Característica | Estado de la nube pública  | EE.UU.: Virginia | EE.UU.: Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **[Aprendizaje automático automatizado](concept-automated-ml.md)** | | | |
| Creación y ejecución de experimentos en cuadernos                                    | Disponibilidad general                   | SÍ                | SÍ         |
| Creación y ejecución de experimentos en la experiencia web de Studio                        | Vista previa pública       | SÍ                | SÍ         |
| Capacidades de previsión de líderes del sector                                  | Disponibilidad general                   | SÍ                | SÍ         |
| Compatibilidad con aprendizaje profundo y otros aprendizajes avanzados                      | Disponibilidad general                   | SÍ                | SÍ         |
| Compatibilidad con datos de gran tamaño (hasta 100 GB)                                          | Vista previa pública       | SÍ                | SÍ         |
| Integración con Azure Databricks                                              | Disponibilidad general                   | No                 | No          |
| Integraciones con SQL, CosmosDB y HDInsight                                   | Disponibilidad general                   | SÍ                | SÍ         |
| **[Canalizaciones de Machine Learning](concept-ml-pipelines.md)** |   |  | | 
| Creación, ejecución y publicación de canalizaciones con el SDK de Azure Machine Learning                   | Disponibilidad general                   | SÍ                | SÍ         |
| Creación de puntos de conexión de canalización con el SDK de Azure Machine Learning                           | Disponibilidad general                   | SÍ                | SÍ         |
| Creación, edición y eliminación de ejecuciones programadas de canalizaciones con el SDK de Azure Machine Learning | Disponibilidad general                   | SÍ*               | SÍ*        |
| Visualización de los detalles de la ejecución de la canalización en Studio                                        | Disponibilidad general                   | SÍ                | SÍ         |
| Creación, ejecución, visualización y publicación de canalizaciones en el diseñador de Azure Machine Learning          | Disponibilidad general      | SÍ                | SÍ         |
| Integración de Azure Databricks con canalización de Machine Learning                             | Disponibilidad general                   | No                 | No          |
| Creación de puntos de conexión de canalización en el diseñador de Azure Machine Learning                             | Disponibilidad general      | SÍ                | SÍ         |
| **[Cuadernos integrados](how-to-run-jupyter-notebooks.md)** |   |  | | 
| Uso compartido de archivos y cuadernos del área de trabajo                                        | Disponibilidad general                   | SÍ                | SÍ         |
| Compatibilidad con R y Python                                                       | Disponibilidad general                   | SÍ                | SÍ         |
| Compatibilidad con redes virtuales                                                    | Vista previa pública       | No                 | No          |
| **[Instancia de proceso](concept-compute-instance.md)** |   |  | | 
| Instancias de proceso administradas para cuadernos integrados                         | Disponibilidad general                   | SÍ                | SÍ         |
| Jupyter, integración con JupyterLab                                            | Disponibilidad general                   | SÍ                | SÍ         |
| Compatibilidad con redes virtuales (VNet)                                             | Vista previa pública       | SÍ                | SÍ         |
| **Compatibilidad con SDK** |  |  | | 
| [Compatibilidad con el SDK de Python](/python/api/overview/azure/ml/)                                                         | Disponibilidad general                   | SÍ                | SÍ         |
| **[Seguridad](concept-enterprise-security.md)** |   | | | 
| Compatibilidad con Virtual Network (VNet) para aprendizaje                                | Disponibilidad general                   | SÍ                | SÍ         |
| Compatibilidad con Virtual Network (VNet) para inferencia                               | Disponibilidad general                   | SÍ                | SÍ         |
| Autenticación de puntos de conexión de puntuación                                            | Vista previa pública       | SÍ                | SÍ         |
| Punto de conexión privado de área de trabajo                                                 | GA  |  GA  |  GA |
| ACI detrás de VNet                                                            | Vista previa pública       | No                 | No          |
| ACR detrás de VNet                                                            | Disponibilidad general       | SÍ                 | SÍ          |
| IP privada de clúster de AKS                                                  | Vista previa pública       | No                 | No          |
| **Proceso** |   | | |
| [Administración de cuotas entre áreas de trabajo](how-to-manage-quotas.md)                                         | Disponibilidad general                   | SÍ                | SÍ         |
| **[Datos para el aprendizaje automático](concept-data.md)** |   | | |
| Creación, visualización o edición de conjuntos de datos y almacenes de datos del SDK                  | Disponibilidad general                   | SÍ                | SÍ         |
| Creación, visualización o edición de conjuntos de datos y almacenes de datos de la interfaz de usuario                   | Disponibilidad general                   | SÍ                | SÍ         |
| Visualización, edición o eliminación de monitores de desfase de conjuntos de datos del SDK                   | Vista previa pública       | SÍ                | SÍ         |
| Visualización, edición o eliminación de monitores de desfase de conjunto de datos de la interfaz de usuario                    | Vista previa pública       | SÍ                | SÍ         |
| **Ciclo de vida de Machine Learning** |   | | |
| [Generación de perfiles de modelos](how-to-deploy-profile-model.md)                                                            | Disponibilidad general                   | SÍ                | PARTIAL     |
| [La CLI de Azure ML 1.0](reference-azure-machine-learning-cli.md)     | Disponibilidad general                   | SÍ                | SÍ         |
| [Modelos acelerados mediante hardware basados en FPGA](how-to-deploy-fpga-web-service.md)                                     | Disponibilidad general                   | No                 | No          |
| [Integración con Visual Studio Code](how-to-setup-vs-code.md)                                             | Vista previa pública       | No                 | No          |
| [Integración con Event Grid](how-to-use-event-grid.md)                                                     | Vista previa pública       | No                 | No          |
| [Integración de Azure Stream Analytics con Azure Machine Learning](../stream-analytics/machine-learning-udf.md)               | Vista previa pública       | No                 | No          |
| **Etiquetado de [imágenes](how-to-create-image-labeling-projects.md) y [texto](how-to-create-text-labeling-projects.md)** |   | | |
| Etiquetado del Portal de administración de proyectos                                        | Disponibilidad general                   | SÍ                | SÍ         |
| Portal de etiquetador                                                            | Disponibilidad general                   | SÍ                | SÍ         |
| Etiquetado mediante recursos privados                                          | Disponibilidad general                   | SÍ                | SÍ         |
| Etiquetado asistido por ML (clasificación de imágenes y detección de objetos)           | Vista previa pública       | SÍ                | SÍ         |
| **[Aprendizaje automático responsable](concept-responsible-ml.md)** |   | | |
| Capacidad de explicación en la interfaz de usuario                                                       | Vista previa pública       | No                 | No          |
| Kit de herramientas de privacidad diferencial SmartNoise                                    | OSS                  | No                 | No          |
| Etiquetas personalizadas en Azure Machine Learning para implementar hojas de datos              | Disponibilidad general                   | No                 | No          |
| Integración con AzureML de equidad                                               | Vista previa pública       | No                 | No          |
| SDK de interpretabilidad                                                      | Disponibilidad general                   | SÍ                | SÍ         |
| **Cursos** |   | | |
| [Streaming de registro de experimentación](how-to-track-monitor-analyze-runs.md)                                              | Disponibilidad general                   | SÍ                | SÍ         |
| [Aprendizaje de refuerzo](how-to-use-reinforcement-learning.md)                                                     | Vista previa pública       | No                 | No          |
| [Interfaz de usuario de experimentación](how-to-track-monitor-analyze-runs.md)                                                         | Vista previa pública                   | SÍ                | SÍ         |
| [Integración de .NET ML.NET 1.0](/dotnet/machine-learning/tutorials/object-detection-model-builder)                                                | Disponibilidad general                   | SÍ                | SÍ         |
| **Inferencia** |   | | |
| [Inferencia por lotes](tutorial-pipeline-batch-scoring-classification.md)                                                          | Disponibilidad general                   | SÍ                | SÍ         |
| [Data Box Edge con FPGA](how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server)                                                    | Vista previa pública       | No                 | No          |
| **Otros** |   | | |
| [Open Datasets](../open-datasets/samples.md)                                                              | Vista previa pública       | SÍ                | SÍ         |
| [Cognitive Search personalizado](how-to-deploy-model-cognitive-search.md)                                                    | Vista previa pública       | SÍ                | SÍ         |


### <a name="azure-government-scenarios"></a>Escenarios de Azure Government

| Escenario                                                    | EE.UU.: Virginia | EE.UU.: Arizona| Limitaciones  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Configuración de la seguridad general** |   | | |
| Deshabilitación o control del acceso a Internet (entrante y saliente) y una red virtual específica | PARTIAL| PARTIAL   |  | 
| Selección de ubicación de todos los recursos o servicios asociados  | SÍ | SÍ |  |
| Cifrado en reposo y en tránsito                                                 | SÍ | SÍ |  |
| Acceso de SSH y raíz a los recursos de proceso                                          | SÍ | SÍ |  |
| Mantenimiento de la seguridad de los sistemas implementados (instancias, puntos de conexión, etc.), incluida la protección de puntos de conexión, la revisión y el registro |  PARTIAL|  PARTIAL |ACI detrás de VNet no disponible actualmente |                                  
| Control (deshabilitación, limitación o restricción) del uso de la integración de ACI/AKS                    | PARTIAL| PARTIAL |ACI detrás de VNet no disponible actualmente|
| Control de acceso basado en rol de Azure (RBAC de Azure): creaciones de roles personalizados                           | SÍ | SÍ |  |
| Control de acceso a imágenes de ACR utilizadas por el servicio de ML (proporcionado o mantenido por Azure frente a personalizado)  |PARTIAL|  PARTIAL |  |
| **Uso general de Machine Learning Service** |  | | |
| Capacidad de tener un entorno de desarrollo para crear un modelo, entrenar ese modelo, hospedarlo como punto de conexión y consumirlo a través de una aplicación web     | SÍ | SÍ |  |
| Capacidad de extraer datos de ADLS (Data Lake Storage)                                 |SÍ | SÍ |  |
| Capacidad de extraer datos de Azure Blob Storage                                       |SÍ | SÍ |  |



### <a name="other-azure-government-limitations"></a>Otras limitaciones de Azure Government

* En el caso de instancias de proceso de Azure Machine Learning, la capacidad de actualizar un token que dura más de 24 horas no está disponible en Azure Government.
* La generación de perfiles de modelos no admite 4 CPU en la región EE.UU.: Arizona.   
* Es posible que los cuadernos de ejemplo no funcionen en Azure Government si necesitan tener acceso a datos públicos.
* Direcciones IP: el comando de la CLI que se utiliza en las instrucciones de [acceso obligatorio a una red de Internet pública](how-to-secure-training-vnet.md#required-public-internet-access) no devuelve ningún intervalo IP. Use los [intervalos IP y las etiquetas de servicio de Azure para Azure Government](https://www.microsoft.com/download/details.aspx?id=57063) en su lugar.
* En el caso de las canalizaciones programadas, también proporcionamos un mecanismo de desencadenador basado en blobs. Este mecanismo no es compatible con las áreas de trabajo de CMK. Para habilitar un desencadenador basado en blobs para áreas de trabajo de CMK, tiene que realizar una configuración adicional. Para más información, consulte [Desencadenamiento de una ejecución de una canalización de Machine Learning desde una aplicación lógica](how-to-trigger-published-pipeline.md).
* Firewalls: cuando use una región Azure Government, agregue los siguientes hosts adicionales a la configuración del firewall:

    * Para el uso de Arizona: `usgovarizona.api.ml.azure.us`
    * Para el uso de Virginia: `usgovvirginia.api.ml.azure.us`
    * Para ambos: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure China 21Vianet 

| Característica                                       | Estado de la nube pública | CH-East-2 | CH-North-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Aprendizaje automático automatizado** |    | | |
| Creación y ejecución de experimentos en cuadernos                                    | Disponibilidad general               | SÍ       | N/D        |
| Creación y ejecución de experimentos en la experiencia web de Studio                        | Vista previa pública   | SÍ       | N/D        |
| Capacidades de previsión de líderes del sector                                  | Disponibilidad general               | SÍ       | N/D        |
| Compatibilidad con aprendizaje profundo y otros aprendizajes avanzados                      | Disponibilidad general               | SÍ       | N/D        |
| Compatibilidad con datos de gran tamaño (hasta 100 GB)                                          | Vista previa pública   | SÍ       | N/D        |
| Integración con Azure Databricks                                              | Disponibilidad general               | No        | N/D        |
| Integraciones con SQL, CosmosDB y HDInsight                                   | Disponibilidad general               | SÍ       | N/D        |
| **Canalizaciones de Machine Learning** |    | | |
| Creación, ejecución y publicación de canalizaciones con el SDK de Azure Machine Learning                   | Disponibilidad general               | SÍ       | N/D        |
| Creación de puntos de conexión de canalización con el SDK de Azure Machine Learning                           | Disponibilidad general               | SÍ       | N/D        |
| Creación, edición y eliminación de ejecuciones programadas de canalizaciones con el SDK de Azure Machine Learning | Disponibilidad general               | SÍ       | N/D        |
| Visualización de los detalles de la ejecución de la canalización en Studio                                        | Disponibilidad general               | SÍ       | N/D        |
| Creación, ejecución, visualización y publicación de canalizaciones en el diseñador de Azure Machine Learning          | Disponibilidad general  | SÍ       | N/D        |
| Integración de Azure Databricks con canalización de Machine Learning                             | Disponibilidad general               | No        | N/D        |
| Creación de puntos de conexión de canalización en el diseñador de Azure Machine Learning                             | Disponibilidad general   | SÍ       | N/D        |
| **Cuadernos integrados** |   | | |
| Uso compartido de archivos y cuadernos del área de trabajo                                        | Disponibilidad general               | SÍ       | N/D        |
| Compatibilidad con R y Python                                                       | Disponibilidad general               | SÍ       | N/D        |
| Compatibilidad con redes virtuales                                                    | Vista previa pública   | No        | N/D        |
| **Instancia de proceso** |    | | |
| Instancias de proceso administradas para cuadernos integrados                         | Disponibilidad general               | SÍ        | N/D        |
| Jupyter, integración con JupyterLab                                            | Disponibilidad general               | SÍ       | N/D        |
| Compatibilidad con redes virtuales (VNet)                                             | Vista previa pública   | SÍ       | N/D        |
| **Compatibilidad con SDK** |    | | |
| Compatibilidad con el SDK de Python                                                         | Disponibilidad general               | SÍ       | N/D        |
| **Seguridad** |   | | |
| Compatibilidad con Virtual Network (VNet) para aprendizaje                                | Disponibilidad general               | SÍ       | N/D        |
| Compatibilidad con Virtual Network (VNet) para inferencia                               | Disponibilidad general               | SÍ       | N/D        |
| Autenticación de puntos de conexión de puntuación                                            | Vista previa pública   | SÍ       | N/D        |
| Punto de conexión privado de área de trabajo                                                 | Disponibilidad general               | No        | N/D        |
| ACI detrás de VNet                                                            | Vista previa pública   | No        | N/D        |
| ACR detrás de VNet                                                            | Disponibilidad general   | SÍ       | N/D        |
| IP privada de clúster de AKS                                                  | Vista previa pública   | No        | N/D        |
| **Proceso** |   | | |
| Administración de cuotas entre áreas de trabajo                                         | Disponibilidad general               | SÍ       | N/D        |
| **Datos para el aprendizaje automático** | | | |
| Creación, visualización o edición de conjuntos de datos y almacenes de datos del SDK                  | Disponibilidad general               | SÍ       | N/D        |
| Creación, visualización o edición de conjuntos de datos y almacenes de datos de la interfaz de usuario                   | Disponibilidad general               | SÍ       | N/D        |
| Visualización, edición o eliminación de monitores de desfase de conjuntos de datos del SDK                   | Vista previa pública   | SÍ       | N/D        |
| Visualización, edición o eliminación de monitores de desfase de conjunto de datos de la interfaz de usuario                    | Vista previa pública   | SÍ       | N/D        |
| **Ciclo de vida de Machine Learning** |    | | |
| Generación de perfiles de modelos                                                            | Disponibilidad general               | PARTIAL   | N/D        |
| La extensión Azure DevOps para Machine Learning y la CLI de Azure ML         | Disponibilidad general               | SÍ       | N/D        |
| Modelos acelerados mediante hardware basados en FPGA                                     | Disponibilidad general               | No        | N/D        |
| Integración con Visual Studio Code                                             | Vista previa pública   | No        | N/D        |
| Integración con Event Grid                                                     | Vista previa pública   | SÍ       | N/D        |
| Integración de Azure Stream Analytics con Azure Machine Learning               | Vista previa pública   | No        | N/D        |
| **Etiquetado** |    | | |
| Etiquetado del Portal de administración de proyectos                                        | Disponibilidad general               | SÍ       | N/D        |
| Portal de etiquetador                                                            | Disponibilidad general               | SÍ       | N/D        |
| Etiquetado mediante recursos privados                                          | Disponibilidad general               | SÍ       | N/D        |
| Etiquetado asistido por ML (clasificación de imágenes y detección de objetos)           | Vista previa pública   | SÍ       | N/D        |
| **Aprendizaje automático responsable** |    | | |
| Capacidad de explicación en la interfaz de usuario                                                       | Vista previa pública   | No        | N/D        |
| Kit de herramientas de privacidad diferencial SmartNoise                                    | OSS              | No        | N/D        |
| Etiquetas personalizadas en Azure Machine Learning para implementar hojas de datos              | Disponibilidad general               | No        | N/D        |
| Integración con AzureML de equidad                                               | Vista previa pública   | No        | N/D        |
| SDK de interpretabilidad                                                      | Disponibilidad general               | SÍ       | N/D        |
| **Cursos** |    | | |
| Streaming de registro de experimentación                                              | Disponibilidad general               | SÍ       | N/D        |
| Aprendizaje de refuerzo                                                     | Vista previa pública   | No        | N/D        |
| Interfaz de usuario de experimentación                                                         | Disponibilidad general               | SÍ       | N/D        |
| Integración de .NET ML.NET 1.0                                                | Disponibilidad general               | SÍ       | N/D        |
| **Inferencia** |   | | |
| Inferencia por lotes                                                          | Disponibilidad general               | SÍ       | N/D        |
| Data Box Edge con FPGA                                                    | Vista previa pública   | No        | N/D        |
| **Otros** |    | | |
| Open Datasets                                                              | Vista previa pública   | SÍ       | N/D        |
| Cognitive Search personalizado                                                    | Vista previa pública   | SÍ       | N/D        |



### <a name="other-azure-china-limitations"></a>Otras limitaciones de Azure China

* Azure China tiene una SKU de máquina virtual limitada, especialmente para la SKU de GPU. Solo tiene la familia NCv3 (V100).
* Los puntos de conexión de la API de REST son diferentes de Azure global. Use la tabla siguiente para buscar el punto de conexión de API de REST para regiones Azure China:

    | Punto de conexión de REST                 | Azure global                                 | Gobierno de China                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Plano de administración | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Plano de datos       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* Es posible que los cuadernos de ejemplo no funcionen, si necesitan tener acceso a datos públicos.
* Intervalos de direcciones IP: el comando de la CLI que se utiliza en las instrucciones de [acceso obligatorio a una red de Internet pública](how-to-secure-training-vnet.md#required-public-internet-access) no devuelve ningún intervalo IP. Use los [intervalos IP y las etiquetas de servicio de Azure para Azure China](https://www.microsoft.com//download/details.aspx?id=57062) en su lugar.
* No se admite la versión preliminar de las instancias de proceso de Azure Machine Learning en un área de trabajo en la que el punto de conexión privado está habilitado, pero se admitirá CI en la siguiente implementación de la expansión del servicio a todas las regiones AML.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre las regiones en las que está disponible Azure Machine Learning, consulte [Productos por región](https://azure.microsoft.com/global-infrastructure/services/).