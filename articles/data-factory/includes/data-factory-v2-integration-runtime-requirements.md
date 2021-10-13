---
author: jianleishen
ms.service: data-factory
ms.topic: include
ms.date: 09/29/2021
ms.author: jianleishen
ms.openlocfilehash: a5fbf3071c134b52fb053afc88f6efb51a5ea2f8
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129725484"
---
<!--
    Separate the generic requirement on Self-hosted Integration Runtime setup from connector articles.
-->
Si el almacén de datos se encuentra en una red local, una red virtual de Azure o una nube privada virtual de Amazon, debe configurar un [entorno de ejecución de integración autohospedado](../create-self-hosted-integration-runtime.md) para conectarse a él.

Si el almacén de datos es un servicio de datos en la nube administrado, puede usar Azure Integration Runtime. Si el acceso está restringido a las direcciones IP que están aprobadas en las reglas de firewall, puede agregar [direcciones IP de Azure Integration Runtime](../azure-integration-runtime-ip-addresses.md) a la lista de permitidos. 

También puede usar la característica del [entorno de ejecución de integración de red virtual administrada](../tutorial-managed-virtual-network-on-premise-sql-server.md) de Azure Data Factory para acceder a la red local sin instalar ni configurar un entorno de ejecución de integración autohospedado.

Consulte [Estrategias de acceso a datos](../data-access-strategies.md) para más información sobre los mecanismos de seguridad de red y las opciones que admite Data Factory.
