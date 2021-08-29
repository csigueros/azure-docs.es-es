---
title: Examen de imágenes de contenedor mediante Acciones de GitHub
description: Obtenga información sobre cómo examinar las imágenes de contenedor mediante la acción Examen de contenedores.
author: v-abiss
ms.author: v-abiss
ms.topic: quickstart
ms.reviewer: jukullam
ms.service: azure
ms.date: 05/20/2021
ms.custom: github-actions-azure
ms.openlocfilehash: c7782349fb467ef34169f46843b3c91d182c0995
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121741091"
---
# <a name="scan-container-images-using-github-actions"></a>Examen de imágenes de contenedor mediante Acciones de GitHub

Para empezar a trabajar con [Acciones de GitHub](https://docs.github.com/en/actions/learn-github-actions), cree un flujo de trabajo para crear y examinar una imagen de contenedor.

Con Acciones de GitHub, puede acelerar el proceso de CI/CD mediante la creación, el examen y la inserción de imágenes en un [Registro de contenedor](https://azure.microsoft.com/services/container-registry/) público o privado desde los flujos de trabajo.

En este artículo, usaremos el [Examen de imágenes de contenedor](https://github.com/marketplace/actions/container-image-scan) desde [Marketplace de GitHub](https://github.com/marketplace).

## <a name="prerequisites"></a>Requisitos previos

- Una cuenta de Azure con una suscripción activa. [Cree una cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Una instancia de Azure Container Registry para almacenar todas las imágenes de contenedor que se van a crear e insertar. Puede [crear una instancia de Azure Container Registry desde Azure Portal](../container-registry/container-registry-get-started-portal.md).
- Una cuenta de GitHub con un repositorio activo. Si no tiene ninguna, regístrese [gratis](https://github.com/join). 
    - En este ejemplo se usa la [aplicación de ejemplo Spring PetClinic de Java](https://github.com/spring-projects/spring-petclinic).

## <a name="workflow-file-overview"></a>Información general sobre el archivo de flujo de trabajo

Un archivo YAML (.yml) define un flujo de trabajo en la ruta de acceso `/.github/workflows/` de su repositorio. En esta definición se incluyen los diversos pasos y parámetros que componen el flujo de trabajo.

El archivo tiene tres secciones:

|Sección  |Tareas  |
|---------|---------|
|**Autenticación** | 1. Cree una instancia de Container Registry en Azure. <br /> 2. Cree un secreto de GitHub. <br /> 3. Inicie sesión en el registro mediante acciones de GH. |
|**Compilar** | 1. Configure el entorno. <br /> 2. Cree la aplicación. |
|**Creación, examen e inserción de la imagen en el registro de contenedor** | 1. Cree la imagen. <br /> 2. Examine la imagen. <br /> 3. Inserte la imagen.|

## <a name="create-github-secrets"></a>Creación de secretos de GitHub

Para usar la [acción Inicio de sesión de Azure Container Registry](https://github.com/marketplace/actions/azure-container-registry-login), primero debe agregar los detalles de Container Registry como un secreto al repositorio de GitHub.

En este ejemplo, creará tres secretos que puede usar para autenticarse con Azure.  

1. Abra el repositorio de GitHub y vaya a **Settings** (Configuración).

    :::image type="content" source="media/github-action-scan/github-repo-settings.png" alt-text="Seleccione Configuración en la barra de navegación.":::

1. Seleccione **Secrets** (Secretos) y, a continuación, **New Secret** (Nuevo secreto).

    :::image type="content" source="media/github-action-scan/azure-secret-add.png" alt-text="Elija la opción para agregar un secreto.":::

1. Pegue los siguientes valores para cada secreto creado con los siguientes valores de Azure Portal al acceder a **Claves de acceso** en la instancia de Container Registry. 

    | Nombre de secreto de GitHub | Valores de Azure Container Registry |
    |---------|---------|
    |`ACR_LOGIN_SERVER` | **Servidor de inicio de sesión** |
    |`REGISTRY_USERNAME` | **Nombre de usuario** |
    |`REGISTRY_PASSWORD` | **password** |
    
1. Seleccione **Add secret** (Agregar secreto) para guardar.

## <a name="add-a-dockerfile"></a>Adición de un archivo Dockerfile

Use el siguiente fragmento de código para crear un `Dockerfile` y confirmarlo en el repositorio.

```
FROM openjdk:11-jre-stretch
ADD target/spring-petclinic-2.4.2.jar spring-petclinic-2.4.2.jar
EXPOSE 8080
ENTRYPOINT [ "java", "-jar", "spring-petclinic-2.4.2.jar" ]
```


## <a name="use-the-docker-login-action"></a>Uso de la acción Inicio de sesión de Docker

Use el secreto con la [acción de inicio de sesión de Azure Container Registry](https://github.com/marketplace/actions/azure-container-registry-login) para autenticarse en Azure.

En este flujo de trabajo, se autentica mediante el inicio de sesión de Azure Container Registry con los detalles del **servidor de inicio de sesión**, el **nombre de usuario y la **contraseña** del registro almacenado en `secrets.ACR_LOGIN_SERVER`, `secrets.REGISTRY_USERNAME` y `secrets.REGISTRY_PASSWORD`, respectivamente, en la [acción docker-login](https://github.com/Azure/docker-login). Para más información sobre cómo hacer referencia a secretos de GitHub en un archivo de flujo de trabajo, consulte [Uso de secretos cifrados en un flujo de trabajo](https://docs.github.com/en/actions/reference/encrypted-secrets#using-encrypted-secrets-in-a-workflow) en la documentación de GitHub.


```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest
    steps:
    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}
```

## <a name="configure-java"></a>Configuración de Java

Configure el entorno de Java con la [acción Java Setup SDK](https://github.com/marketplace/actions/setup-java-jdk). En este ejemplo, configurará el entorno, compilará con Maven y, a continuación, creará, examinará e insertará la imagen en el registro de contenedor.

Los [artefactos de GitHub](https://docs.github.com/en/actions/guides/storing-workflow-data-as-artifacts) son una forma de compartir archivos en un flujo entre trabajos. 

```yaml
on: [push]

name: Container Scan

jobs:
  build-image:
    runs-on: ubuntu-latest    
    steps:
    - name: Checkout
      uses: actions/checkout@v2    

    - name: Login to the Container Registry  
      uses: azure/docker-login@v1
      with:
        login-server: ${{ secrets.ACR_LOGIN_SERVER }}
        username: ${{ secrets.REGISTRY_USERNAME }}
        password: ${{ secrets.REGISTRY_PASSWORD }}

    - name: Setup Java 1.8.x
      uses: actions/setup-java@v1
      with:
        java-version: '1.8.x'
        
    - name: Build with Maven
      run: mvn package --file pom.xml
```

## <a name="build-your-image"></a>Compilación de la imagen 

Cree y etiquete la imagen con el siguiente fragmento de código en el flujo de trabajo. El siguiente fragmento de código usa la CLI de Docker para crear y etiquetar la imagen dentro de un terminal de shell. 

```yaml
    - name: Build and Tag image
      run: |
        docker build -f ./Dockerfile -t ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }} .
        
```

## <a name="scan-the-image"></a>Examen de la imagen

Antes de insertar la imagen integrada en el registro de contenedor, asegúrese de examinar y comprobar la imagen en busca de vulnerabilidades mediante la [acción Detección de imágenes de contenedor](https://github.com/marketplace/actions/container-image-scan).

Agregue el siguiente fragmento de código al flujo de trabajo, que examinará la imagen en busca de ***vulnerabilidades críticas***, para que la imagen que se insertará sea segura y cumpla los estándares.

También puede agregar otras entradas con esta acción o agregar un archivo `allowedlist.yaml` al repositorio para omitir las vulnerabilidades y las comprobaciones de procedimientos recomendados. 

```yaml
    - name: Scan image
      uses: Azure/container-scan@v0
      with:
        image-name: ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        severity-threshold: CRITICAL
        run-quality-checks: true        
```

Ejemplo: `allowedlist.yaml`.

```yaml
general:
  vulnerabilities:
    - CVE-2003-1307
    - CVE-2011-3374
  bestPracticeViolations:
    - CIS-DI-0005
```
## <a name="push-the-image-to-a-private-registry"></a>Inserción de la imagen en un registro privado

Una vez que se examina la imagen y no se detectan vulnerabilidades, es seguro insertar la imagen creada en un registro privado. El siguiente fragmento de código usa la CLI de Docker en un terminal de shell para insertar la imagen.

```yaml
    - name: Push image
      run: |
        docker push ${{ secrets.ACR_LOGIN_SERVER }}/spring-petclinic:${{ github.run_number }}
        
```
## <a name="next-steps"></a>Pasos siguientes
- Obtenga información en [Implementación en Azure Container Instances desde Azure Container Registry](../container-instances/container-instances-using-azure-container-registry.md).