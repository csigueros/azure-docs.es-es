---
title: Directrices para crear experiencias de anclaje eficaces
description: Directrices y consideraciones para crear y ubicar anclajes de forma eficaz mediante Azure Spatial Anchors.
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: c92b45d9c471cf8a94a4001fb5303347d2839dd2
ms.sourcegitcommit: 695a33a2123429289ac316028265711a79542b1c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/01/2021
ms.locfileid: "113125931"
---
# <a name="create-an-effective-anchor-experience-by-using-azure-spatial-anchors"></a>Creación una experiencia de anclaje efectiva mediante Azure Spatial Anchors

En este artículo se proporcionan directrices y consideraciones que le ayudarán a crear y ubicar anclajes de forma efectiva mediante Azure Spatial Anchors.

## <a name="good-anchors"></a>Anclajes adecuados

Es importante invertir tiempo en la formación u orientación de los usuarios para la experiencia de usuario (UX) con el fin de crear anclajes adecuados. Si invierte en la creación de anclajes adecuados por adelantado, ayuda a los usuarios finales a buscar anclajes de forma confiable:

- Entre diferentes dispositivos.
- En distintos momentos.
- En condiciones de iluminación diferentes.
- Desde las perspectivas deseadas dentro del espacio.

## <a name="static-and-dynamic-locations"></a>Ubicaciones estáticas y dinámicas

Parte del diseño de la experiencia de anclaje es elegir las ubicaciones. ¿Las ubicaciones pueden ser estáticas y que un administrador del espacio las defina? ¿O serán dinámicas y definidas por el usuario?

Es posible que un encargado de tienda comercial desee una experiencia en tienda estática para convencer a los usuarios de que la visiten. Sin embargo, es probable que el desarrollador de un juego de mesa de realidad mixta desee permitir a los usuarios elegir dónde jugar.

Para obtener ubicaciones estáticas, puede invitar a los administradores a que dediquen tiempo a mantener el espacio con anclajes adecuados.

Para las ubicaciones dinámicas, debería pensar en cómo formar u orientar a los usuarios en la experiencia de usuario para crear los anclajes adecuados.

## <a name="stable-visual-features"></a>Características visuales estables

Los sistemas de seguimiento visual usados en dispositivos de realidad aumentada y combinada dependen de las características visuales del entorno. Para obtener la experiencia más confiable, realice las siguientes tareas:

- *Cree* anclajes en ubicaciones que tengan características visuales estables (es decir, que las características no cambien con frecuencia).

- *No cree* anclajes en grandes superficies vacías que no dispongan de características distintivas.

- *No cree* anclajes en materiales muy reflectantes.

- *No cree* anclajes en superficies en las que se repita el patrón, como en una alfombra o un papel tapiz.

![Ejemplos de un entorno adecuado y un entorno incorrecto para anclajes](./media/stable-visual.png)

## <a name="various-viewing-perspectives"></a>Distintas perspectivas de visualización

Cuando cree un anclaje, piense en las personas que intentarán localizarlo después.

Considere, por ejemplo, que hay un anclaje en medio de una habitación con dos puertas. Es posible que desee permitir a los usuarios entrar en el salón desde cualquier puerta. Al crear el anclaje, deberá examinar su posición desde ambas puertas. Modifique las perspectivas para capturar datos de entorno alrededor del anclaje para que los usuarios puedan encontrar el anclaje desde cualquier puerta.

En general, cuando cree un anclaje, examínelo desde las perspectivas de las personas que intentarán encontrarlo. Por lo tanto, si coloca contenido virtual en una escultura al aire libre, tiene sentido darle la vuelta a la escultura mientras la examina en el momento de la creación del anclaje. Si el anclaje está en la esquina de una sala, solo habrá una dirección desde la que acercarse. Al crear este anclaje, puede explorarlo solo desde esta perspectiva.

## <a name="multiple-anchors"></a>Varios anclajes

La iluminación puede marcar la diferencia en las características visuales que detecta una aplicación. Los anclajes creados con una luz fuerte y natural podrían ser difíciles de encontrar en la luz artificial y viceversa.

Si tiene este problema, puede resultar útil crear dos anclajes. Cree un anclaje para la luz natural y otro para la luz artificial en el mismo punto. Su aplicación podrá consultar ambos anclajes. Cuando se encuentre el anclaje, la aplicación tomará una decisión sobre él.

De forma similar, puede resultar útil usar varios anclajes en entornos donde las características visuales cambian porque la mayoría de los objetos se mueven. Cuando un anclaje es demasiado difícil de encontrar debido a cambios significativos en el entorno, puede reemplazarlo por uno nuevo. Puede hacer esto, por ejemplo, en una tienda donde el diseño se actualiza cada pocos meses.

## <a name="targets-and-rooms"></a>Destinos y salas

En muchos casos, un anclaje es un punto de entrada a la experiencia de la aplicación. Querrá realizar este paso de forma rápida y confiable para que los usuarios disfruten de su experiencia. Dedicar tiempo a la forma en la que los usuarios encontrarán los anclajes es un paso importante sobre el diseño. Resulta útil pensar en la búsqueda de anclajes en dos escenarios generales: *destinos* y *salas*.

### <a name="targets"></a>Destinos

En el escenario de destino, se conoce bien la ubicación del anclaje. Por ejemplo, en una aplicación de dibujo ficticia de realidad mixta, un usuario coloca un lienzo virtual en la pared. Esa persona solicita a los demás usuarios de la sala que sus dispositivos apunten al mismo lugar de la pared para buscar el anclaje y que comience la experiencia.

Otro ejemplo de un escenario de destino podría ser un inicio de sesión en una cafetería que diga: "Buscar ofertas". La cafetería ha colocado un anclaje aquí. Cuando los usuarios analizan la señal, buscan el anclaje y entran en la experiencia de realidad aumentada para buscar ofertas de café.

En los escenarios de destino pueden ayudar las fotos. Si se muestra a los usuarios una foto del destino deseado en su dispositivo, podrán identificar rápidamente qué analizar en el mundo real. Por ejemplo, podría ayudar a los usuarios a llegar a un área general de un objetivo determinado mediante el uso de GPS. Cuando el usuario llega, la aplicación muestra una foto del destino. El usuario examina todo el espacio, busca el destino y analiza el anclaje.

![Ilustración de un anclaje que muestra una foto del destino en un dispositivo móvil de un usuario](./media/start-here-edit.png)

### <a name="rooms"></a>Salas

En el escenario de sala, los usuarios especifican un espacio sabiendo únicamente que hay un anclaje en algún lugar. Los usuarios examinan el espacio con su dispositivo y encuentran rápidamente el anclaje.

Esta experiencia requiere normalmente crear anclajes bien protegidos, como se describe en Distintas perspectivas de visualización. Si examinó la sala desde muchas perspectivas cuando creó el anclaje, los usuarios podrán analizar prácticamente todo el lugar cuando intenten localizarlo.

![Ilustración de cómo un usuario puede examinar una sala para buscar un anclaje](./media/scan-room.png)

Básicamente, dedica más tiempo a analizar el espacio cuando crea el anclaje para que los usuarios posteriores puedan examinar y buscar el anclaje rápidamente. A medida que cree su experiencia, deberá tener en cuenta este equilibrio importante.

El ejemplo de la aplicación de dibujo de realidad mixta que analizamos anteriormente no funciona adecuadamente un escenario de sala. En este caso, el usuario que coloca el anclaje quiere que los demás se unan a la experiencia rápidamente. Los usuarios no quieren esperar para iniciar la experiencia hasta que se analice bien la sala. Puesto que todos los usuarios saben exactamente hacia dónde apuntar el dispositivo para ubicar los anclajes, este ejemplo funciona mejor como escenario de destino.

## <a name="anchor-location"></a>Ubicación del anclaje

Los sistemas de seguimiento visual dependen de las características visuales en un entorno. Conforme más características visuales pueda incluir un análisis, mayor será la probabilidad de encontrar el anclaje.

Siga las directrices generales de esta sección para crear una experiencia de usuario que fomente un análisis útil del entorno.

En primer lugar, si el usuario no encuentra el anclaje en pocos segundos, la aplicación debe animar a los usuarios a desplazar el dispositivo para capturar más perspectivas. La aplicación también puede animar a los usuarios a que se muevan por el entorno para buscar el anclaje desde varias perspectivas. Cuantas más perspectivas de características detecte el dispositivo, mejor, ya que aumentará la probabilidad de que se encuentre un anclaje.

Para escenarios de destino, solicite al usuario que se desplace por el destino para verlo desde distintas perspectivas. Es decir, pida al usuario que capture el destino desde nuevas perspectivas hasta que encuentre el anclaje.

Para escenarios de sala, solicite al usuario examinar lentamente la sala. Por ejemplo, solicite al usuario que se gire para capturar 180 grados o incluso de 360 grados de la sala. También puede pedir al usuario que vea la sala desde una nueva perspectiva.

El método más significativo es buscar en toda la habitación. Una búsqueda en toda la sala permite capturar más características visuales del entorno que una búsqueda de una pared cercana, por ejemplo. Una búsqueda en una pared cercana no permitirá capturar tantas características útiles de visuales del entorno.

No resulta útil mover varias veces el dispositivo de lado a lado cuando se busca un anclaje. Con ese procedimiento simplemente se capturan los mismos puntos desde la misma perspectiva.

## <a name="experience-tests"></a>Pruebas de experiencia

En este artículo, hemos hablado de directrices generales. Con Spatial Anchors, se escribirán aplicaciones que interactuarán con el mundo real. Por este motivo, debe dedicar tiempo a probar los escenarios de anclaje de su aplicación en entornos reales. Esto se debe realizar especialmente en entornos que representan donde se espera que los usuarios usen la aplicación.
