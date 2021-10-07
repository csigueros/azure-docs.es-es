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
ms.date: 09/24/2021
ms.author: danlep
ms.openlocfilehash: 3f16961ec4774708fa55a2a49e408a6b980cdb31
ms.sourcegitcommit: 48500a6a9002b48ed94c65e9598f049f3d6db60c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2021
ms.locfileid: "129057613"
---
# <a name="api-import-restrictions-and-known-issues"></a>Restricciones de importación de API y problemas conocidos

## <a name="about-this-list"></a>Acerca de esta lista

Al importar una API, podría encontrarse con algunas restricciones o tener que identificar y corregir problemas antes de realizar esta operación correctamente. En este artículo, aprenderá lo siguiente:
* Todas las limitaciones, organizadas según el formato de importación de la API. 
* El funcionamiento de la exportación de OpenAPI.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Limitaciones de la importación de OpenAPI/Swagger

Si recibe errores al importar el documento de OpenAPI, asegúrese de haberlo validado con antelación. Para ello:
* Use el diseñador de Azure Portal (Diseño > Front-end > Editor de especificaciones OpenAPI) o 
* Una herramienta de terceros como <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="general"></a><a name="open-api-general"> </a>General

- Los parámetros necesarios en la ruta de acceso y en la consulta deben tener nombres únicos.
    - En OpenAPI, un nombre de parámetro solo debe ser único dentro de una ubicación; por ejemplo, ruta de acceso, consulta, encabezado. 
    - En API Management se permite que los parámetros tanto de la ruta de acceso como de la consulta discriminen las operaciones. 
        - Como OpenAPI no admite esto, es necesario que los nombres de parámetro sean únicos en toda la plantilla de URL.
- Cuando se importa en línea a API Management, una especificación de OpenAPI puede tener un tamaño de hasta 4 MB. 
    - El límite de tamaño no se aplica cuando se proporciona un documento de OpenAPI a través de una dirección URL a una ubicación a la que se puede acceder desde el servicio API Management.
- `\$ref`Los punteros no pueden hacer referencia a archivos externos.
- Las únicas extensiones que se admiten son:
    - `x-ms-paths` 
    - `x-servers` 
- Extensiones personalizadas:
    - Se omiten en la importación.
    - No se guardan ni se conservan para la exportación.
- Se omiten las definiciones de seguridad.
- `Recursion`: 
    - API Management no admite las definiciones establecidas de forma recursiva.
    - Por ejemplo, esquemas que hacen referencia a sí mismos.
- El objeto `server` no se admite en el nivel de operación de API.
- No se admite la palabra clave `Produces` (que describe los tipos MIME devueltos por una API). 
- La dirección URL disponible del archivo de origen se aplica a direcciones URL del servidor relativas.
- No se admiten las definiciones de esquemas alineadas para las operaciones de API. Definiciones de esquema:
    - Se definen en el ámbito de la API.
    - Se puede hacer referencia a estas en los ámbitos de solicitud o respuesta de las operaciones de API.
- Un parámetro de dirección URL definido debe formar parte de la plantilla de dirección URL.

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI versión 2

La compatibilidad con OpenAPI versión 2 se limita solo al formato JSON.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI versión 3

-   Si se especifican varios `servers`, API Management usará la primera dirección URL HTTPS que encuentre. 
- Si no hay ninguna, la dirección URL del servidor estará vacía.
- `Examples` no se admite, pero `example` está.
- Los campos siguientes se incluyen en la versión 3.x de OpenAPI, pero no se admiten:
    - `explode`
    - `style`
    - `allowReserved`

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI mecanismos de importación, actualización y exportación

### <a name="general"></a><a name="open-import-export-general"> </a>General

Las definiciones de API que se exportan desde un servicio API Management:
* Están pensadas principalmente para aplicaciones externas que tienen que llamar a la API hospedada en el servicio API Management. 
* No están destinadas a importarse en este u otro servicio de API Management. 

Para más información sobre la administración de la configuración de las definiciones de API en los diferentes servicios o entornos, consulte la documentación relativa al uso del servicio API Management con Git. 

### <a name="add-new-api-via-openapi-import"></a>Incorporación de una nueva API a través de OpenAPI Import

Para cada operación encontrada en el documento OpenAPI, se creará una nueva operación con el nombre de recurso de Azure y el nombre para mostrar establecidos en `operationId` y `summary` respectivamente. 
* El valor `operationId` se normaliza.
    *  Si no se especifica `operationId` (no está presente, es `null`, o está vacío), el valor del nombre del recurso de Azure se generará combinando el método HTTP y la plantilla de ruta de acceso, por ejemplo, `get-foo`.
* `summary` el valor se importa tal cual y su longitud está limitada a 300 caracteres.
    * Si no se especifica `summary` (no está presente, es `null` o está vacío), el valor `display name` se establecerá en `operationId`. 

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

Cada:
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

- **Enlaces SOAP**  
    - Solo se admiten enlaces SOAP de "documento" y estilo de codificación "literal".
    - No se admiten las codificaciones de estilo "rpc" o SOAP.
- **WSDL:Import**
    - No compatible. En su lugar, combine las importaciones en un único documento.
- **Mensajes con varias partes** 
    - No se admite este tipo de mensaje.
- **WCF wsHttpBinding** 
    - Los servicios SOAP creados con Windows Communication Foundation deben usar `basicHttpBinding`.
    - `wsHttpBinding` no se admite.
- **MTOM** 
    - Los servicios que usan `MTOM` *pueden* funcionar. 
    - No se ofrece soporte técnico oficial en este momento.
- **Recursividad** 
    - Los tipos definidos de forma recursiva no son compatibles con API Management.
    - Por ejemplo, una referencia a una matriz de ellos mismos.
- **Varios espacios de nombres** 
    - Aunque en un esquema se pueden usar varios espacios de nombres, el único que se puede usar para definir las partes del mensaje es el de destino. 
    - No se conservarán los espacios de nombres que no sean los de destino. 
        - Estos espacios de nombres se usan para definir otros elementos de entrada o salida.
        - Aunque se puede importar un documento WSDL, en la exportación todas las partes del mensaje tendrán el espacio de nombres de destino de WSDL.
- **Matrices** 
    - La transformación de SOAP a REST admite solo las matrices encapsuladas que se muestran en el siguiente ejemplo:

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
