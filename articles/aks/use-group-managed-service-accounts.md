---
title: Habilitación de cuentas de servicio administradas de grupo (GMSA) para los nodos de Windows Server en el clúster de Azure Kubernetes Service (AKS) (versión preliminar)
description: Aprenda a habilitar cuentas de servicio administradas de grupo (GMSA) para los nodos de Windows Server en el clúster de Azure Kubernetes Service (AKS) a fin de proteger los pods.
services: container-service
ms.topic: article
ms.date: 11/01/2021
ms.openlocfilehash: f0acd8cc19ab8a0994b6218a171d8343eef21096
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131478116"
---
# <a name="enable-group-managed-service-accounts-gmsa-for-you-windows-server-nodes-on-your-azure-kubernetes-service-aks-cluster-preview"></a>Habilitación de cuentas de servicio administradas de grupo (GMSA) para los nodos de Windows Server en el clúster de Azure Kubernetes Service (AKS) (versión preliminar)

Una [cuenta de servicio administrado de grupo (GMSA)][gmsa-overview] es una cuenta de dominio administrada para varios servidores que proporciona administración automática de contraseñas, administración simplificada de nombres de entidad de seguridad de servicio (SPN) y la posibilidad de delegar la administración a otros administradores. AKS proporciona la capacidad de habilitar GMSA en los nodos de Windows Server, lo que permite que los contenedores que se ejecutan en nodos de Windows Server se integren y administren mediante GMSA.

La habilitación de GMSA con nodos de Windows Server en AKS está en versión preliminar.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="pre-requisites"></a>Requisitos previos

Para habilitar GMSA con nodos de Windows Server en AKS se necesita lo siguiente:

* Kubernetes 1.19 o superior.
* La versión 0.5.37 o posterior de la extensión `aks-preview`.
* El entorno de ejecución de contenedor de Docker, que es actualmente el predeterminado.
* [Identidades administradas][aks-managed-id] con el clúster de AKS.
* Permisos para crear o actualizar una instancia de Azure Key Vault.
* Permisos para configurar GMSA en el servicio Dominio de Active Directory o en la instancia local de Active Directory.
* El controlador de dominio debe tener Active Directory Web Services habilitado y debe ser accesible en el puerto 9389 por el clúster de AKS.

### <a name="install-the-aks-preview-azure-cli"></a>Instalación de la CLI de Azure `aks-preview`

Necesitará la extensión de la CLI de Azure *aks-preview*. Instale la extensión de la CLI de Azure *aks-preview* mediante el comando [az extension add][az-extension-add]. También puede instalar las actualizaciones disponibles mediante el comando [az extension update][az-extension-update].

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-the-akswindowsgmsapreview-preview-feature"></a>Registro de la característica en vista previa (GB) `AKSWindowsGmsaPreview`

Para usar la característica, también debe habilitar la marca de característica `AKSWindowsGmsaPreview` en la suscripción.

Registre la marca de la característica `AKSWindowsGmsaPreview` con el comando [az feature register][az-feature-register], como se muestra en el siguiente ejemplo:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "AKSWindowsGmsaPreview"
```

Tarda unos minutos en que el estado muestre *Registrado*. Puede comprobar el estado de registro con el comando [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKSWindowsGmsaPreview')].{Name:name,State:properties.state}"
```

Cuando haya terminado, actualice el registro del proveedor de recursos *Microsoft.ContainerService* con el comando [az provider register][az-provider-register]:

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="configure-gmsa-on-active-directory-domain-controller"></a>Configuración de GMSA en el controlador de dominio de Active Directory

Para usar GMSA con AKS, necesita GMSA y una credencial de usuario de dominio estándar a fin de acceder a la credencial de GMSA configurada en el controlador de dominio. Para configurar GMSA en el controlador de dominio, vea [Introducción a las cuentas de servicio administradas de grupo][gmsa-getting-started]. Para la credencial de usuario de dominio estándar, puede utilizar un usuario existente o crear uno, siempre que tenga acceso a la credencial de GMSA.

> [!IMPORTANT]
> Debe usar el servicio Dominio de Active Directory o el servicio Active Directory local. En este momento, no puede usar Azure Active Directory para configurar GMSA con un clúster de AKS.

## <a name="store-the-standard-domain-user-credentials-in-azure-key-vault"></a>Almacene las credenciales de usuario de dominio estándar en Azure Key Vault

El clúster de AKS usa las credenciales de usuario de dominio estándar para acceder a las credenciales de GMSA desde el controlador de dominio. A fin de proporcionar acceso seguro a esas credenciales para el clúster de AKS, se deben almacenar en Azure Key Vault. Puede crear un almacén de claves o usar uno existente.

Use `az keyvault secret set` para almacenar la credencial de usuario de dominio estándar como secreto en el almacén de claves. En el ejemplo siguiente se almacena la credencial de usuario de dominio con la clave *GMSADomainUserCred* en el almacén de claves *MyAKSGMSAVault*. Debe reemplazar los parámetros por la credencial de usuario de dominio, la clave y el almacén de claves propios.

```azurecli
az keyvault secret set --vault-name MyAKSGMSAVault --name "GMSADomainUserCred&quot; --value &quot;$Domain\\$DomainUsername:$DomainUserPassword"
```

## <a name="optional-use-a-custom-vnet-with-custom-dns"></a>Opcional: Uso de una red virtual personalizada con DNS personalizado

El controlador de dominio se debe configurar mediante DNS para que el clúster de AKS pueda acceder a él. Puede configurar la red y el DNS fuera del clúster de AKS para permitir que el clúster acceda al controlador de dominio. Como alternativa, puede configurar una red virtual personalizada con un DNS personalizado mediante Azure CNI con el clúster de AKS para proporcionar acceso al controlador de dominio. Para más información, vea [Configuración de redes de Azure CNI en Azure Kubernetes Service (AKS)][aks-cni].

## <a name="optional-use-your-own-kubelet-identity-for-your-cluster"></a>Opcional: Uso de una identidad de kubelet propia para el clúster

Para proporcionar al clúster de AKS acceso al almacén de claves, la identidad del kubelet del clúster necesita acceso al almacén de claves. De manera predeterminada, cuando se crea un clúster con la identidad administrada habilitada, se crea automáticamente una identidad de kubelet. Puede conceder acceso al almacén de claves para esta identidad después de la creación del clúster, lo que se realiza en un paso posterior.

Como alternativa, puede crear una identidad propia y usarla durante la creación del clúster en un paso posterior. Para más información sobre las identidades administradas proporcionadas, vea [Resumen de identidades administradas][aks-managed-id-kubelet].

Para crear una identidad propia, use `az identity create`. En el ejemplo siguiente se crea una identidad *myIdentity* en el grupo de recursos *myResourceGroup*.

```azurecli
az identity create --name myIdentity --resource-group myResourceGroup
```

Puede conceder a la identidad de kubelet acceso al almacén de claves antes o después de crear el clúster. En el ejemplo siguiente se usa `az identity list` para obtener el identificador de la identidad y establecerlo en *MANAGED_ID*; después se usa `az keyvault set-policy` para conceder a la identidad acceso al almacén de claves *MyAKSGMSAVault*.

```azurecli
MANAGED_ID=$(az identity list --query "[].id" -o tsv)
az keyvault set-policy --name "MyAKSGMSAVault" --object-id $MANAGED_ID --secret-permissions get
```

## <a name="create-aks-cluster"></a>Creación de un clúster de AKS

Para usar GMSA con el clúster de AKS, use los parámetros *enable-windows-gmsa*, *gmsa-dns-server*, *gmsa-root-domain-name* y *enable-managed-identity*.

> [!NOTE]
> Al crear un clúster con grupos de nodos de Windows Server, debe especificar las credenciales de administrador al crear el clúster. Los comandos siguientes le solicitan un nombre de usuario y lo establecen en WINDOWS_USERNAME para su uso en un comando posterior (recuerde que los comandos de este artículo se incluyen en un shell de BASH).
> 
> ```azurecli
> echo "Please enter the username to use as administrator credentials for Windows Server nodes on your cluster: " && read WINDOWS_USERNAME
> ```

Use `az aks create` para crear un clúster de AKS y después `az aks nodepool add` para agregar un grupo de nodos de Windows Server. En el ejemplo siguiente se crea un clúster *MyAKS* en el grupo de recursos *MyResourceGroup,* , se habilita GMSA y, después, se agrega un nuevo grupo de nodos denominado *npwin*.

> [!NOTE]
> Si usa una red virtual personalizada, también debe especificar su identificador mediante *vnet-subnet-id* y es posible que también tenga que agregar *docker-bridge-address*, *dns-service-ip* y *service-cidr* en función de la configuración.
> 
> Si ha creado una identidad propia para la identidad de kubelet, use el parámetro *assign-kubelet-identity* para especificar la identidad.

```azurecli
DNS_SERVER=<IP address of DNS server>
ROOT_DOMAIN_NAME="contoso.com"

az aks create \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --vm-set-type VirtualMachineScaleSets \
    --network-plugin azure \
    --load-balancer-sku standard \
    --windows-admin-username $WINDOWS_USERNAME \
    --enable-managed-identity \
    --enable-windows-gmsa \
    --gmsa-dns-server $DNS_SERVER \
    --gmsa-root-domain-name $ROOT_DOMAIN_NAME

az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKS \
    --os-type Windows \
    --name npwin \
    --node-count 1    
```

También puede habilitar GMSA en clústeres existentes que ya tienen habilitados nodos de Windows Server e identidades administradas mediante `az aks update`. Por ejemplo:

```azurecli
az aks update \
    --resource-group MyResourceGroup \
    --name MyAKS \
    --enable-windows-gmsa \
    --gmsa-dns-server $DNS_SERVER \
    --gmsa-root-domain-name $ROOT_DOMAIN_NAME
```

Después de crear el clúster o de actualizarlo, use `az keyvault set-policy` para conceder a la identidad acceso al almacén de claves. En el ejemplo siguiente se concede a la identidad de kubelet creada por el clúster acceso al almacén de claves *MyAKSGMSAVault*.

> [!NOTE]
> Si ha proporcionado una identidad propia para la identidad de kubelet, omita este paso.

```azurecli
MANAGED_ID=$(az aks show -g MyResourceGroup -n MyAKS --query "identityProfile.kubeletidentity.objectId" -o tsv)

az keyvault set-policy --name "MyAKSGMSAVault" --object-id $MANAGED_ID --secret-permissions get
```

## <a name="install-gmsa-cred-spec"></a>Instalación de especificaciones de credenciales de GMSA

Para configurar `kubectl` para conectarse a su clúster de Kubernetes, use el comando [az aks get-credentials][]. En el ejemplo siguiente se obtienen las credenciales del clúster de AKS llamado *MyAKS* en *MyResourceGroup*:

```azurecli
az aks get-credentials --resource-group MyResourceGroup --name MyAKS
```

Cree un archivo *gmsa-spec.yaml* con lo siguiente, y reemplace los marcadores de posición por valores propios.

```yml
apiVersion: windows.k8s.io/v1alpha1
kind: GMSACredentialSpec
metadata:
  name: aks-gmsa-spec  # This name can be changed, but it will be used as a reference in the pod spec
credspec:
  ActiveDirectoryConfig:
    GroupManagedServiceAccounts:
    - Name: $GMSA_ACCOUNT_USERNAME
      Scope: $NETBIOS_DOMAIN_NAME
    - Name: $GMSA_ACCOUNT_USERNAME
      Scope: $DNS_DOMAIN_NAME
    HostAccountConfig:
      PluginGUID: '{CCC2A336-D7F3-4818-A213-272B7924213E}'
      PortableCcgVersion: "1"
      PluginInput: ObjectId=$MANAGED_ID;SecretUri=$SECRET_URI  # SECRET_URI takes the form https://$akvName.vault.azure.net/secrets/$akvSecretName
  CmsPlugins:
  - ActiveDirectory
  DomainJoinConfig:
    DnsName: $DNS_DOMAIN_NAME
    DnsTreeName: $DNS_ROOT_DOMAIN_NAME
    Guid:  $AD_DOMAIN_OBJECT_GUID
    MachineAccountName: $GMSA_ACCOUNT_USERNAME
    NetBiosName: $NETBIOS_DOMAIN_NAME
    Sid: $GMSA_SID
```

Cree un archivo *gmsa-role.yaml* con lo siguiente.

```yml
#Create the Role to read the credspec
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRole
metadata:
  name: aks-gmsa-role
rules:
- apiGroups: ["windows.k8s.io"]
  resources: ["gmsacredentialspecs"]
  verbs: ["use"]
  resourceNames: ["aks-gmsa-spec"]
```

Cree un archivo *gmsa-role-binding.yaml* con lo siguiente.

```yml
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  name: allow-default-svc-account-read-on-aks-gmsa-spec
  namespace: default
subjects:
- kind: ServiceAccount
  name: default
  namespace: default
roleRef:
  kind: ClusterRole
  name: aks-gmsa-role
  apiGroup: rbac.authorization.k8s.io
```

Use `kubectl apply` para aplicar los cambios de *gmsa-spec.yaml*, *gmsa-role.yaml* y *gmsa-role-binding.yaml*.

```azurecli
kubectl apply -f gmsa-spec.yaml
kubectl apply -f gmsa-role.yaml
kubectl apply -f gmsa-role-binding.yaml
```

## <a name="verify-gmsa-is-installed-and-working"></a>Comprobación de la instalación y el funcionamiento de GMSA

Cree un archivo *gmsa-demo.yaml* con lo siguiente.

```yml
---
kind: ConfigMap
apiVersion: v1
metadata:
  labels:
   app: gmsa-demo
  name: gmsa-demo
  namespace: default
data:
  run.ps1: |
   $ErrorActionPreference = "Stop"

   Write-Output "Configuring IIS with authentication."

   # Add required Windows features, since they are not installed by default.
   Install-WindowsFeature "Web-Windows-Auth", "Web-Asp-Net45"

   # Create simple ASP.Net page.
   New-Item -Force -ItemType Directory -Path 'C:\inetpub\wwwroot\app'
   Set-Content -Path 'C:\inetpub\wwwroot\app\default.aspx' -Value 'Authenticated as <B><%=User.Identity.Name%></B>, Type of Authentication: <B><%=User.Identity.AuthenticationType%></B>'

   # Configure IIS with authentication.
   Import-Module IISAdministration
   Start-IISCommitDelay
   (Get-IISConfigSection -SectionPath 'system.webServer/security/authentication/windowsAuthentication').Attributes['enabled'].value = $true
   (Get-IISConfigSection -SectionPath 'system.webServer/security/authentication/anonymousAuthentication').Attributes['enabled'].value = $false
   (Get-IISServerManager).Sites[0].Applications[0].VirtualDirectories[0].PhysicalPath = 'C:\inetpub\wwwroot\app'
   Stop-IISCommitDelay

   Write-Output "IIS with authentication is ready."

   C:\ServiceMonitor.exe w3svc
---
apiVersion: apps/v1
kind: Deployment
metadata:
  labels:
    app: gmsa-demo
  name: gmsa-demo
  namespace: default
spec:
  replicas: 1
  selector:
    matchLabels:
      app: gmsa-demo
  template:
    metadata:
      labels:
        app: gmsa-demo
    spec:
      securityContext:
        windowsOptions:
          gmsaCredentialSpecName: aks-gmsa-spec
      containers:
      - name: iis
        image: mcr.microsoft.com/windows/servercore/iis:windowsservercore-ltsc2019
        imagePullPolicy: IfNotPresent
        command:
         - powershell
        args:
          - -File
          - /gmsa-demo/run.ps1
        volumeMounts:
          - name: gmsa-demo
            mountPath: /gmsa-demo
      volumes:
      - configMap:
          defaultMode: 420
          name: gmsa-demo
        name: gmsa-demo
      nodeSelector:
        kubernetes.io/os: windows
---
apiVersion: v1
kind: Service
metadata:
  labels:
    app: gmsa-demo
  name: gmsa-demo
  namespace: default
spec:
  ports:
  - port: 80
    targetPort: 80
  selector:
    app: gmsa-demo
  type: LoadBalancer
```

Use `kubectl apply` para aplicar los cambios de *gmsa-demo.yaml*.

```azurecli
kubectl apply -f gmsa-demo.yaml
```

Use `kubectl get service` para mostrar la dirección IP de la aplicación de ejemplo.

```console
kubectl get service gmsa-demo --watch
```

En un primer momento, el parámetro *EXTERNAL-IP* del servicio *gmsa-demo* se muestra como *pendiente*.

```output
NAME               TYPE           CLUSTER-IP   EXTERNAL-IP   PORT(S)        AGE
gmsa-demo          LoadBalancer   10.0.37.27   <pending>     80:30572/TCP   6s
```

Cuando la dirección *EXTERNAL-IP* cambie de *pendiente* a una dirección IP pública real, use `CTRL-C` para detener el proceso de inspección de `kubectl`. En la salida del ejemplo siguiente se muestra una dirección IP pública válida asignada al servicio:

```output
gmsa-demo  LoadBalancer   10.0.37.27   EXTERNAL-IP   80:30572/TCP   2m
```

Para comprobar que GMSA funciona y se ha configurado correctamente, abra un explorador web en la dirección IP externa del servicio *gmsa-demo*. Autentíquese con `$NETBIOS_DOMAIN_NAME\$AD_USERNAME` y la contraseña, y confirme que ve `Authenticated as $NETBIOS_DOMAIN_NAME\$AD_USERNAME, Type of Authentication: Negotiate`.

## <a name="troubleshooting"></a>Solución de problemas

### <a name="no-authentication-is-prompted-when-loading-the-page"></a>No se solicita autenticación al cargar la página

Si se carga la página, pero no se le pide que se autentique, use `kubelet logs POD_NAME` para mostrar los registros del pod y comprobar que ve *IIS con autenticación está listo*.

### <a name="connection-timeout-when-trying-to-load-the-page"></a>Tiempo de espera de conexión al intentar cargar la página

Si se agota el tiempo de espera de conexión al intentar cargar la página, ejecute `kubectl get pods --watch` para comprobar la aplicación de ejemplo. A veces, la dirección IP externa del servicio de aplicación de ejemplo está disponible antes de que se ejecute el pod de la aplicación de ejemplo.

### <a name="pod-fails-to-start-and-an-winapi-error-shows-in-the-pod-events"></a>El pod no se inicia y se muestra un *error winapi* en los eventos del pod

Después de ejecutar `kubectl get pods --watch` y esperar varios minutos, si el pod no se inicia, ejecute `kubectl describe pod POD_NAME`. Si ve un *error winapi* en los eventos de pod, es probable que se trate de un error en la configuración de la especificación de credenciales de GMSA. Compruebe que todos los valores de reemplazo de *gmsa-spec.yaml* sean correctos, vuelva a ejecutar `kubectl apply -f gmsa-spec.yaml` y vuelva a implementar la aplicación de ejemplo.


[aks-cni]: configure-azure-cni.md
[aks-managed-id]: use-managed-identity.md
[aks-managed-id-kubelet]: use-managed-identity.md#summary-of-managed-identities
[az aks get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[gmsa-getting-started]: /windows-server/security/group-managed-service-accounts/getting-started-with-group-managed-service-accounts
[gmsa-overview]: /windows-server/security/group-managed-service-accounts/group-managed-service-accounts-overview
[rdp]: rdp.md