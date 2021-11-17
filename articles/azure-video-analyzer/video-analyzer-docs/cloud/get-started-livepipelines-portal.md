---
title: 'Introducción a las canalizaciones en directo de Azure Video Analyzer: Azure Portal'
description: Este inicio rápido le guía por los pasos necesarios para capturar y grabar vídeo desde una cámara RTSP mediante canalizaciones en directo en el servicio Azure Video Analyzer.
ms.service: azure-video-analyzer
ms.topic: quickstart
ms.date: 11/04/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: ed1c4e979d9b593cc701c294c95eda96b27db415
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554419"
---
# <a name="quickstart-get-started-with-video-analyzer-live-pipelines-in-the-azure-portal"></a>Inicio rápido: Introducción a las canalizaciones en directo de Video Analyzer en Azure Portal

[!INCLUDE [header](includes/cloud-env.md)]

Esta guía de inicio rápido le guía por los pasos necesarios para capturar y grabar vídeo desde una cámara de protocolo de streaming en tiempo real (RTSP) mediante canalizaciones en directo en el servicio Azure Video Analyzer.
Creará una cuenta de Video Analyzer y sus recursos complementarios mediante Azure Portal. Implementará un simulador de cámara RTSP si no tiene acceso a una cámara RTSP real (que se puede hacer accesible a través de Internet). A continuación, implementará los recursos pertinentes de Video Analyzer para grabar vídeo en su cuenta de Video Analyzer.

Los pasos descritos en este documento se aplican a las cámaras accesibles a través de Internet y no a las blindadas detrás de un firewall. El siguiente diagrama representa gráficamente la [canalización](../pipeline.md) en directo que se implementará en la cuenta de Video Analyzer.

> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/live-pipeline.svg" alt-text="Representación de una canalización en directo en la nube":::

## <a name="prerequisites"></a>Prerrequisitos

- Una suscripción de Azure activa. En caso de no tener ninguna, [cree una cuenta gratuita](https://azure.microsoft.com/free/).

  [!INCLUDE [the video analyzer account and storage account must be in the same subscription and region](../includes/note-account-storage-same-subscription.md)]
- Una cámara RTSP accesible a través de Internet o una máquina virtual Linux de Azure (con privilegios de administrador) para hospedar un simulador de cámara RTSP

## <a name="sample-architecture---recording-video-from-a-camera-over-the-internet"></a>Arquitectura de ejemplo: grabación de vídeo desde una cámara a través de Internet
> [!div class="mx-imgBorder"]
> :::image type="content" source="./media/public-camera-to-cloud-live-pipeline-arch.png" alt-text="Diagrama de una arquitectura de ejemplo de una fuente de vídeo de cámara pública que se integra con la canalización en directo de Video Analyzer que captura vídeos en la nube.":::

## <a name="rtsp-camera"></a>Cámara RTSP

Necesitará acceso a una cámara compatible con RTSP; consulte para ver las [cámaras compatibles](../quotas-limitations.md). La cámara se debe configurar para codificar vídeo con una velocidad de bits máxima inferior a 3 Mbps. Anote esta configuración de velocidad de bits máxima. Además, el servidor RTSP de esta cámara debe ser accesible a través de la red pública de Internet. Si puede usar este tipo de cámara, puede ir directamente a la sección Creación de recursos de Azure. Como alternativa, puede implementar un simulador de cámara RTSP tal como se describe en la sección siguiente.

## <a name="deploy-rtsp-camera-simulator"></a>Implementación del simulador de cámara RTSP

En esta sección se muestra cómo implementar un simulador de cámara RTSP en una máquina virtual Linux de Azure con el sistema operativo "Ubuntu Server 18.04". Este simulador utiliza [Live555 Media Server](http://www.live555.com/mediaServer/).

> [!NOTE]
> Las referencias a software de terceros son solo para fines informativos y prácticos. Microsoft no aprueba ni proporciona derechos para el software de terceros. Para más información, consulte [Live555 Media Server](http://www.live555.com/mediaServer/).

> [!WARNING]
> Tenga en cuenta que este punto de conexión del simulador de cámara RTSP se expone a través de Internet y, por lo tanto, será accesible para cualquier persona que conozca la dirección URL de RTSP.

**Pasos de implementación:**
1. Implemente una máquina virtual Linux de Azure de la serie standard_D2s_v3 que ejecute el sistema operativo "Ubuntu Server 18.04". [Consulte aquí](../../../virtual-machines/linux/quick-create-portal.md) los pasos para la creación de máquinas virtuales, no tiene que instalar el servidor web que se menciona en el artículo vinculado. Habilite también el puerto SSH en el Asistente para implementación a fin de que pueda conectarse a la máquina virtual mediante la conexión SSH.
1. Habilite las conexiones entrantes para el protocolo RTSP. En Azure Portal, abra el panel de administración para la máquina virtual Linux creada anteriormente.

    1. Haga clic en Redes: verá la hoja abierta a las reglas de puerto de entrada para el grupo de seguridad de red (NSG) creado para que admita conexiones SSH entrantes.
    1. Haga clic en Agregar regla de puerto de entrada para agregar una nueva regla.
    1. En el panel que se abre, cambie Intervalos de puertos de destino a 554. Elija un nombre para la regla, como "RTSP". Conserve todos los demás valores predeterminados. Obtenga más información [aquí](../../../virtual-machines/windows/nsg-quickstart-portal.md).
1. Instale Docker en la máquina virtual con las instrucciones que se indican [aquí](https://docs.docker.com/engine/install/ubuntu/). Siga solo los pasos hasta comprobar la instalación de Docker mediante la ejecución de la imagen "hello-world".
1. Conéctese a la máquina virtual, por ejemplo, mediante SSH. En la ventana del terminal, cree una carpeta local como "localmedia" para hospedar archivos multimedia. Esta carpeta local de máquina virtual se asignará al contenedor de servidor multimedia RTSP.
1. Copie un archivo MKV que se utiliza para simular la fuente de cámara de la siguiente manera:

    ```
    cd localmedia
    wget https://lvamedia.blob.core.windows.net/public/camera-1800s.mkv
    ```
1. Inicie el servidor RTSP en la máquina virtual mediante la imagen de contenedor pregenerada tal como se muestra a continuación.

    ```    
    sudo docker run -d -p 554:554 -v ${PWD}:/live/mediaServer/media mcr.microsoft.com/ava-utilities/rtspsim-live555:1.2
    ```
1. Una vez que el servidor RTSP se esté ejecutando, los clientes ya pueden conectarse a él mediante una dirección URL de RTSP:

    - Vaya a la página "Información general" de la máquina virtual en Azure Portal y anote el valor de "Dirección IP pública".
    
        - La dirección URL de RTSP es rtsp://{dirección IP pública}:554/media/camera-1800s.mkv y se puede probar con un reproductor desde el escritorio, por ejemplo, VLC.

## <a name="create-azure-resources"></a>Creación de recursos de Azure

El siguiente paso es crear los recursos de Azure necesarios (cuenta de Video Analyzer, cuenta de almacenamiento e identidad administrada asignada por el usuario).

### <a name="create-a-video-analyzer-account-in-the-azure-portal"></a>Creación de una cuenta de Video Analyzer en Azure Portal

1. Inicie sesión en [Azure Portal](https://portal.azure.com/).
1. En la barra de búsqueda de la parte superior, escriba **Video Analyzer**.
1. Seleccione **Video Analyzers** (Instancias de Video Analyzer) en **Servicios**.
1. Seleccione **Agregar**.
1. En la sección **Create Video Analyzer account** (Crear una cuenta de Video Analyzer) escriba los valores necesarios:

   - **Suscripción**: elija la suscripción que esté usando para crear la cuenta de Video Analyzer.
   - **Grupo de recursos**: elija un grupo de recursos en el que crear la cuenta de Video Analyzer o seleccione **Crear** para crear un nuevo grupo de recursos.
   - **Video Analyzer account name** (Nombre de la cuenta de Video Analyzer): este es el nombre de la cuenta de Video Analyzer. El nombre debe estar compuesto totalmente de minúsculas o números, sin espacios y con una longitud de entre 3 y 24 caracteres.
   - **Ubicación**: elija una ubicación para implementar la cuenta de Video Analyzer, por ejemplo, **Oeste de EE. UU. 2**.
   - **Cuenta de almacenamiento**: cree una cuenta de almacenamiento. Se recomienda seleccionar una cuenta de almacenamiento [estándar de uso general v2](../../../storage/common/storage-account-overview.md#types-of-storage-accounts).
   - **Identidad de usuario**: cree una nueva identidad administrada asignada por el usuario y asígnele un nombre.
1. En la parte inferior del formulario, seleccione **Revisar y crear**.

### <a name="deploy-a-live-pipeline"></a>Implementación de una canalización en directo

Una vez creada la cuenta de Video Analyzer, puede continuar con los pasos siguientes para crear una topología de canalización en directo y una canalización en directo.
1. Vaya a la cuenta de Video Analyzer, busque el elemento de menú **En directo** en la parte inferior izquierda y selecciónelo. 
1. En el plano de topologías, seleccione la opción **Crear topología** en la parte superior para crear una topología en directo. Siga los pasos del Asistente del portal para crear una topología de canalización en directo.

    - El Asistente para **crear una topología de canalización** aparecerá en el portal.
    - Seleccione **Try sample topologies** (Probar topologías de ejemplo) ->, luego, la topología **Live capture, record, and stream from RTSP camera** (Captura en directo, grabación y flujo de la cámara RTSP) -> y elija "Continuar" en el cuadro de diálogo **Load sample topology** (Cargar topología de ejemplo).
    - Aparecerá el asistente para crear la topología de canalización activa, que muestra el nodo de origen RTSP conectado a un nodo receptor de vídeo.
    - Escriba los campos necesarios para crear una topología: 
    
        - **Nombre de la topología**: escriba el nombre de la topología. 
        - **Descripción** (opcional): breve descripción sobre la topología 
        - **Tipo** ("En directo" previamente rellenado)
        - Para el nodo de **origen RTSP**: establezca el valor de la propiedad **Transport** como TCP.
        - Seleccione **Guardar** con la configuración predeterminada para el resto de las propiedades.
1. El siguiente paso consiste en crear una canalización en directo mediante la topología creada en el paso anterior. 

    - Seleccione **Canalizaciones** ->, luego, **Crear canalización** -> y, a continuación, la topología de canalización activa creada en el paso anterior para crear una canalización. Después de seleccionar la topología, haga clic en **Crear**.
    - Aparecerá el Asistente **para crear una canalización en directo** en el portal. Rellene los campos obligatorios: 
    
        - **Nombre de canalización en directo**: use un nombre único; permita el uso de caracteres alfanuméricos y guiones.
        - **Velocidad de bits**: es la capacidad máxima en Kbps que está reservada para la canalización en directo; el intervalo permitido es de 500 kbps a 3000 kbps. Use el valor predeterminado 1000 para el archivo camera-1800s.mkv del simulador de cámara RTSP (este valor debe coincidir con el archivo de vídeo de ejemplo usado). 
        - **rtspUserNameParameter**, **rtspPasswordParameter**: establezca unos valores ficticios para estos campos si utiliza el simulador de cámara RTSP; de lo contrario, escriba las credenciales de autenticación para el flujo de cámara RTSP real.
        - **rtspUrlParameter**: utilice `rtsp://<VMpublicIP>:554/media/camera-1800s.mkv` (para el simulador de cámara RTSP) o la dirección URL real del flujo de cámara RTSP.
        - **videoNameParameter**: nombre único del recurso de vídeo de destino que se va a grabar. Nota: Use un recurso de vídeo único para cada cámara (o archivo MKV)
    - Seleccione **Crear** y verá que se crea una canalización en la cuadrícula de canalizaciones del portal.
    - Seleccione la canalización en directo creada en la cuadrícula y la opción **Activar** disponible a la derecha del panel para activar la canalización en directo. Se iniciará la canalización en directo y se empezará a grabar el vídeo.
1. Ahora podrá ver el recurso de vídeo en el panel de la cuenta de Video Analyzer -> **Vídeos** del portal. Su estado indicará **En grabación**, ya que la canalización está activa y grabando el streaming de vídeo en directo.
1. Después de unos segundos, seleccione el vídeo y podrá ver el [flujo de baja latencia](../playback-recordings-how-to.md).

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/camera-1800s-mkv.png" alt-text="Diagrama del vídeo grabado capturado por una canalización en directo en la nube.":::

    > [!NOTE]
    > Si utiliza un simulador de cámara RTSP, no es posible determinar con precisión la latencia de un extremo a otro. Además, una vez que el simulador de cámara RTSP llegue al final del archivo MKV, se detendrá. La canalización en directo intentará volver a conectarse y, después de un tiempo, el simulador reiniciará la secuencia desde el principio del archivo. Si deja que esta canalización en directo se ejecute durante muchas horas, verá espacios en la grabación de vídeo cada vez que el simulador se detenga y se reinicie.
1. Si es necesario, consulte el Registro de actividad para comprobar rápidamente las operaciones de implementación. Consulte [aquí](./monitor-log-cloud.md) para ver los registros de eventos y de supervisión.
1. Para desactivar la grabación de canalización, vaya a su cuenta de Video Analyzer; en el panel izquierdo, seleccione **En directo**-> **Canalizaciones** ->, luego, la canalización que se va a desactivar y elija **Desactivar** en la cuadrícula de canalizaciones, tras lo que se detendrá la grabación. 
1. También puede continuar con la eliminación de la canalización y la topología si no son necesarias.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si quiere probar otros artículos de inicio rápidos o tutoriales, conserve los recursos que creó. En caso contrario, vaya a Azure Portal, luego a los grupos de recursos, seleccione el grupo de recursos en el que ejecutó este inicio rápido y elimine todos los recursos.

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre la administración de la [directiva de retención](../manage-retention-policy.md) de vídeo.
- Pruebe distintos archivos de ejemplo MKV para el simulador multimedia desde [aquí](https://github.com/Azure/video-analyzer/tree/main/media); la velocidad de bits del archivo de ejemplo debe coincidir con la configuración de la canalización.
- Más información sobre la [supervisión y registro para canalizaciones en la nube](./monitor-log-cloud.md).
