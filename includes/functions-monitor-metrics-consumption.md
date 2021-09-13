---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 07/04/2021
ms.author: glenga
ms.openlocfilehash: 34237e64aecfcd4059a9bc9fb3ae390281b3419a
ms.sourcegitcommit: e0ef8440877c65e7f92adf7729d25c459f1b7549
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/09/2021
ms.locfileid: "113567365"
---
#### <a name="portal"></a>[Portal](#tab/portal)

Use el [explorador de métricas de Azure Monitor](../articles/azure-monitor/essentials/metrics-getting-started.md) para ver los datos relacionados con los costos de las aplicaciones de funciones del plan de consumo en un formato gráfico. 

1. Vaya a la aplicación de funciones en [Azure Portal].

1. En el panel izquierdo, desplácese hacia abajo hasta **Supervisión** y elija **Métricas**.  

1. En **Métrica**, elija **Recuento de la ejecución de la función** y **Suma** en **Agregación**. Esto agrega al gráfico la suma de los recuentos de ejecución durante el período elegido.

    ![Definición de una métrica de aplicación de funciones para agregarla al gráfico](./media/functions-monitor-metrics-consumption/monitor-metrics-add-metric.png)

1. Seleccione **Agregar métrica** y repita los pasos 2 a 4 para agregar **Unidades de ejecución de la función** al gráfico. 

El gráfico resultante contiene los totales de ambas métricas de ejecución en el intervalo de tiempo elegido, que en este caso es de dos horas.

![Gráfico de recuentos de ejecución de funciones y unidades de ejecución](./media/functions-monitor-metrics-consumption/monitor-metrics-execution-sum.png)

Dado que el número de unidades de ejecución es mucho mayor que el recuento de ejecuciones, el gráfico simplemente muestra las unidades de ejecución.

En este gráfico se muestra un total de 1,11 mil millones de `Function Execution Units` consumidas en un período de dos horas, medido en MB por milisegundo. Para convertir a GB por segundo, divídalo por 1 024 000. En este ejemplo, la aplicación de funciones ha consumido `1110000000 / 1024000 = 1083.98` GB por segundo. Puede tomar este valor y multiplicar por el precio actual del tiempo de ejecución en la [página de precios de Functions](https://azure.microsoft.com/pricing/details/functions/), que le proporciona el costo de estas dos horas, suponiendo que ya ha usado cualquier concesión gratuita de tiempo de ejecución. 

#### <a name="azure-cli"></a>[CLI de Azure](#tab/azurecli)

La [CLI de Azure](/cli/azure/) tiene comandos para recuperar métricas. Puede usar la CLI desde un entorno de comandos local o directamente desde el portal mediante [Azure Cloud Shell](../articles/cloud-shell/overview.md). Por ejemplo, el comando siguiente [az monitor metrics list](/cli/azure/monitor/metrics#az_monitor_metrics_list) devuelve datos por hora durante el mismo período de tiempo utilizado antes.

Asegúrese de reemplazar `<AZURE_SUBSCRIPTON_ID>` por el identificador de suscripción de Azure que ejecuta el comando.

```azurecli-interactive
az monitor metrics list --resource /subscriptions/<AZURE_SUBSCRIPTION_ID>/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption --metric FunctionExecutionUnits,FunctionExecutionCount --aggregation Total --interval PT1H --start-time 2019-09-11T21:46:00Z --end-time 2019-09-11T23:18:00Z
```

Este comando devuelve una carga JSON similar a la del ejemplo siguiente:

```json
{
  "cost": 0.0,
  "interval": "1:00:00",
  "namespace": "Microsoft.Web/sites",
  "resourceregion": "centralus",
  "timespan": "2019-09-11T21:46:00Z/2019-09-11T23:18:00Z",
  "value": [
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionUnits",
      "name": {
        "localizedValue": "Function Execution Units",
        "value": "FunctionExecutionUnits"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 793294592.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 316576256.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    },
    {
      "id": "/subscriptions/XXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXX/resourceGroups/metrics-testing-consumption/providers/Microsoft.Web/sites/metrics-testing-consumption/providers/Microsoft.Insights/metrics/FunctionExecutionCount",
      "name": {
        "localizedValue": "Function Execution Count",
        "value": "FunctionExecutionCount"
      },
      "resourceGroup": "metrics-testing-consumption",
      "timeseries": [
        {
          "data": [
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T21:46:00+00:00",
              "total": 33538.0
            },
            {
              "average": null,
              "count": null,
              "maximum": null,
              "minimum": null,
              "timeStamp": "2019-09-11T22:46:00+00:00",
              "total": 13040.0
            }
          ],
          "metadatavalues": []
        }
      ],
      "type": "Microsoft.Insights/metrics",
      "unit": "Count"
    }
  ]
}
```
Esta respuesta determinada muestra que, de `2019-09-11T21:46` a `2019-09-11T23:18`, la aplicación ha consumido 1,11 mil millones de MB por milisegundo (1083,98 GB por segundo).

---

[Azure Portal]: https://portal.azure.com