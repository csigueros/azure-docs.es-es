---
title: Carga de registros a Azure Monitor
description: Carga de registros de los servicios de datos habilitados para Azure Arc en Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 812b6add11ac032eb6dffea7de54111574393cf9
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733491"
---
# <a name="upload-logs-to-azure-monitor"></a>Carga de registros a Azure Monitor

Puede exportar los registros de forma periódica y, a continuación, cargarlos en Azure. La exportación y la carga de los registros también crea y actualiza los recursos del controlador de datos, la instancia administrada de SQL y el grupo de servidores Hiperescala de PostgreSQL en Azure.

## <a name="before-you-begin"></a>Antes de empezar

Antes de poder cargar los registros, debe hacer lo siguiente: 

1. [Cree un área de trabajo de Log Analytics](#create-a-log-analytics-workspace)
1. [Asignación del identificador y la clave compartida a variables de entorno](#assign-id-and-shared-key-to-environment-variables)

## <a name="create-a-log-analytics-workspace"></a>Creación de un área de trabajo de Log Analytics

Ejecute estos comandos para crear un área de trabajo de Log Analytics y establecer la información de acceso en variables de entorno.

> [!NOTE]
> Si ya tiene un área de trabajo, omita este paso.

```azurecli
az monitor log-analytics workspace create --resource-group <resource group name> --workspace-name <some name you choose>
```

Salida de ejemplo:

```output
{
  "customerId": "d6abb435-2626-4df1-b887-445fe44a4123",
  "eTag": null,
  "id": "/subscriptions/<Subscription ID>/resourcegroups/user-arc-demo/providers/microsoft.operationalinsights/workspaces/user-logworkspace",
  "location": "eastus",
  "name": "user-logworkspace",
  "portalUrl": null,
  "provisioningState": "Succeeded",
  "resourceGroup": "user-arc-demo",
  "retentionInDays": 30,
  "sku": {
    "lastSkuUpdate": "Thu, 30 Jul 2020 22:37:53 GMT",
    "maxCapacityReservationLevel": 3000,
    "name": "pergb2018"
  },
  "source": "Azure",
  "tags": null,
  "type": "Microsoft.OperationalInsights/workspaces"
}
```

## <a name="assign-id-and-shared-key-to-environment-variables"></a>Asignación del identificador y la clave compartida a variables de entorno

Guarde el valor de `customerId` del área de trabajo de Log Analytics como una variable de entorno que se utilizará más adelante:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_ID=<customerId>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID='<customerId>'
```
::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_ID='<customerId>'
```
::: zone-end

Este comando devuelve las claves de acceso necesarias para conectarse al área de trabajo de Log Analytics:

```azurecli
az monitor log-analytics workspace get-shared-keys --resource-group MyResourceGroup --workspace-name MyLogsWorkpace
```

Salida de ejemplo:

```output
{
  "primarySharedKey": "JXzQp1RcGgjXFCDS3v0sXoxPvbgCoGaIv35lf11Km2WbdGFvLXqaydpaj1ByWGvKoCghL8hL4BRoypXxkLr123==",
  "secondarySharedKey": "p2XHSxLJ4o9IAqm2zINcEmx0UWU5Z5EZz8PQC0OHpFjdpuVaI0zsPbTv5VyPFgaCUlCZb2yEbkiR4eTuTSF123=="
}
```

Guarde la clave principal en una variable de entorno para usarla más adelante:

::: zone pivot="client-operating-system-windows-command"

```console
SET WORKSPACE_SHARED_KEY=<primarySharedKey>
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:WORKSPACE_SHARED_KEY='<primarySharedKey>'
```
::: zone-end


::: zone pivot="client-operating-system-macos-and-linux"

```console
export WORKSPACE_SHARED_KEY='<primarySharedKey>'
```

::: zone-end

## <a name="set-final-environment-variables-and-confirm"></a>Establecimiento de las variables de entorno finales y confirmación

Establezca la dirección URL de la autoridad de SPN en una variable de entorno:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```console
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end


## <a name="verify-environment-variables"></a>Verificación de las variables de entorno

Si quiere, asegúrese de que se hayan establecido todas las variables de entorno necesarias:


::: zone pivot="client-operating-system-windows-command"

```console
echo %WORKSPACE_ID%
echo %WORKSPACE_SHARED_KEY%
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:WORKSPACE_ID
$Env:WORKSPACE_SHARED_KEY
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $WORKSPACE_ID
echo $WORKSPACE_SHARED_KEY
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

Con las variables de entorno establecidas, puede cargar los registros en el área de trabajo de registro. 

## <a name="upload-logs-to-azure-monitor"></a>Carga de registros a Azure Monitor

 Para cargar los registros de las instancias administradas de SQL habilitadas para Azure Arc y los grupos de servidores Hiperescala de PostgreSQL habilitada para Azure Arc, ejecute los siguientes comandos de la CLI:

1. Inicie sesión en el controlador de datos de Azure Arc con la CLI de Azure (`az`) con la extensión `arcdata`.

   ```azurecli
   az arcdata login
   ```

   Siga las indicaciones para establecer el espacio de nombres, el nombre de usuario del administrador y la contraseña. 

1. Se exportan todos los registros al archivo especificado:

> [!NOTE]
> La exportación de información de uso/facturación, métricas y registros mediante el comando `az arcdata dc export` requiere omitir la comprobación de SSL por ahora.  Se le pedirá que omita la comprobación de SSL o puede establecer la variable de entorno `AZDATA_VERIFY_SSL=no` para evitar que se le solicite.  Actualmente no hay ninguna manera de configurar un certificado SSL para la API de exportación del controlador de datos.

   ```azurecli
   az arcdata dc export --type logs --path logs.json
   ```

2. Cargue los registros en un área de trabajo de Log Analytics en Azure Monitor:

   ```azurecli
   az arcdata dc upload --path logs.json
   ```

## <a name="view-your-logs-in-azure-portal"></a>Visualización de los registros en Azure Portal

Una vez que se hayan cargado los registros, debería poder consultarlos mediante el explorador de consultas de registros como se indica a continuación:

1. Abra Azure Portal, busque el área de trabajo por su nombre en la barra de búsqueda de la parte superior y, a continuación, selecciónela.
2. Seleccione Registros en el panel de la izquierda.
3. Seleccione Comenzar (o bien los vínculos de la página Comenzar para más información sobre Log Analytics si no está familiarizado con el producto).
4. Siga el tutorial para más información sobre Log Analytics si es la primera vez que utiliza Log Analytics.
5. Expanda Registros personalizados en la parte inferior de la lista de tablas y verá una tabla denominada "sql_instance_logs_CL".
6. Seleccione el icono de ojo situado junto al nombre de la tabla.
7. Seleccione el botón "Ver en el editor de consultas".
8. Ahora tendrá una consulta en el editor de consultas que mostrará los 10 eventos más recientes del registro.
9. Desde aquí, puede experimentar con la consulta de los registros mediante el editor de consultas, establecer alertas, etc.

## <a name="automating-uploads-optional"></a>Automatización de cargas (opcional)

Si quiere cargar métricas y registros de forma programada, puede crear un script y ejecutarlo en un temporizador cada pocos minutos. A continuación se muestra un ejemplo de cómo automatizar las cargas mediante un script de shell de Linux.

En el editor de código o texto que prefiera, agregue el siguiente script al archivo y guárdelo como un archivo ejecutable de script como .sh (Linux o Mac), o bien .cmd, .bat, .ps1.

```azurecli
az arcdata dc export --type logs --path logs.json --force
az arcdata dc upload --path logs.json
```

Conversión del archivo de script en ejecutable

```console
chmod +x myuploadscript.sh
```

Ejecute el script cada 20 minutos:

```console
watch -n 1200 ./myuploadscript.sh
```

También puede usar un programador de trabajos como cron o el Programador de tareas de Windows, o bien un orquestador como Ansible, Puppet o Chef.

## <a name="next-steps"></a>Pasos siguientes

[Carga de métricas en Azure Monitor](upload-metrics.md)

[Carga de datos de uso, métricas y registros en Azure Monitor](upload-usage-data.md)

[Carga de datos de facturación en Azure y visualización en Azure Portal](view-billing-data-in-azure.md)

[Visualización del recurso de controlador de datos de Azure Arc en Azure Portal](view-data-controller-in-azure-portal.md)
