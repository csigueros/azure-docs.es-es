---
title: La exportación continua puede enviar alertas y recomendaciones de Microsoft Defender for Cloud para áreas de trabajo de Log Analytics o Azure Event Hubs
description: Aprenda a configurar la exportación continua de alertas de seguridad y recomendaciones a áreas de trabajo de Log Analytics o a Azure Event Hubs.
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 11/02/2021
ms.author: memildin
ms.openlocfilehash: 628958b2b378b6b6d36b98c08f49c2f453f19af7
ms.sourcegitcommit: 61f87d27e05547f3c22044c6aa42be8f23673256
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/09/2021
ms.locfileid: "132056290"
---
# <a name="continuously-export-microsoft-defender-for-cloud-data"></a>Exportación continua de datos de Microsoft Defender for Cloud

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Microsoft Defender for Cloud genera recomendaciones y alertas de seguridad detalladas. Puede verlas en el portal o mediante herramientas de programación. También puede que deba exportar parte o la totalidad de esta información para realizar el seguimiento con otras herramientas de supervisión de su entorno. 

Con la **exportación continua**, puede personalizar completamente *qué* se va a exportar y *dónde* irá. Por ejemplo, puede configurarla para que:

- Todas las alertas de gravedad alta se envíen a una instancia de Azure Event Hubs.
- Todos los hallazgos de gravedad media o superior de los exámenes de evaluación de vulnerabilidades de los servidores SQL Server se envíen a un área de trabajo de Log Analytics específica.
- Se entreguen recomendaciones específicas a un centro de eventos o área de trabajo de Log Analytics cada vez que se generen. 
- La puntuación segura para una suscripción se envía a un área de trabajo de Log Analytics cada vez que la puntuación de un control cambia en 0,01 o más. 

Aunque la característica se denomina *continua*, también hay una opción para exportar instantáneas semanales.

En este artículo se describe cómo configurar la exportación continua a áreas de trabajo de Log Analytics o a Azure Event Hubs.

> [!NOTE]
> Si necesita integrar Defender for Cloud con un SIEM, consulte [Transmisión de alertas a una solución de administración de servicios de TI, SIEM o SOAR](export-to-siem.md).

> [!TIP]
> Defender for Cloud también ofrece la opción de realizar una exportación manual única a CSV. Obtenga más información en [Exportación manual de un solo uso de alertas y recomendaciones](#manual-one-time-export-of-alerts-and-recommendations).


## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|Gratuito|
|Roles y permisos necesarios:|<ul><li>**Administrador de seguridad** o **Propietario** en el grupo de recursos.</li><li>Permisos de escritura para el recurso de destino.</li><li>Si utiliza las directivas "DeployIfNotExist" de Azure Policy que se describen a continuación, también necesitará permisos para asignar directivas.</li><li>Para exportar datos al centro de eventos, necesitará permiso de escritura en la directiva del centro de eventos.</li><li>Para exportar a un área de trabajo de Log Analytics:<ul><li>Si tiene la **solución SecurityCenterFree**, necesitará un mínimo de permisos de lectura para la solución del área de trabajo: `Microsoft.OperationsManagement/solutions/read`</li><li>Si no tiene la **solución SecurityCenterFree**, necesitará permisos de escritura para la solución del área de trabajo: `Microsoft.OperationsManagement/solutions/action`</li><li>Más información sobre las [soluciones de Azure Monitor y de área de trabajo de Log Analytics](../azure-monitor/insights/solutions.md)</li></ul></li></ul>|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Nacionales o soberanas (Azure Government y Azure China 21Vianet)| 
|||


## <a name="what-data-types-can-be-exported"></a>¿Qué tipos de datos se pueden exportar?

La exportación continua puede exportar los siguientes tipos de datos siempre que cambien:

- Alertas de seguridad.
- Recomendaciones de seguridad.
- Conclusiones de seguridad. Se puede pensar en ellas como en recomendaciones "secundarias" que pertenecen a una recomendación "principal". Por ejemplo:
    - La recomendación "Se deben instalar actualizaciones del sistema en las máquinas" tendrá una recomendación "secundaria" para cada actualización del sistema pendiente.
    - La recomendación "Es necesario corregir las vulnerabilidades de las máquinas virtuales" tendrá una recomendación "secundaria" para cada vulnerabilidad identificada por el analizador de vulnerabilidades.
    > [!NOTE]
    > Si va a configurar una exportación continua con la API REST, incluya siempre el elemento primario con los resultados. 
- Puntuación segura por suscripción o por control.
- Datos de cumplimiento normativo.


## <a name="set-up-a-continuous-export"></a>Configuración de una exportación continua 

Puede configurar la exportación continua desde las páginas de Microsoft Defender for Cloud en Azure Portal, a través de la API de REST o a gran escala con las plantillas de Azure Policy proporcionadas. Seleccione la pestaña correspondiente a continuación para obtener detalles de cada una de las opciones.

### <a name="use-the-azure-portal"></a>[**Uso de Azure Portal**](#tab/azure-portal)

### <a name="configure-continuous-export-from-the-defender-for-cloud-pages-in-azure-portal"></a>Configuración de la exportación continua desde las páginas de Defender for Cloud en Azure Portal

Los pasos siguientes son necesarios si va a configurar una exportación continua a las área de trabajo de Log Analytics o a Azure Event Hubs.

1. En el menú de Defender for Cloud, abra **Parámetros del entorno**.

1. Seleccione la suscripción específica para la que quiere configurar la exportación de datos.

1. En la barra lateral de la página de configuración de esa suscripción, seleccione **Exportación continua**.

    :::image type="content" source="./media/continuous-export/continuous-export-options-page.png" alt-text="Opciones de exportación en Microsoft Defender for Cloud.":::

    Aquí verá las opciones de exportación. Hay una pestaña para cada destino de exportación disponible. 

1. Seleccione el tipo de datos que quiere exportar y elija los filtros que quiera de cada tipo (por ejemplo, exportar solo alertas de gravedad alta).

1. Seleccione la frecuencia de exportación adecuada:
    - **Streaming**: las evaluaciones se enviarán cuando se actualice el estado de mantenimiento de un recurso (si no se produce ninguna actualización, no se enviarán datos).
    - **Instantáneas**: se enviará una vez a la semana por suscripción una instantánea del estado actual de los tipos de datos seleccionados. Para identificar los datos de la instantánea, busque el campo ``IsSnapshot``.

1. Opcionalmente, si la selección incluye una de estas recomendaciones, puede incluir los resultados de la evaluación de vulnerabilidades junto con ellas:
    - Las bases de datos SQL deben tener resueltos los hallazgos de vulnerabilidades.
    - Los servidores SQL de las máquinas deben tener resueltos los hallazgos de vulnerabilidades.
    - Es necesario corregir las vulnerabilidades de las imágenes de Azure Container Registry (con tecnología de Qualys)
    - Es necesario corregir las vulnerabilidades de las máquinas virtuales
    - Se deben instalar actualizaciones del sistema en las máquinas

    Para incluir los resultados con estas recomendaciones, habilite la opción de **incluir resultados de seguridad**.

    :::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Incluir la alternancia de los resultados de seguridad en la configuración de la exportación continua." :::

1. En el área "Destino de exportación", elija dónde quiere guardar los datos. Los datos se pueden guardar en el destino de una suscripción diferente (por ejemplo, en una instancia central del Centro de eventos o en un área de trabajo central de Log Analytics).
1. Seleccione **Guardar**.

### <a name="use-the-rest-api"></a>[**Uso de la API de REST**](#tab/rest-api)

### <a name="configure-continuous-export-using-the-rest-api"></a>Configuración de la exportación continua mediante la API REST

La exportación continua se puede configurar y administrar mediante las [API de automatizaciones](/rest/api/securitycenter/automations) de Microsoft Defender for Cloud. Use esta API para crear o actualizar reglas para exportarlas a cualquiera de los siguientes destinos posibles:

- Centro de eventos de Azure
- Área de trabajo de Log Analytics
- Azure Logic Apps 

La API proporciona funcionalidad adicional que no está disponible en Azure Portal, por ejemplo:

* **Mayor volumen**: con la API, puede crear varias configuraciones de exportación en una sola suscripción. La página **Exportación continua** de la interfaz de usuario del portal de Defender for Cloud solo admite una configuración de exportación por suscripción.

* **Características adicionales**: la API ofrece parámetros adicionales que no se muestran en la interfaz de usuario. Por ejemplo, puede agregar etiquetas a su recurso de automatización, así como definir su exportación en función de un mayor conjunto de alertas y propiedades recomendadas que las que se ofrecen en la página **Exportación continua** de la interfaz de usuario del portal de Defender for Cloud.

* **Ámbito más centrado**: la API proporciona un nivel más detallado del ámbito de sus configuraciones de exportación. Cuando se define una exportación con la API, se puede hacer a nivel de grupo de recursos. Si va a usar la página **Exportación continua** en la interfaz de usuario del portal de Defender for Cloud, tiene que definirla en el nivel de suscripción.

    > [!TIP]
    > Si ha configurado la exportación múltiple mediante la API o si ha usado parámetros que son solo de la API, esas características adicionales no se mostrarán en la interfaz de usuario de Defender for Cloud. En su lugar, habrá un banner que le informará de que existen otras configuraciones.

En la [documentación de la API REST](/rest/api/securitycenter/automations) encontrará más información sobre la API de automatizaciones.

### <a name="deploy-at-scale-with-azure-policy"></a>[**Implementación a gran escala con Azure Policy**](#tab/azure-policy)

### <a name="configure-continuous-export-at-scale-using-the-supplied-policies"></a>Configuración de la exportación continua a gran escala con las directivas proporcionadas

La automatización de los procesos de supervisión y respuesta ante incidentes de la organización puede mejorar considerablemente el tiempo necesario para investigar y mitigar los incidentes de seguridad.

Para implementar las configuraciones de la exportación continua en la organización, use las directivas "DeployIfNotExist" de Azure Policy proporcionadas que se describen a continuación para crear y configurar los procedimientos de exportación continua.

**Para implementar estas directivas**

1. Desde la tabla siguiente, seleccione la directiva que quiere aplicar:

    |Objetivo  |Directiva  |Id. de directiva  |
    |---------|---------|---------|
    |Exportación continua al centro de eventos|[Implementación de la exportación al centro de eventos para recomendaciones y alertas de Microsoft Defender for Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
    |Exportación continua a las áreas de trabajo de Log Analytics|[Implementación de la exportación al área de trabajo de Log Analytics para recomendaciones y alertas de Microsoft Defender for Cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
    ||||

    > [!TIP]
    > También puede encontrarlos buscando Azure Policy:
    > 1. Abra Azure Policy.
    > :::image type="content" source="./media/continuous-export/opening-azure-policy.png" alt-text="Acceso a Azure Policy.":::
    > 2. En el menú Azure Policy, seleccione **Definiciones** y realice la búsqueda por nombre. 

1. En la página pertinente de Azure Policy, seleccione **Asignar**.
    :::image type="content" source="./media/continuous-export/export-policy-assign.png" alt-text="Asignación de la directiva de Azure.":::

1. Abra cada pestaña y establezca los parámetros como quiera:
    1. En la pestaña **Aspectos básicos**, establezca el ámbito de la directiva. Para usar la administración centralizada, asigne la directiva al grupo de administración que contiene las suscripciones que usarán la configuración de la exportación continua. 
    1. En la pestaña **Parámetros**, establezca el grupo de recursos y los detalles del tipo de datos. 
        > [!TIP]
        > Cada parámetro tiene información sobre herramientas que explica las opciones disponibles.
        >
        > La pestaña Parámetros de Azure Policy (1) proporciona acceso a opciones de configuración similares, como la página de exportación continua de Defender for Cloud (2).
        > :::image type="content" source="./media/continuous-export/azure-policy-next-to-continuous-export.png" alt-text="Comparación de los parámetros de la exportación continua con Azure Policy." lightbox="./media/continuous-export/azure-policy-next-to-continuous-export.png":::
    1. Opcionalmente, para aplicar esta asignación a las suscripciones existentes, abra la pestaña **Corrección** y seleccione la opción para crear una tarea de corrección.
1. Revise la página de resumen y seleccione **Crear**.

--- 

## <a name="information-about-exporting-to-a-log-analytics-workspace"></a>Información sobre la exportación a un área de trabajo de Log Analytics

Si quiere analizar datos de Microsoft Defender for Cloud dentro de un área de trabajo de Log Analytics o usar alertas de Azure junto con alertas de Defender for Cloud, configure la exportación continua al área de trabajo de Log Analytics.

### <a name="log-analytics-tables-and-schemas"></a>Tablas y esquemas de Log Analytics

Las alertas y recomendaciones de seguridad se almacenan en las tablas *SecurityAlert* y *SecurityRecommendation* respectivamente. 

El nombre de la solución de Log Analytics que contiene estas tablas depende de si ha habilitado las características de seguridad mejorada: Seguridad ("Seguridad y auditoría") o SecurityCenterFree. 

> [!TIP]
> Para ver los datos en el área de trabajo de destino, debe habilitar una de estas soluciones: **Security and Audit** o **SecurityCenterFree**.

![Tabla *SecurityAlert* de Log Analytics.](./media/continuous-export/log-analytics-securityalert-solution.png)

Para ver los esquemas de eventos de los tipos de datos exportados, visite el artículo sobre los [esquemas de tabla de Log Analytics](https://aka.ms/ASCAutomationSchemas).


##  <a name="view-exported-alerts-and-recommendations-in-azure-monitor"></a>Visualización de las recomendaciones y alertas exportadas en Azure Monitor

También puede elegir ver las alertas o las recomendaciones de seguridad exportadas en [Azure Monitor](../azure-monitor/alerts/alerts-overview.md). 

Azure Monitor proporciona un conjunto de alertas unificadas que consta de varias alertas de Azure, como el registro de diagnóstico, las alertas de métricas y las alertas personalizadas basadas en las consultas del área de trabajo de Log Analytics.

Para ver las alertas y recomendaciones de Defender for Cloud en Azure Monitor, configure una regla de alerta basada en consultas de Log Analytics (Alerta de registro):

1. En la página **Alertas** de Azure Monitor, seleccione **Nueva regla de alerta**.

    ![Página de alertas de Azure Monitor.](./media/continuous-export/azure-monitor-alerts.png)

1. En la página de creación de reglas, configure la nueva regla (de la misma manera que configuraría una [regla de alertas de registro en Azure Monitor](../azure-monitor/alerts/alerts-unified-log.md)):

    * En **Recurso**, seleccione el área de trabajo de Log Analytics a la que exportó las recomendaciones y las alertas de seguridad.

    * En **Condición**, seleccione **Custom log search** (Búsqueda de registros personalizada). En la página que aparece, configure la consulta, el período de retrospectiva y el período de frecuencia. En la consulta de búsqueda, puede escribir *SecurityAlert* o *SecurityRecommendation* para consultar los tipos de datos que Defender for Cloud exporta continuamente al habilitar la característica de exportación continua a Log Analytics. 
    
    * Opcionalmente, en [Grupo de acciones](../azure-monitor/alerts/action-groups.md), configure el grupo de acciones que desea desencadenar. Los grupos de acciones pueden desencadenar el envío de correo electrónico, los vales de ITSM, los webhooks, etc.
    ![Regla de alertas de Azure Monitor.](./media/continuous-export/azure-monitor-alert-rule.png)

Ahora verá las nuevas alertas o recomendaciones de Microsoft Defender for Cloud (según las reglas de exportación continua configuradas y la condición definida en la regla de alerta de Azure Monitor) en las alertas de Azure Monitor, con el desencadenamiento automático de un grupo de acciones (si se proporciona).

## <a name="manual-one-time-export-of-alerts-and-recommendations"></a>Exportación manual de un solo uso de alertas y recomendaciones

Para descargar un informe en formato CSV con alertas o recomendaciones, abra las páginas **Alertas de seguridad** o **Recomendaciones** y seleccione el botón para **Download CSV report** (Descargar informe en CSV).

> [!TIP]
> Debido a las limitaciones de Azure Resource Graph, los informes se limitan a un tamaño de archivo de 13 000 filas. Si observa errores relacionados con la exportación de demasiados datos, intente limitar la salida seleccionando un conjunto más pequeño de suscripciones para exportar.

:::image type="content" source="./media/continuous-export/download-alerts-csv.png" alt-text="Descargar datos de alertas como un archivo CSV." lightbox="./media/continuous-export/download-alerts-csv.png":::

> [!NOTE]
> Estos informes contienen alertas y recomendaciones para los recursos de las suscripciones seleccionadas actualmente.


## <a name="faq---continuous-export"></a>Preguntas frecuentes: exportación continua

### <a name="what-are-the-costs-involved-in-exporting-data"></a>¿Cuáles son los costos de la exportación de datos?

La habilitación de una exportación continua no tiene costo alguno. Sin embargo, la ingesta y retención de datos del área de trabajo de Log Analytics, pueden tener costo, en función de la configuración. 

Obtenga más información sobre los [precios del área de trabajo de Log Analytics](https://azure.microsoft.com/pricing/details/monitor/).

Obtenga más información sobre los [precios de Azure Event Hubs](https://azure.microsoft.com/pricing/details/event-hubs/).


### <a name="does-the-export-include-data-about-the-current-state-of-all-resources"></a>¿La exportación incluye datos sobre el estado actual de todos los recursos?

No. La exportación continua se creó para la transmisión de **eventos**:

- Las **alertas** recibidas antes de habilitar la exportación no se exportarán.
- Se envían **recomendaciones** cuando cambia el estado de cumplimiento de un recurso. Por ejemplo, cuando un recurso pasa de un estado correcto a otro incorrecto. Por lo tanto, como sucede con las alertas, no se exportarán las recomendaciones para los recursos que no hayan cambiado de estado desde que se habilitó la exportación.
- La **puntuación de seguridad** por control de seguridad o suscripción se envía cuando cambia la puntuación de un control de seguridad en 0,01 o más. 
- El **estado de cumplimiento normativo** se envía cuando cambia el estado del cumplimiento del recurso.



### <a name="why-are-recommendations-sent-at-different-intervals"></a>¿Por qué se envían recomendaciones en distintos intervalos?

Las distintas recomendaciones tienen intervalos de valoración de cumplimiento diferentes, que pueden variar de unos minutos a cada pocos días. Por lo tanto, la cantidad de tiempo que las recomendaciones tardan en aparecer en las exportaciones es distinta.

### <a name="does-continuous-export-support-any-business-continuity-or-disaster-recovery-bcdr-scenarios"></a>¿La exportación continua es compatible con los escenarios de continuidad del negocio o recuperación ante desastres (BCDR)?

Al preparar el entorno para escenarios de BCDR, en los que el recurso de destino está experimentando una interrupción u otro desastre, la organización tiene la responsabilidad de evitar la pérdida de datos mediante la preparación de copias de seguridad de acuerdo con las instrucciones de Azure Event Hubs, el área de trabajo de Log Analytics y la aplicación lógica.

Obtenga más información en [Recuperación ante desastres geográfica de Azure Event Hubs](../event-hubs/event-hubs-geo-dr.md).


### <a name="is-continuous-export-available-for-free"></a>¿La exportación continua está disponible de forma gratuita?

Sí. Tenga en cuenta que muchas alertas solo se proporcionan cuando se han habilitado protecciones avanzadas. Un buen método para obtener una vista previa de las alertas que obtendrá en los datos exportados consiste en ver las alertas que se muestran en las páginas de Defender for Cloud en Azure Portal.



## <a name="next-steps"></a>Pasos siguientes

En este artículo, ha aprendido a configurar exportaciones continuas de sus recomendaciones y alertas. También aprendió a descargar los datos de alertas como un archivo CSV. 

Para obtener material relacionado, consulte la documentación siguiente: 

- Obtenga más información sobre las [plantillas de automatización de flujos de trabajo](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).
- [Documentación de Azure Event Hubs](../event-hubs/index.yml)
- [Documentación de Microsoft Sentinel](../sentinel/index.yml)
- [Documentación sobre Azure Monitor](../azure-monitor/index.yml)
- [Exportación de esquemas de tipos de datos](https://aka.ms/ASCAutomationSchemas)