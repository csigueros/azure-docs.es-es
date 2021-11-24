---
title: Uso de DevTest Labs en canalizaciones de compilaciones y versiones de Azure Pipelines
description: Obtenga información sobre cómo usar Azure DevTest Labs en canalizaciones de versiones y compilaciones de Azure Pipelines.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: a47f61f7e94751c6e639de83a1748970f947a8a1
ms.sourcegitcommit: e1037fa0082931f3f0039b9a2761861b632e986d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2021
ms.locfileid: "132400383"
---
# <a name="use-devtest-labs-in-azure-pipelines-build-and-release-pipelines"></a>Uso de DevTest Labs en canalizaciones de compilaciones y versiones de Azure Pipelines
En este artículo se proporciona información sobre cómo se puede usar DevTest Labs en canalizaciones de versiones y compilaciones de Azure Pipelines. 

## <a name="overall-flow"></a>Flujo general
El flujo básico consiste en tener una **canalización de compilación**, que se encarga de las tareas siguientes:

1. Compilación del código de la aplicación
1. Creación del entorno base en DevTest Labs
1. Actualización del entorno con información personalizada
1. Implementación de la aplicación en el entorno de DevTest Labs
1. Prueba del código 

Una vez que la compilación se haya completado correctamente, la **canalización de versión** usará los artefactos de compilación para iniciar la implementación provisional o de producción. 

Una de las premisas necesarias es que toda la información necesaria para volver a crear el ecosistema probado esté disponible dentro de los artefactos de compilación, incluida la configuración de los recursos de Azure. Debido a que el uso de estos supone un costo, las empresas quieren controlar o realizar un seguimiento del uso de estos recursos. En algunas situaciones, otro departamento, como el de TI, puede administrar las plantillas de Azure Resource Manager que se usan para crear y configurar los recursos. Además, estas plantillas pueden almacenarse en otro repositorio. Esto conduce a una situación interesante al crear y probar una compilación. Tanto el código como la configuración deben almacenarse en los artefactos de compilación para recrear el sistema en producción. 

Mediante el uso de DevTest Labs durante la fase de compilación y prueba, puede agregar plantillas de Azure Resource Manager y archivos compatibles a los orígenes de compilación. Durante la fase de lanzamiento, implementará la configuración exacta que usó en la prueba en producción. La tarea **Creación de entorno de Azure DevTest Labs** con la configuración adecuada guarda las plantillas de Resource Manager en los artefactos de compilación. En este ejemplo, puede usar el código que obtendrá en [Tutorial: Creación de una aplicación web .NET Core y SQL Database en Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md), para implementar y probar la aplicación web en Azure.

![Diagrama que muestra el flujo general.](./media/use-devtest-labs-build-release-pipelines/overall-flow.png)

## <a name="set-up-azure-resources"></a>Configuración de los recursos de Azure
Debe crear un par de elementos con antelación:

- Dos repositorios: El primero, con el código del tutorial y una plantilla de Resource Manager con dos máquinas virtuales adicionales. El segundo contendrá la plantilla de Azure Resource Manager base (configuración existente).
- Un grupo de recursos para la implementación del código y la configuración de producción.
- Un laboratorio con una [conexión al repositorio de configuración](devtest-lab-create-environment-from-arm.md) de la canalización de compilación. Inserte la plantilla de Resource Manager en el repositorio de configuración como azuredeploy.json con metadata.json. Este nombre permite a DevTest Labs reconocer e implementar la plantilla.

La canalización de compilación crea un entorno de DevTest Labs e implementa el código para las pruebas.

## <a name="set-up-a-build-pipeline"></a>Configuración de una canalización de compilación
En Azure Pipelines, cree una canalización de compilación mediante el código que encontrará en [Tutorial: compilación de la aplicación web de .NET Core y SQL Database en Azure App Service](../app-service/tutorial-dotnetcore-sqldb-app.md). Use la plantilla **ASP.NET Core**, que rellenará la tarea necesaria para compilar, probar y publicar el código.

![Captura de pantalla que muestra la selección de la plantilla de ASP.NET.](./media/use-devtest-labs-build-release-pipelines/select-asp-net.png)

Agregue tres tareas adicionales para crear el entorno en DevTest Labs e implementarlo en el entorno.

![Captura de pantalla que muestra una canalización con tres tareas.](./media/use-devtest-labs-build-release-pipelines/pipeline-tasks.png)

### <a name="create-environment-task"></a>Tarea Creación del entorno
En la tarea **Creación de entorno de Azure DevTest Labs**, use las listas desplegables para seleccionar los valores siguientes:

- Suscripción de Azure
- Nombre del laboratorio.
- Nombre del repositorio
- Nombre de la plantilla (que se mostrará en la carpeta donde se almacena el entorno). 

Se recomienda usar listas desplegables en la página, en lugar de escribir la información manualmente. Si escribe manualmente la información, escriba los identificadores completos de los recursos de Azure. En la tarea se muestran los nombres descriptivos, en lugar de los identificadores de los recursos. 

El nombre del entorno es el nombre que se muestra en DevTest Labs. Debe ser un nombre único para cada compilación. Por ejemplo: **TestEnv$(Build.BuildId)** . 

Puede especificar un archivo de parámetros o los parámetros para pasar información a la plantilla de Resource Manager. 

Seleccione la opción **Create output variables based on the environment template output** (Crear variables de salida basadas la salida de la plantilla de entorno) y escriba un nombre de referencia. En este ejemplo, escriba **BaseEnv** para el nombre de referencia. Puede usar BaseEnv al configurar la tarea siguiente. 

![Captura de pantalla que muestra la tarea Creación de entorno de Azure DevTest Labs.](./media/use-devtest-labs-build-release-pipelines/create-environment.png)

### <a name="populate-environment-task"></a>Tarea Relleno del entorno
La segunda tarea (**Relleno de entorno de Azure DevTest Labs**) consiste en actualizar el entorno de DevTest Labs existente. La tarea de creación del entorno genera **BaseEnv.environmentResourceId**, que se usa para configurar el nombre del entorno para esta tarea. La plantilla de Resource Manager para este ejemplo tiene dos parámetros: **adminUserName** y **adminPassword**. 

![Captura de pantalla que muestra la tarea Relleno de entorno de Azure DevTest Labs.](./media/use-devtest-labs-build-release-pipelines/populate-environment.png)

## <a name="app-service-deploy-task"></a>Tarea Implementación de App Service
La tercera tarea es **Implementación de Azure App Service**. El tipo de aplicación se establece en **Aplicación web** y el nombre de App Service se establece en **$(WebSite)**.

![Captura de pantalla que muestra la tarea Implementación de App Service.](./media/use-devtest-labs-build-release-pipelines/app-service-deploy.png)

## <a name="set-up-release-pipeline"></a>Configuración de una canalización de versión
Puede crear una canalización de versión con dos tareas: **Implementación de Azure: crear o actualizar el grupo de recursos** e **Implementación de Azure App Service**. 

En la primera tarea, especifique el nombre y la ubicación del grupo de recursos. La ubicación de la plantilla es un artefacto vinculado. Si la plantilla de Resource Manager incluye plantillas vinculadas, debe realizar una implementación de grupo de recursos personalizado. La plantilla está en el artefacto de colocación publicado. Invalide los parámetros de la plantilla de Resource Manager. Puede dejar la configuración restante con los valores predeterminados. 

Para la segunda tarea, **Implementación de Azure App Service**, especifique la suscripción de Azure, seleccione **Aplicación web** para **Tipo de aplicación** y **$(WebSite)** para el **nombre de App Service**. Puede dejar la configuración restante con los valores predeterminados. 

## <a name="test-run"></a>Serie de pruebas
Ahora que ambas canalizaciones están configuradas, ponga en cola manualmente una compilación y vea si funciona. El siguiente paso consiste en establecer el desencadenador adecuado para la compilación y conectar la compilación a la canalización de versión.

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes:

- [Integración de Azure DevTest Labs en la canalización de entrega e integración continuas de Azure Pipelines](devtest-lab-integrate-ci-cd.md)
- [Integración de entornos en las canalizaciones de integración y entrega continuas de Azure Pipelines](integrate-environments-devops-pipeline.md)
