---
title: 'Introducción a la seguridad: Hiperescala (Citus) en Azure Database for PostgreSQL'
description: 'Protección de la información y seguridad de red para Azure Database for PostgreSQL: Hiperescala (Citus).'
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/15/2021
ms.openlocfilehash: eedad96af5baa6fe588788247c4f8a3a44bee0a0
ms.sourcegitcommit: 37cc33d25f2daea40b6158a8a56b08641bca0a43
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2021
ms.locfileid: "130074968"
---
# <a name="security-in-azure-database-for-postgresql--hyperscale-citus"></a>Seguridad en Hiperescala (Citus) para Azure Database for PostgreSQL

En esta página se describen las distintas capas de seguridad disponibles para proteger los datos del grupo de servidores de Hiperescala (Citus). 

## <a name="information-protection-and-encryption"></a>Protección y cifrado de información

### <a name="in-transit"></a>En tránsito

Cuando se ingieren datos en un nodo, Hiperescala (Citus) los protege al cifrarlos en tránsito con Seguridad de la capa de transporte 1.2. El cifrado (SSL/TLS) siempre se aplica y no se puede deshabilitar.

### <a name="at-rest"></a>En reposo

El servicio de Hiperescala (Citus) usa el módulo criptográfico con validación FIPS 140-2 para el cifrado del almacenamiento de los datos en reposo. Los datos, incluidas las copias de seguridad, se cifran en el disco, junto con los archivos temporales creados mientras se ejecutan las consultas.
El servicio usa el cifrado AES de 256 bits que se incluye en el cifrado de almacenamiento de Azure y las claves las administra el sistema. El cifrado de almacenamiento siempre está activado y no se puede deshabilitar.

## <a name="network-security"></a>Seguridad de las redes

[!INCLUDE [azure-postgresql-hyperscale-access](../../includes/azure-postgresql-hyperscale-access.md)]

## <a name="limits-and-limitations"></a>Límites y limitaciones

Consulte la página de [límites y limitaciones](concepts-hyperscale-limits.md) de Hiperescala (Citus).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre cómo [habilitar y administrar el acceso privado](howto-hyperscale-private-access.md) (versión preliminar).
* Más información sobre los [puntos de conexión privados](/azure/private-link/private-endpoint-overview)
* Obtenga información sobre las [redes virtuales](/azure/virtual-network/concepts-and-best-practices).
* Más información sobre las [zonas DNS privadas](/azure/dns/private-dns-overview)
