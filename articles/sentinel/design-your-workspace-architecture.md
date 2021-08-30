---
title: Diseño de una arquitectura de áreas de trabajo de Azure Sentinel | Microsoft Docs
description: Use un árbol de decisión para conocer cómo podría interesarle diseñar la arquitectura de áreas de trabajo de Azure Sentinel.
services: sentinel
author: batamig
ms.author: bagol
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.topic: conceptual
ms.date: 07/18/2021
ms.openlocfilehash: 930194c3a415a39edacd9ad8c680a872e1760e03
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122178301"
---
# <a name="design-your-azure-sentinel-workspace-architecture"></a>Diseño de una arquitectura de áreas de trabajo de Azure Sentinel

En este artículo se proporciona un árbol de decisión que le ayudará a tomar decisiones clave sobre cómo diseñar su arquitectura de áreas de trabajo de Azure Sentinel. Para obtener más información, consulte [Diseños de área de trabajo de ejemplo de Azure Sentinel](sample-workspace-designs.md) y [Procedimientos recomendados de arquitectura de áreas de trabajo de Azure Sentinel](best-practices-workspace-architecture.md).

## <a name="prerequisites"></a>Requisitos previos

Antes de trabajar con el árbol de decisión, asegúrese de contar con la siguiente información:

|Requisito previo  | Descripción |
|---------|---------|
|**Requisitos normativos relacionados con la residencia de datos de Azure**     |  Azure Sentinel se puede ejecutar en áreas de trabajo en la mayoría de las regiones [compatibles en GA con Log Analytics](https://azure.microsoft.com/global-infrastructure/services/?products=monitor), pero no en todas. Las regiones de Log Analytics recién admitidas pueden tardar cierto tiempo en incorporarse al servicio Azure Sentinel. <br><br> Los datos que genera Azure Sentinel, como incidentes, marcadores y reglas de análisis, pueden contener datos del cliente procedentes de las áreas de trabajo de Log Analytics del cliente.<br><br> Para obtener más información, consulte [Disponibilidad geográfica y residencia de datos](quickstart-onboard.md#geographical-availability-and-data-residency).|
|**Orígenes de datos**     |   Descubra qué [orígenes de datos](connect-data-sources.md) necesita conectar, incluidos conectores integrados a soluciones de Microsoft y de otros fabricantes. También puede usar el formato de evento común (CEF), Syslog o la API REST para conectar los orígenes de datos con Azure Sentinel. <br><br>Si tiene máquinas virtuales de Azure en varias ubicaciones de Azure de las que necesita recopilar registros, y es importante para usted ahorrar en el coste de salida de datos, calcule el coste de salida de datos mediante la [calculadora de precios de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/#overview) para cada ubicación de Azure.      |
|**Roles de usuario y niveles o permisos de acceso a datos**     |    Azure Sentinel usa el [control de acceso basado en rol (RBAC)](../role-based-access-control/role-assignments-portal.md) para proporcionar [roles integrados](../role-based-access-control/built-in-roles.md) que se pueden asignar a usuarios, grupos y servicios en Azure. <br><br>Todos los roles integrados de Azure Sentinel conceden acceso de lectura a los datos en el área de trabajo de Azure Sentinel. Por lo tanto, debe averiguar si es necesario controlar el acceso a los datos por origen de datos o nivel de fila, ya que esta circunstancia afectará a la decisión sobre el diseño de las áreas de trabajo. Para obtener más información, consulte [Roles personalizados y RBAC avanzado de Azure](roles.md#custom-roles-and-advanced-azure-rbac).     |
|**Tasa de ingesta de datos**     |  La tasa de ingesta diaria, expresada normalmente en GB/día, es uno de los factores clave al considerar la gestión de costes y la planificación en el diseño de áreas de trabajo para Azure Sentinel. <br><br>En la mayoría de los entornos híbridos y en la nube, los dispositivos de red, como firewalls o servidores proxy, y los servidores Windows y Linux generan los datos más ingeridos. Para obtener los resultados más precisos, Microsoft recomienda un inventario exhaustivo de orígenes de datos. <br><br>También se puede usar la [calculadora de costes](https://cloudpartners.transform.microsoft.com/download?assetname=assets%2FAzure_Sentinel_Calculator.xlsx&download=1) de Azure Sentinel, que incluye tablas útiles para calcular la superficie de orígenes de datos. <br><br>**Importante:** Estas estimaciones son un punto de partida; la configuración del nivel de detalle del registro y la carga de trabajo producirán variaciones. Se recomienda supervisar el sistema periódicamente para realizar un seguimiento de posibles cambios. Se recomienda una supervisión periódica en función de su escenario. <br><br>Para obtener más información, consulte [Gestión del uso y los costes con los registros de Azure Monitor](../azure-monitor/logs/manage-cost-storage.md).       |
|     |         |

## <a name="decision-tree"></a>Árbol de decisión

En la siguiente imagen se muestra un gráfico de flujo de árbol de decisión completo para ayudarle a conocer la forma óptima de diseñar su área de trabajo.

[ ![Árbol de decisión del diseño de áreas de trabajo de Azure Sentinel.](media/best-practices/workspace-decision-tree.png) ](media/best-practices/workspace-decision-tree.png#lightbox)

En las siguientes secciones se proporciona una versión de texto completo de este árbol de decisión, incluidas las notas tomadas de la imagen que figuran a continuación:

[Nota 1](#note1) | [Nota 2](#note2)  | [Nota 3](#note3)  | [Nota 4](#note4)  | [Nota 5](#note5)  | [Nota 6](#note6)  | [Nota 7](#note7)  | [Nota 8](#note8)  | [Nota 9](#note9) | [Nota 10](#note10)



### <a name="step-1-new-or-existing-workspace"></a>Paso 1: ¿Área de trabajo nueva o ya existente?

¿Tiene ya un área de trabajo que pueda usar para Azure Sentinel?

- **Si no es así, y va a crear una desde cero** de todos modos, vaya directamente al [paso 2](#step-2-keeping-data-in-different-azure-geographies).

- **Si ya tiene un área de trabajo** que podría usar, tenga presente la cantidad de datos que va a ingerir.

    - **Si va a ingerir *más* de 100 GB/día**, le recomendamos usar un área de trabajo aparte con el fin de ahorrar costes.

    - **Si va a ingerir *menos* de 100 GB/día**, continúe con el [paso 2](#step-2-keeping-data-in-different-azure-geographies) para realizar una evaluación adicional. Plantéese esta pregunta de nuevo cuando surja en el [paso 5](#step-5-collecting-any-non-soc-data).

### <a name="step-2-keeping-data-in-different-azure-geographies"></a>Paso 2: ¿Mantener los datos en diferentes zonas geográficas de Azure?

- **Si tiene requisitos normativos que le obliguen a mantener los datos en distintas zonas geográficas de Azure**, use un área de trabajo de Azure Sentinel independiente para cada región de Azure que tenga requisitos de cumplimiento. Para obtener más información, consulte [Consideraciones sobre las regiones](best-practices-workspace-architecture.md#region-considerations).

- **Si no tiene que mantener los datos en diferentes zonas geográficas de Azure**, continúe con el [paso 3](#step-3-do-you-have-multiple-azure-tenants).

### <a name="step-3-do-you-have-multiple-azure-tenants"></a>Paso 3: ¿Tiene varios inquilinos de Azure?

- **Si solo tiene un único inquilino**, vaya directamente al [paso 4](#step-4-splitting-billing--charge-back).

- **Si tiene varios inquilinos de Azure**, considere si va a recopilar registros específicos de un límite de inquilino, como Office 365 o Microsoft 365 Defender.

    - **Si no tiene registros específicos de inquilinos**, vaya directamente al [paso 4](#step-4-splitting-billing--charge-back).

    - **Si *va a* recopilar registros específicos de inquilinos**, use un área de trabajo de Azure Sentinel independiente para cada inquilino de Azure Active Directory. Continúe con el [paso 4](#step-4-splitting-billing--charge-back) para conocer otras consideraciones.

        <a name="note1"></a>[Nota 1 del árbol de decisión:](#decision-tree) Los registros específicos de límites de inquilino, como los de Office 365 y Microsoft Defender, solo se pueden almacenar en el área de trabajo dentro del mismo inquilino.

        Aunque es *posible* usar recopiladores personalizados para recopilar registros específicos de inquilinos de un área de trabajo de otro inquilino, no se recomienda hacerlo debido a las siguientes desventajas:

        - Los datos recopilados por conectores personalizados se ingieren en tablas personalizadas. Por lo tanto, no podrá usar todas las reglas y libros integrados.
        - Algunas de las características integradas, como UEBA y las reglas de aprendizaje automático, no tienen en cuenta las tablas personalizadas.
        - Los conectores personalizados requieren un coste y esfuerzo adicionales, como el uso de Azure Functions y Logic Apps.

        Si estas desventajas no suponen ningún problema para su organización, continúe con el [paso 4](#step-4-splitting-billing--charge-back) en lugar de usar áreas de trabajo de Azure Sentinel independientes.

### <a name="step-4-splitting-billing--charge-back"></a>Paso 4: ¿Dividir la facturación o el contracargo?

Si necesita dividir la facturación o el contracargo, considere si los informes de uso o los cargos cruzados manuales resultan adecuados en su caso.

- **Si *no* necesita dividir la facturación o el contracargo**, continúe con el [paso 5](#step-5-collecting-any-non-soc-data).

- **Si *necesita* dividir la facturación o el contracargo**, considere si los [informes de uso o los cargos cruzados manuales](azure-sentinel-billing.md) resultan adecuados en su caso.

    - **Si los informes de uso o los cargos cruzados manuales resultan adecuados en su caso**, continúe con el [paso 5](#step-5-collecting-any-non-soc-data).

    - **Si *ninguna* de las dos opciones resultan adecuadas en su caso**, use un área de trabajo de Azure Sentinel independiente para cada propietario de costes.

    <a name="note2"></a>[Nota 2 del árbol de decisión:](#decision-tree) Para obtener más información, consulte [Costes y facturación de Azure Sentinel](azure-sentinel-billing.md).

### <a name="step-5-collecting-any-non-soc-data"></a>Paso 5: ¿Recopilar datos que no sean SOC?

- **Si no va a recopilar datos que no sean SOC**, como datos operativos, puede ir directamente al [paso 6](#step-6-multiple-regions).

- **Si *va a recopilar* datos que no sean SOC**, analice si hay superposiciones, esto es, casos en los que se necesite el mismo origen para datos SOC y datos que no sean SOC.

    **Si *encuentra* superposiciones entre datos SOC y datos que no sean SOC**, trate los datos superpuestos exclusivamente como SOC. A continuación, considere si la ingesta de *ambos* tipos de datos por separado es inferior a 100 GB/día, pero superior a esta tasa cuando se combinan:

    - **Sí:** continúe con el [paso 6](#step-6-multiple-regions) para realizar una evaluación adicional.
    - **No:** con el fin de ahorrar costes, no se recomienda usar la misma área de trabajo. Vaya al [paso 6](#step-6-multiple-regions) para realizar una evaluación adicional.

    Para obtener más información en ambos casos, consulte la [nota 10](#note10).

    **Si *no* tiene datos superpuestos**, considere si la ingesta de *ambos* tipos de datos por separado es inferior a 100 GB/día, pero superior a esta tasa cuando se combinan:

    - **Sí:** continúe con el [paso 6](#step-6-multiple-regions) para realizar una evaluación adicional. Para obtener más información, consulte la [nota 3](#combining-your-soc-and-non-soc-data).
    - **No:** con el fin de ahorrar costes, no se recomienda usar la misma área de trabajo. Vaya al [paso 6](#step-6-multiple-regions) para realizar una evaluación adicional.

#### <a name="combining-your-soc-and-non-soc-data"></a>Combinación de los datos SOC y los datos que no son SOC

<a name="note3"></a>[Nota 3 del árbol de decisión:](#decision-tree) Aunque generalmente se recomienda que los clientes conserven un área de trabajo independiente para sus datos que no sean SOC, de modo que estos datos no acarreen costes de Azure Sentinel, puede haber situaciones en las que combinar datos SOC y datos que no sean SOC resulte menos costoso que separarlos.

Por ejemplo, considere una organización que tenga registros de seguridad que ingieran 50 GB/día, registros de operaciones que ingieran 50 GB/día y un área de trabajo en la región Este de EE. UU.

En la siguiente tabla se comparan opciones de área de trabajo con y sin áreas de trabajo independientes.

> [!NOTE]
> Los costes y términos que figuran en la siguiente tabla son falsos y solo se usan con fines ilustrativos. Para obtener información actualizada sobre los costes, use la calculadora de precios de Azure Sentinel.
>

|Arquitectura de áreas de trabajo  |Descripción |
|---------|---------|
|El equipo de SOC tiene su propia área de trabajo, con Azure Sentinel habilitado. <br><br>El equipo de operaciones tiene su propia área de trabajo, sin Azure Sentinel habilitado.     |  **Equipo de SOC:** <br>El coste de Azure Sentinel por 50 GB/día es de 6500 USD mensuales.<br>Los tres primeros meses de retención son gratuitos. <br><br>**Equipo de operaciones:**<br>- El coste de Log Analytics a 50 GB/día es de aproximadamente 3500 USD mensuales.<br>- Los primeros 31 días de retención son gratuitos.<br><br>El coste total de ambos asciende a 10 000 USD mensuales.       |
|Los equipos de SOC y operaciones comparten la misma área de trabajo con Azure Sentinel habilitado. |Combinando ambos registros, la ingesta será de 100 GB/día, lo que cumple los requisitos del nivel de compromiso (50 % para Sentinel y 15 % para Log Analytics).       <br><br>El coste de Azure Sentinel por 100 GB/día asciende a 9000 USD mensuales.      |
|     |         |

En este ejemplo, obtendría un ahorro de costes de 1000 USD mensuales combinando ambas áreas de trabajo; además, el equipo de operaciones también disfrutaría de tres meses gratuitos de retención en lugar de solo 31 días.

Este ejemplo solo es pertinente en aquellos casos en los que los datos SOC y los datos que no son SOC tienen por separado un volumen de ingesta de >=50 GB/día y <100 GB/día.

<a name="note10"></a>[Nota 10 del árbol de decisión:](#decision-tree) Se recomienda usar un área de trabajo independiente para los datos que no sean SOC, de modo que estos datos no acarreen costes de Azure Sentinel.

Sin embargo, esta recomendación de usar áreas de trabajo independientes con datos que no sean SOC parte de una premisa puramente basada en costes, y hay otros factores de diseño clave que se deben examinar al determinar si usar una o varias áreas de trabajo. Para evitar duplicar los costes de ingesta, considere la posibilidad de recopilar datos superpuestos en una sola área de trabajo únicamente con RBAC de Azure de nivel de tabla.

### <a name="step-6-multiple-regions"></a>Paso 6: ¿Varias regiones?

- **Si solo va a recopilar registros de VM de Azure en una *única* región**, vaya directamente al [paso 7](#step-7-segregating-data-or-defining-boundaries-by-ownership).

- **Si va a recopilar registros de VM de Azure en *varias* regiones**, ¿en qué medida le preocupa el coste de salida de datos?

    <a name="note4"></a>[Nota 4 del árbol de decisión:](#decision-tree) La salida de datos hace referencia al [coste de ancho de banda](https://azure.microsoft.com/pricing/details/bandwidth/) por mover datos fuera de centros de datos de Azure. Para obtener más información, consulte [Consideraciones sobre las regiones](best-practices-workspace-architecture.md#region-considerations).

    - Si es prioritario reducir la cantidad de esfuerzo necesario para mantener áreas de trabajo independientes, vaya al [paso 7](#step-7-segregating-data-or-defining-boundaries-by-ownership).

    - Si la importancia del coste de salida de datos hace que merezca la pena mantener áreas de trabajo independientes, use un área de trabajo de Azure Sentinel independiente para cada región en la que necesite reducir dicho coste.

        <a name="note5"></a>[Nota 5 del árbol de decisión:](#decision-tree) Se recomienda tener el menor número de áreas de trabajo posible. Use la [calculadora de precios de Azure](azure-sentinel-billing.md#estimate-azure-sentinel-costs) para calcular el coste y determinar qué regiones necesita realmente, y combine las áreas de trabajo de regiones con costes de salida bajos. Los costes de ancho de banda pueden representar solo una pequeña parte de la factura de Azure en comparación con los costes de ingesta de Azure Sentinel y Log Analytics.

        Por ejemplo, el coste se podría calcular de la siguiente manera:

        - 1000 máquinas virtuales, cada una de las cuales genera 1 GB/día
        - Envío de datos desde una región de EE. UU. a una región de la UE
        - Uso de una tasa de compresión de 2:1 en el agente

        El cálculo del coste estimado sería el siguiente: `1000 VMs * (1GB/day ÷ 2) * 30 days/month * $0.05/GB = $750/month bandwidth cost`

        Este coste de ejemplo sería mucho menor en comparación con los costes mensuales de un área de trabajo independiente de Azure Sentinel y Log Analytics.

        > [!NOTE]
        > Los costes que se muestran son falsos y solo se usan con fines ilustrativos. Para obtener información actualizada sobre los costes, use la calculadora de precios de Azure Sentinel.
        >

### <a name="step-7-segregating-data-or-defining-boundaries-by-ownership"></a>Paso 7: ¿Segregar datos o definir límites por propiedad?

- **Si *no* necesita segregar datos o definir límites de propiedad**, vaya directamente al [paso 8](#step-8-controlling-data-access-by-data-source--table).

- **Si *necesita* segregar datos o definir límites basados en la propiedad**, ¿necesita cada propietario de datos usar el portal de Azure Sentinel?

    - Si cada propietario de datos debe tener acceso al portal de Azure Sentinel, use un área de Azure Sentinel independiente para cada uno de ellos.

        <a name="note6"></a>[Nota 6 del árbol de decisión:](#decision-tree) Para acceder al portal de Azure Sentinel hace falta que cada usuario tenga al menos un rol [Lector de Azure Sentinel](../role-based-access-control/built-in-roles.md), con permisos de **Lector** en todas las tablas del área de trabajo. Si un usuario no tiene acceso a todas las tablas del área de trabajo, deberá usar Log Analytics para acceder a los registros en consultas de búsqueda.

    - Si el acceso a los registros a través de Log Analytics es suficiente en el caso de los propietarios que no tengan acceso al portal de Azure Sentinel, vaya al [paso 8](#step-8-controlling-data-access-by-data-source--table).

    Para obtener más información, vea [Permisos de Azure Sentinel](roles.md).

### <a name="step-8-controlling-data-access-by-data-source--table"></a>Paso 8: ¿Controlar el acceso a los datos por origen o tabla?

- **Si *no* necesita controlar el acceso a los datos por origen o tabla**, use una sola área de trabajo de Azure Sentinel.

- **Si *necesita* controlar el acceso por origen o tabla**, considere usar [RBAC de contexto de recursos](resource-context-rbac.md) en las siguientes situaciones:

    - Si necesita controlar el acceso en el nivel de fila, por ejemplo, para proporcionar varios propietarios en cada origen de datos o tabla

    - Si tiene múltiples tablas u orígenes de datos personalizados, donde cada uno necesita permisos independientes

    En otros casos, cuando *no* necesite controlar el acceso en el nivel de fila, proporcione múltiples tablas u orígenes de datos personalizados con permisos independientes y use una sola área de trabajo de Azure Sentinel, con RBAC de nivel de tabla para el control de acceso a datos.

#### <a name="considerations-for-resource-context-or-table-level-rbac"></a>Consideraciones sobre RBAC de nivel de tabla o contexto de recursos

Si piensa usar RBAC de nivel de tabla o contexto de recursos, tenga presente lo siguiente:

- <a name="note7"></a>[Nota 7 del árbol de decisión:](#decision-tree) Con el fin de configurar RBAC de contexto de recursos para recursos que no sean de Azure, puede que le interese asociar un identificador de recurso a los datos al enviarlos a Azure Sentinel, de modo que el ámbito del permiso se pueda establecer mediante RBAC de contexto de recursos. Para obtener más información, consulte [Configuración explícita de RBAC de contexto de recursos](resource-context-rbac.md#explicitly-configure-resource-context-rbac) y [Modos de acceso por implementación](../azure-monitor/logs/design-logs-deployment.md).

- <a name="note8"></a>[Nota 8 del árbol de decisión:](#decision-tree) Con los [permisos de recursos](../azure-monitor/logs/manage-access.md) o el [contexto de recursos](../azure-monitor/logs/design-logs-deployment.md) los usuarios pueden ver registros únicamente de los recursos a los que tengan acceso. El modo de acceso al área de trabajo debe establecerse en **Permisos de recurso de usuario o área de trabajo**. En los resultados de búsqueda de la página **Registros** de Azure Sentinel solo se incluirán tablas pertinentes para los recursos en los que el usuario tenga permisos.

- <a name="note9"></a>[Nota 9 del árbol de decisión:](#decision-tree) Con [RBAC de nivel de tabla](../azure-monitor/logs/manage-access.md) puede definir un control más pormenorizado de los datos de un área de trabajo de Log Analytics, así como los demás permisos. Este control le permite definir tipos de datos específicos que son accesibles solo a un conjunto concreto de usuarios. Para obtener más información, consulte [RBAC de nivel de tabla en Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/table-level-rbac-in-azure-sentinel/ba-p/965043).


## <a name="next-steps"></a>Pasos siguientes

Para obtener ejemplos prácticos de este árbol de decisión, consulte [Diseños de área de trabajo de ejemplo de Azure Sentinel](sample-workspace-designs.md).

Para más información, consulte:

- [Procedimientos recomendados de arquitectura de áreas de trabajo de Azure Sentinel](best-practices-workspace-architecture.md)
- [Procedimientos recomendados para Azure Sentinel](best-practices.md)