---
title: Procedimientos recomendados de seguridad de la red - Microsoft Azure
description: Este artículo proporciona un conjunto de procedimientos recomendados de seguridad de la red con funcionalidades de Azure integradas.
services: security
author: TerryLanfear
manager: barbkess
editor: TomShinder
ms.assetid: 7f6aa45f-138f-4fde-a611-aaf7e8fe56d1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/02/2019
ms.author: TomSh
ms.openlocfilehash: 5969c015a360d7f1c859070b1fc05df5bd469bbe
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132327089"
---
# <a name="azure-best-practices-for-network-security"></a>Procedimientos recomendados de seguridad de la red de Azure
En este artículo se aborda un conjunto de procedimientos recomendados de Azure que sirven para mejorar la seguridad de la red. Estos procedimientos recomendados se derivan de nuestra experiencia con las redes en Azure, y las experiencias de clientes como usted.

Para cada procedimiento recomendado, en este artículo se explica:

* Qué es el procedimiento recomendado
* Por qué le conviene habilitar este procedimiento recomendado
* Cuál podría ser el resultado si no habilita el procedimiento recomendado
* Alternativas posibles al procedimiento recomendado
* Cómo aprender a habilitar el procedimiento recomendado

Estos procedimientos recomendados de seguridad de la red de Azure se basan en las funcionalidades y los conjuntos de características de la plataforma Azure existentes cuando se redactó. Las opiniones y las tecnologías cambian con el tiempo, por lo que se actualizará de forma periódica para reflejar esos cambios.

## <a name="use-strong-network-controls"></a>Uso de controles de red sólidos
Puede conectar las [máquinas virtuales y los dispositivos de Azure](https://azure.microsoft.com/services/virtual-machines/) a otros dispositivos en red, colocándolos en [redes virtuales de Azure](../../virtual-network/index.yml). Esto es, puede conectar tarjetas de interfaz de red virtual a una red virtual para posibilitar las comunicaciones basadas en TCP/IP entre dispositivos habilitados para la red. Las máquinas virtuales conectadas a una red virtual de Azure pueden conectarse a dispositivos en la misma red virtual, en distintas redes virtuales, en Internet o, incluso, en sus propias redes locales.

Al planear la red y la seguridad de la red, se recomienda centralizar lo siguiente:

- La administración de funciones de red centrales como ExpressRoute, el aprovisionamiento de redes virtuales y subredes y la asignación de direcciones IP.
- El gobierno de elementos de seguridad de red como las funciones de aplicación virtual de red del tipo ExpressRoute, el aprovisionamiento de redes virtuales y subredes y la asignación de direcciones IP.

Si usa un conjunto común de herramientas de administración para supervisar la red y la seguridad de la red, tendrá una visibilidad clara de ambos aspectos. Una estrategia de seguridad sencilla y unificada reduce los errores, ya que permite una mejor comprensión del lado humano y aumenta la confiabilidad de la automatización.

## <a name="logically-segment-subnets"></a>Segmentación lógica de subredes
Las redes virtuales de Azure son similares a una LAN de red local. La idea detrás de una red virtual de Azure es crear una sola red basada en espacios privados de direcciones IP en la que pueden colocar todas las máquinas virtuales de Azure. Los espacios de direcciones IP privados están en los intervalos de clase A (10.0.0.0/8), B (172.16.0.0/12) y C (192.168.0.0/16).

Los procedimientos recomendados para segmentar lógicamente las subredes son:

**Procedimiento recomendado**: no asigne reglas de permiso con intervalos muy amplios (por ejemplo, permita de 0.0.0.0 a 255.255.255.255).  
**Detalles**: asegúrese de que los procedimientos de solución de problemas no fomentan ni permiten configurar estos tipos de reglas. Estas reglas de permiso dan una falsa sensación de seguridad y, a menudo, son detectadas y explotadas por equipos de operaciones clandestinas.

**Procedimiento recomendado**: segmentar el mayor espacio de direcciones en las subredes.   
**Detalles**: use los principios de subred basado en [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) para crear las subredes.

**Procedimiento recomendado**: crear controles de acceso de red entre subredes. El enrutamiento entre subredes se realizará automáticamente y no es necesario configurar manualmente las tablas de enrutamiento. Sin embargo, el valor predeterminado es que no hay ningún control de acceso a la red entre las subredes creadas una red de Azure Virtual Network.   
**Detalles**: use un [grupo de seguridad de red](../../virtual-network/virtual-network-vnet-plan-design-arm.md) para protegerse del tráfico no solicitado en subredes de Azure. Los grupos de seguridad de red son dispositivos de inspección de paquetes con estado simple que utilizan el método tupla 5 (IP de origen, puerto de origen, dirección IP de destino, puerto de destino y el protocolo de nivel 4) para crear reglas de permiso o denegación del tráfico de la red. Puede permitir o denegar el tráfico hacia y desde una sola dirección IP, hacia y desde varias direcciones IP o, incluso, hacia y desde subredes enteras.

Al usar grupos de seguridad de red para controlar el acceso a la red entre subredes, puede establecer recursos que pertenezcan a la misma zona de seguridad o rol en sus propias subredes.

**Procedimiento recomendado**: evitar el uso de redes virtuales y subredes pequeñas para garantizar la simplicidad y la flexibilidad.   
**Detalles**: la mayoría de las organizaciones agregan más recursos de lo planeado inicialmente, y volver a asignar direcciones requiere un esfuerzo enorme. Si se usan subredes pequeñas, el valor de seguridad que se obtiene es limitado, y asignar un grupo de seguridad de red a cada subred supone una sobrecarga. Defina subredes amplias para asegurarse de que dispone de flexibilidad para crecer.

**Procedimiento recomendado**: simplificar la administración de reglas de grupos de seguridad de red mediante la definición de [grupos de seguridad de aplicaciones](https://azure.microsoft.com/blog/applicationsecuritygroups/).  
**Detalles**: defina un grupo de seguridad de aplicaciones para las listas de direcciones IP que crea que puedan cambiar en el futuro o que vayan a usarse en varios grupos de seguridad de red. Procure dar un nombre claro a los grupos de seguridad de aplicaciones para que otros comprendan su contenido y finalidad.

## <a name="adopt-a-zero-trust-approach"></a>Adoptar un método de Confianza cero
Las redes basadas en el perímetro funcionan bajo el supuesto de que se puede confiar en todos los sistemas dentro de una red. Sin embargo, los empleados de hoy en día acceden a los recursos de la organización desde cualquier lugar en una gran variedad de dispositivos y aplicaciones, lo que hace que los controles de seguridad perimetral sean irrelevantes. Las directivas de control de acceso que se centran únicamente en quién puede acceder a un recurso no son suficientes. Para dominar el equilibrio entre seguridad y productividad, los administradores de seguridad también deben tener en cuenta el *modo* en que se accede a los recursos.

Las redes deben evolucionar de las defensas tradicionales porque pueden ser vulnerables a diversas infracciones: un atacante puede poner en peligro un único punto de conexión dentro del límite de confianza y, tras ello, expandir rápidamente un punto de apoyo en toda la red. Las redes de [Confianza cero](https://www.microsoft.com/security/blog/2018/06/14/building-zero-trust-networks-with-microsoft-365/) eliminan el concepto de confianza según la ubicación de red dentro de un perímetro. En su lugar, las arquitecturas de Confianza cero usan notificaciones de confianza de usuario y dispositivo para obtener acceso a los datos y los recursos de la organización. En las nuevas iniciativas, adopte métodos de Confianza cero que validen la confianza en el momento del acceso.

Los procedimientos recomendados son:

**Procedimiento recomendado**: conceder acceso condicional a recursos en función del dispositivo, la identidad, la garantía, la ubicación de red y otros muchos aspectos.  
**Detalles**: el [acceso condicional de Azure AD](../../active-directory/conditional-access/overview.md) permite aplicar los controles de acceso adecuados poniendo en marcha decisiones de control de acceso automatizado según las condiciones necesarias. Para más información, vea [Administración el acceso a la administración de Azure con acceso condicional](../../active-directory/conditional-access/howto-conditional-access-policy-azure-management.md).

**Procedimiento recomendado**: habilitar el acceso a los puertos solo tras la aprobación del flujo de trabajo.  
**Detalles**: puede usar el [acceso a VM Just-In-Time en Microsoft Defender for Cloud](../../security-center/security-center-just-in-time.md) para bloquear el tráfico entrante a las VM de Azure, lo que reduce la exposición a ataques al mismo tiempo que proporciona un acceso sencillo para conectarse a las máquinas virtuales cuando sea necesario.

**Procedimiento recomendado**: conceder permisos temporales para realizar tareas con privilegios, lo que impide que usuarios malintencionados o sin autorización obtengan acceso después de que el permiso haya expirado. El acceso se concede solo cuando los usuarios lo necesitan.  
**Detalles**: use el acceso Just-In-Time en Azure AD Privileged Identity Management o en una solución de terceros para conceder permisos para realizar tareas con privilegios.

Confianza cero es la próxima evolución en seguridad de red. El estado de los ataques cibernéticos condiciona a las organizaciones a adquirir una mentalidad de "presunción de infracción", pero este método no debería limitar nada. Las redes de Confianza cero protegen los recursos y los datos corporativos, al tiempo que garantizan que las organizaciones pueden crear un área de trabajo moderna mediante tecnologías que permiten a los empleados ser productivos en cualquier momento, lugar y modo.

## <a name="control-routing-behavior"></a>Control del comportamiento de enrutamiento
Cuando coloca una máquina virtual en una instancia de Azure Virtual Network, observará que la máquina virtual puede conectarse a cualquier otra máquina virtual de la misma red virtual, incluso si las otras máquinas virtuales están en subredes diferentes. Esto es posible porque hay una colección de rutas del sistema que están habilitadas de forma predeterminada y que permiten este tipo de comunicación. Estas rutas predeterminadas permiten que las máquinas virtuales de la misma red virtual inicien conexiones entre sí y con Internet (solo para comunicaciones salientes a Internet).

Si bien las rutas del sistema predeterminadas son útiles para muchos escenarios de implementación, habrá veces en las que preferirá personalizar la configuración de enrutamiento para las implementaciones. Puede configurar la dirección del próximo salto para que acceda a destinos específicos.

Igualmente le recomendamos que configure las [rutas definidas por el usuario](../../virtual-network/virtual-networks-udr-overview.md) al implementar un dispositivo de seguridad para una red virtual. Trataremos este punto más adelante en la sección dedicada a [proteger los recursos de servicio de Azure críticos únicamente para las redes virtuales](network-best-practices.md#secure-your-critical-azure-service-resources-to-only-your-virtual-networks).

> [!NOTE]
> Las rutas definidas por el usuario no son necesarias, y las rutas del sistema predeterminadas funcionan en la mayoría de lo casos.
>
>

## <a name="use-virtual-network-appliances"></a>Uso de aplicaciones de red virtual
Los grupos de seguridad de red y el enrutamiento definido por el usuario pueden proporcionar un cierto grado de seguridad de red en las capas de red y de transporte del [modelo OSI](https://en.wikipedia.org/wiki/OSI_model). Aún así, es posible que en algunas situaciones quiera o necesite habilitar la seguridad en los niveles altos de la pila. En tales situaciones, se recomienda implementar aplicaciones de seguridad de la red virtual proporcionadas por asociados de Azure.

Las aplicaciones de seguridad de la red de Azure pueden proporcionar niveles de seguridad mejorados que los que proporcionan los controles de nivel de red. Las funcionalidades de seguridad de red correspondientes a los dispositivos de seguridad de la red virtual incluyen:


* Firewalls
* Detección y prevención de intrusiones
* Administración de vulnerabilidades
* Control de aplicaciones
* Detección de anomalías basadas en la red
* Filtrado de web
* Antivirus
* Protección de redes de robots (botnets)

Para encontrar los dispositivos de seguridad de red virtual de Azure, vaya a [Azure Marketplace](https://azure.microsoft.com/marketplace/) y búsquelos mediante las palabras clave "seguridad" y "seguridad de red".

## <a name="deploy-perimeter-networks-for-security-zones"></a>Implementar redes perimetrales para las zonas de seguridad
Una [red perimetral](/azure/architecture/vdc/networking-virtual-datacenter) (también conocida como DMZ) es un segmento de red físico o lógico que está diseñado para proporcionar un nivel de seguridad adicional entre los recursos e Internet. Los dispositivos de control de acceso de red especializados que se encuentran en el borde de una red perimetral solo permiten el tráfico deseado en la red virtual.

Las redes perimetrales son útiles porque permiten centrar la administración, supervisión, registro y generación de informes sobre los dispositivos del control de acceso a la red en el borde de la instancia de Azure Virtual Network. Una red perimetral es donde se suelen habilitar la prevención de denegación de servicio distribuido (DDoS), los sistemas de detección y prevención de intrusiones (IDS/IPS), las reglas y directivas de firewall, el filtrado web, el antimalware de la red, etc. Los dispositivos de seguridad de la red se sitúan entre Internet y la instancia de Azure Virtual Network, y tienen una interfaz en ambas redes.

Aunque este es el diseño básico de una red perimetral, existen muchos diseños diferentes, como la configuración opuesta, el triple alojamiento o el múltiple alojamiento.

Según el concepto de Confianza cero mencionado anteriormente, se recomienda que considere la posibilidad de usar una red perimetral en todas las implementaciones de alta seguridad para mejorar el nivel de control de acceso y seguridad de red de los recursos de Azure. Puede usar Azure o una solución de terceros para proporcionar una capa extra de seguridad entre sus recursos e Internet:

- Controles nativos de Azure. [Azure Firewall](../../firewall/overview.md) y el [firewall de aplicaciones web de Application Gateway](../../application-gateway/features.md#web-application-firewall) ofrecen una seguridad básica, con un firewall como servicio completo, alta disponibilidad integrada, escalabilidad sin restricciones en la nube, filtrado de FQDN, compatibilidad con el conjunto de reglas básicas OWASP y una instalación y configuración sencillos.
- Ofertas de terceros. Busque en [Azure Marketplace](https://azuremarketplace.microsoft.com/) un firewall de próxima generación y otras ofertas de terceros que proporcionen herramientas de seguridad conocidas y niveles de seguridad de red notablemente mejorados. La configuración podría ser más compleja, pero una oferta de terceros podría permitirle usar los conjuntos de habilidades y capacidades existentes.

## <a name="avoid-exposure-to-the-internet-with-dedicated-wan-links"></a>Uso de vínculos WAN dedicados para evitar la exposición en Internet
Muchas organizaciones han elegido la ruta de TI híbrida. Con la TI híbrida, algunos de los recursos de información de la compañía están en Azure, mientras que otros siguen siendo locales. En muchos casos, algunos componentes de un servicio se ejecutan en Azure, mientras que otros componentes siguen siendo locales.

En un escenario de TI híbrida, suele haber algún tipo de conectividad entre locales. Esta conectividad entre locales permite a la empresa conectar sus redes locales con las redes virtuales de Azure. Hay dos soluciones de conectividad entre locales:

* [VPN de sitio a sitio](../../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md). Es una tecnología de confianza y bien establecida, pero que realiza la conexión a través de Internet. Además, el ancho de banda está limitado a un máximo de aproximadamente 1,25 Gbps. VPN de sitio a sitio es una opción conveniente en algunos escenarios.
* **Azure ExpressRoute**. se recomienda que use [ExpressRoute](../../expressroute/expressroute-introduction.md) para la conectividad entre locales. ExpressRoute le permite ampliar sus redes locales en la nube de Microsoft a través de una conexión privada que facilita un proveedor de conectividad. Mediante ExpressRoute, se pueden establecer conexiones con servicios en la nube de Microsoft, como Azure, Microsoft 365 y Dynamics 365. ExpressRoute es un vínculo de WAN dedicada entre su ubicación local o un proveedor de hospedaje de Microsoft Exchange. Al tratarse de una conexión de telecomunicaciones, los datos no viajan a través de Internet y, por tanto, no se exponen a los posibles riesgos inherentes a este tipo de comunicaciones.

La ubicación de la conexión de ExpressRoute puede afectar a la capacidad del firewall, la escalabilidad, la confiabilidad y la visibilidad del tráfico de red. Será necesario identificar dónde debe terminar ExpressRoute en las redes existentes (locales). Puede:

- Terminarlo fuera del firewall (paradigma de red perimetral) si necesita visibilidad del tráfico, si tiene que seguir realizando un procedimiento de aislamiento de los centros de datos existentes o si solamente va a colocar recursos de extranet en Azure.
- Terminarlo dentro del firewall (paradigma de extensión de red). Esta es la recomendación predeterminada. En todos los demás casos, se recomienda tratar Azure como un centro de datos más.

## <a name="optimize-uptime-and-performance"></a>Optimización del rendimiento y el tiempo de actividad
Si un servicio está inactivo, no puede accederse a la información. Si el rendimiento es tan bajo que no se pueden utilizar los datos, podemos considerar que los datos son inaccesibles. Por lo tanto, desde una perspectiva de seguridad, necesitamos hacer todo lo posible para asegurarnos de que nuestros servicios tienen un rendimiento y un tiempo de actividad óptimos.

Un método popular y eficaz para mejorar la disponibilidad y el rendimiento es usar el equilibrio de carga. El equilibrio de carga es un método para distribuir el tráfico de la red entre los servidores que forman parte de un servicio. Por ejemplo, si tiene servidores web front-end que forman parte de su servicio, puede usar el equilibrio de carga para distribuir el tráfico entre ellos.

Esta distribución del tráfico aumenta la disponibilidad, ya que si uno de los servidores web deja de estar disponible, el equilibrio de carga deja de enviarle tráfico y lo redirige a los servidores que aún están en línea. El equilibrio de carga también mejora el rendimiento, ya que la sobrecarga del procesador, la red y la memoria para atender a las solicitudes se distribuye entre todos los servidores con carga equilibrada.

Se recomienda usar el equilibrio de carga siempre que se pueda y, según sea adecuado para los servicios. Estos son los escenarios en el nivel de Azure Virtual Network y el nivel global, junto con las opciones de equilibrio de carga para cada uno.

**Escenario**: ahora tiene una aplicación que:

- Requiere solicitudes de la misma sesión de usuario o cliente para llegar a la misma máquina virtual de back-end. Ejemplos de esto serían las aplicaciones del carro de la compra y los servidores de correo web.
- Como solo acepta una conexión segura, la comunicación sin cifrar con los servidores no es una opción aceptable.
- Es necesario que varias solicitudes HTTP en la misma conexión TCP de ejecución prolongada se enruten a servidores de back-end diferentes o su carga se equilibre entre estos.

**Opción de equilibrio de carga**: use [Azure Application Gateway](../../application-gateway/overview.md), que es un equilibrador de carga del tráfico de web HTTP. Application Gateway admite el cifrado TLS de un extremo a otro y la [terminación TLS](../../application-gateway/overview.md) en la puerta de enlace. A continuación, los servidores web pueden librarse de la sobrecarga de cifrado y descifrado y del tráfico que fluye sin encriptar a los servidores de back-end.

**Escenario**: es necesario equilibrar la carga de las conexiones entrantes de Internet entre los servidores situados en una instancia de Azure Virtual Network. Los escenarios surgen cuando:

- Tiene aplicaciones sin estado que acepten solicitudes entrantes de Internet.
- No se requieren sesiones permanentes o descargas de TLS. Las sesiones temporales son un método que se usa con el equilibrio de carga de la aplicación, para lograr la afinidad del servidor.

**Opción de equilibrio de carga**: use Azure Portal para [crear un equilibrador de carga externo](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que distribuya las solicitudes entrantes entre varias máquinas virtuales para proporcionar un mayor nivel de disponibilidad.

**Escenario**: tendrá que equilibrar la carga de las conexiones de las máquinas virtuales que no estén en Internet. En la mayoría de los casos, los dispositivos se encargan de iniciar en una instancia de Azure Virtual Network las conexiones que se aceptan para el equilibrio de carga, como instancias de SQL Server o servidores web internos.   
**Opción de equilibrio de carga**: use Azure Portal para [crear un equilibrador de carga interno](../../load-balancer/quickstart-load-balancer-standard-public-portal.md) que distribuya las solicitudes entrantes entre varias máquinas virtuales para proporcionar un mayor nivel de disponibilidad.

**Escenario**: necesita conseguir un equilibrio de carga global ya que:

- Tiene una solución en la nube que se distribuye ampliamente en varias regiones y requiere el nivel más alto de tiempo de actividad (o disponibilidad) posible.
- Necesita el nivel más alto de tiempo de actividad para asegurarse de que el servicio está disponible incluso si todo un centro de datos deja de funcionar.

**Opción de equilibrio de carga**: use Azure Traffic Manager. Traffic Manager le permite equilibrar la carga de las conexiones a los servicios, en función de la ubicación del usuario.

Por ejemplo, si el usuario realiza una solicitud a su servicio desde la Unión Europea, la conexión se dirige a los servicios situados en un centro de datos de la Unión Europea. Esta parte del equilibrio de carga global del Administrador de tráfico ayuda a mejorar el rendimiento, ya que la conexión al centro de datos más cercano es más rápida que a los centros de datos que están lejos.

## <a name="disable-rdpssh-access-to-virtual-machines"></a>Deshabilitar el acceso RDP/SSH a las máquinas virtuales
Es posible acceder a Azure Virtual Machines mediante [Escritorio remoto](https://en.wikipedia.org/wiki/Remote_Desktop_Protocol) (RDP) y el protocolo [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell) (SSH). Estos protocolos le permiten administrar máquinas virtuales desde ubicaciones remotas y son los protocolos estándar que se usan en el procesamiento de los centros de datos.

El posible problema de seguridad al usar estos protocolos a través de Internet, es que los atacantes pueden utilizar diversas técnicas [de fuerza bruta](https://en.wikipedia.org/wiki/Brute-force_attack) para obtener acceso a Azure Virtual Machines. Una vez que los atacantes obtienen acceso, pueden utilizar la máquina virtual como punto de inicio para poner en peligro otros equipos de la red virtual o incluso atacar dispositivos en red fuera de Azure.

Por este motivo, se recomienda deshabilitar el acceso directo de RDP y SSH a Azure Virtual Machines desde Internet. Cuando se deshabilita el acceso directo de RDP y SSH desde Internet, tiene otras opciones que puede utilizar para acceder a estas máquinas virtuales y así administrarlas de forma remota.

**Escenario**: habilite una conexión de un solo usuario a una instancia de Azure Virtual Network a través de Internet.   
**Opción**: [VPN de punto a sitio](../../vpn-gateway/vpn-gateway-howto-point-to-site-classic-azure-portal.md) es otro término para una conexión cliente/servidor de VPN con acceso remoto. Una vez establecida la conexión de punto a sitio, el usuario podrá usar RDP o SSH para conectarse a cualquier máquina virtual situada en la instancia de Azure Virtual Network a la que el usuario se conectó mediante la VPN de punto a sitio. Con esto, se supone que el usuario tiene permiso para obtener acceso a dichas máquinas virtuales.

La VPN de punto a sitio es más segura que las conexiones de RDP o SSH directas, ya que el usuario tiene que autenticarse dos veces antes de conectarse a una máquina virtual. En primer lugar, el usuario debe autenticarse (y ser autorizado) para poder establecer la conexión VPN de punto a sitio. En segundo lugar, el usuario debe autenticarse (y ser autorizado) para poder establecer la sesión RDP o SSH.

**Escenario**: permitir que los usuarios de la red local se conecten a máquinas virtuales en Azure Virtual Network.   
**Opción**: una [VPN de sitio a sitio](../../vpn-gateway/vpn-gateway-howto-site-to-site-classic-portal.md) conecta toda una red a otra a través de Internet. Puede usar una VPN de sitio a sitio para conectar su red local a una instancia de Azure Virtual Network. Los usuarios de su red local se pueden conectar mediante el protocolo RDP o SSH, a través de la conexión VPN de sitio a sitio. No debe permitir el acceso directo de RDP o SSH a través de Internet.

**Escenario**: use un vínculo WAN dedicado para ofrecer una funcionalidad similar a la VPN de sitio a sitio.   
**Opción**: use [ExpressRoute](https://azure.microsoft.com/documentation/services/expressroute/). Proporciona funcionalidades similares a la VPN de sitio a sitio. Las principales diferencias son:

- El vínculo WAN dedicado no recorre Internet.
- Los vínculos WAN dedicados suelen ser más estables y eficaces.

## <a name="secure-your-critical-azure-service-resources-to-only-your-virtual-networks"></a>Proteja los recursos de servicio de Azure críticos únicamente en las redes virtuales
Use Azure Private Link para acceder a los servicios PaaS de Azure (por ejemplo, Azure Storage y SQL Database) y a través de un punto de conexión privado de la red virtual. Los puntos de conexión privados permiten proteger los recursos de servicio de Azure críticos únicamente para las redes virtuales. El tráfico desde la red virtual al servicio de Azure siempre permanece en la red troncal de Microsoft Azure. La exposición de la red virtual a la red pública de Internet ya no es necesaria para consumir los servicios PaaS de Azure. 

Azure Private Link proporciona las ventajas siguientes:
- **Seguridad mejorada para los recursos de servicio de Azure**: Con Azure Private Link, se pueden proteger los recursos del servicio de Azure para la red virtual mediante puntos de conexión privados. Si protege los recursos del servicio para un punto de conexión privado en una red virtual, mejorará la seguridad al quitar totalmente el acceso público a través de Internet a estos recursos y al permitir el tráfico solo desde el punto de conexión privado.
- **Acceso privado a recursos de servicios de Azure en la plataforma Azure**: Conecte la red virtual a los servicios de Azure con puntos de conexión privados. No es necesario tener una dirección IP pública. La plataforma Private Link administrará la conectividad entre el consumidor y los servicios a través de la red troncal de Azure.
- **Acceso desde redes locales y emparejadas**: acceda a los servicios que se ejecutan en Azure desde el entorno local a través del emparejamiento privado de ExpressRoute, los túneles VPN y las redes virtuales emparejadas mediante puntos de conexión privados. No es necesario configurar el emparejamiento de Microsoft para ExpressRoute ni atravesar Internet para llegar hasta el servicio. Private Link proporciona una manera segura de migrar cargas de trabajo a Azure.
- **Protección contra la pérdida de datos**: un punto de conexión privado se asigna a una instancia de un recurso de PaaS en lugar de al servicio entero. Los consumidores solo pueden conectarse al recurso específico. Se bloquea el acceso a cualquier otro recurso del servicio. Este mecanismo proporciona protección contra los riesgos de pérdida de datos.
- **Alcance global**: conéctese de forma privada a los servicios que se ejecutan en otras regiones. La red virtual del consumidor puede estar en la región A y puede conectarse a los servicios de la región B.
- **Sencillo de configurar y administrar**: ya no necesita direcciones IP públicas y reservadas en sus redes virtuales para proteger los recursos de Azure a través de una dirección IP del firewall. No hay ningún dispositivo NAT o de puerta de enlace necesario para configurar los puntos de conexión privados. Los puntos de conexión privados se pueden configurar a través de un flujo de trabajo sencillo. En el lado del servicio, también puede administrar las solicitudes de conexión en el recurso del servicio de Azure con facilidad. Azure Private Link funciona también para los consumidores y los servicios que pertenecen a distintos inquilinos de Azure Active Directory. 
    
Para obtener más información sobre los puntos de conexión privados y sobre los servicios de Azure y las regiones para las que están disponibles los puntos de conexión privados, consulte [Azure Private Link](../../private-link/private-link-overview.md).


## <a name="next-steps"></a>Pasos siguientes
Consulte [Patrones y procedimientos recomendados de seguridad en Azure](best-practices-and-patterns.md) para obtener más procedimientos recomendados de seguridad que pueda aplicar cuando diseñe, implemente y administre las soluciones en la nube mediante Azure.
