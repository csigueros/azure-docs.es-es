---
title: Migración de agentes heredados al nuevo agente de Azure Monitor
description: Guía para migrar desde los agentes heredados existentes al nuevo agente de Azure Monitor (AMA) y reglas de recopilación de datos (DCR).
ms.topic: conceptual
author: shseth
ms.author: shseth
ms.date: 7/12/2021
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 01c2f7b49b051e9166ad1ceef99fc816e611f579
ms.sourcegitcommit: 0396ddf79f21d0c5a1f662a755d03b30ade56905
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122271971"
---
# <a name="migrate-from-log-analytics-agents"></a>Migración desde agentes de Log Analytics
En este artículo se proporcionan instrucciones generales sobre cuándo y cómo migrar al nuevo agente de Azure Monitor (AMA) y las reglas de recopilación de datos (DCR). Este artículo se actualizará cuando haya nuevas herramientas de migración disponibles.


## <a name="review"></a>Revisión
- Para determinar si debe migrar al nuevo agente de Azure Monitor ahora o más tarde, consulte la guía de [Introducción al agente de Azure Monitor](./azure-monitor-agent-overview.md#should-i-switch-to-the-azure-monitor-agent).
- Con relación al agente de Azure Monitor, revise las nuevas funcionalidades; la disponibilidad de las características, los servicios y las soluciones existentes; y las limitaciones actuales en [Introducción al agente de Azure Monitor](./agents-overview.md#azure-monitor-agent).


## <a name="test-migration-by-using-the-azure-portal"></a>Prueba de la migración mediante Azure Portal
1. Para garantizar una implementación segura durante la migración, comience las pruebas con recursos del entorno que no es de producción que ejecuten el agente de Log Analytics existente. Después de validar los datos recopilados en estos recursos de prueba, realice la implementación en producción siguiendo los mismos pasos.
1. Vaya a **Monitor** > **Configuración** > **Reglas de recopilación de datos** y [cree reglas](./data-collection-rule-azure-monitor-agent.md#create-rule-and-association-in-azure-portal) para empezar a recopilar parte de los tipos de datos existentes. Cuando use la GUI del portal, realiza por usted los siguientes pasos en todos los recursos de destino:
    - Habilita una identidad administrada asignada por el sistema.
    - Instala la extensión del agente de Azure Monitor.
    - Crea e implementa asociaciones de reglas de recopilación de datos.
1. Compruebe que los datos fluyan según lo esperado a través del agente de Azure Monitor. Consulte si hay nuevos valores de versión del agente en la tabla **Latido**. Asegúrese de que coincida con los datos que fluyen a través del agente de Log Analytics existente.


## <a name="at-scale-migration-by-using-policies"></a>Migración a escala usando directivas
1. Empiece analizando la configuración de supervisión actual con MMA/OMS mediante los siguientes criterios:
    - Orígenes, como máquinas virtuales, conjuntos de escalado de máquinas virtuales y servidores locales
    - Orígenes de datos, como contadores de rendimiento, registros de eventos de Windows y Syslog
    - Destinos, como áreas de trabajo de Log Analytics
1. [Cree reglas de recopilación de datos](/rest/api/monitor/datacollectionrules/create#examples) usando la configuración anterior. Como procedimiento recomendado, es posible que desee tener reglas de recopilación de datos independientes para orígenes de Windows y Linux. O bien puede que desee reglas de recopilación de datos independientes para equipos individuales con diferentes necesidades de recopilación de datos.
1. [Habilite una identidad administrada asignada por el sistema](../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#system-assigned-managed-identity) en recursos de destino.
1. Instale la extensión del agente de Azure Monitor. Implemente asociaciones de reglas de recopilación de datos en todos los recursos de destino usando la [iniciativa de directiva integrada](../deploy-scale.md#built-in-policy-initiatives). Proporcione la regla de recopilación de datos anterior como parámetro de entrada. 
1. Compruebe que los datos fluyan según lo esperado a través del agente de Azure Monitor. Consulte si hay nuevos valores de versión del agente en la tabla **Latido**. Asegúrese de que coincida con los datos que fluyen a través del agente de Log Analytics existente.
1. Valide todas las dependencias de nivel inferior, como paneles, alertas y trabajos de runbook. Todos los libros siguen funcionando ahora usando datos del nuevo agente.
1. [Desinstale el agente de Log Analytics](./agent-manage.md#uninstall-agent) de los recursos. No lo desinstale si necesita usarlo para escenarios de System Center Operations Manager u otras soluciones que aún no estén disponibles en el agente de Azure Monitor.
1. Borre los archivos de configuración, las claves de área de trabajo o los certificados que usaba anteriormente el agente de Log Analytics.


