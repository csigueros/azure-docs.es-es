---
title: Operaciones de seguridad de Azure Active Directory para dispositivos
description: Aprenda a establecer líneas de base y a supervisar dispositivos e informar sobre ellos para identificar posibles riesgos de seguridad con dispositivos.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9298c3c6f7d6c634ed5647a6473e0780cd82bf1d
ms.sourcegitcommit: 9339c4d47a4c7eb3621b5a31384bb0f504951712
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113799439"
---
# <a name="azure-active-directory-security-operations-for-devices"></a>Operaciones de seguridad de Azure Active Directory para dispositivos

Los dispositivos no se usan normalmente como destino en ataques basados en identidades, pero *se pueden* usar para satisfacer y engañar los controles de seguridad, o para suplantar a los usuarios. Los dispositivos pueden tener una de las cuatro relaciones con Azure AD: 

* No registrado

* [Registrado en Azure Active Directory (Azure AD)](../devices/concept-azure-ad-register.md)

* [Azure AD joined](../devices/concept-azure-ad-join.md)

* [Unido a Azure AD híbrido](../devices/concept-azure-ad-join-hybrid.md)   
‎

Los dispositivos registrados y unidos se emiten como [token de actualización principal (PRT),](../devices/concept-primary-refresh-token.md) que se puede usar como artefacto de autenticación principal y, en algunos casos, como artefacto de autenticación multifactor. Los atacantes pueden intentar registrar sus propios dispositivos, usar PRT en dispositivos legítimos para acceder a datos empresariales, robar tokens basados en PRT de dispositivos de usuario legítimos o encontrar configuraciones incorrectas en controles basados en dispositivos en Azure Active Directory. Con dispositivos unidos a Azure AD híbrido, los administradores inician y controlan el proceso de unión, lo que reduce los métodos de ataque disponibles.

Para más información sobre los métodos de integración de dispositivos, consulte [Elección de los métodos de integración](../devices/plan-device-deployment.md) en el artículo [Planeamiento de la implementación de dispositivos de Azure Active Directory.](../devices/plan-device-deployment.md)

Para reducir el riesgo de que los actores no válidos ataquen la infraestructura a través de dispositivos, supervise lo siguiente

* Registro de dispositivos y unión a Azure AD

* Dispositivos no compatibles que acceden a aplicaciones

* Recuperación de claves de BitLocker

* Roles de administrador de dispositivos

* Inicios de sesión en máquinas virtuales

## <a name="where-to-look"></a>Dónde mirar

Los archivos de registro que usa para la investigación y supervisión son: 

* [Registros de auditoría de Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Registros de inicio de sesión](../reports-monitoring/concept-all-sign-ins.md)

* [Registros de auditoría de Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview?view=o365-worldwide.md) 

* [Registros de Azure Key Vault](../..//key-vault/general/logging.md?tabs=Vault)

En Azure Portal, puede ver los registros de auditoría de Azure AD y descargarlos como archivos de valores separados por comas (CSV) o notación de objetos JavaScript (JSON). Azure Portal tiene varias maneras de integrar los registros de Azure AD con otras herramientas que permiten una mayor automatización de la supervisión y las alertas:

* **[Azure Sentinel](../../sentinel/overview.md)** : permite el análisis de seguridad inteligente en el nivel empresarial proporcionando funcionalidades de Administración de eventos e información de seguridad (SIEM). 

* **[Azure Monitor](../..//azure-monitor/overview.md)** : permite la supervisión automatizada y las alertas de diversas condiciones. Puede crear o usar libros para combinar datos de orígenes diferentes.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) (integrado con un Azure AD)** - [: los registros de Azure AD se pueden integrar con otras instancias de SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md), como Splunk, ArcSight, QRadar y Sumo Logic a través de la integración de Azure Event Hubs.

* **[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS)** : le permite detectar y administrar aplicaciones, controlar entre aplicaciones y recursos y comprobar el cumplimiento de las aplicaciones en la nube. 

Gran parte de lo que supervisará y alertará son los efectos de las directivas de acceso condicional. Puede usar el [libro Información detallada e informes del acceso condicional](../conditional-access/howto-conditional-access-insights-reporting.md) para examinar los efectos de una o varias directivas de acceso condicional en los inicios de sesión y los resultados de las directivas, incluido el estado del dispositivo. Este libro le permite ver un resumen de impacto e identificar el impacto durante un período de tiempo específico. También puede usar el libro para investigar los inicios de sesión de un usuario específico. 

 En el resto de este artículo se describe lo que se recomienda supervisar y alertar, y se organiza por el tipo de amenaza. Cuando hay soluciones previamente creadas y específicas, se establecen vínculos a ellas o se proporcionan ejemplos después de la tabla. De lo contrario, puede crear alertas mediante las herramientas anteriores. 

 ## <a name="device-registrations-and-joins-outside-policy"></a>Registros de dispositivos y combinaciones fuera de la directiva

Los dispositivos registrados en Azure AD y unidos a Azure AD poseen tokens de actualización principales (PRT), que son el equivalente de un único factor de autenticación. En ocasiones, estos dispositivos pueden contener notificaciones de autenticación sólida. Para más información sobre cuándo los PRT contienen notificaciones de autenticación segura, vea [¿Qué es un token de actualización principal?](../devices/concept-primary-refresh-token.md) Para evitar que los actores no válidos se registren o se unan a dispositivos, es necesaria la autenticación multifactor (MFA) para registrar o unir dispositivos. A continuación, supervise los dispositivos registrados o unidos sin MFA. También tendrá que estar atento a los cambios en la configuración y las directivas de MFA, así como en las directivas de cumplimiento de dispositivos.

 | Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Registro de dispositivos o unión completados sin MFA| Media| Registros de inicio de sesión| Actividad: autenticación correcta en el servicio de registro de dispositivos. <br>And<br>No se requiere MFA| Alerta cuando: <br>Cualquier dispositivo registrado o unido sin MFA |
| Cambios en el botón de alternancia de MFA de registro de dispositivos en Azure AD| Alto| Registro de auditoría| Actividad: establecimiento de directivas de registro de dispositivos| Buscar: <br>El botón de alternancia establecido en desactivado. No hay ninguna entrada de registro de auditoría. Programación de comprobaciones periódicas. |
| Cambios en las directivas de acceso condicional que requieren un dispositivo compatible o unido a un dominio.| Alto| Registro de auditoría| Cambios en las directivas de CA<br>| Alerta cuando: <br><li> Haya un cambio a cualquier directiva compatible o que requiera unión a un dominio.<li>Haya cambios en ubicaciones de confianza.<li> Se agreguen cuentas o dispositivos a excepciones de directiva de MFA. |


Puede crear una alerta que notifique a los administradores adecuados cuando un dispositivo se registre o una sin MFA mediante Azure Sentinel.

```
Sign-in logs

| where ResourceDisplayName == “Device Registration Service”

| where conditionalAccessStatus ==”success”

| where AuthenticationRequirement <> “multiFactorAuthentication”
```

También puede usar [Microsoft Intune para establecer y supervisar las directivas de cumplimiento de dispositivos](/mem/intune/protect/device-compliance-get-started).

## <a name="non-compliant-device-sign-in"></a>Inicio de sesión de dispositivo no compatible

Es posible que no se pueda bloquear el acceso a todas las aplicaciones de nube y software como servicio con directivas de acceso condicional que requieran dispositivos compatibles. 

La [administración de dispositivos móviles](/windows/client-management/mdm/) (MDM) le ayuda a mantener los dispositivos Windows 10 compatibles. Con Windows versión 1809, lanzamos una [línea base de seguridad](/windows/client-management/mdm/) de directivas. Azure Active Directory se puede [integrar con MDM](/windows/client-management/mdm/azure-active-directory-integration-with-mdm) para exigir el cumplimiento de dispositivos con las directivas corporativas y puede notificar el estado de cumplimiento de un dispositivo. 

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Inicios de sesión por parte de dispositivos no compatibles| Alto| Registros de inicio de sesión| DeviceDetail.isCompliant ==false| Si requiere el inicio de sesión desde dispositivos compatibles, alerte cuando:<br><li> se produzca cualquier inicio de sesión por parte de dispositivos no compatibles.<li> se produzca cualquier acceso sin MFA o una ubicación de confianza.<p>Si trabaja para requerir dispositivos, supervise los inicios de sesión sospechosos. |
| Inicios de sesión por parte de dispositivos desconocidos| Bajo| Registros de inicio de sesión| <li>DeviceDetail está vacío<li>Autenticación de factor único<li>Desde una ubicación que no es de confianza| Buscar: <br><li>se produzca cualquier acceso desde dispositivos fuera de cumplimiento.<li>se produzca cualquier acceso sin MFA o una ubicación de confianza. |


### <a name="use-loganalytics-to-query"></a>Uso de LogAnalytics para consultar

**Inicios de sesión por parte de dispositivos no compatibles**

```
SigninLogs

| where DeviceDetail.isCompliant ==false

| where conditionalAccessStatus == “success”
```
 

**Inicios de sesión por parte de dispositivos desconocidos**

```

SigninLogs
| where isempty(DeviceDetail.deviceId)

| where AuthenticationRequirement == "singleFactorAuthentication"

| where ResultType == "0"

| where NetworkLocationDetails == "[]"
```
 
## <a name="stale-devices"></a>Dispositivos obsoletos

Los dispositivos obsoletos incluyen dispositivos que no han iniciado sesión durante un período de tiempo especificado. Los dispositivos pueden quedar obsoletos cuando un usuario obtiene un nuevo dispositivo o pierde un dispositivo, o cuando un dispositivo unido a Azure AD se borra o se vuelve a aprovisionar. Los dispositivos también pueden permanecer registrados o unidos cuando el usuario ya no está asociado al inquilino. Los dispositivos obsoletos deben quitarse para que no se puedan usar sus tokens de actualización principales (PRT).

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Fecha del último inicio de sesión| Bajo| Graph API| approximateLastSignInDateTime| Use Graph API o PowerShell para identificar y quitar dispositivos obsoletos. |

## <a name="bitlocker-key-retrieval"></a>Recuperación de claves de BitLocker

Los atacantes que han comprometido el dispositivo de un usuario pueden recuperar las claves de [BitLocker](/windows/security/information-protection/bitlocker/bitlocker-device-encryption-overview-windows-10) en Azure AD. No es habitual que los usuarios recuperen claves y deben supervisarse e investigarse.

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Recuperación de claves| Media| Registros de auditoría| OperationName == "Read BitLocker key”| Buscar <br><li>recuperación de claves<li> otro comportamiento anómalo por parte de los usuarios que recuperan claves. |


En LogAnalytics, cree una consulta como

```
AuditLogs

| where OperationName == "Read BitLocker key” 
```

## <a name="device-administrator-roles"></a>Roles de administrador de dispositivos

Los administradores globales y los administradores de dispositivos en la nube obtienen automáticamente derechos de administrador local en todos los dispositivos unidos a Azure AD. Es importante supervisar quién tiene estos derechos para mantener su entorno seguro.

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Usuarios agregados a roles de administrador global o de dispositivo| Alto| Registros de auditoría| Tipo de actividad = Agregar miembro a rol.| Buscar:<li> nuevos usuarios agregados a estos roles de Azure AD.<li> Comportamiento anómalo posterior por máquinas o usuarios. |


## <a name="non-azure-ad-sign-ins-to-virtual-machines"></a>Inicios de sesión que no son de Azure AD en máquinas virtuales

Los inicios de sesión en máquinas virtuales (VM) de Windows o LINUX deben supervisarse para los inicios de sesión de cuentas que no sean cuentas de Azure AD.

### <a name="azure-ad-sign-in-for-linux"></a>Inicio de sesión de Azure AD para LINUX

El inicio de sesión de Azure AD para LINUX permite a las organizaciones iniciar sesión en sus máquinas virtuales LINUX de Azure mediante cuentas de Azure AD a través del protocolo de shell seguro (SSH).

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Inicio de sesión de una cuenta que no es de Azure AD, especialmente a través de SSH| Alto| Registros de autenticación local| Ubuntu:  <br>supervisar /var/log/auth.log para el uso de SSH<br>RedHat: <br>supervisar /var/log/sssd/ para el uso de SSH| Buscar:<li> entradas en [las que las cuentas que no son de Azure AD se conectan correctamente a las máquinas virtuales.](../devices/howto-vm-sign-in-azure-ad-linux.md) <li>Consulte el ejemplo siguiente. |


Ejemplo de Ubuntu:

   9 de mayo 23:49:39 ubuntu1804 aad_certhandler[3915]: Versión: 1.0.015570001; usuario: localusertest01

   9 de mayo 23:49:39 ubuntu1804 aad_certhandler[3915]: El usuario "localusertest01" no es un usuario de AAD; devolviendo resultado vacío.

   9 de mayo 23:49:43 ubuntu1804 aad_certhandler[3916]: Versión: 1.0.015570001; usuario: localusertest01

   9 de mayo 23:49:43 ubuntu1804 aad_certhandler[3916]: El usuario "localusertest01" no es un usuario de AAD; devolviendo resultado vacío.

   9 de mayo 23:49:43 ubuntu1804 sshd[3909]: Aceptado públicamente para localusertest01 desde el puerto 192.168.0.15 53582 ssh2: RSA SHA256:MiROf6f9u1w8J+46AXR1WmPjDhNWJEoXp4HMm9lvJAQ

   9 de mayo 23:49:43 ubuntu1804 sshd[3909]: pam_unix(sshd:session): sesión abierta para el usuario localusertest01 (uid=0).

Puede establecer una directiva para los inicios de sesión de máquinas virtuales LINUX y detectar y marcar las máquinas virtuales Linux que tengan cuentas locales no aprobadas agregadas. Para más información, consulte [Uso de Azure Policy para garantizar estándares y evaluar el cumplimiento](../devices/howto-vm-sign-in-azure-ad-linux.md). 

### <a name="azure-ad-sign-ins-for-windows-server"></a>Inicios de sesión de Azure AD para Windows Server

El inicio de sesión de Azure AD para Windows permite a su organización iniciar sesión en las máquinas virtuales Windows 2019+ de Azure mediante cuentas de Azure AD a través del protocolo de escritorio remoto (RDP).

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Inicio de sesión de una cuenta que no es de Azure AD, especialmente a través de RDP| Alto| Registros de eventos de Windows Server| Inicio de sesión interactivo en máquina virtual Windows| Evento 528, tipo de inicio de sesión 10 (RemoteInteractive).<br>Muestra cuándo un usuario inicia sesión a través de Terminal Services o Escritorio remoto. |


## <a name="next-steps"></a>Pasos siguientes

Consulte estos artículos de la guía de operaciones de seguridad adicionales:

[Información general de operaciones de seguridad de Azure AD](security-operations-introduction.md)

[Operaciones de seguridad para cuentas de usuario](security-operations-user-accounts.md)

[Operaciones de seguridad para cuentas con privilegios](security-operations-privileged-accounts.md)

[Operaciones de seguridad para Privileged Identity Management](security-operations-privileged-identity-management.md)

[Operaciones de seguridad para aplicaciones](security-operations-applications.md)

[Operaciones de seguridad para dispositivos](security-operations-devices.md)

 
[Operaciones de seguridad para infraestructura](security-operations-infrastructure.md)