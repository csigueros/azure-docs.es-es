---
title: 'Tutorial: análisis en la tienda de Azure IoT | Microsoft Docs'
description: En este tutorial se muestra cómo implementar, crear y usar una aplicación de comercio minorista de análisis en la tienda en IoT Central.
services: iot-central
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.custom:
- iot-storeAnalytics-checkout
- iot-p0-scenario
ms.author: timlt
author: timlt
ms.date: 08/17/2021
ms.openlocfilehash: 93438e9726dca4fe74da3bbfc9bd06533115e74e
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2021
ms.locfileid: "123439149"
---
# <a name="tutorial-deploy-and-walk-through-the-in-store-analytics-application-template"></a>Tutorial: Implementación y recorrido por la plantilla de aplicación de análisis en la tienda

Use la plantilla de aplicación de *análisis en la tienda* de IoT Central y las instrucciones de este artículo para desarrollar una solución de análisis en la tienda integral.

:::image type="content" source="media/tutorial-in-store-analytics-create-app/store-analytics-architecture-frame.png" alt-text="Análisis en la tienda de Azure IoT Central.":::

- Conjunto de sensores de IoT que envían datos de telemetría a un dispositivo de puerta de enlace
- Dispositivos de puerta de enlace que envían telemetría y conclusiones agregadas a IoT Central
- Exportación continua de datos al servicio de Azure que se quiera para su manipulación
- Los datos se pueden estructurar en el formato que se quiera y enviarse a un servicio de almacenamiento
- Las aplicaciones empresariales pueden consultar datos y generar conclusiones que impulsen las operaciones comerciales

Echemos un vistazo a los componentes clave que generalmente desempeñan un papel en una solución de análisis en tienda.

## <a name="condition-monitoring-sensors"></a>Sensores de supervisión del estado

Una solución de IoT comienza con un conjunto de sensores que capturan señales significativas desde dentro de un entorno de comercio minorista. Se refleja en diferentes tipos de sensores en el extremo izquierdo del diagrama de arquitectura anterior.

## <a name="gateway-devices"></a>Dispositivos de puerta de enlace

Muchos sensores de IoT pueden alimentar señales sin procesar directamente en la nube o en un dispositivo de puerta de enlace situado cerca de ellas. El dispositivo de puerta de enlace realiza la agregación de datos en el perímetro antes de enviar conclusiones de resumen a una aplicación de IoT Central. Los dispositivos de puerta de enlace también son responsables de la retransmisión de operaciones de comando y control a los dispositivos de sensor cuando proceda. 

## <a name="iot-central-application"></a>Aplicación de IoT Central

La aplicación de Azure IoT Central ingiere datos de distintos tipos sensores de IoT, así como dispositivos de puerta de enlace, en el entorno de comercio minorista y genera un conjunto de conclusiones significativas.

Azure IoT Central también ofrece una experiencia personalizada para el operador de tienda que le permite supervisar y administrar de forma remota los dispositivos de infraestructura.

## <a name="data-transform"></a>Transformación de datos

Puede configurarse la aplicación de Azure IoT Central dentro de una solución para exportar conclusiones sin procesar o agregadas a un conjunto de servicios de plataforma como servicio (PaaS) de Azure que pueden realizar manipulación de datos y enriquecer estas conclusiones antes de colocarlas en una aplicación empresarial. 

## <a name="business-application"></a>Aplicación empresarial

Los datos de IoT se pueden usar para potenciar diferentes tipos de aplicaciones empresariales implementadas en un entorno minorista. El administrador o un miembro del personal de la tienda minorista pueden usar estas aplicaciones para visualizar información empresariales y tomar medidas significativas en tiempo real. Para obtener información sobre cómo crear un panel de Power BI en tiempo real para el equipo comercial, consulte el [tutorial](./tutorial-in-store-analytics-create-app.md).

En este tutorial, aprenderá a:
> [!div class="checklist"]
>
> - Usar la plantilla **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) de Azure IoT Central para crear una aplicación de tienda de venta al por menor
> - Personalizar la configuración de la aplicación
> - Crear y personalizar plantillas de dispositivo IoT
> - Conectar dispositivos a la aplicación
> - Agregar reglas y acciones para supervisar las condiciones

## <a name="prerequisites"></a>Prerrequisitos

- No se necesitan requisitos previos específicos para implementar esta aplicación.
- Puede usar el plan gratuito o una suscripción de Azure.

## <a name="create-in-store-analytics-application"></a>Creación de una aplicación de análisis en la tienda

Cree la aplicación mediante los pasos siguientes:

1. Navegue al sitio de [Compilación Azure IoT Central](https://aka.ms/iotcentral). Después, inicie sesión con una cuenta Microsoft personal, profesional o educativa. Seleccione **Compilar** en la barra de navegación de la izquierda y la pestaña **Venta al por menor**: :::image type="content" source="media/tutorial-in-store-analytics-create-app/iotc-retail-homepage.png" alt-text="Connected logistics template"::: (Plantilla de logística conectada).

1. Seleccione **Crear aplicación** en **In-store analytics - checkout** (Análisis en la tienda: finalización de la compra).

Para obtener más información, vea [Creación de una aplicación de Azure IoT Central](../core/howto-create-iot-central-application.md).

## <a name="walk-through-the-application"></a>Recorrido por la aplicación.

Las secciones siguientes le guiarán por las características clave de la aplicación:

### <a name="customize-application-settings"></a>Personalización de la configuración de la aplicación

Puede cambiar varias opciones de configuración para personalizar la experiencia del usuario en la aplicación. En esta sección, seleccionará un tema de aplicación predefinido. Opcionalmente, aprenderá a crear un tema personalizado y a actualizar la imagen de la aplicación. Un tema personalizado le permite establecer los colores del explorador de la aplicación, el icono del explorador y el logotipo de la aplicación que aparece en la cabecera.

Para seleccionar un tema de aplicación predefinido:

1. Seleccione **Settings** (Configuración) en la cabecera.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/settings-icon.png" alt-text="Configuración de la aplicación de Azure IoT Central.":::

2. Seleccione un nuevo **Theme** (Tema).

3. Seleccione **Guardar**.

En lugar de usar un tema predefinido, puede crear un tema personalizado. Si desea usar un conjunto de imágenes de ejemplo para personalizar la aplicación y completar el tutorial, descargue las [imágenes de ejemplo de Contoso](https://github.com/Azure-Samples/iot-central-docs-samples/tree/master/retail).

Para crear un tema personalizado:

1. Expanda el panel izquierdo, si aún no está expandido.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/dashboard-expand.png" alt-text="Panel izquierdo de Azure IoT Central.":::

1. Seleccione **Administration > Customize your application** (Administración > Personalizar la aplicación).

1. Use el botón **Change** (Cambiar) para elegir una imagen para cargar en el campo **Application logo** (Logotipo de la aplicación). Opcionalmente, especifique un valor para **Logo alt text** (Texto alternativo del logotipo). 

1. Use el botón **Change** (Cambiar) para elegir una imagen para **Browser icon** (Icono del explorador) que aparecerá en las pestañas del explorador.

1. Opcionalmente, reemplace los valores de **Browser colors** (Colores del explorador) mediante la adición de códigos de color HTML en hexadecimal. En **Header** (Encabezado), agregue *#008575*.  En **Accent** (Resaltar), agregue *#A1F3EA*. 

1. Seleccione **Guardar**. 

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/select-application-logo.png" alt-text="Logotipo personalizado de Azure IoT Central.":::

    Después de guardar, la aplicación actualiza los colores del explorador, el logotipo de la cabecera y el icono del explorador.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/saved-application-settings.png" alt-text="Configuración de la aplicación actualizada de Azure IoT Central.":::

Para actualizar la imagen de la aplicación:

1. Seleccione **Administration > Application settings** (Administración > Configuración de la aplicación).

1. Use el botón **Select image** (Seleccionar imagen) para elegir una imagen para cargar como la imagen de la aplicación. Esta imagen aparece en el icono de la aplicación en la página **My Apps** (Mis aplicaciones) del administrador de aplicaciones de IoT Central.

1. Seleccione **Guardar**.

1. Opcionalmente, vaya a la vista **My Apps** (Mis aplicaciones) del sitio web del [administrador de aplicaciones de Azure IoT Central](https://aka.ms/iotcentral). El icono de la aplicación muestra la imagen de la aplicación actualizada.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/customize-application-image.png" alt-text="Imagen de la aplicación personalizada de Azure IoT Central.":::

### <a name="create-device-templates"></a>Creación de plantillas de dispositivo

Puede crear plantillas de dispositivo que le permitan a usted y a los operadores de la aplicación configurar y administrar los dispositivos. Para crear una plantilla se puede crear una personalizada, importar un archivo de plantilla existente o importar una plantilla desde el catálogo de dispositivos IoT de Azure. Después de crear y personalizar una plantilla de dispositivo, úsela para conectar dispositivos reales a la aplicación. También puede usar una plantilla de dispositivo para generar dispositivos simulados para realizar pruebas.

La plantilla de aplicación **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) tiene plantillas de dispositivo para varios dispositivos.  Hay plantillas de dispositivo para dos de los tres dispositivos que se usan en la aplicación. La plantilla del dispositivo RuuviTag no se incluye en la plantilla de aplicación **In-store analytics - checkout** (Análisis en tienda: finalización de la compra). En esta sección, agregará una plantilla de dispositivo para los sensores RuuviTag a la aplicación.

Para agregar una plantilla de dispositivo RuuviTag a la aplicación:

1. Seleccione **Device Templates** (Plantillas de dispositivo) en el panel izquierdo.

1. Seleccione **+ New** (+ Nuevo) para crear una nueva plantilla de dispositivo.

1. Busque y seleccione la plantilla del dispositivo multisensor **RuuviTag** en el catálogo de dispositivos de Azure IoT. 

1. Seleccione **Siguiente: Customize** (Personalizar)

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template.png" alt-text="Captura de pantalla que resalta el botón Next: Customize (Siguiente: Personalizar).":::

1. Seleccione **Crear**. La aplicación agrega la plantilla del dispositivo RuuviTag.

1. Seleccione **Device templates** (Plantillas de dispositivo) en el panel izquierdo. En la página se muestran todas las plantillas de dispositivo incluidas en la plantilla de aplicación y la plantilla del dispositivo RuuviTag que acaba de agregar.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/device-templates-list.png" alt-text="Plantilla de dispositivo del sensor RuuviTag de Azure IoT Central.":::

### <a name="customize-device-templates"></a>Personalización de plantillas de dispositivo

Puede personalizar las plantillas de dispositivo en la aplicación de tres maneras. En primer lugar, puede personalizar las interfaces nativas integradas en los dispositivos cambiando para ello las funcionalidades del dispositivo. Por ejemplo, con un sensor de temperatura, puede cambiar detalles como el nombre para mostrar de la interfaz de temperatura, el tipo de datos, las unidades de medida y los intervalos de funcionamiento mínimo y máximo. 

En segundo lugar, puede personalizar las plantillas de dispositivo mediante la adición de propiedades en la nube. Las propiedades en la nube no forman parte de las funcionalidades integradas del dispositivo. Las propiedades en la nube son datos personalizados que la aplicación de Azure IoT Central crea, almacena y asocia a los dispositivos. Un ejemplo de una propiedad en la nube podría ser un valor calculado o metadatos, como una ubicación que desea asociar a un conjunto de dispositivos.

En tercer lugar, puede personalizar las plantillas de dispositivo mediante la creación de vistas personalizadas. Las vistas proporcionan una manera para que los operadores visualicen los datos de telemetría y los metadatos de los dispositivos, como las métricas del dispositivo y el estado.

Aquí se usan los dos primeros métodos para personalizar la plantilla de dispositivo de los sensores RuuviTag.

Para personalizar las interfaces integradas de la plantilla del dispositivo RuuviTag:

1. Seleccione **Device Templates** (Plantillas de dispositivo) en el panel izquierdo. 

1. Seleccione la plantilla de los sensores RuuviTag. 

1. Oculte el panel izquierdo. La vista Resumen de la plantilla muestra las funcionalidades del dispositivo.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-summary-view.png" alt-text="Vista de resumen de la plantilla del dispositivo RuuviTag de Azure IoT Central.":::

1. Seleccione **Customize** (Personalizar) en el menú de la plantilla del dispositivo RuuviTag. 

1. Desplácese por la lista de funcionalidades y busque el tipo de telemetría `humidity`. Es el elemento de fila con el campo **Display name** (Nombre para mostrar) editable para *humidity* (humedad).

En los pasos siguientes, se personaliza el tipo de telemetría `humidity` para los sensores RuuviTag. Opcionalmente, puede personalizar algunos de los demás tipos de telemetría.

En el caso del tipo de telemetría `humidity`, realice los cambios siguientes:

1. Seleccione el control **Expand** (Expandir) para expandir los detalles del esquema de la fila.

1. Actualice el valor de **Display Name** (Nombre para mostrar) de *humedad* a un valor personalizado como *Humedad relativa*.

1. Cambie la opción **Semantic Type** (Tipo de semántica) de *None* (Ninguno) a *Humidity* (Humedad).  Opcionalmente, establezca los valores de esquema para el tipo de telemetría de humedad en la vista de esquema expandida. La configuración de esquema le permite crear requisitos de validación detallados para los datos de los que los sensores realizan un seguimiento. Por ejemplo, puede establecer los valores de intervalo de funcionamiento mínimo y máximo para una interfaz determinada.

1. Haga clic en **Guardar** para guardar los cambios.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-customize.png" alt-text="Captura de pantalla que muestra la pantalla para personalizar y resalta el botón para guardar.":::

Para agregar una propiedad en la nube a una plantilla de dispositivo en la aplicación:

1. Seleccione **Cloud Properties** (Propiedades en la nube) en el menú de la plantilla del dispositivo RuuviTag.

1. Seleccione **Add Cloud Property** (Agregar propiedad en la nube). 

Especifique los siguientes valores para crear una propiedad personalizada para almacenar la ubicación de cada dispositivo:

1. Escriba el valor *Ubicación* en el campo **Display Name** (Nombre para mostrar). Este valor se copia automáticamente en el campo **Name** (Nombre), que es un nombre descriptivo para la propiedad. Puede usar el valor copiado o cambiarlo.

1. Seleccione *String* (Cadena) en el desplegable **Schema** (Esquema). Un tipo de cadena permite asociar una cadena de nombre de ubicación a cualquier dispositivo basado en la plantilla. Por ejemplo, puede asociar un área de una tienda a cada dispositivo.

1. Establezca **Minimum Length** (Longitud mínima) en *2*. 

1. Establezca **Trim Whitespace** (Recortar espacios en blanco) en **On**.

1. Seleccione **Save** (Guardar) para guardar la propiedad en la nube personalizada.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/ruuvitag-device-template-cloud-property.png" alt-text="Personalización de la plantilla de dispositivo RuuviTag de Azure IoT Central.":::

1. Seleccione **Publicar**. 

    La publicación de una plantilla de dispositivo hace que sea visible para los operadores de la aplicación. Después de publicar una plantilla, úsela para generar dispositivos simulados para realizar pruebas o para conectar dispositivos reales a la aplicación. Si ya tiene dispositivos conectados a la aplicación, al publicar una plantilla personalizada se envían los cambios a los dispositivos.

### <a name="add-devices"></a>Adición de dispositivos

Después de crear y personalizar las plantillas de dispositivo, es el momento de agregar dispositivos. 

En este tutorial, se usa el siguiente conjunto de dispositivos reales y simulados para crear la aplicación:

- Una puerta de enlace Rigado C500 real
- Dos sensores RuuviTag reales
- Un sensor de **Ocupación** simulado. La plantilla de aplicación incluye el sensor simulado, por lo que no es necesario crearlo. 

> [!NOTE]
> Si no tiene dispositivos reales, puede crear sensores RuuviTag simulados para seguir usando este tutorial. Las siguientes instrucciones incluyen los pasos para crear un sensor RuuviTag simulado. No es necesario crear una puerta de enlace simulada.

Complete los pasos de los dos artículos siguientes para conectar una puerta de enlace Rigado real y sensores RuuviTag. Cuando haya terminado, vuelva a este tutorial. Dado que ya ha creado las plantillas de dispositivo en este tutorial, no es necesario crearlas de nuevo en el siguiente conjunto de instrucciones.

- Para conectar una puerta de enlace Rigado, consulte [Conexión de una puerta de enlace Rigado Cascade 500 a la aplicación de Azure IoT Central](../core/howto-connect-rigado-cascade-500.md).
- Para conectar sensores RuuviTag, consulte [Conexión de un sensor RuuviTag a la aplicación de Azure IoT Central](../core/howto-connect-ruuvi.md). También puede usar estas instrucciones para crear dos sensores simulados, si es necesario.

### <a name="add-rules-and-actions"></a>Adición de reglas y acciones

Como parte del uso de sensores en la aplicación de Azure IoT Central para supervisar las condiciones, puede crear reglas para ejecutar acciones cuando se cumplan determinadas condiciones. Una regla está asociada a una plantilla de dispositivo y a uno o varios dispositivos y contiene condiciones que deben cumplirse en función de los eventos o los datos de telemetría del dispositivo. Una regla también tiene una o más acciones asociadas. Las acciones pueden incluir el envío de notificaciones por correo electrónico o el desencadenamiento de una acción de webhook para enviar datos a otros servicios. La plantilla de aplicación **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) incluye algunas reglas predefinidas para los dispositivos de la aplicación.

En esta sección, creará una nueva regla que comprueba el nivel de humedad relativa máximo en función de los datos de telemetría del sensor RuuviTag. Agregará una acción a la regla para que, si la humedad supera el máximo, la aplicación envíe un correo electrónico. 

Para crear una regla: 

1. Expanda el panel izquierdo.

1. Seleccione **Reglas**.

1. Seleccione **+ Nuevo**.

1. Escriba *Nivel de humedad* como el nombre de la regla. 

1. Seleccione la plantilla del dispositivo RuuviTag en **Scopes** (Ámbitos). La regla que defina se aplicará a todos los sensores basados en esa plantilla. Opcionalmente, puede crear un filtro que aplique la regla solo a un subconjunto definido de sensores. 

1. Elija `Relative humidity` para el campo **Telemetry** (Telemetría). Esta es la funcionalidad del dispositivo que ha personalizado en un paso anterior.

1. Elija `Is greater than` para **Operator** (Operador). 

1. Especifique un nivel de humedad interior de un rango superior típico para el entorno como **Value** (Valor). Por ejemplo, escriba *65*. Ha establecido una condición para la regla que se produce cuando la humedad relativa de cualquier sensor RuuviTag real o simulado supera este valor. Es posible que tenga que ajustar el valor hacia arriba o hacia abajo según el intervalo de humedad normal del entorno.  

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/rules-add-conditions.png" alt-text="Agregar condiciones de regla de Azure IoT Central.":::

Para agregar una acción a la regla:

1. Seleccione **+ Email** (+ Correo electrónico).

1. Escriba *Notificación de humedad alta* en el campo **Display name** (Nombre para mostrar) de la acción. 

1. Escriba la dirección de correo electrónico asociada a su cuenta en el campo **To** (Para). Si usa un correo electrónico diferente, la dirección que use debe ser de un usuario que se haya agregado a la aplicación. El usuario también debe iniciar y cerrar sesión al menos una vez.

1. Opcionalmente, escriba una nota para incluirla en el texto del correo electrónico.

1. Seleccione **Done** (Listo) para completar la acción.

    :::image type="content" source="media/tutorial-in-store-analytics-create-app/rules-add-action.png" alt-text="Agregar acciones a reglas de Azure IoT Central.":::

1. Seleccione **Save** (Guardar) para guardar y activar la nueva regla. 

    En unos minutos, la cuenta de correo electrónico especificada debe empezar a recibir correos electrónicos. La aplicación envía un correo electrónico cada vez que un sensor indica que el nivel de humedad superó el valor de la condición.

## <a name="clean-up-resources"></a>Limpieza de recursos

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido a:

* Usar la plantilla **In-store analytics - checkout** (Análisis en tienda: finalización de la compra) de Azure IoT Central para crear una aplicación de tienda de venta al por menor
* Personalizar la configuración de la aplicación
* Crear y personalizar plantillas de dispositivo IoT
* Conectar dispositivos a la aplicación
* Agregar reglas y acciones para supervisar las condiciones

Ahora que ha creado una aplicación de supervisión de las condiciones de Azure IoT Central, este es el siguiente paso sugerido:

> [!div class="nextstepaction"]
> [Personalización del panel](./tutorial-in-store-analytics-customize-dashboard.md)
