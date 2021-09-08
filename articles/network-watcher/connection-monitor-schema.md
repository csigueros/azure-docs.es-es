---
title: Esquema de Connection Monitor de Azure Network Watcher | Microsoft Docs
description: Información sobre el esquema de Connection Monitor de Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: mjha
manager: vinigam
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/05/2021
ms.author: mjha
ms.openlocfilehash: 8cc2528a4a8f8a285e8bbf2f99859155c1d9861d
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451039"
---
# <a name="azure-network-watcher-connection-monitor-schema"></a>Esquema de Connection Monitor de Azure Network Watcher

Connection Monitor proporciona una supervisión de conexión unificada de un extremo a otro en Azure Network Watcher. La característica Connection Monitor admite implementaciones híbridas y en la nube de Azure. Network Watcher proporciona herramientas para supervisar, diagnosticar y ver las métricas relacionadas con la conectividad de las implementaciones de Azure.

Estos son algunos casos de uso de Connection Monitor:

- La VM de servidor front-end web se comunica con una VM de servidor de bases de datos en una aplicación de varios niveles. Quiere comprobar la conectividad de red entre las dos VM.
- Quiere que las VM en la región Este de EE. UU. hagan ping a las VM en la región Centro de EE. UU. y quiere comparar las latencias de red entre regiones.
- Tiene varios sitios de oficina locales en Seattle, Washington y en Ashburn, Virginia. Los sitios de oficina se conectan a las direcciones URL de Microsoft 365. En el caso de los usuarios de las direcciones URL de Microsoft 365, compare las latencias entre Seattle y Ashburn.
- La aplicación híbrida necesita conectividad a un punto de conexión de Azure Storage. El sitio local y la aplicación de Azure se conectan al mismo punto de conexión de Azure Storage. Quiere comparar las latencias del sitio local con las latencias de la aplicación de Azure.
- Quiere comprobar la conectividad entre las instalaciones locales y las máquinas virtuales de Azure que hospedan la aplicación en la nube.

Estas son algunas ventajas de Connection Monitor:

* Experiencia unificada e intuitiva para las necesidades de supervisión híbrida y de Azure
* Supervisión de conectividad entre regiones y entre áreas de trabajo
* Frecuencias de sondeo más altas y una mejor visibilidad del rendimiento de la red
* Alertas más rápidas para las implementaciones híbridas
* Compatibilidad con las comprobaciones de conectividad basadas en HTTP, TCP e ICMP 
* Las métricas y Log Analytics admiten configuraciones de pruebas de Azure y que no son de Azure

Hay dos tipos de registros y datos ingeridos en Log Analytics.
Los datos de prueba (consulta NWConnectionMonitorTestResult) se actualizan en función de la frecuencia de supervisión de un grupo de prueba determinado.
Los datos de ruta de acceso (consulta NWConnectionMonitorPathResult) se actualizan cuando hay un cambio significativo en el porcentaje de pérdida o en el tiempo de ida y vuelta.
Por lo tanto, durante algún tiempo, los datos de prueba pueden seguir actualizados mientras los datos de ruta de acceso no se actualizan con frecuencia, ya que ambos son independientes.

## <a name="connection-monitor-tests-schema"></a>Esquema de pruebas de Connection Monitor

A continuación, se enumeran los campos del esquema de datos de pruebas de Connection Monitor y lo que significan. 

| Campo  |    Descripción   |
|---|---|
| TimeGenerated | Marca de tiempo (UTC) de cuando se generó el registro |
| RecordId  | Identificador de registro para la identificación única del registro de resultados de la prueba |
| ConnectionMonitorResourceId   | Identificador de recurso de Connection Monitor de la prueba |
| TestGroupName | Nombre del grupo de pruebas al que pertenece la prueba |
| TestConfigurationName | Nombre de la configuración de prueba a la que pertenece la prueba |
| SourceType    | Tipo de la máquina de origen configurada para la prueba |
| SourceResourceId  | Identificador de recurso de la máquina de origen |
| SourceAddress | Dirección del origen configurado para la prueba |
| SourceSubnet  | Subred del origen |
| SourceIP  | Dirección IP del origen |
| SourceName    | Nombre del punto de conexión del origen |
| SourceAgentId | Identificador del agente de origen |
| DestinationPort   | Puerto de destino configurado para la prueba |
| DestinationType   | Tipo de la máquina de destino configurada para la prueba |
| DestinationResourceId | Identificador de recurso de la máquina de destino |
| DestinationAddress    | Dirección del destino configurado para la prueba |
| DestinationSubnet | Si procede, la subred del destino |
| DestinationIP | Dirección IP del destino |
| DestinationName   | Nombre del punto de conexión de destino |
| DestinationAgentId    | Identificador del agente de destino |
| Protocolo  | Protocolo de la prueba |
| ChecksTotal   | Número total de comprobaciones realizadas en la prueba |
| ChecksFailed  | Número total de comprobaciones con errores en la prueba |
| TestResult    | Resultado de la prueba |
| TestResultCriterion   | Criterio del resultado de la prueba |
| ChecksFailedPercentThreshold  | Umbral del porcentaje de comprobaciones con error establecido para la prueba |
| RoundTripTimeMsThreshold  | Umbral de ida y vuelta (ms) establecido para la prueba |
| MinRoundTripTimeMs    | Tiempo mínimo de ida y vuelta (ms) para la prueba |
| MaxRoundTripTimeMs    | Tiempo máximo de ida y vuelta para la prueba |
| AvgRoundTripTimeMs    | Tiempo medio de ida y vuelta para la prueba |
| JitterMs  | Desviación media del tiempo de ida y vuelta para la prueba |
| AdditionalData    | Datos adicionales de la prueba |


## <a name="connection-monitor-path-schema"></a>Esquema de ruta de acceso de Connection Monitor

A continuación, se enumeran los campos del esquema de datos de ruta de acceso de Connection Monitor y lo que significan. 

| Campo  |    Descripción   |
|---|---|
| TimeGenerated  | Marca de tiempo (UTC) de cuando se generó el registro |
| RecordId  | Identificador de registro para la identificación única del registro de resultados de la prueba |
| TopologyId    | Identificador de topología del registro de la ruta de acceso |
| ConnectionMonitorResourceId   | Identificador de recurso de Connection Monitor de la prueba |
| TestGroupName | Nombre del grupo de pruebas al que pertenece la prueba |
| TestConfigurationName | Nombre de la configuración de prueba a la que pertenece la prueba |
| SourceType    | Tipo de la máquina de origen configurada para la prueba |
| SourceResourceId  | Identificador de recurso de la máquina de origen |
| SourceAddress | Dirección del origen configurado para la prueba |
| SourceSubnet  | Subred del origen |
| SourceIP  | Dirección IP del origen | 
| SourceName    | Nombre del punto de conexión del origen |
| SourceAgentId | Identificador del agente de origen |
| DestinationPort   | Puerto de destino configurado para la prueba |
| DestinationType   | Tipo de la máquina de destino configurada para la prueba |
| DestinationResourceId | Identificador de recurso de la máquina de destino |
| DestinationAddress    | Dirección del destino configurado para la prueba |
| DestinationSubnet | Si procede, la subred del destino |
| DestinationIP | Dirección IP del destino |
| DestinationName   | Nombre del punto de conexión de destino |
| DestinationAgentId    | Identificador del agente de destino |
| Protocolo  | Protocolo de la prueba |
| ChecksTotal   | Número total de comprobaciones realizadas en la prueba |
| ChecksFailed  | Número total de comprobaciones con errores en la prueba |
| PathTestResult    | Resultado de la prueba |
| PathResultCriterion   | Criterio del resultado de la prueba | 
| ChecksFailedPercentThreshold  | Umbral del porcentaje de comprobaciones con error establecido para la prueba |
| RoundTripTimeMsThreshold  | Umbral de ida y vuelta (ms) establecido para la prueba |
| MinRoundTripTimeMs    | Tiempo mínimo de ida y vuelta (ms) para la prueba |
| MaxRoundTripTimeMs    | Tiempo máximo de ida y vuelta para la prueba |
| AvgRoundTripTimeMs    | Tiempo medio de ida y vuelta para la prueba |
| JitterMs  | Desviación media del tiempo de ida y vuelta para la prueba |
| HopAddresses | Direcciones de salto identificadas para la prueba |
| HopTypes  | Tipos de salto identificados para la prueba |
| HopLinkTypes  | Tipos de vínculo de salto identificados para la prueba |
| HopResourceIds    | Identificadores de los recursos de salto identificados para la prueba |
| Issues    | Problemas identificados para la prueba |
| Hops  | Saltos identificados para la prueba |
| AdditionalData | Datos adicionales de la prueba |
