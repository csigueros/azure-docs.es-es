---
title: Aportación de una identidad de acceso al proveedor de Azure Key Vault para el controlador CSI del almacén de secretos para los secretos de Azure Kubernetes Service (AKS)
description: Obtenga información sobre los distintos métodos que se pueden usar para permitir que el proveedor de Azure Key Vault para el controlador CSI del almacén de secretos se integre con Azure Key Vault (AKV).
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: article
ms.date: 10/13/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: e9e0741cd9f1f90efc4184891401d1d05fc5b5b2
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131511338"
---
# <a name="provide-an-identity-to-access-azure-key-vault-provider-for-secrets-store-csi-driver"></a>Aportación de una identidad para acceder al proveedor de Azure Key Vault para el controlador CSI del almacén de secretos

El controlador CSI del almacén de secretos en Azure Kubernetes Service (AKS) permite varios métodos de acceso basado en identidad a Azure Key Vault (AKV). En este artículo se describen estos métodos y cómo usarlos para acceder a una instancia de AKV y a su contenido desde el clúster. Para obtener más información, vea el artículo [Uso del controlador CSI del almacén de secretos][csi-secrets-store-driver].

## <a name="use-pod-identity"></a>Uso de una identidad de pod

Las identidades administradas del pod de Azure Active Directory usan primitivas de Kubernetes para asociar identidades administradas para recursos de Azure e identidades en Azure Active Directory (AAD) con pods. Estas identidades se pueden usar para conceder acceso al proveedor de secretos de Azure Key Vault para el controlador CSI del almacén de secretos.

### <a name="prerequisites"></a>Prerrequisitos

- Asegúrese de que el [complemento de identidad de pod de AAD][aad-pod-identity] está habilitado en el clúster.
- Debe usar un clúster basado en Linux.

### <a name="usage"></a>Uso

1. Siga los pasos descritos en el artículo [Uso de identidades administradas del pod de AAD][aad-pod-identity-create] para crear una identidad de clúster, asignarle permisos y crear una identidad de pod. Tome nota de los elementos `clientId` y `name` de la identidad recién creada.

2. Asigne permisos a la nueva identidad para que pueda leer la instancia de AKV y ver su contenido mediante la ejecución del siguiente código:

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <pod-identity-client-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <pod-identity-client-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <pod-identity-client-id>
```

3. Cree un objeto SecretProviderClass con el siguiente código YAML; para ello, rellene los valores de `aadpodidbinding`, `tenantId` y los objetos que se recuperarán de la instancia de AKV:

```yml
# This is a SecretProviderClass example using aad-pod-identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-podid
spec:
  provider: azure
  parameters:
    usePodIdentity: "true"               # Set to true for using aad-pod-identity to access keyvault
    keyvaultName: <key-vault-name>       # Set to the name of your Azure Key Vault instance
    cloudName: ""                        # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret             # object types: secret, key or cert
          objectVersion: ""              # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-Id>                # The tenant ID of the Azure Key Vault instance
```

4. Aplique el objeto SecretProviderClass al clúster:

```bash
kubectl apply -f secretproviderclass.yaml
```

5. Cree un pod con el siguiente código YAML, en el que debe rellenar el nombre de la identidad:

```yml
# This is a sample pod definition for using SecretProviderClass and aad-pod-identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-podid
  labels:
    aadpodidbinding: <name>                   # Set the label value to the name of your pod identity
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-podid"
```

6. Aplique el pod al clúster:

```bash
kubectl apply -f pod.yaml
```

## <a name="use-a-user-assigned-managed-identity"></a>Uso de una identidad administrada asignada por el usuario

1. Puede usar la identidad administrada asignada por el usuario creada al [habilitar la identidad administrada en el clúster de AKS][use-managed-identity] para acceder a la instancia de AKV:

```azurecli-interactive
az aks show -g <resource-group> -n <cluster-name> --query identityProfile.kubeletidentity.clientId -o tsv
```

O bien puede crear una nueva y asignarla a su conjunto de escalado de máquinas virtuales o a cada instancia de máquina virtual de su conjunto de disponibilidad:

```azurecli-interactive
az identity create -g <resource-group> -n <identity-name> 
az vmss identity assign -g <resource-group> -n <agent-pool-vmss> --identities <identity-resource-id>
az vm identity assign -g <resource-group> -n <agent-pool-vm> --identities <identity-resource-id>
```

2. Conceda permisos a la identidad para que pueda leer la instancia de AKV y ver su contenido mediante la ejecución del siguiente código:

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-client-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-client-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-client-id>
```

3. Cree un objeto SecretProviderClass con el siguiente código YAML; para ello, rellene los valores de `userAssignedIdentityID`, `keyvaultName`, `tenantId` y los objetos que se recuperarán de la instancia de AKV:

```yml
# This is a SecretProviderClass example using user-assigned identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-user-msi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"          # Set to true for using managed identity
    userAssignedIdentityID: <client-id>   # Set the clientID of the user-assigned managed identity to use
    keyvaultName: <key-vault-name>        # Set to the name of your Azure Key Vault instance
    cloudName: ""                         # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret              # object types: secret, key or cert
          objectVersion: ""               # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-id>                 # The tenant ID of the Azure Key Vault instance
```

4. Aplique el objeto SecretProviderClass al clúster:

```bash
kubectl apply -f secretproviderclass.yaml
```

5. Cree un pod con el siguiente código YAML, en el que debe rellenar el nombre de la identidad:

```yml
# This is a sample pod definition for using SecretProviderClass and user-assigned identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-user-msi
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-user-msi"
```

6. Aplique el pod al clúster:

```bash
kubectl apply -f pod.yaml
```

## <a name="use-a-system-assigned-managed-identity"></a>Uso de una identidad administrada asignada por el sistema

### <a name="prerequisites"></a>Prerrequisitos

- [Habilite la identidad administrada asignada por el sistema][enable-system-assigned-identity] en las máquinas virtuales o los conjuntos de escalado de su clúster.

### <a name="usage"></a>Uso

1. Compruebe que el conjunto de escalado de máquinas virtuales o los nodos del conjunto de disponibilidad tienen su propia identidad asignada por el sistema:

```azurecli-interactive
az vmss identity show -g <resource group>  -n <vmss scalset name> -o yaml
az vm identity show -g <resource group> -n <vm name> -o yaml
```

La salida debe contener `type: SystemAssigned`. Anote el `principalId`.

2. Asigne permisos a la identidad para que pueda leer la instancia de AKV y ver su contenido mediante la ejecución del siguiente código:

```azurecli-interactive
# set policy to access keys in your keyvault
az keyvault set-policy -n <keyvault-name> --key-permissions get --spn <identity-principal-id>
# set policy to access secrets in your keyvault
az keyvault set-policy -n <keyvault-name> --secret-permissions get --spn <identity-principal-id>
# set policy to access certs in your keyvault
az keyvault set-policy -n <keyvault-name> --certificate-permissions get --spn <identity-principal-id>
```

3. Cree un objeto SecretProviderClass con el siguiente código YAML; para ello, rellene los valores de `keyvaultName`, `tenantId` y los objetos que se recuperarán de la instancia de AKV:

```yml
# This is a SecretProviderClass example using system-assigned identity to access Keyvault
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-kvname-system-msi
spec:
  provider: azure
  parameters:
    usePodIdentity: "false"
    useVMManagedIdentity: "true"    # Set to true for using managed identity
    userAssignedIdentityID: ""      # If empty, then defaults to use the system assigned identity on the VM
    keyvaultName: <key-vault-name>
    cloudName: ""                   # [OPTIONAL for Azure] if not provided, azure environment will default to AzurePublicCloud
    objects:  |
      array:
        - |
          objectName: secret1
          objectType: secret        # object types: secret, key or cert
          objectVersion: ""         # [OPTIONAL] object versions, default to latest if empty
        - |
          objectName: key1
          objectType: key
          objectVersion: ""
    tenantId: <tenant-id>           # The tenant ID of the Azure Key Vault instance
```

4. Aplique el objeto SecretProviderClass al clúster:

```bash
kubectl apply -f secretproviderclass.yaml
```

5. Cree un pod con el siguiente código YAML, en el que debe rellenar el nombre de la identidad:

```yml
# This is a sample pod definition for using SecretProviderClass and system-assigned identity to access Keyvault
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline-system-msi
spec:
  containers:
    - name: busybox
      image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
      command:
        - "/bin/sleep"
        - "10000"
      volumeMounts:
      - name: secrets-store01-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-kvname-system-msi"
```

## <a name="next-steps"></a>Pasos siguientes

[Compruebe que los secretos][validate-secrets] se montan en la ruta de acceso del volumen especificada en el archivo YAML del pod.

<!-- LINKS INTERNAL -->

[csi-secrets-store-driver]: ./csi-secrets-store-driver.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[aad-pod-identity-create]: ./use-azure-ad-pod-identity.md#create-an-identity
[use-managed-identity]: ./use-managed-identity.md
[validate-secrets]: ./csi-secrets-store-driver.md#validate-the-secrets
[enable-system-assigned-identity]: ../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#enable-system-assigned-managed-identity-on-an-existing-azure-vm

<!-- LINKS EXTERNAL -->
