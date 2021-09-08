---
title: Plan de atestación y enclaves de Intel SGX en Azure SQL Database
description: Planee la implementación de Always Encrypted con enclaves seguros en Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: bed170c4dbf61006c7d2aca14117f8946563f357
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727306"
---
# <a name="plan-for-intel-sgx-enclaves-and-attestation-in-azure-sql-database"></a>Plan de atestación y enclaves de Intel SGX en Azure SQL Database

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[Always Encrypted con enclaves seguros](/sql/relational-databases/security/encryption/always-encrypted-enclaves) en Azure SQL Database usa enclaves de [Intel software Guard Extensions (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/) y requiere [Microsoft Azure Attestation](/sql/relational-databases/security/encryption/always-encrypted-enclaves#secure-enclave-attestation).

## <a name="plan-for-intel-sgx-in-azure-sql-database"></a>Plan de Intel SGX en Azure SQL Database

Intel SGX es una tecnología de entorno de ejecución de confianza basado en hardware. Intel SGX está disponible para las bases de datos que usan el [modelo de núcleo virtual](service-tiers-sql-database-vcore.md) y la generación de hardware de [serie DC](service-tiers-sql-database-vcore.md?#dc-series). Por lo tanto, para asegurarse de que puede usar Always Encrypted con enclaves seguros en la base de datos, debe seleccionar la generación de hardware de serie DC al crear la base de datos. También puede actualizar la base de datos existente para usar la generación de hardware de serie DC.

> [!NOTE]
> Intel SGX no está disponible en las generaciones de hardware que no sean de serie DC. Por ejemplo, Intel SGX no está disponible para el hardware de Gen5 ni para las bases de datos que usan el [modelo de DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Antes de configurar la generación de hardware de serie DC para la base de datos, compruebe la disponibilidad regional de la serie DC y asegúrese de que comprende sus limitaciones de rendimiento. Para obtener información detallada, vea [Serie DC](service-tiers-sql-database-vcore.md#dc-series).

## <a name="plan-for-attestation-in-azure-sql-database"></a>Plan de atestación en Azure SQL Database

[Microsoft Azure Attestation](../../attestation/overview.md) es una solución para la atestación de entornos de ejecución de confianza (TEE), incluidos los enclaves de Intel SGX en bases de datos de Azure SQL mediante la generación de hardware de serie DC.

Para usar Azure Attestation para atestiguar enclaves de Intel SGX en Azure SQL Database, debe crear un [proveedor de atestación](../../attestation/basic-concepts.md#attestation-provider) y configurarlo con la directiva de atestación proporcionada por Microsoft. Consulte [Configuración de la atestación para Always Encrypted mediante Azure Attestation](always-encrypted-enclaves-configure-attestation.md).

## <a name="roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation"></a>Roles y responsabilidades al configurar la atestación y los enclaves de SGX

La configuración del entorno para que sea compatible con los enclaves de Intel SGX y la atestación de Always Encrypted en Azure SQL Database implica la configuración de componentes de tipos diferentes: Microsoft Azure Attestation, Azure SQL Database y aplicaciones que desencadenan la atestación de enclaves. Los usuarios realizan la configuración de los componentes de cada tipo y asumen uno de los roles siguientes:

- Administrador de atestación: crea un proveedor de atestación en Microsoft Azure Attestation, crea la directiva de atestación, concede acceso al servidor lógico de Azure SQL al proveedor de atestación y comparte la dirección URL de atestación que apunta a la directiva con los administradores de la aplicación.
- Administrador de Azure SQL Database: habilita los enclaves de SGX en las bases de datos seleccionando la generación de hardware de serie DC y proporciona al administrador de atestación la identidad del servidor lógico de Azure SQL que necesita tener acceso al proveedor de atestación.
- Administrador de aplicaciones: configura las aplicaciones con la dirección URL de atestación obtenida del administrador de atestación.

En entornos de producción (donde se controlan datos confidenciales reales), es importante que la organización se adhiera a la separación de roles al configurar la atestación, donde cada rol distinto es asumido por usuarios diferentes. En concreto, si el objetivo de implementar Always Encrypted en la organización es reducir el área expuesta a ataques asegurándose de que los administradores de Azure SQL Database no puedan tener acceso a los datos confidenciales, los administradores de Azure SQL Database no deben controlar las directivas de atestación.

## <a name="next-steps"></a>Pasos siguientes

- [Habilitación de Intel SGX para Azure SQL Database](always-encrypted-enclaves-enable-sgx.md)

## <a name="see-also"></a>Consulte también

- [Tutorial: Introducción a Always Encrypted con enclaves seguros en Azure SQL Database](always-encrypted-enclaves-getting-started.md)