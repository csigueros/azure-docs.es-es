---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4fe6d59d88cf4b0c32da4639a3f1132a82974c28
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131845954"
---
### <a name="cloud-resources"></a>Recursos en la nube

* Un centro de IoT activo.
* Una instancia de IoT Hub Device Provisioning Service en Azure que esté vinculada a IoT Hub.
  * Si no tiene una instancia de Device Provisioning Service, puede seguir las instrucciones de las secciones [Creación de una instancia de IoT Hub Device Provisioning Service](../articles/iot-dps/quick-setup-auto-provision.md#create-a-new-iot-hub-device-provisioning-service) y [Vínculo al centro de IoT y a Device Provisioning Service](../articles/iot-dps/quick-setup-auto-provision.md#link-the-iot-hub-and-your-device-provisioning-service) del inicio rápido de IoT Hub Device Provisioning Service.
  * Cuando Device Provisioning Service esté en ejecución, copie el valor de **Ámbito de id.** de la página de información general. Use este valor cuando configure el entorno de ejecución de IoT Edge.
