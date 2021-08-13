---
title: 'Administración de una integración de Elastic con Azure: Soluciones de asociados de Azure'
description: En este artículo se describe la administración de una instancia de Elastic en Azure Portal. Configuración de las opciones de diagnóstico y eliminación del recurso.
ms.service: partner-services
ms.topic: conceptual
ms.date: 05/20/2021
author: tfitzmac
ms.author: tomfitz
ms.openlocfilehash: 9feb4c5eb143ced9079bfe9e63b451aecd437212
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/10/2021
ms.locfileid: "111952581"
---
# <a name="manage-the-elastic-integration-with-azure"></a>Administración de la integración de Elastic con Azure

En este artículo se describe cómo administrar la integración de Elastic con Azure. Muestra cómo configurar las opciones de diagnóstico y eliminar el recurso de Elastic.

## <a name="reconfigure-rules-for-metrics-and-logs"></a>Volver a configurar las reglas para métricas y registros

Cuando creó el recurso, configuró qué registros se envían a Elastic. Si necesita cambiar esa configuración, seleccione **Métricas y registros** en el panel izquierdo. Realice los cambios necesarios en la forma de enviar los registros a Elastic.

Para más información sobre los dos tipos de registros, consulte [Inicio rápido: Introducción a Elastic](create.md).

:::image type="content" source="media/manage/reconfigure-logs.png" alt-text="Cambiar configuración de registro":::

## <a name="view-monitored-resources"></a>Vista de los recursos supervisados

Para ver una lista de recursos que envían registros a Elastic, seleccione **Recursos supervisados** en el panel izquierdo.

:::image type="content" source="media/manage/monitored-resources.png" alt-text="Vista de los recursos supervisados":::

Puede filtrar la lista por tipo de recurso, nombre de grupo de recursos, ubicación y en función de si el recurso envía registros o no.

La columna **Logs to Elastic** (Registros para Elastic) indica si el recurso envía registros a Elastic. Si el recurso no envía registros, este campo especifica el motivo. Los motivos pueden ser:

* El recurso no admite el envío de registros. Solo los registros de recursos de Azure para todos los tipos de recursos y categorías de registro definidos aquí se pueden configurar para enviar registros a Elastic.
* Se ha alcanzado el límite de cinco configuraciones de diagnóstico. Cada recurso de Azure puede tener un máximo de cinco [configuraciones de diagnóstico](../../azure-monitor/essentials/diagnostic-settings.md).
* Un error impide que los registros se envíen a Elastic.
* Los registros no están configurados para el recurso. Solo aquellos recursos con las etiquetas adecuadas se envían a Elastic. Ha especificado las reglas de etiqueta en la configuración del registro. 
* No se admite la región. El recurso de Azure está en una región que actualmente no admite el envío de registros a Elastic. 

## <a name="monitor-virtual-machines-using-elastic-agent"></a>Supervisión de máquinas virtuales mediante el agente de Elastic

Puede instalar agentes de Elastic en las máquinas virtuales como una extensión. Para ver las máquinas virtuales disponibles en su suscripción, seleccione **Virtual Machines** en el panel izquierdo del recurso de Elastic.

:::image type="content" source="media/manage/vm-agents.png" alt-text="Visualización de máquinas virtuales":::

Para cada máquina virtual, se muestran los datos siguientes:

* Nombre de recurso: nombre de la máquina virtual.
* Estado del recurso: indica si la máquina virtual está detenida o en ejecución. El agente de Elastic solo se puede instalar en máquinas virtuales que estén en ejecución. Si la máquina virtual está detenida, se deshabilitará la instalación del agente de Elastic.
* Versión del agente: número de versión del agente de Elastic.
* Estado del agente: indica si el agente de Elastic está en ejecución en la máquina virtual.
* Integraciones habilitadas: métricas principales que recopila el agente de Elastic.
* Envío de registros: indica si el agente de Elastic envía registros a Elastic.

Para instalar el agente de Elastic, seleccione una máquina virtual y seleccione **Instalar extensión**.

El portal le pide confirmación de que desea instalar el agente con la autenticación predeterminada. Seleccione **OK** (Aceptar) para comenzar la instalación. El portal muestra el estado **Installing** (Instalando) hasta que el agente se instala y aprovisiona.

Una vez instalado el agente de Elastic, el estado cambia a **Installed** (Instalado).

Para comprobar que se ha instalado el agente de Elastic, seleccione la máquina virtual y vaya a **Extensiones**.

Para desinstalar el agente de Elastic en una máquina virtual, seleccione la máquina virtual y seleccione **Desinstalar extensión**.

## <a name="configure-diagnostic-settings"></a>Configuración de valores de diagnóstico

Para configurar las opciones de diagnóstico de un recurso, seleccione ese recurso. En el panel izquierdo, seleccione **Configuración de diagnóstico**. 

En la sección de detalles de destino, active la opción para enviar a soluciones de asociados y seleccione Elastic como destino. Esta opción solo está disponible después de crear un recurso de Elastic.

:::image type="content" source="media/manage/diagnostic-settings.png" alt-text="Configuración de valores de diagnóstico":::

## <a name="delete-elastic-resource"></a>Eliminación de un recurso de Elastic

Cuando ya no necesite el recurso de Elastic, elimínelo de Azure Portal.

Para eliminar el recurso en Azure, seleccione el recurso de Elastic. En **Información general**, seleccione **Eliminar**. Confirme que desea eliminar el recurso de Elastic.

:::image type="content" source="media/manage/delete-elastic.png" alt-text="Eliminación de un recurso de Elastic":::

Cuando se elimine el recurso, los registros ya no se enviarán a Elastic. Se detiene toda la facturación de Elastic mediante Azure Marketplace.

## <a name="next-steps"></a>Pasos siguientes

Para obtener ayuda con la solución de problemas, consulte [Solución de problemas de la integración de Elastic con Azure](troubleshoot.md).