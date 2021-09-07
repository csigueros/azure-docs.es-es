---
title: Migración de recursos de seguridad de Azure de Azure Alemania a Azure global
description: En este artículo se proporciona información sobre cómo migrar los recursos de seguridad de Azure desde Azure Alemania a Azure global.
ms.topic: article
ms.date: 10/16/2020
author: gitralf
ms.author: ralfwi
ms.service: germany
ms.custom: bfmigrate
ms.openlocfilehash: db8c13e214145df91f4b385f95c54e8084f3206c
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/26/2021
ms.locfileid: "117029236"
---
# <a name="migrate-security-resources-to-global-azure"></a>Migración de recursos de seguridad a Azure global

[!INCLUDE [closureinfo](../../includes/germany-closure-info.md)]

En este artículo se proporciona información que puede ayudarle a migrar los recursos de seguridad de Azure desde Azure Alemania a Azure global.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-active-directory"></a>Azure Active Directory

Para obtener información acerca de cómo migrar Azure Active Directory, consulte [Migración de identidades](./germany-migration-identity.md#azure-active-directory).

## <a name="key-vault"></a>Key Vault

Algunas de las características de Azure Key Vault no se pueden migrar desde Azure Alemania a Azure global.

### <a name="encryption-keys"></a>Claves de cifrado

Las claves de cifrado no se pueden migrar. Cree nuevas claves en la región de destino y, después, úselas para proteger el recurso de destino (por ejemplo, Azure Storage o Azure SQL Database). Migre de forma segura los datos de la región anterior a la nueva.

### <a name="application-secrets"></a>Secretos de aplicación

Los secretos de aplicación son certificados, claves de cuenta de almacenamiento y otros secretos relacionados con la aplicación. Durante una migración, primero cree almacén de claves en Azure global. Luego, realice una de las siguientes acciones:

- Cree nuevos secretos de aplicación.
- Lea los secretos en Azure Alemania actuales y escriba el valor en el nuevo almacén.

```powershell
Get-AzKeyVaultSecret -vaultname mysecrets -name Deploydefaultpw
```

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Key Vault](../key-vault/index.yml).
- Revise [Introducción a Key Vault](../key-vault/general/overview.md).
- Examine los [cmdlets de PowerShell para Key Vault](/powershell/module/az.keyvault/).

## <a name="vpn-gateway"></a>VPN Gateway

En este momento no se admite la migración de una instancia de Azure VPN Gateway desde Azure Alemania a Azure global. Se recomienda crear y configurar una nueva instancia de VPN Gateway en Azure global.

Puede recopilar la información sobre la configuración actual de VPN Gateway mediante el portal o PowerShell. En PowerShell, use un conjunto de cmdlets que comienzan por `Get-AzVirtualNetworkGateway*`.

Asegúrese de que actualice la configuración local. Además, elimine todas las reglas existentes en los intervalos de direcciones IP antiguas después de actualizar el entorno de red de Azure.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de VPN Gateway](../vpn-gateway/index.yml).
- Aprenda cómo [crear de una conexión de sitio a sitio](../vpn-gateway/tutorial-site-to-site-portal.md).
- Revise los cmdlets [Get AzVirtualNetworkGateway](/powershell/module/az.network/get-azvirtualnetworkgateway) de PowerShell.
- Lea la entrada de blob [Create a site-to-site connection](/archive/blogs/ralfwi/connecting-clouds) (Creación de una conexión de sitio a sitio).
  
## <a name="application-gateway"></a>Application Gateway

En este momento no se admite la migración de una instancia de Azure Application Gateway desde Azure Alemania a Azure global. Se recomienda crear y configurar una nueva puerta de enlace en Azure global.

Puede recopilar la información sobre la configuración actual la puerta de enlace mediante el portal o PowerShell. En PowerShell, use un conjunto de cmdlets que comienzan por `Get-AzApplicationGateway*`.

Para obtener más información:

- Actualice sus conocimientos completando los [tutoriales de Application Gateway](../web-application-firewall/ag/application-gateway-web-application-firewall-portal.md).
- Aprenda a [crear una puerta de enlace de aplicaciones](../application-gateway/quick-create-portal.md).
- Revise los cmdlets [Get AzApplicationGateway](/powershell/module/az.network/get-azapplicationgateway) de PowerShell.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre herramientas, técnicas y recomendaciones para migrar los recursos en las siguientes categorías de servicio:

- [Proceso](./germany-migration-compute.md)
- [Redes](./germany-migration-networking.md)
- [Storage](./germany-migration-storage.md)
- [Web](./germany-migration-web.md)
- [Bases de datos](./germany-migration-databases.md)
- [Analytics](./germany-migration-analytics.md)
- [IoT](./germany-migration-iot.md)
- [Integración](./germany-migration-integration.md)
- [Identidad](./germany-migration-identity.md)
- [Herramientas de administración](./germany-migration-management-tools.md)
- [Elementos multimedia](./germany-migration-media.md)