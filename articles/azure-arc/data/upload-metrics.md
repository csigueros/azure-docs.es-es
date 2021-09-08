---
title: Carga de métricas a Azure Monitor
description: Carga de las métricas de los servicios de datos habilitados para Azure Arc en Azure Monitor
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 56eed522dc28b29f24e97a94a03e848d5cf58898
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121725665"
---
# <a name="upload-metrics-to-azure-monitor"></a>Carga de métricas a Azure Monitor

Puede exportar las métricas de supervisión de forma periódica y, a continuación, cargarlas en Azure. La exportación y la carga de los datos crea y actualiza los recursos del controlador de datos, la instancia administrada de SQL y el grupo de servidores Hiperescala de PostgreSQL en Azure.


## <a name="prerequisites"></a>Requisitos previos

Antes de continuar, asegúrese de que ha creado la entidad de servicio necesaria y de que le ha asignado un rol adecuado. Para obtener detalles, consulte:
* [Creación de una entidad de servicio](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Asignación de roles a la entidad de servicio](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-metrics"></a>Carga de métricas

Con los servicios de datos de Azure Arc, opcionalmente puede cargar las métricas en Azure Monitor a fin de agregar y analizar métricas, generar alertas, enviar notificaciones o desencadenar acciones automatizadas. 

El envío de los datos a Azure Monitor también permite almacenar los datos de las métricas fuera del sitio y a gran escala, lo que permite el almacenamiento a largo plazo de los datos para realizar análisis avanzados.

Si tiene varios sitios con servicios de datos de Azure Arc, puede usar Azure Monitor como ubicación central para recopilar todos los registros y las métricas de todos los sitios.

## <a name="set-final-environment-variables-and-confirm"></a>Establecimiento de las variables de entorno finales y confirmación

Establezca la dirección URL de la autoridad de SPN en una variable de entorno:

::: zone pivot="client-operating-system-windows-command"

```console
SET SPN_AUTHORITY=https://login.microsoftonline.com
```

::: zone-end

::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
export SPN_AUTHORITY='https://login.microsoftonline.com'
```

::: zone-end

Si quiere, asegúrese de que se hayan establecido todas las variables de entorno necesarias:


::: zone pivot="client-operating-system-powershell"

```PowerShell
$Env:SPN_TENANT_ID
$Env:SPN_CLIENT_ID
$Env:SPN_CLIENT_SECRET
$Env:SPN_AUTHORITY
```


::: zone-end

::: zone pivot="client-operating-system-macos-and-linux"

```console
echo $SPN_TENANT_ID
echo $SPN_CLIENT_ID
echo $SPN_CLIENT_SECRET
echo $SPN_AUTHORITY
```

::: zone-end

::: zone pivot="client-operating-system-windows-command"

```console
echo %SPN_TENANT_ID%
echo %SPN_CLIENT_ID%
echo %SPN_CLIENT_SECRET%
echo %SPN_AUTHORITY%
```

::: zone-end

## <a name="upload-metrics-to-azure-monitor"></a>Carga de métricas a Azure Monitor

Para cargar las métricas de las instancias administradas de SQL habilitadas para Azure Arc y los grupos de servidores de Hiperescala de PostgreSQL habilitados para Azure Arc, ejecute los siguientes comandos de la CLI:

1. Inicie sesión en el controlador de datos con `azdata`.
 
1. Exporte todas las métricas al archivo especificado:

> [!NOTE]
> La exportación de información de uso o facturación, métricas y registros mediante el comando `az arcdata dc export` requiere omitir la comprobación de SSL por ahora.  Se le pedirá que omita la comprobación de SSL, o bien puede establecer la variable de entorno `AZDATA_VERIFY_SSL=no` para evitar que se le solicite.  Actualmente no hay ninguna manera de configurar un certificado SSL para la API de exportación del controlador de datos.

   ```azurecli
   az arcdata dc export --type metrics --path metrics.json
   ```

2. Cargue las métricas en Azure Monitor:

   ```azurecli
   az arcdata dc upload --path metrics.json
   ```

   >[!NOTE]
   >Espere al menos 30 minutos después de que se creen las instancias de datos habilitadas para Azure Arc para la primera carga.
   >
   >Asegúrese de `upload` las métricas inmediatamente después de `export` ya que Azure Monitor solo acepta métricas para los últimos 30 minutos. [Más información](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting).


Si ve un error que indica "Error al obtener métricas" durante la exportación, compruebe si la recopilación de datos está establecida en `true`, ejecutando el comando siguiente:

```azurecli
az arcdata dc config show
```

Consulte la "sección de seguridad".

```output
 "security": {
      "allowDumps": true,
      "allowNodeMetricsCollection": true,
      "allowPodMetricsCollection": true,
      "allowRunAsRoot": false
    },
```

Compruebe que las propiedades `allowNodeMetricsCollection` y `allowPodMetricsCollection` estén establecidas en `true`.

## <a name="view-the-metrics-in-the-portal"></a>Visualización de las métricas en el portal

Una vez que se carguen las métricas, puede verlas en Azure Portal.
> [!NOTE]
> Tenga en cuenta que los datos cargados pueden tardar un par de minutos en procesarse antes de que pueda ver las métricas en el portal.


Para ver las métricas en el portal, use este vínculo para abrirlo: <https://portal.azure.com> Después, busque la instancia de base de datos por nombre en la barra de búsqueda:

Puede ver el uso de la CPU en la página Información general o bien, si quiere métricas más detalladas, puede hacer clic en Métricas en el panel de navegación de la izquierda.

Elija SQL Server como el espacio de nombres de la métrica:

Seleccione la métrica que quiera visualizar (también puede seleccionar varias):

Cambie la frecuencia a los últimos 30 minutos:

> [!NOTE]
> Solo puede cargar las métricas de los últimos 30 minutos. Azure Monitor rechaza las métricas de más de 30 minutos.

## <a name="automating-uploads-optional"></a>Automatización de cargas (opcional)

Si quiere cargar métricas y registros de forma programada, puede crear un script y ejecutarlo en un temporizador cada pocos minutos. A continuación se muestra un ejemplo de cómo automatizar las cargas mediante un script de shell de Linux.

En el editor de código o texto que prefiera, agregue el siguiente script al archivo y guárdelo como un archivo ejecutable de script como .sh (Linux o Mac), o bien .cmd, .bat, .ps1.

```azurecli
az arcdata dc export --type metrics --path metrics.json --force
az arcdata dc upload --path metrics.json
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

## <a name="general-guidance-on-exporting-and-uploading-usage-metrics"></a>Instrucciones generales sobre cómo exportar y cargar el uso, métricas

Las operaciones de creación, lectura, actualización y eliminación (CRUD) en los servicios de datos habilitados para Azure Arc se registran con fines de facturación y supervisión. Hay servicios en segundo plano que supervisan para estas operaciones CRUD y calculan el consumo adecuadamente. El cálculo real del uso o del consumo tiene lugar de forma programada y se realiza en segundo plano. 

Cargue el uso solo una vez al día. Cuando la información de uso se exporta y se carga varias veces dentro del mismo período de 24 horas, solo se actualiza el inventario de recursos en Azure Portal, pero no en el uso de recursos.

En el caso de la carga de métricas, Azure Monitor solo acepta los últimos 30 minutos de datos ([Más información](../../azure-monitor/essentials/metrics-store-custom-rest-api.md#troubleshooting)). Las instrucciones para cargar métricas es cargar las métricas inmediatamente después de crear el archivo de exportación para que pueda ver todo el conjunto de datos en Azure Portal. Por ejemplo, si exportó las métricas a las 14:00 horas y ejecutó el comando de carga a las 14:50. Como Azure Monitor solo acepta datos de los últimos 30 minutos, es posible que no vea ningún dato en el portal. 

## <a name="next-steps"></a>Pasos siguientes

[Carga de registros a Azure Monitor](upload-logs.md)

[Carga de datos de uso, métricas y registros en Azure Monitor](upload-usage-data.md)

[Carga de datos de facturación en Azure y visualización en Azure Portal](view-billing-data-in-azure.md)

[Visualización del recurso de controlador de datos de Azure Arc en Azure Portal](view-data-controller-in-azure-portal.md)
