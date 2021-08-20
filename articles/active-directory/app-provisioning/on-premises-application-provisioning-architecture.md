---
title: Arquitectura de aprovisionamiento de aplicaciones locales de Azure AD | Microsoft Docs
description: Se presenta información general de la arquitectura de aprovisionamiento de aplicaciones locales.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: overview
ms.date: 05/28/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: d996ebf8641b4b2829ecbe7cb70ed50280f89391
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451577"
---
# <a name="azure-ad-on-premises-application-provisioning-architecture"></a>Arquitectura de aprovisionamiento de aplicaciones locales de Azure AD

>[!IMPORTANT]
> La versión preliminar de aprovisionamiento locales se encuentra actualmente en una versión preliminar solo por invitación. Para solicitar acceso a la funcionalidad, use el [formulario de solicitud de acceso](https://aka.ms/onpremprovisioningpublicpreviewaccess). La versión preliminar estará a disposición de más clientes y conectores durante los próximos meses, cuando se prepare la disponibilidad general (GA).

## <a name="overview"></a>Información general

En el diagrama siguiente se muestra información general de cómo funciona el aprovisionamiento de aplicaciones locales.

![Diagrama en el que se muestra la arquitectura para el aprovisionamiento de aplicaciones locales.](.\media\on-premises-application-provisioning-architecture\arch-3.png)

Existen tres componentes principales para el aprovisionamiento de usuarios en una aplicación local:

- El agente de aprovisionamiento proporciona conectividad entre Azure Active Directory (Azure AD) y el entorno local.
- El host ECMA convierte las solicitudes de aprovisionamiento de Azure AD en solicitudes realizadas a la aplicación de destino. Actúa como puerta de enlace entre Azure AD y la aplicación. Se puede usar para importar los conectores ECMA2 existentes que se utilizan con Microsoft Identity Manager. El host ECMA no es necesario si ha creado una aplicación SCIM o una puerta de enlace SCIM.
- El servicio de aprovisionamiento de Azure AD actúa como motor de sincronización.

>[!NOTE]
> La sincronización de Microsoft Identity Manager no es necesaria. Pero puede usarla para crear y probar el conector ECMA antes de importarlo en el host ECMA.


### <a name="firewall-requirements"></a>Requisitos de firewall

No es necesario abrir conexiones de entrada a la red corporativa. Los agentes de aprovisionamiento solo usan conexiones salientes al servicio de aprovisionamiento, lo que significa que no es necesario abrir los puertos de firewall para las conexiones entrantes. Tampoco necesita una red perimetral (DMZ), porque todas las conexiones son salientes y se realizan por medio de un canal seguro.

## <a name="agent-best-practices"></a>Procedimientos recomendados del agente
- Asegúrese de que el servicio Auto Update del agente de aprovisionamiento de Azure AD Connect se esté ejecutando. Se habilita de forma predeterminada al instalar el agente. La actualización automática es necesaria para que Microsoft admita la implementación.
- Evite todo tipo de inspección insertada en las comunicaciones TLS salientes entre los agentes y Azure. Este tipo de inspección insertada provoca la degradación en el flujo de la comunicación.
- El agente tiene que comunicarse con Azure y la aplicación; por eso la ubicación del agente afecta a la latencia de esas dos conexiones. Puede probar a minimizar la latencia del tráfico de extremo a extremo optimizando cada conexión de red. Cada conexión se puede optimizar mediante uno de estos modos:
  - Reducir la distancia entre los dos extremos del salto.
  - Elegir la red adecuada por la que pasar. Por ejemplo, puede ser más rápido pasar por una red privada que por la red pública de Internet debido a los vínculos dedicados.

## <a name="provisioning-agent-questions"></a>Preguntas sobre el agente de aprovisionamiento
Aquí se responden algunas preguntas comunes.

### <a name="what-is-the-ga-version-of-the-provisioning-agent"></a>¿Cuál es la versión de disponibilidad general del agente de aprovisionamiento?

Para obtener la versión de disponibilidad general más reciente del agente de aprovisionamiento, vea [Agente de aprovisionamiento de Azure Active Directory Connect: historial de lanzamiento de versiones](provisioning-agent-release-version-history.md).

### <a name="how-do-i-know-the-version-of-my-provisioning-agent"></a>¿Cómo puedo saber la versión del agente de aprovisionamiento?

 1. Inicie sesión en el servidor de Windows en el que está instalado el agente de aprovisionamiento.
 1. Vaya a **Panel de control** > **Desinstalar o cambiar un programa**.
 1. Busque la versión correspondiente a la entrada **Microsoft Azure AD Connect Provisioning Agent**.

### <a name="does-microsoft-automatically-push-provisioning-agent-updates"></a>¿Microsoft envía automáticamente las actualizaciones del agente de aprovisionamiento?

Sí. Microsoft actualiza automáticamente el agente de aprovisionamiento si el servicio de Windows Agent Updater de Microsoft Azure AD Connect está en ejecución. Es necesario asegurarse de que el agente está actualizado para obtener soporte técnico para la solución de problemas.

### <a name="can-i-install-the-provisioning-agent-on-the-same-server-running-azure-ad-connect-or-microsoft-identity-manager"></a>¿Puedo instalar el agente de aprovisionamiento en el mismo servidor que ejecuta Azure AD Connect o Microsoft Identity Manager?

Sí. Puede instalar el agente de aprovisionamiento en el mismo servidor que ejecuta Azure AD Connect o Microsoft Identity Manager, pero no son necesarios.

### <a name="how-do-i-configure-the-provisioning-agent-to-use-a-proxy-server-for-outbound-http-communication"></a>¿Cómo se puede configurar el agente de aprovisionamiento para usar un servidor proxy para la comunicación HTTP saliente?

El agente de aprovisionamiento admite el uso de un proxy de salida. Puede configurarlo si modifica el archivo de configuración del agente **C:\Archivos de programa\Microsoft Azure AD Connect Provisioning Agent\AADConnectProvisioningAgent.exe.config**. Agregue las líneas siguientes hacia el final del archivo, justo antes de la etiqueta `</configuration>` de cierre. Reemplace las variables `[proxy-server]` y `[proxy-port]` con los valores del puerto y el nombre del servidor proxy.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
                usesystemdefault="true"
                proxyaddress="http://[proxy-server]:[proxy-port]"
                bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```
### <a name="how-do-i-ensure-the-provisioning-agent-can-communicate-with-the-azure-ad-tenant-and-no-firewalls-are-blocking-ports-required-by-the-agent"></a>¿Cómo se garantiza que el agente de aprovisionamiento se pueda comunicar con el inquilino de Azure AD y que ningún firewall bloquee los puertos que necesita el agente?

También puede comprobar si todos los puertos necesarios están abiertos.

### <a name="how-do-i-uninstall-the-provisioning-agent"></a>¿Cómo puedo desinstalar el agente de aprovisionamiento?
1. Inicie sesión en el servidor de Windows en el que está instalado el agente de aprovisionamiento.
1. Vaya a **Panel de control** > **Desinstalar o cambiar un programa**.
1. Desinstale los programas siguientes:
     - Microsoft Azure AD Connect Provisioning Agent
     - Agent Updater de Microsoft Azure AD Connect
     - Microsoft Azure AD Connect Provisioning Agent Package


## <a name="next-steps"></a>Pasos siguientes

- [Aprovisionamiento de aplicaciones](user-provisioning.md)
- [Requisitos previos del host del conector ECMA de Azure AD](on-premises-ecma-prerequisites.md)
- [Instalación del host del conector ECMA de Azure AD](on-premises-ecma-install.md)
- [Configuración del host del conector ECMA de Azure AD](on-premises-ecma-configure.md)
