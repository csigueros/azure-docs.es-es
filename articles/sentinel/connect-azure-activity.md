---
title: Conexión de los datos del Registro de actividad de Azure con Azure Sentinel | Microsoft Docs
description: Transmita los eventos del Registro de actividad de Azure a Azure Sentinel con un solo clic. El servicio Registro de actividad registra y muestra los eventos a nivel de suscripción en Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 8c25baa8-b93b-41da-9e6c-15bb7b5c5511
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/23/2021
ms.author: yelevin
ms.openlocfilehash: 9638b7935d4af2c53bcd5cd59e1c155da524fa13
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121747160"
---
# <a name="connect-data-from-azure-activity-log"></a>Conectar datos del registro de actividad de Azure

Se pueden transmitir eventos desde el [servicio **Registro de actividad**](../azure-monitor/essentials/activity-log.md) de Azure a Azure Sentinel con un solo clic. El **Registro de actividad** es un [registro de plataforma](../azure-monitor/essentials/platform-logs-overview.md) de Azure, que ofrece información sobre los eventos a nivel de la suscripción, desde los datos operativos de Azure Resource Manager hasta las actualizaciones de eventos de Service Health. Con el **Registro de actividad**, se pueden determinar los interrogantes "qué, quién y cuándo" de las operaciones de escritura (PUT, POST, DELETE) que se realizan en los recursos de la suscripción. También puede obtener más información sobre el estado de la operación y otras propiedades pertinentes. El servicio no registra operaciones de lectura (GET) ni operaciones para los recursos que no usan el modelo de Azure Resource Manager (ARM).

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Log Analytics.

- Debe tener permisos de propietario en cualquier suscripción para cuyos registros quiera iniciar o detener la transmisión a Azure Sentinel.

- Para usar Azure Policy con el fin de aplicar una directiva de streaming de registro a suscripciones del Registro de actividad de Azure, debe tener el rol de propietario para el ámbito de asignación de directivas.

## <a name="data-structure-changes"></a>Cambios en la estructura de datos

Este conector está experimentando un cambio en el método usado en el back-end para recopilar eventos del Registro de actividad. Ahora usa la canalización de **configuración de diagnóstico** que usan nuestros conectores más recientes (por ejemplo, los conectores de Azure Key Vault y Azure Kubernetes Service). Si sigue usando el método heredado para este conector, *se recomienda encarecidamente actualizar* a la nueva versión, lo que proporciona una mejor funcionalidad y una mayor coherencia con los registros de recursos. Consulte las instrucciones a continuación.

El método de **configuración de diagnóstico** envía los mismos datos que el método heredado que se envía desde el servicio Registro de actividad, aunque ha habido algunos [cambios en la estructura](../azure-monitor/essentials/activity-log.md#data-structure-changes) de la tabla **AzureActivity**.

Estas son algunas de las mejoras clave resultantes del pasaje a la canalización de configuración de diagnóstico:
- Se mejoró la latencia de ingesta (ingesta de eventos de entre 2 y 3 minutos desde la aparición en lugar de 15 o 20 minutos).
- Se mejoró la confiabilidad.
- Rendimiento mejorado.
- Compatibilidad con todas las categorías de eventos registrados por el servicio Registro de actividad (el mecanismo heredado solo admite un subconjunto; por ejemplo, no admite eventos de Service Health).
- Administración a gran escala con Azure Policy.

Consulte la [documentación Azure Monitor](../azure-monitor/logs/data-platform-logs.md) para obtener un tratamiento más detallado del [Registro de actividad de Azure](../azure-monitor/essentials/activity-log.md) y la [canalización de configuración de diagnóstico](../azure-monitor/essentials/diagnostic-settings.md).

## <a name="set-up-the-azure-activity-log-connector"></a>Configuración del conector del Registro de actividad de Azure

La configuración del nuevo conector del Registro de actividad de Azure tiene dos fases:
1. Desconexión de las suscripciones existentes del método heredado.

1. Conexión de todas las suscripciones pertinentes a la nueva canalización de configuración de diagnóstico mediante **Azure Policy**.

### <a name="disconnect-from-old-pipeline"></a>Desconexión de la canalización antigua

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**. En la lista de conectores, seleccione **Actividad de Azure** y, a continuación, haga clic en el botón **Abrir página del conector** en la parte inferior derecha.

1. En la pestaña **Instrucciones**, en la sección **Configuración**, en el paso 1, revise la lista de suscripciones existentes que están conectadas al método heredado (para saber cuáles se van a agregar al nuevo) y desconéctelas todas a la vez haciendo clic en el botón **Desconectar todo** que aparece a continuación.

### <a name="connect-to-new-pipeline"></a>Conexión a la canalización nueva

1. En el paso 2, seleccione el botón **Asistente para configurar asignaciones de Azure Policy**. Se abrirá el Asistente para asignación de directivas, listo para crear una nueva directiva denominada **Configurar registros de actividad de Azure para transmitirlos al área de trabajo especificada de Log Analytics**.

1. En la pestaña **Básico**, haga clic en el botón con los tres puntos debajo de **Ámbito** para abrir el panel **Ámbito**, donde seleccionará la suscripción (y, opcionalmente, un grupo de recursos). También puede agregar una descripción.

1. En la pestaña **Parámetros**, deje los campos **Efecto** y **Habilitar registros** tal y como están. Elija el área de trabajo de Azure Sentinel en la lista desplegable **Área de trabajo de Log Analytics**.

1. La directiva se aplicará a los recursos agregados en el futuro. Para aplicar la directiva también a los recursos existentes, seleccione la pestaña **Corrección** y marque la casilla **Crear una tarea de corrección**.

1. En la pestaña **Revisar y crear**, haga clic en **Crear**. La directiva se asigna ahora al ámbito elegido.

> [!NOTE]
>
> Con este conector de datos concreto, los indicadores de estado de conectividad (una franja de color en la galería de conectores de datos y los iconos de conexión situados junto a los nombres de tipo de datos) se mostrarán como *Conectado* (verde) solo si los datos se han ingerido en algún momento en los últimos catorce días. Transcurridos catorce días sin ingesta de datos, el conector se mostrará como desconectado. En el momento en que circulen más datos, se devolverá el estado *conectado*.

## <a name="find-your-data"></a>Búsqueda de sus datos

Si quiere consultar el esquema correspondiente en Log Analytics para las alertas de actividad de Azure, escriba `AzureActivity` en la parte superior de la ventana de consulta.

Consulte la pestaña **Pasos siguientes** de la página del conector para ver algunas consultas de ejemplo útiles y plantillas de libro que le ayudarán a analizar y visualizar los datos.

## <a name="next-steps"></a>Pasos siguientes
En este documento, ha aprendido a conectar el registro de actividad de Azure a Azure Sentinel. Para más información sobre Azure Sentinel, consulte los siguientes artículos:
- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a detectar amenazas con Azure Sentinel mediante las reglas [integradas](detect-threats-built-in.md) o [personalizadas](detect-threats-custom.md).
