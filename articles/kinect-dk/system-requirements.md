---
title: Requisitos del sistema del SDK de Sensor de Azure Kinect
description: Comprenda los requisitos del sistema para el SDK del Sensor de Azure Kinect en Windows y Linux.
author: qm13
ms.author: quentinm
ms.custom: CI 115266, CSSTroubleshooting
manager: dcscontentpm
ms.prod: kinect-dk
ms.date: 03/05/2021
ms.topic: article
keywords: azure, kinect, requisitos del sistema, CPU, GPU, USB, instalar, instalación, mínimo, requisitos
ms.openlocfilehash: ba6782ad0a0278f1f66266c8abbe91d33cb5bf68
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131085855"
---
# <a name="azure-kinect-sensor-sdk-system-requirements"></a>Requisitos del sistema del SDK de Sensor de Azure Kinect

En este documento se proporcionan detalles sobre los requisitos del sistema necesarios para instalar el SDK del sensor e implementar correctamente Azure Kinect DK.

## <a name="supported-operating-systems-and-architectures"></a>Sistemas operativos y arquitecturas compatibles

- Versión de abril de 2018 de Windows 10 (versión 1803, compilación 17134) (x64) o una versión posterior
- Linux Ubuntu 18.04 (x64), con un controlador de GPU que use OpenGL v4.4 o una versión posterior

El SDK del sensor está disponible para la API de Windows (Win32) para aplicaciones Windows nativas de C/C++. El SDK actualmente no está disponible para las aplicaciones para UWP. Azure Kinect DK no es compatible con Windows 10 en modo S.

## <a name="development-environment-requirements"></a>Requisitos del entorno de desarrollo

Para contribuir al desarrollo del SDK del sensor, visite [GitHub](https://github.com/Microsoft/Azure-Kinect-Sensor-SDK).

## <a name="minimum-host-pc-hardware-requirements"></a>Requisitos mínimos de hardware del equipo host

El requisito de hardware del equipo host depende de la aplicación/algoritmo/velocidad de fotogramas del sensor/resolución que se ejecutan en el equipo host. La configuración mínima recomendada del SDK del sensor para Windows es:

- Procesador Intel&reg; CoreTM i3 de 7.ª gen. (Dual Core de 2,4 GHz con GPU HD620 o superior)
- 4 GB de memoria
- Puerto USB3 dedicado
- Compatibilidad del controlador de gráficos con OpenGL 4.4 o DirectX 11.0

Las CPU de menor nivel o anteriores también pueden funcionar según el caso de uso.

El rendimiento también difiere entre los sistemas operativos Windows y Linux y los controladores de gráficos que se usen.

## <a name="body-tracking-host-pc-hardware-requirements"></a>Requisitos de hardware del equipo host de seguimiento de personas

Los requisitos del equipo host de seguimiento de personas son más estrictos que los requisitos del equipo host general. La configuración mínima recomendada del SDK de seguimiento de personas para Windows es:

- Procesador Intel&reg; CoreTM i5 de 7.ª gen. (Quad Core de 2,4 GHz o superior)
- 4 GB de memoria
- NVIDIA GEFORCE GTX 1050 o equivalente
- Puerto USB3 dedicado

La configuración mínima recomendada supone el modo de profundidad K4A_DEPTH_MODE_NFOV_UNBINNED a 30 fps siguiendo a 5 personas. Las CPU y GPU NVIDIA de menor nivel o anteriores también pueden funcionar según el caso de uso.

## <a name="usb3"></a>USB3

Existen problemas de compatibilidad conocidos con los controladores de host USB. Puede encontrar más información en la [página de solución de problemas](troubleshooting.md#usb3-host-controller-compatibility).

## <a name="next-steps"></a>Pasos siguientes

- [Información general de Azure Kinect DK](about-azure-kinect-dk.md)

- [Configuración de Azure Kinect DK](set-up-azure-kinect-dk.md)

- [Configuración del seguimiento corporal de Azure Kinect](body-sdk-setup.md)
