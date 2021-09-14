---
title: Configuración del dispositivo de Azure Percept DK
description: Configuración del dispositivo de Azure Percept DK y conexión a Azure IoT Hub
author: WendyAnn0079
ms.author: amiyouss
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/17/2021
ms.custom: template-quickstart
ms.openlocfilehash: 4e37be9e121d634e0d4a83fdd5f786805dedf132
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123427217"
---
# <a name="set-up-the-azure-percept-dk-device"></a>Configuración del dispositivo de Azure Percept DK

Complete la experiencia de configuración de Azure Percept DK para configurar el kit de desarrollo. Después de comprobar que la cuenta de Azure es compatible con Azure Percept, siga estos pasos:

- Inicio de la experiencia de instalación de Azure Percept DK
- Conectar su kit de desarrollo a una red Wi-Fi
- Configurar un inicio de sesión de SSH con fines de acceso remoto al kit de desarrollo
- Creación de un dispositivo en Azure IoT Hub

Si tiene algún problema durante el proceso, consulte las posibles soluciones en la [guía de solución de problemas](./how-to-troubleshoot-setup.md).

> [!NOTE]
> El servicio web de la experiencia de configuración se cerrará después de 30 minutos sin utilizarse. Si no puede conectarse al kit de desarrollo o no ve su punto de acceso Wi-Fi, reinicie el dispositivo.

## <a name="prerequisites"></a>Prerequisites

- Un kit de desarrollo de Azure Percept DK
- Un equipo host basado en Windows, OS X o Linux con funcionalidad Wi-Fi y un explorador web.
- Una suscripción de Azure activa. [Cree una cuenta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Los usuarios deben tener el rol de **propietario** o **colaborador** en la suscripción. Siga los pasos que se indican a continuación para comprobar el rol de la cuenta de Azure. Para más información sobre las definiciones de roles de Azure, consulte la [documentación de control de acceso basado en roles de Azure](../role-based-access-control/rbac-and-directory-admin-roles.md#azure-roles).

    > [!CAUTION]
    > Cierre todas las ventanas del explorador e inicie sesión en la suscripción a través de [Azure Portal](https://portal.azure.com/) antes de iniciar la experiencia de configuración. Consulte la [guía de solución de problemas](./how-to-troubleshoot-setup.md) para obtener más información sobre cómo asegurarse de que ha iniciado sesión con la cuenta correcta.

### <a name="check-your-azure-account-role"></a>Comprobación del rol de la cuenta de Azure

Para comprobar si la cuenta de Azure tiene los roles de "propietario" o "colaborador" en la suscripción, siga estos pasos:

1. Vaya a [Azure Portal](https://portal.azure.com/) e inicie sesión con la misma cuenta de Azure que va a usar con Azure Percept.

1. Seleccione el icono de las **suscripciones** (es similar a una llave amarilla).

1. Seleccione la suscripción en la lista. Si no ve su suscripción en la lista, asegúrese de haber iniciado sesión con la cuenta de Azure correcta. Si desea crear una suscripción, siga [estos pasos](../cost-management-billing/manage/create-subscription.md).

1. En el menú Suscripción, seleccione **Control de acceso (IAM)** .
1. Seleccione **View my access** (Ver mi acceso).
1. Compruebe el rol:
    - Si su rol aparece como **Lector** o usted recibe un mensaje indicando que no tiene permisos para ver los roles, debe seguir el proceso aplicable en su organización para elevar el rol de su cuenta.
    - Si su rol aparece como **propietario** o **colaborador**, la cuenta funcionará con Azure Percept y usted puede continuar con la experiencia de instalación.

## <a name="launch-the-azure-percept-dk-setup-experience"></a>Inicio de la experiencia de instalación de Azure Percept DK

1. Conecte el equipo host al punto de acceso Wi-Fi del kit de desarrollo. Seleccione la siguiente red y escriba la contraseña Wi-Fi cuando se le solicite:

    - **Nombre de red**: **scz-xxxx** o **apd-xxxx** (donde **xxxx** son los últimos cuatro dígitos de la dirección MAC del kit de desarrollo).
    - **Contraseña**: se encuentra en la tarjeta de bienvenida que se incluye en el kit de desarrollo.

    > [!WARNING]
    > Mientras está conectado al punto de acceso Wi-Fi de Azure Percept DK, el equipo host perderá temporalmente su conexión a Internet. Se interrumpirán las llamadas de videoconferencia en curso, las transmisiones web u otras experiencias que necesiten la red.

1. Una vez conectado al punto de acceso Wi-Fi del kit de desarrollo, el equipo host iniciará automáticamente la experiencia de instalación en una nueva ventana del explorador; y **your.new.device/** se mostrará en la barra de direcciones. Si la pestaña no se abre automáticamente, inicie la experiencia de instalación. Para ello, vaya a [http://10.1.1.1](http://10.1.1.1) en un explorador web. Asegúrese de que el explorador haya iniciado sesión con las credenciales de la cuenta de Azure que va a usar con Azure Percept.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-welcome.png" alt-text="Página de bienvenida.":::

    > [!NOTE]
    > **Usuarios de Mac**: al pasar por la experiencia de configuración en un equipo Mac, se abre inicialmente una ventana del portal cautivo que no puede completar la experiencia de configuración. Cierre esta ventana y abra un explorador web en https://10.1.1.1, lo que le permitirá completar la experiencia de configuración.

## <a name="connect-your-dev-kit-to-a-wi-fi-network"></a>Conectar su kit de desarrollo a una red Wi-Fi

1. En la pantalla de **bienvenida**, seleccione **Siguiente**.

1. En la página **Conexión de red**, seleccione **Connect to a new Wi-Fi network** (Conectarse a una nueva red Wi-Fi).

    Si ya ha conectado el kit de desarrollo a la red Wi-Fi, seleccione **Omitir**.

1. Seleccione la red Wi-Fi en la lista de redes disponibles y seleccione **Conectar**. Escriba la contraseña de la red cuando se le solicite.

    > [!NOTE]
    > Se recomienda establecer esta red como "Red preferida" (Mac) o marcar la casilla para "conectarse automáticamente" (Windows).  Esto permitirá que el equipo host se vuelva a conectar al punto de acceso de Wi-Fi del kit de desarrollo si la conexión se interrumpe durante este proceso.

1. Una vez que el kit de desarrollo se ha conectado correctamente, la página mostrará la dirección IPv4 asignada al kit de desarrollo. **Anote la dirección IPv4 que se muestra en la página.** Necesitará la dirección IP al conectarse al kit de desarrollo a través de SSH para solucionar problemas y actualizar los dispositivos.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-success-wi-fi.png" alt-text="Copia de la dirección IP.":::

    > [!NOTE]
    > La dirección IP puede cambiar con cada arranque del dispositivo.

1. Lea el contrato de licencia (debe desplazarse hasta la parte inferior del contrato), seleccione **I have read and agree to the License Agreement** (He leído y acepto el contrato de licencia), en la parte inferior del contrato, y seleccione **Siguiente**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-eula.png" alt-text="Aceptación del CLUF.":::

## <a name="set-up-an-ssh-login-for-remote-access-to-your-dev-kit"></a>Configurar un inicio de sesión de SSH con fines de acceso remoto al kit de desarrollo

1. Cree un nombre de cuenta SSH y una clave o contraseña pública y seleccione **Siguiente**.

    Si ya ha creado una cuenta de SSH, puede omitir este paso.

    **Anote la información de inicio de sesión para usarla más adelante**.

    > [!NOTE]
    > SSH (Secure Shell) es un protocolo de red que le permite conectarse al kit de desarrollo de forma remota a través de un equipo host.

##  <a name="create-a-new-device-in-azure-iot-hub"></a>Creación de un dispositivo en Azure IoT Hub

1. Seleccione **Setup as a new device** (Configurar como dispositivo nuevo) para crear un dispositivo en la cuenta de Azure.

    Ahora se obtendrá un código de dispositivo de Azure.

1. Seleccione **Copiar**.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-copy-code.png" alt-text="Copia del código del dispositivo.":::

    > [!NOTE]
    > Si recibe un error al usar el código de dispositivo en los pasos siguientes o si no se muestra el código del dispositivo, consulte los [pasos de solución de problemas](./how-to-troubleshoot-setup.md) para más información. 

1. Seleccione **Login to Azure** (Iniciar sesión en Azure).

1. Se abrirá una nueva pestaña del explorador con una ventana que indica **Enter code** (Escribir código). Pegue el código en la ventana y seleccione **Next** (Siguiente). NO cierre la pestaña de **bienvenida** con la experiencia de instalación.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-enter-code.png" alt-text="Escritura del código del dispositivo.":::

1. Inicie sesión en Azure Percept con las credenciales de la cuenta de Azure que va a usar con el kit de desarrollo. Cuando finalice, mantenga abierta la pestaña del explorador.

    > [!CAUTION]
    > El explorador puede almacenar en caché automáticamente otras credenciales. Compruebe que está iniciando sesión con la cuenta correcta.

    Después de iniciar sesión correctamente en Azure Percept en el dispositivo, seleccione **Permitir**. 
    
    Vuelva a la pestaña de **Bienvenida** con la experiencia de configuración.

1. Cuando aparezca la página **Assign your device to your Azure IoT Hub** (Asignación de su dispositivo a Azure IoT Hub) en la pestaña de **bienvenida**, siga uno de estos pasos:

    - Vaya a **Select your Azure IoT Hub** (Seleccionar su Azure IoT Hub), si el centro de IoT aparece en esta página.
    - Si no tiene ninguna instancia de IoT Hub o quiere crear una, seleccione **Create a new Azure IOT Hub** (Crear una instancia de Azure IoT Hub).

    > [!IMPORTANT]
    > Si tiene una instancia de IoT Hub, pero no aparece en la lista, es posible que haya iniciado sesión en Azure Percept con las credenciales erróneas. Consulte la [guía de solución de problemas de la instalación](./how-to-troubleshoot-setup.md) para obtener ayuda.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-iot-hub-select.png" alt-text="Selección de una instancia de IoT Hub.":::

1. Para crear una nueva instancia de IoT Hub,

    - Seleccione la suscripción de Azure que va a usar con Azure Percept.
    - Seleccione un grupo de recursos existente. Si no hay ninguno, seleccione **Crear** y siga las indicaciones.
    - Seleccione la región de Azure más próxima a su ubicación física.
    - Asigne un nombre a la nueva instancia de IoT Hub.
    - Seleccione el **plan de tarifa S1 (estándar)** .

    > [!NOTE]
    > La implementación de IoT Hub puede tardar unos minutos en completarse. Si necesita un mayor [rendimiento de los mensajes](../iot-hub/iot-hub-scaling.md#message-throughput) para las aplicaciones de IA perimetrales, tiene la posibilidad de [actualizar IoT Hub a un nivel estándar superior](../iot-hub/iot-hub-upgrade.md) en Azure Portal en cualquier momento. Los niveles B y F no admiten Azure Percept.

1. Cuando finalice, seleccione **Registrar**.

1. Seleccione la instancia de Azure IoT Hub.

1. Escriba un nombre de dispositivo para el kit de desarrollo y seleccione **Next** (Siguiente).

1. Los módulos de dispositivo ahora se implementarán en el dispositivo. Esta operación puede tardar unos minutos.

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-finalize.png" alt-text="Finalización de la instalación.":::

1. **Se ha completado la configuración del dispositivo.** El kit de desarrollo se ha vinculado correctamente a su IoT Hub y ha implementado todos los módulos.

    > [!NOTE]
    > Después de la finalización, el punto de acceso Wi-Fi del kit de desarrollo se desconectará automáticamente y el servicio web de la experiencia de configuración finalizará, lo que dará lugar a dos notificaciones.

    > [!NOTE]
    > Los contenedores de IoT Edge que se configuran como parte de este proceso usan certificados que expirarán a los 90 días. Los certificados se pueden volver a generar automáticamente. Para ello, solo hay que reiniciar IoT Edge. Para más información, consulte [Administración de certificados en un dispositivo IoT Edge](../iot-edge/how-to-manage-device-certificates.md).

1. Conecte el equipo host a la red Wi-Fi a la que conectó el kit de desarrollo.

1. Seleccione **Continue to the Azure portal** (Continuar en Azure Portal).

    :::image type="content" source="./media/quickstart-percept-dk-setup/main-Azure-portal-continue.png" alt-text="Ir a Azure Percept Studio.":::

### <a name="video-walk-through"></a>Vídeo paso a paso 
Vea el vídeo siguiente para un tutorial visual de los pasos descritos anteriormente.
> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Pasos siguientes

Ahora que el kit de desarrollo está configurado, es el momento de ver la inteligencia artificial de visión en acción.
- [Visualización de la secuencia de vídeo del kit de desarrollo](./how-to-view-video-stream.md)
- [Implementación de un modelo de inteligencia artificial de visión en el kit de desarrollo](./how-to-deploy-model.md)
