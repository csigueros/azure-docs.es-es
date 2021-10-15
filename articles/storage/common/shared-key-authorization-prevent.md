---
title: Evitar autorización con clave compartida
titleSuffix: Azure Storage
description: Para exigir que los clientes usen Azure AD para autorizar las solicitudes, puede denegar las solicitudes a la cuenta de almacenamiento que están autorizadas con la clave compartida.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 10/01/2021
ms.author: tamram
ms.reviewer: fryu
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 566c538e0864f11dad0a642dd18f711c7cf00886
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/05/2021
ms.locfileid: "129532834"
---
# <a name="prevent-shared-key-authorization-for-an-azure-storage-account"></a>Impedir la autorización con clave compartida para una cuenta de Azure Storage

Cada solicitud segura a una cuenta de Azure Storage debe estar autorizada. De forma predeterminada, las solicitudes se pueden autorizar con credenciales de Azure Active Directory (Azure AD) o mediante la clave de acceso de la cuenta para la autorización con clave compartida. De estos dos tipos de autorización, Azure AD proporciona mayor seguridad y facilidad de uso a través de la clave compartida y es el que Microsoft recomienda. Para exigir que los clientes usen Azure AD para autorizar las solicitudes, puede denegar las solicitudes a la cuenta de almacenamiento que están autorizadas con la clave compartida.

Cuando se impide la autorización con clave compartida para una cuenta de almacenamiento, Azure Storage rechaza todas las solicitudes posteriores a esa cuenta autorizadas con las claves de acceso de la cuenta. Solo se realizarán correctamente las solicitudes protegidas que estén autorizadas con Azure AD. Para obtener más información sobre el uso de Azure AD, consulte [Autorización del acceso a datos en Azure Storage](authorize-data-access.md).

En este artículo se describe cómo detectar solicitudes enviadas con la autorización con clave compartida y cómo corregir la autorización con clave compartida para la cuenta de almacenamiento.

## <a name="detect-the-type-of-authorization-used-by-client-applications"></a>Detección del tipo de autorización que usan las aplicaciones cliente

Si impide la autorización con clave compartida para una cuenta de almacenamiento, se producirá un error en las solicitudes de los clientes que usan las claves de acceso de cuenta para la autorización con clave compartida. Para comprender cómo la denegación de la autorización con clave compartida puede afectar a las aplicaciones cliente antes de efectuar este cambio, habilite el registro y las métricas para la cuenta de almacenamiento. A continuación, puede analizar los patrones de las solicitudes a su cuenta durante un período de tiempo para determinar cómo se autorizan las solicitudes.

Use métricas para determinar el número de solicitudes que la cuenta de almacenamiento está recibiendo y que están autorizadas con una clave compartida o una firma de acceso compartido (SAS). Use los registros para determinar qué clientes envían esas solicitudes.

Una SAS puede estar autorizada con una clave compartida o con Azure AD. Para más información sobre cómo interpretar las solicitudes realizadas con una firma de acceso compartido, consulte [Descripción de cómo la denegación de la clave compartida afecta a los tokens de SAS](#understand-how-disallowing-shared-key-affects-sas-tokens).

### <a name="monitor-how-many-requests-are-authorized-with-shared-key"></a>Supervisión del número de solicitudes autorizadas con clave compartida

Para realizar el seguimiento de cómo se autorizan las solicitudes a una cuenta de almacenamiento, use el Explorador de métricas de Azure situado en Azure Portal. Para más información sobre el Explorador de métricas, consulte [Introducción al Explorador de métricas de Azure](../../azure-monitor/essentials/metrics-getting-started.md).

Siga estos pasos para crear una métrica que realice el seguimiento de las solicitudes llevadas a cabo con clave compartida o SAS:

1. Vaya a la cuenta de almacenamiento en Azure Portal. En la sección **Supervisión**, seleccione **Métricas**.
1. Seleccione **Agregar métrica**. En el cuadro de diálogo **Métrica**, especifique los valores siguientes:
    1. Deje el campo **Ámbito** establecido en el nombre de la cuenta de almacenamiento.
    1. Establezca **Espacio de nombres de métricas** en *Cuenta*. Esta métrica informará de todas las solicitudes realizadas a la cuenta de almacenamiento.
    1. Establezca el campo **Métrica** en *Transacciones*.
    1. Establezca el campo **Agregación** en *Suma*.

    La nueva métrica mostrará la suma del número de transacciones en la cuenta de almacenamiento a lo largo de un intervalo de tiempo determinado. La métrica resultante aparece como se muestra en la siguiente imagen:

    :::image type="content" source="media/shared-key-authorization-prevent/configure-metric-account-transactions.png" alt-text="Captura de pantalla que muestra cómo configurar la métrica para sumar transacciones realizadas con clave compartida o SAS":::

1. A continuación, seleccione el botón **Agregar filtro** para crear un filtro en la métrica para el tipo de autorización.
1. En el cuadro de diálogo **Filtro**, especifique los valores siguientes:
    1. Establezca el valor **Propiedad** en *Autenticación*.
    1. Establezca el campo **Operador** en el signo igual (=).
    1. En el campo **Valores**, seleccione *Clave de cuenta* y *SAS*.
1. En la esquina superior derecha, seleccione el intervalo de tiempo del que desea ver la métrica. También puede indicar lo pormenorizada que debe ser la agregación de las solicitudes, especificando intervalos en cualquier punto entre 1 minuto y 1 mes. Por ejemplo, establezca **Intervalo de tiempo** en 30 días y **Granularidad de tiempo** en 1 día para ver las solicitudes agregadas por día durante los últimos 30 días.

Una vez configurada la métrica, las solicitudes a la cuenta de almacenamiento comenzarán a aparecer en el gráfico. En la imagen siguiente se muestran las solicitudes que se autorizaron con la clave compartida o que se realizaron con un token de SAS. Las solicitudes se agregan por día durante los últimos treinta días.

:::image type="content" source="media/shared-key-authorization-prevent/metric-shared-key-requests.png" alt-text="Captura de pantalla que muestra las solicitudes agregadas autorizadas con clave compartida":::

También puede configurar una regla de alerta para recibir una notificación cuando se realice un determinado número de solicitudes autorizadas con clave compartida en la cuenta de almacenamiento. Para más información, vea [Creación, visualización y administración de alertas de métricas mediante Azure Monitor](../../azure-monitor/alerts/alerts-metric.md).

### <a name="analyze-logs-to-identify-clients-that-are-authorizing-requests-with-shared-key-or-sas"></a>Análisis de registros para identificar los clientes que están autorizando solicitudes con clave compartida o SAS

Los registros de Azure Storage capturan detalles sobre las solicitudes realizadas a la cuenta de almacenamiento, como el modo en que se autorizó una solicitud. Puede analizar los registros para determinar qué clientes están autorizando solicitudes con clave compartida o token de SAS.

Para registrar las solicitudes a su cuenta de Azure Storage con el fin de evaluar cómo se autorizan, puede usar el registro de Azure Storage en Azure Monitor (versión preliminar). Para más información, consulte [Supervisión de Azure Storage](../blobs/monitor-blob-storage.md).

El registro de Azure Storage en Azure Monitor admite el uso de consultas de registro para analizar los datos de registro. Para consultar los registros, puede usar un área de trabajo de Azure Log Analytics. Para más información sobre las consultas de registro, consulte el [Tutorial: Introducción a las consultas de Log Analytics](../../azure-monitor/logs/log-analytics-tutorial.md).

#### <a name="create-a-diagnostic-setting-in-the-azure-portal"></a>Creación de una configuración de diagnóstico en Azure Portal

Para registrar datos de Azure Storage con Azure Monitor y analizarlos con Azure Log Analytics, primero debe crear una configuración de diagnóstico que indique qué tipos de solicitudes y para qué servicios de almacenamiento quiere registrar los datos. Para crear una configuración de diagnóstico en Azure Portal, siga estos pasos:

1. Cree un área de trabajo de Log Analytics en la suscripción que contenga la cuenta de Azure Storage o use un área de trabajo de Log Analytics existente. Después de configurar el registro de la cuenta de almacenamiento, los registros estarán disponibles en el área de trabajo de Log Analytics. Para obtener más información, consulte [Creación de un área de trabajo de Log Analytics en Azure Portal](../../azure-monitor/logs/quick-create-workspace.md).
1. Vaya a la cuenta de almacenamiento en Azure Portal.
1. En la sección Supervisión, seleccione **Configuración de diagnóstico (versión preliminar)** .
1. Seleccione el servicio de Azure Storage cuyas solicitudes quiere registrar. Por ejemplo, elija **Blob** para registrar las solicitudes a Blob Storage.
1. Seleccione **Agregar configuración de diagnóstico**.
1. Proporcione un nombre para la configuración de diagnóstico.
1. En **Detalles de la categoría**, en la sección **Registro**, elija **StorageRead**, **StorageWrite** y **StorageDelete** para registrar todas las solicitudes de datos al servicio seleccionado.
1. En **Detalles del destino**, seleccione **Enviar a Log Analytics**. Seleccione la suscripción y el área de trabajo de Log Analytics que creó anteriormente, como se muestra en la siguiente imagen.

    :::image type="content" source="media/shared-key-authorization-prevent/create-diagnostic-setting-logs.png" alt-text="Captura de pantalla que muestra cómo crear una configuración de diagnóstico para el registro de las solicitudes":::

Puede crear una configuración de diagnóstico para cada tipo de recurso de Azure Storage en la cuenta de almacenamiento.

Después de crear la configuración de diagnóstico, las solicitudes a la cuenta de almacenamiento se registran posteriormente según esa configuración. Para más información, consulte [Creación de una configuración de diagnóstico para recopilar registros y métricas en Azure](../../azure-monitor/essentials/diagnostic-settings.md).

Puede encontrar una referencia sobre los campos disponibles en los registros de Azure Storage en Azure Monitor en el artículo [Registros de recursos (versión preliminar)](../blobs/monitor-blob-storage-reference.md#resource-logs-preview).

#### <a name="query-logs-for-requests-made-with-shared-key-or-sas"></a>Registros de consultas para las solicitudes realizadas con clave compartida o SAS

Los registros de Azure Storage en Azure Monitor incluyen el tipo de autorización que se usó para hacer una solicitud a una cuenta de almacenamiento. Para recuperar los registros de las solicitudes realizadas en los últimos siete días que se autorizaron con la clave compartida o SAS, abra el área de trabajo de Log Analytics. Luego, pegue la siguiente consulta en una nueva consulta de registro y ejecútela. Esta consulta muestra las diez direcciones IP que enviaron solicitudes con mayor frecuencia que se autorizaron con clave compartida o SAS:

```kusto
StorageBlobLogs
| where AuthenticationType in ("AccountKey", "SAS") and TimeGenerated > ago(7d)
| summarize count() by CallerIpAddress, UserAgentHeader, AccountName
| top 10 by count_ desc
```

También puede configurar una regla de alerta basada en esta consulta para que le informe sobre las solicitudes autorizadas con clave compartida o SAS. Para más información, consulte [Creación, visualización y administración de alertas de registro mediante Azure Monitor](../../azure-monitor/alerts/alerts-log.md).

## <a name="remediate-authorization-via-shared-key"></a>Corrección de la autorización mediante clave compartida

Después de haber analizado cómo se autorizan las solicitudes a la cuenta de almacenamiento, puede tomar medidas para evitar el acceso a través de una clave compartida. Pero antes, debe actualizar las aplicaciones que usan la autorización de clave compartida para usar Azure AD en su lugar. Puede supervisar los registros y las métricas tal y como se describe en [Detección del tipo de autorización que usan las aplicaciones cliente](#detect-the-type-of-authorization-used-by-client-applications) para realizar el seguimiento de la transición. Para obtener más información sobre el uso de Azure AD para acceder a datos en una cuenta de almacenamiento, consulte [Autorización del acceso a datos en Azure Storage](authorize-data-access.md).

Cuando esté seguro de que puede rechazar de forma segura las solicitudes que están autorizadas con la clave compartida, puede establecer la propiedad **AllowSharedKeyAccess** de la cuenta de almacenamiento en **false**.

La propiedad **AllowSharedKeyAccess** no se establece de forma predeterminada y no devuelve un valor hasta que se establece de forma explícita. La cuenta de almacenamiento permite solicitudes que están autorizadas con clave compartida cuando el valor de la propiedad es **null** o **true**.

> [!WARNING]
> Si algún cliente está accediendo actualmente a los datos de la cuenta de almacenamiento con la clave compartida, Microsoft recomienda migrar esos clientes a Azure AD antes de denegar el acceso con clave compartida a la cuenta de almacenamiento.

# <a name="azure-portal"></a>[Azure Portal](#tab/portal)

Para denegar la autorización con clave compartida para una cuenta de almacenamiento en Azure Portal, siga estos pasos:

1. Vaya a la cuenta de almacenamiento en Azure Portal.
1. Busque la opción **Configuración** en **Configuración**.
1. Establezca la opción **Permitir el acceso a la clave de la cuenta de almacenamiento** en **Deshabilitado**.

    :::image type="content" source="media/shared-key-authorization-prevent/shared-key-access-portal.png" alt-text="Captura de pantalla que muestra cómo denegar el acceso con clave compartida para una cuenta":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Para denegar la autorización con clave compartida para una cuenta de almacenamiento con PowerShell, instale el [módulo de PowerShell Az.Storage](https://www.powershellgallery.com/packages/Az.Storage), versión 3.4.0 o posterior. A continuación, configure la propiedad **AllowSharedKeyAccess** para una cuenta de almacenamiento nueva o existente.

En el ejemplo siguiente se muestra cómo denegar el acceso con clave compartida en una cuenta de almacenamiento existente con PowerShell. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```powershell
Set-AzStorageAccount -ResourceGroupName <resource-group> `
    -AccountName <storage-account> `
    -AllowSharedKeyAccess $false
```

# <a name="azure-cli"></a>[CLI de Azure](#tab/azure-cli)

Para denegar la autorización de clave compartida para una cuenta de almacenamiento con la CLI de Azure, instale la CLI de Azure, versión 2.20.0 o posterior. Para más información, consulte [Instalación de la CLI de Azure](/cli/azure/install-azure-cli). A continuación, configure la propiedad **allowSharedKeyAccess** para una cuenta de almacenamiento nueva o existente.

En el ejemplo siguiente se muestra cómo denegar el acceso con clave compartida en una cuenta de almacenamiento existente con la CLI de Azure. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli-interactive
az storage account update \
    --name <storage-account> \
    --resource-group <resource-group> \
    --allow-shared-key-access false
```

---

Después de denegar la autorización con clave compartida, la solicitud a la cuenta de almacenamiento con autorización con clave compartida no se podrá realizar y dará un error con el código de error 403 (prohibido). Azure Storage devuelve un error que indica que la autorización basada en clave no se permite en la cuenta de almacenamiento.

La propiedad **AllowSharedKeyAccess** se admite en cuentas de almacenamiento que usan únicamente el modelo de implementación de Azure Resource Manager. Para información sobre qué cuentas de almacenamiento usan el modelo de implementación de Azure Resource Manager, consulte [Tipos de cuentas de almacenamiento](storage-account-overview.md#types-of-storage-accounts).

### <a name="verify-that-shared-key-access-is-not-allowed"></a>Verificación de que el acceso a la clave compartida no se permite

Para verificar que la autorización con clave compartida ya no se permite, puede intentar llamar a una operación de datos con la clave de acceso a la cuenta. En el siguiente ejemplo intenta crear un contenedor utilizando la clave de acceso. Esta llamada no se podrá realizar cuando se deniegue la autorización con clave compartida para la cuenta de almacenamiento. No olvide reemplazar los valores del marcador de posición entre corchetes con sus propios valores:

```azurecli-interactive
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key> \
    --auth-mode key
```

> [!NOTE]
> Las solicitudes anónimas no están autorizadas y continuarán si ha configurado la cuenta de almacenamiento y el contenedor para el acceso de lectura público anónimo. Para más información, consulte [Configuración del acceso de lectura público anónimo a contenedores y blobs](../blobs/anonymous-read-access-configure.md).

### <a name="check-the-shared-key-access-setting-for-multiple-accounts"></a>Comprobación de la configuración de acceso con clave compartida para varias cuentas

Para comprobar la configuración de acceso con clave compartida en un conjunto de cuentas de almacenamiento con un rendimiento óptimo, puede usar Azure Resource Graph Explorer en Azure Portal. Para más información sobre el uso de Resource Graph Explorer, consulte [Inicio rápido: Ejecución de la primera consulta de Resource Graph mediante Azure Resource Graph Explorer](../../governance/resource-graph/first-query-portal.md).

Si la siguiente consulta se ejecuta en el explorador de Resource Graph, este devuelve una lista de cuentas de almacenamiento y muestra la configuración de acceso con clave compartida de cada cuenta:

```kusto
resources
| where type =~ 'Microsoft.Storage/storageAccounts'
| extend allowSharedKeyAccess = parse_json(properties).allowSharedKeyAccess
| project subscriptionId, resourceGroup, name, allowSharedKeyAccess
```

## <a name="permissions-for-allowing-or-disallowing-shared-key-access"></a>Permisos para admitir o denegar el acceso con clave compartida

Para establecer la propiedad **AllowSharedKeyAccess** para la cuenta de almacenamiento, un usuario debe tener permisos para crear y administrar cuentas de almacenamiento. Los roles de control de acceso basado en rol de Azure (Azure RBAC) que proporcionan estos permisos incluyen la acción **Microsoft.Storage/storageAccounts/write** o **Microsoft.Storage/storageAccounts/\*** . Los roles integrados con esta acción incluyen:

- El rol [Propietario](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager
- El rol [Colaborador](../../role-based-access-control/built-in-roles.md#contributor) de Azure Resource Manager
- El rol [Colaborador de la cuenta de almacenamiento](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Estos roles no proporcionan acceso a los datos de una cuenta de almacenamiento a través de Azure Active Directory (Azure AD). Sin embargo, incluyen **Microsoft.Storage/storageAccounts/listkeys/action**, que concede acceso a las claves de acceso de la cuenta. Con este permiso, un usuario puede usar las claves de acceso de la cuenta para acceder a todos los datos de una cuenta de almacenamiento.

Las asignaciones de roles deben tener el ámbito del nivel de la cuenta de almacenamiento o superior para permitir que un usuario permita o deniegue el acceso con clave compartida para la cuenta de almacenamiento. Para obtener más información sobre el ámbito de los roles, vea [Comprensión del ámbito para RBAC de Azure](../../role-based-access-control/scope-overview.md).

Tenga cuidado de restringir la asignación de estos roles solo a aquellos usuarios que requieran la capacidad de crear una cuenta de almacenamiento o actualizar sus propiedades. Use el principio de privilegios mínimos para asegurarse de que los usuarios tienen los permisos mínimos que necesitan para realizar sus tareas. Para más información sobre la administración del acceso con RBAC de Azure, consulte [Procedimientos recomendados para RBAC de Azure](../../role-based-access-control/best-practices.md).

> [!NOTE]
> Los roles clásicos de administrador de suscripciones Administrador del servicio y Coadministrador equivalen al rol [Propietario](../../role-based-access-control/built-in-roles.md#owner) de Azure Resource Manager. El rol **Propietario** incluye todas las acciones, por lo que un usuario con uno de estos roles administrativos también puede crear y administrar cuentas de almacenamiento. Para más información, consulte [Roles de administrador de suscripciones clásico, de Azure y de administrador de Azure AD](../../role-based-access-control/rbac-and-directory-admin-roles.md#classic-subscription-administrator-roles).

## <a name="understand-how-disallowing-shared-key-affects-sas-tokens"></a>Descripción de cómo la denegación de la clave compartida afecta a los tokens de SAS

Cuando el acceso con clave compartida se deniega para la cuenta de almacenamiento, Azure Storage controla los tokens de SAS según el tipo de SAS y el servicio al que se destina la solicitud. En la tabla siguiente se muestra cómo se autoriza cada tipo de SAS y cómo Azure Storage controlará esa SAS cuando la propiedad **AllowSharedKeyAccess** de la cuenta de almacenamiento sea **false**.

| Tipo de SAS | Tipo de autorización | Comportamiento cuando AllowSharedKeyAccess es false |
|-|-|-|
| SAS de delegación de usuarios (solo para Blob Storage) | Azure AD | La solicitud se permite. Microsoft recomienda el uso de una SAS de delegación de usuario siempre que sea posible para mayor seguridad. |
| SAS de servicio | Clave compartida | La solicitud se deniega para todos los servicios de Azure Storage. |
| SAS de cuenta | Clave compartida | La solicitud se deniega para todos los servicios de Azure Storage. |

Las métricas y el registro de Azure en Azure Monitor no distinguen entre los distintos tipos de firmas de acceso compartido. El filtro **SAS** en el Explorador de métricas de Azure y el campo **SAS** en el registro de Azure Storage en Azure Monitor informan de las solicitudes que están autorizadas con cualquier tipo de SAS. Sin embargo, los distintos tipos de firmas de acceso compartido se autorizan de manera diferente y se comportan de manera distinta cuando se deniega el acceso con clave compartida:

- Un token de SAS de servicio o un token de SAS de cuenta está autorizado con clave compartida y no se permitirá en una solicitud a Blob Storage cuando la propiedad **AllowSharedKeyAccess** esté establecida en **false**.
- Una SAS de delegación de usuario está autorizada con Azure AD y se permitirá en una solicitud a Blob Storage cuando la propiedad **AllowSharedKeyAccess** esté establecida en **false**.

Cuando evalúe el tráfico a la cuenta de almacenamiento, tenga en cuenta que las métricas y los registros tal y como se describen en [Detección del tipo de autorización que usan las aplicaciones cliente](#detect-the-type-of-authorization-used-by-client-applications) pueden incluir solicitudes realizadas con una SAS de delegación de usuario.

Para obtener más información sobre las firmas de acceso compartido, consulte [Otorgar acceso limitado a recursos de Azure Storage con firmas de acceso compartido (SAS)](storage-sas-overview.md).

## <a name="consider-compatibility-with-other-azure-tools-and-services"></a>Consideración de la compatibilidad con otras herramientas y servicios

Varios servicios de Azure usan la autorización con clave compartida para comunicarse con Azure Storage. Si deniega la autorización con clave compartida para una cuenta de almacenamiento, estos servicios no podrán acceder a los datos de esa cuenta y las aplicaciones pueden verse negativamente afectadas.

Algunas herramientas de Azure ofrecen la opción de usar la autorización Azure AD para acceder a Azure Storage. En la tabla siguiente se enumeran algunas herramientas conocidas de Azure y se indica si pueden usar Azure AD para autorizar solicitudes a Azure Storage.

| Herramienta de Azure | Autorización de Azure AD para Azure Storage |
|-|-|
| Azure portal | Compatible. Para obtener información sobre cómo autorizar con su cuenta de Azure AD desde Azure Portal, consulte [Elija cómo autorizar el acceso a los datos de blob en Azure Portal](../blobs/authorize-data-operations-portal.md). |
| AzCopy | Compatible con el Blob Storage. Para obtener información sobre cómo autorizar operaciones de AzCopy, consulte [Elija cómo autorizar el acceso a los datos de blob o de cola en Azure Portal](storage-use-azcopy-v10.md#choose-how-youll-provide-authorization-credentials) en la documentación de AzCopy. |
| Explorador de Azure Storage | Solo compatible con Blob Storage y Azure Data Lake Storage Gen2. El acceso con Azure AD a Queue Storage no se admite. Asegúrese de seleccionar el inquilino de Azure AD correcto. Para más información, consulte [Introducción al Explorador de Storage](../../vs-azure-tools-storage-manage-with-storage-explorer.md?tabs=windows#sign-in-to-azure). |
| Azure PowerShell | Compatible. Para obtener información sobre la forma de autorizar comandos de PowerShell para las operaciones de blobs o colas con Azure AD, consulte los artículos [Ejecución de comandos de PowerShell con credenciales de Azure AD para acceder a datos de blob](../blobs/authorize-data-operations-powershell.md) o [Ejecución de comandos de PowerShell con credenciales de Azure AD para acceder los datos de la cola](../queues/authorize-data-operations-powershell.md). |
| Azure CLI | Compatible. Para información sobre cómo autorizar comandos de la CLI de Azure con Azure AD para el acceso a datos de blobs y colas, consulte [Elección de cómo autorizar el acceso a los datos de blobs o colas con la CLI de Azure](../blobs/authorize-data-operations-cli.md). |
| Azure IoT Hub | Compatible. Para más información, consulte [Compatibilidad de IoT Hub con redes virtuales mediante Private Link e identidad administrada](../../iot-hub/virtual-network-support.md). |
| Azure Cloud Shell | Azure Cloud Shell es un shell integrado en Azure Portal. Azure Cloud Shell hospeda archivos para la persistencia en un recurso compartido de archivos de Azure en una cuenta de almacenamiento. Estos archivos dejarán de estar accesibles si la autorización con clave compartida se deniega para esa cuenta de almacenamiento. Para más información, consulte [Conexión con el almacenamiento de Microsoft Azure Files](../../cloud-shell/overview.md#connect-your-microsoft-azure-files-storage). <br /><br /> Para ejecutar comandos en Azure Cloud Shell a fin de administrar cuentas de almacenamiento para las que se deniega el acceso con clave compartida, primero debe asegurarse de que se le han concedido los permisos necesarios para estas cuentas mediante RBAC de Azure. Para más información, consulte [¿Qué es el control de acceso basado en rol de Azure (RBAC)?](../../role-based-access-control/overview.md) |

## <a name="transition-azure-files-and-table-storage-workloads"></a>Migración de cargas de trabajo de Azure Files y almacenamiento de tablas

Azure Storage admite la autorización de Azure AD solo para solicitudes de Blob Storage y Queue Storage. Si impide la autorización con clave compartida para una cuenta de almacenamiento, se producirá un error en las solicitudes a Azure Files o al almacenamiento de tablas que usan la autorización con clave compartida. Dado que Azure Portal siempre usa la autorización con clave compartida para acceder a los datos de archivos y tablas, si se deniega la autorización con clave compartida para la cuenta de almacenamiento, no podrá acceder a los datos de archivos o tablas en Azure Portal.

Microsoft recomienda migrar los datos de Azure Files o Table Storage a una cuenta de almacenamiento independiente antes de denegar el acceso a la cuenta a través de la clave compartida, o bien que no aplique esta configuración a las cuentas de almacenamiento que admiten cargas de trabajo de Azure Files o Table Storage.

Denegar el acceso con clave compartida para una cuenta de almacenamiento no afecta a las conexiones SMB a Azure Files.

## <a name="next-steps"></a>Pasos siguientes

- [Autorización del acceso a datos en Azure Storage](./authorize-data-access.md)
- [Autorización del acceso a blobs y colas con Azure Active Directory](authorize-data-access.md)
- [Autorización con clave compartida](/rest/api/storageservices/authorize-with-shared-key)