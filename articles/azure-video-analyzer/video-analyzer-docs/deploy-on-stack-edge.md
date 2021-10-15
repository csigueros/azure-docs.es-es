---
title: Implementación de Azure Video Analyzer en Azure Stack Edge
description: En este artículo se indican los pasos que ayudan a implementar Azure Video Analyzer en Azure Stack Edge.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: da14368846cd87d5d4e231933cec0068a4e558f9
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129546630"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>Implementación de Azure Video Analyzer en Azure Stack Edge

En este artículo se indican los pasos que ayudan a implementar Video Analyzer en Azure Stack Edge. Una vez configurado y activado el dispositivo, está listo para la implementación de Video Analyzer. 

En lo que respecta a Video Analyzer, se implementa mediante IoT Hub, aunque los recursos de Azure Stack Edge exponen una API de Kubernetes que permite al cliente implementar soluciones adicionales no compatibles con IoT Hub que pueden interactuar con Video Analyzer. 

> [!TIP]
> El uso de la API de Kubernetes (K8s) para la implementación personalizada es un caso avanzado. Se recomienda que el cliente cree módulos perimetrales e implemente mediante IoT Hub para cada recurso de Azure Stack Edge en lugar de usar la API de Kubernetes. En este artículo se muestran los pasos necesarios para implementar el módulo Video Analyzer mediante IoT Hub.

## <a name="prerequisites"></a>Requisitos previos

* Cuenta de Video Analyzer

    Este [servicio en la nube](./overview.md) se usa para registrar el módulo perimetral Video Analyzer y para reproducir vídeo grabado y análisis de vídeo.
* Identidad administrada

    Es la [identidad administrada](../../active-directory/managed-identities-azure-resources/overview.md) asignada por el usuario que se usa para administrar el acceso a la cuenta de almacenamiento anterior.
* Un recurso de [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md).
* [Una instancia de IoT Hub](../../iot-hub/iot-hub-create-through-portal.md).
* Cuenta de almacenamiento

    Se recomienda usar cuentas de almacenamiento V2 de uso general (GPv2).  
    Obtenga más información sobre las [cuentas de almacenamiento v2 de uso general](../../storage/common/storage-account-upgrade.md?tabs=azure-portal).
* [Visual Studio Code](https://code.visualstudio.com/) en la máquina de desarrollo. Asegúrese de tener la [extensión Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Asegúrese de que la red a la que está conectada la máquina de desarrollo permita Advanced Message Queueing Protocol a través del puerto 5671. Esta configuración permite a Azure IoT Tools comunicarse con Azure IoT Hub.

## <a name="configuring-azure-stack-edge-for-using-video-analyzer"></a>Configuración de Azure Stack Edge para usar Video Analyzer

Azure Stack Edge es una solución de hardware como servicio y un dispositivo informático perimetral habilitado para inteligencia artificial que cuenta con capacidades de transferencia de datos de red. Lea más sobre [Azure Stack Edge y vea instrucciones de configuración detalladas](../../databox-online/azure-stack-edge-gpu-deploy-prep.md). Para comenzar, siga las instrucciones de los vínculos siguientes:

* [Creación de recursos de Data Box Gateway o Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource)
* [Instalación y configuración](../../databox-online/azure-stack-edge-gpu-deploy-install.md)
* Conexión y activación

    1. [Conexión](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
    2. [Configuración de la red](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
    3. [Configuración de dispositivos](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
    4. [Configurar certificados](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
    5. [Activar](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
* [Asociación de una instancia de IoT Hub a Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Habilitación de los requisitos previos de proceso en la interfaz de usuario local de Azure Stack Edge

Antes de continuar, asegúrese de que:

* Ha activado el recurso de Azure Stack Edge.
* Tiene acceso a un sistema cliente Windows con PowerShell 5.0 o posterior para acceder al recurso de Azure Stack Edge.
* Para implementar un clúster de Kubernetes, debe configurar el recurso de Azure Stack Edge mediante su [interfaz de usuario web local](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup). 

    * Conecte y configure:
    
        1. [Conexión](../../databox-online/azure-stack-edge-gpu-deploy-connect.md)
        2. [Configuración de la red](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md)
        3. [Configuración de dispositivos](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)
        4. [Configurar certificados](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md)
        5. [Activar](../../databox-online/azure-stack-edge-gpu-deploy-activate.md)
    * Para habilitar el proceso, vaya a la página Proceso de la interfaz de usuario web local del dispositivo.
    
        * Seleccione la interfaz de red que quiere habilitar para el proceso. Seleccione Habilitar. Al hacerlo, se crea un conmutador virtual para esa interfaz de red en el dispositivo.
        * Deje en blanco las direcciones IP de los nodos de prueba de Kubernetes y las de los servicios externos de Kubernetes.
        * Seleccione Aplicar: esta operación tarda unos 2 minutos.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text="Requisitos previos de proceso en la interfaz de usuario local de Azure Stack Edge":::

        * Si el DNS no está configurado para la API de Kubernetes y el recurso de Azure Stack Edge, puede actualizar el archivo host de Windows.
        
            * Abra un editor de texto como Administrador
            * Abra el archivo "to C:\Windows\System32\drivers\etc\hosts"
            * Agregue la IPv4 y el nombre de host del nombre de dispositivo de la API de Kubernetes al archivo. (Esta información puede encontrarse en el portal de Azure Stack Edge, en la sección Dispositivos).
            * Guardar y cerrar

### <a name="deploy-video-analyzer-edge-modules-using-azure-portal"></a>Implementación de módulos de Video Analyzer Edge mediante Azure Portal

Azure Portal le guía en la creación de un manifiesto de implementación y la inserción de la implementación en un dispositivo de IoT Edge.  
#### <a name="select-your-device-and-set-modules"></a>Selección del dispositivo y establecimiento de módulos

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com/) y vaya a IoT Hub.
1. Seleccione **IoT Edge** en el menú.
1. Haga clic en el identificador del dispositivo de destino en la lista de dispositivos.
1. Seleccione **Set modules** (Establecer módulos).

#### <a name="configure-a-deployment-manifest"></a>Configuración de un manifiesto de implementación

Un manifiesto de implementación es un documento JSON que describe qué módulos se van a implementar, cómo fluyen los datos entre los módulos y las propiedades deseadas de los módulos gemelos. Azure Portal cuenta con un asistente que le guía en la creación de un manifiesto de implementación. Presenta tres pasos organizados en pestañas: **Módulos**, **Rutas** y **Revisar y crear**.

#### <a name="add-modules"></a>Adición de módulos

1. En la sección **Módulos IoT Edge** de la página, haga clic en la lista desplegable **Agregar** y seleccione **Agregar módulo IoT Edge** para mostrar la página **Agregar módulo IoT Edge**.
1. En la pestaña **Configuración del módulo**, proporcione un nombre para el módulo y especifique el URI de imagen del contenedor:   
    Ejemplos:
    
    * **Nombre del módulo IoT Edge**: avaedge
    * **URI de la imagen**: mcr.microsoft.com/media/video-analyzer:1    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="Captura de pantalla que muestra la pestaña Configuración del módulo.":::
    
    > [!TIP]
    > No seleccione **Agregar** hasta que haya especificado los valores en las pestañas **Configuración del módulo**, **Opciones de creación del contenedor** y **Configuración de módulos gemelos**, tal como se describe en este procedimiento.
    
    > [!WARNING]
    > Azure IoT Edge distingue mayúsculas de minúsculas al realizar llamadas a los módulos. Tome nota de la cadena exacta que utilice como nombre de módulo.

1. Abra la pestaña **Variables de entorno**.
   
   Agregue los siguientes valores en los cuadros de entrada que vea

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="Variables de entorno":::

1. Abra la pestaña **Opciones de creación del contenedor**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="Opciones de creación del contenedor":::
 
    Copie y pegue el siguiente código JSON en el cuadro para limitar el tamaño de los archivos de registro que genera el módulo.
    
    ```    
    {
        "HostConfig": {
            "LogConfig": {
                "Type": "",
                "Config": {
                    "max-size": "10m",
                    "max-file": "10"
                }
            },
            "Binds": [
               "/var/lib/videoanalyzer/:/var/lib/videoanalyzer",
               "/var/media:/var/media"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
        }
    }
    ````
   
   La sección "Enlaces" del archivo JSON tiene 2 entradas:
   1. "/var/lib/videoanalyzer:/var/lib/videoanalyzer": se usa para enlazar los datos de configuración persistentes de la aplicación desde el contenedor y almacenarlos en el dispositivo perimetral.
   1. "/var/media:/var/media": esto enlaza las carpetas de elementos multimedia entre el dispositivo perimetral y el contenedor. Se usa para almacenar las grabaciones de vídeo cuando se ejecuta una pipelineTopology que admite el almacenamiento de clips de vídeo en el dispositivo perimetral.
   
1. En la pestaña **Configuración de módulos gemelos**, copie el siguiente código JSON y péguelo en el cuadro.
 
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="Configuración de gemelos":::

    Azure Video Analyzer requiere un conjunto de propiedades gemelas obligatorias para poder ejecutarse, tal como se muestra en [Esquema de configuración de módulos gemelos](module-twin-configuration-schema.md).  

    El código JSON que se debe especificar en el cuadro de edición de Configuración de módulos gemelos tendrá el siguiente aspecto:    
    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
    }
    ```
    Más abajo se muestran algunas propiedades adicionales **recomendadas** que se pueden agregar al código JSON y ayudarán a supervisar el módulo. Para obtener más información, vea [Supervisión y registro](monitor-log-edge.md):
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. Seleccione **Agregar**.  

Agregue el módulo perimetral del simulador RTSP.

1. En la sección **Módulos IoT Edge** de la página, haga clic en la lista desplegable **Agregar** y seleccione **Agregar módulo IoT Edge** para mostrar la página **Agregar módulo IoT Edge**.
1. En la pestaña **Configuración del módulo**, proporcione un nombre para el módulo y especifique el URI de imagen del contenedor:   
    Ejemplos:
    
    * **Nombre del módulo IoT Edge**: rtspsim
    * **URI de imagen**: mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2  


1. Abra la pestaña **Opciones de creación del contenedor**.
 
    Copie y pegue el siguiente código JSON en el cuadro de texto.
    
    ```
    {
        "HostConfig": {
            "Binds": [
               "/home/localedgeuser/samples/input/:/live/mediaServer/media/"
            ],
            "PortBindings": {
                    "554/tcp": [
                        {
                        "HostPort": "554"
                        }
                    ]
            }
        }
    }
    ```
1. Seleccione **Agregar**.  

1. Seleccione **Siguiente: Rutas** para continuar con la sección de rutas. Especifique las rutas.

    En NAME, escriba **AVAToHub** y, en VALUE, escriba **FROM /messages/modules/avaedge/outputs/ INTO $upstream**
1. Después, seleccione **Revisar y crear** para continuar en la sección de revisión.
1. Revise la información de implementación y seleccione **Crear** para implementar el módulo.

    > [!TIP]
    > Siga estos pasos para generar el token de aprovisionamiento:
1. Abra Azure Portal y vaya a Video Analyzer.
1. En el panel de navegación izquierdo, haga clic en **Módulos perimetrales**.
1. Seleccione el módulo perimetral y haga clic en el botón **Generar token**:

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="Generación de token" lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::
1. Copie el token de aprovisionamiento:

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="Copia de token":::



#### <a name="optional-setup-docker-volume-mounts"></a>(Opcional) Configuración de montajes de volumen de Docker

Si quiere ver los datos de los directorios de trabajo, siga estos pasos para configurar montajes de volumen de Docker antes de la implementación. 

En estos pasos se explica cómo crear un usuario de puerta de enlace y configurar recursos compartidos de archivos para ver el contenido del directorio de trabajo de Video Analyzer y la carpeta multimedia de Video Analyzer.

> [!NOTE]
> Se admiten montajes de enlace, pero los montajes de volumen permiten que se vean los datos y, si se quiere, que se copien de forma remota. Es posible usar montajes de enlace y de volumen, pero no pueden apuntar a la misma ruta de acceso de contenedor.

1. Abra Azure Portal y vaya al recurso de Azure Stack Edge.
1. Cree un **Usuario de puerta de enlace** que pueda acceder a recursos compartidos.
    
    1. En el panel de navegación izquierdo, haga clic en **Cloud storage gateway** (Puerta de enlace de almacenamiento en la nube).
    1. Haga clic en **Users** (Usuarios) en el panel de navegación izquierdo.
    1. Haga clic en el icono **+ Add User** (+ Agregar usuario) para establecer el nombre de usuario y la contraseña. (Recomendado: `avauser`).
    1. Haga clic en **Agregar**.

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="Agregar usuario":::
1. Cree un **Recurso compartido local** para la persistencia de Video Analyzer.

    1. Haga clic en **Cloud storage gateway->Shares** (Puerta de enlace de almacenamiento en la nube > Recursos compartidos).
    1. Haga clic en **+ Agregar recursos compartidos**.
    1. Establezca un nombre de recurso compartido. (Recomendado: `ava`).
    1. Mantenga el tipo de recurso compartido como SMB.
    1. Asegúrese de que la opción **Usar el recurso compartido con el proceso perimetral** está activada.
    1. Asegúrese de que la opción **Configurar como recurso compartido local de Edge** está activada.
    1. En Detalles de usuario, conceda acceso al recurso compartido al usuario creado recientemente.
    1. Haga clic en **Crear**.
            
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="Recurso compartido local":::  
    
        > [!TIP]
        > Con el cliente Windows conectado a Azure Stack Edge, conéctese a los recursos compartidos de SMB mediante los pasos [mencionados en este documento](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share).    
1. Cree un recurso compartido remoto para el almacenamiento de File Sync.

    1. En primer lugar, cree una cuenta de almacenamiento de blobs en la misma región. Para ello, haga clic en **Cloud storage gateway->Storage accounts** (Puerta de enlace de almacenamiento en la nube -> Cuentas de almacenamiento).
    1. Haga clic en **Cloud storage gateway->Shares** (Puerta de enlace de almacenamiento en la nube > Recursos compartidos).
    1. Haga clic en **+ Agregar recursos compartidos**.
    1. Establezca un nombre de recurso compartido. (Recomendado: media).
    1. Mantenga el tipo de recurso compartido como SMB.
    1. Asegúrese de que la opción **Usar el recurso compartido con el proceso perimetral** está activada.
    1. Asegúrese de que la opción **Configurar como recurso compartido local de Edge** no está activada.
    1. Seleccione la cuenta de almacenamiento creada recientemente.
    1. Establezca el tipo de almacenamiento en blob en bloques.
    1. Establezca un nombre de contenedor.
    1. En Detalles de usuario, conceda acceso al recurso compartido al usuario creado recientemente.
    1. Haga clic en **Crear**.    
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="Recurso compartido remoto":::
1. Actualice las opciones de creación de contenedores del módulo del simulador RTSP para usar montajes de volumen:
    1. Haga clic en el botón **Establecer módulos**:

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="Establecimiento de módulos" lightbox="./media/deploy-on-stack-edge/set-modules.png":::
    1. Haga clic en el módulo **rtspsim**:

        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="Selección de módulos":::
    1. Seleccione la pestaña **Opciones de creación de contenedor** y agregue los montajes como se muestra a continuación:
    
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="Actualización de módulo":::

        ```json
            "createOptions": 
            {
                "HostConfig": 
                {
                    "Mounts": 
                    [
                        {
                            "Target": "/live/mediaServer/media",
                            "Source": "media",
                            "Type": "volume"
                        }
                    ],
                    "PortBindings": {
                        "554/tcp": [
                            {
                            "HostPort": "554"
                            }
                        ]
                    }
                }
            }
        ```
    1. Haga clic en el botón **Actualizar**.
    1. Haga clic en el botón **Revisar y crear** y, por último, en el botón **Crear** para actualizar el módulo.
    
### <a name="verify-that-the-module-is-running"></a>Comprobación de que el módulo se está ejecutando

El último paso es asegurarse de que el módulo está conectado y funciona según lo previsto. El estado del entorno de ejecución del módulo debe ser "en ejecución" para el dispositivo IoT Edge del recurso de IoT Hub.

Para comprobar que el módulo se está ejecutando, haga lo siguiente:

1. En Azure Portal, vuelva al recurso de Azure Stack Edge.
1. Seleccione el elemento Módulos. Esta acción le lleva a la hoja Módulos. En la lista de módulos, identifique el módulo que ha implementado. El estado del entorno de ejecución del módulo que se agrega es En ejecución.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="Módulo personalizado" lightbox="./media/deploy-on-stack-edge/running-module.png":::

### <a name="configure-the-azure-iot-tools-extension"></a>Configuración de la extensión Azure IoT Tools

Siga estas instrucciones para conectarse a su centro de IoT mediante la extensión Azure IoT Tools.

1. En Visual Studio Code, seleccione Ver > Explorer. O bien, seleccione Ctrl+Mayús+E.
1. En la esquina inferior izquierda de la pestaña Explorador, seleccione Azure IoT Hub.
1. Seleccione el icono Más opciones para ver el menú contextual. Luego, seleccione Set IoT Hub Connection String (Establecer cadena de conexión de IoT Hub).
1. Cuando aparezca un cuadro de entrada, escriba la cadena de conexión de IoT Hub. 

   * Para obtener la cadena de conexión, vaya a la instancia de IoT Hub en Azure Portal y haga clic en Directivas de acceso compartido en el panel de navegación izquierdo.
   * Haga clic en iothubowner para obtener las claves de acceso compartido.
   * Copie la cadena de conexión: clave principal y péguela en el cuadro de entrada de VSCode.

   La cadena de conexión tiene el siguiente aspecto:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Si la conexión se realiza correctamente, aparece la lista de dispositivos perimetrales. Debería ver la instancia de Azure Stack Edge. Ahora puede administrar los dispositivos IoT Edge e interactuar con Azure IoT Hub mediante el menú contextual. Para ver los módulos implementados en el dispositivo perimetral, en el dispositivo de Azure Stack, expanda el nodo Módulos.
    
## <a name="troubleshooting"></a>Solución de problemas

* **Acceso a la API de Kubernetes (kubectl)**

    * Siga la documentación para configurar la máquina para el [acceso al clúster de Kubernetes](../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md).
    * Todos los módulos de IoT Edge implementados usan el espacio de nombres `iotedge`. Asegúrese de incluirlo al usar kubectl.  
* **Registros del módulo**

    La herramienta `iotedge` no es accesible para obtener registros. Debe usar [registros de kubectl](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) para ver los registros o canalizar a un archivo. Ejemplo: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  
* **Métricas de pod y nodo**

    Use [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top) para ver métricas de pod y nodo.
    <br/>`kubectl top pods -n iotedge` 
* **Redes de módulo**   

    Para la detección de un módulo en Azure Stack Edge es necesario que el módulo tenga el enlace de puerto de host en createOptions. Entonces el módulo se puede direccionar a través de `moduleName:hostport`.
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* **Montaje de volumen**

    Un módulo no se inicia si el contenedor intenta montar un volumen en un directorio existente y que no esté vacío.
* **Memoria compartida al usar gRPC**

    La memoria compartida en recursos de Azure Stack Edge se admite en pods de cualquier espacio de nombres mediante IPC de host.
    Configuración de memoria compartida en un módulo perimetral para la implementación mediante IoT Hub.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
* **Colocalización de pod (avanzado)**

    Al usar K8s para implementar soluciones de inferencia personalizadas que se comunican con Video Analyzer a través de gRPC, debe asegurarse de que los pods estén implementados en los mismos nodos que los módulos de Video Analyzer.

    * **Opción 1**: usar afinidad de nodo y etiquetas de nodo integradas para la colocalización.

    Actualmente, la configuración personalizada de NodeSelector no parece ser una opción, ya que los usuarios no tienen acceso para establecer etiquetas en los nodos. Pero, en función de la topología del cliente y las convenciones de nomenclatura, es posible que puedan usar [etiquetas de nodo integradas](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels). Se puede agregar una sección nodeAffinity que haga referencia a los recursos de Azure Stack Edge con Video Analyzer al manifiesto del pod de inferencia para lograr la colocalización.
    * **Opción 2**: usar afinidad de pod para la colocalización (recomendado).

        Kubernetes es compatible con la [afinidad de pod](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity), que puede programar pods en el mismo nodo. Se puede agregar una sección podAffinity que haga referencia al módulo de Video Analyzer al manifiesto del pod de inferencia para lograr la colocalización.

         ```json   
        // Example Video Analyzer module deployment match labels
        selector:
          matchLabels:
            net.azure-devices.edge.deviceid: dev-ase-1-edge
            net.azure-devices.edge.module: mediaedge
        
        // Example Inference deployment manifest pod affinity
        spec:
          affinity:
            podAntiAffinity:
              requiredDuringSchedulingIgnoredDuringExecution:
              - labelSelector:
                  matchExpressions:
                  - key: net.azure-devices.edge.module
                    operator: In
                    values:
                    - mediaedge
                topologyKey: "kubernetes.io/hostname"
        ```
* **Código de error 404 al usar el módulo `rtspsim`**  
    
    El contenedor leerá vídeos de una carpeta exactamente dentro del contenedor. Si asigna o enlaza una carpeta externa con la que ya existe dentro de la imagen de contenedor, Docker ocultará los archivos presentes en la imagen de contenedor.  
 
    Por ejemplo, sin enlaces, el contenedor puede tener estos archivos:  
    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    Y el host puede tener estos archivos:
    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    Sin embargo, cuando se agrega el siguiente enlace en el archivo de manifiesto de implementación, Docker sobrescribirá el contenido de/live/mediaServer/media para que coincida con lo que hay en el host.
    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>Pasos siguientes

[Detección de movimiento y emisión de eventos](detect-motion-emit-events-quickstart.md)
