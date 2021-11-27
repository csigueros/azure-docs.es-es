---
title: Conexión de alertas de seguridad a Microsoft Sentinel
description: Aprenda a conectar alertas de seguridad de Microsoft Defender for Cloud y transmitirlas a Microsoft Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.topic: how-to
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: b30d3080b5c8e024f9266e6fbacc8a3c98e7457c
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132733841"
---
# <a name="connect-security-alerts-from-microsoft-defender-for-cloud"></a>Conexión de alertas de seguridad de Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

## <a name="background"></a>Información previa

[Microsoft Defender for Cloud](../security-center/azure-defender.md), la plataforma integrada de protección de cargas de trabajo en la nube (CWPP) de [Defender for Cloud](../security-center/security-center-introduction.md), es una herramienta de administración de seguridad que le permite detectar y responder rápidamente a las amenazas en las cargas de trabajo de nube híbrida.

Este conector le permite transmitir las alertas de seguridad de Defender for Cloud a Microsoft Sentinel, para que pueda ver, analizar y responder a estas alertas y a los incidentes que generan, en un contexto de amenaza a la organización más amplio.

Como Defender for Cloud se habilita por suscripción, el conector de Defender for Cloud también se habilita o deshabilita por separado para cada suscripción.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>Sincronización de alertas

- Al conectar Microsoft Defender for Cloud a Microsoft Sentinel, el estado de las alertas de seguridad que se ingieren en Microsoft Sentinel se sincroniza entre los dos servicios. Por ejemplo, cuando se cierra una alerta en Defender for Cloud, esa alerta también se mostrará como cerrada en Microsoft Sentinel.

- El cambio del estado de una alerta en Defender for Cloud *no* afectará al estado de los **incidentes** de Microsoft Sentinel que contienen la alerta de Microsoft Sentinel, sino solo al de la propia alerta.

### <a name="bi-directional-alert-synchronization"></a>Sincronización de alertas bidireccional

- Al habilitar la **sincronización bidireccional**, se sincronizará automáticamente el estado de las alertas de seguridad originales con el de los incidentes de Microsoft Sentinel que contienen esas alertas. Por ejemplo, cuando se cierra un incidente de Microsoft Sentinel que contiene una alerta de seguridad, la alerta original correspondiente se cerrará automáticamente en Microsoft Defender for Cloud.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Microsoft Sentinel.

- Debe tener el rol de Lector de seguridad en las suscripciones de los registros que transmita.

- Deberá habilitar al menos un plan en Microsoft Defender for Cloud para cada suscripción en la que quiera habilitar el conector. Para habilitar los planes de Microsoft Defender en una suscripción, debe tener el rol de **Administrador de seguridad** para esa suscripción.

- Para habilitar la sincronización bidireccional, debe tener el rol de **Colaborador** o **Administrador de seguridad** en la suscripción pertinente.

## <a name="connect-to-microsoft-defender-for-cloud"></a>Conexión con Microsoft Defender for Cloud

1. En Microsoft Sentinel, seleccione **Conectores de datos** en el menú de navegación.

1. En la galería de conectores de datos, seleccione **Microsoft Defender for Cloud** y, a continuación, seleccione **Abrir página del conector** en el panel de detalles.

1. En **Configuración**, verá una lista de las suscripciones del inquilino y el estado de su conexión a Microsoft Defender for Cloud. Seleccione el botón de alternancia **Estado** situado junto a cada suscripción cuyas alertas quiera transmitir a Microsoft Sentinel. Para conectar varias suscripciones a la vez, marque las casillas situadas junto a las suscripciones pertinentes y, a continuación, seleccione el botón **Conectar** en la barra situada encima de la lista.

    > [!NOTE]
    > - Las casillas y los botones de alternancia **Conectar** solo estarán activos en las suscripciones para las que tenga los permisos necesarios.
    > - El botón **Conectar** solo estará activo si se ha marcado al menos una casilla de suscripción.

1. Para habilitar la sincronización bidireccional en una suscripción, localice esa suscripción en la lista y elija **Habilitado** en la lista desplegable de la columna **Sincronización bidireccional**. Para habilitar la sincronización bidireccional en varias suscripciones a la vez, marque sus casillas y seleccione el botón **Habilitar sincronización bidireccional** en la barra situada encima de la lista.

    > [!NOTE]
    > - Las casillas y las listas desplegables solo estarán activas en las suscripciones para las que tenga los [permisos necesarios](#prerequisites).
    > - El botón **Habilitar sincronización bidireccional** solo estará activo si se ha marcado al menos una casilla de suscripción.

1. En la columna **Planes de Microsoft Defender** de la lista, puede ver si los planes de Microsoft Defender están habilitados en la suscripción (un requisito previo para habilitar el conector). El valor de cada suscripción de esta columna estará en blanco (lo que significa que no hay ningún plan de Defender habilitado), o puede ser "Todos habilitados" o "Algunos habilitados". Aquellos que indiquen "Algunos habilitados" también tendrán un vínculo **Habilitar todos** que puede seleccionar, lo cual le llevará al panel de configuración de Microsoft Defender for Cloud para esa suscripción, donde puede elegir los planes de Defender que quiere habilitar. El botón del vínculo **Habilitar Microsoft Defender para todas las suscripciones** de la barra encima de la lista le llevará a la página de introducción de Microsoft Defender for Cloud, donde puede elegir en qué suscripciones habilitar Microsoft Defender for Cloud por completo.

    :::image type="content" source="./media/connect-defender-for-cloud/azure-defender-config.png" alt-text="Captura de pantalla de la configuración del conector de Microsoft Defender for Cloud":::

1. Puede seleccionar si quiere que las alertas de Microsoft Defender for Cloud generen incidentes automáticamente en Microsoft Sentinel. En **Create incidents** (Crear incidentes), seleccione **Enabled** (Habilitado) para activar la regla de análisis predeterminada que [crea automáticamente incidentes a partir de alertas](create-incidents-from-alerts.md). Luego, puede editar esta regla en **Analytics** (Análisis), en la pestaña **Active rules** (Reglas activas).

    > [!TIP]
    > Cuando configure [reglas de análisis personalizadas](detect-threats-custom.md) para las alertas de Microsoft Defender for Cloud, tenga en cuenta la gravedad de la alerta para evitar la apertura de incidentes para alertas informativas. 
    >
    > Las alertas informativas de Microsoft Defender for Cloud no representan un riesgo de seguridad por sí mismas y solo son importantes en el contexto de un incidente abierto existente. Para más información, consulte [Alertas e incidentes de seguridad en Microsoft Defender for Cloud](../security-center/security-center-alerts-overview.md).
    > 
    

## <a name="find-and-analyze-your-data"></a>Búsqueda y análisis de los datos

> [!NOTE]
> La sincronización de alertas *en ambas direcciones* puede tardar varios minutos. Es posible que los cambios en el estado de las alertas no aparezcan inmediatamente.

- Las alertas de seguridad se almacenan en la tabla *SecurityAlert* del área de trabajo de Log Analytics.

- Para consultar las alertas de seguridad en Log Analytics, copie lo siguiente en la ventana de consulta como punto de partida:

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- Consulte la pestaña **Pasos siguientes** en la página del conector para obtener consultas de ejemplo adicionales, plantillas de reglas de análisis y libros recomendados útiles.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Microsoft Defender for Cloud a Microsoft Sentinel y a sincronizar alertas entre ellas. Para más información sobre Microsoft Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Microsoft Sentinel](detect-threats-built-in.md).
- Escriba sus propias reglas para [detectar amenazas](detect-threats-custom.md).
