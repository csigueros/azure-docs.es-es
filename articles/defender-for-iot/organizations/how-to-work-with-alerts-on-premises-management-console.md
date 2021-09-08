---
title: Uso de alertas en la consola de administración local
description: Use la consola de administración local para obtener una vista empresarial de las amenazas recientes de la red y comprender mejor cómo las controlan los usuarios del sensor.
ms.date: 07/13/2021
ms.topic: how-to
ms.openlocfilehash: 2766776083eaeb96f6a1d577b160d0cdbe9eb3d0
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2021
ms.locfileid: "114466257"
---
# <a name="work-with-alerts-on-the-on-premises-management-console"></a>Uso de alertas en la consola de administración local 

Puede realizar las acciones siguientes desde la ventana **Alertas** de la consola de administración:

- Trabajar con filtros de alertas

- Trabajar con contadores de alertas

- Consultar la información de las alertas

- Administrar eventos de alerta

- Crear reglas de exclusión de alertas

- Desencadenar reglas de exclusión de alertas desde sistemas externos

- Acelerar el flujo de trabajo de incidentes con grupos de alertas

## <a name="view-alerts-in-the-on-premises-management-console"></a>Consultar alertas en la consola de administración local

La consola de administración local agrega alertas a partir de los sensores conectados. Esto ofrece una vista empresarial de las amenazas recientes de la red y ayuda a comprender mejor cómo las controlan los usuarios del sensor.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alerts-with-samples.png" alt-text="Captura de pantalla de la ventana Alertas":::

### <a name="work-with-alert-filters"></a>Trabajar con filtros de alertas

La ventana **Alertas** muestra las alertas generadas por los sensores conectados a la consola de administración local. Puede ver todas las alertas de los sensores conectados o presentar las alertas enviadas desde uno de estos orígenes específicos:

- Sitio

- Zona

- Dispositivo

- Sensor

Seleccione **Borrar filtros** para ver todas las alertas.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/clear-filters.png" alt-text="Seleccione Borrar filtros para borrar los filtros":::.

### <a name="work-with-alert-counters"></a>Trabajar con contadores de alertas

Los contadores de alertas proporcionan un desglose de las alertas por gravedad y estado confirmado.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/number-of-alerts.png" alt-text="El contador de alertas muestra el número de alertas que tiene":::.

Los siguientes niveles de gravedad aparecen en el contador de alertas:

- **Crítico**: indica un ataque malintencionado que debe administrarse inmediatamente.

- **Grave**: indica una amenaza de seguridad que es importante solucionar.

- **Leve**: indica alguna desviación del comportamiento de línea de base que puede contener una amenaza de seguridad.

- **Advertencia**: indica alguna desviación del comportamiento de línea de base sin amenazas de seguridad.

Los niveles de gravedad están predefinidos.

Puede ajustar el contador para proporcionar números basados en alertas confirmadas y no confirmadas. Las alertas no confirmadas se desencadenaron en los sensores de Defender para IoT, pero los operadores del sensor todavía no la han revisado.

Cuando la opción **Show Acknowledged Alerts** (Mostrar alertas confirmadas) está seleccionada, todas las alertas confirmadas aparecen en la ventana **Alertas**.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/show-acknowledged-alerts.png" alt-text="Ver las alertas confirmadas":::.

### <a name="view-alert-information"></a>Consultar la información de las alertas

La alerta presenta la información siguiente:

- Un resumen del evento de alerta.

- Gravedad de la alerta.

- Un vínculo a la alerta en el sensor que la detectó.

- Un UUID de alerta. El UUID está formado por el id. de la alerta que está asociado al evento de alerta detectado en el sensor, separado por un guion y seguido por un número de identificación del sistema único.

**UUID de alerta en la consola de administración local**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/unauthorized-internet-connectivity.png" alt-text="Un dispositivo está conectado, pero no autorizado":::.

**Id. de alerta del sensor**

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/internet-connectivity-alert.png" alt-text="Id. de alerta del sensor":::

El uso de UUID garantiza que cada alerta mostrada en la consola de administración local se pueda buscar e identificar mediante un número único. Esto es necesario porque las alertas generadas por varios sensores pueden crear el mismo identificador de alerta.

> [!NOTE]
> De forma predeterminada, los UUID se muestran en los siguientes sistemas asociados cuando se definen las reglas de reenvío: ArcSight, servidores de Syslog, QRadar, Sentinel y NetWitness. No es necesario realizar ninguna configuración.

**Para consultar la información de las alertas**:

- En la lista de alertas, seleccione una alerta.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-information.png" alt-text="Captura de pantalla de la información sobre la alerta":::

**Para consultar la alerta en el sensor**:

- Seleccione **OPEN SENSOR** (Abrir sensor) en la alerta.

**Para ver los dispositivos en un mapa de zonas**:

- Para ver el mapa del dispositivo con el foco en el dispositivo de la alerta y en todos los dispositivos conectados a él, seleccione **MOSTRAR DISPOSITIVOS**.

## <a name="manage-alert-events"></a>Administrar eventos de alerta

Hay varias opciones disponibles para administrar los eventos de alerta desde la consola de administración local.

- Conozca o confirme los eventos de alerta. Seleccione **Learn & Acknowledge** (Conocer y confirmar) para obtener información sobre todos los eventos de alerta que se pueden autorizar y para confirmar todos los eventos de alerta que no se han confirmado actualmente.

  :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/learn-and-acknowledge.png" alt-text="Seleccione Learn & Acknowledge (Conocer y confirmar) para obtener toda la información":::.

- Desactive y reactive los eventos de alerta.

Para más información sobre el aprendizaje, el reconocimiento y el silenciamiento de eventos de alerta, vea el artículo [Administrar eventos de alerta](how-to-manage-the-alert-event.md) del sensor.

## <a name="export-alert-information"></a>Exportación de la información de alertas

Exporte la información de alertas a un archivo .csv. Puede exportar la información de todas las alertas detectadas o puede exportar la información en función de la vista filtrada. Se exporta la siguiente información:

- Dirección de origen
- Dirección de destino
- Título de la alerta
- Gravedad de la alerta
- Mensaje de alerta
- Información adicional
- Estado confirmado
- Disponibilidad de PCAP

**Para exportar alertas**:

1. Seleccione **Alertas** en el menú lateral.

1. Selecciona **Export** (Exportar).

1. Seleccione **Exportar alertas extendidas** para exportar información de alertas de filas independientes para cada alerta que abarque varios dispositivos. Cuando se seleccione esta opción, el archivo .csv creará una fila duplicada de la alerta con los elementos únicos de cada fila. El uso de esta opción facilita la investigación de eventos de alertas exportadas.  

## <a name="create-forwarding-rules"></a>Creación de reglas de reenvío

**Para crear una regla de reenvío en la consola de administración**:

1. Inicie sesión en el sensor.

1. Seleccione **Reenvío** en el menú lateral.

1. Seleccione el icono :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/plus-add-icon.png" border="false":::.

1. En la ventana Crear regla de reenvío, escriba un nombre para la regla

    :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/management-console-create-forwarding-rule.png" alt-text="Escriba un nombre descriptivo en el campo de la ventana Crear regla de reenvío.":::

   Defina los criterios por los que se desencadenará una regla de reenvío. El uso de criterios en las reglas de reenvío ayuda a identificar y administrar el volumen de información que se envía del sensor a los sistemas externos.

1. Seleccione un nivel de gravedad en el menú desplegable.

    este es el incidente mínimo que se reenviará, en términos de nivel de gravedad. Por ejemplo, si selecciona **leve**, se reenviarán las alertas de gravedad leve y todas las alertas por encima de este nivel de gravedad. Los niveles están predefinidos.

1. Seleccione los protocolos que se aplicarán.

    la regla de reenvío solo se activa si el tráfico detectado se estaba ejecutando a través de protocolos específicos. Seleccione los protocolos necesarios en la lista desplegable o selecciónelos todos.

1. Seleccione a qué motores se debe aplicar la regla.

    
   Seleccione los motores necesarios o selecciónelos todos. Se enviarán las alertas de los motores seleccionados. 

1. Active la casilla si desea que la regla de reenvío informe las notificaciones del sistema.
  
1. Active la casilla si desea que la regla de reenvío informe las notificaciones de alerta.

1. Seleccione **Agregar** para agregar una acción que se aplicará. Rellene los parámetros necesarios para la acción seleccionada.

    Las acciones de regla de reenvío indican al sensor que reenvíe la información de alertas a los proveedores o servidores asociados. Puede crear varias acciones para cada regla de reenvío.

1. Agregue otra acción si lo desea.

1. Seleccione **Guardar**.

Puede obtener más información [Acerca de la información de alertas reenviada](how-to-forward-alert-information-to-partners.md#about-forwarded-alert-information). También puede [Probar reglas de reenvío](how-to-forward-alert-information-to-partners.md#test-forwarding-rules), o [Editar y eliminar reglas de reenvío](how-to-forward-alert-information-to-partners.md#edit-and-delete-forwarding-rules). También puede obtener más información sobre [Reglas de reenvío y reglas de exclusión de alertas](how-to-forward-alert-information-to-partners.md#forwarding-rules-and-alert-exclusion-rules).

## <a name="create-alert-exclusion-rules"></a>Crear reglas de exclusión de alertas

Indique a Defender para IoT que ignore los desencadenadores de alertas en función de los siguientes parámetros:

- Zonas horarias y períodos de tiempo

- Dirección del dispositivo (IP, MAC y subred)

- Nombre de alerta

- Un sensor específico

Cree reglas de exclusión de alertas cuando desee que Defender para IoT omita la actividad que desencadenará una alerta.

Por ejemplo, si sabe que todos los dispositivos OT supervisados por un sensor específico van a someterse a los procedimientos de mantenimiento durante dos días, puede definir una regla de exclusión que indique a Defender para IoT que suprima las alertas detectadas por este sensor durante el período predefinido.

### <a name="alert-exclusion-logic"></a>Lógica de exclusión de alertas

La lógica de la regla de alertas se basa en `AND`. Esto significa que se desencadenará una alerta solo cuando se cumplan todas las condiciones de la regla.

Si no se define ninguna condición de regla, la condición incluirá todas las opciones. Por ejemplo, si no incluye el nombre de un sensor en la regla, se aplicará a todos los sensores.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-v2.png" alt-text="Captura de pantalla de la vista Crear una regla de exclusión":::

Los resúmenes de las reglas aparecen en la ventana **Regla de exclusión**.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/exclusion-summary-v2.png" alt-text="Captura de pantalla de la vista de resumen de la regla de exclusión":::

Además de usar reglas de exclusión, puede suprimir las alertas mediante su desactivación.

### <a name="create-exclusion-rules"></a>Creación de reglas de exclusión

**Para crear reglas de exclusión**:

1. En el panel izquierdo de la consola de administración local, seleccione **Alert Exclusion** (Exclusión de alertas). Defina una nueva regla de exclusión; para ello, seleccione el icono **Agregar** :::image type="icon" source="media/how-to-work-with-alerts-on-premises-management-console/add-icon.png" border="false"::: en la esquina superior derecha de la ventana que se abre. Se abre el cuadro de diálogo **Crear una regla de exclusión**.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/create-alert-exclusion-view.png" alt-text="Cree una exclusión de alertas rellenando la información aquí":::.

1. Escriba el nombre de una regla en el campo **Nombre**. El nombre no puede contener comillas (`"`).

1. En la sección **By Time Zone/Period** (Por zona horaria/Período), especifique un período de tiempo dentro de una zona horaria específica. Use esta característica cuando se cree una regla de exclusión para un período de tiempo específico en una zona horaria, pero debe implementarse al mismo tiempo en otras zonas horarias. Por ejemplo, puede que necesite aplicar una regla de exclusión entre las 8:00 a. m y las 10:00 a. m. en tres zonas horarias diferentes. En este caso, cree tres reglas de exclusión independientes que usen el mismo período de tiempo y la zona horaria correspondiente.

1. Seleccione **AGREGAR**. Durante el período de exclusión, no se crea ninguna alerta en los sensores conectados.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/by-the-time-period.png" alt-text="Captura de pantalla de la vista por período de tiempo":::

1. En la sección **By Device Address** (Por dirección de dispositivo), defina lo siguiente:

  - Dirección IP del dispositivo, dirección MAC o dirección de subred que desea excluir.

  - Dirección del tráfico para los dispositivos, el origen y el destino excluidos.

1. Seleccione **AGREGAR**.

1. En la sección **By Alert Title** (Por título de alerta), empiece a escribir el título de la alerta. En la lista desplegable, seleccione el título o los títulos de la alerta que se van a excluir.

   :::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/alert-title.png" alt-text="Captura de pantalla de la vista por título de alerta":::

1. Seleccione **AGREGAR**.

1. En la sección **By Sensor Name** (Por nombre de sensor), empiece a escribir el nombre del sensor. En la lista desplegable, seleccione el sensor o los sensores que desea excluir.

1. Seleccione **AGREGAR**.

1. Seleccione **SAVE** (GUARDAR). La nueva regla aparece en la lista de reglas.

Puede suprimir las alertas mediante su desactivación o la creación de reglas de exclusión de alertas. En esta sección se describen los posibles casos de uso de ambas características.

- **Regla de exclusión**. Escriba una regla de exclusión cuando:

  - Sepa de antemano el tiempo durante el cual desea excluir el evento de la base de datos. Por ejemplo, sabe que el escenario detectado en un determinado sensor desencadenará alertas irrelevantes. Por ejemplo, va a realizar operaciones de mantenimiento en los PLC de la organización en un sitio específico y desea suprimir las alertas relacionadas con los PLC para este sitio.

  - Quiere que Defender para IoT ignore eventos durante un intervalo de tiempo específico (para tareas de mantenimiento del sistema).

  - Quiere omitir los eventos de una subred específica.

  - Desea controlar los eventos de alerta generados por varios sensores con una regla.

  - No desea realizar el seguimiento de la exclusión de alertas como un evento en el registro de eventos.

- **Desactivación**. Desactive una alerta cuando se cumplan estas condiciones:

  - Los elementos que se deben desactivar no están planeados. No sabe de antemano qué eventos serán irrelevantes.

  - Desea suprimir la alerta en la ventana **Alertas**, pero todavía desea realizar su seguimiento en el registro de eventos.

  - Quiere omitir los eventos de un canal específico.

### <a name="trigger-alert-exclusion-rules-from-external-systems"></a>Desencadenar reglas de exclusión de alertas desde sistemas externos

Desencadene reglas de exclusión de alertas desde sistemas externos. Por ejemplo, administre reglas de exclusión de sistemas de vales de empresa o sistemas que administran procesos de mantenimiento de red.

Defina los sensores, los motores, la hora de inicio y la hora de finalización que desea aplicar a la regla. Para más información, vea [API del sensor y la consola de administración de Defender para IoT](references-work-with-defender-for-iot-apis.md).

Las reglas que se crean con la API aparecen en la ventana **Regla de exclusión** como RO.

:::image type="content" source="media/how-to-work-with-alerts-on-premises-management-console/edit-exclusion-rule-screen.png" alt-text="Captura de pantalla de la vista de edición de la regla de exclusión":::

## <a name="next-steps"></a>Pasos siguientes

[Uso de alertas en el sensor](how-to-work-with-alerts-on-your-sensor.md).
Revise las [Alertas de motores de Defender para IoT](alert-engine-messages.md).