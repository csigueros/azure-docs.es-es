---
title: Cree un experimento que use un error directo del servicio mediante Azure Chaos Studio con la CLI de Azure
description: Cree un experimento que use un error directo del servicio con la CLI de Azure
author: johnkemnetz
ms.author: johnkem
ms.service: chaos-studio
ms.topic: how-to
ms.date: 11/10/2021
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e6f1f215a96b6f651e344087c98a3a7d9be278db
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132718533"
---
# <a name="create-a-chaos-experiment-that-uses-a-service-direct-fault-with-the-azure-cli"></a>Cree un experimento de caos que use un error directo del servicio con la CLI de Azure

Puede usar un experimento de caos para comprobar que la aplicación es resistente a los errores provocando esos errores en un entorno controlado. En esta guía, provocará una conmutación por error de Azure Comos DB de varias lecturas y una sola escritura mediante un experimento de caos y Azure Chaos Studio. La ejecución de este experimento puede ayudarle a defenderse contra la pérdida de datos cuando se produce un evento de conmutación por error.

Estos mismos pasos se pueden usar para configurar y ejecutar un experimento para cualquier error directo del servicio. Un error **directo del servicio** se ejecuta directamente en un recurso de Azure sin necesidad de instrumentación, a diferencia de los errores basados en agente, que requieren la instalación del agente de caos.

## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Una cuenta de Azure Cosmos DB. Si no tiene una cuenta de Azure Cosmos DB, puede [seguir estos pasos para crear una](../cosmos-db/sql/create-cosmosdb-resources-portal.md).
- Al menos una configuración de región de lectura y escritura para la cuenta de Azure Cosmos DB.

## <a name="launch-azure-cloud-shell"></a>Inicio de Azure Cloud Shell

Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar los pasos de este artículo. Tiene las herramientas comunes de Azure preinstaladas y configuradas para usarlas en la cuenta. 

Para abrir Cloud Shell, seleccione **Pruébelo** en la esquina superior derecha de un bloque de código. También puede abrir Cloud Shell en una pestaña independiente acudiendo a [https://shell.azure.com/bash](https://shell.azure.com/bash). Seleccione **Copiar** para copiar los bloques de código, péguelos en Cloud Shell y, después, seleccione **Entrar** para ejecutarlos.

Si prefiere instalar y usar la CLI en un entorno local, para este tutorial se requiere la versión 2.0.30 de la CLI de Azure o una versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).

> [!NOTE]
> Estas instrucciones usan un terminal de Bash en Azure Cloud Shell. Es posible que algunos comandos no funcionen tal como se describe si se ejecuta la CLI localmente o en un terminal de PowerShell.

## <a name="enable-chaos-studio-on-your-azure-cosmos-db-account"></a>Habilitación de Chaos Studio en la cuenta de Azure Cosmos DB

Chaos Studio no puede insertar errores en un recurso a menos que ese recurso se haya incorporado primero a Chaos Studio. Incorpore un recurso a Chaos Studio mediante la creación de un [destino y funcionalidades](chaos-studio-targets-capabilities.md) en el recurso. Las cuentas de Azure Cosmos DB solo tienen un tipo de destino (servicio directo) y una funcionalidad (conmutación por error), pero otros recursos pueden tener hasta dos tipos de destino (uno para los errores de servicio directo y otro para los errores basados en el agente) y muchas funcionalidades.

1. Cree un destino reemplazando `$RESOURCE_ID` por el identificador de recurso del recurso que va a incorporar y `$TARGET_TYPE`por el [tipo de destino que va a incorporar](chaos-studio-fault-providers.md):

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/$TARGET_TYPE?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    Por ejemplo, si se incorpora una máquina virtual como destino directo del servicio:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

2. Cree las funcionalidades en el destino reemplazando `$RESOURCE_ID` por el identificador de recurso del recurso que va a incorporar, `$TARGET_TYPE` por el [tipo de destino que va a incorporar](chaos-studio-fault-providers.md) y `$CAPABILITY` por el [nombre de la funcionalidad de error que está habilitando](chaos-studio-fault-library.md).
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/$TARGET_TYPE/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    Por ejemplo, si habilita la funcionalidad de apagado de la máquina virtual:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/microsoft-virtualMachine/capabilities/shutdown-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

Ya ha incorporado correctamente la cuenta de Azure Cosmos DB a Chaos Studio.

## <a name="create-an-experiment"></a>Creación de un experimento
Con la cuenta de Azure Cosmos DB ahora incorporada, puede crear el experimento. Un experimento de caos define las acciones que desea realizar en los recursos de destino, organizados en pasos, que se ejecutan secuencialmente, y ramas, que se ejecutan en paralelo.

1. Formule el JSON del experimento a partir del ejemplo JSON indicado a continuación. Modifique el archivo JSON para que se corresponda con el experimento que desea ejecutar mediante [la API Crear experimento](/rest/api/chaosstudio/experiments/create-or-update) y la [biblioteca de errores](chaos-studio-fault-library.md)

    ```json
    {
      "location": "eastus",
      "identity": {
        "type": "SystemAssigned"
      },
      "properties": {
        "steps": [
          {
            "name": "Step1",
            "branches": [
              {
                "name": "Branch1",
                "actions": [
                  {
                    "type": "continuous",
                    "selectorId": "Selector1",
                    "duration": "PT10M",
                    "parameters": [
                      {
                        "key": "readRegion",
                        "value": "East US 2"
                      }
                    ],
                    "name": "urn:csci:microsoft:cosmosDB:failover/1.0"
                  }
                ]
              }
            ]
          }
        ],
        "selectors": [
          {
            "id": "Selector1",
            "type": "List",
            "targets": [
              {
                "type": "ChaosTarget",
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/chaosstudiodemo/providers/Microsoft.DocumentDB/databaseAccounts/myDB/providers/Microsoft.Chaos/targets/Microsoft-CosmosDB"
              }
            ]
          }
        ]
      }
    }
    ```
    
2. Cree el experimento mediante la CLI de Azure, reemplazando `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` y `$EXPERIMENT_NAME` por las propiedades del experimento. Asegúrese de que ha guardado y cargado el JSON del experimento y actualice `experiment.json` con el nombre de archivo JSON.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    Cada experimento crea una identidad administrada asignada por el sistema correspondiente. Tenga en cuenta `principalId` para esta identidad en la respuesta para el paso siguiente.

## <a name="give-experiment-permission-to-your-azure-cosmos-db-account"></a>Conceder permiso de experimento a la cuenta de Azure Cosmos DB
Al crear un experimento de caos, Chaos Studio crea una identidad administrada asignada por el sistema que ejecuta errores en los recursos de destino. Esta identidad debe tener los [permisos adecuados](chaos-studio-fault-providers.md) en el recurso de destino para que el experimento se ejecute correctamente.

Dé al experimento acceso a los recursos mediante el comando siguiente, reemplazando `$EXPERIMENT_PRINCIPAL_ID` por la ID principal del paso anterior y `$RESOURCE_ID` por el identificador de recurso del recurso de destino (en este caso, el identificador de recurso de la instancia de base de datos de Cosmos). Cambie el rol al rol [integrado adecuado para ese tipo de recurso](chaos-studio-fault-providers.md). Ejecute este comando para cada recurso de destino en el experimento. 

```azurecli-interactive
az role assignment create --role "Cosmos DB Operator" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID
```

## <a name="run-your-experiment"></a>Ejecutar el experimento
Ahora ya puede ejecutar el experimento. Para ver el impacto, se recomienda abrir la información general de la cuenta de Azure Cosmos DB y ir a **Replicar datos globalmente** en una pestaña independiente del explorador. La actualización periódica durante el experimento mostrará el intercambio de regiones.

1. Cree el experimento mediante la CLI de Azure, reemplazando `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` y `$EXPERIMENT_NAME` por las propiedades del experimento.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. La respuesta incluye una dirección URL de estado que puede usar para consultar el estado del experimento mientras se ejecuta el experimento.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha ejecutado un experimento directo del servicio de la base de datos de Azure Cosmos, podrá:
- [Crear un experimento que usa errores basados en agente](chaos-studio-tutorial-agent-based-portal.md)
- [Administrar el experimento](chaos-studio-run-experiment.md)
