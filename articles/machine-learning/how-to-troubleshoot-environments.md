---
title: Solución de problemas de imágenes del entorno
titleSuffix: Azure Machine Learning
description: Aprenda a solucionar problemas con las compilaciones de imágenes del entorno y las instalaciones de paquetes.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: saachigopal
ms.author: sagopal
ms.date: 07/27/2021
ms.topic: troubleshooting
ms.custom: devx-track-python
ms.openlocfilehash: 8f5a286a835da7fb74f969295090b0ecc0731a74
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114728473"
---
# <a name="troubleshoot-environment-image-builds"></a>Solución de problemas en las compilaciones de imágenes

Aprenda a solucionar problemas con las compilaciones de imágenes del entorno de Docker y las instalaciones de paquetes.

## <a name="prerequisites"></a>Prerrequisitos

* Suscripción a Azure. Pruebe la [versión gratuita o de pago de Azure Machine Learning](https://azure.microsoft.com/free/).
* El [SDK de Azure Machine Learning](/python/api/overview/azure/ml/install).
* La[CLI de Azure](/cli/azure/install-azure-cli).
* La [extensión de la CLI para Azure Machine Learning](reference-azure-machine-learning-cli.md).
* Para depurar localmente, debe tener una instalación de Docker en funcionamiento en el sistema local.

## <a name="docker-image-build-failures"></a>Errores en la compilación de imágenes de Docker
 
Encontrará la causa principal de la mayoría de los errores de compilación de imágenes en el registro de compilación de la imagen.
Busque el registro de compilación de la imagen en el portal de Azure Machine Learning (20\_image\_build\_log.txt) o en los registros de ejecución de las tareas de Azure Container Registry.
 
Normalmente es más fácil reproducir los errores de forma local. Compruebe el tipo de error y pruebe una de las siguientes `setuptools`:

- Instale la dependencia de Conda localmente: `conda install suspicious-dependency==X.Y.Z`.
- Instale la dependencia de PIP localmente: `pip install suspicious-dependency==X.Y.Z`.
- Intente materializar todo el entorno: `conda create -f conda-specification.yml`.

> [!IMPORTANT]
> Asegúrese de que la plataforma y el intérprete del clúster de proceso local coinciden con los del clúster de proceso remoto. 

### <a name="timeout"></a>Tiempo de espera 
 
Los siguientes problemas de red pueden producir errores en el tiempo de espera:

- Bajo ancho de banda de Internet
- Problemas del servidor
- Dependencias de gran tamaño que no se pueden descargar con la configuración especificada de tiempo de espera de Conda o PIP.
 
Mensajes similares a los ejemplos siguientes indicarán el problema:
 
```
('Connection broken: OSError("(104, \'ECONNRESET\')")', OSError("(104, 'ECONNRESET')"))
```
```
ReadTimeoutError("HTTPSConnectionPool(host='****', port=443): Read timed out. (read timeout=15)",)
```

Si recibe un mensaje de error, pruebe una de las siguientes soluciones posibles:
 
- Pruebe un origen diferente para la dependencia, como los reflejos, Azure Blob Storage u otras fuentes de Python.
- Actualice Conda o PIP. Si usa un archivo de Docker personalizado, actualice la configuración del tiempo de espera.
- Algunas versiones de PIP tienen problemas conocidos. Considere la posibilidad de agregar una versión específica de PIP a las dependencias del entorno.

### <a name="package-not-found"></a>No se encuentra el paquete

Los errores siguientes son los más comunes en la compilación de imágenes:

- No se pudo encontrar el paquete Conda:

   ```
   ResolvePackageNotFound: 
   - not-existing-conda-package
   ```

- No se pudo encontrar el paquete o la versión de PIP especificados:

   ```
   ERROR: Could not find a version that satisfies the requirement invalid-pip-package (from versions: none)
   ERROR: No matching distribution found for invalid-pip-package
   ```

- La dependencia de PIP anidada es incorrecta:

   ```
   ERROR: No matching distribution found for bad-package==0.0 (from good-package==1.0)
   ```

Compruebe que el paquete existe en los orígenes especificados. Use la [búsqueda de PIP](https://pip.pypa.io/en/stable/reference/pip_search/) para comprobar las dependencias de PIP:

- `pip search azureml-core`

En el caso de las dependencias de Conda, use la [búsqueda de Conda](https://docs.conda.io/projects/conda/en/latest/commands/search.html):

- `conda search conda-forge::numpy`

Para obtener más opciones, pruebe lo siguiente:
- `pip search -h`
- `conda search -h`

#### <a name="installer-notes"></a>Notas del instalador

Asegúrese de que existe la distribución necesaria para la plataforma especificada y la versión del intérprete de Python.

En el caso de las dependencias de PIP, vaya a `https://pypi.org/project/[PROJECT NAME]/[VERSION]/#files` para ver si la versión necesaria está disponible. Vaya a https://pypi.org/project/azureml-core/1.11.0/#files para ver un ejemplo.

En el caso de las dependencias de Conda, compruebe el paquete en el repositorio del canal.
En el caso de los canales que mantiene Anaconda, Inc., compruebe la [página de paquetes de Anaconda](https://repo.anaconda.com/pkgs/).

### <a name="pip-package-update"></a>Actualización del paquete de PIP

Durante la instalación o actualización de un paquete de PIP, es posible que el solucionador tenga que actualizar un paquete que ya esté instalado para satisfacer los nuevos requisitos.
Tenga en cuenta que se puede producir un error en la desinstalación por varias razones relacionadas con la versión de PIP o la manera en que se instaló la dependencia.
El caso más común es que una dependencia que haya instalado Conda no se pueda desinstalar mediante PIP.
En este caso, considere la posibilidad de desinstalar la dependencia mediante `conda remove mypackage`.

```
  Attempting uninstall: mypackage
    Found existing installation: mypackage X.Y.Z
ERROR: Cannot uninstall 'mypackage'. It is a distutils installed project and thus we cannot accurately determine which files belong to it which would lead to only a partial uninstall.
``` 
### <a name="installer-issues"></a>Problemas del instalador

Ciertas versiones del instalador tienen problemas en los solucionadores de paquetes que pueden provocar un error de compilación.

Si usa una imagen base personalizada o un archivo Dockerfile, se recomienda usar la versión 4.5.4 o una superior de Conda.

Igualmente, es necesario un paquete de PIP para instalar las dependencias de PIP. Si no se especifica una versión en el entorno, se usará la versión más reciente.
Se recomienda usar una versión conocida de PIP para evitar problemas transitorios o cambios importantes que puedan deberse a la versión más reciente de la herramienta.

Considere la posibilidad de anclar la versión de PIP en su entorno si ve el siguiente mensaje:

   ```
   Warning: you have pip-installed dependencies in your environment file, but you do not list pip itself as one of your conda dependencies. Conda may not use the correct pip to install your packages, and they may end up in the wrong place. Please add an explicit pip dependency. I'm adding one for you, but still nagging you.
   ```

Error de subproceso de PIP:
   ```
   ERROR: THESE PACKAGES DO NOT MATCH THE HASHES FROM THE REQUIREMENTS FILE. If you have updated the package versions, update the hashes as well. Otherwise, examine the package contents carefully; someone may have tampered with them.
   ```

La instalación de PIP se puede bloquear en un bucle infinito si hay conflictos que no se pueden resolver en las dependencias. Si trabaja de forma local, cambie a una versión de PIP anterior a la 20.3. En un entorno de Conda creado a partir de un archivo YAML, solo verá este problema si conda-forge es el canal de prioridad más alta. Para mitigar el problema, especifique explícitamente una versión de PIP anterior a 20.3 (20.3 o 20.2.4 u otra versión) como dependencia de Conda en el archivo de especificación de Conda.

### <a name="modulenotfounderror-no-module-named-distutilsdir_util"></a>ModuleNotFoundError: No hay ningún módulo denominado "distutils.dir_util"

Al configurar el entorno, a veces se encontrará con el problema **ModuleNotFoundError: No module named 'distutils.dir_util'** (ModuleNotFoundError: No hay ningún módulo denominado "distutils.dir_util"). Para corregirlo, ejecute el siguiente comando:

```bash
apt-get install -y --no-install-recommends python3 python3-distutils && \
ln -sf /usr/bin/python3 /usr/bin/python
```

Cuando trabaje con un Dockerfile, ejecútelo como parte de un comando RUN.

```dockerfile
RUN apt-get update && \
  apt-get install -y --no-install-recommends python3 python3-distutils && \
  ln -sf /usr/bin/python3 /usr/bin/python
```

Al ejecutar este comando, se instalan las dependencias de módulo correctas para configurar el entorno. 

## <a name="service-side-failures"></a>Errores en el lado de servicio

Consulte los siguientes escenarios para solucionar los posibles errores del lado de servicio.

### <a name="youre-unable-to-pull-an-image-from-a-container-registry-or-the-address-couldnt-be-resolved-for-a-container-registry"></a>No se puede extraer una imagen de un registro de contenedor o no se pudo resolver la dirección de un registro de contenedor

Problemas posibles:
- Es posible que el nombre de la ruta de acceso al registro de contenedor no se resuelva correctamente. Compruebe que los nombres de las imágenes usan barras diagonales dobles y que la dirección de las barras diagonales en los hosts de Linux y Windows es la correcta.
- Si un registro de contenedor que está detrás de una red virtual usa un punto de conexión privado en [una región no admitida](../private-link/private-link-overview.md#availability), configure el registro de contenedor mediante el punto de conexión de servicio (acceso público) desde el portal, e inténtelo de nuevo.
- Después de colocar el registro de contenedor detrás de una red virtual, ejecute la [plantilla de Azure Resource Manager](./how-to-network-security-overview.md) para que el área de trabajo pueda comunicarse con la instancia del registro de contenedor.

### <a name="you-get-a-401-error-from-a-workspace-container-registry"></a>Obtiene un error 401 de un registro de contenedor del área de trabajo

Vuelva a sincronizar las claves de almacenamiento mediante [ws.sync_keys()](/python/api/azureml-core/azureml.core.workspace.workspace#sync-keys--).

### <a name="the-environment-keeps-throwing-a-waiting-for-other-conda-operations-to-finish-error"></a>El entorno sigue devolviendo el error "Esperando a que finalicen otras operaciones de Conda..."

Si hay una compilación de imagen en curso, el cliente SDK bloquea Conda. Si el usuario bloqueó o canceló incorrectamente el proceso, Conda permanecerá en estado bloqueado. Para solucionar este problema, elimine manualmente el archivo de bloqueo. 

### <a name="your-custom-docker-image-isnt-in-the-registry"></a>La imagen personalizada de Docker no está en el registro

Compruebe si se usa la [etiqueta correcta](./how-to-use-environments.md#create-an-environment) y que `user_managed_dependencies = True`. `Environment.python.user_managed_dependencies = True` deshabilita Conda y usa los paquetes instalados del usuario.

### <a name="you-get-one-of-the-following-common-virtual-network-issues"></a>Obtiene uno de los siguientes problemas comunes de la red virtual

- Compruebe que la cuenta de almacenamiento, el clúster de proceso y el registro de contenedor estén en la misma subred de la red virtual.
- Cuando el registro de contenedor está detrás de una red virtual, no se puede usar directamente para compilar imágenes. Deberá usar el clúster de proceso para compilar imágenes.
- Es posible que sea necesario agregar almacenamiento detrás de una red virtual si:
    - Se usan inferencias o Private Wheel.
    - Se ven errores de servicio 403 (no autorizado).
    - No se pueden obtener los detalles de la imagen de Azure Container Registry.

### <a name="the-image-build-fails-when-youre-trying-to-access-network-protected-storage"></a>Se produce un error en la compilación de la imagen al intentar acceder al almacenamiento protegido por la red

- Las tareas de Azure Container Registry no funcionan detrás de una red virtual. Si el usuario tiene el registro de contenedor detrás de una red virtual, debe usar el clúster de proceso para compilar una imagen.
- El almacenamiento debe estar detrás de la red virtual para poder extraer las dependencias de él.

### <a name="you-cant-run-experiments-when-storage-has-network-security-enabled"></a>No se pueden ejecutar experimentos cuando el almacenamiento tiene habilitada la seguridad de red

Si usa imágenes de Docker predeterminadas y habilita las dependencias administradas por el usuario, debe usar las [etiquetas de servicio](./how-to-network-security-overview.md) MicrosoftContainerRegistry y AzureFrontDoor.FirstParty para agregar Azure Container Registry y sus dependencias a la lista de permitidos.

 Para obtener más información, consulte [Habilitar redes virtuales](./how-to-network-security-overview.md).

### <a name="you-need-to-create-an-icm"></a>Debe crear un ICM

Al crear o asignar un ICM a MetaStore, incluya la incidencia de soporte de CSS para que podamos comprender mejor el problema.

## <a name="next-steps"></a>Pasos siguientes

- [Entrenamiento de un modelo de Machine Learning para clasificar flores](how-to-train-scikit-learn.md)
- [Entrenamiento de un modelo de Machine Learning mediante una imagen personalizada de Docker](how-to-train-with-custom-image.md)
