---
title: Acerca de la integración con Splunk
description: Con el fin de solucionar la falta de visibilidad sobre la seguridad y la resistencia de las redes de tecnología operativa (TO), Defender para IoT desarrolló la aplicación de supervisión de amenazas de Defender para IoT, IIoT e ICS para Splunk, una integración nativa entre Defender para IoT y Splunk que permite un enfoque unificado sobre la seguridad de TI y TO.
ms.date: 1/4/2021
ms.topic: article
ms.openlocfilehash: 28bdc6deaac09d795c45460bb211126a105b80c8
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018890"
---
# <a name="defender-for-iot-and-ics-threat-monitoring-application-for-splunk"></a>Aplicación de supervisión de amenazas de Defender para IoT e ICS para Splunk

Defender para IoT reduce el riesgo para IIoT, ICS y SCADA con motores de autoaprendizaje patentados compatible con ICS. Estos motores proporcionan información inmediata sobre los dispositivos ICS, las vulnerabilidades y las amenazas en menos de una hora de imagen y sin depender de agentes, reglas o firmas, habilidades especializadas o conocimientos previos del entorno.

Con el fin de solucionar la falta de visibilidad sobre la seguridad y la resistencia de las redes de tecnología operativa (TO), Defender para IoT desarrolló la aplicación de supervisión de amenazas de Defender para IoT, IIoT e ICS para Splunk, una integración nativa entre Defender para IoT y Splunk que permite un enfoque unificado sobre la seguridad de TI y TO.

> [!Note]
> Las referencias a CyberX se refieren a Azure Defender para IoT.

## <a name="about-the-splunk-application"></a>Acerca de la aplicación Splunk

La aplicación proporciona a los analistas de los centros de operaciones de seguridad (SOC, por sus siglas en inglés) visibilidad multidimensional sobre los protocolos de TO especializados y los dispositivos IIoT implementados en entornos industriales. Esta capacidad, junto con el análisis del comportamiento compatible con ICS, permite detectar rápidamente la actividad sospechosa o anómala. La aplicación también permite la respuesta a incidentes de TI y TO desde dentro de un SOC corporativo. Esta es una evolución importante dada la convergencia continua de TI y TO para admitir nuevas iniciativas de IIoT, como máquinas inteligentes e inteligencia en tiempo real.

La aplicación Splunk se puede instalar de forma local o ejecutarse en una nube. La integración con Defender para IoT admite ambas implementaciones.

## <a name="about-the-integration"></a>Acerca de la integración

La integración de Defender para IoT y Splunk a través de la aplicación nativa permite a los usuarios:

- Reducir el tiempo necesario para que las organizaciones industriales y de infraestructura crítica detecten, investiguen y actúen sobre las ciberamenazas.

- Obtener inteligencia en tiempo real sobre los riesgos de TO.

- Correlacionar las alertas de Defender para IoT con los repositorios de inteligencia sobre amenazas de seguridad de Splunk Enterprise.

- Supervisar y responder desde un solo panel.

[:::image type="content" source="media/integration-splunk/splunk-mainpage-v2.png" alt-text="Página principal de la herramienta Splunk.":::](media/integration-splunk/splunk-mainpage-v2.png#lightbox)

:::image type="content" source="media/integration-splunk/alerts.png" alt-text="La página de alertas de Splunk.":::

La aplicación permite a los administradores de Splunk analizar las alertas de TO que envía Defender para IoT y supervisar toda la implementación de seguridad de TO, incluidos detalles como:

- Cuál de los cinco motores de análisis detectó la alerta.

- Qué protocolo generó la alerta.

- Qué sensor de Defender para IoT generó la alerta.

- Nivel de gravedad de la alerta.

- Origen y destino de la comunicación.

## <a name="requirements"></a>Requisitos

### <a name="version-requirements"></a>Requisitos de versión

Las siguientes versiones son un requisito:

- Defender para IoT, versión 2.4 y superior.

- Splunkbase, versión 11 y superior.

- Splunk Enterprise, versión 7.2 y superior.
  
## <a name="download-the-application"></a>Descarga de la aplicación

Descargue la *aplicación de supervisión de amenazas de CyberX para Splunk* desde [Splunkbase](https://splunkbase.splunk.com/app/4313/).

## <a name="splunk-permission-requirements"></a>Requisitos de permisos de Splunk

Se necesita el permiso de Splunk siguiente:

- Cualquier usuario con permisos de rol de usuario *administrador*.

## <a name="send-defender-for-iot-alerts-to-splunk"></a>Envío de alertas de Defender para IoT a Splunk

Las alertas de Defender para IoT proporcionan información sobre una amplia variedad de eventos de seguridad, como por ejemplo:

- Desviaciones de la actividad de red de base de referencia aprendida.

- Detecciones de malware.

- Detecciones basadas en cambios de funcionamiento sospechosos.

- Anomalías de la red.

- Desviaciones de las especificaciones del protocolo.

:::image type="content" source="media/integration-splunk/address-scan.png" alt-text="La pantalla de detecciones.":::

Puede configurar Defender para IoT para que envíe alertas al servidor de Splunk, donde se muestra la información de alertas en el panel de Splunk Enterprise.

:::image type="content" source="media/integration-splunk/alerts-and-details.png" alt-text="Visualización de todas las alertas y sus detalles.":::

La siguiente información de alerta se envía al servidor de Splunk.

- Fecha y hora de la alerta.

- Motor de Defender para IoT que detectó el evento: motor de infracción del protocolo, infracción de la directiva, malware, anomalía o infracción operativa.

- Título de la alerta.

- Mensaje de alerta.

- la gravedad de la alerta: Advertencia, leve, grave o crítica.

- Nombre del dispositivo de origen.

- la dirección IP del dispositivo de origen.

- Nombre del dispositivo de destino.

- Dirección IP del dispositivo de destino.

- Dirección IP de la plataforma de Defender para IoT (host).

- Nombre del dispositivo de la plataforma de Defender para IoT (tipo de origen).

A continuación, se muestra una salida de ejemplo:

| Time | Evento |
|--|--|
| 23/7/15<br />9:28:31.000 p. m. | **Alerta de la plataforma de Defender para IoT**: Un comando PLC detuvo un dispositivo.<br /><br />**Tipo**: infracción operativa. <br /><br />**Gravedad**: Principal <br /><br />**Nombre de origen**: my_device1. <br /><br />**Dirección IP de origen**: 192.168.110.131 <br /><br />**Nombre del destino**: my_device2.<br /><br /> **Dirección IP de destino**: 10.140.33.238 <br /><br />**Mensaje**: A network device was stopped using a Stop PLC command. This device will not operate until a Start command is sent. 192.168.110.131 was stopped by 10.140.33.238 (a Siemens S7 device), using a PLC Stop command (Se detuvo un dispositivo de red mediante un comando de detención del PLC. Este dispositivo no funcionará hasta que se envíe un comando de inicio. 10.140.33.238 detuvo 192.168.110.131 [un dispositivo Siemens S7], mediante un comando de detención del PLC).<br /><br />**Host**: 192.168.90.43 <br /><br />**Sourcetype**: Sensor_Agent |

## <a name="define-alert-forwarding-rules"></a>Definición de reglas de reenvío de alertas

Use las *reglas de reenvío* de Defender para IoT para enviar información de alertas a los servidores de Splunk.

Existen opciones para personalizar las reglas de alertas en función de:

- Los protocolos específicos detectados.

- La gravedad del evento.

- El motor de Defender para IoT que detecta los eventos.

Para crear una regla de reenvío:

1. En el panel izquierdo del sensor o de la consola de administración local, seleccione **Reenvío.**

    :::image type="content" source="media/integration-splunk/forwarding.png" alt-text="Selección del botón azul Create Forwarding Alert (Crear alerta de reenvío).":::

1. Seleccione **Create Forwarding Rules** (Crear reglas de reenvío). En la ventana **Create Forwarding Rule** (Crear regla de reenvío), defina los parámetros de la regla.

    :::image type="content" source="media/integration-splunk/forwarding-rule.png" alt-text="Creación de las reglas para la regla de reenvío.":::

    | Parámetro | Descripción |
    |--|--|
    | **Nombre** | Nombre de la regla de reenvío. |
    | **Select Severity** (Seleccionar gravedad) | El incidente de nivel de seguridad mínimo que se va a reenviar. Por ejemplo, si selecciona Minor (Leve), se reenviarán las alertas de gravedad leve y todas las alertas por encima de este nivel de gravedad. |
    | **Protocolos** | De forma predeterminada, se seleccionan todos los protocolos. Para seleccionar un protocolo concreto, elija **Específico** y seleccione el protocolo al que se aplica esta regla. |
    | **Engines** (Motores) | De forma predeterminada, intervienen todos los motores de seguridad. Para seleccionar un motor de seguridad concreto al que se aplica esta regla, seleccione **Específico** y elija el motor. |
    | **Notificaciones del sistema** | Estado en línea o sin conexión del sensor de reenvío. Esta opción solo está disponible si ha iniciado sesión en el administrador central. |

1. Para indicar a Defender para IoT que envíe información de recursos a Splunk, seleccione **Acción** y, luego, elija **Send to Splunk Server** (Enviar al servidor de Splunk).

1. Escriba los siguientes parámetros de Splunk.

    :::image type="content" source="media/integration-splunk/parameters.png" alt-text="Los parámetros de Splunk que debe especificar en esta pantalla.":::

    | Parámetro | Descripción |
    |--|--|
    | **Host** | Dirección del servidor de Splunk. |
    | **Puerto** | 8089 |
    | **Nombre de usuario** | Nombre de usuario del servidor de Splunk. |
    | **Contraseña** | Contraseña del servidor de Splunk. |

1. Seleccionar **Enviar**

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [reenviar información de alertas](how-to-forward-alert-information-to-partners.md).
