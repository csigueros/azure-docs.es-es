---
title: Preguntas más frecuentes sobre Azure Healthcare APIs
description: En este documento se proporcionan respuestas a las preguntas más frecuentes sobre Azure Healthcare APIs.
services: healthcare-apis
author: ginalee-dotcom
ms.custom: references_regions
ms.service: healthcare-apis
ms.topic: reference
ms.date: 07/16/2021
ms.author: ginle
ms.openlocfilehash: 3cccd2a1e418dbc66a5a8c1bfa665c6c3276552d
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780719"
---
# <a name="frequently-asked-questions-about-azure-healthcare-apis-preview"></a>Preguntas más frecuentes sobre Azure Healthcare APIs (versión preliminar)

Estas son algunas de las preguntas más frecuentes sobre Azure Healthcare APIs.

## <a name="azure-healthcare-apis-the-basics"></a>Azure Healthcare APIs: aspectos básicos

### <a name="what-is-the-azure-healthcare-apis"></a>¿Qué es Azure Healthcare APIs?
Azure Healthcare APIs es una plataforma de datos de salud totalmente administrada que permite el intercambio rápido y la persistencia de la información sanitaria protegida (PHI) y de los datos sanitarios a través de estándares industriales abiertos e interoperables como Fast Healthcare Interoperability Resources (FHIR®) y Digital Imaging and Communications in Medicine (DICOM®).

### <a name="what-do-the-azure-healthcare-apis-enable-you-to-do"></a>¿Qué le permite hacer Azure Healthcare APIs?
Azure Healthcare APIs le permite hacer lo siguiente: 

* Conectar rápidamente orígenes y formatos de datos sanitarios dispares, como datos estructurados, de imágenes y de dispositivos, y normalizarlos para que persistan en la nube.

* Transformar e ingerir datos en FHIR. Por ejemplo, puede transformar los datos sanitarios de formatos heredados, como HL7v2 o CDA, o de datos de IoT de alta frecuencia en formatos propietarios de dispositivos a FHIR.

* Conectar los datos almacenados en Healthcare APIs con servicios de todo el ecosistema de Azure, como Synapse, y productos de Microsoft, como Teams, para obtener nuevos conocimientos a través de los análisis y el aprendizaje automático y para habilitar nuevos flujos de trabajo, así como la conexión con SMART en aplicaciones FHIR.

* Administrar cargas de trabajo avanzadas con características empresariales que ofrecen confiabilidad, escalabilidad y seguridad para garantizar que los datos están protegidos y satisfacen las certificaciones de privacidad y cumplimiento necesarias para el sector sanitario.

### <a name="can-i-migrate-my-existing-production-workload-from-azure-api-for-fhir-to-healthcare-apis"></a>¿Puedo migrar mi carga de trabajo de producción existente de Azure API for FHIR a Healthcare APIs?
No, desafortunadamente no ofrecemos funcionalidades de migración en este momento. 

### <a name="what-is-the-pricing-of-azure-healthcare-apis"></a>¿Cuáles son los precios de Azure Healthcare APIs?
Durante la fase de versión preliminar pública, Azure Healthcare APIs está disponible para su uso sin cargo alguno.

### <a name="what-regions-are-healthcare-apis-available"></a>¿En qué regiones está disponible Healthcare APIs?
Consulte la página [Productos por región](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir) para obtener la información más actualizada. 
          
### <a name="what-are-the-subscription-quota-limits-for-the-azure-healthcare-apis"></a>¿Cuáles son los límites de cuota de suscripción para Azure Healthcare APIs?

#### <a name="workspace-logical-container"></a>Área de trabajo (contenedor lógico):
* 200 instancias por suscripción (no ajustable)

#### <a name="dicom-server"></a>Servidor DICOM:
* 800 instancias por suscripción (no ajustable)
* 10 instancias DICOM por área de trabajo (no ajustable)

#### <a name="fhir-server"></a>Servidor FHIR:
* 25 instancias por suscripción (no ajustable)
* 10 instancias de FHIR por área de trabajo (no ajustable)

#### <a name="iot-connector"></a>Conector de IoT:
* 25 conectores de IoT por suscripción (no ajustable)
* 10 conectores de IoT por área de trabajo (no ajustable)
* 1 destino de FHIR* por conector de IoT (no ajustable)

## <a name="more-frequently-asked-questions"></a>Preguntas más frecuentes
[Preguntas más frecuentes sobre el servicio de FHIR Azure Healthcare APIs](./fhir/fhir-faq.md)

[Preguntas más frecuentes sobre el servicio de DICOM Azure Healthcare APIs](./dicom/dicom-services-faqs.yml)


