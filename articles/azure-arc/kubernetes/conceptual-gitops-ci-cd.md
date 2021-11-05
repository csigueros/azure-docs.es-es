---
title: 'Flujo de trabajo de CI/CD con GitOps: Kubernetes habilitado para Azure Arc'
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
description: En este artículo se proporciona información general conceptual de un flujo de trabajo de CI/CD con GitOps.
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, contenedores, CI, CD, Azure DevOps
ms.openlocfilehash: 837c0124739fa6659fc4c7652fcb9ca7be4fbf85
ms.sourcegitcommit: 106f5c9fa5c6d3498dd1cfe63181a7ed4125ae6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/02/2021
ms.locfileid: "131039665"
---
# <a name="cicd-workflow-using-gitops---azure-arc-enabled-kubernetes"></a>Flujo de trabajo de CI/CD con GitOps: Kubernetes habilitado para Azure Arc

Las implementaciones de Kubernetes modernas alojan varias aplicaciones, clústeres y entornos. Con GitOps, puede administrar estas configuraciones complejas más fácilmente, con un seguimiento del estado deseado de los entornos de Kubernetes mediante declaración con Git. Con las herramientas comunes de Git para realizar el seguimiento del estado del clúster, puede aumentar la responsabilidad, facilitar la investigación de errores y habilitar la automatización para administrar entornos.

En esta información general conceptual se explica GitOps como realidad en el ciclo de vida de los cambios de la aplicación completa mediante Azure Arc, Azure Repos y Azure Pipelines. [Salte a un ejemplo](#example-workflow) de un cambio de aplicación único en entornos Kubernetes controlados por GitOps.

## <a name="architecture"></a>Architecture

Considere una aplicación implementada en uno o varios entornos de Kubernetes.

![Arquitectura de CI/CD con GitOps](./media/gitops-arch.png)

### <a name="application-repo"></a>Repositorio de aplicaciones
El repositorio de aplicaciones contiene el código de la aplicación en el que los desarrolladores trabajan durante su bucle interno. Las plantillas de implementación de la aplicación residen en este repositorio en un formato genérico, como Helm o Kustomize. No se almacenan los valores específicos del entorno. Los cambios en este repositorio invocan una canalización de PR o CI que inicia el proceso de implementación.
### <a name="container-registry"></a>Container Registry
El registro de contenedor contiene todas las imágenes propias y de terceros que se usan en los entornos de Kubernetes. Etiquete imágenes de aplicaciones propias con etiquetas en lenguaje natural y la confirmación de Git usada para compilar la imagen. Almacene en caché imágenes de terceros para la seguridad, la velocidad y la resistencia. Establezca un plan para las pruebas y la integración oportunas de las actualizaciones de seguridad. Para obtener más información, vea la guía [Consumo y mantenimiento de contenido público con Azure Container Registry Tasks](../../container-registry/tasks-consume-public-content.md) para obtener un ejemplo.
### <a name="pr-pipeline"></a>Canalización de PR
Las solicitudes de incorporación de cambios (PR) al repositorio de la aplicación se validan con una ejecución correcta de la canalización de PR. Esta canalización ejecuta las pruebas de calidad básicas, como el proceso de linting y las pruebas unitarias en el código de la aplicación. La canalización prueba la aplicación y aplica procesos de linting en los archivos de Docker y las plantillas de Helm utilizados para realizar implementaciones en un entorno de Kubernetes. Las imágenes de Docker se deben compilar y probar, pero no se pueden insertar. Mantenga una duración de la canalización relativamente corta para permitir una iteración rápida.
### <a name="ci-pipeline"></a>Canalización de CI
La canalización de CI de la aplicación ejecuta todos los pasos de canalización de PR y expande las comprobaciones de pruebas e implementación. La canalización se puede ejecutar para cada confirmación o a una cadencia regular con un grupo de confirmaciones. En esta fase, realice pruebas de aplicaciones demasiado largas para una canalización de PR. Inserte imágenes de Docker en la instancia de Container Registry tras la preparación de la compilación para la implementación. A la plantilla reemplazada se le puede aplicar linting con una serie de valores de prueba. En esta fase, a las imágenes utilizadas en el entorno de ejecución del servicio se les debe aplicar linting, y se deben compilar y probar. En la compilación de CI, en concreto, los artefactos se publican para que el paso de CD consuma como preparación para la implementación.
### <a name="flux"></a>Flux
Flux es un servicio que se ejecuta en cada clúster y es responsable de mantener el estado deseado. Con frecuencia, el servicio sondea el repositorio de GitOps en busca de cambios en su clúster y los aplica.
### <a name="cd-pipeline"></a>Canalización de CD
La canalización de CD se desencadena automáticamente mediante compilaciones de CI correctas. Usa las plantillas publicadas anteriormente, sustituye los valores de entorno y abre una solicitud de incorporación de cambios al repositorio de GitOps para solicitar un cambio del estado deseado de uno o varios clústeres de Kubernetes. Los administradores de clústeres revisan la PR de cambio de estado y aprueban la combinación en el repositorio de GitOps. La canalización espera a que se complete la PR, lo que permite que Flux detecte el cambio de estado.
### <a name="gitops-repo"></a>Repositorio de GitOps
El repositorio de GitOps representa el estado deseado actual de todos los entornos entre los clústeres. Los cambios en este repositorio los detecta el servicio Flux en cada clúster y se implementan. Las PR se crean con cambios al estado deseado, se revisan y se combinan. Estas PR contienen cambios en las plantillas de implementación y en los manifiestos de Kubernetes representados resultantes. Los manifiestos representados de bajo nivel permiten una inspección más minuciosa de los cambios que normalmente no se muestran en el nivel de plantilla.
### <a name="kubernetes-clusters"></a>Clústeres de Kubernetes
Al menos uno de los clústeres de Kubernetes habilitado para Azure Arc sirve para los diferentes entornos necesarios para la aplicación. Por ejemplo, un único clúster puede atender a un entorno de desarrollo y QA a través de espacios de nombres diferentes. Un segundo clúster puede proporcionar una separación más sencilla de los entornos y un control más preciso.
## <a name="example-workflow"></a>Flujo de trabajo de ejemplo
Como desarrolladora de aplicaciones, Alice:
* Escribe el código de la aplicación.
* Determina cómo ejecutar la aplicación en un contenedor de Docker.
* Define las plantillas que ejecutan el contenedor y los servicios dependientes en un clúster de Kubernetes.

Aunque Alice sabe que la aplicación necesita la funcionalidad para ejecutarse en varios entornos, no conoce la configuración específica para cada entorno.

Suponga que Alice desea hacer un cambio en la aplicación que modifique la imagen de Docker que se usa en la plantilla de implementación de la aplicación.

1. Alice cambia la plantilla de implementación, la envía a una rama remota en el repositorio de la aplicación y abre una PR para revisarla.
2. Alice pide a su equipo que revise el cambio.
    * La canalización de PR ejecuta la validación.
    * Después de una ejecución correcta de la canalización, el equipo realiza la autorización, y el cambio se combina.
3. La canalización de CI valida el cambio de Alice y se completa correctamente.
    * Es seguro implementar el cambio en el clúster, y los artefactos se guardan en la ejecución de la canalización de CI.
4. El cambio de Alice se combina y desencadena la canalización de CD.
    * La canalización de CD detecta los artefactos almacenados por la ejecución de la canalización de CI de Alice.
    * La canalización de CD sustituye las plantillas con valores específicos del entorno y realiza los cambios en el estado del clúster existente en el repositorio de GitOps.
    * La canalización de CD crea una PR al repositorio de GitOps con los cambios deseados en el estado del clúster.
5. El equipo de Alice revisa y aprueba su PR.
    * El cambio se combina en la rama de destino correspondiente al entorno.
6. En cuestión de minutos, Flux observa un cambio en el repositorio de GitOps e incorpora el cambio de Alice.
    * Debido al cambio de imagen de Docker, el pod de aplicación requiere una actualización.
    * Flux aplica el cambio al clúster.
7. Alice comprueba el punto de conexión de la aplicación para comprobar que la implementación se completó correctamente.
   > [!NOTE]
   > En el caso de los entornos destinados a la implementación, la canalización de CD se itera mediante la creación de una PR para el siguiente entorno y repite los pasos 4 a 7. El proceso puede necesitar una aprobación adicional para implementaciones o entornos de más riesgo, como un cambio relacionado con la seguridad o un entorno de producción.
8.  Una vez realizadas correctamente las implementaciones en todos los entornos, la canalización se completa.

## <a name="next-steps"></a>Pasos siguientes
Obtenga más información sobre la creación de conexiones entre el clúster y un repositorio de GIT como un [recurso de configuración con Kubernetes habilitado para Azure Arc](./conceptual-configurations.md).
