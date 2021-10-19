---
title: ¿Qué es el servicio FHIR?
description: El servicio FHIR permite el intercambio rápido de datos a través de las API de FHIR. Ingiera, administre y conserve información médica protegida (PHI) con un servicio en la nube administrado.
services: healthcare-apis
author: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: overview
ms.date: 08/03/2021
ms.author: chrupa
ms.openlocfilehash: 5ddbef4874a5efd9f63a099f8da52204065b6b4a
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122779073"
---
# <a name="what-is-fhirreg-service"></a>¿Qué es el servicio &reg; FHIR?

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

El servicio FHIR de las API de Azure Healthcare (que aquí se denomina servicio FHIR) permite el intercambio rápido de datos a través de las API de Recursos Rápidos de Interoperabilidad en Salud (FHIR®), con el respaldo de una oferta administrada de plataforma como servicio (PaaS) en la nube. Se facilita a todos los usuarios que trabajan con información médica la ingesta, administración y conservación de la información médica protegida [PHI](https://www.hhs.gov/answers/hipaa/what-is-phi/index.html) en la nube: 

- El servicio FHIR administrado se aprovisiona en la nube en cuestión de minutos 
- Enterprise punto de conexión basado en FHIR en Azure para el acceso a datos y el almacenamiento en formato FHIR
- Alto rendimiento, baja latencia
- Administración segura de información médica protegida (PHI) en un entorno en la nube compatible
- SMART on FHIR para implementaciones móviles y web
- Control de sus propios datos a escala con el control de acceso basado en roles
- Seguimiento del registro de auditoría para el acceso, creación, modificación y lecturas en cada almacén de datos

El servicio FHIR permite crear e implementar un servidor de FHIR en cuestión de minutos para aprovechar la escala elástica de la nube. Los servicios de Azure que potencian el servicio FHIR están diseñados para un rendimiento rápido, independientemente del tamaño de los conjuntos de datos que se administran.

La API de FHIR y el almacén de datos compatible le permiten conectarse de forma segura e interactuar con cualquier sistema que use las API de FHIR.  Microsoft se encarga de las operaciones, el mantenimiento, las actualizaciones y los requisitos de cumplimiento de la oferta de PaaS, para que pueda liberar sus propios recursos operativos y de desarrollo. 

## <a name="leveraging-the-power-of-your-data-with-fhir"></a>Mejora de la eficacia de sus datos con FHIR

El sector sanitario está transformando rápidamente la información médica para adaptarla al estándar emergente de [FHIR&reg;](https://hl7.org/fhir) (Recursos Rápidos de Interoperabilidad en Salud). FHIR posibilita un modelo de datos sólido y extensible con semántica estandarizada e intercambio de datos que permite que todos los sistemas que utilizan FHIR puedan funcionar juntos.  La transformación de los datos en FHIR le permite conectarse rápidamente a los orígenes de datos existentes como, por ejemplo, los sistemas electrónicos de registros sanitarios o las bases de datos de investigación. FHIR también permite el intercambio rápido de datos en implementaciones modernas de desarrollo web y móvil. Lo más importante es que FHIR puede simplificar la ingesta de datos y acelerar el desarrollo con herramientas de análisis y aprendizaje automático.  

### <a name="securely-manage-health-data-in-the-cloud"></a>Administración segura de información médica en la nube

El servicio FHIR permite el intercambio de datos a través de API coherentes, RESTful y FHIR basadas en la especificación HL7 FHIR. Con el respaldo de una oferta de PaaS administrada en Azure, también proporciona un entorno escalable y seguro para la administración y el almacenamiento de información médica protegida (PHI) en el formato FHIR nativo.  

### <a name="free-up-your-resources-to-innovate"></a>Liberación de recursos para la innovación

Puede invertir recursos en la creación y ejecución de su propio servidor de FHIR, pero con el servicio FHIR en las API de Azure Healthcare, Microsoft asume la carga de trabajo de las operaciones, el mantenimiento, las actualizaciones y los requisitos de cumplimiento, lo que le permite liberar sus propios recursos operativos y de desarrollo.

### <a name="enable-interoperability-with-fhir"></a>Habilitar la interoperabilidad con FHIR

El uso del servicio FHIR permite conectarse con cualquier sistema que aproveche las API de FHIR para lectura, escritura, búsqueda y otras funciones. Se puede usar como una herramienta eficaz para consolidar, normalizar y aplicar aprendizaje automático con datos clínicos de registros sanitarios electrónicos, paneles clínicos y de pacientes, programas de supervisión remota o con bases de datos fuera del sistema que tienen API de FHIR.

### <a name="control-data-access-at-scale"></a>Control del acceso a los datos a escala

El usuario tiene el control de los datos. El control de acceso basado en roles le permite administrar el modo en que se almacenan los datos y se accede a ellos. Al proporcionar mayor seguridad y reducir la carga de trabajo administrativa, puede determinar quién tiene acceso a los conjuntos de datos que crea, según las definiciones de roles que cree para su entorno.  

### <a name="secure-your-data"></a>Protección de los datos

Proteja la información médica protegida con una inteligencia de seguridad que no tiene rival. Los datos están aislados en una base de datos única por instancia de API y se protegen mediante la conmutación por error de varias regiones. El servicio FHIR implementa una defensa por capas, en profundidad y una protección contra amenazas avanzada para los datos.  

## <a name="applications-for-the-fhir-service"></a>Aplicaciones para el servicio FHIR

Los servidores de FHIR son herramientas clave para la interoperabilidad de los datos sobre información médica. El servicio FHIR está diseñado como una API y un servicio que puede crear, implementar y empezar a usar rápidamente. A medida que el estándar FHIR se expande en el sector sanitario, los casos de uso seguirán creciendo, pero a continuación se muestran algunas aplicaciones de cliente iniciales en las que el servicio FHIR es útil: 

- **Inicio/IoT y desarrollo de aplicaciones:**  Los clientes que desarrollan una aplicación centrada en pacientes o proveedores (móvil o web) pueden aprovechar el servicio FHIR como un servicio back-end totalmente administrado. El servicio FHIR proporciona un recurso valioso en el que los clientes pueden administrar datos e intercambiar datos en un entorno de nube seguro diseñado para los datos de mantenimiento, aprovechar las directrices de implementación de SMART on FHIR y permitir que todos los sistemas de proveedores usen su tecnología (por ejemplo, la mayoría de los EHR han habilitado las API de lectura de FHIR).   

- **Ecosistemas de atención sanitaria:**  Aunque las historias clínicas electrónicas (EHR) existen como principal origen de información verídica en muchos contextos clínicos, no es infrecuente que los proveedores tengan varias bases de datos que no están conectadas entre sí o que almacenen los datos en diferentes formatos.  El uso del servicio FHIR como servicio que se encuentra encima de esos sistemas permite estandarizar los datos en formato FHIR.  Esto contribuye a permitir el intercambio de datos entre varios sistemas con un formato de datos coherente. 

- **Investigación:** Los investigadores sanitarios encontrarán el estándar FHIR en general y el servicio FHIR útil, ya que normaliza los datos en torno a un modelo de datos FHIR común y reduce la carga de trabajo para el aprendizaje automático y el uso compartido de datos.
Exchange de datos a través del servicio FHIR proporciona registros de auditoría y controles de acceso que ayudan a controlar el flujo de datos y quién tiene acceso a qué tipos de datos. 

## <a name="fhir-from-microsoft"></a>Funcionalidades FHIR de Microsoft

Las funcionalidades de FHIR de Microsoft están disponibles en tres configuraciones:

* El servicio FHIR en las API de Atención sanitaria de Azure: una oferta de PaaS en Azure, que se aprovisiona fácilmente en el Azure Portal y administra Microsoft. Incluye la capacidad de aprovisionar otros conjuntos de datos, como DICOM en la misma área de trabajo. Está disponible en versión preliminar pública. 
* Azure API for FHIR: una oferta de PaaS en Azure, que se aprovisiona fácilmente en el Azure Portal y que administra Microsoft. Esta implementación solo incluye datos de FHIR y es un producto de ga-ga. 
* FHIR Server for Azure: un proyecto de código abierto que se puede implementar en la suscripción de Azure y que está disponible en GitHub en https://github.com/Microsoft/fhir-server.

Para aquellos casos de uso que requieran la ampliación o personalización del servidor de FHIR, o que requieran acceso a los servicios subyacentes, como a la base de datos, sin pasar por las API de FHIR, los desarrolladores deberían elegir la solución FHIR Server for Azure de código abierto.  Para la implementación de una API de FHIR inmediata y lista para producción y un servicio back-end en el que solo se debe acceder a los datos persistentes a través de la API de FHIR, los desarrolladores deben elegir el servicio FHIR.

## <a name="next-steps"></a>Pasos siguientes

Para empezar a trabajar con el servicio FHIR, siga el inicio rápido de 5 minutos para implementar el servicio FHIR.

>[!div class="nextstepaction"]
>[Implementación de un servicio de FHIR en Azure Healthcare APIs mediante el portal](fhir-portal-quickstart.md)
