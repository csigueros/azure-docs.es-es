---
title: 'Extensiones de clústeres: Kubernetes habilitado para Azure Arc'
services: azure-arc
ms.service: azure-arc
ms.date: 10/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: En este artículo, encontrará información general y algunos conceptos sobre la funcionalidad de extensiones de clústeres de Kubernetes habilitado para Azure Arc.
ms.openlocfilehash: 6ae9270822ae16608a2c470ed818bcb8e6c2aa38
ms.sourcegitcommit: 677e8acc9a2e8b842e4aef4472599f9264e989e7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2021
ms.locfileid: "132279279"
---
# <a name="cluster-extensions"></a>Extensiones de clúster

Se pueden crear instancias de servicios de administración, como Azure Monitor y Microsoft Defender para Kubernetes, o de servicios como los servicios de aplicaciones o de datos de Azure en clústeres de Kubernetes mediante la funcionalidad de extensiones de clúster. Los [gráficos de Helm](https://helm.sh/) le ayudan a administrar las aplicaciones de Kubernetes, ya que contienen los elementos necesarios para definir, instalar y actualizar las aplicaciones de Kubernetes, incluso las más complejas. La característica de extensiones de clúster se basa en los componentes de empaquetado de Helm, ya que proporciona una experiencia controlada por Azure Resource Manager para la instalación y la administración del ciclo de vida de las instancias de los servicios que pretende habilitar en el clúster de Kubernetes. Los operadores o administradores del clúster pueden usar esta característica para las siguientes tareas: 

- Instalar diferentes extensiones de la funcionalidad deseada y obtener un inventario de todos los clústeres y las extensiones instaladas en ellos desde interfaces de Azure, como Azure Portal, la CLI, el SDK, etc. 
- Al igual que con cualquier otro recurso de Azure, puede controlar el acceso al recurso de extensiones de clúster mediante el control de acceso basado en rol (RBAC) de Azure.
- Usar Azure Policy para automatizar la implementación a gran escala de extensiones de clúster en todos los clústeres del entorno. 
- Suscribirse a los entrenamientos de versión (por ejemplo, versión preliminar, estable) para cada extensión.
- Administrar las actualizaciones mediante la configuración de la actualización automática de las extensiones o el anclaje a una versión específica.
- Administrar el ciclo de vida de las extensiones, incluidas las actualizaciones de las propiedades de extensión o la eliminación de una o varias instancias de extensión.

Una extensión puede estar limitada a un clúster o a un espacio de nombres. Cada tipo de extensión (por ejemplo, Azure Monitor, Microsoft Defender for Cloud, servicios de aplicaciones de Azure) define el ámbito en el que funciona en el clúster. 

## <a name="architecture-for-azure-arc-enabled-kubernetes-clusters"></a>Arquitectura de los clústeres de Kubernetes habilitados para Azure Arc

[ ![Arquitectura de las extensiones de clústeres](./media/conceptual-extensions.png) ](./media/conceptual-extensions.png#lightbox)

La instancia de una extensión de clúster se crea como un recurso de extensión de Azure Resource Manager (`Microsoft.KubernetesConfiguration/extensions`) encima del recurso de Kubernetes habilitado para Azure Arc (representado por `Microsoft.Kubernetes/connectedClusters`) en Azure Resource Manager. La representación en Azure Resource Manager permite crear una directiva que compruebe todos los recursos de Kubernetes habilitado para Azure Arc, tanto si tienen una extensión de clúster específica como si no. Una vez que ha determinado qué clústeres tienen extensiones de clúster que faltan con los valores de propiedad deseados, puede corregir estos recursos no compatibles mediante Azure Policy.

El componente `config-agent` que se ejecuta en el clúster supervisa y realiza un seguimiento de los recursos de extensión recién creados o de las actualizaciones de los recursos de extensión existentes en el recurso de Kubernetes habilitado para Azure Arc. El componente `extensions-manager` que se ejecuta en el clúster extrae entonces el gráfico de Helm asociado con una extensión de clúster de Azure Container Registry o de Microsoft Container Registry y lo instala en el clúster. 

Los componentes `config-agent` y `extensions-manager` que se ejecutan en el clúster controlan las actualizaciones de las nuevas versiones y otras operaciones, como la eliminación y las actualizaciones de propiedades de la instancia de extensión. Estos agentes usan una identidad administrada asignada por el sistema para comunicarse de forma segura con el servicio de back-end en Azure. 

> [!NOTE]
> * `config-agent` supervisa si se han creado recientemente o se han actualizado los recursos de extensión existentes en el recurso de Kubernetes habilitado para Azure Arc. Por lo tanto, los agentes requieren conectividad para arrastrar el estado deseado de la extensión al clúster. Si los agentes no pueden conectarse a Azure, se producirá un retraso en la propagación del estado deseado al clúster.
> * Una de las propiedades que puede establecer en las extensiones de clúster es la configuración de ProtectedConfiguration. Los valores de configuración protegidos de una extensión se guardan durante un período de 48 horas como máximo en los servicios de Kubernetes habilitado para Azure Arc. Por tanto, si el clúster permanece desconectado durante las 48 horas siguientes a las que se creó el recurso de extensión en Azure, la extensión pasa de tener un estado `Pending` a tener un estado `Failed`. Es recomendable poner los clústeres en línea con la mayor frecuencia posible.

## <a name="next-steps"></a>Pasos siguientes

* Use la guía de inicio rápido para [conectar un clúster de Kubernetes a Azure Arc](./quickstart-connect-cluster.md).
* [Implemente extensiones de clúster](./extensions.md) en un clúster de Kubernetes habilitado para Azure Arc.
