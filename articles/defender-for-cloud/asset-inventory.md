---
title: Inventario de recursos de Microsoft Defender for Cloud
description: Obtenga información sobre la experiencia de administración de recursos de Microsoft Defender for Cloud que proporciona visibilidad completa sobre todos los recursos supervisados de Defender for Cloud.
author: memildin
manager: rkarlin
services: security-center
ms.author: memildin
ms.date: 11/09/2021
ms.service: defender-for-cloud
ms.topic: how-to
ms.openlocfilehash: b8732094b7b575faf97753b8e0e8a5c234f733d2
ms.sourcegitcommit: 2ed2d9d6227cf5e7ba9ecf52bf518dff63457a59
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/16/2021
ms.locfileid: "132525777"
---
# <a name="use-asset-inventory-to-manage-your-resources-security-posture"></a>Uso del inventario de recursos para administrar la posición de seguridad de los recursos

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

La página de inventario de recursos de Microsoft Defender for Cloud proporciona una sola página para ver la posición de seguridad de los recursos que ha conectado a Microsoft Defender for Cloud.

Defender for Cloud analiza periódicamente el estado de seguridad de los recursos conectados a las suscripciones para identificar posibles vulnerabilidades de seguridad. A continuación, se proporcionan recomendaciones sobre cómo corregir dichos puntos vulnerables.

Cuando algún recurso tenga recomendaciones pendientes, aparecerán en el inventario.

Use esta vista y sus filtros para abordar preguntas como:

- ¿Cuál de mis suscripciones con características de seguridad mejoradas habilitadas tiene recomendaciones pendientes?
- ¿A cuál de mis máquinas con la etiqueta "producción" le falta el agente de Log Analytics?
- ¿Cuántas máquinas, etiquetadas con una etiqueta específica, tienen recomendaciones pendientes?
- ¿Qué máquinas de un grupo de recursos concreto tienen una vulnerabilidad conocida (con un número CVE)?

Las posibilidades de administración de recursos de esta herramienta son sustanciales y continúan creciendo.

> [!TIP]
> Las recomendaciones de seguridad de la página del inventario de recursos son las mismas que las de la página **Recomendaciones**, pero aquí se muestran según el recurso afectado. Para obtener más información sobre cómo resolver las recomendaciones, consulte [Implementación de recomendaciones de seguridad en Microsoft Defender for Cloud](review-security-recommendations.md).

## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|Gratis *<br>* Algunas características de la página de inventario, como el [inventario de software](#access-a-software-inventory), requieren la instalación de soluciones de pago.|
|Roles y permisos necesarios:|Todos los usuarios|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Nacionales (Azure Government, Azure China 21Vianet)|
|||

## <a name="what-are-the-key-features-of-asset-inventory"></a>¿Cuáles son las características clave del inventario de recursos?

La página de inventario proporciona las siguientes herramientas:

:::image type="content" source="media/asset-inventory/highlights-of-inventory.png" alt-text="Características principales de la página de inventario de recursos de Microsoft Defender for Cloud." lightbox="media/asset-inventory/highlights-of-inventory.png":::

### <a name="1---summaries"></a>1: Resúmenes

Antes de definir ningún filtro, una franja destacada de valores en la parte superior de la vista de inventario muestra lo siguiente:

- **Recursos totales:** el número total de recursos conectados a Defender for Cloud.
- **Recursos con estado incorrecto** : recursos con recomendaciones de seguridad activas. [Más información sobre las recomendaciones de seguridad](review-security-recommendations.md).
- **Unmonitored resources** (Recursos no supervisados): recursos con problemas de supervisión del agente; tienen implementado el agente de Log Analytics, pero no envía datos o tiene otros problemas de estado.
- **Suscripciones no registradas:** cualquier suscripción del ámbito seleccionado que todavía no se ha conectado a Microsoft Defender for Cloud.

### <a name="2---filters"></a>2: Filtros

Los distintos filtros de la parte superior de la página proporcionan una manera de refinar rápidamente la lista de recursos según la pregunta que intenta responder. Por ejemplo, si quisiera responder a la pregunta *¿A cuáles de mis máquinas con la etiqueta "Producción" les falta el agente de Log Analytics?* , podría combinar el filtro **Supervisión del agente** con el filtro **Etiquetas**.

En cuanto haya aplicado filtros, los valores de resumen se actualizarán para relacionarlos con los resultados de la consulta.

### <a name="3---export-and-asset-management-tools"></a>3: Herramientas de administración de recursos y exportación

**Opciones de exportación**: el inventario incluye una opción para exportar los resultados de las opciones de filtro seleccionadas a un archivo CSV. También puede exportar la consulta propiamente dicha a Azure Resource Graph Explorer para refinar aún más, guardar o modificar la consulta del lenguaje de consulta Kusto (KQL).

> [!TIP]
> La documentación de KQL proporciona una base de datos con algunos datos de ejemplo junto con algunas consultas sencillas para saber cómo funciona el lenguaje. [Más información en este tutorial de KQL](/azure/data-explorer/kusto/query/tutorial?pivots=azuredataexplorer).

**Opciones de administración de recursos**: cuando se encuentran los recursos que coinciden con las consultas, el inventario proporciona accesos directos a operaciones como:

- Asignación de etiquetas a los recursos filtrados: active las casillas junto a los recursos que quiere etiquetar.
- Incorpore nuevos servidores a Defender for Cloud: use el botón de la barra de herramientas **Agregar servidores que no son de Azure**.
- Automatización de cargas de trabajo con Azure Logic Apps: use el botón **Desencadenar aplicación lógica** para ejecutar una aplicación lógica en uno o varios recursos. Las aplicaciones lógicas deben prepararse de antemano y aceptar el tipo de desencadenador correspondiente (solicitud HTTP). [Más información sobre las aplicaciones lógicas](../logic-apps/logic-apps-overview.md)

## <a name="how-does-asset-inventory-work"></a>¿Cómo funciona el inventario de recursos?

El inventario de recursos emplea [Azure Resource Graph (ARG)](../governance/resource-graph/index.yml), un servicio de Azure que proporciona la capacidad de consultar los datos de la posición de seguridad de Defender for Cloud en varias suscripciones.

ARG está diseñado para proporcionar una exploración de recursos eficaz con la posibilidad de realizar consultas a escala.

Con el [lenguaje de consulta de Kusto (KQL)](/azure/data-explorer/kusto/query/), el inventario de recursos puede generar rápidamente información detallada gracias a la referencia cruzada de los datos de Defender for Cloud con otras propiedades de recursos.

## <a name="how-to-use-asset-inventory"></a>Uso del inventario de recursos

1. En la barra lateral de Defender for Cloud, seleccione **Inventario**.

1. Use el cuadro **Filtrar por nombre** para mostrar un recurso específico o use los filtros como se describe a continuación.

1. Seleccione las opciones correspondientes de los filtros para crear la consulta específica que quiere realizar.

    De forma predeterminada, los recursos se ordenan por el número de recomendaciones de seguridad activas.

    > [!IMPORTANT]
    > Las opciones de cada filtro son específicas de los recursos de las suscripciones seleccionadas actualmente **y** de las selecciones de los otros filtros.
    >
    > Por ejemplo, si ha seleccionado solo una suscripción y esta no tiene ningún recurso con recomendaciones de seguridad pendientes de corregir (0 recursos incorrectos), el filtro **Recomendaciones** no tendrán ninguna opción.

    :::image type="content" source="./media/asset-inventory/filtering-to-prod-unmonitored.gif" alt-text="Uso de las opciones de filtro en el inventario de recursos de Microsoft Defender for Cloud para filtrar recursos para los recursos de producción no supervisados":::

1. Para usar el filtro **Security findings contain** (Las conclusiones de seguridad contienen), escriba texto libre para el identificador, la comprobación de seguridad o el nombre de CVE de una conclusión de vulnerabilidad para filtrar los recursos afectados:

    ![Filtro "Security findings contain" (Las conclusiones de seguridad contienen)](./media/asset-inventory/security-findings-contain-elements.png)

    > [!TIP]
    > Los filtros **Security findings contain** (Las conclusiones de seguridad contienen) y **Etiquetas** solo aceptan un único valor. Para filtrar por más de uno, use **Agregar filtros**.

1. Para usar el filtro **Defender for Cloud**, seleccione una o varias opciones (Desactivado, Activado o Parcial):

    - **Desactivado:** recursos que no están protegidos por un plan de Microsoft Defender. Puede hacer clic con el botón derecho en cualquiera de ellos y actualizarlos:

        :::image type="content" source="./media/asset-inventory/upgrade-resource-inventory.png" alt-text="Actualización de un recurso para que esté protegido por el plan de Microsoft Defender correspondiente mediante un clic con el botón derecho." lightbox="./media/asset-inventory/upgrade-resource-inventory.png":::

    - **Activado:** recursos que están protegidos por un plan de Microsoft Defender.
    - **Parcial:** esto se aplica a **suscripciones** que tienen algunos planes de Microsoft Defender deshabilitados, pero no todos. Por ejemplo, la siguiente suscripción tiene siete planes de Microsoft Defender deshabilitados.

        :::image type="content" source="./media/asset-inventory/pricing-tier-partial.png" alt-text="Suscripción parcialmente protegida por planes de Microsoft Defender.":::

1. Para examinar más detalladamente los resultados de la consulta, seleccione los recursos que le interesen.

1. Para ver las opciones de filtro seleccionadas actualmente en forma de consulta en Resource Graph Explorer, seleccione **Abrir consulta**.

    ![Consulta de inventario en ARG.](./media/asset-inventory/inventory-query-in-resource-graph-explorer.png)

1. Si ha definido algunos filtros y ha dejado la página abierta, Defender for Cloud no actualizará los resultados de forma automática. Cualquier cambio en los recursos no afectará a los resultados mostrados a menos que se vuelva a cargar manualmente la página o se seleccione **Actualizar**.

## <a name="access-a-software-inventory"></a>Acceso a un inventario de software

Si ha habilitado la integración con Microsoft Defender para punto de conexión y ha habilitado Microsoft Defender para servidores, tendrá acceso al inventario de software.

:::image type="content" source="media/asset-inventory/software-inventory-filters.gif" alt-text="Si ha habilitado la solución de amenazas y vulnerabilidades, el inventario de recursos de Defender for Cloud ofrece un filtro para seleccionar los recursos por su software instalado.":::

> [!NOTE]
> La opción "En blanco" muestra las máquinas sin Microsoft Defender para punto de conexión (o sin Microsoft Defender para servidores).

Además de los filtros de la página de inventario de recursos, puede explorar los datos de inventario de software de Azure Resource Graph Explorer.

Ejemplos de uso de Azure Resource Graph Explorer para acceder a los datos de inventario de software y explorarlos:

1. Abra **Azure Resource Graph Explorer**.

    :::image type="content" source="./media/multi-factor-authentication-enforcement/opening-resource-graph-explorer.png" alt-text="Inicio de la página de recomendaciones de Azure Resource Graph Explorer**" :::

1. Seleccione el siguiente ámbito de suscripción: securityresources/softwareinventories

1. Escriba cualquiera de las siguientes consultas (o personalícelas o escriba las suyas propias) y seleccione **Ejecutar consulta**.

    - Para generar una lista básica del software instalado:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=properties.version
        ```

    - Para filtrar por los números de versión:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | project id, Vendor=properties.vendor, Software=properties.softwareName, Version=tostring(properties.    version)
        | where Software=="windows_server_2019" and parse_version(Version)<=parse_version("10.0.17763.1999")
        ```

    - Para buscar máquinas con una combinación de productos de software:

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = properties.azureVmId
        | where properties.softwareName == "apache_http_server" or properties.softwareName == "mysql"
        | summarize count() by tostring(vmId)
        | where count_ > 1
        ```

    - Combinación de un producto de software con otra recomendación de seguridad:

        (En este ejemplo: máquinas con MySQL instalado y puertos de administración expuestos)

        ```kusto
        securityresources
        | where type == "microsoft.security/softwareinventories"
        | extend vmId = tolower(properties.azureVmId)
        | where properties.softwareName == "mysql"
        | join (
        securityresources
        | where type == "microsoft.security/assessments"
        | where properties.displayName == "Management ports should be closed on your virtual machines" and properties.status.code == "Unhealthy"
        | extend vmId = tolower(properties.resourceDetails.Id)
        ) on vmId
        ```

## <a name="faq---inventory"></a>Preguntas frecuentes sobre el inventario

### <a name="why-arent-all-of-my-subscriptions-machines-storage-accounts-etc-shown"></a>¿Por qué no se muestran todas las suscripciones, máquinas, cuentas de almacenamiento, etc.?

En la vista de inventario se muestran los recursos conectados de Defender for Cloud desde la perspectiva de la administración de la posición de seguridad en la nube (CSPM). Los filtros no devuelven todos los recursos del entorno; solo los que tengan recomendaciones pendientes (o "activas").

Por ejemplo, en la captura de pantalla siguiente se muestra un usuario con acceso a 8 suscripciones, pero solo 7 tienen recomendaciones actualmente. Por tanto, cuando se filtran por **Tipo de recurso = Suscripciones**, solo las 7 suscripciones con recomendaciones activas aparecen en el inventario:

:::image type="content" source="./media/asset-inventory/filtered-subscriptions-some.png" alt-text="No todas las subscripciones se devuelven cuando no hay recomendaciones activas" lightbox="./media/asset-inventory/filtered-subscriptions-some.png":::.

### <a name="why-do-some-of-my-resources-show-blank-values-in-the-defender-for-cloud-or-monitoring-agent-columns"></a>¿Por qué algunos de mis recursos muestran valores en blanco en las columnas Defender for Cloud o Agente de supervisión?

No todos los recursos supervisados de Defender for Cloud tienen agentes. Por ejemplo, las cuentas de Azure Storage o los recursos de PaaS como discos, Logic Apps, Data Lake Analysis y el Centro de eventos no necesitan que Defender for Cloud supervise los agentes.

Cuando el precio o la supervisión del agente no son pertinentes para un recurso, no se mostrará nada en esas columnas de inventario.

:::image type="content" source="./media/asset-inventory/agent-pricing-blanks.png" alt-text="Algunos recursos muestran información en blanco en las columnas Agente de supervisión o Defender for Cloud." lightbox="./media/asset-inventory/agent-pricing-blanks.png":::

## <a name="next-steps"></a>Pasos siguientes

En este artículo se ha descrito la página de inventario de recursos de Microsoft Defender for Cloud.

Para más información sobre las herramientas relacionadas, consulte las siguientes páginas:

- [Azure Resource Graph (AGR)](../governance/resource-graph/index.yml)
- [Lenguaje de consulta de Kusto (KQL)](/azure/data-explorer/kusto/query/)
