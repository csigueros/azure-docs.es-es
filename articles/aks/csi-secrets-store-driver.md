---
title: Uso del controlador Secrets Store CSI para los secretos de Azure Kubernetes Service
description: Aprenda a usar el controlador Secrets Store CSI para integrar almacenes de secretos con Azure Kubernetes Service (AKS).
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: caab5417760c75e46b78241edd65d5c0124d5917
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/29/2021
ms.locfileid: "129234098"
---
# <a name="use-the-secrets-store-csi-driver-for-kubernetes-in-an-azure-kubernetes-service-aks-cluster-preview"></a>Uso del controlador Secrets Store CSI para Kubernetes en un clúster de Azure Kubernetes Service (AKS) (versión preliminar)

El controlador Secrets Store CSI para Kubernetes permite la integración de Azure Key Vault como un almacén de secretos con un clúster de Kubernetes a través de un [volumen CSI][kube-csi].

## <a name="prerequisites"></a>Requisitos previos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

- Antes de empezar, instale la versión más reciente de la [CLI de Azure](/cli/azure/install-azure-cli-windows) y la extensión *aks-preview*.

### <a name="supported-kubernetes-versions"></a>Versiones de Kubernetes compatibles en Azure Kubernetes Service (AKS).

La versión mínima recomendada de Kubernetes para esta característica es la 1.18. 

## <a name="features"></a>Características

- Montaje de secretos, claves o certificados en un pod mediante un volumen CSI
- Admite volúmenes CSI en línea (versión 1.15 o superior) de Kubernetes
- Admite el montaje de varios objetos de almacenamiento de secretos como un solo volumen
- Admite la portabilidad de pods con CRD de SecretProviderClass
- Admite contenedores de Windows
- Sincronización con secretos de Kubernetes (versión del controlador Secrets Store CSI 0.0.10 o posterior)
- Admite la rotación automática de contenido montado y secretos de Kubernetes sincronizados (versión del controlador Secrets Store CSI 0.0.15 o posterior)

## <a name="register-the-aks-azurekeyvaultsecretsprovider-preview-feature"></a>Registro de la característica en vista previa (GB) `AKS-AzureKeyVaultSecretsProvider`

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Para crear un clúster de AKS que pueda usar el controlador Secrets Store CSI, debe habilitar la marca de características `AKS-AzureKeyVaultSecretsProvider` en la suscripción.

Registre la marca de la característica `AKS-AzureKeyVaultSecretsProvider` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKS-AzureKeyVaultSecretsProvider"
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzureKeyVaultSecretsProvider')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="install-the-aks-preview-cli-extension"></a>Instalación de la extensión aks-preview de la CLI

También se necesita la versión 0.5.9 o posterior de la extensión *aks-preview* de la CLI de Azure. Instale la extensión de la CLI de Azure *aks-preview* mediante el comando [az extension add][az-extension-add]. Si ya tiene instalada la extensión, actualice a la versión más reciente disponible mediante el comando [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

## <a name="create-an-aks-cluster-with-secrets-store-csi-driver-support"></a>Creación de un clúster de AKS con compatibilidad con el controlador Secrets Store CSI

En primer lugar, cree un grupo de recursos de Azure:

```azurecli-interactive
az group create -n myResourceGroup -l eastus2
```

Para crear un clúster de AKS con la funcionalidad del controlador Secrets Store CSI, use el comando [az aks create][az-aks-create] con el complemento `azure-keyvault-secrets-provider`.

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-managed-identity
```

El complemento crea una identidad administrada asignada por el usuario con el fin de acceder a los recursos de Azure, denominado `azurekeyvaultsecretsprovider-*`. Podemos usar esta identidad para conectarnos a Azure Key Vault, donde se almacenarán nuestros secretos. Tome nota del `clientId` de la identidad en la salida:

```json
...,
 "addonProfiles": {
    "azureKeyvaultSecretsProvider": {
      ...,
      "identity": {
        "clientId": "<client-id>",
        ...
      }
    }
```

## <a name="upgrade-an-existing-aks-cluster-with-secrets-store-csi-driver-support"></a>Actualización de un clúster de AKS con compatibilidad con el controlador Secrets Store CSI

Para actualizar un clúster de AKS con la funcionalidad del controlador CSI del almacén de secretos, use el comando [az aks enable-addons][az-aks-enable-addons] con el complemento `azure-keyvault-secrets-provider`:

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

Como se indicó anteriormente, el complemento crea una identidad administrada asignada por el usuario que se puede usar para autenticarse en Azure Key Vault.

## <a name="verify-secrets-store-csi-driver-installation"></a>Comprobación de la instalación del controlador CSI del almacén de secretos

La operación anterior instalará el controlador Secrets Store CSI y el proveedor de Azure Key Vault en los nodos. Para comprobar que la operación se ha completado, enumere todos los pods con las etiquetas secrets-store-csi-driver y secrets-store-provider-azure en el espacio de nombres kube-system y asegúrese de que la salida sea similar a la siguiente:

```bash
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'

NAMESPACE     NAME                                     READY   STATUS    RESTARTS   AGE
kube-system   aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
kube-system   aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
kube-system   aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
kube-system   aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
kube-system   aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
kube-system   aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```

## <a name="enabling-and-disabling-autorotation"></a>Habilitación y deshabilitación de la rotación automática

> [!NOTE]
> Cuando se habilita, el controlador Secrets Store CSI actualizará el montaje del pod y el secreto de Kubernetes definido en secretObjects de SecretProviderClass sondeando los cambios cada dos minutos.

Para habilitar la rotación automática de secretos, use la marca `enable-secret-rotation` al crear el clúster:

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

O bien, actualice un clúster existente con el complemento habilitado:

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

Para deshabilitarlo, use la marca `disable-secret-rotation`:

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

## <a name="create-or-use-an-existing-azure-key-vault"></a>Creación o uso de una instancia de Azure Key Vault

Además de un clúster de AKS, necesitará un recurso de Azure Key Vault que contenga el contenido del secreto. Tenga en cuenta que el nombre de Key Vault debe ser único globalmente.

```azurecli
az keyvault create -n <keyvault-name> -g myResourceGroup -l eastus2
```

Azure Key Vault puede almacenar claves, secretos y certificados. En este ejemplo, estableceremos un secreto de texto sin formato denominado `ExampleSecret`:

```azurecli
az keyvault secret set --vault-name <keyvault-name> -n ExampleSecret --value MyAKSExampleSecret
```

Tome nota de las siguientes propiedades para usarlas en la sección siguiente:

- Nombre del objeto secreto en Key Vault
- Tipo de objeto (secreto, clave o certificado)
- Nombre del recurso de Azure Key Vault
- Identificador de inquilino de Azure al que pertenece la suscripción

## <a name="provide-identity-to-access-azure-key-vault"></a>Proporcionar la identidad para acceder a Azure Key Vault

Use los valores de los pasos anteriores para establecer permisos, lo que permite que la identidad administrada creada por el complemento acceda a los objetos keyvault:

```azurecli
az keyvault set-policy -n <keyvault-name> --<object-type>-permissions get --spn <client-id>
```

## <a name="create-and-apply-your-own-secretproviderclass-object"></a>Creación y aplicación de un objeto SecretProviderClass personalizado

Para usar y configurar el controlador Secrets Store CSI para el clúster de AKS, cree un recurso personalizado de SecretProviderClass. Asegúrese de que la matriz `objects` coincide con los objetos que ha almacenado en la instancia de Azure Key Vault:

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1alpha1
kind: SecretProviderClass
metadata:
  name: <keyvault-name>
spec:
  provider: azure
  parameters:
    keyvaultName: "<keyvault-name>"       # The name of the Azure Key Vault
    useVMManagedIdentity: "true"         
    userAssignedIdentityID: "<client-id>" # The clientId of the addon-created managed identity
    cloudName: ""                         # [OPTIONAL for Azure] if not provided, Azure environment will default to AzurePublicCloud 
    objects:  |
      array:
        - |
          objectName: <secret-name>       # In this example, 'ExampleSecret' 
          objectAlias: <secret-alias>     # [OPTIONAL] specify the filename of the object when written to disk - defaults to objectName if not provided
          objectType: secret              # Object types: secret, key or cert
          objectVersion: ""               # [OPTIONAL] object versions, default to latest if empty
    tenantId: "<tenant-id>"               # the tenant ID containing the Azure Key Vault instance
```

Para obtener más información, consulte [Creación de su propio objeto SecretProviderClass][sample-secret-provider-class]. Asegúrese de usar los valores que anotó anteriormente.

### <a name="apply-the-secretproviderclass-to-your-cluster"></a>Aplicación de SecretProviderClass al clúster

A continuación, implemente el recurso SecretProviderClass que ha creado. Por ejemplo:

```bash
kubectl apply -f ./new-secretproviderclass.yaml
```

## <a name="update-and-apply-your-clusters-deployment-yaml"></a>Actualización y aplicación del archivo YAML de implementación del clúster

Para asegurarse de que el clúster use el nuevo recurso personalizado, actualice el archivo YAML de implementación. Por ejemplo:

```yml
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
spec:
  containers:
  - name: busybox
    image: k8s.gcr.io/e2e-test-images/busybox:1.29
    command:
      - "/bin/sleep"
      - "10000"
    volumeMounts:
    - name: secrets-store-inline
      mountPath: "/mnt/secrets-store"
      readOnly: true
  volumes:
    - name: secrets-store-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "<keyvault-name>"
```

Aplique la implementación actualizada al clúster:

```bash
kubectl apply -f ./my-deployment.yaml
```

## <a name="validate-the-secrets"></a>Validación de los secretos

Una vez que se inicia el pod, el contenido montado en la ruta del volumen especificada en el archivo YAML de implementación estará disponible.

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'ExampleSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/ExampleSecret
```

## <a name="disable-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>Deshabilitación del controlador CSI del almacén de secretos en un clúster de AKS existente

Para deshabilitar la funcionalidad del controlador CSI del almacén de secretos en un clúster existente, use el comando [az aks disable-addons][az-aks-disable-addons] con la marca `azure-keyvault-secrets-provider`:

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

## <a name="next-steps"></a>Pasos siguientes
<!-- Add a context sentence for the following links -->
Después de aprender a usar el controlador Secrets Store CSI con un clúster de AKS, consulte los siguientes recursos:

- [Habilitación de controladores CSI para discos de Azure y Azure Files en AKS][csi-storage-drivers]

<!-- Links -->
<!-- Internal -->
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-enable-addons]: /cli/azure/aks#az_aks_enable_addons
[az-aks-disable-addons]: /cli/azure/aks#az_aks_disable_addons
[key-vault-provider]: ../key-vault/general/key-vault-integrate-kubernetes.md
[csi-storage-drivers]: ./csi-storage-drivers.md
[create-key-vault]: ../key-vault/general/quick-create-cli.md
[set-secret-key-vault]: ../key-vault/secrets/quick-create-portal.md
[aks-managed-identity]: ./use-managed-identity.md

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
