---
title: Archivo de inclusión
description: archivo de inclusión
services: app-service
author: msangapu
ms.service: app-service
ms.topic: include
ms.date: 09/14/2021
ms.author: msangapu
ms.custom: include file
ms.openlocfilehash: afaacb79906c07893d835d654b00815ea97199d0
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128577338"
---
> [!NOTE]
> El comando `az webapp up` realiza las acciones siguientes:
>
>- Cree un [grupo de recursos](/cli/azure/group#az_group_create) predeterminado.
>
>- Cree un [plan de App Service](/cli/azure/appservice/plan#az_appservice_plan_create) predeterminado.
>
>- [Cree una aplicación](/cli/azure/webapp#az_webapp_create) con el nombre especificado.
>
>- [Implemente desde un archivo ZIP](../articles/app-service/deploy-zip.md#deploy-a-zip-package) todos los archivos del directorio de trabajo actual, [con la automatización de compilación habilitada](../articles/app-service/deploy-zip.md#enable-build-automation-for-zip-deploy).
>
>- Almacene en caché los parámetros localmente en el archivo *.azure/config* para que no sea necesario especificarlos de nuevo al implementarlos más adelante con `az webapp up` u otros comandos de la CLI de Azure. Los valores almacenados en caché se usan automáticamente de forma predeterminada.
>