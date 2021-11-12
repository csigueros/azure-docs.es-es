---
title: ¿Qué es Azure Healthcare APIs?
description: Este artículo es una introducción a las API de Azure Healthcare.
services: healthcare-apis
author: stevewohl
ms.service: healthcare-apis
ms.topic: overview
ms.date: 07/09/2021
ms.author: ginle
ms.openlocfilehash: f243f43e50c4739cda36ee8d2374772df436d9f7
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132370391"
---
# <a name="what-is-azure-healthcare-apis-preview"></a>¿Qué son las API de Azure Healthcare (versión preliminar)?

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

Las API de Azure Healthcare son un conjunto de servicios de API administrados basados en estándares y marcos abiertos que permiten a los flujos de trabajo mejorar la atención sanitaria y ofrecer soluciones de atención sanitaria escalables y seguras. El uso de un conjunto de marcos y servicios de API administrados dedicados al sector sanitario es importante y beneficioso, ya que los datos sanitarios recopilados de pacientes y consumidores sanitarios se pueden fragmentar entre varios sistemas, tipos de dispositivos y formatos de datos. Obtener información a partir de los datos sanitarios es una de las mayores barreras para mantener la población y la salud personal, así como comprender la comprensión general de la salud. Hoy en día es más importante reunir sistemas dispares, flujos de trabajo y datos de mantenimiento. Un enfoque unificado y alineado para el acceso a los datos sanitarios, la normalización y la captura de tendencias permitiría la detección de conclusiones operativas y médicas. Podemos simplificar el proceso de conexión de nuevas aplicaciones de dispositivo y habilitar nuevos proyectos de investigación. El uso de las API de Atención sanitaria de Azure como una solución de atención sanitaria escalable y segura puede permitir flujos de trabajo para mejorar la atención sanitaria a través de la información detectada al reunir conjuntos de datos de información sanitaria protegida (PHI) y conectarlos de un extremo a otro con herramientas de aprendizaje automático, análisis e inteligencia artificial. 

Las API de Azure Healthcare proporcionan las siguientes ventajas:
* Capacitar a las nuevas cargas de trabajo para que aprovechen la INFORMACIÓN protegida mediante la habilitación de la recopilación y el acceso a los datos en un solo lugar, de forma coherente.
* Descubra una nueva información mediante la unión de UNA NUEVA INFORMACIÓN y su conexión de un extremo a otro con herramientas de aprendizaje automático, análisis e inteligencia artificial.
* Cree una nube de confianza con confianza en cómo se administra, almacena y hace que la información de mantenimiento protegida esté disponible.
La nueva Microsoft Azure Healthcare APIs, además de FHIR, admite otros estándares de datos del sector sanitario, como DICOM, que amplían la interoperabilidad de los datos sanitarios. El modelo de negocio y la plataforma de infraestructura se han rediseñado para adaptarse a la expansión e introducción de estándares de datos sanitarios diferentes y futuros. Los clientes pueden usar datos de mantenimiento de diferentes tipos en los estándares sanitarios bajo el mismo paraguas de cumplimiento. Las herramientas se han integrado en el servicio administrado que permite a los clientes transformar datos de formatos heredados o propietarios de dispositivos a FHIR. Algunas de estas herramientas se han desarrollado previamente y son de código abierto. Otros serán net new.

Azure Healthcare APIs le permite hacer lo siguiente: 
* Conectar rápidamente orígenes y formatos de datos sanitarios dispares, como datos estructurados, de imágenes y de dispositivos, y normalizarlos para que persistan en la nube.
* Transformar e ingerir datos en FHIR. Por ejemplo, puede transformar los datos sanitarios de formatos heredados, como HL7v2 o CDA, o de datos de IoT de alta frecuencia en formatos propietarios de dispositivos a FHIR.
* Conectar los datos almacenados en Healthcare APIs con servicios de todo el ecosistema de Azure, como Synapse, y productos de Microsoft, como Teams, para obtener nuevos conocimientos a través de los análisis y el aprendizaje automático y para habilitar nuevos flujos de trabajo, así como la conexión con SMART en aplicaciones FHIR.
* Administrar cargas de trabajo avanzadas con características empresariales que ofrecen confiabilidad, escalabilidad y seguridad para garantizar que los datos están protegidos y satisfacen las certificaciones de privacidad y cumplimiento necesarias para el sector sanitario.


## <a name="what-are-the-key-differences-between-azure-healthcare-apis-and-azure-api-for-fhir"></a>¿Cuáles son las principales diferencias entre las API de Azure Healthcare y Azure API for FHIR?

**Servicios vinculados**

Las API de Azure Healthcare ahora admiten varios estándares de datos de mantenimiento para el intercambio de datos estructurados. Una única colección de API de Azure Healthcare le permite implementar varias instancias de diferentes tipos de servicio (FHIR Service, DICOM Service y IoT Connector) que funcionan sin problemas entre sí.

**Introducción al servicio DICOM**

Las API de Azure Healthcare ahora incluyen compatibilidad con los servicios DICOM. DICOM permite el intercambio seguro de datos de imagen y sus metadatos asociados. DICOM es el estándar internacional para transmitir, almacenar, recuperar, imprimir, procesar y mostrar información de imágenes médicas, y es el estándar de imágenes médicas principal aceptado en toda la atención sanitaria. Para obtener más información sobre el servicio DICOM, vea [Información general de DICOM.](./dicom/dicom-services-overview.md)

**Cambios incrementales en el servicio FHIR**

Para el intercambio seguro de datos de FHIR, las API de atención sanitaria ofrecen algunas funcionalidades incrementales que no están disponibles en el Azure API for FHIR. 
* Compatibilidad con transacciones: en las API de atención sanitaria, el servicio FHIR admite agrupaciones de transacciones. Para obtener más información sobre los paquetes de transacciones, visite HL7.org y consulte interacciones de lotes y transacciones.
* Mejoras en la búsqueda encadenada: las búsquedas encadenadas & reserva de búsqueda encadenada ya no están limitadas por 100 elementos por subconscueración.


## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con las API de Azure Healthcare, siga el inicio rápido de 5 minutos para implementar un área de trabajo.

> [!div class="nextstepaction"]
> [Implementación del área de trabajo en el Azure Portal](healthcare-apis-quickstart.md)

> [!div class="nextstepaction"]
> [¿Qué es un área de trabajo de Azure Machine Learning?](workspace-overview.md)
