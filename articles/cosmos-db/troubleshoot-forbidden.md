---
title: Solución de problemas de excepciones de solicitudes prohibidas de Azure Cosmos DB
description: Aprenda a diagnosticar y corregir excepciones de solicitudes prohibidas.
author: ealsur
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.date: 02/05/2021
ms.author: maquaran
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: d1ea89443f1d4eaab359e23dfc201dc732ced75e
ms.sourcegitcommit: 98308c4b775a049a4a035ccf60c8b163f86f04ca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/30/2021
ms.locfileid: "113108880"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-forbidden-exceptions"></a>Diagnóstico y solución de problemas de excepciones de solicitudes prohibidas de Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

El código de estado HTTP 403 significa que se prohíbe que se complete la solicitud.

## <a name="firewall-blocking-requests"></a>Solicitudes de bloqueo de firewall
En este escenario, es habitual ver errores como los siguientes:

```
Request originated from client IP {...} through public internet. This is blocked by your Cosmos DB account firewall settings.
```

```
Request is blocked. Please check your authorization token and Cosmos DB account firewall settings
```

### <a name="solution"></a>Solución
Compruebe que la [configuración actual del firewall](how-to-configure-firewall.md) sea correcta e incluya las direcciones IP o redes a las que está intentando conectarse.
Si las ha actualizado recientemente, tenga en cuenta que los cambios pueden tardar **hasta 15 minutos en aplicarse**.

## <a name="non-data-operations-are-not-allowed"></a>No se permiten operaciones que no son de datos
Este escenario se produce cuando [no se pueden realizar en la cuenta operaciones](how-to-restrict-user-data.md#disallow-the-execution-of-non-data-operations) que no son de datos. En este escenario, es habitual ver errores como los siguientes:

```
Operation 'POST' on resource 'calls' is not allowed through Azure Cosmos DB endpoint
```

### <a name="solution"></a>Solución
Realice la operación mediante Azure Resource Manager, Azure Portal, la CLI de Azure o Azure PowerShell. O bien, vuelva a permitir la ejecución de operaciones que no son de datos.

## <a name="next-steps"></a>Pasos siguientes
* Configuración de [firewall de IP](how-to-configure-firewall.md).
* Configuración del acceso desde [redes virtuales](how-to-configure-vnet-service-endpoint.md).
* Configuración del acceso desde [puntos de conexión privados](how-to-configure-private-endpoints.md).
