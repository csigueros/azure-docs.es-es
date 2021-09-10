---
title: Archivo de inclusión
description: Archivo de inclusión
services: app-service
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 01/14/2020
ms.author: cephalin
ms.custom: include file
ms.openlocfilehash: 54a29bb1cc92347d9a2578a4f0ec9febb22553ac
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225443"
---
## <a name="create-a-project-zip-package"></a>Creación de un paquete ZIP de proyecto

>[!NOTE]
> Si ha descargado los archivos en un paquete ZIP, extráigalos primero. Por ejemplo, si ha descargado un paquete ZIP de GitHub, no puede implementar ese archivo tal cual. GitHub agrega directorios anidados adicionales que no funcionan con App Service. 
>

En la ventana de un terminal local, navegue hasta el directorio raíz del proyecto de la aplicación. 

Este directorio debería contener el archivo de entrada para la aplicación web como, por ejemplo, _index.html_, _index.php_ y _app.js_. También puede contener archivos de administración de paquetes como _project.json_, _composer.json_, _package.json_, _bower.json_ y _requirements.txt_.

A menos que desee que App Service ejecute la automatización de implementación automáticamente, ejecute todas las tareas de compilación (por ejemplo, `npm`, `bower`, `gulp`, `composer` y `pip`) y asegúrese de que tiene todos los archivos que necesita para ejecutar la aplicación. Este paso es necesario si desea [ejecutar su paquete directamente](../articles/app-service/deploy-run-package.md).

Cree un archivo ZIP con todo el contenido del proyecto. En el caso de los proyectos de `dotnet`, esta carpeta es la carpeta de salida del comando `dotnet publish`. El siguiente comando usa la herramienta predeterminada de su terminal:

```
# Bash
zip -r <file-name>.zip .

# PowerShell
Compress-Archive -Path * -DestinationPath <file-name>.zip
``` 

