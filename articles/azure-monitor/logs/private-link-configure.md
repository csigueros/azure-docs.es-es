---
title: Configuración de Private Link
description: Configuración de Private Link
author: noakup
ms.author: noakuper
ms.topic: conceptual
ms.date: 08/01/2021
ms.openlocfilehash: cdbfd69da09bb3f55f0cdf87bb545e2c6bb40a79
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780078"
---
# <a name="configure-your-private-link"></a>Configuración de Private Link
Para configurar Private Link se necesitan algunos pasos: 
* Crear un ámbito de Private Link con recursos.
* Crear un punto de conexión privado en la red y conectarlo al ámbito.
* Configurar el acceso necesario en los recursos de Azure Monitor.

En este artículo se revisa cómo se realiza desde Azure Portal y se proporciona una plantilla de Azure Resource Manager (ARM) de ejemplo para automatizar el proceso. 

## <a name="create-a-private-link-connection"></a>Creación de una conexión de Private Link

Empiece por crear un recurso de Ámbito de Private Link de Azure Monitor.

1. Vaya a **Crear un recurso** en Azure Portal y busque **Azure Monitor Private Link Scope** (Ámbito de Private Link de Azure Monitor).

   ![Búsqueda de ámbito de Private Link de Azure Monitor](./media/private-link-security/ampls-find-1c.png)

2. Seleccione **Crear**.
3. Seleccione una suscripción y un grupo de recursos.
4. Asigne un nombre al AMPLS. Es mejor usar un nombre significativo y claro, como "AppServerProdTelem".
5. Seleccione **Revisar + crear**. 

   ![Creación de ámbito de Private Link de Azure Monitor](./media/private-link-security/ampls-create-1d.png)

6. Deje que se supere la validación y luego seleccione **Crear**.

### <a name="connect-azure-monitor-resources"></a>Conexión de recursos de Azure Monitor

Conecte los recursos de Azure Monitor (las áreas de trabajo de Log Analytics y los componentes de Application Insights) a AMPLS.

1. En el ámbito de Private Link de Azure Monitor, seleccione **Azure Monitor Resources**  (Recursos de Azure Monitor) en el menú de la izquierda. Seleccione el botón **Agregar**.
2. Agregue el área de trabajo o el componente. Al seleccionar el botón **Agregar** aparece un cuadro de diálogo en el que puede seleccionar los recursos de Azure Monitor. Puede examinar las suscripciones y los grupos de recursos, o puede escribir su nombre para filtrarlos. Seleccione el área de trabajo o componente y seleccione **Aplicar** para agregarlos al ámbito.

    ![Captura de pantalla de selección de una experiencia de usuario de ámbito](./media/private-link-security/ampls-select-2.png)

> [!NOTE]
> La eliminación de recursos de Azure Monitor requiere que se desconecten primero de cualquier objeto AMPLS al que estén conectados. No es posible eliminar recursos conectados a un objeto AMPLS.

### <a name="connect-to-a-private-endpoint"></a>Conexión a un punto de conexión privado

Ahora que tiene recursos conectados a su AMPLS, cree un punto de conexión privado para conectar nuestra red. Puede realizar esta tarea en el [centro de Private Link de Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Network/PrivateLinkCenterBlade/privateendpoints) o en el Ámbito de Private Link de Azure Monitor, como se hace en este ejemplo.

1. En el recurso de ámbito, seleccione **Conexiones de punto de conexión privado** en el menú de recursos de la izquierda. Seleccione **Punto de conexión privado** para iniciar el proceso de creación del punto de conexión. Aquí también puede aprobar las conexiones que se iniciaron en el centro de Private Link seleccionándolas y, después, seleccionando **Aprobar**.

    ![Captura de pantalla de la experiencia de usuario de conexiones de punto de conexión privado](./media/private-link-security/ampls-select-private-endpoint-connect-3.png)

2. Seleccione la suscripción, el grupo de recursos y el nombre del punto de conexión, así como la región en la que debe residir. La región debe ser la misma que la de la red virtual a la que se conecta.

3. Seleccione **Siguiente: Resource** (Siguiente: Recurso). 

4. En la pantalla Recurso,

   a. En **Suscripción**, seleccione la suscripción que contiene el recurso de Ámbito de Private Link de Azure Monitor. 

   b. Para **Tipo de recurso**, elija **Microsoft.insights/privateLinkScopes**. 

   c. En la lista desplegable **Recurso**, elija el ámbito de Private Link que creó anteriormente. 

   d. Seleccione **Siguiente: Configuración >** .
      ![Captura de pantalla de selección de creación de punto de conexión privado](./media/private-link-security/ampls-select-private-endpoint-create-4.png)

5. En el panel de configuración,

   a.    Elija el **red virtual** y la **subred** a las que desea conectar los recursos de Azure Monitor. 
 
   b.    Elija **Sí** para **Integrar con la zona DNS privada** y permita que cree automáticamente una nueva zona DNS privada. Las zonas DNS reales pueden ser distintas de las que se muestran en la siguiente captura de pantalla. 
   > [!NOTE]
   > Si elige **No** y prefiere administrar los registros de DNS manualmente, complete primero la configuración de Private Link, incluido este punto de conexión privado y la configuración de AMPLS. A continuación, configure DNS según las instrucciones que se indican en [Configuración de DNS para puntos de conexión privados de Azure](../../private-link/private-endpoint-dns.md). Asegúrese de no crear registros vacíos como preparación para la configuración de Private Link. Los registros de DNS que se crean pueden invalidar la configuración existente e influir en la conectividad con Azure Monitor.
 
   c.    Seleccione **Revisar + crear**.
 
   d.    Permita que la validación se supere. 
 
   e.    Seleccione **Crear**. 

    ![Captura de pantalla de selección de creación de punto de conexión privado.](./media/private-link-security/ampls-select-private-endpoint-create-5.png)

Ahora ha creado un nuevo punto de conexión privado que está conectado a este AMPLS.


## <a name="configure-access-to-your-resources"></a>Configuración del acceso a los recursos
Hasta ahora se ha descrito la configuración de la red, pero también debe tener en cuenta cómo quiere configurar el acceso de red a los recursos supervisados: áreas de trabajo de Log Analytics y componentes de Application Insights.

Vaya a Azure Portal. En el menú del recurso hay un elemento denominado **Aislamiento de red** en el lado izquierdo. En esta página se controla qué redes pueden acceder al recurso mediante un vínculo privado y si otras redes pueden hacerlo o no.


> [!NOTE]
> A partir del 16 de agosto de 2021, se aplicará estrictamente el aislamiento de red. Los recursos establecidos para bloquear las consultas desde redes públicas y que no están conectados a ninguna red privada (por medio de AMPLS) dejarán de aceptar consultas de todas las redes.

![Aislamiento de red de Log Analytics](./media/private-link-security/ampls-network-isolation.png)

### <a name="connected-azure-monitor-private-link-scopes"></a>Ámbitos de Private Link de Azure Monitor conectados
Aquí puede revisar y configurar las conexiones del recurso a los ámbitos de vínculo privado de Azure Monitor. La conexión a los ámbitos (AMPLS) permite que el tráfico desde la red virtual conectada a cada AMPLS llegue al recurso. Tiene el mismo efecto que realizar la conexión desde el ámbito, como se ha hecho en [Conexión de recursos de Azure Monitor](#connect-azure-monitor-resources). 

Para agregar una nueva conexión, seleccione **Agregar** y seleccione el ámbito de Private Link de Azure Monitor. Seleccione **Aplicar** para conectarlo. El recurso puede conectarse a cinco objetos AMPLS, como se ha mencionado en [Consideración de los límites de AMPLS](./private-link-design.md#consider-ampls-limits).

### <a name="virtual-networks-access-configuration---managing-access-from-outside-of-private-links-scopes"></a>Configuración de acceso a redes virtuales: administración del acceso desde ámbitos externos a vínculos privados
La configuración de la parte inferior de esta página controla el acceso desde las redes públicas, es decir, las redes no conectadas a los ámbitos enumerados (AMPLS).

Si establece **Allow public network access for ingestion** (Permitir el acceso de la red pública para la ingesta) en **No**, los clientes (máquinas, SDK, etc.) que se encuentren fuera de los ámbitos conectados no podrán cargar datos en el recurso.

Si establece **Allow public network access for queries** (Permitir el acceso de la red pública para consultas) en **No**, los clientes (máquinas, SDK, etc.) que se encuentren fuera de los ámbitos conectados no podrán consultar datos en el recurso. Estos datos incluyen el acceso a los registros, las métricas y la transmisión de métricas en directo, así como experiencias basadas en ellos, como libros, paneles, experiencias de cliente basadas en API de consulta, conclusiones de Azure Portal, etc. Las experiencias que se ejecutan fuera de Azure Portal y que consultan datos de Log Analytics también se deben ejecutar dentro de la red virtual vinculada privada.


### <a name="exceptions"></a>Excepciones

#### <a name="diagnostic-logs"></a>Registros de diagnóstico
Los registros y las métricas que se cargan en un área de trabajo a través de [Configuración de diagnóstico](../essentials/diagnostic-settings.md) pasan por un canal de Microsoft privado seguro y no se controlan mediante esta configuración.

#### <a name="azure-resource-manager"></a>Azure Resource Manager
La restricción del acceso, como se ha explicado antes, se aplica a los datos del recurso. No obstante, los cambios de configuración, incluida la activación o desactivación de esta configuración de acceso, se administran mediante Azure Resource Manager. Para controlar esta configuración, debe restringir el acceso a los recursos mediante los roles, los permisos, los controles de red y la auditoría adecuados. Para obtener más información, vea [Roles, permisos y seguridad en Azure Monitor](../roles-permissions-security.md).

Además, experiencias concretas (como el conector LogicApp, la solución Update Management y el panel Resumen del área de trabajo del portal, que muestra el panel de soluciones) consultan datos por medio de Azure Resource Manager y, por tanto, no podrán consultar los datos a menos que también se aplique la configuración de Private Link a Resource Manager.


## <a name="review-and-validate-your-private-link-setup"></a>Revisión y validación de la configuración de Private Link

### <a name="reviewing-your-endpoints-dns-settings"></a>Revisión de la configuración de DNS del punto de conexión
El punto de conexión privado que creó debería tener ahora cinco zonas DNS configuradas:

* privatelink-monitor-azure-com
* privatelink-oms-opinsights-azure-com
* privatelink-ods-opinsights-azure-com
* privatelink-agentsvc-azure-automation-net
* privatelink-blob-core-windows-net

> [!NOTE]
> Cada una de estas zonas asigna puntos de conexión de Azure Monitor específicos a direcciones IP privadas desde el grupo de direcciones IP de la red virtual. Las direcciones IP que se muestran en las imágenes siguientes son solo ejemplos. En su lugar, la configuración debería mostrar direcciones IP privadas de su propia red.

#### <a name="privatelink-monitor-azure-com"></a>Privatelink-monitor-azure-com
Esta zona cubre los puntos de conexión globales usados por Azure Monitor, lo que significa que estos puntos de conexión atienden solicitudes que tienen en cuenta todos los recursos, no uno específico. Esta zona debe tener puntos de conexión asignados para:
* `in.ai`: punto de conexión de ingesta de Application Insights (una entrada global y una regional)
* `api`: punto de conexión de la API de Log Analytics y Application Insights.
* `live`: punto de conexión de métricas en directo de Application Insights.
* `profiler`: punto de conexión de Application Insights Profiler.
* `snapshot`: punto de conexión de instantáneas de Application Insights [![Captura de pantalla de la zona DNS privada monitor-azure-com.](./media/private-link-security/dns-zone-privatelink-monitor-azure-com.png)](./media/private-link-security/dns-zone-privatelink-monitor-azure-com-expanded.png#lightbox)

#### <a name="privatelink-oms-opinsights-azure-com"></a>privatelink-oms-opinsights-azure-com
Esta zona cubre la asignación específica del área de trabajo a los puntos de conexión de OMS. Debería ver una entrada para cada área de trabajo vinculada al AMPLS conectado a este punto de conexión privado.
[![Captura de pantalla de la zona DNS privada oms-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-oms-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-ods-opinsights-azure-com"></a>privatelink-ods-opinsights-azure-com
Esta zona cubre la asignación específica del área de trabajo a los puntos de conexión de ODS: el punto de conexión de ingesta de Log Analytics. Debería ver una entrada para cada área de trabajo vinculada al AMPLS conectado a este punto de conexión privado.
[![Captura de pantalla de la zona DNS privada ods-opinsights-azure-com.](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com.png)](./media/private-link-security/dns-zone-privatelink-ods-opinsights-azure-com-expanded.png#lightbox)

#### <a name="privatelink-agentsvc-azure-automation-net"></a>privatelink-agentsvc-azure-automation-net
Esta zona cubre la asignación específica del área de trabajo a los puntos de conexión de automatización del servicio del agente. Debería ver una entrada para cada área de trabajo vinculada al AMPLS conectado a este punto de conexión privado.
[![Captura de pantalla del agente de la zona DNS privada svc-azure-automation-net.](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net.png)](./media/private-link-security/dns-zone-privatelink-agentsvc-azure-automation-net-expanded.png#lightbox)

#### <a name="privatelink-blob-core-windows-net"></a>privatelink-blob-core-windows-net
Esta zona configura la conectividad con la cuenta de almacenamiento de paquetes de soluciones de los agentes globales. A través de ella, los agentes pueden descargar paquetes de soluciones nuevos o actualizados (también conocidos como módulos de administración). Solo se necesita una entrada para controlar los agentes de Log Analytics, independientemente del número de áreas de trabajo que se utilicen.
[![Captura de pantalla DNS privado zona de almacenamiento blob-core-windows-net.](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net.png)](./media/private-link-security/dns-zone-privatelink-blob-core-windows-net-expanded.png#lightbox)
> [!NOTE]
> Esta entrada solo se agrega a las configuraciones de vínculos privados creadas a partir del 19 de abril de 2021 (o a partir de junio de 2021 en nubes soberanas de Azure).


### <a name="validating-you-are-communicating-over-a-private-link"></a>Validación de comunicación a través de Private Link
* Para validar que las solicitudes ya se envían a través del punto de conexión privado, puede revisarlas con una herramienta de seguimiento de red o, incluso, con el explorador. Por ejemplo, al intentar consultar el área de trabajo o la aplicación, asegúrese de que la solicitud se envía a la dirección IP privada asignada al punto de conexión de la API; en este ejemplo, es *172.17.0.9*.

    Nota: Algunos exploradores pueden usar otras opciones de configuración de DNS (consulte [Configuración de DNS del explorador](./private-link-design.md#browser-dns-settings)). Asegúrese de que se aplica la configuración de DNS.

* Para asegurarse de que el área de trabajo o el componente no reciben solicitudes de redes públicas (no conectadas por medio de AMPLS), establezca la ingesta pública del recurso y las marcas de consulta en *No*, como se explica en [Configuración del acceso a los recursos](#configure-access-to-your-resources).

* Desde un cliente de la red protegida, use `nslookup` para cualquiera de los puntos de conexión enumerados en las zonas DNS. El servidor DNS debe resolverlo en las direcciones IP privadas asignadas en lugar de las direcciones IP públicas que se usan de manera predeterminada.


## <a name="use-apis-and-command-line"></a>Uso de API y de la línea de comandos

Puede automatizar el proceso descrito anteriormente mediante plantillas de Azure Resource Manager, REST e interfaces de la línea de comandos.

Para crear y administrar ámbitos de Private Link, use la [API REST](/rest/api/monitor/privatelinkscopes(preview)/private%20link%20scoped%20resources%20(preview)) o la [CLI de Azure (az monitor private-link-scope)](/cli/azure/monitor/private-link-scope).

Para administrar la marca de acceso a la red en el área de trabajo o el componente, utilice las marcas `[--ingestion-access {Disabled, Enabled}]` y `[--query-access {Disabled, Enabled}]`en [áreas de trabajo de Log Analytics](/cli/azure/monitor/log-analytics/workspace) o [componentes de Application Insights](/cli/azure/ext/application-insights/monitor/app-insights/component).

### <a name="example-azure-resource-manager-template-arm-template"></a>Plantilla de Azure Resource Manager (plantilla de ARM) de ejemplo
Esta plantilla de Azure Resource Manager crea lo siguiente:
* Un ámbito de vínculo privado (AMPLS) denominado "my-scope"
* Un área de trabajo de Log Analytics denominada "my-workspace"
* Agregue un recurso con ámbito al AMPLS "my-scope", denominado "my-workspace-connection".

```
{
    "$schema": https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#,
    "contentVersion": "1.0.0.0",
    "parameters": {
        "private_link_scope_name": {
            "defaultValue": "my-scope",
            "type": "String"
        },
        "workspace_name": {
            "defaultValue": "my-workspace",
            "type": "String"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "microsoft.insights/privatelinkscopes",
            "apiVersion": "2019-10-17-preview",
            "name": "[parameters('private_link_scope_name')]",
            "location": "global",
            "properties": {}
        },
        {
            "type": "microsoft.operationalinsights/workspaces",
            "apiVersion": "2020-10-01",
            "name": "[parameters('workspace_name')]",
            "location": "westeurope",
            "properties": {
                "sku": {
                    "name": "pergb2018"
                },
                "publicNetworkAccessForIngestion": "Enabled",
                "publicNetworkAccessForQuery": "Enabled"
            }
        },
        {
            "type": "microsoft.insights/privatelinkscopes/scopedresources",
            "apiVersion": "2019-10-17-preview",
            "name": "[concat(parameters('private_link_scope_name'), '/', concat(parameters('workspace_name'), '-connection'))]",
            "dependsOn": [
                "[resourceId('microsoft.insights/privatelinkscopes', parameters('private_link_scope_name'))]",
                "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            ],
            "properties": {
                "linkedResourceId": "[resourceId('microsoft.operationalinsights/workspaces', parameters('workspace_name'))]"
            }
        }
    ]
}
```

## <a name="next-steps"></a>Pasos siguientes

- Obtener información sobre el [almacenamiento privado](private-storage.md).
- Más información sobre [Private Link para Automation](../../automation/how-to/private-link-security.md)