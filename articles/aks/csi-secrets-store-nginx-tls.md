---
title: Configuración del controlador CSI del almacén de secretos para habilitar el controlador de entrada NGINX con TLS en Azure Kubernetes Service
description: Procedimiento de configuración del controlador CSI del almacén de secretos para habilitar el controlador de entrada NGINX con TLS para Azure Kubernetes Service (AKS).
author: nickomang
ms.author: nickoman
ms.service: container-service
ms.topic: how-to
ms.date: 10/19/2021
ms.custom: template-how-to
ms.openlocfilehash: f7c40dbcf0944ca5c78182d72346f3fc295ef50c
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131848750"
---
# <a name="set-up-secrets-store-csi-driver-to-enable-nginx-ingress-controller-with-tls"></a>Configuración del controlador CSI del almacén de secretos para habilitar el controlador de entrada NGINX con TLS

Este artículo le guiará por el proceso de protección de un controlador de entrada NGINX con TLS con un clúster de Azure Kubernetes Service (AKS) y una instancia de Azure Key Vault (AKV). Para más información, consulte [TLS en Kubernetes][kubernetes-ingress-tls].

La importación del certificado TLS de entrada en el clúster se puede realizar mediante uno de estos dos métodos:

- **Aplicación**: el manifiesto de implementación de la aplicación declara y monta el volumen del proveedor. Solo cuando se implementa la aplicación, el certificado pasa a estar disponible en el clúster y, cuando se quita la aplicación, también se quita el secreto. Este escenario se adapta a los equipos de desarrollo responsables de la infraestructura de seguridad de la aplicación y su integración con el clúster.
- **Controlador de entrada**: la implementación de entrada se modifica para declarar y montar el volumen del proveedor. El secreto se importa cuando se crean pods de entrada. Los pods de la aplicación no tienen acceso al certificado TLS. Este escenario se adapta a escenarios en los que un equipo (es decir, TI) administra y aprovisiona componentes de infraestructura y redes (incluidos los certificados TLS HTTPS) y otros equipos administran el ciclo de vida de las aplicaciones. En este caso, la entrada es específica de un único espacio de nombres o una única carga de trabajo y se implementa en el mismo espacio de nombres que la aplicación.

## <a name="prerequisites"></a>Prerrequisitos

- Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- Antes de empezar, asegúrese de que la versión de la CLI de Azure es >= `2.30.0`, o bien [instale la versión más reciente](/cli/azure/install-azure-cli).
- Un clúster de AKS con el controlador CSI del almacén de secretos configurado.
- Una instancia de Azure Key Vault.


## <a name="generate-a-tls-certificate"></a>Generación de un certificado TLS

```bash
export CERT_NAME=ingresscert
openssl req -x509 -nodes -days 365 -newkey rsa:2048 \
    -out ingress-tls.crt \
    -keyout ingress-tls.key \
    -subj "/CN=demo.test.com/O=ingress-tls"
```

### <a name="import-the-certificate-to-akv"></a>Importación del certificado en AKV

```bash
export AKV_NAME="[YOUR AKV NAME]"
openssl pkcs12 -export -in ingress-tls.crt -inkey ingress-tls.key  -out $CERT_NAME.pfx
# skip Password prompt
```

```azurecli-interactive
az keyvault certificate import --vault-name $AKV_NAME -n $CERT_NAME -f $CERT_NAME.pfx
```

## <a name="deploy-a-secretproviderclass"></a>Implementación de una clase SecretProviderClass

En primer lugar, cree un espacio de nombres:

```bash
export NAMESPACE=ingress-test
```

```azurecli-interactive
kubectl create ns $NAMESPACE
```

Seleccione un [método para proporcionar una identidad de acceso][csi-ss-identity-access] y configurar la clase YAML secretProviderClass en consecuencia. Además:
- Asegúrese de usar `objectType=secret`, ya que esta es la única manera de obtener la clave privada y el certificado de AKV.
- Establezca `kubernetes.io/tls` como `type` en la sección `secretObjects`.

Consulte lo siguiente para obtener un ejemplo del aspecto que podría tener la clase SecretProviderClass:

```yml
apiVersion: secrets-store.csi.x-k8s.io/v1
kind: SecretProviderClass
metadata:
  name: azure-tls
spec:
  provider: azure
  secretObjects:                            # secretObjects defines the desired state of synced K8s secret objects
  - secretName: ingress-tls-csi
    type: kubernetes.io/tls
    data: 
    - objectName: $CERT_NAME
      key: tls.key
    - objectName: $CERT_NAME
      key: tls.crt
  parameters:
    usePodIdentity: "false"
    keyvaultName: $AKV_NAME                 # the name of the AKV instance
    objects: |
      array:
        - |
          objectName: $CERT_NAME
          objectType: secret
    tenantId: $TENANT_ID                    # the tenant ID of the AKV instance
```

Aplicación de la clase SecretProviderClass al clúster de Kubernetes:

```bash
kubectl apply -f secretProviderClass.yaml -n $NAMESPACE
```

## <a name="deploy-the-ingress-controller"></a>Implementar el controlador de entrada

### <a name="add-the-official-ingress-chart-repository"></a>Adición del repositorio oficial de gráficos de entrada

```bash
helm repo add ingress-nginx https://kubernetes.github.io/ingress-nginx
helm repo update
```

### <a name="configure-and-deploy-the-nginx-ingress"></a>Configuración e implementación de la entrada NGINX

Como se mencionó anteriormente, en función de su escenario, puede elegir enlazar el certificado a la aplicación o al controlador de entrada. Siga las instrucciones que se indican a continuación según su selección:

#### <a name="bind-certificate-to-application"></a>Enlace del certificado a la aplicación

La implementación de la aplicación hará referencia al proveedor de Azure Key Vault del controlador CSI del almacén de secretos.

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux
```

#### <a name="bind-certificate-to-ingress-controller"></a>Enlace del certificado al controlador de entrada

La implementación del controlador de entrada hará referencia al proveedor de Azure Key Vault del controlador CSI del almacén de secretos.

> [!NOTE]
> Si no usa la identidad del pod de Azure Active Directory (AAD) como método de acceso, quite la línea con`--set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME`.

```bash
helm install ingress-nginx/ingress-nginx --generate-name \
    --namespace $NAMESPACE \
    --set controller.replicaCount=2 \
    --set controller.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set defaultBackend.nodeSelector."beta\.kubernetes\.io/os"=linux \
    --set controller.podLabels.aadpodidbinding=$AAD_POD_IDENTITY_NAME \
    -f - <<EOF
controller:
  extraVolumes:
      - name: secrets-store-inline
        csi:
          driver: secrets-store.csi.k8s.io
          readOnly: true
          volumeAttributes:
            secretProviderClass: "azure-tls"
  extraVolumeMounts:
      - name: secrets-store-inline
        mountPath: "/mnt/secrets-store"
        readOnly: true
EOF
```

Compruebe que se ha creado el secreto de Kubernetes:

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

## <a name="deploy-the-application"></a>Implementación de la aplicación

De nuevo, en función de su escenario, las instrucciones cambiarán ligeramente. Siga las instrucciones correspondientes al escenario que ha seleccionado hasta ahora:

### <a name="deploy-the-application-using-an-application-reference"></a>Implementación de la aplicación mediante una referencia de aplicación

Cree un archivo llamado `deployment.yaml` con el siguiente contenido:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox-one
  labels:
    app: busybox-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox-one
  template:
    metadata:
      labels:
        app: busybox-one
    spec:
      containers:
      - name: busybox
        image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
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
              secretProviderClass: "azure-tls"
---
apiVersion: v1
kind: Service
metadata:
  name: busybox-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: busybox-one
```

Y aplíquelo al clúster:

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

Compruebe que se ha creado el secreto de Kubernetes:

```bash
kubectl get secret -n $NAMESPACE

NAME                                             TYPE                                  DATA   AGE
ingress-tls-csi                                  kubernetes.io/tls                     2      1m34s
```

### <a name="deploy-the-application-using-an-ingress-controller-reference"></a>Implementación de la aplicación mediante una referencia de controlador de entrada

Cree un archivo llamado `deployment.yaml` con el siguiente contenido:

```yml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: busybox-one
  labels:
    app: busybox-one
spec:
  replicas: 1
  selector:
    matchLabels:
      app: busybox-one
  template:
    metadata:
      labels:
        app: busybox-one
    spec:
      containers:
      - name: busybox
        image: k8s.gcr.io/e2e-test-images/busybox:1.29-1
        command:
          - "/bin/sleep"
          - "10000"
---
apiVersion: v1
kind: Service
metadata:
  name: busybox-one
spec:
  type: ClusterIP
  ports:
  - port: 80
  selector:
    app: busybox-one
```

Y aplíquelo al clúster:

```bash
kubectl apply -f deployment.yaml -n $NAMESPACE
```

## <a name="deploy-an-ingress-resource-referencing-the-secret"></a>Implementación de un recurso de entrada que haga referencia al secreto

Por último, podemos implementar un recurso de entrada de Kubernetes que haga referencia a nuestro secreto. Cree un archivo llamado `ingress.yaml` con el siguiente contenido:

```yml
apiVersion: networking.k8s.io/v1
kind: Ingress
metadata:
  name: ingress-tls
  annotations:
    kubernetes.io/ingress.class: nginx
    nginx.ingress.kubernetes.io/rewrite-target: /$1
spec:
  tls:
  - hosts:
    - demo.test.com
    secretName: ingress-tls-csi
  rules:
  - host: demo.test.com
    http:
      paths:
      - backend:
          service:
            name: busybox-one
            port:
              number: 80
        path: /(.*)
      - backend:
          service:
            name: busybox-two
            port:
              number: 80
        path: /two(/|$)(.*)
```

Tome nota de la sección `tls` que hace referencia al secreto que hemos creado anteriormente y aplique el archivo al clúster:

```bash
kubectl apply -f ingress.yaml -n $NAMESPACE
```

## <a name="obtain-the-external-ip-address-of-the-ingress-controller"></a>Obtención de la dirección IP externa del controlador de entrada

Use `kubectl get service` para obtener la dirección IP externa del controlador de entrada.

```bash
 kubectl get service -l app=nginx-ingress --namespace $NAMESPACE

NAME                                       TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)                      AGE
nginx-ingress-1588032400-controller        LoadBalancer   10.0.255.157   52.xx.xx.xx      80:31293/TCP,443:31265/TCP   19m
nginx-ingress-1588032400-default-backend   ClusterIP      10.0.223.214   <none>           80/TCP                       19m 
```

## <a name="test-ingress-secured-with-tls"></a>Prueba de la entrada protegida con TLS

Use `curl` para comprobar que la entrada se ha configurado correctamente con TLS. Asegúrese de usar la dirección IP externa que ha obtenido en el paso anterior:

```bash
curl -v -k --resolve demo.test.com:443:52.xx.xx.xx https://demo.test.com

# You should see output similar to the following
*  subject: CN=demo.test.com; O=ingress-tls
*  start date: Oct 15 04:23:46 2021 GMT
*  expire date: Oct 15 04:23:46 2022 GMT
*  issuer: CN=demo.test.com; O=ingress-tls
*  SSL certificate verify result: self signed certificate (18), continuing anyway.
```

<!-- LINKS INTERNAL -->
[csi-ss-identity-access]: ./csi-secrets-store-identity-access.md 
<!-- LINKS EXTERNAL -->
[kubernetes-ingress-tls]: https://kubernetes.io/docs/concepts/services-networking/ingress/#tls