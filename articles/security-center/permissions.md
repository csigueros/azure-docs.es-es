---
title: Permisos de Microsoft Defender for Cloud | Microsoft Docs
description: En este artículo se explica cómo usa Microsoft Defender for Cloud el control de acceso basado en rol para asignar permisos a los usuarios e identificar las acciones permitidas de cada rol.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/04/2021
ms.author: memildin
ms.openlocfilehash: 9f22f21f5358dd6cdf798e64727fee510abed105
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131437797"
---
# <a name="permissions-in-microsoft-defender-for-cloud"></a>Permisos de Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Defender for Cloud usa el [control de acceso basado en rol de Azure (RBAC de Azure)](../role-based-access-control/role-assignments-portal.md), que proporciona [roles integrados](../role-based-access-control/built-in-roles.md) que se pueden asignar a usuarios, grupos y servicios de Azure.

Defender for Cloud evalúa la configuración de los recursos para identificar problemas de seguridad y vulnerabilidades. En Defender for Cloud solo se muestra información relacionada con un recurso si se tiene asignado el rol de Propietario, Colaborador o Lector en la suscripción o el grupo de recursos del recurso.

Además de los roles integrados, hay dos roles específicos de Defender for Cloud:

* **Lector de seguridad**: un usuario que pertenece a este rol tiene derechos de visualización en Defender for Cloud. El usuario puede ver las recomendaciones, las alertas, una directiva de seguridad y los estados de seguridad, pero no puede realizar cambios.
* **Administrador de seguridad**: un usuario que pertenece a este rol tiene los mismos derechos que el lector de seguridad, y puede actualizar la directiva de seguridad y descartar las alertas y las recomendaciones.

> [!NOTE]
> Los roles de seguridad, Lector de seguridad y Administrador de seguridad, solo tienen acceso en Defender for Cloud. Los roles de seguridad no tienen acceso a otros servicios de Azure, como Storage, Web y móvil o Internet de las cosas.

## <a name="roles-and-allowed-actions"></a>Roles y acciones permitidas

En la siguiente tabla se muestran los roles y las acciones permitidas en Defender for Cloud.

| **Acción**                                                                                                                      | [Lector de seguridad](../role-based-access-control/built-in-roles.md#security-reader) / <br> [Lector](../role-based-access-control/built-in-roles.md#reader) | [Administrador de seguridad](../role-based-access-control/built-in-roles.md#security-admin) | [Colaborador](../role-based-access-control/built-in-roles.md#contributor) / [Propietario](../role-based-access-control/built-in-roles.md#owner)| [Colaborador](../role-based-access-control/built-in-roles.md#contributor)| [Propietario](../role-based-access-control/built-in-roles.md#owner)|
|:----------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
||||**(Nivel de grupo de recursos)**|**(Nivel de suscripción)**|**(Nivel de suscripción)**|
| Agregar o asignar iniciativas (incluidas las normas de cumplimiento normativo)                                                         | -                             | -              | -                                                      | -                        | ✔                 |
| Editar directivas de seguridad                                                                                                        | -                             | ✔             | -                                                      | -                        | ✔                 |
| Habilitar o deshabilitar planes de Microsoft Defender                                                                                             | -                             | ✔             | -                                                      | -                        | ✔                 |
| Habilitar o deshabilitar el aprovisionamiento automático                                                                                          | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Descartar alertas                                                                                                              | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Aplicar recomendaciones de seguridad en un recurso</br> (y use [Corregir](implement-security-recommendations.md#fix-button)) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Ver alertas y recomendaciones                                                                                             | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |
||||||

> [!NOTE]
> Es recomendable que asigne el rol de menos permisos que los usuarios necesiten para realizar sus tareas. Por ejemplo, asigne el rol Lector a los usuarios que solo necesiten ver información sobre el estado de seguridad de los recursos, pero no llevar a cabo acciones como aplicar recomendaciones o editar directivas.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se ha explicado cómo usa Defender for Cloud RBAC de Azure para asignar permisos a usuarios e identificar las acciones permitidas de cada rol. Ahora que ya está familiarizado con las asignaciones de roles necesarios para supervisar el estado de seguridad de su suscripción, editar directivas de seguridad y aplicar recomendaciones, aprenderá los siguientes conceptos:

- [Establecimiento de directivas de seguridad en Defender for Cloud](tutorial-security-policy.md)
- [Administración de recomendaciones de seguridad en Defender for Cloud](review-security-recommendations.md)
- [Administración de alertas de seguridad y respuesta a ellas en Defender for Cloud](managing-and-responding-alerts.md)
- [Supervisar soluciones de seguridad de asociados](./partner-integration.md)