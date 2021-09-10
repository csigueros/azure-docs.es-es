---
title: Operaciones de seguridad de Azure Active Directory para aplicaciones
description: Obtenga información sobre cómo supervisar y enviar alertas sobre aplicaciones para identificar amenazas de seguridad.
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
ms.openlocfilehash: aaa8f116680b3876eebb2b96a4f4c5e40d6384a2
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732343"
---
# <a name="azure-active-directory-security-operations-guide-for-applications"></a>Guía de operaciones de seguridad de Azure Active Directory para aplicaciones

Las aplicaciones proporcionan una superficie de ataque para las infracciones de seguridad y deben supervisarse. Aunque no son objetivos de ataque tan frecuentes como las cuentas de usuario, pueden producirse infracciones. Dado que las aplicaciones a menudo se ejecutan sin intervención humana, los ataques pueden ser más difíciles de detectar.

En este artículo se proporcionan instrucciones para supervisar y alertar sobre eventos de aplicaciones. Se actualizan periódicamente para ayudar a garantizar lo siguiente:

* Impedir que las aplicaciones malintencionadas obtengan acceso injustificado a los datos.

* Evitar que los actores malintencionados ataquen a las aplicaciones existentes.

* Recopilar información que le permita compilar y configurar nuevas aplicaciones de forma más segura.

Si no está familiarizado con cómo funcionan las aplicaciones en Azure Active Directory (Azure AD), consulte [Aplicaciones y entidades de servicio en Azure AD](../develop/app-objects-and-service-principals.md).

> [!NOTE]
> Si aún no ha revisado la [información general sobre las operaciones de seguridad de Azure Active Directory](security-operations-introduction.md), puede hacerlo ahora si lo desea.

## <a name="what-to-look-for"></a>Qué se debe tener presente

A medida que supervisa los registros de aplicaciones en busca de incidentes de seguridad, revise lo siguiente para ayudar a diferenciar la actividad normal de la malintencionada. Los siguientes eventos pueden indicar problemas de seguridad y cada uno de ellos se trata en el resto del artículo.

* Cualquier cambio que se produzca fuera de los procesos y programaciones empresariales normales.

* Cambios en las credenciales de las aplicaciones

* Permisos de aplicación

   * Entidad de servicio asignada a un rol de Azure AD o Azure RBAC.

   * Aplicaciones a las que se conceden permisos con privilegios elevados.

   * Cambios en Azure Key Vault.

   * Usuario final que otorga consentimiento a las aplicaciones.

   * Se detuvo el consentimiento del usuario final en función del nivel de riesgo.

* Cambios de configuración de las aplicaciones

   * Identificador de recursos universal (URI) cambiado o no estándar.

   * Cambios en los propietarios de las aplicaciones.

   * Direcciones URL de cierre de sesión modificadas.

## <a name="where-to-look"></a>Dónde buscar

Los archivos de registro que usa para la investigación y supervisión son:

* [Registros de auditoría de Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Registros de inicio de sesión](../reports-monitoring/concept-all-sign-ins.md)

* [Registros de auditoría de Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview?view=o365-worldwide)

* [Registros de Azure Key Vault](../../key-vault/general/logging.md)

En Azure Portal, puede ver los registros de auditoría de Azure AD y descargarlos como archivos de valores separados por comas (CSV) o notación de objetos JavaScript (JSON). Azure Portal tiene varias maneras de integrar los registros de Azure AD con otras herramientas que permiten una mayor automatización de la supervisión y las alertas:

* **[Azure Sentinel](../../sentinel/overview.md)** : permite el análisis de seguridad inteligente en el nivel empresarial al ofrecer funcionalidades de administración de eventos e información de seguridad (SIEM). 

* **[Azure Monitor](../../azure-monitor/overview.md)** : permite la supervisión y la generación de alertas automatizadas de diversas condiciones. Puede crear o usar libros para combinar datos de orígenes diferentes.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) integrado con una instancia de SIEM**- [: los registros de Azure AD se pueden integrar con otras instancias de SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md), como Splunk, ArcSight, QRadar y Sumo Logic a través de la integración de Azure Event Hubs.

* **[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS)** : le permite detectar y administrar aplicaciones, controlar entre aplicaciones y recursos y comprobar el cumplimiento de las aplicaciones en la nube.

Gran parte de lo que supervisará y alertará son los efectos de las directivas de acceso condicional. Puede usar el [libro Información detallada e informes del acceso condicional](../conditional-access/howto-conditional-access-insights-reporting.md) para examinar los efectos de una o varias directivas de acceso condicional en los inicios de sesión, así como los resultados de las directivas, incluido el estado del dispositivo. Este libro le permite ver un resumen de impacto e identificar el impacto durante un período de tiempo específico. También puede usar el libro para investigar los inicios de sesión de un usuario específico. 

 En el resto de este artículo se describe lo que se recomienda supervisar y alertar, y se organiza por el tipo de amenaza. Cuando hay soluciones previamente creadas y específicas, se establecen vínculos a ellas o se proporcionan ejemplos después de la tabla. De lo contrario, puede crear alertas mediante las herramientas anteriores. 

## <a name="application-credentials"></a>Credenciales de aplicaciones

Muchas aplicaciones usan credenciales para autenticarse en Azure AD. Las credenciales adicionales agregadas fuera de los procesos esperados podrían ser un actor malintencionado que use esas credenciales. Se recomienda encarecidamente usar certificados X509 emitidos por entidades de confianza o administradas en lugar de usar secretos de cliente. Sin embargo, si necesita usar secretos de cliente, siga las prácticas de protección adecuadas para mantener seguras las aplicaciones. Tenga en cuenta que las actualizaciones de las aplicaciones y la entidad de servicio se registran como dos entradas en el registro de auditoría. 

* Supervise las aplicaciones para identificar aquellas con tiempos de expiración de credenciales largos.

* Reemplace las credenciales de larga duración por credenciales que tengan una duración corta. Tome medidas para asegurarse de que las credenciales no se confirman en los repositorios de código y se almacenan de forma segura.


| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
| -|-|-|-|-|
| Se han agregado credenciales a las aplicaciones existentes.| Alto| Registros de auditoría de Azure AD| Servicio/Directorio principal, Category-ApplicationManagement <br>Actividad: Actualización de certificados de aplicaciones y administración de secretos<br>- y -<br>Actividad: Actualización de entidad de servicio y aplicación| Alerta cuando las credenciales:<li> Se hayan agregado fuera del horario comercial o de los flujos de trabajo normales.<li> Sean de tipos que no se usan en el entorno.<li> Se hayan agregado a una entidad de servicio que admite un flujo que no es SAML. |
| Sean credenciales que tengan una duración mayor de la que permiten las directivas.| Media| Microsoft Graph| Estado y fecha de finalización de las credenciales de clave de aplicación<br>- y -<br>Credenciales de contraseña de aplicación| Puede usar Microsoft Graph API para buscar la fecha de inicio y finalización de las credenciales y evaluar las que tienen una duración mayor de la permitida. Consulte el script de PowerShell que sigue a esta tabla. |

 Están disponibles las siguientes alertas y supervisión pregeneradas.

* Azure Sentinel. [Se envía una alerta cuando se agregan nuevas credenciales de entidad de servicio o de aplicación.](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/AuditLogs/NewAppOrServicePrincipalCredential.yaml) 

* Azure Monitor: [Libro de Azure AD para ayudarlo a evaluar el riesgo de Solorigate: Microsoft Tech Community.](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* MCAS. [Guía de investigación de alertas de detección de anomalías de Cloud App Security.](/cloud-app-security/investigate-anomaly-alerts)

* PowerShell. [Script de PowerShell de ejemplo para buscar la duración de las credenciales.](https://github.com/madansr7/appCredAge)

## <a name="application-permissions"></a>Permisos de aplicación

Al igual que una cuenta de administrador, a las aplicaciones se les pueden asignar roles con privilegios. A las aplicaciones se les pueden asignar roles de Azure AD, como Administrador global, o roles Azure RBAC, como Propietario de la suscripción. Dado que pueden ejecutarse sin un usuario presente y como un servicio en segundo plano, supervise concienzudamente cada vez que se conceda a una aplicación un rol o permiso con privilegios elevados. 

### <a name="service-principal-assigned-to-a-role"></a>Entidad de servicio asignada a un rol


| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
|-|-|-|-|-|
| Aplicación asignada a un rol Azure RBAC o de Azure AD| De alto a medio| Registros de auditoría de Azure AD| Tipo: entidad de servicio<br>Actividad: "Agregar miembro al rol" o "Agregar miembro apto al rol"<br>O bien<br>"Agregar miembro con ámbito a rol".| En el caso de roles con privilegios elevados, como el administrador global, el riesgo es alto. Para los roles con menos privilegios, el riesgo es medio. Se envía una alerta cada vez que se asigna una aplicación a un rol de Azure o de Azure AD fuera de los procedimientos normales de configuración o administración de cambios. |

### <a name="application-granted-highly-privileged-permissions"></a>Aplicación con permisos con privilegios elevados

Las aplicaciones también deben seguir el principio de privilegios mínimos. Investigue los permisos de aplicación para asegurarse de que son realmente necesarios. Puede crear un [informe de concesión de consentimiento de aplicaciones](https://aka.ms/getazureadpermissions) para ayudar a identificar las aplicaciones existentes y resaltar los permisos con privilegios.

| Elementos para supervisar|Nivel de riesgo|Where| Filtro o subfiltro| Notas|
|-|-|-|-|-|
| Aplicación a la que se le han concedido permiso con privilegios elevados, como permisos con " *.All" (Directory.ReadWrite.All) o permisos de amplio alcance (Mail.* )| Alto |Registros de auditoría de Azure AD| "Agregar la asignación de roles de aplicación a la entidad de servicio" <br>-donde-<br> Los destinos identifican una API con datos confidenciales (como Microsoft Graph) <br>- y -<br>AppRole.Value identifica un permiso de aplicación con privilegios elevados (rol de aplicación).| Las aplicaciones concedieron permisos amplios, como " *.All” (Directory.ReadWrite.All) o permisos de amplio alcance (Mail.* ) |
| Administrador que concede permisos de aplicación (roles de aplicación) o permisos delegados con privilegios elevados |Alto| Portal de Microsoft 365| "Agregar la asignación de roles de aplicación a la entidad de servicio" <br>-donde-<br>Los destinos identifican una API con datos confidenciales (como Microsoft Graph)<br>"Agregar concesión de permisos delegados" <br>-donde-<br>Los destinos identifican una API con datos confidenciales (como Microsoft Graph) <br>- y -<br>DelegatedPermissionGrant.Scope incluye permisos con privilegios elevados.| Se envía una alerta cuando un administrador global, un administrador de aplicaciones o un administrador de aplicaciones en la nube da su consentimiento a una aplicación. Analice especialmente consentimientos fuera de la actividad normal y los procedimientos de cambio. |
| A la aplicación se le conceden permisos para Microsoft Graph, Exchange, SharePoint o Azure AD |Alto| Registros de auditoría de Azure AD| "Agregar concesión de permisos delegados" <br>O bien<br>"Agregar la asignación de roles de aplicación a la entidad de servicio" <br>-donde-<br>Los destinos identifican una API con datos confidenciales (como Microsoft Graph, Exchange Online, entre otros)| Se envía una alerta como en la fila anterior. |
| Se conceden permisos de aplicación (roles de aplicación) para otras API |Media| Registros de auditoría de Azure AD| "Agregar la asignación de roles de aplicación a la entidad de servicio" <br>-donde-<br>Los destinos identifican cualquier otra API.| Se envía una alerta como en la fila anterior. |
| Los permisos delegados con privilegios elevados se conceden en nombre de todos los usuarios. |Alto| Registros de auditoría de Azure AD| "Agregar concesión de permisos delegados", donde los destinos identifican una API con datos confidenciales (como Microsoft Graph). <br> DelegatedPermissionGrant.Scope incluye permisos con privilegios elevados. <br>- y -<br>DelegatedPermissionGrant.ConsentType es "AllPrincipals".| Se envía una alerta como en la fila anterior. |

Para obtener más información sobre la supervisión de permisos de aplicación, consulte este tutorial: [Investigación y corrección de aplicaciones de OAuth de riesgo](/cloud-app-security/investigate-risky-oauth).

### <a name="azure-key-vault"></a>Azure Key Vault

Azure Key Vault se puede usar para almacenar los secretos del inquilino. Se recomienda prestar especial atención a los cambios en la configuración y las actividades de Key Vault. 

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
|-|-|-|-|-|
| Cómo y cuándo se accede a los almacenes de claves y quién lo hace| Media| [Registros de Azure Key Vault](../../key-vault/general/logging.md?tabs=Vault)| Tipo de recurso: Instancias de Key Vault| Buscar <li> Cualquier acceso a Key Vault fuera de los procesos y horas normales. <li> Cualquier cambio en la ACL de Key Vault. |

Después de configurar Azure Key Vault, asegúrese de [habilitar el registro](../../key-vault/general/howto-logging.md?tabs=azure-cli), que muestra [cómo y cuándo se accede a las instancias de Key Vault](../../key-vault/general/logging.md?tabs=Vault) y [configurar las alertas](../../key-vault/general/alert.md) en Key Vault para notificar a los usuarios asignados o a las listas de distribución por correo electrónico, llamada, mensaje de texto o notificación de la [cuadrícula de eventos](../../key-vault/general/event-grid-overview.md) si el estado se ve afectado. Además, la configuración de la [supervisión](../../key-vault/general/alert.md) con la información detallada de Key Vault le proporcionará una instantánea de las solicitudes, el rendimiento, los fallos y la latencia de Key Vault. [Log Analytics](../../azure-monitor/logs/log-analytics-overview.md) también tiene algunas [consultas de ejemplo](../../azure-monitor/logs/queries.md) para Azure Key Vault a las que se puede acceder después de seleccionar el Key Vault y, a continuación, en "Supervisión", seleccionar "Registros".

### <a name="end-user-consent"></a>Consentimiento del usuario final

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
|-|-|-|-|-|
| Consentimiento del usuario final a la aplicación| Bajo| Registros de auditoría de Azure AD| Actividad: Consentimiento a la aplicación / ConsentContext.IsAdminConsent = false| Buscar: <li>Cuentas de perfil alto o con privilegios elevados.<li> La aplicación solicita permisos de alto riesgo.<li>Aplicaciones con nombres sospechosos, por ejemplo, genéricos, mal escritos, etc. |


El acto de dar su consentimiento a una aplicación no es en sí mismo malintencionado. Sin embargo, investigue las nuevas concesiones de consentimiento del usuario final en busca de aplicaciones sospechosas. Puede [restringir las operaciones de consentimiento del usuario](../../security/fundamentals/steps-secure-identity.md).

Para obtener más información sobre las especificaciones de consentimiento, vea los recursos siguientes:

* [Administración del consentimiento en las aplicaciones y evaluación de las solicitudes de consentimiento en Azure Active Directory](../manage-apps/manage-consent-requests.md)

* [Detección y corrección de las opciones ilegales para otorgar consentimiento - Office 365](/microsoft-365/security/office-365-security/detect-and-remediate-illicit-consent-grants?view=o365-worldwide)

* [Cuaderno de reproducción de respuesta a incidentes: investigación de concesión de consentimiento de aplicación](/security/compass/incident-response-playbook-app-consent)

### <a name="end-user-stopped-due-to-risk-based-consent"></a>EL usuario final se detuvo debido a un consentimiento basado en riesgos 

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
|-|-|-|-|-|
| El consentimiento del usuario final se detuvo debido a un consentimiento basado en riesgos| Media| Registros de auditoría de Azure AD| Directorio principal/ApplicationManagement/Consentimiento a la aplicación<br> Motivo del estado de error: Microsoft.online.Security.userConsent<br>BlockedForRiskyAppsExceptions| Supervise y analice cada vez que se detenga el consentimiento debido al riesgo. Buscar:<li>Cuentas de perfil alto o con privilegios elevados.<li> La aplicación solicita permisos de alto riesgo.<li>Aplicaciones con nombres sospechosos, por ejemplo, genéricos, mal escritos, etc. |

## <a name="application-configuration-changes"></a>Cambios de configuración de las aplicaciones

Supervise los cambios en la configuración de cualquier aplicación. En concreto, la configuración cambia al identificador uniforme de recursos (URI), la propiedad y la dirección URL de cierre de sesión.

### <a name="dangling-uri-and-redirect-uri-changes"></a>Cambios de URI y URI de redirección pendientes 

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
|-|-|-|-|-|
| URI pendiente| Alto| Registros de Azure AD y registro de aplicaciones| Servicio/Directorio principal, Category-ApplicationManagement<br>Actividad: Actualización de aplicación<br>Éxito: nombre de propiedad AppAddress| Busque URI pendientes, por ejemplo, que apunten a un nombre de dominio que ya no existe o a uno que no posee explícitamente. |
| Cambios de configuración de URI de redirección| Alto| Registros de Azure AD| Servicio/Directorio principal, Category-ApplicationManagement<br>Actividad: Actualización de aplicación<br>Éxito: nombre de propiedad AppAddress| Busque URI que no usen HTTPS*, URI con caracteres comodín al final o el dominio de la dirección URL, URI que no sean exclusivos de la aplicación, URI que apunten a un dominio que no controle. |

Se envía una alerta cada vez que se detecten estos cambios.

### <a name="appid-uri-added-modified-or-removed"></a>URI de AppID agregado, modificado o quitado


| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
|-|-|-|-|-|
| Cambios en el URI de AppID| Alto| Registros de Azure AD| Servicio/Directorio principal, Category-ApplicationManagement<br>Actividad: Actualización<br>Application<br>Actividad: Actualización de la entidad de servicio| Busque las modificaciones del URI de AppID, como agregar, modificar o quitar el URI. |


Se envía una alerta cada vez que se detecten estos cambios fuera de los procedimientos de administración de cambios aprobados.

### <a name="new-owner"></a>Nuevo propietario


| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
|-|-|-|-|-|
| Cambios en la propiedad de las aplicaciones| Media| Registros de Azure AD| Servicio/Directorio principal, Category-ApplicationManagement<br>Actividad: Incorporación de un propietario a una aplicación| Busque cualquier instancia de un usuario que se agrega como propietario de la aplicación fuera de las actividades normales de administración de cambios. |

### <a name="logout-url-modified-or-removed"></a>URL de cierre de sesión modificada o quitada

| Elementos para supervisar| Nivel de riesgo| Where| Filtro o subfiltro| Notas |
|-|-|-|-|-|
| Cambios en la dirección URL de cierre de sesión| Bajo| Registros de Azure AD| Servicio/Directorio principal, Category-ApplicationManagement<br>Actividad: Actualización de aplicación<br>- y -<br>Actividad: Actualización de la entidad de servicio| Busque cualquier modificación en una dirección URL de cierre de sesión. Las entradas en blanco o las entradas en ubicaciones inexistentes impedirían que un usuario terminara una sesión. |

## <a name="additional-resources"></a>Recursos adicionales

A continuación se incluyen vínculos a recursos útiles:

* Kit de herramientas de Azure AD Github: [https://github.com/microsoft/AzureADToolkit](https://github.com/microsoft/AzureADToolkit)

* Guía de seguridad e información general de seguridad de Azure Key Vault: [Información general de seguridad de Azure Key Vault](../../key-vault/general/security-features.md)

* Información y herramientas de riesgo de Solorgate: [Libro de Azure AD para ayudarlo a acceder al riesgo de Solorgate](https://techcommunity.microsoft.com/t5/azure-active-directory-identity/azure-ad-workbook-to-help-you-assess-solorigate-risk/ba-p/2010718)

* Guía de detección de ataques de OAuth: [Adición inusual de credenciales a una aplicación de OAuth](/cloud-app-security/investigate-anomaly-alerts)

Información de configuración de supervisión de Azure AD para SIEM: [Herramientas de asociados con integración de Azure Monitor](../..//azure-monitor/essentials/stream-monitoring-data-event-hubs.md)

 ## <a name="next-steps"></a>Pasos siguientes

Consulte estos artículos de la guía de operaciones de seguridad:

[Introducción a las operaciones de seguridad de Azure AD](security-operations-introduction.md)

[Operaciones de seguridad para cuentas de usuario](security-operations-user-accounts.md)

[Operaciones de seguridad para cuentas con privilegios](security-operations-privileged-accounts.md)

[Operaciones de seguridad para Privileged Identity Management](security-operations-privileged-identity-management.md)

[Operaciones de seguridad para aplicaciones](security-operations-applications.md)

[Operaciones de seguridad para dispositivos](security-operations-devices.md)

 
[Operaciones de seguridad para infraestructura](security-operations-infrastructure.md)