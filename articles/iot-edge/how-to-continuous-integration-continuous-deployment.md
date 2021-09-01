---
title: 'Integración e implementación continuas en dispositivos Azure IoT Edge: Azure IoT Edge'
description: 'Configuración de integración e implementación continuas mediante YAML: Azure IoT Edge con Azure DevOps, Azure Pipelines'
author: kgremban
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: c6cc3f1f49150be53d2da4e1dfc7b40d291999f5
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121722182"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Integración e implementación continuas en dispositivos Azure IoT Edge

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Puede adoptar fácilmente DevOps con las aplicaciones de Azure IoT Edge con las tareas integradas de Azure IoT Edge en Azure Pipelines. En este artículo se muestra cómo puede usar Azure Pipelines para crear, probar e implementar módulos de Azure IoT Edge mediante YAML. Como alternativa, puede [usar el editor clásico](how-to-continuous-integration-continuous-deployment-classic.md).

![Diagrama: ramas CI y CD para desarrollo y producción](./media/how-to-continuous-integration-continuous-deployment/model.png)

En este artículo, aprenderá a usar las [tareas integradas de Azure IoT Edge](/azure/devops/pipelines/tasks/build/azure-iot-edge) para Azure Pipelines para crear canalizaciones de compilación y versión para la solución de IoT Edge. Cada tarea de Azure IoT Edge que se agrega a la canalización implementa una de las cuatro acciones siguientes:

 | Acción | Descripción |
 | --- | --- |
 | Generar imágenes de módulo | Usa el código de su solución de IoT Edge y compila las imágenes de contenedor.|
 | Insertar imágenes de módulo | Inserta las imágenes del módulo en el registro de contenedor especificado. |
 | Generar el manifiesto de implementación | Usa el archivo deployment.template.json y sus variables para generar el archivo final de manifiesto de implementación de IoT Edge. |
 | Implementación en dispositivos de IoT Edge | Crea implementaciones de IoT Edge en uno o varios dispositivos IoT Edge. |

A menos que se especifique lo contrario, los procedimientos descritos en este artículo no exploran toda la funcionalidad disponible mediante parámetros de tarea. Para obtener más información, consulte los siguientes recursos:

* [Versión de tarea](/azure/devops/pipelines/process/tasks?tabs=yaml#task-versions)
* [Opciones de control](/azure/devops/pipelines/process/tasks?tabs=yaml#task-control-options)
* [Variables de entorno](/azure/devops/pipelines/process/variables?tabs=yaml#environment-variables)
* [Variables de salida](/azure/devops/pipelines/process/variables?tabs=yaml#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Requisitos previos

* Un repositorio de Azure Repos. Si no tiene uno, puede [crear un nuevo repositorio de Git en el proyecto](/azure/devops/repos/git/create-new-repo). En este artículo, hemos creado un repositorio denominado **IoTEdgeRepo**.
* Una solución de IoT Edge confirmada e insertada en el repositorio. Si quiere crear una nueva solución de ejemplo para hacer pruebas según este artículo, siga los pasos para [desarrollar y depurar módulos en Visual Studio Code](how-to-vs-code-develop-module.md) o [desarrollar y depurar módulos de C# en Visual Studio](./how-to-visual-studio-develop-module.md). Para este artículo, hemos creado una solución en el repositorio denominada **IoTEdgeSolution**, que tiene el código de un módulo denominado **filtermodule**.

  En este artículo, todo lo que necesita es la carpeta de la solución creada mediante las plantillas de IoT Edge en Visual Studio Code o Visual Studio. No es necesario compilar, insertar, implementar ni depurar este código antes de continuar. Configurará esos procesos en Azure Pipelines.

  Conozca la ruta de acceso al archivo **deployment.template.js** de la solución, ya que se usa en varios pasos. Si no está familiarizado con el rol de la plantilla de implementación, consulte [Aprenda a implementar módulos y establecer rutas](module-composition.md).

  >[!TIP]
  >Si está creando una nueva solución, clone el repositorio localmente en primer lugar. A continuación, cuando cree la solución, puede elegir crearlo directamente en la carpeta del repositorio. Puede confirmar e insertar fácilmente los nuevos archivos desde allí.

* Un registro de contenedor donde pueda insertar imágenes del módulo. Puede usar [Azure Container Registry](../container-registry/index.yml) o un registro de terceros.
* Un [centro de IoT](../iot-hub/iot-hub-create-through-portal.md) de Azure activo con al menos dos dispositivos de IoT Edge para hacer pruebas de las fases independientes de implementación de prueba y producción. Puede seguir los artículos de la guía de inicio rápido para crear un dispositivo IoT Edge en [Linux](quickstart-linux.md) o [Windows](quickstart.md)

Para más información sobre el uso Azure Repos, consulte [Share your code with Visual Studio and Azure Repos](/azure/devops/repos/git/share-your-code-in-git-vs) (Compartir el código con Visual Studio y Azure Repos).

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Crear una canalización de compilación para la integración continua

En esta sección, creará una nueva canalización de compilación. Configure la canalización para que se ejecute automáticamente cuando se inserte algún cambio en la solución de IoT Edge de ejemplo y para publicar registros de compilación.

1. Inicie sesión en la organización de Azure DevOps (`https://dev.azure.com/{your organization}`) y abra el proyecto que contiene el repositorio de la solución de IoT Edge.

   ![Abrir el proyecto de DevOps](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. En el menú del panel izquierdo del proyecto, seleccione **Canalizaciones**. Seleccione **Crear canalización** en el centro de la página. O bien, si ya tiene canalizaciones de compilación, seleccione el botón **Nueva canalización** en la parte superior derecha.

    ![Creación de una nueva canalización de compilación mediante el botón Nueva canalización](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. En la página **¿Dónde está el código?** , seleccione **GIT de Azure Repos`YAML`** . Si quiere usar el editor clásico para crear las canalizaciones de compilación del proyecto, consulte la [guía del editor clásico](how-to-continuous-integration-continuous-deployment-classic.md).

4. Seleccione el repositorio para el que va a crear una canalización.

    ![Seleccionar el repositorio para la canalización de compilación](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. En la página **Configurar la canalización**, seleccione **Canalización inicial**. Si tiene un archivo YAML de Azure Pipelines preexistente que quiere usar para crear esta canalización, puede seleccionar **Archivo YAML de Azure Pipelines existente** y proporcionar la rama y la ruta de acceso del repositorio al archivo.

    ![Seleccione Canalización inicial o Archivo YAML de Azure Pipelines existente para iniciar la canalización de compilación](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. En la página **Revisar YAML de canalización**, puede seleccionar el nombre predeterminado `azure-pipelines.yml` para cambiar el nombre del archivo de configuración de la canalización.

   Seleccione **Mostrar el asistente** para abrir la paleta de **Tareas**.

    ![Seleccione Mostrar el asistente para abrir la paleta de Tareas](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Para agregar una tarea, coloque el cursor al final del código YAML o donde quiera agregar las instrucciones para la tarea. Busque y seleccione **Azure IoT Edge**. Rellene los parámetros de la tarea como se indica a continuación. Después, seleccione **Agregar**.

   | Parámetro | Descripción |
   | --- | --- |
   | Acción | Seleccione **Generar imágenes de módulo**. |
   | archivo .template.json | Proporcione la ruta de acceso al archivo **deployment.template.json** en el repositorio que contiene la solución de IoT Edge. |
   | Plataforma predeterminada | Seleccione el sistema operativo adecuado para los módulos en función de su dispositivo IoT Edge de destino. |

   Para más información sobre esta tarea y sus parámetros, consulte [Tarea de Azure IoT Edge](/azure/devops/pipelines/tasks/build/azure-iot-edge).

   ![Uso de la paleta Tareas para agregar tareas a la canalización](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > Después de agregar cada tarea, el editor resaltará automáticamente las líneas agregadas. Para evitar que se sobrescriba accidentalmente, anule la selección de las líneas y proporcione un nuevo espacio para la siguiente tarea antes de agregar tareas adicionales.

8. Repita este proceso para agregar tres tareas más con los parámetros siguientes:

   * Tarea: **Azure IoT Edge**

     | Parámetro | Descripción |
     | --- | --- |
     | Acción | Seleccione **Insertar imágenes de módulo**. |
     | Tipo de registro de contenedor | Use el tipo predeterminado: **Azure Container Registry**. |
     | Suscripción de Azure | Seleccione su suscripción. |
     | Azure Container Registry | Elija el registro que quiera usar para la canalización. |
     | archivo .template.json | Proporcione la ruta de acceso al archivo **deployment.template.json** en el repositorio que contiene la solución de IoT Edge. |
     | Plataforma predeterminada | Seleccione el sistema operativo adecuado para los módulos en función de su dispositivo IoT Edge de destino. |

     Para más información sobre esta tarea y sus parámetros, consulte [Tarea de Azure IoT Edge](/azure/devops/pipelines/tasks/build/azure-iot-edge).

   * Tarea: **copia de archivos**

     | Parámetro | Descripción |
     | --- | --- |
     | Carpeta de origen | Carpeta de origen desde la que se copiará. Empty es la raíz del repositorio. Use variables si los archivos no están en el repositorio. Ejemplo: `$(agent.builddirectory)`.
     | Contenido | Agregue dos líneas: `deployment.template.json` y `**/module.json`. |
     | Carpeta de destino | Especifique la variable `$(Build.ArtifactStagingDirectory)`. Consulte las [variables de compilación](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) para obtener información acerca de sus descripciones. |

     Para más información sobre esta tarea y sus parámetros, consulte [Tarea de copia de archivos](/azure/devops/pipelines/tasks/utility/copy-files).

   * Tarea: **Publicar artefactos de compilación**

     | Parámetro | Descripción |
     | --- | --- |
     | Ruta de acceso para publicar | Especifique la variable `$(Build.ArtifactStagingDirectory)`. Consulte las [variables de compilación](/azure/devops/pipelines/build/variables?tabs=yaml#build-variables) para obtener información acerca de sus descripciones. |
     | Nombre del artefacto | Especifique el nombre predeterminado: `drop` |
     | Ubicación de publicación de artefactos | Use la ubicación predeterminada: `Azure Pipelines` |

     Para más información sobre esta tarea y sus parámetros, consulte [Publicar artefactos de compilación](/azure/devops/pipelines/tasks/utility/publish-build-artifacts).

9. Seleccione **Guardar** en la lista desplegable **Guardar y ejecutar** de la parte superior derecha.

10. El desencadenador para la integración continua está habilitado de forma predeterminada para la canalización YAML. Si quiere editar esta configuración, seleccione la canalización y haga clic en **Editar** en la parte superior derecha. Seleccione **Más acciones** junto al botón **Ejecutar** en la parte superior derecha y vaya a **Desencadenadores**. La **Integración continua** aparece como habilitada en el nombre de la canalización. Si quiere ver los detalles del desencadenador, active la casilla **Invalidar el desencadenador de integración continua de YAML desde aquí**.

    ![Para revisar la configuración del desencadenador de la canalización, consulte Desencadenadores en Más acciones.](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Continúe con la sección siguiente para compilar la canalización de versión.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Pasos siguientes

* Ejemplo de DevOps en IoT Edge en [Azure DevOps Starter para IoT Edge](how-to-devops-starter.md)
* Puede encontrar información sobre la implementación de IoT Edge en [Descripción de las implementaciones de IoT Edge en un único dispositivo o a escala](module-deployment-monitoring.md).
* Siga los pasos para crear, actualizar o eliminar una implementación en [Implementación y supervisión de módulos de IoT Edge a escala](how-to-deploy-at-scale.md).