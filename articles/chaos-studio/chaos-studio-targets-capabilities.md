---
title: Destinos y funcionalidades de Azure Chaos Studio
description: Comprenda cómo controlar la incorporación de recursos en Azure Chaos Studio mediante destinos y funcionalidades.
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: conceptual
ms.date: 11/01/2021
ms.custom: template-concept, ignite-fall-2021
ms.openlocfilehash: cccfcfa60ed0d527fe3bd0dac9db0a6206a95c2f
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131092869"
---
# <a name="targets-and-capabilities-in-azure-chaos-studio"></a>Destinos y funcionalidades de Azure Chaos Studio

Para poder insertar un error en un recurso de Azure, primero el recurso debe tener habilitados los destinos y funcionalidades correspondientes. Los destinos y las funcionalidades controlan qué recursos están habilitados para la inserción de errores y qué errores se pueden ejecutar en esos recursos. El uso de destinos y funcionalidades [junto con otras medidas de seguridad](chaos-studio-permissions-security.md) puede evitar la inserción de errores accidental o malintencionada con Chaos Studio. Por ejemplo, con los destinos y las funcionalidades, puede permitir que el error de presión de CPU se ejecute en las máquinas virtuales de producción y, al mismo tiempo, evitar que se ejecute en ellas el error del proceso de eliminación.

## <a name="targets"></a>Destinos

Un destino de **Chaos** permite a Chaos Studio interactuar con un recurso para un tipo de destino determinado. Un **tipo de destino** representa el método de inserción de errores en un recurso. Los tipos de recursos que solo admiten errores directos de servicio tienen un tipo de destino, por ejemplo, el tipo `Microsoft-CosmosDB` para Azure Cosmos DB. Los tipos de recursos que admiten errores basados en agente y directos del servicio tienen dos tipos de destino: uno para los errores directos del servicio (por ejemplo, `Microsoft-VirtualMachine`) y otro para los errores basados en agente (siempre `Microsoft-Agent`).

Un destino es un recurso de extensión creado como elemento secundario del recurso que se va a incorporar a Chaos Studio (por ejemplo, una máquina virtual o un grupo de seguridad de red). Un destino define el tipo de destino que está habilitado en el recurso. Por ejemplo, si se incorpora una instancia de Cosmos DB con este identificador de recurso:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB
```

El recurso de Cosmos DB tendrá un recurso secundario con el formato siguiente:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB
```

Solo se pueden usar recursos con un destino creado a partir de ellos para la inserción de errores con Chaos Studio.

## <a name="capabilities"></a>Funcionalidades

Una **funcionalidad** permite a Chaos Studio ejecutar un error determinado en un recurso, como apagar una máquina virtual. Las funcionalidades son únicas por tipo de destino y representan el error que habilitan, por ejemplo, `CPUPressure-1.0`. [Visite la biblioteca de errores de Chaos Studio](chaos-studio-fault-library.md) para comprender todos los errores disponibles y sus correspondientes nombres de funcionalidad y tipos de destino.

Una funcionalidad es un recurso de extensión creado como elemento secundario de un destino. Por ejemplo, si habilita el error de apagado en una máquina virtual con un identificador de destino directo del servicio:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine
```

El recurso de destino tendrá un recurso secundario con el formato siguiente:

```
/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0
```

Un experimento solo puede insertar errores en destinos incorporados con las funcionalidades correspondientes habilitadas. 

## <a name="listing-capability-names-and-parameters"></a>Enumeración de los nombres y parámetros de funcionalidad
Como referencia, hay disponible una lista de nombres de funcionalidad, URN de error y parámetros [en nuestra biblioteca de errores](chaos-studio-fault-library.md), pero puede usar la respuesta HTTP para crear una funcionalidad o realizar una solicitud GET en una funcionalidad existente a fin de obtener esta información a petición. Por ejemplo, al realizar una operación GET en una funcionalidad de apagado de máquina virtual:

```bash
az rest --method get --url "https://management.azure.com/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-08-11-preview"
```

Se devolverá el código JSON siguiente:

```JSON
{
  "id": "/subscriptions/fd9ccc83-faf6-4121-9aff-2a2d685ca2a2/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0",
  "name": "shutdown-1.0",
  "properties": {
    "description": null,
    "name": "shutdown-1.0",
    "parametersSchema": "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json",
    "publisher": "Microsoft",
    "targetType": "VirtualMachine",
    "type": "shutdown",
    "urn": "urn:csci:microsoft:virtualMachine:shutdown/1.0",
    "version": "1.0"
  },
  "resourceGroup": "myRG",
  "systemData": {
    "createdAt": "2021-09-15T23:00:00.826575+00:00",
    "lastModifiedAt": "2021-09-15T23:00:00.826575+00:00"
  },
  "type": "Microsoft.Chaos/targets/capabilities"
}
```

La propiedad `properties.urn` se usa para definir el error que se quiere ejecutar en un experimento de caos. Para comprender el esquema de los parámetros de este error, puede obtener el esquema al que hace referencia `properties.parametersSchema`.

```bash
az rest --method get --url "https://schema-tc.eastus.chaos-prod.azure.com/targetTypes/Microsoft-VirtualMachine/capabilityTypes/Shutdown-1.0/parametersSchema.json"
```

devuelve el código JSON siguiente:
```JSON
{
  "$schema": "https://json-schema.org/draft-07/schema",
  "properties": {
    "abruptShutdown": {
      "type": "boolean"
    },
    "restartWhenComplete": {
      "type": "boolean"
    }
  },
  "type": "object"
}
```

## <a name="next-steps"></a>Pasos siguientes
Ahora que comprende qué son los destinos y las funcionalidades, está listo para:
- [Información sobre errores y acciones](chaos-studio-faults-actions.md)
- [Crear y ejecutar tu primer experimento](chaos-studio-tutorial-service-direct.md)
