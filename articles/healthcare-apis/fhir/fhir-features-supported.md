---
title: Características de FHIR admitidas en el servicio FHIR
description: En este artículo se explican las características de la especificación de FHIR que se implementan en las API de atención sanitaria.
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/11/2021
ms.author: cavoeg
ms.openlocfilehash: 4c1b9efce8ad29eeb9ebca6285849760f20fbc17
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132399908"
---
# <a name="supported-fhir-features"></a>Características de FHIR admitidas

> [!IMPORTANT]
> Azure Healthcare APIs se encuentra actualmente en VERSIÓN PRELIMINAR. Los [Términos de uso complementarios para las versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) incluyen términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado con disponibilidad general.

El servicio FHIR de las API de Azure Healthcare (aquí denominado servicio FHIR) proporciona una implementación totalmente administrada del servidor de FHIR de código abierto y es una implementación del estándar &reg; [FHIR.](https://hl7.org/fhir) [](https://github.com/microsoft/fhir-server) En este documento se enumeran las características principales del servicio FHIR.

## <a name="fhir-version"></a>Versión de FHIR

Versión más reciente admitida: `4.0.1`

Entre las versiones anteriores también admitidas actualmente se incluye: `3.0.2`

## <a name="rest-api"></a>API DE REST

A continuación se muestra un resumen de las funcionalidades de RESTful admitidas. Para más información sobre la implementación de estas funcionalidades, consulte Funcionalidades de la API rest de [FHIR.](fhir-rest-api-capabilities.md) 

| API    | API de Azure para FHIR | Servicio FHIR en las API de atención sanitaria | Comentario |
|--------|--------------------|---------------------------------|---------|
| leer   | Sí                | Sí                             |         |
| vread  | Sí                | Sí                             |         |
| update | Sí                | Sí                             |         | 
| update with optimistic locking | Sí       | Sí       |
| update (conditional)           | Sí       | Sí       |
| patch                          | Sí       | Sí       | Solo compatibilidad [con la revisión JSON.](../../healthcare-apis/fhir/fhir-rest-api-capabilities.md#patch-and-conditional-patch) |
| patch (condicional)            | Sí       | Sí       |
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

## <a name="extended-operations"></a>Operaciones extendidas

Todas las operaciones admitidas que extienden la API REST.

| Tipo de parámetro de búsqueda | API de Azure para FHIR | Servicio FHIR en las API de atención sanitaria| Comentario |
|------------------------|-----------|-----------|---------|
| [$export](../../healthcare-apis/data-transformation/export-data.md) (sistema completo) | Sí       | Sí       | Admite el sistema, el grupo y el paciente. |
| [$convert-data](../../healthcare-apis/data-transformation/convert-data.md)          | Sí       | Sí       |         |
| [$validate](validation-against-profiles.md)              | Sí       | Sí       |         |
| [$member-match](tutorial-member-match.md)          | Sí       | Sí       |         |
| [$patient-everything](patient-everything.md)    | Sí       | Sí       |         |
| $purge historial         | Sí       | Sí       |         |

## <a name="role-based-access-control"></a>Control de acceso basado en rol

El servicio FHIR usa [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) control de acceso. 

## <a name="service-limits"></a>Límites de servicio

* **Tamaño de conjunto**: cada conjunto está limitado a 500 elementos.

* **Límite de** suscripción: de forma predeterminada, cada suscripción está limitada a un máximo de 10 servicios de FHIR. El límite se puede usar en una o varias áreas de trabajo. 

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha leído sobre las características de FHIR admitidas en el servicio FHIR. Para obtener información sobre la implementación del servicio FHIR, consulte
 
>[!div class="nextstepaction"]
>[Implementación de un servicio de FHIR en Azure Healthcare APIs mediante el portal](fhir-portal-quickstart.md)
