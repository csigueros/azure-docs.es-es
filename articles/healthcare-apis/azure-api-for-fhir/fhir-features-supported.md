---
title: 'Características de FHIR admitidas en Azure: Azure API for FHIR'
description: En este artículo se explica qué características de la especificación de FHIR se implementan en Azure API for FHIR
services: healthcare-apis
author: caitlinv39
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: reference
ms.date: 11/11/2021
ms.author: cavoeg
ms.openlocfilehash: b6c170a7b2f46adeb4b287424601455bb078aba7
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: MT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132397307"
---
# <a name="features"></a>Características

Azure API for FHIR proporciona una implementación totalmente administrada Microsoft FHIR Server para Azure. El servidor es una implementación del estándar [FHIR](https://hl7.org/fhir). En este documento se enumeran las características principales de FHIR Server.

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
| patch                          | Sí       | Sí       | Solo compatibilidad [con la revisión JSON.](https://www.hl7.org/fhir/http.html#patch) Hemos incluido una solución alternativa para usar la revisión JSON en una agrupación en [esta solicitud de solicitud de registro.](https://github.com/microsoft/fhir-server/pull/2143)|
| patch (condicional)            | Sí       | Sí       |
| history                        | Sí       | Sí       |
| create                         | Sí       | Sí       | Admite POST y PUT |
| create (conditional)           | Sí       | Sí       | Problema [n.º 1382](https://github.com/microsoft/fhir-server/issues/1382) |
| búsqueda                         | Parcial   | Parcial   | Consulte [Introducción a la búsqueda de FHIR](overview-of-search.md). |
| búsqueda encadenada                 | Sí       | Sí       | Consulte la nota siguiente. |
| búsqueda encadenada inversa         | Sí       | Sí       | Consulte la nota siguiente. |
| proceso por lotes                          | Sí       | Sí       |
| transaction                    | No        | Sí       |
| paging                         | Parcial   | Parcial   | `self` y `next` se admiten                     |
| intermediaries                 | No        | No        |

> [!Note] 
> En Azure API for FHIR y el servidor FHIR de código abierto respaldado por Cosmos, la búsqueda encadenada y la búsqueda encadenada inversa son una implementación de MVP. Para realizar búsquedas encadenadas en Cosmos DB, la implementación le guía por la expresión de búsqueda y emite subconsultas para resolver los recursos coincidentes. Esto se hace para cada nivel de la expresión. Si alguna consulta devuelve más de 1000 resultados, se producirá un error.

## <a name="extended-operations"></a>Operaciones extendidas

Todas las operaciones admitidas que extienden la API REST.

| Tipo de parámetro de búsqueda | API de Azure para FHIR | Servicio FHIR en las API de atención sanitaria| Comentario |
|------------------------|-----------|-----------|---------|
| [$export](../../healthcare-apis/data-transformation/export-data.md) (sistema completo) | Sí       | Sí       | Admite el sistema, el grupo y el paciente.   |
| [$convert-data](convert-data.md)          | Sí       | Sí       |         |
| [$validate](validation-against-profiles.md)              | Sí       | Sí       |         |
| [$member-match](tutorial-member-match.md)          | Sí       | Sí       |         |
| [$patient-everything](patient-everything.md)    | Sí       | Sí       |         |
| $purge-history         | Sí       | Sí       |         |

## <a name="persistence"></a>Persistencia

Microsoft FHIR Server tiene un módulo de persistencia conectable (consulte [`Microsoft.Health.Fhir.Core.Features.Persistence`](https://github.com/Microsoft/fhir-server/tree/master/src/Microsoft.Health.Fhir.Core/Features/Persistence)).

Actualmente, el código fuente abierto de FHIR Server incluye una implementación para [Azure Cosmos DB](../../cosmos-db/index-overview.md) y [SQL Database](https://azure.microsoft.com/services/sql-database/).

Cosmos DB es una base de datos (SQL API, MongoDB API, etc.) de varios modelos distribuida de forma global. Admite diferentes [niveles de coherencia](../../cosmos-db/consistency-levels.md). La plantilla de implementación predeterminada configura FHIR Server con una coherencia de `Strong`, pero la directiva de coherencia puede modificarse (generalmente de forma más relajada) en función de cada solicitud mediante el encabezado de solicitud `x-ms-consistency-level`.

## <a name="role-based-access-control"></a>Control de acceso basado en rol

FHIR Server utiliza [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) para el control de acceso. En concreto, se aplica el control de acceso basado en roles (RBAC) si el parámetro de configuración `FhirServer:Security:Enabled` se establece en `true`, y todas las solicitudes (excepto `/metadata`) a FHIR Server deben tener el encabezado de solicitud `Authorization` establecido en `Bearer <TOKEN>`. El token debe contener uno o varios roles, tal como se define en la notificación `roles`. Se permitirá una solicitud si el token contiene un rol que permite la acción especificada en el recurso especificado.

Actualmente, las acciones permitidas para un rol determinado se aplican *globalmente* en la API.

## <a name="service-limits"></a>Límites de servicio

* [**Unidades de solicitud (RU)**](../../cosmos-db/concepts-limits.md): puede configurar hasta 10 000 unidades de solicitud en el portal de Azure API for FHIR. Necesitará un mínimo de 400 RU o 40 RU/GB, lo que sea mayor. Si necesita más de 10 000 RU, puede poner una vale de soporte técnico para aumentar las RU. El máximo disponible es 1 000 000.

* **Tamaño de conjunto**: cada conjunto está limitado a 500 elementos.

* **Tamaño de los datos**: los datos y documentos deben ser ligeramente inferiores a 2 MB.

* **Límite de suscripción**: de forma predeterminada, cada suscripción está limitada a un máximo de 10 instancias de servidor de FHIR. Si necesita más instancias por suscripción, abra una incidencia de soporte técnico y proporcione detalles sobre sus necesidades.

* **Conexiones simultáneas e instancias**: de manera predeterminada, tiene 15 conexiones simultáneas en dos instancias del clúster (para un total de 30 solicitudes simultáneas). Si necesita un mayor número solicitudes simultáneas, abra una incidencia de soporte técnico y proporcione detalles de lo que necesita.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha leído sobre las características admitidas de FHIR en Azure API for FHIR. Para obtener información sobre la implementación Azure API for FHIR, consulte
 
>[!div class="nextstepaction"]
>[Implementación de Azure API for FHIR](fhir-paas-portal-quickstart.md)
