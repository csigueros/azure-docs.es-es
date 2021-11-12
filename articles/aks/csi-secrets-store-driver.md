---
title: Uso del proveedor de Azure Key Vault para el controlador CSI de Secrets Store para secretos de Azure Kubernetes Service
description: Aprenda a usar el proveedor de Azure Key Vault para el controlador CSI de Secrets Store a fin de integrar almacenes de secretos con Azure Kubernetes Service (AKS).
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/13/2021
ms.custom: template-how-to, devx-track-azurecli
ms.openlocfilehash: cdab72e0a1633aa75a5594acf9b506d944f0eaa0
ms.sourcegitcommit: 591ffa464618b8bb3c6caec49a0aa9c91aa5e882
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/06/2021
ms.locfileid: "131893014"
---
# <a name="use-the-azure-key-vault-provider-for-secrets-store-csi-driver-in-an-azure-kubernetes-service-aks-cluster"></a>Uso del proveedor de Azure Key Vault para el controlador CSI de Secrets Store en un clúster de Azure Kubernetes Service (AKS)

El proveedor de Azure Key Vault para el controlador CSI de Secrets Store permite la integración de Azure Key Vault como un almacén de secretos con un clúster de Kubernetes mediante un [volumen CSI][kube-csi].

## <a name="prerequisites"></a>Requisitos previos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

- Antes de empezar, asegúrese de que la CLI de Azure es >= `2.30.0`, o bien [instale la versión más reciente](/cli/azure/install-azure-cli).

### <a name="supported-kubernetes-versions"></a>Versiones de Kubernetes compatibles en Azure Kubernetes Service (AKS).

La versión mínima recomendada de Kubernetes se basa en la [ventana de compatibilidad de la versión gradual de Kubernetes][kubernetes-version-support]. Asegúrese de que ejecuta N-2 o una versión más reciente.

## <a name="features"></a>Características

- Montaje de secretos, claves o certificados en un pod mediante un volumen CSI
- Admite volúmenes CSI en línea
- Admite el montaje de varios objetos de almacenamiento de secretos como un solo volumen
- Admite la portabilidad de pods con CRD de SecretProviderClass
- Admite contenedores de Windows
- Sincronización con secretos de Kubernetes
- Admite la rotación automática de contenido montado y secretos de Kubernetes sincronizados

## <a name="create-an-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>Creación de un clúster de AKS con el proveedor de Azure Key Vault para la compatibilidad con el controlador CSI de Secrets Store

En primer lugar, cree un grupo de recursos de Azure:

```azurecli-interactive
az group create -n myResourceGroup -l eastus2
```

Para crear un clúster de AKS con la funcionalidad del proveedor de Azure Key Vault para el controlador CSI de Secrets Store use el comando [az aks create][az-aks-create] con el complemento `azure-keyvault-secrets-provider`.

```azurecli-interactive
az aks create -n myAKSCluster -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-managed-identity
```

El complemento crea una identidad administrada asignada por el usuario con el fin de acceder a los recursos de Azure, denominado `azurekeyvaultsecretsprovider-*`. En este ejemplo, se usará esta identidad para la conexión a Azure Key Vault, donde se almacenarán los secretos, pero se pueden usar otros [métodos de acceso de identidad][identity-access-methods]. Tome nota del `clientId` de la identidad en la salida:

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

## <a name="upgrade-an-existing-aks-cluster-with-azure-key-vault-provider-for-secrets-store-csi-driver-support"></a>Actualización de un clúster de AKS existente con el proveedor de Azure Key Vault para la compatibilidad con el controlador CSI de Secrets Store

Para actualizar un clúster de AKS con la funcionalidad del proveedor de Azure Key Vault para el controlador CSI de Secrets Store, use el comando [az aks enable-addons][az-aks-enable-addons] con el complemento `azure-keyvault-secrets-provider`:

```azurecli-interactive
az aks enable-addons --addons azure-keyvault-secrets-provider --name myAKSCluster --resource-group myResourceGroup
```

Como se indicó anteriormente, el complemento crea una identidad administrada asignada por el usuario que se puede usar para autenticarse en Azure Key Vault.

## <a name="verify-azure-key-vault-provider-for-secrets-store-csi-driver-installation"></a>Comprobación de la instalación del proveedor de Azure Key Vault para el controlador CSI de Secrets Store

La operación anterior instalará el controlador CSI de Secrets Store y el proveedor de Azure Key Vault en los nodos. Para comprobar que la operación se ha completado, enumere todos los pods con las etiquetas `secrets-store-csi-driver` y `secrets-store-provider-azure` del espacio de nombres kube-system y asegúrese de que la salida sea similar a la siguiente:

```bash
kubectl get pods -n kube-system -l 'app in (secrets-store-csi-driver, secrets-store-provider-azure)'

NAME                                     READY   STATUS    RESTARTS   AGE
aks-secrets-store-csi-driver-4vpkj       3/3     Running   2          4m25s
aks-secrets-store-csi-driver-ctjq6       3/3     Running   2          4m21s
aks-secrets-store-csi-driver-tlvlq       3/3     Running   2          4m24s
aks-secrets-store-provider-azure-5p4nb   1/1     Running   0          4m21s
aks-secrets-store-provider-azure-6pqmv   1/1     Running   0          4m24s
aks-secrets-store-provider-azure-f5qlm   1/1     Running   0          4m25s
```

Asegúrese de que un pod del controlador CSI de Secrets Store y un pod de proveedor de Azure Key Vault se ejecutan en cada nodo de los grupos de nodos de clúster.

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

El controlador CSI de Secrets Store permite que los métodos siguientes accedan a una instancia de Azure Key Vault:
- [Identidad de pod de Azure Active Directory][aad-pod-identity]
- Identidad administrada asignada por el sistema o el usuario

Siga los pasos para [proporcionar una identidad a fin de acceder a Azure Key Vault][identity-access-methods] para el método elegido.

## <a name="validate-the-secrets"></a>Validación de los secretos

Una vez que se inicia el pod, el contenido montado en la ruta del volumen especificada en el archivo YAML de implementación estará disponible.

```Bash
## show secrets held in secrets-store
kubectl exec busybox-secrets-store-inline -- ls /mnt/secrets-store/

## print a test secret 'ExampleSecret' held in secrets-store
kubectl exec busybox-secrets-store-inline -- cat /mnt/secrets-store/ExampleSecret
```

## <a name="obtaining-certificates-and-keys"></a>Obtención de certificados y claves

El diseño de Azure Key Vault hace distinciones nítidas entre claves, secretos y certificados. Las características de certificados del servicio Key Vault se han diseñado mediante sus funcionalidades de claves y secretos. Cuando se crea un certificado de Key Vault, se crean también una clave direccionable y un secreto con el mismo nombre. La clave permite las operaciones de clave y el secreto la recuperación del valor del certificado como un secreto. Un certificado de Key Vault también contiene metadatos del certificado X.509 público. Azure Key Vault almacena las partes pública y privada del certificado en un secreto. Cada componente individual se puede obtener mediante la especificación de `objectType` en SecretProviderClass. En la tabla siguiente se muestra qué objeto se asigna a los distintos recursos asociados al certificado:

|Object|Valor devuelto|Devuelve toda la cadena de certificados|
|---|---|---|
|`key`|Clave pública en formato PEM|N/D|
|`cert`|Certificado en formato PEM|No|
|`secret`|Clave privada y certificado en formato PEM|Yes|

## <a name="disable-azure-key-vault-provider-for-secrets-store-csi-driver-on-an-existing-aks-cluster"></a>Deshabilitación del proveedor de Azure Key Vault para el controlador CSI de Secrets Store en un clúster de AKS existente

> [!NOTE]
> Antes de deshabilitar el complemento, asegúrese de que no hay ninguna instancia de `SecretProviderClass` en uso. Si intenta deshabilitar el complemento mientras existe una instancia de `SecretProviderClass`, se producirá un error.

Para deshabilitar la funcionalidad del proveedor de Azure Key Vault para el controlador CSI de Secrets Store en un clúster existente, use el comando [az aks disable-addons][az-aks-disable-addons] con la marca `azure-keyvault-secrets-provider`:

```azurecli-interactive
az aks disable-addons --addons azure-keyvault-secrets-provider -g myResourceGroup -n myAKSCluster
```

> [!NOTE]
> Si el complemento está deshabilitado, las cargas de trabajo existentes no tendrán ningún problema y no verá ninguna actualización en los secretos montados. Si el pod se reinicia o se crea uno como parte del evento de escalado vertical, el pod no se iniciará porque el controlador ya está en ejecución.

## <a name="additional-configuration-options"></a>Opciones de configuración adicionales

### <a name="enabling-and-disabling-autorotation"></a>Habilitación y deshabilitación de la rotación automática

> [!NOTE]
> Cuando se habilita, el proveedor de Azure Key Vault para el controlador CSI de Secrets Store actualizará el montaje del pod y el secreto de Kubernetes definido en secretObjects de SecretProviderClass mediante el sondo periódico de los cambios en función del intervalo de sondeo de rotación definido. El intervalo de sondeo de rotación predeterminado es de 2 m.

Para habilitar la rotación automática de secretos, use la marca `enable-secret-rotation` al crear el clúster:

```azurecli-interactive
az aks create -n myAKSCluster2 -g myResourceGroup --enable-addons azure-keyvault-secrets-provider --enable-secret-rotation
```

O bien, actualice un clúster existente con el complemento habilitado:

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation
```

Para especificar un intervalo de rotación personalizado, use la marca `rotation-poll-interval`:

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --enable-secret-rotation --rotation-poll-interval 5m
```

Para deshabilitarlo, use la marca `disable-secret-rotation`:

```azurecli-interactive
az aks update -g myResourceGroup -n myAKSCluster2 --disable-secret-rotation
```

### <a name="sync-mounted-content-with-a-kubernetes-secret"></a>Sincronización de contenido montado con un secreto de Kubernetes

En algunos casos, es posible que quiera crear un secreto de Kubernetes para reflejar el contenido montado.

Al crear una instancia de SecretProviderClass, use el campo `secretObjects` para definir el estado deseado de los secretos de Kubernetes:

> [!NOTE]
> Este no es un ejemplo completo. Tendrá que modificarlo para admitir el método de acceso de identidad de Azure Key Vault elegido.

> [!NOTE]
> Los secretos solo se sincronizarán una vez que inicie un pod que monte los secretos. El uso exclusivo de la sincronización con la característica de secretos de Kubernetes no funciona. Cuando se eliminan todos los pods que consumen el secreto, también se elimina el secreto de Kubernetes.


> [!NOTE]
> Asegúrese de que `objectName` en `secretObjects` coincide con el nombre de archivo del contenido montado. Si en su lugar se usa `objectAlias`, debe coincidir con el alias del objeto.

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-sync
spec:
  provider: azure                             
  secretObjects:                              # [OPTIONAL] SecretObject defines the desired state of synced K8s secret objects
  - data:
    - key: username                           # data field to populate
      objectName: foo1                        # name of the mounted content to sync. this could be the object name or the object alias
    secretName: foosecret                     # name of the Kubernetes Secret object
    type: Opaque                              # type of the Kubernetes Secret object e.g. Opaque, kubernetes.io/tls
```

#### <a name="set-environment-variables-to-reference-kubernetes-secrets"></a>Establecimiento de variables de entorno para hacer referencia a secretos de Kubernetes

Una vez que se crea el secreto de Kubernetes, puede establecer una variable de entorno en el pod que haga referencia al secreto de Kubernetes:

> [!NOTE]
> Este no es un ejemplo completo. Tendrá que modificarlo para admitir el método de acceso de identidad de Azure Key Vault elegido.

```yml
kind: Pod
apiVersion: v1
metadata:
  name: busybox-secrets-store-inline
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
      env:
      - name: SECRET_USERNAME
        valueFrom:
          secretKeyRef:
            name: foosecret
            key: username
  volumes:
    - name: secrets-store01-inline
      csi:
        driver: secrets-store.csi.k8s.io
        readOnly: true
        volumeAttributes:
          secretProviderClass: "azure-sync"
```

## <a name="metrics"></a>Métricas

### <a name="azure-key-vault-provider"></a>Proveedor de Azure Key Vault

Las métricas se sirven mediante Prometheus desde el puerto 8898, pero este puerto no se expone fuera del pod de forma predeterminada. Acceda a las métricas desde localhost mediante `kubectl port-forward`:

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-provider-azure 8898:8898 &
curl localhost:8898/metrics
```

En la tabla siguiente se enumeran las métricas proporcionadas por el proveedor de Azure Key Vault para el controlador CSI de Secrets Store:

|Métrica|Descripción|Etiquetas|
|----|----|----|
|keyvault_request|Distribución del tiempo que se ha tardado en obtener del almacén de claves|`os_type=<runtime os>`, `provider=azure`, `object_name=<keyvault object name>`, `object_type=<keyvault object type>`, `error=<error if failed>`|
|grpc_request|Distribución del tiempo que se ha tardado en obtener en las solicitudes gRPC|`os_type=<runtime os>`, `provider=azure`, `grpc_method=<rpc full method>`, `grpc_code=<grpc status code>`, `grpc_message=<grpc status message>`|

### <a name="secrets-store-csi-driver"></a>Controlador CSI de Secrets Store

Las métricas se sirven desde el puerto 8095, pero este puerto no se expone fuera del pod de forma predeterminada. Acceda a las métricas desde localhost mediante `kubectl port-forward`:

```bash
kubectl port-forward -n kube-system ds/aks-secrets-store-csi-driver 8095:8095 &
curl localhost:8095/metrics
```

En la tabla siguiente se enumeran las métricas proporcionadas por el controlador CSI de Secrets Store:

|Métrica|Descripción|Etiquetas|
|----|----|----|
|total_node_publish|Número total de solicitudes de montaje de volumen correctas|`os_type=<runtime os>`, `provider=<provider name>`|
|total_node_unpublish|Número total de solicitudes de desmontaje de volumen correctas|`os_type=<runtime os>`|
|total_node_publish_error|Número total de errores en las solicitudes de montaje de volumen|`os_type=<runtime os>`, `provider=<provider name>`, `error_type=<error code>`|
|total_node_unpublish_error|Número total de errores en las solicitudes de desmontaje de volumen|`os_type=<runtime os>`|
|total_sync_k8s_secret|Número total de secretos k8s sincronizados|`os_type=<runtime os`, `provider=<provider name>`|
|sync_k8s_secret_duration_sec|Distribución del tiempo que se ha tardado en sincronizar el secreto k8s|`os_type=<runtime os>`|
|total_rotation_reconcile|Número total de conciliaciones de rotación|`os_type=<runtime os>`, `rotated=<true or false>`|
|total_rotation_reconcile_error|Número total de conciliaciones de rotación con error|`os_type=<runtime os>`, `rotated=<true or false>`, `error_type=<error code>`|
|total_rotation_reconcile_error|Distribución del tiempo que se ha tardado en girar el contenido del almacén de secretos para pods|`os_type=<runtime os>`|

## <a name="next-steps"></a>Pasos siguientes
<!-- Add a context sentence for the following links -->
Después de aprender a usar el proveedor de Azure Key Vault Provider para el controlador CSI de Secrets Store con un clúster de AKS, vea los recursos siguientes:

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
[identity-access-methods]: ./csi-secrets-store-identity-access.md
[aad-pod-identity]: ./use-azure-ad-pod-identity.md
[kubernetes-version-support]: ./supported-kubernetes-versions.md?tabs=azure-cli#kubernetes-version-support-policy

<!-- External -->
[kube-csi]: https://kubernetes-csi.github.io/docs/
[key-vault-provider-install]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/installation
[sample-secret-provider-class]: https://azure.github.io/secrets-store-csi-driver-provider-azure/getting-started/usage/#create-your-own-secretproviderclass-object
