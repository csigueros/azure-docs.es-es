---
title: 'Acceso condicional: Bloquear la autenticación heredada (Azure Active Directory)'
description: Cree una directiva de acceso condicional personalizada para bloquear los protocolos de autenticación heredados.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 11/05/2021
ms.author: joflore
author: MicrosoftGuyJFlo
manager: karenhoran
ms.reviewer: calebb, davidspo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9baa605a8ee97129c589d08e2ea1c3beee95ff83
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132708976"
---
# <a name="conditional-access-block-legacy-authentication"></a>Acceso condicional: Bloquear la autenticación heredada

Debido al aumento del riesgo asociado a los protocolos de autenticación heredados, Microsoft recomienda que las organizaciones bloqueen las solicitudes de autenticación que usan estos protocolos y que exijan la autenticación moderna. Para más información acerca del bloqueo de protocolos de autenticación heredados, consulte el artículo [Procedimientos: Bloqueo de la autenticación heredada en Azure AD con acceso condicional](block-legacy-authentication.md).

## <a name="template-deployment"></a>Implementación de plantilla

A la hora de implementar esta directiva las organizaciones pueden optar por utilizar los pasos que se describen a continuación o las [plantillas de acceso condicional (versión preliminar)](concept-conditional-access-policy-common.md#conditional-access-templates-preview). 

## <a name="create-a-conditional-access-policy"></a>Creación de una directiva de acceso condicional

Los pasos siguientes le ayudarán a crear una directiva de acceso condicional para bloquear las solicitudes de autenticación heredadas. Esta directiva se coloca en [modo de solo informe](howto-conditional-access-insights-reporting.md) para comenzar, de modo que los administradores puedan establecer el impacto que tendrán en los usuarios existentes. Cuando los administradores se sientan cómodos con que la directiva se aplique según lo previsto, pueden cambiar a **Activado** o ensayar la implementación agregando grupos específicos y excluyendo otros.

1. Inicie sesión en **Azure Portal** como administrador global, administrador de seguridad o administrador de acceso condicional.
1. Vaya a **Azure Active Directory** > **Seguridad** > **Acceso condicional**.
1. Seleccione **Nueva directiva**.
1. Asigne un nombre a la directiva. Se recomienda que las organizaciones creen un estándar significativo para los nombres de sus directivas.
1. En **Asignaciones**, seleccione **Usuarios y grupos**.
   1. En **Incluir**, seleccione **Todos los usuarios**.
   1. En **Excluir**, seleccione **Usuarios y grupos** y elija las cuentas que deben mantener la capacidad para usar la autenticación heredada. Excluya al menos una cuenta para evitar que se le bloquee. Si no se excluye ninguna cuenta, no podrá crear esta directiva.
   1. Seleccione **Listo**.
1. En **Aplicaciones o acciones en la nube**, seleccione **Todas las aplicaciones en la nube**.
   1. Seleccione **Listo**.
1. En **Condiciones** > **Aplicaciones cliente**, establezca **Configurar** en **Sí**.
   1. Active solo las casillas **Clientes de Exchange ActiveSync** y **Otros clientes**.
   1. Seleccione **Listo**.
1. En **Controles de acceso** > **Conceder**, seleccione **Bloquear acceso**.
   1. Elija **Seleccionar**.
1. Confirme la configuración y establezca **Habilitar directiva** en **Solo informe**.
1. Seleccione **Crear** para crear la directiva.

Después de confirmar la configuración desde el [modo de solo informe](howto-conditional-access-insights-reporting.md), un administrador puede pasar el botón de alternancia **Habilitar directiva** de **Solo informe** a **Activar**.

> [!NOTE]
> Las directivas de acceso condicional se aplican una vez que se completa la autenticación en una fase. El acceso condicional no pretende ser una primera línea de defensa de una organización en escenarios como los ataques por denegación de servicio (DoS), pero puede usar señales de estos eventos para determinar el acceso.

## <a name="next-steps"></a>Pasos siguientes

[Directivas de acceso condicional habituales](concept-conditional-access-policy-common.md)

[Simulación del comportamiento de inicio de sesión mediante la herramienta What If de acceso condicional](troubleshoot-conditional-access-what-if.md)

[Configuración de una aplicación o un dispositivo multifunción para enviar correos electrónicos mediante Microsoft 365](/exchange/mail-flow-best-practices/how-to-set-up-a-multifunction-device-or-application-to-send-email-using-microsoft-365-or-office-365)
