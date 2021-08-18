---
title: Conexión de datos de DNS a Azure Sentinel | Microsoft Docs
description: Aprenda a conectar a Azure Sentinel cualquier servidor de nombres de dominio (DNS) que se ejecute en Windows mediante la instalación de un agente en la máquina DNS.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 77af84f9-47bc-418e-8ce2-4414d7b58c0c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/24/2019
ms.author: yelevin
ms.openlocfilehash: f4e6ddf4e3db0213587a9154a424813c3cc5ca83
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183141"
---
# <a name="connect-your-domain-name-server"></a>Conectar con el servidor de nombres de dominio

> [!IMPORTANT]
> El conector de datos de DNS en Azure Sentinel se encuentra actualmente en versión preliminar pública.
> Esta característica se ofrece sin contrato de nivel de servicio y no se recomienda para cargas de trabajo de producción. Es posible que algunas características no sean compatibles o que tengan sus funcionalidades limitadas. Para más información, consulte [Términos de uso complementarios de las Versiones Preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

> [!CAUTION]
> La habilitación de la solución DNS puede provocar un uso elevado de CPU en la máquina de destino. Como se indicó anteriormente, este conector no se recomienda para su uso en una configuración de producción. Estamos trabajando para conseguir una versión estable de este conector de disponibilidad general.

Puede conectar a Azure Sentinel cualquier servidor de nombres de dominio (DNS) que se ejecute en Windows. Para ello, hay que instalar un agente en el equipo DNS. Si se usan registros de DNS, se puede obtener información relacionada con la seguridad, el rendimiento y las operaciones de la infraestructura de DNS de la organización, ya que recopilan, analizan y correlacionan registros de auditoría y análisis, además de otros datos relacionados de los servidores DNS.

Habilitar una conexión de registro DNS permite lo siguiente:
- Identificar a los clientes que intentan resolver nombres de dominio malintencionados
- Identificar los registros de recursos obsoletos
- Identificar los nombres de dominio consultados con más frecuencia y los clientes DNS participativos
- Ver la carga de solicitudes en los servidores DNS
- Ver errores de registro DNS dinámicos

## <a name="connected-sources"></a>Orígenes conectados

En la tabla siguiente se describen los orígenes conectados que son compatibles con esta solución:

| **Origen conectado** | **Soporte técnico** | **Descripción** |
| --- | --- | --- |
| [Agentes de Windows](../azure-monitor/agents/agent-windows.md) | Sí | La solución recopila información de DNS de los agentes de Windows. |
| [Agentes de Linux](../azure-monitor/vm/monitor-virtual-machine.md) | No | La solución no recopila información de DNS de los agentes directos de Linux. |
| [Grupo de administración de System Center Operations](../azure-monitor/agents/om-agents.md) | Sí | La solución recopila información de DNS de los agentes en un grupo de administración de Operations Manager conectado. No se requiere ninguna conexión directa entre el agente de Operations Manager y Azure Monitor. Los datos se reenvían desde el grupo de administración al área de trabajo de Log Analytics. |
| [Cuenta de Almacenamiento de Azure](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace) | No | La solución no usa Azure Storage. |

### <a name="data-collection-details"></a>Detalles de la recopilación de datos

La solución permite recopilar inventario y datos relacionados con eventos de DNS de los servidores DNS donde se ha instalado un agente de Log Analytics. Los datos relacionados con el inventario como, por ejemplo, el número de servidores DNS, las zonas y los registros de recursos se recopilan con la ejecución de cmdlets de Powershell para DNS. Los datos se actualizan una vez cada dos días. Los datos relacionados con eventos se recopilan casi en tiempo real a partir de los [registros analíticos y de auditoría](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)#enhanc) proporcionados por las funcionalidades mejoradas de registro y diagnóstico de Windows Server 2012 R2.


## <a name="connect-your-dns-appliance"></a>Conectar el dispositivo DNS

1. En el portal de Azure Sentinel, seleccione **Data connectors** (Conectores de datos) y elija el icono de **DNS (versión preliminar)** .
1. Si los equipos DNS están en Azure:
    1. Haga clic en **Install agent on Azure Windows virtual machine** (Instalar el agente en la máquina virtual Windows de Azure).
    1. En la lista **Virtual machines** (Máquinas virtuales), seleccione el equipo DNS que quiera transmitir a Azure Sentinel. Asegúrese de que se trata de una máquina virtual Windows.
    1. En la ventana de la máquina virtual que se abre, haga clic en **Connect**(Conectar).  
    1. Haga clic en **Enable** (Habilitar) en la ventana **DNS connector** (Conector DNS). 

2. Si el equipo DNS no está en Azure:
    1. Haga clic en **Install agent on non-Azure machines** (Instalar el agente en máquinas que no son de Azure).
    1. En la ventana **Direct agent** (Agente directo), seleccione **Download Windows agent (64 bit)** (Descargar agente de Windows de [64 bits]) o **Download Windows agent (32 bit)** (Descargar agente de Windows de [32 bits]).
    1. Instale el agente en el equipo DNS. Copie los valores de **Workspace ID** (Identificador de área de trabajo), **Primary key** (Clave principal) y **Secondary key** (Clave secundaria) y úselos cuando se le solicite durante la instalación.

3. Para usar el esquema correspondiente en Log Analytics para encontrar registros DNS, busque **DnsEvents**.

## <a name="validate"></a>Validación 

En Log Analytics, busque el esquema **DnsEvents** y asegúrese de que hay eventos.

## <a name="troubleshooting"></a>Solución de problemas

Si las consultas de búsqueda no aparecen en Azure Sentinel, siga estos pasos para que aparezcan correctamente:
1. Active los [registros de DNS Analytics en los servidores](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn800669(v=ws.11)).
2. Asegúrese de que aparezca DNSEvents en la lista de colecciones de Log Analytics.
3. Active [Azure DNS Analytics](../azure-monitor/insights/dns-analytics.md).
4. En Azure DNS Analytics, en **Configuración**, cambie cualquiera de los valores de configuración, guárdelo, vuelva a dejarlo como estaba si es necesario y guárdelo de nuevo.
5. Compruebe Azure DNS Analytics para asegurarse de que las consultas aparecen ahora.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar dispositivos locales DNS a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).