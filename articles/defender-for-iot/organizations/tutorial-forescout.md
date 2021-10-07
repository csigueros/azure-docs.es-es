---
title: Integración de Forescout con Azure Defender para IoT
description: En este tutorial, aprenderá a integrar Azure Defender para IoT con Forescout.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: 6a930671e501940ce7f6d0d22036225e7539eb7f
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128702197"
---
# <a name="tutorial-integrate-forescout-with-azure-defender-for-iot"></a>Tutorial: Integración de Forescout con Azure Defender para IoT

> [!Note]
> Las referencias a CyberX se refieren a Azure Defender para IoT.

Este tutorial le ayudará a aprender a integrar Forescout con Azure Defender para IoT.

Azure Defender para IoT ofrece un ICS y una plataforma de ciberseguridad IoT. Defender para IoT es la única plataforma con tecnología de análisis de amenazas y aprendizaje automático compatible con ICS. Defender para IoT ofrece:

- Información inmediata sobre el panorama de los dispositivos ICS con una amplia variedad de detalles sobre los atributos.

- Conocimiento integrado profundo compatible con ICS de los protocolos, dispositivos y aplicaciones de OT, así como de sus comportamientos.

- Conclusiones inmediatas sobre vulnerabilidades y amenazas conocidas y de día cero.

- Una tecnología de modelado automatizado de amenazas de ICS para predecir las rutas más probables de los ataques de ICS dirigidos mediante un análisis propietario.

La integración con Forescout ayuda a reducir el tiempo necesario para que las organizaciones industriales y de infraestructura crítica detecten, investiguen y actúen sobre las ciberamenazas.

- Use la inteligencia de los dispositivos TO de Azure Defender para IoT para desencadenar acciones de directiva de Forescout y así cerrar el ciclo de seguridad. Por ejemplo, puede enviar automáticamente un correo electrónico de alerta a los administradores de SOC cuando se detecten determinados protocolos o cuando cambien los detalles del firmware.

- Correlacione la información de Defender para IoT con la de otros módulos de *Forescout eyeExtend* que vigilan la supervisión, la administración de incidentes y el control de dispositivos.

La integración de Defender para IoT con la plataforma Forescout proporciona visibilidad, supervisión y control centralizados del entorno de IoT y TO. Estas plataformas puente permiten visibilidad automatizada, y administración de dispositivos ICS y de flujos de trabajo aislados. La integración proporciona a los analistas de SOC visibilidad multinivel sobre los protocolos de TO implementados en entornos industriales. Hay más información disponible sobre elementos como el firmware, los tipos de dispositivos, los sistemas operativos y las puntuaciones de análisis de riesgos basados en tecnologías propietarias de Azure Defender para IoT.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> - Generar token de acceso
> - Configuración de la plataforma Forescout
> - Comprobación de la comunicación
> - Visualización de atributos de dispositivo en Forescout
> - Creación de directivas de Azure Defender para IoT en Forescout

Si aún no tiene una cuenta de Azure, puede [crear una cuenta gratuita de Azure ahora](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Requisitos previos

- Azure Defender para IoT, versión 2.4 o superior.

- Forescout, versión 8.0 o superior

- Una licencia para el módulo Forescout eyeExtend para la plataforma Azure Defender para IoT.

## <a name="generate-an-access-token"></a>Generar token de acceso

Los tokens de acceso permiten que los sistemas externos accedan a los datos descubiertos por Defender para IoT. Los tokens de acceso permiten que los datos se utilicen para las API de REST externas y a través de conexiones SSL. Puede generar tokens de acceso a la API REST de Azure Defender para IoT.

Para garantizar la comunicación entre Defender para IoT y Forescout, debe generar un token de acceso en Defender para IoT.

**Para generar un token de acceso**:

1. Inicie sesión en el sensor de Defender para IoT que se consultará mediante Forescout.

1. Seleccione **Configuración del sistema** > **Tokens de acceso** en la sección **General**.

1. Seleccione **Generar nuevo token**.

    :::image type="content" source="media/tutorial-forescout/generate-access-tokens-screen.png" alt-text="Captura de pantalla de la pantalla de generación de tokens de acceso.":::

1. Escriba una descripción del token en el cuadro de diálogo **New access token** (Nuevo token de acceso).

   :::image type="content" source="media/tutorial-forescout/new-forescout-token.png" alt-text="Nuevo token de acceso":::

1. Seleccione **Next** (Siguiente). A continuación, el token se muestra en el cuadro de diálogo.

   > [!NOTE]
   > Guarde el token en un lugar seguro. Lo necesitará al configurar la plataforma Forescout.

1. Seleccione **Finalizar**.

   :::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="Adición del token finalizada":::

## <a name="configure-the-forescout-platform"></a>Configuración de la plataforma Forescout

Ahora, puede configurar la plataforma Forescout para que se comunique con un sensor de Defender para IoT.

**Para configurar la plataforma Forescout**:

1. En la plataforma Forescout, instale **el módulo Forescout eyeExtend para CyberX**.

1. Inicie sesión en la consola CounterACT.

1. En el menú Herramientas , seleccione **Opciones**.

1. Vaya a **Módulos** >  **Plataforma de CyberX**.

   :::image type="content" source="media/tutorial-forescout/settings-for-module.png" alt-text="Configuración del módulo de Azure Defender para IoT":::

1. En el campo de Dirección del servidor, escriba la dirección IP del sensor de Defender para IoT que se consultará mediante el dispositivo de Forescout.

1. En el campo Token de acceso, escriba el token de acceso que se generó anteriormente.

1. Seleccione **Aplicar**.

### <a name="change-sensors-in-forescout"></a>Cambio de sensores en Forescout

Para que la plataforma Forescout se comunique con un sensor diferente, se debe cambiar la configuración de Forescout.

**Para cambiar sensores en Forescout**:

1. Cree un token de acceso en el sensor pertinente de Defender para IoT.

1. Vaya a **Módulos Forescout** > **Plataforma de CyberX**.

1. Elimine la información que se muestra en ambos campos.

1. Inicie sesión en el nuevo sensor de Defender para IoT y [genere un nuevo token de acceso](#generate-an-access-token).

1. En el campo de Dirección del servidor, escriba la nueva dirección IP del sensor de Defender para IoT que se consultará mediante el dispositivo de Forescout.

1. En el campo Token de acceso, escriba el nuevo token de acceso.

1. Seleccione **Aplicar**.

## <a name="verify-communication"></a>Comprobación de la comunicación

Una vez configurada la conexión, deberá confirmar que las dos plataformas se están comunicando.

**Para confirmar que las dos plataformas se comunican**:

1. Inicie sesión en el sensor de Defender para IoT.

1. Vaya a **Configuración del sistema** > **Tokens de acceso**.

El campo Usado le avisará si la conexión entre el sensor y el dispositivo Forescout no funciona. Si se muestra **N/D**, la conexión no funciona. Si se muestra **Usado**, indicará la última vez que se recibió una llamada externa con este token.

:::image type="content" source="media/tutorial-forescout/forescout-access-token-added-successfully.png" alt-text="Comprobación de que el token se ha recibido":::

## <a name="view-device-attributes-in-forescout"></a>Visualización de atributos de dispositivo en Forescout

Mediante la integración de Defender para IoT con Forescout, podrá ver los distintos atributos del dispositivo detectados por Defender para IoT en la aplicación Forescout.

En la tabla siguiente se enumeran todos los atributos que están visibles a través de la aplicación Forescout:

| Elemento | Descripción |
|--|--|
| **Autorizado por Azure Defender para IoT** | Un dispositivo detectado en la red por Defender para IoT durante el período de aprendizaje de la red. |
| **Firmware** | Detalles del firmware del dispositivo. Por ejemplo, el modelo y la versión. |
| **Nombre** | Nombre del dispositivo. |
| **Sistema operativo** | El sistema operativo del dispositivo. |
| **Tipo** | El tipo de dispositivo. Por ejemplo, un PLC, un sistema Historian o una estación de ingeniería. |
| **Proveedor** | El proveedor del dispositivo. Por ejemplo, Rockwell Automation. |
| **Nivel de riesgo** | El nivel de riesgo calculado por Defender para IoT. |
| **Protocolos** | Los protocolos detectados en el tráfico generado por el dispositivo. |

**Para ver los atributos de un dispositivo**:

1. Inicie sesión en la plataforma Forescout y, luego, vaya a **Asset Inventory** (Inventario de activos).

   :::image type="content" source="media/tutorial-forescout/device-firmware-attributes-in-forescout.png" alt-text="Visualización de los atributos de firmware.":::

1. Seleccione **Plataforma de CyberX**.

   :::image type="content" source="media/tutorial-forescout/vendor-attributes-in-forescout.png" alt-text="Visualización de los atributos de los proveedores.":::

### <a name="view-more-details"></a>Ver más detalles

Después de ver los atributos de un dispositivo, puede ver más detalles de cada dispositivo, como la información de cumplimiento y directiva de Forescout.

**Para visualizar detalles adicionales**:

1. Inicie sesión en la plataforma Forescout y, luego, vaya a **Asset Inventory** (Inventario de activos).

1. Seleccione **Plataforma de CyberX**.

1. En la sección Hosts de inventario de dispositivos, haga clic con el botón derecho en un dispositivo. Se abre el cuadro de diálogo de detalles del host con información adicional.

## <a name="create-azure-defender-for-iot-policies-in-forescout"></a>Creación de directivas de Azure Defender para IoT en Forescout

Las directivas de Forescout se pueden usar para automatizar el control y la administración de los dispositivos detectados por Defender para IoT. Por ejemplo,

- enviar automáticamente un correo electrónico a los administradores de SOC cuando se detecten determinadas versiones de firmware;

- agregar dispositivos específicos detectados por Defender para IoT a un grupo de Forescout para administrar mejor los flujos de trabajo de incidentes y seguridad, por ejemplo, con otras integraciones de SIEM.

Puede crear directivas personalizadas en Forescout mediante las propiedades condicionales de Defender para IoT.

**Para acceder a las propiedades de Defender para IoT**:

1. Vaya a **Condiciones de directiva** > **Árbol de propiedades**.

1. En el árbol de propiedades, expanda la carpeta Plataforma de CyberX. Están disponibles las siguientes propiedades de Defender para IoT.

:::image type="content" source="media/tutorial-forescout/forescout-property-tree.png" alt-text="Propiedades":::

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos para limpiar.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a empezar a trabajar con la integración de Forescout. Continúe para obtener información sobre la [integración de Palo Alto](./tutorial-palo-alto.md).

> [!div class="nextstepaction"]
> [Botón de pasos siguientes](./tutorial-palo-alto.md)