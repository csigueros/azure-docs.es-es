---
title: Restricciones y detalles de los formatos de API compatibles
titleSuffix: Azure API Management
description: Los detalles de los problemas conocidos y las restricciones en los formatos de API Open, WSDL y WADL se admiten en Azure API Management.
services: api-management
documentationcenter: ''
author: dlepow
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/26/2021
ms.author: danlep
ms.openlocfilehash: 5ddb4d615dc3d1bd7c9830d11a27341baf57edb3
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131031819"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restricciones de importación de API y problemas conocidos

Al importar una API, podría encontrarse con algunas restricciones o tener que identificar y corregir problemas antes de realizar esta operación correctamente. En este artículo, aprenderá lo siguiente:
* El comportamiento de API Management durante la importación de OpenAPI. 
* Las limitaciones de importación, organizadas según el formato de importación de la API. 
* El funcionamiento de la exportación de OpenAPI.

## <a name="api-management-during-openapi-import"></a>API Management durante la importación de OpenAPI

Durante la importación de OpenAPI, API Management:
* Comprueba específicamente los parámetros de la cadena de consulta marcados como obligatorios.
* Convierte los parámetros de la cadena de consulta en parámetros de plantilla. 

Si prefiere un comportamiento diferente, puede: 
* Cambiarlo manualmente a través del editor basado en formularios o 
* Quitar el atributo "required" de la definición de OpenAPI, por lo que no los convierte en parámetros de plantilla.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Limitaciones de la importación de OpenAPI/Swagger

Si recibe errores al importar el documento de OpenAPI, asegúrese de haberlo validado con antelación. Para ello:
* Use el diseñador de Azure Portal (Diseño > Front-end > Editor de especificaciones OpenAPI) o 
* Una herramienta de terceros como <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="general"></a><a name="open-api-general"> </a>General

#### <a name="url-template-requirements"></a>Requisitos de plantilla de dirección URL

| Requisito | Descripción |
| ----------- | ----------- |
| **Nombres únicos de los parámetros de consulta y ruta de acceso necesarios** | En OpenAPI: <ul><li>Un nombre de parámetro solo debe ser único dentro de una ubicación; por ejemplo, ruta de acceso, consulta, encabezado.</li></ul>En API Management:<ul><li>Se permite que los parámetros tanto de la ruta de acceso como de la consulta discriminen las operaciones.</li><li>OpenAPI no admite esta discriminación, por lo que es necesario que los nombres de parámetro sean únicos en toda la plantilla de URL.</li></ul>  |
| **Parámetro de dirección URL definido** | Debe formar parte de la plantilla de dirección URL. |
| **Dirección URL del archivo de origen disponible** | Se aplica a las direcciones URL de servidor relativas. |
| **Punteros `\$ref`** | No pueden hacer referencia a archivos externos. |


#### <a name="openapi-specifications"></a>Especificaciones de OpenAPI

**Versiones compatibles**

API Management solo admite:
* OpenAPI versión 2.
* OpenAPI versión 3.0.x (hasta la versión 3.0.3).

La versión 3.1 de OpenAPI no se admite actualmente en API Management.

**Limitaciones de tamaño**

| Límite de tamaño | Descripción |
| ---------- | ----------- |
| **Hasta 4 MB/s** | Cuando una especificación de OpenAPI se importa en línea a API Management. |
| **No se aplica el límite de tamaño** | Cuando se proporciona un documento de OpenAPI a través de una dirección URL a una ubicación a la que se puede acceder desde el servicio API Management. |

#### <a name="supported-extensions"></a>Extensiones admitidas
Las únicas extensiones que se admiten son:

| Extensión | Descripción |
| ----------- | ----------- |
| **`x-ms-paths`** | <ul><li>Permite definir rutas de acceso diferenciadas por parámetros de consulta en la dirección URL.</li><li>Se trata en la [documentación de AutoRest](https://github.com/Azure/autorest/tree/main/docs/extensions#x-ms-paths).</li></ul> |
| **`x-servers`** | Un backport del [objeto `servers` de OpenAPI 3](https://swagger.io/docs/specification/api-host-and-base-path/) para OpenAPI 2. |

#### <a name="unsupported-extensions"></a>Extensiones no admitidas
| Extensión | Descripción |
| ----------- | ----------- |
| **`Recursion`** | API Management no admite las definiciones establecidas de forma recursiva.<br />Por ejemplo, esquemas que hacen referencia a sí mismos. |
| **Objeto `Server`** | No se admite en el nivel de operación de API. |
| **Palabra clave `Produces`** | Describe los tipos MIME devueltos por una API. <br />No se admite. |

#### <a name="custom-extensions"></a>Extensiones personalizadas
- Se omiten en la importación.
- No se guardan ni se conservan para la exportación.

#### <a name="unsupported-definitions"></a>Definiciones no admitidas 
No se admiten las definiciones de esquemas alineadas para las operaciones de API. Definiciones de esquema:
- Se definen en el ámbito de la API.
- Se puede hacer referencia a estas en los ámbitos de solicitud o respuesta de las operaciones de API.

#### <a name="ignored-definitions"></a>Definiciones omitadas
Se omiten las definiciones de seguridad.

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI versión 2

La compatibilidad con OpenAPI versión 2 se limita solo al formato JSON.

### <a name="openapi-version-30x"></a><a name="open-api-v3"> </a>OpenAPI versión 3.0.x

La versión más reciente compatible de OpenAPI versión 3.0 es la 3.0.3.

#### <a name="https-urls"></a>Direcciones URL HTTPS
- Si se especifican varios `servers`, API Management usará la primera dirección URL HTTPS que encuentre. 
- Si no hay ninguna, la dirección URL del servidor estará vacía.

#### <a name="supported"></a>Compatible
- `example`

#### <a name="unsupported"></a>No compatible
Los campos siguientes se incluyen en la [versión 3.0.x de OpenAPI](https://swagger.io/specification/), pero no se admiten:

| Object | Campo |
| ----------- | ----------- |
| **OpenAPI** | `externalDocs` |
| **Componentes** | <ul><li>`responses`</li><li>`parameters`</li><li>`examples`</li><li>`requestBodies`</li><li>`headers`</li><li>`securitySchemes`</li><li>`links`</li><li>`callbacks`</li></ul> |
| **PathItem** | <ul><li>`trace`</li><li>`servers`</li></ul> |
| **operación** | <ul><li>`externalDocs`</li><li>`callbacks`</li><li>`security`</li><li>`servers`</li></ul> |
| **Parámetro** | <ul><li>`allowEmptyValue`</li><li>`style`</li><li>`explode`</li><li>`allowReserved`</li></ul> |

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI mecanismos de importación, actualización y exportación

### <a name="general"></a><a name="open-import-export-general"> </a>General

Las definiciones de API que se exportan desde un servicio API Management:
* Están pensadas principalmente para aplicaciones externas que tienen que llamar a la API hospedada en el servicio API Management. 
* No están destinadas a importarse en este u otro servicio de API Management. 

Para más información sobre la administración de la configuración de las definiciones de API en los diferentes servicios o entornos, consulte la documentación relativa al uso del servicio API Management con Git. 

### <a name="add-new-api-via-openapi-import"></a>Incorporación de una nueva API a través de OpenAPI Import

Para cada operación que se encuentra en el documento de OpenAPI, se creará una nueva operación con:
* Nombre del recurso de Azure establecido en `operationId`.
    * El valor `operationId` se normaliza.
    *  Si no se especifica `operationId` (no está presente, es `null`, o está vacío), el valor del nombre del recurso de Azure se generará combinando el método HTTP y la plantilla de ruta de acceso.
        * Por ejemplo, `get-foo`.

* Nombre para mostrar establecido en `summary`. 
    * Valor `summary`:
        * Se importa tal y como está.
        * La longitud está limitada a 300 caracteres.
    * Si no se especifica `summary` (no está presente, es `null` o está vacío), el valor del nombre para mostrar se establecerá en `operationId`. 

### <a name="update-an-existing-api-via-openapi-import"></a>Actualización de una API existente a través de la importación de OpenAPI

Durante la importación, la operación de API existente:
* Cambia para coincidir con la API descrita en el documento de OpenAPI. 
* Coincide con una operación en el documento de OpenAPI comparando su valor `operationId` con el nombre del recurso de Azure de la operación existente. 
    * Si se encuentra una coincidencia, las propiedades de la operación existente se actualizarán "en contexto".
    * Si no se encuentra una coincidencia:
        * Se creará una nueva operación combinando el método HTTP y la plantilla de ruta de acceso, por ejemplo, `get-foo`. 
        * Para cada nueva operación, la importación intentará copiar las directivas de una operación existente con el mismo método HTTP y la misma plantilla de ruta de acceso.

Se eliminarán todas las operaciones no coincidentes existentes.

Para hacer que la importación sea más predecible, siga estas instrucciones:

- Especifique la propiedad `operationId` para cada operación.
- Absténgase de cambiar `operationId` después de la importación inicial.
- Nunca cambie `operationId` y la plantilla del método HTTP o de la ruta de acceso al mismo tiempo.

### <a name="export-api-as-openapi"></a>Exportar API como OpenAPI

Para cada operación, su:
* Nombre del recurso de Azure de la operación se exportará como `operationId`.
* El nombre para mostrar se exportará como `summary`.

**Reglas de normalización para `operationId`**
- Convertir en minúsculas.
- Reemplace cada secuencia de caracteres no alfanuméricos por un único guion.
    - Por ejemplo, `GET-/foo/{bar}?buzz={quix}` se transformará en `get-foo-bar-buzz-quix-`.
- Recorte los guiones en ambos lados.
    - Por ejemplo, `get-foo-bar-buzz-quix-` se convertirá en `get-foo-bar-buzz-quix`.
- Se trunca para ajustar 76 caracteres, cuatro caracteres menos que el límite máximo de un nombre de recurso.
- Utilice los cuatro caracteres restantes para un sufijo de desduplicación, si es necesario, con el formato `-1, -2, ..., -999`.

## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

Los archivos WSDL se usan para crear API de tránsito de SOAP y de SOAP a REST.

### <a name="soap-bindings"></a>Enlaces SOAP 
- Solo se admiten enlaces SOAP de "documento" y estilo de codificación "literal".
- No se admiten las codificaciones de estilo "rpc" o SOAP.

### <a name="wsdlimport"></a>WSDL:Import
No compatible. En su lugar, combine las importaciones en un único documento.

### <a name="messages-with-multiple-parts"></a>Mensajes con varias partes 
No se admite este tipo de mensaje.

### <a name="wcf-wshttpbinding"></a>WCF wsHttpBinding 
- Los servicios SOAP creados con Windows Communication Foundation deben usar `basicHttpBinding`.
- `wsHttpBinding` no se admite.

### <a name="mtom"></a>MTOM 
- Los servicios que usan `MTOM` *pueden* funcionar. 
- No se ofrece soporte técnico oficial en este momento.

### <a name="recursion"></a>Recursividad
- Los tipos definidos de forma recursiva no son compatibles con API Management.
- Por ejemplo, una referencia a una matriz de ellos mismos.

### <a name="multiple-namespaces"></a>Varios espacios de nombres
Aunque en un esquema se pueden usar varios espacios de nombres, el único que se puede usar para definir las partes del mensaje es el de destino. Estos espacios de nombres se usan para definir otros elementos de entrada o salida.

En la exportación no se conservarán los espacios de nombres que no sean los de destino. Aunque puede importar un documento WSDL que defina los elementos de mensaje con otros espacios de nombres, todos los elementos de mensaje tendrán el espacio de nombres de destino WSDL en la exportación.

### <a name="arrays"></a>Matrices 
La transformación de SOAP a REST admite solo las matrices encapsuladas que se muestran en el siguiente ejemplo:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

Actualmente, no hay ningún problema de importación conocido de WADL.
