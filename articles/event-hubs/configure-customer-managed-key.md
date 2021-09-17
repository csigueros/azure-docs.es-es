---
title: Configuración de su propia clave para cifrar datos en reposo de Azure Event Hubs
description: En este artículo se proporciona información sobre cómo configurar su propia clave para cifrar datos en reposo de Azure Event Hubs.
ms.topic: conceptual
ms.date: 08/18/2021
ms.openlocfilehash: ed230cc1e0a377a580e0f23feeac74f6e0b5489d
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122515710"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest"></a>Configuración de claves administradas por el cliente para cifrar datos en reposo de Azure Event Hubs
Azure Event Hubs proporciona cifrado de datos en reposo con Azure Storage Service Encryption (Azure SSE). El servicio de Event Hubs usa Azure Storage para almacenar los datos. Todos los datos almacenados con Azure Storage se cifran con claves administradas por Microsoft. Si usa su propia clave (también conocida como Bring Your Own Key [BYOK] o clave administrada por el cliente), los datos se cifran mediante la clave administrada por Microsoft, pero además la clave administrada por Microsoft se cifrará mediante la clave administrada por el cliente. Esta característica permite crear, rotar, deshabilitar y revocar el acceso a las claves administradas por el cliente que se usan para cifrar claves administradas por Microsoft. La habilitación de la característica BYOK es un proceso que solo hay que configurar una vez en el espacio de nombres.

> [!IMPORTANT]
> - La funcionalidad BYOK se admite en los niveles **Premium** y **Dedicado** de Event Hubs.
> - El cifrado solo se puede habilitar para espacios de nombres nuevos o vacíos. Si el espacio de nombres contiene centros de eventos, se producirá un error en la operación de cifrado.

Puede usar Azure Key Vault para administrar las claves y auditar su uso. Puede crear sus propias claves y almacenarlas en un almacén de claves, o puede usar las API de Azure Key Vault para generarlas. Para obtener más información sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](../key-vault/general/overview.md)

En este artículo se muestra cómo configurar un almacén de claves con claves administradas del cliente mediante Azure Portal. Para obtener información sobre cómo crear un almacén de claves mediante Azure Portal, consulte [Inicio rápido: Creación de una instancia de Azure Key Vault mediante Azure Portal](../key-vault/general/quick-create-portal.md).

## <a name="enable-customer-managed-keys-azure-portal"></a>Habilitación de las claves administradas por el cliente (Azure Portal)
Para habilitar las claves administradas por el cliente en Azure Portal, siga estos pasos. Si usa el nivel Dedicado, vaya primero al clúster dedicado de Event Hubs.

1. Seleccione el espacio de nombres en el que desea habilitar BYOK.
1. En la página **Configuración** del espacio de nombres de Event Hubs, seleccione **Cifrado**. 
1. Seleccione **Cifrado de claves en reposo que administra el cliente** como se muestra en la siguiente imagen. 

    ![Habilitación de clave administrada por el cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Configuración de un almacén de claves con claves
Después de habilitar las claves administradas por el cliente, debe asociar la clave administrada por el cliente con su espacio de nombres de Azure Event Hubs. Event Hubs solo admite Azure Key Vault. Si habilita la opción **Encryption with customer-managed key** (Cifrado con clave administrada por el cliente) en la sección anterior, debe importar la clave en Azure Key Vault. Recuerde que las claves deben tener habilitadas las opciones **Eliminación temporal** y **No purgar**. Estas opciones se pueden configurar mediante [PowerShell](../key-vault/general/key-vault-recovery.md) o la [CLI](../key-vault/general/key-vault-recovery.md).

1. Para crear un nuevo almacén de claves, siga el [inicio rápido](../key-vault/general/overview.md) de Azure Key Vault. Para más información sobre cómo importar claves existentes, consulte [Información acerca de claves, secretos y certificados](../key-vault/general/about-keys-secrets-certificates.md).

    > [!IMPORTANT]
    > El uso de claves administradas por el cliente con Azure Event Hubs requiere que el almacén de claves tenga configuradas dos propiedades obligatorias. Son las siguientes:  **Eliminación temporal** y **No purgar**. Estas propiedades están habilitadas de manera predeterminada cuando crea un nuevo almacén de claves en Azure Portal. Sin embargo, si tiene que habilitar estas propiedades en un almacén de claves existente, deberá usar PowerShell o la CLI de Azure.
1. Para activar la eliminación temporal y la protección de purgas al crear un almacén, use el comando [az keyvault create](/cli/azure/keyvault#az_keyvault_create).

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para agregar la protección de purgas a un almacén existente (que ya tenga habilitada la eliminación temporal), use el comando [az keyvault update](/cli/azure/keyvault#az_keyvault_update).

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Siga estos pasos para crear claves:
    1. Para crear una nueva clave, seleccione **Generar/Importar** en el menú **Claves** en **Configuración**.
        
        ![Seleccione el botón Generate/Import (Generar/importar).](./media/configure-customer-managed-key/select-generate-import.png)
    1. Establezca **Opciones** en **Generar** y asigne un nombre a la clave.

        ![Crear una clave](./media/configure-customer-managed-key/create-key.png) 
    1. Ahora puede seleccionar esta clave para asociarla con el espacio de nombres de Event Hubs para el cifrado en la lista desplegable. 

        ![Selección de clave del almacén de claves](./media/configure-customer-managed-key/select-key-from-key-vault.png)

        > [!NOTE]
        > Para lograr redundancia, puede agregar hasta 3 claves. En caso de que una de las claves haya expirado o no sea accesible, se usarán las demás claves para el cifrado.
    1. Rellene los detalles de la clave y haga clic en **Seleccionar**. Esto habilitará el cifrado de la clave administrada por Microsoft con su clave (clave administrada por el cliente). 

## <a name="managed-identities"></a>Identidades administradas
Hay dos tipos de identidades administradas que puede asignar a un espacio de nombres de Event Hubs.

- **Asignadas por el sistema**: puede habilitar una identidad administrada directamente en un espacio de nombres de Event Hubs. Cuando se habilita una identidad administrada asignada por el sistema, se crea una identidad en Azure AD vinculada al ciclo de vida de ese espacio de nombres de Event Hubs. Por tanto, cuando se elimina el espacio de nombres, Azure elimina automáticamente la identidad. Por naturaleza, solo ese recurso (espacio de nombres) de Azure puede usar esta identidad para solicitar tokens de Azure AD.
- **Asignadas por el usuario**: también es posible crear una identidad administrada como recurso independiente de Azure, que se denomina identidad asignada por el usuario. Puede crear una identidad administrada asignada por el usuario y asignársela a uno o varios espacios de nombres de Event Hubs. En el caso de las identidades administradas asignadas por el usuario, la identidad se administra independientemente de los recursos que la utilicen. No están asociadas al ciclo de vida del espacio de nombres. Cuando no se necesite, la identidad asignada por el usuario se puede eliminar explícitamente.    

    Para más información, consulte [¿Qué es Managed Identities for Azure Resources?](../active-directory/managed-identities-azure-resources/overview.md)


## <a name="encrypt-using-system-assigned-identities-template"></a>Cifrado mediante identidades asignadas por el sistema (plantilla)
En esta sección se muestra cómo realizar las siguientes tareas con **plantillas de Azure Resource Manager**. 

1. Cree un **espacio de nombres de Event Hubs** con una identidad de servicio administrada.
2. Cree un **almacén de claves** y conceda a la identidad de servicio acceso a él. 
3. Actualice el espacio de nombres de Event Hubs con la información del almacén de claves (clave/valor). 


### <a name="create-an-event-hubs-cluster-and-namespace-with-managed-service-identity"></a>Creación de un clúster y un espacio de nombres de Event Hubs con la identidad de servicio administrada
En esta sección se muestra cómo crear un espacio de nombres de Azure Event Hubs con la identidad de servicio administrada mediante una plantilla de Azure Resource Manager y PowerShell. 

1. Cree una plantilla de Azure Resource Manager para crear un espacio de nombres de Event Hubs con una identidad de servicio administrada. Asigne al archivo el nombre: **CreateEventHubClusterAndNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             },
             "dependsOn":[
                "[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]"
             ]
          }
       ],
       "outputs":{
          "EventHubNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.EventHub/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Cree un archivo de parámetros de plantilla llamado: **CreateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Reemplace los siguientes valores: 
    > - `<EventHubsClusterName>`: nombre del clúster de Event Hubs.    
    > - `<EventHubsNamespaceName>`: nombre del espacio de nombres de Event Hubs.
    > - `<Location>`: ubicación del espacio de nombres de Event Hubs.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    
    ```
3. Ejecute el siguiente comando de PowerShell para implementar la plantilla y crear un espacio de nombres de Event Hubs. A continuación, recupere el identificador del espacio de nombres de Event Hubs para usarlo más adelante. Antes de ejecutar el comando, reemplace `{MyRG}` por el nombre del grupo de recursos.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateEventHubClusterAndNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateEventHubClusterAndNamespace.json -TemplateParameterFile ./CreateEventHubClusterAndNamespaceParams.json

    $EventHubNamespaceId = $outputs.Outputs["eventHubNamespaceId"].value
    ```
 
### <a name="grant-event-hubs-namespace-identity-access-to-key-vault"></a>Concesión de acceso al almacén de claves a la identidad del espacio de nombres de Event Hubs

1. Ejecute el siguiente comando para crear un almacén de claves con las características **protección de purga** y **eliminación temporal** habilitadas. 

    ```powershell
    New-AzureRmKeyVault -Name {keyVaultName} -ResourceGroupName {RGName}  -Location {location} -EnableSoftDelete -EnablePurgeProtection    
    ```     
    
    O BIEN    
    
    Ejecute el siguiente comando para actualizar un **almacén de claves existente**. Antes de ejecutar el comando, especifique valores para los nombres de grupo de recursos y almacén de claves. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Establezca la directiva de acceso del almacén de claves para que la identidad administrada del espacio de nombres de Event Hubs pueda acceder al valor de la clave del almacén de claves. Use el identificador del espacio de nombres de Event Hubs de la sección anterior. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $EventHubNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-event-hubs-namespace-with-customer-managed-key-from-key-vault"></a>Cifrado de datos en el espacio de nombres de Event Hubs con la clave administrada por el cliente desde el almacén de claves
Hasta el momento ha realizado los pasos siguientes: 

1. Ha creado un espacio de nombres premium con una identidad administrada.
2. Cree un almacén de claves y conceda a la identidad administrada acceso al almacén de claves. 

En este paso, actualizará el espacio de nombres de Event Hubs con la información del almacén de claves. 

1. Cree un archivo JSON llamado **CreateEventHubClusterAndNamespace.json** con el contenido siguiente: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             }
          }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
             "properties":{
                "isAutoInflateEnabled":false,
                "maximumThroughputUnits":0,
                "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                      {
                         "keyName":"[parameters('keyName')]",
                         "keyVaultUri":"[parameters('keyVaultUri')]"
                      }
                   ]
                }
             }
          }
       ]
    }
    ``` 

2. Cree un archivo de parámetros de plantilla: **UpdateEventHubClusterAndNamespaceParams.json**. 

    > [!NOTE]
    > Reemplace los siguientes valores: 
    > - `<EventHubsClusterName>`: nombre del clúster de Event Hubs.        
    > - `<EventHubsNamespaceName>`: nombre del espacio de nombres de Event Hubs.
    > - `<Location>`: ubicación del espacio de nombres de Event Hubs.
    > - `<KeyVaultName>`: nombre de su almacén de claves.
    > - `<KeyName>`: nombre de la clave del almacén de claves.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "clusterName":{
             "value":"<EventHubsClusterName>"
          },
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          }
       }
    }
    ```             
3. Ejecute el siguiente comando de PowerShell para implementar la plantilla de Resource Manager. Antes de ejecutar el comando, reemplace `{MyRG}` por el nombre del grupo de recursos. 

    ```powershell
    New-AzResourceGroupDeployment -Name UpdateEventHubNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateEventHubClusterAndNamespace.json -TemplateParameterFile ./UpdateEventHubClusterAndNamespaceParams.json 
    ```

## <a name="encrypt-using-user-assigned-identities-template"></a>Cifrado mediante identidades asignadas por el usuario (plantilla)

1. Cree una **identidad asignada por el usuario**.
1. Cree un **almacén de claves** y conceda acceso a este a la identidad asignada por el usuario mediante directivas de acceso.
1. Cree un **espacio de nombres de Event Hubs** con la identidad administrada asignada por el usuario y la información del almacén de claves.

### <a name="create-a-user-assigned-identity"></a>Creación de una identidad asignada por el usuario
Siga las instrucciones de [Crear una identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) para crear una identidad asignada por el usuario. También puede crear una identidad asignada por el usuario mediante la [CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md), [PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), la [plantilla de Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) y [REST](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md). 

> [!NOTE]
> A un espacio de nombres se le pueden asignar hasta **4** identidades de usuario. Estas asociaciones se eliminan cuando se elimina el espacio de nombres o cuando `identity -> type` en la plantilla pasa a `None`. 

### <a name="create-a-key-vault-and-grant-access-to-user-assigned-identity"></a>Creación de un almacén de claves y concesión de acceso a la identidad asignada por el usuario 

1. Ejecute el siguiente comando para crear un almacén de claves con las características protección de purga y eliminación temporal habilitadas.

    ```azurepowershell-interactive
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName} -Location "{location}" -EnableSoftDelete -EnablePurgeProtection           
    ```
    
    O BIEN

    Ejecute el siguiente comando para actualizar un almacén de claves existente. Antes de ejecutar el comando, especifique valores para los nombres de grupo de recursos y almacén de claves.

    ```azurepowershell-interactive
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force            
    ```
2. Obtenga el **identificador de la entidad de servicio** para la identidad del usuario mediante el siguiente comando de PowerShell. En el ejemplo, `ud1` es la identidad asignada por el usuario que se usará para el cifrado.

    ```azurepowershell-interactive
    $servicePrincipal=Get-AzADServicePrincipal -SearchString "ud1"    
    ```
3. Conceda a la identidad asignada por el usuario acceso al almacén de claves mediante la asignación de una directiva de acceso.     

    ```azurepowershell-interactive
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $servicePrincipal.Id -PermissionsToKeys get,wrapKey,unwrapKey,list    
    ```

    > [!NOTE]
    > Puede agregar hasta **tres** claves, pero la identidad del usuario usada para el cifrado debe ser la misma para todas ellas. Actualmente, solo se admite una identidad de cifrado única. 

### <a name="create-an-event-hubs-namespace-with-user-identity-and-key-vault-information"></a>Creación de un espacio de nombres de Event Hubs con la identidad del usuario y la información del almacén de claves
En esta sección se proporciona un ejemplo que muestra cómo realizar las siguientes tareas mediante una plantilla de Azure Resource Manager. 

- Asigne una identidad administrada por el usuario a un espacio de nombres de Event Hubs.

    ```json
                "identity": {
                    "type": "UserAssigned",
                    "userAssignedIdentities": {
                        "[parameters('identity').userAssignedIdentity]": {}
                    }
                },
    ```    
- Habilite el cifrado en el espacio de nombres; para ello, especifique una clave del almacén de claves y la identidad administrada por el usuario para acceder a ella. 

    ```json
                    "encryption":{
                       "keySource":"Microsoft.KeyVault",
                       "keyVaultProperties":[
                            {
                                "keyName": "[parameters('keyName')]",
                                "keyVaultUri": "[parameters('keyVaultUri')]",
                                "identity": {
                                    "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                                }
                            }
                       ]
                    }
    ```
   

1. Cree un archivo JSON denominado **CreateEventHubsNamespaceWithUserIdentityAndEncryption.json** con el siguiente contenido:

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
        "clusterName":{
            "type":"string",
             "metadata":{
                "description":"Name for the Event Hub cluster."
             }
          },
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace to be created in cluster."
             }
          },
          "location":{
             "type":"string",
             "defaultValue":"[resourceGroup().location]",
             "metadata":{
                "description":"Specifies the Azure location for all resources."
             }
          },
          "keyVaultUri":{
             "type":"string",
             "metadata":{
                "description":"URI of the KeyVault."
             }
          },
          "keyName":{
             "type":"string",
             "metadata":{
                "description":"KeyName."
             },
         "identity": {
            "type": "Object",
            "defaultValue": {
                "userAssignedIdentity": ""
            },
            "metadata": {
                "description": "user-assigned identity."
            }
         }
       },
       "resources":[
          {
             "type":"Microsoft.EventHub/clusters",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('clusterName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Dedicated",
                "capacity":1
             }
          },
          {
             "type":"Microsoft.EventHub/namespaces",
             "apiVersion":"2021-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Standard",
                "tier":"Standard",
                "capacity":1
             },
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[parameters('identity').userAssignedIdentity]": {}
                }
            },
             "properties":{
                "encryption":{
                   "keySource":"Microsoft.KeyVault",
                   "keyVaultProperties":[
                        {
                            "keyName": "[parameters('keyName')]",
                            "keyVaultUri": "[parameters('keyVaultUri')]",
                            "identity": {
                                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
                            }
                        }
                   ]
                }
             }
          }
       ]
    }        
    ```  
1. Cree un archivo de parámetros de plantilla: **CreateEventHubsNamespaceWithUserIdentityAndEncryptionParams.json**.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<EventHubsNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          },
          "keyVaultUri":{
             "value":"https://<KeyVaultName>.vault.azure.net"
          },
          "keyName":{
             "value":"<KeyName>"
          },
          "identity": {
            "value": {
                "userAssignedIdentity": "/subscriptions/<AZURE SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP NAME>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<USER MANAGED IDENTITY NAME>"
            }
         }
       }
    }
    ```

    En el archivo de parámetros, reemplace los marcadores de posición por los valores adecuados.
    
    | Marcador de posición | value | 
    | ----------- | ----- | 
    | `<EventHubsNamespaceName>` | Nombre del espacio de nombres de Event Hubs. | 
    | `<Location>` | Ubicación donde desea crear el espacio de nombres. | 
    | `<KeyVaultName>` | Nombre del almacén de claves. | 
    | `<KeyName>` | Nombre de la clave del almacén de claves. | 
    | `<AZURE SUBSCRIPTION ID>` | Su identificador de suscripción de Azure. |
    | `<RESOURCE GROUP NAME>` | Grupo de recursos de la identidad administrada por el usuario. | 
    | `<USER MANAGED IDENTITY NAME>` | Nombre de la identidad administrada por el usuario. | 

3. Ejecute el siguiente comando de PowerShell para implementar la plantilla de Resource Manager. Antes de ejecutar el comando, reemplace `{MyRG}` por el nombre del grupo de recursos.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name CreateEventHubsNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./ CreateEventHubsNamespaceWithUserIdentityAndEncryption.json -TemplateParameterFile ./ CreateEventHubsNamespaceWithUserIdentityAndEncryptionParams.json        
    ```


## <a name="use-both-user-assigned-and-system-assigned-identities"></a>Uso de identidades asignadas por el usuario y por el sistema
Un espacio de nombres puede tener identidades asignadas por el sistema y por el usuario de manera simultánea. En este caso, la propiedad `type` debe ser `SystemAssigned`, `UserAssigned` como se muestra en el ejemplo siguiente. 

```json
"identity": {
    "type": "SystemAssigned, UserAssigned",
    "userAssignedIdentities": {
        "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<userIdentity1>" : {}
    }
}
```

En este escenario, puede elegir la identidad asignada por el sistema o la asignada por el usuario para cifrar los datos en reposo.  

En la plantilla de Resource Manager, si no especifica un atributo `identity`, se usará la identidad administrada por el sistema. Este es un ejemplo de fragmento de código. 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

Consulte el ejemplo siguiente para usar la identidad administrada por el usuario para el cifrado. Observe que el atributo `identity` se establece en la identidad administrada por el usuario. 

```json
"properties":{
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]",
            "identity": {
                "userAssignedIdentity": "[parameters('identity').userAssignedIdentity]"
            }
         }
      ]
   }
}
```

## <a name="enable-infrastructure-or-double-encryption-of-data"></a>Habilitación del cifrado de infraestructura (o doble) de los datos
Si necesita una mayor garantía de que los datos sean seguros, puede habilitar el cifrado en el nivel de infraestructura, también conocido como cifrado doble. 

Cuando se habilita el cifrado de la infraestructura, los datos de las cuentas del espacio de nombres de Event Hubs se cifran dos veces, una en el nivel de servicio y otra en el de infraestructura, con dos algoritmos de cifrado y dos claves diferentes. Por tanto, el cifrado de infraestructura de los datos de Event Hubs sirve de protección en caso de que uno de los algoritmos de cifrado o las claves puedan estar en peligro.

Puede habilitar el cifrado de infraestructura mediante la actualización de la plantilla de Azure Resource Manager con la propiedad `requireInfrastructureEncryption` en el archivo **CreateEventHubClusterAndNamespace.json** anterior, como se muestra a continuación. 

```json
"properties":{
   "isAutoInflateEnabled":false,
   "maximumThroughputUnits":0,
   "clusterArmId":"[resourceId('Microsoft.EventHub/clusters', parameters('clusterName'))]",
   "encryption":{
      "keySource":"Microsoft.KeyVault",
      "requireInfrastructureEncryption":true,
      "keyVaultProperties":[
         {
            "keyName":"[parameters('keyName')]",
            "keyVaultUri":"[parameters('keyVaultUri')]"
         }
      ]
   }
}
```

## <a name="rotate-revoke-and-cache-encryption-keys"></a>Rotación, revocación y copia en caché de claves de cifrado

### <a name="rotate-your-encryption-keys"></a>Rotación de las claves de cifrado
Puede rotar la clave en el almacén de claves mediante el mecanismo de rotación de los Azure Key Vault. También es posible establecer fechas de activación y expiración para automatizar la rotación de claves. El servicio Event Hubs detectará nuevas versiones de clave y comenzará a usarlas automáticamente.

### <a name="revoke-access-to-keys"></a>Revocación del acceso a las claves
Al revocar el acceso a las claves de cifrado, no se purgan los datos de Event Hubs. Sin embargo, no se podrá acceder a los datos desde el espacio de nombres de Event Hubs. Puede revocar la clave de cifrado mediante la directiva de acceso o eliminando la clave. Obtenga más información sobre las directivas de acceso y la protección del almacén de claves en [Protección del acceso a un almacén de claves](../key-vault/general/security-features.md).

Una vez revocada la clave de cifrado, el servicio Event Hubs en el espacio de nombres cifrado dejará de ser operativo. Si el acceso a la clave está habilitado o si se ha restaurado la clave eliminada, el servicio Event Hubs seleccionará la clave para que pueda acceder a los datos desde el espacio de nombres de Event Hubs cifrado.

### <a name="caching-of-keys"></a>Almacenamiento en caché de las claves
La instancia de Event Hubs (el centro de eventos) sondea sus claves de cifrado enumeradas cada cinco minutos. Luego, las almacena en caché y las usa hasta el siguiente sondeo, que tiene lugar al cabo de cinco minutos. Siempre que haya al menos una clave disponible, se puede acceder al centro de eventos. Si no se puede acceder a ninguna clave de la lista cuando se sondean, dejarán de estar disponibles todos los centros de eventos. 

A continuación tiene más detalles: 

- Cada cinco minutos, el servicio Event Hubs sondea todas las claves administradas por el cliente incluidas en el registro del espacio de nombres:
    - Si se ha rotado una clave, el registro se actualiza con la nueva clave.
    - Si se ha revocado una clave, la clave se quita del registro.
    - Si todas las claves se han revocado, el estado de cifrado del espacio de nombres se establece en **Revocado**. No se podrá acceder a los datos desde el espacio de nombres de Event Hubs.

## <a name="considerations-when-using-geo-disaster-recovery"></a>Consideraciones al usar la recuperación ante desastres geográfica

> [!IMPORTANT]
> Para habilitar la recuperación ante desastres geográfica en un espacio de nombres que use el cifrado de BYOK, el espacio de nombres secundario para el emparejamiento debe tener habilitada en este una identidad administrada asignada por el sistema o por el usuario. 

### <a name="geo-disaster-recovery---encryption-with-system-assigned-identities"></a>Recuperación ante desastres geográfica: cifrado con identidades asignadas por el sistema
Para habilitar el cifrado de la clave administrada por Microsoft con una clave administrada por el cliente, se configura una [directiva de acceso](../key-vault/general/secure-your-key-vault.md) para la identidad administrada asignada por el sistema en la instancia especificada de Azure Key Vault. Esto garantiza el acceso controlado a la instancia de Azure Key Vault desde el espacio de nombres de Azure Event Hubs.

Debido a esto:

- Si la [recuperación ante desastres geográfica](event-hubs-geo-dr.md) ya está habilitada para el espacio de nombres de Event Hubs y desea habilitar la clave administrada por el cliente, siga estos pasos:
    - Interrumpa el emparejamiento.
    - [Configure la directiva de acceso](../key-vault/general/assign-access-policy-portal.md) para la identidad administrada asignada por el sistema de los espacios de nombres principal y secundario en el almacén de claves.
    - Configure el cifrado en el espacio de nombres principal.
    - Vuelva a emparejar los espacios de nombres principal y secundario.
- Si desea habilitar la recuperación ante desastres geográfica en un espacio de nombres de Event Hubs en el que ya está configurada la clave administrada por el cliente, siga estos pasos: 
    - [Configure la directiva de acceso](../key-vault/general/assign-access-policy-portal.md) para la identidad administrada del espacio de nombres secundario en el almacén de claves.
    - Empareje los espacios de nombres principal y secundario.

### <a name="geo-disaster-recovery---encryption-with-user-assigned-identities"></a>Recuperación ante desastres geográfica: cifrado con identidades asignadas por el usuario
Estas son algunas recomendaciones: 

1.  Cree una identidad administrada y asígnele permisos de Key Vault. 
2.  Agregue la identidad como identidad asignada por el usuario y habilite el cifrado con la identidad en ambos espacios de nombres. 
3.  Empareje los espacios de nombres. 

Condiciones para habilitar la recuperación ante desastres geográfica y el cifrado con identidades asignadas por el usuario:

1.  El espacio de nombres secundario ya debe tener el cifrado habilitado con una identidad asignada por el usuario si se va a emparejar con un espacio de nombres principal que tenga el cifrado habilitado. 
2.  No se puede habilitar el cifrado en un espacio de nombres principal ya emparejado, aunque el secundario tenga una identidad asignada por el usuario asociada con él.

## <a name="set-up-diagnostic-logs"></a>Configuración de registros de diagnósticos 
La configuración de los registros de diagnóstico para los espacios de nombres habilitados para BYOK proporciona la información necesaria sobre las operaciones. Estos registros pueden habilitarse y, posteriormente, transmitirse a un centro de eventos, analizarse mediante análisis de registros o transmitirse al almacenamiento para realizar análisis personalizados. Para más información acerca de los registros de diagnóstico, consulte [Información general sobre los registros de diagnóstico de Azure](../azure-monitor/essentials/platform-logs-overview.md). Para acceder al esquema, consulte [Referencia sobre la supervisión de datos de Azure Event Hubs](monitor-event-hubs-reference.md#customer-managed-key-user-logs-schema).

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:
- [Información general de Event Hubs](event-hubs-about.md)
- [Introducción a Azure Key Vault](../key-vault/general/overview.md)