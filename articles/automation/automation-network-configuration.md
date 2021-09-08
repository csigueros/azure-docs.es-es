---
title: Detalles de configuración de la red de Azure Automation
description: En este artículo se proporcionan detalles de la información de red requerida por Azure Automation State Configuration, Azure Automation Hybrid Runbook Worker, Update Management y Seguimiento de cambios e inventario.
ms.author: magoedte
ms.topic: conceptual
ms.date: 01/26/2021
ms.openlocfilehash: 2c3b00a3d60db16c3b768494f8630587a16033d8
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114465805"
---
# <a name="azure-automation-network-configuration-details"></a>Detalles de configuración de la red de Azure Automation

En esta página se proporcionan los detalles de red necesarios para [Hybrid Runbook Worker y State Configuration](#hybrid-runbook-worker-and-state-configuration) y para [Update Management y Seguimiento de cambios e inventario](#update-management-and-change-tracking-and-inventory).

## <a name="hybrid-runbook-worker-and-state-configuration"></a>Hybrid Runbook Worker y State Configuration

El siguiente puerto y las direcciones URL son necesarios para Hybrid Runbook Worker, y para [State Configuration de Automation](automation-dsc-overview.md) a fin de comunicarse con Azure Automation.

* Puerto: solo se requiere el puerto 443 para el acceso a Internet
* Dirección URL global: `*.azure-automation.net`
* Dirección URL global de US Gov Virginia: `*.azure-automation.us`
* Servicio del agente: `https://<workspaceId>.agentsvc.azure-automation.net`

### <a name="network-planning-for-hybrid-runbook-worker"></a>Planeación de la red para Hybrid Runbook Worker

Para que una instancia de Hybrid Runbook Worker de usuario o sistema se conecten y se registren con Azure Automation, debe tener acceso al número de puerto y a las direcciones URL que se describen en esta sección. El rol de trabajo también ha de tener acceso a los [puertos y las direcciones URL necesarios para que el agente de Log Analytics](../azure-monitor/agents/agent-windows.md) se conecte al área de trabajo Log Analytics de Azure Monitor.

Si tiene una cuenta de Automation definida para una región específica, puede restringir la comunicación de Hybrid Runbook Worker con ese centro de datos regional. Revise los registros DNS necesarios en los [registros DNS usados por Azure Automation](how-to/automation-region-dns-records.md).

### <a name="configuration-of-private-networks-for-state-configuration"></a>Configuración de redes privadas para State Configuration

Si los nodos se encuentran en una red privada, se necesitan el puerto y las direcciones URL definidas anteriormente. Estos recursos proporcionan conectividad de red para que el nodo administrado y el DSC permitido se comuniquen con Azure Automation.

Si usa recursos de DSC que se comunican entre nodos, como los [recursos WaitFor*](/powershell/scripting/dsc/reference/resources/windows/waitForAllResource), también debe permitir el tráfico entre nodos. Consulte la documentación de cada recurso de DSC para comprender estos requisitos de red.

Para comprender los requisitos del cliente para TLS 1.2, consulte el artículo sobre [TLS 1.2 para Azure Automation](automation-managing-data.md#tls-12-for-azure-automation).

## <a name="update-management-and-change-tracking-and-inventory"></a>Update Management y Seguimiento de cambios e inventario

Las direcciones de esta tabla son necesarias tanto para Update Management como para Seguimiento de cambios e inventario. El párrafo que sigue a la tabla también se aplica a ambos servicios.

La comunicación con estas direcciones usa el **puerto 443**.

|Azure Public  |Azure Government  |
|---------|---------|
|\*.ods.opinsights.azure.com    | \*.ods.opinsights.azure.us         |
|\*.oms.opinsights.azure.com     | \*.oms.opinsights.azure.us        |
|\*.blob.core.windows.net | \*.blob.core.usgovcloudapi.net|
|\*.azure-automation.net | \*.azure-automation.us|

Al crear reglas de seguridad de grupo de red o configurar Azure Firewall para permitir el tráfico en el servicio Automation y en el área de trabajo de Log Analytics, utilice las [etiquetas de servicio](../virtual-network/service-tags-overview.md#available-service-tags) **GuestAndHybridManagement** y **AzureMonitor**. De esta forma, se simplifica la administración continua de las reglas de seguridad de red. Para conectarse al servicio Automation desde las máquinas virtuales de Azure de forma segura y privada, revise [Uso de Azure Private Link](./how-to/private-link-security.md). Para obtener información de intervalos y la etiqueta de servicio actual para incluirla como parte de las configuraciones del firewall local, consulte [Archivos JSON descargables](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files).

## <a name="next-steps"></a>Pasos siguientes

* Obtenga información sobre la [Introducción a Update Management de Azure Automation](update-management\overview.md).
* Obtenga información acerca de [Hybrid Runbook Worker](automation-hybrid-runbook-worker.md).
* Obtenga información acerca de [Seguimiento de cambios e inventario](change-tracking\overview.md).
* Obtenga información acerca de [State Configuration de Automation](automation-dsc-overview.md).