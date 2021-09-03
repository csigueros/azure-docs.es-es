---
title: Carga de datos de uso en Azure
description: Carga de datos de uso de los servicios de datos habilitados para Azure Arc en Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
zone_pivot_groups: client-operating-system-macos-and-linux-windows-powershell
ms.openlocfilehash: 74df592db61e4c9c50f9b199d7803fb8e1481878
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121732159"
---
# <a name="upload-usage-data-to-azure"></a>Carga de datos de uso en Azure

Puede exportar la información de uso de forma periódica. La exportación y la carga de esta información crea y actualiza los recursos del controlador de datos, la instancia administrada de SQL y el grupo de servidores Hiperescala de PostgreSQL en Azure.

> [!NOTE] 
> Durante el período de versión preliminar, los servicios de datos habilitados para Azure Arc no suponen ningún costo.



> [!NOTE]
> Espere al menos 24 horas después de crear el controlador de datos de Azure Arc antes de cargar los datos de uso.

## <a name="create-service-principal-and-assign-roles"></a>Creación de una entidad de servicio y asignación de roles

Antes de continuar, asegúrese de que ha creado la entidad de servicio necesaria y de que le ha asignado un rol adecuado. Para obtener detalles, consulte:
* [Creación de una entidad de servicio](upload-metrics-and-logs-to-azure-monitor.md#create-service-principal).
* [Asignación de roles a la entidad de servicio](upload-metrics-and-logs-to-azure-monitor.md#assign-roles-to-the-service-principal)

## <a name="upload-usage-data"></a>Carga de datos de uso

La información de uso, como el inventario y el uso de recursos, se puede cargar en Azure con el siguiente método de dos pasos:

1. Exporte los datos de uso mediante comando `az arcdata dc export`, como se indica a continuación:

> [!NOTE]
> La exportación de información de uso/facturación, métricas y registros mediante el comando `az arcdata dc export` requiere omitir la comprobación de SSL por ahora.  Se le pedirá que omita la comprobación de SSL, pero puede establecer la variable de entorno `AZDATA_VERIFY_SSL=no` para evitar que se le solicite.  Actualmente no hay ninguna manera de configurar un certificado SSL para la API de exportación del controlador de datos.

   ```azurecli
   az arcdata dc export --type usage --path usage.json --k8s-namespace <namespace> --use-k8s
   ```
 
   Este comando crea un archivo `usage.json` con todos los recursos de datos habilitados para Azure Arc, como las instancias administradas de SQL y las instancias de Hiperescala de PostgreSQL, etc., que se han creado en el controlador de datos.

2. Cargue los datos de uso mediante el comando `upload`.

   ```azurecli
   az arcdata dc upload --path usage.json
   ```

## <a name="automating-uploads-optional"></a>Automatización de cargas (opcional)

Si quiere cargar métricas y registros de forma programada, puede crear un script y ejecutarlo en un temporizador cada pocos minutos. A continuación se muestra un ejemplo de cómo automatizar las cargas mediante un script de shell de Linux.

En el editor de código o texto que prefiera, agregue el siguiente script al archivo y guárdelo como un archivo ejecutable de script como `.sh` (Linux o Mac) o bien `.cmd`, `.bat` o `.ps1`.

```azurecli
az arcdata dc export --type usage --path usage.json --force --k8s-namespace <namespace> --use-k8s
az arcdata dc upload --path usage.json
```

Conversión del archivo de script en ejecutable

```console
chmod +x myuploadscript.sh
```

Ejecute el script todos los días para su uso:

```console
watch -n 1200 ./myuploadscript.sh
```

También puede usar un programador de trabajos como cron o el Programador de tareas de Windows, o bien un orquestador como Ansible, Puppet o Chef.

## <a name="next-steps"></a>Pasos siguientes

[Carga de métricas en Azure Monitor](upload-metrics.md)

[Carga de registros a Azure Monitor](upload-logs.md)

[Carga de datos de facturación en Azure y visualización en Azure Portal](view-billing-data-in-azure.md)

[Visualización del recurso de controlador de datos de Azure Arc en Azure Portal](view-data-controller-in-azure-portal.md)
