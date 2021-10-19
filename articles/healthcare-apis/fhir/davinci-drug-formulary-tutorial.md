---
title: Tutorial de la fórmula de da-da-puy
description: Este tutorial le guía a través de la configuración del servicio FHIR para pasar las pruebas de Touchstone en la guía de implementación de DaVinci Health Formulary.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: modillon
ms.date: 08/06/2021
ms.openlocfilehash: 56cf21f960cadd54340be48deeb44b1e37470f69
ms.sourcegitcommit: 28cd7097390c43a73b8e45a8b4f0f540f9123a6a
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122778821"
---
# <a name="tutorial-for-da-vinci-drug-formulary"></a>Tutorial del formulario de da-da-da-10000001

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este tutorial, le guiaremos a través de la configuración del servicio FHIR en las API de Atención sanitaria de Azure (que aquí se denomina servicio FHIR) para pasar las pruebas de [Touchstone](https://touchstone.aegis.net/touchstone/) para da [DaHir Payer Data Exchange US Health Formulary Implementation Guide](http://hl7.org/fhir/us/Davinci-drug-formulary/).

## <a name="touchstone-capability-statement"></a>Instrucción de funcionalidad de Touchstone

La primera prueba en la que nos centraremos es probar el servicio FHIR con la instrucción de funcionalidad [DaHir Drug Formulary](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/00-Capability&activeOnly=false&contentEntry=TEST_SCRIPTS). Si ejecuta esta prueba sin actualizaciones, se producirá un error en la prueba debido a que faltan parámetros de búsqueda y perfiles que faltan.

### <a name="define-search-parameters"></a>Definición de parámetros de búsqueda

Como parte del ig de la fórmula de la receta de da york, deberá definir tres nuevos parámetros de búsqueda [para](how-to-do-custom-search.md) el recurso FormularyDrug. Los tres se prueban en la instrucción de funcionalidad.

* [DrugTier](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugTier.json.html)
* [Plan de consumo](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugPlan.json.html)
* [Nombre DeCombreDeCombre](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/SearchParameter-DrugName.json.html)

El resto de los parámetros de búsqueda necesarios para la IG de da-hir se definen mediante la especificación base y ya están disponibles en el servicio FHIR sin más actualizaciones.

### <a name="store-profiles"></a>Almacenamiento de perfiles

Fuera de la definición de parámetros de búsqueda, la única otra actualización que debe realizar para superar esta prueba es cargar los [perfiles necesarios.](validation-against-profiles.md) Hay dos perfiles que se usan como parte de la fórmula de dación de receta.

* [Formulary Drug](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-FormularyDrug.html)
* [Plan de cobertura de formularios](http://hl7.org/fhir/us/davinci-drug-formulary/STU1.0.1/StructureDefinition-usdf-CoveragePlan.html)

### <a name="sample-rest-file"></a>Archivo de rest de ejemplo

Para ayudar con la creación de estos perfiles y parámetros de búsqueda, tenemos el archivo HTTP de ejemplo de Da [Dag Formulary](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary.http) en el sitio de código abierto que incluye todos los pasos descritos anteriormente en un único archivo. Una vez que haya cargado todos los perfiles y parámetros de búsqueda necesarios, puede ejecutar la prueba de la instrucción de funcionalidad en Touchstone. Debe obtener una ejecución correcta:

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-test-script-execution.png" alt-text="Ejecución del script de prueba de Da Dag.":::

## <a name="touchstone-query-test"></a>Prueba de consulta de Touchstone

La segunda prueba son las [funcionalidades de consulta](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/Formulary/01-Query&activeOnly=false&contentEntry=TEST_SCRIPTS). Esta prueba valida que puede buscar recursos específicos del plan de cobertura y de la salud mediante varios parámetros. La mejor ruta de acceso sería probar con los recursos que ya tiene en la base de datos, pero también tenemos el archivo HTTP da [VinciFormulary_Sample_Resources](https://github.com/microsoft/fhir-server/blob/main/docs/rest/DaVinciFormulary/DaVinciFormulary_Sample_Resources.http) disponible con recursos de ejemplo que se extraían de los ejemplos de la IG que puede usar para crear los recursos y realizar pruebas.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-test-execution-results.png" alt-text="Resultados de la ejecución de pruebas de Da Dag.":::

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se explica cómo pasar los datos del pagador de da hay que Exchange de estados unidos en Touchstone. A continuación, puede obtener información sobre cómo probar la Guía de implementación de Da Dag PDex en Touchstone.

>[!div class="nextstepaction"]
>[Da Vinci PDex](davinci-pdex-tutorial.md)