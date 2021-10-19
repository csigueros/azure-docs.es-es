---
title: 'Tutorial: Configuración de la red en un clúster de Azure FXT Edge Filer'
description: Cómo personalizar la configuración de red después de crear el clúster de Azure FXT Edge Filer
author: ekpgh
ms.author: v-erkel
ms.service: fxt-edge-filer
ms.topic: tutorial
ms.date: 10/07/2021
ms.openlocfilehash: c723214962e67ef04f9cf7659f63d29af87a4732
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129670066"
---
# <a name="tutorial-configure-the-clusters-network-settings"></a>Tutorial: Configuración de red del clúster

Antes de usar un clúster de Azure FXT Edge Filer recién creado, debe comprobar y personalizar varias configuraciones de red del flujo de trabajo.

En este tutorial se explica la configuración de red que podría tener que ajustar para un nuevo clúster.

Aprenderá a:

> [!div class="checklist"]
>
> * Qué configuración de red podría tener que actualizar después de crear un clúster
> * Qué casos de uso de Azure FXT Edge Filer requieren un servidor AD o un servidor DNS
> * Cómo configurar DNS (RRDNS) round robin para equilibrar automáticamente la carga de las solicitudes cliente en el clúster FXT

El tiempo necesario para completar estos pasos depende de la cantidad de cambios de configuración que sean necesarios en el sistema:

* Si solo necesita leer el tutorial y comprobar algunos valores, tardará de 10 a 15 minutos.
* Si necesita configurar DNS round robin, esa tarea puede tardar una hora o más.

## <a name="adjust-network-settings"></a>Ajuste de la configuración de red

En la configuración de un nuevo clúster de Azure FXT Edge Filer intervienen varias tareas relacionadas con la red. Compruebe esta lista y decida cuáles se aplican a su sistema.

Para más información sobre la configuración de red de un clúster, lea [Configuring network services](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html) (Configuración de los servicios de red) en la guía de configuración del clúster.

* Configure DNS round robin para la red orientada al cliente (opcional).

  Equilibre la carga del tráfico del clúster mediante la configuración del sistema DNS como se describe en [Configuración de DNS para el clúster de Azure FXT Edge Filer](#configure-dns-for-load-balancing).

* Compruebe la configuración de NTP.

* Configure las descargas de nombre de grupo y nombre de usuario de Active Directory (si es necesario).

  Si los hosts de red usan Active Directory u otro tipo de servicio de directorio externo, debe modificar la configuración de los servicios de directorio del clúster para configurar cómo el clúster descarga la información de nombre de usuario y grupo. Lea **Cluster** > **Directory Services** (Clúster > Servicios de directorio) en la guía de configuración del clúster para más información.

  Un servidor de AD es necesario si desea compatibilidad con SMB. Configure AD antes de comenzar a configurar SMB.

* Defina las redes VLAN (opcional).
  
  Configure cualquier red VLAN adicional necesaria antes de definir el espacio de nombres global y las instancias de servidor virtual del clúster. Lea [Working with VLANs](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/network_overview.html#vlan-overview) (Uso de redes VLAN) en la guía de configuración del clúster para más información.

* Configure los servidores proxy (si es necesario).

  Si el clúster usa un servidor proxy para tener acceso a direcciones externas, siga estos pasos para configurarlo:

  1. Defina el servidor proxy en la página **Proxy Configuration** (Configuración de proxy).
  1. Aplique la configuración del servidor proxy con la página **Cluster** > **General Setup** (Clúster > Configuración general) o la página **Core Filer Details** (Detalles principales de Filer).
  
  Para más información, lea [Using web proxies](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/proxy_overview.html) (Uso de servidores proxy) en la guía de configuración del clúster.

* Cargue los [certificados de cifrado](#encryption-certificates) que usará el clúster (opcional)

### <a name="encryption-certificates"></a>Certificados de cifrado

El clúster de FXT Edge Filer usa certificados X.509 para estas funciones:

* Para cifrar el tráfico de administración del clúster

* Para autenticarse en nombre de un cliente en servidores de terceros KMIP

* Para comprobar los certificados de servidor de los proveedores de nube

Si tiene que cargar los certificados en el clúster, use la página de configuración **Cluster** > **Certificates** (Clúster > Certificados). Los detalles se encuentran en la página [Cluster > Certificates](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_certificates.html) (Clúster > Certificados) de la guía de configuración del clúster.

Para cifrar la comunicación de administración del clúster, use la página de configuración **Cluster** > **General Setup** (Clúster > Configuración general) para seleccionar qué certificado usar para TLS administrativo.

Asegúrese de que las máquinas de administración cumplen los [estándares de cifrado](supported-ciphers.md) del clúster.

> [!Note]
> Las claves de acceso del servicio en la nube se almacenan mediante la página de configuración **Cloud Credentials** (Credenciales de nube). En la sección anterior [Add a core filer](add-storage.md#add-a-core-filer) (Adición de un archivador principal) se muestra un ejemplo; lea la sección [Cloud Credentials](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_cloud_credentials.html) (Credenciales de nube) de la guía de configuración del clúster para más información.

## <a name="configure-dns-for-load-balancing"></a>Configuración de DNS para el equilibrio de carga

En esta sección se explican los conceptos básicos de la configuración de un sistema de DNS (RRDNS) round-robin para distribuir la carga del cliente entre todas las direcciones IP orientadas al cliente en el clúster de FXT Edge Filer.

### <a name="decide-whether-or-not-to-use-dns"></a>Decidir si usar o no DNS

Aunque siempre se recomienda equilibrar la carga, no es necesario usar siempre DNS. Por ejemplo, con algunos tipos de flujos de trabajo de cliente podría ser más adecuado usar un script para asignar direcciones IP del clúster uniformemente entre los clientes cuando montan el clúster. Algunos métodos se describen en [Mount the cluster](mount-clients.md) (Montaje del clúster).

Tenga esto en mente la hora de decidir si quiere usar un servidor DNS:

* Si solo los clientes NFS acceden al sistema, no es necesario DNS. Es posible especificar todas las direcciones de red mediante direcciones IP numéricas.

* Si el sistema admite el acceso de SMB (CIFS), DNS es necesario, ya que debe especificar un dominio DNS para el servidor de Active Directory.

* DNS es necesario si quiere utilizar la autenticación Kerberos.

### <a name="round-robin-dns-configuration-details"></a>Detalles de configuración de DNS round robin

Un sistema DNS round robin (RRDNS) enruta automáticamente las solicitudes de cliente entre varias direcciones.

Para configurar este sistema, debe personalizar el archivo de configuración del servidor DNS para que, cuando reciba solicitudes de montaje en la dirección de dominio principal de FXT Edge Filer, asigne el tráfico entre todos los puntos de montaje del clúster. Los clientes montan el clúster con su nombre de dominio como argumento de servidor, y se enrutan a la siguiente dirección IP de montaje automáticamente.

Hay dos pasos principales para configurar RRDNS:

1. Modifique el archivo ``named.conf`` del servidor DNS para establecer un orden cíclico para las consultas al clúster de FXT. Esta opción hace que el servidor pase por todos los valores IP disponibles. Agregue una instrucción como la siguiente:

   ```bash
   options {
       rrset-order {
           class IN A name "fxt.contoso.com" order cyclic;
       };
   };
   ```

1. Configure registros A y registros de puntero (PTR) para cada dirección IP disponible como en el ejemplo siguiente.

   Estos comandos ``nsupdate`` proporcionan un ejemplo de configuración correcta de DNS para un clúster de Azure FXT Edge Filer con el nombre de dominio fxt.contoso.com y tres direcciones de montaje (10.0.0.10, 10.0.0.11 y 10.0.0.12):

   ```bash
   update add fxt.contoso.com. 86400 A 10.0.0.10
   update add fxt.contoso.com. 86400 A 10.0.0.11
   update add fxt.contoso.com. 86400 A 10.0.0.12
   update add client-IP-10.contoso.com. 86400 A 10.0.0.10
   update add client-IP-11.contoso.com. 86400 A 10.0.0.11
   update add client-IP-12.contoso.com. 86400 A 10.0.0.12
   update add 10.0.0.10.in-addr.arpa. 86400 PTR client-IP-10.contoso.com
   update add 11.0.0.10.in-addr.arpa. 86400 PTR client-IP-11.contoso.com
   update add 12.0.0.10.in-addr.arpa. 86400 PTR client-IP-12.contoso.com
   ```

   Estos comandos crean un registro A para cada una de las direcciones de montaje del clúster y, además, configuran registros de puntero para admitir adecuadamente comprobaciones inversas de DNS.

   En el diagrama siguiente se muestra la estructura básica de esta configuración.

   :::image type="complex" source="media/round-robin-dns-diagram-fxt.png" alt-text="Diagrama que muestra la configuración de DNS del punto de montaje de cliente.":::
   <El diagrama muestra conexiones entre tres categorías de elementos: el nombre de dominio único del clúster de FXT Edge Filer (a la izquierda), tres direcciones IP (columna central) y tres interfaces de cliente DNS de uso interno (columna derecha). Un único óvalo a la izquierda con la etiqueta "fxt.contoso.com" está conectado mediante flechas que apuntan a tres óvalos etiquetados con las direcciones IP: 10.0.0.10, 10.0.0.11 y 10.0.0.12. Las flechas del óvalo fxt.contoso.com a los tres óvalos de IP están etiquetadas como "A". Cada uno de los óvalos de dirección IP está conectado mediante dos flechas a un óvalo etiquetado como una interfaz de cliente: el óvalo con la IP 10.0.0.10 está conectado a "client-IP-10.contoso.com", el óvalo con la IP 10.0.0.11 está conectado a "client-IP-11.contoso.com" y el óvalo con la IP 10.0.0.12 está conectado a "client-IP-11.contoso.com". Las conexiones entre los óvalos de dirección IP y los de interfaz de cliente son dos flechas: una flecha con la etiqueta "PTR" que apunta desde el óvalo de dirección IP al de interfaz de cliente, y una flecha con la etiqueta "A" que apunta desde el óvalo de interfaz de cliente al de dirección IP.> :::image-end:::

Una vez configurado el sistema RRDNS, indique a los equipos cliente que lo usen para resolver la dirección del clúster de FXT en sus comandos de montaje.

### <a name="enable-dns-in-the-cluster"></a>Habilitación de DNS en el clúster

Especifique el servidor DNS que usa el clúster en la página de configuración **Cluster** > **Administrative Network** (Clúster > Red administrativa). La configuración de esta página incluye:

* Dirección del servidor DNS
* Nombre de dominio DNS
* Dominios de búsqueda DNS

Para más información, lea [DNS Settings](<https://azure.github.io/Avere/legacy/ops_guide/4_7/html/gui_admin_network.html#gui-dns>) (Configuración de DNS) en la guía de configuración de clúster.

## <a name="next-steps"></a>Pasos siguientes

Este es el último paso de la configuración básica del clúster de Azure FXT Edge Filer.

* Aprenda sobre los LED del sistema y otros indicadores en [Monitor hardware status](monitor.md) (Supervisión del estado del hardware).
* Conozca más sobre cómo los clientes deben montar el clúster de FXT Edge Filer en [Mount the cluster](mount-clients.md) (Montaje del clúster).
* Para más información sobre el funcionamiento y la administración de un clúster de FXT Edge Filer, consulte la [guía de configuración del clúster](https://azure.github.io/Avere/legacy/ops_guide/4_7/html/ops_conf_index.html).
