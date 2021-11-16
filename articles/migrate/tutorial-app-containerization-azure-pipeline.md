---
title: Implementación continua de aplicaciones contenedorizadas con Azure DevOps
description: 'Tutorial: Implementación continua de aplicaciones contenedorizadas con Azure DevOps'
services: ''
author: rahug1190
manager: bsiva
ms.topic: tutorial
ms.date: 11/08/2021
ms.author: rahugup
ms.openlocfilehash: 7d23b7c84cc7a7053bfa80b3f6e1b24c3bd4470c
ms.sourcegitcommit: 27ddccfa351f574431fb4775e5cd486eb21080e0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2021
ms.locfileid: "132000569"
---
# <a name="continuous-deployment-for-containerized-applications-with-azure-devops"></a>Implementación continua de aplicaciones contenedorizadas con Azure DevOps

En esta guía paso a paso, aprenderá a crear una canalización para la creación e implementación continuas de aplicaciones contenedorizadas para operaciones de día 2 con Azure DevOps. Cada vez que modifique el código de un repositorio que contenga un archivo Dockerfile, las imágenes se insertarán en Azure Container Registry y los manifiestos se implementarán en Azure Kubernetes Service o Azure App Service.

Azure DevOps permite hospedar, crear, planear y probar el código con flujos de trabajo complementarios. Si utiliza Azure Pipelines como uno de estos flujos de trabajo, podrá implementar una aplicación con CI/CD que funcione con cualquier plataforma y en cualquier nube. Una canalización se define como un archivo YAML que se encuentra en el directorio raíz del repositorio.

## <a name="prerequisites"></a>Prerrequisitos

Antes de comenzar este tutorial, debe:

-  Incluir la aplicación web ASP.NET o Java en contenedores e implementarla utilizando la contenedorización de aplicaciones de Azure Migrate.
-  Tener una cuenta de GitHub en la que pueda crear un repositorio. Si no tiene ninguna, puede [crearla gratis](https://github.com/).
-  Una organización de Azure DevOps. Si no tiene ninguna, puede [crearla gratis](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). (Las organizaciones de Azure DevOps son diferentes de las organizaciones de GitHub. Puede asignar a la organización de DevOps y la de GitHub el mismo nombre si desea que estén coordinadas). <br/> Si el equipo ya tiene una, asegúrese de que usted es el administrador del proyecto de Azure DevOps que desea usar.
-  Tener capacidad para ejecutar canalizaciones en agentes hospedados por Microsoft. Puede comprar un trabajo [paralelo](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops) o solicitar un nivel gratuito. Para solicitar un nivel gratuito, siga las instrucciones que se indican en [este artículo](/azure/devops/pipelines/licensing/concurrent-jobs?view=azure-devops). Tenga en cuenta que se pueden tardar 2 o 3 días laborables en conceder el nivel gratuito.


## <a name="locate-the-artifacts"></a>Localización de los artefactos

La herramienta de contenedorización de aplicaciones de Azure Migrate genera automáticamente artefactos que se pueden usar para configurar un flujo de trabajo de CI/CD en la aplicación mediante Azure Pipelines. Los artefactos se generan una vez que la implementación de la aplicación se ha completado utilizando esta herramienta. Para encontrar los artefactos, siga estos pasos: 

1. Vaya a la máquina que ejecuta la herramienta de contenedorización de aplicaciones de Azure Migrate. 
2. Vaya al directorio **C:\ProgramData\Microsoft Azure Migrate App Containerization**. Si no puede acceder a C:\ProgramData, compruebe que la opción para mostrar *elementos ocultos* de *Vista* esté seleccionada en el Explorador de archivos. 
3. Seleccione el directorio que corresponda a la **dirección IP de la máquina de origen/FQDN**. La máquina de origen es la máquina especificada en la herramienta de contenedorización de aplicaciones que ejecuta la aplicación que se ha contenedorizado.
4. En el caso de las aplicaciones Java 
    - Vaya a **JavaTomcatWebApp\Artifacts**.    
    - Vaya al directorio **Catalina\localhost**. Si no encuentra este directorio, intente acceder al directorio correspondiente con el nombre del motor de Tomcat y el nombre de host.
    - Busque la carpeta de la aplicación dentro de este directorio. 
5. En el caso de las aplicaciones ASP.NET
    - Vaya a **IISAspNetWebApp**.
    - Busque la carpeta de la aplicación dentro de este directorio.


## <a name="upload-artifacts-to-github"></a>Carga de artefactos en GitHub

Los artefactos tienen que cargarse en un repositorio de origen que se usará con Azure DevOps. 

1. Inicie sesión en su cuenta de GitHub. 
2. Siga los pasos que se indican en [este artículo](https://docs.github.com/get-started/quickstart/create-a-repo) para crear y usar un nuevo repositorio. 
3. El próximo paso consiste en cargar los siguientes artefactos en este repositorio.
   -  En el caso de las aplicaciones Java, seleccione las siguientes carpetas y archivos en la carpeta de la aplicación situada en la máquina que ejecuta la herramienta de contenedorización de aplicaciones.
        - Carpeta MandatoryArtifacts
        - Carpeta manifests
        - Carpeta OptionalArtifacts
        - Dockerfile 
        - Archivo Entryscript.sh
        - Archivo azure-pipelines.yml
    - En el caso de las aplicaciones ASP.NET, seleccione las siguientes carpetas y archivos en la carpeta de la aplicación situada en la máquina que ejecuta la herramienta de contenedorización de aplicaciones.
        - Carpeta manifests
        - Carpeta Build
        - Archivo azure-pipelines.yml

## <a name="sign-in-to-azure-pipelines"></a>Inicio de sesión en Azure Pipelines

Inicie sesión en [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines). Una vez que haya iniciado sesión, el explorador accederá a https://dev.azure.com/my-organization-name y aparecerá el panel de Azure DevOps.

En la organización seleccionada, cree un *proyecto*. Si no tiene ningún proyecto en la organización, aparecerá la pantalla **Cree un proyecto para empezar**. De lo contrario, seleccione el botón **Crear proyecto** en la esquina superior derecha del panel.

## <a name="add-service-connections"></a>Adición de conexiones de servicio

Para poder crear la canalización, primero debe crear las conexiones de servicio, ya que se le pedirá que elija y compruebe las conexiones de la plantilla. Tener una conexión de servicio le permitirá conectarse a Azure Container Registry cuando utilice las plantillas de tarea y a la suscripción de Azure en la que desea implementar la aplicación. 

1. En la esquina inferior izquierda, seleccione **Configuración del proyecto > Conexiones de servicio**.
2. Seleccione **Nueva conexión de servicio** y la opción **Docker Registry > Azure Container Registry** que corresponda al tipo de conexión de servicio que necesita. Haga clic en Siguiente.
3. Elija un método de autenticación y seleccione Siguiente.
4. Escriba los parámetros de la conexión de servicio. La lista de parámetros será diferente para cada tipo de conexión de servicio. Para más información, consulte la [lista de tipos de conexión de servicio y los parámetros asociados](/azure/devops/pipelines/library/service-endpoints?view=azure-devops&tabs=yaml#common-service-connection-types).
5. Seleccione **Guardar** para crear la conexión.
6. **Valide** la conexión cuando se haya creado y se especificarán los parámetros. El vínculo de validación utiliza una llamada REST al servicio externo con la información especificada e indica si la llamada se ha hecho correctamente.
7. Repita los mismos pasos para crear una conexión de servicio a la suscripción de Azure seleccionando **Nueva conexión de servicio > Azure Resource Manager**. 
8. Anote el identificador de recurso de las dos conexiones de servicio. 

## <a name="create-the-pipeline"></a>Creación de la canalización

Ahora que ha creado las dos conexiones de servicio, puede configurar la canalización. La herramienta de contenedorización de aplicaciones ha creado automáticamente la canalización YAML y se puede configurar de la siguiente manera:  

1. Vaya a Canalizaciones y seleccione **Nueva canalización**.
2. Siga los pasos del asistente y seleccione primero **GitHub** como ubicación del código fuente.
3. Puede que se le redirija a GitHub para iniciar sesión. Si es así, escriba sus credenciales de GitHub.
4. Cuando aparezca la lista de repositorios, seleccione el que corresponda.
5. Es posible que se le redirija a GitHub para instalar la aplicación Azure Pipelines. Si es así, seleccione Aprobar e instalar.
6. Cuando aparezca la nueva canalización, consulte el archivo YAML para ver lo que hace.
7. En el archivo YAML, especifique el identificador de recurso de la conexión de servicio de Azure Container Registry como valor de la variable **dockerRegistryServiceConnection**.
8. Especifique el identificador de recurso de la conexión de servicio de Azure Resource Manager como valor de la variable **dockerRegistryServiceConnection**.
9. Cuando esté todo listo, haga clic en **Guardar** para confirmar la nueva canalización en el repositorio. 

La canalización estará totalmente configurada para crear e implementar las operaciones contenedorizadas del día 2. Puede [personalizar la canalización](/azure/devops/pipelines/customize-pipeline?view=azure-devops#prerequisite) para que se ajuste a las necesidades de su organización. 