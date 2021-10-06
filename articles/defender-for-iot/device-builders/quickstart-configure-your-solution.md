---
title: 'Inicio rápido: Incorporación de recursos de Azure en la solución de IoT'
description: En esta guía de inicio rápido, aprenda a configurar la solución de IoT de un extremo a otro mediante Azure Defender para IoT.
ms.topic: quickstart
ms.date: 09/13/2021
ms.openlocfilehash: e66accdc1ba671941c13433eeca9b3e9c541781b
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128634831"
---
# <a name="quickstart-configure-your-azure-defender-for-iot-solution"></a>Inicio rápido: Configuración de la solución de Azure Defender para IoT

En este artículo se explica cómo configurar la solución de seguridad de IoT mediante Defender para IoT por primera vez.

## <a name="prerequisites"></a>Requisitos previos

- Ninguno

## <a name="what-is-defender-for-iot"></a>¿Qué es Defender para IoT?

Defender para IoT proporciona una seguridad completa, de un extremo a otro, para las soluciones de IoT que usen Azure.

Con Defender para IoT se puede supervisar toda una solución de IoT desde un solo panel, así como presentar todos los dispositivos de IoT, las plataformas de IoT y los recursos back-end de Azure.

Una vez que haya habilitado Defender para IoT en su instancia de IoT Hub, Defender para IoT identificará automáticamente otros servicios de Azure y se conectará a los servicios relacionados que están asociados a la solución de IoT.

También podrá seleccionar otros grupos de recursos de Azure que forman parte de la solución de IoT.

Las opciones que se seleccionen permiten agregar suscripciones completas, grupos de recursos o recursos individuales.

Después de definir todas las relaciones de los recursos, Defender para IoT utilizará Defender para generar alertas y recomendaciones de seguridad relativas a estos recursos.

## <a name="add-azure-resources-to-your-iot-solution"></a>Adición de recursos de Azure a una solución de IoT

**Para agregar un nuevo recurso a la solución de IoT**:

1. En Azure Portal, busque y seleccione **IoT Hub**.

1. En la sección Seguridad, seleccione **Configuración** > **Recursos supervisados.** .

1. Seleccione **Editar** y los recursos supervisados que pertenezcan a la solución de IoT.

1. Seleccione **Agregar**.

Ahora se agregará un nuevo grupo de recursos a la solución de IoT.

Defender para IoT ahora supervisará los grupos de recursos que acaba de agregar y muestra alertas y recomendaciones de seguridad pertinentes como parte de la solución de IoT.

## <a name="next-steps"></a>Pasos siguientes

Pase al siguiente artículo para aprender a crear microagentes de Defender para IoT...

> [!div class="nextstepaction"]
> [Creación de microagentes de Defender para IoT](quickstart-create-security-twin.md)
