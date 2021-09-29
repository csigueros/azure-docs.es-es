---
title: Excepciones de Resource Manager de Azure Service Bus | Microsoft Docs
description: Lista de las excepciones de Service Bus que expone Azure Resource Manager y de las acciones sugeridas.
ms.topic: article
ms.date: 09/15/2021
ms.openlocfilehash: be88fc84fcc8d90994f134a6efd64ed2bbd964a4
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128610833"
---
# <a name="service-bus-resource-manager-exceptions"></a>Excepciones de Service Bus que expone Resource Manager

En este artículo se enumeran las excepciones generadas al interactuar con Azure Service Bus mediante Azure Resource Manager a través de plantillas o de llamadas directas.

> [!IMPORTANT]
> Este documento se actualiza con frecuencia. Compruebe si hay actualizaciones.

A continuación se muestran las distintas excepciones y errores que aparecen en Azure Resource Manager.

## <a name="error-bad-request"></a>Error: Solicitud incorrecta.

"Solicitud incorrecta" significa que no se pudo validar la solicitud que recibió Resource Manager.

| Código de error | Subcódigo de error | Mensaje de error | Descripción | Recomendación |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Bad Request | 40000 | SubCode=40000. The property *'property name'* cannot be set when creating a Queue because the namespace *'namespace name'* is using the 'Basic' Tier. This operation is only supported in 'Standard' or 'Premium' tier (La propiedad "property name" no se puede establecer al crear una cola porque el espacio de nombres "namespace name" está utilizando un nivel Básico. Esta operación solo se admite en los niveles Estándar o Premium). | En el nivel Básico de Azure Service Bus no se pueden establecer ni actualizar las siguientes propiedades: <ul> <li> RequiresDuplicateDetection </li> <li> AutoDeleteOnIdle </li> <li>RequiresSession</li> <li>DefaultMessageTimeToLive </li> <li> DuplicateDetectionHistoryTimeWindow </li> <li> EnableExpress </li> <li> ForwardTo </li> <li> Temas </li> </ul> | Considere la posibilidad de actualizar del nivel Básico al nivel Estándar o Premium para usar esta funcionalidad. |
| Bad Request | 40000 | SubCode=40000. The value for the 'requiresDuplicateDetection' property of an existing Queue(or Topic) cannot be changed (No se puede cambiar el valor de la propiedad "requiresDuplicateDetection" de una cola (o tema) existente). | La detección de duplicados debe estar habilitada o deshabilitada en el momento de la creación de la entidad. El parámetro de configuración de la detección de duplicados no se puede cambiar después de la creación. | Para habilitar la detección de duplicados en una cola o un tema creados anteriormente, puede crear una nueva cola o tema con detección de duplicados y, a continuación, realizar el reenvío desde la cola original al nuevo tema o cola. |
| Bad Request | 40000 | SubCode=40000. The specified value 16384 is invalid. La propiedad "MaxSizeInMegabytes" debe tener uno de los siguientes valores: 1024;2048;3072;4096;5120. | El valor de MaxSizeInMegabytes no es válido. | Asegúrese de que el valor de MaxSizeInMegabytes es uno de los siguientes: 1024, 2048, 3072, 4096, 5120. |
| Bad Request | 40000 | SubCode=40000. Partitioning cannot be changed for Queue/Topic (No se puede cambiar la creación de particiones para la cola o el tema). | No se puede cambiar la creación de particiones para la entidad. | Cree una nueva entidad (cola o tema) y habilite las particiones. | 
| Bad Request | ninguno | The namespace *'namespace name'* does not exist (El espacio de nombres "namespace name" no existe). | El espacio de nombres no existe en la suscripción de Azure. | Para solucionar este error intente alguna de las opciones siguientes <ul> <li> Asegúrese de que la suscripción de Azure es la correcta. </li> <li> Asegúrese de que el espacio de nombres existe. </li> <li> Compruebe que el nombre del espacio de nombres es correcto (no tiene errores ortográficos ni cadenas nulas). </li> </ul> | 
| Bad Request | 40400 | Subcode = 40400. The auto forwarding destination entity does not exist (La entidad de destino de reenvío automático no existe). | El destino de la entidad de destino de reenvío automático no existe. | La entidad de destino (cola o tema) debe existir antes de que se cree el origen. Vuelva a intentarlo después de crear la entidad de destino. |
| Bad Request | 40000 | SubCode=40000. The supplied lock time exceeds the allowed maximum of '5' minutes (El tiempo de bloqueo proporcionado supera el máximo permitido de "5" minutos). | El tiempo durante el que un mensaje se puede bloquear debe estar entre 1 minuto (mínimo) y 5 minutos (máximo). | Asegúrese de que el tiempo de bloqueo proporcionado está entre 1 y 5 minutos. |
| Bad Request | 40000 | SubCode=40000. Both DelayedPersistence and RequiresDuplicateDetection property cannot be enabled together (Las propiedades DelayedPersistence y RequiresDuplicateDetection no se pueden habilitar juntas). | Las entidades con detección de duplicados habilitada en ellas deben ser persistentes, por lo que no se puede retrasar la persistencia. | Más información sobre [Detección de duplicados](duplicate-detection.md) |
| Bad Request | 40000 | SubCode=40000. The value for RequiresSession property of an existing Queue cannot be changed (No se puede cambiar el valor de la propiedad RequiresSession de una cola existente). | La compatibilidad con las sesiones debe estar habilitada en el momento de la creación de la entidad. Una vez creada, no puede habilitar o deshabilitar sesiones en una entidad existente (cola o suscripción). | Elimine y vuelva a crear una nueva cola (o suscripción) con la propiedad "RequiresSession" habilitada. |
| Bad Request | 40000 | SubCode=40000. 'URI_PATH' contains character(s) that is not allowed by Service Bus. Entity segments can contain only letters, numbers, periods(.), hyphens(-), and underscores(_) ("URI_PATH" contiene caracteres no permitidos por Service Bus. Los segmentos de la entidad pueden contener solo letras, números, puntos (.), guiones (-) y guiones bajos (_)). | Entity segments can contain only letters, numbers, periods(.), hyphens(-), and underscores(_) ("URI_PATH" contiene caracteres no permitidos por Service Bus. Los segmentos de la entidad pueden contener solo letras, números, puntos (.), guiones (-) y guiones bajos (_)). Cualquier otro carácter hará que se produzca un error en la solicitud. | Asegúrese de que no hay ningún carácter no válido en la ruta de acceso del URI. |


## <a name="error-code-429"></a>Código de error: 429

Al igual que en HTTP, el "código de error 429" indica "demasiadas solicitudes". Implica que se está limitando el recurso específico (espacio de nombres) debido a que hay demasiadas solicitudes (o debido a operaciones en conflicto) en ese recurso.

| Código de error | Subcódigo de error | Mensaje de error | Descripción | Recomendación |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| 429 | 50004 | SubCode=50004. The request was terminated because the namespace *your namespace* is being throttled (La solicitud finalizó porque se está limitando el espacio de nombres "su espacio de nombres"). | Esta condición de error se produce si el número de solicitudes entrantes supera la limitación del recurso. | Espere unos segundos y vuelva a intentarlo. <br/> <br/> Conozca más información sobre las [cuotas](service-bus-quotas.md) y [límites de solicitudes de Azure Resource Manager](../azure-resource-manager/management/request-limits-and-throttling.md)|
| 429 | 40901 | SubCode=40901. Another conflicting operation is in progress (Hay otra operación en conflicto en curso). | Hay otra operación en conflicto en curso en el mismo recurso o entidad | Espere a que se complete la operación en curso actual antes de volver a intentarlo. |
| 429 | 40900 | SubCode=40900. Conflicto. Se solicita una operación no permitida con el estado actual del recurso. | Esta condición se puede alcanzar cuando se realizan varias solicitudes para realizar las operaciones en la misma entidad (cola, tema, suscripción o regla) al mismo tiempo. | Espere unos segundos y vuelva a intentarlo. |
| 429 | 40901 | Request on entity *'entity name'* conflicted with another request (La solicitud para la entidad "nombre de entidad" ha entrado en conflicto con otra solicitud). | Hay otra operación en conflicto en curso en el mismo recurso o entidad | Espere a que se complete la operación anterior antes de volver a intentarlo. |
| 429 | 40901 | Another update request is in progress for the entity *'entity name'* (Hay otra solicitud de actualización en curso para la entidad "nombre de entidad"). | Hay otra operación en conflicto en curso en el mismo recurso o entidad | Espere a que se complete la operación anterior antes de volver a intentarlo. |
| 429 | None | Resource Conflict Occurred. Another conflicting operation may be in progress. If this is a retry for failed operation, background cleanup is still pending (Se ha producido un conflicto de recursos. Puede que haya otra operación en conflicto en curso. Si se trata de un reintento de una operación con error, la limpieza en segundo plano está todavía pendiente). Vuelva a intentarlo más tarde. | Esta condición se puede producir si hay una operación pendiente en la misma entidad. | Espere a que se complete la operación anterior antes de volver a intentarlo. |


## <a name="error-code-not-found"></a>Código de error: No encontrado

Esta clase de errores indica que no se ha encontrado el recurso.

| Código de error | Subcódigo de error | Mensaje de error | Descripción | Recomendación |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| No encontrado | ninguno | Entity *'entity name'* was not found (No se ha encontrado la entidad "nombre de entidad"). | La entidad en la que no se encontró la operación. | Compruebe si la entidad existe y vuelva a intentar la operación. |
| No encontrado | ninguno | Not Found. The Operation doesn't exist (No se encuentra. La operación no existe). | La operación que está intentando realizar no existe. | Compruebe la operación e inténtelo de nuevo. |
| No encontrado | ninguno | The incoming request is not recognized as a namespace policy put request (La solicitud entrante no se reconoce como una solicitud put de directiva de espacio de nombres). | El cuerpo de la solicitud entrante es nulo y, por tanto, no se puede ejecutar como una solicitud put. | Compruebe el cuerpo de la solicitud para asegurarse de que no sea nulo. | 
| No encontrado | ninguno | The messaging entity *'entity name'* could not be found (No se pudo encontrar la entidad de mensajería "nombre de entidad"). | No se ha encontrado la entidad en la que está intentando ejecutar la operación. | Compruebe si la entidad existe y vuelva a intentar la operación. |

## <a name="error-code-internal-server-error"></a>Código de error: Error interno del servidor

Esta clase de errores indica que se produjo un error interno del servidor

| Código de error | Subcódigo de error | Mensaje de error | Descripción | Recomendación |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| Internal Server Error | 50000 | Subcódigo = 50000. Internal Server Error| Puede ocurrir por diversos motivos. Algunos de los síntomas son: <ul> <li> La solicitud o el cuerpo del cliente está dañado y conduce a un error. </li> <li> Se agotó el tiempo de espera de la solicitud del cliente debido a problemas de procesamiento en el servicio. </li> </ul> | Para resolver este problema <ul> <li> Asegúrese de que los parámetros de las solicitudes no sean nulos o tengan un formato incorrecto. </li> <li> Vuelva a intentarlo. </li> </ul> |

## <a name="error-code-unauthorized"></a>Código de error: No autorizado

Esta clase de errores indica la ausencia de autorización para ejecutar el comando.

| Código de error | Subcódigo de error | Mensaje de error | Descripción | Recomendación |
| ---------- | ------------- | ------------- | ----------- | -------------- |
| No autorizado | ninguno | Operación no válida en el espacio de nombres secundario. El espacio de nombres secundario es de solo lectura. | La operación se realizó en el espacio de nombres secundario, que se configura como un espacio de nombres de solo lectura. | Vuelva a intentar el comando en el espacio de nombres principal. Más información sobre el [espacio de nombres secundario](service-bus-geo-dr.md) |
| No autorizado | ninguno | MissingToken: no se encontró el encabezado de autorización. | Este error se produce cuando la autorización tiene valores null o valores incorrectos. | Asegúrese de que el valor de token mencionado en el encabezado de autorización sea correcto y no sea null. |