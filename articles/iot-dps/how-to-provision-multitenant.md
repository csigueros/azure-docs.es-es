---
title: Cómo aprovisionar dispositivos para el multiinquilinato en Azure IoT Hub Device Provisioning Service
description: Cómo aprovisionar dispositivos para el multiinquilinato con su instancia de Device Provisioning Service (DPS)
author: anastasia-ms
ms.author: v-stharr
ms.topic: how-to
ms.date: 10/02/2021
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 595bd473bb70f4c23554998b7240e4ef0ed1183d
ms.sourcegitcommit: bee590555f671df96179665ecf9380c624c3a072
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2021
ms.locfileid: "129670343"
---
# <a name="how-to-provision-for-multitenancy"></a>Cómo aprovisionar para el multiinquilinato 

En esta guía paso a paso se muestra cómo aprovisionar de forma segura varios dispositivos de claves simétricas simuladas para un grupo de instancias de IoT Hub mediante una [directiva de asignación](concepts-service.md#allocation-policy). Las directivas de asignación definidas por el servicio de aprovisionamiento admiten una gran variedad de escenarios de asignación. Dos escenarios comunes son:

* **Geolocalización o geolatencia**: puesto que un dispositivo se mueve entre ubicaciones, la latencia de red se mejora mediante su aprovisionamiento en el centro de IoT más cercano a cada ubicación. En este escenario, se selecciona un grupo de centros de IoT, que abarcan varias regiones, para las inscripciones. La directiva de asignación **Latencia más baja** está seleccionada para estas inscripciones. Esta directiva hace que Device Provisioning Service evalúe la latencia del dispositivo y determine el centro de IoT más cercano del grupo de centros de IOT.

* **Multiinquilinato**: puede que los dispositivos que se usan en una solución de IoT se tengan que asignar a un centro de IoT o un grupo de centros de IoT específicos. La solución puede requerir que todos los dispositivos de un inquilino determinado se comuniquen con un grupo específico de centros de IoT. En algunos casos, un inquilino puede tener centros de IoT y requerir que los dispositivos se asignen a sus centros.

Es habitual combinar estos dos escenarios. Por ejemplo, una solución de IoT multiinquilino normalmente asigna los dispositivos de los inquilinos mediante un grupo de centros de IoT repartidos entre distintas regiones. Estos dispositivos de los inquilinos se pueden asignar al centro de IoT del grupo que tiene la menor latencia según la ubicación geográfica.

En este artículo, se usa un ejemplo de dispositivo simulado del [SDK de C para Azure IoT](https://github.com/Azure/azure-iot-sdk-c) para demostrar cómo aprovisionar dispositivos en un escenario multiinquilino entre regiones. En este artículo, llevará a cabo los siguientes pasos:

> [!div class="checklist"]
> * Usar la CLI de Azure para crear dos centros de IoT regionales (**Oeste de EE. UU. 2** y **Este de EE. UU.** ).
> * Crear una inscripción multiinquilino.
> * Usar la CLI de Azure para crear dos VM de Linux regionales para actuar como dispositivos en las mismas regiones (**Oeste de EE. UU. 2** y **Este de EE. UU.** ).
> * Configurar el entorno de desarrollo para el SDK de C para Azure IoT en ambas máquinas virtuales de Linux.
> * Simular los dispositivos para ver que están aprovisionados para el mismo inquilino en la región más cercana.

>[!IMPORTANT]
> Algunas regiones pueden aplicar, de vez en cuando, restricciones en la creación de Virtual Machines. En el momento de escribir esta guía, las regiones *westus2* y *eastus* permitían la creación de VM. Si no puede crearlas en ninguna de esas regiones, puede probar otra región. Para más información sobre cómo elegir regiones geográficas de Azure al crear VM, consulte [Regiones para máquinas virtuales en Azure](../virtual-machines/regions.md).

## <a name="prerequisites"></a>Prerrequisitos

* Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) antes de empezar.

* Realice los pasos que se describen en [Configuración de IoT Hub Device Provisioning Service con Azure Portal](./quick-setup-auto-provision.md).

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

## <a name="create-two-regional-iot-hubs"></a>Creación de dos centros de IoT regionales

En esta sección, creará un grupo de recursos de Azure y dos nuevos recursos de centro de IoT regionales para un inquilino. Un centro de IoT será para la región **Oeste de EE. UU. 2** y el otro para la región **Este de EE. UU.**

>[!IMPORTANT]
>Le recomendamos que use este mismo grupo de recursos para todos los recursos creados en este artículo. De esta forma, será más fácil limpiar al finalizar.

1. En Azure Cloud Shell, cree un grupo de recursos con el comando [az group create](/cli/azure/group#az_group_create) siguiente:

    ```azurecli-interactive
    az group create --name contoso-us-resource-group --location eastus
    ```

2. Cree un centro de IoT en la ubicación *eastus* y agréguelo al grupo de recursos que creó con el siguiente comando [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) (reemplace `{unique-hub-name}` por su propio nombre único):

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location eastus --sku S1
    ```

    Este comando puede tardar varios minutos en completarse.

3. Ahora, cree un centro de IoT en la ubicación *westus2* y agréguelo al grupo de recursos que creó con el siguiente comando [az iot hub create](/cli/azure/iot/hub#az_iot_hub_create) (reemplace `{unique-hub-name}` por su propio nombre único):

    ```azurecli-interactive
    az iot hub create --name {unique-hub-name} --resource-group contoso-us-resource-group --location westus2 --sku S1
    ```

    Este comando puede tardar varios minutos en completarse.

## <a name="create-the-multitenant-enrollment"></a>Creación de la inscripción multiinquilino

En esta sección, creará un grupo de inscripción para los dispositivos del inquilino.  

Para que sea más sencillo, en este artículo se usa la [atestación de clave simétrica](concepts-symmetric-key-attestation.md) con la inscripción. Con el fin de obtener una solución más segura, considere la posibilidad de usar la [atestación de certificado X.509](concepts-x509-attestation.md) con una cadena de confianza.

1. En Azure Portal, vaya a la instancia de Device Provisioning Service.

2. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

3. Seleccione **+ Agregar grupo de inscripciones**.

4. En la página **Agregar grupo de inscripciones**, escriba la siguiente información:

    **Nombre del grupo**: escriba *contoso-us-devices*.

    **Tipo de atestación**: seleccione *clave simétrica*.

    **Generar claves automáticamente**: esta casilla ya debe estar seleccionada.

    **Seleccione cómo quiere asignar los dispositivos a los centros**: seleccione *Latencia más baja*.

5. Seleccione **Vincular un nuevo centro de IoT**.

    :::image type="content" source="./media/how-to-provision-multitenant/create-multitenant-enrollment.png" alt-text="Agregar grupo de inscripción multiinquilino para la atestación de clave simétrica.":::

6. En la página **Agregar un vínculo a IoT Hub**, escriba la siguiente información:

    **Suscripción**: si tiene varias suscripciones, elija aquella en la que ha creado los centros de IoT regionales.

    **Centro de IoT**: seleccione el centro de IoT que creó para la ubicación *eastus*.

    **Directiva de acceso**: elija *iothubowner*.

    :::image type="content" source="./media/how-to-provision-multitenant/link-regional-hubs.png" alt-text="Vincular los centros de IoT regionales con el servicio de aprovisionamiento.":::

7. Seleccione **Guardar**.

8. Repita los pasos del 5 al 7 para el segundo centro de IoT que creó para la ubicación *westgus*.

9. Seleccione las dos instancias de IoT Hub que creó en la lista desplegable para **seleccionar los centros de IoT a los que se puede asignar este grupo**.

    :::image type="content" source="./media/how-to-provision-multitenant/enrollment-regional-hub-group.png" alt-text="Seleccionar los centros de IoT vinculados.":::

10. Seleccione **Guardar**.

11. Seleccione *contoso-us-devices* en la lista de grupos de inscripción.

12. Copie el valor de *Clave principal*. Esta clave se usará más adelante para generar claves de dispositivo únicas para ambos dispositivos simulados.

    :::image type="content" source="./media/how-to-provision-multitenant/copy-primary-key.png" alt-text="Copiar el valor de la clave principal.":::

## <a name="create-regional-linux-vms"></a>Creación de máquinas virtuales regionales de Linux

En esta sección, creará dos máquinas virtuales (VM) regionales de Linux. Estas máquinas virtuales ejecutarán un ejemplo de simulación de dispositivo de cada región para demostrar el aprovisionamiento de dispositivos de inquilino desde ambas regiones.

Para que la limpieza sea más sencilla, estas máquinas virtuales se agregarán al mismo grupo de recursos que contiene los centros de IoT que se han creado: *contoso-us-resource-group*. En cambio, las VM se ejecutarán en regiones diferentes (**Oeste de EE. UU. 2** y **Este de EE. UU.** ).

1. En Azure Cloud Shell, ejecute el siguiente comando para crear una VM de la región **Este de EE. UU.** después de realizar los siguientes cambios de parámetro en el comando:

    **--name**: escriba un nombre único para su máquina virtual de dispositivo regional del **Este de EE. UU.**

    **--admin-username**: use su propio nombre de usuario administrador.

    **--admin-password**: use su propia contraseña de administrador.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceEast \
    --location eastus \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    Este comando tardará varios minutos en completarse. 

2. Cuando se haya completado el comando, copie el valor de **publicIpAddress** de la VM de la región Este de EE. UU.

3. En Azure Cloud Shell, ejecute el comando para crear una VM de la región **Oeste de EE. UU. 2** después de realizar los siguientes cambios de parámetro en el comando:

    **--name**: escriba un nombre único para su VM de dispositivo regional de **Oeste de EE. UU. 2**. 

    **--admin-username**: use su propio nombre de usuario administrador.

    **--admin-password**: use su propia contraseña de administrador.

    ```azurecli-interactive
    az vm create \
    --resource-group contoso-us-resource-group \
    --name ContosoSimDeviceWest2 \
    --location westus2 \
    --image Canonical:UbuntuServer:18.04-LTS:18.04.201809110 \
    --admin-username contosoadmin \
    --admin-password myContosoPassword2018 \
    --authentication-type password
    --public-ip-sku Standard
    ```

    Este comando tardará varios minutos en completarse.

4. Cuando se haya completado el comando, copie el valor de **publicIpAddress** de la VM de la región Oeste de EE. UU. 2.

5. Abra dos shells de línea de comandos.

6. Conéctese a una de las máquinas virtuales regionales en cada shell mediante SSH.

    Pase su nombre de usuario administrador y la dirección IP pública que ha copiado como parámetros para conectarse mediante SSH. Escriba la contraseña de administrador cuando se le pida.

    ```bash
    ssh contosoadmin@1.2.3.4

    contosoadmin@ContosoSimDeviceEast:~$
    ```

    ```bash
    ssh contosoadmin@5.6.7.8

    contosoadmin@ContosoSimDeviceWest:~$
    ```

## <a name="prepare-the-azure-iot-c-sdk-development-environment"></a>Preparación del entorno de desarrollo del SDK de C para Azure IoT

En esta sección, clonará el SDK de C para Azure IoT en cada VM. El SDK contiene un ejemplo que simula el aprovisionamiento de dispositivos de un inquilino desde cada región.

Para cada VM:

1. Instale **CMake**, **g++** , **gcc** y [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git) mediante los siguientes comandos:

    ```bash
    sudo apt-get update
    sudo apt-get install cmake build-essential libssl-dev libcurl4-openssl-dev uuid-dev git-all
    ```

2. Busque y copie el nombre de etiqueta de la [versión más reciente](https://github.com/Azure/azure-iot-sdk-c/releases/latest) del SDK.

3. Clone el [SDK de C para Azure IoT](https://github.com/Azure/azure-iot-sdk-c) en ambas máquinas virtuales.  Use la etiqueta que encontró en el paso anterior como valor del parámetro `-b`:

    ```bash
    git clone -b <release-tag> https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    Esta operación puede tardar varios minutos en completarse.

4. Cree una carpeta **cmake** dentro del repositorio y cambie a esa carpeta.

    ```bash
    mkdir ~/azure-iot-sdk-c/cmake
    cd ~/azure-iot-sdk-c/cmake
    ```

5. Ejecute el siguiente comando para compilar una versión del SDK específica para su plataforma de cliente de desarrollo:

    ```bash
    cmake -Dhsm_type_symm_key:BOOL=ON -Duse_prov_client:BOOL=ON  ..
    ```

6. Una vez realizada la compilación, las últimas líneas de salida tendrán un aspecto similar al siguiente:

    ```bash
    -- IoT Client SDK Version = 1.7.0
    -- Provisioning SDK Version = 1.7.0
    -- Looking for include file stdint.h
    -- Looking for include file stdint.h - found
    -- Looking for include file stdbool.h
    -- Looking for include file stdbool.h - found
    -- target architecture: x86_64
    -- Performing Test CXX_FLAG_CXX11
    -- Performing Test CXX_FLAG_CXX11 - Success
    -- Found OpenSSL: /usr/lib/x86_64-linux-gnu/libcrypto.so (found version "1.1.1")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so (found version "7.58.0")
    -- Found CURL: /usr/lib/x86_64-linux-gnu/libcurl.so
    -- target architecture: x86_64
    -- iothub architecture: x86_64
    -- Configuring done
    -- Generating done
    -- Build files have been written to: /home/contosoadmin/azure-iot-sdk-c/azure-iot-sdk-c
    ```

## <a name="derive-unique-device-keys"></a>Derivación de las claves de dispositivo únicas

Al usar la atestación de clave simétrica con las inscripciones de grupos, no se usan las claves de grupo de inscripción directamente. En su lugar, se deriva una clave única de la clave del grupo de inscripción para cada dispositivo. Para obtener más información, vea [Agrupación de inscripciones con claves simétricas](concepts-symmetric-key-attestation.md#group-enrollments).

En esta parte del tutorial, generará una clave de dispositivo a partir de la clave maestra del grupo para calcular un [HMAC-SHA256](https://wikipedia.org/wiki/HMAC) del identificador de registro único para el dispositivo. A continuación, el resultado se convertirá al formato Base64.

>[!IMPORTANT]
>No incluya la clave maestra del grupo en el código del dispositivo.

Para **ambos** dispositivos *eastus* y *westus 2*:

1. Genere la clave única mediante **openssl**. Usará el siguiente script de shell de Bash (reemplace `{primary-key}` por la **clave principal** del grupo de inscripción que copió anteriormente y reemplace `{contoso-simdevice-east}` por su propio identificador de registro único para cada dispositivo. Use caracteres alfanuméricos en minúsculas y guiones ("-") para definir ambos identificadores).

    ```bash
    KEY={primary-key}
    REG_ID={contoso-simdevice}
    
    keybytes=$(echo $KEY | base64 --decode | xxd -p -u -c 1000)
    echo -n $REG_ID | openssl sha256 -mac HMAC -macopt hexkey:$keybytes -binary | base64
    ```

2. El script generará una salida similar a la clave siguiente:

    ```bash
    p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=
    ```

3. Ahora, cada dispositivo del inquilino tiene su propia clave de dispositivo derivada y su identificador de registro único para realizar la atestación de claves simétricas con el grupo de inscripción durante el proceso de aprovisionamiento.

## <a name="simulate-the-devices-from-each-region"></a>Simulación de los dispositivos desde cada región

En esta sección, actualizará un ejemplo de aprovisionamiento en el SDK de C para Azure IoT para ambas VM regionales. 

El código de ejemplo simula una secuencia de arranque de dispositivo que envía la solicitud de aprovisionamiento a la instancia de Device Provisioning Service. La secuencia de arranque hace que se reconozca y se asigne el dispositivo al centro de IoT más cercano según la latencia.

1. En Azure Portal, seleccione la pestaña **Información general** para la instancia de Device Provisioning Service y anote el valor de **_Ámbito de id_**.

    :::image type="content" source="./media/how-to-provision-multitenant/copy-id-scope.png" alt-text="Extraer información del punto de conexión del servicio de aprovisionamiento de dispositivos desde la hoja del portal.":::

2. En ambas VM, abra **~/azure-iot-sdk-c/provisioning\_client/samples/prov\_dev\_client\_sample/prov\_dev\_client\_sample.c** para su edición.

    ```bash
    vi ~/azure-iot-sdk-c/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample.c
    ```

3. En ambas VM, busque la constante `id_scope` y reemplace el valor por el de **Ámbito de id.** que copió anteriormente. 

    ```c
    static const char* id_scope = "0ne00002193";
    ```

4. En ambas VM, busque la definición de la función `main()` en el mismo archivo. Asegúrese de que la variable `hsm_type` esté establecida en `SECURE_DEVICE_TYPE_SYMMETRIC_KEY` tal como se muestra a continuación para que coincida con el método de atestación del grupo de inscripción. 

    Guarde los cambios de los archivos en ambas máquinas virtuales.

    ```c
    SECURE_DEVICE_TYPE hsm_type;
    //hsm_type = SECURE_DEVICE_TYPE_TPM;
    //hsm_type = SECURE_DEVICE_TYPE_X509;
    hsm_type = SECURE_DEVICE_TYPE_SYMMETRIC_KEY;
    ```

5. En ambas máquinas virtuales, encuentre la llamada a `prov_dev_set_symmetric_key_info()` en **prov\_dev\_client\_sample.c** que se marcó como comentario.

    ```c
    // Set the symmetric key if using they auth type
    //prov_dev_set_symmetric_key_info("<symm_registration_id>", "<symmetric_Key>");
    ```

    Quite la marca del comentario en las llamadas de función y reemplace los valores de marcador de posición (incluidos los corchetes angulares) por los identificadores de registro únicos y las claves de dispositivo derivadas para cada dispositivo que derivó en la sección anterior. Las claves que se muestran a continuación son solo para fines ilustrativos. Use las claves que generó anteriormente.

    Este de EE. UU:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-east", "p3w2DQr9WqEGBLUSlFi1jPQ7UWQL4siAGy75HFTFbf8=");
    ```

    Oeste de EE. UU.:
    ```c
    // Set the symmetric key if using they auth type
    prov_dev_set_symmetric_key_info("contoso-simdevice-west", "J5n4NY2GiBYy7Mp4lDDa5CbEe6zDU/c62rhjCuFWxnc=");
    ```

6. En ambas VM, guarde el archivo.

7. En ambas máquinas virtuales, vaya a la carpeta de ejemplo que se muestra a continuación y compile el ejemplo.

    ```bash
    cd ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/
    cmake --build . --target prov_dev_client_sample --config Debug
    ```

8. Una vez que la compilación se realice correctamente, ejecute **prov\_dev\_client\_sample.exe** en ambas máquinas virtuales para simular un dispositivo de inquilino de cada región. Tenga en cuenta que cada dispositivo se asigna al centro de IoT del inquilino más cercano a las regiones del dispositivo simulado.

    Ejecución de la simulación:
    ```bash
    ~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample/prov_dev_client_sample
    ```

    Salida de ejemplo de la máquina virtual de Este de EE. UU.:

    ```bash
    contosoadmin@ContosoSimDeviceEast:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-east-hub.azure-devices.net, deviceId: contoso-simdevice-east
    Press enter key to exit:

    ```

    Salida de ejemplo de la máquina virtual de Oeste de EE. UU.:
    ```bash
    contosoadmin@ContosoSimDeviceWest:~/azure-iot-sdk-c/cmake/provisioning_client/samples/prov_dev_client_sample$ ./prov_dev_client_sample
    Provisioning API Version: 1.2.9

    Registering Device

    Provisioning Status: PROV_DEVICE_REG_STATUS_CONNECTED
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING
    Provisioning Status: PROV_DEVICE_REG_STATUS_ASSIGNING

    Registration Information received from service: contoso-west-hub.azure-devices.net, deviceId: contoso-simdevice-west
    Press enter key to exit:
    ```

## <a name="clean-up-resources"></a>Limpieza de recursos

Si tiene previsto seguir trabajando con los recursos creados en este artículo, puede dejarlos. De lo contrario, realice los pasos siguientes para eliminar todos los recursos creados en este artículo para evitar cargos innecesarios.

En estos pasos se supone que ha creado todos los recursos de este artículo como se ha indicado en el mismo grupo de recursos denominado **contoso-us-resource-group**.

> [!IMPORTANT]
> La eliminación de un grupo de recursos es irreversible. El grupo de recursos y todos los recursos contenidos en él se eliminan permanentemente. Asegúrese de no eliminar por accidente el grupo de recursos o los recursos equivocados. Si ha creado una instancia de IoT Hub en un grupo de recursos ya existente que contiene recursos que desea conservar, puede eliminar solo esa instancia en lugar de eliminar todo el grupo de recursos.
>

Para eliminar el grupo de recursos por nombre:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. Seleccione **Grupos de recursos**.

3. En el cuadro de texto **Filtrar por nombre...**, escriba el nombre del grupo de recursos que contiene los recursos: **contoso-us-resource-group**.

4. A la derecha del grupo de recursos de la lista de resultados, haga clic en **...** y, a continuación, en **Eliminar grupo de recursos**.

5. Se le pedirá que confirme la eliminación del grupo de recursos. Escriba de nuevo el nombre del grupo de recursos para confirmar y, después, seleccione **Eliminar**. Transcurridos unos instantes, el grupo de recursos y todos los recursos que contiene se eliminan.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre el reaprovisionamiento, consulte:

> [!div class="nextstepaction"]
> [Conceptos sobre el reaprovisionamiento de dispositivos de IoT Hub](concepts-device-reprovision.md)

* Para más información sobre el desaprovisionamiento, consulte
> [!div class="nextstepaction"]
> [Desaprovisionamiento de dispositivos aprovisionados automáticamente](how-to-unprovision-devices.md)