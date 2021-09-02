---
title: Configuración del dispositivo de Azure Percept Audio
description: Aprenda a conectar el dispositivo Azure Percept Audio con Azure Percept DK
author: MrHamlet
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: 8b6ec3ba8473be8e924d3c4b8ae17ccbdcf6568f
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123221838"
---
# <a name="set-up-the-azure-percept-audio-device"></a>Configuración del dispositivo de Azure Percept Audio

Azure Percept Audio está listo para usarse con Azure Percept DK. No es necesario realizar una instalación independiente.

## <a name="prerequisites"></a>Prerrequisitos

- Kit de desarrollo Azure Percept DK
- Azure Percept Audio
- [Suscripción de Azure](https://azure.microsoft.com/free/)
- [Experiencia de instalación de Azure Percept DK](./quickstart-percept-dk-set-up.md): ya ha conectado el dispositivo DevKit a una red Wi-Fi, creado una instancia de IoT Hub y conectado el dispositivo DevKit a esa instancia.
- Altavoces o auriculares que se pueden conectar a un conector de audio de 3,5 mm (opcional)

## <a name="connecting-your-devices"></a>Conexión de dispositivos

1. Conecte el dispositivo Azure Percept Audio a la placa base de Azure Percept DK con el cable micro USB tipo B a USB tipo A incluido. Conecte el extremo del cable micro USB a la placa del intermediador de Audio (desarrollador) y el extremo tipo A a la placa base de Percept DK.

1. (Opcional) Conecte el altavoz o los auriculares al dispositivo Azure Percept Audio mediante el conector de auriculares, etiquetado como "Salida de línea". Esto le permitirá escuchar las respuestas de audio.

1. Encienda el kit de desarrollo; para ello, conéctelo al adaptador de alimentación. El LED L02 pasará a parpadear en blanco para indicar que el dispositivo se ha encendido y se está autenticando.

1. Espere a que se complete el proceso de autenticación; que tarda hasta 5 minutos.

1. Estará listo para empezar a crear prototipos cuando vea uno de los estados siguientes de indicador LED:

    - El LED L02 cambiará a blanco fijo que indica que la autenticación se ha completado y que el kit de desarrollo se ha configurado sin una palabra clave.
    - Los tres indicadores LED se vuelven azules, que indica que la autenticación se ha completado y que el kit de desarrollo se ha configurado con una palabra clave.

## <a name="next-steps"></a>Pasos siguientes

Creación de una [solución de voz sin código](./tutorial-no-code-speech.md) en [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
