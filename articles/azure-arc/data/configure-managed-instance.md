---
title: Configuración de una instancia de SQL Managed Instance habilitada para Azure Arc
description: Configuración de una instancia de SQL Managed Instance habilitada para Azure Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e84d5be7252f81c4e80d6070ada2151fcc3960f1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743887"
---
# <a name="configure-azure-arc-enabled-sql-managed-instance"></a>Configuración de una instancia de SQL Managed Instance habilitada para Azure Arc

En este artículo se explica cómo configurar la instancia de SQL Managed Instance habilitada para Azure Arc.


## <a name="configure-resources"></a>Configure resources

### <a name="configure-using-cli"></a>Configuración mediante la CLI

Puede editar la configuración de las instancias de SQL Managed Instance habilitada para Azure Arc con la CLI. Ejecute el siguiente comando para ver las opciones de configuración. 

```azurecli
az sql mi-arc edit --help
```

En el ejemplo siguiente se establecen las solicitudes y límites de CPU, núcleos y memoria.

```azurecli
az sql mi-arc edit --cores-limit 4 --cores-request 2 --memory-limit 4Gi --memory-request 2Gi -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

Para ver los cambios realizados en la instancia de SQL Managed Instance, puede usar los comandos siguientes para ver el archivo de configuración YAML:

```azurecli
az sql mi-arc show -n <NAME_OF_SQL_MI> --k8s-namespace <namespace> --use-k8s
```

## <a name="configure-server-options"></a>Configuración de las opciones del servidor

Puede configurar las opciones de configuración del servidor para la instancia de SQL Managed Instance habilitada para Azure Arc después de su creación. En este artículo se describe cómo configurar opciones como habilitar o deshabilitar el agente mssql o habilitar marcas de seguimiento específicas para escenarios de solución de problemas.

Haga lo siguiente para cambiar alguna de esas opciones:

1. Cree un archivo `mssql-custom.conf` personalizado que incluya la configuración de destino. En el ejemplo siguiente se habilita el Agente SQL y se habilita la marca de seguimiento 1204.:

   ```
   [sqlagent]
   enabled=true
   
   [traceflag]
   traceflag0 = 1204
   ```

1. Copie el archivo `mssql-custom.conf` en `/var/opt/mssql` en el contenedor `mssql-miaa` del pod `master-0`. Reemplace `<namespaceName>` por el nombre del espacio de nombres de Arc.

   ```bash
   kubectl cp mssql-custom.conf master-0:/var/opt/mssql/mssql-custom.conf -c mssql-server -n <namespaceName>
   ```

1. Reinicie la instancia de SQL Server.  Reemplace `<namespaceName>` por el nombre del espacio de nombres de Arc.

   ```bash
   kubectl exec -it master-0  -c mssql-server -n <namespaceName> -- /bin/bash
   supervisorctl restart mssql-server
   exit
   ```


**Restricciones conocidas**
- Los procedimientos anteriores requieren permisos de administrador de clústeres de Kubernetes.
