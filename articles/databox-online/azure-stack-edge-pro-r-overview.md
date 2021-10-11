---
title: Introducción a Microsoft Azure Stack Edge Pro R | Microsoft Docs
description: Se describen los dispositivos Azure Stack Edge Pro R, una solución de almacenamiento que usa un dispositivo físico para la transferencia a través de la red a Azure y la solución se puede implementar en entornos exigentes.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/05/2021
ms.author: alkohli
ms.openlocfilehash: 6bf565a2f7d2d08931ca32f34ac07de2e8cec9b5
ms.sourcegitcommit: 57b7356981803f933cbf75e2d5285db73383947f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129544761"
---
# <a name="what-is-the-azure-stack-edge-pro-r"></a>¿Qué es Azure Stack Edge Pro R?

Azure Stack Edge Pro R es un resistente dispositivo informático perimetral diseñado para usarlo en entornos exigentes. Azure Stack Edge Pro R se entrega como una solución de hardware como servicio. Microsoft envía un dispositivo administrado en la nube que actúa como puerta de enlace de almacenamiento de red y tiene una unidad de procesamiento gráfico (GPU) integrada que habilita la inferencia de IA acelerada.

En este artículo se proporciona información general acerca de la solución de Azure Stack Edge Pro R, sus funcionalidades clave y los escenarios en los que puede implementar este dispositivo.


## <a name="key-capabilities"></a>Principales capacidades

Azure Stack Edge Pro R presenta las siguientes funcionalidades:

|Capacidad |Descripción  |
|---------|---------|
|Hardware resistente| Hardware de clase de servidor resistente diseñado para entornos exigentes. Dispositivo incluido en una caja de transporte portátil. |
|Administración en la nube     |El dispositivo y el servicio se administran con Azure Portal.|
|Cargas de trabajo de proceso perimetral   |Permite el análisis, procesamiento y filtrado de datos. Admite máquinas virtuales y cargas de trabajo en contenedores. <ul><li>Para obtener información sobre las cargas de trabajo de máquina virtual, consulte [Introducción a la máquina virtual en Azure Stack Edge](azure-stack-edge-gpu-virtual-machine-overview.md).</li> <li>Para cargas de trabajo en contenedores, consulte [Información general de Kubernetes en Azure Stack Edge](azure-stack-edge-gpu-kubernetes-overview.md).</li></ul> |
|Inferencia de IA acelerada| Habilitada por una GPU de NVIDIA T4. <br> Para obtener más información, consulte [Uso compartido de GPU en el dispositivo Azure Stack Edge](azure-stack-edge-gpu-sharing.md).|
|Acceso a datos     | Ofrece acceso directo a los datos de Azure Storage Blob y Azure Files mediante las API en la nube para un procesamiento de datos adicional en la nube. La memoria caché local del dispositivo se usa para agilizar el acceso a los archivos usados más recientemente.|
|Modo desconectado| El dispositivo y el servicio se pueden administrar a través de Azure Stack Hub. Implemente, ejecute y administre aplicaciones en modo sin conexión. <br> El modo sin conexión es compatible con escenarios de carga sin conexión.|
|Protocolos de transferencia de archivos compatibles     |Compatibilidad con los protocolos estándar SMB, NFS y REST para la ingesta de datos. <br> Para obtener más información sobre las versiones compatibles, vaya a [Requisitos del sistema de Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md).|
|Actualización de datos     | Capacidad para actualizar los archivos locales con la versión más reciente de la nube. <br> Para obtener más información, consulte [Actualización de un recurso compartido en Azure Stack Edge](azure-stack-edge-gpu-manage-shares.md#refresh-shares).|
|Cifrado doble    | El uso de unidades de cifrado automático proporciona la primera capa de cifrado. VPN proporciona la segunda capa de cifrado. Compatibilidad con BitLocker para cifrar los datos localmente y proteger las transferencias de datos en la nube mediante *HTTPS*. <br> Para más información, consulte [Configuración de VPN en el dispositivo Azure Stack Edge Pro R](azure-stack-edge-mini-r-configure-vpn-powershell.md).|
|Límite de ancho de banda| Limite el uso de ancho de banda durante las horas punta. <br> Para más información, consulte [Administración de las programaciones de ancho de banda de Azure Stack Edge](azure-stack-edge-gpu-manage-bandwidth-schedules.md).|
|Pedidos sencillos| Pedidos masivos y seguimiento del dispositivo mediante Azure Edge Hardware Center (versión preliminar). <br> Para más información, consulte [Pedido de un dispositivo a través de Azure Edge Hardware Center](azure-stack-edge-gpu-deploy-prep.md#create-a-new-resource). |

<!--|Scale out file server| Available as 1-node and 4-node cluster configurations|-->

## <a name="use-cases"></a>Casos de uso

Estos son los distintos escenarios en los que se puede usar Azure Stack Edge Pro R para la inferencia rápida de Machine Learning (ML) perimetral y el preprocesamiento de datos antes de enviarlos a Azure.

- **Inferencia con Azure Machine Learning**: con Azure Stack Edge Pro R, puede ejecutar modelos de ML para obtener resultados rápidos sobre los que se puede actuar antes de que los datos se envíen a la nube. El conjunto de datos completo se puede transferir opcionalmente para continuar entrenando y mejorando los modelos de Machine Learning. Para obtener más información sobre cómo usar los modelos acelerados por hardware de Azure ML en el dispositivo de Azure Stack Edge Pro R, consulte [Implementación de modelos acelerados por hardware de Azure ML en Azure Stack Edge Pro R](../machine-learning/how-to-deploy-fpga-web-service.md#deploy-to-a-local-edge-server).

- **Preprocesamiento de datos**: transforme datos antes de enviarlos a Azure para crear un conjunto de datos más accionable. El preprocesamiento se puede usar para:

    - Agregar datos.
    - Modificar datos, por ejemplo, para quitar los datos personales.
    - Subconjuntos de datos para optimizar el almacenamiento y el ancho de banda, o para un análisis más exhaustivo.
    - Analizar y reaccionar a los eventos de IoT.

- **Transferencia de datos a través de la red a Azure**: use Azure Stack Edge Pro R para transferir datos a Azure de forma rápida y sencilla a fin de poder realizar más procesos y análisis, o para fines de archivado.

## <a name="components"></a>Componentes

La solución Azure Stack Edge Pro R consta de un recurso Azure Stack Edge, un dispositivo físico Azure Stack Edge Pro R y una interfaz de usuario web local.

- **Dispositivo físico Azure Stack Edge Pro R**: un dispositivo de almacenamiento y proceso de un nodo incluido en una caja de transporte resistente. También está disponible un sistema de alimentación ininterrumpida (SAI) opcional.

    ![Para el dispositivo de un nodo Azure Stack Edge Pro R](media/azure-stack-edge-pro-r-overview/device-image-1.png)

    [!INCLUDE [azure-stack-edge-gateway-edge-hardware-center-overview](../../includes/azure-stack-edge-gateway-edge-hardware-center-overview.md)]    

    Para más información, vaya a [Creación de un pedido para el dispositivo Azure Stack Edge Pro R](azure-stack-edge-pro-r-deploy-prep.md#create-a-new-resource).

- **Recurso Azure Stack Edge**: recurso de Azure Portal que le permite administrar un dispositivo Azure Stack Edge Pro R resistente desde una interfaz web a la cual puede acceder desde diferentes ubicaciones geográficas. Use el recurso Azure Stack Edge para crear y administrar recursos, ver y administrar dispositivos y alertas y administrar recursos compartidos.  

- **Interfaz de usuario web local de Azure Stack Edge Pro R**: una interfaz de usuario web local basada en explorador que se encuentra en su dispositivo Azure Stack Edge Pro R y cuya finalidad principal es la configuración inicial del dispositivo. Además, use la interfaz de usuario web local para ejecutar diagnósticos, apagar y reiniciar el dispositivo de Azure Stack Edge Pro, ver registros de copia y ponerse en contacto con el Soporte técnico de Microsoft para realizar una solicitud de servicio.

    [!INCLUDE [azure-stack-edge-gateway-local-web-ui-languages](../../includes/azure-stack-edge-gateway-local-web-ui-languages.md)]


## <a name="region-availability"></a>Disponibilidad en regiones

El dispositivo físico Azure Stack Edge Pro R, el recurso de Azure y la cuenta de almacenamiento de destino a la que transferirá los datos no tienen que estar en la misma región.

- **Disponibilidad de recursos**: para obtener una lista de todas las regiones en que está disponible el recurso de Azure Stack Edge, vaya a [Productos de Azure disponibles por región](https://azure.microsoft.com/global-infrastructure/services/?products=databox&regions=all). 

- **Disponibilidad del dispositivo**: para ver una lista de todos los países en los que el dispositivo Azure Stack Edge Pro R está disponible, vaya a la sección **Availability** (Disponibilidad) de la pestaña **Azure Stack Edge Pro R** para conocer los [precios de Azure Stack Edge Pro R](https://azure.microsoft.com/pricing/details/azure-stack/edge/#azureStackEdgeProR).

- **Cuentas de almacenamiento de destino**: las cuentas de almacenamiento que almacenan los datos están disponibles en todas las regiones de Azure. Las regiones en las que las cuentas de almacenamiento almacenan los datos de Azure Stack Edge Pro R deben estar ubicadas cerca de la ubicación del dispositivo a fin de obtener un rendimiento óptimo. Una cuenta de almacenamiento que se encuentra lejos del dispositivo causa latencias largas y un rendimiento más lento.

El servicio Azure Stack Edge es un servicio no regional. Para obtener más información, consulte [Regiones y zonas de disponibilidad en Azure](../availability-zones/az-overview.md). El servicio Azure Stack Edge no depende de una región específica de Azure, lo que lo hace más resistentes a las interrupciones en toda la zona, así como a interrupciones en toda la región.

Para obtener una explicación de las consideraciones para elegir una región para el servicio Azure Stack Edge, el dispositivo y el almacenamiento de datos, consulte [Elección de una región para Azure Stack Edge](azure-stack-edge-gpu-regions.md).

## <a name="next-steps"></a>Pasos siguientes

- Revise los [requisitos del sistema de Azure Stack Edge Pro R](azure-stack-edge-gpu-system-requirements.md).
<!--- Understand the [Azure Stack Edge Pro R limits](azure-stack-edge-limits.md).-->
