---
title: Cifrado de discos de sistema operativo con una clave administrada por el cliente (CMK) en Red Hat OpenShift en Azure (ARO)
description: Cifrado de discos de sistema operativo con una clave administrada por el cliente (CMK) en Red Hat OpenShift en Azure (ARO)
author: sayjadha
ms.author: suvetriv
ms.service: azure-redhat-openshift
keywords: encryption, byok, aro, deploy, openshift, red hat
ms.topic: how-to
ms.date: 10/18/2021
ms.custom: template-how-to
ms.openlocfilehash: f9b60767f929f8fc9d40836daa6435d0c69d110f
ms.sourcegitcommit: 147910fb817d93e0e53a36bb8d476207a2dd9e5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/18/2021
ms.locfileid: "130134289"
---
# <a name="encrypt-os-disks-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Cifrado de discos de sistema operativo con una clave administrada por el cliente (CMK) en Red Hat OpenShift en Azure (ARO) (versión preliminar)

De forma predeterminada, los discos de sistema operativo de las máquinas virtuales de un clúster de Red Hat OpenShift en Azure se cifran con claves generadas automáticamente administradas por Microsoft Azure. Para mayor seguridad, los clientes pueden cifrar los discos del sistema operativo con claves autoadministradas al implementar un clúster de ARO. Estas características permiten un mayor control mediante el cifrado de datos confidenciales con claves administradas por el cliente.

Los clústeres creados con claves administradas por el cliente tienen una clase de almacenamiento predeterminada habilitada con sus claves. Por lo tanto, estas claves cifran los discos del sistema operativo y los de datos. Las claves administradas por el cliente se almacenan en Azure Key Vault. Para más información sobre el uso de Azure Key Vault para crear y mantener claves, consulte [Cifrado del lado servidor de Azure Disk Storage](../key-vault/general/basic-concepts.md) en la documentación de Microsoft Azure.

> [!IMPORTANT]
> Las características en vista previa de ARO están disponibles como opción de participación y autoservicio. Las características en vista previa se proporcionan "tal cual" y "según disponibilidad", y se excluyen de los contratos de nivel de servicio y la garantía limitada. Las características en vista previa reciben cobertura parcial del soporte al cliente en la medida de lo posible. Por lo tanto, estas características no están diseñadas para usarse en producción.

## <a name="limitation"></a>Limitación
Los clientes son los responsables del mantenimiento de Key Vault y el conjunto de cifrado de disco en Azure. Si no se conservan las claves, se interrumpirán los clústeres de ARO. Las VM dejan de funcionar y, por lo tanto, todo el clúster de ARO deja de funcionar. El equipo de ingeniería de Red Hat OpenShift en Azure no puede acceder a las claves, de modo que no puede realizar una copia de seguridad, replicarlas ni recuperarlas. Para obtener más información sobre el uso de conjuntos de cifrado de disco para administrar las claves de cifrado, consulte [Cifrado del lado servidor de Azure Disk Storage](../virtual-machines/disk-encryption.md) en la documentación de Microsoft Azure.

## <a name="prerequisites"></a>Requisitos previos
* [Comprobación de los permisos](tutorial-create-cluster.md#verify-your-permissions). Debe disponer de los permisos de colaborador y administrador de acceso de usuarios o bien de permisos de propietario.
* Registre los proveedores de recursos si tiene varias suscripciones a Azure. Para obtener más detalles sobre el registro, consulte [Registro de los proveedores de recursos](tutorial-create-cluster.md#register-the-resource-providers).

## <a name="install-the-preview-azure-cli-extension"></a>Instalación de la versión preliminar de la extensión de la CLI de Azure
Instale y use la Interfaz de la línea de comandos (CLI) de Azure para crear una instancia de Key Vault. La CLI de Azure permite la ejecución de comandos mediante un terminal con mensajes de la línea de comandos interactivos o un script.
> [!NOTE]
> La extensión de la CLI solo se necesita para la característica en vista previa.

1. Haga clic en la siguiente dirección URL para descargar el archivo wheel de Python y la extensión de la CLI: [https://aka.ms/az-aroext-latest.whl](https://aka.ms/az-aroext-latest.whl)
1. Ejecute el siguiente comando:
    ```azurecli-interactive
    az extension add --upgrade -s <path to downloaded .whl file>
    ```
1. Compruebe que se está usando la extensión de la CLI:
    ```azurecli-interactive
    az extension list
    [
      {
        "experimental": false,
        "extensionType": "whl",
        "name": "aro",
        "path": "<path may differ depending on system>",
        "preview": true,
        "version": "1.0.1"
      }
    ]
    ```

## <a name="create-a-virtual-network-containing-two-empty-subnets"></a>Creación de una red virtual que contenga dos subredes vacías
Cree una red virtual con dos subredes vacías. Si ya tiene una red virtual adaptada a sus necesidades, puede omitir este paso. Para revisar el procedimiento de creación de una red virtual, consulte [Creación de una red virtual con dos subredes vacías](tutorial-create-cluster.md#create-a-virtual-network-containing-two-empty-subnets).

## <a name="create-an-azure-key-vault-instance"></a>Creación de una instancia de Azure Key Vault
Debe usar una instancia de Azure Key Vault para almacenar las claves. Cree una nueva instancia de Key Vault con la protección de purga habilitada. A continuación, cree una nueva clave Key Vault para almacenar su propia clave personalizada.
1. Establecer permisos de entorno adicionales:
    ```
    export KEYVAULT_NAME=$USER-enckv
    export KEYVAULT_KEY_NAME=$USER-key
    export DISK_ENCRYPTION_SET_NAME=$USER-des
    ```
1. Cree una instancia de Key Vault y una clave en Key Vault:
    ```azurecli-interactive
    az keyvault create -n $KEYVAULT_NAME \
                   -g $RESOURCEGROUP \
                   -l $LOCATION \
                   --enable-purge-protection true \
                   --enable-soft-delete true

    az keyvault key create --vault-name $KEYVAULT_NAME \
                           -n $KEYVAULT_KEY_NAME \
                           --protection software

    KEYVAULT_ID=$(az keyvault show --name $KEYVAULT_NAME --query "[id]" -o tsv)

    KEYVAULT_KEY_URL=$(az keyvault key show --vault-name $KEYVAULT_NAME \
                                            --name $KEYVAULT_KEY_NAME \
                                            --query "[key.kid]" -o tsv)
    ```

## <a name="create-an-azure-disk-encryption-set"></a>Creación de un conjunto de cifrado de disco de Azure
El conjunto de cifrado de disco de Azure se usa como punto de referencia para los discos en clústeres de ARO. Está conectado a la instancia de Azure Key Vault que creó en el paso anterior y extrae las claves administradas por el cliente de esa ubicación.
```azurecli-interactive
az disk-encryption-set create -n $DISK_ENCRYPTION_SET_NAME \
                              -l $LOCATION \
                              -g $RESOURCEGROUP \
                              --source-vault $KEYVAULT_ID \
                              --key-url $KEYVAULT_KEY_URL

DES_ID=$(az disk-encryption-set show -n $DISK_ENCRYPTION_SET_NAME -g $RESOURCEGROUP --query 'id' -o tsv)

DES_IDENTITY=$(az disk-encryption-set show -n $DISK_ENCRYPTION_SET_NAME \
                                           -g $RESOURCEGROUP \
                                           --query "[identity.principalId]" \
                                           -o tsv)
```

## <a name="grant-permissions-for-the-disk-encryption-set-to-access-the-key-vault"></a>Concesión de permisos para que el conjunto de cifrado de disco acceda a Key Vault
Use el conjunto de cifrado de disco que creó en el paso anterior y conceda permiso para que dicho conjunto pueda acceder a Azure Key Vault y usarlo.
```azurecli-interactive
az keyvault set-policy -n $KEYVAULT_NAME \
                       -g $RESOURCEGROUP \
                       --object-id $DES_IDENTITY \
                       --key-permissions wrapkey unwrapkey get
```

## <a name="create-an-aro-cluster"></a>Creación de un clúster de ARO
Cree un clúster de ARO para usar las claves administradas por el cliente.
```azurecli-interactive
az aro create --resource-group $RESOURCEGROUP \
              --name $CLUSTER  \
              --vnet aro-vnet  \
              --master-subnet master-subnet \
              --worker-subnet worker-subnet \
              --disk-encryption-set $DES_ID
```
Después de crear el clúster de ARO, todas las VM se cifran con las claves de cifrado administradas por el cliente.

Para comprobar que ha configurado las claves correctamente, ejecute los siguientes comandos:
1. Obtenga el nombre del grupo de recursos de clúster donde se encuentran las VM, los discos, etc.:
    ```azurecli-interactive
    CLUSTERRESOURCEGROUP=$(az aro show --resource-group $RESOURCEGROUP --name $CLUSTER --query 'clusterProfile.resourceGroupId' -o tsv | cut -d '/' -f 5)
    ```
2. Compruebe que los discos tienen asociado el conjunto de cifrado de disco correcto:
    ```azurecli-interactive
    az disk list -g $CLUSTERRESOURCEGROUP --query '[].encryption'
    ```
    El campo `diskEncryptionSetId` de la salida debe apuntar al conjunto de cifrado de disco que especificó al crear el clúster de ARO.
