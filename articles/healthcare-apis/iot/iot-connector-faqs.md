---
title: 'Preguntas más frecuentes sobre el conector de IoT: API de Azure Healthcare'
description: En este documento se proporcionan respuestas a las preguntas más frecuentes sobre el conector de IoT.
services: healthcare-apis
author: msjasteppe
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 10/22/2021
ms.author: jasteppe
ms.openlocfilehash: d1a02502a13d2702b8965cebefe6935b4e457891
ms.sourcegitcommit: 692382974e1ac868a2672b67af2d33e593c91d60
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/22/2021
ms.locfileid: "130268441"
---
# <a name="frequently-asked-questions-about-iot-connector"></a>Preguntas más frecuentes sobre el conector de IoT

Estas son algunas de las preguntas más frecuentes sobre el conector de IoT.

## <a name="iot-connector-the-basics"></a>Conector de IoT: conceptos básicos

### <a name="what-are-the-differences-between-the-azure-api-for-fhir-iot-connector-preview-and-the-azure-healthcare-apis-iot-connector"></a>¿Cuáles son las diferencias entre el conector Azure API for FHIR IoT (versión preliminar) y el conector ioT de las API de Azure Healthcare?

Azure Healthcare APIs IoT Connector es el sucesor del conector de IoT de Azure API for Recursos Rápidos de Interoperabilidad en Salud (FHIR&#174;) (versión preliminar). 

Se han introducido varias mejoras, como los puntos de conexión de ingesta de mensajes de dispositivo hospedados por el cliente (por ejemplo, un centro de eventos de Azure), el uso de identidades administradas y Azure Role-Based Access Control (RBAC de Azure).

### <a name="can-i-use-iot-connector-with-a-different-fhir-service-other-than-the-azure-healthcare-apis-fhir-service"></a>¿Puedo usar el conector de IoT con otro servicio FHIR que no sea el servicio FHIR de Azure Healthcare APIs?

No. Actualmente, el conector de IoT de las API de Azure Healthcare solo admite el servicio FHIR de las API de Azure Healthcare para la persistencia de los datos. La versión de código abierto del conector de IoT admite el uso de diferentes servicios de FHIR. Para más información, consulte la [sección Proyectos de código](iot-git-projects.md) abierto.  

### <a name="what-versions-of-fhir-does-the-iot-connector-support"></a>¿Qué versiones de FHIR admite el conector de IoT?

Actualmente, el conector de IoT solo admite la persistencia de [HL7 FHIR&#174; R4.](https://www.hl7.org/implement/standards/product_brief.cfm?product_id=491) 

### <a name="what-are-the-subscription-quota-limits-for-iot-connector"></a>¿Cuáles son los límites de cuota de suscripción para el conector de IoT?

* 25 conectores de IoT por suscripción (ajustables con una solicitud de soporte técnico de Azure)
* 10 conectores de IoT por área de trabajo (ajustables con una solicitud de soporte técnico de Azure)
* Un destino de FHIR* por IoT Connector (no ajustable)

(* - FHIR Destination es un recurso secundario del conector de IoT)

### <a name="can-i-use-the-iot-connector-with-device-messages-from-apple174-google174-or-fitbit174-devices"></a>¿Puedo usar el conector de IoT con mensajes de dispositivo de Apple&#174;, Google&#174; o Fitbit&#174; dispositivos?

Sí. El conector de IoT admite mensajes de dispositivo de todas estas plataformas. Para más información, consulte la [sección Proyectos de código](iot-git-projects.md) abierto.  

## <a name="more-frequently-asked-questions"></a>Preguntas más frecuentes
[Preguntas más frecuentes sobre las API de Azure Healthcare](../healthcare-apis-faqs.md)

[Preguntas más frecuentes sobre el servicio de FHIR Azure Healthcare APIs](../fhir/fhir-faq.md)

[Preguntas más frecuentes sobre el servicio de DICOM Azure Healthcare APIs](../dicom/dicom-services-faqs.yml)

(FHIR&#174;) es una marca comercial registrada de [HL7](https://hl7.org/fhir/) y se usa con el permiso hl7.