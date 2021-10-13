---
title: Administración de entornos en Estudio
titleSuffix: Azure Machine Learning
description: Aprenda a crear y administrar entornos en Estudio de Azure Machine Learning.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 5/25/2021
ms.topic: how-to
ms.custom: devx-track-python
ms.openlocfilehash: afa72add9b4fe6014b03331e8ee520669edb29b1
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129426063"
---
# <a name="manage-software-environments-in-azure-machine-learning-studio"></a>Administración de entornos de software en Estudio de Azure Machine Learning

En este artículo, aprenderá a crear y administrar [entornos](/python/api/azureml-core/azureml.core.environment.environment) de Azure Machine Learning en Estudio de Azure Machine Learning. Use los entornos para realizar un seguimiento de las dependencias de software de sus proyectos y reproducirlas a medida que evolucionan.

En los ejemplos de este artículo se muestran los siguientes procedimientos:

* Examinar entornos mantenidos.
* Crear un entorno y especificar las dependencias de paquetes.
* Editar una especificación de entorno existente y sus propiedades.
* Recompilar un entorno y vea los registros de compilación de imagen.

Para obtener información general de alto nivel sobre cómo funcionan los entornos en Azure Machine Learning, consulte [¿Qué son los entornos de ML?](concept-environments.md) Para obtener más información, consulte [Configuración de un entorno de desarrollo de Python para Azure Machine Learning](how-to-configure-environment.md).

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/) antes de empezar.
* Un [área de trabajo de Azure Machine Learning](how-to-manage-workspace.md).

## <a name="browse-curated-environments"></a>Examen de entornos mantenidos

Los entornos seleccionados contienen colecciones de paquetes de Python y están disponibles en el área de trabajo de forma predeterminada. Estos entornos están respaldados por imágenes de Docker en caché, lo que reduce el costo de preparación de la ejecución y permite escenarios de entrenamiento e inferencia. 

Haga clic en un entorno para ver información detallada sobre su contenido. Para más información, consulte [Entornos mantenidos de Azure Machine Learning](resource-curated-environments.md). 

## <a name="create-an-environment"></a>Creación de un entorno

Para crear un entorno:
1. En [Azure Machine Learning Studio](https://ml.azure.com), abra el área de trabajo.
1. En el lado izquierdo, seleccione **Entornos**.
1. Seleccione la pestaña **Entornos personalizados**. 
1. Seleccione el botón **Crear**. 

Para crear un entorno, especifique uno de los siguientes elementos:
* [Archivo](https://pip.pypa.io/en/stable/cli/pip_install) de requisitos de pip
* [Archivo](https://docs.conda.io/projects/conda/en/latest/user-guide/tasks/manage-environments.html) YAML de Conda
* [Imagen](https://hub.docker.com/search?q=&type=image) de Docker
* [Dockerfile](https://docs.docker.com/develop/develop-images/dockerfile_best-practices/)

:::image type="content" source="media/how-to-manage-environments-in-studio/create-page.jpg" alt-text="Asistente para la creación de un entorno":::

Antes de crear la entidad, puede personalizar el archivo de configuración, agregar etiquetas y descripciones, y revisar las propiedades. 

Si se asigna a un nuevo entorno el mismo nombre que un entorno existente en el área de trabajo, se creará una nueva versión del entorno existente.

## <a name="view-and-edit-environment-details"></a>Visualización y edición de los detalles del entorno

Una vez creado un entorno, haga clic en su nombre para ver los detalles. Use el menú desplegable para seleccionar diferentes versiones del entorno. Aquí puede ver los metadatos y el contenido del entorno, en sus capas de Docker y Conda. 

Haga clic en los iconos de lápiz para editar etiquetas y descripciones, así como los archivos de configuración o la imagen. Tenga en cuenta que cualquier cambio en las secciones Docker o Conda creará una nueva versión del entorno. 

:::image type="content" source="media/how-to-manage-environments-in-studio/details-page.jpg" alt-text="Página de detalles de entornos":::

## <a name="view-image-build-logs"></a>Visualización de registros de compilación de imagen

Haga clic en la pestaña **Registro de compilación** de la página de detalles para ver los registros de compilación de imagen de una versión del entorno. 

## <a name="rebuild-an-environment"></a>Recompilación de un entorno

En la página de detalles, haga clic en el botón **recompilar** para recompilar el entorno. Con esta acción, puede que los paquetes desanclados de los archivos de configuración se actualicen a la versión más reciente. 
