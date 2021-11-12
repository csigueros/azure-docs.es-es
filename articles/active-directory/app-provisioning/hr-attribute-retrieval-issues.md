---
title: Solución de problemas de recuperación de atributos con el aprovisionamiento de RR. HH.
description: Descubra cómo solucionar problemas de recuperación de atributos con el aprovisionamiento de RR. HH.
services: active-directory
author: kenwith
manager: karenh444
ms.service: active-directory
ms.subservice: app-provisioning
ms.topic: troubleshooting
ms.workload: identity
ms.date: 10/27/2021
ms.author: kenwith
ms.reviewer: chmutali
ms.openlocfilehash: f1c0cc4812a3a2770c4b4580d0755bf3766499dd
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478120"
---
# <a name="troubleshoot-hr-attribute-retrieval-issues"></a>Solución de problemas de recuperación de atributos de RR. HH.

## <a name="provisioning-app-is-not-fetching-all-workday-attributes"></a>La aplicación de aprovisionamiento no captura todos los atributos de Workday
**Se aplica a:**
* Aprovisionamiento de usuarios de Workday a Active Directory en el entorno local
* Aprovisionamiento de usuarios de Workday a Azure Active Directory

| | |
|-- | -- |
| **Problema** | Acaba de configurar la aplicación de aprovisionamiento de entrada de Workday y se ha conectado correctamente a la dirección URL del inquilino de Workday. Ha ejecutado una sincronización de prueba y ha observado que la aplicación de aprovisionamiento no recupera todos los atributos de Workday. Solo se leen y se aprovisionan en el destino algunos atributos. |
| **Causa** | De forma predeterminada, la aplicación de aprovisionamiento de Workday se suministra con asignaciones de atributos y definiciones XPATH que funcionan con Workday Web Services (WWS) v21.1. Al configurar la conectividad con Workday en la aplicación de aprovisionamiento, si especificó explícitamente la versión de la API de WWS (por ejemplo, `https://wd3-impl-services1.workday.com/ccx/service/contoso4/Human_Resources/v34.0`), puede encontrarse con este problema debido a la falta de coincidencia entre la versión de la API de WWS y las definiciones de XPATH.  |
| **Resolución** | * *Opción 1*: quite de la dirección URL la información de versión de la API de WWS y use la versión 21.1 predeterminada de la API de WWS. <br> * *Opción 2*: actualice manualmente las expresiones de API XPATH para que sea compatible con la versión de la API de WWS que prefiera. Actualice las **expresiones de API XPATH** en **Asignación de atributos -> Opciones avanzadas -> Edit attribute list for Workday** (Editar lista de atributos para Workday). Para ello, consulte la [referencia de los atributos de Workday](../app-provisioning/workday-attribute-reference.md#xpath-values-for-workday-web-services-wws-api-v30).  |

## <a name="provisioning-app-is-not-fetching-workday-integration-system-attributes--calculated-fields"></a>La aplicación de aprovisionamiento no captura atributos del sistema de integración o campos calculados de Workday
**Se aplica a:**
* Aprovisionamiento de usuarios de Workday a Active Directory en el entorno local
* Aprovisionamiento de usuarios de Workday a Azure Active Directory

| | |
|-- | -- |
| **Problema** | Acaba de configurar la aplicación de aprovisionamiento de entrada de Workday y se ha conectado correctamente a la dirección URL del inquilino de Workday. Tiene un sistema de integración configurado en Workday y ha configurado elementos XPATH que apuntan a atributos del sistema de integración de Workday. Aun así, la aplicación de aprovisionamiento de Azure AD no captura valores asociados a estos atributos del sistema de integración o campos calculados. |
| **Causa** | Esta es una limitación conocida. Actualmente, la aplicación de aprovisionamiento de Workday no admite la captura de campos calculados o atributos del sistema de integración.  |
| **Resolución** | No hay ninguna solución alternativa para esta limitación. |


## <a name="next-steps"></a>Pasos siguientes

* [Más información sobre los escenarios de integración de Azure AD y Workday y las llamadas a servicios web](workday-integration-reference.md)
* [Aprenda a revisar los registros y a obtener informes sobre la actividad de aprovisionamiento](check-status-user-account-provisioning.md)

