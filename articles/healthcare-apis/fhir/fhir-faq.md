---
title: Preguntas más frecuentes sobre el servicio FHIR de Azure Healthcare APIs
description: Obtenga respuestas a las preguntas más frecuentes sobre el servicio FHIR, como la ubicación de almacenamiento de datos detrás de las API de FHIR y la compatibilidad con versiones.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.custom: references_regions
ms.openlocfilehash: 2b0a4a3aeb0045c0df31c61d6b10b5d9bc12e349
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780148"
---
# <a name="frequently-asked-questions-about-the-fhir-service"></a>Preguntas más frecuentes sobre el servicio FHIR

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En esta sección se tratan algunas de las preguntas más frecuentes sobre el servicio FHIR de Azure Healthcare APIs (en lo sucesivo, "servicio FHIR").

## <a name="fhir-service-the-basics"></a>Servicio FHIR: aspectos básicos

### <a name="what-is-fhir"></a>¿Qué es FHIR?

Recursos Rápidos de Interoperabilidad en Salud (FHIR, pronunciado como "fire" en inglés) es un estándar de interoperabilidad diseñado para permitir el intercambio de datos del sector sanitario entre diferentes sistemas sanitarios. Este estándar fue desarrollado por la organización HL7 y lo adoptan organizaciones del sector sanitario de todo el mundo. La versión más reciente de FHIR disponible es la R4 (versión 4). El servicio FHIR admite la versión R4 y también es compatible con la versión anterior STU3 (Standard for Trial Use 3). Para más información sobre FHIR, visite [HL7.org](http://hl7.org/fhir/summary.html).

### <a name="is-the-data-behind-the-fhir-apis-stored-in-azure"></a>¿Están los datos detrás de las API de FHIR almacenados en Azure?

Sí, los datos se almacenan en bases de datos administradas en Azure. El servicio FHIR de Azure Healthcare APIs no proporciona acceso directo al almacén de datos subyacente.

### <a name="what-identity-provider-do-you-support"></a>¿Qué proveedor de identidades admite?

Actualmente se admite Microsoft Azure Active Directory como proveedor de identidades.

### <a name="what-fhir-version-do-you-support"></a>¿Qué versión de FHIR admite?

Se admiten las versiones 4.0.0 y 3.0.1.

Para obtener detalles, consulte las [características admitidas](fhir-features-supported.md). Lea sobre lo que ha cambiado entre las versiones de FHIR (es decir, de STU3 a R4) en el [historial de versiones de HL7 STU3](https://hl7.org/fhir/R4/history.html).

### <a name="what-is-the-difference-between-the-azure-api-for-fhir-and-the-fhir-service-in-the-healthcare-apis"></a>¿Cuál es la diferencia entre Azure API for FHIR y el servicio FHIR de Healthcare APIs?

El servicio FHIR es nuestra implementación de la especificación FHIR ubicada en Azure Healthcare APIs, que le permite tener un servicio FHIR y un servicio DICOM en una sola área de trabajo. Azure API for FHIR era nuestro producto inicial de disponibilidad general y sigue estando disponible como producto independiente. En lo que a características se refiere, estas son las principales diferencias:

* El servicio FHIR tiene un límite de 4 TB y se encuentra en versión preliminar pública, mientras que Azure API for FHIR admite más de 4 TB y está disponible de forma general.
* El servicio FHIR admite [conjuntos de transacciones](https://www.hl7.org/fhir/http.html#transaction).
* Azure API for FHIR tiene más características de plataforma (como vínculo privado, claves administradas por el cliente y registros), que aún no están disponibles en el servicio FHIR de Azure Healthcare APIs. Se proporcionarán más detalles sobre estas características con la disponibilidad general.

### <a name="whats-the-difference-between-fhir-service-in-the-azure-healthcare-apis-and-the-open-source-fhir-server"></a>¿Cuál es la diferencia entre el servicio FHIR de Azure Healthcare APIs y FHIR Server de código abierto?

El servicio FHIR de Azure Healthcare APIs es una versión hospedada y administrada de Microsoft FHIR Server para Azure de código abierto. En el servicio administrado, Microsoft proporciona todo el mantenimiento y las actualizaciones.

Al ejecutar FHIR Server para Azure, tiene acceso directo a los servicios subyacentes, pero también es responsable de mantener y actualizar el servidor y todo el trabajo de cumplimiento requerido si almacena datos de PHI.

### <a name="in-which-regions-is-the-fhir-service-available"></a>¿En qué regiones está disponible el servicio FHIR?

Ampliamos continuamente las superficies globales de Healthcare APIs en función de las demandas de los clientes. El servicio FHIR está disponible actualmente en 25 regiones, incluidas dos gubernamentales de EE. UU.: Este de Australia, Sur de Brasil *, Centro de Canadá, Centro de la India*, Este de Asia *, Este de EE. UU. 2, Este de EE. UU., Norte de Alemania **, Centro-oeste de Alemania, Norte de Alemania**, Japón Oriental, Japón Occidental*, Centro de Corea del Sur*, Centro-norte de EE. UU., Norte de Europa, Norte de Sudáfrica, Centro-sur de EE. UU., Sudeste Asiático, Norte de Suiza, Sur de Reino Unido, Oeste de Reino Unido, Centro-oeste de EE. UU., Oeste de Europa, Oeste de EE. UU. 2, USGov Virginia y USGov Arizona.

\* Regiones con versión preliminar privada. ** Región solo con recuperación ante desastres.

Para obtener más información, consulte la [compatibilidad con regiones geográficas](https://azure.microsoft.com/global-infrastructure/services/?products=azure-api-for-fhir&regions=all).

### <a name="where-can-i-see-what-is-releasing-into-the-fhir-service"></a>¿Dónde puedo ver lo que se publica en el servicio FHIR?

Para obtener información sobre lo que se publica en Azure API for FHIR, consulte la [versión](https://github.com/microsoft/fhir-server/releases) de FHIR Server de código abierto. Hemos trabajado para etiquetar los elementos del servicio FHIR si se van a publicar en el servicio administrado y suelen estar disponibles dos semanas después de aparecer en la página de la versión en código abierto. También se han incluido instrucciones sobre cómo probar la compilación [aquí](https://github.com/microsoft/fhir-server/blob/master/docs/Testing-Releases.md) si se quiere probar en el entorno propio. Se está evaluando cómo compartir mejor las actualizaciones adicionales del servicio administrado.

### <a name="what-is-smart-on-fhir"></a>¿Qué es SMART on FHIR?

SMART (Substitutable Medical Applications and Reusable Technology) on FHIR es un conjunto de especificaciones de código abierto para integrar aplicaciones de asociados con servidores de FHIR y otros sistemas de TI sanitarios, como los registros sanitarios electrónicos y los intercambios de información sanitaria. Al crear una aplicación SMART on FHIR, puede garantizar que una gran cantidad de sistemas diferentes pueden acceder a la aplicación y aprovecharla.
Para obtener más información acerca de SMART, visite [SMART Health IT](https://smarthealthit.org/).

### <a name="where-can-i-find-what-version-of-fhir-is-running-on-my-database"></a>¿Dónde puedo encontrar qué versión de FHIR se está ejecutando en mi base de datos?

Puede encontrar la versión exacta de FHIR expuesta en la instrucción de funcionalidad, en la propiedad "fhirVersion" (dirección URL/metadatos FHIR).

## <a name="fhir-implementations-and-specifications"></a>Implementaciones y especificaciones de FHIR

### <a name="can-i-create-a-custom-fhir-resource"></a>¿Puedo crear un recurso personalizado de FHIR?

No se permiten recursos de FHIR personalizados. Si necesita un recurso FHIR personalizado, puede crear un recurso personalizado sobre el [recurso básico](http://www.hl7.org/fhir/basic.html) con extensiones. 

### <a name="are-extensions-supported-on-the-fhir-service"></a>¿Se admiten [extensiones](https://www.hl7.org/fhir/extensibility.html) en el servicio FHIR?

Está permitido cargar datos JSON de FHIR válidos en el servidor. Si desea almacenar la definición de la estructura que define la extensión, puede guardarla como un recurso de definición de estructura. Para buscar en extensiones, deberá [definir sus propios parámetros de búsqueda](how-to-do-custom-search.md). 

### <a name="what-is-the-limit-on-_count"></a>¿Cuál es el límite en recuento?

El límite actual de "_count" es 1000. Si establece "_count" en más de 1000, recibirá una advertencia en el conjunto de que solo se mostrarán 1000 registros.

### <a name="can-i-post-a-bundle-to-the-fhir-service"></a>¿Puedo publicar un conjunto en el servicio FHIR?

Actualmente se admite publicar [conjuntos por lotes](https://www.hl7.org/fhir/valueset-bundle-type.html) y [conjunto de transacciones](https://www.hl7.org/fhir/http.html#transaction) en el servicio FHIR.

### <a name="how-can-i-get-all-resources-for-a-single-patient-in-the-fhir-service"></a>¿Cómo puedo obtener todos los recursos de un solo paciente en el servicio FHIR?

Se admite la [operación $patient-everything](patient-everything.md), que le proporcionará todos los datos relacionados con un solo paciente. 

## <a name="using-the-fhir-service"></a>Uso del servicio FHIR

### <a name="where-can-i-see-some-examples-of-using-the-fhir-service-within-a-workflow"></a>¿Dónde puedo ver ejemplos de uso del servicio FHIR dentro de un flujo de trabajo?

Hay una colección de arquitecturas de referencia disponible en la página de [GitHub de arquitectura sanitaria](https://github.com/microsoft/health-architectures).

### <a name="where-can-i-see-an-example-of-connecting-a-web-application-to-fhir-service"></a>¿Dónde puedo ver un ejemplo de conexión de una aplicación web al servicio FHIR?

Tenemos una [página de GitHub de la arquitectura de estado](https://aka.ms/health-architectures) que contiene aplicaciones y escenarios de ejemplo. En ella se muestra cómo conectar una aplicación web al servicio FHIR.
