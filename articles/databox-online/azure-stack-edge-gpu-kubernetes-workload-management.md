---
title: Descripción de la administración de cargas de trabajo de Kubernetes en un dispositivo Azure Stack Edge Pro| Microsoft Docs
description: Describe cómo se pueden administrar las cargas de trabajo de Kubernetes en un dispositivo Azure Stack Edge Pro.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: conceptual
ms.date: 03/01/2021
ms.author: alkohli
ms.openlocfilehash: a0b3a5d0cbbae9f25a845f79db35d1049f93f776
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2021
ms.locfileid: "129710172"
---
# <a name="kubernetes-workload-management-on-your-azure-stack-edge-pro-device"></a>Administración de cargas de trabajo de Kubernetes en un dispositivo Azure Stack Edge Pro

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

En un dispositivo Azure Stack Edge Pro, se crea un clúster de Kubernetes al configurar el rol de proceso. Una vez creado el clúster de Kubernetes, las aplicaciones en contenedor se pueden implementar en el clúster de Kubernetes en pods. Hay distintas formas de implementar las cargas de trabajo en el clúster de Kubernetes. 

En este artículo se describen los distintos métodos que se pueden usar para implementar cargas de trabajo en un dispositivo Azure Stack Edge Pro.

## <a name="workload-types"></a>Tipos de carga de trabajo

Los dos tipos comunes de cargas de trabajo que se pueden implementar en un dispositivo Azure Stack Edge Pro son aplicaciones sin estado o aplicaciones con estado.

- **Las aplicaciones sin estado** no conservan su estado y no guardan datos en el almacenamiento persistente. Todos los datos de usuario y de sesión permanecen con el cliente. Algunos ejemplos de aplicaciones sin estado incluyen front-ends web como Nginx y otras aplicaciones web.

    Puede crear una implementación de Kubernetes para implementar una aplicación sin estado en el clúster. 

- **Las aplicaciones con estado** requieren que se guarde su estado. Las aplicaciones con estado usan almacenamiento persistente, como volúmenes persistentes, para guardar los datos para su uso por parte del servidor o de otros usuarios. Algunos ejemplos de aplicaciones con estado son las bases de datos como [Azure SQL Edge](../azure-sql-edge/overview.md) y MongoDB.

    Puede crear una implementación de Kubernetes para implementar una aplicación con estado. 

## <a name="deployment-flow"></a>Flujo de implementación

Para implementar aplicaciones en un dispositivo Azure Stack Edge Pro, siga estos pasos: 
 
1. **Configuración del acceso**: En primer lugar, usará el espacio de ejecución de PowerShell para crear un usuario, crear un espacio de nombres y conceder al usuario acceso a ese espacio de nombres.
2. **Configuración del almacenamiento**: A continuación, usará el recurso Azure Stack Edge en Azure Portal para crear volúmenes persistentes con el aprovisionamiento estático o dinámico para las aplicaciones con estado que va a implementar.
3. **Configuración de la red**: Por último, usará los servicios para exponer aplicaciones externamente y dentro del clúster de Kubernetes.
 
## <a name="deployment-types"></a>Tipos de implementación

Existen tres formas principales de implementar las cargas de trabajo. Cada una de estas metodologías de implementación le permite conectarse a un espacio de nombres distinto en el dispositivo y, a continuación, implementar aplicaciones sin estado o con estado.

![Implementación de la carga de trabajo de Kubernetes](./media/azure-stack-edge-gpu-kubernetes-workload-management/kubernetes-workload-management-1.png)

- **Implementación local**: Esta implementación se realiza con la herramienta de acceso desde la línea de comandos, como `kubectl` que permite implementar `yamls` de Kubernetes. Acceda al clúster de Kubernetes en su dispositivo Azure Stack Edge Pro a través de un archivo `kubeconfig`. Para más información, vaya a [Acceso a un clúster de Kubernetes mediante kubectl](azure-stack-edge-gpu-create-kubernetes-cluster.md).

- **Implementación de IoT Edge**: Esto se realiza mediante IoT Edge, que se conecta a Azure IoT Hub. Se conecta al clúster de Kubernetes en el dispositivo Azure Stack Edge Pro mediante el espacio de nombres `iotedge`. Los agentes de IoT Edge implementados en este espacio de nombres son responsables de la conectividad con Azure. Se aplica la configuración de `IoT Edge deployment.json` mediante DevOps de CI/CD de Azure. La administración de espacios de nombres y IoT Edge se realiza mediante el operador en la nube.

- **Implementación de Kubernetes habilitado para Azure Arc**: Kubernetes habilitado para Azure Arc es una herramienta de administración híbrida que le permitirá implementar aplicaciones en los clústeres de Kubernetes. Se conecta al clúster de Kubernetes en el dispositivo Azure Stack Edge Pro mediante `azure-arc namespace`. Los agentes implementados en este espacio de nombres son responsables de la conectividad con Azure. La configuración de implementación se aplica mediante la administración de configuraciones basada en GitOps. 
    
    Kubernetes habilitado para Azure Arc también le permitirá usar Azure Monitor para contenedores para ver y supervisar su clúster. Para más información, vaya a [¿Qué es Kubernetes habilitado para Azure Arc?](../azure-arc/kubernetes/overview.md).
    
    A partir de marzo de 2021, Kubernetes habilitado para Azure Arc estará disponible con carácter general para los usuarios y se aplicarán cargos de uso estándar. Como cliente valioso de la versión preliminar, Kubernetes habilitado para Azure Arc estará a su disposición sin cargo alguno para dispositivos de Azure Stack Edge. Para disfrutar de la oferta de la versión preliminar, cree una [solicitud de soporte técnico](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest):

    1. En **Tipo de problema** como **Facturación**.
    2. En **Suscripción**, seleccione la suscripción.
    3. En **Servicio**, seleccione **Mis servicios** y, después, seleccione **Azure Stack Edge**.
    4. En **Recurso**, seleccione su recurso.
    5. En **Resumen**, escriba una descripción del problema.
    6. En **Tipo de problema**, seleccione **Unexpected Charges** (Cargos inesperados).
    7. En **Subtipo de problema**, seleccione **Help me understand charges on my free trial** (Necesito ayuda para entender los cargos de la evaluación gratuita).


## <a name="choose-the-deployment-type"></a>Elección del tipo de implementación

Durante la implementación de aplicaciones, tenga en cuenta la siguiente información:

- **Uno o varios tipos**: Puede elegir una opción de implementación única o una combinación de distintas opciones de implementación.
- **En la nube o local**: En función de las aplicaciones, puede elegir la implementación local con kubectl o la implementación en la nube con IoT Edge y Azure Arc. 
    - Al elegir una implementación local, está restringido a la red en la que está implementado el dispositivo Azure Stack Edge Pro.
    - Si tiene un agente en la nube que puede implementar, debe implementar su operador en la nube y usar administración en la nube.
- **IoT o Azure Arc**: La elección de la implementación también depende de la intención del escenario del producto. Si va a implementar aplicaciones o contenedores que tienen una integración más profunda con IoT o con el ecosistema IoT, seleccione IoT Edge para implementar sus aplicaciones. Si tiene implementaciones de Kubernetes existentes, Azure Arc es la mejor opción.


## <a name="next-steps"></a>Pasos siguientes

Para implementar una aplicación de forma local con kubectl, consulte:

- [Implementación de una aplicación sin estado en Azure Stack Edge Pro mediante kubectl](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).

Para implementar una aplicación con IoT Edge, consulte:

- [Implementación de un módulo de ejemplo en Azure Stack Edge Pro mediante IoT Edge](azure-stack-edge-gpu-deploy-sample-module.md).

Para implementar una aplicación mediante Azure Arc, consulte:

- [Implementación de una aplicación con Azure Arc](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md).
