---
title: Personalización de una aptitud de API web en conjuntos de aptitudes
titleSuffix: Azure Cognitive Search
description: Extienda las funcionalidades de los conjuntos de aptitudes de Azure Cognitive Search mediante un llamado a las API web. Use la aptitud API web personalizada para integrar el código personalizado.
author: LiamCavanagh
ms.author: liamca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/17/2020
ms.openlocfilehash: ffc9faa9bc6715417107e35729fc2e53f7e1fa83
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114731381"
---
# <a name="custom-web-api-skill-in-an-azure-cognitive-search-enrichment-pipeline"></a>Aptitud API web personalizada en una canalización de enriquecimiento de Azure Cognitive Search

La aptitud **API web personalizada** permite extender el enriquecimiento con IA al llamar a un punto de conexión de la API web que proporciona operaciones personalizadas. De manera similar a las aptitudes integradas, una aptitud **API web personalizada** tiene entradas y salidas. En función de las entradas, la API web recibe una carga de JSON cuando se ejecuta el indexador y genera una carga de JSON como respuesta, junto con un código de estado correcto. Se espera que la respuesta tenga las salidas especificadas por la aptitud personalizada. Cualquier otra respuesta se considera un error y no se realiza ningún enriquecimiento.

La estructura de las cargas JSON se describen con mayor detalle más adelante en este documento.

> [!NOTE]
> El indizador realizará dos reintentos para ciertos códigos de estado HTTP estándar devueltos desde la API web. Estos códigos de estado HTTP son: 
> * `502 Bad Gateway`
> * `503 Service Unavailable`
> * `429 Too Many Requests`

## <a name="odatatype"></a>@odata.type  
Microsoft.Skills.Custom.WebApiSkill

## <a name="skill-parameters"></a>Parámetros de la aptitud

Los parámetros distinguen mayúsculas de minúsculas.

| Nombre de parámetro     | Descripción |
|--------------------|-------------|
| `uri` | URI de la API web adonde se enviará la carga de _JSON_. Solo se permite el esquema de URI **https** |
| `httpMethod` | Método que se usará al enviar la carga. Los métodos permitidos son `PUT` o `POST` |
| `httpHeaders` | Colección de pares clave-valor donde las claves representan los nombres de encabezados y los valores representan los valores de encabezados que se enviarán a la API web junto con la carga. Estos encabezados no se permiten en esta colección: `Accept`, `Accept-Charset`, `Accept-Encoding`, `Content-Length`, `Content-Type`, `Cookie`, `Host`, `TE`, `Upgrade`, `Via` |
| `timeout` | (Opcional) Cuando se especifica, indica el tiempo de expiración del cliente http que hace la llamada API. Debe tener el formato de un valor "dayTimeDuration" XSD (subconjunto restringido de un valor de [duración ISO 8601](https://www.w3.org/TR/xmlschema11-2/#dayTimeDuration) ). Por ejemplo, `PT60S` para 60 segundos. Si no se establece, se elige el valor predeterminado de 30 segundos. El tiempo de expiración se puede establecer en un máximo de 230 segundos y un mínimo de 1. |
| `batchSize` | (Opcional) Indica cuántos "registros de datos" (consulte la estructura de la carga de _JSON_ que se muestra más adelante) se enviarán por llamada API. Si no se establece, se elige un valor predeterminado de 1000. Se recomienda usar este parámetro para lograr un equilibrio adecuado entre el rendimiento de la indexación y la carga en la API |
| `degreeOfParallelism` | (Opcional) Cuando se especifica, indica el número de llamadas que realizará el indexador en paralelo al punto de conexión que ha proporcionado. Puede reducir este valor si el punto de conexión no puede con una carga demasiado elevada de solicitudes o aumentarlo si el punto de conexión es capaz de aceptar más solicitudes y desea un aumento en el rendimiento del indexador.  Si no se establece, se usa un valor predeterminado de 5. `degreeOfParallelism` se puede establecer en un máximo de 10 y un mínimo de 1. |

## <a name="skill-inputs"></a>Entradas de la aptitud

No hay entradas "predefinidas" para esta aptitud. Puede elegir uno o más campos que ya estarían disponibles en el momento de la ejecución de esta aptitud como entradas y la carga de _JSON_ enviada a la API web tendrá distintos campos.

## <a name="skill-outputs"></a>Salidas de la aptitud

No hay salidas "predefinidas" para esta aptitud. En función de la respuesta que devuelva la API web, agregue campos de salida para poder seleccionarlos desde la respuesta de _JSON_.


## <a name="sample-definition"></a>Definición de ejemplo

```json
  {
        "@odata.type": "#Microsoft.Skills.Custom.WebApiSkill",
        "description": "A custom skill that can identify positions of different phrases in the source text",
        "uri": "https://contoso.count-things.com",
        "batchSize": 4,
        "context": "/document",
        "inputs": [
          {
            "name": "text",
            "source": "/document/content"
          },
          {
            "name": "language",
            "source": "/document/languageCode"
          },
          {
            "name": "phraseList",
            "source": "/document/keyphrases"
          }
        ],
        "outputs": [
          {
            "name": "hitPositions"
          }
        ]
      }
```
## <a name="sample-input-json-structure"></a>Estructura de JSON de entrada de ejemplo

Esta estructura _JSON_ representa la carga que se enviará a la API web.
Siempre seguirá estas restricciones:

* La entidad de nivel superior se llama `values` y será una matriz de objetos. El número de dichos objetos será como máximo el `batchSize`
* Cada objeto de la matriz `values` tendrá
    * Una propiedad `recordId` que será una cadena **única** usada para identificar ese registro.
    * Una propiedad `data` que será un objeto _JSON_. Los campos de la propiedad `data` corresponderán a los "nombres" especificados en la sección `inputs` de la definición de aptitud. El valor de esos campos provendrá del `source` de esos campos (que podrían ser de un campo del documento o posiblemente de otra aptitud).

```json
{
    "values": [
      {
        "recordId": "0",
        "data":
           {
             "text": "Este es un contrato en Inglés",
             "language": "es",
             "phraseList": ["Este", "Inglés"]
           }
      },
      {
        "recordId": "1",
        "data":
           {
             "text": "Hello world",
             "language": "en",
             "phraseList": ["Hi"]
           }
      },
      {
        "recordId": "2",
        "data":
           {
             "text": "Hello world, Hi world",
             "language": "en",
             "phraseList": ["world"]
           }
      },
      {
        "recordId": "3",
        "data":
           {
             "text": "Test",
             "language": "es",
             "phraseList": []
           }
      }
    ]
}
```

## <a name="sample-output-json-structure"></a>Estructura JSON de salida de ejemplo

La "salida" corresponde a la respuesta devuelta desde la API web. La API web solo debe devolver una carga de _JSON_ (que se comprueba al mirar el encabezado de la respuesta `Content-Type`) y debe cumplir con estas restricciones:

* Debe haber una entidad de nivel superior llamada `values`, que debe ser una matriz de objetos.
* El número de objetos en la matriz debe ser el mismo número de objetos enviados a la API web.
* Cada objeto debe tener:
   * Una propiedad `recordId`
   * Una propiedad `data`, que es un objeto donde los campos son enriquecimientos que coinciden con los "nombres" en `output` y cuyos valores se consideran el enriquecimiento.
   * Una propiedad `errors`, una matriz que muestra todos los errores detectados que se agregarán al historial de ejecuciones del indizador. Esta propiedad es obligatoria, pero puede tener un valor `null`.
   * Una propiedad `warnings`, una matriz que muestra todas las advertencias detectadas que se agregarán al historial de ejecuciones del indizador. Esta propiedad es obligatoria, pero puede tener un valor `null`.
* No es necesario que los objetos de la matriz `values` estén en el mismo orden que los objetos de la matriz `values` enviados como solicitud a la API web. Sin embargo, `recordId` se usa para correlación, por lo que se descartará cualquier registro de la respuesta que contenga un `recordId` que no fue parte de la solicitud original a la API web.

```json
{
    "values": [
        {
            "recordId": "3",
            "data": {
            },
            "errors": [
              {
                "message" : "'phraseList' should not be null or empty"
              }
            ],
            "warnings": null
        },
        {
            "recordId": "2",
            "data": {
                "hitPositions": [6, 16]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "0",
            "data": {
                "hitPositions": [0, 23]
            },
            "errors": null,
            "warnings": null
        },
        {
            "recordId": "1",
            "data": {
                "hitPositions": []
            },
            "errors": null,
            "warnings": {
                "message": "No occurrences of 'Hi' were found in the input text"
            }
        },
    ]
}

```

## <a name="error-cases"></a>Casos de error
Además de una API web no disponible o del envío de códigos de estado no correcto, los siguientes se consideran como casos con errores:

* Si la API web devuelve un código de estado correcto, pero la respuesta indica que no es `application/json`, la respuesta se considera como no válida y no se realizará ningún enriquecimiento.
* Si hay registros **no válidos** (con `recordId` no en la solicitud original o con valores duplicados) en la matriz `values` de la respuesta, no se realizará ningún enriquecimiento para **esos** registros.

En los casos en que la API web no está disponible o devuelve un error HTTP, se agregará un error descriptivo con todos los detalles disponibles sobre el error HTTP al historial de ejecución del indizador.

## <a name="see-also"></a>Consulte también

+ [Definición de un conjunto de aptitudes](cognitive-search-defining-skillset.md)
+ [Incorporación de una aptitud personalizada a una canalización de enriquecimiento con IA](cognitive-search-custom-skill-interface.md)
+ [Ejemplo: Creación de una aptitud personalizada para el enriquecimiento con inteligencia artificial](cognitive-search-create-custom-skill-example.md)
