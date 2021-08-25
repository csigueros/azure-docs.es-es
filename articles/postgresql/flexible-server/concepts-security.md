---
title: 'Seguridad en Azure Database for PostgreSQL: Servidor flexible'
description: 'Obtenga información sobre la seguridad en la opción de implementación opción Servidor flexible para Azure Database for PostgreSQL: Servidor flexible'
author: gennadNY
ms.author: gennadyk
ms.service: postgresql
ms.custom: mvc
ms.devlang: python
ms.topic: quickstart
ms.date: 07/26/2021
ms.openlocfilehash: 489ae3e9593ed5fa0865fb6f3fbe2eb617113d71
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181498"
---
# <a name="security-in-azure-database-for-postgresql---flexible-server"></a>Seguridad en Azure Database for PostgreSQL: Servidor flexible

Existen varios niveles de seguridad disponibles para proteger los datos en el servidor de Azure Database for PostgreSQL. En este artículo se describen esas opciones de seguridad.

## <a name="information-protection-and-encryption"></a>Protección y cifrado de información

### <a name="in-transit"></a>En tránsito
 Azure Database for PostgreSQL protege los datos mediante el cifrado de datos en tránsito con Seguridad de la capa de transporte. El cifrado (SSL/TLS) se aplica de forma predeterminada.

### <a name="at-rest"></a>En reposo
El servicio Azure Database for PostgreSQL usa el módulo criptográfico con validación FIPS 140-2 para el cifrado del almacenamiento de los datos en reposo. Los datos, incluidas las copias de seguridad, se cifran en el disco, junto con los archivos temporales creados mientras se ejecutan las consultas. El servicio usa el cifrado AES de 256 bits que se incluye en el cifrado de almacenamiento de Azure y las claves las administra el sistema. Todo esto se diferencia poco de lo que ofrecen otras tecnologías de cifrado en reposo como Cifrado de datos transparente (TDE) en las bases de datos de SQL Server u Oracle. El cifrado de almacenamiento siempre está activado y no se puede deshabilitar.


## <a name="network-security"></a>Seguridad de las redes

Al ejecutar Azure Database for PostgreSQL: Servidor flexible se pueden elegir dos opciones de red principales. Las opciones son acceso privado (integración con red virtual) y acceso público (direcciones IP permitidas) . Mediante el uso del acceso privado, el servidor flexible se implementa en Azure Virtual Network. Las redes virtuales de Azure proporcionan una comunicación de red privada y segura. Los recursos de una red virtual se pueden comunicar mediante direcciones IP privadas.
Con acceso público se accede al servidor flexible a través de un punto de conexión público. El punto de conexión público es una dirección DNS que se puede resolver públicamente, y el acceso a él se puede proteger mediante un firewall que bloquea todas las conexiones de forma predeterminada. 



### <a name="ip-firewall-rules"></a>Reglas de firewall de IP
Las reglas de firewall de IP otorgan acceso a los servidores según la dirección IP de origen de cada solicitud. Consulte la [información general sobre las reglas de firewall](concepts-firewall-rules.md) para obtener más información.


### <a name="private-vnet-access"></a>Acceso a una red virtual privada
El servidor flexible se puede implementar en Azure Virtual Network. Las redes virtuales de Azure proporcionan una comunicación de red privada y segura. Para más información, consulte el [servidor flexible](concepts-networking.md)

### <a name="network-security-groups-nsg"></a>Grupos de seguridad de red (NSG)
Las reglas de seguridad de grupos de seguridad de red permiten filtrar el tipo de tráfico de red que puede fluir dentro y fuera de las interfaces de red y las subredes de redes virtuales.  Para más información, consulte [Introducción a los grupos de seguridad de red](../../virtual-network/network-security-groups-overview.md).

## <a name="access-management"></a>Administración de acceso

Al crear el servidor de Azure Database for PostgreSQL, se deben proporcionar las credenciales de un rol de usuario administrador. Este rol de administrador se puede usar para crear más [roles de MySQL](https://www.postgresql.org/docs/current/user-manag.html).

También se puede conectar al servidor a través de la [autenticación de Azure Active Directory (AAD)](../concepts-aad-authentication.md).


### <a name="azure-defender-protection"></a>Protección de Azure Defender

 Azure Database for PostgreSQL: Servidor flexible actualmente no admite la [protección de Azure Defender](../../security-center/azure-defender.md). 


Existe un [registro de auditoría](../concepts-audit.md) disponible para realizar un seguimiento de las actividades en las bases de datos. 


## <a name="next-steps"></a>Pasos siguientes
  - Habilitación de reglas de firewall para [IP](concepts-firewall-rules.md) de redes de acceso público
  - Más información sobre [las redes de acceso privado con Azure Database for PostgreSQL: Servidor flexible](concepts-networking.md)
  - Obtenga más información sobre la [autenticación de Azure Active Directory](../concepts-aad-authentication.md) en Azure Database for PostgreSQL.