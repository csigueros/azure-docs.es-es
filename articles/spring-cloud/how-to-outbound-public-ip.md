---
title: Procedimiento para identificar las direcciones IP públicas de salida en Azure Spring Cloud
description: Procedimiento para ver las direcciones IP públicas estáticas de salida para comunicarse con recursos externos, como la base de datos, el almacenamiento, el almacén de claves, etc.
author: karlerickson
ms.author: karler
ms.service: spring-cloud
ms.topic: how-to
ms.date: 09/17/2020
ms.custom: devx-track-java
ms.openlocfilehash: cc3050cb7c92e0e46c02a4a12f218364b94b2d1f
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015545"
---
# <a name="how-to-identify-outbound-public-ip-addresses-in-azure-spring-cloud"></a>Procedimiento para identificar las direcciones IP públicas de salida en Azure Spring Cloud

En esta página se explica cómo ver las direcciones IP públicas estáticas de salida de las aplicaciones de Azure Spring Cloud.  Las direcciones IP públicas se usan para comunicarse con recursos externos, como las bases de datos, el almacenamiento y los almacenes de claves.

## <a name="how-ip-addresses-work-in-azure-spring-cloud"></a>Funcionamiento de las direcciones IP en Azure Spring Cloud

Un servicio de Azure Spring Cloud tiene una o más direcciones IP públicas de salida. El número de direcciones IP públicas de salida puede variar en función de los niveles y otros factores.

Las direcciones IP públicas de salida suelen ser constantes y permanecen inmutables, pero hay excepciones.

## <a name="when-outbound-ips-change"></a>Cuando cambian las direcciones IP de salida

Cada instancia de Azure Spring Cloud tiene un número establecido de direcciones IP públicas de salida en un momento dado. Las conexiones de salida de las aplicaciones, como a una base de datos de back-end, usan una de las direcciones IP públicas de salida como dirección IP de origen. La dirección IP se selecciona aleatoriamente en tiempo de ejecución, por lo que el servicio de back-end debe abrir su firewall para todas las direcciones IP de salida.

El número de direcciones IP públicas de salida cambia cuando realiza una de las acciones siguientes:

- Actualiza la instancia de Azure Spring Cloud entre niveles.
- Envía una incidencia de soporte técnico para obtener más direcciones IP públicas de salida por necesidad empresarial.

## <a name="find-outbound-ips"></a>Búsqueda de las direcciones IP de salida

Para buscar las direcciones IP públicas de salida utilizadas actualmente por la instancia de servicio en Azure Portal, seleccione **Redes** en el panel de navegación izquierdo de la instancia. Se enumeran en el campo **Direcciones IP de salida**.

Puede encontrar la misma información si ejecuta el comando siguiente en Cloud Shell

```azurecli
az spring-cloud show --resource-group <group_name> --name <service_name> --query properties.networkProfile.outboundIps.publicIps --output tsv
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
* [Más información sobre las identidades administradas para recursos de Azure](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/managed-identities-azure-resources/overview.md)
* [Más información sobre Key Vault en Azure Spring Cloud](./tutorial-managed-identities-key-vault.md)
