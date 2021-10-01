---
title: Operaciones de seguridad de Azure Active Directory para infraestructura
description: Obtenga información sobre cómo supervisar y alertar sobre los componentes de infraestructura para detectar amenazas de seguridad.
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
ms.openlocfilehash: e0a3c2b380160ef68655f0727f4d47280cd5cd02
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124754278"
---
# <a name="security-operations-for-infrastructure"></a>Operaciones de seguridad para infraestructura

La infraestructura tiene muchos componentes donde pueden producirse vulnerabilidades si no están configurados correctamente. Como parte de la estrategia de supervisión y alertas para la infraestructura, supervise y alerte los eventos en las áreas siguientes:

* Autenticación y autorización

* Componentes de autenticación híbrida, incluidos los servidores de federación

* Directivas 

* Suscripciones

La supervisión y la alerta de los componentes de la infraestructura de autenticación son fundamentales. Cualquier riesgo individual puede dar lugar a un riesgo total del entorno en su conjunto. Muchas empresas que usan Azure AD funcionan en un entorno de autenticación híbrida. Esto significa que los componentes locales y en la nube deben incluirse en la estrategia de supervisión y alerta. Tener un entorno de autenticación híbrida también introduce otro vector de ataque al entorno.

Se recomienda que todos los componentes se consideren recursos del plano de control o nivel 0, así como las cuentas que se usan para administrarlos. Consulte [Protección de recursos con privilegios](/security/compass/overview) para obtener instrucciones sobre cómo diseñar e implementar el entorno. Esta guía incluye recomendaciones para cada uno de los componentes de autenticación híbrida que podrían usarse para un inquilino de Azure AD.

Un primer paso para poder detectar eventos inesperados y posibles ataques consiste en establecer una base de referencia. Para todos los componentes locales que se enumeran en este artículo, consulte [Implementación de acceso con privilegios](/security/compass/privileged-access-deployment), que forma parte de la guía de Protección de recursos con privilegios.

## <a name="where-to-look"></a>Dónde mirar

Los archivos de registro que usa para la investigación y supervisión son: 

* [Registros de auditoría de Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Registros de inicio de sesión](../reports-monitoring/concept-all-sign-ins.md)

* [Registros de auditoría de Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview) 

* [Registros de Azure Key Vault](../../key-vault/general/logging.md?tabs=Vault)

En Azure Portal, puede ver los registros de auditoría de Azure AD y descargarlos como archivos de valores separados por comas (CSV) o notación de objetos JavaScript (JSON). Azure Portal tiene varias maneras de integrar los registros de Azure AD con otras herramientas que permiten una mayor automatización de la supervisión y las alertas:

* [Azure Sentinel](../../sentinel/overview.md): Permite el análisis de seguridad inteligente en el nivel empresarial proporcionando funcionalidades de Administración de eventos e información de seguridad (SIEM). 

* [Azure Monitor](../../azure-monitor/overview.md): Permite la supervisión automatizada y las alertas de diversas condiciones. Puede crear o usar libros para combinar datos de orígenes diferentes.

* [Azure Event Hubs](../../event-hubs/event-hubs-about.md) (integrado con SIEM): Los [registros de Azure AD se pueden integrar con otras instancias de SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md), como Splunk, ArcSight, QRadar y Sumo Logic, a través de la integración de Azure Event Hubs.

* [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS): Le permite detectar y administrar aplicaciones, controlar entre aplicaciones y recursos, y comprobar el cumplimiento de las aplicaciones en la nube. 

En el resto de este artículo se describe lo que debe supervisar y alertar, y se organiza por el tipo de amenaza. Si hay soluciones creadas previamente específicas, encontrará vínculos a ellas después de la tabla. De lo contrario, puede crear alertas mediante las herramientas anteriores.

## <a name="authentication-infrastructure"></a>Infraestructura de autenticación

En entornos híbridos que contienen recursos y cuentas tanto locales como basados en la nube, la infraestructura de Active Directory es una parte clave de la pila de autenticación. La pila también es objetivo de ataques, por lo que debe configurarse para mantener un entorno seguro y debe supervisarse correctamente. Los ejemplos de tipos de ataque actuales que se usan en la infraestructura de autenticación emplean técnicas de Difusión de contraseña y Solorigate. A continuación se incluyen vínculos a artículos recomendados:

* [Información general sobre la protección del acceso con privilegios](/security/compass/overview): En este artículo se proporciona información general sobre las técnicas actuales que usan Confianza cero para crear y mantener un acceso con privilegios seguro.

* [Actividades de dominio supervisado de Microsoft Defender for Identity](/defender-for-identity/monitored-activities): En este artículo encontrará una lista completa de actividades para supervisar y establecer alertas. 

* [Tutorial de alertas de seguridad de Microsoft Defender for Identity](/defender-for-identity/understanding-security-alerts): En este artículo se dan instrucciones sobre cómo crear e implementar una estrategia de alertas de seguridad.

A continuación se incluyen vínculos a artículos específicos que se centran en la supervisión y las alertas de la infraestructura de autenticación:

* [Rutas de desplazamiento lateral (LMP) de Microsoft Defender for Identity](/defender-for-identity/use-case-lateral-movement-path): En este artículo se describen las técnicas de detección que puede usar para ayudar a detectar cuando se usen cuentas no confidenciales para acceder a cuentas confidenciales en toda su red.

* [Trabajo con alertas de seguridad en Microsoft Defender for Identity](/defender-for-identity/working-with-suspicious-activities): En este artículo se describe cómo revisar y administrar alertas una vez que se registran. 

 A continuación, encontrará aspectos específicos que debe buscar:

| Elementos para supervisar| Nivel de riesgo| Where| Notas |
| - | - | - | - |
| Tendencias de bloqueo de extranet| Alto| Azure AD Connect Health| Use la información de [Supervisión de AD FS mediante Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md) para conocer herramientas y técnicas que ayudan a detectar tendencias de bloqueo de extranet. |
| Inicios de sesión con error|Alto | Portal de Connect Health| Exporte o descargue el informe de IP de riesgo y siga las instrucciones de [Informe de direcciones IP de riesgo (versión preliminar pública)](../hybrid/how-to-connect-health-adfs-risky-ip.md) para conocer los pasos siguientes. |
| Cumple con los requisitos de privacidad| Bajo| Azure AD Connect Health| Configure Azure AD Conectar Health para deshabilitar las colecciones de datos y la supervisión según el artículo [Privacidad del usuario y Azure AD Connect Health](../hybrid/reference-connect-health-user-privacy.md). |
| Posible ataque por fuerza bruta en LDAP| Media| Microsoft Defender for Identity| Use el sensor para ayudar a detectar posibles ataques por fuerza bruta contra LDAP. |
| Reconocimiento de enumeración de cuentas| Media| Microsoft Defender for Identity| Use el sensor para ayudar a realizar el reconocimiento de enumeración de cuentas. |
| Correlación general entre Azure AD y Azure AD FS|Media | Microsoft Defender for Identity| Use funcionalidades para correlacionar las actividades entre los entornos de Azure AD y Azure AD FS. |


 

### <a name="pass-through-authentication-monitoring"></a>Supervisión de la autenticación transferida 

Con la autenticación transferida de Azure Active Directory (Azure AD), los usuarios inician sesión mediante la validación de sus contraseñas directamente en la instancia de Active Directory local. 

A continuación, encontrará aspectos específicos que debe buscar:

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - | - | - | - | - |
| Errores de autenticación transferida de Azure AD|Media | Registros de aplicaciones y servicios\Microsoft\AzureAdConnect\AuthenticationAgent\Admin| AADSTS80001: No es posible conectarse a Active Directory.| Asegúrese de que los servidores del agente sean miembros del mismo bosque de AD que los usuarios cuyas contraseñas haya que validar y que pueden conectarse a Active Directory. |
| Errores de autenticación transferida de Azure AD| Media| Registros de aplicaciones y servicios\Microsoft\AzureAdConnect\AuthenticationAgent\Admin| AADSTS8002: Se ha agotado el tiempo de espera al conectarse a Active Directory.| Asegúrese de que Active Directory está disponible y responde a las solicitudes de los agentes. |
| Errores de autenticación transferida de Azure AD|Media | Registros de aplicaciones y servicios\Microsoft\AzureAdConnect\AuthenticationAgent\Admin| AADSTS80004: El nombre de usuario transferido al agente no era válido.| Asegúrese de que el usuario esté intentando iniciar sesión con el nombre de usuario correcto. |
| Errores de autenticación transferida de Azure AD|Media | Registros de aplicaciones y servicios\Microsoft\AzureAdConnect\AuthenticationAgent\Admin| AADSTS80005: La validación encontró una excepción WebException impredecible.| Se trata de un error transitorio. Vuelva a intentarlo. Si el error no desaparece, póngase en contacto con el soporte técnico de Microsoft. |
| Errores de autenticación transferida de Azure AD| Media| Registros de aplicaciones y servicios\Microsoft\AzureAdConnect\AuthenticationAgent\Admin| AADSTS80007: Error al establecer comunicación con Active Directory.| Compruebe los registros del agente para más información y verifique que Active Directory está funcionando según lo previsto. |
| Errores de autenticación transferida de Azure AD|Alto | API de la función LogonUserA de Win32| Eventos de inicio de sesión 4624(s): Una cuenta ha iniciado sesión correctamente.<br>—correlacionar con—<br>4625(F): No se pudo iniciar sesión en una cuenta.| Use con los nombres de usuario sospechosos en el controlador de dominio que autentica las solicitudes. Instrucciones en [Función LogonUserA (winbase.h)](/windows/win32/api/winbase/nf-winbase-logonusera) |
| Errores de autenticación transferida de Azure AD| Media| Script de PowerShell del controlador de dominio| Consulte Query en la tabla siguiente. | Use la información de [Solución de problemas de autenticación transferida de Azure Active Directory](../hybrid/tshoot-connect-pass-through-authentication.md) para obtener instrucciones adicionales. |

```Kusto

<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ProcessName'] and (Data='C:\Program Files\Microsoft Azure AD Connect Authentication Agent\AzureADConnectAuthenticationAgentService.exe')]]</Select>

</Query>

</QueryList>
```

### <a name="appproxy-connector"></a>Conector AppProxy

Azure AD y Application Proxy de Azure AD ofrecen a los usuarios remotos una experiencia de inicio de sesión único (SSO). Los usuarios se conectan de forma segura a las aplicaciones locales sin una red privada virtual (VPN) ni servidores de doble conexión ni reglas de firewall. Si el servidor del conector Application Proxy de Azure AD está en peligro, los atacantes podrían modificar la experiencia de SSO o cambiar el acceso a las aplicaciones publicadas. 

Para configurar la supervisión de Application Proxy, consulte [Solución de problemas y mensajes de error de Application Proxy](../app-proxy/application-proxy-troubleshoot.md). El archivo de datos que registra la información se puede encontrar en Registros de aplicaciones y servicios\Microsoft\AadApplicationProxy\Connector\Admin. Para obtener una guía de referencia completa de la actividad de auditoría, consulte [Referencia sobre actividades de auditoría de Azure AD](../reports-monitoring/reference-audit-activities.md). Aspectos específicos que supervisar:

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - | - | - | - | - |
| Errores de Kerberos| Media | Varias herramientas| Media | Guía de errores de autenticación Kerberos en Errores de Kerberos en [Solución de problemas y mensajes de error de Application Proxy](../app-proxy/application-proxy-troubleshoot.md). |
| Problemas de seguridad de DC| Alto| Registros de auditoría de seguridad de DC| Id. de evento 4742(S): Se cambió una cuenta de equipo<br>- y -<br>Marca: de confianza para delegación<br>O bien<br>Marca: de confianza para autenticarse para delegación| Investigue todo cambio de marca. |
| Ataques similares a pass-the-ticket| Alto| | | Siga las instrucciones en:<li>[Reconocimiento de entidad de seguridad (LDAP) (identificador externo 2038)](/defender-for-identity/reconnaissance-alerts)<li>[Tutorial: Alertas de credenciales en peligro](/defender-for-identity/compromised-credentials-alerts)<li> [Comprensión y uso de las rutas de desplazamiento lateral con Microsoft Defender for Identity](/defender-for-identity/use-case-lateral-movement-path)<li> [Reconocimiento de los perfiles de entidades](/defender-for-identity/entity-profiles) |


### <a name="legacy-authentication-settings"></a>Configuración de la autenticación heredada

Para que la autenticación multifactor (MFA) sea efectiva, también es necesario bloquear la autenticación heredada. Luego tiene que supervisar el entorno y alertar sobre cualquier uso de la autenticación heredada. Esto se debe a que los protocolos de autenticación heredados, como POP, SMTP, IMAP y MAPI, no pueden aplicar MFA. Esto convierte estos protocolos en los puntos de entrada preferidos de los atacantes de su organización Para más información sobre las herramientas que puede usar para bloquear la autenticación heredada, consulte [Nuevas herramientas para bloquear la autenticación heredada en su organización](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/new-tools-to-block-legacy-authentication-in-your-organization/ba-p/1225302). 

La autenticación heredada se captura en el registro de inicios de sesión de Azure AD como parte de los detalles del evento. Puede usar el libro de Azure Monitor para ayudar a detectar el uso de la autenticación heredada. Para más información, consulte [Inicios de sesión que utilizan una autenticación heredada](../reports-monitoring/howto-use-azure-monitor-workbooks.md), que forma parte de [Uso de los libros de Azure Monitor en informes de Azure Active Directory](../reports-monitoring/howto-use-azure-monitor-workbooks.md). También puede usar el libro Protocolos no seguros para Azure Sentinel. Para más información, consulte [Guía de implementación del libro de protocolos no seguros de Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/azure-sentinel-insecure-protocols-workbook-implementation-guide/ba-p/1197564). Entre las actividades específicas que se deben supervisar se incluyen:

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - | - | - | - | - |
| Autenticaciones heredadas|Alto | Registro de inicios de sesión de Azure AD| ClientApp: POP<br>ClientApp: IMAP<br>ClientApp: MAPI<br>ClientApp: SMTP<br>ClientApp: ActiveSync en EXO<br>Otros clientes = SharePoint y EWS| En entornos de dominio federados, las autenticaciones con errores no se registran, por lo que no aparecerán en el registro. |


## <a name="azure-ad-connect"></a>Azure AD Connect

Azure AD Conectar brinda una ubicación centralizada que permite la sincronización de cuentas y atributos entre el entorno local y el entorno de Azure AD basado en la nube. Azure AD Connect es la herramienta de Microsoft diseñada para satisfacer y lograr sus objetivos de identidad híbrida. Ofrece las siguientes características:

* [Sincronización de hash de contraseña](../hybrid/whatis-phs.md): Un método de inicio de sesión que sincroniza el hash de la contraseña de un usuario de AD local con Azure AD.

* [Sincronización](../hybrid/how-to-connect-sync-whatis.md): responsable de la creación de usuarios, grupos y otros objetos. También de asegurar que la información de identidad de los usuarios y los grupos de su entorno local coincide con la de la nube. Esta sincronización también incluye los códigos hash de contraseña.

* [Seguimiento de estado](../hybrid/whatis-azure-ad-connect.md) Azure AD Connect Health puede proporcionar una sólida supervisión y una ubicación central en Azure Portal donde se puede ver esta actividad.

La sincronización de la identidad entre el entorno local y el entorno en la nube presenta una nueva superficie de ataque para el entorno local y el basado en la nube. Es recomendable que:

* Los servidores de Azure AD Connect principal y de almacenamiento provisional se traten como sistemas de nivel 0 en el plano de control. 

* Siga un conjunto estándar de directivas que rigen cada tipo de cuenta y su uso en su entorno.

*  Instale Azure AD Connect y Connect Health. Ofrecen principalmente datos operativos para el entorno. 

El registro de las operaciones de Azure AD Connect se produce de maneras diferentes:

* El asistente de Azure AD Connect registra los datos en \ProgramData\AADConnect. Cada vez que se invoca al asistente, se crea un archivo de registro de seguimiento con marca de tiempo. El registro de seguimiento se puede importar en Sentinel u otras herramientas de Administración de eventos e información de seguridad<sup data-htmlnode=""></sup> (SIEM) de terceros para su análisis.

* Algunas operaciones inician un script de PowerShell para capturar información de registro. Para recopilar estos datos, debe asegurarse de que el registro de bloques de script esté habilitado.

### <a name="monitoring-configuration-changes"></a>Cambios en la configuración de supervisión

Azure AD usa el motor de datos de Microsoft SQL Server o SQL para almacenar información de configuración de Azure AD Connect. Por lo tanto, la supervisión y la auditoría de los archivos de registro asociados a la configuración deben incluirse en la estrategia de supervisión y auditoría. En concreto, incluya las siguientes tablas en la estrategia de supervisión y alertas.

| Elementos para supervisar| Where| Notas |
| - | - | - |
| mms_management_agent| Registros de auditoría del servicio SQL| Consulte [Registros de SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_partition| Registros de auditoría del servicio SQL| Consulte [Registros de SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_run_profile| Registros de auditoría del servicio SQL| Consulte [Registros de SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_server_configuration| Registros de auditoría del servicio SQL| Consulte [Registros de SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records) |
| mms_synchronization_rule| Registros de auditoría del servicio SQL| Consulte [Registros de SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records) |


Para obtener información sobre qué y cómo supervisar la información de configuración, consulte:

* Para SQL Server, consulte [Registros de SQL Server Audit](/sql/relational-databases/security/auditing/sql-server-audit-records).

* Para Azure Sentinel, consulte [Conexión a servidores Windows para recopilar eventos de seguridad](/sql/relational-databases/security/auditing/sql-server-audit-records). 

* Para obtener información sobre cómo configurar y usar Azure AD Connect, consulte [¿Qué es Azure AD Connect?](../hybrid/whatis-azure-ad-connect.md)

### <a name="monitoring-and-troubleshooting-synchronization"></a>Supervisión y solución de problemas de sincronización

 Una función de Azure AD Connect es sincronizar la sincronización de hash entre la contraseña local de un usuario y Azure AD. Si las contraseñas no se sincronizan según lo esperado, la sincronización puede afectar a la totalidad o parte de los usuarios. Use lo siguiente para ayudar a comprobar el funcionamiento correcto o para solucionar problemas:

* Información para comprobar y solucionar problemas de sincronización de hash, consulte [Solución de problemas de sincronización de hash de contraseñas con la sincronización de Azure AD Connect](../hybrid/tshoot-connect-password-hash-synchronization.md). 

* Modificaciones en los espacios del conector, consulte [Solución de problemas de objetos y atributos de Azure AD Connect](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes). 

**Recursos importantes para la supervisión**

| Elementos para supervisar | Recursos |
| - | - |
| Validación de la sincronización de hash|Consulte, [Solución de problemas de sincronización de hash de contraseña con la sincronización de Azure AD Connect](../hybrid/tshoot-connect-password-hash-synchronization.md). |
 Modificaciones en los espacios del conector|Consulte [Solución de problemas de objetos y atributos de Azure AD Connect](/troubleshoot/azure/active-directory/troubleshoot-aad-connect-objects-attributes). |
| Modificaciones en las reglas que configuró| En concreto, supervise los cambios de filtrado, los cambios de dominio y unidad organizativa, los cambios de atributo y los cambios basados en grupos. |
| Cambios de SQL y MSDE | Cambios en los parámetros de registro y adición de funciones personalizadas. |

**Supervise lo siguiente**: 

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - | - | - | - | - |
| Cambios de Scheduler|Alto | PowerShell| Set-ADSyncScheduler| Buscar modificaciones en la programación |
| Cambios en las tareas programadas| Alto | Registros de auditoría de Azure AD| Actividad = 4699(S): se eliminó una tarea programada<br>O bien<br>Actividad = 4701(s): se deshabilitó una tarea programada<br>O bien<br>Actividad = 4701(s): se actualizó una tarea programada| Supervisar todo |



* Para más información sobre el registro de operaciones de script de PowerShell, consulte [Habilitación del registro de bloques de script](/powershell/module/microsoft.powershell.core/about/about_logging_windows), que forma parte de la documentación de referencia de PowerShell.

* Para más información sobre cómo configurar el registro de PowerShell para su análisis por parte de Splunk, consulte [Get Data into Splunk User Behavior Analytics](https://docs.splunk.com/Documentation/UBA/5.0.4.1/GetDataIn/AddPowerShell) (Obtención de datos en el análisis de comportamiento de usuarios de Splunk).

### <a name="monitoring-seamless-single-sign-on"></a>Supervisión del inicio de sesión único de conexión directa

El inicio de sesión único de conexión directa (SSO de conexión directa) de Azure Active Directory (Azure AD) permite iniciar sesión automáticamente a los usuarios en equipos de escritorio corporativos conectados a la red de la empresa. El inicio de sesión único de conexión directa proporciona a los usuarios un acceso sencillo a las aplicaciones en la nube sin necesidad de otros componentes locales. El inicio de sesión único usa las funcionalidades de autenticación transferida y sincronización de hash de contraseña proporcionadas por Azure AD Connect.

La supervisión de la actividad de inicio de sesión único y de Kerberos puede ayudarle a detectar patrones generales de ataque de robo de credenciales. Use la siguiente información para la supervisión:

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - | - | - | - | - |
| Errores asociados a errores de validación de inicio de sesión único y Kerberos|Media | Registro de inicios de sesión de Azure AD| | Lista de códigos de error de inicio de sesión único en [Inicio de sesión único](../hybrid/tshoot-connect-sso.md). |
| Consulta sobre solución de problemas|Media | PowerShell| Consulte Query en la tabla siguiente. Compruebe cada bosque con inicio de sesión único habilitado.| Compruebe cada bosque con inicio de sesión único habilitado. |
| Eventos relacionados con Kerberos|Alto | Supervisión de Microsoft Defender for Identity| | Revise la guía disponible en [Rutas de desplazamiento lateral (LMP) de Microsoft Defender for Identity](/defender-for-identity/use-case-lateral-movement-path). |

```kusto
<QueryList>

<Query Id="0" Path="Security">

<Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>

</Query>

</QueryList>
```
## <a name="password-protection-policies"></a>Directivas de protección con contraseña

Si implementa Protección con contraseña de Azure AD, la supervisión y la notificación son tareas fundamentales. En los vínculos siguientes se ofrece información detallada para ayudarle a conocer las distintas técnicas de supervisión, además de explicar dónde registra cada servicio la información y cómo envía notificaciones sobre el uso de la protección con contraseña de Azure AD. 

Los servicios del agente de controlador de dominio (DC) y de proxy registran mensajes en el registro de eventos. Todos los cmdlets de PowerShell que se describe a continuación solo están disponibles en el servidor proxy (consulte el módulo de PowerShell AzureADPasswordProtection). El software del agente de controlador de dominio no instala ningún módulo de PowerShell.

Puede encontrar información detallada para planear e implementar la protección con contraseña local en [Planeación e implementación de la protección con contraseña de Azure Active Directory local](../authentication/howto-password-ban-bad-on-premises-deploy.md). Para detalles de supervisión, consulte [Supervisión de protección con contraseña de Azure AD local](../authentication/howto-password-ban-bad-on-premises-monitor.md). En cada controlador de dominio, el software del servicio del agente de DC escribe los resultados de cada operación de validación de contraseña individual (y otros estados) en el registro de eventos local siguiente:

* \Registros de aplicaciones y servicios\Microsoft\AzureADPasswordProtection\DCAgent\Admin

* \Registros de aplicaciones y servicios\Microsoft\AzureADPasswordProtection\DCAgent\Operational

* \Registros de aplicaciones y servicios\Microsoft\AzureADPasswordProtection\DCAgent\Trace

El registro de administración del agente de controlador de dominio es la principal fuente de información de cómo se comporta el software. De manera predeterminada, el registro de seguimiento está desactivado y debe habilitarse antes de que se registren los datos. Para solucionar los problemas de proxy de aplicación y los mensajes de error, puede encontrar información detallada en [Solución de problemas Application Proxy de Azure Active Directory](../app-proxy/application-proxy-troubleshoot.md). La información de estos eventos se registra en:

* Registros de aplicaciones y servicios\Microsoft\AadApplicationProxy\Connector\Admin

* Registro de auditoría de Azure AD, categoría Application Proxy

La referencia completa para las actividades de auditoría de Azure AD está disponible en [Referencia sobre actividades de auditoría Azure Active Directory (Azure AD)](../reports-monitoring/reference-audit-activities.md). 

## <a name="next-steps"></a>Pasos siguientes


Consulte estos artículos de la guía de operaciones de seguridad adicionales:

[Información general de operaciones de seguridad de Azure AD](security-operations-introduction.md)

[Operaciones de seguridad para cuentas de usuario](security-operations-user-accounts.md)

[Operaciones de seguridad para cuentas con privilegios](security-operations-privileged-accounts.md)

[Operaciones de seguridad para Privileged Identity Management](security-operations-privileged-identity-management.md)

[Operaciones de seguridad para aplicaciones](security-operations-applications.md)

[Operaciones de seguridad para dispositivos](security-operations-devices.md)
 
[Operaciones de seguridad para infraestructura](security-operations-infrastructure.md)


 

  
‎
