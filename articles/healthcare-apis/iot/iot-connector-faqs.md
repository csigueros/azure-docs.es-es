---
title: 'Preguntas más frecuentes sobre el conector de IoT: API de Azure Healthcare'
description: En este documento se proporcionan respuestas a las preguntas más frecuentes sobre el conector de IoT.
services: healthcare-apis
author: msjasteppe
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 11/05/2021
ms.author: jasteppe
ms.openlocfilehash: 153a4c86d0f6f1b2617327360569cd92d1a318ca
ms.sourcegitcommit: 5af89a2a7b38b266cc3adc389d3a9606420215a9
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "131990251"
---
# <a name="frequently-asked-questions-about-iot-connector"></a>Preguntas más frecuentes sobre el conector de IoT

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

Estas son algunas de las preguntas más frecuentes sobre el conector de IoT.

## <a name="iot-connector-the-basics"></a>Conector de IoT: conceptos básicos

### <a name="what-are-the-differences-between-the-azure-api-for-fhir-iot-connector-preview-and-the-azure-healthcare-apis-iot-connector"></a>¿Cuáles son las diferencias entre el conector Azure API for FHIR IoT (versión preliminar) y el conector ioT de las API de Azure Healthcare?

Azure Healthcare APIs IoT Connector es el sucesor del conector de IoT de Azure API for Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;) (versión preliminar). 

Se han introducido varias mejoras, como los puntos de conexión de ingesta de mensajes de dispositivo hospedados por el cliente (por ejemplo, un centro de eventos de Azure), el uso de identidades administradas y Azure Role-Based Access Control (Azure RBAC).

### <a name="can-i-use-iot-connector-with-a-different-fhir-service-other-than-the-azure-healthcare-apis-fhir-service"></a>¿Puedo usar el conector de IoT con otro servicio FHIR que no sea el servicio FHIR de Azure Healthcare APIs?

No. Actualmente, el conector de IoT de las API de Azure Healthcare solo admite el servicio FHIR de las API de Azure Healthcare para la persistencia de los datos. La versión de código abierto del conector de IoT admite el uso de diferentes servicios de FHIR. Para más información, consulte la [sección Proyectos de código](iot-git-projects.md) abierto.  

### <a name="what-versions-of-fhir-does-the-iot-connector-support"></a>¿Qué versiones de FHIR admite el conector de IoT?

Actualmente, el conector de IoT solo admite la persistencia de [HL7 FHIR&#174; R4.](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491) 

### <a name="what-are-the-subscription-quota-limits-for-iot-connector"></a>¿Cuáles son los límites de cuota de suscripción para el conector de IoT?

* 25 conectores de IoT por suscripción (no ajustables)
* 10 conectores de IoT por área de trabajo (no ajustables)
* Un destino de FHIR* por conector de IoT (no ajustable)

(* - FHIR Destination es un recurso secundario del conector de IoT)

### <a name="can-i-use-the-iot-connector-with-device-messages-from-apple174-google174-or-fitbit174-devices"></a>¿Puedo usar el conector de IoT con mensajes de dispositivo de Apple&#174;, Google&#174; o Fitbit&#174; dispositivos?

Sí. El conector de IoT admite mensajes de dispositivo de todas estas plataformas. Para más información, consulte la [sección Proyectos de código](iot-git-projects.md) abierto.  

## <a name="more-frequently-asked-questions"></a>Preguntas más frecuentes
[Preguntas más frecuentes sobre las API de Azure Healthcare](../healthcare-apis-faqs.md)

[Preguntas más frecuentes sobre el servicio de FHIR Azure Healthcare APIs](../fhir/fhir-faq.md)

[Preguntas más frecuentes sobre el servicio de DICOM Azure Healthcare APIs](../dicom/dicom-services-faqs.yml)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso de HL7.
