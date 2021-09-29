---
title: Base de referencia de seguridad de Azure para Azure Cloud Services
description: La base de referencia de seguridad de Azure Cloud Services proporciona instrucciones de procedimientos y recursos para implementar las recomendaciones de seguridad especificadas en Azure Security Benchmark.
author: msmbaldwin
ms.service: cloud-services
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: mbaldwin
ms.custom: subject-security-benchmark
ms.openlocfilehash: 814514ede598d0de435c767ee29ef6aa4564d003
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128616581"
---
# <a name="azure-security-baseline-for-azure-cloud-services"></a>Base de referencia de seguridad de Azure para Azure Cloud Services

Esta base de referencia de seguridad aplica la guía de la [versión 1.0 de Azure Security Benchmark](../security/benchmarks/overview-v1.md) en Microsoft Azure Cloud Services. Azure Security Benchmark proporciona recomendaciones sobre cómo puede proteger sus soluciones de nube en Azure.
El contenido se agrupa por los **controles de seguridad** definidos en Azure Security Benchmark y las directrices relacionadas aplicables a Cloud Services. Se han excluido los **controles** no aplicables a Cloud Services.

 
Para ver cómo Cloud Services se asigna por completo a Azure Security Benchmark, consulte el [archivo completo de asignación de línea de base de seguridad de Cloud Services](https://github.com/MicrosoftDocs/SecurityBenchmarks/tree/master/Azure%20Offer%20Security%20Baselines).

## <a name="network-security"></a>Seguridad de redes

*Para más información, consulte [Azure Security Benchmark: seguridad de red](../security/benchmarks/security-control-network-security.md).*

### <a name="11-protect-azure-resources-within-virtual-networks"></a>1.1: Protección de los recursos de Azure dentro de las redes virtuales

**Guía**: Cree una instancia clásica de Azure Virtual Network con subredes públicas y privadas independientes para aplicar el aislamiento según puertos de confianza e intervalos IP. Esta red y subredes virtuales deben ser los recursos basados en Virtual Network clásica (implementación clásica) y no los recursos actuales de Azure Resource Manager.  

Permita o rechace el tráfico mediante un grupo de seguridad de red, que contiene reglas de control de acceso según la dirección del tráfico, el protocolo, la dirección de origen y el puerto, y la dirección de destino y el puerto. Las reglas de un grupo de seguridad de red pueden cambiarse en cualquier momento; los cambios se aplican a todas las instancias asociadas.

Microsoft Azure Cloud Services (clásica) no se puede colocar en redes virtuales de Azure Resource Manager. Sin embargo, las redes virtuales basadas en Resource Manager y las redes virtuales basadas en implementaciones clásicas pueden conectarse a través de emparejamiento. 

- [Introducción a los grupo de seguridad de red](../virtual-network/network-security-groups-overview.md)

- [Emparejamiento de redes virtuales](./cloud-services-connectivity-and-networking-faq.yml#how-can-i-use-azure-resource-manager-virtual-networks-with-cloud-services-)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="12-monitor-and-log-the-configuration-and-traffic-of-virtual-networks-subnets-and-nics"></a>1.2: Supervisión y registro de la configuración y el tráfico de redes virtuales, subredes y NIC

**Guía**: Documente la configuración de Azure Cloud Services y supervise si tiene cambios. Use el archivo de configuración de servicio para especificar el número de instancias de rol que se implementan para cada rol en el servicio, los valores de los parámetros de configuración y las huellas digitales de los certificados asociados a un rol. 

Si el servicio forma parte de una red virtual, se debe proporcionar información de configuración de la red en el archivo de configuración de servicio, así como en el archivo de configuración de red virtual. La extensión predeterminada del archivo de configuración de servicio es. cscfg. Tenga en cuenta que Azure Policy no se admite para las implementaciones clásicas para la aplicación de la configuración.

Establezca los valores de configuración de un servicio en la nube en el archivo de configuración del servicio (.cscfg), y la definición en un archivo de definición de servicio (.csdef). Use el archivo de definición de servicio para definir el modelo de servicio de una aplicación. Defina los roles, que están disponibles para un servicio en la nube, y también especifique los puntos de conexión de servicio. Registre la configuración de Azure Cloud Services con el archivo de configuración de servicio. Cualquier reconfiguración se puede realizar a través del archivo ServiceConfig.cscfg. 

Supervise la definición de servicio del elemento NetworkTrafficRules opcional, que restringe qué roles pueden comunicarse con puntos de conexión internos especificados. Configure el nodo NetworkTrafficRules, un elemento opcional en el archivo de definición de servicio, para especificar el modo en que los roles deben comunicarse entre sí. Defina límites a qué roles pueden acceder a los puntos de conexión internos del rol en cuestión.  Tenga en cuenta que no se puede modificar la definición del servicio. 

Habilite los registros de flujo de los grupos de seguridad de red y envíe los registros a una cuenta de Azure Storage para su auditoría. Envíe los registros de flujo a un área de trabajo de Log Analytics y luego use Análisis de tráfico para proporcionar conclusiones sobre los patrones de tráfico en el inquilino de Azure. Algunas de las ventajas del Análisis de tráfico son la capacidad de visualizar la actividad de la red, identificar las zonas activas y las amenazas de seguridad, comprender los patrones de flujo de tráfico y detectar configuraciones de red incorrectas.

- [Implementación mediante Azure Resource Manager frente a la implementación clásica: Conozca los modelos de implementación y el estado de los recursos](../azure-resource-manager/management/deployment-models.md)

- [Archivo de configuración de Cloud Services](schema-cscfg-file.md)

- [Lista de servicios admitidos por Azure Policy](/cli/azure/azure-services-the-azure-cli-can-manage)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="13-protect-critical-web-applications"></a>1.3: Proteja las aplicaciones web críticas

**Guía**: Microsoft usa el protocolo Seguridad de la capa de transporte (TLS) v1.2 para proteger los datos cuando se transmiten entre Azure Cloud Services y los clientes. Los centros de datos de Microsoft negocian una conexión TLS con sistemas cliente que se conectan a servicios de Azure. TLS proporciona una autenticación sólida, privacidad de mensajes e integridad (lo que permite la detección de la manipulación, interceptación y falsificación de mensajes), interoperabilidad, flexibilidad de algoritmo, y facilidad de implementación y uso.

- [Aspectos básicos del cifrado](../security/fundamentals/encryption-overview.md)

- [Configuración de certificados TLS/SSL](cloud-services-configure-ssl-certificate-portal.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="14-deny-communications-with-known-malicious-ip-addresses"></a>1.4: Deniegue las comunicaciones con direcciones IP malintencionadas conocidas

**Guía**: Azure Cloud implementa una seguridad de red multicapa para proteger sus servicios de plataforma contra los ataques de denegación de servicio distribuido (DDoS). Azure DDoS Protection forma parte del proceso de supervisión continuo de Azure Cloud, que mejora continuamente a través de pruebas de penetración. DDoS Protection está diseñado para resistir ataques no solo desde el exterior, sino también de otros inquilinos de Azure. 

Hay varias maneras de bloquear o denegar la comunicación además de la protección de nivel de plataforma dentro de Azure Cloud Services. Dichos componentes son: 

-  Crear una tarea de inicio para bloquear de manera selectiva algunas direcciones IP específicas
-  Restringir un acceso de rol web de Azure a un conjunto de direcciones IP especificadas modificando su archivo web.config de IIS

Impida el tráfico entrante a la dirección URL predeterminada o al nombre de su instancia de Cloud Services, por ejemplo, *.cloudapp.net. Establezca el encabezado de host en un nombre DNS personalizado, en la configuración de enlace de sitio del archivo de definición (* .csdef) de Cloud Services.

Configure una regla de denegación de aplicación a las asignaciones de administrador de suscripciones clásicas. De forma predeterminada, una vez definido el extremo interno, la comunicación puede fluir sin restricciones desde cualquier rol hasta el extremo interno de otro rol. Para limitar la comunicación, debe agregar un elemento NetworkTrafficRules al elemento ServiceDefinition en el archivo de definición de servicio.

- [¿Cómo puedo bloquear o deshabilitar el tráfico entrante a la dirección URL predeterminada de un servicio en la nube?](./cloud-services-connectivity-and-networking-faq.yml#how-can-i-block-disable-incoming-traffic-to-the-default-url-of-my-cloud-service-)

- [Azure DDOS Protection](./cloud-services-connectivity-and-networking-faq.yml#how-do-i-prevent-receiving-thousands-of-hits-from-unknown-ip-addresses-that-might-indicate-a-malicious-attack-to-the-cloud-service-)

- [Bloqueo de una dirección IP específica](./cloud-services-startup-tasks-common.md#block-a-specific-ip-address)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="15-record-network-packets"></a>1.5: Registro de los paquetes de red

**Guía**: Use Azure Network Watcher, un servicio de supervisión, diagnóstico y análisis del rendimiento de red que permite supervisar redes de Azure. La extensión de máquina virtual del agente de Network Watcher es un requisito para capturar el tráfico de red a petición y otra funcionalidad avanzada en Azure Virtual Machines. Instale la extensión de máquina virtual del agente de Network Watcher y active los registros de flujo del grupo de seguridad de red.

Configure los registros de flujo en un grupo de seguridad de red. Revise los detalles sobre cómo implementar la extensión de máquina virtual de Network Watcher en una máquina virtual existente implementada mediante el modelo de implementación clásica.

- [Configuración de los registros de flujo en un grupo de seguridad de red](../virtual-machines/extensions/network-watcher-linux.md)

- [Para obtener más información sobre cómo configurar los registros de flujo, consulte aquí](/cli/azure/azure-services-the-azure-cli-can-manage)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="16-deploy-network-based-intrusion-detectionintrusion-prevention-systems-idsips"></a>1.6: Implementación de sistemas de prevención de intrusiones y detección de intrusiones (IDS/IPS) basados en la red

**Guía**: Azure Cloud Services no tiene ninguna funcionalidad integrada de IDS ni IPS. Los clientes pueden seleccionar e implementar una solución de IDS o IPS basada en red complementaria desde Azure Marketplace en función de los requisitos de su organización. Al usar soluciones de terceros, asegúrese de probar exhaustivamente la solución de IDS o IPS seleccionada con Azure Cloud Services para garantizar el funcionamiento y la funcionalidad adecuados.

- [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/?term=Firewall)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="17-manage-traffic-to-web-applications"></a>1.7: Administre el tráfico a las aplicaciones web

**Guía**: Los certificados de servicio, que están conectados a Azure Cloud Services, permiten la comunicación segura con el servicio como origen y destino. Estos certificados se establecen en la definición de los servicios y se implementan automáticamente en la máquina virtual que ejecuta una instancia de un rol web. Por ejemplo, para un rol web, puede usar un certificado de servicio que pueda autenticar un punto de conexión HTTPS expuesto. 

Para actualizar el certificado, solo es necesario cargar un nuevo certificado y cambiar el valor de la huella digital en el archivo de configuración de servicio.

Use el protocolo TLS 1.2, el método más usado para proteger los datos, para proporcionar confidencialidad y protección de integridad. 

Por lo general, para proteger las aplicaciones web frente a ataques como OWASP Top 10, puede implementar Azure Application Gateway compatible con Azure Web Application Firewall para proteger las aplicaciones web. 

- [Certificados de servicio](cloud-services-certs-create.md)

- [Configuración de TLS para una aplicación en Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Implementación de Application Gateway](../application-gateway/quick-create-portal.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="19-maintain-standard-security-configurations-for-network-devices"></a>1.9: Mantenga las configuraciones de seguridad estándar para dispositivos de red

**Guía**: Proteja la configuración de Azure Cloud Services y supervise si tiene cambios. El archivo de configuración de servicio especifica el número de instancias de rol que se implementan para cada rol del servicio, los valores de los parámetros de configuración y las huellas digitales de los certificados asociados a un rol. 

Si el servicio forma parte de una red virtual, se debe proporcionar la información de configuración de la red en el archivo de configuración de servicio, así como en el archivo de configuración de red virtual. La extensión predeterminada del archivo de configuración de servicio es. cscfg.

Tenga en cuenta que Azure Policy no se admite con Azure Cloud Services para la aplicación de la configuración.

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="110-document-traffic-configuration-rules"></a>1.10: Documente las reglas de configuración de tráfico

**Guía**: Se pueden usar grupos de seguridad de red de Azure para filtrar el tráfico de red hacia y desde los recursos de Azure de una instancia de Azure Virtual Network. Un grupo de seguridad de red contiene reglas de seguridad que permiten o deniegan el tráfico de red entrante, o el tráfico de red saliente, de varios tipos de recursos de Azure. Para cada regla, puede especificar un origen y destino, un puerto y un protocolo.

Use el campo "Descripción" para las reglas de grupo de seguridad de red individuales dentro de Azure Cloud Services para documentar las reglas, que permiten el tráfico desde o hacia una red.

- [Filtrado del tráfico de red con reglas de grupos de seguridad de red](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="111-use-automated-tools-to-monitor-network-resource-configurations-and-detect-changes"></a>1.11: Use herramientas automatizadas para supervisar las configuraciones de recursos de red y detectar cambios

**Guía**: Use las características de supervisión de puntos de conexión integrados y la conmutación por error automática de los puntos de conexión de Azure Traffic Manager. Estas le ayudan a ofrecer aplicaciones de alta disponibilidad que son resistentes a los errores de punto de conexión y a los errores de las regiones de Azure. Para configurar la supervisión de los puntos de conexión, debe especificar ciertos valores en el perfil de Traffic Manager.

Obtenga conclusiones del registro de actividad, un registro de plataforma de Azure, sobre los eventos del nivel de suscripción. Incluye información como, por ejemplo, cuándo se modificó un recurso o cuándo se inició una máquina virtual. Vea el registro de actividad en Azure Portal o recupere las entradas con PowerShell y la CLI. 

Cree una configuración de diagnóstico para enviar el registro de actividad a Azure Monitor, Azure Event Hubs, para reenviarlo fuera de Azure, o a Azure Storage, para archivarlo. Configure Azure Monitor para las alertas de notificación cuando cambien los recursos críticos en Azure Cloud Services. 

- [Registro de actividad de Azure](../azure-monitor/essentials/activity-log.md)

- [Crear, ver y administrar las alertas del registro de actividad mediante Azure Monitor](../azure-monitor/alerts/alerts-activity-log.md)

- [Supervisión de Traffic Manager](../traffic-manager/traffic-manager-monitoring.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

## <a name="logging-and-monitoring"></a>Registro y supervisión

*Para más información, consulte [Azure Security Benchmark: registro y supervisión](../security/benchmarks/security-control-logging-monitoring.md).*

### <a name="21-use-approved-time-synchronization-sources"></a>2.1: Uso de orígenes de sincronización de hora aprobados

**Guía**: Microsoft mantiene los orígenes de hora de los recursos de Azure para Azure Cloud Services. Es posible que los clientes tengan que crear una regla de red para permitir el acceso a un servidor de hora que se use en su entorno, a través del puerto 123 con el protocolo UDP.

- [Acceso al servidor NTP](../firewall/protect-azure-virtual-desktop.md#additional-considerations)

**Responsabilidad**: Compartido

**Supervisión de Azure Security Center**: ninguna

### <a name="22-configure-central-security-log-management"></a>2.2: Configuración de la administración central de registros de seguridad

**Guía**: Consuma datos de streaming del servicio en la nube mediante programación con Azure Event Hubs. Integre y envíe todos estos datos a Azure Sentinel para supervisar y revisar los registros, o use la SIEM de un tercero. Para la administración central del registro de seguridad, configure la exportación continua de los datos de Azure Security Center elegidos a Azure Event Hubs y configure el conector adecuado para la SIEM. Estas son algunas opciones de Azure Sentinel, incluidas herramientas de terceros:

- Azure Sentinel: Use el conector nativo de datos de las alertas de Azure Security Center.
- Splunk: Use el complemento de Azure Monitor para Splunk.
- IBM QRadar: use un origen de registro configurado manualmente
- ArcSight: use SmartConnector

Revise la documentación de Azure Sentinel para obtener más detalles sobre los conectores disponibles con Azure Sentinel. 

- [Conexión con orígenes de datos](../sentinel/connect-data-sources.md)

- [Integración en una SIEM](../security-center/continuous-export.md)

- [Almacenamiento de datos de diagnóstico](diagnostics-extension-to-storage.md)

- [Configuración de la integración de SIEM a través de Azure Event Hubs](../security-center/continuous-export.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="23-enable-audit-logging-for-azure-resources"></a>2.3: Habilitación del registro de auditoría para recursos de Azure

**Guía**: Configure Visual Studio para configurar Azure Diagnostics para la solución de problemas de Azure Cloud Services, lo que captura los datos del sistema y del registro en máquinas virtuales, incluidas las instancias de máquina virtual que ejecutan su instancia de Azure Cloud Services. Los datos de Diagnostics se transfieren a la cuenta de almacenamiento de su elección. Active el diagnóstico en los proyectos de Azure Cloud Services antes de su implementación.

 
Vea el Historial de cambios de algunos eventos en el registro de actividad en Azure Monitor. Audite los cambios que se produjeron durante el período de un evento. Elija un evento del registro de actividad para una inspección más profunda en la pestaña Historial de cambios (versión preliminar). Envíe los datos de diagnóstico a Application Insights cuando publique Azure Cloud Services desde Visual Studio. Cree el recurso de Azure Application Insights en cualquier momento o envíe los datos a un recurso existente de Azure. 

Application Insights puede supervisar la disponibilidad, el rendimiento, los errores y el uso de Azure Cloud Services. Se pueden agregar gráficos personalizados a Application Insights, para poder ver los datos que más interesan. Los datos de instancias de rol pueden recopilarse con el SDK de Application Insights en el proyecto de Azure Cloud Services. 

- [Activación del diagnóstico en Visual Studio antes de la implementación](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines#to-turn-on-diagnostics-in-visual-studio-before-deployment)

- [Visualización del historial de cambios](../azure-monitor/essentials/activity-log.md#view-change-history)

- [Application Insights para Azure Cloud Services (clásico)](../azure-monitor/app/cloudservices.md)

- [Configuración de diagnósticos para Azure Cloud Services (clásico) y máquinas virtuales](/visualstudio/azure/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="25-configure-security-log-storage-retention"></a>2.5: Configuración de la retención del almacenamiento de registros de seguridad

**Guía**: Puede usar la supervisión avanzada con Azure Cloud Services, lo que permite que se muesteen y recopilen métricas adicionales en intervalos de 5 minutos, 1 hora y 12 horas. Los datos agregados se almacenan en una cuenta de almacenamiento, en tablas, y se purgan después de 10 días. Sin embargo, la cuenta de almacenamiento usada se configura en función del rol, y puede usar diferentes cuentas de almacenamiento para distintos roles. La configuración se realiza con una cadena de conexión en los archivos .csdef y .cscfg.

Tenga en cuenta que la supervisión avanzada conlleva el uso de la extensión de Azure Diagnostics (el SDK de Application Insights es opcional) en el rol que se quiere supervisar. La extensión de Diagnostics usa un archivo de configuración (por rol) denominado diagnostics.wadcfgx para configurar las métricas de diagnóstico supervisadas. La extensión Azure Diagnostic recopila y almacena los datos en una cuenta de Azure Storage. Estos valores se configuran en los archivos .wadcfgx, .csdef y .cscfg.

- [Introducción a la supervisión de servicios en la nube](cloud-services-how-to-monitor.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="26-monitor-and-review-logs"></a>2.6: Supervisión y registros de revisión

**Guía**: Hay disponibles modos de supervisión básica o avanzada para Azure Cloud Services. Azure Cloud Services recopila automáticamente datos de supervisión básica (porcentaje de CPU, E/S de red y lectura/escritura de disco) desde una máquina virtual de host. Vea los datos de supervisión recopilados en las páginas de información general y métricas del servicio en la nube en Azure Portal. 

Habilite el diagnóstico en Azure Cloud Services para recopilar datos de diagnóstico, como registros de aplicaciones, contadores de rendimiento, etc., mientras usa la extensión de Azure Diagnostics. Habilite o deshabilite la configuración de diagnóstico en un servicio en la nube que ya esté en ejecución, con el cmdlet Set-AzureServiceDiagnosticsExtension, o implemente un servicio en la nube con la extensión de diagnóstico de manera automática. De manera opcional, instale el SDK de Application Insights. Envíe los contadores de rendimiento a Azure Monitor.

La extensión Azure Diagnostic recopila y almacena los datos en una cuenta de Azure Storage. Transfiera los datos de Diagnostic al Emulador de Microsoft Azure Storage o a Azure Storage, ya que no se almacenan de forma permanente. Una vez en el almacenamiento, puede verlos con una de las diversas herramientas disponibles, como el Explorador de servidores en Visual Studio, el Explorador de Microsoft Azure Storage o Azure Management Studio. Configure las métricas de diagnóstico que va a supervisar con el archivo de configuración (por rol) denominado diagnostics.wadcfgx en la extensión de diagnóstico. 

- [Introducción a la supervisión de servicios en la nube](cloud-services-how-to-monitor.md)

- [Habilitación del diagnósticos en un rol de trabajo: integración en una SIEM](../security-center/continuous-export.md)

- [Habilitar el diagnóstico en Azure Cloud Services mediante PowerShell](cloud-services-diagnostics-powershell.md)

- [Almacenamiento y visualización de los datos de diagnóstico en Azure Storage](./diagnostics-extension-to-storage.md?preserve-view=)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="27-enable-alerts-for-anomalous-activities"></a>2.7: Habilitación de alertas para actividades anómalas

**Guía**: Puede supervisar los datos de registro de Azure Cloud Services mediante la integración en Azure Sentinel, o con la SIEM de un tercero, al habilitar las alertas para actividades anómalas.

- [Integración en una SIEM](../security-center/continuous-export.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="28-centralize-anti-malware-logging"></a>2.8: Centralización del registro antimalware

**Guía**: Microsoft Antimalware para Azure protege Azure Cloud Services y las máquinas virtuales. Además tiene la opción de implementar soluciones de seguridad de terceros, como firewalls de aplicación web, firewalls de red, antimalware, detección de intrusiones, además de sistemas de prevención (IDS o IPS) y mucho más.

- [¿Cuáles son las características y funcionalidades que proporcionan los IPS/IDS y DDoS básicos de Azure?](./cloud-services-configuration-and-management-faq.yml#what-are-the-features-and-capabilities-that-azure-basic-ips-ids-and-ddos-provides-)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

## <a name="identity-and-access-control"></a>Identidad y Access Control

*Para más información, consulte [Azure Security Benchmark: identidad y control de acceso](../security/benchmarks/security-control-identity-access-control.md).*

### <a name="31-maintain-an-inventory-of-administrative-accounts"></a>3.1: Mantenga un inventario de cuentas administrativas

**Guía**: Microsoft recomienda que administre el acceso a los recursos de Azure mediante el control de acceso basado en rol (RBAC) de Azure. Sin embargo, Azure Cloud Services no es compatible con el modelo de Azure RBAC, ya que no es un servicio basado en Azure Resource Manager, y es necesario usar una suscripción clásica.

De manera predeterminada, administrador de cuenta, administrador de servicios y coadministrador son las tres variantes de roles de administrador de suscripciones clásicas de Azure. 

Los administradores de suscripción clásica tienen acceso completo a la suscripción de Azure. Se pueden administrar los recursos con Azure Portal, con las API de Azure Resource Manager o bien con las API del modelo de implementación clásica. La cuenta que se utiliza para suscribirse a Azure se establece automáticamente como administrador de cuenta y administrador de servicio. Se pueden agregar coadministradores adicionales más adelante. 

Tanto el administrador de servicios como los coadministradores tienen un acceso equivalente al de los usuarios a los que se les ha asignado el rol Propietario (un rol de Azure) en el ámbito de la suscripción. Administre los coadministradores o vea el administrador de servicios mediante la pestaña Administradores clásicos en Azure Portal. 

Enumere las asignaciones de roles para el administrador y los coadministradores del servicio clásico con el comando de PowerShell:

Get-AzRoleAssignment -IncludeClassicAdministrators

Revise las diferencias entre los roles administrativos de la suscripción clásica. 

- [Diferencias entre los roles administrativos de la suscripción clásica](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="33-use-dedicated-administrative-accounts"></a>3.3: Use cuentas administrativas dedicadas

**Guía**: Se recomienda crear procedimientos operativos estándar en torno al uso de cuentas administrativas dedicadas, en función de los roles disponibles y de los permisos necesarios para usar y administrar los recursos de Azure Cloud Services.

- [Diferencias entre los roles administrativos de la suscripción clásica](../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="34-use-single-sign-on-sso-with-azure-active-directory"></a>3.4: Use el inicio de sesión único (SSO) con Azure Active Directory

**Guía**: Evite administrar identidades independientes para las aplicaciones que se ejecutan en Azure Cloud Services. Implemente el inicio de sesión único para evitar que los usuarios tengan que administrar varias identidades y credenciales.

- [¿Qué es el inicio de sesión único (SSO)?](../active-directory/manage-apps/what-is-single-sign-on.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="36-use-dedicated-machines-privileged-access-workstations-for-all-administrative-tasks"></a>3.6: Use máquinas dedicadas (estaciones de trabajo de acceso con privilegios) para todas las tareas administrativas

**Guía**: Se recomienda usar una estación de trabajo segura y administrada por Azure (también conocida como "estación de trabajo de acceso con privilegios") para las tareas administrativas, que requieren privilegios elevados.

- [Descripción de las estaciones de trabajo seguras administradas por Azure](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/)

- [Habilitación de la autenticación multifactor de Azure Active Directory (Azure AD)](../active-directory/authentication/howto-mfa-getstarted.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

## <a name="data-protection"></a>Protección de datos

*Para más información, consulte [Azure Security Benchmark: protección de datos](../security/benchmarks/security-control-data-protection.md).*

### <a name="41-maintain-an-inventory-of-sensitive-information"></a>4.1: Mantenimiento de un inventario de información confidencial

**Guía**: Use las API de REST de Azure Cloud Services para realizar un inventario de los recursos de Azure Cloud Services para la información confidencial. Sondee los recursos de servicio en la nube implementados para obtener la configuración y los recursos .pkg.

 Como ejemplo, a continuación se enumeran algunas API:

- Get Deployment: La operación Get Deployment devuelve información de configuración, de estado y de las propiedades del sistema para una implementación.
- Get Package: La operación Get Package recupera un paquete de servicios en la nube para una implementación y almacena los archivos del paquete en Microsoft Azure Blob Storage.
- Get Cloud Service Properties: La operación Get Cloud Service Properties recupera las propiedades para el servicio en la nube especificado.

Revise la documentación de las API de REST de Azure Cloud Services y cree un proceso para la protección de datos confidenciales, en función de los requisitos de su organización.

- [Get Deployment](/rest/api/compute/cloudservices/rest-get-deployment)

- [Propiedades Get del servicio en la nube](/rest/api/compute/cloudservices/rest-get-cloud-service-properties)

- [Obtener paquete](/rest/api/compute/cloudservices/rest-get-package)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="42-isolate-systems-storing-or-processing-sensitive-information"></a>4.2: Aislamiento de los sistemas que almacenan o procesan información confidencial

**Guía**: Implemente el aislamiento mediante suscripciones y grupos de administración independientes para dominios de seguridad individuales, como el tipo de entorno y el nivel de confidencialidad de los datos para Azure Cloud Services.

También puede editar "permissionLevel" en el elemento de certificado de Azure Cloud Services para especificar los permisos de acceso otorgados a los procesos de rol. Si solo quiere que los procesos elevados puedan acceder a la clave privada, especifique el permiso elevated. El permiso limitedOrElevated permite que todos los procesos del rol accedan a la clave privada. Los valores posibles son limitedOrElevated o elevated. El valor predeterminado es limitedOrElevated.

- [Creación de suscripciones adicionales de Azure](../cost-management-billing/manage/create-subscription.md)

- [Creación de grupos de administración](../governance/management-groups/create-management-group-portal.md)

- [Esquema WebRole](./schema-csdef-webrole.md#Certificate)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="43-monitor-and-block-unauthorized-transfer-of-sensitive-information"></a>4.3: Supervisión y bloqueo de una transferencia no autorizada de información confidencial

**Guía**: Se recomienda usar una solución de terceros de Azure Marketplace en los perímetros de la red para supervisar la transferencia no autorizada de información confidencial y bloquear dichas transferencias al tiempo que alerta a los profesionales de seguridad de la información.

- [Descripción de la protección de datos de los clientes en Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidad**: Compartido

**Supervisión de Azure Security Center**: ninguna

### <a name="44-encrypt-all-sensitive-information-in-transit"></a>4.4: Cifrado de toda la información confidencial en tránsito

**Guía**: Configure TLS v2 para Azure Cloud Services. Use Azure Portal para agregar el certificado a la implementación de Azure Cloud Services de ensayo y agregue la información del certificado a los archivos CSDEF y CSCFG de los servicios. Vuelva a empaquetar la aplicación y actualice la implementación de ensayo para usar el nuevo paquete. 

Use los certificados de servicio en Azure, que están conectados a Azure Cloud Services, para habilitar la comunicación segura con el servicio como origen y destino. Proporcione un certificado que pueda autenticar un punto de conexión HTTPS expuesto. Defina los certificados de servicio en la definición del servicio en la nube, e impleméntelos automáticamente en la máquina virtual que ejecuta una instancia del rol.

Autenticarse con la API de administración con certificados de administración le permite autenticarse con el modelo de implementación clásica. Muchos programas y herramientas (como Visual Studio o Azure SDK) usan estos certificados para automatizar la configuración y la implementación de diferentes servicios de Azure. 

Como referencia adicional, la API del modelo de implementación clásica proporciona acceso mediante programación a la funcionalidad del modelo de implementación clásica, disponible a través de Azure Portal. Se puede usar Azure SDK para Python para administrar Azure Cloud Services y las cuentas de Azure Storage. Azure SDK para Python encapsula la API del modelo de implementación clásica, que es una API REST. Todas las operaciones de la API se realizan mediante TLS y se autentican mutuamente con los certificados X.509 v3. Se puede obtener acceso al servicio de administración desde un servicio que se ejecute en Azure. También se puede acceder directamente a través de Internet desde cualquier aplicación que envíe una solicitud HTTPS y reciba una respuesta HTTPS.

- [Configuración de TLS para una aplicación en Azure](cloud-services-configure-ssl-certificate-portal.md)

- [Uso de la administración de servicios de Python](cloud-services-python-how-to-use-service-management.md)

**Responsabilidad**: Compartido

**Supervisión de Azure Security Center**: ninguna

### <a name="45-use-an-active-discovery-tool-to-identify-sensitive-data"></a>4.5: Uso de una herramienta de detección activa para identificar datos confidenciales

**Guía**: Se recomienda usar una herramienta de detección activa de terceros para identificar toda la información confidencial almacenada, procesada o transmitida por los sistemas tecnológicos de la organización, incluidos los de la ubicación local o los que se encuentran en un proveedor de servicios remoto, y actualizar el inventario de información confidencial de la organización.

- [Descripción de la protección de datos de los clientes en Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidad**: Compartido

**Supervisión de Azure Security Center**: ninguna

### <a name="47-use-host-based-data-loss-prevention-to-enforce-access-control"></a>4.7: Uso de la prevención de pérdida de datos basada en host para aplicar el control de acceso

**Guía**: No se aplica a Cloud Services (clásico). No exige la prevención de pérdida de datos.

Se recomienda implementar una herramienta de terceros, como una solución de prevención de pérdida de datos basada en host automatizada, para aplicar controles de acceso a los datos incluso cuando se copian datos de un sistema.

En el caso de la plataforma subyacente administrada por Microsoft, Microsoft trata todo el contenido de los clientes como confidencial y hace grandes esfuerzos para proteger a los clientes contra la pérdida y exposición de sus datos. Para garantizar la seguridad de los datos de los clientes en Azure, Microsoft ha implementado y mantiene un conjunto de controles y funcionalidades eficaces de protección de datos.

- [Descripción de la protección de datos de los clientes en Azure](../security/fundamentals/protection-customer-data.md)

**Responsabilidad**: Compartido

**Supervisión de Azure Security Center**: ninguna

### <a name="48-encrypt-sensitive-information-at-rest"></a>4.8: Cifrado de información confidencial en reposo

**Guía**: Azure Cloud Services no admite el cifrado en reposo. Esto se debe a que Azure Cloud Services está diseñado para no tener estado. Azure Cloud Services admite el almacenamiento externo, por ejemplo, Azure Storage, que, de manera predeterminada, se cifra en reposo.  

Los datos de aplicación almacenados en discos temporales no se cifran. El cliente es responsable de administrar y cifrar estos datos, según sea necesario.  

- [Descripción del cifrado en reposo en Azure](../security/fundamentals/encryption-atrest.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="49-log-and-alert-on-changes-to-critical-azure-resources"></a>4.9: Registro y alerta de cambios en los recursos críticos de Azure

**Guía**: Puede usar las alertas de métricas clásicas en Azure Monitor para obtener notificaciones cuando una de sus métricas aplicadas a recursos críticos cruce un umbral. Las alertas de métricas clásicas son una funcionalidad más antigua que permite enviar alertas solo en métricas no dimensionales. Existe una funcionalidad más reciente existente llamada Alertas de métricas que ha mejorado la funcionalidad de las alertas de métricas clásicas. 

Además, Application Insights puede supervisar la disponibilidad, el rendimiento, los errores y el uso de las aplicaciones de Azure Cloud Services. Esto usa datos combinados de los SDK de Application Insights con datos de Azure Diagnostics de su instancia de Azure Cloud Services.

- [Creación, visualización y administración de alertas de métricas clásicas mediante Azure Monitor](../azure-monitor/alerts/alerts-classic-portal.md)

- [Información general de las alertas de métricas](../azure-monitor/alerts/alerts-metric-overview.md) 

- [Application Insights para Azure Cloud Services (clásico)](../azure-monitor/app/cloudservices.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

## <a name="vulnerability-management"></a>Administración de vulnerabilidades

*Para más información, consulte [Azure Security Benchmark: administración de vulnerabilidades](../security/benchmarks/security-control-vulnerability-management.md).*

### <a name="52-deploy-automated-operating-system-patch-management-solution"></a>5.2: Implemente una solución de administración de revisiones de sistema operativo automatizada

**Guía**: Tenga en cuenta que esta información se relaciona con el sistema operativo invitado de Azure para roles web y de trabajo de Azure Cloud Services con la plataforma como servicio (PaaS). No obstante, no se aplica a Virtual Machines con la infraestructura como servicio (IaaS).

De manera predeterminada, Azure actualiza periódicamente el sistema operativo invitado del cliente a la imagen compatible más reciente dentro de la familia del sistema operativo que ha especificado en la configuración del servicio (.cscfg), como Windows Server 2016.

Cuando un cliente elige una versión específica del sistema operativo para su implementación de Azure Cloud Services, deshabilita las actualizaciones automáticas del sistema operativo y hace que la aplicación de revisiones sea su responsabilidad. El cliente tiene que asegurarse de que las instancias de rol reciban actualizaciones o, de lo contrario, puede exponer su aplicación a vulnerabilidades de seguridad.

- [SO invitado de Azure](cloud-services-guestos-msrc-releases.md)

- [Directiva de compatibilidad y retirada del SO invitado de Azure](cloud-services-guestos-retirement-policy.md)

- [Configuración de Cloud Service (clásico)](cloud-services-how-to-configure-portal.md)

- [Administración de la versión del SO invitado](./cloud-services-how-to-configure-portal.md#manage-guest-os-version)

**Responsabilidad**: Compartido

**Supervisión de Azure Security Center**: ninguna

### <a name="53-deploy-an-automated-patch-management-solution-for-third-party-software-titles"></a>5.3: Implementación de una solución de administración de revisiones automatizada de títulos de software de terceros

**Guía**: Use una solución de administración de revisiones de terceros. Los clientes que ya usan Configuration Manager en su entorno también pueden usar System Center Updates Publisher, lo que les permite publicar actualizaciones personalizadas en Windows Server Update Service. 

Esto permite que Update Management revise las máquinas que usan Configuration Manager como repositorio de actualizaciones con software de terceros.

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="55-use-a-risk-rating-process-to-prioritize-the-remediation-of-discovered-vulnerabilities"></a>5.5: Use un proceso de clasificación de riesgos para priorizar la corrección de las vulnerabilidades detectadas

**Guía**: Se recomienda que un cliente comprenda el alcance del riesgo de un ataque de DDoS de forma continuada. 

Se recomienda pensar en los escenarios siguientes:

- ¿Qué nuevos recursos de Azure que están públicamente disponibles necesitan protección?
- ¿Hay un único punto de error en el servicio?
- ¿Cómo pueden aislarse los servicios para limitar el impacto de un ataque, manteniendo al mismo tiempo los servicios disponibles para los clientes legítimos?
- ¿Hay redes virtuales en las que DDoS Protection Standard debería estar habilitado y no lo está?
- ¿Estás mis servicios activos o activos con conmutación por error en varias regiones?

Documentación complementaria:

- [Evaluación del riesgo de los recursos de Azure](../security/fundamentals/ddos-best-practices.md#risk-evaluation-of-your-azure-resources)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

## <a name="inventory-and-asset-management"></a>Administración de recursos y del inventario

*Para más información, consulte [Azure Security Benchmark: inventario y administración de recursos](../security/benchmarks/security-control-inventory-asset-management.md).*

### <a name="61-use-automated-asset-discovery-solution"></a>6.1: Uso de la solución de detección de recursos automatizada

**Guía**: No se aplica a Azure Cloud Services. Esta recomendación se aplica a recursos de proceso de IaaS.

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="63-delete-unauthorized-azure-resources"></a>6.3: Eliminación de recursos de Azure no autorizados

**Guía**: Se recomienda conciliar el inventario periódicamente y asegurarse de que los recursos no autorizados se eliminen de la suscripción de manera oportuna.

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="64-define-and-maintain-an-inventory-of-approved-azure-resources"></a>6.4: Definición y mantenimiento de un inventario de los recursos de Azure aprobados

**Guía**: El cliente debe definir los recursos de Azure y el software aprobados para los recursos de proceso.

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="65-monitor-for-unapproved-azure-resources"></a>6.5: Supervisión de recursos de Azure no aprobados

**Guía**: Use la característica Control de aplicaciones adaptables, disponible en Azure Security Center. Se trata de una solución inteligente, automatizada e integral de Security Center, que ayuda a controlar qué aplicaciones se pueden ejecutar en las máquinas Windows y Linux de Azure y en las que no son de Azure. También ayuda a proteger las máquinas frente a malware. 

Esta característica está disponible tanto para máquinas virtuales y servidores de Azure como Windows (todas las versiones, clásica o Azure Resource Manager) y Linux que no son de Azure.

Security Center usa el aprendizaje automático para analizar las aplicaciones que se ejecutan en sus máquinas virtuales y crea una lista de permitidas a partir de esta inteligencia. Esta funcionalidad simplifica enormemente el proceso de configuración y mantenimiento de directivas de listas de aplicaciones permitidas, lo que le permite:
- Bloquear o alertar sobre intentos de ejecución de aplicaciones malintencionadas, incluidas aquellas que podrían ser omitidas por las soluciones antimalware.

- Cumplir con la directiva de seguridad de la organización que dicta el uso de software con licencia únicamente.
- Evitar el uso de software no deseado en el entorno.
- Evitar la ejecución de aplicaciones anteriores y no compatibles.
- Impedir herramientas de software específicas no permitidas en la organización.
- Permiten que TI controle el acceso a información confidencial a través del uso de aplicaciones.

Encontrará más detalles en los vínculos a los que se hace referencia.

- [Controles de aplicación adaptables](../security-center/security-center-adaptive-application.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="66-monitor-for-unapproved-software-applications-within-compute-resources"></a>6.6: Supervisión de aplicaciones de software no aprobadas en recursos de proceso

**Guía**: Use la característica Control de aplicaciones adaptables, disponible en Azure Security Center. Se trata de una solución inteligente, automatizada e integral de Security Center, que ayuda a controlar qué aplicaciones se pueden ejecutar en las máquinas Windows y Linux de Azure y en las que no son de Azure. También ayuda a proteger las máquinas frente a malware. 

Esta característica está disponible tanto para máquinas virtuales y servidores de Azure como Windows (todas las versiones, clásica o Azure Resource Manager) y Linux que no son de Azure.

Security Center usa el aprendizaje automático para analizar las aplicaciones que se ejecutan en sus máquinas virtuales y crea una lista de permitidas a partir de esta inteligencia. Esta funcionalidad simplifica enormemente el proceso de configuración y mantenimiento de directivas de listas de aplicaciones permitidas, lo que le permite:

- Bloquear o alertar sobre intentos de ejecución de aplicaciones malintencionadas, incluidas aquellas que podrían ser omitidas por las soluciones antimalware.

- Cumplir con la directiva de seguridad de la organización que dicta el uso de software con licencia únicamente.

- Evitar el uso de software no deseado en el entorno.

- Evitar la ejecución de aplicaciones anteriores y no compatibles.

- Impedir herramientas de software específicas no permitidas en la organización.

- Permiten que TI controle el acceso a información confidencial a través del uso de aplicaciones.

Encontrará más detalles en los vínculos a los que se hace referencia.

- [Controles de aplicación adaptables](../security-center/security-center-adaptive-application.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="67-remove-unapproved-azure-resources-and-software-applications"></a>6.7: Eliminación de aplicaciones de software y recursos de Azure no aprobadas

**Guía**: Use la característica Control de aplicaciones adaptables, disponible en Azure Security Center. Se trata de una solución inteligente, automatizada e integral de Security Center, que ayuda a controlar qué aplicaciones se pueden ejecutar en las máquinas Windows y Linux de Azure y en las que no son de Azure. También ayuda a proteger las máquinas frente a malware. 

Esta característica está disponible tanto para máquinas virtuales y servidores de Azure como Windows (todas las versiones, clásica o Azure Resource Manager) y Linux que no son de Azure.

Security Center usa el aprendizaje automático para analizar las aplicaciones que se ejecutan en sus máquinas virtuales y crea una lista de permitidas a partir de esta inteligencia. Esta funcionalidad simplifica enormemente el proceso de configuración y mantenimiento de directivas de listas de aplicaciones permitidas, lo que le permite:

- Bloquear o alertar sobre intentos de ejecución de aplicaciones malintencionadas, incluidas aquellas que podrían ser omitidas por las soluciones antimalware.

- Cumplir con la directiva de seguridad de la organización que dicta el uso de software con licencia únicamente.

- Evitar el uso de software no deseado en el entorno.

- Evitar la ejecución de aplicaciones anteriores y no compatibles.

- Impedir herramientas de software específicas no permitidas en la organización.

- Permiten que TI controle el acceso a información confidencial a través del uso de aplicaciones.

Encontrará más detalles en los vínculos a los que se hace referencia.

- [Controles de aplicación adaptables](../security-center/security-center-adaptive-application.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="68-use-only-approved-applications"></a>6.8: Uso exclusivo de aplicaciones aprobadas

**Guía**: Use la característica Control de aplicaciones adaptables, disponible en Azure Security Center. Se trata de una solución inteligente, automatizada e integral de Security Center, que ayuda a controlar qué aplicaciones se pueden ejecutar en las máquinas Windows y Linux de Azure y en las que no son de Azure. También ayuda a proteger las máquinas frente a malware. 

Esta característica está disponible tanto para máquinas virtuales y servidores de Azure como Windows (todas las versiones, clásica o Azure Resource Manager) y Linux que no son de Azure.

Security Center usa el aprendizaje automático para analizar las aplicaciones que se ejecutan en sus máquinas virtuales y crea una lista de permitidas a partir de esta inteligencia. Esta funcionalidad simplifica enormemente el proceso de configuración y mantenimiento de directivas de listas de aplicaciones permitidas, lo que le permite:

- Bloquear o alertar sobre intentos de ejecución de aplicaciones malintencionadas, incluidas aquellas que podrían ser omitidas por las soluciones antimalware.

- Cumplir con la directiva de seguridad de la organización que dicta el uso de software con licencia únicamente.

- Evitar el uso de software no deseado en el entorno.

- Evitar la ejecución de aplicaciones anteriores y no compatibles.

- Impedir herramientas de software específicas no permitidas en la organización.

- Permiten que TI controle el acceso a información confidencial a través del uso de aplicaciones.

Encontrará más detalles en los vínculos a los que se hace referencia.

- [Controles de aplicación adaptables](../security-center/security-center-adaptive-application.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="610-maintain-an-inventory-of-approved-software-titles"></a>6.10: Mantenimiento de un inventario de títulos de software aprobados

**Guía**: Use la característica Control de aplicaciones adaptables, disponible en Azure Security Center. Se trata de una solución inteligente, automatizada e integral de Security Center, que ayuda a controlar qué aplicaciones se pueden ejecutar en las máquinas Windows y Linux de Azure y en las que no son de Azure. También ayuda a proteger las máquinas frente a malware. 

Esta característica está disponible tanto para máquinas virtuales y servidores de Azure como Windows (todas las versiones, clásica o Azure Resource Manager) y Linux que no son de Azure.

Security Center usa el aprendizaje automático para analizar las aplicaciones que se ejecutan en sus máquinas virtuales y crea una lista de permitidas a partir de esta inteligencia. Esta funcionalidad simplifica enormemente el proceso de configuración y mantenimiento de directivas de listas de aplicaciones permitidas, lo que le permite:
- Bloquear o alertar sobre intentos de ejecución de aplicaciones malintencionadas, incluidas aquellas que podrían ser omitidas por las soluciones antimalware.

- Cumplir con la directiva de seguridad de la organización que dicta el uso de software con licencia únicamente.
- Evitar el uso de software no deseado en el entorno.
- Evitar la ejecución de aplicaciones anteriores y no compatibles.
- Impedir herramientas de software específicas no permitidas en la organización.
- Permiten que TI controle el acceso a información confidencial a través del uso de aplicaciones.

Encontrará más detalles en los vínculos a los que se hace referencia.

- [Controles de aplicación adaptables](../security-center/security-center-adaptive-application.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="612-limit-users-ability-to-execute-scripts-in-compute-resources"></a>6.12: Limitación de la capacidad de los usuarios para ejecutar scripts en los recursos de proceso

**Guía**: Use la característica Control de aplicaciones adaptables, disponible en Azure Security Center. Se trata de una solución inteligente, automatizada e integral de Security Center, que ayuda a controlar qué aplicaciones se pueden ejecutar en las máquinas Windows y Linux de Azure y en las que no son de Azure. También ayuda a proteger las máquinas frente a malware. 

Esta característica está disponible tanto para máquinas virtuales y servidores de Azure como Windows (todas las versiones, clásica o Azure Resource Manager) y Linux que no son de Azure.

Security Center usa el aprendizaje automático para analizar las aplicaciones que se ejecutan en sus máquinas virtuales y crea una lista de permitidas a partir de esta inteligencia. Esta funcionalidad simplifica enormemente el proceso de configuración y mantenimiento de directivas de listas de aplicaciones permitidas, lo que le permite:

- Bloquear o alertar sobre intentos de ejecución de aplicaciones malintencionadas, incluidas aquellas que podrían ser omitidas por las soluciones antimalware.

- Cumplir con la directiva de seguridad de la organización que dicta el uso de software con licencia únicamente.

- Evitar el uso de software no deseado en el entorno.

- Evitar la ejecución de aplicaciones anteriores y no compatibles.

- Impedir herramientas de software específicas no permitidas en la organización.

- Permiten que TI controle el acceso a información confidencial a través del uso de aplicaciones.

Encontrará más detalles en los vínculos a los que se hace referencia.

- [Controles de aplicación adaptables](../security-center/security-center-adaptive-application.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="613-physically-or-logically-segregate-high-risk-applications"></a>6.13: Segregación física o lógica de aplicaciones de alto riesgo

**Guía**: En el caso de aplicaciones confidenciales o de alto riesgo con Azure Cloud Services, implemente suscripciones o grupos de administración independientes para proporcionar aislamiento.

Use un grupo de seguridad de red, cree una regla de seguridad de entrada, elija un servicio como http, elija también un puerto personalizado, asígnele una prioridad y un nombre. La prioridad afecta al orden en que se aplican las reglas: cuanto más bajo es el valor numérico, antes se aplica la regla. Deberá asociar el grupo de seguridad de red a una subred o una interfaz de red específica para aislar o segmentar el tráfico de red en función de sus necesidades empresariales.

Encontrará más detalles en los vínculos a los que se hace referencia.

- [Tutorial: Filtrado del tráfico de red con un grupo de seguridad de red mediante Azure Portal](../virtual-network/tutorial-filter-network-traffic.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

## <a name="secure-configuration"></a>Configuración segura

*Para más información, consulte [Azure Security Benchmark: configuración segura](../security/benchmarks/security-control-secure-configuration.md).*

### <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Establezca configuraciones seguras para todos los recursos de Azure

**Guía**: Use las recomendaciones de Azure Security Center como línea base de configuración segura para los recursos de Azure Cloud Services. 

En Azure Portal, elija Security Center, luego Procesos y aplicaciones y Azure Cloud Services para ver las recomendaciones aplicables a los recursos del servicio.

- [Guía de referencia sobre las recomendaciones de seguridad](../security-center/recommendations-reference.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Mantenga configuraciones de recursos de Azure seguras

**Guía**: No se aplica a Azure Cloud Services. Se basa en el modelo de implementación clásica. Se recomienda usar la solución de un tercero para mantener configuraciones seguras de recursos de Azure.

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Almacene de forma segura la configuración de los recursos de Azure

**Guía**: El archivo de configuración de Azure Cloud Services almacena los atributos operativos de un recurso. Puede almacenar una copia de los archivos de configuración en una cuenta de almacenamiento segura.

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Implementación de herramientas de administración de configuración para recursos de Azure

**Guía**: No se aplica a Azure Cloud Services. Se basa en el modelo de implementación clásica y no se puede administrar mediante herramientas de configuración basadas en una implementación de Azure Resource Manager.

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Implementación de herramientas de administración de la configuración para sistemas operativos

**Guía**: No se aplica a Azure Cloud Services. Esta recomendación se aplica a los recursos de proceso basados en infraestructura como servicio (IaaS).

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Implementación de la supervisión de la configuración automatizada para los recursos de Azure

**Guía**: Use Azure Security Center para realizar análisis de base de referencia para los recursos de Azure.  

- [Corrección de recomendaciones en Azure Security Center](../security-center/security-center-remediate-recommendations.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Implemente la supervisión de configuración automatizada para sistemas operativos

**Guía**: En Azure Security Center, elija la característica Proceso y aplicaciones, y siga las recomendaciones para máquinas virtuales, servidores y contenedores.

- [Descripción de las recomendaciones sobre contenedores de Azure Security Center](../security-center/container-security.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="711-manage-azure-secrets-securely"></a>7.11: Administre los secretos de Azure de forma segura

**Guía**: Azure Cloud Services se basa en un modelo de implementación clásica y no se integra en Azure Key Vault.

Puede proteger los secretos, como las credenciales que se usan en Azure Cloud Services, para no tener que escribir una contraseña cada vez. Para empezar, especifique una contraseña de texto sin formato y conviértala en una cadena segura con el comando de PowerShell ConvertTo-SecureString. Luego, convierta esta cadena segura en una cadena estándar cifrada mediante ConvertFrom-SecureString.  Ya puede guardar esta cadena estándar cifrada en un archivo con Set-Content.

Además, se recomienda almacenar las claves privadas de los certificados usados en Azure Cloud Services en un almacenamiento seguro.

- [Configuración de Escritorio remoto desde PowerShell](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Elimine la exposición de credenciales no intencionada

**Guía**: Proteja los secretos, como las credenciales que se usan en Azure Cloud Services, para no tener que escribir una contraseña cada vez. 
 

Para empezar, especifique una contraseña de texto sin formato y cámbiela a una cadena segura con el comando de PowerShell ConvertTo-SecureString. Luego, convierta esta cadena segura en una cadena estándar cifrada mediante ConvertFrom-SecureString. Ahora puede guardar esta cadena estándar cifrada en un archivo con el comando Set-Content.

Almacene las claves privadas de los certificados usados en Azure Cloud Services en una ubicación de almacenamiento segura.

- [Configuración de Escritorio remoto desde PowerShell](./cloud-services-role-enable-remote-desktop-powershell.md#configure-remote-desktop-from-powershell)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

## <a name="malware-defense"></a>Defensa contra malware

*Para más información, consulte [Azure Security Benchmark: defensa contra malware](../security/benchmarks/security-control-malware-defense.md).*

### <a name="81-use-centrally-managed-antimalware-software"></a>8.1: Uso de software antimalware administrado centralmente

**Guía**: Microsoft Antimalware para Azure está disponible para Azure Cloud Services y Virtual Machines. Se trata de una protección gratuita en tiempo real que ayuda a identificar y eliminar virus, spyware y otros programas de software malintencionados. Genera alertas cuando software no deseado o malintencionado intenta instalarse o ejecutarse en los sistemas de Azure. 

Use el cmdlet de Antimalware basado en PowerShell para obtener la configuración de Antimalware, con Get-AzureServiceAntimalwareConfig.

Habilite la extensión Antimalware con un script de PowerShell en la tarea de inicio de Azure Cloud Services.

Elija la característica de control de aplicaciones adaptables en Azure Security Center, una solución automatizada, inteligente e integral. Ayuda a proteger las máquinas frente al malware y le permite bloquear o alertar sobre intentos de ejecución de aplicaciones malintencionadas, incluidas aquellas que podrían ser omitidas por las soluciones antimalware.

- [¿Cómo puedo agregar una extensión antimalware para Azure Cloud Services de forma automática?](./cloud-services-configuration-and-management-faq.yml#how-can-i-add-an-antimalware-extension-for-my-cloud-services-in-an-automated-way-)

- [Escenarios de implementación de Antimalware](../security/fundamentals/antimalware.md#antimalware-deployment-scenarios)

- [Controles de aplicación adaptables](../security-center/security-center-adaptive-application.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

## <a name="incident-response"></a>Respuesta a los incidentes

*Para más información, consulte [Azure Security Benchmark: respuesta ante incidentes](../security/benchmarks/security-control-incident-response.md).*

### <a name="101-create-an-incident-response-guide"></a>10.1: Creación de una guía de respuesta ante incidentes

**Instrucciones**: Cree una guía de respuesta a incidentes para su organización. Asegúrese de que haya planes de respuesta a incidentes escritos que definan todos los roles del personal, así como las fases de administración y gestión de los incidentes, desde la detección hasta la revisión posterior a la incidencia.

- [Configuración de las automatizaciones de flujos de trabajo en Azure Security Center](../security-center/security-center-planning-and-operations-guide.md)

- [Guía para crear su propio proceso de respuesta a incidentes de seguridad](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [Anatomía de un incidente del Centro de respuestas de seguridad de Microsoft](https://msrc-blog.microsoft.com/2019/07/01/inside-the-msrc-building-your-own-security-incident-response-process)

- [El cliente también puede usar la guía de control de incidentes de seguridad de equipos de NIST como referencia para la creación de su propio plan de respuesta a incidentes](https://nvlpubs.nist.gov/nistpubs/SpecialPublications/NIST.SP.800-61r2.pdf)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="102-create-an-incident-scoring-and-prioritization-procedure"></a>10.2: Creación de un procedimiento de priorización y puntuación de incidentes

**Instrucciones**: Azure Security Center asigna una gravedad a cada alerta para ayudarle a priorizar aquellas que se deben investigar en primer lugar. La gravedad se basa en la confianza que tiene Security Center en la búsqueda o en el análisis utilizados para emitir la alerta, así como en el nivel de confianza de que ha habido un intento malintencionado detrás de la actividad que ha provocado la alerta. 

Marque las suscripciones de forma clara (por ejemplo, producción o no producción) y cree un sistema de nomenclatura para identificar y clasificar claramente los recursos de Azure.

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="103-test-security-response-procedures"></a>10.3: Prueba de los procedimientos de respuesta de seguridad

**Instrucciones**: Realice ejercicios para probar las capacidades de respuesta a los incidentes de los sistemas con regularidad. Identifique puntos débiles y brechas y revise el plan según sea necesario. 

- [Consulte la publicación de NIST: Guía para probar, entrenar y ejecutar programas para planes y funcionalidades de TI](https://nvlpubs.nist.gov/nistpubs/Legacy/SP/nistspecialpublication800-84.pdf)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="104-provide-security-incident-contact-details-and-configure-alert-notifications-for-security-incidents"></a>10.4: Provisión de detalles de contacto de incidentes de seguridad y configuración de notificaciones de alerta para incidentes de seguridad

**Guía**: La información de contacto del incidente de seguridad la utilizará Microsoft para ponerse en contacto con usted si Microsoft Security Response Center (MSRC) detecta que un tercero no autorizado o ilegal ha accedido a los datos del cliente. Revise los incidentes después del hecho para asegurarse de que se resuelven los problemas. 

- [Establecimiento del contacto de seguridad de Azure Security Center](../security-center/security-center-provide-security-contact-details.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="105-incorporate-security-alerts-into-your-incident-response-system"></a>10.5: Incorporación de alertas de seguridad en el sistema de respuesta a incidentes

**Instrucciones**: Exporte sus alertas y recomendaciones de Azure Security Center mediante la característica de exportación continua. La exportación continua le permite exportar alertas y recomendaciones de forma manual o continua. Puede usar el conector de datos de Security Center para transmitir las alertas a Azure Sentinel. 

- [Configuración de la exportación continua](../security-center/continuous-export.md) 

- [Transmisión de alertas a Azure Sentinel](../sentinel/connect-azure-security-center.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

### <a name="106-automate-the-response-to-security-alerts"></a>10.6: Automatización de la respuesta a las alertas de seguridad

**Instrucciones**: use la característica de automatización del flujo de trabajo de Azure Security Center para desencadenar automáticamente respuestas mediante "Logic Apps" en las alertas y recomendaciones de seguridad.

- [Configuración de la automatización de flujo de trabajo y Logic Apps](../security-center/workflow-automation.md)

**Responsabilidad**: Customer

**Supervisión de Azure Security Center**: ninguna

## <a name="penetration-tests-and-red-team-exercises"></a>Pruebas de penetración y ejercicios del equipo rojo

*Para más información, consulte [Azure Security Benchmark: Pruebas de penetración y ejercicios del equipo rojo](../security/benchmarks/security-control-penetration-tests-red-team-exercises.md).*

### <a name="111-conduct-regular-penetration-testing-of-your-azure-resources-and-ensure-remediation-of-all-critical-security-findings"></a>11.1: Realice pruebas de penetración periódicas de los recursos de Azure y asegúrese de corregir todos los resultados de seguridad críticos

**Guía**: Siga las reglas de compromiso de la prueba de penetración de Microsoft Cloud para asegurarse de que las pruebas de penetración no infrinjan las directivas de Microsoft. Use la estrategia de Microsoft y la ejecución de las pruebas de penetración del equipo rojo y sitios activos en la infraestructura de nube, los servicios y las aplicaciones administradas por Microsoft. 

- [Reglas de interacción de las pruebas de penetración](https://www.microsoft.com/msrc/pentest-rules-of-engagement?rtc=1) 

- [Equipo rojo de Microsoft Cloud](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e)

**Responsabilidad**: Compartido

**Supervisión de Azure Security Center**: ninguna

## <a name="next-steps"></a>Pasos siguientes

- Consulte la [Información general sobre Azure Security Benchmark V2](../security/benchmarks/overview.md).
- Obtenga más información sobre las [líneas de base de seguridad de Azure](../security/benchmarks/security-baselines-overview.md).
