---
title: Solución de administración de Office 365 en Azure
description: Este artículo proporciona detalles sobre la configuración y el uso de la solución Office 365 en Azure.  Incluye una descripción detallada de los registros de Office 365 creados en Azure Monitor.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/30/2020
ms.openlocfilehash: 674e8036463a317d6e578750bc7ed3802ae776e4
ms.sourcegitcommit: 2d412ea97cad0a2f66c434794429ea80da9d65aa
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/14/2021
ms.locfileid: "122181649"
---
# <a name="office-365-management-solution-in-azure-preview"></a>Solución de administración de Office 365 en Azure (versión preliminar)

![Logotipo de Office 365](media/solution-office-365/icon.png)

> [!IMPORTANT]
> ## <a name="solution-update"></a>Actualización de la solución
> Esta solución se ha reemplazado por la solución de disponibilidad general [Office 365](../../sentinel/connect-office-365.md) en [Azure Sentinel](../../sentinel/overview.md) y la [solución de supervisión e informes de Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). De forma conjunta, proporcionan una versión actualizada de la solución Office 365 de Azure Monitor anterior con una experiencia de configuración mejorada. Puede seguir usando la solución existente hasta el 31 de octubre de 2020.
> 
> Azure Sentinel es una solución de administración de eventos e información de seguridad nativa en la nube que ingiere registros y proporciona funcionalidad de SIEM adicional, como detecciones, investigaciones, búsqueda e información controlada por aprendizaje automático. El uso de Azure Sentinel ahora le proporcionará ingesta de registros de administración de Exchange y de actividad de Office 365 SharePoint.
> 
> Los informes de Azure AD proporcionan una vista de registros más completa de la actividad de Azure AD en el entorno, lo que incluye eventos de inicio de sesión, eventos de auditoría y cambios en el directorio. Para conectarse a los registros de Azure AD, puede usar el [conector de Azure AD de Azure Sentinel](../../sentinel/connect-azure-active-directory.md) o configurar la [integración de registros de Azure AD con Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md). 
>
> La colección de registro de Azure AD está sujeta a los precios de Azure Monitor.  Para más información, vea [Precios de Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/).
>
> Para usar la solución Office 365 de Azure Sentinel:
> 1. El uso del conector de Office 365 en Azure Sentinel afecta a los precios del área de trabajo. Para más información, vea [Precios de Azure Sentinel](https://azure.microsoft.com/pricing/details/azure-sentinel/).
> 2. Si ya usa la solución Office 365 de Azure Monitor, primero tendrá que desinstalarla mediante el script de la [sección de desinstalación siguiente](#uninstall).
> 3. [Habilite la solución Azure Sentinel](../../sentinel/quickstart-onboard.md) en el área de trabajo.
> 4. Vaya a la página **Conectores de datos** de Azure Sentinel y habilite el conector **Office 365**.
>
> ## <a name="frequently-asked-questions"></a>Preguntas más frecuentes
> 
> ### <a name="q-is-it-possible-to-on-board-the-office-365-azure-monitor-solution-between-now-and-october-31"></a>P: ¿Es posible incorporar la solución Office 365 de Azure Monitor entre ahora y el 31 de octubre?
> No, los scripts de incorporación de la solución Office 365 de Azure Monitor ya no están disponibles. La solución se eliminará el 31 de octubre.
> 
> ### <a name="q-will-the-tables-and-schemas-be-changed"></a>P: ¿Se modificarán las tablas y los esquemas?
> El nombre y el esquema de la tabla **OfficeActivity** seguirán siendo los mismos que en la solución actual. Puede seguir usando las mismas consultas en la nueva solución excluyendo las consultas que hacen referencia a datos de Azure AD.
> 
> Los registros de la nueva [solución de supervisión e informes de Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md) se inscribirán en las tablas [SigninLogs](../../active-directory/reports-monitoring/concept-sign-ins.md) y [AuditLogs](../../active-directory/reports-monitoring/concept-audit-logs.md) en lugar de **OfficeActivity**. Para más información, vea [Procedimientos para analizar los registros de Azure AD](../../active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics.md), que también es relevante para los usuarios de Azure Sentinel y Azure Monitor.
> 
> Estos son algunos ejemplos para convertir consultas de **OfficeActivity** a **SigninLogs**:
> 
> **Consulta de inicios de sesión con error, por usuario:**
> 
> ```Kusto
> OfficeActivity
> | where TimeGenerated >= ago(1d) 
> | where OfficeWorkload == "AzureActiveDirectory"                      
> | where Operation == 'UserLoginFailed'
> | summarize count() by UserId    
> ```
> 
> ```Kusto
> SigninLogs
> | where ConditionalAccessStatus == "failure" or ConditionalAccessStatus == "notApplied"
> | summarize count() by UserDisplayName
> ```
> 
> **Visualización de operaciones de Azure AD:**
> 
> ```Kusto
> OfficeActivity
> | where OfficeWorkload =~ "AzureActiveDirectory"
> | sort by TimeGenerated desc
> | summarize AggregatedValue = count() by Operation
> ```
> 
> ```Kusto
> AuditLogs
> | summarize count() by OperationName
> ```
> 
> ### <a name="q-how-can-i-on-board-azure-sentinel"></a>P: ¿Cómo puedo incorporar Azure Sentinel?
> Azure Sentinel es una solución que puede habilitar en un área de trabajo de Log Analytics nueva o existente. Para más información, vea [Documentación sobre la incorporación de Azure Sentinel](../../sentinel/quickstart-onboard.md).
>
> ### <a name="q-do-i-need-azure-sentinel-to-connect-the-azure-ad-logs"></a>P: ¿Necesito Azure Sentinel para conectar los registros de Azure AD?
> Puede configurar la [integración de registros de Azure AD con Azure Monitor](../../active-directory/reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md), que no está relacionado con la solución de Azure Sentinel. Azure Sentinel proporciona un conector nativo y contenido de serie para los registros de Azure AD. Para más información, vea la siguiente pregunta sobre el contenido de serie orientado a la seguridad.
>
> ###    <a name="q-what-are-the-differences-when-connecting-azure-ad-logs-from-azure-sentinel-and-azure-monitor"></a>P: ¿Cuáles son las diferencias al conectar registros de Azure AD desde Azure Sentinel y Azure Monitor?
> Azure Sentinel y Azure Monitor se conectan a los registros de Azure AD en función de la misma [solución de informes y supervisión de Azure AD](../../active-directory/reports-monitoring/plan-monitoring-and-reporting.md). Azure Sentinel proporciona un conector nativo de un solo clic que conecta los mismos datos y proporciona información de supervisión.
>
> ###    <a name="q-what-do-i-need-to-change-when-moving-to-the-new-azure-ad-reporting-and-monitoring-tables"></a>P: ¿Qué debo cambiar al pasar a las nuevas tablas de informes y supervisión de Azure AD?
> Todas las consultas que usen datos de Azure AD, incluidas las consultas de alertas, paneles y cualquier contenido que se haya creado con datos de Azure AD de Office 365, se deben volver a crear con las nuevas tablas.
>
> Azure Sentinel y Azure AD proporcionan contenido integrado que puede usar al pasar a la solución de informes y supervisión de Azure AD. Para más información, vea la siguiente pregunta sobre contenido de serie orientado a la seguridad y [Procedimiento para usar libros de Azure Monitor para informes de Azure Active Directory](../../active-directory/reports-monitoring/howto-use-azure-monitor-workbooks.md). 
>
> ### <a name="q-how-i-can-use-the-azure-sentinel-out-of-the-box-security-oriented-content"></a>P: ¿Cómo puedo usar el contenido de serie orientado a la seguridad de Azure Sentinel?
> Azure Sentinel proporciona paneles de serie orientados a la seguridad, consultas de alertas personalizadas, consultas de búsqueda, investigación y funcionalidades de respuesta automatizadas basadas en los registros de Office 365 y Azure AD. Explore Azure Sentinel en GitHub y los tutoriales de Azure Sentinel para obtener más información:
>
> - [Detección de amenazas integrada](/azure/azure-monitor/insights/articles/sentinel/detect-threats-built-in.md)
> - [Creación de reglas de análisis personalizadas para detectar amenazas sospechosas](/azure/azure-monitor/insights/articles/sentinel/detect-threats-custom.md)
> - [Supervisión de los datos](/azure/azure-monitor/insights/articles/sentinel/monitor-your-data.md)
> - [Investigación de incidentes con Azure Sentinel](/azure/azure-monitor/insights/articles/sentinel/investigate-cases.md)
> - [Configuración de respuestas automatizadas frente a amenazas en Azure Sentinel](../../sentinel/tutorial-respond-threats-playbook.md)
> - [Comunidad de Azure Sentinel en GitHub](https://github.com/Azure/Azure-Sentinel/tree/master/Playbooks)
> 
> ### <a name="q-does-azure-sentinel-provide-additional-connectors-as-part-of-the-solution"></a>P: ¿Azure Sentinel proporciona conectores adicionales como parte de la solución?
> Sí, vea [Conexión con orígenes de datos de Azure Sentinel](../../sentinel/connect-data-sources.md).
> 
> ###    <a name="q-what-will-happen-on-october-31-do-i-need-to-offboard-beforehand"></a>P: ¿Qué ocurrirá el 31 de octubre? ¿Tengo que realizar la retirada con antelación?
> 
> - No podrá recibir datos de la solución **Office365**. La solución se quitará del área de trabajo y dejará de estar disponible en el marketplace.
> - Para los clientes de Azure Sentinel, la solución **Office365** del área de trabajo de Log Analytics se incluirá en la solución **SecurityInsights** de Azure Sentinel.
> - Si no retira la solución manualmente antes del 31 de octubre, los datos se desconectarán automáticamente, y se quitará la tabla **OfficeActivity**. Aún así, podrá restaurar la tabla cuando habilite el conector de Office 365 en Azure Sentinel, como se explica a continuación.
> 
> ### <a name="q-will-my-data-transfer-to-the-new-solution"></a>P: ¿Mis datos se transferirán a la nueva solución?
> Sí. Cuando quite la solución **Office 365** del área de trabajo, los datos dejarán de estar disponibles temporalmente porque el esquema se ha quitado. Al habilitar el nuevo conector de **Office 365** en Azure Sentinel, el esquema se restaura en el área de trabajo y los datos ya recopilados estarán disponibles. 
 

La solución de administración de Office 365 permite supervisar el entorno de Office 365 en Azure Monitor.

- Supervise las actividades de usuario en las cuentas de Office 365 para analizar patrones de uso e identificación de tendencias de comportamiento. Por ejemplo, puede extraer escenarios de uso específicos, como los archivos que se comparten fuera de la organización o los sitios de SharePoint más populares.
- Supervise las actividades del administrador para realizar el seguimiento de cambios de configuración u operaciones de privilegios elevados.
- Detecte e investigue comportamientos de usuario no deseados, que puede personalizar para las necesidades de la organización.
- Demuestre el cumplimiento de las normas y las auditorías. Por ejemplo, puede supervisar las operaciones de acceso a archivos en los archivos confidenciales, lo que pueden ayudarle con el proceso de cumplimiento y auditoría.
- Solucione problemas operativos mediante [consultas de registros](../logs/log-query-overview.md) en los datos de actividad de Office 365 de su organización.


## <a name="uninstall"></a>Desinstalación

Puede quitar la solución de administración de Office 365 mediante el proceso de [Quitar una solución de administración](solutions.md#remove-a-monitoring-solution). Esta acción no detendrá la recopilación de datos de Office 365 en Azure Monitor. Realice el procedimiento siguiente para cancelar la suscripción a Office 365 y detener la recopilación de datos.

1. Guarde el script siguiente como *office365_unsubscribe.ps1*.

    ```powershell
    param (
        [Parameter(Mandatory=$True)][string]$WorkspaceName,
        [Parameter(Mandatory=$True)][string]$ResourceGroupName,
        [Parameter(Mandatory=$True)][string]$SubscriptionId,
        [Parameter(Mandatory=$True)][string]$OfficeTennantId
    )
    $line='#-------------------------------------------------------------------------------------------------------------------------------------------------------------------------'
    
    $line
    IF ($Subscription -eq $null)
        {Login-AzAccount -ErrorAction Stop}
    $Subscription = (Select-AzSubscription -SubscriptionId $($SubscriptionId) -ErrorAction Stop)
    $Subscription
    $option = [System.StringSplitOptions]::RemoveEmptyEntries 
    $Workspace = (Set-AzOperationalInsightsWorkspace -Name $($WorkspaceName) -ResourceGroupName $($ResourceGroupName) -ErrorAction Stop)
    $Workspace
    $WorkspaceLocation= $Workspace.Location
    
    # Client ID for Azure PowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2"
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    $domain='login.microsoftonline.com'
    $adTenant =  $Subscription[0].Tenant.Id
    $authority = "https://login.windows.net/$adTenant";
    $ARMResource ="https://management.azure.com/";
    $xms_client_tenant_Id ='55b65fb5-b825-43b5-8972-c8b6875867c1'
    
    switch ($WorkspaceLocation) {
           "USGov Virginia" { 
                             $domain='login.microsoftonline.us';
                              $authority = "https://login.microsoftonline.us/$adTenant";
                              $ARMResource ="https://management.usgovcloudapi.net/"; break} # US Gov Virginia
           default {
                    $domain='login.microsoftonline.com'; 
                    $authority = "https://login.windows.net/$adTenant";
                    $ARMResource ="https://management.azure.com/";break} 
                    }
    
    Function RESTAPI-Auth { 
    
    $global:SubscriptionID = $Subscription.SubscriptionId
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURIARM=$ARMResource;
    # Authenticate and Acquire Token 
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $global:authResultARM = $authContext.AcquireTokenAsync($resourceAppIdURIARM, $clientId, $redirectUri, $platformParameters)
    $global:authResultARM.Wait()
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $authHeader
    }
    
    Function Office-UnSubscribe-Call{
    
    #----------------------------------------------------------------------------------------------------------------------------------------------
    $authHeader = $global:authResultARM.Result.CreateAuthorizationHeader()
    $ResourceName = "https://manage.office.com"
    $SubscriptionId   = $Subscription[0].Subscription.Id
    $OfficeAPIUrl = $ARMResource + 'subscriptions/' + $SubscriptionId + '/resourceGroups/' + $ResourceGroupName + '/providers/Microsoft.OperationalInsights/workspaces/' + $WorkspaceName + '/datasources/office365datasources_'  + $SubscriptionId + $OfficeTennantId + '?api-version=2015-11-01-preview'
    
    $Officeparams = @{
        ContentType = 'application/json'
        Headers = @{
        'Authorization'="$($authHeader)"
        'x-ms-client-tenant-id'=$xms_client_tenant_Id
        'Content-Type' = 'application/json'
        }
        Method = 'Delete'
        URI = $OfficeAPIUrl
      }
    
    $officeresponse = Invoke-WebRequest @Officeparams 
    $officeresponse
    
    }
    
    #GetDetails 
    RESTAPI-Auth -ErrorAction Stop
    Office-UnSubscribe-Call -ErrorAction Stop
    ```

2. Ejecute el script con el siguiente comando:

    ```
    .\office365_unsubscribe.ps1 -WorkspaceName <Log Analytics workspace name> -ResourceGroupName <Resource Group name> -SubscriptionId <Subscription ID> -OfficeTennantID <Tenant ID> 
    ```

    Ejemplo:

    ```powershell
    .\office365_unsubscribe.ps1 -WorkspaceName MyWorkspace -ResourceGroupName MyResourceGroup -SubscriptionId '60b79d74-f4e4-4867-b631-yyyyyyyyyyyy' -OfficeTennantID 'ce4464f8-a172-4dcf-b675-xxxxxxxxxxxx'
    ```

Se le pedirán las credenciales. Proporcione las credenciales para el área de trabajo de Log Analytics.

## <a name="data-collection"></a>datos, recopilación

Inicialmente, la recopilación de datos puede tardar unas horas. Cuando comienza la recopilación, Office 365 envía una [notificación de webhook](/office/office-365-management-api/office-365-management-activity-api-reference#receiving-notifications) con datos detallados a Azure Monitor cada vez que se crea un registro. Este registro está disponible en Azure Monitor al cabo de unos minutos de su recepción.

## <a name="using-the-solution"></a>Uso de la solución

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

Al agregar la solución Office 365 al área de trabajo de Log Analytics, se agrega el icono **Office 365** al panel. Este icono muestra un recuento y una representación gráfica del número de equipos en el entorno y del cumplimiento de las actualizaciones.<br><br>
![Icono de resumen de Office 365](media/solution-office-365/tile.png)  

Haga clic en el icono de **Office 365** para abrir el panel de **Office 365**.

![Panel de Office 365](media/solution-office-365/dashboard.png)  

El panel incluye las columnas de la tabla siguiente. Cada columna muestra las diez principales alertas por recuento que coinciden con los criterios de esa columna para el ámbito e intervalo de tiempo especificados. Puede ejecutar una búsqueda de registros que proporcione toda la lista haciendo clic en Ver todo en la parte inferior de la columna o haciendo clic en el encabezado de columna.

| Columna | Descripción |
|:--|:--|
| Operaciones | Proporciona información acerca de los usuarios activos de todas las suscripciones de Office 365 supervisadas. También podrá ver el número de actividades que se producen con el tiempo.
| Exchange | Muestra el desglose de las actividades del servidor de Exchange, como Add-Mailbox Permission o Set-Mailbox. |
| SharePoint | Muestra las actividades principales que realizan los usuarios en documentos de SharePoint. Cuando obtiene los detalles de este icono, la página de búsqueda muestra los detalles de estas actividades, como el documento de destino y la ubicación de esta actividad. Por ejemplo, para un evento de acceso a archivos, podrá ver el documento al que se tiene acceso, el nombre de la cuenta asociada y la dirección IP. |
| Azure Active Directory | Incluye las actividades principales de los usuarios, como restablecer la contraseña de usuario y los intentos de inicio de sesión. Cuando se profundiza, podrá ver los detalles de estas actividades, como el estado del resultado. Esto es especialmente útil si desea supervisar las actividades sospechosas en Azure Active Directory. |




## <a name="azure-monitor-log-records"></a>Registros de Azure Monitor

El valor de **Tipo** es **OfficeActivity** para todos los registros creados en el área de trabajo de Log Analytics en Azure Monitor por la solución de Office 365.  La propiedad **OfficeWorkload** determina a qué servicio de Office 365 hace referencia el registro: Exchange, AzureActiveDirectory, SharePoint o OneDrive.  La propiedad **RecordType** especifica el tipo de operación.  Las propiedades varían para cada tipo de operación y se muestran en las tablas siguientes.

### <a name="common-properties"></a>Propiedades comunes

Las siguientes propiedades son comunes a todos los registros de Office 365.

| Propiedad | Descripción |
|:--- |:--- |
| Tipo | *OfficeActivity* |
| ClientIP | La dirección IP del dispositivo que se usó cuando se registró la actividad. La dirección IP se muestra en formato de dirección IPv4 o IPv6. |
| OfficeWorkload | Servicio de Office 365 al que hace referencia el registro.<br><br>AzureActiveDirectory<br>Exchange<br>SharePoint|
| Operación | El nombre de la actividad de usuario o administrador.  |
| OrganizationId | El identificador único GUID del inquilino de Office 365 de su organización. Este valor siempre será el mismo para su organización, con independencia del servicio de Office 365 en el que se produce. |
| RecordType | Tipo de operación realizada. |
| ResultStatus | Indica si la acción (especificada en la propiedad Operation) se realizó correctamente o no. Los valores posibles son Succeeded (correcta), PartiallySucceeded (parcialmente correcta) o Failed (con errores). Para la actividad de administración de Exchange, el valor es True o False. |
| UserId | El UPN (nombre principal de usuario) del usuario que realizó la acción que generó el registro, por ejemplo, my_name@my_domain_name. Tenga en cuenta que también se incluyen los registros de actividad realizada por cuentas del sistema (como SHAREPOINT\system o NTAUTHORITY\SYSTEM). | 
| UserKey | Un identificador alternativo para el usuario identificado en la propiedad UserId.  Por ejemplo, esta propiedad se rellena con el identificador único de Passport (PUID) para los eventos producidos por los usuarios de SharePoint, OneDrive para la empresa y Exchange. Esta propiedad también puede especificar el mismo valor que la propiedad UserID para los eventos que se producen en otros servicios y los eventos producidos por las cuentas del sistema|
| UserType | El tipo de usuario que realizó la operación.<br><br>Administración<br>Application<br>DcAdmin<br>Normal<br>Reserved<br>ServicePrincipal<br>Sistema |


### <a name="azure-active-directory-base"></a>Azure Active Directory

Las siguientes propiedades son comunes a todos los registros de Azure Active Directory.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AzureActiveDirectory_EventType | El tipo de evento de Azure AD. |
| ExtendedProperties | Las propiedades extendidas del evento de Azure AD. |


### <a name="azure-active-directory-account-logon"></a>Inicio de sesión de cuenta de Azure Active Directory

Estos registros se crean cuando un usuario de Active Directory intenta iniciar sesión.

| Propiedad | Descripción |
|:--- |:--- |
| `OfficeWorkload` | AzureActiveDirectory |
| `RecordType`     | AzureActiveDirectoryAccountLogon |
| `Application` | La aplicación que desencadena el evento de inicio de sesión en la cuenta, como Office 15. |
| `Client` | Detalles sobre dispositivo, sistema operativo del dispositivo y explorador del dispositivo que usó el cliente para el evento de inicio de sesión en la cuenta. |
| `LoginStatus` | Esta propiedad viene directamente de OrgIdLogon.LoginStatus. Los algoritmos de alertas podrían realizar la asignación de distintos errores de inicio de sesión interesantes. |
| `UserDomain` | La información de identidad del inquilino (TII). | 


### <a name="azure-active-directory"></a>Azure Active Directory

Estos registros se crean cuando se realizan cambios o adiciones en objetos de Active Directory de Azure.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | AzureActiveDirectory |
| RecordType     | AzureActiveDirectory |
| AADTarget | El usuario sobre el que se realizó la acción (identificada por la propiedad Operation). |
| Actor | El usuario o la entidad de servicio que realizó la acción. |
| ActorContextId | El identificador GUID de la organización al que pertenece el actor. |
| ActorIpAddress | Dirección IP del actor en formato de dirección IPV4 o IPV6. |
| InterSystemsId | El identificador GUID que realiza el seguimiento de las acciones en los componentes del servicio Office 365. |
| IntraSystemId |     El identificador GUID generado por Azure Active Directory para realizar el seguimiento de la acción. |
| SupportTicketId | El identificador del vale de soporte técnico para la acción en situaciones de "actuar en nombre de". |
| TargetContextId | El identificador GUID de la organización a la que pertenece el usuario de destino. |


### <a name="data-center-security"></a>Data Center Security

Estos registros se crean a partir de los datos de auditoría de Data Center Security.  

| Propiedad | Descripción |
|:--- |:--- |
| EffectiveOrganization | El nombre del inquilino al que estaba destinada la elevación o el cmdlet. |
| ElevationApprovedTime | La marca de tiempo en el momento de la aprobación de la elevación. |
| ElevationApprover | El nombre de un administrador de Microsoft. |
| ElevationDuration | La duración en activo de la elevación. |
| ElevationRequestId |     Un identificador único para la solicitud de elevación. |
| ElevationRole | El rol para el que se solicitó la elevación. |
| ElevationTime | La hora de inicio de la elevación. |
| Start_Time | La hora de inicio de la ejecución del cmdlet. |


### <a name="exchange-admin"></a>Administración de Exchange

Estos registros se crean cuando se realizan cambios en la configuración de Exchange.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeAdmin |
| ExternalAccess |     Especifica si el cmdlet se ha ejecutado por un usuario de la organización, por personal del centro de datos de Microsoft o una cuenta de servicio del centro de datos o por un administrador delegado. El valor False indica que el cmdlet se ejecutó por alguien de su organización. El valor True indica que el cmdlet lo ejecutó personal del centro de datos, una cuenta de servicio del centro de datos o un administrador delegado. |
| ModifiedObjectResolvedName |     Este es el nombre descriptivo del objeto que ha sido modificado por el cmdlet. Esto se registra únicamente si el cmdlet modifica el objeto. |
| OrganizationName | El nombre del inquilino. |
| OriginatingServer | El nombre del servidor desde el que se ejecutó el cmdlet. |
| Parámetros | El nombre y valor de todos los parámetros que se utilizaron con el cmdlet que se identifica en la propiedad Operations. |


### <a name="exchange-mailbox"></a>Buzón de Exchange

Estos registros se crean cuando se realizan cambios o agregaciones en los buzones de Exchange.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| ClientInfoString | Información sobre el cliente de correo electrónico que se usó para realizar la operación, como la versión del explorador, la versión de Outlook y la información del dispositivo móvil. |
| Client_IPAddress | La dirección IP del dispositivo que se usó cuando se registró la operación. La dirección IP se muestra en formato de dirección IPv4 o IPv6. |
| ClientMachineName | El nombre del equipo que hospeda al cliente de Outlook. |
| ClientProcessName | El cliente de correo electrónico que se usó para tener acceso al buzón. |
| ClientVersion | La versión del cliente de correo electrónico. |
| InternalLogonType | Reservado para uso interno. |
| Logon_Type | Indica el tipo de usuario que tuvo acceso al buzón de correo y realizó la operación que se ha registrado. |
| LogonUserDisplayName |     El nombre descriptivo del usuario que realizó la operación. |
| LogonUserSid | El identificador SID del usuario que realizó la operación. |
| MailboxGuid | El identificador GUID de Exchange del buzón de correo al que se obtuvo acceso. |
| MailboxOwnerMasterAccountSid | Identificador SID de la cuenta maestra de la cuenta del propietario del buzón de correo. |
| MailboxOwnerSid | El identificador SID del propietario del buzón. |
| MailboxOwnerUPN | La dirección de correo electrónico de la persona que posee el buzón al que se obtuvo acceso. |


### <a name="exchange-mailbox-audit"></a>Auditoría de buzón de Exchange

Estos registros se crean cuando se crea una entrada de auditoría de buzones de correo.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | Exchange |
| RecordType     | ExchangeItem |
| Elemento | Representa el elemento en el que se realizó la operación | 
| SendAsUserMailboxGuid | El identificador GUID de Exchange del buzón de correo al que se obtuvo acceso para enviar correo. |
| SendAsUserSmtp | Dirección SMTP del usuario que se está suplantando. |
| SendonBehalfOfUserMailboxGuid | El identificador GUID de Exchange del buzón de correo al que se obtuvo acceso para enviar correo en su nombre. |
| SendOnBehalfOfUserSmtp | Dirección SMTP del usuario en cuyo nombre se envió el correo electrónico. |


### <a name="exchange-mailbox-audit-group"></a>Auditoría de grupos buzones de Exchange

Estos registros se crean cuando se realizan cambios o agregaciones en los grupos de Exchange.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | Exchange |
| OfficeWorkload | ExchangeItemGroup |
| AffectedItems | Información sobre cada elemento del grupo. |
| CrossMailboxOperations | Indica si estuvo implicado más de un buzón de correo en la operación. |
| DestMailboxId | Se establece únicamente si el parámetro CrossMailboxOperations es True. Especifica el identificador GUID del buzón de destino. |
| DestMailboxOwnerMasterAccountSid | Se establece únicamente si el parámetro CrossMailboxOperations es True. Especifica el identificador SID de la cuenta maestra del propietario del buzón de destino. |
| DestMailboxOwnerSid | Se establece únicamente si el parámetro CrossMailboxOperations es True. Especifica el identificador SID del buzón de correo de destino. |
| DestMailboxOwnerUPN | Se establece únicamente si el parámetro CrossMailboxOperations es True. Especifica el UPN del propietario del buzón de correo de destino. |
| DestFolder | La carpeta de destino, para operaciones como Move (mover). |
| Carpeta | La carpeta donde se encuentra un grupo de elementos. |
| Carpetas |     Información sobre las carpetas de origen implicadas en una operación; por ejemplo, si las carpetas son seleccionadas y, a continuación, se eliminan. |


### <a name="sharepoint-base"></a>SharePoint

Estas propiedades son comunes a todos los registros de SharePoint.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| EventSource | Identifica que se ha producido un evento en SharePoint. Los valores posibles son SharePoint y ObjectModel. |
| ItemType | El tipo de objeto al que se ha accedido o modificado. Consulte la tabla ItemType para obtener detalles sobre los tipos de objetos. |
| MachineDomainInfo | Información sobre las operaciones de sincronización del dispositivo. Esta información se notifica solo si está presente en la solicitud. |
| MachineId |     Información sobre las operaciones de sincronización del dispositivo. Esta información se notifica solo si está presente en la solicitud. |
| Site_ | El identificador GUID del sitio donde se encuentra el archivo o carpeta al que tuvo acceso el usuario. |
| Source_Name | La entidad que desencadenó la operación auditada. Los valores posibles son SharePoint y ObjectModel. |
| UserAgent | Información sobre el cliente o explorador del usuario. Esta información la proporciona el cliente o el explorador. |


### <a name="sharepoint-schema"></a>Esquema de SharePoint

Estos registros se crean cuando se realizan cambios en la configuración de SharePoint.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePoint |
| CustomEvent | Cadena opcional para eventos personalizados. |
| Event_Data |     Carga opcional para eventos personalizados. |
| ModifiedProperties | La propiedad se incluye para los eventos de administración, como agregar a un usuario como miembro de un sitio o un grupo de administración de la colección de sitios. La propiedad incluye el nombre de la propiedad que se ha modificado (por ejemplo, el grupo de administración del sitio), el nuevo valor de la propiedad modificada (como el usuario que se ha agregado como un administrador del sitio) y el valor anterior del objeto modificado. |


### <a name="sharepoint-file-operations"></a>Operaciones de archivos de SharePoint

Estos registros se crean en respuesta a las operaciones de archivos en SharePoint.

| Propiedad | Descripción |
|:--- |:--- |
| OfficeWorkload | SharePoint |
| OfficeWorkload | SharePointFileOperation |
| DestinationFileExtension | La extensión de archivo de un archivo que se ha copiado o movido. Esta propiedad solo se muestra para los eventos FileCopied y FileMoved. |
| DestinationFileName | El nombre del archivo que se ha copiado o movido. Esta propiedad solo se muestra para los eventos FileCopied y FileMoved. |
| DestinationRelativeUrl | La dirección URL de la carpeta de destino donde se ha copiado o movido un archivo. La combinación de los valores de los parámetros SiteURL, DestinationRelativeURL y DestinationFileName es el valor de la propiedad ObjectID, que es el nombre de la ruta de acceso completa del archivo que se copió. Esta propiedad solo se muestra para los eventos FileCopied y FileMoved. |
| SharingType | El tipo de permisos que se asignaron al usuario con el que se ha compartido el recurso. Este usuario se identifica mediante el parámetro UserSharedWith. |
| Site_Url | La dirección URL del sitio donde se encuentra el archivo o carpeta al que tuvo acceso el usuario. |
| SourceFileExtension | La extensión de archivo del archivo al que tuvo acceso el usuario. Esta propiedad está en blanco si el objeto al que se tuvo acceso es una carpeta. |
| SourceFileName |     El nombre del archivo o carpeta al que tuvo acceso el usuario. |
| SourceRelativeUrl | La dirección URL de la carpeta que contiene el archivo al que tuvo acceso el usuario. La combinación de los valores de los parámetros SiteURL, SourceRelativeURL y SourceFileName es el valor de la propiedad ObjectID, que es el nombre de la ruta de acceso completa del archivo al que tuvo acceso el usuario. |
| UserSharedWith |     El usuario con el que se compartió un recurso. |




## <a name="sample-log-queries"></a>Consultas de registros de ejemplo

En la tabla siguiente se proporcionan ejemplos de consultas de registros para los registros de actualizaciones recopilados por esta solución.

| Consultar | Descripción |
| --- | --- |
|Recuento de todas las operaciones de la suscripción a Office 365 |OfficeActivity &#124; summarize count() by Operation |
|Uso de sitios de SharePoint|OfficeActivity &#124; where OfficeWorkload =~ "sharepoint" &#124; summarize count() by SiteUrl \| sort by Count asc|
|Operaciones de acceso a archivos por tipo de usuario | OfficeActivity &#124; summarize count() by UserType |
|Supervisión de acciones externas en Exchange|OfficeActivity &#124; where OfficeWorkload =~ "exchange" and ExternalAccess == true|



## <a name="next-steps"></a>Pasos siguientes

* Use las [consultas de registros de Azure Monitor](../logs/log-query-overview.md) para ver datos detallados sobre la actualización.
* [Cree sus propios paneles](../visualize/tutorial-logs-dashboards.md) para mostrar las consultas de búsqueda favoritas de Office 365.
* [Cree alertas](../alerts/alerts-overview.md) para recibir notificaciones proactivas de actividades importantes de Office 365.