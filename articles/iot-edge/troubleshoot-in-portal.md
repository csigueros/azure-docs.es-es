---
title: 'Solución de problemas desde Azure Portal: Azure IoT Edge | Microsoft Docs'
description: Use la página de solución de problemas de Azure Portal para supervisar los dispositivos y los módulos IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 05/26/2021
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: ba7cbd0d6af9a548c66f186ecd0aac289c52041b
ms.sourcegitcommit: bd65925eb409d0c516c48494c5b97960949aee05
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/06/2021
ms.locfileid: "111543151"
---
# <a name="troubleshoot-iot-edge-devices-from-the-azure-portal"></a>Solución de problemas de dispositivos IoT Edge desde Azure Portal

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

IoT Edge proporciona una forma simplificada de supervisar los módulos y solucionar sus problemas en Azure Portal. La página de solución de problemas es un contenedor de métodos directos del agente de IoT Edge para que pueda recuperar los registros de los módulos implementados y reiniciarlos de forma remota fácilmente.

## <a name="prerequisites"></a>Prerrequisitos

La funcionalidad completa de esta característica de solución de problemas del portal requiere la versión 1.1.3 de IoT Edge o posteriores, si se encuentra en la rama compatible a largo plazo, o bien la versión 1.2.1 o posteriores si se encuentra en la rama estable más reciente. Tanto el componente de host IoT Edge como el módulo edgeAgent deben tener en estas versiones.

## <a name="access-the-troubleshooting-page"></a>Acceso a la página de solución de problemas

Puede acceder a la página de solución de problemas del portal a través de la página de detalles del dispositivo o del módulo IoT Edge.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y vaya a IoT Hub.

1. En el panel izquierdo, seleccione **IoT Edge** en el menú.

1. Seleccione el dispositivo IoT Edge que quiera administrar de la lista de dispositivos.

1. En la página de detalles del dispositivo, puede seleccionar **Solucionar problemas** en el menú o bien seleccionar el estado en tiempo de ejecución de un módulo determinado que quiera inspeccionar.

   ![En la página de detalles del dispositivo, seleccione la opción para solucionar problemas o un estado en tiempo de ejecución del módulo.](./media/troubleshoot-in-portal/troubleshoot-from-device-details.png)

1. En esa misma página, también puede seleccionar el nombre de un módulo para abrir la página de detalles correspondiente. Desde ahí, puede seleccionar **Solucionar problemas** en el menú.

   ![En la página de detalles del módulo, seleccione la opción de solución de problemas](./media/troubleshoot-in-portal/troubleshoot-from-module-details.png)

## <a name="view-module-logs-in-the-portal"></a>Visualización de los registros del módulo en el portal

En la página **Solucionar problemas**, puede ver y descargar registros de cualquiera de los módulos en ejecución en el dispositivo IoT Edge.

Esta página tiene un límite máximo de 1500 líneas de registro y cualquier registro que supere este límite se truncará. Si los registros son demasiado grandes, se producirá un error al intentar obtener los registros del módulo. En ese caso, pruebe a cambiar el filtro de intervalo de tiempo para recuperar menos datos o considere la posibilidad de usar métodos directos para la [Recuperación de registros de implementaciones de IoT Edge](how-to-retrieve-iot-edge-logs.md) a fin de recopilar archivos de registro más grandes.

Use el menú desplegable para elegir qué módulo se va a inspeccionar.

![Elección de módulos en el menú desplegable](./media/troubleshoot-in-portal/select-module.png)

De forma predeterminada, esta página muestra los últimos quince minutos de registros. Seleccione el filtro **Intervalo de tiempo** para ver registros diferentes. Use el control deslizante para seleccionar una ventana de tiempo dentro de los últimos 60 minutos o seleccione **Especificar hora en su lugar** para elegir una ventana de fecha y hora específica.

![Seleccionar intervalo de tiempo](./media/troubleshoot-in-portal/select-time-range.png)

Una vez que tenga los registros del módulo para solucionar los problemas durante el intervalo de tiempo que quiere inspeccionar, puede usar el filtro **Buscar** para recuperar líneas específicas de los registros. Puede filtrar por advertencias o errores, o bien proporcionar un término o una frase específicos para buscarlos. La característica **Buscar** admite búsquedas de texto no cifrado o [expresiones regulares de .NET](/dotnet/standard/base-types/regular-expression-language-quick-reference) para búsquedas más complejas.

Puede descargar los registros del módulo como archivo de texto. El archivo de registro descargado reflejará los filtros activos que haya aplicado a los registros.

>[!TIP]
>El uso de la CPU en un dispositivo alcanzará un pico temporalmente cuando recopile registros en respuesta a una solicitud del portal. Este comportamiento es previsible y el uso debe estabilizarse una vez completada la tarea.

## <a name="restart-modules"></a>Reinicio de módulos

La página **Solucionar problemas** incluye una característica para reiniciar un módulo. Al seleccionar esta opción, se envía un comando al agente de IoT Edge para reiniciar el módulo seleccionado. El reinicio de un módulo no afectará a la capacidad de recuperar registros anterior al reinicio.

![Reinicio de un módulo desde la página de solución de problemas](./media/troubleshoot-in-portal/restart-module.png)

## <a name="next-steps"></a>Pasos siguientes

Encuentre más sugerencias para la [Solución de problemas del dispositivo IoT Edge](troubleshoot.md) o bien obtenga información sobre [problemas habituales y soluciones](troubleshoot-common-errors.md). 

Si tiene más preguntas, cree una [solicitud de soporte técnico](https://portal.azure.com/#create/Microsoft.Support) para obtener ayuda.
