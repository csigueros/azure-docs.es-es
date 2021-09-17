---
title: Definición de un nuevo tipo de dispositivo IoT en Azure IoT Central | Microsoft Docs
description: En este artículo se muestra cómo crear una plantilla de dispositivo Azure IoT en la aplicación de Azure IoT Central. Se definen la telemetría, el estado, las propiedades y los comandos del tipo.
author: dominicbetts
ms.author: dobett
ms.date: 08/13/2021
ms.topic: how-to
ms.service: iot-central
services: iot-central
ms.custom:
- contperf-fy21q1
- device-developer
ms.openlocfilehash: 59c95633322f279504ded61bbf02a7415b1337b9
ms.sourcegitcommit: 8000045c09d3b091314b4a73db20e99ddc825d91
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2021
ms.locfileid: "122446293"
---
# <a name="define-a-new-iot-device-type-in-your-azure-iot-central-application"></a>Definición de un nuevo tipo de dispositivo IoT en la aplicación de Azure IoT Central

Una plantilla de dispositivo es un plano técnico que define las características y los comportamientos de un tipo de dispositivo que se conecta a una [aplicación de Azure IoT Central](concepts-app-templates.md).

En este artículo se describe cómo crear una plantilla de dispositivo en IoT Central. Por ejemplo, puede crear una plantilla de dispositivo para un sensor que envía telemetría, como la temperatura, y propiedades, como la ubicación. A partir de esta plantilla de dispositivo, un operador puede crear y conectar dispositivos reales.

En la captura de pantalla siguiente se muestra un ejemplo de una plantilla de dispositivo:

:::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="Captura de pantalla en la que se muestra una plantilla de dispositivo.":::

La plantilla de dispositivo contiene las secciones siguientes:

- Modelo: use el modelo para definir cómo interactúa el dispositivo con la aplicación de IoT Central. Cada modelo tiene un identificador de modelo único y define las funcionalidades del dispositivo. Las funcionalidades se agrupan en interfaces. Las interfaces permiten reutilizar componentes entre modelos o usar la herencia para ampliar el conjunto de funcionalidades.
- Propiedades en la nube: use las propiedades en la nube para definir la información que almacena la aplicación de IoT Central sobre los dispositivos. Por ejemplo, una propiedad en la nube podría registrar la fecha en la que se realizó la última revisión de un dispositivo.
- Personalizar: use personalizaciones para modificar las funcionalidades. Por ejemplo, especifique los valores de temperatura mínima y máxima de una propiedad.
- Vistas: use las vistas para visualizar los datos del dispositivo y los formularios para administrar y controlar un dispositivo.

Para más información, consulte [¿Qué son las plantillas de dispositivo?](concepts-device-templates.md).

## <a name="create-a-device-template"></a>Creación de una plantilla de dispositivo

Tiene varias opciones para crear plantillas de dispositivo:

- Diseñe la plantilla de dispositivo en la GUI de IoT Central.
- Importe una plantilla de dispositivo del [catálogo de dispositivos Azure Certified for IoT](https://aka.ms/iotdevcat). También puede personalizar la plantilla de dispositivos según sus requisitos en IoT Central.
- Cuando el dispositivo se conecta a IoT Central, que envíe el identificador de modelo del modelo que implementa. IoT Central usa el identificador de modelo para recuperar el modelo del repositorio de modelos y crear una plantilla de dispositivo. Agregue las propiedades, las personalizaciones y las vistas de la nube que la aplicación de IoT Central necesita a la plantilla del dispositivo.
- Cuando el dispositivo se conecte a IoT Central, deje que IoT Central [genere automáticamente una definición de plantilla de dispositivo](#autogenerate-a-device-template) a partir de los datos que envía el dispositivo.
- Cree un modelo de dispositivo, para lo que debe usar el [lenguaje de definición de Digital Twins (DTDL), V2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md). Importe manualmente el modelo de dispositivo en la aplicación de IoT Central. Después, agregue las propiedades, las personalizaciones y las vistas en la nube que la aplicación de IoT Central necesita.
- También puede agregar plantillas de dispositivo a una aplicación de IoT Central mediante la [API de REST](/learn/modules/manage-iot-central-apps-with-rest-api/) o la [CLI](howto-manage-iot-central-from-cli.md).

> [!NOTE]
> En cada caso, el código del dispositivo debe implementar las funcionalidades definidas en el modelo. La implementación del código de dispositivo no se ve afectada por las secciones de propiedades, personalizaciones y vistas en la nube de la plantilla de dispositivo.

En esta sección se muestra cómo importar una plantilla de dispositivo desde el catálogo y cómo personalizarla mediante la GUI de IoT Central. En este ejemplo se usa la plantilla de dispositivo **ESP32-Azure IoT Kit** del catálogo de dispositivos:

1. Para agregar una nueva plantilla de dispositivo, seleccione **+ New** (+ Nuevo) en la página **Device templates** (Plantillas de dispositivo).
1. En la página **Select type** (Seleccionar tipo), desplácese hacia abajo hasta que encuentre el icono de **ESP32-Azure IoT Kit** en la sección **Use a preconfigured device template** (Usar una plantilla de dispositivo preconfigurada).
1. Seleccione el icono **ESP32-Azure IoT Kit** y **Next: Revisión**.
1. En la página **Revisar**, seleccione **Crear**.
El nombre de la plantilla que creó es **Sensor Controller**. El modelo incluye componentes como **Sensor Controller**, **SensorTemp** y **Device Information interface** (Interfaz de información del dispositivo). Los componentes definen las funcionalidades de un dispositivo ESP32, como la telemetría, las propiedades y los comandos.

:::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="Captura de pantalla en la que se muestra una plantilla de dispositivo de Sensor Controller.":::

## <a name="autogenerate-a-device-template"></a>Generación automática de una plantilla de dispositivo

También puede crear automáticamente una plantilla de dispositivo a partir de un dispositivo conectado que aún no esté asignado a una plantilla de dispositivo. IoT Central usa los valores de propiedad y telemetría que envía el dispositivo para deducir un modelo de dispositivo.

> [!NOTE]
> Actualmente, esta característica en versión preliminar no puede usar la telemetría ni las propiedades de los componentes. Solo puede generar funcionalidades a partir de la telemetría y las propiedades raíz.

Los pasos siguientes muestran cómo usar esta característica:

1. Conecte el dispositivo a IoT Central y empiece a enviar los datos. Cuando vea los datos en la vista **Datos sin procesar**, seleccione **Auto-create template** (Crear plantilla automáticamente) en la lista desplegable **Administrar plantilla**:

    :::image type="content" source="media/howto-set-up-template/infer-model-1.png" alt-text="Captura de pantalla en la que se muestra los datos sin procesar del dispositivo sin signo.":::

1. En la página **Vista previa de los datos**, realice los cambios necesarios en los datos sin procesar y seleccione **Crear plantilla**:

    :::image type="content" source="media/howto-set-up-template/infer-model-2.png" alt-text="Captura de pantalla en la que se muestra el cambio de vista previa de los datos que le permite editar los datos que usa IoT Central para generar la plantilla de dispositivo.":::

1. IoT Central genera una plantilla basada en el formato de los datos mostrados en la página **Vista previa de los datos** y le asigna el dispositivo. Puede realizar más cambios a la plantilla de dispositivo, como cambiarle el nombre o agregar funcionalidades, en la página **Plantillas de dispositivo**:

    :::image type="content" source="media/howto-set-up-template/infer-model-3.png" alt-text="Captura de pantalla en la que se muestra cómo cambiar el nombre de la plantilla de dispositivo generada automáticamente.":::

## <a name="manage-a-device-template"></a>Administración de una plantilla de dispositivo

Puede cambiar el nombre o eliminar una plantilla desde la página del editor de la plantilla.

Después de definir la plantilla, puede publicarla. Hasta que se publique la plantilla, no podrá conectar un dispositivo a ella y no aparecerá en la página **Dispositivos**.

Para más información sobre cómo modificar y realizar un control de versiones de plantillas de dispositivo, consulte [Edición de una plantilla de dispositivo existente](howto-edit-device-template.md).

## <a name="models"></a>Modelos

El modelo define cómo interactúa el dispositivo con la aplicación de IoT Central. Personalice el modelo con más funcionalidades, agregue interfaces para heredar funcionalidades o agregue nuevos componentes basados en otras interfaces.

Para crear un modelo de dispositivo, puede:

- Usar IoT Central para crear un modelo personalizado desde cero.
- Importar un modelo de DTDL desde un archivo JSON. Un desarrollador de dispositivos puede haber usado Visual Studio Code para crear un modelo de dispositivo para la aplicación.
- Seleccione uno de los dispositivos en el catálogo de dispositivos. Esta opción importa el modelo de dispositivo que el fabricante ha publicado para este dispositivo. Un modelo de dispositivo importado como este se publica automáticamente.

1. Para ver el identificador del modelo, seleccione la interfaz raíz en el modelo y seleccione **Editar identidad**:

    :::image type="content" source="media/howto-set-up-template/view-id.png" alt-text="Captura de pantalla en la que se muestra el identificador de modelo de la interfaz raíz de la plantilla de dispositivo.":::

1. Para ver el identificador de componente, seleccione **Editar identidad** en cualquiera de las interfaces de componente del modelo.

Para más información, consulte la [Guía de modelado de IoT Plug and Play](../../iot-pnp/concepts-modeling-guide.md).

### <a name="interfaces-and-components"></a>Interfaces y componentes

Para ver y administrar las interfaces en el modelo de dispositivo:

1. Vaya a la página **Plantillas de dispositivo** y seleccione la plantilla de dispositivo que ha creado. Las interfaces se muestran en la sección **Modelos** de la plantilla de dispositivo. En la captura de pantalla siguiente se muestra un ejemplo de la interfaz raíz de **Sensor Controller** en una plantilla de dispositivo:

    :::image type="content" source="media/howto-set-up-template/device-template.png" alt-text="Captura de pantalla en la que se muestra la interfaz raíz de un modelo"::: 

1. Seleccione los puntos suspensivos para agregar una interfaz heredada o un componente a la interfaz raíz. Para más información sobre las interfaces y los componentes, vea [Varios componentes](../../iot-pnp/concepts-modeling-guide.md#multiple-components) en la guía de modelado.

    :::image type="content" source="media/howto-set-up-template/add-interface.png" alt-text="Agregación de una interfaz o un componente":::

1. Para exportar un modelo o una interfaz, seleccione **Exportar**.

1. Para ver o editar el DTDL de una interfaz o una funcionalidad, seleccione **Edit DTDL** (Editar DTDL).

### <a name="capabilities"></a>Funcionalidades

Seleccione **+ Agregar funcionalidad** para agregar una funcionalidad a una interfaz o componente. Por ejemplo, puede agregar la funcionalidad **Target Temperature** (Temperatura de destino) a un componente **SensorTemp**.

:::image type="content" source="media/howto-set-up-template/add-capability.png" alt-text="Agregación de una funcionalidad":::

#### <a name="telemetry"></a>Telemetría

La telemetría es un flujo de valores enviados desde el dispositivo, normalmente desde un sensor. Por ejemplo, un sensor podría informar de la temperatura ambiente como se muestra a continuación:

:::image type="content" source="media/howto-set-up-template/telemetry.png" alt-text="Agregación de telemetría":::

En la siguiente tabla se muestran las opciones de configuración de una funcionalidad de telemetría:

| Campo | Descripción |
| ----- | ----------- |
| Display Name (Nombre para mostrar) | Nombre para mostrar del valor de telemetría que se usa en las vistas y formularios. |
| Nombre | El nombre del campo en el mensaje de telemetría. IoT Central genera un valor para este campo a partir del nombre para mostrar, pero puede elegir su propio valor si es necesario. Este campo debe ser alfanumérico. |
| Capability Type (Tipo de funcionalidad) | Telemetría. |
| Semantic Type (Tipo semántico) | El tipo semántico de la telemetría, como la temperatura, el estado o el evento. La elección del tipo semántico determina cuál de los campos siguientes está disponible. |
| Schema | El tipo de datos de telemetría, como doble, cadena o vector. Las opciones disponibles vienen determinadas por el tipo semántico. El esquema no está disponible para los tipos semánticos de evento y estado. |
| severity | Solo está disponible para el tipo semántico de evento. Los niveles de gravedad son **Error**, **Información** o **Advertencia**. |
| State Values (Valores de estado) | Solo está disponible para el tipo semántico de estado. Defina los valores de estado posibles, cada uno de los cuales tiene el nombre para mostrar, el nombre, el tipo de enumeración y el valor. |
| Unidad | Una unidad para el valor de telemetría, como **mph**, **%** o **&deg;C**. |
| Display Unit (Unidad de visualización) | Unidad de visualización para usarse en vistas y formularios. |
| Comentario | Cualquier comentario sobre la funcionalidad de telemetría. |
| Descripción | Una descripción de la funcionalidad de telemetría. |

#### <a name="properties"></a>Propiedades

Las propiedades representan valores de un momento dado. Puede establecer las propiedades que se pueden editar desde IoT Central.
Por ejemplo, un dispositivo puede usar una propiedad grabable para permitir que un operador establezca la temperatura de destino como se muestra a continuación:

:::image type="content" source="media/howto-set-up-template/property.png" alt-text="Agregación de una propiedad":::

En la siguiente tabla se muestran las opciones de configuración de una funcionalidad de propiedad:

| Campo | Descripción |
| ----- | ----------- |
| Display Name (Nombre para mostrar) | Nombre para mostrar del valor de propiedad que se usa en las vistas y formularios. |
| Nombre | El nombre de la propiedad. IoT Central genera un valor para este campo a partir del nombre para mostrar, pero puede elegir su propio valor si es necesario. Este campo debe ser alfanumérico. |
| Capability Type (Tipo de funcionalidad) | Propiedad. |
| Semantic Type (Tipo semántico) | El tipo semántico de la propiedad, como la temperatura, el estado o el evento. La elección del tipo semántico determina cuál de los campos siguientes está disponible. |
| Schema | El tipo de datos de la propiedad, como doble, cadena o vector. Las opciones disponibles vienen determinadas por el tipo semántico. El esquema no está disponible para los tipos semánticos de evento y estado. |
| Editable | Si la propiedad no se puede modificar, el dispositivo puede notificar los valores de la propiedad a IoT Central. Si la propiedad se puede modificar, el dispositivo puede notificar los valores de la propiedad a IoT Central y este puede enviar actualizaciones de propiedades al dispositivo.
| severity | Solo está disponible para el tipo semántico de evento. Los niveles de gravedad son **Error**, **Información** o **Advertencia**. |
| State Values (Valores de estado) | Solo está disponible para el tipo semántico de estado. Defina los valores de estado posibles, cada uno de los cuales tiene el nombre para mostrar, el nombre, el tipo de enumeración y el valor. |
| Unidad | Una unidad para el valor de propiedad, como **mph**, **%** o **&deg;C**. |
| Display Unit (Unidad de visualización) | Unidad de visualización para usarse en vistas y formularios. |
| Comentario | Cualquier comentario sobre la funcionalidad de propiedad. |
| Descripción | Una descripción de la funcionalidad de propiedad. |

#### <a name="commands"></a>Comandos:

Puede llamar a los comandos de dispositivo desde IoT Central. Los comandos, opcionalmente, pasan parámetros al dispositivo y reciben una respuesta del dispositivo. Por ejemplo, puede llamar a un comando para que reinicie un dispositivo en 10 segundos, como se muestra a continuación:

:::image type="content" source="media/howto-set-up-template/command.png" alt-text="Agregación de comandos":::

En la tabla siguiente se muestran las opciones de configuración de una funcionalidad de comando:

| Campo | Descripción |
| ----- | ----------- |
| Display Name (Nombre para mostrar) | Nombre para mostrar del comando que se usa en las vistas y formularios. |
| Nombre | El nombre del comando. IoT Central genera un valor para este campo a partir del nombre para mostrar, pero puede elegir su propio valor si es necesario. Este campo debe ser alfanumérico. |
| Capability Type (Tipo de funcionalidad) | Comando. |
| Queue if offline (poner en cola si no hay conexión) | Si está habilitado, puede llamar al comando aunque el dispositivo esté sin conexión. Si no está habilitado, solo puede llamar al comando cuando el dispositivo esté en línea. |
| Comentario | Cualquier comentario sobre la funcionalidad del comando. |
| Descripción | Una descripción de la funcionalidad del comando: |
| Solicitud | Si está habilitada, una definición del parámetro de solicitud que incluye lo siguiente: nombre, nombre para mostrar, esquema, unidad y unidad de visualización. |
| Response | Si está habilitada, una definición de la respuesta del comando que incluye lo siguiente: nombre, nombre para mostrar, esquema, unidad y unidad de visualización. |

Para más información sobre la forma en que los dispositivos implementan comandos, consulte [Cargas de telemetría, propiedades y comandos > Comandos y comandos de larga duración](concepts-telemetry-properties-commands.md#commands).

#### <a name="offline-commands"></a>Comandos sin conexión

Puede elegir comandos de cola si un dispositivo está sin conexión; para ello, habilite la opción **Queue if offline** (Poner en cola si no está conectado) en un comando de la plantilla de dispositivo.

:::image type="content" source="media/howto-set-up-template/offline-commands.png" alt-text="Agregación de comandos sin conexión":::

Esta opción usa los mensajes de la nube al dispositivo de IoT Hub para enviar notificaciones a los dispositivos. Para obtener más información, consulte el artículo de IoT Hub [Envío de mensajes de la nube al dispositivo](../../iot-hub/iot-hub-devguide-messages-c2d.md).

Los mensajes de la nube al dispositivo:

- Son notificaciones unidireccionales al dispositivo de la solución.
- Ofrecen la garantía de que el mensaje se entrega al menos una vez. IoT Hub conserva los mensajes de la nube al dispositivo en las colas por dispositivo, garantizando así la resistencia frente a errores de conectividad y de dispositivo.
- Requieren que el dispositivo implemente un controlador de mensajes para procesar el mensaje de la nube al dispositivo.

> [!NOTE]
> Esta opción solo está disponible en la interfaz de usuario web de IoT Central. Esta configuración no se incluye si exporta un modelo o un componente desde la plantilla de dispositivo.

## <a name="cloud-properties"></a>Propiedades de la nube

Use las propiedades en la nube para almacenar información acerca de los dispositivos en IoT Central. Las propiedades en la nube no se envían nunca a un dispositivo. Por ejemplo, puede usar las propiedades en la nube para almacenar el nombre del cliente que ha instalado el dispositivo o la fecha del último servicio del dispositivo.

:::image type="content" source="media/howto-set-up-template/cloud-properties.png" alt-text="Agregación de propiedades de nube"::: 

En la tabla siguiente se muestran las opciones de configuración de una propiedad en la nube:

| Campo | Descripción |
| ----- | ----------- |
| Display Name (Nombre para mostrar) | Nombre para mostrar del valor de propiedad en la nube que se usa en las vistas y formularios. |
| Nombre | El nombre de la propiedad en la nube. IoT Central genera un valor para este campo a partir del nombre para mostrar, pero puede elegir su propio valor si es necesario. |
| Semantic Type (Tipo semántico) | El tipo semántico de la propiedad, como la temperatura, el estado o el evento. La elección del tipo semántico determina cuál de los campos siguientes está disponible. |
| Schema | El tipo de datos de la propiedad en la nube, como doble, cadena o vector. Las opciones disponibles vienen determinadas por el tipo semántico. |

## <a name="customizations"></a>Personalizaciones

Use las personalizaciones cuando necesite modificar un componente importado o agregar características específicas de IoT Central a una funcionalidad. Por ejemplo, puede cambiar el nombre para mostrar y las unidades de una propiedad como se muestra a continuación:

:::image type="content" source="media/howto-set-up-template/customize.png" alt-text="Realización de personalizaciones":::

En la tabla siguiente se muestran las opciones de configuración de personalizaciones:

| Campo | Descripción |
| ----- | ----------- |
|Nombre para mostrar | Invalide el nombre para mostrar del modelo. |
|Tipo semántico | Invalide el tipo semántico del modelo. |
|Unidad | Invalide la unidad del modelo. |
|Unidad de visualización | Invalidación del modelo. |
|Comentario | Invalidación del modelo. |
|Descripción | Invalidación del modelo. |
|Color | Opción específica de IoT Central. |
|Valor mínimo | Establecer valor mínimo: opción específica de IoT Central. |
|Valor máximo | Establecer valor máximo: opción específica de IoT Central. |
|Posiciones decimales | Opción específica de IoT Central. |
|Valor inicial | Comandos solo valor específico de IoT Central: valor de parámetro predeterminado. |

## <a name="views"></a>Vistas

Las vistas permiten definir vistas y formularios que permiten a un operador supervisar e interactuar con un dispositivo. Las vistas usan visualizaciones como gráficos para mostrar los valores de telemetría y propiedad.

La generación de vistas predeterminadas es una forma rápida de visualizar la información importante del dispositivo. Las tres vistas predeterminadas son las siguientes:

### <a name="default-views"></a>Vistas predeterminadas

- **Comandos**: una vista con los comandos del dispositivo que permite que el operador los envíe al dispositivo.
- **Información general**: una vista con los datos de telemetría del dispositivo que muestra gráficos y métricas.
- **Acerca de**: una vista con información del dispositivo que muestra sus propiedades.

Después de seleccionar **Generar vistas predeterminadas**, las vistas se agregan automáticamente a la sección **Vistas** de la plantilla de dispositivo.

### <a name="custom-views"></a>Vistas personalizadas

Agregue vistas a una plantilla de dispositivo para permitir que los operadores visualicen un dispositivo mediante gráficos y métricas. Puede agregar sus propias vistas personalizadas a una plantilla de dispositivo.

Para agregar una vista a una plantilla de dispositivo:

1. Vaya a la plantilla del dispositivo y seleccione **Vistas**.
1. Seleccione **Visualización del dispositivo**.
1. Escriba un nombre para la vista en **Nombre de vista**.
1. Seleccione **Start with a visual** (Iniciar con un objeto visual) en Agregar iconos y elija el tipo de objeto visual para el icono. Después, seleccione **Agregar icono** o arrastre y coloque el objeto visual en el lienzo. Para configurar el icono, seleccione el icono de engranaje.

:::image type="content" source="media/howto-set-up-template/start-visual.png" alt-text="Inicio con un objeto visual"::: 

:::image type="content" source="media/howto-set-up-template/tile.png" alt-text="configuración del icono"::: 

Para probar la vista, seleccione **Configure preview device** (Configurar la vista previa de dispositivo). Esta característica le permite ver la vista del mismo modo que el operador una vez publicada. Use esta característica para asegurarse de que las vistas muestran los datos correctos. Elija entre las siguientes opciones:

- Ningún dispositivo de versión preliminar.
- El dispositivo de prueba real que ha configurado para la plantilla de dispositivo.
- Un dispositivo existente en la aplicación mediante el uso del identificador de dispositivo.

### <a name="forms"></a>Formularios

Agregue formularios a una plantilla de dispositivo para permitir a los operadores administrar un dispositivo con la visualización y configuración de propiedades. Los operadores solo pueden editar las propiedades en la nube y las propiedades del dispositivo que se pueden modificar. Puede tener varios formularios para una plantilla de dispositivo.

1. Seleccione el nodo **Views** (Vistas) y, después, seleccione el icono **Editing device and cloud data** (Editar datos del dispositivo y de la nube) para agregar una vista.

1. Cambie el nombre del formulario a **Manage device** (Administrar dispositivo).

1. Seleccione las propiedades de la nube **Customer Name** (Nombre del cliente) y **Last Service Date** (Fecha de la última revisión), así como la propiedad **Target Temperature** (Temperatura objetivo). Después, seleccione **Add section** (Agregar sección).

1. Seleccione **Save** (Guardar) para guardar la configuración.

:::image type="content" source="media/howto-set-up-template/form.png" alt-text="configuración del formulario":::

## <a name="publish-a-device-template"></a>Publicación de una plantilla de dispositivo

Antes de poder conectar un dispositivo que implementa el modelo de dispositivo, debe publicar la plantilla del dispositivo.

Para publicar una plantilla de dispositivo, vaya a su plantilla de dispositivo y seleccione **Publicar**.

Después de publicar una plantilla de dispositivo, el operador puede ir a la página **Dispositivos** y agregar dispositivos reales o simulados que usan la plantilla de dispositivo. Puede seguir modificando y guardando la plantilla de dispositivo mientras realiza los cambios. Si desea enviar estos cambios al operador para que los vea en la página **Dispositivos**, debe seleccionar **Publicar** cada vez.

## <a name="next-steps"></a>Pasos siguientes

Como sugerencia, lea cómo [realizar cambios en una plantilla de dispositivo existente](howto-edit-device-template.md).
