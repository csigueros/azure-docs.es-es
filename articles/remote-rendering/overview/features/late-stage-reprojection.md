---
title: Reproyección de fase tardía
description: Información sobre la reproyección de fase tardía y cómo se usa.
author: sebastianpick
ms.author: sepick
ms.date: 02/04/2020
ms.topic: article
ms.openlocfilehash: 13a950f67053d9eaaea39e2df34df3dd12ed00c1
ms.sourcegitcommit: 4cd97e7c960f34cb3f248a0f384956174cdaf19f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132028725"
---
# <a name="late-stage-reprojection"></a>Reproyección de fase tardía

La *reproyección de fase tardía* (LSR) es una característica de hardware que ayuda a estabilizar los hologramas cuando el usuario se mueve.

Se espera que los modelos estáticos mantengan visualmente su posición al desplazarse por ellos. Si parece que son inestables, se puede sugerir este comportamiento en los problemas de LSR. Tenga en cuenta que las transformaciones dinámicas adicionales, como animaciones o vistas de explosión, pueden enmascarar este comportamiento.

Puede elegir entre dos modos de LSR diferentes, en concreto, **LSR planar** o **LSR de profundidad**. Ambos modos de LSR mejoran la estabilidad del holograma, aunque tienen limitaciones diferentes. Empiece por probar el LSR de profundidad, ya que es probable que se obtengan mejores resultados en la mayoría de los casos.

## <a name="how-to-set-the-lsr-mode"></a>Cómo establecer el modo LSR

El modo LSR que se usa dependerá de si la aplicación cliente envía un búfer de profundidad. Si se envía el búfer de profundidad, usa **LSR de profundidad** y **LSR planar** en caso contrario.

En los párrafos siguientes se explica cómo se realiza el envío del búfer de profundidad en Unity y aplicaciones nativas, respectivamente.

### <a name="unity"></a>Unity

En el Editor de Unity, vaya a *:::no-loc text="File > Build Settings":::* . Seleccione *:::no-loc text="Player Settings":::* en la parte inferior izquierda y, a continuación, compruebe en *:::no-loc text="Player > XR Settings > Virtual Reality SDKs > Windows Mixed Reality":::* si la opción **:::no-loc text="Enable Depth Buffer Sharing":::** está activada:

![Marca del uso compartido del búfer de profundidad habilitado](./media/unity-depth-buffer-sharing-enabled.png)

Si es así, la aplicación usará LSR de profundidad, de lo contrario, usará LSR planar.

Cuando se usa OpenXR, siempre se debe enviar el búfer de profundidad. La configuración se puede encontrar en *:::no-loc text="XR Plug-in Management > OpenXR":::* . El modo de reproyección se puede cambiar a través de una extensión en el complemento OpenXR:

```cs
using Microsoft.MixedReality.OpenXR;

public class OverrideReprojection : MonoBehaviour
{
    void OnEnable()
    {
        RenderPipelineManager.endCameraRendering += RenderPipelineManager_endCameraRendering;
    }
    void OnDisable()
    {
        RenderPipelineManager.endCameraRendering -= RenderPipelineManager_endCameraRendering;
    }

    // When using the Universal Render Pipeline, OnPostRender has to be called manually.
    private void RenderPipelineManager_endCameraRendering(ScriptableRenderContext context, Camera camera)
    {
        OnPostRender();
    }

    // Called directly when using Unity's legacy renderer.
    private void OnPostRender()
    {
        ReprojectionSettings reprojectionSettings = default;
        reprojectionSettings.ReprojectionMode = ReprojectionMode.PlanarManual; // Or your favorite reprojection mode.
        
        // In case of PlanarManual you also need to provide a focus point here.
        reprojectionSettings.ReprojectionPlaneOverridePosition = ...;
        reprojectionSettings.ReprojectionPlaneOverrideNormal = ...;
        reprojectionSettings.ReprojectionPlaneOverrideVelocity = ...;

        foreach (ViewConfiguration viewConfiguration in ViewConfiguration.EnabledViewConfigurations)
        {
            if (viewConfiguration.IsActive && viewConfiguration.SupportedReprojectionModes.Contains(reprojectionSettings.ReprojectionMode))
            {
                viewConfiguration.SetReprojectionSettings(reprojectionSettings);
            }
        }
    }
}
```

### <a name="native-c-applications"></a>Aplicaciones nativas de C++

El envío del búfer de profundidad está totalmente bajo el control del código de enlace nativo de C++, independientemente de la versión WMR o OpenXR. La única condición que debe cumplirse es que, en el momento en que se llama a `GraphicsBinding::BlitRemoteFrame`, se debe enlazar un búfer de profundidad  a la API de gráficos.

## <a name="depth-lsr"></a>LSR de profundidad

Para que el LSR de profundidad funcione, la aplicación cliente debe proporcionar un búfer de profundidad válido que contenga toda la geometría pertinente que se debe tener en cuenta durante LSR.

LSR de profundidad intenta estabilizar el fotograma de vídeo en función del contenido del búfer de profundidad proporcionado. Como consecuencia, el contenido que no se haya presentado en él, como los objetos transparentes, no se puede ajustar mediante LSR y pueden mostrarse artefactos de inestabilidad y de reproyección.

Para mitigar la inestabilidad de la reproyección de objetos transparentes, puede forzar la escritura del búfer de profundidad. Vea la marca de material *TransparencyWritesDepth* para los materiales de [Color](color-materials.md) y [PBR](pbr-materials.md). Sin embargo, tenga en cuenta que la calidad visual de la interacción de un objeto transparente u opaco puede verse afectada al habilitar esta marca.

## <a name="planar-lsr"></a>LSR planar

El LSR planar no tiene información de profundidad por píxel, como el LSR de profundidad. En su lugar, reproyecta todo el contenido en función de un plano que debe proporcionar cada fotograma.

El LSR planar reproyecta los objetos que se encuentran cerca del plano proporcionado. Cuanto más lejos esté un objeto, mayor será la inestabilidad. Aunque LSR de profundidad es mejor en la reproyección de objetos con profundidades diferentes, el LSR planar puede funcionar de forma más adecuada para alinear el contenido con un plano.

### <a name="configure-planar-lsr-in-unity"></a>Configuración de LSR planar en Unity

Los parámetros de plano se derivan del denominado *punto de enfoque*. Cuando se usa WMR, el punto de enfoque debe establecerse en cada fotograma a través de `UnityEngine.XR.WSA.HolographicSettings.SetFocusPointForFrame`. Para más información, consulte la [API del punto de enfoque de Unity](/windows/mixed-reality/focus-point-in-unity). En el caso de OpenXR, el punto de enfoque debe establecerse a través del `ReprojectionSettings` que se muestra en la sección anterior.
Si no establece un punto de enfoque, se elegirá una reserva. Sin embargo, la reserva automática suele dar lugar a resultados poco óptimos.

Puede calcular el punto de enfoque usted mismo, aunque es posible que tenga sentido basarlo en el calculado por el host de Remote Rendering. Llame a `RemoteManagerUnity.CurrentSession.GraphicsBinding.GetRemoteFocusPoint` para obtenerlo.

Normalmente, tanto el cliente como el host representan contenido del que el otro lado no es consciente, como los elementos de la interfaz de usuario en el cliente. Por lo tanto, puede ser conveniente combinar el punto de enfoque remoto con uno calculado localmente.

Los puntos de enfoque calculados en dos marcos sucesivos pueden variar mucho. Si solo se usan tal cual puede parecer que los hologramas saltan. Para evitar este comportamiento, se recomienda la interpolación entre los puntos de enfoque anterior y actual.

## <a name="reprojection-pose-modes"></a>Modos de posición de reproyección

El ámbito del problema general con la representación híbrida se puede indicar de esta manera: el contenido remoto y local se encuentra dentro de posiciones distintas (es decir, espacios de coordenadas) porque el servidor predice la posición remota, mientras que la posición local es la actual. Sin embargo, al final de un marco de representación, tanto el contenido remoto como el local deben alinearse y llevarse a la pantalla. En la ilustración siguiente se muestra un ejemplo en el que las posiciones locales y remotas se traducen en comparación con la ventanilla de presentación:

![Diagrama que muestra la posición remota y local en relación con la ventanilla de destino.](./media/reprojection-remote-local.png)

ARR proporciona dos modos de reproyección que funcionan ortogonalmente con el modo LSR descrito anteriormente. Estos modos se conocen como **:::no-loc text="Remote pose mode":::** y **:::no-loc text="Local pose mode":::** . A diferencia del modo LSR, los modos de posición definen cómo se combina el contenido remoto y local. La elección del modo negocia la calidad visual del contenido local para el rendimiento en tiempo de ejecución, por lo que las aplicaciones deben considerar detenidamente qué opción es adecuada. Consulte las consideraciones siguientes.

### :::no-loc text="Remote pose mode":::

:::no-loc text="Remote pose mode":::es el modo predeterminado de ARR. En este modo, el contenido local se representa sobre la secuencia de imagen remota entrante mediante la posición remota del marco remoto. A continuación, el resultado combinado se reenvía al sistema operativo para la reproyección final. Aunque este enfoque usa solo una reproyección, la corrección final se basa en el intervalo de ida y vuelta, por lo que el error de reproyección completo también se aplica al contenido local. Como consecuencia, la diferencia de corrección grande puede dar lugar a distorsión significativa de la geometría local, incluidos los elementos de la interfaz de usuario.

Con la ilustración anterior, se aplica la transformación siguiente en :::no-loc text="Remote pose mode"::::

![Pasos de reproyección en modo de posición remota.](./media/reprojection-pose-mode-remote.png)

### :::no-loc text="Local pose mode":::

En este modo, la reproyección se divide en dos pasos distintos: en el primer paso, el contenido remoto se reproyecta en el espacio de posición local, es decir, el espacio con el que se representa el contenido local en dispositivos VR/AR de forma predeterminada. Después, el contenido local se representa sobre esta imagen previamente transformada mediante la posición local habitual. En el segundo paso, el resultado combinado se reenvía al sistema operativo para la reproyección final. Puesto que esta segunda reproyección solo incurre en una diferencia pequeña (de hecho, la misma diferencia que se usaría si ARR no estaba presente), los artefactos de distorsión en el contenido local se mitigan significativamente.

En consecuencia, la ilustración tiene el siguiente aspecto:

![Pasos de reproyección en modo de posición local.](./media/reprojection-pose-mode-local.png)

### <a name="performance-and-quality-considerations"></a>Consideraciones de rendimiento y calidad

La elección del modo de posición tiene implicaciones en la calidad visual y el rendimiento. El costo adicional en tiempo de ejecución en el lado cliente por realizar la reproyección adicional en :::no-loc text="Local pose mode"::: en un dispositivo HoloLens 2 equivale a aproximadamente 1 milisegundo por marco de tiempo de GPU. Este costo adicional debe tenerse en cuenta si la aplicación cliente ya está cerca del presupuesto de fotogramas de 16 milisegundos. Por otro lado, hay tipos de aplicaciones sin contenido local o con contenido local que no es propenso a artefactos de distorsión. En esos casos :::no-loc text="Local pose mode"::: no obtiene ninguna ventaja visual porque la calidad de la reproyección de contenido remoto no se ven afectadas.

Por lo tanto, el consejo general sería probar los modos por caso de uso y ver si la mejora de la calidad visual justifica la sobrecarga de rendimiento adicional. También es posible alternar el modo dinámicamente, por ejemplo, habilitar el modo local solo cuando se muestran las URI importantes.

### <a name="how-to-change-the-no-loc-textpose-mode-at-runtime"></a>Cómo cambiar :::no-loc text="Pose mode"::: en tiempo de ejecución

La siguiente API de cliente se puede usar para cambiar el modo en tiempo de ejecución:

```cs
RenderingSession session = ...;
session.GraphicsBinding.SetPoseMode(PoseMode.Local); // set local pose mode
```
 
```cpp
ApiHandle<RenderingSession> session = ...;
session->GetGraphicsBinding()->SetPoseMode(PoseMode::Local); // set local pose mode
```

El modo se puede cambiar siempre que el objeto de enlace de gráficos esté disponible.

## <a name="next-steps"></a>Pasos siguientes

* [Consultas de rendimiento en el lado servidor](performance-queries.md)