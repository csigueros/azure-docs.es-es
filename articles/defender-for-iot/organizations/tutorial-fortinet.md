---
title: Integración de Fortinet con Azure Defender para IoT
description: En este tutorial, aprenderá a integrar Azure Defender para IoT con Fortinet.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 09/23/2021
ms.custom: template-tutorial
ms.openlocfilehash: 30c60a71c15b8b597735c69f2f1f76178016b749
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128702102"
---
# <a name="tutorial-integrate-fortinet-with-azure-defender-for-iot"></a>Tutorial: Integración de Fortinet con Azure Defender para IoT

Este tutorial le ayudará a aprender a integrar Fortinet con Azure Defender para IoT.

Azure Defender para IoT mitiga los riesgos de IIoT, ICS y SCADA con motores de autoaprendizaje preparados para ICS que proporcionan información detallada sobre dispositivos, puntos vulnerables y amenazas para ICS.  Defender para IoT lo consigue sin depender de agentes, reglas, firmas, aptitudes especializadas o conocimientos previos del entorno.

Defenders para IoT y Fortinet han establecido una asociación tecnológica que detecta y detiene los ataques a IoT y a las redes ICS.

Fortinet y Azure Defender para IoT impiden lo siguiente:

- Cambios no autorizados en los controladores lógicos programables (PLC).

- Malware que manipula los dispositivos de ICS e IoT mediante sus protocolos nativos.

- Recopilación de datos por herramientas de reconocimiento.

- Infracciones de protocolo producidas por configuraciones incorrectas o atacantes malintencionados.

Defender para IoT detecta un comportamiento anómalo en las redes de IoT e ICS y ofrece esa información a FortiGate y FortiSIEM, como se indica a continuación:

- **Visibilidad**: la información proporcionada por Defender para IoT permite a los administradores de FortiSIEM ver las redes de IoT e ICS anteriormente invisibles.

- **Bloquear ataques malintencionados:** los administradores de FortiGate pueden usar la información detectada por Defender para IoT para crear reglas que detengan un comportamiento anómalo, independientemente de si el comportamiento está causado por actores caóticos o dispositivos mal configurados, antes de que se produzcan daños en la producción, los beneficios o los usuarios.

FortiSIEM y la solución de administración de eventos e incidentes de seguridad de múltiples proveedores de Fortinet reúnen visibilidad, correlación, respuesta automatizada y corrección en una única solución escalable.

Al utilizar una vista de servicios de negocio, se reduce la complejidad de la administración de la red y las operaciones de seguridad, lo que libera recursos y mejora la detección de infracciones. FortiSIEM proporciona correlación cruzada al aplicar el aprendizaje automático y UEBA para mejorar la respuesta, con el fin de detener las brechas antes de que se produzcan.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> - Crear una clave de API en Fortinet
> - Establecer una regla de reenvío para bloquear alertas relacionadas con malware
> - Bloquear el origen de alertas sospechosas
> - Enviar alertas de Defender para IoT a FortiSIEM
> - Bloquear un origen malintencionado mediante el firewall de Fortigate

Si aún no tiene una cuenta de Azure, puede [crear una cuenta gratuita de Azure ahora](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Requisitos previos

No hay ningún requisito previo para este tutorial.

## <a name="create-an-api-key-in-fortinet"></a>Creación de una clave de API en Fortinet

Una clave de interfaz de programación de aplicaciones (API) es un código generado de forma única que permite a una API identificar la aplicación o el usuario que solicita acceso a ella. Se necesita una clave de API para que Azure Defender para IoT y Fortinet se comuniquen correctamente.

**Para crear una clave de API en Fortinet**:

1. En FortiGate, vaya a **System** > **Admin Profiles** (Sistema > Perfiles de administración).

1. Cree un perfil con los permisos siguientes:

    | Parámetro | Número de selección |
    |--|--|
    | **Security Fabric** (Tejido de seguridad) | None |
    | **Fortiview** | None |
    | **User & Device** (Usuario y dispositivo) | None |
    | **Firewall** | Personalizado |
    | **Directiva** | Lectura/Escritura |
    | **Dirección** | Lectura/Escritura  |
    | **Servicio** | None |
    | **Programación** | None |
    | **Logs & Report** (Registros e informe) | None |
    | **Network** | None |
    | **Sistema** | None |
    | **Security Profile** (Perfil de seguridad) | None |
    | **VPN** | None |
    | **WAN Opt & Cache** (Opción de WAN y caché) | None |
    | **WiFi & Switch** (WiFi y conmutador) | None |

1. Vaya a **System** > **Administrators** (Sistema > Administradores) y cree un nuevo **administrador de API REST** con los siguientes campos:

    | Parámetro | Descripción |
    | --------- | ----------- |
    | **Nombre de usuario** | Escriba el nombre de la regla de reenvío. |
    | **Comentarios** | Especifique el incidente de nivel de seguridad mínimo que se va a reenviar. Por ejemplo, si se selecciona **Minor** (Leve), se reenviarán no solo las alertas leves, sino también todas las aquellas con un nivel de gravedad superior. |
    | **Perfil de administrador** | En la lista desplegable, seleccione el nombre del perfil que ha definido en el paso anterior. |
    | **PKI Group** (Grupo de PKI) | Cambie el conmutador a **Disable** (Deshabilitar). |
    | **CORS Allow Origin** (Permitir origen en CORS) | Cambie el conmutador a **Enable** (Habilitar). |
    | **Restrict login to trusted hosts** (Restringir el inicio de sesión a hosts de confianza) | Agregue las direcciones IP de los sensores y las consolas de administración que se conectarán a FortiGate. |

Cuando se genere la clave de API, guárdela, ya que no se volverá a proporcionar.

:::image type="content" source="media/tutorial-fortinet/api-key.png" alt-text="La captura de pantalla de la descripción genera automáticamente una nueva clave de API.":::

## <a name="set-a-forwarding-rule-to-block-malware-related-alerts"></a>Establecimiento de una regla de reenvío para bloquear alertas relacionadas con malware

El firewall de FortiGate se puede utilizar para bloquear el tráfico sospechoso.

**Para establecer una regla de reenvío que bloquee las alertas relacionadas con malware**:

1. Inicie sesión en la consola de administración de Azure Defender para IoT.

1. En el panel izquierdo, seleccione **Reenvío**.

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="Captura de pantalla de la opción de la ventana de reenvío en un sensor.":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

1. Seleccione **Create Forwarding Rule** (Crear regla de reenvío) y defina los siguientes parámetros de la regla.

    | Parámetro | Descripción |
    | --------- | ----------- |
    | **Nombre** | Especifique un nombre descriptivo para la alerta de reenvío. |
    | **Select Severity** (Seleccionar gravedad) | En el menú desplegable, seleccione el incidente de nivel de seguridad mínimo que desea reenviar. Por ejemplo, si se selecciona **Minor** (Leve), se reenviarán no solo las alertas leves, sino también todas las aquellas con un nivel de gravedad superior. |
    | **Protocolos** | Para seleccionar un protocolo concreto, elija **Específico** y seleccione el protocolo al que se aplica esta regla. De forma predeterminada, se seleccionan todos los protocolos. |
    | **Engines** (Motores) | Para seleccionar un motor de seguridad concreto al que se aplica esta regla, seleccione **Específico** y elija el motor. De forma predeterminada, intervienen todos los motores de seguridad. |
    | **Notificaciones del sistema** | Reenvíe el estado *en línea* o *desconectado* del sensor. Esta opción solo está disponible si ha iniciado sesión en la consola de administración local. |

1. En la sección Acciones, seleccione **Agregar** y, a continuación, seleccione **Enviar a FortiGate** en el menú desplegable.

    :::image type="content" source="media/tutorial-fortinet/fortigate.png" alt-text="Captura de pantalla de la sección Agregar una acción de la ventana Create Forwarding Rule (Crear regla de reenvío).":::

1. Para configurar la regla de reenvío de FortiGate, establezca los parámetros siguientes:

    :::image type="content" source="media/tutorial-fortinet/configure.png" alt-text="Captura de pantalla de la ventana Create Forwarding Rule (Crear regla de reenvío).":::

    | Parámetro | Descripción |
    |--|--|
    | **Host** | Especifique la dirección IP del servidor de FortiGate. |
    | **Clave de API** | Escriba la [clave de API](#create-an-api-key-in-fortinet) creada en FortiGate. |
    | **Interfaz entrante** | Escriba el puerto de la interfaz de entrada. |
    | **Interfaz de salida** | Escriba el puerto de la interfaz de salida. |
    | **Configuración**| Asegúrese de que se muestra **√** en las siguientes opciones para habilitar el bloqueo de orígenes sospechosos mediante el firewall de FortiGate: <br> - **Block illegal function codes (Bloquear códigos de función ilegales)** : infracciones de protocolo - valor de campo no válido que infringe la especificación del protocolo ICS (posible ataque) <br /> - **Block unauthorized PLC programming / firmware updates (Bloquear las actualizaciones de firmware/programación de PLC no autorizadas)** : cambios de PLC no autorizados <br /> - **Block unauthorized PLC stop (Bloquear detención de PLC no autorizada)** : detención de PLC (tiempo de inactividad) <br /> - **Block malware-related alerts (Bloquear las alertas relacionadas con malware)** : bloqueo de los intentos de malware industrial (TRITON, NotPetya, etc.). <br /> -  **(Opcional)** : puede seleccionar la opción de **Automatic blocking** (Bloqueo automático). Si se selecciona Automatic blocking (Bloqueo automático), el bloqueo se ejecuta de manera automática e inmediata. <br /> - **Block unauthorized scanning (Bloquear el análisis no autorizado)** : análisis no autorizado (reconocimiento potencial) |

1. Seleccione **Submit** (Enviar).

## <a name="block-the-source-of-suspicious-alerts"></a>Bloqueo del origen de alertas sospechosas

El origen de las alertas sospechosas se puede bloquear para evitar más repeticiones.

**Para bloquear el origen de alertas sospechosas**:

1. Inicie sesión en la consola de administración y seleccione **Alertas** en el menú de la izquierda.

1. Seleccione la alerta relacionada con la integración de Fortinet.

1. Para bloquear automáticamente el origen sospechoso, seleccione **Block Source** (Bloquear origen).

    :::image type="content" source="media/tutorial-fortinet/block-source.png" alt-text="Captura de pantalla de la ventana de alerta.":::

1. En el cuadro de diálogo de confirmación, seleccione **OK** (Aceptar).

## <a name="send-defender-for-iot-alerts-to-fortisiem"></a>Envío de alertas de Defender para IoT a FortiSIEM

Las alertas de Defender para IoT proporcionan información sobre una amplia variedad de eventos de seguridad, como por ejemplo:

- Desviaciones de la actividad de red de línea de base aprendida

- Detecciones de malware

- Detecciones basadas en cambios operativos sospechosos

- Anomalías de red

- Desviaciones de las especificaciones del protocolo

Puede configurar Defender para IoT para que envíe alertas al servidor de FortiSIEM, donde se muestra la información de las alertas en la ventana de análisis:

:::image type="content" source="media/tutorial-fortinet/analytics.png" alt-text="Captura de pantalla de la ventana de análisis.":::

Cada alerta de Defender para IoT se analiza luego sin ninguna otra configuración en el lado de FortiSIEM y se presenta en FortiSIEM como evento de seguridad. Los siguientes detalles del evento aparecen de forma predeterminada:

:::image type="content" source="media/tutorial-fortinet/event-detail.png" alt-text="Captura de pantalla de la vista de los detalles del evento en la ventana de detalles del evento.":::

Puede utilizar luego las reglas de reenvío de Defender para IoT a fin de enviar información de alertas a FortiSIEM.

**Para utilizar las reglas de reenvío de Defender para IoT a fin de enviar información de alertas a FortiSIEM**:

1. En el panel izquierdo del sensor o de la consola de administración local, seleccione **Reenvío**.

    [:::image type="content" source="media/tutorial-fortinet/forwarding-view.png" alt-text="Captura de pantalla de la vista de las reglas de reenvío en la ventana de reenvío.":::](media/tutorial-fortinet/forwarding-view.png#lightbox)

2. Seleccione **Create Forwarding Rule** (Crear regla de reenvío) y defina los parámetros de la regla.

    | Parámetro | Descripción |
    |--|--|
    | **Nombre** | Especifique un nombre descriptivo para la alerta de reenvío. |
    | **Select Severity** (Seleccionar gravedad) | Seleccione el incidente de nivel de seguridad mínimo que se reenvía. Por ejemplo, si se selecciona **Minor** (Leve), se reenviarán no solo las alertas leves, sino también todas las aquellas con un nivel de gravedad superior. |
    | **Protocolos** | Para seleccionar un protocolo concreto, elija **Específico** y seleccione el protocolo al que se aplica esta regla. De forma predeterminada, se seleccionan todos los protocolos. |
    | **Engines** (Motores) | Para seleccionar un motor de seguridad concreto al que se aplica esta regla, seleccione **Específico** y elija el motor. De forma predeterminada, intervienen todos los motores de seguridad. |
    | **Notificaciones del sistema** | Reenvíe el estado *en línea* o *desconectado* de un sensor. Esta opción solo está disponible si ha iniciado sesión en la consola de administración local. |

3. En la sección de acciones, seleccione **Enviar a FortiSIEM**.

    :::image type="content" source="media/tutorial-fortinet/forward-rule.png" alt-text="Captura de pantalla de la creación de una regla de reenvío y selección de envío a Fortinet.":::

4. Escriba los detalles del servidor de FortiSIEM.

    :::image type="content" source="media/tutorial-fortinet/details.png" alt-text="Captura de pantalla de la incorporación de los detalles de FortiSIEm a la regla de reenvío.":::

    | Parámetro | Descripción |
    | --------- | ----------- |
    | **Host** | Escriba la dirección IP del servidor de FortiSIEM. |
    | **Puerto** | Escriba el puerto del servidor de FortiSIEM. |
    | **Zona horaria** | Marca de tiempo para la detección de alertas. |

5. Seleccione **Submit** (Enviar).

## <a name="block-a-malicious-source-using-the-fortigate-firewall"></a>Bloqueo de un origen malintencionado mediante el firewall de Fortigate

Puede establecer directivas para bloquear automáticamente orígenes malintencionados en el firewall de FortiGate mediante alertas en Defender para IoT.

:::image type="content" source="media/tutorial-fortinet/firewall.png" alt-text="Captura de pantalla de la vista de la ventana del firewall de FortiGate.":::

Por ejemplo, la siguiente alerta puede bloquear el origen malintencionado:

:::image type="content" source="media/tutorial-fortinet/suspicion.png" alt-text="Captura de pantalla de la ventana de sospecha de malware NotPetya.":::

**Para establecer una regla de firewall de FortiGate que bloquee un origen malintencionado**:

1. En FortiGate, [cree una clave de API](#create-an-api-key-in-fortinet).

1. Inicie sesión en el sensor de Defender para IoT o en la consola de administración y seleccione **Reenvío**, [establezca una regla de reenvío que bloquee las alertas relacionadas con malware](#set-a-forwarding-rule-to-block-malware-related-alerts).

1. En el sensor de Defender para IoT o en la consola de administración, seleccione **Alertas** y [bloquee un origen malintencionado](#block-a-malicious-source-using-the-fortigate-firewall).

1. Vaya a la ventana **Administrator** (Administrador) de FortiGate y localice la dirección de origen malintencionada que ha bloqueado.

   :::image type="content" source="media/tutorial-fortinet/administrator.png" alt-text="Captura de pantalla de la vista de la ventana del administrador de FortiGate.":::

   La directiva de bloqueo se creará automáticamente y aparecerá en la ventana de la directiva IPv4 de FortiGate.

   :::image type="content" source="media/tutorial-fortinet/policy.png" alt-text="Captura de pantalla de la vista de la ventana de la directiva IPv4 de FortiGate.":::

1. Seleccione la directiva y asegúrese de que la opción para habilitar esta directiva esté en posición activada.

   :::image type="content" source="media/tutorial-fortinet/edit.png" alt-text="Captura de pantalla de la vista de edición de la directiva IPv4 de FortiGate.":::

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos para limpiar.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo empezar a trabajar con la integración de Fortinet. Continúe para obtener información sobre la [integración de Palo Alto](./tutorial-palo-alto.md).

> [!div class="nextstepaction"]
> [Botón de pasos siguientes](./tutorial-palo-alto.md)
