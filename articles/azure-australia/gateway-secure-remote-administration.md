---
title: Protección de la administración remota de la puerta de enlace en Azure Australia
description: Guía sobre la configuración de la administración remota segura en las regiones de Australia para cumplir con los requisitos específicos de las directivas, los reglamentos y la legislación de la administración pública de Australia.
author: emilyre
ms.service: azure-australia
ms.topic: conceptual
ms.date: 07/22/2019
ms.author: yvettep
ms.openlocfilehash: 98554997056f8d09e4b6dde8751432b72fa6a8be
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129232812"
---
# <a name="secure-remote-administration-of-your-gateway-in-azure-australia"></a>Protección de la administración remota de la puerta de enlace en Azure Australia

Es fundamental para la disponibilidad y la integridad de cualquier sistema que las actividades administrativas se realicen de forma segura y controlada. Las actividades administrativas deben realizarse desde un dispositivo seguro, mediante una conexión segura y deben estar respaldadas por procesos de autenticación y autorización seguros. La administración remota segura garantiza que solo se realicen acciones autorizadas y que las realicen solo los administradores autorizados.

En este artículo se proporciona información sobre cómo implementar una funcionalidad de administración remota segura para un sistema accesible a través de Internet hospedado en Azure que conforme a la guía para el consumidor del Australian Cyber Security Centre (ACSC) y la intención del manual de seguridad de la información (ISM) de ACSC.

## <a name="australian-cyber-security-centre-acsc-requirements"></a>Requisitos del Australian Cyber Security Centre (ACSC)

Los requisitos de seguridad generales para los sistemas de la Commonwealth se definen en el manual ISM. Para ayudar a las entidades de la Commonwealth a proporcionar una administración segura, ACSC ha publicado [ACSC Protect: Secure Administration](https://www.acsc.gov.au/publications/protect/secure-administration.htm) (ACSC Protect: Administración segura).

En este documento se describe la importancia de la seguridad en la administración y se sugiere un método para implementar un entorno de administración seguro. En el documento se describen los elementos de una solución de administración segura de la siguiente manera:

|Elemento   |Descripción   |
|---|---|
|Control del acceso con privilegios   |Supervisar el acceso a las cuentas con privilegios es un control de seguridad fundamental que protege las cuentas con privilegios de un uso inadecuado. La metodología de control de acceso abarcará los conceptos de "privilegios mínimos" y "necesarios", así como procesos y los procedimientos de administración de cuentas de servicio y de los movimientos de personal.   |
|Autenticación multifactor   |La implementación de factores adicionales de autenticación además de los nombres de usuario y las frases de contraseña, como los tokens físicos o las tarjetas inteligentes, puede ayudar a proteger los recursos críticos. Si un rol no admitido pone en peligro las credenciales de las cuentas con privilegios, como todas las acciones administrativas tendrían que pasar primero por algún tipo de autenticación multifactor, se reducirían considerablemente las consecuencias.|
|Estaciones de trabajo con privilegios|El uso de un entorno seguro conocido para las tareas administrativas puede suponer un riesgo menor para la red gracias a la implementación de controles de seguridad adicionales.|
|Registro y auditoría   |La generación, la recopilación y el análisis automatizados de eventos relacionados con la seguridad y la administración desde estaciones de trabajo, servidores, dispositivos de red y cuadros de salto permitirán la detección de peligros e intentos de ataque. La automatización permite a las organizaciones responder con mayor rapidez, lo que reduce el riesgo.|
|Segmentación y segregación de red|Segmentar una red en zonas lógicas, como dominios de seguridad diferentes, y seguir segregando estas redes lógicas mediante la restricción de los tipos de datos que fluyen de una zona a otra restringe el movimiento lateral. La segmentación impide que un rol no admitido acceda a más recursos.|
|Cuadros de salto|Un cuadro de salto es un servidor de acceso remoto protegido que suele utilizar el software de Servicios de Escritorio remoto o Secure Shell (SSH) de Microsoft. Los cuadros de salto actúan como peldaño para los administradores que acceden a sistemas críticos, donde todas las acciones administrativas se realizan desde el host dedicado.|

En este artículo se proporciona una arquitectura de referencia sobre cómo se pueden usar los elementos anteriores para la administración segura de sistemas implementados en Azure.

## <a name="architecture"></a>Architecture

Proporcionar una funcionalidad de administración segura requiere que todos los componentes trabajen juntos para formar una solución cohesiva. En la arquitectura de referencia proporcionada, los componentes se asignan a los elementos que se describen en [ACSC Protect: Secure Administration](https://www.acsc.gov.au/publications/protect/secure-administration.htm).

![Arquitectura de administración remota segura de Azure](media/remote-admin.png)

## <a name="components"></a>Componentes

La arquitectura está diseñada para garantizar que a una cuenta con privilegios solo se le concedan los permisos necesarios, que se identifique de forma segura y se le proporcione acceso a las interfaces administrativas solo desde un dispositivo autorizado y mediante mecanismos de comunicación seguros que se controlen y auditen.

|Solución| Componentes|Elementos|
|---|---|---|
|Dispositivos seguros |<ul><li>Estación de trabajo con privilegios</li><li>Dispositivo móvil</li><li>Microsoft Intune</li><li>Directiva de grupo</li><li>Servidor/host de salto</li><li>Administración Just-In-Time (JIT)</li></ul> |<ul><li>Estaciones de trabajo con privilegios</li><li>Cuadros de salto</li></ul>|
|Comunicación segura |<ul><li>Azure portal</li><li>Azure VPN Gateway</li><li>Puerta de enlace de Escritorio remoto (RD)</li><li>Grupos de seguridad de red (NSG)</li></ul> |<ul><li>Segmentación y segregación de red</li></ul>|
|Autenticación sólida |<ul><li>Controlador de dominio (DC)</li><li>Azure Active Directory (Azure AD)</li><li>Servidor de directivas de redes (NPS)</li><li>Azure AD MFA</li></ul> |<ul><li>Autenticación multifactor</li></ul> |
|Autorización segura |<ul><li>Administración de identidad y acceso (IAM)</li><li>Privileged Identity Management (PIM)</li><li>Acceso condicional</li></ul>|<ul><li>Control del acceso con privilegios</li></ul>|
|||

>[!NOTE]
>Para más información sobre el elemento de registro y auditoría, consulte el artículo sobre [el registro, la auditoría y la visibilidad de la puerta de enlace](gateway-log-audit-visibility.md).

## <a name="administration-workflow"></a>Flujo de trabajo de administración

La administración de los sistemas implementados en Azure se divide en dos categorías, la administración de la configuración de Azure y la de las cargas de trabajo implementadas en Azure. La configuración de Azure se realiza mediante Azure Portal y la administración de las cargas de trabajo se completa con mecanismos administrativos como el Protocolo de escritorio remoto (RDP), Secure Shell (SSH) o, para las funcionalidades de PaaS, mediante herramientas como SQL Management Studio.

La obtención de acceso para la administración es un proceso de varios pasos que implica los componentes enumerados en la arquitectura y requiere acceso previo a Azure Portal y a la configuración de Azure para poder acceder a las cargas de trabajo de Azure.

>[!NOTE]
> Los pasos que se describen aquí son el proceso general con los componentes de la interfaz gráfica de usuario (GUI) de Azure. Estos pasos pueden realizarse también con otras interfaces, como PowerShell.

### <a name="azure-configuration-and-azure-portal-access"></a>Configuración de Azure y acceso a Azure Portal

|Paso |Descripción |
|---|---|
|Inicio de sesión en la estación de trabajo con privilegios |El administrador inicia sesión en la estación de trabajo con privilegios mediante credenciales administrativas. Los controles de Directiva de grupo impiden que las cuentas no administrativas se autentiquen en la estación de trabajo con privilegios y evitan que las cuentas administrativas se autentiquen en estaciones de trabajo sin privilegios. Microsoft Intune administra el cumplimiento de la estación de trabajo con privilegios para garantizar que está actualizada con las revisiones de software, antimalware y otros requisitos de cumplimiento. |
|Inicio de sesión en Azure Portal |El administrador abre un explorador web en Azure Portal, cifrado mediante seguridad de la capa de transporte (TLS), e inicia sesión con las credenciales administrativas. La solicitud de autenticación se procesa directamente mediante Azure Active Directory o con mecanismos de autenticación como los Servicios de federación de Active Directory (AD FS) o la autenticación de paso a través. |
|Azure AD MFA |Azure AD MFA envía una solicitud de autenticación al dispositivo móvil registrado de la cuenta con privilegios. Intune administra el dispositivo móvil para garantizar el cumplimiento de los requisitos de seguridad. El administrador debe autenticarse primero en el dispositivo móvil y, a continuación, en la aplicación Microsoft Authenticator con un PIN o sistema biométrico para que el intento de autenticación se autorice en Azure AD MFA. |
|Acceso condicional |Las directivas de acceso condicional comprueban el intento de autenticación para garantizar que cumple los requisitos necesarios, como la dirección IP de la que procede la conexión, la pertenencia a grupos de la cuenta con privilegios y el estado de administración y cumplimiento de la estación de trabajo con privilegios, tal como los indicó Intune. |
|Privileged Identity Management (PIM) |Desde Azure Portal el administrador ahora puede activar o solicitar la activación de los roles con privilegios con autorización mediante PIM. PIM garantiza que las cuentas con privilegios no tengan privilegios administrativos permanentes y que todas las solicitudes de acceso con privilegios sean solo durante el tiempo necesario para realizar las tareas administrativas. PIM también proporciona registro de todas las solicitudes y activaciones para fines de auditoría. |
|Administración de identidades y acceso|Una vez que la cuenta con privilegios se ha identificado y activado de forma segura, se proporciona al administrador acceso a las suscripciones de Azure y a los recursos para los que se les han asignado permisos mediante la administración de identidades y acceso.|

Una vez que la cuenta con privilegios ha completado los pasos para obtener acceso administrativo a Azure Portal, se puede configurar el acceso a las cargas de trabajo y se pueden establecer conexiones administrativas.

### <a name="azure-workload-administration"></a>Administración de cargas de trabajo de Azure

|Paso |Descripción|
|---|---|
|Acceso Just-In-Time (JIT)|Para acceder a las máquinas virtuales, el administrador usa JIT para solicitar acceso al Protocolo de escritorio remoto en el servidor de salto desde la dirección IP de puerta de enlace de escritorio remoto y RDP o SSH desde el servidor de salto a las máquinas virtuales con la carga de trabajo correspondiente.|
|Azure VPN Gateway|Ahora el administrador establece una conexión VPN de punto a sitio con IPSec desde su estación de trabajo con privilegios a Azure VPN Gateway, que realiza la autenticación de certificado para establecer la conexión.|
|Puerta de enlace de Escritorio remoto|Ahora el administrador intenta una conexión mediante el Protocolo de escritorio remoto al servidor de salto con la puerta de enlace de Escritorio remoto especificada en la configuración de Conexión a Escritorio remoto. La puerta de enlace de Escritorio remoto tiene una dirección IP privada a la que se puede acceder mediante la conexión de Azure VPN Gateway. Las directivas de la puerta de enlace de Escritorio remoto controlan si la cuenta con privilegios está autorizada para acceder al servidor de salto solicitado. La puerta de enlace de Escritorio remoto solicita al administrador las credenciales y reenvía la solicitud de autenticación al servidor de directivas de redes (NPS).|
|Servidor de directivas de redes (NPS)|El servidor de directivas de redes recibe la solicitud de autenticación de la puerta de enlace de Escritorio remoto y valida el nombre de usuario y la contraseña en Active Directory antes de enviar una solicitud a Azure Active Directory para desencadenar una solicitud de autenticación de Azure AD MFA.|
|Azure AD MFA|Azure AD MFA envía una solicitud de autenticación al dispositivo móvil registrado de la cuenta con privilegios. Intune administra el dispositivo móvil para garantizar el cumplimiento de los requisitos de seguridad. El administrador debe autenticarse primero en el dispositivo móvil y, a continuación, en la aplicación Microsoft Authenticator con un PIN o sistema biométrico para que el intento de autenticación se autorice en Azure AD MFA.|
|Servidor de salto|Una vez autenticado correctamente, la conexión RDP se cifra mediante seguridad de la capa de transporte (TLS) y se envía a través del túnel IPSec cifrado a Azure VPN Gateway mediante la puerta de enlace de Escritorio remoto al servidor de salto. Desde el servidor de salto el administrador puede ahora conectarse mediante RDP o SSH a las máquinas virtuales de carga de trabajo tal y como se especifica en la solicitud JIT.|

## <a name="general-guidance"></a>Instrucciones generales

Al implementar los componentes que se enumeran en este artículo, se aplican las siguientes directrices generales:

* Valide la disponibilidad de la región de los servicios, asegurándose de que todos los datos permanezcan en las ubicaciones autorizadas e implemente en el Centro de Australia o el Centro de Australia 2 como primera preferencia para las cargas de trabajo PROTEGIDAS.

* Consulte la publicación *Azure - ACSC Certification Report – Protected 2018* (Azure: informe de certificación de ACSC de protección de 2018) para conocer el estado de certificación de servicios individuales y realizar sus propias valoraciones sobre cualquiera de los componentes pertinentes que no se incluyan en el informe de conformidad con *ACSC CONSUMER GUIDE – Microsoft Azure at PROTECTED* (GUÍA DEL CONSUMIDOR DE ACSC: Microsoft Azure en PROTEGIDO)

* Garantice la conectividad de red y cualquier configuración de proxy necesaria para el acceso a los componentes de autenticación necesarios, como Azure AD, ADFS y PTA

* Use Azure Policy para supervisar y aplicar el cumplimiento de los requisitos.

* Asegúrese de que las máquinas virtuales, especialmente los controladores de dominio de Active Directory, se almacenan en cuentas de almacenamiento cifradas y utilizan Azure Disk Encryption

* Cree y mantenga procesos de gobernanza y administración de privilegios de administración y de identidad seguros para respaldar los controles técnicos que se enumeran en este artículo.

|Resource|URL|
|---|---|
|Documentos de cumplimiento normativo y de las directivas de Australia|[Documentos de cumplimiento normativo y de las directivas de Australia](https://aka.ms/au-irap)|
|Productos de Azure: por regiones de Australia e independientes de la región|[Productos de Azure: por regiones de Australia e independientes de la región](https://azure.microsoft.com/global-infrastructure/services/?regions=non-regional,australia-central,australia-central-2,australia-east,australia-southeast)|
|Estrategias para mitigar incidentes de ciberseguridad|[Estrategias para mitigar incidentes de ciberseguridad](https://acsc.gov.au/infosec/mitigationstrategies.htm)|
|ACSC Protect: Secure Administration|[ACSC Protect: Secure Administration](https://acsc.gov.au/publications/protect/secure-administration.htm)|
|Procedimiento: Integración de la infraestructura de la puerta de enlace de Escritorio remoto utilizando la extensión Servidor de directivas de redes (NPS) y Azure AD|[Integración de puerta de enlace de Escritorio remoto con el servidor de directivas de redes y Azure AD](../active-directory/authentication/howto-mfa-nps-extension-rdg.md)|

## <a name="component-guidance"></a>Guía de componentes

En esta sección se proporciona información sobre el propósito de cada componente y su rol en la arquitectura de administración remota segura global. Se proporcionan vínculos adicionales de acceso a recursos útiles, como documentación de referencia, guías y tutoriales.

## <a name="secure-devices"></a>Protección de dispositivos

Los dispositivos físicos que emplean los usuarios con privilegios para realizar funciones administrativas son objetivos valiosos para actores malintencionados. Mantener la seguridad y la integridad de los dispositivos físicos y asegurarse de que están libres del software malintencionado y de protegerlos del peligro es una parte clave al proporcionar una funcionalidad de administración remota segura. Esto implica una configuración de seguridad de alta prioridad, tal y como se especifica en las ocho estrategias esenciales de ACSC para mitigar los incidentes de ciberseguridad, por ejemplo, el filtrado de aplicaciones, las aplicaciones de aplicación de revisión, la protección de aplicaciones y los sistemas operativos de aplicación de revisión. Es obligatorio instalar, configurar, auditar y validar estas funcionalidades, así como informar de ellas, para garantizar que el estado de un dispositivo cumple los requisitos de la organización.

### <a name="privileged-workstation"></a>Estación de trabajo con privilegios

La estación de trabajo con privilegios es una máquina protegida que se puede usar para realizar tareas administrativas y solo es accesible para las cuentas administrativas. La estación de trabajo con privilegios debe contar con directivas y configuración para limitar el software que se puede ejecutar, su acceso a los recursos de red y de Internet; además, las credenciales deben estar protegidas frente al robo o la vulnerabilidad del dispositivo.|

|Recursos|Vínculo|
|---|---|
|Introducción a la arquitectura de las estaciones de trabajo con acceso con privilegios|[https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model/](/security/compass/privileged-access-deployment)|
|Protección del material de referencia de acceso con privilegios|[https://docs.microsoft.com/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material](/windows-server/identity/securing-privileged-access/securing-privileged-access-reference-material)|

### <a name="mobile-device"></a>Dispositivo móvil

Los dispositivos móviles tienen mayor riesgo de pérdida accidental o robo debido a su portabilidad y tamaño, y deben protegerse adecuadamente. Los dispositivos móviles proporcionan un factor adicional seguro para la autenticación, dada su capacidad de aplicar la autenticación para el acceso a ellos, su rastreabilidad mediante los servicios de ubicación, las funciones de cifrado y la capacidad de borrado de forma remota. Al usar un dispositivo móvil como factor de autenticación adicional para Azure, este debe configurarse para usar la aplicación Microsoft Authenticator con autenticación biométrica o de PIN, y no mediante llamadas de teléfono o mensajes de texto.

|Recursos|Vínculo|
|---|---|
|Métodos de autenticación de Azure AD|[https://docs.microsoft.com/azure/active-directory/authentication/concept-authentication-methods](../active-directory/authentication/concept-authentication-methods.md)|
|Uso de la aplicación Microsoft Authenticator|[https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app](https://support.microsoft.com/help/4026727/microsoft-account-how-to-use-the-microsoft-authenticator-app)|

### <a name="microsoft-intune"></a>Microsoft Intune

Intune es el componente de Enterprise Mobility + Security que administra los dispositivos móviles y las aplicaciones. Se integra estrechamente con otros componentes, como Azure Active Directory para el control de acceso y de identidad y Azure Information Protection para la protección de datos. Intune proporciona directivas para las estaciones de trabajo y los dispositivos móviles con el fin de establecer los requisitos de cumplimiento de acceso a los recursos y funcionalidades de informes y auditoría para obtener información sobre el estado de los dispositivos administrativos.

|Recursos|Vínculo|
|---|---|
|Documentación de Microsoft Intune|[https://docs.microsoft.com/intune/](/intune/)|
|Introducción al cumplimiento de los dispositivos en Intune|[https://docs.microsoft.com/intune/device-compliance-get-started](/intune/device-compliance-get-started)|

### <a name="group-policy"></a>Directiva de grupo

Directiva de grupo se utiliza para controlar la configuración de los sistemas operativos y las aplicaciones. Las directivas de seguridad controlan la autenticación, la autorización y la configuración de auditoría de un sistema. Se usa para reforzar la estación de trabajo con privilegios, proteger las credenciales administrativas y limitar el acceso de las cuentas sin privilegios a los dispositivos con privilegios.

|Recursos|Vínculo|
|---|---|
|Opción Permitir el inicio de sesión local de Directiva de grupo|[https://docs.microsoft.com/windows/security/threat-protection/security-policy-settings/allow-log-on-locally](/windows/security/threat-protection/security-policy-settings/allow-log-on-locally)|

### <a name="jump-server--bastion-host"></a>Servidor/host de salto

El servidor/host de salto es un punto centralizado para la administración. Tiene las herramientas necesarias para realizar tareas administrativas, pero también, el acceso a la red necesario para conectarse a los recursos de los puertos administrativos. El servidor de salto es el punto central para la administración de las cargas de trabajo de máquina virtual de este artículo, pero también se puede configurar como punto autorizado para administrar funcionalidades de plataforma como servicio (PaaS), como SQL. El acceso a las funcionalidades de PaaS se puede restringir por servicio mediante controles de identidad y de red.

|Recursos|Vínculo|
|---|---|
|Implementación de hosts administrativos seguros|[https://docs.microsoft.com/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts](/windows-server/identity/ad-ds/plan/security-best-practices/implementing-secure-administrative-hosts)|

### <a name="just-in-time-jit-access"></a>Acceso Just-In-Time (JIT)

JIT es una funcionalidad de Azure Security Center que utiliza grupos de seguridad de red (NSG) para bloquear el acceso a protocolos administrativos como RDP y SSH en las máquinas virtuales. Las aplicaciones hospedadas en máquinas virtuales continúan funcionando normalmente, pero para obtener acceso administrativo se debe solicitar solo para un tiempo establecido. Todas las solicitudes se registran con fines de auditoría.

|Recursos |Vínculo |
|---|---|
|Administración del acceso Just-In-Time (JIT)|[https://docs.microsoft.com/azure/security-center/security-center-just-in-time](../security-center/security-center-just-in-time.md)|
|Automatización del acceso Just-In-Time de Azure a las máquinas virtuales|[https://blogs.technet.microsoft.com/motiba/2018/06/24/automating-azure-just-in-time-vm-access](/archive/blogs/motiba/automating-azure-just-in-time-vm-access)|

## <a name="secure-communication"></a>Comunicación segura

El tráfico de comunicaciones de las actividades de administración puede contener información altamente confidencial, como credenciales administrativas, y debe administrarse y protegerse en consecuencia. Proporcionar una comunicación segura implica funcionalidades de cifrado confiables para evitar que se intercepte y se segmente la red y restricciones que limiten el tráfico administrativo a los puntos de conexión autorizados y controlen el movimiento lateral si un sistema está en peligro.

### <a name="azure-portal"></a>Azure portal

Las comunicaciones con Azure Portal se cifran mediante la seguridad de la capa de transporte (TLS) y el uso de Azure Portal ha sido certificado por ACSC. Las entidades de la Commonwealth deben seguir las recomendaciones de *ACSC Consumer Guide* (Guía del consumidor de ACSC) y configurar sus exploradores web para asegurarse de que usan la versión más reciente de TLS con algoritmos criptográficos compatibles.

|Recursos |Vínculo |
|---|---|
|Introducción al cifrado de Azure: cifrado en tránsito|[https://docs.microsoft.com/azure/security/security-azure-encryption-overview#encryption-of-data-in-transit](../security/fundamentals/encryption-overview.md#encryption-of-data-in-transit)|

### <a name="azure-vpn-gateway"></a>Azure VPN Gateway

Azure VPN Gateway proporciona la conexión cifrada segura desde la estación de trabajo con privilegios a Azure. Azure VPN Gateway ha recibido la certificación de ACSC para proporcionar comunicación IPSec segura. Las entidades de la Commonwealth deben configurar Azure VPN Gateway de acuerdo con la guía del consumidor de ACSC, el informe de certificación de ACSC y otras instrucciones específicas.

|Recursos |Vínculo |
|---|---|
|Acerca de las conexiones de punto a sitio|[https://docs.microsoft.com/azure/vpn-gateway/point-to-site-about](../vpn-gateway/point-to-site-about.md)|
|Detalles criptográficos de Azure VPN Gateway|[https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-compliance-crypto](../vpn-gateway/vpn-gateway-about-compliance-crypto.md)|
|Configuración de Azure VPN Gateway|[Configuración de Azure VPN Gateway](vpn-gateway.md)|

### <a name="remote-desktop-rd-gateway"></a>Puerta de enlace de Escritorio remoto (RD)

La puerta de enlace de Escritorio remoto es un mecanismo seguro para controlar y autorizar las conexiones RDP a los sistemas. Encapsula el tráfico RDP en el protocolo seguro de transferencia de hipertexto (HTTPS) y lo cifra mediante TLS. TLS proporciona una capa adicional de seguridad para el tráfico administrativo.

|Recursos |Vínculo |
|---|---|
|Arquitectura de los servicios de Escritorio remoto|[https://docs.microsoft.com/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture](/windows-server/remote/remote-desktop-services/desktop-hosting-logical-architecture)|

### <a name="network-security-groups-nsgs"></a>Grupos de seguridad de red (NSG)

Los grupos de seguridad de red funcionan como listas de control de acceso (ACL) para el tráfico que entra en las subredes o máquinas virtuales o sale de estas. Segmentan la red y proporcionan un mecanismo para controlar y limitar los flujos de comunicaciones permitidos entre los sistemas. Son un componente básico de la administración Just-in-Time (JIT) para permitir o denegar el acceso a los protocolos administrativos.

|Recursos |Vínculo |
|---|---|
|Introducción a los grupos de seguridad de Azure|[https://docs.microsoft.com/azure/virtual-network/security-overview](../virtual-network/network-security-groups-overview.md)|
|Procedimiento: Planeamiento de redes virtuales|[https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm](../virtual-network/virtual-network-vnet-plan-design-arm.md)|

## <a name="strong-authentication"></a>Autenticación sólida

Identificar de forma segura a los usuarios con privilegios antes de concederles acceso a los sistemas es un componente fundamental de la administración segura. Debe haber mecanismos instaurados para proteger las credenciales asociadas a las cuentas con privilegios y evitar que los actores malintencionados consigan acceder a los sistemas mediante suplantación o robo de credenciales.

### <a name="domain-controller-dc"></a>Controlador de dominio (DC)

En general, un controlador de dominio hospeda una copia de la base de datos de Active Directory, que contiene todos los usuarios, equipos y grupos del dominio. Los controladores de dominio realizan la autenticación de usuarios y equipos. Los controladores de dominio de esta arquitectura se hospedan como máquinas virtuales en Azure y proporcionan servicios de autenticación para las cuentas con privilegios que se conectan a servidores de salto y máquinas virtuales de carga de trabajo.

|Recursos |Vínculo |
|---|---|
|Introducción a Active Directory Domain Services|[https://docs.microsoft.com/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview)|

### <a name="azure-active-directory-azure-ad"></a>Azure Active Directory (Azure AD)

Azure AD es el servicio de autenticación para Azure. Contiene las identidades

en la nube y proporciona autenticación y autorización para un entorno de Azure. Azure se puede sincronizar con Active Directory mediante Azure AD Connect y para proporcionar autenticación federada con los Servicios de federación de Active Directory (AD FS) y Azure AD Connect. Azure AD es un componente básico de la administración segura.

|Recursos |Vínculo |
|---|---|
|Documentación de Azure Active Directory|[https://docs.microsoft.com/azure/active-directory](../active-directory/index.yml)|
|Documentación de la identidad híbrida|[https://docs.microsoft.com/azure/active-directory/hybrid](../active-directory/hybrid/index.yml)|

### <a name="network-policy-server-nps"></a>Servidor de directivas de redes (NPS)

Un servidor de directivas de redes es un servidor de autenticación y de directivas que proporciona procesos de autenticación y autorización avanzados. En esta arquitectura se proporciona para integrar la autenticación de Azure AD MFA con las solicitudes de autenticación de puerta de enlace de Escritorio remoto. Tiene un complemento específico para admitir la integración con Azure AD MFA en Azure AD.

|Recursos |Vínculo |
|---|---|
|Documentación del servidor de directivas de redes|[https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top](/windows-server/networking/technologies/nps/nps-top)|

### <a name="azure-ad-mfa"></a>Azure AD MFA

Azure AD MFA es un servicio de autenticación proporcionado en Azure Active Directory para habilitar otras solicitudes de autenticación distintas del nombre de usuario y la contraseña para acceder a recursos en la nube como Azure Portal. Azure AD MFA admite distintos métodos de autenticación. Esta arquitectura utiliza la aplicación Microsoft Authenticator para mejorar la seguridad y la integración con el servidor de directivas de redes.

|Recursos |Vínculo |
|---|---|
|Funcionamiento: Azure AD Multi-Factor Authentication|[https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks](../active-directory/authentication/concept-mfa-howitworks.md)|
|Procedimiento: Implementación de Azure AD Multi-factor Authentication en la nube|[https://docs.microsoft.com/azure/active-directory/authentication/howto-mfa-getstarted](../active-directory/authentication/howto-mfa-getstarted.md)|

## <a name="strong-authorisation"></a>Autorización segura

Una vez identificada la cuenta con privilegios de forma segura, se le puede conceder acceso a los recursos. La autorización controla y administra los privilegios asignados a una cuenta específica. Los procesos de autorización seguros se alinean con las ocho estrategias esenciales de ACSC para mitigar los incidentes de ciberseguridad relativos a la restricción de privilegios administrativos.

### <a name="identity-and-access-management"></a>Administración de identidades y acceso

El acceso para realizar acciones con privilegios en Azure se basa en los roles que se asignan a esa cuenta. Azure incluye una amplia gama de roles con permisos específicos para llevar a cabo tareas específicas. Estos roles se pueden conceder a distintos niveles, como de suscripción o de grupo de recursos. La asignación de roles y la administración de permisos se basan en las cuentas y los grupos en Azure Active Directory y se administran mediante control de acceso (IAM) en Azure.

|Recursos |Vínculo |
|---|---|
|Control de acceso basado en roles de Azure (RBAC de Azure)|[https://docs.microsoft.com/azure/role-based-access-control](../role-based-access-control/index.yml)|
|Definiciones de los roles|[https://docs.microsoft.com/azure/role-based-access-control/role-definitions](../role-based-access-control/role-definitions.md)|

### <a name="privileged-identity-management-pim"></a>Privileged Identity Management (PIM)

PIM es un componente de Azure Active Directory que controla el acceso de los roles con privilegios. Las cuentas con privilegios no requieren acceso con privilegios permanente o duradero, sino que se les puede conceder la capacidad de solicitar acceso con privilegios durante un tiempo determinado para realizar actividades con privilegios. PIM proporciona controles adicionales sobre el mantenimiento y la restricción del acceso con privilegios, así como sobre el registro y la auditoría para realizar el seguimiento de las instancias de uso con privilegios.

|Recursos |Vínculo |
|---|---|
|Documentación sobre Privileged Identity Management (PIM)|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management](../active-directory/privileged-identity-management/index.yml)|
|Primer uso de PIM|[https://docs.microsoft.com/azure/active-directory/privileged-identity-management/pim-getting-started](../active-directory/privileged-identity-management/pim-getting-started.md)|

### <a name="conditional-access"></a>Acceso condicional

El acceso condicional es un componente de Azure Active Directory que permite o deniega el acceso a los recursos en función de ciertas condiciones. Estas condiciones pueden ser la ubicación de red, el tipo de dispositivo, el estado de cumplimiento, la pertenencia a grupos y mucho más. El acceso condicional se usa para aplicar MFA, la administración de dispositivos y el cumplimiento mediante Intune y la pertenencia a grupos de las cuentas administrativas.

|Recursos |Vínculo |
|---|---|
|Documentación sobre el acceso condicional|[https://docs.microsoft.com/azure/active-directory/conditional-access](../active-directory/conditional-access/index.yml)|
|Procedimiento: Uso obligatorio de dispositivos administrados para el acceso a aplicaciones en la nube mediante el acceso condicional|[https://docs.microsoft.com/azure/active-directory/conditional-access/require-managed-devices](../active-directory/conditional-access/require-managed-devices.md)|

## <a name="next-steps"></a>Pasos siguientes

Revise el artículo sobre [la administración y el control del tráfico de entrada de puerta de enlace](gateway-ingress-traffic.md) para más información sobre el control de los flujos de tráfico mediante los componentes de la puerta de enlace en Azure.
