---
ms.topic: include
ms.date: 10/29/2021
author: kgremban
ms.author: kgremban
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 92c933cf2f504459359652124f72ca959241db92
ms.sourcegitcommit: 2cc9695ae394adae60161bc0e6e0e166440a0730
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131505959"
---
## <a name="create-a-dps-enrollment"></a>Crear una inscripción de DPS

Cree una inscripción para aprovisionar uno o varios dispositivos mediante DPS.

Si desea aprovisionar un único dispositivo IoT Edge, cree una **inscripción individual**. Si necesita aprovisionar varios dispositivos, siga los pasos para crear una **inscripción de grupo** de DPS.

Al crear una inscripción en DPS, tiene la oportunidad de declarar un **estado inicial de dispositivo gemelo**. En el dispositivo gemelo, puede establecer etiquetas para agrupar dispositivos por cualquier métrica que necesite en su solución, como la región, el entorno, la ubicación o el tipo de dispositivo. Estas etiquetas se usan para crear [implementaciones automáticas](../articles/iot-edge/how-to-deploy-at-scale.md).

Para más información sobre las inscripciones en el servicio de aprovisionamiento de dispositivos, consulte [Administración de inscripciones de dispositivos](../articles/iot-dps/how-to-manage-enrollments.md).

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

### <a name="create-a-dps-individual-enrollment"></a>Creación de una inscripción individual de DPS

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones individuales mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment](/cli/azure/iot/dps/enrollment). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para un dispositivo de IoT Edge.

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia del servicio de aprovisionamiento de dispositivos de IoT Hub.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Add individual enrollment** (Agregar inscripción individual) y, a continuación, complete los pasos siguientes para configurar la inscripción:  

   1. En **Mecanismo**, seleccione **Clave simétrica**.

   1. Proporcione un **Id. de registro** único para el dispositivo.

   1. De manera opcional, proporcione un **Id. de dispositivo IoT Hub** para el dispositivo. Puede usar identificadores de dispositivo para dirigirse a un dispositivo individual para la implementación del módulo. Si no proporciona un id. de dispositivo, se usará el id. de registro.

   1. Seleccione **Verdadero** para declarar que la inscripción es para un dispositivo IoT Edge.

   1. De manera opcional, agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Por ejemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Seleccione **Guardar**.

1. Copie el valor de **Clave principal** de la inscripción individual para usarlo al instalar el entorno de ejecución de Azure IoT Edge.

Ahora que existe una inscripción para este dispositivo, el entorno de ejecución de Azure IoT Edge puede aprovisionar automáticamente el dispositivo durante la instalación.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

### <a name="create-a-dps-group-enrollment"></a>Creación de una inscripción de grupo de DPS

> [!TIP]
> Los pasos de este artículo son para Azure Portal, pero también puede crear inscripciones de grupo mediante la CLI de Azure. Para más información, consulte [az iot dps enrollment-group](/cli/azure/iot/dps/enrollment-group). Como parte del comando de la CLI, use la marca **edge-enabled** para especificar que la inscripción es para dispositivos de IoT Edge. En el caso de una inscripción de grupo, todos los dispositivos deben ser dispositivos IoT Edge, o bien ninguno puede serlo.

1. En [Azure Portal](https://portal.azure.com), navegue hasta la instancia del servicio de aprovisionamiento de dispositivos de IoT Hub.

1. En **Configuración**, seleccione **Administrar inscripciones**.

1. Seleccione **Add individual enrollment** (Agregar inscripción individual) y, a continuación, complete los pasos siguientes para configurar la inscripción:  

   1. Escriba un **Nombre de grupo**.

   1. Seleccione **Clave simétrica** como tipo de atestación.

   1. Seleccione **Verdadero** para declarar que la inscripción es para un dispositivo IoT Edge. En el caso de una inscripción de grupo, todos los dispositivos deben ser dispositivos IoT Edge, o bien ninguno puede serlo.

   1. De manera opcional, agregue un valor de etiqueta a **Estado inicial de dispositivo gemelo**. Puede usar etiquetas para los grupos de dispositivos de destino para la implementación del módulo. Por ejemplo:

      ```json
      {
         "tags": {
            "environment": "test"
         },
         "properties": {
            "desired": {}
         }
      }
      ```

   1. Seleccione **Guardar**.

1. Copie el valor de **Clave principal** del grupo de inscripción para usarlo al crear claves de dispositivo para usarlas con una inscripción del grupo.

Ahora que existe un grupo de inscripciones, el entorno de ejecución de Azure IoT Edge puede aprovisionar los dispositivos durante la instalación de forma automática.

#### <a name="derive-a-device-key"></a>Derivación de una clave de dispositivo

Cada dispositivo que se aprovisiona como parte de una inscripción de grupo necesita una clave de dispositivo derivada para realizar la atestación de clave simétrica con la inscripción durante el aprovisionamiento.

Para generar la clave del dispositivo, use la clave que copió del grupo de inscripciones de DPS para calcular un valor [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) del identificador de registro único del dispositivo y convierta el resultado a formato Base64.

> [!IMPORTANT]
> No incluya la clave principal o secundaria de la inscripción en el código del dispositivo.

En Windows, puede usar PowerShell para generar las claves de dispositivo derivadas tal y como se muestra en el ejemplo siguiente.

Reemplace el valor de **KEY** por el de la **clave principal** que ha apuntado anteriormente.

Reemplace el valor de **REG_ID** por el identificador de registro del dispositivo.

```powershell
$KEY='PASTE_YOUR_ENROLLMENT_KEY_HERE'
$REG_ID='PASTE_YOUR_REGISTRATION_ID_HERE'

$hmacsha256 = New-Object System.Security.Cryptography.HMACSHA256
$hmacsha256.key = [Convert]::FromBase64String($KEY)
$sig = $hmacsha256.ComputeHash([Text.Encoding]::ASCII.GetBytes($REG_ID))
$derivedkey = [Convert]::ToBase64String($sig)
echo "`n$derivedkey`n"
```

A continuación se muestra una salida de ejemplo de una clave de dispositivo derivada:

```powershell
Jsm0lyGpjaVYVP2g3FnmnmG9dI/9qU24wNoykUmermc=
```

---
