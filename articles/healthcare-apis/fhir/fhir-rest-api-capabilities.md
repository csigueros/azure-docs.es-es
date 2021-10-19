---
title: Funcionalidades de la API rest de FHIR para el servicio FHIR de las API de Azure Healthcare
description: En este artículo se describen las interacciones y funcionalidades de RESTful para el servicio FHIR de Azure Healthcare APIs.
author: stevewohl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 10/06/2021
ms.author: cavoeg
ms.openlocfilehash: bca8eb3714a685c9477f512a38406cb77f1c6886
ms.sourcegitcommit: 1d56a3ff255f1f72c6315a0588422842dbcbe502
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/06/2021
ms.locfileid: "130187739"
---
# <a name="fhir-rest-api-capabilities-for-azure-healthcare-apis-fhir-service"></a>Funcionalidades de la API rest de FHIR para el servicio FHIR de las API de Azure Healthcare

En este artículo, se tratarán algunos de los matices de las interacciones de RESTful del servicio FHIR de Azure Healthcare APIs (aquí denominado servicio FHIR).


## <a name="conditional-createupdate"></a>Creación o actualización condicionales

El servicio FHIR admite la creación, la creación condicional, la actualización y la actualización condicional, tal como se define en la especificación de FHIR. Un encabezado útil en estos escenarios es [el encabezado If-Match.](https://www.hl7.org/fhir/http.html#concurrency) El `If-Match` encabezado se usa y validará la versión que se está actualizando antes de realizar la actualización. Si no coincide con el esperado, producirá el mensaje de `ETag` `ETag` error *412 Error de condición previa.* 

## <a name="delete"></a>Eliminar

[La](https://www.hl7.org/fhir/http.html#delete) eliminación definida por la especificación de FHIR requiere que, después de eliminar, las lecturas posteriores no específicas de la versión de un recurso devuelvan un código de estado HTTP 410 y el recurso ya no se encuentra mediante la búsqueda. Además, el servicio FHIR le permite eliminar completamente (incluido todo el historial) del recurso. Para eliminar completamente el recurso, puede pasar una configuración de parámetros `hardDelete` a true (`DELETE {server}/{resource}/{id}?hardDelete=true`). Si no pasa este parámetro o se establece en false, las versiones históricas del recurso `hardDelete` seguirán estando disponibles.

> [!NOTE]
> Si solo desea eliminar el historial, el servicio FHIR admite una operación personalizada, , que le permite eliminar el historial `$purge-history` de un recurso. 

## <a name="conditional-delete"></a>Eliminación condicional

Además de eliminar, el servicio FHIR admite la eliminación condicional, que permite pasar un criterio de búsqueda para eliminar un recurso. De forma predeterminada, la eliminación condicional permite eliminar un elemento a la vez. También puede especificar el parámetro `_count` para eliminar hasta 100 elementos a la vez. A continuación se muestran algunos ejemplos del uso de la eliminación condicional.

Para eliminar un solo elemento mediante la eliminación condicional, debe especificar criterios de búsqueda que devuelvan un solo elemento.

DELETE `https://{{hostname}}/Patient?identifier=1032704`

Puede realizar la misma búsqueda, pero incluir hardDelete=true para eliminar también todo el historial.

DELETE `https://{{hostname}}/Patient?identifier=1032704&hardDelete=true`

Si desea eliminar varios recursos, puede incluir , que eliminará hasta 100 recursos que coincidan `_count=100` con los criterios de búsqueda.

DELETE `https://{{hostname}}/Patient?identifier=1032704&_count=100`

## <a name="patch-and-conditional-patch"></a>Revisión y revisión condicional

Patch es una valiosa operación RESTful cuando solo necesita actualizar una parte del recurso de FHIR. El uso de Patch permite especificar los elementos que desea actualizar en el recurso sin tener que actualizar todo el registro. FHIR define tres tipos de maneras de aplicar revisiones a los recursos en FHIR: revisión JSON, revisión XML y revisión de ruta de acceso de FHIR. El servicio FHIR admite la revisión JSON y la revisión JSON condicional (que permite aplicar revisiones a un recurso en función de un criterio de búsqueda en lugar de un identificador). Para ver algunos ejemplos del uso de la revisión JSON, consulte el archivo [REST de ejemplo](https://github.com/microsoft/fhir-server/blob/main/docs/rest/PatchRequests.http).

> [!NOTE]
> Cuando se usa en LA 3, y si solicita un conjunto de historial, el recurso con revisión `PATCH` `Bundle.entry.request.method` se asigna a `PUT` . Esto se debe a que LA3 no contiene una definición para el `PATCH` verbo en el conjunto de valores [HTTPVerb](http://hl7.org/fhir/STU3/valueset-http-verb.html).

### <a name="testing-patch"></a>Revisión de pruebas

En Revisión, hay una operación de prueba que le permite validar que una condición es verdadera antes de realizar la revisión. Por ejemplo, si desea establecer a un paciente como paciente con tratamiento (solo si aún no están marcados como desaparecidos), puede usar el ejemplo siguiente: 

PATCH `http://{FHIR-SERVICE-NAME}/Patient/{PatientID}` Content-type: `application/json-patch+json`

```
[
    {
        "op": "test",
        "path": "/deceasedBoolean",
        "value": false
    },
    {
        "op": "replace",
        "path": "/deceasedBoolean",
        "value": true
    }
]

```

### <a name="patch-in-bundles"></a>Revisión en lotes

De forma predeterminada, json patch no se admite en los recursos de agrupación. Esto se debe a que un paquete solo admite con recursos de FHIR y la revisión JSON no es un recurso de FHIR. Para evitar esto, trataremos los recursos binarios con un tipo de contenido de como codificación base64 de cadena JSON cuando se ejecuta `"application/json-patch+json"` una agrupación. Para obtener información sobre esta solución alternativa, inicie sesión en [Zulip](https://chat.fhir.org/#narrow/stream/179166-implementers/topic/Transaction.20with.20PATCH.20request). 

En el ejemplo siguiente, queremos cambiar el sexo de la paciente a mujer. Hemos tomado la revisión JSON `[{"op":"replace","path":"/gender","value":"female"}]` y la hemos codificado en base64.

POST `https://{FHIR-SERVICE-NAME}/` content-type: `application/json`

```
{
    "resourceType": "Bundle",
    "id": "bundle-batch",
    "type": "batch",
    "entry": [
        {
            "fullUrl": "Patient/{PatientID}",
            "resource": {
                "resourceType": "Binary",
                "contentType": "application/json-patch+json",
                "data": "W3sib3AiOiJyZXBsYWNlIiwicGF0aCI6Ii9nZW5kZXIiLCJ2YWx1ZSI6ImZlbWFsZSJ9XQ=="
            },
            "request": { 
                "method": "PATCH",
                "url": "Patient/{PatientID}"
            }
        }
    ]
}

```

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido algunas de las funcionalidades rest del servicio FHIR. A continuación, puede obtener más información sobre los aspectos clave de la búsqueda de recursos en el servicio FHIR. 

>[!div class="nextstepaction"]
>[Introducción a la búsqueda de FHIR](overview-of-search.md)

(FHIR&#174;) es una marca registrada [de HL7](https://hl7.org/fhir/) y se usa con el permiso HL7.


