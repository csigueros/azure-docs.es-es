---
title: az postgres arc-server reference
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az postgres arc-server.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: c58819707eb5987bddeb0d8983c29e7519b69818
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780482"
---
# <a name="az-postgres-arc-server"></a>az postgres arc-server
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az postgres arc-server create](#az-postgres-arc-server-create) | Cree un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
[az postgres arc-server edit](#az-postgres-arc-server-edit) | Edite la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
[az postgres arc-server delete](#az-postgres-arc-server-delete) | Elimine un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
[az postgres arc-server show](#az-postgres-arc-server-show) | Muestre los detalles de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
[az postgres arc-server list](#az-postgres-arc-server-list) | Enumere los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
[az postgres arc-server endpoint](reference-az-postgres-arc-server-endpoint.md) | Permite administrar los puntos de conexión de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
## <a name="az-postgres-arc-server-create"></a>az postgres arc-server create
Para establecer la contraseña del grupo de servidores, establezca la variable de entorno AZDATA_PASSWORD.
```bash
az postgres arc-server create --name -n 
                              [--path]  
                              
[--k8s-namespace -k]  
                              
[--cores-limit]  
                              
[--cores-request]  
                              
[--memory-limit]  
                              
[--memory-request]  
                              
[--storage-class-data]  
                              
[--storage-class-logs]  
                              
[--storage-class-backups]  
                              
[--volume-claim-mounts]  
                              
[--extensions]  
                              
[--volume-size-data]  
                              
[--volume-size-logs]  
                              
[--volume-size-backups]  
                              
[--workers -w]  
                              
[--engine-version]  
                              
[--no-external-endpoint]  
                              
[--port]  
                              
[--no-wait]  
                              
[--engine-settings]  
                              
[--coordinator-settings]  
                              
[--worker-settings]  
                              
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Cree un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server create -n pg1 --k8s-namespace namespace --use-k8s
```
Cree un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc con configuración de motor. Los dos ejemplos siguientes son válidos.
```bash
az postgres arc-server create -n pg1 --engine-settings "key1=val1" --k8s-namespace namespace 
az postgres arc-server create -n pg1 --engine-settings "key2=val2" --k8s-namespace namespace --use-k8s
```
Cree un grupo de servidores de PostgreSQL con montajes de notificaciones de volumen.
```bash
az postgres arc-server create -n pg1 --volume-claim-mounts backup-pvc:backup 
```
Cree un grupo de servidores PostgreSQL con un límite de memoria específico para distintos roles de nodo.
```bash
az postgres arc-server create -n pg1 --memory-limit "coordinator=2Gi,w=1Gi" --workers 1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre del grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--path`
Ruta de acceso al archivo de código fuente json para el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc. Esta información es opcional.
#### `--k8s-namespace -k`
El espacio de nombres de Kubernetes donde se implementa el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
#### `--cores-limit`
Número máximo de núcleos de CPU para el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc que se puede usar por nodo. Se pueden usar núcleos fraccionarios. Opcionalmente, se puede especificar una lista separada por comas de roles con valores en el formato <role>=<value>. Los roles válidos son "coordinator" o "c" y "worker" o "w". Si no se especifica ningún rol, la configuración se aplicará a todos los nodos del grupo de servidores PostgreSQL de hiperescala.
#### `--cores-request`
Número mínimo de núcleos de CPU que debe haber disponibles por nodo para programar el servicio. Se pueden usar núcleos fraccionarios. Opcionalmente, se puede especificar una lista separada por comas de roles con valores en el formato <role>=<value>. Los roles válidos son "coordinator" o "c" y "worker" o "w". Si no se especifica ningún rol, la configuración se aplicará a todos los nodos del grupo de servidores PostgreSQL de hiperescala.
#### `--memory-limit`
Límite de memoria para el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc como un número seguido de Ki (kilobytes), Mi (megabytes) o Gi (gigabytes). Opcionalmente, se puede especificar una lista separada por comas de roles con valores en el formato <role>=<value>. Los roles válidos son "coordinator" o "c" y "worker" o "w". Si no se especifica ningún rol, la configuración se aplicará a todos los nodos del grupo de servidores PostgreSQL de hiperescala.
#### `--memory-request`
Solicitud de memoria para el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc como un número seguido de Ki (kilobytes), Mi (megabytes) o Gi (gigabytes). Opcionalmente, se puede especificar una lista separada por comas de roles con valores en el formato <role>=<value>. Los roles válidos son "coordinator" o "c" y "worker" o "w". Si no se especifica ningún rol, la configuración se aplicará a todos los nodos del grupo de servidores PostgreSQL de hiperescala.
#### `--storage-class-data`
Clase de almacenamiento que se va a usar en los volúmenes persistentes de datos.
#### `--storage-class-logs`
Clase de almacenamiento que se va a usar en los volúmenes persistentes de registro.
#### `--storage-class-backups`
Clase de almacenamiento que se va a usar en los volúmenes persistentes de copia de datos.
#### `--volume-claim-mounts`
Lista separada por comas de montajes de notificación de volumen. Un montaje de notificación de volumen es un par formado por una notificación de volumen persistente existente (en el mismo espacio de nombres) y el tipo de volumen (y metadatos opcionales según el tipo de volumen) separados por dos puntos. El volumen persistente se puede montar en cada pod para el grupo de servidores de PostgreSQL. La ruta de acceso de montaje puede depender del tipo de volumen.
#### `--extensions`
Lista separada por comas de las extensiones de Postgres que deben cargarse en el inicio. Vea la documentación de Postgres para obtener información sobre los valores admitidos.
#### `--volume-size-data`
Tamaño del volumen de almacenamiento que se va a usar en los datos como un número positivo seguido de Ki (kilobytes), Mi (megabytes) o Gi (gigabytes).
#### `--volume-size-logs`
Tamaño del volumen de almacenamiento que se va a usar en los registros como un número positivo seguido de Ki (kilobytes), Mi (megabytes) o Gi (gigabytes).
#### `--volume-size-backups`
Tamaño del volumen de almacenamiento que se va a usar en las copias de seguridad como un número positivo seguido de Ki (kilobytes), Mi (megabytes) o Gi (gigabytes).
#### `--workers -w`
Número de nodos de trabajo que se aprovisionarán en un grupo de servidores. En la versión preliminar no se admite la reducción del número de nodos de trabajo. Consulte la documentación para obtener más información.
#### `--engine-version`
Debe ser 11 o 12. El valor predeterminado es 12.
`12`
#### `--no-external-endpoint`
Si se especifica, no se creará ningún servicio externo. De lo contrario, se crea un servicio externo con el mismo tipo de servicio que el controlador de datos.
#### `--port`
Opcional.
#### `--no-wait`
Si se especifica, el comando no esperará a que la instancia esté en un estado listo antes de volver.
#### `--engine-settings`
Lista separada por comas de los valores del motor de Postgres con el formato '"key1=val1, key2=val2".
#### `--coordinator-settings`
Lista separada por comas de la configuración del motor de Postgres con el formato "key1=val1, key2=val2" que se aplicará al rol de nodo "coordinator". Si se establece la configuración específica del rol de nodo, la configuración predeterminada se omitirá y se sustituirá por la configuración proporcionada aquí.
#### `--worker-settings`
Lista separada por comas de la configuración del motor de Postgres con el formato "key1=val1, key2=val2" que se aplicará al rol de nodo "worker". Si se establece la configuración específica del rol de nodo, la configuración predeterminada se omitirá y se sustituirá por la configuración proporcionada aquí.
#### `--use-k8s`
Use las API de Kubernetes locales para realizar esta acción.
### <a name="global-arguments"></a>Argumentos globales
#### `--debug`
Aumente el nivel de detalle de registro para mostrar todos los registros de depuración.
#### `--help -h`
Muestre este mensaje de ayuda y salga.
#### `--output -o`
Formato de salida.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc.  Valor predeterminado: json.
#### `--query -q`
Cadena de consulta de JMESPath. Para obtener más información y ejemplos, vea [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Nombre o identificador de la suscripción Puede configurar la suscripción predeterminada mediante `az account set -s NAME_OR_ID`.
#### `--verbose`
Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.
## <a name="az-postgres-arc-server-edit"></a>az postgres arc-server edit
Edite la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server edit --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--workers -w]  
                            
[--cores-limit]  
                            
[--cores-request]  
                            
[--memory-limit]  
                            
[--memory-request]  
                            
[--extensions]  
                            
[--port]  
                            
[--no-wait]  
                            
[--engine-settings]  
                            
[--replace-settings]  
                            
[--coordinator-settings]  
                            
[--worker-settings]  
                            
[--admin-password]  
                            
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Edite la configuración de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server edit --path ./spec.json -n pg1 --k8s-namespace namespace --use-k8s
```
Edite un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc con configuración de motor para el nodo de coordinación.
```bash
az postgres arc-server edit -n pg1 --coordinator-settings "key2=val2" --k8s-namespace namespace
```
Edite un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc y reemplace la configuración de motor existente por la nueva configuración "key1=val1".
```bash
az postgres arc-server edit -n pg1 --engine-settings "key1=val1" --replace-settings --k8s-namespace namespace
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre del grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc que se está editando. El nombre con el que se implementa la instancia no se puede cambiar.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
El espacio de nombres de Kubernetes donde se implementa el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
#### `--path`
Ruta de acceso al archivo de código fuente json para el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc. Esta información es opcional.
#### `--workers -w`
Número de nodos de trabajo que se aprovisionarán en un grupo de servidores. En la versión preliminar no se admite la reducción del número de nodos de trabajo. Consulte la documentación para obtener más información.
#### `--cores-limit`
Número máximo de núcleos de CPU para el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc que se pueden usar por nodo, con compatibilidad con núcleos fraccionarios. Para quitar los límites de núcleos, especifique su valor como una cadena vacía. Opcionalmente, se puede especificar una lista separada por comas de roles con valores en el formato <role>=<value>. Los roles válidos son "coordinator" o "c" y "worker" o "w". Si no se especifica ningún rol, la configuración se aplicará a todos los nodos del grupo de servidores PostgreSQL de hiperescala.
#### `--cores-request`
Número mínimo de núcleos de CPU que debe haber disponibles por nodo para programar el servicio. Se pueden usar núcleos fraccionarios. Para quitar las solicitudes de núcleos, especifique su valor como una cadena vacía. Opcionalmente, se puede especificar una lista separada por comas de roles con valores en el formato <role>=<value>. Los roles válidos son "coordinator" o "c" y "worker" o "w". Si no se especifica ningún rol, la configuración se aplicará a todos los nodos del grupo de servidores PostgreSQL de hiperescala.
#### `--memory-limit`
Límite de memoria para el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc como un número seguido de Ki (kilobytes), Mi (megabytes) o Gi (gigabytes). Para quitar los límites de memoria, especifique su valor como una cadena vacía. Opcionalmente, se puede especificar una lista separada por comas de roles con valores en el formato <role>=<value>. Los roles válidos son "coordinator" o "c" y "worker" o "w". Si no se especifica ningún rol, la configuración se aplicará a todos los nodos del grupo de servidores PostgreSQL de hiperescala.
#### `--memory-request`
Solicitud de memoria para el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc como un número seguido de Ki (kilobytes), Mi (megabytes) o Gi (gigabytes). Para quitar la solicitud de memoria, especifique su valor como una cadena vacía. Opcionalmente, se puede especificar una lista separada por comas de roles con valores en el formato <role>=<value>. Los roles válidos son "coordinator" o "c" y "worker" o "w". Si no se especifica ningún rol, la configuración se aplicará a todos los nodos del grupo de servidores PostgreSQL de hiperescala.
#### `--extensions`
Lista separada por comas de las extensiones de Postgres que deben cargarse en el inicio. Vea la documentación de Postgres para obtener información sobre los valores admitidos.
#### `--port`
Opcional.
#### `--no-wait`
Si se especifica, el comando no esperará a que la instancia esté en un estado listo antes de volver.
#### `--engine-settings`
Lista separada por comas de los valores del motor de Postgres con el formato '"key1=val1, key2=val2". La configuración proporcionada se combinará con la configuración existente. Para quitar una configuración, indique un valor vacío, como "removedKey=". Si se cambia una configuración de motor que requiere un reinicio, el servicio se reiniciará para aplicar la configuración inmediatamente.
#### `--replace-settings`
Cuando se especifica con --engine-settings, reemplazará toda la configuración de motor personalizada existente por el nuevo conjunto de valores y configuraciones.
#### `--coordinator-settings`
Lista separada por comas de la configuración del motor de Postgres con el formato "key1=val1, key2=val2" que se aplicará al rol de nodo "coordinator". Si se establece la configuración específica del rol de nodo, la configuración predeterminada se omitirá y se sustituirá por la configuración proporcionada aquí.
#### `--worker-settings`
Lista separada por comas de la configuración del motor de Postgres con el formato "key1=val1, key2=val2" que se aplicará al rol de nodo "worker". Si se establece la configuración específica del rol de nodo, la configuración predeterminada se omitirá y se sustituirá por la configuración proporcionada aquí.
#### `--admin-password`
Si se especifica, la contraseña de administrador del grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc se establecerá en el valor de la variable de entorno AZDATA_PASSWORD si está presente y, si no, en un valor solicitado.
#### `--use-k8s`
Use las API de Kubernetes locales para realizar esta acción.
### <a name="global-arguments"></a>Argumentos globales
#### `--debug`
Aumente el nivel de detalle de registro para mostrar todos los registros de depuración.
#### `--help -h`
Muestre este mensaje de ayuda y salga.
#### `--output -o`
Formato de salida.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc.  Valor predeterminado: json.
#### `--query -q`
Cadena de consulta de JMESPath. Para obtener más información y ejemplos, vea [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Nombre o identificador de la suscripción Puede configurar la suscripción predeterminada mediante `az account set -s NAME_OR_ID`.
#### `--verbose`
Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.
## <a name="az-postgres-arc-server-delete"></a>az postgres arc-server delete
Elimine un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server delete --name -n 
                              [--k8s-namespace -k]  
                              
[--force -f]  
                              
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Elimine un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server delete -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre del grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
El espacio de nombres de Kubernetes donde se implementa el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
#### `--force -f`
Fuerce la eliminación del grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc sin confirmación.
#### `--use-k8s`
Use las API de Kubernetes locales para realizar esta acción.
### <a name="global-arguments"></a>Argumentos globales
#### `--debug`
Aumente el nivel de detalle de registro para mostrar todos los registros de depuración.
#### `--help -h`
Muestre este mensaje de ayuda y salga.
#### `--output -o`
Formato de salida.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc.  Valor predeterminado: json.
#### `--query -q`
Cadena de consulta de JMESPath. Para obtener más información y ejemplos, vea [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Nombre o identificador de la suscripción Puede configurar la suscripción predeterminada mediante `az account set -s NAME_OR_ID`.
#### `--verbose`
Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.
## <a name="az-postgres-arc-server-show"></a>az postgres arc-server show
Muestre los detalles de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server show --name -n 
                            [--k8s-namespace -k]  
                            
[--path]  
                            
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Muestre los detalles de un grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server show -n pg1 --k8s-namespace namespace --use-k8s
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre del grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
El espacio de nombres de Kubernetes donde se implementa el grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
#### `--path`
Ruta de acceso donde se debe escribir la especificación completa del grupo de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc. Si se omite, la especificación se escribe en la salida estándar.
#### `--use-k8s`
Use las API de Kubernetes locales para realizar esta acción.
### <a name="global-arguments"></a>Argumentos globales
#### `--debug`
Aumente el nivel de detalle de registro para mostrar todos los registros de depuración.
#### `--help -h`
Muestre este mensaje de ayuda y salga.
#### `--output -o`
Formato de salida.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc.  Valor predeterminado: json.
#### `--query -q`
Cadena de consulta de JMESPath. Para obtener más información y ejemplos, vea [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Nombre o identificador de la suscripción Puede configurar la suscripción predeterminada mediante `az account set -s NAME_OR_ID`.
#### `--verbose`
Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.
## <a name="az-postgres-arc-server-list"></a>az postgres arc-server list
Enumere los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server list [--k8s-namespace -k] 
                            [--use-k8s]
```
### <a name="examples"></a>Ejemplos
Enumere los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc.
```bash
az postgres arc-server list --k8s-namespace namespace --use-k8s
```
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
El espacio de nombres de Kubernetes donde se implementan los grupos de servidores de Hiperescala de PostgreSQL habilitada para Azure Arc. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
#### `--use-k8s`
Use las API de Kubernetes locales para realizar esta acción.
### <a name="global-arguments"></a>Argumentos globales
#### `--debug`
Aumente el nivel de detalle de registro para mostrar todos los registros de depuración.
#### `--help -h`
Muestre este mensaje de ayuda y salga.
#### `--output -o`
Formato de salida.  Valores permitidos: json, jsonc, none, table, tsv, yaml, yamlc.  Valor predeterminado: json.
#### `--query -q`
Cadena de consulta de JMESPath. Para obtener más información y ejemplos, vea [http://jmespath.org](http://jmespath.org).
#### `--subscription`
Nombre o identificador de la suscripción Puede configurar la suscripción predeterminada mediante `az account set -s NAME_OR_ID`.
#### `--verbose`
Aumente el nivel de detalle de registro. Use --debug para obtener registros de depuración completos.
