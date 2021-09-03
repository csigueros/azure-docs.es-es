---
title: Uso de cuadernos con Azure Sentinel para cuestiones de seguridad
description: En este artículo se describe cómo usar cuadernos con las capacidades de búsqueda de Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/24/2021
ms.openlocfilehash: 7b1202648db3aed73ea040fb60610879a6816dd9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737566"
---
# <a name="use-jupyter-notebook-to-hunt-for-security-threats"></a>Uso de Jupyter Notebook para buscar amenazas de seguridad

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

La base de Azure Sentinel es el almacén de datos; combina consultas de alto rendimiento y esquemas dinámicos, y tiene capacidad para escalar a volúmenes de datos masivos. Azure Portal y todas las herramientas de Azure Sentinel emplean una API común para acceder a este almacén de datos. Esta misma API está disponible también para herramientas externas, como los cuadernos de [Jupyter](https://jupyter.org/) y Python. Mientras que muchas tareas comunes pueden llevarse a cabo en el portal, Jupyter amplía las posibilidades de lo que se puede hacer con estos datos. Combina una programación completa con una vasta colección de bibliotecas pensadas para el análisis de datos, la visualización y Machine Learning. Estas características hacen que Jupyter sea una herramienta fascinante para la búsqueda e investigación relacionadas con cuestiones de seguridad.

Por ejemplo, use cuadernos para:

- Realizar análisis que no están integrados en Azure Sentinel, como algunas características de aprendizaje automático de Python.
- Crear visualizaciones de datos que no están integradas en Azure Sentinel, como escalas de tiempo personalizadas y árboles de proceso.
- Integrar orígenes de datos fuera Azure Sentinel, como un conjunto de datos local.

Hemos integrado la experiencia de Jupyter en Azure Portal, lo que facilita el proceso para crear y ejecutar cuadernos para analizar los datos. La biblioteca *Kqlmagic* actúa como el elemento aglutinador que permite realizar consultas desde Azure Sentinel y ejecutarlas directamente en un cuaderno. En las consultas se usa el [lenguaje de consulta Kusto](https://kusto.azurewebsites.net/docs/kusto/query/index.html). Azure Sentinel viene con varios cuadernos, desarrollados por algunos de los analistas de seguridad de Microsoft. Algunos de estos cuadernos se crean para escenarios concretos y se pueden usar tal cual; otros se incluyen como ejemplos para ilustrar las técnicas y características que se pueden copiar o adaptar para usarlas en sus propios cuadernos, y también se pueden importar otros desde la comunidad de Azure Sentinel en GitHub.

La experiencia de Jupyter integrada usa [Azure Notebooks](https://notebooks.azure.com/) para almacenar, compartir y ejecutar cuadernos. Estos cuadernos también se pueden ejecutar localmente si tiene Jupyter y un entorno de Python en el equipo o en otros entornos de JupyterHub, como Azure Databricks.


## <a name="notebook-components"></a>Componentes de Notebook

Los cuadernos constan de dos componentes:
- **La interfaz basada en explorador** donde se escriben y ejecutan las consultas y el código, y donde se muestran los resultados de la ejecución.
- **Un *kernel***, responsable de analizar y ejecutar el código en sí.

El kernel del cuaderno de Azure Sentinel se ejecuta en una máquina virtual (VM) de Azure. Existen varias opciones de licencia para aprovechar máquinas virtuales más eficaces si los cuadernos incluyen modelos de Machine Learning complejos.

Los cuadernos de Azure Sentinel usan muchas bibliotecas de Python conocidas, como *pandas*, *matplotlib* y *bokeh*, entre otras. Existen otros muchos paquetes de Python que se pueden elegir, que cubren áreas como las siguientes:

- Visualizaciones y gráficos
- Procesamiento de datos y análisis
- Estadísticas y computación numérica
- Aprendizaje automático y aprendizaje profundo

También hemos lanzado una serie de herramientas de seguridad de Jupyter de código abierto en un paquete denominado [msticpy](https://github.com/Microsoft/msticpy/). Este paquete se usa en muchos de los cuadernos incluidos en el producto. Las herramientas de Msticpy están diseñadas expresamente para ayudar a crear cuadernos que se puedan usar en búsquedas e investigaciones, y estamos trabajando activamente en nuevas características y mejoras. Para más información, consulte la [documentación de MSTIC Jupyter y Python Security Tools](https://msticpy.readthedocs.io/).

El [repositorio de la comunidad GitHub de Azure Sentinel](https://github.com/Azure/Azure-Sentinel) es la ubicación de los futuros cuadernos de Azure Sentinel creados por Microsoft o procedentes de contribuciones de la comunidad.


## <a name="manage-access-to-azure-sentinel-notebooks"></a>Administración del acceso a los cuadernos de Azure Sentinel

Para usar los cuadernos, primero debe tener los permisos adecuados, dependiendo de su rol de usuario.

Como los cuadernos de Azure Sentinel se ejecutan en la plataforma de [Azure Machine Learning](../machine-learning/overview-what-is-azure-machine-learning.md) (Azure ML), debe tener acceso adecuado al área de trabajo de Azure Sentinel y un [área de trabajo de Azure ML](../machine-learning/concept-workspace.md).

|Permiso  |Descripción  |
|---------|---------|
|**Permisos de Azure Sentinel**     |   Al igual que otros recursos de Azure Sentinel, para acceder a los cuadernos en la hoja Cuadernos de Azure Sentinel, se requiere un rol Lector de Azure Sentinel, Respondedor de Azure Sentinel o Colaborador de Azure Sentinel. <br><br>Para obtener más información, vea [Permisos de Azure Sentinel](roles.md).|
|**Permisos de Azure Machine Learning**     | Un área de trabajo de Azure Machine Learning es un recurso de Azure. Al igual que otros recursos de Azure, cuando se crea un área de trabajo de Azure Machine Learning, viene con roles predeterminados. Puede agregar usuarios al área de trabajo y asignarlos a uno de estos roles integrados. Para más información, vea [Roles predeterminados de Azure Machine Learning](../machine-learning/how-to-assign-roles.md) y [Roles integrados de Azure](../role-based-access-control/built-in-roles.md). <br><br>   **Importante**: El acceso de los roles puede tener un ámbito de varios niveles en Azure. Por ejemplo, un usuario con acceso de propietario a un área de trabajo podría no tener acceso de propietario al grupo de recursos que contiene el área de trabajo. Para obtener más información, consulte [Cómo funciona Azure RBAC](../role-based-access-control/overview.md). <br><br>Si es propietario de un área de trabajo de Azure ML, puede agregar y quitar roles para el área de trabajo y asignar roles a los usuarios. Para más información, consulte:<br>    - [Azure Portal](../role-based-access-control/role-assignments-portal.md)<br>    - [PowerShell](../role-based-access-control/role-assignments-powershell.md)<br>    - [CLI de Azure](../role-based-access-control/role-assignments-cli.md)<br>   - [API de REST](../role-based-access-control/role-assignments-rest.md)<br>    - [Plantillas de Azure Resource Manager](../role-based-access-control/role-assignments-template.md)<br> - [CLI de Azure Machine Learning ](../machine-learning/how-to-assign-roles.md#manage-workspace-access)<br><br>Si los roles integrados no son suficientes, también puede crear roles personalizados. Los roles personalizados pueden tener permisos para leer, escribir, eliminar y procesar recursos de ese área de trabajo. Puede hacer que el rol esté disponible en un nivel de área de trabajo específico, un nivel de grupo de recursos específico o un nivel de suscripción específico. Para más información, vea [Creación de un rol personalizado](../machine-learning/how-to-assign-roles.md#create-custom-role). |
|     |         |


## <a name="create-an-azure-ml-workspace-from-azure-sentinel"></a>Creación de un área de trabajo de Azure ML desde Azure Sentinel

En este procedimiento se describe cómo crear un área de trabajo de Azure ML desde Azure Sentinel para los cuadernos de Azure Sentinel.

**Para crear el área de trabajo**:

1. Desde Azure Portal, vaya a **Azure Sentinel** > **Administración de amenazas** > **Cuadernos** y, después, seleccione **Crear un área de trabajo de AML**.

1. Escriba los detalles siguientes y, después, seleccione **Siguiente**.

    |Campo|Descripción|
    |--|--|
    |**Suscripción**|Seleccione la suscripción de Azure que quiera usar.|
    |**Grupos de recursos**|Use un grupo de recursos existente en su suscripción o escriba un nombre para crear un nuevo grupo de recursos. Un grupo de recursos almacena los recursos relacionados con una solución de Azure.|
    |**Workspace name** (Nombre del área de trabajo)|Escriba un nombre único que identifique el área de trabajo. Los nombres deben ser únicos en el grupo de recursos. Utilice un nombre que sea fácil de recordar y que se diferencie del de las áreas de trabajo creadas por otros.|
    |**Región**|Seleccione la ubicación más cercana a los usuarios y los recursos de datos para crear el área de trabajo.|
    |**Cuenta de almacenamiento**| Una cuenta de almacenamiento se usa como un almacén de datos predeterminado para el área de trabajo. Puede crear un recurso de Azure Storage o seleccionar uno existente en la suscripción.|
    |**KeyVault**| Un almacén de claves se usa para almacenar secretos y otra información confidencial que el área de trabajo necesita. Puede crear un recurso de Azure Key Vault o seleccionar uno existente en la suscripción.|
    |**Application Insights**| El área de trabajo usa Azure Application Insights para almacenar información de supervisión sobre los modelos implementados. Puede crear un recurso de Azure Application Insights o seleccionar uno existente en la suscripción.|
    |**Registro de contenedor**| Un registro de contenedor se usa para registrar imágenes de Docker usadas en el entrenamiento y las implementaciones. Para minimizar los costos, se crea un recurso de Azure Container Registry solo después de compilar la primera imagen. Como alternativa, puede optar por crear el recurso ahora o seleccionar uno existente en la suscripción, o bien seleccione **Ninguno** si no desea usar ningún registro de contenedor.|
    | | |

1. En la pestaña **Redes**, seleccione si desea conectar el área de trabajo mediante un punto de conexión público o mediante un punto de conexión privado que configure. Si el área de trabajo de Azure Sentinel tiene un punto de conexión público, se recomienda usar un punto de conexión público para el área de trabajo de Azure ML para evitar posibles problemas en la comunicación de red. Seleccione **Revisar y crear** cuando haya terminado.

1. En la pestaña **Revisar y crear**, revise la información para comprobar que es correcta y, después, seleccione **Crear** para empezar a implementar el área de trabajo. Por ejemplo:

    :::image type="content" source="media/notebooks/machine-learning-create-last-step.png" alt-text="Revise y cree el área de trabajo de Machine Learning desde Azure Sentinel":::.

    La creación del área de trabajo en la nube puede tardar varios minutos. Durante este tiempo, la página **Información general** del área de trabajo muestra el estado de implementación actual y se actualiza cuando se completa la implementación.

1. Una vez completada la implementación, puede volver a **Cuadernos** de Azure Sentinel e iniciar los cuadernos desde la nueva área de trabajo de Azure ML.

    Si tiene varios cuadernos, asegúrese de seleccionar un área de trabajo de AML predeterminada que se usará al iniciar los cuadernos. Por ejemplo:

    :::image type="content" source="media/notebooks/default-machine-learning.png" alt-text="Seleccione un área de trabajo de AML predeterminada para los cuadernos":::.


## <a name="launch-a-notebook-in-your-azure-ml-workspace"></a>Inicio de un cuaderno en el área de trabajo de Azure ML

Una vez creada el área de trabajo de AML, empiece a iniciar los cuadernos en el área de trabajo de Azure ML, desde Azure Sentinel.

**Para iniciar el cuaderno**:

1. En Azure Portal, vaya a **Azure Sentinel** > **Threat management (Administración de amenazas)**  > **Notebooks** (Cuadernos), donde puede ver los cuadernos que proporciona Azure Sentinel.

    > [!TIP]
    > En la parte superior de la página **Cuadernos**, seleccione **Guías y comentarios** para mostrar más recursos e instrucciones en un panel de la derecha.

1. Seleccione un cuaderno para ver su descripción, los tipos de datos necesarios y los orígenes de datos.

    Cuando haya encontrado el cuaderno que desea usar, seleccione **Guardar cuaderno** para clonarlo en su propia área de trabajo.

    Edite el nombre según sea necesario. Si el cuaderno ya existe en el área de trabajo, tendrá la opción de sobrescribir el cuaderno existente o crear uno.

    :::image type="content" source="media/notebooks/save-notebook.png" alt-text="Guarde un cuaderno para clonarlo en su propia área de trabajo":::.

1. Una vez guardado el cuaderno, el botón **Guardar cuaderno** cambia a **Iniciar cuaderno**. Seleccione **Iniciar cuaderno** para abrirlo en el área de trabajo de AML.

    Por ejemplo:

    :::image type="content" source="media/notebooks/sentinel-notebooks-on-machine-learning.png" alt-text="Inicie el cuaderno en el área de trabajo de AML":::.

1. En la parte superior de la página, seleccione una instancia de **Proceso** que se usará para el servidor de cuadernos.

    Si no tiene una instancia de proceso, [cree una](../machine-learning/how-to-create-manage-compute-instance.md?tabs=#use-the-script-in-the-studio). Si la instancia de proceso está detenida, asegúrese de iniciarla. Para más información, vea [Ejecución de un cuaderno en Estudio de Azure Machine Learning](../machine-learning/how-to-run-jupyter-notebooks.md).

    Solo puede ver y usar las instancias de proceso que usted cree. Los archivos de usuario se almacenan de forma independiente de la máquina virtual y se comparten entre todas las instancias de proceso en el área de trabajo.

    > [!TIP]
    > Si va a crear una instancia de proceso para probar los cuadernos, cree la instancia de proceso con la categoría **De uso general**.

1. Una vez creado e iniciado el servidor de cuadernos, puede empezar a ejecutar las celdas del cuaderno. En cada celda, seleccione el icono **Ejecutar** para ejecutar el código del cuaderno.

    Para más información, vea [Métodos abreviados del modo de comando](../machine-learning/how-to-run-jupyter-notebooks.md).

1. Si necesita reiniciar el kernel del cuaderno y volver a ejecutar las celdas del cuaderno desde el principio, seleccione **Operaciones de kernel** > **Reiniciar kernel**. Por ejemplo:

    :::image type="content" source="media/notebooks/sentinel-notebooks-restart-kernel.png" alt-text="Reinicie un kernel de cuaderno":::.

## <a name="troubleshooting"></a>Solución de problemas

Si tiene problemas con los cuadernos, vea [Solución de problemas con los cuadernos en Azure Machine Learning](../machine-learning/how-to-run-jupyter-notebooks.md#troubleshooting).


## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre el uso de los cuadernos en la búsqueda y la investigación de amenazas mediante la exploración de algunas plantillas de cuadernos, como [**Examen de credenciales en Azure Log Analytics**](https://www.youtube.com/watch?v=OWjXee8o04M) e **Investigación guiada: alertas de proceso**. 

    Busque plantillas de cuaderno en la pestaña Azure Sentinel > **Cuadernos** > **Plantillas**.

- Obtenga más información en el [Repositorio de GitHub de Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks):

    - El directorio [**Sample-Notebooks**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/Sample-Notebooks) incluye cuadernos de ejemplo que se guardan con datos que puede usar para mostrar la salida deseada.

    - El directorio [**HowTos**](https://github.com/Azure/Azure-Sentinel-Notebooks/tree/master/HowTos) incluye cuadernos que describen conceptos como la configuración de la versión predeterminada de Python, la creación de marcadores de Azure Sentinel a partir de un cuaderno, etc.

    Para más información, vea la guía [Introducción a los cuadernos de Azure ML y Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/A%20Getting%20Started%20Guide%20For%20Azure%20Sentinel%20ML%20Notebooks.ipynb).

> [!NOTE]
> Los cuadernos compartidos del [repositorio de GitHub de Azure Sentinel](https://github.com/Azure/Azure-Sentinel-Notebooks) están diseñados como herramientas útiles, ilustraciones y ejemplos de código que puede usar al desarrollar sus propios cuadernos.
>
> Agradecemos cualquier comentario, sugerencias, peticiones de características, contribuciones de cuadernos, informes de errores o mejoras y adiciones a cuadernos ya existentes. Vaya a la [comunidad GitHub de Azure Sentinel](https://github.com/Azure/Azure-Sentinel) para abrir una incidencia, crear una bifurcación y cargar una contribución.
>

Para más información, consulte:

- [Seminario web: Aspectos básicos de los cuadernos de Azure Sentinel](https://www.youtube.com/watch?v=rewdNeX6H94)
- [Tutorial: Introducción a los cuadernos de Azure Sentinel](https://www.youtube.com/results?search_query=azazure+sentinel+notebooks)
- [Búsqueda de amenazas con Azure Sentinel, versión preliminar](hunting.md)
- [Realizar un seguimiento de los datos durante una búsqueda](bookmarks.md)
- [Jupyter, msticpy y Azure Sentinel](https://msticpy.readthedocs.io/en/latest/getting_started/JupyterAndAzureSentinel.html)
- [Tutorial: Edición y ejecución de cuadernos de Jupyter Notebook sin salir de Azure ML Studio](https://www.youtube.com/watch?v=AAj-Fz0uCNk)
