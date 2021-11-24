---
title: Creación de una configuración de diagnóstico para enviar registros y métricas de plataforma de Azure Monitor a diferentes destinos
description: Envíe métricas y registros a Azure Monitor, Azure Storage o Azure Event Hubs utilizando la misma configuración de diagnóstico.
author: rboucher
ms.author: robb
services: azure-monitor
ms.topic: conceptual
ms.date: 11/11/2021
ms.openlocfilehash: aa49185d8941038ab100a0a480061cf63560f439
ms.sourcegitcommit: 0415f4d064530e0d7799fe295f1d8dc003f17202
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/17/2021
ms.locfileid: "132705462"
---
# <a name="create-diagnostic-settings-to-send-azure-monitor-platform-logs-and-metrics-to-different-destinations"></a>Creación de una configuración de diagnóstico para enviar registros y métricas de plataforma de Azure Monitor a diferentes destinos

En este artículo, se explica cómo crear y establecer la configuración de diagnóstico para enviar métricas y registros de plataforma de Azure a diferentes destinos.

Las [métricas de plataforma](./metrics-supported.md) se envían automáticamente a las [métricas de Azure Monitor](./data-platform-metrics.md), de manera predeterminada y sin configurar.

Los [registros de plataforma](./platform-logs-overview.md), como los registros de recursos y los registros de actividad de Azure, proporcionan información de diagnóstico y auditoría detallada sobre los recursos de Azure y la plataforma de Azure de la que dependen. El registro de actividad existe por sí mismo, pero se puede enrutar a otras ubicaciones.  Los registros de recursos no se recopilan hasta que se enrutan a un destino.

Cada recurso de Azure necesita su propia configuración de diagnóstico, que establece los siguientes criterios:

- Orígenes: el tipo de datos de métrica y registro que se envían a los destinos definidos en la configuración. Los tipos disponibles varían según el tipo de recurso.
- Destinos: uno o más destinos a lo que enviar.

Cada configuración de diagnóstico puede definir un único destino. Si desea enviar datos a más de un tipo de destino determinado (por ejemplo, dos áreas de trabajo de Log Analytics diferentes), cree varias configuraciones. Cada recurso puede tener hasta 5 configuraciones de diagnóstico.

El siguiente vídeo describe cómo enrutar registros de plataforma con una configuración de diagnóstico.
> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4AvVO]

## <a name="sources"></a>Orígenes

Estas son las opciones de origen.

### <a name="metrics"></a>Métricas

La configuración **AllMetrics** enruta las métricas de plataforma de un recurso a destinos adicionales. Es posible que esta opción no aparezca en todos los proveedores de recursos.  

### <a name="logs"></a>Registros

Con los registros, puede seleccionar las categorías de registro que desea enrutar individualmente o elegir un grupo de categorías.

> [!NOTE]
> Los grupos de categorías no se aplican a las métricas. No todos los recursos tienen grupos de categorías disponibles.

Los **grupos de categorías** permiten recopilar dinámicamente registros de recursos basados en agrupaciones definidas previamente en lugar de seleccionar categorías de registro individuales. Microsoft define las agrupaciones para ayudar a supervisar casos de uso específicos en todos los servicios de Azure. Con el tiempo, las categorías del grupo se pueden actualizar a medida que se van implantando nuevos registros o a medida que cambian las evaluaciones. Cuando se agregan o quitan categorías de registros de un grupo de categorías, la recopilación de registros se modifica automáticamente sin tener que actualizar la configuración de diagnóstico.

Cuando se usan grupos de categorías, se hace lo siguiente: 
- Ya no puede seleccionar individualmente los registros de recursos en función de los tipos de categoría individuales
- Ya no puede aplicar la configuración de retención a los registros enviados a Azure Storage

Actualmente, hay dos grupos de categorías:
- **Todos**: cada registro de recursos que ofrece el recurso.
- **Auditar**: todos los registros de recursos que registran las interacciones de los clientes con los datos o la configuración del servicio.

## <a name="destinations"></a>Destinations
Los registros y las métricas de plataforma se pueden enviar a los destinos de la tabla siguiente. 

| Destination | Descripción |
|:---|:---|
| [Área de trabajo de Log Analytics](../logs/design-logs-deployment.md) | Las métricas se convierten en formulario de registro. Es posible que esta opción no esté disponible en todos los tipos de recursos. Si se envían al almacén de registros de Azure Monitor (que se puede buscar mediante análisis de registros), podrá integrarlas en consultas, alertas y visualizaciones con datos de registro existentes.  
| [Cuenta de Almacenamiento de Azure](../../storage/blobs/index.yml) | Archivar los registros y las métricas en una cuenta de almacenamiento de Azure resulta útil para realizar auditorías, análisis estáticos o copias de seguridad. En comparación con los registros de Azure Monitor y las áreas de trabajo de Log Analytics, Azure Storage es más económico y los registros se pueden mantener indefinidamente.  | 
| [Event Hubs](../../event-hubs/index.yml) | El envío de registros y métricas a Event Hubs permite transmitir datos a sistemas externos, como SIEM de terceros y otras soluciones de análisis de registros.  |
| [Integraciones de asociados de Azure Monitor](../../partner-solutions/overview.md)| Integraciones especializadas entre Azure Monitor y otras plataformas de supervisión que no son de Microsoft. Resulta útil cuando ya se usa uno de los asociados.  |

## <a name="requirements-and-limitations"></a>Limitaciones y requisitos

### <a name="metrics-as-a-source"></a>Métricas como origen
Hay ciertas limitaciones a la hora de exportar métricas.

- **Actualmente no se admite el envío de métricas multidimensionales a través de la configuración de diagnóstico**: las métricas con dimensiones se exportan como métricas unidimensionales planas, agregadas entre valores de dimensión. *Por ejemplo*: La métrica "IOReadBytes" de una cadena de bloques puede consultarse y representarse individualmente en cada nodo. Sin embargo, cuando se exporta utilizando la configuración de diagnóstico, la métrica exportada muestra los bytes de lectura de todos los nodos.
- **No todas las métricas son exportables con la configuración de diagnóstico**: debido a limitaciones internas, no todas las métricas se pueden exportar a registros de Azure Monitor o  de Log Analytics. Para más información, consulte la columna exportable en la [lista de métricas admitidas](./metrics-supported.md)

Para solucionar estas limitaciones de métricas específicas, se recomienda extraerlas manualmente mediante la [API de REST de métricas](/rest/api/monitor/metrics/list) e importarlas a los registros de Azure Monitor con [la API de recopilador de datos de Azure Monitor](../logs/data-collector-api.md).

### <a name="activity-log-as-a-source"></a>Registro de actividad como origen

> [!IMPORTANT]
> Antes de crear una configuración de diagnóstico para el registro de actividad, debe deshabilitar cualquier configuración heredada. Para más información, consulte [Métodos de recopilación heredados](../essentials/activity-log.md#legacy-collection-methods).


### <a name="destination-limitations"></a>Limitaciones de destino

Se deben crear todos los destinos para la configuración de diagnóstico antes de crear la configuración de diagnóstico. El destino no tiene que estar en la misma suscripción que el recurso que envía los registros, siempre que el usuario que realice la configuración tenga el acceso de Azure RBAC adecuado a ambas suscripciones. Con Azure Lighthouse, también se puede enviar una configuración de diagnóstico a un área de trabajo de otro inquilino de Azure Active Directory. En la tabla siguiente se proporcionan los requisitos únicos para cada destino, incluidas las restricciones regionales.

| Destination | Requisitos |
|:---|:---|
| Área de trabajo de Log Analytics | No es necesario que el área de trabajo esté en la misma región que el recurso que se esté supervisando.|
| Cuenta de almacenamiento de Azure | No utilice una cuenta de almacenamiento existente que tenga otros datos sin supervisión almacenados en ella, para que pueda controlar mejor el acceso a los datos. Sin embargo, si va a archivar el registro de actividad y los registros de recurso, puede que tenga sentido utilizar esa misma cuenta de almacenamiento para mantener todos los datos de supervisión en una ubicación central.<br><br>Para enviar los datos al almacenamiento inmutable, establezca la directiva de inmutabilidad para la cuenta de almacenamiento tal como se escribe en [Establecimiento y administración de directivas de inmutabilidad para el almacenamiento de blobs](../../storage/blobs/immutable-policy-configure-version-scope.md). Debe seguir todos los pasos que aparecen en este artículo vinculado, incluida la habilitación de las escrituras de blobs en anexos protegidos.<br><br>La cuenta de almacenamiento debe estar en la misma región que el recurso que se esté supervisando, si el recurso es regional.|
| Event Hubs | La directiva de acceso compartido del espacio de nombres define los permisos que tiene el mecanismo de transmisión. Para transmitir a Event Hubs, se necesitan permisos de administración, envío y escucha. Para actualizar la configuración de diagnóstico para incluir la transmisión, debe tener el permiso ListKey sobre esa regla de autorización de Event Hubs.<br><br>El espacio de nombres del centro de eventos debe estar en la misma región que el recurso que se está supervisando, si este es regional. <br><br> La configuración de diagnósticos no puede tener acceso a los recursos de Event Hubs cuando están habilitadas las redes virtuales. Tiene que habilitar la opción de *Permitir que los servicios de confianza de Microsoft* puedan omitir este firewall en la instancia de Event Hubs para que Azure Monitor (configuración de diagnósticos) tenga acceso a los recursos de Event Hubs.|
| Integraciones de asociados | Varía según el asociado.  Consulte la [documentación de integraciones de asociados de Azure Monitor](../../partner-solutions/overview.md) para obtener más información.  

### <a name="azure-data-lake-storage-gen2-as-a-destination"></a>Azure Data Lake Storage Gen2 como destino

> [!NOTE]
> Actualmente, las cuentas de Azure Data Lake Storage Gen2 no se admiten como destino de la configuración de diagnóstico, aunque pueden aparecer como una opción válida en Azure Portal.

## <a name="create-in-azure-portal"></a>Creación en Azure Portal

Puede realizar configuraciones de diagnóstico en Azure Portal desde el menú de Azure Monitor o desde el menú del recurso.

1. El lugar de Azure Portal en el que se realiza la configuración de diagnóstico depende del recurso.

   - Si se trata de un único recurso, en el menú de este recurso, haga clic en la opción **Configuración de diagnóstico** de **Supervisar**.

        ![Captura de pantalla de la sección Monitoring (Supervisión) de un menú de recursos en Azure Portal con Diagnostics settings (Configuración de diagnóstico) resaltado.](media/diagnostic-settings/menu-resource.png)

   - Si se trata de uno o varios recursos, en el menú de Azure Monitor, haga clic en la opción **Configuración de diagnóstico** de **Configuración** y después en el recurso.

        ![Captura de pantalla de la sección Settings (Configuración) del menú de Azure Monitor con Diagnostics settings (Configuración de diagnóstico) resaltado.](media/diagnostic-settings/menu-monitor.png)

   - En el registro de actividad, haga clic en la opción **Registro de actividad** del menú **Azure Monitor** y después en **Configuración de diagnóstico**. No olvide deshabilitar cualquier configuración heredada del registro de actividad. Consulte [Deshabilitar la configuración existente](./activity-log.md#legacy-collection-methods) para más información.

        ![Captura de pantalla del menú de Azure Monitor con el registro de actividad seleccionado y Diagnostics settings (Configuración de diagnóstico) resaltado en la barra de menús de Monitor - Activity Log (Supervisar - Registro de actividad).](media/diagnostic-settings/menu-activity-log.png)

2. Si no existe ninguna configuración en el recurso que ha seleccionado, se le pide que cree una. Haga clic en **Agregar configuración de diagnóstico**.

   ![Agregar configuración de diagnóstico: sin configuración actual](media/diagnostic-settings/add-setting.png)

   Si ya hay una configuración en el recurso, verá una lista de opciones que ya están configuradas. Puede hacer clic en **Agregar configuración de diagnóstico** para agregar una nueva configuración o **Editar configuración** para modificar una existente. Cada configuración no puede tener más de uno de los tipos de destino.

   ![Agregar configuración de diagnóstico: configuración actual](media/diagnostic-settings/edit-setting.png)

3. Asigne un nombre a la configuración, si aún no lo tiene.

      :::image type="Add diagnostic setting" source="media/diagnostic-settings/setting-new-blank.png" alt-text="Nueva configuración de diagnóstico":::

4. **Registros y métricas para enrutar**: en el caso de los registros, elija un grupo de categorías o active las casillas individuales de cada categoría de datos que quiera enviar a los destinos especificados más adelante. La lista de categorías es diferente en cada servicio de Azure. Elija *allMetrics* si también desea almacenar métricas en registros de Azure Monitor. 

5. **Detalles del destino**: active la casilla de cada destino. Cuando se activa una casilla, aparecen opciones que le permiten agregar información adicional.

      ![Envío a Log Analytics o centros de eventos](media/diagnostic-settings/send-to-log-analytics-event-hubs.png)

    1. **Log Analytics**: especifique la suscripción y el área de trabajo.  Si no tiene ningún área de trabajo, tendrá que [crear una antes de continuar](../logs/quick-create-workspace.md).

    1. **Centro de eventos**: especifique los criterios siguientes:
       - La suscripción de la que forma parte el centro de eventos
       - El espacio de nombres del centro de eventos. Si aún no lo ha hecho, tendrá que [crear uno](../../event-hubs/event-hubs-create.md)
       - El nombre del centro de eventos (opcional) al que se van a enviar todos los datos. Si no especifica un nombre, se crea un centro de eventos para cada categoría de registro. Si va a enviar varias categorías, puede especificar un nombre para limitar el número de centros de eventos creados. Consulte [Cuotas y límites de Azure Event Hubs](../../event-hubs/event-hubs-quotas.md) para más detalles.
       - La directiva del centro de eventos (opcional), una directiva que defina los permisos que tiene el mecanismo de transmisión. Para más información, consulte este artículo sobre las [características de los centros de eventos](../../event-hubs/event-hubs-features.md#publisher-policy).

    1. **Storage**: elija una suscripción, una cuenta de almacenamiento y una directiva de retención.

        ![Enviar a Storage](media/diagnostic-settings/storage-settings-new.png)

        > [!TIP]
        > Considere la posibilidad de establecer la directiva de retención en 0 y usar la [directiva de ciclo de vida de Azure Storage](../../storage/blobs/lifecycle-management-policy-configure.md) o eliminar los datos del almacenamiento mediante un trabajo programado. Es probable que estas estrategias proporcionen un comportamiento más coherente. 
        >
        > En primer lugar, si está usando el almacenamiento para archivar, querrá conservar los datos durante más de 365 días. En segundo lugar, si elige una directiva de retención que sea mayor que 0, la fecha de expiración se asociará a los registros cuando se almacenen. No se puede cambiar la fecha de los registros una vez almacenados. Si establece la directiva de retención de *WorkflowRuntime* en 180 días y 24 horas después la establece en 365 días, los registros almacenados durante las primeras 24 horas se eliminarán automáticamente a los 180 días, mientras que todos los registros posteriores de ese mismo tipo se eliminarán automáticamente después de 365 días. Aunque posteriormente se modifique la directiva de retención, los registros que se almacenaron durante las 24 primeras horas no se conservarán 365 días.

     1. **Integración de asociados**: primero debe instalar una integración de asociados en la suscripción. Las opciones de configuración variarán según el asociado. Para obtener más información, vea [Integraciones de asociados de Azure Monitor](../../partner-solutions/overview.md). 
    
6. Haga clic en **Save**(Guardar).

Transcurridos unos instantes, la nueva configuración aparece en la lista de configuraciones para este recurso y los registros se transmiten a los destinos especificados en cuanto se generan nuevos datos de eventos. Pueden pasar hasta quince minutos desde que se emite un evento hasta que [aparece en un área de trabajo de Log Analytics](../logs/data-ingestion-time.md).

## <a name="create-using-powershell"></a>Creación mediante PowerShell

Use el cmdlet [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting) para crear una configuración de diagnóstico con [Azure PowerShell](../powershell-samples.md). Consulte la documentación de este cmdlet para las descripciones de sus parámetros.

> [!IMPORTANT]
> No puede usar este método con el registro de actividad de Azure. En su lugar, siga las indicaciones de [Creación de la configuración de diagnóstico en Azure Monitor con una plantilla de Resource Manager](./resource-manager-diagnostic-settings.md) para crear una plantilla de Resource Manager e implementarla con PowerShell.

A continuación se muestra un cmdlet de PowerShell de ejemplo para crear una configuración de diagnóstico con los tres destinos.

```powershell
Set-AzDiagnosticSetting -Name KeyVault-Diagnostics -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault -Category AuditEvent -MetricCategory AllMetrics -Enabled $true -StorageAccountId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount -WorkspaceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/myworkspace  -EventHubAuthorizationRuleId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```

## <a name="create-using-azure-cli"></a>Creación mediante la CLI de Azure

Use el comando [az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az_monitor_diagnostic_settings_create) para crear una configuración de diagnóstico con la [CLI de Azure](/cli/azure/monitor). Consulte la documentación de este comando para las descripciones de sus parámetros.

> [!IMPORTANT]
> No puede usar este método con el registro de actividad de Azure. En su lugar, siga las indicaciones de [Creación de la configuración de diagnóstico en Azure Monitor con una plantilla de Resource Manager](./resource-manager-diagnostic-settings.md) para crear una plantilla de Resource Manager e implementarla con la CLI.

A continuación se muestra un comando de la CLI de ejemplo para crear una configuración de diagnóstico con los tres destinos. La sintaxis es ligeramente diferente en función del cliente.

# <a name="cmd"></a>[CMD](#tab/CMD)
```azurecli
az monitor diagnostic-settings create  ^
--name KeyVault-Diagnostics ^
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault ^
--logs    "[{""category"": ""AuditEvent"",""enabled"": true}]" ^
--metrics "[{""category"": ""AllMetrics"",""enabled"": true}]" ^
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount ^
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace ^
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="powershell"></a>[PowerShell](#tab/PowerShell)
```azurecli
az monitor diagnostic-settings create  `
--name KeyVault-Diagnostics `
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault `
--logs    '[{""category"": ""AuditEvent"",""enabled"": true}]' `
--metrics '[{""category"": ""AllMetrics"",""enabled"": true}]' `
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount `
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace `
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
# <a name="bash"></a>[Bash](#tab/Bash)
```azurecli
az monitor diagnostic-settings create  \
--name KeyVault-Diagnostics \
--resource /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.KeyVault/vaults/mykeyvault \
--logs    '[{"category": "AuditEvent","enabled": true}]' \
--metrics '[{"category": "AllMetrics","enabled": true}]' \
--storage-account /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount \
--workspace /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/microsoft.operationalinsights/workspaces/myworkspace \
--event-hub-rule /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myresourcegroup/providers/Microsoft.EventHub/namespaces/myeventhub/authorizationrules/RootManageSharedAccessKey
```
---

## <a name="create-using-resource-manager-template"></a>Creación mediante una plantilla de Resource Manager
Consulte [Ejemplos de plantillas de Resource Manager para la configuración de diagnóstico en Azure Monitor](./resource-manager-diagnostic-settings.md) para crear o actualizar la configuración de diagnóstico con una plantilla de Resource Manager.

## <a name="create-using-rest-api"></a>Creación mediante la API REST
Consulte [Configuración de diagnóstico](/rest/api/monitor/diagnosticsettings) para crear o actualizar configuraciones de diagnóstico mediante la [API REST de Azure Monitor](/rest/api/monitor/).

## <a name="create-at-scale-using-azure-policy"></a>Creación a gran escala mediante Azure Policy

Dado que es necesario crear una configuración de diagnóstico para cada recurso de Azure, se puede usar Azure Policy para crear automáticamente una configuración de diagnóstico a medida que se crea cada recurso. Cada tipo de recurso de Azure tiene un conjunto único de categorías que se deben enumerar en la configuración de diagnóstico. Debido a esto, cada tipo de recurso requiere una definición de directiva independiente. Algunos tipos de recursos tienen definiciones de directivas integradas que se pueden asignar sin modificaciones. Para otros tipos de recursos, debe crear una definición personalizada. 

Con la adición de grupos de categorías de registro de recursos, ahora puede elegir opciones que se actualicen de manera dinámica a medida que cambian las categorías de registro.  Para obtener más información, vea [orígenes de configuración de diagnóstico](#sources) enumerados anteriormente en este artículo. 

### <a name="built-in-policy-definitions-for-azure-monitor"></a>Definiciones de directivas integradas para Azure Monitor
Hay dos definiciones de directivas integradas para cada tipo de recurso: una para enviar datos a un área de trabajo de Log Analytics y otra a un centro de eventos. Si solo necesita una ubicación, asigne esa directiva al tipo de recurso. Si necesita ambas, asigne ambas definiciones de directiva al recurso.

Por ejemplo, la siguiente imagen muestra las definiciones de directiva de configuración de diagnóstico integradas para Azure Data Lake Analytics.

![Captura de pantalla parcial de la página Definiciones de Azure Policy, que muestra dos definiciones de directivas de configuración de diagnóstico integradas para Data Lake Analytics.](media/diagnostic-settings/builtin-diagnostic-settings.png)

### <a name="custom-policy-definitions"></a>Definiciones de directivas personalizadas
En el caso de los tipos de recursos que no tienen una directiva integrada, debe crear una definición de directiva personalizada. Puede hacerlo manualmente en Azure Portal mediante la copia de una directiva integrada existente y, a continuación, su modificación para el tipo de recurso. No obstante, es más eficaz crear la directiva mediante programación con un script de la Galería de PowerShell.

El script [Create-AzDiagPolicy](https://www.powershellgallery.com/packages/Create-AzDiagPolicy) crea archivos de directivas para un tipo de recurso determinado que se pueden instalar mediante PowerShell o la CLI de Azure. Use el procedimiento siguiente para crear una definición de directiva personalizada para la configuración de diagnóstico:

1. Asegúrese de que tiene instalado [Azure PowerShell](/powershell/azure/install-az-ps).
2. Para instalar el script, use el siguiente comando:
  
    ```azurepowershell
    Install-Script -Name Create-AzDiagPolicy
    ```

3. Ejecute el script con los parámetros para especificar dónde enviar los registros. Se le pedirá que especifique una suscripción y un tipo de recurso. 

   Por ejemplo, para crear una definición de directiva que envíe registros a un área de trabajo de Log Analytics y un centro de eventos, use el comando siguiente.

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

   Como alternativa, puede especificar una suscripción y un tipo de recurso en el comando. Por ejemplo, para crear una definición de directiva que envíe registros a un área de trabajo de Log Analytics y a un centro de eventos para bases de datos de Azure SQL Server, use el comando siguiente:

   ```azurepowershell
   Create-AzDiagPolicy.ps1 -SubscriptionID xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -ResourceType Microsoft.Sql/servers/databases  -ExportLA -ExportEH -ExportDir ".\PolicyFiles"  
   ```

5. El script crea carpetas independientes para cada definición de directiva. Cada carpeta contiene tres archivos denominados *azurepolicy.json*, *azurepolicy.rules.json* y *azurepolicy.parameters.json*. Si quiere crear la directiva manualmente en Azure Portal, puede copiar y pegar el contenido del archivo *azurepolicy.json*, ya que incluye la definición de directiva completa. Use los otros dos archivos con PowerShell o la CLI de Azure para crear la definición de directiva desde la línea de comandos.

   En los siguientes ejemplos se muestra cómo instalar la definición de directiva desde PowerShell y la CLI de Azure. Cada ejemplo incluye metadatos para especificar la categoría **Supervisión** para agrupar la nueva definición de directiva con las definiciones de directivas integradas.

   ```azurepowershell
   New-AzPolicyDefinition -name "Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace" -policy .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json -parameter .\Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json -mode All -Metadata '{"category":"Monitoring"}'
   ```

   ```azurecli
   az policy definition create --name 'deploy-diag-setting-sql-database--workspace' --display-name 'Deploy Diagnostic Settings for SQL Server database to Log Analytics workspace'  --rules 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.rules.json' --params 'Apply-Diag-Settings-LA-Microsoft.Sql-servers-databases\azurepolicy.parameters.json' --subscription 'AzureMonitor_Docs' --mode All
   ```

### <a name="initiative"></a>Iniciativa
En lugar de crear una asignación para cada definición de directiva, una estrategia habitual consiste en crear una iniciativa que incluya las definiciones de directivas para crear la configuración de diagnóstico para cada servicio de Azure. Cree una asignación entre la iniciativa y un grupo de administración, una suscripción o un grupo de recursos, en función de cómo administre el entorno. Esta estrategia ofrece las siguientes ventajas:

- Cree una única asignación para la iniciativa en lugar de varias asignaciones para cada tipo de recurso. Use la misma iniciativa para varios grupos de supervisión, suscripciones o grupos de recursos.
- Modifique la iniciativa cuando necesite agregar un nuevo tipo de recurso o destino. Por ejemplo, los requisitos iniciales pueden enviar datos solo a un área de trabajo de Log Analytics, pero más adelante puede que quiera agregar un centro de eventos. Modifique la iniciativa en lugar de crear nuevas asignaciones.

Para obtener más información sobre la creación de una iniciativa, consulte [Creación y asignación de una definición de iniciativa](../../governance/policy/tutorials/create-and-manage.md#create-and-assign-an-initiative-definition). Tenga en cuenta las recomendaciones siguientes:

- Establezca **Categoría** en **Supervisión** para agruparla con las definiciones de directivas integradas y personalizadas relacionadas.
- En lugar de especificar los detalles del área de trabajo de Log Analytics y el centro de eventos para las definiciones de directiva que se incluyen en la iniciativa, use un parámetro de iniciativa común. Este parámetro le permite especificar fácilmente un valor común para todas las definiciones de directivas y cambiar ese valor si es necesario.

![Captura de pantalla que muestra la configuración de la definición de iniciativa.](media/diagnostic-settings/initiative-definition.png)

### <a name="assignment"></a>Asignación 
Asigne la iniciativa a un grupo de administración, una suscripción o un grupo de recursos de Azure en función del ámbito de los recursos que se van a supervisar. Un [grupo de administración](../../governance/management-groups/overview.md) es útil para dar un ámbito a la directiva, especialmente si la organización tiene varias suscripciones.

![Captura de pantalla de la configuración de la pestaña Aspectos básicos de la sección Asignar iniciativa de la Configuración de diagnóstico en el área de trabajo de Log Analytics en Azure Portal.](media/diagnostic-settings/initiative-assignment.png)

Mediante el uso de parámetros de iniciativa, puede especificar el área de trabajo o cualquier otro detalle para todas las definiciones de directivas de la iniciativa. 

![Captura de pantalla que muestra los parámetros de la iniciativa en la pestaña Parámetros.](media/diagnostic-settings/initiative-parameters.png)

### <a name="remediation"></a>Corrección
La iniciativa se aplicará a cada máquina virtual a medida que esta se cree. Una [tarea de corrección](../../governance/policy/how-to/remediate-resources.md) implementa las definiciones de directivas de la iniciativa en los recursos existentes, de modo que puede crear una configuración de diagnóstico para los recursos que ya se han creado.

Al crear la asignación mediante Azure Portal, tiene la opción de crear una tarea de corrección al mismo tiempo. Consulte [Corrección de los recursos no compatibles con Azure Policy](../../governance/policy/how-to/remediate-resources.md) para más información sobre la corrección.

![Captura de pantalla que muestra la corrección de iniciativas para un área de trabajo de Log Analytics.](media/diagnostic-settings/initiative-remediation.png)

## <a name="troubleshooting"></a>Solución de problemas

### <a name="metric-category-is-not-supported"></a>La categoría de métrica no es compatible

Al implementar una configuración de diagnóstico, recibe un mensaje de error similar a *La categoría de métrica "xxxx" no se admite*. Puede recibir este error aunque una implementación anterior se haya completado correctamente. 

El problema se produce al usar una plantilla de Resource Manager, la API de REST de configuración de diagnóstico, la CLI de Azure o Azure PowerShell. La configuración de diagnóstico creada a través de Azure Portal no se ve afectada, ya que solo se presentan los nombres de categoría admitidos.

El problema se debe a un cambio reciente en la API subyacente. Las categorías de métricas que no sean "AllMetrics" no se admiten y nunca se han admitido, salvo para unos servicios de Azure específicos. En el pasado, se omitieron otros nombres de categoría al implementar una configuración de diagnóstico. El back-end de Azure Monitor simplemente redirigió estas categorías a "AllMetrics".  A partir de febrero de 2021, el back-end se actualizó para confirmar específicamente que la categoría de métrica proporcionada es precisa. Este cambio ha provocado un error en algunas implementaciones.

Si recibe este error, actualice las implementaciones para reemplazar los nombres de categoría de métrica por "AllMetrics" para corregir el problema. Si la implementación agregaba anteriormente varias categorías, solo debe conservarse una con la referencia "AllMetrics". Si sigue teniendo el problema, póngase en contacto con el equipo de soporte técnico de Azure a través de Azure Portal. 

### <a name="setting-disappears-due-to-non-ascii-characters-in-resourceid"></a>La configuración desaparece debido a caracteres no ASCII en resourceID

La configuración de diagnóstico no admite identificadores de recursos con caracteres no ASCII (por ejemplo, Preproducción). Puesto que no puede cambiar el nombre de los recursos en Azure, la única opción es crear un recurso sin caracteres no ASCII. Si los caracteres están en un grupo de recursos, puede mover los recursos que contiene a uno nuevo. De lo contrario, deberá volver a crear el recurso.

## <a name="next-steps"></a>Pasos siguientes

- [Más información sobre los registros de plataforma de Azure](./platform-logs-overview.md)