---
title: 'Tutorial: Supervisión de la calidad del agua de Azure IoT | Microsoft Docs'
description: En este tutorial se muestra cómo implementar y usar la plantilla de aplicación para la aplicación de supervisión de calidad del agua para IoT Central.
author: miriambrus
ms.author: miriamb
ms.date: 08/02/2021
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 481a085b1fd5fec55cd34f885dfcda40fec6f5e7
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179142"
---
# <a name="tutorial-deploy-and-walk-through-the-water-quality-monitoring-application"></a>Tutorial: Implementación y recorrido por la aplicación de supervisión de calidad del agua

Use la plantilla de aplicación de *supervisión de calidad del agua* de IoT Central y las instrucciones de este artículo para desarrollar una solución de supervisión de calidad del agua de un extremo a otro.


![Arquitectura de supervisión de la calidad del agua](./media/tutorial-waterqualitymonitoring/concepts-water-quality-monitoring-architecture1.png)

### <a name="devices-and-connectivity"></a>Dispositivos y conectividad

Las soluciones de administración de agua usan dispositivos de agua inteligentes, como medidores de flujo, monitores de calidad del agua, válvulas inteligentes y detectores de fugas.

Los dispositivos que se usan en las soluciones inteligentes para el agua normalmente se conectan mediante redes de área extensa de bajo consumo (LPWAN) o mediante un operador de red de terceros. Para estos tipos de dispositivos, use el [Puente de dispositivos de Azure IoT Central](../core/howto-build-iotc-device-bridge.md) para enviar los datos del dispositivo a la aplicación de IoT en Azure IoT Central. También puede tener puertas de enlace de dispositivo que sean compatibles con IP y que puedan conectarse directamente a IoT Central.

### <a name="iot-central"></a>IoT Central

Azure IoT Central es una plataforma de aplicaciones de IoT que le ayuda a compilar e implementar rápidamente una solución de IoT. Puede utilizar su marca, personalizar e integrar la solución con servicios de terceros.

Al conectar los dispositivos de agua inteligentes a IoT Central, la aplicación proporciona el control, la supervisión y las alertas de los dispositivos, la interfaz de usuario con RBAC integrado, paneles de información configurables y opciones de extensibilidad.

### <a name="extensibility-and-integrations"></a>Extensibilidad e integraciones

Puede ampliar su aplicación de IoT en IoT Central y, si lo desea:

* Transformar e integrar los datos de IoT para el análisis avanzado, como el entrenamiento de modelos de aprendizaje automático, mediante la exportación continua de datos de la aplicación de IoT Central.
* Automatizar flujos de trabajo en otros sistemas al desencadenar acciones mediante Power Automate o webhooks desde la aplicación de IoT Central.
* Acceder mediante programación a la aplicación IoT de IoT Central mediante las API de IoT Central.

### <a name="business-applications"></a>Aplicaciones empresariales

Puede usar datos de IoT para crear varias aplicaciones empresariales dentro de una utilidad de agua. En la [aplicación de supervisión de consumo de agua de IoT Central](tutorial-water-consumption-monitoring.md) puede configurar reglas y acciones, y establecer que creen alertas en [Connected Field Service](/dynamics365/field-service/connected-field-service). Configure Power Automate en reglas de IoT Central para automatizar los flujos de trabajo entre aplicaciones y servicios. Además, en función de las actividades de los servicios de Connected Field Service, la información se puede devolver a Azure IoT Central.

En este tutorial, aprenderá a:

> [!div class="checklist"]

> * Usar la plantilla **Water quality monitoring** para crear una aplicación de supervisión de la calidad del agua
> * Explore y personalice un panel.
> * Explorar una plantilla de dispositivo de supervisión de la calidad del agua
> * Explorar los dispositivos simulados
> * Explorar y configurar reglas
> * Configurar trabajos
> * Realizar la personalización de marca de la aplicación mediante etiquetado.

## <a name="prerequisites"></a>Requisitos previos

* No se necesitan requisitos previos específicos para implementar esta aplicación.
* Puede usar el plan gratuito o una suscripción de Azure.

## <a name="create-water-quality-monitoring-application"></a>Creación de una aplicación de supervisión de calidad del agua

Cree la aplicación mediante los pasos siguientes:

1. Navegue al sitio de [Compilación Azure IoT Central](https://aka.ms/iotcentral). Después, inicie sesión con una cuenta Microsoft personal, profesional o educativa. Seleccione **Compilar** en la barra de navegación de la izquierda y la pestaña **Gobierno**: :::image type="content" source="media/tutorial-waterqualitymonitoring/iot-central-government-tab-overview1.png" alt-text="Plantilla de aplicación":::

1. Seleccione **Crear aplicación** en **Supervisión de la calidad del agua**.

Para más información, consulte [Creación de una aplicación de Azure IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Recorrido por la aplicación.

Las secciones siguientes le guiarán por las características clave de la aplicación:

### <a name="dashboard"></a>Panel

Después de crear la aplicación, se abre el panel **Wide World water quality dashboard**.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-dashboard1.png" alt-text="Panel de supervisión de la calidad del agua":::

Como desarrollador, puede crear y personalizar las vistas del panel para los operadores. Pero antes de probar la personalización, explore primero el panel.

Todos los datos que se muestran en el panel se basan en los datos del dispositivo simulado, que se tratarán en la sección siguiente.

El panel incluye los siguientes tipos de iconos:

* **Icono de imagen del servicio de agua Wide World**: el primer icono de la esquina superior izquierda del panel es una imagen que muestra el servicio de agua ficticio llamado Wide World. Puede personalizar el icono para usar su propia imagen o puede quitar el icono.

* **Iconos de KPI de pH medio**: los iconos de KPI como **Average pH in the last 30 minutes** se encuentran en la parte superior del panel. Puede personalizar los iconos de KPI y establecerlos en un tipo y un intervalo de tiempo diferentes.

* **Mapa del área de supervisión del agua**: Azure IoT Central usa Azure Maps, que puede establecer directamente en la aplicación para mostrar la [ubicación](../core/howto-use-location-data.md) del dispositivo. También puede asignar información de ubicación de la aplicación al dispositivo y, luego, usar Azure Maps para mostrar la información en un mapa. Mantenga el mouse sobre el mapa y pruebe los controles.

* **Gráfico de mapa término de distribución de pH medio**: puede seleccionar diferentes gráficos de visualización para mostrar la telemetría del dispositivo de la manera más adecuada para su aplicación.

* **Gráfico de líneas de Critical quality indicators**: puede visualizar la telemetría del dispositivo representada en un gráfico de líneas durante un intervalo de tiempo.  

* **Gráfico de barras de Concentration of chemical agents**: puede visualizar la telemetría del dispositivo en un gráfico de barras.

* **Reset sensors parameters tile** (Restablecer icono de parámetros de sensores): el panel incluye un icono de las acciones que puede iniciar un operador directamente desde el panel de supervisión. Un ejemplo de tales acciones podría ser el restablecimiento de las propiedades de un dispositivo.

* **Iconos de la lista de propiedades**: el panel incluye varios iconos de propiedades que representan la información de umbral, la información de estado del dispositivo y la información de mantenimiento.

### <a name="customize-the-dashboard"></a>Personalización del panel

Como desarrollador, puede personalizar las vistas del panel para los operadores.

1. Seleccione **Edit** (Editar) para personalizar el panel **Wide World water quality dashboard**. Para personalizar el panel, seleccione comandos en el menú **Edit** (Editar). Después de que el panel esté en modo de edición, puede agregar nuevos iconos o configurar los archivos existentes.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/edit-dashboard.png" alt-text="Edición del panel":::

1. Seleccione **+ New** (+ Nuevo) para crear un panel que pueda configurar. Puede tener varios paneles y navegar entre ellos desde el menú del panel.

## <a name="explore-a-water-quality-monitoring-device-template"></a>Exploración de una plantilla de dispositivo de supervisión de la calidad del agua

Una plantilla de dispositivo de Azure IoT Central define las funcionalidades de un dispositivo. Las funcionalidades disponibles son la telemetría, las propiedades y los comandos. Como desarrollador, puede definir plantillas de dispositivo en Azure IoT Central que representen las funcionalidades de los dispositivos conectados. También puede crear dispositivos simulados para probar la aplicación y la plantilla de dispositivo.

La aplicación de supervisión de la calidad del agua que creó incluye una plantilla de dispositivo de supervisión de la calidad del agua.

Para ver la plantilla de dispositivo, siga estos pasos:

1. Seleccione **Device templates** (Plantillas de dispositivo) en el panel izquierdo de la aplicación en Azure IoT Central.
1. En la lista de plantillas de dispositivo, seleccione **Water Quality Monitor** (Monitor de calidad del agua) para abrirla.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template.png" alt-text="La plantilla de dispositivo":::

### <a name="customize-the-device-template"></a>Personalización de la plantilla de dispositivo

Practique la personalización de la siguiente configuración de la plantilla de dispositivo:

1. En el menú de la plantilla de dispositivo, seleccione **Customize** (Personalizar).
1. Vaya al tipo de telemetría **Temperature** (Temperatura).
1. Cambie el valor de **Display name** (Nombre para mostrar) a **Reported temperature** (Temperatura notificada).
1. Cambie la unidad de medida o establezca **Min value** (Valor mínimo) y **Max value** (Valor máximo).
1. Seleccione **Guardar**.

#### <a name="add-a-cloud-property"></a>Adición de una propiedad de la nube

1. En el menú de la plantilla de dispositivo, seleccione **Propiedades de nube**.
1. Para agregar una nueva propiedad de la nube, seleccione **+ Add Cloud Property** (+ Agregar propiedad de la nube). En Azure IoT Central, puede agregar a un dispositivo una propiedad pertinente, aunque no se espera que la envíe el dispositivo. Un ejemplo de este tipo de propiedad sería un umbral de alerta específico del área de instalación, información de recursos o información de mantenimiento.
1. Escriba **Área de instalación** en **Nombre para mostrar** y elija **Cadena** en **Esquema**.
1. Seleccione **Guardar**.

### <a name="explore-views"></a>Exploración de las vistas

La plantilla de dispositivo de supervisión de la calidad del agua incluye vistas predefinidas. Las vistas definen cómo verán los operadores los datos del dispositivo y establecen las propiedades de la nube. Explore las vistas y practique la realización de cambios.

:::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-device-template-views.png" alt-text="Vistas de la plantilla de dispositivo":::

### <a name="publish-the-device-template"></a>Publicación de la plantilla de dispositivo

Si realiza algún cambio, asegúrese de seleccionar **Publish** (Publicar) para publicar la plantilla de dispositivo.

### <a name="create-a-new-device-template"></a>Creación de una nueva plantilla de dispositivo

1. En la página **Plantillas de dispositivo**, seleccione **+Nuevo** para crear una plantilla de dispositivo y siga el proceso de creación.
1. Cree una plantilla de dispositivo personalizada o elija una del catálogo de dispositivos Azure IoT.

## <a name="explore-simulated-devices"></a>Explorar los dispositivos simulados

La aplicación de supervisión de la calidad del agua que creó con la plantilla de aplicación tiene dos dispositivos simulados. Estos dispositivos se asignan a la plantilla de dispositivo de supervisión de la calidad del agua.

### <a name="view-the-devices"></a>Visualización de los dispositivos

1. Seleccione **Devices** (Dispositivos) en el panel izquierdo de la aplicación.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-devices.png" alt-text="Dispositivos":::

1. Seleccione un dispositivo simulado.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitor-device1.png" alt-text="Selección del dispositivo 1":::

1. En la pestaña **Propiedades de nube**, cambie el valor de **Acidity (pH) threshold** (Umbral de acidez [pH]) de **8** a **9** y seleccione **Guardar**.
1. Explore las pestañas **Device Properties** (Propiedades del dispositivo) **Device Dashboard** (Panel del dispositivo).

> [!NOTE]
> Todas las pestañas se han configurado desde las **vistas de plantilla de dispositivo**.

### <a name="add-new-devices"></a>Adición de nuevos dispositivos

1. En la pestaña **Devices** (Dispositivos), seleccione **+ New** )+ Nuevo) para agregar un nuevo dispositivo.
1. Use el **identificador de dispositivo** sugerido o escriba el suyo propio. También, en **Nombre de dispositivo**, puede escribir un nombre para el nuevo dispositivo.
1. Seleccione **Water Quality Monitor** (Monitor de calidad del agua) en **Plantilla de dispositivo**. 
1. Asegúrese de que **Simulate this device** (Simular este dispositivo) está establecido en **Sí** si quiere crear un dispositivo simulado. 
1. Seleccione **Crear**.  

## <a name="explore-and-configure-rules"></a>Exploración y configuración de reglas

En Azure IoT Central, puede crear reglas que supervisen automáticamente la telemetría del dispositivo. Estas reglas desencadenan una acción cuando se cumple cualquiera de sus condiciones. Una posible acción es enviar notificaciones por correo electrónico. Otras posibilidades incluyen una acción de Power Automate o una acción de webhook para enviar datos a otros servicios.

La aplicación de supervisión de la calidad del agua que ha creado tiene dos reglas preconfiguradas.

### <a name="view-rules"></a>Visualización de reglas

1. Seleccione **Rules** (Reglas) en el panel izquierdo de la aplicación.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-rules.png" alt-text="Reglas":::

1. Seleccione **High pH alert**, que es una de las reglas preconfiguradas en la aplicación.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-high-ph-alert.png" alt-text="La regla de alerta de pH alto":::

   La regla **High pH alert** está configurada para comprobar la condición de acidez (pH) que sea superior a 8.

A continuación, agregue una acción de correo electrónico a la regla:

1. Seleccione **+ Email** (+ Correo electrónico).
1. En el cuadro **Display name** (Nombre para mostrar), escriba **High pH alert**.
1. En el cuadro **To** (Para), escriba la dirección de correo electrónico asociada a la cuenta de Azure IoT Central.
1. Tiene la opción de escribir una nota e incluirla en el texto del correo electrónico.
1. Seleccione **Done** (Listo) para completar la acción.
1. Establezca la regla en **Habilitado** y seleccione **Guardar**.

A los pocos minutos de que se cumpla la condición configurada, recibirá un correo electrónico.

> [!NOTE]
> La aplicación envía un correo electrónico cada vez que se cumple una condición. Seleccione **Disable** (Deshabilitar) en una regla para dejar de recibir correo electrónico automatizado de esa regla.
  
Para crear una regla, seleccione **Rules** (Reglas) en el panel izquierdo de la aplicación y, a continuación, seleccione **+ New** (+ Nueva).

## <a name="configure-jobs"></a>Trabajos de configuración

Con los trabajos de Azure IoT Central, puede desencadenar actualizaciones de propiedades de dispositivo o de la nube en varios dispositivos. También puede usar trabajos para desencadenar comandos de dispositivo en varios dispositivos. Azure IoT Central automatiza el flujo de trabajo.

1. Seleccione **Jobs** (Trabajos) en el panel izquierdo de la aplicación.
1. Seleccione **+Nuevo trabajo** y configure uno o varios trabajos.

## <a name="customize-your-application"></a>Personalización de la aplicación

Como creador, puede cambiar varias opciones de configuración para personalizar la experiencia del usuario en la aplicación.

1. Seleccione **Administration** > **Customize your application** (Administración > Personalizar la aplicación).
1. En **Logotipo de encabezado**, seleccione **Cambiar** para elegir la imagen que quiere cargar como logotipo.
1. En **Browser icon** (Icono del explorador), seleccione **Change** (Cambiar) para elegir la imagen que aparece en las pestañas del explorador.
1. En **Browser colors** (Colores del explorador), puede reemplazar los valores predeterminados por códigos de color HTML hexadecimales.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-customize-your-application1.png" alt-text="Personalización de la aplicación":::

### <a name="update-the-application-image"></a>Actualización de la imagen de la aplicación

1. Seleccione **Administración**  > **Su aplicación**.

1. Seleccione **Cambiar** para elegir una imagen para cargar como imagen de la aplicación.

## <a name="clean-up-resources"></a>Limpieza de recursos

Si no va a seguir usando la aplicación, elimínela mediante los siguientes pasos:

1. Abra la pestaña **Administration** (Administración) del panel izquierdo de la aplicación.
1. Seleccione **Su aplicación** y elija el botón **Eliminar**.

    :::image type="content" source="media/tutorial-waterqualitymonitoring/water-quality-monitoring-application-settings-delete-app1.png" alt-text="Eliminación de la aplicación":::
