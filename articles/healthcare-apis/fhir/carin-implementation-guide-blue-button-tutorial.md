---
title: CARIN Blue Button Implementation Guide for Blue Button
description: Este tutorial le guía por los pasos de configuración del servicio FHIR para pasar las pruebas de Touchstone para la Guía de implementación de CARIN para el botón azul (C4BB IG).
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 08/03/2021
ms.openlocfilehash: 4aebd92ea55e789a42444bc488dcaf2c02b2acb1
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778371"
---
# <a name="carin-implementation-guide-for-blue-button174"></a>Guía de implementación de CARIN para el botón azul&#174;

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este tutorial, le guiaremos por la configuración del servicio FHIR en las API de Azure Healthcare (por lo que aquí se denomina servicio FHIR) para pasar las pruebas de [Touchstone](https://touchstone.aegis.net/touchstone/) para la Guía de implementación de [CARIN](https://build.fhir.org/ig/HL7/carin-bb/index.html) para el botón azul (C4BB IG).

## <a name="touchstone-capability-statement"></a>Instrucción de funcionalidad de Touchstone

La primera prueba en la que nos centraremos es probar el servicio FHIR con la instrucción de funcionalidad [C4BB IG](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS). Si ejecuta esta prueba en el servicio FHIR sin ninguna actualización, se producirá un error en la prueba debido a la falta de parámetros de búsqueda y a la falta de perfiles. 

### <a name="define-search-parameters"></a>Definición de parámetros de búsqueda

Como parte de C4BB IG, deberá definir tres nuevos parámetros [de búsqueda](how-to-do-custom-search.md) para el `ExplanationOfBenefit` recurso. Dos de ellos se prueban en la instrucción de funcionalidad (tipo y fecha de servicio) y uno es necesario para `_include` las búsquedas (medidas de seguridad).  

* [type](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-type.json)
* [service-date](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-service-date.json)
* [Asegurador](https://build.fhir.org/ig/HL7/carin-bb/SearchParameter-explanationofbenefit-insurer.json)

> [!NOTE]
> En el código JSON sin formato para estos parámetros de búsqueda, el nombre se establece en `ExplanationOfBenefit_<SearchParameter Name>` . La prueba de Touchstone espera que el nombre de estos sea **de tipo**, **service-date** y **de la compañía de seguros**.  
 
El resto de los parámetros de búsqueda necesarios para C4BB IG se definen mediante la especificación base y ya están disponibles en el servicio FHIR sin ninguna actualización adicional.
 
### <a name="store-profiles"></a>Almacenamiento de perfiles

Fuera de definir los parámetros de búsqueda, la otra actualización que debe realizar para superar esta prueba es cargar los [perfiles necesarios.](validation-against-profiles.md) Hay ocho perfiles definidos dentro de C4BB IG. 

* [Cobertura C4BB](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Coverage.html) 
* [C4BB ExplanationOfBenefit Inpatient Institutional](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Inpatient-Institutional.html) 
* [Explicación de C4BBOfBenefit Outpatient Institutional](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Outpatient-Institutional.html) 
* [Explicación de C4BBOfBenefit Infirible](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Pharmacy.html) 
* [C4BB ExplanationOfBenefit Professional NonCcian](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-ExplanationOfBenefit-Professional-NonClinician.html) 
* [Organización C4BB](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Organization.html) 
* [C4BB Patient](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Patient.html) 
* [Profesional de C4BB](https://build.fhir.org/ig/HL7/carin-bb/StructureDefinition-C4BB-Practitioner.html) 

### <a name="sample-rest-file"></a>Archivo de rest de ejemplo

Para ayudar con la creación de estos perfiles y parámetros de búsqueda, tenemos un archivo [HTTP](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB.http) de ejemplo que incluye todos los pasos descritos anteriormente en un único archivo. Una vez que haya cargado todos los perfiles y parámetros de búsqueda necesarios, puede ejecutar la prueba de la instrucción de funcionalidad en Touchstone.

:::image type="content" source="media/centers-medicare-services-tutorials/capability-test-script-execution-results.png" alt-text="Resultados de la ejecución del script de prueba de funcionalidad.":::

## <a name="touchstone-read-test"></a>Prueba de lectura de Touchstone

Después de probar la instrucción de funcionalidades, probaremos las funcionalidades [de](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/01-Read&activeOnly=false&contentEntry=TEST_SCRIPTS) lectura del servicio FHIR con la IG de C4BB. Esta prueba está probando la conformidad con los ocho perfiles que cargó en la primera prueba. Deberá tener los recursos cargados que se ajusten a los perfiles. La mejor ruta de acceso sería probar con los recursos que ya tiene en la base de datos, pero también tenemos un archivo [HTTP](https://github.com/microsoft/fhir-server/blob/main/docs/rest/C4BB/C4BB_Sample_Resources.http) disponible con recursos de ejemplo que se extraían de los ejemplos de la IG que puede usar para crear los recursos y realizar pruebas.

:::image type="content" source="media/centers-medicare-services-tutorials/test-execution-results-touchstone.png" alt-text="Resultados de la ejecución de pruebas de lectura de Touchstone.":::

## <a name="touchstone-eob-query-test"></a>Prueba de consulta EOB de Touchstone

La siguiente prueba que revisaremos es la [prueba de consulta EOB](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/02-EOBQuery&activeOnly=false&contentEntry=TEST_SCRIPTS). Si ya ha completado la prueba de lectura, tendrá todos los datos cargados que necesitará. Esta prueba valida que puede buscar recursos y `Patient` específicos `ExplanationOfBenefit` mediante varios parámetros.

:::image type="content" source="media/centers-medicare-services-tutorials/test-execution-touchstone-eob-query-test.png" alt-text="Resultados de la ejecución de consultas EOB de Touchstone.":::

## <a name="touchstone-error-handling-test"></a>Prueba de control de errores de Touchstone

La prueba final que se recorrerá es probar el [control de errores.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/CARIN/CARIN-4-BlueButton/99-ErrorHandling&activeOnly=false&contentEntry=TEST_SCRIPTS) El único paso que debe realizar es eliminar un recurso ExplanationOfBenefit de la base de datos y usar el identificador del recurso `ExplanationOfBenefit` eliminado en la prueba.

:::image type="content" source="media/centers-medicare-services-tutorials/test-execution-touchstone-error-handling.png" alt-text="Resultados del control de errores de EOB de Touchstone.":::


## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se explica cómo pasar las pruebas de CARIN IG para el botón azul en Touchstone. A continuación, puede revisar cómo probar las pruebas de formulario de Da Vinci.

>[!div class="nextstepaction"]
>[DaVinci Drug Formulary](davinci-drug-formulary-tutorial.md)       
 
