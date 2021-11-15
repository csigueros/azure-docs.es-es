---
title: Articulaciones para seguimiento de personas de Azure Kinect
description: Obtenga información sobre la estructura corporal, las articulaciones, las coordenadas de articulaciones y la jerarquía de articulaciones en Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, portabilidad, cuerpo, seguimiento, articulación, jerarquía, hueso, conexión
ms.openlocfilehash: 2616573d0474addb14abe5f6e932181b097bed46
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130223466"
---
# <a name="azure-kinect-body-tracking-joints"></a>Articulaciones para seguimiento de personas de Azure Kinect

El seguimiento de personas de Azure Kinect puede realizar un seguimiento de varios cuerpos humanos al mismo tiempo. Cada persona incluye un id. para la correlación temporal entre los fotogramas y el esqueleto cinemático. El número de personas detectadas en cada fotograma se puede obtener mediante el comando `k4abt_frame_get_num_bodies()`.

## <a name="joints"></a>Articulaciones

La posición y orientación de la articulación son estimaciones relativas al marco de referencia global del sensor de profundidad. La posición se especifica en milímetros. La orientación se expresa como un cuaternión normalizado.

## <a name="joint-coordinates"></a>Coordenadas de articulación

La posición y la orientación de cada articulación forman su propio sistema de coordenadas de articulación hacia la derecha. Todos los sistemas de coordenadas de articulación son sistemas de coordenadas absolutos en el sistema de coordenadas 3D de la cámara de profundidad.

> [!NOTE]
> La elección de la orientación del eje volteado para las articulaciones correspondientes en ambos lados del cuerpo está diseñada para simplificar el movimiento del espejo, por ejemplo, elevar ambos brazos más de 20 grados, lo que es común con avatares comerciales, motores de juegos y software de representación.

![Coordenadas de articulación](./media/concepts/joint-coordinates.png)

Leyenda: | eje x = rojo | eje y = verde | eje z = azul |

## <a name="joint-hierarchy"></a>Jerarquía de articulaciones

Un esqueleto incluye 32 articulaciones, con una jerarquía de articulaciones que avanza del centro del cuerpo hasta las extremidades. Cada conexión (hueso) vincula la articulación primaria con una articulación secundaria. En la ilustración se muestran las ubicaciones y las conexiones de las articulaciones con respecto al cuerpo humano.

![Jerarquía de articulaciones](./media/concepts/joint-hierarchy.png)

En la tabla siguiente se enumeran las conexiones de articulaciones estándar.

|Índice |Nombre de articulación     | Articulación primaria   |
|------|---------------|----------------|
| 0    |PELVIS         | -              |
| 1    |SPINE_NAVAL    | PELVIS         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |NECK           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PELVIS         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PELVIS         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | NECK           |
| 27   |NOSE           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>Pasos siguientes

[Mapa de índice de seguimiento de personas](body-index-map.md)
