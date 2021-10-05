---
title: Roles, permisos y seguridad en Azure Monitor
description: Obtenga información sobre cómo utilizar los permisos y los roles de Azure Monitor para restringir el acceso a los recursos de supervisión.
services: azure-monitor
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 3b4335f3a6690ef502bdd495ea0de317eacfe738
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788693"
---
# <a name="roles-permissions-and-security-in-azure-monitor"></a>Roles, permisos y seguridad en Azure Monitor

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Muchos equipos necesitan regular estrictamente el acceso a los datos y la configuración de supervisión. Por ejemplo, si tiene miembros del equipo que trabajan exclusivamente en la supervisión (ingenieros de soporte técnico o ingenieros de DevOps) o si usa un proveedor de servicios administrados, puede concederles acceso solo a datos de supervisión. Es posible que quiera restringir su capacidad para crear, modificar o eliminar recursos. 

En este artículo se explica cómo aplicar rápidamente un rol de supervisión integrado a un usuario en Azure o crear un rol personalizado propio para un usuario que necesita permisos de supervisión limitados. En el artículo después se describen las consideraciones de seguridad para los recursos relacionados con Azure Monitor y cómo puede limitar el acceso a los datos que contienen.

## <a name="built-in-monitoring-roles"></a>Roles de supervisión integrados
Los roles integrados en Azure Monitor ayudan a limitar el acceso a recursos de una suscripción, sin impedir que los responsables de supervisión de la infraestructura obtengan y configuren los datos que necesitan. Azure Monitor proporciona dos roles de fábrica: un lector de supervisión y un colaborador de supervisión.

### <a name="monitoring-reader"></a>Lector de supervisión
Las personas asignadas al rol Lector de supervisión pueden ver todos los datos de supervisión en una suscripción, pero no pueden modificar ningún recurso ni editar ninguna configuración relacionada con la supervisión de recursos. Este rol es adecuado para los usuarios de una organización, como los ingenieros de soporte técnico u operaciones que necesitan tener la capacidad de:

* Ver los paneles de supervisión de Azure Portal.
* Ver las reglas de alerta definidas en [Alertas de Azure](alerts/alerts-overview.md).
* Consultar métricas con la [API REST de Azure Monitor](/rest/api/monitor/metrics), los [cmdlets de PowerShell](powershell-samples.md) o la [CLI multiplataforma](cli-samples.md).
* Consultar el registro de actividades a través del portal, la API REST de Azure Monitor, los cmdlets de PowerShell o la CLI multiplataforma.
* Ver la [configuración de diagnóstico](essentials/diagnostic-settings.md) de un recurso.
* Ver el [perfil de registro](essentials/activity-log.md#legacy-collection-methods) de una suscripción.
* Consultar la configuración de escalado automático.
* Consultar la configuración y actividad de alertas.
* Acceder a datos de Application Insights y ver los datos en Application Insights Analytics.
* Buscar datos del área de trabajo de Log Analytics, incluidos los datos de uso del área de trabajo.
* Ver grupos de administración en Log Analytics.
* Recuperar el esquema de búsqueda en un área de trabajo de Log Analytics.
* Enumerar los paquetes de supervisión en un área de trabajo de Log Analytics.
* Recuperar y ejecutar las búsquedas guardadas en un área de trabajo de Log Analytics.
* Recuperar la configuración de almacenamiento del área de trabajo de Log Analytics.

> [!NOTE]
> Este rol no otorga acceso de lectura a los datos de registro que se han transmitido a un centro de eventos o que están almacenados en una cuenta de almacenamiento. Para obtener información sobre cómo configurar el acceso a estos recursos, consulte la sección [Consideraciones de seguridad para datos de supervisión](#security-considerations-for-monitoring-data) más adelante en este artículo. 

### <a name="monitoring-contributor"></a>Colaborador de supervisión
Las personas que tienen asignado el rol Colaborador de supervisión pueden ver todos los datos de supervisión de una suscripción. También pueden crear o modificar la configuración de supervisión, pero no pueden modificar ningún otro recurso. 

Este rol es un superconjunto del rol Lector de supervisión. Es adecuado para los miembros del equipo de supervisión de una organización o los proveedores de servicios administrados que, además de los permisos mencionados anteriormente, deben poder:

* Ver paneles de supervisión en el portal y crear sus propios paneles de supervisión privados.
* Determinar la [configuración de diagnóstico](essentials/diagnostic-settings.md) de un recurso.\*
* Establecer el [perfil de registro](essentials/activity-log.md#legacy-collection-methods) de una suscripción.\*
* Establecer la configuración y la actividad de las reglas de alertas a través de [Alertas de Azure](alerts/alerts-overview.md).
* Crear componentes y pruebas web de Application Insights.
* Enumerar las claves compartidas de un área de trabajo de Log Analytics.
* Habilitar o deshabilitar los paquetes de supervisión en el área de trabajo de Log Analytics.
* Crear, eliminar y ejecutar las búsquedas guardadas en el área de trabajo de Log Analytics.
* Crear y eliminar la configuración de almacenamiento del área de trabajo de Log Analytics.

\*Para definir la configuración del diagnóstico o el perfil de registros, al usuario también se le debe conceder el permiso ListKeys por separado en el recurso de destino (cuenta de almacenamiento o espacio de nombres del centro de eventos).

> [!NOTE]
> Este rol no otorga acceso de lectura a los datos de registro que se han transmitido a un centro de eventos o que están almacenados en una cuenta de almacenamiento. Para obtener información sobre cómo configurar el acceso a estos recursos, consulte la sección [Consideraciones de seguridad para datos de supervisión](#security-considerations-for-monitoring-data) más adelante en este artículo. 

## <a name="monitoring-permissions-and-azure-custom-roles"></a>Permisos de supervisión y roles de Azure personalizados
Si los roles integrados anteriores no satisfacen las necesidades exactas de su equipo, puede [crear un rol de Azure personalizado](../role-based-access-control/custom-roles.md) con permisos más granulares. Estas son las operaciones comunes de control de acceso basado en roles (RBAC) de Azure para Azure Monitor:

| Operación | Descripción |
| --- | --- |
| Microsoft.Insights/ActionGroups/[Read, Write, Delete] |Leer, escribir o eliminar grupos de acciones. |
| Microsoft.Insights/ActivityLogAlerts/[Read, Write, Delete] |Leer, escribir o eliminar alertas del registro de actividad. |
| Microsoft.Insights/AlertRules/[Read, Write, Delete] |Leer, escribir o eliminar reglas de alertas (de alertas clásicas). |
| Microsoft.Insights/AlertRules/Incidents/Read |Enumerar los incidentes (historial de la regla de alerta desencadenada) de reglas de alerta. Esto solo se aplica solo al portal. |
| Microsoft.Insights/AutoscaleSettings/[Read, Write, Delete] |Leer, escribir o eliminar la configuración de escalado automático. |
| Microsoft.Insights/DiagnosticSettings/[Read, Write, Delete] |Leer, escribir o eliminar la configuración de diagnóstico. |
| Microsoft.Insights/EventCategories/Read |Enumerar todas las categorías posibles en el registro de actividad. Lo utiliza Azure Portal. |
| Microsoft.Insights/eventtypes/digestevents/Read |Este permiso es necesario para los usuarios que necesitan acceder a registros de actividades a través del portal. |
| Microsoft.Insights/eventtypes/values/Read |Enumerar eventos del registro de actividades (eventos de administración) de una suscripción. Este permiso es aplicable para el acceso mediante programación y mediante el portal al registro de actividades. |
| Microsoft.Insights/ExtendedDiagnosticSettings/[Read, Write, Delete] | Leer, escribir o eliminar configuración de diagnóstico para registros de flujo de red. |
| Microsoft.Insights/LogDefinitions/Read |Este permiso es necesario para los usuarios que necesitan acceder a registros de actividades a través del portal. |
| Microsoft.Insights/LogProfiles/[Read, Write, Delete] |Leer, escribir o eliminar perfiles de registro (transmisión del registro de actividad a un centro de eventos o una cuenta de almacenamiento). |
| Microsoft.Insights/MetricAlerts/[Read, Write, Delete] |Leer, escribir o eliminar alertas de métricas casi en tiempo real. |
| Microsoft.Insights/MetricDefinitions/Read |Leer definiciones de métrica (lista de tipos de métricas disponibles para un recurso). |
| Microsoft.Insights/Metrics/Read |Leer las métricas de un recurso. |
| Microsoft.Insights/Register/Action |Registrar el proveedor de recursos de Azure Monitor. |
| Microsoft.Insights/ScheduledQueryRules/[Read, Write, Delete] |Leer, escribir o eliminar alertas de registro en Azure Monitor. |

> [!NOTE]
> El acceso a las alertas, la configuración de diagnóstico y las métricas de un recurso requiere que el usuario tenga acceso de lectura al tipo de recurso y el ámbito de ese recurso. La creación (escritura) de una configuración de diagnóstico o un perfil de registro que se archiva en una cuenta de almacenamiento o se transmite a centros de eventos requiere que el usuario tenga también el permiso ListKeys en el recurso de destino. 

Por ejemplo, puede usar la tabla anterior para crear un rol personalizado de Azure para un lector del registro de actividad como el siguiente:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Activity Log Reader"
$role.Description = "Can view activity logs."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Insights/eventtypes/*")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription")
New-AzRoleDefinition -Role $role 
```

## <a name="security-considerations-for-monitoring-data"></a>Consideraciones de seguridad para datos de supervisión
Los datos de supervisión, en particular los archivos de registro, pueden obtener información confidencial, como los nombres de usuario o las direcciones IP. Los datos de supervisión de Azure se presentan en tres formatos básicos:

- El registro de actividad, que describe todas las acciones de plano de control en su suscripción de Azure.
- Los registros de recursos, que son registros emitidos por un recurso.
- Métricas, que emiten los recursos.

Estos tipos de datos pueden almacenarse en una cuenta de almacenamiento o transmitirse a un centro de eventos, y ambos son recursos de Azure de uso general. Dado que son recursos de uso general, su creación, eliminación o el acceso a ellos constituyen una operación privilegiada reservada a un administrador. Se recomienda utilizar los procedimientos siguientes para recursos relacionados con la supervisión para impedir el uso indebido:

* Utilizar una cuenta de almacenamiento dedicada a datos de supervisión. Si necesita separar los datos de supervisión en varias cuentas de almacenamiento, nunca comparta el uso de una cuenta de almacenamiento entre los datos de supervisión y de otro tipo. Este uso compartido podría conceder acceso accidentalmente a datos no relacionados con la supervisión a organizaciones que solo necesitan tener acceso a datos de supervisión. Por ejemplo, una organización de terceros para la administración de eventos e información de seguridad solo debe tener acceso a los datos de supervisión.
* Utilice un único espacio de nombres dedicado de un bus de servicio o un centro de eventos en todas las configuraciones de diagnóstico por la misma razón descrita en el punto anterior.
* Limite el acceso a las cuentas de almacenamiento o los centros de eventos relacionados con la supervisión manteniéndolos en un grupo de recursos separado. [Use el ámbito](../role-based-access-control/overview.md#scope) en los roles de supervisión para limitar el acceso solo a ese grupo de recursos.
* No conceda nunca el permiso ListKeys para las cuentas de almacenamiento o los centros de eventos en el ámbito de la suscripción cuando un usuario solo necesita acceso a los datos de supervisión. En su lugar, conceda estos permisos al usuario en un ámbito de recurso o grupo de recursos (si tiene un grupo de recursos de supervisión dedicado).

### <a name="limiting-access-to-monitoring-related-storage-accounts"></a>Restricción del acceso a cuentas de almacenamiento relacionadas con la supervisión
Cuando un usuario o aplicación necesita acceso a datos de supervisión en una cuenta de almacenamiento, debe [generar una firma de acceso compartido (SAS)](/rest/api/storageservices/create-account-sas) en la cuenta de almacenamiento que contenga los datos de supervisión con acceso de solo lectura de nivel de servicio a Blob Storage. En PowerShell, la SAS de la cuenta podría ser como el código siguiente:

```powershell
$context = New-AzStorageContext -ConnectionString "[connection string for your monitoring Storage Account]"
$token = New-AzStorageAccountSASToken -ResourceType Service -Service Blob -Permission "rl" -Context $context
```

A continuación, puede proporcionar el token a la entidad que necesita leer de esa cuenta de almacenamiento. La entidad puede enumerar y leer todos los blobs de esa cuenta de almacenamiento.

O bien, si necesita controlar este permiso con Azure RBAC, puede conceder a dicha entidad el permiso `Microsoft.Storage/storageAccounts/listkeys/action` para esa cuenta de almacenamiento determinada. Este permiso es necesario para los usuarios que necesitan tener la capacidad de definir una configuración de diagnóstico o un perfil de registro a fin de archivar algo en una cuenta de almacenamiento. Por ejemplo, puede crear el siguiente rol de Azure personalizado para un usuario o una aplicación que solo necesita leer desde una cuenta de almacenamiento:

```powershell
$role = Get-AzRoleDefinition "Reader"
$role.Id = $null
$role.Name = "Monitoring Storage Account Reader"
$role.Description = "Can get the storage account keys for a monitoring storage account."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Storage/storageAccounts/listkeys/action")
$role.Actions.Add("Microsoft.Storage/storageAccounts/Read")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.Storage/storageAccounts/myMonitoringStorageAccount")
New-AzRoleDefinition -Role $role 
```

> [!WARNING]
> El permiso ListKeys permite al usuario enumerar las claves de la cuenta de almacenamiento principal y secundaria. Estas claves conceden al usuario todos los permisos firmados (como leer, escribir, crear blobs y eliminar blobs) en todos los servicios suscritos (blob, cola, tabla y archivo) en esa cuenta de almacenamiento. Se recomienda usar una SAS de cuenta cuando sea posible. 

### <a name="limiting-access-to-monitoring-related-event-hubs"></a>Restricción del acceso a centros de eventos relacionados con la supervisión
Puede seguir un patrón similar con los centros de eventos, pero primero debe crear una regla de autorización dedicada para la escucha. Si desea conceder acceso a una aplicación que solo necesita escuchar a centros de eventos relacionados con la supervisión, haga lo siguiente:

1. En el portal, cree una directiva de acceso compartido en los centros de eventos que se crearon para la transmisión de datos de supervisión con notificaciones de escucha exclusivamente. Por ejemplo, podría llamarlo "monitoringReadOnly". Si es posible, proporcione la clave directamente al consumidor y omita el paso siguiente.
2. Si el consumidor debe tener la capacidad de obtener la clave ad hoc, conceda al usuario la acción ListKeys para ese centro de eventos. Este paso es necesario para los usuarios que necesitan tener la capacidad de definir una configuración de diagnóstico o un perfil de registro para realizar transmisiones a los centros de eventos. Por ejemplo, podría crear una regla de Azure RBAC:
   
   ```powershell
   $role = Get-AzRoleDefinition "Reader"
   $role.Id = $null
   $role.Name = "Monitoring Event Hub Listener"
   $role.Description = "Can get the key to listen to an event hub streaming monitoring data."
   $role.Actions.Clear()
   $role.Actions.Add("Microsoft.EventHub/namespaces/authorizationrules/listkeys/action")
   $role.Actions.Add("Microsoft.EventHub/namespaces/Read")
   $role.AssignableScopes.Clear()
   $role.AssignableScopes.Add("/subscriptions/mySubscription/resourceGroups/myResourceGroup/providers/Microsoft.ServiceBus/namespaces/mySBNameSpace")
   New-AzRoleDefinition -Role $role 
   ```

## <a name="monitoring-within-a-secured-virtual-network"></a>Supervisión dentro de una red virtual protegida

Azure Monitor necesita acceso a los recursos de Azure para proporcionar los servicios que habilite. Si desea supervisar los recursos de Azure a la vez que los protege del acceso a la red pública de Internet, puede usar cuentas de almacenamiento protegidas.

Los datos de supervisión a menudo se escriben en una cuenta de almacenamiento. Es posible que quiera asegurarse de que los usuarios no autorizados no puedan acceder a los datos que se copian en una cuenta de almacenamiento. Para mayor seguridad, puede bloquear el acceso de red para permitir que solo los recursos autorizados y los servicios de confianza de Microsoft accedan a una cuenta de almacenamiento mediante la restricción del uso de redes seleccionadas por parte de una cuenta de almacenamiento.

![Captura de pantalla que muestra la configuración de firewalls y redes virtuales.](./media/roles-permissions-security/secured-storage-example.png)

Azure Monitor se considera un servicio de Microsoft de confianza. Si activa la casilla **Permitir que los servicios Microsoft de confianza accedan a esta cuenta de almacenamiento**, Azure Monitor tendrá acceso a la cuenta de almacenamiento protegida. A continuación, habilita la escritura de los registros de recursos de Azure Monitor, el registro de actividad y las métricas en su cuenta de almacenamiento bajo estas condiciones protegidas. Esta configuración también permitirá que Log Analytics lea los registros de almacenamiento protegido.   

Para más información, vea [Configuración de Firewalls y redes virtuales de Azure Storage](../storage/common/storage-network-security.md).

## <a name="next-steps"></a>Pasos siguientes
* [Más información sobre Azure RBAC y permisos en Azure Resource Manager](../role-based-access-control/overview.md)
* [Lea la información general sobre supervisión en Azure](overview.md)

