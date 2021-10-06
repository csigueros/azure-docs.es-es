---
title: ¿Qué es el servicio Azure Face?
titleSuffix: Azure Cognitive Services
description: El servicio Azure Face ofrece algoritmos de IA que permiten detectar, reconocer y analizar caras humanas en las imágenes.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: overview
ms.date: 09/27/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: reconocimiento facial, software de reconocimiento facial, análisis facial, coincidencia facial, aplicación de reconocimiento facial, búsqueda de caras por imagen, búsqueda de reconocimiento facial
ms.openlocfilehash: 3cb63bb5fb98eddee84677bb8e2d8d11ed86c876
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129362761"
---
# <a name="what-is-the-azure-face-service"></a>¿Qué es el servicio Azure Face?

> [!WARNING]
> El 11 de junio de 2020 Microsoft anunció que no venderá tecnología de reconocimiento facial a los departamentos de policía de Estados Unidos hasta que se promulgue un reglamento estricto cimentado en los derechos humanos. Por lo tanto, es posible que los clientes no usen las características o la funcionalidad del reconocimiento facial incluidas en los servicios de Azure, como Face o Video Indexer, si un es un departamento de policía de Estados Unidos o permite el uso de dichos servicios por parte de cualquiera de ellos. Al crear un nuevo recurso de Face, debe reconocer y aceptar en Azure Portal que no va a utilizar el servicio en o para un departamento de seguridad en Estados Unidos y que ha revisado la [documentación de IA responsable (RAI)](../cognitive-services-apis-create-account-cli.md#prerequisites) y usará este servicio de acuerdo con él.

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

El servicio Azure Face ofrece algoritmos de IA que detectan, reconocen y analizan caras humanas en las imágenes. El software de reconocimiento facial es importante en muchos escenarios diferentes, como la verificación de identidad, el control de acceso sin contacto y el desenfoque facial para la privacidad.

Verificación de identidad: comprueba la identidad de alguien con una tarjeta de identificación emitida por el Gobierno, como un pasaporte, carnet de conducir u otra imagen de registro para conceder acceso a servicios físicos o digitales o recuperar una cuenta. Entre los escenarios de acceso específicos se incluyen la apertura de una nueva cuenta, la comprobación de un trabajo o la administración de una valoración en línea. La verificación de identidad se puede realizar una única vez cuando se incorpora alguien y, repetidas veces, cuando alguien accede a un servicio físico o digital.

Control de acceso sin contacto: en comparación con los métodos actuales, como tarjetas o vales, la identificación de caras opcional permite una experiencia de control de acceso mejorada a la vez que se reducen los riesgos de seguridad y protección por uso compartido, pérdida o robo de tarjetas. El reconocimiento facial ayuda al proceso de registro de una persona en el conjunto de registros en aeropuertos, estadios, parques temático o edificios, así como en los mostradores de recepción en oficinas, hospitales, tiendas, gimnasios, clubes o escuelas.

Difuminado de caras: difuminado o desenfoque de las caras detectadas de personas grabadas en un vídeo para proteger su privacidad.

Esta documentación contiene los siguientes tipos de artículos:
* Los [inicios rápidos](./Quickstarts/client-libraries.md) son instrucciones paso a paso que permiten realizar llamadas al servicio y obtener los resultados en un breve período de tiempo. 
* Las [guías paso a paso](./Face-API-How-to-Topics/HowtoDetectFacesinImage.md) contienen instrucciones para usar el servicio de maneras más específicas o personalizadas.
* Los [artículos conceptuales](./concepts/face-detection.md) proporcionan explicaciones detalladas de la funcionalidad y las características del servicio.
* Los [tutoriales](./enrollment-overview.md) son guías más largas que muestran cómo usar este servicio como componente en soluciones empresariales más amplias.

## <a name="face-detection-and-analysis"></a>Detección y análisis de caras

La detección de caras es necesaria como primer paso en todos los demás escenarios. API Detect detecta caras humanas en una imagen y devuelve las coordenadas del rectángulo en sus ubicaciones. También devuelve un identificador único que representa los datos de caras almacenados, que se utilizan en operaciones posteriores para identificar o comprobar caras.

Si lo desea, la detección de caras también puede extraer un conjunto de atributos faciales, como la posición de la cabeza, la edad, las emociones, el vello facial y las gafas. Estos atributos son predicciones generales, no clasificaciones reales. Algunos atributos son útiles para asegurarse de que la aplicación obtiene datos faciales de alta calidad cuando los usuarios se agregan a un servicio Face (por ejemplo, la aplicación podría recomendar a los usuarios que se quiten las gafas de sol si las llevan puestas).

> [!NOTE]
> La característica de detección de caras también está disponible a través del [servicio Computer Vision](../computer-vision/overview.md). Sin embargo, si desea utilizar más operaciones de caras como identificar, comprobar, buscar similar o agrupar caras, debe usar este servicio en su lugar.

Para más información sobre la detección de caras y análisis, consulte el artículo sobre los conceptos de la [Detección de caras](concepts/face-detection.md). Vea también la documentación de referencia de [Detect API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236).


## <a name="identity-verification"></a>Verificación de identidad

Las empresas y aplicaciones modernas pueden utilizar las operaciones de identificación de caras y comprobación de caras para comprobar que un usuario es quien dice ser. 

### <a name="identification"></a>Identificación

La identificación facial puede abordar la coincidencia "de uno a varios" de una cara de una imagen con un conjunto de caras en un repositorio seguro. Los candidatos de coincidencia se devuelven en función de la coincidencia de sus datos faciales con la cara de la consulta. Este escenario se utiliza para conceder acceso de creación a un determinado grupo de personas o para comprobar el usuario de un dispositivo.

La siguiente imagen muestra un ejemplo de una base de datos llamada `"myfriends"`. Cada grupo puede contener hasta un millón de objetos de persona diferentes. Cada objeto de persona puede tener hasta 248 caras registradas.

![Una cuadrícula con tres columnas para diferentes personas, cada una con tres filas de imágenes de caras.](./Images/person.group.clare.jpg)

Una vez creado y entrenado un grupo, puede realizar la identificación en el grupo con una cara nueva detectada. Si la cara se identifica como una persona en el grupo, se devuelve el objeto de persona.

### <a name="verification"></a>Comprobación

La operación de comprobación responde a la pregunta "¿Estas dos caras pertenecen a la misma persona?". 

La verificación también es una coincidencia "uno a uno" de una cara de una imagen con otra cara de un repositorio seguro o una foto.

La comprobación se puede usar en escenarios de comprobación de identidad o control de acceso para comprobar que una imagen coincide con una imagen capturada previamente (por ejemplo, una foto de una tarjeta de identificación emitida por el gobierno).

Para más información sobre la verificación de identidad, consulte la guía de conceptos de [Reconocimiento facial](concepts/face-recognition.md) o la documentación de referencia de [Identify API ](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395239) y [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a).


## <a name="find-similar-faces"></a>Búsqueda de caras similares

La operación de búsqueda de similares realiza una coincidencia facial entre una cara objetivo y un conjunto de caras candidatas, y busca un conjunto más reducido de caras parecidas a la cara objetivo. Esto resulta útil para realizar una búsqueda de caras por imagen. 

El servicio admite dos modos de funcionamiento, **matchPerson** y **matchFace**. El modo **matchPerson** devuelve las caras parecidas tras filtrar por la misma persona mediante [Verify API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f3039523a). El modo **matchFace** ignora el filtro de la misma persona. Devuelve una lista de caras candidatas similares que pueden o no pertenecer a la misma persona.

En el siguiente ejemplo se muestra la cara objetivo:

![Una mujer sonriendo](./Images/FaceFindSimilar.QueryFace.jpg)

Y estas imágenes son las caras candidatas:

![Cinco imágenes de personas sonriendo. Las imágenes a y b muestran a la misma persona.](./Images/FaceFindSimilar.Candidates.jpg)

Al buscar cuatro caras parecidas, el modo **matchPerson** devuelve a y b, que muestran a la misma persona que la cara objetivo. El modo **matchFace** devuelve a, b, c y d: exactamente cuatro caras candidatas, aunque algunas no sean la misma persona que el objetivo o el nivel de similitud sea bajo. Para más información, vea la guía de conceptos [Reconocimiento facial](concepts/face-recognition.md) o la documentación de referencia de [Find Similar API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395237).

## <a name="group-faces"></a>Agrupación de caras

La operación de agrupación divide un conjunto de caras desconocidas en varios grupos más pequeños en función de la similitud. Cada grupo es un subconjunto apropiado separado del conjunto original de caras. También devuelve una única matriz "messyGroup" que contiene los identificadores de caras para los que no se han encontrado similitudes.

Todas las caras de un grupo devuelto pertenecerán probablemente a la misma persona, pero puede haber varios grupos diferentes para una única persona. Estos grupos se distinguen por otro factor, por ejemplo, la expresión. Para más información, vea la guía de conceptos [Reconocimiento facial](concepts/face-recognition.md) o la documentación de referencia de [Group API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395238).

## <a name="sample-app"></a>Aplicación de ejemplo

Las aplicaciones de ejemplo siguientes muestran algunas formas de usar el servicio Face:

- [Aplicación FamilyNotes UWP](https://github.com/Microsoft/Windows-appsample-familynotes) es una aplicación de la Plataforma universal de Windows (UWP) que usa la identificación facial junto con la voz, Cortana, la escritura manuscrita y la cámara en un escenario de uso compartido de notas en familia.

## <a name="data-privacy-and-security"></a>Seguridad y privacidad de datos

Al igual que sucede con todos los recursos de Cognitive Services, los desarrolladores que usan el servicio Face deben estar al tanto de las directivas de Microsoft sobre los datos de los clientes. Para más información, vea la [página de Cognitive Services](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) en Microsoft Trust Center.

## <a name="next-steps"></a>Pasos siguientes

Siga una guía de inicio rápido para codificar los componentes básicos de una aplicación de reconocimiento facial en el idioma de su elección.

- [Inicio rápido de la biblioteca cliente](quickstarts/client-libraries.md)