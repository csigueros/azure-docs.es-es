---
title: Uso de patient-everything en Azure API for FHIR
description: En este artículo se explica cómo usar la operación patient-everything en Azure API for FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 06/04/2021
ms.author: cavoeg
ms.openlocfilehash: 935084c12551dcbebecf3f10bb0e6eff19eff45f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780776"
---
# <a name="patient-everything-in-fhir"></a>Patient-everything en FHIR

La operación [$patient-everything](https://www.hl7.org/fhir/patient-operation-everything.html) se usa para proporcionar a un paciente acceso a todo su registro o para que un proveedor u otro usuario realicen una descarga masiva de datos. Esta operación se usa para devolver toda la información relacionada con uno o varios pacientes descrita en el recurso o contexto en el que se invoca esta operación.  

## <a name="use-patient-everything"></a>Uso de patient-everything
Para llamar a patient-everything, use el comando siguiente:

```json
GET {FHIRURL}/Patient/{ID}/$everything
```
Azure API for FHIR valida que puede encontrar el paciente que coincide con el identificador de paciente proporcionado. Si se encuentra un resultado, la respuesta será un conjunto de tipo "searchset" con la siguiente información: 
* [Recurso de paciente](https://www.hl7.org/fhir/patient.html) 
*  Recursos a los que hace referencia directamente el recurso Patient (excepto el vínculo) 
*  Recursos en el [compartimiento de pacientes](https://www.hl7.org/fhir/compartmentdefinition-patient.html)
*  [Recursos de dispositivo](https://www.hl7.org/fhir/device.html) que hacen referencia al recurso de paciente. Esto se limita a 100 dispositivos. Si el paciente tiene más de 100 dispositivos vinculados, solo se devolverán 100. 


## <a name="patient-everything-parameters"></a>Parámetros de patient-everything
Azure API for FHIR admite los siguientes parámetros de consulta. Todos estos parámetros son opcionales:

|Parámetro de consulta        |  Descripción|
|-----------------------|------------|
| \_type | Permite especificar los tipos de recursos que se van a incluir en la respuesta. Por ejemplo, \_ type=Encounter devolvería solo los recursos `Encounter` asociados al paciente. |
| \_since | Solo devolverá los recursos que se han modificado desde el momento indicado. |
| start | Si se especifica la fecha de inicio, se extraerán los recursos en los que su fecha clínica sea posterior a la fecha de inicio especificada. Si no se proporciona ninguna fecha de inicio, todos los registros anteriores a la fecha de finalización están en el ámbito. |
| end | Si se especifica la fecha de finalización, se extraerán los recursos en los que su fecha clínica sea anterior a la fecha de finalización especificada. Si no se proporciona ninguna fecha de finalización, todos los registros posteriores a la fecha de inicio están en el ámbito. |

> [!Note]
> Tendrá que especificar un identificador para un paciente específico. Si necesita todos los datos para todos los pacientes, vea [$export](../data-transformation/export-data.md). 


## <a name="examples-of-patient-everything"></a>Ejemplos de $patient-everything 

A continuación se muestran algunos ejemplos del uso de la operación $patient-everything. 

Para usar $patient-everything a fin de consultar "todo" lo relativo a un paciente entre 2010 y 2020, use la siguiente llamada: 

```json
GET {FHIRURL}/Patient/{ID}/$everything?start=2010&end=2020
``` 

Para usar $patient-everything a fin de consultar los valores Observation (Observación) y Encounter (Encuentro) de un paciente, use la siguiente llamada: 
```json
GET {FHIRURL}/Patient/{ID}/$everything_type=Observation,Encounter 
```

Para usar $patient-everything para consultar "todo" lo relativo a un paciente desde 2021-05-27T05:00:00Z, use la siguiente llamada: 

```json
GET {FHIRURL}/Patient/{ID}/$everything?_since=2021-05-27T05:00:00Z 
```

Si se encuentra un paciente para cada una de estas llamadas, se obtiene una respuesta 200 con un paquete de los recursos correspondientes.

## <a name="next-step"></a>Paso siguiente
Ahora que sabe cómo usar la operación patient-everything, puede obtener más información sobre las opciones de búsqueda en la introducción a la guía de búsqueda.

>[!div class="nextstepaction"]
>[Introducción a la búsqueda de FHIR](overview-of-search.md)