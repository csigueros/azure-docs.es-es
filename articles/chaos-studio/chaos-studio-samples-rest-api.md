---
title: Uso de las API REST para administrar experimentos de Azure Chaos Studio
description: Ejecute y administre un experimento de caos con Azure Chaos Studio mediante el uso de API REST.
services: chaos-studio
author: johnkemnetz
ms.topic: article
ms.date: 11/01/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: ignite-fall-2021
ms.openlocfilehash: f06c89e61f9ed1889502417ca5708493dd9d2e33
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718476"
---
# <a name="use-the-chaos-studio-rest-apis-to-run-and-manage-chaos-experiments"></a>Uso de las API REST de Chaos Studio para ejecutar y administrar experimentos de caos

> [!WARNING]
> La inserción de errores puede afectar a la aplicación o al servicio. Debe tener cuidado para no interrumpir a los clientes.  

La API Chaos Studio proporciona compatibilidad para iniciar experimentos mediante programación. También puede usar armclient y la CLI de Azure para ejecutar estos comandos desde la consola. Los ejemplos siguientes están diseñados para la CLI de Azure.

> [!Warning]
> Estas API todavía están en desarrollo y pueden cambiar.

## <a name="rest-apis"></a>API de REST

Se pueden usar API REST de Squall para iniciar y detener experimentos, consultar el estado de destino, consultar el estado del experimento y consultar y eliminar configuraciones de suscripción. Es posible usar la utilidad `AZ CLI` para realizar estas acciones desde la línea de comandos.

> [!TIP]
> Para obtener una salida más detallada con la CLI de AZ, anexe **--verbose** al final de cada comando. Esto devolverá más metadatos cuando se ejecuten los comandos, incluido **x-ms-correlation-request-id**, que ayuda a depurar.

### <a name="chaos-provider-commands"></a>Comandos del proveedor de Chaos

#### <a name="enumerate-details-about-the-microsoftchaos-resource-provider"></a>Enumeración de los detalles del proveedor de recursos de Microsoft.Chaos

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Chaos?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-all-the-operations-of-the-chaos-studio-resource-provider"></a>Enumeración de todas las operaciones del proveedor de recursos de Chaos Studio

```bash
az rest --method get --url "https://management.azure.com/providers/Microsoft.Chaos/operations?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-chaos-provider-configurations"></a>Enumeración de las configuraciones del proveedor de Chaos

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/microsoft.chaos/chaosProviderConfigurations/?api-version={apiVersion}" --resource "https://management.azure.com" --verbose 
```

#### <a name="create-chaos-provider-configuration"></a>Creación de la configuración del proveedor de Chaos

```bash
az rest --method put --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/microsoft.chaos/chaosProviderConfigurations/{chaosProviderType}?api-version={apiVersion}" --body @{providerSettings.json} --resource "https://management.azure.com"
```

### <a name="chaos-target-and-agent-commands"></a>Comandos de destino y agente de Chaos

#### <a name="list-all-the-targets-or-agents-under-a-subscription"></a>Enumeración de todos los destinos o agentes de una suscripción

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.Chaos/chaosTargets/?api-version={apiVersion}" --url-parameter "chaosProviderType={chaosProviderType}" --resource "https://management.azure.com"
```

### <a name="chaos-experiment-commands"></a>Comandos del experimento de caos

#### <a name="list-all-experiments-in-a-resource-group"></a>Enumeración de todos los experimentos de un grupo de recursos

```bash
az rest --method get --url "https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Chaos/chaosExperiments?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-an-experiment-configuration-details-by-name"></a>Obtención de los detalles de configuración de un experimento por nombre

```bash
az rest --method get --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="create-or-update-an-experiment"></a>Creación o actualización de un experimento

```bash
az rest --method put --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --body @{experimentName.json} --resource "https://management.azure.com"
```

#### <a name="delete-an-experiment"></a>Eliminar un experimento

```bash
az rest --method delete --url "https://management.azure.com/{experimentId}?api-version={apiVersion}" --resource "https://management.azure.com" --verbose
```

#### <a name="start-an-experiment"></a>Inicio de un experimento

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/start?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-statuses-history-of-an-experiment"></a>Obtención de los estados (historial) de un experimento

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/statuses?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="get-status-of-an-experiment"></a>Obtención del estado de un experimento

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/status?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="cancel-stop-an-experiment"></a>Cancelación (detención) de un experimento

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/cancel?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-the-details-of-the-last-two-experiment-executions"></a>Enumeración de los detalles de las dos últimas ejecuciones de experimentos

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/executiondetails?api-version={apiVersion}" --resource "https://management.azure.com"
```

#### <a name="list-the-details-of-a-specific-experiment-execution"></a>Enumeración de los detalles de la ejecución de un experimento específico

```bash
az rest --method get --url "https://management.azure.com/{experimentId}/executiondetails/{executionDetailsId}?api-version={apiVersion}" --resource "https://management.azure.com"
```

## <a name="parameter-definitions"></a>Definiciones de parámetros

| Nombre de parámetro | Definición | Búsqueda |
| --- | --- | --- |
| {apiVersion} | Versión de la API que se usará al ejecutar el comando proporcionado | Se encuentra en la [documentación de API](/rest/api/chaosstudio/). |
| {experimentId} | Identificador de recurso de Azure para el experimento | Se encuentra en la [hoja del portal de experimentos de Chaos Studio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.chaos%2Fchaosexperiments). |
| {chaosProviderType} | Tipo o nombre del proveedor de Chaos Studio | Los proveedores disponibles se encuentran en la [lista de tipos de configuración del proveedor actual](chaos-studio-fault-providers.md). |
| {experimentName.json} | JSON con la configuración del experimento de caos | Generado por el usuario |
| {subscriptionId} | Identificador de suscripción donde se encuentra el recurso de destino | Se encuentra en la [hoja del portal de suscripciones](https://ms.portal.azure.com/#blade/Microsoft_Azure_Billing/SubscriptionsBlade). |
| {resourceGroupName} | Nombre del grupo de recursos donde se encuentra el recurso de destino | Se encuentra en la [hoja del portal de grupos de recursos](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceGroups). |
| {executionDetailsId} | Identificador de ejecución de la ejecución de un experimento | Se encuentra en la [hoja del portal de experimentos de Chaos Studio](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.chaos%2Fchaosexperiments). |