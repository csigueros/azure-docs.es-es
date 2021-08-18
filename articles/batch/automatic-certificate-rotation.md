---
title: Habilitar la rotación automática de certificados en un grupo de Batch
description: Puede crear un grupo de Batch con una identidad administrada y un certificado que se renueve automáticamente.
ms.topic: conceptual
ms.date: 07/16/2021
ms.openlocfilehash: 9a8f4d785c9a411df6c867724f057b1dc9b29722
ms.sourcegitcommit: 47ac63339ca645096bd3a1ac96b5192852fc7fb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114362424"
---
# <a name="enable-automatic-certificate-rotation-in-a-batch-pool"></a>Habilitar la rotación automática de certificados en un grupo de Batch

 Puede crear un grupo de Batch con un certificado que se renueve automáticamente. Para ello, se debe crear el grupo con una [identidad administrada asignada por el usuario](managed-identity-pools.md) que tendrá acceso al certificado en [Azure Key Vault](../key-vault/general/overview.md).

## <a name="create-a-user-assigned-identity"></a>Creación de una identidad asignada por el usuario

En primer lugar, [cree la identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) en el mismo inquilino que la cuenta de Batch. No es necesario que esta identidad administrada esté en el mismo grupo de recursos ni en la misma suscripción.

Asegúrese de anotar el **id. de cliente** de la identidad gestionada asignada al usuario. Lo necesitará más adelante.

:::image type="content" source="media/automatic-certificate-rotation/client-id.png" alt-text="Captura de pantalla que muestra el ID. de cliente de una identidad administrada asignada por el usuario en el Azure Portal.":::

## <a name="create-your-certificate"></a>Creación del certificado

A continuación, deberá crear un certificado y agregarlo a Azure Key Vault. Si aún no ha creado un almacén de claves, deberá hacerlo en primer lugar. Para las instrucciones, consulte [Inicio rápido: Establecimiento y recuperación de un certificado de Azure Key Vault mediante Azure Portal](../key-vault/certificates/quick-create-portal.md).

Al crear el certificado, asegúrese de establecer el **tipo de acción duración** para que se renueve automáticamente y especifique el número de días después del cual se debe renovar el certificado.

:::image type="content" source="media/automatic-certificate-rotation/certificate.png" alt-text="Captura de pantalla de la creación del certificado en Azure Portal.":::

Una vez creado el certificado, tome nota de su **identificador de secreto**. Lo necesitará más adelante.

:::image type="content" source="media/automatic-certificate-rotation/secret-identifier.png" alt-text="Captura de pantalla que muestra el identificador de secreto de un certificado.":::

## <a name="add-an-access-policy-in-azure-key-vault"></a>Agregar una política de acceso en Azure Key Vault

En el almacén de claves, asigne una directiva de acceso Key Vault que permita que la identidad administrada asignada por el usuario tenga acceso a secretos y certificados. Para instrucciones detalladas, consulte [Asignación de una directiva de acceso de Key Vault mediante Azure Portal](../key-vault/general/assign-access-policy-portal.md).

## <a name="create-a-batch-pool-with-a-user-assigned-managed-identity"></a>Creación de un grupo de Batch con una identidad administrada asignada por el usuario

Cree un grupo de Batch con la identidad administrada mediante la [biblioteca de administración de .net de Batch](/dotnet/api/overview/azure/batch#management-library). Para obtener más información, vea [configurar identidades administradas en grupos de Batch](managed-identity-pools.md).

En el ejemplo siguiente se usa la API de REST de administración de batch para crear un grupo. Asegúrese de usar el **identificador de secreto** del certificado para `observedCertificates` y el identificador de **cliente** de la identidad administrada para `msiClientId`, reemplazando los datos de ejemplo siguientes.

URI DE LA API REST

```http
PUT https://management.azure.com/subscriptions/<subscriptionid>/resourceGroups/<resourcegroupName>/providers/Microsoft.Batch/batchAccounts/<batchaccountname>/pools/<poolname>?api-version=2021-01-01
```

Cuerpo de la solicitud

```json
{
    "name": "test2",
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
                        "name": "KVExtensions",
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
                                    "https://testkvwestus2s.vault.azure.net/secrets/authcertforumatesting/8f5f3f491afd48cb99286ba2aacd39af"
                                ]
                            },
                            "authenticationSettings": {
                                "msiEndpoint": "http://169.254.169.254/metadata/identity",
                                "msiClientId": "b9f6dd56-d2d6-4967-99d7-8062d56fd84c"
                            }  }, "protectedSettings":{}
                    }                ]            }
        },
        "scaleSettings": {
            "fixedScale": {
                "targetDedicatedNodes": 1,
                "resizeTimeout": "PT15M"
            }
        },
    },
    "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
            "/subscriptions/042998e4-36dc-4b7d-8ce3-a7a2c4877d33/resourceGroups/ACR/providers/Microsoft.ManagedIdentity/userAssignedIdentities/testumaforpools": {}
        }
    }
}

```

## <a name="validate-the-certificate"></a>Validación del certificado

Para confirmar que el certificado se ha implementado correctamente, inicie sesión en el nodo de proceso. Debería ver un resultado similar al siguiente:

```
root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status# cat 1.status
[{"status":{"code":0,"formattedMessage":{"lang":"en","message":"Successfully started Key Vault extension service. 2021-03-03T23:12:23Z"},"operation":"Service start.","status":"success"},"timestampUTC":"2021-03-03T23:12:23Z","version":"1.0"}]root@74773db5fe1b42ab9a4b6cf679d929da000000:/var/lib/waagent/Microsoft.Azure.KeyVault.KeyVaultForLinux-1.0.1363.13/status#
```

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre las [identidades administradas para recursos de Azure](../active-directory/managed-identities-azure-resources/overview.md).
- Aprenda a usar [claves administradas por el cliente con identidades administradas por el usuario](batch-customer-managed-key.md).
