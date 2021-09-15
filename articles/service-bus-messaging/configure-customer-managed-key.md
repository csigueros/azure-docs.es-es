---
title: Configuración de su propia clave para cifrar datos en reposo de Azure Service Bus
description: En este artículo se proporciona información sobre cómo configurar su propia clave para cifrar datos en reposo de Azure Service Bus.
ms.topic: conceptual
ms.date: 02/10/2021
ms.openlocfilehash: 58ce3559a9524abdd3391f9d663651c58a20144d
ms.sourcegitcommit: d43193fce3838215b19a54e06a4c0db3eda65d45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/20/2021
ms.locfileid: "122514763"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest"></a>Configuración de las claves administradas por el cliente para el cifrado de datos en reposo de Azure Service Bus
Azure Service Bus Premium proporciona cifrado de datos en reposo con Azure Storage Service Encryption (Azure SSE). Service Bus Premium usa Azure Storage para almacenar los datos. Todos los datos almacenados con Azure Storage se cifran con claves administradas por Microsoft. Si usa su propia clave (también conocida como Bring Your Own Key [BYOK] o clave administrada por el cliente), los datos se cifran mediante la clave administrada por Microsoft, pero además la clave administrada por Microsoft se cifrará mediante la clave administrada por el cliente. Esta característica permite crear, rotar, deshabilitar y revocar el acceso a las claves administradas por el cliente que se usan para cifrar claves administradas por Microsoft. La habilitación de la característica BYOK es un proceso que solo hay que configurar una vez en el espacio de nombres.

Hay algunas advertencias para la clave administrada por el cliente para el cifrado en el lado del servicio. 
- Esta característica se admite en el nivel [Premium de Azure Service Bus](service-bus-premium-messaging.md). No se puede habilitar para los espacios de nombres estándar de Service Bus.
- El cifrado solo se puede habilitar para espacios de nombres nuevos o vacíos. Si el espacio de nombres contiene colas o temas, se producirá un error en la operación de cifrado.

Puede usar Azure Key Vault para administrar las claves y auditar su uso. Puede crear sus propias claves y almacenarlas en un almacén de claves, o puede usar las API de Azure Key Vault para generarlas. Para obtener más información sobre Azure Key Vault, consulte [¿Qué es Azure Key Vault?](../key-vault/general/overview.md)

## <a name="enable-customer-managed-keys-azure-portal"></a>Habilitación de las claves administradas por el cliente (Azure Portal)
Para habilitar claves administradas del cliente en Azure Portal, siga estos pasos:

1. Vaya al espacio de nombres Premium de Service Bus.
2. En la página **Configuración** del espacio de nombres de Service Bus, seleccione **Cifrado**.
3. Seleccione **Cifrado de claves en reposo que administra el cliente** como se muestra en la siguiente imagen.

    ![Habilitación de clave administrada por el cliente](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Configuración de un almacén de claves con claves

Después de habilitar las claves administradas por el cliente, debe asociar la clave administrada por el cliente con su espacio de nombres de Azure Service Bus. Service Bus solo admite Azure Key Vault. Si habilita la opción **Encryption with customer-managed key** (Cifrado con clave administrada por el cliente) en la sección anterior, debe importar la clave en Azure Key Vault. Recuerde que las claves deben tener habilitadas las opciones **Eliminación temporal** y **No purgar**. Estas opciones se pueden configurar mediante [PowerShell](../key-vault/general/key-vault-recovery.md) o la [CLI](../key-vault/general/key-vault-recovery.md).

1. Para crear un nuevo almacén de claves, siga el [inicio rápido](../key-vault/general/overview.md) de Azure Key Vault. Para más información sobre cómo importar claves existentes, consulte [Información acerca de claves, secretos y certificados](../key-vault/general/about-keys-secrets-certificates.md).

    > [!IMPORTANT]
    > El uso de claves administradas por el cliente con Azure Service Bus requiere que el almacén de claves tenga configuradas dos propiedades obligatorias. Son las siguientes:  **Eliminación temporal** y **No purgar**. Estas propiedades están habilitadas de manera predeterminada cuando crea un nuevo almacén de claves en Azure Portal. Sin embargo, si tiene que habilitar estas propiedades en un almacén de claves existente, deberá usar PowerShell o la CLI de Azure.
1. Para activar la eliminación temporal y la protección de purgas al crear un almacén, use el comando [az keyvault create](/cli/azure/keyvault#az_keyvault_create).

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Para agregar la protección de purgas a un almacén existente (que ya tenga habilitada la eliminación temporal), use el comando [az keyvault update](/cli/azure/keyvault#az_keyvault_update).

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Siga estos pasos para crear claves:
    1. Para crear una nueva clave, seleccione **Generar/Importar** en el menú **Claves** en **Configuración**.
        
        ![Seleccione el botón Generate/Import (Generar/importar).](./media/configure-customer-managed-key/select-generate-import.png)

    1. Establezca **Opciones** en **Generar** y asigne un nombre a la clave.

        ![Crear una clave](./media/configure-customer-managed-key/create-key.png) 

    1. Ahora puede seleccionar esta clave para asociarla con el espacio de nombres de Service Bus para el cifrado en la lista desplegable. 

        ![Selección de clave del almacén de claves](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Para lograr redundancia, puede agregar hasta 3 claves. En caso de que una de las claves haya expirado o no sea accesible, se usarán las demás claves para el cifrado.
        
    1. Rellene los detalles de la clave y haga clic en **Seleccionar**. Esto habilitará el cifrado de la clave administrada por Microsoft con su clave (clave administrada por el cliente). 


    > [!IMPORTANT]
    > Si desea usar la clave administrada por el cliente junto con la recuperación ante desastres geográfica, revise esta sección. 
    >
    > Para habilitar el cifrado de la clave administrada por Microsoft con una clave administrada por el cliente, se configura una [directiva de acceso](../key-vault/general/security-features.md) para la identidad administrada de Service Bus en la instancia especificada de Azure Key Vault. Esto garantiza el acceso controlado al almacén de claves de Azure desde el espacio de nombres de Azure Service Bus.
    >
    > Debido a esto:
    > 
    >   * Si la [recuperación ante desastres geográfica](service-bus-geo-dr.md) ya está habilitada para el espacio de nombres de Service Bus y desea habilitar la clave administrada por el cliente, siga estos pasos: 
    >     * Interrumpa el emparejamiento
    >     * [Configure la directiva de acceso](../key-vault/general/assign-access-policy-portal.md) para la identidad administrada de los espacios de nombres principal y secundario en el almacén de claves.
    >     * Configure el cifrado en el espacio de nombres principal.
    >     * Vuelva a emparejar los espacios de nombres principal y secundario.
    > 
    >   * Si desea habilitar la recuperación ante desastres geográfica en un espacio de nombres de Service Bus en el que la clave administrada por el cliente ya está configurada, haga lo siguiente:
    >     * [Configure la directiva de acceso](../key-vault/general/assign-access-policy-portal.md) para la identidad administrada del espacio de nombres secundario en el almacén de claves.
    >     * Empareje los espacios de nombres principal y secundario.

## <a name="managed-identities"></a>Identidades administradas
Hay dos tipos de identidades administradas que puede asignar a un espacio de nombres de Service Bus.

- **Asignadas por el sistema**: puede habilitar una identidad administrada directamente en un espacio de nombres de Service Bus. Cuando se habilita una identidad administrada asignada por el sistema, se crea una identidad en Azure AD vinculada al ciclo de vida de ese espacio de nombres de Service Bus. Por tanto, cuando se elimina el espacio de nombres, Azure elimina automáticamente la identidad. Por naturaleza, solo ese recurso (espacio de nombres) de Azure puede usar esta identidad para solicitar tokens de Azure AD.
- **Asignadas por el usuario**: también es posible crear una identidad administrada como recurso independiente de Azure, que se denomina identidad asignada por el usuario. Puede crear una identidad administrada asignada por el usuario y asignársela a uno o varios espacios de nombres de Service Bus. En el caso de las identidades administradas asignadas por el usuario, la identidad se administra independientemente de los recursos que la utilicen. No están asociadas al ciclo de vida del espacio de nombres. Cuando no se necesite, la identidad asignada por el usuario se puede eliminar explícitamente.     

    Para más información, consulte [¿Qué es Managed Identities for Azure Resources?](../active-directory/managed-identities-azure-resources/overview.md)

## <a name="encrypt-using-system-assigned-identities-template"></a>Cifrado mediante identidades asignadas por el sistema (plantilla)
En esta sección se muestra cómo realizar las siguientes tareas: 

1. Cree un espacio de nombres de Service Bus **premium** con una **identidad de servicio administrada**.
2. Cree un **almacén de claves** y conceda a la identidad de servicio acceso a él. 
3. Actualice el espacio de nombres de Service Bus con la información del almacén de claves (clave/valor). 

### <a name="create-a-premium-service-bus-namespace-with-managed-service-identity"></a>Creación de un espacio de nombres de Service Bus con una identidad de servicio administrada
En esta sección se muestra cómo crear un espacio de nombres de Azure Service Bus con la identidad de servicio administrada mediante una plantilla de Azure Resource Manager y PowerShell. 

1. Cree una plantilla de Azure Resource Manager para crear un espacio de nombres de nivel premium de Service Bus con una identidad de servicio administrada. Asigne el nombre al archivo: **CreateServiceBusPremiumNamespace.json**: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "type":"string",
             "metadata":{
                "description":"Name for the Namespace."
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
             "properties":{
    
             }
          }
       ],
       "outputs":{
          "ServiceBusNamespaceId":{
             "type":"string",
             "value":"[resourceId('Microsoft.ServiceBus/namespaces',parameters('namespaceName'))]"
          }
       }
    }
    ```
2. Cree un archivo de parámetros de plantilla llamado: **CreateServiceBusPremiumNamespaceParams.json**. 

    > [!NOTE]
    > Reemplace los siguientes valores: 
    > - `<ServiceBusNamespaceName>`: nombre del espacio de nombres de Service Bus.
    > - `<Location>`: ubicación del espacio de nombres de Service Bus.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
          },
          "location":{
             "value":"<Location>"
          }
       }
    }
    ```
3. Ejecute el siguiente comando de PowerShell para implementar la plantilla y crear un espacio de nombres de Service Bus. A continuación, recupere el identificador del espacio de nombres de Service Bus para usarlo más adelante. Antes de ejecutar el comando, reemplace `{MyRG}` por el nombre del grupo de recursos.  

    ```powershell
    $outputs = New-AzResourceGroupDeployment -Name CreateServiceBusPremiumNamespace -ResourceGroupName {MyRG} -TemplateFile ./CreateServiceBusPremiumNamespace.json -TemplateParameterFile ./CreateServiceBusPremiumNamespaceParams.json
    
    $ServiceBusNamespaceId = $outputs.Outputs["serviceBusNamespaceId"].value
    ```
 
### <a name="grant-service-bus-namespace-identity-access-to-key-vault"></a>Concesión de acceso al almacén de claves a la identidad del espacio de nombres de Service Bus

1. Ejecute el siguiente comando para crear un almacén de claves con las características **protección de purga** y **eliminación temporal** habilitadas. 

    ```powershell
    New-AzureRmKeyVault -Name "{keyVaultName}" -ResourceGroupName {RGName}  -Location "{location}" -EnableSoftDelete -EnablePurgeProtection    
    ```
    
    O BIEN
    
    Ejecute el siguiente comando para actualizar un **almacén de claves existente**. Antes de ejecutar el comando, especifique valores para los nombres de grupo de recursos y almacén de claves. 
    
    ```powershell
    ($updatedKeyVault = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -ResourceGroupName {RGName} -VaultName {keyVaultName}).ResourceId).Properties| Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"-Force | Add-Member -MemberType "NoteProperty" -Name "enablePurgeProtection" -Value "true" -Force
    ``` 
2. Establezca la directiva de acceso del almacén de claves para que la identidad administrada del espacio de nombres de Service Bus pueda acceder al valor de la clave del almacén de claves. Use el identificador del espacio de nombres de Service Bus de la sección anterior. 

    ```powershell
    $identity = (Get-AzureRmResource -ResourceId $ServiceBusNamespaceId -ExpandProperties).Identity
    
    Set-AzureRmKeyVaultAccessPolicy -VaultName {keyVaultName} -ResourceGroupName {RGName} -ObjectId $identity.PrincipalId -PermissionsToKeys get,wrapKey,unwrapKey,list
    ```

### <a name="encrypt-data-in-service-bus-namespace-with-customer-managed-key-from-key-vault"></a>Cifrado de datos en el espacio de nombres de Service Bus con la clave administrada por el cliente desde el almacén de claves 
Hasta el momento ha realizado los pasos siguientes: 

1. Ha creado un espacio de nombres premium con una identidad administrada.
2. Cree un almacén de claves y conceda a la identidad administrada acceso al almacén de claves. 

En este paso, actualizará el espacio de nombres de Service Bus con la información del almacén de claves. 

1. Cree un archivo JSON denominado **UpdateServiceBusNamespaceWithEncryption.json** con el siguiente contenido: 

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2018-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "identity":{
                "type":"SystemAssigned"
             },
             "sku":{
                "name":"Premium",
                "tier":"Premium",
                "capacity":1
             },
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
          }
       ]
    }
    ``` 

2. Cree un archivo de parámetros de plantilla: **UpdateServiceBusNamespaceWithEncryptionParams.json**.

    > [!NOTE]
    > Reemplace los siguientes valores: 
    > - `<ServiceBusNamespaceName>`: nombre del espacio de nombres de Service Bus.
    > - `<Location>`: ubicación del espacio de nombres de Service Bus.
    > - `<KeyVaultName>`: nombre del almacén de claves.
    > - `<KeyName>`: nombre de la clave del almacén de claves.  

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    New-AzResourceGroupDeployment -Name UpdateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./UpdateServiceBusNamespaceWithEncryption.json -TemplateParameterFile ./UpdateServiceBusNamespaceWithEncryptionParams.json
    ```    

## <a name="encrypt-using-user-assigned-identities-template"></a>Cifrado mediante identidades asignadas por el usuario (plantilla)

1. Cree una **identidad asignada por el usuario**.
1. Cree un **almacén de claves** y conceda acceso a este a la identidad asignada por el usuario mediante directivas de acceso.
1. Cree un espacio de nombres de Service Bus **prémium** con la identidad administrada asignada por el usuario y la información del almacén de claves.

### <a name="create-a-user-assigned-identity"></a>Creación de una identidad asignada por el usuario
Siga las instrucciones del artículo [Crear una identidad administrada asignada por el usuario](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) para crear una identidad asignada por el usuario. También se puede crear una identidad asignada por el usuario mediante la [CLI](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md), [PowerShell](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md), la [plantilla de Azure Resource Manager](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) y [REST](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md). 

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
    > Puede agregar hasta **3** claves, pero la identidad del usuario usada para el cifrado debe ser la misma para todas las claves. Actualmente, solo se admite una identidad de cifrado única.

### <a name="create-a-premium-service-bus-namespace-with-user-identity-and-key-vault-information"></a>Creación de un espacio de nombres de Service Bus prémium con la identidad del usuario y la información del almacén de claves
En esta sección se proporciona un ejemplo que muestra cómo realizar las siguientes tareas mediante una plantilla de Azure Resource Manager. 

- Asigne una identidad administrada por el usuario a un espacio de nombres de Service Bus.

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
   

1. Cree un archivo JSON denominado **CreateServiceBusNamespaceWithUserIdentityAndEncryption.json** con el siguiente contenido:

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
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
             "type":"Microsoft.ServiceBus/namespaces",
             "apiVersion":"2021-01-01-preview",
             "name":"[parameters('namespaceName')]",
             "location":"[parameters('location')]",
             "sku":{
                "name":"Premium",
                "tier":"Premium",
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
1. Cree un archivo de parámetros de plantilla: **CreateServiceBusNamespaceWithUserIdentityAndEncryptionParams.json**.

    ```json
    {
       "$schema":"https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion":"1.0.0.0",
       "parameters":{
          "namespaceName":{
             "value":"<ServiceBusNamespaceName>"
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
    | `<ServiceBusNamespaceName>` | Nombre del espacio de nombres de Service Bus. | 
    | `<Location>` | Ubicación donde desea crear el espacio de nombres. | 
    | `<KeyVaultName>` | Nombre del almacén de claves. | 
    | `<KeyName>` | Nombre de la clave del almacén de claves. | 
    | `<AZURE SUBSCRIPTION ID>` | Su identificador de suscripción de Azure. |
    | `<RESOURCE GROUP NAME>` | Grupo de recursos de la identidad administrada por el usuario. | 
    | `<USER MANAGED IDENTITY NAME>` | Nombre de la identidad administrada por el usuario. | 

3. Ejecute el siguiente comando de PowerShell para implementar la plantilla de Resource Manager. Antes de ejecutar el comando, reemplace `{MyRG}` por el nombre del grupo de recursos.

    ```azurepowershell-interactive
    New-AzResourceGroupDeployment -Name CreateServiceBusNamespaceWithEncryption -ResourceGroupName {MyRG} -TemplateFile ./ CreateServiceBusNamespaceWithUserIdentityAndEncryption.json -TemplateParameterFile ./ CreateServiceBusNamespaceWithUserIdentityAndEncryptionParams.json        
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

En este escenario, puede elegir la identidad asignada por el sistema o por el usuario para cifrar los datos en reposo.  

En la plantilla de Resource Manager, si no especifica un atributo `identity`, se usará la identidad administrada por el sistema. Este es un ejemplo de fragmento de código: 

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

## <a name="enable-infrastructure-double-encryption-of-data"></a>Habilitación del cifrado (doble) de la infraestructura de los datos
Si necesita una mayor garantía de que los datos están protegidos, puede habilitar el cifrado de nivel de infraestructura, también conocido como cifrado doble. 

Cuando se habilita el cifrado de infraestructura, los datos de Azure Service Bus se cifran dos veces, una vez en el nivel de servicio y otra en el nivel de infraestructura, con dos algoritmos de cifrado y dos claves diferentes. Por tanto, el cifrado de infraestructura de los datos de Azure Service Bus sirve de protección en caso de que uno de los algoritmos de cifrado o las claves puedan estar en peligro.

Puede habilitar el cifrado de infraestructura mediante la actualización de la plantilla de Azure Resource Manager con la propiedad `requireInfrastructureEncryption` en el anterior archivo **UpdateServiceBusNamespaceWithEncryption.json**, como se muestra a continuación. 

```json
"properties":{
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

## <a name="rotate-revoke-and-cache-keys"></a>Rotación, revocación y almacenamiento en caché de las claves

### <a name="rotate-your-encryption-keys"></a>Rotación de las claves de cifrado

Puede rotar la clave en el almacén de claves mediante el mecanismo de rotación de los Azure Key Vault. También es posible establecer fechas de activación y expiración para automatizar la rotación de claves. El servicio Service Bus detectará nuevas versiones de clave y comenzará a usarlas automáticamente.

### <a name="revoke-access-to-keys"></a>Revocación del acceso a las claves

Al revocar el acceso a las claves de cifrado, no se purgan los datos de Service Bus. Sin embargo, no se podrá acceder a los datos desde el espacio de nombres de Service Bus. Puede revocar la clave de cifrado mediante la directiva de acceso o eliminando la clave. Obtenga más información sobre las directivas de acceso y la protección del almacén de claves en [Protección del acceso a un almacén de claves](../key-vault/general/secure-your-key-vault.md).

Una vez revocada la clave de cifrado, el servicio Service Bus en el espacio de nombres cifrado dejará de ser operativo. Si el acceso a la clave está habilitado o si se ha restaurado la clave eliminada, el servicio Service Bus seleccionará la clave para que pueda acceder a los datos desde el espacio de nombres de Service Bus cifrado.

### <a name="caching-of-keys"></a>Almacenamiento en caché de las claves
La instancia de Service Bus sondea sus claves de cifrado enumeradas cada cinco minutos. Luego, las almacena en caché y las usa hasta el siguiente sondeo, que tiene lugar al cabo de cinco minutos. Siempre que haya al menos una clave disponible, se puede acceder a las colas y los temas. Si no se puede acceder a todas las claves enumeradas cuando se sondean, todas las colas y temas dejarán de estar disponibles. 

A continuación tiene más detalles: 

- Cada cinco minutos, el servicio Service Bus sondea todas las claves administradas por el cliente que se enumeran en el registro del espacio de nombres:
    - Si se ha rotado una clave, el registro se actualiza con la nueva clave.
    - Si se ha revocado una clave, la clave se quita del registro.
    - Si todas las claves se han revocado, el estado de cifrado del espacio de nombres se establece en **Revocado**. No se podrá acceder a los datos desde el espacio de nombres de Service Bus. 

## <a name="considerations-when-using-geo-disaster-recovery"></a>Consideraciones al usar la recuperación ante desastres geográfica

### <a name="geo-disaster-recovery---encryption-with-system-assigned-identities"></a>Recuperación ante desastres geográfica: cifrado con identidades asignadas por el sistema
Para habilitar el cifrado de la clave administrada por Microsoft con una clave administrada por el cliente, se configura una [directiva de acceso](../key-vault/general/secure-your-key-vault.md) para la identidad administrada asignada por el sistema en la instancia especificada de Azure Key Vault. Esto garantiza el acceso controlado al almacén de claves de Azure desde el espacio de nombres de Azure Service Bus.

Debido a esto:

- Si la [recuperación ante desastres geográfica](service-bus-geo-dr.md) ya está habilitada para el espacio de nombres de Service Bus y desea habilitar la clave administrada por el cliente, siga estos pasos:
    - Interrumpa el emparejamiento.
    - [Configure la directiva de acceso](../key-vault/general/assign-access-policy-portal.md) para la identidad administrada asignada por el sistema de los espacios de nombres principal y secundario en el almacén de claves.
    - Configure el cifrado en el espacio de nombres principal.
    - Vuelva a emparejar los espacios de nombres principal y secundario.
- Si desea habilitar la recuperación ante desastres geográfica en un espacio de nombres de Service Bus en el que la clave administrada por el cliente ya está configurada, siga estos pasos: 
    - [Configure la directiva de acceso](../key-vault/general/assign-access-policy-portal.md) para la identidad administrada del espacio de nombres secundario en el almacén de claves.
    - Empareje los espacios de nombres principal y secundario.

### <a name="geo-disaster-recovery---encryption-with-user-assigned-identities"></a>Recuperación ante desastres geográfica: cifrado con identidades asignadas por el usuario
Estas son algunas recomendaciones: 

1.  Cree una identidad administrada y asígnele permisos de Key Vault a la identidad administrada. 
2.  Agregue la identidad como identidad asignada por el usuario y habilite el cifrado con la identidad en ambos espacios de nombres. 
3.  Emparejamiento de los espacios de nombres 

Condiciones para habilitar la recuperación ante desastres geográfica y el cifrado de identidades asignadas por el usuario:

1.  El espacio de nombres secundario ya debe tener el cifrado habilitado con una identidad asignada por el usuario si se va a emparejar con un espacio de nombres principal que tenga el cifrado habilitado. 
2.  No se puede habilitar el cifrado en un espacio de nombres principal ya emparejado, aunque el secundario tenga una identidad asignada por el usuario asociada con el espacio de nombres.
    

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:
- [Información general de Service Bus](service-bus-messaging-overview.md)
- [Introducción a Azure Key Vault](../key-vault/general/overview.md)
