---
author: dlepow
ms.service: api-management
ms.topic: include
ms.date: 01/26/2021
ms.author: danlep
ms.openlocfilehash: 86b5a62d7aaf16009e59637ebe9a967fce1e0358
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128569625"
---
#### <a name="requirements-for-key-vault-firewall"></a>Requisitos de firewall de Key Vault

Si el [firewall de Key Vault](../articles/key-vault/general/network-security.md) está habilitado en el almacén de claves, los siguientes son requisitos adicionales:

* Para acceder al almacén de claves, debe usar la identidad administrada **asignada por el sistema** de la instancia de API Management.
* En el firewall de Key Vault, establezca la opción **¿Quiere permitir que los servicios de confianza de Microsoft puedan omitir este firewall?**

#### <a name="virtual-network-requirements"></a>Requisitos de red virtual

Si la instancia de API Management se ha implementado en una red virtual, configure también las siguientes opciones de red:

* Habilite un [punto de conexión de servicio](../articles/key-vault/general/overview-vnet-service-endpoints.md) para Azure Key Vault en la subred de API Management.
* Configure una regla de grupo de seguridad de red (NSG) para permitir el tráfico saliente a las [etiquetas de servicio](../articles/virtual-network/service-tags-overview.md) AzureKeyVault y AzureActiveDirectory. 

Para obtener más información, consulte los detalles de configuración de red en [Conexión a una red virtual](../articles/api-management/api-management-using-with-vnet.md#network-configuration-issues).