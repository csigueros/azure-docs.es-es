---
title: Procedimiento de configuración de Microsoft Defender para SQL
description: Obtener información sobre cómo habilitar el plan opcional de Microsoft Defender para SQL de Microsoft Defender for Cloud
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: how-to
ms.date: 02/11/2021
ms.author: memildin
ms.custom: ignite-fall-2021
ms.openlocfilehash: 9a7e817df5e938756ca9fe331f82136505515451
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131009745"
---
# <a name="enable-microsoft-defender-for-sql-servers-on-machines"></a>Habilitación de Microsoft Defender para servidores SQL en máquinas 

[!INCLUDE [Banner for top of topics](./includes/banner.md)]

Este plan de Microsoft Defender detecta actividades anómalas que indican intentos inusuales y potencialmente peligrosos de obtener acceso a las bases de datos o de vulnerar su seguridad.

Verá alertas cuando haya actividades sospechosas en las bases de datos, posibles vulnerabilidades o ataques por inyección de código SQL, y patrones anómalos de acceso y consulta a las bases de datos.

## <a name="availability"></a>Disponibilidad

|Aspecto|Detalles|
|----|:----|
|Estado de la versión:|Disponibilidad general (GA)|
|Precios:|**Microsoft Defender for Cloud para servidores SQL en máquinas** se factura como se indica en la [página de precios](https://azure.microsoft.com/pricing/details/security-center/)|
|Versiones de SQL protegidas:|Azure SQL Server (todas las versiones cubiertas con el soporte técnico de Microsoft)|
|Nubes:|:::image type="icon" source="./media/icons/yes-icon.png"::: Nubes comerciales<br>:::image type="icon" source="./media/icons/yes-icon.png"::: Azure Government<br>:::image type="icon" source="./media/icons/no-icon.png"::: Azure China 21Vianet|
|||

## <a name="set-up-microsoft-defender-for-sql-servers-on-machines"></a>Configuración de Microsoft Defender para servidores SQL en máquinas

Para habilitar este plan:

[Paso 1. Instale la extensión del agente](#step-1-install-the-agent-extension)

[Paso 2. Aprovisione el agente de Log Analytics en el host de SQL Server:](#step-2-provision-the-log-analytics-agent-on-your-sql-servers-host)

[Paso 3. Habilite el plan opcional en la página de precios y configuración de Defender for Cloud:](#step-3-enable-the-optional-plan-in-defender-for-clouds-pricing-and-settings-page)


### <a name="step-1-install-the-agent-extension"></a>Paso 1. Instale la extensión del agente

- **SQL Server en Azure VM**: registre VM con SQL Server con la extensión Agente de IaaS de SQL como se explica en [Registro de VM con SQL Server con la extensión Agente de IaaS de SQL](../azure-sql/virtual-machines/windows/sql-agent-extension-manually-register-single-vm.md).

- **SQL Server en Azure Arc:** instale el agente Azure Arc siguiendo los métodos de instalación descritos en la [documentación de Azure Arc](../azure-arc/servers/manage-vm-extensions.md).

### <a name="step-2-provision-the-log-analytics-agent-on-your-sql-servers-host"></a>Paso 2. Aprovisione el agente de Log Analytics en el host de SQL Server:

- **SQL Server en Azure VM**: si la máquina SQL se hospeda en una máquina virtual de Azure, puede [habilitar el aprovisionamiento automático del agente de Log Analytics <a name="auto-provision-mma"></a>](enable-data-collection.md#auto-provision-mma). Como alternativa, puede seguir el procedimiento manual para [incorporar máquinas virtuales a la instancia de Azure Stack Hub](quickstart-onboard-machines.md?pivots=azure-portal#onboard-your-azure-stack-hub-vms).
- **SQL Server en Azure Arc**: si el servidor SQL Server está administrado por servidores habilitados para [Azure Arc](../azure-arc/index.yml), puede implementar el agente de Log Analytics con la recomendación "El agente de Log Analytics debe estar instalado en las máquinas basadas en Windows de Azure Arc (versión preliminar)" de Defender for Cloud.

- **SQL Server local**: si el servidor SQL Server se hospeda en una máquina local de Windows sin Azure Arc, tiene dos opciones para conectarlo a Azure:
    
    - **Implementación de Azure Arc**: puede conectar cualquier máquina de Windows a Defender for Cloud. Sin embargo, Azure Arc proporciona una integración más profunda entre *todas* las de su entorno de Azure. Si configura Azure Arc, verá la página **SQL Server: Azure Arc** en el portal y las alertas de seguridad aparecerán en una pestaña **Seguridad** dedicada en esa página. Por lo tanto, la primera opción (y la recomendada) es [configurar Azure Arc en el host](../azure-arc/servers/onboard-portal.md#install-and-validate-the-agent-on-windows) y seguir las instrucciones anteriores para **SQL Server en Azure Arc**.
        
    - **Conexión de la máquina Windows sin Azure Arc**: si opta por conectar un servidor SQL Server que se ejecute en una máquina Windows sin usar Azure Arc, siga las instrucciones del artículo sobre cómo [conectar máquinas Windows a Azure Monitor](../azure-monitor/agents/agent-windows.md).


### <a name="step-3-enable-the-optional-plan-in-defender-for-clouds-pricing-and-settings-page"></a>Paso 3. Habilite el plan opcional en la página de precios y configuración de Defender for Cloud:

1. En el menú de Defender for Cloud, abra la página **Parámetros del entorno**.

    - Si usa el **área de trabajo predeterminada de Microsoft Defender for Cloud** (denominada "defaultworkspace-[su identificador de suscripción]-[región]"), seleccione la **suscripción** correspondiente.

    - Si utiliza **un área de trabajo no predeterminada**, seleccione el **área de trabajo** correspondiente (escriba el nombre del área de trabajo en el filtro si es necesario):

        ![Búsqueda por título en el área de trabajo no predeterminada.](./media/security-center-advanced-iaas-data/pricing-and-settings-workspaces.png)

1. Establezca la opción del plan **Microsoft Defender for Cloud para servidores SQL en máquinas** en **Activada**. 

    :::image type="content" source="./media/security-center-advanced-iaas-data/sql-servers-on-vms-in-pricing-small.png" alt-text="Página de precios de Defender for Cloud con planes opcionales.":::

    El plan se habilitará en todos los servidores SQL conectados al área de trabajo seleccionada. La protección de datos estará totalmente activa después del primer reinicio de la instancia de SQL Server.

    >[!TIP] 
    > Para crear una nueva área de trabajo, siga las instrucciones de [Crear un área de trabajo de Log Analytics](../azure-monitor/logs/quick-create-workspace.md).


1. Opcionalmente, configure notificaciones de alertas de seguridad por correo electrónico. 

    Puede establecer una lista de destinatarios que reciban una notificación por correo electrónico cuando se generen alertas de Defender for Cloud. El correo electrónico contiene un vínculo directo a la alerta en Microsoft Defender for Cloud con todos los detalles pertinentes. Para más información, consulte el artículo sobre cómo [configurar las notificaciones por correo electrónico para recibir alertas](configure-email-notifications.md).


## <a name="microsoft-defender-for-sql-alerts"></a>Alertas de Microsoft Defender para SQL
Las alertas se generan a partir de intentos inusuales y potencialmente dañinos para acceder a las máquinas SQL o aprovechar sus vulnerabilidades. Estos eventos pueden desencadenar alertas que se muestran en la [página de referencia de alertas](alerts-reference.md#alerts-sql-db-and-warehouse).

## <a name="explore-and-investigate-security-alerts"></a>Exploración e investigación de las alertas de seguridad

Las alertas de Microsoft Defender para SQL están disponibles en la página de alertas de Defender for Cloud, en la página de seguridad de la máquina, el [panel de protecciones de carga de trabajo](workload-protections-dashboard.md) o a través del vínculo directo de los correos electrónicos de alerta.

1. Para ver las alertas, seleccione **Alertas de seguridad** en el menú de Defender for Cloud y seleccione una alerta.

1. Las alertas están diseñadas para ser independientes, con pasos de corrección detallados e información de investigación en cada una. Puede investigar adicionalmente mediante otras funcionalidades de Microsoft Defender for Cloud y Microsoft Sentinel para obtener una vista más amplia:

    * Habilite la característica de auditoría de SQL Server para realizar más investigaciones. Si es usuario de Microsoft Sentinel, puede cargar los registros de auditoría de SQL desde los eventos del registro de seguridad de Windows a Sentinel y disfrutar de una experiencia de investigación enriquecida. [Obtenga más información sobre cómo realizar auditorías de SQL Server](/sql/relational-databases/security/auditing/create-a-server-audit-and-server-audit-specification?preserve-view=true&view=sql-server-ver15).
    * Para mejorar la posición de seguridad, siga las recomendaciones de Defender for Cloud para la máquina host indicadas en cada alerta. Esto reducirá los riesgos de futuros ataques. 

    [Obtenga más información sobre cómo administrar y responder a alertas](managing-and-responding-alerts.md).


## <a name="faq---microsoft-defender-for-sql-servers-on-machines"></a>Preguntas más frecuentes: Microsoft Defender para servidores SQL en máquinas

### <a name="if-i-enable-this-microsoft-defender-plan-on-my-subscription-are-all-sql-servers-on-the-subscription-protected"></a>Si habilito este plan de Microsoft Defender en mi suscripción, ¿están protegidos todos los servidores SQL Server de la suscripción? 

No. Para defender una implementación de SQL Server en una máquina virtual de Azure o en una instancia de SQL Server que se ejecuta en una máquina habilitada para Azure Arc, Defender for Cloud requiere lo siguiente:

- Un agente de Log Analytics en la máquina
- El área de trabajo de Log Analytics pertinente para tener habilitada la solución de Microsoft Defender para SQL

El *estado* de la suscripción, que se muestra en la página del servidor SQL Server de Azure Portal, refleja el estado predeterminado del área de trabajo y se aplica a todas las máquinas conectadas. Solo los servidores SQL Server en hosts con un agente de Log Analytics que informa a esa área de trabajo están protegidos por Defender for Cloud.




## <a name="next-steps"></a>Pasos siguientes

Para obtener material relacionado, vea el siguiente artículo:

- [Alertas de seguridad para SQL Database y Azure Synapse Analytics](alerts-reference.md#alerts-sql-db-and-warehouse)
- [Configuración de notificaciones de alertas de seguridad por correo electrónico](configure-email-notifications.md)
- [Más información sobre Microsoft Sentinel](../sentinel/index.yml)
