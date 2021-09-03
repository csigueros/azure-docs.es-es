---
title: Reglas de recopilación de datos en Azure Monitor (versión preliminar)
description: Información general sobre las reglas de recopilación de datos (DCR) en Azure Monitor incluido su contenido y estructura, y cómo puede crearlas y trabajar con ellas.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/19/2021
ms.custom: references_region
ms.openlocfilehash: 83ad2245ec010bd91907ae27e077f86b4d6b1d5e
ms.sourcegitcommit: 0af634af87404d6970d82fcf1e75598c8da7a044
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/15/2021
ms.locfileid: "112122394"
---
# <a name="data-collection-rules-in-azure-monitor"></a>Reglas de recopilación de datos en Azure Monitor
Las reglas de recopilación de datos (DCR) definen los datos que entran en Azure Monitor y especifican dónde se deben enviar los datos o almacenarlos. En este artículo se proporciona información general sobre las reglas de recopilación de datos, incluido su contenido y su estructura, y cómo puede crearlas y trabajar con ellas.

## <a name="input-sources"></a>Orígenes de entrada
Las reglas de recopilación de datos admiten actualmente los siguientes orígenes de entrada:

- Agente de Azure Monitor en ejecución en máquinas virtuales, conjuntos de escalado de máquinas virtuales y Azure Arc para servidores. Consulte [Configuración de la recopilación de datos para el agente de Azure Monitor (versión preliminar)](../agents/data-collection-rule-azure-monitor-agent.md).



## <a name="components-of-a-data-collection-rule"></a>Componentes de una regla de recopilación de datos
Una regla de recopilación de datos incluye los siguientes componentes.

| Componente | Descripción |
|:---|:---|
| Orígenes de datos | Origen único de los datos de supervisión con su propio formato y método para exponer los datos. Entre los ejemplos de origen de datos se incluyen el registro de eventos de Windows, los contadores de rendimiento y syslog. Cada origen de datos coincide con un tipo de origen de datos determinado tal y como se describe a continuación. |
| Secuencias | Identificador único que describe un conjunto de orígenes de datos que se transformarán y se esquematizarán como un tipo. Cada origen de datos requiere una o varias secuencias; varios orígenes de datos pueden utilizar una secuencia. Todos los orígenes de datos de una secuencia comparten un esquema común. Por ejemplo, puede usar varias secuencias si desea enviar un origen de datos determinado a varias tablas en el mismo área de trabajo de Log Analytics. |
| Destinations | Conjunto de destinos donde deben enviarse los datos. Algunos ejemplos son el área de trabajo de Log Analytics y las métricas de Azure Monitor. | 
| Flujos de datos | Definición de las secuencias que se deben enviar a los destinos. | 

Las reglas de recopilación de datos se almacenan de forma regional y están disponibles en todas las regiones públicas donde se admite Log Analytics. Las regiones y nubes gubernamentales no se admiten en este momento.

En el diagrama siguiente se muestran los componentes de una regla de recopilación de datos y su relación

[![Diagrama de DCR](media/data-collection-rule-overview/data-collection-rule-components.png)](media/data-collection-rule-overview/data-collection-rule-components.png#lightbox)

### <a name="data-source-types"></a>Tipos de origen de datos
Cada origen de datos tiene un tipo de origen de datos. Cada tipo define un conjunto único de propiedades que se deben especificar para cada origen de datos. Los tipos de orígenes de datos disponibles actualmente se muestran en la tabla siguiente.

| Tipo de origen de datos | Descripción | 
|:---|:---|
| extensión | Origen de datos basado en la extensión de máquina virtual |
| performanceCounters | Contadores de rendimiento para Windows y Linux |
| syslog | Eventos de Syslog en Linux |
| windowsEventLogs | Registro de eventos de Windows |


## <a name="limits"></a>Límites
Para obtener información sobre los límites que se aplican a cada regla de recopilación de datos, consulte los [límites de servicio de Azure Monitor](../service-limits.md#data-collection-rules).

## <a name="data-residency"></a>Residencia de datos 
Las reglas de recopilación de datos como servicio se implementan de forma regional. Una regla se crea y se almacena en la región que se especifica, y se hace una copia de seguridad en la [región emparejada](../../best-practices-availability-paired-regions.md#azure-regional-pairs) dentro de la misma geoárea.  

**Residencia de datos en una sola región**: la característica en versión preliminar que permite almacenar los datos de clientes en una única región solo está disponible actualmente en la región de Sudeste Asiático (Singapur) de la geoárea Asia Pacífico y en la región Sur de Brasil (Estado de São Paulo) de la geoárea Brasil. La residencia en una sola región está habilitada de forma predeterminada en estas regiones.

## <a name="create-a-dcr"></a>Creación de una regla de recopilación de datos
Actualmente, puede usar cualquiera de los métodos siguientes para crear una regla de recopilación de datos:

- [Use Azure Portal](../agents/data-collection-rule-azure-monitor-agent.md) para crear una regla de recopilación de datos y asociarla a una o varias máquinas virtuales.
- Editar directamente la regla de recopilación de datos en el archivo JSON y [enviarla mediante la API de REST](/rest/api/monitor/datacollectionrules).
- Cree DCR y asociaciones con la [CLI de Azure](https://github.com/Azure/azure-cli-extensions/blob/master/src/monitor-control-service/README.md).
- Cree DCR y asociaciones con Azure PowerShell.
  - [Get-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRule.md)
  - [New-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRule.md)
  - [Set-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Set-AzDataCollectionRule.md)
  - [Update-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Update-AzDataCollectionRule.md)
  - [Remove-AzDataCollectionRule](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRule.md)
  - [Get-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Get-AzDataCollectionRuleAssociation.md)
  - [New-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/New-AzDataCollectionRuleAssociation.md)
  - [Remove-AzDataCollectionRuleAssociation](https://github.com/Azure/azure-powershell/blob/master/src/Monitor/Monitor/help/Remove-AzDataCollectionRuleAssociation.md)

## <a name="sample-data-collection-rule"></a>Eliminación de una regla de recopilación de datos
La siguiente regla de recopilación de datos de ejemplo es para máquinas virtuales con el agente de Azure Monitor y tiene los detalles siguientes:

- Datos de rendimiento
  - Recopila los contadores de procesador, memoria, disco lógico y disco físico específicos cada 15 segundos, y se carga cada minuto.
  - Recopila los contadores de proceso específicos cada 30 segundos y los carga cada 5 minutos.
- Eventos de Windows
  - Recopila eventos de seguridad de Windows y los carga cada minuto.
  - Recopila eventos del sistema y de aplicación de Windows, y los carga cada 5 minutos.
- syslog
  - Recopila eventos de depuración, críticos y de emergencia desde la utilidad cron.
  - Recopila eventos de alerta, críticos y de emergencia desde la utilidad syslog.
- Destinations
  - Envía todos los datos a un área de trabajo de Log Analytics denominada centralWorkspace.

> [!NOTE]
> Para ver una explicación de las consultas de XPath que se usan para especificar la recopilación de eventos en las reglas de recopilación de datos, consulte la sección sobre los [límites en la recopilación de datos con consultas XPath personalizadas](data-collection-rule-azure-monitor-agent.md#limit-data-collection-with-custom-xpath-queries)


```json
{
    "location": "eastus",
    "properties": {
      "dataSources": {
        "performanceCounters": [
          {
            "name": "cloudTeamCoreCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT1M",
            "samplingFrequencyInSeconds": 15,
            "counterSpecifiers": [
              "\\Processor(_Total)\\% Processor Time",
              "\\Memory\\Committed Bytes",
              "\\LogicalDisk(_Total)\\Free Megabytes",
              "\\PhysicalDisk(_Total)\\Avg. Disk Queue Length"
            ]
          },
          {
            "name": "appTeamExtraCounters",
            "streams": [
              "Microsoft-Perf"
            ],
            "scheduledTransferPeriod": "PT5M",
            "samplingFrequencyInSeconds": 30,
            "counterSpecifiers": [
              "\\Process(_Total)\\Thread Count"
            ]
          }
        ],
        "windowsEventLogs": [
          {
            "name": "cloudSecurityTeamEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT1M",
            "xPathQueries": [
              "Security!*"
            ]
          },
          {
            "name": "appTeam1AppEvents",
            "streams": [
              "Microsoft-Event"
            ],
            "scheduledTransferPeriod": "PT5M",
            "xPathQueries": [
              "System!*[System[(Level = 1 or Level = 2 or Level = 3)]]",
              "Application!*[System[(Level = 1 or Level = 2 or Level = 3)]]"
            ]
          }
        ],
        "syslog": [
          {
            "name": "cronSyslog",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "cron"
            ],
            "logLevels": [
              "Debug",
              "Critical",
              "Emergency"
            ]
          },
          {
            "name": "syslogBase",
            "streams": [
              "Microsoft-Syslog"
            ],
            "facilityNames": [
              "syslog"
            ],
            "logLevels": [
              "Alert",
              "Critical",
              "Emergency"
            ]
          }
        ]
      },
      "destinations": {
        "logAnalytics": [
          {
            "workspaceResourceId": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/my-resource-group/providers/Microsoft.OperationalInsights/workspaces/my-workspace",
            "name": "centralWorkspace"
          }
        ]
      },
      "dataFlows": [
        {
          "streams": [
            "Microsoft-Perf",
            "Microsoft-Syslog",
            "Microsoft-Event"
          ],
          "destinations": [
            "centralWorkspace"
          ]
        }
      ]
    }
  }
```


## <a name="next-steps"></a>Pasos siguientes

- [Cree una regla de recopilación de datos](data-collection-rule-azure-monitor-agent.md) y una asociación a ella desde una máquina virtual mediante el agente de Azure Monitor.
