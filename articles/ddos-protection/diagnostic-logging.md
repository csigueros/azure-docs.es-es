---
title: Registros de flujo e informes de Azure DDoS Protection Estándar
description: Aprenda a configurar informes y registros de flujo.
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: eded4bfc37df72b9163aeac51d66c973b24838b2
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124750253"
---
# <a name="view-and-configure-ddos-diagnostic-logging"></a>Visualización y configuración del registro de diagnósticos de DDoS

La versión estándar de Azure DDoS Protection proporciona información detallada y visualización de ataques con DDoS Attack Analytics. Los clientes que protegen sus redes virtuales frente a ataques DDoS disponen de visibilidad detallada sobre el tráfico de ataques y las acciones llevadas a cabo para mitigarlos mediante informes de mitigación de ataques y registros de flujo de mitigación. La telemetría enriquecida se expone a través de Azure Monitor, incluidas las métricas detalladas mientras dure un ataque DDoS. Las alertas se pueden configurar para cualquiera de las métricas de Azure Monitor que expone DDoS Protection. El registro se puede integrar con [Azure Sentinel](../sentinel/data-connectors-reference.md#azure-ddos-protection), Splunk (Azure Event Hubs), OMS Log Analytics y Azure Storage para realizar análisis avanzados con la interfaz de Azure Monitor Diagnostics.

Los siguientes registros de diagnóstico están disponibles para Azure DDoS Protection Standard: 

- **DDoSProtectionNotifications**: se le enviarán notificaciones cada vez que un recurso de IP pública esté sufriendo un ataque y cuando haya terminado la mitigación del ataque.
- **DDoSMitigationFlowLogs**: Los registros de flujo de mitigación de ataques le permiten revisar casi en tiempo real el tráfico descartado, el tráfico reenviado y otros puntos de datos interesantes durante un ataque DDoS activo. Puede ingerir el flujo constante de estos datos en Azure Sentinel o en sus sistemas SIEM de terceros mediante un centro de eventos para la supervisión casi en tiempo real, realizar acciones y solucionar la necesidad de sus operaciones de defensa.
- **DDoSMitigationReports**: Los informes de mitigación de ataques usan los datos del protocolo Netflow que se agrega para proporcionar información detallada sobre el ataque en el recurso. Cada vez que un recurso IP público es objeto de ataque, tan pronto se inicia la mitigación se inicia la generación de informes. Durante todo el período de mitigación, habrá un informe incremental que se genera cada cinco minutos y un informe posterior a la mitigación. Su finalidad es garantizar que en caso de que el ataque DDoS continúe durante mucho tiempo, podrá ver la instantánea más actual del informe de mitigación cada cinco minutos y un resumen completo una vez finalizada la mitigación del ataque. 
- **AllMetrics**: proporciona todas las métricas posibles disponibles mientras dura un ataque de DDoS. 

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Configurar los registros de diagnóstico de DDoS, como las notificaciones, los informes de mitigación y los registros de flujo de mitigación. 
> * Habilitar el registro de diagnóstico en todas las direcciones IP públicas de un ámbito definido.
> * Ver los datos de registro en los libros.

## <a name="prerequisites"></a>Requisitos previos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- Antes realizar los pasos de este tutorial, primero debe crear un [plan de protección de Azure DDoS Standard](manage-ddos-protection.md) y DDoS Protection Standard debe estar habilitado en una red virtual.
- DDoS supervisa las direcciones IP públicas asignadas a los recursos dentro de una red virtual. Si no tiene ningún recurso con direcciones IP públicas en la red virtual, primero debe crear un recurso con una dirección IP pública. Puede supervisar la dirección IP pública de todos los recursos implementados a través de Resource Manager (no clásico) que aparecen en [Red virtual para servicios de Azure](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (incluidas las instancias de Azure Load Balancer donde las máquinas virtuales de back-end se encuentran en la red virtual), excepto para entornos de Azure App Service. Para continuar con este tutorial, puede crear rápidamente una máquina virtual [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) o [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json).    

## <a name="configure-ddos-diagnostic-logs"></a>Configuración de los registros de diagnóstico de DDoS

Si quiere habilitar automáticamente el registro de diagnóstico en todas las direcciones IP públicas dentro de un entorno, vaya a [Habilitación del registro de diagnóstico en todas las direcciones IP públicas](#enable-diagnostic-logging-on-all-public-ips).

1. Seleccione **Todos los servicios** en la parte superior izquierda del portal.
2. Escriba *Monitor* en el cuadro **Filtrar**. Seleccione **Monitor** cuando aparezca en los resultados.
3. En **Configuración**, seleccione **Configuración de diagnóstico**.
4. Seleccione la **suscripción** y el **grupo de recursos** que contienen la dirección IP pública que desea registrar.
5. En **Tipo de recurso**, seleccione **Dirección IP pública** y, luego, elija la dirección IP pública específica para la que quiere habilitar los registros.
6. Seleccione **Agregar configuración de diagnóstico**. En **Detalles de la categoría**, seleccione todas las opciones que necesita y, luego, elija **Guardar**.

    ![Configuración de diagnóstico de DDoS](./media/ddos-attack-telemetry/ddos-diagnostic-settings.png)

7. En **Detalles del destino**, seleccione todas las opciones necesarias:

    - **Archivar en una cuenta de almacenamiento**: los datos se escriben en una cuenta de Azure Storage. Para más información sobre esta opción, consulte [Archivo de registros de recursos](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-storage).
    - **Transmisión a un centro de eventos**: permite que un receptor de registros seleccione los registros mediante una instancia de Azure Event Hub. Los centros de eventos habilitan la integración con Splunk y otros sistemas SIEM. Para más información sobre esta opción, consulte [Transmisión de registros de recursos a un centro de eventos](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-azure-event-hubs).
    - **Enviar a Log Analytics**: Escribe registros en el servicio Azure Monitor. Para obtener más información sobre esta opción, consulte [Recopilación de registros para su uso en los registros de Azure Monitor](../azure-monitor/essentials/resource-logs.md?toc=%2fazure%2fvirtual-network%2ftoc.json#send-to-log-analytics-workspace).

### <a name="query-ddos-protection-logs-in-log-analytics-workspace"></a>Consulta de registros de protección contra DDOS en un área de trabajo de Log Analytics

#### <a name="ddosprotectionnotifications-logs"></a>Registros DDoSProtectionNotifications

1. En la hoja **Áreas de trabajo de Log Analytics**, seleccione su área de trabajo.

4. En **General**, haga clic en **Registros**.

5. En el Explorador de consultas, escriba la siguiente consulta de Kusto. Cambie el intervalo de tiempo a "Personalizado" e indique un intervalo correspondiente a los últimos 3 meses. A continuación, presione "Ejecutar".

    ```kusto
    AzureDiagnostics
    | where Category == "DDoSProtectionNotifications"
    ```

#### <a name="ddosmitigationflowlogs"></a>DDoSMitigationFlowLogs

1. Ahora, cambie la consulta por lo siguiente, manteniendo el mismo intervalo de tiempo, y presione "Ejecutar".

    ```kusto
    AzureDiagnostics
    | where Category == "DDoSMitigationFlowLogs"
    ```

#### <a name="ddosmitigationreports"></a>DDoSMitigationReports

1. Ahora, cambie la consulta por lo siguiente, manteniendo el mismo intervalo de tiempo, y presione "Ejecutar".

    ```kusto
    AzureDiagnostics
    | where Category == "DDoSMitigationReports"
    ```

### <a name="log-schemas"></a>Esquemas de registro

En la tabla siguiente se muestran los nombres y las descripciones de los campos:

# <a name="ddosprotectionnotifications"></a>[DDoSProtectionNotifications](#tab/DDoSProtectionNotifications)

| Nombre del campo | Descripción |
| --- | --- |
| **TimeGenerated** | Fecha y hora en formato UTC en que se creó la notificación. |
| **ResourceId** | Identificador de recurso de la dirección IP pública. |
| **Categoría** | En el caso de las notificaciones, será `DDoSProtectionNotifications`.|
| **ResourceGroup** | El grupo de recursos que contiene la dirección IP pública y la red virtual. |
| **SubscriptionId** | El identificador de la suscripción del plan de protección contra DDoS. |
| **Recurso** | El nombre de la dirección IP pública. |
| **ResourceType** | Siempre será `PUBLICIPADDRESS`. |
| **OperationName** | En el caso de las notificaciones, será `DDoSProtectionNotifications`.  |
| **Mensaje** | Detalles del ataque. |
| **Type** | Tipo de notificación. Los valores posibles son `MitigationStarted`. `MitigationStopped`. |
| **PublicIpAddress** | La dirección IP pública. |

# <a name="ddosmitigationflowlogs"></a>[DDoSMitigationFlowLogs](#tab/DDoSMitigationFlowLogs)

| Nombre del campo | Descripción |
| --- | --- |
| **TimeGenerated** | Fecha y hora en formato UTC en que se creó el registro de flujo. |
| **ResourceId** | Identificador de recurso de la dirección IP pública. |
| **Categoría** | En el caso de los registros de flujo, será `DDoSMitigationFlowLogs`.|
| **ResourceGroup** | El grupo de recursos que contiene la dirección IP pública y la red virtual. |
| **SubscriptionId** | El identificador de la suscripción del plan de protección contra DDoS. |
| **Recurso** | El nombre de la dirección IP pública. |
| **ResourceType** | Siempre será `PUBLICIPADDRESS`. |
| **OperationName** | En el caso de los registros de flujo, será `DDoSMitigationFlowLogs`. |
| **Mensaje** | Detalles del ataque. |
| **SourcePublicIpAddress** | La dirección IP pública del cliente que genera tráfico hacia la dirección IP pública. |
| **SourcePort** | Número de puerto comprendido entre 0 y 65535. |
| **DestPublicIpAddress** | La dirección IP pública. |
| **DestPort** | Número de puerto comprendido entre 0 y 65535. |
| **Protocolo** | Tipo de protocolo. Los valores posibles son `tcp`, `udp` y `other`.|

# <a name="ddosmitigationreports"></a>[DDoSMitigationReports](#tab/DDoSMitigationReports)

| Nombre del campo | Descripción |
| --- | --- |
| **TimeGenerated** | Fecha y hora en formato UTC en que se creó el informe. |
| **ResourceId** | Identificador de recurso de la dirección IP pública. |
| **Categoría** | En el caso de las notificaciones, será `DDoSMitigationReports`.|
| **ResourceGroup** | El grupo de recursos que contiene la dirección IP pública y la red virtual. |
| **SubscriptionId** | El identificador de la suscripción del plan de protección contra DDoS. |
| **Recurso** | El nombre de la dirección IP pública. |
| **ResourceType** | Siempre será `PUBLICIPADDRESS`. |
| **OperationName** | En el caso de los informes de mitigación, será `DDoSMitigationReports`. |
| **ReportType** | Los valores posibles son `Incremental` y `PostMitigation`.|
| **MitigationPeriodStart** | Fecha y hora en formato UTC en que se inició la mitigación.  |
| **MitigationPeriodEnd** | Fecha y hora en formato UTC en que finalizó la mitigación. |
| **IPAddress** | La dirección IP pública. |
| **AttackVectors** |  Desglose de los tipos de ataque. Entre las claves se incluyen `TCP SYN flood`, `TCP flood`, `UDP flood`, `UDP reflection` y `Other packet flood`.|
| **TrafficOverview** |  Desglose del tráfico de ataque. Entre las claves se incluyen `Total packets`, `Total packets dropped`, `Total TCP packets`, `Total TCP packets dropped`, `Total UDP packets`, `Total UDP packets dropped`, `Total Other packets` y `Total Other packets dropped`. |
| **Protocolos** | Desglose de los protocolos implicados. Entre las claves se incluyen `TCP`, `UDP` y `Other`. |
| **DropReasons** | Desglose de los motivos de los paquetes descartados. Entre las claves se incluyen `Protocol violation invalid TCP syn`, `Protocol violation invalid TCP`, `Protocol violation invalid UDP`, `UDP reflection`, `TCP rate limit exceeded`, `UDP rate limit exceeded`, `Destination limit exceeded`, `Other packet flood`, `Rate limit exceeded` y `Packet was forwarded to service`. |
| **TopSourceCountries** | Desglose de los 10 primeros países de origen del tráfico entrante. |
| **TopSourceCountriesForDroppedPackets** | Desglose de los 10 primeros países de origen del tráfico de ataque que se ha mitigado. |
| **TopSourceASNs** | Desglose de los 10 primeros números de sistema autónomo (ASN) de origen del tráfico entrante.  |
| **SourceContinents** | Desglose de los continentes de origen del tráfico entrante. |
***

## <a name="enable-diagnostic-logging-on-all-public-ips"></a>Habilitación del registro de diagnóstico en todas las direcciones IP públicas

Esta [directiva integrada](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F752154a7-1e0f-45c6-a880-ac75a7e4f648) habilita automáticamente el registro de diagnóstico en todos los registros de IP pública de un ámbito definido. Para obtener una lista completa de directivas integradas, consulte [Definiciones de directivas integradas de Azure Policy para Azure DDoS Protection estándar](policy-reference.md).

## <a name="view-log-data-in-workbooks"></a>Visualización de los datos de registro en los libros

### <a name="azure-sentinel-data-connector"></a>Conector de datos de Azure Sentinel

Puede conectar los registros a Azure Sentinel, ver y analizar los datos en los libros, crear alertas personalizadas e incorporarlas a procesos de investigación. Para conectarse a Azure Sentinel, consulte [Conexión a Azure Sentinel](../sentinel/data-connectors-reference.md#azure-ddos-protection). 

![Conector de DDoS de Azure Sentinel](./media/ddos-attack-telemetry/azure-sentinel-ddos.png)

### <a name="azure-ddos-protection-workbook"></a>Libro de Azure DDoS Protection

[Esta plantilla de Azure Resource Manager](https://aka.ms/ddosworkbook) se puede usar para implementar un libro de análisis de ataques. Este libro le permite visualizar los datos de ataques en varios paneles que se pueden filtrar para comprender fácilmente qué está en riesgo. 

[![Implementación en Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%20DDoS%20Protection%2FWorkbook%20-%20Azure%20DDOS%20monitor%20workbook%2FAzureDDoSWorkbook_ARM.json)

![Libro de DDoS Protection](./media/ddos-attack-telemetry/ddos-attack-analytics-workbook.png)

## <a name="validate-and-test"></a>Validación y prueba

Para simular un ataque DDoS para validar los registros, consulte [Validación de la detección de DDoS](test-through-simulations.md).

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

- Configurar los registros de diagnóstico de DDoS, como las notificaciones, los informes de mitigación y los registros de flujo de mitigación. 
- Habilitar el registro de diagnóstico en todas las direcciones IP públicas de un ámbito definido.
- Ver los datos de registro en los libros.

Para aprender a configurar alertas de ataque, continúe con el siguiente tutorial.

> [!div class="nextstepaction"]
> [Visualización y configuración de alertas de protección contra DDoS](alerts.md)