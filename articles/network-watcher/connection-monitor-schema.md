---
title: Esquemas de Connection Monitor de Azure Network Watcher | Microsoft Docs
description: Comprenda el esquema de datos de pruebas y el esquema de datos de ruta de Connection Monitor de Azure Network Watcher.
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
ms.openlocfilehash: 4cad1ea0d90f85a12e7d7f9b7dbc869a61a91a39
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "122969771"
---
# <a name="azure-network-watcher-connection-monitor-schemas"></a>Esquemas de Connection Monitor de Azure Network Watcher

Connection Monitor proporciona una supervisión de conexión unificada de un extremo a otro en Azure Network Watcher. La característica Connection Monitor admite implementaciones híbridas y en la nube de Azure. Network Watcher proporciona herramientas para supervisar, diagnosticar y ver las métricas relacionadas con la conectividad de las implementaciones de Azure.

Estos son algunos casos de uso de Connection Monitor:

- La máquina virtual del servidor front-end web se comunica con una máquina virtual de servidor de bases de datos en una aplicación de varios niveles. Quiere comprobar la conectividad de red entre las dos VM.
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

Hay dos tipos de registros o datos ingeridos en Log Analytics. Los datos de prueba (consulta NWConnectionMonitorTestResult) se actualizan en función de la frecuencia de supervisión de un grupo de prueba determinado. Los datos de ruta de acceso (consulta NWConnectionMonitorPathResult) se actualizan cuando hay un cambio significativo en el porcentaje de pérdida o el tiempo de ida y vuelta. Para duraciones prolongadas, los datos de prueba pueden seguir actualizándose aunque los datos de ruta de acceso no se actualicen con frecuencia, ya que son independientes.

## <a name="connection-monitor-tests-schema"></a>Esquema de pruebas de Connection Monitor

La tabla siguiente enumera los campos del esquema de datos de pruebas de Connection Monitor y explica su significado. 

| Campo  |    Descripción   |
|---|---|
| TimeGenerated | Marca de tiempo (UTC) de cuando se generó el registro |
| RecordId  | El identificador de registro para la identificación única del registro de resultados de la prueba |
| ConnectionMonitorResourceId   | El identificador de recurso de Connection Monitor de la prueba |
| TestGroupName | El nombre del grupo de pruebas al que pertenece la prueba |
| TestConfigurationName | El nombre de la configuración de prueba a la que pertenece la prueba |
| SourceType    | Tipo de la máquina de origen configurada para la prueba |
| SourceResourceId  | Identificador de recurso de la máquina de origen |
| SourceAddress | Dirección del origen configurado para la prueba |
| SourceSubnet  | Subred del origen |
| SourceIP  | Dirección IP del origen |
| SourceName    | El nombre del punto de conexión de origen |
| SourceAgentId | Identificador del agente de origen |
| DestinationPort   | Puerto de destino configurado para la prueba |
| DestinationType   | Tipo de la máquina de destino configurada para la prueba |
| DestinationResourceId | El identificador de recurso de la máquina de destino |
| DestinationAddress    | Dirección del destino configurado para la prueba |
| DestinationSubnet | Si procede, la subred del destino |
| DestinationIP | Dirección IP del destino |
| DestinationName   | El nombre del punto de conexión de destino |
| DestinationAgentId    | Identificador del agente de destino |
| Protocolo  | Protocolo de la prueba |
| ChecksTotal   | Número total de comprobaciones realizadas en la prueba |
| ChecksFailed  | Número total de comprobaciones con errores en la prueba |
| TestResult    | Resultado de la prueba |
| TestResultCriterion   | Criterio del resultado de la prueba |
| ChecksFailedPercentThreshold  | Umbral del porcentaje de comprobaciones con error establecido para la prueba |
| RoundTripTimeMsThreshold  | El umbral de ida y vuelta (en milisegundos) establecido para la prueba |
| MinRoundTripTimeMs    | El tiempo mínimo de ida y vuelta (en milisegundos) para la prueba |
| MaxRoundTripTimeMs    | El tiempo máximo de ida y vuelta para la prueba |
| AvgRoundTripTimeMs    | El tiempo medio de ida y vuelta para la prueba |
| JitterMs  | La desviación media del tiempo de ida y vuelta para la prueba |
| AdditionalData    | Datos adicionales de la prueba |


## <a name="connection-monitor-path-schema"></a>Esquema de ruta de acceso de Connection Monitor

La tabla siguiente enumera los campos del esquema de datos de ruta de Connection Monitor y explica su significado. 

| Campo  |    Descripción   |
|---|---|
| TimeGenerated  | Marca de tiempo (UTC) de cuando se generó el registro |
| RecordId  | El identificador de registro para la identificación única del registro de resultados de la prueba |
| TopologyId    | Identificador de topología del registro de la ruta de acceso |
| ConnectionMonitorResourceId   | El identificador de recurso de Connection Monitor de la prueba |
| TestGroupName | El nombre del grupo de pruebas al que pertenece la prueba |
| TestConfigurationName | El nombre de la configuración de prueba a la que pertenece la prueba |
| SourceType    | Tipo de la máquina de origen configurada para la prueba |
| SourceResourceId  | Identificador de recurso de la máquina de origen |
| SourceAddress | Dirección del origen configurado para la prueba |
| SourceSubnet  | Subred del origen |
| SourceIP  | Dirección IP del origen | 
| SourceName    | El nombre del punto de conexión de origen |
| SourceAgentId | Identificador del agente de origen |
| DestinationPort   | Puerto de destino configurado para la prueba |
| DestinationType   | Tipo de la máquina de destino configurada para la prueba |
| DestinationResourceId | El identificador de recurso de la máquina de destino |
| DestinationAddress    | Dirección del destino configurado para la prueba |
| DestinationSubnet | Si procede, la subred del destino |
| DestinationIP | Dirección IP del destino |
| DestinationName   | El nombre del punto de conexión de destino |
| DestinationAgentId    | Identificador del agente de destino |
| Protocolo  | Protocolo de la prueba |
| ChecksTotal   | Número total de comprobaciones realizadas en la prueba |
| ChecksFailed  | Número total de comprobaciones con errores en la prueba |
| PathTestResult    | Resultado de la prueba |
| PathResultCriterion   | Criterio del resultado de la prueba | 
| ChecksFailedPercentThreshold  | Umbral del porcentaje de comprobaciones con error establecido para la prueba |
| RoundTripTimeMsThreshold  | El umbral de ida y vuelta (en milisegundos) establecido para la prueba |
| MinRoundTripTimeMs    | El tiempo mínimo de ida y vuelta (en milisegundos) para la prueba |
| MaxRoundTripTimeMs    | El tiempo máximo de ida y vuelta para la prueba |
| AvgRoundTripTimeMs    | El tiempo medio de ida y vuelta para la prueba |
| JitterMs  | La desviación media del tiempo de ida y vuelta para la prueba |
| HopAddresses | Direcciones de salto identificadas para la prueba |
| HopTypes  | Tipos de salto identificados para la prueba |
| HopLinkTypes  | Tipos de vínculo de salto identificados para la prueba |
| HopResourceIds    | Identificadores de los recursos de salto identificados para la prueba |
| Issues    | Problemas identificados para la prueba |
| Hops  | Saltos identificados para la prueba |
| AdditionalData | Datos adicionales de la prueba |
