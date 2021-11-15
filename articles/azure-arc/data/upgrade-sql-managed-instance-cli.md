---
title: Actualización de una instancia administrada habilitada para Azure Arc de modo indirecto mediante la CLI
description: Actualización de una instancia administrada habilitada para Azure Arc de modo indirecto mediante la CLI
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: grrlgeek
ms.author: jeschult
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: daff60736185f448183d24207099670884402795
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131565918"
---
# <a name="upgrade-an-indirect-mode-azure-arc-enabled-managed-instance-using-the-cli"></a>Actualización de una instancia administrada habilitada para Azure Arc de modo indirecto mediante la CLI

## <a name="prerequisites"></a>Prerrequisitos

### <a name="install-tools"></a>Instalación de herramientas

Para poder continuar con las tareas de este artículo, debe instalar:

- La [CLI de Azure (az)](/cli/azure/install-azure-cli)
- La [extensión de `arcdata` para la CLI de Azure](install-arcdata-extension.md)

## <a name="limitations"></a>Limitaciones

El controlador de datos de Azure Arc debe actualizarse a la nueva versión para que la instancia administrada se pueda actualizar.

Actualmente, solo se puede actualizar una instancia administrada cada vez.

## <a name="upgrade-the-managed-instance"></a>Actualización de la instancia administrada

En primer lugar, se puede realizar un simulacro. Esto validará el esquema de versión y enumerará las instancias que se actualizarán.

````cli
az sql mi-arc upgrade --name <instance name> --k8s-namespace <namespace> --dry-run --use-k8s
````

El resultado será:

```output
Preparing to upgrade sql sqlmi-1 in namespace arc to data controller version.
****Dry Run****1 instance(s) would be upgraded by this commandsqlmi-1 would be upgraded to 20211024.1.
```

### <a name="general-purpose"></a>De uso general

Para actualizar una instancia administrada, use el siguiente comando:

````cli
az sql mi-arc upgrade --name <instance name> --desired-version <version> --k8s-namespace <namespace> --use-k8s
````

Ejemplo:

````cli
az sql mi-arc upgrade --name instance1 --target v1.0.0.20211028 --k8s-namespace arc1 --use-k8s
````

## <a name="monitor"></a>Supervisión

Puede supervisar el progreso de la actualización con kubectl o la CLI.

### <a name="kubectl"></a>kubectl

```console
kubectl describe sqlmi --namespace <namespace>
```

### <a name="cli"></a>CLI

```cli
az sql mi-arc show --name <instance name> --k8s-namespace <namespace> --use-k8s
```

### <a name="output"></a>Output

La salida del comando mostrará la información del recurso. La información de actualización estará en Estado.

Durante la actualización, ```State``` mostrará ```Updating``` y ```Running Version``` será la versión actual:

```output
Status:
  Log Search Dashboard:  https://30.88.222.48:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:sqlmi-1'))
  Metrics Dashboard:     https://30.88.221.32:3000/d/40q72HnGk/sql-managed-instance-metrics?var-hostname=sqlmi-1-0
  Observed Generation:   2
  Primary Endpoint:      30.76.129.38,1433
  Ready Replicas:        1/1
  Running Version:       v1.0.0_2021-07-30
  State:                 Updating
```

Una vez completada la actualización, ```State``` mostrará ```Ready``` y ```Running Version``` será la nueva versión:

```output
Status:
  Log Search Dashboard:  https://30.88.222.48:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:sqlmi-1'))
  Metrics Dashboard:     https://30.88.221.32:3000/d/40q72HnGk/sql-managed-instance-metrics?var-hostname=sqlmi-1-0
  Observed Generation:   2
  Primary Endpoint:      30.76.129.38,1433
  Ready Replicas:        1/1
  Running Version:       20211024.1
  State:                 Ready
```

## <a name="troubleshoot-upgrade-problems"></a>Solución de problemas de actualización

Si tiene problemas con la actualización, consulte la [guía de solución de problemas](troubleshoot-guide.md).
