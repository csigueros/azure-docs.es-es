---
title: Administración de las inscripciones de dispositivos para Azure IoT Hub Device Provisioning Service en Azure Portal
description: Administración de las inscripciones de dispositivos para Device Provisioning Service (DPS) en Azure Portal
author: anastasia-ms
ms.author: v-stharr
ms.date: 10/25/2021
ms.topic: how-to
ms.service: iot-dps
services: iot-dps
manager: lizross
ms.openlocfilehash: 7f0fdcf8a3e51405861082b3c2ad8a6385f5a6e9
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131843423"
---
# <a name="how-to-manage-device-enrollments-with-azure-portal"></a>Administración de las inscripciones de dispositivos con Azure Portal

Una *inscripción de dispositivos* crea un registro de un único dispositivo o grupo de dispositivos que, en algún momento, se registrará en Azure IoT Hub Device Provisioning Service (DPS). El registro de la inscripción contiene la configuración inicial para los dispositivos como parte de la inscripción. En la configuración se incluye el centro de IoT al que se asignará un dispositivo o una directiva de asignación que configura el centro a partir de un conjunto de centros. En este artículo se muestra cómo administrar las inscripciones de dispositivos para el servicio de aprovisionamiento.

Azure IoT Hub Device Provisioning Service admite dos tipos de inscripciones:

* [Grupos de inscripción](concepts-service.md#enrollment-group): usados para inscribir varios dispositivos relacionados.
* [Inscripciones individuales](concepts-service.md#individual-enrollment): usadas para inscribir un solo dispositivo.

## <a name="create-an-enrollment-group"></a>Creación de un grupo de inscripción

 Un grupo de inscripción es una entrada para un grupo de dispositivos que comparte un mecanismo de atestación común. Se recomienda usar un grupo de inscripción para un gran número de dispositivos que comparta una configuración inicial o para dispositivos que van al mismo inquilino. Se admiten los dispositivos que usan la atestación de [clave simétrica](concepts-symmetric-key-attestation.md) o [certificados X.509](concepts-x509-attestation.md).

### <a name="create-a-symmetric-key-enrollment-group"></a>Crear un grupo de inscripción de clave simétrica

Para crear y usar grupos de inscripción con claves simétricas, consulte el tutorial [Aprovisionamiento de dispositivos con claves simétricas](how-to-legacy-device-symm-key.md).

Para crear un grupo de inscripción de clave simétrica:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service en la que quiere inscribir el dispositivo.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. En la parte superior de la página, haga clic en **+ Agregar grupo de inscripciones**.

6. En la página **Agregar grupo de inscripciones**, escriba la siguiente información:

   | Campo | Descripción |
    | :--- | :--- |
    | **Nombre de grupo** | Nombre del grupo de dispositivos.|
    | **Tipo de atestación** |seleccione **Clave simétrica**.|
    | **Generar claves automáticamente** |Active esta casilla.|
    | **Seleccione cómo desea asignar los dispositivos a los centros** |Seleccione *Configuración estática* para poder realizar la asignación a un centro concreto.|
    | **Seleccione los centros de IoT a los que se puede asignar este grupo** |Seleccione uno de los centros.|

    Deje el resto de los campos con los valores predeterminados.

    :::image type="content" source="./media/how-to-manage-enrollments/add-enrollment-group-symm-key.png" alt-text="Agregue el grupo de inscripción para la atestación de clave simétrica.":::

7. Seleccione **Guardar**.

### <a name="create-a-x509-certificate-enrollment-group"></a>Creación de un grupo de inscripciones de certificados X.509

Para crear un grupo de inscripciones de certificados X.509:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service en la que quiere inscribir el dispositivo.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. En la parte superior de la página, haga clic en **+ Agregar grupo de inscripciones**.

6. En la página **Agregar grupo de inscripciones**, escriba la siguiente información:

    | Campo | Descripción |
    | :--- | :--- |
    | **Nombre de grupo** | Nombre del grupo de dispositivos.|
    | **Tipo de atestación** |Seleccione **Certificado**.|
    | **Tipo de certificado** | Seleccione **Certificado de entidad de certificación** o **Intermedio** en función del certificado con que firmó los certificados de dispositivo.|
    | **Certificado principal**| Si va a firmar los certificados de dispositivo con un certificado de entidad de certificación, el certificado de entidad de certificación raíz debe tener la [prueba de posesión](how-to-verify-certificates.md) completada. Si va a firmar los certificados de dispositivo con un certificado intermedio, estará disponible un botón de carga para permitirle cargar un certificado intermedio. El certificado que firmó el intermedio también debe tener una [prueba de posesión](how-to-verify-certificates.md) completada.

    Deje el resto de los campos con los valores predeterminados.

    :::image type="content" source="./media/how-to-manage-enrollments/add-enrollment-group-cert.png" alt-text="Agregue el grupo de inscripción para la atestación de certificados de entidad de certificación.":::

7. Seleccione **Guardar**.

## <a name="create-an-individual-enrollment"></a>Creación de una inscripción individual

Una inscripción individual es una entrada para un único dispositivo que se puede asignar a un centro de IoT. Se admiten los dispositivos que usan [clave simétrica](concepts-symmetric-key-attestation.md), [certificados X.509](concepts-x509-attestation.md) y la [atestación de TPM](concepts-tpm-attestation.md).

### <a name="create-a-symmetric-key-individual-enrollment"></a>Creación de una inscripción individual de clave simétrica

>[!TIP]
>Para obtener instrucciones más detalladas sobre cómo crear y usar inscripciones individuales con claves simétricas, consulte [Inicio rápido: Aprovisionamiento de un dispositivo simulado con claves simétricas](quick-create-simulated-device-symm-key.md#create-a-device-enrollment).

Para crear una inscripción individual de clave simétrica:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service en la que quiere inscribir el dispositivo.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. En la parte superior de la página, seleccione **+ Agregar inscripción individual**.

6. En la página **Agregar inscripción**, escriba la siguiente información.

    | Campo | Descripción |
    | :--- | :--- |
    | **Mecanismo** | Seleccione *Clave simétrica* |
    | **Generar claves automáticamente** |Active esta casilla. |
    | **Identificador de registro** | Escriba un id. de registro único.|
    | **Identificador de dispositivo IoT Hub** |  Este identificador representará el dispositivo. Debe seguir las reglas de un identificador de dispositivo. Para más información, consulte [Propiedades de identidad del dispositivo](../iot-hub/iot-hub-devguide-identity-registry). Si el id. de dispositivo se deja sin especificar, se usará el id. de registro.|
    | **Seleccione cómo desea asignar los dispositivos a los centros** |Seleccione *Configuración estática* para poder realizar la asignación a un centro concreto.|
    | **Seleccione los centros de IoT a los que se puede asignar este grupo** |Seleccione uno de los centros.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-symm-key.png" alt-text="Agregar una inscripción individual para la atestación de clave simétrica.":::

7. Seleccione **Guardar**.

### <a name="create-a-x509-certificate-individual-enrollment"></a>Creación de una inscripción individual de certificado X.509

>[!TIP]
>Para obtener instrucciones más detalladas sobre cómo crear y usar inscripciones individuales con certificados X.509, consulte [Inicio rápido: Aprovisionamiento de un dispositivo simulado con certificado X.509](quick-create-simulated-device-x509.md#create-a-device-enrollment).

Para crear una inscripción individual de certificado X.509:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service en la que quiere inscribir el dispositivo.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. En la parte superior de la página, seleccione **+ Agregar inscripción individual**.

6. En la página **Agregar inscripción**, escriba la siguiente información.

    | Campo | Descripción |
    | :--- | :--- |
    | **Mecanismo** | Seleccione *X.509*. |
    | **Archivo .pem o .cer del certificado principal** | Cargue un certificado a partir del cual puede generar certificados de hoja. Si elige el archivo .cer, solo se acepta el certificado codificado en base 64. |
    | **Identificador de dispositivo IoT Hub** |  Este identificador representará el dispositivo. Debe seguir las reglas de un identificador de dispositivo. Para más información, consulte [Propiedades de identidad del dispositivo](../iot-hub/iot-hub-devguide-identity-registry). El id. de dispositivo debe ser el nombre del sujeto en el certificado de dispositivo que cargue para la inscripción. Dicho nombre de firmante debe cumplir las reglas de los identificadores de dispositivo.|
    | **Seleccione cómo desea asignar los dispositivos a los centros** |Seleccione *Configuración estática* para poder realizar la asignación a un centro concreto.|
    | **Seleccione los centros de IoT a los que se puede asignar este grupo** |Seleccione uno de los centros.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-cert.png" alt-text="Agregue la inscripción individual para la atestación de certificado X.509.":::

7. Seleccione **Guardar**.

### <a name="create-a-tpm-individual-enrollment"></a>Creación de una inscripción individual de TPM

>[!TIP]
>Para obtener instrucciones más detalladas sobre cómo crear y usar inscripciones individuales mediante la atestación de TPM, consulte uno de los ejemplos de [Aprovisionamiento de un dispositivo TPM simulado](quick-create-simulated-device-tpm.md#create-a-device-enrollment-entry).

Para crear una inscripción individual de TPM:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service en la que quiere inscribir el dispositivo.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. En la parte superior de la página, seleccione **+ Agregar inscripción individual**.

6. En la página **Agregar inscripción**, escriba la siguiente información.

    | Campo | Descripción |
    | :--- | :--- |
    | **Mecanismo** | Seleccione *TPM*. |
    | **Clave de aprobación** | Clave de aprobación única del dispositivo TPM. |
    | **Identificador de registro** | Escriba un id. de registro único.|
    | **Identificador de dispositivo IoT Hub** |  Este identificador representará el dispositivo. Debe seguir las reglas de un identificador de dispositivo. Para más información, consulte [Propiedades de identidad del dispositivo](../iot-hub/iot-hub-devguide-identity-registry). Si el id. de dispositivo se deja sin especificar, se usará el id. de registro.|
    | **Seleccione cómo desea asignar los dispositivos a los centros** |Seleccione *Configuración estática* para poder realizar la asignación a un centro concreto.|
    | **Seleccione los centros de IoT a los que se puede asignar este grupo** |Seleccione uno de los centros.|

    :::image type="content" source="./media/how-to-manage-enrollments/add-individual-enrollment-tpm.png" alt-text="Agregue la inscripción individual para la atestación de TPM.":::

7. Seleccione **Guardar**.

## <a name="update-an-enrollment-entry"></a>Actualización de una entrada de inscripción

Para actualizar una entrada de inscripción existente:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service en la que quiere inscribir el dispositivo.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. Seleccione la entrada de inscripción que desea modificar.

6. En la página de detalles de la entrada de inscripción, puede actualizar todos los elementos, excepto el tipo de seguridad y las credenciales.

7. Cuando termine, seleccione **Guardar**.

## <a name="remove-a-device-enrollment"></a>Eliminación de una inscripción de dispositivo

Para eliminar una entrada de inscripción:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

2. En el menú de la izquierda o en la página del portal, seleccione **Todos los recursos**.

3. Seleccione la instancia de Device Provisioning Service en la que quiere inscribir el dispositivo.

4. En el menú de **Configuración**, seleccione **Administrar inscripciones**.

5. Seleccione la entrada de inscripción que desea eliminar. 

6. En la parte superior de la página, seleccione **Eliminar**.

7. Cuando se le solicite confirmación, haga clic en **Sí**.

8. Una vez finalizada la acción, verá que la entrada se ha eliminado de la lista de inscripciones de dispositivos.
