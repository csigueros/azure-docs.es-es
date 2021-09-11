---
title: Cómo realizar búsquedas personalizadas en Azure API for FHIR
description: En este artículo se describe cómo definir sus propios parámetros de búsqueda personalizados para usarlos en la base de datos.
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 05/03/2021
ms.author: cavoeg
ms.openlocfilehash: 1fd19057d71a4ef6ac7f199d9fd133285ffeee18
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781207"
---
# <a name="defining-custom-search-parameters-for-azure-api-for-fhir"></a>Definición de parámetros de búsqueda personalizados para Azure API for FHIR

La especificación FHIR define un conjunto de parámetros de búsqueda para todos los recursos y parámetros de búsqueda que son específicos de recursos. Sin embargo, hay escenarios en los que es posible que desee buscar un elemento de un recurso que no esté definido mediante la especificación FHIR como parámetro de búsqueda estándar. En este artículo se describe cómo definir sus propios [parámetros de búsqueda](https://www.hl7.org/fhir/searchparameter.html) para usarlos en Azure API for FHIR.

> [!NOTE]
> Cada vez que cree, actualice o elimine un parámetro de búsqueda, deberá ejecutar un [trabajo de reindexación](how-to-run-a-reindex.md) para permitir que dicho parámetro se utilice en producción. A continuación se describe cómo puede probar parámetros de búsqueda antes de volver a indexar todo el servidor FHIR.

## <a name="create-new-search-parameter"></a>Creación de un parámetro de búsqueda

Para crear un parámetro de búsqueda, aplica `POST` al recurso `SearchParameter` en la base de datos. En el siguiente código de ejemplo se muestra cómo agregar el [parámetro de búsqueda de US Core Race](http://hl7.org/fhir/us/core/STU3.1.1/SearchParameter-us-core-race.html) al recurso `Patient`.

```rest
POST {{FHIR_URL}}/SearchParameter

{
  "resourceType" : "SearchParameter",
  "id" : "us-core-race",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "Returns patients with a race extension matching the specified code.",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

``` 

> [!NOTE]
> El nuevo parámetro de búsqueda aparecerá en la instrucción de funcionalidad del servidor FHIR una vez que aplique POST al parámetro de búsqueda en la base de datos **y** vuelva a indexarla. Ver el objeto `SearchParameter` en la instrucción de funcionalidad es la única manera de saber si se admite un parámetro de búsqueda en su servidor FHIR. Si encuentra el parámetro de búsqueda buscándolo, pero no lo ve en la instrucción de funcionalidad, deberá indexar el parámetro de todos modos. Puede aplicar POST a varios parámetros de búsqueda antes de desencadenar una operación de reindexación.

Elementos importantes de un objeto `SearchParameter`:

* **url:** clave única para describir el parámetro de búsqueda. Muchas organizaciones, como HL7, usan un formato de dirección URL estándar para los parámetros de búsqueda que definen, como se muestra anteriormente en el parámetro de búsqueda de US Core Race.

* **code:** el valor almacenado en **code** es lo que usará al realizar la búsqueda. En el ejemplo anterior, usaría `GET {FHIR_URL}/Patient?race=<code>` en la búsqueda para obtener todos los pacientes de una raza específica. El código debe ser único para los recursos a los que se aplica el parámetro de búsqueda.

* **base:** describe los recursos a los que se aplica el parámetro de búsqueda. Si el parámetro de búsqueda se aplica a todos los recursos, puede usar `Resource`; de lo contrario, puede enumerar todos los recursos pertinentes.
 
* **type:** describe el tipo de datos del parámetro de búsqueda. El tipo está limitado por la compatibilidad con Azure API for FHIR. Esto significa que no se puede definir un parámetro de búsqueda de tipo Especial ni definir un [parámetro de búsqueda compuesto](overview-of-search.md), a menos que sea una combinación admitida.

* **expression:** describe cómo calcular el valor de la búsqueda. Al describir un parámetro de búsqueda, debe incluir la expresión, aunque la especificación no lo requiera. El motivo es que necesita la expresión o la sintaxis xpath, y Azure API for FHIR ignora dicha sintaxis.

## <a name="test-search-parameters"></a>Prueba de los parámetros de búsqueda

Aunque no puede usar los parámetros de búsqueda en producción hasta que ejecute un trabajo de reindexación, hay varias maneras de probarlos antes de volver a indexar toda la base de datos. 

En primer lugar, puede probar el nuevo parámetro de búsqueda para ver qué valores se devolverán. Ejecutando el siguiente comando en una instancia de recurso específica (introduciendo su Id.), se obtiene una lista de pares de valores con el nombre del parámetro de búsqueda y el valor almacenado para el paciente específico. La lista incluirá todos los parámetros de búsqueda del recurso, y podrá desplazarse por ella para buscar el parámetro que ha creado. La ejecución de este comando no cambiará ningún comportamiento del servidor FHIR. 

```rest
GET https://{{FHIR_URL}}/{{RESOURCE}}/{{RESOUCE_ID}}/$reindex

```
Por ejemplo, para buscar todos los parámetros de búsqueda de un paciente, ejecute lo siguiente:

```rest
GET https://{{FHIR_URL}}/Patient/{{PATIENT_ID}}/$reindex

```

El resultado tendrá este aspecto:

```json
{
  "resourceType": "Parameters",
  "id": "8be24e78-b333-49da-a861-523491c3437a",
  "meta": {
    "versionId": "1"
  },
  "parameter": [
    {
      "name": "deceased",
      "valueString": "http://hl7.org/fhir/special-values|false"
    },
    {
      "name": "language",
      "valueString": "urn:ietf:bcp:47|en-US"
    },
    {
      "name": "race",
      "valueString": "2028-9"
    },
...
```
Una vez que vea que el parámetro de búsqueda se muestre según lo previsto, puede volver a indexar un único recurso para probar la búsqueda con el elemento. En primer lugar, va a volver a indexar un único recurso:

```rest
POST https://{{FHIR_URL}/{{RESOURCE}}/{{RESOURCE_ID}}/$reindex
```

Al ejecutar este comando, se establecen los índices de los parámetros de búsqueda para el recurso específico que haya definido para dicho tipo de recurso. Esta acción actualiza el servidor FHIR. Ya puede buscar el encabezado "use-partial-indices" y establecerlo en "true", lo que significa que devolverá resultados en los que cualquiera de los recursos tenga indexado el parámetro de búsqueda, aunque no todos los recursos de ese tipo lo tengan. 

Siguiendo con el ejemplo anterior, podría indexar un paciente para habilitar el objeto `SearchParameter` de US Core Race:

```rest
POST https://{{FHIR_URL}/Patient/{{PATIENT_ID}}/$reindex
```

A continuación, busque pacientes que tengan una raza específica:

```rest
GET https://{{FHIR_URL}}/Patient?race=2028-9
x-ms-use-partial-indices: true
```

Una vez que haya probado el parámetro de búsqueda y le satisfaga su funcionamiento, ejecute o programe el trabajo de reindexación, de modo que los parámetros de búsqueda se puedan usar en el servidor FHIR para los casos de uso de producción.

## <a name="update-a-search-parameter"></a>Actualización de un parámetro de búsqueda

Para actualizar un parámetro de búsqueda, use `PUT` para crear una versión del parámetro. Debe incluir el objeto `SearchParameter ID` en el elemento `id` del cuerpo de la solicitud `PUT` y en la llamada `PUT`.

> [!NOTE]
> Si no conoce el Id. del parámetro de búsqueda, puede buscarlo. El uso de `GET {{FHIR_URL}}/SearchParameter` devolverá todos los parámetros de búsqueda personalizados. Podrá desplazarse por la lista para encontrar el parámetro que necesita. También puede limitar la búsqueda por nombre. Con el ejemplo siguiente, podría buscar el nombre usando `USCoreRace: GET {{FHIR_URL}}/SearchParameter?name=USCoreRace`.

```rest
PUT {{FHIR_ULR}}/SearchParameter/{SearchParameter ID}

{
  "resourceType" : "SearchParameter",
  "id" : "SearchParameter ID",
  "url" : "http://hl7.org/fhir/us/core/SearchParameter/us-core-race",
  "version" : "3.1.1",
  "name" : "USCoreRace",
  "status" : "active",
  "date" : "2019-05-21",
  "publisher" : "US Realm Steering Committee",
  "contact" : [
    {
      "telecom" : [
        {
          "system" : "other",
          "value" : "http://www.healthit.gov/"
        }
      ]
    }
  ],
  "description" : "New Description!",
  "jurisdiction" : [
    {
      "coding" : [
        {
          "system" : "urn:iso:std:iso:3166",
          "code" : "US",
          "display" : "United States of America"
        }
      ]
    }
  ],
  "code" : "race",
  "base" : [
    "Patient"
  ],
  "type" : "token",
  "expression" : "Patient.extension.where(url = 'http://hl7.org/fhir/us/core/StructureDefinition/us-core-race').extension.value.code"
}

```

El resultado será un objeto `SearchParameter` actualizado; además, la versión se incrementará.

> [!Warning]
> Tenga cuidado al actualizar parámetros de búsqueda que ya se hayan indexado en su base de datos. El cambio del comportamiento existente de un parámetro podría incidir en el comportamiento esperado. Se recomienda ejecutar un trabajo de reindexación inmediatamente.

## <a name="delete-a-search-parameter"></a>Eliminación de un parámetro de búsqueda

Si necesita eliminar un parámetro de búsqueda, use lo siguiente:

```rest
Delete {{FHIR_URL}}/SearchParameter/{SearchParameter ID}
```

> [!Warning]
> Tenga cuidado al eliminar parámetros de búsqueda que ya se hayan indexado en su base de datos. El cambio del comportamiento existente de un parámetro podría incidir en el comportamiento esperado. Se recomienda ejecutar un trabajo de reindexación inmediatamente.

## <a name="next-steps"></a>Pasos siguientes

En este artículo ha obtenido información sobre cómo crear un parámetro de búsqueda. A continuación, puede obtener información sobre cómo volver a indexar el servidor FHIR.

>[!div class="nextstepaction"]
>[Procedimientos para ejecutar un trabajo de reindexación](how-to-run-a-reindex.md)