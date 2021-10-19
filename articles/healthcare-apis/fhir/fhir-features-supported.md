---
title: Características de FHIR admitidas en el servicio FHIR
description: En este artículo se explican las características de la especificación de FHIR que se implementan en las API de atención sanitaria.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 08/03/2021
ms.author: cavoeg
ms.openlocfilehash: c1f40d6129f08b8369885631232b394d66f1d260
ms.sourcegitcommit: 91915e57ee9b42a76659f6ab78916ccba517e0a5
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130045850"
---
# <a name="supported-fhir-features"></a>Características de FHIR admitidas

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

El servicio FHIR en las API de Azure Healthcare (que aquí se denomina servicio FHIR) proporciona una implementación totalmente administrada del servidor de FHIR de código abierto y es una implementación del estándar &reg; [FHIR.](https://hl7.org/fhir) [](https://github.com/microsoft/fhir-server) En este documento se enumeran las características principales del servicio FHIR.

## <a name="fhir-version"></a>Versión de FHIR

Versión más reciente admitida: `4.0.1`

Entre las versiones anteriores también admitidas actualmente se incluye: `3.0.2`

## <a name="rest-api"></a>API DE REST

| API    | API de Azure para FHIR | Servicio FHIR en las API de atención sanitaria | Comentario |
|--------|--------------------|---------------------------------|---------|
| leer   | Sí                | Sí                             |         |
| vread  | Sí                | Sí                             |         |
| update | Sí                | Sí                             |         | 
| update with optimistic locking | Sí       | Sí       |
| update (conditional)           | Sí       | Sí       |
| patch                          | Sí       | Sí       | Compatibilidad solo [con la revisión](https://www.hl7.org/fhir/http.html#patch) JSON. Hemos incluido una solución alternativa para usar la revisión JSON en un paquete de [esta solicitud de solicitud de acceso.](https://github.com/microsoft/fhir-server/pull/2143)|
| patch (condicional)            | Sí       | Sí       |
| delete                         | Sí       | Sí       | Consulte los detalles en la sección de eliminación a continuación. |
| delete (conditional)           | Sí       | Sí       | Consulte los detalles en la sección de eliminación a continuación. |
| history                        | Sí       | Sí       |
| create                         | Sí       | Sí       | Admite POST y PUT |
| create (conditional)           | Sí       | Sí       | Problema [n.º 1382](https://github.com/microsoft/fhir-server/issues/1382) |
| búsqueda                         | Parcial   | Parcial   | Consulte [Introducción a la búsqueda de FHIR](overview-of-search.md). |
| búsqueda encadenada                 | Sí       | Sí       | |
| búsqueda encadenada inversa         | Sí       | Sí       | |
| proceso por lotes                          | Sí       | Sí       |
| transaction                    | No        | Sí       |
| paging                         | Parcial   | Parcial   | `self` y `next` se admiten                     |
| intermediaries                 | No        | No        |

### <a name="delete-and-conditional-delete"></a>Eliminación y eliminación condicional

La eliminación definida por la especificación FHIR requiere que, después de la eliminación, las lecturas posteriores no específicas de la versión de un recurso devuelvan un código de estado HTTP 410 y que el recurso ya no se encuentre en la búsqueda. El Azure API for FHIR y el servicio FHIR también le permiten eliminar completamente (incluido todo el historial) del recurso. Para eliminar completamente el recurso, puede pasar una configuración de parámetros `hardDelete` a true (`DELETE {server}/{resource}/{id}?hardDelete=true`). Si no pasa este parámetro o establece `hardDelete` en false, las versiones históricas del recurso seguirán estando disponibles.

Además de eliminar, el Azure API for FHIR y el servicio FHIR admiten la eliminación condicional, lo que le permite pasar un criterio de búsqueda para eliminar un recurso. De forma predeterminada, la eliminación condicional le permitirá eliminar un elemento a la vez. También puede especificar el parámetro `_count` para eliminar hasta 100 elementos a la vez. A continuación se muestran algunos ejemplos del uso de la eliminación condicional.

Para eliminar un solo elemento mediante la eliminación condicional, debe especificar criterios de búsqueda que devuelvan un solo elemento.
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704
```

Puede realizar la misma búsqueda, pero incluir hardDelete=true para eliminar también todo el historial.
```JSON 
DELETE https://{{hostname}}/Patient?identifier=1032704&hardDelete=true
```

Si desea eliminar varios recursos, puede incluir , que eliminará hasta 100 recursos que coincidan `_count=100` con los criterios de búsqueda. 
``` JSON
DELETE https://{{hostname}}/Patient?identifier=1032704&_count=100
```

## <a name="extended-operations"></a>Operaciones extendidas

Todas las operaciones admitidas que amplían la API REST.

| Tipo de parámetro de búsqueda | API de Azure para FHIR | Servicio FHIR en las API de atención sanitaria| Comentario |
|------------------------|-----------|-----------|---------|
| $export (todo el sistema) | Sí       | Sí       |         |
| Patient/$export        | Sí       | Sí       |         |
| Group/$export          | Sí       | Sí       |         |
| $convert-data          | Sí       | Sí       |         |
| $validate              | Sí       | Sí       |         |
| $member-match          | Sí       | Sí       |         |
| $patient-everything    | Sí       | Sí       |         |
| $purge-history         | Sí       | Sí       |         |

## <a name="role-based-access-control"></a>Control de acceso basado en rol

El servicio FHIR usa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para el control de acceso. 

## <a name="service-limits"></a>Límites de servicio

* **Tamaño de conjunto**: cada conjunto está limitado a 500 elementos.

* **Límite de** suscripción: de forma predeterminada, cada suscripción está limitada a un máximo de 10 servicios de FHIR. El límite se puede usar en una o varias áreas de trabajo. 

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha leído sobre las características de FHIR admitidas en el servicio FHIR. A continuación, implemente el servicio FHIR.
 
>[!div class="nextstepaction"]
>[Implementación de un servicio de FHIR en Azure Healthcare APIs mediante el portal](fhir-portal-quickstart.md)
