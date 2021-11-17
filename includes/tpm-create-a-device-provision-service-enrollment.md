---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 6bc968f7763131c4c12e7d523619029698e4ac09
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131579354"
---
## <a name="create-a-device-provisioning-service-enrollment"></a>Creación de una inscripción del servicio de aprovisionamiento de dispositivos

Use la información de aprovisionamiento del TPM para crear una inscripción individual en servicio de aprovisionamiento de dispositivos.

Al crear una inscripción en el servicio de aprovisionamiento de dispositivos, tiene la oportunidad de declarar un **estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica utilizada en la solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](../articles/iot-edge/how-to-deploy-at-scale.md).

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones individuales mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para un dispositivo de IoT Edge.

1. En [Azure Portal](https://portal.azure.com), vaya a la instancia del servicio de aprovisionamiento de dispositivos de IoT Hub.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Agregar inscripción individual** y, después, complete los pasos siguientes para configurar la inscripción:

   1. En **Mecanismo**, seleccione **TPM**.

   1. Proporcione la **clave de aprobación** y el **identificador de registro** que ha copiado de la máquina virtual o el dispositivo físico.

   1. Si quiere, proporcione un identificador para el dispositivo. Si no proporciona un id. de dispositivo, se usará el id. de registro.

   1. Seleccione **Verdadero** para declarar que esta máquina virtual o el dispositivo físico es un dispositivo IoT Edge.

   1. Elija el centro de IoT vinculado al que quiere conectar el dispositivo o seleccione **Link to new IoT Hub** (Vincular a un nuevo centro de IoT). Puede elegir varios centros y el dispositivo se asignará a uno de ellos según la directiva de asignación seleccionada.

   1. Si quiere, agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Para más información, consulte [Implementación de módulos IoT Edge a escala](../articles/iot-edge/how-to-deploy-at-scale.md).

   1. Seleccione **Guardar**.

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación.
