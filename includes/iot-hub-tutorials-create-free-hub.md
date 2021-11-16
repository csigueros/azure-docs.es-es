---
title: Archivo de inclusión
description: archivo de inclusión
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 406893018da40f10f764f44a13d13bd420492519
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132354011"
---
Para crear una instancia de IoT Hub mediante Azure Portal:

1. Inicie sesión en [Azure Portal](https://portal.azure.com).

1. En la página de inicio de Azure, seleccione **Crear un recurso** y, después, escriba *IoT Hub* en **Buscar en Marketplace**.

1. Seleccione **IoT Hub** en los resultados de la búsqueda y, después, haga clic en **Crear**.

1. En la pestaña **Datos básicos**, complete los campos como se indica a continuación:

   - **Suscripción**: seleccione la suscripción que quiera usar para el centro.

   - **Grupo de recursos**: seleccione un grupo de recursos o cree uno. Para crear uno, haga clic en **Crear** y escriba el nombre que quiera usar. Para usar un grupo de recursos existente, selecciónelo. Para más información, consulte [Administración de grupos de recursos de Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md). Este tutorial utiliza el nombre **tutorials-iot-hub-rg**.

   - **Región**: seleccione la región a la que quiera asignar el centro. Seleccione la ubicación más cercana a la suya. Algunas características, como los [flujos de dispositivo de IoT Hub](../articles/iot-hub/iot-hub-device-streams-overview.md), solo están disponibles en regiones específicas. Para ver estas características limitadas, debe seleccionar una de las regiones admitidas. En este tutorial se usa la región **Oeste de EE. UU.**

   - **Nombre de la instancia de IoT Hub**: escriba el nombre del centro. Este nombre debe ser único globalmente. En este tutorial se usa **tutorials-iot-hub**. Debe elegir un nombre exclusivo de su elección al crear el concentrador.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Creación de un centro mediante Azure Portal":::

1. Seleccione **Siguiente: Redes** para continuar con la creación del centro.

   Elija los puntos de conexión que se pueden conectar a su instancia de IoT Hub. Puede seleccionar la configuración predeterminada **Punto de conexión público (todas las redes)** o elegir **Punto de conexión público (intervalos de IP seleccionados)** o **Punto de conexión privado**. Para este tutorial, acepte los valores predeterminados.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="Elija los puntos de conexión que se puedan conectar":::.

1. Seleccione **Siguiente: Administración** para continuar con la creación del centro.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Configure el tamaño y la escala de un nuevo centro mediante Azure Portal.":::

   Puede aceptar la configuración predeterminada aquí. Si lo desea, puede modificar cualquiera de los siguientes campos:

   - **Plan de tarifa y escala**: nivel seleccionado. Seleccione el nivel Gratis. El nivel gratis está pensado para la prueba y evaluación. Permite la conexión de 500 dispositivos con el centro de IoT y hasta 8000 mensajes al día. Cada suscripción a Azure puede crear un centro de IoT en el nivel gratis.

   - **Unidades de IoT Hub**: El número de mensajes que se permiten por unidad al día depende del plan de tarifa del centro. Por ejemplo, si quiere que el Centro de IoT admita la entrada de 700 000 mensajes, seleccione dos unidades del nivel S1.
   Cada suscripción a Azure puede crear un centro de IoT en el nivel gratis. Para más información sobre las demás opciones del nivel, consulte la sección [Elección del nivel correcto de IoT Hub](../articles/iot-hub/iot-hub-scaling.md).

   - **Defender para IoT** Actívelo para agregar un nivel adicional de protección ante amenazas en IoT y en los dispositivos. Esta opción no está disponible para los centros de conectividad del nivel gratuito. Para obtener más información acerca de esta característica, consulte [Microsoft Defender para IoT](/azure/asc-for-iot/).

   - **Configuración avanzada** > **Particiones del dispositivo a la nube**: esta propiedad relaciona los mensajes del dispositivo a la nube con el número de lectores simultáneos de los mensajes. La mayoría de los centros solo necesitan cuatro particiones. Un centro de nivel Gratis está limitado a dos particiones.

1. Seleccione **Siguiente: Etiquetas** para pasar a la pantalla siguiente.

   Las etiquetas son pares nombre-valor. Puede asignar la misma etiqueta a varios recursos y grupos de recursos para clasificar los recursos y consolidar la facturación. Para más información, consulte [Uso de etiquetas para organizar los recursos de Azure](../articles/azure-resource-manager/management/tag-resources.md).

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Asigne las etiquetas del centro mediante Azure Portal.":::

1. Seleccione **Siguiente: Revisar y crear** para revisar sus selecciones. Verá algo parecido a esta pantalla, pero con los valores que ha seleccionado al crear el centro.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="Revise la información antes de crear el nuevo centro":::.

1. Anote el nombre de la instancia de IoT Hub que eligió. Usará este valor más adelante en el tutorial.
