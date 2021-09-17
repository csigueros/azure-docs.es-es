---
title: 'Tutorial: Administración de desechos conectada de Azure IoT | Microsoft Docs'
description: En este tutorial se muestra cómo implementar y usar la plantilla de aplicación de administración de desechos conectada para IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 1ed898b02f2c30c3dcb043903bd3c3261d088539
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122183291"
---
# <a name="tutorial-deploy-and-walk-through-the-connected-waste-management-application-template"></a>Tutorial: Implementación y recorrido de la plantilla de aplicación de administración de desechos conectada

Use la aplicación de *administración de desechos conectada* y las instrucciones de este artículo para desarrollar una solución completa de administración de desechos conectada.

:::image type="content" source="media/tutorial-connectedwastemanagement/concepts-connected-waste-management-architecture-1.png" alt-text="Arquitectura de la administración de desechos conectada":::

### <a name="devices-and-connectivity"></a>Dispositivos y conectividad

Los dispositivos, como los contenedores de desechos, que se usan en entornos abiertos, normalmente se conectan mediante redes de área extensa de bajo consumo (LPWAN) o a través de un operador de red de terceros. Para estos tipos de dispositivos, use el [Puente de dispositivos de Azure IoT Central](../core/howto-build-iotc-device-bridge.md) para enviar los datos del dispositivo a la aplicación de IoT en Azure IoT Central. También puede tener puertas de enlace de dispositivo que sean compatibles con IP y que puedan conectarse directamente a IoT Central.

### <a name="iot-central"></a>IoT Central

Azure IoT Central es una plataforma de aplicaciones de IoT que le ayuda a compilar e implementar rápidamente una solución de IoT. Puede utilizar su marca, personalizar e integrar la solución con servicios de terceros.

Al conectar los dispositivos de desechos a IoT Central, la aplicación proporciona el control, la supervisión y las alertas de los dispositivos, la interfaz de usuario con RBAC integrado, paneles de información configurables y opciones de extensibilidad.

### <a name="extensibility-and-integrations"></a>Extensibilidad e integraciones

Puede ampliar su aplicación de IoT en IoT Central y, si lo desea:

* Transformar e integrar los datos de IoT para el análisis avanzado, como el entrenamiento de modelos de aprendizaje automático, mediante la exportación continua de datos de la aplicación de IoT Central.
* Automatizar flujos de trabajo en otros sistemas al desencadenar acciones mediante Power Automate o webhooks desde la aplicación de IoT Central.
* Acceder mediante programación a la aplicación IoT de IoT Central mediante las API de IoT Central.

### <a name="business-applications"></a>Aplicaciones empresariales

Puede usar datos de IoT para crear varias aplicaciones empresariales dentro de una utilidad de desechos. Por ejemplo, en una solución de administración de residuos conectada, puede optimizar el envío de camiones de recogida de basura. La optimización se puede realizar en función de los datos de los sensores de IoT de los contenedores de desechos conectados. En la [aplicación de administración de desechos conectada a IoT Central](./tutorial-connected-waste-management.md), puede configurar reglas y acciones y establecerlas para crear alertas en el [servicio de campo conectado](/dynamics365/field-service/connected-field-service). Configure Power Automate en reglas de IoT Central para automatizar los flujos de trabajo entre aplicaciones y servicios. Además, en función de las actividades de los servicios de Connected Field Service, la información se puede devolver a Azure IoT Central.

Puede configurar fácilmente los siguientes procesos de integración con IoT Central y el servicio de campo conectado:

* Azure IoT Central puede enviar información sobre las anomalías del dispositivo al servicio de campo conectado para el diagnóstico.
* El servicio de campo conectado puede crear casos o pedidos desencadenados a partir de las anomalías del dispositivo.
* El servicio de campo conectado puede programar técnicos para que realicen las comprobaciones pertinentes, con el fin de evitar incidentes que provoquen que haya un tiempo de inactividad.
* El panel de dispositivos de Azure IoT Central se puede actualizar con información relevante acerca de la programación y los servicios.

En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Usar la plantilla *Administración de residuos conectada* de Azure IoT Central para crear la aplicación.
> * Explorar y personalizar el panel. 
> * Explorar la plantilla de dispositivo de cubo de basura conectado.
> * Explorar los dispositivos simulados
> * Explorar y configurar reglas
> * Configurar trabajos
> * Personalización de marca de la aplicación.

## <a name="prerequisites"></a>Requisitos previos

* No se necesitan requisitos previos específicos para implementar esta aplicación.
* Puede usar el plan gratuito o una suscripción de Azure.

## <a name="create-connected-waste-management-application"></a>Creación de una aplicación de administración de desechos conectada

1. Navegue al sitio de [Compilación Azure IoT Central](https://aka.ms/iotcentral). Después, inicie sesión con una cuenta Microsoft personal, profesional o educativa. Seleccione **Compilar** en la barra de navegación de la izquierda y la pestaña **Gobierno**: :::image type="content" source="media/tutorial-connectedwastemanagement/iot-central-government-tab-overview.png" alt-text="Plantilla de administración de desechos conectada":::

1. Seleccione **Crear aplicación** en **Administración de desechos conectada**.

Para más información, consulte [Creación de una aplicación de Azure IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Recorrido por la aplicación.

Las secciones siguientes le guiarán por las características clave de la aplicación:

### <a name="dashboard"></a>Panel 

Después de implementar la plantilla de aplicación, el panel predeterminado es el **panel de administración de desechos de Wide World**.

:::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-1.png" alt-text="Captura de pantalla del panel de administración de residuos Wide World.":::


Como desarrollador, puede crear y personalizar las vistas del panel para los operadores. En primer lugar, vamos a explorar el panel. 

>[!NOTE]
>Todos los datos que se muestran en el panel se basan en los datos del dispositivo simulado, que se verán con más detalle en la sección siguiente. 

El panel consta de diferentes iconos:

* **Icono de imagen del servicio Wide World Water**: el primer icono del panel es un icono de imagen de un servicio de agua ficticio, "Wide World Water". Puede personalizar el icono y colocar su propia imagen o eliminarlo. 

* **Icono de imagen de cubo de basura**: puede usar iconos de imagen y contenido para crear una representación visual del dispositivo que se va a supervisar, junto con un texto descriptivo. 

* **Icono de indicador KPI de nivel de llenado**: este icono muestra el valor devuelto por un sensor de *nivel de llenado* de un cubo de basura. Fill level (Nivel de llenado) y otros sensores como *Odor meter* (Medidor de olor) o *Weight* (Peso) de un cubo de basura se pueden supervisar de forma remota. El operador puede tomar medidas, como enviar un camión de recogida de basuras. 

* **Mapa del área de supervisión de residuos**: este icono usa Azure Maps y se puede configurar directamente en Azure IoT Central. El icono del mapa muestra la [ubicación](../core/howto-use-location-data.md) del dispositivo. Intente mantener el puntero sobre el mapa y pruebe los controles: acercar, alejar o ampliar.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-map.png" alt-text="Captura de pantalla del mapa del panel de la plantilla de administración de residuos conectada.":::



* **Gráfico de barras del nivel de llenado, olor y peso**: puede visualizar uno o varios tipos de datos de telemetría de los dispositivos en un gráfico de barras. También puede expandir este gráfico.  

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-dashboard-bar-chart.png" alt-text="Captura de pantalla del gráfico de barras del panel de la plantilla de administración de desechos conectada":::


* **Icono de contenido de Field Services**: el panel incluye un vínculo a cómo realizar la integración con los servicios de campo de Dynamics 365 desde la aplicación de Azure IoT Central. Por ejemplo, puede usar Field Services para crear vales para enviar los servicios de recogida de basura. 

### <a name="customize-the-dashboard"></a>Personalización del panel 

Para personalizar el panel, seleccione el menú **Edit** (Editar). A continuación, puede agregar nuevos iconos o configurar los existentes. Este es el aspecto del panel en el modo de edición: 

:::image type="content" source="media/tutorial-connectedwastemanagement/edit-dashboard.png" alt-text="Captura de pantalla del panel de la plantilla de administración de residuos conectada en el modo de edición.":::


También puede seleccionar **+ New** (+ Nuevo) para crear un panel y configurarlo desde el principio. Puede tener varios paneles y cambiar entre ellos desde el menú del panel. 

### <a name="explore-the-device-template"></a>Exploración de la plantilla de dispositivo

Una plantilla de dispositivo de Azure IoT Central define las funcionalidades de un dispositivo, entre ellas, la telemetría, las propiedades o los comandos. Como desarrollador, puede definir plantillas de dispositivo que representen la funcionalidad de los dispositivos que se van a conectar. 

La aplicación Administración de residuos conectada incluye una plantilla de dispositivo de cubo de basura conectado de ejemplo.

Para ver la plantilla de dispositivo, siga estos pasos:

1. Seleccione **Device templates** (Plantillas de dispositivo) en el panel izquierdo de la aplicación en Azure IoT Central. 

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template.png" alt-text="Captura de pantalla que muestra la lista de plantillas de dispositivo de la aplicación.":::


1. En la lista **Device templates** (Plantillas de dispositivo), seleccione **Connected Waste Bin** (Cubo de basura conectado).

1. Examine las funcionalidades de la plantilla de dispositivo. Puede ver que se definen sensores como **Fill level** (Nivel de llenado), **Odor meter** (Medidor de olor), **Weight** (Peso) y **Location** (Ubicación).

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template-connected-bin.png" alt-text="Captura de pantalla que muestra los detalles de la plantilla de dispositivo del cubo de desechos conectado":::


### <a name="customize-the-device-template"></a>Personalización de la plantilla de dispositivo

Pruebe a personalizar lo siguiente:

1. En el menú de la plantilla de dispositivo, seleccione **Customize** (Personalizar).
1. Busque el tipo de telemetría **Odor meter** (Medidor de olor).
1. Actualice el campo **Display name** (Nombre para mostrar) de **Odor meter** (Medidor de olor) a **Odor level** (Nivel de olor).
1. Pruebe a actualizar la unidad de medida o establezca los campos **Min value** (Valor mínimo) y **Max value** (Valor máximo).
1. Seleccione **Guardar**. 

### <a name="add-a-cloud-property"></a>Adición de una propiedad de la nube 

A continuación, se indica cómo puede hacerlo.

1. En el menú de la plantilla de dispositivo, seleccione **Cloud property** (Propiedad de la nube).
1. Seleccione **+ Add Cloud Property** (+ Agregar propiedad de la nube). En Azure IoT Central, puede agregar a un dispositivo una propiedad pertinente, aunque no se espera que el dispositivo la envíe. Por ejemplo, una propiedad de la nube podría ser un umbral de alerta específico del área de instalación, información de recursos o información de mantenimiento. 
1. Seleccione **Guardar**. 
 
### <a name="views"></a>Vistas 

La plantilla de dispositivo de cubo de basura conectado incluye vistas predefinidas. Explore las vistas y actualícelas si lo desea. Las vistas definen cómo verán los operadores los datos del dispositivo y las propiedades de la nube de entrada. 

:::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-device-template-views.png" alt-text="Captura de pantalla de las vistas de plantillas de dispositivo de la plantilla de administración de desechos conectada":::


### <a name="publish"></a>Publicar 

Si ha realizado algún cambio, recuerde publicar la plantilla de dispositivo. 

### <a name="create-a-new-device-template"></a>Creación de una nueva plantilla de dispositivo 

Para crear una nueva plantilla de dispositivo, seleccione **+ New** (+ Nueva) y siga los pasos. Puede crear una plantilla de dispositivo personalizada desde el principio o elegir una del catálogo de dispositivos de Azure. 

### <a name="explore-simulated-devices"></a>Explorar los dispositivos simulados

En Azure IoT Central, puede crear dispositivos simulados para probar la aplicación y la plantilla de dispositivo. 

La aplicación Administración de residuos conectada tiene dos dispositivos simulados asociados a la plantilla de dispositivo de cubo de basura conectado. 

### <a name="view-the-devices"></a>Visualización de los dispositivos

1. En el panel izquierdo de Azure IoT Central, seleccione **Device** (Dispositivo). 

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-devices.png" alt-text="Captura de pantalla de los dispositivos de la plantilla de administración de residuos conectada.":::


1. Seleccione el dispositivo **Connected Waste Bin** (Cubo de basura conectado).  

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-devices-bin-1.png" alt-text="Captura de pantalla de las propiedades del dispositivo de la plantilla de administración de residuos conectada.":::


1. Vaya a la pestaña **Cloud Properties** (Propiedades de la nube). Actualice el valor de **Bin full alert threshold** (Umbral de alerta de cubo lleno) de **95** a **100**. 

Explore las pestañas **Device Properties** (Propiedades del dispositivo) **Device Dashboard** (Panel del dispositivo). 

> [!NOTE]
> Todas las pestañas se han configurado a partir de las vistas de la plantilla de dispositivo.

### <a name="add-new-devices"></a>Adición de nuevos dispositivos

Para agregar nuevos dispositivos, seleccione **+ New** (+ Nuevo) en la pestaña **Devices** (Dispositivos). 

## <a name="explore-and-configure-rules"></a>Exploración y configuración de reglas

En Azure IoT Central, puede crear reglas para supervisar automáticamente los datos de telemetría de los dispositivos y desencadenar acciones cuando se cumplan una o varias condiciones. Las acciones pueden incluir el envío de notificaciones por correo electrónico, el desencadenamiento de una acción de Power Automate o iniciar una acción de webhook para enviar datos a otros servicios.

La aplicación Administración de residuos conectada tiene cuatro reglas de ejemplo.

### <a name="view-rules"></a>Visualización de reglas

1. En el panel izquierdo de Azure IoT Central, seleccione **Rules** (Reglas).

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-rules.png" alt-text="Captura de pantalla de las reglas de la plantilla de administración de residuos conectada.":::


1. Seleccione **Bin full alert** (Alerta de cubo lleno).

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-bin-full-alert.png" alt-text="Captura de pantalla de la alerta de cubo lleno.":::


 1. **Bin full alert** (Alerta de cubo lleno) comprueba la condición siguiente: **Fill level is greater than or equal to Bin full alert threshold** (Nivel de llenado es mayor o igual que Umbral de alerta de cubo lleno).

    **Bin full alert threshold** (Umbral de alerta de cubo lleno) es una propiedad de la nube que se define en la plantilla del dispositivo cubo de basura conectado. 

Ahora, se creará una acción de correo electrónico.

### <a name="create-an-email-action"></a>Creación de una acción de correo electrónico

Puede configurar una acción de correo electrónico en la lista **Actions** (Acciones) de la regla:
1. Seleccione **+ Email** (+ Correo electrónico). 
1. En el campo **Display name** (Nombre para mostrar), escriba **High pH alert** (Alerta de pH alto).
1. Escriba la dirección de correo electrónico asociada a la cuenta de Azure IoT Central en el campo **To** (Para). 
1. Tiene la opción de escribir una nota e incluirla en el texto del correo electrónico.
1. Seleccione **Done** > **Save** (Listo > Guardar). 

Ahora recibirá un correo electrónico cuando se cumpla la condición configurada.

>[!NOTE]
>La aplicación envía un correo electrónico cada vez que se cumple una condición. Deshabilite la regla para dejar de recibir correo electrónico de la regla automatizada. 
  
Para crear una nueva regla, en el panel izquierdo de **Rules** (Reglas), seleccione **+New** (+Nuevo).

## <a name="configure-jobs"></a>Trabajos de configuración

En Azure IoT Central, los trabajos permiten desencadenar actualizaciones de propiedades de la nube o del dispositivo en varios dispositivos. También puede usar trabajos para desencadenar comandos de dispositivo en varios dispositivos. Azure IoT Central automatiza el flujo de trabajo. 

1. En el panel izquierdo de Azure IoT Central, seleccione **Jobs** (Trabajos). 
1. Seleccione **+New** (+Nuevo) y configure uno o varios trabajos. 

## <a name="customize-your-application"></a>Personalización de la aplicación 

Como creador, puede cambiar varias opciones de configuración para personalizar la experiencia del usuario en la aplicación.

### <a name="change-the-application-theme"></a>Cambio del tema de la aplicación

A continuación, se indica cómo puede hacerlo.
1. Vaya a **Administration** > **Customize your application** (Administración > Personalizar la aplicación).
1. Seleccione **Change** (Cambiar) para elegir una imagen para cargar en el campo **Application logo** (Logotipo de la aplicación).
1. Seleccione **Change** (Cambiar) para elegir una imagen para cargar en el **icono del explorador** (una imagen que aparecerá en las pestañas del explorador).
1. También puede reemplazar los colores predeterminados del explorador mediante la adición de códigos de color HTML hexadecimales. Use los campos **Header** (Encabezado) y **Accent** (Color de énfasis) para este fin.

    :::image type="content" source="media/tutorial-connectedwastemanagement/connected-waste-management-customize-your-application.png" alt-text="Captura de pantalla de la personalización de la aplicación de la plantilla de administración de residuos conectada.":::


1. También puede cambiar las imágenes de la aplicación. Seleccione **Administration** > **Application settings** > **Select image** (Administración > Configuración de la aplicación > Seleccionar imagen) para elegir la imagen que se va a cargar como imagen de la aplicación.
1. Por último, también puede cambiar el tema; para ello, seleccione **Settings** (Configuración) en la cabecera de la aplicación.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando esta aplicación, puede eliminarla mediante estos pasos:

1. Seleccione **Administration** (Administración) en el panel izquierdo de la aplicación de Azure IoT Central.
1. Seleccione **Application settings** > **Delete** (Configuración de la aplicación > Eliminar).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Conceptos de consumo de agua conectado](./tutorial-water-consumption-monitoring.md)
