---
title: Configuración de compilación para Azure Static Web Apps
description: Obtenga información sobre cómo controlar el proceso de compilación para Azure Static Web Apps.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 08/23/2021
ms.author: cshoe
ms.custom: contperf-fy21q4
ms.openlocfilehash: 54b6231c98ce244913062010782591493ea45a5e
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2021
ms.locfileid: "124837984"
---
# <a name="build-configuration-for-azure-static-web-apps"></a>Configuración de compilación para Azure Static Web Apps

La configuración de compilación de Azure Static Web Apps se basa en Azure Pipelines o Acciones de GitHub.  Cada sitio tiene un archivo de configuración YAML que controla cómo se construye e implementa. En este artículo se explican la estructura y las opciones del archivo.

En la tabla siguiente se enumeran los valores de configuración disponibles.

| Propiedad | Descripción | Requerido |
|---|---|---|
| `app_location` | Esta carpeta contiene el código fuente de la aplicación de front-end. El valor es relativo a la raíz del repositorio en GitHub y la carpeta de trabajo actual en Azure DevOps. | Yes |
| `api_location` | Esta carpeta contiene el código fuente de la aplicación de API. El valor es relativo a la raíz del repositorio en GitHub y la carpeta de trabajo actual en Azure DevOps. | No |
| `output_location` | Si la aplicación web ejecuta un paso de compilación, la ubicación de salida es la carpeta donde se generan los archivos públicos. Para la mayoría de los proyectos, `output_location` es relativo a `app_location`. Pero para los proyectos de .NET, la ubicación es relativa a la carpeta de salida de publicación. | No |
| `app_build_command` | Para las aplicaciones de Node.js, puede definir un comando personalizado para compilar la aplicación de contenido estático.<br><br>Por ejemplo, para configurar una compilación de producción para una aplicación angular, cree un script NPM denominado `build-prod` para ejecutar `ng build --prod` y escriba `npm run build-prod` como comando personalizado. Si se deja en blanco, el flujo de trabajo intenta ejecutar los comandos `npm run build` o `npm run build:azure`. | No |
| `api_build_command` | Para las aplicaciones de Node.js, puede definir un comando personalizado para compilar la aplicación de API de Azure Functions. | No |
| `skip_app_build` | Establezca el valor en `true` para omitir la creación de la aplicación de front-end. | No |

Con estos valores, puede configurar Acciones de GitHub o [Azure Pipelines](publish-devops.md) a fin de ejecutar la integración continua o entrega continua (CI/CD) para la aplicación web estática.

> [!NOTE]
> Actualmente, solo puede definir `app_build_command` y `api_build_command` para compilaciones de Node.js.

## <a name="file-name-and-location"></a>Nombre y ubicación del archivo

# <a name="github-actions"></a>[Acciones de GitHub](#tab/github-actions)

GitHub genera el archivo de configuración, que se almacena en la carpeta *.github/workflows*, denominado con el formato siguiente: `azure-static-web-apps-<RANDOM_NAME>.yml`.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

De forma predeterminada, el archivo de configuración se almacena en la raíz del repositorio con el nombre `azure-pipelines.yml`.

---

## <a name="build-configuration"></a>Configuración de compilación

La siguiente configuración de ejemplo supervisa los cambios en el repositorio. A medida que se insertan confirmaciones en la rama `main`, la aplicación se compila desde la carpeta `app_location` y los archivos de `output_location` se sirven a la web pública. Además, la aplicación de la carpeta *api* está disponible en la ruta `api` del sitio.

# <a name="github-actions"></a>[Acciones de GitHub](#tab/github-actions)

```yml
name: Azure Static Web Apps CI/CD

on:
  push:
    branches:
      - main
  pull_request:
    types: [opened, synchronize, reopened, closed]
    branches:
      - main

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
          repo_token: ${{ secrets.GITHUB_TOKEN }} # Used for Github integrations (i.e. PR comments)
          action: "upload"
          ###### Repository/Build Configurations ######
          app_location: "src" # App source code path
          api_location: "api" # Api source code path - optional
          output_location: "public" # Built app content directory - optional
          ###### End of Repository/Build Configurations ######

  close_pull_request_job:
    if: github.event_name == 'pull_request' && github.event.action == 'closed'
    runs-on: ubuntu-latest
    name: Close Pull Request Job
    steps:
      - name: Close Pull Request
        id: closepullrequest
        uses: Azure/static-web-apps-deploy@v1
        with:
          azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
          action: "close"
```

En esta configuración:

- La rama `main` se supervisa en busca de confirmaciones.
- Cuando una solicitud de incorporación de cambios se abre, se sincroniza, se vuelve a abrir o se cierra en la rama `main`, se [desencadena](https://help.github.com/actions/reference/events-that-trigger-workflows) un flujo de trabajo de Acciones de GitHub.
- `build_and_deploy_job` se ejecuta al insertar confirmaciones o abrir una solicitud de incorporación de cambios en la rama indicada en la propiedad `on`.
- `app_location` apunta a la carpeta `src` que contiene los archivos de código fuente de la aplicación web.
- `api_location` apunta a la carpeta `api` que contiene la aplicación de Azure Functions para los puntos de conexión de API del sitio.
- `output_location` apunta a la carpeta `public` que contiene la versión final de los archivos de código fuente de la aplicación.

No cambie los valores de `repo_token`, `action` y `azure_static_web_apps_api_token`, ya que Azure Static Web Apps los establece de forma automática.

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
trigger:
  - main

pool:
  vmImage: ubuntu-latest

steps:
  - checkout: self
    submodules: true
  - task: AzureStaticWebApp@0
    inputs:
      app_location: 'src' # App source code path
      api_location: 'api' # Api source code path - optional
      output_location: 'public' # Built app content directory - optional
      azure_static_web_apps_api_token: $(deployment_token)
```

En esta configuración:

- La rama `main` se supervisa en busca de confirmaciones.
- `app_location` apunta a la carpeta `src` que contiene los archivos de código fuente de la aplicación web.
- `api_location` apunta a la carpeta `api` que contiene la aplicación de Azure Functions para los puntos de conexión de API del sitio.
- `output_location` apunta a la carpeta `public` que contiene la versión final de los archivos de código fuente de la aplicación.
- La variable `$(deployment_token)` apunta al [token de implementación generado por Azure DevOps](./publish-devops.md).

---

## <a name="custom-build-commands"></a>Comandos de compilación personalizados

En las aplicaciones de Node.js, puede controlar con precisión los comandos que se ejecutan durante el proceso de compilación de la aplicación o la API. En el ejemplo siguiente se muestra cómo definir la compilación con valores para `app_build_command` y `api_build_command`.

# <a name="github-actions"></a>[Acciones de GitHub](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: '/'
  api_location: 'api'
  output_location: 'dist'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
```
# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yaml
...

inputs:
  app_location: 'src'
  api_location: 'api'
  app_build_command: 'npm run build-ui-prod'
  api_build_command: 'npm run build-api-prod'
  output_location: 'public'
  azure_static_web_apps_api_token: $(deployment_token)
```

---

## <a name="skip-building-front-end-app"></a>Omisión de la compilación de una aplicación front-end

Si necesita control total de la compilación para la aplicación de front-end, puede omitir la compilación automática e implementar la aplicación compilada en un paso anterior.

Para omitir la compilación de la aplicación de front-end:

- Establezca `app_location` en la ubicación de los archivos que quiera implementar.
- Establezca `skip_app_build` en `true`.
- Establezca `output_location` en una cadena vacía (`''`).

# <a name="github-actions"></a>[Acciones de GitHub](#tab/github-actions)

```yml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src/dist'
  api_location: 'api'
  output_location: ''
  skip_app_build: true
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

```yml
...

inputs:
  app_location: 'src/dist'
  api_location: 'api'
  output_location: '' # Leave this empty
  skip_app_build: true
  azure_static_web_apps_api_token: $(deployment_token)
```

---

> [!NOTE]
> Solo puede omitir la compilación de la aplicación de front-end. La API siempre se compila si existe.

## <a name="environment-variables"></a>Variables de entorno

Puede establecer variables de entorno para la compilación a través de la sección `env` de la configuración de un trabajo.

# <a name="github-actions"></a>[Acciones de GitHub](#tab/github-actions)

```yaml
...

with:
  azure_static_web_apps_api_token: ${{ secrets.AZURE_STATIC_WEB_APPS_API_TOKEN }}
  repo_token: ${{ secrets.GITHUB_TOKEN }}
  action: 'upload'
  app_location: 'src'
  api_location: 'api'
  output_location: 'public'

env: # Add environment variables here
  HUGO_VERSION: 0.58.0
```

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Actualmente Azure DevOps no admite variables de entorno.

---

## <a name="monorepo-support"></a>Compatibilidad con monorrepositorios

Un monorrepositorio es un repositorio que contiene código para más de una aplicación. De manera predeterminada, el flujo de trabajo realiza el seguimiento de todos los archivos de un repositorio, pero se puede ajustar la configuración para que el destino sea una sola aplicación.

Para que un archivo de flujo de trabajo tenga como destino una única aplicación, especifique las rutas de acceso en las secciones `push` y `pull_request`.

# <a name="github-actions"></a>[Acciones de GitHub](#tab/github-actions)

Al configurar un repositorio único, el ámbito de configuración de cada aplicación se limita exclusivamente a los archivos de una sola aplicación. Los distintos archivos de flujo de trabajo residen en paralelo en la carpeta _.github/workflows_ del repositorio.

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

En este ejemplo, solo los cambios realizados en los siguientes archivos desencadenan una nueva compilación:

- Cualquier archivo dentro de la carpeta _app1_
- Cualquier archivo dentro de la carpeta _api1_
- Cambios en el archivo de flujo de trabajo _azure-static-web-apps-purple-pond.yml_ de la aplicación

# <a name="azure-devops"></a>[Azure DevOps](#tab/azure-devops)

Para admitir más de una aplicación en un único repositorio, cree un archivo de flujo de trabajo independiente y asócielo a otras instancias de Azure Pipelines.

---

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Revisión de las solicitudes de incorporación de cambios en entornos de pre-producción](review-publish-pull-requests.md)
