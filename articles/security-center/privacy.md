---
title: Administración de datos de usuario en Microsoft Defender for Cloud | Microsoft Docs
description: Obtenga información sobre cómo administrar los datos de usuario en Microsoft Defender for Cloud. La administración de los datos de usuario incluye la capacidad de acceder, eliminar o exportar datos.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: memildin
ms.openlocfilehash: 69130a9d3804ffcf118dcd13aa2a40422fa01469
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131428375"
---
# <a name="manage-user-data-in-microsoft-defender-for-cloud"></a>Administración de datos de usuario en Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

En este artículo se proporciona información sobre cómo administrar los datos de usuario en Microsoft Defender for Cloud. La administración de los datos de usuario incluye la capacidad de acceder, eliminar o exportar datos.

[!INCLUDE [gdpr-intro-sentence.md](../../includes/gdpr-intro-sentence.md)]

Un usuario de Defender for Cloud que tenga asignado el rol Lector, Propietario, Colaborador o Administrador de cuenta puede acceder a los datos del cliente dentro de la herramienta. Para obtener más información sobre el rol Administrador de cuenta, consulte [Roles integrados para el control de acceso basado en roles de Azure](../role-based-access-control/built-in-roles.md) para obtener más información sobre los roles Lector, Propietario y Colaborador. Consulte el artículo sobre los [administradores de la suscripción de Azure](../cost-management-billing/manage/add-change-subscription-administrator.md).

## <a name="searching-for-and-identifying-personal-data"></a>Buscar e identificar datos personales
Un usuario de Defender for Cloud puede ver sus datos personales a través de Azure Portal. En Defender for Cloud solo se almacenan los detalles de contacto de seguridad como direcciones de correo electrónico y números de teléfono. Para obtener más información, consulte [Provisión de detalles de contacto de seguridad en Microsoft Defender for Cloud](configure-email-notifications.md).

En Azure Portal, un usuario puede ver las configuraciones de IP permitidas mediante la característica de acceso Just-In-Time a la máquina virtual de Defender for Cloud. Para obtener más información, consulte [Administrar el acceso a máquina virtual mediante Just-In-Time](just-in-time-access-usage.md).

En Azure Portal, un usuario puede ver las alertas de seguridad proporcionadas por Defender for Cloud, incluidos los detalles de atacantes y direcciones IP. Para obtener más información, consulte [Administración y respuesta a alertas de seguridad en Microsoft Defender for Cloud](managing-and-responding-alerts.md).

## <a name="classifying-personal-data"></a>Clasificación de datos personales
No es necesario clasificar los datos personales que se encuentran en la característica de contactos de seguridad de Defender for Cloud. Los datos guardados son una dirección de correo electrónico (o varias) y un número de teléfono. Defender for Cloud valida los [datos de contacto](configure-email-notifications.md).

No es necesario clasificar las direcciones IP y los números de puerto que guarda la característica [Just-In-Time](just-in-time-access-usage.md) de Defender for Cloud.

Solo un usuario que tenga asignado el rol de Administrador puede clasificar los datos personales mediante la [visualización de alertas](managing-and-responding-alerts.md) en Defender for Cloud.

## <a name="securing-and-controlling-access-to-personal-data"></a>Protección y control del acceso a los datos personales
Un usuario de Defender for Cloud que tenga asignado el rol Lector, Propietario, Colaborador o Administrador de cuenta puede acceder a los [datos de contacto de seguridad](configure-email-notifications.md).

Un usuario de Defender for Cloud que tenga asignado el rol Lector, Propietario, Colaborador o Administrador de cuenta puede acceder a las directivas [Just-In-Time](just-in-time-access-usage.md).

Un usuario de Defender for Cloud que tenga asignado el rol Lector, Propietario, Colaborador o Administrador de cuenta puede ver las [alertas](managing-and-responding-alerts.md).

## <a name="updating-personal-data"></a>Actualizar datos personales
Un usuario de Defender for Cloud que tenga asignado el rol Propietario, Colaborador o Administrador de cuenta puede actualizar los [datos de contacto de seguridad](configure-email-notifications.md) a través de Azure Portal.

Un usuario de Defender for Cloud que tenga asignado el rol Propietario, Colaborador o Administrador de cuenta puede actualizar las [directivas Just-In-Time](just-in-time-access-usage.md).

Un Administrador de cuenta no puede editar incidentes de alertas. Un [incidente de alerta](managing-and-responding-alerts.md) se considera datos de seguridad y es de solo lectura.

## <a name="deleting-personal-data"></a>Eliminar datos personales
Un usuario de Defender for Cloud que tenga asignado el rol Propietario, Colaborador o Administrador de cuenta puede eliminar los [datos de contacto de seguridad](configure-email-notifications.md) a través de Azure Portal.

Un usuario de Defender for Cloud que tenga asignado el rol Propietario, Colaborador o Administrador de cuenta puede eliminar las [directivas Just-In-Time](just-in-time-access-usage.md) a través de Azure Portal.

Un usuario de Defender for Cloud no puede eliminar incidentes de alerta. Por motivos de seguridad, un [incidente de alerta](managing-and-responding-alerts.md) se considera datos de solo lectura.

## <a name="exporting-personal-data"></a>Exportar datos personales
Un usuario de Defender for Cloud que tenga asignado el rol Lector, Propietario, Colaborador o Administrador de cuenta puede exportar los [datos de contacto de seguridad](configure-email-notifications.md) si:

- Copia desde Azure Portal
- Ejecuta la llamada de API de REST de Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/Microsoft.Security/securityContacts?api-version={api-version}
  ```

Un usuario de Defender for Cloud que tenga asignado el rol Administrador de cuenta puede exportar las [directivas Just-In-Time](just-in-time-access-usage.md) que contienen las direcciones IP si:

- Copia desde Azure Portal
- Ejecuta la llamada de API de REST de Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.Security/locations/{location}/jitNetworkAccessPolicies/default?api-version={api-version}
  ```

Un Administrador de cuenta puede exportar los detalles de alerta si:

- Copia desde Azure Portal
- Ejecuta la llamada de API de REST de Azure, GET HTTP:
  ```HTTP
  GET https://<endpoint>/subscriptions/{subscriptionId}/providers/microsoft.Security/alerts?api-version={api-version}
  ```

Para obtener más información, consulte [Obtener alertas de seguridad (colección GET)](/previous-versions/azure/reference/mt704050(v=azure.100)).

## <a name="restricting-the-use-of-personal-data-for-profiling-or-marketing-without-consent"></a>Restricción del uso de datos personales para la generación de perfiles o marketing sin consentimiento
Un usuario de Defender for Cloud puede elegir no participar mediante la eliminación de sus [datos de contacto de seguridad](configure-email-notifications.md).

Los [datos Just-In-Time](just-in-time-access-usage.md) se consideran datos no identificables y se conservan durante un período de 30 días.

Los [datos de alertas](managing-and-responding-alerts.md) se consideran datos de seguridad y se conservan durante un período de dos años.

## <a name="auditing-and-reporting"></a>Informes y auditoría
Los registros de auditoría de contacto de seguridad, Just-In-Time y las actualizaciones de alertas se mantienen en [los registros de actividad de Azure](../azure-monitor/essentials/platform-logs-overview.md).
