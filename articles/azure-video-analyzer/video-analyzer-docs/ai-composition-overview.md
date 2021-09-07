---
title: Composiciones de IA en Azure Video Analyzer
description: En este artículo encontrará información general sobre la compatibilidad de Azure Video Analyzer con tres tipos de composiciones de IA. El tema también ofrece una explicación del escenario para cada tipo de composición de IA.
ms.topic: conceptual
ms.date: 08/11/2021
ms.openlocfilehash: 709bfe642c6427e131c95d64af6de0ee410cf261
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780362"
---
# <a name="ai-composition"></a>Composición de IA

En este artículo encontrará información general sobre la compatibilidad de Azure Video Analyzer con tres tipos de composiciones de IA. 

* [Secuencial](#sequential-ai-composition)
* [Parallel](#parallel-ai-composition)
* [Combinado](#combined-ai-composition)

## <a name="sequential-ai-composition"></a>Composición secuencial de IA

Los nodos de IA se pueden componer en secuencia. Esto permite que un nodo descendente aumente las inferencias generadas por un nodo ascendente.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/sequential.svg" alt-text="Composición secuencial de IA":::
 
### <a name="key-aspects"></a>Aspectos clave

* Las extensiones de canalización actúan como nodos de paso multimedia y se pueden configurar de forma que los servidores de IA externos reciban fotogramas con diferentes velocidades, formatos y resoluciones. Además, se puede especificar la configuración para que los servidores de IA externos puedan recibir todos los fotogramas o solo los fotogramas que ya contengan inferencias.
* Las inferencias se agregan a los fotogramas a medida que pasan por los distintos nodos de extensión; se puede agregar un número ilimitado de estos nodos en secuencia.
* Otros escenarios, como la grabación continua de vídeo o la grabación de vídeo basada en eventos, se pueden combinar con la composición secuencial de IA.

    
## <a name="parallel-ai-composition"></a>Composición de IA en paralelo

Los nodos de IA también se pueden componer en paralelo en lugar de hacerlo en secuencia. Esto permite realizar inferencias independientes en la secuencia de vídeo ingerida, lo que ahorra ancho de banda de ingesta en el perímetro.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/parallel.svg" alt-text="Composición de IA en paralelo":::
 
### <a name="key-aspects"></a>Aspectos clave

* El vídeo se puede dividir en un número arbitrario de ramas paralelas, y dicha división puede producirse en cualquier momento después de los nodos siguientes.
    
    * Origen RTSP
    * Detector de movimientos
    * Extensión de canalización

## <a name="combined-ai-composition"></a>Composición combinada de IA

Las construcciones de composiciones secuenciales y paralelas se pueden combinar para desarrollar canalizaciones complejas de IA que admiten composición. Esto es posible, ya que las canalizaciones de AVA permiten que los nodos de extensión se combinen secuencialmente o en paralelo de manera indefinida, junto con otros nodos admitidos.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/ai-composition/complex.svg" alt-text="Composición combinada de IA":::
 


## <a name="next-steps"></a>Pasos siguientes

[Análisis de secuencias de vídeo en directo con varios modelos de IA mediante la composición de IA](analyze-ai-composition.md)
