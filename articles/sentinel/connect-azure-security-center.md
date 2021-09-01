---
title: Conexión de alertas de Azure Defender a Azure Sentinel
description: Descubra cómo conectar alertas de Azure Defender desde Azure Security Center y transmitirlas a Azure Sentinel.
author: yelevin
manager: rkarlin
ms.assetid: d28c2264-2dce-42e1-b096-b5a234ff858a
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: how-to
ms.date: 07/08/2021
ms.author: yelevin
ms.openlocfilehash: 0d348231c28e33b7eaef97a468e7e8a6a8677c32
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122179392"
---
# <a name="connect-azure-defender-alerts-from-azure-security-center"></a>Conexión de alertas de Azure Defender desde Azure Security Center

## <a name="background"></a>Información previa

[Azure Defender](../security-center/azure-defender.md), la plataforma integrada de protección de cargas de trabajo en la nube (CWPP) de [Azure Security Center](../security-center/security-center-introduction.md), es una herramienta de administración de seguridad que le permite detectar y responder rápidamente a las amenazas en las cargas de trabajo de nube híbrida. 

Este conector permite transmitir las alertas de seguridad de Azure Defender desde Azure Security Center a Azure Sentinel, para que pueda ver, analizar y responder a las alertas de Defender y los incidentes que generan, en un contexto de amenaza a la organización más amplio.

Como Azure Defender se habilita por suscripción, el conector de Azure Defender también se habilita o deshabilita por separado para cada suscripción.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

### <a name="alert-synchronization"></a>Sincronización de alertas

- Cuando se conecta Azure Defender a Azure Sentinel, el estado de las alertas de Azure Defender que se ingieren en Azure Sentinel se sincroniza entre los dos servicios. Por ejemplo, cuando se cierra una alerta en Azure Defender, esa alerta también aparecerá como cerrada en Azure Sentinel.

- El cambio del estado de una alerta en Azure Defender *no* afecta al estado de los **incidentes** de Azure Sentinel que contienen la alerta de Azure Sentinel, solo al de la propia alerta.

### <a name="bi-directional-alert-synchronization"></a>Sincronización de alertas bidireccional

> [!IMPORTANT]
>
> - La característica de **sincronización de alertas bidireccional** se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

- Al habilitar la **sincronización bidireccional**, se sincronizará automáticamente el estado de las alertas de Azure Defender originales con el de los incidentes de Azure Sentinel que contienen esas alertas. Por ejemplo, cuando se cierra un incidente de Azure Sentinel que contiene una alerta de Azure Defender, la alerta original correspondiente se cerrará en Azure Defender automáticamente.

## <a name="prerequisites"></a>Requisitos previos

- Debe tener permisos de lectura y escritura en el área de trabajo de Azure Sentinel.

- Debe tener el rol de Lector de seguridad en las suscripciones de los registros que transmita.

- Deberá habilitar al menos un plan de **Azure Defender** en Azure Security Center para cada suscripción para la que quiera habilitar el conector. Para habilitar los planes de Azure Defender en una suscripción, debe tener el rol de **Administrador de seguridad** para esa suscripción.

-  Para habilitar la sincronización bidireccional, debe tener el rol de **Colaborador** o **Administrador de seguridad** en la suscripción pertinente.

## <a name="connect-to-azure-defender"></a>Conexión a Azure Defender

1. En el menú de navegación de Azure Sentinel, seleccione **Conectores de datos**.

1. En la galería de conectores de datos, seleccione **Azure Defender** y, a continuación, haga clic en el panel de detalles **Abrir página del conector**.

1. En **Configuración**, verá una lista de las suscripciones del inquilino y el estado de su conexión a Azure Defender. Seleccione el botón de alternancia **Estado** situado junto a cada suscripción cuyas alertas quiera transmitir a Azure Sentinel. Para conectar varias suscripciones a la vez, marque las casillas situadas junto a las suscripciones pertinentes y, a continuación, seleccione el botón **Conectar** en la barra situada encima de la lista.

    > [!NOTE]
    > - Las casillas y los botones de alternancia **Conectar** solo estarán activos en las suscripciones para las que tenga los permisos necesarios.
    > - El botón **Conectar** solo estará activo si se ha marcado al menos una casilla de suscripción.

1. Para habilitar la sincronización bidireccional en una suscripción, localice esa suscripción en la lista y elija **Habilitado** en la lista desplegable de la columna **Sincronización bidireccional (versión preliminar)** . Para habilitar la sincronización bidireccional en varias suscripciones a la vez, marque sus casillas y seleccione el botón **Habilitar sincronización bidireccional** en la barra situada encima de la lista.

    > [!NOTE]
    > - Las casillas y las listas desplegables solo estarán activas en las suscripciones para las que tenga los [permisos necesarios](#prerequisites).
    > - El botón **Habilitar sincronización bidireccional** solo estará activo si se ha marcado al menos una casilla de suscripción.

1. En la columna **Planes de Azure Defender** de la lista, puede ver si los planes de Azure Defender están habilitados en la suscripción (un requisito previo para habilitar el conector). El valor de cada suscripción de esta columna estará en blanco (lo que significa que no hay ningún plan de Defender habilitado), o puede ser "Todos habilitados" o "Algunos habilitados". Aquellos que indiquen "Algunos habilitados" también tendrán un vínculo **Habilitar todos** que puede seleccionar, lo cual le llevará al panel de configuración de Azure Defender para esa suscripción, donde puede elegir los planes de Defender que quiere habilitar. El vínculo **Habilitar Azure Defender para todas las suscripciones** de la barra encima de la lista le llevará a la página de tareas iniciales de Azure Defender, donde puede elegir en qué suscripciones habilitar Azure Defender por completo.

    :::image type="content" source="./media/connect-azure-security-center/azure-defender-config.png" alt-text="Captura de pantalla de configuración del conector de Azure Defender":::

1. Puede seleccionar si quiere que las alertas de Azure Defender generen incidentes automáticamente en Azure Sentinel. En **Create incidents** (Crear incidentes), seleccione **Enabled** (Habilitado) para activar la regla de análisis predeterminada que [crea automáticamente incidentes a partir de alertas](create-incidents-from-alerts.md). Luego, puede editar esta regla en **Analytics** (Análisis), en la pestaña **Active rules** (Reglas activas).

    > [!TIP]
    > Cuando configure [reglas de análisis personalizadas](detect-threats-custom.md) para las alertas de Azure Defender, tenga en cuenta la gravedad de la alerta para evitar la apertura de incidentes para las alertas informativas. 
    >
    > Las alertas informativas de Azure Security Center no representan un riesgo de seguridad por sí mismas y solo son importantes en el contexto de un incidente abierto existente. Para más información, consulte [Incidentes y alertas de seguridad en Azure Security Center](../security-center/security-center-alerts-overview.md).
    > 
    

## <a name="find-and-analyze-your-data"></a>Búsqueda y análisis de los datos

> [!NOTE]
> La sincronización de alertas *en ambas direcciones* puede tardar varios minutos. Es posible que los cambios en el estado de las alertas no aparezcan inmediatamente.

- Las alertas de Azure Defender se almacenan en la tabla *SecurityAlert* del área de trabajo de Log Analytics.

- Para consultar las alertas de Azure Defender en Log Analytics, copie lo siguiente en la ventana de consulta como punto de partida:

    ```kusto
    SecurityAlert 
    | where ProductName == "Azure Security Center"
    ```

- Consulte la pestaña **Pasos siguientes** en la página del conector para obtener consultas de ejemplo adicionales, plantillas de reglas de análisis y libros recomendados útiles.

## <a name="next-steps"></a>Pasos siguientes

En este documento, ha aprendido a conectar Azure Defender a Azure Sentinel y a sincronizar las alertas entre ellos. Para más información sobre Azure Sentinel, consulte los siguientes artículos:

- Aprenda a [obtener visibilidad de los datos y de posibles amenazas](get-visibility.md).
- Empiece a [detectar amenazas con Azure Sentinel](detect-threats-built-in.md).
- Escriba sus propias reglas para [detectar amenazas](detect-threats-custom.md).