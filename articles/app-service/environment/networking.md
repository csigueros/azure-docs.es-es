---
title: Redes de App Service Environment
description: Detalles de las redes de App Service Environment
author: madsd
ms.assetid: 6f262f63-aef5-4598-88d2-2f2c2f2bfc24
ms.topic: article
ms.date: 06/30/2021
ms.author: madsd
ms.custom: seodec18
ms.openlocfilehash: 177a9095a6a1cfb15a7bd17e106406521d1eda14
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128639023"
---
# <a name="app-service-environment-networking"></a>Redes de App Service Environment

> [!NOTE]
> En este artículo se aborda App Service Environment v3, que se usa con planes de App Service v2 aislados.
> 


App Service Environment (ASE) es una implementación de inquilino único de Azure App Service que hospeda aplicaciones web, aplicaciones de API y aplicaciones de funciones. Cuando se instala ASE, se elige la instancia de Azure Virtual Network (VNet) en la que desea que se implemente. Toda la aplicación de tráfico de entrada y salida estará dentro de la VNet que especifique. El ASE se implementa en una sola subred de la red virtual. No se puede implementar nada más en esa misma subred.

## <a name="subnet-requirements"></a>Requisitos de subred

La subred debe delegarse en Microsoft.Web/hostingEnvironments y debe estar vacía.

El tamaño de la subred puede afectar a los límites de escalado de las instancias del plan de App Service en ASE. Se recomienda usar un espacio de direcciones de /24 (256 direcciones) para la subred con el fin de garantizar que hay suficientes direcciones para admitir la escala de producción.

Para usar una subred más pequeña, debe tener en cuenta los siguientes detalles de la configuración de red y de ASE.

Cualquier subred tiene cinco direcciones reservadas para fines de administración. Además de las direcciones de administración, ASE escalará dinámicamente la infraestructura subyacente y usará entre 4 y 27 direcciones en función de la configuración, la escala y la carga. Las direcciones restantes se pueden usar para las instancias del plan de App Service. El tamaño mínimo de la subred es un espacio de direcciones de /27 (32 direcciones).

Si se queda sin direcciones, se puede restringir el escalado horizontal de sus planes de App Service en ASE, o bien puede experimentar una mayor latencia durante cargas de tráfico intensivas si no se puede escalar la infraestructura subyacente.

## <a name="addresses"></a>Direcciones 

La implementación del ASE tiene la siguiente información de red en el momento de la creación:

| Tipo de dirección | description |
|--------------|-------------|
| Red virtual de ASE | La red virtual en la que se implementa el ASE. |
| Subred de ASE | La subred en la que se implementa el ASE. |
| Sufijo del dominio | El sufijo de dominio que usan las aplicaciones realizadas en este ASE. |
| IP virtual | Esta configuración es el tipo de IP virtual que usa ASE. Los dos valores posibles son interno y externo. |
| Dirección de entrada | La dirección de entrada es la dirección con la que se llega a las aplicaciones de este ASE. Si tiene una dirección IP virtual interna, se trata de una dirección en la subred del ASE. Si la dirección es externa, será una dirección orientada al público. |
| Direcciones de salida predeterminadas | De forma predeterminada, las aplicaciones de este ASE usarán esta dirección al realizar llamadas de salida a Internet. |

ASEv3 tiene detalles sobre las direcciones que usa el ASE en la parte de **direcciones IP** del portal de ASE.

![Interfaz de usuario de direcciones de ASE](./media/networking/networking-ip-addresses.png)

A medida que escale los planes de App Service en su ASE, usará más direcciones de la subred del ASE. El número de direcciones usadas variará en función del número de instancias del plan de App Service que tenga y del tráfico que recibe el ASE. Las aplicaciones del ASE no tienen direcciones dedicadas en la subred del ASE. Las direcciones específicas que usa una aplicación de la subred del ASE cambiarán con el tiempo.

## <a name="ports"></a>Puertos

ASE recibe el tráfico de la aplicación en los puertos 80 y 443. Si esos puertos están bloqueados, no podrá acceder a las aplicaciones. 

> [!NOTE]
> Se debe permitir el puerto 80 desde AzureLoadBalancer a la subred del ASE para mantener el tráfico activo entre el equilibrador de carga y la infraestructura de ASE. Podrá seguir controlando el tráfico del puerto 80 a la dirección IP virtual del ASE.
> 

## <a name="extra-configurations"></a>Configuraciones adicionales

Puede establecer grupos de seguridad de red (NSG) y tablas de enrutamientos (UDR) sin restricción. Puede forzar la tunelización de todo el tráfico saliente desde el ASE a un dispositivo de firewall de salida, como Azure Firewall, y así no tener que preocuparse por nada que no sean las dependencias de la aplicación. Puede colocar dispositivos WAF, como Application Gateway, delante del tráfico entrante a su ASE para exponer aplicaciones específicas en ese ASE. Para otra dirección de salida dedicada a Internet, puede usar una puerta de enlace NAT Gateway con el ASE. Para usar una puerta de enlace NAT Gateway con el ASE, configúrela en la subred del ASE. 

## <a name="dns"></a>DNS

### <a name="dns-configuration-to-your-ase"></a>Configuración de DNS en el ASE

Si el ASE se crea con una dirección IP virtual externa, las aplicaciones se colocan automáticamente en el DNS público. Si el ASE se crea con una dirección IP virtual interna, es posible que tenga que configurar el DNS para ella. Si ha seleccionado tener zonas privadas de Azure DNS configuradas automáticamente durante la creación del ASE, el DNS se configura en la red virtual del ASE. Si ha seleccionado configurar manualmente el DNS, debe usar su propio servidor DNS o configurar zonas privadas de Azure DNS. Para encontrar la dirección de entrada del ASE; vaya a la interfaz de usuario **Portal de ASE > Direcciones IP**. 

Si quiere usar su propio servidor DNS, debe agregar los siguientes registros:

1. Cree una zona para el &lt;nombre de ASE&gt;.appserviceenvironment.net.
1. Cree un registro D en esa zona que apunte * a la dirección IP de entrada usada por el ASE.
1. Cree un registro D en esa zona que apunte @ a la dirección IP de entrada usada por el ASE.
1. Cree una zona en el &lt;nombre de ASE&gt;.appserviceenvironment.net denominada SCM.
1. Cree un registro D en la zona SCM que apunte * a la dirección IP usada por el punto de conexión privado del ASE.

Para configurar DNS en las zonas privadas de Azure DNS:

1. Cree una zona privada de Azure DNS denominada `<ASE-name>.appserviceenvironment.net`.
1. Cree un registro D en esa zona que apunte * a la dirección IP de entrada.
1. Cree un registro D en esa zona que apunte @ a la dirección IP de entrada.
1. Cree un registro D en esa zona que apunte *.scm a la dirección IP de entrada.

La configuración de DNS para el sufijo de dominio predeterminado del ASE no restringe las aplicaciones a solo aquellas que son accesibles por esos nombres. Puede establecer un nombre de dominio personalizado sin ninguna validación en las aplicaciones de un ASE. Si quiere crear una zona denominada *contoso.net*, puede hacerlo y que apunte a la dirección IP de entrada. El nombre de dominio personalizado funciona para las solicitudes de aplicación, pero no para el sitio SCM. El sitio SCM solo está disponible en *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net*. 

#### <a name="dns-configuration-from-your-ase"></a>Configuración de DNS desde el ASE

Las aplicaciones de ASE usarán el DNS con el que está configurada la VNet. Si desea que algunas aplicaciones usen un servidor DNS distinto al que está configurado en la VNet, puede establecerlo manualmente en cada aplicación con las configuraciones de aplicación WEBSITE_DNS_SERVER y WEBSITE_DNS_ALT_SERVER. La configuración de aplicación WEBSITE_DNS_ALT_SERVER configura el servidor DNS secundario. El servidor DNS secundario solo se usa cuando no hay ninguna respuesta del servidor DNS principal. 

## <a name="limitations"></a>Limitaciones

Aunque el ASE se implementa en una red virtual de cliente, hay algunas características de red que no están disponibles con ASE. 

* Envío de tráfico SMTP. Puede tener alertas que se desencadenen por correo electrónico, pero la aplicación no puede enviar tráfico saliente en el puerto 25.
* Uso de Network Watcher o de flujo de NSG para supervisar el tráfico saliente.

## <a name="more-resources"></a>Más recursos

- [Referencia de variables de entorno y configuración de la aplicación](../reference-app-settings.md)