---
title: Introducción a Red Hat OpenShift en Azure
description: Aprenda las características y ventajas de Red Hat OpenShift en Microsoft Azure para implementar y administrar aplicaciones basadas en contenedor.
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: overview
ms.date: 11/13/2020
ms.custom: mvc
ms.openlocfilehash: 7a0b0eca91cbf070e41057254d060d6dbc8ff249
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121726192"
---
# <a name="azure-red-hat-openshift"></a>Red Hat OpenShift en Azure

El servicio *Red Hat OpenShift en Microsoft* Azure permite implementar clústeres de [OpenShift](https://www.openshift.com/) totalmente administrados.

Red Hat OpenShift en Azure amplía [Kubernetes](https://kubernetes.io/). La ejecución de los contenedores en producción con Kubernetes requiere herramientas y recursos adicionales. Esto suele incluir la necesidad de trabajar con registros de imágenes, la administración de almacenamiento, las soluciones de red y las herramientas de registro y supervisión, todo lo cual debe tener versiones y probarse junto. La creación de aplicaciones basadas en contenedor requiere un trabajo de integración aún mayor con middleware, marcos, bases de datos y herramientas de CI/CD. Red Hat OpenShift en Azure combina todo esto en una sola plataforma, ofreciendo facilidad de operaciones a los equipos de TI mientras proporciona a los equipos de aplicaciones lo que tienen que ejecutar.

Red Hat y Microsoft han diseñado, operado y admitido Red Hat OpenShift en Azure de forma conjunta para ofrecer una experiencia de soporte integrado. No hay máquinas virtuales que operar y no se requiere ninguna aplicación de revisiones. Red Hat y Microsoft revisan, actualizan y supervisan los nodos maestros, de infraestructura y aplicación en su nombre. Sus clústeres de Red Hat OpenShift en Azure se implementan en su suscripción a Azure y se incluyen en su factura de Azure.

Puede elegir sus propias soluciones de CI/CD, almacenamiento, red y registro, o bien usar las soluciones integradas para la administración de código fuente automatizada, la creación de contenedores y aplicaciones, implementaciones, el escalado, la administración del estado, etc. Red Hat OpenShift en Azure proporciona una experiencia de inicio de sesión integrada a través de Azure Active Directory.

Para comenzar, complete el tutorial [Create an Azure Red Hat OpenShift cluster](tutorial-create-cluster.md) (Creación de un clúster de Red Hat OpenShift en Azure).

## <a name="access-security-and-monitoring"></a>Acceso, seguridad y supervisión

Para una seguridad y administración mejoradas, Red Hat OpenShift en Azure le permite integrarse con Azure Active Directory (Azure AD) y usar el control de acceso basado en rol de Kubernetes. También puede supervisar el mantenimiento del clúster y recursos.

## <a name="cluster-and-node"></a>Clúster y nodo

Los nodos de Red Hat OpenShift en Azure se ejecutan en máquinas virtuales de Azure. El almacenamiento se puede conectar a nodos y pods, y actualizar los componentes de clúster.

## <a name="service-level-agreement"></a>Acuerdo de Nivel de Servicio

Red Hat OpenShift en Azure ofrece un Acuerdo de Nivel de Servicio para garantizar que el servicio estará disponible el 99,95 % del tiempo. Para más información sobre el Acuerdo de Nivel de Servicio, consulte el [Acuerdo de Nivel de Servicio de Red Hat OpenShift en Azure](https://azure.microsoft.com/support/legal/sla/openshift/v1_0/).

## <a name="next-steps"></a>Pasos siguientes

Aprenda los requisitos previos de Red Hat OpenShift en Azure:

> [!div class="nextstepaction"]
> [Configuración de un entorno de desarrollo](tutorial-create-cluster.md)
