---
title: Desarrollo distribuido y colaborativo de recursos de Azure DevTest Labs
description: En este artículo se indican los procedimientos recomendados para establecer un entorno de desarrollo distribuido y colaborativo para desarrollar recursos de DevTest Labs.
ms.topic: conceptual
ms.date: 06/26/2020
ms.openlocfilehash: f24ab6e612762df5ee0a0f869507c51ac9e5f538
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128644322"
---
# <a name="best-practices-for-distributed-and-collaborative-development-of-azure-devtest-labs-resources"></a>Procedimientos recomendados para el desarrollo distribuido y colaborativo de recursos de Azure DevTest Labs
El desarrollo distribuido y colaborativo permite que diferentes equipos o personas desarrollen y mantengan un código base. Para que tenga éxito, el proceso de desarrollo depende de la capacidad de crear, compartir e integrar la información. Este principio clave de desarrollo se puede aplicar en Azure DevTest Labs. En un laboratorio, hay varios tipos de recursos que normalmente se distribuyen entre los diferentes laboratorios de una empresa. Los diferentes tipos de recursos se centran en dos áreas:

- Recursos que se almacenan internamente en el laboratorio (basados en laboratorios).
- Recursos que se almacenan en [repositorios externos conectados al laboratorio](devtest-lab-add-artifact-repo.md) (basados en repositorios de código). 

En este documento se describen algunos procedimientos recomendados que permiten la colaboración y la distribución entre varios equipos, al mismo tiempo que se garantiza la personalización y la calidad en todos los niveles.

## <a name="lab-based-resources"></a>Recursos basados en laboratorios

### <a name="custom-virtual-machine-images"></a>Imágenes de máquinas virtuales personalizadas
Puede disponer de un origen común de imágenes personalizadas que se implementan en los laboratorios durante la noche. Para obtener más información, consulte [Generador de imágenes](image-factory-create.md).    

### <a name="formulas"></a>Fórmulas
Las [fórmulas](devtest-lab-manage-formulas.md) son específicas del laboratorio y no tienen un mecanismo de distribución. Los miembros del laboratorio se encargan del desarrollo de las fórmulas al completo. 

## <a name="code-repository-based-resources"></a>Recursos basados en repositorios de código
Hay dos características diferentes que se basan en repositorios de código, artefactos y entornos. En este artículo se describen no solo las características, sino también la manera de configurar eficazmente los repositorios y el flujo de trabajo para permitir personalizar los artefactos y los entornos disponibles en la organización o el equipo.  Este flujo de trabajo se basa en una [estrategia estándar de bifurcación para el control de código fuente](/azure/devops/repos/tfvc/branching-strategies-with-tfvc). 

### <a name="key-concepts"></a>Conceptos clave
La información de origen para artefactos incluye metadatos y scripts. La información de origen para entornos incluye metadatos y plantillas de Resource Manager con todos los archivos auxiliares necesarios, como scripts de PowerShell, scripts de DSC, archivos ZIP, etc.  

### <a name="repository-structure"></a>Estructura de repositorios  
La configuración más común para el control de código fuente (SCC) consiste en establecer una estructura de varios niveles para almacenar los archivos de código (plantillas de Resource Manager, metadatos y scripts) que se usan en los laboratorios. En concreto, cree diferentes repositorios para almacenar recursos administrados por los distintos niveles de la empresa:   

- Recursos de toda la empresa
- Recursos de la unidad o la división empresarial
- Recursos específicos del equipo

Cada uno de estos niveles está vinculado a otro repositorio en el que la rama principal debe tener calidad de producción. Las [ramas](/azure/devops/repos/git/git-branching-guidance) de cada repositorio están destinadas al desarrollo de esos recursos específicos (artefactos o plantillas). Esta estructura se adapta a la perfección a DevTest Labs, ya que se pueden conectar fácilmente varios repositorios y ramas al mismo tiempo a los laboratorios de la organización. El nombre del repositorio se incluye en la interfaz de usuario (UI) para evitar las confusiones en caso de que haya nombres, descripciones o publicadores iguales.
     
En el diagrama siguiente se muestran dos repositorios: un repositorio de empresa que mantiene la división de TI y un repositorio de división que mantiene la división de I+D.

![Ejemplo de entorno de desarrollo distribuido y colaborativo](./media/best-practices-distributive-collaborative-dev-env/distributive-collaborative-dev-env.png)
   
Esta estructura por niveles permite el desarrollo y, al mismo tiempo, mantiene una mayor calidad en la rama principal, mientras que la existencia de varios repositorios conectados a un laboratorio ofrece una mayor flexibilidad.

## <a name="next-steps"></a>Pasos siguientes    
Vea los artículos siguientes:

- Adición de un repositorio a un laboratorio con [Azure Portal](devtest-lab-add-artifact-repo.md) o la [plantilla de Azure Resource Manager](add-artifact-repository.md)
- [Artefactos de DevTest Labs](devtest-lab-artifact-author.md)
- [Entornos de DevTest Labs](devtest-lab-create-environment-from-arm.md)
