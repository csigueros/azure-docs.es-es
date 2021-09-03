---
title: Ejemplos de búsqueda de Azure API for FHIR
description: Cómo buscar mediante diferentes parámetros de búsqueda, modificadores y otras herramientas de búsqueda de FHIR
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/21/2021
ms.author: cavoeg
ms.openlocfilehash: 731b85d0486c3d9fa7ba9e3af4cc2f047912fcdc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780732"
---
# <a name="fhir-search-examples-for-azure-api-for-fhir"></a>Ejemplos de búsqueda de FHIR para Azure API for FHIR

A continuación se muestran algunos ejemplos de uso de las operaciones de búsqueda de FHIR, incluidos los parámetros y modificadores de búsqueda, la búsqueda en cadena y en cadena inversa, la búsqueda compuesta, la visualización del siguiente conjunto de entradas para los resultados de la búsqueda y la búsqueda con una solicitud `POST`. Para obtener más información sobre la búsqueda, vea [Introducción a la búsqueda de FHIR](overview-of-search.md).
   
## <a name="search-result-parameters"></a>Parámetros de resultados de la búsqueda

### <a name="_include"></a>_include

`_include` busca entre los recursos aquellos que incluyen el parámetro especificado del recurso. Por ejemplo, puede buscar entre recursos `MedicationRequest` para encontrar solo los que incluyen información sobre las recetas de un paciente específico, que es el parámetro de `reference` `patient`. En el ejemplo siguiente, se extraerán todos los objetos `MedicationRequests` y se hace referencia a todos los pacientes desde `MedicationRequests`:

```rest
 GET [your-fhir-server]/MedicationRequest?_include=MedicationRequest:patient

```

> [!NOTE]
> **_include** y **_revinclude** están limitados a 100 elementos.

### <a name="_revinclude"></a>_revinclude

`_revinclude` permite buscar en la dirección opuesta como `_include`. Por ejemplo, puede buscar pacientes y, a continuación, invertir incluir todos los contactos que hacen referencia a los pacientes:

```rest
GET [your-fhir-server]/Patient?_revinclude=Encounter:subject

```
### <a name="_elements"></a>_elements

`_elements` limita el resultado de la búsqueda a un subconjunto de campos para reducir el tamaño de la respuesta omitiendo los datos innecesarios. El parámetro acepta una lista separada por comas de elementos base:

```rest
GET [your-fhir-server]/Patient?_elements=identifier,active

```

En esta solicitud, recibirá un conjunto de pacientes, pero cada recurso solo incluirá los identificadores y el estado activo del paciente. Los recursos de esta respuesta devuelta contendrán un valor `meta.tag` de `SUBSETTED` para indicar que se trata de un conjunto incompleto de resultados.

## <a name="search-modifiers"></a>Modificadores de búsqueda

### <a name="not"></a>:not

`:not` permite buscar recursos en los que un atributo no es verdadero. Por ejemplo, podría buscar pacientes en los que el sexo no sea femenino:

```rest
GET [your-fhir-server]/Patient?gender:not=female

```

Como valor devuelto, se obtienen todas las entradas de pacientes en las que el sexo no es femenino, incluidos los valores vacíos (entradas especificadas sin género). Esto es diferente de buscar pacientes en los que el sexo es masculino, ya que eso no incluiría las entradas sin un género específico.

### <a name="missing"></a>:missing

`:missing` devuelve todos los recursos que no tienen un valor para el elemento especificado cuando el valor es `true`, y devuelve todos los recursos que contienen el elemento especificado cuando el valor es `false`. En el caso de los elementos de tipo de datos simples, `:missing=true` coincidirá con todos los recursos en los que el elemento esté presente con extensiones pero tenga un valor vacío. Por ejemplo, si desea encontrar todos los recursos `Patient` a los que falta información sobre la fecha de nacimiento, puede hacer lo siguiente:

```rest
GET [your-fhir-server]/Patient?birthdate:missing=true

```

### <a name="exact"></a>:exact
`:exact` se usa para los parámetros `string`, y devuelve resultados que coinciden con el parámetro con precisión, como en el caso del uso de mayúsculas y minúsculas y la concatenación de caracteres.

```rest
GET [your-fhir-server]/Patient?name:exact=Jon

```

Esta solicitud devuelve recursos `Patient` que tienen el nombre exactamente igual que `Jon`. Si el recurso tuviera pacientes con nombres como `Jonathan` o `joN`, la búsqueda omitiría el recurso, ya que no coincide exactamente con el valor especificado.

### <a name="contains"></a>:contains
`:contains` se usa para los parámetros `string` y busca recursos con coincidencias parciales del valor especificado en cualquier parte de la cadena dentro del campo en el que se está buscando. `contains` no tiene en cuenta las mayúsculas y minúsculas y permite la concatenación de caracteres. Por ejemplo:

```rest
GET [your-fhir-server]/Patient?address:contains=Meadow

```

Esta solicitud devolvería todos los recursos `Patient` con campos `address` que tienen valores que contienen la cadena "Meadow". Esto significa que podría obtener direcciones que incluyan valores como "Meadowers" o "59 Meadow ST" como resultados de la búsqueda.

## <a name="chained-search"></a>Búsqueda encadenada 

Para realizar una serie de operaciones de búsqueda que abarquen varios parámetros de referencia, puede "encadenar" la serie de parámetros de referencia anexándolos a la solicitud de servidor uno por uno mediante un punto `.`. Por ejemplo, si desea ver todos los recursos `DiagnosticReport` con una referencia `subject` a un recurso `Patient` que incluye un determinado `name`:  

```rest
 GET [your-fhir-server]/DiagnosticReport?subject:Patient.name=Sarah

```

Esta solicitud devolvería todos los recursos `DiagnosticReport` con una paciente llamada "Sarah". El punto `.` después del campo `Patient` realiza la búsqueda encadenada en el parámetro de referencia del parámetro `subject`.

Otro uso común de una búsqueda normal (no encadenada) es buscar todos los contactos médicos de un paciente específico. Los valores `Patient` a menudo tendrán uno o varios valores `Encounter` con un sujeto. Para buscar todos los recursos `Encounter` de `Patient` con el valor `id` proporcionado:

```rest
GET [your-fhir-server]/Encounter?subject=Patient/78a14cbe-8968-49fd-a231-d43e6619399f

```

Mediante la búsqueda encadenada, puede encontrar todos los recursos `Encounter` que coincidan con un fragmento determinado de información de `Patient`, como `birthdate`:

```rest
GET [your-fhir-server]/Encounter?subject:Patient.birthdate=1987-02-20

```

Esto permitiría no solo buscar recursos `Encounter` para un solo paciente, sino en todos los pacientes que tengan el valor de fecha de nacimiento especificado. 

Además, la búsqueda encadenada se puede realizar más de una vez en una solicitud mediante el símbolo `&`, que permite buscar varias condiciones en una solicitud. En tales casos, la búsqueda encadenada busca "independientemente" cada parámetro, en lugar de buscar condiciones que solo cumplan todas las condiciones a la vez:

```rest
GET [your-fhir-server]/Patient?general-practitioner:Practitioner.name=Sarah&general-practitioner:Practitioner.address-state=WA

```

Esto devolvería todos los recursos de `Patient` que tienen "Sarah" como `generalPractitioner` y tienen una propiedad `generalPractitioner` que tiene la dirección con el estado WA. En otras palabras, si un paciente tuviera a Sarah del estado NY y a Bill del estado WA como referencias de la propiedad `generalPractitioner` del paciente, se devolverían ambos.

Para los escenarios en los que la búsqueda tiene que ser una operación AND que abarque todas las condiciones como grupo, consulte el siguiente ejemplo de **búsqueda compuesta**.

## <a name="reverse-chain-search"></a>Búsqueda en cadena inversa

La búsqueda en cadena permite buscar recursos en función de las propiedades de los recursos a los que hacen referencia. El uso de la búsqueda en cadena inversa le permite hacerlo al revés. Puede buscar recursos en función de las propiedades de los recursos que hacen referencia a ellos, mediante el parámetro `_has`. Por ejemplo, el recurso `Observation` tiene un parámetro de búsqueda `patient` que hace referencia a un recurso de paciente. Para buscar todos los recursos de pacientes a los que hace referencia `Observation` con un elemento `code` específico:

```rest
GET [base]/Patient?_has:Observation:patient:code=527

```

Esta solicitud devuelve los recursos de pacientes a los que hace referencia `Observation` con el código `527`. 

Además, la búsqueda en cadena inversa puede tener una estructura recursiva. Por ejemplo, si desea buscar todos los pacientes que tienen `Observation` donde la observación tiene un evento de auditoría de un usuario `janedoe` específico, podría hacer lo siguiente:

```rest
GET [base]/Patient?_has:Observation:patient:_has:AuditEvent:entity:agent:Practitioner.name=janedoe

``` 

> [!NOTE]
> En Azure API for FHIR y el servidor FHIR de código abierto respaldado por Cosmos, la búsqueda encadenada y la búsqueda encadenada inversa son una implementación de MVP. Para realizar búsquedas encadenadas en Cosmos DB, la implementación le guía por la expresión de búsqueda y emite subconsultas para resolver los recursos coincidentes. Esto se hace para cada nivel de la expresión. Si alguna consulta devuelve más de 100 resultados, se producirá un error.

## <a name="composite-search"></a>Búsqueda compuesta

Para buscar recursos que cumplan varias condiciones a la vez, use la búsqueda compuesta que une una secuencia de valores de parámetro único con un símbolo `$`. El resultado devuelto sería la intersección de los recursos que coinciden con todas las condiciones especificadas por los parámetros de búsqueda unidos. Estos parámetros de búsqueda se denominan parámetros de búsqueda compuesta y definen un nuevo parámetro que combina los diferentes parámetros en una estructura anidada. Por ejemplo, si desea buscar todos los recursos `DiagnosticReport` que contienen `Observation` con un valor de potasio menor o igual que 9,2:

```rest
GET [your-fhir-server]/DiagnosticReport?result.code-value-quantity=2823-3$lt9.2

``` 

Esta solicitud especifica el componente que contiene un código de `2823-3`, que en este caso sería potasio. Después del símbolo `$`, especifica el intervalo del valor para el componente que usa `lt` para "menor o igual que" y `9.2` para el intervalo de valores de potasio. 

## <a name="search-the-next-entry-set"></a>Búsqueda en el siguiente conjunto de entradas

El número máximo de entradas que se pueden devolver por una sola consulta de búsqueda es 1000. Sin embargo, es posible que tenga más de 1000 entradas que coincidan con la consulta de búsqueda y que desee ver el siguiente conjunto de entradas después de las primeras 1000 entradas devueltas. En tal caso, usaría el valor `url` del token de continuación en `searchset` como en el resultado `Bundle` siguiente:

```json
    "resourceType": "Bundle",
    "id": "98731cb7-3a39-46f3-8a72-afe945741bd9",
    "meta": {
        "lastUpdated": "2021-04-22T09:58:16.7823171+00:00"
    },
    "type": "searchset",
    "link": [
        {
            "relation": "next",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd"
        },
        {
            "relation": "self",
            "url": "[your-fhir-server]/Patient?_sort=_lastUpdated"
        }
    ],

```

Y realizaría una solicitud GET para la dirección URL proporcionada en el campo `relation: next`:

```rest
GET [your-fhir-server]/Patient?_sort=_lastUpdated&ct=WzUxMDAxNzc1NzgzODc5MjAwODBd

```

Esto devolverá el siguiente conjunto de entradas para el resultado de la búsqueda. `searchset` es el conjunto completo de entradas de los resultados de la búsqueda, y el token de continuación `url` es el vínculo proporcionado por el servidor para que pueda recuperar las entradas que no se muestran en el primer conjunto debido a la restricción en el número máximo de entradas devueltas para una consulta de búsqueda.

## <a name="search-using-post"></a>Búsqueda mediante POST

Todos los ejemplos de búsqueda mencionados anteriormente han usado solicitudes `GET`. También puede realizar operaciones de búsqueda mediante solicitudes `POST` usando `_search`:

```rest
POST [your-fhir-server]/Patient/_search?_id=45

```

Esta solicitud devolvería todos los recursos `Patient` con el valor `id` de 45. Al igual que en las solicitudes GET, el servidor determina cuál de los recursos cumple las condiciones y devuelve un recurso agrupado en la respuesta HTTP.

Otro ejemplo de búsqueda mediante POST donde los parámetros de consulta se envían como cuerpo del formulario es:

```rest
POST [your-fhir-server]/Patient/_search
content-type: application/x-www-form-urlencoded

name=John

```
## <a name="next-steps"></a>Pasos siguientes

>[!div class="nextstepaction"]
>[Introducción a la búsqueda de FHIR](overview-of-search.md)