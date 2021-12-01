---
title: 'Tutorial: Da Dag Plan Net - Azure API for FHIR'
description: Este tutorial le guía a través de la configuración del servicio FHIR en Azure API for FHIR para pasar las pruebas de Touchstone para la Guía de implementación de datos del Exchange da DaHir.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 11/29/2021
ms.openlocfilehash: 84dd99b8712eda5cff23bd7702f1336598ab26df
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2021
ms.locfileid: "133361846"
---
# <a name="da-vinci-plan-net-for-azure-api-for-fhir"></a>Da Planes Net para Azure API for FHIR

En este tutorial, le guiaremos a través de la configuración del servicio FHIR en Azure API for FHIR para pasar las pruebas de [Touchstone](https://touchstone.aegis.net/touchstone/) para la Guía de implementación de DaDex PDEX Payer Network (Plan-Net).

## <a name="touchstone-capability-statement"></a>Instrucción de funcionalidad de Touchstone

La primera prueba en la que nos centraremos es probar Azure API for FHIR con la instrucción [de funcionalidad Plan-Net Da Tests](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS). Si ejecuta esta prueba sin actualizaciones, se producirá un error en la prueba debido a que faltan parámetros de búsqueda y perfiles que faltan.

## <a name="define-search-parameters"></a>Definición de parámetros de búsqueda

Como parte de Da Dag Plan-Net IG, deberá definir [](how-to-do-custom-search.md) seis nuevos parámetros de búsqueda para los recursos De atención sanitaria, Plan de seguros, Rol profesional, Organización y Afiliación de la organización. Los seis se prueban en la instrucción de funcionalidad:

* [Área de cobertura del servicio sanitario](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-healthcareservice-coverage-area.html)
* [Área de cobertura del plan de seguros](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-coverage-area.html)
* [Tipo de plan de seguros](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-insuranceplan-plan-type.html)
* [Área de cobertura de la organización](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organization-coverage-area.html)
* [Red de afiliación de la organización](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-organizationaffiliation-network.html)
* [Red de rol profesional](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/SearchParameter-practitionerrole-network.html)

> [!NOTE]
> En el json sin formato para estos parámetros de búsqueda, el nombre se establece en `Plannet_sp_<Resource Name>_<SearchParameter Name>` . La prueba de Touchstone espera que el nombre de estos sea solo `SearchParameter Name` el (área de cobertura, tipo de plan o red).

El resto de los parámetros de búsqueda necesarios para el Plan-Net IG de Da Dag se definen mediante la especificación base y ya están disponibles en Azure API for FHIR sin actualizaciones adicionales.

## <a name="store-profiles"></a>Almacenamiento de perfiles

Fuera de la definición de parámetros de búsqueda, debe cargar los [perfiles y](./validation-against-profiles.md#storing-profiles) extensiones necesarios para superar esta prueba. Hay nueve perfiles que se usan como parte del grupo Da Dag Plan-Net IG:

* [Punto de conexión de Plan-Net](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Endpoint.html)
* [Plan-Net Healthcare Service](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-HealthcareService.html)
* [Plan-Net InsurancePlan](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-InsurancePlan.html) 
* [Ubicación de Plan-Net](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Location.html)
* [Plan-Net Network](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Network.html)
* [Organización de Plan-Net](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Organization.html)
* [Organización de Plan-NetAffiliation](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-OrganizationAffiliation.html)
* [Profesional de Plan-Net](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-Practitioner.html)
* [Plan-Net PractitionerRole](http://hl7.org/fhir/us/davinci-pdex-plan-net/STU1/StructureDefinition-plannet-PractitionerRole.html)

## <a name="sample-rest-file"></a>Archivo de rest de ejemplo

Para ayudar a crear estos parámetros y perfiles de búsqueda, tenemos un archivo HTTP de ejemplo en el sitio de código abierto que incluye todos los pasos descritos anteriormente en un único archivo. Una vez que haya cargado todos los perfiles y parámetros de búsqueda necesarios, puede ejecutar la prueba de la instrucción de funcionalidad en Touchstone.

:::image type="content" source="media/davinci-plan-net/davinci-plan-net-test-script-execution-passed.png" alt-text="Se ha pasado el script de ejecución de pruebas rest de ejemplo de Da Dag Plan Net":::

## <a name="touchstone-error-handling-test"></a>Prueba de control de errores de Touchstone

La segunda prueba que se va a recorrer es probar el [control de errores.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/01-Error-Codes&activeOnly=false&contentEntry=TEST_SCRIPTS) El único paso que debe hacer es eliminar un recurso HealthcareService de la base de datos y usar el identificador del recurso HealthcareService eliminado en la prueba. El archivo [DaVinci_PlanNet.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet.http) de ejemplo en el sitio de código abierto proporciona un ejemplo de HealthcareService para publicar y eliminar para este paso.

:::image type="content" source="media/davinci-plan-net/davinci-test-script-execution-passed.png" alt-text="Se ha pasado el script de ejecución de pruebas de error de la prueba de error de Da Dag Plan Net":::

## <a name="touchstone-query-test"></a>Prueba de consulta de Touchstone

La siguiente prueba que se va a recorrer es la prueba [de funcionalidades de consulta](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PlanNet/03-Query&activeOnly=false&contentEntry=TEST_SCRIPTS). Esta prueba está probando la conformidad con los perfiles que cargó en la primera prueba. Deberá tener recursos cargados que se ajusten a los perfiles. La mejor ruta de acceso sería probar con los recursos que ya tiene en la base de datos, pero también tenemos el archivo [DaVinci_PlanNet_Sample_Resources.http](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciPlanNet/DaVinci_PlanNet_Sample_Resources.http) con recursos de ejemplo que se extraían de los ejemplos de la IG que puede usar para crear los recursos y realizar pruebas.  

:::image type="content" source="media/davinci-plan-net/touchstone-query-test-execution-failed.png" alt-text="Error en la prueba de consulta net del plan Da Dag":::

> [!NOTE]
> Con los recursos de ejemplo proporcionados, debería esperar una tasa de éxito del 98 % de las pruebas de consulta.
> Hay un problema de GitHub en el servidor de FHIR que está causando un error en una de estas pruebas.
Recurso devuelto varias veces si cumple los criterios base y _include base. [#2037](https://github.com/microsoft/fhir-server/issues/2037) 

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se ha recorrido la configuración de Azure API for FHIR para pasar las pruebas de Touchstone para la Guía de implementación de Da Dag PDEX Payer Network (Plan-Net). Para obtener más información sobre las características admitidas en Azure API for FHIR, consulte

>[!div class="nextstepaction"]
>[Características compatibles](fhir-features-supported.md)
