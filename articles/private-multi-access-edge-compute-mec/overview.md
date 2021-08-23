---
title: Proceso perimetral multiacceso privado de Azure
description: Obtenga información sobre la solución de proceso perimetral multiacceso (MEC) privado de Azure que reúne una cartera de servicios de proceso, redes y aplicaciones de Microsoft administrados desde la nube.
author: niravi-msft
ms.service: private-multi-access-edge-compute-mec
ms.topic: overview
ms.date: 06/16/2021
ms.author: kumud
ms.openlocfilehash: d5c5c579ecc60e561d34b305468913d0a8635d81
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2021
ms.locfileid: "114286903"
---
# <a name="what-is-azure-private-multi-access-edge-compute"></a>¿Qué es el proceso perimetral multiacceso privado de Azure?

El proceso perimetral multiacceso (MEC) privado de Azure es una solución que reúne una cartera de servicios de proceso, redes y aplicaciones de Microsoft administrados desde la nube. La solución permite a los operadores e integrantes del sistema ofrecer alto rendimiento, conectividad de baja latencia y aplicaciones de IoT en el perímetro empresarial para la siguiente ola de transformación digital empresarial. 

MEC privado de Azure es una evolución de la zona perimetral privada, que amplía el ámbito de las posibilidades desde una única plataforma y servicio a una solución que saca partido de varias plataformas y funcionalidades. Estas funcionalidades incluyen aplicaciones y servicios perimetrales, funciones de red perimetral, opciones de proceso perimetral, así como radios y dispositivos perimetrales. Al procesar datos más cerca del dispositivo final, estas funcionalidades ayudan a mejorar los escenarios de usuario sensibles a la latencia y al rendimiento, como análisis de vídeo, robótica en tiempo real y casos de uso de IoT mixtos a escala global. Los clientes y asociados pueden beneficiarse de todo un conjunto de servicios de Azure y componentes de tecnología del ecosistema para compilar, implementar y administrar soluciones de manera rápida y sencilla. 

## <a name="benefits-to-customers-and-partners"></a>Ventajas para clientes y asociados
- Clientes empresariales:
    - Acceso a una cartera creciente de servicios de Azure, ISV y del ecosistema de desarrolladores.
    - Elección de soluciones de confianza adaptadas al sector.
    - Pila de tecnología totalmente integrada a partir de una superficie de 1U.

- Asociados de telecomunicaciones e integradores de sistemas:
    - Facilidad para adquirir, desplegar, gestionar y monetizar 5G y Edge para sus clientes.
    - Elección del ecosistema mantenido de productos, servicios en la nube y aplicaciones.
    - Acceso al ecosistema del desarrollador de Azure.

- ISV de aplicación:
    - Plataforma completa para desarrollar aplicaciones de latencia ultrabaja. 
    - Experiencia del desarrollador coherente con las herramientas y los recursos establecidos.
    - Escalado de la distribución mediante Microsoft Marketplace

## <a name="microsoft-capabilities"></a>Funcionalidades de Microsoft
MEC privado de Azure incluye varias funcionalidades de Microsoft. Entre ellos se incluyen una combinación de productos de funciones de red, servicios de administración, así como infraestructura y servicios de hardware. 

### <a name="azure-network-functions-offered-via-marketplace"></a>Funciones de red de Azure ofrecidas a través de Marketplace

**Metaswitch Fusion Core**: Fusion Core es una solución 5G Core totalmente contenedorizada que admite todas las funciones de red necesarias para la conectividad entre dispositivos IoT conectados a través de radio 4G o 5G a la red de datos. La solución ofrece algunas de las siguientes ventajas clave:
 - se implementan y aprovisionan fácilmente desde el portal de Azure Marketplace en Azure Stack Edge.
 - alto rendimiento de 25 Gbps en una superficie de proceso excepcionalmente baja de cuatro núcleos físicos.
 - compatibilidad con tipos de acceso independientes 4G y 5G.
 - herramientas integradas para paneles de KPI y control de servicios centrados en la empresa. 
 
Fusion Core permite a ISV implementar aplicaciones en el mismo nodo de Azure Stack Edge para aplicaciones IoT Edge como análisis de vídeo en directo. 

**Affirmed Private Network Service**: Affirmed Private Network Service es una solución de Azure Marketplace que ofrece un servicio administrado de red privada para operadores de red móvil y proveedores de servicios administrados que desean proporcionar ofertas de servicio administrado 4G y 5G a las empresas. APNS permite a los operadores proporcionar a las empresas una red móvil privada a nivel de operador que les permite ejecutar y operar aplicaciones críticas para la empresa, que requieren baja latencia, ancho de banda elevado y seguridad de un extremo a otro. Es un operador de red móvil integrada que proporciona una movilidad completa entre redes de operadores privadas y públicas. Con su automatización y operaciones simplificadas, APNS ofrece escalabilidad en miles de ubicaciones perimetrales empresariales y utiliza Azure para ofrecer una seguridad mejorada entre redes privadas y aplicaciones empresariales. Ofrece la flexibilidad de implementar todo el núcleo móvil en el perímetro de la red, todo en la nube o de un modo híbrido con el plano de control en la nube y el plano de usuario en el perímetro empresarial. 

### <a name="azure-management-services"></a>Servicios de administración de Azure

**Azure Network Functions Manager (NFM)** : Azure NFM permite la implementación de funciones de red en el perímetro mediante interfaces y herramientas de Azure coherentes. Utilice este servicio para implementar funciones de red de núcleo de paquetes y SD-WAN para Azure Stack Edge. Para más información, consulte [Azure Network Function Manager](../network-function-manager/overview.md).

**Kubernetes habilitado para Arc**: con Kubernetes habilitado para Azure Arc se pueden asociar y configurar clústeres de Kubernetes ubicados dentro o fuera de Azure. Puede supervisar y administrar a escala con unas implementaciones basadas en directivas y aplicar unas configuraciones de seguridad coherentes a escala. Kubernetes habilitado para Azure Arc funciona con todos los clústeres de Kubernetes con la certificación de Cloud Native Computing Foundation (CNCF). Para más información, consulte [Azure Arc](https://azure.microsoft.com/services/azure-arc/).

### <a name="azure-stack-hardware-and-services"></a>Hardware y servicios de Azure Stack
**Azure Stack Edge**: Azure Stack Edge ofrece una cartera de dispositivos que llevan el proceso, el almacenamiento y la inteligencia justo al perímetro donde se crean los datos. Se trata de dispositivos de montaje en bastidor de 1U que incluyen 1-2 GPU NVIDIA T4. Azure IoT Edge permite implementar y administrar contenedores desde IoT Hub e integrarlos con soluciones de Azure IoT en Azure Stack Edge. La SKU de Azure Stack Edge Pro está certificada para ejecutar funciones de red en el perímetro. Para más información, consulte [Azure Stack Edge](https://azure.microsoft.com/products/azure-stack/edge/).

**Azure Stack HCI**: Azure Stack HCI es un nuevo sistema operativo de infraestructura hiperconvergida (HCI) que se entrega como un servicio de Azure que proporciona las actualizaciones más recientes de características, rendimiento y seguridad. Implemente y ejecute máquinas virtuales de Windows y Linux en el centro de datos, o bien, en el perímetro mediante las herramientas y procesos existentes. Amplíe el centro de datos a la nube con Azure Backup, Azure Monitor y Azure Security Center. Para más información, consulte [Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/).

### <a name="application-services"></a>Servicios de aplicación

**Azure IoT Edge Runtime**: Azure IoT Edge Runtime permite administrar e implementar cargas de trabajo en la nube en dispositivos de proceso perimetral con las mismas herramientas y la misma posición de seguridad que las cargas de trabajo nativas en la nube. Para más información, consulte el artículo sobre [Azure IoT Edge Runtime](/windows/ai/windows-ml-container/iot-edge-runtime).

**Azure IoT Hub**: Azure IoT Edge Runtime permite administrar e implementar cargas de trabajo en la nube en dispositivos de proceso perimetral con las mismas herramientas y posición de seguridad que las cargas de trabajo nativas en la nube. Para más información, consulte [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/).

**Azure IoT Central**: Azure IoT Central es una plataforma de aplicaciones administradas que permite la administración de dispositivos y la ingesta de datos como servicio con un modelo de precios predecible y una escala global integrada. Para más información, consulte [Azure IoT Central](https://azure.microsoft.com/services/iot-central/).

**Azure Digital Twins**: Azure Digital Twins permite modelar los sensores de dispositivos en su contexto empresarial teniendo en cuenta las relaciones espaciales, los patrones de uso y otro tipo de contextos empresariales que convierten a una flota de dispositivos en una réplica digital de un recurso o entorno físico. Para más información, consulte [Azure Digital Twins](https://azure.microsoft.com/services/digital-twins/).

## <a name="next-steps"></a>Pasos siguientes
- Más información sobre [Metaswitch Fusion Core](metaswitch-fusion-core-overview.md)
- Más información sobre el [Servicio de red privada afirmada](affirmed-private-network-service-overview.md)