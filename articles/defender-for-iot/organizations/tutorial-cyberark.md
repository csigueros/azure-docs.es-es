---
title: Integración de CyberArk con Azure Defender para IoT
description: En este tutorial, aprenderá a integrar Azure Defender para IoT con CyberArk.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/30/2021
ms.custom: template-tutorial
ms.openlocfilehash: 3dc47696a820708f3118ff752cfabfce365a5153
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2021
ms.locfileid: "129369785"
---
# <a name="tutorial-integrate-cyberark-with-azure-defender-for-iot"></a>Tutorial: Integración de CyberArk con Azure Defender para IoT

Este tutorial le ayudará a aprender a integrar CyberArk con Azure Defender para IoT.

Defender para IoT ofrece la única plataforma de ICS y ciberseguridad de IoT con tecnología patentada de análisis de amenazas y aprendizaje automático compatible con ICS.

Los actores de amenazas usan credenciales de acceso remoto en peligro para acceder a redes de infraestructura críticas a través de conexiones VPN y escritorio remoto. Mediante el uso de conexiones de confianza, este enfoque omite fácilmente cualquier seguridad perimetral de OT. Las credenciales suelen robarse a usuarios con privilegios, como ingenieros de control y personal de mantenimiento de asociados, que requieren acceso remoto para realizar tareas diarias.

La integración de Defender para IoT junto con CyberARK le permite:

- Reducir los riesgos de OT por el acceso remoto no autorizado

- Proporcionar supervisión continua y seguridad de acceso con privilegios para OT

- Mejorar la respuesta a incidentes, la búsqueda de amenazas y el modelado de amenazas

El dispositivo de Defender para IoT se conecta a la red de OT través de un puerto SPAN (puerto de reflejo) en dispositivos de red como conmutadores y enrutadores a través de una conexión de un solo sentido (entrante) a las interfaces de red dedicadas en el dispositivo de Defender para IoT.

También se proporciona una interfaz de red dedicada en el dispositivo de Defender para IoT para la administración centralizada y el acceso a la API. Esta interfaz también se usa para comunicarse con la solución de CyberArk PSM que se implementa en el centro de datos de la organización para administrar usuarios con privilegios y proteger las conexiones de acceso remoto.

:::image type="content" source="media/tutorial-cyberark/architecture.png" alt-text="Implementación de la solución de CyberArk PSM":::

En este tutorial, aprenderá a:

> [!div class="checklist"]
> - Configurar PSM en CyberArk
> - Habilitar la integración en Defender para IoT
> - Ver y administrar detecciones
> - Detener la integración

## <a name="prerequisites"></a>Requisitos previos

- CyberARK versión 2.0.

- Compruebe que tiene acceso con la CLI a todos los dispositivos de Defender para IoT de su empresa.

- Una cuenta de Azure. Si aún no tiene una cuenta de Azure, puede [crear una cuenta gratuita de Azure ahora](https://azure.microsoft.com/free/).

## <a name="configure-psm-cyberark"></a>Configuración de PSM en CyberArk

CyberArk debe configurarse para permitir la comunicación con Defender para IoT. Esta comunicación se logra mediante la configuración de PSM.

**Para configurar PSM**:

1. Busque y abra el archivo `c:\Program Files\PrivateArk\Server\dbparam.xml`.

1. Agregue los siguientes parámetros:

    `[SYSLOG]` <br>
    `UseLegacySyslogFormat=Yes` <br>
    `SyslogTranslatorFile=Syslog\CyberX.xsl` <br>
    `SyslogServerIP=<CyberX Server IP>` <br>
    `SyslogServerProtocol=UDP` <br>
    `SyslogMessageCodeFilter=319,320,295,378,380` <br>

1. Guarde el archivo y ciérrelo.

1. Coloque el archivo de configuración de Syslog de Defender para IoT `CyberX.xsl` en `c:\Program Files\PrivateArk\Server\Syslog\CyberX.xsl`.

1. Abra la **Administración central del servidor de informes**.

1. Seleccione **Detener semáforo** para detener el servidor.

    :::image type="content" source="media/tutorial-cyberark/server.png" alt-text="Captura de pantalla del semáforo de detención de administración central del servidor.":::

1. Seleccione **Iniciar semáforo** para iniciar el servidor.

## <a name="enable-the-integration-in-defender-for-iot"></a>Habilitación de la integración en Defender para IoT

Para habilitar la integración, el servidor de Syslog debe estar habilitado en la consola de administración de Defender para IoT. De manera predeterminada, el servidor de Syslog escucha la dirección IP del sistema mediante el puerto 514 UDP.

**Para configurar Defender para IoT**:

1. En la consola de administración de Defender para IoT, vaya a **Configuración del sistema**.

1. Alterne el servidor de Syslog a **Activado**.

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="Captura de pantalla del servidor de Syslog activado.":::

1. (Opcional) Para cambiar el puerto, inicie sesión en el sistema a través de la CLI, vaya a `/var/cyberx/properties/syslog.properties` y cambie `listener: 514/udp`.

## <a name="view-and-manage-detections"></a>Visualización y administración de detecciones

La integración entre Azure Defender para IoT y PSM de CyberArk se realiza a través de mensajes de Syslog. La solución de PSM envía estos mensajes a Defender para IoT y le notifica las sesiones remotas o errores de comprobación.

Una vez que la plataforma de Defender para IoT recibe estos mensajes de PSM, los correlaciona con los datos que ve en la red, validando así que las conexiones de acceso remoto a la red las generó la solución de PSM y no un usuario no autorizado.

### <a name="view-alerts"></a>Visualización de alertas

Cada vez que la plataforma de Defender para IoT identifique sesiones remotas que no han sido autorizadas por PSM, emitirá un `Unauthorized Remote Session`. Para facilitar la investigación inmediata, la alerta también muestra las direcciones IP y los nombres de los dispositivos de origen y destino.

**Para ver las alertas**:

1. Inicie sesión en la consola de administración.

1. En el panel de la izquierda, seleccione **Alertas**.

1. En la lista de alertas, seleccione la alerta titulada **Sesión remota no autorizada**.

    :::image type="content" source="media/tutorial-cyberark/unauthorized.png" alt-text="Alerta Sesión remota no autorizada.":::

### <a name="event-timeline"></a>Escala de tiempo de eventos

Cada vez que PSM autoriza una conexión remota, es visible en la página Escala de tiempo de eventos de Defender para IoT. La página Escala de tiempo del evento muestra una escala de tiempo de todas las alertas y notificaciones.

**Para ver la escala de tiempo de eventos**:

1. Inicie sesión en el sensor de Defender para IoT.

1. En el panel izquierdo, seleccione **Escala de tiempo de eventos**.

1. Busque cualquier evento titulado Sesión remota de PSM.

    :::image type="content" source="media/tutorial-cyberark/event.png" alt-text="Vista de la pantalla Registro de eventos.":::

### <a name="auditing--forensics"></a>Auditoría y análisis forense

Los administradores pueden auditar e investigar las sesiones de acceso remoto consultando la plataforma de Defender para IoT a través de su interfaz de minería de datos integrada. Esta información se puede usar para identificar todas las conexiones de acceso remoto que se han producido, incluidos detalles forenses como desde o hacia dispositivos, protocolos (RDP o SSH), usuarios de origen y destino, marcas de tiempo y si las sesiones se autorizaron mediante PSM.

**Para auditar e investigar**:

1. Inicie sesión en el sensor de Defender para IoT.

1. Seleccione **Minería de datos** en el panel de la izquierda.

1. Seleccione **Acceso remoto**.

    :::image type="content" source="media/tutorial-cyberark/data-mining.png" alt-text="Vista de la interfaz de minería de datos.":::

## <a name="stop-the-integration"></a>Detención de la integración

En cualquier momento, puede impedir que la integración se comunique.

**Para detener la integración**:

1. En la consola de administración de Defender para IoT, vaya a la pantalla **Configuración del sistema**.

1. Alterne el servidor de Syslog a **Desactivado**.

    :::image type="content" source="media/tutorial-cyberark/toggle.png" alt-text="Vista del estado del servidor.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos para limpiar.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a empezar a trabajar con la integración de CyberArk. Continúe para obtener información sobre la integración de Forescout.

> [!div class="nextstepaction"]
> [Botón de pasos siguientes](./tutorial-forescout.md)
