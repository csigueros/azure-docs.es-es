---
title: Cifrado de los datos en reposo de Azure IoT Hub mediante claves administradas por el cliente | Microsoft Docs
description: Cifrado de los datos en reposo de Azure IoT Hub mediante claves administradas por el cliente
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/07/2021
ms.author: asrastog
ms.openlocfilehash: 4dd3ee01504c2777ad72e32e11932b3b63d07448
ms.sourcegitcommit: 555ea0d06da38dea1de6ecbe0ed746cddd4566f5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2021
ms.locfileid: "113515535"
---
# <a name="encryption-of-azure-iot-hub-data-at-rest-using-customer-managed-keys"></a>Cifrado de los datos en reposo de Azure IoT Hub mediante claves administradas por el cliente

IoT Hub admite el cifrado de los datos en reposo con claves administradas por el cliente (CMK), también conocido como Bring your own key (BYOK). Azure IoT Hub proporciona cifrado de datos en reposo y en tránsito a medida que se escriben en nuestros centros de datos; los datos se cifran cuando se leen y se descifran cuando se escriben. 

De manera predeterminada, IoT Hub usa claves administradas por Microsoft para cifrar los datos. Con CMK, puede lograr otro nivel de cifrado adicional al cifrado predeterminado y puede optar por cifrar los datos en reposo con una clave de cifrado de claves, administrada a través de [Azure Key Vault](https://azure.microsoft.com/services/key-vault/). Esto le ofrece más flexibilidad para crear, rotar, deshabilitar y revocar controles de acceso. Si BYOK está configurado para IoT Hub, también proporcionamos el cifrado doble, que ofrece una segunda capa de protección, a la vez que permite controlar la clave de cifrado mediante Azure Key Vault.

Para usar esta funcionalidad, debe crear una nueva instancia de IoT Hub (nivel básico o estándar). Para probar esta funcionalidad, póngase en contacto con nosotros a través del [soporte técnico de Microsoft](https://azure.microsoft.com/support/create-ticket/). Mencione el nombre de la empresa y el Id. de suscripción cuando se ponga en contacto con el soporte técnico de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

* [¿Qué es IoT Hub?](./about-iot-hub.md)

* [Más información sobre Azure Key Vault](../key-vault/general/overview.md)
