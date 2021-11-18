---
title: Visualización de la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc
titleSuffix: Azure Arc-enabled data services
description: Vea la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: 0789e9bb28868ce4d75aa48beb956fcb4dd74031
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132345810"
---
# <a name="show-the-configuration-of-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Visualización de la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc

En este artículo se explica cómo mostrar la configuración de los grupos de servidores. Lo explica anticipando algunas preguntas que puede que se haga y que encuentre respuestas. En ocasiones, puede haber varias respuestas válidas. En este artículo se detallan las más comunes y otras de utilidad. Esas preguntas se agrupan por tema:

- Desde el punto de vista de Kubernetes
- Desde el punto de vista de los servicios de datos habilitados para Azure Arc

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="from-a-kubernetes-point-of-view"></a>Desde el punto de vista de Kubernetes

### <a name="what-are-the-postgres-server-groups-deployed-and-how-many-pods-are-they-using"></a>¿Cuáles son los grupos de servidores de Postgres implementados y cuántos pods usan?

Enumere los recursos de Kubernetes del tipo Postgres. Ejecute el comando:

```console
kubectl get postgresqls -n <namespace>
```

La salida de este comando muestra la lista de grupos de servidores creados. Para cada uno, se indica el número de pods. Por ejemplo:

```output
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   5/5          20.101.12.221:5432   12d
```

En este ejemplo se muestra que se crea un grupo de servidores. Se ejecuta en cinco pods: uno de coordinación y cuatro de trabajo.

### <a name="what-pods-are-used-by-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>¿Qué pods se usan en los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc?

Ejecute:

```console
kubectl get pods -n <namespace>
```

El comando devuelve la lista de pods. Verá los pods usados por los grupos de servidores en función de los nombres asignados a esos grupos de servidores. Por ejemplo:

```console
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-4jrtl   1/1     Running   0          12d
control-kz8gh        2/2     Running   0          12d
controldb-0          2/2     Running   0          12d
logsdb-0             3/3     Running   0          12d
logsui-qjkgz         3/3     Running   0          12d
metricsdb-0          2/2     Running   0          12d
metricsdc-4jslw      2/2     Running   0          12d
metricsdc-4tl2g      2/2     Running   0          12d
metricsdc-fkxv2      2/2     Running   0          12d
metricsdc-hs4h5      2/2     Running   0          12d
metricsdc-tvz22      2/2     Running   0          12d
metricsui-7pcch      2/2     Running   0          12d
postgres01c0-0       3/3     Running   0          2d19h
postgres01w0-0       3/3     Running   0          2d19h
postgres01w0-1       3/3     Running   0          2d19h
postgres01w0-2       3/3     Running   0          2d19h
postgres01w0-3       3/3     Running   0          2d19h
```

### <a name="what-pod-is-used-for-what-role-in-the-server-group"></a>¿Qué pod se usa para qué rol en el grupo de servidores?

Cualquier nombre de pod con el sufijo `c` representa un nodo de coordinación. Cualquier nombre de nodo con sufijo `w` es nodo de trabajo, como los cinco pods que hospedan el grupo de servidores:

- `postgres01c0-0`, el nodo de coordinación
- `postgres01w0-0`, un nodo de trabajo
- `postgres01w0-1`, un nodo de trabajo
- `postgres01w0-2`, un nodo de trabajo
- `postgres01w0-3`, un nodo de trabajo

Por ahora, puede omitir el carácter `0` que aparece después de `c` y `w` (ServerGroupName`c0`-x o ServerGroupName`w0`-x). Será una notación que se use cuando el producto ofrezca experiencias de alta disponibilidad.

### <a name="what-is-the-status-of-the-pods"></a>¿Cuál es el estado de los pods?

Ejecute `kubectl get pods -n <namespace>` y examine la columna `STATUS`.

### <a name="what-persistent-volume-claims-pvcs-are-being-used"></a>¿Qué notificaciones de volumen persistente (PVC) se usan?

Para comprender qué PVC se usan, y cuáles se usan para datos, registros y copias de seguridad, ejecute:

```console
kubectl get pvc -n <namespace>
```

De forma predeterminada, el prefijo del nombre de una PVC indica su uso:

- `backups-`...: es una PVC que se usa para copias de seguridad.
- `data-`...: es una PVC que se usa para archivos de datos.
- `logs-`...: es una PVC que se usa para los registros de transacciones y los archivos WAL.

Por ejemplo:

```output
NAME                                            STATUS   VOLUME              CAPACITY   ACCESS MODES   STORAGECLASS    AGE
backups-few7hh0k4npx9phsiobdc3hq-postgres01-0   Bound    local-pv-485e37db   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-1   Bound    local-pv-9d3d4a15   1938Gi     RWO            local-storage   6d6h
backups-few7hh0k4npx9phsiobdc3hq-postgres01-2   Bound    local-pv-7b8dd819   1938Gi     RWO            local-storage   6d6h
...
data-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-3c1a8cc5   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-8303ab19   1938Gi     RWO            local-storage   6d6h
data-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-55572fe6   1938Gi     RWO            local-storage   6d6h
...
logs-few7hh0k4npx9phsiobdc3hq-postgres01-0      Bound    local-pv-5e852b76   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-1      Bound    local-pv-55d309a7   1938Gi     RWO            local-storage   6d6h
logs-few7hh0k4npx9phsiobdc3hq-postgres01-2      Bound    local-pv-5ccd02e6   1938Gi     RWO            local-storage   6d6h
...
```

### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-server-group"></a>¿Cuánta memoria y núcleos virtuales se están usando y qué extensiones se han creado para un grupo de servidores?

Use kubectl para describir los recursos de Postgres. Para ello, necesita su tipo (nombre del recurso de Kubernetes, CRD, para Postgres en Azure Arc) y el nombre del grupo de servidores.

El formato general de este comando es:

```console
kubectl describe <CRD name>/<server group name> -n <namespace>
```

Por ejemplo:

```console
kubectl describe postgresql/postgres01 -n arc
```

Este comando muestra la configuración del grupo de servidores:

```output
Name:         postgres01
Namespace:    arc
Labels:       <none>
Annotations:  <none>
API Version:  arcdata.microsoft.com/v1beta2
Kind:         PostgreSql
Metadata:
  Creation Timestamp:  2021-10-13T01:09:25Z
  Generation:          29
  Managed Fields:
    API Version:  arcdata.microsoft.com/v1beta2
    Fields Type:  FieldsV1
    fieldsV1:
      f:spec:
        .:
        f:dev:
        f:engine:
          .:
          f:extensions:
          f:version:
        f:scale:
          .:
          f:replicas:
          f:workers:
        f:scheduling:
          .:
          f:default:
            .:
            f:resources:
              .:
              f:requests:
                .:
                f:memory:
          f:roles:
            .:
            f:coordinator:
              .:
              f:resources:
                .:
                f:limits:
                  .:
                  f:cpu:
                  f:memory:
                f:requests:
                  .:
                  f:cpu:
                  f:memory:
            f:worker:
              .:
              f:resources:
                .:
                f:limits:
                  .:
                  f:cpu:
                  f:memory:
                f:requests:
                  .:
                  f:cpu:
                  f:memory:
        f:services:
          .:
          f:primary:
            .:
            f:port:
            f:type:
        f:storage:
          .:
          f:backups:
            .:
            f:volumes:
          f:data:
            .:
            f:volumes:
          f:logs:
            .:
            f:volumes:
    Manager:      OpenAPI-Generator
    Operation:    Update
    Time:         2021-10-22T22:37:51Z
    API Version:  arcdata.microsoft.com/v1beta2
    Fields Type:  FieldsV1
    fieldsV1:
      f:IsValid:
      f:spec:
        f:scale:
          f:syncReplicas:
      f:status:
        .:
        f:logSearchDashboard:
        f:metricsDashboard:
        f:observedGeneration:
        f:podsStatus:
        f:primaryEndpoint:
        f:readyPods:
        f:state:
    Manager:         unknown
    Operation:       Update
    Time:            2021-10-22T22:37:53Z
  Resource Version:  1541521
  UID:               23565e53-2e7a-4cd6-8f80-3a79397e1d7a
Spec:
  Dev:  false
  Engine:
    Extensions:
      Name:   citus
    Version:  12
  Scale:
    Replicas:       1
    Sync Replicas:  0
    Workers:        4
  Scheduling:
    Default:
      Resources:
        Requests:
          Memory:  256Mi
    Roles:
      Coordinator:
        Resources:
          Limits:
            Cpu:     2
            Memory:  1Gi
          Requests:
            Cpu:     1
            Memory:  256Mi
      Worker:
        Resources:
          Limits:
            Cpu:     2
            Memory:  1Gi
          Requests:
            Cpu:     1
            Memory:  256Mi
  Services:
    Primary:
      Port:  5432
      Type:  LoadBalancer
  Storage:
    Backups:
      Volumes:
        Size:  5Gi
    Data:
      Volumes:
        Class Name:  managed-premium
        Size:        5Gi
    Logs:
      Volumes:
        Class Name:  managed-premium
        Size:        5Gi
Status:
  Log Search Dashboard:  https://12.235.78.99:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
  Metrics Dashboard:     https://12.346.578.99:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01
  Observed Generation:   29
  Pods Status:
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:40:55.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:40:55.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-1
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:42:41.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:42:41.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01c0-0
    Role:                    coordinator
    Conditions:
      Last Transition Time:  2021-10-22T22:37:54.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:40:52.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:40:52.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:54.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-3
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:38:35.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:38:35.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-0
    Role:                    worker
    Conditions:
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  Initialized
      Last Transition Time:  2021-10-22T22:42:40.000000Z
      Status:                True
      Type:                  Ready
      Last Transition Time:  2021-10-22T22:42:40.000000Z
      Status:                True
      Type:                  ContainersReady
      Last Transition Time:  2021-10-22T22:37:53.000000Z
      Status:                True
      Type:                  PodScheduled
    Name:                    postgres01w0-2
    Role:                    worker
  Primary Endpoint:          20.101.12.221:5432
  Ready Pods:                5/5
  Running Version:           v1.1.0_2021-10-12_patching_0bcb7bcaf
  State:                     Ready
Events:                      <none>
```

#### <a name="interpret-the-configuration-information"></a>Interpretación de la información de configuración

Vamos a explicar algunos puntos específicos de interés de la descripción del elemento `servergroup` mostrada anteriormente. ¿Qué nos informa sobre este grupo de servidores?

- Pertenece a la versión 12 de Postgres y ejecuta la extensión Citus:

   ```output
   Spec:
     Dev:  false
     Engine:
       Extensions:
         Name:   citus
       Version:  12
   ```

- Se creó el 13 de octubre de 2021:

   ```output
     Metadata:
     Creation Timestamp:  2021-10-13T01:09:25Z
   ```

- Usa cuatro nodos de trabajo:

   ```output
        Scale:
          Replicas:       1
          Sync Replicas:  0
          Workers:        4
   ```

- Configuración de recursos: en este ejemplo, los nodos de coordinación y de trabajo tienen garantizados 256 MiB de memoria. Los nodos de coordinación y de trabajo no pueden usar más de 1 GiB de memoria. Tanto el nodo de coordinación como los nodos de trabajo tienen garantizado un núcleo virtual y no pueden consumir más de dos núcleos virtuales.

   ```console
        Scheduling:
       Default:
         Resources:
           Requests:
             Memory:  256Mi
       Roles:
         Coordinator:
           Resources:
             Limits:
               Cpu:     2
               Memory:  1Gi
             Requests:
               Cpu:     1
               Memory:  256Mi
         Worker:
           Resources:
             Limits:
               Cpu:     2
               Memory:  1Gi
             Requests:
               Cpu:     1
               Memory:  256Mi
   ```

- ¿Cuál es el estado del grupo de servidores? ¿Está disponible para mis aplicaciones?

   Sí, todos los pods (el nodo de coordinación y los cuatro nodos de trabajo están listos).

   ```console
   Ready Pods:                5/5
   ```

## <a name="from-an-azure-arc-enabled-data-services-point-of-view"></a>Desde el punto de vista de los servicios de datos habilitados para Azure Arc

Use los comandos de la CLI de Az.

### <a name="what-are-the-postgres-server-groups-deployed-and-how-many-workers-are-they-using"></a>¿Cuáles son los grupos de servidores de Postgres implementados y cuántos nodos de trabajo usan?

Ejecute el siguiente comando.

   ```azurecli
   az postgres arc-server list --k8s-namespace <namespace> --use-k8s
   ```

Enumera los grupos de servidores que hay implementados.

   ```output
   [
     {
       "name": "postgres01",
       "replicas": 1,
       "state": "Ready",
       "workers": 4
     }
   ]
   ```

También indica cuántos nodos de trabajo usa el grupo de servidores. Cada nodo de trabajo se implementa en un pod al que debe agregar un pod que se usa para hospedar el nodo de coordinación.

### <a name="how-much-memory-and-vcores-are-being-used-and-what-extensions-were-created-for-a-group"></a>¿Cuánta memoria y núcleos virtuales se usan y qué extensiones se han creado para un grupo?

Ejecute uno de los siguientes comandos:

```azurecli
az postgres arc-server show -n <server group name>  --k8s-namespace <namespace> --use-k8s
```

Por ejemplo:

```azurecli
az postgres arc-server show -n postgres01 --k8s-namespace arc --use-k8s
```

Devuelve la información en un formato y contenido similar al devuelto por kubectl. Use la herramienta que prefiera para interactuar con el sistema.

## <a name="next-steps"></a>Pasos siguientes

- [Obtenga información sobre los conceptos de Hiperescala de PostgreSQL habilitada para Azure Arc](concepts-distributed-postgres-hyperscale.md).
- [Obtenga información acerca de cómo escalar horizontalmente (agregar nodos de trabajo) un grupo de servidores](scale-out-in-postgresql-hyperscale-server-group.md).
- [Obtenga información acerca de cómo escalar o reducir verticalmente (aumentar o reducir la memoria y/o los núcleos virtuales) de un grupo de servidores](scale-up-down-postgresql-hyperscale-server-group-using-cli.md).
- [Obtenga información sobre la configuración de almacenamiento](storage-configuration.md).
- [Consulte cómo supervisar una instancia de base de datos](monitor-grafana-kibana.md)
- [Uso de extensiones de PostgreSQL en el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc](using-extensions-in-postgresql-hyperscale-server-group.md)
- [Configuración de la seguridad del grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc](configure-security-postgres-hyperscale.md)
