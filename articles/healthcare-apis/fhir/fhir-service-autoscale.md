---
title: Característica de escalado automático para el servicio FHIR de Las API de Azure Healthcare
description: En este artículo se describe la característica de escalado automático para el servicio FHIR de Azure Healthcare APIs.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 11/16/2021
ms.author: zxue
ms.openlocfilehash: 382a3ccee6f14c4d95e74bb40a8d899868754926
ms.sourcegitcommit: 81a1d2f927cf78e82557a85c7efdf17bf07aa642
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/19/2021
ms.locfileid: "132814865"
---
# <a name="fhir-service-autoscale"></a>Escalado automático del servicio FHIR

El servicio FHIR de Azure Healthcare APIs es un servicio administrado que permite a los clientes conservar los datos de atención sanitaria compatibles con FHIR e interactuar con ellos de forma segura a través del punto de conexión del servicio de API. El servicio FHIR proporciona la funcionalidad de escalado automático integrada para satisfacer varias cargas de trabajo.  

## <a name="what-is-fhir-service-autoscale"></a>¿Qué es el escalado automático del servicio FHIR?   

La característica de escalado automático para el servicio FHIR está diseñada para proporcionar escalabilidad del servicio optimizada automáticamente para satisfacer las demandas de los clientes cuando realizan transacciones de datos en cargas de trabajo coherentes o diversas en cualquier momento. Está disponible en todas las regiones en las que se admite el servicio FHIR. Tenga en cuenta que la característica de escalado automático está sujeta a los recursos disponibles en las regiones de Azure.   

## <a name="how-does-fhir-service-autoscale-work"></a>¿Cómo funciona el escalado automático del servicio FHIR?  

Cuando las cargas de trabajo de transacción son altas, la característica de escalado automático aumenta automáticamente los recursos informáticos. Cuando las cargas de trabajo de transacción son bajas, disminuye los recursos informáticos en consecuencia.  

La característica de escalado automático ajusta automáticamente los recursos informáticos para optimizar la escalabilidad general del servicio. Tanto si está realizando solicitudes de lectura que incluyen consultas sencillas como obtener información de pacientes mediante un identificador de paciente y consultas avanzadas como obtener todos los recursos para los pacientes cuyo nombre es "Sarah", o si va a crear o actualizar recursos de FHIR, la característica de escalado automático administra la dinámica y la complejidad de la asignación de recursos para garantizar una `DiagnosticReport` alta escalabilidad.

La característica de escalado automático forma parte del servicio administrado y no requiere ninguna acción de los clientes. Sin embargo, se recomienda a los clientes que compartan sus comentarios para ayudar a mejorar la característica. Los clientes también pueden generar una incidencia de soporte técnico para solucionar cualquier problema de escalabilidad que puedan haber experimentado.  

### <a name="what-is-the-cost-of-the-fhir-service-autoscale"></a>¿Cuál es el costo de la escala automática del servicio FHIR?  

La característica de escalado automático no supone ningún costo adicional para los clientes en función del nuevo modelo de facturación de API.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido sobre la característica de escalado automático del servicio FHIR en las API de Azure Healthcare. Para más información sobre las características admitidas del servicio FHIR, consulte

>[!div class="nextstepaction"]
>[Características de FHIR admitidas](fhir-features-supported.md)
