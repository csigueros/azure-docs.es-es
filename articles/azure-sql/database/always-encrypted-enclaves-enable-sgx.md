---
title: Habilitación de Intel SGX para Always Encrypted
description: Obtenga información sobre cómo habilitar Intel SGX para Always Encrypted con enclaves seguros en Azure SQL Database seleccionando una generación de hardware habilitada para SGX.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviwer: vanto
ms.date: 07/14/2021
ms.openlocfilehash: b967ad27b3f20b75e9b7571a865228508bca9112
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/14/2021
ms.locfileid: "113727263"
---
# <a name="enable-intel-sgx-for-always-encrypted-for-your-azure-sql-database"></a>Habilitación de Intel SGX para Always Encrypted en Azure SQL Database 

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]


[Always Encrypted con enclaves seguros](/sql/relational-databases/security/encryption/always-encrypted-enclaves) en Azure SQL Database usa enclaves de [Software Guard Extensions de Intel (Intel SGX)](https://itpeernetwork.intel.com/microsoft-azure-confidential-computing/). Para que Intel SGX esté disponible, las bases de datos deben usar el [modelo de núcleo virtual](service-tiers-vcore.md) y la generación de hardware de [serie DC](service-tiers-sql-database-vcore.md#dc-series).

La configuración de la generación de hardware de serie DC para habilitar los enclaves de Intel SGX es responsabilidad del administrador de Azure SQL Database. Consulte [Roles y responsabilidades al configurar la atestación y los enclaves de SGX](always-encrypted-enclaves-plan.md#roles-and-responsibilities-when-configuring-sgx-enclaves-and-attestation).

> [!NOTE]
> Intel SGX no está disponible en las generaciones de hardware que no sean de serie DC. Por ejemplo, Intel SGX no está disponible para el hardware de Gen5 ni para las bases de datos que usan el [modelo de DTU](service-tiers-dtu.md).

> [!IMPORTANT]
> Antes de configurar la generación de hardware de serie DC para la base de datos, compruebe la disponibilidad regional de la serie DC y asegúrese de que comprende sus limitaciones de rendimiento. Para obtener más información, vea [Serie DC](service-tiers-sql-database-vcore.md#dc-series).

Para obtener instrucciones detalladas sobre cómo configurar una base de datos nueva o existente para usar una generación de hardware específica, consulte [Selección de una generación de hardware](service-tiers-sql-database-vcore.md#selecting-a-hardware-generation).
   
## <a name="next-steps"></a>Pasos siguientes

- [Configuración de Azure Attestation para el servidor de Azure SQL Database](always-encrypted-enclaves-configure-attestation.md)

## <a name="see-also"></a>Consulte también

- [Tutorial: Introducción a Always Encrypted con enclaves seguros en Azure SQL Database](always-encrypted-enclaves-getting-started.md)