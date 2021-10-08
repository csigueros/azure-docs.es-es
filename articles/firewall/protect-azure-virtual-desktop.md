---
title: Uso de Azure Firewall para proteger Azure Virtual Desktop
description: Aprenda a usar Azure Firewall para proteger las implementaciones de Azure Virtual Desktop
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 08/09/2021
ms.author: victorh
ms.openlocfilehash: 5c165dc8f00bb21894de06e541c02788bd7b51e5
ms.sourcegitcommit: f29615c9b16e46f5c7fdcd498c7f1b22f626c985
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2021
ms.locfileid: "129425000"
---
# <a name="use-azure-firewall-to-protect-azure-virtual-desktop-deployments"></a>Uso de Azure Firewall para proteger las implementaciones de Azure Virtual Desktop

Azure Virtual Desktop es un servicio de virtualización de aplicaciones y de escritorio que se ejecuta en Azure. Cuando un usuario final se conecta a un entorno de Azure Virtual Desktop, un grupo de hosts ejecuta su sesión. Un grupo de hosts es una colección de máquinas virtuales de Azure que se registran en Azure Virtual Desktop como hosts de sesión. Estas máquinas virtuales se ejecutan en la red virtual y están sujetas a sus controles de seguridad. Necesitan acceso saliente a Internet para que el servicio Azure Virtual Desktop funcione correctamente y es posible que también necesiten acceso a Internet para los usuarios finales. Azure Firewall puede ayudarle a bloquear su entorno y a filtrar el tráfico saliente.

[ ![Arquitectura de Azure Virtual Desktop](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png) ](media/protect-windows-virtual-desktop/windows-virtual-desktop-architecture-diagram.png#lightbox)

Siga las instrucciones de este artículo para proporcionar protección adicional para el grupo de hosts de Azure Virtual Desktop mediante Azure Firewall.

## <a name="prerequisites"></a>Requisitos previos

 - Un entorno de Azure Virtual Desktop implementado y un grupo de hosts.
 - Una instancia de Azure Firewall implementada con al menos una directiva de Firewall Manager

   Para obtener más información, vea [Tutorial: Creación de un grupo de hosts mediante Azure Portal](../virtual-desktop/create-host-pools-azure-marketplace.md).

Para obtener más información sobre los entornos de Azure Virtual Desktop, vea [Entorno de Azure Virtual Desktop](../virtual-desktop/environment-setup.md).

## <a name="host-pool-outbound-access-to-azure-virtual-desktop"></a>Acceso de salida del grupo de hosts a Azure Virtual Desktop

Las máquinas virtuales de Azure que cree para Azure Virtual Desktop deben tener acceso a varios nombres de dominio completos (FQDN) para que funcionen correctamente. Azure Firewall proporciona una etiqueta FQDN de Azure Virtual Desktop para simplificar esta configuración. Siga estos pasos para permitir el tráfico saliente de la plataforma Azure Virtual Desktop:

Tendrá que crear una directiva de Azure Firewall y colecciones de reglas para reglas de red y reglas de aplicaciones. Asigne una prioridad a la colección de reglas y una acción de permitir o denegar.

### <a name="create-network-rules"></a>Creación de reglas de red

| NOMBRE      | Tipo de origen | Source                    | Protocolo | Puertos de destino | Tipo de destino | Destination                       |
| --------- | ----------- | ------------------------- | -------- | ----------------- | ---------------- | --------------------------------- |
| Nombre de la regla | Dirección IP  | Dirección IP de la red virtual o subred | TCP      | 80                | Dirección IP       | 169.254.169.254, 168.63.129.16    |
| Nombre de la regla | Dirección IP  | Dirección IP de la red virtual o subred | TCP      | 443               | Etiqueta de servicio      | AzureCloud, WindowsVirtualDesktop |
| Nombre de la regla | Dirección IP  | Dirección IP de la red virtual o subred | TCP, UDP | 53                | Dirección IP       | *                                 |

> [!NOTE]
> Es posible que algunas implementaciones no necesiten reglas de DNS. Por ejemplo, los controladores de dominio de Azure Active Directory reenvían consultas de DNS a Azure DNS en la dirección 168.63.129.16.

### <a name="create-application-rules"></a>Creación de reglas de aplicación

| NOMBRE      | Tipo de origen | Source                    | Protocolo   | Tipo de destino | Destination                                                                                 |
| --------- | ----------- | ------------------------- | ---------- | ---------------- | ------------------------------------------------------------------------------------------- |
| Nombre de la regla | Dirección IP  | Dirección IP de la red virtual o subred | Https:443  | Etiqueta de FQDN         | WindowsVirtualDesktop, WindowsUpdate, Windows Diagnostics, MicrosoftActiveProtectionService |
| Nombre de la regla | Dirección IP  | Dirección IP de la red virtual o subred | Https:1688 | FQDN             | kms.core.windows.net                                                                        |

> [!IMPORTANT]
> Se recomienda no utilizar la inspección de TLS con Azure Virtual Desktop. Para más información, consulte las [directrices del servidor proxy](../virtual-desktop/proxy-server-support.md#dont-use-ssl-termination-on-the-proxy-server).

## <a name="host-pool-outbound-access-to-the-internet"></a>Acceso de salida del grupo de hosts a Internet

En función de las necesidades de la organización, es posible que quiera habilitar un acceso seguro de salida a Internet para los usuarios finales. Si la lista de destinos permitidos está bien definida (por ejemplo, para el [acceso a Microsoft 365](/microsoft-365/enterprise/microsoft-365-ip-web-service)) puede usar reglas de red y de aplicación de Azure Firewall para configurar el acceso necesario. De esta forma, el tráfico del usuario final se enruta directamente a Internet para obtener el mejor rendimiento. Si tiene que permitir la conectividad de red para Windows 365 o Intune, vea [Requisitos de red para Windows 365](/windows-365/requirements-network#allow-network-connectivity) y [Puntos de conexión de red para Intune](/mem/intune/fundamentals/intune-endpoints).

Si quiere filtrar el tráfico saliente de Internet de los usuarios mediante una puerta de enlace web segura local existente, puede configurar exploradores web u otras aplicaciones que se ejecuten en el grupo de hosts de Azure Virtual Desktop con una configuración de proxy explícita. Por ejemplo, consulte [Cómo usar las opciones de línea de comandos de Microsoft Edge para establecer la configuración de proxy](/deployedge/edge-learnmore-cmdline-options-proxy-settings). Esta configuración de proxy solo afecta al acceso a Internet del usuario final, de modo que permite el tráfico saliente de la plataforma Azure Virtual Desktop directamente mediante Azure Firewall.

## <a name="control-user-access-to-the-web"></a>Control del acceso de los usuarios a la web

Los administradores pueden permitir o denegar el acceso de los usuarios a diferentes categorías de sitios web. Agregue una regla a la colección de aplicaciones desde la dirección IP específica a las categorías web que quiera permitir o denegar. Revise todas las [categorías web](web-categories.md).

## <a name="additional-considerations"></a>Consideraciones adicionales

Es posible que tenga que configurar reglas de firewall adicionales, en función de los requisitos:

- Acceso al servidor NTP

  De forma predeterminada, las máquinas virtuales que ejecutan Windows se conectan a `time.windows.com` por medio del puerto 123 de UDP para la sincronización de la hora. Cree una regla de red para permitir este acceso o para un servidor horario que use en su entorno.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre Azure Virtual Desktop: [¿Qué es Azure Virtual Desktop?](../virtual-desktop/overview.md)
