---
title: Redes de App Service Environment
description: Detalles de las redes de App Service Environment
author: madsd
ms.topic: overview
ms.date: 11/15/2021
ms.author: madsd
ms.openlocfilehash: cdedc94e64a0646c7e4ffa8c93f082d040e440c7
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132524082"
---
# <a name="app-service-environment-networking"></a>Redes de App Service Environment

> [!NOTE]
> En este artículo se aborda App Service Environment v3, que se usa con planes de App Service v2 aislados.
> 

App Service Environment (ASE) es una implementación de inquilino único de Azure App Service que hospeda contenedores de Windows y Linux, aplicaciones web, aplicaciones de API, aplicaciones lógicas y aplicaciones de funciones. Cuando se instala ASE, se elige la instancia de Azure Virtual Network en la que desea que se implemente. Todo el tráfico entrante y saliente de la aplicación estará dentro de la red virtual que especifique. El ASE se implementa en una única subred de la red virtual. No se puede implementar nada más en esa misma subred.

## <a name="subnet-requirements"></a>Requisitos de subred

La subred debe delegarse en Microsoft.Web/hostingEnvironments y debe estar vacía.

El tamaño de la subred puede afectar a los límites de escalado de las instancias del plan de App Service en ASE. Se recomienda usar un espacio de direcciones `/24` (256 direcciones) para la subred con el fin de garantizar que hay suficientes direcciones para admitir la escala de producción.

Para usar una subred más pequeña, debe tener en cuenta los siguientes detalles de la configuración de red y de ASE.

Cualquier subred tiene cinco direcciones reservadas para fines de administración. Además de las direcciones de administración, ASE escalará dinámicamente la infraestructura subyacente y usará entre 4 y 27 direcciones en función de la configuración y la carga. Las direcciones restantes se pueden usar para las instancias del plan de App Service. El tamaño mínimo de la subred es un espacio de direcciones `/27` (32 direcciones).

Si se queda sin direcciones en la subred, se puede restringir el escalado horizontal de sus planes de App Service en ASE, o bien puede experimentar una mayor latencia durante cargas de tráfico intensivas si no se puede escalar la infraestructura subyacente.

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

## <a name="ports-and-network-restrictions"></a>Puertos y restricciones de red

Para que la aplicación reciba tráfico, debe asegurarse de que las reglas de grupos de seguridad de red (NSG) entrantes permiten que la subred de ASE reciba tráfico de los puertos necesarios. Además de los puertos en los que desea recibir tráfico, debe asegurarse de que AzureLoadBalancer puede conectarse a la subred de ASE en el puerto 80. Esto se usa para las comprobaciones internas de estado de la máquina virtual. Todavía puede controlar el tráfico del puerto 80 desde la red virtual a la subred de ASE.

La recomendación general es configurar la siguiente regla de NSG entrante:

|Puerto|Source|Destination|
|-|-|-|
|80 443|VirtualNetwork|Rango de subred de ASE|

El requisito mínimo para que el ASE esté operativo es:

|Puerto|Source|Destination|
|-|-|-|
|80|AzureLoadBalancer|Rango de subred de ASE|

Si usa la regla mínima necesaria, puede que necesite una o varias reglas más para el tráfico de la aplicación y, si usa cualquiera de las opciones de implementación o depuración, también tendrá que permitir este tráfico a la subred de ASE. El origen de estas reglas puede ser VirtualNetwork o una o varias direcciones IP o intervalos IP de cliente específicos. El destino siempre será el intervalo de subredes de ASE.

Los puertos de acceso de aplicación normales son:

|Uso|Puertos|
|-|-|
|HTTP/HTTPS|80, 443|
|FTP/FTPS|21, 990, 10001-10020|
|Depuración remota en Visual Studio|4022, 4024, 4026|
|Servicio Web Deploy|8172|

## <a name="network-routing"></a>Enrutamiento de red

Puede establecer tablas de rutas (UDR) sin restricciones. Puede forzar la tunelización de todo el tráfico de la aplicación saliente desde el ASE a un dispositivo de firewall de salida, como Azure Firewall, y así no tener que preocuparse por nada que no sean las dependencias de la aplicación. Puede colocar dispositivos WAF, como Application Gateway, delante del tráfico entrante a su ASE para exponer aplicaciones específicas en ese ASE. Si desea personalizar la dirección saliente de las aplicaciones en un ASE, puede agregar un NAT Gateway a la subred de ASE.

## <a name="dns"></a>DNS

En las secciones siguientes se describen las consideraciones de DNS y la configuración entrante al ASE y saliente desde el ASE.

### <a name="dns-configuration-to-your-ase"></a>Configuración de DNS en el ASE

Si el ASE se crea con una dirección IP virtual externa, las aplicaciones se colocan automáticamente en el DNS público. Si el ASE se crea con una dirección IP virtual interna, es posible que tenga que configurar el DNS para ella. Si ha seleccionado tener zonas privadas de Azure DNS configuradas automáticamente durante la creación del ASE, el DNS se configura en la red virtual del ASE. Si ha seleccionado configurar manualmente el DNS, debe usar su propio servidor DNS o configurar zonas privadas de Azure DNS. Para encontrar la dirección de entrada del ASE; vaya a la interfaz de usuario **Portal de ASE > Direcciones IP**. 

Si quiere usar su propio servidor DNS, debe agregar los siguientes registros:

1. Cree una zona para `<ASE-name>.appserviceenvironment.net`.
1. Cree un registro D en esa zona que apunte * a la dirección IP de entrada usada por el ASE.
1. Cree un registro D en esa zona que apunte @ a la dirección IP de entrada usada por el ASE.
1. Cree una zona en `<ASE-name>.appserviceenvironment.net` llamada scm.
1. Cree un registro D en la zona SCM que apunte * a la dirección IP usada por el punto de conexión privado del ASE.

Para configurar DNS en las zonas privadas de Azure DNS:

1. Cree una zona privada de Azure DNS denominada `<ASE-name>.appserviceenvironment.net`.
1. Cree un registro D en esa zona que apunte * a la dirección IP de entrada.
1. Cree un registro D en esa zona que apunte @ a la dirección IP de entrada.
1. Cree un registro D en esa zona que apunte *.scm a la dirección IP de entrada.

Además del dominio predeterminado proporcionado cuando se crea una aplicación, también puede agregar un dominio personalizado a la aplicación. Puede establecer un nombre de dominio personalizado sin ninguna validación en las aplicaciones de un ASE con un ILB. Si usa dominios personalizados, deberá asegurarse de que tienen registros DNS configurados. Puede seguir las instrucciones anteriores para configurar zonas DNS y registros para un nombre de dominio personalizado reemplazando el nombre de dominio predeterminado por el nombre de dominio personalizado. El nombre de dominio personalizado funciona para las solicitudes de aplicación, pero no para el sitio SCM. El sitio SCM solo está disponible en *&lt;appname&gt;.scm.&lt;asename&gt;.appserviceenvironment.net*.

### <a name="dns-configuration-from-your-ase"></a>Configuración de DNS desde el ASE

Las aplicaciones del ASE usarán el DNS con el que está configurada la red virtual. Si desea que algunas aplicaciones usen un servidor DNS distinto al que está configurado en la red virtual, puede establecerlo manualmente en cada aplicación con las configuraciones de aplicación WEBSITE_DNS_SERVER y WEBSITE_DNS_ALT_SERVER. La configuración de aplicación WEBSITE_DNS_ALT_SERVER configura el servidor DNS secundario. El servidor DNS secundario solo se usa cuando no hay ninguna respuesta del servidor DNS principal.

## <a name="limitations"></a>Limitaciones

Aunque el ASE se implementa en una red virtual de cliente, hay algunas características de red que no están disponibles con ASE:

* Envío de tráfico SMTP. Puede tener alertas que se desencadenen por correo electrónico, pero la aplicación no puede enviar tráfico saliente en el puerto 25.
* Uso de Network Watcher o de flujo de NSG para supervisar el tráfico saliente.

## <a name="more-resources"></a>Más recursos

- [Referencia de variables de entorno y configuración de la aplicación](../reference-app-settings.md)