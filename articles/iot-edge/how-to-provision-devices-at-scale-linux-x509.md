---
title: Creación y aprovisionamiento de dispositivos IoT Edge a escala mediante certificados X.509 en Linux - Azure IoT Edge | Microsoft Docs
description: Uso de certificados X.509 para probar el aprovisionamiento de dispositivos a gran escala para Azure IoT Edge con Device Provisioning Service
author: kgremban
ms.author: kgremban
ms.date: 10/29/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: contperf-fy21q2
ms.openlocfilehash: 35f8b3482eff16197f9851ab849d9bdd370ad314
ms.sourcegitcommit: 8946cfadd89ce8830ebfe358145fd37c0dc4d10e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/05/2021
ms.locfileid: "131850819"
---
# <a name="create-and-provision-iot-edge-devices-at-scale-on-linux-using-x509-certificates"></a>Creación y aprovisionamiento de dispositivos IoT Edge a escala en Linux mediante certificados X.509

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

En este artículo se proporcionan instrucciones completas para el aprovisionamiento automático de uno o varios dispositivos Linux IoT Edge con certificados X.509. Puede aprovisionar automáticamente los dispositivos Azure IoT Edge con [Azure IoT Hub Device Provisioning Service](../iot-dps/index.yml) (DPS). Si no está familiarizado con el proceso de aprovisionamiento automático, revise la [información general sobre el aprovisionamiento](../iot-dps/about-iot-dps.md#provisioning-process) antes de continuar.

Las tareas son las siguientes:

1. Generar certificados y claves.
1. Crear una **inscripción individual** para un dispositivo o una **inscripción de grupo** para un conjunto de dispositivos.
1. Instalar el entorno de ejecución de IoT Edge y registrar el dispositivo con IoT Hub.

El uso de certificados X.509 como un mecanismo de atestación es una manera excelente para escalar la producción y simplificar el aprovisionamiento de dispositivos. Normalmente, los certificados X.509 están organizados en una cadena de certificados de confianza. Comenzando por un certificado raíz de confianza o autofirmado, cada certificado de la cadena firma al certificado inmediatamente inferior. Este patrón crea una cadena delegada de confianza desde el certificado raíz a través de todos los certificados intermedios hasta el certificado "hoja" final que está instalado en un dispositivo.

## <a name="prerequisites"></a>Prerrequisitos

<!-- Cloud resources prerequisites H3 and content -->
[!INCLUDE [iot-edge-prerequisites-at-scale-cloud-resources.md](../../includes/iot-edge-prerequisites-at-scale-cloud-resources.md)]

### <a name="iot-edge-installation"></a>Instalación de IoT Edge

Un dispositivo Linux físico o virtual para que sea el dispositivo IoT Edge.

## <a name="generate-device-identity-certificates"></a>Generación de los certificados de identidad del dispositivo

El certificado de identidad del dispositivo es un certificado de hoja que se conecta a través de una cadena de certificados de confianza con el certificado de la entidad de certificación X.509 (CA) superior. El nombre común (CN) del certificado de identidad del dispositivo debe estar establecido en el id. de dispositivo que quiere que tenga el dispositivo en el centro de IoT.

Los certificados de identidad del dispositivo solo se usan para aprovisionar al dispositivo IoT Edge y autenticarlo con Azure IoT Hub. No son certificados de firma, a diferencia de los certificados de CA que el dispositivo IoT Edge presenta a los módulos o los dispositivos hoja para la comprobación. Para obtener más información, consulte los [detalles de uso de los certificados de Azure IoT Edge](iot-edge-certs.md).

Después de crear el certificado de identidad del dispositivo, debe tener dos archivos: un archivo. cer o. pem que contiene la parte pública del certificado y un archivo. cer o. pem con la clave privada del certificado. Si planea usar una inscripción de grupo en DPS, también necesitará la parte pública de un certificado de CA raíz o intermedio en la misma cadena de certificados de confianza.

Necesita los siguientes archivos para configurar el aprovisionamiento automático con X.509:

* El certificado de identidad del dispositivo y su certificado de clave privada. El certificado de identidad de dispositivo se carga en DPS si crea una inscripción individual. La clave privada se pasa al entorno de ejecución de Azure IoT Edge.
* Una cadena de certificados completa, que debe contener al menos la identidad del dispositivo y los certificados intermedios. La cadena de certificados completa se pasa al entorno de ejecución de Azure IoT Edge.
* Un certificado intermedio o raíz de entidad de certificación de la cadena de certificados de confianza. Este certificado se carga en DPS si crea una inscripción de grupo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

> [!NOTE]
> Actualmente, una limitación en libiothsm impide el uso de certificados que expiran el 1 de enero de 2038 o en una fecha posterior.

:::moniker-end
<!-- end 1.1 -->

### <a name="use-test-certificates-optional"></a>Uso de certificados de prueba (opcional)

Si no tiene una entidad de certificación disponible para crear nuevos certificados de identidad y quiere probar este escenario, el repositorio de Git de Azure IoT Edge contiene scripts que puede usar para generar certificados de prueba. Estos certificados están diseñados solo para pruebas de desarrollo y no deben usarse en producción.

Para crear certificados de prueba, siga los pasos descritos en [Creación de certificados de demostración para probar las características de dispositivo IoT Edge](how-to-create-test-certificates.md). Complete las dos secciones necesarias para configurar los scripts de generación de certificados y crear un certificado de CA raíz. A continuación, siga los pasos para crear un certificado de identidad del dispositivo. Cuando haya terminado, debe tener la cadena de certificados y el par de claves siguientes:

* `<WRKDIR>/certs/iot-edge-device-identity-<name>-full-chain.cert.pem`
* `<WRKDIR>/private/iot-edge-device-identity-<name>.key.pem`

Necesita ambos certificados en el dispositivo IoT Edge. Si va a usar la inscripción individual en DPS, deberá cargar el archivo. cert.pem. Si va a usar la inscripción de grupos en DPS, también necesitará un certificado de CA raíz o intermedio en la misma cadena de certificados de confianza que se va a cargar. Si usa certificados de demostración, use el certificado `<WRKDIR>/certs/azure-iot-test-only.root.ca.cert.pem` para la inscripción de grupos.

<!-- Create a DPS enrollment using X.509 certificates H2 and content -->
[!INCLUDE [iot-edge-create-dps-enrollment-x509.md](../../includes/iot-edge-create-dps-enrollment-x509.md)]

<!-- Install IoT Edge on Linux H2 and content -->
[!INCLUDE [install-iot-edge-linux.md](../../includes/iot-edge-install-linux.md)]

## <a name="provision-the-device-with-its-cloud-identity"></a>Aprovisionamiento del dispositivo con su identidad de nube

Una vez que el entorno de ejecución esté instalado en el dispositivo, configure el dispositivo con la información que usa para conectarse a Device Provisioning Service y a IoT Hub.

Tenga lista la siguiente información:

* El valor de **Ámbito de id.** del DPS. Puede recuperar este valor de la página Información general de la instancia de DPS en Azure Portal.
* El archivo de cadena de certificados de identidad del dispositivo en el dispositivo.
* El archivo de clave de identidad del dispositivo en el dispositivo.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/iotedge/config.yaml
   ```

1. Busque la sección configuraciones de aprovisionamiento del archivo. Quite las marcas de comentario de las líneas de aprovisionamiento de certificado X.509 de DPS y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   La línea `provisioning:` no debe ir precedida por espacios en blanco y se debe aplicar una sangría de dos espacios a los elementos anidados.

   ```yml
   # DPS X.509 provisioning configuration
   provisioning:
     source: "dps"
     global_endpoint: "https://global.azure-devices-provisioning.net"
     scope_id: "SCOPE_ID_HERE"
     attestation:
       method: "x509"
   #   registration_id: "OPTIONAL_REGISTRATION_ID_LEAVE_COMMENTED_OUT_TO_REGISTER_WITH_CN_OF_IDENTITY_CERT"
       identity_cert: "REQUIRED_URI_TO_DEVICE_IDENTITY_CERTIFICATE_HERE"
       identity_pk: "REQUIRED_URI_TO_DEVICE_IDENTITY_PRIVATE_KEY_HERE"
   #  always_reprovision_on_startup: true
   #  dynamic_reprovisioning: false
   ```

1. Actualice los valores de `scope_id`, `identity_cert` y `identity_pk` con la información de DPS y del dispositivo.

   Al agregar el certificado X.509 y la información de clave al archivo config.yaml, las rutas de acceso se deben especificas como URI de archivo. Por ejemplo:

   `file:///<path>/identity_certificate_chain.pem`
   `file:///<path>/identity_key.pem`

1. De manera opcional, proporcione el `registration_id` del dispositivo, que debe coincidir con el nombre común (CN) del certificado de identidad. Si deja esa línea comentada, se aplicará automáticamente el CN.

1. También puede usar las líneas `always_reprovision_on_startup` o `dynamic_reprovisioning` para configurar el comportamiento de reaprovisionamiento del dispositivo. Si un dispositivo se establece para que se vuelva a aprovisionar en el inicio, siempre intentará aprovisionar con DPS primero y, a continuación, revertir a la copia de seguridad de aprovisionamiento si se produce un error. Si un dispositivo se establece para que se vuelva a aprovisionar dinámicamente, IoT Edge se reiniciará y volverá a aprovisionar si se detecta un evento de reaprovisionamiento. Para más información, consulte [Conceptos sobre el reaprovisionamiento de dispositivos de IoT Hub](../iot-dps/concepts-device-reprovision.md).

1. Guarde y cierre el archivo config.yaml.

1. Reinicie el entorno de ejecución de IoT Edge para que aplique todos los cambios de configuración realizados en el dispositivo.

   ```bash
   sudo systemctl restart iotedge
   ```

:::moniker-end
<!-- end 1.1. -->

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

1. Cree un archivo de configuración para el dispositivo basándose en un archivo de plantilla que se proporciona como parte de la instalación de IoT Edge.

   ```bash
   sudo cp /etc/aziot/config.toml.edge.template /etc/aziot/config.toml
   ```

1. Abra el archivo de configuración en el dispositivo IoT Edge.

   ```bash
   sudo nano /etc/aziot/config.toml
   ```

1. Busque la sección **Provisioning** (Aprovisionamiento) del archivo. Quite las marcas de comentario de las líneas de aprovisionamiento de DPS con el certificado X.509 y asegúrese de que cualquier otra línea de aprovisionamiento esté comentada.

   ```toml
   # DPS provisioning with X.509 certificate
   [provisioning]
   source = "dps"
   global_endpoint = "https://global.azure-devices-provisioning.net"
   id_scope = "SCOPE_ID_HERE"
   
   [provisioning.attestation]
   method = "x509"
   registration_id = "REGISTRATION_ID_HERE"

   identity_cert = "DEVICE_IDENTITY_CERTIFICATE_HERE"

   identity_pk = "DEVICE_IDENTITY_PRIVATE_KEY_HERE"
   ```

1. Actualice el valor de `id_scope` con el identificador de ámbito que copió de la instancia de DPS.

1. Proporcione un `registration_id` para el dispositivo, que es el identificador que el dispositivo tendrá en IoT Hub. El identificador de registro debe coincidir con el nombre común (CN) del certificado de identidad.

1. Actualice los valores de `identity_cert` y `identity_pk` con la información del certificado y el dispositivo.

   El valor del certificado de identidad se puede proporcionar como un URI de archivo o se puede emitir dinámicamente mediante EST o una entidad de certificación local. Quite la marca de comentario de una sola línea, en función del formato que decida usar.

   El valor de clave privada de identidad se puede proporcionar como un URI de archivo o como un URI de PKCS#11. Quite la marca de comentario de una sola línea, en función del formato que decida usar.

   Si usa cualquier URI de PKCS#11, busque la sección **PKCS#11** en el archivo de configuración y proporcione información sobre la configuración de PKCS#11.

1. Guarde y cierre el archivo.

1. Aplique los cambios de configuración que ha realizado a IoT Edge.

   ```bash
   sudo iotedge config apply
   ```

:::moniker-end
<!-- end 1.2 -->

## <a name="verify-successful-installation"></a>Comprobación de instalación correcta

Si el entorno de ejecución se inició correctamente, puede ir a IoT Hub y empezar a implementar módulos de IoT Edge en el dispositivo.

# <a name="individual-enrollment"></a>[Inscripción individual](#tab/individual-enrollment)

Puede comprobar que la inscripción individual que ha creado en Device Provisioning Service se ha usado. En Azure Portal, vaya a la instancia de Device Provisioning Service. Abra los detalles de la inscripción para la inscripción individual que ha creado. Tenga en cuenta que el estado de la inscripción está **asignado** y se muestra el id. de dispositivo.

# <a name="group-enrollment"></a>[Inscripción de grupo](#tab/group-enrollment)

Puede comprobar que la inscripción de grupo que ha creado en Device Provisioning Service se ha usado. En Azure Portal, vaya a la instancia de Device Provisioning Service. Abra los detalles de la inscripción para la inscripción de grupo que ha creado. Vaya a la pestaña **Entradas de registro** para ver todos los dispositivos registrados en ese grupo.

---

Use los siguientes comandos en el dispositivo para comprobar que la instancia de IoT Edge se haya instalado e iniciado correctamente.

<!-- 1.1 -->
:::moniker range="iotedge-2018-06"

Compruebe el estado del servicio IoT Edge.

```cmd/sh
systemctl status iotedge
```

Examine los registros del servicio.

```cmd/sh
journalctl -u iotedge --no-pager --no-full
```

Enumere los módulos en ejecución.

```cmd/sh
iotedge list
```

:::moniker-end

<!-- 1.2 -->
:::moniker range=">=iotedge-2020-11"

Compruebe el estado del servicio IoT Edge.

```cmd/sh
sudo iotedge system status
```

Examine los registros del servicio.

```cmd/sh
sudo iotedge system logs
```

Enumere los módulos en ejecución.

```cmd/sh
sudo iotedge list
```

:::moniker-end

## <a name="next-steps"></a>Pasos siguientes

El proceso de inscripción en Device Provisioning Service permite establecer el id. de dispositivo y las etiquetas del dispositivo gemelo al mismo tiempo que se aprovisiona el nuevo dispositivo. Puede usar esos valores para dirigirse a dispositivos individuales o grupos de dispositivos con la administración automática de dispositivos. Aprenda a [implementar y supervisar los módulos de IoT Edge a escala mediante Azure Portal](how-to-deploy-at-scale.md) o la [CLI de Azure](how-to-deploy-cli-at-scale.md).
