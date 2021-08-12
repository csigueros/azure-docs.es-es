---
title: Representación de un modelo con Unity
description: Inicio rápido que guía al usuario por los pasos necesarios para representar un modelo
author: florianborn71
ms.author: flborn
ms.date: 01/23/2020
ms.topic: quickstart
ms.openlocfilehash: 0c5ae9dd292aeef8aa1e052cf276568e84bdd95d
ms.sourcegitcommit: b5508e1b38758472cecdd876a2118aedf8089fec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113586877"
---
# <a name="quickstart-render-a-model-with-unity"></a>Inicio rápido: Representación de un modelo con Unity

En esta guía de inicio rápido se describe cómo ejecutar un ejemplo de Unity que representa un modelo integrado de forma remota mediante el servicio Azure Remote Rendering (ARR).

No entraremos en detalles sobre la API de ARR o cómo configurar un nuevo proyecto de Unity. Estos temas se tratan en [Tutorial: Visualización de modelos representados de forma remota](../tutorials/unity/view-remote-models/view-remote-models.md).

En este artículo de inicio rápido aprenderá lo siguiente:
> [!div class="checklist"]
>
>* Configurar el entorno de desarrollo local
>* Obtener y compilar la aplicación de ejemplo de inicio rápido de ARR para Unity
>* Representar un modelo en la aplicación de ejemplo de inicio rápido de ARR

## <a name="prerequisites"></a>Requisitos previos

Para obtener acceso al servicio Azure Remote Rendering, primero debe [crear una cuenta](../how-tos/create-an-account.md).

Debe instalar el software siguiente:

* Windows SDK 10.0.18362.0 [(descargar)](https://developer.microsoft.com/windows/downloads/windows-10-sdk)
* La versión más reciente de Visual Studio 2019 [(descargar)](https://visualstudio.microsoft.com/vs/older-downloads/)
* [Las herramientas de Visual Studio para Mixed Reality](/windows/mixed-reality/install-the-tools). En concreto, las instalaciones de *carga de trabajo* siguientes son obligatorias:
  * **Desarrollo para el escritorio con C++**
  * **Desarrollo de Plataforma universal de Windows (UWP)**
* GIT [(descargar)](https://git-scm.com/downloads)
* Unity (consulte los [requisitos del sistema](../overview/system-requirements.md#unity) para versiones compatibles).

## <a name="clone-the-sample-app"></a>Clonación de la aplicación de ejemplo

Abra un símbolo del sistema (escriba `cmd` en el menú Inicio de Windows) y cambie a un directorio en el que desee almacenar el proyecto de ejemplo de ARR.

Ejecute los comandos siguientes:

```cmd
mkdir ARR
cd ARR
git clone https://github.com/Azure/azure-remote-rendering
powershell -ExecutionPolicy RemoteSigned -File azure-remote-rendering\Scripts\DownloadUnityPackages.ps1
```

El último comando crea un subdirectorio en el directorio ARR que contiene los diversos proyectos de ejemplo para Azure Remote Rendering.

La aplicación de ejemplo del artículo de inicio rápido para Unity se encuentra en el subdirectorio *Unity/Quickstart*.

## <a name="rendering-a-model-with-the-unity-sample-project"></a>Representación de un modelo con el proyecto de ejemplo Unity

Abra el centro de conectividad de Unity y agregue el proyecto de ejemplo, que está en la carpeta *ARR\azure-remote-rendering\Unity\Quickstart*.
Abra el proyecto. Si es necesario, permita que Unity actualice el proyecto a la versión instalada.

El modelo predeterminado que se representa es un [modelo de ejemplo integrado](../samples/sample-model.md). Le mostraremos cómo convertir un modelo personalizado mediante el servicio de conversión de ARR en el [siguiente artículo de inicio rápido](convert-model.md).

### <a name="enter-your-account-info"></a>Especificación de la información de la cuenta

1. En el explorador de recursos de Unity, vaya a la carpeta *Scenes* y abra la escena **Quickstart** (Inicio rápido).
1. En la *jerarquía*, seleccione el objeto de juego **RemoteRendering**.
1. En *Inspector*, escriba las [credenciales de la cuenta](../how-tos/create-an-account.md). Si aún no tiene una cuenta, [créela](../how-tos/create-an-account.md).

![Información de cuenta de ARR](./media/arr-sample-account-info.png)

> [!IMPORTANT]
> Establezca **RemoteRenderingDomain** en `<region>.mixedreality.azure.com`, donde `<region>` es [una de las regiones disponibles cercanas](../reference/regions.md).
> Establezca **AccountDomain** en el [dominio de la cuenta](../how-tos/create-an-account.md#retrieve-the-account-information), tal como se muestra en Azure Portal.

Más adelante implementaremos este proyecto en un dispositivo HoloLens y nos conectaremos al servicio Remote Rendering desde ese dispositivo. Dado que no hay ninguna manera fácil de escribir las credenciales en el dispositivo, el ejemplo de inicio rápido **guardará las credenciales en la escena de Unity**.

> [!WARNING]
> Asegúrese de no revisar el proyecto con las credenciales guardadas en algún repositorio donde se filtre información secreta de acceso.

### <a name="create-a-session-and-view-the-default-model"></a>Creación de una sesión y visualización del modelo predeterminado

Presione el botón **Play** (Reproducir) de Unity para iniciar la sesión. Debería ver una superposición con el texto de estado en la parte inferior de la ventanilla en el panel *Game* (Juego). La sesión se someterá a una serie de transiciones de estado. En el estado **Iniciándose**, el servidor se pone en marcha, lo que tarda varios minutos. En caso de que se realice correctamente, realiza la transición al estado **Ready** (Preparado). Ahora la sesión entra en el estado **Conectando**, donde intenta alcanzar el tiempo de ejecución de representación en ese servidor. Cuando se realiza correctamente, el ejemplo cambia al estado **Connected** (Conectado). En este punto, comenzará a descargar el modelo para la representación. Debido al tamaño del modelo, la descarga puede tardar unos minutos más. A continuación, aparecerá el modelo representado de forma remota.

![Salida generada por el ejemplo](media/arr-sample-output.png)

Felicidades. Ahora está viendo un modelo representado de forma remota.

## <a name="inspecting-the-scene"></a>Inspección de la escena

Cuando se ejecute la conexión de representación remota, el panel Inspector se actualizará con información de estado adicional: ![Reproducción de un ejemplo de Unity](./media/arr-sample-configure-session-running.png)

Ahora puede explorar el gráfico de escenas seleccionando el nuevo nodo y haciendo clic en **Show children** (Mostrar elementos secundarios) en Inspector.

![Jerarquía de Unity](./media/unity-hierarchy.png)

Hay un objeto [plano de corte](../overview/features/cut-planes.md) en la escena. Intente habilitarlo en las propiedades y moverlo:

![Cambio del plano de corte](media/arr-sample-unity-cutplane.png)

Para sincronizar las transformaciones, haga clic en **Sync now** (Sincronizar ahora) o active la opción **Sync every frame** (Sincronizar cada fotograma). En el caso de las propiedades de los componentes, basta con cambiarlas.

## <a name="next-steps"></a>Pasos siguientes

En el siguiente artículo de inicio rápido, implementaremos el ejemplo en un dispositivo HoloLens para ver el modelo representado de forma remota en su tamaño original.

> [!div class="nextstepaction"]
> [Inicio rápido: Implementación de un ejemplo de Unity en un HoloLens](deploy-to-hololens.md)

Como alternativa, el ejemplo también se puede implementar en un equipo de escritorio.

> [!div class="nextstepaction"]
> [Inicio rápido: Implementación de un ejemplo de Unity en el escritorio](deploy-to-desktop.md)