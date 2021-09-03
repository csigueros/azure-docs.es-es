---
title: Operaciones de seguridad de Azure Active Directory para cuentas con privilegios
description: Aprenda a establecer líneas de base y, a continuación, supervise y notifique posibles problemas de seguridad con cuentas con privilegios en Azure Active Directory.
services: active-directory
author: BarbaraSelden
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 07/15/2021
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1944e9f0e8d1ad7a447d12c99aa65a22ccb233d6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741708"
---
# <a name="security-operations-for-privileged-accounts"></a>Operaciones de seguridad para cuentas con privilegios

La seguridad de los recursos empresariales depende de la integridad de las cuentas con privilegios que administran los sistemas de TI. Los atacantes cibernéticos usan ataques de robo de credenciales y otros medios dirigidos a las cuentas con privilegios para acceder a datos confidenciales.

Tradicionalmente, la seguridad de la organización se ha centrado en los puntos de entrada y salida de una red, como el perímetro de seguridad. Sin embargo, las aplicaciones de software como servicio (SaaS) y los dispositivos personales en Internet han hecho que este enfoque sea menos eficaz. 

Azure Active Directory (Azure AD) usa la administración de identidades y acceso (IAM) como el plano de control. En la capa de identidad de la organización, los usuarios que tienen asignados roles administrativos con privilegios tienen el control. Las cuentas usadas para el acceso deben estar protegidas, independientemente de que se trate de un entorno local, en la nube o híbrido.

Usted es totalmente responsable de todas las capas de seguridad de su entorno de TI local. Cuando se usan servicios de Azure, la prevención y la respuesta son responsabilidades conjuntas de Microsoft como proveedor de servicios en la nube y de usted como cliente.

* Para más información sobre el modelo de responsabilidad compartida, visite [Responsabilidad compartida en la nube](../../security/fundamentals/shared-responsibility.md).

* Para más información sobre cómo proteger el acceso de los usuarios con privilegios, visite [Protección del acceso con privilegios para implementaciones híbridas y en la nube en Azure AD](../roles/security-planning.md).

* Puede encontrar una amplia variedad de vídeos, guías paso a paso y contenido de conceptos clave sobre la identidad con privilegios en [Documentación sobre Privileged Identity Management](../privileged-identity-management/index.yml).

## <a name="where-to-look"></a>Dónde mirar

Los archivos de registro que usa para la investigación y supervisión son: 

* [Registros de auditoría de Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Registros de auditoría de Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview?view=o365-worldwide) 

* [Información detallada de Azure Key Vault](../../azure-monitor/insights/key-vault-insights-overview.md)

En Azure Portal, puede ver los registros de auditoría de Azure AD y descargarlos como archivos de valores separados por comas (CSV) o notación de objetos JavaScript (JSON). Azure Portal tiene varias maneras de integrar los registros de Azure AD con otras herramientas que permiten una mayor automatización de la supervisión y las alertas:

* [Azure Sentinel](../../sentinel/overview.md): permite el análisis de seguridad inteligente en el nivel empresarial proporcionando funcionalidades de Administración de eventos e información de seguridad (SIEM). 

* [Azure Monitor](../../azure-monitor/overview.md): permite la supervisión automatizada y las alertas de diversas condiciones. Puede crear o usar libros para combinar datos de orígenes diferentes.

* [Azure Event Hubs](../../event-hubs/event-hubs-about.md) (integrado con SIEM): los [registros de Azure AD se pueden insertar en otras instancias de SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md), como Splunk, ArcSight, QRadar y Sumo Logic, a través de la integración de Azure Event Hubs.

* [Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS): le permite detectar y administrar aplicaciones, controlar las aplicaciones y los recursos y comprobar el cumplimiento de las aplicaciones en la nube. 

* Microsoft Graph: puede exportar los datos y usar MS Graph para realizar más análisis. Para más información, visite [SDK de PowerShell de Microsoft Graph y Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-graph-api.md). 

* [Identity Protection](../identity-protection/overview-identity-protection.md): genera tres informes clave que puede usar para ayudar en la investigación:

   * Usuarios de riesgo: contiene información sobre qué usuarios están en riesgo, detalles sobre detecciones, historial de todos los inicios de sesión de riesgo e historial de riesgos.

   * Inicios de sesión de riesgo: contiene información que rodea la circunstancia de un inicio de sesión que puede indicar circunstancias sospechosas. Para obtener información adicional sobre cómo investigar información de este informe, visite [Instrucciones: Investigación de riesgos](../identity-protection/howto-identity-protection-investigate-risk.md). 

   * Detecciones de riesgo: contiene información sobre otros riesgos desencadenados cuando se detecta un riesgo y otra información pertinente, como la ubicación de inicio de sesión y los detalles de Microsoft Cloud App Security (MCAS).

 

Aunque se desaconseja la práctica, las cuentas con privilegios pueden tener derechos de administración permanentes. Si decide usar privilegios permanentes y la cuenta está en peligro, esto puede tener un impacto muy negativo. Se recomienda priorizar la supervisión de las cuentas con privilegios e incluir las cuentas en la configuración de Privileged Identity Management (PIM). Para más información sobre PIM, vea [Empiece a usar Privileged Identity Management](../privileged-identity-management/pim-getting-started.md). Además, se recomienda validar que las cuentas de administrador:

* Son obligatorias.

* Tienen el privilegio mínimo para ejecutar las actividades requeridas.

* Están protegidas con MFA como mínimo.

* Se ejecutan desde dispositivos de estación de trabajo de acceso con privilegios (PAW) o de estación de trabajo de administración segura (SAW). 

En el resto de este artículo se describe lo que se recomienda supervisar y alertar, y se organiza por el tipo de amenaza. Cuando hay soluciones previamente creadas y específicas, se establecen vínculos a ellas después de la tabla. De lo contrario, puede crear alertas mediante las herramientas anteriores. En concreto, en este artículo se proporcionan detalles sobre la configuración de líneas de base, la auditoría de inicios de sesión y el uso de cuentas con privilegios, y las herramientas y los recursos que puede usar para ayudar a mantener la integridad de las cuentas con privilegios. El contenido se organiza en las siguientes áreas de temas:

* Cuentas de emergencia de socorro 

* Inicio de sesión en la cuenta con privilegios

* Cambios en la cuenta con privilegios

* Grupos con privilegios

* Asignación de privilegios y elevación

## <a name="emergency-access-accounts"></a>Cuentas de acceso de emergencia

Es importante evitar que se bloquee accidentalmente el inquilino de Azure Active Directory (Azure AD). Puede mitigar el impacto de un bloqueo accidental mediante la creación de cuentas de acceso de emergencia en la organización. Las cuentas de acceso de emergencia también se conocen como cuentas de "socorro", como en los mensajes de "socorro en caso de emergencia" que se encuentran en los equipos de seguridad físicos, como las alarmas de incendios.

Las cuentas de acceso de emergencia tienen privilegios elevados y no se asignan a usuarios específicos. Las cuentas de acceso de emergencia se limitan a situaciones de urgencia o socorro en las que no se pueden usar las cuentas con privilegios normales. Por ejemplo, cuando una directiva de acceso condicional está mal configurada y bloquea todas las cuentas administrativas normales. Restrinja el uso de la cuenta de emergencia solo a las veces que sea absolutamente necesario.

Consulte también nuestras instrucciones sobre qué hacer en una emergencia, en [Protección de las prácticas de acceso para los administradores en Azure AD](../roles/security-planning.md).

Envíe una alerta de prioridad alta cada vez que se utilice una cuenta de acceso de emergencia.

### <a name="discovery"></a>Descubrimiento

Dado que las cuentas de emergencia solo se usan si hay una emergencia, la supervisión no debe detectar ninguna actividad de la cuenta. Envíe una alerta de prioridad alta cada vez que se utilice o modifique una cuenta de acceso de emergencia.  Cualquiera de los siguientes eventos podría indicar que una persona malintencionada está intentando poner en peligro sus entornos.

* Cuenta usada: supervise y notifique cualquier actividad que use este tipo de cuenta, lo que incluye:

* Iniciar sesión

* Cambio de la contraseña de la cuenta 

* Cambio de los roles o los permisos de la cuenta 

* Adición o modificación del método de autenticación o de las credenciales 

Para más información sobre la administración de las cuentas de acceso de emergencia, vea [Administración de las cuentas de administrador de acceso de emergencia en Azure AD](../roles/security-emergency-access.md). Para obtener información detallada sobre cómo crear una alerta para una cuenta de emergencia, vea [Creación de una regla de alerta](../roles/security-emergency-access.md). 

## <a name="privileged-account-sign-in"></a>Inicio de sesión en la cuenta con privilegios

Supervise toda la actividad de inicio de sesión de la cuenta con privilegios mediante los registros de inicio de sesión de Azure AD como el origen de datos. Además de la información de inicio de sesión correcto y con error, los registros contienen los detalles siguientes:

* Interrupciones
* Dispositivo
* Ubicación
* Riesgo
* Application
* Fecha y hora
* ¿La cuenta está deshabilitada?
* Bloqueo
* Fraude de MFA
* Error de CA

### <a name="things-to-monitor"></a>Elementos que se deben supervisar

Puede supervisar los eventos de inicio de sesión de la cuenta con privilegios en los registros de inicio de sesión de Azure AD. Notifique e investigue los siguientes eventos para las cuentas con privilegios.

| Elementos para supervisar | Nivel de riesgo |  Where |  Filtro o subfiltro | Notas |
| - | - | - | - | - |
| Error de inicio de sesión, umbral de contraseña incorrecta | Alto | Registro de inicios de sesión de Azure AD | Estado = Error<br>- y -<br>Código de error = 50126 | Defina un umbral de línea de base y, a continuación, supervise y ajuste para adaptar los comportamientos de la organización y limitar la generación de alertas falsas. |
| Error debido a un requisito de CA |Alto | Registro de inicios de sesión de Azure AD | Estado = Error<br>- y -<br>Código de error = 53003<br>- y -<br>Motivo del error = bloqueado por la CA | Esto puede ser una indicación de que un atacante está intentando acceder a la cuenta. |
| Cuentas con privilegios que no siguen la directiva de nomenclatura.| | Suscripción de Azure | [Enumeración de asignaciones de roles de Azure mediante Azure Portal: RBAC de Azure](../../role-based-access-control/role-assignments-list-portal.md)| Muestra las asignaciones de roles de las suscripciones y alertas en los que el nombre de inicio de sesión no coincide con el formato de la organización. Por ejemplo, ADM_ como prefijo. |
| Interrupción |  Alto/Medio | Inicios de sesión de Azure AD | Estado = Interrumpido<br>- y -<br>Código de error = 50074<br>- y -<br>Motivo del error = Se requiere autenticación sólida<br>Estado = Interrumpido<br>- y -<br>Código de error = 500121<br>Motivo del error = Error de autenticación durante la solicitud de autenticación sólida | Esto puede ser una indicación de que un atacante tiene la contraseña de la cuenta, pero no puede superar el desafío de MFA. |   |   |
| Cuentas con privilegios que no siguen la directiva de nomenclatura.| Alto | Directorio de Azure AD | [Enumeración de asignaciones de roles de Azure AD](../roles/view-assignments.md)| Muestra las asignaciones de roles de las alertas de roles de Azure AD en las que el UPN no coincide con el formato de su organización. Por ejemplo, ADM_ como prefijo. |
| Detectar cuentas con privilegios no registradas para MFA. | Alto | Graph API de Azure AD| Consulta de IsMFARegistered eq false para cuentas de administrador. [Enumeración de los detalles de credentialUserRegistration:Microsoft Graph beta](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&tabs=http) | Audite e investigue para determinar si es intencionada o de vigilancia. |
| Bloqueo de cuenta | Alto | Registro de inicios de sesión de Azure AD | Estado = Error<br>- y -<br>Código de error = 50053 | Defina un umbral de línea de base y, a continuación, supervise y ajuste para adaptar los comportamientos de la organización y limitar la generación de alertas falsas. |
| Cuenta deshabilitada o bloqueada para inicios de sesión | Bajo | Registro de inicios de sesión de Azure AD | Estado = Error<br>- y -<br>Destino = UPN de usuario<br>- y -<br>Código de error = 50057 | Esto podría indicar que alguien está intentando obtener acceso a una cuenta una vez que ha dejado una organización. Aunque la cuenta está bloqueada, sigue siendo importante registrar y esta actividad y alertar sobre ella. |
| Bloque o alerta de fraude de MFA | Alto | Registro de inicios de sesión de Azure AD/Análisis de registros de Azure | Correcto = false<br>- y -<br>Detalle del resultado = MFA denegado<br>- y -<br>Destino = usuario | El usuario con privilegios ha indicado que no ha enviado el mensaje de MFA, y podría indicar que un atacante tiene la contraseña de la cuenta. |
| Inicios de sesión de cuentas con privilegios fuera de los controles esperados. |  | Registro de inicios de sesión de Azure AD | Estado = error<br>UserPricipalName = <Admin account><br>Ubicación = <unapproved location><br>Dirección IP = <unapproved IP><br>Información del dispositivo= <explorador/sistema operativo no aprobado> | Supervise y notifique todas las entradas que haya definido como no aprobadas. |
| Fuera de los tiempos de inicio de sesión normales | Alto | Registro de inicios de sesión de Azure AD | Estado = correcto<br>- y -<br>Ubicación =<br>- y -<br>Hora = fuera del horario laboral | Supervise y notifique si los inicios de sesión se producen fuera del horario esperado. Es importante encontrar el patrón de trabajo normal para cada cuenta con privilegios y alertar si hay cambios no planeados fuera de las horas de trabajo normales. Los inicios de sesión fuera del horario laboral normal podrían indicar riesgos o posibles amenazas internas. | 
| Riesgo de Identity Protection | Alto | Registros de Identity Protection | Estado de riesgo = en riesgo<br>- y -<br>Nivel de riesgo = bajo/medio/alto<br>- y -<br>Actividad = Inicio de sesión/TOR desconocido, etc. | Esto indica que se ha detectado alguna anomalía con el inicio de sesión de la cuenta y que es necesario estar alerta. | 
| Cambio de contraseña | Alto | Registros de auditoría de Azure AD | Actor de la actividad = administrador/autoservicio<br>- y -<br>Destino = usuario<br>- y -<br>Estado = correcto/error | Alertar sobre cualquier cambio de contraseña de la cuenta de administrador, especialmente para administradores globales, administradores de usuarios, administradores de suscripciones y cuentas de acceso de emergencia. Escriba una consulta destinada a todas las cuentas con privilegios. | 
| Modificación del protocolo de autenticación heredado | Alto | Registro de inicios de sesión de Azure AD | Aplicación cliente = Otro cliente, IMAP, POP3, MAPI, SMTP, etc.<br>- y -<br>Nombre de usuario = UPN<br>- y -<br>Aplicación = Exchange (ejemplo) | Muchos ataques usan la autenticación heredada y, por tanto, si hay un cambio en el protocolo de autenticación para el usuario, podría ser una indicación de un ataque. |
| Nuevo dispositivo o ubicación | Alto | Registro de inicios de sesión de Azure AD | Información del dispositivo = Id. de dispositivo<br>- y -<br>Browser<br>- y -<br>SO<br>- y -<br>Compatible/Administrado<br>- y -<br>Destino = usuario<br>- y -<br>Ubicación | La mayoría de la actividad de administración debe proceder de [dispositivos de acceso con privilegios](/security/compass/privileged-access-devices), desde un número limitado de ubicaciones. Por lo tanto, alerte sobre nuevos dispositivos o ubicaciones. |
| Se ha cambiado la configuración de las alertas de auditoría. | Alto | Registros de auditoría de Azure AD | Servicio = PIM<br>- y -<br>Categoría = Administración de roles<br>- y -<br>Actividad = Deshabilitar alerta de PIM<br>- y -<br>Estado = Correcto | Los cambios en una alerta principal se deben alertar si son inesperados. |


## <a name="changes-by-privileged-accounts"></a>Cambios por cuentas con privilegios 

Supervise todos los cambios completados e intentados por una cuenta con privilegios. Esto le permite establecer lo que es una actividad normal para cada cuenta con privilegios y alertar sobre la actividad que se desvía de lo esperado. Los registros de auditoría de Azure AD se usan para registrar este tipo de evento. Para más información sobre los registros de auditoría de Azure AD, vea [Registros de auditoría en Azure Active Directory](../reports-monitoring/concept-audit-logs.md). 

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Las cuentas con privilegios a las que se han asignado permisos en Azure Active Directory Domain Services pueden realizar tareas para Azure AD DS que afectan a la posición de seguridad de las máquinas virtuales (VM) hospedadas de Azure que usan Azure AD Domain Services. Habilite las auditorías de seguridad en las máquinas virtuales y supervise los registros. Para más información sobre cómo habilitar las auditorías de Azure AD DS y para obtener una lista de los que se consideran privilegios confidenciales, consulte los siguientes recursos.

* [Habilitación de auditorías de seguridad para Azure Active Directory Domain Services](../../active-directory-domain-services/security-audit-events.md)

* [Auditar uso de privilegios confidenciales](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use)

| Elementos para supervisar                                                         | Nivel de riesgo | Where               | Filtro o subfiltro                                                                                                                                                                                                                                                                                                                                                                                                                                                                    | Notas                                                                                                                                                                                                                                                                                                                                                                                                                             |
|-------------------------------------------------------------------------|------------|---------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cambios intentados y completados                                  | Alto       | Registros de auditoría de Azure AD | Fecha y hora<br>- y -<br>Servicio<br>- y -<br>Categoría y nombre de la actividad (qué)<br>- y -<br>Estado = correcto o error<br>- y -<br>Destino<br>- y -<br>Iniciador/actor (quién)                                                                                                                                                                                                                                                                                                | Los cambios no planeados se deben alertar inmediatamente. Estos registros deben conservarse para ayudar en cualquier investigación. Los cambios de nivel de inquilino deben investigarse inmediatamente (vincularse a la documentación de la infraestructura). Cambios que reducirían la posición de seguridad del inquilino. Por ejemplo: excluir cuentas de MFA o del acceso condicional. Alerta sobre las [adiciones o cambios en las aplicaciones](security-operations-applications.md). |
| **EJEMPLO**<br>Se intentó o completó el cambio de servicios o aplicaciones de alto valor. | Alto       | Registro de auditoría           | Servicio<br>- y -<br>Categoría y nombre de la actividad                                                                                                                                                                                                                                                                                                                                                                                                                                | <li>Fecha y hora <li>Servicio <li>Categoría y nombre de la actividad <li>Estado = correcto o error <li>Destino <li>Iniciador/actor (quién)                                                                                                                                                                                                                                                                                        |
| Cambios con privilegios en Azure AD DS                                             | Alto       | AD DS               | Búsqueda del evento [4673](/windows/security/threat-protection/auditing/event-4673) | [Habilitación de auditorías de seguridad para Azure Active Directory Domain Services](../../active-directory-domain-services/security-audit-events.md)<br>[Auditar uso de privilegios confidenciales](/windows/security/threat-protection/auditing/audit-sensitive-privilege-use). Consulte el artículo para obtener una lista de todos los eventos con privilegios.                                                                                                                            |


## <a name="changes-to-privileged-accounts"></a>Cambios en cuentas con privilegios

Investigue los cambios en las reglas de autenticación y los privilegios de las cuentas con privilegios, especialmente si el cambio proporciona más privilegios o capacidad para realizar tareas en el entorno de Azure AD. 

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - | - | - | - | - |
| Creación de una cuenta con privilegios.| Media| Registros de auditoría de Azure AD| Servicio = Directorio principal<br>- y -<br>Categoría = administración de usuarios<br>- y -<br>Tipo de actividad = Agregar usuario<br>—correlacionar con—<br>Tipo de categoría = Administración de roles<br>- y -<br>Tipo de actividad = Agregar miembro a rol<br>- y -<br>Propiedades modificadas = Role.DisplayName| Supervise la creación de cualquier cuenta con privilegios. Busque la correlación de un intervalo de tiempo corto entre la creación y la eliminación de cuentas. |
| Cambios en los métodos de autenticación.| Alto| Registros de auditoría de Azure AD| Servicio = Método de autenticación<br>- y -<br>Tipo de actividad = Información de seguridad registrada por el usuario<br>- y -<br>Categoría = administración de usuarios| Esto podría indicar que un atacante agrega un método de autenticación a la cuenta para poder tener acceso continuado. |
| Alertar de los cambios en los permisos de las cuentas con privilegios.| Alto| Registros de auditoría de Azure AD| Categoría = Administración de roles<br>- y -<br>Tipo de actividad: agregar un miembro válido (permanente)<br>- y -<br>Tipo de actividad: agregar un miembro válido (opcional)<br>- y -<br>Estado = correcto/error<br>- y -<br>Propiedades modificadas = Role.DisplayName| Esto es especialmente para las cuentas a las que se asignan roles que no se conocen o están fuera de sus responsabilidades normales. |
| Cuentas con privilegios no utilizadas.| Media| Revisiones de acceso de Azure AD| | Realice una revisión mensual de las cuentas de usuario con privilegios inactivas. |
| Cuentas exentas del acceso condicional| Alto| Registros de Azure Monitor<br>O bien<br>Revisiones de acceso| Acceso condicional: información e informes| Es muy probable que cualquier cuenta exenta del acceso condicional omita los controles de seguridad y sea más vulnerable a riesgos. Las cuentas de emergencia están exentas. Consulte la información sobre cómo supervisar las cuentas de emergencia en una sección posterior de este artículo.|


Para más información sobre cómo supervisar las excepciones a las directivas de acceso condicional, vea [Información detallada e informes del acceso condicional](../conditional-access/howto-conditional-access-insights-reporting.md).

Para más información sobre cómo detectar cuentas con privilegios no utilizadas, vea [Creación de una revisión de acceso para los roles de Azure AD en Azure AD Privileged Identity Management (PIM)](../privileged-identity-management/pim-how-to-start-security-review.md).

 
## <a name="assignment-and-elevation"></a>Asignación y elevación

Tener cuentas con privilegios que se aprovisionan permanentemente con capacidades elevadas puede aumentar la superficie expuesta a ataques y el riesgo para el límite de seguridad. En su lugar, emplee el acceso Just-In-Time mediante un procedimiento de elevación. Este tipo de sistema permite asignar la idoneidad para los roles con privilegios, y los administradores elevan sus privilegios a esos roles solo al realizar tareas que necesitan esos privilegios. El uso de un proceso de elevación le permite supervisar las elevaciones y el no uso de cuentas con privilegios. 

### <a name="establish-a-baseline"></a>Establecimiento de una línea de base

Para supervisar las excepciones, primero debe crear una línea de base. Determine lo siguiente:

* Cuentas de administrador 

   * La estrategia de su cuenta con privilegios

   * Uso de cuentas locales para administrar recursos locales

   * Uso de cuentas basadas en la nube para administrar recursos en la nube

   * Enfoque para separar y supervisar los permisos administrativos de los recursos locales y basados en la nube

* Protección de roles con privilegios 

   * Estrategia de protección para los roles que tienen privilegios administrativos

   * Directiva de la organización para usar cuentas con privilegios

   * Estrategia y principios para mantener privilegios permanentes frente a proporcionar acceso con límite de tiempo y aprobado

 

Los siguientes conceptos e información le ayudarán a determinar las directivas.

* Principios de administración Just-In-Time: use los registros de Azure AD para capturar información para realizar tareas administrativas comunes en su entorno. Determine la cantidad de tiempo típica necesaria para completar las tareas. 

* Principios de administración Just Enough: [determine el rol con privilegios mínimos](../roles/delegate-by-task.md), que puede ser un rol personalizado, necesario para las tareas administrativas. 

* Establecer una directiva de elevación: una vez que tenga información sobre el tipo de privilegios elevados necesarios y cuánto tiempo se necesita para cada tarea, cree directivas que reflejen el uso con privilegios elevados para su entorno. Por ejemplo, definir una directiva para limitar el acceso de administrador global a 1 hora.

 Una vez establecida la línea de base y la directiva, puede configurar la supervisión para detectar y notificar el uso fuera de la directiva. 

### <a name="discovery"></a>Descubrimiento

Se recomienda prestar especial atención a los cambios en la asignación y la elevación de privilegios e investigarlos. 

### <a name="things-to-monitor"></a>Elementos que se deben supervisar

Puede supervisar los cambios de la cuenta con privilegios con los registros de auditoría de Azure AD y los registros de Azure Monitor. En concreto, se recomienda incluir lo siguiente en el proceso de supervisión. 

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - | - | - | - | - |
| Lo que se ha agregado al rol con privilegios elegible.| Alto| Registros de auditoría de Azure AD| Servicio = PIM<br>- y -<br>Categoría = Administración de roles<br>- y -<br>Tipo de actividad: Agregar miembro al rol completado (elegible)<br>- y -<br>Estado = correcto/error<br>- y -<br>Propiedades modificadas = Role.DisplayName| Ahora se proporciona acceso con privilegios a cualquier cuenta elegible para un rol. Si la asignación es inesperada o en un rol que no es responsabilidad del titular de la cuenta, investigue. |
| Roles asignados fuera de PIM.| Alto| Registros de auditoría de Azure AD| Servicio = PIM<br>- y -<br>Categoría = Administración de roles<br>- y -<br>Tipo de actividad = Agregar miembro a rol (permanente)<br>- y -<br>Estado = correcto/error<br>- y -<br>Propiedades modificadas = Role.DisplayName| Se deben supervisar y notificar minuciosamente. Los usuarios no deben tener roles asignados fuera de PIM siempre que sea posible. |
| Elevaciones| Media| Registros de auditoría de Azure AD| Servicio = PIM<br>- y -<br>Categoría = Administración de roles<br>- y -<br>Tipo de actividad: Agregar miembro al rol completado (activación de PIM)<br>- y -<br>Estado = correcto/error<br>- y -<br>Propiedades modificadas = Role.DisplayName| Una vez que se ha elevado una cuenta con privilegios, ahora puede realizar cambios que podrían afectar a la seguridad del inquilino. Se deben registrar todas las elevaciones y, si se están produciendo fuera del patrón estándar para ese usuario, se debe alertar e investigar si no está planeado. |
| Elevación de aprobaciones y denegaciones| Bajo| Registros de auditoría de Azure AD| Servicio = Revisión de acceso<br>- y -<br>Categoría = UserManagement<br>- y -<br>Tipo de actividad = Solicitud aprobada o denegada<br>- y -<br>Actor iniciado = UPN| Supervise todas las elevaciones, ya que podrían ser un indicio claro de la escala de tiempo de un ataque. |
| Cambios en la configuración de PIM| Alto| Registros de auditoría de Azure AD| Servicio =PIM<br>- y -<br>Categoría = Administración de roles<br>- y -<br>Tipo de actividad = Actualización de la configuración de roles en PIM<br>- y -<br>Motivo de estado = MFA durante la activación deshabilitada (ejemplo)| Una de estas acciones podría reducir la seguridad de la elevación de PIM y facilitar a los atacantes la adquisición de una cuenta con privilegios. |
| Elevación que no se produce en SAW/PAW| Alto| Registros de inicio de sesión de Azure AD| Id. de dispositivo<br>- y -<br>Browser<br>- y -<br>SO<br>- y -<br>Compatible/Administrado<br>Correlacionar con:<br>Servicio = PIM<br>- y -<br>Categoría = Administración de roles<br>- y -<br>Tipo de actividad: Agregar miembro al rol completado (activación de PIM)<br>- y -<br>Estado = correcto/error<br>- y -<br>Propiedades modificadas = Role.DisplayName| Si se configura, cualquier intento de elevar en un dispositivo que no sea PAW o SAW debe investigarse inmediatamente, ya que podría indicar que un atacante intenta usar la cuenta. |
| Elevación para administrar todas las suscripciones de Azure| Alto| Azure Monitor| Registro de actividad (pestaña) <br>Actividad de directorio (pestaña) <br> Nombre de las operaciones = Asigna el autor de la llamada al administrador de acceso de usuarios <br> - y - <br> Categoría de eventos=administrativo <br> - y -<br>Estado = correcto, inicio, error<br>- y -<br>Evento iniciado por| Esto se debe investigar inmediatamente si no es un cambio planeado. Esta configuración podría permitir que un atacante acceda a las suscripciones de Azure de su entorno. |


Para más información sobre la administración de la elevación, vea [Elevación de los privilegios de acceso para administrar todas las suscripciones y los grupos de administración de Azure](../../role-based-access-control/elevate-access-global-admin.md). Para obtener información sobre la supervisión de elevaciones mediante la información disponible en los registros de Azure AD, vea [Registro de actividad de Azure](../../azure-monitor/essentials/activity-log.md), que forma parte de la documentación de Azure Monitor.

Para obtener información sobre cómo configurar alertas para el rol de Azure, vea [Configuración de alertas de seguridad para roles de recursos de Azure en Privileged Identity Management](../privileged-identity-management/pim-resource-roles-configure-alerts.md). 

 ## <a name="next-steps"></a>Pasos siguientes

Consulte estos artículos de la guía de operaciones de seguridad:

[Introducción a las operaciones de seguridad de Azure AD](security-operations-introduction.md)

[Operaciones de seguridad para cuentas de usuario](security-operations-user-accounts.md)

[Operaciones de seguridad para cuentas con privilegios](security-operations-privileged-accounts.md)

[Operaciones de seguridad para Privileged Identity Management](security-operations-privileged-identity-management.md)

[Operaciones de seguridad para aplicaciones](security-operations-applications.md)

[Operaciones de seguridad para dispositivos](security-operations-devices.md)

 
[Operaciones de seguridad para infraestructura](security-operations-infrastructure.md)
