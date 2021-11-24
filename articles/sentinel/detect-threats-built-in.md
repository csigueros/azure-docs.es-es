---
title: Detección de amenazas con reglas de análisis integradas en Microsoft Sentinel | Microsoft Docs
description: Aprenda a usar las reglas de detección de amenazas listas para usar, basadas en plantillas integradas, que le avisan cuando ocurre algo sospechoso.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2021
ms.author: yelevin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 1c9f9fd17c40503e456bb76c19d413b7e4bd8468
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132724356"
---
# <a name="detect-threats-out-of-the-box"></a>Detección de amenazas integrada

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Después de [conectar los orígenes de datos](quickstart-onboard.md) a Microsoft Sentinel, puede recibir una notificación cuando suceda algo sospechoso. Por este motivo, Microsoft Sentinel proporciona plantillas integradas predefinidas para ayudarle a crear reglas de detección de amenazas.

Las plantillas de reglas las ha diseñado un equipo de expertos y analistas en seguridad de Microsoft a partir de amenazas conocidas, vectores de ataque habituales y cadenas de escalado de actividades sospechosas. Las reglas creadas a partir de estas plantillas buscarán automáticamente en el entorno las actividades que parezcan sospechosas. Muchas de las plantillas se pueden personalizar para buscar o filtrar actividades, según sus necesidades. Las alertas generadas por estas reglas crearán incidentes que puede asignar e investigar en su entorno.

Este artículo le ayuda a comprender cómo detectar amenazas con Microsoft Sentinel:

> [!div class="checklist"]
> * Uso de detecciones de amenazas predefinidas
> * Automatizar las respuestas frente a amenazas

## <a name="view-built-in-detections"></a>Visualización de detecciones integradas

Para ver todas las detecciones y reglas de análisis de Microsoft Sentinel, vaya a **Análisis** > **Plantillas de regla**. Esta pestaña contiene todas las reglas integradas de Microsoft Sentinel.

:::image type="content" source="media/tutorial-detect-built-in/view-oob-detections.png" alt-text="Uso de las detecciones integradas para encontrar amenazas con Microsoft Sentinel":::

Entre las detecciones integradas, se incluyen las siguientes:

| Tipo de regla | Descripción |
| --------- | --------- |
| **Seguridad de Microsoft** | Las plantillas de seguridad de Microsoft crean automáticamente incidentes de Microsoft Sentinel a partir de las alertas generadas en otras soluciones de seguridad de Microsoft, en tiempo real. Puede usar las reglas de seguridad de Microsoft como plantilla para crear nuevas reglas con una lógica parecida. <br><br>Para más información sobre las reglas de seguridad, consulte [Creación automática de incidentes a partir de alertas de seguridad de Microsoft](create-incidents-from-alerts.md). |
| <a name="fusion"></a>**Fusion**<br>(algunas detecciones en versión preliminar) | Microsoft Sentinel usa el motor de correlación de Fusion, que cuenta con algoritmos de aprendizaje automático escalables con los que detecta ataques avanzados de varias fases. Para ello, correlaciona muchas alertas y eventos de baja fidelidad de varios productos con incidentes útiles y de alta fidelidad. La fusión está habilitada de manera predeterminada. Dado que la lógica está oculta y, por lo tanto, no se puede personalizar, solo puede crear una regla con esta plantilla. <br><br>El motor de Fusion también puede correlacionar las alertas generadas por las [reglas de análisis programadas](#scheduled) con las de otros sistemas, lo que da lugar a incidentes de alta fidelidad. |
| **Análisis de comportamiento de aprendizaje automático (ML)** | Las plantillas de análisis de comportamiento de aprendizaje automático se basan en algoritmos de aprendizaje automático de Microsoft, por lo que no puede ver la lógica interna de cómo funcionan ni cuándo se ejecutan. <br><br>Dado que la lógica está oculta y, por lo tanto, no se puede personalizar, solo puede crear una regla con cada plantilla de este tipo. |
| <a name="anomaly"></a>**Anomalía**<br>(versión preliminar) | Las plantillas de reglas de anomalías usan SOC-ML (aprendizaje automático) para detectar tipos específicos de comportamiento anómalo. Cada regla tiene sus propios parámetros y umbrales únicos, adecuados para el comportamiento que se está analizando. <br><br>Aunque estas configuraciones de reglas no se pueden cambiar ni ajustar, sí que puede duplicar la regla y cambiar y ajustar el duplicado. En tales casos, ejecute el duplicado en modo **Distribución de paquetes piloto** y el original de manera simultánea en modo **Producción**. Luego compare los resultados y cambie el duplicado a **Producción** siempre que se ajuste a lo que espera. <br><br>Para obtener más información, consulte [Uso de anomalías de SOC-ML para detectar amenazas en Microsoft Sentinel](soc-ml-anomalies.md) y [Uso de reglas de análisis de detección de anomalías en Microsoft Sentinel](work-with-anomaly-rules.md). |
| <a name="scheduled"></a>**Programadas** | Las reglas de análisis programado se basan en consultas integradas escritas por expertos de seguridad de Microsoft. Puede ver la lógica de consulta y realizar cambios en ella. Puede usar la plantilla de reglas programadas y personalizar la lógica de consulta y la configuración de programación para crear otras reglas. <br><br>Varias plantillas de reglas de análisis programadas nuevas generan alertas correlacionadas por el motor de Fusion con alertas de otros sistemas para generar incidentes de alta fidelidad. Para obtener más información, consulte [Detección avanzada de ataques de varias fases](configure-fusion-rules.md#configure-scheduled-analytics-rules-for-fusion-detections).<br><br>**Sugerencia**: Las opciones de programación de reglas incluyen configurar la regla para que se ejecute cada número especificado de minutos, horas o días, y el tiempo empezará a correr al habilitar la regla. <br><br>Recomendamos tener en cuenta cuándo se activa una regla de análisis nueva o editada para garantizar que las reglas reciban la nueva pila de incidentes a tiempo. Por ejemplo, es posible que desee ejecutar una regla en sincronización con el momento en que sus analistas SOC comienzan su jornada laboral, y activar las reglas en ese momento.|
| <a name="nrt"></a>**Casi en tiempo real (NRT)**<br>(versión preliminar) | Las reglas NRT son un conjunto limitado de reglas programadas, diseñadas para ejecutarse una vez al minuto, con el fin de proporcionar información lo más actualizada posible. <br><br>Funcionan principalmente como reglas programadas y se configuran de forma similar, con algunas limitaciones. Para obtener más información, vea [Detección rápida de amenazas con reglas de análisis casi en tiempo real (NRT) en Microsoft Sentinel](near-real-time-rules.md). |
| | |

> [!IMPORTANT]
> - Las plantillas de regla indicadas anteriormente se encuentran actualmente en **VERSIÓN PRELIMINAR**, al igual que algunas de las plantillas de detección de **Fusión** (consulte [Detección avanzada de ataques de varias fases en Microsoft Sentinel](fusion.md) par adeterminar cuáles). Consulte [Términos de uso complementarios para las Versiones preliminares de Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) para conocer los términos legales adicionales que se aplican a las características de Azure que se encuentran en la versión beta, en versión preliminar o que todavía no se han publicado para que estén disponibles con carácter general.
>
> - Mediante la creación y la habilitación de reglas basadas en las plantillas de **análisis de comportamiento de ML**, **se concede permiso a Microsoft para copiar los datos ingeridos fuera de la geografía del área de trabajo de Microsoft Sentinel** según sea necesario para el procesamiento por parte de los modelos y motores de aprendizaje automático.
>

## <a name="use-built-in-analytics-rules"></a>Uso de reglas de análisis integradas

En este procedimiento se describe cómo usar plantillas de reglas de análisis integradas.

**Para usar reglas de análisis integradas**:

1. En la página Microsoft Sentinel > **Análisis** > **Plantillas de regla**, seleccione un nombre de plantilla y, luego, el botón **Crear regla** en el panel de detalles a fin de crear una regla activa basada en esa plantilla. 

    Cada plantilla tiene una lista de orígenes de datos necesarios. Al abrir la plantilla, se comprueba automáticamente la disponibilidad de los orígenes de datos. Si hay un problema de disponibilidad, es posible que el botón **Crear regla** esté deshabilitado, o puede que vea una advertencia a tal efecto.

    :::image type="content" source="media/tutorial-detect-built-in/use-built-in-template.png" alt-text="Panel de vista previa de reglas de detección":::

1. Al seleccionar **Crear regla**, se abrirá el Asistente para la creación de reglas basado en la plantilla seleccionada. Todos los detalles se rellenan automáticamente y, con las plantillas **Programado** o **Microsoft security** (Seguridad de Microsoft), puede personalizar la lógica y otras configuraciones de reglas para satisfacer mejor sus necesidades específicas. Este proceso puede repetirse para crear reglas adicionales en función de la plantilla integrada. Después de seguir los pasos del Asistente para la creación de reglas hasta el final, habrá terminado de crear una regla basada en la plantilla. Las nuevas reglas aparecerán en la pestaña **Active rules** (Reglas activas).

    Para obtener más detalles sobre cómo personalizar las reglas en el Asistente para la creación de reglas, consulte [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md).

> [!TIP]
> - Asegúrese de **habilitar todas las reglas asociadas con los orígenes de datos conectados** a fin de garantizar una cobertura de seguridad completa para el entorno. La manera más eficaz de habilitar las reglas de análisis es hacerlo directamente en la página del conector de datos, que enumera las reglas relacionadas. Para obtener más información, consulte [Conexión con orígenes de datos](connect-data-sources.md).
> 
> - También puede **insertar reglas en Microsoft Sentinel mediante [API](/rest/api/securityinsights/) y [PowerShell](https://www.powershellgallery.com/packages/Az.SecurityInsights/0.1.0)** , aunque para ello es necesario un trabajo adicional. 
> 
>     Al usar la API o PowerShell, debe exportar las reglas a JSON antes de habilitar las reglas. La API o PowerShell pueden ser útiles al habilitar reglas en varias instancias de Microsoft Sentinel con una configuración idéntica en cada instancia.
> 
## <a name="export-rules-to-an-arm-template"></a>Exportación de reglas a una plantilla de ARM

Puede [exportar fácilmente la regla a una plantilla de Azure Resource Manager (ARM)](import-export-analytics-rules.md) si desea administrar e implementar las reglas como código. También puede importar reglas de archivos de plantilla para verlos y editarlos en la interfaz de usuario.

## <a name="next-steps"></a>Pasos siguientes

- Para crear reglas personalizadas, use las reglas existentes como plantillas o referencias. El uso de reglas existentes como base de referencia ayuda al crear la mayor parte de la lógica antes de realizar los cambios necesarios. Para más información, consulte [Creación de reglas de análisis personalizadas para detectar amenazas](detect-threats-custom.md).

- Para aprender a automatizar las respuestas a las amenazas, consulte [Configuración de respuestas automatizadas frente a amenazas en Microsoft Sentinel](tutorial-respond-threats-playbook.md).
