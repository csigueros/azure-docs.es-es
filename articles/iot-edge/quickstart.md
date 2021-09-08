---
title: Inicio rápido para crear un dispositivo de Azure IoT Edge en Windows | Microsoft Docs
description: En esta guía de inicio rápido, obtenga información sobre cómo crear un dispositivo IoT Edge y luego implementar código creado previamente de manera remota desde Azure Portal.
author: kgremban
manager: lizross
ms.author: kgremban
ms.reviewer: fcabrera
ms.date: 06/18/2021
ms.topic: quickstart
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, devx-track-azurecli
monikerRange: =iotedge-2018-06
ms.openlocfilehash: 3f0e2baa7ac30e0e75167dc603de73fdd1df8d73
ms.sourcegitcommit: f53f0b98031cd936b2cd509e2322b9ee1acba5d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123215372"
---
# <a name="quickstart-deploy-your-first-iot-edge-module-to-a-windows-device"></a>Inicio rápido: Implementación del primer módulo de IoT Edge en un dispositivo Windows

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

Pruebe Azure IoT Edge en este inicio rápido mediante la implementación de código en contenedor en un dispositivo IoT Edge para Linux en Windows. IoT Edge permite administrar de forma remota el código de los dispositivos para que pueda enviar más cargas de trabajo al perímetro. Para este inicio rápido, es aconsejable que use su propio dispositivo para ver lo fácil que es utilizar Azure IoT Edge para Linux en Windows.

En esta guía de inicio rápido, aprenderá a hacer lo siguiente:

* Crear un Centro de IoT.
* Registrar un dispositivo IoT Edge en su instancia de IoT Hub.
* Instalar e iniciar Azure IoT Edge para Linux en el runtime de Windows del dispositivo.
* Implementar un módulo de forma remota en un dispositivo IoT Edge y enviar datos de telemetría.

![Diagrama que muestra la arquitectura de este inicio rápido para el dispositivo y la nube.](./media/quickstart/install-edge-full.png)

En este inicio rápido se explica cómo configurar Azure IoT Edge para Linux en un dispositivo Windows. Después, podrá implementar un módulo de Azure Portal en el dispositivo. El módulo que utilizará es un sensor simulado que genera datos de temperatura, humedad y presión. Otros tutoriales de Azure IoT Edge se basan en el trabajo que se realiza aquí mediante la implementación de módulos que analizan los datos simulados para obtener información empresarial.

Si no tiene una suscripción activa a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free) antes de comenzar.

## <a name="prerequisites"></a>Requisitos previos

Prepare el entorno para la CLI de Azure.

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

Cree un grupo de recursos en la nube para administrar todos los recursos que va a usar en este inicio rápido.

   ```azurecli-interactive
   az group create --name IoTEdgeResources --location westus2
   ```

Asegúrese de que el dispositivo de IoT Edge cumpla con los requisitos siguientes:

* Ediciones
  * Windows 10, versión 1809 o posterior; compilación 17763 o posterior
    * Professional, Enterprise, IoT Enterprise
  * Windows Server 2019, compilación 17763 o posterior

* Requisitos de hardware
  * Memoria libre mínima: 1 GB
  * Espacio libre en disco mínimo: 10 GB

>[!NOTE]
>En este inicio rápido se utiliza PowerShell para crear una implementación de IoT Edge para Linux en Windows. También puede usar Windows Admin Center. Si quiere usar Windows Admin Center para crear la implementación, siga la guía paso a paso sobre [instalación y aprovisionamiento de Azure IoT Edge para Linux en un dispositivo Windows](how-to-install-iot-edge-on-windows.md?tabs=windowsadmincenter).

## <a name="create-an-iot-hub"></a>Crear un centro de IoT

Empiece por crear un centro de IoT con la CLI de Azure.

![Diagrama que muestra el paso para crear un centro de IoT.](./media/quickstart/create-iot-hub.png)

El nivel gratuito de Azure IoT Hub funciona para este inicio rápido. Si ha usado IoT Hub en el pasado y ya tiene un centro creado, puede usarlo.

El código siguiente crea un centro **F1** gratis en el grupo de recursos `IoTEdgeResources`. Reemplace `{hub_name}` por un nombre único para su centro de IoT. La creación de una instancia de IoT Hub puede tardar unos minutos.

```azurecli-interactive
az iot hub create --resource-group IoTEdgeResources --name {hub_name} --sku F1 --partition-count 2
```

Si se produce un error porque ya hay un centro gratuito en la suscripción, cambie la SKU a `S1`. Si recibe un error que le indica que el nombre del centro de IoT no está disponible, es que alguien más ya tiene un centro con ese nombre. Pruebe con uno nuevo.

## <a name="register-an-iot-edge-device"></a>Registro de un dispositivo de IoT Edge

Registre un dispositivo de IoT Edge con la instancia de IoT Hub recién creada.

![Diagrama que muestra el paso para registrar un dispositivo con una identidad de un centro de IoT.](./media/quickstart/register-device.png)

Cree una identidad para el dispositivo simulado, con el fin de que pueda comunicarse con su centro de IoT. La identidad del dispositivo reside en la nube, y se usa una cadena de conexión de dispositivo única para asociar un dispositivo físico a una identidad de dispositivo.

Los dispositivos de IoT Edge se comportan y se pueden administrar de manera diferente a los dispositivos de IoT típicos. Utilice la marca `--edge-enabled` para declarar que esta identidad es para un dispositivo IoT Edge.

1. En Azure Cloud Shell, escriba el comando siguiente para crear un dispositivo llamado **myEdgeDevice** en el centro.

     ```azurecli-interactive
     az iot hub device-identity create --device-id myEdgeDevice --edge-enabled --hub-name {hub_name}
     ```

     Si recibe un error acerca de las claves de directiva `iothubowner`, asegúrese de que Cloud Shell ejecuta la versión más reciente de la extensión Azure IoT.

1. Vea la cadena de conexión del dispositivo, que vincula el dispositivo físico con su identidad en IoT Hub. Contiene el nombre del centro de IoT, el nombre del dispositivo y una clave compartida que autentica las conexiones entre los dos.

     ```azurecli-interactive
     az iot hub device-identity connection-string show --device-id myEdgeDevice --hub-name {hub_name}
     ```

1. Copie el valor de la clave `connectionString` desde la salida JSON y guárdelo. Este valor es la cadena de conexión del dispositivo. Lo utilizará para configurar el tiempo de ejecución de IoT Edge en la sección siguiente.

     ![Captura de pantalla que muestra la salida connectionString en Cloud Shell.](./media/quickstart/retrieve-connection-string.png)

## <a name="install-and-start-the-iot-edge-runtime"></a>Instale e inicie el runtime de IoT Edge

Instale Azure IoT Edge para Linux en Windows en el dispositivo y configúrelo con la cadena de conexión del dispositivo.

![Diagrama que muestra el paso para iniciar el tiempo de ejecución de IoT Edge.](./media/quickstart/start-runtime.png)

Ejecute los siguientes comandos de PowerShell en el dispositivo de destino donde quiera implementar Azure IoT Edge para Linux en Windows. Para realizar la implementación en un dispositivo de destino remoto mediante PowerShell, puede usar [PowerShell remoto](/powershell/module/microsoft.powershell.core/about/about_remote) para establecer una conexión a un dispositivo remoto y ejecutar estos comandos de manera remota en ese dispositivo.

1. En una sesión de PowerShell con privilegios elevados, ejecute cada uno de los siguientes comandos para descargar IoT Edge para Linux en Windows.

   ```powershell
   $msiPath = $([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))
   $ProgressPreference = 'SilentlyContinue'
   Invoke-WebRequest "https://aka.ms/AzEflowMSI" -OutFile $msiPath
   ```

1. Instale IoT Edge para Linux en Windows en el dispositivo.

   ```powershell
   Start-Process -Wait msiexec -ArgumentList "/i","$([io.Path]::Combine($env:TEMP, 'AzureIoTEdge.msi'))","/qn"
   ```

1. Establezca la directiva de ejecución en el dispositivo de destino en `AllSigned` si todavía no lo está. Puede comprobar la directiva de ejecución actual en un símbolo del sistema de PowerShell con privilegios elevados con el siguiente comando:

   ```powershell
   Get-ExecutionPolicy -List
   ```

   Si la directiva de ejecución de `local machine` no es `AllSigned`, puede establecer la directiva de ejecución con el siguiente comando:

   ```powershell
   Set-ExecutionPolicy -ExecutionPolicy AllSigned -Force
   ```

1. Creación de la implementación de IoT Edge para Linux en Windows.

   ```powershell
   Deploy-Eflow
   ```

1. Escriba "Y" para aceptar los términos de licencia.

1. Escriba "O" o "R" para activar o desactivar la opción **Datos de diagnóstico opcionales** en función de sus preferencias. A continuación se muestra una implementación correcta.

   ![Una implementación correcta indicará "implementación correcta" al final de los mensajes.](./media/how-to-install-iot-edge-on-windows/successful-powershell-deployment-2.png)

1. Aprovisione el dispositivo con la cadena de conexión del dispositivo que ha recuperado en la sección anterior. Reemplace el texto de marcador de posición por un valor propio.

   ```powershell
   Provision-EflowVm -provisioningType ManualConnectionString -devConnString "<CONNECTION_STRING_HERE>"
   ```

El dispositivo de IoT Edge está ya configurado. Está preparado para ejecutar módulos implementados en la nube.

## <a name="deploy-a-module"></a>Implementación de un módulo

Administre el dispositivo Azure IoT Edge desde la nube para implementar un módulo que envía datos de telemetría a IoT Hub.

![Diagrama que muestra el paso para implementar un módulo.](./media/quickstart/deploy-module.png)

<!--
[!INCLUDE [iot-edge-deploy-module](../../includes/iot-edge-deploy-module.md)]

Include content included below to support versioned steps in Linux quickstart. Can update include file once Windows quickstart supports v1.2
-->

Una de las funcionalidades clave de Azure IoT Edge es la implementación de código en dispositivos de IoT Edge desde la nube. Los *módulos de IoT Edge* son paquetes ejecutables que se implementan como contenedores. En esta sección, va a implementar un módulo pregenerado desde la [sección de módulos de IoT Edge de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) directamente desde la instancia de Azure IoT Hub.

El módulo que se implementa en esta sección simula un sensor y envía los datos generados. Este módulo es un fragmento de código útil para empezar a trabajar con IoT Edge porque se pueden usar los datos simulados para desarrollo y pruebas. Si desea ver exactamente lo que hace este módulo, puede ver el [código fuente del sensor de temperatura simulado](https://github.com/Azure/iotedge/blob/027a509549a248647ed41ca7fe1dc508771c8123/edge-modules/SimulatedTemperatureSensor/src/Program.cs).

Siga estos pasos para implementar el primer módulo desde Azure Marketplace.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya al centro de IoT.

1. En el menú de la izquierda, en **Administración automática de dispositivos**, seleccione **IoT Edge**.

1. Seleccione el identificador del dispositivo de destino en la lista de dispositivos.

   Al crear un nuevo dispositivo IoT Edge, se mostrará el código de estado `417 -- The device's deployment configuration is not set` en Azure Portal. Este estado es normal y significa que el dispositivo está listo para recibir una implementación de módulo.


1. En la barra superior, seleccione **Establecer módulos**.

   ![Captura de pantalla que muestra la selección de Establecer módulos.](./media/quickstart/select-set-modules.png)

1. En **Módulos de IoT Edge**, abra el menú desplegable **Agregar** y, a continuación, seleccione **Módulo de Marketplace**.

   ![Captura de pantalla que muestra el menú desplegable Agregar.](./media/quickstart/add-marketplace-module.png)

1. En **Marketplace de módulos IoT Edge**, busque y seleccione el módulo `Simulated Temperature Sensor`.

   El módulo se agrega a la sección Módulos de IoT Edge con el estado **en ejecución** deseado.

1. Seleccione **Siguiente: Rutas** para continuar con el paso siguiente del asistente.

   ![Captura de pantalla que muestra cómo continuar con el paso siguiente después de agregar el módulo.](./media/quickstart/view-temperature-sensor-next-routes.png)

1. En la pestaña **Rutas**, quite la ruta predeterminada (**route**) y, a continuación, seleccione **Siguiente: Revisar y crear** para continuar al próximo paso del asistente.

   >[!Note]
   >Las rutas se construyen mediante pares de nombre-valor. Debería ver dos rutas en esta página. La ruta predeterminada (**route**) envía todos los mensajes a IoT Hub (que se denomina `$upstream`). Una segunda ruta (**SimulatedTemperatureSensorToIoTHub**) se creó automáticamente al agregar el módulo desde Azure Marketplace. Esta ruta envía todos los mensajes desde el módulo de temperatura simulado a IoT Hub. Puede eliminar la ruta predeterminada porque es redundante en este caso.

   ![Captura de pantalla que muestra cómo quitar la ruta predeterminada y pasar al paso siguiente.](./media/quickstart/delete-route-next-review-create.png)

1. Revise el archivo JSON y luego seleccione **Crear**. El archivo JSON define todos los módulos que se implementan en el dispositivo IoT Edge. Verá el módulo **SimulatedTemperatureSensor** y los dos módulos en tiempo de ejecución, **edgeAgent** y **edgeHub**.

   >[!Note]
   >Cuando se envía una implementación nueva a un dispositivo IoT Edge, no se inserta nada en el dispositivo. En lugar de eso, el dispositivo consulta a IoT Hub de manera periódica para comprobar cualquier instrucción nueva. Si el dispositivo encuentra un manifiesto de implementación actualizado, usa la información sobre la nueva implementación para extraer las imágenes del módulo de la nube y, después, comienza a ejecutar localmente los módulos. Este proceso puede tardar unos minutos.

1. Después de crear los detalles de la implementación del módulo, el asistente lo lleva nuevamente a la página de detalles del dispositivo. Vea el estado de implementación en la pestaña **Módulos**.

   Deben aparecer tres módulos: **$edgeAgent**, **$edgeHub** y **SimulatedTemperatureSensor**. Si se muestra **SÍ** para uno o varios de los módulos en **ESPECIFICADO EN LA IMPLEMENTACIÓN** pero no en **NOTIFICADO POR EL DISPOSITIVO**, significa que el dispositivo IoT Edge todavía los está iniciando. Espere unos minutos y actualice la página.

   ![Captura de pantalla que muestra el sensor de temperatura simulado en la lista de módulos implementados.](./media/quickstart/view-deployed-modules.png)

## <a name="view-the-generated-data"></a>Visualización de los datos generados

En esta guía de inicio rápido, ha creado un nuevo dispositivo de IoT Edge y ha instalado el runtime de IoT Edge en él. Luego, ha usado Azure Portal para implementar un módulo de IoT Edge para que se ejecute en el dispositivo sin tener que realizar cambios en el propio dispositivo.

El módulo que ha insertado genera los datos del entorno de ejemplo que puede usar posteriormente para las pruebas. El sensor simulado está supervisando una máquina y el entorno alrededor de esta. Por ejemplo, este sensor podría estar en una sala de servidores, en una fábrica o en un aerogenerador. Los mensajes que envía incluyen la temperatura y la humedad ambientales, la temperatura y la presión de la máquina y una marca de tiempo. Los tutoriales de IoT Edge usan los datos creados por este módulo como datos de prueba con fines de análisis.

1. Inicie sesión en la instancia de IoT Edge para Linux en la máquina virtual Windows con el siguiente comando en la sesión de PowerShell:

   ```powershell
   Connect-EflowVm
   ```

   >[!NOTE]
   >La única cuenta que se permite a SSH en la máquina virtual es la del usuario que la creó.

1. Una vez que haya iniciado sesión, puede comprobar la lista de módulos de IoT Edge en ejecución con el comando de Linux siguiente:

   ```bash
   sudo iotedge list
   ```

   ![Compruebe que el sensor de temperatura, el agente y el centro están en ejecución.](./media/quickstart/iotedge-list-screen.png)

1. Vea los mensajes que se envían desde el módulo de sensor de temperatura a la nube por medio del siguiente comando de Linux:

   ```bash
   sudo iotedge logs SimulatedTemperatureSensor -f
   ```

   >[!IMPORTANT]
   >Los comandos de IoT Edge distinguen las mayúsculas de las minúsculas en los nombres de los módulos.

   ![Vea los registros de salida del módulo Sensor de temperatura simulado.](./media/quickstart/temperature-sensor-screen.png)

También puede utilizar la [extensión de Azure IoT Hub para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) para ver los mensajes que llegan a su centro de IoT.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si desea continuar con los tutoriales de IoT Edge, omita este paso. Puede utilizar el dispositivo que ha registrado y configurado en este inicio rápido. En caso contrario, puede eliminar los recursos de Azure que creó para evitar gastos.

Si ha creado una máquina virtual y un centro de IoT en un nuevo grupo de recursos, puede eliminar dicho grupo y todos los recursos asociados. Si no desea eliminar todo el grupo, puede eliminar recursos individuales en su lugar.

> [!IMPORTANT]
> Compruebe el contenido del grupo de recursos para asegurarse de que no haya nada que desee conservar. La eliminación de un grupo de recursos es irreversible.

Utilice el comando siguiente para quitar el grupo **IoTEdgeResources**. La eliminación puede tardar unos minutos.

```azurecli-interactive
az group delete --name IoTEdgeResources
```

Puede confirmar que se ha eliminado el grupo de recursos mediante este comando para ver la lista de grupos de recursos.

```azurecli-interactive
az group list
```

### <a name="remove-azure-iot-edge-for-linux-on-windows"></a>Eliminación de Azure IoT Edge para Linux en Windows

Utilice la extensión del panel en Windows Admin Center a fin de desinstalar Azure IoT Edge para Linux en Windows.

1. Conéctese al dispositivo IoT Edge en Windows Admin Center. Se carga la extensión de la herramienta del panel de Azure.

1. Seleccione **Desinstalar**. Después de quitar Azure IoT Edge, Windows Admin Center quita la entrada de conexión de dispositivo de Azure IoT Edge de la página **Inicio**.

>[!Note]
>Otra forma de quitar Azure IoT Edge del sistema Windows consiste en seleccionar **Inicio** > **Configuración** > **Aplicaciones** > **Azure IoT Edge LTS** > **Desinstalar** en el dispositivo IoT Edge. Este método eliminará Azure IoT Edge del dispositivo IoT Edge, pero dejará la conexión atrás en Windows Admin Center. Para completar la eliminación, desinstale también Windows Admin Center en el menú **Settings** (Configuración).

## <a name="next-steps"></a>Pasos siguientes

En esta guía de inicio rápido, ha creado un dispositivo IoT Edge y ha usado la interfaz en la nube de Azure IoT Edge para implementar el código en el dispositivo. Ahora tiene un dispositivo de prueba que genera datos sin procesar acerca de su entorno.

En el siguiente tutorial, aprenderá a supervisar la actividad y el estado del dispositivo desde Azure Portal.

> [!div class="nextstepaction"]
> [Supervisión de los dispositivos IoT Edge](tutorial-monitor-with-workbooks.md)
