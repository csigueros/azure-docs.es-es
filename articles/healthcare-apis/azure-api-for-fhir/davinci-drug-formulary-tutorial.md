---
title: Tutorial sobre la fórmula de la receta de Da Dag para Azure API for FHIR
description: Este tutorial le guía a través de la configuración Azure API for FHIR para pasar las pruebas de Touchstone en la guía de implementación de DaVinci Drug Formulary.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 11/29/2021
ms.openlocfilehash: 86efbb3c0d0dccdc99ef9364d9c579cb5c7655bb
ms.sourcegitcommit: 66b6e640e2a294a7fbbdb3309b4829df526d863d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2021
ms.locfileid: "133398424"
---
# <a name="tutorial-for-da-vinci-drug-formulary-for-azure-api-for-fhir"></a>Tutorial de DaAnalysis Drug Formulary for Azure API for FHIR

En este tutorial, le guiaremos a través de la configuración de Azure API for FHIR para pasar las pruebas de [Touchstone](https://touchstone.aegis.net/touchstone/) para la Guía de implementación del formulario de formularios de [da-payer data Exchange](http://hl7.org/fhir/us/Davinci-drug-formulary/)da de Estados Unidos.

## <a name="touchstone-capability-statement"></a>Instrucción de funcionalidad de Touchstone

La primera prueba en la que nos centraremos es probar Azure API for FHIR con la instrucción de funcionalidad [De la formulación](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS)de la receta de cáncer . Si ejecuta esta prueba sin ninguna actualización, se producirá un error en la prueba debido a la falta de parámetros de búsqueda y a la falta de perfiles.

### <a name="define-search-parameters"></a>Definición de parámetros de búsqueda

Como parte de la ig del fórmulado de productos de Da York, deberá definir tres nuevos parámetros de búsqueda [para](how-to-do-custom-search.md) el recurso FormularyDrug. Los tres se prueban en la instrucción de funcionalidad.

* [DrugTier](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugTier.json.html)
* [Plan de consumo](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugPlan.json.html)
* [Nombre de latual](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugName.json.html)

El resto de los parámetros de búsqueda necesarios para el igbundo de la fórmula de la receta de da asco se definen mediante la especificación base y ya están disponibles en Azure API for FHIR sin más actualizaciones.

### <a name="store-profiles"></a>Almacenamiento de perfiles

Fuera de definir los parámetros de búsqueda, la única otra actualización que debe realizar para superar esta prueba es cargar los [perfiles necesarios.](validation-against-profiles.md) Hay dos perfiles que se usan como parte del IG de la fórmula de la receta de da asco.

* [Formulary Drug](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-FormularyDrug.html)
* [Plan de cobertura de formularios](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-CoveragePlan.html)

### <a name="sample-rest-file"></a>Archivo de rest de ejemplo

Para ayudar con la creación de estos perfiles y parámetros de búsqueda, tenemos el archivo HTTP de ejemplo de la fórmula da [DaEtt](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary.http) en el sitio de código abierto que incluye todos los pasos descritos anteriormente en un único archivo. Una vez que haya cargado todos los perfiles y parámetros de búsqueda necesarios, puede ejecutar la prueba de la instrucción de funcionalidad en Touchstone. Debe obtener una ejecución correcta:

:::image type="content" source="media/cms-tutorials/davinci-test-script-execution.png" alt-text="Ejecución de scripts de prueba de Da Dag.":::

## <a name="touchstone-query-test"></a>Prueba de consulta de Touchstone

La segunda prueba son las [funcionalidades de consulta](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/01-Query&activeOnly=false&contentEntry=TEST_SCRIPTS). Esta prueba valida que puede buscar recursos específicos del plan de cobertura y de la salud mediante varios parámetros. La mejor ruta de acceso sería probar con los recursos que ya tiene en la base de datos, pero también tenemos el archivo HTTP [da VinciFormulary_Sample_Resources](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary_Sample_Resources.http) disponible con recursos de ejemplo que se extraían de los ejemplos de la IG que puede usar para crear los recursos y realizar pruebas.

:::image type="content" source="media/cms-tutorials/davinci-test-execution-results.png" alt-text="Resultados de la ejecución de pruebas de Da Dag.":::

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se explica cómo pasar los datos del pagador Da Dag Exchange de estados unidos en Touchstone. A continuación, puede obtener información sobre cómo probar la Guía de implementación de DaDex PDex en Touchstone.

>[!div class="nextstepaction"]
>[Da Vinci PDex](davinci-pdex-tutorial.md)