---
title: Configuración del proyecto de etiquetado de imágenes
titleSuffix: Azure Machine Learning
description: Cree un proyecto para etiquetar las imágenes con la herramienta de etiquetado de datos. Para ayudar con la tarea, habilite el etiquetado asistido por ML o el etiquetado con intervención humana.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 09/24/2021
ms.custom: data4ml
ms.openlocfilehash: e600c5d6d880a5aba0a714173897660e306aa853
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129424566"
---
# <a name="create-an-image-labeling-project-and-export-labels"></a>Creación de un proyecto de etiquetado de imágenes y exportación de etiquetas

Aprenda a crear y ejecutar proyectos de etiquetado de datos para etiquetar imágenes en Azure Machine Learning.  Para ayudar con la tarea, use el etiquetado asistido por aprendizaje automático o el etiquetado con intervención humana.

Configure etiquetas para la clasificación, la detección de objetos (rectángulo de selección) o la segmentación de instancias (polígono).

También puede usar la herramienta de etiquetado de datos para [crear un proyecto de etiquetado de texto](how-to-create-text-labeling-projects.md).

## <a name="image-labeling-capabilities"></a>Funcionalidades del etiquetado de imágenes

El etiquetado de datos de Azure Machine Learning es una ubicación central para crear, administrar y supervisar proyectos de etiquetado de datos:

- Coordine los datos, las etiquetas y los miembros del equipo para administrar de forma eficaz las tareas de etiquetado.
- Realiza un seguimiento del progreso y mantiene la cola de tareas de etiquetado incompletas.
- Inicie y detenga el proyecto y controle el progreso de etiquetado.
- Examine los datos etiquetados y expórtelos como un conjunto de datos de Azure Machine Learning.

> [!Important]
> Las imágenes de datos deben ser archivos disponibles en un almacén de datos de blobs de Azure. (Si no tiene un almacén de datos existente, puede cargar los archivos durante la creación del proyecto).

Los datos de imagen pueden ser archivos de cualquiera de estos tipos: ".jpg", ".jpeg", ".png", ".jpe", ".jfif", ".bmp", ".tif", ".tiff", ".dcm" o ".dicom". Cada archivo es un elemento que se va a etiquetar.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-an-image-labeling-project"></a>Creación de un proyecto de etiquetado de imágenes

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. Para crear un proyecto, seleccione **Agregar proyecto**. Asigne un nombre apropiado al proyecto. El nombre del proyecto no se puede reutilizar, ni siquiera si el proyecto se elimina en el futuro.

1. Seleccione **Imagen** para crear un proyecto de etiquetado de imágenes.

    :::image type="content" source="media/how-to-create-labeling-projects/labeling-creation-wizard.png" alt-text="Creación de un proyecto de etiquetado para el etiquetado de imágenes":::

    * Elija un proyecto de tipo **Clasificación de imágenes con varias clases** cuando quiera aplicar una *sola etiqueta* de un conjunto de etiquetas a una imagen.
    * Elija un proyecto de tipo **Clasificación de imágenes con varias etiquetas** cuando quiera aplicar *una o varias* etiquetas de un conjunto de etiquetas a una imagen. Por ejemplo, una fotografía de un perro podría etiquetarse como *perro* y *diurno*.
    * Elija un proyecto de tipo **Identificación del objeto (rectángulo de selección)** cuando quiera asignar una etiqueta y un rectángulo de selección a cada objeto de una imagen.
    * Elija un proyecto de tipo **Segmentación de instancias (polígono)** cuando desee asignar una etiqueta y dibujar un polígono alrededor de cada objeto dentro de una imagen.

1. Seleccione **Siguiente** cuando esté listo para continuar.

## <a name="add-workforce-optional"></a>Agregar recursos (opcional)

[!INCLUDE [outsource](../../includes/machine-learning-data-labeling-outsource.md)]

## <a name="specify-the-data-to-label"></a>Especificación de los datos que se van a etiquetar

Si ya ha creado un conjunto de datos que contiene los datos, selecciónelo en la lista desplegable **Seleccione un conjunto de datos existente**. O bien, seleccione **Crear un conjunto de datos** para usar un almacén de información de Azure existente o cargar archivos locales.

> [!NOTE]
> Un proyecto no puede contener más de 500 000 archivos.  Si el conjunto de datos supera ese número, solo se cargarán los primeros 500 000 archivos.  

### <a name="create-a-dataset-from-an-azure-datastore"></a>Creación de un conjunto de datos a partir de un almacén de datos de Azure

En muchos casos, basta con cargar archivos locales. Sin embargo, [Explorador de Azure Storage](https://azure.microsoft.com/features/storage-explorer/) es una forma más rápida y eficaz de transferir una gran cantidad de datos. Se recomienda usar Explorador de Storage de forma predeterminada para migrar archivos.

Para crear un conjunto de datos a partir de los datos que ya ha almacenado en el almacenamiento de blobs de Azure:

1. Seleccione **Crear un conjunto de datos** > **De almacén de datos**.
1. En **Nombre**, asigne un nombre al conjunto de datos.
1. El **tipo de conjunto de datos** se establece como "archivo", ya que solo los tipos de conjuntos de datos de archivo son compatibles con las imágenes.
1. Seleccione el almacén de datos.
1. Si los datos están en una subcarpeta del almacenamiento de blobs, elija **Examinar** para seleccionar la ruta de acceso.
    * Anexe "/**" a la ruta de acceso para incluir todos los archivos que haya en las subcarpetas de la ruta de acceso seleccionada.
    * Anexe "* */* .*" para incluir todos los datos que haya en el contenedor actual y sus subcarpetas.
1. (Opcional) Proporcione una descripción para el conjunto de datos.
1. Seleccione **Next** (Siguiente).
1. Confirme los detalles. Seleccione **Atrás** para modificar la configuración o **Crear** para crear el conjunto de datos.

### <a name="create-a-dataset-from-uploaded-data"></a>Creación de un conjunto de datos a partir de los datos cargados

Para cargar los datos directamente:

1. Seleccione **Crear un conjunto de datos** > **De archivos locales**.
1. En **Nombre**, asigne un nombre al conjunto de datos.
1. El **tipo de conjunto de datos** se establece como "archivo", ya que solo los tipos de conjuntos de datos de archivo son compatibles con las imágenes.
1. (Opcional) Proporcione una descripción para el conjunto de datos.
1. Seleccione **Siguiente**.
1. (Opcional) Seleccione o cree un almacén de datos. También puede mantener el almacén de blobs predeterminado para realizar la carga ("workspaceblobstore") del área de trabajo de Machine Learning.
1. Seleccione **Examinar** para seleccionar los archivos o carpetas locales que se van a cargar.
1. Seleccione **Next** (Siguiente).
1. Confirme los detalles. Seleccione **Atrás** para modificar la configuración o **Crear** para crear el conjunto de datos.

## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a>Configuración de la actualización incremental

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>Especificación de clases de etiquetas

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-image-labeling-task"></a>Descripción de la tarea de etiquetado de imágenes

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

En el caso de los rectángulos de selección, estas son algunas preguntas importantes:

* ¿Cómo se define el cuadro de límite para esta tarea? ¿Debe estar totalmente en el interior del objeto o en el exterior? ¿Debe recortarse lo más cerca posible o hay algo de margen?
* ¿Qué nivel de cuidado y coherencia espera que los etiquetadores apliquen para definir los rectángulos de selección?
* ¿Cómo se etiqueta el objeto que se muestra parcialmente en la imagen? 
* ¿Cómo se etiqueta el objeto parcialmente cubierto por otro objeto?

>[!NOTE]
> Recuerde que los etiquetadores podrán seleccionar las 9 primeras etiquetas usando las claves numéricas de 1 a 9.

## <a name="use-ml-assisted-data-labeling"></a>Uso del etiquetado de datos asistido por Machine Learning

La página **Etiquetado asistido por ML** permite desencadenar modelos de Machine Learning automáticos para acelerar la tarea de etiquetado. Las imágenes médicas (".dcm") no se incluyen en el etiquetado asistido.

Al principio del proyecto de etiquetado, los elementos se presentan en orden aleatorio para reducir el posible sesgo. Sin embargo, los sesgos presentes en el conjunto de datos se reflejarán en el modelo entrenado. Por ejemplo, si el 80 % de los elementos son de una sola clase, aproximadamente el 80 % de los datos usados para entrenar el modelo serán de esa clase. Este entrenamiento no incluye el aprendizaje activo.

Seleccione *Habilitar el etiquetado con asistencia de ML* y especifique una GPU para habilitar el etiquetado con asistencia, que consta de dos fases:

* Agrupación en clústeres
* Etiquetado previo

El número exacto de datos con etiqueta necesarios para iniciar el etiquetado con asistencia no es un número fijo.  Puede variar significativamente de un proyecto de etiquetado a otro. En algunos proyectos, a veces es posible ver tareas previas de etiquetado o de agrupación en clústeres después de que se hayan etiquetado manualmente 300 elementos. El etiquetado con asistencia de ML usa una técnica llamada *transferencia de aprendizaje*, que usa un modelo entrenado previamente para iniciar el proceso de entrenamiento. Si las clases del conjunto de datos son similares a las del modelo entrenado previamente, puede haber etiquetas previas disponibles después de unos pocos cientos de elementos etiquetados manualmente. Si el conjunto de datos es significativamente diferente de los datos utilizados para entrenar previamente el modelo, puede tardar mucho más.

Como las etiquetas finales se siguen basando en la entrada del etiquetador, a veces esta tecnología se denomina etiquetado *con intervención humana*.

> [!NOTE]
> El etiquetado de datos asistido por ML no es compatible con las cuentas de almacenamiento predeterminadas que están protegidas en una [red virtual](how-to-network-security-overview.md). Debe usar una cuenta de almacenamiento no predeterminada para el etiquetado de datos asistidos mediante ML. La cuenta de almacenamiento no predeterminada se puede proteger en la red virtual.

### <a name="clustering"></a>Agrupación en clústeres

Después de haber enviado un determinado número de etiquetas, el modelo de clasificación de Machine Learning empezará a agrupar elementos similares.  Estas imágenes similares se presentan a los etiquetadores en la misma pantalla, para acelerar el etiquetado manual. La agrupación en clústeres es especialmente útil cuando el etiquetador está viendo una cuadrícula de 4, 6 o 9 imágenes.

Una vez que se ha entrenado un modelo de Machine Learning con los datos etiquetados manualmente, el modelo se trunca a su última capa totalmente conectada. A continuación, las imágenes sin etiquetar pasan a través del modelo truncado en un proceso comúnmente conocido como "incrustación" o "caracterización". El proceso incrusta cada imagen en un espacio de alta dimensión definido por esta capa de modelo. Las imágenes más próximas en el espacio se usan para las tareas de agrupación en clústeres. 

La fase de agrupación en clústeres no aparece para los modelos de detección de objetos o clasificación de texto.

### <a name="prelabeling"></a>Etiquetado previo

Después de haber enviado un número suficiente de etiquetas, se usará un modelo de clasificación para predecir las etiquetas. O bien, se utilizará un modelo de detección de objetos para predecir los rectángulos de selección. Ahora el etiquetador ve las páginas que contienen las etiquetas predichas ya presentes en cada elemento. También se muestran cuadros de predicción para la detección de objetos. La tarea siguiente consiste en revisar estas predicciones y corregir cualquier imagen que se haya etiquetado incorrectamente antes de enviar la página.  

Una vez que se ha entrenado un modelo de Machine Learning en los datos etiquetados manualmente, el modelo se evalúa en un conjunto de prueba de elementos etiquetados manualmente para determinar su precisión en distintos umbrales de confianza. Este proceso de evaluación se usa para determinar un umbral de confianza por encima del cual el modelo es lo suficientemente preciso como para mostrar las etiquetas previas. A continuación, el modelo se evalúa con datos sin etiquetar. Los elementos con predicciones más confiables que este umbral se usan para la etiquetado previo.

## <a name="initialize-the-image-labeling-project"></a>Inicialización del proyecto de etiquetado de imágenes

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>Ejecutar y supervisar el proyecto

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>Panel

En la pestaña **Panel** se muestra el progreso de la tarea de etiquetado.

:::image type="content" source="./media/how-to-create-labeling-projects/labeling-dashboard.png" alt-text="Panel de etiquetado de datos":::

En el gráfico de progreso se muestran cuántos elementos se han etiquetado, omitido, precisan de revisión o están pendientes.  Mantenga el puntero sobre el gráfico para ver el número de elementos de cada sección.

En la sección central se muestra la cola de tareas que todavía se deben asignar. Cuando el etiquetado con asistencia de ML esté desactivado, en esta sección se mostrará el número de tareas manuales que se van a asignar. Cuando el etiquetado con asistencia de ML esté activado, también se mostrará:

* Tareas que contienen elementos agrupados en la cola
* Tareas que contienen elementos etiquetados previamente en la cola

Además, cuando el etiquetado con asistencia de ML está habilitado, se muestra una pequeña barra de progreso cuando se produce la siguiente ejecución de entrenamiento.  En la sección Experimentos se proporcionan vínculos para cada una de las ejecuciones de aprendizaje automático.

* Entrenamiento: Entrena un modelo para predecir las etiquetas.
* Validación: Determina si se usará la predicción de este modelo para etiquetar previamente los elementos. 
* Inferencia: Ejecución de predicción para nuevos elementos.
* Caracterización: Elementos de clúster (solo para proyectos de clasificación de imágenes).

En el lado derecho, se muestra una distribución de las etiquetas de las tareas que se han completado.  Recuerde que en algunos tipos de proyecto, un elemento puede tener varias etiquetas, en cuyo caso, el número total de etiquetas puede ser mayor que el número total de elementos.

### <a name="data-tab"></a>Pestaña Datos

En la pestaña **Datos**, puede ver el conjunto de datos y revisar los datos etiquetados. Desplácese por los datos etiquetados para ver las etiquetas. Si ve datos etiquetados incorrectamente, selecciónelos y elija **Rechazar**; se quitarán las etiquetas y los datos se volverán a colocar en la cola sin etiquetar.

### <a name="details-tab"></a>Pestaña Detalles

Vea los detalles del proyecto.  En esta pestaña, puede:

* Ver los detalles del proyecto y los conjuntos de datos de entrada
* Habilitar la actualización incremental
* Ver detalles del contenedor de almacenamiento usado para almacenar salidas etiquetadas en el proyecto
* Agregar etiquetas al proyecto
* Editar las instrucciones que proporcione a las etiquetas
* Editar los detalles del etiquetado con asistencia de ML, incluido habilitar o deshabilitar

### <a name="access-for-labelers"></a>Acceso para etiquetadores

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>Incorporación de una nueva clase de etiqueta a un proyecto

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>Exportar las etiquetas

Use el botón **Exportar** en la página **Detalles del proyecto** del proyecto de etiquetado. En cualquier momento, puede exportar los datos de etiquetas para realizar experimentos de Machine Learning. 

* Las etiquetas de imagen se pueden exportar como:
    * [Formato COCO](http://cocodataset.org/#format-data). El archivo COCO se crea en el almacén de blobs predeterminado del área de trabajo de Azure Machine Learning, en una carpeta dentro de *Labeling/export/coco*. 
    * Un [conjunto de datos de Azure Machine Learning con etiquetas](how-to-use-labeled-dataset.md). 

Acceda al conjunto de datos exportado de Azure Machine Learning en la sección **Conjuntos de datos** de Machine Learning. La página de detalles del conjunto de datos también proporciona código de ejemplo para acceder a las etiquetas desde Python.

![Conjunto de datos exportado](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Solución de problemas

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]

### <a name="object-detection-troubleshooting"></a>Solución de problemas con la detección de objetos

|Incidencia  |Solución  |
|---------|---------|
|Al presionar la tecla ESC mientras se etiqueta para la detección de objetos, se crea una etiqueta de tamaño cero en la esquina superior izquierda. El envío de etiquetas en este estado produce un error.     |   Haga clic en la cruz junto a la etiqueta para eliminarla.  |

## <a name="next-steps"></a>Pasos siguientes

* [Tutorial: Creación de un proyecto de etiquetado para la clasificación de imágenes](tutorial-labeling.md).
* [Cómo etiquetar imágenes](how-to-label-data.md)
