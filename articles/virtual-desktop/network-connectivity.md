---
title: Descripción de la conectividad de red de Azure Virtual Desktop
titleSuffix: Azure
description: Información sobre la conectividad de red de Azure Virtual Desktop
author: gundarev
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: denisgun
ms.openlocfilehash: 2bffc15980013d91bfe24101499550d1dcfea6e2
ms.sourcegitcommit: 8bca2d622fdce67b07746a2fb5a40c0c644100c6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/09/2021
ms.locfileid: "111753240"
---
# <a name="understanding-azure-virtual-desktop-network-connectivity"></a>Descripción de la conectividad de red de Azure Virtual Desktop

Azure Virtual Desktop permite hospedar sesiones de cliente en los hosts de sesión que se ejecutan en Azure. Microsoft administra partes de los servicios en nombre del cliente y proporciona puntos de conexión seguros para conectar clientes y hosts de sesión. En el diagrama siguiente se ofrece información general de alto nivel de las conexiones de red que usa Azure Virtual Desktop.

:::image type="content" source="media/windows-virtual-desktop-network-connections.svg" alt-text="Diagrama de las conexiones de red de Azure Virtual Desktop" lightbox="media/windows-virtual-desktop-network-connections.svg":::

## <a name="session-connectivity"></a>Conectividad de sesión

Azure Virtual Desktop usa un protocolo de escritorio remoto (RDP) para proporcionar funcionalidades de entrada y visualización remotas a través de conexiones de red. RDP se lanzó inicialmente con Windows NT 4.0 Terminal Server Edition y ha evolucionado continuamente con cada versión de Microsoft Windows y Windows Server. Desde el principio, RDP se ha desarrollado para no depender de la pila de transporte subyacente y, en la actualidad, admite varios tipos de transporte.

## <a name="reverse-connect-transport"></a>Transporte de conexión inversa

Azure Virtual Desktop usa el transporte de conexión inversa para establecer la sesión remota y para transportar el tráfico del RDP. A diferencia de las implementaciones de Servicios de Escritorio remoto locales, el transporte de conexión inversa no usa un cliente de escucha TCP para recibir conexiones RDP entrantes. En su lugar, usa la conectividad saliente a la infraestructura de Azure Virtual Desktop a través de la conexión HTTPS.

## <a name="session-host-communication-channel"></a>Canal de comunicación del host de sesión

Al iniciar el host de sesión de Azure Virtual Desktop, el servicio Cargador del agente de Escritorio remoto establece el canal de comunicación persistente del agente de Azure Virtual Desktop. Este canal de comunicación se superpone a una conexión de Seguridad de la capa de transporte (TLS) segura y sirve como bus para el intercambio de mensajes de servicio entre el host de sesión y la infraestructura de Azure Virtual Desktop.

## <a name="client-connection-sequence"></a>Secuencia de la conexión cliente

La secuencia de la conexión cliente se describe a continuación:

1. El uso del usuario cliente de Azure Virtual Desktop compatible se suscribe al área de trabajo de esta aplicación.
2. Azure Active Directory autentica al usuario y devuelve el token usado para enumerar los recursos disponibles para un usuario.
3. El cliente pasa el token al servicio de suscripción a fuentes de Azure Virtual Desktop.
4. El servicio de suscripción a fuentes de Azure Virtual Desktop valida el token.
5. El servicio de suscripción a fuentes de Azure Virtual Desktop pasa la lista de escritorios y RemoteApps disponibles al cliente en forma de configuración de conexión con firma digital.
6. El cliente almacena la configuración de la conexión para cada recurso disponible en un conjunto de archivos .rdp.
7. Cuando un usuario selecciona el recurso para conectarse, el cliente usa el archivo .rdp asociado y establece la conexión TLS 1.2 segura con la instancia de la puerta de enlace de Azure Virtual Desktop más cercana y pasa la información de conexión.
8. La puerta de enlace de Azure Virtual Desktop valida la solicitud y pide al agente de Azure Virtual Desktop que orqueste la conexión.
9. El agente de Azure Virtual Desktop identifica el host de sesión y usa el canal de comunicación persistente establecido previamente para inicializar la conexión.
10. La pila de Escritorio remoto inicia la conexión TLS 1.2 con la misma instancia de puerta de enlace de Azure Virtual Desktop que usa el cliente.
11. Después de que tanto el cliente como el host de sesión se conecten a la puerta de enlace, esta empieza a retransmitir los datos sin procesar entre ambos puntos de conexión, lo que establece el transporte de conexión inversa base para el RDP.
12. Una vez establecido el transporte base, el cliente inicia el protocolo de enlace del RDP.

## <a name="connection-security"></a>Seguridad de conexión

TLS 1.2 se usa para todas las conexiones iniciadas desde los clientes y hosts de sesión a los componentes de la infraestructura de Azure Virtual Desktop. Azure Virtual Desktop usa los mismos cifrados TLS 1.2 que [Azure Front Door](../frontdoor/front-door-faq.yml#what-are-the-current-cipher-suites-supported-by-azure-front-door-). Es importante asegurarse de que tanto los equipos cliente como los hosts de sesión pueden utilizar estos cifrados.
Para el transporte de conexión inversa, tanto el cliente como el host de sesión se conectan a la puerta de enlace de Azure Virtual Desktop. Después de establecer la conexión TCP, el cliente o el host de sesión validan el certificado de la puerta de enlace de Azure Virtual Desktop.
Después de establecer el transporte base, el RDP establece una conexión TLS anidada entre el cliente y el host de sesión mediante los certificados del host de sesión. De forma predeterminada, el certificado usado para el cifrado RDP lo genera automáticamente el sistema operativo durante la implementación. Si lo desea, los clientes pueden implementar certificados administrados centralmente emitidos por la entidad de certificación de empresa. Para más información sobre los certificados de configuración, consulte la [documentación de Windows Server](/troubleshoot/windows-server/remote/remote-desktop-listener-certificate-configurations).

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre los requisitos de ancho de banda de Azure Virtual Desktop, consulte la [descripción de los requisitos del protocolo de Escritorio remoto (RDP) para Azure Virtual Desktop](rdp-bandwidth.md).
* Para empezar a trabajar con calidad de servicio (QoS) para Azure Virtual Desktop, consulte [Implementación de calidad de servicio (QoS) para Azure Virtual Desktop](rdp-quality-of-service-qos.md).
