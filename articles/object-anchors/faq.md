---
title: Preguntas más frecuentes
description: Preguntas frecuentes sobre el servicio Azure Object Anchors.
author: craigktreasure
manager: vriveras
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: overview
ms.service: azure-object-anchors
ms.openlocfilehash: 18069157b4c7f38216c01649a33ed5f999dc7577
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128638394"
---
# <a name="frequently-asked-questions-about-azure-object-anchors"></a>Preguntas más frecuentes sobre Azure Object Anchors

Azure Object Anchors permite a una aplicación detectar un objeto en el mundo físico mediante un modelo 3D y estimar su posición 6-DoF (seis grados de libertad).

Para obtener más información, consulte [Introducción a Azure Object Anchors](overview.md).

## <a name="product-faq"></a>Preguntas más frecuentes del producto
**P: ¿Qué recomendaciones hay para los objetos que se deben usar?**

**R:** Se recomiendan las siguientes propiedades para los objetos:

* De 1 a 10 metros para cada dimensión.
* No simétrica, con suficientes variaciones en la geometría.
* Baja reflectividad (superficies mate) con color brillante.
* Objetos estacionarios.
* Cantidades de articulación pequeñas o nulas.
* Fondos claros con un desorden mínimo o nulo.
* El objeto analizado debe tener una coincidencia 1:1 con el modelo con que se entrenó.

**P: ¿Cuáles son las dimensiones de objeto máximas que se pueden procesar para la conversión del modelo?**

**R:** Cada dimensión de un modelo CAD debe tener menos de 10 metros. Para más información, consulte los [requisitos de recursos](overview.md).

**P: ¿Cuál es el tamaño máximo del modelo CAD que se puede procesar para la conversión?**

**R:** El tamaño de archivo del modelo debe ser inferior a 150 MB. Para más información, consulte los [requisitos de recursos](overview.md).

**P: ¿Qué formatos CAD se admiten?**

**R:** Actualmente se admiten los tipos de archivo `fbx`, `ply`, `obj`, `glb` y `gltf`. Para más información, consulte los [requisitos de recursos](overview.md).

**P: ¿Cuál es la dirección de la gravedad y la unidad que requiere el servicio de conversión de modelos?**

**R:** La dirección de la gravedad es el vector hacia abajo que apunta a la tierra y la unidad de medida representa la escala del modelo. Al convertir un modelo, es importante [asegurarse de que la dirección de la gravedad y la unidad de dimensión del recurso sean correctas](./troubleshoot/object-detection.md#ensure-the-gravity-direction-and-asset-dimension-unit-are-correct).

**P: ¿Cuánto tiempo se tarda en convertir un modelo CAD?**

**R:** Para un modelo `ply`, normalmente entre 3 y 15 minutos. Si envía modelos en otros formatos, espere de 15 a 60 minutos según el tamaño de archivo.

**P: ¿Cómo me recupero de un error de conversión de modelos?**

**R:** Para más información sobre los distintos códigos de error que pueden ser el resultado de un trabajo de conversión de modelos con errores y cómo controlar cada uno de ellos, consulte la [página de códigos de error de conversión](.\model-conversion-error-codes.md).

**P: ¿Qué dispositivos admite Object Anchors?**

**A:** HoloLens 2.

**P: ¿Qué compilación del sistema operativo debe ejecutar mi HoloLens?**

**R:** Versión de SO 18363.720 o posterior, publicada después del 12 de marzo de 2020.

  Más detalles en la [actualización Windows 10, del 12 de marzo de 2020](https://support.microsoft.com/help/4551762).

**P: ¿Cuánto tiempo se tarda en detectar un objeto en HoloLens?**

**R:** Depende del tamaño del objeto y del proceso de examen. Para obtener una detección más rápida, pruebe a seguir los procedimientos recomendados para un examen exhaustivo.
Para los objetos con una distancia de menos de 2 metros en cada dimensión, la detección puede producirse en pocos segundos. En el caso de objetos grandes, como un automóvil, el usuario debe recorrer un bucle completo alrededor del objeto para obtener una detección confiable, lo que significa que la detección podría tardar decenas de segundos.

**P: ¿Cuáles son los procedimientos recomendados al usar Object Anchors en una aplicación de HoloLens?**

**R:**

 1. Realice una calibración ocular para obtener una representación precisa.
 2. Asegúrese de que la sala tenga una textura visual enriquecida y una buena iluminación.
 3. Mantenga inmóvil el objeto, y alejado del desorden si es posible.
 4. De manera opcional, borre la memoria caché de [asignación espacial](/windows/mixed-reality/spatial-mapping) en el dispositivo HoloLens.
 5. Desplácese alrededor del objeto para examinarlo. Asegúrese de que se observe la mayor parte del objeto.
 6. Establezca un área de búsqueda suficientemente grande para cubrir el objeto.
 7. El objeto debe permanecer estacionario durante la detección.
 8. Inicie la detección del objeto y visualice la representación basada en la posición estimada.
 9. Para ahorrar batería, bloquee el objeto detectado o detenga el seguimiento una vez que la posición esté estable y sea precisa.

**P: ¿Qué versión de Mixed Reality Toolkit (MRTK) debe usar mi aplicación HoloLens de Unity para poder trabajar con el SDK de Unity para Object Anchors?**

**A:** El SDK de Unity para Azure Object Anchors no tiene ninguna clase de dependencia de Mixed Reality Toolkit, lo que significa que puede usar la versión que prefiera. Para más información, consulte [Introducción a MRTK para Unity](/windows/mixed-reality/develop/unity/mrtk-getting-started).

**P: ¿Cuál es la precisión de una posición calculada?**

**R:** Depende del tamaño del objeto, el material, el entorno, etc. En el caso de los objetos pequeños, la posición estimada puede tener un error de 2 cm. En el caso de objetos grandes, como un automóvil, el error puede ser de hasta 2 a 8 cm.

**P: ¿Sirve Object Anchors para objetos en movimiento?**

**R:** No se admiten objetos en **constante movimiento** ni **dinámicos**. Se admiten objetos en una posición completamente nueva en el espacio una vez que se han movido físicamente allí, pero no se puede realizar su seguimiento mientras se mueven.

**P: ¿Puede Object Anchors controlar deformaciones o articulaciones?**

**R:** Parcialmente, según cuánto cambie la forma o la geometría del objeto debido a una deformación o articulación. Si la geometría del objeto cambia mucho, el usuario puede crear otro modelo para esa configuración y usarlo para la detección.

**P: ¿Cuántos modelos diferentes puede detectar Object Anchors al mismo tiempo?**

**R:** Actualmente se admite la detección de tres modelos a la vez para garantizar la mejor experiencia del usuario, pero no se aplica ningún límite.

**P: ¿Puede Object Anchors detectar varias instancias del mismo modelo de objetos?**

**R:** Sí, se admite la detección de hasta tres instancias del mismo tipo de modelo para garantizar la mejor experiencia del usuario, pero no se aplica ningún límite. Puede detectar una instancia de objeto por área de búsqueda. Al llamar a `ObjectQuery.SearchAreas.Add`, puede agregar más áreas de búsqueda a una consulta para detectar más instancias. Puede llamar a `ObjectObserver.DetectAsync` con varias consultas para detectar varios modelos.

**P: ¿Qué debo hacer si el entorno de tiempo de ejecución de Object Anchors no puede detectar el objeto?**

**R:** Hay muchos factores que pueden impedir que un objeto se detecte correctamente: el entorno, la configuración de conversión de modelos, la configuración de consulta, etc. Obtenga más información sobre cómo [solucionar problemas de detección de objetos](./troubleshoot/object-detection.md).

**P: ¿Cómo elegir los parámetros de la consulta de objeto?**

**R:** Aquí se incluyen algunas [directrices generales](./troubleshoot/object-detection.md#adjust-object-query-values) y una guía más detallada para [objetos difíciles de detectar](./detect-difficult-object.md).

**P: ¿Cómo se obtienen los datos de diagnóstico de Object Anchors desde HoloLens?**

**R:** La aplicación puede especificar la ubicación de los archivos de diagnóstico. La aplicación de ejemplo de Object Anchors escribe los datos de diagnóstico en la carpeta **TempState**

**P: ¿Por qué el modelo de origen no se alinea con el objeto físico cuando se usa la posición que devuelve el SDK de Unity de Object Anchors?**

**R:** Unity puede cambiar el sistema de coordenadas al importar un modelo de objetos. Por ejemplo, el SDK de Unity de Object Anchors invierte el eje Z al convertir de un sistema de coordenadas de la mano derecha a la izquierda, pero Unity puede aplicar una rotación adicional sobre el eje X o Y. Un desarrollador puede establecer esta rotación adicional visualizando y comparando los sistemas de coordenadas.

**P: ¿Se admiten 2D?**

**R:** Puesto que se basa en geometría, solo se admiten 3D.

**P: ¿Se diferencian modelos iguales con colores diferentes?**

**R:** Dado que nuestros algoritmos se basan en geometría, los distintos colores del mismo modelo no se comportarán de manera diferente durante la detección.

**P: ¿Puedo usar Object Anchors sin conectividad a Internet?**

**R:**
* Para la conversión y el entrenamiento de modelos, se requiere conectividad, ya que esto sucede en la nube.
* Las sesiones en tiempo de ejecución se producen totalmente en el dispositivo y no requieren conectividad, ya que todos los cálculos tienen lugar en HoloLens 2.

## <a name="privacy-faq"></a>Preguntas más frecuentes sobre privacidad
**P: ¿Cómo almacena los datos Azure Object Anchors?**

**R:** Solo almacenamos metadatos del sistema, que se cifran en reposo con una clave de cifrado de datos administrados por Microsoft.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha obtenido algunas respuestas a preguntas comunes para conseguir los mejores resultados al usar Azure Object Anchors.
Aquí tiene algunos artículos relacionados:

> [!div class="nextstepaction"]
> [procedimientos recomendados](./best-practices.md)

> [!div class="nextstepaction"]
> [Solución de problemas de detección de objetos](./troubleshoot/object-detection.md)
