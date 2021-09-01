---
title: Virtualization anidada en Azure IoT Edge para Linux en Windows | Microsoft Docs
description: Obtenga información sobre cómo navegar por la virtualización anidada en Azure IoT Edge para Linux en Windows.
author: fcabrera
manager: kgremban
ms.author: fcabrera
ms.date: 2/24/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
monikerRange: =iotedge-2018-06
ms.openlocfilehash: e583808b0be0ff4105abc438ace1b52b9d3317eb
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726506"
---
# <a name="nested-virtualization-for-azure-iot-edge-for-linux-on-windows"></a>Virtualización anidada en Azure IoT Edge para Linux en Windows
Hay dos formas de hacer que la virtualización anidada sea compatible con Azure IoT Edge para Linux en Windows. Los usuarios pueden implementar una máquina virtual local o una máquina virtual de Azure. En este artículo se explica a los usuarios cuál es la opción más adecuada en su caso y se proporciona información sobre los requisitos de configuración.

> [!NOTE]
>
> Asegúrese de habilitar una [opción de red](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization#networking-options) para la virtualización anidada. De no hacerlo, se producirán errores de instalación de EFLOW. 

## <a name="deployment-on-local-vm"></a>Implementación en una máquina virtual local

Este es el enfoque de base de referencia para cualquier máquina virtual de Windows que hospede Azure IoT Edge para Linux en Windows. En este caso, la virtualización anidada deberá habilitarse antes de iniciar la implementación. Lea el artículo [Ejecución de Hyper-V en una máquina virtual con la virtualización anidada](/virtualization/hyper-v-on-windows/user-guide/nested-virtualization) para obtener más información sobre cómo configurar este escenario.

Si usa Windows Server, asegúrese de [instalar el rol de Hyper-V](/windows-server/virtualization/hyper-v/get-started/install-the-hyper-v-role-on-windows-server).

## <a name="deployment-on-azure-vms"></a>Implementación en máquinas virtuales de Azure

Azure IoT Edge para Linux en Windows no es compatible con una máquina virtual de Azure que ejecuta la SKU del servidor, a menos que se utilice un script que obtenga un modificador predeterminado. Para más información sobre cómo activar un conmutador predeterminado, consulte [Creación de un conmutador virtual para Linux en Windows](how-to-create-virtual-switch.md).

> [!NOTE]
>
> Cualquier máquina virtual de Azure que supuestamente hospede EFLOW debe [admitir la virtualización anidada](../virtual-machines/acu.md).
