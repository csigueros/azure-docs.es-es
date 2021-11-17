---
title: Configuración y uso de entornos públicos
description: En este artículo se describe cómo configurar y usar entornos públicos (plantillas de Azure Resource Manager en un repositorio de Git) en Azure DevTest Labs.
ms.topic: how-to
ms.date: 06/26/2020
ms.openlocfilehash: 3570f88ae8fe88740721b04783a8689e22c7bb7d
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132286381"
---
# <a name="configure-and-use-public-environments-in-azure-devtest-labs"></a>Configuración y uso de entornos públicos en Azure DevTest Labs
Azure DevTest Labs tiene un [repositorio público de plantillas de Azure Resource Manager](https://github.com/Azure/azure-devtestlab/tree/master/Environments) que puede usar para crear entornos. No tiene que conectarse a un origen externo de GitHub por sí mismo. Este repositorio incluye plantillas usadas con frecuencia, como Azure Web Apps, Clúster de Service Fabric y una granja de SharePoint de desarrollo. Esta característica es similar al repositorio público de artefactos que se incluye para todos los laboratorios que cree. El repositorio de entorno proporciona plantillas de entorno preautorizado con parámetros de entrada mínimos. Las plantillas le dan una experiencia de introducción sin problemas para los recursos de plataforma como servicio (PaaS) dentro de los laboratorios.
  
## <a name="configuring-public-environments"></a>Configuración de entornos públicos
Como propietario de un laboratorio, puede habilitar el repositorio público del entorno para el laboratorio durante la creación del laboratorio. Para habilitar los entornos públicos para el laboratorio, seleccione **Activado** en el campo **Entornos públicos** al crear un laboratorio. 

![Habilitación de un entorno público para un nuevo laboratorio](media/devtest-lab-configure-use-public-environments/enable-public-environment-new-lab.png)

Para ver laboratorios existentes, el repositorio del entorno público no está habilitado. Habilite manualmente el repositorio para usar las plantillas. Para los laboratorios creados mediante plantillas de Resource Manager, el repositorio también está deshabilitado de manera predeterminada.

Puede habilitar o deshabilitar entornos públicos para el laboratorio y también poner a disposición solo entornos específicos para los usuarios del laboratorio mediante los pasos siguientes: 

1. Seleccione **Configuración y directivas** para el laboratorio. 
2. En la sección **Bases para máquinas virtuales**, seleccione **Entornos públicos**.
3. Para habilitar entornos públicos para el laboratorio, seleccione **Sí**. De lo contrario, seleccione **No**. 
4. Si habilitó los entornos públicos, todos los entornos en el repositorio se habilitan de manera predeterminada. Puede anular la selección de un entorno para que no esté disponible para los usuarios del laboratorio. 

![Captura de pantalla que muestra la página de entornos públicos.](media/devtest-lab-configure-use-public-environments/public-environments-page.png)

## <a name="use-environment-templates-as-a-lab-user"></a>Uso de plantillas de entorno como usuario del laboratorio
Como usuario del laboratorio, puede crear un entorno a partir de la lista de plantillas de entorno habilitada seleccionando **Agregar** en la barra de herramientas en la página del laboratorio. La lista de bases incluye las plantillas de entornos públicos habilitadas por el administrador del laboratorio en la parte superior de la lista.

![Captura de pantalla que muestra las plantillas de entorno público.](media/devtest-lab-configure-use-public-environments/public-environment-templates.png)

## <a name="next-steps"></a>Pasos siguientes
Este repositorio es un repositorio de código abierto al que puede contribuir. Para agregar sus propias plantillas de Resource Manager, útiles y de uso frecuente, envíe una solicitud de incorporación de cambios en el repositorio.
