---
title: Desarrollo de extensiones de trabajo de Python para Azure Functions
description: Aprenda a crear y publicar extensiones de trabajo que le permitirán insertar comportamiento de middleware en funciones de Python que se ejecutan en Azure.
ms.topic: how-to
ms.date: 6/1/2021
ms.custom: devx-track-python
ms.openlocfilehash: c5ee4ec3a8bd6fd6994962ab35453b34cb7fda8b
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124764287"
---
# <a name="develop-python-worker-extensions-for-azure-functions"></a>Desarrollo de extensiones de trabajo de Python para Azure Functions

Azure Functions permite integrar comportamientos personalizados como parte de la ejecución de funciones de Python. Esta característica le permite crear lógica de negocios que los clientes pueden usar fácilmente en sus propias aplicaciones de funciones. Para obtener más información, vea la [Referencia para desarrolladores de Python](functions-reference-python.md#python-worker-extensions).

En este tutorial, aprenderá a: 
> [!div class="checklist"]
> * Crear una extensión de trabajo de Python de nivel de aplicación para Azure Functions 
> * Consumir la extensión en una aplicación tal como lo hacen los clientes 
> * Empaquetar y publicar una extensión para su consumo 

## <a name="prerequisites"></a>Requisitos previos

Antes de empezar, debe cumplir estos requisitos:

* [Python 3.6.x o superior](https://www.python.org/downloads/release/python-374/). Para consultar la lista completa de las versiones compatibles de Python en Azure Functions, consulte la [Guía para desarrolladores de Python](functions-reference-python.md#python-version).

* [Azure Functions Core Tools](functions-run-local.md#v2), versión 3.0.3568 o superior.

* Tener instalado [Visual Studio Code](https://code.visualstudio.com/) en una de las [plataformas compatibles](https://code.visualstudio.com/docs/supporting/requirements#_platforms).

## <a name="create-the-python-worker-extension"></a>Creación de la extensión de trabajo de Python

La extensión que cree informará del tiempo transcurrido de una invocación de desencadenador HTTP en los registros de la consola y en el cuerpo de la respuesta HTTP.

### <a name="folder-structure"></a>Estructura de carpetas

La carpeta del proyecto de extensión debería tener una estructura similar a la siguiente:

```
<python_worker_extension_root>/
 | - .venv/
 | - python_worker_extension_timer/
 | | - __init__.py
 | - setup.py
 | - readme.md
```

| Carpeta/Archivo | Descripción |
| --- | --- |
| **.venv/** | (Opcional) Contiene un entorno virtual de Python usado para el desarrollo local. |
| **python_worker_extension/** | Contiene el código fuente de la extensión de trabajo de Python. Esta carpeta contiene el módulo principal de Python que se va a publicar en PyPI. |
| **setup.py** | Contiene los metadatos del paquete de extensión de trabajo de Python. |
| **readme.md** | (Opcional) Contiene la instrucción y el uso de la extensión. Este contenido se muestra como descripción en la página principal del proyecto de PyPI. |

### <a name="configure-project-metadata"></a>Configuración de los metadatos del proyecto

Primero debe crear `setup.py`, que proporciona información esencial sobre el paquete. Para asegurarse de que la extensión se distribuya y se integre correctamente en las aplicaciones de funciones del cliente, confirme que `'azure-functions >= 1.7.0, < 2.0.0'` se encuentra en la sección `install_requires`.

En la plantilla siguiente, debe cambiar los campos`author`, `author_email`, `install_requires`, `license`, `packages` y `url`, según sea necesario.

:::code language="python" source="~/azure-functions-python-worker-extension/setup.py":::

A continuación, implementará el código de extensión en el ámbito de nivel de aplicación.

### <a name="implement-the-timer-extension"></a>Implementación de la extensión de temporizador

Agregue el código siguiente en `python_worker_extension_timer/__init__.py` para implementar la extensión de nivel de aplicación:

:::code language="python" source="~/azure-functions-python-worker-extension/python_worker_extension_timer/__init__.py":::

Este código hereda de [AppExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/app_extension_base.py) para que la extensión se aplique a todas las funciones de la aplicación. También podría implementar la extensión en un ámbito de nivel de función si hereda de [FuncExtensionBase](https://github.com/Azure/azure-functions-python-library/blob/dev/azure/functions/extension/func_extension_base.py).

El método `init` es un método de clase al que llama el trabajo cuando se importa la clase de extensión. Puede realizar acciones de inicialización aquí para la extensión. En este caso, se inicializa una asignación de códigos hash para registrar la hora de inicio de la invocación para cada función.

El método `configure` está orientado al cliente. En el archivo Léame, puede indicar a los clientes cuándo necesitan llamar a `Extension.configure()`. El archivo Léame también debe documentar las funcionalidades de la extensión, su posible configuración y su uso. En este ejemplo, los clientes pueden decidir si el tiempo transcurrido se notifica en `HttpResponse`.

El trabajo de Python llama al método `pre_invocation_app_level` antes de que se ejecute la función. Proporciona la información de la función, como su contexto y argumentos. En este ejemplo, la extensión registra un mensaje y guarda la hora de inicio de una invocación en función de su invocation_id.

De forma similar, se llama a `post_invocation_app_level` después de la ejecución de la función. En este ejemplo se calcula el tiempo transcurrido en función de la hora de inicio y la hora actual. También sobrescribe el valor devuelto de la respuesta HTTP.

## <a name="consume-your-extension-locally"></a>Consumo local de la extensión

Ahora que ha creado una extensión, puede usarla en un proyecto de aplicación para comprobar que funciona según lo previsto. 

### <a name="create-an-http-trigger-function"></a>Creación de una función desencadenada por HTTP

1. Cree una carpeta para el proyecto de la aplicación y vaya a ella. 

1. Desde el shell adecuado, como Bash, ejecute el siguiente comando para inicializar el proyecto:

    ```bash
    func init --python
    ```

1. Use el siguiente comando para crear una función de desencadenador HTTP que permita el acceso anónimo:

    ```bash
    func new -t HttpTrigger -n HttpTrigger -a anonymous
    ```

### <a name="activate-a-virtual-environment"></a>Activación de un entorno virtual

1. Cree un entorno virtual de Python basado en el sistema operativo, como se muestra a continuación:

    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    python3 -m venv .venv
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    py -m venv .venv
    ``` 
    ---

1. Active el entorno virtual de Python basado en el sistema operativo, como se muestra a continuación:
    # <a name="linux"></a>[Linux](#tab/linux)
    ```bash
    source .venv/bin/activate
    ```
    # <a name="windows"></a>[Windows](#tab/windows)
    ```console
    .venv\Scripts\Activate.ps1
    ``` 
    ---

### <a name="configure-the-extension"></a>Configuración de la extensión

1. Instale paquetes remotos para el proyecto de aplicación de funciones con el siguiente comando: 
    
    ```bash
    pip install -r requirements.txt
    ```

1. Instale la extensión desde la ruta de acceso del archivo local en modo editable, como se muestra a continuación:

    ```bash
    pip install -e <PYTHON_WORKER_EXTENSION_ROOT>
    ```

    En este ejemplo, reemplace `<PYTHON_WORKER_EXTENSION_ROOT>` por la ubicación del archivo del proyecto de extensión.   
    Cuando un cliente use la extensión, en su lugar agregará la ubicación del paquete de extensión al archivo requirements.txt, como en los ejemplos siguientes:

    # <a name="pypi"></a>[PyPI](#tab/pypi)
    ```python
    # requirements.txt
    python_worker_extension_timer==1.0.0
    ``` 
    # <a name="github"></a>[GitHub](#tab/github)
    
    ```python
    # requirements.txt
    git+https://github.com/Azure-Samples/python-worker-extension-timer@main
    ```
    ---

1. Abra el archivo de proyecto local.settings.json y agregue el siguiente campo a `Values`:

    ```json
    "PYTHON_ENABLE_WORKER_EXTENSIONS": "1" 
    ```

    Si lo ejecuta en Azure, en su lugar agregue `PYTHON_ENABLE_WORKER_EXTENSIONS=1` a la [configuración de la aplicación en la aplicación de funciones](functions-how-to-use-azure-function-app-settings.md#settings).

1. Agregue las dos líneas siguientes antes de la función `main` en \_\_init.py\_\_:

    ```python
    from python_worker_extension_timer import TimerExtension
    TimerExtension.configure(append_to_http_response=True)
    ```

    Este código importa el módulo `TimerExtension` y establece el valor de configuración `append_to_http_response`.

### <a name="verify-the-extension"></a>Comprobación de la extensión

1. En la carpeta raíz del proyecto de aplicación, inicie el host de función mediante `func host start --verbose`. Debería ver el punto de conexión local de la función en la salida como `https://localhost:7071/api/HttpTrigger`.

1. En el explorador, envíe una solicitud GET a `https://localhost:7071/api/HttpTrigger`. Debería ver una respuesta como la siguiente, con los datos de **TimeElapsed** de la solicitud anexados. 

    <pre>
    This HTTP triggered function executed successfully. Pass a name in the query string or in the request body for a personalized response. (TimeElapsed: 0.0009996891021728516 sec)
    </pre>

## <a name="publish-your-extension"></a>Publicación de la extensión

Después de crear y comprobar la extensión, debe completar las tareas de publicación siguientes:

> [!div class="checklist"]
> + Elija una licencia.
> + Cree un archivo readme.md y otra documentación. 
> + Publique la biblioteca de extensiones en un registro de paquetes de Python o en un sistema de control de versiones (VCS).

# <a name="pypi"></a>[PyPI](#tab/pypi)

Para publicar la extensión en PyPI:

1. Ejecute el siguiente comando para instalar `twine` y `wheel` en el entorno predeterminado de Python o en un entorno virtual:

    ```bash
    pip install twine wheel
    ```

1. Quite la carpeta `dist/` anterior del repositorio de extensiones.

1. Ejecute el siguiente comando para generar un nuevo paquete en `dist/`:

    ```bash
    python setup.py sdist bdist_wheel
    ```

1. Ejecute el siguiente comando para cargar el paquete en PyPI:

    ```bash
    twine upload dist/*
    ```

    Es posible que tenga que proporcionar las credenciales de la cuenta de PyPI durante la carga.

Después de estos pasos, los clientes pueden usar la extensión si incluyen el nombre del paquete en el archivo requirements.txt.

Para obtener más información, consulte el [tutorial oficial de empaquetado de Python](https://packaging.python.org/tutorials/packaging-projects/).

# <a name="github"></a>[GitHub](#tab/github)

También puede publicar el código fuente de la extensión con el archivo setup.py en un repositorio de GitHub, como se muestra en [este repositorio de ejemplo](https://github.com/Azure-Samples/python-worker-extension-timer). 

Para obtener más información sobre la compatibilidad con VCS en pip, consulte la [documentación oficial de compatibilidad con VCS en pip](https://pip.pypa.io/en/stable/cli/pip_install/#vcs-support).

---

## <a name="examples"></a>Ejemplos

+ Puede ver cómo se ha completado el proyecto de extensión de ejemplo de este artículo en el repositorio de ejemplo [python_worker_extension_timer](https://github.com/Azure-Samples/python-worker-extension-timer). 

+ La integración de OpenCensus es un proyecto de código abierto que usa la interfaz de extensión para integrar el seguimiento de telemetría en aplicaciones de Python para Azure Functions. Consulte el repositorio [opencensus-python-extensions-azure](https://github.com/census-ecosystem/opencensus-python-extensions-azure/tree/main/extensions/functions) para revisar la implementación de esta extensión de trabajo de Python.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información sobre el desarrollo de Python de Azure Functions, consulte los siguientes recursos:

* [Guía de Azure Functions para desarrolladores de Python](functions-reference-python.md)
* [Procedimientos recomendados para Azure Functions](functions-best-practices.md)
* [Referencia para desarrolladores de Azure Functions](functions-reference.md)
