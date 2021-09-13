---
title: Migración de Live Video Analytics a Azure Video Analyzer
description: Aprenda a migrar de Live Video Analytics a Azure Video Analyzer.
ms.topic: how-to
ms.date: 08/16/2021
ms.openlocfilehash: a2225e25685bb9177851379fd86b59c986ff28f0
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326568"
---
# <a name="how-to-migrate-from-live-video-analytics-to-azure-video-analyzer"></a>Migración de Live Video Analytics a Azure Video Analyzer

Utilice este artículo si ha implementado un módulo de Edge de Live Video Analytics en un dispositivo IoT Edge y actualmente ejecuta grafos multimedia para procesar fuentes de las cámaras del Protocolo de streaming en tiempo real (RTSP).

Si no usa cámaras RTSP y está utilizando los [inicios rápidos o tutoriales de Live Video Analytics](../live-video-analytics-edge/overview.md), simplemente debe cambiar al [inicio rápido o tutorial correspondiente con Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/overview.md).

Las instrucciones siguientes se aplican a la migración de Live Video Analytics a Azure Video Analyzer en el caso de un único dispositivo IoT Edge. Si existen otros dispositivos, deberá repetir estos pasos con cada uno.

## <a name="prerequisites"></a>Requisitos previos

* Una suscripción de Azure activa.

   > [!NOTE]
   > Necesitará una suscripción de Azure en la que tenga roles de acceso de  [Colaborador](../../role-based-access-control/built-in-roles.md)  y  [Administrador de acceso de usuario](../../role-based-access-control/built-in-roles.md)  al grupo de recursos en el que creará recursos (identidad administrada asignada por el usuario, cuenta de almacenamiento, cuenta de Video Analyzer). Si no tiene los permisos adecuados, pida al administrador de la cuenta que se los conceda. 

* Necesitará privilegios administrativos para los dispositivos IoT Edge en los que haya instalado Live Video Analytics.

## <a name="create-a-backup-of-current-settings"></a>Creación de una copia de seguridad de la configuración actual

Antes de comenzar las tareas de migración, se recomienda encarecidamente guardar el manifiesto de implementación perimetral y las configuraciones del grafo multimedia de Live Video Analytics.

> [!NOTE]
> A continuación, se enumeran los pasos. Los datos de las credenciales de RTSP y de las direcciones URL de streaming de las cámaras no se pueden recuperar.  Deberá recuperar esa información directamente desde la aplicación de administración de la cámara.

### <a name="save-the-current-deployment-manifest"></a>Guardado del manifiesto de implementación actual

1. Inicie sesión en Azure Portal.

1. Vaya a **IoT Hub** y, en **Administración de dispositivos automática**, seleccione **IoT Edge**.

1. En **IoT Edge**, seleccione el dispositivo IoT Edge que quiere migrar a Azure Video Analyzer.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/iot-edge.png" alt-text="Captura de pantalla que muestra dispositivos IoT Edge.":::

1. Seleccione **Establecer módulos** y, luego, **Revisar y crear**.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/set-modules.png" alt-text="Captura de pantalla que muestra el manifiesto de implementación.":::

1. Copie la sección **Implementación** en la ventana y guárdela en una ubicación segura.

1. Seleccione la **X** de la esquina superior derecha del portal.  

    > [!NOTE]
    > *No* seleccione **Crear**.  Revise el manifiesto de implementación y busque el nodo `lvaEdge` en `modules`.  Si tiene un nodo `env` debajo del nodo `lvaEdge`, anote los valores de LOCAL_EDGE_USER y lOCAL_GROUP_ID; los necesitará más adelante en el documento.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/deployment-manifest-env-node.png" alt-text="Captura de pantalla que muestra el manifiesto de implementación y la sección env para el usuario y el grupo perimetrales locales.":::

### <a name="save-the-live-video-analytics-topologies"></a>Guardado de las topologías de Live Video Analytics

1. Para guardar el grafo multimedia de Live Video Analytics, seleccione el módulo Live Video Analytics en el dispositivo IoT Edge.  En el ejemplo siguiente, el nombre del contenedor es *IvaEdge*.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/live-video-analytics-edge.png" alt-text="Captura de pantalla que muestra el nombre del contenedor del módulo Live Video Analytics":::.

1. Seleccione **Método directo**.

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Captura de pantalla que muestra las llamadas de método directo de Live Video Analytics.":::

1. Escriba **GraphTopologyList** en el campo Nombre del método y escriba el código siguiente en la carga:

    ```JSON
    {
        "@apiVersion" : "2.0"
    }
    ```

   :::image type="content" source="./media/migrate-to-azure-video-analyzer/method-name.png" alt-text="Captura de pantalla que muestra la llamada de método directo de IoT Edge.":::

1. Seleccione **Invocar método**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/invoke-method.png" alt-text="Captura de pantalla que muestra el método de invocación.":::

1. El valor devuelto se mostrará en la sección de resultados.  Esta es la topología del grafo de Live Video Analytics.  Copie el código JSON y guárdelo en un lugar seguro.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/topology-result.png" alt-text="Captura de pantalla que muestra el resultado del método GraphTopologyList.":::

1. Repita el proceso, pero reemplace `GraphTopologyList` por `GraphInstanceList` y guarde la sección de resultados.  Esta es una lista de las instancias de grafo. Anote los nombres de todas las instancias activas.

1.  Después de crear una copia de seguridad de la configuración de Live Video Analytics, puede *desactivar todos los grafos multimedia activos*; para ello escriba **GraphInstanceDeactivate** en **Nombre del método** y, luego, escriba el código siguiente:

    ```json
    {
        "@apiVersion" : "2.0",
        "name" : "<Graph_Name>"
    }
    ```

    > [!NOTE]
    > Reemplace Graph_Name por el nombre de las instancias activas en los nuevos resultados.  Repita este proceso hasta que se desactiven todos los gráficos activos. 

## <a name="azure-video-analyzer-device-preparation"></a>Azure Video Analyzer: preparación de los dispositivos 

Para iniciar la migración, el dispositivo IoT Edge debe estar configurado para ejecutar el módulo de Azure Video Analyzer. El módulo de Azure Video Analyzer debe ejecutarse en un dispositivo IoT Edge con una cuenta de usuario local sin privilegios. El módulo necesita ciertas carpetas locales para almacenar los datos de configuración de la aplicación. El módulo del simulador de cámara RTSP necesita archivos de vídeo con los que pueda sintetizar una fuente de vídeo en directo. 

1. Inicie sesión en el dispositivo IoT Edge mediante SSH.

    > [!Note]
    > En función de la revisión del archivo de manifiesto del dispositivo IoT Edge (paso 6 de la sección "Creación de una copia de seguridad de la configuración actual"), deberá crear un usuario o grupo en el dispositivo IoT Edge o reutilizar los que se crearon en Live Video Analytics.

    Si el dispositivo IoT Edge se ha configurado con un identificador de usuario y grupo local para Live Video Analytics (que se encuentra en el manifiesto de implementación), puede omitir el paso 1; sin embargo, anote el nombre y el valor del usuario y del grupo, ya que se usarán más adelante en este documento.  Si también usa una estructura de carpetas única para la implementación de Live Video Analytics (que se encuentra en el manifiesto de implementación), reemplace *videoanalyzer* por esos valores y omita las dos primeras líneas del paso 2.  

    En el ejemplo siguiente se muestra una estructura de carpetas única de una implementación de Live Video Analytics.  Tenga en cuenta que *applicationDataDirectory* es /var/lib/videofiles. En este ejemplo, Live Video Analytics tiene los valores *localedgeuser* y *localedgegroup* creados en el dispositivo IoT Edge.

    ```
    "lvaEdge": {
                "properties.desired": {
                    "applicationDataDirectory": "/var/lib/videofiles",
                    "azureMediaServicesArmId": "/subscriptions/resourceGroups/lvamigration/providers/microsoft.media/mediaservices/lvasamplejbbvaomtycjas",
                    "aadTenantId": "<guid>",
                    "aadServicePrincipalAppId": "<guid>",
                    "aadServicePrincipalSecret": "<guid>",
                    "aadEndpoint": "https://login.microsoftonline.com",
                    "aadResourceId": "https://management.core.windows.net/",
                    "armEndpoint": "https://management.azure.com/",
                    "diagnosticsEventsOutputName": "AmsDiagnostics",
                    "operationalEventsOutputName": "AmsOperational",
                    "logLevel": "Information",
                    "logCategories": "Application,Events",
                    "allowUnsecuredEndpoints": true,
                    "telemetryOptOut": false
                }
            },
    ```

    Después, ejecute el código siguiente:

     ```
       sudo mkdir -p /var/lib/videofiles/tmp/ 
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/tmp/
       sudo mkdir -p /var/lib/videofiles/logs
       sudo chown -R localedgeuser:localedgegroup /var/lib/videofiles/logs
     ```

1. Cree una cuenta de usuario local en el dispositivo IoT Edge mediante la ejecución del código siguiente:

   ```bash
   sudo groupadd -g 1010 localedgegroup
   sudo useradd --home-dir /home/localedgeuser --uid 1010 --gid 1010 localedgeuser
   sudo mkdir -p /home/localedgeuser
   sudo chown -R localedgeuser:localedgegroup /home/localedgeuser/
   ```

1. Cree la estructura de carpetas *avaedge* con el código siguiente:

   ```bash
   sudo mkdir -p /var/lib/videoanalyzer 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/
   sudo mkdir -p /var/lib/videoanalyzer/tmp/ 
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/tmp/
   sudo mkdir -p /var/lib/videoanalyzer/logs
   sudo chown -R localedgeuser:localedgegroup /var/lib/videoanalyzer/logs
   ```

## <a name="create-azure-resources"></a>Creación de recursos de Azure

El siguiente paso consiste en crear los recursos de Azure necesarios. Las instrucciones siguientes se documentan en la sección [Creación de recursos de Azure](../../azure-video-analyzer/video-analyzer-docs/get-started-detect-motion-emit-events-portal.md) y se repiten aquí para mayor comodidad. 

Ahora creará los recursos de Azure necesarios (cuenta de Video Analyzer, cuenta de almacenamiento e identidad administrada asignada por el usuario). 

Al crear una cuenta de Azure Video Analyzer, tiene que asociar una cuenta de Azure Storage a ella. Si usa Video Analyzer para grabar vídeo en directo desde una cámara, los datos se almacenan como blobs en un contenedor de la cuenta de almacenamiento. Debe usar una identidad administrada para conceder a la cuenta de Video Analyzer el acceso adecuado a la cuenta de almacenamiento. 

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Creación de una cuenta de Video Analyzer en Azure Portal

1. Inicie sesión en  [Azure Portal](https://portal.azure.com/). 

1. En el cuadro de búsqueda, escriba  **Video Analyzer**. 

1. En  **Servicios** , seleccione  **Video Analyzers** (Instancias de Video Analyzer). 

1. Seleccione  **Agregar**. 

1. En la sección  **Create Video Analyzer account**  (Crear una cuenta de Video Analyzer), escriba estos valores: 

   - **Suscripción**: elija la suscripción que esté usando para crear la cuenta de Video Analyzer. 

   - **Grupo de recursos**: elija un grupo de recursos en el que crear la cuenta de Video Analyzer o seleccione  **Crear**  para crear un grupo de recursos. 

   - **Video Analyzer account name** (Nombre de la cuenta de Video Analyzer): este es el nombre de la cuenta de Video Analyzer. El nombre debe estar compuesto totalmente de minúsculas o números, sin espacios y con una longitud de entre 3 y 24 caracteres. 

   - **Ubicación**: elija una ubicación para implementar la cuenta de Video Analyzer, por ejemplo,  **Oeste de EE. UU. 2**. 

   - **Cuenta de almacenamiento**: cree una cuenta de almacenamiento. Se recomienda seleccionar una cuenta de almacenamiento  [standard general-purpose v2](../../storage/common/storage-account-overview.md#types-of-storage-accounts) . 

      > [!NOTE]
      > Si tiene una cuenta de almacenamiento que se encuentra en la región en la que va a implementar la cuenta de Azure Video Analyzer, puede optar por usar esa cuenta de almacenamiento en lugar de crear una. 

   - **Identidad de usuario**: cree una nueva identidad administrada asignada por el usuario y asígnele un nombre. 

1. En la parte inferior del formulario, **marque** la casilla que aparece al lado del texto "I certify that I have obtained all necessary rights and consents under applicable law to process media content and agree to be bound by all applicable" (Certifico que he obtenido todos los derechos y consentimientos necesarios en virtud de la ley vigente para procesar el contenido multimedia y acepto mi vinculación a todos los aplicables). 

1. Seleccione  **Revisar y crear**  en la parte inferior del formulario. 

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/video-analyzer-account.png" alt-text="Captura de pantalla que muestra cómo crear la cuenta de Video Analyzer en Azure Portal.":::

   > [!NOTE]
   > Estos recursos de Azure se agregarán al grupo de recursos.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/resources.png" alt-text="Captura de pantalla que muestra los recursos que también se agregarán al grupo de recursos de Azure.":::

## <a name="deploy-the-azure-video-analyzer-edge-module"></a>Implementación del módulo perimetral de Azure Video Analyzer

### <a name="generate-azure-video-analyzer-edge-module-token"></a>Generación del token del módulo perimetral de Azure Video Analyzer

1. Vaya a su cuenta de Video Analyzer.

1. Seleccione **Edge Modules** (Módulos perimetrales) en el panel **Perimetral**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-module.png" alt-text="Captura de pantalla que muestra la creación del módulo perimetral de la cuenta de Azure Video Analyzer.":::

1. Seleccione **Add edge modules**, (Agregar módulos perimetrales), escriba **avaedge** como nombre del nuevo módulo perimetral y seleccione **Agregar**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/add-edge-modules.png" alt-text="Captura de pantalla que muestra cómo agregar módulos perimetrales":::.

1. Aparecerá el panel **Copy the provisioning token** (Copiar el token de aprovisionamiento) en el lado derecho de la pantalla. En **Recommended desired properties for IoT module deployment** (Propiedades deseadas recomendadas para la implementación del módulo IoT), copie el fragmento de código.  Necesitará esta información en un paso posterior.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/provisioning-token.png" alt-text="Captura de pantalla que muestra el panel del token de aprovisionamiento.":::

### <a name="deploy-the-azure-video-analyzer-edge-module"></a>Implementación del módulo perimetral de Azure Video Analyzer

1.  Vaya a su instancia de Azure IoT Hub.

1. En **Administración automática de dispositivos**, elija **Select IoT Edge** (Seleccionar IoT Edge).

1. Seleccione el valor de identificador de dispositivo del dispositivo IT Edge.

1. Seleccione Set modules (Establecer módulos).

1. Seleccione **Agregar** y, luego, **Marketplace de módulos IoT Edge** en el menú desplegable.

1. Escriba Azure Video Analyzer en el campo de búsqueda.

1. Seleccione Azure Video Analyzer.

1. En el panel **Establecer módulos**, seleccione **AzureVideoAnalyzerEdge**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge.png" alt-text="Captura de pantalla que muestra cómo se agrega el módulo IoT Edge Azure Video Analyzer.":::

1. En el campo Nombre del módulo IoT Edge, escriba **avaedge**.
1. Seleccione **Variables de entorno**. 

   > [!NOTE]
   > Si la implementación de Live Video Analytics usaba un usuario y un grupo locales que se encuentran en el paso 6 de la sección "Creación de una copia de seguridad de la configuración actual", use esos valores en lugar de LOCAL_USER_ID, LOCAL_GROUP_ID en el paso siguiente.

1. En el campo Nombre, escriba **LOCAL_USER_ID** y, en el campo Valor, escriba **1010**.  En el siguiente campo de nombre, escriba **LOCAL_GROUP_ID** y, en el campo Valor, escriba **1010**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/environment-variables.png" alt-text="Captura de pantalla que muestra las variables de entorno de Azure Video Analyzer.":::

1. Seleccione **Opciones de creación de contenedor** y pegue lo siguiente:

      ```json
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
                  "/var/media/:/var/media/",
                  "/var/lib/videoanalyzer/:/var/lib/videoanalyzer"
            ],
            "IpcMode": "host",
            "ShmSize": 1536870912
         }
      }
      ```

1. Seleccione  **Configuración de módulos gemelos**  y pegue el fragmento de código que copió anteriormente de la página  **Copy the provisioning token** (Copiar el token de aprovisionamiento) de la cuenta de Video Analyzer. 

      ```json
      {
         "applicationDataDirectory": "/var/lib/videoanalyzer",
         "ProvisioningToken": "<Provisioning Token Value>",
         "diagnosticsEventsOutputName": "diagnostics",
         "operationalEventsOutputName": "operational",
         "logLevel": "information",
         "LogCategories": "Application,Events",
         "allowUnsecuredEndpoints": true,
         "telemetryOptOut": false
      }
      ```

      > [!NOTE]
      > `<Provisioning Token Value>` es un marcador de posición del token de aprovisionamiento capturado anteriormente en el paso 4 de la sección "Generación del token del módulo perimetral de Azure Video Analyzer".

1. Seleccione **Actualizar**.

1. Seleccione  **Rutas**.
1. En  **NOMBRE**, escriba  **AVAToHub**. En  **VALOR**, escriba  __FROM /messages/modules/avaedge/outputs/* INTO $upstream__. 
1. Quite la ruta **LVAToHub** seleccionando el icono de papelera que hay a la derecha.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-route.png" alt-text="Captura de pantalla que muestra cómo eliminar la ruta de I O T Edge de Live Video Analytics.":::

1. En **Módulos**, busque el módulo *lvaEdge* y quítelo seleccionando el icono de papelera de la derecha.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/delete-live-video-analytics-edge.png" alt-text="Captura de pantalla que muestra cómo eliminar el módulo IoT Edge Live Video Analytics.":::

1. Seleccione  **Revisar y crear** y, luego, elija  **Crear**  para implementar el módulo perimetral  **avaedge** . 

### <a name="verify-your-deployment"></a>Comprobación de la implementación

En la página de detalles del dispositivo, compruebe que el módulo  **avaedge**   aparece como  **Especificado en la implementación**  y  **Notificado por el dispositivo**. 

Los módulos pueden tardar unos minutos en iniciarse en el dispositivo y después notificarlo a IoT Hub. Actualice la página para ver el estado actualizado. El código de estado  **200 -- Correcto**  significa que el estado del  [entorno de ejecución de IoT Edge](../../iot-edge/iot-edge-runtime.md)  es bueno y que funciona bien. 

:::image type="content" source="./media/migrate-to-azure-video-analyzer/status-200.png" alt-text="Captura de pantalla que muestra que el entorno de ejecución de IoT Edge indica un estado de 200.":::

:::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-edge-running.png" alt-text="Captura de pantalla que muestra que el entorno de ejecución de IoT Edge informa de que todos los módulos se están ejecutando.":::

## <a name="convert-media-graph-topologies-to-azure-video-analyzer-pipelines"></a>Conversión de topologías de grafo multimedia en canalizaciones de Azure Video Analyzer

Existen algunas diferencias entre las topologías de grafo multimedia que usa Live Video Analytics y las topologías de canalización que usa Azure Video Analyzer.  Use la tabla siguiente para comparar las dos.

| Escenario                                                     | Grafo multimedia (Live Video Analytics)                                            | Canalización (Azure Video Analyzer)                                               |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ |
| Registro de un objeto de interés mediante varios modelos de IA         | [ai-composition](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/ai-composition/2.0/topology.json) | [ai-composition](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/ai-composition/topology.json) |
| Vídeo y audio                                              | [audio-video]()                                               | [audio-video]()                                               |
| Grabación continua de vídeo                                   | [cvr-asset](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/cvr-asset) | [cvr-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/cvr-video-sink) |
| Grabación e inferencia continuas de vídeo mediante la extensión gRPC | [cvr-with-grpcExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-grpcExtension/topology.json) | [cvr-with-grpcExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-grpcExtension/topology.json) |
| Grabación e inferencia continuas de vídeo mediante la extensión HTTP | [cvr-with-httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-httpExtension/2.0/topology.json) | [cvr-with-httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-httpExtension/topology.json) |
| Grabación continua de vídeo con detección de movimiento             | [cvr-with-motion](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/cvr-with-motion/2.0/topology.json) | [cvr-with-motion](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/cvr-with-motion/topology.json) |
| Grabación de vídeo basada en eventos en [vídeo o recursos de Azure Video Analyzer] en función de eventos de inteligencia artificial externa | [evr-grpcExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-grpcExtension-assets) | [evr-grpcExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sinkhttps://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-grpcExtension-video-sink) |
| Grabación de vídeo basada en eventos en [vídeo o recursos de Azure Video Analyzer] en función de eventos de inteligencia artificial externa | [evr-httpExtension-assets](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-httpExtension-assets) | [evr-httpExtension-video-sink](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/evr-httpExtension-video-sink) |
| Grabación de vídeo basada en eventos en [recursos/receptor de vídeo] en función de los objetos específicos detectados por el motor de inferencia externo | [evr-hubMessages-assets](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-assets/2.0/topology.json) | [evr-hubMessages-video-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-video-sink/topology.json) |
| Grabación de vídeo basada en eventos en archivos basados en mensajes enviados a través del centro de conectividad de IoT Edge | [evr-hubMessage-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-hubMessage-files/2.0/topology.json) | [evr-hubMessage-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-hubMessage-file-sink/topology.json) |
| Grabación de vídeo basada en eventos en archivos locales basados en eventos de movimiento | [evr-motion-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-files/2.0/topology.json) | [evr-motion-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-file-sink/topology.json) |
| Grabación de vídeo basada en eventos en [recursos/receptor de vídeo] y archivos locales basados en eventos de movimiento | [evr-motion-assets-files](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/evr-motion-assets-files/2.0/topology.json) | [evr-motion-video-sink-file-sink](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/evr-motion-video-sink-file-sink/topology.json) |
| Análisis de vídeo en directo mediante la extensión gRPC para enviar imágenes al módulo OpenVINO(TM) DL Streamer - Edge AI Extension desde Intel | [grpcExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/grpcExtensionOpenVINO/2.0/topology.json) | [grpcExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/grpcExtensionOpenVINO/topology.json) |
| Análisis de vídeo en directo mediante la extensión HTTP para enviar imágenes a un motor de inferencia externo. | [httpExtension](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/2.0/topology.json) | [httpExtension](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtension/topology.json) |
| Análisis de vídeo en directo mediante la extensión HTTP para enviar imágenes al módulo OpenVINO™ Model Server – AI Extension de Intel | [httpExtensionOpenVINO](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtensionOpenVINO/2.0/topology.json) | [httpExtensionOpenVINO](https://github.com/Azure/video-analyzer/blob/main/pipelines/live/topologies/httpExtensionOpenVINO/topology.json) |
| Análisis de vídeo en directo con Computer Vision para análisis espacial | [lva-spatial-analysis](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/lva-spatial-analysis/2.0/topology.json) | [spatial-analysis](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies/spatial-analysis) |

> [!NOTE]
> Puede encontrar y comparar las [topologías de grafo multimedia de Live Video Analytics](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies) y las [topologías de canalización de Video Analyzer](https://github.com/Azure/video-analyzer/tree/main/pipelines/live/topologies) en nuestra sección de ejemplos de GitHub. Al revisar estos ejemplos, podrá actualizar las topologías de grafo multimedia existentes con las topologías de canalización de Azure Video Analyzer.

### <a name="set-and-activate-the-pipeline-topology"></a>Establecimiento y activación de la topología de canalización

Después de convertir la topología de grafo multimedia (Live Video Analytics) en la topología de canalización (Azure Video Analyzer), deberá establecer la canalización en el módulo *avaedge*.

1. Para establecer la topología, seleccione el módulo perimetral Azure Video Analyzer en el dispositivo IoT Edge.  En el ejemplo siguiente, el nombre del contenedor es *avaedge*.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/azure-video-analyzer-container.png" alt-text="Captura de pantalla que muestra el módulo IoT Edge Live Video Analytics.":::

1. Seleccione **Método directo**.

    :::image type="content" source="./media/migrate-to-azure-video-analyzer/direct-method.png" alt-text="Captura de pantalla que muestra las llamadas de método directo de Live Video Analytics.":::

1. Escriba **pipelineTopologySet** en **Nombre del método** y especifique el código JSON de canalización convertido de la carga.

1. Seleccione **Invocar método**.

      En el campo de resultados, debería ver "status": 201 seguido de la topología.  que indica que se ha creado una topología.

1. A continuación, es necesario crear la canalización activa mediante la topología.  Use los pasos del 1 al 3, pero, en el campo de nombre del método, escriba **livePipelineSet** y en el campo de la carga especifique:

   ```JSON
         {
   "@apiVersion": "1.0",
   "name": "<live pipeline name>",
   "properties": {
      "topologyName": "<Name of the set topology>",
      "description": "Sample pipeline description",
      "parameters": [
         {
         "name": "rtspUrl",
         "value": "<RTSP camera string>"
         },
         {
         "name": "rtspUserName",
         "value": "<Camera User>"
         },
         {
         "name": "rtspPassword",
         "value": "Camera User Password"
         }
      ]
   }
   }
   ```

   > [!NOTE]
   > Reemplace los valores `<live pipeline name>`, `<Name of the set topology>`, `<RTSP camera string>` y `<Camera User Password>` por los de su entorno. 

1. Seleccione **Invocar método**.
   
      En el campo de resultados, debería ver "status": 201 seguido de la canalización activa.  Este estado indica que se ha creado una topología.

2. Por último, active la canalización activa siguiendo los pasos del 1 al 3; sin embargo, en **Nombre del método**, escriba **livePipelineActivate** y, en el campo de carga, especifique:

      ```JSON
         {
            "@apiVersion": "1.0",
            "name": "<live pipeline name>"
         }
      ```

    > [!NOTE]
    > Reemplace `<live pipeline name>` por el nombre de la canalización activa establecida anteriormente.

4. Seleccione **Invocar método**.
   
   En el campo de resultados, debería ver "status": 200.  Este estado indica que se ha activado una nueva canalización.

## <a name="direct-methods"></a>Métodos directos

Tanto Live Video Analytics como Azure Video Analyzer usan llamadas de método directo.  Como parte de la migración, tenga en cuenta también que los métodos directos se han actualizado y requiere la revisión de los flujos de trabajo que podrían usarlos en soluciones de Live Video Analytics.  En la tabla siguiente se muestran las diferencias entre las [llamadas de método directo de Live Video Analytics](direct-methods.md) y las [llamadas de método directo de Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/direct-methods.md).

| Análisis de vídeo en directo | Azure Video Analyzer |
| ------------------------ | ------------------------ |
| GraphTopologyList        | pipelineTopologyList     |
| GraphTopologySet         | pipelineTopologySet      |
| GraphTopologyDelete      | pipelineTopologyDelete   |
| GraphInstanceList        | livePipelineList         |
| GraphInstanceSet         | livePipelineSet          |
| GraphInstanceActivate    | livePipelineActivate     |
| GraphInstanceDeactivate  | livePipelineDeactivate   |
| GraphInstanceDelete      | livePipelineDelete       |

## <a name="miscellaneous-changes"></a>Cambios varios 

- Appsettings.json: actualice el archivo appsettings.json para que moduleId haga referencia a su módulo. Es decir, establézcalo en *avaeEdge*. 

- Hay un cambio menor en el [contrato gRPC](https://github.com/Azure/video-analyzer/tree/main/contracts/grpc). Aunque la extensión gRPC existente debería seguir funcionando, es aconsejable actualizar los archivos de prototipo. El cambio es una nueva línea en inferencing.proto : string sequence_id=14.

## <a name="next-steps"></a>Pasos siguientes

- Pruebe el  [inicio rápido para grabar vídeos en la nube al detectarse movimiento](../../azure-video-analyzer/video-analyzer-docs/detect-motion-record-video-clips-cloud.md). 
- Pruebe el  [inicio rápido para analizar vídeo en directo](../../azure-video-analyzer/video-analyzer-docs/analyze-live-video-use-your-model-http.md). 
- Revise la [terminología de Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/terminology.md). 
- Revise la [canalización de Azure Video Analyzer](../../azure-video-analyzer/video-analyzer-docs/pipeline.md). 