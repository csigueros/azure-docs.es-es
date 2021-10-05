---
title: ¿Durante cuánto tiempo Azure AD almacena los datos de informes? | Microsoft Docs
description: Descubra durante cuánto tiempo Azure almacena los distintos tipos de datos de informes.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 183e53b0-0647-42e7-8abe-3e9ff424de12
ms.service: active-directory
ms.devlang: ''
ms.topic: reference
ms.tgt_pltfrm: ''
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/05/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b53333ff9d61e6ee5bbc98bb4756199ca07486d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630079"
---
# <a name="how-long-does-azure-ad-store-reporting-data"></a>¿Durante cuánto tiempo Azure AD almacena los datos de informes?


En este artículo, aprenderá sobre las directivas de retención de datos para los diferentes informes de actividad en Azure Active Directory. 

### <a name="when-does-azure-ad-start-collecting-data"></a>¿Cuándo Azure AD comienza la recopilación de datos?

| Edición de Azure AD | Inicio de la recopilación |
| :--              | :--   |
| Azure AD Premium P1 <br /> Azure AD Premium P2 | Cuándo suscribirse |
| Azure AD Free| La primera vez que abra la [hoja de Azure Active Directory](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/Overview) o use las [API de informes](./overview-reports.md)  |

---

### <a name="when-is-the-activity-data-available-in-the-azure-portal"></a>¿Cuándo estarán disponibles los datos de actividad en Azure Portal?

- **Inmediatamente**: si ya ha estado trabajando con informes en Azure Portal.
- **Dentro de 2 horas**: si no ha activado la generación de informes en Azure Portal.

---

### <a name="how-soon-can-i-see-activities-data-after-getting-a-premium-license"></a>¿En cuánto tiempo podré ver los datos de las actividades después de obtener una licencia prémium?

Si ya tiene datos de actividades con la licencia gratuita, entonces los verá inmediatamente al actualizar. Si no tiene ningunos, tardarán hasta tres días en mostrarse en los informes después de que actualice a una licencia Premium.

---

### <a name="when-does-azure-ad-start-collecting-security-signal-data"></a>¿Cuándo Azure AD comienza a recopilar datos de señales de seguridad?  

En el caso de las señales de seguridad, el proceso de recopilación se inicia cuando decide usar **Identity Protection Center**. 

---

### <a name="how-long-does-azure-ad-store-the-data"></a>¿Durante cuánto tiempo Azure AD almacena los datos?

**Informes de actividad**    

| Informe                 | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--                    | :--           | :--                 | :--                 |
| Registros de auditoría             | 7 días        | 30 días             | 30 días             |
| Inicios de sesión               | 7 días        | 30 días             | 30 días             |
| Uso de MFA de Azure AD        | 30 días       | 30 días             | 30 días             |

Puede conservar los datos de actividad de auditoría e inicio de sesión durante un tiempo mayor que el período de retención predeterminado que se ha descrito anteriormente si los enruta a una cuenta de almacenamiento de Azure con Azure Monitor. Para obtener más información, consulte [Archivado de registros de Azure AD en una cuenta de Azure Storage](quickstart-azure-monitor-route-logs-to-storage-account.md).

**Señales de seguridad**

| Informe         | Azure AD Free | Azure AD Premium P1 | Azure AD Premium P2 |
| :--            | :--           | :--                 | :--                 |
| Usuarios de riesgo    | ilimitado      | ilimitado            | ilimitado            |
| Inicios de sesión no seguros | 7 días        | 30 días             | 90 días             |

> [!NOTE]
> Los usuarios de riesgo no se eliminan hasta que se corrige el riesgo.

---

### <a name="can-i-see-last-months-data-after-getting-an-azure-ad-premium-license"></a>¿Puedo ver los datos del último mes después de obtener una licencia de Azure AD Premium?

**No**, no puede. Azure almacena hasta siete días de datos de actividad para una versión gratuita. Por lo tanto, cuando se cambia de una versión gratuita a una Premium, solo puede ver hasta 7 días de datos.

---
