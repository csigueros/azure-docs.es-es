---
title: Solución de problemas de detección de objetos
description: Solución de problemas con la detección de un objeto mediante Azure Object Anchors.
author: craigktreasure
manager: rgarcia
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: troubleshooting
ms.service: azure-object-anchors
ms.openlocfilehash: b70babbf3f105903cc7b14d175908b972bde9158
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128602002"
---
# <a name="troubleshooting-object-detection"></a>Solución de problemas de detección de objetos

En este artículo se da por supuesto que ya ha convertido un modelo 3D en un modelo de detección de Azure Object Anchors y que ha cargado correctamente el modelo en una aplicación.

## <a name="troubleshooting-steps"></a>Pasos para solucionar problemas

* Asegúrese de que el modelo que está detectando está dentro del tamaño admitido (de 1 a 10 metros) para obtener la mejor experiencia.
* Asegúrese de que la sala tenga suficientes texturas; para ello, puede agregar algunos pósteres.
* Quite los hologramas actuales para restablecer el mapa, tal como se describe [a continuación](#remove-holograms-to-reset-the-map).
* Examine el objeto de manera más exhaustiva.
* Proporcione un rectángulo delimitador estrecho como área de búsqueda, que incluya la totalidad o la mayor parte del objeto.
* Borre la memoria caché de asignación espacial y vuelva a escanear el objeto.
* Asegúrese de que se usaron la dirección de gravedad y la dimensión de recursos correctas durante la conversión del modelo, tal como se describe [a continuación](#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct).
* Inspeccione visualmente el modelo de detección, tal como se describe [a continuación](#visually-inspect-the-detection-models-mesh).
* Ajuste los valores de la consulta del modelo como se describe [a continuación](#adjust-object-query-values).
* Capture los diagnósticos como se describe [a continuación](#capture-diagnostics).

### <a name="remove-holograms-to-reset-the-map"></a>Quitar los hologramas para restablecer el mapa

Si ve que los objetos que se detectan tienen cualquiera de los siguientes problemas, quite y restablezca el mapa para corregir el problema:
* Orientación invertida
* Posición incorrecta
* Modelo inclinado

Para quitar hologramas y restablecer el mapa, abra la aplicación **Configuración** y vaya a **Sistema** -> **Hologramas**. A continuación, seleccione **Quitar todos los hologramas** para empezar a crear un mapa nuevo.

Borrar los hologramas garantiza que los objetos se puedan detectar correctamente en sus posiciones actuales si es que se movieron recientemente.

Vuelva a examinar los alrededores del entorno con el dispositivo HoloLens. Recorra los objetos que quiera detectar varias veces desde 1 a 2 metros.

### <a name="ensure-the-gravity-direction-and-asset-dimension-unit-are-correct"></a>Asegúrese de que la dirección de la gravedad y la unidad de dimensión del recurso sean correctas

Cuando envíe un modelo 3D para realizar la conversión mediante el SDK de conversión de Object Anchors (obtenga más información [aquí](../quickstarts/get-started-model-conversion.md)), deberá especificar la dirección de gravedad correcta (`Gravity`) y la unidad de medida (`AssetDimensionUnit`) para su modelo 3D. Si esos valores no son correctos, es poco probable que Object Anchors detecte el objeto correctamente.

La dirección de la gravedad es el vector descendente que apunta hacia la tierra. En el caso de los modelos CAD, la dirección de la gravedad suele ser la opuesta a una dirección ascendente. Por ejemplo, en muchos casos +Z representa arriba, en cuyo caso, -Z o `Vector3(0.0, 0.0, -1.0)` representarían la dirección de la gravedad. Al determinar la gravedad, debe considerar la orientación en la que se verá el modelo durante el tiempo de ejecución. Si está intentando detectar una silla en el mundo real en una superficie plana, la gravedad podría ser `Vector3(0.0, 0.0, -1.0)`. Sin embargo, si la silla está en una pendiente de 45 grados, la gravedad podría ser `Vector3(0.0, -Sqrt(2)/2, -Sqrt(2)/2)`.

La dirección de la gravedad se puede calcular con una herramienta de representación 3D, como [MeshLab](http://www.meshlab.net/).

Esta unidad de medida representa la escala del modelo. Las unidades admitidas se pueden encontrar mediante la enumeración **Microsoft.Azure.ObjectAnchors.Conversion.AssetLengthUnit**.

También puede seguir las instrucciones que se indican [aquí](../visualize-converted-model.md) para visualizar un modelo de detección en Unity y así poder inspeccionar visualmente la dirección de la gravedad y el aspecto correcto de la escala.

### <a name="visually-inspect-the-detection-models-mesh"></a>Inspección visual de la malla del modelo de detección

A veces, puede resultar útil inspeccionar visualmente la malla del modelo de detección para que pueda ver cualquier problema de orientación, escala o característica. Siga las instrucciones que se indican [aquí](../visualize-converted-model.md) para visualizar un modelo convertido en Unity.

### <a name="adjust-object-query-values"></a>Ajuste de los valores de consulta de objetos

* Ofrezca áreas de búsqueda estrechas para cubrir el objeto en la mayor medida posible a los efectos de mejorar la velocidad y la precisión de la detección.
* El valor predeterminado `ObjectQuery.MinSurfaceCoverage` suele ser suficiente, pero también puede usar un valor más pequeño para obtener una detección más rápida.
* Use un valor pequeño para `ObjectQuery.ExpectedMaxVerticalOrientationInDegrees` si se espera que el objeto esté de pie.
* Una aplicación siempre debe usar un modelo de objetos `1:1` para la detección. De manera ideal, la escala estimada debe estar cerca de 1, con un error del 1 %. Una aplicación podría establecer `ObjectQuery.MaxScaleChange` en `0` o `0.1` para deshabilitar o habilitar la estimación de escala, y evaluar cualitativamente la posición de la instancia.
* Para más información, consulte [Detección de un objeto difícil](../detect-difficult-object.md).

### <a name="capture-diagnostics"></a>Captura de diagnósticos

La aplicación puede capturar y guardar archivos de diagnóstico mediante el objeto [ObjectDiagnosticsSession](../concepts/sdk-overview.md#objectdiagnosticssession).

La [aplicación de ejemplo de Unity con MRTK](../quickstarts/get-started-unity-hololens-mrtk.md) escribe diagnósticos en la carpeta **TempState**. Puede iniciar una sesión de diagnóstico abriendo el <a href="/windows/mixed-reality/mrtk-unity/features/ux-building-blocks/hand-menu" target="_blank">menú manual</a>, seleccionando **Iniciar seguimiento**, reproduciendo un intento de detección y, a continuación, seleccionando **Detener seguimiento** para guardar el archivo de diagnóstico. A continuación, puede usar el [Portal de dispositivos de Windows](/windows/mixed-reality/develop/platform-capabilities-and-apis/using-the-windows-device-portal) para recuperar el archivo de diagnóstico de la carpeta **TempState** de la aplicación.

Asimismo, puede compartir el archivo de diagnóstico con nosotros para que podamos ayudarle a depurar el problema.

## <a name="next-steps"></a>Pasos siguientes

En esta guía de solución de problemas, ha aprendido a solucionar problemas de detección de un objeto físico mediante Azure Object Anchors.
Aquí tiene algunos artículos relacionados:

> [!div class="nextstepaction"]
> [Detección de un objeto difícil](../detect-difficult-object.md)

> [!div class="nextstepaction"]
> [Visualización de un modelo Object Anchors](../visualize-converted-model.md)
