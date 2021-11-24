---
title: Integración de entornos en Azure Pipelines
description: Aprenda a integrar entornos de Azure DevTest Labs en las canalizaciones de integración continua (CI) y entrega continua (CD) de Azure DevOps.
ms.topic: how-to
ms.date: 11/15/2021
ms.openlocfilehash: b3478242dddd7141100f05ae44a09902a2546ffd
ms.sourcegitcommit: 362359c2a00a6827353395416aae9db492005613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/15/2021
ms.locfileid: "132488102"
---
# <a name="integrate-environments-into-your-cicd-pipelines"></a>Integración de entornos en las canalizaciones de CI/CD 
En este artículo aprenderá a crear e implementar un entorno para luego eliminarlo, todo ello en una canalización completa. Puede usar este entorno para integrar las canalizaciones de versión de integración continua (CI)/entrega continua (CD) con Azure DevTest Labs. Usará la extensión Azure DevTest Labs Tasks en Azure DevOps Services para la integración. Estas extensiones permiten que sea más fácil implementar rápidamente un [entorno](devtest-lab-test-env.md) para una determinada tarea de prueba y, luego, eliminarlo cuando finalice la prueba. 

Lo habitual sería realizar cada tarea de forma individual en su propia canalización de compilación, prueba e implementación. Las extensiones usadas en este artículo son un complemento a estas [tareas de creación o eliminación de máquinas virtuales de DTL](devtest-lab-integrate-ci-cd.md):

- Creación de un entorno
- Eliminación de un entorno

## <a name="prerequisites"></a>Requisitos previos
Para poder integrar la canalización de CI/CD con Azure DevTest Labs, debe hacer lo siguiente: 
1. Instalar la extensión [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks) desde Visual Studio Marketplace. 
1. [Crear un laboratorio](devtest-lab-create-lab.md) y asegurarse de que está configurado para usar el **entorno público**, opción que está activada de forma predeterminada.

## <a name="create-a-release-definition--environment"></a>Creación de una definición de versión y un entorno
Para crear la definición de versión, siga estos pasos:

1. En el proyecto de Azure DevOps, seleccione **Versiones** en la sección **Canalizaciones**.
1. En la pestaña **Versiones**, seleccione **Nueva canalización**.  En la ventana **Seleccionar una plantilla** de la derecha, puede encontrar la lista de plantillas destacadas para patrones de implementación habituales. 
1. Para esta canalización, seleccione **Fase vacía** para empezar a crear el entorno que se usará con fines de desarrollo o pruebas.
1. En la fase vacía, seleccione **Tareas** en la barra de herramientas y, a continuación, seleccione **Fase 1**.

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-stage.png" alt-text="Captura de pantalla que muestra cómo abrir la fase de versión de la canalización.":::

1. Para agregar tareas a la fase, seleccione el signo más (+) en la sección **Trabajo de agente**. Aparece una lista donde puede buscar tareas disponibles. 
1. En la ventana **Agregar tarea**, busque `Azure DevTest Labs Create Environment`.
1. Seleccione la tarea `Azure DevTest Labs Create Environment` en los resultados y, después, **Agregar**.
1. Seleccione la tarea que acaba de agregar. Aparece la ventana **Azure DevTest Labs Create Environment (Preview)** (Crear entorno de Azure DevTest Labs [versión preliminar]).

   :::image type="content" source="./media/integrate-environments-devops-pipeline/new-release-pipeline-environment.png" alt-text="Captura de pantalla que muestra los campos necesarios para el entorno de Azure Pipelines para Azure DevTest Labs.":::

2. En la pestaña **Tareas**, configure el entorno de la siguiente manera:

   |Campo|Description|
   |-----|-----------|
   |**Suscripción a Azure Resource Manager**|Suscripción de Azure Resource Manager para configurar antes de la ejecución. Seleccione una conexión en la lista **Conexiones disponibles del servicio de Azure** o cree una conexión con permisos más restrictivos a la suscripción de Azure. Para más información, consulte [Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints) (Punto de conexión de servicio de Azure Resource Manager).|
   |**Nombre de laboratorio**|Seleccione el nombre de un laboratorio que creó anteriormente (consulte los requisitos previos). En la práctica, debe seleccionar el laboratorio en el que desea realizar la implementación. El recurso se crea en este laboratorio.  También puede usar variables, `$(labName)`.  Escribir manualmente el nombre descriptivo provoca errores, así que use las listas desplegables para seleccionar la información.|
   |**Nombre del entorno**|Nombre del entorno que se va a crear en el laboratorio seleccionado.|
   |**Nombre del repositorio**|Nombre del repositorio de código fuente que contiene la plantilla. Puede elegir el repositorio predeterminado, `Public Environment Repo`, u otro repositorio que contenga la plantilla que desea usar. Los repositorios se diseñan en las directivas de laboratorio.  Escribir manualmente el nombre descriptivo provoca errores, así que use las listas desplegables para seleccionar la información.|
   |**Nombre de plantilla**|Nombre de la plantilla que se usará para crear el entorno. Seleccione el nombre de la plantilla de entorno. Escribir manualmente el nombre descriptivo provoca errores, así que use las listas desplegables para seleccionar la información.| 
   |**Nombre del entorno**|Escriba un nombre para identificar de forma única la instancia del entorno dentro del laboratorio.  Debe ser único dentro del laboratorio.|
   |**Parameters File (Archivo de parámetros)**  & **Parameter overrides (Invalidaciones de parámetros)**|Se usan para pasar parámetros personalizados al entorno. Se pueden usar una o ambas secciones para establecer los valores de parámetro. Por ejemplo, puede usar estos campos para pasar la contraseña cifrada. También puede usar variables para evitar pasar información secreta en los registros e incluso enlazarla a Azure Key Vault.|

## <a name="delete-the-environment"></a>Eliminación del entorno
La fase final consiste en eliminar el entorno que ha implementado en su instancia de Azure DevTest Labs. Lo habitual es eliminar el entorno después de ejecutar las tareas de desarrollo o de ejecutar las pruebas que necesita en los recursos implementados.

En la definición de versión, seleccione **Agregar tareas** y, luego, en la pestaña **Implementar**, agregue una tarea de **eliminación de entorno de Azure DevTest Labs**. Configúrelo de la siguiente forma:

1. Para eliminar la máquina virtual, consulte [Azure DevTest Labs Tasks](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
    1. En **Suscripción de Azure Resource Manager**, seleccione una conexión en la lista **Conexiones a servicios de Azure disponibles**, o cree una conexión con permisos más restrictivos a la suscripción de Azure. Para más información, consulte [Azure Resource Manager service endpoint](/azure/devops/pipelines/library/service-endpoints) (Punto de conexión de servicio de Azure Resource Manager).
    2. En **Nombre del laboratorio**, seleccione aquel donde existe el entorno.
    3. En **Nombre del entorno**, escriba el nombre del entorno que se va a quitar.
2. Escriba un nombre para la definición de versión y, a continuación, guárdelo.

## <a name="next-steps"></a>Pasos siguientes
Vea los artículos siguientes: 
- [Creación de entornos de varias máquinas virtuales con plantillas de Resource Manager](devtest-lab-create-environment-from-arm.md)
- Plantillas de inicio rápido de Resource Manager para la automatización de DevTest Labs desde el [repositorio de GitHub de DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
- [Página de solución de problemas de VSTS](/azure/devops/pipelines/troubleshooting)
