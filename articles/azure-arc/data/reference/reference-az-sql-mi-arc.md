---
title: Referencia de az sql mi-arc
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az sql mi-arc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: 81c8b2dbca45ac25db35fe15c2510b967bab1ae8
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780355"
---
# <a name="az-sql-mi-arc"></a>az sql mi-arc
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az sql mi-arc endpoint](reference-az-sql-mi-arc-endpoint.md) | Permite ver y administrar puntos de conexión de SQL.
[az sql mi-arc create](#az-sql-mi-arc-create) | Crea una instancia de SQL Managed Instance.
[az sql mi-arc edit](#az-sql-mi-arc-edit) | Edita la configuración de una instancia de SQL Managed Instance.
[az sql mi-arc delete](#az-sql-mi-arc-delete) | Elimina una instancia de SQL Managed Instance.
[az sql mi-arc show](#az-sql-mi-arc-show) | Muestra los detalles de una instancia de SQL Managed Instance.
[az sql mi-arc get-mirroring-cert](#az-sql-mi-arc-get-mirroring-cert) | Recupera el certificado del punto de conexión de creación de reflejo del grupo de disponibilidad de SQL MI y lo almacena en un archivo.
[az sql mi-arc list](#az-sql-mi-arc-list) | Enumera instancias de SQL Managed Instance.
[az sql mi-arc config](reference-az-sql-mi-arc-config.md) | Comandos de configuración.
[az sql mi-arc dag](reference-az-sql-mi-arc-dag.md) | Crea o elimina un grupo de disponibilidad distribuido.
## <a name="az-sql-mi-arc-create"></a>az sql mi-arc create
Para establecer la contraseña de la instancia de SQL Managed Instance, establezca la variable de entorno AZDATA_PASSWORD.
```bash
az sql mi-arc create --name -n 
                     --k8s-namespace -k  
                     
[--path]  
                     
[--replicas]  
                     
[--cores-limit -c]  
                     
[--cores-request]  
                     
[--memory-limit -m]  
                     
[--memory-request]  
                     
[--storage-class-data -d]  
                     
[--storage-class-logs -g]  
                     
[--storage-class-datalogs]  
                     
[--storage-class-backups]  
                     
[--volume-size-data]  
                     
[--volume-size-logs]  
                     
[--volume-size-datalogs]  
                     
[--volume-size-backups]  
                     
[--no-wait]  
                     
[--no-external-endpoint]  
                     
[--cert-public-key-file]  
                     
[--cert-private-key-file]  
                     
[--service-cert-secret]  
                     
[--admin-login-secret]  
                     
[--license-type -l]  
                     
[--tier -t]  
                     
[--dev]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-labels]  
                     
[--service-annotations]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]  
                     
[--collation]  
                     
[--language]  
                     
[--agent-enabled]  
                     
[--trace-flags]
```
### <a name="examples"></a>Ejemplos
Crea una instancia de SQL Managed Instance.
```bash
az sql mi-arc create -n sqlmi1 --k8s-namespace namespace
```
Cree una instancia administrada de SQL con tres réplicas en el escenario de alta disponibilidad.
```bash
az sql mi-arc create -n sqlmi2 --replicas 3 --k8s-namespace namespace
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre de la instancia de SQL Managed Instance.
#### `--k8s-namespace -k`
Espacio de nombres en el que se implementará la instancia de SQL Managed Instance. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--path`
Ruta de acceso al archivo azext_arcdata del archivo JSON de la instancia de SQL Managed Instance.
#### `--replicas`
Esta opción especifica el número de réplicas de SQL Managed Instance que se implementarán en el clúster de Kubernetes para lograr alta disponibilidad. Los valores permitidos son "3" o "1", con el valor predeterminado de "1".
#### `--cores-limit -c`
Límite de núcleos de la instancia administrada como un entero.
#### `--cores-request`
Solicitud de núcleos de la instancia administrada como un entero.
#### `--memory-limit -m`
Límite de la capacidad de la instancia administrada como un número entero seguido de Gi (gigabytes). Ejemplo: 4 Gi
#### `--memory-request`
Solicitud de la capacidad de la instancia administrada como un número entero seguido de Gi (gigabytes). Ejemplo: 4 Gi
#### `--storage-class-data -d`
Clase de almacenamiento que se va a usar para los datos (.mdf, .ndf). Si no se especifica ningún valor, no se especifica ninguna clase de almacenamiento, lo que da lugar a que Kubernetes use la predeterminada.
#### `--storage-class-logs -g`
Clase de almacenamiento que se va a usar para los registros (/var/log). Si no se especifica ningún valor, no se especifica ninguna clase de almacenamiento, lo que da lugar a que Kubernetes use la predeterminada.
#### `--storage-class-datalogs`
Clase de almacenamiento que se va a usar para los registros de datos (.ldf). Si no se especifica ningún valor, no se especifica ninguna clase de almacenamiento, lo que da lugar a que Kubernetes use la predeterminada.
#### `--storage-class-backups`
Clase de almacenamiento que se va a usar para las copias de seguridad (/var/opt/mssql/backups). Si no se especifica ningún valor, no se especifica ninguna clase de almacenamiento, lo que da lugar a que Kubernetes use la predeterminada.
#### `--volume-size-data`
Tamaño del volumen de almacenamiento que se va a usar para los datos como un número positivo seguido de K (kilobytes), MB (megabytes) o GB (gigabytes).
#### `--volume-size-logs`
Tamaño del volumen de almacenamiento que se va a usar para los registros como un número positivo seguido de K (kilobytes), MB (megabytes) o GB (gigabytes).
#### `--volume-size-datalogs`
Tamaño del volumen de almacenamiento que se va a usar para los registros de datos como un número positivo seguido de K (kilobytes), MB (megabytes) o GB (gigabytes).
#### `--volume-size-backups`
Tamaño del volumen de almacenamiento que se va a usar para las copias de seguridad como un número positivo seguido de K (kilobytes), MB (megabytes) o GB (gigabytes).
#### `--no-wait`
Si se especifica, el comando no espera a que la instancia esté lista para devolver un valor.
#### `--no-external-endpoint`
Si se especifica, no se crea ningún servicio externo. De lo contrario, se crea un servicio externo con el mismo tipo de servicio que el controlador de datos.
#### `--cert-public-key-file`
Ruta de acceso al archivo que contiene una clave pública de certificado con formato PEM que se va a usar para SQL Server.
#### `--cert-private-key-file`
Ruta de acceso al archivo que contiene una clave privada de certificado con formato PEM que se va a usar para SQL Server.
#### `--service-cert-secret`
Nombre del secreto de Kubernetes que se va a generar y que hospeda u hospedará el certificado de servicio SQL.
#### `--admin-login-secret`
Nombre del secreto de Kubernetes que se va a generar y que hospeda u hospedará la credencial de la cuenta de inicio de sesión del administrador de usuarios.
#### `--license-type -l`
Tipo de licencia que se va a aplicar a esta instancia administrada. Los valores permitidos son: BasePrice, LicenseIncluded. El valor predeterminado es LicenseIncluded. No se puede cambiar el tipo de licencia.
#### `--tier -t`
Plan de tarifa de la instancia. Valores permitidos: BusinessCritical (bc para abreviar) o GeneralPurpose (gp para abreviar). El valor predeterminado es GeneralPurpose. No se puede cambiar el plan de tarifa.
#### `--dev`
Si se especifica, se considera una instancia de desarrollo y no se factura.
#### `--labels`
Lista separada por comas de etiquetas de la instancia de SQL Managed Instance.
#### `--annotations`
Lista separada por comas de anotaciones de la instancia de SQL Managed Instance.
#### `--service-labels`
Lista separada por comas de etiquetas que se van a aplicar a todos los servicios externos.
#### `--service-annotations`
Lista separada por comas de anotaciones que se van a aplicar a todos los servicios externos.
#### `--storage-labels`
Lista separada por comas de etiquetas que se van a aplicar a todas las PCV.
#### `--storage-annotations`
Lista separada por comas de anotaciones que se van a aplicar a todas las PCV.
#### `--use-k8s`
Creación de una instancia de SQL Managed Instance mediante las API locales de Kubernetes.
#### `--collation`
La intercalación de SQL Server para la instancia.
#### `--language`
La configuración regional de SQL Server de cualquier identificador de lenguaje admitido (LCID) para la instancia.
#### `--agent-enabled`
Habilitación del Agente SQL Server para la instancia. De forma predeterminada, está deshabilitada. Los valores predeterminados son "true" o "false".
#### `--trace-flags`
Lista separada por comas de marcas de seguimiento. De manera predeterminada, no hay marcas.
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
## <a name="az-sql-mi-arc-edit"></a>az sql mi-arc edit
Edita la configuración de una instancia de SQL Managed Instance.
```bash
az sql mi-arc edit --name -n 
                   [--k8s-namespace -k]  
                   
[--path]  
                   
[--cores-limit -c]  
                   
[--cores-request]  
                   
[--memory-limit -m]  
                   
[--memory-request]  
                   
[--no-wait]  
                   
[--dev]  
                   
[--labels]  
                   
[--annotations]  
                   
[--service-labels]  
                   
[--service-annotations]  
                   
[--agent-enabled]  
                   
[--trace-flags]  
                   
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Edita la configuración de una instancia de SQL Managed Instance.
```bash
az sql mi-arc edit --path ./spec.json -n sqlmi1
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre de la instancia de SQL Managed Instance que se va a editar. El nombre con el que se implementa la instancia no se puede cambiar.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
Espacio de nombres en el que existe la instancia de SQL Managed Instance. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
#### `--path`
Ruta de acceso al archivo azext_arcdata del archivo JSON de la instancia de SQL Managed Instance.
#### `--cores-limit -c`
Límite de núcleos de la instancia administrada como un entero.
#### `--cores-request`
Solicitud de núcleos de la instancia administrada como un entero.
#### `--memory-limit -m`
Límite de la capacidad de la instancia administrada como un número entero seguido de Gi (gigabytes). Ejemplo: 4 Gi
#### `--memory-request`
Solicitud de la capacidad de la instancia administrada como un número entero seguido de Gi (gigabytes). Ejemplo: 4 Gi
#### `--no-wait`
Si se especifica, el comando no espera a que la instancia esté lista para devolver un valor.
#### `--dev`
Si se especifica, se considera una instancia de desarrollo y no se factura.
#### `--labels`
Lista separada por comas de etiquetas de la instancia de SQL Managed Instance.
#### `--annotations`
Lista separada por comas de anotaciones de la instancia de SQL Managed Instance.
#### `--service-labels`
Lista separada por comas de etiquetas que se van a aplicar a todos los servicios externos.
#### `--service-annotations`
Lista separada por comas de anotaciones que se van a aplicar a todos los servicios externos.
#### `--agent-enabled`
Habilitación del Agente SQL Server para la instancia. De forma predeterminada, está deshabilitada.
#### `--trace-flags`
Lista separada por comas de marcas de seguimiento. De manera predeterminada, no hay marcas.
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
## <a name="az-sql-mi-arc-delete"></a>az sql mi-arc delete
Elimina una instancia de SQL Managed Instance.
```bash
az sql mi-arc delete --name -n 
                     [--k8s-namespace -k]  
                     
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Elimina una instancia de SQL Managed Instance.
```bash
az sql mi-arc delete -n sqlmi1
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre de la instancia de SQL Managed Instance que se va a eliminar.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
Espacio de nombres en el que existe la instancia de SQL Managed Instance. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
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
## <a name="az-sql-mi-arc-show"></a>az sql mi-arc show
Muestra los detalles de una instancia de SQL Managed Instance.
```bash
az sql mi-arc show --name -n 
                   [--path -p]  
                   
[--k8s-namespace -k]  
                   
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Muestra los detalles de una instancia de SQL Managed Instance.
```bash
az sql mi-arc show -n sqlmi1
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre de la instancia de SQL Managed Instance que se va a mostrar.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--path -p`
Ruta donde se debe escribir la especificación completa de la instancia de SQL Managed Instance. Si se omite, la especificación se escribe en la salida estándar.
#### `--k8s-namespace -k`
Espacio de nombres en el que existe la instancia de SQL Managed Instance. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
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
## <a name="az-sql-mi-arc-get-mirroring-cert"></a>az sql mi-arc get-mirroring-cert
Recupera el certificado del punto de conexión de creación de reflejo del grupo de disponibilidad de SQL MI y lo almacena en un archivo.
```bash
az sql mi-arc get-mirroring-cert --name -n 
                                 --cert-file  
                                 
[--k8s-namespace -k]  
                                 
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Recupera el certificado del punto de conexión de creación de reflejo de sqlmi1 y lo almacena en el archivo fileName1.
```bash
az sql mi-arc get-mirroring-cert -n sqlmi1 --cert-file fileName1
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre de la instancia de SQL Managed Instance.
#### `--cert-file`
Nombre de archivo local en el que se va a almacenar el certificado recuperado en formato PEM.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
Espacio de nombres en el que existe la instancia de SQL Managed Instance. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
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
## <a name="az-sql-mi-arc-list"></a>az sql mi-arc list
Enumera instancias de SQL Managed Instance.
```bash
az sql mi-arc list [--k8s-namespace -k] 
                   [--use-k8s]
```
### <a name="examples"></a>Ejemplos
Enumera instancias de SQL Managed Instance.
```bash
az sql mi-arc list
```
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--k8s-namespace -k`
Espacio de nombres en el que existen las instancias de SQL Managed Instance. Si no se especifica ningún espacio de nombres, se utilizará el espacio de nombres definido en kubeconfig.
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
