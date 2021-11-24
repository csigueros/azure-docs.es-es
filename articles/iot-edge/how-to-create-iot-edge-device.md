---
title: 'Creación de un dispositivo IoT Edge: Azure IoT Edge | Microsoft Docs'
description: Obtenga información sobre las opciones de la plataforma y del aprovisionamiento para crear un dispositivo IoT Edge.
author: kgremban
ms.service: iot-edge
services: iot-edge
ms.topic: conceptual
ms.date: 11/11/2021
ms.author: kgremban
ms.openlocfilehash: 6fdfbd937e767cde118ed80476e73d6207c657e6
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132495285"
---
# <a name="create-an-iot-edge-device"></a>Crear un dispositivo IoT Edge

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

En este artículo se proporciona información general sobre las opciones disponibles para instalar y aprovisionar IoT Edge en los dispositivos.

En este artículo se proporcionan todas las opciones de la solución de IoT Edge y se le ofrece ayuda con la:

* [Elección de una plataforma](#choose-a-platform)
* [Elección de cómo aprovisionar los dispositivos](#choose-how-to-provision-your-devices)
* [Elección de un método de autenticación](#choose-an-authentication-method)

Al final de este artículo, tendrá una imagen clara de las opciones de plataforma, aprovisionamiento y autenticación que desea usar para la solución de IoT Edge.

## <a name="get-started"></a>Introducción

Si sabe qué tipo de plataforma, aprovisionamiento y opciones de autenticación desea usar para crear un dispositivo IoT Edge, use los vínculos de la tabla siguiente para empezar.

Si desea conocer más detalles sobre cómo elegir la opción adecuada, continúe con este artículo para más información.

<!--1.1-->
:::moniker range="iotedge-2018-06"

|    | Contenedores Linux en hosts Linux | Contenedores Linux en hosts Windows | Contenedores Windows en hosts Windows |
|--| ----- | ---------------- | ------- |
| **Aprovisionamiento manual (un solo dispositivo)** | [Certificados X.509](how-to-provision-single-device-linux-x509.md)<br><br>[Claves simétricas](how-to-provision-single-device-linux-symmetric.md) | [Certificados X.509](how-to-provision-single-device-linux-on-windows-x509.md)<br><br>[Claves simétricas](how-to-provision-single-device-linux-on-windows-symmetric.md) | [Certificados X.509](how-to-provision-single-device-windows-x509.md)<br><br>[Claves simétricas](how-to-provision-single-device-windows-symmetric.md) |
| **Aprovisionamiento automático (dispositivos a escala)** | [Certificados X.509](how-to-provision-devices-at-scale-linux-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-tpm.md)<br><br>[Claves simétricas](how-to-provision-devices-at-scale-linux-symmetric.md) | [Certificados X.509](how-to-provision-devices-at-scale-linux-on-windows-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)<br><br>[Claves simétricas](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md) | [Certificados X.509](how-to-provision-devices-at-scale-windows-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-windows-tpm.md)<br><br>[Claves simétricas](how-to-provision-devices-at-scale-windows-symmetric.md) |

:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"

>[!NOTE]
>En la tabla siguiente se reflejan los escenarios admitidos para IoT Edge versión 1.2. Para ver contenido sobre los dispositivos de Windows, cambie a la versión [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) de este artículo.

|    | Contenedores Linux en hosts Linux |
|--| ----- |
| **Aprovisionamiento manual (un solo dispositivo)** | [Certificados X.509](how-to-provision-single-device-linux-x509.md)<br><br>[Claves simétricas](how-to-provision-single-device-linux-symmetric.md) |
| **Aprovisionamiento automático (dispositivos a escala)** | [Certificados X.509](how-to-provision-devices-at-scale-linux-x509.md)<br><br>[TPM](how-to-provision-devices-at-scale-linux-tpm.md)<br><br>[Claves simétricas](how-to-provision-devices-at-scale-linux-symmetric.md) |

:::moniker-end

## <a name="terms-and-concepts"></a>Términos y conceptos

Si aún no está familiarizado con la terminología de IoT Edge, repase algunos conceptos clave:

**Entorno de ejecución de Azure IoT Edge**: el [entorno de ejecución de Azure IoT Edge](iot-edge-runtime.md) es una colección de programas que convierten un dispositivo en un dispositivo IoT Edge. Colectivamente, los componentes del entorno de ejecución de Azure IoT Edge permiten a los dispositivos IoT Edge ejecutar los módulos de IoT Edge.

**Aprovisionamiento**: se debe aprovisionar cada dispositivo IoT Edge. El aprovisionamiento es un proceso de dos pasos. El primer paso consiste en registrar el dispositivo en un centro de IoT que crea una identidad en la nube que el dispositivo usa para establecer la conexión a su centro de conectividad. El segundo paso es configurar el dispositivo con su identidad en la nube. El aprovisionamiento se puede realizar manualmente por dispositivo o a escala mediante [IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md).

**Autenticación**: los dispositivos IoT Edge deben comprobar su identidad cuando se conectan a IoT Hub. Puede elegir qué método de autenticación usar, como contraseñas de clave simétrica, huellas digitales de certificados o módulos de plataforma segura (TPM).

## <a name="choose-a-platform"></a>Elección de una plataforma

El sistema operativo del contenedor y el sistema operativo host hacen referencia a las opciones de plataforma. El sistema operativo del contenedor es el sistema operativo que se usa dentro de los contenedores del entorno de ejecución de Azure IoT Edge y del módulo. El sistema operativo host es el sistema operativo del dispositivo en el que se ejecutan los contenedores y módulos del entorno de ejecución de Azure IoT Edge.

Hay tres opciones de plataforma para los dispositivos IoT Edge.

* **Contenedores de Linux en hosts de Linux**: ejecute contenedores de IoT Edge basados en Linux directamente en un host de Linux. En la documentación de IoT Edge, también puede ver que se hace referencia a esta opción como **Linux** y **contenedores de Linux** para simplificar.

* **Contenedores de Linux en host de Windows**: ejecute contenedores de IoT Edge basados en Linux en una máquina virtual Linux en un host de Windows. En la documentación de IoT Edge, también puede ver que se hace referencia a esta opción como **Linux en Windows**, **IoT Edge para Linux en Windows** y **EFLOW**.

* **Contenedores de Windows en host de Windows**: ejecute contenedores de IoT Edge basados en Windows directamente en un host de Windows. En la documentación de IoT Edge, también puede ver que se hace referencia a esta opción como **Windows** y **contenedores de Windows** para simplificar.

para la información más reciente sobre qué sistemas operativos se admiten actualmente en los escenarios de producción, consulte [Sistemas compatibles con Azure IoT Edge](support.md#operating-systems).

### <a name="linux-containers-on-linux"></a>Contenedores de Linux en Linux

En el caso de los dispositivos Linux, el entorno de ejecución de Azure IoT Edge se instala directamente en el dispositivo host.

IoT Edge admite dispositivos Linux X64, ARM32 y ARM64. Microsoft ofrece paquetes de instalación para los sistemas operativos Ubuntu Server 18.04 y Raspberry Pi OS Stretch.

La compatibilidad con dispositivos ARM64 está en [versión preliminar pública](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

### <a name="linux-containers-on-windows"></a>Contenedores de Linux en Windows

<!--1.1-->
:::moniker range="iotedge-2018-06"
IoT Edge para Linux en Windows hospeda una máquina virtual Linux en el dispositivo Windows. La máquina virtual viene precompilada con el entorno de ejecución de Azure IoT Edge y las actualizaciones se administran mediante Microsoft Update.

IoT Edge para Linux en Windows es el método recomendado para ejecutar IoT Edge en dispositivos Windows. Para más información, consulte [¿Qué es Azure IoT Edge para Linux en Windows?](iot-edge-for-linux-on-windows.md)

Actualmente, IoT Edge para Linux en Windows no admite la versión 1.2 de Azure IoT Edge.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
Actualmente, IoT Edge versión 1.2 no admite IoT Edge para Linux en Windows. Para más información sobre IoT Edge para Linux en Windows, consulte la sección sobre la versión [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) de este artículo.
:::moniker-end

### <a name="windows-containers-on-windows"></a>Contenedores Windows en Windows

<!--1.1-->
:::moniker range="iotedge-2018-06"
En el caso de los dispositivos Windows, el entorno de ejecución de Azure IoT Edge se instala directamente en el dispositivo host. Esta plataforma le permite compilar, implementar y ejecutar los módulos de IoT Edge como contenedores Windows.

   > [!NOTE]
   > Los contenedores de Windows no son la manera recomendada de ejecutar IoT Edge en dispositivos Windows, ya que no se admiten después de la versión 1.1 de Azure IoT Edge.
   >
   > Considere la posibilidad de utilizar IoT Edge para Linux en Windows, la cual se admite en versiones futuras.
:::moniker-end

<!--1.2-->
:::moniker range=">=iotedge-2020-11"
La versión IoT Edge 1.2 no es compatible con contenedores de Windows. Los contenedores de Windows no se admiten después de la versión 1.1. Para más información sobre IoT Edge con contenedores de Windows, consulte la sección sobre la versión [IoT Edge 1.1](?view=iotedge-2018-06&preserve-view=true) de este artículo.
:::moniker-end

## <a name="choose-how-to-provision-your-devices"></a>Elección de cómo aprovisionar los dispositivos

Puede aprovisionar un único dispositivo o varios dispositivos a escala, en función de las necesidades de la solución de IoT Edge.

Las opciones disponibles para autenticar las comunicaciones entre los dispositivos IoT Edge y los centros de IoT dependen del método de aprovisionamiento que elija. Puede leer más sobre esas opciones en la [sección Elegir un método de autenticación](#choose-an-authentication-method).

### <a name="single-device"></a>Un solo dispositivo

El aprovisionamiento de un solo dispositivo hace referencia al aprovisionamiento de un dispositivo IoT Edge sin la asistencia de [IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md) (DPS). Verá que también se hace referencia al aprovisionamiento de un solo dispositivo como **aprovisionamiento manual**.

Con el aprovisionamiento de un solo dispositivo, deberá especificar manualmente la información de aprovisionamiento, en forma de cadena de conexión, en los dispositivos. El aprovisionamiento manual es rápido y fácil de configurar para unos pocos dispositivos, pero la carga de trabajo aumentará con el número de dispositivos. Tenga esto en cuenta cuando esté considerando la escalabilidad de la solución.

Los métodos de autenticación de **clave simétrica** y **X.509 autofirmado** están disponibles para el aprovisionamiento manual. Puede leer más sobre esas opciones en la [sección Elegir un método de autenticación](#choose-an-authentication-method).

### <a name="devices-at-scale"></a>Dispositivos a escala

El aprovisionamiento de dispositivos a escala hace referencia al aprovisionamiento de uno o varios dispositivos IoT Edge con la ayuda de [IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md). También se hace referencia al aprovisionamiento a escala como **aprovisionamiento automático**.

Si la solución de IoT Edge requiere más de un dispositivo, el aprovisionamiento automático mediante DPS le ahorra el esfuerzo de escribir manualmente la información de aprovisionamiento en los archivos de configuración de cada dispositivo que quiera usar. Este modelo automatizado se puede escalar a millones de dispositivos IoT Edge. Puede ver el flujo de aprovisionamiento automatizado en la [sección En segundo plano de la página de información general de IoT Hub Device Provisioning Service](../iot-dps/about-iot-dps.md#behind-the-scenes).

Puede proteger la solución de IoT Edge con el método de autenticación que prefiera. Los métodos de autenticación de **clave simétrica**, **certificados X.509** y **atestación con el módulo de plataforma segura (TPM)** están disponibles para el aprovisionamiento de dispositivos a escala. Puede leer más sobre esas opciones en la [sección Elegir un método de autenticación](#choose-an-authentication-method).

Para ver más características de DPS, consulte la [sección Características de la página de información general](../iot-dps/about-iot-dps.md#features-of-the-device-provisioning-service).

## <a name="choose-an-authentication-method"></a>Elección de un método de autenticación

### <a name="symmetric-keys-attestation"></a>Atestación de claves simétricas

La atestación de clave simétrica es un enfoque sencillo para autenticar un dispositivo. Este método de atestación representa una experiencia de "Hola mundo" para los desarrolladores que no estén familiarizados con el aprovisionamiento de dispositivos, o no tengan estrictos requisitos de seguridad.

cuando se crea una nueva identidad de dispositivo en IoT Hub, el servicio crea dos claves. Debe colocar una de las claves en el dispositivo y este presenta la clave a IoT Hub al autenticarse.

Este método de autenticación es más rápido para comenzar, pero no es tan seguro. El aprovisionamiento de dispositivos mediante un TPM o certificado X.509 es más seguro y se debe usar para soluciones con requisitos de seguridad más estrictos.

### <a name="x509-certificate-attestation"></a>Atestación de certificados de cliente X.509

El uso de certificados X.509 como un mecanismo de atestación es una manera excelente para escalar la producción y simplificar el aprovisionamiento de dispositivos. Normalmente, los certificados X.509 están organizados en una cadena de certificados de confianza. Comenzando por un certificado raíz de confianza o autofirmado, cada certificado de la cadena firma al certificado inmediatamente inferior. Este patrón crea una cadena delegada de confianza desde el certificado raíz a través de todos los certificados intermedios hasta el certificado "hoja" final que está instalado en un dispositivo.

se crean dos certificados de identidad X.509 y se colocan en el dispositivo. Cuando se crea una identidad de dispositivo nueva en IoT Hub, se proporcionan huellas digitales desde ambos certificados. Cuando el dispositivo se autentica en IoT Hub, presenta un certificado, y IoT Hub comprueba que el certificado coincide con su huella digital.

Este método de autenticación es más seguro que el de las claves simétricas y se recomienda para los escenarios de producción.

### <a name="trusted-platform-module-tpm-attestation"></a>Atestación del módulo de plataforma segura (TPM)

El uso de la atestación de TPM es el método más seguro para el aprovisionamiento de dispositivos, ya que proporciona características de autenticación en software y en hardware. Cada chip del TPM usa una clave de aprobación única para comprobar su autenticidad.

La atestación de TPM solo está disponible para el aprovisionamiento a escala con DPS y solo admite inscripciones individuales no de grupo. Las inscripciones de grupos no están disponibles debido a la naturaleza específica del dispositivo del TPM.

TPM 2.0 es necesario cuando se usa la atestación de TPM con el servicio de aprovisionamiento de dispositivos.

Este método de autenticación es más seguro que el de las claves simétricas y se recomienda para los escenarios de producción.

## <a name="next-steps"></a>Pasos siguientes

Puede usar el índice para ir a la guía completa adecuada para crear un dispositivo IoT Edge para los requisitos de plataforma, aprovisionamiento y autenticación de la solución de IoT Edge.

También puede usar los vínculos siguientes para ir al artículo correspondiente.

### <a name="linux-containers-on-linux-hosts"></a>Contenedores Linux en hosts Linux

**Aprovisionamiento manual de un solo dispositivo**:

* [Aprovisionamiento de un solo dispositivo Linux mediante certificados X.509](how-to-provision-single-device-linux-x509.md)
* [Aprovisionamiento de un solo dispositivo Linux mediante claves simétricas](how-to-provision-single-device-linux-symmetric.md)

**Aprovisionamiento de varios dispositivos a escala**:

* [Aprovisionamiento de dispositivos Linux a escala mediante certificados X.509](how-to-provision-devices-at-scale-linux-x509.md)
* [Aprovisionamiento de dispositivos Linux a escala mediante atestación de TPM](how-to-provision-devices-at-scale-linux-tpm.md)
* [Aprovisionamiento de dispositivos Linux a escala mediante claves simétricas](how-to-provision-devices-at-scale-linux-symmetric.md)

<!--1.1-->
:::moniker range="iotedge-2018-06"
### <a name="linux-containers-on-windows-hosts"></a>Contenedores Linux en hosts Windows

**Aprovisionamiento manual de un solo dispositivo**:

* [Aprovisionamiento de un solo dispositivo Linux en Windows mediante certificados X.509](how-to-provision-single-device-linux-on-windows-x509.md)
* [Aprovisionamiento de un solo dispositivo Linux en Windows mediante claves simétricas](how-to-provision-single-device-linux-on-windows-symmetric.md)

**Aprovisionamiento de varios dispositivos a escala**:

* [Aprovisionamiento de dispositivos Linux en Windows a escala mediante certificados X.509](how-to-provision-devices-at-scale-linux-on-windows-x509.md)
* [Aprovisionamiento de dispositivos Linux en Windows a escala mediante atestación de TPM](how-to-provision-devices-at-scale-linux-on-windows-tpm.md)
* [Aprovisionamiento de dispositivos Linux en Windows a escala mediante claves simétricas](how-to-provision-devices-at-scale-linux-on-windows-symmetric.md)

### <a name="windows-containers-on-windows-hosts"></a>Contenedores Windows en hosts Windows

**Aprovisionamiento manual de un solo dispositivo**:

* [Aprovisionamiento de un solo dispositivo Windows mediante certificados X.509](how-to-provision-single-device-windows-x509.md)
* [Aprovisionamiento de un solo dispositivo Windows mediante claves simétricas](how-to-provision-single-device-windows-symmetric.md)

**Aprovisionamiento de varios dispositivos a escala**:

* [Aprovisionamiento de dispositivos Windows a escala mediante certificados X.509](how-to-provision-devices-at-scale-windows-x509.md)
* [Aprovisionamiento de dispositivos Windows a escala mediante atestación de TPM](how-to-provision-devices-at-scale-windows-tpm.md)
* [Aprovisionamiento de dispositivos Windows a escala mediante claves simétricas](how-to-provision-devices-at-scale-windows-symmetric.md)
:::moniker-end
