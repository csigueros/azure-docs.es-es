---
title: 'Introducción al servicio DICOM: Azure Healthcare APIs'
description: En este artículo, obtendrá información sobre los conceptos de DICOM, la creación de imágenes médicas y el servicio DICOM.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 07/10/2021
ms.author: aersoy
ms.openlocfilehash: 7cff5c0095bdb9d230673f347cdf5b3e6d099d17
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121779858"
---
# <a name="overview-of-the-dicom-service"></a>Introducción al servicio DICOM

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este artículo, se describen los conceptos de DICOM, la creación de imágenes médicas y el servicio DICOM.

## <a name="medical-imaging"></a>Imágenes médicas

La creación de imágenes médicas es la técnica y el proceso de creación de representaciones visuales del interior de un cuerpo para el análisis clínico y la intervención médica, así como la representación visual de la función de algunos órganos o tejidos (fisiología). La creación de imágenes médicas busca revelar estructuras internas ocultas por la piel y los huesos, así como diagnosticar y tratar la enfermedad. La creación de imágenes médicas también establece una base de datos de anatomía y fisiología normales para que sea posible identificar anomalías. Aunque la creación de imágenes de los órganos y los tejidos retirados se puede realizar por motivos médicos, estos procedimientos suelen formar parte de la patología en lugar de la creación de imágenes médicas. [Wikipedia, Creación de imágenes médicas](https://en.wikipedia.org/wiki/Medical_imaging)

## <a name="dicom"></a>DICOM

DICOM (Imágenes digitales y comunicaciones en Medicina) es el estándar internacional para transmitir, almacenar, recuperar, imprimir, procesar y mostrar información de imágenes médicas, y es el principal estándar de imágenes médicas aceptado en todo el sector sanitario. Aunque hay algunas excepciones (dentistas, veterinarios), casi todas las especialidades médicas, fabricantes de equipos, proveedores de software y profesionales individuales se basan en DICOM en alguna fase de cualquier flujo de trabajo médico que implique imágenes. DICOM garantiza que las imágenes médicas cumplan los estándares de calidad, de modo que se pueda mantener la precisión del diagnóstico. La mayoría de las modalidades de creación de imágenes, incluidas la tomografía procesada (CT), la creación de imágenes de resonancia magnética (MRI) y los ultrasonidos, deben ajustarse a los estándares DICOM. Es necesario el uso de aplicaciones DICOM especializadas para acceder y usar imágenes en formato DICOM.

## <a name="dicom-service"></a>Servicio DICOM

Un servicio DICOM es un servicio administrado que necesita una suscripción de Azure y una cuenta de Azure Active Directory para su implementación en el área de trabajo de Azure Healthcare APIs. Permite la comunicación basada en estándares con cualquier sistema habilitado para DICOMweb&trade;. El servicio DICOM inserta metadatos DICOM en un servicio FHIR, o un servidor FHIR, lo que permite una única fuente de verdad para los datos clínicos y los metadatos de creación de imágenes. 

La necesidad de integrar eficazmente datos no clínicos se ha agudizado. Para tratar eficazmente a los pacientes, investigar nuevos tratamientos, soluciones de diagnóstico o proporcionar una visión general eficaz del historial sanitario de un único paciente, las organizaciones deben integrar datos de varios orígenes. Una de las integraciones más urgentes se produce entre los datos clínicos y los datos de imágenes.

FHIR&trade; se está convirtiendo en un estándar importante para datos clínicos y proporciona extensibilidad para admitir la integración de otro tipo de datos directamente o mediante referencias. Mediante el uso del servicio DICOM, las organizaciones pueden almacenar referencias a los datos de creación de imágenes en FHIR&trade; y habilitar consultas que cruzan conjuntos de datos clínicos y de creación de imágenes. Esto puede permitir muchos escenarios diferentes, por ejemplo:

- **Creación de cohortes para la investigación.** A menudo, mediante consultas sobre pacientes que coinciden con los datos de los sistemas clínicos y de creación de imágenes, como este (que desencadenó el esfuerzo de integrar los datos de FHIR&trade; y DICOM): "Dame todos los tratamientos prescritos con todos los documentos de exploración CT y sus informes radiológicos asociados para cualquier paciente mayor de 45 años que haya tenido un diagnóstico de osteosarcoma en los últimos dos años".
- **Búsqueda de resultados sobre pacientes similares para conocer las opciones y planear tratamientos.** Cuando se le presenta un diagnóstico de pacientes, un médico puede identificar los resultados de los pacientes y los planes de tratamiento para pacientes anteriores con un diagnóstico similar, incluso cuando estos incluyan datos de imágenes.
- **Proporcionar una vista longitudinal de un paciente durante el diagnóstico.** Los radiólogos, especialmente los telerradiólogos, a menudo no tienen acceso completo a la historia médica de un paciente ni a los estudios de imágenes relacionados. Mediante la integración de FHIR&trade;, estos datos se pueden proporcionar fácilmente, incluso a radiólogos fuera de la red local de la organización.
- **Cierre del bucle de comentarios con los telerradiólogos.** Lo ideal es que un radiólogo tenga acceso a los datos clínicos de un hospital para cerrar el bucle de comentarios después de realizar una recomendación. Sin embargo, para los telerradiólogos, este no suele ser el caso. En su lugar, a menudo no pueden cerrar el bucle de comentarios después de realizar un diagnóstico, ya que no tienen acceso a los datos de los pacientes después de la lectura inicial. Sin acceso (o con acceso limitado) a los resultados clínicos, no pueden obtener los comentarios necesarios para mejorar sus aptitudes. Como dijo un telerradiólogo: "Digamos, por ejemplo, sobre la glándula paratiroidea. Hacemos más exámenes que cualquier otra clínica del país y, sin embargo, tengo que suplicar para que los cirujanos me digan lo que han encontrado realmente. De los más de 500 estudios que hago cada mes, solo tengo comentarios directos sobre tres o cuatro".  A través de la integración con FHIR&trade;, una organización puede crear fácilmente una herramienta que proporcionará comentarios directos a los telerradiólogos, lo que les ayudará a mejorar sus aptitudes y a realizar mejores recomendaciones en el futuro.
- **Cierre del bucle de comentarios para los modelos de ML e inteligencia artificial.** Los modelos de aprendizaje automático funcionan mejor cuando se pueden usar comentarios del mundo real para mejorar sus modelos. Sin embargo, los proveedores de ML de terceros rara vez obtienen los comentarios que necesitan para mejorar sus modelos a lo largo del tiempo. Por ejemplo, un ISV lo ha dicho de esta manera: "Usamos una combinación de modelos de ML y expertos humanos para recomendar un plan de tratamiento para la cirugía del corazón. Sin embargo, rara vez se obtienen comentarios de los médicos sobre la precisión de nuestro plan. Por ejemplo, a menudo se recomienda un tamaño de stent. Nos gustaría recibir comentarios sobre si nuestra predicción era correcta, pero la única vez que escuchamos a los clientes es cuando hay un problema importante con nuestras recomendaciones". Al igual que con los comentarios de los telerradiólogos, la integración con FHIR&trade; permite a las organizaciones crear un mecanismo para proporcionar comentarios a la canalización de reentrenamiento del modelo.

## <a name="deploy-dicom-service-to-azure"></a>Implementación del servicio DICOM en Azure

El servicio DICOM necesita una suscripción de Azure para configurar y ejecutar los componentes necesarios. De manera predeterminada, estos componentes se crean dentro de un grupo de recursos de Azure existente o nuevo para simplificar la administración. Además, se requiere una cuenta de Azure Active Directory. Para cada instancia del servicio DICOM, creamos una combinación de recursos aislados y multiinquilino.

## <a name="summary"></a>Resumen

En este artículo conceptual, se proporciona información general sobre DICOM, la creación de imágenes médicas y el servicio DICOM.
 
## <a name="next-steps"></a>Pasos siguientes

Para empezar a usar el servicio DICOM, consulte:

>[!div class="nextstepaction"]
>[Implementación del servicio DICOM mediante Azure Portal](deploy-dicom-services-in-azure.md)

>[!div class="nextstepaction"]
>[Uso de las API estándar de DICOMweb &trade; con servicios DICOM](dicomweb-standard-apis-with-dicom-services.md)
