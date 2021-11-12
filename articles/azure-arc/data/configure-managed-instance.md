---
title: Configuración de una instancia de SQL Managed Instance habilitada para Azure Arc
description: Configuración de una instancia de SQL Managed Instance habilitada para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: d4dc8843804d48fb98fef7cd336e6b56f54f49ae
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131554362"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Configuración de una instancia de SQL Managed Instance habilitada para Azure Arc

En este artículo se explica cómo configurar la instancia de SQL Managed Instance habilitada para Azure Arc.


## <a name="configure-resources-such-as-cores-memory"></a>Configuración de recursos como núcleos, memoria


### <a name="configure-using-cli"></a>Configuración mediante la CLI

Puede editar la configuración de las instancias de SQL Managed Instance habilitada para Azure Arc con la CLI. Ejecute el siguiente comando para ver las opciones de configuración. 

```azurecli
az sql mi-arc edit --help
```

Puede actualizar la memoria y los núcleos disponibles de una instancia de SQL Managed Instance habilitada para Azure Arc mediante el siguiente comando:

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

En el ejemplo siguiente se establecen las solicitudes y límites de CPU, núcleos y memoria.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n sqlinstance1 --k8s-namespace arc --use-k8s
```

Para ver los cambios realizados en la instancia de SQL Managed Instance habilitada para Azure Arc, puede usar los comandos siguientes para ver el archivo de configuración YAML:

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>Configuración de las opciones del servidor

Puede configurar las opciones de configuración del servidor para la instancia de SQL Managed Instance habilitada para Azure Arc después de su creación. En este artículo se describe cómo configurar opciones como habilitar o deshabilitar el agente mssql o habilitar marcas de seguimiento específicas para escenarios de solución de problemas.


### <a name="enable-sql-server-agent"></a>Habilitación del Agente SQL Server

De manera predeterminada, el Agente SQL Server está deshabilitado. Para habilitarlo, se puede ejecutar el comando siguiente:

```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --agent-enabled true
```
Por ejemplo:
```azurecli
az sql mi-arc edit -n sqlinstance1 --k8s-namespace arc --use-k8s --agent-enabled true
```

### <a name="enable-trace-flags"></a>Habilitar marcas de seguimiento

Las marcas de seguimiento se pueden habilitar de la siguiente manera:
```azurecli
az sql mi-arc edit -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s --trace-flags "3614,1234" 
```

