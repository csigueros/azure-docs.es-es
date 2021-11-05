---
title: 'Tutorial: Implementación de una aplicación web conectada a Azure Storage Blob con un conector de servicio'
description: Creación de una aplicación web conectada a Azure Storage Blob con un conector de servicio.
author: shizn
ms.author: xshi
ms.service: serviceconnector
ms.topic: tutorial
ms.date: 10/28/2021
ms.custom: ignite-fall-2021
ms.openlocfilehash: 11c04c9a513e454e8a9296c00b716a154b5bc541
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131030465"
---
# <a name="tutorial-deploy-web-application-connected-to-azure-storage-blob-with-service-connector"></a>Tutorial: Implementación de una aplicación web conectada a Azure Storage Blob con un conector de servicio

Aprenda a establecer el acceso a Azure Storage para una aplicación web (no para un usuario con la sesión iniciada) que se ejecuta en Azure App Service mediante identidades administradas. En este tutorial, se usa la CLI de Azure para completar las siguientes tareas:

> [!div class="checklist"]
> * Configurar el entorno inicial con la CLI de Azure
> * Crear una cuenta de almacenamiento y un contenedor de Azure Blob Storage.
> * Implementación de código en Azure App Service y conexión al almacenamiento con identidad administrada mediante el conector de servicio

## <a name="1-set-up-your-initial-environment"></a>1. Configuración del entorno inicial

1. Disponga de una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
2. Instale la <a href="/cli/azure/install-azure-cli" target="_blank">CLI de Azure</a> 2.18.0 o posterior, con la que se ejecutan comandos en cualquier shell para aprovisionar y configurar los recursos de Azure.


Compruebe que la versión de la CLI de Azure es la 2.18.0 o posterior:

```Azure CLI
az --version
```

Si necesita actualizar, pruebe el comando `az upgrade` (requiere la versión 2.11 o superior), o consulte <a href="/cli/azure/install-azure-cli" target="_blank">Instalación de la CLI de Azure</a>.

Después, inicie sesión en Azure mediante la CLI:

```Azure CLI
az login
```

Este comando abre un explorador que recopila las credenciales. Cuando el comando finaliza, muestra una salida JSON que contiene información sobre las suscripciones.

Una vez que haya iniciado sesión, puede ejecutar comandos de Azure con la CLI de Azure para trabajar con los recursos de su suscripción.

## <a name="2-clone-or-download-the-sample-app"></a>2. Clonación o descarga de la aplicación de ejemplo

Clone el repositorio de ejemplo:
```Bash
git clone https://github.com/Azure-Samples/serviceconnector-webapp-storageblob-dotnet.git
```

y vaya a la carpeta raíz del repositorio:
```Bash
cd WebAppStorageMISample
```

## <a name="3-create-the-app-service-app"></a>3. Creación de la aplicación de App Service

En el terminal, asegúrese de que está en la carpeta del repositorio *WebAppStorageMISample* que contiene el código de la aplicación.

Cree una aplicación de App Service (el proceso de host) con el comando [`az webapp up`](/cli/azure/webapp#az_webapp_up):

```Azure CLI
az webapp up --name <app-name> --sku B1 --location eastus --resource-group ServiceConnector-tutorial-rg
```

- Para el argumento `--location`, asegúrese de usar la ubicación que [admite Service Connector](concept-region-support.md).
- **Reemplace** *\<app-name>* por un nombre que sea único en Azure (el punto de conexión del servidor es `https://<app-name>.azurewebsites.net`). Los caracteres permitidos para *\<app-name>* son `A`-`Z`, `0`-`9` y `-`. Un buen patrón es usar una combinación del nombre de la empresa y un identificador de la aplicación.

## <a name="4-create-a-storage-account-and-blob-storage-container"></a>4. Creación de una cuenta de almacenamiento y un contenedor de Blob Storage

Para crear una cuenta de almacenamiento de uso general v2 y un contenedor de Blob Storage, ejecute el siguiente comando en el terminal. **Reemplace** *\<storage-name>* por un nombre único. El nombre del contenedor debe escribirse en minúsculas, comenzar por una letra o un número, y solo puede incluir letras, números y el carácter de guión (-).

```Azure CLI
az storage account create --name <storage-name> --resource-group ServiceConnector-tutorial-rg --sku Standard_RAGRS --https-only
```


## <a name="5-connect-app-service-app-to-blob-storage-container-with-managed-identity"></a>5. Conexión de la aplicación de App Service al contenedor de Blob Storage con identidad administrada

En el terminal, ejecute el siguiente comando para conectar la aplicación web a Blob Storage con una identidad administrada.

```Azure CLI
az webapp connection create storage-blob -g ServiceConnector-tutorial-rg -n <app-name> --tg ServiceConnector-tutorial-rg --account <storage-name> --system-identity
```

- **Reemplace** *\<app-name>* por el nombre de la aplicación web que usó en el paso 3.
- **Reemplace** *\<storage-name>* por el nombre de la aplicación de almacenamiento que usó en el paso 4.


## <a name="6-run-sample-code"></a>6. Ejecución del código de ejemplo

En el terminal, ejecute el siguiente comando para abrir la aplicación de ejemplo en el explorador. Reemplace *\<app-name>* por el nombre de la aplicación web que usó en el paso 3.

```Azure CLI
az webapp browse --name <app-name> 
```

El código de ejemplo es una aplicación web. Cada vez que actualice la página de índice, se creará o actualizará un blob con el texto `Hello Service Connector! Current is {UTC Time Now}` en el contenedor de almacenamiento y volverá a leerlo para mostrarlo en la página de índice.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre Service Connector, siga los tutoriales que se enumeran a continuación.

> [!div class="nextstepaction"]
> [Más información sobre los conceptos de Service Connector](./concept-service-connector-internals.md)
