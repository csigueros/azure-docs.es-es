---
title: Tunelización forzada de Azure Firewall
description: Puede configurar la tunelización forzada para enrutar el tráfico vinculado a Internet con una aplicación virtual de red o firewall para su posterior procesamiento.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 08/13/2021
ms.author: victorh
ms.openlocfilehash: b3d52451713c8fc504487aa293d566264f4eadb6
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183380"
---
# <a name="azure-firewall-forced-tunneling"></a>Tunelización forzada de Azure Firewall

Al configurar una nueva instancia de Azure Firewall, puede enrutar todo el tráfico vinculado a Internet a un próximo salto designado, en lugar de ir directamente a Internet. Por ejemplo, es posible que tenga una ruta predeterminada anunciada por medio de BGP o mediante Ruta definida por el usuario (UDR) a fin de forzar el tráfico a un firewall perimetral local u otra aplicación virtual de red (NVA) para procesar el tráfico de red antes de que pase a Internet. Para admitir esta configuración, debe crear una instancia de Azure Firewall con la configuración de túnel forzado habilitada. Se trata de un requisito obligatorio para evitar la interrupción del servicio. Si se trata de un firewall existente previamente, debe volver a crearlo en modo de túnel forzado para admitir esta configuración. Para obtener más información, vea [Preguntas frecuentes sobre Azure Firewall](firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) sobre cómo detener y reiniciar un firewall en modo de túnel forzado.

## <a name="forced-tunneling-configuration"></a>Configuración de la tunelización forzada

Puede configurar la tunelización forzada durante la creación del firewall si habilita el modo de túnel forzado, como se muestra a continuación. Para admitir la tunelización forzada, el tráfico de administración de servicio se separa del tráfico del cliente. Se requiere una subred dedicada adicional denominada **AzureFirewallManagementSubnet** (tamaño de subred mínimo /26) con su propia dirección IP pública asociada. 

En el modo de tunelización forzada, el servicio Azure Firewall incorpora la subred de administración (AzureFirewallManagementSubnet) para sus fines *operativos*. De forma predeterminada, el servicio asocia una tabla de rutas proporcionada por el sistema a la subred de administración. La única ruta permitida en esta subred es una ruta predeterminada a Internet y *Propagar las rutas de la puerta de enlace* se debe deshabilitar. Evite asociar tablas de rutas de cliente a la subred de administración al crear el firewall. 

:::image type="content" source="media/forced-tunneling/forced-tunneling-configuration.png" alt-text="Configuración de la tunelización forzada":::

En esta configuración, *AzureFirewallSubnet* puede incluir rutas a cualquier firewall local o NVA para procesar el tráfico antes de pasarlo a Internet. También puede publicar estas rutas mediante BGP en *AzureFirewallSubnet* si está habilitada la opción **Propagate gateway routes** (Propagar rutas de puerta de enlace) en la subred.

Por ejemplo, puede crear una ruta predeterminada en *AzureFirewallSubnet* con la puerta de enlace de VPN como siguiente salto para llegar al dispositivo local. O bien, puede habilitar la **Propagate gateway routes** (Propagar rutas de puerta de enlace) para obtener las rutas adecuadas a la red local.

:::image type="content" source="media/forced-tunneling/route-propagation.png" alt-text="Propagación de rutas de puerta de enlace de red virtual":::

Si ha habilitado la tunelización forzada, al tráfico vinculado a Internet se le aplica SNAT para una de las direcciones IP privadas del firewall en AzureFirewallSubnet, ocultando el origen del firewall local.

Si su organización usa un intervalo de direcciones IP públicas para las redes privadas, Azure Firewall aplicará SNAT al tráfico para una de las direcciones IP privadas de firewall en AzureFirewallSubnet. Sin embargo, puede configurar Azure Firewall de modo que **no** aplique SNAT al intervalo de direcciones IP públicas. Para más información, consulte [Aplicación de SNAT por parte de Azure Firewall a intervalos de direcciones IP privadas](snat-private-range.md).

Después de configurar Azure Firewall para admitir la tunelización forzada, no se puede deshacer la configuración. Si quita el resto de configuraciones de IP del firewall, también se quitará la configuración de IP de administración y se desasignará el firewall. No se puede quitar la dirección IP pública asignada a la configuración de IP de administración, pero puede asignar una dirección IP pública diferente.

## <a name="next-steps"></a>Pasos siguientes

- [Tutorial: Implementación y configuración de Azure Firewall en una red híbrida con Azure Portal](tutorial-hybrid-portal.md)
