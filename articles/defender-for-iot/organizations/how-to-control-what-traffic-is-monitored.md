---
title: Control del tráfico que se supervisa
description: Los sensores realizan automáticamente una detección profunda de paquetes en el tráfico de TI y OT y resuelven la información sobre los dispositivos de red, como los atributos del dispositivo y el comportamiento de la red. Hay varias herramientas disponibles para controlar el tipo de tráfico que detecta cada sensor.
ms.date: 12/07/2020
ms.topic: how-to
ms.openlocfilehash: b80ef4a4a1fca7383da80fda6fdf42831913f2bd
ms.sourcegitcommit: a038863c0a99dfda16133bcb08b172b6b4c86db8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/29/2021
ms.locfileid: "113020802"
---
# <a name="control-what-traffic-is-monitored"></a>Control del tráfico que se supervisa

Los sensores realizan automáticamente una detección profunda de paquetes en el tráfico de TI y OT y resuelven la información sobre los dispositivos de red, como los atributos del dispositivo y el comportamiento. Hay varias herramientas disponibles para controlar el tipo de tráfico que detecta cada sensor.

## <a name="learning-and-smart-it-learning-modes"></a>Modos de aprendizaje y aprendizaje de TI inteligente

El modo de aprendizaje indica al sensor que aprenda la actividad habitual de la red. Algunos ejemplos son los dispositivos o los protocolos detectados en la red, las transferencias de archivos entre dispositivos específicos, etc. Esta actividad se convierte en la base de referencia de la red.

El modo de aprendizaje se habilita automáticamente después de la instalación y permanece habilitado hasta que se desactive. El período aproximado del modo de aprendizaje oscila entre dos y seis semanas, según el tamaño y la complejidad de la red. Después de este período, cuando el modo de aprendizaje está deshabilitado, toda nueva actividad detectada desencadena alertas. Las alertas se desencadenan cuando el motor de directivas detecta desviaciones de la base de referencia aprendida.

Después de que finaliza el período de aprendizaje y el modo de aprendizaje se deshabilita, el sensor podría detectar un nivel inusualmente alto de cambios en la base de referencia, que son el resultado de la actividad de TI normal, como las solicitudes DNS y HTTP. La actividad se conoce como comportamiento de TI no determinista. El comportamiento también puede desencadenar alertas de infracción de directivas y notificaciones del sistema innecesarias. Para reducir la cantidad de estas alertas y notificaciones, puede habilitar la función **Smart IT Learning** (Aprendizaje de TI inteligente).

Cuando se habilita este modo, el sensor realiza un seguimiento del tráfico de red que genera el comportamiento de TI no determinista en función de escenarios de alerta específicos.

El sensor supervisa este tráfico durante siete días. Si durante este tiempo detecta el mismo tráfico de TI no determinista, continúa supervisando el tráfico durante otros siete días. Cuando el tráfico no se detecta durante siete días completos, el aprendizaje de TI inteligente se deshabilita para ese escenario. El nuevo tráfico detectado en ese escenario solo generará alertas y notificaciones.

Trabajar con el aprendizaje de TI inteligente le ayuda a reducir el número de alertas y notificaciones innecesarias ocasionadas por escenarios de TI ruidosos.

Si el sensor está controlado por la consola de administración local, no se pueden deshabilitar los modos de aprendizaje. En casos como este, el modo de aprendizaje solo se puede deshabilitar desde la consola de administración.

Las funcionalidades de aprendizaje (aprendizaje y aprendizaje de TI inteligente) están habilitadas de forma predeterminada.

Para habilitar o deshabilitar el aprendizaje:

- Seleccione **Configuración del sistema** y alterne entre las opciones **Aprendizaje** y **Smart IT Learning** (Aprendizaje de TI inteligente).

:::image type="content" source="media/concept-learning-modes/toggle-options-for-learning-and-smart-it-learning.png" alt-text="Pantalla de alternancia de configuración del sistema.":::

## <a name="configure-subnets"></a>Configurar subredes

Las configuraciones de subred afectan a cómo se ven los dispositivos en el mapa de dispositivos.

De forma predeterminada, el sensor detecta la configuración de la subred y rellena el cuadro de diálogo **Configuración de subred** con esta información.

Para habilitar el foco sobre los dispositivos de OT, la subred agrega automáticamente los dispositivos de TI al mapa de dispositivos. Cada subred se presenta como una única entidad en el mapa, que incluye una funcionalidad interactiva de contracción/expansión para "explorar en profundidad" una subred de TI y volver.

Cuando trabaje con subredes, seleccione las subredes de ICS para identificar las subredes de OT. Luego, puede centrar la vista del mapa sobre las redes de OT e ICS y contraer hasta un nivel mínimo la presentación de los elementos de red de TI. Esta tarea reduce el número total de los dispositivos que se muestran en el mapa y proporciona una imagen clara de los elementos de red de OT e ICS.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/expand-network-option.png" alt-text="Captura de pantalla que muestra el filtrado por una red.":::

Puede cambiar la configuración o cambiar la información de la subred manualmente; para ello, exporte los datos detectados, modifíquelos manualmente y, luego, importe de nuevo la lista de subredes que ha definido manualmente. Para más información sobre la exportación e importación, consulte [Importación de la información del dispositivo](how-to-import-device-information.md).

En algunos casos, como en los entornos donde se usan intervalos públicos como intervalos internos, puede indicar al sensor que resuelva todas las subredes como subredes internas seleccionando la opción **Do Not Detect Internet Activity** (No detectar la actividad de Internet). Al seleccionar esta opción:

- Las direcciones IP públicas se tratarán como direcciones locales.

- No se enviará ninguna alerta sobre la actividad de Internet no autorizada, lo que reduce las notificaciones y alertas recibidas sobre direcciones externas.

Para configurar las subredes:

1. En el menú lateral, seleccione **Configuración del sistema**.

2. En la ventana **Configuración del sistema**, seleccione **Subredes**.

   :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-subnets-configuration-screen.png" alt-text="Captura de pantalla que muestra la pantalla de configuración de subredes."::: 

3. Para agregar subredes automáticamente cuando se detecten nuevos dispositivos, mantenga seleccionada la opción **Auto Subnets Learning** (Aprendizaje automático de subredes).

4. Para resolver todas las subredes como subredes internas, seleccione **Don't Detect Internet Activity** (No detectar la actividad de Internet).

5. Seleccione **Agregar red** y defina los siguientes parámetros para cada subred:

    - Dirección IP de la subred.
    - Dirección de la máscara de subred.
    - Nombre de la subred. Se recomienda asignar un nombre descriptivo a cada subred para que pueda identificarla fácilmente y así diferenciar entre redes de TI y redes de OT. El nombre puede tener hasta 60 caracteres.

6. Para marcar esta subred como una subred de OT, seleccione **ICS Subnet** (Subred de ICS).

7. Para presentar la subred por separado cuando organice el mapa según el nivel de Purdue, seleccione **Segregated** (Segregado).

8. Para eliminar una subred, seleccione :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/delete-icon.png" border="false":::.

9. Para eliminar todas las subredes, seleccione **Borrar todo**.

10. Para exportar subredes configuradas, seleccione **Exportar**. La tabla de subredes se descarga en la estación de trabajo.

11. Seleccione **Guardar**.

### <a name="importing-information"></a>Importación de la información 

Para importar la información de subred, seleccione **Importar** y elija un archivo CSV para importar. La información de subred se actualiza con la información importada. Si importa un campo vacío, perderá los datos.

## <a name="detection-engines"></a>Motores de detección

Los motores de análisis de autoaprendizaje eliminan la necesidad de actualizar firmas o definir reglas. Los motores emplean el análisis de comportamiento específico de ICS y la ciencia de datos para analizar de forma continua el tráfico de red de OT en busca de anomalías, malware, problemas operativos, infracciones de protocolo y desviaciones de la actividad de red de base de referencia.

> [!NOTE]
> Se recomienda habilitar todos los motores de seguridad.

Cuando un motor detecta una desviación, se desencadena una alerta. Puede ver y administrar las alertas desde la pantalla de alertas o un sistema asociado.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/deviation-alert-screen.png" alt-text="Captura de pantalla que muestra la detección de la desviación.":::

El nombre del motor que desencadenó la alerta aparece bajo el título de la alerta.

### <a name="protocol-violation-engine"></a>Motor de infracción del protocolo 

Se produce una infracción del protocolo cuando la estructura de paquetes o los valores de campo no cumplen la especificación del protocolo.

Escenario de ejemplo: Alerta *"Illegal MODBUS Operation (Function Code Zero)"* [Operación MODBUS no válida (código de función cero)]. Esta alerta indica que un dispositivo primario envió una solicitud con el código de función 0 a un dispositivo secundario. Esta acción no se permite de acuerdo con la especificación del protocolo y es posible que el dispositivo secundario no trate la entrada correctamente.

### <a name="policy-violation-engine"></a>Motor de infracción de directiva

Se produce una infracción de directiva cuando hay una desviación del comportamiento de base de referencia definido en la configuración aprendida o configurada.

Escenario de ejemplo: Alerta *"Unauthorized HTTP User Agent"* (Agente de usuario HTTP no autorizado). Esta alerta indica que una aplicación que no se ha aprendido o aprobado por la directiva se usa como cliente HTTP en un dispositivo. Puede tratarse de una nueva aplicación o explorador web de ese dispositivo.

### <a name="malware-engine"></a>Motor de malware

El motor de malware detecta actividades de red malintencionadas.

Escenario de ejemplo: Alerta *"Suspicion of Malicious Activity (Stuxnet)"* [Sospecha de actividad malintencionada (Stuxnet)]. Esta alerta indica que el sensor ha detectado actividad de red sospechosa que se sabe que está relacionada con el malware Stuxnet. Este malware es una amenaza persistente avanzada dirigida a redes de control industrial y SCADA.

### <a name="anomaly-engine"></a>Motor de anomalías

El motor de anomalías detecta anomalías en el comportamiento de la red.

Escenario de ejemplo: *"Periodic Behavior in Communication Channel"* (Comportamiento periódico en el canal de comunicación). El componente inspecciona las conexiones de red y busca el comportamiento cíclico y periódico de la transmisión de datos. Este comportamiento es habitual en redes industriales.

### <a name="operational-engine"></a>Motor operativo

El motor operativo detecta incidentes operativos o entidades que no funcionan correctamente.

Escenario de ejemplo: Alerta *"Device is Suspected to be Disconnected (Unresponsive)"* [Se sospecha que el dispositivo está desconectado (no responde)]. Esta alerta se genera cuando un dispositivo no responde a ningún tipo de solicitud durante un período predefinido. Esta alerta puede indicar que el dispositivo está apagado, desconectado o no funciona correctamente.

### <a name="enable-and-disable-engines"></a>Habilitación y deshabilitación de motores

Al deshabilitar un motor de directivas, la información que genera el motor no estará disponible para el sensor. Por ejemplo, si deshabilita el motor de anomalías, no recibirá alertas sobre anomalías de la red. Si ha creado una regla de reenvío, no se enviarán las anomalías que aprenda el motor. Para habilitar o deshabilitar un motor de directivas, seleccione **Habilitado** o **Deshabilitado** para el motor específico.

La puntuación total se muestra en la esquina inferior derecha de la pantalla **Configuración del sistema**. La puntuación indica el porcentaje de protección disponible habilitado mediante los motores de protección contra amenazas. Cada motor es el 20 por ciento de la protección disponible.

:::image type="content" source="media/how-to-control-what-traffic-is-monitored/protection-score-screen.png" alt-text="Captura de pantalla que muestra una puntuación":::.

## <a name="configure-dhcp-address-ranges"></a>Configuración de intervalos de direcciones DHCP

La red puede constar de direcciones IP estáticas y dinámicas. Por lo general, las direcciones estáticas se encuentran en redes de OT mediante historiadores, controladores y dispositivos de infraestructura de red como conmutadores y enrutadores. Normalmente, la asignación de direcciones IP dinámicas se implementa en redes invitadas con equipos portátiles, PC, smartphones y otro equipo portátil (mediante conexiones físicas Wi-Fi o LAN en diferentes ubicaciones).

Si está trabajando con redes dinámicas, deberá tratar con los cambios de dirección IP que se producen cuando se asignan nuevas direcciones IP. Esto se hace definiendo intervalos de direcciones DHCP.

Puede que se produzcan cambios, por ejemplo, cuando un servidor DHCP asigna direcciones IP.

La definición de direcciones IP dinámicas en cada sensor permite una compatibilidad completa y transparente en casos en los que cambie la dirección IP. Esto garantiza que se generan informes completos para cada dispositivo único.

La consola del sensor presenta la dirección IP más actual asociada con el dispositivo e indica qué dispositivos son dinámicos. Por ejemplo:

- El informe de minería de datos y el informe de inventario de dispositivos consolidan toda la actividad aprendida del dispositivo en una entidad, con independencia de los cambios en la dirección IP. Estos informes indican qué direcciones se definieron como direcciones DHCP.

  :::image type="content" source="media/how-to-control-what-traffic-is-monitored/populated-device-inventory-screen-v2.png" alt-text="Captura de pantalla que muestra el inventario de dispositivos.":::

- La ventana **Propiedades del dispositivo** indica si el dispositivo se definió como un dispositivo DHCP.

Para establecer un intervalo de direcciones DHCP:

1.  En el menú lateral, seleccione **DHCP Ranges** (Intervalos DHCP) en la ventana **Configuración del sistema**.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/dhcp-address-range-screen.png" alt-text="Captura de pantalla que muestra la selección de intervalos DHCP.":::

2.  Defina un nuevo intervalo con los valores **Desde** y **Hasta**.

3.  Opcionalmente: Defina el nombre del intervalo, hasta 256 caracteres.

4.  Para exportar los intervalos a un archivo CSV, seleccione **Exportar**.

5. Para agregar varios intervalos manualmente desde un archivo CSV, seleccione **Importar** y, luego, elija el archivo.

    > [!NOTE]
    > Los intervalos que se importen desde un archivo CSV sobrescribirán la configuración de intervalos existente.

6. Seleccione **Guardar**.

## <a name="configure-dns-servers-for-reverse-lookup-resolution"></a>Configuración de los servidores DNS para la resolución de búsquedas inversas

Para mejorar el enriquecimiento de dispositivos, puede configurar varios servidores DNS para llevar a cabo búsquedas inversas. Puede resolver nombres de host o FQDN asociados a las direcciones IP detectadas en las subredes de red. Por ejemplo, si un sensor detecta una dirección IP, podría consultar varios servidores DNS para resolver el nombre de host.

Se admiten todos los formatos CIDR.

El nombre de host aparece en el inventario de dispositivos, en el mapa de dispositivos y en los informes.

Puede programar resoluciones de búsquedas inversas en intervalos horarios específicos, por ejemplo, cada 12 horas. También puede programar una hora específica.

Para definir servidores DNS:

1. Seleccione **Configuración del sistema** y, luego, elija **Configuración DNS**.

2. Seleccione **Agregar servidor DNS**.

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-configuration-screen.png" alt-text="Captura de pantalla que muestra la selección de Agregar servidor DNS.":::

3. En el campo **Schedule reverse DNS lookup** (Programar búsqueda inversa de DNS), elija:

    - Intervalos (por hora).
  
    - Una hora concreta. Use el formato europeo. Por ejemplo, use **14:30** en lugar de **2:30 p .m.** .

4. En el campo **DNS Server Address** (Dirección de servidor DNS), escriba la dirección IP de DNS.

5. En el campo **DNS Server Port** (Puerto de servidor DNS), escriba el puerto DNS.

6. Resuelva las direcciones IP de la red en los FQDN de los dispositivos. En el campo **Number of Labels** (Número de etiquetas), agregue el número de etiquetas de dominio que se mostrarán. Se muestran hasta 30 caracteres de izquierda a derecha.

7. En el campo **Subredes**, escriba las subredes que quiere que consulte el servidor DNS.

8. Seleccione el botón de alternancia **Habilitar** si quiere iniciar la búsqueda inversa.

### <a name="test-the-dns-configuration"></a>Prueba de la configuración de DNS 

Mediante un dispositivo de prueba, compruebe que la configuración definida funcione correctamente:

1. Habilite el botón de alternancia **DNS Lookup** (Consultar DNS).

2. Seleccione **Probar**.

3. Escriba una dirección en **Lookup Address** (Dirección de búsqueda) en el cuadro de diálogo **DNS reverse lookup test for server** (Prueba de búsqueda DNS inversa para el servidor).

    :::image type="content" source="media/how-to-enrich-asset-information/dns-reverse-lookup-test-screen.png" alt-text="Captura de pantalla que muestra el área de dirección de búsqueda.":::

4. Seleccione **Probar**.

## <a name="configure-windows-endpoint-monitoring"></a>Configuración de la supervisión de puntos de conexión de Windows

Con la funcionalidad de supervisión de puntos de conexión de Windows, puede configurar Azure Defender para IoT para sondear de forma selectiva los sistemas Windows. Esta actividad le proporciona información más precisa y específica sobre los dispositivos, por ejemplo, los niveles de Service Pack.

Puede configurar el sondeo con intervalos y hosts específicos, y que se realice solo con la frecuencia que se desee. El sondeo selectivo se realiza mediante el Instrumental de administración de Windows (WMI), que es el lenguaje de scripting estándar de Microsoft para administrar sistemas Windows.

> [!NOTE]
> - Solo puede ejecutar un examen cada vez.
> - Los mejores resultados se obtendrán con los usuarios que tengan privilegios de administrador local o de dominio.
> - Antes de comenzar la configuración de WMI, configure una regla de firewall que abra el tráfico saliente del sensor a la subred examinada mediante el puerto UDP 135 y todos los puertos TCP por encima de 1024.

Una vez finalizado el sondeo, se puede encontrar un archivo de registro con todos los intentos de sondeo en la opción para exportar un registro. El registro contiene todas las direcciones IP que se sondearon. Para cada dirección IP, el registro muestra la información de acierto y error. También hay un código de error, que es una cadena libre derivada de la excepción. Solo se mantiene en el sistema el examen del último registro.

Puede realizar exámenes programados o manuales. Una vez finalizado el examen, puede ver los resultados en un archivo CSV.

**Requisitos previos**

Configure una regla de firewall que abra el tráfico saliente del sensor a la subred examinada mediante el puerto UDP 135 y todos los puertos TCP por encima de 1024.

Para configurar un examen automático:

1. En el menú lateral, seleccione **Configuración del sistema**.

2. Seleccione **Windows Endpoint Monitoring** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: (Supervisión de puntos de conexión de Windows).

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Captura de pantalla que muestra la selección de Windows Endpoint Monitoring (Supervisión de puntos de conexión de Windows).":::

3. En el panel **Programación de exámenes**, configure las opciones de la siguiente manera:

      - **By fixed intervals (in hours)** (Por intervalos fijos [en horas]): establezca la programación de examen según intervalos en horas.

      - **By specific times** (Por horas específicas): establezca la programación de examen según horas específicas y seleccione **Save Scan** (Guardar examen).

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/schedule-a-scan-screen-v2.png" alt-text="Captura de pantalla que muestra el botón Save Scan (Guardar examen).":::

4. Para definir el intervalo de examen, seleccione **Set scan ranges** (Establecer intervalos de examen).

5. Establezca el intervalo de direcciones IP y agregue su usuario y contraseña.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/edit-scan-range-screen.png" alt-text="Captura de pantalla que muestra cómo agregar un usuario y una contraseña.":::

6. Para excluir un intervalo de direcciones IP de un examen, seleccione **Deshabilitar** junto al intervalo.

7. Para quitar un intervalo, seleccione :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/remove-scan-icon.png" border="false"::: junto a él.

8. Seleccione **Guardar**. Se cierra el cuadro de diálogo **Edit Scan Ranges Configuration** (Editar configuración de intervalos de examen) y el número de intervalos aparece en el panel **Scan Ranges** (Intervalos de examen).

Para realizar un examen manual:

1. En el menú lateral, seleccione **Configuración del sistema**.

2. Seleccione **Windows Endpoint Monitoring** :::image type="icon" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-icon-v2.png" border="false"::: (Supervisión de puntos de conexión de Windows).

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/windows-endpoint-monitoring-screen-v2.png" alt-text="Captura de pantalla que muestra la pantalla de configuración de Windows Endpoint Monitoring (Supervisión de puntos de conexión de Windows).":::

3. En el panel **Acciones**, seleccione **Iniciar detección**. Aparece una barra de estado en el panel **Acciones** que muestra el progreso del proceso de examen.

    :::image type="content" source="media/how-to-control-what-traffic-is-monitored/started-scan-screen-v2.png" alt-text="Captura de pantalla que muestra el botón Iniciar detección.":::

Para ver los resultados del examen:

1. Una vez finalizado el examen, en el panel **Acciones**, seleccione **Ver resultados del examen**. Se descarga en el equipo el archivo CSV con los resultados del examen.

## <a name="see-also"></a>Consulte también

[Investigación de las detecciones del sensor en un inventario de dispositivos](how-to-investigate-sensor-detections-in-a-device-inventory.md)
[Investigación de las detecciones del sensor en el mapa de dispositivos](how-to-work-with-the-sensor-device-map.md)
