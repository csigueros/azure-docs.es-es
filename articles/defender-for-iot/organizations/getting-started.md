---
title: 'Inicio rápido: Introducción'
description: En este inicio rápido, aprenderá a usar el flujo de trabajo básico para la implementación de Defender para IoT.
ms.topic: quickstart
ms.date: 11/09/2021
ms.openlocfilehash: 85bd254236a6d08d8f232e14e5a41dab99f0af04
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132283873"
---
# <a name="quickstart-get-started-with-defender-for-iot"></a>Inicio rápido: Introducción a Defender para IoT

En este artículo se proporciona información general de los pasos para configurar Microsoft Defender para IoT. El proceso requiere que realice lo siguiente:

- Registre la suscripción y los sensores en Defender para IoT en Azure Portal.
- Instale el software de la consola de administración local y el sensor.
- Realice la activación inicial del sensor y la consola de administración.

## <a name="permission-requirements"></a>Requisitos de permisos

### <a name="for-sensors-and-on-premises-management-consoles"></a>Sensores y consolas de administración locales

Algunos de los pasos de configuración requieren permisos de usuario específicos.

Los permisos de usuario administrativo son necesarios para activar el sensor y la consola de administración, cargar certificados SSL/TLS y generar nuevas contraseñas.

### <a name="for-defender-for-iot-in-the-azure-portal"></a>Para Defender para IoT en Azure Portal

En la tabla siguiente se describen los permisos de acceso de usuarios para las herramientas del portal de Azure:

| Permiso | Lector de seguridad | Administrador de seguridad | Colaborador de la suscripción | Propietario de la suscripción |
|--|--|--|--|--|
| Visualización de detalles y acceso a software, archivos de activación y paquetes de inteligencia sobre amenazas  | ✓ | ✓ | ✓ | ✓ |
| Sensores incorporados  |  |  ✓ | ✓ | ✓ |
| Incorporación de suscripciones y actualización de dispositivos confirmados  |  |  | ✓ | ✓ |
| Recuperación de contraseñas  | ✓  |  ✓ | ✓ | ✓ |

## <a name="identify-the-solution-infrastructure"></a>Identificación de la infraestructura de la solución

**Aclaración de las necesidades de configuración de red**

Investigue lo siguiente:

- Arquitectura de red
- Ancho de banda supervisado
- Requisitos para crear certificados
- Otros detalles de la red.

Para más información, consulte [Acerca de la configuración de red de Microsoft Defender para IoT](how-to-set-up-your-network.md).

**Aclaración de qué sensores y dispositivos de la consola de administración son necesarios para controlar la carga de red**

Microsoft Defender para IoT admite implementaciones físicas y virtuales. En el caso de las implementaciones físicas, puede comprar varios dispositivos certificados. Para más información, vea [Identificación de los dispositivos necesarios](how-to-identify-required-appliances.md).

Se recomienda calcular el número aproximado de dispositivos que se van a supervisar. Más adelante, cuando registre su suscripción de Azure en el portal, se le pedirá que escriba este número. Los números se pueden agregar en intervalos de 1000; por ejemplo, 1000, 2000, 3000, etc. El número de dispositivos supervisados se denomina *dispositivos confirmados*.

## <a name="register-with-microsoft-defender-for-iot"></a>Registro con Microsoft Defender para IoT

El registro incluye:

- Incorporación de las suscripciones de Azure a Defender para IoT
- Definición de los dispositivos confirmados
- Descarga de un archivo de activación para la consola de administración local

También puede usar una suscripción de prueba para supervisar 1000 dispositivos de forma gratuita durante 30 días. Para más información, consulte [Incorporación de una suscripción de prueba](how-to-manage-subscriptions.md#onboard-a-trial-subscription).

**Para realizar el registro**:

1. Vaya a [Defender para IoT: Introducción](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) en Azure Portal.

1. Seleccione **Onboard subscription** (Incorporar suscripción).

1. En la página **Precios**, seleccione una suscripción o cree una y agregue el número de dispositivos confirmados.

1. Seleccione la pestaña **Download the on-premises management console** (Descargar la consola de administración local) y guarde el archivo de activación descargado. Este archivo contiene los dispositivos confirmados agregados que ha definido. El archivo se cargará en la consola de administración después del inicio de sesión inicial.

Para obtener información sobre cómo retirar una suscripción, consulte [Retirada de una suscripción](how-to-manage-subscriptions.md#offboard-a-subscription).

## <a name="install-and-set-up-the-on-premises-management-console"></a>Instalación y configuración de la consola de administración local

Después de adquirir su dispositivo de la consola de administración local:

- Descargue el paquete ISO desde Azure Portal.
- Instale el software.
- Active y lleve a cabo la configuración inicial de la consola de administración.

**Para realizar la instalación y la configuración**:

1. Vaya a [Defender para IoT: Introducción](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) en Azure Portal.

1. Seleccione la pestaña **On-premises management console** (Consola de administración local).

1. Elija una versión y seleccione **Descargar**.

1. Instale el software de la consola de administración local. Para más información, vea [Instalación de Defender para IoT](how-to-install-software.md).

1. Active y configure la consola de administración. Para más información, vea [Activación y configuración de la consola de administración local](how-to-activate-and-set-up-your-on-premises-management-console.md).

## <a name="onboard-a-sensor"></a>Incorporación de sensores ##

Para incorporar un sensor, debe registrarlo en Microsoft Defender para IoT y descargar un archivo de activación de sensor.

1. Defina un nombre para el sensor y asócielo con una suscripción.

1. Elija un modo de conexión del sensor:

   - **Sensores conectados a la nube**: la información que detectan los sensores se muestra en la consola del sensor. Además, se proporciona información de alertas mediante un centro de IoT, que puede compartirse con otros servicios de Azure, como Microsoft Sentinel. También puede elegir insertar automáticamente paquetes de inteligencia sobre amenazas desde Defender para IoT a los sensores. Para más información, consulte [Investigación y paquetes de inteligencia sobre amenazas](how-to-work-with-threat-intelligence-packages.md).

   - **Locally managed sensors** (Sensores administrados de forma local): la información que detectan los sensores se muestra en la consola del sensor. Si trabaja en una red aislada y quiere una vista unificada de toda la información detectada por varios sensores administrados de forma local, trabaje con la consola de administración local.

1. Seleccione un sitio al que asociar el sensor dentro de un centro de IoT. Elija un centro de IoT que sirva como puerta de enlace entre este sensor y Microsoft Defender para IoT. Defina el nombre para mostrar y la zona. También puede agregar etiquetas descriptivas. El nombre para mostrar, la zona y las etiquetas son entradas descriptivas en la página [Sites and sensors](how-to-manage-sensors-on-the-cloud.md#view-onboarded-sensors) (Sitios y sensores).

1. Seleccione **Registrar**.

1. Seleccione **Download activation file** (Descargar archivo de activación).

Para más información sobre la incorporación, consulte [Incorporación y administración de sensores con Defender para IoT](how-to-manage-sensors-on-the-cloud.md).

## <a name="install-and-set-up-the-sensor"></a>Instalación y configuración del sensor

Descargue el paquete ISO de Azure Portal, instale el software y configure el sensor.

1. Vaya a [Defender para IoT: Introducción](https://portal.azure.com/#blade/Microsoft_Azure_IoT_Defender/IoTDefenderDashboard/Getting_Started) en Azure Portal.

1. Seleccione **Set up sensor** (Configurar sensor).

1. Elija una versión y seleccione **Descargar**.

1. Instale el software del sensor. Para más información, vea [Instalación de Defender para IoT](how-to-install-software.md).

1. Active y configure el sensor. Para más información, vea [Inicio de sesión y activación de un sensor](how-to-activate-and-set-up-your-sensor.md).

## <a name="connect-sensors-to-an-on-premises-management-console"></a>Conecte los sensores a una consola de administración local.

Conecte los sensores a una consola de administración para garantizar lo siguiente:

- Los sensores envían información de inventario de alertas y dispositivos a la consola de administración local.

- La consola de administración local puede realizar copias de seguridad de los sensores, administrar las alertas que los sensores detectan, investigar desconexiones de los sensores y llevar a cabo otras actividades en sensores conectados.

Se recomienda agrupar varios sensores que supervisan las mismas redes en una zona. Al hacerlo, se fusionará la información recopilada por varios sensores.

Para más información, vea [Conexión de los sensores a la consola de administración local](how-to-activate-and-set-up-your-on-premises-management-console.md#connect-sensors-to-the-on-premises-management-console).

## <a name="populate-microsoft-sentinel-with-alert-information-optional"></a>Rellenado de Microsoft Sentinel con información de alerta (opcional)

Envíe información de alertas a Microsoft Sentinel mediante la configuración de Microsoft Sentinel. Consulte [Conexión de los datos de Defender para IoT con Microsoft Sentinel](how-to-configure-with-sentinel.md).  

## <a name="next-steps"></a>Pasos siguientes ##

[Bienvenida a Microsoft Defender para IoT](overview.md)

[Arquitectura de Microsoft Defender para IoT](architecture.md)
