---
title: Acerca de los entornos de Azure Machine Learning
titleSuffix: Azure Machine Learning
description: Obtenga información sobre las ventajas de los entornos de aprendizaje automático, que permiten definiciones de dependencias de aprendizaje automático reproducibles, auditables y portátiles para varios destinos de proceso.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: larryfr
author: Blackmist
ms.date: 09/23/2021
ms.openlocfilehash: e798893b28eaaa6aabb1d3f3623aea60df7bc2c6
ms.sourcegitcommit: 96deccc7988fca3218378a92b3ab685a5123fb73
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131576156"
---
# <a name="what-are-azure-machine-learning-environments"></a>¿Qué son los entornos de Azure Machine Learning?

Los entornos de Azure Machine Learning son una encapsulación del entorno en el que se produce el entrenamiento de aprendizaje automático. Especifican los paquetes, las variables de entorno y la configuración de software de Python en torno a los scripts de entrenamiento y puntuación. También especifican los entornos de ejecución (Python, Spark o Docker). Los entornos son entidades administradas y con control de versiones en el área de trabajo de Machine Learning que permiten flujos de trabajo de aprendizaje automático reproducibles, auditables y portátiles en distintos destinos de proceso.

Puede usar un objeto de `Environment` en el proceso local para:
* Desarrollar el script de aprendizaje.
* Reutilizar el mismo entorno en el Proceso de Azure Machine Learning para el entrenamiento del modelo a escala.
* Implementar el modelo con ese mismo entorno.
* Vuelva a revisar el entorno en el que se entrenó un modelo existente.

En el diagrama siguiente se ilustra cómo puede usar un solo objeto `Environment` en la configuración de ejecución (para el entrenamiento) y en la configuración de inferencia e implementación (para las implementaciones de servicios web).

![Diagrama del entorno del flujo de trabajo de aprendizaje automático](./media/concept-environments/ml-environment.png)

El entorno, el destino de proceso y el script de entrenamiento constituyen todos juntos la configuración de ejecución: la especificación completa de una ejecución de entrenamiento.

## <a name="types-of-environments"></a>Tipos de entornos

Los entornos se pueden dividir a grandes rasgos en tres categorías: *mantenidos*, *administrados por el usuario* y *administrados por el sistema*.

Los entornos mantenidos los proporciona Azure Machine Learning y están disponibles en el área de trabajo de forma predeterminada. Están concebidos para usarse tal cual; contienen colecciones de paquetes y configuraciones de Python que le ayudarán a empezar a usar diferentes marcos de aprendizaje automático. Estos entornos creados previamente también permiten un tiempo de implementación más rápido. Para obtener una lista completa, consulte el [artículo sobre los entornos mantenidos](resource-curated-environments.md).

Para los entornos administrados por usuarios, es responsable de configurar el entorno e instalar todos los paquetes que necesita el script de entrenamiento en el destino de proceso. Asegúrese también de incluir las dependencias necesarias para realizar la implementación del modelo.

Use entornos administrados por el sistema cuando quiera que [Conda](https://conda.io/docs/) administre el entorno de Python. Un nuevo entorno de Conda se materializa a partir de la especificación de Conda sobre una imagen base de Docker.

## <a name="create-and-manage-environments"></a>Creación y administración de entornos

Puede crear entornos desde clientes como el SDK de Python de AzureML, la CLI de Azure Machine Learning, la página Entornos de Azure Machine Learning Studio y la [extensión de VS Code](how-to-manage-resources-vscode.md#create-environment). Cada cliente permite personalizar la imagen base, Dockerfile y la capa de Python si es necesario.

Para ver ejemplos de código específicos, consulte la sección "Creación de un entorno" de [Uso de los entornos](how-to-use-environments.md#create-an-environment). 

Los entornos también se administran fácilmente a través del área de trabajo, lo que le permite:

* Registrar de entornos.
* Capturar entornos del área de trabajo que se usarán para el entrenamiento o la implementación.
* Crear una nueva instancia de un entorno editando una existente.
* Ver los cambios en los entornos en el tiempo, lo que garantiza la reproducibilidad.
* Compilar imágenes de Docker de forma automática desde los entornos.

Los entornos "anónimos" se registran de forma automática en el área de trabajo cuando se envía un experimento. No se mostrarán en la lista, pero se pueden recuperar por versión.

Para ver ejemplos de código, consulte la sección "Administración de entornos" de [Uso de entornos](how-to-use-environments.md#manage-environments).

## <a name="environment-building-caching-and-reuse"></a>Compilación, almacenamiento en caché y reutilización de entornos

Azure Machine Learning compila definiciones de entorno en imágenes de Docker y entornos de Conda. También almacena en caché los entornos para que se puedan volver a usar en las ejecuciones de entrenamiento e implementaciones de punto de conexión de servicio posteriores. Para ejecutar un script de entrenamiento de forma remota, es necesario crear una imagen de Docker, mientras que una ejecución local puede usar un entorno de Conda directamente. 

### <a name="submitting-a-run-using-an-environment"></a>Envío de una ejecución mediante un entorno

La primera vez que se envía una ejecución remota mediante un entorno, Azure Machine Learning Service invoca una [tarea de compilación de ACR](../container-registry/container-registry-tasks-overview.md) en la instancia de Azure Container Registry (ACR) asociada al área de trabajo. Después, la imagen de Docker compilada se almacena en caché en la instancia de ACR del área de trabajo. Los entornos mantenidos están respaldados por imágenes de Docker que se almacenan en caché en la instancia de ACR global. Al inicio de la ejecución, el destino de proceso recupera la imagen de la instancia de ACR pertinente.

En el caso de ejecuciones locales, se crea un entorno de Docker o Conda en función de la definición del entorno. Luego, los scripts se ejecutan en el proceso de destino: un entorno de tiempo de ejecución local o un motor de Docker local.

### <a name="building-environments-as-docker-images"></a>Compilación de entornos como imágenes de Docker

Si la imagen de una definición de entorno en particular todavía no existe en la instancia de ACR del área de trabajo, se creará una nueva imagen. La compilación de la imagen consta de dos pasos:

 1. Descarga de una imagen base y ejecución de todos los pasos de Docker;
 2. Creación de un entorno de Conda según las dependencias de Conda que se hayan especificado en la definición de entorno.

El segundo paso se omite si se especifican [dependencias administradas por el usuario](/python/api/azureml-core/azureml.core.environment.pythonsection). En este caso, es responsable de instalar todos los paquetes de Python, ya sea al incluirlos en la imagen base o al especificar pasos de Docker personalizados en el primer paso. También es responsable de especificar la ubicación correcta para el archivo ejecutable de Python. También es posible usar una [imagen base de Docker personalizada](./how-to-deploy-custom-container.md).

### <a name="image-caching-and-reuse"></a>Almacenamiento en caché y reutilización de imágenes

Si usa la misma definición de entorno para otra ejecución, Machine Learning vuelve a usar la imagen almacenada en caché de la instancia de ACR del área de trabajo para ahorrar tiempo.

Para ver los detalles de una imagen almacenada en caché, consulte la página Entornos en el Estudio de Azure Machine Learning o use el método [`Environment.get_image_details`](/python/api/azureml-core/azureml.core.environment.environment#get-image-details-workspace-).

Para determinar si se debe reutilizar una imagen almacenada en caché o si debe crear una nueva, AzureML calcula [un valor hash](https://en.wikipedia.org/wiki/Hash_table) a partir de la definición de entorno y lo compara con los valores hash de los entornos existentes. El hash se basa en la definición del entorno:
 
 * Base image
 * Pasos para la personalización de una imagen de Docker
 * Paquetes de Python
 * Paquetes de Spark

El hash no se ve afectado por el nombre o la versión del entorno. Si cambia el nombre del entorno o crea uno con la misma configuración y paquetes que otro entorno, el valor hash seguirá siendo el mismo. Sin embargo, los cambios en la definición del entorno, como agregar o eliminar un paquete de Python o cambiar la versión de un paquete, harán que cambie el valor hash resultante. Cambiar el orden de las dependencias o los canales en un entorno también cambiará el hash y requerirá una nueva compilación de imagen. De forma similar, cualquier cambio en un entorno mantenido dará lugar a la creación de un nuevo entorno "no mantenido". 

> [!NOTE]
> No podrá enviar ningún cambio local a un entorno seleccionado sin cambiar el nombre del entorno. Los prefijos "AzureML-" y "Microsoft" se reservan exclusivamente para entornos seleccionados y se producirá un error en el envío del trabajo si el nombre comienza por cualquiera de ellos.

El valor de hash calculado del entorno se compara con el de la instancia de ACR global y del área de trabajo o con el del destino de proceso si se trata de ejecuciones locales. Si se encuentra una coincidencia, se extrae y se usa la imagen almacenada en caché; de lo contrario, se desencadena una compilación de la imagen.

En el siguiente diagrama se muestran tres definiciones de entorno. Dos de ellos tienen nombres y versiones diferentes, pero imágenes base idénticas y paquetes de Python, lo que da como resultado el mismo hash y la misma imagen almacenada en caché correspondiente. El tercer entorno tiene diferentes paquetes y versiones de Python y, por lo tanto, corresponde a una imagen almacenada en caché y un hash distintos.

![Diagrama del almacenamiento en caché del entorno e imágenes de Docker](./media/concept-environments/environment-caching.png)

Las imágenes almacenadas en caché en el área de trabajo de ACR tendrán nombres como `azureml/azureml_e9607b2514b066c851012848913ba19f`, y el hash aparecerá al final.

>[!IMPORTANT]
> * Si se crea un entorno con una dependencia de paquete desanclada (por ejemplo, `numpy`), dicho entorno usa la versión de paquete que estuviera *disponible cuando se creó el entorno*. Además, cualquier entorno futuro que use una definición coincidente usará la versión original. 
>
>   Para actualizar el paquete, especifique un número de versión para forzar la recompilación de la imagen. Por ejemplo, cambie `numpy` por `numpy==1.18.1`. Se instalarán nuevas dependencias, incluidas las anidadas, lo que podría estropear un escenario que anteriormente funcionaba.
>
> * Usar una imagen base desanclada como `mcr.microsoft.com/azureml/openmpi3.1.2-ubuntu18.04` en la definición de entorno tiene como resultado que la imagen vuelve a generarse cada vez que la etiqueta `latest` se actualiza. Esto permite que la imagen reciba las revisiones y actualizaciones del sistema más recientes.

> [!WARNING]
>  El método [`Environment.build`](/python/api/azureml-core/azureml.core.environment.environment#build-workspace--image-build-compute-none-) recompilará la imagen almacenada en caché, con el posible efecto secundario de que se actualicen los paquetes sin especificar y que todas las definiciones de entorno correspondientes a esa imagen almacenada en caché ya no puedan reproducirse.

### <a name="image-patching"></a>Aplicación de revisiones de imágenes

Microsoft es responsable de aplicar revisiones a las imágenes base en busca de vulnerabilidades de seguridad conocidas. Las actualizaciones de las imágenes admitidas se lanzan cada dos semanas, con el compromiso de que no haya vulnerabilidades sin revisiones anteriores a 30 días en la versión más reciente de la imagen. Las imágenes con revisión se presentan con una nueva etiqueta inmutable y la etiqueta `:latest` se actualiza a la versión más reciente de la imagen con revisión. 

Si proporciona sus propias imágenes, recuerde que es responsable de actualizarlas.

Para obtener más información acerca de las imágenes base, consulte los vínculos siguientes:

* Repositorio de GitHub de [Imágenes base de Azure Machine Learning](https://github.com/Azure/AzureML-Containers).
* [Entrenamiento de un modelo mediante una imagen personalizada](how-to-train-with-custom-image.md).
* [Implementación de un modelo de TensorFlow mediante un contenedor personalizado](how-to-deploy-custom-container.md).

## <a name="next-steps"></a>Pasos siguientes

* Aprenda a [crear y usar entornos](how-to-use-environments.md) en Azure Machine Learning.
* Vea la documentación de referencia del SDK de Python para la [clase environment](/python/api/azureml-core/azureml.core.environment%28class%29).
