---
title: Configuración y uso de entornos públicos
description: En este artículo se describe cómo configurar y usar entornos públicos (plantillas de Azure Resource Manager en un repositorio de Git) en Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 57772ef7308e6db6982344bdddc3c0628d3d668a
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/24/2021
ms.locfileid: "128678561"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Configuración y uso de entornos públicos en Azure DevTest Labs
Azure DevTest Labs tiene un [repositorio público de plantillas de Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que puede usar para crear entornos sin tener que conectarse a un origen externo de GitHub por sí mismo. Este repositorio incluye plantillas usadas con frecuencia, como Azure Web Apps, Clúster de Service Fabric y entorno de desarrollo de Granja de SharePoint. Esta característica es similar al repositorio público de artefactos que se incluye para todos los laboratorios que cree. El repositorio de entorno le permite empezar a trabajar rápidamente con las plantillas de entorno ya creadas, con el mínimo de parámetros de entrada, para proporcionarle una experiencia de introducción sin problemas a los recursos de PaaS en laboratorios. 

## <a name="configuring-public-environments"></a>Configuración de entornos públicos
Como propietario de un laboratorio, puede habilitar el repositorio público del entorno para el laboratorio durante la creación del laboratorio. Para habilitar los entornos públicos para el laboratorio, seleccione **Activado** en el campo **Entornos públicos** al crear un laboratorio. 

![Habilitación de un entorno público para un nuevo laboratorio](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)


Para ver laboratorios existentes, el repositorio del entorno público no está habilitado. Habilítelo manualmente para utilizar plantillas en el repositorio. Para los laboratorios creados mediante plantillas de Resource Manager, el repositorio también está deshabilitado de forma predeterminada.

Puede habilitar o deshabilitar entornos públicos para el laboratorio y también poner a disposición solo entornos específicos para los usuarios del laboratorio mediante los pasos siguientes: 

1. Seleccione **Configuración y directivas** para el laboratorio. 
2. En la sección **BASES PARA MÁQUINAS VIRTUALES**, seleccione **Entornos públicos**.
3. Para habilitar entornos públicos para el laboratorio, seleccione **Sí**. De lo contrario, seleccione **No**. 
4. Si habilitó los entornos públicos, todos los entornos en el repositorio se habilitan de manera predeterminada. Puede anular la selección de un entorno para que no esté disponible para los usuarios del laboratorio. 

![Página Entornos públicos](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Uso de plantillas de entorno como usuario del laboratorio
Como usuario del laboratorio, puede crear un entorno a partir de la lista de plantillas de entorno habilitada simplemente seleccionando **+Agregar** desde la barra de herramientas en la página del laboratorio. La lista de bases incluye las plantillas de entornos públicos habilitadas por el administrador del laboratorio en la parte superior de la lista.

![Plantillas de entornos públicos](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Pasos siguientes
Este es un repositorio de código abierto al que puede contribuir con sus propias plantillas de Resource Manager usadas con frecuencia y útiles. Para contribuir, basta con enviar una solicitud de incorporación de cambios en el repositorio.  
