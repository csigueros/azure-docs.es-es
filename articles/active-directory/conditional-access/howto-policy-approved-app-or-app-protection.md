---
title: 'Acceso condicional: requerir una aplicación aprobada o una directiva de protección de aplicaciones (Azure Active Directory)'
description: Creación de una directiva de acceso condicional personalizada que requiera una aplicación aprobada o una directiva de protección de aplicaciones
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/08/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, davidspo, spunukol
ms.collection: M365-identity-device-management
ms.openlocfilehash: 071c1e3ae0a2cd1eb352103839b9c32e39902985
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132725369"
---
# <a name="conditional-access-require-approved-client-apps-or-app-protection-policy"></a>Acceso condicional: requerir aplicaciones cliente aprobadas o una directiva de protección de aplicaciones

Los usuarios utilizan sus dispositivos móviles habitualmente para tareas personales y profesionales. A la vez que se aseguran de que el personal pueda ser productivo, las organizaciones también quieren evitar la pérdida de datos de aplicaciones que se encuentren en dispositivos que no administren completamente. 

Con el acceso condicional, las organizaciones pueden restringir el acceso a [aplicaciones cliente aprobadas (con capacidad para la autenticación moderna) con directivas de protección de aplicaciones de Intune](concept-conditional-access-grant.md#require-app-protection-policy). En el caso de las aplicaciones cliente anteriores que puede que no admitan directivas de protección de aplicaciones, los administradores pueden restringir el acceso a las [aplicaciones cliente aprobadas](concept-conditional-access-grant.md#require-approved-client-app).

> [!WARNING]
> Las directivas de protección de aplicaciones solo se admiten en iOS y Android.
>
> No todas las aplicaciones se admiten como aplicaciones aprobadas ni admiten directivas de protección de aplicaciones. Para ver una lista de algunas de las aplicaciones cliente comunes, consulte la sección [Requerir la directiva de protección de aplicaciones](concept-conditional-access-grant.md#require-app-protection-policy). Si la aplicación no se encuentra en la lista, póngase en contacto con su desarrollador.
> 
> Para requerir aplicaciones cliente aprobadas para dispositivos iOS y Android, estos dispositivos deben registrarse primero en Azure AD.

> [!NOTE]
> "Requerir uno de los controles seleccionados" en Conceder controles es como una cláusula OR. Esta cláusula se usa dentro de la directiva para que los usuarios puedan utilizar aplicaciones que admitan los controles de concesión **Requerir directiva de protección de aplicaciones** o **Requerir aplicación cliente aprobada**. **Requerir la directiva de protección de aplicaciones** se aplica cuando la aplicación admite ese control de concesión.

Para más información sobre las ventajas que aporta el uso de directivas de protección de aplicaciones, consulte el artículo [Introducción a las directivas de protección de aplicaciones](/mem/intune/apps/app-protection-policy).

## <a name="template-deployment"></a>Implementación de plantilla

A la hora de implementar esta directiva las organizaciones pueden optar por utilizar los pasos que se describen a continuación o las [plantillas de acceso condicional (versión preliminar)](concept-conditional-access-policy-common.md#conditional-access-templates-preview). 

## <a name="create-a-conditional-access-policy"></a>Creación de una directiva de acceso condicional

Los siguientes pasos le ayudarán a crear una directiva de acceso condicional que requiera una aplicación cliente aprobada **o** una directiva de protección de aplicaciones. Esta directiva funciona conjuntamente con una [directiva de protección de aplicaciones creada en Microsoft Intune](/mem/intune/apps/app-protection-policies).

Esta directiva se coloca en [modo de solo informe](howto-conditional-access-insights-reporting.md) al principio, con el fin de que los administradores puedan determinar el impacto que tendrán en los usuarios existentes. Cuando los administradores se sientan cómodos con que la directiva se aplique según lo previsto, pueden cambiar a **Activado** o ensayar la implementación agregando grupos específicos y excluyendo otros.

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Todos los usuarios**.
   1. En **Excluir**, seleccione **Usuarios y grupos**, y excluya al menos una cuenta para evitar que se le bloquee. Si no excluye ninguna cuenta, no podrá crear la directiva.
   1. Seleccione **Listo**.
1. En **Aplicaciones o acciones en la nube**, seleccione **Todas las aplicaciones en la nube**.
   1. Seleccione **Listo**.
1. En **Condiciones** > **Plataformas de dispositivo**, seleccione **Sí** en **Configurar**.
   1. En **Incluir**, seleccione **Plataformas de dispositivo**.
   1. Elija **Android** e **iOS**.
   1. Seleccione **Listo**.
1. En **Controles de acceso** > **Conceder**, seleccione **Conceder acceso**.
   1. Seleccione **Requerir aplicación cliente aprobada** o **Requerir directiva de protección de aplicaciones**.
   1. **En el caso de controles múltiples**, seleccione **Requerir uno de los controles seleccionados**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Solo informe**.
1. Seleccione **Crear** para crear la directiva.

Después de confirmar la configuración desde el [modo de solo informe](howto-conditional-access-insights-reporting.md), un administrador puede pasar el botón de alternancia **Habilitar directiva** de **Solo informe** a **Activar**.

## <a name="next-steps"></a>Pasos siguientes

[Introducción general a las directivas de protección de aplicaciones](/intune/apps/app-protection-policy)

[Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md)

[Simulación del comportamiento de inicio de sesión mediante la herramienta What If de acceso condicional](troubleshoot-conditional-access-what-if.md)
