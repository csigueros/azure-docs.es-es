---
title: Información general de la búsqueda en Azure API for FHIR
description: En este artículo se describe una introducción a la búsqueda de FHIR que se implementa en Azure API for FHIR
author: ginalee-dotcom
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 5/17/2021
ms.author: cavoeg
ms.openlocfilehash: af0b40ccd33829803978aa5c42f1dcb4c0fcc4c2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780779"
---
# <a name="overview-of-search-in-azure-api-for-fhir"></a>Información general de la búsqueda en Azure API for FHIR

En la especificación FHIR se definen los aspectos básicos de la búsqueda de recursos de FHIR. Este artículo le guiará por algunos aspectos clave de la búsqueda de recursos en FHIR. Para obtener detalles completos sobre cómo buscar recursos de FHIR, vea [Búsqueda](https://www.hl7.org/fhir/search.html) en la especificación de HL7 FHIR. A lo largo de este artículo, se proporcionarán ejemplos de sintaxis de búsqueda. Cada búsqueda se realizará en el servidor de FHIR, que normalmente tiene una dirección URL de `https://<FHIRSERVERNAME>.azurewebsites.net`. En los ejemplos, se usará el marcador de posición {{FHIR_URL}} para esta dirección URL. 

Las búsquedas de FHIR se pueden realizar sobre un tipo de recurso concreto, un [compartimiento](https://www.hl7.org/fhir/compartmentdefinition.html) especificado o todos los recursos. La manera más sencilla de ejecutar una búsqueda en FHIR consiste en usar una solicitud `GET`. Por ejemplo, si quiere extraer todos los pacientes de la base de datos, podría usar la solicitud siguiente: 

```rest
GET {{FHIR_URL}}/Patient
```

También puede buscar mediante `POST`, lo que resulta útil si la cadena de consulta es demasiado larga. Para realizar búsquedas con `POST`, los parámetros de búsqueda se pueden enviar como el cuerpo de un formulario. Esto permite series más largas y complejas de parámetros de consulta que pueden ser difíciles de ver y comprender en una cadena de consulta.

Si la solicitud de búsqueda se realiza correctamente, recibirá una respuesta de agrupación de FHIR con el tipo `searchset`. Si se produce un error en la búsqueda, encontrará los detalles del error en `OperationOutcome` para ayudarle a entender por qué se ha producido.

En las secciones siguientes, se describirán los distintos aspectos implicados en la búsqueda. Una vez que haya revisado estos detalles, consulte nuestra [página de ejemplos](search-samples.md) que contiene ejemplos de búsquedas que puede realizar en Azure API for FHIR.

## <a name="search-parameters"></a>Parámetros de búsqueda

Cuando realice una búsqueda, lo hará en función de varios atributos del recurso. Estos atributos se denominan parámetros de búsqueda. Cada recurso tiene un conjunto de parámetros de búsqueda definidos. El parámetro de búsqueda se debe definir e indexar en la base de datos para que pueda buscarlo correctamente.

Cada parámetro de búsqueda tiene un [tipo de datos](https://www.hl7.org/fhir/search.html#ptypes) definido. A continuación se describe la compatibilidad con los distintos tipos de datos:


| **Tipo de parámetro de búsqueda**  | **Azure API for FHIR** | **Servicio FHIR en Azure Healthcare APIs** | **Comment**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  number                    | Sí                  | Sí                       |
|  date                      | Sí                  | Sí                       |
|  string                    | Sí                  | Sí                       |
|  token                     | Sí                  | Sí                       |
|  reference                 | Sí                  | Sí                       |
|  compuestos                 | Parcial              | Parcial                   | La lista de tipos compuestos admitidos se describe más adelante en este artículo. |
|  quantity                  | Sí                  | Sí                       |
|  uri                       | Sí                  | Sí                       |
|  especial                   | No                   | No                        |

### <a name="common-search-parameters"></a>Parámetros de búsqueda comunes

Hay [parámetros de búsqueda comunes](https://www.hl7.org/fhir/search.html#all) que se aplican a todos los recursos. Se enumeran a continuación, junto con su compatibilidad dentro de Azure API for FHIR:

| **Parámetro de búsqueda común** | **Azure API for FHIR** | **Servicio FHIR en Azure Healthcare APIs** | **Comment**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _id                         | Sí                  | Sí                       
| _lastUpdated                | Sí                  | Sí                       |
| _tag                        | Sí                  | Sí                       |
| _type                       | Sí                  | Sí                       |
| _security                   | Sí                  | Sí                       |
| _profile                    | Sí                  | Sí                       |
| _has                        | Parcial              | Sí                       | La compatibilidad con _has se encuentra en MVP para Azure API for FHIR y la versión de OSS con respaldo de Cosmos DB. En la sección de encadenamiento siguiente se incluye más información. |
| _query                      | No                   | No                        |
| _filter                     | No                   | No                        |
| _list                       | No                   | No                        |
| _text                       | No                   | No                        |
| _content                    | No                   | No                        |

### <a name="resource-specific-parameters"></a>Parámetros específicos del recurso

Con Azure API for FHIR, se admiten prácticamente todos los [parámetros de búsqueda específicos del recurso](https://www.hl7.org/fhir/searchparameter-registry.html) definidos por la especificación FHIR. Los únicos parámetros de búsqueda que no se admiten están disponibles en los vínculos siguientes:

* [Parámetros de búsqueda no admitidos en STU3](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/Stu3/unsupported-search-parameters.json)

* [Parámetros de búsqueda no admitidos en R4](https://github.com/microsoft/fhir-server/blob/main/src/Microsoft.Health.Fhir.Core/Data/R4/unsupported-search-parameters.json)

También puede ver la compatibilidad actual con los parámetros de búsqueda en [FHIR Capability Statement](https://www.hl7.org/fhir/capabilitystatement.html) con la siguiente solicitud:

```rest
GET {{FHIR_URL}}/metadata
```

A fin de ver los parámetros de búsqueda en la declaración de funcionalidad, vaya a `CapabilityStatement.rest.resource.searchParam` para ver los parámetros de búsqueda de cada recurso y a `CapabilityStatement.rest.searchParam` para buscar los parámetros de búsqueda de todos los recursos.

> [!NOTE]
> En Azure API for FHIR no se crean ni indexan automáticamente los parámetros de búsqueda que no estén definidos por la especificación de FHIR. Pero se proporciona compatibilidad para que defina [parámetros de búsqueda](how-to-do-custom-search.md) propios.

### <a name="composite-search-parameters"></a>Parámetros de búsqueda compuestos
La búsqueda compuesta permite buscar en pares de valores. Por ejemplo, si tuviera que buscar una observación de altura en la que la persona medía 152,4 cm, querría asegurarse de que un solo componente de la observación contiene el código de altura **y** el valor de 152,4. No le gustaría obtener una observación en la que se almacenase un peso de 152,4 y una altura de 121,92, aunque la observación tuviera entradas que se calificaran para el valor 152,4 y el código de altura, pero en secciones de componentes diferentes. 

Con Azure API for FHIR, se admiten los siguientes emparejamientos de tipo de parámetro de búsqueda:

* Referencia, Token
* Token, Fecha
* Token, Número, Número
* Token, Cantidad
* Token, Cadena
* Token, Token

Para obtener más información, vea los [parámetros de búsqueda compuesta](https://www.hl7.org/fhir/search.html#composite) de HL7. 

> [!NOTE]
> Los parámetros de búsqueda compuestos no admiten modificadores según la especificación de FHIR.

 ### <a name="modifiers--prefixes"></a>Modificadores y prefijos

Los [modificadores](https://www.hl7.org/fhir/search.html#modifiers) permiten alterar el parámetro de búsqueda. A continuación se muestra una introducción de todos los modificadores de FHIR y la compatibilidad en Azure API for FHIR. 

| **Modificadores** | **Azure API for FHIR** | **Servicio FHIR en Azure Healthcare APIs** | **Comment**|
| -------------------------  | -------------------- | ------------------------- | ------------|
|  :missing     | Sí                  | Sí                       |
|  :exact       | Sí                  | Sí                       |
|  :contains    | Sí                  | Sí                       |
|  :text        | Sí                  | Sí                       |
|  :type (reference) | Sí             | Sí                       |
|  :not         | Sí                  | Sí                       |
|  :below (uri) | Sí                  | Sí                       |
|  :above (uri) | Sí                  | Sí                       |
|  :in (token)  | No                   | No                        |
|  :below (token) | No                 | No                        |
|  :above (token) | No                 | No                        |
|  :not-in (token) | No                | No                        |

Para los parámetros de búsqueda que tienen un orden concreto (números, fechas y cantidades), puede usar un [prefijo](https://www.hl7.org/fhir/search.html#prefix) en el parámetro a fin de facilitar la búsqueda de coincidencias. Azure API for FHIR admite todos los prefijos.

 ### <a name="search-result-parameters"></a>Parámetros de resultados de la búsqueda
Para facilitar la administración de los recursos devueltos, puede usar parámetros de resultado de la búsqueda en la búsqueda. Para obtener más información sobre cómo usar cada uno de los parámetros de resultado de la búsqueda, consulte el sitio web de [HL7](https://www.hl7.org/fhir/search.html#return). 

| **Parámetros de resultados de la búsqueda**  | **Azure API for FHIR** | **Servicio FHIR en Azure Healthcare APIs** | **Comment**|
| -------------------------  | -------------------- | ------------------------- | ------------|
| _elements                     | Sí                  | Sí                       |
| _count                        | Sí                  | Sí                       | _count se limita a 1000 recursos. Si se establece en un valor superior a 1000, solo se devolverán 1000 y una advertencia en el conjunto.                               |
| _include                      | Sí                  | Sí                       | Los elementos incluidos se limitan a 100. _include en PaaS y OSS en Cosmos DB no incluyen compatibilidad con :iterate [(nº 2137)](https://github.com/microsoft/fhir-server/issues/2137).                               |
| _revinclude                   | Sí                  | Sí                       |Los elementos incluidos se limitan a 100. _revinclude en PaaS y OSS en Cosmos DB no incluyen compatibilidad con :iterate [(nº 2137)](https://github.com/microsoft/fhir-server/issues/2137).  También hay un código de estado incorrecto para una solicitud incorrecta [nº 1319](https://github.com/microsoft/fhir-server/issues/1319)                            |
| _summary                      | Sí             | Sí                   |
| _total                        | Parcial              | Parcial                   | _total=none y _total=accurate                               |
| _sort                         | Parcial              | Parcial                   | Se admite sort=_lastUpdated. Para Azure API for FHIR y las bases de datos de OSS Cosmos DB creadas después del 20 de abril de 2021, también se admite la ordenación por nombre, apellido y fecha clínica. El servicio FHIR y la base de datos OSS SQL DB admiten la ordenación por cadenas y fechas.                              |
| _contained                    | No                   | No                        |
| _containedType                | No                   | No                        |
| _score                        | No                   | No                        |

De manera predeterminada, Azure API for FHIR se establece en control sensible. Esto significa que el servidor omitirá los parámetros desconocidos o no admitidos. Si quiere usar un control estricto, puede usar el encabezado **Prefer** y establecer `handling=strict`.

 ## <a name="chained--reverse-chained-searching"></a>Búsqueda encadenada y búsqueda encadenada inversa

Una [búsqueda encadenada](https://www.hl7.org/fhir/search.html#chaining) permite buscar mediante un parámetro de búsqueda en un recurso al que hace referencia otro recurso. Por ejemplo, si quiere encontrar instancias donde el nombre del paciente sea Jane, use lo siguiente:

`GET {{FHIR_URL}}/Encounter?subject:Patient.name=Jane`

De forma similar, puede realizar una búsqueda encadenada inversa. Esto le permite obtener recursos donde se especifican criterios en otros recursos que hacen referencia a ellos. Para obtener más ejemplos de búsqueda encadenada e inversa, consulte la página de [ejemplos de búsqueda de FHIR](search-samples.md). 

> [!NOTE]
> En Azure API for FHIR y el código abierto con respaldo de Cosmos DB, hay una limitación por la que cada subconsulta necesaria para las búsquedas encadenadas y encadenadas inversas solo devolverá 100 elementos. Si se encuentran más de 100 elementos, recibirá el siguiente mensaje de error: "Las subconsultas en una expresión encadenada no pueden devolver más de 100 resultados, use un criterio más selectivo". Para obtener una consulta correcta, debe ser más específico en lo que busca.

## <a name="pagination"></a>Paginación

Como se ha mencionado antes, los resultados de una búsqueda serán una agrupación paginada. De forma predeterminada, la búsqueda devolverá 10 resultados por página, pero esto se puede aumentar (o disminuir) si se especifica `_count`. Dentro de la agrupación, habrá un vínculo propio que contiene el resultado actual de la búsqueda. Si hay coincidencias adicionales, la agrupación contendrá un vínculo siguiente. Puede seguir usando el vínculo siguiente para obtener las páginas de resultados posteriores. `_count` está limitado a 1000 elementos o menos. 

Actualmente, Azure API for FHIR solo admite el vínculo siguiente en las agrupaciones y no admite los vínculos primero, último o anterior.

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido los conceptos básicos de la búsqueda, vea la página de ejemplos de búsqueda para obtener más información sobre cómo realizar búsquedas con diferentes parámetros de búsqueda, modificadores y otros escenarios de búsqueda de FHIR.

>[!div class="nextstepaction"]
>[Ejemplos de búsqueda de FHIR](search-samples.md)
