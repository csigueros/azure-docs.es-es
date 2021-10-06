---
title: Operaciones de seguridad de Azure Active Directory para cuentas de usuario
description: Guía para establecer bases de referencia y cómo supervisar y alertar sobre posibles problemas de seguridad con las cuentas de usuario.
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
ms.openlocfilehash: f78765e7b0d26b2767dabf4b1fdc4ccdd6873123
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124803594"
---
# <a name="azure-active-directory-security-operations-for-user-accounts"></a>Operaciones de seguridad de Azure Active Directory para cuentas de usuario

La identidad del usuario es uno de los aspectos más importantes de la protección de la organización y los datos. En este artículo se proporcionan instrucciones para supervisar la creación, eliminación y uso de cuentas. En la primera parte se explica cómo supervisar la creación y eliminación inusual de una cuenta. En la segunda parte se explica cómo supervisar el uso inusual de la cuenta. 

Si aún no ha leído la [introducción sobre operaciones de seguridad de Azure Active Directory](security-operations-introduction.md), le recomendamos que lo haga antes de continuar.

Este artículo trata sobre cuentas de usuario generales. Para las cuentas con privilegios, consulte Operaciones de seguridad: Cuentas con privilegios.

## <a name="define-a-baseline"></a>Definición de una base de referencia

Para detectar comportamientos anómalos, primero debe definir cuál es el comportamiento normal y previsible. La definición del comportamiento previsible para su organización le ayuda a determinar cuándo se produce un comportamiento inesperado. La definición también ayuda a reducir el nivel de ruido de los falsos positivos durante la supervisión y el envío de alertas. 

Una vez que defina lo que se considera previsible, realice la supervisión de base de referencia para validar sus expectativas. Con esa información, puede supervisar los registros para todo lo que se encuentra fuera de las tolerancias que defina. 

Use los registros de auditoría y de inicio de sesión de Azure AD, y los atributos de directorio como orígenes de datos para las cuentas creadas fuera de los procesos normales. Las siguientes son sugerencias que le ayudarán a pensar y definir lo que es normal para su organización.

* **Creación de cuentas de usuario**, evalúe lo siguiente:

   * Estrategia y principios para las herramientas y procesos usados para crear y administrar cuentas de usuario. Por ejemplo, si hay atributos estándar o formatos que se apliquen a los atributos de las cuentas de usuario. 

  * Orígenes aprobados para la creación de cuentas. Por ejemplo, si se origina en Active Directory (AD), Azure Active Directory o en sistemas de recursos humanos como Workday.

  * Estrategia de alertas para las cuentas creadas fuera de los orígenes aprobados. ¿Hay una lista controlada de organizaciones con las que colabora su organización?

  * Aprovisionamiento de cuentas de invitado y parámetros de alerta para cuentas creadas fuera de la administración de derechos u otros procesos normales.

  * Estrategia y parámetros de alerta para las cuentas creadas, modificadas o deshabilitadas por una cuenta que no es un administrador de usuarios aprobado.

  * Supervisión y estrategia de alertas para cuentas que no tienen atributos estándar, como el identificador de empleado o el hecho de no seguir las convenciones de nomenclatura de la organización.

  * Estrategia, principios y proceso para la eliminación y retención de cuentas.

* **Cuentas de usuario locales**: evalúe los siguientes aspectos para las cuentas sincronizadas con Azure AD Connect:

  * Bosques, dominios y unidades organizativas en el ámbito de la sincronización. ¿Quiénes son los administradores aprobados que pueden cambiar esta configuración y la frecuencia con la que se comprueba el ámbito?

  * Tipos de cuentas que se sincronizan. Por ejemplo, cuentas de usuario o cuentas de servicio. 

  * Proceso para crear cuentas locales con privilegios y cómo se controla la sincronización de este tipo de cuenta.

  * Proceso para crear cuentas de usuario locales y cómo se administra la sincronización de este tipo de cuenta.

Para más información sobre cómo proteger y supervisar cuentas locales, consulte [Protección de Microsoft 365 contra ataques locales](protect-m365-from-on-premises-attacks.md).

* **Cuenta de usuario en la nube**: evalúe los siguientes aspectos:

  * Proceso para aprovisionar y administrar cuentas en la nube directamente en Azure AD.

  * Proceso para determinar los tipos de usuarios aprovisionados como cuentas en la nube de Azure AD. Por ejemplo, ¿solo permite cuentas con privilegios o también permite cuentas de usuario?

  * Proceso para crear y mantener una lista de usuarios de confianza y procesos que se espera que creen y administren cuentas de usuario en la nube.

  * Proceso para crear y mantener una estrategia de alertas para cuentas no aprobadas basadas en la nube. 

## <a name="where-to-look"></a>Dónde mirar

Los archivos de registro que usa para la investigación y supervisión son: 

* [Registros de auditoría de Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Registros de inicio de sesión](../reports-monitoring/concept-all-sign-ins.md)

* [Registros de auditoría de Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview) 

* [Registros de Azure Key Vault](../../key-vault/general/logging.md?tabs=Vault)

* [Registro de usuarios de riesgo](../identity-protection/howto-identity-protection-investigate-risk.md)

* [Registro de UserRiskEvents](../identity-protection/howto-identity-protection-investigate-risk.md)

En Azure Portal, puede ver los registros de auditoría de Azure AD y descargarlos como archivos de valores separados por comas (CSV) o notación de objetos JavaScript (JSON). Azure Portal tiene varias maneras de integrar los registros de Azure AD con otras herramientas que permiten una mayor automatización de la supervisión y las alertas:

* **[Azure Sentinel](../../sentinel/overview.md)** : permite el análisis de seguridad inteligente en el nivel empresarial al ofrecer funcionalidades de administración de eventos e información de seguridad (SIEM). 

* **[Azure Monitor](../../azure-monitor/overview.md)** : permite la supervisión y la generación de alertas automatizadas de diversas condiciones. Puede crear o usar libros para combinar datos de orígenes diferentes.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) (integrado con SIEM)** - [: los registros de Azure AD se pueden integrar con otras instancias de SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md), como Splunk, ArcSight, QRadar y Sumo Logic a través de la integración de Azure Event Hubs.

* **[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS)** : le permite detectar y administrar aplicaciones, controlar entre aplicaciones y recursos y comprobar el cumplimiento de las aplicaciones en la nube. 

Gran parte de lo que supervisará y alertará son los efectos de las directivas de acceso condicional. Puede usar el [libro Información detallada e informes del acceso condicional](../conditional-access/howto-conditional-access-insights-reporting.md) para examinar los efectos de una o varias directivas de acceso condicional en los inicios de sesión, así como los resultados de las directivas, incluido el estado del dispositivo. Este libro le permite ver un resumen de impacto e identificar el impacto durante un período de tiempo específico. También puede usar el libro para investigar los inicios de sesión de un usuario específico. 

 En el resto de este artículo se describe lo que se recomienda supervisar y alertar, y se organiza por el tipo de amenaza. Cuando hay soluciones previamente creadas y específicas, se establecen vínculos a ellas o se proporcionan ejemplos después de la tabla. De lo contrario, puede crear alertas mediante las herramientas anteriores. 

## <a name="account-creation"></a>Creación de cuentas

La creación de cuentas anómalas puede indicar un problema de seguridad. Se deben investigar las cuentas de corta duración, las cuentas que no siguen los estándares de nomenclatura y las cuentas creadas fuera de los procesos normales.

### <a name="short-lived-accounts"></a>Cuentas de corta duración

La creación y eliminación de cuentas fuera de los procesos de administración de identidades normales se deben supervisar en Azure AD. Las cuentas de corta duración son cuentas creadas y eliminadas en un breve intervalo de tiempo. Este tipo de creación y eliminación rápida de cuentas podría significar que un actor malintencionado está intentando evitar la detección mediante la creación de cuentas, su uso y la eliminación de la cuenta.

Los patrones de cuentas de corta duración pueden indicar que las personas o procesos no aprobados pueden tener el derecho de crear y eliminar cuentas que no se encuentran en los procesos y directivas establecidos. Este tipo de comportamiento quita los marcadores visibles del directorio. 

Si la traza de datos para la creación y eliminación de cuentas no se detecta rápidamente, es posible que la información necesaria para investigar un incidente ya no exista. Por ejemplo, es posible que las cuentas se eliminen y, a continuación, se purguen de la papelera de reciclaje. Los registros de auditoría se conservan 30 días. Sin embargo, puede exportar los registros a Azure Monitor o a una solución de administración de eventos e información de seguridad (SIEM) para una retención a más largo plazo. 

| Elementos para supervisar                                                  | Nivel de riesgo | Where               | Filtro o subfiltro                                                                                                                                         | Notas                                                                                                                               |
|------------------------------------------------------------------|------------|---------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| Eventos de creación y eliminación de cuentas dentro de un período de tiempo cerrado.  | Alto       | Registros de auditoría de Azure AD | Actividad: Agregar usuario<br>Estado = correcto<br>- y -<br>Actividad: Eliminar usuario<br>Estado = correcto<br>                                                          | Busque eventos de nombre principal de usuario (UPN). Busque las cuentas creadas y eliminadas en menos de 24 horas.                          |
| Cuentas creadas y eliminadas por usuarios o procesos no aprobados. | Media     | Registros de auditoría de Azure AD | Iniciado por (actor): NOMBRE PRINCIPAL DE USUARIO<br>- y -<br>Actividad: Agregar usuario<br>Estado = correcto<br>y/o<br>Actividad: Eliminar usuario<br>Estado = correcto      | Si los actores son usuarios no aprobados, configure el envío de una alerta.                                                                    |
| Cuentas de orígenes no aprobados.                              | Media     | Registros de auditoría de Azure AD | Actividad: Agregar usuario<br>Estado = correcto<br>Destinos = Nombre principal de usuario (UPN)                                                                                 | Si la entrada no es de un dominio aprobado o es un dominio bloqueado conocido, configure el envío de una alerta.                               |
| Cuentas asignadas a un rol con privilegios.                          | Alto       | Registros de auditoría de Azure AD | Actividad: Agregar usuario<br>Estado = correcto<br>- y -<br>Actividad: Eliminar usuario<br>Estado = correcto<br>- y -<br>Actividad = Agregar miembro a rol.<br>Estado = correcto | Si la cuenta se asigna a un rol de Azure AD, un rol de Azure o a una pertenencia a grupos con privilegios, configure una alerta y dé prioridad a la investigación. |

Las cuentas con o sin privilegios se deben supervisar y contar con procesos de alerta. Sin embargo, dado que las cuentas con privilegios tienen permisos administrativos, estas deben tener mayor prioridad en los procesos de supervisión, alerta y respuesta. 

### <a name="accounts-not-following-naming-policies"></a>Cuentas que no siguen las directivas de nomenclatura

Es posible que las cuentas de usuario que no están siguiendo las directivas de nomenclatura se hayan creado a espaldas de las directivas organizativas. 

Un procedimiento recomendado es tener una directiva de nomenclatura para los objetos de usuario. Tener una directiva de nomenclatura facilita la administración y ayuda a proporcionar coherencia. La directiva también puede ayudar a detectar cuándo se han creado usuarios a espaldas de los procesos aprobados. Un actor malintencionado podría no tener en cuenta los estándares de nomenclatura y esto podría facilitar la detección de una cuenta aprovisionada a espaldas de los procesos de la organización.

Las organizaciones tienden a tener formatos y atributos específicos que se usan para crear cuentas de usuario o con privilegios. Por ejemplo:

* UPN de cuenta de administrador = ADM_firstname.lastname@tenant.onmicrosoft.com

* UPN de cuenta de usuario = Firstname.Lastname@contoso.com

Las cuentas de usuario también suelen tener un atributo que identifica a un usuario real. Por ejemplo, EMPID = XXXNNN. A continuación, se incluyen sugerencias que le ayudarán a pensar y definir lo que es normal para su organización, así como los aspectos que debe tener en cuenta al definir la base de referencia para las entradas de registro en las que las cuentas no siguen la convención de nomenclatura de su organización:

* Cuentas que no siguen la convención de nomenclatura. Por ejemplo, `nnnnnnn@contoso.com` en lugar de `firstname.lastname@contoso.com`. 

* Cuentas que no tienen los atributos estándar rellenos o no tienen el formato correcto. Por ejemplo, no tienen un identificador de empleado válido.

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - | - | - | - | - |
| Cuentas de usuario que no tienen definidos los atributos esperados.| Bajo| Registros de auditoría de Azure AD| Actividad: Agregar usuario<br>Estado = correcto| Busque cuentas con los atributos estándar que son NULL o tienen un formato incorrecto. Por ejemplo, EmployeeID |
| Cuentas de usuario creadas con un formato de nomenclatura incorrecto.| Bajo| Registros de auditoría de Azure AD| Actividad: Agregar usuario<br>Estado = correcto| Busque cuentas con un UPN que no siga la directiva de nomenclatura. |
| Cuentas con privilegios que no siguen la directiva de nomenclatura.| Alto| Suscripción de Azure| [Enumeración de asignaciones de roles de Azure mediante Azure Portal: RBAC de Azure](../../role-based-access-control/role-assignments-list-portal.md)| Muestra las asignaciones de roles de las suscripciones y alertas en los que el nombre de inicio de sesión no coincide con el formato de la organización. Por ejemplo, ADM_ como prefijo. |
| Cuentas con privilegios que no siguen la directiva de nomenclatura.| Alto| Directorio de Azure AD| [Enumeración de asignaciones de roles de Azure AD](../roles/view-assignments.md)| Muestra las asignaciones de roles de las alertas de roles de Azure AD en las que el UPN no coincide con el formato de su organización. Por ejemplo, ADM_ como prefijo. |



Para más información sobre el análisis, consulte:

* Registros de auditoría de Azure AD: [Análisis de datos de texto en registros de Azure Monitor](../../azure-monitor/logs/parse-text.md)

* Para suscripciones de Azure: [Enumeración de asignaciones de roles de Azure mediante Azure PowerShell](../../role-based-access-control/role-assignments-list-powershell.md)

* Para Azure Active Directory: [Enumeración de asignaciones de roles de Azure AD](../roles/view-assignments.md) 

### <a name="accounts-created-outside-normal-processes"></a>Cuentas creadas a espaldas de los procesos normales

Tener procesos estándar para crear usuarios y cuentas con privilegios es importante para que pueda controlar de forma segura el ciclo de vida de las identidades. Si los usuarios se aprovisionan y desaprovisionan a espaldas de los procesos establecidos, pueden presentar riesgos de seguridad. El funcionamiento a espaldas de los procesos establecidos también puede presentar problemas de administración de identidades. Entre los posibles riesgos se incluyen:

* Es posible que no se pueda controlar que las cuentas de usuario y con privilegios cumplan las directivas de la organización. Esto puede dar lugar a una superficie de ataque más amplia en las cuentas que no se administran correctamente.

* Resulta más difícil detectar cuándo los actores malintencionados crean cuentas con fines malintencionados. Al tener cuentas válidas creadas a espaldas de los procedimientos establecidos, resulta más difícil detectar cuándo se crean cuentas o se modifican permisos con fines malintencionados. 

Es recomendable que las cuentas de usuario y con privilegios solo se creen siguiendo las directivas de la organización. Por ejemplo, se debe crear una cuenta con los estándares de nomenclatura y la información de la organización correctos, y en el ámbito de la gobernanza de identidades adecuada. Las organizaciones deben tener controles rigurosos sobre quién tiene derechos para crear, administrar y eliminar identidades. Los roles para crear estas cuentas deben administrarse concienzudamente y los derechos solo están disponibles después de seguir un flujo de trabajo establecido para aprobar y obtener estos permisos.

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - | - | - | - | - |
| Cuentas de usuario creadas o eliminadas por usuarios o procesos no aprobados.| Media| Registros de auditoría de Azure AD| Actividad: Agregar usuario<br>Estado = correcto<br>y/o<br>Actividad: Eliminar usuario<br>Estado = correcto<br>- y -<br>Iniciado por (actor) = NOMBRE PRINCIPAL DE USUARIO| Alerta sobre cuentas creadas por usuarios o procesos no aprobados. Priorice las cuentas creadas con privilegios elevados. |
| Cuentas de usuario creadas o eliminadas de orígenes no aprobados.| Media| Registros de auditoría de Azure AD| Actividad: Agregar usuario<br>Estado = correcto<br>O bien<br>Actividad: Eliminar usuario<br>Estado = correcto<br>- y -<br>Destinos = Nombre principal de usuario (UPN)| Se envía una alerta si el dominio no está aprobado o es un dominio bloqueado conocido. |


## <a name="unusual-sign-ins"></a>Inicios de sesión inusuales

Es normal ver errores en la autenticación de usuarios. Pero observar patrones o bloques de errores puede ser un indicador de que algo sucede con la identidad de un usuario. Por ejemplo, puede ser el caso de ataques por difusión de contraseña o por fuerza bruta, o cuando una cuenta de usuario está en peligro. Es fundamental supervisar y notificar alertas cuando surjan estos patrones. Esto ayuda a garantizar que puede proteger al usuario y los datos de su organización. 

Puede que parezca que el éxito significa que todo está bien. Pero puede significar que un actor malintencionado ha accedido correctamente a un servicio. La supervisión de inicios de sesión correctos le ayuda a detectar cuentas de usuario que han obtenido acceso pero no son cuentas de usuario que deban tenerlo. Las operaciones correctas de autenticación de usuario son entradas normales en los registros de inicios de sesión de Azure AD. Se recomienda supervisar y enviar alertas cuando surjan estos patrones. Esto ayuda a garantizar que puede proteger las cuentas de usuario y los datos de su organización. 


Cuando diseñe y ponga en marcha una estrategia de supervisión y alertas de registros, tenga en cuenta las herramientas disponibles en Azure Portal. Identity Protection le permite automatizar la detección, protección y corrección de riesgos basados en identidades. Identity Protection usa sistemas heurísticos y de aprendizaje automático con inteligencia para detectar riesgos y asignar una puntuación de riesgo a los usuarios e inicios de sesión. Los clientes pueden configurar directivas basadas en un nivel de riesgo para cuándo permitir o denegar el acceso, o permitir que el usuario se proteja de forma segura de un riesgo. Las siguientes detecciones de riesgo de Identity Protection informan de los niveles de riesgo en la actualidad:

| Elementos para supervisar | Nivel de riesgo | Where | Filtro o subfiltro | Notas |
| - | - | - | - | - |
| Credenciales filtradas: Detección de riesgo del usuario| Alto| Registros de detección de riesgo de Azure AD| Experiencia del usuario: credenciales filtradas <br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Inteligencia sobre amenazas de Azure AD: Detección de riesgo del usuario| Alto| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Inteligencia de Azure AD sobre amenazas <br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Detección de riesgos de inicio de sesión de direcciones IP anónimas| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Dirección IP anónima <br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Detección de riesgo de inicio de sesión de viaje atípico| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Viaje atípico <br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Token anómalo| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Token anómalo <br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Detección de riesgo de inicio de sesión de dirección IP vinculada a malware| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Dirección IP vinculada al malware <br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Detección de riesgos de inicio de sesión sospechoso en el explorador| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Explorador sospechoso <br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Propiedades de inicio de sesión desconocidas: detección de riesgo de inicio de sesión| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Propiedades de inicio de sesión desconocidas <br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Detección de riesgos de inicio de sesión de direcciones IP malintencionadas| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Dirección IP malintencionada<br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Reglas de manipulación sospechosa de la bandeja de entrada: Detección de riesgo de inicio de sesión| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Reglas de manipulación sospechosa de la bandeja de entrada<br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Difusión de contraseña: Detección de riesgo de inicio de sesión| Alto| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Difusión de contraseña<br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Detección de riesgo de inicio de sesión de viaje imposible| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Viaje imposible<br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Nuevo país: Detección de riesgo de inicio de sesión| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Nuevo país<br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Actividad desde una dirección IP anónima: Detección de riesgo de inicio de sesión| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Actividad desde una dirección IP anónima<br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Reenvío sospechoso desde la bandeja de entrada: Detección de riesgo de inicio de sesión| Varía| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Reenvío sospechoso desde la bandeja de entrada<br><br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |
| Inteligencia sobre amenazas de Azure AD: Detección de riesgo de inicio de sesión| Alto| Registros de detección de riesgo de Azure AD| Experiencia de usuario: Inteligencia de Azure AD sobre amenazas<br>API: Consulte el [tipo de recurso riskDetection: versión beta de Microsoft Graph](/graph/api/resources/riskdetection?view=graph-rest-beta&preserve-view=true)| Consulte [¿Qué es el riesgo? Azure AD Identity Protection](../identity-protection/concept-identity-protection-risks.md) |

Para más información, visite [¿Qué es Identity Protection?](../identity-protection/overview-identity-protection.md) 


### <a name="what-to-look-for"></a>Qué se debe tener presente

Configure la supervisión de los datos dentro de los registros de inicios de sesión de Azure AD para asegurarse de que las alertas se producen y cumplen las directivas de seguridad de su organización. A continuación se indican algunos ejemplos:

* **Autenticaciones erróneas**: como humanos, todos nos equivocamos con nuestras contraseñas de vez en cuando. Sin embargo, muchas autenticaciones erróneas pueden indicar que un actor malintencionado está intentando obtener acceso. Los ataques difieren en intensidad, pero pueden oscilar entre unos pocos intentos por hora o una frecuencia mucho mayor. Por ejemplo, la difusión de contraseñas suele atacar las contraseñas más débiles en muchas cuentas, mientras que los ataques por fuerza bruta intentan usar muchas contraseñas en cuentas seleccionadas. 

* **Autenticaciones interrumpidas**: una interrupción en Azure AD representa una inserción de un proceso adicional para realizar la autenticación, como la aplicación de un control en una directiva de entidad de certificación. Este es un evento normal y puede ocurrir si las aplicaciones no están configuradas correctamente. Pero cuando se detectan muchas interrupciones en una cuenta de usuario, esto puede indicar que algo sucede con esa cuenta. 

  * Por ejemplo, si filtró por un usuario en los registros de inicio de sesión y ve un gran volumen de apariciones del estado de inicio de sesión = Interrumpido y Acceso condicional = Error. Si profundiza más, puede que aparezca en los detalles de autenticación que la contraseña es correcta, pero que se requiere una autenticación sólida. Esto podría significar que el usuario no está completando la autenticación multifactor (MFA), lo que podría indicar que la contraseña del usuario está en peligro y que el actor malintencionado no puede realizar la autenticación multifactor.

* **Bloqueo inteligente**: Azure AD proporciona un servicio de bloqueo inteligente que presenta el concepto de ubicaciones conocidas y no conocidas en el proceso de autenticación. Una cuenta de usuario que visita una ubicación conocida podría autenticarse correctamente mientras que a un actor malintencionado no familiarizado con la misma ubicación se le bloquearía después de varios intentos. Busque las cuentas que se han bloqueado y realice una investigación adicional. 

* **Cambios de IP**: es normal ver a usuarios con origen en distintas direcciones IP. Sin embargo, con el modelo Confianza cero nunca se confía y las direcciones siempre se comprueban. Observar un gran volumen de direcciones IP e inicios de sesión con error puede ser un indicador de intrusión. Busque un patrón con muchas autenticaciones erróneas que se están llevando a cabo desde varias direcciones IP. Tenga en cuenta que las conexiones de red privada virtual (VPN) pueden provocar falsos positivos. Independientemente de las dificultades, se recomienda supervisar los cambios de direcciones IP y, si es posible, usar Azure AD Identity Protection para detectar y mitigar automáticamente estos riesgos.

* **Ubicaciones**: por lo general, se espera que una cuenta de usuario esté en la misma ubicación geográfica. También se esperan inicios de sesión desde ubicaciones en las que tenga empleados o relaciones empresariales. Cuando la cuenta del usuario llega desde otra ubicación internacional en menos tiempo del que debiera, es una señal de que la cuenta de usuario se está utilizando indebidamente. Tenga en cuenta que las VPN pueden provocar falsos positivos. Se recomienda que supervise las cuentas de usuario que inician sesión desde ubicaciones geográficamente lejanas y, si es posible, use Azure AD Identity Protection para detectar y mitigar automáticamente estos riesgos.

Para esta área de riesgo, es recomendable que supervise las cuentas de usuario estándar y las cuentas con privilegios, pero dé prioridad a las investigaciones de las cuentas con privilegios. Las cuentas con privilegios son las cuentas más importantes de cualquier inquilino de Azure AD. Para obtener detalles específicos sobre las cuentas con privilegios, consulte Operaciones de seguridad: Cuentas con privilegios. 

### <a name="how-to-detect"></a>Detección

Puede usar Azure Identity Protection y los registros de inicio de sesión de Azure AD para ayudar a detectar amenazas indicadas por características de inicio de sesión inusuales. La información sobre Identity Protection está disponible en [¿Qué es Identity Protection?](../identity-protection/overview-identity-protection.md) También puede replicar los datos en Azure Monitor o un SIEM con fines de supervisión y alerta. Para definir lo normal para su entorno y establecer una base de referencia, determine lo siguiente:

* Los parámetros que considere normales para la base de usuarios.

* El número medio de intentos de una contraseña durante un período de tiempo antes de que el usuario llame al servicio de asistencia o realice un autoservicio de restablecimiento de contraseña.

* Número de intentos con error que desea permitir antes de que se envíe una alerta y si será diferente para las cuentas de usuario y las cuentas con privilegios.

* Número de intentos de autenticación multifactor que desea permitir antes de que se envíe una alerta y si será diferente para las cuentas de usuario y las cuentas con privilegios.

* Si la autenticación heredada está habilitada y el plan de acción para interrumpir el uso. 

* Las direcciones IP de salida conocidas son para su organización.

* Los países desde los que operan los usuarios.

* Si hay grupos de usuarios que permanecen en el mismo país o ubicación de red.

* Identifique cualquier otro indicador de inicios de sesión inusuales que sean específicos de su organización. Por ejemplo, días u horas de la semana o año en los que la organización no trabaja.

Una vez que haya limitado el ámbito de lo que es normal para los tipos de cuentas de su entorno, tenga en cuenta lo siguiente para ayudar a determinar los escenarios que desea supervisar y los casos en los que se deben generar alertas, y para ajustar estas.

* ¿Necesita supervisar y alertar si Identity Protection está configurado?

* ¿Se aplican condiciones más estrictas a las cuentas con privilegios que puede usar para supervisar y alertar? Por ejemplo, solo se requieren cuentas con privilegios desde direcciones IP de confianza.

* ¿Son demasiado agresivas las bases de referencia establecidas? Tener demasiadas alertas puede dar lugar a que las alertas se ignoren o no se tengan en cuenta.

Configure Identity Protection para garantizar que la protección vigente permita cumplir las directivas de la base de referencia de seguridad. Por ejemplo, bloquear a los usuarios si el riesgo es alto. Este nivel de riesgo indica con un alto grado de confianza que una cuenta de usuario está en peligro. Para más información sobre cómo configurar directivas de riesgo de inicio de sesión y de usuario, visite [Directivas de Identity Protection](../identity-protection/concept-identity-protection-policies.md). Para más información sobre cómo configurar el acceso condicional, visite [Acceso condicional: Acceso condicional basado en el riesgo del inicio de sesión](../conditional-access/howto-conditional-access-policy-risk.md).

A continuación se enumeran por orden de importancia en función del impacto y la gravedad de las entradas.

### <a name="monitoring-for-failed-unusual-sign-ins"></a>Supervisión de inicios de sesión inusuales con error

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Intentos de inicio de sesión con errores.| Medio: si se produjo un incidente aislado<br>Alto: si varias cuentas tienen el mismo patrón o una VIP.| Registro de inicios de sesión de Azure AD| Estado: Error<br>- y -<br>Código de error 50126 de inicio de sesión <br>Se ha producido un error al validar las credenciales debido a un nombre de usuario o contraseña no válidos.| Defina un umbral de línea de base y, a continuación, supervise y ajuste para adaptar los comportamientos de la organización y limitar la generación de alertas falsas. |
| Eventos de bloqueo inteligente.| Medio: si se produjo un incidente aislado<br>Alto: si varias cuentas tienen el mismo patrón o una VIP.| Registro de inicios de sesión de Azure AD| Estado: Error<br>- y -<br>Código de error de inicio de sesión = 50053 – IdsLocked| Defina un umbral de línea de base y, a continuación, supervise y ajuste para adaptar los comportamientos de la organización y limitar la generación de alertas falsas. |
| Interrupciones| Medio: si se produjo un incidente aislado<br>Alto: si varias cuentas tienen el mismo patrón o una VIP.| Registro de inicios de sesión de Azure AD| 500121, Error de autenticación durante la solicitud de autenticación sólida. <br>O bien<br>50097, se requiere autenticación de dispositivo, o bien, 50074, se requiere autenticación sólida. <br>O bien<br>50155, DeviceAuthenticationFailed<br>O bien<br>50158, ExternalSecurityChallenge: no se cumplió el desafío de seguridad externo.<br>O bien<br>53003 y Motivo del error = bloqueado por la CA| Supervisión y alertas en interrupciones.<br>Defina un umbral de línea de base y, a continuación, supervise y ajuste para adaptar los comportamientos de la organización y limitar la generación de alertas falsas. |


A continuación se enumeran por orden de importancia en función del impacto y la gravedad de las entradas.

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Alertas de fraude de autenticación multifactor (MFA).| Alto| Registro de inicios de sesión de Azure AD| Estado: Error<br>- y -<br>Detalles = MFA denegado<br>| Supervisión y alerta sobre cualquier entrada. |
| Autenticaciones con errores desde países en los que la organización no opera.| Media| Registro de inicios de sesión de Azure AD| Ubicación = \<unapproved location\>| Supervisión y alerta sobre cualquier entrada. |
| Autenticaciones con error de protocolos heredados o protocolos que no se usan.| Media| Registro de inicios de sesión de Azure AD| Estado = error<br>- y -<br>Aplicación cliente = Otros clientes, POP, IMAP, MAPI, SMTP, ActiveSync| Supervisión y alerta sobre cualquier entrada. |
| Errores bloqueados por la CA.| Media| Registro de inicios de sesión de Azure AD| Código de error = 53003 <br>- y -<br>Motivo del error = bloqueado por la CA| Supervisión y alerta sobre cualquier entrada. |
| Han aumentado las autenticaciones con errores de cualquier tipo.| Media| Registro de inicios de sesión de Azure AD| Captura un aumento de los errores en todo el panel. Por ejemplo, el total de errores de hoy es superior en un 10 % al del mismo día de la semana anterior.| Si no tiene un umbral establecido, supervise y alerte si los errores aumentan un 10 % o más. |
| La autenticación se produce en momentos y días de la semana en los que los países no suelen realizar operaciones empresariales.| Bajo| Registro de inicios de sesión de Azure AD| Captura autenticaciones interactivas que se producen fuera de los días y horas de trabajo normales. <br>Estado = correcto<br>- y -<br>Ubicación = \<location\><br>- y -<br>Day\Time = \<not normal working hours\>| Supervisión y alerta sobre cualquier entrada. |
| Cuenta deshabilitada o bloqueada para inicios de sesión| Bajo| Registro de inicios de sesión de Azure AD| Estado = Error<br>- y -<br>Código de error: 50057, la cuenta de usuario está deshabilitada.| Esto podría indicar que alguien está intentando obtener acceso a una cuenta una vez que ha dejado una organización. Aunque la cuenta está bloqueada, sigue siendo importante registrar y esta actividad y alertar sobre esta actividad. |


### <a name="monitoring-for-successful-unusual-sign-ins"></a>Supervisión de inicios de sesión inusuales correctos

 | Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - |- |- |- |- |
| Autenticaciones de cuentas con privilegios fuera de los controles esperados.| Alto| Registro de inicios de sesión de Azure AD| Estado = correcto<br>- y -<br>UserPricipalName = \<Admin account\><br>- y -<br>Ubicación = \<unapproved location\><br>- y -<br>Dirección IP = \<unapproved IP\><br>Información del dispositivo = \<unapproved Browser, Operating System\><br>| Supervise y alerte sobre autenticaciones correctas de cuentas con privilegios fuera de los controles esperados. Se enumeran tres controles comunes. |
| Cuando solo se requiere la autenticación de un solo factor.| Bajo| Registro de inicios de sesión de Azure AD| Estado = correcto<br>Requisito de autenticación = Autenticación de un solo factor| Supervise esto periódicamente y asegúrese de que este es el comportamiento esperado. |
| Detectar cuentas con privilegios no registradas para MFA.| Alto| Azure Graph API| Consulta de IsMFARegistered eq false para cuentas de administrador. <br>[Enumeración de los detalles de credentialUserRegistration:Microsoft Graph beta] | Microsoft Docs](/graph/api/reportroot-list-credentialuserregistrationdetails?view=graph-rest-beta&preserve-view=true&tabs=http)| Audite e investigue para determinar si es intencionada o de vigilancia. |
| Las autenticaciones correctas desde países en los que su organización no trabaja.| Media| Registro de inicios de sesión de Azure AD| Estado = correcto<br>Ubicación = \<unapproved country\>| Supervise y alerte de las entradas que no sean iguales a los nombres de ciudad que proporcione. |
| Autenticación correcta, sesión bloqueada por la entidad de certificación.| Media| Registro de inicios de sesión de Azure AD| Estado = correcto<br>- y -<br>código de error = 53003: motivo del error, bloqueado por la CA| Supervise e investigue si se realiza correctamente la autenticación, pero la sesión está bloqueada por la entidad de certificación. |
| Autenticación correcta después de deshabilitar la autenticación heredada.| Media| Registro de inicios de sesión de Azure AD| estado = correcto <br>- y -<br>Aplicación cliente = Otros clientes, POP, IMAP, MAPI, SMTP, ActiveSync| Si su organización ha deshabilitado la autenticación heredada, supervise y alerte cuando se haya realizado correctamente la autenticación heredada. |


De forma periódica, se recomienda revisar las autenticaciones para aplicaciones de impacto empresarial medio (MBI) y de alto impacto empresarial (HBI) en las que solo se requiere autenticación de un solo factor. Para cada uno de ellas, quiere determinar si se esperaba o no la autenticación de un solo factor. Además, revise si aumentan las autenticaciones correctas o en momentos inesperados según la ubicación. 

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| - | - |- |- |- |
| Autenticaciones en aplicaciones de MBI y HBI mediante la autenticación de un solo factor.| Bajo| Registro de inicios de sesión de Azure AD| estado = correcto<br>- y -<br>Identificador de aplicación = \<HBI app\> <br>- y -<br>Requisito de autenticación = Autenticación de un solo factor.| Revise y valide que esta configuración sea intencionada. |
| Autenticaciones en días y horas de la semana o año en que los países no realizan operaciones empresariales normalmente.| Bajo| Registro de inicios de sesión de Azure AD| Captura autenticaciones interactivas que se producen fuera de los días y horas de trabajo normales. <br>Estado = correcto<br>Ubicación = \<location\><br>Fecha y hora = \<not normal working hours\>| Supervise y alerte sobre autenticaciones en días y horas de la semana o año en que los países no realizan operaciones empresariales normalmente. |
| Aumento medible de inicios de sesión correctos.| Bajo| Registro de inicios de sesión de Azure AD| Se detecta un aumento en las autenticaciones correctas en el panel. Por ejemplo, el total de autenticaciones correctas de hoy es superior en un 10 % al del mismo día de la semana anterior.| Si no tiene un umbral establecido, supervise y alerte si las autenticaciones correctas aumentan un 10 % o más. |

## <a name="next-steps"></a>Pasos siguientes
Consulte estos artículos de la guía de operaciones de seguridad:

[Introducción a las operaciones de seguridad de Azure AD](security-operations-introduction.md)

[Operaciones de seguridad para cuentas de usuario](security-operations-user-accounts.md)

[Operaciones de seguridad para cuentas con privilegios](security-operations-privileged-accounts.md)

[Operaciones de seguridad para Privileged Identity Management](security-operations-privileged-identity-management.md)

[Operaciones de seguridad para aplicaciones](security-operations-applications.md)

[Operaciones de seguridad para dispositivos](security-operations-devices.md)
 
[Operaciones de seguridad para infraestructura](security-operations-infrastructure.md)
