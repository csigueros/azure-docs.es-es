---
title: Creación de un controlador de datos
description: Cree un controlador de datos de Azure Arc en un clúster típico de Kubernetes de varios nodos que ya haya implementado.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 74c41ed89a3980967532955e46d4dade9eaacdb1
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2021
ms.locfileid: "121728044"
---
# <a name="create-the-azure-arc-data-controller"></a>Creación del controlador de datos de Azure Arc


## <a name="overview-of-creating-the-azure-arc-data-controller"></a>Información general sobre la creación del controlador de datos de Azure Arc

Los servicios de datos habilitados para Azure Arc se pueden crear en varios tipos diferentes de clústeres de Kubernetes y servicios de Kubernetes administrado mediante varios enfoques diferentes.

Actualmente, la lista admitida de servicios y distribuciones de Kubernetes es la siguiente:

- Azure Kubernetes Service (AKS)
- Azure Kubernetes Service en Azure Stack HCl
- Azure RedHat OpenShift (ARO)
- OpenShift Container Platform (OCP)
- AWS Elastic Kubernetes Service (EKS)
- Google Cloud Kubernetes Engine (GKE)
- Código abierto de nivel superior de Kubernetes implementado normalmente mediante kubeadm

> [!IMPORTANT]
> * La versión mínima admitida de Kubernetes es la versión 1.17. Consulte [Problemas conocidos](./release-notes.md#known-issues) para más información. 
> * La versión mínima admitida de OCP es la 4.5.
> * Consulte [Requisitos de conectividad](connectivity.md) para comprender qué conectividad se requiere entre su entorno y Azure.
> * Consulte [Configuración de almacenamiento](storage-configuration.md) para conocer los detalles acerca de cómo configurar el almacenamiento persistente.
> * si usa Azure Kubernetes Service, el tamaño de la máquina virtual del nodo de trabajo del clúster debe ser al menos **Standard_D8s_v3** y usar **discos Premium.** El clúster no debe abarcar varias zonas de disponibilidad. 
> * Si usa otra distribución o servicio de Kubernetes, debe asegurarse de que tiene un tamaño mínimo de nodo de 8 GB de memoria RAM y 4 núcleos y una capacidad total de 32 GB de RAM disponible entre todos los nodos de Kubernetes. Por ejemplo, podría tener 1 nodo con 32 GB de memoria RAM y 4 núcleos o bien tener 2 nodos con 16 GB de memoria RAM y 4 núcleos cada uno.

> [!NOTE]
> Si usa Red Hat OpenShift Container Platform en Azure, se recomienda usar la versión más reciente disponible.

En función de la opción que elija, algunas herramientas serán _obligatorias_, pero se recomienda [instalar todas las herramientas de cliente](./install-client-tools.md) antes de comenzar la creación del controlador de datos de Azure Arc.

Independientemente de la opción que elija, durante el proceso de creación tendrá que proporcionar la siguiente información:

- **Nombre del controlador de datos**: nombre descriptivo del controlador de datos; por ejemplo, "Controlador de datos de producción" o "Controlador de datos de Seattle".
- **Nombre de usuario del controlador de datos**: nombre de usuario del usuario administrador del controlador de datos.
- **Contraseña del controlador de datos**: contraseña del usuario administrador del controlador de datos.
- **Nombre del espacio de nombres de Kubernetes**: nombre del espacio de nombres de Kubernetes en el que desea crear el controlador de datos.
- **Modo de conectividad**: el modo de conectividad determina el grado de conectividad desde el entorno de los servicios de datos habilitados para Azure Arc a Azure. El modo de conectividad indirecta está generalmente disponible. El modo de conectividad directa está en versión preliminar.  Para más información, consulte[Modo de conectividad](./connectivity.md). 
- **Identificador de suscripción de Azure**: GUID de la suscripción de Azure en el que desea que se cree el recurso del controlador de datos en Azure.
- **Nombre del grupo de recursos de Azure**: nombre del grupo de recursos en el que desea que se cree el recurso del controlador de datos en Azure.
- **Ubicación de Azure**: ubicación de Azure en la que se almacenarán los metadatos del recurso del controlador de datos en Azure. Para obtener una lista de las regiones disponibles, consulte [Infraestructura global de Azure/Productos por región](https://azure.microsoft.com/global-infrastructure/services/?products=azure-arc). Los metadatos y la información de facturación sobre los recursos de Azure administrados por el controlador de datos que va a implementar solo se almacenarán en la ubicación de Azure que especifique como parámetro de ubicación. Si va a implementar en el modo conectado directamente, el parámetro de ubicación para el controlador de datos será el mismo que la ubicación del recurso de ubicación personalizado de destino.

## <a name="next-steps"></a>Pasos siguientes

Hay varias opciones para crear el controlador de datos de Azure Arc:

> **¿Solo desea realizar algunas pruebas?**  
> Empiece a trabajar rápidamente con [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) en Azure Kubernetes Service (AKS), AWS Elastic Kubernetes Service (EKS), Google Cloud Kubernetes Engine (GKE) o en una máquina virtual de Azure.
> 
- [Creación de un controlador de datos en modo conectado indirecto con la CLI](create-data-controller-indirect-cli.md)
- [Creación de un controlador de datos en modo conectado indirecto con Azure Data Studio](create-data-controller-indirect-azure-data-studio.md)
- [Creación de un controlador de datos en modo conectado indirecto desde Azure Portal mediante un cuaderno de Jupyter Notebook en Azure Data Studio](create-data-controller-indirect-azure-portal.md)
- [Creación de un controlador de datos en modo de conexión indirecta con herramientas de Kubernetes como kubectl u oc](create-data-controller-using-kubernetes-native-tools.md)
- [Creación de un controlador de datos en modo de conexión directa](create-data-controller-direct-prerequisites.md)
- [Creación de un controlador de datos con Azure Arc JumpStart para una experiencia acelerada de una implementación de prueba](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)
