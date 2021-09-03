---
title: Guía de operaciones de seguridad de Azure Active Directory
description: Aprenda a supervisar, identificar y alertar sobre incidencias de seguridad con cuentas, aplicaciones, dispositivos e infraestructura
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
ms.openlocfilehash: bef4e1b8f4524e9b883bfe4aa5ef7b2a0e2bcf83
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121746086"
---
# <a name="azure-active-directory-security-operations-guide"></a>Guía de operaciones de seguridad de Azure Active Directory

Microsoft tiene un enfoque exitoso y probado para la [seguridad de Confianza cero](https://aka.ms/Zero-Trust) utilizando principios de [Defensa a fondo](https://us-cert.cisa.gov/bsi/articles/knowledge/principles/defense-in-depth) que aprovechan la identidad como un plano de control. A medida que las organizaciones siguen adoptando un mundo híbrido de cargas de trabajo para la escala, el ahorro de costos y la seguridad, Azure Active Directory (Azure AD) desempeña un papel fundamental en la estrategia de administración de identidades. Recientemente, las noticias sobre el compromiso de la identidad y la seguridad han hecho que los equipos de TI empresariales consideren cada vez más su postura de seguridad de la identidad como una medida del éxito de la seguridad defensiva.

Cada vez más, las organizaciones deben adoptar una combinación de aplicaciones locales y en la nube, a las que los usuarios acceden con cuentas locales y de solo nube. La administración de usuarios, aplicaciones y dispositivos tanto en el ámbito local como en la nube plantea escenarios complicados.

Azure Active Directory crea una identidad de usuario común para la autenticación y autorización en todos los recursos, independientemente de la ubicación. A esto le llamamos identidad híbrida. 

Para lograr la identidad híbrida con Azure AD, se puede usar uno de tres métodos de autenticación, en función de los escenarios. Los tres métodos son:

* [Sincronización de hash de contraseña (PHS)](../hybrid/whatis-phs.md)

* [Autenticación de paso a través (PTA)](../hybrid/how-to-connect-pta.md)

* [Federación (AD FS)](../hybrid/whatis-fed.md)

A medida que audite las operaciones de seguridad actuales o establezca operaciones de seguridad para su entorno de Azure, es recomendable que:

* Lea partes específicas de la guía de seguridad de Microsoft para establecer una línea de base de conocimiento sobre la protección del entorno de Azure híbrido o basado en la nube.

* Audite su estrategia de cuenta y contraseña y los métodos de autenticación para ayudar a disuadir a los vectores de ataque más comunes.

* Cree una estrategia de supervisión continua y alertas sobre actividades que puedan indicar una amenaza de seguridad.

## <a name="audience"></a>Público

La guía de operaciones de seguridad de Azure AD está pensada para equipos empresariales de operaciones de seguridad e identidades de TI y proveedores de servicios administrados que necesitan contrarrestrar las amenazas a través de una mejor configuración de seguridad de identidades y perfiles de supervisión. Esta guía es especialmente pertinente para los administradores de TI y los arquitectos de identidades que aconsejan a los equipos de pruebas de penetración y defensivos del centro de operaciones de seguridad (SOC) mejorar y mantener su posición de seguridad de identidad. 

## <a name="scope"></a>Ámbito

En esta introducción se proporcionan las recomendaciones de estrategia y auditoría de contraseñas y lecturas previas sugeridas. En este artículo también se proporciona información general sobre las herramientas disponibles para entornos híbridos de Azure, así como entornos de Azure totalmente basados en la nube. Por último, proporcionamos una lista de orígenes de datos que puede usar para supervisar y alertar, así como para configurar su entorno y estrategia de Administración de eventos e información de seguridad (SIEM). En el resto de las instrucciones se presentan estrategias de supervisión y alertas en las áreas siguientes:

* [Cuentas de usuario](security-operations-user-accounts.md): guía específica para las cuentas de usuario sin privilegios (por supuesto tampoco privilegios administrativos), incluida la creación y el uso anómalos de cuentas e inicios de sesión inusuales.

* [Cuentas con privilegios](security-operations-privileged-accounts.md): guía específica para las cuentas de usuario con privilegios que tienen permisos elevados para realizar tareas administrativas, incluidas las asignaciones de roles de Azure AD, las asignaciones de roles de recursos de Azure y la administración de acceso para recursos y suscripciones de Azure.

* [Privileged Identity Management (PIM)](security-operations-privileged-identity-management.md): guía específica para usar PIM para administrar, controlar y supervisar el acceso a los recursos. 

* [Aplicaciones](security-operations-applications.md): guía específica para las cuentas que se usan para proporcionar autenticación para las aplicaciones. 

* [Dispositivos](security-operations-devices.md): guía específica para la supervisión y las alertas de dispositivos registrados o unidos fuera de las directivas, uso no compatible, gestión de roles de administración de dispositivos e inicios de sesión en máquinas virtuales.

*  [Infraestructura](security-operations-infrastructure.md): guía específica para la supervisión y alertas sobre amenazas para los entornos híbridos y basados exclusivamente en la nube.

## <a name="important-reference-content"></a>Contenido de referencia importante

Microsoft tiene muchos productos y servicios que le permiten personalizar su entorno de TI para adaptarlo a sus necesidades. Como parte de la estrategia de supervisión y alertas, se recomienda revisar las instrucciones siguientes que son pertinentes para su entorno operativo:

* Sistemas operativos Windows

   * [Referencia de supervisión y auditoría de seguridad de Windows 10 y Windows Server 2016](https://www.microsoft.com/download/details.aspx?id=52630)

   * [Línea base de seguridad (FINAL) para Windows 10 v1909 y Windows Server v1909](https://techcommunity.microsoft.com/t5/microsoft-security-baselines/security-baseline-final-for-windows-10-v1909-and-windows-server/ba-p/1023093)

* Entornos locales

   * [Arquitectura de Microsoft Defender for Identity](/defender-for-identity/architecture)

   * [Inicio rápido: Conexión de Microsoft Defender for Identity a Active Directory](/defender-for-identity/install-step2)

   * [Línea base de seguridad de Azure paras Microsoft Defender for Identity](/defender-for-identity/security-baseline)

   * [Supervisión de Active Directory en busca de indicios de riesgo](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise)

* Entornos de Azure basados en la nube


   * [Supervisión de los inicios de sesión con el registro de inicios de sesión de Azure AD](../reports-monitoring/concept-all-sign-ins.md)

   * [Informes de actividad de auditoría en el portal de Azure Active Directory](../reports-monitoring/concept-audit-logs.md)

   * [Investigación de riesgos en Azure Active Directory Identity Protection](../identity-protection/howto-identity-protection-investigate-risk.md) 

   * [Conexión de datos de Azure AD Identity Protection a Azure Sentinel.](../../sentinel/connect-azure-ad-identity-protection.md)

* Active Directory Domain Services (AD DS)

   * [Recomendaciones de la directiva de auditoría](/windows-server/identity/ad-ds/plan/security-best-practices/audit-policy-recommendations)

* Active Directory Federation Services (AD FS)

   * [Solución de problemas de AD FS: auditoría de eventos y registro](/windows-server/identity/ad-fs/troubleshooting/ad-fs-tshoot-logging)

## <a name="data-sources"></a>Orígenes de datos 

Los archivos de registro que usa para la investigación y supervisión son:

* [Registros de auditoría de Azure AD](../reports-monitoring/concept-audit-logs.md)

* [Registros de inicio de sesión](../reports-monitoring/concept-all-sign-ins.md)

* [Registros de auditoría de Microsoft 365](/microsoft-365/compliance/auditing-solutions-overview?view=o365-worldwide)

* [Registros de Azure Key Vault](../../key-vault/general/logging.md?tabs=Vault)

En Azure Portal, puede ver los registros de auditoría de Azure AD y descargarlos como archivos de valores separados por comas (CSV) o notación de objetos JavaScript (JSON). Azure Portal tiene varias maneras de integrar los registros de Azure AD con otras herramientas que permiten una mayor automatización de la supervisión y las alertas:

* **[Azure Sentinel](../../sentinel/overview.md)** : permite el análisis de seguridad inteligente en el nivel empresarial proporcionando funcionalidades de Administración de eventos e información de seguridad (SIEM). 

* **[Azure Monitor](../../azure-monitor/overview.md)** : permite la supervisión automatizada y las alertas de diversas condiciones. Puede crear o usar libros para combinar datos de orígenes diferentes.

* **[Azure Event Hubs](../../event-hubs/event-hubs-about.md) (integrado con SIEM)** - [: los registros de Azure AD se pueden integrar con otras instancias de SIEM](../reports-monitoring/tutorial-azure-monitor-stream-logs-to-event-hub.md), como Splunk, ArcSight, QRadar y Sumo Logic a través de la integración de Azure Event Hubs.

* **[Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security) (MCAS)** : le permite detectar y administrar aplicaciones, controlar entre aplicaciones y recursos y comprobar el cumplimiento de las aplicaciones en la nube.

Gran parte de lo que supervisará y alertará son los efectos de las directivas de acceso condicional. Puede usar el [libro Información detallada e informes del acceso condicional](../conditional-access/howto-conditional-access-insights-reporting.md) para examinar los efectos de una o varias directivas de acceso condicional en los inicios de sesión, así como los resultados de las directivas, incluido el estado del dispositivo. Este libro le permite ver un resumen de impacto e identificar el impacto durante un período de tiempo específico. También puede usar el libro para investigar los inicios de sesión de un usuario específico. 

En el resto de este artículo se describe lo que se recomienda supervisar y alertar, y se organiza por el tipo de amenaza. Cuando hay soluciones previamente creadas y específicas, se establecen vínculos a ellas o se proporcionan ejemplos después de la tabla. De lo contrario, puede crear alertas mediante las herramientas anteriores. 

* **[Identity Protection](../identity-protection/overview-identity-protection.md)** : genera tres informes clave que puede usar para ayudar en la investigación:

   * **Usuarios de riesgo**: contiene información sobre qué usuarios están en riesgo, detalles sobre detecciones, historial de todos los inicios de sesión de riesgo e historial de riesgos.

   * **Inicios de sesión de riesgo**: contiene información que rodea la circunstancia de un inicio de sesión que puede indicar circunstancias sospechosas. Para obtener información adicional sobre cómo investigar información de este informe, visite [Instrucciones: Investigación de riesgos](../identity-protection/howto-identity-protection-investigate-risk.md). 

   *  **Detecciones de riesgo**: contiene información sobre las señales de riesgo detectadas por Azure AD Identity Protection que informa del inicio de sesión y del riesgo del usuario. Para obtener más información, consulte la [guía de operaciones de seguridad de Azure AD para cuentas de usuario](security-operations-user-accounts.md).

### <a name="data-sources-for-domain-controller-monitoring"></a>Orígenes de datos para la supervisión de controladores de dominio

Para obtener los mejores resultados, se recomienda supervisar los controladores de dominio mediante Microsoft Defender for Identity. Esto le permitirá obtener las mejores funcionalidades de detección y automatización. Siga las instrucciones de:

* [Arquitectura de Microsoft Defender for Identity](/defender-for-identity/architecture)

* [Inicio rápido: Conexión de Microsoft Defender for Identity a Active Directory](/defender-for-identity/install-step2)

Si no pretende usar Microsoft Defender para la identidad, puede [supervisar los controladores de dominio mediante mensajes de registro de eventos](/windows-server/identity/ad-ds/plan/security-best-practices/monitoring-active-directory-for-signs-of-compromise) o [ejecutando cmdlets de PowerShell](/windows-server/identity/ad-ds/deploy/troubleshooting-domain-controller-deployment). 

## <a name="components-of-hybrid-authentication"></a>Componentes de la autenticación híbrida

Como parte de un entorno híbrido de Azure, lo siguiente se debe tomar como referencia e incluirse en la estrategia de supervisión y alertas. 

* **Agente de PTA**: el agente de autenticación transferida se usa para habilitar la autenticación de paso a través y se instala en el entorno local. Consulte [Agente de autenticación de paso a través de Azure AD: Historial de lanzamiento de versiones](../hybrid/reference-connect-pta-version-history.md) para información sobre cómo verificar la versión del agente y los pasos siguientes. 

* **AD FS/WAP**: Servicios de federación de Active Directory (AD FS) (Azure AD FS) y Web Application Proxy (WAP) permiten compartir de forma segura la identidad digital y los derechos en los límites de seguridad y empresa. Para información sobre los procedimientos recomendados de seguridad, consulte [Procedimientos recomendados para proteger Servicios de federación de Active Directory (AD FS)]/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs. 

* **Agente de Azure AD Connect Health**: el agente que se usa para proporcionar un vínculo de comunicaciones para Azure AD Connect Health. Para información sobre de cómo instalar el agente, consulte [Instalación del agente de Azure AD Connect Health](../hybrid/how-to-connect-health-agent-install.md). 

* **Motor de sincronización de Azure AD Connect**: el componente local, también denominado el motor de sincronización. Para información sobre la característica, consulte [Características del servicio de sincronización de Azure AD Connect](../hybrid/how-to-connect-syncservice-features.md).

* **Agente de controlador de dominio de protección de contraseñas**: el agente de controlador de dominio de protección de contraseñas de Azure se usa para ayudar a supervisar e informar de los mensajes del registro de eventos. Para obtener información, consulte ../authentication/concept-password-ban-bad-on-premises.md.

* **DLL de filtro de contraseña**: la DLL del agente de controlador de dominio del filtro de contraseña recibe las solicitudes de validación de contraseña de usuario desde el sistema operativo. El filtro las reenvía al servicio DC Agent que se ejecuta localmente en el controlador de dominio. Para información sobre el uso de la DLL, consulte [Aplicación de Protección con contraseña de Azure AD local en Active Directory Domain Services](../authentication/concept-password-ban-bad-on-premises.md). 

* **Agente de escritura diferida de contraseñas**: la escritura diferida de contraseñas es una característica que se habilita con [Azure AD Connect](../hybrid/whatis-hybrid-identity.md) y que permite que los cambios de contraseña en la nube se escriban en diferido en un directorio local existente en tiempo real. Para información sobre esta característica, consulte [¿Cómo funciona la escritura diferida del autoservicio de restablecimiento de contraseña en Azure Active Directory?](../authentication/concept-sspr-writeback.md)

* **Conector de Application Proxy de Azure AD**: agentes ligeros que se colocan en local y facilitan la conexión saliente del servicio Proxy de aplicación. Para más información, consulte [Descripción de los conectores del Proxy de aplicación de Azure AD](../app-proxy/application-proxy-connectors.md). 

## <a name="components-of-cloud-based-authentication"></a>Componentes de la autenticación basada en la nube

Como parte de un entorno basado en la nube de Azure, lo siguiente se debe tomar como referencia e incluirse en la estrategia de supervisión y alertas.

* **Application Proxy de Azure AD**: este servicio en la nube proporciona acceso remoto seguro a aplicaciones web locales. Para más información, consulte [Acceso remoto a aplicaciones locales mediante Azure AD Application Proxy](../app-proxy/application-proxy-connectors.md). 

* **Azure AD Connect**: servicios usados para una solución de Azure AD Connect. Para más información, consulte [¿Qué es Azure AD Connect?](../hybrid/whatis-azure-ad-connect.md).

* **Azure AD Connect Health**: Service Health proporciona un panel personalizable que realiza un seguimiento del estado de los servicios de Azure en las regiones donde los use. Para más información, consulte [Azure AD Connect Health](../hybrid/whatis-azure-ad-connect.md).

* **Azure MFA**: autenticación multifactor de Azure AD requiere que un usuario proporcione más de una forma de prueba para la autenticación. Esto puede proporcionar un primer paso proactivo para proteger el entorno. Para más información, consulte el [Funcionamiento: Azure AD Multi-Factor Authentication](../authentication/concept-mfa-howitworks.md).

* **Grupos dinámicos**: la configuración dinámica de la pertenencia a grupos de seguridad para los administradores de Azure Active Directory (Azure AD) puede establecer reglas para rellenar los grupos creados en Azure AD en función de los atributos de usuario. Para más información, consulte [Grupos dinámicos y colaboración B2B de Azure Active Directory](../external-identities/use-dynamic-groups.md).

* **Acceso condicional**: el acceso condicional es la herramienta que usa Azure Active Directory para reunir las señales, tomar decisiones y aplicar las directivas de la organización. El acceso condicional está en el centro del nuevo plano de control basado en identidades. Para más información, consulte [¿Qué es el acceso condicional?](../conditional-access/overview.md).

* **Protección de identidad**: herramienta que permite a las organizaciones automatizar la detección y corrección de riesgos basados en identidades, investigar riesgos mediante datos del portal y exportar datos de detección de riesgos a su SIEM. Para más información, consulte [¿Qué es protección de identidad?](../identity-protection/overview-identity-protection.md).

* **Licencias basadas en grupos**: las licencias se pueden asignar a grupos en lugar de directamente a los usuarios. Azure AD almacena información sobre los estados de asignación de licencias para los usuarios. 

* **Servicio de aprovisionamiento**: el aprovisionamiento automático hace referencia a la creación de identidades y roles de usuario en las aplicaciones en la nube a las que los usuarios necesitan acceder. Además de crear identidades de usuario, el aprovisionamiento automático incluye el mantenimiento y la eliminación de identidades de usuario a medida que el estado o los roles cambian. Para más información, consulte [Funcionamiento del aprovisionamiento de aplicaciones en Azure Active Directory](../app-provisioning/how-provisioning-works.md).

* **Graph API**: Microsoft Graph API es una API web de RESTful que le permite tener acceso a recursos de servicio de Microsoft Cloud. Después de registrar la aplicación y obtener los tokens de autenticación para un usuario o servicio, puede realizar solicitudes a Microsoft Graph API. Para más información, consulte [Introducción a Microsoft Graph](/graph/overview).

* **Servicio de dominio**: Azure Active Directory Domain Services (Azure AD DS) proporciona servicios de dominio administrados como, por ejemplo, unión a un dominio o directivas de grupo. Para más información, consulte [¿Qué es Azure Active Directory Domain Services?](../../active-directory-domain-services/overview.md).

* **Azure Resource Manager**: Azure Resource Manager es el servicio de implementación y administración para Azure. Proporciona una capa de administración que le permite crear, actualizar y eliminar recursos de la cuenta de Azure. Para más información, consulte [¿Qué es Azure Resource Manager?](../../azure-resource-manager/management/overview.md).

* **Identidad administrada**: las identidades administradas eliminan la necesidad de que los desarrolladores administren las credenciales. Proporcionan una identidad que usan las aplicaciones al conectarse a recursos que admiten la autenticación de Azure Active Directory (Azure AD). Para obtener más información, consulte [¿Qué son las identidades administradas para recursos de Azure?](../managed-identities-azure-resources/overview.md)

* **Privileged Identity Management**: Privileged Identity Management (PIM) es un servicio de Azure Active Directory (Azure AD) que permite administrar, controlar y supervisar el acceso a recursos importantes de la organización. Para más información, consulte [¿Qué es Azure AD Privileged Identity Management?](../privileged-identity-management/pim-configure.md)

* **Revisiones de acceso**: las revisiones de acceso de Azure Active Directory (Azure AD) permiten a las organizaciones administrar de forma eficiente la pertenencia a grupos, el acceso a las aplicaciones empresariales y las asignaciones de roles. El acceso de los usuarios se puede revisar de forma periódica para asegurarse de que solo las personas adecuadas tengan acceso continuado. Para más información, consulte [¿Qué son las revisiones de acceso de Azure AD?](../governance/access-reviews-overview.md).

* **Administración de derechos**: la administración de derechos de Azure Active Directory (Azure AD) es una característica de [control de identidad](../governance/identity-governance-overview.md) que permite a las organizaciones administrar el ciclo de vida de identidad y acceso a escala, mediante la automatización de los flujos de trabajo de solicitud de acceso, las asignaciones de acceso, las revisiones y la expiración. Para obtener más información, consulte [¿Qué es la administración de derechos de Azure AD?](../governance/entitlement-management-overview.md)

* **Registros de actividad**: el registro de actividad es un [registro de plataforma](../../azure-monitor/essentials/platform-logs-overview.md) de Azure que proporciona conclusiones sobre los eventos del nivel de suscripción. Esto incluye información como cuándo se modificó un recurso o cuándo se inició una máquina virtual. Para información, consulte [Registro de actividad de Azure](../../azure-monitor/essentials/activity-log.md).

* **Servicio de autoservicio de restablecimiento de contraseña**: el autoservicio de restablecimiento de contraseña (SSPR) de Azure Active Directory (Azure AD) ofrece a los usuarios la posibilidad de cambiar o restablecer su contraseña sin necesidad de que intervenga el administrador o el departamento de soporte técnico. Para obtener más información, [Consulte cómo funciona: Autoservicio de restablecimiento de contraseña de Azure AD](../authentication/concept-sspr-howitworks.md).

* **Servicios de dispositivo**: la administración de identidades de los dispositivos es la base del [acceso condicional basado en dispositivos](../conditional-access/require-managed-devices.md). Con las directivas de acceso condicional basado en dispositivos puede asegurarse de que el acceso a los recursos del entorno solo es posible con los dispositivos administrados. Para información, consulte [¿Qué es una identidad de dispositivo?](../devices/overview.md)

* **Administración de grupos de autoservicio**: puede permitir que los usuarios creen y administren sus propios grupos de seguridad o grupos de Microsoft 365 en Azure Active Directory (Azure AD). El propietario del grupo puede aprobar o rechazar solicitudes de pertenencia y puede delegar el control de la pertenencia a grupos. Las características de administración de grupos de autoservicio no están disponibles para grupos de seguridad habilitados para correo electrónico o listas de distribución. Para más información, consulte [Configuración de la administración de grupos de autoservicio en Azure Active Directory](../enterprise-users/groups-self-service-management.md).

* **Detecciones de riesgo**: contiene información sobre otros riesgos desencadenados cuando se detecta un riesgo y otra información pertinente, como la ubicación de inicio de sesión y los detalles de Microsoft Cloud App Security (MCAS).

## <a name="next-steps"></a>Pasos siguientes

Consulte estos artículos de la guía de operaciones de seguridad:

[Información general de operaciones de seguridad de Azure AD](security-operations-introduction.md)

[Operaciones de seguridad para cuentas de usuario](security-operations-user-accounts.md)

[Operaciones de seguridad para cuentas con privilegios](security-operations-privileged-accounts.md)

[Operaciones de seguridad para Privileged Identity Management](security-operations-privileged-identity-management.md)

[Operaciones de seguridad para aplicaciones](security-operations-applications.md)

[Operaciones de seguridad para dispositivos](security-operations-devices.md)

 
[Operaciones de seguridad para infraestructura](security-operations-infrastructure.md)
