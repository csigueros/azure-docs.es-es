---
title: Carga de datos de uso, métricas y registros en Azure
description: Carga del inventario de recursos, los datos de uso, las métricas y los registros en Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 6f314b8918b415c0449722d1229c6de47af1cac5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741441"
---
# <a name="upload-usage-data-metrics-and-logs-to-azure"></a>Carga de datos de uso, métricas y registros en Azure

Puede exportar periódicamente la información de uso con fines de facturación, supervisión de métricas y registros, y después cargarla en Azure. La exportación y la carga de cualquiera de estos tres tipos de datos también creará y actualizará los recursos del controlador de datos, la instancia administrada de SQL y el grupo de servidores de Hiperescala de SQL en Azure.

Antes de poder cargar datos, métricas o registros de uso, debe completar lo siguiente:

* Instalación de herramientas 
* [Registro del `Microsoft.AzureArcData`proveedor de recursos](#register-the-resource-provider) 
* [Creación de la entidad de servicio](#create-service-principal)

## <a name="install-tools"></a>Instalación de herramientas

Las herramientas necesarias incluyen: 
* CLI de Azure (az) 
* Extensión `arcdata` 

Consulte [Instalación de herramientas](./install-client-tools.md).

## <a name="register-the-resource-provider"></a>Registrar el proveedor de recursos

Antes de cargar métricas o datos de usuario en Azure, debe asegurarse de que su suscripción de Azure tiene registrado el proveedor de recursos `Microsoft.AzureArcData`.

Para comprobar el proveedor de recursos, ejecute el siguiente comando:

```azurecli
az provider show -n Microsoft.AzureArcData -o table
```

Si el proveedor de recursos no está registrado actualmente en la suscripción, puede registrarlo. Para registrarlo, ejecute el siguiente comando:  Este comando puede tardar un minuto o dos en completarse.

```azurecli
az provider register -n Microsoft.AzureArcData --wait
```

## <a name="create-service-principal"></a>Creación de una entidad de servicio

La entidad de servicio se usa para cargar datos y métricas de uso.

Use estos comandos para crear la entidad de servicio de carga de métricas:

> [!NOTE]
> Para crear una entidad de servicio se necesitan [determinados permisos en Azure](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app).

Para crear una entidad de servicio, actualice el siguiente ejemplo. Remplace `<ServicePrincipalName>`, `SubscriptionId` y `resourcegroup` por sus valores y ejecute el comando:

```azurecli
az ad sp create-for-rbac --name <ServicePrincipalName> --role Contributor --scopes /subscriptions/<SubscriptionId>/resourceGroups/<resourcegroup>
```

Si ha creado la entidad de servicio anteriormente y solo necesita obtener las credenciales actuales, ejecute el siguiente comando para restablecer las credenciales.

```azurecli
az ad sp credential reset --name <ServicePrincipalName>
```

Por ejemplo, para crear una entidad de servicio denominada `azure-arc-metrics`, ejecute el comando siguiente:

```azurecli
az ad sp create-for-rbac --name azure-arc-metrics --role Contributor --scopes /subscriptions/a345c178a-845a-6a5g-56a9-ff1b456123z2/resourceGroups/myresourcegroup
```

Salida de ejemplo:

```output
"appId": "2e72adbf-de57-4c25-b90d-2f73f126e123",
"displayName": "azure-arc-metrics",
"name": "http://azure-arc-metrics",
"password": "5039d676-23f9-416c-9534-3bd6afc78123",
"tenant": "72f988bf-85f1-41af-91ab-2d7cd01ad1234"
```

Guarde los valores `appId`, `password` y `tenant` en una variable de entorno para su uso posterior. 

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_CLIENT_ID=<appId>
SET SPN_CLIENT_SECRET=<password>
SET SPN_TENANT_ID=<tenant>
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_CLIENT_ID='<appId>'
export SPN_CLIENT_SECRET='<password>'
export SPN_TENANT_ID='<tenant>'
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_CLIENT_ID="<appId>"
$Env:SPN_CLIENT_SECRET="<password>"
$Env:SPN_TENANT_ID="<tenant>"
```

::: zone-end

Una vez creada la entidad de servicio, asígnele el rol adecuado. 

## <a name="assign-roles-to-the-service-principal"></a>Asignación de roles a la entidad de servicio

Ejecute este comando para asignar la entidad de servicio al rol `Monitoring Metrics Publisher` en la suscripción donde se encuentran los recursos de la instancia de base de datos:

::: zone pivot="client-operating-system-windows-command"

> [!NOTE]
> Debe usar comillas dobles para los nombres de rol cuando se ejecuta desde un entorno de Windows.

```azurecli
az role assignment create --assignee <appId> --role "Monitoring Metrics Publisher" --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>

```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```azurecli
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```powershell
az role assignment create --assignee <appId> --role 'Monitoring Metrics Publisher' --scope subscriptions/<SubscriptionID>/resourceGroups/<resourcegroup>
```

::: zone-end

Salida de ejemplo:

```output
{
  "canDelegate": null,
  "id": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleAssignments/f82b7dc6-17bd-4e78-93a1-3fb733b912d",
  "name": "f82b7dc6-17bd-4e78-93a1-3fb733b9d123",
  "principalId": "5901025f-0353-4e33-aeb1-d814dbc5d123",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<Subscription ID>/providers/Microsoft.Authorization/roleDefinitions/3913510d-42f4-4e42-8a64-420c39005123",
  "scope": "/subscriptions/<Subscription ID>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="verify-service-principal-role"></a>Comprobación del rol de la entidad de servicio

```azurecli
az role assignment list -o table
```

Con la entidad de servicio asignada al rol adecuado, puede continuar con la carga de métricas o datos de usuario. 



## <a name="upload-logs-metrics-or-usage-data"></a>Carga de registros, métricas o datos de uso

Los pasos específicos para cargar registros, métricas o datos de uso varían en función del tipo de información que cargue. 

[Carga de registros a Azure Monitor](upload-logs.md)

[Carga de métricas a Azure Monitor](upload-metrics.md)

[Carga de datos de uso en Azure](upload-usage-data.md)

## <a name="general-guidance-on-exporting-and-uploading-usage-and-metrics"></a>Instrucciones generales sobre cómo exportar y cargar los datos de uso y las métricas

Las operaciones de creación, lectura, actualización y eliminación (CRUD) en los servicios de datos habilitados para Azure Arc se registran con fines de facturación y supervisión. Hay servicios en segundo plano que supervisan para estas operaciones CRUD y calculan el consumo adecuadamente. El cálculo real del uso o del consumo tiene lugar de forma programada y se realiza en segundo plano. 

Cargue el uso solo una vez al día. Cuando la información de uso se exporta y se carga varias veces dentro del mismo período de 24 horas, solo se actualiza el inventario de recursos en Azure Portal, pero no en el uso de recursos.

En el caso de la carga de métricas, Azure Monitor solo acepta los últimos 30 minutos de datos ([Más información](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). Las instrucciones para cargar métricas es cargar las métricas inmediatamente después de crear el archivo de exportación para que pueda ver todo el conjunto de datos en Azure Portal. Por ejemplo, si exportó las métricas a las 14:00 horas y ejecutó el comando de carga a las 14:50. Como Azure Monitor solo acepta datos de los últimos 30 minutos, es posible que no vea ningún dato en el portal. 

## <a name="next-steps"></a>Pasos siguientes

[Más información sobre las entidades de servicio](/powershell/azure/azurerm/create-azure-service-principal-azureps#what-is-a-service-principal)

[Carga de datos de facturación en Azure y visualización en Azure Portal](view-billing-data-in-azure.md)

[Visualización del recurso de controlador de datos de Azure Arc en Azure Portal](view-data-controller-in-azure-portal.md)
