---
title: 'Creación y administración del informe de historial de revisiones de acceso descargable (versión preliminar): Azure Active Directory'
description: Con las revisiones de acceso de Azure Active Directory, puede descargar un historial de revisiones para las revisiones de acceso de su organización.
services: active-directory
documentationcenter: ''
author: ajburnle
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: compliance
ms.date: 03/25/2021
ms.author: ajburnle
ms.openlocfilehash: 6f0d9b92b9aa3bb48533d5270c0fcd31aa8df82c
ms.sourcegitcommit: 7f59e3b79a12395d37d569c250285a15df7a1077
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/02/2021
ms.locfileid: "111026652"
---
# <a name="create-and-manage-downloadable-access-review-history-report-preview-in-azure-ad-access-reviews"></a>Creación y administración del informe de historial de revisiones de acceso descargable (versión preliminar) en revisiones de acceso de Azure Active Directory

Con las revisiones de acceso de Azure Active Directory (Azure AD), puede crear un historial de revisiones descargable para ayudar a su organización a obtener más conclusiones. El informe extrae las decisiones tomadas por los revisores al crear un informe. Estos informes se pueden construir para incluir revisiones de acceso específicas, durante un período de tiempo específico, y se pueden filtrar para incluir diferentes tipos de revisión y resultados de revisión.
 
## <a name="who-can-access-and-request-review-history"></a>¿Quién puede acceder al historial de revisión y solicitarlo?

La revisión y solicitud del historial de revisión están disponibles para cualquier usuario si está autorizado para ver las revisiones de acceso. Para ver qué roles pueden ver y crear revisiones de acceso, consulte [¿Qué tipos de recursos se pueden revisar?](deploy-access-reviews.md#what-resource-types-can-be-reviewed). El administrador global y el lector global pueden ver todas las revisiones de acceso. Los demás usuarios solo pueden ver informes sobre las revisiones de acceso que han generado.

## <a name="how-to-create-a-review-history-report"></a>Creación de un informe de historial de revisiones

**Rol de requisito previo:** todos los usuarios están autorizados para ver las revisiones de acceso

1. En Azure Portal, seleccione **Azure Active Directory** y, luego, **Gobierno de identidades**.

1. En el menú de la izquierda, en **Revisiones de acceso,** seleccione **Historial de revisiones**.
 
1. Seleccione **Nuevo informe**. 

1. Especifique una fecha de inicio y finalización de la revisión.

1. Seleccione los tipos de revisión y los resultados de revisión que desea incluir en el informe. 

    ![Revisiones de acceso - Informe del historial de revisiones de acceso - Crear](./media/access-reviews-downloadable-review-history/create-review-history.png)

1. A continuación, seleccione **Crear** para crear un informe de historial de revisiones de acceso.

## <a name="how-to-download-review-history-reports"></a>Descarga de informes de historial de revisiones

Una vez creado un informe de historial de revisiones, puede descargarlo. Todos los informes creados están disponibles para su descarga durante treinta días en formato CSV.

1. Seleccione **Historial de revisiones** en **Gobierno de identidades**. Todos los informes del historial de revisiones que haya creado estarán disponibles. 
1. Seleccione el informe que desea descargar. 

## <a name="what-is-included-in-a-review-history-report"></a>¿Qué se incluye en un informe de historial de revisiones?

Los informes proporcionan detalles por usuario que muestran lo siguiente:

| Nombre del elemento | Descripción |
| --- | --- |
| AccessReviewId |  Identificador del objeto de revisión |
| ReviewType | Los tipos de revisión incluyen grupo, aplicación, rol de Azure AD, rol de Azure y paquete de acceso|
|ResourceDisplayName | Nombre para mostrar del recurso que se está revisando |
| ResourceId | Identificador del recurso que se está revisando |
| ReviewName |  Nombre de la revisión |
| CreatedDateTime | Fecha y hora de creación de la revisión |
| ReviewStartDate | Fecha de inicio de la revisión
| ReviewEndDate | Fecha de finalización de la revisión |
| ReviewStatus | Estado de la revisión. Para ver todos los estados de revisión, consulte la tabla de estados de revisión de acceso [aquí](create-access-review.md). |
| OwnerId | Identificador del propietario revisor |
| OwnerName | Nombre del propietario revisor |
| OwnerUPN | Nombre principal de usuario del propietario revisor |
| PrincipalId | Identificador de la entidad de seguridad que se está revisando |
| Nombre principal | Nombre de la entidad de seguridad que se está revisando |
| PrincipalUPN | Nombre principal del usuario que se está revisando |
| PrincipalType | Tipo de la entidad de seguridad. Entre las opciones se incluyen usuario, grupo y entidad de servicio. |
| ReviewDate | Fecha de la revisión |
| ReviewResult | Los resultados de la revisión incluyen Denegar, Aprobar y No revisado |
|Justificación | Justificación del resultado de la revisión proporcionado por el revisor |
| ReviewerId | Identificador del revisor |
| ReviewerName | Nombre del revisor |
| ReviewerUPN | Nombre principal del usuario revisor |
| ReviewerEmailAddress | Dirección de correo electrónico del revisor |
| AppliedByName | Nombre del usuario que aplicó el resultado de la revisión |
| AppliedByUPN | Nombre principal de usuario del usuario que aplicó el resultado de la revisión|
| AppliedByEmailAddress | Dirección de correo electrónico del usuario que aplicó el resultado de la revisión |
| AppliedDate | Fecha en la que se aplicó el resultado de la revisión |
| AccessRecommendation | Entre las recomendaciones del sistema se incluyen Aprobar, Denegar y Sin información |
|SubmissionResult | Estado del envío de los resultados de la revisión incluye Aplicado y No aplicado. |

## <a name="next-steps"></a>Pasos siguientes
- [Revisión del acceso a grupos o aplicaciones](perform-access-review.md)
- [Revisión del acceso de uno mismo a grupos o aplicaciones](review-your-access.md)
- [Completar una revisión de acceso de grupos o aplicaciones](complete-access-review.md)
