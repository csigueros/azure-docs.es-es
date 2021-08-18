---
title: 'Implementación de módulos desde Azure Portal: Azure IoT Edge'
description: Use IoT Hub en Azure Portal para enviar un módulo de IoT Edge desde la instancia de IoT Hub al dispositivo IoT Edge, tal como se configura en un manifiesto de implementación.
author: kgremban
ms.author: kgremban
ms.date: 10/13/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: df229db3f68de7bdd59a3074e3011bebc8676178
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750783"
---
# <a name="deploy-azure-iot-edge-modules-from-the-azure-portal"></a>Implementación de módulos de Azure IoT Edge desde Azure Portal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Una vez que ha creado módulos de IoT Edge con su lógica empresarial, querrá implementarlos en sus dispositivos para usarlos en el perímetro. Si tiene varios módulos que funcionan conjuntamente para recopilar y procesar datos, puede implementarlos todos a la vez y declarar las reglas de enrutamiento que los conectan.

Este artículo muestra cómo le puede ayudar Azure Portal en la creación de un manifiesto de implementación y en la inserción de la implementación en un dispositivo de IoT Edge. Para obtener información sobre la creación de una implementación dirigida a varios dispositivos en función de sus etiquetas compartidas, consulte [Implementación y supervisión de módulos de IoT Edge a escala](how-to-deploy-at-scale.md).

## <a name="prerequisites"></a>Prerrequisitos

* Una instancia de [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) en la suscripción de Azure.
* Un dispositivo IoT Edge.

  Si no tiene un dispositivo IoT Edge configurado, puede crear uno en una máquina virtual de Azure. Siga los pasos de alguno de los artículos de inicio rápido para [Crear un dispositivo virtual Linux](quickstart-linux.md) o [Crear un dispositivo virtual Windows](quickstart.md).

## <a name="configure-a-deployment-manifest"></a>Configuración de un manifiesto de implementación

Un manifiesto de implementación es un documento JSON que describe qué módulos se van a implementar, cómo fluyen los datos entre los módulos y las propiedades deseadas de los módulos gemelos. Para más información sobre los manifiestos de implementación y cómo crearlos, consulte [Descripción de cómo se pueden utilizar, configurar y reutilizar los módulos de IoT Edge](module-composition.md).

Azure Portal tiene un asistente que le guía en la creación del manifiesto de implementación, en lugar de crear el documento JSON de forma manual. Consta de tres pasos: **Adición de módulos**, **Especificación de rutas** y **Revisión de la implementación**.

>[!NOTE]
>En los pasos de este artículo se refleja la última versión de esquema del agente y el centro de conectividad de IoT Edge. La versión de esquema 1.1 se publicó junto con la versión 1.0.10 de IoT Edge, y permite las características de orden de inicio y priorización de rutas del módulo.
>
>Si va a implementar en un dispositivo con la versión 1.0.9 o anterior, edite **Configuración del entorno de ejecución** en el paso **Módulos** del asistente para usar la versión de esquema 1.0.

### <a name="select-device-and-add-modules"></a>Selección de dispositivo y adición de módulos

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a IoT Hub.
1. En el panel izquierdo, seleccione **IoT Edge** en el menú.
1. Haga clic en el identificador del dispositivo de destino en la lista de dispositivos.
1. En la barra superior, seleccione **Establecer módulos**.
1. En la sección **Configuración de Container Registry** de la página, proporcione las credenciales para acceder a cualquier registro del contenedor privado que contiene las imágenes del módulo.
1. En la sección **Módulos de IoT Edge** de la página, haga clic en **Agregar**.
1. Elija uno de los tres tipos de módulos en el menú desplegable:

   * **Módulo de IoT Edge**: proporcione el nombre del módulo y el identificador URI de la imagen de contenedor. Por ejemplo, el identificador URI de la imagen para el módulo SimulatedTemperatureSensor de ejemplo es `mcr.microsoft.com/azureiotedge-simulated-temperature-sensor:1.0`. Si la imagen del módulo está almacenada en un registro de contenedor privado, agregue las credenciales en esta página para tener acceso a la imagen.
   * **Módulo de Marketplace**: módulos hospedados en Azure Marketplace. Algunos módulos de Marketplace requieren una configuración adicional, por lo que debe revisar los detalles del módulo en la lista de [módulos de IoT Edge de Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).
   * **Módulo de Azure Stream Analytics**: solo módulos generados a partir de una carga de trabajo de Azure Stream Analytics.

1. Después de agregar un módulo, seleccione el nombre del módulo en la lista para abrir la configuración del módulo. Rellene los campos opcionales si es necesario.

   Para obtener más información sobre la configuración de módulos disponible, vea [Configuración y administración de módulos](module-composition.md#module-configuration-and-management).

   Para más información sobre el módulo gemelo, consulte [Definición o actualización de las propiedades deseadas](module-composition.md#define-or-update-desired-properties).

1. Repita los pasos del 6 al 8 para agregar módulos adicionales a la implementación.
1. Seleccione **Siguiente: Rutas** para continuar con la sección de rutas.

### <a name="specify-routes"></a>Especificación de rutas

En la pestaña **Rutas**, se define cómo se pasan los mensajes entre los módulos de IoT Hub. Los mensajes se construyen mediante pares de nombre-valor. De forma predeterminada, la primera implementación de un nuevo dispositivo incluye una ruta denominada **route** y definida como **FROM /messages/\* INTO $upstream**, lo que significa que cualquier salida de mensajes de cualquier módulo se envía al centro de IoT.  

Los parámetros **Prioridad** y **Período de vida** son parámetros opcionales que se pueden incluir en una definición de ruta. El parámetro Priority permite elegir en qué rutas los mensajes se procesarán primero o en qué rutas se deben procesar en último lugar. La prioridad se determina estableciendo un número de 0 a 9, donde 0 es la prioridad máxima. El parámetro Time to Live permite declarar durante cuánto tiempo se deben mantener los mensajes de esa ruta hasta que se procesen o se quiten de la cola.

Para obtener más información sobre cómo crear rutas, vea [Declaración de rutas](module-composition.md#declare-routes).

Una vez establecidas las rutas, seleccione **Siguiente: Revisar y crear** para continuar al próximo paso del asistente.

### <a name="review-deployment"></a>Revisión de la implementación

La sección de revisión le muestra el manifiesto de implementación en formato JSON que se ha creado en función de las selecciones de las dos secciones anteriores. Observe que hay dos módulos declarados que no ha agregado: **$edgeAgent** y **$edgeHub**. Estos dos módulos constituyen el [entorno de ejecución de Azure IoT Edge](iot-edge-runtime.md) y son valores predeterminados necesarios en todas las implementaciones.

Revise la información de implementación y seleccione **Crear**.

## <a name="view-modules-on-your-device"></a>Visualización de módulos en el dispositivo

Una vez que los módulos se han implementado en el dispositivo, puede verlos todos en la página Detalles del dispositivo de IoT Hub. Esta página muestra el nombre de cada módulo implementado, así como información útil, como el estado de la implementación y el código de salida.

## <a name="deploy-modules-from-azure-marketplace"></a>Implementación de módulos desde Azure Marketplace

[Azure Marketplace](https://azuremarketplace.microsoft.com/) es un mercado de aplicaciones y servicios en línea en el que puede examinar una amplia gama de aplicaciones y soluciones empresariales que están certificadas y optimizadas para ejecutarse en Azure, incluidos los [módulos de IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules).

Puede implementar un módulo de IoT Edge desde Azure Marketplace y desde IoT Hub.

### <a name="deploy-from-azure-marketplace"></a>Implementación desde Azure Marketplace

Examine los módulos de IoT Edge en Marketplace y, cuando encuentre el que desea, puede implementarlo seleccionando **Crear** o **Conseguirlo ahora**. Continúe con los pasos del Asistente para la implementación que pueden variar en función del módulo de IoT Edge seleccionado:

1. Para confirmar las condiciones de uso y la directiva de privacidad del proveedor, seleccione **Continuar**. En primer lugar, debe proporcionar información de contacto.
1. Elija la suscripción y la instancia de IoT Hub a la que está asociado el dispositivo de destino.
1. Elija **Implementar en un dispositivo**.
1. Escriba el nombre del dispositivo o seleccione **Buscar dispositivo** para buscar entre los dispositivos registrados en el centro.
1. Seleccione **Crear** para continuar el proceso estándar de configurar un manifiesto de implementación, lo que incluye agregar otros módulos si lo desea. Los detalles del nuevo módulo, como el URI de la imagen, las opciones de creación y las propiedades deseadas están predefinidas pero se pueden cambiar.

Compruebe que el módulo está implementado en su instancia de IoT Hub en Azure Portal. Seleccione el dispositivo, elija **Establecer módulos**; el módulo debe aparecer en la sección **Módulos de IoT Edge**.

### <a name="deploy-from-azure-iot-hub"></a>Implementación desde Azure IoT Hub

Puede implementar rápidamente un módulo desde Azure Marketplace en el dispositivo de su instancia de IoT Hub desde Azure Portal.

1. En Azure Portal, navegue a IoT Hub.
1. En el panel izquierdo, en **Administración automática de dispositivos**, seleccione **IoT Edge**.
1. Seleccione el dispositivo IoT Edge que va a recibir la implementación.
1. En la barra superior, seleccione **Establecer módulos**.
1. En la sección **Módulos de IoT Edge**, haga clic en **Agregar** y seleccione **Módulo de Marketplace** en el menú desplegable.

![Adición de módulo en IoT Hub](./media/how-to-deploy-modules-portal/iothub-add-module.png)

Elija un módulo en la página **Marketplace de módulos IoT Edge**. El módulo que seleccione se configura automáticamente para su suscripción, grupo de recursos y dispositivo. Posteriormente, aparece en la lista de módulos de IoT Edge. Es posible que este módulo necesite configuración adicional.

> [!TIP]
> La información sobre los módulos de IoT Edge de Azure IoT Hub es limitada. En primer lugar, puede obtener más información sobre los [módulos de IoT Edge](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1&subcategories=iot-edge-modules) en Azure Marketplace.

Seleccione **Siguiente: Rutas** y continúe con la implementación como se describe en las secciones [Especificación de rutas](#specify-routes) y [Revisión de la implementación](#review-deployment) anteriores de este artículo.

## <a name="next-steps"></a>Pasos siguientes

Aprenda a [implementar y supervisar módulos de IoT Edge a escala](how-to-deploy-at-scale.md)
