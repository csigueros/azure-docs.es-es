---
title: 'Envío de notificaciones por correo electrónico en PIM: Azure Active Directory | Microsoft Docs'
description: Se describen las notificaciones por correo electrónico en Azure AD Privileged Identity Management (PIM).
services: active-directory
documentationcenter: ''
author: curtand
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: pim
ms.date: 06/30/2021
ms.author: curtand
ms.reviewer: hanki
ms.custom: pim
ms.collection: M365-identity-device-management
ms.openlocfilehash: 18aa1e49ad3f87ab3e222f250354c2bc5e2aca16
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121749081"
---
# <a name="email-notifications-in-pim"></a>Notificaciones por correo electrónico en PIM

Privileged Identity Management (PIM) le permite saber cuándo se producen eventos importantes en la organización de Azure Active Directory (Azure AD), como el momento en que se asigna o se activa un rol. Privileged Identity Management le mantiene informado mediante el envío de notificaciones de correo electrónico, a usted y a otros participantes. Estos mensajes de correo electrónico también podrían incluir vínculos a tareas pertinentes, tales como la activación o renovación de un rol. En este artículo se describe el aspecto de estos correos electrónicos, cuándo se envían y quién los recibe.

## <a name="sender-email-address-and-subject-line"></a>Dirección de correo electrónico del remitente y línea de asunto

Los correos electrónicos que se envían desde Privileged Identity Management a Azure AD y los roles de recursos de Azure tienen como remitente la siguiente dirección de correo electrónico:

- Dirección de correo electrónico:  **azure-noreply\@microsoft.com**
- Nombre para mostrar: Microsoft Azure

Estos mensajes de correo electrónico incluyen un prefijo **PIM** en la línea de asunto. Este es un ejemplo:

- PIM: a Alain Charon se le ha asignado el rol de lector de copias de seguridad de forma permanente

## <a name="notifications-for-azure-ad-roles"></a>Notificaciones para roles de Azure AD

Privileged Identity Management envía mensajes de correo electrónico cuando se producen los eventos siguientes para roles de Azure AD:

- Cuando una activación de roles con privilegios está pendiente de aprobación
- Cuando se completa una solicitud de activación de roles con privilegios
- Cuando se habilita Azure AD Privileged Identity Management

El destinatario de estos correos electrónicos para roles de Azure AD depende de su rol, el evento y las notificaciones de configuración:

| Usuario | La activación de roles está pendiente de aprobación | La solicitud de activación de roles está completa | PIM está habilitado |
| --- | --- | --- | --- |
| Administrador de roles con privilegios</br>(Activado/apto) | Sí</br>(solo si no se especifican aprobadores explícitos) | Sí* | Sí |
| Administrador de seguridad</br>(Activado/apto) | No | Sí* | Sí |
| Administrador global</br>(Activado/apto) | No | Sí* | Sí |

\* Si el parámetro [**Notificaciones**](pim-how-to-change-default-settings.md) se establece en **Habilitar**.

Este es un correo electrónico de ejemplo que se envía cuando un usuario activa un rol de Azure AD para la organización ficticia Contoso.

![Nuevo correo electrónico de Privileged Identity Management para roles de Azure AD](./media/pim-email-notifications/email-directory-new.png)

### <a name="weekly-privileged-identity-management-digest-email-for-azure-ad-roles"></a>Correo electrónico de resumen semanal de Privileged Identity Management para roles de Azure AD

Se envía un correo electrónico de resumen semanal de Privileged Identity Management para los roles de Azure AD a los administradores de roles con privilegios, los administradores de seguridad y los administradores globales que han habilitado Privileged Identity Management. Este correo electrónico semanal proporciona una instantánea de las actividades de Privileged Identity Management para la semana, así como las asignaciones de roles con privilegios. Solo está disponible para las organizaciones de Azure AD en la nube pública. Este es un ejemplo de correo electrónico:

![Correo electrónico de resumen semanal de Privileged Identity Management para roles de Azure AD](./media/pim-email-notifications/email-directory-weekly.png)

En el correo electrónico se incluye lo siguiente:

| Icono | Descripción |
| --- | --- |
| **Users activated** (Activaciones de usuarios) | Número de veces que los usuarios activaron su rol apto dentro de la organización. |
| **Users made permanent** (Usuarios convertidos en permanentes) | Número de veces que los usuarios con una asignación apta se hicieron permanentes. |
| **Asignaciones de roles en Privileged Identity Management** | Número de veces que los usuarios recibieron un rol apto dentro de Privileged Identity Management. |
| **Role assignments outside of PIM** (Asignaciones de roles fuera de PIM) | Número de veces que a los usuarios se les asigna un rol permanente fuera de Privileged Identity Management (dentro de Azure AD). Esta alerta y el correo electrónico que la acompaña pueden habilitarse o deshabilitarse abriendo la configuración de alertas. |

En la sección **Información general de los roles principales** figuran los cinco principales roles en la organización según el número total de administradores permanentes y aptos para cada rol. El vínculo **Realizar acción** abre [Detección e información](pim-security-wizard.md) donde puede convertir administradores permanentes en administradores aptos en lotes.

## <a name="email-timing-for-activation-approvals"></a>Temporización del correo electrónico en las aprobaciones de activación

Cuando los usuarios activan su rol y la configuración de este requiere aprobación, los aprobadores recibirán dos correos electrónicos para cada aprobación:

- Solicitud para aprobar o denegar la solicitud de activación del usuario (enviada por el motor de aprobación de solicitudes)
- Se ha aprobado la solicitud del usuario (enviada por el motor de aprobación de solicitudes)

Además, los administradores globales y los administradores de roles con privilegios reciben un correo electrónico para cada aprobación:

- Se ha activado el rol del usuario (enviado por Privileged Identity Management)

Los dos primeros correos electrónicos enviados por el motor de aprobación de solicitudes pueden enviarse con retraso. Actualmente, el 90 % de los mensajes de correo electrónico tardan entre tres y diez minutos, pero en un 1 % de los clientes este tiempo puede alargarse hasta quince minutos.

Si se acepta una solicitud de aprobación en Azure Portal antes de que se envíe el primer correo electrónico, este ya no se desencadenará y los demás aprobadores ya no recibirán por correo electrónico la solicitud de aprobación. Esto podría aparecer como no hubieran recibido un correo electrónico, pero es el comportamiento esperado.

## <a name="pim-emails-for-azure-resource-roles"></a>Correos electrónicos de PIM para roles de recursos de Azure

Privileged Identity Management envía mensajes de correo electrónico a los propietarios y administradores de acceso de usuario cuando se producen los siguientes eventos para los roles de recursos de Azure:

- Cuando una asignación de roles está pendiente de aprobación
- Cuando se asigna un rol
- Cuando un rol está a punto de expirar
- Cuando un rol se puede ampliar
- Cuando un rol se está renovando por un usuario final
- Cuando se completa una solicitud de activación de rol

Privileged Identity Management envía mensajes de correo electrónico a los usuarios finales cuando se producen los siguientes eventos para los roles de recursos de Azure:

- Cuando se asigna un rol al usuario
- Cuando expira un rol del usuario
- Cuando se amplía un rol del usuario
- Cuando se completa una solicitud de activación de rol del usuario

Este es un correo electrónico de ejemplo que se envía cuando se asigna a un usuario un rol de recursos de Azure para la organización ficticia Contoso.

![Nuevo correo electrónico de Privileged Identity Management para roles de recursos de Azure](./media/pim-email-notifications/email-resources-new.png)

## <a name="next-steps"></a>Pasos siguientes

- [Configuración del rol de Azure AD en Privileged Identity Management](pim-how-to-change-default-settings.md)
- [Aprobación o rechazo de solicitudes para los roles de Azure AD en Privileged Identity Management](azure-ad-pim-approval-workflow.md)
