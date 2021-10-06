---
title: 'Configuración de una red virtual: instancia de Azure Cache for Redis de nivel prémium'
description: Obtenga información sobre cómo crear y administrar la compatibilidad de red virtual para su instancia de Azure Cache for Redis de nivel prémium.
author: curib
ms.author: cauribeg
ms.service: cache
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: 0e231920a7aee35edd7a5913987ac37f0902b5fe
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129538147"
---
# <a name="configure-virtual-network-support-for-a-premium-azure-cache-for-redis-instance"></a>Configuración de la compatibilidad de una red virtual para una instancia de Azure Cache for Redis de nivel prémium

La implementación de [Azure Virtual Network](https://azure.microsoft.com/services/virtual-network/) aporta mayor seguridad y aislamiento, junto con: subredes, directivas de control de acceso y otras características que restringen más el acceso. Si una instancia de Azure Cache for Redis se configura con una red virtual, no se puede acceder a ella de forma pública, solo desde máquinas virtuales y aplicaciones que se encuentren dentro de la red virtual. En este artículo se describe cómo configurar la compatibilidad con redes virtuales de una instancia de Azure Cache for Redis de nivel prémium.

> [!NOTE]
> Azure Cache for Redis admite tanto el modelo de implementación clásica como las redes virtuales de Azure Resource Manager.
>

> [!IMPORTANT]
> Azure Cache for Redis ahora admite Azure Private Link, lo que simplifica la arquitectura de red y protege la conexión entre los puntos de conexión en Azure. Puede conectarse a una instancia de Azure Cache desde la red virtual a través de un punto de conexión privado, al que se le asigna una dirección IP privada en una subred dentro de la red virtual. Azure Private Links se ofrece en todos nuestros niveles, incluye compatibilidad con Azure Policy y administración simplificada de reglas de NSG. Para más información, consulte [Documentación de Private Link](cache-private-link.md). Para migrar a Private Link las cachés con inserción de red virtual, consulte [aquí](cache-vnet-migration.md).
>

## <a name="set-up-virtual-network-support"></a>Configuración de la compatibilidad con redes virtuales

La compatibilidad con redes virtuales se configura en el panel **New Azure Cache for Redis** (Nueva instancia de Azure Cache for Redis) durante la creación de la memoria caché.

1. Para crear una instancia de caché de nivel prémium, inicie sesión en [Azure Portal](https://portal.azure.com) y seleccione **Crear un recurso**.  También se puede crear mediante las plantillas de Resource Manager, PowerShell o la CLI de Azure. Para más información sobre cómo crear una instancia de Azure Cache for Redis, consulte [Creación de una caché](cache-dotnet-how-to-use-azure-redis-cache.md#create-a-cache).

    :::image type="content" source="media/cache-private-link/1-create-resource.png" alt-text="Captura de pantalla se muestra la opción Crear un recurso.":::

1. En la página **Nuevo**, seleccione **Bases de datos**. Después, seleccione **Azure Cache for Redis**.

    :::image type="content" source="media/cache-private-link/2-select-cache.png" alt-text="Captura de pantalla que muestra la selección de Azure Cache for Redis.":::

1. En la página **Nueva caché en Redis**, configure las opciones de la nueva instancia de caché de nivel prémium.

   | Configuración      | Valor sugerido  | Descripción |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Nombre DNS** | Escriba un nombre único global. | El nombre de la memoria caché debe ser una cadena de entre 1 y 63 caracteres, y solo puede contener números, letras o guiones. El nombre debe comenzar y terminar por un número o una letra y no puede contener guiones consecutivos. El *nombre de host* de la instancia de caché será *\<DNS name>.redis.cache.windows.net*. |
   | **Suscripción** | Seleccione la suscripción en la lista desplegable. | La suscripción en la que se creará esta nueva instancia de Azure Cache for Redis. |
   | **Grupos de recursos** | Seleccione un grupo de recursos en la lista desplegable o seleccione **Crear nuevo** y escriba un nombre nuevo para el grupo de recursos. | El nombre del grupo de recursos en el que se van a crear la caché y otros recursos. Al colocar todos los recursos de la aplicación en un grupo de recursos, puede administrarlos o eliminarlos fácilmente. |
   | **Ubicación** | seleccione una ubicación en la lista desplegable. | Seleccione una [región](https://azure.microsoft.com/regions/) cerca de otros servicios que vayan a usar la memoria caché. |
   | **Tipo de caché** |Seleccione una instancia de caché de nivel prémium en la lista desplegable para configurar las características de dicho nivel. Para más información, consulte [Precios de Azure Cache for Redis](https://azure.microsoft.com/pricing/details/cache/). |  El plan de tarifa determina el tamaño, el rendimiento y las características disponibles para la memoria caché. Para más información, consulte la [introducción a Azure Cache for Redis](cache-overview.md). |

1. Seleccione la pestaña **Redes** o elija el botón **Redes** situado en la parte inferior de la página.

1. En la pestaña **Redes**, seleccione **Redes virtuales** como método de conectividad. Para usar una nueva red virtual, primero es preciso crearla, para lo que debe seguir los pasos de [Creación de una red virtual mediante Azure Portal](../virtual-network/manage-virtual-network.md#create-a-virtual-network) o [Creación de Virtual Network (clásica) mediante Azure Portal](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal). Después, vuelva al panel **Nuevo - Azure Cache for Redis** para crear y configurar una instancia de caché de nivel prémium.

   > [!IMPORTANT]
   > Si implementa una instancia de Azure Cache for Redis en una red virtual de Resource Manager, la memoria caché debe estar en una subred dedicada que no contenga otros recursos, salvo instancias de Azure Cache for Redis. Si se intenta implementar una instancia de Azure Cache for Redis en una subred de una red virtual de Resource Manager que contiene otros recursos, o bien que tiene una instancia de NAT Gateway asignada, se produce un error en la implementación.
   >
   >

   | Configuración      | Valor sugerido  | Descripción |
   | ------------ |  ------- | -------------------------------------------------- |
   | **Red virtual** | Seleccione la red virtual en la lista desplegable. | Seleccione una red virtual que esté en la misma suscripción y la misma ubicación que la memoria caché. |
   | **Subred** | Seleccione la subred en la lista desplegable. | Se debe usar la notación CIDR para el intervalo de direcciones de la subred (por ejemplo, 192.168.1.0/24). Debe incluirse en el espacio de direcciones de la red virtual. |
   | **Dirección IP estática** | (Opcional) Escriba una dirección IP estática. | Si no especifica una dirección IP estática, se elegirá una dirección IP automáticamente. |

   > [!IMPORTANT]
   > Azure reserva algunas direcciones IP dentro de cada subred y estas direcciones no se pueden usar. La primera y la última dirección IP de las subredes están reservadas para la conformidad con el protocolo, junto con otras tres direcciones usadas para los servicios de Azure. Para más información, consulte [¿Hay alguna restricción en el uso de direcciones IP dentro de estas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)
   >
   > Además de las direcciones IP que usa la infraestructura de red virtual de Azure, cada instancia de Azure Cache for Redis de la subred usa dos direcciones IP por partición y una dirección IP adicional para el equilibrador de carga. Se considera que una instancia de caché que no está en clúster tiene una partición.
   >

1. Seleccione el botón **Siguiente: Opciones avanzadas** o haga clic en el botón **Siguiente: Opciones avanzadas** situado en la parte inferior de la página.

1. En la pestaña **Opciones avanzadas** de la instancia de caché de nivel prémium, configure el puerto no TLS, la agrupación en clústeres y la persistencia de datos.

1. Seleccione el botón **Siguiente: Opciones avanzadas** o elija el botón **Siguiente: Etiquetas** situado en la parte inferior de la página.

1. Opcionalmente, en la pestaña **Etiquetas**, escriba el nombre y el valor si quiere clasificar el recurso.

1. Seleccione **Revisar + crear**. Pasará a la pestaña **Revisar y crear**, donde Azure valida la configuración.

1. Tras aparecer el mensaje verde **Validación superada**, seleccione **Crear**.

La caché tarda un tiempo en crearse. Puede supervisar el progreso en la página **Información general** de Azure Cache for Redis. Cuando **Estado** se muestra como **En ejecución**, la memoria caché está lista para su uso. Una vez que se crea la memoria caché, para ver la configuración de la red virtual es preciso seleccionar **Virtual Network** en el menú **Recursos**.

![Virtual network][redis-cache-vnet-info]

Para conectarse a la instancia de Azure Cache for Redis cuando usa una red virtual, especifique el nombre de host de la memoria caché en la cadena de conexión, como se muestra en el ejemplo siguiente:

```csharp
private static Lazy<ConnectionMultiplexer>
    lazyConnection = new Lazy<ConnectionMultiplexer> (() =>
    {
        return ConnectionMultiplexer.Connect("contoso5premium.redis.cache.windows.net,abortConnect=false,ssl=true,password=password");
    });

public static ConnectionMultiplexer Connection
{
    get
    {
        return lazyConnection.Value;
    }
}
```

## <a name="azure-cache-for-redis-virtual-network-faq"></a>Preguntas más frecuentes sobre redes virtuales de Azure Cache for Redis

La lista siguiente contiene las respuestas a las preguntas más frecuentes sobre el escalado de Azure Cache for Redis.

* ¿Cuáles son algunos de las incidencias comunes de configuración incorrecta con Azure Cache for Redis y las redes virtuales?
* [¿Cómo se puede comprobar que la memoria caché funciona en una red virtual?](#how-can-i-verify-that-my-cache-is-working-in-a-virtual-network)
* Al intentar conectarme a la instancia de Azure Cache for Redis en una red virtual, ¿por qué aparece un error que indica que el certificado remoto no es válido?
* [¿Se pueden usar redes virtuales con una memoria caché básica o estándar?](#can-i-use-virtual-networks-with-a-standard-or-basic-cache)
* ¿Por qué se produce un error al crear una instancia de Azure Cache for Redis en algunas subredes, pero no en otras?
* [¿Cuáles son los requisitos de espacio de direcciones de subred?](#what-are-the-subnet-address-space-requirements)
* [¿Puedo conectarme a mi caché desde una red virtual emparejada?](#can-i-connect-to-my-cache-from-a-peered-virtual-network)
* [¿Funcionan todas las características de caché cuando una memoria caché se hospeda en una red virtual?](#do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network)

### <a name="what-are-some-common-misconfiguration-issues-with-azure-cache-for-redis-and-virtual-networks"></a>¿Cuáles son algunos de las incidencias comunes de configuración incorrecta con Azure Cache for Redis y las redes virtuales?

Cuando Azure Cache for Redis se hospeda en una red virtual, se usan los puertos de la tabla siguiente.

>[!IMPORTANT]
>Si los puertos de las siguientes tablas están bloqueados, la memoria caché podría no funcionar correctamente. Tener bloqueados uno o varios de estos puertos es la incidencia más común de una configuración incorrecta cuando se utiliza Azure Cache for Redis en una red virtual.
>

* [Requisitos de puerto de salida](#outbound-port-requirements)
* [Requisitos de puerto de entrada](#inbound-port-requirements)

#### <a name="outbound-port-requirements"></a>Requisitos de puerto de salida

Existen nueve requisitos de puerto de salida. Las solicitudes que salen de estos rangos son: a) de salida a otros servicios necesarios para que la memoria caché funcione o b) internas en la subred de Redis para la comunicación entre nodos. En el caso de la replicación geográfica, hay otros requisitos de salida para la comunicación entre subredes tanto de la caché principal como de la de réplica.

| Puertos | Dirección | Protocolo de transporte | Propósito | IP local | Dirección IP remota |
| --- | --- | --- | --- | --- | --- |
| 80, 443 |Salida |TCP |Dependencias de Redis en Azure Storage/PKI (Internet) | (Subred de Redis) |* <sup>4</sup> |
| 443 | Salida | TCP | Dependencia de Redis en Azure Key Vault y Azure Monitor | (Subred de Redis) | AzureKeyVault, AzureMonitor <sup>1</sup> |
| 53 |Salida |TCP/UDP |Dependencias de Redis en DNS (Internet y red virtual) | (Subred de Redis) | 168.63.129.16 y 169.254.169.254 <sup>2</sup> y cualquier servidor DNS personalizado para la subred <sup>3</sup> |
| 8443 |Salida |TCP |Comunicaciones internas en Redis | (Subred de Redis) | (Subred de Redis) |
| 10221-10231 |Salida |TCP |Comunicaciones internas en Redis | (Subred de Redis) | (Subred de Redis) |
| 20226 |Salida |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis) |
| 13000-13999 |Salida |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis) |
| 15000-15999 |Salida |TCP |Comunicaciones internas de Redis y replicación geográfica | (Subred de Redis) |(Subred de Redis) (Subred del mismo nivel de réplica geográfica) |
| 6379-6380 |Salida |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis) |

<sup>1</sup> Puede usar las etiquetas de servicio AzureKeyVault y AzureMonitor con los grupos de seguridad de red de Resource Manager (NSG).

<sup>2</sup> Estas direcciones IP que pertenecen a Microsoft se usan para dirigir la máquina virtual del host que trabaja con Azure DNS.

<sup>3</sup> Esta información no es necesaria para aquellas subredes sin ningún servidor DNS personalizado o cachés en Redis que omiten los DNS personalizados.

<sup>4</sup> Para más información, consulte [Requisitos adicionales de conectividad de red virtual](#additional-virtual-network-connectivity-requirements).

#### <a name="geo-replication-peer-port-requirements"></a>Requisitos de los puertos del mismo nivel en la replicación geográfica

Si usa la replicación geográfica entre memorias caché en redes virtuales de Azure: a) desbloquee los puertos 15000-15999 para toda la subred en las direcciones de entrada *y* salida, y b) a ambas memorias caché. Con esta configuración, todos los componentes de réplica de la subred pueden comunicarse directamente entre sí, incluso si hay una conmutación por error geográfica futura.

#### <a name="inbound-port-requirements"></a>Requisitos de puerto de entrada

Existen ocho requisitos de intervalo de puertos de entrada. Las solicitudes entrantes de estos rangos provienen de otros servicios hospedados en la misma red virtual. O bien, son internas de las comunicaciones de subred de Redis.

| Puertos | Dirección | Protocolo de transporte | Propósito | IP local | Dirección IP remota |
| --- | --- | --- | --- | --- | --- |
| 6379, 6380 |Entrada |TCP |Comunicación del cliente con Redis, equilibrio de carga de Azure | (Subred de Redis) | (Subred de Redis), (subred de cliente), AzureLoadBalancer <sup>1</sup> |
| 8443 |Entrada |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis) |
| 8500 |Entrada |TCP/UDP |Equilibrio de carga de Azure | (Subred de Redis) | AzureLoadBalancer |
| 10221-10231 |Entrada |TCP |Comunicación de cliente con clústeres de Redis, comunicaciones internas para Redis | (Subred de Redis) |(Subred de Redis), AzureLoadBalancer, (subred de cliente) |
| 13000-13999 |Entrada |TCP |Comunicación del cliente con clústeres de Redis, Equilibrio de carga de Azure | (Subred de Redis) | (Subred de Redis), (subred de cliente), AzureLoadBalancer |
| 15000-15999 |Entrada |TCP |Comunicación del cliente con los clústeres de Redis, el equilibrio de carga de Azure y la replicación geográfica | (Subred de Redis) | (Subred de Redis), (subred de cliente), AzureLoadBalancer, (subred del mismo nivel con replicación geográfica) |
| 16001 |Entrada |TCP/UDP |Equilibrio de carga de Azure | (Subred de Redis) | AzureLoadBalancer |
| 20226 |Entrada |TCP |Comunicaciones internas en Redis | (Subred de Redis) |(Subred de Redis) |

<sup>1</sup> Puede usar la etiqueta de servicio AzureLoadBalancer para Resource Manager o AZURE_LOADBALANCER para el modelo de implementación clásica para crear las reglas del grupo de seguridad de red.

#### <a name="additional-virtual-network-connectivity-requirements"></a>Requisitos adicionales de conectividad de red virtual

Existen requisitos de conectividad de red para entornos para una instancia de Azure Cache for Redis que podrían no cumplirse inicialmente en una red virtual. Azure Cache for Redis requiere todos los elementos siguientes para funcionar correctamente cuando se usa en una red virtual:

* Conectividad de red saliente a los puntos de conexión de Azure Storage en todo el mundo. Se incluyen los puntos de conexión ubicados en la misma región que la instancia de Azure Cache for Redis, así como los puntos de conexión de almacenamiento ubicados en *otras* regiones de Azure. Los puntos de conexión de Azure Storage se resuelven en los siguientes dominios de DNS: *table.core.windows.net*, *blob.core.windows.net*, *queue.core.windows.net* y *file.core.windows.net*.
* Conectividad de red saliente para *ocsp.digicert.com*, *crl4.digicert.com*, *ocsp.msocsp.com*, *mscrl.microsoft.com*, *crl3.digicert.com*, *cacerts.digicert.com*, *oneocsp.microsoft.com* y *crl.microsoft.com*. Esta conectividad es necesaria para admitir la funcionalidad TLS/SSL.
* La configuración de DNS de la red virtual debe poder resolver todos los puntos de conexión y dominios mencionados en los puntos anteriores. Se pueden cumplir los requisitos de DNS al asegurar que se configura y se mantiene una infraestructura DNS válida para la red virtual.
* Conectividad de red saliente con los siguientes puntos de conexión de Azure Monitor, que se resuelven en los siguientes dominios DNS: *shoebox2-black.shoebox2.metrics.nsatc.net*, *north-prod2.prod2.metrics.nsatc.net*, *azglobal-black.azglobal.metrics.nsatc.net*, *shoebox2-red.shoebox2.metrics.nsatc.net*, *east-prod2.prod2.metrics.nsatc.net*, *azglobal-red.azglobal.metrics.nsatc.net*, *shoebox3.prod.microsoftmetrics.com*, *shoebox3-red.prod.microsoftmetrics.com* y *shoebox3-black.prod.microsoftmetrics.com*, *azredis-red.prod.microsoftmetrics.com* y *azredis-black.prod.microsoftmetrics.com*.

### <a name="how-can-i-verify-that-my-cache-is-working-in-a-virtual-network"></a>¿Cómo se puede comprobar que la memoria caché funciona en una red virtual?

>[!IMPORTANT]
>Cuando se conecta a una instancia de Azure Cache for Redis que se hospeda en una red virtual, los clientes de caché deben estar en la misma red virtual o en una red virtual con el emparejamiento de red virtual habilitado dentro de la misma región de Azure. El emparejamiento de red virtual global no se admite actualmente. Este requisito se aplica a cualquier aplicación de prueba o herramienta de diagnóstico con la que se hace ping. Independientemente de dónde se hospede la aplicación cliente, los grupos de seguridad de red u otras capas de red deben configurarse de modo que el tráfico de red del cliente pueda llegar a la instancia de Azure Cache for Redis.
>

Después de configurar los requisitos de puerto tal como se describe en la sección anterior, puede comprobar que la memoria caché funciona siguiendo estos pasos:

* [Reinicie](cache-administration.md#reboot) todos los nodos de la caché. La memoria caché no se podrá reiniciar correctamente si no se pueden alcanzar todas las dependencias de caché necesarias (como se documenta en [Requisitos del puerto de entrada](cache-how-to-premium-vnet.md#inbound-port-requirements) y [Requisitos de puerto de salida](cache-how-to-premium-vnet.md#outbound-port-requirements)).
* Después de que se hayan reiniciado los nodos de la memoria caché, como indica el estado de la memoria caché en Azure Portal, puede realizar las siguientes pruebas:
  + Hacer ping en el punto de conexión de caché con el puerto 6380 desde una máquina que esté dentro de la misma red virtual como la memoria caché, con [tcping](https://www.elifulkerson.com/projects/tcping.php). Por ejemplo:

    `tcping.exe contosocache.redis.cache.windows.net 6380`

    Si la herramienta `tcping` informa de que el puerto está abierto, la memoria caché está disponible para su conexión en los clientes de la red virtual.

  + Otra forma de realizar la prueba: cree un cliente de caché de prueba que se conecte a la caché y que, después, agregue y recupere algunos elementos de la caché. El cliente de caché de prueba puede ser una aplicación de consola que use StackExchange.Redis. Instale la aplicación cliente de ejemplo en una máquina virtual que se encuentra en la misma red virtual que la memoria caché. A continuación, ejecútela para comprobar la conectividad con la caché.

### <a name="when-i-try-to-connect-to-my-azure-cache-for-redis-instance-in-a-virtual-network-why-do-i-get-an-error-stating-the-remote-certificate-is-invalid"></a>Al intentar conectarme a la instancia de Azure Cache for Redis en una red virtual, ¿por qué aparece un error que indica que el certificado remoto no es válido?

Cuando intente conectarse a una instancia de Azure Cache for Redis en una red virtual, verá un error de validación de certificado como este:

`{"No connection is available to service this operation: SET mykey; The remote certificate is invalid according to the validation procedure.; …"}`

La causa podría ser que se está conectando al host mediante la dirección IP. Se recomienda usar el nombre de host. En otras palabras, use la siguiente cadena:

`[mycachename].redis.windows.net:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Evite el uso de la dirección IP similar a la cadena de conexión siguiente:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False`

Si no puede resolver el nombre DNS, algunas bibliotecas de cliente incluyen opciones de configuración como `sslHost`, que proporciona el cliente StackExchange.Redis. Esta opción le permite invalidar el nombre de host utilizado para la validación del certificado. Por ejemplo:

`10.128.2.84:6380,password=xxxxxxxxxxxxxxxxxxxx,ssl=True,abortConnect=False;sslHost=[mycachename].redis.windows.net`

### <a name="can-i-use-virtual-networks-with-a-standard-or-basic-cache"></a>¿Se pueden usar redes virtuales con una memoria caché básica o estándar?

Las redes virtuales solo se pueden usar con memorias caché de nivel prémium.

### <a name="why-does-creating-an-azure-cache-for-redis-instance-fail-in-some-subnets-but-not-others"></a>¿Por qué se produce un error al crear una instancia de Azure Cache for Redis en algunas subredes, pero no en otras?

Si se implementa una instancia de Azure Cache for Redis en una red virtual, la memoria caché debe estar en una subred dedicada que no contenga ningún otro tipo de recursos. Si se intenta implementar una instancia de Azure Cache for Redis en una subred de red virtual de Resource Manager que contiene otros recursos (como instancias de Azure Application Gateway y NAT de salida), lo habitual es que se produzca un error en la implementación. Antes de crear una instancia de Azure Cache for Redis, elimine los recursos existentes de otros tipos.

También debe tener suficientes direcciones IP disponibles en la subred.

### <a name="what-are-the-subnet-address-space-requirements"></a>¿Cuáles son los requisitos de espacio de direcciones de subred?

Azure reserva algunas direcciones IP dentro de cada subred y estas direcciones no se pueden usar. La primera y la última dirección IP de las subredes están reservadas para la conformidad con el protocolo, junto con otras tres direcciones usadas para los servicios de Azure. Para más información, consulte [¿Hay alguna restricción en el uso de direcciones IP dentro de estas subredes?](../virtual-network/virtual-networks-faq.md#are-there-any-restrictions-on-using-ip-addresses-within-these-subnets)

Además de las direcciones IP que usa la infraestructura de red virtual de Azure, cada instancia de Azure Cache for Redis de la subred usa dos direcciones IP por partición de clúster, además de direcciones IP adicionales para réplicas adicionales, en caso de que las haya. Se usa una dirección IP adicional para el equilibrador de carga. Se considera que una caché no en clúster tiene una partición.

### <a name="can-i-connect-to-my-cache-from-a-peered-virtual-network"></a>¿Puedo conectarme a mi caché desde una red virtual emparejada?

Si las redes virtuales están en la misma región, puede conectarlas mediante un emparejamiento de la red virtual o una conexión de red virtual a red virtual de VPN Gateway.

Si las redes virtuales de Azure emparejadas se encuentran en regiones *diferentes*: una máquina virtual cliente de la región 1 no puede acceder a la caché de la región 2 a través de su dirección IP con equilibrio de carga debido a una restricción con equilibradores de carga básicos. Es decir, a menos que sea una caché con un equilibrador de carga estándar, que actualmente es solo una caché que se creó con *zonas de disponibilidad*.

Para más información sobre las restricciones del emparejamiento de red virtual, consulte Red virtual - Emparejamiento - Requisitos y restricciones. Una solución es usar una conexión de red virtual a red virtual VPN Gateway, en lugar del emparejamiento de red virtual.

### <a name="do-all-cache-features-work-when-a-cache-is-hosted-in-a-virtual-network"></a>¿Funcionan todas las características de caché cuando una memoria caché se hospeda en una red virtual?

Cuando la memoria caché forma parte de una red virtual, solo los clientes de la red virtual pueden tener acceso a la memoria caché. Como resultado, las siguientes características de administración de la memoria caché no funcionan en este momento:

* **Consola de Redis**: como la consola de Redis se ejecuta en el explorador local (normalmente en una máquina de desarrollador que no está conectada a la red virtual) no se puede conectar a la caché.

## <a name="use-expressroute-with-azure-cache-for-redis"></a>Uso de ExpressRoute con Azure Cache for Redis

Los clientes pueden conectar un circuito de [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) a su infraestructura de red virtual. De esta manera, amplían su red local a Azure.

De forma predeterminada, los circuitos ExpressRoute recién creados no realizan la tunelización forzada (anuncio de una ruta predeterminada, 0.0.0.0/0) en una red virtual. Como resultado, se permite la conectividad de salida a Internet directamente desde la red virtual. Las aplicaciones cliente pueden conectarse a otros puntos de conexión de Azure, entre los que se incluye una instancia de Azure Cache for Redis.

Una configuración de cliente común consiste en usar la tunelización forzada (anunciar una ruta predeterminada) que obliga a que el tráfico de salida de Internet fluya a nivel local. Este flujo de tráfico interrumpe la conectividad con Azure Cache for Redis si el tráfico de salida se bloquea en el entorno local debido a que la instancia de Azure Cache for Redis no se puede comunicar con sus dependencias.

La solución es definir una, o varias, rutas definidas por el usuario (UDR) en la subred que contiene la instancia de Azure Cache for Redis. Una ruta definida por el usuario define las rutas de subred específica que se respetarán en lugar de la ruta predeterminada.

Si es posible, use la siguiente configuración:

* La configuración de ExpressRoute anuncia 0.0.0.0/0 y, de manera predeterminada, fuerza la tunelización de todo el tráfico saliente a local.
* El UDR aplicado a la subred que contiene la instancia de Azure Cache for Redis define 0.0.0.0/0 con una ruta de trabajo para tráfico TCP/IP a Internet. Por ejemplo, establece el tipo de próximo salto en *Internet*.

El efecto combinado de estos pasos es que el UDR de nivel de subred tiene prioridad sobre la tunelización forzada de ExpressRoute y eso garantiza el acceso saliente a Internet desde la instancia de Azure Cache for Redis.

La conexión a una memoria caché de Azure Cache for Redis desde una aplicación local mediante ExpressRoute no es un escenario de uso típico debido a razones de rendimiento. Para obtener el mejor rendimiento, los clientes de Azure Cache for Redis deben estar en la misma región que la instancia de Azure Cache for Redis.

>[!IMPORTANT]
>Las rutas definidas en una UDR *deben* ser lo suficientemente específicas para que tengan prioridad sobre cualquier otra ruta anunciada por la configuración de ExpressRoute. En el ejemplo siguiente se usa el intervalo de direcciones 0.0.0.0/0 amplio y, como tal, puede reemplazarse accidentalmente por los anuncios de ruta con intervalos de direcciones más específicos.

>[!WARNING]
>Azure Cache for Redis no es compatible con las configuraciones de ExpressRoute que *anuncian incorrectamente rutas entre la ruta de acceso de emparejamiento público y la ruta de acceso de emparejamiento privado*. Las configuraciones de ExpressRoute con el emparejamiento público configurado reciben anuncios de ruta de Microsoft para un amplio conjunto de intervalos de direcciones IP de Microsoft Azure. Si estos intervalos de direcciones se anuncian incorrectamente en la ruta de acceso de emparejamiento privado, el resultado es que se forzará incorrectamente la tunelización de todos los paquetes de red salientes desde la subred de la instancia de Azure Cache for Redis a la infraestructura de red local del cliente. Este flujo de red interrumpe Azure Cache for Redis. La solución a este problema consiste en detener rutas anunciadas entre la ruta de acceso de interconexión pública y la ruta de acceso de interconexión privada.

La información general sobre UDR está disponible en [Enrutamiento del tráfico de redes virtuales](../virtual-network/virtual-networks-udr-overview.md).

Para más información sobre ExpressRoute, consulte [Información técnica sobre ExpressRoute](../expressroute/expressroute-introduction.md).

## <a name="next-steps"></a>Pasos siguientes

Más información sobre las características de Azure Cache for Redis.

* [Niveles de servicio de Azure Cache for Redis Premium](cache-overview.md#service-tiers)

<!-- IMAGES -->

[redis-cache-vnet]: ./media/cache-how-to-premium-vnet/redis-cache-vnet.png

[redis-cache-vnet-ip]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-ip.png

[redis-cache-vnet-info]: ./media/cache-how-to-premium-vnet/redis-cache-vnet-info.png
