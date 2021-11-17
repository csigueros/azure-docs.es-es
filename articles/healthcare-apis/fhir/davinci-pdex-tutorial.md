---
title: 'Tutorial: DaIque PDex: API de Azure Healthcare (versión preliminar)'
description: Este tutorial le guía a través de la configuración del servicio FHIR para superar las pruebas de la Guía de implementación de datos del Exchange Da Dag Payer.
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.author: cavoeg
author: caitlinv39
ms.date: 11/12/2021
ms.openlocfilehash: 12f7cbac225cfe4e85d2df21c363d4c08fb0b4c6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132494462"
---
# <a name="da-vinci-pdex"></a>Da Vinci PDex

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

En este tutorial, le guiaremos a través de la configuración del servicio FHIR en las API de Azure Healthcare (que aquí se denomina servicio FHIR) para pasar las pruebas de [Touchstone](https://touchstone.aegis.net/touchstone/) para la Guía de implementación de Exchange de [da-payer](http://hl7.org/fhir/us/davinci-pdex/toc.html) data (PDex IG).

> [!NOTE]
> El servicio FHIR solo admite JSON. El servicio FHIR de código abierto de Microsoft admite JSON y XML, y en código abierto puede usar el parámetro _format para ver la instrucción de funcionalidad XML: `GET {fhirurl}/metadata?_format=xml`

## <a name="touchstone-capability-statement"></a>Instrucción de funcionalidad de Touchstone

El primer conjunto de pruebas en el que nos centraremos es probar el servicio FHIR con la instrucción de funcionalidad PDex IG. Esto incluye tres pruebas:

* La primera prueba valida la instrucción de funcionalidad básica con respecto a los requisitos de IG y se pasará sin actualizaciones.

* La segunda prueba valida que se han agregado todos los perfiles para US Core. Esta prueba se superará sin actualizaciones, pero incluirá una serie de advertencias. Para quitar estas advertencias, debe [cargar los perfiles de US Core.](validation-against-profiles.md) Hemos creado un archivo [HTTP de ejemplo que](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/USCore.http) le guía a través de la creación de todos los perfiles. También puede obtener los [perfiles directamente](http://hl7.org/fhir/us/core/STU3.1.1/profiles.html#profiles) desde el sitio HL7, que tendrá las versiones más recientes.

* La tercera prueba valida que se [admite $patient operación de todos](patient-everything.md) los componentes.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-failed.png" alt-text="Error en la ejecución de Da Dag PDex.":::

## <a name="touchstone-member-match-test"></a>Prueba de coincidencia $member de touchstone

La [segunda prueba de](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/01-Member-Match&activeOnly=false&contentEntry=TEST_SCRIPTS) la sección Payer Data Exchange comprueba la existencia de la operación $member coincidencia de [pago.](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html) Puede obtener más información sobre la operación $member coincidencia en nuestra información general $member [de la operación de coincidencia de datos.](tutorial-member-match.md)

En esta prueba, deberá cargar algunos datos de ejemplo para que la prueba se pase. Aquí tenemos un archivo rest [con](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/membermatch.http) el paciente y la cobertura vinculada que necesitará para la prueba. Una vez cargados estos datos, podrá superar correctamente esta prueba. Si no se cargan los datos, recibirá una respuesta 422 debido a que no encuentra una coincidencia exacta.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-script-passed.png" alt-text="Se ha superado el script de prueba de Da Dag PDex.":::

## <a name="touchstone-patient-by-reference"></a>Paciente de Touchstone por referencia

Las siguientes pruebas que revisaremos son las pruebas [de paciente por referencia.](https://touchstone.aegis.net/touchstone/testdefinitions?selectedTestGrp=/FHIRSandbox/DaVinci/FHIR4-0-1-Test/PDEX/PayerExchange/02-PatientByReference&activeOnly=false&contentEntry=TEST_SCRIPTS) Este conjunto de pruebas validan que puede encontrar un paciente en función de varios criterios de búsqueda. La mejor manera de probar al paciente por referencia será realizar pruebas [](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PayerDataExchange/PDex_Sample_Data.http) con sus propios datos, pero hemos cargado un archivo de recursos de ejemplo que también puede cargar para usarlo.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-execution-passed.png" alt-text="Se ha pasado la ejecución de Da Dag PDex.":::

## <a name="touchstone-patienteverything-test"></a>Prueba de pacientes/$everything touchstone

La prueba final que vamos a recorrer es probar patient-everything. Para esta prueba, deberá cargar un paciente y, a continuación, usará el identificador de ese paciente para probar que puede usar la operación $everything para extraer todos los datos relacionados con el paciente.

:::image type="content" source="media/centers-medicare-services-tutorials/davinci-pdex-test-patient-everything.png" alt-text="se ha superado la prueba de $everything táctil.":::

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, se explica cómo superar las pruebas de Exchange pago en Touchstone. A continuación, puede obtener información sobre cómo probar la Guía de implementación de DaDex PDEX Payer Network (Plan-Net).

>[!div class="nextstepaction"]
>[Da Vinci Plan Net](davinci-plan-net.md)  
