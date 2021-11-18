---
title: Cree un experimento que use un error basado en agente con Azure Chaos Studio con la CLI de Azure
description: Cree un experimento que use un error basado en agente y configure el agente Chaos con la CLI de Azure
author: johnkemnetz
ms.topic: how-to
ms.date: 11/10/2021
ms.author: johnkem
ms.service: chaos-studio
ms.custom: template-how-to, ignite-fall-2021
ms.openlocfilehash: e2f605e06cd7d57acbe9fe421f4c1b883cc4d193
ms.sourcegitcommit: 901ea2c2e12c5ed009f642ae8021e27d64d6741e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132373258"
---
# <a name="create-a-chaos-experiment-that-uses-an-agent-based-fault-on-a-virtual-machine-or-virtual-machine-scale-set-with-the-azure-cli"></a>Cree un experimento de caos que use un error basado en agente en una máquina virtual o un conjunto de escalado de máquinas virtuales con la CLI de Azure

Puede usar un experimento de caos para comprobar que la aplicación es resistente a los errores provocando esos errores en un entorno controlado. En esta guía, se generará un evento de CPU elevada en una máquina virtual Linux usando un experimento del caos y Azure Chaos Studio. Ejecutar este experimento le puede permitir defenderse ante el hecho de que una aplicación se quede sin recurso.

Estos mismos pasos se pueden usar para configurar y ejecutar un experimento para cualquier error basado en agente. Un error **basado en agente** requiere configurar e instalar el agente del caos, a diferencia de un error directo de servicio, que se produce directamente en un recurso de Azure sin necesidad de instrumentación.


## <a name="prerequisites"></a>Prerrequisitos

- Suscripción a Azure. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)] 
- Una máquina virtual. Si no tiene ninguna máquina virtual, puede [seguir estos pasos para crear una](../virtual-machines/linux/quick-create-portal.md).
- Una configuración de red que le permita [acceder mediante SSH a su máquina virtual](../virtual-machines/ssh-keys-portal.md).
- Una identidad administrada asignada por el usuario. Si no tiene ninguna identidad administrada asignada por el usuario, puede [seguir estos pasos para crear una](../active-directory/managed-identities-azure-resources/how-manage-user-assigned-managed-identities.md).

## <a name="launch-azure-cloud-shell"></a>Inicio de Azure Cloud Shell

Azure Cloud Shell es un shell interactivo gratuito que puede usar para ejecutar los pasos de este artículo. Tiene las herramientas comunes de Azure preinstaladas y configuradas para usarlas en la cuenta. 

Para abrir Cloud Shell, seleccione **Pruébelo** en la esquina superior derecha de un bloque de código. También puede abrir Cloud Shell en una pestaña independiente acudiendo a [https://shell.azure.com/bash](https://shell.azure.com/bash). Seleccione **Copiar** para copiar los bloques de código, péguelos en Cloud Shell y, después, seleccione **Entrar** para ejecutarlos.

Si prefiere instalar y usar la CLI en un entorno local, para este tutorial se requiere la versión 2.0.30 de la CLI de Azure o una versión posterior. Ejecute `az --version` para encontrar la versión. Si necesita instalarla o actualizarla, vea [Instalación de la CLI de Azure]( /cli/azure/install-azure-cli).

## <a name="assign-managed-identity-to-the-virtual-machine"></a>Asignación de una identidad administrada a la máquina virtual

Antes de configurar Chaos Studio en la máquina virtual, debe asignar una identidad administrada asignada por el usuario a cada máquina virtual o conjunto de escalado de máquinas virtuales donde planea instalar el agente mediante el comando `az vm identity assign` o `az vmss identity assign`. Reemplace `$VM_RESOURCE_ID`/`$VMSS_RESOURCE_ID`por el identificador de recurso de la máquina virtual que va a agregar como destino del caos y `$MANAGED_IDENTITY_RESOURCE_ID`por el identificador de recurso de la identidad administrada asignada por el usuario.

**Máquina virtual**

```azurecli-interactive
az vm identity assign --ids $VM_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

**Conjunto de escalado de máquinas virtuales**

```azurecli-interactive
az vmss identity assign --ids $VMSS_RESOURCE_ID --identities $MANAGED_IDENTITY_RESOURCE_ID
```

## <a name="enable-chaos-studio-on-your-virtual-machine"></a>Habilitación de Chaos Studio en una máquina virtual

Chaos Studio no puede insertar errores en una máquina virtual a menos que esta se haya incorporado primero a Chaos Studio. Se incorpora una máquina virtual a Chaos Studio creando un [destino y capacidades](chaos-studio-targets-capabilities.md) en el recurso y, después, instalando el agente del caos. Las máquinas virtuales tienen dos tipos de destino: uno que habilita los errores directos de servicio (no se requiere ningún agente) y otro que habilita los errores basados en agente (se requiera instalar un agente). El agente del caos es una aplicación instalada en su máquina virtual como una [extensión de máquina virtual](../virtual-machines/extensions/overview.md) que le permite insertar errores en el sistema operativo invitado.

### <a name="install-stress-ng-linux-only"></a>Instalación de stress-ng (solamente Linux)

El agente de Chaos Studio para Linux requiere stress-ng, una aplicación de código abierto que puede generar varios eventos de estrés en una máquina virtual. Puede instalar stress-ng [conectándose a su máquina virtual Linux](../virtual-machines/ssh-keys-portal.md) y ejecutando el comando de instalación adecuado para su administrador de paquetes; por ejemplo:

```bash
sudo apt-get update && sudo apt-get -y install unzip && sudo apt-get -y install stress-ng
```

O:

```bash
sudo dnf -y install https://dl.fedoraproject.org/pub/epel/epel-release-latest-8.noarch.rpm && sudo yum -y install stress-ng
```

### <a name="enable-chaos-target-and-capabilities"></a>Habilitación del destino y las funcionalidades del caos

A continuación, configure un destino Microsoft-Agent en cada máquina virtual o conjunto de escalado de máquinas virtuales que especifique la identidad administrada asignada por el usuario que usará el agente para conectarse a Chaos Studio. En este ejemplo, usamos una identidad administrada para todas las máquinas virtuales. Se debe crear un destino mediante la API REST. En este ejemplo, se usa el comando `az rest` de la CLI para ejecutar las llamadas a la API REST.

1. Modifique el siguiente código JSON reemplazando `$USER_IDENTITY_CLIENT_ID`por el identificador de cliente de la identidad administrada, que puede encontrar en la información general de Azure Portal de la identidad administrada asignada por el usuario que creó y por el `$USER_IDENTITY_TENANT_ID` identificador de inquilino de Azure, que puede encontrar en el Azure Portal en **Azure Active Directory** en **Información del inquilino**. Guarde el archivo JSON como un archivo en la misma ubicación en la que está ejecutando la CLI de Azure (en Cloud Shell puede arrastrar y colocar el archivo JSON para cargarlo).

    ```json
    {
      "properties": {
        "identities": [
          {
            "clientId": "$USER_IDENTITY_CLIENT_ID",
            "tenantId": "$USER_IDENTITY_TENANT_ID",
            "type": "AzureManagedIdentity"
          }
        ]
      }
    }
    ```

2. Cree el destino reemplazando `$RESOURCE_ID` por el identificador de recurso de la máquina virtual de destino o del conjunto de escalado de máquinas virtuales. Sustituya `target.json` por el nombre del archivo JSON que creó en el paso anterior.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview --body @target.json --query properties.agentProfileId -o tsv
    ```

3. Copie el GUID del **agentProfileId** devuelto por este comando para usarlo en un paso posterior.

4. Cree las funcionalidades en el destino mediante la creación de un archivo capability.json como se muestra a continuación. Guarde el archivo JSON en la misma ubicación en la está ejecutando la CLI de Azure. Reemplace `$RESOURCE_ID` por el identificador de recurso de la máquina virtual de destino o del conjunto de escalado de máquinas virtuales y`$CAPABILITY` por el [nombre de la funcionalidad de error que está habilitando](chaos-studio-fault-library.md).
    
    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/$CAPABILITY?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

    Por ejemplo, si habilita la funcionalidad de Presión de CPU:

    ```azurecli-interactive
    az rest --method put --url "https://management.azure.com/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myVM/providers/Microsoft.Chaos/targets/Microsoft-Agent/capabilities/CPUPressure-1.0?api-version=2021-09-15-preview" --body "{\"properties\":{}}"
    ```

### <a name="install-the-chaos-studio-virtual-machine-extension"></a>Instalación de la extensión de máquina virtual de Chaos Studio

El agente Chaos es una aplicación que se ejecuta en las instancias de máquina virtual o conjunto de escalado de máquinas virtuales para ejecutar errores basados en agente. Durante la instalación, configure el agente con la identidad administrada que el agente debe usar para autenticarse en Chaos Studio, el identificador de perfil del destino de Microsoft-Agent que creó y, opcionalmente, una clave de instrumentación de Application Insights que permite al agente enviar eventos de diagnóstico a Azure Application Insights.

1. Antes de comenzar, asegúrese de que tiene la siguiente información:
    * **agentProfileId:** la propiedad devuelta al crear el destino. Si no tiene esta propiedad, puede ejecutar `az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` y copiar la propiedad `agentProfileId`.
    * **ClientId**: la identidad de cliente de la identidad gestionada asignada por el usuario al destino. Si no tiene esta propiedad, puede ejecutar `az rest --method get --uri https://management.azure.com/$RESOURCE_ID/providers/Microsoft.Chaos/targets/Microsoft-Agent?api-version=2021-09-15-preview` y copiar la propiedad `clientId`
    * (opcionalmente) **AppInsightsKey:** la clave de instrumentación del componente Application Insights, que puede encontrar en la página Application Insights del portal en **Essentials**.

2. Instale la extensión de máquina virtual de Chaos Studio. Reemplace `$VM_RESOURCE_ID` por el identificador de recurso de la máquina virtual o reemplace `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP`y `$VMSS_NAME` por esas propiedades para el conjunto de escalado de máquinas virtuales. Reemplace `$AGENT_PROFILE_ID`por agentProfileId, `$USER_IDENTITY_CLIENT_ID`por el clientID de la identidad administrada y `$APP_INSIGHTS_KEY`por la clave de instrumentación de Application Insights. Si no usa Application Insights, elimine ese par clave/valor.

    #### <a name="install-the-agent-on-a-virtual-machine"></a>Instalación del agente en una máquina virtual

    **Windows**

    ```azurecli-interactive
    az vm extension set --ids $VM_RESOURCE_ID --name ChaosWindowsAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    **Linux**

    ```azurecli-interactive
    az vm extension set --ids $VM_RESOURCE_ID --name ChaosLinuxAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    #### <a name="install-the-agent-on-a-virtual-machine-scale-set"></a>Instalación del agente en un conjunto de escalado de máquinas virtuales

    **Windows**

    ```azurecli-interactive
    az vmss extension set --subscription $SUBSCRIPTION_ID --resource-group $RESOURCE_GROUP --vmss-name $VMSS_NAME --name ChaosWindowsAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```

    **Linux**

    ```azurecli-interactive
    az vmss extension set --subscription $SUBSCRIPTION_ID --resource-group $RESOURCE_GROUP --vmss-name $VMSS_NAME --name ChaosLinuxAgent --publisher Microsoft.Azure.Chaos --version 1.0 --settings '{"profile": "$AGENT_PROFILE_ID", "auth.msi.clientid":"$USER_IDENTITY_CLIENT_ID", "appinsightskey":"$APP_INSIGHTS_KEY"}'
    ```
3. Si configura un conjunto de escalado de máquinas virtuales, compruebe que las instancias se hayan actualizado al modelo más reciente. 

## <a name="create-an-experiment"></a>Creación de un experimento

Ahora que tiene la máquina virtual incorporada, puede crear un experimento. Un experimento de caos define las acciones que desea realizar en los recursos de destino, organizados en pasos, que se ejecutan secuencialmente, y ramas, que se ejecutan en paralelo.

1. Formule el JSON del experimento a partir del ejemplo JSON indicado a continuación. Modifique el archivo JSON para que se corresponda con el experimento que desea ejecutar mediante [la API Crear experimento](/rest/api/chaosstudio/experiments/create-or-update) y la [biblioteca de errores](chaos-studio-fault-library.md)

    ```json
    {
      "identity": {
        "type": "SystemAssigned"
      },
      "location": "centralus",
      "properties": {
        "selectors": [
          {
            "id": "Selector1",
            "targets": [
              {
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myWindowsVM/providers/Microsoft.Chaos/targets/microsoft-agent",
                "type": "ChaosTarget"
              },
              {
                "id": "/subscriptions/b65f2fec-d6b2-4edd-817e-9339d8c01dc4/resourceGroups/myRG/providers/Microsoft.Compute/virtualMachines/myLinuxVM/providers/Microsoft.Chaos/targets/micosoft-agent",
                "type": "ChaosTarget"
              }
            ],
            "type": "List"
          }
        ],
        "steps": [
          {
            "branches": [
              {
                "actions": [
                  {
                    "duration": "PT10M",
                    "name": "urn:csci:microsoft:agent:cpuPressure/1.0",
                    "parameters": [
                      {
                        "key": "pressureLevel",
                        "value": "95"
                      }
                    ],
                    "selectorId": "Selector1",
                    "type": "continuous"
                  }
                ],
                "name": "Branch 1"
              }
            ],
            "name": "Step 1"
          }
        ]
      }
    }
    ```

    Si se ejecuta en un conjunto de escalado de máquinas virtuales, modifique los parámetros de error para incluir el número o números de instancia al destino:

    ```json
    "parameters": [
      {
        "key": "pressureLevel",
        "value": "95"
      },
      {
        "key": "virtualMachineScaleSetInstances",
        "value": "[0,1,2]"
      }
    ]
    ```

    Puede identificar los números de instancia del conjunto de escalado en Azure Portal al ir al conjunto de escalado de máquinas virtuales y hacer clic en **Instancias**. El nombre de instancia finalizará en el número de instancia.
    
2. Cree el experimento mediante la CLI de Azure, reemplazando `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` y `$EXPERIMENT_NAME` por las propiedades del experimento. Asegúrese de que ha guardado y cargado el JSON del experimento y actualice `experiment.json` con el nombre de archivo JSON.

    ```azurecli-interactive
    az rest --method put --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME?api-version=2021-09-15-preview --body @experiment.json
    ```

    Cada experimento crea una identidad administrada asignada por el sistema correspondiente. Tenga en cuenta `principalId` para esta identidad en la respuesta para el paso siguiente.

## <a name="give-experiment-permission-to-your-virtual-machine"></a>Concesión de permiso de experimento a la máquina virtual
Al crear un experimento de caos, Chaos Studio crea una identidad administrada asignada por el sistema que ejecuta errores en los recursos de destino. Esta identidad debe tener los [permisos adecuados](chaos-studio-fault-providers.md) en el recurso de destino para que el experimento se ejecute correctamente. El rol Lector es necesario para los errores basados en agente. Otros roles que no tienen el permiso */Lectura, como, por ejemplo, el Colaborador de máquina virtual, no concederán el permiso adecuado para los errores basados en agente.

Dé al experimento acceso a la máquina virtual o al conjunto de escalado de máquinas virtuales mediante el comando siguiente, reemplazando `$EXPERIMENT_PRINCIPAL_ID` por la ID principal del paso anterior y `$RESOURCE_ID` por el identificador de recurso de la máquina virtual de destino o del conjunto de escalado de máquinas virtuales (el identificador de recurso de la máquina virtual, no el identificador de recurso del agente de caos usado en la definición del experimento). Ejecute este comando para cada máquina virtual o conjunto de escalado de máquinas virtuales de destino en el experimento.

```azurecli-interactive
az role assignment create --role "Reader" --assignee-principal-type "ServicePrincipal" --assignee-object-id $EXPERIMENT_PRINCIPAL_ID --scope $RESOURCE_ID 
```


## <a name="run-your-experiment"></a>Ejecutar el experimento
Ahora ya puede ejecutar el experimento. Para ver el impacto, recomendamos abrir un [gráfico de métricas de Azure Monitor](../azure-monitor/essentials/tutorial-metrics.md) con la presión de CPU de su máquina virtual en otra pestaña del navegador.

1. Cree el experimento mediante la CLI de Azure, reemplazando `$SUBSCRIPTION_ID`, `$RESOURCE_GROUP` y `$EXPERIMENT_NAME` por las propiedades del experimento.

    ```azurecli-interactive
    az rest --method post --uri https://management.azure.com/subscriptions/$SUBSCRIPTION_ID/resourceGroups/$RESOURCE_GROUP/providers/Microsoft.Chaos/experiments/$EXPERIMENT_NAME/start?api-version=2021-09-15-preview
    ```

2. La respuesta incluye una dirección URL de estado que puede usar para consultar el estado del experimento mientras se ejecuta el experimento.

## <a name="next-steps"></a>Pasos siguientes
Ahora que ha ejecutado un experimento basado en agente, podrá:
- [Crear un experimento que use errores directos de servicio](chaos-studio-tutorial-service-direct-portal.md)
- [Administrar el experimento](chaos-studio-run-experiment.md)