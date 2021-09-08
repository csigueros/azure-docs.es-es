---
title: 'Validación de reglas de pertenencia dinámica a grupos (versión preliminar): Azure AD | Microsoft Docs'
description: Procedimientos para probar miembros con una regla de pertenencia para grupos dinámicos en Azure Active Directory.
services: active-directory
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.workload: identity
ms.topic: how-to
ms.date: 09/02/2021
ms.author: curtand
ms.reviewer: yukarppa
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: b4d4e87464012d96650a46b93f2bd7d664a5ec03
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426623"
---
# <a name="validate-a-dynamic-group-membership-rule-preview-in-azure-active-directory"></a>Validación de una regla de pertenencia dinámica a grupos (versión preliminar) en Azure Active Directory

Azure Active Directory (Azure AD) ofrece ahora los medios para validar las reglas de pertenencia dinámica a grupos (en versión preliminar pública). En la pestaña **Validación de las reglas**, puede validar la regla dinámica con los miembros del grupo de ejemplo, para confirmar que la regla funciona según lo previsto. Al crear o actualizar reglas de grupos dinámicos, los administradores quieren saber si un usuario o un dispositivo será miembro del grupo. Esto ayuda a evaluar si el usuario o el dispositivo cumplen los criterios de la regla y ayuda a solucionar problemas cuando no se espera la pertenencia.

## <a name="step-by-step-walk-through"></a>Tutorial paso a paso

Para empezar, vaya a **Azure Active Directory** > **Grupos**. Seleccione un grupo dinámico existente o cree un nuevo grupo dinámico y haga clic en Reglas de pertenencia dinámica. A continuación, puede ver la pestaña **Validación de las reglas**.

![Localice la pestaña Validación de las reglas y comience con una regla existente.](./media/groups-dynamic-rule-validation/validate-tab.png)

En la pestaña **Validación de las reglas**, puede seleccionar usuarios para validar la pertenencia a grupos. Se pueden seleccionar 20 usuarios o dispositivos a la vez.

![Incorporación de usuarios para validar la regla existente](./media/groups-dynamic-rule-validation/validate-tab-add-users.png)

Después de elegir los usuarios o dispositivos en el selector y confirmarlos con **Seleccionar**, se iniciará automáticamente la validación y aparecerán sus resultados.

![Consulta de los resultados de la validación de la regla](./media/groups-dynamic-rule-validation/validate-tab-results.png)

Los resultados indican si un usuario es miembro del grupo o no. Si la regla no es válida o hay un problema de red, el resultado se mostrará como **Desconocido**. En este **caso**, el mensaje de error detallado describirá el problema y las acciones necesarias.

![Consulta de los detalles de los resultados de la validación de la regla](./media/groups-dynamic-rule-validation/validate-tab-view-details.png)

Puede modificar la regla; se desencadenará la validación de pertenencias. Para ver por qué el usuario no es miembro del grupo, haga clic en "Ver detalles"; los detalles de verificación mostrarán el resultado de cada una de las expresiones que componen la regla. Haga clic en **Aceptar** para salir.

## <a name="next-steps"></a>Pasos siguientes

- [Reglas de pertenencia dinámica a grupos de Azure Active Directory](groups-dynamic-membership.md)
