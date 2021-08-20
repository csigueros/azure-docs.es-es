---
title: 'Inicio rápido: Tutorial detallado de MRTK'
description: En este inicio rápido, obtendrá una descripción detallada de la aplicación de ejemplo de Unity para MRTK de Azure Object Anchors.
author: RamonArguelles
manager: virivera
ms.author: rgarcia
ms.date: 07/12/2021
ms.topic: quickstart
ms.service: azure-object-anchors
ms.openlocfilehash: 515d44cc7cb40972c67424799163bc57deba9182
ms.sourcegitcommit: b59e0afdd98204d11b7f9b6a3e55f5a85d8afdec
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114371566"
---
# <a name="quickstart-in-depth-mrtk-walkthrough"></a>Inicio rápido: Tutorial detallado de MRTK

En esta guía se proporciona una descripción detallada de la [aplicación de ejemplo de Unity para MRTK de Azure Object Anchors](get-started-unity-hololens-mrtk.md). Está pensado para proporcionar conclusiones sobre el diseño del ejemplo. Al leer esta guía, los desarrolladores pueden acelerar su comprensión de los conceptos clave de Azure Object Anchors en el ejemplo.

## <a name="project-layout"></a>Diseño del proyecto

Los recursos creados para el ejemplo de Unity para MRTK de Azure Object Anchors se almacenan en `Assets\MixedReality.AzureObjectAnchors`. Las subcarpetas son las siguientes:

- **Iconos**
  - Contiene algunos iconos personalizados que se usan en el menú orientado al usuario.
- **Materiales**
  - Contiene sombreadores y materiales para la visualización de reconstrucción de la superficie y un sombreador *solo de profundidad*, que escribe en el búfer de profundidad para facilitar la estabilización del holograma alrededor del texto.
- **Prefabs**
  - Contiene objetos `GameObjects` de Unity reutilizables. En concreto, `TrackableObjectPrefab` representa el objeto creado cuando Azure Object Anchors detecta un objeto.
- **Perfiles**
  - Contiene perfiles de MRTK personalizados que describen la funcionalidad mínima necesaria de MRTK para habilitar la aplicación.
- **Escenas**
  - Contiene `AOASampleTestScene`, que es la escena principal del ejemplo.
- **Scripts**
  - Contiene los scripts escritos para el ejemplo.

## <a name="unity-scene"></a>Escena de Unity

**Área de juego de Mixed Reality**: principalmente código reutilizable de MRTK

- <a href="/windows/mixed-reality/develop/unity/mrtk-getting-started" target="_blank">Introducción a MRTK para Unity</a>.
- Hay una interfaz de usuario conectada a la cámara que detalla el estado general de Azure Object Anchors (vea `OverlayDebugText.cs`).

**Área de juego de objetos de Mixed Reality**: principalmente relacionado con Azure Object Anchors, pero con algunos controles de MRTK. Dos scripts, `TrackableObjectSearch` y `ObjectTracker`, asociados al elemento primario, representan la interfaz principal con Azure Object Anchors.

- **Menú**
  - Principalmente código de MRTK, pero las interacciones de la interfaz de usuario se dirigen a la funcionalidad de Azure Object Anchors.
  - El script `TrackableObjectMenu` adjunto realiza el trabajo principal de enrutar eventos de la interfaz de usuario de MRTK a las instancias adecuadas de Azure Object Anchors.
  - <a href="/windows/mixed-reality/design/hand-menu" target="_blank">Menú de mano de MRTK</a>.
- **WorkspaceBoundingBox**
  - Contiene scripts de MRTK asociados al control de un rectángulo de selección.
  - También contiene un objeto secundario `ModelVis`, que se usa para visualizar el modelo de Azure Object Anchors antes de que se haya producido una detección para facilitar la alineación durante detecciones complicadas.

## <a name="menu-walkthrough"></a>Tutorial de los menús

Antes de analizar los scripts, se verán los elementos de menú. De este modo, se puede hacer referencia a cómo interactúan estos elementos de menú con los scripts.

:::image type="content" source="./media/mrtk-menus.png" alt-text="Menús de MRTK":::

Los submenús inferior y derecho no aparecen automáticamente, pero se alternan por medio de `Search Area Settings` y `Tracker Settings`, respectivamente.

### <a name="main-menu"></a>Menú principal

- **Iniciar búsqueda**
  - Inicia la búsqueda de objetos en el área de búsqueda especificada.
- **Alternar asignación espacial**
  - Alterna entre mostrar la asignación espacial durante la búsqueda, mostrar siempre la asignación espacial y no mostrarla nunca.
- **Configuración del seguimiento / Configuración del área de búsqueda**
  - Alterna los submenús correspondientes.
- **Iniciar seguimiento/Detener seguimiento**
  - Inicia o finaliza el seguimiento de diagnóstico.
- **Cargar seguimiento**
  - Carga el seguimiento de diagnósticos en Microsoft para el análisis de depuración.

### <a name="tracker-settings"></a>Menú de configuración de seguimiento

- **Alta precisión**
  - Cuando está habilitado, `ObjectInstanceTrackingMode` se establece en `HighLatencyAccuratePosition`.
  - Cuando está deshabilitado, `ObjectInstanceTrackingMode` se establece en `LowLatencyCoarsePosition`.
- **Alineación vertical relajada**
  - Cuando se habilita, establece `AllowedVerticalOrientationInDegrees` en 10 grados. Esta característica permite la detección de objetos situados sobre rampas.
  - Cuando se deshabilita, establece `AllowedVerticalOrientationInDegrees` en 0 grados.
- **Permitir cambio de escala**
  - Cuando se habilita, establece `MaxScaleChange` en 0,1. Esta característica permite que Azure Object Anchors ajuste la escala del objeto en función de los ajustes de escala de seguimiento de HoloLens.
  - Cuando se deshabilita, establece `MaxScaleChange` en 0.
- **Control deslizante de proporción de cobertura**
  - Ajusta la proporción de cobertura necesaria para que la detección de objetos considere una coincidencia. Los valores más bajos aumentarán la frecuencia de detección. Esta característica puede ser deseable para objetos difíciles de detectar, pero también puede provocar un aumento de las detecciones de objetos falsos positivos.

### <a name="search-area-settings"></a>Menú de configuración del área de búsqueda

- **Bloquear Área de búsqueda**
  - Cuando se habilita, impide que el usuario cambie el área de búsqueda.
- **Ajustar automáticamente el área de búsqueda**
  - Cuando se habilita, permite que los scripts muevan el área de búsqueda para refinar el proceso de detección.
- **Malla de ciclo**
  - Recorrerá las mallas para los objetos `.ou` que se pueden detectar y ninguna malla.

## <a name="scripts"></a>Scripts

- **AutonomousSearchArea.cs**
  - Este script está asociado a `WorkspaceBoundingBox`. El script intenta escalar y colocar `WorkspaceBoundingBox` de forma automática. Se habilita cuando `Auto-Adjust Search Area` está habilitado.
- **ObjectAnchorsSubscription.cs**
  - Este script contiene la información necesaria para cargar los datos de diagnóstico.
- **ObjectTracker.cs**
  - Este script es el puente entre Unity y los aspectos de detección del SDK de Azure Object Anchors.
- **ObjectTrackerDiagnostics.cs**
  - Este script administra la característica de diagnóstico del SDK de Azure Object Anchors.
- **OverlayDebugText.cs**
  - Este script está asociado a la cámara principal. Es responsable de mostrar el estado general del ejemplo y de Azure Object Anchors al usuario.
- **PositionDebugInfo.cs**
  - Este script simplemente obliga al texto de depuración asociado a un objeto detectado a situarse delante del usuario.
- **SearchAreaController.cs**
  - Este script administra el estado del objeto `WorkspaceBoundingBox` que se usa para indicar dónde debe buscar los objetos Azure Object Anchors.
- **SearchAreaModelVisualization.cs**
  - Este script habilita la funcionalidad `Cycle Mesh` de `Search Area Settings`.
- **SpatialMappingController.cs**
  - Este script administra cuándo se debe habilitar la asignación espacial en función de la interacción con `Toggle Spatial Mapping` en `Main Menu`.
- **TextToSpeech.cs**
  - Este script toma texto y lo convierte en voz.
- **TrackableObjectData.cs**
  - Este script representa los datos de los objetos de los que se puede realizar el seguimiento.
- **TrackableObjectDataLoader.cs**
  - Este script realiza el trabajo de cargar los archivos `.ou` y convertirlos en elementos `TrackableObjectData`.
- **TrackableObjectMenu.cs**
  - La mayoría de las interacciones del usuario fluyen de la interfaz de usuario a este script y, después, al script adecuado. Por ejemplo, `TrackableObjectMenu` tiene `ToggleSpatialMapping`, que se enruta a `SpatialMappingController`.
- **TrackableObjectSearch.cs**
  - Este script realiza tareas de administración ligera del área de búsqueda. En concreto, la interacción en la que el usuario pulsa en el espacio y el cuadro de búsqueda se coloca delante del usuario. También hace que el interior del cuadro de búsqueda no se represente durante la búsqueda o mientras se detecta un objeto, para evitar que la representación completa oculte el objeto.
- **TrackedObject.cs**
  - Script principal en `TrackableObjectPrefab`. Mantiene el estado de visualización de un objeto detectado por Azure Object Anchors.
- **TrackedObjectData.cs**
  - Este script contiene información sobre un objeto de Azure Object Anchors del que se realiza el seguimiento.

## <a name="prefabs"></a>Objetos prefabricados

- **TrackableObjectPrefab**
  - Objeto prefabricado que se crea cuando AzureObjectAnchors detecta un objeto. Un elemento secundario de este Objeto prefabricado, `LogicalCenter`, representa el centro del objeto y es un lugar adecuado para comenzar al intentar adjuntar contenido secundario a los objetos detectados. El texto de información que muestra el ejemplo se adjunta a este centro lógico.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Conceptos: Información general del SDK](../concepts/sdk-overview.md)

> [!div class="nextstepaction"]
> [P+F](../faq.md)

> [!div class="nextstepaction"]
> [Biblioteca cliente de Azure Object Anchors para .NET: versión 0.3.0-beta.1](/dotnet/api/overview/azure/mixedreality.objectanchors.conversion-readme-pre)
