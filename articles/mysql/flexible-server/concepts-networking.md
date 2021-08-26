---
title: 'Información general sobre redes: Servidor flexible de Azure Database for MySQL'
description: Obtenga información sobre las opciones de conectividad y redes en la opción de implementación Servidor flexible para Azure Database for MySQL
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/23/2020
ms.openlocfilehash: 44b13211bd8a7f3b71f1357db0097500801c2a0c
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121724296"
---
# <a name="connectivity-and-networking-concepts-for-azure-database-for-mysql---flexible-server-preview"></a>Conceptos de conectividad y redes para Servidor flexible (versión preliminar) de Azure Database for MySQL

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

En este artículo se presentan los conceptos para controlar la conectividad con el servidor flexible de Azure MySQL. Aprenderá en detalle los conceptos de red para un servidor flexible de Azure Database for MySQL a fin de crear un servidor y acceder a este de forma segura en Azure.

> [!IMPORTANT]
> Azure Database for MySQL: servidor flexible está en versión preliminar.

Un servidor flexible de Azure Database for MySQL admite dos formas de configurar la conectividad con los servidores:
> [!NOTE]
> La opción de red no se puede cambiar una vez que se haya creado el servidor.

   * **Acceso privado (integración con red virtual)** [Acceso privado](./concepts-networking-vnet.md): puede implementar el servidor flexible en la instancia de [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md). Las redes virtuales de Azure proporcionan una comunicación de red privada y segura. Los recursos de una red virtual se pueden comunicar mediante direcciones IP privadas.
   
   * **Acceso público (direcciones IP permitidas)** [Acceso público](./concepts-networking-public.md): se accede al servidor flexible a través de un punto de conexión público. El punto de conexión público es una dirección DNS que se puede resolver públicamente. La frase "direcciones IP permitidas" hace referencia a un intervalo de direcciones IP a las que decida conceder permiso para acceder al servidor. Estos permisos se denominan **reglas de firewall**.

## <a name="choosing-a-networking-option"></a>Elección de una opción de red

Elija la opción de **acceso privado (integración con red virtual)** si quiere las funcionalidades siguientes:
   * Conexión desde los recursos de Azure en la misma red virtual o [red virtual emparejada](../../virtual-network/virtual-network-peering-overview.md) a su servidor flexible
   * Uso de VPN o ExpressRoute para conectarse desde recursos que no son de Azure al servidor flexible
   * Ningún punto de conexión público

Elija el método de **acceso público (direcciones IP permitidas)** si quiere las funcionalidades siguientes:
   * Conexión desde recursos de Azure que no admiten redes virtuales
   * Conexión desde recursos externos de Azure que no están conectados mediante VPN o ExpressRoute 
   * El servidor flexible tiene un punto de conexión público

Las características siguientes se aplican tanto si decide usar la opción de acceso privado como la de acceso público:
* Las conexiones de direcciones IP permitidas se deben autenticar en el servidor MySQL con credenciales válidas
* Hay [cifrado de conexión](#tls-and-ssl) disponible para el tráfico de red
* El servidor tiene un nombre de dominio completo (FQDN). En el caso de la propiedad hostname en las cadenas de conexión, se recomienda usar el FQDN en lugar de una dirección IP.
* Las dos opciones controlan el acceso en el nivel de servidor, no en el nivel de base de datos o de tabla. Tendría que usar las propiedades de los roles de MySQL para controlar el acceso de bases de datos, tablas y otros objetos.


### <a name="unsupported-virtual-network-scenarios"></a>Escenarios de red virtual no admitidos

* Punto de conexión público (o dirección IP pública o DNS): un servidor flexible implementado en una red virtual no puede tener un punto de conexión público
* Una vez que se haya implementado el servidor flexible en una red virtual y una subred, no se puede trasladar a otra red virtual o subred. * Una vez implementado el servidor flexible, no se puede mover la red virtual que este usa a otro grupo de recursos o suscripción.
* No se puede aumentar el tamaño de la subred (espacios de direcciones) cuando existen recursos en la subred
* No se permite el cambio de acceso público a privado después de crear el servidor. El método recomendado es usar la restauración a un momento dado

Obtenga información sobre cómo habilitar el acceso privado (integración con red virtual) con [Azure Portal](how-to-manage-virtual-network-portal.md) o la [CLI de Azure](how-to-manage-virtual-network-cli.md).

> [!NOTE]
> Si usa el servidor DNS personalizado, debe usar un reenviador DNS para resolver el FQDN de Azure Database for MySQL: servidor flexible. Para obtener más información, consulte [Resolución de nombres con su propio servidor DNS](../../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).

## <a name="hostname"></a>Nombre de host
Con independencia de la opción de red que elija, se recomienda usar siempre un nombre de dominio completo (FQDN) `<servername>.mysql.database.azure.com` en las cadenas de conexión al conectarse al servidor flexible. 

## <a name="tls-and-ssl"></a>TLS y SSL
El servidor flexible de Azure Database for MySQL admite la conexión de las aplicaciones cliente al servidor MySQL mediante el cifrado de la Capa de sockets seguros (SSL) con la Seguridad de la capa de transporte (TLS). TLS es un protocolo estándar del sector que garantiza conexiones de red cifradas entre el servidor de bases de datos y las aplicaciones cliente, lo que le permite ajustarse a los requisitos de cumplimiento.

De manera predeterminada, el servidor flexible de Azure Database for MySQL solo admite conexiones cifradas mediante la Seguridad de la capa de transporte (TLS 1.2), y todas las conexiones entrantes con TLS 1.0 y TLS 1.1 se denegarán de manera predeterminada. La obligatoriedad de conexiones cifradas o la configuración de versiones de TLS en el servidor flexible se pueden configurar y cambiar. 

A continuación, se muestran las distintas configuraciones de SSL y TLS que puede tener para el servidor flexible:

| Escenario   | Configuración de parámetros del servidor      | Descripción                                    |
|------------|--------------------------------|------------------------------------------------|
|Deshabilitar SSL (conexiones cifradas) | require_secure_transport = OFF |Si la aplicación heredada no admite conexiones cifradas al servidor MySQL, puede deshabilitar la obligatoriedad de conexiones cifradas en el servidor flexible estableciendo require_secure_transport=OFF.|
|Exigir SSL con la versión de TLS < 1.2 | require_secure_transport = ON y tls_version = TLSV1 o TLSV1.1| Si la aplicación heredada admite conexiones cifradas, pero necesita la versión de TLS > 1, puede permitir conexiones cifradas, pero configurar el servidor flexible para permitir conexiones con la versión de TLS (v1.0 o v1.1) admitida por la aplicación.|
|Exigir SSL con la versión de TLS = 1.2 (configuración predeterminada)|require_secure_transport = ON y tls_version = TLSV1.2| Esta es la configuración predeterminada y recomendada para el servidor flexible.|
|Exigir SSL con la versión de TLS = 1.3 (compatible con MySQL v8.0 y versiones posteriores)| require_secure_transport = ON y tls_version = TLSV1.3| Esta configuración es útil y recomendada para el desarrollo de nuevas aplicaciones|

> [!Note]
> No se admiten cambios en el cifrado SSL del servidor flexible. Los conjuntos de cifrado FIPS se aplican de forma predeterminada cuando tls_version está establecido en la versión de TLS 1.2. En el caso de las versiones de TLS distintas de la versión 1.2, el cifrado SSL se establece en la configuración predeterminada que se incluye con la instalación de la comunidad de MySQL.

Consulte cómo [conectarse mediante SSL/TLS](how-to-connect-tls-ssl.md) para obtener más información. 


## <a name="next-steps"></a>Pasos siguientes
* Información sobre cómo habilitar el acceso privado (integración con red virtual) con [Azure Portal](how-to-manage-virtual-network-portal.md) o la [CLI de Azure](how-to-manage-virtual-network-cli.md)
* Información sobre cómo habilitar el acceso público (direcciones IP permitidas) mediante [Azure Portal](how-to-manage-firewall-portal.md) o la [CLI de Azure](how-to-manage-firewall-cli.md)
* Información sobre cómo [usar TLS](how-to-connect-tls-ssl.md)
