---
title: Solución de problemas del proveedor de Azure Key Vault para el controlador Secrets Store CSI en Azure Kubernetes Service (AKS)
description: Obtenga información sobre cómo solucionar problemas comunes al usar el proveedor de Azure Key Vault para el controlador Secrets Store CSI en Azure Kubernetes Service (AKS).
author: nickomang
ms.service: container-service
ms.topic: troubleshooting
ms.date: 10/18/2021
ms.author: nickoman
ms.openlocfilehash: 94532cd6d0aa8f65bbd17b26886da4ee05a5860e
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132335943"
---
# <a name="troubleshooting-azure-key-vault-provider-for-secrets-store-csi-driver"></a>Solución de problemas del proveedor de Azure Key Vault para el controlador Secrets Store CSI

En este artículo se proporciona información general sobre los componentes que pueden ayudar en la solución de problemas y una lista de problemas comunes y sus soluciones.

## <a name="logging"></a>Registro

Los registros del proveedor de Azure Key Vault están disponibles en los pods del proveedor. Para solucionar problemas con el proveedor, puede ver los registros del pod del proveedor que se ejecuta en el mismo nodo que el pod de la aplicación:

```bash
# find the secrets-store-provider-azure pod running on the same node as your application pod
kubectl get pods -l app=secrets-store-provider-azure -n kube-system -o wide
kubectl logs -l app=secrets-store-provider-azure -n kube-system --since=1h | grep ^E
```

También se puede acceder a los registros del controlador Secrets Store CSI:

```bash
# find the secrets-store-csi-driver pod running on the same node as your application pod
kubectl get pods -l app=secrets-store-csi-driver -n kube-system -o wide
kubectl logs -l app=secrets-store-csi-driver -n kube-system --since=1h | grep ^E
```

## <a name="common-issues"></a>Problemas comunes

### <a name="failed-to-get-key-vault-token-nmi-response-failed-with-status-code-404"></a>No se pudo obtener el token del almacén de claves: error de respuesta NMI con el código de estado 404.

Si recibió el siguiente mensaje de error en los registros o eventos:

```bash
Warning  FailedMount  74s    kubelet            MountVolume.SetUp failed for volume "secrets-store-inline" : kubernetes.io/csi: mounter.SetupAt failed: rpc error: code = Unknown desc = failed to mount secrets store objects for pod default/test, err: rpc error: code = Unknown desc = failed to mount objects, error: failed to get keyvault client: failed to get key vault token: nmi response failed with status code: 404, err: <nil>
```

Significa que el componente NMI de aad-pod-identity devolvió un error para la solicitud de token. Para obtener más detalles sobre el error, consulte los registros del pod de NMI y consulte la [guía de solución de problemas][aad-troubleshooting] de las identidades del pod de AAD para resolver el problema.

### <a name="keyvaultbaseclientgetsecret-failure-sending-request-statuscode0--original-error-context-canceled"></a>keyvault.BaseClient#GetSecret: error al enviar la solicitud: StatusCode=0 – Error original: contexto cancelado

Si recibió el siguiente mensaje de error en los registros o eventos:

```bash
E1029 17:37:42.461313       1 server.go:54] failed to process mount request, error: keyvault.BaseClient#GetSecret: Failure sending request: StatusCode=0 -- Original Error: context deadline exceeded
```

Esto significa que el pod del proveedor no puede acceder a la instancia de AKV porque:

- Hay una regla de firewall que bloquea el tráfico de salida del proveedor.
- Hay directivas de red configuradas en el clúster que bloquean el tráfico de salida.
- Los pods del proveedor se ejecutan en hostNetwork. Por lo tanto, si hay una directiva que bloquea este tráfico o hay vibración de red en el nodo, podría producir el error anterior. Compruebe si hay directivas configuradas para bloquear el tráfico y permitir la lista de pods del proveedor. Además, asegúrese de que hay conectividad con Azure AD y Key Vault desde el nodo.

Puede probar la conectividad de Key Vault desde el pod que se ejecuta en la red de host, como se muestra a continuación:

- Crear pod

  ```bash
  cat <<EOF | kubectl apply -f -
  apiVersion: v1
  kind: Pod
  metadata:
    name: curl
  spec:
    hostNetwork: true
    containers:
    - args:
      - tail
      - -f
      - /dev/null
      image: curlimages/curl:7.75.0
      name: curl
    dnsPolicy: ClusterFirst
    restartPolicy: Always
  EOF
  ```

- Ejecutar en el pod creado anteriormente

  ```bash
  kubectl exec -it curl -- sh
  ```

- Autenticar con AKV

  ```bash
  curl -X POST 'https://login.microsoftonline.com/<AAD_TENANT_ID>/oauth2/v2.0/token' -d 'grant_type=client_credentials&client_id=<AZURE_CLIENT_ID>&client_secret=<AZURE_CLIENT_SECRET>&scope=https://vault.azure.net/.default'
  ```

- Intentar obtener un secreto ya creado en AKV

  ```bash
  curl -X GET 'https://<KEY_VAULT_NAME>.vault.azure.net/secrets/<SECRET_NAME>?api-version=7.2' -H "Authorization: Bearer <ACCESS_TOKEN_ACQUIRED_ABOVE>"
  ```

<!-- LINKS EXTERNAL -->
[aad-troubleshooting]: https://azure.github.io/aad-pod-identity/docs/troubleshooting/
