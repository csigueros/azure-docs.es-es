---
title: Habilitación de un registro de contenedor de Edge en un dispositivo de Azure Stack Edge Pro con GPU
description: Se describe cómo habilitar un registro de contenedor de Edge local en un dispositivo de Azure Stack Edge Pro con GPU.
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 18fa68b6d0d0922bad0a632ba10bd82a8cfe8506
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128643030"
---
# <a name="enable-edge-container-registry-on-your-azure-stack-edge-pro-gpu-device"></a>Habilitación de un registro de contenedor de Edge en un dispositivo de Azure Stack Edge Pro con GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En este artículo se describe cómo habilitar el registro de contenedor de Edge y usarlo desde el clúster de Kubernetes en el dispositivo de Azure Stack Edge Pro. En el ejemplo que se usa en el artículo se detalla cómo se inserta una imagen de un registro de origen, en este caso, Microsoft Container Registry, en el registro del dispositivo de Azure Stack Edge, el registro de contenedor de Edge.

### <a name="about-edge-container-registry"></a>Acerca del registro de contenedor de Edge

Las aplicaciones de proceso de contenedor se ejecutan en imágenes de contenedor y estas imágenes se almacenan en registros. Los registros pueden ser públicos, como Docker Hub, privados o administrados por un proveedor de nube, como Azure Container Registry. Para obtener más información, consulte [Acerca de los registros, repositorios e imágenes](../container-registry/container-registry-concepts.md).

Un registro de contenedor de Edge proporciona un repositorio en Edge, en el dispositivo de Azure Stack Edge Pro. Puede usar este registro para almacenar y administrar las imágenes de contenedor privadas.

En un entorno de varios nodos, las imágenes de contenedor se pueden descargar e insertar en el registro de contenedor de Edge una vez. Todas las aplicaciones de Edge pueden usar el registro de contenedor de Edge para las implementaciones posteriores.


## <a name="prerequisites"></a>Requisitos previos

Antes de comenzar, asegúrese de que:

1. Tiene acceso a un dispositivo Azure Stack Edge Pro.

1. Ha activado un dispositivo Azure Stack Edge Pro como se describe en [Activación de Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Ha habilitado el rol de proceso en el dispositivo. También se ha creado un clúster de Kubernetes en el dispositivo al configurar el proceso en el dispositivo según las instrucciones de [Configuración del proceso en un dispositivo Azure Stack Edge Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Tiene el punto de conexión de la API de Kubernetes de la página **Dispositivo** de la interfaz de usuario web local. Para obtener más información, consulte las instrucciones en [Obtención de los puntos de conexión de Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Tiene acceso a un sistema cliente con un [sistema operativo compatible](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). Si usa un cliente Windows, el sistema debe ejecutar PowerShell 5.0 o posterior para acceder al dispositivo.

    1. Si quiere extraer e insertar sus propias imágenes de contenedor, asegúrese de que el sistema tiene instalado el cliente de Docker. Si usa un cliente Windows, [instale el escritorio de Docker en Windows](https://docs.docker.com/docker-for-windows/install/).  


## <a name="enable-container-registry-as-add-on"></a>Habilitación de un registro de contenedor como complemento

El primer paso es habilitar el registro de contenedor de Edge como complemento.

1. [Conexión a la interfaz de PowerShell del dispositivo](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface). 

1. Para habilitar el registro de contenedor como complemento, escriba: 

    `Set-HcsKubernetesContainerRegistry`
    
    Esta operación puede tardar varios minutos en completarse.

    Esta es la salida de ejemplo de este comando:    
            
    ```powershell
    [10.128.44.40]: PS>Set-HcsKubernetesContainerRegistry
    Operation completed successfully. Use Get-HcsKubernetesContainerRegistryInfo for credentials    
    ```
            
1. Para obtener los detalles del registro de contenedor, escriba:

    `Get-HcsKubernetesContainerRegistryInfo`

    Esta es la salida de ejemplo de este comando:  
    
    ```powershell
    [10.128.44.40]: PS> Get-HcsKubernetesContainerRegistryInfo
                
    Endpoint                                   IPAddress    Username     Password
    --------                                   ---------    --------     --------
    ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 10.128.44.41 ase-ecr-user i3eTsU4zGYyIgxV
    ```    

1. Anote el nombre de usuario y la contraseña de la salida de `Get-HcsKubernetesContainerRegistryInfo`. Estas credenciales se usan para iniciar sesión en el registro de contenedor de Edge mientras se insertan imágenes.            


## <a name="manage-container-registry-images"></a>Administración de imágenes del registro de contenedor

Puede que quiera tener acceso al registro de contenedor desde fuera del dispositivo de Azure Stack Edge. También puede que quiera insertar o extraer imágenes en el registro.

Siga estos pasos para obtener acceso al registro de contenedor de Edge:

1. Obtenga los detalles del punto de conexión para el registro de contenedor de Edge.
    1. En la interfaz de usuario local, vaya a **Dispositivo**.
    1. Busque el **punto de conexión del registro de contenedor de Edge**.
        ![Punto de conexión del registro de contenedor de Edge en la página Dispositivo](media/azure-stack-edge-gpu-edge-container-registry/get-edge-container-registry-endpoint-1.png) 
    1. Copie este punto de conexión y cree una entrada DNS correspondiente en el archivo `C:\Windows\System32\Drivers\etc\hosts` del cliente para conectarse al punto de conexión del registro de contenedor de Edge. 

        \<IP address of the Kubernetes main node\>    \<Edge container registry endpoint\> 
        
        ![Adición de entrada DNS para el punto de conexión del registro de contenedor de Edge](media/azure-stack-edge-gpu-edge-container-registry/add-domain-name-service-entry-hosts-1.png)    

1. Descargue el certificado de registro de contenedor de Edge desde la interfaz de usuario local. 
    1. En la interfaz de usuario local, vaya a **Certificados**.
    1. Busque la entrada para el **certificado de registro de contenedor de Edge**. A la derecha de esta entrada, seleccione **Descargar** para descargar el certificado de registro de contenedor de Edge en el sistema cliente que usará para tener acceso al dispositivo. 

        ![Descarga del certificado del punto de conexión del registro de contenedor de Edge](media/azure-stack-edge-gpu-edge-container-registry/download-edge-container-registry-endpoint-certificate-1.png)  

1. Instale el certificado descargado en el cliente. Si usa un cliente Windows, siga estos pasos: 
    1. Seleccione el certificado y, en el **Asistente para importar certificados**, seleccione la ubicación del almacén como **Máquina local**. 

        ![Instalación del certificado 1](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-1.png) 
    
    1. Instale el certificado en la máquina local del almacén raíz de confianza. 

        ![Instalación del certificado 2](media/azure-stack-edge-gpu-edge-container-registry/install-certificate-2.png) 

1. Una vez instalado el certificado, reinicie el cliente de Docker en el sistema.

1. Inicie sesión en el registro de contenedor de Edge. Escriba:

    `docker login <Edge container registry endpoint> -u <username> -p <password>`

    Proporcione el punto de conexión del registro de contenedor de Edge en la página **Dispositivos** y el nombre de usuario y la contraseña que ha obtenido de la salida de `Get-HcsKubernetesContainerRegistryInfo`. 

1. Use los comandos de inserción o extracción de Docker para insertar o extraer imágenes de contenedor desde el registro de contenedor.
 
    1. Extraiga una imagen de la imagen del registro de contenedor de Microsoft. Escriba:
        
        `docker pull <Full path to the container image in the Microsoft Container Registry>`
       
    1. Cree un alias de la imagen que extrajo con la ruta de acceso completa al registro.

        `docker tag <Path to the image in the Microsoft container registry> <Path to the image in the Edge container registry/Image name with tag>`

    1. Inserte la imagen en el registro.
    
        `docker push <Path to the image in the Edge container registry/Image name with tag>`

    1. Ejecute la imagen insertada en el registro.
    
        `docker run -it --rm -p 8080:80 <Path to the image in the Edge container registry/Image name with tag>`

        Esta es una salida de ejemplo de los comandos de inserción y extracción:

        ```powershell
        PS C:\WINDOWS\system32> docker login ecr.dbe-hw6h1t2.microsoftdatabox.com:31001 -u ase-ecr-user -p 3bbo2sOtDe8FouD
        WARNING! Using --password via the CLI is insecure. Use --password-stdin.
        Login Succeeded
        PS C:\WINDOWS\system32> docker pull mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        1.17.5-alpine: Pulling from oss/nginx/nginx
        Digest: sha256:5466bbc0a989bd1cd283c0ba86d9c2fc133491ccfaea63160089f47b32ae973b
        Status: Image is up to date for mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine
        PS C:\WINDOWS\system32> docker tag mcr.microsoft.com/oss/nginx/nginx:1.17.5-alpine ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        PS C:\WINDOWS\system32> docker push ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        The push refers to repository [ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx]
        bba7d2385bc1: Pushed
        77cae8ab23bf: Pushed
        2.0: digest: sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff size: 739
        PS C:\WINDOWS\system32> docker run -it --rm -p 8080:80 ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        2020/11/10 00:00:49 [error] 6#6: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:8080", referrer: "http://localhost:8080/"
        172.17.0.1 - - [10/Nov/2020:00:00:49 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:8080/" "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36" "-"
        ^C
        PS C:\WINDOWS\system32>    
        ```
    
1. Vaya a `http://localhost:8080` para ver el contenedor en ejecución. En este caso, verá el servidor web nginx en ejecución.

    ![Vista del contenedor en ejecución](media/azure-stack-edge-gpu-edge-container-registry/view-running-container-1.png)

    Para detener y quitar el contenedor, presione `Control+C`.

 

## <a name="use-edge-container-registry-images-via-kubernetes-pods"></a>Uso de imágenes de registro de contenedor de Edge a través de pods de Kubernetes

Ya puede implementar la imagen que insertó en el registro de contenedor de Edge desde dentro de los pods de Kubernetes.

1. Para implementar la imagen, debe configurar el acceso al clúster mediante *kubectl*. Cree un espacio de nombres y un usuario, conceda al usuario acceso al espacio de nombres y obtenga un archivo de *configuración*. Asegúrese de que puede conectarse a los pods de Kubernetes. 
    
    Siga todos los pasos que se indican en [Conexión y administración de un clúster de Kubernetes mediante kubectl en un dispositivo de Azure Stack Edge Pro con GPU](azure-stack-edge-gpu-create-kubernetes-cluster.md). 

    Esta es una salida de ejemplo de un espacio de nombres en el dispositivo desde el que el usuario puede tener acceso al clúster de Kubernetes.

    ```powershell
    [10.128.44.40]: PS>New-HcsKubernetesNamespace -Namespace myecr
    [10.128.44.40]: PS>New-HcsKubernetesUser -UserName ecruser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01URXdOVEF6TkRJek1Gb1hEVE13TVRFd016QXpOREl6TUZvd0ZURVRNQkVnNjOVRLWndCQ042cm1XQms2eXFwcXI1MUx6bApTaXMyTy91UEJ2YXNSSUUzdzgrbmEwdG1aTERZZ2F6MkQwMm42Q29mUmtyUTR2d1lLTnR1MlpzR3pUdz0KLS0tLS1FTkQgQ0VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.dbe-hw6h1t2.microsoftdatabox.com:6443
        name: kubernetes
        ===================CUT=========================================CUT==============
        client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJYmVWRGJSTzZ3ell3RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURFeE1EVXdNelF5TXpCYUZ3MHlNVEV4TURreU16UTRNal
        ===================CUT=========================================CUT==============
        DMVUvN3lFOG5UU3k3b2VPWitUeHdzCjF1UDByMjhDZ1lCdHdRY0ZpcFh1blN5ak16dTNIYjhveFI2V3VWWmZldFFKNElKWEFXOStVWGhKTFhyQ2x4bUcKWHRtbCt4UU5UTzFjQVNKRVZWVDd6Tjg2ay9kSU43S3JIVkdUdUxlUDd4eGVjV2VRcWJrZEVScUsxN0liTXpiVApmbnNxc0dobEdmLzdmM21kTGtyOENrcWs5TU5aM3MvUVIwRlFCdk94ZVpuUlpTeDVGbUR5S1E9PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=

    [10.128.44.40]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myecr -UserName ecruser
    [10.128.44.40]: PS>kubectl get pods -n "myecr"
    No resources found.
    PS C:\WINDOWS\system32>
    ```  

2. Los secretos de extracción de imágenes ya están establecidos en todos los espacios de nombres de Kubernetes en el dispositivo. Puede obtener los secretos con el comando `get secrets`. Este es una salida de ejemplo:

    ```powershell
    PS C:\WINDOWS\system32> .\kubectl.exe get secrets -n myecr
    NAME                  TYPE                                  DATA   AGE
    ase-ecr-credentials   kubernetes.io/dockerconfigjson        1      99m
    default-token-c7kww   kubernetes.io/service-account-token   3      107m
    sec-smbcredentials    microsoft.com/smb                     2      99m
    PS C:\WINDOWS\system32>   
    ```    

3. Implemente un pod en el espacio de nombres mediante kubectl. Use el siguiente `yaml`. 

    Reemplace la imagen `<image-name>` por la imagen insertada en el registro de contenedor. Consulte los secretos de los espacios de nombres con imagePullSecrets con un nombre: `ase-ecr-credentials`.
    
    ```yml
    apiVersion: v1
    kind: Pod
    metadata:
      name: nginx
    spec:
      containers:
      - name: nginx
        image: ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/nginx:2.0
        imagePullPolicy: Always
      imagePullSecrets:
      - name: ase-ecr-credentials
    ```

4. Aplique la implementación en el espacio de nombres que creó con el comando de aplicación. Compruebe que el contenedor se está ejecutando. Este es una salida de ejemplo:
   
    ```yml
    PS C:\Windows\System32> .\kubectl.exe apply -f .\deployment.yml -n myecr
    pod/nginx configured
    PS C:\Windows\System32> .\kubectl.exe get pods -n myecr
    NAME    READY   STATUS    RESTARTS   AGE
    nginx   1/1     Running   0          27m
    PS C:\Windows\System32>
    ```

## <a name="delete-container-registry-images"></a>Eliminación de imágenes del registro de contenedor

El almacenamiento del registro de contenedor de Edge se hospeda en un recurso compartido local dentro del dispositivo de Azure Stack Edge Pro, que está limitado por el almacenamiento disponible en el dispositivo. Es su responsabilidad eliminar las imágenes de Docker no utilizadas del registro de contenedor mediante la API de Docker HTTP v2 (https://docs.docker.com/registry/spec/api/) ).  

Para quitar una o varias imágenes de contenedor, siga estos pasos:

1. Establezca el nombre de la imagen en la imagen que quiere eliminar.

    ```powershell
    PS C:\WINDOWS\system32> $imageName="nginx"    
    ```

1. Establezca el nombre de usuario y la contraseña del registro de contenedor como credencial de PS.

    ```powershell
    PS C:\WINDOWS\system32> $username="ase-ecr-user"
    PS C:\WINDOWS\system32> $password="3bbo2sOtDe8FouD"
    PS C:\WINDOWS\system32> $securePassword = ConvertTo-SecureString $password -AsPlainText -Force
    PS C:\WINDOWS\system32> $credential = New-Object System.Management.Automation.PSCredential ($username, $securePassword)    
    ```

1. Enumere las etiquetas asociadas a la imagen.

    ```powershell
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/nginx/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32> $tags = Invoke-RestMethod -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/tags/list" | Select-Object -ExpandProperty tags
    PS C:\WINDOWS\system32> $tags
    2.0
    PS C:\WINDOWS\system32>    
    ```
  
1. Enumere el hash asociado a la etiqueta que quiere eliminar. Utiliza $tags de la salida del comando anterior. Si tiene varias etiquetas, seleccione una de ellas y úsela en el siguiente comando.

    ```powershell
    PS C:\WINDOWS\system32> $response = Invoke-WebRequest -Method Head -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$tags" -Headers @{ 'Accept' = 'application/vnd.docker.distribution.manifest.v2+json' }
    PS C:\WINDOWS\system32> $digest = $response.Headers['Docker-Content-Digest']
    PS C:\WINDOWS\system32> $digest
    sha256:b4c0378c841cd76f0b75bc63454bfc6fe194a5220d4eab0d75963bccdbc327ff
    PS C:\WINDOWS\system32>    
    ```
1. Elimine la imagen con el hash de image:tag.

    ```powershell
    PS C:\WINDOWS\system32> Invoke-WebRequest -Method Delete -Credential $credential -Uri "https://ecr.dbe-hw6h1t2.microsoftdatabox.com:31001/v2/$imageName/manifests/$digest" | Select-Object -ExpandProperty StatusDescription    
    ```

Después de eliminar las imágenes no utilizadas, un proceso que se ejecuta cada noche recupera automáticamente el espacio asociado a las imágenes sin referencia. 

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de una aplicación sin estado en Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).