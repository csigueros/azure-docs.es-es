---
title: Uso de Acciones de GitHub para crear actualizaciones de código en Azure Functions
description: Obtenga información sobre cómo usar Acciones de GitHub para definir un flujo de trabajo para crear e implementar proyectos de Azure Functions en GitHub.
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: cshoe
ms.custom: devx-track-csharp, devx-track-python, github-actions-azure
ms.openlocfilehash: 0317b4f67a092702de7d0f430fbdb55fbaee2bf9
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128556608"
---
# <a name="continuous-delivery-by-using-github-action"></a>Entrega continua con Acciones de GitHub

Use [Acciones de GitHub](https://github.com/features/actions) para definir un flujo de trabajo que compile e implemente automáticamente el código en la aplicación de funciones de Azure Functions. 

En Acciones de GitHub, un [flujo de trabajo](https://docs.github.com/en/actions/learn-github-actions/introduction-to-github-actions#the-components-of-github-actions) es un proceso automatizado que define en su repositorio de GitHub. Este proceso indica a GitHub cómo compilar e implementar el proyecto de aplicación de funciones en GitHub. 

Un archivo YAML (.yml) define un flujo de trabajo en la ruta de acceso `/.github/workflows/` de su repositorio. En esta definición se incluyen los diversos pasos y parámetros que componen el flujo de trabajo. 

Para un flujo de trabajo de Azure Functions, el archivo tiene tres secciones: 

| Sección | Tareas |
| ------- | ----- |
| **Autenticación** | Descargue un perfil de publicación.<br/>Cree un secreto de GitHub.|
| **Compilar** | Configure el entorno.<br/>Compile la aplicación de función.|
| **Implementación** | Implemente la aplicación de función.|

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una cuenta de GitHub. Si no tiene ninguna, regístrese [gratis](https://github.com/join).  
- Una aplicación funcional hospedada en Azure con un repositorio de GitHub.   
    - [Inicio rápido: Creación de una función en Azure mediante Visual Studio Code](./create-first-function-vs-code-csharp.md)

## <a name="generate-deployment-credentials"></a>Genere las credenciales de implementación.

La manera recomendada de autenticarse con Azure Functions para las Acciones de GitHub es con un perfil de publicación. También puede autenticarse con una entidad de servicio. Para obtener más información, vea [este repositorio de Acciones de GitHub](https://github.com/Azure/functions-action). 

Después de guardar su credencial de perfil de publicación como un [secreto de GitHub](https://docs.github.com/en/actions/reference/encrypted-secrets), usará este secreto en el flujo de trabajo para autenticarse con Azure. 

#### <a name="download-your-publish-profile"></a>Descarga del perfil de publicación

Para descargar el perfil de publicación de la aplicación de funciones:

1. Seleccione la página **Información general** de la aplicación de funciones y, después, seleccione **Obtener perfil de publicación**.

   :::image type="content" source="media/functions-how-to-github-actions/get-publish-profile.png" alt-text="Descargar perfil de publicación":::

1. Guarde y copie el contenido del archivo.


### <a name="add-the-github-secret"></a>Adición del secreto de GitHub

1. En [GitHub](https://github.com), examine su repositorio, seleccione **Configuración** > **Secretos** > **Agregar un nuevo secreto**.

   :::image type="content" source="media/functions-how-to-github-actions/add-secret.png" alt-text="Agregar secreto":::

1. Agregue un secreto nuevo usando `AZURE_FUNCTIONAPP_PUBLISH_PROFILE` para **Nombre**, el contenido del archivo del perfil de publicación para **Value** y, después, seleccione **Agregar secreto**.

Ahora GitHub puede autenticarse en su aplicación de funciones de Azure.

## <a name="create-the-environment"></a>Creación del entorno 

La configuración del entorno se realiza mediante una acción de configuración de publicación específica del lenguaje.

# <a name="net"></a>[.NET](#tab/dotnet)

.NET (incluido ASP.NET) usa la acción `actions/setup-dotnet`.  
En el ejemplo siguiente se muestra la parte del flujo de trabajo que configura el entorno:

```yaml
    - name: Setup DotNet 2.2.402 Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: 2.2.402
```

# <a name="java"></a>[Java](#tab/java)

Java usa la acción `actions/setup-java`.  
En el ejemplo siguiente se muestra la parte del flujo de trabajo que configura el entorno:

```yaml
    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        # If your pom.xml <maven.compiler.source> version is not in 1.8.x
        # Please change the Java version to match the version in pom.xml <maven.compiler.source>
        java-version: '1.8.x'
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

JavaScript (Node.js) usa la acción `actions/setup-node`.  
En el ejemplo siguiente se muestra la parte del flujo de trabajo que configura el entorno:

```yaml

    - name: Setup Node 12.x Environment
      uses: actions/setup-node@v2
      with:
        node-version: 14.x
```

# <a name="python"></a>[Python](#tab/python)

Python usa la acción `actions/setup-python`.  
En el ejemplo siguiente se muestra la parte del flujo de trabajo que configura el entorno:

```yaml
    - name: Setup Python 3.7 Environment
      uses: actions/setup-python@v1
      with:
        python-version: 3.7
```
---

## <a name="build-the-function-app"></a>Creación de la aplicación de funciones

Esto depende del lenguaje y, para los lenguajes admitidos por Azure Functions, esta sección deben ser los pasos de compilación estándar de cada lenguaje.

En el ejemplo siguiente se muestra la parte del flujo de trabajo que compila la aplicación de funciones, que es específica del lenguaje:

# <a name="net"></a>[.NET](#tab/dotnet)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
```

# <a name="java"></a>[Java](#tab/java)

```yaml
    env:
      POM_XML_DIRECTORY: '.'  # set this to the directory which contains pom.xml file

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'  # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
```

# <a name="python"></a>[Python](#tab/python)

```yaml
    env:
      AZURE_FUNCTIONAPP_PACKAGE_PATH: '.' # set this to the path to your web app project, defaults to the repository root

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
```
---

## <a name="deploy-the-function-app"></a>Implementación de la aplicación de funciones

Para implementar su código en una aplicación de funciones, use la acción `Azure/functions-action`. Esta acción tiene tres parámetros:

|Parámetro |Explicación  |
|---------|---------|
|_**app-name**_ | (Obligatorio) El nombre de su aplicación de funciones. |
|_**slot-name**_ | (Opcional) El nombre de la [ranura de implementación](functions-deployment-slots.md) en la que desea realizar la implementación. La ranura ya debe estar definida en su aplicación de funciones. |
|_**publish-profile**_ | (Opcional) Nombre del secreto de GitHub para el perfil de publicación. |

En el siguiente ejemplo, se usa la versión 1 de `functions-action` y una `publish profile` para la autenticación. 

# <a name="net"></a>[.NET](#tab/dotnet)

Configure un flujo de trabajo de .NET para Linux que use un perfil de publicación.

```yaml
name: Deploy DotNet project to function app with a Linux environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@v2

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```
Configure un flujo de trabajo de .NET para Windows que use un perfil de publicación.

```yaml
name: Deploy DotNet project to function app with a Windows environment

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name  # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'    # set this to the path to your web app project, defaults to the repository root
  DOTNET_VERSION: '2.2.402'              # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@v2

    - name: Setup DotNet ${{ env.DOTNET_VERSION }} Environment
      uses: actions/setup-dotnet@v1
      with:
        dotnet-version: ${{ env.DOTNET_VERSION }}

    - name: 'Resolve Project Dependencies Using Dotnet'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        dotnet build --configuration Release --output ./output
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: '${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}/output'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="java"></a>[Java](#tab/java)

Configure un flujo de trabajo de Java para Linux que use un perfil de publicación.

```yaml
name: Deploy Java project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the dotnet version to use

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@v2

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: bash
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Configure un flujo de trabajo de Java para Windows que use un perfil de publicación.

```yaml
name: Deploy Java project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name      # set this to your function app name on Azure
  POM_XML_DIRECTORY: '.'                     # set this to the directory which contains pom.xml file
  POM_FUNCTIONAPP_NAME: your-app-name        # set this to the function app name in your local development environment
  JAVA_VERSION: '1.8.x'                      # set this to the java version to use

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@v2

    - name: Setup Java Sdk ${{ env.JAVA_VERSION }}
      uses: actions/setup-java@v1
      with:
        java-version: ${{ env.JAVA_VERSION }}

    - name: 'Restore Project Dependencies Using Mvn'
      shell: pwsh
      run: |
        pushd './${{ env.POM_XML_DIRECTORY }}'
        mvn clean package
        mvn azure-functions:package
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: './${{ env.POM_XML_DIRECTORY }}/target/azure-functions/${{ env.POM_FUNCTIONAPP_NAME }}'
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Configure un flujo de trabajo de Node.JS para Linux que use un perfil de publicación.

```yaml
name: Deploy Node.js project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '12.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@v2

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v2
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

Configure un flujo de trabajo de Node.JS para Windows que use un perfil de publicación.

```yaml
name: Deploy Node.js project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name    # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'      # set this to the path to your web app project, defaults to the repository root
  NODE_VERSION: '10.x'                     # set this to the node version to use (supports 8.x, 10.x, 12.x)

jobs:
  build-and-deploy:
    runs-on: windows-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@v2

    - name: Setup Node ${{ env.NODE_VERSION }} Environment
      uses: actions/setup-node@v2
      with:
        node-version: ${{ env.NODE_VERSION }}

    - name: 'Resolve Project Dependencies Using Npm'
      shell: pwsh
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        npm install
        npm run build --if-present
        npm run test --if-present
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}

```
# <a name="python"></a>[Python](#tab/python)

Configure un flujo de trabajo de Python para Linux que use un perfil de publicación.

```yaml
name: Deploy Python project to function app

on:
  [push]

env:
  AZURE_FUNCTIONAPP_NAME: your-app-name # set this to your application's name
  AZURE_FUNCTIONAPP_PACKAGE_PATH: '.'   # set this to the path to your web app project, defaults to the repository root
  PYTHON_VERSION: '3.7'                 # set this to the python version to use (supports 3.6, 3.7, 3.8)

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - name: 'Checkout GitHub Action'
      uses: actions/checkout@v2

    - name: Setup Python ${{ env.PYTHON_VERSION }} Environment
      uses: actions/setup-python@v1
      with:
        python-version: ${{ env.PYTHON_VERSION }}

    - name: 'Resolve Project Dependencies Using Pip'
      shell: bash
      run: |
        pushd './${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}'
        python -m pip install --upgrade pip
        pip install -r requirements.txt --target=".python_packages/lib/site-packages"
        popd
    - name: 'Run Azure Functions Action'
      uses: Azure/functions-action@v1
      id: fa
      with:
        app-name: ${{ env.AZURE_FUNCTIONAPP_NAME }}
        package: ${{ env.AZURE_FUNCTIONAPP_PACKAGE_PATH }}
        publish-profile: ${{ secrets.AZURE_FUNCTIONAPP_PUBLISH_PROFILE }}
```

---

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Más información sobre la integración de Azure y GitHub](/azure/developer/github/)
