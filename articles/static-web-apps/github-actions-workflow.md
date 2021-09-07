---
title: Flujos de trabajo de acciones de GitHub para Azure Static Web Apps
description: Obtenga información sobre cómo usar repositorios de GitHub para configurar la implementación continua en Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 9dc0c8a83279e3a70bceebb316536485e337c873
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729729"
---
# <a name="github-actions-workflows-for-azure-static-web-apps"></a>Flujos de trabajo de acciones de GitHub para Azure Static Web Apps

Cuando se usa la implementación de GitHub, un archivo YAML controla el flujo de trabajo de compilación del sitio. En este artículo se explican la estructura y las opciones del archivo.

Una implementación comienza con un [desencadenador](#triggers), que ejecuta [trabajos](#jobs) compuestos por pasos [individuales](#steps).

> [!NOTE]
> Azure Static Web Apps también admite flujos de trabajo de Azure DevOps. Consulte [Publicación con Azure DevOps](publish-devops.md) para obtener información sobre cómo configurar una canalización.

## <a name="file-name-and-location"></a>Nombre y ubicación del archivo

Al vincular el repositorio, Azure Static Web Apps genera un archivo que controla el flujo de trabajo.

Siga los pasos a continuación para ver el archivo de flujo de trabajo.

1. Abra el repositorio de la aplicación en GitHub.
1. Seleccione la pestaña **Código**.
1. Seleccione la carpeta **.github/workflows**.
1. Seleccione el archivo con un nombre similar a **azure-static-web-apps-<NOMBRE_ALEATORIO>.yml**.

## <a name="triggers"></a>Desencadenadores

Un [desencadenador](https://help.github.com/actions/reference/events-that-trigger-workflows) de acciones de GitHub envía una notificación a un flujo de trabajo de acciones de GitHub para que ejecute un trabajo basado en determinados eventos. Los desencadenadores se muestran al usar la propiedad `on` en el archivo de flujo de trabajo.

```yml
on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
```

En este ejemplo, un flujo de trabajo comienza cuando una solicitud de incorporación de cambios en la rama *principal* se:

- abierto
- sincroniza
- reabre
- closed

Puede personalizar esta parte del flujo de trabajo para dirigirse a ramas o eventos diferentes.

## <a name="jobs"></a>Trabajos

Cada desencadenador define una serie de [trabajos](https://help.github.com/actions/reference/workflow-syntax-for-github-actions#jobs) que se ejecutarán en respuesta al evento.

| Nombre | Descripción |
| --- | --- |
| `build_and_deploy_job` | Se ejecuta al insertar ("push") confirmaciones o abrir una solicitud de incorporación de cambios ("pull request") en la rama mostrada en la propiedad `on`.          |
| `close_pull_request_job` | Solo se ejecuta cuando se cierra una solicitud de incorporación de cambios, lo que quita el entorno de ensayo creado a partir de solicitudes de incorporación de cambios. |

## <a name="steps"></a>Pasos

Los pasos son tareas secuenciales de un trabajo. Un paso lleva a cabo acciones como la instalación de dependencias, la ejecución de pruebas y la implementación de la aplicación a la fase de producción.

| Trabajo | Pasos |
| --- | --- |
| `build_and_deploy_job` | <li>Extrae el repositorio del entorno de la acción de GitHub.<li>Compila e implementa el repositorio en Azure Static Web Apps. |
| `close_pull_request_job` | <li>Notifica a Azure Static Web Apps que se ha cerrado una solicitud de incorporación de cambios ("pull request"). |

## <a name="build-and-deploy"></a>Compilación e implementación

El paso denominado `build_and_deploy_job` se compila e implementa en su sitio en Azure Static Web Apps. En la sección `with`, puede personalizar los siguientes valores para su implementación.

En el ejemplo siguiente se muestra cómo aparecen estos valores en un archivo de flujo de trabajo.

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  ###### End of Repository/Build Configurations ######
```

[!INCLUDE [static-web-apps-folder-structure](../../includes/static-web-apps-folder-structure.md)]

No cambie los valores de `repo_token`, `action` y `azure_static_web_apps_api_token`, ya que Azure Static Web Apps los establece de forma automática.

## <a name="custom-build-commands"></a>Comandos de compilación personalizados

Puede tomar un control preciso sobre los comandos que se ejecutan durante el proceso de compilación de la aplicación o la API. Los siguientes comandos aparecen en la sección `with` de un trabajo.

| Get-Help | Descripción |
| --- |--- |
| `app_build_command` | Define un comando personalizado para compilar la aplicación de contenido estático.<br><br>Por ejemplo, para configurar una compilación de producción para una aplicación angular, cree un script NPM denominado `build-prod` para ejecutar `ng build --prod` y escriba `npm run build-prod` como comando personalizado. Si se deja en blanco, el flujo de trabajo intenta ejecutar los comandos `npm run build` o `npm run build:azure`. |
| `api_build_command` | Define un comando personalizado para compilar la aplicación de la API de Azure Functions. |

En el ejemplo siguiente se muestra cómo definir comandos de compilación personalizados dentro de la sección `with` de un trabajo.

```yml
...
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: '/' # App source code path
  api_location: 'api' # Api source code path - optional
  output_location: 'dist' # Built app content directory - optional
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  ###### End of Repository/Build Configurations ######
```

> [!NOTE]
> Actualmente, solo puede definir comandos de compilación personalizados para compilaciones de Node.js. El proceso de compilación siempre llama a `npm install` antes de cualquier comando personalizado.

## <a name="skip-building-front-end-app"></a>Omisión de la compilación de una aplicación front-end

Si necesita un control total de la compilación para la aplicación front-end, puede agregar pasos personalizados al flujo de trabajo. Por ejemplo, puede optar por omitir la compilación automática e implementar la aplicación compilada en un paso anterior.

Para omitir la compilación de la aplicación front-end, establezca `skip_app_build` en `true` y `app_location` en la ubicación de la carpeta que se implementará.

```yml
with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN_MANGO_RIVER_0AFDB141E }}
  repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for GitHub integrations (i.e. PR comments)
  action: 'upload'
  ###### Repository/Build Configurations - These values can be configured to match you app requirements. ######
  app_location: 'dist' # Application build output generated by a previous step
  api_location: 'api' # Api source code path - optional
  output_location: '' # Leave this empty
  skip_app_build: true
  ###### End of Repository/Build Configurations ######
```

| Propiedad         | Descripción                                                 |
| ---------------- | ----------------------------------------------------------- |
| `skip_app_build` | Establezca el valor en `true` para omitir la creación de la aplicación de front-end. |

> [!NOTE]
> Solo puede omitir la compilación de la aplicación de front-end. La API siempre se compila si existe.

## <a name="environment-variables"></a>Variables de entorno

Puede establecer variables de entorno para la compilación a través de la sección `env` de la configuración de un trabajo.

```yaml
jobs:
  build_and_deploy_job:
    if: github.event_name == 'push' || (github.event_name == 'pull_request' && github.event.action != 'closed')
    runs-on: ubuntu-latest
    name: Build and Deploy Job
    steps:
      - uses: actions/checkout@v2
        with:
          submodules: true
      - name: Build And Deploy
        id: builddeploy
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          repo_token: ${{ secrets.GITHUB_TOKEN }}
          action: 'upload'
          ###### Repository/Build Configurations
          app_location: '/'
          api_location: 'api'
          output_location: 'public'
          ###### End of Repository/Build Configurations ######
        env: # Add environment variables here
          HUGO_VERSION: 0.58.0
```

## <a name="monorepo-support"></a>Compatibilidad con monorrepositorios

Un monorrepositorio es un repositorio que contiene código para más de una aplicación. De manera predeterminada, el flujo de trabajo realiza el seguimiento de todos los archivos de un repositorio, pero se puede ajustar la configuración para que el destino sea una sola aplicación.

Al configurar un repositorio único, cada aplicación estática tiene su propio archivo de configuración destinado exclusivamente a los archivos de una sola aplicación. Los distintos archivos de flujo de trabajo residen en paralelo en la carpeta _.github/workflows_ del repositorio.

```files
├── .github
│   └── workflows
│       ├── azure-static-web-apps-purple-pond.yml
│       └── azure-static-web-apps-yellow-shoe.yml
│
├── app1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── app2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
├── api1  👉 controlled by: azure-static-web-apps-purple-pond.yml
├── api2  👉 controlled by: azure-static-web-apps-yellow-shoe.yml
│
└── README.md
```

Para que un archivo de flujo de trabajo tenga como destino una única aplicación, especifique las rutas de acceso en las secciones `push` y `pull_request`.

En el ejemplo siguiente se muestra cómo agregar un nodo `paths` a las secciones `push` y `pull_request` de un archivo denominado _azure-static-web-apps-purple-pond.yml_.

```yml
on:
  push:
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main
    paths:
      - app1/**
      - api1/**
      - .github/workflows/azure-static-web-apps-purple-pond.yml
```

En esta instancia, solo los cambios realizados en los siguientes archivos desencadenan una nueva compilación:

- Cualquier archivo dentro de la carpeta _app1_
- Cualquier archivo dentro de la carpeta _api1_
- Cambios en el archivo de flujo de trabajo _azure-static-web-apps-purple-pond.yml_ de la aplicación

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Revisión de las solicitudes de incorporación de cambios en entornos de pre-producción](review-publish-pull-requests.md)
