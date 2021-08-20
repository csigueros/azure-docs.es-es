---
title: Permisos en Azure Security Center | Microsoft Docs
description: En este artículo se explica cómo Azure Security Center usa el control de acceso basado en roles para asignar permisos a los usuarios e identifica las acciones permitidas para cada rol.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 07/04/2021
ms.author: memildin
ms.openlocfilehash: 50a907625d9ef2db84289165f377b9ebf5f5d56e
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297503"
---
# <a name="permissions-in-azure-security-center"></a>Permisos en Azure Security Center

Security Center usa el [control de acceso basado en rol de Azure (RBAC de Azure)](../role-based-access-control/role-assignments-portal.md), que proporciona [roles integrados](../role-based-access-control/built-in-roles.md) que se pueden asignar a usuarios, grupos y servicios en Azure.

Security Center evalúa la configuración de los recursos para identificar problemas de seguridad y vulnerabilidades. En Security Center, solo se muestra información relacionada con un recurso cuando tiene asignado el rol de Propietario, Colaborador o Lector para la suscripción o el grupo de recursos relacionados con un recurso.

Además de estos roles integrados, hay dos roles específicos de Security Center:

* **Lector de seguridad**: un usuario que pertenece a este rol tiene derechos de visualización en Security Center. El usuario puede ver las recomendaciones, las alertas, una directiva de seguridad y los estados de seguridad, pero no puede realizar cambios.
* **Administrador de seguridad**: un usuario que pertenece a este rol tiene los mismos derechos que el lector de seguridad, y puede actualizar la directiva de seguridad y descartar las alertas y las recomendaciones.

> [!NOTE]
> Los roles de seguridad Lector de seguridad y Administrador de seguridad solo tienen acceso a Security Center. Los roles de seguridad no tienen acceso a otros servicios de Azure, como Storage, Web y móvil o Internet de las cosas.

## <a name="roles-and-allowed-actions"></a>Roles y acciones permitidas

En la siguiente tabla se muestran los roles y las acciones permitidas en Security Center.

| **Acción**                                                                                                                      | [Lector de seguridad](../role-based-access-control/built-in-roles.md#security-reader) / <br> [Lector](../role-based-access-control/built-in-roles.md#reader) | [Administrador de seguridad](../role-based-access-control/built-in-roles.md#security-admin) | [Colaborador](../role-based-access-control/built-in-roles.md#contributor) / [Propietario](../role-based-access-control/built-in-roles.md#owner)| [Colaborador](../role-based-access-control/built-in-roles.md#contributor)| [Propietario](../role-based-access-control/built-in-roles.md#owner)|
|:----------------------------------------------------------------------------------------------------------------------------|:-----------------------------:|:--------------:|:------------------------------------------------------:|:------------------------:|:------------------:|
||||**(Nivel de grupo de recursos)**|**(Nivel de suscripción)**|**(Nivel de suscripción)**|
| Agregar o asignar iniciativas (incluidas las normas de cumplimiento normativo)                                                         | -                             | -              | -                                                      | -                        | ✔                 |
| Editar directivas de seguridad                                                                                                        | -                             | ✔             | -                                                      | -                        | ✔                 |
| Habilitar o deshabilitar planes de Azure Defender                                                                                             | -                             | ✔             | -                                                      | -                        | ✔                 |
| Habilitar o deshabilitar el aprovisionamiento automático                                                                                          | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Descartar alertas                                                                                                              | -                             | ✔             | -                                                      | ✔                       | ✔                  |
| Aplicar recomendaciones de seguridad en un recurso</br> (y use [Corregir](security-center-remediate-recommendations.md#fix-button)) | -                             | -              | ✔                                                     | ✔                        | ✔                 |
| Ver alertas y recomendaciones                                                                                             | ✔                            | ✔              | ✔                                                     | ✔                        | ✔                 |
||||||

> [!NOTE]
> Es recomendable que asigne el rol de menos permisos que los usuarios necesiten para realizar sus tareas. Por ejemplo, asigne el rol Lector a los usuarios que solo necesiten ver información sobre el estado de seguridad de los recursos, pero no llevar a cabo acciones como aplicar recomendaciones o editar directivas.

## <a name="next-steps"></a>Pasos siguientes
En este artículo se ha explicado cómo Security Center usa RBAC de Azure para asignar permisos a los usuarios e identifica las acciones permitidas para cada rol. Ahora que ya está familiarizado con las asignaciones de roles necesarios para supervisar el estado de seguridad de su suscripción, editar directivas de seguridad y aplicar recomendaciones, aprenderá los siguientes conceptos:

- [Establecer directivas de seguridad en Security Center](tutorial-security-policy.md)
- [Administrar recomendaciones de seguridad en Security Center](security-center-recommendations.md)
- [Responder a alertas de seguridad en Security Center](security-center-managing-and-responding-alerts.md)
- [Supervisar soluciones de seguridad de asociados](./security-center-partner-integration.md)