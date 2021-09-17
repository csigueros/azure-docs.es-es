---
title: Configuración de las reglas de firewall de IP
description: En este artículo se muestra cómo configurar reglas de firewall de IP en Azure Synapse Analytics.
author: ashinMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 08/15/2021
ms.author: seshin
ms.reviewer: wiassaf
ms.openlocfilehash: a375c4c99e3b86706f9fed7bc52f3064995cd21c
ms.sourcegitcommit: 05dd6452632e00645ec0716a5943c7ac6c9bec7c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122252793"
---
# <a name="azure-synapse-analytics-ip-firewall-rules"></a>Reglas de firewall de IP de Azure Synapse Analytics

En este artículo se explican las reglas de firewall de IP y se muestra cómo configurarlas en Azure Synapse Analytics.

## <a name="ip-firewall-rules"></a>Reglas de firewall de IP

Las reglas de firewall de IP conceden o deniegan el acceso al área de trabajo de Synapse en función de la dirección IP de origen de cada solicitud. Se pueden configurar reglas de firewall de IP para el área de trabajo. Las reglas de firewall de IP configuradas en el nivel de área de trabajo se aplican a todos los puntos de conexión públicos del área de trabajo (grupos de SQL dedicado, grupo de SQL sin servidor y desarrollo).

## <a name="create-and-manage-ip-firewall-rules"></a>Creación y administración de reglas de firewall de IP

Hay dos maneras de agregar reglas de firewall de IP a un área de trabajo de Azure Synapse. Para agregar un firewall de IP al área de trabajo, seleccione **Redes** y active **Uso permitido de conexiones de todas las direcciones IP** al crear el área de trabajo.

> [!Important]
> Esta característica solo está disponible para áreas de trabajo de Azure Synapse no asociadas a una red virtual administrada.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-connections-all-ip-addresses.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-connections-all-ip-addresses.png" alt-text="Captura de pantalla que resalta la pestaña Seguridad y la casilla &quot;Uso permitido de conexiones de todas las direcciones IP&quot;.":::


También puede agregar reglas de firewall de IP a un área de trabajo de Synapse después de crear el área de trabajo. En Azure Portal, vaya a **Seguridad** y seleccione **Firewalls**. Para agregar una nueva regla de firewall de IP, asígnele un nombre, una dirección IP inicial y una dirección IP final. Seleccione **Guardar** cuando haya terminado.

:::image type="content" source="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-firewalls-add-client-ip.png" lightbox="./media/synpase-workspace-ip-firewall/azure-synapse-workspace-networking-firewalls-add-client-ip.png" alt-text="Captura de pantalla de la página Redes de un área de trabajo de Synapse en la que se resaltan los campos Agregar IP de cliente y Reglas.":::

## <a name="connect-to-synapse-from-your-own-network"></a>Conexión con Synapse desde su propia red

Puede conectarse al área de trabajo de Synapse con Synapse Studio. También puede usar SQL Server Management Studio (SSMS) para conectarse a los recursos de SQL (grupos de SQL dedicado y grupo de SQL sin servidor) del área de trabajo.

Asegúrese de que el firewall de la red y del equipo local permita la comunicación saliente en los puertos TCP 80, 443 y 1443 para Synapse Studio.

Además, debe permitir la comunicación saliente en el puerto UDP 53 para Synapse Studio. Para conectarse mediante herramientas como SSMS y Power BI, debe permitir la comunicación saliente en el puerto TCP 1433.


## <a name="next-steps"></a>Pasos siguientes

Creación de un [área de trabajo de Azure Synapse](../quickstart-create-workspace.md)

Creación de un área de trabajo de Azure Synapse con una [red virtual de área de trabajo administrada](./synapse-workspace-managed-vnet.md)