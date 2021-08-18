---
title: Acceso híbrido seguro de Azure AD con F5 | Microsoft Docs
description: Integración del administrador de directivas de acceso de F5 BIG-IP y Azure Active Directory para el acceso híbrido seguro
services: active-directory
author: davidmu1
manager: martinco
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 11/12/2020
ms.author: davidmu
ms.collection: M365-identity-device-management
ms.reviewer: miccohen
ms.openlocfilehash: cc55eff7c5ed3a4773a016bcd73bbd33708d9595
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121738781"
---
# <a name="f5-big-ip-access-policy-manager-and-azure-active-directory-integration-for-secure-hybrid-access"></a>Integración del administrador de directivas de acceso de F5 BIG-IP y Azure Active Directory para el acceso híbrido seguro

La proliferación de la movilidad y la constante evolución del panorama de amenazas está haciendo aumentar la vigilancia sobre el acceso a los recursos y la gobernanza, lo cual ha situado a [Confianza cero](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/) al frente y en el centro de todos los programas de modernización.
En Microsoft y F5, sabemos que esta transformación digital implica normalmente un recorrido de varios años para cualquier empresa, lo que puede significar que algunos recursos críticos queden expuestos hasta su modernización. La motivación detrás del acceso híbrido seguro de F5 BIG-IP y Azure Active Directory no solo pretende mejorar el acceso remoto a las aplicaciones locales, sino también reforzar la posición general de seguridad de estos servicios vulnerables.

A modo de contexto, la investigación estima que el 60-80 % de las aplicaciones locales son heredadas por naturaleza o, en otras palabras, que no se pueden integrar directamente con Azure Active Directory (AD). En el mismo estudio también se indica que una gran proporción de estos sistemas se ejecuta en versiones de nivel inferior de SAP, Oracle, SAGE y otras cargas de trabajo conocidas que proporcionan servicios críticos.

El acceso híbrido seguro se encarga de remediar este "punto débil", ya que permite que las organizaciones sigan usando sus inversiones en F5 para la entrega de aplicaciones y redes de nivel superior. En combinación con Azure AD permite enlazar el heterogéneo panorama de aplicaciones con el moderno plano de control de identidades.

Hacer que Azure AD realice una autenticación previa para el acceso a los servicios publicados de BIG-IP proporciona muchas ventajas:

- Autenticación sin contraseña mediante [Windows Hello](/windows/security/identity-protection/hello-for-business/hello-overview), [MS Authenticator](../user-help/user-help-auth-app-download-install.md), [Claves de Fast Identity Online (FIDO)](../authentication/howto-authentication-passwordless-security-key.md) y [autenticación basada en certificados](../authentication/active-directory-certificate-based-authentication-get-started.md).

- [Acceso condicional](../conditional-access/overview.md) preventivo y [autenticación multifactor (MFA)](../authentication/concept-mfa-howitworks.md).

- [Protección de identidades](../identity-protection/overview-identity-protection.md): control adaptable mediante la generación de perfiles de riesgo de usuario y sesión.

- [Detección de credenciales filtradas](../identity-protection/concept-identity-protection-risks.md).

- [Autoservicio de restablecimiento de contraseña (SSPR)](../authentication/tutorial-enable-sspr.md)

- [Colaboración de asociados](../governance/entitlement-management-external-users.md): administración de derechos para el acceso controlado de invitados.

- [Cloud App Security (CASB)](/cloud-app-security/what-is-cloud-app-security): para un control y detección de aplicaciones completo.

- Supervisión de amenazas: [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) para el análisis avanzado de amenazas.

- El [portal de Azure AD](https://azure.microsoft.com/features/azure-portal/): un plano de control único para gobernar la identidad y el acceso.

## <a name="scenario-description"></a>Descripción del escenario

Como controlador de entrega de aplicaciones (ADC) y SSL-VPN, un sistema de BIG-IP proporciona acceso local y remoto a todos los tipos de servicios, entre los que se incluyen:

- Aplicaciones web modernas y heredadas

- Aplicaciones no basadas en web

- Servicios REST y SOAP Web API

Local Traffic Manager (LTM) permite la publicación segura de servicios mediante la funcionalidad de proxy inverso. Al mismo tiempo, un sofisticado administrador de directivas de acceso (APM) amplía la instancia de BIG-IP con un conjunto de funcionalidades enriquecidas, lo que permite la federación y el inicio de sesión único (SSO).

La integración se basa en una confianza de federación estándar entre el APM y Azure AD que es común para la mayoría de los casos de uso de acceso híbrido seguro que incluye el [escenario SSL-VPN](f5-aad-password-less-vpn.md). Los recursos de Lenguaje de marcado de aserción de seguridad (SAML), OAuth y Open ID Connect (OIDC) no son una excepción, ya que también estos se pueden proteger para el acceso remoto. También podría haber escenarios en los que una instancia de BIG-IP se convierta en un cuello de botella para el acceso de Confianza cero a todos los servicios, incluidas las aplicaciones SaaS.

Una ventaja de BIG-IP para su integración con Azure AD es la que permite la transición de protocolos necesaria para proteger los servicios heredados o no integrados de Azure AD con controles modernos como la [autenticación sin contraseña](https://www.microsoft.com/security/business/identity/passwordless) y el [acceso condicional](../conditional-access/overview.md). En este escenario, una instancia de BIG-IP sigue cumpliendo su rol como proxy inverso, al tiempo que deja la autenticación previa y la autorización a Azure AD, que las realiza servicio a servicio.

Los pasos 1-4 del diagrama ilustran el intercambio de autenticación previa de front-end entre un usuario, una instancia de BIG-IP y Azure AD, en un flujo iniciado por el proveedor de servicios. Los pasos 5-6 muestran el enriquecimiento de la sesión de APM subsiguiente y el inicio de sesión único en los servicios back-end individuales.

![En la imagen se muestra la arquitectura de alto nivel.](./media/f5-aad-integration/integration-flow-diagram.png)

| Paso | Descripción |
|:------|:-----------|
| 1. | El usuario selecciona un icono de aplicación en el portal y resuelve la dirección URL en el proveedor de servicios de SAML (BIG-IP). |
| 2. | BIG-IP redirige al usuario a IDP de SAML (Azure AD) para la autenticación previa.|
| 3. | Azure AD procesa directivas de acceso condicional y [controles de sesión](../conditional-access/concept-conditional-access-session.md) para la autorización.|
| 4. | El usuario se redirige de nuevo a BIG-IP presentando las notificaciones de SAML que emite Azure AD. |
| 5. | BIG-IP solicita cualquier información de sesión adicional para incluirla en el [inicio de sesión único](../hybrid/how-to-connect-sso.md) y el [control de acceso basado en rol (RBAC)](../../role-based-access-control/overview.md) del servicio publicado. |
| 6. | BIG-IP reenvía la solicitud del cliente al servicio back-end.

## <a name="user-experience"></a>Experiencia del usuario

Tanto si se trata de un empleado directo, un afiliado o un consumidor, la mayoría de los usuarios ya están familiarizados con la experiencia de inicio de sesión de Office 365, por lo que el acceso a los servicios de BIG-IP a través del acceso híbrido seguro resulta muy familiar.

Ahora, los usuarios encuentran sus servicios publicados de BIG-IP consolidados en [MyApps](../user-help/my-apps-portal-end-user-access.md) o las [plataformas de lanzamiento de O365](https://o365pp.blob.core.windows.net/media/Resources/Microsoft%20365%20Business/Launchpad%20Overview_for%20Partners_10292019.pdf) junto con las funcionalidades de autoservicio para un conjunto más amplio de servicios, independientemente del tipo de dispositivo o ubicación. Los usuarios pueden incluso seguir accediendo a los servicios publicados directamente a través del portal de webtops propiedad de BIG-IP, si lo prefiere. Al cerrar la sesión, el acceso híbrido seguro garantiza que la sesión de los usuarios finaliza en ambos extremos, BIG-IP y Azure AD, lo cual garantiza que los servicios permanecen completamente protegidos frente a accesos no autorizados.  

Las capturas de pantallas que se proporcionan proceden del portal de aplicaciones de Azure AD al que los usuarios pueden acceder de forma segura para encontrar sus servicios publicados de BIG-IP y administrar las propiedades de sus cuentas.  

![La captura de pantalla muestra la galería MyApps de Woodgrove](media/f5-aad-integration/woodgrove-app-gallery.png)

![La captura de pantalla muestra la página de autoservicio MyAccounts de Woodgrove](media/f5-aad-integration/woodgrove-myaccount.png)

## <a name="insights-and-analytics"></a>Información y análisis

El rol de BIG-IP es fundamental para cualquier empresa, por lo que se deben supervisar las instancias de BIG-IP implementadas para garantizar que los servicios publicados tengan una alta disponibilidad, tanto en un nivel de acceso híbrido seguro como también en el nivel operativo.

Existen varias opciones para registrar eventos, ya sea de forma local o remota, mediante una solución de administración de eventos e información de seguridad (SIEM) que permite el almacenamiento y el procesamiento de datos de telemetría. Una solución muy eficaz para la supervisión de Azure AD y de actividades específicas del acceso híbrido seguro es usar [Azure Monitor](../../azure-monitor/overview.md) y [Azure Sentinel](../../sentinel/overview.md) que ofrecen lo siguiente:

- Información general detallada de su organización, posiblemente en varias nubes, y en ubicaciones locales, como la infraestructura de BIG-IP.

- Plano de control único que proporciona una vista combinada de todas las señales, evitando la dependencia de herramientas complejas y dispares.

![En la imagen se muestra el flujo de supervisión.](media/f5-aad-integration/azure-sentinel.png)

## <a name="prerequisites"></a>Requisitos previos

La integración de F5 BIG-IP con Azure AD para el acceso híbrido seguro tiene los siguientes requisitos previos:

- Una instancia de F5 BIG-IP que se ejecute en cualquiera de las siguientes plataformas:

  - Dispositivo físico

  - Edición virtual de un hipervisor, como Microsoft Hyper-V, VMware ESXi, Linux KVM y Citrix Hypervisor

  - Edición virtual de la nube, como Azure, VMware, KVM, Community Xen, MS Hyper-V, AWS, OpenStack y Google Cloud

    La ubicación real de una instancia de BIG-IP puede ser local o cualquier plataforma en la nube compatible, incluida Azure, siempre que tenga conectividad a Internet, recursos que se publican y otros servicios necesarios, como Active Directory.  

- Una licencia de F5 BIG-IP APM, a través de una de las siguientes opciones:

  - F5 BIG-IP® Best bundle (o)

    - Licencia independiente de F5 BIG-IP Access Policy Manager™ (APM).

    - Licencia del complemento F5 BIG-IP Access Policy Manager™ (APM) en una instalación de F5 BIG-IP® Local Traffic Manager™ (LTM) ya existente.

    - Una [licencia de evaluación gratuita](https://www.f5.com/trial/big-ip-trial.php) de 90 días de F5 BIG-IP Access Policy Manager™ (APM).

- Licencias de Azure AD mediante cualquiera de las siguientes opciones:

  - Una [suscripción gratuita](/windows/client-management/mdm/register-your-free-azure-active-directory-subscription#:~:text=%20Register%20your%20free%20Azure%20Active%20Directory%20subscription,will%20take%20you%20to%20the%20Azure...%20More%20) de Azure AD proporciona los requisitos básicos mínimos para implementar el acceso híbrido seguro con autenticación sin contraseña.

  - Una [suscripción Premium](https://www.microsoft.com/security/business/identity-access-management/azure-ad-pricing) proporciona todo el valor adicional que se indica en el prefacio, incluido el [acceso condicional](../conditional-access/overview.md), la [autenticación multifactor](../authentication/concept-mfa-howitworks.md) y la [protección de identidades](../identity-protection/overview-identity-protection.md).

No se necesita experiencia ni conocimientos previos de F5 BIG-IP para implementar el acceso híbrido seguro, aunque es recomendable que se familiarice con la terminología de F5 BIG-IP. La [knowledge base](https://www.f5.com/services/resources/glossary) enriquecida de F5 también es un buen punto de partida para empezar a conocer BIG-IP.

## <a name="deployment-scenarios"></a>Escenarios de implementación

En los siguientes tutoriales se proporcionan instrucciones detalladas sobre la implementación de algunos de los patrones más comunes para el acceso híbrido seguro con BIG-IP y Azure AD:

- [Tutorial de implementación de F5 BIG-IP en Azure](f5-bigip-deployment-guide.md)

- [Inicio de sesión único de F5 BIG-IP APM y Azure AD para aplicaciones de Kerberos](../saas-apps/kerbf5-tutorial.md#configure-f5-single-sign-on-for-kerberos-application)

- [Inicio de sesión único de F5 BIG-IP APM y Azure AD para aplicaciones basadas en encabezados](../saas-apps/headerf5-tutorial.md#configure-f5-single-sign-on-for-header-based-application)

- [Protección de F5 BIG-IP SSL-VPN con el acceso híbrido seguro de Azure AD](f5-aad-password-less-vpn.md)

## <a name="additional-resources"></a>Recursos adicionales

- [El fin de las contraseñas, cambie al modo sin contraseña](https://www.microsoft.com/security/business/identity/passwordless)

- [Acceso híbrido seguro de Azure Active Directory](https://azure.microsoft.com//services/active-directory/sso/secure-hybrid-access/)

- [Marco de confianza cero de Microsoft para habilitar el trabajo remoto](https://www.microsoft.com/security/blog/2020/04/02/announcing-microsoft-zero-trust-assessment-tool/)

- [Introducción a Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/?&OCID=AID2100131_SEM_XfknpgAAAHoVMTvh:20200922160358:s&msclkid=5e0e022409fc1c94dab85d4e6f4710e3&ef_id=XfknpgAAAHoVMTvh:20200922160358:s&dclid=CJnX6vHU_esCFUq-ZAod1iQF6A)

## <a name="next-steps"></a>Pasos siguientes

Considere la posibilidad de ejecutar una prueba de concepto del acceso híbrido seguro con la infraestructura de BIG-IP existente o mediante la implementación de una instancia de prueba. La [implementación de una máquina virtual de BIG-IP Virtual Edition (VE) en Azure](f5-bigip-deployment-guide.md) tarda aproximadamente 30 minutos. Cuando esta acabe, dispondrá de:

- Una plataforma totalmente segura para modelar una prueba de concepto de acceso híbrido seguro

- Una instancia de preproducción, una plataforma totalmente segura que se usará para probar nuevas revisiones y actualizaciones del sistema de BIG-IP.

Al mismo tiempo, debe identificar una o dos aplicaciones que se puedan usar como destino para la publicación a través de BIG-IP y que se puedan proteger con el acceso híbrido seguro.  

Nuestra recomendación es comenzar con una aplicación que aún no se ha publicado mediante una instancia de BIG-IP para evitar posibles interrupciones en los servicios de producción. Las instrucciones que se mencionan en este artículo le ayudarán a familiarizarse con el procedimiento general para crear los distintos objetos de configuración de BIG-IP y configurar el acceso híbrido seguro. Una vez que haya finalizado, debería poder hacer lo mismo con cualquier otro servicio nuevo, además de tener suficientes conocimientos para convertir los servicios publicados de BIG-IP existentes en acceso híbrido seguro con un esfuerzo mínimo.

En la guía interactiva siguiente se describe el procedimiento de alto nivel para implementar el acceso híbrido seguro y ver la experiencia del usuario final.

[![La imagen muestra la portada de una guía interactiva](media/f5-aad-integration/interactive-guide.png)](https://aka.ms/Secure-Hybrid-Access-F5-Interactive-Guide)
