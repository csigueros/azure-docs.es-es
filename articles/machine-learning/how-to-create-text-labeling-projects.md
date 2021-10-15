---
title: Configuración de un proyecto de etiquetado de texto
titleSuffix: Azure Machine Learning
description: Cree un proyecto para etiquetar texto mediante la herramienta de etiquetado de datos. Especifique una o varias etiquetas que se aplicarán a cada fragmento de texto.
author: sdgilley
ms.author: sgilley
ms.service: machine-learning
ms.subservice: mldata
ms.topic: how-to
ms.date: 09/24/2021
ms.custom: data4ml
ms.openlocfilehash: e4d227cf215ab5d37af8a25e6e44e811f739f25b
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129423716"
---
# <a name="create-a-text-labeling-project-and-export-labels-preview"></a>Creación de un proyecto de etiquetado de texto y exportación de etiquetas (versión preliminar)

Aprenda a crear y ejecutar proyectos de etiquetado de datos para etiquetar datos de texto en Azure Machine Learning.  Especifique una o varias etiquetas que se aplicarán a cada fragmento de texto.

También puede usar la herramienta de etiquetado de datos para [crear un proyecto de etiquetado de imágenes](how-to-create-image-labeling-projects.md).

> [!IMPORTANT]
> El etiquetado de texto se encuentra actualmente en versión preliminar pública.
> Se ofrece la versión preliminar sin Acuerdo de Nivel de Servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas.
> Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="text-labeling-capabilities"></a>Funcionalidades del etiquetado de texto

El etiquetado de datos de Azure Machine Learning es una ubicación central para crear, administrar y supervisar proyectos de etiquetado de datos:

- Coordine los datos, las etiquetas y los miembros del equipo para administrar de forma eficaz las tareas de etiquetado. 
- Realiza un seguimiento del progreso y mantiene la cola de tareas de etiquetado incompletas.
- Inicie y detenga el proyecto y controle el progreso de etiquetado.
- Examine los datos etiquetados y expórtelos como un conjunto de datos de Azure Machine Learning.

> [!Important]
> Los datos de texto deben estar disponibles en un almacén de datos de blobs de Azure. (Si no tiene un almacén de datos existente, puede cargar los archivos durante la creación del proyecto).

Los datos de texto pueden ser archivos ".txt" o ".csv".

* En el caso de los archivos ".txt", cada archivo representa un elemento que se va a etiquetar.
* Para los archivos ".csv", cada fila representa un elemento presentado al etiquetador.  Puede mostrar una o varias columnas para usarlas al etiquetar esa fila.

## <a name="prerequisites"></a>Requisitos previos

[!INCLUDE [prerequisites](../../includes/machine-learning-data-labeling-prerequisites.md)]

## <a name="create-a-text-labeling-project"></a>Creación de un proyecto de etiquetado de texto

[!INCLUDE [start](../../includes/machine-learning-data-labeling-start.md)]

1. Para crear un proyecto, seleccione **Agregar proyecto**. Asigne un nombre apropiado al proyecto. El nombre del proyecto no se puede reutilizar, ni siquiera si el proyecto se elimina en el futuro.

1. Seleccione **Texto** para crear un proyecto de etiquetado de texto.

    :::image type="content" source="media/how-to-create-labeling-projects/text-labeling-creation-wizard.png" alt-text="Creación de un proyecto de etiquetado para el etiquetado de texto":::

    * Elija **Clasificación de texto con varias clases (versión preliminar)** cuando quiera aplicar una *sola etiqueta* de un conjunto de etiquetas a cada fragmento de texto.
    * Elija **Clasificación de texto con varias etiquetas (versión preliminar)** cuando quiera aplicar *una o varias* etiquetas de un conjunto de etiquetas a un fragmento de texto.

1. Seleccione **Siguiente** cuando esté listo para continuar.

## <a name="add-workforce-optional"></a>Incorporación de recursos (opcional)

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
1. Elija el **Tipo de conjunto de datos**:
    * Seleccione **Tabular** si usa un archivo .csv, donde cada fila contiene una respuesta.
    * Seleccione **Archivo** si usa archivos .txt para cada respuesta.
1. (Opcional) Proporcione una descripción para el conjunto de datos.
1. Seleccione **Siguiente**.
1. Seleccione el almacén de datos.
1. Si los datos están en una subcarpeta del almacenamiento de blobs, elija **Examinar** para seleccionar la ruta de acceso.
    * Anexe "/**" a la ruta de acceso para incluir todos los archivos que haya en las subcarpetas de la ruta de acceso seleccionada.
    * Anexe "* */* .*" para incluir todos los datos que haya en el contenedor actual y sus subcarpetas.
1. Seleccione **Next** (Siguiente).
1. Confirme los detalles. Seleccione **Atrás** para modificar la configuración o **Crear** para crear el conjunto de datos.

### <a name="create-a-dataset-from-uploaded-data"></a>Creación de un conjunto de datos a partir de los datos cargados

Para cargar los datos directamente:

1. Seleccione **Crear un conjunto de datos** > **De archivos locales**.
1. En **Nombre**, asigne un nombre al conjunto de datos.
1. Elija el **tipo de conjunto de datos**.
    * Seleccione **Tabular** si usa un archivo .csv, donde cada fila es una respuesta.
    * Seleccione **Archivo** si usa archivos .txt para cada respuesta.
1. (Opcional) Proporcione una descripción para el conjunto de datos.
1. Seleccione **Siguiente**.
1. (Opcional) Seleccione o cree un almacén de datos. También puede mantener el almacén de blobs predeterminado para realizar la carga ("workspaceblobstore") del área de trabajo de Machine Learning.
1. Seleccione **Cargar** para seleccionar las carpetas o los archivos locales que se van a cargar.
1. Seleccione **Siguiente**.
1. Si se cargan archivos .csv:
    * Confirme la configuración, obtenga una vista previa y seleccione **Siguiente**.
    * Incluya todas las columnas de texto que quiera que vea el etiquetador al clasificar esa fila.
    * Seleccione **Next** (Siguiente).
1.  Confirme los detalles. Seleccione **Atrás** para modificar la configuración o **Crear** para crear el conjunto de datos.


## <a name="configure-incremental-refresh"></a><a name="incremental-refresh"> </a>Configuración de la actualización incremental

[!INCLUDE [refresh](../../includes/machine-learning-data-labeling-refresh.md)]

## <a name="specify-label-classes"></a>Especificación de clases de etiquetas

[!INCLUDE [classes](../../includes/machine-learning-data-labeling-classes.md)]

## <a name="describe-the-text-labeling-task"></a>Descripción de la tarea de etiquetado de texto

[!INCLUDE [describe](../../includes/machine-learning-data-labeling-describe.md)]

>[!NOTE]
> Recuerde que los etiquetadores podrán seleccionar las 9 primeras etiquetas usando las claves numéricas de 1 a 9.


## <a name="initialize-the-text-labeling-project"></a>Inicialización del proyecto de etiquetado de texto

[!INCLUDE [initialize](../../includes/machine-learning-data-labeling-initialize.md)]

## <a name="run-and-monitor-the-project"></a>Ejecutar y supervisar el proyecto

[!INCLUDE [run](../../includes/machine-learning-data-labeling-run.md)]

### <a name="dashboard"></a>Panel


En la pestaña **Panel** se muestra el progreso de la tarea de etiquetado.

:::image type="content" source="./media/how-to-create-labeling-projects/text-labeling-dashboard.png" alt-text="Panel de etiquetado de datos de texto":::


En el gráfico de progreso se muestran cuántos elementos se han etiquetado, omitido, precisan de revisión o están pendientes.  Mantenga el puntero sobre el gráfico para ver el número de elementos de cada sección.

En la sección central se muestra la cola de tareas que todavía se deben asignar. 


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

### <a name="access-for-labelers"></a>Acceso para etiquetadores

[!INCLUDE [access](../../includes/machine-learning-data-labeling-access.md)]

## <a name="add-new-label-class-to-a-project"></a>Incorporación de una nueva clase de etiqueta a un proyecto

[!INCLUDE [add-label](../../includes/machine-learning-data-labeling-add-label.md)]

## <a name="export-the-labels"></a>Exportar las etiquetas
 
Use el botón **Exportar** en la página **Detalles del proyecto** del proyecto de etiquetado. En cualquier momento, puede exportar los datos de etiquetas para realizar experimentos de Machine Learning.

Puede exportar lo siguiente:

* Un archivo CSV. El archivo CSV se crea en el almacén de blobs predeterminado del área de trabajo de Azure Machine Learning, en una carpeta dentro de *Labeling/export/csv*. 
* Un [conjunto de datos de Azure Machine Learning con etiquetas](how-to-use-labeled-dataset.md). 

Acceda al conjunto de datos exportado de Azure Machine Learning en la sección **Conjuntos de datos** de Machine Learning. La página de detalles del conjunto de datos también proporciona código de ejemplo para acceder a las etiquetas desde Python.

![Conjunto de datos exportado](./media/how-to-create-labeling-projects/exported-dataset.png)

## <a name="troubleshooting"></a>Solución de problemas

[!INCLUDE [troubleshooting](../../includes/machine-learning-data-labeling-troubleshooting.md)]


## <a name="next-steps"></a>Pasos siguientes

* [Etiquetado de texto](how-to-label-data.md#label-text)

