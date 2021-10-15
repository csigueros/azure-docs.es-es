---
title: Solución de problemas de bloqueo de cuentas en Azure AD Domain Services | Microsoft Docs
description: Aprenda a solucionar problemas comunes que hacen que las cuentas de usuario se bloqueen en Azure Active Directory Domain Services.
services: active-directory-ds
author: justinha
manager: daveba
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: troubleshooting
ms.date: 07/06/2020
ms.author: justinha
ms.openlocfilehash: 487befb0e0d48a1ccf61a38af29c17c596fa70b8
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129350081"
---
# <a name="troubleshoot-account-lockout-problems-with-an-azure-active-directory-domain-services-managed-domain"></a>Solucionar problemas de bloqueo de cuenta con un dominio administrado Azure Active Directory Domain Services

Para evitar que se repitan intentos de inicio de sesión malintencionados, un dominio administrado de Azure Active Directory Domain Services (Azure AD DS) bloquea las cuentas después de un umbral establecido. Este bloqueo de cuentas también puede producirse por accidente, sin que se haya producido un ataque de inicio de sesión. Por ejemplo, si un usuario escribe repetidamente una contraseña incorrecta o un servicio intenta usar una contraseña obsoleta, la cuenta se bloquea.

En este artículo de solución de problemas se explica por qué se bloquean las cuentas, cómo puede configurar este comportamiento y cómo puede consultar las auditorías de seguridad para solucionar problemas de eventos de bloqueo.

## <a name="what-is-an-account-lockout"></a>¿Qué es un bloqueo de cuenta?

Una cuenta de usuario en un dominio administrado de Azure AD DS se bloquea cuando alcanza un umbral establecido para los intentos de inicio de sesión incorrectos. Este comportamiento está diseñado para protegerle en caso de que se intente iniciar sesión por fuerza bruta repetidamente, lo que podría ser un síntoma de un ataque digital automatizado.

**De forma predeterminada, si hay cinco intentos de contraseña incorrectos en un plazo de 2 minutos, la cuenta se bloqueará durante 30 minutos.**

Los umbrales predeterminados de bloqueo de cuentas se configuran mediante una directiva de contraseñas específica. Si tiene un conjunto concreto de requisitos, puede invalidar estos umbrales predeterminados. Sin embargo, no se recomienda aumentar los límites de los umbrales para intentar reducir el número de bloqueos de cuenta. Primero, solucione los problemas relacionados con el origen del comportamiento de bloqueo de cuentas.

### <a name="fine-grained-password-policy"></a>Directiva de contraseñas específica personalizada

Las directivas de contraseñas específicas (FGPP) le permiten aplicar restricciones específicas de directivas de bloqueo de cuentas y contraseñas a diferentes usuarios de un dominio. FGPP solo afecta a los usuarios de un dominio administrado. Los usuarios de la nube y los usuarios del dominio sincronizados en el dominio administrado desde Azure AD solo se ven afectados por las directivas de contraseña dentro del dominio administrado. Sus cuentas en Azure AD o en un directorio local no se ven afectadas.

Las directivas se distribuyen a través de la asociación de grupos en el dominio administrado y los cambios que realice se aplicarán en el siguiente inicio de sesión de usuario. Al cambiar la directiva, no se desbloquea una cuenta de usuario que ya esté bloqueada.

Para obtener más información acerca de las directivas de contraseña específicas y las diferencias entre los usuarios creados directamente en Azure AD DS y los sincronizados desde Azure AD, consulte [Configuración de las directivas de bloqueo de cuenta y contraseña][configure-fgpp].

## <a name="common-account-lockout-reasons"></a>Causas frecuentes del bloqueo de cuentas

Los motivos más frecuentes por los que una cuenta se bloquea sin que entren en juego factores o elementos malintencionados, son los siguientes:

* **Es el propio usuario el que produce el bloqueo.**
    * Hace poco que se cambió la contraseña y el usuario sigue utilizando la antigua. La directiva predeterminada puede bloquear la cuenta porque el usuario ha intentando iniciar sesión cinco veces con la contraseña antigua en un plazo de 2 minutos.
* **Hay una aplicación o servicio que tiene una contraseña antigua.**
    * Si las aplicaciones o los servicios usan una cuenta, es posible que estos recursos intenten iniciar sesión varias veces con una contraseña antigua. Este comportamiento provoca el bloqueo de la cuenta.
    * Intente minimizar el uso de la cuenta entre diferentes aplicaciones o servicios y mantenga un registro de dónde se usan las credenciales. Si cambia la contraseña de una cuenta, actualice las aplicaciones o servicios asociados.
* **La contraseña se modificó en otro entorno y la nueva contraseña todavía no se ha sincronizado.**
    * Si se cambia la contraseña de una cuenta fuera del dominio administrado, por ejemplo, en un entorno de AD DS local, el cambio de contraseña puede tardar unos minutos en sincronizarse a través de Azure AD y en el dominio administrado.
    * Si un usuario intenta iniciar sesión en un recurso del dominio administrado antes de que se complete el proceso de sincronización de contraseñas hará que se bloquee la cuenta.

## <a name="troubleshoot-account-lockouts-with-security-audits"></a>Solución de problemas de bloqueos de cuentas con auditorías de seguridad

Para solucionar problemas cuando se bloquee una cuenta y saber de dónde proceden los eventos de bloqueo, [habilite las auditorías de seguridad en Azure AD DS][security-audit-events]. Los eventos de auditoría solo se capturan desde el momento en que se habilita esta característica. Lo ideal sería que las auditorías de seguridad se habilitaran *antes* de que una cuenta se bloqueara. Si un usuario tiene repetidamente problemas con el bloqueo de cuentas, se pueden habilitar las auditorías de seguridad por si vuelve a ocurrir.

En las siguientes consultas de ejemplo, puede ver cómo puede consultar los *eventos de bloqueo de cuentas* con el código *4740* una vez que las auditoría de seguridad estén habilitadas.

Vea todos los eventos de bloqueo de cuentas de los últimos siete días:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
```

Vea todos los eventos de bloqueo de los últimos siete días de una cuenta llamada *driley*.

```Kusto
AADDomainServicesAccountLogon
| where TimeGenerated >= ago(7d)
| where OperationName has "4740"
| where "driley" == tolower(extract("Logon Account:\t(.+[0-9A-Za-z])",1,tostring(ResultDescription)))
```

Ver todos los eventos de bloqueo de cuenta entre el 26 de junio de 2020 a las 9:00 a. m. y el 1 de julio de 2020 a la medianoche, ordenados de forma ascendente por la fecha y la hora:

```Kusto
AADDomainServicesAccountManagement
| where TimeGenerated >= datetime(2020-06-26 09:00) and TimeGenerated <= datetime(2020-07-01)
| where OperationName has "4740"
| sort by TimeGenerated asc
```

**Note**

En los eventos 4776 y 4740, puede encontrar los detalles del valor "Source Workstation: " (Estación de trabajo de origen: " vacío. Esto se debe a que se introdujo una contraseña incorrecta a través de un inicio de sesión de red a través de otros dispositivos.
Por ejemplo: si tiene un servidor RADIUS, que puede reenviar la autenticación a AAD DS. Para confirmar la habilitación de RDP en el back-end del controlador de dominio, configure los registros de Netlogon.

03/04 19:07:29 [LOGON] [10752] contoso: SamLogon: Transitive Network logon of contoso\Nagappan.Veerappan from  (via LOB11-RADIUS) Entered 

03/04 19:07:29 [LOGON] [10752] contoso: SamLogon: Transitive Network logon of contoso\Nagappan.Veerappan from  (via LOB11-RADIUS) Returns 0xC000006A

03/04 19:07:35 [LOGON] [10753] contoso: SamLogon: Transitive Network logon of contoso\Nagappan.Veerappan from  (via LOB11-RADIUS) Entered 

03/04 19:07:35 [LOGON] [10753] contoso: SamLogon: Transitive Network logon of contoso\Nagappan.Veerappan from  (via LOB11-RADIUS) Returns 0xC000006A

Habilite RDP en los controladores de dominio del grupo de seguridad de red para el back-end para configurar la captura de diagnósticos (es decir, netlogon).
[Reglas de seguridad de entrada](alert-nsg.md#inbound-security-rules)

Si ya ha modificado el NSG predeterminado, siga estos pasos: [Puerto 3389: administración mediante escritorio remoto](network-considerations.md#port-3389---management-using-remote-desktop).

Para habilitar el registro de Netlogon en cualquier servidor, siga estos pasos: [Habilitación del registro de depuración para el servicio Netlogon](/troubleshoot/windows-client/windows-security/enable-debug-logging-netlogon-service).

## <a name="next-steps"></a>Pasos siguientes

Si necesita más información sobre las directivas de contraseñas específicas para ajustar los umbrales de bloqueo de cuentas, consulte [Configuración de directivas de contraseñas y bloqueo de cuentas][configure-fgpp].

Si sigue teniendo problemas para unir la máquina virtual al dominio administrado, [busque ayuda y abra una incidencia de soporte técnico para Azure Active Directory][azure-ad-support].

<!-- INTERNAL LINKS -->
[configure-fgpp]: password-policy.md
[security-audit-events]: security-audit-events.md
[azure-ad-support]: ../active-directory/fundamentals/active-directory-troubleshooting-support-howto.md
