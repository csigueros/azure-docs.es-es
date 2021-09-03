---
title: Referencia de az arcdata dc
titleSuffix: Azure Arc-enabled data services
description: Artículo de referencia sobre los comandos az arcdata dc.
author: MikeRayMSFT
ms.author: mikeray
ms.reviewer: seanw
ms.date: 07/30/2021
ms.topic: reference
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: f5b804ca5bf0c38165d848f75de7a3cd428fb2f6
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121780593"
---
# <a name="az-arcdata-dc"></a>az arcdata dc
## <a name="commands"></a>Comandos:
| Get-Help | Descripción|
| --- | --- |
[az arcdata dc create](#az-arcdata-dc-create) | Crear un controlador de datos.
[az arcdata dc delete](#az-arcdata-dc-delete) | Eliminar un controlador de datos.
[az arcdata dc endpoint](reference-az-arcdata-dc-endpoint.md) | Comandos de punto de conexión.
[az arcdata dc status](reference-az-arcdata-dc-status.md) | Comandos de estado.
[az arcdata dc config](reference-az-arcdata-dc-config.md) | Comandos de configuración.
[az arcdata dc debug](reference-az-arcdata-dc-debug.md) | Depurar controlador de datos.
[az arcdata dc export](#az-arcdata-dc-export) | Exportar métricas, registros o utilización.
[az arcdata dc upload](#az-arcdata-dc-upload) | Cargar el archivo de datos exportado.
## <a name="az-arcdata-dc-create"></a>az arcdata dc create
Crear un controlador de datos. Se requiere kubeconfig en el sistema junto con las variables de entorno siguientes: ["AZDATA_USERNAME", "AZDATA_PASSWORD"].
```bash
az arcdata dc create --k8s-namespace -k 
                     --name -n  
                     
--connectivity-mode  
                     
--resource-group -g  
                     
--location -l  
                     
[--profile-name]  
                     
[--path -p]  
                     
[--storage-class]  
                     
[--infrastructure]  
                     
[--labels]  
                     
[--annotations]  
                     
[--service-annotations]  
                     
[--service-labels]  
                     
[--storage-labels]  
                     
[--storage-annotations]  
                     
[--use-k8s]
```
### <a name="examples"></a>Ejemplos
Implementación del controlador de datos.
```bash
az arcdata dc create --name name --k8s-namespace namespace  --connectivity-mode indirect --resource-group group  --location location, --subscription subscription
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--k8s-namespace -k`
El espacio de nombres de Kubernetes en el que se va a implementar el controlador de datos. Si ya existe, se usará. Si no existe, en primer lugar se hará un intento para crearlo.
#### `--name -n`
El nombre del controlador de datos.
#### `--connectivity-mode`
La conectividad a Azure (indirecta o directa), en la que el controlador de datos debe funcionar.
#### `--resource-group -g`
El grupo de recursos de Azure en el que se debe agregar el recurso del controlador de datos.
#### `--location -l`
La ubicación de Azure en la que se almacenarán los metadatos del controlador de datos (por ejemplo, eastus).
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--profile-name`
El nombre de un perfil de configuración existente. Para ver las opciones disponibles, ejecute `az arcdata dc config list`. Una de las siguientes: ["azure-arc-gke", "azure-arc-eks", "azure-arc-kubeadm", "azure-arc-aks-default-storage", "azure-arc-azure-openshift", "azure-arc-ake", "azure-arc-openshift", "azure-arc-aks-hci", "azure-arc-aks-premium-storage"].
#### `--path -p`
La ruta de acceso a un directorio que contiene un perfil de configuración personalizado que se va a usar. Para crear un perfil de configuración personalizado, ejecute `az arcdata dc config init`.
#### `--storage-class`
La clase de almacenamiento que se va a usar para todos los datos y que registra los volúmenes persistentes de todos los pods del controlador de datos que los necesitan.
#### `--infrastructure`
Infraestructura en la que se ejecutará el controlador de datos. Valores permitidos: ["aws", "gcp", "azure", "alibaba", "onpremises", "other", "auto"]
#### `--labels`
Lista separada por comas de etiquetas que se aplicarán a todos los recursos del controlador de datos.
#### `--annotations`
Lista separada por comas de anotaciones que se aplicarán a todos los recursos del controlador de datos.
#### `--service-annotations`
Lista separada por comas de anotaciones que se aplicarán a todos los servicios de controlador de datos externos.
#### `--service-labels`
Lista separada por comas de etiquetas que se aplicarán a todos los servicios de controlador de datos externos.
#### `--storage-labels`
Lista separada por comas de etiquetas que se aplicarán a todas las PVC creadas por el controlador de datos.
#### `--storage-annotations`
Lista separada por comas de anotaciones que se aplicarán a todas las PVC creadas por el controlador de datos.
#### `--use-k8s`
Crear un controlador de datos mediante API locales de Kubernetes.
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
## <a name="az-arcdata-dc-delete"></a>az arcdata dc delete
Elimina el controlador de datos: se requiere kubeconfig en el sistema.
```bash
az arcdata dc delete --name -n 
                     --k8s-namespace -k  
                     
[--force -f]  
                     
[--yes -y]
```
### <a name="examples"></a>Ejemplos
Implementación del controlador de datos.
```bash
az arcdata dc delete --name name --k8s-namespace namespace
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--name -n`
Nombre del controlador de datos.
#### `--k8s-namespace -k`
El espacio de nombres de Kubernetes en el que existe el controlador de datos.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--force -f`
Fuerce la eliminación del controlador de datos y todos sus servicios de datos.
#### `--yes -y`
Elimine el controlador de datos sin mensaje de confirmación.
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
## <a name="az-arcdata-dc-export"></a>az arcdata dc export
Exporta métricas, registros o utilización a un archivo.
```bash
az arcdata dc export --type -t 
                     --path -p  
                     
--k8s-namespace -k  
                     
[--force -f]  
                     
[--use-k8s]
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--type -t`
El tipo de datos que se van a exportar. Opciones: registros, métricas y utilización.
#### `--path -p`
La ruta de acceso completa o relativa, incluido el nombre del archivo que se va a exportar.
#### `--k8s-namespace -k`
El espacio de nombres de Kubernetes en el que existe el controlador de datos.
### <a name="optional-parameters"></a>Parámetros opcionales
#### `--force -f`
Fuerza la creación de un archivo de salida. Sobrescribe cualquier archivo existente en la misma ruta de acceso.
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
## <a name="az-arcdata-dc-upload"></a>az arcdata dc upload
Carga el archivo de datos exportado desde un controlador de datos a Azure.
```bash
az arcdata dc upload --path -p 
                     
```
### <a name="required-parameters"></a>Parámetros requeridos
#### `--path -p`
La ruta de acceso completa o relativa, incluido el nombre del archivo que se va a cargar.
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
