---
title: Archivo de inclusión
description: archivo de inclusión
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 06/12/2019
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 44601c97a873138bcd152de2450fe6d98be814df
ms.sourcegitcommit: 34aa13ead8299439af8b3fe4d1f0c89bde61a6db
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/18/2021
ms.locfileid: "122423303"
---
## <a name="prepare-your-repository"></a>Preparación del repositorio

Para obtener compilaciones automáticas del servidor de compilación de Azure App Service, asegúrese de que la raíz del repositorio tenga los archivos correctos del proyecto.

| Tiempo de ejecución | Archivos del directorio raíz |
|-|-|
| ASP.NET (solo Windows) | _*.sln_, _*.csproj_ o _default.aspx_ |
| ASP.NET Core | _*.sln_ o _*.csproj_ |
| PHP | _index.php_ |
| Ruby (solo Linux) | _Gemfile_ |
| Node.js | _server.js_, _app.js_ o _package.json_ con un script de inicio |
| Python | _\*.py_, _requirements.txt_ o _runtime.txt_ |
| HTML | _default.htm_, _default.html_, _default.asp_, _index.htm_, _index.html_ o _iisstart.htm_ |
| Trabajos web | _\<job_name>/run.\<extension>_ en _App\_Data/jobs/continuous_ para WebJobs continuos, o _App\_Data/jobs/triggered_ para WebJobs desencadenados. Para más información, consulte la [documentación de WebJobs de Kudu](https://github.com/projectkudu/kudu/wiki/WebJobs). |
| Functions | Consulte [Implementación continua para Azure Functions](../articles/azure-functions/functions-continuous-deployment.md#requirements-for-continuous-deployment). |

Para personalizar la implementación, puede incluir un archivo *.deployment* en la raíz del repositorio. Para más información, consulte el artículo sobre la [personalización de las implementaciones](https://github.com/projectkudu/kudu/wiki/Customizing-deployments) y el artículo sobre el [script de implementación personalizado](https://github.com/projectkudu/kudu/wiki/Custom-Deployment-Script).

> [!NOTE]
> Si usa Visual Studio, deje que [Visual Studio cree un repositorio automáticamente](/azure/devops/repos/git/creatingrepo?tabs=visual-studio). El proyecto está listo inmediatamente para su implementación por medio de Git.
>

