---
title: Comprobación de la seguridad de los puertos en el firewall integrado
description: Aprenda a comprobar la protección de firewall integrado en Instancia administrada de Azure SQL.
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: how-to
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: mathoma
ms.date: 12/04/2018
ms.openlocfilehash: 21401f20fe1f71b49861a3ddae8a5a3cdb999408
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741195"
---
# <a name="verify-the-azure-sql-managed-instance-built-in-firewall"></a>Comprobación del firewall integrado de Instancia administrada de Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Las [reglas de seguridad de entrada obligatorias](connectivity-architecture-overview.md#mandatory-inbound-security-rules-with-service-aided-subnet-configuration) de Azure SQL Managed Instance requieren la apertura de los puertos administrados 9000, 9003, 1438, 1440 y 1452 desde **Cualquier origen** en el grupo de seguridad de red (NSG) que protege SQL Managed Instance. Aunque estos puertos están abiertos en el nivel de NSG, el firewall integrado los protege en el nivel de red.

## <a name="verify-firewall"></a>Comprobar el firewall

Para comprobar estos puertos, use cualquier herramienta de escáner de seguridad para probar estos puertos. La captura de pantalla siguiente muestra cómo usar una de estas herramientas.

![Comprobación del firewall integrado](./media/management-endpoint-verify-built-in-firewall/03_verify_firewall.png)

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de SQL Managed Instance y la conectividad, consulte [Arquitectura de conectividad de Azure SQL Managed Instance](connectivity-architecture-overview.md).
