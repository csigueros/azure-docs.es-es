---
author: ggailey777
ms.author: glenga
ms.date: 7/24/2019
ms.topic: include
ms.service: azure-functions
ms.openlocfilehash: 4c16fc8a6f497a05294a9b7357b8ffc4dab285f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741376"
---
#### <a name="built-in-log-streaming"></a>Streaming integrado de registros

Use el comando [`func azure functionapp logstream`](../articles/azure-functions/functions-core-tools-reference.md#func-azure-functionapp-list-functions) para empezar a recibir registros de streaming de una aplicación de funciones específica que se ejecuta en Azure, como en el ejemplo siguiente:

```bash
func azure functionapp logstream <FunctionAppName>
```

>[!NOTE]
>El streaming de registro integrado aún no se ha habilitado en Core Tools para las aplicaciones de funciones que se ejecutan en Linux en un plan de consumo. En estos planes de hospedaje, es preciso usar Live Metrics Stream para ver los registros casi en tiempo real.

#### <a name="live-metrics-stream"></a>Secuencia de métricas en directo

La información de [Live Metrics Stream](../articles/azure-monitor/app/live-stream.md) de una aplicación de funciones se puede ver en una ventana nueva del explorador. Para ello, hay que incluir la opción `--browser`, como se hace en el ejemplo siguiente:

```bash
func azure functionapp logstream <FunctionAppName> --browser
```
