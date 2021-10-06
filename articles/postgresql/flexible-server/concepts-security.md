---
title: 'Seguridad en Azure Database for PostgreSQL: Servidor flexible'
description: Obtenga información sobre la seguridad en la opción de implementación opción Servidor flexible para Azure Database for PostgreSQL.
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 09/08/2021
ms.openlocfilehash: cf5c28d197aabf60d7e6ee36c2e724ffad435522
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128559216"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Seguridad en Azure Database for PostgreSQL: Servidor flexible

Hay varios niveles de seguridad disponibles para ayudar a proteger los datos en el servidor de Azure Database for PostgreSQL. En este artículo se describen esas opciones de seguridad.

## <a name="information-protection-and-encryption"></a>Protección y cifrado de información

Azure Database for PostgreSQL cifra los datos de dos maneras:

- **Datos en tránsito:** Azure Database for PostgreSQL cifra los datos en tránsito con Capa de sockets seguros y Seguridad de la capa de transporte (SSL/TLS). El cifrado se aplica de forma predeterminada. Consulte esta [guía](how-to-connect-tls-ssl.md) para más detalles. Para mejorar la seguridad, puede optar por habilitar la [autenticación SCRAM](how-to-connect-scram.md).
 Aunque no se recomienda, si es necesario, tiene la opción de deshabilitar TLS\SSL para las conexiones al servidor flexible de Azure Database for PostgreSQL mediante la actualización del parámetro de servidor **require_secure_transport** a OFF. También puede establecer la versión de TLS; para ello, establezca los parámetros de servidor **ssl_min_protocol_version** y **ssl_max_protocol_version**.

- **Datos en reposo:** Azure Database for PostgreSQL usa el módulo criptográfico FIPS 140-2 validado para el cifrado del almacenamiento. Los datos se cifran en el disco, incluidas las copias de seguridad y los archivos temporales que se crean mientras se ejecutan las consultas. 

  El servicio usa el cifrado AES de 256 bits que se incluye en el cifrado de almacenamiento de Azure y las claves las administra el sistema. Todo esto se diferencia poco de lo que ofrecen otras tecnologías de cifrado en reposo como el cifrado de datos transparente en las bases de datos de SQL Server u Oracle. El cifrado de almacenamiento siempre está activado y no se puede deshabilitar.


## <a name="network-security"></a>Seguridad de las redes

Cuando se ejecuta Azure Database for PostgreSQL: servidor flexible, tiene dos opciones de red principales:

- **Acceso privado:** el servidor se puede implementar en Azure Virtual Network. Las redes virtuales de Azure ayudan a proporcionan una comunicación de red privada y segura. Los recursos de una red virtual se pueden comunicar mediante direcciones IP privadas. Para más información, consulte la [introducción a las redes para Azure Database for PostgreSQL con la opción Servidor flexible](concepts-networking.md).

  Las reglas de seguridad de grupos de seguridad de red permiten filtrar el tipo de tráfico de red que puede fluir dentro y fuera de las interfaces de red y las subredes de redes virtuales. Para más información, consulte la [introducción a los grupos de seguridad de red](../../virtual-network/network-security-groups-overview.md).

- **Acceso público:** se accede al servidor a través de un punto de conexión público. El punto de conexión público es una dirección DNS que se puede resolver públicamente. El acceso a él se protege con un firewall que bloquea todas las conexiones de forma predeterminada. 

  Las reglas de firewall de IP otorgan acceso a los servidores según la dirección IP de origen de cada solicitud. Para más información, consulte la [introducción a las reglas de firewall](concepts-firewall-rules.md).

## <a name="access-management"></a>Administración de acceso

Al crear el servidor de Azure Database for PostgreSQL, se deben proporcionar las credenciales de un rol de usuario administrador. Este rol de administrador se puede usar para crear más [roles de PostgreSQL](https://www.postgresql.org/docs/current/user-manag.html).

Por ejemplo,

```SQL
postgres=> create role demouser with password 'password123';
```

Puede auditar periódicamente la lista de roles del servidor. Por ejemplo, puede conectarse mediante el cliente `psql` y consultar la tabla `pg_roles`, que enumera todos los roles junto con privilegios, como crear roles adicionales, crear bases de datos, replicación, etc. 

```SQL
postgres=> \x
Expanded display is on.
postgres=> select * from pg_roles where rolname='demouser';
-[ RECORD 1 ]--+---------
rolname        | demouser
rolsuper       | f
rolinherit     | t
rolcreaterole  | f
rolcreatedb    | f
rolcanlogin    | f
rolreplication | f
rolconnlimit   | -1
rolpassword    | ********
rolvaliduntil  |
rolbypassrls   | f
rolconfig      |
oid            | 24827

```

El [registro de auditoría](../concepts-audit.md) también está disponible con la opción de servidor flexible para realizar un seguimiento de la actividad de las bases de datos. 

> [!NOTE]
> La característica Servidor flexible de Azure Database for PostgreSQL actualmente no admite la [protección de Azure Defender](../../security-center/azure-defender.md). 

## <a name="updating-passwords"></a>Actualización de contraseñas

Para mejorar la seguridad, es un procedimiento recomendado rotar periódicamente la contraseña del administrador y las contraseñas de usuario de la base de datos. Se recomienda usar contraseñas seguras con mayúsculas y minúsculas, números y caracteres especiales.

### <a name="reset-administrator-password"></a>Restablecimiento de la contraseña del administrador

Siga la [guía de procedimientos](./how-to-manage-server-portal.md#reset-admin-password) para restablecer la contraseña de administrador.

### <a name="update-database-user-password"></a>Actualización de la contraseña de usuario de base de datos

Puede usar las herramientas de cliente para actualizar las contraseñas de usuario de base de datos. Por ejemplo,
```SQL
postgres=> alter role demouser with password 'Password123!';
ALTER ROLE
```
## <a name="next-steps"></a>Pasos siguientes
- Habilite [reglas de firewall para direcciones IP](concepts-firewall-rules.md) en redes de acceso público.
- Más información sobre las [redes de acceso privado con Azure Database for PostgreSQL: Servidor flexible](concepts-networking.md)
- Obtenga más información sobre la [autenticación de Azure Active Directory](../concepts-aad-authentication.md) en Azure Database for PostgreSQL.