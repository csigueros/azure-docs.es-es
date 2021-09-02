---
title: Seguridad de Azure Percept
description: Más información acerca de la seguridad de Azure Percept.
author: mimcco
ms.author: amiyouss
ms.service: azure-percept
ms.topic: conceptual
ms.date: 03/24/2021
ms.custom: template-concept
ms.openlocfilehash: cfee4f14b5e7f135c7bbe609b5f3453b5fce8589
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221949"
---
# <a name="azure-percept-security"></a>Seguridad de Azure Percept

Los dispositivos de Azure Percept están diseñados con una raíz de confianza del hardware. Esta seguridad integrada le ayuda a proteger los datos de inferencia y los sensores con acceso a información confidencial de la privacidad, como cámaras y micrófonos, y permite realizar la autenticación y autorización del dispositivo para los servicios de Azure Percept Studio.

> [!NOTE]
> Azure Percept DK solo tiene licencia para usarse en entornos de desarrollo y de prueba.

## <a name="devices"></a>Dispositivos

### <a name="azure-percept-dk"></a>Azure Percept DK

Azure Percept DK incluye la versión 2.0 de un módulo de plataforma de confianza (TPM) que se puede usar para conectar el dispositivo con seguridad adicional a los servicios de aprovisionamiento de dispositivos (DPS) de Azure. TPM es un estándar ISO de Trusted Computing Group para todo el sector. Puede obtener más información sobre TPM en [la especificación TPM 2.0 completa](https://trustedcomputinggroup.org/resource/tpm-library-specification/) o la especificación ISO/IEC 11889. Para obtener más información sobre cómo DPS puede aprovisionar dispositivos de forma segura, consulte [Azure IoT Hub Device Provisioning Service: Atestación de TPM](../iot-dps/concepts-tpm-attestation.md).

### <a name="azure-percept-system-on-modules-soms"></a>Módulo de sistema Azure Percept (SoMs)

El Módulo de sistema habilitado para Azure Percept Vision y el módulo de sistema de Azure Percept Audio incluyen una unidad con microcontrolador (MCU) para proteger el acceso a los sensores integrados de la inteligencia artificial. En cada arranque, el firmware de la MCU autentica y autoriza el acelerador de inteligencia artificial en los servicios de Azure Percept Studio mediante la arquitectura del motor Device Identifier Composition Engine (DICE). DICE funciona dividiendo el arranque en capas y creando secretos de dispositivo únicos (UDS) para cada capa y configuración. Si se arranca con otro código o configuración, en cualquier punto de la cadena, los secretos serán diferentes. Para obtener más información sobre DICE, consulte las [especificaciones del grupo de trabajo de DICE](https://trustedcomputinggroup.org/work-groups/dice-architectures/). Para configurar el acceso a Azure Percept Studio y a los servicios necesarios, consulte el artículo [Configuración de firewalls para Azure Percept DK](concept-security-configuration.md).

Los dispositivos de Azure Percept usan la raíz de confianza del hardware para proteger el firmware. La ROM de arranque garantiza la integridad del firmware entre la ROM y el cargador del sistema operativo que, a su vez, garantiza la integridad de los demás componentes de software mediante la creación de una cadena de confianza.

## <a name="services"></a>Servicios

### <a name="iot-edge"></a>IoT Edge

Azure Percept DK se conecta a Azure Percept Studio con seguridad adicional y a otros servicios de Azure mediante el protocolo de Seguridad de la capa de transporte (TLS). Azure Percept DK es un dispositivo habilitado para Azure IoT Edge. El entorno de ejecución de IoT Edge es una colección de programas que convierten un dispositivo en un dispositivo IoT Edge. Colectivamente, los componentes del entorno de ejecución de Azure IoT Edge permiten que los dispositivos IoT Edge reciban el código para ejecutarse en el perímetro y comunicar los resultados. Azure Percept DK emplea contenedores de Docker para aislar cargas de trabajo de IoT Edge del sistema operativo host y de las aplicaciones habilitadas para Edge. Para más información acerca del marco de seguridad de Azure IoT Edge, consulte [Administrador de seguridad de IoT Edge](../iot-edge/iot-edge-security-manager.md).

### <a name="device-update-for-iot-hub"></a>Device Update para IoT Hub

Device Update para IoT Hub habilita una actualización más segura, escalable y confiable que proporciona seguridad renovable a los dispositivos Azure Percept. Proporciona controles de administración enriquecidos y comprobación de actualizaciones a través de información. Azure Percept DK incluye una solución de actualización de dispositivos preintegrada que proporciona una actualización resistente (A/B) desde el firmware a las capas del sistema operativo.

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Obtenga más información sobre las configuraciones de firewall y las recomendaciones de seguridad](concept-security-configuration.md).

> [!div class="nextstepaction"]
> [Compre un Azure Percept DK en la tienda en línea de Microsoft](https://go.microsoft.com/fwlink/p/?LinkId=2155270)
