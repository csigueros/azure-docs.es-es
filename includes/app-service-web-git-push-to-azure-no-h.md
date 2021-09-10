---
title: Archivo de inclusión
description: Archivo de inclusión
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 02/02/2018
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 3d79d02054e21ba359637194d00fee9ac01a6d56
ms.sourcegitcommit: 47fac4a88c6e23fb2aee8ebb093f15d8b19819ad
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2021
ms.locfileid: "123078406"
---
1. Puesto que va a implementar la rama `main`, debe establecer la rama de implementación predeterminada para la aplicación de App Service en `main` (consulte [Cambio de la rama de implementación](../articles/app-service/deploy-local-git.md#change-deployment-branch)). En Cloud Shell, establezca el valor de la aplicación `DEPLOYMENT_BRANCH` con el comando [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set). 

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group myResourceGroup --settings DEPLOYMENT_BRANCH='main'
    ```

1. En la ventana del terminal local, agregue una instancia remota de Azure al repositorio de Git local. Reemplace *\<deploymentLocalGitUrl-from-create-step>* por la dirección URL del repositorio Git remoto que guardó en [Creación de una aplicación web](#create-a-web-app).

    ```bash
    git remote add azure <deploymentLocalGitUrl-from-create-step>
    ```

1. Realice la insercion en la instancia remota de Azure para implementar la aplicación con el comando siguiente. Cuando el Administrador de credenciales de Git le solicite las credenciales, asegúrese de que especifica las que creó en **Configuración de un usuario de implementación**, no las que se usan para iniciar sesión en Azure Portal.

    ```bash
    git push azure main
    ```

    Este comando puede tardar varios minutos en ejecutarse. Durante la ejecución, muestra información similar a la del ejemplo siguiente:
