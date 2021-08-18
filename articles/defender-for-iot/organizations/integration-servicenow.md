---
title: Acerca de la integración de ServiceNow
description: La aplicación de administración de ICS de Defender para IoT para ServiceNow proporciona a los analistas de los centros de operaciones de seguridad no solo visibilidad multidimensional de los protocolos de TO especializados y los dispositivos IoT implementados en entornos industriales, sino también un análisis del comportamiento compatible con ICS, lo que permite detectar rápidamente comportamientos sospechosos o anómalos.
ms.date: 1/17/2021
ms.topic: article
ms.openlocfilehash: 6e39c3d018003983f6dc5b5e16a9791de84d6005
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113018921"
---
# <a name="the-defender-for-iot-ics-management-application-for-servicenow"></a>La aplicación de administración de ICS de Defender para IoT para ServiceNow

La aplicación de administración de ICS de Defender para IoT para ServiceNow proporciona a los analistas de los centros de operaciones de seguridad no solo visibilidad multidimensional de los protocolos de OT especializados y los dispositivos IoT implementados en entornos industriales, sino también un análisis del comportamiento compatible con ICS, lo que permite detectar rápidamente comportamientos sospechosos o anómalos. Esta es una evolución importante, dada la convergencia continua de TI y OT para admitir nuevas iniciativas de Internet de las cosas, como máquinas inteligentes e inteligencia en tiempo real.

La aplicación también permite responder a incidentes tanto de TI como de OT desde dentro de un centro de operaciones de seguridad corporativo.

## <a name="about-defender-for-iot"></a>Acerca de Defender para IoT

Defender para IoT es la única plataforma de ciberseguridad para ICS e IoT creada por expertos del equipo azul con experiencia en la defensa de una infraestructura nacional crítica y la única plataforma con análisis de amenazas patentados y compatibles con ICS y aprendizaje automático. Defender para IoT ofrece:

- Información inmediata sobre el panorama de los dispositivos ICS con una amplia variedad de detalles sobre los atributos.

- Conocimiento integrado profundo compatible con ICS de los protocolos, dispositivos y aplicaciones de OT, así como de sus comportamientos.

- Conclusiones inmediatas sobre vulnerabilidades y amenazas conocidas y de día cero.

- Una tecnología de modelado automatizado de amenazas de ICS para predecir las rutas más probables de los ataques de ICS dirigidos mediante un análisis propietario.

> [!Note]
> Las referencias a CyberX se refieren a Azure Defender para IoT.

## <a name="about-the-integration"></a>Acerca de la integración

La integración de Defender para IoT con ServiceNow proporciona un nuevo nivel de visibilidad, supervisión y control centralizados para el panorama de IoT y OT. Estas plataformas puente permiten la visibilidad y la administración de amenazas automatizadas en dispositivos ICS e IoT a los que anteriormente no se podía acceder.

### <a name="threat-management"></a>Administración de amenazas

La aplicación de administración de ICS de Defender para IoT ayuda a:

- Reducir el tiempo que necesitan las organizaciones industriales y de infraestructura crítica para detectar, investigar y actuar sobre las ciberamenazas.

- Obtener inteligencia en tiempo real sobre los riesgos de OT.

- Correlacionar las alertas de Defender para IoT con los flujos de trabajo de administración de incidentes y de supervisión de amenazas de ServiceNow.

- Desencadenar flujos de trabajo y vales de ServiceNow con otros servicios y aplicaciones en la plataforma ServiceNow.

Los motores de seguridad de Defender para IoT identifican las amenazas de seguridad de ICS y SCADA, y posteriormente proporcionan una alerta inmediata a los ataques de malware, la red y las desviaciones de la directiva de seguridad, así como las anomalías operativas y de protocolo. Para más información sobre los detalles de alerta enviados a ServiceNow, consulte el apartado sobre los [informes de alertas](#alert-reporting).

### <a name="device-visibility-and-management"></a>Administración y visibilidad de dispositivos

La base de datos de administración de configuración de ServiceNow (CMDB) se enriquece y complementa con un amplio conjunto de atributos de dispositivo insertados por la plataforma de Defender para IoT. Esto garantiza una visibilidad completa y continua del panorama del dispositivo y permite supervisar y responder desde un único panel. Para más información sobre los atributos de dispositivo enviados a ServiceNow, consulte el apartado sobre la [visualización de detecciones de Defender para IoT en ServiceNow](#view-defender-for-iot-detections-in-servicenow).

## <a name="system-requirements-and-architecture"></a>Arquitectura y requisitos del sistema

En este artículo se describe:

- **Requisitos de software**  
- **Arquitectura**

## <a name="software-requirements"></a>Requisitos de software

- Versión 3.0.2 de ServiceNow Service Management

- Revisión 2.8.11.1, o superior, de Defender para IoT

> [!Note]
> Si ya trabaja con una integración de Defender para IoT y ServiceNow, y utiliza la consola de administración local para realizar la actualización, los datos de la versión anterior recibidos de los sensores de Defender para IoT deberían borrarse de ServiceNow.

## <a name="architecture"></a>Architecture

### <a name="on-premises-management-console-architecture"></a>Arquitectura de consola de administración local

La consola de administración local proporciona una fuente unificada para toda la información de dispositivos y alertas que se envía a ServiceNow.

Puede configurar una consola de administración local para que se comunique con una instancia de ServiceNow. La consola de administración local inserta datos de los sensores a la aplicación Defender para IoT mediante la API REST.

Si configura el sistema para que trabaje con una consola de administración local, deshabilite la sincronización de ServiceNow, las reglas de reenvío y las configuraciones de proxy en los sensores, en caso de que se hayan configurado.

Estas configuraciones deben utilizarse para la consola de administración local. Las instrucciones de configuración se describen en este artículo.

### <a name="sensor-architecture"></a>Arquitectura de sensores

Si desea configurar el entorno para incluir la comunicación directa entre los sensores y ServiceNow, defina la sincronización de ServiceNow, las reglas de reenvío y la configuración del proxy (si se necesita un proxy) para cada sensor.

Se recomienda configurar la integración mediante la consola de administración local para comunicarse con ServiceNow.

## <a name="create-access-tokens-in-servicenow"></a>Creación de tokens de acceso en ServiceNow

En este artículo se describe cómo crear tokens de acceso en ServiceNow. El token es necesario para comunicarse con Defender para IoT.

Necesitará el **identificador de cliente** y el **secreto de cliente** tanto al crear reglas de reenvío de Defender para IoT, que reenvían información de las alertas a ServiceNow, como al configurar Defender para IoT para que inserte los atributos de dispositivo en las tablas de ServiceNow.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Configuración de Defender para IoT para que se comunique con ServiceNow

En este artículo se describe cómo configurar Defender para IoT para que se comunique con ServiceNow.

### <a name="send-defender-for-iot-alert-information"></a>Envío de información de alertas de Defender para IoT

En este artículo se describe cómo configurar Defender para IoT para insertar información de alertas en las tablas de ServiceNow. Para obtener información sobre los datos de alertas enviados, consulte el apartado acerca de los [informes de alertas](#alert-reporting).

Las alertas de Defender para IoT aparecen en ServiceNow como incidentes de seguridad.

Para enviar información de alertas a ServiceNow, defina una regla de *reenvío* de Defender para IoT.

Para ello:

1. En el panel izquierdo de Defender para IoT, seleccione **Forwarding** (Reenvío).  

1. Seleccione el :::image type="content" source="media/integration-servicenow/plus.png" alt-text="botón del icono del signo más."::: . Se abre el cuadro de diálogo Create Forwarding Rule (Crear regla de reenvío).  

    :::image type="content" source="media/integration-servicenow/forwarding-rule.png" alt-text="Crear regla de reenvío":::

1. Agregue un nombre de regla.

1. Defina los criterios según los cuales Defender para IoT desencadenará la regla de reenvío. El uso de criterios en las reglas de reenvío ayuda a identificar y administrar el volumen de información que se envía desde Defender para IoT a ServiceNow. Están disponibles las opciones siguientes:

    - **Severity levels** (Niveles de gravedad): es el incidente de nivel de seguridad mínimo que se reenvía. Por ejemplo, si se selecciona **Minor** (Leve), se reenviarán las alertas leves y todas aquellas con un nivel de gravedad superior. Los niveles los predefine Defender para IoT.

    - **Protocols** (Protocolos): la regla de reenvío solo se activa si el tráfico detectado se estaba ejecutando a través de protocolos específicos. Seleccione los protocolos necesarios en la lista desplegable o selecciónelos todos.

    - **Engines** (Motores): seleccione los motores necesarios o selecciónelos todos. Se enviarán las alertas de los motores seleccionados.

1. Compruebe que la casilla **Report Alert Notifications** (Informar de notificaciones de alertas) está seleccionada.

1. En la sección Actions (Acciones), seleccione **Add** (Agregar) y, después, **ServiceNow**.

    :::image type="content" source="media/integration-servicenow/select-servicenow.png" alt-text="Seleccionar ServiceNow en las opciones de la lista desplegable":::.

1. Escriba los parámetros de acción de ServiceNow:

    :::image type="content" source="media/integration-servicenow/parameters.png" alt-text="Rellenar los parámetros de acción de ServiceNow":::

1. En el panel **Actions** (Acciones), establezca los parámetros siguientes:

  | Parámetro | Descripción |
  |--|--|
  | Domain | Escriba la dirección IP del servidor de ServiceNow. |
  | Nombre de usuario | Escriba el nombre de usuario del servidor de ServiceNow. |
  | Contraseña | Escriba la contraseña del servidor de ServiceNow. |
  | Id. de cliente | Escriba el identificador de cliente que ha recibido para Defender para IoT en la página **Application Registries** (Registros de aplicación) de ServiceNow. |
  | Secreto del cliente | Escriba el secreto de cliente que ha creado para Defender para IoT en la página **Application Registries** (Registros de aplicación) de ServiceNow. |
  | Tipo de informe | **Incidents** (Incidentes): reenvíe una lista de alertas que se presentan en ServiceNow con un identificador de incidente y una breve descripción de cada alerta.<br /><br />**Defender for IoT Application** (Aplicación Defender para IoT): reenvíe la información de alerta completa, lo que incluye los detalles del sensor, el motor y las direcciones de origen y destino. La información se reenvía a Defender para IoT en la aplicación de ServiceNow. |

1. Seleccione **SAVE** (GUARDAR). Las alertas de Defender para IoT aparecen en ServiceNow como incidentes.

### <a name="send-defender-for-iot-device-attributes"></a>Envío de atributos de dispositivo de Defender para IoT

En este artículo se describe cómo configurar Defender para IoT para insertar una amplia variedad de atributos de dispositivo en las tablas de ServiceNow. Para saber qué tipo de información se ha insertado en ServiceNow, consulte ***Información de inventario***.

Para enviar atributos a ServiceNow, debe asignar la consola de administración local a una instancia de ServiceNow. De esta forma tiene la certeza de que la plataforma de Defender para IoT se puede comunicar con la instancia y autenticarla.

Para agregar una instancia de ServiceNow:

1. Inicie sesión en la consola de administración local de Azure Defender para IoT.

1. Seleccione **Configuración del sistema** y, después, **ServiceNow** en la sección de integración de la consola de administración local.

      :::image type="content" source="media/integration-servicenow/servicenow.png" alt-text="Seleccione el botón de ServiceNow.":::

1. Escriba los siguientes parámetros de sincronización en el cuadro de diálogo ServiceNow Sync (Sincronización de ServiceNow).

    :::image type="content" source="media/integration-servicenow/sync.png" alt-text="El cuadro de diálogo ServiceNow Sync (Sincronización de ServiceNow).":::

     Parámetro | Descripción |
    |--|--|
    | Enable Sync (Habilitar sincronización) | Habilite y deshabilite la sincronización después de definir los parámetros. |
    | Sync Frequency (minutes) [Frecuencia de sincronización (minutos)] | De forma predeterminada, la información se inserta en ServiceNow cada 60 minutos. El mínimo es de 5 minutos. |
    | Instancia de ServiceNow | Escriba la dirección URL de la instancia de ServiceNow. |
    | Id. de cliente | Escriba el identificador de cliente que ha recibido para Defender para IoT en la página **Application Registries** (Registros de aplicación) de ServiceNow. |
    | Secreto del cliente | Escriba la cadena de secreto de cliente que ha creado para Defender para IoT en la página **Application Registries** (Registros de aplicación) de ServiceNow. |
    | Nombre de usuario | Escriba el nombre de usuario de esta instancia. |
    | Contraseña | Especifique la contraseña de esta instancia. |

1. Seleccione **SAVE** (GUARDAR).

## <a name="verify-communication"></a>Comprobación de la comunicación

Compruebe que la consola de administración local está conectada a la instancia de ServiceNow. Para ello, debe mirar la fecha de *Last Sync* (Última sincronización).

:::image type="content" source="media/integration-servicenow/sync-confirmation.png" alt-text="Para comprobar la fecha y hora de la comunicación, mire la última sincronización.":::

## <a name="set-up-the-integrations-using-the-https-proxy"></a>Configuración de las integraciones mediante el proxy HTTPS

Cuando se configura la integración de Defender para IoT y ServiceNow, la consola de administración local y el servidor de ServiceNow se comunican mediante el puerto 443. Si el servidor de ServiceNow está detrás del proxy, no se puede usar el puerto predeterminado.

Para que Defender para IoT admita un proxy HTTPS en la integración con ServiceNow, es preciso habilitar el cambio del puerto predeterminado que se usa para la integración.

Para configurar el proxy:

1. Edite las propiedades globales en la consola de administración local:  
    `sudo vim /var/cyberx/properties/global.properties`

2. Agregue los siguientes parámetros:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Guarde y salga.

4. Ejecute el comando siguiente: `sudo monit restart all`.

Después de la configuración, todos los datos de ServiceNow se reenvían mediante el proxy configurado.

## <a name="download-the-defender-for-iot-application"></a>Descarga de la aplicación Defender para IoT

En este artículo se describe cómo descargar la aplicación.

Para acceder a la aplicación Defender para IoT:

1. Vaya a <https://store.servicenow.com/>.

2. Busque `Defender for IoT` o `CyberX IoT/ICS Management`.

   :::image type="content" source="media/integration-servicenow/search-results.png" alt-text="Busque CyberX en la barra de búsqueda.":::

3. Seleccione la aplicación.

   :::image type="content" source="media/integration-servicenow/cyberx-app.png" alt-text="Seleccione la aplicación de la lista.":::

4. Seleccione **Request App** (Solicitar aplicación).

   :::image type="content" source="media/integration-servicenow/sign-in.png" alt-text="Inicie sesión en la aplicación con sus credenciales.":::

5. Inicie sesión y descargue la aplicación.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Visualización de las detecciones de Defender para IoT en ServiceNow

En este artículo se describen los atributos de dispositivo y la información de alertas que se presenta en ServiceNow.

Para ver los atributos de dispositivo:

1. Inicie sesión en ServiceNow.

2. Vaya a la **plataforma de CyberX**.

3. Vaya a **Inventory** (Inventario) o a **Alert** (Alerta).

   [:::image type="content" source="media/integration-servicenow/alert-list.png" alt-text="Inventario o alerta":::](media/integration-servicenow/alert-list.png#lightbox)

## <a name="inventory-information"></a>Información del inventario

La base de datos de administración de la configuración (CMDB) se enriquece y complementa con los datos que Defender para IoT envía a ServiceNow. Al agregar o actualizar atributos de dispositivo en las tablas de elementos de configuración de CMDB de ServiceNow, Defender para IoT puede desencadenar los flujos de trabajo y las reglas de negocio de ServiceNow.

Está disponible la siguiente información:

- Los atributos del dispositivo, por ejemplo, el MAC del dispositivo, el sistema operativo, el proveedor o el protocolo detectado.

- Información del firmware, como la versión de firmware y el número de serie.

- Información del dispositivo conectado, como la dirección del tráfico entre el origen y el destino.

### <a name="devices-attributes"></a>Atributos de dispositivo

En este artículo se describen los atributos del dispositivo que se insertan en ServiceNow.

| Elemento | Descripción |
|--|--|
| Dispositivo | El nombre del sensor que detectó el tráfico. |
| ID | El identificador de dispositivo asignado por Defender para IoT. |
| Nombre | El nombre de dispositivo. |
| Dirección IP | La dirección (o direcciones) IP del dispositivo. |
| Tipo | El tipo de dispositivo, por ejemplo, un conmutador, PLC, historiador o controlador de dominio. |
| Dirección MAC | La dirección (o direcciones) MAC del dispositivo. |
| Sistema operativo | El sistema operativo del dispositivo. |
| Vendor | Proveedor del dispositivo. |
| Protocolos | Los protocolos detectados en el tráfico generado por el dispositivo. |
| Propietario | Escriba el nombre del propietario del dispositivo. |
| Ubicación | Escriba la ubicación física del dispositivo. |

Vea los dispositivos conectados a otro dispositivo.

Para ver los dispositivos conectados:

1. Seleccione un dispositivo y, después, seleccione la **aplicación** que se muestra para ese dispositivo.

    :::image type="content" source="media/integration-servicenow/appliance.png" alt-text="Seleccione la aplicación deseada en la lista.":::

1. En el cuadro de diálogo **Device Details** (Detalles del dispositivo), seleccione **Connected Devices** (Dispositivos conectados).

### <a name="firmware-details"></a>Detalles del firmware

En este artículo se describe la información del firmware del dispositivo que se inserta en ServiceNow.

| Elemento | Descripción |
|--|--|
| Dispositivo | El nombre del sensor que detectó el tráfico. |
| Dispositivo | El nombre de dispositivo. |
| Dirección | Dirección IP del dispositivo. |
| Module Address (Dirección del módulo) | Modelo del dispositivo e identificador o número de ranura. |
| Serie | Número de serie del dispositivo. |
| Modelo | Número de modelo del dispositivo. |
| Versión | Número de versión del firmware. |
| Datos adicionales | Más datos sobre el firmware que ha definido el proveedor como, por ejemplo, el tipo de dispositivo. |

### <a name="connection-details"></a>Detalles de conexión

En este artículo se describe la información de la conexión del dispositivo que se inserta en ServiceNow.

:::image type="content" source="media/integration-servicenow/connections.png" alt-text="Información acerca de la conexión del dispositivo":::

| Elemento | Descripción |
|--|--|
| Dispositivo | El nombre del sensor que detectó el tráfico. |
| Dirección | La dirección del tráfico. <br /> <br /> - **One Way** (Unidireccional) indica que el destino es el servidor y el origen es el cliente. <br /> <br /> - **Two Way** (Bidireccional) indica que tanto el origen como el destino son servidores o que se desconoce el cliente. |
| Identidad del dispositivo de origen | La dirección IP del dispositivo que se comunicó con el dispositivo conectado. |
| Nombre del dispositivo de origen | El nombre del dispositivo que se comunicó con el dispositivo conectado. |
| Identidad del dispositivo de destino | La dirección IP del dispositivo conectado. |
| Nombre de dispositivo de destino | El nombre del dispositivo conectado. |

## <a name="alert-reporting"></a>Notificación de alertas

Las alertas se desencadenan cuando los motores de Defender para IoT detectan en el tráfico de red y el comportamiento cambios que requieren su atención. Para más información sobre los tipos de alertas que genera cada motor, consulte [Acerca de los motores de alerta](#about-alert-engines).

En este artículo se describe la información de las alertas del dispositivo que se insertan en ServiceNow.

| Elemento | Descripción |
|--|--|
| Creado | La fecha y hora en que se generó la alerta. |
| Motor | El motor que detectó el evento. |
| Título | El título de la alerta. |
| Descripción | La descripción de la alerta. |
| Protocolo | El protocolo detectado en el tráfico. |
| Gravedad | La gravedad de alerta que define Defender para IoT. |
| Dispositivo | El nombre del sensor que detectó el tráfico. |
| Nombre de origen | El nombre de origen. |
| Dirección IP de origen| La dirección IP de origen. |
| Nombre del destino | El nombre del destino. |
| Dirección IP de destino | La dirección IP de destino. |
| Persona asignada | Escriba el nombre de la persona asignada al vale. |

### <a name="updating-alert-information"></a>Actualización de la información de alertas

Seleccione la entrada en la columna Created (Creado) para ver la información de la alerta en un formulario. No solo puede actualizar los detalles de la alerta, sino también asignar la alerta a un individuo para que la examine y la controle.

[:::image type="content" source="media/integration-servicenow/alert.png" alt-text="Ver la información de la alerta.":::](media/integration-servicenow/alert.png#lightbox)

### <a name="about-alert-engines"></a>Acerca de los motores de alerta

En este artículo se describe el tipo de alertas que desencadena cada motor.

| Tipo de alerta | Descripción |
|--|--|
| Alertas de infracción de directivas | Se desencadena cuando el motor de infracción de directivas detecta una desviación del tráfico previamente aprendido. Por ejemplo: <br /><br />- Se detecta un nuevo dispositivo. <br /><br />- Se detecta una nueva configuración en un dispositivo. <br /><br />- Un dispositivo no definido como dispositivo de programación realiza un cambio de programación. <br /><br />- Se produce un cambio en la versión del firmware. |
| Alertas de infracción de protocolos | Se desencadena cuando el motor de infracción de protocolo detecta valores de campo o estructuras de paquetes que no cumplen la especificación del protocolo. |
| Alertas operativas | Se desencadena cuando el motor operativo detecta incidentes de funcionamiento de la red o un dispositivo que no funciona correctamente. Por ejemplo, un dispositivo de red se detuvo mediante un comando Stop PLC o una interfaz de un sensor dejó de supervisar el tráfico. |
| Alertas de malware | Se desencadena cuando el motor de malware detecta cualquier tipo de actividad de red malintencionada como, por ejemplo, ataques conocidos como Conficker. |
| Alertas de anomalías | Se desencadenan cuando el motor de anomalías detecta una desviación. Por ejemplo, un dispositivo está explorando la red, pero no está definido como un dispositivo de detección. |

## <a name="next-steps"></a>Pasos siguientes

Más información sobre cómo [reenviar información de alertas](how-to-forward-alert-information-to-partners.md).
