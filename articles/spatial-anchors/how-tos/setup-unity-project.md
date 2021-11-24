---
title: Instalación de Azure Spatial Anchors para Unity
description: Configure un proyecto de Unity para que use Azure Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/12/2021
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 945a95054f281e0a5920232a729de4087d1deab6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132490523"
---
# <a name="configuring-azure-spatial-anchors-in-a-unity-project"></a>Configuración de Azure Spatial Anchors en un proyecto de Unity

En esta guía se muestra cómo empezar a trabajar con el SDK de Azure Spatial Anchors en el proyecto de Unity.

## <a name="project-requirements"></a>Requisitos de proyecto

[!INCLUDE [Unity Project Requirements](../../../includes/spatial-anchors-unity-project-requirements.md)]

## <a name="configuring-a-project"></a>Configuración de un proyecto

Antes de incluir el SDK de Azure Spatial Anchors en el proyecto de Unity, asegúrese de instalar los paquetes [necesarios](#project-requirements) mediante el administrador de paquetes de Unity.

### <a name="download-asa-packages"></a>Descarga de paquetes de ASA
[!INCLUDE [Download Unity Packages](../../../includes/spatial-anchors-unity-download-packages.md)]

### <a name="import-asa-packages"></a>Importación de paquetes de ASA
[!INCLUDE [Import Unity Packages](../../../includes/spatial-anchors-unity-import-packages.md)]

### <a name="hololens-only"></a>Solo HoloLens

#### <a name="configure-your-unity-project-xr-settings"></a>Configuración de XR del proyecto de Unity
Al desarrollar aplicaciones de Mixed Reality en HoloLens, debe establecer la configuración de XR en Unity. Consulte [Establecimiento de la configuración de XR - Mixed Reality | Microsoft Docs](/windows/mixed-reality/develop/unity/xr-project-setup?tabs=openxr) y [Elección de una versión de Unity y un complemento XR - Mixed Reality | Microsoft Docs](/windows/mixed-reality/develop/unity/choosing-unity-version) para obtener más detalles.

La versiones 2.9.0 o anteriores del SDK de Azure Spatial Anchors solo proporcionan compatibilidad para el complemento XR de Windows (com.unity.xr.windowsmr) y, por lo tanto, el paquete de Windows de Azure Spatial Anchor tiene una dependencia explícita del complemento XR de Windows.

Las versiones 2.10.0 o posteriores del SDK de Azure Spatial Anchors admiten tanto el complemento OpenXR de Mixed Reality ([com.microsoft.mixedreality.openxr](https://dev.azure.com/aipmr/MixedReality-Unity-Packages/_packaging?_a=package&feed=Unity-packages&view=overview&package=com.microsoft.mixedreality.openxr&protocolType=Npm)) como el complemento XR de Windows ([com.unity.xr.windowsmr](https://docs.unity3d.com/Manual/com.unity.xr.windowsmr.html)). Deberá incluir el paquete com.microsoft.mixedreality.openxr o el com.unity.xr.windowsmr en el proyecto, según su elección.

#### <a name="configure-your-unity-project-capabilities"></a>Configuración de las funcionalidades del proyecto de Unity

Asegúrese de habilitar las siguientes funcionalidades en el proyecto de Unity:
- SpatialPerception
- InternetClient
- PrivateNetworkClientServer

> [!WARNING]
> Si no se habilita la funcionalidad PrivateNetworkClientServer, se puede producir un error al consultar los delimitadores cuando el dispositivo usa una red que está configurada para ser privada.
### <a name="android-only-configure-the-maintemplategradle-file"></a>Solo Android: configuración del archivo mainTemplate.gradle

1. Vaya a **Edit** > **Project Settings** > **Player** (Editar > Configuración del proyecto > Reproductor).
2. En **Inspector Panel** (Panel de inspector) de **Player Settings** (Configuración del Reproductor), seleccione el icono de **Android**.
3. En la sección **Build**, active la casilla **Custom Main Gradle Template** (Plantilla principal de Gradle personalizada) para generar una plantilla personalizada de Gradle en `Assets\Plugins\Android\mainTemplate.gradle`.
4. Abre el archivo `mainTemplate.gradle` en un editor de texto.
5. En la sección `dependencies`, pegue las siguientes dependencias:

    ```gradle
    implementation('com.squareup.okhttp3:okhttp:[3.11.0]')
    implementation('com.microsoft.appcenter:appcenter-analytics:[1.10.0]')
    ```

Cuando haya terminado, la sección `dependencies` debe tener un aspecto similar al siguiente:

[!code-gradle[AzureSpatialAnchorsScript](../../../includes/spatial-anchors-unity-android-gradle-setup.md?range=9-13&highlight=3-4)]

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Cómo: Creación y localización de delimitadores en Unity](./create-locate-anchors-unity.md)