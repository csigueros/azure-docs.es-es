---
title: Directiva de validación de Azure API Management para solicitudes de GraphQL | Microsoft Docs
description: Obtenga información sobre una nueva directiva que puede usar en Azure API Management para validar y autorizar solicitudes de GraphQL.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 10/21/2021
ms.author: danlep
ms.custom: ignite-fall-2021
ms.openlocfilehash: 02225e0a07ef6567b8b190d9d31a957a69adbc4f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131091725"
---
# <a name="api-management-policy-to-validate-and-authorize-graphql-requests-preview"></a>Directiva de API Management para validar y autorizar solicitudes de GraphQL (versión preliminar)

En este artículo se proporciona una referencia para una nueva directiva de API Management para validar y autorizar solicitudes a una [GraphQL API](graphql-api.md) importada a API Management.

Para obtener más información sobre cómo agregar y configurar directivas, consulte [Directivas en API Management](./api-management-policies.md).

## <a name="validation-policy"></a>Directiva de validación

| Directiva | Descripción |
| ------ | ----------- |
| [Validación de la solicitud de GraphQL](#validate-graphql-request) | Valida y autoriza una solicitud a la GraphQL API. |


## <a name="validate-graphql-request"></a>Validación de la solicitud de GraphQL

La directiva de `validate-graphql-request` valida la solicitud de GraphQL y autoriza el acceso a rutas de consulta específicas. Una consulta no válida es un "error de solicitud". La autorización solo se realiza para solicitudes válidas. 

**Permisos**  
Dado que las consultas de GraphQL usan un esquema plano:
* Los permisos se pueden aplicar en cualquier nodo hoja de un tipo de salida: 
    * Mutación, consulta o suscripción
    * Campo individual en una declaración de tipos. 
* Es posible que los permisos no se apliquen a: 
    * Tipos de entrada
    * Fragments
    * Uniones
    * Interfaces
    * El elemento de esquema   

**Elementos de autorización**  
Puede usar varios elementos de autorización. La ruta de acceso más específica se usa para seleccionar la regla de autorización adecuada para cada nodo hoja de la consulta. 
* Opcionalmente, cada autorización puede proporcionar una acción diferente.
* Las cláusulas de `if` permiten al administrador especificar acciones condicionales. 

**Sistema de introspección**   
La directiva de path=`/__*` es el sistema de [introspección](https://graphql.org/learn/introspection/). Puede usarla para rechazar solicitudes de introspección (`__schema`, `__type`, etc.).   

### <a name="policy-statement"></a>Instrucción de la directiva

```xml
<validate-graphql-request error-variable-name="variable name" max-size="size in bytes" max-depth="query depth">
    <authorize-path="query path, for example: /Query/list Users or /__*" action="allow|remove|reject" />
        <if condition="policy expression" action="allow|remove|reject" />
</validate-graphql-request>
```

### <a name="example"></a>Ejemplo

En el ejemplo siguiente, validamos una consulta de GraphQL y rechazamos:
* Solicitudes de más de 100 kb o con una profundidad de consulta superior a 4. 
* Acceso al sistema de introspección y a la consulta de `list Users`. 

```xml
<validate-graphql-request error-variable-name="name" max-size="102400" max-depth="4"> 
    <authorize path="/" action="allow" /> 
    <authorize path="/__*" action="reject" /> 
    <authorize path="/Query/list Users" action="reject" /> 
</validate-graphql-request> 
```

### <a name="elements"></a>Elementos

| Nombre         | Descripción                                                                                                                                   | Obligatorio |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| `validate-graphql-request` | Elemento raíz.                                                                                                                               | Sí      |
| `authorize` | Agregue uno o varios de estos elementos para proporcionar autorización de nivel de campo con errores de nivel de solicitud y de campo.   | Yes |
| `if` | Agregue uno o varios de estos elementos para los cambios condicionales en la acción para una autorización de nivel de campo. | No |

### <a name="attributes"></a>Atributos

| Nombre                       | Descripción                                                                                                                                                            | Obligatorio | Valor predeterminado |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| `error-variable-name` | Nombre de la variable de `context.Variables` en la que se registrarán los errores de validación.  |   No    | N/D   |
| `max-size` | Tamaño máximo de la carga útil de la solicitud en bytes. Valor máximo permitido: 102 400 bytes (100 KB). (Póngase en contacto con el [soporte técnico](https://azure.microsoft.com/support/options/) si tiene que aumentar este límite). | Sí       | N/D   |
| `max-depth` | Entero. Profundidad de consulta máxima. | No | 6 |
| `path` | Ruta de la consulta en la que se ejecutará la validación de autorización. | Sí | N/D |
| `action` | [Acción](#request-actions) que se va a realizar para el campo coincidente. Se puede cambiar si se especifica una condición de coincidencia. |  Sí     | N/D   |
| `condition` | Valor booleano que determina si la [expresión de la directiva](api-management-policy-expressions.md) coincide. Se usa la primera condición de coincidencia. | No | N/D |

### <a name="request-actions"></a>Acciones de solicitud

Las acciones disponibles se describen en la tabla siguiente.

|Acción |Descripción  |
|---------|---------|
|`reject`     | Se produce un error de solicitud y la solicitud no se envía al back-end.     |
|`remove`     | Se produce un error de campo y el campo se quita de la solicitud.         |
|`allow`     | El campo se pasa al back-end.        |

### <a name="usage"></a>Uso

Esta directiva puede usarse en las siguientes [secciones](./api-management-howto-policies.md#sections) y [ámbitos](./api-management-howto-policies.md#scopes) de directiva.

-   **Secciones de la directiva:** inbound (entrada)

-   **Ámbitos de la directiva:** todos los ámbitos

## <a name="error-handling"></a>Control de errores

Si no se valida con el esquema GraphQL o se produce un error en el tamaño o la profundidad de la solicitud, se trata de un error de solicitud y se produce un error en la solicitud con un bloque de errores (pero sin bloque de datos). 

De forma similar a la propiedad [`Context.LastError`](api-management-error-handling-policies.md#lasterror), todos los errores de validación de GraphQL se propagan automáticamente en la variable `GraphQLErrors`. Si los errores deben propagarse por separado, puede especificar un nombre de variable de error. Los errores se insertan en la variable `error` y en la variable `GraphQLErrors`. 

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el trabajo con directivas, vea:

-   [Directivas de Azure API Management](api-management-howto-policies.md)
-   [API de transformación](transform-api.md)
-   [Referencia de directivas](./api-management-policies.md) para una lista completa de instrucciones de directivas y su configuración
-   [Ejemplos de directivas](./policy-reference.md)
-   [Control de errores](./api-management-error-handling-policies.md)
