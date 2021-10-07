---
title: Prácticas recomendadas
description: Procedimientos recomendados para obtener resultados mejorados
author: ariye
ms.author: crtreasu
ms.date: 09/10/2021
ms.topic: best-practice
ms.service: azure-object-anchors
ms.openlocfilehash: 119837282409719a194341467a5d8164a46431b9
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124777590"
---
# <a name="best-practices"></a>Procedimientos recomendados

Se recomienda probar algunos de estos pasos para obtener los mejores resultados.

## <a name="conversion"></a>Conversión

- Compruebe las dimensiones de los objetos físicos. Azure Object Anchors funciona mejor con los objetos cuya dimensión más pequeña se encuentra en el intervalo recomendado de 1 a 10 metros.
- Inspeccione el modelo 3D en un programa de software como [**MeshLab**](https://www.meshlab.net/) para obtener los detalles siguientes.
  - Asegúrese de que el modelo 3D tenga una malla de triángulo y que los triángulos de la superficie exterior miren hacia afuera. Es decir, los vértices deben estar orientados para que las normales sigan la regla de la mano derecha en su orientación hacia afuera.
  - Asegúrese de que el modelo 3D se especifica con las unidades de escala correctas con respecto a los objetos físicos. Las unidades deben ser: ***centímetros, decímetros, pies, pulgadas, kilómetros, metros, milímetros, yardas***.
  - Confirme la dirección de la gravedad nominal que corresponde a la orientación vertical del objeto en el mundo real. Si la gravedad o vertical descendente del objeto es -Y, use * **(0, -1, 0)** _ o _*_ (0, 0, -1)_** para -Z y, de igual modo, para cualquier otra dirección.
  - Asegúrese de que el modelo 3D esté codificado en uno de los formatos admitidos: `.glb`, `.gltf`, `.ply`, `.fbx`, `.obj`.
- Nuestro servicio de conversión de modelos podría tardar mucho tiempo en procesar un modelo de gran tamaño y con un gran nivel de detalle. Para aumentar la eficacia, puede preprocesar el modelo 3D para quitar las caras interiores.

## <a name="detection"></a>Detección

> [!VIDEO https://channel9.msdn.com/Shows/Docs-Mixed-Reality/Azure-Object-Anchors-Detection-and-Alignment-Best-Practices/player]

- El SDK del entorno de ejecución proporcionado requiere una región de búsqueda proporcionada por el usuario para buscar y detectar los objetos físicos. La región de búsqueda podría ser un rectángulo delimitador, una esfera, el tronco de una vista o cualquier combinación de ellos. Para evitar una detección falsa, establezca una región de búsqueda lo suficientemente grande como para abarcar el objeto. Si usa las aplicaciones de ejemplo proporcionadas, inicie la aplicación a aproximadamente 2 metros de la superficie más cercana.
- Antes de iniciar la aplicación Azure Object Anchors en un dispositivo con HoloLens 2, quite todos los hologramas mediante la aplicación **Configuración**.
  Vaya a **Sistema** -> **Hologramas** y, después, seleccione **Quitar todos los hologramas** para empezar con un mapa nuevo.

  La eliminación de los hologramas garantiza de que los objetos se puedan detectar correctamente en sus posiciones actuales si es que se movieron recientemente.
- Después de quitar los hologramas y antes de iniciar la aplicación, rodee el objeto a una distancia de entre 1 y 2 metros para examinarlo con HoloLens.

  El examen previo de un objeto y su entorno puede ayudar a limpiar las superficies residuales creadas a partir de objetos y exámenes anteriores.
  De lo contrario, es posible que la aplicación vea superficies fantasma que provoquen una alineación inexacta del modelo 3D y los hologramas asociados. El examen previo del objeto también puede reducir en gran medida la latencia de detección de Azure Object Anchors, por ejemplo, de 30 a 5 segundos.
- Si los objetos son oscuros y muy reflectantes, puede que tenga que examinar el objeto a una distancia menor desde varios ángulos y varias distancias.
- El SDK del entorno de ejecución proporcionado ofrece algunos parámetros para que los usuarios puedan ajustar la detección, tal y como se muestra en las aplicaciones de ejemplo. Los parámetros predeterminados funcionan bien con la mayoría de los objetos. Si cree que necesita ajustarlos para objetos específicos, he aquí algunas recomendaciones:
  - Use un menor umbral de cobertura de la superficie si el objeto físico es grande, oscuro o brillante.
  - Permita un pequeño cambio de escala (por ejemplo, 0,1) para un objeto grande, como un automóvil.
  - Permita alguna desviación en grados entre la dirección vertical local del objeto y la gravedad cuando el objeto esté en una pendiente.

## <a name="next-steps"></a>Pasos siguientes

En esta guía, ha aprendido algunos procedimientos recomendados para obtener los mejores resultados posibles cuando use Azure Object Anchors para detectar un objeto. Aquí tiene algunos artículos relacionados:

> [!div class="nextstepaction"]
> [Solución de problemas de detección de objetos](./troubleshoot/object-detection.md)
