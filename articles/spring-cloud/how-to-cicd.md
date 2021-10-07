---
title: Automatización de implementaciones de aplicaciones en Azure Spring Cloud
description: Describe cómo usar la tarea de Azure Spring Cloud para Azure Pipelines.
author: karlerickson
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 09/13/2021
ms.author: karler
ms.custom: devx-track-java
zone_pivot_groups: programming-languages-spring-cloud
ms.openlocfilehash: b3e5520f91301471bfc9df6bbd4a9cd4a8bc6a6a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128666069"
---
# <a name="automate-application-deployments-to-azure-spring-cloud"></a>Automatización de implementaciones de aplicaciones en Azure Spring Cloud

En este artículo se muestra cómo usar la [tarea de Azure Spring Cloud para Azure Pipelines](/azure/devops/pipelines/tasks/deploy/azure-spring-cloud) para implementar aplicaciones.

Las herramientas de integración continua y entrega continua permiten implementar rápidamente las actualizaciones en las aplicaciones existentes con el mínimo esfuerzo y riesgo. Azure DevOps ayuda a organizar y controlar estos trabajos clave. 

En el vídeo siguiente se describe la automatización de un extremo a otro mediante las herramientas que prefiera, incluida Azure Pipelines.

<br>

> [!VIDEO https://www.youtube.com/embed/D2cfXAbUwDc?list=PLPeZXlCR7ew8LlhnSH63KcM0XhMKxT1k_]

::: zone pivot="programming-language-csharp"

## <a name="create-an-azure-resource-manager-service-connection"></a>Creación de una conexión de servicio de Azure Resource Manager

Primero, cree una conexión de servicio de Azure Resource Manager para el proyecto de Azure DevOps. Para obtener instrucciones, consulte [Conexión a Microsoft Azure](/azure/devops/pipelines/library/connect-to-azure). Asegúrese de seleccionar la misma suscripción que usa para la instancia de servicio de Azure Spring Cloud.

## <a name="build-and-deploy-apps"></a>Compilación e implementación de aplicaciones

Ya puede compilar e implementar los proyectos mediante una serie de tareas. La siguiente plantilla de Azure Pipelines define variables, una tarea de .NET Core para compilar la aplicación y una tarea de Azure Spring Cloud para implementarla.

```yaml
variables:
  workingDirectory: './steeltoe-sample'
  planetMainEntry: 'Microsoft.Azure.SpringCloud.Sample.PlanetWeatherProvider.dll'
  solarMainEntry: 'Microsoft.Azure.SpringCloud.Sample.SolarSystemWeather.dll'
  planetAppName: 'planet-weather-provider'
  solarAppName: 'solar-system-weather'
  serviceName: '<your service name>'

steps:
# Restore, build, publish and package the zipped planet app
- task: DotNetCoreCLI@2
  inputs:
    command: 'publish'
    publishWebProjects: false
    arguments: '--configuration Release'
    zipAfterPublish: false
    modifyOutputPath: false
    workingDirectory: $(workingDirectory)

# Deploy the planet app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(planetAppName)/publish-deploy-planet.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(planetMainEntry)

# Deploy the solar app
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<Service Connection Name>'
    Action: 'Deploy'
    AzureSpringCloud: $(serviceName)
    AppName: 'testapp'
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(workingDirectory)/src/$(solarAppName)/publish-deploy-solar.zip
    RuntimeVersion: 'NetCore_31'
    DotNetCoreMainEntryPath: $(solarMainEntry)
```

::: zone-end
::: zone pivot="programming-language-java"

## <a name="set-up-an-azure-spring-cloud-instance-and-an-azure-devops-project"></a>Configuración de una instancia Azure Spring Cloud y un proyecto de Azure DevOps

En primer lugar, siga estos pasos para configurar una instancia de Azure Spring Cloud existente para usarla con Azure DevOps.

1. Vaya a la instancia de Azure Spring Cloud y, a continuación, cree una aplicación nueva. 
1. Vaya al portal de Azure DevOps y, a continuación, cree un proyecto en la organización elegida. Si no tiene una organización de Azure DevOps, puede crear una gratis.
1. Seleccione **Repositorio** y, a continuación, importe el [código de demostración de Spring Boot](https://github.com/spring-guides/gs-spring-boot) en el repositorio.

## <a name="create-an-azure-resource-manager-service-connection"></a>Creación de una conexión de servicio de Azure Resource Manager

A continuación, cree una conexión de servicio de Azure Resource Manager para el proyecto de Azure DevOps. Para obtener instrucciones, consulte [Conexión a Microsoft Azure](/azure/devops/pipelines/library/connect-to-azure). Asegúrese de seleccionar la misma suscripción que usa para la instancia de servicio de Azure Spring Cloud.

## <a name="build-and-deploy-apps"></a>Compilación e implementación de aplicaciones

Ya puede compilar e implementar los proyectos mediante una serie de tareas. En las secciones siguientes se muestran varias opciones para implementar la aplicación mediante Azure DevOps.

### <a name="deploy-using-a-pipeline"></a>Implementación mediante una canalización

Para realizar una implementación mediante una canalización, siga estos pasos:

1. Seleccione **Canalizaciones** y cree una nueva canalización con una plantilla de Maven.
1. Edite el archivo *azure-pipelines.yml* para establecer el campo `mavenPomFile` en *'complete/pom.xml'* .
1. Seleccione la opción **Mostrar el asistente** que está a la derecha y, a continuación, seleccione la plantilla **Azure Spring Cloud**.
1. Seleccione la conexión de servicio que creó para la suscripción de Azure y, a continuación, seleccione la instancia de Spring Cloud y la instancia de la aplicación. 
1. Deshabilite la opción **Usar implementación provisional**.
1. Establezca el valor de la opción **Paquete o carpeta** como *complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar*.
1. Seleccione **Agregar** para agregar esta tarea a la canalización.
  
   La configuración de la canalización debe coincidir con la siguiente imagen.

   :::image type="content" source="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg" alt-text="Captura de pantalla de la configuración de canalización." lightbox="media/spring-cloud-how-to-cicd/pipeline-task-setting.jpg":::

   También puede compilar e implementar los proyectos mediante la siguiente plantilla de canalización. En primer lugar, este ejemplo define una tarea de Maven para compilar la aplicación, seguida de una segunda tarea que implementa el archivo JAR mediante la tarea de Azure Spring Cloud para Azure Pipelines.

   ```yaml
   steps:
   - task: Maven@3
     inputs:
       mavenPomFile: 'complete/pom.xml'
   - task: AzureSpringCloud@0
     inputs:
       azureSubscription: '<your service connection name>'
       Action: 'Deploy'
       AzureSpringCloud: <your Azure Spring Cloud service>
       AppName: <app-name>
       UseStagingDeployment: false
       DeploymentName: 'default'
       Package: ./target/your-result-jar.jar
   ```

3. Seleccione **Guardar y ejecutar** y espere a que finalice el trabajo.

### <a name="blue-green-deployments"></a>Implementaciones azul-verde

La implementación que se muestra en la sección anterior recibe inmediatamente el tráfico de la aplicación tras la implementación. Esto le permite probar la aplicación en el entorno de producción antes de recibir cualquier tráfico del cliente.

#### <a name="edit-the-pipeline-file"></a>Edición del archivo de canalización

Para compilar la aplicación de la misma manera que se mostró anteriormente y agregarla a una implementación provisional, use la plantilla siguiente. En este ejemplo, la implementación de almacenamiento provisional ya debe existir. Para obtener un enfoque alternativo, consulte [Estrategias de implementación azul-verde](concepts-blue-green-deployment-strategies.md).

```yaml
steps:
- task: Maven@3
  inputs:
    mavenPomFile: 'pom.xml'
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
    Package: ./target/your-result-jar.jar
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Set Production'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: true
```

#### <a name="use-the-releases-section"></a>Uso de la sección Versiones

Los siguientes pasos le muestran cómo habilitar una implementación azul-verde desde la sección **Versiones**.

1. Seleccione **Canalizaciones** y cree una nueva canalización para su artefacto de compilación y publicación de Maven.
   1. Seleccione el **GIT de Azure Repos** para obtener la ubicación del código.
   1. Seleccione el repositorio donde se encuentre el código.
   1. Seleccione la plantilla **Maven** y modifique el archivo para establecer el campo `mavenPomFile` en *`complete/pom.xml`* .
   1. Seleccione la opción **Mostrar el asistente** que está en el lado derecho y, a continuación, seleccione la plantilla **Publicar artefactos de compilación**.
   1. Establezca la **Ruta de acceso para publicar** como *complete/target/spring-boot-complete-0.0.1-SNAPSHOT.jar*.
   1. Seleccione **Guardar y ejecutar**.

1. Seleccione **Versiones** y **Crear versión**.
1. Agregue una nueva canalización y seleccione **Trabajo vacío** para crear un trabajo.
1. En **Fases**, seleccione la línea **1 trabajo, 0 tarea**

   :::image type="content" source="media/spring-cloud-how-to-cicd/create-new-job.jpg" alt-text="Captura de pantalla que indica la opción que debe seleccionar para agregar una tarea a un trabajo.":::

   1. Seleccione **+** para agregar una tarea al trabajo.
   1. Busque la plantilla **Azure Spring Cloud** y seleccione **Agregar** para agregar la tarea al trabajo.
   1. Seleccione **Implementación de Azure Spring Cloud:** para editar la tarea.
   1. Complete la tarea con la información de la aplicación y deshabilite **Usar implementación provisional**.
   1. Habilite **Crear una nueva implementación provisional si no existe una** y escriba un nombre en **Implementación**.
   1. Seleccione **Guardar** para guardar la tarea.
   1. Seleccione **Aceptar**.
1. Seleccione **Canalización** y, a continuación, seleccione **Agregar un artefacto**.
   1. En **Origen (canalización de compilación)** , seleccione la canalización que creó anteriormente.
   1. Seleccione **Aceptar** y, a continuación, **Guardar**.
1. Seleccione **1 trabajo, 1 tarea** en **Fases**.
1. Vaya a la tarea **Implementación de Azure Spring Cloud** en la **Fase 1** y seleccione los puntos suspensivos que están junto a la opción **Paquete o carpeta**.
1. Seleccione *spring-boot-complete-0.0.1-SNAPSHOT.jar* en el cuadro de diálogo y, a continuación, seleccione **Aceptar**.

   :::image type="content" source="media/spring-cloud-how-to-cicd/change-artifact-path.jpg" alt-text="Captura de pantalla del cuadro de diálogo &quot;Seleccionar un archivo o carpeta&quot;.":::

1. Seleccione **+** para agregar otra tarea de **Azure Spring Cloud** al trabajo.
2. Cambie la acción a **Establecer la implementación de producción**.
3. Seleccione **Guardar** y luego **Crear versión** para iniciar automáticamente la implementación. 

Para comprobar el estado actual de la versión de la aplicación, seleccione **Ver versión**. Una vez finalizada esta tarea, visite Azure Portal para comprobar el estado de la aplicación.

### <a name="deploy-from-source"></a>Implementación desde el origen

Para realizar una implementación directamente en Azure sin tener que usar un paso de compilación independiente, use la siguiente plantilla de canalización.

```yaml
- task: AzureSpringCloud@0
  inputs:
    azureSubscription: '<your service connection name>'
    Action: 'Deploy'
    AzureSpringCloud: <your Azure Spring Cloud service>
    AppName: <app-name>
    UseStagingDeployment: false
    DeploymentName: 'default'
    Package: $(Build.SourcesDirectory)
```

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

* [Inicio rápido: Implementación de la primera aplicación de Azure Spring Cloud](./quickstart.md)
