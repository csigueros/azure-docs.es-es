---
title: 'Tutorial: Generación de datos de dispositivos simulados: Machine Learning en Azure IoT Edge'
description: 'Tutorial: Creación de dispositivos virtuales que generan datos de telemetría simulados que pueden utilizarse posteriormente para entrenar un modelo de aprendizaje automático.'
author: kgremban
ms.author: kgremban
ms.date: 1/20/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e6383ff947832fa94d0e65b3a021d99ede6247bc
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726425"
---
# <a name="tutorial-generate-simulated-device-data"></a>Tutorial: Generación de datos de dispositivos simulados

[!INCLUDE [iot-edge-version-201806](../../includes/iot-edge-version-201806.md)]

En este artículo, se usan datos de entrenamiento de aprendizaje automático para simular un dispositivo que envía datos de telemetría a Azure IoT Hub. Tal y como se indica en la introducción, este tutorial usa el [conjunto de datos de simulación de degradación del motor de turbofán](https://c3.nasa.gov/dashlink/resources/139/) para simular datos de un conjunto de motores de avión para entrenamiento y pruebas.

En nuestro escenario experimental, sabemos que:

* Los datos constan de varias series temporales multivariantes.
* Cada conjunto de datos se divide en subconjuntos de entrenamiento y prueba.
* Cada serie temporal proviene de un motor distinto.
* Cada motor se inicia con diferentes grados de desgaste inicial y variación de fabricación.

Para este tutorial, se usa el subconjunto de datos de entrenamiento de un único conjunto de datos (FD003).

En realidad, cada motor sería un dispositivo IoT independiente. Suponiendo que no tenga disponible una colección de motores de turbofán conectados a internet, crearemos un software sustituto para estos dispositivos.

El simulador es un programa de C# que usa las API de IoT Hub para registrar dispositivos virtuales con IoT Hub mediante programación. A continuación, se leen los datos de cada dispositivo del subconjunto de datos proporcionado por la NASA y se envían a su instancia de IoT Hub mediante un dispositivo IoT simulado. Todo el código para esta parte del tutorial está disponible en el directorio DeviceHarness del repositorio.

El proyecto DeviceHarness es un proyecto de .NET Core escrito en C# que consta de cuatro clases:

* **Program:** el punto de entrada para la ejecución responsable de controlar la entrada del usuario y la coordinación general.
* **TrainingFileManager:** responsable de leer y analizar el archivo de datos seleccionado.
* **CycleData:** representa una sola fila de datos de un archivo convertido al formato de mensaje.
* **TurbofanDevice:** responsable de crear un dispositivo IoT que se corresponde con un único dispositivo (serie temporal) en los datos y de transmitir los datos a IoT Hub.

Las tareas descritas en este artículo deberían tardar unos 20 minutos en completarse.

En un contexto real, el trabajo de este paso seguramente lo realizarían desarrolladores de dispositivos y desarrolladores de nube.

En esta sección del tutorial, aprenderá a:

> [!div class="checklist"]
>
> * Agregar un proyecto externo al entorno de desarrollo.
> * Usar el proyecto DeviceHarness de ejemplo para generar datos de dispositivo IoT simulados.
> * Ver los datos generados en su centro de IoT.

## <a name="prerequisites"></a>Requisitos previos

Este artículo forma parte de un tutorial sobre el uso de Azure Machine Learning en IoT Edge. Cada artículo de la serie se basa en el trabajo del artículo anterior. Si ha llegado a este artículo directamente, visite el [primer artículo](tutorial-machine-learning-edge-01-intro.md) de la serie.

## <a name="configure-visual-studio-code-and-build-deviceharness-project"></a>Configuración de Visual Studio Code y compilación del proyecto DeviceHarness

1. Abra una sesión de Escritorio remoto en la máquina virtual de desarrollo.

1. En Visual Studio Code, abra la carpeta `C:\source\IoTEdgeAndMlSample\DeviceHarness`.

1. Puesto que es la primera vez que usa extensiones en esta máquina, algunas extensiones se actualizarán e instalarán sus dependencias. Es posible que se le solicite que actualice la extensión. En tal caso, seleccione **Recargar ventana**.

   Si aparecen errores de OmniSharp en la ventana de salida, deberá desinstalar la extensión de C#.

1. Se le indicará que agregue los recursos necesarios para DeviceHarness. Seleccione **Sí** para agregarlos.

   * Es posible que la notificación tarde unos segundos en aparecer.
   * Si no ve la notificación, compruebe el icono de campana en la esquina inferior derecha.

   ![Mensaje emergente de la extensión de VS Code](media/tutorial-machine-learning-edge-03-generate-data/add-required-assets.png)

1. Seleccione **Restaurar** para restaurar las dependencias del paquete.

   ![Mensaje de restauración de VS Code](media/tutorial-machine-learning-edge-03-generate-data/restore-package-dependencies.png)

   Si no recibe estas notificaciones, cierre Visual Studio Code, elimine los directorios bin y obj de `C:\source\IoTEdgeAndMlSample\DeviceHarness`, abra Visual Studio Code y vuelva a abrir la carpeta DeviceHarness.

1. Para comprobar que el entorno está configurado correctamente, active una compilación, **Ctrl** + **Mayús** + **B**, o **Terminal** > **Ejecutar tarea de compilación**.

1. Se le pedirá que seleccione la tarea de compilación que se va a ejecutar. Seleccione **Build** (Compilar).

1. La compilación se ejecutará y generará un mensaje de operación correcta.

   ![Mensaje de salida de compilación correcta](media/tutorial-machine-learning-edge-03-generate-data/build-success.png)

1. Puede establecer esta compilación como tarea de compilación predeterminada si selecciona **Terminal** > **Configure Default Build Task...**  (Configurar tarea de compilación predeterminada) y elige **Compilar** en el mensaje.

## <a name="connect-to-iot-hub-and-run-deviceharness"></a>Conexión a IoT Hub y ejecución de DeviceHarness

Ahora que el proyecto se está compilando, conéctese a su instancia de IoT Hub para acceder a la cadena de conexión y supervisar el progreso de la generación de datos.

### <a name="sign-in-to-azure-in-visual-studio-code"></a>Inicio de sesión en Azure en Visual Studio Code

1. Inicie sesión en su suscripción de Azure en Visual Studio Code, abra la paleta de comandos, `Ctrl + Shift + P` o **Ver** > **Paleta de comandos**.

1. Busque el comando **Azure: Sign in** (Iniciar sesión).

   Se abrirá una ventana del explorador, que le pedirá sus credenciales. Cuando vea una página que indica que la operación ha sido correcta, puede cerrar el explorador.

### <a name="connect-to-your-iot-hub-and-retrieve-hub-connection-string"></a>Conexión a la instancia de IoT Hub y recuperación de la cadena de conexión del centro

1. En la sección inferior del explorador de Visual Studio Code, seleccione el marco de **Azure IoT Hub** para expandirlo.

1. En el marco expandido, haga clic en **Select IoT Hub** (Seleccionar IoT Hub).

1. Cuando se le solicite, seleccione la suscripción de Azure y, a continuación, su instancia de IoT Hub.

1. Haga clic en **...** , a la derecha de **Azure IoT Hub**, para ver más acciones. Seleccione **Copy IoT Hub connection string** (Copiar cadena de conexión de IoT Hub).

   ![Copia de cadena de conexión de IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/copy-hub-connection-string.png)

### <a name="run-the-deviceharness-project"></a>Ejecución del proyecto DeviceHarness

1. Seleccione **Ver** > **Terminal** para abrir el terminal de Visual Studio Code.

   Si no ve un símbolo del sistema, presione Entrar.

1. Escriba `dotnet run` en el terminal.

1. Cuando se le solicite la cadena de conexión de IoT Hub, pegue la cadena de conexión que copió en la sección anterior.

1. En el marco de **dispositivos de Azure IoT Hub**, haga clic en el botón Actualizar.

   ![Actualización de la lista de dispositivos de IoT Hub](media/tutorial-machine-learning-edge-03-generate-data/refresh-hub-device-list.png)

1. Observe cómo se agregan los dispositivos a la instancia de IoT Hub y cómo se muestran en verde para indicar que los datos se están enviando mediante ese dispositivo. Después de que los dispositivos envíen mensajes a IoT Hub, se desconectan y aparecen en azul.

1. Para ver los mensajes que se envían al centro, haga clic con el botón derecho en cualquier dispositivo y seleccione **Start Monitoring Built-in Event Endpoint** (Iniciar supervisión del punto de conexión del evento integrado). Los mensajes se mostrarán en el panel de salida de Visual Studio Code.

1. Para detener la supervisión, haga clic en el panel de salida de **Azure IoT Hub** y seleccione **Detener la supervisión del punto de conexión de eventos integrado**.

1. Deje que la aplicación se ejecute hasta que finalice, lo que tardará unos minutos.

## <a name="check-iot-hub-for-activity"></a>Comprobación de actividad en IoT Hub

Los datos enviados por DeviceHarness fueron a su centro de IoT, que puede comprobar en Azure Portal.

1. Abra [Azure Portal](https://portal.azure.com/) y vaya al centro de IoT que creó para este tutorial.

1. En el menú de la izquierda, en **Supervisión**, seleccione **Métricas**.

1. En la página definición del gráfico, haga clic en el menú desplegable **Métrica**, desplácese hacia abajo en la lista y seleccione **Routing: data delivered to storage** (Enrutamiento: datos entregados al almacenamiento). El gráfico mostrará un pico en el momento en que los datos se enrutaron al almacenamiento.

   ![El gráfico muestra un pico en el momento en que los datos se entregaron al almacenamiento](media/tutorial-machine-learning-edge-03-generate-data/iot-hub-usage.png)

## <a name="validate-data-in-azure-storage"></a>Validación de datos en Azure Storage

Los datos que acabamos de enviar a IoT Hub se han enrutado al contenedor de almacenamiento que creamos en el artículo anterior. Vamos a echar un vistazo a los datos en nuestra cuenta de almacenamiento.

1. En Azure Portal, vaya a la cuenta de almacenamiento.

1. En el navegador de la cuenta de almacenamiento, seleccione **Explorador de Storage (versión preliminar)** .

1. En el explorador de almacenamiento, seleccione **Blob Containers** y, después, `devicedata`.

1. En el panel de contenido, haga clic en la carpeta del nombre del centro de IoT, seguido del año, mes, día y hora. Verá varias carpetas que representan los minutos en los que se escribieron los datos.

   ![Visualización de carpetas en Blob Storage](media/tutorial-machine-learning-edge-03-generate-data/confirm-data-storage-results.png)

1. Haga clic en una de esas carpetas para buscar archivos de datos etiquetados como **00** y **01** correspondientes a la partición.

1. Los archivos se escriben en el formato [Avro](https://avro.apache.org/). Haga doble clic en uno de estos archivos para abrir otra pestaña del explorador y representar los datos parcialmente. Si se le pide que abra el archivo en un programa, puede elegir VS Code y se representará correctamente.

1. Ahora no es necesario que intente leer ni interpretar los datos; esto se hará en el siguiente artículo.

## <a name="clean-up-resources"></a>Limpieza de recursos

Este tutorial forma parte de una serie en la que cada artículo complementa el trabajo realizado en los anteriores. No borre ningún recurso hasta que complete el tutorial final.

## <a name="next-steps"></a>Pasos siguientes

En este artículo, usamos un proyecto de .NET Core para crear un conjunto de dispositivos IoT virtuales y enviar datos a través de ellos a nuestro centro de IoT y a un contenedor de Azure Storage. Este proyecto simula un escenario real en el que los dispositivos IoT físicos envían datos a un centro de IoT y a un almacenamiento seleccionado. Estos datos incluyen las lecturas de los sensores, la configuración operativa, las señales de error, los modos, etc. Una vez que se han recopilado suficientes datos, se usa para entrenar modelos que predicen la vida útil restante del dispositivo. Mostraremos este aprendizaje automático en el siguiente artículo.

Continúe con el siguiente artículo para entrenar un modelo de aprendizaje automático con los datos.

> [!div class="nextstepaction"]
> [Entrenamiento e implementación de un modelo de Azure Machine Learning](tutorial-machine-learning-edge-04-train-model.md)
