---
title: Configuración de reglas de detección de ataques en varias fases (Fusion) en Microsoft Sentinel
description: Creación y configuración de reglas de detección de ataques basadas en la tecnología Fusion en Microsoft Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 2a684bf84438bc743d8ce30e62faa539310df024
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132716170"
---
# <a name="configure-multistage-attack-detection-fusion-rules-in-microsoft-sentinel"></a>Configuración de reglas de detección de ataques en varias fases (Fusion) en Microsoft Sentinel

> [!IMPORTANT]
> La nueva versión de la regla de análisis de Fusion se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

[!INCLUDE [reference-to-feature-availability](includes/reference-to-feature-availability.md)]

Microsoft Sentinel usa Fusion, un motor de correlación basado en algoritmos de aprendizaje automático escalable, para detectar automáticamente ataques de varias fases al identificar combinaciones de comportamientos anómalos y de actividades sospechosas que se observan en diversas fases de la cadena de eliminación. A partir de estas detecciones, Microsoft Sentinel genera incidentes que, de otro modo, serían muy difíciles de detectar. Estos incidentes incluyen dos o más alertas o actividades. Por diseño, estos incidentes tienen poco volumen, alta fidelidad y alta gravedad.

Cuando se personaliza para adaptarla a su entorno, esta tecnología de detección no solo reduce las tasas de [falsos positivos](false-positives.md), sino que también puede detectar ataques con información limitada o que falta.

## <a name="configure-fusion-rules"></a>Configuración de reglas de Fusion

Esta detección está habilitada de forma predeterminada en Microsoft Sentinel. Para comprobar o cambiar el estado, siga estas instrucciones:

1. Inicie sesión en [Azure Portal](https://portal.azure.com) y escriba **Microsoft Sentinel**.

1. En el menú de navegación de Microsoft Sentinel, seleccione **Análisis**.

1. Seleccione la pestaña **Reglas activas** y busque **Advanced Multistage Attack Detection** (Detección avanzada de ataques en varias fases) en la columna **NOMBRE** filtrando la lista por el tipo de regla **Fusion**. Compruebe la columna **ESTADO** para confirmar si esta detección está habilitada o deshabilitada.

    :::image type="content" source="./media/fusion/selecting-fusion-rule-type.png" alt-text="Captura de pantalla de la regla de análisis de Fusion." lightbox="./media/fusion/selecting-fusion-rule-type.png":::

1. Para cambiar el estado, seleccione esta entrada y, en el panel de vista previa **Advanced Multistage Attack Detection** (Detección avanzada de ataques en varias fases), seleccione **Editar**.

1. En la pestaña **General** del **Asistente para reglas de análisis**, fíjese en el estado (Habilitado/Deshabilitado) o cámbielo si lo desea.

    Si ha cambiado el estado pero no tiene que realizar más cambios, seleccione la pestaña **Revisar y actualizar** y seleccione **Guardar**.

    Para una configuración más avanzada de la regla de detección de Fusion, seleccione **Siguiente: Configurar Fusion**.

    :::image type="content" source="media/configure-fusion-rules/configure-fusion-rule.png" alt-text="Captura de pantalla de la configuración de reglas de Fusion." lightbox="media/configure-fusion-rules/configure-fusion-rule.png":::

1. **Configuración de señales de origen para la detección de Fusion**: se recomienda incluir todas las señales de origen enumeradas, con todos los niveles de gravedad, para obtener el mejor resultado. De forma predeterminada, ya están todos incluidos, pero tiene la opción de realizar cambios de las maneras siguientes:

    > [!NOTE]
    > Si excluye una señal de origen determinada o un nivel de gravedad de alerta, no se desencadenarán las detecciones de Fusion que se basen en señales de ese origen o en alertas que coincidan con ese nivel de gravedad. 
    
    - **Excluya las señales de las detecciones de Fusion**, incluidas las anomalías, las alertas de varios proveedores y los registros sin procesar.
     
        *Caso de uso:* si está probando un origen de señal específico que se sabe que genera alertas ruidosas, puede desactivar temporalmente las señales de ese origen de señal concreto para las detecciones de Fusion.

    - **Configurar la gravedad de la alerta para cada proveedor**: por diseño, el modelo Fusion ML correlaciona las señales de baja fidelidad en un único incidente de gravedad alta en función de las señales anómalas en la cadena de eliminación de varios orígenes de datos. Las alertas incluidas en Fusion suelen ser de gravedad inferior (media, baja, informativa), pero en ocasiones se incluyen alertas de gravedad alta relevantes.
     
        *Caso de uso:* si tiene un proceso independiente para evaluar e investigar alertas de gravedad alta y prefiere no incluir estas alertas en Fusion, puede configurar las señales de origen para excluir las alertas de gravedad alta de las detecciones de Fusion. 



    - **Excluir patrones de detección específicos de la detección de Fusion**. Es posible que determinadas detecciones de Fusion no sean aplicables a su entorno o que sean propensas a generar falsos positivos. Si desea excluir un patrón de detección de Fusion específico, siga las instrucciones siguientes:

        1. Busque y abra un incidente de Fusion del tipo que desea excluir.

        1. En la sección **Descripción**, seleccione **Mostrar más**.

        1. En **Excluir este patrón de detección específico**, seleccione **vínculo de exclusión**, que le redirigirá a la pestaña **Configurar Fusion** del asistente para reglas de análisis.

            :::image type="content" source="media/configure-fusion-rules/exclude-fusion-incident.png" alt-text="Captura de pantalla del incidente de Fusion. Seleccione el vínculo de exclusión.":::

        En la pestaña **Configurar Fusion**, verá que el patrón de detección (una combinación de alertas y anomalías en un incidente de Fusion) se ha agregado a la lista de exclusión, junto con la hora en que se agregó el patrón de detección.

        Puede quitar un patrón de detección excluido en cualquier momento si selecciona el icono de papelera en ese patrón de detección.

        :::image type="content" source="media/configure-fusion-rules/exclusion-patterns-list.png" alt-text="Captura de pantalla de la lista de patrones de detección excluidos.":::

        Los incidentes que coincidan con patrones de detección excluidos se seguirán desencadenando, pero **no se mostrarán en la cola de incidentes activos**. Se rellenarán automáticamente con los siguientes valores:

        - **Estado**: "cerrado"
        
        - **Clasificación de cierre**: "indeterminado"

        - **Comentario**: "Patrón de detección de Fusion excluido y cerrado automáticamente"

        - **Etiqueta**: "ExcludedFusionDetectionPattern": puede consultar en esta etiqueta para ver todos los incidentes que coinciden con este patrón de detección.

            :::image type="content" source="media/configure-fusion-rules/auto-closed-incident.png" alt-text="Captura de pantalla del incidente de Fusion excluido y cerrado automáticamente.":::



> [!NOTE]
> Actualmente, Microsoft Sentinel usa 30 días de datos del historial para entrenar los sistemas de aprendizaje automático. Estos datos siempre se cifran mediante las claves de Microsoft cuando pasan por la canalización de aprendizaje automático. Sin embargo, los datos de entrenamiento no se cifran mediante [claves administradas por el cliente (CMK)](customer-managed-keys.md) si CMK se ha habilitado en el área de trabajo de Microsoft Sentinel. Para deshabilitar Fusion, vaya a **Microsoft Sentinel** \> **Configuración** \> **Análisis \> Reglas activas**, haga clic con el botón derecho en la regla **Detección avanzada de ataques de varias fases** y seleccione **Deshabilitar**.

## <a name="configure-scheduled-analytics-rules-for-fusion-detections"></a>Configuración de reglas de análisis programadas para las detecciones de Fusion

> [!IMPORTANT]
>
> - La detección basada en Fusion mediante alertas de reglas de análisis se encuentra actualmente en **VERSIÓN PRELIMINAR**. Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.

**Fusion** puede detectar ataques y amenazas emergentes en varias fases en función del escenario mediante las alertas que generan las [reglas de análisis programadas](detect-threats-custom.md). Se recomienda realizar los pasos siguientes para configurar y habilitar estas reglas, con el fin de que pueda sacar el máximo partido de las funcionalidades de fusión de Microsoft Sentinel.

1. La fusión de amenazas emergentes puede usar alertas generadas por las reglas de análisis programadas, tanto las [integradas](detect-threats-built-in.md#scheduled) como las [creadas por los analistas de seguridad](detect-threats-custom.md), que contienen información sobre la cadena de eliminación (tácticas) y la asignación de entidades. Para asegurarse de que Fusion puede usar la salida de una regla de análisis para detectar amenazas emergentes:

    - Revise la **asignación de entidades** para estas reglas programadas. Use la [sección de configuración de asignación de entidades](map-data-fields-to-entities.md) para asignar parámetros de los resultados de las consultas a entidades reconocidas por Microsoft Sentinel. Puesto que Fusion pone en correlación las alertas en función de las entidades (como la *cuenta de usuario* o la *dirección IP*), los algoritmos de Machine Learning no pueden realizar la coincidencia de alertas sin la información de la entidad.

    - Revise las **tácticas** en los detalles de las reglas de análisis. El algoritmo de Machine Learning de Fusion usa la información de las tácticas de [MITRE ATT&CK](https://attack.mitre.org/) para detectar ataques en varias fases y las tácticas con las que se etiqueten las reglas de análisis se mostrarán en los incidentes resultantes. Los cálculos de Fusion pueden verse afectados si a las alertas entrantes les falta información de las tácticas.

1. Fusion puede también detectar amenazas en función del escenario con reglas basadas en las siguientes **plantillas de reglas de análisis programadas**, que se pueden encontrar en la pestaña **Plantillas de regla** de la hoja **Análisis**. Para habilitar estas detecciones, seleccione el nombre de la regla en la galería de plantillas y haga clic en **Crear regla** en el panel de detalles.

    - [Cisco - firewall block but success logon to Azure AD](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/SigninFirewallCorrelation.yaml) (Cisco: el firewall está bloqueado, pero se ha iniciado sesión correctamente en Azure AD)
    - [Fortinet - Beacon pattern detected](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/CommonSecurityLog/Fortinet-NetworkBeaconPattern.yaml) (Fortinet: se ha detectado un patrón de señal)
    - [IP with multiple failed Azure AD logins successfully logs in to Palo Alto VPN](https://github.com/Azure/Azure-Sentinel/blob/60e7aa065b196a6ed113c748a6e7ae3566f8c89c/Detections/MultipleDataSources/HostAADCorrelation.yaml) (Una IP con varios inicios de sesión en Azure AD con errores se registra correctamente en la VPN de Palo Alto)
    - [Multiple Password Reset by user](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/MultipleDataSources/MultiplePasswordresetsbyUser.yaml) (Restablecimiento de contraseña múltiple por parte del usuario)
    - [Rare application consent](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AuditLogs/RareApplicationConsent.yaml) (Consentimiento de aplicación rara)
    - [SharePointFileOperation via previously unseen IPs](https://github.com/Azure/Azure-Sentinel/blob/master/Detections/OfficeActivity/SharePoint_Downloads_byNewIP.yaml) (SharePointFileOperation a través de direcciones IP no vistas anteriormente)
    - [Suspicious Resource deployment](https://github.com/Azure/Azure-Sentinel/blob/83c6d8c7f65a5f209f39f3e06eb2f7374fd8439c/Detections/AzureActivity/NewResourceGroupsDeployedTo.yaml) (Implementación de recursos sospechosos)

    > [!NOTE]
    > En el caso del conjunto de reglas de análisis programadas que usa Fusion, el algoritmo de Machine Learning realiza una coincidencia aproximada de las consultas de KQL que se proporcionan en las plantillas. El cambio del nombre de las plantillas no afectará a las detecciones de Fusion.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre [Detecciones de Fusion en Microsoft Sentinel](fusion.md).

Obtenga más información sobre las [detecciones de Fusion basadas en escenarios](fusion-scenario-reference.md).

Ahora que conoce más detalles sobre la detección avanzada de ataques de varias fases, puede que le interese el siguiente inicio rápido para aprender a obtener visibilidad sobre sus datos y a detectar posibles amenazas:[Introducción a Microsoft Sentinel](get-visibility.md).

Si está preparado para investigar los incidentes que se han creado, consulte el siguiente tutorial:[Investigar incidentes con Microsoft Sentinel](investigate-cases.md).
