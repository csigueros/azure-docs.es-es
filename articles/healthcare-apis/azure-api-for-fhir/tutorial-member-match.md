---
title: 'Tutorial: $member coincidencia de coincidencias : Azure API for FHIR'
description: En este tutorial se presenta la $member de coincidencia de datos que se define como parte del registro de estado de DaNess Exchange (HRex).
services: healthcare-apis
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: tutorial
ms.reviewer: matjazl
ms.author: cavoeg
author: caitlinv39
ms.date: 06/01/2021
ms.openlocfilehash: 592a5a473cf000713bc8a266e36516bc777a6e62
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121785879"
---
# <a name="member-match-operation-for-azure-api-for-fhir"></a>$member de coincidencia de Azure API for FHIR

[$member coincidencia es](http://hl7.org/fhir/us/davinci-hrex/2020Sep/OperationDefinition-member-match.html) una operación que se define como parte del registro de estado de DaNess Exchange (HRex). En esta guía, le guiaremos por lo que $member coincidencia y cómo usarlo.

## <a name="overview-of-member-match"></a>Información general sobre $member coincidencia

La operación de coincidencia de $member se creó para ayudar con el intercambio de datos de pago a pago, al permitir que un nuevo pagador obtenga un identificador único para un paciente del pagador anterior del paciente. La $member de coincidencia de datos requiere que se pasen tres fragmentos de información en el cuerpo de la solicitud:

* Datos demográficos de los pacientes

* La información de cobertura anterior

* La nueva información de cobertura (no necesaria en función de nuestra implementación)

Una vez pasados los datos, el Azure API for FHIR valida que puede encontrar un paciente que coincida exactamente con los datos demográficos pasados con la información de cobertura anterior que se ha pasado. Si se encuentra un resultado, la respuesta será un paquete con los datos originales del paciente más un nuevo identificador agregado desde el pagador anterior y la información de cobertura anterior.

> [!NOTE]
> La especificación describe cómo pasar y devolver la nueva información de cobertura. Hemos decidido omitir los datos para mantener los resultados más pequeños.

## <a name="example-of-member-match"></a>Ejemplo de $member-match

Para usar $member coincidencia, use la siguiente llamada:

`POST {{fhirurl}}/Patient/$member-match`

Deberá incluir un recurso de parámetros en el cuerpo que incluya el paciente, la cobertura anterior y la nueva cobertura. Para ver una representación JSON, consulte solicitud [$member ejemplo de coincidencia.](http://hl7.org/fhir/us/davinci-hrex/2020Sep/Parameters-member-match-in.json.html)

Si se encuentra una coincidencia única, recibirá una respuesta 200 con otro identificador agregado:

:::image type="content" source="media/cms-tutorials/two-hundred-response.png" alt-text="2000 código de respuesta.":::

Si el $member coincidencia no encuentra una coincidencia única, recibirá una respuesta 422 con un código de error.

## <a name="next-steps"></a>Pasos siguientes

En esta guía, ha aprendido sobre la operación $member coincidencia. A continuación, puede obtener información sobre cómo probar los datos del Exchange Da Dag en IG en Touchstone, lo que requiere la $member de coincidencia.

>[!div class="nextstepaction"]
>[DaVinci PDex](../fhir/davinci-pdex-tutorial.md)