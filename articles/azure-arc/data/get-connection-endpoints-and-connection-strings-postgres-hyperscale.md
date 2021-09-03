---
title: Obtención de puntos de conexión y cadenas de conexión de formularios para el grupo de servidores de Hiperescala de PostgreSQL habilitada para Arc
titleSuffix: Azure Arc-enabled data services
description: Obtención de puntos de conexión y cadenas de conexión de formularios para el grupo de servidores de hiperescala de PostgreSQL habilitado para Arc
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 964b7fcca00afb91a457203d2ed53b885a254d5e
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733558"
---
# <a name="get-connection-endpoints-and-form-the-connection-strings-for-your-arc-enabled-postgresql-hyperscale-server-group"></a>Obtención de puntos de conexión y cadenas de conexión de formularios para el grupo de servidores de Hiperescala de PostgreSQL habilitada para Arc

En este artículo se explica cómo se pueden recuperar los puntos de conexión del grupo de servidores y cómo se pueden formar las cadenas de conexión que se pueden utilizar con las aplicaciones o herramientas.


[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="get-connection-end-points"></a>Obtención de puntos de conexión:

Ejecute el siguiente comando:
```azurecli
az postgres arc-server endpoint list -n <server group name> --k8s-namespace <namespace> --use-k8s
```
Por ejemplo:
```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
```

Muestra la lista de puntos de conexión: el punto de conexión de PostgreSQL que se usa para conectar la aplicación y usar la base de datos, los puntos de conexión Kibana y Grafana para análisis de registros y supervisión. Por ejemplo: 
```console
Arc
 ===================================================================================================================
 Postgres01 Instance
 -------------------------------------------------------------------------------------------------------------------
 Description           Endpoint

 PostgreSQL Instance   postgresql://postgres:<replace with password>@12.345.567.89:5432
 Log Search Dashboard  https://89.345.712.81:30777/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:postgres01'))
 Metrics Dashboard     https://89.345.712.81:30777/grafana/d/postgres-metrics?var-Namespace=arc&var-Name=postgres01

```
Use estos puntos de conexión para:
- Formar las cadenas de conexión y conectarse con las aplicaciones o herramientas de cliente
- Acceder a los paneles Grafana y Kibana desde el explorador

Por ejemplo, puede usar el punto final denominado _Instancia de PostgreSQL_ para conectarse con psql al grupo de servidores. Por ejemplo:
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
> _ERROR: (401)_ 
> _Motivo: No autorizado_
> _Encabezados de respuesta HTTP: HTTPHeaderDict({'Date': 'Sun, 06 Sep 2020 16:58:38 GMT', 'Content-Length': '0', 'WWW-Authenticate': '_ 
> _Dominio básico="Credenciales_ de inicio de sesión requeridas", Error de portador="invalid_token", error_description="El token ha expirado"'})_ Cuando esto ocurre, necesita volver a conectarse a azdata como se ha explicado anteriormente.

## <a name="from-cli-with-kubectl"></a>Desde la CLI con kubectl
```console
kubectl get postgresqls/<server group name> -n <namespace name>
```

Estos comandos producirán una salida como la siguiente. Puede usar esa información para formar las cadenas de conexión:
```console
NAME         STATE   READY-PODS   EXTERNAL-ENDPOINT   AGE
postgres01   Ready   3/3          123.456.789.4:31066      5d20h
``` 

## <a name="form-connection-strings"></a>Cadenas de conexión de formulario:
Use la siguiente tabla de plantillas de cadenas de conexiones para el grupo de servidores. Después, puede copiarlas, pegarlas y personalizarlas según sea necesario:

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


