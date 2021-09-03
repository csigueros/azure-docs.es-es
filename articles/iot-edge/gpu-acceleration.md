---
title: Aceleración de GPU en Azure IoT Edge para Linux en Windows | Microsoft Docs
description: Obtenga información sobre cómo configurar Azure IoT Edge para Linux en máquinas virtuales Windows para usar GPU de dispositivo host.
author: v-tcassi
manager: kgremban
ms.author: v-tcassi
ms.date: 06/22/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 2aa4dcc0aa4ecdd3b620aef874a0b5b9ceef4c96
ms.sourcegitcommit: cd7d099f4a8eedb8d8d2a8cae081b3abd968b827
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/25/2021
ms.locfileid: "112963553"
---
# <a name="gpu-acceleration-for-azure-iot-edge-for-linux-on-windows-preview"></a>Aceleración de GPU en Azure IoT Edge para Linux en Windows (versión preliminar)

Las GPU son una opción popular para los cálculos de inteligencia artificial, ya que ofrecen funcionalidades de procesamiento paralelo y a menudo pueden ejecutar inferencias basadas en la visión más rápido que las CPU. Para admitir mejor la inteligencia artificial y las aplicaciones de aprendizaje automático, Azure IoT Edge para Linux en Windows puede exponer una GPU al módulo de Linux de la máquina virtual.

> [!NOTE]
> Las características de aceleración de GPU que se detallan a continuación se encuentran en versión preliminar y están sujetas a cambios.

Azure IoT Edge para Linux en Windows admite varias tecnologías de paso a través de GPU, como las siguientes:

* **Asignación directa de dispositivos (DDA)** : los núcleos de GPU se asignan a la máquina virtual Linux o al host.

* **Paravirtualización de GPU (GPU-PV)** : la GPU se comparte entre la máquina virtual Linux y el host.

La implementación de Azure IoT Edge para Linux en Windows seleccionará automáticamente el método de acceso directo adecuado para que coincida con las funcionalidades admitidas del hardware de GPU del dispositivo.

> [!IMPORTANT]
> Estas características pueden incluir componentes desarrollados y propiedad de NVIDIA Corporation o sus emisores de licencias. El uso de los componentes se rige por el contrato de licencia para el usuario final de NVIDIA que se encuentra [en el sitio web de NVIDIA](https://www.nvidia.com/content/DriverDownload-March2009/licence.php?lang=us).
>
> Al usar las características de aceleración de GPU, acepta y está de acuerdo con los términos del contrato de licencia del usuario final de NVIDIA.

## <a name="prerequisites"></a>Requisitos previos

Las características de aceleración de GPU de Azure IoT Edge para Linux en Windows admiten actualmente un conjunto selecto de hardware de GPU. Además, para usar esta característica puede ser necesaria la última compilación del Canal de desarrolladores de Windows Insider, en función de la configuración.

A continuación se enumeran las GPU admitidas y las versiones de Windows necesarias:

* NVIDIA T4 (admite DDA)

  * Windows Server, compilación 17763 o posterior
  * Windows Enterprise o Professional, compilación 21318 o posterior (Windows Insider)

* NVIDIA GeForce/Quadro (admite GPU-PV)

  * Windows Enterprise o Professional, compilación 20145 o posterior (Windows Insider)

### <a name="windows-insider-builds"></a>Compilaciones de Windows Insider

Para los usuarios de Windows Enterprise o Professional, tendrá que [registrarse en el programa Windows Insider](https://insider.windows.com/getting-started#register).

Una vez que se registre, siga las instrucciones de la pestaña **2. Programa piloto** para obtener acceso a la compilación de Windows Insider adecuada. Al seleccionar el canal que quiera usar, seleccione el [Canal de desarrolladores](/windows-insider/flight-hub/#active-development-builds-of-windows-10). Después de la instalación, puede comprobar el número de versión de compilación si ejecuta `winver` desde el símbolo del sistema.

### <a name="t4-gpus"></a>GPU T4

Para las **GPU T4**, Microsoft recomienda instalar un controlador de mitigación de dispositivos del proveedor de GPU. Aunque es opcional, la instalación de un controlador de mitigación puede mejorar la seguridad de la implementación. Para obtener más información, vea [Implementación de dispositivos gráficos mediante la asignación directa de dispositivos](/windows-server/virtualization/hyper-v/deploy/deploying-graphics-devices-using-dda#optional---install-the-partitioning-driver).

> [!WARNING]
> La habilitación del acceso directo de dispositivos de hardware puede aumentar los riesgos de seguridad. Se recomienda instalar un controlador de mitigación de dispositivos del proveedor de GPU.

### <a name="geforcequadro-gpus"></a>GPU GeForce/Quadro

Para las **GPU GeForce/Quadro**, descargue e instale el [controlador habilitado para NVIDIA CUDA para Subsistema de Windows para Linux (WSL)](https://developer.nvidia.com/cuda/wsl) a fin de usarlo con los flujos de trabajo de ML CUDA existentes. Desarrollado originalmente para WSL, los controladores CUDA para WSL también se usan para Azure IoT Edge para Linux en Windows.

## <a name="using-gpu-acceleration-for-your-linux-on-windows-deployment"></a>Uso de la aceleración de GPU para la implementación de Linux en Windows

Ahora está listo para implementar y ejecutar módulos de Linux con aceleración de GPU en el entorno de Windows por medio de Azure IoT Edge para Linux en Windows. Puede encontrar más detalles sobre el proceso de implementación en [Instalación de Azure IoT Edge para Linux en Windows](how-to-install-iot-edge-on-windows.md).

## <a name="next-steps"></a>Pasos siguientes

* [Creación de la implementación de Azure IoT Edge para Linux en Windows](how-to-install-iot-edge-on-windows.md)

* Pruebe nuestro [ejemplo habilitado para GPU con Vision on Edge](https://github.com/Azure-Samples/azure-intelligent-edge-patterns/blob/master/factory-ai-vision/Tutorial/Eflow.md), una plantilla de solución en la que se ilustra cómo crear una aplicación de aprendizaje automático propia basada en la visión.

* Para obtener más información sobre las tecnologías de acceso directo de GPU, visite la [documentación de DDA](/windows-server/virtualization/hyper-v/plan/plan-for-gpu-acceleration-in-windows-server#discrete-device-assignment-dda) y la [entrada de blog de GPU-PV](https://devblogs.microsoft.com/directx/directx-heart-linux/#gpu-virtualization).
