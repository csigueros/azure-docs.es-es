---
title: Creación de plantillas de configuración personalizadas
description: Creación de plantillas de configuración personalizadas
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: dinethi
ms.author: dinethi
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: e7d5d470fd967c9f350fddaae2032085caf17c07
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121737186"
---
# <a name="create-custom-configuration-templates"></a>Creación de plantillas de configuración personalizadas

En este artículo se explica cómo crear una plantilla de configuración personalizada para el controlador de datos habilitado para Azure Arc. 

Uno de los parámetros necesarios durante la implementación de un controlador de datos en modo conectado indirectamente es el parámetro `az arcdata dc create --profile-name`. Actualmente, la lista disponible de perfiles integrados se puede encontrar mediante la ejecución de la consulta:

```azurecli
az arcdata dc config list
```
Estos perfiles son archivos JSON de plantilla que tienen varias configuraciones para el controlador de datos habilitado para Azure Arc, como la configuración del repositorio y el registro de Docker, las clases de almacenamiento para datos y registros, el tamaño de almacenamiento para datos y registros, la seguridad, el tipo de servicio, etc., y se pueden personalizar para su entorno. 

Sin embargo, en algunos casos, es posible que quiera personalizar esas plantillas de configuración para satisfacer sus requisitos y pasar la plantilla de configuración personalizada mediante el parámetro `--path` al comando `az arcdata dc create`, en lugar de pasar una plantilla de configuración preconfigurada mediante el parámetro `--profile-name`.

## <a name="create-customjson-file"></a>Creación de un archivo custom.json

Ejecute `az arcdata dc config init` para iniciar una control.jsen el archivo con una configuración predefinida basada en la distribución del clúster de Kubernetes.
Por ejemplo, una archivo de plantilla control.json para un clúster de Kubernetes basado en la plantilla `azure-arc-kubeadm` de un subdirectorio llamado `custom` en el directorio de trabajo actual se puede crear de la siguiente manera:

```azurecli
az arcdata dc config init --source azure-arc-kubeadm --path custom
```
El archivo control.json creado se puede editar en cualquier editor, como Visual Studio Code para personalizar la configuración adecuada para su entorno.

## <a name="use-custom-controljson-file-to-deploy-azure-arc-enabled-data-controller-using-azure-cli-az"></a>Uso del archivo control.json personalizado para implementar el controlador de datos habilitado para Azure Arc mediante la CLI de Azure (az)

Una vez creado el archivo de plantilla, se puede aplicar durante la ejecución del comando create del controlador de datos habilitado para Azure Arc de la siguiente forma:

```azurecli
az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription id> --resource-group <resource group name> --location <location> --connectivity-mode indirect  --k8s-namespace <namespace> --use-k8s

#Example:
#az arcdata dc  create --path ./custom --namespace arc --name arc --subscription <subscription ID> --resource-group my-resource-group --location eastus --connectivity-mode indirect --k8s-namespace <namespace> --use-k8s
```

## <a name="use-custom-controljson-file-for-deploying-azure-arc-data-controller-using-azure-portal"></a>Uso del archivo control.json personalizado para implementar el controlador de datos habilitado para Azure Arc mediante Azure Portal

En la pantalla de creación del controlador de datos de Azure Arc, seleccione "Configurar plantilla personalizada" en Plantilla personalizada. Esto invocará una hoja para proporcionar una configuración personalizada. En esta hoja, puede escribir los valores de las distintas opciones de configuración o cargar directamente un archivo control.json configurado previamente en el archivo. 

Después de asegurarse de que los valores sean correctos, haga clic en Aplicar para continuar con la implementación del controlador de datos de Azure Arc.

## <a name="next-steps"></a>Pasos siguientes

[Implementación del controlador de datos: modo de conexión directa (requisitos previos)](create-data-controller-direct-prerequisites.md)

[Creación del controlador de datos de Azure Arc (CLI)](create-data-controller-direct-cli.md)
