---
title: Uso de la asignación de dispositivos sensores
description: La asignación de dispositivos ofrece una representación gráfica de los dispositivos de red detectados. Use la asignación para analizar y administrar la información de los dispositivos y los segmentos de red, y generar informes.
ms.date: 1/7/2021
ms.topic: how-to
ms.openlocfilehash: f7579cbca618baef404236556993c9831dd84bdf
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113019065"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Investigación de las detecciones de sensores en la asignación de dispositivos

La asignación de dispositivos ofrece una representación gráfica de los dispositivos de red detectados. Use la asignación para hacer lo siguiente:

  - Recuperar, analizar y administrar la información del dispositivo.

  - Analizar segmentos de red como, por ejemplo, grupos específicos de interés o capas de Purdue.

  - Generar informes como, por ejemplo, exportar detalles de dispositivos y resúmenes.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Captura de pantalla de la asignación de dispositivos.":::

Para acceder a la asignación:

  - Seleccione **Device Map** (Asignación de dispositivos) en la pantalla principal de la consola.

## <a name="map-search-and-layout-tools"></a>Herramientas de diseño y búsqueda de la asignación

Para trabajar en la asignación se utilizan las siguientes herramientas.

El rol de usuario determina las herramientas que están disponibles en la ventana de asignación de dispositivos. Vea [Creación y administración de usuarios](how-to-create-and-manage-users.md) para obtener más información sobre los roles de usuario.

| Símbolo | Descripción |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Buscar un dispositivo específico por dirección IP o dirección MAC. Escriba la dirección IP o la dirección MAC en el cuadro de texto. La asignación muestra el dispositivo que ha buscado con dispositivos conectados a él. |
| Resaltado y filtros de grupo <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Captura de pantalla de resaltado y filtros de grupo."::: | Filtrar o resaltar la asignación en función de grupos de dispositivos predeterminados y personalizados. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Vista Contraer de TI, para habilitar una vista centrada en los dispositivos OT y para agrupar los dispositivos de TI.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Mantener la disposición actual de los dispositivos en la asignación. Por ejemplo, si arrastra dispositivos a nuevas ubicaciones en la asignación, los dispositivos permanecerán en estas ubicaciones al salir de la asignación. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Ajustar a la pantalla |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | -Ver la capa de Purdue identificada para este dispositivo, que incluye automático, control de procesos, supervisor y empresa <br /> -Ver las conexiones entre dispositivos.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Mostrar u ocultar entre difusión y multidifusión. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Filtrar los dispositivos de la asignación según la hora en que se comunicaron por última vez con otros dispositivos. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="notifications" border="false"::: | Ver notificaciones sobre un dispositivo. Por ejemplo, si se detectó una nueva dirección IP para un dispositivo con una dirección MAC existente |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Exportarar" border="false"::: | Exportar o importar información del dispositivo. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="properties" border="false"::: | Ver las propiedades básicas del dispositivo de los dispositivos seleccionados. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Acercar" border="false"::: o :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="Alejar" border="false"::: | Acercar o alejar dispositivos en la asignación. |

## <a name="view-ot-elements-only"></a>Solo vista de elementos OT

De forma predeterminada, los dispositivos de TI se agregan automáticamente por subred, de modo que la vista de asignación se centra en las redes ICS y OT. La presentación de los elementos de red de TI está contraída hasta un mínimo, lo que reduce el número total de dispositivos que se presentan en la asignación y proporciona una imagen clara de los elementos de red ICS y OT.

Cada subred se presenta como una sola entidad en la asignación de dispositivos, lo que incluye una funcionalidad interactiva de contracción y expansión para examinar los detalles de una subred de TI y volver.

En la ilustración siguiente se muestra una subred de TI contraída con 27 elementos de red de TI.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="subred de TI contraída con 27 elementos de red de TI":::

Para habilitar la capacidad de contracción de redes de TI:

- En la ventana **Configuración del sistema**, asegúrese de que la capacidad de alternancia de agrupación de redes de TI está habilitada.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Ventana Configuración del sistema":::

Para expandir una subred de TI:

1. Para diferenciar entre las redes OT y de TI, seleccione **Subredes** en la pantalla Configuración del sistema.

   > [!NOTE]
   > Se recomienda asignar a cada subred un nombre descriptivo que el usuario pueda identificar fácilmente con el fin de diferenciar entre redes OT y de TI.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Subnets Configuration"::: (Configuración de subredes)

2. En la ventana **Edit Subnets Configuration** (Editar configuración de subredes), desactive la casilla **ICS Subnet** (Subred ICS) de cada subred que quiera definir como subred de TI. Las subredes de TI aparecen contraídas en la asignación de dispositivos con las notificaciones de los dispositivos ICS (como un controlador o PLC) en las redes de TI.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Edit Subnets Configuration"::: (Editar configuración de subredes)

3. Para expandir la red de TI en la asignación, en la ventana Dispositivos, haga clic con el botón derecho en ella y seleccione **Expand Network** (Expandir red).

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Expanda la vista de la red.":::

4. Aparecerá un cuadro de confirmación que le notificará que el cambio de diseño no se puede rehacer.

5. Seleccione **Aceptar**. Los elementos de la subred de TI aparecen en la asignación.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK (CORRECTO)":::

Para contraer una subred de TI:

1.  En el panel izquierdo, seleccione **Dispositivos**.

2. En la ventana Dispositivos, seleccione el icono de contraer. El número en rojo indica cuántas subredes de TI expandidas aparecen actualmente en la asignación.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Ventana de dispositivo":::

3. Seleccione las subredes que quiere contraer o seleccione **Contraer todo**. La subred seleccionada aparece contraída en la asignación.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Contraer todo":::

El icono de contraer se actualiza con el número actualizado de subredes de TI expandidas.

## <a name="view-or-highlight-device-groups"></a>Visualización o resaltado de grupos de dispositivos

Puede personalizar la presentación de la asignación en función de grupos de dispositivos. Por ejemplo, los grupos de dispositivos asociados a un protocolo OT, una VLAN o subred específicos. Hay grupos predefinidos disponibles y se pueden crear grupos personalizados.

Ver grupos por:

  - **Resaltado:** resalte en azul los dispositivos que pertenecen a un grupo específico.

  - **Filtrado:** solo se muestran los dispositivos que pertenecen a un grupo específico.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Vista estándar del puerto":::

Están disponibles los siguientes grupos predefinidos:

| Nombre del grupo | Descripción |
|--|--|
| **Aplicaciones conocidas** | Dispositivos que usan puertos reservados, como TCP.  |
| **puertos no estándar (valor predeterminado)** | Dispositivos que usan puertos no estándar o puertos a los que no se les ha asignado un alias. |
| **OT protocols (default)** (Protocolos OT [valor predeterminado]) | Dispositivos que controlan el tráfico de OT conocido. |
| **Autorización (valor predeterminado)** | Dispositivos detectados en la red durante el proceso de aprendizaje o que se autorizaron oficialmente a la red. |
| **Device inventory filters** (Filtros de inventario de dispositivos) | Los dispositivos agrupados según los filtros se guardan en la tabla de inventario de dispositivos. |
| **Intervalos de sondeo** | Dispositivos agrupados por intervalos de sondeo. Los intervalos de sondeo se generan automáticamente por canales cíclicos o períodos. Por ejemplo, 15,0 segundos, 3,0 segundos, 1,5 segundos o cualquier intervalo. Revisar esta información le ayudará a saber si los sistemas se sondean con demasiada rapidez o lentitud. |
| **Programar** | Estaciones de ingeniería y máquinas programadas. |
| **Subredes** | Dispositivos que pertenecen a una subred específica. |
| **VLAN** | Dispositivos asociados a un identificador de VLAN concreto. |
| **Cross subnet connections** (Conexiones entre subredes) | Dispositivos que se comunican desde una subred a otra subred. |
| **Pinned alerts** (Alertas ancladas) | Dispositivos para los que el usuario ha anclado una alerta. |
| **Attack vector simulations** (Simulaciones de vector de ataque) | Dispositivos vulnerables detectados en los informes de vector de ataque. Para ver estos dispositivos en la asignación, active la casilla **Display on Device Map** (Mostrar en la asignación de dispositivos) al generar el vector de ataque. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Add Attack Vector Simulations"::: (Agregar simulaciones de vector de ataque). |
| **Última visualización** | Dispositivos agrupados por el intervalo de tiempo durante el que se vieron por última vez, por ejemplo: una hora, seis horas, un día, siete días. |
| **Not In Active Directory** (No está en Active Directory) | Todos los dispositivos que no son de PLC y que no se comunican con la instancia de Active Directory. |

Para resaltar o filtrar dispositivos:

1. Seleccione **Device Map** (Asignación de dispositivos) en el menú lateral.

2. Seleccione el icono de filtro. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menú":::

3. En el panel Grupos, seleccione el grupo cuyos dispositivos quiere resaltar o filtrar.

4. Seleccione **Resaltar** o **Filtrar**. Alterne la misma selección para quitar el resaltado o el filtro.

## <a name="define-custom-groups"></a>Definición de grupos personalizados

Además de ver los grupos predefinidos, puede definir grupos personalizados. Los grupos aparecen en los informes de asignación de dispositivos, de inventario de dispositivos y de minería de datos.

> [!NOTE]
> También puede crear grupos desde el inventario de dispositivos.

Para crear un grupo:

1. Seleccione **Dispositivos** en el menú lateral. Se muestra la asignación de dispositivos.

1. Seleccione :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Group Setting"::: (Configuración de grupos) para mostrar la configuración de grupos.

1. Seleccione :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="Grupos"::: para crear un grupo personalizado.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Pantalla de creación de un grupo personalizado":::

1. Agregue el nombre del grupo, use hasta 30 caracteres.

1. Seleccione los dispositivos pertinentes, tal y como se indica a continuación:

   - Para agregar los dispositivos desde este menú, selecciónelos de la lista (seleccionar con el botón de flecha).<br /> O bien, <br /> 
   - Para agregar los dispositivos desde este menú, cópielos de un grupo seleccionado (seleccionar con el botón de flecha).

1. Seleccione **Agregar grupo** para agregar grupos existentes a grupos personalizados.

### <a name="add-devices-to-a-custom-group"></a>Adición de dispositivos a un grupo personalizado

Puede agregar dispositivos a un grupo personalizado o crear un grupo personalizado y el dispositivo.

1. Haga clic con el botón derecho en uno o varios dispositivos de la asignación.

1. Seleccione **Agregar al grupo**.

1. Escriba un nombre de grupo en el campo Grupo y seleccione +. Aparece el nuevo grupo. Si el grupo ya existe, se agregará al grupo personalizado existente.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Nombre de grupo":::

1. Agregue dispositivos a un grupo repitiendo los pasos del 1 al 3.

## <a name="map-zoom-views"></a>Vistas de zoom de asignación

El uso de vistas de asignación ayuda a agilizar el análisis forense al analizar redes de gran tamaño.

Se pueden mostrar tres vistas de detalles de dispositivos:

  - [Vista general](#birds-eye-view)

  - [Vista de conexiones y tipos de dispositivo](#device-type-and-connection-view)

  - [Vista detallada](#detailed-view)

### <a name="birds-eye-view"></a>Vista general

Esta vista proporciona una vista general de los dispositivos representados tal y como se indica a continuación:

  - Los puntos rojos indican que los dispositivos tienen alertas

  - Los puntos en forma de estrella indican los dispositivos marcados como importantes

  - Los puntos negros indican que los dispositivos no tienen alertas

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Vista general":::

### <a name="device-type-and-connection-view"></a>Vista de conexiones y tipos de dispositivo 

Esta vista presenta los dispositivos representados como iconos en la asignación para resaltar dispositivos con alertas, tipos de dispositivo y dispositivos conectados.

  - Los dispositivos con alertas se muestran con un anillo rojo

  - Los dispositivos sin alertas se muestran con un anillo gris

  - Los dispositivos mostrados como una estrella se marcaron como importantes

El icono de tipo de dispositivo se muestra con los dispositivos conectados.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="vista de conexiones":::

### <a name="detailed-view"></a>Vista detallada 

En la vista detallada se presentan los dispositivos y los indicadores y las etiquetas de dispositivo con la siguiente información:

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Vista detallada":::

### <a name="control-the-zoom-view"></a>Control de la vista de zoom

La vista de asignación que se muestra depende del nivel de zoom de la asignación. El cambio entre las vistas de asignación se realiza cambiando los niveles de zoom.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="Control de la vista de zoom":::

### <a name="enable-simplified-zoom-views"></a>Habilitación de vistas de zoom simplificadas

Los administradores que quieren que los analistas de seguridad y los usuarios de solo lectura accedan a la vista general y la vista de conexiones y tipos de dispositivo, deben habilitar la opción de vista simplificada.

Para habilitar las vistas de asignación simplificadas:

  - Seleccione **Configuración del sistema** y luego active la opción **Simplified Map View** (Vista de asignación simplificada).

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Simplify map view"::: (Vista de asignación simplificada)

## <a name="learn-more-about-devices"></a>Más información sobre los dispositivos

Hay disponible una amplia gama de herramientas para obtener más información sobre los dispositivos de la asignación de dispositivos:

- [Etiquetas e indicadores de dispositivos](#device-labels-and-indicators)

- [Vistas rápidas del dispositivo](#device-quick-views)

- [Visualización y administración de propiedades del dispositivo](#view-and-manage-device-properties)

- [Visualización de tipos de dispositivo](#view-device-types)

- [Backplane](#backplane-properties)

- [Visualización de una escala de tiempo de eventos del dispositivo](#view-a-timeline-of-events-for-the-device)

- [Análisis de los detalles y los cambios de programación](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Etiquetas e indicadores de dispositivos

Las siguientes etiquetas e indicadores pueden aparecer en los dispositivos de la asignación:

| Etiqueta de dispositivo | Descripción |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="Nombre de host IP"::: | Nombre de host de la dirección IP y dirección IP, o direcciones de subred |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Número de alertas"::: | Número de alertas asociadas al dispositivo |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Icono de tipo de dispositivo, por ejemplo, almacenamiento, PLC o historiador. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="dispositivos agrupados"::: | Número de dispositivos agrupados en una subred de una red de TI. En este ejemplo, "8". |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="período de aprendizaje del dispositivo"::: | Un dispositivo que se detectó después del período de aprendizaje y no estaba autorizado como dispositivo de red. |
| Línea sólida | Conexión lógica entre dispositivos |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Nuevo dispositivo"::: | Nuevo dispositivo detectado una vez completado el aprendizaje. |

### <a name="device-quick-views"></a>Vistas rápidas del dispositivo

Acceda a las propiedades y conexiones del dispositivo desde la asignación.

Para abrir el menú de propiedades rápidas:

  - Seleccione el menú de propiedades rápidas :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="menú de propiedades rápidas":::.

#### <a name="quick-device-properties"></a>Propiedades rápidas del dispositivo

Seleccione un dispositivo o varios mientras la pantalla de propiedades rápidas está abierta para ver los aspectos destacados de dichos dispositivos:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Propiedades rápidas del dispositivo":::

#### <a name="quick-connection-properties"></a>Propiedades de conexión rápidas

Seleccione una conexión mientras la pantalla de propiedades rápidas está abierta para ver los protocolos que se usan en esta conexión y cuándo se vieron por última vez:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Propiedades de conexión rápidas":::

## <a name="view-and-manage-device-properties"></a>Visualización y administración de propiedades del dispositivo

Puede ver las propiedades del dispositivo de cada dispositivo mostrado en la asignación. Por ejemplo, el nombre, el tipo o el sistema operativo del dispositivo o el firmware o proveedor.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Visualización y administración de propiedades del dispositivo":::

La siguiente información se puede actualizar manualmente. La información especificada manualmente reemplazará la información detectada por Defender para IoT.

  - Nombre

  - Tipo

  - SO

  - Capa de Purdue

  - Descripción

| Elemento | Descripción |
|--|--|
| Información básica | Información básica que es necesaria. |
| Nombre | El nombre de dispositivo. <br /> De forma predeterminada, el sensor detecta el nombre del dispositivo tal y como se definió en la red. Por ejemplo, un nombre definido en el servidor DNS. <br /> Si no se definieron estos nombres, la dirección IP del dispositivo aparece en este campo. <br /> Puede cambiar manualmente el nombre de un dispositivo. Asigne a los dispositivos nombres descriptivos que reflejen su funcionalidad. |
| Tipo | Tipo de dispositivo detectado por el sensor. <br /> Para obtener más información, vea [Visualización de tipos de dispositivo](#view-device-types). |
| Vendor | Proveedor del dispositivo. Esto viene determinado por los caracteres iniciales de la dirección MAC del dispositivo. Este campo es de solo lectura. |
| Sistema operativo | Sistema operativo de dispositivo detectado por el sensor. |
| Capa de Purdue | Capa de Purdue identificada por el sensor para este dispositivo, que incluye: <br /> - Automático <br /> - Control de procesos <br /> - Supervisor <br /> - Enterprise |
| Descripción | Campo de texto estático. <br /> Agregue más información sobre el dispositivo. |
| Atributos | Cualquier información adicional que se haya detectado sobre el dispositivo durante el período de aprendizaje y que no pertenezca a otras categorías aparece en la sección de atributos. <br /> Esta información es de solo lectura. |
| Configuración | Puede cambiar manualmente la configuración del dispositivo para evitar falsos positivos: <br /> - **Authorized Device** (Dispositivo autorizado): Durante el período de aprendizaje, todos los dispositivos detectados en la red se identifican como dispositivos autorizados. Cuando un dispositivo se detecta después del período de aprendizaje, aparece de forma predeterminada como dispositivo no autorizado. Puede cambiar esta información manualmente. <br /> - **Known as Scanner** (Conocido como escáner): Habilite esta opción si sabe que este dispositivo se conoce como escáner y no es necesario avisarle de ello. <br /> - **Programming Device** (Dispositivo de programación): Habilite esta opción si sabe que este dispositivo se conoce como dispositivo de programación y se usa para realizar cambios en la programación. La identificación como un dispositivo de programación impedirá que se produzcan alertas de los cambios de programación procedentes de este recurso. |
| Grupos personalizados | Grupos personalizados en la asignación de dispositivos en la que participa este dispositivo. |
| State | Estado de autorización y seguridad del dispositivo: <br /> - El estado es `Secured` cuando no hay ninguna alerta <br /> - Cuando hay alertas sobre el dispositivo, se muestra el número de alertas <br /> - El estado `Unauthorized` se muestra para los dispositivos que se agregaron a la red después del período de aprendizaje. Puede definir manualmente el dispositivo como `Authorized Device` en la configuración <br /> - En caso de que la dirección de este dispositivo se defina como dirección dinámica, se agrega `DHCP` al estado. |


| Red | Descripción |
|--|--|
| Interfaces | Interfaces de dispositivo. Campo de solo lectura. |
| Protocolos | Protocolos utilizados por el dispositivo. Campo de solo lectura. |
| Firmware | Si hay información de backplane disponible, no se mostrará la información de firmware. |
| Dirección | Dirección IP del dispositivo. |
| Serie | Número de serie del dispositivo. |
| Module Address (Dirección del módulo) | Modelo del dispositivo e identificador o número de ranura. |
| Modelo | Número de modelo del dispositivo. |
| Versión de firmware | Número de versión del firmware. |

Para ver la información del dispositivo, siga estos pasos:

1. Seleccione **Dispositivos** en el menú lateral.

2. Haga clic con el botón derecho en un dispositivo y seleccione **Ver propiedades**. Se muestra la ventana Propiedades del dispositivo.

3. Seleccione la alerta necesaria en la parte inferior de esta ventana para ver información detallada sobre las alertas de este dispositivo.

### <a name="view-device-types"></a>Visualización de tipos de dispositivo

El sensor identifica automáticamente el tipo de dispositivo durante el proceso de detección de dispositivos. Puede cambiar el tipo manualmente.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Visualización de tipos de dispositivo":::

En la tabla siguiente se presentan todos los tipos del sistema:

| Category | Tipo de dispositivo |
|--|--|
| ICS | Estación de ingeniería <br /> PLC <br />Historiador <br />HMI <br />IED <br />Controlador de DCS <br />RTU <br />Sistema de embalaje industrial <br />Escala industrial <br />Industrial Robot <br />Slot <br />Medidor <br />Unidad de frecuencia variable  <br />Controlador de robot <br />Servounidad <br />Dispositivo neumático <br />Marquesina |
| IT | Controlador de dominio <br />Servidor de base de datos <br />Estación de trabajo <br />Servidor <br />Estación terminal <br />Storage <br />Smart Phone <br />Tablet <br />Servidor de copia de seguridad |
| IoT | Cámara IP <br />Impresora  <br />Reloj de marcaje <br />Cajero automático <br />Smart TV <br />Consola de juego <br />DVR <br />Panel de control de puerta <br />HVAC <br />Thermostat <br />Alarma de incendios <br />Luz inteligente <br />Conmutador inteligente <br />Detector de incendios <br />Teléfono IP <br />Sistema de alarma <br />Sirena de alarma <br />Detector de movimientos <br />Ascensor <br />Sensor de humedad <br />Escáner de código de barras <br />Sistema de alimentación ininterrumpida <br />Sistema contador de personas <br />Intercom <br />Torniquete |
| Red | Punto de acceso inalámbrico <br />Enrutador <br />Switch <br />Firewall <br />VPN Gateway <br />Servidor NTP <br />Wifi Pineapple <br />Ubicación física <br />Adaptador de E/S <br /> Convertidor de protocolos |

Para ver la información del dispositivo, siga estos pasos:

1.  Seleccione **Dispositivos** en el menú lateral.

2. Haga clic con el botón derecho en un dispositivo y seleccione **Ver propiedades**. Se muestra la ventana Propiedades del dispositivo.

3. Seleccione la alerta necesaria para ver información detallada sobre las alertas de este dispositivo.

### <a name="backplane-properties"></a>Propiedades de backplane

Si un PLC contiene varios módulos separados en bastidores y ranuras, puede que las características varíen entre las tarjetas del módulo. Por ejemplo, si la dirección IP y la dirección MAC son iguales, es posible que el firmware sea diferente.

Puede usar la opción de backplane para revisar varios controladores/tarjetas y sus dispositivos anidados como una sola entidad con diversas definiciones. Cada ranura de la vista de backplane representa los dispositivos subyacentes: los dispositivos que se detectaron detrás de ella.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Propiedades de backplane":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Propiedades del dispositivo de backplane":::

Un backplane puede contener hasta 30 tarjetas de controlador y hasta 30 unidades de bastidor. El número total de dispositivos incluidos en los distintos niveles puede ser de hasta 200 dispositivos.

El panel Backplane se muestra en la ventana Propiedades del dispositivo cuando se detectan detalles del backplane.

Cada ranura aparece con el número de dispositivos subyacentes y el icono que muestra el tipo de módulo.

| Icono | Tipo del módulo |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Fuente de alimentación"::: | Fuente de alimentación |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="E/S analógica"::: | E/S analógica |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Adaptador de comunicaciones"::: | Adaptador de comunicaciones |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="E/S digital"::: | E/S digital |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="CPU"::: | CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Genérico"::: | Genérico |

Al seleccionar una ranura, aparecen los detalles de la ranura:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="selección de una ranura":::

Para ver los dispositivos subyacentes situados detrás de la ranura, seleccione **VIEW ON MAP** (VER EN LA ASIGNACIÓN). La ranura se presenta en la asignación de dispositivos con todos los módulos y dispositivos subyacentes conectados a ella.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="VIEW ON MAP"::: (VER EN LA ASIGNACIÓN)

## <a name="view-a-timeline-of-events-for-the-device"></a>Visualización de una escala de tiempo de eventos del dispositivo

Vea una escala de tiempo de eventos asociados al dispositivo.

Para ver la escala de tiempo:

1. Haga clic con el botón derecho en un dispositivo de la asignación.

2. Seleccione **Mostrar eventos**. Se abre la ventana Event Timeline (Escala de tiempo del evento) con información sobre los eventos detectados para el dispositivo seleccionado.

Vea [Escala de tiempo del evento](#event-timeline) para obtener más información.

## <a name="analyze-programming-details-and-changes"></a>Análisis de los detalles y los cambios de programación

Mejore los análisis forenses mostrando los eventos de programación que se llevan a cabo en los dispositivos de red y analizando los cambios en el código. Esta información le ayuda a detectar actividades de programación sospechosas, por ejemplo:

  - Human error (Error humano): un ingeniero programa el dispositivo equivocado.

  - Corrupted programming automation (Automatización de programación dañada): la programación se realiza erróneamente debido a un error de automatización.

  - Hacked systems (Sistemas pirateados): usuarios no autorizados han iniciado sesión en un dispositivo de programación.

Puede mostrar un dispositivo programado y desplazarse por los distintos cambios de programación que llevan a cabo otros dispositivos.

Vea el código que el dispositivo de programación ha agregado, modificado, quitado o recargado. Busque los cambios de programación por tipos de archivo, fechas u horas de interés.

### <a name="when-to-review-programming-activity"></a>Cuándo revisar la actividad de programación 

Es posible que tenga que revisar la actividad de programación:

  - Después de ver una alerta relacionada con programación no autorizada

  - Después de una actualización planeada de los controladores

  - Cuando un proceso o una máquina no funciona correctamente (para ver quién realizó la última actualización y cuándo)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Registro de cambios de programación":::

Otras opciones le permitirán:

  - Marcar eventos de interés con una estrella.

  - Descargar un archivo *.txt con el código actual.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>Acerca de los eventos de programación autorizada y no autorizada 

Los eventos de programación no autorizada se llevan a cabo en dispositivos que no se han aprendido ni definido manualmente como dispositivos de programación. Los eventos de programación autorizada se llevan a cabo en dispositivos que se resolvieron o definieron manualmente como dispositivos de programación.

La ventana Programming Analysis (Análisis de programación) muestra los eventos de programación autorizada y no autorizada.

### <a name="accessing-programming-details-and-changes"></a>Acceso a los detalles y los cambios de programación

Acceda a la ventana Programming Analysis (Análisis de programación) desde:

- [Escala de tiempo del evento](#event-timeline)

- [Alertas de programación no autorizada](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Escala de tiempo del evento

Use la escala de tiempo del evento para mostrar una escala de tiempo de los eventos en los que se detectaron cambios de programación.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Vista de la escala de tiempo del evento.":::

### <a name="unauthorized-programming-alerts"></a>Alertas de programación no autorizada

Se desencadenan alertas cuando dispositivos de programación no autorizados llevan a cabo actividades de programación.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Alertas de programación no autorizada":::

> [!NOTE]
> También puede ver información de programación básica en la ventana Propiedades del dispositivo y el inventario de dispositivos.

### <a name="working-in-the-programming-timeline-window"></a>Uso de la ventana Programming Timeline (Escala de tiempo de programación)

En esta sección se describe cómo ver los archivos de programación y comparar versiones. Busque archivos específicos enviados a un dispositivo programado. Busque archivos por lo siguiente:

  - Date

  - Tipo de archivo

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="ventana de escala de tiempo de programación":::

|Tipo de escala de tiempo de programación | Descripción |
|--|--|
| Programmed Device (Dispositivo programado) | Proporciona detalles sobre el dispositivo programado, incluido el nombre de host y el archivo. |
| Eventos recientes | Muestra los 50 eventos más recientes detectados por el sensor. <br />Para resaltar un evento, mantenga el mouse sobre él y haga clic en la estrella. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> Se pueden ver los últimos 50 eventos. |
| Archivos | Muestra los archivos detectados durante la fecha seleccionada y el tamaño de archivo en el dispositivo programado. <br /> De forma predeterminada, el número máximo de archivos disponibles para mostrar por dispositivo es de 300. <br /> De forma predeterminada, el tamaño máximo de archivo para cada archivo es de 15 MB. |
| Estado de archivo :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | Las etiquetas de archivo indican el estado del archivo en el dispositivo e incluyen: <br /> **Agregado**: el archivo se agregó al punto de conexión en la fecha o la hora seleccionada. <br /> **Actualizado:** el archivo se actualizó en la fecha o la hora seleccionada. <br /> **Eliminado**: este archivo se ha quitado. <br /> **Sin etiqueta**: el archivo no ha cambiado.   |
| Programming Device (Dispositivo de programación) | Dispositivo que realizó el cambio de programación. Es posible que varios dispositivos hayan realizado cambios de programación en un dispositivo programado. Se muestran el nombre de host, la fecha o la hora del cambio y el usuario que ha iniciado sesión. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Muestra el archivo actual instalado en el dispositivo programado. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Descarga un archivo de texto del código que se muestra. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Compara el archivo actual con el archivo detectado en una fecha seleccionada. |

### <a name="choose-a-file-to-review"></a>Elección de un archivo para su revisión

En esta sección se describe cómo elegir un archivo para revisarlo.

Para elegir un archivo para su revisión:

1. Seleccione un evento en el panel **Eventos recientes**.

2. Seleccione un archivo en el panel Archivos. El archivo aparece en el panel Actuales.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Selección del archivo con el que quiere trabajar.":::

### <a name="compare-files"></a>Comparar archivos

En esta sección se describe cómo comparar archivos de programación.

Para comparar:

1. Seleccione un evento en el panel Eventos recientes.

2. Seleccione un archivo en el panel Archivos. El archivo aparece en el panel Actuales. Puede comparar este archivo con otros archivos.

3. Seleccione el indicador de comparación.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Indicador de comparación":::

   En la ventana se muestran todas las fechas en las que el archivo seleccionado se detectó en el dispositivo programado. Es posible que varios dispositivos de programación hayan actualizado el archivo en el dispositivo programado.

   El número de diferencias detectadas aparece en la esquina superior derecha de la ventana. Es posible que tenga que desplazarse hacia abajo para ver las diferencias.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="desplazamiento hacia abajo hasta la selección":::

   El número se calcula por líneas adyacentes de texto modificado. Por ejemplo, si se cambiaron ocho líneas consecutivas de código (eliminadas, actualizadas o agregadas), esto se calculará como una diferencia.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="Vista de la escala de tiempo de programación.":::

4. Seleccione una fecha. El archivo detectado en la fecha seleccionada aparecerá en la ventana.

5. El archivo seleccionado en el panel Eventos recientes o Archivos siempre aparece a la derecha.

### <a name="device-programming-information-other-locations"></a>Información de programación del dispositivo: otras ubicaciones

Además de revisar los detalles en la escala de tiempo de programación, puede acceder a la información de programación en la ventana Propiedades del dispositivo y el inventario de dispositivos.

| Tipo de dispositivo | Descripción |
|--|--|
| Propiedades de dispositivo | En la ventana Propiedades del dispositivo se proporciona información sobre el último evento de programación detectado en el dispositivo\. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="Propiedades del dispositivo"::: |
| Device inventory (Inventario de dispositivos) | El inventario de dispositivos indica si el dispositivo es un dispositivo de programación\. :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="Inventario de dispositivos"::: |

## <a name="manage-device-information-from-the-map"></a>Administración de la información del dispositivo desde la asignación

El sensor no actualiza ni modifica los dispositivos directamente en la red. Los cambios realizados aquí solo afectan a la forma en que se analiza el dispositivo.

### <a name="delete-devices"></a>Eliminar dispositivos

Es posible que quiera eliminar un dispositivo si la información aprendida no es pertinente. Por ejemplo,

  - Un contratista asociado en una estación de trabajo de ingeniería se conecta temporalmente para realizar actualizaciones de configuración. Una vez completada la tarea, se quita el dispositivo.

  - Debido a los cambios en la red, algunos dispositivos ya no están conectados.

Si no elimina el dispositivo, el sensor lo seguirá supervisando. Después de 60 días, aparecerá una notificación que recomienda que se elimine.

Es posible que reciba una alerta que indique que el dispositivo no responde si otro dispositivo intenta acceder a él. En este caso, puede que la red no esté configurada correctamente.

El dispositivo se quitará de los informes de asignación de dispositivos, de inventario de dispositivos y de minería de datos. Se mantendrá otra información, por ejemplo: la información almacenada en widgets.

El dispositivo debe estar inactivo durante al menos 10 minutos para eliminarlo.

Para eliminar un dispositivo de la asignación de dispositivos:

1. Seleccione **Dispositivos** en el menú lateral.

2. Haga clic con el botón derecho en un dispositivo y seleccione **Eliminar**.

### <a name="merge-devices"></a>Combinación de dispositivos

Es posible que, en determinadas circunstancias, tenga que combinar dispositivos. Esto puede ser necesario si el sensor detectó entidades de red independientes que están asociadas a un solo dispositivo único. Por ejemplo,

  - Un PLC con cuatro tarjetas de red.

  - Un equipo portátil con Wi-Fi y tarjeta física.
  
  - Una estación de trabajo con dos o más tarjetas de red.

Al realizar una combinación, se indica al sensor que combine las propiedades del dispositivo de dos dispositivos en uno. Al hacerlo, la ventana Propiedades del dispositivo y los informes del sensor se actualizarán con los detalles de las propiedades del nuevo dispositivo.

Por ejemplo, si combina dos dispositivos, cada uno con una dirección IP, las dos direcciones IP aparecerán como interfaces independientes en la ventana Propiedades del dispositivo. Solo se pueden combinar dispositivos autorizados.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Ventana Propiedades del dispositivo":::

La escala de tiempo del evento presenta el evento de combinación.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="Escala de tiempo del evento con eventos combinados.":::

No se puede deshacer una combinación de dispositivos. Si combinó dos dispositivos por error, elimine el dispositivo y espere a que el sensor vuelva a detectarlos a los dos.

Para combinar dispositivos:

1. Seleccione dos dispositivos (Mayús + clic) y haga clic con el botón derecho en uno de ellos.

2. Seleccione **Combinar** para combinar los dispositivos. La combinación puede tardar hasta 2 minutos en completarse.

3. En el cuadro de diálogo de establecimiento de atributos de dispositivos combinados, elija un nombre de dispositivo.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="cuadro de diálogo de atributos":::

4. Seleccione **Guardar**.

### <a name="authorize-and-unauthorize-devices"></a>Autorización y no autorización de dispositivos

Durante el período de aprendizaje, todos los dispositivos detectados en la red se identifican como dispositivos autorizados. La etiqueta **Autorizado** no aparece en estos dispositivos en la asignación de dispositivos.

Cuando un dispositivo se detecta después del período de aprendizaje, aparece como dispositivo no autorizado. Además de ver los dispositivos no autorizados en la asignación, también puede verlos en el inventario de dispositivos.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Inventario de dispositivos":::

**Nuevo dispositivo frente a no autorizado**

Los nuevos dispositivos detectados después del período de aprendizaje aparecerán con una etiqueta `New` y `Unauthorized`.

Si mueve un dispositivo en la asignación o cambia manualmente las propiedades del dispositivo, se quita la etiqueta `New` del icono del dispositivo.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Dispositivos no autorizados: informes de vectores de ataque y de evaluación de riesgos

Los dispositivos no autorizados se incluyen en los informes de evaluación de riesgos y los informes de vectores de ataque.

- **Informes de vectores de ataque:** Los dispositivos marcados como no autorizados se resuelven en el vector de ataque como dispositivos sospechosos de actividad no autorizada que podrían ser una amenaza para la red.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Visualización de los informes de vectores de ataque":::

- **Informes de evaluación de riesgos:** Los dispositivos marcados como no autorizados:

  - Se identifican en informes de evaluación de riesgos

Para autorizar o no autorizar dispositivos manualmente:

1. Haga clic con el botón derecho en el dispositivo en la asignación y seleccione **No autorizar**.

### <a name="mark-devices-as-important"></a>Marcar dispositivos como importantes

Puede marcar dispositivos de red significativos como importantes, por ejemplo, los servidores empresariales críticos. Estos dispositivos se marcan con una estrella en la asignación. La estrella varía según el nivel de zoom de la asignación.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Dispositivos importantes: informes de vectores de ataque y de evaluación de riesgos

Los dispositivos importantes se calculan al generar informes de evaluación de riesgos e informes de vectores de ataque.

  - Informes de vectores de ataque: los dispositivos marcados como importantes se resuelven en el vector de ataque como destinos de ataque. 

  - Informes de evaluación de riesgos: Los dispositivos marcados como importantes se calculan al proporcionar la puntuación de seguridad en el informe de evaluación de riesgos.

## <a name="generate-activity-reports-from-the-map"></a>Generación de informes de actividad a partir de la asignación

Genere un informe de actividad para un dispositivo seleccionado durante 1, 6, 12 o 24 horas. Está disponible la siguiente información:

  - Categoría: información básica de detección por escenarios de tráfico.

  - Dispositivos de origen y destino

  - Datos: información adicional dada de baja.

  - Fecha y hora en que se vieron por última vez.

Puede guardar el informe como archivo de Microsoft Excel o Word.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Actividad reciente":::

Para generar un informe de actividad sobre un dispositivo:

1. Haga clic con el botón derecho en un dispositivo de la asignación.

2. Seleccione un informe de actividades.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Visualización de un informe de las actividades.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Generación de informes de vectores de ataque a partir de la asignación

Simule un informe de vectores de ataque para saber si un dispositivo de la asignación seleccionado es un destino de ataque vulnerable.

Los informes de vectores de ataque proporcionan una representación gráfica de una cadena de vulnerabilidades de dispositivos que se pueden infringir. Estas vulnerabilidades pueden conceder a un atacante acceso a dispositivos de red clave. El simulador de vectores de ataque calcula los vectores de ataque en tiempo real y analiza todos los vectores de ataque por un destino específico.

Para ver un dispositivo en informes de vectores de ataque:

1. Haga clic con el botón derecho en un dispositivo de la asignación.

2. Seleccione **Simulate Attack Vectors** (Simular vectores de ataque). Se abre el cuadro de diálogo Vector de ataque con el dispositivo que seleccione como destino de ataque.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Adición de simulación de vector de ataque":::

3. Agregue los parámetros restantes al cuadro de diálogo y seleccione **Add Simulation** (Agregar simulación).

## <a name="export-device-information-from-the-map"></a>Exportación de información del dispositivo desde la asignación

Exporte la siguiente información del dispositivo desde la asignación.

  - Detalles del dispositivo (Microsoft Excel)

  - Un resumen del dispositivo (Microsoft Excel)

  - Un archivo de Word con grupos (Microsoft Word)

Para realizar la exportación:

1. Seleccione el icono de exportación en la asignación.

1. Seleccione una opción de exportación.

## <a name="see-also"></a>Consulte también

[Investigación de las detecciones de sensores en un inventario de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
