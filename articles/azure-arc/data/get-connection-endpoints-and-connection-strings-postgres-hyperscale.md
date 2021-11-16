---
title: Obtención de puntos de conexión y creación de cadenas de conexión para un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc
titleSuffix: Azure Arc-enabled data services
description: Obtenga puntos de conexión y cree cadenas de conexión para un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 11/03/2021
ms.topic: how-to
ms.openlocfilehash: f340cf95072015a3896291484ef1289a9d34d6ed
ms.sourcegitcommit: e41827d894a4aa12cbff62c51393dfc236297e10
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2021
ms.locfileid: "131564107"
---
# <a name="get-connection-endpoints--create-the-connection-strings-for-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>Obtención de puntos de conexión y creación de las cadenas de conexión para un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc

En este artículo se explica cómo se pueden recuperar los puntos de conexión del grupo de servidores y cómo se pueden formar las cadenas de conexión que se pueden utilizar con las aplicaciones o herramientas.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Obtención de puntos de conexión:

Ejecute el siguiente comando:
```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
Por ejemplo:
```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace arc --use-k8s
```

Devuelve la lista de puntos de conexión: el punto de conexión de PostgreSQL, el panel de búsqueda de registros (Kibana) y el panel de métricas (Grafana). Por ejemplo: 

```output
{
  "instances": [
    {
      "endpoints": [
        {
          "description": "PostgreSQL Instance",
          "endpoint": "postgresql://postgres:<replace with password>@12.345.567.89:5432"
        },
        {
          "description": "Log Search Dashboard",
          "endpoint": "https://23.456.78.99:5601/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))"
        },
        {
          "description": "Metrics Dashboard",
          "endpoint": "https://34.567.890.12:3000/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01"
        }
      ],
      "engine": "PostgreSql",
      "name": "postgres01"
    }
  ],
  "namespace": "arc"
}
```

Use estos puntos de conexión para:

- Formar las cadenas de conexión y conectarse con las aplicaciones o herramientas de cliente
- Acceder a los paneles Grafana y Kibana desde el explorador

Por ejemplo, puede usar el punto de conexión denominado _Instancia de PostgreSQL_ para conectarse con psql al grupo de servidores:
```console
psql postgresql://postgres:MyPassworkd@12.345.567.89:5432
psql (10.14 (Ubuntu 10.14-0ubuntu0.18.04.1), server 12.4 (Ubuntu 12.4-1.pgdg16.04+1))
WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=#
```
> [!NOTE]
>
> - La contraseña de usuario _postgres_ indicada en el punto de conexión denominado "_instancia de PostgreSQL_" es la contraseña que eligió al implementar el grupo de servidores.


## <a name="from-cli-with-kubectl"></a>Desde la CLI con kubectl
```console
kubectl get postgresqls/<server group name> -n <namespace name>
```

Por ejemplo:
```azurecli
kubectl get postgresqls/postgres01 -n arc
```

Estos comandos producirán una salida como la siguiente. Puede usar esa información para formar las cadenas de conexión:
```console
NAME         STATE   READY-PODS   PRIMARY-ENDPOINT     AGE
postgres01   Ready   3/3          12.345.567.89:5432   9d
``` 

## <a name="form-connection-strings"></a>Formación de cadenas de conexión

Use los ejemplos de cadenas de conexión siguientes para el grupo de servidores. Cópielos, péguelos y personalícelos según sea necesario:

### <a name="adonet"></a>ADO.NET

```ado.net
Server=192.168.1.121;Database=postgres;Port=24276;User Id=postgres;Password={your_password_here};Ssl Mode=Require;`
```

### <a name="c-libpq"></a>C++ (libpq)

```cpp
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="jdbc"></a>JDBC

```jdbc
jdbc:postgresql://192.168.1.121:24276/postgres?user=postgres&password={your_password_here}&sslmode=require
```

### <a name="nodejs"></a>Node.js

```node.js
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="php"></a>PHP

```php
host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require
```

### <a name="psql"></a>psql

```psql
psql "host=192.168.1.121 port=24276 dbname=postgres user=postgres password={your_password_here} sslmode=require"
```

### <a name="python"></a>Python

```python
dbname='postgres' user='postgres' host='192.168.1.121' password='{your_password_here}' port='24276' sslmode='true'
```

### <a name="ruby"></a>Ruby

```ruby
host=192.168.1.121; dbname=postgres user=postgres password={your_password_here} port=24276 sslmode=require
```

## <a name="next-steps"></a>Pasos siguientes
- Obtenga información sobre el [escalado horizontal (adición de nodos de trabajo)](scale-out-in-postgresql-hyperscale-server-group.md) del grupo de servidores.
- Obtenga información sobre [cómo escalar o reducir verticalmente (aumentar o reducir la memoria o los núcleos virtuales)](scale-up-down-postgresql-hyperscale-server-group-using-cli.md) el grupo de servidores.
