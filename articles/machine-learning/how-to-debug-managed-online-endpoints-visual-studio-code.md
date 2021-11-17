---
title: Depuración de puntos de conexión en línea localmente en VS Code (versión preliminar)
titleSuffix: Azure Machine Learning
description: Obtenga información sobre cómo usar Visual Studio Code para probar y depurar puntos de conexión en línea localmente antes de implementarlos en Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: luisquintanilla
ms.author: luquinta
ms.date: 11/03/2021
ms.topic: troubleshooting
ms.custom: devplatv2
ms.openlocfilehash: 33b46c490912d0c651c5629da17795efe074b6d9
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565856"
---
# <a name="debug-online-endpoints-locally-in-visual-studio-code-preview"></a>Depuración de puntos de conexión en línea localmente en Visual Studio Code (versión preliminar)

Obtenga información sobre cómo usar el depurador de Visual Studio Code (VS Code) para probar y depurar puntos de conexión en línea localmente antes de implementarlos en Azure.

Los puntos de conexión locales de Azure Machine Learning le ayudan a probar y depurar localmente el script de puntuación, la configuración del entorno, la configuración de código y el modelo de Machine Learning.

## <a name="online-endpoint-local-debugging"></a>Depuración local de puntos de conexión en línea

Depurar puntos de conexión localmente antes de implementarlos en la nube puede ayudarle a detectar antes errores en el código y en la configuración. Tiene distintas opciones para depurar puntos de conexión localmente con VS Code.

- [Servidor HTTP de inferencia de Azure Machine Learning (versión preliminar)](how-to-inference-server-http.md)
- Punto de conexión local

Esta guía se centra en los puntos de conexión locales.

En la tabla siguiente encontrará información general sobre los escenarios que le ayudarán a elegir lo que le funcione mejor en su caso.

| Escenario | Servidor HTTP de inferencia | Punto de conexión local |
|--|--|--|
| Actualización del entorno de Python local, **sin** recompilación de imágenes de Docker | Sí | No |
| Actualización del script de puntuación | Sí | Sí |
| Actualización de las configuraciones de implementación (implementación, entorno, código, modelo) | No | Sí |
| Integración del depurador de VS Code | Sí | Sí |

## <a name="prerequisites"></a>Requisitos previos

En esta guía se da por supuesto que los siguientes elementos se han instalados localmente en el equipo.

- [Docker](https://docs.docker.com/engine/install/)
- [Código de VS](https://code.visualstudio.com/#alt-downloads)
- [CLI de Azure](/cli/azure/install-azure-cli)
- [Extensión `ml` de la CLI de Azure](how-to-configure-cli.md)

Para obtener más información, consulte la guía sobre [cómo preparar el sistema para implementar puntos de conexión en línea administrados](how-to-deploy-managed-online-endpoints.md#prepare-your-system).

Los ejemplos de este artículo se basan en ejemplos de código que se encuentran en el repositorio [azureml-examples](https://github.com/azure/azureml-examples). Para ejecutar los comandos localmente sin tener que copiar y pegar los archivos YAML y demás, clone el repositorio y luego cambie los directorios al directorio `cli` del repositorio:

```azurecli
git clone https://github.com/Azure/azureml-examples --depth 1
cd azureml-examples
cd cli
```

Si aún no ha establecido los valores predeterminados de la CLI de Azure, guarde la configuración predeterminada. Para evitar pasar los valores de la suscripción, el área de trabajo y el grupo de recursos varias veces, ejecute los comandos siguientes. Reemplace los parámetros siguientes por valores para su configuración específica:

* Reemplace `<subscription>` con la identificación de su suscripción de Azure.
* Reemplace `<workspace>` por el nombre del área de trabajo de Azure Machine Learning.
* Reemplace `<resource-group>` por el grupo de recursos de Azure que contiene el área de trabajo.
* Reemplace `<location>` por la región de Azure que contiene el área de trabajo.

> [!TIP]
> Puede ver cuáles son los valores predeterminados actuales mediante el comando `az configure -l`.

```azurecli
az account set --subscription <subscription>
az configure --defaults workspace=<workspace> group=<resource-group> location=<location>
```

## <a name="launch-development-container"></a>Inicio del contenedor de desarrollo

Los puntos de conexión locales de Azure Machine Learning usan contenedores de desarrollo de Docker y VS Code para compilar y configurar un entorno de depuración local. Con los contenedores de desarrollo, puede aprovechar las características de VS Code desde dentro de un contenedor de Docker. Para obtener más información sobre los contenedores de desarrollo, consulte [Creación de un contenedor de desarrollo](https://code.visualstudio.com/docs/remote/create-dev-container).

Para depurar puntos de conexión en línea localmente en VS Code, use la marca `--vscode-debug` al crear o actualizar una implementación en línea de Azure Machine Learning. En el comando siguiente se usa un ejemplo de implementación del repositorio de ejemplos:

```azurecli
az ml online-deployment create --file endpoints/online/managed/sample/blue-deployment.yml --local --vscode-debug
```

> [!IMPORTANT]
> En el Subsistema de Windows para Linux (WSL), deberá actualizar la variable de entorno PATH para incluir la ruta de acceso al ejecutable de VS Code o usar la interoperabilidad de WSL. Para obtener más información, consulte [Interoperabilidad de Windows con Linux](/windows/wsl/interop).

Una imagen de Docker se ha creado localmente. Todo error de configuración del entorno o archivo de modelo se muestra en esta fase del proceso.

> [!NOTE]
> La primera vez que inicie un contenedor de desarrollo nuevo o actualizado, puede tardar varios minutos.

Una vez que la imagen se compile correctamente, el contenedor de desarrollo se abrirá en una ventana de VS Code.

Usará algunas extensiones de VS Code para depurar las implementaciones en el contenedor de desarrollo. Azure Machine Learning instala automáticamente estas extensiones en el contenedor de desarrollo.

- Depuración de inferencia
- [Pylance](https://marketplace.visualstudio.com/items?itemName=ms-python.vscode-pylance)
- [Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter)
- [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

> [!IMPORTANT]
> Antes de iniciar la sesión de depuración, asegúrese de que las extensiones de VS Code hayan terminado de instalarse en el contenedor de desarrollo.  

## <a name="start-debug-session"></a>Inicio de la sesión de depuración

Una vez configurado el entorno, use el depurador de VS Code para probar y depurar la implementación localmente.

1. Abra el script de puntuación en Visual Studio Code.

    > [!TIP]
    > El script score.py usado por el punto de conexión implementado anteriormente se encuentra en `azureml-samples/cli/endpoints/online/managed/sample/score.py` en el repositorio que ha clonado. Sin embargo, los pasos de esta guía funcionan con cualquier script de puntuación.

1. Establezca un punto de interrupción en el script de puntuación.

    - Para depurar el comportamiento de inicio, coloque los puntos de interrupción dentro de la función `init`.
    - Para depurar el comportamiento de puntuación, coloque los puntos de interrupción dentro de la función `run`.

1. Seleccione la vista de ejecución de VS Code.
1. En la lista desplegable Run and Debug (Ejecutar y depurar), seleccione **Azure ML: Debug Local Endpoint** (Azure ML: depurar punto de conexión local) para iniciar la depuración local del punto de conexión.

    En la sección **Puntos de interrupción** de la vista de ejecución, compruebe que:

    - **Raised Exceptions** (Excepciones generadas) esté **sin activar**.
    - **Uncaught Exceptions** (Excepciones no detectadas) esté **activada**.

    :::image type="content" source="media/how-to-debug-managed-online-endpoints-visual-studio-code/configure-debug-profile.png" alt-text="Configuración del perfil de depuración del entorno local de Azure ML":::

1. Seleccione el icono de reproducción situado junto a la lista desplegable Run and Debug (Ejecutar y depurar) para iniciar la sesión de depuración.

    En este momento, se detectan los puntos de interrupción de la función `init`. Use las acciones de depuración para avanzar por el código. Para obtener más información sobre las acciones de depuración, consulte la [guía de acciones de depuración](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

Para obtener más información sobre el depurador VS Code, consulte [Depuración en VS Code](https://code.visualstudio.com/Docs/editor/debugging).

## <a name="debug-your-endpoint"></a>Depuración del punto de conexión

Ahora que la aplicación se está ejecutando en el depurador, intente realizar una predicción para depurar el script de puntuación.

Use el comando `invoke` de la extensión `ml` para realizar una solicitud al punto de conexión local.

```azurecli
az ml online-endpoint invoke --name <ENDPOINT-NAME> --request-file <REQUEST-FILE> --local
```

En este caso, `<REQUEST-FILE>` es un archivo JSON que contiene ejemplos de datos de entrada para el modelo con el fin de realizar predicciones en forma similar al siguiente JSON:

```json
{"data": [
    [1,2,3,4,5,6,7,8,9,10], 
    [10,9,8,7,6,5,4,3,2,1]
]}
```

> [!TIP]
> El URI de puntuación es la dirección donde el punto de conexión escucha las solicitudes. Use la extensión `ml` para obtener el URI de puntuación.
>
>    ```azurecli
>    az ml online-endpoint show --name <ENDPOINT-NAME> --local
>    ```
>
> La salida debe tener una apariencia similar a la siguiente:
>
> ```json
> {
>  "auth_mode": "aml_token",
>  "location": "local",
>  "name": "my-new-endpoint",
>  "properties": {},
>  "provisioning_state": "Succeeded",
>  "scoring_uri": "http://localhost:5001/score",
>  "tags": {},
>  "traffic": {},
>  "type": "online"
>}
>```
>
>El URI de puntuación se puede encontrar en la propiedad `scoring_uri`.

En este momento, se detectan los puntos de interrupción de la función `run`. Use las acciones de depuración para avanzar por el código. Para obtener más información sobre las acciones de depuración, consulte la [guía de acciones de depuración](https://code.visualstudio.com/Docs/editor/debugging#_debug-actions).

## <a name="edit-your-endpoint"></a>Edición del punto de conexión

A medida que depura y soluciona los problemas de la aplicación, hay escenarios en los que tendrá que actualizar el script de puntuación y las configuraciones.

Para aplicar cambios en el código:

1. Actualización del código
1. Reinicie la sesión de depuración con el comando `Developer: Reload Window` de la paleta de comandos. Para obtener más información, consulte la [documentación de la paleta de comandos](https://code.visualstudio.com/docs/getstarted/userinterface#_command-palette).

> [!NOTE]
> Puesto que el directorio que contiene el código y los recursos del punto de conexión está montado en el contenedor de desarrollo, los cambios que realice en el contenedor de desarrollo se sincronizarán con el sistema de archivos local.

Para realizar cambios más exhaustivos que impliquen actualizaciones en la configuración del entorno y del punto de conexión, use el comando `update` de la extensión `ml`. Esto desencadenará una recompilación de la imagen completa con los cambios.

```azurecli
az ml online-deployment update --file <DEPLOYMENT-YAML-SPECIFICATION-FILE> --local --vscode-debug
```

Una vez que se haya compilado la imagen actualizada y se inicie el contenedor de desarrollo, use el depurador de VS Code para probar y solucionar los problemas del punto de conexión actualizado.

## <a name="next-steps"></a>Pasos siguientes

- [Implementación y puntuación de un modelo de aprendizaje automático con un punto de conexión en línea administrado (versión preliminar)](how-to-deploy-managed-online-endpoints.md)
- [Solución de problemas de implementación y puntuación de puntos de conexión en línea administrados (versión preliminar)](how-to-troubleshoot-managed-online-endpoints.md)
