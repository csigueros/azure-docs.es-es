---
title: Configuración de Connection Monitor para Azure ExpressRoute
description: Configure la supervisión de la conectividad de red basada en la nube para circuitos de Azure ExpressRoute. Aborda la supervisión a través de emparejamiento privado de ExpressRoute y emparejamiento de Microsoft.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 07/28/2021
ms.author: duau
ms.openlocfilehash: 794e841d8d50683374c86301ec55bc4f6ab53ec6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780660"
---
# <a name="configure-connection-monitor-for-expressroute"></a>Configuración de Connection Monitor para ExpressRoute

Este artículo ayuda a configurar una extensión de Connection Monitor para supervisar ExpressRoute. Connection Monitor es una solución de supervisión de red basada en la nube que supervisa la conectividad entre las implementaciones de nube de Azure y las ubicaciones locales (sucursales, etc.) Connection Monitor forma parte de los registros de Azure Monitor.  La extensión también le permite supervisar la conectividad de red para las conexiones privadas y de emparejamiento de Microsoft. Al configurar Connection Monitor para ExpressRoute, puede detectar problemas de red para identificarlos y eliminarlos.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Con Connection Monitor para ExpressRoute puede hacer lo siguiente:

* Supervisar la latencia y pérdida en varias redes virtuales y establecer alertas.

* Supervisar todas las rutas de acceso (incluidas las redundantes) de la red.

* Solucionar problemas de red transitorios y puntuales que resultan difíciles de replicar.

* Ayudar a determinar un segmento específico de la red que es responsable de la degradación del rendimiento.

## <a name="workflow"></a>Flujo de trabajo de <a name="workflow"></a>

Se instalan agentes de supervisión en varios servidores, tanto en el entorno local como en Azure. Los agentes se comunican entre sí mediante el envío de paquetes de protocolo de enlace TCP. La comunicación entre los agentes permite a Azure asignar la topología de red y la ruta de acceso que puede tomar el tráfico.

1. Cree un área de trabajo de Log Analytics.

1. Instalación y configuración de agentes de software. (Si solo quiere supervisar el emparejamiento de Microsoft, no es necesario instalar ni configurar agentes de software):

    * Instale agentes de supervisión en los servidores locales y las máquinas virtuales de Azure (para emparejamiento privado).
    * Configure las opciones en los servidores de agente de supervisión para permitir que estos agentes se comuniquen. (Por ejemplo, abrir puertos del firewall).

1. Configure las reglas de grupo de seguridad de red (NSG) para permitir que el agente de supervisión instalado en las máquinas virtuales de Azure se comuniquen con agentes de supervisión locales.

1. Habilite Network Watcher en la suscripción.

1. Configure la supervisión: cree monitores de conexión con grupos de prueba para supervisar los puntos de conexión de origen y de destino en la red.

Si ya usa Network Performance Monitor (en desuso) o Connection Monitor para supervisar otros objetos o servicios, y ya tiene un área de trabajo de Log Analytics en una de las regiones admitidas, puede omitir los pasos 1 y 2, y comenzar la configuración en el paso 3.

## <a name="create-a-workspace"></a><a name="configure"></a> Creación de un área de trabajo

Cree un área de trabajo en la suscripción que tiene las redes virtuales vinculadas a los circuitos ExpressRoute.

1. Inicie sesión en [Azure Portal](https://portal.azure.com). En la suscripción que tiene las redes virtuales conectadas al circuito ExpressRoute, seleccione **+ Crear un recurso**. Busque *Área de trabajo de Log Analytics* y, después, seleccione **Crear**.

   >[!NOTE]
   >Puede crear una nueva área de trabajo o usar una existente. Si desea usar un área de trabajo existente, debe asegurarse de que se haya migrado el área de trabajo al nuevo lenguaje de consulta. [Más información...](../azure-monitor/logs/log-query-overview.md)
   >

    :::image type="content" source="./media/how-to-configure-connection-monitor/search-log-analytics.png" alt-text="Captura de pantalla de la búsqueda de Log Analytics en la creación de un recurso.":::

1. Para crear un área de trabajo, escriba o seleccione la información siguiente.  

    | Configuración | Value |
    | -------- | ----- |
    | Suscripción | Seleccione la suscripción con el circuito ExpressRoute. |
    | Grupo de recursos | Cree un grupo de recursos o seleccione uno existente. |
    | Nombre | Escriba un nombre para identificar esta área de trabajo. |
    | Region | Seleccione una región en la que se creará esta área de trabajo. |

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-workspace-basic.png" alt-text="Captura de pantalla de la pestaña Datos básicos para crear un área de trabajo de Log Analytics.":::

     >[!NOTE]
     >El circuito ExpressRoute puede estar en cualquier lugar del mundo. No tiene que estar en la misma región que el área de trabajo.
     >

1. Seleccione **Revisar y crear** para validarlo y después **Crear** para implementar el área de trabajo. Una vez que haya implementado el área de trabajo, continúe con la sección siguiente para configurar la solución de supervisión.

## <a name="configure-monitoring-solution"></a><a name="npm"></a>Configuración de soluciones de supervisión

Complete el script de Azure PowerShell siguiente y reemplace los valores de *$SubscriptionId*, *$location*, *$resourceGroup* y *$workspaceName*. Después, ejecute el script para configurar la solución de supervisión.

```azurepowershell-interactive
$subscriptionId = "Subscription ID should come here"
Select-AzSubscription -SubscriptionId $subscriptionId

$location = "Workspace location should come here"
$resourceGroup = "Resource group name should come here"
$workspaceName = "Workspace name should come here"

$solution = @{
    Location          = $location
    Properties        = @{
        workspaceResourceId = "/subscriptions/$($subscriptionId)/resourcegroups/$($resourceGroup)/providers/Microsoft.OperationalInsights/workspaces/$($workspaceName)"
    }
    Plan              = @{
        Name          = "NetworkMonitoring($($workspaceName))" 
        Publisher     = "Microsoft"
        Product       = "OMSGallery/NetworkMonitoring"
        PromotionCode = ""
    }
    ResourceName      = "NetworkMonitoring($($workspaceName))" 
    ResourceType      = "Microsoft.OperationsManagement/solutions" 
    ResourceGroupName = $resourceGroup
}

New-AzureRmResource @solution -Force
```

Una vez que haya configurado la solución de supervisión, continúe con el siguiente paso de instalación y configuración de los agentes de supervisión en los servidores.

## <a name="install-and-configure-agents-on-premises"></a><a name="agents"></a>Instalación y configuración de agentes en el entorno local

### <a name="download-the-agent-setup-file"></a><a name="download"></a>Descarga del archivo de instalación del agente

1. Vaya al **área de trabajo de Log Analytics** y seleccione **Administración de agentes** en *Configuración*. Descargue el agente correspondiente al sistema operativo de la máquina.

    :::image type="content" source="./media/how-to-configure-connection-monitor/download-agent.png" alt-text="Captura de pantalla de la página de administración de agentes en el área de trabajo.":::

1. Después, copie los valores de **Id. de área de trabajo** y **Clave principal** en el Bloc de notas.

    :::image type="content" source="./media/how-to-configure-connection-monitor/copy-id-key.png" alt-text="Captura de pantalla del identificador y la clave principal del área de trabajo.":::

1. Para máquinas Windows, descargue y ejecute este script de PowerShell [*EnableRules.ps1*](https://aka.ms/npmpowershellscript) en una ventana de PowerShell con privilegios de administrador. El script de PowerShell ayudará a abrir el puerto de firewall adecuado para las transacciones TCP. 
 
    Para las máquinas Linux, el número de puerto se debe cambiar manualmente con los pasos siguientes:

    * Vaya a la ruta: /var/opt/microsoft/omsagent/npm_state.
    * Abra el archivo npmdregistry.
    * Cambie el valor de número de puerto `PortNumber:<port of your choice>`.

### <a name="install-log-analytics-agent-on-each-monitoring-server"></a><a name="installagentonprem"></a>Instalación del agente de Log Analytics en cada servidor de supervisión

Se recomienda instalar el agente de Log Analytics en al menos dos servidores en ambos lados de la conexión de ExpressRoute para obtener redundancia. Por ejemplo, en la red local y en la red virtual de Azure. Use los pasos siguientes para instalar agentes:

1. Seleccione el sistema operativo adecuado siguiente para los pasos de instalación del agente de Log Analytics en los servidores.

    * [Windows](../azure-monitor/agents/agent-windows.md#install-agent-using-setup-wizard)
    * [Linux](../azure-monitor/agents/agent-linux.md)

1. Una vez completado el proceso, Microsoft Monitoring Agent aparece en el Panel de control. Puede revisar la configuración y [comprobar la conectividad del agente](../azure-monitor/agents/agent-windows.md#verify-agent-connectivity-to-azure-monitor) a los registros de Azure Monitor.

1. Repita los pasos 1 y 2 en las demás máquinas locales que quiera usar para la supervisión.

### <a name="install-network-watcher-agent-on-each-monitoring-server"></a><a name="installagentazure"></a>Instalación del agente de Network Watcher en cada servidor de supervisión

#### <a name="new-azure-virtual-machine"></a>Nueva máquina virtual de Azure

Si va a crear una máquina virtual de Azure para supervisar la conectividad de la red virtual, puede instalar el agente de Network Watcher al [crear la máquina virtual](../network-watcher/connection-monitor.md#create-the-first-vm).

#### <a name="existing-azure-virtual-machine"></a>Máquina virtual de Azure existente

Si va a usar una máquina virtual existente para supervisar la conectividad, puede instalar el agente de red de forma independiente para [Linux](../virtual-machines/extensions/network-watcher-linux.md) y [Windows](../virtual-machines/extensions/network-watcher-windows.md).

### <a name="open-the-firewall-ports-on-the-monitoring-agent-servers"></a><a name="firewall"></a>Apertura de los puertos de firewall en los servidores de agente de supervisión

Las reglas de un firewall pueden bloquear la comunicación entre los servidores de origen y destino. Connection Monitor detecta este problema y lo muestra como un mensaje de diagnóstico en la topología. Para habilitar la supervisión de la conexión, asegúrese de que las reglas de firewall permitan los paquetes por medio de TCP o ICMP entre el origen y el destino.

#### <a name="windows"></a>Windows

Para las máquinas Windows, puede ejecutar un script de PowerShell para crear las claves del Registro necesarias para Connection Monitor. Este script también crea reglas del Firewall de Windows para permitir que los agentes de supervisión creen conexiones TCP entre sí. Las claves del Registro que crea el script especifican si se deben escribir los registros de depuración y la ruta del archivo de registro. Asimismo, define el puerto TCP del agente empleado para establecer la comunicación. El script establece automáticamente los valores de estas claves. No debería cambiar manualmente estas claves.

El puerto 8084 se abre de forma predeterminada. Puede utilizar un puerto personalizado especificando el parámetro "portNumber" en el script. Sin embargo, si lo hace, debe especificar el mismo puerto para todos los servidores en los que se ejecuta el script.

>[!NOTE]
>El script de PowerShell "EnableRules" solo configura reglas del firewall de Windows en el servidor donde se ejecuta. Si tiene un firewall de red, debe asegurarse de que permita el tráfico destinado al puerto TCP que use Connection Monitor.
>

En los servidores de agente, abra una ventana de PowerShell con privilegios administrativos. Ejecute el script [EnableRules](https://aka.ms/npmpowershellscript) de PowerShell (que descargó anteriormente). No utilice ningún parámetro.

:::image type="content" source="./media/how-to-configure-connection-monitor/enable-rules-script.png" alt-text="Captura de pantalla de la ejecución del script de habilitación de reglas en la ventana de PowerShell.":::

#### <a name="linux"></a>Linux

Para las máquinas Linux, es necesario cambiar manualmente los números de puerto utilizados:

1. Vaya a la ruta: /var/opt/microsoft/omsagent/npm_state.
1. Abra el archivo npmdregistry.
1. Cambie el valor del número de puerto `PortNumber:\<port of your choice\>`. Los números de puerto que se usan deben ser iguales en todos los agentes utilizados en un área de trabajo.

## <a name="configure-network-security-group-rules"></a><a name="opennsg"></a>Configuración de las reglas del grupo de seguridad de red

Para supervisar los servidores que se encuentran en Azure, debe configurar reglas del grupo de seguridad de red (NSG) para permitir el tráfico TCP o ICMP desde Connection Monitor. El puerto predeterminado es **8084, que permite que el agente de supervisión instalado en la máquina virtual de Azure se comunique con un agente de supervisión local.

Para obtener más información sobre NSG, vea el tutorial sobre [filtrado del tráfico de red](../virtual-network/tutorial-filter-network-traffic.md).

> [!NOTE]
> Asegúrese de que ha instalado los agentes (tanto el agente de servidor local como el agente de servidor de Azure) y que ha ejecutado el script de PowerShell antes de continuar con este paso.
>

## <a name="enable-network-watcher"></a><a name="enablenetworkwatcher"></a>Habilitación de Network Watcher

Todas las suscripciones que tienen una red virtual se habilitan con Network Watcher. Asegúrese de que Network Watcher no esté explícitamente deshabilitado en la suscripción. Para más información, consulte [Habilitación de Network Watcher](../network-watcher/network-watcher-create.md).

## <a name="create-a-connection-monitor"></a><a name="createcm"></a> Creación de un monitor de conexión

Para obtener información general sobre cómo crear un monitor de conexión, pruebas y grupos de pruebas en los puntos de conexión de origen y destino de la red, vea [Creación de un monitor de conexión](../network-watcher/connection-monitor-create-using-portal.md). Siga estos pasos para configurar la supervisión de conexiones para el emparejamiento privado y el emparejamiento de Microsoft.

1. En Azure Portal, vaya al recurso **Network Watcher** y seleccione **Monitor de conexión** en *Supervisión*. Después, seleccione **Crear** para crear un monitor de conexión.

    :::image type="content" source="./media/how-to-configure-connection-monitor/create-connection-monitor.png" alt-text="Captura de pantalla del monitor de conexión en Network Watcher.":::

1. En la pestaña **Datos básicos** del flujo de trabajo de creación, seleccione la misma región en la que haya implementado el área de trabajo de Log Analytics para el campo *Región*. En *Configuración del área de trabajo*, seleccione el área de trabajo de Log Analytics existente que ha creado antes. Después, seleccione **Siguiente: Grupos de prueba >>** .

    :::image type="content" source="./media/how-to-configure-connection-monitor/connection-monitor-basic.png" alt-text="Captura de pantalla de la pestaña Datos básicos para crear Connection Monitor.":::

1. En la página de detalles Agregar grupo de prueba, agregará los puntos de conexión de origen y de destino para el grupo de prueba. También configurará las configuraciones de prueba entre ellos. Escriba un **Nombre** para este grupo de prueba.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details.png" alt-text="Captura de pantalla de la página de detalles Agregar grupo de prueba.":::

1. Seleccione **Agregar origen** y vaya a la pestaña **Puntos de conexión que no son de Azure**. Elija los recursos locales con el agente de Log Analytics instalado para los que quiera supervisar la conectividad y, después, seleccione **Agregar puntos de conexión**.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-source-endpoints.png" alt-text="Captura de pantalla de la adición de puntos de conexión de origen.":::

1. Después, seleccione **Agregar destinos**. 

    Para supervisar la conectividad por medio del **emparejamiento privado** de ExpressRoute, vaya a la pestaña **Puntos de conexión de Azure**. Elija los recursos de Azure que tengan instalado el agente de Network Watcher y de los que quiera supervisar la conectividad a las redes virtuales en Azure. Asegúrese de seleccionar la dirección IP privada de cada uno de los recursos en la columna *IP*. Seleccione **Agregar punto de conexión** para agregarlo a la lista de destinos para el grupo de prueba.
    
    :::image type="content" source="./media/how-to-configure-connection-monitor/add-destination-endpoints.png" alt-text="Captura de pantalla de la adición de puntos de conexión de destino de Azure.":::

    Para supervisar la conectividad por medio del **emparejamiento de Microsoft** de ExpressRoute, vaya a la pestaña **Direcciones externas**. Seleccione los puntos de conexión de servicios de Microsoft en los que quiera supervisar la conectividad por medio del emparejamiento de Microsoft. Seleccione **Agregar punto de conexión** para agregarlo a la lista de destinos para el grupo de prueba.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-external-destination-endpoints.png" alt-text="Captura de pantalla de la adición de puntos de conexión de destino externos.":::

1. Ahora, seleccione **Agregar configuración de prueba**. Seleccione **TCP** para al protocolo y escriba el **puerto de destino** que haya abierto en los servidores. Después, configure los valores de **Frecuencia de prueba** y los **umbrales de Comprobaciones erróneas y Tiempo de ida y vuelta**. Luego, seleccione **Agregar configuración de prueba**.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-configuration.png" alt-text="Captura de pantalla de la página Agregar configuración de prueba.":::

1. Seleccione **Agregar grupo de prueba** cuando haya agregado los orígenes, los destinos y la configuración de prueba.

    :::image type="content" source="./media/how-to-configure-connection-monitor/add-test-group-details-configured.png" alt-text="Captura de pantalla de los detalles de agregar grupo de prueba configurados." lightbox="./media/how-to-configure-connection-monitor/add-test-group-details-configured-expanded.png":::

1. Seleccione **Siguiente: Crear alerta >>** si quiere crear alertas. Cuando haya terminado, seleccione **Revisar y crear** y después **Crear**.

## <a name="view-results"></a>Vista de resultados

1. Vaya al recurso **Network Watcher** y seleccione **Monitor de conexión** en *Supervisión*. Debería ver el nuevo monitor de conexión después de 5 minutos. Para ver los gráficos de rendimiento y topología de red del monitor de conexión, seleccione la prueba en la lista desplegable de grupos de prueba.

    :::image type="content" source="./media/how-to-configure-connection-monitor/overview.png" alt-text="Captura de pantalla de la página de información general del monitor de conexión." lightbox="./media/how-to-configure-connection-monitor/overview-expanded.png":::

1. En el panel **Análisis de rendimiento** puede ver el porcentaje de comprobación con error y los resultados de cada prueba para el tiempo de ida y vuelta. Puede ajustar el período de tiempo de los datos mostrados si selecciona la lista desplegable de la parte superior del panel.

    :::image type="content" source="./media/how-to-configure-connection-monitor/performance-analysis.png" alt-text="Captura de pantalla del panel de análisis de rendimiento." lightbox="./media/how-to-configure-connection-monitor/performance-analysis-expanded.png":::

1. Al cerrar el panel **Análisis de rendimiento** se muestra la topología de red detectada por el monitor de conexión entre los puntos de conexión de origen y destino que ha seleccionado. En esta vista se muestran las rutas bidireccionales del tráfico entre los puntos de conexión de origen y de destino. También puede ver la latencia salto a salto de los paquetes antes de que lleguen a la red perimetral de Microsoft. 

    :::image type="content" source="./media/how-to-configure-connection-monitor/topology.png" alt-text="Captura de pantalla de la topología de red en el monitor de conexión." lightbox="./media/how-to-configure-connection-monitor/topology-expanded.png":::

    Al seleccionar cualquier salto en la vista de topología, se mostrará información adicional sobre el salto. Aquí también se mostrarán los problemas detectados por el monitor de conexión sobre el salto.

    :::image type="content" source="./media/how-to-configure-connection-monitor/hop-details.png" alt-text="Captura de pantalla de información adicional sobre un salto de red.":::

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la [supervisión de Azure ExpressRoute](monitor-expressroute.md)