---
title: Creación de un trabajo de entrenamiento con la interfaz de usuario de creación de trabajos
titleSuffix: Azure Machine Learning
description: Aprenda a usar la interfaz de usuario de creación de trabajos en Azure Machine Learning Studio para crear un trabajo de entrenamiento.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.custom: devplatv2
author: wenxwei
ms.author: wenxwei
ms.date: 06/22/2021
ms.reviewer: laobri
ms.openlocfilehash: 94eb2beb95a43e422e8bff6c3ab53f7d2e6c208b
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094716"
---
# <a name="create-a-training-job-with-the-job-creation-ui-preview"></a>Creación de un trabajo de entrenamiento con la interfaz de usuario de creación de trabajos (versión preliminar)

Hay muchas maneras de crear un trabajo de entrenamiento con Azure Machine Learning. Puede usar la CLI (consulte [Entrenamiento de modelos [crear trabajos] con la CLI [v2]](how-to-train-cli.md)), la API REST (consulte [Entrenamiento de modelos con REST [versión preliminar]](how-to-train-with-rest.md)) o, directamente, la interfaz de usuario para crear un trabajo de entrenamiento. En este artículo, aprenderá a usar sus propios datos y código para entrenar un modelo de Machine Learning con la interfaz de usuario de creación de trabajos en Azure Machine Learning Studio.

## <a name="prerequisites"></a>Requisitos previos

* Suscripción a Azure. Si no tiene una suscripción de Azure, cree una cuenta gratuita antes de empezar. Pruebe hoy mismo la [versión gratuita o de pago de Azure Machine Learning](https://aka.ms/AMLFree).

* Un área de trabajo de Azure Machine Learning. Consulte [Creación de un área de trabajo de Azure Machine Learning](how-to-manage-workspace.md). 

* Conocimientos básicos de qué es un trabajo en Azure Machine Learning. Consulte [Introducción a los trabajos](how-to-train-cli.md#introducing-jobs).

## <a name="get-started"></a>Introducción

1. Inicie sesión en [Azure Machine Learning Studio](https://ml.azure.com). 

1. Seleccione su suscripción y área de trabajo.
 
* Puede acceder a la interfaz de usuario de creación de trabajos desde la página principal. Haga clic en **Crear nuevo** y seleccione **Trabajo**. 
[![Página principal del Estudio de Azure Machine Learning](media/how-to-train-with-ui/home-entry.png)](media/how-to-train-with-ui/home-entry.png)

* También puede acceder a la creación de trabajos desde el panel izquierdo. Haga clic en **+ Nuevo** y seleccione **Trabajo**. 
[![Panel de navegación izquierdo del Estudio de Azure Machine Learning](media/how-to-train-with-ui/left-nav-entry.png)](media/how-to-train-with-ui/left-nav-entry.png)

* Si está en la página de experimentos, también puede ir a la pestaña **Todas las ejecuciones** y hacer clic en **Crear trabajo**. 
[![Entrada de página de experimentos para la interfaz de usuario de creación de trabajos](media/how-to-train-with-ui/experiment-entry.png)](media/how-to-train-with-ui/experiment-entry.png)

Todas estas opciones le llevarán al panel de creación de trabajos, que contiene un asistente para configurar y crear un trabajo de entrenamiento. 

## <a name="select-compute-resources"></a>Selección de los recursos de proceso

El primer paso de la interfaz de usuario de creación de trabajos consiste en seleccionar el destino de proceso en el que quiere que se ejecute el trabajo. La interfaz de usuario de creación de trabajos admite varios tipos de proceso:

| Tipo de proceso | Introducción | 
| --- | --- | 
| Instancia de proceso | [¿Qué es una instancia de proceso de Azure Machine Learning?](concept-compute-instance.md) | 
| Clúster de proceso | [¿Qué es un clúster de proceso?](how-to-create-attach-compute-cluster.md#what-is-a-compute-cluster) | 
| Clúster de Kubernetes asociado | [Configuración del aprendizaje automático habilitado para Azure Arc (versión preliminar)](how-to-attach-arc-kubernetes.md) | 

1. Seleccione un tipo de proceso.
1. Seleccione un recurso de proceso existente. En la lista desplegable se muestra la información de nodo y el tipo de SKU para ayudarle a elegir.
1. Para un clúster de proceso o un clúster de Kubernetes, también puede especificar cuántos nodos quiere para el trabajo en **Recuento de instancias**. El número predeterminado de instancias es 1. 
1. Cuando esté satisfecho con las opciones elegidas, seleccione **Siguiente**. 
 [![Selección de un clúster de proceso](media/how-to-train-with-ui/compute-cluster.png)](media/how-to-train-with-ui/compute-cluster.png)

Si usa Azure Machine Learning por primera vez, verá una lista vacía y un vínculo para crear un proceso. 

 [![Crear una nueva instancia de proceso](media/how-to-train-with-ui/create-new-compute.png)](media/how-to-train-with-ui/create-new-compute.png)

Para obtener más información sobre cómo crear los distintos tipos, vea:

| Tipo de proceso | Procedimiento | 
| --- | --- | 
| Instancia de proceso | [Creación y administración de una instancia de proceso de Azure Machine Learning](how-to-create-manage-compute-instance.md) | 
| Clúster de proceso | [Creación de un clúster de proceso de Azure Machine Learning](how-to-create-attach-compute-cluster.md) | 
| Clúster de Kubernetes asociado | [Asociación de un clúster de Kubernetes habilitado para Azure Arc](how-to-attach-arc-kubernetes.md) | 

## <a name="specify-the-necessary-environment"></a>Especificación del entorno necesario

Después de seleccionar un destino de proceso, debe especificar el entorno de tiempo de ejecución para el trabajo. La interfaz de usuario de creación de trabajos admite tres tipos de entorno:

* Entornos mantenidos
* Entornos personalizados
* Imagen de registro de contenedor 

### <a name="curated-environments"></a>Entornos mantenidos

Los entornos mantenido son colecciones definidas por Azure de paquetes de Python que se usan en cargas de trabajo de ML comunes. Los entornos mantenidos están disponibles en el área de trabajo de forma predeterminada. Estos entornos están respaldados por imágenes de Docker en caché, lo que reduce la sobrecarga de preparación de la ejecución. Las tarjetas que aparecen en la página "Entornos mantenidos" muestran los detalles de cada entorno. Para obtener más información, vea [Entornos mantenidos de Azure Machine Learning](resource-curated-environments.md).

 [![Entornos mantenidos](media/how-to-train-with-ui/curated-env.png)](media/how-to-train-with-ui/curated-env.png)

### <a name="custom-environments"></a>Entornos personalizados

Los entornos personalizados son los entornos que ha especificado usted mismo. Puede especificar un entorno o reutilizar uno que ya haya creado. Para obtener más información, consulte [Administración de entornos de software en el Estudio de Azure Machine Learning (versión preliminar)](how-to-manage-environments-in-studio.md#create-an-environment). 

### <a name="container-registry-image"></a>Imagen de registro de contenedor

Si no quiere usar los entornos mantenidos de Azure Machine Learning ni especificar su propio entorno personalizado, puede usar una imagen de Docker de un registro de contenedor público, como [Docker Hub](https://hub.docker.com/). Si la imagen se encuentra en un contenedor privado, active **This is a private container registry** (Es un registro de contenedor privado). En el caso de los registros privados, deberá escribir un nombre de usuario y una contraseña válidos para que Azure pueda obtener la imagen. 
[![Imagen de registro de contenedor](media/how-to-train-with-ui/container-registry-image.png)](media/how-to-train-with-ui/container-registry-image.png)

## <a name="configure-your-job"></a>Configuración del trabajo

Después de especificar el entorno, puede configurar el trabajo con más opciones. 

|Campo| Descripción|
|------| ------|
|Nombre del trabajo| El campo de nombre del trabajo se usa para identificar de forma única el trabajo. También se usa como nombre para mostrar del trabajo. Es opcional establecer este campo; si no escribe nada, Azure generará un nombre GUID para el trabajo. Nota: El nombre de trabajo debe ser único.|
|Nombre del experimento| Esto ayuda a organizar el trabajo en el Estudio de Azure Machine Learning. El registro de ejecución de cada trabajo se organizará en el experimento correspondiente en la pestaña "Experimento" del Estudio. De forma predeterminada, Azure colocará el trabajo en el experimento **Predeterminado**.|
|Código| Puede cargar un archivo de código o una carpeta desde la máquina, o bien cargar un archivo de código desde el almacenamiento de blobs predeterminado del área de trabajo. Una vez que haya realizado la selección, Azure mostrará los archivos que se van a cargar. |
|Get-Help| El comando que se debe ejecutar. Los argumentos de la línea de comandos pueden escribirse explícitamente en el comando o inferirse de otras secciones, sobre todo las **entradas** que usan la notación con llaves, como se indica en la sección siguiente.|
|Entradas| Especifique el enlace de entrada. Se admiten tres tipos de entradas: 1) un conjunto de datos registrado de Azure Machine Learning; 2) un almacenamiento de blobs predeterminado del área de trabajo; 3) un archivo local cargado. Puede agregar varias entradas. |
|Variables de entorno| El establecimiento de variables de entorno le permite proporcionar una configuración dinámica al trabajo. Aquí puede agregar la variable y el valor.|
|Etiquetas| Agregue etiquetas al trabajo como ayuda para la organización.|

### <a name="specify-code-and-inputs-in-the-command-box"></a>Especificación de código y entradas en el cuadro de comandos

#### <a name="code"></a>Código

El comando se ejecuta desde el directorio raíz de la carpeta de código cargada. Después de seleccionar el archivo de código o la carpeta, verá los archivos que se van a cargar. Copie la ruta de acceso relativa al código que contiene el punto de entrada y péguela en el cuadro con la etiqueta **Enter the command to start the job** (Escriba el comando para iniciar el trabajo). 

Si el código se encuentra en el directorio raíz, puede hacer referencia directamente a él en el comando. Por ejemplo, `python main.py`.

Si el código no está en el directorio raíz, debe usar la ruta de acceso relativa. Por ejemplo, la estructura del [modelo de lenguaje de palabras](https://github.com/Azure/azureml-examples/tree/main/cli/jobs/train/pytorch/word-language-model) es la siguiente:

```tree
.
├── job.yml
├── data
└── src
    └── main.py
```
En este caso, el código fuente se encuentra en el subdirectorio `src`. El comando sería `python ./src/main.py` (más otros argumentos de la línea de comandos).

[![Referencia al código en el comando](media/how-to-train-with-ui/code-command.png)](media/how-to-train-with-ui/code-command.png)

#### <a name="inputs"></a>Entradas

Hay dos formas de realizar el enlace de entrada: 

* Nombre de entrada: cuando usa una entrada en el comando, debe especificar el nombre de la entrada. Para indicar una variable de entrada, use el formato `{inputs.input_name}`. Por ejemplo, `{inputs.wiki}`. Después, puede hacer referencia a ella en el comando, por ejemplo, `--data {inputs.wiki}`.
[![Referencia al nombre de entrada en el comando](media/how-to-train-with-ui/input-command-name.png)](media/how-to-train-with-ui/input-command-name.png)

* Ruta de acceso: puede usar `--data .path` para especificar una ubicación en la nube. La ruta de acceso es lo que especifique en el campo **Path on compute** (Ruta de acceso en proceso).
[![Referencia a la ruta de acceso de entrada en el comando](media/how-to-train-with-ui/input-command-path.png)](media/how-to-train-with-ui/input-command-path.png)

>[!NOTE] 
>En el **comando para iniciar el trabajo**, debe agregar un punto en el valor de la **ruta de acceso en proceso**. Por ejemplo, `/data/wikitext-2` se convierte en `./data/wikitext-2`.

## <a name="review-and-create"></a>Revisión y creación 

Una vez que haya configurado el trabajo, seleccione **Siguiente** para ir a la página **Revisar**. Para modificar una opción, seleccione el icono de lápiz y realice el cambio. 

Puede elegir **ver la especificación de YAML** para revisar y descargar el archivo yaml generado por esta configuración de trabajo. Este archivo yaml del trabajo se puede usar para enviar el trabajo desde la CLI (v2). (Consulte [Entrenamiento de modelos [crear trabajos] con la CLI [v2]](how-to-train-cli.md)). [![Visualización de la especificación de yaml](media/how-to-train-with-ui/view-yaml.png)](media/how-to-train-with-ui/view-yaml.png)
[![Especificación de yaml](media/how-to-train-with-ui/yaml-spec.png)](media/how-to-train-with-ui/yaml-spec.png)

Para iniciar el trabajo, seleccione **Crear**. Una vez que se haya creado el trabajo, Azure le mostrará la página de detalles de ejecución, donde puede supervisar y administrar el trabajo de entrenamiento. 

## <a name="next-steps"></a>Pasos siguientes

* [Implementación y puntuación de un modelo de aprendizaje automático con un punto de conexión en línea administrado (versión preliminar)](how-to-deploy-managed-online-endpoints.md)

* [Entrenamiento de modelos (crear trabajos) con la CLI (v2)](how-to-train-cli.md)
