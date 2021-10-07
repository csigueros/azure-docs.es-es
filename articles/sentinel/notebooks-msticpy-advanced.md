---
title: Configuraciones avanzadas para cuadernos de Jupyter y MSTICPy en Azure Sentinel | Microsoft Docs
description: Obtenga información sobre las configuraciones avanzadas disponibles para cuadernos de Jupyter con MSTICPy al trabajar en Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.topic: how-to
ms.date: 09/12/2021
ms.openlocfilehash: b3a33bc1f72af20a28bffbd96df73b95d31e3cca
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128594598"
---
# <a name="advanced-configurations-for-jupyter-notebooks-and-msticpy-in-azure-sentinel"></a>Configuraciones avanzadas para cuadernos de Jupyter y MSTICPy en Azure Sentinel

En este artículo se describen las configuraciones avanzadas para trabajar con cuadernos de Jupyter y MSTICPy en Azure Sentinel.

Para obtener más información, vea [Uso de Jupyter Notebook para buscar amenazas de seguridad](notebooks.md) y [Tutorial: Introducción a los cuadernos de Jupyter y MSTICPy en Azure Sentinel](notebook-get-started.md).

## <a name="prerequisites"></a>Requisitos previos

Este artículo es una continuación del [Tutorial: Introducción a los cuadernos de Jupyter y MSTICPy en Azure Sentinel](notebook-get-started.md). Se recomienda realizar el tutorial antes de continuar con los procedimientos avanzados que se describen a continuación.

## <a name="specify-authentication-parameters-for-azure-and-azure-sentinel-apis"></a>Especificación de parámetros de autenticación para API de Azure y Azure Sentinel

En este procedimiento se describe cómo configurar parámetros de autenticación para Azure Sentinel y otros recursos de API de Azure en el archivo **msticpyconfig.yaml**.

**Para agregar la autenticación de Azure y la configuración de API de Azure Sentinel en el editor de configuraciones de MSTICPy**:

1. Continúe a la celda siguiente, con el código siguiente y ejecútelo:

    ```python
    mpedit.set_tab("Data Providers")
    mpedit
    ```

1. En la pestaña **Data Providers** (Proveedores de datos), seleccione **AzureCLI** > **Add** (CLI de Azure > Agregar).

1. Seleccione los métodos de autenticación que se van a usar:

    - Aunque puede usar un conjunto de métodos diferentes a los [predeterminados de Azure](notebook-get-started.md#specify-the-azure-cloud-and-azure-authentication-methods), este uso no es una configuración típica.
    - A menos que quiera usar la autenticación **env** (variable de entorno), deje vacíos los campos **clientId**, **tenantiId** y **clientSecret**.
    - Aunque no se recomienda, MSTICPy también admite el uso de secretos e id. de aplicación cliente para la autenticación. En esos casos, defina los campos **clientId**, **tenantId** y **clientSecret** directamente en la pestaña **Data Providers** (Proveedores de datos).

1. Seleccione **Save File** (Guardar archivo) para guardar los cambios.

## <a name="define-autoloading-query-providers"></a>Definición de proveedores de consultas de carga automática

Defina los proveedores de consultas que quiera que MSTICPy cargue automáticamente al ejecutar la función `nbinit.init_notebook`.

Si crea cuadernos con frecuencia, la carga automática de proveedores de consultas puede ahorrarle tiempo, ya que se asegura de que los proveedores necesarios se cargan antes que otros componentes, como las funciones dinámicas y los notebooklet.

**Para agregar proveedores de consultas de carga automática**:

1. Continúe a la celda siguiente, con el código siguiente y ejecútelo:

    ```python
    mpedit.set_tab("Autoload QueryProvs")
    mpedit
    ```

1. En la pestaña **Autoload QueryProv** (Carga automática del proveedor de consultas):

   - Para proveedores de Azure Sentinel, especifique el nombre del proveedor y el nombre del área de trabajo a la que quiera conectarse.
   - Para otros proveedores de consultas, especifique solo el nombre del proveedor.

   Cada proveedor también tiene los siguientes valores opcionales:

   - **Conexión automática:** esta opción se define como **True** de forma predeterminada y MSTICPy intenta autenticarse en el proveedor inmediatamente después de la carga. MSTICPy supone que ha configurado las credenciales para el proveedor en la configuración.

   - **Alias:** cuando MSTICPy carga un proveedor, le asigna un nombre de variable de Python. De forma predeterminada, el nombre de la variable es **qryworkspace_name** para los proveedores de Azure Sentinel y **qryprovider_name** para otros proveedores.

        Por ejemplo, si carga un proveedor de consultas para el área de trabajo *ContosoSOC*, se creará en el entorno del cuaderno con el nombre `qry_ContosoSOC`. Agregue un alias si quiere usar algo más corto o más fácil de escribir y recordar. El nombre de la variable de proveedor será `qry_<alias>`, donde `<alias>` se reemplaza por el nombre de alias que haya proporcionado.

        Los proveedores que cargue mediante este mecanismo también se agregan al atributo `current_providers` de MSTICPy, que se usa, por ejemplo, en el código siguiente:

        ```python
        import msticpy
        msticpy.current_providers
        ```

1. Seleccione **Save Settings** (Guardar configuración) para guardar los cambios.

## <a name="define-autoloaded-msticpy-components"></a>Definición de componentes de MSTICPy cargados automáticamente

En este procedimiento se describe cómo definir otros componentes que MSTICPy carga automáticamente al ejecutar la función `nbinit.init_notebook`.

Los componentes admitidos incluyen, en el orden siguiente:

1. **TILookup:** la [biblioteca de proveedores de TI](notebook-get-started.md#add-threat-intelligence-provider-settings)
1. **GeoIP:** el [proveedor de GeoIP](notebook-get-started.md#add-geoip-provider-settings) que quiera usar
1. **AzureData:** módulo que se usa para consultar detalles sobre los [recursos de Azure](#specify-authentication-parameters-for-azure-and-azure-sentinel-apis)
1. **AzureSentinelAPI:** módulo que se usa para consultar la [API de Azure Sentinel](#specify-authentication-parameters-for-azure-and-azure-sentinel-apis)
1. **Notebooklets:** notebooklet del [paquete msticnb](https://msticnb.readthedocs.io/en/latest/)
1. **Pivot:** funciones dinámicas

> [!NOTE]
> Los componentes se cargan en este orden porque el componente Pivot necesita que se carguen los proveedores de consultas y el resto para buscar las funciones dinámicas que asocia a las entidades. Para obtener más información, vea la [documentación de MSTICPy](https://msticpy.readthedocs.io/en/latest/data_analysis/PivotFunctions.html).
>

**Para definir componentes de MSTICPy cargados automáticamente**:

1. Continúe a la celda siguiente, con el código siguiente y ejecútelo:

   ```python
   mpedit.set_tab("Autoload Components")
   mpedit
   ```

1. En la pestaña **Autoload Components** (Carga automática de componentes), defina los valores de parámetro necesarios. Por ejemplo:

   - **GeoIpLookup**.  Escriba el nombre del proveedor de GeoIP que quiera usar, ya sea *GeoLiteLookup* o *IPStack*.  Para obtener más información, vea [Adición de valores de proveedor de GeoIP](notebook-get-started.md#add-geoip-provider-settings).

   - **Componentes AzureData y AzureSentinelAPI**.  Defina los valores siguientes:

      - **auth_methods:** invalide la configuración predeterminada de AzureCLI y conéctese mediante los métodos seleccionados.
      - **Conexión automática:** se establece en false para realizar la carga sin conectarse.

      Para obtener más información, vea [Especificación de parámetros de autenticación para API de Azure y Azure Sentinel](#specify-authentication-parameters-for-azure-and-azure-sentinel-apis).

   - **Notebooklets**. El componente **Notebooklets** tiene un único bloque de parámetros: **AzureSentinel**.

      Especifique el área de trabajo de Azure Sentinel mediante la sintaxis siguiente: `workspace:\<workspace name>`. El nombre del área de trabajo debe ser una de las definidas en la pestaña **Azure Sentinel**.

      Si quiere agregar más parámetros para enviar a la función `notebooklets init`, debe especificarlos como pares clave-valor, separados por nuevas líneas. Por ejemplo:

      ```python
      workspace:<workspace name>
      providers=["LocalData","geolitelookup"]
      ```

      Para obtener más información, vea la [documentación de MSTICNB (MSTIC Notebooklets)](https://msticnb.readthedocs.io/en/latest/msticnb.html#msticnb.data_providers.init).

    Algunos componentes, como **TILookup** y **Pivot**, no necesitan ningún parámetro.

1. Seleccione **Save Settings** (Guardar configuración) para guardar los cambios.


## <a name="switch-between-python-36-and-38-kernels"></a>Cambio entre los kernels de Python 3.6 y 3.8

Si va a cambiar entre los kernels de Python 3.65 y 3.8, es posible que MSTICPy y otros paquetes no se instalen según lo previsto.

Esto puede ocurrir cuando el comando `!pip install pkg` se instala correctamente en el primer entorno, pero después no lo hace en el segundo. Esto crea una situación en la que el segundo entorno no puede importar ni usar el paquete.

Se recomienda no usar `!pip install...` para instalar paquetes en cuadernos de Azure Machine Learning. En su lugar, use una de las opciones siguientes:

- **Use la magic de línea %pip dentro de un cuaderno**. Ejecute:

  ```python

  %pip install --upgrade msticpy
  ```

- **Instalación desde un terminal**:

  1. Abra un terminal en los cuadernos de Azure Machine Learning y ejecute los comandos siguientes:

     ``` bash
     conda activate azureml_py38
     pip install --upgrade msticpy
     ```

  1. Cierre el terminal y reinicie el kernel.


## <a name="set-an-environment-variable-for-your-msticpyconfigyaml-file"></a>Establecimiento de una variable de entorno para el archivo msticpyconfig.yaml

Si realiza la ejecución en Azure Machine Learning y tiene el archivo **msticpyconfig.yaml** en la raíz de la carpeta de usuario, MSTICPy encontrará automáticamente esta configuración. Pero si ejecuta los cuadernos en otro entorno, siga las instrucciones de esta sección para establecer una variable de entorno que apunte a la ubicación del archivo de configuración.

La definición de la ruta al archivo **msticpyconfig.yaml** en una variable de entorno le permite almacenar el archivo en una ubicación conocida y asegurarse de cargar siempre la misma configuración.

Use varios archivos de configuración, con varias variables de entorno, si quiere utilizar otra configuración para otros cuadernos.

1. Decida una ubicación para el archivo **msticpyconfig.yaml**, como **~/.msticpyconfig.yaml** o **%userprofile%/msticpyconfig.yaml**.

    **Usuarios de Azure Machine Learning**: si almacena el archivo de configuración en la carpeta de usuario de Azure Machine Learning, la función `init_notebook` de MSTICPy (que se ejecuta en la celda de inicialización) buscará y usará automáticamente el archivo, y no tendrá que establecer una variable de entorno **MSTICPYCONFIG**.

    Pero si también tiene secretos almacenados en el archivo, se recomienda almacenar el archivo de configuración en la unidad local de proceso. El almacenamiento interno del proceso solo es accesible para el creador del proceso, mientras que el almacenamiento compartido es accesible para todo el que tenga acceso al área de trabajo de Azure Machine Learning.

    Para obtener más información, vea [¿Qué es una instancia de proceso de Azure Machine Learning?](/azure/machine-learning/concept-compute-instance).

1. Si es necesario, copie el archivo **msticpyconfig.yaml** en la ubicación seleccionada.

1. Establezca la variable de entorno **MSTICPYCONFIG** para que apunte a esa ubicación.

Use uno de los procedimientos siguientes para definir la variable de entorno **MSTICPYCONFIG**.
# <a name="windows"></a>[Windows](#tab/windows)

Por ejemplo, para establecer la variable de entorno **MSTICPYCONFIG** en sistemas Windows:

1. Mueva el archivo **msticpyconfig.yaml** a la instancia de proceso según sea necesario.
1. Abra el cuadro de diálogo **Propiedades del sistema** por la pestaña **Avanzado**.
1. Seleccione **Variables de entorno...** para abrir el cuadro de diálogo **Variables de entorno**.
1. En el área **Variables del sistema**, seleccione **Nuevo...** y defina los valores como se muestra a continuación:

    - **Nombre de variable**: defínalo como `MSTICPYCONFIG`
    - **Valor de variable**: escriba la ruta al archivo **msticpyconfig.yaml**.

# <a name="linux"></a>[Linux](#tab/linux)

En este procedimiento se describe cómo actualizar el archivo **.bashrc** para establecer la variable de entorno **MSTICPYCONFIG** en sistemas Linux.

1. Mueva el archivo **msticpyconfig.yaml** a la instancia de proceso según sea necesario.

1. Abra un terminal de Azure Machine Learning, por ejemplo, desde la página **Cuadernos** de Azure Sentinel.

1. Compruebe que puede acceder al archivo **msticpyconfig.yaml.**

   En el terminal de Azure Machine Learning, el directorio actual debe ser el directorio principal del almacén de archivos de Azure Machine Learning, montado en el sistema Compute Linux. El terminal es similar al del siguiente ejemplo:

   ```python
   azureuser@alicecontoso-azml7:~/cloudfiles/code/Users/alicecontoso$
   ```

   Para enumerar todos los archivos del directorio principal, incluido el archivo **msticpyconfig.yaml,** escriba `ls`.

1. Para mover el archivo **msticpyconfig.yaml** al almacén de archivos Proceso, escriba lo siguiente:

   ```python
   mv msticpyconfig.yaml ~
   ```

1. Use uno de los procesos siguientes a fin de editar el archivo **.bashrc** para la variable de entorno:

    |Get-Help  |Pasos  |
    |---------|---------|
    |**vim**     |     1. Ejecute: `vim ~/.bashrc`. <br>2. Presione **MAYÚS+G** > **Fin** para ir al final del archivo. 3. Para crear una línea, escriba **a** y, después, presione **ENTRAR**. <br>4. Agregue la variable de entorno y presione **ESC** para volver al modo de comando. <br>5. Para guardar el archivo, escriba **:wq**.    |
    |**nano**     |           1. Ejecute: `nano ~/.bashrc`.<br>        1. Presione **ALT+/** u **OPCIÓN+/** para ir al final del archivo.<br>        1. Agregue la variable de entorno y, después, guarde el archivo. Presione **CTRL+X** y después **Y**.      |
    |     |         |

    Agregue una de las variables de entorno siguientes:

    - Si ha movido el archivo **msticpyconfig.yaml**, ejecute `export MSTICPYCONFIG=~/msticpyconfig.yaml`.
    - Si no ha movido el archivo **msticpyconfig.yaml**, ejecute `export MSTICPYCONFIG=~/cloudfiles/code/Users/<YOURNAME>/msticpyconfig.yaml`.


# <a name="azure-ml-options"></a>[Opciones de Azure Machine Learning](#tab/azure-ml)

Si tiene que almacenar el archivo **msticpyconfig.yaml** en otro lugar que no sea la carpeta de usuario de Azure Machine Learning, use una de las opciones siguientes:

- **Un archivo *nbuser_settings.py* en la raíz de la carpeta de usuario**.  Aunque este proceso es más sencillo y menos intrusivo que editar el archivo **kernel.json**, solo se admite cuando se ejecuta la función `init_notebook` al principio del código del cuaderno. Aunque este es el comportamiento predeterminado, si ejecuta el código del cuaderno sin ejecutar `init_notebook` primero, es posible que MSTICPy no pueda encontrar el archivo de configuración.

    1. En el terminal de Azure Machine Learning, cree el archivo **nbuser_settings.py** en la raíz de la carpeta de usuario, que es la carpeta con su nombre de usuario.
    1. En el archivo **nbuser_settings.py**, agregue las líneas siguientes:
        ```python
          import os
          os.environ["MSTICPYCONFIG"] = "~/msticpyconfig.yaml"
        ```

    La función `init_notebook` importa automáticamente este archivo y establece la variable de entorno `MSTICPYCONFIG` para el cuaderno actual.

- **El archivo *kernel.json* para el kernel de Python**. Use este procedimiento si planea ejecutar el cuaderno de forma manual y posiblemente sin llamar a la función `init_notebook` al principio.

    Hay kernels para Python 3.6 y Python 3.8. Si usa los dos kernels, edite los dos archivos.

    - **Ubicación de Python 3.8**: */usr/local/share/jupyter/kernels/python38-azureml/kernel.json*
    - **Ubicación de Python 3.6**: */usr/local/share/jupyter/kernels/python3-azureml/kernel.json*

    Para establecer la variable de entorno en el archivo **kernel.json**:

    1. Realice una copia del archivo **kernel.json** y abra el original en un editor. Es posible que tenga que usar `sudo` para sobrescribir el archivo **kernel.json**; el contenido del archivo será similar al del ejemplo siguiente:

         ```python
         {
             "argv": [
             "/anaconda/envs/azureml_py38/bin/python",
             "-m",
             "ipykernel_launcher",
             "-f",
             "{connection_file}"
             ],
             "display_name": "Python 3.8 - AzureML",
             "language": "python"
         }
         ```

    1. Después del elemento `"language"`, agregue la línea siguiente: `"env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }`

        Asegúrese de agregar la coma al final de la línea `"language": "python"`. Por ejemplo:

         ```python
         {
             "argv": [
             "/anaconda/envs/azureml_py38/bin/python",
             "-m",
             "ipykernel_launcher",
             "-f",
             "{connection_file}"
             ],
             "display_name": "Python 3.8 - AzureML",
             "language": "python",
             "env": { "MSTICPYCONFIG": "~/msticpyconfig.yaml" }
         }
         ```

---

> [!NOTE]
> Para las opciones Linux y Windows, tendrá que reiniciar el servidor de Jupyter para que seleccione la variable de entorno que ha definido.
>

## <a name="next-steps"></a>Pasos siguientes

Para más información, consulte:

|Firmante  |Más referencias  |
|---------|---------|
|**MSTICPy**     |      - [Configuración del paquete MSTICPy](https://msticpy.readthedocs.io/en/latest/getting_started/msticpyconfig.html)<br> - [Editor de configuraciones de MSTICPy](https://msticpy.readthedocs.io/en/latest/getting_started/SettingsEditor.html)<br>    - [Configuración del entorno de Notebook](https://github.com/Azure/Azure-Sentinel-Notebooks/blob/master/ConfiguringNotebookEnvironment.ipynb).<br>    - [Cuaderno MPSettingsEditor](https://github.com/microsoft/msticpy/blob/master/docs/notebooks/MPSettingsEditor.ipynb). <br><br>**Nota**: El repositorio Azure-Sentinel-Notebooks de GitHub también contiene un archivo *msticpyconfig.yaml* de plantilla con secciones comentadas, lo que puede ayudarle a comprender la configuración.      |
|**Azure Sentinel y cuadernos de Jupyter**     |      - [Cuadernos de Jupyter: introducción](https://realpython.com/jupyter-notebook-introduction/)<br>    - [Documentación de MSTICPy](https://msticpy.readthedocs.io/)<br>    - [Documentación de cuadernos de Azure Sentinel](notebooks.md)<br>    - [The Infosec Jupyterbook](https://infosecjupyterbook.com/introduction.html)<br>    - [Tutorial del Cuaderno del Explorador de host de Linux](https://techcommunity.microsoft.com/t5/azure-sentinel/explorer-notebook-series-the-linux-host-explorer/ba-p/1138273)<br>    - [Por qué usar Jupyter para investigaciones de seguridad](https://techcommunity.microsoft.com/t5/azure-sentinel/why-use-jupyter-for-security-investigations/ba-p/475729)<br>    - [Investigaciones de seguridad con Azure Sentinel y Notebooks](https://techcommunity.microsoft.com/t5/azure-sentinel/security-investigation-with-azure-sentinel-and-jupyter-notebooks/ba-p/432921)<br>    - [Documentación de Pandas](https://pandas.pydata.org/pandas-docs/stable/user_guide/index.html)<br>    - [Documentación de Bokeh](https://docs.bokeh.org/en/latest/)       |
|     |         |

