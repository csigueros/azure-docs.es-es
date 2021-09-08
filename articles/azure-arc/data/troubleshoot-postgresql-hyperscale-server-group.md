---
title: Solución de problemas de grupos de servidores de Hiperescala de PostgreSQL
description: Solución de problemas de grupos de servidores de Hiperescala de PostgreSQL con un cuaderno de Jupyter Notebook
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: b828d9e5765a180e1b42de9b96a0ee06eab085f8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733497"
---
# <a name="troubleshooting-postgresql-hyperscale-server-groups"></a>Solución de problemas de grupos de servidores de Hiperescala de PostgreSQL
En este artículo se describen algunas técnicas que puede usar para solucionar problemas con el grupo de servidores. Además de este artículo, puede consultar cómo usar [Kibana](monitor-grafana-kibana.md) para buscar los registros o cómo usar [Grafana](monitor-grafana-kibana.md) para visualizar las métricas del grupo de servidores. 

## <a name="getting-more-details-about-the-execution-of-a-cli-command"></a>Más detalles sobre la ejecución de un comando de la CLI
Puede agregar el parámetro **--debug** a cualquier comando de la CLI que ejecute. Si lo hace, se mostrará en la consola información adicional sobre la ejecución de ese comando. Le resultará útil obtener detalles para ayudarle a entender el comportamiento de ese comando.
Por ejemplo, podría ejecutar
```azurecli
az postgres arc-server create -n postgres01 -w 2 --debug --k8s-namespace <namespace> --use-k8s
```

o
```azurecli
az postgres arc-server edit -n postgres01 --extension --k8s-namespace <namespace> --use-k8s SomeExtensionName --debug
```

Además, puede usar el parámetro --help en cualquier comando de la CLI para mostrar ayuda y la lista de parámetros de un comando concreto. Por ejemplo:
```azurecli
az postgres arc-server create --help
```


## <a name="collecting-logs-of-the-data-controller-and-your-server-groups"></a>Recopilación de registros del controlador de datos y los grupos de servidores
Lea el artículo sobre la [obtención de registros de los servicios de datos habilitados para Azure Arc](troubleshooting-get-logs.md).



## <a name="interactive-troubleshooting-with-jupyter-notebooks-in-azure-data-studio"></a>Solución de problemas interactiva con cuadernos de Jupyter en Azure Data Studio

Los cuadernos pueden documentar los procedimientos mediante la inclusión de contenido de marcado para describir qué hacer y cómo hacerlo. También pueden proporcionar código ejecutable para automatizar un procedimiento.  Este patrón es útil para todo, desde los procedimientos operativos estándar hasta las guías de solución de problemas.

Por ejemplo, supongamos que va a solucionar problemas de un grupo de servidores de Hiperescala de PostgreSQL que podría tener algunos problemas al usar Azure Data Studio.

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

[!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

### <a name="install-tools"></a>Instalación de herramientas

Instale Azure Data Studio, `kubectl` y la CLI de Azure (`az`) con la extensión `arcdata` en la máquina cliente que usa para ejecutar el cuaderno en Azure Data Studio. Para ello, siga las instrucciones que se indican en [Instalación de las herramientas de cliente](install-client-tools.md).

### <a name="update-the-path-environment-variable"></a>Actualización de la variable de entorno PATH

Asegúrese de que estas herramientas se pueden invocar desde cualquier parte de este equipo cliente. Por ejemplo, en un equipo cliente con Windows, actualice la variable de entorno del sistema PATH y agregue la carpeta en la que ha instalado kubectl.

### <a name="log-into-your-kubernetes-cluster-with-kubectl"></a>Inicio de sesión en el clúster de Kubernetes con kubectl

Para ello, puede usar los comandos de ejemplo que se proporcionan en [esta](https://blog.christianposta.com/kubernetes/logging-into-a-kubernetes-cluster-with-kubectl/) entrada de blog.
Ejecutaría comandos como:

```console
kubectl config view
kubectl config set-credentials kubeuser/my_kubeuser --username=<your Arc Data Controller Admin user name> --password=<password>
kubectl config set-cluster my_kubeuser --server=https://<IP address>:<port>
kubectl config set-context default/my_kubeuser/ArcDataControllerAdmin --user=ArcDataControllerAdmin/my_kubeuser --namespace=arc --cluster=my_kubeuser
kubectl config use-context default/my_kubeuser/ArcDataControllerAdmin
```

#### <a name="the-troubleshooting-notebook"></a>Cuaderno de solución de problemas

Inicie Azure Data Studio y abra el cuaderno de solución de problemas. 

Implemente los pasos descritos en [033-manage-Postgres-with-AzureDataStudio.md](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md) para:

1. Conexión al controlador de datos de Arc
2. Haga clic con el botón derecho en la instancia de Postgres y elija **[Manage]** (Administrar).
3. Seleccione el **panel [Diagnose and solve problems]** (Diagnosticar y solucionar problemas).
4. Seleccione el vínculo **[Troubleshoot]** (Solucionar problemas).

:::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook.jpg" alt-text="Azure Data Studio: abrir cuaderno de solución de problemas de PostgreSQL":::

Se abre el **cuaderno TSG100: solucionador de problemas de Hiperescala de PostgreSQL habilitada para Azure Arc**: :::image type="content" source="media/postgres-hyperscale/ads-controller-postgres-troubleshooting-notebook2.jpg" alt-text="Azure Data Studio: uso del cuaderno de solución de problemas de PostgreSQL":::

#### <a name="run-the-scripts"></a>Ejecución de los scripts
Seleccione el botón "Ejecutar todo" situado en la parte superior para ejecutar todo el cuaderno de una vez o bien puede recorrerlo paso a paso y ejecutar cada celda de código una por una.

Examine el resultado de la ejecución de las celdas de código para ver si hay algún problema potencial.

Agregaremos más detalles al cuaderno a lo largo del tiempo sobre cómo reconocer problemas comunes y cómo resolverlos.

## <a name="next-step"></a>Paso siguiente
- Más información sobre la [obtención de registros de los servicios de datos habilitados para Azure Arc](troubleshooting-get-logs.md)
- Más información sobre [Búsqueda de registros con Kibana](monitor-grafana-kibana.md)
- Más información sobre [Supervisión con Grafana](monitor-grafana-kibana.md)
- Creación de sus propios cuadernos
