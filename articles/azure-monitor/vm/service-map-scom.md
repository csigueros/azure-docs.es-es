---
title: Integración de la característica de asignación de VM Insights con Operations Manager | Microsoft Docs
description: VM Insights detecta automáticamente los componentes de la aplicación en sistemas Windows y Linux, y asigna la comunicación entre servicios. En este artículo se aborda el uso de la característica Asignar para crear automáticamente diagramas de aplicaciones distribuidas en Operations Manager.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 07/12/2019
ms.openlocfilehash: eacbf24fdd4d92ac4da1419c02be7ae006b640d4
ms.sourcegitcommit: 702df701fff4ec6cc39134aa607d023c766adec3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/03/2021
ms.locfileid: "131432251"
---
# <a name="integrate-system-center-operations-manager-with-vm-insights-map-feature"></a>Integración de System Center Operations Manager con la característica de asignación de VM Insights

En VM Insights, puede ver los componentes de la aplicación detectados en las máquinas virtuales (VM) de Windows y Linux que se ejecutan en Azure o en su entorno. Con esta integración entre la característica de asignación y System Center Operations Manager, puede crear automáticamente diagramas de aplicaciones distribuidas en Operations Manager basados en las asignaciones de dependencias dinámicas de VM Insights. En este artículo se describe cómo configurar el grupo de administración de System Center Operations Manager para que admita esta característica.

>[!NOTE]
>Si ya ha implementado Service Map, puede ver las asignaciones en VM Insights, lo que incluye características adicionales para supervisar el rendimiento y el estado de la VM. La característica de asignación de VM Insights está pensada para reemplazar la solución Service Map independiente. Para más información, consulte [Información general sobre VM Insights](../vm/vminsights-overview.md).

## <a name="prerequisites"></a>Prerrequisitos

* Un grupo de administración de System Center Operations Manager (2012 R2 o posterior).
* Un área de trabajo de Log Analytics configurada para admitir VM Insights.
* Una o varias máquinas virtuales Windows y Linux o equipos físicos supervisados por Operations Manager que envíen datos al área de trabajo de Log Analytics. Los servidores Linux que informen a un grupo de administración de Operations Manager deben estar configurados para conectarse directamente a Azure Monitor. Para obtener más información, consulte la introducción del artículo [Recopilación de datos de registro con el agente de Log Analytics](../agents/log-analytics-agent.md).
* Una entidad de servicio con acceso a la suscripción de Azure asociada al área de trabajo de Log Analytics. Para más información, vaya a [Create a service principal](#create-a-service-principal) (Creación de una entidad de servicio).

## <a name="install-the-service-map-management-pack"></a>Instalación del módulo de administración de Mapa de servicio

La integración entre Operations Manager y la característica Asignar se habilita al importar el paquete de módulos de administración de Microsoft.SystemCenter.ServiceMap (Microsoft.SystemCenter.ServiceMap.mpb). Puede descargar el paquete de módulo de administración en el [Centro de descarga de Microsoft](https://www.microsoft.com/download/details.aspx?id=55763). El paquete contiene los siguientes módulos de administración:

* Microsoft Service Map Application Views
* Microsoft System Center Service Map Internal
* Microsoft System Center Service Map Overrides
* Microsoft System Center Service Map

## <a name="configure-integration"></a>Configuración de la integración

Después de instalar el módulo de administración de Service Map, habrá un nuevo nodo, **Service Map**, en **Operations Management Suite**, en el panel **Administración** de la consola Operaciones de Operations Manager.

>[!NOTE]
>[Operations Management Suite era una colección de servicios](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) que incluía Log Analytics y ahora forma parte de [Azure Monitor](../overview.md).

Para configurar la integración de la característica de asignación de VM Insights, haga lo siguiente:

1. Para abrir el asistente para configuración, en el panel de **información general de Mapa de servicio**, haga clic en **Agregar área de trabajo**.  

    ![Panel de información general de Mapa de servicio](media/service-map-scom/scom-configuration.png)

2. En la ventana **Configuración de la conexión**, escriba el identificador o el nombre del inquilino, el identificador de la aplicación (también conocido como nombre de usuario o identificador de cliente) y la contraseña de la entidad de servicio; a continuación, haga clic en **Siguiente**. Para más información, vaya a Creación de una entidad de servicio.

    ![Ventana de configuración de la conexión](media/service-map-scom/scom-config-spn.png)

3. En la ventana **Selección de suscripción**, seleccione la suscripción de Azure, el grupo de recursos de Azure (el que contiene el área de trabajo de Log Analytics) y el área de trabajo de Log Analytics; a continuación, haga clic en **Siguiente**.

    ![Área de trabajo de configuración de Operations Manager](media/service-map-scom/scom-config-workspace.png)

4. En la ventana **Selección de Grupo de máquinas**, elija los grupos de máquinas de Service Map que desea sincronizar con Operations Manager. Haga clic en **Agregar o quitar Grupos de máquinas**, seleccione los grupos de la lista de **Grupos de máquinas disponibles** y haga clic en **Agregar**.  Cuando haya terminado la selección de grupos, haga clic en **Aceptar** para finalizar.

    ![Configuración de grupos de máquinas de Operations Manager](media/service-map-scom/scom-config-machine-groups.png)

5. En la ventana **Selección de servidor**, se puede configurar el grupo de servidores de Service Map con los servidores que quiere sincronizar entre Operations Manager y la característica Asignar. Haga clic en **Agregar o quitar servidores**.

    Para que la integración genere un diagrama de aplicación distribuida para un servidor, el servidor debe cumplir lo siguiente:

   * Administrado por Operations Manager
   * Configurado para informar al área de trabajo de Log Analytics configurada con VM Insights
   * Aparece en el grupo de servidores de Service Map

     ![Grupo de configuración de Operations Manager](media/service-map-scom/scom-config-group.png)

6. Opcional: Seleccione el grupo de recursos de todos los servidores de administración para comunicarse con Log Analytics y, a continuación, haga clic en **Agregar área de trabajo**.

    ![Captura de pantalla de la página Server Pool (Grupo de servidores) de Add Microsoft Operations Management Suite Workspace (Agregar área de trabajo de Microsoft Operations Management Suite) con la opción All Management Servers Resource Pool (Grupo de recursos de todos los servidores de administración) seleccionada.](media/service-map-scom/scom-config-pool.png)

    Es posible que el proceso de configuración y registro del área de trabajo de Log Analytics tarde un minuto. Una vez configurado, Operations Manager inicia la primera sincronización de asignación.

    ![Captura de pantalla de la pantalla Completion (Finalización) de Add Microsoft Operations Management Suite Workspace (Agregar área de trabajo de Microsoft Operations Management Suite) que confirma que se ha agregado el área de trabajo.](media/service-map-scom/scom-config-success.png)

## <a name="monitor-integration"></a>Integración de la supervisión

Una vez conectada el área de trabajo de Log Analytics, se mostrará una nueva carpeta, Service Map, en el panel **Supervisión** de la consola de operaciones de Operations Manager.

![Panel de supervisión de Operations Manager](media/service-map-scom/scom-monitoring.png)

La carpeta de Service Map tiene cuatro nodos:

* **Alertas activas**: muestra todas las alertas activas sobre la comunicación entre Operations Manager y Azure Monitor.  

  >[!NOTE]
  >Estas alertas no son alertas de Log Analytics sincronizadas con Operations Manager. Se generan en el grupo de administración a partir de los flujo de trabajo que se definen en el módulo de administración de Service Map.

* **Servidores**: muestra los servidores supervisados que se han configurado para la sincronización desde la característica de asignación de VM Insights.

    ![Panel de servidores de supervisión de Operations Manager](media/service-map-scom/scom-monitoring-servers.png)

* **Machine Group Dependency Views** (Vistas de dependencia de grupo de máquinas): Muestra todos los grupos de máquinas sincronizados desde la característica Asignar. Puede hacer clic en cualquier grupo para ver su diagrama de aplicaciones distribuidas.

    ![Captura de pantalla de Service Map que muestra un diagrama con imágenes para cada grupo de máquinas y líneas que indican las dependencias entre ellas.](media/service-map-scom/scom-group-dad.png)

* **Server Dependency Views** (Vistas de dependencia de servidor): Muestra todos los servidores sincronizados desde la característica Asignar. Puede hacer clic en cualquier servidor para ver su diagrama de aplicaciones distribuidas.

    ![Captura de pantalla de Service Map que muestra un diagrama con imágenes para cada servidor y líneas que indican las dependencias entre ellos.](media/service-map-scom/scom-dad.png)

## <a name="edit-or-delete-the-workspace"></a>Edición o eliminación del área de trabajo

Puede editar o eliminar el área de trabajo configurada desde el panel de **información general de Mapa de servicio** (panel **Administración** > **Operations Management Suite** > **Mapa de servicio**).

> [!NOTE]
> [Operations Management Suite era una colección de servicios](../terminology.md#april-2018---retirement-of-operations-management-suite-brand) que incluía Log Analytics, que ahora forma parte de [Azure Monitor](../overview.md).

Por ahora solo se puede configurar un área de trabajo de Log Analytics en esta versión actual.

![Panel de edición de área de trabajo de Operations Manager](media/service-map-scom/scom-edit-workspace.png)

## <a name="configure-rules-and-overrides"></a>Configuración de reglas e invalidaciones

Una regla, *Microsoft.SystemCenter.ServiceMapImport.Rule*, captura información periódicamente de la característica Asignar de VM Insights. Para modificar el intervalo de sincronización, puede invalidar la regla y modificar el valor del parámetro **IntervalMinutes**.

![Ventana de propiedades de invalidaciones de Operations Manager](media/service-map-scom/scom-overrides.png)

* **Habilitado**: sirve para habilitar o deshabilitar las actualizaciones automáticas.
* **IntervalMinutes**: Especifica el tiempo entre actualizaciones. El intervalo predeterminado es de una hora. Si quiere sincronizar las asignaciones con más frecuencia, puede cambiar el valor.
* **TimeoutSeconds**: especifica el período de tiempo antes de que se agote el tiempo de espera de la solicitud.
* **TimeWindowMinutes**: especifica el período de tiempo para consultar datos. El valor predeterminado es de 60 minutos, que es el intervalo máximo permitido.

## <a name="known-issues-and-limitations"></a>Limitaciones y problemas conocidos

El diseño actual presenta los problemas y limitaciones siguientes:

* Solo se puede conectar a una única área de trabajo de Log Analytics.
* Aunque puede agregar servidores manualmente al grupo de servidores de Service Map desde el panel **Creación**, las asignaciones para esos servidores no se sincronizan inmediatamente. Se sincronizarán desde la característica de asignación de VM Insights durante el siguiente ciclo de sincronización.
* Si realiza cambios en los diagramas de aplicación distribuida creados por el módulo de administración, es probable que se sobrescriban esos cambios en la próxima sincronización con VM Insights.

## <a name="create-a-service-principal"></a>Creación de una entidad de servicio

Para obtener documentación oficial de Azure acerca de cómo crear una entidad de servicio, consulte:

* [Uso de Azure PowerShell para crear una entidad de servicio](../../active-directory/develop/howto-authenticate-service-principal-powershell.md)
* [Uso de la CLI de Azure para crear a una entidad de servicio](/cli/azure/create-an-azure-service-principal-azure-cli)
* [Create a service principal by using the Azure portal](../../active-directory/develop/howto-create-service-principal-portal.md) (Uso de Azure Portal para crear una entidad de servicio)

### <a name="suggestions"></a>Sugerencias

¿Tiene algún comentario para nosotros sobre la integración con la característica de asignación de VM Insights o con esta documentación? Visite nuestra [página Voz del usuario](https://feedback.azure.com/d365community/forum/aa68334e-1925-ec11-b6e6-000d3a4f09d0?c=ad4304e4-1925-ec11-b6e6-000d3a4f09d0), donde puede sugerir características o votar sugerencias existentes.

