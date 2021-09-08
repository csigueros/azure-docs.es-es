---
title: Línea base de seguridad de Azure para Azure Virtual Desktop
description: La base de referencia de seguridad de Azure Virtual Desktop proporciona instrucciones de procedimientos y recursos para implementar las recomendaciones de seguridad especificadas en Azure Security Benchmark.
author: msmbaldwin
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: b9a0d2b785f98555d723c81c6beecaa85313881e
ms.sourcegitcommit: f2eb1bc583962ea0b616577f47b325d548fd0efa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2021
ms.locfileid: "114728663"
---
# <a name="azure-security-baseline-for-azure-virtual-desktop"></a>Línea base de seguridad de Azure para Azure Virtual Desktop

Esta base de referencia de seguridad aplica la guía de la [versión 2.0 de Azure Security Benchmark](../security/benchmarks/overview.md) en Azure Virtual Desktop. Azure Security Benchmark proporciona recomendaciones sobre cómo puede proteger sus soluciones de nube en Azure. El contenido se agrupa en función de los **controles de seguridad** que define Azure Security Benchmark y las directrices aplicables a Azure Virtual Desktop. Se han excluido los **controles** que no son aplicables a Azure Virtual Desktop.

El servicio Azure Virtual Desktop incluye el propio servicio, la versión de Windows 10 Enterprise para la SKU virtual multisesión y FSLogix. Para ver las recomendaciones de seguridad relacionadas con FSLogix, consulte la [base de referencia de seguridad sobre almacenamiento](../storage/common/security-baseline.md). El servicio tiene un agente que se ejecuta en máquinas virtuales, pero como las máquinas virtuales están bajo control total del cliente, siga las [recomendaciones de seguridad sobre proceso.](../virtual-machines/windows/security-baseline.md)

Para ver cómo Azure Virtual Desktop se asigna por completo a Azure Security Benchmark, consulte el [archivo completo de asignación de la base de referencia de seguridad de Azure Virtual Desktop](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Seguridad de redes

*Para más información, consulte [Azure Security Benchmark: seguridad de red](../security/benchmarks/security-controls-v2-network-security.md).*

### <a name="ns-1-implement-security-for-internal-traffic"></a>NS-1: implementación de la seguridad para el tráfico interno

**Guía**: debe crear o usar una red virtual existente al implementar las máquinas virtuales que se van a registrar en Azure Virtual Desktop. Asegúrese de que todas las redes virtuales de Azure siguen un principio de segmentación empresarial que se adapte a los riesgos empresariales. Todos los sistemas que podrían suponer un riesgo mayor para la organización deben aislarse en su propia red virtual y estar lo suficientemente protegidos con un grupo de seguridad de red o con Azure Firewall.

Use las características de la protección de red adaptable de Azure Security Center para recomendar configuraciones de grupo de seguridad de red que limiten los puertos y las direcciones IP con referencia a las reglas de tráfico externo.

En función de las aplicaciones y la estrategia de segmentación empresarial, restrinja o permita el tráfico entre los recursos internos en función de las reglas del grupo de seguridad de red. En el caso de aplicaciones específicas bien definidas (como una aplicación de tres niveles), puede ser un enfoque de tipo "denegar de manera predeterminada, permitir por excepción" altamente seguro. Es posible que esta estrategia no escale bien si tiene muchas aplicaciones y puntos de conexión que interactúan entre sí. También puede usar Azure Firewall en circunstancias en las que se requiere la administración central a través de un gran número de segmentos o radios de empresa (en una topología en estrella tipo hub-and-spoke).

En el caso de los grupos de seguridad de red asociados a las subredes de las máquinas virtuales (que forman parte de Azure Virtual Desktop), debe permitir el tráfico saliente a puntos de conexión concretos. 

- [Obtención de las direcciones URL a las que se debe permitir el acceso a Azure Virtual Desktop](safe-url-list.md)

- [Protección de red adaptable en Azure Security Center](../security-center/security-center-adaptive-network-hardening.md) 

- [Azure Firewall para Azure Virtual Desktop ](../firewall/protect-windows-virtual-desktop.md)

- [Creación de un grupo de seguridad de red con reglas de seguridad](../virtual-network/tutorial-filter-network-traffic.md)

 

- [Implementación y configuración de Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="ns-2-connect-private-networks-together"></a>NS-2: Conexión conjunta de redes privadas

**Guía**: Use Azure ExpressRoute o la red privada virtual de Azure para crear conexiones privadas entre centros de datos de Azure y una infraestructura local en un entorno de coubicación. Las conexiones ExpressRoute no utilizan la red pública de Internet y ofrecen más confiabilidad, velocidad, seguridad y una menor latencia que las conexiones a Internet habituales. 

Para las redes privadas virtuales de punto a sitio y de sitio a sitio, puede conectar dispositivos o redes locales a una red virtual mediante cualquier combinación de estas opciones de red privada virtual y Azure ExpressRoute.

Para conectar entre sí dos o más redes virtuales en Azure, use el emparejamiento de red virtual. El tráfico entre redes virtuales emparejadas es privado y se mantiene en la red troncal de Azure.

- [Qué son los modelos de conectividad de ExpressRoute](../expressroute/expressroute-connectivity-models.md) 

- [Información general sobre la red privada virtual de Azure](../vpn-gateway/vpn-gateway-about-vpngateways.md) 

- [Interconexión de red virtual](../virtual-network/virtual-network-peering-overview.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="ns-4-protect-applications-and-services-from-external-network-attacks"></a>NS-4: protección de las aplicaciones y servicios de ataques de redes externas

**Guía**: Use Azure Firewall para proteger las aplicaciones y los servicios contra el tráfico potencialmente malintencionado de Internet y otras ubicaciones externas. Proteja los recursos de Azure Virtual Desktop de ataques de redes externas, incluidos los ataques de denegación de servicio distribuido, los ataques específicos de la aplicación y el tráfico de Internet no solicitado y potencialmente malintencionado. Proteja sus recursos de ataques de denegación de servicio distribuido al habilitar la protección contra DDoS estándar en las redes virtuales de Azure. Use Azure Security Center para detectar riesgos de configuración incorrecta relacionados con los recursos de red.

Azure Virtual Desktop no está diseñado para ejecutar aplicaciones web y no requiere que se configuren opciones adicionales ni se implementen servicios de red complementarios para protegerlo de ataques de red externos que tienen como destino aplicaciones web.

- [Documentación sobre Azure Firewall](../firewall/index.yml)

- [Administración de Azure DDoS Protection estándar mediante Azure Portal](../ddos-protection/manage-ddos-protection.md) 

- [Recomendaciones de Azure Security Center](../security-center/recommendations-reference.md#networking-recommendations)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="ns-5-deploy-intrusion-detectionintrusion-prevention-systems-idsips"></a>NS-5: implementación de sistemas de prevención de intrusiones y detección de intrusiones (IDS/IPS)

**Guía**: Use Azure Firewall con el filtrado basado en inteligencia sobre amenazas para alertar o bloquear el tráfico desde y hacia dominios y direcciones IP malintencionados y conocidos. La direcciones IP y los dominios proceden de la fuente Inteligencia sobre amenazas de Microsoft. Cuando se requiera la inspección de las cargas, puede implementar una solución de terceros para la detección y prevención de intrusos desde Azure Marketplace. 

Si tiene un requisito legal o de otro tipo para el uso de una solución de detección y prevención de intrusiones, asegúrese de que siempre esté optimizada para proporcionar alertas de alta calidad a la solución de administración de eventos e información de seguridad (SIEM).

- [Implementación de Azure Firewall](../firewall/tutorial-firewall-deploy-portal.md) 

- [Azure Marketplace incluye funcionalidades de IDS de terceros](https://azuremarketplace.microsoft.com/marketplace?search=IDS) 

- [Funcionalidad de detección y respuesta de la protección contra amenazas avanzada de Microsoft Defender](/bs-cyrl-ba/windows/security/threat-protection/microsoft-defender-atp/overview-endpoint-detection-response)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="ns-6-simplify-network-security-rules"></a>NS-6: simplificación de las reglas de seguridad de red

**Guía**: use etiquetas de servicio de Azure Virtual Network para definir controles de acceso a la red en los grupos de seguridad de red o en una instancia de Azure Firewall configurada para los recursos de Azure Virtual Desktop. Puede utilizar etiquetas de servicio en lugar de direcciones IP específicas al crear reglas de seguridad. Al especificar el nombre de la etiqueta de servicio (por ejemplo: WindowsVirtualDesktop) en el campo adecuado de origen o destino de una regla, puede permitir o denegar el tráfico para el servicio correspondiente. Microsoft administra los prefijos de direcciones que la etiqueta de servicio incluye y actualiza automáticamente dicha etiqueta a medida que las direcciones cambian.

- [Descripción y uso de etiquetas de servicio](../virtual-network/service-tags-overview.md)

- [Obtenga más información sobre lo que se incluye aquí en la etiqueta de servicio de Azure Virtual Desktop](safe-url-list.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

## <a name="identity-management"></a>Administración de identidades

*Para más información, consulte [Azure Security Benchmark: Administración de identidades](../security/benchmarks/security-controls-v2-identity-management.md).*

### <a name="im-1-standardize-azure-active-directory-as-the-central-identity-and-authentication-system"></a>IM-1: Unificación en Azure Active Directory como sistema central de identidad y autenticación

**Guía**: Azure Virtual Desktop usa Azure Active Directory (Azure AD) como un servicio de administración de identidades y acceso predeterminado de Azure. Debe unificar Azure AD para controlar la administración de identidades y accesos de la organización en:

- Los recursos de Microsoft Cloud, como Azure Portal, Azure Storage, Azure Virtual Machines (Linux y Windows), Azure Key Vault, PaaS y aplicaciones SaaS.

- Los recursos de su organización, como aplicaciones en Azure o los recursos de la red corporativa.

La protección de Azure AD debe tener máxima prioridad en la práctica de seguridad en la nube de su organización. Azure AD proporciona una puntuación de seguridad de la identidad para ayudarle a evaluar la posición de seguridad de las identidades en relación con los procedimientos recomendados de Microsoft. Use la puntuación para medir el grado de coincidencia de la configuración con los procedimientos recomendados y para hacer mejoras en la posición de seguridad.

Azure AD admite identidades externas, que permiten a los usuarios que no tienen una cuenta de Microsoft iniciar sesión en sus aplicaciones y recursos con su identidad externa.

- [Inquilinos en Azure AD](../active-directory/develop/single-and-multi-tenant-apps.md)

- [Uso de proveedores de identidades externos para una aplicación](../active-directory/external-identities/identity-providers.md)

- [¿Qué es la puntuación de seguridad de la identidad en Azure AD?](../active-directory/fundamentals/identity-secure-score.md)

- [Roles específicos que necesita para usar Azure Virtual Desktop ](/azure/virtual-desktop/faq#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="im-2-manage-application-identities-securely-and-automatically"></a>IM-2: Administración de identidades de aplicaciones de forma segura y automática

**Guía**: Azure Virtual Desktop admite identidades administradas de Azure en cuentas no humanas, como los servicios o la automatización. Se recomienda usar la característica de identidades administradas de Azure en lugar de crear una cuenta de usuario con más permisos para acceder a los recursos o ejecutarlos. 

Azure Virtual Desktop recomienda el uso de Azure Active Directory (Azure AD) para crear una entidad de servicio con permisos restringidos en el nivel de recursos para configurar entidades de servicio con credenciales de certificado y revertir a secretos de cliente. En ambos casos, Azure Key Vault se puede usar junto con las identidades administradas de Azure, de modo que el entorno en tiempo de ejecución (por ejemplo, una instancia de Azure Functions) pueda recuperar la credencial del almacén de claves.

- [Servicios que admiten identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md)

- [Entidad de servicio de Azure](/powershell/azure/create-azure-service-principal-azureps) 

- [Creación de una entidad de servicio con certificado](../active-directory/develop/howto-authenticate-service-principal-powershell.md) 

- [Uso de Azure Key Vault para el registro de entidades de seguridad](../key-vault/general/authentication.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="im-3-use-azure-ad-single-sign-on-sso-for-application-access"></a>IM-3: Uso del inicio de sesión único de Azure AD para acceder a las aplicaciones

**Guía**: Azure Virtual Desktop usa Azure Active Directory (Azure AD) para proporcionar la opción de administración de identidades y acceso a los recursos de Azure, las aplicaciones en la nube y las aplicaciones locales. Esto incluye no solo las identidades empresariales, como los empleados, sino también las identidades externas, como asociados y proveedores. Esto permite que el inicio de sesión único (SSO) administre y proteja el acceso a los datos y recursos de la organización locales y en la nube. Conecte todos los usuarios, aplicaciones y dispositivos a Azure AD para un acceso seguro y sin problemas, con mayor visibilidad y control.

- [Descripción del inicio de sesión único de aplicaciones con Azure AD](../active-directory/manage-apps/what-is-single-sign-on.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="im-4-use-strong-authentication-controls-for-all-azure-active-directory-based-access"></a>IM-4: Uso de controles con autenticación multifactor sólida para todo el acceso basado en Azure Active Directory 

**Guía**: Azure Virtual Desktop usa Azure Active Directory (Azure AD), que admite controles de autenticación sólida a través de la autenticación multifactor y otros métodos seguros sin contraseña.

- Autenticación multifactor: habilite la autenticación multifactor de Azure AD y siga las recomendaciones de la administración de identidades y acceso de Azure Security Center para conocer los procedimientos recomendados para la configuración de la autenticación multifactor. La autenticación multifactor se puede exigir a todos los usuarios, a usuarios concretos o por usuario en función de los factores de riesgo y las condiciones de inicio de sesión.

- Autenticación sin contraseña: hay disponibles tres opciones de autenticación sin contraseña: Windows Hello para empresas, aplicación Microsoft Authenticator y métodos de autenticación locales, como las tarjetas inteligentes.

Azure Virtual Desktop admite la autenticación heredada basada en contraseña como, por ejemplo, las cuentas solo para la nube (es decir, cuentas de usuario creadas directamente en Azure) que tienen una directiva de contraseñas de línea de base o las cuentas híbridas (cuentas de usuario que provienen de instancias de Azure AD locales que siguen las directivas de contraseñas locales). Cuando se usa la autenticación con contraseña, Azure AD proporciona una funcionalidad de protección de contraseñas que impide que los usuarios establezcan contraseñas fáciles de adivinar. Microsoft proporciona una lista global de contraseñas prohibidas que se actualiza en función de la telemetría; asimismo, los clientes pueden ampliarla en función de sus necesidades (por ejemplo, mediante la personalización de marca o referencias culturales entre otras). Esta protección de contraseñas se puede usar para las cuentas híbridas y solo en la nube.

La autenticación basada solo en las credenciales de contraseña es susceptible a métodos de ataque populares. Para una mayor seguridad, use una autenticación sólida, como la autenticación multifactor y una directiva de contraseñas segura. En el caso de las aplicaciones de terceros y los servicios de Marketplace que pueden tener contraseñas predeterminadas, debe cambiarlas durante la configuración inicial del servicio.

Para administradores y usuarios con privilegios, asegúrese de que se usa el nivel más alto de los métodos de autenticación sólida, seguido de la implementación de la directiva de autenticación sólida adecuada para otros usuarios.

- [Introducción a las opciones de autenticación sin contraseña de Azure Active Directory](../active-directory/authentication/concept-authentication-passwordless.md) 

- [Directiva de contraseñas predeterminada de Azure AD](../active-directory/authentication/concept-sspr-policy.md#password-policies-that-only-apply-to-cloud-user-accounts) 

- [Eliminación de contraseñas incorrectas mediante Protección con contraseña de Azure Active Directory](../active-directory/authentication/concept-password-ban-bad.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="im-5-monitor-and-alert-on-account-anomalies"></a>IM-5: Supervisión y alerta de anomalías de cuenta

**Guía**: Azure Virtual Desktop se integra con Azure Active Directory (Azure AD) para proporcionar los siguientes orígenes de datos:

- Inicio de sesión: el informe de inicio de sesión proporciona información sobre el uso de aplicaciones administradas y actividades de inicio de sesión del usuario.

- Los registros de auditoría proporcionan rastreabilidad mediante los registros de todos los cambios realizados por diversas características de Azure AD. Algunos ejemplos de registros de auditoría incluyen los cambios realizados en cualquier recurso de Azure AD como agregar o quitar usuarios, aplicaciones, grupos, roles y directivas.

- Inicios de sesión de riesgo: un inicio de sesión de riesgo es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario.

- Usuarios marcados en riesgo: un usuario en riesgo es un indicador de una cuenta de usuario que puede haber estado en peligro.

Estos orígenes de datos se pueden integrar con Azure Monitor, Azure Sentinel o sistemas de administración de eventos e información de seguridad (SIEM) de terceros. Azure Security Center también puede alertar sobre determinadas actividades sospechosas, como un número excesivo de intentos de autenticación incorrectos y cuentas en desuso en la suscripción. Azure Advanced Threat Protection (AATP) es una solución de seguridad que puede usar señales de Active Directory para identificar, detectar e investigar amenazas avanzadas, identidades en riesgo y acciones internas malintencionadas.

- [Informes de actividad de auditoría en Azure AD](../active-directory/reports-monitoring/concept-audit-logs.md)

- [Visualización de los inicios de sesión de riesgo de Azure AD](../active-directory/identity-protection/overview-identity-protection.md)

- [Alertas del módulo de protección de inteligencia sobre amenazas de Azure Security Center](../security-center/alerts-reference.md)

- [Integración de los registros de actividad de Azure en Azure Monitor](../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="im-6-restrict-azure-resource-access-based-on-conditions"></a>IM-6: Restricción del acceso a recursos de Azure en función de las condiciones

**Guía**: Azure Virtual Desktop admite la característica de acceso condicional con Azure Active Directory (Azure AD) para obtener un control de acceso más pormenorizado basado en condiciones definidas por el usuario. Por ejemplo, es posible que los inicios de sesión de usuarios de ciertos intervalos IP requieran el uso de la autenticación multifactor para acceder. 

También se puede usar la directiva de administración de sesión de autenticación pormenorizada para distintos casos de uso.

- [Introducción al acceso condicional de Azure](../active-directory/conditional-access/overview.md) 

- [Directivas de acceso condicional habituales](../active-directory/conditional-access/concept-conditional-access-policy-common.md) 

- [Configuración de la administración de las sesiones de autenticación con el acceso condicional](../active-directory/conditional-access/howto-conditional-access-session-lifetime.md) 

- [Aquí encontrará información específica sobre la configuración del acceso condicional a Azure Virtual Desktop](set-up-mfa.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

## <a name="privileged-access"></a>Acceso con privilegios

*Para más información, consulte [Azure Security Benchmark: Acceso con privilegios](../security/benchmarks/security-controls-v2-privileged-access.md).*

### <a name="pa-2-restrict-administrative-access-to-business-critical-systems"></a>PA-2: Restricción del acceso administrativo a los sistemas críticos para la empresa

**Guía**: Azure Virtual Desktop usa el control de acceso basado en rol de Azure (Azure RBAC) para aislar el acceso a los sistemas críticos para la empresa. Asegúrese de restringir también el acceso a los sistemas de administración, identidad y seguridad que tienen acceso administrativo a los recursos críticos para la empresa, como controladores de dominio de Active Directory, herramientas de seguridad y herramientas de administración del sistema con agentes instalados en sistemas críticos para la empresa. Los atacantes que ponen en peligro estos sistemas de administración y seguridad pueden posiblemente convertirlos inmediatamente en un arma para poner en peligro los recursos críticos para la empresa.

Para garantizar un control de acceso coherente, todos los tipos de control de acceso se deben alinear con la estrategia de segmentación de la empresa.

- [Modelo de referencia y componentes de Azure](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151) 

- [Acceso al grupo de administración](../governance/management-groups/overview.md#management-group-access) 

- [Administradores de la suscripción de Azure](../cost-management-billing/manage/add-change-subscription-administrator.md)

- [Permisos de administrador mínimos necesarios para administrar Azure Virtual Desktop](/azure/virtual-desktop#what-are-the-minimum-admin-permissions-i-need-to-manage-objects)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="pa-3-review-and-reconcile-user-access-regularly"></a>PA-3: Revisión y conciliación de manera periódica del acceso de los usuarios

**Guía**: Azure Virtual Desktop usa cuentas de Azure Active Directory (Azure AD) para administrar sus recursos, revisar las cuentas de usuario y acceder a la asignación con regularidad para asegurarse de que las cuentas y el acceso sean válidos.

Use las revisiones de acceso de Azure AD para revisar las pertenencias a grupos, el acceso a las aplicaciones empresariales y las asignaciones de roles. Los informes de Azure AD pueden proporcionar registros para ayudar a detectar cuentas obsoletas.

Además, se puede configurar Azure Privileged Identity Management para enviar una alerta cuando se cree un número de cuentas de administrador excesivo y para identificar las cuentas de administrador que hayan quedado obsoletas o que no estén configuradas correctamente.

Algunos servicios de Azure admiten roles y usuarios locales que no se administran mediante Azure AD. Estos usuarios deberán administrarse por separado.

- [Roles integrados de Azure Virtual Desktop](rbac.md)

- [Creación de una revisión de acceso de los roles de recursos de Azure en Privileged Identity Management (PIM)](../active-directory/privileged-identity-management/pim-resource-roles-start-access-review.md)

- [Procedimiento para usar las revisiones de acceso e identidades de Azure AD](../active-directory/governance/access-reviews-overview.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="pa-4-set-up-emergency-access-in-azure-ad"></a>PA-4: Configuración del acceso de emergencia en Azure AD

**Guía**: Azure Virtual Desktop usa Azure Active Directory (Azure AD) para administrar sus recursos. Para evitar que se le bloquee por accidente el acceso a la organización de Azure AD, configure una cuenta de acceso de emergencia para cuando no se puedan usar cuentas administrativas normales. Las cuentas de acceso de emergencia tienen normalmente privilegios elevados y no se asignan a usuarios específicos. Las cuentas de acceso de emergencia se limitan a situaciones "excepcionales" o de emergencia en las que no se pueden usar las cuentas administrativas normales.

Debe asegurarse de que las credenciales (como contraseña, certificado o tarjeta inteligente) de las cuentas de acceso de emergencia estén protegidas y solo las conozcan aquellas personas que estén autorizadas a usarlas solo en caso de emergencia.

- [Administración de cuentas de acceso de emergencia en Azure AD](../active-directory/roles/security-emergency-access.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="pa-5-automate-entitlement-management"></a>PA-5: Automatización de la administración de derechos 

**Guía**: Azure Virtual Desktop se integra en Azure Active Directory (Azure AD) para administrar sus recursos. Use las características de administración de derechos de Azure AD para automatizar los flujos de trabajo de solicitud de acceso, como las asignaciones, las revisiones y las expiraciones del acceso. También se admiten otras aprobaciones de dos o más fases.

- [¿Qué son las revisiones de acceso de Azure AD?](../active-directory/governance/access-reviews-overview.md) 

- [¿Qué es la administración de derechos de Azure AD?](../active-directory/governance/entitlement-management-overview.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="pa-6-use-privileged-access-workstations"></a>PA-6: Uso de estaciones de trabajo con privilegios de acceso

**Guía**: De cara a la seguridad de los roles confidenciales, como administradores, desarrolladores y operadores de servicios críticos, es importante que las estaciones de trabajo sean seguras y estén aisladas. Use estaciones de trabajo de usuario de alta seguridad o Azure Bastion para las tareas administrativas. 

Use Azure Active Directory (Azure AD), Protección contra amenazas avanzada (ATP) de Microsoft Defender o Microsoft Intune para implementar una estación de trabajo de usuario segura y administrada para las tareas administrativas. Las estaciones de trabajo protegidas se pueden administrar de forma centralizada para aplicar una configuración segura, como autenticación sólida, líneas de base de software y hardware y acceso lógico y de red restringido.

- [Descripción de las estaciones de trabajo con privilegios de acceso](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/) 

- [Implementación de una estación de trabajo con privilegios de acceso](/security/compass/privileged-access-deployment)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="pa-7-follow-just-enough-administration-least-privilege-principle"></a>PA-7: Seguimiento de solo una administración suficiente (principio de privilegios mínimos) 

**Guía**: Azure Virtual Desktop se integra con el control de acceso basado en roles de Azure (Azure RBAC) para administrar sus recursos. Azure RBAC le permite administrar el acceso a los recursos de Azure mediante las asignaciones de roles. Puede asignar estos roles a usuarios, grupos de entidades de servicio e identidades administradas. Hay roles integrados predefinidos para determinados recursos, y estos roles se pueden inventariar o consultar mediante herramientas como la CLI de Azure, Azure PowerShell o el Azure Portal. 

Los privilegios que se asignen a los recursos con Azure RBAC siempre se deben limitar a aquellos que los roles requieran. Este modelo complementa al enfoque Just-in-Time (JIT) de Privileged Identity Management (PIM) con Azure Active Directory (Azure AD) y se debe revisar periódicamente.

Además, use los roles integrados para asignar los permisos y crear solo roles personalizados cuando sea necesario.

- [¿Qué es el control de acceso basado en rol de Azure (RBAC)?](../role-based-access-control/overview.md) 

- [Configuración de Azure RBAC](../role-based-access-control/role-assignments-portal.md) 

- [Procedimiento para usar las revisiones de acceso e identidades de Azure AD](../active-directory/governance/access-reviews-overview.md)

- [Roles integrados de Azure Virtual Desktop](rbac.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="pa-8-choose-approval-process-for-microsoft-support"></a>PA-8: Selección del proceso de aprobación para el soporte técnico de Microsoft  

**Guía**: en escenarios de soporte técnico en los que Microsoft necesita acceder a los datos del cliente, Azure Virtual Desktop admite la Caja de seguridad del cliente, la cual proporciona una interfaz para que pueda revisar y aprobar o rechazar las solicitudes de acceso a los datos del cliente.

- [Descripción de la Caja de seguridad del cliente](../security/fundamentals/customer-lockbox-overview.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Compartido

## <a name="data-protection"></a>Protección de datos

*Para más información, consulte [Azure Security Benchmark: protección de datos](../security/benchmarks/security-controls-v2-data-protection.md).*

### <a name="dp-1-discovery-classify-and-label-sensitive-data"></a>DP-1: Detección, clasificación y etiquetado de datos confidenciales

**Guía**: detecte, clasifique y etiquete los datos confidenciales para que pueda diseñar los controles adecuados. Esto se lleva a cabo para garantizar que los sistemas tecnológicos de la organización almacenan, procesan y transmiten la información confidencial de forma segura.

Use Azure Information Protection (y su herramienta de detección asociada) con la información confidencial de los documentos de Office en Azure, del entorno local, de Office 365 y de otras ubicaciones.

Puede utilizar Azure SQL Information Protection para ayudar en la clasificación y el etiquetado de la información almacenada en las instancias de Azure SQL Database.

- [Etiquetado de información confidencial mediante Azure Information Protection](/azure/information-protection/what-is-information-protection) 

- [Implementación de la detección de datos de Azure SQL](../azure-sql/database/data-discovery-and-classification-overview.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="dp-2-protect-sensitive-data"></a>DP-2: Protección de datos confidenciales

**Guía**: Proteja los datos confidenciales mediante la restricción del acceso con el control de acceso basado en rol de Azure (Azure RBAC), los controles de acceso basados en la red y los controles específicos de los servicios de Azure (como el cifrado en SQL y otras bases de datos).

Para garantizar un control de acceso coherente, todos los tipos de control de acceso se deben alinear con la estrategia de segmentación de la empresa. La estrategia de segmentación de la empresa también debe estar informada de la ubicación de los datos y sistemas confidenciales o críticos para la empresa.

Microsoft trata todo el contenido de los clientes como confidencial y protege a los clientes contra la pérdida y exposición de sus datos. Para garantizar la seguridad de los datos de los clientes dentro de Azure, Microsoft ha implementado algunos controles y funcionalidades de protección de datos predeterminados.

- [Control de acceso basado en rol (RBAC) de Azure](../role-based-access-control/overview.md) 

- [Descripción de la protección de datos de los clientes en Azure](../security/fundamentals/protection-customer-data.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="dp-3-monitor-for-unauthorized-transfer-of-sensitive-data"></a>DP-3: Supervisión de la transferencia no autorizada de datos confidenciales

**Guía**: Supervisión de la transferencia de datos no autorizada a ubicaciones que se encuentran fuera de la visibilidad y el control de la empresa. Normalmente, esto implica la supervisión de actividades anómalas (transferencias grandes o inusuales) que podrían indicar una filtración de datos no autorizada.

La Protección contra amenazas avanzada (ATP) con Azure Storage y Azure SQL ATP pueden alertar sobre una transferencia anómala de información que podría indicar transferencias no autorizadas de información confidencial.

Azure Information Protection (AIP) proporciona funcionalidades de supervisión para la información que se ha clasificado y etiquetado.

Use soluciones de prevención de pérdida de datos como, por ejemplo, las que se basan en hosts, para aplicar controles de detección o preventivos que impidan la filtración de datos.

- [Habilitación de ATP para Azure SQL](../azure-sql/database/threat-detection-overview.md) 

- [Habilitación de ATP para Azure Storage](../storage/common/azure-defender-storage-configure.md?tabs=azure-security-center)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

## <a name="asset-management"></a>Administración de recursos

*Para más información, consulte [Azure Security Benchmark: Administración de recursos](../security/benchmarks/security-controls-v2-asset-management.md).*

### <a name="am-1-ensure-security-team-has-visibility-into-risks-for-assets"></a>AM-1: Asegurarse de que el equipo de seguridad tiene visibilidad sobre los riesgos para los recursos

**Guía**: Asegúrese de que se conceden a los equipos de seguridad permisos de lector de seguridad en el inquilino y las suscripciones de Azure para que puedan supervisar los riesgos de seguridad mediante Azure Security Center. 

En función de la estructura de las responsabilidades del equipo de seguridad, la supervisión de los riesgos de seguridad puede ser responsabilidad de un equipo de seguridad central o de un equipo local. Dicho esto, la información y los riesgos de seguridad siempre se deben agregar de forma centralizada dentro de una organización. 

Los permisos de lector de seguridad se pueden aplicar en general a un inquilino completo (grupo de administración raíz) o a grupos de administración o a suscripciones específicas. 

Es posible que se requieran permisos adicionales para obtener visibilidad sobre las cargas de trabajo y los servicios. 

- [Introducción al rol de lector de seguridad](../role-based-access-control/built-in-roles.md#security-reader)

- [Información general sobre los grupos de administración de Azure](../governance/management-groups/overview.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="am-2-ensure-security-team-has-access-to-asset-inventory-and-metadata"></a>AM-2: Asegurarse de que el equipo de seguridad tiene acceso a los metadatos y al inventario de recursos

**Guía**: Aplique etiquetas a los recursos, grupos de recursos y suscripciones de Azure con el fin de organizarlos de manera lógica en una taxonomía. Cada etiqueta consta de un nombre y un par de valores. Por ejemplo, puede aplicar el nombre "Environment" y el valor "Production" a todos los recursos en producción.

Use el inventario de máquinas virtuales de Azure para automatizar la recopilación de información sobre el software en Virtual Machines. El nombre del software, la versión, el publicador y la hora de actualización están disponibles en Azure Portal. Para obtener acceso a la fecha de instalación y otra información, habilite los diagnósticos de nivel de invitado y transfiera los registros de eventos de Windows a un área de trabajo de Log Analytics.

- [Creación de consultas con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md) 

- [Administración del inventario de recursos de Azure Security Center](../security-center/asset-inventory.md) 

- [Guía de decisiones de nomenclatura y etiquetado de recursos](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=%2fazure%2fazure-resource-manager%2fmanagement%2ftoc.json)

- [Habilitación del inventario de máquinas virtuales de Azure](../automation/automation-tutorial-installed-software.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="am-3-use-only-approved-azure-services"></a>AM-3: Uso exclusivo de servicios de Azure aprobados

**Guía**: Use Azure Policy para auditar y restringir qué servicios pueden aprovisionar los usuarios en su entorno. Use Azure Resource Graph para consultar y detectar recursos dentro de sus suscripciones. También puede usar Azure Monitor para crear reglas para desencadenar alertas cuando se detecta un servicio no aprobado.

- [Configuración y administración de Azure Policy](../governance/policy/tutorials/create-and-manage.md) 

- [Denegación de un tipo de recurso específico con Azure Policy](../governance/policy/samples/built-in-policies.md#general) 

- [Creación de consultas con Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="am-4-ensure-security-of-asset-lifecycle-management"></a>AM-4: Garantizar la seguridad de la administración del ciclo de vida de los recursos

**Instrucciones**: No aplicable. Azure Virtual Desktop no se puede usar para garantizar la seguridad de los recursos en un proceso de administración del ciclo de vida. Es responsabilidad del cliente mantener los atributos y las configuraciones de red de los recursos que se consideran de gran impacto. 

Se recomienda que el cliente cree un proceso para capturar los cambios de configuración de los atributos y la red, medir el efecto de los cambios y crear tareas de corrección, según corresponda.

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="am-5-limit-users-ability-to-interact-with-azure-resource-manager"></a>AM-5: Limitación de la capacidad de los usuarios para interactuar con Azure Resource Manager

**Instrucciones**: Use el acceso condicional de Azure para limitar la capacidad de los usuarios de interactuar con Azure Resource Manager mediante la configuración de la opción "Bloquear acceso" en la aplicación "Administración de Microsoft Azure".

- [Configuración del acceso condicional para bloquear el acceso a Azure Resource Manager](../role-based-access-control/conditional-access-azure-management.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="am-6-use-only-approved-applications-in-compute-resources"></a>AM-6: Uso exclusivo de aplicaciones aprobadas en recursos de proceso

**Guía**: use el inventario de máquinas virtuales de Azure para automatizar la recopilación de información sobre todo el software de las máquinas virtuales. El nombre del software, la versión, el publicador y la hora de actualización están disponibles en Azure Portal. Para obtener acceso a la fecha de instalación y otra información, habilite los diagnósticos de nivel de invitado y transfiera los registros de eventos de Windows a un área de trabajo de Log Analytics.

- [Habilitación del inventario de máquinas virtuales de Azure](../automation/automation-tutorial-installed-software.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

## <a name="logging-and-threat-detection"></a>registro y detección de amenazas

*Para más información, consulte [Azure Security Benchmark: registro y detección de amenazas](../security/benchmarks/security-controls-v2-logging-threat-detection.md).*

### <a name="lt-1-enable-threat-detection-for-azure-resources"></a>LT-1: Habilitación de la detección de amenazas para recursos de Azure

**Guía**: use la funcionalidad de detección de amenazas integrada de Azure Security Center y habilite Azure Defender (conocido formalmente como Protección contra amenazas avanzada) para los recursos de Azure Virtual Desktop. Azure Defender para Azure Virtual Desktop proporciona una capa adicional de inteligencia de seguridad que detecta intentos poco habituales y potencialmente peligrosos de acceder a los recursos de Azure Virtual Desktop o vulnerarlos.

Reenvíe los registros desde Azure Virtual Desktop a su solución de administración de eventos e información de seguridad (SIEM), la cual se puede usar para configurar detecciones de amenazas personalizadas. Asegúrese de que está supervisando distintos tipos de recursos de Azure para detectar posibles amenazas y anomalías. Céntrese en obtener alertas de alta calidad para reducir los falsos positivos que deben revisar los analistas. Las alertas se pueden crear a partir de datos de registro, agentes u otros datos.

- [Protección contra amenazas en Azure Security Center](../security-center/azure-defender.md) 

- [Guía de referencia de alertas de seguridad de Azure Security Center](../security-center/alerts-reference.md)

- [Creación de reglas de análisis personalizadas para detectar amenazas](../sentinel/tutorial-detect-threats-custom.md) 

- [Inteligencia sobre amenazas cibernéticas con Azure Sentinel](/azure/architecture/example-scenario/data/sentinel-threat-intelligence)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="lt-2-enable-threat-detection-for-azure-identity-and-access-management"></a>LT-2: Habilitación de la detección de amenazas para la administración de identidades y acceso de Azure

**Guía**: Azure Active Directory (Azure AD) proporciona los siguientes registros de usuario que se pueden ver en los informes de Azure AD o integrados con Azure Monitor, Azure Sentinel u otras herramientas de supervisión de administración de eventos e información de seguridad para casos de uso de supervisión y análisis más sofisticados:

- Inicio de sesión: el informe de inicio de sesión proporciona información sobre el uso de aplicaciones administradas y actividades de inicio de sesión del usuario.

- Los registros de auditoría proporcionan rastreabilidad mediante los registros de todos los cambios realizados por diversas características de Azure AD. Algunos ejemplos de registros de auditoría incluyen los cambios realizados en cualquier recurso de Azure AD como agregar o quitar usuarios, aplicaciones, grupos, roles y directivas.

- Inicios de sesión de riesgo: un inicio de sesión de riesgo es un indicador de un intento de inicio de sesión que puede haber realizado alguien que no es el propietario legítimo de una cuenta de usuario.

- Usuarios marcados en riesgo: un usuario en riesgo es un indicador de una cuenta de usuario que puede haber estado en peligro.

Azure Security Center también puede alertar sobre determinadas actividades sospechosas, como un número excesivo de intentos de autenticación incorrectos y cuentas en desuso en la suscripción. Además de la supervisión básica de la protección de seguridad, el módulo de protección contra amenazas de Azure Security Center también puede recopilar alertas de seguridad más detalladas de recursos individuales de proceso de Azure (máquinas virtuales, contenedores, App Service), recursos de datos (base de datos SQL y almacenamiento) y niveles de servicio de Azure. Esta funcionalidad le permite ver las anomalías de las cuentas dentro de los recursos individuales.

- [Informes de actividad de auditoría en Azure Active Directory](../active-directory/reports-monitoring/concept-audit-logs.md) 

- [Habilitación de Azure Identity Protection](../active-directory/identity-protection/overview-identity-protection.md) 

- [Protección contra amenazas en Azure Security Center](../security-center/azure-defender.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="lt-3-enable-logging-for-azure-network-activities"></a>LT-3: Habilitación del registro para las actividades de red de Azure

**Guía**: Azure Virtual Desktop no genera ni procesa registros de consulta del sistema de nombres de dominio (DNS). No obstante, los recursos que se registran en el servicio pueden producir registros de flujo.

Habilite y recopile registros de recursos y flujos de grupos de seguridad de red, registros de Azure Firewall y registros de Web Application Firewall (WAF) para un análisis de seguridad que favorezca la investigación de incidentes, la búsqueda de amenazas y la generación de alertas de seguridad. Puede enviar los registros de flujo a un área de trabajo de Azure Monitor Log Analytics y, a continuación, usar Análisis de tráfico para conseguir información detallada.

- [Habilitación de los registros de flujo de grupos de seguridad de red](../network-watcher/network-watcher-nsg-flow-logging-portal.md) 

- [Métricas y registros de Azure Firewall](../firewall/logs-and-metrics.md) 

- [Habilitación y uso del Análisis de tráfico](../network-watcher/traffic-analytics.md) 

- [Soluciones de supervisión de redes de Azure en Azure Monitor](../azure-monitor/insights/azure-networking-analytics.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="lt-4-enable-logging-for-azure-resources"></a>LT-4: Habilitación del registro para recursos de Azure

**Guía**: los registros de actividad, que están disponibles automáticamente, contienen todas las operaciones de escritura (PUT, POST, DELETE) de los recursos de Azure Virtual Desktop, excepto las operaciones de lectura (GET). Los registros de actividad se pueden usar para encontrar errores al solucionar problemas o para supervisar cómo un usuario de su organización modificó un recurso.

- [Recopilación de registros y métricas de plataforma con Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Descripción del registro y de los distintos tipos de registro de Azure](../azure-monitor/essentials/platform-logs-overview.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Compartido

### <a name="lt-5-centralize-security-log-management-and-analysis"></a>LT-5: Centralizar la administración y el análisis de los registros de seguridad

**Guía**: Centralice el almacenamiento y el análisis de los registros para permitir su correlación. Asegúrese de que, para cada origen de registro, asigna un propietario de datos, una guía de acceso, una ubicación de almacenamiento, las herramientas que se usan para procesar y acceder a los datos, y los requisitos de retención de estos.

Asegúrese también de que integra los registros de actividad de Azure en el registro central. Recopile registros mediante Azure Monitor para agregar datos de seguridad generados por dispositivos de punto de conexión, recursos de red y otros sistemas de seguridad. En Azure Monitor, use áreas de trabajo de Log Analytics para realizar consultas y análisis, y use cuentas de Azure Storage para el almacenamiento de archivos a largo plazo.

Asimismo, habilite e incorpore datos en Azure Sentinel o en un sistema de administración de eventos e información de seguridad (SIEM) de terceros. Muchas organizaciones optan por usar Azure Sentinel para los datos de acceso frecuente y Azure Storage para los datos inactivos que se usan con menos frecuencia.

- [Recopilación de registros y métricas de plataforma con Azure Monitor](../azure-monitor/essentials/diagnostic-settings.md) 

- [Incorporación de Azure Sentinel](../sentinel/quickstart-onboard.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

## <a name="incident-response"></a>Respuesta a los incidentes

*Para más información, consulte [Azure Security Benchmark: respuesta ante incidentes](../security/benchmarks/security-controls-v2-incident-response.md).*

### <a name="ir-1-preparation--update-incident-response-process-for-azure"></a>IR-1: Preparación: actualización del proceso de respuesta a incidentes para Azure

**Guía**: Asegúrese de que la organización tenga procesos para responder a incidentes de seguridad, que haya actualizado estos procesos para Azure y que los ejercite regularmente para garantizar su disponibilidad.

- [Implementación de la seguridad en todo el entorno empresarial](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guía de referencia de respuesta a incidentes](/microsoft-365/downloads/IR-Reference-Guide.pdf)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="ir-2-preparation--setup-incident-notification"></a>IR-2: Preparación: notificación de incidentes de configuración

**Guía**: Configure la información de contacto en caso de incidentes de seguridad en Azure Security Center. Microsoft utilizará esta información para ponerse en contacto con usted si el Centro de respuestas de seguridad de Microsoft (MSRC) detecta que un tercero no autorizado o ilegal ha accedido a sus datos. También hay opciones para personalizar la alerta y notificación de incidentes en diferentes servicios de Azure en función de sus necesidades de respuesta a incidentes. 

- [Establecimiento del contacto de seguridad de Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Supervisión de Azure Security Center**: Sí

**Responsabilidad**: Customer

### <a name="ir-3-detection-and-analysis--create-incidents-based-on-high-quality-alerts"></a>IR-3: Detección y análisis: creación de incidentes en función de alertas de alta calidad

**Guía**: Asegúrese de que cuenta con un proceso para crear alertas de alta calidad y medir la calidad de las alertas. Esto le permite aprender de incidentes anteriores y clasificar las alertas para los analistas, de modo que no pierdan tiempo con falsos positivos. 

Las alertas de alta calidad se pueden crear en función de la experiencia de pasados incidentes, información validada procedente de la comunidad y herramientas diseñadas para generar y limpiar alertas mediante la fusión y correlación de diversos orígenes de la señal. 

Azure Security Center proporciona alertas de alta calidad en muchos recursos de Azure. Puede usar el conector de datos de ASC para enviar las alertas a Azure Sentinel. Azure Sentinel le permite crear reglas de alerta avanzadas con el fin de generar automáticamente incidentes para investigar. 

Exporte las alertas y recomendaciones de Azure Security Center mediante la característica de exportación para ayudar a identificar riesgos en los recursos de Azure. Esta exportación puede hacerse de forma manual o de modo continuo.

- [Configuración de la exportación](../security-center/continuous-export.md)

- [Transmisión de alertas a Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="ir-4-detection-and-analysis--investigate-an-incident"></a>IR-4: Detección y análisis: investigación de incidentes

**Guía**: Asegúrese de que los analistas pueden consultar y usar diversos orígenes de datos a medida que investigan posibles incidentes, para conseguir una visión global de lo que ha sucedido. Se deben recopilar diversos registros para realizar un seguimiento de las actividades de un posible atacante en la cadena de eliminación para evitar puntos ciegos.  También debe asegurarse de que se capturan detalles y aprendizajes para otros analistas y para futuras referencias históricas.  

Los orígenes de datos para la investigación incluyen los orígenes de registro centralizados que ya se recopilan de los servicios en el ámbito y los sistemas en ejecución, pero también pueden incluir:

- Datos de red: use registros de flujo de grupos de seguridad de red, Azure Network Watcher y Azure Monitor para capturar registros de flujo de red y otra información de análisis. 

- Instantáneas de sistemas en ejecución: 

    - Use la funcionalidad de instantáneas de la máquina virtual de Azure para crear una instantánea del disco del sistema en ejecución. 

    - Use la funcionalidad de volcado de la memoria nativa del sistema operativo para crear una instantánea de la memoria del sistema en ejecución.

    - Use la característica de instantánea de los servicios de Azure o la propia funcionalidad del software para crear instantáneas de los sistemas en ejecución.

Azure Sentinel proporciona amplios análisis de datos en prácticamente cualquier origen de registro y un portal de administración de casos para administrar todo el ciclo de vida de los incidentes. La información de inteligencia durante una investigación puede asociarse a un incidente con fines de seguimiento e informes. 

- [Instantánea del disco de una máquina Windows](../virtual-machines/windows/snapshot-copy-managed-disk.md)

- [Instantánea del disco de una máquina Linux](../virtual-machines/linux/snapshot-copy-managed-disk.md)

- [Recopilación del volcado de memoria e información de diagnóstico del servicio de soporte técnico de Microsoft Azure](https://azure.microsoft.com/support/legal/support-diagnostic-information-collection/) 

- [Investigación de incidentes con Azure Sentinel](../sentinel/tutorial-investigate-cases.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="ir-5-detection-and-analysis--prioritize-incidents"></a>IR-5: Detección y análisis: clasificar incidentes

**Guía**: Proporcione contexto a los analistas sobre los incidentes en los que deberán centrarse en primer lugar en función de la gravedad de la alerta y la confidencialidad de los recursos. 

Azure Security Center asigna una gravedad a cada alerta para ayudarle a priorizar aquellas que se deben investigar en primer lugar. La gravedad se basa en la confianza que tiene Security Center en la búsqueda o en el análisis utilizados para emitir la alerta, así como en el nivel de confianza de que ha habido un intento malintencionado detrás de la actividad que ha provocado la alerta.

Adicionalmente, marque los recursos con etiquetas y cree un sistema de nomenclatura para identificar y clasificar los recursos de Azure, especialmente los que procesan datos confidenciales. Es su responsabilidad asignar prioridades a la corrección de las alertas en función de la importancia de los recursos y el entorno de Azure donde se produjo el incidente.

- [Alertas de seguridad en el Centro de seguridad de Azure](../security-center/security-center-alerts-overview.md)

- [Uso de etiquetas para organizar los recursos de Azure](../azure-resource-manager/management/tag-resources.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="ir-6-containment-eradication-and-recovery--automate-the-incident-handling"></a>IR-6: Contención, erradicación y recuperación: automatización del control de incidentes

**Guía**: Automatice las tareas repetitivas manuales para acelerar el tiempo de respuesta y reducir la carga de los analistas. Las tareas manuales tardan más tiempo en ejecutarse, lo que ralentiza cada incidente y reduce el número de incidentes que un analista puede manejar. Las tareas manuales también aumentan la fatiga del analista, lo que aumenta el riesgo del error humano que produce retrasos y reduce la capacidad de los analistas de centrarse de manera efectiva en tareas complejas. Use las características de automatización de flujo de trabajo de Azure Security Center y Azure Sentinel para desencadenar acciones automáticamente o ejecutar un cuaderno de estrategias para responder a las alertas de seguridad entrantes. El cuaderno de estrategias lleva a cabo acciones, como el envío de notificaciones, la deshabilitación de cuentas y el aislamiento de redes problemáticas. 

- [Configuración de la automatización de flujos de trabajo en Security Center](../security-center/workflow-automation.md)

- [Configuración de respuestas automatizadas frente a amenazas en Azure Security Center](../security-center/tutorial-security-incident.md#triage-security-alerts)

- [Configuración de respuestas automatizadas frente a amenazas en Azure Sentinel](../sentinel/tutorial-respond-threats-playbook.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

## <a name="posture-and-vulnerability-management"></a>administración de posturas y vulnerabilidades

*Para más información, consulte [Azure Security Benchmark: administración de posturas y vulnerabilidades](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md).*

### <a name="pv-3-establish-secure-configurations-for-compute-resources"></a>PV-3: establecimiento de configuraciones seguras para los recursos de proceso

**Guía**: use Azure Security Center y Azure Policy para establecer configuraciones seguras en todos los recursos de proceso, incluidas las VM, los contenedores y otros elementos.

Puede usar imágenes de sistema operativo personalizado o la configuración de estado de Azure Automation para establecer la configuración de seguridad del sistema operativo que requiere su organización.

- [Cómo supervisar las recomendaciones de Azure Security Center](../security-center/security-center-recommendations.md) 

- [Introducción a State Configuration de Azure Automation](../automation/automation-dsc-overview.md) 

- [Entorno de Azure Virtual Desktop](environment-setup.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="pv-4-sustain-secure-configurations-for-compute-resources"></a>PV-4: sostenimiento de configuraciones seguras para los recursos de proceso

**Guía**: use Azure Security Center y Azure Policy para evaluar y corregir periódicamente los riesgos de configuración en los recursos de proceso de Azure, incluidas las máquinas virtuales, los contenedores, etc. Además, puede usar plantillas de Azure Resource Manager, imágenes de sistema operativo personalizadas o State Configuration de Azure Automation para mantener la configuración de seguridad del sistema operativo que requiere su organización. Las plantillas de máquina virtual de Microsoft combinadas con State Configuration de Azure Automation pueden ayudar a cumplir y mantener los requisitos de seguridad.

Las imágenes de máquinas virtuales de Azure Marketplace que publica Microsoft las administra y mantiene Microsoft mismo.

Azure Security Center también puede examinar vulnerabilidades en las imágenes de contenedor y realizar una supervisión continua de la configuración de Docker en contenedores basada en la prueba comparativa de Docker de CIS. Puede usar la página de recomendaciones de Azure Security Center para ver las recomendaciones y corregir los problemas.

- [Implementación de las recomendaciones de evaluación de vulnerabilidades de Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md) 

- [Creación de una máquina virtual de Azure a partir de una plantilla de Resource Manager](../virtual-machines/windows/ps-template.md) 

- [Introducción a State Configuration de Azure Automation](../automation/automation-dsc-overview.md) 

- [Seguridad de los contenedores en Security Center](../security-center/container-security.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="pv-5-securely-store-custom-operating-system-and-container-images"></a>PV-5: Almacenamiento seguro de las imágenes de contenedor y de sistema operativo personalizadas

**Guía**: Azure Virtual Desktop permite a los clientes administrar imágenes de sistema operativo. Use el control de acceso basado en rol de Azure (Azure RBAC) para asegurarse de que solo los usuarios autorizados pueden acceder a las imágenes personalizadas. Use una instancia de Azure Shared Image Gallery para compartir imágenes con diferentes usuarios, entidades de servicio o grupos de Active Directory dentro de su organización. Almacene imágenes de contenedor en Azure Container Registry y use RBAC para garantizar que solo los usuarios autorizados puedan tener acceso.

- [Descripción de Azure RBAC](../role-based-access-control/rbac-and-directory-admin-roles.md) 

- [Configuración de Azure RBAC](../role-based-access-control/quickstart-assign-role-user-portal.md) 

- [Introducción a la galería de imágenes compartidas](../virtual-machines/shared-image-galleries.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="pv-6-perform-software-vulnerability-assessments"></a>PV-6: Realización de evaluaciones de vulnerabilidad de software

**Guía**: Azure Virtual Desktop le permite implementar sus propias máquinas virtuales y registrarlas en el servicio, así como hacer que SQL Database se ejecute en el entorno.

Azure Virtual Desktop puede usar una solución de terceros para realizar evaluaciones de vulnerabilidades en dispositivos de red y aplicaciones web. Al realizar exámenes remotos, no use una cuenta administrativa única y perpetua. Considere la posibilidad de implementar la metodología de aprovisionamiento JIT para la cuenta de examen. Las credenciales de la cuenta de examen deben protegerse, supervisarse y utilizarse solo para el examen de vulnerabilidades.

Según se requiera, exporte los resultados de análisis en intervalos coherentes y compare los resultados con análisis anteriores para comprobar que se han corregido las vulnerabilidades.

Siga las recomendaciones de Azure Security Center sobre cómo realizar evaluaciones de vulnerabilidades en sus máquinas virtuales de Azure (y servidores SQL). Azure Security Center tiene un escáner de vulnerabilidades integrado para máquinas virtuales, imágenes de contenedor y bases de datos SQL.

Según se requiera, exporte los resultados de análisis en intervalos coherentes y compare los resultados con análisis anteriores para comprobar que se han corregido las vulnerabilidades. Al usar recomendaciones de administración de vulnerabilidades sugeridas por Azure Security Center, puede dinamizar en el portal de la solución seleccionada para ver los datos de análisis históricos.

- [Implementación de las recomendaciones de evaluación de vulnerabilidades de Azure Security Center](../security-center/deploy-vulnerability-assessment-vm.md) 

- [Escáner de vulnerabilidades integrado para máquinas virtuales](../security-center/deploy-vulnerability-assessment-vm.md) 
- [Evaluación de vulnerabilidades de SQL](../azure-sql/database/sql-vulnerability-assessment.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="pv-7-rapidly-and-automatically-remediate-software-vulnerabilities"></a>PV-7: corrección rápida y automática de vulnerabilidades de software

**Guía**: Azure Virtual Desktop no usa ni requiere software de terceros. No obstante, Azure Virtual Desktop le permite implementar sus propias máquinas virtuales y registrarlas en el servicio.

Use Update Management de Azure Automation o una solución de terceros para asegurarse de que las actualizaciones de seguridad más recientes se instalan en sus máquinas virtuales de Windows Server. En el caso de las máquinas virtuales Windows, asegúrese de que Windows Update se ha habilitado y configurado para actualizarse automáticamente.

Para software de terceros, use una solución de administración de revisiones de terceros o System Center Updates Publisher para Configuration Manager.

- [Configuración de Update Management para máquinas virtuales en Azure](../automation/update-management/overview.md) 

- [Administración de actualizaciones y revisiones para las máquinas virtuales de Azure](../automation/update-management/manage-updates-for-vm.md)

- [Configuración de Microsoft Endpoint Configuration Manager para Azure Virtual Desktop](configure-automatic-updates.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="pv-8-conduct-regular-attack-simulation"></a>PV-8: realización de una simulaciones de ataques periódicas

**Guía**: Azure Virtual Desktop no permite que los clientes realicen sus propias pruebas de penetración en sus recursos de Azure Virtual Desktop.

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Compartido

## <a name="endpoint-security"></a>seguridad de los puntos de conexión

*Para más información, consulte [Azure Security Benchmark: seguridad de los puntos de conexión](../security/benchmarks/security-controls-v2-endpoint-security.md).*

### <a name="es-1-use-endpoint-detection-and-response-edr"></a>ES-1: uso de la detección y respuesta de puntos de conexión (EDR)

**Guía**: Azure Virtual Desktop no proporciona ninguna funcionalidad específica para procesos de detección y respuesta de puntos de conexión (EDR). Sin embargo, los recursos registrados en el servicio sí pueden beneficiarse de estas funcionalidades. 

Habilite las funcionalidades de detección y respuesta de puntos de conexión (EDR) para servidores y clientes, e intégrelas con los procesos de operaciones de seguridad y administración de eventos e información de seguridad.

Protección contra amenazas avanzada de Microsoft Defender proporciona funcionalidades de detección y respuesta de puntos de conexión como parte de una plataforma de seguridad empresarial para puntos de conexión para evitar, detectar, investigar y responder a amenazas avanzadas.

- [Introducción a protección contra amenazas avanzada de Microsoft Defender](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) 

- [Servicio ATP de Microsoft Defender para servidores Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-server-endpoints) 

- [Servicio ATP de Microsoft Defender para servidores que no son de Windows](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-non-windows)

- [Protección contra amenazas avanzada de Microsoft Defender para una infraestructura de escritorio virtual no persistente](/windows/security/threat-protection/microsoft-defender-atp/configure-endpoints-vdi)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="es-2-use-centrally-managed-modern-anti-malware-software"></a>ES-2: Uso de software antimalware moderno administrado centralmente

**Guía**: proteja sus recursos de Azure Virtual Desktop con una solución antimalware de punto de conexión moderna y administrada centralmente capaz de realizar análisis periódicos y en tiempo real.

Azure Security Center puede identificar automáticamente el uso de varias soluciones antimalware populares para las máquinas virtuales e informar del estado de ejecución de la protección del punto de conexión, así como realizar recomendaciones.

Microsoft Antimalware para Azure Cloud Services es el antimalware predeterminado para las máquinas virtuales (VM) Windows. También puede usar la detección de amenazas de Azure Security Center para los servicios de datos con el fin de detectar malware cargado en las cuentas de Azure Storage.

- [Configuración de Microsoft Antimalware para Cloud Services y Virtual Machines](../security/fundamentals/antimalware.md) 

- [Soluciones de protección de punto de conexión compatibles](../security-center/security-center-services.md?tabs=features-windows#supported-endpoint-protection-solutions-)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="es-3-ensure-anti-malware-software-and-signatures-are-updated"></a>ES-3: Asegúrese de que se han actualizado el software y las firmas antimalware

**Guía**: Asegúrese de que las firmas antimalware se actualizan de forma rápida y coherente.

Siga las recomendaciones en Azure Security Center: "Proceso y aplicaciones" para asegurarse de que todas las máquina virtuales y contenedores estén actualizados con las firmas más recientes.

De forma predeterminada, Microsoft Antimalware instalará automáticamente las últimas firmas y actualizaciones del motor.

- [Implementación de Microsoft Antimalware para Azure Cloud Services y Virtual Machines](../security/fundamentals/antimalware.md) 

- [Valoración y recomendaciones de Endpoint Protection en Azure Security Center](../security-center/security-center-endpoint-protection.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

## <a name="backup-and-recovery"></a>Copia de seguridad y recuperación

*Para más información, consulte [Azure Security Benchmark: Copia de seguridad y recuperación](../security/benchmarks/security-controls-v2-backup-recovery.md).*

### <a name="br-1-ensure-regular-automated-backups"></a>BR-1: Garantía de copias de seguridad automáticas periódicas

**Guía**: Asegúrese de realizar copias de seguridad de sistemas y datos para mantener la continuidad empresarial después de un evento inesperado. Esto debería guiarse por los objetivos del objetivo de punto de recuperación (RPO) y el objetivo de tiempo de recuperación (RTO).

Habilite Azure Backup y configure el origen de las copias de seguridad (por ejemplo, máquinas virtuales de Azure, SQL Server, bases de datos de HANA o recursos compartidos de archivos), así como la frecuencia y el período de retención deseados.

Para un nivel más alto de redundancia, puede habilitar la opción de almacenamiento con redundancia geográfica para replicar los datos de copia de seguridad en una región secundaria y recuperar mediante la restauración entre regiones.

- [Continuidad empresarial y recuperación ante desastres](/azure/cloud-adoption-framework/ready/enterprise-scale/business-continuity-and-disaster-recovery) 

- [Habilitación de Azure Backup](../backup/index.yml) 

- [Habilitación de la restauración entre regiones](../backup/backup-azure-arm-restore-vms.md#cross-region-restore) 

- [Procedimiento para configurar un plan de continuidad empresarial y recuperación ante desastres en Azure Virtual Desktop](disaster-recovery.md)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="br-2-encrypt-backup-data"></a>BR-2: Cifrado de los datos de copia de seguridad

**Guía**: Asegúrese de que las copias de seguridad están protegidas frente a ataques. Esto debe incluir el cifrado de las copias de seguridad para proteger frente a la pérdida de confidencialidad.

En el caso de las copias de seguridad periódicas de servicios de Azure, los datos de copia de seguridad se cifran automáticamente mediante claves administradas por la plataforma de Azure. Puede optar por cifrar la copia de seguridad mediante claves administradas por el cliente. En este caso, asegúrese de que esta clave administrada por el cliente del almacén de claves también esté en el ámbito de la copia de seguridad.

Use el control de acceso basado en roles en Azure Backup, Azure Key Vault u otros recursos para proteger las copias de seguridad y las claves administradas por el cliente. Además, puede habilitar características de seguridad avanzadas que requieran autenticación multifactor antes de modificar o eliminar copias de seguridad.

Información general sobre las características de seguridad de Azure Backup /azure/backup/security-overview 

- [Cifrado de datos de copia de seguridad mediante claves administradas por el cliente](../backup/encryption-at-rest-with-cmk.md) 

- [Copias de seguridad de las claves de Key Vault en Azure](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey?preserve-view=true&view=azurermps-6.13.0)

- [Características de seguridad que ayudan a proteger las copias de seguridad híbridas frente a ataques](../backup/backup-azure-security-feature.md#prevent-attacks)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

### <a name="br-3-validate-all-backups-including-customer-managed-keys"></a>BR-3: Validación de todas las copias de seguridad, incluidas las claves administradas por el cliente

**Guía**: se recomienda validar la integridad de los datos en los medios de copia de seguridad de forma periódica realizando un proceso de restauración de datos para asegurarse de que la copia de seguridad funciona correctamente.

- [Recuperación de archivos desde una copia de seguridad de máquina virtual de Azure](../backup/backup-azure-restore-files-from-vm.md)

- [Implementación de seguridad](../backup/backup-azure-restore-files-from-vm.md#security-implementations)

**Supervisión de Azure Security Center**: no disponible actualmente

**Responsabilidad**: Customer

## <a name="governance-and-strategy"></a>Gobernanza y estrategia

*Para más información, consulte [Azure Security Benchmark: gobernanza y estrategia](../security/benchmarks/security-controls-v2-governance-strategy.md).*

### <a name="gs-1-define-asset-management-and-data-protection-strategy"></a>GS-1: Definición de la estrategia de protección de datos y administración de recursos 

**Guía**: Asegúrese de documentar y comunicar una estrategia clara para la protección y supervisión continua de sistemas y datos. Dé prioridad a la detección, evaluación, protección y supervisión de los sistemas y datos críticos para la empresa. 

Esta estrategia debe incluir instrucciones, directivas y estándares documentados para los siguientes elementos: 

-   Norma de clasificación de datos de acuerdo con los riesgos empresariales

-   Visibilidad en la organización de seguridad de los riesgos y el inventario de recursos 

-   Aprobación de la organización de seguridad de los servicios de Azure para su uso 

-   Seguridad de los recursos durante su ciclo de vida

-   Estrategia de control de acceso necesaria según la clasificación de datos de la organización

-   Uso de las funcionalidades de protección de datos nativa de Azure y de terceros

-   Requisitos de cifrado de datos para casos de uso en tránsito y en reposo

-   Normas criptográficas adecuadas

Para más información, consulte las siguientes referencias:
- [Recomendación para la arquitectura de seguridad de Azure: almacenamiento, datos y cifrado](/azure/architecture/framework/security/storage-data-encryption?bc=%2fsecurity%2fcompass%2fbreadcrumb%2ftoc.json&toc=%2fsecurity%2fcompass%2ftoc.json)

- [Aspectos básicos de la seguridad de Azure: seguridad, cifrado y almacenamiento de datos de Azure](../security/fundamentals/encryption-overview.md)

- [Cloud Adoption Framework: procedimientos recomendados de cifrado y seguridad de los datos de Azure](../security/fundamentals/data-encryption-best-practices.md?bc=%2fazure%2fcloud-adoption-framework%2f_bread%2ftoc.json&toc=%2fazure%2fcloud-adoption-framework%2ftoc.json)

- [Azure Security Benchmark: administración de recursos](../security/benchmarks/security-controls-v2-asset-management.md)

- [Azure Security Benchmark: protección de datos](../security/benchmarks/security-controls-v2-data-protection.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="gs-2-define-enterprise-segmentation-strategy"></a>GS-2: Definición de una estrategia de segmentación empresarial 

**Guía**: Establezca en toda la empresa una estrategia para segmentar el acceso a los recursos mediante una combinación de identidad, red, aplicación, suscripción, grupo de administración y otros controles.

Equilibre concienzudamente la necesidad de separación de seguridad con la necesidad de habilitar el funcionamiento diario de los sistemas que necesitan comunicarse entre sí y acceder a los datos.

Asegúrese de que la estrategia de segmentación se implementa de forma coherente en todos los tipos de control, como la seguridad de red, los modelos de identidad y acceso, y los modelos de acceso y permisos de las aplicaciones, y los controles de los procesos humanos.

- [Guía de la estrategia de segmentación en Azure (vídeo)](/security/compass/microsoft-security-compass-introduction#azure-components-and-reference-model-2151)

- [Guía de la estrategia de segmentación en Azure (documento)](/security/compass/governance#enterprise-segmentation-strategy)

- [Alineación de la segmentación de red con la estrategia de segmentación empresarial](/security/compass/network-security-containment#align-network-segmentation-with-enterprise-segmentation-strategy)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="gs-3-define-security-posture-management-strategy"></a>GS-3: Definición de la estrategia de administración de la posición de seguridad

**Guía**: Mida y mitigue continuamente los riesgos de los recursos individuales y el entorno en el que se hospedan. Dé prioridad a los recursos de gran valor y a las superficies de ataque muy expuestas, como las aplicaciones publicadas, los puntos de entrada y salida de red, los puntos de conexión de usuario y administrador, etc.

- [Azure Security Benchmark: administración de posturas y vulnerabilidades](../security/benchmarks/security-controls-v2-posture-vulnerability-management.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="gs-4-align-organization-roles-responsibilities-and-accountabilities"></a>GS-4: Alineación de los roles y responsabilidades de la organización

**Guía**: Asegúrese de documentar y comunicar una estrategia clara para los roles y las responsabilidades de la organización de seguridad. Dé prioridad a ofrecer una responsabilidad clara en las decisiones de seguridad, a proporcionar a todos los usuarios formación sobre el modelo de responsabilidad compartida y a los equipos técnicos sobre la tecnología para proteger la nube.

- [Procedimiento recomendado de seguridad de Azure 1. Personas: Formación del equipo sobre el recorrido de seguridad de la nube](/azure/cloud-adoption-framework/security/security-top-10#1-people-educate-teams-about-the-cloud-security-journey)

- [Procedimiento recomendado de seguridad de Azure 2. Personas: Formación del equipo en tecnología de seguridad de la nube](/azure/cloud-adoption-framework/security/security-top-10#2-people-educate-teams-on-cloud-security-technology)

- [Procedimiento recomendado de seguridad de Azure 3. Proceso: Asignación de responsabilidades por las decisiones de seguridad en la nube](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="gs-5-define-network-security-strategy"></a>GS-5: Definición de la estrategia de seguridad de red

**Guía**: Establezca un enfoque de seguridad de red de Azure como parte de la estrategia de control de acceso de seguridad general de su organización.  

Esta estrategia debe incluir instrucciones, directivas y estándares documentados para los siguientes elementos: 

-   Centralización de la responsabilidad de seguridad y la administración de redes

-   Modelo de segmentación de la red virtual alineado con la estrategia de segmentación empresarial

-   Estrategia de corrección en diferentes escenarios de amenazas y ataques

-   Estrategia de entrada y salida y perimetral de Internet

-   Estrategia de interconectividad entre el entorno local y la nube híbrida

-   Artefactos de seguridad de red actualizados (por ejemplo, diagramas de red y arquitectura de red de referencia)

Para más información, consulte las siguientes referencias:
- [Procedimiento recomendado de seguridad de Azure 11. Arquitectura: Establecimiento de una estrategia de seguridad unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Azure Security Benchmark: seguridad de red](../security/benchmarks/security-controls-v2-network-security.md)

- [Azure Network Security Overview (Información general sobre Azure Network Security)](../security/fundamentals/network-overview.md)

- [Estrategia para la arquitectura de red empresarial](/azure/cloud-adoption-framework/ready/enterprise-scale/architecture)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="gs-6-define-identity-and-privileged-access-strategy"></a>GS-6: Definición de la estrategia de acceso con privilegios e identidades

**Guía**: Establezca una identidad de Azure y enfoques de acceso con privilegios como parte de la estrategia de control de acceso de seguridad general de su organización.  

Esta estrategia debe incluir instrucciones, directivas y estándares documentados para los siguientes elementos: 

-   Un sistema de identidad y autenticación centralizado y su interconectividad con otros sistemas de identidad internos y externos

-   Métodos de autenticación sólida en diferentes casos de uso y condiciones

-   Protección de usuarios con privilegios elevados

-   Supervisión y control de anomalías en las actividades de los usuarios  

-   Proceso de revisión y conciliación del acceso y la identidad de los usuarios

Para más información, consulte las siguientes referencias:

- [Azure Security Benchmark: administración de identidades](../automation/update-management/overview.md)

- [Azure Security Benchmark: acceso con privilegios](../security/benchmarks/security-controls-v2-privileged-access.md)

- [Procedimiento recomendado de seguridad de Azure 11. Arquitectura: Establecimiento de una estrategia de seguridad unificada](/azure/cloud-adoption-framework/security/security-top-10#11-architecture-establish-a-single-unified-security-strategy)

- [Información general sobre seguridad de administración de identidades de Azure](../security/fundamentals/identity-management-overview.md)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

### <a name="gs-7-define-logging-and-threat-response-strategy"></a>GS-7: Definición de la estrategia de registro y respuesta a amenazas

**Guía**: Establezca una estrategia de registro y respuesta a amenazas para detectar y corregir rápidamente las amenazas mientras cumple los requisitos de cumplimiento. Para dar prioridad, proporcione a los analistas alertas de alta calidad y experiencias sin problemas para que puedan centrarse en las amenazas en lugar de los pasos manuales y de integración. 

Esta estrategia debe incluir instrucciones, directivas y estándares documentados para los siguientes elementos: 

-   Las responsabilidades y el rol de la organización en las operaciones de seguridad (SecOps) 

-   Un proceso de respuesta a incidentes bien definido que esté alineado con NIST u otro marco del sector. 

-   Captura y retención de registros para admitir la detección de amenazas, la respuesta ante incidentes y las necesidades de cumplimiento

-   Visibilidad y correlación centralizada de la información sobre amenazas, mediante SIEM, funcionalidades nativas de Azure y otros orígenes 

-   Plan de comunicación y notificación con sus clientes, proveedores y entidades públicas de interés

-   Uso de plataformas nativas de Azure y de terceros para el tratamiento de incidentes, como el registro y la detección de amenazas, los análisis forenses y la corrección y erradicación de ataques

-   Procesos para controlar incidentes y actividades posteriores a incidentes, como las lecciones aprendidas y la retención de pruebas

Para más información, consulte las siguientes referencias:

- [Azure Security Benchmark: registro y detección de amenazas](../security/benchmarks/security-controls-v2-logging-threat-detection.md)

- [Azure Security Benchmark: respuesta a incidentes](../security/benchmarks/security-controls-v2-incident-response.md)

- [Procedimiento recomendado de seguridad de Azure 4. Proceso: Actualización de los procesos de respuesta a incidentes para la nube](/azure/cloud-adoption-framework/security/security-top-10#4-process-update-incident-response-ir-processes-for-cloud)

- [Guía de decisiones sobre registros e informes en Azure Adoption Framework](/azure/cloud-adoption-framework/decision-guides/logging-and-reporting/)

- [Escala, administración y supervisión empresarial de Azure](/azure/cloud-adoption-framework/ready/enterprise-scale/management-and-monitoring)

**Supervisión de Azure Security Center**: No aplicable

**Responsabilidad**: Customer

## <a name="next-steps"></a>Pasos siguientes

- Consulte la [Información general sobre Azure Security Benchmark V2](../security/benchmarks/overview.md).
- Obtenga más información sobre las [líneas de base de seguridad de Azure](../security/benchmarks/security-baselines-overview.md).
