---
title: Acerca de la integración de Cisco ISE pxGrid
description: La unión de las funcionalidades de Defender para IoT y Cisco ISE pxGrid proporciona a los equipos de seguridad una visibilidad de dispositivos sin precedentes en el ecosistema de la empresa.
ms.date: 12/28/2020
ms.topic: how-to
ms.openlocfilehash: 00151f2e407c65d024f3bd59bdaa85a08ae677f4
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113019018"
---
# <a name="about-the-cisco-ise-pxgrid-integration"></a>Acerca de la integración de Cisco ISE pxGrid

Defender para IoT es la única plataforma para ICS e IoT ciberseguridad creada por expertos del equipo azul con experiencia en la defensa de una infraestructura nacional crítica y la única plataforma con análisis de amenazas patentados y compatibles con ICS y aprendizaje automático. Defender para IoT ofrece:

- Información inmediata sobre dispositivos ICS, vulnerabilidades y amenazas conocidas y de día cero.

- Conocimiento integrado profundo compatible con ICS de los protocolos, dispositivos y aplicaciones de OT, así como de sus comportamientos.

- Una tecnología de modelado automatizado de amenazas de ICS para predecir las rutas más probables de los ataques de ICS dirigidos mediante un análisis propietario.

## <a name="powerful-device-visibility-and-control"></a>Control y visibilidad eficaces de los dispositivos

La integración de Defender para IoT con Cisco ISE pxGrid proporciona un nuevo nivel de visibilidad, supervisión y control centralizados para el panorama de OT.

Estas plataformas puente permiten la visibilidad y la protección automatizadas en dispositivos ICS e IIoT a los que anteriormente no se podía acceder.

### <a name="ics-and-iiot-device-visibility-comprehensive-and-deep"></a>Visibilidad de dispositivos ICS e IIoT: completa y profunda

La tecnología patentada de Defender para IoT garantiza una administración de inventario y una detección de dispositivos IIoT e ICS completa y profunda.

Los tipos de dispositivos, así como sus fabricantes, puertos abiertos, números de serie, revisiones de firmware, direcciones IP y datos de direcciones MAC, entre otra información, se actualizan en tiempo real. Defender para IoT puede mejorar aún más la visibilidad, detección y análisis de esta línea base gracias a su integración con orígenes de datos empresariales críticos. Algunos ejemplos son las CMDB, DNS, los firewalls y las API web.

Además, la plataforma de Defender para IoT combina la supervisión pasiva con técnicas opcionales de sondeo selectivo para proporcionar el inventario de dispositivos más preciso y detallado de las organizaciones de infraestructura críticas y del sector.

### <a name="bridged-capabilities"></a>Funcionalidades con puente

La unión de estas funcionalidades y Cisco ISE pxGrid proporciona a los equipos de seguridad una visibilidad de dispositivos sin precedentes en el ecosistema de la empresa.

Su sólida y perfecta integración perfecta con Cisco ISE pxGrid garantiza que ningún dispositivo OT se queda sin detectar y que no se pierde información de los dispositivos.

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoint-categories.png" alt-text="Ejemplo de OUI de categorías de puntos de conexión.":::

:::image type="content" source="media/integration-cisco-isepxgrid-integration/endpoints.png" alt-text="Puntos de conexión de ejemplo en el sistema.":::  

:::image type="content" source="media/integration-cisco-isepxgrid-integration/attributes.png" alt-text="Captura de pantalla de los atributos ubicados en el sistema.":::  

### <a name="use-case-coverage-ise-policies-based-on-defender-for-iot-attributes"></a>Cobertura de casos de uso: Directivas de ISE basadas en los atributos de Defender para IoT

Use las eficaces directivas de ISE basadas en el aprendizaje profundo de Defender para IoT para controlar los requisitos de los casos de uso de ICS y IoT.

El uso de directivas le permite cerrar el ciclo de seguridad y hacer que la red sea compatible en tiempo real.

Por ejemplo, los clientes pueden usar los atributos de IoT e ICS de Defender para IoT para crear directivas que controlen los siguientes casos de uso, como:

- Cree una directiva de autorización que permita dispositivos conocidos y autorizados en una zona confidencial en función de atributos permitidos (por ejemplo, la versión de firmware específica de los PLC de Rockwell Automation).

- Envíe una notificación de los equipos de seguridad cuando se detecte un dispositivo ICS en una zona no OT.

- Corrija las máquinas con proveedores anticuados o no conformes.

- Ponga en cuarentena y bloquee los dispositivos cuando sea necesario.

- Genere informes sobre PLC o RTU que ejecuten firmware con vulnerabilidades conocidas (CVE).

### <a name="about-this-article"></a>Información acerca de este artículo

En este artículo se describe cómo configurar pxGrid y la plataforma de Defender para IoT para asegurarse de que Defender para IoT inserta atributos de OT en Cisco ISE.

### <a name="getting-more-information"></a>Más información

Para más información sobre los requisitos de integración de Cisco ISE pxGrid, consulte <https://www.cisco.com/c/en/us/products/security/pxgrid.html>

## <a name="integration-system-requirements"></a>Requisitos del sistema de integración

En este artículo se describen los requisitos del sistema de integración:

Requisitos de Defender para IoT

- La versión 2.5 de Defender para IoT

Requisitos de Cisco

- La versión 2.0 de pxGrid

- La versión 2.4 de Cisco ISE

Requisitos de red

- Compruebe que el dispositivo de Defender para IoT tiene acceso a la interfaz de administración de Cisco ISE.

- Compruebe que tiene acceso con la CLI a todos los dispositivos de Defender para IoT de su empresa.

> [!NOTE]
> Solo los dispositivos con direcciones MAC se sincronizan con Cisco ISE pxGrid.

## <a name="cisco-ise-pxgrid-setup"></a>Instalación de Cisco ISE pxGrid

En este artículo se describe cómo:

- Configurar la comunicación con pxGrid

- Suscribirse al tema del dispositivo de punto de conexión

- Generación de certificados

- Definir la configuración de pxGrid

## <a name="set-up-communication-with-pxgrid"></a>Configurar la comunicación con pxGrid

En este artículo se describe cómo configurar la comunicación con pxGrid.

Para configurar la comunicación:

1. Inicie sesión en Cisco ISE.

1. Seleccione **Administration**>**System** y **Deployment** (Administración > Sistema y Desarrollo).

1. Seleccione el nodo necesario. En la pestaña General Settings (Configuración general), seleccione la **casilla pxGrid**.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/pxgrid.png" alt-text="Asegúrese de que la casilla pxGrid está seleccionada.":::

1. Seleccione la pestaña **Profiling Configuration** (Configuración de generación de perfiles).

1. Seleccione la **casilla pxGrid**. agregue una descripción.

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/profile-configuration.png" alt-text="Captura de pantalla en la que se agrega una descripción":::

## <a name="subscribe-to-the-endpoint-device-topic"></a>Suscripción al tema del dispositivo de punto de conexión

Compruebe que el nodo ISE pxGrid se ha suscrito al tema del dispositivo de punto de conexión. Vaya a **Administration**>**pxGrid Services**>**Web Clients** (Administración > Servicios pxGrid > Clientes web). Ahí, puede comprobar que ISE se ha suscrito al tema del dispositivo de punto de conexión.

## <a name="generate-certificates"></a>Generación de certificados

En este artículo se describe cómo generar certificados.

Para generar:

1. Seleccione **Administration** > **pxGrid Services** (Administración > Servicios pxGrid) y, después, **Certificates** (Certificados).

    :::image type="content" source="media/integration-cisco-isepxgrid-integration/certificates.png" alt-text="Seleccione la pestaña de certificados para generar un certificado.":::

1. En el campo **I Want To** (Quiero), seleccione **Generate a single certificate (without a certificate signing request)** [Generar un certificado individual (sin una solicitud de firma del certificado)].

1. Rellene los restantes campos siguientes y seleccione **Create** (Crear).

1. Cree la clave del certificado de cliente y el certificado de servidor y, después, conviértalos al formato del almacén de claves de Java. Tendrá que copiarlos en todos los sensores de Defender para IoT de la red.

## <a name="define-pxgrid-settings"></a>Definir la configuración de pxGrid

Para definir la configuración:

1. Seleccione **Administration** > **pxGrid Services** (Administración > Servicios pxGrid) y, después, **Settings** (Configuración).

1. Habilite **Automatically approve new certificate-based accounts** (Aprobar automáticamente nuevas cuentas basadas en certificado) y **Allow password-based account creation** (Permitir la creación de cuentas con contraseña).

  :::image type="content" source="media/integration-cisco-isepxgrid-integration/allow-these.png" alt-text="Asegúrese de que ambas casillas están seleccionadas.":::

## <a name="set-up-the-defender-for-iot-appliances"></a>Configuración de los dispositivos de Defender para IoT

En este artículo se describe cómo configurar los dispositivos de Defender para IoT para que se comuniquen con pxGrid. La configuración debe realizarse en todos los dispositivos de Defender para IoT que vayan a insertar datos en Cisco ISE.

Para configurar los dispositivos:

1. Inicie sesión en la CLI del sensor.

1. Copie los archivos que se crearon previamente en el sensor y péguelos en: `/var/cyberx/properties/`.

1. Edite `/var/cyberx/properties/pxgrid.properties`:

    1. Agregue una clave y confianza y, después, almacene los nombres de archivo y las contraseñas.

    2. Agregue el nombre de host de la instancia de pxGrid.

    3. `Enabled=true`

1. Reinicie el dispositivo.

1. Repita estos pasos en todos los dispositivos de la empresa que vayan a insertar datos.

## <a name="view-and-manage-detections-in-cisco-ise"></a>Visualización y administración de detecciones en Cisco ISE

1. Las detecciones de puntos de conexión se muestran en la pestaña Context **Visibility** > **Endpoints** (Visibilidad de contexto > Puntos de conexión) de ISE.

1. Seleccione **Policy** > **Profiling** > **Add** > **Rules** >  **+ Condition** > **Create New Condition** (Directiva > Generación de perfiles > Agregar > Reglas > + Condición > Crear condición).

1. Seleccione **Attribute** (Atributo) y use los diccionarios de dispositivos IOT para crear una regla de generación de perfiles basada en los atributos insertados. Se insertan los siguientes atributos.

    - Tipo de dispositivo

    - Revisión del hardware

    - Dirección IP

    - Dirección MAC

    - Nombre

    - Product ID

    - Protocolo

    - Número de serie

    - Revisión del software

    - Vendor

Solo se sincronizan los dispositivos con direcciones MAC.

## <a name="troubleshooting-and-logs"></a>Solución de problemas y registros

Los registros se pueden encontrar en:

- `/var/cyberx/logs/pxgrid.log`

- `/var/cyberx/logs/core.log`

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [reenviar información de alertas](how-to-forward-alert-information-to-partners.md).
