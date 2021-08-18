---
title: Acerca de la integración con Forescout
description: La integración de Azure Defender para IoT con la plataforma Forescout proporciona un nuevo nivel de visibilidad, supervisión y control centralizados para el entorno de IoT y TO.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 07e5187970d193502b95b49c5517a8e3824767be
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113019009"
---
# <a name="about-the-forescout-integration"></a>Acerca de la integración con Forescout

Azure Defender para IoT ofrece una plataforma de ciberseguridad para ICS e IoT creada por expertos del equipo azul con una gran trayectoria en defensa de la infraestructura nacional crítica. Defender para IoT es la única plataforma con tecnología patentada de análisis de amenazas y aprendizaje automático compatible con ICS. Azure Defender para IoT ofrece:

- Información inmediata sobre el panorama de los dispositivos ICS con una amplia variedad de detalles sobre los atributos.
- Conocimiento integrado profundo compatible con ICS de los protocolos, dispositivos y aplicaciones TO y sus comportamientos.
- Conclusiones inmediatas sobre vulnerabilidades y amenazas conocidas y de día cero.
- Tecnología de modelado automatizado de amenazas de ICS para predecir las rutas más probables de los ataques de ICS dirigidos mediante análisis de propiedad.

La integración de Defender para IoT con la plataforma Forescout proporciona un nuevo nivel de visibilidad, supervisión y control centralizados del entorno de IoT y TO.

Estas plataformas puente permiten la visibilidad y la administración automatizadas de los dispositivos ICS y de los flujos de trabajo aislados a los que anteriormente era imposible acceder.

La integración proporciona a los analistas de SOC visibilidad multinivel sobre los protocolos de TO implementados en entornos industriales. Hay más información disponible sobre elementos como el firmware, los tipos de dispositivos, los sistemas operativos y las puntuaciones de análisis de riesgos basados en tecnologías propietarias de Azure Defender para IoT.

> [!Note]
> Las referencias a CyberX se refieren a Azure Defender para IoT.
## <a name="devices"></a>Dispositivos

### <a name="device-visibility-and-management"></a>Visibilidad y administración de dispositivos

El inventario del dispositivo se enriquece con atributos críticos detectados por la plataforma Defender para IoT. De esta forma se garantiza que:

- Se adquiere visibilidad completa y continua sobre el entorno de los dispositivos TO desde un único panel.
- Se obtiene inteligencia en tiempo real sobre los riesgos de TO.

:::image type="content" source="media/integration-forescout/forescout-device-inventory.png" alt-text="Inventario de dispositivos":::

:::image type="content" source="media/integration-forescout/forescout-device-details.png" alt-text="Detalles del dispositivo":::

### <a name="device-control"></a>Control de dispositivos

La integración con Forescout ayuda a reducir el tiempo necesario para que las organizaciones industriales y de infraestructura crítica detecten, investiguen y actúen sobre las ciberamenazas.

- Use la inteligencia de los dispositivos TO de Azure Defender para IoT para desencadenar acciones de directiva de Forescout y así cerrar el ciclo de seguridad. Por ejemplo, puede enviar automáticamente un correo electrónico de alerta a los administradores de SOC cuando se detecten determinados protocolos o cuando cambien los detalles del firmware.

- Correlacione la información de Defender para IoT con la de otros módulos de *Forescout eyeExtend* que vigilan la supervisión, la administración de incidentes y el control de dispositivos.

## <a name="system-requirements"></a>Requisitos del sistema

- Azure Defender para IoT, versión 2.4 o superior.
- Forescout, versión 8.0 o superior
- Una licencia para el módulo *Forescout eyeExtend* para la plataforma Azure Defender para IoT.

### <a name="getting-more-forescout-information"></a>Obtención de más información sobre Forescout

Para más información sobre la plataforma Forescout, consulte el [centro de recursos de Forescout](https://www.forescout.com/company/resources/#resource_filter_group).

## <a name="system-setup"></a>Configuración del sistema

En este artículo se describe cómo configurar la comunicación entre la plataforma Defender para IoT y la plataforma Forescout.

### <a name="set-up-the-defender-for-iot-platform"></a>Configuración de la plataforma Defender para IoT

Para garantizar la comunicación entre Defender para IoT y Forescout, genere un token de acceso en Defender para IoT.

Los tokens de acceso permiten que sistemas externos accedan a los datos que detecta Defender para IoT y realicen acciones con esos datos mediante la API REST externa a través de conexiones SSL. Puede generar tokens de acceso a la API REST de Azure Defender para IoT.

Para generar un token, haga lo siguiente:

1. Inicie sesión en el sensor de Defender para IoT que se consultará mediante Forescout.

1. Seleccione **Configuración del sistema** y, luego, elija **Tokens de acceso** en la sección **General**. Se abre el cuadro de diálogo **Tokens de acceso**.
   :::image type="content" source="media/integration-forescout/generate-access-tokens-screen.png" alt-text="Tokens de acceso":::
1. Seleccione **Generar nuevo token** en el cuadro de diálogo **Tokens de acceso**.
1. Escriba una descripción del token en el cuadro de diálogo **New access token** (Nuevo token de acceso).
   :::image type="content" source="media/integration-forescout/new-forescout-token.png" alt-text="Nuevo token de acceso":::
1. Seleccione **Next** (Siguiente). El token se muestra en el cuadro de diálogo. :::image type="content" source="media/integration-forescout/forescout-access-token-display-screen.png" alt-text="Visualización del token":::
   > [!NOTE]
   > *Guarde el token en un lugar seguro. Lo necesitará al configurar la plataforma Forescout*.
1. Seleccione **Finalizar**.

   :::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Adición del token finalizada":::

### <a name="set-up-the-forescout-platform"></a>Configuración de la plataforma Forescout

Puede configurar la plataforma Forescout para que se comunique con un sensor de Defender para IoT.

Para realizar la configuración:

1. Instale *el módulo Forescout eyeExtend para CyberX* en la plataforma Forescout.

1. Inicie sesión en la consola de CounterACT y seleccione **Options** (Opciones) en el menú **Tools** (Herramientas). Se abre el cuadro de diálogo **Options** (Opciones).

1. Vaya a la carpeta **Modules** (Módulos) y selecciónela.

1. En el panel **Modules** (Módulos), seleccione **CyberX Platform** (Plataforma de CyberX). Se abre el panel de la plataforma Defender para IoT.

   :::image type="content" source="media/integration-forescout/settings-for-module.png" alt-text="Configuración del módulo de Azure Defender para IoT":::

   Escriba la siguiente información:

   - **Dirección del servidor**: escriba la dirección IP del sensor de Defender para IoT que se consultará mediante el dispositivo de Forescout.
   - **Token de acceso**: escriba el token de acceso generado para el sensor de Defender para IoT que se conectará al dispositivo de Forescout. Para generar un token, consulte [Configuración de la plataforma Defender para IoT](#set-up-the-defender-for-iot-platform).

1. Seleccione **Aplicar**.

Si quiere que la plataforma Forescout se comunique con otro sensor:

1. Cree un token de acceso en el sensor pertinente de Defender para IoT.

1. En el cuadro de diálogo **Forescout Modules** > **CyberX Platform** (Módulos de Forescout > Plataforma de CyberX):

   - Elimine la información que se muestra.
   
   - Escriba la dirección IP del nuevo sensor y la información del nuevo token de acceso.

### <a name="verify-communication"></a>Comprobación de la comunicación

Después de configurar Defender para IoT y Forescout, abra el cuadro de diálogo Tokens de acceso del sensor en Defender para IoT.

Si se muestra el valor **N/A** en el campo **Usado** de este token, significa que la conexión entre el sensor y el dispositivo de Forescout no funciona.

**Usado** indica la última vez que se recibió una llamada externa con este token.

:::image type="content" source="media/integration-forescout/forescout-access-token-added-successfully.png" alt-text="Comprobación de que el token se ha recibido":::

## <a name="view-defender-for-iot-detections-in-forescout"></a>Visualización de las detecciones de Defender para IoT en Forescout

Para ver los atributos de un dispositivo:

1. Inicie sesión en la plataforma Forescout y, luego, vaya a **Asset Inventory** (Inventario de activos).

1. Vaya a **CyberX Platform** (Plataforma de CyberX). Se muestran los siguientes atributos para los dispositivos TO detectados mediante Defender para IoT.

   | Elemento | Descripción |
   |--|--|
   | Autorizado por Azure Defender para IoT | Un dispositivo detectado en la red por Defender para IoT durante el período de aprendizaje de la red. |
   | Firmware | Detalles del firmware del dispositivo. Por ejemplo, el modelo y la versión. |
   | Nombre | Nombre del dispositivo. |
   | Sistema operativo | El sistema operativo del dispositivo. |
   | Tipo | El tipo de dispositivo. Por ejemplo, un PLC, un sistema Historian o una estación de ingeniería. |
   | Vendor | El proveedor del dispositivo. Por ejemplo, Rockwell Automation. |
   | Nivel de riesgo | El nivel de riesgo calculado por Defender para IoT. |
   | Protocolos | Los protocolos detectados en el tráfico generado por el dispositivo. |

:::image type="content" source="media/integration-forescout/device-firmware-attributes-in-forescout.png" alt-text="Visualización de los atributos de firmware.":::

:::image type="content" source="media/integration-forescout/vendor-attributes-in-forescout.png" alt-text="Visualización de los atributos de los proveedores.":::

### <a name="viewing-more-details"></a>Visualización de más detalles

Vea información adicional de los dispositivos detectados por Defender para IoT. Por ejemplo, información de cumplimiento y de directivas de Forescout.

Para ello, haga clic con el botón derecho en un dispositivo de la sección **Device Inventory Hosts** (Hosts de inventario de dispositivos). Se abre el cuadro de diálogo de detalles del host con información adicional.

:::image type="content" source="media/integration-forescout/details-dialog-box-in-forescout.png" alt-text="Detalles del host":::

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Creación de directivas de Azure Defender para IoT en Forescout

Las directivas de Forescout se pueden usar para automatizar el control y la administración de los dispositivos detectados por Defender para IoT. Por ejemplo,

- enviar automáticamente un correo electrónico a los administradores de SOC cuando se detecten determinadas versiones de firmware;

- agregar dispositivos específicos detectados por Defender para IoT a un grupo de Forescout para administrar mejor los flujos de trabajo de incidentes y seguridad, por ejemplo, con otras integraciones de SIEM.

Cree una directiva personalizada de Forescout con Defender para IoT mediante propiedades de condición.

Para acceder a las propiedades de Defender para IoT:

1. Vaya al **árbol de propiedades** desde el cuadro de diálogo **Condiciones de la directiva**.

1. Expanda la carpeta **CyberX Platform** en el **árbol de propiedades**. Están disponibles las siguientes propiedades de Defender para IoT.

:::image type="content" source="media/integration-forescout/forescout-property-tree.png" alt-text="Propiedades":::

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [reenviar información de alertas](how-to-forward-alert-information-to-partners.md).
