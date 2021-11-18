---
title: Análisis de seguridad a gran escala mediante la integración de Microsoft Sentinel Notebooks y Azure Synapse
description: En este artículo se describe cómo ejecutar consultas de macrodatos en Azure Synapse Analytics con Microsoft Sentinel Notebooks.
services: sentinel
author: batamig
ms.author: bagol
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.topic: how-to
ms.custom: mvc, ignite-fall-2021
ms.date: 11/09/2021
ms.openlocfilehash: 0d298bfc2414c21efa8db869fae7bd9ab5f66632
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132712631"
---
# <a name="large-scale-security-analytics-using-microsoft-sentinel-notebooks-and-azure-synapse-integration-public-preview"></a>Análisis de seguridad a gran escala usando la integración de Microsoft Sentinel Notebooks y Azure Synapse (versión preliminar pública)

> [!IMPORTANT]
> La integración de Microsoft Sentinel Notebooks con Azure Synapse Analytics está actualmente en VERSIÓN PRELIMINAR. En la página [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) se incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en versión beta, versión preliminar o que todavía no se han publicado para su disponibilidad general.
>

La integración de Microsoft Sentinel Notebooks con Azure Synapse Analytics permite realizar análisis de seguridad a gran escala.

Aunque KQL y Log Analytics son las principales herramientas y soluciones para consultar y analizar datos en Microsoft Sentinel, Azure Synapse proporciona características adicionales para el análisis de macrodatos, con acceso al lago de datos integrado y el motor de procesamiento distribuido de Apache Spark.

La integración con Azure Synapse proporciona:

- **Análisis de macrodatos de seguridad**, mediante un grupo de proceso de Apache Spark para Azure Synapse totalmente administrado y de costo optimizado.

- **Acceso rentable a Data Lake** para crear análisis de datos históricos mediante Azure Data Lake Storage Gen2, que es un conjunto de funcionalidades dedicadas al análisis de macrodatos que se basa en Azure Blob Storage.

- **Flexibilidad para integrar orígenes de datos** en flujos de trabajo de operaciones de seguridad de varios orígenes y formatos.

- **PySpark, una API basada en Python** para usar el marco de Spark en combinación con Python, lo que reduce la necesidad de aprender un nuevo lenguaje de programación si ya está familiarizado con Python.

Por ejemplo, puede que quiera usar cuadernos con Azure Synapse para buscar comportamientos anómalos de los registros de firewall de red para detectar una posible señalización de red o para entrenar y compilar modelos de Modelo de Machine Learning sobre los datos recopilados de un área de trabajo de Log Analytics.

## <a name="prerequisites"></a>Prerrequisitos

### <a name="understand-microsoft-sentinel-notebooks"></a>Información sobre Microsoft Sentinel Notebooks

Se recomienda obtener información sobre Microsoft Sentinel Notebooks en general antes de realizar los procedimientos de este artículo.

Para comenzar, vea [Uso de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md) y [Tutorial: Introducción a Jupyter Notebook y MSTICPy en Microsoft Sentinel](notebook-get-started.md).

### <a name="required-roles-and-permissions"></a>Roles y permisos necesarios

Para usar Azure Synapse con Microsoft Sentinel Notebooks, debe tener los siguientes roles y permisos:

|Tipo  |Detalles  |
|---------|---------|
|**Microsoft Sentinel**     |- El rol de **colaborador de Microsoft Sentinel**, con el fin de guardar e iniciar cuadernos desde Microsoft Sentinel.         |
|**Azure Machine Learning**     |- Un rol de **propietario** o **colaborador** de nivel de grupo de recursos, para crear una nueva área de trabajo de Azure Machine Learning si es necesario. <br>- Un rol de **colaborador** en el área de trabajo de Azure Machine Learning donde se ejecuta Microsoft Sentinel Notebooks.    <br><br>Para obtener más información, consulte [Administración del acceso a un área de trabajo de Azure Machine Learning](../machine-learning/how-to-assign-roles.md).     |
|**Azure Synapse Analytics**     | - Un rol de **propietario** de nivel de grupo de recursos, para crear una nueva área de trabajo de Azure Synapse.<br>- Un rol de **colaborador** en el área de trabajo de Azure Synapse para ejecutar las consultas. <br>- Un rol de **colaborador** en el área de trabajo de Azure Synapse Analytics en Synapse Studio.   <br><br>Para obtener más información, consulte [Descripción de los roles necesarios para realizar tareas comunes en Synapse](../synapse-analytics/security/synapse-workspace-understand-what-role-you-need.md).     |
|**Azure Data Lake Storage Gen2**     | - Un rol de **colaborador** de Log Analytics, para exportar datos de un área de trabajo de Log Analytics.<br>- Un rol de colaborador de Azure Blob Storage, para consultar datos desde un lago de datos.  <br><br>Para más información, consulte [Asignación de un rol de Azure](../storage/blobs/assign-azure-role-data-access.md?tabs=portal).|
|     |         |

### <a name="connect-to-azure-ml-and-synapse-workspaces"></a>Conexión a áreas de trabajo de Azure Machine Learning y Synapse

Para usar Microsoft Sentinel Notebooks con Azure Synapse, primero debe conectarse a un área de trabajo de Azure Machine Learning y a otra de Azure Synapse.

**Para crear o conectarse a una área de trabajo de Azure Machine Learning**:

Las áreas de trabajo de Azure Machine Learning son un requisito básico para usar Notebooks en Microsoft Sentinel.

Si todavía no está conectado, consulte [Uso de cuadernos de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md).

**Para crear una nueva área de trabajo de Azure Synapse**:

En la parte superior de la página **Notebooks** de Microsoft Sentinel, seleccione **Configure Azure Synapse** (Configurar Azure Synapse) y, después, seleccione **Create new Azure Synapse workspace** (Crear nueva área de trabajo de Azure Synapse).

> [!NOTE]
> Una instancia de Azure Data Lake Storage Gen2 es una instancia de Data Lake integrada que se incluye con todas las áreas de trabajo de Azure Synapse. Asegúrese de seleccionar o crear una nueva instancia de Data Lake que se encuentre en la misma región que el área de trabajo de Microsoft Sentinel. Esto es necesario para cuando exporte los datos, como se describe más adelante en este artículo.
>

Para obtener más información, consulte la [documentación de Azure Synapse](../synapse-analytics/quickstart-create-workspace.md).


## <a name="configure-your-azure-synapse-analytics-integration"></a>Configuración de la integración de Azure Synapse Analytics

Microsoft Sentinel proporciona el cuaderno integrado **Azure Synapse: configurar Azure ML y Azure Synapse Analytics** para guiarle a través de las configuraciones necesarias para la integración con Azure Synapse.

> [!NOTE]
> La configuración de la integración de Azure Synapse es un procedimiento único y solo necesita ejecutar este cuaderno una vez para el área de trabajo de Microsoft Sentinel.
>

**Para ejecutar el cuaderno Azure Synapse: configurar Azure ML y Azure Synapse Analytics**:

1. En la página **Notebooks** de Microsoft Sentinel, seleccione la pestaña **Templates** (Plantillas) y escriba **Synapse** en la barra de búsqueda para buscar el cuaderno.

1. Busque y seleccione el cuaderno **Azure Synapse: configurar Azure ML y Azure Synapse Analytics** y luego seleccione **Clone notebook template** (Clonar la plantilla de cuaderno) en la parte inferior derecha.

    En el panel **Clone notebook** (Clonar cuaderno), modifique el nombre del cuaderno si es necesario, seleccione el [área de trabajo de Azure Machine Learning](#connect-to-azure-ml-and-synapse-workspaces) y, después, seleccione **Guardar**.

1. Cuando se implemente el cuaderno, seleccione **Launch Notebook** (Iniciar cuaderno) para abrirlo.

    El cuaderno se abre en el área de trabajo de Azure ML, dentro de Microsoft Sentinel. Para obtener más información, consulte [Inicio de un cuaderno en el área de trabajo de Azure ML](notebooks.md#launch-a-notebook-in-your-azure-ml-workspace).

1. Ejecute las celdas de los pasos iniciales del cuaderno para cargar las bibliotecas y funciones de Python necesarias y para autenticarse en los recursos de Azure.

1. Ejecute las celdas del paso 4, **Configuración de un grupo de Azure Synapse Spark** para crear un nuevo [grupo de Azure Synapse Apache Spark](../synapse-analytics/spark/apache-spark-pool-configurations.md) que se usará al ejecutar las consultas de macrodatos.

1. Ejecute las celdas del paso 5, **Configuración del área de trabajo de Azure Machine Learning y servicios vinculados** para asegurarse de que el área de trabajo de Azure Machine Learning puede comunicarse con el área de trabajo de Azure Synapse. Para obtener más información, consulte [Vinculación de las áreas de trabajo de Azure Synapse y Azure Machine Learning, y asociación de grupos de Azure Spark](../machine-learning/how-to-link-synapse-ml-workspaces.md).

1. Ejecute las celdas del paso 6, **Exportación de datos de Azure Log Analytics a Azure Data Lake Storage Gen2** para exportar los datos que quiera usar para las consultas de Azure Log Analytics a Azure Data Lake Storage.

Una vez que los datos estén en Azure Data Lake Storage, está listo para empezar a ejecutar consultas de macrodatos con Azure Synapse. Para obtener más información, consulte [Exportación de datos de Log Analytics en Azure Monitor](../azure-monitor/logs/logs-data-export.md?tabs=portal).

## <a name="hunt-on-historical-data-at-scale"></a>Búsqueda de datos históricos a escala

Microsoft Sentinel proporciona el cuaderno integrado **Azure Synapse: detectar posible señalización de red mediante Apache Spark**. Use este cuaderno como plantilla para un escenario de ejemplo de seguridad real para empezar a trabajar con la búsqueda de macrodatos con Microsoft Sentinel y Azure Synapse.

**Para detectar posibles señalizaciones de red mediante Microsoft Sentinel y Azure Synapse**:

1. En la página **Notebooks** de Microsoft Sentinel, seleccione la pestaña **Templates** (Plantillas) y escriba **Synapse** en la barra de búsqueda para buscar el cuaderno.

1. Busque y seleccione el cuaderno **Azure Synapse: detectar posible señalización de red mediante Apache Spark** y luego seleccione **Clone notebook template** (Clonar la plantilla de cuaderno) en la parte inferior derecha.

    En el panel **Clone notebook** (Clonar cuaderno), modifique el nombre del cuaderno si es necesario, seleccione el [área de trabajo de Azure Machine Learning](#connect-to-azure-ml-and-synapse-workspaces) y, después, seleccione **Guardar**.

1. Cuando se implemente el cuaderno, seleccione **Launch Notebook** (Iniciar cuaderno) para abrirlo.

    El cuaderno se abre en el área de trabajo de Azure ML, dentro de Microsoft Sentinel. Para obtener más información, consulte [Inicio de un cuaderno en el área de trabajo de Azure ML](notebooks.md#launch-a-notebook-in-your-azure-ml-workspace).

1. Ejecute las celdas de los pasos iniciales del cuaderno para cargar las bibliotecas y funciones de Python necesarias y para autenticarse en los recursos de Azure.

1. Cuando llegue a la celda con la etiqueta **Start a Spark Session** (Iniciar una sesión de Spark), ejecute la celda para empezar a usar la sesión de Azure Synapse para usar el grupo de Apache Spark como proceso para la preparación de datos y para realizar tareas de procesamiento de datos en lugar de usar el proceso de Azure Machine Learning.

1. Ejecute las celdas posteriores para configurar y ejecutar las consultas en los datos que ahora están almacenados en la instancia de Azure Data Lake Storage. Por ejemplo, [actualice el período de recuperación](#define-your-data-lookback-period) para incluir datos de un intervalo de tiempo específico.

1. Cuando haya terminado con la consulta, exporte los resultados de Azure Data Lake Storage al área de trabajo de Log Analytics.

    El código siguiente, que se muestra en el paso **Exportar resultados desde ADLS**, guarda los resultados de la consulta como un único archivo JSON. Defina el nombre del directorio y ejecute la celda:

    ```python
    %%synapse
    dir_name = "<dir-name>"  # specify desired directory name
    new_path = adls_path + dir_name
    csl_beacon_pd = csl_beacon_df.coalesce(1).write.format("json").save(new_path)
    ```

1. Después de exportar los datos, puede detener la sesión de Spark. Después de detener la sesión de Spark, se ejecutan consultas posteriores con el proceso predeterminado de Azure Machine Learning indicado en el campo **Proceso** de la parte superior de la página.

    Ejecute la celda en el paso **Detener sesión de Spark**:

    ```python
    %synapse stop
    ```

1. Exporte el archivo JSON con los resultados de la consulta de Azure Data Lake Storage a un sistema de archivos local.

    Use el código de los pasos **Exportar resultados de ADLS al sistema de archivos local**, **Descargar los archivos de ADLS** y **Mostrar los resultados** para guardar el archivo JSON localmente y visualizarlos.

1. Después de guardar los resultados localmente, puede enriquecerlos con datos adicionales y ejecutar visualizaciones. Por ejemplo, el cuaderno **Azure Synapse: detectar posible señalización de red mediante Apache Spark** proporciona pasos adicionales para realizar las siguientes acciones:

    - Enriquecer los resultados con la dirección IP GeoLocation, WhoIs y otros datos de inteligencia sobre amenazas, para tener una imagen más completa de los comportamientos anómalos de la red.
    - Ejecutar visualizaciones de MSTICPy para asignar ubicaciones mientras observa la distribución de conexiones de red remotas u otros eventos.

    Los resultados se pueden volver a escribir en Microsoft Sentinel para una investigación más exhaustiva. Por ejemplo, puede crear incidentes personalizados, listas de control o marcadores de búsqueda a partir de los resultados.

    > [!TIP]
    > Siga estos pasos tal como están para detectar posibles señalizaciones de red o usarlos como plantilla y modificarlos para las necesidades de su organización.
    >

## <a name="manage-your-azure-synapse-session-from-microsoft-sentinel"></a>Administración de la sesión de Azure Synapse desde Microsoft Sentinel

Cuando no esté en una sesión de Azure Synapse, el valor predeterminado de Microsoft Sentinel será el proceso de Azure ML seleccionado en el campo **Compute**  (Proceso) de la parte superior de la página **Notebooks**.

Use el código siguiente, que puede copiar desde aquí o del cuaderno **Azure Synapse: detectar posible señalización de red mediante Apache Spark**, para iniciar y detener la sesión de Azure Synapse.

### <a name="start-an-azure-synapse-session-from-within-microsoft-sentinel"></a>Inicio de una sesión de Azure Synapse desde Microsoft Sentinel

Ejecute el código siguiente:

```python
%synapse start -w $amlworkspace -s $subscription_id -r $resource_group -c $synapse_spark_compute
```

Inicie todas las celdas de código posteriores con `%%synapse` para usar la sesión de Synapse que ha iniciado.

Por ejemplo:

```python
%%synapse

# Primary storage info
account_name = '<storage account name>' # fill in your primary account name
container_name = '<container name>' # fill in your container name
subscription_id = '<subscription if>' # fill in your subscription id
resource_group = '<resource group>' # fill in your resource groups for ADLS
workspace_name = '<Microsoft Sentinel/log analytics workspace name>' # fill in your workspace name
device_vendor = "Fortinet"  # Replace your desired network vendor from commonsecuritylogs

# Datetime and lookback parameters
end_date = "<enter date in the format yyyy-MM-dd e.g.2021-09-17>"  # fill in your input date
lookback_days = 21 # fill in lookback days if you want to run it on historical data. make sure you have historical data available in ADLS
```

### <a name="define-your-data-lookback-period"></a>Definición del período de recuperación de datos

Las consultas de macrodatos de este cuaderno de ejemplo se pueden ejecutar en datos de una fecha predefinida, mediante el parámetro o con el parámetro `end-date`, un intervalo de tiempo más largo.

Por ejemplo:

- Si está interesado en los datos de una fecha específica, especifique el 15 de noviembre de 2021 como fecha actual y la consulta se ejecutará solo en los datos del 15 de noviembre de 2021. 

- Para definir un ámbito de tiempo más largo para la consulta, además de la fecha actual, defina un parámetro lookback. Por ejemplo, si el parámetro `lookback_days` se establece en `21` días y el parámetro `end_date` se establece en `2021-11-17`, la consulta buscará los datos de los 21 días, contando desde el 17 de noviembre de 2021.

En el cuaderno **, Azure Synapse: detectar posible señalización de red mediante Apache Spark**, encontrará este código en el paso de **preparación de datos**.

Por ejemplo:

```python
# Datetime and lookback parameters
end_date = "2021-11-17>"  # fill in your input date
lookback_days = "21" # fill in lookback days if you want to run it on historical data. Make sure you have historical data available in ADLS
```

En el ejemplo anterior, las consultas se ejecutarán con datos entre el 28 de octubre y el 17 de noviembre de 2021.

### <a name="stop-an-azure-synapse-session-from-within-microsoft-sentinel"></a>Detención de una sesión de Azure Synapse desde Microsoft Sentinel

Ejecute el código siguiente:

```python
%synapse stop
```

### <a name="switch-azure-synapse-workspaces-in-microsoft-sentinel"></a>Cambio de áreas de trabajo de Azure Synapse en Microsoft Sentinel

Para administrar o seleccionar un área de trabajo de Synapse diferente a la que ha iniciado sesión actualmente, use uno de los métodos siguientes:

- **Si ya ha creado un servicio vinculado entre azure Machine Learning y la nueva área de trabajo de Azure Machine Learning**:

    1. Escriba el nombre del parámetro `linkservice` en la siguiente celda de código y después, vuelva a ejecutar la celda y las celdas siguientes:

        ```python
        amlworkspace = "<aml workspace name>"  # fill in your AML workspace name
        subscription_id = "<subscription id>" # fill in your subscription id
        resource_group = '<resource group of AML workspace>' # fill in your resource groups for AML workspace
        linkedservice = '<linked service name>' # fill in your linked service created to connect to synapse workspace
        ```

    1. Asegúrese de proporcionar un nombre del grupo de Azure Synapse Spark que se ha registrado y asociado al servicio vinculado:

        ```python
        synapse_spark_compute = "<synapse spark compute>"
        ```

- **Si aún no tiene un servicio vinculado entre las áreas de trabajo de Azure Machine Learning y Azure Synapse**, asegúrese de ejecutar el cuaderno **Azure Synapse: configurar Azure ML y Azure Synapse Analytics** para configurar el servicio vinculado antes de ejecutar el cuaderno **Azure Synapse: detectar posible señalización de red mediante Apache Spark**.

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

- [Uso de cuadernos de Jupyter para buscar amenazas de seguridad](notebooks.md)
- [Tutorial: Introducción a Jupyter Notebook y MSTICPy en Microsoft Sentinel](notebook-get-started.md)
- [Vinculación de las áreas de trabajo de Azure Synapse y Azure Machine Learning, y asociación de grupos de Azure Spark (versión preliminar)](../machine-learning/how-to-link-synapse-ml-workspaces.md)