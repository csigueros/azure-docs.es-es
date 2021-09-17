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
ms.openlocfilehash: 2bd76a9633311a073f2a672efd1560272deddb48
ms.sourcegitcommit: 2da83b54b4adce2f9aeeed9f485bb3dbec6b8023
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2021
ms.locfileid: "122770104"
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

## <a name="ecma-connector-host-architecture"></a>Arquitectura del host del conector de ECMA
El host del conector de ECMA tiene varias áreas que usa para el aprovisionamiento local.  El diagrama siguiente es un dibujo conceptual que presenta estas áreas individuales.  En la tabla siguiente se describen con más detalle.

[![Host del conector de ECMA](.\media\on-premises-application-provisioning-architecture\ecma-2.png)](.\media\on-premises-application-provisioning-architecture\ecma-2.png#lightbox)



|Área|Descripción|
|-----|-----|
|Puntos de conexión|Responsable de la comunicación y la transferencia de datos con el servicio de aprovisionamiento de Azure AD.|
|Caché en memoria|Se usa para almacenar los datos importados desde el origen de datos local.|
|Sincronización automática|Proporciona sincronización asincrónica de datos entre el host del conector de ECMA y el origen de datos local.|
|Lógica de negocios|Se usa para coordinar todas las actividades del host del conector de ECMA.  La hora de la sincronización automática se puede configurar en el host de ECMA en la página de propiedades.|

### <a name="about-anchor-attributes-and-distinguished-names"></a>Acerca de los atributos delimitadores y los nombres distintivos
La siguiente información se proporciona para explicar mejor los atributos delimitadores y los nombres distintivos, que el conector de genericSQL usa especialmente.

El atributo delimitador es un atributo único de un tipo de objeto que no cambia y representa ese objeto en la memoria caché en memoria del host del conector de ECMA.

El nombre distintivo (DN) es un nombre que identifica de forma única un objeto indicando su ubicación actual en la jerarquía del directorio.  O, en el caso de SQL, en la partición. El nombre se forma concatenando el atributo delimitador a la raíz de la partición de directorio. 

Cuando se piensa en nombres distintivos tradicionales en formato tradicional, por ejemplo, de Active Directory o LDAP, se piensa en algo similar a:

  CN=Lola Jacobson,CN=Users,DC=contoso,DC=com

Sin embargo, para un origen de datos como SQL, que es plano, no jerárquico, el nombre distintivo debe estar ya presente en una de las tablas o crearse a partir de la información que proporcionamos al host del conector de ECMA.  

Esto se consigue al marcar **Autogenerated** (Generado automáticamente) en la casilla al configurar el conector de genericSQL. Al elegir el nombre distintivo generado automáticamente, el host de ECMA generará uno en formato LDAP: CN=&lt;valordelimitador&gt;,OBJECT=&lt;type&gt;. También se presupone que la opción DN is Anchor (DN es delimitador) **no está marcado** en la página Conectividad. 
 
 [![Opción DN is Anchor (DN es delimitador) sin marcar](.\media\on-premises-application-provisioning-architecture\user-2.png)](.\media\on-premises-application-provisioning-architecture\user-2.png#lightbox)

El conector de genericSQL espera que el nombre distintivo se rellene en formato LDAP.  El conector de genericSQL usa el estilo de LDAP con el nombre de componente "OBJECT=". Esto le permite usar particiones (cada tipo de objeto es una partición).

Como el host del conector de ECMA actualmente solo admite el tipo de objeto USER, OBJECT=&lt;type&gt; será OBJECT=USER.  Por lo tanto, el nombre distintivo de un usuario con un valor delimitador ljacobson sería:

  CN=ljacobson,OBJECT=USER


### <a name="user-creation-workflow"></a>Flujo de trabajo de creación de usuarios

1.  El servicio de aprovisionamiento de Azure AD consulta el host del conector de ECMA para ver si el usuario existe.  Usa como filtro el **atributo coincidente**.  Este atributo se define en el portal de Azure AD, en Aplicaciones empresariales -> On-premises provisioning (Aprovisionamiento local) -> Aprovisionamiento -> Attribute matching (Atributo coincidente).  Se indica mediante el 1 para la prioridad de coincidencia.
Puede definir uno o varios atributos coincidentes y priorizarlos.  Si desea cambiar el atributo coincidente, también puede hacerlo.
 [![Atributo coincidente](.\media\on-premises-application-provisioning-architecture\match-1.png)](.\media\on-premises-application-provisioning-architecture\match-1.png#lightbox)

2.  El host del conector de ECMA recibe la solicitud GET y consulta su caché interna para ver si el usuario existe y se ha importado.  Esto se hace mediante el **atributo de consulta**. El atributo de consulta se define en la página de tipos de objeto.  
 [![Atributo de consulta](.\media\on-premises-application-provisioning-architecture\match-2.png)](.\media\on-premises-application-provisioning-architecture\match-2.png#lightbox)


3. Si el usuario no existe, Azure AD realizará una solicitud POST para crearlo.  El host del conector de ECMA responderá a Azure AD con HTTP 201 y proporcionará un identificador de usuario. Este identificador se deriva del valor delimitador definido en la página de tipos de objeto. Este delimitador lo usará Azure AD para consultar el host del conector de ECMA en solicitudes futuras. 
4. Si se produce un cambio en el usuario de Azure AD, Azure AD realizará una solicitud GET para recuperar el usuario mediante el delimitador del paso anterior, en lugar del atributo coincidente del paso 1. Esto permite, por ejemplo, que el nombre principal de usuario cambie sin romper el vínculo entre el usuario de Azure AD y la aplicación.  


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
 2. Vaya a **Panel de control** > **Desinstalar o cambiar un programa**.
 3. Busque la versión correspondiente a la entrada **Microsoft Azure AD Connect Provisioning Agent**.

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
 2. Vaya a **Panel de control** > **Desinstalar o cambiar un programa**.
 3. Desinstale los programas siguientes:
     - Microsoft Azure AD Connect Provisioning Agent
     - Agent Updater de Microsoft Azure AD Connect
     - Microsoft Azure AD Connect Provisioning Agent Package





## <a name="next-steps"></a>Pasos siguientes

- [Aprovisionamiento de aplicaciones](user-provisioning.md)
- [Requisitos previos del host del conector ECMA de Azure AD](on-premises-ecma-prerequisites.md)
- [Instalación del host del conector ECMA de Azure AD](on-premises-ecma-install.md)
- [Configuración del host del conector ECMA de Azure AD](on-premises-ecma-configure.md)
