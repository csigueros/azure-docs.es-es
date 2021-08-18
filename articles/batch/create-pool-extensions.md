---
title: Uso de extensiones con grupos de Batch
description: Las extensiones son pequeñas aplicaciones que facilitan la configuración e instalación posteriores al aprovisionamiento en nodos de ejecución de Batch.
ms.topic: how-to
ms.date: 08/06/2021
ms.openlocfilehash: b9c34dd5d191253352181158ece29218657f8d75
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722638"
---
# <a name="use-extensions-with-batch-pools"></a>Uso de extensiones con grupos de Batch

Las extensiones son pequeñas aplicaciones que facilitan la configuración e instalación posteriores al aprovisionamiento en nodos de ejecución de Batch. Puede seleccionar cualquiera de las extensiones permitidas por Azure Batch y instalarlas en los nodos de ejecución a medida que se aprovisionan. Después, la extensión puede realizar su operación esperada.

Puede comprobar el estado activo de las extensiones que usa y recuperar la información que estas devuelven para llevar a cabo cualquier funcionalidad de detección, corrección o diagnóstico.

## <a name="prerequisites"></a>Prerrequisitos

- Los grupos con extensiones deben usar la [configuración de la máquina virtual](nodes-and-pools.md#virtual-machine-configuration).
- El tipo de extensión CustomScript está reservado para el servicio Azure Batch y no se puede invalidar.
- Es posible que algunas extensiones necesiten una identidad administrada de nivel de grupo accesible en el contexto de un nodo de ejecución para funcionar correctamente. Consulte [Configuración de identidades administradas en grupos de Batch](managed-identity-pools.md), si procede, para las extensiones.

### <a name="supported-extensions"></a>Extensiones admitidas

Actualmente se pueden instalar las extensiones siguientes al crear un grupo de Batch.

- La extensión de Azure Key Vault para [Linux](../virtual-machines/extensions/key-vault-linux.md) y [Windows](../virtual-machines/extensions/key-vault-windows.md).
- La extensión de Log Analytics y de supervisión para [Linux](../virtual-machines/extensions/oms-linux.md) y [Windows](../virtual-machines/extensions/oms-windows.md).

Puede solicitar compatibilidad para publicadores o tipos de extensión adicionales abriendo una solicitud de soporte técnico.

## <a name="create-a-pool-with-extensions"></a>Creación de un grupo con extensiones

En el ejemplo siguiente se crea un grupo de Batch de nodos de Linux que usa la extensión de Azure Key Vault.

URI DE LA API REST

```http
 PUT https://management.azure.com/subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>/providers/Microsoft.Batch/batchAccounts/<batchaccountName>/pools/<batchpoolName>?api-version=2021-01-01
```

Cuerpo de la solicitud

```json
{
    "name": "test1",
    "type": "Microsoft.Batch/batchAccounts/pools",
    "properties": {
        "vmSize": "STANDARD_DS2_V2",
        "taskSchedulingPolicy": {
            "nodeFillType": "Pack"
        },
        "deploymentConfiguration": {
            "virtualMachineConfiguration": {
                "imageReference": {
                    "publisher": "canonical",
                    "offer": "ubuntuserver",
                    "sku": "18.04-lts",
                    "version": "latest"
                },
                "nodeAgentSkuId": "batch.node.ubuntu 18.04",
                "extensions": [
                    {
                        "name": "secretext",
                        "type": "KeyVaultForLinux",
                        "publisher": "Microsoft.Azure.KeyVault",
                        "typeHandlerVersion": "1.0",
                        "autoUpgradeMinorVersion": true,
                        "settings": {
                            "secretsManagementSettings": {
                                "pollingIntervalInS": "300",
                                "certificateStoreLocation": "/var/lib/waagent/Microsoft.Azure.KeyVault",
                                "requireInitialSync": true,
                                "observedCertificates": [
                                    "https://testkvwestus2.vault.azure.net/secrets/authsecreat"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "885b1a3d-f13c-4030-afcf-9f05044d78dc"
                            }
                        },
                        "protectedSettings":{}
                    }
                ]
            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "targetLowPriorityNodes": 0,
                "resizeTimeout": "PT15M"
            }
        }
```

## <a name="get-extension-data-from-a-pool"></a>Obtención de datos de extensión de un grupo

En el ejemplo siguiente se recuperan datos de la extensión de Azure Key Vault.

URI DE LA API REST

```http
 GET https://<accountname>.<region>.batch.azure.com/pools/test3/nodes/tvmps_a3ce79db285d6c124399c5bd3f3cf308d652c89675d9f1f14bfc184476525278_d/extensions/secretext?api-version=2010-01-01
```

Cuerpo de la respuesta

```json
{
  "odata.metadata":"https://testwestus2batch.westus2.batch.azure.com/$metadata#extensions/@Element","instanceView":{
    "name":"secretext","statuses":[
      {
        "code":"ProvisioningState/succeeded","level":0,"displayStatus":"Provisioning succeeded","message":"Successfully started Key Vault extension service. 2021-02-08T19:49:39Z"
      }
    ]
  },"vmExtension":{
    "name":"KVExtensions","publisher":"Microsoft.Azure.KeyVault","type":"KeyVaultForLinux","typeHandlerVersion":"1.0","autoUpgradeMinorVersion":true,"settings":"{\r\n  \"secretsManagementSettings\": {\r\n    \"pollingIntervalInS\": \"300\",\r\n    \"certificateStoreLocation\": \"/var/lib/waagent/Microsoft.Azure.KeyVault\",\r\n    \"requireInitialSync\": true,\r\n    \"observedCertificates\": [\r\n      \"https://testkvwestus2.vault.azure.net/secrets/testumi\"\r\n    ]\r\n  },\r\n  \"authenticationSettings\": {\r\n    \"msiEndpoint\": \"http://169.254.169.254/metadata/identity\",\r\n    \"msiClientId\": \"885b1a3d-f13c-4030-afcf-922f05044d78dc\"\r\n  }\r\n}"
  }
}

```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información sobre cómo [copiar aplicaciones y datos en nodos de grupo](batch-applications-to-pool-nodes.md).
- Aprenda sobre cómo trabajar con [nodos y grupos](nodes-and-pools.md).
