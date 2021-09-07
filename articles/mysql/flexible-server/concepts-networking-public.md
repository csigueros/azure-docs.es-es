---
title: 'Información general sobre el acceso público a redes: servidor flexible para Azure Database for MySQL'
description: Obtenga información sobre la opción de redes de acceso público en la opción de implementación de servidor flexible para Azure Database for MySQL.
author: Madhusoodanan
ms.author: dimadhus
ms.service: mysql
ms.topic: conceptual
ms.date: 8/6/2021
ms.openlocfilehash: 414b2f8d6e64112c737fe220003ca2c58b3e9d7b
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121781071"
---
# <a name="public-network-access-for-azure-database-for-mysql---flexible-server-preview"></a>Acceso a redes públicas para Azure Database for MySQL: servidor flexible (versión preliminar)

[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

En este artículo se describe la opción de conectividad pública para su servidor. Conocerá en detalle los conceptos para crear un servidor flexible de Azure Database for MySQL al que se pueda acceder de forma segura a través de Internet.

> [!IMPORTANT]
> Azure Database for MySQL: servidor flexible está en versión preliminar.

## <a name="public-access-allowed-ip-addresses"></a>Acceso público (direcciones IP permitidas)

Configurando el acceso público a su servidor flexible, se podrá acceder a este a través de un punto de conexión público, esto es, a través de Internet. El punto de conexión público es una dirección DNS que se puede resolver públicamente. La frase "direcciones IP permitidas" hace referencia a un intervalo de direcciones IP a las que decida conceder permiso para acceder al servidor. Estos permisos se denominan reglas de firewall.

Entre las características del método de acceso público se incluyen las siguientes:

* Solo las direcciones IP que permita pueden acceder al servidor flexible de MySQL. De forma predeterminada, no se permite ninguna dirección IP. Puede agregar direcciones IP durante la creación del servidor o después.
* El servidor MySQL tiene un nombre DNS que se pueda resolver de forma pública
* El servidor flexible no está en una de las redes virtuales de Azure
* El tráfico de red hacia y desde el servidor no se realiza a través de una red privada. El tráfico usa las rutas de Internet generales.

### <a name="firewall-rules"></a>Reglas de firewall

La concesión de permisos a una dirección IP se denomina regla de firewall. Si un intento de conexión procede de una dirección IP no permitida, el cliente de origen verá un error.

### <a name="allowing-all-azure-ip-addresses"></a>Permitir todas las direcciones IP de Azure

Si no hay ninguna dirección IP saliente fija disponible para su servicio de Azure, puede habilitar las conexiones de todas las direcciones IP del centro de datos de Azure.

> [!IMPORTANT]
> La opción **Permitir acceso público desde los recursos y servicios de Azure dentro de Azure** configura el firewall para permitir todas las conexiones desde Azure, incluidas las que provienen de suscripciones de otros clientes. Al seleccionar esta opción, asegúrese de que los permisos de usuario y el inicio de sesión limiten el acceso solamente a los usuarios autorizados.

Obtenga información sobre cómo habilitar y administrar el acceso público (direcciones IP permitidas) mediante [Azure Portal](how-to-manage-firewall-portal.md) o la [CLI de Azure](how-to-manage-firewall-cli.md).

### <a name="troubleshooting-public-access-issues"></a>Solución de problemas de acceso público

Tenga en cuenta los aspectos siguientes cuando el acceso al servicio del servidor de Microsoft Azure Database for MySQL no se comporte de la manera prevista:

* **Los cambios realizados en la lista de permitidos no han surtido efecto todavía:** puede haber un retraso de hasta cinco minutos hasta que los cambios en la configuración del firewall del servidor de Azure Database for MySQL surtan efecto.

* **Error de autenticación:** si un usuario no tiene los permisos en el servidor de Azure Database for MySQL o la contraseña usada es incorrecta, se denegará la conexión al servidor de Azure Database for MySQL. La creación de una configuración de firewall solo ofrece a los clientes una oportunidad de intentar conectarse al servidor. Cada cliente todavía tiene que proporcionar las credenciales de seguridad necesarias.

* **Dirección IP del cliente dinámica:** Si tiene una conexión a Internet con un direccionamiento IP dinámico y tiene problemas al acceder al firewall, pruebe una de las soluciones siguientes:

  * Pida al proveedor de acceso a Internet (ISP) el intervalo de direcciones IP asignado a los equipos cliente que acceden al servidor de Azure Database for MySQL y agréguelo como regla de firewall.
  * Obtenga el direccionamiento IP estático en su lugar para los equipos cliente y luego agregue la dirección IP estática como regla de firewall.
  
* **La regla de firewall no está disponible en formato IPv6:** las reglas de firewall deben estar en formato IPv4. Si especifica reglas de firewall en formato IPv6, aparece el error de validación.

## <a name="next-steps"></a>Pasos siguientes

* Información sobre cómo habilitar el acceso público (direcciones IP permitidas) mediante [Azure Portal](how-to-manage-firewall-portal.md) o la [CLI de Azure](how-to-manage-firewall-cli.md)
* Información sobre cómo [usar TLS](how-to-connect-tls-ssl.md)
