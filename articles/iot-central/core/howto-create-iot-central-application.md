---
title: Creación de una aplicación de IoT Central | Microsoft Docs
description: En este artículo se describen las opciones para crear una aplicación de IoT Central desde el sitio de Azure IoT Central, Azure Portal y un entorno de línea de comandos.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/11/2021
ms.topic: how-to
ms.openlocfilehash: 70567f3cb1a80c7d23d19bf7de43c80723af0dfe
ms.sourcegitcommit: 61e7a030463debf6ea614c7ad32f7f0a680f902d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/28/2021
ms.locfileid: "129094010"
---
# <a name="create-an-iot-central-application"></a>Creación de una aplicación de IoT Central

Para crear una aplicación de IoT Central tiene varias opciones. Puede usar uno de los métodos basados en interfaz gráfica de usuario si prefiere un enfoque manual, o uno de los métodos mediante programación o la CLI si quiere automatizar el proceso.

Sea cual sea el enfoque que elija, las opciones de configuración son las mismas y el proceso normalmente tarda menos de un minuto en completarse.

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="options"></a>Opciones

En esta sección se describen las opciones disponibles al crear una aplicación de IoT Central. Según el método que elija, es posible que tenga que proporcionar las opciones en un formulario o como parámetros de una línea de comandos:

### <a name="pricing-plans"></a>Panes de tarifa

El plan *Gratuito* le permite crear una aplicación de IoT Central para realizar una prueba durante siete días. El plan gratuito:

- No requiere una suscripción a Azure.
- Solo se puede crear y administrar en el sitio de [Azure IoT Central](https://aka.ms/iotcentral).
- Permite conectar hasta cinco dispositivos.
- Se puede actualizar a un plan estándar si quiere conservar la aplicación.

Los planes *estándar*:

- Requieren una suscripción a Azure. Debe tener como mínimo acceso de **colaborador** en la suscripción a Azure. Si creó la suscripción usted mismo, automáticamente será un administrador con acceso suficiente. Para más información, consulte [¿Qué es el control de acceso basado en rol de Azure?](../../role-based-access-control/overview.md)
- Permiten crear y administrar aplicaciones de IoT Central mediante cualquiera de los métodos disponibles.
- Permiten conectar tantos dispositivos como necesite. Se le facturará por dispositivo. Para saber más, consulte [Precios de Azure IoT Central](https://azure.microsoft.com/pricing/details/iot-central/).
- No se pueden degradar a un plan gratuito, pero se pueden actualizar o degradar a otros planes estándar.

En la tabla siguiente se resumen las diferencias entre los tres planes estándar:

| Nombre del plan | Dispositivos gratuitos | Mensajes/mes | Caso de uso |
| --------- | ------------ | -------------- | -------- |
| S0        | 2            | 400            | Unos cuantos mensajes al día |
| S1        | 2            | 5\.000          | Unos cuantos mensajes por hora |
| S2        | 2            | 30,000         | Mensajes cada pocos minutos |

### <a name="application-name"></a>Nombre de la aplicación

El _nombre de la aplicación_ que elija aparece en la barra de título de cada página de la aplicación de IoT Central. También aparece en el icono de la aplicación en la página **Aplicaciones** del sitio de [Azure IoT Central](https://aka.ms/iotcentral).

El _subdominio_ que elija identifica de manera única la aplicación. El subdominio forma parte de la URL que se usa para acceder a la aplicación. La URL de una aplicación de IoT Central es similar a `https://yoursubdomain.azureiotcentral.com`.

### <a name="application-template-id"></a>Identificador de plantilla de la aplicación

La plantilla de aplicación que elija determina el contenido inicial de la aplicación, como paneles y plantillas de dispositivo. Para una aplicación personalizada, use `iotc-pnp-preview` como identificador de plantilla.

Para más información sobre las plantillas de aplicación personalizadas y centradas en el sector, consulte [¿Qué son las plantillas de aplicación?](concepts-app-templates.md)

### <a name="billing-information"></a>Información de facturación

Si elige uno de los planes estándar, debe proporcionar información de facturación:

- La suscripción a Azure que está usando.
- El directorio que contiene la suscripción que está usando.
- La ubicación en la que se hospedará la aplicación. IoT Central utiliza regiones de Azure como ubicaciones: Este de Australia, Centro de EE. UU., Este de EE. UU., Este de EE. UU. 2, Japón Oriental, Norte de Europa, Sudeste Asiático, Sur de Reino Unido, Oeste de Europa, Oeste de EE. UU.

## <a name="azure-iot-central-site"></a>Sitio de Azure IoT Central

La manera más fácil de empezar a crear aplicaciones de IoT Central es en el sitio de [Azure IoT Central](https://aka.ms/iotcentral).

La sección [Compilación](https://apps.azureiotcentral.com/build) le permite seleccionar la plantilla de aplicación que quiere usar:

:::image type="content" source="media/howto-create-iot-central-application/choose-template.png" alt-text="Captura de pantalla de la página Compilación que le permite elegir una plantilla de aplicación.":::

Si selecciona **Crear aplicación**, puede proporcionar la información necesaria para crear una aplicación a partir de la plantilla:

:::image type="content" source="media/howto-create-iot-central-application/create-application.png" alt-text="Captura de pantalla que muestra la página de creación de aplicación de IoT Central.":::

La página **Aplicaciones** enumera todas las aplicaciones de IoT Central a las que tiene acceso. La lista incluye las aplicaciones que ha creado y las aplicaciones a las que se le ha concedido acceso.

> [!TIP]
> Todas las aplicaciones que cree mediante un plan de precios estándar en el sitio de Azure IoT Central usan el grupo de recursos **IOTC** de su suscripción. Los enfoques que se describen en la siguiente sección le permiten elegir un grupo de recursos.

## <a name="copy-an-application"></a>Copia de una aplicación

Puede crear una copia de cualquier aplicación, menos las instancias de dispositivo, el historial de datos del dispositivo y los datos de usuario. La copia usa un plan de tarifa estándar que se le facturará. No se puede crear una aplicación que use el plan de tarifa gratis mediante la copia de una aplicación.

Seleccione **Copiar**. En el cuadro de diálogo, escriba los detalles de la nueva aplicación. Después, seleccione **Copiar** para confirmar que quiere continuar. Para obtener más información sobre los campos del formulario, consulte [Creación de una aplicación](howto-create-iot-central-application.md).

:::image type="content" source="media/howto-create-iot-central-application/app-copy-1.png" alt-text="Captura de pantalla que muestra la página de configuración de la aplicación.":::

:::image type="content" source="media/howto-create-iot-central-application/app-copy-2.png" alt-text="Captura de pantalla que muestra la página de configuración &quot;Copiar aplicación&quot;":::.

Una vez que la operación de copia de la aplicación se realiza correctamente, puede navegar a la nueva aplicación mediante el vínculo.

Al copiar una aplicación, también se copia la definición de las reglas y la acción de correo electrónico. Algunas acciones, como Flow y Logic Apps, están asociadas a reglas específicas a través del identificador de regla. Cuando una regla se copia en otra aplicación, obtiene su propio identificador de regla. En este caso, los usuarios tendrán que crear una nueva acción y, a continuación, asociar la nueva regla a ella. En general, es recomendable comprobar las reglas y las acciones para garantizar que están actualizadas en la nueva aplicación.

> [!WARNING]
> Si un panel incluye iconos que muestran información acerca de dispositivos específicos, estos iconos muestran el mensaje **No se encontró el recurso solicitado** en la nueva aplicación. Debe volver a configurar estos iconos para mostrar información acerca de los dispositivos en la nueva aplicación.

## <a name="create-and-use-a-custom-application-template"></a>Creación y uso de una plantilla de aplicación personalizada

Cuando crea una aplicación de Azure IoT Central, tiene la posibilidad de usar plantillas de ejemplo integradas. También puede crear sus propias plantillas de aplicación a partir de las aplicaciones existentes de IoT Central. A continuación, puede usar sus propias plantillas de aplicación al crear nuevas aplicaciones.

Cuando crea una plantilla de aplicación, esta incluye los siguientes elementos de su aplicación existente:

- El panel de la aplicación predeterminado, incluidos el diseño del panel y todos los iconos que ha definido.
- Las plantillas del dispositivo, incluidas las medidas, la configuración, las propiedades, los comandos y el panel.
- Las reglas. Se incluyen todas las definiciones de las reglas. Sin embargo, no se incluyen las acciones, excepto las de correo electrónico.
- Grupos de dispositivos, incluidas sus consultas.

> [!WARNING]
> Si un panel incluye iconos que muestran información acerca de dispositivos específicos, estos iconos muestran el mensaje **No se encontró el recurso solicitado** en la nueva aplicación. Debe volver a configurar estos iconos para mostrar información acerca de los dispositivos en la nueva aplicación.

Cuando crea una plantilla de aplicación, no incluye los siguientes elementos:

- Dispositivos
- Usuarios
- Definiciones de exportaciones de datos continuas

Agregue estos elementos manualmente a cualquier aplicación creada a partir de una plantilla de aplicación.

Para crear una plantilla de aplicación a partir de una aplicación existente de IoT Central:

1. Vaya a la sección **Administración** en su aplicación.
1. Seleccione **Application Template Export** (Exportación de la plantilla de aplicación).
1. En la página **Application Template Export** (Exportación de la plantilla de aplicación), escriba un nombre y una descripción para la plantilla.
1. Seleccione el botón **Exportar** para crear la plantilla de aplicación. Ahora puede copiar el **vínculo que se puede compartir** que permite que otras personas puedan crear una nueva aplicación a partir de la plantilla:

:::image type="content" source="media/howto-create-iot-central-application/create-template.png" alt-text="Captura de pantalla que muestra cómo crear una plantilla de aplicación.":::

:::image type="content" source="media/howto-create-iot-central-application/create-template-2.png" alt-text="Captura de pantalla que muestra cómo exportar una plantilla de aplicación.":::

### <a name="use-an-application-template"></a>Uso de una plantilla de aplicación

Para usar una plantilla de aplicación para crear una nueva aplicación de IoT Central, necesita un **vínculo que se puede compartir** que haya creado anteriormente. Pegue el **vínculo que se puede compartir** en la barra de direcciones de su explorador. La página **Crear una aplicación**  se muestra con su plantilla de aplicación personalizada seleccionada:

:::image type="content" source="media/howto-create-iot-central-application/create-app.png" alt-text="Captura de pantalla que muestra cómo crear una aplicación a partir de una plantilla.":::

Seleccione el plan de tarifa y rellene los demás campos del formulario. A continuación, seleccione **Crear** para crear una nueva aplicación de IoT Central a partir de la plantilla de aplicación.

### <a name="manage-application-templates"></a>Administración de las plantillas de aplicaciones

En la página **Application Template Export** (Exportación de la plantilla de aplicación), puede eliminar o actualizar la plantilla de aplicación.

Si elimina una plantilla de aplicación, ya no podrá usar el vínculo que se puede compartir generado anteriormente para crear nuevas aplicaciones.

Para actualizar la plantilla de aplicación, cambie el nombre o la descripción de la plantilla en la página **Application Template Export** (Exportación de la plantilla de aplicación). A continuación, vuelva a seleccionar el botón **Exportar**. Esta acción genera un nuevo **vínculo que se puede compartir** e invalida cualquier URL anterior de **vínculo que se puede compartir vínculo**.

## <a name="other-approaches"></a>Otros enfoques

También puede usar los siguientes enfoques para crear una aplicación de IoT Central:

- [Creación de una aplicación de IoT Central desde Azure Portal](howto-manage-iot-central-from-portal.md#create-iot-central-applications)
- [Creación de una aplicación de IoT Central mediante la línea de comandos](howto-manage-iot-central-from-cli.md#create-an-application)
- [Creación de una aplicación de IoT Central mediante programación](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/)

## <a name="next-steps"></a>Pasos siguientes

Ahora que ha aprendido a administrar aplicaciones de Azure IoT Central desde la CLI de Azure, le sugerimos el paso siguiente:

> [!div class="nextstepaction"]
> [Administrar su aplicación](howto-administer.md)
