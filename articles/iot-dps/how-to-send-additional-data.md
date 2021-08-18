---
title: Transferencia de la carga útil entre un dispositivo y el servicio Azure Device Provisioning
description: En este documento se describe cómo transferir una carga útil entre el dispositivo y el servicio Device Provisioning (DPS).
author: wesmc7777
ms.author: wesmc
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: ff8157a938c3b754a35c5a588ac5590be2432971
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121739240"
---
# <a name="how-to-transfer-payloads-between-devices-and-dps"></a>Transferencia de cargas útiles entre dispositivos y DPS
A veces, DPS necesita más datos de los dispositivos a fin de aprovisionarlos adecuadamente en la instancia correcta de IoT Hub, y el dispositivo necesita proporcionar los datos. A la inversa, DPS puede devolver datos al dispositivo para facilitar la lógica del cliente. 

## <a name="when-to-use-it"></a>Cuándo se debe usar
Esta característica se puede usar como una mejora de la [asignación personalizada](./how-to-use-custom-allocation-policies.md). Por ejemplo, quiere asignar los dispositivos en función de su modelo sin la intervención de personas. En este caso, usará [asignación personalizada](./how-to-use-custom-allocation-policies.md). Puede configurar el dispositivo para que incluya la información del modelo como parte de la [llamada de registro del dispositivo](/rest/api/iot-dps/device/runtime-registration/register-device). DPS pasará la carga útil del dispositivo en el webhook de asignación personalizada. Y la función puede decidir a qué instancia de IoT Hub se dirigirá el dispositivo cuando reciba información del modelo de dispositivo. Asimismo, si el webhook quiere devolver algunos datos al dispositivo, pasará los datos como cadena en la respuesta del webhook.  

## <a name="device-sends-data-payload-to-dps"></a>El dispositivo envía la carga útil de datos a DPS
Cuando su dispositivo envía una [llamada de dispositivo de registro](/rest/api/iot-dps/device/runtime-registration/register-device) a DPS, la llamada de registro se puede mejorar para que incorpore otros campos en el cuerpo. El cuerpo será similar al siguiente: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>Devolución de datos de DPS al dispositivo
Si el webhook de la directiva de asignación personalizada quiere devolver algunos datos al dispositivo, pasará los datos como cadena en la respuesta del webhook. El cambio está en la sección de carga útil más adelante. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Compatibilidad con SDK
Esta característica está disponible en los [SDK de cliente](./index.yml) de C, C#, JAVA y Node.js.  

## <a name="next-steps"></a>Pasos siguientes
* Desarrollo con el [SDK de IoT de Azure]( https://github.com/Azure/azure-iot-sdks) para Azure IoT Hub y Azure IoT Hub Device Provisioning Service
