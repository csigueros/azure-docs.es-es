---
title: 'Escalabilidad y rendimiento: Personalizer'
titleSuffix: Azure Cognitive Services
description: 'Las aplicaciones y los sitios web de alto rendimiento y alto tráfico tienen dos factores principales que se deben considerar con Personalizer para mejorar la escalabilidad y el rendimiento: la latencia y el rendimiento del entrenamiento.'
author: jeffmend
ms.author: jeffme
ms.manager: nitinme
ms.service: cognitive-services
ms.subservice: personalizer
ms.topic: conceptual
ms.date: 10/24/2019
ms.openlocfilehash: 711d1f69485fb7f4c9256ac4a2edbe351ae4619c
ms.sourcegitcommit: 16e25fb3a5fa8fc054e16f30dc925a7276f2a4cb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2021
ms.locfileid: "122831409"
---
# <a name="scalability-and-performance"></a>Escalabilidad y rendimiento

Las aplicaciones y los sitios web de alto rendimiento y alto tráfico tienen dos factores principales que se deben considerar con Personalizer para mejorar la escalabilidad y el rendimiento:

* Mantener una baja latencia al realizar llamadas a Rank API.
* Garantizar que el rendimiento del entrenamiento esté al día con la entrada de eventos.

La personalización puede devolver una clasificación rápidamente, con la mayor parte de la duración de las llamadas dedicada a la comunicación a través de API REST. Azure realizará la escalabilidad automática de la capacidad para responder rápidamente a las solicitudes.

##  <a name="low-latency-scenarios"></a>Escenarios de baja latencia

Algunas aplicaciones requieren latencias bajas al devolver una clasificación. Se necesitan latencias bajas:

* Evitar que el usuario espere mucho tiempo antes de mostrar el contenido clasificado.
* Ayudar a que un servidor con tráfico extremo no tenga que limitar el escaso tiempo de proceso ni las conexiones de red.


## <a name="scalability-and-training-throughput"></a>Escalabilidad y rendimiento del entrenamiento

Personalizer funciona al actualizar un modelo que se vuelve a entrenar según los mensajes que Personalizer envía de manera asincrónica después de Rank API y Reward API. Estos mensajes se envían con una instancia de Azure Event Hub para la aplicación.

 Es poco probable que la mayoría de las aplicaciones alcance el máximo rendimiento de combinación y entrenamiento de Personalizer. Si bien alcanzar este valor máximo no ralentizará la aplicación, implicaría que las colas de Event Hub se rellenan más rápido que si se hubieran limpiado.

## <a name="how-to-estimate-your-throughput-requirements"></a>Cálculo de los requisitos de rendimiento

* Para calcular el número promedio de bytes por evento de clasificación, sume las longitudes de los documentos JSON de contexto y acción.
* Divida 20 MB/s por estos bytes promedio estimados.

Por ejemplo, si la carga promedio tiene 500 características y cada una de ellas tiene 20 caracteres estimados, cada evento tiene aproximadamente 10 KB. Con estos cálculos, 20 000 000 / 10 000 = 2000 eventos/s, que es alrededor de 173 millones de eventos por día. 

Si está por alcanzar estos límites, póngase en contacto con el equipo de soporte técnico para recibir consejos sobre la arquitectura.

## <a name="next-steps"></a>Pasos siguientes

[Creación y configuración de Personalizer](how-to-settings.md).