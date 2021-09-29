---
title: Integración de Splunk con Azure Defender para IoT
description: En este tutorial, aprenderá a integrar Splunk con Azure Defender para IoT.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/12/2021
ms.custom: template-tutorial
ms.openlocfilehash: 2484de315508310729882e46b5e22669d01ec9f8
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124814703"
---
# <a name="tutorial-integrate-splunk-with-azure-defender-for-iot"></a>Tutorial: Integración de Splunk con Azure Defender para IoT

Este tutorial le ayudará a aprender a integrar Splunk con Azure Defender para IoT.

Defender para IoT reduce el riesgo para IIoT, ICS y SCADA con motores de autoaprendizaje patentados compatible con ICS. Estos motores proporcionan información inmediata sobre los dispositivos ICS, las vulnerabilidades y las amenazas en menos de una hora de imagen y sin depender de agentes, reglas o firmas, habilidades especializadas o conocimientos previos del entorno.

Con el fin de solucionar la falta de visibilidad sobre la seguridad y la resistencia de las redes de tecnología operativa (TO), Defender para IoT desarrolló la aplicación de supervisión de amenazas de Defender para IoT, IIoT e ICS para Splunk, una integración nativa entre Defender para IoT y Splunk que permite un enfoque unificado sobre la seguridad de TI y TO.

La aplicación proporciona a los analistas de los centros de operaciones de seguridad (SOC, por sus siglas en inglés) visibilidad multidimensional sobre los protocolos de TO especializados y los dispositivos IIoT implementados en entornos industriales. Esta capacidad, junto con el análisis del comportamiento compatible con ICS, permite detectar rápidamente la actividad sospechosa o anómala. La aplicación también permite responder a incidentes tanto de TI como de OT desde dentro de un centro de operaciones de seguridad corporativo. Esta es una evolución importante dada la convergencia continua de TI y TO para admitir nuevas iniciativas de IIoT, como máquinas inteligentes e inteligencia en tiempo real.

La aplicación Splunk se puede instalar de forma local o ejecutarse en una nube. La integración de Splunk con Defender para IoT admite ambas implementaciones.

> [!Note]
> Las referencias a CyberX se refieren a Azure Defender para IoT.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Descargar la aplicación Defender para IoT en Splunk
> * Envío de alertas de Defender para IoT a Splunk

Si no tiene una suscripción a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.

## <a name="prerequisites"></a>Requisitos previos

### <a name="version-requirements"></a>Requisitos de versión

Las siguientes versiones son necesarias para que se ejecute la aplicación.

- Defender para IoT, versión 2.4 y superior.

- Splunkbase, versión 11 y superior.

- Splunk Enterprise, versión 7.2 y superior.

### <a name="splunk-permission-requirements"></a>Requisitos de permisos de Splunk

Se necesita el permiso de Splunk siguiente:

- Cualquier usuario con un rol de usuario de nivel de *administrador*.

## <a name="download-the-defender-for-iot-application-in-splunk"></a>Descargar la aplicación Defender para IoT en Splunk

Para acceder a la aplicación Defender para IoT en Splunk, deberá descargar la aplicación en el almacén de aplicaciones de Splunkbase.

**Para acceder a la aplicación Defender para IoT en Splunk**:

1. Vaya al almacén de aplicaciones de [Splunkbase](https://splunkbase.splunk.com/).

1. Busque `CyberX ICS Threat Monitoring for Splunk`.

1. Seleccione la aplicación de supervisión de amenazas de CyberX para Splunk.

1. Seleccione el botón **INICIAR SESIÓN PARA DESCARGAR**.

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Envío de alertas de Defender para IoT a Splunk

Las alertas de Defender para IoT proporcionan información sobre una amplia variedad de eventos de seguridad. Estos eventos incluyen los siguientes:

- Desviaciones de la actividad de red de base de referencia aprendida.

- Detecciones de malware.

- Detecciones basadas en cambios de funcionamiento sospechosos.

- Anomalías de la red.

- Desviaciones de las especificaciones del protocolo.

    :::image type="content" source="media/tutorial-splunk/address-scan.png" alt-text="La pantalla de detecciones.":::

También puede configurar Defender para IoT para que envíe alertas al servidor de Splunk, donde se muestra la información de alertas en el panel de Splunk Enterprise.

:::image type="content" source="media/tutorial-splunk/alerts-and-details.png" alt-text="Visualización de todas las alertas y sus detalles." lightbox="media/tutorial-splunk/alerts-and-details-expanded.png":::

Para enviar información de alertas a los servidores de Splunk desde Defender para IoT, deberá crear una regla de reenvío.

**Para crearla**:

1. Inicie sesión en el sensor y seleccione **Reenvío** en el panel izquierdo.

    :::image type="content" source="media/tutorial-splunk/forwarding.png" alt-text="Selección del botón azul Create Forwarding Alert (Crear alerta de reenvío).":::

1. Seleccione **Create Forwarding Rules** (Crear reglas de reenvío).

1. En la ventana **Create Forwarding Rule** (Crear regla de reenvío), defina los parámetros de la regla.

    :::image type="content" source="media/tutorial-splunk/forwarding-rule.png" alt-text="Creación de las reglas para la regla de reenvío." lightbox="media/tutorial-splunk/forwarding-rule-expanded.png":::

    | Parámetro | Descripción |
    |--|--|
    | **Nombre** | Nombre de la regla de reenvío. |
    | **Select Severity** (Seleccionar gravedad) | El incidente de nivel de seguridad mínimo que se va a reenviar. Por ejemplo, si selecciona Minor (Leve), se reenviarán las alertas de gravedad leve y todas las alertas por encima de este nivel de gravedad. |
    | **Protocolos** | De forma predeterminada, se seleccionan todos los protocolos. Para seleccionar un protocolo concreto, elija **Específico** y seleccione el protocolo al que se aplica esta regla. |
    | **Engines** (Motores) | De forma predeterminada, intervienen todos los motores de seguridad. Para seleccionar un motor de seguridad concreto al que se aplica esta regla, seleccione **Específico** y elija el motor. |
    | **Notificaciones del sistema** | Estado en línea o sin conexión del sensor de reenvío. Esta opción solo está disponible si ha iniciado sesión en el administrador central. |

1. Seleccione **Acción** y, a continuación, **Send to Splunk Server** (Enviar al servidor de Splunk).

1. Escriba los siguientes parámetros de Splunk.

    :::image type="content" source="media/tutorial-splunk/parameters.png" alt-text="Los parámetros de Splunk que debe especificar en esta pantalla." lightbox="media/tutorial-splunk/parameters-expanded.png":::

    | Parámetro | Descripción |
    |--|--|
    | **Host** | Dirección del servidor de Splunk. |
    | **Puerto** | 8089 |
    | **Nombre de usuario** | Nombre de usuario del servidor de Splunk. |
    | **Contraseña** | Contraseña del servidor de Splunk. |

1. Seleccione **Submit** (Enviar).

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos para limpiar.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a empezar a trabajar con la integración de Splunk. Continúe para obtener información sobre cómo [integrar ServiceNow con Azure Defender para IoT](tutorial-servicenow.md).

> [!div class="nextstepaction"]
> [Integración de ServiceNow con Azure Defender para IoT](tutorial-servicenow.md)