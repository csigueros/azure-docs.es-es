---
title: Implementación de Azure Video Analyzer en Azure Stack Edge
description: En este artículo se describe cómo implementar Azure Video Analyzer en Azure Stack Edge.
ms.topic: how-to
ms.date: 06/01/2021
ms.openlocfilehash: 2834828eb666bd80ca35284884e7745d8dbff350
ms.sourcegitcommit: 92889674b93087ab7d573622e9587d0937233aa2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/19/2021
ms.locfileid: "130178113"
---
# <a name="deploy-azure-video-analyzer-on-azure-stack-edge"></a>Implementación de Azure Video Analyzer en Azure Stack Edge

En este artículo se proporcionan instrucciones completas para implementar Azure Video Analyzer en su dispositivo de Azure Stack Edge. Una vez que haya configurado y activado el dispositivo, este estará listo para la implementación de Video Analyzer. 

En el artículo se implementará Video Analyzer mediante Azure IoT Hub, pero los recursos de Azure Stack Edge exponen una API de Kubernetes con la que se pueden implementar soluciones adicionales no compatibles con IoT Hub que pueden interactuar con Video Analyzer. 

> [!TIP]
> El uso de la API de Kubernetes para la implementación personalizada es un caso avanzado. Se recomienda crear módulos perimetrales e implementarlos mediante IoT Hub para cada recurso de Azure Stack Edge en lugar de usar la API de Kubernetes. En este artículo se muestra cómo implementar el módulo de Video Analyzer mediante IoT Hub.

## <a name="prerequisites"></a>Requisitos previos

* Una cuenta de Azure Video Analyzer

    Este [servicio en la nube](./overview.md) se usa para registrar el módulo perimetral Video Analyzer y para reproducir vídeo grabado y análisis de vídeo.

* Una identidad administrada

    Esta es la [identidad administrada](../../active-directory/managed-identities-azure-resources/overview.md) asignada por el usuario que se usa para administrar el acceso a la cuenta de almacenamiento.

* Un recurso de [Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md).

* Un [centro de IoT](../../iot-hub/iot-hub-create-through-portal.md)

* Una cuenta de almacenamiento

    Se recomienda usar una [cuenta de almacenamiento estándar de uso general v2](../../storage/common/storage-account-upgrade.md?tabs=azure-portal).  
    
* [Visual Studio Code](https://code.visualstudio.com/), instalado en la máquina de desarrollo

*  La [extensión de Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools), instalada en Visual Studio Code

* Asegúrese de que la red a la que está conectada la máquina de desarrollo permita Advanced Message Queueing Protocol a través del puerto 5671. Esta configuración permite a Azure IoT Tools comunicarse con su centro de Azure IoT.

## <a name="configure-azure-stack-edge-to-use-video-analyzer"></a>Configuración de Azure Stack Edge para usar Video Analyzer

Azure Stack Edge es una solución de hardware como servicio y un dispositivo informático perimetral habilitado para inteligencia artificial que cuenta con funcionalidades de transferencia de datos de red. Para obtener más información, consulte [Azure Stack Edge e instrucciones de configuración detalladas](../../databox-online/azure-stack-edge-gpu-deploy-prep.md). 

Para comenzar, haga lo siguiente:

1. [Cree un recurso de Azure Stack Edge o de Azure Data Box Gateway](../../databox-online/azure-stack-edge-gpu-deploy-prep.md?tabs=azure-portal#create-a-new-resource).  
1. [Instale y configure Azure Stack Edge Pro con GPU](../../databox-online/azure-stack-edge-gpu-deploy-install.md).  
1. Haga lo siguiente para conectar y activar el recurso:

    a. [Conéctese a la configuración de la interfaz de usuario web local](../../databox-online/azure-stack-edge-gpu-deploy-connect.md).  
    b. [Configure la red](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).  
    c. [Configure el dispositivo](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md).  
    d. [Configure los certificados](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md).  
    e. [Active el dispositivo](../../databox-online/azure-stack-edge-gpu-deploy-activate.md).  

1. [Asociación de una instancia de IoT Hub a Azure Stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute).

### <a name="meet-the-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>Cumplimiento de los requisitos previos de proceso en la interfaz de usuario local de Azure Stack Edge

Antes de continuar, asegúrese de haber hecho lo siguiente:

* Ha activado el recurso de Azure Stack Edge.
* Tiene acceso a un sistema cliente Windows con PowerShell 5.0 o posterior para acceder al recurso de Azure Stack Edge.
* Para implementar clústeres de Kubernetes, ha configurado el recurso de Azure Stack Edge en su [interfaz de usuario web local](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup). 

    1. Haga lo siguiente para conectar el recurso y configurarlo:
    
        a. [Conéctese a la configuración de la interfaz de usuario web local](../../databox-online/azure-stack-edge-gpu-deploy-connect.md).  
        b. [Configure la red](../../databox-online/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md).  
        c. [Configuración del dispositivo](../../databox-online/azure-stack-edge-gpu-deploy-set-up-device-update-time.md)  
        d. [Configure los certificados](../../databox-online/azure-stack-edge-gpu-deploy-configure-certificates.md).  
        e. [Active el dispositivo](../../databox-online/azure-stack-edge-gpu-deploy-activate.md).

    1. Para habilitar el proceso, vaya a la página **Proceso** de la interfaz de usuario web local del dispositivo.
    
        a. Seleccione una interfaz de red que quiera habilitar para el proceso y, después, seleccione **Habilitar**. Al habilitar el proceso se crea un conmutador virtual en el dispositivo, en esa interfaz de red.  
        b. Deje en blanco las direcciones IP de los nodos de prueba de Kubernetes y las de los servicios externos de Kubernetes.  
        c. Seleccione **Aplicar**. La operación tardará aproximadamente dos minutos.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="../../databox-online/media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/compute-network-2.png" alt-text="Captura de pantalla de los requisitos previos de proceso en la interfaz de usuario local de Azure Stack Edge.":::

        Si Azure DNS no está configurado para la API de Kubernetes y el recurso de Azure Stack Edge, puede hacer lo siguiente para actualizar el archivo host de Windows:
        
        a. Abra un editor de texto como Administrador.  
        b. Abra el archivo *hosts* en *C:\Windows\System32\drivers\etc\\* .  
        c. Agregue el nombre de host y el protocolo de Internet versión 4 (IPv4) del nombre de dispositivo de la API de Kubernetes al archivo. Puede encontrar esta información en el portal de Azure Stack Edge, en **Dispositivos**.  
        d. Guarde y cierre el archivo.

### <a name="deploy-video-analyzer-edge-modules-by-using-the-azure-portal"></a>Implementación de módulos Edge de Video Analyzer mediante Azure Portal

En Azure Portal, puede crear un manifiesto de implementación e insertar la implementación en un dispositivo de IoT Edge.  

#### <a name="select-your-device-and-set-modules"></a>Selección del dispositivo y establecimiento de módulos

1. Inicie sesión en [Azure Portal](https://ms.portal.azure.com/) y, a continuación, vaya al centro de IoT.
1. En el panel izquierdo, seleccione **IoT Edge**.
1. Seleccione el identificador del dispositivo de destino en la lista de dispositivos.
1. Seleccione **Set modules** (Establecer módulos).

#### <a name="configure-a-deployment-manifest"></a>Configuración de un manifiesto de implementación

Un manifiesto de implementación es un documento JSON que describe qué módulos se van a implementar, cómo fluyen los datos entre los módulos y las propiedades deseadas de los módulos gemelos. Azure Portal cuenta con un asistente que le guía en la creación de un manifiesto de implementación. Tiene tres pasos, que se organizan en las pestañas **Módulos**, **Rutas** y **Revisar y crear**.

#### <a name="add-modules"></a>Adición de módulos

1. En la sección **Módulos IoT Edge**, en la lista desplegable **Agregar**, seleccione **Módulo IoT Edge** para mostrar la página **Agregar módulo IoT Edge**.
1. Seleccione la pestaña **Configuración del módulo**, proporcione un nombre para el módulo y especifique el URI de imagen del contenedor. Los valores de ejemplo se muestran en la imagen siguiente:     
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-module.png" alt-text="Captura de pantalla del panel Configuración del módulo en la página Agregar módulo IoT Edge.":::
    
    > [!TIP]
    > No seleccione **Agregar** hasta que haya especificado los valores en las pestañas **Configuración del módulo**, **Opciones de creación del contenedor** y **Configuración de módulos gemelos**, tal como se describe en este procedimiento.
    
    > [!IMPORTANT]
    > Los valores de Azure IoT Edge distinguen mayúsculas de minúsculas al realizar llamadas a los módulos. Tome nota de la cadena exacta que utiliza como nombre del módulo.

1. Seleccione la pestaña **Variables de entorno** y, a continuación, escriba los valores, tal y como se muestra en la imagen siguiente:
   
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/environment-variables.png" alt-text="Captura de pantalla del panel &quot;Variables de entorno&quot; en la página &quot;Agregar módulo IoT Edge&quot;.":::

1. Seleccione la pestaña **Opciones de creación del contenedor**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/container-create-options.png" alt-text="Captura de pantalla del panel Opciones de creación del contenedor en la página Agregar módulo IoT Edge.":::
 
    Pegue el código JSON siguiente en el cuadro del panel **Opciones de creación del contenedor**. Esta acción limita el tamaño de los archivos de registro que genera el módulo.
    
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
   
   La sección "Binds" del archivo JSON tiene dos entradas:
   * **"/var/lib/videoanalyzer:/var/lib/videoanalyzer"** se usa para enlazar los datos de configuración persistentes de la aplicación desde el contenedor y almacenarlos en el dispositivo perimetral.
   * **"/var/media:/var/media"** enlaza las carpetas de elementos multimedia entre el dispositivo perimetral y el contenedor. Se usa para almacenar las grabaciones de vídeo cuando se ejecuta un elemento pipelineTopology que admite el almacenamiento de clips de vídeo en el dispositivo perimetral.
   
1. Seleccione la pestaña **Configuración de módulos gemelos**.
 
   Para ejecutarse, Azure Video Analyzer requiere un conjunto de propiedades gemelas obligatorias, tal y como se muestra en [Esquema de configuración de módulos gemelos](module-twin-configuration-schema.md).  

1. Pegue el código JSON siguiente en el cuadro del panel **Configuración de módulos gemelos**:    

    ```
    {
        "applicationDataDirectory": "/var/lib/videoanalyzer",
        "ProvisioningToken": "{provisioning-token}",
    }
    ```
   
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/twin-settings.png" alt-text="Captura de pantalla del panel &quot;Configuración de módulos gemelos&quot; en la página &quot;Agregar módulo IoT Edge&quot;.":::   

    Para ayudar a supervisar el módulo, puede agregar las propiedades *recomendadas* siguientes al código JSON. Para obtener más información, vea [Supervisión y registro](monitor-log-edge.md).
    
    ```
    "diagnosticsEventsOutputName": "diagnostics",
    "OperationalEventsOutputName": "operational",
    "logLevel": "Information",
    "logCategories": "Application,Events",
    "allowUnsecuredEndpoints": true,
    "telemetryOptOut": false
    ```
1. Seleccione **Agregar**.  

#### <a name="add-the-real-time-streaming-protocol-rtsp-simulator-edge-module"></a>Adición del módulo perimetral del simulador del protocolo RTSP

1. En la sección **Módulos IoT Edge**, en la lista desplegable **Agregar**, seleccione **Módulo IoT Edge** para mostrar la página **Agregar módulo IoT Edge**.
1. Seleccione la pestaña **Configuración del módulo**, proporcione un nombre para el módulo y especifique el URI de imagen del contenedor. Por ejemplo:   
    
    * **Nombre del módulo IoT Edge**: rtspsim  
    * **URI de imagen**: mcr.microsoft.com/lva-utilities/rtspsim-live555:1.2 

1. Seleccione la pestaña **Opciones de creación del contenedor** y, a continuación, pegue el código JSON siguiente en el cuadro:
    
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

1. Seleccione **Siguiente: Rutas** para continuar con la sección de rutas. 

1. Para especificar las rutas, en **Nombre**, escriba **AVAToHub** y, a continuación, en **Valor**, escriba **FROM /messages/modules/avaedge/outputs/ INTO $upstream**.

1. Seleccione **Siguiente: Revisar y crear** para continuar en la sección de revisión.

1. Revise la información de implementación y, a continuación, seleccione **Crear** para implementar el módulo.

#### <a name="generate-the-provisioning-token"></a>Generación del token de aprovisionamiento

1. En Azure Portal, vaya a Video Analyzer.
1. En el panel izquierdo, seleccione **Edge modules** (Módulos perimetrales).
1. Seleccione el módulo perimetral y, a continuación, seleccione **Generar token**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/generate-provisioning-token.png" alt-text="Captura de pantalla del panel &quot;Agregar módulos perimetrales&quot; para generar un token." lightbox="./media/deploy-on-stack-edge/generate-provisioning-token.png":::

1. Copie el token de aprovisionamiento, tal y como se muestra en la imagen siguiente:

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/copy-provisioning-token.png" alt-text="Captura de pantalla de la página &quot;Copiar token de aprovisionamiento&quot;.":::



#### <a name="optional-set-up-docker-volume-mounts"></a>(Opcional) Configuración de montajes de volumen de Docker

Si quiere ver los datos de los directorios de trabajo, configure los montajes de volumen de Docker antes de la implementación. 

En esta sección se describe cómo crear un usuario de puerta de enlace y configurar recursos compartidos de archivos para ver el contenido del directorio de trabajo de Video Analyzer y la carpeta multimedia de Video Analyzer.

> [!NOTE]
> Se admiten montajes de enlace, pero los montajes de volumen permiten que se vean los datos y, si se elige, que se copien de forma remota. Es posible usar tanto montajes de enlace como de volumen, pero no pueden apuntar a la misma ruta de acceso de contenedor.

1. En Azure Portal, vaya al recurso de Azure Stack Edge.
1. Haga lo siguiente para crear un usuario de puerta de enlace que pueda acceder a los recursos compartidos:
    
    a. En el panel izquierdo, seleccione **Cloud storage gateway** (Puerta de enlace de almacenamiento en la nube).  
    b. En el panel izquierdo, seleccione **Usuarios**.  
    c. Seleccione **Agregar usuario** para establecer el nombre de usuario (por ejemplo, se recomienda *avauser*) y la contraseña.  
    d. Seleccione **Agregar**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/add-user.png" alt-text="Captura de pantalla de la página &quot;Agregar usuario&quot; del recurso de Azure Stack Edge.":::

1. Haga lo siguiente para crear un *recurso compartido local* para la persistencia de Video Analyzer:

    a. Seleccione **Cloud storage gateway** (Puerta de enlace de almacenamiento en la nube) > **Recursos compartidos**.  
    b. Seleccione **Agregar recurso compartido**.  
    c. Establezca un nombre de recurso compartido (por ejemplo, se recomienda *ava*).  
    d. Mantenga el tipo de recurso compartido como **SMB**.  
    e. Asegúrese de que la casilla **Usar el recurso compartido con el proceso perimetral** esté seleccionada.  
    f. Asegúrese de que la casilla **Configurar como recurso compartido local de Edge** esté seleccionada.  
    g. En **Detalles del usuario**, conceda acceso al recurso compartido al usuario creado recientemente; para ello, seleccione **Usar existente**.  
    h. Seleccione **Crear**.
            
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/local-share.png" alt-text="Captura de pantalla de la página &quot;Agregar recurso compartido&quot; para crear un recurso compartido local.":::  
    
    > [!TIP]
    > Con el cliente Windows conectado al dispositivo Azure Stack Edge, siga las instrucciones de la sección "Conexión a un recurso compartido SMB" de [Transferencia de datos con Azure Stack Edge Pro FPGA](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share).    

1. Haga lo siguiente a fin de crear un *recurso compartido remoto* para el almacenamiento de sincronización de archivos:

    a. Cree una cuenta de Azure Blob Storage en la misma región; para ello, seleccione **Cloud storage gateway** (Puerta de enlace de almacenamiento en la nube)  > **Cuentas de almacenamiento**.  
    b. Seleccione **Cloud storage gateway** (Puerta de enlace de almacenamiento en la nube) > **Recursos compartidos**.  
    c. Seleccione **Agregar recursos compartidos**.  
    d. En el cuadro **Nombre**, escriba un nombre de recurso compartido (por ejemplo, se recomienda *media*).  
    e. En **Tipo**, mantenga el tipo de recurso compartido como **SMB**.  
    f. Asegúrese de que la casilla **Usar el recurso compartido con el proceso perimetral** esté seleccionada.  
    g. Asegúrese de que la casilla **Configurar como recurso compartido local de Edge** no esté seleccionada.  
    h. En la lista desplegable **Cuenta de almacenamiento**, seleccione la cuenta de almacenamiento recién creada.  
    i. En la lista desplegable **Servicio de almacenamiento**, seleccione **Blob en bloques**.  
    j. En el cuadro **Seleccionar el contenedor de blobs**, escriba el nombre del contenedor.  
    k. En **Detalles del usuario**, seleccione **Usar existente** para conceder acceso al recurso compartido al usuario creado recientemente.  
    l. Seleccione **Crear**.    
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/remote-share.png" alt-text="Captura de pantalla de la página &quot;Agregar recurso compartido&quot; para crear un recurso compartido remoto.":::

1. Para usar montajes de volumen, actualice la configuración en el panel **Opciones de creación del contenedor** para el módulo del simulador RTSP mediante el proceso siguiente:

    a. Seleccione el botón **Establecer módulos**.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/set-modules.png" alt-text="Captura de pantalla que muestra el botón &quot;Establecer módulos&quot; en el panel de configuración del dispositivo perimetral." lightbox="./media/deploy-on-stack-edge/set-modules.png":::  

    b. En la lista **Nombre**, seleccione el módulo **rtspsim**:

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/select-module.png" alt-text="Captura de pantalla del módulo &quot;rtspsim&quot; en &quot;Módulos IoT Edge&quot; en el panel de configuración del dispositivo perimetral.":::  
    
    c. En el panel **Actualizar módulo IoT Edge**, seleccione la pestaña **Opciones de creación del contenedor** y, a continuación, agregue los montajes como se muestra en el código JSON siguiente:
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/update-module.png" alt-text="Captura de pantalla del código JSON de los montajes en el panel &quot;Opciones de creación del contenedor&quot;.":::

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
    d. Seleccione **Actualizar**.  
    e. Para actualizar el módulo, seleccione **Revisar y crear** y, a continuación, elija **Crear**.
    
### <a name="verify-that-the-module-is-running"></a>Comprobación de que el módulo se está ejecutando

Por último, asegúrese de que el módulo del dispositivo IoT Edge esté conectado y se ejecute según lo previsto. Para comprobar el estado del módulo en tiempo de ejecución, haga lo siguiente:

1. En Azure Portal, vuelva al recurso de Azure Stack Edge.
1. En el panel izquierdo, seleccione **Módulos**. 
1. En el panel **Módulos**, en la lista **Nombre**, seleccione el módulo que ha implementado. En la columna **Estado en tiempo de ejecución**, el estado del módulo debe ser *en ejecución*.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-on-stack-edge/running-module.png" alt-text="Captura de pantalla del panel &quot;Módulo&quot;, en la que se muestra el estado en tiempo de ejecución del módulo seleccionado como &quot;en ejecución&quot;." lightbox="./media/deploy-on-stack-edge/running-module.png":::

### <a name="configure-the-azure-iot-tools-extension"></a>Configuración de la extensión Azure IoT Tools

Para conectarse a su centro de IoT mediante la extensión de Azure IoT Tools, haga lo siguiente:

1. En Visual Studio Code, seleccione **Ver** > **Explorador**.
1. En la parte inferior izquierda de la pestaña **Explorador**, seleccione **Azure IoT Hub**.
1. Haga clic en el icono **Más opciones** para ver el menú contextual y, a continuación, seleccione la opción **Set IoT Hub Connection String** (Establecer cadena de conexión de IoT Hub).

   Aparece un cuadro de entrada en el que escribirá la cadena de conexión de su centro de IoT. Para obtener la cadena de conexión, haga lo siguiente: 

   a. En Azure Portal, vaya hasta su instancia de IoT Hub.  
   b. En el panel izquierdo, seleccione **Directivas de acceso compartido**.  
   c. Seleccione **iothubowner** para obtener las clave de acceso compartido.  
   d. Copie la clave principal de la cadena de conexión y, a continuación, péguela en el cuadro de entrada.

   > [!NOTE]
   > La cadena de conexión está escrita con el formato siguiente:
   >
   > `HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Una vez que la conexión se establezca correctamente, se muestra una lista de dispositivos perimetrales, incluido el dispositivo de Azure Stack Edge. Ahora puede administrar los dispositivos IoT Edge e interactuar con su centro de Azure IoT mediante el menú contextual. 
   
   Para ver los módulos implementados en el dispositivo perimetral, en el dispositivo de Azure Stack, expanda el nodo **Módulos**.
    
## <a name="troubleshooting"></a>Solución de problemas

* **Acceso a la API de Kubernetes (kubectl)**

    * Configure la máquina para acceder al clúster de Kubernetes; para ello, siga las instrucciones de [Creación y administración de un clúster de Kubernetes en un dispositivo de Azure Stack Edge Pro con GPU](../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md).
    * Todos los módulos IoT Edge implementados usan el espacio de nombres *iotedge*. Asegúrese de incluir ese nombre cuando use kubectl. 

* **Registros del módulo**

    Si no se puede acceder a la herramienta *iotedge* para obtener los registros, use [kubectl logs](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs) para ver los registros o canalizarlos a un archivo. Por ejemplo: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  

* **Métricas de pod y nodo**

    Use [kubectl top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top) para ver métricas de pod y nodo. Por ejemplo:   <br/>`kubectl top pods -n iotedge` 

* **Redes de módulo**   

    Para la detección de un módulo en Azure Stack Edge, es necesario que el módulo tenga el enlace de puerto de host en createOptions. Entonces el módulo se puede direccionar a través de `moduleName:hostport`.
    
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

* **Memoria compartida cuando se usa gRPC**

    La memoria compartida en recursos de Azure Stack Edge se admite en pods de cualquier espacio de nombres cuando use IPC de host.
    
    Use el código siguiente a fin de configurar la memoria compartida en un módulo perimetral para la implementación a través de IoT Hub:
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a Kubernetes pod or deployment manifest for deployment via the Kubernetes API spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
* **Coubicación de pod (avanzado)**

    Al usar Kubernetes para implementar soluciones de inferencia personalizadas que se comunican con Video Analyzer a través de gRPC, asegúrese de que los pods estén implementados en los mismos nodos que los módulos de Video Analyzer.

    * **Opción 1**: usar la *afinidad de nodo* y etiquetas de nodo integradas para la coubicación.  

    Actualmente, la configuración personalizada de NodeSelector no parece ser una opción, ya que los usuarios no tienen acceso para establecer etiquetas en los nodos. Sin embargo, en función de la topología del usuario y de las convenciones de nomenclatura, es posible que puedan usar [etiquetas de nodo integradas](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels). Para lograr la coubicación, se puede agregar al manifiesto del pod de inferencia una sección nodeAffinity que haga referencia a los recursos de Azure Stack Edge con Video Analyzer.
    
    * **Opción 2**: (recomendada) usar la *afinidad de pod* para la coubicación.  

        Kubernetes es compatible con la [afinidad de pod](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity), que puede programar pods en el mismo nodo. Para lograr la coubicación, puede agregar al manifiesto de pod de inferencia una sección podAffinity que haga referencia al módulo de Video Analyzer.

         ```json   
        // Example Video Analyzer module deployment match labels
        selector:
          matchLabels:
            net.azure-devices.edge.deviceid: dev-ase-1-edge
            net.azure-devices.edge.module: mediaedge
        
        // Example inference deployment manifest pod affinity
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

* **Al usar el módulo *rtspsim*, se obtiene un código de error 404**  
    
    El contenedor lee vídeos de una carpeta exactamente dentro del contenedor. Si asigna o enlaza una carpeta externa con otra que ya existe dentro de la imagen de contenedor, Docker oculta los archivos presentes en la imagen de contenedor.
 
    Por ejemplo, sin enlaces, el contenedor puede tener estos archivos:  

    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    A su vez, el host puede tener estos archivos:

    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    Sin embargo, cuando se agrega el enlace siguiente en el archivo de manifiesto de implementación, Docker sobrescribe el contenido de/live/mediaServer/media para que coincida con lo que hay en el host.

    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>Pasos siguientes

[Detección de movimiento y emisión de eventos](detect-motion-emit-events-quickstart.md)
