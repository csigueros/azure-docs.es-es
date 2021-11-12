---
title: Claves administradas por el cliente de Azure Managed Instance for Apache Cassandra
description: Claves administradas por el cliente
author: TheovanKraay
ms.author: thvankra
ms.service: managed-instance-apache-cassandra
ms.topic: conceptual
ms.date: 10/29/2021
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 4cf92148accf1d07aaafc0a1640de36e2884a9c5
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893450"
---
# <a name="customer-managed-keys---overview"></a>Información general de las claves administradas por el cliente

Azure Managed Instance for Apache Cassandra proporciona la funcionalidad de cifrar datos en el disco mediante su propia clave. En este artículo se describe cómo implementar claves administradas por el cliente con Azure Key Vault.

## <a name="prerequisites"></a>Requisitos previos

- Configure un secreto mediante Azure Key Vault. Obtenga más información sobre Azure Key Vault [aquí](/azure/key-vault/secrets/about-secrets).
- Implemente una red virtual en el grupo de recursos y aplique el rol de colaborador de red con la entidad de servicio de Azure Cosmos DB como miembro. Consulte [Creación de un clúster de Azure Managed Instance for Apache Cassandra mediante la CLI de Azure](create-cluster-cli.md) para obtener más información. 

> [!IMPORTANT]
> Este artículo requiere la CLI de Azure 2.30.0 o una versión posterior. Si usa Azure Cloud Shell, la versión más reciente ya está instalada.

## <a name="create-a-cluster-with-system-assigned-identity"></a><a id="create-cluster"></a>Creación de un clúster con una identidad asignada por el sistema

   > [!NOTE]
   > Como se indicó en los requisitos previos, para evitar errores de implementación, asegúrese de que ha aplicado el rol adecuado a la red virtual antes de intentar implementar un clúster de instancia administrada:
   > ```azurecli-interactive  
   >     az role assignment create \
   >     --assignee a232010e-820c-4083-83bb-3ace5fc29d0b \
   >     --role 4d97b98b-1d4f-4787-a291-c67834d212e7 \
   >     --scope /subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>
   > ```

1. Cree un clúster en el que especifique el tipo de identidad como SystemAssigned y reemplace `<subscriptionID>`, `<resourceGroupName>`, `<vnetName>` y `<subnetName>` por los valores adecuados:

    ```azurecli-interactive    
    subnet="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
    cluster="thvankra-cmk-test-wcus"
    group="thvankra-nova-cmk-test"
    region="westcentralus"
    password="PlaceholderPassword"
    
    az managed-cassandra cluster create \
        --identity-type SystemAssigned \
        --resource-group $group \
        --location $region \
        --cluster-name $cluster \
        --delegated-management-subnet-id $subnet \
        --initial-cassandra-admin-password $password
    ```

1. Obtención de la información de identidad del clúster creado

    ```azurecli-interactive
    az managed-cassandra cluster show -c $cluster -g $group
    ```

    La salida incluirá una sección de identidad como la siguiente. Copie `principalId` para su uso posterior:

    ```shell
      "identity": {
        "principalId": "1aa51c7f-196a-4013-a656-1ccabfdc54e0",
        "tenantId": "72f988bf-86f1-41af-91ab-2d7cd011db47",
        "type": "SystemAssigned"
      }
    ```
 
1. En Azure Key Vault, cree una directiva de acceso a las claves:

   :::image type="content" source="./media/cmk/key-vault-access-policy-1.png" alt-text="Directiva de acceso 1 de Key Vault" lightbox="./media/cmk/key-vault-access-policy-1.png" border="true":::

1. Asigne los permisos de clave `get`, `wrap` y `unwrap` del almacén de claves al valor `principalId` del clúster recuperado anteriormente. En el portal, también puede buscar el identificador de la entidad de seguridad del clúster por el nombre del clúster:
 

   :::image type="content" source="./media/cmk/key-vault-access-policy-2.png" alt-text="Directiva de acceso 2 de Key Vault" lightbox="./media/cmk/key-vault-access-policy-2.png" border="true":::

   > [!WARNING]
   > Asegúrese de que el almacén de claves tiene habilitada la protección de purga. Se producirá un error en las implementaciones del centro de datos si no la tienen. 

1. Después de hacer clic en `add` para agregar la directiva de acceso, asegúrese de guardarla:

   :::image type="content" source="./media/cmk/save.png" alt-text="Guarde la directiva de acceso." lightbox="./media/cmk/key-vault-access-policy-2.png" border="true":::

1. Para obtener el identificador de clave, seleccione la clave:

   :::image type="content" source="./media/cmk/select-key.png" alt-text="Seleccione la clave." lightbox="./media/cmk/key-identifier-1.png" border="true":::

1. Haga clic en la versión actual:

   :::image type="content" source="./media/cmk/current-version.png" alt-text="Seleccione la versión actual." lightbox="./media/cmk/key-identifier-1.png" border="true":::

1. Guarde el identificador de clave para su uso posterior:

   :::image type="content" source="./media/cmk/key-identifier-2.png" alt-text="Paso 2 del identificador de clave." lightbox="./media/cmk/key-identifier-1.png" border="true":::


1. Cree el centro de datos. Para ello, reemplace `<key identifier>` por la misma clave (el URI que copió en el paso anterior) para el cifrado del disco administrado (managed-disk-customer-key-uri) y del almacenamiento de copia de seguridad (backup-storage-customer-key-uri), como se muestra a continuación (use el mismo valor para `subnet` que usó anteriormente): 

    ```azurecli-interactive
    managedDiskKeyUri = "<key identifier>"
    backupStorageKeyUri = "<key identifier>"
    group="thvankra-nova-cmk-test"
    region="westcentralus"
    cluster="thvankra-cmk-test-2"
    dc="dc1"
    nodecount=3
    subnet="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>/providers/Microsoft.Network/virtualNetworks/<vnetName>/subnets/<subnetName>"
        
    az managed-cassandra datacenter create \
        --resource-group $group \
        --cluster-name $cluster \
        --data-center-name $dc \
        --managed-disk-customer-key-uri $managedDiskKeyUri \
        --backup-storage-customer-key-uri $backupStorageKeyUri \
        --node-count $nodecount \
        --delegated-subnet-id $subnet \
        --data-center-location $region \
        --sku Standard_DS14_v2
    ```

1. Se puede asignar una identidad a un clúster existente sin información de identidad, como se muestra a continuación:

    ```azurecli-interactive
    az managed-cassandra cluster update --identity-type SystemAssigned -g $group -c $cluster
    ```

## <a name="rotating-the-key"></a><a id="update-cluster"></a>Rotación de la clave

1. A continuación se muestra el comando para actualizar la clave:

    ```azurecli-interactive
    managedDiskKeyUri = "<key identifier>"
    backupStorageKeyUri = "<key identifier>"
    
    az managed-cassandra datacenter update \
        --resource-group $group \
        --cluster-name $cluster \ 
        --data-center-name $dc \
        --managed-disk-customer-key-uri $managedDiskKeyUri \
        --backup-storage-customer-key-uri $backupStorageKeyUri
    ```