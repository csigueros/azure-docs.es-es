---
title: Conceptos del reconocimiento facial
titleSuffix: Azure Cognitive Services
description: En este artículo se explica el concepto de reconocimiento facial, sus operaciones relacionadas y las estructuras de datos subyacentes.
services: cognitive-services
author: PatrickFarley
manager: nitime
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pafarley
ms.openlocfilehash: 6f22e48c869ebc2cf4101127f3d87cc7836da35d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747548"
---
# <a name="face-recognition-concepts"></a>Conceptos del reconocimiento facial

En este artículo se explica el concepto de reconocimiento facial, sus operaciones relacionadas y las estructuras de datos subyacentes. En términos generales, el reconocimiento facial hace referencia al método de comprobar o identificar a un individuo a partir de su cara. La comprobación es una coincidencia uno a uno que toma dos caras y devuelve si son la misma cara; por su parte, la identificación es una coincidencia de uno a varios que toma una sola cara como entrada y devuelve un conjunto de candidatos que coinciden. El reconocimiento facial es importante para implementar el escenario de verificación de identidad que las empresas y las aplicaciones usan para comprobar que un usuario (remoto) es quien dice ser.

## <a name="related-data-structures"></a>Estructuras de datos relacionados

Las operaciones de reconocimiento usan principalmente las siguientes estructuras de datos. Estos objetos se almacenan en la nube y se puede hacer referencia a ellos por sus cadenas de identificador. Las cadenas de identificador siempre son únicas dentro de una suscripción. Se pueden duplicar los campos de nombre.

|Nombre|Descripción|
|:--|:--|
|DetectedFace| Esta representación facial única se recupera mediante la operación de [detección de caras](../Face-API-How-to-Topics/HowtoDetectFacesinImage.md). Su identificador expira 24 horas después de su creación.|
|PersistedFace| Cuando se agregan objetos DetectedFace a un grupo, como FaceList o Person, se convierten en objetos PersistedFace. Se pueden [recuperar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524c) en cualquier momento y no caducan.|
|[FaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039524b) o [LargeFaceList](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/5a157b68d2de3616c086f2cc)| Esta estructura de datos es una lista variada de objetos PersistedFace. Un objeto FaceList tiene un identificador único, una cadena de nombre y, opcionalmente, una cadena de datos de usuario.|
|[Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c)| Esta estructura de datos es una lista de objetos PersistedFace que pertenecen a la misma persona. Tiene un identificador único, una cadena de nombre y, opcionalmente, una cadena de datos de usuario.|
|[PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) o [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d)| Esta estructura de datos es una lista variada de objetos Person. Tiene un identificador único, una cadena de nombre y, opcionalmente, una cadena de datos de usuario. Se debe [entrenar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) un objeto PersonGroup antes de que se pueda utilizar en operaciones de reconocimiento.|

## <a name="recognition-operations"></a>Operaciones de reconocimiento

En esta sección se detalla cómo las operaciones subyacentes usan las estructuras de datos descritas anteriormente para identificar y comprobar una cara.

### <a name="persongroup-creation-and-training"></a>Creación y entrenamiento de PersonGroup

Debe crear un [PersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395244) o [LargePersonGroup](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/599acdee6ac60f11b48b5a9d) para almacenar el conjunto de personas con las que realizar la coincidencia. PersonGroups contiene objetos [Person](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523c), cada uno de los cuales representa a una persona individual y contiene un conjunto de datos faciales que pertenecen a esa persona.

La operación [Entrenar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395249) prepara el conjunto de datos que se usará en las comparaciones de datos faciales.

### <a name="identification"></a>Identificación

La operación [Identificar](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) toma uno o varios identificadores de la cara de origen (de un objeto DetectedFace o PersistedFace) y PersonGroup o LargePersonGroup. Devuelve una lista de los objetos Person a los que podría pertenecer cada cara de origen. Los objetos Person devueltos se encapsulan como objetos Candidate, que tienen un valor de confianza de predicción.


### <a name="verification"></a>Comprobación

La operación [Verify](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a) toma un identificador de cara único (de un objeto DetectedFace o PersistedFace) y un objeto Person. Determina si la cara pertenece a esa misma persona. La comprobación es una coincidencia uno a uno y se puede usar como comprobación final de los resultados de la llamada API de identificación. Sin embargo, opcionalmente puede pasar PersonGroup al que pertenece la persona candidata para mejorar el rendimiento de la API.


## <a name="input-data"></a>Datos de entrada

Utilice las siguientes sugerencias para asegurarse de que las imágenes de entrada proporcionan los resultados de reconocimiento más precisos:

* Los formatos de imagen de entrada admitidos son JPEG, PNG, GIF(el primer fotograma) y BMP.
* El tamaño del archivo de imagen no debe ser superior a 6 MB.
* Cuando crea objetos Person, use las fotos que presentan distintos tipos de ángulos e iluminaciones.
* Es posible que no se puedan reconocer algunas caras debido a desafíos técnicos como, por ejemplo:
  * Imágenes con iluminación extrema, por ejemplo, fuerte contraluz.
  * Obstáculos que bloquean uno o los dos ojos.
  * Diferencias en el tipo de pelo o vello facial.
  * Cambios en la apariencia facial debido a la edad.
  * Expresiones faciales extremas.

## <a name="next-steps"></a>Pasos siguientes

Ahora que está familiarizado con los conceptos de reconocimiento facial, escriba un script que identifique las caras con un objeto PersonGroup entrenado.

* [Inicio rápido de la biblioteca cliente de Face](../Quickstarts/client-libraries.md)