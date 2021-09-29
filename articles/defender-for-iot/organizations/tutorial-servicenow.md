---
title: Integración de ServiceNow con Azure Defender para IoT
description: En este tutorial, aprenderá a integrar ServiceNow con Azure Defender para IoT.
author: ElazarK
ms.author: v-ekrieg
ms.topic: tutorial
ms.date: 07/27/2021
ms.custom: template-tutorial
ms.openlocfilehash: 506af89773f9639df2bd3fcc07a204531d0dcf6e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124749010"
---
# <a name="tutorial-integrate-servicenow-with-azure-defender-for-iot"></a>Tutorial: Integración de ServiceNow con Azure Defender para IoT

Este tutorial le ayudará a aprender a integrar ServiceNow con Azure Defender para IoT.

La integración de Defender para IoT con ServiceNow proporciona un nuevo nivel de visibilidad, supervisión y control centralizados para el panorama de IoT y OT. Estas plataformas puente permiten la visibilidad y la administración de amenazas automatizadas en dispositivos ICS e IoT a los que anteriormente no se podía acceder.

La base de datos de administración de configuración de ServiceNow (CMDB) se enriquece y complementa con un amplio conjunto de atributos de dispositivo insertados por la plataforma de Defender para IoT. Esto garantiza una visibilidad completa y continua del panorama de los dispositivos. Esta visibilidad le permite supervisar y responder desde un solo panel. 

En este tutorial aprenderá a:

> [!div class="checklist"]
> * Descargar la aplicación Defender para IoT en ServiceNow
> * Configuración de Defender para IoT para que se comunique con ServiceNow
> * Creación de tokens de acceso en ServiceNow
> * Enviar atributos de dispositivo de Defender para IoT a ServiceNow
> * Configurar la integración mediante un proxy HTTPS
> * Visualización de las detecciones de Defender para IoT en ServiceNow
> * Ver los dispositivos conectados

## <a name="prerequisites"></a>Requisitos previos

### <a name="software-requirements"></a>Requisitos de software

Acceso a ServiceNow y a Defender para IoT 

- Versión 3.0.2 de ServiceNow Service Management

- Revisión 2.8.11.1 o superior de Defender para IoT

> [!Note]
> Si ya trabaja con una integración de Defender para IoT y ServiceNow, y utiliza la consola de administración local para realizar la actualización, los datos de la versión anterior recibidos de los sensores de Defender para IoT deberían borrarse de ServiceNow.

### <a name="architecture"></a>Architecture

- **Arquitectura de la consola de administración local**: puede configurar una consola de administración local para que se comunique con una instancia de ServiceNow. La consola de administración local inserta datos de los sensores a la aplicación Defender para IoT mediante la API REST.

    Para configurar su sistema para que funcione con una consola de administración local, tendrá que deshabilitar la sincronización de ServiceNow, las reglas de reenvío y la configuración del proxy de todos los sensores en los que se hayan configurado.

- **Arquitectura de sensores**: si desea configurar el entorno para incluir la comunicación directa entre los sensores y ServiceNow, defina la sincronización de ServiceNow, las reglas de reenvío y la configuración del proxy (si se necesita un proxy) para cada sensor.

## <a name="download-the-defender-for-iot-application-in-servicenow"></a>Descargar la aplicación Defender para IoT en ServiceNow

Para acceder a la aplicación Defender para IoT en ServiceNow, deberá descargar la aplicación en el almacén de aplicaciones de ServiceNow. 

**Para acceder a la aplicación Defender para IoT en ServiceNow**:

1. Vaya al [almacén de aplicaciones de ServiceNow](https://store.servicenow.com/).

1. Busque `Defender for IoT` o `CyberX IoT/ICS Management`.

   :::image type="content" source="media/tutorial-servicenow/search-results.png" alt-text="Captura de pantalla de la pantalla de búsqueda en ServiceNow.":::

1. Seleccione la aplicación.

   :::image type="content" source="media/tutorial-servicenow/cyberx-app.png" alt-text="Captura de pantalla de los resultados de la pantalla de búsqueda.":::

1. Seleccione **Request App** (Solicitar aplicación).

   :::image type="content" source="media/tutorial-servicenow/sign-in.png" alt-text="Inicie sesión en la aplicación con sus credenciales.":::

1. Inicie sesión y descargue la aplicación.

## <a name="set-up-defender-for-iot-to-communicate-with-servicenow"></a>Configuración de Defender para IoT para que se comunique con ServiceNow

Configure Defender para IoT para insertar información de alertas en las tablas de ServiceNow. Las alertas de Defender para IoT aparecen en ServiceNow como incidentes de seguridad. Esto puede hacerse definiendo una regla de reenvío de Defender para IoT para enviar información de alerta a ServiceNow.

**Para insertar información de alerta en las tablas de ServiceNow**:

1. Inicie sesión en la consola de administración local.

1. Seleccione **Reenviar** en el panel izquierdo.

1. Haga clic en el botón :::image type="icon" source="media/tutorial-servicenow/plus-icon.png" border="false":::.

    :::image type="content" source="media/tutorial-servicenow/forwarding-rule.png" alt-text="Captura de pantalla de la ventana para crear regla de reenvío.":::

1. Agregue un nombre de regla.

1. Defina los criterios según los cuales Defender para IoT desencadenará la regla de reenvío. El uso de criterios en las reglas de reenvío ayuda a identificar y administrar el volumen de información que se envía desde Defender para IoT a ServiceNow. Están disponibles las opciones siguientes:

    - **Severity levels** (Niveles de gravedad): es el incidente de nivel de seguridad mínimo que se reenvía. Por ejemplo, si se selecciona **Minor** (Leve), se reenviarán las alertas leves y todas aquellas con un nivel de gravedad superior. Los niveles los predefine Defender para IoT.

    - **Protocols** (Protocolos): la regla de reenvío solo se activa si el tráfico detectado se estaba ejecutando a través de protocolos específicos. Seleccione los protocolos necesarios en la lista desplegable o selecciónelos todos.

    - **Engines** (Motores): seleccione los motores necesarios o selecciónelos todos. Se enviarán las alertas de los motores seleccionados.

1. Compruebe que la casilla **Report Alert Notifications** (Informar de notificaciones de alertas) está seleccionada.

1. En la sección Actions (Acciones), seleccione **Add** (Agregar) y, después, **ServiceNow**.

    :::image type="content" source="media/tutorial-servicenow/select-servicenow.png" alt-text="Seleccionar ServiceNow en las opciones de la lista desplegable":::.

1. Escriba los parámetros de acción de ServiceNow:

    :::image type="content" source="media/tutorial-servicenow/parameters.png" alt-text="Rellene los parámetros de acción de ServiceNow.":::

1. En el panel **Actions** (Acciones), establezca los parámetros siguientes:

      | Parámetro | Descripción |
      |--|--|
      | Domain | Escriba la dirección IP del servidor de ServiceNow. |
      | Nombre de usuario | Escriba el nombre de usuario del servidor de ServiceNow. |
      | Contraseña | Escriba la contraseña del servidor de ServiceNow. |
      | Id. de cliente | Escriba el identificador de cliente que ha recibido para Defender para IoT en la página **Application Registries** (Registros de aplicación) de ServiceNow. |
      | Secreto del cliente | Escriba el secreto de cliente que ha creado para Defender para IoT en la página **Application Registries** (Registros de aplicación) de ServiceNow. |
      | Tipo de informe | **Incidents** (Incidentes): reenvíe una lista de alertas que se presentan en ServiceNow con un identificador de incidente y una breve descripción de cada alerta.<br /><br />**Defender for IoT Application** (Aplicación Defender para IoT): reenvíe la información de alerta completa, lo que incluye los detalles del sensor, el motor y las direcciones de origen y destino. La información se reenvía a Defender para IoT en la aplicación de ServiceNow. |

1. Seleccione **SAVE** (GUARDAR). 

Las alertas de Defender para IoT ahora aparecerán en ServiceNow como incidentes.

## <a name="create-access-tokens-in-servicenow"></a>Creación de tokens de acceso en ServiceNow

Se necesita un token para permitir que ServiceNow se comunique con Defender para IoT.

Necesitará `Client ID` y `Client Secret` que escribió al crear las reglas de reenvío de Defender para IoT. Las reglas de reenvío de Defender para IoT reenvían información de las alertas a ServiceNow, como al configurar Defender para IoT para que inserte los atributos de dispositivo en las tablas de ServiceNow.

## <a name="send-defender-for-iot-device-attributes-to-servicenow"></a>Enviar atributos de dispositivo de Defender para IoT a ServiceNow

Configure Defender para IoT para insertar una amplia variedad de atributos de dispositivo en las tablas de ServiceNow. Para enviar atributos a ServiceNow, debe asignar la consola de administración local a una instancia de ServiceNow. De esta forma tiene la certeza de que la plataforma de Defender para IoT se puede comunicar con la instancia y autenticarla.

**Para agregar una instancia de ServiceNow**:

1. Inicie sesión en la consola de administración local de Azure Defender para IoT.

1. Seleccione **Configuración del sistema** y, después, **ServiceNow** en la sección de integración de la consola de administración local.

      :::image type="content" source="media/tutorial-servicenow/servicenow.png" alt-text="Captura de pantalla de la selección del botón ServiceNow.":::

1. Escriba los siguientes parámetros de sincronización en el cuadro de diálogo ServiceNow Sync (Sincronización de ServiceNow).

    :::image type="content" source="media/tutorial-servicenow/sync.png" alt-text="Captura de pantalla del cuadro de diálogo de sincronización de ServiceNow.":::

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

Compruebe que la consola de administración local está conectada a la instancia de ServiceNow. Para ello, debe mirar la fecha de Last Sync (Última sincronización).

:::image type="content" source="media/tutorial-servicenow/sync-confirmation.png" alt-text="Captura de pantalla de la comunicación que se produce al mirar la última sincronización.":::

## <a name="set-up-the-integrations-using-a-https-proxy"></a>Configuración de las integraciones mediante un proxy HTTPS

Cuando se configura la integración de Defender para IoT y ServiceNow, la consola de administración local y el servidor de ServiceNow se comunican mediante el puerto 443. Si el servidor de ServiceNow está detrás de un proxy, no se puede usar el puerto predeterminado.

Para que Defender para IoT admita un proxy HTTPS en la integración con ServiceNow, es preciso habilitar el cambio del puerto predeterminado que se usa para la integración.

**Para configurar el proxy**:

1. Edite las propiedades globales en la consola de administración local con el siguiente comando:

    ```bash
    sudo vim /var/cyberx/properties/global.properties
    ```

2. Agregue los siguientes parámetros:

    - `servicenow.http_proxy.enabled=1`

    - `servicenow.http_proxy.ip=1.179.148.9`

    - `servicenow.http_proxy.port=59125`

3. Seleccione **Guardar y salir**.

4. Restablezca las propiedades globales en la consola de administración local con el siguiente comando: 

    ```bash
    sudo monit restart all
    ```

Después de la configuración, todos los datos de ServiceNow se reenvían mediante el proxy configurado.

## <a name="view-defender-for-iot-detections-in-servicenow"></a>Visualización de las detecciones de Defender para IoT en ServiceNow

En este artículo se describen los atributos de dispositivo y la información de alertas que se presenta en ServiceNow.

**Para ver los atributos de dispositivo**:

1. Inicie sesión en ServiceNow.

2. Vaya a la **plataforma de CyberX**.

3. Vaya a **Inventory** (Inventario) o a **Alert** (Alerta).

   [:::image type="content" source="media/tutorial-servicenow/alert-list.png" alt-text="Captura de pantalla del inventario o alerta.":::](media/tutorial-servicenow/alert-list.png#lightbox)

## <a name="view-connected-devices"></a>Ver los dispositivos conectados

Para ver los dispositivos conectados:

1. Seleccione un dispositivo y, después, seleccione la **aplicación** que se muestra para ese dispositivo.

    :::image type="content" source="media/tutorial-servicenow/appliance.png" alt-text="Captura de pantalla de la aplicación deseada en la lista.":::

1. En el cuadro de diálogo **Device Details** (Detalles del dispositivo), seleccione **Connected Devices** (Dispositivos conectados).

## <a name="clean-up-resources"></a>Limpieza de recursos

No hay recursos para limpiar.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a empezar a trabajar con la integración de ServiceNow. Continúe para obtener información sobre la [integración de Cisco](./integration-forescout.md).

> [!div class="nextstepaction"]
> [Botón de pasos siguientes](./integration-forescout.md)