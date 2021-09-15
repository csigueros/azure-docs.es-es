---
title: Enlace de gráficos
description: Configuración de casos de uso y enlaces de gráficos
author: florianborn71
manager: jlyons
services: azure-remote-rendering
titleSuffix: Azure Remote Rendering
ms.author: flborn
ms.date: 12/11/2019
ms.topic: conceptual
ms.service: azure-remote-rendering
ms.custom: devx-track-csharp
ms.openlocfilehash: 4c9bfc12a1af68e56aacea5b73bd2b4ddc6f857c
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221607"
---
# <a name="graphics-binding"></a>Enlace de gráficos

Para poder usar Azure Remote Rendering en una aplicación personalizada, debe integrarse en la canalización de representación de la aplicación. Esta integración es responsabilidad del enlace de gráficos.

Una vez configurado, el enlace de gráficos proporciona acceso a varias funciones que afectan a la imagen representada. Estas funciones se pueden dividir en dos categorías: funciones generales que siempre están disponibles y funciones específicas que solo son relevantes para el tipo `Microsoft.Azure.RemoteRendering.GraphicsApiType` seleccionado.

## <a name="graphics-binding-in-unity"></a>Enlace de gráficos en Unity

En Unity, el enlace completo se controla mediante el struct `RemoteUnityClientInit` pasado a `RemoteManagerUnity.InitializeManager`. Para establecer el modo gráfico, el campo `GraphicsApiType` tiene que establecerse en el enlace elegido. El campo se rellenará automáticamente en función de si un objeto XRDevice está presente. El comportamiento se puede invalidar manualmente con los comportamientos siguientes:

* **HoloLens 2:** el enlace de gráficos [OpenXR](#openxr)[o Windows Mixed Reality](#windows-mixed-reality) se usa en función del complemento Unity XR activo.
* **Aplicación de escritorio de UWP plana**: siempre se usa [Simulación](#simulation).
* **Editor de Unity**: siempre se usa [Simulación](#simulation), a menos que se conecte un casco de realidad virtual de WMR, en cuyo caso se deshabilitará ARR para permitir que depure las partes no relacionadas con ARR de la aplicación. Vea también el artículo sobre cómo [usar Holographic Remoting](../how-tos/unity/holographic-remoting.md).

La otra parte relevante para Unity es el acceso al [enlace básico](#access); se pueden saltar todas las otras secciones siguientes.

## <a name="graphics-binding-setup-in-custom-applications"></a>Configuración del enlace de gráficos en aplicaciones personalizadas

Para seleccionar un enlace de gráficos, siga estos dos pasos: En primer lugar, el enlace de gráficos debe inicializarse estáticamente cuando se inicializa el programa:

```cs
RemoteRenderingInitialization managerInit = new RemoteRenderingInitialization();
managerInit.GraphicsApi = GraphicsApiType.OpenXrD3D11;
managerInit.ConnectionType = ConnectionType.General;
managerInit.Right = ///...
RemoteManagerStatic.StartupRemoteRendering(managerInit);
```

```cpp
RemoteRenderingInitialization managerInit;
managerInit.GraphicsApi = GraphicsApiType::OpenXrD3D11;
managerInit.ConnectionType = ConnectionType::General;
managerInit.Right = ///...
StartupRemoteRendering(managerInit); // static function in namespace Microsoft::Azure::RemoteRendering

```
La llamada anterior se debe realizar antes de que se acceda a otras API de Remote Rendering.
De forma similar, se debe llamar a la función de-init `RemoteManagerStatic.ShutdownRemoteRendering();` correspondiente una vez que se hayan destruido todos los demás objetos de Remote Rendering.
Para WMR, también es necesario llamar a `StartupRemoteRendering` antes que a cualquier API holográfica. Lo mismo se aplica a OpenXR con respecto a API relacionadas.

## <a name="span-idaccessaccessing-graphics-binding"></a><span id="access">Acceso al enlace de gráficos

Una vez configurado un cliente, se puede tener acceso al enlace de gráficos básico con el captador `RenderingSession.GraphicsBinding`. Por ejemplo, las últimas estadísticas de fotogramas se pueden recuperar de la siguiente manera:

```cs
RenderingSession currentSession = ...;
if (currentSession.GraphicsBinding != null)
{
    FrameStatistics frameStatistics;
    if (currentSession.GraphicsBinding.GetLastFrameStatistics(out frameStatistics) == Result.Success)
    {
        ...
    }
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
if (ApiHandle<GraphicsBinding> binding = currentSession->GetGraphicsBinding())
{
    FrameStatistics frameStatistics;
    if (binding->GetLastFrameStatistics(&frameStatistics) == Result::Success)
    {
        ...
    }
}
```

## <a name="graphic-apis"></a>API de gráficos

Actualmente se pueden seleccionar tres API de gráficos:`OpenXrD3D11`, `WmrD3D11` y `SimD3D11`. Hay una cuarta, `Headless`, pero todavía no se admite en el lado cliente.

### <a name="openxr"></a>OpenXR

`GraphicsApiType.OpenXrD3D11` es el enlace predeterminado que se ejecuta en HoloLens 2. Creará el enlace `GraphicsBindingOpenXrD3d11`. En este modo, Azure Remote Rendering crea una capa de API de OpenXR para integrarse en el entorno de ejecución de OpenXR.

Para tener acceso a los enlaces de gráficos derivados, es necesario convertir la base `GraphicsBinding`.
Para usar el enlace de OpenXR, es necesario hacer tres cosas:

#### <a name="package-custom-openxr-layer-json"></a>Empaquetado de JSON de capa de OpenXR personalizada

Para usar Remote Rendering con OpenXR, es necesario activar la capa de API de OpenXR personalizada. Para ello, se debe realizar la llamada a `StartupRemoteRendering` que se menciona en la sección anterior. Sin embargo, como requisito previo, debe empaquetarse el objeto `XrApiLayer_msft_holographic_remoting.json` con la aplicación para que se pueda cargar. Esta operación se realiza automáticamente si se agrega el paquete NuGet **"Microsoft.Azure.RemoteRendering.Cpp"** a un proyecto.

#### <a name="inform-remote-rendering-of-the-used-xr-space"></a>Notificación a Remote Rendering del espacio XR usado

Este paso es necesario para alinear contenido remoto y representado localmente.

```cs
RenderingSession currentSession = ...;
ulong space = ...; // XrSpace cast to ulong
GraphicsBindingOpenXrD3d11 openXrBinding = (currentSession.GraphicsBinding as GraphicsBindingOpenXrD3d11);
if (openXrBinding.UpdateAppSpace(space) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
XrSpace space = ...;
ApiHandle<GraphicsBindingOpenXrD3d11> openXrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingOpenXrD3d11>();
#ifdef _M_ARM64
    if (openXrBinding->UpdateAppSpace(reinterpret_cast<uint64_t>(space)) == Result::Success)
#else
    if (openXrBinding->UpdateAppSpace(space) == Result::Success)
#endif
{
    ...
}
```

El objeto `XrSpace` anterior es el usado por la aplicación que define el sistema de coordenadas del espacio universal en el que se expresan las coordenadas de la API.

#### <a name="render-remote-image-openxr"></a>Representación de la imagen remota (OpenXR)

Al principio de cada fotograma, el marco remoto debe representarse en el búfer de reserva. Esto se hace mediante una llamada a `BlitRemoteFrame`, que rellenará la información de color y de profundidad de ambos ojos, en el destino de representación enlazado actualmente. Por lo tanto, es importante hacerlo después de enlazar el búfer de reserva completo como destino de representación.

> [!WARNING]
> Después de que la imagen remota se agregue en el búfer de reserva, el contenido local debe representarse mediante una técnica de representación estéreo de un único paso; por ejemplo, con **SV_RenderTargetArrayIndex**. El uso de otras técnicas de representación estéreo, como la representación de cada ojo en un paso independiente, puede dar lugar a una degradación importante del rendimiento o de los artefactos gráficos, y debe evitarse.

```cs
RenderingSession currentSession = ...;
GraphicsBindingOpenXrD3d11 openXrBinding = (currentSession.GraphicsBinding as GraphicsBindingOpenXrD3d11);
openXrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingOpenXrD3d11> openXrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingOpenXrD3d11>();
openXrBinding->BlitRemoteFrame();
```

### <a name="windows-mixed-reality"></a>Windows Mixed Reality

`GraphicsApiType.WmrD3D11` es el enlace de gráficos usado anteriormente para ejecutarse en HoloLens 2. Creará el enlace `GraphicsBindingWmrD3d11`. En este modo, Azure Remote Rendering enlaza directamente a las API holográficas.

Para tener acceso a los enlaces de gráficos derivados, es necesario convertir la base `GraphicsBinding`.
Hay dos tareas que deben realizarse para usar el enlace de WMR:

#### <a name="inform-remote-rendering-of-the-used-coordinate-system"></a>Informar a Remote Rendering del sistema de coordenadas usado

Este paso es necesario para alinear contenido remoto y representado localmente.

```cs
RenderingSession currentSession = ...;
IntPtr ptr = ...; // native pointer to ISpatialCoordinateSystem
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
if (wmrBinding.UpdateUserCoordinateSystem(ptr) == Result.Success)
{
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* ptr = ...; // native pointer to ISpatialCoordinateSystem
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
if (wmrBinding->UpdateUserCoordinateSystem(ptr) == Result::Success)
{
    ...
}
```

El puntero anterior `ptr` debe ser un puntero a un objeto `ABI::Windows::Perception::Spatial::ISpatialCoordinateSystem` nativo que define el sistema de coordenadas del espacio universal en el que se expresan las coordenadas de la API.

#### <a name="render-remote-image-wmr"></a>Representación de la imagen remota (WMR)

En este caso se aplican las mismas consideraciones que en el anterior de OpenXR. Las llamadas de API tienen un aspecto similar al siguiente:

```cs
RenderingSession currentSession = ...;
GraphicsBindingWmrD3d11 wmrBinding = (currentSession.GraphicsBinding as GraphicsBindingWmrD3d11);
wmrBinding.BlitRemoteFrame();
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
ApiHandle<GraphicsBindingWmrD3d11> wmrBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingWmrD3d11>();
wmrBinding->BlitRemoteFrame();
```

### <a name="simulation"></a>Simulation

`GraphicsApiType.SimD3D11` es el enlace de simulación y, si se selecciona, crea el enlace de gráficos de `GraphicsBindingSimD3d11`. Esta interfaz se utiliza para simular el movimiento principal, por ejemplo, en una aplicación de escritorio, y representa una imagen monoscópica.

Para implementar el enlace de simulación, es importante comprender la diferencia entre la cámara local y el fotograma remoto, tal y como se describe en la página [Cámara](../overview/features/camera.md).

Se necesitan dos cámaras:

* **Cámara local**: Esta cámara representa la posición actual de la cámara controlada por la lógica de la aplicación.
* **Cámara proxy**: Esta cámara coincide con el *fotograma remoto* actual enviado por el servidor. Como hay un retraso entre el cliente que solicita un fotograma y su llegada, el *fotograma remoto* siempre va un poco por detrás del movimiento de la cámara local.

El enfoque básico es que tanto la imagen remota como el contenido local se representan en un destino fuera de la pantalla mediante la cámara proxy. La imagen proxy se reproyecta en el espacio de la cámara local, lo que se explica más detalladamente en la [reproyección en fase tardía](../overview/features/late-stage-reprojection.md).

`GraphicsApiType.SimD3D11` también admite la representación estereoscópica, que debe habilitarse durante la siguiente llamada de instalación de `InitSimulation` más adelante. La configuración es un poco más complicada y funciona de la siguiente manera:

#### <a name="create-proxy-render-target"></a>Creación del destino de representación proxy

El contenido remoto y local debe representarse en un destino de representación de color o profundidad fuera de pantalla, denominado "proxy", con los datos de la cámara proxy proporcionados por la función `GraphicsBindingSimD3d11.Update`.

El proxy debe coincidir con la resolución del búfer de reserva y debe estar en formato *DXGI_FORMAT_R8G8B8A8_UNORM* o *DXGI_FORMAT_B8G8R8A8_UNORM*. En el caso de la representación estereoscópica, la textura del proxy de color y la textura del proxy de profundidad (si se usa la profundidad) deben tener dos capas de matriz en lugar de una. Una vez que una sesión está lista, es necesario llamar a `GraphicsBindingSimD3d11.InitSimulation` antes de conectarse a ella:

```cs
RenderingSession currentSession = ...;
IntPtr d3dDevice = ...; // native pointer to ID3D11Device
IntPtr color = ...; // native pointer to ID3D11Texture2D
IntPtr depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
simBinding.InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

```cpp
ApiHandle<RenderingSession> currentSession = ...;
void* d3dDevice = ...; // native pointer to ID3D11Device
void* color = ...; // native pointer to ID3D11Texture2D
void* depth = ...; // native pointer to ID3D11Texture2D
float refreshRate = 60.0f; // Monitor refresh rate up to 60hz.
bool flipBlitRemoteFrameTextureVertically = false;
bool flipReprojectTextureVertically = false;
bool stereoscopicRendering = false;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();
simBinding->InitSimulation(d3dDevice, depth, color, refreshRate, flipBlitRemoteFrameTextureVertically, flipReprojectTextureVertically, stereoscopicRendering);
```

La función init debe proporcionarse con punteros al dispositivo d3d nativo, así como a la textura de color y profundidad del destino de representación proxy. Una vez inicializado, se puede llamar a `RenderingSession.ConnectAsync` y `Disconnect` varias veces, pero cuando se cambia a una sesión diferente, se debe llamar primero a `GraphicsBindingSimD3d11.DeinitSimulation` en la sesión anterior antes de que se pueda llamar a `GraphicsBindingSimD3d11.InitSimulation` en otra sesión.

#### <a name="render-loop-update"></a>Actualización del bucle de representación

El proceso de actualización del bucle de representación consta de varios pasos:

1. En cada fotograma, antes de que tenga lugar la representación, se llama a `GraphicsBindingSimD3d11.Update` con la transformación de la cámara actual que se envía al servidor con el fin de representarse. Al mismo tiempo, la transformación del proxy devuelto debe aplicarse a la cámara proxy para representarse en el destino de representación proxy.
Si el valor de la actualización del proxy devuelto `SimulationUpdate.frameId` es NULL, todavía no hay datos remotos. En ese caso, en lugar de representarse en el destino de representación proxy, cualquier contenido local se debe representar directamente en el búfer de reserva, utilizando los datos de la cámara actuales e ignorando los dos pasos siguientes.
1. Ahora la aplicación debe enlazar el destino de representación proxy y llamar a `GraphicsBindingSimD3d11.BlitRemoteFrameToProxy`. Esta acción rellenará la información de profundidad y color remota en el destino de representación proxy. Ahora se puede representar cualquier contenido local en el proxy mediante la transformación de la cámara proxy.
1. Después, el búfer de reserva debe estar enlazado como un destino de representación y llamarse a `GraphicsBindingSimD3d11.ReprojectProxy` en el punto en el que se puede presentar el búfer de reserva.

```cs
RenderingSession currentSession = ...;
GraphicsBindingSimD3d11 simBinding = (currentSession.GraphicsBinding as GraphicsBindingSimD3d11);
SimulationUpdateParameters updateParameters = new SimulationUpdateParameters();
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult = new SimulationUpdateResult();
simBinding.Update(updateParameters, out updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding.BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding.ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

```cpp
ApiHandle<RenderingSession> currentSession;
ApiHandle<GraphicsBindingSimD3d11> simBinding = currentSession->GetGraphicsBinding().as<GraphicsBindingSimD3d11>();

SimulationUpdateParameters updateParameters;
// Fill out camera data with current camera data
// (see "Simulation Update structures" section below)
...
SimulationUpdateResult updateResult;
simBinding->Update(updateParameters, &updateResult);
// Is the frame data valid?
if (updateResult.FrameId != 0)
{
    // Bind proxy render target
    simBinding->BlitRemoteFrameToProxy();
    // Use proxy camera data to render local content
    ...
    // Bind back buffer
    simBinding->ReprojectProxy();
}
else
{
    // Bind back buffer
    // Use current camera data to render local content
    ...
}
```

#### <a name="simulation-update-structures"></a>Estructuras de actualización de simulación

Cada fotograma, la **actualización de bucle de representación** de la sección anterior requiere la entrada de un intervalo de parámetros de cámara correspondientes a la cámara local y devuelve un conjunto de parámetros de cámara que corresponden a la cámara del siguiente fotograma disponible. Estos dos conjuntos se capturan en las estructuras `SimulationUpdateParameters` y `SimulationUpdateResult`, respectivamente:

```cs
public struct SimulationUpdateParameters
{
    public int FrameId;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};

public struct SimulationUpdateResult
{
    public int FrameId;
    public float NearPlaneDistance;
    public float FarPlaneDistance;
    public StereoMatrix4x4 ViewTransform;
    public StereoCameraFov FieldOfView;
};
```

Los miembros de la estructura tienen el significado siguiente:

| Miembro | Descripción |
|--------|-------------|
| FrameId | Identificador de fotograma continuo. Es necesario para la entrada SimulationUpdateParameters y se debe incrementar continuamente para cada nuevo fotograma. Si aún no hay datos de fotogramas disponibles, el valor será 0 en SimulationUpdateResult. |
| ViewTransform | Par estéreo izquierdo-derecho de las matrices de transformación de la vista de cámara del fotograma. En la representación monoscópica, únicamente el miembro `Left` es válido. |
| FieldOfView | Par estéreo izquierdo-derecho de los campos de visión de la cámara de fotogramas de acuerdo con la [convención de campo de visualización de OpenXR](https://www.khronos.org/registry/OpenXR/specs/1.0/html/xrspec.html#angles). En la representación monoscópica, únicamente el miembro `Left` es válido. |
| NearPlaneDistance | distancia de plano cercano utilizada para la matriz de proyección del fotograma remoto actual. |
| FarPlaneDistance | distancia de plano lejano utilizada para la matriz de proyección del fotograma remoto actual. |

Los pares estéreo `ViewTransform` y `FieldOfView` permiten establecer ambos valores de cámara ocular en el caso de que la representación estereoscópica esté habilitada. De lo contrario, se omitirán los miembros de `Right`. Como puede verse, mientras no se especifiquen matrices de proyección, solo la transformación de la cámara se pasa en forma de matrices de transformación 4x4 normales. Azure Remote Rendering calcula internamente las matrices reales utilizando los campos de visión especificados y el conjunto formado por el plano cercano y el plano lejano actuales en la [API de CameraSettings](../overview/features/camera.md).

Dado que el plano cercano y el plano lejano se pueden cambiar en tiempo de ejecución en [CameraSettings](../overview/features/camera.md), según sea necesario, y el servicio aplica esta configuración de forma asincrónica, cada estructura SimulationUpdateResult también incluirá el plano cercano y el plano lejano utilizados durante la representación del fotograma correspondiente. Utilice esos valores de plano para adaptar las matrices de proyección a fin de representar objetos locales para que coincidan con la representación de fotograma remoto.

Por último, aunque la llamada de **actualización de simulación** requiere la convención de campo de visualización de OpenXR, por motivos de normalización y seguridad algorítmica, se pueden usar las funciones de conversión que se muestran en los siguientes ejemplos de rellenado de estructuras:

```cs
public SimulationUpdateParameters CreateSimulationUpdateParameters(int frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters = default;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (parameters.FieldOfView.Left.FromProjectionMatrix(projectionMatrix) != Result.Success)
    {
        // Invalid projection matrix
        throw new ArgumentException("Invalid projection settings");
    }
    return parameters;
}

public void GetCameraSettingsFromSimulationUpdateResult(SimulationUpdateResult result, out Matrix4x4 projectionMatrix, out Matrix4x4 viewTransform, out int frameId)
{
    projectionMatrix = default;
    viewTransform = default;
    frameId = 0;

    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (result.FieldOfView.Left.ToProjectionMatrix(result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention.ZeroToOne, out projectionMatrix) != Result.Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

```cpp
SimulationUpdateParameters CreateSimulationUpdateParameters(uint32_t frameId, Matrix4x4 viewTransform, Matrix4x4 projectionMatrix)
{
    SimulationUpdateParameters parameters;
    parameters.FrameId = frameId;
    parameters.ViewTransform.Left = viewTransform;
    if (FovFromProjectionMatrix(projectionMatrix, parameters.FieldOfView.Left) != Result::Success)
    {
        // Invalid projection matrix
        return {};
    }
    return parameters;
}

void GetCameraSettingsFromSimulationUpdateResult(const SimulationUpdateResult& result, Matrix4x4& projectionMatrix, Matrix4x4& viewTransform, uint32_t& frameId)
{
    if (result.FrameId == 0)
    {
        // Invalid frame data
        return;
    }

    // Use the screenspace depth convention you expect for your projection matrix locally
    if (FovToProjectionMatrix(result.FieldOfView.Left, result.NearPlaneDistance, result.FarPlaneDistance, DepthConvention::ZeroToOne, projectionMatrix) != Result::Success)
    {
        // Invalid field-of-view
        return;
    }
    viewTransform = result.ViewTransform.Left;
    frameId = result.FrameId;
}
```

Estas funciones de conversión permiten alternar rápidamente entre la especificación de campo de visión y una matriz de proyección perspectiva 4x4 normal, según sean sus necesidades de representación local. Las funciones de conversión contienen lógica de comprobación y devolverán errores, sin establecer un resultado válido, en el caso de que las matrices de proyección de entrada o los campos de visión de entrada no sean válidos.

## <a name="api-documentation"></a>Documentación de la API

* [RemoteManagerStatic.StartupRemoteRendering() de C#](/dotnet/api/microsoft.azure.remoterendering.remotemanagerstatic.startupremoterendering)
* [Clase GraphicsBinding de C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbinding)
* [Clase GraphicsBindingWmrD3d11 de C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingwmrd3d11)
* [Clase GraphicsBindingSimD3d11 de C#](/dotnet/api/microsoft.azure.remoterendering.graphicsbindingsimd3d11)
* [Clase RemoteRenderingInitialization de C++](/cpp/api/remote-rendering/remoterenderinginitialization)
* [Clase GraphicsBinding de C++](/cpp/api/remote-rendering/graphicsbinding)
* [Clase GraphicsBindingWmrD3d11 de C++](/cpp/api/remote-rendering/graphicsbindingwmrd3d11)
* [Clase GraphicsBindingSimD3d11 de C++](/cpp/api/remote-rendering/graphicsbindingsimd3d11)

## <a name="next-steps"></a>Pasos siguientes

* [Cámara](../overview/features/camera.md)
* [Reproyección de fase tardía](../overview/features/late-stage-reprojection.md)
* [Tutorial: Visualización de modelos representados de forma remota](../tutorials/unity/view-remote-models/view-remote-models.md)